NAME

    wait_for - wait_for a host to be reachable


SYNOPSIS

    wait_for ([--option=value] | host) [command]


DESCRIPTION

this command waits for a host:port to be reachable. It either executes a command directly or if no command is given it returns 0 or an error code.
the first non option argument is recognized as the beginning of the command. The command has to go last.
wait_for needs at least 1 argument. Either --host or a command.

OPTIONS

    --sleep=1          # Number of seconds to sleep between checks. (default:1)
    --delay=0          # Number of seconds to wait before starting to check the host. (default:0)
    --wait=0           # Number of seconds to wait after the host is reachable. (default:0)
    --timeout=300      # Number of seconds after which wait_for should abord with an timeout. (default:1)
    --icmp             # ping host instead of a tcp connection to port.
    --proto=icmp       # Proto ICMP or TCP.
    --port=22          # Port number to poll (Default:22/ssh) (unless icmp)
    --host=host        # A resolvable hostname or IP address.
    --help             # Shows this help page

COMMAND

if --host is not give then the first argument is used as host.
In any other case, every non option argument is considered as beginning of the command wait_for is supposed to execute.
if no command is given "wait_for" just returns an exit status code after finishing.


NETCATPATH

    wait_for is looking for netcat, nc.openbsd, nc.traditional, or nc in PATH.
    If the binary has a different name it can be enforced by setting the environment variable NETCATCMD 
    export NETCATCMD=/usr/local/bin/netcat.bin


EXAMPLES

    wait_for myserver scp file root@myserver:/folder/  || echo "Timeout. myserver not reachable"
    wait_for --port=80 myserver && curl --HEAD http://myserver/ && notify-send --hint string:sound-name:dialog-ok "Server back up"
    wait_for --icmp --wait=300 my-super-secure-server && ssh my-super-secure-server systemctl restart start   # better not provoke fail2ban
    pon dsl; wait_for --icmp --wait=10 --host=dyndns.mydomain.com sudo /etc/firewall restart.

    if wait_for --wait=300 mad4milk.de ;
    then
        echo "do stuff"
    else
        case $? in
            22) echo "invalid argument. can't do stuff";;
            66) echo "timeout. can't do stuff";;
            *)  echo "$? something unexpectet happend";;
        esac
    fi


KNOWNBUGS

because I use a bash socket I can't control the timeout of the socket. I will add a netcat option in the future.
Under some circumstances the there are additional 30 seconds of sleep (the socket timeout) every poll.
This will reduce the poll rate and timeout will take longer. Up to 29 seconds.

--timeout is not percise because 'timeouts' of the check can introduce further delays.
    


EXIT STATUS

wait_for exits with ( from errno-base.h/errno.h taken from the linux kernel )

    0  - on success
    22 - invalid argument
    62 - time expired
    or the return code of COMMAND
    

ALTERNATIVES

If you're only looking for ssh/scp trying to connect until the ssh is reachable again you should take a
look into the ssh_config manpage."ConnectionAttempts" could be the option for you.


