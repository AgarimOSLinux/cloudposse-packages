<!-- 














  ** DO NOT EDIT THIS FILE
  ** 
  ** This file was automatically generated by the `build-harness`. 
  ** 1) Make all changes to `README.yaml` 
  ** 2) Run `make init` (you only need to do this once)
  ** 3) Run`make readme` to rebuild this file. 
  **
  ** (We maintain HUNDREDS of open source projects. This is how we maintain our sanity.)
  **















  -->
[![README Header][readme_header_img]][readme_header_link]

[![Cloud Posse][logo]](https://cpco.io/homepage)

# Packages [![Codefresh Build Status](https://g.codefresh.io/api/badges/pipeline/cloudposse/cloudposse%2Fpackages%2Fapk?branch=master&key=eyJhbGciOiJIUzI1NiJ9.NWEwMTJmNWM3Yjg3YTQwMDAxYTlkMjU0.UwxTSXZGXq3wPQjj3O1k71kQsGuWGlzgkp9V2-llce8&type=cf-1)](https://g.codefresh.io/pipelines/apk/builds?repoOwner=cloudposse&repoName=packages&serviceName=cloudposse%2Fpackages&filter=trigger:build~Build;branch:master;pipeline:5baae099b35f251ecadf1fa0~apk) [![Auto Update Status](https://github.com/cloudposse/packages/workflows/auto-update/badge.svg)](https://github.com/cloudposse/packages/actions?query=workflow%3Aauto-update) [![Latest Release](https://img.shields.io/github/release/cloudposse/packages.svg)](https://github.com/cloudposse/packages/releases/latest) [![Slack Community](https://slack.cloudposse.com/badge.svg)](https://slack.cloudposse.com)


Cloud Posse distribution of awesome apps.


---

This project is part of our comprehensive ["SweetOps"](https://cpco.io/sweetops) approach towards DevOps. 
[<img align="right" title="Share via Email" src="https://docs.cloudposse.com/images/ionicons/ios-email-outline-2.0.1-16x16-999999.svg"/>][share_email]
[<img align="right" title="Share on Google+" src="https://docs.cloudposse.com/images/ionicons/social-googleplus-outline-2.0.1-16x16-999999.svg" />][share_googleplus]
[<img align="right" title="Share on Facebook" src="https://docs.cloudposse.com/images/ionicons/social-facebook-outline-2.0.1-16x16-999999.svg" />][share_facebook]
[<img align="right" title="Share on Reddit" src="https://docs.cloudposse.com/images/ionicons/social-reddit-outline-2.0.1-16x16-999999.svg" />][share_reddit]
[<img align="right" title="Share on LinkedIn" src="https://docs.cloudposse.com/images/ionicons/social-linkedin-outline-2.0.1-16x16-999999.svg" />][share_linkedin]
[<img align="right" title="Share on Twitter" src="https://docs.cloudposse.com/images/ionicons/social-twitter-outline-2.0.1-16x16-999999.svg" />][share_twitter]




It's 100% Open Source and licensed under the [APACHE2](LICENSE).












## Introduction


Use this repo to easily install releases of popular Open Source apps. We provide a few ways to use it.

1. **Make Based Installer.** This installer works regardless of your OS and distribution. It downloads packages directly from their GitHub source repos and installs them to your `INSTALL_PATH`. 
2. **Alpine Linux Packages.** Use our Alpine repository to install prebuilt packages that use the original source binary (where possible) from the maintainers' official GitHub repo releases.
3. **Docker Image.** Use our docker image as a base-image or as part of a multi-stage docker build. The docker image always distributes the latest linux binaries for `x86_64` architectures.

See examples below for usage.

**Is one of our packages out of date?**

Open up an [issue](https://github.com/cloudposse/packages/issues) or submit a PR (*preferred*). We'll review quickly!

## Usage




### Alpine Repository (recommended)

A public Alpine repository is provided by [Cloud Posse](https://cloudposse.com). The repository is hosted on Amazon S3 and fronted by [CloudFlare's CDN](http://cloudflare.com) with end-to-end TLS. This ensures insane availability with DDoS mitigation and low-cost hosting. Using this alpine repository is ultimately more reliable than depending on [GitHub for availability](https://twitter.com/githubstatus) and provides an easier way to manage dependencies pinned at multiple versions. 

The repository itself is managed using [`alpinist`](https://github.com/cloudposse/alpinist), which takes care of the heavy lifting of building repository indexes. You can self-host your own Alpine repository using this strategy.

### Configure the alpine repository:

#### The Easy Way

We provide a bootstrap script to configure the alpine repository for your version of alpine. 

```
curl -sSL https://apk.cloudposse.com/install.sh | sh
```
__NOTE__: Requires `bash` and `curl` to run:

#### For Docker

Add the following to your `Dockerfile` near the top.
```
# Install the cloudposse alpine repository
ADD https://apk.cloudposse.com/ops@cloudposse.com.rsa.pub /etc/apk/keys/
RUN echo "@cloudposse https://apk.cloudposse.com/3.11/vendor" >> /etc/apk/repositories
```
__NOTE__: we support alpine `3.7`, `3.8`, `3.9`, `3.10`, and `3.11` packages at this time

### Installing Alpine Packages

When adding packages, we recommend using `apk add --update $package` to update the repository index before installing packages.

Simply install any package as normal:
```
apk add gomplate
```

But we recommend that you use version pinning:
```
apk add gomplate==3.0.0-r0
```

And maybe even repository pinning, so you know that you get our versions:
```
apk add gomplate@cloudposse==3.0.0-r0
```

### Makefile Interface

The `Makefile` interface works on OSX and Linux. It's a great way to distribute binaries in an OS-agnostic way which does not depend on a package manager (e.g. no `brew` or `apt-get`). 

This method is ideal for [local development environments](https://docs.cloudposse.com/local-dev-environments/) (which is how we use it) where you need the dependencies installed natively for your OS/architecture, such as installing a package on OSX.

See all available packages:
```
make -C install help
```

Install everything...
```
make -C install all
```

Install specific packages:
```
make -C install aws-vault chamber
```

Install to a specific folder:
```
make -C install aws-vault INSTALL_PATH=/usr/bin
```

Uninstall a specific package
```
make -C uninstall yq
```




## Examples

### Docker Multi-stage Build

Add this to a `Dockerfile` to install packages using a multi-stage build process:
```
FROM cloudposse/packages:latest AS packages

COPY --from=packages /packages/bin/kubectl /usr/local/bin/
```

### Docker with Git Clone

Or... add this to a `Dockerfile` to easily install packages on-demand:
```
RUN git clone --depth=1 -b master https://github.com/cloudposse/packages.git /packages && \
    rm -rf /packages/.git && \
    make -C /packages/install kubectl
```

### Makefile Inclusion

Sometimes it's necessary to install some binary dependencies when building projects. For example, we frequently 
rely on `gomplate` or `helm` to build chart packages.

Here's a stub you can include into a `Makefile` to make it easier to install binary dependencies.

```
export PACKAGES_VERSION ?= master
export PACKAGES_PATH ?= packages/
export INSTALL_PATH ?= $(PACKAGES_PATH)/bin

## Install packages
packages/install:
        @if [ ! -d $(PACKAGES_PATH) ]; then \
          echo "Installing packages $(PACKAGES_VERSION)..."; \
          rm -rf $(PACKAGES_PATH); \
          git clone --depth=1 -b $(PACKAGES_VERSION) https://github.com/cloudposse/packages.git $(PACKAGES_PATH); \
          rm -rf $(PACKAGES_PATH)/.git; \
        fi

## Install package (e.g. helm, helmfile, kubectl)
packages/install/%: packages/install
        @make -C $(PACKAGES_PATH)/install $(subst packages/install/,,$@)

## Uninstall package (e.g. helm, helmfile, kubectl)
packages/uninstall/%:
        @make -C $(PACKAGES_PATH)/uninstall $(subst packages/uninstall/,,$@)
```

### Contributing Additional Packages
In addition to following the Contributing section, the following steps can be used to add new packages for review (via a PR).
1. Clone an existing, similar, package within the vendors directory. Name the new folder with the same name as the binary package being installed.
2. At a minimum, update the `VERSION`, `DESCRIPTION`, and `Makefile` to reflect the binary being installed. Ensure that a test task exist in the package Makefile.
3. Test the install and ensure that it downloads and runs as expected (`make -C install <your_package> INSTALL_PATH=/tmp`)
4. Test the apk build (see below)
5. Update the `README.md` (`make init readme/deps readme`)

### Testing apk builds

To validate that a new package will build into an apk you can use the following steps;

```bash
make docker/build/apk/shell
make -C vendor/<appname> apk
# Some temp build files in the volume mount set user/group to nobody/nobody for apk building.
# It is easier to remove them while within the docker container.
rm -rf ./tmp/build.*
exit
```



## Makefile Targets
```
amtool                    0.20.0     Tool for interacting with the Alertmanager API
argocd                    1.4.3      Declarative GitOpts for Kubernetes
assume-role               0.3.2      Easily assume AWS roles in your terminal.
atlantis                  0.11.1     Terraform For Teams
awless                    0.1.11     A Mighty CLI for AWS
aws-iam-authenticator     0.5.0      A tool to use AWS IAM credentials to authenticate to a Kubernetes cluster
aws-okta                  0.19.4     aws-okta allows users to authenticate with AWS using Okta credentials
aws-vault                 5.4.1      A vault for securely storing and accessing AWS credentials in development environments
cfssl                     1.4.1      Cloudflare's PKI and TLS toolkit
cfssljson                 1.4.1      Cloudflare's PKI and TLS toolkit json parser
chamber                   2.8.1      CLI for managing secrets
cli53                     0.8.17     Command line tool for Amazon Route 53
cloudflared               2020.3.2   Argo Tunnel client
cloudposse-atlantis       0.9.0.3    Terraform For Teams, enhanced by Cloud Posse
codefresh                 0.52.0     Codefresh CLI
consul                    1.7.2      Hashicorp consul
ctop                      0.7.3      Top-like interface for container metrics
direnv                    2.21.2     Unclutter your .profile
doctl                     1.42.0     A command line tool for DigitalOcean services
duffle                    0.3.5b1    CNAB installer
emailcli                  1.0.3      Command line email sending client written in Go.
fargate                   0.3.2      CLI for AWS Fargate
fetch                     0.3.7      fetch makes it easy to download files, folders, and release assets from a specific public git commit, branch, or tag
figurine                  1.0.1      Print your name in style
fzf                       0.21.1     A command-line fuzzy finder
gh                        0.7.0      The GitHub CLI
ghr                       0.13.0     Upload multiple artifacts to GitHub Releases in parallel
github-commenter          0.5.0      Command line utility for creating GitHub comments on Commits, Pull Request Reviews or Issues
github-release            0.7.2      Commandline app to create and edit releases on Github (and upload artifacts)
github-status-updater     0.2.0      Command line utility for updating GitHub commit statuses and enabling required status checks for pull requests
gitleaks                  1.2.0      Audit git repos for secrets 🔑
gomplate                  3.6.0      A flexible commandline tool for template rendering. Supports lots of local and remote datasources.
gonsul                    0.2.1      A stand-alone alternative to git2consul 
goofys                    0.24.0     a high-performance, POSIX-ish Amazon S3 file system written in Go
gosu                      1.12       Simple Go-based setuid+setgid+setgroups+exec
gotop                     3.0.0      A terminal based graphical activity monitor inspired by gtop and vtop
helm                      3.1.3      The Kubernetes Package Manager
helm2                     2.16.6     The Kubernetes Package Manager
helm3                     3.1.2      The Kubernetes Package Manager
helmfile                  0.111.0    Deploy Kubernetes Helm Charts
htmltest                  0.12.1     :white_check_mark: Test generated HTML for problems
hugo                      0.69.1     The world’s fastest framework for building websites.
jq                        1.6        Command-line JSON processor
json2hcl                  0.0.6      Convert JSON to HCL, and vice versa
jx                        2.1.8      Jenkins-X
k3d                       1.7.0      Little helper to run Rancher Lab's k3s in Docker
k6                        0.26.2     A modern load testing tool, using Go and JavaScript - https://k6.io
k9s                       0.19.2     Kubernetes CLI To Manage Your Clusters In Style
katafygio                 0.8.1      K8s continuous backup to git
kfctl                     1.0        Machine Learning Toolkit for Kubernetes
kind                      0.7.0      A tool for running local Kubernetes clusters using Docker
kops                      1.16.1     Kubernetes Operations (kops) - Production Grade K8s Installation, Upgrades, and Management
kops-1.12                 1.12.3     Kubernetes Operations (kops) - Production Grade K8s Installation, Upgrades, and Management
krew                      0.3.4      Kubectl plugin manager
kubecron                  1.0.2      Utilities to manage kubernetes cronjobs. Run a CronJob manually for test purposes. Suspend/unsuspend a CronJob
kubectl                   1.18.2     Production-Grade Container Scheduling and Management
kubectl-1.13              1.13.12    Production-Grade Container Scheduling and Management (v1.13)
kubectl-1.14              1.14.10    Production-Grade Container Scheduling and Management (v1.14)
kubectl-1.15              1.15.11    Production-Grade Container Scheduling and Management (v1.15)
kubectl-1.16              1.16.8     Production-Grade Container Scheduling and Management (v1.16)
kubectl-1.17              1.17.4     Production-Grade Container Scheduling and Management (v1.17)
kubectx                   0.8.0      Switch faster between clusters and namespaces in kubectl
kubens                    0.8.0      Switch faster between clusters and namespaces in kubectl
kubeval                   0.15.0     Validate your Kubernetes configuration files, supports multiple Kubernetes versions
lazydocker                0.8        The lazier way to manage everything docker
lectl                     0.20       Script to check issued certificates by Let's Encrypt on CTL (Certificate Transparency Log) using https://crt.sh
minikube                  1.9.2      Run Kubernetes locally
misspell                  0.3.4      Correct commonly misspelled English words in source files
nomad                     0.11.1     Hashicorp nomad
pack                      0.10.0     Create cloud native Buildpacks
packer                    1.5.5      Packer is a tool for creating identical machine images for multiple platforms from a single source configuration.
pandoc                    2.9.2.1    Universal markup converter
pgmetrics                 1.9.0      Postgres metrics
popeye                    0.8.1      A Kubernetes cluster resource sanitizer
promtool                  2.17.2     Prometheus CLI tool
rakkess                   0.4.4      Review Access - kubectl plugin to show an access matrix for all available resources
rancher                   2.4.0      Rancher CLI
rbac-lookup               0.5.0      Find Kubernetes roles and cluster roles bound to any user, service account, or group name.
retry                     3.3.0      ♻️ Functional mechanism based on channels to perform actions repetitively until successful.
saml2aws                  2.25.0     CLI tool which enables you to login and retrieve AWS temporary credentials using a SAML IDP
scenery                   0.1.5      A Terraform plan output prettifier
sentinel                  0.14.2     Hashicorp sentinel
sentry-cli                1.52.3     A command line utility to work with Sentry.
shellcheck                0.7.1      ShellCheck, a static analysis tool for shell scripts
shfmt                     3.1.0      A shell parser, formatter and interpreter (POSIX/Bash/mksh)
slack-notifier            0.2.0      Command line utility to send messages with attachments to Slack channels via Incoming Webhooks
sops                      3.5.0      Secrets management stinks, use some sops!
stern                     1.11.0     ⎈ Multi pod and container log tailing for Kubernetes
sudosh                    0.2.0      Shell wrapper to run a login shell with `sudo` as the current user for the purpose of audit logging
teleport                  4.2.8      Privileged access management for elastic infrastructure.
terraform                 0.12.24    Terraform is a tool for building, changing, and combining infrastructure safely and efficiently.
terraform-0.11            0.11.14    Terraform is a tool for building, changing, and combining infrastructure safely and efficiently.
terraform-0.12            0.12.24    Terraform is a tool for building, changing, and combining infrastructure safely and efficiently.
terraform-docs            0.9.1      Generate docs from terraform modules
terragrunt                0.23.10    Terragrunt is a thin wrapper for Terraform that provides extra tools for working with multiple Terraform modules.
terrahelp                 0.7.4      Terrahelp is as a command line utility that provides useful tricks like masking of terraform output.
tfenv                     0.4.0      Transform environment variables for use with Terraform (e.g. `HOSTNAME` ⇨ `TF_VAR_hostname`)
tfmask                    0.4.0      Terraform utility to mask select output from `terraform plan` and `terraform apply`
trivy                     0.6.0      A Simple and Comprehensive Vulnerability Scanner for Containers, Suitable for CI
variant                   0.36.4     Variant is a Universal CLI tool that works like a task runner
variant2                  0.24.0     Variant is a Universal CLI tool that works like a task runner
vault                     1.4.0      Hashicorp vault
venona                    0.31.1     Codefresh runtime-environment agent
vert                      0.1.0      Simple CLI for comparing two or more versions
yq                        3.3.0      yq is a portable command-line YAML processor
```



## Share the Love 

Like this project? Please give it a ★ on [our GitHub](https://github.com/cloudposse/packages)! (it helps us **a lot**) 

Are you using this project or any of our other projects? Consider [leaving a testimonial][testimonial]. =)


## Related Projects

Check out these related projects.

- [build-harness](https://github.com/cloudposse/build-harness) - Collection of Makefiles to facilitate building Golang projects, Dockerfiles, Helm charts, and more
- [geodesic](https://github.com/cloudposse/geodesic) - Geodesic is the fastest way to get up and running with a rock solid, production grade cloud platform built on strictly Open Source tools.



## Help

**Got a question?** We got answers. 

File a GitHub [issue](https://github.com/cloudposse/packages/issues), send us an [email][email] or join our [Slack Community][slack].

[![README Commercial Support][readme_commercial_support_img]][readme_commercial_support_link]

## DevOps Accelerator for Startups


We are a [**DevOps Accelerator**][commercial_support]. We'll help you build your cloud infrastructure from the ground up so you can own it. Then we'll show you how to operate it and stick around for as long as you need us. 

[![Learn More](https://img.shields.io/badge/learn%20more-success.svg?style=for-the-badge)][commercial_support]

Work directly with our team of DevOps experts via email, slack, and video conferencing.

We deliver 10x the value for a fraction of the cost of a full-time engineer. Our track record is not even funny. If you want things done right and you need it done FAST, then we're your best bet.

- **Reference Architecture.** You'll get everything you need from the ground up built using 100% infrastructure as code.
- **Release Engineering.** You'll have end-to-end CI/CD with unlimited staging environments.
- **Site Reliability Engineering.** You'll have total visibility into your apps and microservices.
- **Security Baseline.** You'll have built-in governance with accountability and audit logs for all changes.
- **GitOps.** You'll be able to operate your infrastructure via Pull Requests.
- **Training.** You'll receive hands-on training so your team can operate what we build.
- **Questions.** You'll have a direct line of communication between our teams via a Shared Slack channel.
- **Troubleshooting.** You'll get help to triage when things aren't working.
- **Code Reviews.** You'll receive constructive feedback on Pull Requests.
- **Bug Fixes.** We'll rapidly work with you to fix any bugs in our projects.

## Slack Community

Join our [Open Source Community][slack] on Slack. It's **FREE** for everyone! Our "SweetOps" community is where you get to talk with others who share a similar vision for how to rollout and manage infrastructure. This is the best place to talk shop, ask questions, solicit feedback, and work together as a community to build totally *sweet* infrastructure.

## Discourse Forums

Participate in our [Discourse Forums][discourse]. Here you'll find answers to commonly asked questions. Most questions will be related to the enormous number of projects we support on our GitHub. Come here to collaborate on answers, find solutions, and get ideas about the products and services we value. It only takes a minute to get started! Just sign in with SSO using your GitHub account.

## Newsletter

Sign up for [our newsletter][newsletter] that covers everything on our technology radar.  Receive updates on what we're up to on GitHub as well as awesome new projects we discover. 

## Office Hours

[Join us every Wednesday via Zoom][office_hours] for our weekly "Lunch & Learn" sessions. It's **FREE** for everyone! 

[![zoom](https://img.cloudposse.com/fit-in/200x200/https://cloudposse.com/wp-content/uploads/2019/08/Powered-by-Zoom.png")][office_hours]

## Contributing

### Bug Reports & Feature Requests

Please use the [issue tracker](https://github.com/cloudposse/packages/issues) to report any bugs or file feature requests.

### Developing

If you are interested in being a contributor and want to get involved in developing this project or [help out](https://cpco.io/help-out) with our other projects, we would love to hear from you! Shoot us an [email][email].

In general, PRs are welcome. We follow the typical "fork-and-pull" Git workflow.

 1. **Fork** the repo on GitHub
 2. **Clone** the project to your own machine
 3. **Commit** changes to your own branch
 4. **Push** your work back up to your fork
 5. Submit a **Pull Request** so that we can review your changes

**NOTE:** Be sure to merge the latest changes from "upstream" before making a pull request!


## Copyright

Copyright © 2017-2020 [Cloud Posse, LLC](https://cpco.io/copyright)



## License 

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0) 

See [LICENSE](LICENSE) for full details.

    Licensed to the Apache Software Foundation (ASF) under one
    or more contributor license agreements.  See the NOTICE file
    distributed with this work for additional information
    regarding copyright ownership.  The ASF licenses this file
    to you under the Apache License, Version 2.0 (the
    "License"); you may not use this file except in compliance
    with the License.  You may obtain a copy of the License at

      https://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing,
    software distributed under the License is distributed on an
    "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    KIND, either express or implied.  See the License for the
    specific language governing permissions and limitations
    under the License.









## Trademarks

All other trademarks referenced herein are the property of their respective owners.

## About

This project is maintained and funded by [Cloud Posse, LLC][website]. Like it? Please let us know by [leaving a testimonial][testimonial]!

[![Cloud Posse][logo]][website]

We're a [DevOps Professional Services][hire] company based in Los Angeles, CA. We ❤️  [Open Source Software][we_love_open_source].

We offer [paid support][commercial_support] on all of our projects.  

Check out [our other projects][github], [follow us on twitter][twitter], [apply for a job][jobs], or [hire us][hire] to help with your cloud strategy and implementation.



### Contributors

|  [![Erik Osterman][osterman_avatar]][osterman_homepage]<br/>[Erik Osterman][osterman_homepage] | [![Igor Rodionov][goruha_avatar]][goruha_homepage]<br/>[Igor Rodionov][goruha_homepage] | [![Andriy Knysh][aknysh_avatar]][aknysh_homepage]<br/>[Andriy Knysh][aknysh_homepage] |
|---|---|---|

  [osterman_homepage]: https://github.com/osterman
  [osterman_avatar]: https://img.cloudposse.com/150x150/https://github.com/osterman.png
  [goruha_homepage]: https://github.com/goruha
  [goruha_avatar]: https://img.cloudposse.com/150x150/https://github.com/goruha.png
  [aknysh_homepage]: https://github.com/aknysh
  [aknysh_avatar]: https://img.cloudposse.com/150x150/https://github.com/aknysh.png

[![README Footer][readme_footer_img]][readme_footer_link]
[![Beacon][beacon]][website]

  [logo]: https://cloudposse.com/logo-300x69.svg
  [docs]: https://cpco.io/docs?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/packages&utm_content=docs
  [website]: https://cpco.io/homepage?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/packages&utm_content=website
  [github]: https://cpco.io/github?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/packages&utm_content=github
  [jobs]: https://cpco.io/jobs?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/packages&utm_content=jobs
  [hire]: https://cpco.io/hire?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/packages&utm_content=hire
  [slack]: https://cpco.io/slack?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/packages&utm_content=slack
  [linkedin]: https://cpco.io/linkedin?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/packages&utm_content=linkedin
  [twitter]: https://cpco.io/twitter?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/packages&utm_content=twitter
  [testimonial]: https://cpco.io/leave-testimonial?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/packages&utm_content=testimonial
  [office_hours]: https://cloudposse.com/office-hours?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/packages&utm_content=office_hours
  [newsletter]: https://cpco.io/newsletter?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/packages&utm_content=newsletter
  [discourse]: https://ask.sweetops.com/?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/packages&utm_content=discourse
  [email]: https://cpco.io/email?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/packages&utm_content=email
  [commercial_support]: https://cpco.io/commercial-support?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/packages&utm_content=commercial_support
  [we_love_open_source]: https://cpco.io/we-love-open-source?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/packages&utm_content=we_love_open_source
  [terraform_modules]: https://cpco.io/terraform-modules?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/packages&utm_content=terraform_modules
  [readme_header_img]: https://cloudposse.com/readme/header/img
  [readme_header_link]: https://cloudposse.com/readme/header/link?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/packages&utm_content=readme_header_link
  [readme_footer_img]: https://cloudposse.com/readme/footer/img
  [readme_footer_link]: https://cloudposse.com/readme/footer/link?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/packages&utm_content=readme_footer_link
  [readme_commercial_support_img]: https://cloudposse.com/readme/commercial-support/img
  [readme_commercial_support_link]: https://cloudposse.com/readme/commercial-support/link?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/packages&utm_content=readme_commercial_support_link
  [share_twitter]: https://twitter.com/intent/tweet/?text=Packages&url=https://github.com/cloudposse/packages
  [share_linkedin]: https://www.linkedin.com/shareArticle?mini=true&title=Packages&url=https://github.com/cloudposse/packages
  [share_reddit]: https://reddit.com/submit/?url=https://github.com/cloudposse/packages
  [share_facebook]: https://facebook.com/sharer/sharer.php?u=https://github.com/cloudposse/packages
  [share_googleplus]: https://plus.google.com/share?url=https://github.com/cloudposse/packages
  [share_email]: mailto:?subject=Packages&body=https://github.com/cloudposse/packages
  [beacon]: https://ga-beacon.cloudposse.com/UA-76589703-4/cloudposse/packages?pixel&cs=github&cm=readme&an=packages
