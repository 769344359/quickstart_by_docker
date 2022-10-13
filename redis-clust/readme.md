

第一步,其中的`redis-cluster.tmpl` 的ip要改成自己的ip, 执行下面`shell`
```
for port in `seq 7001 7006`; do \
  mkdir -p ./redis-cluster/${port}/conf \
  && PORT=${port} envsubst < ./redis-cluster.tmpl > ./redis-cluster/${port}/conf/redis.conf \
  && mkdir -p ./redis-cluster/${port}/data; \
done
```

第二步:部署
```
docker stack deploy  --compose-file  docker-compose.yml   rediscluster
```

第三步:查看状态
```
docker stack ps  rediscluster
```

选择进入一个容器:
```
/usr/local/bin
```
然后执行创建主从的命令
```
redis-cli -p 7001  --cluster create 122.51.163.131:7001 122.51.163.131:7002 122.51.163.131:7003 122.51.163.131:7004 122.51.163.131:7005 122.51.163.131:7006 --cluster-replicas 1
```
最后输出
```
redis-cli -p 7001  --cluster create 122.51.163.131:7001 122.51.163.131:7002 122.51.163.131:7003 122.51.163.131:7004 122.51.163.131:7005 122.51.163.131:7006 --cluster-replicas 1
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 122.51.163.131:7005 to 122.51.163.131:7001
Adding replica 122.51.163.131:7006 to 122.51.163.131:7002
Adding replica 122.51.163.131:7004 to 122.51.163.131:7003
>>> Trying to optimize slaves allocation for anti-affinity
[WARNING] Some slaves are in the same host as their master
M: 7fde0e264f4c00e3f17d14409ff622cc8d980fcb 122.51.163.131:7001
   slots:[0-5460] (5461 slots) master
M: 6464883a323fa485606cd11e28aeec7d60571e24 122.51.163.131:7002
   slots:[5461-10922] (5462 slots) master
M: bcb8183aad2ccc97c536b05a94ef2d1f62dbb1d1 122.51.163.131:7003
   slots:[10923-16383] (5461 slots) master
S: aeee3f3adb40eaa604e891777aed07c21c833b2f 122.51.163.131:7004
   replicates 6464883a323fa485606cd11e28aeec7d60571e24
S: 7e672e9c36ce287185d331b598c7e6cc348446a5 122.51.163.131:7005
   replicates bcb8183aad2ccc97c536b05a94ef2d1f62dbb1d1
S: 2fa9abe8b67759a1ddfcb56b0f4bf5fcbd5e97df 122.51.163.131:7006
   replicates 7fde0e264f4c00e3f17d14409ff622cc8d980fcb
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
.
>>> Performing Cluster Check (using node 122.51.163.131:7001)
M: 7fde0e264f4c00e3f17d14409ff622cc8d980fcb 122.51.163.131:7001
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
S: aeee3f3adb40eaa604e891777aed07c21c833b2f 122.51.163.131:7004
   slots: (0 slots) slave
   replicates 6464883a323fa485606cd11e28aeec7d60571e24
M: 6464883a323fa485606cd11e28aeec7d60571e24 122.51.163.131:7002
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: 2fa9abe8b67759a1ddfcb56b0f4bf5fcbd5e97df 122.51.163.131:7006
   slots: (0 slots) slave
   replicates 7fde0e264f4c00e3f17d14409ff622cc8d980fcb
M: bcb8183aad2ccc97c536b05a94ef2d1f62dbb1d1 122.51.163.131:7003
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
S: 7e672e9c36ce287185d331b598c7e6cc348446a5 122.51.163.131:7005
   slots: (0 slots) slave
   replicates bcb8183aad2ccc97c536b05a94ef2d1f62dbb1d1
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered

```



## 相关阅读
- https://blog.csdn.net/qq_34901049/article/details/99670449
-  https://stackoverflow.com/questions/47670979/docker-service-in-swarm-fails-with-error-ask-non-zero-exit-1
- http://c.biancheng.net/view/3213.html
-  https://redis.io/topics/cluster-tutorial
-  https://zhuanlan.zhihu.com/p/216211089
- https://www.cnblogs.com/idea360/p/12391416.html
