## Introduction

Intake provides easy access data sources from remote / cloud storage, however for large files the 
cost of downloading files everytime data is read can be extremely high. We have developed a caching 
strategy to store and manage data sources on the local file system.

## How it works

Intake catalogs are composed of data sources, which contain attributes descibing how to 
load data files. To enable caching, catalog authors can now specify caching parameters 
that will be used to download data file(s), store them in a configured local directory, 
and return a their location on local disk. For example:

```
nyc_taxi:
    description: NYC Taxi dataset
    driver: csv
    cache:
      - argkey: urlpath
        regex: 'datashader-data'
        type: file
    args:
      urlpath: 's3://datashader-data/nyc_taxi.csv'
      storage_options: {'anon': True}
```

Glob strings can be used for the ``urlpath``, which will be expanded to the individual files 
listed in the base directory of the path. Each file will be downloaded in parallel and referrenced
in the metadata.

Downloaded files are stored locally in the cache directory, specified in the configuration 
file, environment variable, or at runtime. The default location for the cache directory 
is ``~/.intake/cache``. Inside this directory are subdirectories for each data source found in 
the catalog file that has been cached. These directories are named by hashing the data 
source driver, urlpath, and cache regex to avoid collision among data sources and cache 
specifications. Files are downloaded on the first read and will persist until the 
cache has been cleared explicitly using convience methods. 

See the [documentation](https://intake.readthedocs.io/en/latest/catalog.html#caching-source-files-locally)
for more details. 

## Example Notebook

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/mmccarty/intake-blog/updates?filepath=examples%2Fcaching.ipynb)

## Future Work

  * To start we have implemented a caching strategy based on individual files, therefore this solution will not handle navigating directory structures to download data sources ([PR 140](https://github.com/ContinuumIO/intake/pull/140)).
  * Many remote data sources are stored as compressed files such as tar/gz/gz2/zip. We need a decompressor which will unpack after the download has completed and return the local directory ([PR 140](https://github.com/ContinuumIO/intake/pull/140)).
  * Extensions to the Intake CLI to manage cached data ([PR 145](https://github.com/ContinuumIO/intake/pull/145)).
  * Cached files do not have an expiration, therefore they will need to be cleared manually to get updated data. We do generate a created date when a file is downloaded and store this in the metadata. This could be used to trigger a download using a configurable expiration duration.
  * Checksum verification need to be explored so that updated data can be downloaded regardless of expiration.
  * An API for managing and clearing cached data at the data source level, rather than accessing the cache objects directly.