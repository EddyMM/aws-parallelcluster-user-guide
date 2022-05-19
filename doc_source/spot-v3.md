# Working with Spot Instances<a name="spot-v3"></a>

AWS ParallelCluster uses Spot Instances if you have set `SlurmQueues` / `CapacityType` or `AwsBatchQueues` / `CapacityType` to `SPOT` in the cluster configuration file\. Spot Instances are more cost effective than On\-Demand Instances, but they might be interrupted\. It might help to take advantage of *Spot Instance interruption notices*, which provide a two\-minute warning before Amazon EC2 must stop or terminate your Spot Instance\. For more information, see [Spot Instance interruptions](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-interruptions.html) in *Amazon EC2 User Guide for Linux Instances*\. To learn how `AwsBatchQueues` works with Spot Instances, see [Compute Resources](https://docs.aws.amazon.com/batch/latest/userguide/compute_environment_parameters.html#compute_environment_compute_resources) in the *AWS Batch User Guide*\. The following sections describe three scenarios in which Spot Instances can be interrupted when using `SlurmQueues`\.

## Scenario 1: Spot Instance with no running jobs is interrupted<a name="no-jobs-v3"></a>

When this interruption occurs, AWS ParallelCluster tries to replace the instance if the scheduler queue has pending jobs that require additional instances, or if the number of active instances is lower than the `SlurmQueues` / `ComputeResources` / `MinCount`\. If AWS ParallelCluster can't provision new instances, then a request for new instances is periodically repeated\.

## Scenario 2: Spot Instance running single node jobs is interrupted<a name="single-node-v3"></a>

The job fails with a state code of `NODE_FAIL`, and the job is requeued \(unless `--no-requeue` is specified when the job is submitted\)\. If the node is a static node, it's replaced\. If the node is a dynamic node, the node is terminated and reset\. For more information about `sbatch`, including the `--no-requeue` parameter, see [https://slurm.schedmd.com/sbatch.html](https://slurm.schedmd.com/sbatch.html) in the *Slurm documentation*\.

## Scenario 3: Spot Instance running multi\-node jobs is interrupted<a name="multi-node-v3"></a>

The job fails with a state code of `NODE_FAIL`, and the job is requeued \(unless `--no-requeue` was specified when the job was submitted\)\. If the node is a static node, it's replaced\. If the node is a dynamic node, the node is terminated and reset\. Other nodes that were running the terminated jobs might be allocated to other pending jobs, or scaled down after the configured `SlurmSettings` / `ScaledownIdletime` time has passed\.

For more information about Spot Instances, see [Spot Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\.