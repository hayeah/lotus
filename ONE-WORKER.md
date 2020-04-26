Changes to do all steps related to a sector on one worker.

# Build

Clone the modified lotus repo:

```
git clone -b one-worker https://github.com/hayeah/lotus.git
```

Clone dependencies that had been modified:

```
mkdir extern-dev

git clone -b one-worker https://github.com/hayeah/sector-storage.git extern-dev/sector-storage

git clone -b one-worker https://github.com/hayeah/specs-storage.git extern-dev/specs-storage
```

Then:

```
make all
```