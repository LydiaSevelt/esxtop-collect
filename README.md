# esxtop-collect
Daemon has to be deployed on every vSphere server. It collects esxtop's statistics to influxdb for further use, for example, in grafana
![](https://raw.githubusercontent.com/shep89/esxtop-collect/master/screenshots/grafana-cpu.png)
![](https://raw.githubusercontent.com/shep89/esxtop-collect/master/screenshots/grafana-net-disks.png)

# Preparing for first use
1. Enable remote SSH console on vSphere hosts
2. Edit influxdb_server and influxdb_dbname in /bin/esxtop-collect
3. Edit metrics_collect lists for each category to include the metrics you wish to collect. Reference CollectableMetrics.md for full list.
4. Place daemon and init scripts in /bin and /etc/init.d directories
5. Set ohostname to the desired hostname to be reported if you need to override the hostname, leave as None to use system hostname
6. chmod 755 /bin/esxtop-collect /etc/init.d/esxtop-collect
7. chkconfig esxtop-collect on
8. /etc/init.d/esxtop-collect start

# Making installation easier on other hosts in a cluster

Prepare

1. Login to remote SSH console on vSphere host
2. cd / ; rm /esxtop-collect.tgz ; tar -cz -f /esxtop-collect.tgz /bin/esxtop-collect /etc/init.d/esxtop-collect
3. Place esxtop-collect.tgz on a web server

Install

1. wget -O - http://.../esxtop-collect.tgz | tar - xz -C /
2. chkconfig esxtop-collect on
3. /etc/init.d/esxtop-collect start

# Known issues

When pulling the "Group Memory" data if you have VMs with a naming scheme that ends with .[0-9]+ you will not be able to pull metrics.
Processes on the host as listed with the process name followed by .$PID, VM names are listed the same except without the .$PID, this
makes filtering the host processes from the VMs difficult.

Example:

	Host process:
	"\\esxhost1\Group Memory(376:init.33332)\Shared MBytes"
	VM:
	"\\esxhost1\Group Memory(46414637:linuxvm)\Shared MBytes"

VMs named something like linuxbox.12432 will be filtered.
