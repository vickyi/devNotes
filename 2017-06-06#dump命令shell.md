### dump
```
#!/usr/bin/env bash
#echo "Flow has been called succesfully, the real function will be started soon."
SCRIPT_PATH=~+
echo ${SCRIPT_PATH}
if [ $# -lt 2 ]
then
    echo "dump param error"
    exit 1
fi

#remoteIp=hive-exe-abcplus-000.abcplus
params=("$@")
echo "params:${params[@]}"

java -jar /home/hadoop/abcplus_workspace/software/lib/Dump.jar ${params[@]}
```