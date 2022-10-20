---
title: [C#] Windows 自動封鎖 IP
categories: C#
tags:
  - Windows
  - Firewall
  - Powershell
  - Event Log
abbrlink: 4026304689
date: 2022-10-17 22:12:10
---

自架的對外伺服器常被陌生 IP try 帳密，導致事件檢視器常常卡一堆登入失敗的紀錄而影響判讀，於是決定寫隻簡單的程式來監聽 Event Log 並即時將登入失敗的 IP 加到封鎖清單中。原本以為網路上查查資料，複製貼上改一下應該一小時左右就能搞定，結果各種撞牆卡了我一整天才完成，特此紀錄一下相關注意事項！

![Event Log](20221017233626.png)  
![Attack Bar Chart](20221017221448.png)  

<!-- more -->

## Nuget

``` xml
<PackageReference Include="Microsoft.Extensions.Logging.EventLog" Version="6.0.0" />
<PackageReference Include="System.Diagnostics.EventLog" Version="6.0.0" />
<PackageReference Include="Microsoft.PowerShell.SDK" Version="7.2.6" />
<PackageReference Include="System.Management.Automation" Version="7.2.6" />
```

## Step

### 1. 讀取 `EventLog/Security` 取得 `EventId=4625` 的相關資訊

* 讀取需使用 `系統管理員` 權限
* 範例：

    ``` csharp
    var entries =
        from EventLogEntry e in eventLog.Entries
        where e.InstanceId == 4625
        select new
        {
            e.ReplacementStrings,
        };

    if (entries.Count() > 0)
    {
        var events = entries.ToList();
        var ips = new Dictionary<string, int>();

        foreach (var d in events)
        {
            if (d.ReplacementStrings.Length >= 19)
            {
                var targetUserName = d.ReplacementStrings[5];
                var ip = d.ReplacementStrings[19];
            }
        }
    ```

* `ReplacementStrings` 資料結構參考：

    ``` xml
    <Data Name="SubjectUserSid">S-1-0-0</Data> 
    <Data Name="SubjectUserName">-</Data> 
    <Data Name="SubjectDomainName">-</Data> 
    <Data Name="SubjectLogonId">0x0</Data> 
    <Data Name="TargetUserSid">S-1-0-0</Data> 
    <Data Name="TargetUserName">ѓ®бвм</Data> 
    <Data Name="TargetDomainName" /> 
    <Data Name="Status">0xc000006d</Data> 
    <Data Name="FailureReason">%%2313</Data> 
    <Data Name="SubStatus">0xc0000064</Data> 
    <Data Name="LogonType">3</Data> 
    <Data Name="LogonProcessName">NtLmSsp</Data> 
    <Data Name="AuthenticationPackageName">NTLM</Data> 
    <Data Name="WorkstationName">-</Data> 
    <Data Name="TransmittedServices">-</Data> 
    <Data Name="LmPackageName">-</Data> 
    <Data Name="KeyLength">0</Data> 
    <Data Name="ProcessId">0x0</Data> 
    <Data Name="ProcessName">-</Data> 
    <Data Name="IpAddress">83.69.141.105</Data> 
    <Data Name="IpPort">5223</Data> 
    ```

### 2. 從防火牆規則取得已封鎖 IP 列表

* 使用 Powershell 語法 `Get-NetFirewallRule` 取得已封鎖 IP 列表
* 參考 Nuget 套件 `System.Management.Automation`
* 需再參考 Nuget 套件 `Microsoft.PowerShell.SDK`，不然可能會出現以下錯誤：  
    <span style="color:red">"Cannot load PowerShell snap-in Microsoft.PowerShell.Diagnostics because of the following error: Could not load file or assembly"</span>
* 需注意執行環境的 [Powershell 執行原則](https://learn.microsoft.com/zh-tw/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7.2) 是否低於 `RemoteSigned`，不然可能會出現以下錯誤：  
    <span style="color:red">Cannot be loaded because running scripts is disabled on this system</span>

* 範例：

    ``` csharp
    using (var ps = PowerShell.Create())
    {
        // 設定執行原則
        ps.AddScript("Set-ExecutionPolicy RemoteSigned");
        // 從防火牆規則 firewallRuleName 取出當前已封鎖 IP 列表
        ps.AddScript("Import-Module NetSecurity");
        ps.AddScript($"[string[]](Get-NetFirewallRule -DisplayName '{firewallRuleName}' | Get-NetFirewallAddressFilter).RemoteAddress");

        // 以上語法有特別轉型成 String[]，故可用 Invoke<string>() 直接取得
        foreach (string ip in ps.Invoke<string>())
        {
            blockedIps.Add(ip);
        }

        // Powershell 執行異常時不會拋錯，需用以下語法取出
        PSDataCollection<ErrorRecord> errors = ps.Streams.Error;
        if (errors != null && errors.Count > 0)
        {
            foreach (ErrorRecord err in errors)
            {
                Write2EventLog($"Error: {err}", EventLogEntryType.Error);
            }
        }
    }
    ```

### 3. 整併 IP 列表並寫入防火牆規則

* 使用 Powershell 語法  `Set-NetFirewallRule` 寫回阻擋規則
* 使用 `@("111.222.333.444", "111.222.333.555")` 格式產生 Powershell 的 String[] 參數
* 範例：

    ``` csharp
    using (var ps = PowerShell.Create())
    {
        var sb = new StringBuilder();
        sb.Append("\"");
        sb.Append(string.Join("\",\"", ips));
        sb.Append("\"");

        ps.AddScript("Set-ExecutionPolicy RemoteSigned");
        ps.AddScript("Import-Module NetSecurity");
        ps.AddScript($"Set-NetFirewallRule -DisplayName '{firewallRuleName}' -Direction Inbound -Action Block -RemoteAddress @({sb})");

        ps.Invoke();
    }
    ```

### 4. 透過 `4625` 事件觸發

* 可直接從 `EventLog/Security` 事件附加工作排程

    ![從 4625 事件附加工作排程](20221017231704.png)  
    ![觸發程序](20221017231820.png)  

## Example

> <https://github.com/KuoAnn/AutoBlockIp>

## 後續

放了一個晚上就擋了 14 組 IP，網路的世界真是險惡...

![Event Log](20221018221135.png)  

自啟用排程後有效降低被攻擊的次數

![Attack Bar Chart](20221018222136.png)  
