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
