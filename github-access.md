# Accessing Github using Personal Access Tokens and SSH

## Overview

As of August 2021 Github will have deprecated (tech talk for stopped supporting) password authentication for all repositories on their site.
This will not change how you collaborate using the commands we showed you in this session.
It will however change how you login when you first use github on a new machine, or when you need to re-authenticate at any stage.

There are two methods you can choose to use instead of a password:
1. Personal Access Tokens (PAT)
2. SSH based authentication


Either of these will work, and which is the 'best' to use is debatable.
A PAT is going to be very similar to how you used a password, you generate one using the github website, and then you store the text and copy-paste the PAT when you are asked for a password during log-in.
An SSH key is going to behave to any public key authentication scheme, you have a private key that you store locally and a public key that you place in github.

We provide some guidance on setting up both of these mechanisms below.
However it is **strongly** encouraged that you consult the [github documentation](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure) as this will be the most up-to-date reference that is maintained by github themselves.

### Personal Access Tokens

Before you start creating access tokens, make sure you have [verified your email](https://docs.github.com/en/github/getting-started-with-github/verifying-your-email-address) by clicking on the link sent by github when you signed up.

Navigate to [Generate New personal access token](https://github.com/settings/tokens/new) in your github developer settings:
- `settings` (click on your profile icon on the top right, settings is near the bottom of the drop-down) 
- -> `Developer settings` (near the bottom of the menus on the left of the page)
- -> `Personal access tokens` (third option down in the left menus)
- -> `Generate new token` (grey button on right of page)

Give your token a name in the `Note` field
- e.g. university laptop

Select the permissions you want to assign this token
- All `repo` boxes -> if you want to access you repo from the command line like you did in this session

Generate the token by clicking on `Generate token` (Green button at bottom of page)

Click on the clipboard icon beside the token to copy the token to your clipboard

**IMPORTANT** - Make sure you store this token as you would any other password, you will not be able to view it again whenever you leave the page.
- This also means that you should not hardcode you access token into any programs you write as anyone who can see your code will be able to access your other repos using you PAT

Once you have your token generated, you can use it in place of a password anytime you perform Git operations over `HTTPS`. For example:

```bash
git clone https://github.com/username/repo.git
Username: github_username
Password: token_generated_above
```

This token will not work for `ssh://` Git operations, see [below](#ssh) for how to add an ssh key to your account.

### SSH

*note: When using an ssh key, make sure you clone using `ssh://github.com/username/repo` rather than `https`*

There are two parts to this method; local generation of your SSH key pair, and uploading the public key to github.

**Generating a key-pair**
Open up a terminal on your local machine.
On Mac and Linux this can be the default `terminal` app.
For windows you should use the `Git Bash` app.
```bash
# Creates a new ssh key using your email address as the label
# If your machine does not support the ed25519 algorithm then you can use RSA:
# ssh-keygen -t rsa -b 2096 -C "your__github_email@example.com"
ssh-keygen -t ed25519 -C "your__github_email@example.com"
```
The generation process will include two prompts for input.
1. The file location -> accept the default (/Users/you/.ssh/<key_name>)
2. A secure passphrase (c.f. a password) -> used each time you try to authenticate using ssh
    - You can leave this blank if you do not want a passphrase

*Mac only* (skip if using Windows or Linux)

If you are running Mac then you have some extra steps to load the SSH key
```bash
# Check if you already have a ssh config file
open ~/.ssh/config

# Create an ssh config if one does not exist
touch ~/.ssh/config

# Open the ssh config file
# nano only used as an example
nano ~/.ssh/config

# contents of ssh config file
Host *
  AddKeysToAgent yes
  UseKeychain yes # Do not add this line if you did not set a passphrase
  IdentityFile ~/.ssh/id_ed25519 # Or whatever you choose to name your key

# save changes to ssh config

# Load the new key so you can use it locally
ssh-add -K ~/.ssh/id_ed25519
```

*Windows and Linux* (Not requred on Mac)

On Windows and Linux you just need to add the new key, no modifications to the config file required.

```bash
# Load you new key so you can use it locally
ssh-add ~/.ssh/id_ed25519
```

**Uploading public key to github**

Start by copying your **public key**

```bash
# print key to your terminal
cat ~/.ssh/id_ed25519.pub
# Example: ssh-ed25519 abc123 your_github_email@example.com
```

Remember to make sure you are copying the contents of the .pub file, and not the private key.
You will know if you have copied to private key as the output will start:

`-----BEGIN OPENSSH PRIVATE KEY-----`

Open the [New/Add SSH Key](https://github.com/settings/ssh/new) page in your github account settings
- `settings` (click on your profile icon on the top right, settings is near the bottom of the drop-down) 
- -> `SSH and GPG keys` (half-way down the menus on the left
- -> `New SSH Key` (green button on right of screen)

Fill in the form:
- `Title` -> Add a descriptive label for your key, e.g. university mac
- `Key` -> Copy the contents from the .pub file

Finish by clicking `Add SSH key` (green button)

Enter your github password if prompted