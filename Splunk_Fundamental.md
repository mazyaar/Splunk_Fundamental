# Splunk:

>1-	Introduce
2-	Implement
3-	Setup and Configurations.
4-	Advance Configurations
5-	Applications and Add-on
6-	Distributed structure
7-	Forwarder Management
8-	Users Management
9-	Dashboard Management
10-	Advance Settings

------------------------------------------------------------------------------------------------------------------------------------------------

# 1-	Introduce

## SOC Process:


-	Identify
-	Protect
-	Detect
-	Response
-	Recover

## Cyber Attack lifecycle:


-	Reconnaissance
-	Intial compromise
-	Command and control
-	Lateral Movment
-	Target attainment
-	Exfilltration, Corruption and disruption.


## Splunk Enterprise Data Pipeline:

*	Data Input
*	Parsing
*	Indexing
*	Search


## Splunk Feature		LOG MGMT

### Data input			indexer
				universal Forwarder
				heavy forwarder
				
				
### parsing				indexer
				heavy forwarder


### search			indexer
				search head
				
								
### Database			NoSQL MongoDB Search engine


### Pros			Pipeline strategy
				User access and roles
				Xml support
				Best Documentation Support
				
				
### Query			SPL splunk standard


### Supported programming		.Net, Groovy, Community Contributed Clients, Java, JavaScript Perl, PHP, Python, Ruby.

### languages		 


## Splunk Instance:


## 1- Forwarders: Forward Log to indexer.

- universal forwarder, light forwarder, Heavy forwarder.


> Note: Advice to use heavy forwarder when you want seprate parsing data and indexing data from specific instance and use two seprate instance.


## 2- Splunk Deployment Server:
- to Management and configuration all instance. is unit of content deployed the members of one or more server classes.

 
## 3- Splunk Indexer: for index data and send log to search head.



## Splunk Stractures

###### 1- Instance standalone: 1-Forwarder 2-parser 3-Indexer 4-Search Head

* Note: Most Company Implement Parsind and Indexing together on a Instance, if scale is very big you should seprate with use heavy forwarder and seprate parsing and indexing.

Master Cluster Node(MC): It's one of Splamk's instance that manage Cluster Indexers. Indexer and search head to find each other should negotiate with Master Cluster instance. 


Deployer: A Splunk Enterprise instance that distributes baseline configurations and apps to search head cluster members.
* Note: The deployer is not a search head cluster membe

* Note: for DB on Instance, Use extra SSD hard disk


License Master(LM): Setup all icense on this Machine.

* Note: Forwarder use Deployment Server and Search Head and Indexer Uses Master Cluster Node for Managment.

------------------------------------------------------------------------------------------------------------------------------------------------

## Splunk Dashboard.
* first login then: ``setting/ data input/ find Proitocol of log/ Add new/ set Port/ choice Source type/ set new source type for each log/ ``


Add new index: ``setting/ indedx/ New Index/ Set index name/ Save.``



## Splunk file Configurations:
-	Splunk file Configuration Include 3 parts:

-	file configuration: every file with .conf suffix
-	Stanza: Settings in strings, like change protocol, port or etc.
-	attribute:	setting that in sub stanza.


------------------------------------------------------------------------------------------------------------------------------------------------
## Splunk Enterprise .conf file:

_file configuration name: [file].conf_

*** Two parts in file configuration:***

> 1- stanza: all of setting that occurd in a string is a stanza.
> 2- setting of stanza that named attribute.


 _for example:_
		file configuration: web.conf
		stanza: [setting]

		attribute:	enableSplunkwebSSl=0
				httpport = 8500
				
 _Note: 0 is splunk conf means no._

 _Note:_	
 
- 1- Important file configuration
		Default (Splunk defaults)
		Local (admins customzation)
- 2- Don't edit default configurations._	
- 3- To set custom configurations, create configurations file in 'local' directory
- 4- Any change configuration in web GUI or CLI api, create new file configuration in local directory.
- 5- Restart splunk service after any change from configuration files.
- 6- Import: Attributes of stanza start with lowercase letters.
- 7- Configuration file precedence.

		system local directory - highest priority
		app local directories
		app default directories
		system default directories
		
- 8- List of important configuration files:

	1- inputs.conf: include all of setting that sets for inputs splunk, such forwarders and file system monitoring. 
		
	2- outputs.conf: include all of setting that sets for outputs splunk, send data receiving splunk instance, either indexers or  other forwarders.

	3- web.conf: possible attribute and values you can use to configurations.
		
	4- server.conf: configurations servers options.
		
	5- indexes.conf: configurations indexes options.
------------------------------------------------------------------------------------------------------------------------------------------------
## Clustering Factor: 
	Replication factor:
		the number of copy of data that you want thr cluster to maintain.
	
	search factor:
		the number of searchable copies of data that an indexer cluster maintains.
		
		
[fortigate]
		homePath=$SPLUNK_DB\fortigate\db

		$SPLUNK_DB:/opt/splunk/var/lib/splunk/IndexName/(/db or /colddb or /thaweddb)

		(you can edit $SPLUNK_DB in $SPLUNK_HOME/etc/splunk-launch.conf)
***
------------------------------------------------------------------------------------------------------------------------------------------------
## Splunk for Medium size Business:
_(Senario: Running a Splunk on small business)._

- 1 * windows Core:
    * LM
    * CM
    * MC
	
- 4 * Linux
```
		vi /etc/hostname
       		sh01.test.ir
```

