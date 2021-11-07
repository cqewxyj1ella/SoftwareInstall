# install process  
## basic environment information
I'm installing matlab on node6, which runs an OS of ubuntu20.04.1-server and has no GUI.  
There's few guide on installing matlab on linux without gui, and I'm still trying.  
Just to record my steps to review later.  
## install as a user using sudo  
1. get iso file from school's genuine software  
2. use `sftp` to transfer the iso file to node6(a remote linux server)  
under the path: `$HOME/opt`  
3. mount the iso:  
``` bash  
sudo mkdir -p /media/matlab
sudo mount -o ro,loop ./R2021a_Linux.iso /media/matlab
```
4. run the install script  
`sudo bash /media/matlab/install`  
but met problems:
```bash
terminate called after throwing an instance of '(anonymous namespace)::DisplayError'
  what():  No display available.
```
## solve the problems
1. install x11 tools  
`sudo apt install x11-xserver-utils`  
2. allow x11 forwarding  
```bash
sudo vim /etc/ssh/sshd_config
# set 
# X11Forwarding yes
sudo vim /etc/ssh/ssh_config
# set
# ForwardX11 yes
# ForwardX11Trusted yes
```
3. add x11 host  
```bash
export DISPLAY='127.0.0.1:0.0'
xhost +
# and met error:
xhost:  unable to open display "127.0.0.1:0.0"
```
exit and ssh with -X:  
`ssh -X ella717@<host-ip>`  
or  
`ssh -Y ella717@<host-ip>`  
**so the problem of x11 is not solved...**
## install in the other way
helpful guide links:  
[github-blog-on-how-to-install-matlab-without-gui](https://jiangnanhugo.github.io/blog/install-matlab-without-gui)  
[zhihu-guide](https://zhuanlan.zhihu.com/p/394298249)  
1. make a new directory for matlab's license
```bash
sudo mkdir -p /usr/local/matlab/R2021a/license
```
2. copy the license file to it  
```bash
sudo cp -P $HOME/opt/2021a_activate/network.lic /usr/local/matlab/R2021a/liscense/.
```
3. modify the inputFile
```bash
sudo cp -P /media/matlab/installer_input.txt /usr/local/matlab
sudo chmod 666 /usr/local/matlab/installer_input.txt
sudo vim /usr/local/matlab/installer_input.txt
```
```bash
# the /usr/local/matlab/installer_input.txt file:
destinationFolder=/usr/local/matlab/R2021a
fileInstallationKey=xxxxxxxx
agreeToLicense=yes
outputFile=/tmp/mathworks_install_ella717.log
enableLNU=yes
improveMATLAB=yes
licensePath=/usr/local/matlab/R2021a/license/network.lic
# be sure to uncomment the line!
```
4. install with modified inputFile  
`sudo bash /media/matlab/install -inputFile /usr/local/matlab/installer_input.txt`  
5. make alias and set the path    
```bash  
export PATH=$PATH:/usr/local/matlab/R2021a/bin
alias matlab="matlab -nodesktop -nodisplay"
```
6. and the same problem remains...  
**-to be continued-**  