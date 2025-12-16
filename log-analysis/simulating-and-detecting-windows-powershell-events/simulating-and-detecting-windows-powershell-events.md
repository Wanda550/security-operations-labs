# Simulating and Detecting Windows PowerShell Events

## 🎯 Objective

Demonstrate how PowerShell logs can be collected, analyzed, and for security monitoring.  
Generating simple logs on a Windows systems and simulating how SOC Analysts(professional) use logs to detect security incidents.

## 🖥️ Requirements

**Systems:**  

- Windows 10/11 or Windows Server 2019/2022  

**Tools:**  

- Windows Event Viewer  
- PowerShell (pre-installed)

## 🔧 Step 1: Enable `gpedit.msc` (if missing)

Open Run (Win + R) and type:

gpedit.msc and click to start.

If Group Policy Editor is unavailable (Windows Home edition), PowerShell logging can be enabled via the Windows Registry using official Microsoft-supported keys.

```powershell 
# Enable Script Block Logging
New-Item -Path "HKLM:\Software\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging" -Force
Set-ItemProperty -Path "HKLM:\Software\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging" -Name EnableScriptBlockLogging -Value 1

# Enable Module Logging
New-Item -Path "HKLM:\Software\Policies\Microsoft\Windows\PowerShell\ModuleLogging" -Force
Set-ItemProperty -Path "HKLM:\Software\Policies\Microsoft\Windows\PowerShell\ModuleLogging" -Name EnableModuleLogging -Value 1
New-ItemProperty -Path "HKLM:\Software\Policies\Microsoft\Windows\PowerShell\ModuleLogging" -Name ModuleNames -PropertyType Hashtable -Value @{"*"="*"} -Force
```

## **Step 2: Enable Windows PowerShell Logging**

Under Local Computer Policy There is Computer Configuration

Go to Adminstrative Templetes -> Windows Components -> Windows Powershell:

Click Turn on Module Logging, enable it click the button show set value to *(which is wildcard for all modules) click **OK**

Click Turn on Powershell Script Block Logging, enable it

Click Turn Script Execution, enable it set Execution Policy to **Allow all scripts**

Close **gpedit.msc**

## **Step 3: Simulate a Suspicious PowerShell Command**

To simulate a suspicious activity, open an elevated PowerShell(run as adminstrator) session and run the following command:

```powershell
Get-LocalUser | Select-Object Name, Enabled
```

![PowerShell Command](../../references/powershell-command.png)

## **Step 4: Detect the Log in Windows Event Viewer**

Open Event Viewer to find Powershell logs(where they are stored) 

Navigate to
   `Applications and Services Logs -> Microsoft -> Windows -> then search for Powershell -> Operationals.`

Click Filter Current Log, and filter for Event ID 4104 (which logs PowerShell script execution).

Look for an entry that shows the execution of the Get-LocalUser command.

Take a screenshot of the event details.

## 🧠 Analysis

Once PowerShell logging is enabled, every executed script block is recorded under Event ID 4104 in the Microsoft-Windows-PowerShell/Operational log.

In this lab, the executed command:

Get-LocalUser | Select-Object Name, Enabled

was successfully captured by Windows logging. This command enumerates local user accounts and their enabled or disabled status. While this action can be legitimate for system administration, it is also a common reconnaissance technique used by attackers after initial access.

From a SOC analyst perspective:

Enumerating local users is often one of the first post-compromise actions
Attackers use this information to identify privileged or inactive accounts
PowerShell-based enumeration is frequently abused because it blends in with normal administrative activity

Event ID 4104 Visibility

Event ID 4104 provides high-value forensic context, including:
Full PowerShell script block content
User account and security context
Execution timestamp
Host machine details

## 📸 Screenshots
![PowerShell Event](../../references/powershell-event.png)
![PowerShell Event Screenshot](../../references/powershell-event-screenshot.png)
![PowerShell Event Detailed Screenshot](../../references/powershell-event-screenshot-detailed.png)

## Conclusion:

Understanding **Log Analysis**: Logs are crucial for detecting, investigating, and responding to security incidents. Through the use of Windows Event Viewer and Linux log files, you can monitor system activity and identify potential security issues.

This lab demonstrates:

1. How PowerShell activity generates Event ID 4104 logs
2. How SOC analysts use logging for threat detection
3. How script block logging helps identify suspicious or malicious commands
4. PowerShell logging is essential for detecting post-exploitation activity, lateral movement, and privilege escalation attempts