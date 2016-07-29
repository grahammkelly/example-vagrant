# Example Vagrant server

Configures a minimal Vagrant Ubuntu server.

Currently, this is configured on an explicit IP _`192.168.33.10`_.

The Vagrant file configures the new VM to accept login to the 'vagrant' account
using the creating user's default public key. This is done by copying the
public key from the `.ssh/id_rsa.pub` file and appending it to the `.ssh/authorixed_keys` 
file under the vagrant user. 

```ruby
  ...
  config.vm.provision "shell" do |s|
    ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip
    s.inline = <<-SHELL
        echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
    SHELL
  end
  ...
```

## Local system configuration


Due to the nature of Vagrant, you can bring up and destroy these VMs at will. If you've 
SSH'ed into the VM at all, the SSH fingerprint will then stop you logging into the 
box again once it was destroyed and re-created.

Additionally, as Vagrant creates (and assumes use of) the `vagrant` user as the default user
you probably want to login to that user by default (i.e. without needing to specify at the 
command line).

To do this, create (or edit) `~/.ssh/config` and make sure the following is included (_note, 
this assumes I'll always create VM's on the `192.169.33.0` subnet. I could also have specified the
exact IP._)

```
host 192.168.33.*
    StrictHostKeyChecking no
    UserKnownHostsFile=/dev/null
    User vagrant
```



