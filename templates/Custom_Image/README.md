# Using custom images in a CycleCloud cluster.

## Note:
_*It is assumed that you have some familiarity with Azure CycleCloud. If not, please try the [tutorials in the lab](https://github.com/cyclecloud/cyclecloud_tutorials).

## Pre-requisites
1. A running and configured Azure CycleCloud server
2. The CycleCloud CLI installed and configured with a Azure CycleCloud server

## Custom Templates
This directory contains two identical cluster templates, except that one uses GridEngine for the scheduler while the other uses PBSPro. Diff the two files to see the difference, which is essentially the specs used in the different nodes.

To use the templates:
### Import the cluster as a template:
    ```
    $ cyclecloud import_template pbspro_customimage -f pbspro_customimage.txt 
    Go to the CyleCloud UI and you’ll see an Icon named "PBSpro_customimage" in the icon wall.
    ```

### Create a cluster using the template:
- Use the "+" button to add a new cluster, and select the icon for cluster you just imported.

In the Advanced Settings of the cluster form, there’s a text box to fill in the resource ID of a custom image. The resource ID should look like this:
/subscriptions/${SUBSCRIPTION-ID}/resourceGroups/${RESOURCE-GROUP} /providers/Microsoft.Compute/images/${IMAGE-NAME}

- Start the cluster


 
