---
layout: post
title: "레디스 설치 및 실행"
comments: true
---

centos 6.9에 레디스를 설치하려고합니다.
해당 이력은 실패 시나리오까지 포함되어있으므로,
결론을 보고 싶으 신 분은, 맨밑의 성공사례로 내려가시기 바랍니다.

레디스를 설치합니다. wget 이 없으므로 먼저 yum install wget으로 wget을 설치합니다.

```
[root@localhost ~]# yum install wget
Loaded plugins: fastestmirror
Setting up Install Process
Loading mirror speeds from cached hostfile
 * base: ftp.riken.jp
 * extras: ftp.tsukuba.wide.ad.jp
 * updates: ftp.tsukuba.wide.ad.jp
Resolving Dependencies
--> Running transaction check
---> Package wget.x86_64 0:1.12-10.el6 will be installed
--> Finished Dependency Resolution

~~ 중략 ~~~

Running rpm_check_debug
Running Transaction Test
Transaction Test Succeeded
Running Transaction
  Installing : wget-1.12-10.el6.x86_64                                                                                                                                                                                                                                    1/1
  Verifying  : wget-1.12-10.el6.x86_64                                                                                                                                                                                                                                    1/1

Installed:
  wget.x86_64 0:1.12-10.el6

Complete!
```

wget http://download.redis.io/redis-stable.tar.gz
으로 레디스를 다운받습니다.

```
[root@localhost home]# mkdir redis
[root@localhost home]# cd redis/
[root@localhost redis]# wget http://download.redis.io/redis-stable.tar.gz
--2020-05-18 15:44:48--  http://download.redis.io/redis-stable.tar.gz
Resolving download.redis.io... 109.74.203.151
Connecting to download.redis.io|109.74.203.151|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2253771 (2.1M) [application/x-gzip]
Saving to: `redis-stable.tar.gz'

100%[======>] 2,253,771    318K/s   in 7.7s    

2020-05-18 15:44:57 (287 KB/s) - `redis-stable.tar.gz' saved [2253771/2253771]
```


```
tar xvzf redis-stable.tar.gz
cd redis-stable
make
```

make라는 명령어를 쳤는데, 에러가 납니다.
```
make[3]: Entering directory `/home/redis/redis-stable/deps/hiredis'
cc -std=c99 -pedantic -c -O3 -fPIC   -Wall -W -Wstrict-prototypes -Wwrite-strings -Wno-missing-field-initializers -g -ggdb net.c
make[3]: cc: 명령을 찾지 못했음
make[3]: *** [net.o] 오류 127
make[3]: Leaving directory `/home/redis/redis-stable/deps/hiredis'
make[2]: *** [hiredis] 오류 2
make[2]: Leaving directory `/home/redis/redis-stable/deps'
make[1]: [persist-settings] 오류 2 (무시됨)
    CC adlist.o
/bin/sh: cc: command not found
make[1]: *** [adlist.o] 오류 127
make[1]: Leaving directory `/home/redis/redis-stable/src'
make: *** [all] 오류 2
```

yum install gcc 로 gcc를 설치해줍니다.

```
[root@localhost redis-stable]# make clean
cd src && make clean
make[1]: Entering directory `/home/redis/redis-stable/src'
rm -rf redis-server redis-sentinel redis-cli redis-benchmark redis-check-rdb redis-check-aof *.o *.gcda *.gcno *.gcov redis.info lcov-html Makefile.dep dict-benchmark
rm -f adlist.d quicklist.d ae.d anet.d dict.d server.d sds.d zmalloc.d lzf_c.d lzf_d.d pqsort.d zipmap.d sha1.d ziplist.d release.d networking.d util.d object.d db.d replication.d rdb.d t_string.d t_list.d t_set.d t_zset.d t_hash.d config.d aof.d pubsub.d multi.d debug.d sort.d intset.d syncio.d cluster.d crc16.d endianconv.d slowlog.d scripting.d bio.d rio.d rand.d memtest.d crcspeed.d crc64.d bitops.d sentinel.d notify.d setproctitle.d blocked.d hyperloglog.d latency.d sparkline.d redis-check-rdb.d redis-check-aof.d geo.d lazyfree.d module.d evict.d expire.d geohash.d geohash_helper.d childinfo.d defrag.d siphash.d rax.d t_stream.d listpack.d localtime.d lolwut.d lolwut5.d lolwut6.d acl.d gopher.d tracking.d connection.d tls.d sha256.d timeout.d setcpuaffinity.d anet.d adlist.d dict.d redis-cli.d zmalloc.d release.d ae.d crcspeed.d crc64.d siphash.d crc16.d ae.d anet.d redis-benchmark.d adlist.d dict.d zmalloc.d siphash.d
make[1]: Leaving directory `/home/redis/redis-stable/src'
[root@localhost redis-stable]# MAKE
-bash: MAKE: command not found
[root@localhost redis-stable]# make
cd src && make all
make[1]: Entering directory `/home/redis/redis-stable/src'
    CC Makefile.dep
