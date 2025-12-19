# Install-Matlab2025b-on-Arch
When users try to run the "MathWorksProductAuthorizer.sh" an error occurs:

```
line 14: 12804 Segmentation fault         (core dumped) $BINDIR/MathWorksProductAuthorizer $@terminated by signal sigsegv (address boundary error)
```

This is an issue with the current GnuTLS that is shipped with Arch. 


# Install instructions / Fix 
Install Matlab-MPM, this is matlabs package manager. 
https://aur.archlinux.org/packages/matlab-mpm

## Step 1
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





