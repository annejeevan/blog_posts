
# Setup 4 Node Hadoop Cluster on AWS EC2 Instances

We will try to create an image from an existing AWS EC2 instance after installing java and hadoop on it. If there is no instance created yet, create one and login to the instance using this article.

### Install Java And Hadoop

* Its always a good way to upgrade the repositories first. apt-get update downloads the package lists from the repositories and "updates" them to get information on the newest versions of packages and their dependencies.

    $ sudo apt-get **update** && sudo apt-**get** dist-**upgrade**

![](https://cdn-images-1.medium.com/max/2006/1*p-eEUV140Pzxm2GFtaCkHg.jpeg)

### Install OpenJDK

* Installing latest java.

    $ sudo apt-get install openjdk-8-jdk

![](https://cdn-images-1.medium.com/max/2000/1*9fsU7cn_9Z9VKxgicHjbEw.jpeg)

### Installing Hadoop

* Download Hadoop from one of [these mirrors](http://www.apache.org/dyn/closer.cgi/hadoop/common). Select appropriate version number. Below command will download gzip file and copies it to Downloads directory, which is created using -P paramter.

    $ wget [http://apache.mirrors.tds.net/hadoop/common/hadoop-2.8.1/hadoop-2.8.1.tar.gz](http://apache.mirrors.tds.net/hadoop/common/hadoop-2.8.1/hadoop-2.8.1.tar.gz) -P ~/Downloads

![](https://cdn-images-1.medium.com/max/3774/1*HyCaiIlGPiIXqvuPc938iw.jpeg)

* We will now try to extract it to /usr/local.

    $ sudo tar zxvf ~/Downloads/hadoop-* -C /usr/local

* Renaming the hadoop-* to hadoop under /usr/local directory.

    $ sudo mv /usr/local/hadoop-* /usr/local/hadoop

Now the Java and Hadoop are installed. We will declare the environmental variables in the instance, which helps applications locate hadoop.

### **Setting up Environmental Variables**

* To know where the java is installed (where the java executable is), execute the below command. Path may be different for you.

![](https://cdn-images-1.medium.com/max/2000/1*J4gt-bDm_j2ePn4C3XcqsQ.jpeg)

* Open .bashrc file in your home directory with your favorite editor. Include the below lines .

    $ vi ~/.bashrc

For Java:

    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
    export PATH=$PATH:$JAVA_HOME/bin

For Hadoop:

    export HADOOP_HOME=/usr/local/hadoop
    export PATH=$PATH:$HADOOP_HOME/bin

For Hadoop Configuration directory:

    export HADOOP_CONF_DIF=/usr/local/hadoop/etc/hadoop

For reflecting to current session with out restarting.

    source ~/.bashrc

Check whether the environmental variables are available or not.

### Creating an Image

* We will create an image from AWS console, with all the above configurations. This helps us in creating nodes in hadoop cluster with out repeating the above steps for each node.

* On EC2 management console, select “Instances” under INSTANCES. And then “Actions” -> “Image” -> “Create Image”

![](https://cdn-images-1.medium.com/max/2212/1*lLBd2e29NAijsrJa5ltibg.jpeg)

* Provide any name, description and click on “Create Image”.

![](https://cdn-images-1.medium.com/max/2624/1*ogt2c0eTVvo3bHalT2z9Dg.jpeg)

* You will be able to find the created image under IMAGES -> “AMIs”.

![](https://cdn-images-1.medium.com/max/3274/1*W5EWMTg3Ad3uj3cA0cnCyw.jpeg)

## Setting up Cluster Nodes from the Image Created

* You have created an image with Java and Hadoop installed, which you can use it to create nodes in the cluster. Select the created image and click “Launch”

* Choose an Instance Type according to the requirement. Here we stick the default t2.micro instance type. Click on “Next: Configure Instance Details”

* Change the “Number of instances” from 1 to 4 in Configure Instance Details. Out of 4 (NameNode -1 , DataNodes-3).

* Default storage. Click on “Next: Add Tags”

* Optional: Create a rule with Name as Key and “Hadoop Cluster” as Value and click on “Next: Configure Security Group”

* Select “All Traffic” from the dropdown and click on “Review and Launch”. And then Launch with key pair already created.

* Instances will be created as shown below. I have edited the Names for each node.

![](https://cdn-images-1.medium.com/max/2540/1*Yy-VVyCfSkBp4Qj_3Z0jyw.jpeg)

* Lets create a SSH config file to log in to the instances easily. On your computer we could use either Putty (as showed [here](https://medium.com/@jeevananandanne/guide-to-set-up-ubuntu-16-04-on-aws-ec2-instance-745f3433f16)) or GIT BASH (ensure it is installed). I will be using GIT BASH here.

    $ touch ~/.ssh/config

![](https://cdn-images-1.medium.com/max/2000/1*iZulfTfOvm3tOet5sv1bOQ.jpeg)

* Edit the config file.

    vi ~/.ssh/config

* Copy the below lines to the file. (Probably you need click on the middle button of mouse to paste in the file)

    Host namenode
      HostName ec2-18-216-40-160.us-east-2.compute.amazonaws.com
      User ubuntu
      IdentityFile ~/.ssh/MyLab_Machine.pem

    Host datanode1
      HostName ec2-18-220-65-115.us-east-2.compute.amazonaws.com
      User ubuntu
      IdentityFile ~/.ssh/MyLab_Machine.pem

    Host datanode2
      HostName ec2-52-15-229-142.us-east-2.compute.amazonaws.com
      User ubuntu
      IdentityFile ~/.ssh/MyLab_Machine.pem

    Host datanode3
      HostName ec2-18-220-72-56.us-east-2.compute.amazonaws.com
      User ubuntu
      IdentityFile ~/.ssh/MyLab_Machine.pem

* This file lets SSH associate a shorthand name with a hostname, a user, and the private key, so you don’t have to type those in each time. This is assuming your private key MyLab_Machine.pem is in .ssh. If it isn't be sure to move or copy it there: cp key_file ~/.ssh/MyLab_Machine.pem. Now you can log into the NameNode with just $ ssh namenode. Also, copy the config file to the NameNode.

    $ scp ~/.ssh/config namenode:~/.ssh

![](https://cdn-images-1.medium.com/max/3744/1*VMaAt-eWPlFbR3X3x25MkA.jpeg)

* We need to make sure the NameNode can connect to each DataNode over ssh without typing a password. You’ll do this by creating a public key for the NameNode and adding it to each DataNode.

* Log in to NameNode, create a public key using ssh-keygen and copy it to authorized_keys.

    $ ssh namenode
    $ ssh-keygen -f ~/.ssh/id_rsa -t rsa -P ""
    $ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

* In order to login to the each DataNode without a password from NameNode. Copy the authorized_keys to each DataNode.

    $ ssh datanode1 'cat >> ~/.ssh/authorized_keys' < ~/.ssh/id_rsa.pub
    $ ssh datanode2 'cat >> ~/.ssh/authorized_keys' < ~/.ssh/id_rsa.pub
    $ ssh datanode3 'cat >> ~/.ssh/authorized_keys' < ~/.ssh/id_rsa.pub

* Try logging into DataNodes and test if you are able to login without a password.

### Configuring the Cluster

**Cluster-wide configuration:**

* First, you’ll deal with the configuration on each node, then get into specific configurations for the NameNode and DataNodes. On each node, go to the Hadoop configuration folder, you should be able to get there with $ cd $HADOOP_CONF_DIR since we set that in .bashrc earlier. When editing these configuration files, you'll need root access so remember to use $ sudo. In the configuration folder, edit core-site.xml:

    <configuration>
      <property>
        <name>fs.defaultFS</name>
        <value>hdfs://<your namenode public dns name>:9000</value>
      </property>
    </configuration>

* This configuration fs.defaultFS tells the cluster nodes which machine the NameNode is on and that it will communicate on port 9000 which is for hdfs.

* On each node, in yarn-site.xml you set options related to YARN, the resource manager:

    <configuration>
    
    <!— Site specific YARN configuration properties -->
    
      <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
      </property>
      <property>
        <name>yarn.resourcemanager.hostname</name>
        <value><your namenode public dns name></value>
      </property>
    </configuration>

* Similarly with fs.defaultFS, yarn.resourcemanager.hostname sets the machine that the resource manager runs on.

* On each node, copy mapred-site.xml from mapred-site.xml.template

    $ sudo cp mapred-site.xml.**template** mapred-site.xml

* Add below to the mapred-site.xml

    <configuration>
      <property>
        <name>mapreduce.jobtracker.address</name>
        <value><your namenode public dns name>:54311</value>
      </property>
      <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
      </property>
    </configuration>

* Again, mapreduce.jobtracker.address sets the machine the job tracker runs on, and the port it communicates with. The other option here mapreduce.framework.name sets MapReduce to run on YARN.

**NameNode specific configuration:**

* Now, NameNode specific configuration, these will all be configured only on the NameNode. First, add the DataNode hostnames to /etc/hosts. You can get the hostname for each DataNode by entering $ hostname, or $ echo $(hostname) on each DataNode.

* Now edit /etc/hosts and include these lines:

    <namenode_IP> namenode_hostname
    <datanode1_IP> datanode1_hostname
    <datanode2_IP> datanode2_hostname
    <datanode3_IP> datanode3_hostname
    127.0.0.1 localhost

* Edit hdfs-site.xml file on NameNode as below:

    <configuration>
      <property>
        <name>dfs.replication</name>
        <value>3</value>
      </property>
      <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:///usr/local/hadoop/data/hdfs/namenode</value>
      </property>
    </configuration>

* dfs.replication sets how many times each data block is replicated across the cluster. dfs.namenode.name.dir sets the directory for storing NameNode data (.fsimage). You’ll also have to create the directory to store the data.

    $ sudo mkdir -p $HADOOP_HOME/data/hdfs/namenode

* Next, you’ll create the masters file in HADOOP_CONF_DIR. The masters file sets which machine the secondary namenode runs on. In your case, you'll have the secondary NameNode run on the same machine as the NameNode, so edit masters, add the hostname of NameNode (**Note:** Not the public hostname, but the hostname you get from $ hostname). Typically though, you would have the secondary NameNode run on a different machine than the primary NameNode.

* Next, edit the slaves file in HADOOP_CONF_DIR, this file sets the machines that are DataNodes. In slaves, add the hostnames of each datanode (**Note:** Again, not the public hostname, but $ hostname hostnames). The slaves file might already contain a line localhost, you should remove it, otherwise the NameNode would run as a DataNode too. It should look like this:

    datanode1_hostname
    datanode2_hostname
    datanode3_hostname

* Finally on the NameNode, change the owner of HADOOP_HOME to ubuntu

    $ sudo chown -R ubuntu $HADOOP_HOME

**DataNode specific configura:**

* Edit HADOOP_CONF_DIR/hdfs-site.xml on each DataNode:

    <configuration>
      <property>
        <name>dfs.replication</name>
        <value>3</value>
      </property>
      <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:///usr/local/hadoop/data/hdfs/datanode</value>
      </property>
    </configuration>

* Again, this sets the directory where the data is stored on the DataNodes. And again, create the directory on each DataNode. Also change the owner of the Hadoop directory.

    $ sudo mkdir -p $HADOOP_HOME/data/hdfs/datanode
    $ sudo chown -R ubuntu $HADOOP_HOME

### **Launch Hadoop Cluster**

* On the NameNode, format the file system, then start HDFS.

    $ hdfs namenode -format
    $ $HADOOP_HOME/sbin/start-dfs.sh

* Start YARN.

    $$HADOOP_HOME/sbin/start-yarn.sh

* Start the job history server.

    $ $HADOOP_HOME/sbin/mr-jobhistory-daemon.sh start historyserver

* To see the Java processes (Hadoop daemons for instance), enter

    $jps

You can access the NameNode WebUI.

![](https://cdn-images-1.medium.com/max/3482/1*LL8yj9hzK7RiTj3N_b-qVg.jpeg)
