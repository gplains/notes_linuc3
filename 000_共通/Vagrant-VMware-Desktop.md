# VMware DesktopでのVagrant使用

VMware Workstation(Desktop Hypervisor)向けのVagrantプラグインがMPLになった(2025.2現在)

## 一般的な流れ

- Vagrantをダウンロード
- Vagrant のVMware Desktop 用プラグインを入手
  https://developer.hashicorp.com/vagrant/install/vmware
- コマンドラインを適当に叩く

```
mkdir c:\vagrant ; cd c:\vagrant
mkdir alma-9 ; cd alma-9
vagrant init almalinux/9
vagrant up
```

## VMware Workstation にVMが表示されない

Vagrantfileでgui有効にする

```
config.vm.provider "vmware_desktop" do |vb|
  vb.gui = true
end
```

## 仮想ネットワークエディタ

C:\Program Files (x86)\VMware\VMware Workstation\vmnetcfg.exe

## VMに複数NICを持たせるサンプル

```
config.vm.define "lvs" do |machine|
  machine.vm.network "public_network", ip: "192.168.11.112"
  machine.vm.network "private_network", ip: "192.168.12.101" 
  machine.vm.hostname = "machine-a"
  machine.vm.provision "shell", inline: <<-SHELL
      sudo yum -y install ipvsadm keepalived
      sudo echo 'net.ipv4.ip_forward = 1' >> /etc/sysctl.conf
      sudo sysctl -p
      sudo systemctl enable ipvsadm 
      sudo touch /etc/sysconfig/ipvsadm 
      sudo systemctl start ipvsadm 
  SHELL
end
```