IPs: 
- LM,CM,MC = 172.17.97.95
- SH: 172.17.97.96
- Indexer1: 172.17.97.97
- Indexer2: 172.17.97.98
- forwarder: 172.17.97.99
- windows: 172.17.97.93
- linux: 172.17.97.94
- firewall: 172.17.97.92
    
on windows servedr core:       

		vi /etc/sysconfig/network-scripts/ifcfg-ens192

```
	BOOTPROTO=static
	IPADDR=172.17.106.165
	NETMASK=255.255.255.240
	GATEWAY=172.17.106.161
	DNS1=8.8.8.8
	ONBOOT=YES
```
```
systemctl stop firewalld
systemctl disable firewalld
systemctl status firewalld
```


		systemctl restart networkmanager
		shutdown -r now
------------------------------------------------------------------------------------------------------------------------------------------------

1 x Windows server core (CM01): Cluster Manager

Install Monitor console, cluster Master, License Master.

4 x  linux instance: indexer*2, forwarder, Search Head.

commands:
#### powershell
```
rename-computer CM01
set-netfireweallprofile -Profile Domain,Public,Private -Enabled False
get-psdrive
```
---------------
```
cd d:\
.\setup.exe /S /v /qn (for vmware tools)
```

#### Network settings:
```
get-netadapter
$netadapter = get-netadapter -name Ethernet0
$netadapter | set-netipinterface -dhcp disabled
$netadapter | set-netipaddress -addressfamily IPv4 -ipaddress 172.17.97.95 -perfixlength 26 -type unicast -defaultgateway 172.17.97.65
$netadapter | set-dnsclient serveraddress -serveraddress 8.8.8.8
```
```
get-netipconfiguration
get-childitem ( for lists files)
.\splunk-8.XXXXX.msi
\splunk ip address: [ip]:8000
```
------------------------------------------------------------------------------------------------------------------------------------------------
Splunk Command on linux:
```
/opt/splunk/./splunk enable boot-start
/opt/splunk/./splunk enable set web-port 80
/opt/splunk/./splunk restart
```
------------------------------------------------------------------------------------------------------------------------------------------------
Forwarder:

```vi /opt/splunkforwarder/etc/system/local/outputs.conf```

```
[tcpout]
defaultGroup = indexers

[tcpout:indexers]
autoLBFrequency = 30
forceTimeBaseAutoLB = true
indexerDiscovery = splunkidxcm
useACK = true

[indexer_discovery:splunkidxcm]
pass4Symmkey = UF@test
master_uri = https://ipaddress:8089
```

```
/opt/splunkforwarder/bin/./splunk restart
```
------------------------------------------------------------------------------------------------------------------------------------------------

go to cluster Master to configuration for send log to Universal Forwarder
```
c:\program Files\splunk\etc\system\local\server.conf
#edit with notpad++ and add end of configuration:
```
```
[indexer_discover]
pass4Symmkey = UF@test
pollin_rate = 10
indexerWeightByDiskCapacity = true
```

.\splunk.exe restart
------------------------------------------------------------------------------------------------------------------------------------------------

for configuration Indexers go to Cluster Master:
```
c:\program Files\splunk\etc\master-apps\_cluster\local\indexes.conf
```
```
[windows]
repFactor = auto
enableDataIntegirityControl = 1
enableTsidxReduction = 1
timePeriodInsecBeforetsidxReduction = 15552000
homePath = $SPLUNK_DB/windows/db
coldPath = $SPLUNK_DB/windows/colddb
thawedPath = $SPLUNK_DB/windows/thaweddb

[linux]
repFactor = auto
enableDataIntegirityControl = 1
enableTsidxReduction = 1
timePeriodInsecBeforetsidxReduction = 15552000
homePath = $SPLUNK_DB/linux/db
coldPath = $SPLUNK_DB/linux/colddb
thawedPath = $SPLUNK_DB/linux/thaweddb

[fortigate]
repFactor = auto
enableDataIntegirityControl = 1
enableTsidxReduction = 1
timePeriodInsecBeforetsidxReduction = 15552000
homePath = $SPLUNK_DB/fortigate/db
coldPath = $SPLUNK_DB/fortigate/colddb
thawedPath = $SPLUNK_DB/fortigate/thaweddb
```

for apply configuration:
```
.\splunk.exe apply cluster-bundle --answer-yes
```
------------------------------------------------------------------------------------------------------------------------------------------------

#### configuration universal forwarder:
```
nano /opt/splunkforwarder/etc/system/local/inputs.conf
```
```
[default]
host = uf01.test.ir

[udp://514]
connection_host = ip
index = fortigate
sourcetype = fg_log

[udp://25514]
connection_host = ip
index = fortiweb
sourcetype = fwb_log
```



for restart: ``/opt/splunkforwarder/bin/./splunk restart``

------------------------------------------------------------------------------------------------------------------------------------------------

#### on both indexer servers setup port for recive log from UF:
```
nano /opt/splunkforwarder/etc/system/local/inputs.conf
```
```
[splunktcp://9797]
disabled=0
```
```
/opt/splunkforwarder/bin/./splunk restart
```
------------------------------------------------------------------------------------------------------------------------------------------------

## Splunk for Large size Business:


1  x windows Core:
    LM
    CM
    MC
	
4 x Linux
```
vi /etc/hostname
       sh01.test.ir
  ```     
