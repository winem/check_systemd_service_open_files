# check_systemd_service_open_files
Icinga2 plugin to check the number of open files per systemd service. 

## Dependencies
This plugin requires:
  - Python3

## How it works
First, the plugin uses the provided service name to:
  1. run `service servicename status` to get the PID from the _Main PID_ field and the location of the service file from the _Loaded_ field
  2. fetch the maximum number of open files for this service from the service file (parameter: `LimitNOFILE`)

`pgrep -P` is used to get the pids of all child processes of the PID. 
The plugin does a recursive lookup on the returned pids until no further childs can be found.

The open files in `/proc/{pid}/fd/` except for 0, 1 and 2 are counted and the total number will be provided with the `open_files` metric.

## Performance data
This plugin provides the number of open files for the monitored service.

The metrics will always contain total numbers for the value and thresholds lthough the thresholds can be specified as total numbers as well as percentage values.

## Usage
See the examples below or execute the plugin with -h/--help.

## Examples
Check systemd-timesyncd without `LimitNOFILE` specified in the service file and without defined thresholds:
```
./check_systemd_service_open_files -s systemd-timesyncd
OPEN FILES SYSTEMD-TIMESYNCD OK - systemd-timesyncd_open_files is 11 | systemd-timesyncd_open_files=11
```

Check systemd-timesyncd with the default values. LimitNOFILE was set to 10 for this test:
```
./check_systemd_service_open_files -s systemd-timesyncd
OPEN FILES SYSTEMD-TIMESYNCD CRITICAL - systemd-timesyncd_open_files is > 9 (11) | systemd-timesyncd_open_files=11;8;9
```

Same as above with LimitNOFILE set to 12:
```
./check_systemd_service_open_files -s systemd-timesyncd
OPEN FILES SYSTEMD-TIMESYNCD WARNING - systemd-timesyncd_open_files is > 10 (11) | systemd-timesyncd_open_files=11;10;11
```

Same as above again with LimitNOFILE = 15 and differnt thresholds being provided:
```
# warning threshold of 10 open files
root@m-do-ffm-plugins-01:/tmp# ./check_systemd_service_open_files -s systemd-timesyncd -w 10
OPEN FILES SYSTEMD-TIMESYNCD WARNING - systemd-timesyncd_open_files is > 10 (11) | systemd-timesyncd_open_files=11;10

# warning threshold of 5 and critical threshold of 10
root@m-do-ffm-plugins-01:/tmp# ./check_systemd_service_open_files -s systemd-timesyncd -w 5 -c 10 
OPEN FILES SYSTEMD-TIMESYNCD CRITICAL - systemd-timesyncd_open_files is > 10 (11) | systemd-timesyncd_open_files=11;5;10

# thresholds provided in %
root@m-do-ffm-plugins-01:/tmp# ./check_systemd_service_open_files -s systemd-timesyncd --warning-prct 30 --critical-prct 50
OPEN FILES SYSTEMD-TIMESYNCD CRITICAL - systemd-timesyncd_open_files is > 7 (11) | systemd-timesyncd_open_files=11;4;7
```

Error cases:
```
# combining total numbers and percentage values as thresholds
./check_systemd_service_open_files -s systemd-timesyncd -w 5 --critical-prct 70; echo $?
OPEN FILES SYSTEMD-TIMESYNCD UNKNOWN - please use either total numbers or percentage vales and thresholds. Do not mix them. -w and -c or --warning-prct and --critical-prct.
3

# use a non-existing service
./check_systemd_service_open_files -s foobar; echo $?
OPEN FILES FOOBAR UNKNOWN - Service foobar was not found.
3
```
