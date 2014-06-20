Deploying Django on AWS (Ubuntu)
======

I've put this repository together with the intention that it can be used as a teach yourself tutorial for deploying Django with Ansible.  Have fun deploying and please feel free to contribute if you think it can be improved.

I've mainly borrowed from or been influenced by this [repo](https://github.com/jcalazan/ansible-django-stack) which was inspired by this excellent [blogpost](http://michal.karzynski.pl/blog/2013/06/09/django-nginx-gunicorn-virtualenv-supervisor/).

This is what we'll be doing:

1. Create an EC2 Ubuntu server using [Vagrant](http://www.vagrantup.com/)
2. Provison the server using [Ansible](http://www.ansible.com/home)
3. Optionally create a RDS instance
4. Deploy your Django project from Git and serve it with this stack:
  * [Gunicorn](http://gunicorn.org/)
  * [Nginx](http://nginx.org/)
  * [Supervisord](http://supervisord.org/)
  * [Memcached](http://memcached.org/)
  * [Virtualenv](http://virtualenv.readthedocs.org/en/latest/)  

Once you've configured your enviroment and Ansible variables you'll be able to do *all* this by simply typing:

`vagrant up --provider=aws`

Thereafter, you'll be able to make ongoing deployments and updates using Ansible.

##AWS credentials

First of all you'll need to get the following details from your AWS account:

  * ACCESS_KEY_ID
  * SECRET_ACCESS_KEY

Next, if you haven't already, you'll need to generate a keypair and configure the security groups and subnets for your VPC.  Having done all this, you should go to the file called 'local_envs' in this directory and fill in all your information.  The database variables in this file are intended for your local database installation.

Finally, install [Vagrant](https://docs.vagrantup.com/v2/installation/) and this [AWS provider](https://github.com/mitchellh/vagrant-aws) for Vagrant

At this stage you should be able to create an EC2 instance via Vagrant.

To verify, source your variables in the 'envs' file and run the following:

`vagrant up --provider=aws`
    
`vagrant ssh` to take a look around, then `exit` to leave

Back on your host you can checkout your server details with:
    `vagrant ssh-config`

And finally clean up with:
    `vagrant destroy`

Should Vagrant fail to complete your provisioning, you can always just run Ansible picking up from where you left off.  

`ansible-playbook build/playbook.yml -i build/inventory/hosts  --start-at-task='my task'`

To do this you'll need a valid host IP in your inventory and also be sure to run ansible-playbook from the same directory as where 'ansible.cfg' is located.  Alternatively you can move this file to a system directory where Ansible expects it to be.

##Project configuration

Start by opening the 'playbook.yml' file and providing values for all the variables listed under 'vars'.  The application will be deployed and owned by 'application_user'.  You'll need to provide their password hash by running the following command:

`python -c "from passlib.hash import sha512_crypt; print sha512_crypt.encrypt('your-password')"`

##SSH agent forwarding and cloning your Git repository

You might have some issues with Ansible timing out or giving a public key denied errors when trying to connect to your private repository.  [This guide](https://help.github.com/articles/using-ssh-agent-forwarding) is pretty useful for troubleshooting these errors.

##Variable management and Ansible Vault

Ansible Vault allows you to easily encrypt .yml files which you'll want to commit version control.  The group_vars directory is a good place to organise your encrypted variable files.

`ansible-vault encrypt foo.yml bar.yml baz.yml #to encrypt existing files`

`ansible-vault create foo.yml #to create a new encrypted file`

`ansible-vault edit foo.yml`

To run a playbook with encrypted files:

`ansible-playbook site.yml --ask-vault-pass`

##Ongoing deployments

Use tags to run a bespoke collection of tasks on your hosts

`ansible-playbook build/playbook.yml -i build/inventory/hosts  --tags='deploy'`

##To do
Dynamic inventories, Development, Staging, Live setups



