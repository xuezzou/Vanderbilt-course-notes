### Learn basic shell commands
- file cmds
    + Change directories `cd`
    + Create a new directory `mkdir` `mkdir -p`
    + Move a file / directory `mv`
    + Copy a file / directory `cp`
    + Move / Copy files using wildcards
    + Delete files and non-empty directories `rm`
    + List the contents of a directory along with hidden files `ls -a`
    + Cat a file `cat`
    + Less a file `less`
    + Tail a log file `tail`
- process permission
    + [Check the permissions on a file](https://askubuntu.com/questions/528411/how-do-you-view-file-permissions) `ls -l fileName`
    + Change the permissions on a file `chmod`
    + [Change the owner of a file](http://linuxcommand.org/lc3_lts0090.php) `[chown]`
- [vim](https://www.radford.edu/~mhtay/CPSC120/VIM_Editor_Commands.htm)
    - Open vi `vi`
    - Switch between insert and command mode
        - From command mode to insert mode  type `i` ( see details below)
        - From insert mode to command mode    type `Esc` (escape key)
    - Navigate a file in vi (in commands mode)
        - `h` Moves the cursor one character to the left
        - `l` Moves the cursor one character to the right
        - `k` Moves the cursor up one line
        - `j` Moves the cursor down one line
        - `nG` or :n Cursor goes to the specified (n) line (ex. 10G goes to line 10)
    - Jump to an arbitrary line in vi
    - Search for a text string in vi
        - `/word` in cmd mode
    - Edit the text in vi
        - `:wq` write and quit
    - Exit a file without saving changes vi
        - `q!`
    - Save a file in vi
        - `:x`

- [Send the output of a command to a file](https://askubuntu.com/questions/420981/how-do-i-save-terminal-output-to-a-file)
- [Pipe the output of one command to another](https://www.geeksforgeeks.org/piping-in-unix-or-linux/)
    - e.g. `ls -l | more`

- [environment variable](https://codeburst.io/linux-environment-variables-53cea0245dc9)
    + [Set an environment variable](https://askubuntu.com/questions/58814/how-do-i-add-environment-variables)
    + Echo an environment variable
        - `echo $VARNAME`

- SSH to another machine and close the connection
    - `ssh user@host` connect to *host* as *user*
    - `ssh -p port user@host` connect to *host* on port *port* as *user*
- Understand the root user and sudo
- Understand the home directory
    - `/home` is where users keep their personal work. In general, this is the only place users are allowed to write files. This keeps things nice and clean :-)
- List the currently running processes and how much CPU / memory they are consuming 
    - `ps` display you currently active processes
    - `top` display all running processes
    - `kill pid` kill process id *pid*
- Understand what the PATH is
    - This ENV stores the path of all the directories which holds binary files you want to execute just by specifying the name of the file and not by relative or absolute path.
    - `echo $PATH`
- Be able to add entries to your PATH
    - `bash_rc` `export PATH="$PATH:/usr/local/bin/"`
- Understand and know how to modify your BASH configuration `.bashrc`, `.bash_profile`, etc. (you can graduate to other shells, but start with BASH)

### Basic Linux Configuration, Users, Permissions, and Process Commands
#### Vagrant Basics
- Useful changes to the Vagrantfile
    ```
    Change these in Vagrantfile:

    # Add a known address that we can get into the VM from
    config.vm.network "private_network", ip: "192.168.2.2"
    # Automatically deploy everything from ./deploy to /homne/vagrant/deploy on the guest
    config.vm.provision "file", source: "./deploy", destination: "/home/vagrant/deploy"

    Reload the changes:

    vagrant reload --provision
    ```
- Starting Vagrant `vagrant up`
- SSHing into Vagrant `vagrant ssh`
- SSH
    - Connect to remote machines
    - In this case, connecting to local VM

#### Host Configuration
- Anything prefixed with "#" is a comment
- Find and install packages 
    - Apache
    ```
    apt-cache search apache
    #or
    apt-cache search apache | grep apache

    sudo apt-get install apache2
    ```
- Creating Setup Scripts for Vagrant
    - Things to read about
        - apt-get install
        - BASH
- List installed packages `apt list --installed`
- List files installed by package `dpkg -L apache2`

### Attack Surface Understanding
#### Users
- Current user
`whoami`
- Execute a command as super user
`sudo <cmd>`
- Execute a command as if you are another user (www-data)
    ```
    sudo -u www-data find /var/ -type f -executable
    ```
- List all users
    ```
    awk -F: '{ print $1}' /etc/passwd
    ```
- List logged in users
    ```
    w
    w vagrant
    who 
    users
    ```
- See the last time someone logged in
    ```
    last
    lastlog
    ```
- Add user
    ```
    useradd -m -d /home/bob bob
    ```
- Set their password
    ```
    passwd bob
    ```
- Add bob to the sudo group
    ```
    usermod -a -G sudo bob
    ```
- Switch to another user
    ```
    su bob
    ```
- Add detailed auditing of users
    ```
    sudo apt-get install acct
    ```
- Audit user
    ```
    sudo lastcomm vagrant
    ```
- Who was the last person to add a user
    ```
    sudo lastcomm useradd
    ```

#### Data
- Change directory
    ```
    cd /home/vagrant
    ```
- Display file contents
    ```
    cat /home/vagrant/.bash_history
    ```
- Other ways to see file contents interactively
    ```
    more /home/vagrant/.bash_history
    ```
- Follow a file as it changes
    ```
    tail -f /home/vagrant/.bash_history
    ```
- Quickly create a file by echoing output into it
    ```
    echo "echo bob" > ./somefile
    ```
- Edit a file with vi
    ```
    Beyond the scope of this tutorial, go see https://www.tutorialspoint.com/unix/unix-vi-editor.htm

    To escape vi, you can always type "ESC", then ":q!"
    ```
- List files and permissions
    ```
    ls -alt /home/vagrant
    ```
- Linux file permissions
    ```
    `drwxr-xr-x 4 vagrant vagrant 4096 Jun 20 20:02 .
    -rw------- 1 vagrant vagrant   65 Jun 20 20:02 .bash_history
    drwx------ 2 vagrant vagrant 4096 Jun 20 20:00 .ssh
    drwx------ 2 vagrant vagrant 4096 Jun 20 20:00 .cache
    drwxr-xr-x 4 root    root    4096 Jun 20 20:00 ..
    -rw-r--r-- 1 vagrant vagrant  220 Apr 11 08:32 .bash_logout
    -rw-r--r-- 1 vagrant vagrant 3771 Apr 11 08:32 .bashrc
    -rw-r--r-- 1 vagrant vagrant  655 Apr 11 08:32 .profile

    [Is Dir?][Is Sym Link?][User Read][User Write][User Exec][Group Read][Group Write][Group Exec][Others Read][Others Write][Others Exec] [Ignore] [Owner] [Group]
    ```
- Add execute permission to file for current user
    ```
    chmod +x /home/vagrant/deploy/hello.txt
    ```
- Remove write and execute permission from file for group
    ```
    chmod g-xw /home/vagrant/deploy/hello.txt
    ```
- Remove read,write,execute permission from file for others
    ```
    chmod o-rxw /home/vagrant/deploy/hello.txt
    ```
- See what files are owned by user / group
    ```
    sudo find /var/ -user www-data
    ```
- See what files someone can write to
    ```
    find /etc -type f -readable
    ```
- See what files another user can execute
    ```
    sudo -u www-data find /var/ -type f -executable
    ```

#### Running Instructions / Processes
- List running processes
    ```
    ps -few
    ```
    - Process ID
    - User of the process
    - Group of the user
    - Permissions of the group
- Where did that process come from
    ```
    sudo ls -l /proc/<pid>/exe
    ```
- What binary is used when I run a command
    ```
    which ls
    ```
- What files is a process using
    ```
    sudo lsof | grep apache2
    ```
- What processes are listening for connections from the network
    ```
    sudo lsof -i | grep LISTEN
    sudo lsof -P -i :80
    ```
- See network interfaces
    ```
    ifconfig
    ```
- See who is sending lots of traffic
    ```
    sudo apt-get install nethogs
    sudo nethogs enp0s3
    ```
- Show resource consumption
    ```
    top
    ```
- Some sensitive places on Linux
    ```
    /etc/passwd
    /home/some-user/.ssh/
    /usr/bin 
    /proc
    ```

### More
- [curl command tutorial](https://www.booleanworld.com/curl-command-tutorial-examples/)
- [curl exercise](https://jvns.ca/blog/2019/08/27/curl-exercises/)
- [SSH Tutorial](https://support.suso.com/supki/SSH_Tutorial_for_Linux)
- [Checkout Remote Git Branches](https://www.git-tower.com/learn/git/faq/checkout-remote-branch)
    - git checkout for Remote Branches
    - `git branch -a`
    - `git checkout --track origin/photos-with-users`