```
IPs: 
LS01 (windows Core include License master, Distributed Management Console, Deployment server) = 172.17.97.101 
Master Cluster (CM,MC, Deployer) = 172.17.97.100
Search Head (SH01): 172.17.97.111
Search Head (SH02): 172.17.97.112
Indexer(IDX01): 172.17.97.113 with seprate database
Indexer (IDX02): 172.17.97.114 with seprate database
Universal forwarder (UF01): 172.17.97.99
Pc01 = 172.17.97.93
Pc02 = 172.17.97.94
firewall (FGT) = 172.17.97.92
Fortiweb (FWB) = 172.17.97.109
```

* Notice: one of search head must set as captain.
* Notice: Forwarder never cluster. you should use load balancer and set setting on each forwarder manually.

------------------------------------------------------------------------------------------------------------------------------------------------
Create virtual disk lvm:
```
pvcreate /dev/sdb/ /dev/sdb
pvcreate /dev/sdb/ /dev/sdc
```
```
vgcreate vg1 /dev/sdb
vgcreate vg2 /dev/sdc
```
```
lvcreate -n hot 99G vg1
lvcreate -n cold 99G vg2
```
```
mkdir /splunk 
mkdir /archive
```
```
mkfs.ext4 /dev/vg1/hot
mkfs.ext4 /dev/vg1/cold
```
```
mount /dev/vg1/hot /splunk
mount /dev/vg2/cold /archive 
```
```
blkid
nano /etc/fstab

UUID=XXXXXXXXXXXXXXXXX(blkid)	/splunk	ext4	default	0 0
UUID=XXXXXXXXXXXXXXXXX(blkid)	/archive	ext4	default 0 0
```
```
systemctl daemon-reload
systemctl restart networkmanager
```

------------------------------------------------------------------------------------------------------------------------------------------------

***config server.conf file configuration on Console Master:***
```
nano /opt/splunk/etc/system/local/server.conf
```
```
[shclustering]
pass4Symmkey = test@shd
shcluster_label = shd01
```

```/opt/splunk/bin/./splunk restart```


-----------------------------------------------------------------------------------------------------------------------------------------------

## connect search head by cluster master and deployer and choice search head captain:

>/opt/splunk/bin/./splunk init shcluster-config -mgmt_uri https://172.17.97.111:8089 -replication_port 9000 -replication_factor 2 -conf_deploy_fetch_url https://172.17.97.100:8089 -secret test@shd -shcluster_label shd01
```
/opt/splunk/bin/./splunk restart
```

## set captain search head:
```
/opt/splunk/bin/./splunk/ bootdtrap shcluster-captain -server_list "https://172.17.97.111:8089,https://172.17.97.112:8089"
```
```
/opt/splunk/bin/./splunk show shcluster-status
```
-----------------------------------------------------------------------------------------------------------------------------------------------

### universal forwarder (UF) Configurations:

```vi /opt/splunkforwarder/etc/system/local/outputs.conf```
```
[tcpout]
defaultGroup = indexers

[tcpout:indexers]
autoLBFrequency = 30
forceTimeBaseAutoLB = true
indexerDiscovery = splunkidxcm
useACK = true

[indexer_discovery:splunkidxcm]
pass4Symmkey = test@cm01uf
master_uri = https://172.17.97.100:8089
```

```/opt/splunkforwarder/bin/./splunk restart```

-----------------------------------------------------------------------------------------------------------------------------------------------

### Console Monitor (CM) Configuration:
```vi /opt/splunk/etc/system/local/server.conf```
```
[indexer_discovery]
pass4SymmKey = test@cm01uf
polling_rate = 10
indexerWeightByDiskCapacity = true
```
```
/opt/splunk/bin/./splunk restart
```
-----------------------------------------------------------------------------------------------------------------------------------------------

*** indexers (IDXs) Configurations: ***
```vi /opt/splunk/etc/system/local/inputs.conf```
```
[splunktco://9997]
disabled = 0
```
``` 
/opt/splunk/bin/./splunk restart 
```
-----------------------------------------------------------------------------------------------------------------------------------------------
*** indexers (IDXs) Configurations: ***
``` vi /opt/splunk/etc/system/local/indexes.conf ```

```
[idx-fgt]
repFactor = auto
enableDataIntegirityControl = 1
enableTsidxReduction = 1
timePeriodInsecBeforetsidxReduction = 15552000
homePath = $SPLUNK_DB/idx-fgt/db
coldPath = $SPLUNK_DB/idx-fgt/colddb
thawedPath = $SPLUNK_DB/idx-fgt/thaweddb

[idx-fwb]
repFactor = auto
enableDataIntegirityControl = 1
enableTsidxReduction = 1
timePeriodInsecBeforetsidxReduction = 15552000
homePath = $SPLUNK_DB/idx-fwb/db
coldPath = $SPLUNK_DB/idx-fwb/colddb
thawedPath = $SPLUNK_DB/idx-fwb/thaweddb

[windows]
repFactor = auto
enableDataIntegirityControl = 1 

enableTsidxReduction = 1
timePeriodInsecBeforetsidxReduction = 15552000
homePath = $SPLUNK_DB/windows/db
coldPath = $SPLUNK_DB/windows/colddb
thawedPath = $SPLUNK_DB/windows/thaweddb
```

```.\splunk.exe apply cluster-bundle --answer-yes```

or

```/opt/splunk/bin/./splunk apply cluster-bundle --answer-yes```

```
/opt/splunk/bin/./splunk restart
```
-----------------------------------------------------------------------------------------------------------------------------------------------
### universal forwarder (UF) Configurations:

```vi /opt/splunkforwarder/etc/system/local/inputes.conf```

