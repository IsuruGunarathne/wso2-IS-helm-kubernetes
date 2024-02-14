# Deploying WSO2 IS on Kubernetes using Helm, with a MySQL database

Note : `.prettierignore` is used to ignore the formatting of the `.yaml` files. as this messes with helm templating
https://github.com/prettier/prettier/issues/6517

Note : start the IS only after the MySQL is up and running

## IS

### JDBC driver

download the jar file at https://mvnrepository.com/artifact/mysql/mysql-connector-java/8.0.20
and place it in <IS_HOME>/repository/components/lib directory
then rebuild the docker image

### run the following command to install the chart

`sudo systemctl start docker`

`minikube start`

`helm install is-release . ` this should be run from within the IS directory

### run the following command to upgrade the chart

`helm upgrade is-release . `

### run the following command to delete the chart

`helm delete is-release`

### access console within the docker container

get the pod name using `kubectl get pods` then run
`kubectl exec -it <pods name> -- /usr/bin/bash`

## MySQL

### run the following command to install the chart

`helm install mysql-release . ` from within the MySQL directory

### checking the mysql deployment

`kubectl describe deployment mysql`

### checking persistent volume

`kubectl get pv` to get the persistent volume

to access the directory
`minikube ssh` then `cd /` then navigate to the host path

### accessing the mysql cli

`kubectl run -it --rm --image=mysql:latest --restart=Never mysql-client -- mysql -h mysql -ppassword`

or

go into terminal in the container using `kubectl exec -it <pods name> -- /bin/bash` then run
`mysql -h mysql -u regadmin -pregadmin` from inside the container

### remove mysql chart

`helm delete mysql-release`

## Ingress

### enabling ingress and ingress-dns on minikube

`minikube addons enable ingress`
`minikube addons enable ingress-dns`

### adding the host to /etc/hosts

`echo "$(minikube ip) wso2is.com" | sudo tee -a /etc/hosts`

### checking addons

`minikube addons list`

make sure that ingress and ingress-dns are enabled, go to the browser and type `https://wso2.is` to access the deployment

## Testing MSSQL connection

exec into a pod and install msodbcsql18

`curl https://packages.microsoft.com/keys/microsoft.asc | tee /etc/apt/trusted.gpg.d/microsoft.asc`
`curl https://packages.microsoft.com/config/ubuntu/22.04/prod.list | tee /etc/apt/sources.list.d/mssql-release.list`
`apt-get update`
`apt-get install mssql-tools18 unixodbc-dev`
`echo 'export PATH="$PATH:/opt/mssql-tools18/bin"' >> ~/.bash_profile`
`echo 'export PATH="$PATH:/opt/mssql-tools18/bin"' >> ~/.bashrc`
`source ~/.bashrc`

run `sqlcmd -S wso2is-db-secondary.database.windows.net,1433 -U <username> -P <pw> -Q "SELECT DISTINCT type_desc FROM sys.all_objects;"`
