# sysmon-modular | A Sysmon configuration repository for everybody to customise

[![license](https://img.shields.io/github/license/olafhartong/sysmon-modular.svg?style=flat-square)](https://github.com/olafhartong/sysmon-modular/blob/master/license.md)
![Maintenance](https://img.shields.io/maintenance/yes/2022.svg?style=flat-square)
[![GitHub last commit](https://img.shields.io/github/last-commit/olafhartong/sysmon-modular.svg?style=flat-square)](https://github.com/olafhartong/sysmon-modular/commit/master)
![Build Sysmon config with all modules](https://github.com/olafhartong/sysmon-modular/workflows/Build%20Sysmon%20config%20with%20all%20modules/badge.svg)
[![Twitter](https://img.shields.io/twitter/follow/olafhartong.svg?style=social&label=Follow)](https://twitter.com/olafhartong)
[![Discord Shield](https://discordapp.com/api/guilds/715302469751668787/widget.png?style=shield)](https://discord.gg/B5n6skNTwy)

This is a Microsoft Sysinternals Sysmon [download here](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon) configuration repository, set up modular for easier maintenance and generation of specific configs.

The sysmonconfig.xml within the repo is automatically generated after a successful merge by the PowerShell script and a successful load by Sysmon in an Azure Pipeline run.

## Pre-Grenerated configurations
| Config | Description|
| --- | --- |
| [default - sysmonconfig.xml](https://raw.githubusercontent.com/olafhartong/sysmon-modular/master/sysmonconfig.xml) | This is the balanced configuration, most used, more information [here](https://github.com/olafhartong/sysmon-modular/wiki/Configuration-options#generating-the-default-configuration) |
[verbose - sysmonconfig-excludes-only.xml](https://raw.githubusercontent.com/olafhartong/sysmon-modular/master/sysmonconfig-excludes-only.xml) |  This is the very verbose configuration, all events are included, only the exclusion modules are applied. This should not be used in production without validation, will generate a significant amount of data and might impact performance. More information [here](https://github.com/olafhartong/sysmon-modular/wiki/Configuration-options#generating-custom-configs)|
| sysmon-mde-augmentation | A configuration to augment Defender for Endpoint, intended to augment the information and have as little overlap as possible. Coming soon |

Do keep in mind that tuning per environment is _strongly_ recommended. More info on how to generate a custom config, incorporating your own modules [here](https://github.com/olafhartong/sysmon-modular/wiki/Configuration-options#generating-custom-configs)

## NOTICE; Sysmon below 13 will not completely be compatible with this configuration

Older versions are still available in the branches, but are not as complete as the current branch

- V8.x >> [here](https://github.com/olafhartong/sysmon-modular/tree/version-8)
- V9.x >> [here](https://github.com/olafhartong/sysmon-modular/tree/version-9)
- V10.4 >> [here](https://github.com/olafhartong/sysmon-modular/tree/v10.4)
- V12.x >> [here](https://github.com/olafhartong/sysmon-modular/tree/version-12)

To understand added features in the latest version, have a look at my [small blog post](https://medium.com/falconforce/sysmon-11-dns-improvements-and-filedelete-events-7a74f17ca842) or watch my [DerbyCon talk](http://www.irongeek.com/i.php?page=videos/derbycon9/stable-36-endpoint-detection-super-powers-on-the-cheap-with-sysmon-olaf-hartong)

**Note:**
I do recommend using a minimal number of configurations within your environment for multiple obvious reasons, like; maintenance, output equality, manageability and so on. But do make tailored configurations for Domain Controllers, Servers and workstations.

## Credits

Big credit goes out to SwiftOnSecurity for laying a great foundation and making this repo possible!
**[sysmonconfig-export.xml](https://github.com/SwiftOnSecurity/sysmon-config/blob/master/sysmonconfig-export.xml)**.

Final thanks to **[Mathias Jessen](https://twitter.com/iisresetme)** for his Merge script, without it, this project would not have worked as well.

## Contributing

Pull requests / issue tickets and new additions will be greatly appreciated!

## More information

I started a series of blog posts covering this repo;
- [Endpoint detection Superpowers on the cheap - part1 - MITRE ATT&CK, Sysmon and my modular configuration](https://medium.com/@olafhartong/endpoint-detection-superpowers-on-the-cheap-part-1-e9c28201ac47)
- [Endpoint detection Superpowers on the cheap — part 2 — Deploy and Maintain](https://medium.com/@olafhartong/endpoint-detection-superpowers-on-the-cheap-part-2-deploy-and-maintain-d06580329fe8)
- [Endpoint detection Superpowers on the cheap — part 3 — Sysmon Tampering](https://medium.com/@olafhartong/endpoint-detection-superpowers-on-the-cheap-part-3-sysmon-tampering-49c2dc9bf6d9)

## Mitre ATT&CK

I strive to map all configurations to the ATT&CK framework whenever Sysmon is able to detect it.
Please note this is a possible log entry that might lead to a detection, not in all cases is this the only telemetry for that technique. Additionally there might be more techniques releated to that rule, the one mapped is the one I deemed most likely.

## Required actions

I highly recommend looking at the configs before implementing them in your production environment. This enables you to have as actionable logging as possible and as litte noise as possible.

### Customization

You will need to install and observe the results of the configuration in your own environment before deploying it widely.
For example, you will need to exclude actions of your antivirus, which will otherwise likely fill up your logs with useless information.

### Generating a config

#### PowerShell

    $> git clone https://github.com/olafhartong/sysmon-modular.git
    $> cd sysmon modular
    $> . .\Merge-SysmonXml.ps1
    $> Merge-AllSysmonXml -Path ( Get-ChildItem '[0-9]*\*.xml') -AsString | Out-File sysmonconfig.xml

### Generating custom configs

Below functions with great thanks to mbmy

**New Function:** 
`Find-RulesInBasePath` - takes a base path (i.e. C:\folder\sysmon-modular\) and finds all candidate xml rule files based upon regex pattern

Example:
```PS C:\Users\sysmon\sysmon-modular> Find-RulesInBasePath -BasePath C:\users\sysmon\sysmon-modular\ -OutputRules | Out-File available_rules.txt```

**Merge-AllSysmonXml New Parameters:**

`-BasePath` - finds all candidate xml rule files from a provided path based upon regex pattern and merges them

Example:
```PS C:\Users\sysmon\sysmon-modular> Merge-AllSysmonXml -AsString -BasePath C:\Users\sysmon\sysmon-modular\```


`-ExcludeList` - Combined with -BasePath, takes a list of rules and excludes them from found rules prior to merge

Example:
```PS C:\Users\sysmon\sysmon-modular> Merge-AllSysmonXml -AsString -BasePath C:\Users\sysmon\sysmon-modular\ -ExcludeList C:\users\sysmon\sysmon-modular\exclude_rules.txt```


`-IncludeList` - Combined with -BasePath, finds all available rules from base path but only merges those defined in a list

Example:
```PS C:\Users\sysmon\sysmon-modular> Merge-AllSysmonXml -AsString -BasePath C:\Users\sysmon\sysmon-modular\ -IncludeList C:\users\sysmon\sysmon-modular\include_rules.txt```


Include/Exclude List Format Example:

```1_process_creation\exclude_adobe_acrobat.xml
3_network_connection_initiated\include_native_windows_tools.xml
12_13_14_registry_event\exclude_internet_explorer_settings.xml
12_13_14_registry_event\exclude_webroot.xml
17_18_pipe_event\include_winreg.xml
19_20_21_wmi_event\include_wmi_create.xml
2_file_create_time\exclude_chrome.xml
3_network_connection_initiated\include_native_windows_tools.xml
3_network_connection_initiated\include_ports_proxies.xml
8_create_remote_thread\include_general_commment.xml
8_create_remote_thread\include_psinject.xml
9_raw_access_read\include_general_commment.xml
```


**Building a config with all sysmon-modular rules for certain event IDs (include whole directory) and then disabling all event ids without imported rules**

Example:
```
# generate the config
$sysmonconfig =  Merge-AllSysmonXml  -BasePath . -IncludeList $workingFolder\include.txt -VerboseLogging -PreserveComments

# flip off any rule groups where rules were not imported
foreach($rg in $sysmonconfig.SelectNodes("/Sysmon/EventFiltering/RuleGroup [*/@onmatch]"))
{
    $ruleNodes = $rg.SelectNodes("./* [@onmatch]")

    if(     $ruleNodes -eq $null `
        -or $ruleNodes.ChildNodes.count -gt 0)
    {
        # no rule nodes found (unlikely) or more than one rule found
        continue
    }

    # RuleGroup with only one rule node
    $ruleNode = $ruleNodes[0]

    if($ruleNode.onmatch -eq "exclude" -and $ruleNode.ChildNodes.count -eq 0 )
    {
        $message = "{0} {1} has no matching conditions.  Toggled to 'include' to limit output" -f $ruleNode.Name,$rg.Name
        Write-Warning $message

        $ruleNode.onmatch = "include"
        $comment = $sysmonconfig.CreateComment($message)
        $rg.AppendChild($comment) | Out-Null
    }
}
```

Include/Exclude List Format Example (for entire rule/event families):

```
1_process_creation
5_process_ended
11_file_create
23_file_delete
7_image_load
17_18_pipe_event
```

## Use

### Install

Run with administrator rights

    sysmon.exe -accepteula -i sysmonconfig.xml

### Update existing configuration

Run with administrator rights

    sysmon.exe -c sysmonconfig.xml

Note.