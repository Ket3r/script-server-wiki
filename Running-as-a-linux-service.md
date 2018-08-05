Below are some examples of how to run script-server as a linux service.

## systemd
The configuration should go to /lib/systemd/system/script-server.service  

You have to modify **ExecStart** to suit your path
```
[Unit]
Description=Script Server
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
ExecStart=/usr/bin/python3 /opt/script-server/launcher.py

[Install]
WantedBy=multi-user.target
```

The service can be started/stopped with the following commands:
```
systemctl enable script-server.service
systemctl start script-server.service
systemctl stop script-server.service
```


## init.d
Modify **scriptsrvexec** according to your paths
```
# chkconfig: 345 50 50
# description: Script-server service

# Source function library.
. /etc/rc.d/init.d/functions

scriptsrvexec="/home/script-server/script-server.py"
prog=$(basename $scriptsrvexec)

lockfile=/var/lock/subsys/${prog}
pidfile="/var/run/${prog}.pid"
logfile="/var/log/${prog}.log"
 
start() {
    [ -x $scriptsrvexec ] || exit 5

    echo -n $"Starting $prog: "
    touch $logfile $pidfile
    chown script-server:script-server $logfile
    chown script-server:script-server $pidfile

    daemon --user script-server "{ $scriptsrvexec &>> $logfile & }; echo \$! >| $pidfile"
    retval=$?
    echo
    [ $retval -eq 0 ] && touch $lockfile
    return $retval
}
 
stop() {
    echo -n $"Stopping $prog: "
    killproc -p $pidfile $scriptsrvexec 2>> $logfile
    retval=$?
    echo
    [ $retval -eq 0 ] && rm -f $lockfile
    return $retval
}
 
restart() {
    stop
    start
}
 
reload() {
    echo -n $"Reloading $prog: "
    killproc $scriptsrvexec -HUP
    RETVAL=$?
    echo
}
 
rh_status() {
    status $prog
}
 
rh_status_q() {
    rh_status >/dev/null 2>&1
}
 
 
case "$1" in
    start)
        rh_status_q && exit 0
        $1
        ;;
    stop)
        rh_status_q || exit 0
        $1
        ;;
    restart)
        $1
        ;;
    reload)
        rh_status_q || exit 7
        $1
	;;
    status)
	rh_status || exit 0
	;;
    *)
        echo $"Usage: $0 {start|stop|status|restart|reload}"
        exit 2
esac
```