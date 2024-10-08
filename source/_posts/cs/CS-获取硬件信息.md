---
title: C#-获取硬件信息
categories:
- [C#]
---
硬件信息，可以利用`System.Management`命名空间下的`ManagementClass`和`ManagementObjectSearcher`类。以下是一些获取常见硬件信息的示例代码：

### 1. 获取CPU信息

```c#
using System;
using System.Management;

class Program
{
    static void Main()
    {
        string cpuInfo;
        ManagementClass mc = new ManagementClass("Win32_Processor");
        ManagementObjectCollection moc = mc.GetInstances();

        foreach (ManagementObject mo in moc)
        {
            cpuInfo = "Name: " + mo.Properties["Name"].Value.ToString() +
                      "\nManufacturer: " + mo.Properties["Manufacturer"].Value.ToString() +
                      "\nID: " + mo.Properties["ProcessorId"].Value.ToString();
            Console.WriteLine(cpuInfo);
        }
    }
}
```

### 2. 获取内存信息

```c#
using System;
using System.Management;

class Program
{
    static void Main()
    {
        ManagementClass memClass = new ManagementClass("Win32_PhysicalMemory");
        ManagementObjectCollection memObjects = memClass.GetInstances();

        double totalMemory = 0;
        foreach (ManagementObject mo in memObjects)
        {
            double memorySize = Convert.ToDouble(mo.Properties["Capacity"].Value) / (1024 * 1024); // 转换为MB
            totalMemory += memorySize;
        }

        Console.WriteLine($"Total Memory: {totalMemory} MB");
    }
}
```

### 3. 获取硬盘信息

```c#
using System;
using System.Management;

class Program
{
    static void Main()
    {
        ManagementObjectSearcher searcher = new ManagementObjectSearcher("SELECT * FROM Win32_DiskDrive");
        foreach (ManagementObject disk in searcher.Get())
        {
            string diskName = disk["Caption"].ToString();
            string diskSize = disk["Size"].ToString(); // 这个值通常是字节，可以根据需要转换为GB或TB
            Console.WriteLine($"Disk Name: {diskName}");
            Console.WriteLine($"Disk Size: {FormatBytes(Convert.ToInt64(diskSize))}");
        }
    }

    static string FormatBytes(long bytes)
    {
        string[] sizes = { "B", "KB", "MB", "GB", "TB" };
        int order = 0;
        while (bytes >= 1024 && order < sizes.Length - 1)
        {
            bytes /= 1024;
            order++;
        }
        return $"{bytes:#,##0.##} {sizes[order]}";
    }
}
```

### 注意事项

- 在运行这些代码之前，请确保你的项目已经引用了`System.Management`命名空间。
- 运行这些脚本可能需要管理员权限，尤其是在获取某些敏感信息时。
- 上述代码提供了基本的硬件信息获取方法，实际应用中可能需要根据具体需求进一步定制和处理。