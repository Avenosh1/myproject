# myproject
## CI/CD Pipeline for Java Application:
  - Developed a CI/CD pipeline for a Java application using Jenkins and Docker.
  - Technologies: Jenkins, Docker, Git, GitHub, Java, Groovy.

## Built a Jenkins pipeline to automate building, testing, and running a Docker container.
  - Configured Jenkins to trigger builds automatically on GitHub push events.
  - Designed a Jenkinsfile to define stages for checkout, Docker image creation, and application testing.
  - Successfully integrated GitHub as the SCM and Docker for application testing.

## Steps that were taken:

- deploy a Ubuntu Server 2022 on the Azure by using Cli:

--
```
# define the variables:
$resourceGroup = "Artiom_RG" 
$vmName = "MyVmtop"
$location = "westeurope"
$osType = "Ubuntu2204"
$size = "Standard_D4s_v3"
# create a VM
$vm = az vm create `
--name $vmName --resource-group $resourceGroup --admin-username <username> --admin-password <password> --image $osType --location $location --size $size --security-type TrustedLaunch --ultra-ssd-enabled false  
az vm show --resource-group $resourceGroup --name $vmName --show-details -o table
```
-- add 8080 port open in the NSG configuration of the VM

3 - ssh by using the command:

ssh <user>@<publicIP>






