# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  
  config.vm.box = 'dummy'

  config.vm.provider :aws do |aws, override|
    
    aws.access_key_id = ENV['AWS_ACCESS_KEY_ID']
    aws.secret_access_key = ENV['AWS_SECRET_ACCESS_KEY']
    aws.keypair_name = ENV['AWS_KEYPAIR_NAME']
    
    #Coming soon..
    #aws.associate_public_ip = true
    #aws.subnet_id = '' #public subnet of your VPC
    
    aws.security_groups = ''
    aws.region = ''

    aws.ami = 'ami-896c96fe'
    aws.instance_type = 't1.micro'

    override.ssh.username = ENV['AWS_SSH_USER']
    override.ssh.private_key_path = ENV['AWS_KEY_PATH']
  
  end

  config.vm.provision 'ansible' do |ansible|
    ansible.playbook = 'build/playbook.yml'
    ansible.verbose = 'vv'
  end

end
