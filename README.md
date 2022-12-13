NAME

    wait_for - wait_for a host to be reachable


SYNOPSIS

    wait_for ([--option=value] | host) [command]


DESCRIPTION

    this command waits for a host:port to be reachable. It either executes a command directly or if no command is given it returns 0 or an error code.
    the first non option argument is recognized as the beginning of the command. The command has to go last.
    wait_for needs at least 1 argument. Either --host or a command.

OPTIONS

    --sleep=1          # Number of seconds to sleep between checks. Default is 1 sec. sleep can't be bigger than timeout
    --delay=0          # Number of seconds to wait before starting to check the host. Default is 0 secs
    --wait=0           # Number of seconds to wait after the host is reachable. Default is 0 sec
    --timeout=300      # Maximum number of seconds to wait for, if run up it will force an error. Default 300 seconds.
                Is only checked every --sleep seconds, so real timeout is only proximately --timeout
    --port=22          # Port number to poll Default is 22 (ssh)
    --proto=icmp       # Proto ICMP or TCP. If ICMP port will be ignored
    --help             # Shows this help page
    --host=host        # A resolvable hostname or IP address to wait for.

COMMAND

    if --host is not give then the first argument is used as host.
    In any other case, every non option argument is considered as beginning of the command wait_for is supposed to execute.
    if no command is given "wait_for" just returns an exit status code after finishing.


EXAMPLES

    wait_for myserver scp file root@myserver:/folder/  || echo "Timeout. myserver not reachable"
    wait_for --port=80 myserver && curl --HEAD http://myserver/ && notify-send --hint string:sound-name:dialog-ok "Server back up"
    wait_for --icmp --wait=300 my-super-secure-server && ssh my-super-secure-server systemctl restart start   # better not provoke fail2ban
    pon dsl; wait_for --icmp --wait=10 --host=dyndns.mydomain.com sudo /etc/firewall restart.

    if ./wait_for --mad4milk.de;
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


EXIT STATUS

    wait_for exits with ( from errno-base.h/errno.h taken from the linux kernel )
    0  - on success
    22 - invalid argument
    62 - time expired
    or the return code of COMMAND

ALTERNATIVES

    If you're only looking for ssh/scp trying to connect until the ssh is reachable again you should take a
    look into the ssh_config manpage."ConnectionAttempts" could be the option for you.


