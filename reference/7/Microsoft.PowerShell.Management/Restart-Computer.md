---
external help file: Microsoft.PowerShell.Commands.Management.dll-Help.xml
keywords: powershell,cmdlet
locale: en-us
Module Name: Microsoft.PowerShell.Management
md.date: 3/11/2019
online version: http://go.microsoft.com/fwlink/?LinkId=821625
schema: 2.0.0
title: Restart-Computer
---
# Restart-Computer

## SYNOPSIS
Restarts ("reboots") the operating system on local and remote computers.

## SYNTAX

```
Restart-Computer [-WsmanAuthentication <String>] [[-ComputerName] <String[]>] [[-Credential] <PSCredential>]
 [-Force] [-Wait] [-Timeout <Int32>] [-For <WaitForServiceTypes>] [-Delay <Int16>] [-WhatIf] [-Confirm]
 [<CommonParameters>]
```

## DESCRIPTION

The `Restart-Computer` cmdlet restarts the operating system on the local and remote computers.

You can use the parameters of `Restart-Computer` to run the restart operations as a background job,
to specify the authentication levels and alternate credentials, to limit the operations that run at
the same time, and to force an immediate restart.

Starting in Windows PowerShell 3.0, you can wait for the restart to complete before you run the next
command, specify a waiting time-out and query interval, and wait for particular services to be
available on the restarted computer.
This feature makes it practical to use `Restart-Computer` in scripts and functions.
You can also use the WSMan protocol to restart the computer, in case DCOM calls are blocked, such as
by an enterprise firewall.

This cmdlet requires Windows PowerShell remoting only when you use the **AsJob** parameter in a
command.

## EXAMPLES

### Example 1: Restart the local computer

This command restarts the local computer.

```powershell
Restart-Computer
```

### Example 2: Restart several computers including the local computer

This command restarts two remote computers, Server01 and Server02, and the local computer,
identified as localhost.

```powershell
Restart-Computer -ComputerName "Server01", "Server02", "localhost"
```

### Example 3: Restart computers as a background job

These commands run a `Restart-Computer` command as a background job on two remote computers, and
then get the results.

Because *AsJob* creates the job on the local computer and automatically returns the results to the
local computer, you can run `Receive-Job` as a local command.

```powershell
#Use the AsJob parameter of Restart-Computer to run the command as a background job.
# Store the resulting job object in the $j variable.
$j = Restart-Computer -ComputerName "Server01", "Server02" -AsJob
# Uses the pipeline operator to send the job object to the Receive-Job cmdlet, which gets the job results.
$j | Receive-Job
```

### Example 4: Restart a remote computer

This command restarts the Server01 remote computer.
The command uses customized impersonation and authentication settings.

```powershell
Restart-Computer -ComputerName "Server01" -Impersonation Anonymous -DcomAuthentication PacketIntegrity
```

### Example 5: Force restart of all computers in a text file

This example forces an immediate restart of all of the computers in the `Domain01.txt` file.

The **Force** parameter forces an immediate restart, and the **ThrottleLimit** parameter limits the
command to 10 concurrent connections.

```powershell
# Use the Get-Content cmdlet to get a list of computers from the Domain01.txt file.
# Store the list in the $s variable.
$s = Get-Content Domain01.txt
# Get the credential of an account with permissions to restart a computer.
$c = Get-Credential
# Restart the computers using the list and credentials.
Restart-Computer -ComputerName $s -Force -ThrottleLimit 10 -Credential $c
```

### Example 6: Restart a remote computer and wait

This command restarts the Server01 remote computer and then waits up to 5 minutes (300 seconds) for
PowerShell to be available on the restarted computer before it continues.

The command uses the **Wait**, **For**, and **Timeout** parameters to specify the conditions of the
wait.
It uses the **Delay** parameter to reduce the interval between queries to the remote computer that
determine whether it is restarted.

```powershell
Restart-Computer -ComputerName "Server01" -Wait -For PowerShell -Timeout 300 -Delay 2
```

### Example 7: Restart a computer by using WSMan

This command restarts the Server01 remote computer by using the WSMan protocol, instead of DCOM,
which is the default.
It also uses Kerberos authentication to determine whether the current user has permission to restart
the remote computer.

These settings are designed for enterprises in which DCOM-based restarts fail because DCOM is
blocked, such as by a firewall.

```powershell
Restart-Computer -ComputerName "Server01" -Protocol WSMan -WsmanAuthentication Kerberos
```

## PARAMETERS

### -ComputerName

Specifies one or more computers.
The default is the local computer.

Type the NETBIOS name, an IP address, or a fully qualified domain name of a remote computer.
To specify the local computer, type the computer name, a dot `.`, or localhost.

This parameter does not rely on PowerShell remoting.
You can use the **ComputerName** parameter even if your computer is not configured to run remote
commands.

