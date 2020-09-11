# Creating Vurtual Machines

## Objectives
    - Create several standard VMs
    - Create Advanced VMs

## Steps
1.  Create a Utility Virtual Machine
    - region:us-central1, zone:us-central1-c, machinetype:n1-standard-1 External IP:None
        gcloud compute instances create gnc-instance --zone=us-central1-c --machine-type=n1-standard-1 --subnet=default --no-address

2.  Create a Windows virtual machine
    - region:europe-west2, zone:europe-west2-a, Machine_type:n1-standard-2, Image:Windows Server 2016 Datacenter Core, disk_type:SSD, Size:100GB
        gcloud compute instances create gnc-windows --zone=europe-west2-a --machine-type=n1-standard-2 --subnet=default --tags=http-server,https-server --image=windows-server-2016-dc-core-v20200813 --image-project=windows-cloud --boot-disk-size=100GB --boot-disk-type=pd-ssd

3.  Create a custom virtual machine
    - region:us-west1, zone:us-west1-b, Machine_type:Custom, Cores:6, memory:32
        gcloud compute instances create custom --zone=us-west1-b --machine-type=custom-6-32768

