| Title                | Koadic Execution                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Detects command line parameters used by Koadic hack tool                                                                                                                                           |
| ATT&amp;CK Tactic    |  <ul><li>[TA0002: Execution](https://attack.mitre.org/tactics/TA0002)</li></ul>  |
| ATT&amp;CK Technique | <ul><li>[T1170: Mshta](https://attack.mitre.org/techniques/T1170)</li></ul>  |
| Data Needed          | <ul><li>[DN_0002_4688_windows_process_creation_with_commandline](../Data_Needed/DN_0002_4688_windows_process_creation_with_commandline.md)</li><li>[DN_0003_1_windows_sysmon_process_creation](../Data_Needed/DN_0003_1_windows_sysmon_process_creation.md)</li></ul>  |
| Trigger              | <ul><li>[T1170: Mshta](../Triggers/T1170.md)</li></ul>  |
| Severity Level       | high |
| False Positives      | <ul><li>Pentest</li></ul>  |
| Development Status   | experimental |
| References           | <ul><li>[https://unit42.paloaltonetworks.com/unit42-sofacy-groups-parallel-attacks/](https://unit42.paloaltonetworks.com/unit42-sofacy-groups-parallel-attacks/)</li><li>[https://github.com/zerosum0x0/koadic/blob/master/data/stager/js/stdlib.js#L955](https://github.com/zerosum0x0/koadic/blob/master/data/stager/js/stdlib.js#L955)</li><li>[https://blog.f-secure.com/hunting-for-koadic-a-com-based-rootkit/](https://blog.f-secure.com/hunting-for-koadic-a-com-based-rootkit/)</li></ul>  |
| Author               | wagga |


## Detection Rules

### Sigma rule

```
title: Koadic Execution
id: 5cddf373-ef00-4112-ad72-960ac29bac34
status: experimental
description: Detects command line parameters used by Koadic hack tool 
references:
    - https://unit42.paloaltonetworks.com/unit42-sofacy-groups-parallel-attacks/
    - https://github.com/zerosum0x0/koadic/blob/master/data/stager/js/stdlib.js#L955
    - https://blog.f-secure.com/hunting-for-koadic-a-com-based-rootkit/
tags:
    - attack.execution
    - attack.t1170
date: 2020/01/12
author: wagga
logsource:
    category: process_creation
    product: windows
detection:
    selection1:
        CommandLine:
            - '*cmd.exe* /q /c chcp *'
    condition: selection1
fields:
    - CommandLine
    - ParentCommandLine
falsepositives:
    - Pentest
level: high

```





### es-qs
    
```
CommandLine.keyword:(*cmd.exe*\\ \\/q\\ \\/c\\ chcp\\ *)
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_watcher/watch/5cddf373-ef00-4112-ad72-960ac29bac34 <<EOF\n{\n  "metadata": {\n    "title": "Koadic Execution",\n    "description": "Detects command line parameters used by Koadic hack tool",\n    "tags": [\n      "attack.execution",\n      "attack.t1170"\n    ],\n    "query": "CommandLine.keyword:(*cmd.exe*\\\\ \\\\/q\\\\ \\\\/c\\\\ chcp\\\\ *)"\n  },\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "bool": {\n              "must": [\n                {\n                  "query_string": {\n                    "query": "CommandLine.keyword:(*cmd.exe*\\\\ \\\\/q\\\\ \\\\/c\\\\ chcp\\\\ *)",\n                    "analyze_wildcard": true\n                  }\n                }\n              ],\n              "filter": {\n                "range": {\n                  "timestamp": {\n                    "gte": "now-30m/m"\n                  }\n                }\n              }\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": "root@localhost",\n        "subject": "Sigma Rule \'Koadic Execution\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}Hit on {{_source.@timestamp}}:\\n      CommandLine = {{_source.CommandLine}}\\nParentCommandLine = {{_source.ParentCommandLine}}================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
CommandLine.keyword:(*cmd.exe* \\/q \\/c chcp *)
```


### splunk
    
```
(CommandLine="*cmd.exe* /q /c chcp *") | table CommandLine,ParentCommandLine
```


### logpoint
    
```
(event_id="1" CommandLine IN ["*cmd.exe* /q /c chcp *"])
```


### grep
    
```
grep -P '^(?:.*.*cmd\\.exe.* /q /c chcp .*)'
```



