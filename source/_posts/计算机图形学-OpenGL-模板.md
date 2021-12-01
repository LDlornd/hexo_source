---
title: 计算机图形学 OpenGL 模板
author: lornd
mathjax: true
date: 2021-12-01 14:38:38
url: opengl_samples
tags:
---

## 整体程序框架

```cpp
 #include <glut.h>
#include <cstdlib>

void myDisplay(void) {
    // do something
}

int main(int argc, char* argv[]) {
    glutInit(&argc, argv);
    glutInitDisplayMode(GLUT_RGB | GLUT_SINGLE);
    glutInitWindowPosition(100, 100);
    glutInitWindowSize(500, 500);
    glutCreateWindow("Sierpinski 镂垫");
    glutDisplayFunc(&myDisplay);
    glutMainLoop();
    return 0;
}
```

## Sierpinski 镂垫

```cpp
void myDisplay(void) {
    GLfloat points[3][2] = { { 0, 0 }, { 1, 0 }, { 0, 1 } };
    GLfloat np[2] = { 0.2, 0.3 };

    glClear(GL_COLOR_BUFFER_BIT);

    glBegin(GL_POINTS);
    
    for (int i = 0; i < 3; ++i) {
        glVertex2fv(points[0]);
    }

    int epochs = 10000;

    for (int i = 1; i <= epochs; ++i) {
        int tmp = rand() % 3;
        np[0] = (np[0] + points[tmp][0]) / 2;
        np[1] = (np[1] + points[tmp][1]) / 2;
        glVertex2fv(np);
    }

    glEnd();
    glFlush();
}
```
