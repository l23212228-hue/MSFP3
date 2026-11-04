[![Open in MATLAB Online](https://www.mathworks.com/images/responsive/global/open-in-matlab-online.svg)](https://matlab.mathworks.com/open/github/v1?repo=l23212228-hue/MSFP3)

# Práctica 3: Modelado y control del sistema musculoesquelético

Repositorio académico de la asignatura **Modelado de Sistemas Fisiológicos**, correspondiente al programa de **Ingeniería Biomédica** del Tecnológico Nacional de México / Instituto Tecnológico de Tijuana. En esta práctica se modela un compartimento musculoesquelético simplificado, se construye su circuito eléctrico equivalente mediante la analogía fuerza-voltaje, se obtiene su función de transferencia por el principio de superposición, y se evalúa la respuesta de un individuo sano (*control*) frente a un caso simulado de atrofia muscular (*caso*), diseñando una estrategia de control *in silico* mediante un controlador PID.

**Docente:** Dr. Paul Antonio Valle Trujillo  
**Correo:** paul.valle@tectijuana.edu.mx  
**Departamento:** Departamento de Ingeniería Eléctrica y Electrónica  
**Institución:** Tecnológico Nacional de México / Instituto Tecnológico de Tijuana  
**Asignatura:** Modelado de Sistemas Fisiológicos  
**Programa:** Ingeniería Biomédica  
**Estudiante:** Reyes Zamudio Mariant · 23212228 · l23212228@tectijuana.edu.mx  

**Sede:** Blvd. Alberto Limón Padilla s/n, Tijuana, C. P. 22454, B. C., México.

---

<a id="contenido"></a>
## Contenido

1. [Resumen](#resumen)
2. [Competencia y objetivos](#objetivos)
3. [Relación con el temario](#temario)
4. [Modelo físico y analogía electromecánica](#analogia)
5. [Formulación matemática y superposición](#modelo)
6. [Análisis de estabilidad y error en estado estacionario](#analisis)
7. [Parámetros del sistema (Control vs. Caso)](#parametros)
8. [Implementación en MATLAB, Simulink y Simscape Electrical](#matlab)
9. [Control en lazo cerrado y sintonización PID](#pid)
10. [Reproducción en Python](#python)
11. [Resultados y verificación cruzada](#resultados)
12. [Flujo de trabajo](#flujo)
13. [Actividades del estudiante](#actividades)
14. [Estructura del repositorio](#estructura)
15. [Requisitos](#requisitos)
16. [Referencias](#referencias)

---

<a id="resumen"></a>
## Resumen

Esta práctica aborda el modelado dinámico de las propiedades viscoelásticas del sistema musculoesquelético. La respuesta pasiva y activa del tejido muscular se representa mediante un modelo mecánico compuesto por elementos elásticos en serie ($C_s$), elementos elásticos en paralelo ($C_p$), un amortiguador viscoso ($R$) y una fuente de fuerza contráctil activa ($F_0(t) = \alpha F(t)$ con $0 < \alpha < 1$). 

A través de la **analogía fuerza-voltaje**, el sistema mecánico se transforma en un circuito eléctrico equivalente donde las fuerzas corresponden a voltajes, las velocidades a corrientes y los desplazamientos a cargas eléctricas. Debido a la presencia de la fuente de fuerza contráctil dependiente de la entrada, la deducción del modelo matemático requiere la aplicación del **principio de superposición** para determinar la función de transferencia del sistema $G(s) = F_s(s)/F(s)$ (o $X(s)/F(s)$).

Se simula la respuesta al impulso unitario para comparar el comportamiento de un individuo sano (*control*) frente a un caso simulado de atrofia muscular (*caso*), caracterizado por un cambio en la componente viscosa ($R$). Posteriormente, se diseña un **controlador PID** en lazo cerrado para obligar al sistema atrófico a seguir la dinámica del sujeto sano, validando los resultados cruzados entre Simulink, Simscape Electrical, MATLAB y Python.

**Palabras clave:** sistema musculoesquelético; atrofia muscular; analogía fuerza-voltaje; principio de superposición; controlador PID; MATLAB; Simulink; Simscape Electrical; Python; simulación *in silico*.

[Volver al contenido](#contenido)

---

<a id="objetivos"></a>
## Competencia y objetivos

### Competencia a desarrollar

Modeliza sistemas fisiológicos mediante analogías entre sistemas físicos, ecuaciones diferenciales y funciones de transferencia para analizar su comportamiento dinámico y diseña estrategias de control mediante técnicas de control clásico. En particular, analiza un modelo mecánico simplificado del sistema musculoesquelético, construye su circuito eléctrico equivalente mediante la analogía fuerza-voltaje, compara la respuesta de un individuo sano (*control*) con un caso simulado de atrofia muscular (*caso*) y diseña una estrategia de control *in silico* orientada a reducir las diferencias entre ambas respuestas.

### Objetivo general

Formular el modelo matemático del sistema musculoesquelético mediante su circuito equivalente fuerza-voltaje y el principio de superposición, analizando su estabilidad y error en estado estacionario, e implementando un controlador PID *in silico* para compensar la atrofia muscular mediante verificaciones computacionales en MATLAB, Simulink, Simscape y Python.

### Objetivos específicos

1. Analizar el esquema mecánico del músculo e identificar sus componentes elásticos ($C_s, C_p$), viscosos ($R$) y activos ($F_0$).
2. Transformar el modelo mecánico en un circuito eléctrico equivalente empleando la analogía fuerza-voltaje.
3. Derivar la función de transferencia $G(s)$ utilizando el principio de superposición (apagando alternadamente las fuentes de fuerza).
4. Determinar los polos del sistema y calcular el error en estado estacionario $e(0)$ ante una entrada escalón.
5. Emular la respuesta al impulso unitario en lazo abierto en Simulink/Simscape y Python para las condiciones *control* y *caso*.
6. Diseñar y sintonizar las ganancias ($k_P, k_I, k_D$) de un controlador PID en lazo cerrado para minimizar el error $e(t) = x_{\text{control}}(t) - x_{\text{caso}}(t)$.
7. Realizar una verificación cruzada de resultados entre las representaciones matemáticas, bloques de función de transferencia, circuitos de Simscape y scripts de Python.
8. Diseñar un diagrama biológico detallado en BioRender que vincule la estructura muscular fisiológica con sus equivalentes mecánicos y eléctricos.

[Volver al contenido](#contenido)

---

<a id="temario"></a>
## Relación con el temario

Esta práctica se enmarca dentro del temario oficial de la asignatura **Modelado de Sistemas Fisiológicos (IBF-1020)**:

| Unidad | Área | Contenidos aplicados |
|---|---|---|
| I | Sistemas de control retroalimentados | Retroalimentación negativa, error en estado estacionario, respuesta temporal al impulso y sintonización de controladores PID. |
| II | Modelado matemático | Analogías físicas (fuerza-voltaje), superposición de fuentes, compliancias pasivas/activas y funciones de transferencia. |
| III | Controladores | Estrategias de control clásico *in silico* aplicadas a patologías musculoesqueléticas (atrofia muscular). |

[Volver al contenido](#contenido)

---

<a id="analogia"></a>
## Modelo físico y analogía electromecánica

El compartimento musculoesquelético se compone de una compliancia pasiva en paralelo ($C_p$, sarcolema y tejido conjuntivo), una compliancia en serie ($C_s$, tendón), una amortiguación viscosa ($R$, fricción tisular) y una fuerza activa contráctil ($F_0(t) = \alpha F(t)$).

### Tabla de equivalencias (Analogía Fuerza-Voltaje)

| Sistema mecánico | Variable / Elemento | Sistema eléctrico equivalente | Variable / Elemento |
|---|---|---|---|
| Fuerza | $F(t)$ | Voltaje | $V(t)$ |
| Velocidad | $\dot{x}(t) = \frac{dx}{dt}$ | Corriente | $I(t)$ |
| Desplazamiento | $x(t)$ | Carga eléctrica | $q(t)$ |
| Amortiguador | $R$ | Resistencia | $R$ |
| Compliancia mecánica | $C_p, C_s$ | Capacitancia | $C_p, C_s$ |
| Masa | $M$ | Inductancia | $L$ |

> **Nota topológica:** En la analogía fuerza-voltaje, los elementos conectados mecánicamente en paralelo se representan mediante ramas eléctricas en serie, y los elementos mecánicos en serie se convierten en conexiones eléctricas en paralelo.

[Volver al contenido](#contenido)

---

<a id="modelo"></a>
## Formulación matemática y superposición

Debido a que el circuito eléctrico equivalente contiene dos fuentes de voltaje/fuerza ($F(t)$ y $F_0(t) = \alpha F(t)$), la respuesta total $F_s(s)$ se obtiene aplicando el **principio de superposición**:

$$F_s(s) = F_{s1}(s) + F_{s2}(s)$$

### 1. Apagando la fuente contráctil $F_0(t) = 0$
Al mantener únicamente la fuerza externa $F(t)$, las ecuaciones del nodo conducen a:

$$C_p \frac{dF_{s1}(t)}{dt} = C_s \frac{d[F(t) - F_{s1}(t)]}{dt} + \frac{F(t) - F_{s1}(t)}{R}$$

Aplicando la Transformada de Laplace con condiciones iniciales nulas:

$$F_{s1}(s) = \frac{C_s R s + 1}{R(C_p + C_s)s + 1} F(s)$$

### 2. Apagando la fuerza externa $F(t) = 0$
Al considerar únicamente la contribución de la fuente interna $-F_0(t) = -\alpha F(t)$:

$$- \alpha F(t) = R x(t) + \frac{1}{C_p + C_s} \int x(t) dt$$

De donde se obtiene la respuesta del componente $F_{s2}(s)$:

$$F_{s2}(s) = \frac{-\alpha}{R(C_p + C_s)s + 1} F(s)$$

### 3. Función de Transferencia Global $G(s)$
Sumando ambas contribuciones $F_s(s) = F_{s1}(s) + F_{s2}(s)$:

$$F_s(s) = \frac{C_s R s + 1 - \alpha}{R(C_p + C_s)s + 1} F(s)$$

Por lo tanto, la función de transferencia del sistema musculoesquelético es:

$$\boxed{G(s) = \frac{F_s(s)}{F(s)} = \frac{R C_s s + 1 - \alpha}{R(C_p + C_s)s + 1}}$$

[Volver al contenido](#contenido)

---

<a id="analisis"></a>
## Análisis de estabilidad y error en estado estacionario

### 1. Polos del sistema y Estabilidad
El denominador de la función de transferencia define la ecuación característica:

$$R(C_p + C_s)s + 1 = 0 \implies \lambda = -\frac{1}{R(C_p + C_s)}$$

Dado que $R > 0$, $C_p > 0$ y $C_s > 0$, el polo $\lambda$ se localiza estrictamente en el semiplano izquierdo del plano complejo ($\text{Re}(\lambda) < 0$), lo que garantiza que el sistema es **asintóticamente estable** tanto en la condición de *control* como en la de *caso*.

### 2. Error en Estado Estacionario $e_{ss}$
Aplicando el teorema del valor final para una entrada escalón unitario ($F(s) = 1/s$):

$$e(0) = \lim_{s \to 0} s F(s) \left[ 1 - G(s) \right] = \lim_{s \to 0} \left( 1 - \frac{R C_s s + 1 - \alpha}{R(C_p + C_s)s + 1} \right)$$

$$\boxed{e_{ss} = 1 - (1 - \alpha) = \alpha}$$

El error en estado estacionario depende directamente de la fracción de fuerza contráctil activa $\alpha$.

[Volver al contenido](#contenido)

---

<a id="parametros"></a>
## Parámetros del sistema (Control vs. Caso)

La condición de **atrofia muscular** se simula modificando la componente viscosa del tejido ($R$), incrementando la resistencia al movimiento mientras que las compliancias y el factor de contracción activa se mantienen constantes.

| Parámetro | Descripción | Control (Sano) | Caso (Atrofia) | Unidades |
|---|---|:---:|:---:|:---:|
| $F(t)$ | Amplitud de fuerza de entrada | $1.0$ | $1.0$ | $\text{V}$ ($\text{N}$) |
| $\alpha$ | Factor de fuerza contráctil activa | $0.25$ | $0.25$ | Adimensional |
| $C_s$ | Compliancia en serie (Tendón) | $10$ | $10$ | $\mu\text{F}$ |
| $C_p$ | Compliancia en paralelo (Sarcolema) | $100$ | $100$ | $\mu\text{F}$ |
| $R$ | Amortiguación viscosa del tejido | $100$ | $10\,000$ ($10\text{ k}$) | $\Omega$ |

[Volver al contenido](#contenido)

---

<a id="matlab"></a>
## Implementación en MATLAB, Simulink y Simscape Electrical

El sistema se implementa en MATLAB/Simulink mediante tres enfoques equivalentes:
1. **Ecuaciones del sistema:** Bloques de operaciones continuas derivados de la analogía física.
2. **Función de Transferencia:** Bloque `Transfer Fcn` cargando $G(s) = \frac{R C_s s + 1 - \alpha}{R(C_p + C_s)s + 1}$.
3. **Simscape Electrical:** Red física de componentes ($R, C_p, C_s$ y fuentes dependientes) para verificar la emulación circuital.

Las simulaciones en lazo abierto evalúan la **respuesta al impulso unitario** en un intervalo temporal de $t \in [0, 10]\text{ s}$.

[Volver al contenido](#contenido)

---

<a id="pid"></a>
## Control en lazo cerrado y sintonización PID

Para compensar la respuesta alterada por la atrofia muscular, se implementa un sistema de control con retroalimentación negativa donde la señal de referencia es la salida del individuo sano $x_{\text{control}}(t)$.

$$\text{Error: } e(t) = x_{\text{control}}(t) - x_{\text{caso}}(t)$$