```yaml
Type: String[]
Parameter Sets: (All)
Aliases: CN, __SERVER, Server, IPAddress

Required: False
Position: 0
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

### -Credential

Specifies a user account that has permission to perform this action.
The default is the current user.

Type a user name, such as User01 or Domain01\User01, or enter a **PSCredential** object, such as one
generated by the `Get-Credential` cmdlet.

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Delay

Determines how often, in seconds, PowerShell queries the service that is specified by the
**For** parameter to determine whether it is available after the computer is restarted.
Specify a delay between queries, in seconds.
The default value is 5 seconds.

This parameter is valid only together with the **Wait** and **For** parameters.

This parameter was introduced in Windows PowerShell 3.0.

```yaml
Type: Int16
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -For

Specifies the behavior of PowerShell as it waits for the specified service or feature to
become available after the computer restarts.
This parameter is valid only with the **Wait** parameter.

The acceptable values for this parameter are:

- Default: Waits for Windows PowerShell to restart.
- PowerShell: Can run commands in a Windows PowerShell remote session on the computer.
- WMI: Receives a reply to a Win32_ComputerSystem query for the computer.
- WinRM: Can establish a remote session to the computer by using WS-Management.

This parameter was introduced in Windows PowerShell 3.0.

```yaml
Type: WaitForServiceTypes
Parameter Sets: (All)
Aliases:
Accepted values: Wmi, WinRM, PowerShell

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Force

Forces an immediate restart of the computers.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: f

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Timeout

Specifies the duration of the wait, in seconds.
When the time-out elapses, `Restart-Computer` returns the command prompt, even if the computers are
not restarted. The default value, -1, represents an indefinite time-out.

The **Timeout** parameter is valid only with the **Wait** parameter.

This parameter was introduced in Windows PowerShell 3.0.

```yaml
Type: Int32
Parameter Sets: (All)
Aliases: TimeoutSec

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Wait

Indicates that this cmdlet suppresses the Windows PowerShell prompt and blocks the pipeline until
all of the computers have restarted.
You can use this parameter in a script to restart computers and then continue to process when the
restart is finished.

By default, **Wait** waits indefinitely for the computers to restart, but you can use **Timeout** to
adjust the timing and the **For** and **Delay** parameters to wait for particular services to be
available on the restarted computers.

The **Wait** parameter is not valid when you are restarting the local computer.
If the value of the **ComputerName** parameter contains the names of remote computers and the local
computer, `Restart-Computer` generates a non-terminating error for *Wait* on the local computer, but
it waits for the remote computers to restart.

This parameter was introduced in Windows PowerShell 3.0.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -WsmanAuthentication

Specifies the mechanism that is used to authenticate the user credentials when you use the WSMan
protocol.

The acceptable values for this parameter are: Basic, CredSSP, Default, Digest, Kerberos, and
Negotiate. The default value is Default.
For more information about the values of this parameter, see [AuthenticationMechanism Enumeration](/dotnet/api/system.management.automation.runspaces.authenticationmechanism).

> [!WARNING]
> Credential Security Service Provider (CredSSP) authentication, in which the user
> credentials are passed to a remote computer to be authenticated, is designed for commands that
> require authentication on more than one resource, such as accessing a remote network share.
> This mechanism increases the security risk of the remote operation.
> If the remote computer is compromised, the credentials that are passed to it can be used to control
> the network session.

This parameter was introduced in Windows PowerShell 3.0.

```yaml
Type: String
Parameter Sets: (All)
Aliases:
Accepted values: Default, Basic, Negotiate, CredSSP, Digest, Kerberos

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Confirm

Prompts you for confirmation before running the cmdlet.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### -WhatIf

Shows what would happen if the cmdlet runs.
The cmdlet is not run.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### CommonParameters

This cmdlet supports the common parameters: -Debug, -ErrorAction, -ErrorVariable,
-InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose,
-WarningAction, and -WarningVariable. For more information, see [about_CommonParameters](../Microsoft.PowerShell.Core/About/about_CommonParameters.md).

## INPUTS

### System.String

You can pipe computer names to this cmdlet.

In Windows PowerShell 2.0, the **ComputerName** parameter takes input from the pipeline only by
property name.
In Windows PowerShell 3.0, and later, the **ComputerName** parameter takes input from the pipeline by value.

## OUTPUTS

### None, System.Management.Automation.RemotingJob

This cmdlet returns a job object, if you specify the **AsJob** parameter.
Otherwise, it does not generate any output.

## NOTES

This cmdlet uses the **Win32Shutdown** method of the WMI **WIN32_OperatingSystem** class.
In Windows PowerShell 2.0, **AsJob** does not work reliably when you are restarting/stopping remote
computers. In Windows PowerShell 3.0, the implementation is changed to resolve this problem.

## RELATED LINKS

[Rename-Computer](Rename-Computer.md)

[Stop-Computer](Stop-Computer.md)

[Test-Connection](Test-Connection.md)