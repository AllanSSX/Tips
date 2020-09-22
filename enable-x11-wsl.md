# Letting Ubuntu bash on Windows 10 run 'ssh -X'  to get a GUI environment on a remote server

## 1 - Install a x11 server on Windows 10

- [VcXsrv](https://sourceforge.net/projects/vcxsrv/) - still in active development
- [XMing](https://sourceforge.net/projects/xming/) - no update since 2016

## 2 -Install Ubuntu dependencies on you computer

```bash
sudo apt get update
sudo apt get upgrade
sudo apt install ssh xauth xorg x11-apps
```

## 3 - Export DISPLAY in your local `.bashrc`

Go to folder `~` or `$HOME` on you local computer, append `export DISPLAY=localhost:0.0` to your `.bashrc` file and save it.

``` bash
# ~/.bashrc
...
...
export DISPLAY=localhost:0.0
```

## 4 - Start `VcXsrv` and connect to the host with x11 forwarding

Restart your bash shell, open your `VcXsrv` program and use `ssh -XY yourusername@yourhost`. Then enjoy the GUI environment.

``` bash
ssh -XY yourusername@yourhost
```

## 5 - Debug

If you experience troubles using x11 forwarding with the previous steps, look at the following options that will may help you.

### 1 - [optional] Full configuration of x11 forwarding through ssh

Go to the folder contains `ssh_config` file at `/etc/ssh`.

Edit `ssh_config` as root (`sudo`). Inside `ssh_config`, remove the hash `#` in the lines `ForwardAgent`, `ForwardX11`, `ForwardX11Trusted`, and set the corresponding arguments to `yes`.

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

### 2 - [optional] Create `~/.Xauthority` file.

By default, the `~/.Xauthority` file is not always created and can lead to this error message:

``` bash
Warning: No xauth data; using fake authentication data for X11 forwarding
```

As its quite boring to have this message at each connexion, it can be resolved by this command:

``` bash
xauth generate localhost:0.0 .
```

Remember to start VcXsrv or Xming before running the command otherwise it will send you an error message.


### 3 - [optional] Set `$LC` and `$LANG`

After the `ssh` connexion, you can receive this message:

```bash
/usr/bin/manpath: can't set the locale; make sure $LC_* and $LANG are correct
```

The solution is to generate locales for your `$LANG` defined in this file: `/etc/environment`. The locale generated for the system is in this file: `/etc/default/locale`.


To do that, run the command `sudo dpkg-reconfigure locales` or edit `/etc/default/locale` as root. Mine looks like this:


```bash
LANG=en_US.utf-8
LC_ALL=en_US.utf-8
```

After, restart your Linux system to finish the configuration.
