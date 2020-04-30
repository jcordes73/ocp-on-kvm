# OCP on KVM

OCP on KVM eases the installation of OpenShift Container Platform on RHEL KVM Hosts

To learn more about installing OpenShift, visit [docs.openshift.com](https://docs.openshift.com)
and select the version of OpenShift you are using.

## Prerequisites

### Bastion Host

A server with Red Hat Enterprise Linux 8.1 installed.

The Bastion Host is hosting several needed service for the installation

- DNS server
- TFTP server
- Nexus 
- Apache HTTPD
- HAProxy

You need to install ansible and git:

	subscription-manager repos --enable=ansible-2.9-for-rhel-8-x86_64-rpms
	yum install -y ansible git

Clone this git repository

	git clone https://github.com/sa-mw-dach/ocp-on-kvm

### Bare-Metal servers

N bare-metal Servers for installing RHEL 8.1 with KVM. For an all-in-one installation this can also be the Bastion Host.

## Creating the Ansible inventory

You can find an example Ansible inventory [here](documentation/example-files/inventory-example)

## OpenShift on KVM installation

Create Ansible-Vault roles/kvm/vars/subscription-vault.yml

	rhn_user: <rhn-user>
	rhn_password: <rhn-password>

Encrypt the vault

	ansible-vault encrypt roles/kvm/vars/subscription-vault.yml

Create SSH-Key

	ssh-keygen -q -t rsa -f /root/.ssh/id_ocplabs -C "" -N ""

Execute playbook

	ansible-playbook -vvv --ask-vault-pass -i inventory playbook.yml > ansible.log 2>&1

## Post-Installation

Run the dditional post-installation playbook

        ansible-playbook -vvv -i inventory playbook-customise.yml

for

- SSO (using Keycloak)
- OpenShift Container Storage (OCS)
- Container Registry Storage (using OCS)

## Remove OCS

Run the following playbook to remove the OCS installation

	ansible-playbook -vvv -i inventory playbook-remove-ocs.yml

## License

OpenShift is licensed under the Apache Public License 2.0. The source code for this
program is [located on github](https://github.com/openshift/installer).
