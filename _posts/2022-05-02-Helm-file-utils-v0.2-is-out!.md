---
layout: post
title: "Helm file utils v0.2 is out!"
author: "Mihael Rodek"
categories: journal
tags: [helm, helm-file-utils]
image: futl-v2.png
---

We are happy to announce that new major release of Helm File Utils is finally out!

In this version we've added a few new reader and transformer protocols as well as some structural code changes.

## What's new?

* [Community standards](#community-standards)
* [Readers](#readers)
  * [GitHttps](#git_https)
  * [Ssh](#ssh)
* [Transformers](#transformers)
  * [Json2yaml](#json2yaml)
  * [Yaml2json](#yaml2json)
* [Code improvements](#code-improvements)
* [Next steps](#next-steps)

### Community standards

Until now, we have only been focused on developing the plugin itself while not focusing on community. 
That will change from now on and we'll try to update our community guidelines as often as possible.

We have finally updated project's [README](https://github.com/true-north-engineering/helm-file-utils/blob/develop/README.md){:target="_blank"}
so plugin is easier to use and understand to any user. Besides updating the README, we've decided to approach community 
interested in contributing to plugin writing the [Contribution](https://github.com/true-north-engineering/helm-file-utils/blob/develop/CONTRIBUTION.md){:target="_blank"}
and [Testing](https://github.com/true-north-engineering/helm-file-utils/blob/develop/TESTS.md){:target="_blank"} guidelines.


### Readers

As you probably already know, we use Readers for reading the content from specified destination. Until now, only local
read from files and directories and remote read from http(s) was supported. Since git is one of the most important developer's
features, we've decided to add support for it. For now, it's only available through https, but don't worry, ssh implementation is planned
in near future. Similarly to git, we have also decided to add support for ssh access. Find more about new Reader's usage below.


#### Git_https

Protocol that allows user to read content via git. Content is read from repository that can be either private or public one.
While public repositories require no authentication to acces the repository, private repositories require authentication
using username and password (**P**ersonal **A**ccess **T**oken).\
Order for checking credentials is as it follows:
1. Check if credentials are provided in URI using the ``[username[:password]@]`` syntax
2. Look for environment variables named **FUTL_GIT_USER** or **FUTL_GIT_PASSWORD**
3. Look for environment variable named **FUTL_CI** - if variable exists prompt user to enter credentials

_**NOTE : It is assumed that environment variables are set by user himself otherwise program might not work as expected.**_

Git_https template followed by every field explanation can be found below. Optional fields are enlisted in [ ].

```text
!futl git_https://[username[:password]@]git_clone_url path/to/read[#branch]
```

_Credentials_ - optional field, allows user to authenticate when using the private repository as mentioned above\
_Git_clone_url_ - clone url of repository, e.g. ``github.com/true-north-engineering/helm-file-utils.git``\
_Path_ - path to file or directory\
_Branch_ - optional field, can be used for fetching data from specific branch, if none is provided, default repository branch is used

Example of simple usage:

```text
!futl git_https://true-north:pat@github.com/true-north-engineering/helm-file-utils.git tests/filetest/inputfile3.txt#develop
```

More examples of using git_https can be found in [git_https](https://github.com/true-north-engineering/helm-file-utils/tree/main/tests/git_https/input/) test folder.

#### Ssh

Protocol that allows user to read content via ssh. To use the ssh it is required to have authentication.\
Order for checking authentication is as it follows:
1. Check for public keys stored in ``path/from/home/variable/.ssh/.pub`` where path is assumed to be environment variable **HOME**
2. Check if credentials are provided in URI using the ``[username[:password]@]`` syntax
3. Look for environment variables named **FUTL_SSH_USER** or **FUTL_SSH_PASSWORD**
4. Look for environment variable named **FUTL_CI** - if variable exists prompt user to enter credentials


Ssh template followed by every field explanation can be found below. Optional fields are enlisted in [ ].

`` ssh://[username[:password]@]hostname[:port]/path/to/file``
y
_Credentials_ - optional field, allows user to authenticate when connecting via ssh\
_Hostname_ - host where ssh is hosted, can be IP or domain name\
_Port_ - optional field, allows user to change port where ssh is hosted, if not provided, default is 22\
_Path_ - path to file or directory\

More examples of using ssh can be found in [ssh](https://github.com/true-north-engineering/helm-file-utils/tree/main/tests/ssh/input/) test folder.

### Transformers

So far, our transformers were only focused on base64 file manipulations. Helm File Utils v0.2 adds two new transformers -
json2yaml and yaml2json. Our take here was very simple. Most of the files used while working with Helm are either in `.json`
or `.yaml` format. Find more about their usage following the chapters below.

#### Yaml2json

Transformer that transforms given `.yaml` or `.yml` file into `.json` file.

```text
!futl yaml2json://../../tests/filetest/inputfile_yaml.yaml
```

#### Json2yaml

Transformer that transforms given `.json` file into `.yaml` file.

```text
!futl json2yaml://../../tests/filetest/inputfile_json.json
```


### Code improvements

Until now our code was quite messy. To be honest, it still is. We tried to reconstruct our code
while following main Go principles. Most of the code is now commented and should help new contributors to understand it easily.
Error handling now also follows main error handling principles throughout the code.
One of the things that also got a new look are Tests. Find more about writing tests [here](https://github.com/true-north-engineering/helm-file-utils/blob/develop/TESTS.md){:target="_blank"}.


## Next steps

We believe that future of this project is bright and that we have much more to offer.
Some of our future plans involve adding Reader **git_ssh** protocol and Transformer **xslt** and **custom** protocols.
Those should bring Helm File Utils to completely new level while helping developers to do even more manipulations
over their files.

Stay tuned for more!