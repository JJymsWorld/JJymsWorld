---
title: 裁剪算法之Liang－Barsky
tags:
  - [计算机图形学]
categories:
  - gallery
date: 2022-07-16 17:05:22
---

## 一. 概念

裁剪是CG中许多重要问题的基础，裁剪最典型的用途是确定场景中或画面中位于给定区域之内的部分。由于在一个典型的场景中，需要对大量的点、线段进行裁剪，因此裁剪算法的效率十分重要。

关于裁剪有一些很常见的算法，比如说Cohen-Sutherland线段细分裁剪算法、中点分割算法、Cyrus-Beck算法、Liang-Barsky算法等。这里主要介绍Liang-Barsky算法。

## 二. Liang-Barsky算法

首先知道直线和裁剪框的位置关系，如下图：

![](https://cdn.jsdelivr.net/gh/qq171895821/Jymscloudiimmgg@main/img/20181109022843394.png)

可以看到直线可能完全在裁剪框的外面，也可能完全在裁剪框的里面，或者和裁剪框相交。

所以裁剪的规则是这样的：

1. 如果直线的两个端点都在窗口边界内，如ab，则直线保留；

2. 如果直线的一个端点在窗口边界之内，另一个端点在窗口边界之外，如ef，则应从直线与边界的而交点处裁剪掉；

3. 如果直线的两个端点都在窗口边界之外，则分两种情况：
   - 一种情况如ij，直线全部在窗口之外，应该全部裁剪掉；
   - 另一种情况如gh，直接横穿窗口边界，应该保留相交的片段，裁剪掉剩余的片段。

---

**算法推导：**

如下图所示：

![](https://cdn.jsdelivr.net/gh/qq171895821/Jymscloudiimmgg@main/img/liang2.png)

这里用参数方程表示直线P~1~P~2~,其中t是一个参数，t∈[0,1]：

​	![](https://cdn.jsdelivr.net/gh/qq171895821/Jymscloudiimmgg@main/img/liang3.png)

裁剪区域内部可以表达为两个不等式：

![](https://cdn.jsdelivr.net/gh/qq171895821/Jymscloudiimmgg@main/img/2018110902451641.png)

把上述参数方程代入得到不等式：

![](https://cdn.jsdelivr.net/gh/qq171895821/Jymscloudiimmgg@main/img/20181109024816121.png)

即

![](https://cdn.jsdelivr.net/gh/qq171895821/Jymscloudiimmgg@main/img/20181109024844225.png)

![](https://cdn.jsdelivr.net/gh/qq171895821/Jymscloudiimmgg@main/img/20181109024906302.png)

![](https://cdn.jsdelivr.net/gh/qq171895821/Jymscloudiimmgg@main/img/20181109025824716.png)

**把直线看成是一条有方向的线段，把窗口的四条边及其延长线分成两类：入边和出边**

入边：左边界和下边界----从裁剪框外向内

出边：右边界和上边界----从裁剪框内向外

![](https://cdn.jsdelivr.net/gh/qq171895821/Jymscloudiimmgg@main/img/20181109030255360.png)

**分情况讨论**

1. d=0，q<0, 说明直线与裁剪框平行，并且位于裁剪框的外面，直线为不可见，可抛弃，直接结束。q>=0，说明直线在它所平行的窗口边界的内部，还需进一步计算确定直线是否在窗口内、外、或者相交
2. d<0，说明直线是从裁剪边界的外部延伸到内部
3. d>0,  说明直线是从裁剪边界的内部延伸到外部

对于d≠0，可以利用式子计算直线与边界k的交点的参数u。对于每条直线，可以计算直线位于裁剪窗口内线段的参数d1和d2

  d1的值是由那些使得直线是从外部延伸到内部的窗口边界决定。对于这些边计算ri = qi/di.

  d1 = max(ri,0)

 d2的值是由那些使得直线是从内部延伸到窗口边界决定

 d2 = min(ri,1)

  如果d1>d2,这条直线完全在窗口的外面，不可见，可抛弃，否则，根据参数u的两个值，计算出裁剪后线段的端点

### 伪代码如下：

```shell
#Liang-Barsky two-dimensional clipping algorithm
#P1 and P2 are the line end points with components x1,y1,x2,y2
#tL and tU are the lower and upper parametric limits
#xL,xR,yB,yT are the left,right,bottom and top window edges
	function clipt(d,q,tL,tU):
		clipt performs trivial rejection tests and finds
		the max of the lower set of parameter values and 
		the min of the upper set of parameter values
		
		visible = true
		if d=0 and q<0 then #line is outside and parallel to edge
			visible = false
		else if d < 0 then #looking for upper limit
			t = q/d
			if t>tU then #check for trivial invisible
				visible = false
			else if t>tL then #find the min of the max
				tL = t
			end if
		else if d>0 then #look for the lower limit
			t = q/d
			if t<tL then #check for trivial invisible
				visible = false
			else if t<tU then #find the max of the min
				tU = t
			end if
			
		end if
		return visible
	end function
	
	start the main funciton:
		tL=0,tU=1
		delatx = x2-x1
		if clipt(-delatx,x1-xL,tL,tU) = true then #left edge
			if clipt(delatx,xR-x1,tL,tU) = true then #right edge
				deltay = y2-y1
				if clipt(-deltay,y1-yB,tL,tU) = true then #bottom edge
					if clipt(deltay,yT-y1,tL,tU) = true then #top edge
						if tU<1 then 
							x2 = x1+tU*delatx
							y2 = y1+tU*deltay
						end if
						if tL>0 then
							x1 = x1+tL*deltax
							y1 = y1+tL*deltay
						end if
						Draw P1,P2
					end if
				end if
			end if
		end if
```



### openGL实现代码：

```cpp
#include <windows.h>
#include <GL/glut.h>
#include <math.h>
#include<stdio.h>
float xmin,xmax,ymin,ymax;
 
 
void myinit(void)
{
    glShadeModel (GL_FLAT);
    glClearColor (1.0, 1.0, 1.0, 0.0);
}
 
 
void myReshape(int w, int h)
{
    glViewport(0, 0, w, h);
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    if (w <= h) 
	gluOrtho2D (0.0, 1.0, 0.0, 1.0*(GLfloat)h/(GLfloat)w);
    else 
	gluOrtho2D (0.0, 1.0*(GLfloat)w/(GLfloat)h, 0.0, 1.0);
    glMatrixMode(GL_MODELVIEW);
}
 
int Clip(float p,float q,float *tL,float *tU) 
{  
	int flag=1;/*flag为标志变量0表示舍弃1表示可见*/ 
	float r; 
 
	if (p<0.0) 
	{ 
		r=q/p; 
		if (r>*tU)
			flag=0; 
		else if (r>*tL) {
			*tL = r;/*m取进入点最大参数值*/ 
		} 
	} 
	else if (p>0.0) { 
		r=q/p; 
		if (r<*tL)
			flag=0; 
		else if (r<*tU) {
			*tU = r;/*n取离开点的最小值*/ 
		} 
	} 
 
	else if (q<0 && p==0) //平行于边界而且在界外的线 
		flag=0; 
	return flag; 
}
void myclip()
// line clipping algorithm 
{
	float dx, dy, x1,tL,tU, x2, y1, y2;
	tL = 0, tU = 1.0;
	printf("请输入线段的两个顶点坐标x1,y1,x2,y2:\n");
	scanf("%f%f%f%f",&x1,&y1,&x2,&y2);
 
	glBegin(GL_LINES); 
	glColor4f (0.0, 0.0, 0.0, 0.0); 
	glVertex2f(x1, y1); // line startpoint 
	glVertex2f(x2, y2); // line endpoint 
	glEnd(); 
 
	dx=x2-x1; 
  
	if (Clip(-dx, x1 - xmin, &tL, &tU))
		if (Clip(dx, xmax - x1, &tL, &tU)){
			dy=y2-y1; 
			if (Clip(-dy, y1 - ymin, &tL, &tU))
				if (Clip(dy, ymax - y1, &tL, &tU))
				{ 
					if (tU<1.0) 
					{ 
						x2 = x1 + tU*dx;//通过n求得裁剪后的p2端点 
						y2 = y1 + tU*dy;
					} 
					if (tL>0.0)
					{ 
						x1 = x1 + tL*dx;//通过m求得裁剪后的p1端点 
						y1 = y1 + tL*dy;
					} 
					glBegin(GL_LINES); 
					glColor4f (1.0, 0.0, 0.0, 1.0); 
					glVertex2f( x1, y1); // clipped line startpoint 
					glVertex2f( x2, y2); // clipped line endpoint 
					glEnd();
				} 
		} 
 
}
 
void display(void)
 {
    glClear(GL_COLOR_BUFFER_BIT);
 
	printf("请分别输入矩形的左右下上边界：\n");
	scanf("%f%f%f%f",&xmin,&xmax,&ymin,&ymax);
    glColor4f (1.0, 1.0, 0.0, 0.75);
	glBegin(GL_POLYGON);
	glVertex2f( xmin, ymin); // Bottom Left
	glVertex2f( xmax, ymin); // Bottom Left
	glVertex2f( xmax, ymax); // Bottom Right
	glVertex2f( xmin, ymax); // Bottom Right
	glEnd();
 
   myclip();
    glFlush();
}
 
 
/*  Main Loop
 *  Open window with initial window size, title bar, 
 *  RGBA display mode, and handle input events.
 */
int main(int argc, char** argv)
{
   glutInit(&argc, argv);
   glutInitDisplayMode (GLUT_SINGLE | GLUT_RGBA);
   //define size and the relative positon of the applicaiton window on the display
   glutInitWindowSize (500, 500); 
   glutInitWindowPosition (100, 100);
 	//init the defined window with "argv[1]" as topic showed on the top the window
   glutCreateWindow (argv[0]);
	// opengl setup
   myinit ();
 
	//define callbacks
   glutDisplayFunc(display); 
   glutReshapeFunc(myReshape);
   //enter the loop for display
   glutMainLoop();
 
	return 1;

```



### MFC实现代码：

```cpp
// 511works.cpp : 定义应用程序的入口点。
//

#include "framework.h"
#include "511works.h"
#include<math.h>
#include<algorithm>
#include<stdio.h>
#include<string>
#include<cstring>
#include <atlstr.h>

using namespace std;

#define MAX_LOADSTRING 100

// 全局变量:
HINSTANCE hInst;                                // 当前实例
WCHAR szTitle[MAX_LOADSTRING];                  // 标题栏文本
WCHAR szWindowClass[MAX_LOADSTRING];            // 主窗口类名

//自定义全局变量
POINT point[2];
POINT LinePoint[100];
int position = 0;
int LinePosition = 0;
RECT rect;
POINT p;
int xmin, xmax, ymin, ymax;


//函数声明
void myclip(HDC hdc, HWND hWnd);
void GetMinMax(POINT* point);


// 此代码模块中包含的函数的前向声明:
ATOM                MyRegisterClass(HINSTANCE hInstance);
BOOL                InitInstance(HINSTANCE, int);
LRESULT CALLBACK    WndProc(HWND, UINT, WPARAM, LPARAM);
INT_PTR CALLBACK    About(HWND, UINT, WPARAM, LPARAM);

int APIENTRY wWinMain(_In_ HINSTANCE hInstance,
                     _In_opt_ HINSTANCE hPrevInstance,
                     _In_ LPWSTR    lpCmdLine,
                     _In_ int       nCmdShow)
{
    UNREFERENCED_PARAMETER(hPrevInstance);
    UNREFERENCED_PARAMETER(lpCmdLine);

    // TODO: 在此处放置代码。

    // 初始化全局字符串
    LoadStringW(hInstance, IDS_APP_TITLE, szTitle, MAX_LOADSTRING);
    LoadStringW(hInstance, IDC_MY511WORKS, szWindowClass, MAX_LOADSTRING);
    MyRegisterClass(hInstance);

    // 执行应用程序初始化:
    if (!InitInstance (hInstance, nCmdShow))
    {
        return FALSE;
    }

    HACCEL hAccelTable = LoadAccelerators(hInstance, MAKEINTRESOURCE(IDC_MY511WORKS));

    MSG msg;

    // 主消息循环:
    while (GetMessage(&msg, nullptr, 0, 0))
    {
        if (!TranslateAccelerator(msg.hwnd, hAccelTable, &msg))
        {
            TranslateMessage(&msg);
            DispatchMessage(&msg);
        }
    }

    return (int) msg.wParam;
}



//
//  函数: MyRegisterClass()
//
//  目标: 注册窗口类。
//
ATOM MyRegisterClass(HINSTANCE hInstance)
{
    WNDCLASSEXW wcex;

    wcex.cbSize = sizeof(WNDCLASSEX);

    wcex.style          = CS_HREDRAW | CS_VREDRAW;
    wcex.lpfnWndProc    = WndProc;
    wcex.cbClsExtra     = 0;
    wcex.cbWndExtra     = 0;
    wcex.hInstance      = hInstance;
    wcex.hIcon          = LoadIcon(hInstance, MAKEINTRESOURCE(IDI_MY511WORKS));
    wcex.hCursor        = LoadCursor(nullptr, IDC_ARROW);
    wcex.hbrBackground  = (HBRUSH)(COLOR_WINDOW+1);
    wcex.lpszMenuName   = MAKEINTRESOURCEW(IDC_MY511WORKS);
    wcex.lpszClassName  = szWindowClass;
    wcex.hIconSm        = LoadIcon(wcex.hInstance, MAKEINTRESOURCE(IDI_SMALL));

    return RegisterClassExW(&wcex);
}

//
//   函数: InitInstance(HINSTANCE, int)
//
//   目标: 保存实例句柄并创建主窗口
//
//   注释:
//
//        在此函数中，我们在全局变量中保存实例句柄并
//        创建和显示主程序窗口。
//
BOOL InitInstance(HINSTANCE hInstance, int nCmdShow)
{
   hInst = hInstance; // 将实例句柄存储在全局变量中

   HWND hWnd = CreateWindowW(szWindowClass, szTitle, WS_OVERLAPPEDWINDOW,
      CW_USEDEFAULT, 0, CW_USEDEFAULT, 0, nullptr, nullptr, hInstance, nullptr);

   if (!hWnd)
   {
      return FALSE;
   }

   ShowWindow(hWnd, nCmdShow);
   UpdateWindow(hWnd);

   return TRUE;
}

//
//  函数: WndProc(HWND, UINT, WPARAM, LPARAM)
//
//  目标: 处理主窗口的消息。
//
//  WM_COMMAND  - 处理应用程序菜单
//  WM_PAINT    - 绘制主窗口
//  WM_DESTROY  - 发送退出消息并返回
//
//
LRESULT CALLBACK WndProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam)
{
    HDC hdc;
    switch (message)
    {
    case WM_COMMAND:
        {
            int wmId = LOWORD(wParam);
            // 分析菜单选择:
            switch (wmId)
            {
            case IDM_ABOUT:
                DialogBox(hInst, MAKEINTRESOURCE(IDD_ABOUTBOX), hWnd, About);
                break;
            case IDM_EXIT:
                DestroyWindow(hWnd);
                break;
            case ID_JUXING:
                hdc = GetDC(hWnd);
                Rectangle(hdc, point[0].x, point[0].y, point[1].x, point[1].y);
                UpdateWindow(hWnd);
                break;
            case ID_DRAWLINE:
                hdc = GetDC(hWnd);
                MoveToEx(hdc, LinePoint[LinePosition - 2].x, LinePoint[LinePosition - 2].y, NULL);
                LineTo(hdc, LinePoint[LinePosition - 1].x, LinePoint[LinePosition - 1].y);
                UpdateWindow(hWnd);
                break;
            case ID_CUT:
                hdc = GetDC(hWnd);
                GetMinMax(point);
                myclip(hdc, hWnd);
                UpdateWindow(hWnd);
                break;
            default:
                return DefWindowProc(hWnd, message, wParam, lParam);
            }
        }
        break;
    case WM_LBUTTONDOWN:
        hdc = GetDC(hWnd);
        position = position % 2;
        point[position].x = LOWORD(lParam);
        point[position].y = HIWORD(lParam);
        position++;
        break;
    case WM_RBUTTONDOWN:
        hdc = GetDC(hWnd);
        LinePoint[LinePosition].x = LOWORD(lParam);
        LinePoint[LinePosition].y = HIWORD(lParam);
        LinePosition++;
        break;
    case WM_PAINT:
        {
            PAINTSTRUCT ps;
            hdc = BeginPaint(hWnd, &ps);
            // TODO: 在此处添加使用 hdc 的任何绘图代码...
            EndPaint(hWnd, &ps);
        }
        break;
    case WM_DESTROY:
        PostQuitMessage(0);
        break;
    default:
        return DefWindowProc(hWnd, message, wParam, lParam);
    }
    return 0;
}

// “关于”框的消息处理程序。
INT_PTR CALLBACK About(HWND hDlg, UINT message, WPARAM wParam, LPARAM lParam)
{
    UNREFERENCED_PARAMETER(lParam);
    switch (message)
    {
    case WM_INITDIALOG:
        return (INT_PTR)TRUE;

    case WM_COMMAND:
        if (LOWORD(wParam) == IDOK || LOWORD(wParam) == IDCANCEL)
        {
            EndDialog(hDlg, LOWORD(wParam));
            return (INT_PTR)TRUE;
        }
        break;
    }
    return (INT_PTR)FALSE;
}

void GetMinMax(POINT * point) {
    xmin = point[0].x;
    xmax = point[1].x;
    ymin = point[0].y;
    ymax = point[1].y;
}

int clip(float p, float q, float* tL, float* tU) {
    int flag = 1; //0表示舍弃1表示可见
    float r;
    if (p < 0.0) {
        r = q / p;
        if (r > *tU) {
            flag = 0;
        }
        else if (r > *tL) {
            *tL = r;//取进入点最大参数值
        }
    }

    else if (p > 0.0) {
        r = q / p;
        if (r < *tL) {
            flag = 0;
        }
        else if (r < *tU) {
            *tU = r;//取离开点最小参数值
        }
    }

    else if (q < 0 && p == 0) {
        flag = 0;
    }
    return flag;
}

void myclip(HDC hdc, HWND hWnd) {
    HPEN hp;
    float dx, dy, x1, tL, tU, x2, y1, y2;
    tL = 0, tU = 1.0;
    x1 = LinePoint[LinePosition - 2].x;
    y1 = LinePoint[LinePosition - 2].y;
    x2 = LinePoint[LinePosition - 1].x;
    y2 = LinePoint[LinePosition - 1].y;
    dx = x2 - x1;
    if (clip(-dx, x1 - xmin, &tL, &tU)) {
        if (clip(dx, xmax - x1, &tL, &tU)) {
            dy = y2 - y1;
            if (clip(-dy, y1 - ymin, &tL, &tU)) {
                if (clip(dy, ymax - y1, &tL, &tU)) {
                    if (tU < 1.0) {
                        x2 = x1 + tU * dx;
                        y2 = y1 + tU * dy;
                    }
                    if (tL > 0.0) {
                        x1 = x1 + tL * dx;
                        y1 = y1 + tL * dy;
                    }
                    hp = CreatePen(PS_SOLID, 1, RGB(255, 0, 0));
                    SelectObject(hdc, hp);
                    MoveToEx(hdc, x1, y1, NULL);
                    LineTo(hdc, x2, y2);
                    DeleteObject(hp);
                    string tLString = to_string(tL);
                    string tUString = to_string(tU);
                    LPCTSTR startParams = L"开始组参数: ";
                    LPCTSTR endParams = L"结束组参数: ";
                    LPCTSTR pointposition = L"裁剪后线段端点分别为(顺序为x1,y1,x2,y2): ";
                    string x1string = to_string(x1);
                    string x2string = to_string(x2);
                    string y1string = to_string(y1);
                    string y2string = to_string(y2);
                    CString content = CString(startParams) + CString(tLString.c_str()) + CString(endParams) + CString(tUString.c_str()) + CString(pointposition) + CString(x1string.c_str()) + CString(L" ") + CString(y1string.c_str()) + CString(L" ") + CString(x2string.c_str()) + CString(L" ") + CString(y2string.c_str());
                    MessageBox(hWnd, content, L"result", MB_OK);
                }
            }
        }
    }
}
```



