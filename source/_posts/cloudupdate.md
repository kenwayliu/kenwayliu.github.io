title: Cloudstack 4.2.0 update 4.2.1 故障报告
date: 2014-05-04 22:44:33
categories: CloudStack
comments: true
tags:
        - IAAS
---

为了解决4.2.0的bug，现在需要升级到4.2.1
我当前的环境是两个NODE，为了安全期间，先从一台NODE下手,升级正常后在升级另一个NODE
升级步骤：
1. 备份数据库
2. 停止NODE2的 CloudStack-managerment、CloudStack-usage服务
3. 配置4.2.1的yum，通过yum升级4.2.0到4.2.1
4. 启动CloudStack-management、CloudStack-usage服务
5. 由于我们当前用的是vmware hypervisor，用的是高级网络，当启动管理端的时候有的端口组会漏刷，这时需要人工检查相关的VLAN-ID.
6. 等带数分钟后，通过log查看managerment是否启动，正常启动后，登录WEB-UI登录验证版本信息.
7. 创建Test vm测试服务可用性
<!--more-->
	- 问题来了发现无法创建虚机,查看log抛出下面的异常。
    ```
    2014-05-04 20:09:32,830 DEBUG [cloud.storage.StorageManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Checking pool 24 for storage, totalSize: 1998770405376, usedBytes: 96927219712, usedPct: 0.048493423482406664, disable threshold: 0.85
2014-05-04 20:09:32,854 DEBUG [cloud.storage.StorageManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Checking pool: 24 for volume allocation [Vol[791|vm=469|ROOT]], maxSize : 7995081621504, totalAllocatedSize : 2269890215936, askingSize : 21474836480, allocated disable threshold: 0.85
2014-05-04 20:09:32,857 DEBUG [storage.allocator.AbstractStoragePoolAllocator] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Checking if storage pool is suitable, name: null ,poolId: 25
2014-05-04 20:09:32,867 DEBUG [cloud.storage.StorageManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Checking pool 25 for storage, totalSize: 1998770405376, usedBytes: 160569491456, usedPct: 0.08033413493822186, disable threshold: 0.85
2014-05-04 20:09:32,892 DEBUG [cloud.storage.StorageManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Checking pool: 25 for volume allocation [Vol[791|vm=469|ROOT]], maxSize : 7995081621504, totalAllocatedSize : 1245540515840, askingSize : 21474836480, allocated disable threshold: 0.85
2014-05-04 20:09:32,894 DEBUG [storage.allocator.AbstractStoragePoolAllocator] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Checking if storage pool is suitable, name: null ,poolId: 26
2014-05-04 20:09:32,904 DEBUG [cloud.storage.StorageManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Checking pool 26 for storage, totalSize: 2004407549952, usedBytes: 88187338752, usedPct: 0.04399671052631579, disable threshold: 0.85
2014-05-04 20:09:32,928 DEBUG [cloud.storage.StorageManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Checking pool: 26 for volume allocation [Vol[791|vm=469|ROOT]], maxSize : 8017630199808, totalAllocatedSize : 4563402752000, askingSize : 21474836480, allocated disable threshold: 0.85
2014-05-04 20:09:32,928 DEBUG [storage.allocator.ClusterScopeStoragePoolAllocator] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) ClusterScopeStoragePoolAllocator returning 7 suitable storage pools
2014-05-04 20:09:32,928 DEBUG [cloud.deploy.DeploymentPlanningManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Trying to find a potenial host and associated storage pools from the suitable host/pool lists for this VM
2014-05-04 20:09:32,928 DEBUG [cloud.deploy.DeploymentPlanningManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Checking if host: 12 can access any suitable storage pool for volume: ROOT
2014-05-04 20:09:32,931 DEBUG [cloud.deploy.DeploymentPlanningManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Host: 12 can access pool: 25
2014-05-04 20:09:32,934 DEBUG [cloud.deploy.DeploymentPlanningManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Found a potential host id: 12 name: 172.30.22.100 and associated storage pools for this VM
2014-05-04 20:09:32,936 DEBUG [cloud.deploy.DeploymentPlanningManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Returning Deployment Destination: Dest[Zone(Id)-Pod(Id)-Cluster(Id)-Host(Id)-Storage(Volume(Id|Type-->Pool(Id))] : Dest[Zone(1)-Pod(1)-Cluster(3)-Host(12)-Storage(Volume(791|ROOT-->Pool(25))]
2014-05-04 20:09:32,936 DEBUG [cloud.vm.VirtualMachineManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Deployment found  - P0=VM[User|Qiao_ICPUpdate_Test05], P0=Dest[Zone(Id)-Pod(Id)-Cluster(Id)-Host(Id)-Storage(Volume(Id|Type-->Pool(Id))] : Dest[Zone(1)-Pod(1)-Cluster(3)-Host(12)-Storage(Volume(791|ROOT-->Pool(25))]
2014-05-04 20:09:33,067 DEBUG [cloud.capacity.CapacityManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) VM state transitted from :Starting to Starting with event: OperationRetryvm's original host id: null new host id: 12 host id before state transition: null
2014-05-04 20:09:33,088 DEBUG [cloud.capacity.CapacityManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Hosts's actual total CPU: 128000 and CPU after applying overprovisioning: 2560000
2014-05-04 20:09:33,089 DEBUG [cloud.capacity.CapacityManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) We are allocating VM, increasing the used capacity of this host:12
2014-05-04 20:09:33,089 DEBUG [cloud.capacity.CapacityManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Current Used CPU: 216000 , Free CPU:2344000 ,Requested CPU: 4000
2014-05-04 20:09:33,089 DEBUG [cloud.capacity.CapacityManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Current Used RAM: 345744867328 , Free RAM:10649178472448 ,Requested RAM: 8589934592
2014-05-04 20:09:33,089 DEBUG [cloud.capacity.CapacityManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) CPU STATS after allocation: for host: 12, old used: 216000, old reserved: 0, actual total: 128000, total with overprovisioning: 2560000; new used:220000, reserved:0; requested cpu:4000,alloc_from_last:false
2014-05-04 20:09:33,089 DEBUG [cloud.capacity.CapacityManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) RAM STATS after allocation: for host: 12, old used: 345744867328, old reserved: 0, total: 10994923339776; new used: 354334801920, reserved: 0; requested mem: 8589934592,alloc_from_last:false
2014-05-04 20:09:33,102 DEBUG [cloud.vm.VirtualMachineManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) VM is being created in podId: 1
2014-05-04 20:09:33,121 DEBUG [cloud.network.NetworkManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Network id=213 is already implemented
2014-05-04 20:09:33,141 DEBUG [cloud.network.NetworkModelImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Service SecurityGroup is not supported in the network id=213
2014-05-04 20:09:33,148 DEBUG [cloud.network.NetworkManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Changing active number of nics for network id=213 on 1
2014-05-04 20:09:33,166 DEBUG [cloud.network.NetworkManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Asking VirtualRouter to prepare for Nic[590-469-null-172.30.51.96]
2014-05-04 20:09:33,199 DEBUG [network.router.VirtualNetworkApplianceManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Lock is acquired for network id 213 as a part of router startup in Dest[Zone(Id)-Pod(Id)-Cluster(Id)-Host(Id)-Storage(Volume(Id|Type-->Pool(Id))] : Dest[Zone(1)-Pod(1)-Cluster(3)-Host(12)-Storage(Volume(791|ROOT-->Pool(25))]
2014-05-04 20:09:33,229 DEBUG [network.router.VirtualNetworkApplianceManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Lock is released for network id 213 as a part of router startup in Dest[Zone(Id)-Pod(Id)-Cluster(Id)-Host(Id)-Storage(Volume(Id|Type-->Pool(Id))] : Dest[Zone(1)-Pod(1)-Cluster(3)-Host(12)-Storage(Volume(791|ROOT-->Pool(25))]
2014-05-04 20:09:33,246 DEBUG [cloud.network.NetworkModelImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Service SecurityGroup is not supported in the network id=213
2014-05-04 20:09:33,272 DEBUG [cloud.network.NetworkModelImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Service SecurityGroup is not supported in the network id=213
2014-05-04 20:09:33,278 DEBUG [network.router.VirtualNetworkApplianceManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Applying dhcp entry in network Ntwk[213|Guest|7]
2014-05-04 20:09:33,317 DEBUG [agent.manager.ClusteredAgentAttache] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Seq 9-1236337169: Forwarding Seq 9-1236337169:  { Cmd , MgmtId: 345050394595, via: 9, Ver: v1, Flags: 100111, [{"com.cloud.agent.api.routing.DhcpEntryCommand":{"vmMac":"06:b2:d2:00:02:19","vmIpAddress":"172.30.51.96","vmName":"Qiao_ICPUpdate_Test05","defaultRouter":"172.30.51.254","defaultDns":"172.30.51.21","duid":"00:03:00:01:06:b2:d2:00:02:19","isDefault":true,"executeInSequence":true,"accessDetails":{"router.guest.ip":"172.30.51.21","zone.network.type":"Advanced","router.name":"r-163-VM","router.ip":"172.30.22.54"},"wait":0}}] } to 345050394596
2014-05-04 20:09:33,648 DEBUG [agent.transport.Request] (AgentManager-Handler-6:null) Seq 9-1236337169: Processing:  { Ans: , MgmtId: 345050394595, via: 9, Ver: v1, Flags: 110, [{"com.cloud.agent.api.Answer":{"result":true,"wait":0}}] }
2014-05-04 20:09:33,649 DEBUG [agent.manager.AgentAttache] (AgentManager-Handler-6:null) Seq 9-1236337169: No more commands found
2014-05-04 20:09:33,649 DEBUG [agent.transport.Request] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Seq 9-1236337169: Received:  { Ans: , MgmtId: 345050394595, via: 9, Ver: v1, Flags: 110, { Answer } }
2014-05-04 20:09:33,667 DEBUG [cloud.network.NetworkModelImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Service SecurityGroup is not supported in the network id=213
2014-05-04 20:09:33,672 DEBUG [network.router.VirtualNetworkApplianceManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Applying userdata and password entry in network Ntwk[213|Guest|7]
2014-05-04 20:09:33,709 DEBUG [agent.manager.ClusteredAgentAttache] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Seq 9-1236337170: Forwarding Seq 9-1236337170:  { Cmd , MgmtId: 345050394595, via: 9, Ver: v1, Flags: 100111, [{"com.cloud.agent.api.routing.SavePasswordCommand":{"password":"fnirq_cnffjbeq","vmIpAddress":"172.30.51.96","vmName":"Qiao_ICPUpdate_Test05","executeInSequence":true,"accessDetails":{"router.guest.ip":"172.30.51.21","zone.network.type":"Advanced","router.ip":"172.30.22.54","router.name":"r-163-VM"},"wait":0}},{"com.cloud.agent.api.routing.VmDataCommand":{"vmIpAddress":"172.30.51.96","vmName":"Qiao_ICPUpdate_Test05","executeInSequence":true,"accessDetails":{"router.guest.ip":"172.30.51.21","zone.network.type":"Advanced","router.ip":"172.30.22.54","router.name":"r-163-VM"},"wait":0}}] } to 345050394596
2014-05-04 20:09:34,044 DEBUG [agent.transport.Request] (AgentManager-Handler-2:null) Seq 9-1236337170: Processing:  { Ans: , MgmtId: 345050394595, via: 9, Ver: v1, Flags: 110, [{"com.cloud.agent.api.Answer":{"result":false,"details":"SavePassword failed due to bash: /opt/cloud/bin/savepassword.sh: No such file or directory\n","wait":0}}] }
2014-05-04 20:09:34,044 DEBUG [agent.manager.AgentAttache] (AgentManager-Handler-2:null) Seq 9-1236337170: No more commands found
2014-05-04 20:09:34,044 DEBUG [agent.transport.Request] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Seq 9-1236337170: Received:  { Ans: , MgmtId: 345050394595, via: 9, Ver: v1, Flags: 110, { Answer } }
2014-05-04 20:09:34,045 INFO  [cloud.vm.VirtualMachineManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Unable to contact resource.
com.cloud.exception.ResourceUnavailableException: Resource [DataCenter:1] is unreachable: Unable to apply userdata and password entry on router 
	at com.cloud.network.router.VirtualNetworkApplianceManagerImpl.applyRules(VirtualNetworkApplianceManagerImpl.java:3828)
	at com.cloud.network.router.VirtualNetworkApplianceManagerImpl.applyUserData(VirtualNetworkApplianceManagerImpl.java:3017)
	at com.cloud.network.element.VirtualRouterElement.addPasswordAndUserdata(VirtualRouterElement.java:930)
	at com.cloud.network.NetworkManagerImpl.prepareElement(NetworkManagerImpl.java:2085)
	at com.cloud.network.NetworkManagerImpl.prepareNic(NetworkManagerImpl.java:2200)
	at com.cloud.network.NetworkManagerImpl.prepare(NetworkManagerImpl.java:2136)
	at com.cloud.vm.VirtualMachineManagerImpl.advanceStart(VirtualMachineManagerImpl.java:886)
	at com.cloud.vm.VirtualMachineManagerImpl.start(VirtualMachineManagerImpl.java:577)
	at org.apache.CloudStack.engine.cloud.entity.api.VMEntityManagerImpl.deployVirtualMachine(VMEntityManagerImpl.java:227)
	at org.apache.CloudStack.engine.cloud.entity.api.VirtualMachineEntityImpl.deploy(VirtualMachineEntityImpl.java:209)
	at com.cloud.vm.UserVmManagerImpl.startVirtualMachine(UserVmManagerImpl.java:3440)
	at com.cloud.vm.UserVmManagerImpl.startVirtualMachine(UserVmManagerImpl.java:3000)
	at com.cloud.vm.UserVmManagerImpl.startVirtualMachine(UserVmManagerImpl.java:2986)
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
2014-05-04 20:09:34,074 DEBUG [cloud.vm.VirtualMachineManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Cleaning up resources for the vm VM[User|Qiao_ICPUpdate_Test05] in Starting state
2014-05-04 20:09:34,080 DEBUG [agent.manager.ClusteredAgentAttache] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Seq 12-571212319: Forwarding Seq 12-571212319:  { Cmd , MgmtId: 345050394595, via: 12, Ver: v1, Flags: 100111, [{"com.cloud.agent.api.StopCommand":{"isProxy":false,"executeInSequence":true,"vmName":"i-2-469-VM","wait":0}}] } to 345050394596
2014-05-04 20:09:34,129 DEBUG [agent.transport.Request] (AgentManager-Handler-4:null) Seq 12-571212319: Processing:  { Ans: , MgmtId: 345050394595, via: 12, Ver: v1, Flags: 110, [{"com.cloud.agent.api.StopAnswer":{"result":true,"details":"VM i-2-469-VM is no longer in vSphere","wait":0}}] }
2014-05-04 20:09:34,130 DEBUG [agent.manager.AgentAttache] (AgentManager-Handler-4:null) Seq 12-571212319: No more commands found
2014-05-04 20:09:34,130 DEBUG [agent.transport.Request] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Seq 12-571212319: Received:  { Ans: , MgmtId: 345050394595, via: 12, Ver: v1, Flags: 110, { StopAnswer } }
2014-05-04 20:09:34,147 DEBUG [cloud.network.NetworkManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Changing active number of nics for network id=213 on -1
2014-05-04 20:09:34,158 DEBUG [cloud.vm.VirtualMachineManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Successfully released network resources for the vm VM[User|Qiao_ICPUpdate_Test05]
2014-05-04 20:09:34,158 DEBUG [cloud.vm.VirtualMachineManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Successfully cleanued up resources for the vm VM[User|Qiao_ICPUpdate_Test05] in Starting state
2014-05-04 20:09:34,180 DEBUG [cloud.deploy.DeploymentPlanningManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Deploy avoids pods: [], clusters: [], hosts: [12]
2014-05-04 20:09:34,180 DEBUG [cloud.deploy.DeploymentPlanningManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) DataCenter id = '1' provided is in avoid set, DeploymentPlanner cannot allocate the VM, returning.
2014-05-04 20:09:34,228 DEBUG [cloud.capacity.CapacityManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) VM state transitted from :Starting to Stopped with event: OperationFailedvm's original host id: null new host id: null host id before state transition: 12
2014-05-04 20:09:34,242 DEBUG [cloud.capacity.CapacityManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Hosts's actual total CPU: 128000 and CPU after applying overprovisioning: 2560000
2014-05-04 20:09:34,242 DEBUG [cloud.capacity.CapacityManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Hosts's actual total RAM: 549746139136 and RAM after applying overprovisioning: 10994923339776
2014-05-04 20:09:34,242 DEBUG [cloud.capacity.CapacityManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) release cpu from host: 12, old used: 220000,reserved: 0, actual total: 128000, total with overprovisioning: 2560000; new used: 216000,reserved:0; movedfromreserved: false,moveToReserveredfalse
2014-05-04 20:09:34,242 DEBUG [cloud.capacity.CapacityManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) release mem from host: 12, old used: 354334801920,reserved: 0, total: 10994923339776; new used: 345744867328,reserved:0; movedfromreserved: false,moveToReserveredfalse
2014-05-04 20:09:34,303 DEBUG [cloud.capacity.CapacityManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) VM state transitted from :Stopped to Starting with event: StartRequestedvm's original host id: null new host id: null host id before state transition: null
2014-05-04 20:09:34,303 DEBUG [cloud.vm.VirtualMachineManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Successfully transitioned to start state for VM[User|Qiao_ICPUpdate_Test05] reservation id = 4dfbe3d9-9929-4d1b-9fcf-dbcf5aedadee
2014-05-04 20:09:34,315 DEBUG [cloud.vm.VirtualMachineManagerImpl] (Job-Executor-7:job-1915 = [ bf596afe-55ed-45ce-b6dd-8da5ff1d2c78 ]) Trying to deploy VM, vm has dcId: 1 and podId: 1
```
根据log的输出,问题出在

	- 2014-05-04 20:09:34,044 DEBUG [agent.transport.Request] (AgentManager-Handler-2:null) Seq 9-1236337170: Processing:  { Ans: , MgmtId: 345050394595, via: 9, Ver: v1, Flags: 110, [{"com.cloud.agent.api.Answer":{"result":false,"details":`"SavePassword failed due to bash: /opt/cloud/bin/savepassword.sh: No such file or directory\n","wait":0}}] }`
这时我们找到相关的虚拟路由器，登录到路由器中，查看/opt/cloud/bin目录下面是不是有此文件，发现没有，这是我们重启虚拟路由器（由于这个问题我也是刚碰到，所以我们需要找不是很重要的虚拟路由进行重启，重启的过程可能会重刷主机端口，这时要注意观察，等待虚拟路由重启成功后查看/opt/cloud/bin目录中是否有savepassword.sh这个文件，庆幸的是有这个文件，然后我们重建新部署虚拟机，部署成功）


