Create the file:

```
#!/bin/bash
# save as ~/keep_awake.sh

echo "Keeping Mac awake... Press Ctrl+C to stop."
caffeinate -disu
echo "Done. Mac can sleep normally now."
```

then add the executable: `chmod +x ~/keep_awake.sh` 

then run the file: `./keep_awake.sh`
