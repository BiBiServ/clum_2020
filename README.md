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
`> source FILE.sh`  
Note, that you have to source the file in every new terminal, if you want to access OpenStack options.  

¹ Alternatively, you can [set up a credentials file](https://github.com/BiBiServ/bibigrid/blob/master/bibigrid-openstack/docs/Credentials_Setup.md) (Option 2).
