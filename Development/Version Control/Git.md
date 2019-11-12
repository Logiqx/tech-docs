# Git

## Background

Home Page - [Git](https://git-scm.com/)



## Migration from CVS

I have documented the activities that I performed when migrating from [CVS to Git](CVS2Git.md).



## SourceTree

I perform most Git activities via the command line but I have also been using SourceTree as a GUI.

Home Page - [SourceTree](https://www.atlassian.com/software/sourcetree)



## GitFlow

Links:

- [A successful Git branching model](http://nvie.com/posts/a-successful-git-branching-model/)
- [Introducing GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html)
- [GitFlow](https://github.com/nvie/gitflow)



## SSH Keys

To avoid being prompted for username and password for every "push" it is advisable to use SSH keys.

#### Local

Create your SSH key as follows:

```sh
ssh-keygen -t rsa -b 4096 -C "someone@somewhere.com"
```

Start the ssh-agent and add the key:

```sh
eval $(ssh-agent -s)
ssh-add ~/.ssh/id_rsa
```

Tip: You can add the above lines to ~/.profile so that it is done automically.



#### GitHub

Copy / paste the public key into the [SSH Keys](https://github.com/settings/keys) page on GitHub:

```sh
cat ~/.ssh/id_rsa
```



#### Colours

Change the colours for command line output using the following commands:

```sh
# git config --global color.ui auto
git config --global color.diff.old "cyan"
git config --global color.status.untracked "cyan"
git config --global color.status.changed "yellow bold"
```



#### Troubleshooting

If you still get prompted for the username then Git might be using https instead of ssh.

Edit the URL line(s) in .git/config:

```ini
url = https://github.com/USERNAME/REPOSITORY.git
...
url = ssh://git@github.com/USERNAME/REPOSITORY.git
```



## Releases

### Tagging

A couple of my projects use Git tags to trigger automated builds on DockerHub:

- [Python 3 + Beautiful Soup 4 on Alpine Linux](https://github.com/Logiqx/python-bs4)
- [AWS Command Line Interface for Docker (Alpine Linux)](https://github.com/Logiqx/aws-cli)

Both use lightweight tags as described in the [basics of tagging](https://git-scm.com/book/en/v2/Git-Basics-Tagging).

```sh
git tag <tagname>
git push origin <tagname>
```

### Commit IDs

The git revision can be useful for CI/CD builds since it is unique and can be useful as a Docker tag.

Here are two simple commands to get the Git commit id:

```
git describe --always --abbrev=12
git rev-parse --short=12 HEAD
```





#### Links

[Connecting to GitHub with SSH](https://help.github.com/en/articles/connecting-to-github-with-ssh)

[Generating a new SSH key and adding it to the ssh-agent](https://help.github.com/en/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)