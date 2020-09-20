## Info
A fork of [eris-fleet](https://github.com/danclay/eris-fleet) that supports a more custom logging handler.

This is also the repo that is required for an upcoming IPC handler [eris-fleet-ipc-handler](https://github.com/BowserV2/eris-fleet-ipc-handler) made by BowserV2.

## How to use eris-fleet?
Please read DanClay's [README](https://github.com/danclay/eris-fleet/blob/master/README.md) for more information. 

## Usable listeners
Listener name|  `whatToLog` whitelist name
--|--
stats_update | `stats_update`
total_shutdown | `total_shutdown`
admiral_start | `admiral_start`
resharding_worker_killed | `resharding_worker_killed`
resharding_transition | `resharding_transition`
resharding_transition_complete | `resharding_transition_complete`
service_launch | `service_launch`
all_services_launched | `all_services_launched`
cluster_launch | `cluster_launch`
all_clusters_launched | `all_clusters_launched`
shards_spread | `shards_spread`
gateway_shards | `gateway_shards`
cluster_shutdown | `cluster_shutdown`
service_shutdown | `service_shutdown`
cluster_restart | `cluster_restart`
service_restart | `service_restart`

## Making your own listener
Adding your own listener to a cluster is simple using the process.send function

#### **Note**: If you add your own listener, you don't need to add it to the whitelist for `whatToLog`

Code in your cluster.
```js
process.send({
	op: 'log',
	msg: 'My custom message! This doesn\'t have to be a string',
	logger: 'myCustomLoggerName',
	source: `Cluster ${this.clusterID}` //Optional
})
```
#### **Note**: All options except `source` are required.

Admiral code.
```js
<Admiral>.on('myCustomLoggerName', (data) => { console.log(data) })
```
 
## Accessing the custom logging information
Accessing the logging information is simple.

#### **Note**: All logging information you wish to log must be put inside the [whatToLog](https://github.com/danclay/eris-fleet/blob/master/README.md#choose-what-to-log) object


```js
const { isMaster } =  require('cluster');
const { Fleet } =  require('eris-fleet-advanced-logging');

const options = {
	/* [ALL OPTIONS NEEDED BY DANCLAY'S VERSION], */
	whatToLog: {
		whitelist: ['admiral_start', 'cluster_start', 'cluster_ready', ...]
	}
}

const  Admiral  =  new  Fleet(options);

if(isMaster) {
    Admiral.on('log', m => {...}) //Do something with all messages.

    Admiral.on('admiral_start', m => {...}) //Do something with all messages from admiral startup

    Admiral.on('cluster_start', m => {...}) //Do something with a message when a cluster starts

    Admiral.on('cluster_ready', m => {...}) //Do something with a message when a cluster readies
}
```
