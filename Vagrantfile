# -*- mode: ruby -*-
# vi: set ft=ruby :

$installXymon = <<SCRIPT
  Write-Host "Installing xymon Client"
  c:
  cd \\
  mkdir C:\\temp
#  (New-Object System.Net.WebClient).DownloadFile('https://raw.githubusercontent.com/poorleno1/xymonPS/master/installation_script.ps1', "c:\\temp\\installation_script.ps1")
#  Invoke-Expression "& `"c:\\temp\\installation_script.ps1`""
  Write-Host "Installing patches.."
#  c:\vagrant\Windows8.1-KB2966828-x64.msu /quiet
  Write-Host "Finished installing patches.."
 # Import-Module ServerManager 
 # Add-WindowsFeature PowerShell-ISE
 Write-Host "Installing .net 3.5"
 #Install-WindowsFeature Net-Framework-Core
 DISM /Online /Enable-Feature /FeatureName:NetFx3 /All
 Write-Host "Finished Installing .net 3.5"
 Write-Host "Configuring 500mb drive"
 get-disk | where {$_.size -eq 500mb -and $_.partitionstyle -eq 'raw'} | Initialize-Disk -PartitionStyle MBR -PassThru | New-Partition -AssignDriveLetter -UseMaximumSize | Format-Volume -FileSystem NTFS -NewFileSystemLabel "TempDisk" -Confirm:$false 
  Set-Partition -DriveLetter D -NewDriveLetter P 
 get-disk | where {$_.size -eq 11000mb -and $_.partitionstyle -eq 'raw'} | Initialize-Disk -PartitionStyle MBR -PassThru | New-Partition -AssignDriveLetter -UseMaximumSize | Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisk" -Confirm:$false 
 Set-Partition -DriveLetter D -NewDriveLetter S
 get-disk | where {$_.size -eq 2000mb -and $_.partitionstyle -eq 'raw'} | Initialize-Disk -PartitionStyle MBR -PassThru | New-Partition -AssignDriveLetter -UseMaximumSize | Format-Volume -FileSystem NTFS -NewFileSystemLabel "LogDisk" -Confirm:$false 
 Set-Partition -DriveLetter D -NewDriveLetter L
  get-disk | where {$_.size -eq 3000mb -and $_.partitionstyle -eq 'raw'} | Initialize-Disk -PartitionStyle MBR -PassThru | New-Partition -AssignDriveLetter -UseMaximumSize | Format-Volume -FileSystem NTFS -NewFileSystemLabel "BackupDisk" -Confirm:$false 
 Set-Partition -DriveLetter D -NewDriveLetter Z
 
 Write-Host "Finished configuring 500mb drive"
 Get-disk 
  #(New-Object System.Net.WebClient).DownloadFile('https://chocolatey.org/install.ps1', "c:\\temp\\install.ps1")
  #Invoke-Expression "& `"c:\\temp\\install.ps1`""
  #choco install -y git 
SCRIPT


# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "mwrock/Windows2012R2"
  #config.vm.name = "Windows2012mwrock_#{Time.now.getutc.to_i}"
  #config.vm.hostname = "Windows2012mwrock4"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: "192.168.33.18"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"
  
  # if Vagrant.has_plugin?("vagrant-proxyconf")
   # config.proxy.http     = "http://145.247.13.164:3128/"
   # config.proxy.https    = "http://145.247.13.164:3128/"
   # config.proxy.no_proxy = "localhost,127.0.0.1,.statoilfuelretail.com"
  # end
  
  config.vm.post_up_message = "The OS is up and running."

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
     vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
    vb.memory = "1024"
   
   # Creating SATA Controller and adding disks to that controller
	 file_to_disk4 = './tmp/backup_disk.vdi'
	unless File.exist?(file_to_disk4)
 	  vb.customize ['storagectl', :id, '--add','sata' ,'--name', 'SATA Controller',  '--controller', 'IntelAHCI',  '--portcount', '6', '--hostiocache', 'on' ]
      vb.customize ['createhd', '--filename', file_to_disk4, '--size', 3000] # size is in MB
    end
     vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 0, '--device', 0, '--type', 'hdd', '--medium', file_to_disk4] 
	
	file_to_disk = './tmp/temp_disk.vdi'
	unless File.exist?(file_to_disk)
      vb.customize ['createhd', '--filename', file_to_disk, '--size', 500] # size is in MB
    end
     vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', file_to_disk]
	 
	file_to_disk2 = './tmp/data_disk.vdi'
	unless File.exist?(file_to_disk2)
      vb.customize ['createhd', '--filename', file_to_disk2, '--size', 11000] # size is in MB
    end
     vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 2, '--device', 0, '--type', 'hdd', '--medium', file_to_disk2] 
	 
	file_to_disk3 = './tmp/log_disk.vdi'
	unless File.exist?(file_to_disk3)
      vb.customize ['createhd', '--filename', file_to_disk3, '--size', 2000] # size is in MB
    end
     vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 3, '--device', 0, '--type', 'hdd', '--medium', file_to_disk3] 

	
   end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   sudo apt-get update
  #   sudo apt-get install -y apache2
  # SHELL
  
  config.vm.provision "shell" do |s|
    s.inline = $installXymon
    # s.args = [GUEST_MODULE_DIR + "*"]
  end
end
