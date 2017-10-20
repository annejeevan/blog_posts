
# Guide to Set up Ubuntu 16.04 on AWS EC2 Instance

In this tutorial, We will learn how to configure the Ubuntu 16.04 OS on AWS (Amazon Web Service) EC2 (Elastic Cloud Compute) Instance from scratch.

### **Sign up for AWS**

* Go to [AWS signup page](https://portal.aws.amazon.com/billing/signup?redirect_url=https%3A%2F%2Faws.amazon.com%2Fregistration-confirmation#/start), if you don’t have an account already and continue with the process. For this you would need your Credit Card details (because instances will be charged based on the usage, we are just using free tier) and a valid phone number.

![](https://cdn-images-1.medium.com/max/2592/1*2QiRLaDnweF2JMB5BY4-iA.jpeg)

* These are virtual machines which we are going to create can be requested and run from anywhere in the world, typically at some cost per hour (recently Amazon announced [per second billing](https://aws.amazon.com/blogs/aws/new-per-second-billing-for-ec2-instances-and-ebs-volumes/)), per machine. Each virtual machine has a configurable number of CPUs and amount of memory and storage. We’re going to be using a free offering, but most organizations will need more resources to store and analyze their data.

### Login and access to AWS services

* After you log in, you should choose a region that is closest to you shown here. You could select the services you need. We will be select EC2 service under Compute.

![](https://cdn-images-1.medium.com/max/3604/1*NW2QcHIHJV7HubjZ58mArA.jpeg)

* On the EC2 dashboard, Click on ‘Launch Instance’ button in the section of Create Instance (as shown below).

![](https://cdn-images-1.medium.com/max/2000/1*MEGRIn5oTvzHeXwSLPhdFQ.jpeg)

### Choose AMI and Instance Type

* You will be asked to choose an AMI of your choice. (An AMI is an Amazon Machine Image. It is a template basically of an Operating System platform which you can use as a base to create your instance). Once you launch an EC2 instance from your preferred AMI, the instance will automatically be booted with the desired OS.

* Here we are choosing **Ubuntu Server 16.04 LTS (64-bit) **AMI.

![](https://cdn-images-1.medium.com/max/3720/1*8DJowR79-C4cQzjnn0AWxw.jpeg)

* We will choose t2.micro instance type, which is a 1vCPU and 1GB memory server offered by AWS as a free tier. Click on “Next: Configure Instance Details” for further configurations

![](https://cdn-images-1.medium.com/max/3766/1*-122Q8n-AH2iDZc2yQJiIA.jpeg)

### Configure Instance

* Here are you can mention the number of instances needed, also we could create new VPC, which we will not be exploring here. We will keep it as is and proceed by clicking “Next: Add Storage”

![](https://cdn-images-1.medium.com/max/3766/1*tFbElTdB12IOp6AZWZcelg.jpeg)

### Add Storage

* This is where you set the storage for your instance, 8 GB is by default. It changes as per your requirement. Click “Next: Tag Instance”

![](https://cdn-images-1.medium.com/max/3800/1*GIFEMMgVtQvgN8BFYuppug.jpeg)

### Add Tag

* You can create a tag to your instance. Anything is fine, I used “Hadoop”. This gives great visibility on what exactly the type of instance is. Click “Next: Configure Security Group.”

![](https://cdn-images-1.medium.com/max/3796/1*YnSDNJE25GR3VWLGdFBLSQ.jpeg)

### Configure Security Group

* The security group determines who can connect to your instances. The default is “SSH” and 0.0.0.0/0. This means that any computer can connect to your instance over ssh. Generally, you would create different rules to restrict list of IP addresses that can access your cluster on port range. However, for ease, you should leave it open. You’ll also need to change the Source to “My IP” (it automatically pops up the ip address) so that we can access the instances over SSH. Then, click “Review and Launch” to launch your instances.

![](https://cdn-images-1.medium.com/max/3818/1*vZX_sIxyfYEID_ZnvQ4w5g.jpeg)

Launch the instance by clicking on “Launch”. You will be prompted to create and download a private key. This key will allow you to connect to your instances with SSH. If you don’t download this, or delete it somehow, you won’t be able to connect to your cluster. If you lose it, don’t panic, but you’ll have to shut down the instances and start up new ones. Name it whatever you like, I just used “MyLab_Machine.” Click “View Instances” to see your instance booting up in the EC2 dashboard. You’ll want to write down the public hostname, called “Public DNS” on the instance panel.

![](https://cdn-images-1.medium.com/max/3784/1*7RTg-LgIpE9-UofKLe9PmA.png)

![](https://cdn-images-1.medium.com/max/2446/1*5bE5UmqrXrhLJLxX4BMVgg.jpeg)

### Connecting to the Created Remote Instance using Putty

* As you have downloaded the key pair (which is with .pem extension), we need to generate private key to login to the remote instance using putty.

* By installing [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), PuttyGen will also be installed. Open the PuttyGen, select “File” -> “Load private key”. It will generate the private key. Save private key to a file by selecting “Save private key”.

![](https://cdn-images-1.medium.com/max/2000/1*Xf-uG329ZS-odi_56Imhyg.jpeg)

* Now, open Putty, Select the “SSH” and expand under “Connection”, select “Auth” and then load the .ppk file as shown below.

![](https://cdn-images-1.medium.com/max/2000/1*CtUA43_ok30Mb7A9m51aCA.jpeg)

* Fill the Hostname field with ubuntu@<your public dns> (here ubuntu is the user account). Also you can save the session by giving it a name and clicking on “Save”.

![](https://cdn-images-1.medium.com/max/2000/1*rPbMDaMHuGJtGBFjFXzWJQ.jpeg)

* Click on Open, and “yes” to login to the server.

![](https://cdn-images-1.medium.com/max/2000/1*NwtpmY-nZVi4BW7zE13Gmw.jpeg)

You are logged in now.

**Note: If you forget to terminate the instance when not in use, you could be hit with real sticker shock when you get your next invoice.**
