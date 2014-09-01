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

sudo vi /var/chef/cookbooks/hello/recipes/default.rb

```

```
log “Hello, World!”
```

```
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

## 2.5

```
sudo knife cookbook create dstat -o /var/chef/cookbooks
```

/var/chef/cookbooks/dstat/recipes/default.rb
```
package "dstat" do
  action :install
end
```

```
sudo chef-solo -o hello,dstat
```

```
rpm -q dstat
```


## 2.6
### install knife-solo, Berkshelf
```
bundle init
```

* Gemfile
```
gem "knife-solo"
gem "berkshelf"
```

```
bundle install --path vendor/bundle
```

### create repository with knife-solo
```
bundle exec knife solo init .
```

### install Chef Solo with knife-solo
```
bundle exec knife solo bootstrap webdb
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
  knife ssl check -c /home/vagrant/chef-solo/solo.rb
```

* * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *

下記の部分を変更

* /home/vagrant/chef-solo/solo.rb
```
ssl_verify_mode :verify_peer
```

### create cookbook
```
bundle exec knife cookbook create dstat -o site-cookbooks
```

site-cookbooks/dstat/recipes/default.rb
```
package "dstat" do
  action :install
end
```
