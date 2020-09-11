# Google Cloud Fundamentals: Getting Started with Compute Engine

## Objectives
    - Create a Compute Engine virtual machine using the Google Cloud Platform (GCP) Console.

    - Create a Compute Engine virtual machine using the gcloud command-line interface.

    - Connect between the two instances.

## Steps

1. Create a virtual machine using the GCP Console

        gcloud compute instances create my-vm-1 --zone=us-central1-a --machine-type=n1-standard-1 --subnet=default --tags=http-server --image=debian-9-stretch-v20200910 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=my-vm-1

2. Create a virtual machine using the gcloud command line

        gcloud config set compute/zone us-central1-a

        gcloud compute instances create "my-vm-2" --machine-type "n1-standard-1" --image-project "debian-cloud" --image "debian-9-stretch-v20190213" --subnet "default"

3. Connect between VM instances

    1. Use the ping command to confirm that my-vm-2 can reach my-vm-1 over the network

        - Connect to my-vm-2

                gcloud compute ssh my-vm-2

        - ping my-vm-1 from my-vm-2

                ping -c 4 my-vm-1

        - use the ssh command to open a command prompt on my-vm-1 from my-vm-2

                ssh my-vm-1

        - At the command prompt at my-vm-1 install the Nginx server.

                sudo apt-get install nginx-light -y 

        - Use the nano text editor to add a custom message to the home page of the web server

                sudo nano /var/www/html/index.nginx-debian.hmtl

        - Use the arrow keys to move the cursor to the line just below the h1 header. Add text like this, and replace YOUR_NAME with your name

                Hi, from Chidiebere

        - Exit the text editor and confirm the web server is serving your new page. At the command prompt at my-vm-1, execute this command:

                curl http:://localhost

            - Result:
                    The response will be the HTML source of the web server's home page, including your line of custom text

        - To exit to command prompt on my-vm-1, execute this command:

                exit

        - To confirm that my-vm-2 can reach the web server on my-vm-1, at the command promt on my-vm-2, execute this command:

                curl http://my-vm-1

            - Result:
                    The response will be the HTML source of the web server's home page, including your line of custom text

    2. How to get the external ip address  of my-vm-1 instance from this command:

            gcloud compute instances list --zone us-central1-agigt branch


        


