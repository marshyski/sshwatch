SSHWATCH ``v2.0``
========
Intrusion Prevention System ( **IPS** ) for Secure Shell ( **SSH** ) sourced from https://code.google.com/p/sshwatch/ - krink@csun.edu THANKS HOMIE!

Why use this?
-------------
This project is similar to [DenyHosts][1] but enables better logging using [NMAP][2] and [Dig][3].

Technical Overview
------------------
Continuously tail (subprocess tail -F) the system security logs, searching for a match on "sshd", "Failed password", "Invalid user". With a match, add the source ip to a list. After number of sequentially matched failed attempts, in consecutive order, from the same source ip, under the thresh hold time, puts the source ip in iptables block and nmap/dig is ran. The "clear" value will remove the iptables block at selected interval.

                                            ----------------------
    ----------    --------    ----------- / |iptables Blocks BFer| \
    |        |    |      |    |         |   ----------------------  -----------------
    |SSH BFer| -> |System| -> |sshwatchd|                           |Clear iptables |
    |        |    |      |    |         |   ----------------------  |BFer in 60 mins|
    ----------    --------    ----------- \ |NMAP/dig Probed BFer|  -----------------
                                            |/var/log/nmap.log   |
                                            ----------------------
	BFer = Brute Forcer 


Requirements
------------
  * Linux (Redhat, Debain)
  * root or equivalent 
  * OPENSSH Server
  * Python 2.4+
  * iptables (IPv4)
  * nmap (optional)
  * dig (bind-utils) (optional)

Installation
------------

**From Source**

    git clone https://github.com/marshyski/sshwatch.git
    sshwatch  -> /etc/init.d
    sshwatchd -> /usr/sbin

**From Packages**

    rpm -ivh sshwatch-2.0-1.noarch.rpm #Redhat only
    dpkg -i sshwatch_2.0_all.deb #Debian only

**Post Install**

    chmod -f 0700 /etc/init.d/sshwatch /usr/sbin/sshwatchd
    chown -f root:root /etc/init.d/sshwatch /usr/sbin/sshwatchd
    chkconfig sshwatch on #Redhat only
    /etc/init.d/sshwatch start

Usage
-----
**Variables in sshwatchd**

    thresh   = number of seconds between consecutive attempts, default is 60
    attempts = number of consecutive attempts, default is 4
    clear    = number of seconds elapsed to clear active source blocks, default is 3600
    nmaplog  = nmap probes are logged here, default is /var/log/nmap.log
    nmap     = nmap probe malicious source and stored in nmaplog, default is 0 (off)

**Run in standalone / no-daemon / DEBUG mode**

    ./sshwatchd /var/log/auth.log >/var/log/sshwatch.log 2>&1 & #Debian
    ./sshwatchd /var/log/secure >/var/log/sshwatch.log 2>&1 &   #Redhat

Changes from 1.0 to 2.0
-----------
- Block all traffic from an IP not just on source IP / Port 22
- NMAP/Dig source IP and store in /var/log/nmap.log
- Packages, curtisity of [fpm][4] building.
- A rich README ^_^

Help & Feedback
---------------
You can email (marshyski@gmail.com) me directly if you need help, submit an issue or pull request.  Fork it.


  [1]: http://denyhosts.sourceforge.net/
  [2]: http://nmap.org/
  [3]: http://linux.die.net/man/1/dig
  [4]: https://github.com/jordansissel/fpm
