This is the getting started guide for __benchmarking__. If you're interested in __development__ please visit the [getting started guide for development](../Development/Getting-Started.md).

You will need three computers (or virtual machines) networked together to fill the three benchmark roles of database server, load generation server, and web framework server. We provide a Vagrant script to setup a full benchmark-ready Linux environment in Amazon EC2.

If you wish to benchmark a framework on a non-Amazon cloud, e.g. Azure or Rackspace, or a framework that requires Windows OS or SQL server, there are some limited helper scripts available at the moment. We welcome any pull requests along these lines. While you can always use *manual deployment*, automated scripts exist for many scenarios. Take a look at the [Summary of Script Directories section](../Codebase/Summary-of-Script-Directories.md) to figure out which directory has scripts relevant to your use case. 

*Please note: Running this software will make many modifications to software and settings, so it's recommended you either use a VM or hardware dedicated to this project.*

# Benchmark Suite Deployment

## Required Host Roles

**Client (aka load generation) Server**

* Runs the benchmark client
* Operating system: Ubuntu Server 14.04 LTS 64-bit.

**Database server**

* Hosts databases (e.g. `MySQL`, `PostgreSQL`, `MongoDB`, `SQL Server`).
* Operating system: `Ubuntu Server 14.04 LTS 64-bit` or `Windows Server 2012 Datacenter 64-bit`

**App (aka web framework) Server**

* Hosts the web frameworks under test
* Operating system: `Windows Server 2012 Datacenter 64-bit` or `Ubuntu Server 14.04 LTS 64-bit`

## Manual Deployment

**Evaluation downloads**

If you want to run the benchmark on Windows and/or SQL Server, you can download evaluation versions from Microsoft:
* [Download Windows Server 2012](http://technet.microsoft.com/en-us/evalcenter/hh670538.aspx)
* [Download SQL Server 2012 SP1](http://www.microsoft.com/betaexperience/pd/SQL2012EvalCTA/enus/default.aspx)

**Prerequisites**

Before you get started, there are a couple of steps you can take to make running the tests easier on yourself.

Since the tests can run for several hours, it helps to set everything up so that once the tests are running, you can leave the machines unattended and don't need to be around to enter ssh or sudo passwords.

1. Enable passwordless SSH access to localhost ([search Google for help](https://www.google.com/#hl=en&q=passwordless%20SSH%20access), yielding references such as these: [article 1](http://hortonworks.com/kb/generating-ssh-keys-for-passwordless-login/) [article 2](http://superuser.com/questions/336226/how-to-ssh-to-localhost-without-password) [article 3](https://help.ubuntu.com/community/SSH/OpenSSH/Keys))
2. Enable passwordless sudo access ([Google for help](https://www.google.com/#hl=en&q=passwordless%20sudo)).

After this, clone our repository and run `toolset/run-tests.py --help` for 
detailed guidance. You can also refer to the [Benchmark Suite Setup file](https://github.com/TechEmpower/FrameworkBenchmarks/blob/master/toolset/setup/README.md) for more information. 

**Installation and Usage Details**

If you choose to run TFB on your own computer, you will need to install 
passwordless SSH to your `load server` and your `database server` from 
your `app server`. You will also need to enable passwordless sudo access
on all three servers. If you are only planning to use *verify* mode, then
all three servers can be the same computer, and you will need to ensure
you have passwordless sudo access to `localhost`. 

For all Linux framework tests, we use [Ubuntu 14.04](http://www.ubuntu.com/download/server), so 
it is recommended you use this for development or use. Furthermore, the load server is Linux-only,
even when testing Windows frameworks.

## Windows server setup

* Connect to the Windows server via Remote Desktop.
* Copy `installer-bootstrap.ps1` from "toolset/setup/windows" to the server (use CTRL-C and CTRL-V).
* Copy your Linux client private key too.
* Right click on the installer script and select `Run with PowerShell`.
* Press Enter to confirm.
* It will install git and then launch `installer.ps1` from the repository, which will install everything else.
* The installation takes about 20 minutes.
* Then you have a working console: try `python`, `git`, `ssh`, `curl`, `node` etc. and verify that everything works + PowerShell goodies.

The client/database machine is still assumed to be a Linux box. You can install just the client software via

    python toolset\run-tests.py -s server-private-ip -c client-private-ip -i "C:\Users\Administrator\Desktop\client.key" --install-software --install client --list-tests

but this step is not required if you already installed the Linux server and client as described above.

Now you can run tests:

    python toolset\run-tests.py -s server-private-ip -c client-private-ip -i "C:\Users\Administrator\Desktop\client.key" --max-threads 2 --duration 30 --sleep 5 --name win --test aspnet --type all

## SQL Server setup

* Connect to the SQL Server host via Remote Desktop.
* Run a `Command Prompt` as Administrator.
* Enter this command:

        powershell -ExecutionPolicy Bypass -Command "iex (New-Object Net.WebClient).DownloadString('https://raw.github.com/TechEmpower/FrameworkBenchmarks/master/toolset/setup/sqlserver/setup-sqlserver-bootstrap.ps1')"

* This will configure SQL Server, the Windows Firewall, and populate the database.

Now, when running `run-tests.py`, just add `-d <ip of SQL Server instance>`. This works for the (Windows Server-based) `aspnet-sqlserver-raw` and `aspnet-sqlserver-entityframework` tests.

## Other hosting

The benchmark suite will work on physical or virtual servers that have local network connectivity between them and Internet access for software download.

In general, any private or public hosting can be used, following this procedure:

* Provision the instances (servers) according to the roles, operating system and additional software described in the deploment overview.
* Configure networking as needed.
* Set the Linux hosts for passwordless sudo as described in the instructions for dedicated hardware.

Specific provisioning instructions and/or scripts for particular hosting or cloud services providers are welcome!
