> # ![](https://about.gitlab.com/ico/favicon-32x32.png) GitLab SSH Config

This tutorial will help you to configure your GitLab SSH keys. Once configured, you should be able to clone repositories, push your changes and submit your pull requests to GitLab repositories via SSH.

_Unless otherwise noted, all instructions are for the Window environment. If you are looking for instructions for Linux or MAC OS, please consult the [official documentation](https://gitlab.com/help/ssh/README) instead._


## Generating a New SSH Key

If not already installed, install [Git for Windows](https://gitforwindows.org/).

Open Git Bash and generate a new SSH key pair:

```bash
$ ssh-keygen -t rsa -b 4096 -C "your-email@example.com"
```

Click through the default settings.

You should have successfully generated two keys under `~/.ssh/`:

1. id_rsa - the private key
2. id_rsa.pub - the public key

Copy the contents of the **id_rsa.pub** into your [GitLab console](https://gitlab.com/profile/keys) and click `Add key`.


## Generating a Custom Named SSH Key

Open Git Bash and generate a new SSH key pair:

```bash
$ ssh-keygen -t rsa -b 4096 -C "your-email@example.com"
```

Choose a custom name for your key: `~/.ssh/gitlab_rsa_example`.

Leave the pass-phrase empty.

You should have successfully generated two keys under `~/.ssh/`:

1. gitlab_rsa_example - the private key
2. gitlab_rsa_example.pub - the public key


## Adding the Key to GitLab

Copy the contents of the **gitlab_rsa_example.pub** into your [GitLab console](https://gitlab.com/profile/keys) and click `Add key`.


## Adding an SSH Identity

Since your are using a custom key name, _gitlab_rsa_example_ instead of the default _id_rsa_, you must configure the SSH client to find it:

```bash
$ eval $(ssh-agent -s)
$ ssh-add ~/.ssh/gitlab_rsa_example
```


## <a name="storing-identity"></a> Storing the SSH Identity

To retain these settings, you'll need to save them to a configuration file.

Create a configuration file `~./ssh/config` and paste the following settings:

```ini
# GitLab | Example
Host YOUR-USERNAME.gitlab.com
  Host gitlab.com
  Preferredauthentications publickey
  IdentityFile ~/.ssh/gitlab_rsa_example
```


## Removing an SSH Identity

If something went wrong in the configuration steps above, or if an identity becomes obsolete, you can remove it altogether.

List the stored ssh keys:

```bash
$ ssh-keygen -l
```

Generate a public key from the private key (do this even if you already have the pub):

```bash
$ ssh-keygen -y -f ~/.ssh/gitlab_rsa_example > ~/.ssh/gitlab_rsa_example.pub
```

And then call `ssh-add` to remove the identity:

```bash
$ ssh-add -d ~/.ssh/gitlab_rsa_example.pub
```

The agent may refuse to remove the identity:

```
Could not remove identity "~/.ssh/gitlab_rsa_example.pub": agent refused operation
```

Try reverting our changes from step [Storing the SSH Identity](#storing-identity), by deleting the relevant `Host` entry, and then retry removing the identity.


## Testing

Run the following command in your terminal:

```bash
$ ssh -T git@YOUR-USERNAME.gitlab.com
```

The first time you connect, you should see a warning:

```
The authenticity of host 'gitlab.com (35.231.145.151)' can't be established.
ECDSA key fingerprint is SHA256:HbW3g8zUjNSksFbqTiUWPWg2Bq1x8xdGUrliXFzSnUw.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'gitlab.com' (ECDSA) to the list of known hosts.
```

Answer `yes` to add GitLab.com to the list of trusted hosts in a `./ssh/known_hosts` file.

Run the above command again. You should now see the following message:

```
Welcome to GitLab, @username!
```


## Adding Remote Origin

```bash
$ git remote add origin git@YOUR-USERNAME.gitlab.com/repository-name.git
```



**Congrats!** You are now ready to pull and push code to GitLab repositories.
