# F5 Privileged User Access (PUA)

## Introduction
The F5 Privileged User Access (PUA) solution is designed to secure privileged access across federal networks, particularly where legacy systems and administrative interfaces lack native support for modern authentication methods like CAC/PIV or MFA.

## Key Features
- CAC/PIV Integration: Adds strong authentication to systems that don’t support it natively.
- No Client Software Required: Operates without installing agents or modifying backend systems.
- Single Sign-On (SSO): Centralized access to all administrative interfaces and applications.
- Ephemeral Passwords: Uses one-time passwords to enhance security.
- Policy Management: Enforces access controls across servers, routers, firewalls, and other network assets.
- Audit Trails: Full logging and traceability for security teams.
- Customizable: Adapts to client-specific limitations and requirements.

## Why Use F5 PUA
Federal and DoD Drivers:
- Cybersecurity Scorecard Compliance: Mandated by DoD CIO and Cyber Command.
- Legacy System Vulnerabilities: Many critical systems cannot support MFA.
- Audit and Authorization Requirements: CAT I/II weaknesses must be addressed to maintain Authorization to Operate (ATO).
- Elimination of Static Passwords: Push toward dynamic, secure authentication methods.

Challenges Addressed:
- Inconsistent MFA deployment across networks.
- Difficulty securing legacy infrastructure.
- High risk of credential compromise.
- Complexity and cost of traditional PAM solutions.

Strategic Benefits:
- Reduces Attack Surface: All access requires CAC/PIV.
- Supports Compliance: Meets stringent DoD and federal audit requirements.
- Enhances Operational Efficiency: Simplifies access management.
- Future-Proofing: Enables secure use of legacy systems without costly upgrades.

## Deployment Overview
The following sections provide a step-by-step guide to deploying the F5 PUA solution.

## Getting Started and Configuration
### Prerequisites

