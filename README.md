chef-practical-guide
===================

## Chap.2

### 2.2
```
vagrant box add opscode-centos-6.5 http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_centos-6.5_chef-provisionerless.box

vagrant init opscode-centos-6.5
vagrant up

vagrant ssh-config --host webdb >> ~/.ssh/config
```

### 2.3
```
curl -L https://www.opscode.com/chef/install.sh | sudo bash
```

### 2.4
```
sudo knife cookbook create hello -o /var/chef/cookbooks

sudo chef-solo -o hello
```

アラート

* * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
SSL validation of HTTPS requests is disabled. HTTPS connections are still
encrypted, but chef is not able to detect forged replies or man in the middle
attacks.

To fix this issue add an entry like this to your configuration file:

```
  # Verify all HTTPS connections (recommended)
  ssl_verify_mode :verify_peer

  # OR, Verify only connections to chef-server
  verify_api_cert true
```

To check your SSL configuration, or troubleshoot errors, you can use the
`knife ssl check` command like so:

```
  knife ssl check -c /etc/chef/solo.rb
```

* * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *

下記を追加
```
sudo mkdir /etc/chef
sudo vi /etc/chef/solo.rb
```

* /etc/chef/solo.rb
```
# Verify all HTTPS connections (recommended)
ssl_verify_mode :verify_peer
```
