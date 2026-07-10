Kind `CronJob`, `spec.schedule: "* * * * *"`. The check just needs a CronJob named heartbeat with a non-empty schedule — you don't have to wait for it to fire.
