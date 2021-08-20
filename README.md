
# Guide for configuring GitHub authentication

This is meant to be a brief guide for setting up GitHub authentication
via ssh - this configuration will be done in R but once setup the ssh
keys will be available for any other tools using git (e.g. Jupyter) on
the same system.

We will begin by creating a public private key pair using the
`credentials` package, run the following line in the RStudio Console:

``` r
credentials::ssh_setup_github()
```

The function will first prompt you to create an SSH key pair if one does
not already exist

``` r
## No SSH key found. Generate one now? 
## 
## 1: Yes
## 2: No
## 
## Selection: 
```

Select Yes by entering `1` and hitting enter. The key pair will be
generated and the resulting public key will be printed,

``` r
## Generating new RSA keyspair at: /home/guest/.ssh/id_rsa
## Your public key:
## 
##  ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC5KwynraqqA4O91nOBtyuJMhYoeCAroSv7yC/GTXcjXsBvgYAlL0PCurJP7uRFbvkUaoBvuaGohR1qDlYNNWzH5FBDqftY+o35otq88mhDUaOmCzuik+MCLGiS/hp2D+5imc1Vqjabvk3fexMr7qkHrJE04vB/ZzI0iZzoZ0zGIJSistNODhrt6jCK7OzPFb4/lGGDxp0+vaGKeXIQGGdUwvMUD+HyNqTgO+g7rcdgmnVMWhhLH8uNb7tpQwDbRQu1h4R+xHO9JMHxFUz2cX4Du6GaQXuYLX3p8X276Nl8CU/V2R4CbGMJwO0Z0jcY8CVUhYBOIJsuS1a5ttHSoLCx 
```

You will then be prompted to enter this public key to GitHub, via the
provided link, you can also select Yes here to have the function open a
browser window directly to this page.

``` r
## Please copy the line above to GitHub: https://github.com/settings/ssh/new
## Would you like to open a browser now? 
## 
## 1: Yes
## 2: No
## 
## Selection:
```

If you are not logged into GitHub the website will ask you to do so,
once logged in you should see the following form:

<img src="figures/github_ssh.png" width="100%" />

You should enter a meaningful name for the key and then copy and paste
the entire public key

<img src="figures/github_ssh2.png" width="100%" />

Once finished, you can click the green Add SSH key button. This key
should now show in the list of GitHub SSH keys on
<https://github.com/settings/keys>.

You can now test that the SSH authentication is working by attempting to
clone a private repository (make sure to select the SSH url and not
HTTPS).

## Frequently Asked Questions

1.  **What happens if I already have an SSH key?**
    
    Nothing bad, the `creditials` package will recognize this and just
    print the existing public SSH key. If you want to get rid of the
    existing key you will need to delete the `id_rsa` and `id_rsa.pub`
    files from the `.ssh` directory in your home directory.

2.  **How do I protect my private key?**
    
    The private key of your key pair is saved as a file in a folder
    called `.ssh` in your home directory, having access to the file is
    equivalent to having your password (at least as far as git
    interactions are concerned). By default, permissions should be set
    such that only your user account should be able to access that file.
    If you would like additional security you can encrypt this key using
    a passphrase (i.e. password) via
    `credentials::ssh_update_passphrase()` which will then be required
    each time the key pair is used (e.g. pushing, pulling, etc.).

3.  **I get an error about an unprotected private key file when trying
    to use git**
    
    If you are seeing an error message that looks like the following:
    
        @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
        @         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
        @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
        Permissions 0644 for '/home/guest/.ssh/id_rsa' are too open.
        It is required that your private key files are NOT accessible by others.
        This private key will be ignored.
        Load key "/home/guest/.ssh/id_rsa": bad permissions
        git@github.com: Permission denied (publickey).
        fatal: Could not read from remote repository.
        
        Please make sure you have the correct access rights
        and the repository exists.
    
    this is likely due to your system having a slightly outdated version
    of the `credentials` package which had a bug where the wrong
    permissions were applied to the key pair files. You can either fix
    the permissions to remove read access to anyone but yourself or
    update `credentials`, delete the existing keys, and start the
    process over. See Google for documentation on how to change
    permissions on your specific OS.

4.  **I set this up for my container and now it won’t work on stat
    server (or some other computer)**
    
    This process, much like git configuration, must be done on each
    machine you intend to use, or at least each file system. Generally
    the recommendation is to create a new key pair for each machine you
    will be using, e.g. OIT container, stat server, your laptop, etc.
    The process is quick and GitHub supports the addition of multiple
    public keys.
