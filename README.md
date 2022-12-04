SYNOPSIS

        wait_for [--option=value] [command]


DESCRIPTION

                this command waits for a host:port to be reachable. It either executes a command directly or if no command is given it returns 0 or an error code. 
                the first non option argument is recognized as the beginning of the command. The command has to go last.
                wait_for needs at least 1 argument. Either --host or a command.

OPTIONS

                --host=127.0.0.1   # A resolvable hostname or IP address to wait for Default is 127.0.0.1
                --help             # Shows this help page
                --sleep=1          # Number of seconds to sleep between checks. Default 1 seconds
                --delay=0          # Number of seconds to wait before starting to poll. Default 0 seconds
                --wait=0           # Number of seconds to wait after the host is reachable. Default  0 seconds
                --timeout=300      # Maximum number of seconds to wait for, when used with another condition it will force an error. Default 300 seconds
                --port=22          # Port number to poll Default is 22 (ssh)

COMMAND

                every non option argument is considered as beginning of the command wait_for is supposed to execute.
                if no command is given wait_for returns an exit status code.


EXAMPLES

                wait_for --host=myserver scp file  root@myserver:/folder/  || echo "Timeout. myserver not reachable"
                wait_for --host=myserver ssh myserver reboot && echo "myserver is rebooting"
                wait_for --host=myserver --port=80  &&  curl --HEAD myserver  || echo "My Server not reachable" 
                wait_for --sleep=5

                if wait_for --host=myserver;
                then
                        scp stuff 
                        ssh "do stuff"
                        do more stuff
                else
                        echo "can't do stuff"
                fi


EXIT STATUS

                wait_for exits with 
                        0 on success
                        84 on connect timeout
                        92 invalid argument
                or the return code of "command"
