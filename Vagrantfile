# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing
VAGRANTFILE_API_VERSION = '2'

PM_ROOT = File.dirname(__FILE__)
PM_CONFIG = File.expand_path(File.join(File.dirname(__FILE__), 'config.json'))
require_relative File.join(PM_ROOT, 'lib', 'ruby', 'playa_settings')
pmconf = PlayaSettings.new(PM_CONFIG)

# #############################################################################
# Vagrant VM Definitions
# #############################################################################

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.provider :vmware_fusion do |fusion, override|
    fusion.name = pmconf.box_name

    # Every Vagrant virtual environment requires a box to build off of.
    override.vm.box = pmconf.box_name

    # There are two levels of caching here.
    # 1. If pmconf.box_local is set, then the file referenced by pmconf.box_url
    #    was found in the Packer build path (packer/builds/*.box) and Vagrant's
    #    vm.box_url is set to that path. To force retrieving the box
    #    from the URL again, simply remove the Packer builds directory.
    # 2. Vagrant only retrieves box images from vm.box_url if it does not have
    #    a local copy in ~/.vagrant.d/boxes/$BOX_NAME. These can be removed
    #    with the command: "vagrant box remove $BOX_NAME"
    override.vm.box_url = pmconf.box_local ? pmconf.box_local : pmconf.box_url

    # Create a private network, which allows host-only access to the machine
    # using a specific IP.
    override.vm.network :private_network, ip: pmconf.ip_address

    # If true, then any SSH connections made will enable agent forwarding.
    # Default value: false
    override.ssh.forward_agent = true

    # Note: You'll want a decent amount of memory for your mesos master/slave
    # VM. The strict minimum, at least while the VM is provisioned, is the
    # amount necessary to compile mesos and the jenkins plugin. 2048m+ is
    # recommended.  The CPU count can be lowered, but you may run into issues
    # running the Jenkins Mesos Framework if you do so.
    fusion.customize ['modifyvm', :id, '--memory', pmconf.vm_ram]
    fusion.customize ['modifyvm', :id, '--cpus',   pmconf.vm_cpus]

    # Make the project root available to the guest VM.
    # override.vm.synced_folder '.', '/vagrant'

    # Only provision if explicitly request with 'provision' or 'up --provision'
    if ARGV.any? { |arg| arg =~ /^(--)?provision$/ }
      override.vm.provision :shell do |shell|
        shell.path = 'lib/scripts/common/mesosflexinstall'
        arg_array = ['--slave-hostname', pmconf.ip_address]
        # Using an array for shell args requires Vagrant 1.4.0+
        # TODO: Set as array directly when Vagrant 1.3 support is dropped
        shell.args = arg_array.join(' ')
      end
    end

  end

end
