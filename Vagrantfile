# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.omnibus.chef_version = :latest

  config.vm.provision :chef_solo do |chef|
    chef.roles_path = "roles"
    chef.cookbooks_path = ["cookbooks", "site-cookbooks"]
    chef.add_role("jenkins_development")
    chef.json = {
      "java" => {
	"jdk_version" => '8',
	"accept_license_agreement" => true
     },
	"letsencrypt" => {
       "domains" => "jenkins.stderr.org",
       "contact" => "mailto:elijah.wright@gmail.com"
     },
  "jenkins" => {
	  "master" => {
	    "version" => "2.0",
	    "source" => "http://mirrors.jenkins-ci.org/war-rc/2.0/jenkins.war"
	  },
	  "install_method" => "war"
       }
     }
    # chef.add_role("huginn_production")
  end

  config.vm.provider :virtualbox do |vb, override|
    vb.memory = 4096
    vb.cpus = 3 
    override.vm.box = "ubuntu/wily64"
    if Vagrant.has_plugin?("vagrant-cachier")
      # Configure cached packages to be shared between instances of the same base box.
      # More info on http://fgrehm.viewdocs.io/vagrant-cachier/usage
      config.cache.scope = :box

      # OPTIONAL: If you are using VirtualBox, you might want to use that to enable
      # NFS for shared folders. This is also very useful for vagrant-libvirt if you
      # want bi-directional sync
      ##config.cache.synced_folder_opts = {
      ##  type: :nfs,
      ##  # The nolock option can be useful for an NFSv3 client that wants to avoid the
      ##  # NLM sideband protocol. Without this option, apt-get might hang if it tries
      ##  # to lock files needed for /var/cache/* operations. All of this can be avoided
      ##  # by using NFSv4 everywhere. Please note that the tcp option is not the default.
      ##  mount_options: ['rw', 'vers=3', 'tcp', 'nolock']
      ##}
      # For more information please check http://docs.vagrantup.com/v2/synced-folders/basic_usage.html
    end

    config.vm.network "public_network", bridge: 'en0: Wi-Fi (AirPort)'
    config.vm.network :forwarded_port, host: 8086, guest: 8080
  end

  config.vm.provider :parallels do |prl, override|
    override.vm.box = "parallels/ubuntu-12.04"
  end

  config.vm.provider :aws do |aws, override|
    aws.ami = ENV['AWS_AMI'] || "ami-828675f5"
    aws.region = ENV['AWS_REGION'] || "eu-west-1"
    aws.instance_type = "t1.micro"

    override.vm.box = "dummy"
    override.vm.box_url = "https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box"
    override.ssh.private_key_path = ENV["AWS_SSH_PRIVKEY"]
    override.ssh.username = ENV['AWS_SSH_USER'] || "ubuntu"

    aws.access_key_id = ENV["AWS_ACCESS_KEY_ID"]
    aws.secret_access_key = ENV["AWS_SECRET_ACCESS_KEY"]
    aws.keypair_name = ENV["AWS_KEYPAIR_NAME"]
    aws.security_groups = [ ENV["AWS_SECURITY_GROUP"] ]
  end
end

