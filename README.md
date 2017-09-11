# Octomender
```
Octomender = Octocat + Recommender
```
Github Repo Recommender System.
Get repo recommendation based on your GitHub star history

## Dependencies
- [redis](https://redis.io/): An in-memory database that persists on disk
- [hireids](https://github.com/redis/hiredis): Minimalistic C client for Redis >= 1.2
- [redis-py](https://github.com/andymccurdy/redis-py): Redis Python Client
- [OpenMP>=4.0](http://www.openmp.org/): C/C++ API that supports multi-platform shared memory multiprocessing programming

## Dataset
[Github Archive](https://www.githubarchive.org/)

## Build
```
cd lib; make
```

## Preprocessing
### [parse.py](preprocessing/parse.py)
Parse raw json data files into three pickle data files.
- output-data-basename.user: map of user id (str) to user name (str)
- output-data-basename.repo: map of repo id (int) to repo name (str)
- output-data-basename.edge: list of tuples of user-repo edge (str, int)
```
Usage: parse.py {-m|--member|-w|--watch} {<input-json-directory>|<input-json-file>} <output-data-basename>
  -m, --member      parse MemberEvent.
  -w, --watch       parse WatchEvent.
Ex:    parse.py -m 2017-06-01-0.json data
Ex:    parse.py --watch json/2017-05/ data/2017-05
```
Refer raw json data format to [GitHub API v3](https://developer.github.com/v3/activity/events/types/).

### [parse_mp.py](preprocessing/parse_mp.py)
Ditto, but run with multiprocessing. Default number of processes is 16.
```
Usage: parse.py {-m|--member|-w|--watch} {<input-json-directory>|<input-json-file>} <output-data-basename> [n-process]
  -m, --member      parse MemberEvent.
  -w, --watch       parse WatchEvent.
  n-process         number of processes when multiprocessing.
Ex:    parse.py -m 2017-06-01-0.json data
Ex:    parse.py --watch json/2017-05/ data/2017-05 32
```

### [mergedata.py](preprocessing/mergedata.py)
Merge multiple pickle data files into one.
```
Usage: mergedata.py <input-data-dir> <output-data-basename>
Ex:    mergedata.py data/2016-010203/ data/2016-Q1
```

### [graph2redis.py](preprocessing/graph2redis.py)
Insert graph data into redis database.
```
Usage: graph2redis.py <input-edgelist> <redis-port>
Ex:    graph2redis.py data/2016-Q1.edge 6379
```
