NAME
        wait_for - wait_for a host to be reachable


SYNOPSIS

        wait_for [--option=value] [command]


DESCRIPTION

                this command waits for a host:port to be reachable. It either executes a command directly or if no command is given it returns 0 or an error code. 
                the first non option argument is recognized as the beginning of the command. The command has to go last.
                wait_for needs at least 1 argument. Either --host or a command.

OPTIONS

                --host=127.0.0.1   # A resolvable hostname or IP address to wait for Default is 127.0.0.1
                --help             # Shows this help page
                --sleep=1          # Number of seconds to sleep between checks. Default 1 seconds. Sleep has to be a only fraction of timeout.
                --delay=0          # Number of seconds to wait before starting to poll. Default 0 seconds
                --wait=0           # Number of seconds to wait after the host is reachable. Default  0 seconds
                --timeout=300      # Maximum number of seconds to wait for, if run up it will force an error. Default 300 seconds. 
                                    Is only checked every --sleep seconds, so real timeout is only proximately --timeout
                --port=22          # Port number to poll Default is 22 (ssh)
                --proto=icmp       # Proto ICMP or TCP. If ICMP port will be ignored

COMMAND
                every non option argument is considered as beginning of the command wait_for is supposed to execute.
                if no command is given wait_for returns an exit status code.


EXAMPLES
                wait_for --host=myserver scp file  root@myserver:/folder/  || echo "Timeout. myserver not reachable"
                wait_for --host=myserver ssh myserver reboot && echo "myserver is rebooting"
                wait_for --host=myserver --port=80  &&  curl --HEAD myserver  || echo "My Server not reachable" 
                pon dsl; wait_for --proto=icmp --wait=10 --host=dyndns.mydomain.com sudo /etc/firewall restart.

                if wait_for --host=mad4milk.de;
                then
                        scp stuff 
                        ssh "do stuff"
                        do more stuff
                else
                        echo "can't do stuff"
                fi      
KNOWNBUGS

                because I use a bash socket I can't control the timeout of said socket. I may add a netcat option.
                Under some circumstances the there are additional 30 seconds of sleep (the socket timeout) every poll. 
                This will reduce the poll rate and timeout will take longer. Up to 29 seconds. 


EXIT STATUS
                wait_for exits with 
                        0 on success
                        84 on connect timeout
                        92 invalid argument
                or the return code of "command"
