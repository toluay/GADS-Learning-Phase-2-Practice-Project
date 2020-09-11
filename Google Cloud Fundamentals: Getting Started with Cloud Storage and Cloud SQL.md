##Google Cloud Fundamentals: Getting Started with Cloud Storage and Cloud SQL

#Objectives

In this lab, you learn how to perform the following tasks:
-Create a Cloud Storage bucket and place an image into it.
-Create a Cloud SQL instance and configure it.
-Connect to the Cloud SQL instance from a web server.
-Use the image in the Cloud Storage bucket on a web page.

##Steps 

1.)Deploy a web server VM instance

    gcloud compute instances create bloghost --metadata-from-file startup-script=examples/scripts/install.sh
    
    the starting scripts is in the file install.sh: 
    apt-get update
    apt-get install apache2 php php-mysql -y
     service apache2 restart
     
  2) Create a Cloud Storage bucket using the gsutil command line
  
    i) For convenience, enter your chosen location into an environment variable called LOCATION. Enter one of these commands:
      export LOCATION=US
      or 
      export LOCATION=EU
      
     ii)In Cloud Shell, the DEVSHELL_PROJECT_ID environment variable contains your project ID. Enter this command to make a bucket named after your project ID:
     
     gsutil mb -l $LOCATION gs://$DEVSHELL_PROJECT_ID
     
     iii) Retrieve a banner image from a publicly accessible Cloud Storage location:
     
     gsutil cp gs://cloud-training/gcpfci/my-excellent-blog.png my-excellent-blog.png
     
     iv)Copy the banner image to your newly created Cloud Storage bucket:
     
     gsutil cp my-excellent-blog.png gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png
     
     v)Modify the Access Control List of the object you just created so that it is readable by everyone:
     
     gsutil acl ch -u allUsers:R gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png
     
  3) Create the Cloud SQL instance
  
     gcloud sql instances create blog-db --password="admin" --sql-version=MYSQL_5_7" --zone=us-central1-a
     
    To create a user  blogdbuser with password :
    
    gcloud sql users create blogdbuser --instance=blog-db -i blog-db --host='%' --password='password'
    
     i) Create a GCP compute instance and authorize it:
      Create a file of type “.sh”, in linux the extension don’t really matter. They just tell us what the file does. Call the file anything you like I will call mine “apache-install.sh”, and add these lines to it
   
   #! /bin/bash
apt-get update
apt-get install apache2 php php-mysql -y
service apache2 start

     ii) Use the below command to get the IP address of the Sql instance and use that in the variable “$dbserver”:

     gcloud sql instances list
  4)Use the image in the Cloud Storage bucket on a web page.
  
  i)Enter this command to set your working directory to the document root of the web server:
  
  cd /var/www/html
  
  ii)Use the nano text editor to edit index.php:
  
  sudo nano index.php
  
  iii)Paste this HTML markup immediately before the URL:
  before :<img src='
  then use the storage 
  <img src='https://storage.googleapis.com/qwiklabs-gcp-0005e186fa559a09/my-excellent-blog.png'>
  iv)Restart the web server:
  
  sudo service apache2 restart
  
  
