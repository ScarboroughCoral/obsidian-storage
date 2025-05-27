---
title: 浅谈Windows API
date: 2018-10-08 15:22:14

tags:
- Windows API
- C
categories:
- Computer Science
- Operating System
---


{% note default %}
尽管SEDF_JKHJKE这种变量定义形式很烂，但死磕一次。
{% endnote %}

<!-- more -->

### Windows API中的窗口程序API

这篇博客主要是想简单写一写Windows API中关于图形窗口程序的部分。我认为图形窗口部分最核心的就是消息循环机制，虽然不知道内部如何实现，不知道内部是不是需要调度，消息队列可能就是最简单的FIFS（先到先服务的思想来处理消息）。




### 消息是什么消息

消息是I/O消息或者软件消息，这些消息是有编码的，比如按下键盘`VK_XXX`或者关闭程序`VM_CLOSE`这些，实际上这些消息都有一个唯一确定的ID，即它的编码。当消息被触发时，Windows会封装一个MSG对象，这个MSG对象里面至少有一个消息的编码`ID`，然后把这个MSG对象放入消息队列。当MSG对象排队到队头时，就会对此消息进行处理，进行处理的函数是`消息回调函数`（代码中的`MyWinProc`），这是自己定义的，需要你来对某些你所需要的消息进行特殊的处理。

> 这是不停地从消息队列队头取消息并分发处理
```c
	while (GetMessage(&msg, NULL, 0, 0))
	{
		TranslateMessage(&msg);
		DispatchMessage(&msg);
	}
```
> 这是处理消息时回调函数，根据不同的消息ID编码值做出不同的处理。
```c
LRESULT CALLBACK MyWinProc(HWND hwnd, UINT msg, WPARAM wparam, LPARAM lparam) {
	switch (msg)
	{
	case WM_CLOSE:
		DestroyWindow(hwnd);
		break;
	case WM_DESTROY:
		PostQuitMessage(0);
		break;
	default:
		break;
	}
	return DefWindowProc(hwnd, msg, wparam, lparam);
}
```

### 创建窗口

创建窗口毫不意外需要以下东西：
* 窗口属性配置信息，即代码中的`WNDCLASS`
* 创建函数，及代码中的`CreateWindow`

另外还需要以下东西：
* 创建窗口前需要将窗口属性配置信息注册`RegisterClass(&wndclass)`
* 创建完窗口之后需要将窗口显示`ShowWindow(hwnd, SW_NORMAL)`和开始更新`UpdateWindow(hwnd)`


### 代码

全部代码如下：

```C

#include <windows.h>

LRESULT CALLBACK MyWinProc(HWND hwnd, UINT msg, WPARAM wparam, LPARAM lparam) {
	switch (msg)
	{
	case WM_CLOSE:
		DestroyWindow(hwnd);
		break;
	case WM_DESTROY:
		PostQuitMessage(0);
		break;
	default:
		break;
	}
	return DefWindowProc(hwnd, msg, wparam, lparam);
}
int WINAPI WinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, LPSTR lpCmdLine, int nCmdShow) {
	WNDCLASS wndclass = {
		CS_HREDRAW | CS_VREDRAW,
		MyWinProc,
		0,
		0,
		hInstance,
		LoadIcon(NULL, IDI_APPLICATION),
		LoadCursor(NULL, IDC_ARROW),
		(HBRUSH)GetStockObject(GRAY_BRUSH),
		NULL,
		"wincls"
	};
	RegisterClass(&wndclass);
	HWND hwnd = CreateWindow("wincls", "我的窗口", WS_OVERLAPPEDWINDOW, 0, 0, 800, 600, NULL, NULL, NULL, NULL);
	ShowWindow(hwnd, SW_NORMAL);
	UpdateWindow(hwnd);
	MSG msg;
	while (GetMessage(&msg, NULL, 0, 0))
	{
		TranslateMessage(&msg);
		DispatchMessage(&msg);
	}

	return 0;
}

```
