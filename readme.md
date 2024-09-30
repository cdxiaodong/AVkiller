# Project Summary

### Summary

**English:**

### Project Description: avkiller-test-true.cpp

**Overview:**
The `avkiller-test-true.cpp` file is a C++ program that interacts with Windows Management Instrumentation (WMI) to query and display information about running processes on a Windows system. The program initializes COM, connects to the WMI service, executes a WQL (WMI Query Language) query to retrieve process information, and then iterates through the results to print the name and executable path of each process.

**Key Features:**

1. **COM Initialization:**
   - The program starts by initializing the COM library using `CoInitializeEx` with the `COINIT_MULTITHREADED` flag. This is necessary for making WMI calls.
   - It then initializes COM security settings using `CoInitializeSecurity`.

2. **WMI Connection:**
   - The program creates an instance of `IWbemLocator` to locate the WMI service.
   - It connects to the WMI service using `IWbemLocator::ConnectServer`, specifying the `ROOT\\CIMV2` namespace, which is commonly used for system-level WMI queries.

3. **Security Settings:**
   - The program sets the security levels on the WMI proxy using `CoSetProxyBlanket` to ensure secure communication with the WMI service.

4. **WMI Query Execution:**
   - The program executes a WQL query to retrieve information about all running processes using `IWbemServices::ExecQuery`. The query `SELECT * FROM Win32_Process` fetches details about all processes.

5. **Result Iteration:**
   - The program iterates over the results of the query, retrieving and printing the name and executable path of each process using `IWbemClassObject::Get`.

6. **Cleanup:**
   - The program properly releases all COM objects and uninitializes the COM library to free resources.

**Purpose:**
This program is designed to demonstrate how to interact with WMI in a C++ application to retrieve and display system process information. It can be used as a reference or starting point for more complex WMI-based applications, such as system monitoring tools or security software.

**Dependencies:**
- The program relies on the Windows SDK, specifically the WMI and COM libraries (`wbemuuid.lib`).
- It uses standard C++ libraries for input/output and string handling.

**Potential Use Cases:**
- System administrators can use this program to monitor running processes on a Windows machine.
- Developers can extend this program to include more detailed process information or integrate it into larger monitoring or security applications.

**Note:**
- The program assumes that the user has the necessary permissions to query WMI and access process information.
- Error handling is implemented to manage potential issues during COM initialization, WMI connection, and query execution.

**Chinese:**

### 项目描述：avkiller-test-true.cpp

**概述：**
`avkiller-test-true.cpp` 文件是一个 C++ 程序，它与 Windows 管理规范 (WMI) 进行交互，以查询和显示 Windows 系统上正在运行的进程信息。该程序初始化 COM，连接到 WMI 服务，执行 WQL（WMI 查询语言）查询以检索进程信息，然后遍历结果以打印每个进程的名称和可执行路径。

**主要功能：**

1. **COM 初始化：**
   - 程序首先使用 `CoInitializeEx` 并设置 `COINIT_MULTITHREADED` 标志来初始化 COM 库。这是进行 WMI 调用所必需的。
   - 然后使用 `CoInitializeSecurity` 初始化 COM 安全设置。

2. **WMI 连接：**
   - 程序创建一个 `IWbemLocator` 实例以定位 WMI 服务。
   - 它使用 `IWbemLocator::ConnectServer` 连接到 WMI 服务，指定 `ROOT\\CIMV2` 命名空间，该命名空间通常用于系统级 WMI 查询。

3. **安全设置：**
   - 程序使用 `CoSetProxyBlanket` 在 WMI 代理上设置安全级别，以确保与 WMI 服务的安全通信。

4. **WMI 查询执行：**
   - 程序执行 WQL 查询以检索所有正在运行的进程信息，使用 `IWbemServices::ExecQuery`。查询 `SELECT * FROM Win32_Process` 获取所有进程的详细信息。

5. **结果遍历：**
   - 程序遍历查询结果，使用 `IWbemClassObject::Get` 检索并打印每个进程的名称和可执行路径。

6. **清理：**
   - 程序正确释放所有 COM 对象并取消初始化 COM 库，以释放资源。

**目的：**
该程序旨在演示如何在 C++ 应用程序中与 WMI 交互以检索和显示系统进程信息。它可以作为更复杂的基于 WMI 的应用程序（如系统监控工具或安全软件）的参考或起点。

**依赖项：**
- 该程序依赖于 Windows SDK，特别是 WMI 和 COM 库（`wbemuuid.lib`）。
- 它使用标准 C++ 库进行输入/输出和字符串处理。

