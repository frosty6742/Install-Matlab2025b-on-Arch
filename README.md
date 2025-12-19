# Install-Matlab2025b-on-Arch
When users try to run the "MathWorksProductAuthorizer.sh" an error occurs:

```
line 14: 12804 Segmentation fault         (core dumped) $BINDIR/MathWorksProductAuthorizer $@terminated by signal sigsegv (address boundary error)
```

This is an issue with the current GnuTLS that is shipped with Arch. 


# Install instructions / Fix 

## Step 1
Install Matlab-MPM, this is matlabs package manager. 
https://aur.archlinux.org/packages/matlab-mpm

```
paru -S matlab-mpm
```

``` 
# make directory
mkdir /home/$USER/matlab/
cd /home/$USER/matlab/

# install matlab using mpm
mpm install --release=R2025b --destination=/home/$USER/matlab/R2025b MATLAB
```

More info on mpm and the mpm install arguments:
https://www.mathworks.com/help/install/ug/get-mpm-os-command-line.html


## Step 2
Now we will install a older, working version, of GnuTLS for only matlab

```
mkdir /home/$USER/matlab/gnutls
cd /home/$USER/matlab/gnutls

# old gnutls version
wget https://archive.archlinux.org/packages/g/gnutls/gnutls-3.8.9-1-x86_64.pkg.tar.zst

tar --use-compress-program=unzstd -xvf gnutls-3.8.9-1-x86_64.pkg.tar.zst
```

```
# reinstall MathWorksServiceHosts
wget https://www.mathworks.com/MathWorksServiceHost/glnxa64/ReinstallMathWorksServiceHost
chmod +x ReinstallMathWorksServiceHost
./ReinstallMathWorksServiceHost
```

## Step 3
Now that this is patched we will be able to run the authorization script without the error. Do this with the patched version of GnuTLS by running:
```
env LD_LIBRARY_PATH=/home/$USER/matlab/gnutls/usr/lib/ /home/$USER/matlab/R2025b/bin/glnxa64/MathWorksProductAuthorizer.sh
```

To run matlab now run:
```
env LD_LIBRARY_PATH=/home/$USER/matlab/gnutls/usr/lib/ /home/$USER/matlab/R2025b/bin/matlab
```


## Make a desktop entry
This just allows you to hit the superkey and start matlab
```
nano ~/.local/share/applications/matlab-r2025b.desktop
```


Paste this in: (Make sure to set your usename)
```
[Desktop Entry]
Type=Application
Name=MATLAB R2025b
Comment=MATLAB with custom LD_LIBRARY_PATH
Exec=/usr/bin/env LD_LIBRARY_PATH=/home/YOURUSERNAME/matlab/gnutls/usr/lib /home/YOURUSERNAME/matlab/R2025b/bin/matlab -desktop
Icon=/home/YOURUSERNAME/matlab/R2025b/bin/glnxa64/cef_resources/matlab.png
Terminal=false
Categories=Development;Science;Math;
StartupNotify=true
```
Cntrl+S then Cntrl+X to save and exit


Make executable:
```
chmod +x ~/.local/share/applications/matlab-r2025b.desktop
```

Refresh Desktop Database
```
update-desktop-database ~/.local/share/applications
```

# Credits/ Resources
https://www.reddit.com/r/archlinux/comments/1n4ot2k/has_anyone_got_matlab_working_in_archlinux/

Thanks to u/BigBOBS_LOL for a lot of these steps.
