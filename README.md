# Server Audit Helpers: README

Latest version: 0.3.1  
Author: Kevin Chan <kefin@makedostudio.com>

This package resides at:

* [https://bitbucket.org/kchan/server-audit-helpers](https://bitbucket.org/kchan/server-audit-helpers)

### What is This Repository For?

The package is a set of wrapper scripts to help you run security audit tools to scan your servers. There are default wrapper scripts to run the following tools -- note: you must also install these tools on your server:

* lynis
* lsat
* rkhunter

**The main `run_audit` script**

This is a wrapper script to run security tools and email the output to system administrators.

**The `dummy` script**

A "dummy" script template is included as an example of how to create a new sub-script to use with the main `run_audit` script.

The following are installed but disabled (in the "bin/disabled" folder):

* chkrootkit
* tripwire

**Again, IMPORTANT:** The security tools must be already installed on your server for the wrapper scripts to work.

### How to Install the Package

You must have root privileges to install and use the audit scripts.

The following example commands will download the audit scripts to a directory called `packages` in `/usr/local/src`,

    cd /usr/local/src
    [ ! -d "packages" ] && sudo mkdir packages

Clone the `hg` repository from bitbucket.org:

    sudo hg clone https://kchan@bitbucket.org/kchan/server-audit-helpers

Or unpack tarball into your server's source directory (this example uses a directory called `packages` in `/usr/local/src`):

    cd /usr/local/src/packages
    sudo tar -xvzf audit.tar.gz

`cd` into audit tools directory and run the install script:

    cd server-audit-helpers
    sudo ./install-scripts
    # or, use the -y/--yes option to bypass the prompt
    sudo ./install-scripts -y

The script will install the audit scripts in `/usr/local/audit`.

Now, edit the configurations file located in `/etc/audit`:

    cd /etc/audit
    sudo vi run_audit.conf

Customize the settings in the `run_audit.conf` configuration file according to your local server setup. Be sure to add your sysadmin email address to `RECIPIENTS` so the audit scripts can send you the reports:

    RECIPIENTS="your-sysadmin@your-domain.com"

Be sure to add a `logrotate` task for the audit logs (by default these are located in `/var/log/audit`):

    sudo vi /etc/logrotate.d/audit

Enter the following tasks in the file:

    /var/log/audit/*.log {
        daily
        missingok
        rotate 30
        compress
        delaycompress
        notifempty
        create 640 root adm
    }


### How to Use

Note: The `run_audit` script must be run by root.

You can run the top-level wrapper script on the command line:

    sudo /usr/local/audit/bin/run_audit

Or, most likely you would want to create a cron task to run the script at regular intervals:

    sudo crontab -e

add:

    # run security audit tools each night
    0 0 * * *  /usr/local/audit/bin/run_audit > /dev/null 2>&1

Or run using the "at" daemon:

    echo /usr/local/audit/bin/run_audit | sudo at 12am

To execute individual scripts by specifying them as parameters to the `run_audit` script:

    sudo /usr/local/audit/bin/run_audit lynis
    sudo /usr/local/audit/bin/run_audit lsat
    sudo /usr/local/audit/bin/run_audit rkhunter lsat lynis

You can also add custom audit scripts to the `/usr/local/audit/bin` directory. Use the `dummy` script in `/user/local/audit/bin` as a template.

### Contact

If you have questions about this package, please contact Kevin Chan at <kefin@makedostudio.com>.
