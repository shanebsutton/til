# Using Vagrant

From [vagrantup.com](https://www.vagrantup.com/intro) - "Vagrant is a tool for building and managing virtual machine environments in a single workflow."

It requires a [hypervisor](https://en.wikipedia.org/wiki/Hypervisor) to run the VMs, and VirtualBox appears to be the way to go...for local dev, at least.

After using VirtualBox in the past without Vagrant, I will say that this is a much easier way to manage a VM for learning/playing/testing.

Installation of the tools is pretty straightforward.  This [blog post](https://sloopstash.com/blog/how-to-build-vm-on-windows-10-using-virtualbox-vagrant-git-bash.html) was helpful in getting the tools installed (including Git Bash) and setting up an Ubuntu VM.

My use case was for following the demos on an ACG course, "Deploying Resources to GCP with Terraform".

With everything installed:

```bash
cd c:\code\courses
mkdir ACG-DeployingWithTerraform
cd ACG-DeployingWithTerraform
vagrant init centos/8
```

I made a directory for the course, and in that directory I used `vagrant init` to create a Vagrantfile.

The Vagrantfile was updated for the course as follows (with the default comments omitted):

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "centos/8"
  config.vm.hostname = "acg-terraform"
  config.vm.network "private_network", ip: "192.168.60.10"
  config.vm.provider "virtualbox" do |vb|
    # Customize the memory and cpu on the VM:
    vb.memory = "1024"
    vb.cpus = 2
  end
end
```

Starting the vm is simple:

```bash
vagrant up
```

It has to download the OS on the initial launch.  After that:

```bash
# SSH to VM
vagrant ssh
# Stop VM
vagrant halt
# Delete VM
vagrant destroy
```

The host home directory (where .vagrant is located on your machine) will auto sync with the guest VM every time `vagrant up` or `vagrant reload` are run.

To find on the VM: `cd /vagrant`

## References

- [Vagrant Docs](https://www.vagrantup.com/docs)
- [VirtualBox](https://www.virtualbox.org/)
- [How to build VM on Windows 10 using VirtualBox, Vagrant, and Git Bash - blog post](https://sloopstash.com/blog/how-to-build-vm-on-windows-10-using-virtualbox-vagrant-git-bash.html)
