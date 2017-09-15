# OMG HERE GOES

you need a softlayer account.

* create a ssh keypair as cluster/ssh_key, cluster/ssh_key.pub
* upload the public key to softlayer
* update `create_sl_vms.yaml` to use your key and a suitable VLAN
* slipstream the softlayer pip into the cloud private deployer

```
$ docker build -t ibmcom/cfc-installer:1.1.0-sl .
```

* Install ansible and softlayer PIPs

```
$ pip install -r requirements.txt
```

* Create softlayer VMs

```
$ ansible-playbook playbooks/create_sl_vms.yml
```

* Once they're online prepare them:

```
$ ssh-add cluster/ssh_key
$ ansible-playbook -i hosts prepare_sl_vms.yaml -u root
```

* Once that's done you can deploy from our slipstreamed deployer:

```
$ docker run -e SL_USERNAME=XXXXX -e SL_API_KEY=YYYY -e LICENSE=accept --net=host --rm -t -v "$(pwd)/cluster":/installer/cluster ibmcom/cfc-installer:1.1.0-sl install
...
...
PLAY RECAP *********************************************************************
169.46.198.XXX             : ok=44   changed=22   unreachable=0    failed=0   
169.46.198.YYY             : ok=69   changed=39   unreachable=0    failed=0   
169.46.198.ZZZ             : ok=45   changed=22   unreachable=0    failed=0   


POST DEPLOY MESSAGE ************************************************************

UI URL is https://169.46.198.XXX:8443 , default username/password is admin/admin

```
