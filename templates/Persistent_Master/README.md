# An Example Template for a Cluster with a *_Persistent_* master node

It is often useful to restart a terminated cluster and have it come up with the same IP address for the master node, as well as have persistent data disks attached to the exported NFS volume that are re-attached.

This cluster template is an example showing how one can modify a default CycleCloud template to make the master node "persistent".

## Sidenote About "Stopping" Clusters
There is no function in Azure CycleCloud for "Stop" or "Deallocate". There is only one way to "turn off" a running cluster:  *Terminate*

A terminate operation deprovisions the master node (and any running compute nodes). *All network and storage* resources for the master node are also deprovisioned by default. The only thing that is retained is the record of the cluster in the datastore of CycleCloud, so that if it is ever re-started again, the cluster attributes are recalled and used. In other words, the cluster will be brought up with the same architecture (image used, CycleCloud projects, subnets etc)

A terminated cluster can be *Deleted*. This essentially deletes all records of the cluster from the datastore, and the cluster can not be re-created again with the same architecture. You would have to re-create the cluster from the UI or re-import the cluster template.

## Modifiying a Cluster Template to make the master node persistent

Take a look at the example `persistent_master.txt` in this directory.

- Specify that the primary network interface `eth0` of the master node uses a static public IP address:
  * Lines 23 to 55 defines the master node of the cluster
  * Lines 32-34 (shown below) defines the primary network interface for the master node
  * `AssociatePublicIpAddress` is a boolean variable that is parameterized and can be toggled with a check-box in the cluster creation or edit page
  * `StaticPublicIpAddress = true` is the additional attribute that specifies that the public IP address assigned be retained across a cluster termination. 

    ```INI
        [[[network-interface eth0]]]
        AssociatePublicIpAddress = $UsePublicNetwork
        StaticPublicIpAddress = true
    ```

- Add persistent data disks to the master node:

  * Lines 40-55 shows how one would attach persistent data disks and use that for the exported NFS volume
  * Lines 40 to 50 specify that two premium disks (SSD = True) of 512GB each should be added to the master node when it is provisioned. These disks are persistent `Persistent = true`, meaning that they will not be deleted if the cluster is terminated. However, they *will* be deleted if the cluster is deleted. 
  * Lines 52-55 specify that the two disks are raided in a RAID0 config, formatted as an `ext4` filesystem, and mounted at `/mnt/exports`
  * `/mnt/exports` is the default NFS export directory for all CycleCloud clusters.


    ```INI
        [[[volume nfs-1]]]
        Size = 512
        SSD = True
        Mount = nfs
        Persistent = true

        [[[volume nfs-2]]]
        Size = 512
        SSD = True
        Mount = nfs
        Persistent = true

        [[[configuration cyclecloud.mounts.nfs]]]
        mountpoint = /mnt/exports
        fs_type = ext4
        raid_level = 0
    ```



## Usage

This cluster template is meant as an example and not as a standalone template, but if you wish to use this to start a clsuter:

- Import the cluster template:
```CLI
cyclecloud import_template "Persistent-Master" -f persistent_master.txt
```
- Go to your Azure CycleCloud Server and Create a New Cluster. You will see a new cluster icon named "Persistent-Master"

