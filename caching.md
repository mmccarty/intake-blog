## Introduction

Intake provides easy access data sources from remote / cloud storage, however for large files the 
cost of downloading files everytime data is read can be extremely high. We have developed a caching 
strategy to store and manage data sources on the local file system.

## How it works

Intake catalogs are composed of data sources, which contain attributes descibing how to load data 
files. To enable caching, catalogs authors can now specify caching parameters that will be used to 
download data file(s), store them in a configured local directory, and return a their location on 
local disk. For example:

```
nyc_taxi_cache:
    description: NYC Taxi dataset with caching
    driver: csv
    cache:
      - argkey: urlpath
        regex: 'datashader-data'
        type: file
    args:
      urlpath: 's3://datashader-data/nyc_taxi.csv'
      storage_options: {'anon': True}
```


The cache directory can be specified in the configuration file, environment 
variable, or at runtime. See the documentation for [details](https://intake.readthedocs.io/en/latest/catalog.html#caching-source-files-locally).

Files are downloaded on the first read and will persist until the cache has been cleared.

## Example

To Do:

Add intake with caching to the NYC Taxi dataset to
https://github.com/ContinuumIO/intake/blob/master/examples/caching_demo.ipynb


## Future Work

  * To start we have implemented a caching strategy based on individual files, therefore this solution will not handle navigating directory structures to download data sources.
  * Many remote data sources are stored as compressed files such as tar/gz/gz2/zip. We need a decompressor which will unpack after the download has completed and return the local directory. 
  * Cache expiration and checksum verification have not been explored, but would be nice to have.
  * Extensions to the Intake CLI to manage cached data.