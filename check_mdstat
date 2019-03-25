#!/usr/bin/env bash

# nagios script checks for failed raid device
# linux software raid /proc/mdstat
# karl@webmedianow.com 2013-10-01

STATE_OK=0
STATE_WARNING=1
STATE_CRITICAL=2
STATE_UNKNOWN=3
STATE_DEPENDENT=4

PATH=/bin:/usr/bin:/sbin:/usr/sbin
export PATH

usage() {
cat <<-EOE
Usage: $0 mdadm_device total_drives

  mdadm_device is md0, md1, etc...
  total_drives is 2 for mirror, or 3, 4 etc...

Nagios script to check if failed drive in /proc/mdstat

Example: raid 2 (2 disk mirror)
  /opt/nagios/libexec/check_mdstat.sh md0 2

Example: raid 5 with 8 disks
  /opt/nagios/libexec/check_mdstat.sh md0 8

EOE
exit $STATE_UNKNOWN
}

if [ $# -lt 2 ]; then
  usage
fi

cmd_device="$1"
drive_num="$2"

U=""
for i in $(seq 1 $drive_num);
do
  U="${U}U"
done

uu="[${U}]"
nn="[${drive_num}/${drive_num}]"

#cat /proc/mdstat | grep -A 1 ^md1 | tail -1 | awk '{print ($(NF))}'
# [UUUUUUUU] is OK raid
# [_U] is Failed Drive

# check if we have correct device...
if cat /proc/mdstat | grep ^${cmd_device} | awk '{print $1}' | grep ^${cmd_device}$ >/dev/null 2>&1
then
  device=$cmd_device
else
  echo "Couldn't match $cmd_device"
  exit $STATE_UNKNOWN 
fi

u_status=$(cat /proc/mdstat | grep -A 1 ^${device} | tail -1 | awk '{print ($(NF))}')
n_status=$(cat /proc/mdstat | grep -A 1 ^${device} | tail -1 | awk '{print ($(NF-1))}')

if [ $uu = $u_status ] && [ $nn = $n_status ]; then
  echo "OK:  $device $n_status $u_status"
  exit $STATE_OK
else
  echo "FAIL:  $device $n_status $u_status"
  exit $STATE_CRITICAL
fi


