**Here is the structured command table**:

| **Action Description**             | **Command** |
|-----------------------------------|-------------|
| List all Auto Scaling Groups      | `aws autoscaling describe-auto-scaling-groups --output table --color off` |
| Filter ASGs by name pattern       | `aws autoscaling describe-auto-scaling-groups --query "AutoScalingGroups[?contains(AutoScalingGroupName, 'staging') || contains(AutoScalingGroupName, 'upgrade')].AutoScalingGroupName" --output text` |
| Inspect the ASGs’ current sizes   | `aws autoscaling describe-auto-scaling-groups --auto-scaling-group-names eks-cluster-staging-nodes eks-cluster-upgrade-temp --query "AutoScalingGroups[].{Name:AutoScalingGroupName, Min:MinSize, Max:MaxSize, Desired:DesiredCapacity}" --output table --color off` |
| Check instance protection status  | `aws autoscaling describe-auto-scaling-groups --auto-scaling-group-names eks-cluster-staging-nodes eks-cluster-upgrade-temp --query "AutoScalingGroups[].Instances[].{ID:InstanceId, Protected:ProtectedFromScaleIn}" --output table --color off` |
| Disable instance protection       | `aws autoscaling set-instance-protection --auto-scaling-group-name eks-cluster-upgrade-temp --instance-ids i-0fedcba987654 --no-protected-from-scale-in` |
| Check default cooldown            | `aws autoscaling describe-auto-scaling-groups --auto-scaling-group-names eks-cluster-staging-nodes --query "AutoScalingGroups[].DefaultCooldown" --output text` |
| Script to scale down matching ASGs| `for asg in $(aws autoscaling describe-auto-scaling-groups --query "AutoScalingGroups[?contains(AutoScalingGroupName,'upgrade')].AutoScalingGroupName" --output text); do aws autoscaling update-auto-scaling-group --auto-scaling-group-name "$asg" --min-size 0 --desired-capacity 0 --max-size 3; done` |
| Confirm scaled-down ASGs          | `aws autoscaling describe-auto-scaling-groups --auto-scaling-group-names eks-cluster-staging-nodes eks-cluster-upgrade-temp --query "AutoScalingGroups[].{Name:AutoScalingGroupName, Desired:DesiredCapacity, Instances:length(Instances)}" --output table --color off` |