make[1]: Leaving directory `/home/redis/redis-stable/src'
make[1]: Entering directory `/home/redis/redis-stable/src'
    CC adlist.o
cc1: error: unrecognized command line option "-std=c11"
make[1]: *** [adlist.o] 오류 1
make[1]: Leaving directory `/home/redis/redis-stable/src'
make: *** [all] 오류 2
[root@localhost redis-stable]# make distclean
cd src && make distclean
make[1]: Entering directory `/home/redis/redis-stable/src'
rm -rf redis-server redis-sentinel redis-cli redis-benchmark redis-check-rdb redis-check-aof *.o *.gcda *.gcno *.gcov redis.info lcov-html Makefile.dep dict-benchmark
rm -f adlist.d quicklist.d ae.d anet.d dict.d server.d sds.d zmalloc.d lzf_c.d lzf_d.d pqsort.d zipmap.d sha1.d ziplist.d release.d networking.d util.d object.d db.d replication.d rdb.d t_string.d t_list.d t_set.d t_zset.d t_hash.d config.d aof.d pubsub.d multi.d debug.d sort.d intset.d syncio.d cluster.d crc16.d endianconv.d slowlog.d scripting.d bio.d rio.d rand.d memtest.d crcspeed.d crc64.d bitops.d sentinel.d notify.d setproctitle.d blocked.d hyperloglog.d latency.d sparkline.d redis-check-rdb.d redis-check-aof.d geo.d lazyfree.d module.d evict.d expire.d geohash.d geohash_helper.d childinfo.d defrag.d siphash.d rax.d t_stream.d listpack.d localtime.d lolwut.d lolwut5.d lolwut6.d acl.d gopher.d tracking.d connection.d tls.d sha256.d timeout.d setcpuaffinity.d anet.d adlist.d dict.d redis-cli.d zmalloc.d release.d ae.d crcspeed.d crc64.d siphash.d crc16.d ae.d anet.d redis-benchmark.d adlist.d dict.d zmalloc.d siphash.d
(cd ../deps && make distclean)
make[2]: Entering directory `/home/redis/redis-stable/deps'
(cd hiredis && make clean) > /dev/null || true
(cd linenoise && make clean) > /dev/null || true
(cd lua && make clean) > /dev/null || true
(cd jemalloc && [ -f Makefile ] && make distclean) > /dev/null || true
(rm -f .make-*)
make[2]: Leaving directory `/home/redis/redis-stable/deps'
(rm -f .make-*)
make[1]: Leaving directory `/home/redis/redis-stable/src'
```

make clean 을 했을 때, 잘 안될 경우, make distclean 를 처리합니다.

```
x_pool.o src/nstime.o src/pages.o src/prng.o src/prof.o src/rtree.o src/stats.o src/sz.o src/tcache.o src/ticker.o src/tsd.o src/witness.o
make[3]: Leaving directory `/home/redis/redis-stable/deps/jemalloc'
make[2]: Leaving directory `/home/redis/redis-stable/deps'
    CC adlist.o
cc1: error: unrecognized command line option "-std=c11"
make[1]: *** [adlist.o] 오류 1
make[1]: Leaving directory `/home/redis/redis-stable/src'
make: *** [all] 오류 2
```
다시 make를 했는데,
cc1: error: unrecognized command line option "-std=c11" 에러가 납니다.

```
[root@localhost redis-stable]# gcc -std=c11 --version
gcc (GCC) 4.4.7 20120313 (Red Hat 4.4.7-23)
Copyright (C) 2010 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

yum install gcc-c++
로 c++ 까지 설치합니다.

make distclean 처리한 후 다시 make 처리를 합니다.

그래도 안됩니다.

...centos 6.9 의 경우 yum install 로 설치히 4.4.7 ... 버전으로 밖에 설치가 안됩니다.

gcc update를 업데이트 해야합니다.

.... 업데이트를 해보았으나 결국 실패했습니다.

그렇다면 이제 남은 방법은 ....
레디스를 그냥 6.9에 맞춰서 깔아보겠습니다.

```
[root@localhost redis_1]# wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
--2020-05-19 10:21:44--  http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
Resolving dl.fedoraproject.org... 209.132.181.23, 209.132.181.25, 209.132.181.24
Connecting to dl.fedoraproject.org|209.132.181.23|:80... connected.
HTTP request sent, awaiting response... 404 Not Found
2020-05-19 10:21:45 ERROR 404: Not Found.
```

음... 404 Not Found가 떨어져버립니다....

```
[root@localhost redis_1]# rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm(을)를 복구합니다
경고: /var/tmp/rpm-tmp.RqmtU4: Header V3 RSA/SHA256 Signature, key ID 0608b895: NOKEY
준비 중...               ########################################### [100%]
   1:epel-release           ########################################### [100%]
```


