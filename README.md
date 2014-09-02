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

## Apache MySQLのセットアップ
```
bundle exec knife cookbook create apache -o site-cookbooks
bundle exec knife cookbook create mysql -o site-cookbooks
```

nodes/webdb.json
```
{
  "run_list":[
    "recipe[dstat]",
    "recipe[apache]",
    "recipe[mysql]"
  ],
  "automatic": {
    "ipaddress": "webdb"
  }
}

site-cookbooks/apache/recipes/default.rb
```
package "httpd" do
  action :install
end

service "httpd" do
  action [ :enable, :start ]
end
```

site-cookbooks/mysql/recipes/default.rb
```
package "mysql-server" do
  action :install
end

service "mysqld" do
  action [ :enable, :start ]
end
```

```
$ bundle exec knife solo cook webdb
```

```
$ vagrant ssh
$ ps auxw | egrep "(httpd|mysqld)"
```

```
$ cp /etc/httpd/conf/httpd.conf /vagrant/site-cookbooks/apache/templates/default/httpd.conf.erb
```
site-cookbooks/apache/recipes/default.rb
```
template "httpd.conf" do
  path "/etc/httpd/conf/httpd.conf"
  owner "root"
  group "root"
  mode 0644
  notifies :reload, 'service[httpd]'
end
```

```
$ vagrant destroy -f
$ vagrant up
$ vagrant ssh
$ bundle exec knife solo bootstrap webdb
```

なぜか`$ bundle exec knife solo cook webdb`すると時々Segmentation faultが発生する
```
/vendor/bundle/ruby/1.9.1/gems/celluloid-0.16.0.pre3/lib/celluloid/tasks.rb:63: [BUG] Segmentation fault
```

通常は
```
Starting Chef Client, version 11.14.6
Compiling Cookbooks...
Converging 6 resources
Recipe: dstat::default
  * package[dstat] action install (up to date)
Recipe: apache::default
  * package[httpd] action install (up to date)
  * service[httpd] action enable (up to date)
  * service[httpd] action start (up to date)
  * template[httpd.conf] action create (up to date)
Recipe: mysql::default
  * package[mysql-server] action install (up to date)
  * service[mysqld] action enable (up to date)
  * service[mysqld] action start (up to date)

Running handlers:
Running handlers complete
Chef Client finished, 0/8 resources updated in 4.828610598 seconds
```