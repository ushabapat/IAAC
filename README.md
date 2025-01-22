Description :   

The Nginx deployment in AKS cluster using helm chart with Argocd integration. In this case I have considered a nginx deployment as a sample to validate the deployment via Argo CD.

NOTE:  The Image TAG value will get updated automatically from the build and deployment of the application workflow.  The application is based on the .net. I have not created a new helm chart for the same. Just used the same helm chart to check if the TAG value get automatically or not.  



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

Rollback



ArgoCD allows you to rollback to any previously synchronized version.

	1.	List the history of deployments:


argocd app history nginx




	2.	Rollback to a specific version (e.g., nginx-v1):



argocd app rollback nginx 1



Automating Versioned Deployments



In the current workflow :

	1.	Update in the Git repository.

	2.	Uses the ArgoCD CLI or API to update the application path and sync.

Use argocd app sync nginx
Valiate the sync is successful. You can alos validate in the argocd ui to check the details. 



