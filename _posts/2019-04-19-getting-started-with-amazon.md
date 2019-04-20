---
layout: post
title: "Getting started with AWS"
date: 2019-04-19
---

This starts a series of posts about devops where we'll be leveraging AWS to solve some common situations that arise when running apps on the cloud. In this post we will go over a very simple set up: A web server hooked into a database for persistence. While very far from a real production situation this setup is a good starting place to learn about AWS fundamental tools. The concepts I'll be going over today include:

- Amazon Regions
- EC2 instance
- AMI
- Security Groups
- SSH
- Manual Dependencies installation

## The Plan
The simplest (but not necessarily most recommended) way to get a server running on amazon can be achieved with the following steps:
- Choose an Amazon Region
- Create an EC2 instance.
- [Ssh](https://searchsecurity.techtarget.com/definition/Secure-Shell) and install software (dependencies, database, etc)
- Test the app

We'll go through each of these steps and explain all the new terms along the way. Throughout this tutorial I'll be using a very simple url rails 5 url shortener [app](https://github.com/eduardopoleo/aws_tiny_url). We'll be using this repo in subsequent steps so feel free to check it out.

### Choose an Amazon Region.
Amazon has data-centers located on different parts of the [world](https://aws.amazon.com/about-aws/global-infrastructure/) which are known as [regions](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html). Each region contains a certain number of availability zones (AZ) where ultimately the infrastructure lives. While each region is completely independent from each other, AZs from within the same region can communicate with each other through [low latency links](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-regions-availability-zones) nevertheless, they are physically and resource-wise (power supply, etc) isolated from one another. This set up allows AWS to efficiently serve users across the globe while providing enough redundancy to protect against unfortunate events (e.g power outages, natural disasters, etc).

Choosing an AWS region is easy just go to the top right corner and choose one according to your location.

![Selecting Regions](https://s3.us-east-2.amazonaws.com/eduardo-tutorial-videos/ec2/selecting_region.gif)

As a rule of thumb you should pick a region that it's closest to where you're users are located to minimize latency. In my particular case that would be US EAST. 

### Create an EC2 instance
EC2 stands for Elastic Cloud Computing. Despite recent development on serverless technology
EC2 are still Amazon's bread and butter. They consist on virtual servers of different capacities that developers can "rent" to run their applications on the cloud. When creating an EC2 instance AWS takes you through a wizard with a few different steps, we'll be covering these next. To launch the wizard we just need to find the EC2 service and click the "Launch Instance" button.

![EC2 Wizard](https://s3.us-east-2.amazonaws.com/eduardo-tutorial-videos/ec2/selecting_AMI.gif)

### STEP 1: Choose AMI

We're then faced with the first step of the process where we'll be choosing an Amazon Machine Image ([AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html)). AMIs are basically templates with pre-installed software ready for developers to use. AMI can be free or paid and they can be provided by AWS or third parties. Also organization can decide to create their own images for private use when their needs become very specific.

For this exercise we'll be using an Ubuntu server which can easily support our simple rails app. To find this specific AMI just paste "Ubuntu Server 16.04 LTS" on the AMI search bar and select the free-tier option. If you're following along please make sure you choose the instance with id ami-0653e888ec96eab9b.

#### Step 2: Choose an Instance Type

Instance types basically determine the specs and performance of your machine. AWS provides several "series" or tiers with specs suitable for different kind of tasks. You can find more info about these tiers in [here](https://aws.amazon.com/ec2/instance-types/). In general, T and M series are good all-purpose machines suitable for small-mid web applications. We'll be choosing t2.micro as it's free-tier eligible and will do for the purpose of this tutorial.

#### Step 3: Configure Instance Details
We can further tweak our instance configuration but for now we'll leave this options unchanged.

#### Step 4: Add Storage
The default amount (8Gib) should be more than enough for our instance storage.

#### Step 5: Add Tags
[Tagging](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html?icmpid=doc) allows to attach arbitrary meta-data to our AWS resources. We can then use them to easily manage our resources (e.g search, classify them, etc). Tags in general have not specific semantic meaning to AWS they are just interpreted as key-value string pairs used to label resources at the developers discretion. We do not need tagging since our setup is going to be very minimal.

#### Step 6: Configure Security Group
[Security Groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html) They basically work as firewalls with specific rules to white-list the kind of request we'll let into our server. We will create a new security group and give it any name and description we want, (e.g aws-security-group and Security group for AWS tutorial) then we can proceed to add rules. For our particular case we'll require 2 types of interaction with the server, ssh and http. 
- SSH 
The ssh connection will allow us to login into our server and install the additional software required to run our app. There should be a SSH rule added by default, if no just click add rule and select SSH on the type drop-down. For the source we'll be selecting "Custom" 0.0.0.0/0 will basically allows us to ssh into the server from anywhere in the world as long as we have the correct permission.
- HTTP
We then have to define a HTTP connection and open it to the world. For this we just simply select add a rule with: 
Type "Custom TCP Rule"
Port Range: 3000 (since we will starting our server on that port)
Source: Custom 0.0.0.0/0 (which means that our app should be reachable from anywhere in the world)

#### Step 7: Key Pairs
On the final step of wizard we're taken to the review page where we can just go and hit "Launch". We're then be prompted with a modal to select a key pair. Select "Create a new pair" and give a name such as 'aws-tutorial-key-pair' and click "Download Key Pair". It's important that you keep this key secure and at easy accessible directory on you computer as we'll be needing it very shortly to ssh into our servers. Finally, just click on "Launch Instances".

### Ssh and install software
After launching our instance we can access our instance panel by searching for EC2 on the services tab and going to our running instances. From there we can pull up the connect instructions for our instance.

![Ssh](https://s3.us-east-2.amazonaws.com/eduardo-tutorial-videos/ec2/ec2_panel_ssh.gif)

The modal present 2 sets of instructions. The first consist to authorize your key so navigate to the directory where you stored the previously downloaded SSH key and execute:

`chmod 400 aws-tutorial-key-pair.pem`

then on that same directory we can just run the ssh command as shown in the instructions modal which should look like something like this

`ssh -i "aws-tutorial-key-pair.pem" ubuntu@<YOUR_INSTANCE_PUBLIC_DNS>`

Please for the rest of the article and if you're following along feel free to replace the <YOUR_INSTANCE_PUBLIC_DNS> by your instance DNS which you can find as shown below:

![Public DNS](https://s3.us-east-2.amazonaws.com/eduardo-tutorial-videos/ec2/instace_public_dns.gif)

Now that we've ssh into our instance we then proceed to install all the software required to have our application running.

```bash
# Installing OS required dependencies
sudo apt update
sudo apt install -y curl libssl-dev libreadline-dev zlib1g-dev autoconf bison build-essential libyaml-dev libreadline-dev libncurses5-dev libffi-dev libgdbm-dev

# Installing the ruby version manager rbenv 
curl -sL https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-installer | bash -

echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
source ~/.bashrc

# Installing ruby
rbenv install 2.5.1
rbenv global 2.5.1

# Installing the node runtime (required for rails)
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt install -y nodejs
sudo apt install -y gcc g++ make

# Installing Postgres
sudo apt install -y postgresql postgresql-contrib
sudo apt-get -y install postgresql-server-dev-9.5
sudo -u postgres createuser ubuntu
sudo -u postgres createdb tiny_url_production

# cloning and running our project
git clone https://github.com/eduardopoleo/aws_tiny_url.git
cd aws_tiny_url
gem install bundler -v '< 2.0'
gem install rails -v 5.2.1

bundle install
RAILS_ENV=production rake db:migrate
RAILS_ENV=production rake db:seed
```

Finally, we run the server with 

```bash
RAILS_ENV=production rails server -b <YOUR_INSTANCE_PUBLIC_DNS> -p 3000
```

As mentioned at the beginning of the post our app is a very simple url shortener. You can provide an url and it'll return a shorten version of it in the form of a slug. We can give it a try by executing a post request with curl from your local machine:

```bash
curl --header "Content-Type: application/json" \
  --request POST \
  --data '{"original_url":"https://unix.stackexchange.com/questions/115838/what-is-the-right-file-permission-for-a-pem-file-to-ssh-and-scp"}' \
  <YOUR_INSTANCE_PUBLIC_DNS>:3000/url -v

#=> The server should response with something along the lies of {"url":"<YOUR_INSTANCE_PUBLIC_DNS>:3000/784b81d"}
```

Now if you navigate to the returned url you should be redirected to the original url.

This concludes the first post on this AWS series, we managed to get a simple rails app up and running and learned lots of AWS concepts along the way. That being said, this set up is less than ideal to run production apps for many reasons:

- `<YOUR_INSTANCE_PUBLIC_DNS>:3000` is not really an enticing url for your service
- Our setup server set up is very tedious and error prone, specifically the part where we install dependencies
- We are not leveraging amazon managed solution like RDS to handle our database persistence
- Our server won't scale if many requests required to be processed 
- Our "deployment" process is less than ideal having to ssh and git pull to a public repo.

On the next post we'll be going into how to hook up our app to Amazons [RDS](https://aws.amazon.com/rds/) and how to improve performance using [ElasticCache](https://aws.amazon.com/elasticache/).

Thanks for reading