# de.NBI cloud user meeting 2020 - CLUM 2020
Teaching aids / [slides for the de.NBI cloud user meeting 2020]()  

## Setup of BiBiGrid
### Clone repository and Build package
At first, we need to clone the repository of the [BiBiGrid-Tool](https://github.com/BiBiServ/bibigrid/).
~~~BASH
> git clone https://github.com/BiBiServ/bibigrid.git
> cd bibigrid
~~~
Then we can build the OpenStack package with the following command:

~~~BASH
> mvn -P openstack clean package
~~~
### Credentials
To have access to cloud provider options, you have to setup credentials to validate your OpenStack identity.  
Therefore you may source the OpenStack RC File v3 ¹ by clicking on your Account symbol on the right upper corner of the OpenStack Dashboard and download the *OpenStack RC File v3*.  

![Pop-Up Menü oben rechts](/popup_rc-file.png)  

After downloading you have to open up a terminal and source the downloaded file (e.g. bibiserv-openrc.sh) to get the credentials into your environment.  
~~~BASH
> source FILE.sh
~~~  
Note, that you have to source the file in every new terminal, if you want to access OpenStack options.  

¹ Alternatively, you can [set up a credentials file](https://github.com/BiBiServ/bibigrid/blob/master/bibigrid-openstack/docs/Credentials_Setup.md) (Option 2).

### Configuration
First up, you need to have a SSH-key for communication with the remote compute system. As you have learned previously, you may generate one with the `ssh-keygen` command, e.g. `ssh-keygen -t rsa -b 4096`.  
- Open an editor (Kate, Gedit or **without GUI** nano or vim)  
- Copy & Paste the following configuration  
- save the file as *configuration.yml*.  
~~~BASH
#use openstack
mode: openstack

# credentialsFile: path/to/credentials.yml        # Not necessary if you sourced the rc file

#Access
sshPublicKeyFile: 
  - "HOME_DIRECTORY/.ssh/id_rsa.pub"              # Replace with your home directory
sshUser: ubuntu
#keypair: example_keypair                         # Not necessary
region: example_region                            # Replace with your region
availabilityZone: default                         # Replace with your availabilityZone

#Network
subnet: example_subnet                            # Replace with your Subnet 

###################################################
#BiBiGrid-Master                                  # Replace with your Instance types and image-ids
masterInstance:                                   #
  type: de.NBI tiny                               #
  image: 4da30121-d004-4271-90e9-e145297da41f     #
                                                  #
#BiBiGrid-Workers                                 # Replace with your Instance types and image-ids
workerInstances:                                  #
  - type: de.NBI tiny                             #
    count: 2                                      #
    image: 4da30121-d004-4271-90e9-e145297da41f   #
  - type: de.NBI small                            #
    count: 1                                      #
    image: 4da30121-d004-4271-90e9-e145297da41f   #
###################################################

#Firewall/Security Group
ports:
  - type: TCP
    number: 80

#services
nfs: yes
oge: no
zabbix: yes
slurm: yes

ideConf:
  ide: true
  port_start: 8282
  port_end: 8383
  
zabbixConf:
    db: zabbix                    # Database name. Default is "zabbix"
    db_user: zabbix               # User name for Database. Default is "zabbix"
    db_password: zabbix           # Password of user. Default is "zabbix"
    timezone: Europe/Berlin       # Default is "Europe/Berlin"
    server_name: bibigrid         # Name of Server. Default is "bibigrid"
    admin_password: zabbix        # Change to an unique and secure password
~~~  
### Creating a bibigrid alias
To keep the cluster setup process simple you can set an alias for the BiBiGrid JAR file installed before.  
The Unix command should look like the following (depending on JAR filename):
~~~BASH
> alias bibigrid="java -jar /path/to/bibigrid-*.jar"
~~~
### BiBiGrid commands - Creating the first Cluster
For information about the command set, you may now check the help command:  
~~~BASH
> bibigrid --help
~~~
Now we can create the first cluster with our previously generated configuration:  
~~~BASH
> bibigrid -c -v -o configuration.yml
~~~
If no problem occurs, our cluster should be ready in a couple minutes...  

You can now list your cluster(s) via the listing command:  
~~~BASH
> bibigrid --list
~~~

### Monitoring your cluster setup
To get an overview about how your cluster is working, you can use *Zabbix* for monitoring.  
Therefore type `http://ip.of.your.master/zabbix` into your browser address bar.  
The public-ip of your cluster should be visible with the list command and is also displayed after setup.

For a detailed documentation please visit the [Getting Started Readme](https://github.com/BiBiServ/bibigrid/blob/master/docs/README.md).
