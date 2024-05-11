# NDFC Ansible - IPFM Fabric Example

This repo includes a very basic example for building an IP Fabric for Media fabric using the DCNN Ansible Collection.

You will want (for now, as of 2024-05-10) to switch into the dcnm_fabric_ipfm branch.  I've opened a PR to merge IPFM support into the dcnm_fabric module, but it will take a couple weeks for this to happen.

If you're already familiar with Ansible and it's installed and set up already, then the following should work:

1. Install the DCNM Collection repo

```bash
cd $ANSIBLE_COLLECTIONS_PATH/ansible_collections
# If not already present...
mkdir cisco
cd cisco
git clone https://github.com/CiscoDevNet/ansible-dcnm.git
mv ansible-dcnm dcnm
```

2. Switch to the dcnm_fabric_ipfm branch (this will not be needed once this branch is merged into the main branch in a few weeks).

```bash
cd $ANSIBLE_COLLECTIONS_PATH/ansible_collections/cisco/dcnm
git switch dcnm_fabric_ipfm
```

3. Install this repo and modify for your NDFC controller and NX-OS switches.  See steps 5 and 6 further below.


If you're starting from scratch, then something like the steps below will get you mostly there, assuming a ``bash`` environment. Modify to taste, and as needed for other environments.

1. Clone the Ansible, Ansible Netcommon, and DCNM Collections repos.

```bash
export ANSIBLE_COLLECTIONS_PATH=$HOME/ansible
mkdir $ANSIBLE_COLLECTIONS_PATH
mkdir $ANSIBLE_COLLECTIONS_PATH/ansible_collections
cd $ANSIBLE_COLLECTIONS_PATH/ansible_collections
git clone https://github.com/ansible/ansible.git
git clone https://github.com/ansible-collections/ansible.netcommon.git
mkdir $ANSIBLE_COLLECTIONS_PATH/ansible_collections/cisco
cd $ANSIBLE_COLLECTIONS_PATH/ansible_collections/cisco
git clone https://github.com/CiscoDevNet/ansible-dcnm.git
mv ansible-dcnm dcnm
cd dcnm
# switch to the dcnm_fabric_ipfm branch (will not be needed once this is merged in a few weeks...)
git switch dcnm_fabric_ipfm
```

2. Create and activate a python virtual environment.

```bash
cd $HOME
python -m venv py
source $HOME/py/bin/activate
```

3. Install ansible in this environment

```bash
pip install ansible
```

4. Clone this repo into some directory (we'll clone it directly off $HOME)

```bash
cd $HOME
git clone https://github.com/allenrobel/ndfc-ipfm-fabric.git
cd $HOME/ndfc-ipfm-fabric
```

5. Edit the ./inventory/hosts/hosts file to modify the ``ansible_host`` to point to your NDFC controller IP address.

```bash
vim $HOME/ndfc-ipfm-fabric/inventory/hosts/hosts
```

The file includes everything you need to modify:


```yaml
---
ndfc:
  hosts:
    ndfc1:
      ansible_host: 10.1.1.1
```

6. Edit the ./inventory/group_vars/ndfc/params.yaml file to add your NDFC controller username/password, nxos switch usernames/passwords, and nxos switch IP addresses.  The file included includes everything you need to modify.

```bash
vim $HOME/ndfc-ipfm-fabric/group_vars/ndfc/params.yaml
```

```yaml
ansible_connection: ansible.netcommon.httpapi
ansible_network_os: cisco.dcnm.dcnm
ansible_httpapi_validate_certs: no
ansible_httpapi_use_ssl: yes
# username for NDFC controller
ansible_user: admin
# password for NDFC controller
ansible_password: myAnsiblePassword
nxos_username: admin
nxos_password: myNxosPassword
nxos_auth_protocol: MD5
leaf_1_ipv4: 10.1.2.1
leaf_2_ipv4: 10.1.2.2
spine_1_ipv4: 10.1.3.1
spine_2_ipv4: 10.1.3.2
```

7. Use ansible-playbook to create the fabric, with 2x spine and 2x leaf.

```bash
cd $HOME/ndfc-ipfm-fabric
ansible-playbook ipfm-fabric.yaml -i inventory
```

