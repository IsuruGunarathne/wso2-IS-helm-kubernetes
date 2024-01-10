# Deploying WSO2 IS on Kubernetes using Helm

Note : `.prettierignore` is used to ignore the formatting of the `.yaml` files. as this messes with helm templating
https://github.com/prettier/prettier/issues/6517

# run the following command to install the chart

`sudo systemctl start docker`

`minikube start`

`helm install is-release . `

# run the following command to upgrade the chart

`helm upgrade is-release . `

# run the following command to delete the chart

`helm delete is-release`

# access console within the docker container

get the pod name using `kubectl get pods` then run
`kubectl exec -it <pods name> -- /usr/bin/bash`
