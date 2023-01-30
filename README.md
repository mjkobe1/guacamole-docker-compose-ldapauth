# GuacamoleLDAPAuth

Apache Guacamole is a free and open-source, clientless remote desktop gateway. 
It provides a HTML5 web interface to access systems via Kubernetes, RDP, SSH, Telnet or VNC.

In this setup we will cover the installation of apache guacamole on docker compose with active directory authentication.

# 1.) Install Docker Engine on Debian:

### Uninstall old versions: 
```
sudo apt-get remove docker docker-engine docker.io containerd runc
```
### Update the apt package index and install packages to allow apt to use a repository over HTTPS:
```
sudo apt-get update
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```
### Add Docker's official GPG key:
```
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```
### Use the following command to set up the repository:
```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
### Update the apt package index:
```
sudo apt-get update
```
### install Docker Engine, containered and Docker Compose
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```
### Verify that the Docker Engine installation is successful by running the hello-world image:
```
sudo docker run hello-world
```

# 2.) Copy boschkundendienst´s guacamole-docker-comopse GIT repository:
```
git clone "https://github.com/boschkundendienst/guacamole-docker-compose.git"
cd guacamole-docker-compose
sudo ./prepare.sh
sudo docker compose up -d
```
* Your guacamole server should now be available at ```https://ip of your server:8443/```. 
The default username is ```guacadmin``` with password ```guacadmin```.

# 3.) Authentication with your Active Directory:

### Go to /guacamole-docker-compose/ and create a directory called config
```
cd guacamole-docker-compose/ 
mkdir config
cd config/
```
### Create a file called guacamole.properties
```
nano guacamole.properties
```
### Copy this to your guacamole.properties file and change the [] to your LDAP properties
```
# Auth provider Class
auth-provider: net.sourcefroge.guacamole.net.auth.ldap.LDAPAuthenticationProvider

# LDAP properties
ldap-hostname:              [AD-Host-Address]
ldap-port:                  [Port-Number]
ldap-user-base-dn:          OU=[Organizational Unit],DC=[Domain Component],DC=[Domain Component]
ldap-username-attribute     samAccountName
ldap-encryption-method:     none
ldap-search-bind-dn:        CN=Administrator,CN=Users,DC=[Domain Component],DC=[Domain Component]
ldap-search-bind-password:  [Password]
ldap-member-attribute:      member
ldap-member-attribute-type: dn
ldap-group-name-attribute:  cn
ldap-group-base-dn:         OU=[Organiztional Unit],DC[Domain Component],DC=[Domain Component]
# Import groups as groups and users as users. Without this lines the groups were importet as users and the users as groups.
ldap-group-search-filter:   (objectClass=group)
ldap-user-search-filter:    (objectClass=user)

