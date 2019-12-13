This template allows you to monitor a standalone ESXi host without any kind of host discovery that is required by zabbix official vmware template.
The template is the same as Template Virt VMware Hypervisor but with all {HOST.HOST} variables replaced by {$UUID}

If you have not already done so, you need to activate zabbix server vmware polling in your zabbix server configuration file (/etc/zabbix/zabbix_server.conf).
StartVMwareCollectors should be set to a value depending on the number of host you wish to monitor: servicenum < StartVMwareCollectors < (servicenum * 2) where servicenum is the number of VMware services. 
If you wish to monitor 1 ESXi host, set StartVMwareCollectors to 2.

Import this template to your templates inventory.
Create an host for your ESXi hypervisor. You don't need any agent/ipmi/snmp interfaces.
Link the host to this template.
Creates 4 macros :
  {$URL}      -> https://esxi.hostname/sdk 
  {$USERNAME} -> any esxi user that have at least readonly access (recommended for obvious security reason)
  {$PASSWORD} -> the user's password
  {$UUID}     -> esxi host UUID

*How to obtain ESXi UUID*
0. You need to enable MOB vib as esxcli does not seems to provide the valid uuid for this.
1. enable MOB vib using one of these methods : 
    * Using vsphere client : Go to you're vsphere Advanced Settings, Config, Hostagent, plugins, solo, check "Config.HostAgent.plugins.solo.enableMob"
    * Using esxi console : `vim-cmd hostsvc/advopt/update Config.HostAgent.plugins.solo.enableMob bool true`
    * Using Powercli : `Get-VMHost #IPADDRESS | Set-VMHostAdvancedConfiguration -Name Config.HostAgent.plugins.solo.enableMob -Value True`
2. Browse to https://esxi.hostname/mob/?moid=ha-host&doPath=hardware.systemInfo
3. disable MOB vib again :
    * Using vsphere client : uncheck Config.HostAgent.plugins.solo.enableMob
    * Using esxi console : `vim-cmd hostsvc/advopt/update Config.HostAgent.plugins.solo.enableMob bool false`
    * Using Powercli : `Get-VMHost #IPADDRESS | Set-VMHostAdvancedConfiguration -Name Config.HostAgent.plugins.solo.enableMob -Value False`