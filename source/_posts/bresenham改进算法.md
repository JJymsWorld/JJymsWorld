---
title: bresenham改进算法(任意方向，任意斜率)
tags:
  - [计算机图形学]
categories:
  - gallery
date: 2022-07-16 17:06:54
---

算法代码实现

```cpp
void Line_by_Bresenham(HDC hdc, int x0, int y0, int xEnd, int yEnd) {
    int x, y, dx, dy, e;
    dx = xEnd - x0;
    dy = yEnd - y0;
    x = x0;
    y = y0;
    if ((dx >= 0 && dy >= 0) || (dx <= 0 && dy <= 0)) {
        if ((dx <= 0) || (dx == 0 && dy < 0)) {
            dx = -dx;
            x = xEnd;
            dy = -dy;
            y = yEnd;
        }
        if (dy < dx) {
            e = -dx;
            for (int i = 0; i < dx; i++) {
                SetPixel(hdc, x, y, RGB(0, 0, 0));
                x++;
                e = e + 2 * dy;
                if (e >= 0) {
                    y++;
                    e = e - dx - dx;
                }
            }
        }
        else {
            e = -dy;
            for (int i = 0; i < dy; i++) {
                SetPixel(hdc, x, y, RGB(0, 0, 0));
                y++;
                e = e + dx + dx;
                if (e >= 0) {
                    x++;
                    e = e - dy - dy;
                }
            }
        }
    }
    else {
        int tempx, tempy;
        if (dx < 0) {
            tempx = -dx;
            tempy = dy;
        }
        if (dy < 0) {
            tempx = dx;
            tempy = -dy;
        }
        if (tempx > tempy) {
            if (dx < 0) {
                dx = -dx;
                x = xEnd;
                dy = -dy;
                y = yEnd;
            }
            e = -dx;
            for (int i = 0; i < dx; i++) {
                SetPixel(hdc, x, y, RGB(0, 0, 0));
                x++;
                e = e - dy - dy;
                if (e >= 0) {
                    y--;
                    e = e - dx - dx;
                }
            }
        }
        else {
            if (dy < 0) {
                dx = -dx;
                x = xEnd;
                dy = -dy;
                y = yEnd;
            }
            e = -dy;
            for (int i = 0; i < dy; i++) {
                SetPixel(hdc, x, y, RGB(0, 0, 0));
                y++;
                e = e - dx - dx;
                if (e >= 0) {
                    x--;
                    e = e - dy - dy;
                }
            }
        }
    }
}
```

