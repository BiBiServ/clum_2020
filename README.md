# de.NBI cloud user meeting 2020 - CLUM 2020
Teaching aids / [slides for the de.NBI cloud user meeting 2020]()  

## Setup of BiBiGrid
### Download the binary jar (or clone & build repository)
At first, you can [download the BiBiGrid](https://bibiserv.cebitec.uni-bielefeld.de/resources/bibigrid/bibigrid-openstack-2.1.1.jar).  
Alternatively, you may clone the [BiBiGrid repository](https://github.com/BiBiServ/bibigrid/) and build it yourself with mvn in the 'bibigrid' directory.
~~~BASH
> git clone https://github.com/BiBiServ/bibigrid.git
> cd bibigrid
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
~~~
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
    admin_password: zabbix        # Change to an unique and secure password ------------- !
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

-----------------------------------------------------------------------  
[How to use Ansible?]()  

-----------------------------------------------------------------------  

After the cluster setup, you can now list your cluster(s) via the listing command:  
~~~BASH
> bibigrid --list
~~~

### Job Scheduling with SLURM workload manager
Open Source tool to execute tasks on the cluster instead of a single computer.
#### Commands
- scontrol: View and/or modify Slurm state
- sinfo:    Reports state of Slurm partitions and nodes
- squeue:   Reports the state of jobs or job steps
- scancel:  Cancel a pending or running job or job step
- srun:     Submit a job for execution
- ...

## Hello World, Hello BiBiGrid!

To see how the cluster with slurm works in action, we start with a typical example : *Hello World !*

- Connect to your cluster using the Theia Web-IDE (You may use another terminal here)
`
bibigrid --ide <clusterid>
`

- Create a new shell script `helloworld.sh` in the spool directory (`/vol/spool`):

```
#!/bin/bash
echo Hello from $(hostname) !
sleep 10
```

- Open a terminal and change into the spool directory. 
`cd /vol/spool`

- Make our helloworld script executable:
`chmod u+x helloworld.sh`

- Submit this script as array job 50 times : `sbatch --array=1-50 --job-name=helloworld hello-world.sh`
- See the status of our cluster: `squeue`
- See the output: `cat slurm-*.out`

### New Feature: Manual Cluster Scaling
In some cases, you may want to scale down your cluster, if you don't need as much worker instances or scale up, if you want to append some.
We now try to scale down one worker instance of our first worker batch previously configured.
```BASH
> bibigrid -sd <bibigrid-id> 1 1
```
If you have a running cluster, that is barely working to full capacity, it is recommended to scale it down.

### Monitoring your cluster setup
To get an overview about how your cluster is working, you can use *Zabbix* for monitoring.  
Therefore it is necessary to use a port-forwarding in order to access the zabbix server on your localhost browser.

Login to the cluster using `ssh -L <local-port>:localhost:80 user@ip-address`.
As the `<local-port>` you may choose *50000* or some port in that range, since these are unlikely to be already used.
The `ìp-address` is the public address of your master instance, that you already got back after cluster finish in the line `export BIBIGRID_MASTER=<ip-address>`.   Alternatively, you can use the `list` command from before to get an overview and copy the respective address in the row `public-ip`.

After you have successfully logged in into your master instance, type `http://localhost:<local-port>/zabbix` into your browser address bar. Accordingly, it is the same `<local-port>` that you have chosen before.   
The public-ip of your cluster should be visible with the list command `bibigrid -l` and is also displayed after setup.

For a detailed documentation please visit the [Getting Started Readme](https://github.com/BiBiServ/bibigrid/blob/master/docs/README.md).

## Terminate a cluster

To terminate a running cluster you can simply use:

`bibigrid -t <clusterid>`

Optionally, it is possible to terminate more than one cluster appending the other ids as follows:
`bibigrid -t <clusterid1> <clusterid2> <clusterid3> ...`

Another option is to terminate all your clusters using your username:
`bibigrid -t <user>`

