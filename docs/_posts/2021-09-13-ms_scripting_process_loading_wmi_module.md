---
title: "MS Scripting Process Loading WMI Module"
excerpt: "Command and Scripting Interpreter, JavaScript"
categories:
  - Endpoint
last_modified_at: 2021-09-13
toc: true
toc_label: ""
tags:
  - Anomaly
  - T1059
  - Command and Scripting Interpreter
  - Execution
  - T1059.007
  - JavaScript
  - Execution
  - Splunk Enterprise
  - Splunk Enterprise Security
  - Splunk Cloud
  - Endpoint
  - Exploitation
---



[Try in Splunk Security Cloud](https://www.splunk.com/en_us/cyber-security.html){: .btn .btn--success}

#### Description

This search is to detect a suspicious MS scripting process such as wscript.exe or cscript.exe that loading wmi module to process wmi query. This behavior was seen in FIN7 implant where it uses javascript to execute wmi query to parse host information that will send to its C2 server. this anomaly detections is a good initial step to hunt further a suspicious wmi query or wmi related events to the host that may give you good information regarding process that are commonly using wmi query or modules or might be an attacker using this technique.

- **Type**: Anomaly
- **Product**: Splunk Enterprise, Splunk Enterprise Security, Splunk Cloud
- **Datamodel**: [Endpoint](https://docs.splunk.com/Documentation/CIM/latest/User/Endpoint)
- **Last Updated**: 2021-09-13
- **Author**: Teoderick Contreras, Splunk
- **ID**: 2eba3d36-14a6-11ec-a682-acde48001122


#### ATT&CK

| ID          | Technique   | Tactic         |
| ----------- | ----------- |--------------- |

| [T1059](https://attack.mitre.org/techniques/T1059/) | Command and Scripting Interpreter | Execution |


| [T1059.007](https://attack.mitre.org/techniques/T1059/007/) | JavaScript | Execution |





#### Search

```
`sysmon` EventCode =7 Image IN ("*\\wscript.exe", "*\\cscript.exe") ImageLoaded IN ("*\\fastprox.dll", "*\\wbemdisp.dll", "*\\wbemprox.dll", "*\\wbemsvc.dll" , "*\\wmiutils.dll", "*\\wbemcomn.dll") 
| stats min(_time) as firstTime max(_time) as lastTime count by Image EventCode process_name ProcessId ProcessGuid Computer ImageLoaded 
| `security_content_ctime(firstTime)` 
| `security_content_ctime(lastTime)` 
| `ms_scripting_process_loading_wmi_module_filter`
```

#### Associated Analytic Story
* [FIN7](/stories/fin7)


#### How To Implement
To successfully implement this search, you need to be ingesting logs with the process name, parent process, and command-line executions from your endpoints. If you are using Sysmon, you must have at least version 6.0.4 of the Sysmon TA. Tune and filter known instances where renamed rundll32.exe may be used.

#### Required field
* _time
* Image
* EventCode
* process_name
* ProcessId
* ProcessGuid
* Computer
* ImageLoaded


#### Kill Chain Phase
* Exploitation


#### Known False Positives
automation scripting language may used by network operator to do ldap query.


#### RBA

| Risk Score  | Impact      | Confidence   | Message      |
| ----------- | ----------- |--------------|--------------|
| 9.0 | 30 | 30 | $process_name$ loading wmi modules $ImageLoaded$ in $dest$ |




#### Reference

* [https://www.fireeye.com/blog/threat-research/2018/08/fin7-pursuing-an-enigmatic-and-evasive-global-criminal-operation.html](https://www.fireeye.com/blog/threat-research/2018/08/fin7-pursuing-an-enigmatic-and-evasive-global-criminal-operation.html)
* [https://attack.mitre.org/groups/G0046/](https://attack.mitre.org/groups/G0046/)



#### Test Dataset
Replay any dataset to Splunk Enterprise by using our [`replay.py`](https://github.com/splunk/attack_data#using-replaypy) tool or the [UI](https://github.com/splunk/attack_data#using-ui).
Alternatively you can replay a dataset into a [Splunk Attack Range](https://github.com/splunk/attack_range#replay-dumps-into-attack-range-splunk-server)

* [https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/malware/fin7/fin7_js_2/sysmon.log](https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/malware/fin7/fin7_js_2/sysmon.log)



[*source*](https://github.com/splunk/security_content/tree/develop/detections/endpoint/ms_scripting_process_loading_wmi_module.yml) \| *version*: **1**