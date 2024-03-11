# Hash Hash Hash
The goal of this lab is to implement a thread-safe hash table in C using mutex locks. Therea are two versions: v1 with a single lock and v2 with fine-grained locking. Each version is compared to the base implementation - which runs on 1 thread - in terms of correctness and performance. 

## Building
```shell
make
```

## Running
```shell
./hash-table-tester -t 4 -s 100000
```

## First Implementation
In the `hash_table_v1_add_entry` function, I added a single mutex around the entire function. Because performance was not a factor in V1, adding a lock around the entire function code ensures that concurrent threads do not try to access the hash table at the same time. I make sure to unlock the code in before the return statement (to prevent deadlocks) and at the end of the function. I added a global variable called called "mutex" and used that in `hash_table_v1_add_entry` to hold the state of the lock. 

### Performance
```shell
Generation: 81,488 usec
Hash table base: 1,917,110 usec
  - 0 missing
Hash table v1: 2,802,585 usec
  - 0 missing
Hash table v2: 580,492 usec
  - 0 missing
```
Version 1 is a little slower than the base version. This is because in the mission to get rid of race conditions, I added a lock around the entire `hash_table_v1_add_entry` function, which means that only 1 thread has access to the entire hash table at a time. This worsens performance as only 1 thread can operate on the hash table at a time. 

## Second Implementation
`hash_table_v2_add_entry` employs fine-grained locking, assigning a mutex to each bucket. This method allows higher concurrency and improves performance, particularly under high load. We can see this in the results as v2 is more than 3x faster than the base implementation. 


### Performance
```shell
bash-4.4$ ./hash-table-tester -t 4 -s 100000
Generation: 81,488 usec
Hash table base: 1,917,110 usec
  - 0 missing
Hash table v1: 2,802,585 usec
  - 0 missing
Hash table v2: 580,492 usec
  - 0 missing
```

Instead of locking the entire hash table like in v1, I placed locks around individual buckets in v2. This allows threads to make concurrent accesses to the hash table as long as it's not the same bucket, improving the performance. It is faster than both the base version and v1. 

## Cleaning up
```shell
make clean
```