# ansible-percona-xtradb-cluster
Ansible routines to deploy Percona XtraDB Cluster on CentOS / Red Hat Linux distros.

In this file, I will present and demonstrate how to install PXC Galera Cluster in an automated and easy way.

For this, I will be using the scenario described down below:
```
1 Linux server for Ansible
3 Linux servers for PXC (the one that we will install PXC using Ansible)
```

First of all, we have to prepare our Linux environment to use Ansible

Please have a look below how to install Ansible on CentOS/Red Hat:
```
yum install ansible -y
```
Well now that we have Ansible installed already, we need to install git to clone our git repository on the Linux server, see below how to install it on CentOS/Red Hat:
```
yum install git -y
```

Copying the script packages using git:
```
cd /root
git clone https://github.com/emersongaudencio/ansible-percona-xtradb-cluster.git
```
Alright then after we have installed Ansible and git and clone the git repository. We have to generate ssh heys to share between the Ansible control machine and the database machines. Let see how to do that down below.

To generate the keys, keep in mind that is mandatory to generate the keys inside of the directory who was copied from the git repository, see instructions below:
```
cd /root/ansible-percona-xtradb-cluster/ansible
ssh-keygen -f ansible
```
After that you have had generated the keys to copy the keys to the database machines, see instructions below:
```
ssh-copy-id -i ansible.pub 10.70.2.248
```

Please edit the file called hosts inside of the ansible git directory :
```
vi hosts
```
Please add the hosts that you want to install your database and save the hosts file, see an example below:

```
# This is the default ansible 'hosts' file.
#

[pxc56galeracluster]
dbnode01 ansible_ssh_host=10.70.2.248
dbnode02 ansible_ssh_host=10.70.2.201
dbnode03 ansible_ssh_host=10.70.2.170
```

For testing if it is all working properly, run the command below :
```
ansible -m ping dbnode01 -v
ansible -m ping dbnode01 -v
ansible -m ping dbnode01 -v
```

Alright finally we can install our pxc56, pxc57, pxc80 using Ansible as we planned to, run the command below:
```

sh run_xtradb_galera_install.sh dbnode01 56 56 "10.70.2.248" "pxc56" "10.70.2.248,10.70.2.201,10.70.2.170"
sh run_xtradb_galera_install.sh dbnode02 56 56 "10.70.2.248" "pxc56" "10.70.2.248,10.70.2.201,10.70.2.170"
sh run_xtradb_galera_install.sh dbnode03 56 56 "10.70.2.248" "pxc56" "10.70.2.248,10.70.2.201,10.70.2.170"


sh run_xtradb_galera_install.sh dbnode01 57 57 "10.116.0.4" "pxc57" "10.116.0.4,10.116.0.2,10.116.0.3"
sh run_xtradb_galera_install.sh dbnode02 57 57 "10.116.0.4" "pxc57" "10.116.0.4,10.116.0.2,10.116.0.3"
sh run_xtradb_galera_install.sh dbnode03 57 57 "10.116.0.4" "pxc57" "10.116.0.4,10.116.0.2,10.116.0.3"


sh run_xtradb_galera_install.sh dbnode01 80 80 "10.110.0.4" "pxc80" "10.110.0.4,10.110.0.2,10.110.0.3"
sh run_xtradb_galera_install.sh dbnode02 80 80 "10.110.0.4" "pxc80" "10.110.0.4,10.110.0.2,10.110.0.3"
sh run_xtradb_galera_install.sh dbnode03 80 80 "10.110.0.4" "pxc80" "10.110.0.4,10.110.0.2,10.110.0.3"


```

### Parameters specification:
#### run_xtradb_galera_install.sh
Parameter    | Value           | Mandatory   | Order        | Accepted values
------------ | ------------- | ------------- | ------------- | -------------
hostname or group-name listed on hosts files | dbnode01 | Yes | 1 | hosts who are placed inside of the hosts  file
db pxc version | 56 | Yes | 2 | 56,57,80
pxc galera cluster gtid | 56 | Yes | 3 | integer unique number between 1 to 1024 to identify gtid pxc galera cluster
db pxc galera primary server address | 10.70.2.248 | Yes | 4 | primary server ip address or dns name respective for
pxc galera cluster name | pxc56 | Yes | 5 | unique name to identify pxc galera cluster
pxc galera cluster members | 10.70.2.248,10.70.2.201,10.70.2.170 | Yes | 6 | list of ip addresses for the machines who will belongs to the cluster

PS: Just remember that you can do a single installation at the time or a group installation you inform the name of the group in the hosts' files instead of the host itself.

The PXC versions supported for this script are these between the round brackets (56,57,80).