```nano /opt/splunkforwarder/etc/system/local/inputs.conf```
```
[default]
host = uf01.test.ir

[udp://514]
connection_host = ip
index = idx-fgt
sourcetype = fgt_log

[udp://514]
connection_host = ip
index = idx-fwb
sourcetype = fwb_log
```
```
/opt/splunkforwarder/bin/./splunk restart
```
-----------------------------------------------------------------------------------------------------------------------------------------------
#### Distributed management console : for monitore all instance on splunk

- lincense Master: a windows core that licensed all splunk instance
- Deployment server : for deploye configurations like forwarders and endpoint configurations.
- Diployer on Master Cluster node: manage search heads.
- Indexers and Search heads must add on Master Cluster.
- for negotiated SH to Master Cluster:

```vi /opt/splunk/etc/system/local/server.conf```
```
[shclustering]
pass4Symmkey = password
shcluster_label = sh01
```

```/op/splunk/bin/./splunk restart```


### ON sh01:
***negotiate search head01 and search head deployer:***
```
/opt/splunk/bin/./splunk init shcluster-config -mgmt_uri https://[ipadd_SH01:8089] -replication_factor 2 -conf_deploy_fetch_url https://[ipaddress_MC] -secret [password] -shcluster_label [sh01]
```

```/opt/splunk/bin/./splunk restart```


***negotiate search head01 and cluster master:***
```
/opt/splunk/bin/./splunk edit cluster-config -mode searchhead -master_uri https://[ipaddress_Master Cluster:8089] -secret [password]
```


#### On sh02:

***negotiate search head02 and search head deployer:***
```
/opt/splunk/bin/./splunk init shcluster-config -mgmt_uri https://[ipadd_SH02:8089] -replication_factor 2 -conf_deploy_fetch_url https://[ipaddress_MC] -secret [password] -shcluster_label [sh02]
```
```
/opt/splunk/bin/./splunk restart
```

***negotiate search head01 and cluster master:***
```
/opt/splunk/bin/./splunk edit cluster-config -mode searchhead -master_uri https://[ipaddress_Master Cluster:8089] -secret [password]
```
```
/opt/splunk/bin/./splunk restart
```

***set captain for one of search head:***
```
/opt/splunk/bin/./splunk bootstrap shcluster-captain -servers_list "https://[ipaddress_sh01:8089],[ipaddress_sh02:8089]"
/opt/splunk/bin/./splunk show cluster-status
```

* notice: if you want use same port for many source to send log in indexers or Universal forwarder, you should specify source address on splunk input configuration for example set stanza on input.conf on UF like:
```
[udp://ip1_address1:514]
connection_host = ip
index = idx-fgt
sourcetype = fgt_log

[udp://ip2_address1:514]
connection_host = ip
index = idx-fwb
sourcetype = fwb_log
```
-----------------------------------------------------------------------------------------------------------------------------------------------
***Deployment server: this server deploy configuration on forwarders such linux, windows,UF and etc, you must configuration UFS deploymentclient.conf file:***

- STEP1:
```
vi /opt/splunkforwarder/etc/system/local/deploymentclient.conf
[deployment-client]
[target-broker:deploymentServer]
targetUri = [ip address of deployment server:8089]
```


- STEP2:
```
next steps is make apps directory for input and output on Deployment server for each instance on deployment server:
```
```
mkdir /opt/splunkforwarder/etc/deployment-apps/input_win
mkdir /opt/splunkforwarder/etc/deployment-apps/output_win
mkdir /opt/splunkforwarder/etc/deployment-apps/input_lins
mkdir /opt/splunkforwarder/etc/deployment-apps/output_lins
mkdir /opt/splunkforwarder/etc/deployment-apps/input_of_ufcores
mkdir /opt/splunkforwarder/etc/deployment-apps/output_of_ufcores
```
```
cd c:\Program File\Splunk\bin
.\splunk.exe restart
```

- STEP3:
* Make server class on deployment:

login deployment server, forwarder managment, Server class, add, create one, choice name [wins_clients], add client, set windows ips or dns name on white list, save

login deployment server, forwarder managment, Server class, add, create one, choice name [wins_clients], add client, set lins ips or dns name on white list, save

login deployment server, forwarder managment, Server class, add, create one, choice name [ufcores], add client, set ufcores ips or dns name on white list, save

open win_clients, add apps of wins: input_wins and outputs_wins
open lins_clients, add apps of wins: input_lins and outputs_lins
open ufcores, add apps of wins: input_of_ufcores and outputs_of_ufcores

priority of file configurations:

1. System local directory -- highest priority
2. App local directories
3. App default directories
4. System default directory -- lowest priority

* notice:
> if you configure input and output instance from deployment server will not overwrite and config merge with old configuration, so it's advice when you setup forwarder on instance you should delete or rename input and output file thats exist on system local directory.


```cd Program File/splunk/etc/deployment-apps/input_lins/local```

##### make a inputs.conf file:
```

[default]
host = linux-client.test.ir


[monitor://var/log/message]
disabled=false
sourcetype=syslog
index=lin01


```cd Program File/splunk/etc/deployment-apps/input_wins/local```

make a inputs.conf file:

[default]
host = windows01


[WinEventLog://Application]
checkpointInterval = 5
current_only = 0
disabled=0
start_from = oldest
index = windows01

[WinEventLog://Security]
checkpointInterval = 5
current_only = 0
disabled=0
start_from = oldest
index = windows01

[WinEventLog://System]
checkpointInterval = 5
current_only = 0
disabled=0
start_from = oldest
index = windows01
```


```cd Program File/splunk/etc/deployment-apps/input_of_ufcores/local```

