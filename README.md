# kube-monitor
Monitor kubernetes resources


Kube Monitor is a collection of agents that run at interval and try to establish the health of cluster resources like etcd and VIPs.
It is written in GO and VueJS.


## UI

If kube-monitor is started with -p [port#], it will generate a UI page showing the overall health of the agents and recent events.
The page is updated in real-time as agents execute.

![49f0ee80-056e-11eb-8416-94bf592fd8be (1561×943)20220919192000 (1)](https://user-images.githubusercontent.com/10535265/191146980-88446e1f-aa40-459a-9b12-d76e91860181.png)


## Agent Health
kube-monitor can generate log and events. Events are similar to logs except that Debug/Trace is excluded and the extra 'OK' events is added.
These events are used to establish the 'health' of the agents. Health is a state where events occur over time.

| Health  | Description
|---------|--------------------------------------------------------------
|Fatal    | The resource monitored by the agent is failing with loss of service.
|Error    | The resource is failing but there is no loss of service.
|Warning  | This resource is not failing but the agent noticed issues like incorrect configuration.
|Info     | event/informational only. Has no impact on the health of the agent.
|OK       | Resource monitored by the agent is healthy.

![f892a480-0a30-11eb-88a7-8f7dc9b04e40 (1176×676)20220919192726](https://user-images.githubusercontent.com/10535265/191147434-fe60bfbe-48b0-4cdb-8d18-dad58dbb4b8d.png)




### Components => Agents => Cluster

![7607ac00-07c8-11eb-8549-7983db2e0500 (1207×749)20220919192743](https://user-images.githubusercontent.com/10535265/191147420-08dd438b-aae9-4079-8544-67ea7a741bf1.png)


## Command

```
$ config/kube-monitor.osx cluster -h

Usage:
  monitor cluster [flags]

Flags:
Flags:
  -a, --agentlist string     Agents List. Can be comma-separated
  -c, --configdir string     Agents config dir (default "config")
  -d, --debug int            debug level (default=info,0=error, 1=info, 2=debug, 3=more debug) (default 1)
  -v, --gitDir string        Git Dir (default "git")
  -h, --help                 help for cluster
  -i, --iterations int       iterations (default=0 - continuously)
  -k, --kubeconfig string    kube config. Default to ~/.kube/config
  -x, --kubecontext string   kube context. Can be a comma-separated list or regex.
  -l, --logformat string     default=tty/color, t=color+time, j=json, s=splunk
  -u, --sshuser string       SSH User

  -P, --parallel             Run agents in parallel for -i iterations

  -R, --concurrency int      Node access concurrency
  -F, --frequency int        Agent Frequency (in seconds)

# Initial/Save/Compare:
  -I  --initial              Initial Run and before-upgrade checks.
  -S, --save                 Save resources to cache
  -C, --compare              Compare resource to cache

```


#### AgentList ( -a )

This is used to specify the agent to execute. It can be a comma-separated list of agents or the keywork 'all'.

Example: run the portworx agent, for one iteration against the default context .

```
$ config/kube-monitor.osx cluster -a px -i1
```

#### Initial Run (-I)

Initial Run is executed before the upgrade to save information.
Some agents (etcd) generate different errors when executed in the initial run, before the upgrade.

#### Save ( -S )

Some agents like the Persistent Volume agent (pv) allow to save the volumes in the config directory so they can
be compared later using the -C option. -I implies -S.

#### Compare ( -C )

Compare what was previously saved in the config directory using -S to the current information. This currently only
applies to the pv agent.

Example:
```
$ config/kube-monitor.osx cluster -a pv -S -i1

INFO[0000] Using user ccadie
INFO[0000] Using agent-config config/monitor.json
INFO[0000] Loading Vaquero models from /tmp/vaquero ...
INFO[0001] Vaquero hosts count:17837
INFO[0001] Vaquero models loaded
INFO[0001] Using kube-config /Users/ccadie/.kube/config
INFO[0001] -----------------------------------------------------------
INFO[0001] Using Context cluster1
INFO[0001] agent-pv start 2020-09-20 13:17:09
INFO[0001] Found 141 PVs
INFO[0001] Saved 141 PVs
INFO[0001] agent-pv end - duration=111 ms, Found 0 error, 0 warning

$ config/kube-monitor.osx cluster -a pv -C

INFO[0000] Using user ccadie
INFO[0000] Using agent-config config/monitor.json
INFO[0000] Loading Vaquero models from /tmp/vaquero ...
INFO[0000] Vaquero hosts count:17837
INFO[0000] Vaquero models loaded
INFO[0000] Using kube-config /Users/ccadie/.kube/config
INFO[0000] -----------------------------------------------------------
INFO[0000] Using Context cluster1
INFO[0001] agent-pv start 2020-09-20 13:17:21
INFO[0001] Found 141 PVs
INFO[0001] Compare - found 0 difference
INFO[0001] agent-pv end - duration=99 ms, Found 0 error, 0 warning
```


#### LogFormat ( -l )

 default:  short format with colors
```
 INFO[0005] Kube Hosts count: 1943
```

 -lt : color + time
```
 INFO[2020-09-14T15:10:19Z] Using Site site-1
```

 -lj: json format
```
{"level":"info","msg":"Using Site site-1","time":"2020-09-14T15:10:52Z"}
```

 -ls : Splunk Format
```
 2020-09-14T15:11:12.587Z level=INFO msg="Using Site site-1"
```


## Execute in container

This command mount your $HOME/.kube directory in the container to give kube-monitor access to your contexts.

```
 $ docker run \

       -v $(pwd)/config:/config \
       -v $HOME/.kube:/kube \
       -v $(pwd)/git:/git \
       -ti kube-monitor \
       /kube-monitor cluster -u $USER -d1 -s site-1 -a etcd

```

By default, the osx version will use the local directories config, git, ~/.kube/config for kubernetes contexts
and ~/.ssh/id_rsa|dsa for SSH access.



