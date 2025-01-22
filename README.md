Description :   

The Nginx deployment in AKS cluster using helm chart with Argocd integration. 



### Install ArgoCD

kubectl create namespace argocd


kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

Get default password

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}"


Optional :Powershell:

$version = (Invoke-RestMethod https://api.github.com/repos/argoproj/argo-cd/releases/latest).tag_name
$url = "https://github.com/argoproj/argo-cd/releases/download/" + $version + "/argocd-windows-amd64.exe"
Invoke-WebRequest -Uri $url -OutFile $output

NOTE: Expose the argocd-server service to get the public IP, so that you can connect to the ARGOCD UI outside the cluster and no need to perform any port-forward to validate. It will also help to login using cli and check the status of the sync.

Use cli for Login:

argocd login (LB IP example:  4.123.10.150:443 or the domain name) --username admin --password "xxxxxxxxxxx" --insecure 


Get default password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}"
Default User: admin

Add cluster to Argocd using : argocd cluster add <current-conetxt>

NOTE: This will create a service account `argocd-manager` on the cluster referenced by context `xxxx` with full cluster level privileges


Validate connection with: argocd cluster list

Add the repo to the argocd:
argocd repo add <git repo url > --name <xxx>

For deployment of application:

argocd app create nginx   --repo https://github.com/ushabapat/IAAC.git   --path nginx-chart   --dest-server https://kubernetes.default.svc   --dest-namespace default --revision master

Use argocd app sync nginx
Valiate the sync is successful. You can alos validate in the argocd ui to check the details. 



