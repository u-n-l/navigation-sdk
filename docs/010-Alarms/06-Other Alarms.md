# Other Alarms

Receive alerts for environmental changes such as entering or exiting tunnels and transitions between day and night.

## Get Notified for Tunnel Events
Set up notifications for entering and exiting tunnels using the `AlarmListener`:
```
AlarmListener alarmListener = AlarmListener(
    onTunnelEntered: () {
        showSnackbar("Tunnel entered");
    },
    onTunnelLeft: () {
        showSnackbar("Tunnel left");
    },
);
```

## Get Notified for Day and Night Transitions
Receive notifications when your location transitions between day and night based on geographical region and seasonal changes:

```
AlarmListener alarmListener = AlarmListener(
    onEnterDayMode: () {
        showSnackbar("Day mode entered");
    },
    onEnterNightMode: () {
        showSnackbar("Night mode entered");
    },
);
```
