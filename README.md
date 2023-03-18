### 部署smokeping docker 
docker run -d \
  --name=smokeping \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Asia/Shanghai \
  -p 0.0.0.0:10001:80 \
  -v /mnt/data0/smokeping/config:/config \
  -v /mnt/data0/smokeping/data:/data \
  --restart unless-stopped \
  lscr.io/linuxserver/smokeping:latest


### prometheus docker 
```shell
docker run --name prometheus \
 -d --restart=always \
 -p 0.0.0.0:9090:9090 \
 -v /etc/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml \
 -v /etc/prometheus/rules:/etc/prometheus/rules \
 prom/prometheus:v2.27.1 \
 --web.enable-lifecycle \
 --config.file=/etc/prometheus/prometheus.yml \
 --storage.tsdb.path=/prometheus \
 --web.console.libraries=/usr/share/prometheus/console_libraries \
 --web.console.templates=/usr/share/prometheus/consoles


```



### influxdb shell docker

```shell
#!/bin/bash

export HOME=/root

name=influxdb

if [ `docker ps -a -f name=/$name$ -q|wc -l` -ne 0 ]; then
    >&2 echo "container /$name exists, remove before run it"
    >&2 echo
    >&2 echo "docker rm -f /$name"
    return 1
fi

binding=`cat /etc/hosts|grep localvip|head -1 |awk '{print $1}'`
[ "$binding" != "" ] && binding="$binding:"

docker run \
  -d --restart=always \
  --name $name \
  -p 0.0.0.0:18086:8086 \
  -p 0.0.0.0:18083:8083 \
  -p 0.0.0.0:25826:25826/udp \
  -v /mnt/data0/influxdb/lib:/var/lib/influxdb \
  -v /mnt/data0/influxdb/etc/influxdb.conf:/etc/influxdb/influxdb.conf:ro \
  -v /usr/share/collectd/types.db:/usr/share/collectd/types.db:ro \
 influxdb:1.8 -config /etc/influxdb/influxdb.conf


```

### python 脚本运行
```shell

* * * * * python3 /root/ops/tools/monitor/collection_to_prometheus.py
  
```