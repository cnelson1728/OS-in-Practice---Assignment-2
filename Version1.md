#!/bin/bash

DIALOG_CANCEL=1
DIALOG_ESC=255
HEIGHT=0
WIDTH=60

display_result() {

	dialog --title "$1" \
	--no-collapse \
	--msgbox "$result" 0 0

}

while true; do
exec 3>&1
selection=$(dialog \
--backtitle "Operating Systems in Practice - Assingment 2" \
--title "Current Directory: $PWD" \
--clear \
--cancel-label "Exit" \
--menu "Please select:" $HEIGHT $WIDTH 3 \
"1" "Display System Information" \
"2" "Display Disk Space" \
"3" "Display Home Space Utilization" \
2>&1 1>&3)
exit_status=$?
exec 3>&-

case $exit_status in
$DIALOG_CANCEL)
clear
echo "Program terminated."
exit
;;
$DIALOG_ESC)
clear
echo "Program aborted." >&2
exit 1
;;
esac

case $selection in
0 )
clear
echo "Program terminated."
;;
1 )
result=$(echo "Hostname: $HOSTNAME"; uptime)
display_result "System Information"
;;
2 )
result=$(df -h)
display_result "Disk Space"
;;
3 )
if [[ $(id -u) -eq 0 ]]; then
result=$(du -sh /home/* 2> /dev/null)
display_result "Home Space Utilization (All Users)"
else
result=$(du -sh $HOME 2> /dev/null)
display_result "Home Space Utilization ($USER)"
fi
;;
esac
done
