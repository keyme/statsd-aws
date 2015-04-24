VAGRANTFILE_API_VERSION = '2'
CONFIG = JSON.parse(File.read('config.json'))

def setup(config, env)
  config.vm.box = 'dummy'
  config.omnibus.chef_version = :latest
  config.vm.box_url = 'https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box'

  config.vm.provider :aws do |aws, override|
    aws.ami = 'ami-d017b2b8'
    aws.region = 'us-east-1'
    aws.instance_type = 'm3.medium'
    aws.security_groups = %w(statsd)
    aws.tags = { :Name => "#{env}-statsd" }

    aws.keypair_name = CONFIG["aws"]["keypair"]
    aws.access_key_id = CONFIG["aws"]["access_key_id"]
    aws.secret_access_key = CONFIG["aws"]["secret_access_key"]

    override.ssh.username = 'ubuntu'
    override.ssh.private_key_path = CONFIG["private_key_path"]
  end

  config.berkshelf.enabled = true
  config.vm.provision :chef_solo do |chef|
    chef.add_recipe 'apt'
    chef.add_recipe 'statsd'

    chef.json = {
      statsd: CONFIG["environments"][env]
    }
  end
end

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  CONFIG["environments"].keys.each do |env|
    config.vm.define env do |env_config|
      setup(env_config, env)
    end
  end
end
