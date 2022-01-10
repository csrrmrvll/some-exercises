# Litecoin and some scripts
Adapted from:
 - https://raw.githubusercontent.com/domyrtille/interview_project/main/README.md
 - https://raw.githubusercontent.com/kenan3008/litecoin/main/README.md

## Docker-ayes

Buit docker container can be retrieved by executing `docker pull c3542/litecoin:0.18.1`

[Dockerfile](https://github.com/csrrmrvll/some-exercises/blob/main/Dockerfile) contains a multi-stage dockerfile to build an image of a container with the `litecoin` binary with gpg keys and sha checksums verfication. It runs as a non-root user

Container is scanned for vulnerabilities using Anchore. To get it up and running locally, use [this](https://engine.anchore.io/docs/quickstart/) guide. At the time of the writing of this document, there was no vulnerabilities.

## k8s FTW
he [statefulset](https://github.com/csrrmrvll/some-exercises/blob/main/kube/litecoin-statefulset.yaml) has multiple objects appart from the statefulset like PV, PVC and a headless service. The statefulset has been applied to minikube with a 2Gb PV just as a test.

## Gitlab pipeline

Gitlab pipeline is available in the `.gitlab-ci.yaml` file. It contains two stages: `build` and `deploy`.
`build` stage builds the docker container and pushes it to the dockerhub registry.
`deploy` stage deploys the latest image to a staging cluster using the helm chart and `latest` image tag.

A better approach could be to build a new version of the helm chart along with a new image, push it to some registry and then deploy this helm chart referencing the correct image tag to staging.
Another solution could utilize GitOps approach and ArgoCD. Gitlab pipeline would make the necessary changes to a particular git repository monitored by ArgoCD. ArgoCD would then detect this change and make the necessary adjustments in the cluster.

## Script kiddies

Task: List of all users is available in the file `/etc/passwd`. Get username and home path of a user with ID `1000` .

Solution:

```
cat /etc/passwd | grep 1000 | awk -F':' '{print $1}'

cat /etc/passwd | grep 1000 | awk -F':' '{print $6}'
```

`cat /etc/passwd` prints out the whole content of the `etc/passwd`
`grep 1000` selects the line with the user id equal to 1000.
`awk -F':' '{print $1}'` defines `:` to be the delimiter, then selects the first field and outputs it.
`awk -F':' '{print $6}'` defines `:` to be the delimiter, then selects the sixth field and outputs it.

Content of the file used for testing is in the `etc_passwd_example` file.

## Script grown-ups

The same problem as in the previous section is solved in the `parse_etc_passwd.py` python script. The script accepts one argument`--uid` . It will then output the user name and home dir for the user.

The script utilizes the `argparse` to read command line arguments.

Usage:

```
$ python3 parse_etc_passwd.py -h
usage: parse_etc_passwd.py [-h] [--uid uid]

outputs user name and home dir for given uid

optional arguments:
  -h, --help  show this help message and exit
  --uid uid   uid of the user
```


Content of the file used for testing is in the `etc_passwd_example` file.

## Terraform

Be sure to define a profile `tf-devops-dev` in your credentials, to be able to run this code.

All the resources are located in the `terraform` folder.
There are two variables defined:
* `prefix` - environment where you are deploying, defaults to `prod`
* `aws_account` - ID of the AWS account you're using, no default value
