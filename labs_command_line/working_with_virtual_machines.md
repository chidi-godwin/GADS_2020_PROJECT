# Working with Virtual Machines

## Objectives
    - Customize an application server
    -Install and configure necessary software
    -Configure network access
    -Schedule regular backups

## Steps
1. Create the VM

        gcloud compute instances create mc-server --zone=us-central1-a --machine-type=n1-standard-1 --subnet=default --address=34.121.52.57 --tags=minecraft-server --image=debian-9-stretch-v20200902 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=mc-server --create-disk=mode=rw,size=50,name=minecraft-disk,device-name=minecraft-disk

2. Prepare the data disk

    1. For mc-server, click SSH to open a terminal and connect.
            gcloud compute ssh --zone "us-central1-a" "mc-server"

    2. To create a directory that serves as the mount point for the data disk
            sudo mkdir -p /home/minecraft

    3. Format the disk
            sudo mkfs.ext4 -F -E lazy_itable_init=0, lazy_journal_init=0,discard /dev/disk/by-id/google-minecraft-disk

    4. Mount the disk
            sudo mount -o discard,defaults /dev/disk/by-id/google-minecraft-disk /home/minecraft

3. Install and run the application
    - Install the Java Runtime Environment (JRE) and the Minecraft server

        1. update the Debian repositories on the VM
                sudo apt-get update

        2. install the headless JRE
                sudo apt-get install -y default-jre-headless

        3. navigate to the directory where the persistent disk is mounted
                cd /home/minecraft

        4. install wget
                sudo apt-get install wget

        5. download the current Minecraft server JAR file (1.11.2 JAR)
                sudo wget https://launcher.mojang.com/v1/objects/d0d0fe2b1dc6ab4c65554cb734270872b72dadd6/server.jar

    - Initialize the Minecraft server

        1. initialize the Minecraft server
                sudo java -Xmx1024M -Xms1024M -jar server.jar nogui

        2. edit the EULA
                sudo nano eula.txt

    - Create a virtual terminal screen to start the Minecraft server

        1. install screen
                sudo apt-get install -y screen

        2. start your Minecraft server in a screen virtual terminal
                sudo screen -S mcs java -Xmx1024M -Xms1024M -jar server.jar nogui

    - Detach from the screen and close your SSH session

4. Allow client traffic
    - create firewall rule
            gcloud compute firewall-rules create minecraft-rule --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:25565 --source-ranges=0.0.0.0/0 --target-tags=minecraft-server

5. Schedule regular backups

    - Create a Cloud Storage bucket

        1. connect with ssh
                gcloud compute ssh --zone "us-central1-a" "mc-server"

        2. Create a globally unique bucket name, and store it in the environment variable YOUR_BUCKET_NAME
                export YOUR_BUCKET_NAME=gnc-mincraft

        3. create the bucket using the gsutil tool
                gsutil mb gs://$YOUR_BUCKET_NAME-minecraft-backup

    - Create a backup script

        1. In the mc-server SSH terminal, navigate to your home directory
                cd /home/minecraft

        2. create the script
                sudo nano /home/minecraft/backup.sh

                #!/bin/bash
                screen -r mcs -X stuff '/save-all\n/save-off\n'
                /usr/bin/gsutil cp -R ${BASH_SOURCE%/*}/world gs://${YOUR_BUCKET_NAME}-minecraft-backup/$(date "+%Y%m%d-%H%M%S")-world
                screen -r mcs -X stuff '/save-on\n'

        3. To make the script executable
                sudo chmod 755 /home/minecraft/backup.sh

    - Test the backup script and schedule a cron job
        
        1. In the mc-server SSH terminal, run the backup script

                . /home/minecraft/backup.sh

        2. In the mc-server SSH terminal, open the cron table for editing

                sudo crontab -e

        3. At the bottom of the cron table, paste the following line

                0 */4 * * * /home/minecraft/backup.sh

    
6. Server maintenance

    - Connect via SSH to the server, stop it and shut down the VM

            gcloud compute ssh --zone "us-central1-a" "mc-server"

            sudo screen -r -X stuff '/stop\n'

    - Stop Instance 

            gcloud compute instances stop --zone "us-central1-a" "mc-server"

    - Add custom metadata

            gcloud compute instances add-metadata "mc-server" --metadata=startup-script-url="https://storage.googleapis.com/cloud-training/archinfra/mcserver/startup.sh",shutdown-script-url="https://storage.googleapis.com/cloud-training/archinfra/mcserver/shutdown.sh"

            