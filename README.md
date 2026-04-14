# Apuntes-Unidad-2-Graficacion

# Unidad 2: Graficación 2D

## Introducción

La graficación en dos dimensiones (2D) es una rama fundamental de la computación gráfica enfocada en la representación visual de objetos en un plano. Este campo combina matemáticas, geometría y programación para permitir la creación, manipulación y visualización de imágenes digitales.

En el contexto de la graficación, las transformaciones bidimensionales permiten modificar objetos, mientras que las curvas, fractales y tipografías enriquecen la calidad visual de las representaciones. Estos conceptos son ampliamente utilizados en áreas como el diseño gráfico, interfaces de usuario, videojuegos, animación digital y sistemas de visualización.

---

## 2.1 Transformación Bidimensional

En graficación 2D, las transformaciones nos permiten alterar de una forma uniforme toda la imagen. Es un hecho que a veces es más fácil modificar toda la imagen que una porción de ella. Esto supone un complemento muy útil para las técnicas de dibujo manual, donde es normalmente más fácil modificar una pequeña porción del dibujo que crear un dibujo completamente nuevo.
La composición de transformaciones bidimensionales consiste en la mezcla de las transformaciones bidimensionales básicas como son traslación, sesgado y escalado.
Notemos que no mencionamos la rotación como una transformación básica, esta es en realidad la combinación de escalado y sesgado.
<img width="68" height="67" alt="image" src="https://github.com/user-attachments/assets/1958bd47-6470-4b0f-9621-3380b4ce9a7c" />


Son esenciales en procesos como animación, modelado y renderizado.

### 2.1.1 Traslación

Una traslación es el movimiento en línea recta de un objeto de una posición a otra. Movimiento de una figura, sin rotarla ni voltearla. "Deslizar".
La figura sigue viéndose exactamente igual, solo que en un lugar diferente. Se aplica una transformación en un objeto para cambiar su posición a lo largo de la trayectoria de una línea recta de una dirección de coordenadas a otra. 
<img width="320" height="123" alt="image" src="https://github.com/user-attachments/assets/7079313f-7e44-431c-83f6-215558631351" />

Matemáticamente:

x' = x + tx
y' = y + ty

Donde (tx, ty) es el vector de desplazamiento.

### 2.1.2 Escalamiento

Una transformación para alterar el tamaño de un objeto se denomina escalamiento. Dependiendo del factor de escalamiento el objeto sufrirá un cambio en su tamaño pasando a ser mayor, o menor en su segmento de longitud. Esta es la transformación del objeto especialmente interesante, pues con ella se consigue el efecto Zoom.

<img width="320" height="262" alt="image" src="https://github.com/user-attachments/assets/fdde2949-11b3-479b-9b6c-aef8ed9ae605" />

Puede ser:

* Uniforme: mismo factor en ambos ejes
* No uniforme: factores distintos

x' = x * sx
y' = y * sy

### 2.1.3 Rotación

La transformación de puntos de un objeto situados en trayectorias circulares es llama rotación. Este tipo de transformación se especifica con un ángulo de rotación, el cual determina la cantidad de rotación de cada vértice de un polígono. 
Se pueden hacer que los objetos giren alrededor de un punto arbitrario o el punto pivote de la transformación de rotación puede colocarse en cualquier parte en el interior o fuera de la frontera exterior de un objeto, el efecto de la rotación consiste en oscilar el objeto con respecto a este punto interno. 
Para rotar un objeto (en este caso bidimensional), se ha de determinar la cantidad de grados en la que ha de rotarse la figura. Para ello, y sin ningún tipo de variación sobre la figura, la cantidad de ángulo ha de ser constante sobre todos los puntos. 


Se realiza alrededor de un punto, normalmente el origen.

x' = x cosθ - y sinθ
y' = x sinθ + y cosθ

### 2.1.4 Sesgado (Shearing)

El sesgado es un tipo de transformación no rígida, pues existe una deformación del objeto original al aplicar dicha transformación. Existen dos tipos de sesgo: sesgo horizontal y sesgo vertical. 
Sesgo horizontal. Las coordenadas adyacentes al eje x permanecen fijas, los valores de y no cambian. 
Sesgo vertical. Las coordenadas adyacentes al eje y permanecen fijas, los valores de x no cambian. 

<img width="320" height="312" alt="image" src="https://github.com/user-attachments/assets/5ff07957-34c8-4246-bc58-e5dd0ebb3e7f" />


En graficación, se usa para efectos visuales o simulación de perspectiva.

x' = x + shx * y
y' = y + shy * x

---

## 2.2 Representación Matricial de las Transformaciones Bidimensionales

En sistemas de graficación, las transformaciones se implementan mediante matrices, lo que permite optimizar cálculos y combinar múltiples transformaciones en una sola operación.
<img width="1265" height="701" alt="image" src="https://github.com/user-attachments/assets/69f9b14e-28a5-4724-8d64-edecc43fd6f5" />

Se utilizan coordenadas homogéneas para facilitar este proceso.

Ejemplo de matrices en gráficos:

Traslación:

|1 0 tx|
|0 1 ty|
|0 0 1 |

Escalamiento:

|sx 0  0|
|0  sy 0|
|0  0  1|

Rotación:

|cosθ -sinθ 0|
|sinθ cosθ  0|
|0    0     1|

Estas matrices son fundamentales en APIs gráficas como OpenGL o motores de renderizado.


## Codigo de flechas 
import bpy

