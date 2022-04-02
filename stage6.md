Stage 6 - move to an Aurora Cluster in 3 availability zones

Snapshot the RDS instance, and then migrate the snapshot to an Aurora cluster.

To improve the performance of the Aurora cluster, you can create additonal replicas for reads. 

At the end of this stage, we need to recreate the parameter for the DBEndpoint. 
