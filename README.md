# The whole purpose of this combination of tools such as VirtualBox, Vagrant, Ansible to automate the Windows MySQL Installation the software to the C:\ drive and redirect or route the database data location to the D:\

There are some extra options which also part of win_mysql role like the following below:
** Create MySQL database. It can be one or multiple databases at one time. **
** Create MySQL database user. Currently testing model with two simple user1 and user2. **
** Grant MySQL database user to the database. **
** Open a Windows Firewall Port 3306 ready for connection and testing from the Laptop. **

## PreRequisites:
1. MacOS Laptop
2. VirtualBox
3. Vagrant

## Some Instruction to perform on the MacOS Laptop ready for a first run.
"""
1. vagrant box add jborean93/WindowsServer2019 --provider virtualbox

2. brew install ansible

3. brew install pipx

4. pipx inject ansible pywinrm  # if you installed ansible with pipx

5. Edit your zsh shell with the ~/.zshrc file by adding extra lines as follows

** export OBJC_DISABLE_INITIALIZE_FORK_SAFETY=YES **

without adding the above line and activate your zsh. You likely get error

### ERROR
objc[18501]: +[__NSCFConstantString initialize] may have been in progress in another thread when fork() was called.
objc[18501]: +[__NSCFConstantString initialize] may have been in progress in another thread when fork() was called. We cannot safely call it or ignore it in the fork() child process. Crashing instead. Set a breakpoint on objc_initializeAfterForkError to debug.

### Important Note:
You must configure your VirtualBox properly and reedit the inventory.ini, etc files.

The ** 192.168.56.190 ** is my VirtualBox private network. Your may be different and in need of replacing to match your.

6. vagrant up

### If everything configure properly. It should create a Windows 2019 Server with both C:\ and extra raw disk where Ansible will kick in later on to search and format and assign a new drive in alphabet order. The C:\ belong to Windows OS and the next letter is D:\. This is display in my environment. Hope it's also in your too.

If you still get above error, the try adding couple extra into ~/.zshrc file

export DISABLE_SPRING=true
export OBJC_DISABLE_INITIALIZE_FORK_SAFETY=YES
export ANSIBLE_KEEP_REMOTE_FILES=1
"""



## Troubleshoot

If you did not see the ansible first out as follows:

ok: [windows2019] => {
    "changed": false,
    "invocation": {
        "module_args": {
            "data": "pong"
        }
    },
    "ping": "pong"
}

or

ESTABLISH WINRM CONNECTION FOR USER: vagrant on PORT 5986 TO 192.168.53.190 fatal: [windows2019]: UNREACHABLE! => { "changed": false, "msg": "ssl: HTTPSConnectionPool(host='192.168.53.190', port=5986): Max retries exceeded with url: /wsman (Caused by ConnectTimeoutError(<urllib3.connection.HTTPSConnection object at 0x10f6f8290>, 'Connection to 192.168.53.190 timed out. (connect timeout=30)'))", "unreachable": true }

## ESTABLISH WINRM CONNECTION FOR USER: vagrant on PORT 5986 TO 192.168.53.190 fatal: [windows2019]: UNREACHABLE! means your macOS Laptop not communicate with Windows 2019 server via WINRM. By default, the ** jborean93/WindowsServer2019 ** Vagrant image should already configure and open the WINRM. Check your macOS Laptop and make sure WINRM is install as per instruction above.

Also manually login onto the VirtualBox Windows 2019 machine with username=vagrant and password=vagrant as default.

Set-Item -Path WSMan:\localhost\Service\Auth\Basic -Value $true

Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

powershell.exe -File ConfigureRemotingForAnsible.ps1 https://github.com/ansible/ansible-documentation/blob/devel/examples/scripts/ConfigureRemotingForAnsible.ps1 


Now, go to your macOS machine and run the following command below.

ansible windows-dev -i hostfile -m win_ping

127.0.0.1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

ansible-playbook -i hostfile setup.yml --extra-vars=windows-dev" <- You can manually run this, but it is not recommending. 

You should try and get the automation running by execute the vagrant command again i.e

vagrant provision

Again, after troubleshoot and manually get a message ** pong ** appear and you run ** vagrant provision ** to performing all Ansible roles. You can start testing the connection from the macOS Laptop.

Once more time. Do as follows

vagrant destroy -f	# Delete out Windows.

vagrant up		# create a Windows again and hopefully this time. should not need to login and re-run those above commands.

ENJOY!!!!
