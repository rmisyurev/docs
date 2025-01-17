Security groups follow the principle "All traffic that is not allowed is prohibited". Therefore, security group rules for a cluster's cloud network might prevent connections to the cluster if one or more groups are assigned to it.

Let's say that a VM in {{ yandex-cloud }} is used to access the cluster. In this case, if only the 10.133.0.0/24 subnet is specified in the incoming traffic rules for the security group, but the VM is in the 10.128.0.0/16 subnet, the VM won't be able to connect to the cluster. A VM won't be also able to connect from the 10.133.0.0/24 subnet if it tries to access a port not specified in the security group rules.

