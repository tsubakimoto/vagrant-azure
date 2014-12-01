# Vagrant Azure Provider

![Gem Version](https://badge.fury.io/rb/vagrant-azure.png)

This is a [Vagrant](http://www.vagrantup.com) 1.5.2+ plugin that adds [Microsoft Azure](https://azure.microsoft.com)
provider to Vagrant, allowing Vagrant to control and provision machines in Microsoft Azure.

**NOTE:** This plugin requires Vagrant 1.5.2+, and a Windows based workstation if creating Windows instances in Azure. Linux instances are supported with Linux, Mac OS X, and Windows workstations.

## Usage

Install Vagrant 1.5.2 or higher - [Download Vagrant](http://www.vagrantup.com/downloads.html)

Install the vagrant-azure plugin using the standard Vagrant 1.1+ installation methods. After installing the plugin, you can you can ```vagrant up``` and use ```azure``` provider. For example:

```
C:\> vagrant plugin install vagrant-azure
...
C:\> vagrant up --provider=azure
...
```

You'll need an ```azure``` box before you can do ```vagrant up``` though.

## Quick Start

You can use the dummy box and specify all the required details manually in the ```config.vm.provider``` block in your ```Vagrantfile```. Add the dummy box with the name you want:

```
C:\> vagrant box add azure https://github.com/msopentech/vagrant-azure/raw/master/dummy.box
...
```

Now edit your ```Vagrantfile``` as shown below and provide all the values as explained.

```ruby
Vagrant.configure('2') do |config|
	config.vm.box = 'azure'

	config.vm.provider :azure do |azure|
		azure.mgmt_certificate = 'YOUR AZURE MANAGEMENT CERTIFICATE'
		azure.mgmt_endpoint = 'https://management.core.windows.net'
		azure.subscription_id = 'YOUR AZURE SUBSCRIPTION ID'
		azure.storage_acct_name = 'NAME OF YOUR STORAGE ACCOUNT' # optional. A new one will be generated if not provided.

		azure.vm_image = 'NAME OF THE IMAGE TO USE'
		azure.vm_user = 'PROVIDE A USERNAME' # defaults to 'vagrant' if not provided
		azure.vm_password = 'PROVIDE A VALID PASSWORD' # min 8 characters. should contain a lower case letter, an uppercase letter, a number and a special character

		azure.vm_name = 'PROVIDE A NAME FOR YOUR VIRTUAL MACHINE' # max 15 characters. contains letters, number and hyphens. can start with letters and can end with letters and numbers
		azure.cloud_service_name = 'PROVIDE A NAME FOR YOUR CLOUD SERVICE' # same as vm_name. leave blank to auto-generate
		azure.deployment_name = 'PROVIDE A NAME FOR YOUR DEPLOYMENT' # defaults to cloud_service_name
		azure.vm_location = 'PROVIDE A LOCATION FOR VM' # e.g., West US
	    azure.ssh_private_key_file = 'PATH TO YOUR KEY FILE'
	    azure.ssh_certificate_file = 'PATH TO YOUR CERTIFICATE FILE'

	    # Provide the following values if creating a *Nix VM
	    azure.ssh_port = 'A VALID PUBLIC PORT'

	    # Provide the following values if creating a Windows VM
	    azure.winrm_transport = [ 'http', 'https' ] # this will open up winrm ports on both http (5985) and http (5986) ports
	    azure.winrm_https_port = 'A VALID PUBLIC PORT' # customize the winrm https port, instead of 5986
	    azure.winrm_http_port = 'A VALID PUBLIC PORT' # customize the winrm http port, insted of 5985

	    azure.tcp_endpoints = '3389:53389' # opens the Remote Desktop internal port that listens on public port 53389. Without this, you cannot RDP to a Windows VM.
	end

	config.ssh.username = 'YOUR USERNAME' # the one used to create the VM
	config.ssh.password = 'YOUR PASSWORD' # the one used to create the VM
end
```

Now you can run

```
C:\> vagrant up --provider=azure
```

This will bring up an Azure VM as per the configuration options set above.

You can now either SSH (if its a *Nix VM) using ```vagrant ssh```, or RDP (if its a Windows VM) using ```vagrant rdp```.

Normally, a lot of this options, e.g., ```vm_image```, will be embedded in a box file and you just have to provide minimal options in the ```Vagrantfile```. Since, we're using a dummy box, there are no pre-configured defaults.

## Azure Boxes

The vagrant-azure plugin provides the ability to use ```azure``` boxes with Vagrant. Please see the example box provided in [example_box/ directory](https://github.com/msopentech/vagrant-azure/tree/master/example_box) and follow the instructions there to build an ```azure``` box.

Please see [Vagrant Docs](http://docs.vagrantup.com/v2/) for more details.

## Configuration

The vagrant-azure provide exposes a few Azure specific configration options:

* `mgmt_certificate` - アカウントのAzureのポータルにアップロードされたあなたのAzureの管理証明書。
* `mgmt_endpoint` - Azureの管理エンドポイント。 `https://management.core.windows.net`
* `subscription_id` - AzureのサブスクリプションID。
* `storage_acct_name` - ストレージは、VMを作成するときに使用するアカウント。
* `vm_user` - VMを作成するときのユーザ名。 デフォルトは `vagrant` です。
* `vm_password` - VMに作成したユーザのパスワード。
* `vm_image` - VMを作るために使用するイメージ名。
* `vm_name` - 作成するVMの名前。
* `vm_size` - 作成するVMのサイズ。
* `cloud_service_name` - VMを作成するためのクラウドサービス名。
* `deployment_name` - クラウドサービスでの配置に名前をつけ、VMに追加します。
* `vm_location` - クラウドサービス、ストレージアカウントを作成する場所。
* `ssh_private_key_file` - SSHやHTTPS経由でWinRMを有効にする場合に使用する秘密鍵ファイル。
* `ssh_certificate_file` - SSHやHTTPS経由でWinRMを有効にする場合に使用する証明書ファイル。
* `ssh_port` - 別のパブリックポートに内部SSHポート22をマップする。
* `winrm_transport` - WinRMの有効/無効を設定します。指定できるのは `http` か `https` です。
* `winrm_https_port` - 別のパブリックポートに内部WinRMのhttpsポート5986をマップする。
* `winrm_http_port` - 別のパブリックポートに内部WinRMのhttpポート5985をマップする。
* `tcp_endpoints` - 開放する追加ポートです。例えば `80` と指定すれば `80` 番ポートを、 `80,3389:53389` と指定すれば `80` 番ポートと `3389` 番ポートを開放します。このとき `3389` 番ポートは `53389` 番ポートにマップされます。
* 

## New Commands for `azure` provider

The `azure` provider introduces the following new `vagrant` commands.

* `rdp` - To connect to a Windows VM using RDP. E.g.,
```
C:\> vagrant up --provider=azure
...
C:\> vagrant rdp
```


## Multi Machine
The options for multi machines are similar to Vagrant, please refer to the vagrant doc at http://docs.vagrantup.com/v2/multi-machine/index.html

