The check reads `/tmp/bogus`. `grep handsonn.local /etc/hosts | awk '{print $1}' > /tmp/bogus` extracts the first column (the IP) into that file.