성공사례입니다. 위의 내용을 무시하시고 여기서부터 보셔도 무방합니다.   

우선 레디스를 다시 설치해보도록 하겠습니다!!!

```
[root@localhost ~]# yum --enablerepo=epel,remi install redis
Loaded plugins: fastestmirror
Setting up Install Process
Determining fastest mirrors
epel/metalink                                                                                                                  | 7.5 kB     00:00     
 * base: mirror.kakao.com
 * centos-sclo-rh: mirror.kakao.com
 * centos-sclo-sclo: mirror.kakao.com
 * epel: ftp.riken.jp
 * extras: mirror.kakao.com
 * remi: ftp.riken.jp
 * remi-safe: ftp.riken.jp
 * updates: mirror.kakao.com
base                                                                                                                           | 3.7 kB     00:00     
centos-sclo-rh                                                                                                                 | 3.0 kB     00:00     
centos-sclo-rh/primary_db                                                                                                      | 1.5 MB     00:00     
centos-sclo-sclo                                                                                                               | 2.9 kB     00:00     
docker-ce-stable                                                                                                               | 3.5 kB     00:00     
docker-ce-stable/primary_db                                                                                                    |  45 kB     00:00     
epel                                                                                                                           | 4.7 kB     00:00     
epel/primary_db                                                                                                                | 6.1 MB     00:00     
extras                                                                                                                         | 3.4 kB     00:00     
mariadb                                                                                                                        | 2.9 kB     00:00     
remi                                                                                                                           | 3.0 kB     00:00     
remi/primary_db                                                                                                                | 2.4 MB     00:00     
remi-safe                                                                                                                      | 3.0 kB     00:00     
remi-safe/primary_db                                                                                                           | 1.2 MB     00:00     
testing-1.1-devtools-6                                                                                                         |  951 B     00:00     
updates                                                                                                                        | 3.4 kB     00:00     
updates/primary_db                                                                                                             |  10 MB     00:01     
Resolving Dependencies
--> Running transaction check
---> Package redis.x86_64 0:6.0.3-1.el6.remi will be updated
---> Package redis.x86_64 0:6.0.5-1.el6.remi will be an update
--> Finished Dependency Resolution

Dependencies Resolved

======================================================================================================================================================
 Package                          Arch                              Version                                     Repository                       Size
======================================================================================================================================================
Updating:
 redis                            x86_64                            6.0.5-1.el6.remi                            remi                            1.3 M

Transaction Summary
======================================================================================================================================================
Upgrade       1 Package(s)

Total download size: 1.3 M
Is this ok [y/N]: y
Downloading Packages:
redis-6.0.5-1.el6.remi.x86_64.rpm                                                                                              | 1.3 MB     00:00     
Running rpm_check_debug
Running Transaction Test
Transaction Test Succeeded
Running Transaction
  Updating   : redis-6.0.5-1.el6.remi.x86_64                                                                                                      1/2
warning: /etc/redis.conf created as /etc/redis.conf.rpmnew
  Cleanup    : redis-6.0.3-1.el6.remi.x86_64                                                                                                      2/2
  Verifying  : redis-6.0.5-1.el6.remi.x86_64                                                                                                      1/2
  Verifying  : redis-6.0.3-1.el6.remi.x86_64                                                                                                      2/2

Updated:
  redis.x86_64 0:6.0.5-1.el6.remi                                                                                                                     

Complete!

```

```
[root@localhost ~]# chkconfig redis on
[root@localhost ~]#

```

```
[root@localhost redis_1]# netstat -nap | grep LISTEN
tcp        0      0 0.0.0.0:6379                0.0.0.0:*                   LISTEN      2296/redis-server 0
```

드디어 레디스가 실행이 되었습니다.  
자동으로 6379 포트로 켜졌습니다.  

하지만 외부에서 커넥션 요청을 하면 연결이 되지 않고 끊깁니다.  
방화벽 문제도 아니고,   
해당 문제는 redis의 설정 분제입니다.  

암호 설정 과 외부 접속 등의 기능을 추가해둡니다.    

```
vim /etc/redis.conf
```

레디스를 엽니다. 암호 설정과 외부에서 접속가능하게 처리하겠습니다.
암호를 설정하려면 requirepass를 찾아 foobared라고 되어 있는부분을 지우고 설정하려는 암호를 넣습니다.
외부에서 접속을 허용하기 위해서는 bind를 찾아 127.0.0.1로 되어있는 부분을 지우고 0.0.0.0으로 수정합니다.
redis.conf 파일을 저장하고 redis를 재시작해 줍니다.

```
requirepass 1234
bind 0.0.0.0
```

netstat 명령으로 redis-server가 외부에서 접속하능 하도록 되어 있는지 확인해 봅니다.
redis의 포트는 6379입니다.

잘 접속되는거 확인 완료!!!
