# 實作：.NET Core

### Console Application

建立專案：

```text
dotnet new console -o myApp
cd myApp
```

檢視 Program.cs

```csharp
using System;

namespace myApp
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

執行程式：

```text
dotnet restore
dotnet run
```

### MVC Web Application

建立專案：

```text
dotnet new mvc -o myWeb
cd myWeb
```

執行程式：

```text
dotnet restore
dotnet run
```

### 延伸閱讀

* [Get started with .NET in 10 minutes](https://www.microsoft.com/net/learn/get-started/linux/ubuntu16-04)