docker (or compatible) - [Installation Instructions](https://docs.docker.com/engine/install/)

*Note: The operations outlined in this document are illustrated using commands executed on Ubuntu 22.04.*

### Step 1: Installation


#### From GitHub:

*__Note:__ Since this is a private repository, you must be an authorized user to have access to the following docker images*

There are a few options to download the application
1. Download the docker-compose.yaml and env.sample files
```shell
# Download via shel
wget https://github.com/f5devcentral/privileged-user-access/blob/main/docker-compose.yml
wget https://github.com/f5devcentral/privileged-user-access/blob/main/env.sample
# Edit the following file with device secrets as required (see "Configure .env" below)
cp env.sample .env
```
   
2. Clone the repo
```shell
# Clone the repo
git clone https://github.com/f5devcentral/privileged-user-access.git
cd privileged-user-access
# Edit the following file with device secrets as required (see "Configure .env" below)
cp env.sample .env
```
   
3. Download latest source tarball from the [release](https://github.com/f5devcentral/privileged-user-access/releases) section.
```shell
# Clone the repo
wget https://github.com/f5devcentral/privileged-user-access/releases/download/v2.1.4/source.tar.gz
tar -zxf source.tar.gz 
cd source
# Edit the following file with device secrets as required (see "Configure .env" below)
cp env.sample .env
```
 

#### Update current installation
##### 1. Update the application to the "latest" version

```shell
docker compose down
docker rmi  ghcr.io/f5devcentral/privileged-user-access/pua:latest
docker compose up
```

##### 2. Update the image to a specific version (tag)
```shell
docker compose down
<edit docker-compose.yml and  service.pua.image: value by replaceing "latest" tag with the required version tag>
docker compose up
```

### Step 2: Configuring .env

You must configure the .env file before you can startup the application


```
cp env.sample .env
<edit .env and use table below to configure your settings>
```

Following are list of available env variables 



|Name|Description|Default|Notes  |
|--|--|--|--|
|BIGIP_VERIFY_CERT|If true, validate BigIP SSL certificate|true|If set to true, the app will validate the BigIP cert against the known CA certs or those present in USER_CERTS_PATH |
|BIGIP_x_x_x_x|BigIP credentials for iControlRest Connectivity|--|See section __"Setting Device Authentication/secrets"__|
|APP_PORT|Port to access PUA via API or WebUI|8080||
|APP_LOG_LEVEL| Log level for PUA | info | Available options: debug, info, warn, error |
|USER_PLAYBOOKS_PATH| Path to local directory to store playbooks __*__ |./temp/playbooks |Add each playbook under their own directory|
|USER_CONTENT_PATH| Path to local directory to store user content __*__|./temp/content||
|USER_CERTS_PATH|Path to local directory to store user certificates __*__ |./temp/certs |Contains BigIP CA Certs|
|USER_RESOURCE_PATH|Path to local directory to store user resources __*__ |./temp/resources|Contains files uploaded to Application via "Resources" interface|
|MONGO_DB_DATA|Path to local directory to store MongoDB data|/temp/data|MongoDB uses this dir to store DB files
|MONGO_DB_PORT_EXTERNAL __**__|Port App uses to access MongoDB service|27017||
|MONGO_DB_PORT_INTERNAL __**__|MongoDB internal container port|27017||
|MONGO_DB_HOST __**__|MongoDB service name|pua_mongodb||
|MONGO_INITDB_DATABASE __**__|MongoDB DB name|pua||
|MONGO_DB_AUTH_SRC __**__|MongoDB Auth DB name|||
|MONGO_INITDB_ROOT_USERNAME __**__|MongoDB Username|||
|MONGO_DB_PORT_INTERNAL __**__|MongoDB Password|||

__*__ *Path is relative to current directory; root access is required*
__**__ *Modifying these settings could cause unexpected behaviour*



#### Setting Device Authentication/secrets
The PUA tool default configuration relies on environment variables
which contain device access credentials. There are a number of ways to manage and inject secrets into a container environment (modifications to the docker-compose file to support your preferred management process are encouraged), but for simplicity, the .env file in the root project directory is mounted.

When declaring a device secret, you must create an key/value pair in .env file for which

1. the key has the form "BIGIP_A_B_C_D". Where "A_B_C_D can be one of the following with the "." replaced by "_"
a. Device IP
b. Device Hostname


2. the value is 
a. username:password - provide the username and password separated by ":"
b. iControlRest (iCR) Token - Proved the Alphanumneric string generated by BigIP

*Note : When using an iCR Token, please be aware of the token timeout*


Please reference the following examples:

```
# Example 1: For BigIP with IP[10.10.10.1], username[userfoo] & password[bar123]
BIGIP_10_10_10_1=userfoo:

# Example 2 : For BigIP with IP[20.20.20.1] and iCR Token [UE6X6OYOX343434343XGY2MYCKR]
BIGIP_20_20_20_1=UE6X6OYOX343434343XGY2MYCKR

# Example 1: For BigIP with hostname[bigip.acme.com], username[userfoo] & password[bar123]
BIGIP_bigip_acme_com=userfoo:

```

The variable name (the part on the left of the equal sign) must contain the IP address of the BigIP separated by "_"

##### Account Permissions
Since the tool will configure the BigIP, read-write access is required.


#### Secure communications

PUA expects a valid TLS cert bundle. The configuration parameter `BIGIP_VERIFY_CERT` defaults to true. Installers that would like to opt-in to run in an insecure TLS mode must set `BIGIP_VERIFY_CERT: false` and understand that the connection between the PUA Applicator and the BIG-IP does not have secure TLS termination.

##### Configure CA File

If the device has an internally signed certificate, you can mount and use your CA file, you must copy the CA cert file into the directory specified by the `USER_CERTS_PATH` directory


### Step 3:  Launch PUA Applicator tool
Once the above configurations have been made, the tool can be started with:

```
docker compose up
```

The Applicator UI can be accessed at
`http://<hostname>:8080/`.  
*where <hostname> is the host IP/name where the application was installed

You will see the following landing page

![PUA Landing page](./images/pua_landing_page.png)"PUA Landing page")


#### Step 4: Configure your deployment

Instructions on how to configure PUA can be found in [Configuring PUA](./configuration.md) 

## Support

For support, please open a GitHub issue.  Note, the code in this repository is community supported and is not supported by F5 Networks.  For a complete list of supported projects please reference [SUPPORT.md](SUPPORT.md).

## Community Code of Conduct

Please refer to the [F5 DevCentral Community Code of Conduct](code_of_conduct.md).

## License

[Apache License 2.0](LICENSE)

## Copyright

Copyright 2014-2024 F5 Networks Inc.

### F5 Networks Contributor License Agreement

Before you start contributing to any project sponsored by F5 Networks, Inc. (F5) on GitHub, you will need to sign a Contributor License Agreement (CLA).

If you are signing as an individual, we recommend that you talk to your employer (if applicable) before signing the CLA since some employment agreements may have restrictions on your contributions to other projects.
Otherwise by submitting a CLA you represent that you are legally entitled to grant the licenses recited therein.

If your employer has rights to intellectual property that you create, such as your contributions, you represent that you have received permission to make contributions on behalf of that employer, that your employer has waived such rights for your contributions, or that your employer has executed a separate CLA with F5.

If you are signing on behalf of a company, you represent that you are legally entitled to grant the license recited therein.
You represent further that each employee of the entity that submits contributions is authorized to submit such contributions on behalf of the entity pursuant to the CLA.

## Launching the Application


## Support
For support, please open a GitHub issue.  Note, the code in this repository is community supported and is not supported by F5 Networks.  For a complete list of supported projects please reference [SUPPORT.md](SUPPORT.md).