class ModalMoveOperator2D(bpy.types.Operator):
    """Movimiento 2D real: Flechas para X y Z"""
    bl_idname = "object.modal_move_operator_2d"
    bl_label = "Modal Move Operator 2D"

    def modal(self, context, event):
        obj = bpy.data.objects.get("Stroke")
        
        if obj is None:
            return {'FINISHED'}

        if event.value == 'PRESS':
            # --- MOVIMIENTO HORIZONTAL ---
            if event.type == 'LEFT_ARROW':
                obj.location.x -= 0.5
            elif event.type == 'RIGHT_ARROW':
                obj.location.x += 0.5
            
            # --- MOVIMIENTO VERTICAL (CORREGIDO) ---
            # Usamos 'z' para que suba/baje en la pantalla 
            # y no se acerque/aleje (que es lo que hace el eje 'y')
            elif event.type == 'UP_ARROW':
                obj.location.z += 0.5
            elif event.type == 'DOWN_ARROW':
                obj.location.z -= 0.5

            # Salida del modo
            elif event.type == 'ESC':
                print("Control 2D finalizado.")
                return {'FINISHED'}

        return {'RUNNING_MODAL'}

    def invoke(self, context, event):
        context.window_manager.modal_handler_add(self)
        print("Control 2D Activo: Flechas para mover, ESC para salir.")
        return {'RUNNING_MODAL'}

    # Registro del operador
if hasattr(bpy.types, "ModalMoveOperator2D"):
    bpy.utils.unregister_class(ModalMoveOperator2D)
    
bpy.utils.register_class(ModalMoveOperator2D)
bpy.ops.object.modal_move_operator_2d('INVOKE_DEFAULT')

---

## 2.3 Trazo de Líneas Curvas

En graficación 2D, las curvas permiten representar formas suaves y naturales que no pueden lograrse con líneas rectas.

Son ampliamente utilizadas en diseño vectorial, animación y modelado.

### 2.3.1 Curvas de Bézier

Las curvas de Bézier son esenciales en la graficación moderna, especialmente en diseño vectorial.

Se definen mediante puntos de control que determinan la forma de la curva.

Aplicaciones:

* Diseño en software como Illustrator o Inkscape
* Tipografías digitales
* Animación de trayectorias

Ventajas:

* Fácil control
* Suavidad
* Precisión

### 2.3.2 Curvas B-spline

Las curvas B-spline ofrecen mayor control en comparación con Bézier.

Son utilizadas en aplicaciones avanzadas como:

* Modelado CAD
* Animación profesional
* Superficies complejas

Ventaja clave:

* Control local: modificar un punto no afecta toda la curva


## Ejemplo de animacion 2D


<img width="834" height="413" alt="image" src="https://github.com/user-attachments/assets/a1c38e58-4c0c-4b53-8d1e-b90f06804a47" />


---

## 2.4 Fractales

En graficación, los fractales se utilizan para generar imágenes complejas a partir de algoritmos simples.

Se caracterizan por su autosimilitud y detalle infinito.

Aplicaciones en graficación:

* Generación de paisajes
* Texturas naturales
* Efectos visuales

Ejemplos:

* Conjunto de Mandelbrot
* Conjunto de Julia

Los fractales permiten crear gráficos altamente detallados con bajo costo computacional.

---

## 2.5 Uso y Creación de Fuentes de Texto

El texto es un elemento clave en la graficación 2D, especialmente en interfaces gráficas.

Tipos de fuentes:

* Bitmap: basadas en píxeles
* Vectoriales: basadas en curvas (TrueType, OpenType)

En graficación, las fuentes vectoriales son preferidas por su escalabilidad.

Aspectos importantes:

* Renderizado de texto
* Anti-aliasing para suavizar bordes
* Legibilidad en diferentes resoluciones

La creación de fuentes implica diseñar caracteres mediante curvas matemáticas, frecuentemente usando curvas Bézier.

---

## Conclusiones

La graficación 2D integra conceptos matemáticos y computacionales que permiten representar visualmente información de forma eficiente. Las transformaciones bidimensionales son fundamentales para manipular objetos dentro del espacio gráfico, mientras que su representación matricial permite optimizar procesos dentro de sistemas computacionales.

El uso de curvas como Bézier y B-spline mejora la calidad visual de los gráficos, permitiendo representar formas complejas de manera precisa. Asimismo, los fractales amplían las posibilidades creativas al generar estructuras detalladas mediante algoritmos.

Finalmente, el manejo adecuado de fuentes de texto es esencial para la comunicación visual dentro de aplicaciones gráficas. En conjunto, estos elementos constituyen la base de la graficación moderna y son indispensables en el desarrollo de software visual.

---

## Referencias Bibliográficas (Formato APA)

* Foley, J. D., Van Dam, A., Feiner, S. K., & Hughes, J. F. (1996). *Computer Graphics: Principles and Practice*. Addison-Wesley.

* Hearn, D., & Baker, M. P. (2010). *Computer Graphics with OpenGL*. Pearson.

* Rogers, D. F. (2001). *An Introduction to NURBS: With Historical Perspective*. Morgan Kaufmann.

* Angel, E., & Shreiner, D. (2015). *Interactive Computer Graphics: A Top-Down Approach*. Addison-Wesley.

* Salomon, D. (2006). *Curves and Surfaces for Computer Graphics*. Springer.

* González, R. C., & Woods, R. E. (2018). *Digital Image Processing*. Pearson.

* Lara, J. G. (s. f.). Conceptos de: graficación en 2D, transformación bidimensional, traslación, escalación, rotación y sesgado. 
