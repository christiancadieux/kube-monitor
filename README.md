# kube-monitor
Monitor kubernetes resources


Kube Monitor is a collection of agents that run at interval and try to establish the health of cluster resources like etcd and VIPs.
It is written in go and vuejs and can run on user's laptop.


## UI

If kube-monitor is started with -p [port#], it will generate a UI page showing the overall health of the agents and recent events.

![49f0ee80-056e-11eb-8416-94bf592fd8be (1561×943)20220919192000](https://user-images.githubusercontent.com/10535265/191146848-5bf35bc0-25c9-406a-b5be-8dcede7c51d7.png)
