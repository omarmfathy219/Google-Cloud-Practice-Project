# Google Cloud Fundamentals: Getting Started with Cloud Storage and Cloud SQL

https://googlepluralsight.qwiklabs.com/focuses/11121994?parent=lti_session

## Objectives

 - Create a Cloud Storage bucket and place an image into it.
 - Create a Cloud SQL instance and configure it.
 - Connect to the Cloud SQL instance from a web server.
 - Use the image in the Cloud Storage bucket on a web page.




## Task 1: Deploy a web server VM instance

1- Setup your cloud shell environment Open your Cloud shell terminal. Once the initialization is complete we will be ready to start.
- Config the project to use for this session, (assume project name is, sky-high-cloud-12345)
- 
```sh
export PROJECT_ID=sky-high-cloud-12345
```
- Next, tell gcloud about our project,

```sh
gcloud config set project $PROJECT_ID
```
2- Create VM instance We are going to create a vm instance in our us-central1-a zone and will use a debian-9-stretch image, run:

```sh
gcloud compute instances create bloghost \
--zone us-central1-a \
--machine-type e2-medium \
--image-project debian-cloud \
--image debian-9-stretch-v20200910 \
--subnet default \
--metadata startup-script="apt-get update; apt-get install apache2 php php-mysql -y; service apache2 restart;"
```

3- Edit firewall rules We are going to allow HTTP traffic to our VM instance, run:

 ```sh 
 gcloud compute firewall-rules create default-allow-http \
 --direction=INGRESS \
 --priority=1000 \
 --network=default \
 --action=ALLOW \
 --rules=tcp:80 \
 --source-ranges=0.0.0.0/0 \
 --target-tags=http-server
 ```

## Task 2: Create a Cloud Storage bucket using the gsutil command line

1- Create a storage bucket To create a storage bucket in US location, run:

```sh
gsutil mb -l US gS://$DEVSHELL_PROJECT_ID
```
2- Copy image to our storage bucket. We will copy an image hosted on a different cloud storage bucket into our bucket, run:

```sh
gsutil cp gs://cloud-training/gcpfci/my-excellent-blog.png gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png
```

3- Verify item was copied. To check that the image was copied to our storage bucket, run:

```sh
gsutil ls -l gs://$DEVSHELL_PROJECT_ID/
```

## Task 3: Create the Cloud SQL instance
1- Create a MySQL cloud SQL instance We are going to create MySQL instanace blog-db with root password set to Omar123 and in theus-central1-a zone.

```sh
gcloud sql instances create blog-db \
--root-password Omar123 \
--zone us-central1-a \
--database-version MYSQL_5_6
```
2- Create a user in our instance We are going to add a user account to the blog-db instance, run:

```sh
gcloud sql users create blogdbuser --instance blog-db --host % --password Omar123
```

3- Update authorized networks We are going to add the external IP address for our VM into sql instance authorized networks list. First we wil grab the external IP for our bloghost vm, run:


```sh
export bloghost_xip=$(gcloud compute instances describe bloghost --zone us-central1-a --format 'get(networkInterfaces[0].accessConfigs[0].natIP)')
```

## Task 4: Configure an application in a Compute Engine instance to use Cloud SQL

1- SSH into bloghost vm

```sh
gcloud compute ssh bloghost --zone us-central1-a --quiet
```
2- Navigate to the web server index file Switch the command prompt path server default html directory, run:

```sh
cd /var/www/html
```
3- Open the index.php to edit it Using nano lets to create and edit the index.php file, run:

```sh
sudo nano index.php
```
Paste in the following content into the file:

```html
<html>
<head><title>Welcome to my excellent blog</title></head>
<body>
<h1>Welcome to my excellent blog</h1>
<?php
$dbserver = "CLOUDSQLIP";
$dbuser = "blogdbuser";
$dbpassword = "DBPASSWORD";
// In a production blog, we would not store the MySQL
// password in the document root. Instead, we would store it in a
// configuration file elsewhere on the web server VM instance.

$conn = new mysqli($dbserver, $dbuser, $dbpassword);

if (mysqli_connect_error()) {
 echo ("Database connection failed: " . mysqli_connect_error());
} else {
 echo ("Database connection succeeded.");
}
?>
</body>
</html>
```
- Replace the DBPASSWORD with your instance password, fo rour case this will be Omar123
- To get the IP address for the blog-db sql instance. Open another tab and run:

```sh
gcloud sql instances describe blog-db --format 'get(ipAddresses.ipAddress)'
```
- Copy the output. Go back to bloghost vm ssh terminal and replace CLOUDSQLIP with the copied value.
- Press Ctrl+O, and then press Enter to save your edited file. Press Ctrl+X to exit the nano text editor.

4- Restart the webserver On bloghost vm ssh instance, run:

```sh
sudo service apache2 restart
```
- Open a new web browser tab and paste into the address bar your bloghost VM instance's external IP address followed by /index.php. The URL will look like this: 35.192.208.2/index.php

- When you load the page, the following message appears:

```sh
Database connection succeeded.
```

## Congratulations!