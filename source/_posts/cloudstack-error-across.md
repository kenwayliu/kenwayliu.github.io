title: Cloudstack 4.2.1-无法创建vm
date: 2014-03-13 23:49:33
categories: CloudStack-errorlog
comments: true
tags:
        - IAAS
---

1.今天创建vm,抛出以下异常
通过以下方法可以解决
<!--more-->

```ruby
2014-03-13 16:15:31,228 DEBUG [cloud.storage.VolumeManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Checking if we need to prepare 2 volumes for VM[User|DSJAppServer01]
2014-03-13 16:15:31,248 DEBUG [storage.image.TemplateDataFactoryImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) template 290 is already in store:2, type:Image
2014-03-13 16:15:31,274 DEBUG [storage.datastore.PrimaryDataStoreImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Not found (templateId:290poolId:23) in template_spool_ref, persisting it
2014-03-13 16:15:31,293 DEBUG [storage.image.TemplateDataFactoryImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) template 290 is already in store:23, type:Primary
2014-03-13 16:15:31,295 DEBUG [storage.volume.VolumeServiceImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Found template 290-2-d3ccfe00-e08a-3a52-86f1-e19c4e26f2a7 in storage pool 23 with VMTemplateStoragePool id: 223
2014-03-13 16:15:31,307 DEBUG [storage.volume.VolumeServiceImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Acquire lock on VMTemplateStoragePool 223 with timeout 3600 seconds
2014-03-13 16:15:31,314 INFO  [storage.volume.VolumeServiceImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) lock is acquired for VMTemplateStoragePool 223
2014-03-13 16:15:31,331 DEBUG [storage.motion.AncientDataMotionStrategy] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) copyAsync inspecting src type TEMPLATE copyAsync inspecting dest type TEMPLATE
2014-03-13 16:15:31,375 DEBUG [agent.transport.Request] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Seq 18-1940796686: Sending  { Cmd , MgmtId: 345050394595, via: 18, Ver: v1, Flags: 100111, [{"org.apache.CloudStack.storage.command.CopyCommand":{"srcTO":{"org.apache.CloudStack.storage.to.TemplateObjectTO":{"path":"template/tmpl/2/290/60539132-19a1-3dfe-b425-fdb45fd9076d.ova","origUrl":"http://172.22.2.1/inspur/inspurcloud/customvm/win200864bitnopatch.ova","uuid":"d1da06af-6ae4-4d5f-80c6-a6a6fd373311","id":290,"format":"OVA","accountId":2,"checksum":"2c609d3019b0cadf5943793f4b8edd9f","hvm":true,"displayText":"win2008 64bit without patch","imageDataStore":{"com.cloud.agent.api.to.NfsTO":{"_url":"nfs://172.22.2.1/vg02data01/icp/ss01","_role":"Image"}},"name":"290-2-d3ccfe00-e08a-3a52-86f1-e19c4e26f2a7","hypervisorType":"VMware"}},"destTO":{"org.apache.CloudStack.storage.to.TemplateObjectTO":{"origUrl":"http://172.22.2.1/inspur/inspurcloud/customvm/win200864bitnopatch.ova","uuid":"d1da06af-6ae4-4d5f-80c6-a6a6fd373311","id":290,"format":"OVA","accountId":2,"checksum":"2c609d3019b0cadf5943793f4b8edd9f","hvm":true,"displayText":"win2008 64bit without patch","imageDataStore":{"org.apache.CloudStack.storage.to.PrimaryDataStoreTO":{"uuid":"841a5ebc-f2fc-3a18-84bb-92c06dd51318","id":23,"poolType":"VMFS","host":"VMFS datastore: /INSPURXCICP/DSJ01-R02-V01","path":"/INSPURXCICP/DSJ01-R02-V01","port":0}},"name":"290-2-d3ccfe00-e08a-3a52-86f1-e19c4e26f2a7","hypervisorType":"VMware"}},"executeInSequence":true,"wait":10800}}] }
2014-03-13 16:15:31,375 INFO  [agent.manager.AgentAttache] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Seq 18-1940796686: Unable to send due to Resource [Host:18] is unreachable: Host 18: Channel is closed
2014-03-13 16:15:31,375 DEBUG [agent.manager.AgentAttache] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Seq 18-1940796686: Cancelling.
2014-03-13 16:15:31,375 DEBUG [CloudStack.storage.RemoteHostEndPoint] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Failed to send command, due to Agent:15, com.cloud.exception.AgentUnavailableException: Resource [Host:18] is unreachable: Host 18: Channel is closed
2014-03-13 16:15:31,375 DEBUG [storage.motion.AncientDataMotionStrategy] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) copy object failed: 
com.cloud.utils.exception.CloudRuntimeException: Failed to send command, due to Agent:15, com.cloud.exception.AgentUnavailableException: Resource [Host:18] is unreachable: Host 18: Channel is closed
	at org.apache.CloudStack.storage.RemoteHostEndPoint.sendMessage(RemoteHostEndPoint.java:104)
	at org.apache.CloudStack.storage.motion.AncientDataMotionStrategy.copyObject(AncientDataMotionStrategy.java:210)
	at org.apache.CloudStack.storage.motion.AncientDataMotionStrategy.copyAsync(AncientDataMotionStrategy.java:411)
	at org.apache.CloudStack.storage.motion.DataMotionServiceImpl.copyAsync(DataMotionServiceImpl.java:55)
	at org.apache.CloudStack.storage.volume.VolumeServiceImpl.createBaseImageAsync(VolumeServiceImpl.java:440)
	at org.apache.CloudStack.storage.volume.VolumeServiceImpl.createVolumeFromTemplateAsync(VolumeServiceImpl.java:569)
	at com.cloud.storage.VolumeManagerImpl.recreateVolume(VolumeManagerImpl.java:2536)
	at com.cloud.storage.VolumeManagerImpl.prepare(VolumeManagerImpl.java:2592)
	at com.cloud.vm.VirtualMachineManagerImpl.advanceStart(VirtualMachineManagerImpl.java:889)
	at com.cloud.vm.VirtualMachineManagerImpl.start(VirtualMachineManagerImpl.java:578)
	at org.apache.CloudStack.engine.cloud.entity.api.VMEntityManagerImpl.deployVirtualMachine(VMEntityManagerImpl.java:237)
	at org.apache.CloudStack.engine.cloud.entity.api.VirtualMachineEntityImpl.deploy(VirtualMachineEntityImpl.java:209)
	at com.cloud.vm.UserVmManagerImpl.startVirtualMachine(UserVmManagerImpl.java:3406)
	at com.cloud.vm.UserVmManagerImpl.startVirtualMachine(UserVmManagerImpl.java:2966)
	at com.cloud.vm.UserVmManagerImpl.startVirtualMachine(UserVmManagerImpl.java:2952)
	at com.cloud.utils.component.ComponentInstantiationPostProcessor$InterceptorDispatcher.intercept(ComponentInstantiationPostProcessor.java:125)
	at org.apache.CloudStack.api.command.user.vm.DeployVMCmd.execute(DeployVMCmd.java:420)
	at com.cloud.api.ApiDispatcher.dispatch(ApiDispatcher.java:158)
	at com.cloud.async.AsyncJobManagerImpl$1.run(AsyncJobManagerImpl.java:531)
	at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
	at java.util.concurrent.FutureTask$Sync.innerRun(FutureTask.java:334)
	at java.util.concurrent.FutureTask.run(FutureTask.java:166)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1146)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
	at java.lang.Thread.run(Thread.java:679)
2014-03-13 16:15:31,376 DEBUG [storage.motion.AncientDataMotionStrategy] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) copy failed
com.cloud.utils.exception.CloudRuntimeException: com.cloud.utils.exception.CloudRuntimeException: Failed to send command, due to Agent:15, com.cloud.exception.AgentUnavailableException: Resource [Host:18] is unreachable: Host 18: Channel is closed
	at org.apache.CloudStack.storage.motion.AncientDataMotionStrategy.copyObject(AncientDataMotionStrategy.java:231)
	at org.apache.CloudStack.storage.motion.AncientDataMotionStrategy.copyAsync(AncientDataMotionStrategy.java:411)
	at org.apache.CloudStack.storage.motion.DataMotionServiceImpl.copyAsync(DataMotionServiceImpl.java:55)
	at org.apache.CloudStack.storage.volume.VolumeServiceImpl.createBaseImageAsync(VolumeServiceImpl.java:440)
	at org.apache.CloudStack.storage.volume.VolumeServiceImpl.createVolumeFromTemplateAsync(VolumeServiceImpl.java:569)
	at com.cloud.storage.VolumeManagerImpl.recreateVolume(VolumeManagerImpl.java:2536)
	at com.cloud.storage.VolumeManagerImpl.prepare(VolumeManagerImpl.java:2592)
	at com.cloud.vm.VirtualMachineManagerImpl.advanceStart(VirtualMachineManagerImpl.java:889)
	at com.cloud.vm.VirtualMachineManagerImpl.start(VirtualMachineManagerImpl.java:578)
	at org.apache.CloudStack.engine.cloud.entity.api.VMEntityManagerImpl.deployVirtualMachine(VMEntityManagerImpl.java:237)
	at org.apache.CloudStack.engine.cloud.entity.api.VirtualMachineEntityImpl.deploy(VirtualMachineEntityImpl.java:209)
	at com.cloud.vm.UserVmManagerImpl.startVirtualMachine(UserVmManagerImpl.java:3406)
	at com.cloud.vm.UserVmManagerImpl.startVirtualMachine(UserVmManagerImpl.java:2966)
	at com.cloud.vm.UserVmManagerImpl.startVirtualMachine(UserVmManagerImpl.java:2952)
	at com.cloud.utils.component.ComponentInstantiationPostProcessor$InterceptorDispatcher.intercept(ComponentInstantiationPostProcessor.java:125)
	at org.apache.CloudStack.api.command.user.vm.DeployVMCmd.execute(DeployVMCmd.java:420)
	at com.cloud.api.ApiDispatcher.dispatch(ApiDispatcher.java:158)
	at com.cloud.async.AsyncJobManagerImpl$1.run(AsyncJobManagerImpl.java:531)
	at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
	at java.util.concurrent.FutureTask$Sync.innerRun(FutureTask.java:334)
	at java.util.concurrent.FutureTask.run(FutureTask.java:166)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1146)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
	at java.lang.Thread.run(Thread.java:679)
2014-03-13 16:15:31,411 INFO  [storage.volume.VolumeServiceImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) releasing lock for VMTemplateStoragePool 223
2014-03-13 16:15:31,412 WARN  [utils.db.Merovingian2] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Was unable to find lock for the key template_spool_ref223 and thread id 1577994744
2014-03-13 16:15:31,412 DEBUG [cloud.storage.VolumeManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Unable to create Vol[491|vm=301|ROOT]:com.cloud.utils.exception.CloudRuntimeException: com.cloud.utils.exception.CloudRuntimeException: Failed to send command, due to Agent:15, com.cloud.exception.AgentUnavailableException: Resource [Host:18] is unreachable: Host 18: Channel is closed
2014-03-13 16:15:31,412 INFO  [cloud.vm.VirtualMachineManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Unable to contact resource.
com.cloud.exception.StorageUnavailableException: Resource [StoragePool:23] is unreachable: Unable to create Vol[491|vm=301|ROOT]:com.cloud.utils.exception.CloudRuntimeException: com.cloud.utils.exception.CloudRuntimeException: Failed to send command, due to Agent:15, com.cloud.exception.AgentUnavailableException: Resource [Host:18] is unreachable: Host 18: Channel is closed
	at com.cloud.storage.VolumeManagerImpl.recreateVolume(VolumeManagerImpl.java:2544)
	at com.cloud.storage.VolumeManagerImpl.prepare(VolumeManagerImpl.java:2592)
	at com.cloud.vm.VirtualMachineManagerImpl.advanceStart(VirtualMachineManagerImpl.java:889)
	at com.cloud.vm.VirtualMachineManagerImpl.start(VirtualMachineManagerImpl.java:578)
	at org.apache.CloudStack.engine.cloud.entity.api.VMEntityManagerImpl.deployVirtualMachine(VMEntityManagerImpl.java:237)
	at org.apache.CloudStack.engine.cloud.entity.api.VirtualMachineEntityImpl.deploy(VirtualMachineEntityImpl.java:209)
	at com.cloud.vm.UserVmManagerImpl.startVirtualMachine(UserVmManagerImpl.java:3406)
	at com.cloud.vm.UserVmManagerImpl.startVirtualMachine(UserVmManagerImpl.java:2966)
	at com.cloud.vm.UserVmManagerImpl.startVirtualMachine(UserVmManagerImpl.java:2952)
	at com.cloud.utils.component.ComponentInstantiationPostProcessor$InterceptorDispatcher.intercept(ComponentInstantiationPostProcessor.java:125)
	at org.apache.CloudStack.api.command.user.vm.DeployVMCmd.execute(DeployVMCmd.java:420)
	at com.cloud.api.ApiDispatcher.dispatch(ApiDispatcher.java:158)
	at com.cloud.async.AsyncJobManagerImpl$1.run(AsyncJobManagerImpl.java:531)
	at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
	at java.util.concurrent.FutureTask$Sync.innerRun(FutureTask.java:334)
	at java.util.concurrent.FutureTask.run(FutureTask.java:166)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1146)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
	at java.lang.Thread.run(Thread.java:679)
2014-03-13 16:15:31,425 DEBUG [cloud.vm.VirtualMachineManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Cleaning up resources for the vm VM[User|DSJAppServer01] in Starting state
2014-03-13 16:15:31,428 DEBUG [agent.transport.Request] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Seq 14-1829315172: Sending  { Cmd , MgmtId: 345050394595, via: 14, Ver: v1, Flags: 100111, [{"com.cloud.agent.api.StopCommand":{"isProxy":false,"executeInSequence":true,"vmName":"i-28-301-VM","wait":0}}] }
2014-03-13 16:15:31,428 DEBUG [agent.transport.Request] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Seq 14-1829315172: Executing:  { Cmd , MgmtId: 345050394595, via: 14, Ver: v1, Flags: 100111, [{"com.cloud.agent.api.StopCommand":{"isProxy":false,"executeInSequence":true,"vmName":"i-28-301-VM","wait":0}}] }
2014-03-13 16:15:31,429 DEBUG [agent.manager.DirectAgentAttache] (DirectAgent-431:null) Seq 14-1829315172: Executing request
2014-03-13 16:15:31,429 INFO  [vmware.resource.VmwareResource] (DirectAgent-431:172.22.1.6) Executing resource StopCommand: {"isProxy":false,"executeInSequence":true,"vmName":"i-28-301-VM","wait":0}
2014-03-13 16:15:31,429 DEBUG [vmware.mo.HostMO] (DirectAgent-431:172.22.1.6) find VM i-28-301-VM on host
2014-03-13 16:15:31,429 INFO  [vmware.mo.HostMO] (DirectAgent-431:172.22.1.6) VM i-28-301-VM not found in host cache
2014-03-13 16:15:31,429 DEBUG [vmware.mo.HostMO] (DirectAgent-431:172.22.1.6) load VM cache on host
2014-03-13 16:15:31,444 INFO  [vmware.resource.VmwareResource] (DirectAgent-431:172.22.1.6) VM i-28-301-VM is no longer in vSphere
2014-03-13 16:15:31,445 DEBUG [agent.manager.DirectAgentAttache] (DirectAgent-431:null) Seq 14-1829315172: Response Received: 
2014-03-13 16:15:31,445 DEBUG [agent.transport.Request] (DirectAgent-431:null) Seq 14-1829315172: Processing:  { Ans: , MgmtId: 345050394595, via: 14, Ver: v1, Flags: 110, [{"com.cloud.agent.api.StopAnswer":{"vncPort":0,"result":true,"details":"VM i-28-301-VM is no longer in vSphere","wait":0}}] }
2014-03-13 16:15:31,445 DEBUG [agent.manager.AgentAttache] (DirectAgent-431:null) Seq 14-1829315172: No more commands found
2014-03-13 16:15:31,445 DEBUG [agent.transport.Request] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Seq 14-1829315172: Received:  { Ans: , MgmtId: 345050394595, via: 14, Ver: v1, Flags: 110, { StopAnswer } }
2014-03-13 16:15:31,462 DEBUG [cloud.network.NetworkManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Changing active number of nics for network id=222 on -1
2014-03-13 16:15:31,762 DEBUG [cloud.vm.VirtualMachineManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Successfully released network resources for the vm VM[User|DSJAppServer01]
2014-03-13 16:15:31,762 DEBUG [cloud.vm.VirtualMachineManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Successfully cleanued up resources for the vm VM[User|DSJAppServer01] in Starting state
2014-03-13 16:15:31,785 DEBUG [cloud.deploy.DeploymentPlanningManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Deploy avoids pods: [], clusters: [], hosts: [14, 15]
2014-03-13 16:15:31,787 DEBUG [cloud.deploy.DeploymentPlanningManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) DeploymentPlanner allocation algorithm: com.cloud.deploy.FirstFitPlanner_EnhancerByCloudStack_a648ceb@5f712485
2014-03-13 16:15:31,787 DEBUG [cloud.deploy.DeploymentPlanningManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Trying to allocate a host and storage pools from dc:2, pod:2,cluster:null, requested cpu: 12000, requested ram: 68719476736
2014-03-13 16:15:31,787 DEBUG [cloud.deploy.DeploymentPlanningManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Is ROOT volume READY (pool already allocated)?: No
2014-03-13 16:15:31,788 DEBUG [cloud.deploy.FirstFitPlanner] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Searching resources only under specified Pod: 2
2014-03-13 16:15:31,788 DEBUG [cloud.deploy.FirstFitPlanner] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Listing clusters in order of aggregate capacity, that have (atleast one host with) enough CPU and RAM capacity under this Pod: 2
2014-03-13 16:15:31,796 DEBUG [cloud.deploy.FirstFitPlanner] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Removing from the clusterId list these clusters from avoid set: []
2014-03-13 16:15:31,808 DEBUG [cloud.deploy.DeploymentPlanningManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Checking resources in Cluster: 5 under Pod: 2
2014-03-13 16:15:31,808 DEBUG [allocator.impl.FirstFitAllocator] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ] FirstFitRoutingAllocator) Looking for hosts in dc: 2  pod:2  cluster:5
2014-03-13 16:15:31,814 DEBUG [allocator.impl.FirstFitAllocator] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ] FirstFitRoutingAllocator) FirstFitAllocator has 2 hosts to check for allocation: [Host[-14-Routing], Host[-15-Routing]]
2014-03-13 16:15:31,824 DEBUG [allocator.impl.FirstFitAllocator] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ] FirstFitRoutingAllocator) Found 2 hosts for allocation after prioritization: [Host[-14-Routing], Host[-15-Routing]]
2014-03-13 16:15:31,824 DEBUG [allocator.impl.FirstFitAllocator] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ] FirstFitRoutingAllocator) Looking for speed=12000Mhz, Ram=65536
2014-03-13 16:15:31,824 DEBUG [allocator.impl.FirstFitAllocator] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ] FirstFitRoutingAllocator) Host name: 172.22.1.6, hostId: 14 is in avoid set, skipping this and trying other available hosts
2014-03-13 16:15:31,824 DEBUG [allocator.impl.FirstFitAllocator] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ] FirstFitRoutingAllocator) Host name: 172.22.1.1, hostId: 15 is in avoid set, skipping this and trying other available hosts
2014-03-13 16:15:31,824 DEBUG [allocator.impl.FirstFitAllocator] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ] FirstFitRoutingAllocator) Host Allocator returning 0 suitable hosts
2014-03-13 16:15:31,824 DEBUG [cloud.deploy.DeploymentPlanningManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) No suitable hosts found
2014-03-13 16:15:31,824 DEBUG [cloud.deploy.DeploymentPlanningManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) No suitable hosts found under this Cluster: 5
2014-03-13 16:15:31,830 DEBUG [cloud.deploy.DeploymentPlanningManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Could not find suitable Deployment Destination for this VM under any clusters, returning. 
2014-03-13 16:15:31,830 DEBUG [cloud.deploy.FirstFitPlanner] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Searching resources only under specified Pod: 2
2014-03-13 16:15:31,831 DEBUG [cloud.deploy.FirstFitPlanner] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Listing clusters in order of aggregate capacity, that have (atleast one host with) enough CPU and RAM capacity under this Pod: 2
2014-03-13 16:15:31,837 DEBUG [cloud.deploy.FirstFitPlanner] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Removing from the clusterId list these clusters from avoid set: [5]
2014-03-13 16:15:31,840 DEBUG [cloud.deploy.FirstFitPlanner] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) No clusters found after removing disabled clusters and clusters in avoid list, returning.
2014-03-13 16:15:31,868 DEBUG [cloud.capacity.CapacityManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) VM state transitted from :Starting to Stopped with event: OperationFailedvm's original host id: null new host id: null host id before state transition: 14
2014-03-13 16:15:31,881 DEBUG [cloud.capacity.CapacityManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Hosts's actual total CPU: 64000 and CPU after applying overprovisioning: 1280000
2014-03-13 16:15:31,881 DEBUG [cloud.capacity.CapacityManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Hosts's actual total RAM: 549746139136 and RAM after applying overprovisioning: 10994923339776
2014-03-13 16:15:31,881 DEBUG [cloud.capacity.CapacityManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) release cpu from host: 14, old used: 28500,reserved: 0, actual total: 64000, total with overprovisioning: 1280000; new used: 16500,reserved:0; movedfromreserved: false,moveToReserveredfalse
2014-03-13 16:15:31,881 DEBUG [cloud.capacity.CapacityManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) release mem from host: 14, old used: 137707388928,reserved: 0, total: 10994923339776; new used: 68987912192,reserved:0; movedfromreserved: false,moveToReserveredfalse
2014-03-13 16:15:31,923 DEBUG [cloud.vm.UserVmManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Destroying vm VM[User|DSJAppServer01] as it failed to create on Host with Id:15
2014-03-13 16:15:31,954 DEBUG [cloud.capacity.CapacityManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) VM state transitted from :Stopped to Error with event: OperationFailedToErrorvm's original host id: null new host id: null host id before state transition: null
2014-03-13 16:15:32,033 WARN  [apache.CloudStack.alerts] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ])  alertType:: 8 // dataCenterId:: 2 // podId:: 2 // clusterId:: null // message:: Failed to deploy Vm with Id: 301, on Host with Id: 15
2014-03-13 16:15:32,136 INFO  [user.vm.DeployVMCmd] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) com.cloud.exception.InsufficientServerCapacityException: Unable to create a deployment for VM[User|DSJAppServer01]Scope=interface com.cloud.dc.DataCenter; id=2
2014-03-13 16:15:32,136 INFO  [user.vm.DeployVMCmd] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Unable to create a deployment for VM[User|DSJAppServer01]
com.cloud.exception.InsufficientServerCapacityException: Unable to create a deployment for VM[User|DSJAppServer01]Scope=interface com.cloud.dc.DataCenter; id=2
	at com.cloud.vm.VirtualMachineManagerImpl.advanceStart(VirtualMachineManagerImpl.java:842)
	at com.cloud.vm.VirtualMachineManagerImpl.start(VirtualMachineManagerImpl.java:578)
	at org.apache.CloudStack.engine.cloud.entity.api.VMEntityManagerImpl.deployVirtualMachine(VMEntityManagerImpl.java:237)
	at org.apache.CloudStack.engine.cloud.entity.api.VirtualMachineEntityImpl.deploy(VirtualMachineEntityImpl.java:209)
	at com.cloud.vm.UserVmManagerImpl.startVirtualMachine(UserVmManagerImpl.java:3406)
	at com.cloud.vm.UserVmManagerImpl.startVirtualMachine(UserVmManagerImpl.java:2966)
	at com.cloud.vm.UserVmManagerImpl.startVirtualMachine(UserVmManagerImpl.java:2952)
	at com.cloud.utils.component.ComponentInstantiationPostProcessor$InterceptorDispatcher.intercept(ComponentInstantiationPostProcessor.java:125)
	at org.apache.CloudStack.api.command.user.vm.DeployVMCmd.execute(DeployVMCmd.java:420)
	at com.cloud.api.ApiDispatcher.dispatch(ApiDispatcher.java:158)
	at com.cloud.async.AsyncJobManagerImpl$1.run(AsyncJobManagerImpl.java:531)
	at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
	at java.util.concurrent.FutureTask$Sync.innerRun(FutureTask.java:334)
	at java.util.concurrent.FutureTask.run(FutureTask.java:166)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1146)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
	at java.lang.Thread.run(Thread.java:679)
2014-03-13 16:15:32,140 DEBUG [cloud.async.AsyncJobManagerImpl] (Job-Executor-8:job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ]) Complete async job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ], jobStatus: 2, resultCode: 530, result: Error Code: 533 Error text: Unable to create a deployment for VM[User|DSJAppServer01]
2014-03-13 16:15:32,424 DEBUG [cloud.api.ApiServlet] (catalina-exec-6:null) ===START===  172.30.22.1 -- GET  sessionkey=2x5T5NMxyGWnBzgLBdgMnJ8dsrU%3D&response=json&listall=True&keyword=dsj&command=listVirtualMachines
2014-03-13 16:15:32,467 DEBUG [cloud.api.ApiServlet] (catalina-exec-6:null) ===END===  172.30.22.1 -- GET  sessionkey=2x5T5NMxyGWnBzgLBdgMnJ8dsrU%3D&response=json&listall=True&keyword=dsj&command=listVirtualMachines
2014-03-13 16:15:33,252 DEBUG [cloud.api.ApiServlet] (catalina-exec-1:null) ===START===  172.30.22.1 -- GET  sessionkey=2x5T5NMxyGWnBzgLBdgMnJ8dsrU%3D&command=queryAsyncJobResult&response=json&jobid=5fa4d6ef-5596-4043-98ac-5a4defed54ee
2014-03-13 16:15:33,278 DEBUG [cloud.async.AsyncJobManagerImpl] (catalina-exec-1:null) Async job-1131 = [ 5fa4d6ef-5596-4043-98ac-5a4defed54ee ] completed
2014-03-13 16:15:33,286 DEBUG [cloud.api.ApiServlet] (catalina-exec-1:null) ===END===  172.30.22.1 -- GET  sessionkey=2x5T5NMxyGWnBzgLBdgMnJ8dsrU%3D&command=queryAsyncJobResult&response=json&jobid=5fa4d6ef-5596-4043-98ac-5a4defed54ee
2014-03-13 16:15:39,618 DEBUG [agent.manager.AgentManagerImpl] (AgentManager-Handler-1:null) SeqA 8-147828: Processing Seq 8-147828:  { Cmd , MgmtId: -1, via: 8, Ver: v1, Flags: 11, [{"com.cloud.agent.api.ConsoleProxyLoadReportCommand":{"_proxyVmId":85,"_loadInfo":"{\n  \"connections\": []\n}","wait":0}}] }
2014-03-13 16:15:39,631 DEBUG [agent.manager.AgentManagerImpl] (AgentManager-Handler-1:null) SeqA 8-147828: Sending Seq 8-147828:  { Ans: , MgmtId: 345050394595, via: 8, Ver: v1, Flags: 100010, [{"com.cloud.agent.api.AgentControlAnswer":{"result":true,"wait":0}}] }
2014-03-13 16:15:41,807 DEBUG [cloud.server.StatsCollector] (StatsCollector-1:null) StorageCollector is running...
2014-03-13 16:15:41,877 DEBUG [agent.transport.Request] (StatsCollector-1:null) Seq 13-1827806745: Received:  { Ans: , MgmtId: 345050394595, via: 13, Ver: v1, Flags: 10, { GetStorageStatsAnswer } }
2014-03-13 16:15:41,884 INFO  [agent.manager.AgentAttache] (StatsCollector-1:null) Seq 18-1940796687: Unable to send due to Resource [Host:18] is unreachable: Host 18: Channel is closed
2014-03-13 16:15:41,884 DEBUG [agent.manager.AgentAttache] (StatsCollector-1:null) Seq 18-1940796687: Cancelling.
2014-03-13 16:15:41,884 DEBUG [CloudStack.storage.RemoteHostEndPoint] (StatsCollector-1:null) Failed to send command, due to Agent:18, com.cloud.exception.AgentUnavailableException: Resource [Host:18] is unreachable: Host 18: Channel is closed
2014-03-13 16:15:41,884 ERROR [cloud.server.StatsCollector] (StatsCollector-1:null) Error trying to retrieve storage stats
com.cloud.utils.exception.CloudRuntimeException: Failed to send command, due to Agent:18, com.cloud.exception.AgentUnavailableException: Resource [Host:18] is unreachable: Host 18: Channel is closed
	at org.apache.CloudStack.storage.RemoteHostEndPoint.sendMessage(RemoteHostEndPoint.java:104)
	at com.cloud.server.StatsCollector$StorageCollector.run(StatsCollector.java:545)
	at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
	at java.util.concurrent.FutureTask$Sync.innerRunAndReset(FutureTask.java:351)
	at java.util.concurrent.FutureTask.runAndReset(FutureTask.java:178)
	at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.access$201(ScheduledThreadPoolExecutor.java:165)
	at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:267)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1146)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
	at java.lang.Thread.run(Thread.java:679)
```
### 解决方法
```python
1.根据log输出判断是存储的问题,由于secondary storage在远程data vcent,log中抛出,有 lock,我们可以销毁secondary来解决这个问题.
2.销毁secondary后发现所有的模板没有了,那是因为我们的serconary在远程data vcent,我们的网络中划分了vlan,当销毁第2个secondary的时候,system重建secondary vm后不会刷新network vlan,这时我们需要手动输入想对应的vlan即可解决问题.
```