##### make a inputs.conf file:
```
[default]
host = uf01

[monitor://var/log/message]
disabled=false
sourcetype=syslog
index=lin01


configuration for outputs:
cd Program File/splunk/etc/deployment-apps/output_lins/local
make a outputs.conf file:

[tcpout]
defaultGroup = default-autolb-group

[tcp:default-autolb-group]
server = [UF IP:9997]

configuration for outputs:
cd Program File/splunk/etc/deployment-apps/output_wins/local
make a outputs.conf file:

[tcpout]
defaultGroup = default-autolb-group

[tcp:default-autolb-group]
server = [UF IP:9997]
```

```cd Program File/splunk/etc/deployment-apps/output_of_ufcores/local```
##### make a outputs.conf file:
```
[tcpout]
defaultGroup = default-autolb-group

[tcp:default-autolb-group]
server = [UF IP:9997]
```
```
cd \Program File\splun\bin\
.\splunk.exe reload deploy-server
```


* on each forwarder a directory named apps shows and configuration deploy on this directory.

-----------------------------------------------------------------------------------------------------------------------------------------------
manage multi forwarder with deployment server:

you can separate parsing from indexer and config on heavy forwarder

Deployment server: for configure inputs.conf and output.conf and don't use it for established another config for another instance.

Beset practice: all same forwarders classified to specific server class, for ex: windows class, Linux server class.
and we can use a server class for universal forwarder.

for each server class make a specific app and assign to a server class, for windows forwarders create inputs and outputs app file and assigned to those.

with this command:
splunk reload deployment-server enforce forwarders to use new configure and merge new configurations.

-----------------------------------------------------------------------------------------------------------------------------------------------

>deployment server configuration overview step by step:

1- port tcp 9997 must be open.

2- create deploymentclient.conf on each forwarders.

3- configure deploymentclient.conf to use negotiate forwarders with deployment server.

4- configure server class and deploy apps to server class.

5- use deployment server command: splunk reload deployment-server, to import new configurations.
-----------------------------------------------------------------------------------------------------------------------------------------------

Senario overview:

1- setup UF on all OS

2- configure input.conf

3- open tcp port 9997

4- send logs to indexers and negotiate with Master Cluster Node.

5- send UF logs from indexer to UFcores then send from Ufcores to indexer with configure inputs and outputs file on all forwatders with deployment server step by step:

a- open tcp port 9997
b- configure deploymentclient.conf on all UF ( os and UFcore)
c- make a directory for each app.
d- classified on server classes and atrach apps and clients on it.
e- edit apps configurations files.
f- reload deployment server.


## Solutions:

### step 1:
setup last splunk forwarders to each OS then edit outputs.conf file on local directory:

```
[tcpout]
defaultGroup = indexers

[tcpout:indexers]
autoLBFrequency = 30
forceTimebaseAutoLB = true
indexerDiscovery = splunkidxcm
useACK=true

[indexer_discovery:splunkidxcm]
pass4SymmKey = password
master_uri = https://ipaddress_masterclaster:8089
```

and reload splunk forwarder,


### step 2:
then on windows os configure inputs.conf file, ( for Linux os in thihs scenario we configure inputs.conf with deployment server)
```
[default]
host = windows01


[WinEventLog://Application]
checkpointInterval = 5
current_only = 0
disabled=0
start_from = oldest
index = windows01

[WinEventLog://Security]
checkpointInterval = 5
current_only = 0
disabled=0
start_from = oldest
index = windows01

[WinEventLog://System]
checkpointInterval = 5
current_only = 0
disabled=0
start_from = oldest
index = windows01
```
and reload splunk forwarder,



### Step 3:
Next steps configure Master Cluster Node:

```vi /opt/splunk/etc/system/local/server.conf```
```
[indexer_discover]
pass4Symmkey = password
polling_rate = 10
indexerWeightByDiskCapacity = true
```


### Step 4:
on Indexers open tcp port 9997

```vi /opt/splunk/etc/system/local/inputs.conf```
```
[splunktcp://9997]
disabled=0
```
and reload splunk,


### step 5:

Master Cluster Node management indexer so if you want change indexers configuration you should use from it, so we want change indexers.conf then on cluster master change configurations of indexers:

notice: for each forwarders have specific indexer.

```vi /opt/splunk/etc/master-apps/_cluster/local/indexes.conf```

```
[windows01]
repFactor=auto
homePath = /splunk/windows01/db
coldPath = /archive/windows01/colddb
homePath = /splunk/windows01/thaweddb
enableDataIntegirityControl = 0
enableTSidxreduction = 1
timePeriodInSecBeforeTsidxReduction = 23328000
maxTotalDataSizeMB = 20000

[lin01]
repFactor=auto
homePath = /splunk/lin01/db
coldPath = /archive/lin01/colddb
homePath = /splunk/lin01/thaweddb
enableDataIntegirityControl = 0
enableTSidxreduction = 1
timePeriodInSecBeforeTsidxReduction = 23328000
maxTotalDataSizeMB = 20000
```
```/opt/splunk/bin/,/splunk aooly cluster-bundle --answer-yes```

-----------------------------------------------------------------------------------------------------------------------------------------------

## Monitor Console:
for centeral monitoring forwarder sources:

- Unconfigures MC
- standalone MC
- Distributed MC

Notice: don't setup MC on each Searchheads(SH)

- Indexers Monitored with Cluster Master and other instance monitored with Distributed Monitor Console.
- Cluster Master as Monitor Console:
>steps: login to Cluster Master,  settings, Monitor Console, Add peers, edit roles of each istance.


### Monitor console as DMC :