**潜在用途：**
- 系统管理员可以使用此程序监控 Windows 机器上正在运行的进程。
- 开发人员可以扩展此程序以包含更多详细的进程信息，或将其集成到更大的监控或安全应用程序中。

**注意：**
- 该程序假设用户具有查询 WMI 和访问进程信息的必要权限。
- 实现了错误处理以管理 COM 初始化、WMI 连接和查询执行过程中可能出现的问题。

### Content

## File: avkiller-test-true.cpp

```
﻿#include <iostream>
#include <comdef.h>
#include <Wbemidl.h>
#pragma comment(lib, "wbemuuid.lib")

using namespace std;

int main()
{
    HRESULT hres;

    // Initialize COM library
    hres = CoInitializeEx(0, COINIT_MULTITHREADED);
    if (FAILED(hres))
    {
        cout << "Failed to initialize COM library. Error code: " << hex << hres << endl;
        return 1;
    }

    // Initialize security
    hres = CoInitializeSecurity(
        NULL,
        -1,
        NULL,
        NULL,
        RPC_C_AUTHN_LEVEL_DEFAULT,
        RPC_C_IMP_LEVEL_IMPERSONATE,
        NULL,
        EOAC_NONE,
        NULL);
    if (FAILED(hres))
    {
        cout << "Failed to initialize security. Error code: " << hex << hres << endl;
        CoUninitialize();
        return 1;
    }

    // Obtain the initial locator to WMI
    IWbemLocator* pLoc = NULL;
    hres = CoCreateInstance(
        CLSID_WbemLocator,
        0,
        CLSCTX_INPROC_SERVER,
        IID_IWbemLocator,
        (LPVOID*)&pLoc);
    if (FAILED(hres))
    {
        cout << "Failed to create IWbemLocator object. Error code: " << hex << hres << endl;
        CoUninitialize();
        return 1;
    }

    // Connect to WMI through the IWbemLocator::ConnectServer method
    IWbemServices* pSvc = NULL;
    hres = pLoc->ConnectServer(
        _bstr_t(L"ROOT\\CIMV2"), // Namespace
        NULL, // User name
        NULL, // Password
        0, // Locale
        NULL, // Security flags
        0, // Authority
        0, // Context
        &pSvc); // IWbemServices proxy
    if (FAILED(hres))
    {
        cout << "Could not connect to WMI. Error code: " << hex << hres << endl;
        pLoc->Release();
        CoUninitialize();
        return 1;
    }

    // Set the security levels on the proxy
    hres = CoSetProxyBlanket(
        pSvc, // IWbemServices proxy
        RPC_C_AUTHN_WINNT,
        RPC_C_AUTHZ_NONE,
        NULL,
        RPC_C_AUTHN_LEVEL_CALL,
        RPC_C_IMP_LEVEL_IMPERSONATE,
        NULL,
        EOAC_NONE);
    if (FAILED(hres))
    {
        cout << "Could not set proxy blanket. Error code: " << hex << hres << endl;
        pSvc->Release();
        pLoc->Release();
        CoUninitialize();
        return 1;
    }

    // Use the IWbemServices pointer to make requests of WMI
    IEnumWbemClassObject* pEnumerator = NULL;
    hres = pSvc->ExecQuery(
        bstr_t("WQL"),
        bstr_t("SELECT * FROM Win32_Process"),
        WBEM_FLAG_FORWARD_ONLY | WBEM_FLAG_RETURN_IMMEDIATELY,
        NULL,
        &pEnumerator);
    if (FAILED(hres))
    {
        cout << "Query failed. Error code: " << hex << hres << endl;
        pSvc->Release();
        pLoc->Release();
        CoUninitialize();
        return 1;
    }

    //
    // Iterate over the results of the query
    IWbemClassObject* pclsObj = NULL;
    ULONG uReturn = 0;
    while (pEnumerator)
    {
        hres = pEnumerator->Next(WBEM_INFINITE, 1, &pclsObj, &uReturn);
        if (0 == uReturn)
        {
            break;
        }

        // Get the process name and path
        VARIANT vtProp;
        hres = pclsObj->Get(L"Name", 0, &vtProp, 0, 0);
        wcout << L"Process Name : " << vtProp.bstrVal << endl;
        VariantClear(&vtProp);

        hres = pclsObj->Get(L"ExecutablePath", 0, &vtProp, 0, 0);
        wcout << L"Process Path : " << vtProp.bstrVal << endl;
        VariantClear(&vtProp);

        pclsObj->Release();
    }

    // Clean up
    pEnumerator->Release();
    pSvc->Release();
    pLoc->Release();
    CoUninitialize();

    return 0;
}
```

----------------------------------------

