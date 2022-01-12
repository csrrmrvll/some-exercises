# Litecoin and some scripts
Adapted from:
 - https://raw.githubusercontent.com/domyrtille/interview_project/main/README.md
 - https://raw.githubusercontent.com/kenan3008/litecoin/main/README.md
 - https://raw.githubusercontent.com/airstand/litecoin/master/README.md

## Docker-ayes

Buit docker container can be retrieved by executing `docker pull c3542/litecoin:0.18.1`

[Dockerfile](https://github.com/csrrmrvll/some-exercises/blob/main/Dockerfile) contains a multi-stage dockerfile to build an image of a container with the `litecoin` binary with gpg keys and sha checksums verfication. It runs as a non-root user

Container is scanned for vulnerabilities using Anchore. To get it up and running locally, use [this](https://engine.anchore.io/docs/quickstart/) guide. At the time of the writing of this document, there was no vulnerabilities.

## k8s FTW
he [statefulset](https://github.com/csrrmrvll/some-exercises/blob/main/kube/litecoin-statefulset.yaml) has multiple objects appart from the statefulset like PV, PVC and a headless service. The statefulset has been applied to minikube with a 2Gb PV just as a test.

## Jenkinsfile

This is a very simple Jenkinsfile using Groovy DSL using my own repo/registry and my minikube kube-config as parameters.

## Script kiddies

Task: List of all users is available in the file `/etc/passwd`. Substitute the ID `1000` by `mask-uid`, find the line and get username and home path of that user.

Solution:

```
cat /etc/passwd | sed 's/1000/mask-uid/g' | grep match-me | awk -F':' '{print $1}'

cat /etc/passwd | sed 's/1000/mask-uid/g' | grep match-me | awk -F':' '{print $6}'
```

`cat /etc/passwd` prints out the whole content of the `etc/passwd`
`sed 's/1000/mask-uid/g'` substitutes the ID `1000` by `mask-uid`
`grep 1000` selects the line with the user id equal to `mask-uid`
`awk -F':' '{print $1}'` defines `:` to be the delimiter, then selects the first field and outputs it
`awk -F':' '{print $6}'` defines `:` to be the delimiter, then selects the sixth field and outputs it

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

## Terraform

Borrowed from [kenan3008/litecoin/terraform](https://github.com/kenan3008/litecoin/tree/main/terraform)

Be sure to define a profile `tf-devops-dev` in your credentials, to be able to run this code.

All the resources are located in the `terraform` folder.
There are two variables defined:
* `prefix` - environment where you are deploying, defaults to `prod`
* `aws_account` - ID of the AWS account you're using, no default value
