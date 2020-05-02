Changes to do all steps related to a sector on one worker.

# Build

Clone the modified lotus repo:

```
git clone -b one-worker.4.30 https://github.com/hayeah/lotus.git
```

Clone dependencies that had been modified:

```
mkdir extern-dev

git clone -b one-worker.4.30 https://github.com/hayeah/sector-storage.git extern-dev/sector-storage

git clone -b one-worker.4.30 https://github.com/hayeah/specs-storage.git extern-dev/specs-storage
```

Then:

```
make all
```

# Instruction

The lotus-seal-worker had been modified to generate & seal sectors by itself. When a sector is finalized, the lotus-seal-worker moves the sealed sector from its local storage (`~/.lotusworker`), to the shared sealed sector storage (`~/.lotusstorage`).

The shared `~/.lotusstorage` should be accessible to the lotus-seal-worker as a filesystem path, (e.g. as NFS, CephFS).

Go to the [faucet](t01000.miner.interopnet.kittyhawk.wtf) to initialize a miner.

To run the lotus-storage-miner:

```
./lotus-storage-miner run
```

On startup, the storage miner will generate an UUID for the shared storage (i.e. `~/.lotusstorage`). This UUID will stay the same across restarts. To see what it is:

```
cat ~/.lotusstorage/sectorstore.json
{
  "ID": "74365f80-7ea8-4463-8468-aa4cdab03b0a",
  "Weight": 10,
  "CanSeal": true,
  "CanStore": true
}
```

Then we start a lotus-worker, and instruct it to move sealed sectors to the above storage when finalizing:

```
./lotus-seal-worker run --address :4001 \
    --bind-store \
    74365f80-7ea8-4463-8468-aa4cdab03b0a:"$HOME/.lotusstorage"
```

* `--bind-store`: this is an added option. It tells the worker that the remote storage "74365f80-7ea8-4463-8468-aa4cdab03b0a" is accessible with the local path `~/.lotusstorage`

You can start a second lotus-worker on the same machine for testing:

```
WORKER_PATH=~/.lotusworker2 ./lotus-seal-worker run --address :4002 \
 --bind-store \
  74365f80-7ea8-4463-8468-aa4cdab03b0a:"$HOME/.lotusstorage"

```