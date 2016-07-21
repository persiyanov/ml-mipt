# Goal
Want to move computation on machine with much power.
We will set up Anaconda 4.0.0 and XGBoost 0.4 (it is tricky installable).

# Preliminaries
* [Amazon AWS Educate](https://aws.amazon.com/education/awseducate/?nc1=h_ls) gives 100$ for MIPT students.
* [GitHub Students Pack](https://education.github.com/pack) additionaly gives 15$.

# Let's start
## AWS Console and launching EC2 Instance.

* Register on https://aws.amazon.com/ and Sign In to the AWS Console.
* Get your $$$ from Amazon AWS Educate and GitHub Students Pack and activate them in your account settings (top right corner in AWS Console), "Credits" tab.
* Go to EC2 tab.
* Click Launch Instance button.

#### Step 1. Choose AMI
Choose **Ubuntu Server 14.04 LTS (HVM), SSD Volume Type**. Click **Next**.

#### Step 2. Choose Instance Type
Now you need to choose Instance Type. Types c3,c4,g2,r3* seem good for our tasks. You can compare them on http://www.ec2instances.info/ (Don't forget to choose your region. You can determine and change it in top right corner of your AWS Console.). We choose **c4.4xlarge**. It costs ~20-25 cents per hour.

Click **Next**.

#### Step 3. Configure Instance
There are two types of Amazon Instances: On-Demand and Spot Instances. Read about them here: https://aws.amazon.com/ec2/spot/. In few words, Spot Instances have auction on Amazon EC2 power.

If you want to pay less (~20-25 cents as was said) you need to check **Request Spot instances**. You will see current prices on this type of instance in your region. **Set price** on 2-5 cents higher than maximum of three prices if you want to get an instance as fast as possible.

Click **Next**.

#### Step 4. Add Storage
* Amazon provides 30GB SSD storage device for free. So replace default 8 with 30GiB.
* Uncheck **Delete on Termination**. It prevents from deleting your storage after instance terminating.

Click **Next**.

#### Step 5. Tag Instance
No changes here. Press **Next**.

#### Step 6. Configure Security Group
* Check **Create a new security group**
* Remain SSH rule, and add two more rules: (HTTPS/TCP/443/Anywhere) and (Custom TCP Rule/TCP/8888/Anywhere).

Click **Review and Launch** and then **Launch**.

#### Step 7. Creating key pair
Create new key pair following Amazon's instructions and download it. You need this file for connecting to your instance through SSH. 

Click **Request Spot Instance**.

You will see picture like this:

<img src="https://camo.githubusercontent.com/744706eaaeb6c7b5acd8124223c2347531ac6fd8/68747470733a2f2f646c2e64726f70626f7875736572636f6e74656e742e636f6d2f732f69346a3132303230317061767a74772f494e5354414e43455f4f50454e2e706e673f646c3d30" width="720">

## Connecting instance and configuring SSH config
Wait until your **Instance State** will be *Running* and click **Connect** button.

<img src="https://camo.githubusercontent.com/92b5ca49e6ee50dffd36af81d1800c21284fbd1f/68747470733a2f2f646c2e64726f70626f7875736572636f6e74656e742e636f6d2f732f6f3330337a7131306d666a767571772f72756e6e696e675f636f6e6e6563742e706e673f646c3d30" width="720">

According to instructions, add read permissions:
```bash
chmod 400 aws_c4_xlarge.pem
```

You can follow these instructions and connect to your instance over SSH with command like this:
```bash
ssh -i "aws_c4_xlarge.pem" ubuntu@ec2-52-38-217-74.us-west-2.compute.amazonaws.com
```
but more handy is to connect via
```bash
ssh aws
```

And that's how to do that:
* Create SSH config or edit existing adding these lines:
```bash
Host aws
	HostName ec2-52-11-148-133.us-west-2.compute.amazonaws.com 
	User ubuntu
	IdentityFile ~/.ssh/aws_c4_xlarge.pem
```
* Indentaion with TAB
* Instead of 'aws' you can make your own alias.
* You will have different HostName, check instructions clicking **Connect** button in your AWS Console.

Reopen your terminal and connect to the instance via ```ssh aws```.


## Configuring environment
In this section, we will install Anaconda 4.0.0, XGBoost 0.4 and set up Jupyter server.

#### Anaconda
To install Anaconda, execute the following lines:
```bash
wget https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda2-4.0.0-Linux-x86_64.sh
chmod +x Anaconda2-4.0.0-Linux-x86_64.sh
./Anaconda2-4.0.0-Linux-x86_64.sh
```
Install Anaconda just pressing Enter and typing 'yes' everywhere. Reconnect to the instance:
```bash
logout
ssh aws
```
Create virtual environment with Python 2.7:
```bash
conda create --name venv anaconda
```
To activate virtual environment, type `source activate venv`, to deactivate `source deactivate`.

#### XGBoost
Activate your virtual environment.

Install XGBoost:
```bash
sudo apt-get update & upgrade
sudo apt-get install git make g++ python-setuptools
git clone --recursive https://github.com/dmlc/xgboost
cd xgboost
make -j4
cd python-package
sudo python setup.py install
```
Last command can raise exception `ImportError: No module named numpy.distutils.core`. Whatever, XGBoost is correctly installed. Only thing we need is to add the package to `PYTHONPATH`:
```bash
echo "export PYTHONPATH=~/xgboost/python-package" > ~/.bash_profile
source ~/.bash_profile
```
That's all! Try to import it.
#### Jupyter
You need to generate Jupyter config to start remote server. The simplest way is the following:
```bash
cd ~
wget https://raw.githubusercontent.com/persiyanov/ml-mipt/master/amazon-howto/jupyter_notebook_ec2.sh
chmod +x jupyter_notebook_ec2.sh
./jupyter_notebook_ec2.sh
```
Enter the password which you want to use while connecting to Jupyter through browser. Repeat it. Then press Enter several times. This is the my log:
```bash
(venv)ubuntu@ip-172-31-12-235:~$ chmod +x jupyter_notebook_ec2.sh 
(venv)ubuntu@ip-172-31-12-235:~$ ./jupyter_notebook_ec2.sh 
Writing default config to: /home/ubuntu/.jupyter/jupyter_notebook_config.py
Enter password: 
Verify password: 
Generating a 1024 bit RSA private key
...........................................................................................++++++
...........++++++
writing new private key to 'mycert.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:        
State or Province Name (full name) [Some-State]:
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:
Email Address []:
(venv)ubuntu@ip-172-31-12-235:~$ 
(venv)ubuntu@ip-172-31-12-235:~$ ls
anaconda2  Anaconda2-4.0.0-Linux-x86_64.sh  certs  jupyter_notebook_ec2.sh  xgboost
```

To start Jupyter server, you need to execute `jupyter notebook --certfile=~/certs/mycert.pem --keyfile ~/certs/mycert.key` command or download my bash script which executes this line:
```bash
wget https://raw.githubusercontent.com/persiyanov/ml-mipt/master/amazon-howto/start-jupyter.sh
chmod +x start-jupyter.sh
./start-jupyter.sh
```

Now server has started. Try to connect it via HTTPS. Type in your browser `https://<hostname>:8888` or `https://<public_ip>:8888`. Hostname is the similar to that we used writing SSH config. You can always determine your HostName and Public Ip in AWS Console clicking at your instance. In my case: `https://ec2-52-38-217-74.us-west-2.compute.amazonaws.com:8888`.

Now you can connect to Jupyter and run your notebooks at EC2 instance! But that's not the end. We want to make our interaction with instance more comfortable. Next two sections are about that.


## tmux
We want to off our computer or disconnect from the Internet but preserve computing our models on EC2 instance. As for now, we will lost our SSH session if something from this will happen. And for solving this problem we use `tmux`:
```bash
tmux new -s xxx
./start-jupyter.sh
```
We have just started Jupyter server in tmux session. As soon as we did it, we can close this SSH connection and all processes will retain.


## Amazon AMI
We don't want to set up Anaconda, XGBoost, SSH Config and other each time we start new instance. We want to preserve this state. For this purpose, we use Amazon AMI.

In your Amazon AWS Console, in the tab **Instances**, select your instance, click **Actions -> Image -> Create Image**. Name your image and click Create Image.

Next time you want to start instance, at the **Step 1** select tab **My AMIs** and choose your AMI.
