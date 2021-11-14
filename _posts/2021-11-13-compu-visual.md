---
layout: post
title: OpenGL
subtitle: OpenGL
post: [c++,glut]
---

La manera en la que **OpenGL** dibuja las cosas es en base a **primitivas** que pueden ser puntos, segmentos de linea y poligonos.

### Semana 1

**glutInit(&argc, argv)**: se usa para inicializar la biblioteca GLUT  
**glutInitDisplayMode(GLUT_DOUBLE | GLUT_RGB | GLUT_DEPTH)**: establece el modo de visualización inicial  
`GLUT_DOUBLE`: máscara de bits para seleccionar una ventana con doble búffer  
`GLUT_RGB`: un alias para GLUT_RGBA  
`LUT_DEPTH`: mascara de bits para seleccionar una ventana con un búfer de profundidad  
**glClearColor(0,0,0,0)**: especifique valores claros para los búferes de color  
**glMatrixMode(GL_PROJECTION)**: Le decimos que active la matriz de proyección quw es lo que se va a retocar  
**glLoadIdentity()**: reemplaza la matriz actual con la matriz de identidad  
**glutCreateWindow("Hello World")**: dar nombre al prgrama

**GL_PLOLYGON**: se unen todos los vertices formando un poligono  

**glutDisplayFunc(funcion dibujar)**: con esto le decimos a la librería que cada vez que note que se debe redibujar, llame a una función que hemos llamado dibujar (en la mayoria de los ejemplos aparece con DISPLAY)  
**glutReshapeFunc(funcion controlDeVentana)**: controla el cambio de tamaño de la ventana de visualizacion  
**glutMainLoop()**: Siempre se pone al final del main, le decimos a la libreria que espere eternamente a que se produzcan eventos

**glRectf(-0.8f,-0.8f,-0.5f,-0.5f)**: Dibuja un rectangulo, los 4 parámetros representan respectivamente las coordenadas horizontal y vertica de dos puntos en la diagonal (de abajo a la izquierda a arriba a la derecha y el punto central de la ventana se toma como origen, lo que equivalea establecer un sistema de coordenadas dimensionales)

### Semana 3

**theta[]**: indica los ángulos iniciales en los 3 ejes  
**glColor3f(GLfloat red, GLfloat green, GLfloat blue)**: colores  
** glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT)**: borra los búferes de profundidad y color de openGL(o cualquier otro búffer o combinación de búferes). Dado que OpenGL es una máquina de estado, es una buena práctica comenzar cada cuadro con una pizarra linpia  
**glRotatef(theta[0],1.0,0.0,0.0)**: multiplica la matriz actual por una matriz de rotación


