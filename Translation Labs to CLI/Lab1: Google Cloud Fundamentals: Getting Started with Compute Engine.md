# Google Cloud Fundamentals: Getting Started with Compute Engine


https://googlepluralsight.qwiklabs.com/focuses/11017277?parent=lti_session


## Objectives

 - Create a Compute Engine virtual machine using the Google Cloud Platform (GCP) Console.
 - Create a Compute Engine virtual machine using the gcloud command-line interface.
 - Connect between the two instances.


## Task 1: Sign in to the Google Cloud Platform (GCP) Console

## Task 2: Create a virtual machine using the GCP Console

  ```sh
gcloud config set compute/zone us-central1-a

           gcloud compute instances create "my-vm-1"  --machine-type "n1-standard-1"  --image-project "debian-cloud"  --image "debian-9-stretch-v20190213"  --subnet "default" --tags http
           
           gcloud compute firewall-rule create allow-http --action=ALLOW --direction=INGRESS --rules=tcp80 --target-tags=http
```
## Task 3: Create a virtual machine using the gcloud command line

- To display a list of all the zones in the region to which Qwiklabs assigned you

```sh
gcloud compute zones list | grep us-central1  
```
- To set your default zone to the one you just chose, enter this partial command gcloud config set compute/zone followed by the zone you chose.

```sh
gcloud config set compute/zone us-central1-b  
```

- To create a VM instance called my-vm-2 in that zone, execute this command:

```sh
gcloud compute instances create "my-vm-2" \
--machine-type "n1-standard-1" \
--image-project "debian-cloud" \
--image "debian-9-stretch-v20190213" \
--subnet "default"
```
- To close the Cloud Shell, execute the following command:
```sh
exit
```
## Task 4: Connect between VM instances
- To open a command prompt on the my-vm-2 instance, click SSH in its row in the VM instances list.
- Use the ping command to confirm that my-vm-2 can reach my-vm-1 over the network:
```sh
ping my-vm-1
```
- Press Ctrl+C to abort the ping command.
- Use the ssh command to open a command prompt on my-vm-1:
```sh
ssh my-vm-1
```
- At the command prompt on my-vm-1, install the Nginx web server:
```sh
sudo apt-get install nginx-light -y
```
- Use the nano text editor to add a custom message to the home page of the web server:
```sh
sudo nano /var/www/html/index.nginx-debian.html
```
- Use the arrow keys to move the cursor to the line just below the h1 header. Add text like this, and replace YOUR_NAME with your name:
```sh
Hi from Omar
```
- Press Ctrl+O and then press Enter to save your edited file, and then press Ctrl+X to exit the nano text editor.
- Confirm that the web server is serving your new page. At the command prompt on my-vm-1, execute this command:
```sh
curl http://localhost/
```
- To exit the command prompt on my-vm-1, execute this command:

```sh
exit
```
- To confirm that my-vm-2 can reach the web server on my-vm-1, at the command prompt on my-vm-2, execute this command:

```sh
curl http://my-vm-1/
```
The response will again be the HTML source of the web server's home page, including your line of custom text.

## Congratulations!