steps: login to Cluster Master,  settings, Monitor Console, settings, General Setup, choice Distributed.

then:

setting, Distributed search, search peers, Add peers, edit roles of each instances.
 

notice: if you want to use Distributed Monitor Console for Monitored all instance:
1- add Cluster Master to Monitor console as search peers.
2- go to forwarder, forwarders deployment, on Monitor console, and enable, save.
3- configure monitoring console instance as a search-head in cluster master for Monitor console can monitore cluster Master:
> go to Monitor console that setup pn license master and configure with this command:
.\splunk.exe edit cluster-config -mode searchhead -master-uri https://ip_add_Clustermaster:8089 -secret password

```./splunk.exe restart```

and check on cluster master that knows monitor console as Searchhead.

-----------------------------------------------------------------------------------------------------------------------------------------------
## Search Processing Language events:

sourcetype: is one of default field that splunk software assigns to all incoming data.

fields: field appear in event data as searchable name-value pairing such as user_name or ip_address or etc.

pipeline: with this option you can use from result of search 1 to use on search 2 and ...


Keywords: modify result of search to view

* as
* by
* over
* where (also in result and filtering cat.)

***Operation: including or excluding fields value.***

* AND
* OR
* NOT
* !=

***correlation: these commands can be used to build correlation searchable.***

* lookup (Also in field/Add cat.)
* stats (Also in Report cat.)
* transaction (Also in result/grouping cat.)
* correlate (Also in report cat.)

***field: these are commands you can use to add, extract, and modify field or field value.***

* ADD
* eval
* EXTRACT
* rex
* MODIFY
* replace
* rename

***reports: these command are to build transforming search. these command return statistical data tables that are required for charts and other kinds of data visualizations.***

* addtotals
* top
* rare
* chart
* timechart

***results: these command can be used manage search results.***

* Alerting
* sendmail
* filtering
* dedup
* field
* regex
* table
* Generating
* search
* Reordering

***time: use these command to search based on time range or add time information to your events.***

* earliest
* latest

-----------------------------------------------------------------------------------------------------------------------------------------------

# keywords:

## as: 
```
index = web1 | timechart sum(bytes) as total_bytes
```
(set a name to shows on charts.)

## by: 
```
index = web1 | timechart sum(bytes) by categoryId
```
(shows search as a specific field)

## over: 
```
index = web1 | chart sum(bytes) as total_client_bytes over categoryId
```
(shows table as specific field pattern.)

## where: 
```
index = web1 | where status>200 | stats count by productid | sort -count
```
(show search result as new on past searches results.)

## note: space knows as a AND
-----------------------------------------------------------------------------------------------------------------------------------------------

# Boolean and oprators ( Uppercase only)

## AND: 
```
index = web1 AND status=2* And clientip=192.* | stats count by clientip | sort -count
```
(use for multi search thats all is true.)

## OR: 
```
index = web1 status=2* OR status=5* | stats count by clientip.status | sort -count
```
(use for search multi search thats one of search is true.)

## NOT: 
```
index = web1 NOT status=2* NOT status=5* | stats count by clientip.status | sort -count
```

