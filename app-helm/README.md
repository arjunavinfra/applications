helm repo add <custom-repo-name> <URL>
eg: helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo list 
helm install --set key="values" <custom-release-name> <custom-repo-name>/<chart-name>
eg: helm install arjun bitnami/wordpress
helm list                                                           # show the release name 
helm uninstall <custom-release-name>
helm repo remove <custom-repo-name>
helm search hub wordpress
helm update <chart>
helm rollback <chart>

To download chart 

1. Add repo 
   eg: helm repo add bitnami https://charts.bitnami.com/bitnami

2. helm pull <repo-name>/<chart-name>
   eg: helm pull -untar bitnami/wordpress

3. helm install <custom-release-name> ./chartname
   eg: helm install instance ./wordpress


To upgrade

0. helm repo update 

1. helm upgade <custom-release-name> <repo-name>/<chart-name>

2. helm list 

3. helm history <custom-release-name>

4. helm rollback  <custom-release-name>  <RIVISION NUMBER (1,2,3)>


Creating Helm chart 

helm create <Release-name> ./dir

3 type of 
1. Chart , Value , Cappabilities , Release


Release.Name: The name of the release (not the chart)
Release.Namespace: The namespace the chart was released to.
Release.Service: The service that conducted the release.
Release.IsUpgrade: This is set to true if the current operation is an upgrade or rollback.
Release.IsInstall: This is set to true if the current operation is an install.
Chart: The contents of the Chart.yaml. Thus, the chart version is obtainable as Chart.Version and the maintainers are in Chart.Maintainers.
Files: A map-like object containing all non-special files in the chart. This will not give you access to templates, but will give you access to additional files that are present (unless they are excluded using .helmignore). Files can be accessed using {{ index .Files "file.name" }} or using the {{.Files.Get name }} function. You can also access the contents of the file as []byte using {{ .Files.GetBytes }}
Capabilities: A map-like object that contains information about the versions of Kubernetes ({{ .Capabilities.KubeVersion }}) and the supported Kubernetes API versions ({{ .Capabilities.APIVersions.Has "batch/v1" }})



verifying chart 

helm lint [check the syntax error in helm templating ]

helm template [yaml intentation ] 
helm template --debug [will show the error line ]

helm install <> <> --dry-run [kube error]


view manifest 
helm template <folder/chartname>
helm get manifest <release name>


function!!!!!!!!!!!1

{{- }}   [to remove white space]
{{ with .Release }} [ setting the root ]
{{ .Value.image.repository | defaullt nginx |quot }}
{{ upper Value.image.repository }}
{{ replace "x" "y"  .Value.image.repository}}

conditions!!!!!!!!!!!!!!11

{{ if not .Values.image_name }}

{{ else if eq .Value.image.name "nginx" }}
label:
   name: nginx

{{ end }}



{{ if .Values.service_Account.create }}
<service account defanition>
{{ end }}


kind: Configmpa
data:
  regions:
{{- range .Values.regions }}
-{{. | intent 5}}
{{end}}


Templates!!!!!!!!!!1

_helper.tpl 

{{ define "labels"}}
  app: kube.io/name
  db: kube.io/dbname
{{ end }}

yaml 


{{ include "label" . | quote }}

{{ template "label" . }}


  {{ if eq .Values.favorite.drink "coffee" }}mug: "true"{{ end }}



chart Hook !!!!!!!!!!!!!!1

pre and post

- pre-install 
- pre-upgrade 
- pre-rollback 
- pre-delete 

define this under a job 

annotations:
"helm.sh/hook": pre-upgrade
"helm.sh/hook-weight": "5"
"helm.sh/hook-delete-policy": hook-succeeded



package sign !!!!!!!!!!!1

gpg --quick-generate-key "John Smith" # create pub and pvt keys 

 gpg --export-secret-keys >~/.gnupg/secring.gpg  # export pvt key 

 helm package --sign --key 'John Smith' --keyring ~/.gnupg/secring.gpg ./nginx-chart

 gpg --list-keys


 ls
-----BEGIN PGP SIGNED MESSAGE-----
Hash: SHA512
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
maintainers:
- - email: john@example.com
name: john smith
name: nginx-chart
type: application
version: 0.1.0
...
files:
nginx-chart-0.1.0.tgz: sha256:b7d05022a9617ab953a3246bc7ba6a9de9d4286b2e78e3ea7975cc54698c4274
-----BEGIN PGP SIGNATURE-----
...
=kser
-----END PGP SIGNATURE-----


$ sha256sum nginx-chart-0.1.0.tgz

b7d05022a9617ab953a3246bc7ba6a9de9d4286b2e78e3ea7975cc54698c4274 nginx-chart-0.1.0.tg

helm verify ./nginx-chart-0.1.0.tgz

Error: failed to load keyring: open /home/vagrant/.gnupg/pubring.gpg: no such file or directory

$ gpg --export 'John Smith' > mypublickey

$ helm verify --keyring ./mypublickey ./nginx-0.1.0.tgz

Signed by: John Smith

Using Key With Fingerprint: 20F2395A3176A22DD33DA45470D5188339885A0B

Chart Hash Verified: sha256:b7d05022a9617ab953a3246bc7ba6a9de9d4286b2e78e3ea7975cc54698c4274

$ gpg --recv-keys --keyserver keyserver.ubuntu.com 8D40FE0CACC3FED4AD1C217180BA57AAFAAD1CA5

$ helm install --verify nginx-chart-0.1.0


uploading file

pkg.tgz

index.yaml [details of pkg create when we run the index comment]

provance [cryptography verification]



$ ls

nginx-chart nginx-chart-0.1.0.tgz nginx-chart-0.1.0.tgz.prov

$ mkdir nginx-chart-files

$ cp nginx-chart-0.1.0.tgz nginx-chart-0.1.0.tgz.provn ginx-chart-files/

$ helm repo index nginx-chart-files/ --url https://example.com/charts

$ ls nginx-chart-files

index.yaml nginx-chart-0.1.0.tgz nginx-chart-0



for updateing dependancy chart 

helm dependency update <alias chart name>




for packaging the application and publish over github 

helm package ./

helm repo index ./

sha -a  256 <pkg>.tgz

create the github page 

helm repo add arjun https://arjunavinfra.github.io/app-helm/charts
