======================
 Ansible Role: slurm
======================

This role configures the various pieces of a Slurm Cluster.

----------------
 Role Variables
----------------

Key role variables are documented with their default values below. See ``defaults/main.yml`` for a full list.

::

    slurm_munge_key: ""

This key must be set and will be shared amongst all hosts in the cluster for message authentication.

::

    slurm_repo_url: ""

This must be the URL of an RPM repository that Yum will use to install the various Slurm components.

::

    slurm_run_slurmd: False
    slurm_run_slurmctld: False
    slurm_run_slurmd: False

Setting any of these to true will cause the corresponding service to be configured and started on a host.

::

    slurm_slurmctld_hosts: []

Nodes in the cluster will look to the hosts in this list as the Control hosts.

::

    slurm_slurmdbd_host: ""
    slurm_slurmdbd_backuphost: ""

The SlurmDBD and backup SlurmDBD hostnames, respectively. If the latter is set, Slurm Accounting will be configured.

::

    slurm_node_config: ""

This text block will be dropped into the Slurm config and should contain the node configuration directives.

::

    slurm_partition_config: ""

This text block will be dropped into the Slurm config and should contain the partition configuration directives.


------------------
 Example Playbook
------------------

With a hosts file like the following:

::

    [Cluster]
    mgmt slurm_run_slurmctld=True
    login[0-1]
    compute[01-10] slurm_run_slurmd=True

The following playbook will configure a cluster:

::

    ---
    - hosts: Cluster
      vars:
        slurm_repo_url: "https://repos.example.com/slurm/"
        slurm_munge_key: "{{ lookup('file', 'keys/munge/munge.key') }}"
        slurm_slurmctld_hosts: ['mgmt']
        slurm_node_config: |
          NodeName=DEFAULT CPUS=2 RealMemory=500 State=UNKNOWN
          NodeName=compute[01-10]
        slurm_partition_config: |
          PartitionName=DEFAULT State=UP
          PartitionName=batch Nodes=compute[01-10] MaxTime=10 Default=YES
      roles:
        - slurm
