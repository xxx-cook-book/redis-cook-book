# Redis

* Installation
  * Mac OS X
    ```shell
    $ brew install redis
    ```
  * Ubuntu
    ```shell
    # Latest Version
    $ sudo apt-get update
    $ sudo apt-get install -y python-software-properties
    $ sudo add-apt-repository -y ppa:chris-lea/redis-server
    $ sudo apt-get update
    $ sudo apt-get install -y redis-server
    ```

* Help
  ```shell
  $ sudo /usr/bin/redis-server -h
  Usage: ./redis-server [/path/to/redis.conf] [options]
         ./redis-server - (read config from stdin)
         ./redis-server -v or --version
         ./redis-server -h or --help
         ./redis-server --test-memory <megabytes>

  Examples:
         ./redis-server (run the server with default conf)
         ./redis-server /etc/redis/6379.conf
         ./redis-server --port 7777
         ./redis-server --port 7777 --slaveof 127.0.0.1 8888
         ./redis-server /etc/myredis.conf --loglevel verbose

  Sentinel mode:
         ./redis-server /etc/sentinel.conf --sentinel
  ```

## References