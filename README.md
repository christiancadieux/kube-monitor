# kube-monitor
Monitor kubernetes resources


Kube Monitor is a collection of agents that run at interval and try to establish the health of cluster resources like etcd and VIPs.
It is written in go and vuejs and can run on user's laptop.


## UI

If kube-monitor is started with -p [port#], it will generate a UI page showing the overall health of the agents and recent events.

![49f0ee80-056e-11eb-8416-94bf592fd8be (1561×943)20220919192000 (1)](https://user-images.githubusercontent.com/10535265/191146980-88446e1f-aa40-459a-9b12-d76e91860181.png)


## Agent Health
rdei-monitor can generate log and events. Events are similar to logs except that Debug/Trace is excluded and the extra 'OK' events is added.
These events are used to establish the 'health' of the agents. Health is a state where events occur over time.

| Health  | Description
|---------|--------------------------------------------------------------
|Fatal    | The resource monitored by the agent is failing with loss of service.
|Error    | The resource is failing but there is no loss of service.
|Warning  | This resource is not failing but the agent noticed issues like incorrect configuration.
|Info     | event/informational only. Has no impact on the health of the agent.
|OK       | Resource monitored by the agent is healthy.



![7607ac00-07c8-11eb-8549-7983db2e0500 (1207×749)20220919192743](https://user-images.githubusercontent.com/10535265/191147420-08dd438b-aae9-4079-8544-67ea7a741bf1.png)


### Components => Agents => Cluster

![f892a480-0a30-11eb-88a7-8f7dc9b04e40 (1176×676)20220919192726](https://user-images.githubusercontent.com/10535265/191147434-fe60bfbe-48b0-4cdb-8d18-dad58dbb4b8d.png)
