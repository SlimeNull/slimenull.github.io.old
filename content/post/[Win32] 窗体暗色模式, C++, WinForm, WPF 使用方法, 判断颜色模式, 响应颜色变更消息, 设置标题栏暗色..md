---
title: '[Win32] 窗体暗色模式, C++, WinForm, WPF 使用方法, 判断颜色模式, 响应颜色变更消息, 设置标题栏暗色.'
slug: '[Win32]窗体暗色模式,CPP,WinForm,WPF使用方法,判断颜色模式,响应颜色变更消息,设置标题栏暗色.'
date: 2023-04-04 17:32:08
tags:
  - wpf
  - c++
  - 开发语言
categories:
  - .NET
  - 桌面程序
  - C++
description: 'Win32 暗色模式适配, C++, WinForm, WPF 判断当前颜色模式, 响应颜色变更消息, 设置标题栏暗色'
---

2023 年了, 如果咱写的程序还不支持暗色模式, 那就说不过去了.

## 判断是否是暗色模式


在 Windows 中判断当前系统的颜色模式是否是暗色, 可以通过查询注册表项来实现.


下面是 C++ 的示例代码:

```cpp
HKEY hKey;
const wchar_t* subKey = L"SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Themes\\Personalize";
const wchar_t* valueName = L"AppsUseLightTheme";
DWORD value = -1;
DWORD valueSize = sizeof(DWORD);
if (RegOpenKeyEx(HKEY_CURRENT_USER, subKey, 0, KEY_READ, &hKey) == ERROR_SUCCESS)
{
    HRESULT hr = RegGetValue(hKey, nullptr, valueName, RRF_RT_REG_DWORD, nullptr, &value, &valueSize);
    if (hr != S_OK)
    {
        value = -1;    // 不要假定该键必须存在，如果找不到将返回默认值
    }
    RegCloseKey(hKey);
}

if (value == 0)
{
    // 当前使用暗色主题
}
else if (value == 1)
{
    // 当前使用亮色主题
}
else
{
    // 无法确定当前主题
}
```


下面是 C# 示例代码:

```cs
using Microsoft.Win32;

RegistryKey key = Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Microsoft\Windows\CurrentVersion\Themes\Personalize");
if (key != null)
{
    int appsUseLightTheme = (int)key.GetValue("AppsUseLightTheme", -1);
    if (appsUseLightTheme == 0)
    {
        // 当前使用暗色主题
    }
    else if (appsUseLightTheme == 1)
    {
        // 当前使用亮色主题
    }
    else
    {
        // 无法确定当前主题
    }
    key.Close();
}
```


## 设置窗口暗色模式


Windows 中自带了一个暗色模式的 WinAPI, 直接调用就可以将窗口标题栏变成暗色.


下面是 C++ 的示例代码:

```cpp
#include <windows.h>
#include <dwmapi.h>

int main() {
	HWND hWnd = GetMainWindowHandle(); // 获取主窗口句柄
	DWORD dwAttribute = DWMWA_USE_IMMERSIVE_DARK_MODE; // 设置暗色模式属性
	BOOL bValue = TRUE; // 启用暗色模式
	DwmSetWindowAttribute(hWnd, dwAttribute, &bValue, sizeof(bValue)); // 设置窗口属性

	return 0;
}
```


下面是 C# 的示例代码:

```cs
[DllImport("dwmapi.dll", PreserveSig = true)]
public static extern int DwmSetWindowAttribute(IntPtr hwnd, DwmWindowAttribute attr, ref int attrValue, int attrSize);

public static bool EnableDarkModeForWindow(IntPtr hWnd, bool enable)
{
	int darkMode = enable ? 1 : 0;
	int hr = DwmSetWindowAttribute(hWnd, DwmWindowAttribute.UseImmersiveDarkMode, ref darkMode, sizeof(int));
	return hr >= 0;
}

public enum DwmWindowAttribute : uint
{
	NCRenderingEnabled = 1,
	NCRenderingPolicy,
	TransitionsForceDisabled,
	AllowNCPaint,
	CaptionButtonBounds,
	NonClientRtlLayout,
	ForceIconicRepresentation,
	Flip3DPolicy,
	ExtendedFrameBounds,
	HasIconicBitmap,
	DisallowPeek,
	ExcludedFromPeek,
	Cloak,
	Cloaked,
	FreezeRepresentation,
	PassiveUpdateMode,
	UseHostBackdropBrush,
	UseImmersiveDarkMode = 20,
	WindowCornerPreference = 33,
	BorderColor,
	CaptionColor,
	TextColor,
	VisibleFrameBorderThickness,
	SystemBackdropType,
	Last
}
```


## 响应配色更改


当系统配色更改的时候, 会向所有窗体发送一个 `THEMECHANGED` 消息:


C++ 中, 你可以在 WndProc 捕捉这个消息, 然后进行具体操作. 例如上面提到的, 设置窗口暗色模式.


```cpp
#include <Windows.h>
#include <iostream>

LRESULT CALLBACK WndProc(HWND hwnd, UINT uMsg, WPARAM wParam, LPARAM lParam);

int main() {
    // 注册窗口类
    WNDCLASS wc = {};
    wc.lpfnWndProc = WndProc;
    wc.hInstance = GetModuleHandle(NULL);
    wc.lpszClassName = L"MyWindowClass";
    if (!RegisterClass(&wc)) {
        std::cerr << "Failed to register window class" << std::endl;
        return 1;
    }

    // 创建窗口
    HWND hwnd = CreateWindow(
        L"MyWindowClass",
        L"My Window",
        WS_OVERLAPPEDWINDOW,
        CW_USEDEFAULT, CW_USEDEFAULT, CW_USEDEFAULT, CW_USEDEFAULT,
        NULL, NULL, GetModuleHandle(NULL), NULL
    );
    if (!hwnd) {
        std::cerr << "Failed to create window" << std::endl;
        return 1;
    }

    // 显示窗口
    ShowWindow(hwnd, SW_SHOWDEFAULT);

    // 消息循环
    MSG msg;
    while (GetMessage(&msg, NULL, 0, 0)) {
        TranslateMessage(&msg);
        DispatchMessage(&msg);
    }

    return 0;
}

LRESULT CALLBACK WndProc(HWND hwnd, UINT uMsg, WPARAM wParam, LPARAM lParam) {
    switch (uMsg) {
    case WM_DESTROY:
        PostQuitMessage(0);
        return 0;
    case WM_THEMECHANGED:
        std::cout << "Theme changed" << std::endl;
        return 0;
    default:
        return DefWindowProc(hwnd, uMsg, wParam, lParam);
    }
}
```


C# 的话, 如果你在使用 WinForm 或 WPF, 你可以直接使用 C# 封装好的 `SystemEvents` 类, 它有一个 `UserPreferenceChanged` 事件, 通过订阅这个事件, 并判断具体分类, 最后执行操作:


```cs
SystemEvents.UserPreferenceChanged += SystemEvents_UserPreferenceChanged;

private void SystemEvents_UserPreferenceChanged(object sender, UserPreferenceChangedEventArgs e)
{
    if (e.Category == UserPreferenceCategory.General)
    {
        // 更新颜色主题
    }
}
```
