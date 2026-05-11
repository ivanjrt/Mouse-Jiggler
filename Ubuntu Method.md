# Create the script:
save it as: `keep_awake.sh`
****
```bash
#!/bin/bash

clear
START_TIME=$(date +%s)

format_elapsed() {
    local elapsed=$(( $(date +%s) - START_TIME ))
    printf "%02d:%02d:%02d" $((elapsed/3600)) $(( (elapsed%3600)/60 )) $((elapsed%60))
}

cleanup() {
    # Restore original settings
    gsettings set org.gnome.desktop.screensaver lock-enabled true
    gsettings set org.gnome.desktop.session idle-delay 300
    echo ""
    echo -e "\e[36mScreen lock restored. Total runtime: $(format_elapsed)\e[0m"
    exit 0
}
trap cleanup SIGINT SIGTERM

# Disable screen lock and idle
gsettings set org.gnome.desktop.screensaver lock-enabled false
gsettings set org.gnome.desktop.session idle-delay 0

echo "Start time: $(date '+%A %m/%d %I:%M:%S %p (%Z)')"
echo "Screen lock disabled. Press Ctrl+C to restore and exit."
echo ""

while true; do
    clear
    echo "Start time: $(date '+%A %m/%d %I:%M:%S %p (%Z)')"
    echo "Elapsed time: $(format_elapsed)"
    echo "-Press Ctrl+C to exit-"
    sleep 30
done
```
Once the file has been created

```
chmod +x keep_awake.sh
```
To run it:
```
./keep_awake.sh
```

