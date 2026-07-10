`echo '* * * * * date >> /tmp/heartbeat' | crontab -` installs a per-minute job. The check greps the installed crontab (or /var/spool/cron/crontabs/root) for 'heartbeat'.
