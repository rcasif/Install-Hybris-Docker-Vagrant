# -*- mode: ruby -*-
# vi: set ft=ruby :


   $script1 = <<-SCRIPT
		
		echo "Installing docker.."
		yum install wget -y
		tee /etc/yum.repos.d/docker.repo <<-'EOF'
		[dockerrepo]
		name=Docker Repository
		baseurl=https://yum.dockerproject.org/repo/main/centos/$releasever/
		enabled=1
		gpgcheck=1
		gpgkey=https://yum.dockerproject.org/gpg
		EOF

		yum install -y docker-engine
		service docker start
		usermod -aG docker $(whoami)
		chkconfig docker on
		
  SCRIPT
  
	  $script2 = <<-SCRIPT

			yum update -y
			echo "Pulling mysql image for hybris.."
			docker pull rcasif/hybrisb2c-mysql:v1

			echo "Runing mysql container for hybris.."
			docker run -idt -p 3307:3306 --name hybris-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d rcasif/hybrisb2c:v1

			echo "pull Hybris image.."
			docker pull rcasif/hybrisb2c:v1

			echo "Run Hybris container.."
			docker run -itd -p 9001:9001 -p 9002:9002 -p 8983:8983 -p 3306:3306 rcasif/hybris:v1
	  
	  SCRIPT

  Vagrant.configure("2") do |config|
 
  config.vm.box = "centos/7"

  config.vm.provider "virtualbox" do |vb|
      vb.name = "hybris-vagrant-vm"
      vb.memory = "4096" 
  end 

  config.vm.network :private_network, ip: '192.168.17.27'  
  #Open Hybris's default ports 9001 and 9002
  config.vm.network "forwarded_port", guest: 9001, host: 9001, host_ip: "localhost"
  config.vm.network "forwarded_port", guest: 9002, host: 9002, host_ip: "localhost" 
  
  config.ssh.forward_agent = true

  #set up the synch folder between the host and the guest
  config.vm.synced_folder ".", "/vagrant", type: "virtualbox"

	#The primary shell script that installs SAP Hybris and its dependencies
  config.vm.provision "shell", path: $script1, privileged: false 
  config.vm.provision "shell", path: $script2, privileged: false 
  

  config.vm.post_up_message = "
              
              ###############################
              #      Congratulations!       #
              #      Hybris has been        #
              #   successfully installed    #
              ###############################                           
                    HybrisArchitect.com                   
  "

end