(use for search that's not true.)

!= 
```
index = web1 NOT status!=2* status!=5* | stats count by clientip.status | sort -count
```
(use for multi search that's specific strings or value is not true.)


> "stats count by categoryId" means: (shows count of categoryId as chart)

> stats count by categoryId clientIp _time : (shows count of categoryId and clientip as chart)

-----------------------------------------------------------------------------------------------------------------------------------------------

# Correlations:


## lookup: 
```
sourcetype=vendor_* | stats count by code vendorID | lookup prices code
```
(look up a specific value on a search)

## stats: 
```
index=web1 | stats sum(bytes) AS total
```
(Perform the calculation process with count, sum or avg)

## transaction: \
```
index=web1 | transaction UserName  startswith=(status=in) endswith=(status=out)
```
(shows chart as a specific pattern.)

## correlate: 
```
index=web1 | where status>200 | stats count by productid | sort -count
```
(shows correlation between many value of search.)

-----------------------------------------------------------------------------------------------------------------------------------------------

# Fields


eval: 
```
index=web1 status!=2* | eval bytesKB=bytes/1024 | table clientIp bytes bytesKB
```
(Make new field from a ordinary pattern for search.)

## rex: 
```
index=web1 | rex field=useragent "(?<browser>[^\/]+)" | table browser | dedup browser
```
(Make new field and edit a value or a field (sed) from unordinary pattern.)

## replace: 
```
index=web1 | stats count by clientIp status | replace 200 with success in status | dedup status
```
(replace value of a field from any value.)

## rename: 
```
index=web1 | stats count by clientIp status | rename clientIp as srcip, status as responsecode
```
(replace name of a value by another value with "")

-----------------------------------------------------------------------------------------------------------------------------------------------

# Reports


## addtotals: 
```
index=web1` | chart count by action categoryId | addtotal fieldname=action_total row=t col=t labelfield=action label=categotuId_total
```
(set name to shows total value on table.)

## top: 

```
index=web1 | top clientIp 
```
(top of searches recordes.)


> - default is 10 top value.
- use limit for increase count of value.
- use showperc with f/t value for shows percentage.
- use count field for named top field.

## rare: 
```
index=web1 | rare clientIp
```
(low of searches recordes.)

## chart: 

```
index=web1 | chart count by action status
index=web1 | chart sum(bytes) as totalbytes by action
```

(Make az search for shows on charts.)

## timechart 
```
index=web1 | timechart sum(bytes) as totalbytes by action
```
(set time for results.)
-----------------------------------------------------------------------------------------------------------------------------------------------

# Result

## sendemail
```
index=web1 | method=POST erliest=@d+6h | sendemail ro="email@gmail.com" sendresult=true
```
(send search result to email.)

## dedup 
```
index=web1 | stats count by clientIp status | dedup clientip
```
(delete dupplicate value on search when shows.)



## fields
```
index=web1 | fields clientIp status | field - _*
index=web1 | fields - clientIp status | outputcsv CSVNAME
```
(reload or delete fields from results of searches.)
- 1. _time and _raw are default fieilds on internal searches.
- 2. others save to outputcsv
- 3. reload interval with make new field by eval.

## regex
```
index=web1 | where status>200 | stats count by productId | sort -count
```
(use this to delete unusable fields and value and find a suitable pattern.)

## table
```
index=web1 | table _time clie* status
```
(make table for fields)

## search
```
index=web1 | rex field=useragent "(?<browser>[^\/]+)" | search browser=moz* status>200
```
(make a search from last piplines like status, database, use, error, failed, Arecord,...)

## head 
```
index=web1 | table clientip status _time | head 3
```
(default search 10 first result)

## tail 
```
index=web1 | table clientip status _time | tail 3
```
(default search 10 last result)

## sort 
```
index=web1 | table clientip status _time | sort -_time
```
(sort results)

*** use " - " mines status.

## time

time picker : use from GUI.

### cli base*** : 

***cli Absolute time range***
```
index web earliest=09/05/2020:09:00:00 latest=09/05/2020:10.:20:00
```
***relative time range***
> relative time (-) (+)
```
index=web earliest=-2w latest=now
```
> relative snap to a time boundary
```
index=web earliest=-mon@mon latest=@mon
```
* second			s,sec,secs,second,seconds
* minute			m,mm,minute,minutes
* hour				h,hr,hrs,hour,hours
* day				d,day,days
* week				w,week,weeks
* mounth			mon,mounth,mounths
* quarter			q,qtr,qtrs,quarter,quarters
* year				y,yr,yrs,year,years

> w0 and w7 are sunday for sutarday use: w0-1
use earliest=1 latest=now for retrive all time logs times.



##### -w@w : (go past week and start day one of week)
##### -w@d : (go past week and start from same day)
##### -w@h : (go past week and start from same hours)
##### -w@m : (go past week and start from same minute)
##### -W@s : (go past week and start from same Seconds)
##### @w0  : (start from first day of week)
##### @d-2h: (start from this day and 2hours past)
##### -2w@w+3d : (start from to week and on week add 3 days)
-----------------------------------------------------------------------------------------------------------------------------------------------
## Alert Workflow

1. search: what do yoou want track?
2. Alert type: How often do you want to chech events?
	* Real-Time
	*Schedule
3. Alert trigger conditions and throttling: How often do you want to trigger an alert?
	* Real-Time
	*Schedule
4. Alert Action: What happens when the alert triggers?
	* Email notificationAction.
	* use webhook alrt acion.
	* output results to a CSV lookup.
	* Lg events.
	* Monitor Triggered alerts.			

## Report Management

***Reports are created when you save a search or a pivot for later reuse. After a report is created, you can:***

   * Manually create and edit reports.
   * Accelerate slow-completing reports
   * Set up scheduled reports
   * Configure the priority of scheduled reports.
   * Understand how to generate PDFs of reports, dashboards, searches, and pivots. ​

    ​
    
## visualization:		1- Dashboard:	1-Static	2-Interactive
				2- Apps and Add-ons
				3- Dataset


## Dashboards Workflow

    1- Select a visualization
       
       * Events list (also with Sparkline)
       * Table
       * Charts
       * Single Value
       * Gauge
       * Maps

   2- Generate and configure visualizations

   3- Build and edit dashboards (dashboards made by PANELS)
        1- Static
        2- Interactive
           > Form inputs
                * Time input example (time)
                * Text Input (Text)
                * Form input example (dropdown)
                * Multiselect input example (multiselect)
                * Access labels and values of form inputs (radio)
                * Checkbox input (Checkbox)



## Knowledge Objects: 
> Knowledge objects are the units Splunk uses to interpret, classify, enrich, normalize, and model data. You can create, edit, save, and share knowledge objects.


***Apps***: 
> A collection of knowledge objects that address specific use cases.
Use this for Searchheads

			* Configuration files Dashboards, 
			* reports
			* Scripts
			* Lookup files

***Add-ons***

> A type of app that provides specific capabilities to other apps, such as getting data in, mapping data, or providing saved searches and macros for use by one or more apps. Add-ons do not contain a full UI, and often provide some custom configurations or data inputs.

### Free Apps

>
    Cisco networks
    Splunk dashboard examples
    Splunk app for infrastructure
    Splunk app for windows infrastructure
    Splunk app for unix and linux
    Security essentials
    Security essentials for fraud detection
    Security essentials for Ransomware

### Licensed apps

>
    Splunk app for ES
    Splunk app for Vmware
    Splunk app for MS Exchange
    Splunk app for PCI Compliance
    Splunk app for ITSI
    Splunk app for UBA

### Splunk add-on

>
    Splunk add-on for microsoft active directory
    Splunk add-on for microsoft windows
    Microsoft Windows DHCP addon for Splunk
    Splunk Add-On for Microsoft Sysmon
    Splunk Add-on for Microsoft IIS
    Splunk Add-on for Microsoft SQL Server
    Splunk Add-on for Unix and Linux


## Dataset Types: A dataset is a collection of data that you define and maintain for a specific business purpose.
 

		1- Lookups:	1-1. Lookup definitions
				1-2. Lookup table files
		
		2- Table Dataset (New and available in Cloud)
		
		3- Data model datasets:(A DATA MODEL will be created by DATASETS and will be visualize by PIVOTS)
				3-1. Hierarchical
				3-2. Add dataset types
				3-3. Field Extraction Types
				

## Authentication Methods

I. Internal

    1.Splunk Authentication (Password Policy)
       * Admin, Power, User, or Custom Capabilities Users
         https://docs.splunk.com/Documentation/Splunk/8.0.3/Security/Rolesandcapabilities

II. External

    1.LDAP
    2.Scripted Authentication API (SAML)
      *  RADIUS, PAM, etc.


## Hardening Access

	I. Password policy management
	II. Use tokens for users login
	III. Multifactor authentication such as Duo Security or RSA Security:
			- Push notification on smart phone
			- SMS message
			- Phone call
			- One time code generated by the mobile app
  
								
								
## Roles

Control access to resources on the Splunk platform by determine what 
	
	### permissions and capabilities
				
				(capabilities)Configurable in roles page:
					   	 	1.Role inheritance
					    		2.Capabilities
					    		3.Allowed and default indexes
					    		4.Search restrictions
    					    		5.Resource access

				(permissions)Configurable per Knowledge Object page:
							* You can set permission for search on each Knowledge Object such as Datasets, Reports, Alerts, Dashboards, etc.	


#  Regular Experesion:
 
***. = Means match all characters***
 
***\. = Means just match "." characters.***
 
***\\ = Match  backslash characters.***


| **Pattern** | **Description**                                |
|-------------|------------------------------------------------|
| `.`         | Any Character Except New Line                  |
| `\d`        | Digit (0-9)                                    |
| `\D`        | Not a Digit (0-9)                              |
| `\w`        | Word Character (a-z, A-Z, 0-9, _)              |
| `\W`        | Not a Word Character                           |
| `\s`        | Whitespace (space, tab, newline)               |
| `\S`        | Not Whitespace (space, tab, newline)           |
| `\b`        | Word Boundary                                  |
| `\B`        | Not a Word Boundary                            |
| `^`         | Beginning of a String                          |
| `$`         | End of a String                                |
| `[]`        | Matches Characters in brackets                 |
| `[^ ]`      | Matches Characters NOT in brackets             |
| `|`         | Either Or                                      |
| `( )`       | Group                                          |
| **Quantifiers** |                                            |
| `*`         | 0 or More                                      |
| `+`         | 1 or More                                      |
| `?`         | 0 or One                                       |
| `{3}`       | Exact Number                                   |
| `{3,4}`     | Range of Numbers (Minimum, Maximum)            |


#### Sample Regexs ####

[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+
```

```

**Regular Expressions**

| **regex**           | **Description**                                                |
|---------------------|----------------------------------------------------------------|
| \t                  | tab                                                            |
| a* a+ a?            | 0 or more, 1 or more, 0 or 1                                   |
| a{5} a{2,}          | exactly five, two or more                                      |
| a{1,3}              | between one & three                                            |
| ab\|cd              | match ab or cd                                                 |
| \xxx                | Latin character specified by an octal number xxx               |
| \xdd                | Latin character specified by a hexadecimal number dd           |
| \uxxxx              | Unicode character specified by a hexadecimal number dddd       |
| X (?=n) (?!n)       | Followed or not followed by a specific string n                |


| regex       | Samples                  |
|-------------|--------------------------|
| .           | 	.*               |
| \w\d\s      | \d{1,3}\.\d{1,3}\.\d{1,3}|
| \W\D\S      | \$\+                     |
| [abc]       |  ([\w\.\-]+)@([a-zA-Z_\-]+)(\.)([a-zA-Z]{2,6})       		 |
|	      |	[\w\.\-]+@[\w\.-]+[\w\.\-]+			 |
|	      |	.*@.*\..*  |
| [a-g] [A-G] | [a-zA-Z0-9]              |
| (abc)       | \(blog\.*\)              |



| regex       | Samples                  |
|-------------|--------------------------|
|    a* a+ a?        | 	 behr(olu)?(olu)?z              |
|    a{1,3}        | 	\d{3,4}-\d{7,8}               |
|    ab|cd        | 	(https|http).*.(pdf|doc)               |
|    X (?=n) (?!n)        | 	(?=.\d)(?=.[a-z])(?=.*[A-Z])(.{8,})               |
|   Linux shell         | 	cat /etc/passwd | grep -E "(root|^daemon)"               |
|   splunk         	| 	"Index=* | 
|			| rex "optional_filtering_assignment(?<srcip>regex)" <br> Result of regex -> will be assign to filter " |



| regex | Samples |
|---|---|
| \?=\! \? : | a\?=b means select a in ab <br> a\?lb means select a in ac but don't select a in ab <br> a\?:b means select ab in abc |
| rex mode=sed field=cmd "s/regex/convert/g" | Remove selected characters |
| rex mode=sed field=Location "s/Location#All Locations#//g" | Location#All Locations#Tehran |
| rex mode=sed field=Type "s/Device Type#All Device Types#//g" | Device Type#All Device Types#Datacenter |
| \[CmdAV=show CmdArgAV=system CmdArgAV=resources CmdArgAV=module CmdArgAV=all CmdArgAV=<cr>\] <br> Rex mode=sed field=usercmd "s/\^(?:\? : \?)\|CmdAV=\? \?)\|CmdArgAV=(?:\? : \?)\s\//\//g" |  |


##

