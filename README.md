# LINBIT Ansible NFS Cluster

Build an HA NFS Cluster using Ansible with packages from LINBIT.

System requirements:

  - An account at https://my.linbit.com (contact sales@linbit.com).
  - Deployment environment must have Ansible `2.7.0+` and `python-netaddr`.
  - All target systems must have passwordless SSH access.
  - All hostnames used in inventory file are resolvable (better to use IP addresses).
  - Target systems are RHEL 7, 8 or 9 (or RHEL compatible distros).

# Usage

Edit `hosts.ini` for your environment and target nodes. 

Explanation of the variables:

  - `cluster_vip` is the virtual IP your clients will use to connect to the cluster.
  - `cluster_vip_cidr` is the cidr netmask for your virtual IP belongs in.
  - `drbd_backing_disk` is a raw block device or LVM that DRBD will use as backing storage.
  - `drbd_replication_network` is the subnet your DRBD replication interface exists in.

For example:
```
[nodes]
192.168.7.190
192.168.7.191
192.168.7.192

[nodes:vars]
cluster_vip="192.168.7.195"
cluster_vip_cidr="24"
drbd_backing_disk="/dev/vdb"
drbd_replication_network="172.16.7.0/24"

# ansible_user needs sudo/root access
ansible_user="root"
```

If you do not want to be prompted for your https://my.linbit.com credentials, pass `--extra-vars` to `ansible-playbook` at execution time:
```
$ ansible-playbook -e lb_user="username" -e lb_pass="password" -e lb_con_id="1234" -e lb_clu_id="1234" site.yaml
```

You will be prompted for any variables not set with `--extra-vars`.

# Notes

You can add and remove nodes to the cluster by adding/removing nodes in the inventory file and re-rerunning the playbook. You will lose any data created on your cluster when re-running the playbook (see `wipefs` task in `site.yaml`). When "scaling down" (removing nodes) by re-running the playbook, be sure to stop Pacemaker/Corosync/DRBD on the node(s) you're removing.

# Feedback

Please pass any issues or questions you may have regarding this playbook to your LINBIT account manager or sales@linbit.com.
