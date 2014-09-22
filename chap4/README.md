


コミニティクックブックを利用
=====================

検証環境構築
--------------------------
### vagrant 作成
```
$ vagrant box add opscode-centos-6.5 http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_centos-6.5_chef-provisionerless.box

$ vagrant init opscode-centos-6.5
```
### プライベートネットワーク設定
Vagrantfile
```
config.vm.network "private_network", ip: "192.168.33.10"
```
```
$ vagrant up
```
### SSH設定
```
$ vagrant ssh-config --host wedb >> ~/.ssh/config
```

gemでkife-soloインストール
------------------
```
$ bundle init
```

Gemfile
```
# A sample Gemfile
source "https://rubygems.org"

gem "knife-solo"
gem "berkshelf"
```

```
$ bundle install --path vendor/bundle
```

knife-solo でChef Soloをインストール
--------------
```
$ bundle exec knife solo init .
$ bundle exec knife solo bootstrap webdb
```
確認
```
$ ssh webdb
[vagrant@localhost ~]$ chef-solo -v
Chef: 11.16.2
```

クックブック検索
--------------
```
$ bundle exec knife cookbook site search apache2
```
 
 Berkshelf でクックブックをインストール
 ---------------
 Berkshelf
 ```
 site :opscode

 cookbook 'yum-epel'
 cookbook 'apache2'
 cookbook 'mysql'
 ```
 ```
 $ bundle exec berks
 ```

 ```
 DEPRECATED: Your Berksfile contains a site location pointing to the Opscode Community Site (site :opscode). Site locations have been replaced by the source location. Change this to: 'source "https://supermarket.getchef.com"' to remove this warning. For more information visit https://github.com/berkshelf/berkshelf/wiki/deprecated-locations
 Resolving cookbook dependencies...
 Fetching cookbook index from https://supermarket.getchef.com...
 Users/hiro/Documents/projects/chef-practical-guide/vendor/bundle/ruby/1.9.1/gems/celluloid-0.16.0/lib/celluloid/tasks.rb:63: [BUG] Segmentation fault
 ruby 1.9.3p362 (2012-12-25 revision 38607) [x86_64-darwin12.2.1]
 ```

 ```site``` -> ```source "https://supermarket.getchef.com"```

 ```
 source "https://supermarket.getchef.com"

 cookbook 'yum-epel'
 cookbook 'apache2'
 cookbook 'mysql'
 ```

 再実行
 ```
 $ rm Berkshelf.lock
 $ bundle exec berks
 Resolving cookbook dependencies...
 Fetching cookbook index from https://supermarket.getchef.com...
 Using apache2 (2.0.0)
 Using iptables (0.14.0)
 Using logrotate (1.7.0)
 Using mysql (5.5.2)
 Using pacman (1.1.1)
 Using yum (3.3.2)
 Using yum-epel (0.5.1)
 Using yum-mysql-community (0.1.10)
 ```

 コミュニティクックブックを使う
 ---------
 nodes/webdb.json
 ```
 {
     "run_list": [
         "recipe[yum-epel]"
           ],
             "automatic": {
                   "ipaddress": "webdb"
                     }
 }
 ```

 ```
 $ bundle exec knife solo cook webdb
 ```
 エラー。。。




