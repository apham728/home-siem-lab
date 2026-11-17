# Custom Detection Rules

## T1016 System Network Configuration Discovery
```xml
<group name="windows,sysmon,">
  <!-- base rule checking for commands related to T1016 -->
  <rule id="100030" level="3">
    <if_sid>61603,92031,92032,92036</if_sid>
    <field name="win.eventdata.image" type="pcre2">(?i)\\(arp|nbtstat|net|ipconfig|netsh)\.exe$</field>
    <description>Sysmon: network discovery tool executed ($(win.eventdata.image))</description>
  </rule>

  <!-- detecting several different tools from same parent in short timeframe -->
  <rule id="100031" level="7" frequency="4" timeframe="30">
    <if_matched_sid>100030</if_matched_sid>
    <same_field>win.eventdata.parentProcessGuid</same_field>
    <description>Sysmon: multiple network discovery tools run from the same parent within 30s</description>
    <mitre>
      <id>T1016</id>
    </mitre>
  </rule>
</group>
```

The rule detects several discovery tools (`arp`, `nbtstat`, `net`, `ipconfig`, `netsh`) run from the same parent process within 30 seconds. It relies on Sysmon's process creation event in order to send out an alert with the ID T1016 with rule 100031 to signify multiple tools share a parent process.  

I wrote this rule because the alerts sent from Wazuh's default rules produced multiple generic alerts for the T1016 test, like `Suspicious Windows cmd shell execution` and `Discovery activity executed`, which could be easily overlooked. I wanted to make a rule that targeted this type of behavior and sent out a more specific alert that would allow analysts to tell someone is looking through their network. 

I tested this custom rule by running Atomic test T1016-1 and confirming that rule 100031 was sent out along with the T1016 tag.  
**Before**
![Alerts before rule](../screenshots/atomic-test-2-before-rule.png)
> Before: no alert was tagged with T1016 and the logs seem generic enough that someone might ignore it

**After**
![Alerts before rule](../screenshots/atomic-test-2-after-rule.png)
> After: a log with the rule 100031 is seen and it is tagged with T1016
