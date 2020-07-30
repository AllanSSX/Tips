# Letting Ubuntu bash on Windows 10 run 'ssh -X'  to get a GUI environment on a remote server

## 1 - Install a x11 server on Windows 10

- [VcXsrv](https://sourceforge.net/projects/vcxsrv/) - still in active development
- [XMing](https://sourceforge.net/projects/xming/) - no update since 2016

## 2 -Install Ubuntu dependencies

```bash
sudo apt install ssh xauth xorg x11-apps
```

## 3 - Configure x11 forwarding through ssh

Go to the folder contains `ssh_config` file at `/etc/ssh`.

Edit `ssh_config` as root (USE `sudo`). Inside `ssh_config`, remove the hash `#` in the lines `ForwardAgent`, `ForwardX11`, `ForwardX11Trusted`, and set the corresponding arguments to `yes`.

``` bash
# /etc/ssh/ssh_config

Host *
    ForwardAgent yes
    ForwardX11 yes
    ForwardX11Trusted yes
```

Also remove the front hash `#` before `Port 22` and `Protocol 2`, and also append a new line at the end of the file to state the xauth file location.

``` bash
# /etc/ssh/ssh_config

#   IdentifyFile ...
    Port 22
    Protocol 2
#   Cipher 3des
#   ...
#   ...
    ...
    ...
    GSSAPIDelegateCredentials no
    XauthLocation /usr/bin/xauth
```

## 4 - Export DISPLAY using .bashrc

Go to folder `~` or `$HOME`, append `export DISPLAY=localhost:0` to your `.bashrc` file and save it.

``` bash
# ~/.bashrc
...
...
export DISPLAY=localhost:0
```

## 5 - Create `~/.Xauthority` file.

By default, the `~/.Xauthority` file is not always created and can lead to this error message:

``` bash
Warning: No xauth data; using fake authentication data for X11 forwarding
```

As its quite boring to have this message at each connexion, it can be resolved by this command:

``` bash
xauth generate :0 .
```

## 6 - Start `Xming` and connect to the host with x11 forwarding 

Restart your bash shell, open your `Xming` program and use `ssh -X yourusername@yourhost`. Then enjoy the GUI environment.

``` bash
ssh -X yourusername@yourhost
```
