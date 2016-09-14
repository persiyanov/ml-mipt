This is a newest version of Amazon How-to. Now, it's more easier to install all environment, because you will just build docker image with latest libraries and run it.
[Old version](https://github.com/persiyanov/ml-mipt/edit/master/amazon-howto/old_howto.md)

# Note
Section "AWS Console and launching EC2 Instance." remains the same. You should follow 7 steps to get your EC2 instance.

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
