[![Open in MATLAB Online](https://www.mathworks.com/images/responsive/global/open-in-matlab-online.svg)](https://matlab.mathworks.com/open/github/v1?repo=USUARIO/REPOSITORIO)

# MSFP3: Modelado y control del sistema musculoesquelético

Repositorio académico de la asignatura **Modelado de Sistemas Fisiológicos**, correspondiente al programa de **Ingeniería Biomédica** del Tecnológico Nacional de México / Instituto Tecnológico de Tijuana. Esta práctica integra modelado mecánico, analogías electromecánicas, superposición de circuitos, análisis temporal y de estabilidad, diseño de controladores PID y simulación computacional *in silico*.

El caso de estudio representa el **sistema musculoesquelético** mediante un modelo viscoelástico de primer orden (elemento contráctil, amortiguador viscoso, tendón y estructuras elásticas). Se busca comparar la respuesta de un individuo sano (**control**) con la de un caso simulado de **atrofia muscular** (**caso**), tanto en lazo abierto como en lazo cerrado con un controlador PID que reduce la diferencia entre ambas respuestas.

**Docente:** Dr. Paul Antonio Valle Trujillo  
**Correo:** paul.valle@tectijuana.edu.mx  
**Departamento:** Ingeniería Eléctrica y Electrónica  
**Institución:** Tecnológico Nacional de México / Instituto Tecnológico de Tijuana  
**Asignatura:** Modelado de Sistemas Fisiológicos  
**Programa:** Ingeniería Biomédica  
**Estudiante:** Neltzon Farid García Altamirano · 23212194 · l23212194@tijuana.tecnm.mx

**Repositorio de la asignatura:** https://github.com/DrPaulValle/MSF-IBF1020  
**Sede:** Blvd. Alberto Limón Padilla s/n, Tijuana, C. P. 22454, B. C., México.

---

<a id="contenido"></a>
## Contenido

1. [Resumen](#resumen)
2. [Competencia y objetivos](#objetivos)
3. [Relación con el temario](#temario)
4. [Modelo físico y analogía eléctrica](#analogia)
5. [Formulación matemática](#modelo)
6. [Análisis temporal y de estabilidad](#analisis)
7. [Señales de entrada y condiciones de simulación](#senales)
8. [Implementación en MATLAB, Simulink y Simscape](#matlab)
9. [Control en lazo cerrado y sintonización PID](#pid)
10. [Reproducción en Python](#python)
11. [Resultados y verificación cruzada](#resultados)
12. [Flujo de trabajo](#flujo)
13. [Actividades del estudiante](#actividades)
14. [Estructura del repositorio](#estructura)
15. [Requisitos](#requisitos)
16. [Reproducibilidad y entregables](#reproducibilidad)
17. [Referencias](#referencias)

---

<a id="resumen"></a>
## Resumen

La práctica tiene como propósito **modelar, analizar, simular y controlar** un compartimento musculoesquelético a partir de un modelo mecánico simplificado. El músculo se aproxima mediante un amortiguador viscoso $R$, un elemento elástico en serie $C_s$ (tendón), un elemento elástico en paralelo $C_p$ (estructuras pasivas del músculo) y una fuente de fuerza activa $F_0(t)=\alpha F(t)$ que representa al elemento contráctil. Mediante la analogía fuerza–voltaje se obtiene un circuito eléctrico equivalente, que se resuelve por **superposición**: primero con la fuente $F_0(t)$ apagada y después con $F(t)$ apagada. La suma de ambas contribuciones da la función de transferencia

$$
\boxed{G(s)=\frac{F_s(s)}{F(s)}=\frac{R\,C_s\,s+1-\alpha}{R\,(C_p+C_s)\,s+1}.}
$$

El **caso de atrofia** se representa únicamente incrementando $R$ de $100\,\Omega$ a $10\,\mathrm{k}\Omega$. Esto no modifica la ganancia en estado estacionario, pero vuelve la respuesta 100 veces más lenta: la constante de tiempo pasa de $11\,\mathrm{ms}$ a $1.1\,\mathrm{s}$.

Se estudian el error en estado estacionario y la estabilidad a través del polo de cada modelo. Posteriormente se aplica un pulso de entrada y se contrastan las implementaciones en MATLAB/Simulink (ecuaciones y función de transferencia), un modelo de EDO integrado en MATLAB y, de forma complementaria, Python. Se incorpora un **controlador PID** sobre el modelo del caso, cuya señal de error es la diferencia entre la respuesta del control y la del caso, y cuyas ganancias se sintonizan con la herramienta *Tune* de Simulink.

La práctica se desarrolla **exclusivamente mediante experimentación *in silico***. La estrategia de control tiene propósitos académicos y de simulación, y no constituye una estrategia terapéutica para el tratamiento de la atrofia muscular.

**Palabras clave:** sistema musculoesquelético; modelo viscoelástico; analogía fuerza–voltaje; superposición; función de transferencia; controlador PID; atrofia muscular; MATLAB; Simulink; Simscape Electrical; Python; simulación *in silico*.

[Volver al contenido](#contenido)

---

<a id="objetivos"></a>
## Competencia y objetivos

### Competencia a desarrollar

Modelizar sistemas fisiológicos mediante analogías entre sistemas físicos, ecuaciones diferenciales y funciones de transferencia para analizar su comportamiento dinámico, y diseñar estrategias de control mediante técnicas de control clásico. En particular, se analiza un modelo mecánico simplificado del sistema musculoesquelético, se construye su circuito eléctrico equivalente mediante la analogía fuerza–voltaje, se compara la respuesta de un individuo sano (control) con un caso simulado de atrofia muscular (caso) y se diseña una estrategia de control *in silico* orientada a reducir las diferencias entre ambas respuestas.

### Objetivo general

Analizar y controlar el modelo viscoelástico del sistema musculoesquelético mediante su formulación matemática, su implementación computacional y el diseño de un controlador PID, comparando las respuestas del control, del caso y del sistema controlado.

### Objetivos específicos

- Identificar los elementos del modelo mecánico $F_0(t)$, $F(t)$, $R$, $C_p$, $C_s$, $x(t)$ y $x_1(t)$, y describir su función.
- Convertir el modelo mecánico en un circuito eléctrico equivalente mediante la analogía fuerza–voltaje.
- Obtener la función de transferencia aplicando la transformada de Laplace y el principio de superposición.
- Expresar el modelo como una ecuación diferencial ordinaria de primer orden.
- Determinar el error en estado estacionario y analizar la estabilidad mediante el polo de cada modelo.
- Comparar la respuesta del control y del caso y analizar el efecto del incremento de $R$.
- Simular el modelo con ecuaciones, función de transferencia y componentes de Simscape Electrical.
- Implementar un controlador PID sobre el caso y sintonizar sus ganancias con *Tune*.
- Reproducir las respuestas de lazo abierto y lazo cerrado mediante MATLAB y Python.
- Elaborar un diagrama biológico que relacione los elementos del modelo con las estructuras musculares.
- Documentar la práctica en un repositorio de GitHub.

[Volver al contenido](#contenido)

---

<a id="temario"></a>
## Relación con el temario

La práctica integra contenidos correspondientes a tres unidades de la asignatura **Modelado de Sistemas Fisiológicos (IBF-1020)**:

| Unidad | Área | Contenidos aplicados |
|---|---|---|
| I | Sistemas de control retroalimentados | Elementos del sistema de control, diagramas de bloques, respuesta ante un pulso, análisis de error y criterios de estabilidad |
| II | Modelado matemático | Analogía fuerza–voltaje, superposición, transformada de Laplace y función de transferencia de primer orden |
| III | Controladores | Controlador PID, diseño y sintonización de ganancias |

El enfoque combina **Ingeniería de Control, Electrónica Analógica, Ciencias de la Computación y Fisiología**, de acuerdo con la competencia general de la asignatura.

[Volver al contenido](#contenido)

---

<a id="analogia"></a>
## Modelo físico y analogía eléctrica

El modelo mecánico (Figura 5.3 del syllabus, adaptado de Khoo [1]) representa las propiedades viscoelásticas del músculo mediante un elemento contráctil, un amortiguador y dos resortes. La deformación del amortiguador es $x(t)-x_1(t)$, por lo que su velocidad relativa es $\frac{d}{dt}[x(t)-x_1(t)]$. La fuerza del elemento contráctil se relaciona con la fuerza del modelo mediante

$$
F_0(t)=\alpha F(t),\qquad 0<\alpha<1.
$$

### Correspondencia entre el modelo mecánico, el circuito y la fisiología

| Elemento mecánico | Equivalente eléctrico | Interpretación fisiológica |
|---|---|---|
| $F(t)$ | Voltaje de entrada | Fuerza generada |
| $F_0(t)=\alpha F(t)$ | Fuente de voltaje del elemento contráctil | Elemento contráctil muscular |
| $R$ | Resistencia | Comportamiento viscoso del tejido |
| $C_s$ | Capacitancia (elemento en serie) | Tendón |
| $C_p$ | Capacitancia (elemento en paralelo) | Sarcolema y estructuras elásticas musculares pasivas |
| $x(t)$ | Carga / corriente en la rama de $C_p$ | Desplazamiento total |
| $x_1(t)$ | Carga / corriente en la rama de $C_s$ | Deformación del tendón |
| $F_s(t)$ | Voltaje de salida | Fuerza transmitida |

### Diagrama biológico

![Diagrama biológico del modelo musculoesquelético](diagrama_biologico.png)

*Figura elaborada en BioRender. A la derecha se muestra el modelo mecánico equivalente; a la izquierda se ubica cada elemento del modelo en el músculo y su tendón.*

Cada elemento del diagrama se relaciona con el modelo mecánico y con su equivalente eléctrico de la siguiente forma:

| Elemento del diagrama | Modelo mecánico | Equivalente eléctrico |
|---|---|---|
| Fuerza generada $F(t)$ | Fuerza de entrada del modelo | Fuente de voltaje $F(t)$ |
| Elemento contráctil muscular $F_0$ | Fuerza activa $F_0(t)=\alpha F(t)$ | Fuente de voltaje $\alpha F(t)$ |
| Elemento viscoso muscular $R$ | Amortiguador | Resistencia $R$ |
| Estructuras elásticas musculares $C_p$ | Resorte en paralelo | Capacitancia $C_p$ |
| Tendón $C_s$ | Resorte en serie | Capacitancia $C_s$ |
| Desplazamiento del músculo $x(t)-x_1(t)$ | Deformación del amortiguador | Carga asociada a $R$ |
| Desplazamiento del tendón $x_1(t)$ | Deformación de $C_s$ | Carga asociada a $C_s$ |
| Desplazamiento total $x(t)$ | Desplazamiento de $C_p$ | Carga asociada a $C_p$ |

El desplazamiento total se reparte entre el músculo y el tendón: $x(t)=\left[x(t)-x_1(t)\right]+x_1(t)$.

### Analogía fuerza–voltaje

| Sistema mecánico | Sistema eléctrico equivalente |
|---|---|
| Fuerza $F(t)$ | Voltaje $V(t)$ |
| Velocidad $\dot{x}(t)$ | Corriente $I(t)$ |
| Desplazamiento $x(t)$ | Carga eléctrica $q(t)$ |
| Amortiguador | Resistencia $R$ |
| Compliancia mecánica | Capacitancia $C$ |
| Masa | Inductancia $L$ (no presente en este modelo) |

Una consecuencia de esta analogía es que las conexiones mecánicas y eléctricas no conservan necesariamente la misma topología: componentes mecánicos en paralelo pueden convertirse en elementos eléctricos en serie, y componentes mecánicos en serie, en elementos eléctricos en paralelo.

> **Nota de notación:** en los cálculos por superposición de la siguiente sección, $x(t)$ designa la **corriente** (velocidad mecánica) que circula por la rama de $C_p$, de acuerdo con la relación $x(t)=C_p\,\dfrac{dF_{s1}(t)}{dt}$. Por eso aparece como corriente en las ecuaciones de malla y no como desplazamiento.

### Parámetros para la simulación

| Parámetro | Control (sano) | Caso (atrofia) | Unidad |
|---|---:|---:|---|
| $F(t)$ | $1$ | $1$ | V |
| $\alpha$ | $0.25$ | $0.25$ | — |
| $C_s$ | $10$ | $10$ | µF |
| $C_p$ | $100$ | $100$ | µF |
| $R$ | $100$ | $10\,000$ | Ω |

La **única** diferencia entre control y caso es el incremento de $R$, que modifica la componente viscosa del modelo y produce una respuesta mecánica diferente.

> **Alcance de la analogía:** los valores anteriores corresponden al circuito eléctrico equivalente empleado para la experimentación *in silico*. La correspondencia entre unidades eléctricas y mecánicas conserva la estructura matemática, pero no debe interpretarse como una conversión numérica directa de unidades sin especificar factores de escala.

[Volver al contenido](#contenido)

---

<a id="modelo"></a>
## Formulación matemática

El circuito tiene dos fuentes, $F(t)$ y $F_0(t)=\alpha F(t)$. Como el sistema es lineal, la salida se obtiene como suma de dos contribuciones:

$$
F_s(t)=F_{s1}(t)+F_{s2}(t),
$$

donde $F_{s1}$ es la respuesta con $F_0(t)$ apagada y $F_{s2}$ la respuesta con $F(t)$ apagada.

### 1. Circuito 1: apagando $F_0(t)$

La corriente total que carga a $C_p$ es la suma de la corriente de la rama de $C_s$ y la de la rama de $R$, ambas alimentadas por la diferencia $F(t)-F_{s1}(t)$:

$$
x(t)=x_1(t)+x_2(t),\qquad x(t)=C_p\frac{dF_{s1}(t)}{dt},
$$

$$
x_1(t)=C_s\frac{d\left[F(t)-F_{s1}(t)\right]}{dt},\qquad
x_2(t)=\frac{F(t)-F_{s1}(t)}{R}.
$$

Igualando:

$$
C_p\frac{dF_{s1}(t)}{dt}=C_s\frac{d\left[F(t)-F_{s1}(t)\right]}{dt}+\frac{F(t)-F_{s1}(t)}{R}.
$$

Aplicando la transformada de Laplace con condiciones iniciales nulas:

$$
C_p s F_{s1}(s)=C_s s\left[F(s)-F_{s1}(s)\right]+\frac{F(s)-F_{s1}(s)}{R}.
$$

Agrupando los términos en $F_{s1}(s)$ a la izquierda y los de $F(s)$ a la derecha:

$$
\left(C_p s+C_s s+\frac{1}{R}\right)F_{s1}(s)=\left(C_s s+\frac{1}{R}\right)F(s),
$$

$$
\frac{R(C_p+C_s)s+1}{R}\,F_{s1}(s)=\frac{C_sRs+1}{R}\,F(s).
$$

Despejando:

$$
\boxed{\frac{F_{s1}(s)}{F(s)}=\frac{R\,C_s\,s+1}{R\,(C_p+C_s)\,s+1}.}
$$

### 2. Circuito 2: apagando $F(t)$

Con $F(t)$ apagada solo actúa la fuente del elemento contráctil. Las capacitancias $C_s$ y $C_p$ quedan en paralelo (capacitancia equivalente $C_p+C_s$), en serie con $R$:

$$
-\alpha F(t)=R\,x(t)+\frac{1}{C_p+C_s}\int x(t)\,dt,\qquad
F_{s2}(t)=\frac{1}{C_p+C_s}\int x(t)\,dt.
$$

En el dominio de Laplace:

$$
-\alpha F(s)=R\,X(s)+\frac{X(s)}{(C_p+C_s)s},\qquad
F_{s2}(s)=\frac{X(s)}{(C_p+C_s)s}.
$$

De la primera ecuación, $-\alpha F(s)=X(s)\,\dfrac{R(C_p+C_s)s+1}{(C_p+C_s)s}$. Dividiendo la segunda ecuación entre la primera, $X(s)$ se cancela:

$$
\frac{F_{s2}(s)}{-\alpha F(s)}=\frac{\dfrac{X(s)}{(C_p+C_s)s}}{X(s)\,\dfrac{R(C_p+C_s)s+1}{(C_p+C_s)s}}=\frac{1}{R(C_p+C_s)s+1},
$$

$$
\boxed{\frac{F_{s2}(s)}{F(s)}=\frac{-\alpha}{R\,(C_p+C_s)\,s+1}.}
$$

### 3. Función de transferencia

Por superposición, $F_s(s)=F_{s1}(s)+F_{s2}(s)$:

$$
F_s(s)=\frac{(C_sRs+1)\,F(s)}{R(C_p+C_s)s+1}-\frac{\alpha\,F(s)}{R(C_p+C_s)s+1},
$$

$$
\boxed{G(s)=\frac{F_s(s)}{F(s)}=\frac{R\,C_s\,s+1-\alpha}{R\,(C_p+C_s)\,s+1}.}
$$

El denominador es de primer grado debido a que el modelo tiene **un único modo dinámico independiente**, asociado con la constante de tiempo $\tau=R(C_p+C_s)$.

### 4. Representación mediante EDO de primer orden

Separando $G(s)$ en una ganancia directa más un filtro de primer orden:

$$
G(s)=\frac{C_s}{C_p+C_s}+\frac{1-\alpha-\dfrac{C_s}{C_p+C_s}}{R(C_p+C_s)s+1}.
$$

Definiendo la variable auxiliar $x(t)$ como la salida del filtro de primer orden con entrada $F(t)$, es decir $X(s)=F(s)/[R(C_p+C_s)s+1]$, el modelo se expresa como

$$
\begin{aligned}
\dot{x}(t)&=\frac{F(t)-x(t)}{R\,(C_p+C_s)},\\[4pt]
F_s(t)&=\frac{C_s}{C_p+C_s}\,F(t)+\left(1-\alpha-\frac{C_s}{C_p+C_s}\right)x(t).
\end{aligned}
$$

Esta es la forma que se integra por el método de Euler en la función `musculoesqueletico` de la sección [Implementación](#matlab). La variable auxiliar $x(t)$ de este modelo de estado **no** es la $x(t)$ de los cálculos por superposición.

[Volver al contenido](#contenido)

---

<a id="analisis"></a>
## Análisis temporal y de estabilidad

### 1. Control y caso

Sustituyendo $\alpha=0.25$, $C_s=10\,\mu\mathrm{F}$ y $C_p=100\,\mu\mathrm{F}$:

| Característica | Control ($R=100\,\Omega$) | Caso ($R=10\,\mathrm{k}\Omega$) |
|---|---:|---:|
| $G(s)$ | $\dfrac{0.001\,s+0.75}{0.011\,s+1}$ | $\dfrac{0.1\,s+0.75}{1.1\,s+1}$ |
| Constante de tiempo $\tau=R(C_p+C_s)$ | $11\ \mathrm{ms}$ | $1.1\ \mathrm{s}$ |
| Polo | $-90.91\ \mathrm{s^{-1}}$ | $-0.909\ \mathrm{s^{-1}}$ |
| Cero | $-750\ \mathrm{s^{-1}}$ | $-7.5\ \mathrm{s^{-1}}$ |
| Ganancia en estado estacionario $G(0)=1-\alpha$ | $0.75$ | $0.75$ |
| Salto inicial $G(\infty)=\dfrac{C_s}{C_p+C_s}$ | $0.0909$ | $0.0909$ |
| Tiempo de establecimiento ($\approx 4\tau$) | $\approx 44\ \mathrm{ms}$ | $\approx 4.4\ \mathrm{s}$ |

### 2. Error en estado estacionario

Para una entrada escalón unitario,

$$
F(s)=\frac{1}{s},\qquad F_s(s)=G(s)\frac{1}{s}.
$$

Si se cumple la estabilidad requerida para aplicar el teorema del valor final,

$$
\lim_{t\to\infty}F_s(t)=\lim_{s\to0}sF_s(s)=G(0)=1-\alpha=0.75\,\mathrm{V}.
$$

Así, el error entre la entrada y la salida para **esa entrada escalón en lazo abierto** es

$$
\boxed{e_{ss}=\lim_{s\to0}sF(s)[1-G(s)]=\alpha=0.25.}
$$

$G(0)$ no depende de $R$: **el incremento de $R$ no modifica el valor final**, solo la rapidez con la que se alcanza. Control y caso tienen el mismo error en estado estacionario.

### 3. Polo y estabilidad

El polo del sistema satisface

$$
R(C_p+C_s)s+1=0,
$$

$$
\boxed{p=-\frac{1}{R\,(C_p+C_s)}.}
$$

Como $R$, $C_p$ y $C_s$ son positivos, el denominador tiene coeficientes positivos y el polo es real y negativo. Ambos modelos son **estables**. Al aumentar $R$, el polo se desplaza hacia el origen (de $-90.9$ a $-0.909$): la respuesta se vuelve más lenta, no inestable.

### 4. Predicción previa

Con los resultados anteriores se espera, ante un pulso de $1\,\mathrm{V}$ y $1\,\mathrm{s}$:

- un **salto inmediato** de $\approx 0.09\,\mathrm{V}$ al subir la entrada, porque el numerador y el denominador de $G(s)$ son del mismo grado;
- un crecimiento exponencial hacia $0.75\,\mathrm{V}$ con constante de tiempo $\tau$: el control alcanza esa meseta en unos $44\,\mathrm{ms}$, y el caso no la alcanza durante el pulso de $1\,\mathrm{s}$;
- una caída inmediata de $\approx 0.09\,\mathrm{V}$ al bajar la entrada, seguida de un decaimiento exponencial a cero con la misma $\tau$.

#### Ejemplo mínimo de verificación en MATLAB

```matlab
alpha = 0.25;
Cs = 10e-6;
Cp = 100e-6;

R = 100;
Gcontrol = tf([R*Cs, 1-alpha], [R*(Cp+Cs), 1]);
polo_control = pole(Gcontrol)

R = 10e3;
Gcaso = tf([R*Cs, 1-alpha], [R*(Cp+Cs), 1]);
polo_caso = pole(Gcaso)
```

Este código es ilustrativo; no sustituye al modelo de Simulink ni al cuaderno de la práctica.

[Volver al contenido](#contenido)

---

<a id="senales"></a>
## Señales de entrada y condiciones de simulación

El intervalo de simulación es $0\le t\le10\,\mathrm{s}$. Se establece un **paso máximo** de $\Delta t=10^{-3}\,\mathrm{s}$ y el solver `ode15s` en Simulink.

| Señal | Definición para la simulación | Propósito |
|---|---|---|
| Pulso | Amplitud $1\,\mathrm{V}$, duración $1\,\mathrm{s}$, inicio en $t=1\,\mathrm{s}$ (periodo $10\,\mathrm{s}$, ancho $10\,\%$, retardo $1\,\mathrm{s}$) | Examinar la respuesta ante una perturbación de duración finita |

La misma señal se aplica al control, al caso y al caso con controlador, con las mismas condiciones iniciales nulas, para que las respuestas sean comparables.

[Volver al contenido](#contenido)

---

<a id="matlab"></a>
## Implementación en MATLAB, Simulink y Simscape

Se reproducen tres representaciones equivalentes del sistema para las condiciones control y caso:

| Implementación | Descripción | Aspecto por verificar |
|---|---|---|
| Ecuaciones del sistema, Simulink | Bloques de suma, ganancias, derivación e integración | Consistencia con las ecuaciones de la superposición |
| Función de transferencia, Simulink | Bloque de planta con $G(s)=\dfrac{RC_ss+1-\alpha}{R(C_p+C_s)s+1}$ | Respuesta entrada–salida de primer orden |
| Circuito equivalente, Simscape Electrical | Componentes eléctricos $R$, $C_s$, $C_p$ y fuentes | Concordancia con las representaciones matemáticas |

### Ejecución desde el cuaderno MATLAB

El cuaderno computacional (`.mlx`) ejecuta el modelo de Simulink (`MPSFP3NFGA.slx`) y recupera en el *Workspace* las señales de:

1. **Entrada:** $F(t)$.
2. **Control:** $F_s(t)$ del individuo sano.
3. **Caso en lazo abierto:** $F_s(t)$ de la atrofia, sin controlador.
4. **Caso con controlador:** salida del sistema con PID.

#### Datos de la simulación

```matlab
clc; clear; close all; warning('off','all')
open_system('MPSFP3NFGA.slx')
parameters.StopTime = '10';
parameters.Solver = 'ode15s';
parameters.MaxStep ='1e-3';
```

#### Respuesta del modelo de Simulink

```matlab
X = sim('MPSFP3NFGA.slx',parameters);
plotsignals(X.t,X.F,X.Fsx,X.FsY,X.PID, 'Simulink')
```

#### Modelo de EDO

Se integra el modelo de la [sección 4 de la formulación matemática](#modelo) con el método de Euler y paso $\Delta t=10^{-3}\,\mathrm{s}$, para el control y para el caso, y se compara con la respuesta de Simulink. El vector `par` tiene el orden `[alpha, Cs, Cp, R]`.

```matlab
dt = 1e-3;
t = (0:dt:10)';
F = zeros(length(t),1); F(1/dt:2/dt) = 1;
PID = interp1(X.t, X.PID, t, 'previous');

par_control = [0.25,10e-6,100e-6,100];
par_caso = [0.25,10e-6,100e-6,10e3];

Fs_control = musculoesqueletico(par_control,F,t);
Fs_caso =  musculoesqueletico(par_caso,F,t);

plotsignals(t,F,Fs_control,Fs_caso,PID,'EDOsys')
```

```matlab
function Fs = musculoesqueletico(par,F,t)
    alpha = par(1); Cs = par(2); Cp = par(3); R = par (4); dt =  t(2);
n = round(t(end)/dt);
    x = zeros(length(t),1); x(1) = 0;
    Fs = zeros(length(t),1); Fs(1) = 0;
for i = 1:n
        x(i+1) = x(i) + ((F(i)-x(i))/(R*(Cp+Cs)))*dt;
        Fs(i+1) = Cs*F(i)/(Cp+Cs) + (1-alpha-Cs/(Cp+Cs))*x(i);
end
end
```

Las dos líneas dentro del ciclo `for` son la discretización de Euler de las ecuaciones de estado:

| Línea del código | Ecuación |
|---|---|
| `x(i+1) = x(i) + ((F(i)-x(i))/(R*(Cp+Cs)))*dt` | $\dot{x}=\dfrac{F-x}{R(C_p+C_s)}$ |
| `Fs(i+1) = Cs*F(i)/(Cp+Cs) + (1-alpha-Cs/(Cp+Cs))*x(i)` | $F_s=\dfrac{C_s}{C_p+C_s}F+\left(1-\alpha-\dfrac{C_s}{C_p+C_s}\right)x$ |

#### Función de graficación

```matlab
function plotsignals(t,F,Fsx,FsY,PID,name)
set(figure(),'color','w')
set(gcf,'units','inches','position',[1,1,7,3.5])
set(gca,'fontname','times new roman','fontsize',11)
hold on; grid off; box on;
ctrl1 = [0, 125, 204]/255;
ctrl2 = [235,185,0]/255;
ctrl3 = [209,0,86]/255;
ctrl4 = [80,0,113]/255;
order = [ctrl1;ctrl2;ctrl3;ctrl4]; colororder(order)

plot(t,F,'-','Linewidth',1.5,'Color',ctrl1)
plot(t,Fsx,'-','Linewidth',1.5,'Color',ctrl2)
plot(t,FsY,'-','Linewidth',1.5,'Color',ctrl3)
plot(t,PID,':','Linewidth',2.5,'Color',ctrl4)
L = legend('$F(t)$','$F_{s}(t):Control$','$F_{s}(t):Caso$','$PID: Transferencia');
set(L,'interpreter','latex','FontSize',9,'location','northoutside','orientation','horizontal','Box','Off')
xlabel('$t$ [s]','interpreter','latex')
ylabel('$F_i (t)$ [V]','interpreter','latex')
xlim([-0.25 10]); xticks(0:1:10)
ylim([-0.1,1.2]); yticks(-0.1:0.1:1.2)



exportgraphics(gcf,[name,'_matlab','.pdf'],'ContentType','vector')



end
```

La función grafica la entrada, el control, el caso y la respuesta con PID, identifica las variables y sus unidades, y guarda la figura en PDF vectorial (`Simulink_matlab.pdf` y `EDOsys_matlab.pdf`).

[Volver al contenido](#contenido)

---

<a id="pid"></a>
## Control en lazo cerrado y sintonización PID

### 1. Retroalimentación negativa

La respuesta del individuo sano se utiliza como **referencia** para el caso. Siguiendo el esquema de la Figura 5.4 del syllabus (individuo sano → comparación → PID → paciente), el controlador PID se incorpora al modelo del caso y actúa sobre la señal de error

$$
e(t)=x_{control}(t)-x_{caso}(t).
$$

La ley de control ideal en el dominio temporal está dada por

$$
u(t)=k_Pe(t)+k_I\int_0^t e(\tau)\,d\tau+k_D\frac{de(t)}{dt},
$$

y en el dominio de Laplace, formalmente,

$$
K(s)=k_P+\frac{k_I}{s}+k_Ds.
$$

Para el caso ideal de retroalimentación negativa unitaria, tomando como referencia la salida del control, la respuesta del caso con controlador es

$$
F_{s,caso}^{PID}(s)=\frac{K(s)G_{caso}(s)}{1+K(s)G_{caso}(s)}\,F_{s,control}(s).
$$

En la implementación mediante el bloque **PID Controller** deben revisarse la configuración y el filtrado de la acción derivativa.

### 2. Sintonización

Se utiliza la opción **Tune** del bloque `PID Controller` de Simulink (forma paralela, tiempo continuo) buscando una respuesta estable y una reducción del error entre la salida del caso y la respuesta de referencia del individuo sano. Las ganancias obtenidas son:

| Ganancia | Valor |
|---|---:|
| $k_P$ | $0.021475$ |
| $k_I$ | $42\,258.7709$ |
| $k_D$ | $0$ |
| $N$ (filtro derivativo) | $100$ |

Con $k_D=0$ el controlador sintonizado es un **PI**. Estos valores pueden variar con la versión del software y el procedimiento de sintonización; lo importante es conservar la evidencia de la respuesta y de los parámetros utilizados.

> **Pendiente:** agregar la captura de la herramienta *Tune* (`controlador_pid.png`) como evidencia de la sintonización.

[Volver al contenido](#contenido)

---

<a id="python"></a>
## Reproducción en Python

La implementación en Python, ejecutada mediante **Visual Studio Code**, permite comprobar los resultados de MATLAB/Simulink con una segunda herramienta computacional. Debe contener los siguientes apartados:

1. Definición del vector temporal ($0\le t\le10\,\mathrm{s}$, $\Delta t=10^{-3}\,\mathrm{s}$).
2. Construcción de la señal de entrada (pulso de $1\,\mathrm{V}$ y $1\,\mathrm{s}$).
3. Definición de las funciones de transferencia del control y del caso.
4. Cálculo de los polos de ambos modelos.
5. Definición del controlador con las ganancias $k_P$, $k_I$ y $k_D$ obtenidas con *Tune*.
6. Construcción del sistema del caso en lazo cerrado.
7. Simulación de la respuesta forzada.
8. Generación y almacenamiento de las figuras.

Las respuestas de Python se comparan con las de MATLAB/Simulink **bajo las mismas entradas, condiciones iniciales y parámetros**.

> **Pendiente:** agregar `musculoesqueletico.py` y las figuras generadas (`Python.pdf`, nombre sugerido).

[Volver al contenido](#contenido)

---

<a id="resultados"></a>
## Resultados y verificación cruzada

Las respuestas permiten contrastar **entrada, control, caso en lazo abierto y caso con controlador**. La convención gráfica de `plotsignals` es:

- **Azul:** entrada aplicada, $F(t)$.
- **Amarillo:** respuesta del control (individuo sano), $F_s(t)$.
- **Rojo/magenta:** respuesta del caso (atrofia), $F_s(t)$.
- **Morado punteado:** respuesta del sistema con PID.

### Respuesta en lazo abierto ante el pulso de 1 V y 1 s

Valores obtenidos de la función de transferencia $G(s)$ de cada condición con el pulso unitario que inicia en $t=1\,\mathrm{s}$:

| Instante | Control ($\tau=11\,\mathrm{ms}$) | Caso ($\tau=1.1\,\mathrm{s}$) |
|---|---:|---:|
| Salto inicial al subir el pulso | $\approx 0.09\ \mathrm{V}$ | $\approx 0.09\ \mathrm{V}$ |
| Valor al final del pulso ($t=2\,\mathrm{s}$) | $\approx 0.75\ \mathrm{V}$ (meseta alcanzada) | $\approx 0.48\ \mathrm{V}$ (sin alcanzar la meseta) |
| Valor inmediatamente después de bajar el pulso | $\approx 0.66\ \mathrm{V}$ | $\approx 0.39\ \mathrm{V}$ |
| Regreso a cero | $\approx 44\ \mathrm{ms}$ | $\approx 4.4\ \mathrm{s}$ |

### Archivos de resultados

| Representación | MATLAB/Simulink | Python |
|---|---|---|
| Modelo de Simulink (control, caso y PID) | [`Simulink_matlab.pdf`](Simulink_matlab.pdf) | — |
| Modelo de EDO (control, caso y PID de Simulink) | [`EDOsys_matlab.pdf`](EDOsys_matlab.pdf) | — |

Los enlaces anteriores funcionarán cuando los archivos estén incluidos en la raíz del repositorio; si se organizan por carpetas, deberán ajustarse las rutas relativas del README. Los PDF se enlazan para su consulta, dado que GitHub no los presenta como imágenes incrustadas convencionales.

### Aspectos para discutir

- Coincidencia entre las representaciones por ecuaciones, función de transferencia, circuito equivalente y EDO integrada en MATLAB.
- Efecto del incremento de $R$ sobre la velocidad de respuesta, con ganancia final constante.
- Efecto del controlador sobre la amplitud, la velocidad de respuesta, el amortiguamiento y la diferencia entre el caso y el control.
- Consistencia entre las figuras de MATLAB y Python.
- Limitaciones del modelo musculoesquelético simplificado.

### Interpretación de resultados

- **Efecto de $R$.** El caso de atrofia se modela solo aumentando $R$: el polo pasa de $-90.9$ a $-0.909$ y se conserva $G(0)=0.75$. El caso llega al mismo valor final que el control, pero 100 veces más lento. Ante un pulso de $1\,\mathrm{s}$, el control alcanza su meseta y el caso no.
- **Consistencia entre representaciones.** La función de transferencia, el modelo de Simulink y la integración por Euler describen la misma dinámica de primer orden. Con $\Delta t=1\,\mathrm{ms}$ el método de Euler es estable en ambos casos (el más exigente es el control, con $\Delta t/\tau\approx0.09$).
- **Salto instantáneo.** Como el numerador y el denominador de $G(s)$ son del mismo grado, la salida salta de forma inmediata a $C_s/(C_p+C_s)\approx0.09$ al subir o bajar la entrada.
- **Controlador.** Con $k_D=0$, el PID sintonizado se comporta como PI: la acción integral reduce el error entre el caso y la referencia sana.

> **Pendiente:** completar con la comparación cuantitativa del lazo cerrado (amplitud, velocidad, amortiguamiento y error residual entre $x_{caso}$ y $x_{control}$) y la verificación con Simscape y Python.

[Volver al contenido](#contenido)

---

<a id="flujo"></a>
## Flujo de trabajo

```mermaid
flowchart TD
    A[Modelo mecanico del musculo] --> B[Analogia fuerza-voltaje]
    B --> C[Superposicion: circuito 1 y circuito 2]
    C --> D[Funcion de transferencia]
    C --> E[EDO de primer orden]
    D --> F[Polo, estabilidad y error]
    E --> G[Simulink: ecuaciones]
    D --> H[Simulink: funcion de transferencia]
    B --> I[Simscape Electrical: circuito]
    G --> J[Lazo abierto: control y caso]
    H --> J
    I --> J
    J --> K[PID sobre el error control - caso]
    K --> L[Sintonizacion y respuesta en lazo cerrado]
    D --> M[Python: simulacion independiente]
    L --> M
    J --> N[Verificacion cruzada]
    L --> N
    M --> N
    B --> O[Diagrama biologico en BioRender]
    N --> P[README, figuras y evidencias en GitHub]
    O --> P
```

El procedimiento integra la formulación analítica y la experimentación *in silico* con una verificación cruzada entre herramientas.

[Volver al contenido](#contenido)

---

<a id="actividades"></a>
## Actividades del estudiante

La práctica sigue una secuencia de **modelado, simulación, control y documentación**, desarrollada exclusivamente *in silico*.

### Etapa 1. Formulación y análisis

- [x] Identificar los elementos $F_0(t)$, $F(t)$, $R$, $C_p$, $C_s$, $x(t)$ y $x_1(t)$.
- [x] Construir la analogía electromecánica fuerza–voltaje.
- [x] Formular las ecuaciones del sistema por superposición (circuitos 1 y 2).
- [x] Obtener la función de transferencia $F_s(s)/F(s)$ del control y del caso.
- [x] Expresar el modelo como EDO de primer orden.
- [x] Calcular el error en estado estacionario.
- [x] Calcular los polos y analizar la estabilidad.
- [ ] Representar los polos en el plano complejo.

### Etapa 2. Simulación del sistema

- [x] Reproducir el modelo por ecuaciones en Simulink.
- [x] Reproducir la función de transferencia en Simulink.
- [ ] Reproducir el circuito equivalente en Simscape Electrical.
- [x] Obtener la respuesta en lazo abierto del control y del caso.
- [ ] Verificar la consistencia entre las representaciones.

### Etapa 3. Diseño y comprobación del controlador

- [x] Construir el sistema de retroalimentación con PID sobre el caso.
- [x] Sintonizar y registrar $k_P$, $k_I$ y $k_D$.
- [ ] Analizar la respuesta en lazo cerrado.
- [x] Desarrollar el cuaderno de MATLAB y la función de graficación con exportación a PDF.
- [ ] Implementar el control y el caso en Python con las mismas ganancias.
- [ ] Realizar la verificación cruzada de los resultados.

### Etapa 4. Diagrama biológico y documentación

- [x] Elaborar el diagrama biológico en BioRender (bíceps femoral).
- [x] Relacionar cada elemento del diagrama con el modelo mecánico y su equivalente eléctrico.
- [ ] Incorporar figuras y capturas de las simulaciones.
- [ ] Redactar las conclusiones.
- [ ] Publicar los archivos y un `CITATION.cff` correctamente identificados.

[Volver al contenido](#contenido)

---

<a id="estructura"></a>
## Estructura del repositorio

Una organización **sugerida** para la entrega, compatible con los archivos solicitados, es:

```text
.
├── README.md                      # Reporte y guía de reproducción
├── CITATION.cff                   # Datos de citación del repositorio
├── Apellido_NoControl.mlx         # Cuaderno MATLAB del estudiante
├── Apellido_NoControl.pdf         # Exportación del cuaderno
├── MPSFP3NFGA.slx                 # Modelo Simulink
├── MPSFP3NFGA_simscape.slx        # Circuito equivalente en Simscape Electrical
├── musculoesqueletico.py          # Implementación Python
├── Simulink_matlab.pdf            # Figura del modelo de Simulink
├── EDOsys_matlab.pdf              # Figura del modelo de EDO
├── Python.pdf                     # Figura de Python
├── controlador_pid.png            # Captura de sintonización (nombre sugerido)
├── diagrama_biologico.png         # Diagrama de BioRender
├── apuntes/                       # Cálculos manuscritos de la superposición
└── evidencias/                    # Capturas adicionales de las simulaciones
```

**Criterio de nombres:** `MPSFP3NFGA.slx`, `Simulink_matlab.pdf` y `EDOsys_matlab.pdf` corresponden a los archivos y nombres que genera el código de la práctica. Los demás nombres son **propuestas organizativas**. Si se utilizan otros nombres o subcarpetas, actualizar las rutas del README y no eliminar evidencias requeridas.

[Volver al contenido](#contenido)

---

<a id="requisitos"></a>
## Requisitos

### Software

- **MATLAB** y **Simulink**, con el modelo `MPSFP3NFGA.slx` y el cuaderno de la práctica.
- **Simscape Electrical**, para construir el circuito equivalente.
- Herramientas de Simulink para la sintonización del bloque **PID Controller**.
- **Python** y **Visual Studio Code** para la implementación complementaria.
- **Git y GitHub** para organizar y presentar el repositorio de la práctica.

Para el ejemplo de verificación de la sección de análisis, que utiliza `tf` y `pole`, es necesario disponer de **Control System Toolbox**.

[Volver al contenido](#contenido)

---

<a id="reproducibilidad"></a>
## Reproducibilidad y entregables

### Secuencia de reproducción

1. Consultar el [repositorio general de la asignatura](https://github.com/DrPaulValle/MSF-IBF1020) y descargar los archivos proporcionados para la práctica.
2. Verificar la topología del circuito equivalente, los valores de $\alpha$, $R$, $C_s$, $C_p$ y las condiciones iniciales.
3. Derivar el modelo por superposición y completar el análisis del polo y del error en estado estacionario.
4. Ejecutar los modelos de Simulink y Simscape para el control y el caso.
5. Sintonizar el PID sobre el caso y registrar las ganancias y la evidencia de la sintonización.
6. Ejecutar el cuaderno MATLAB y exportar las figuras en PDF.
7. Sustituir las ganancias en Python, reproducir las señales y exportar las figuras.
8. Comparar los resultados de MATLAB, Simulink, Simscape y Python, y explicar las diferencias.
9. Elaborar el diagrama biológico y relacionarlo con el modelo mecánico y el circuito equivalente.
10. Completar este README con las evidencias propias.
11. Publicar el repositorio con sus fuentes y `CITATION.cff`.

### Resultados mínimos esperados

| Entregable | Evidencia solicitada |
|---|---|
| Análisis del modelo mecánico | Identificación y función de cada elemento |
| Analogía electromecánica | Circuito equivalente y correspondencias |
| Análisis matemático | Superposición, función de transferencia, EDO, polo y error |
| Simulaciones | Modelo `.slx`, circuito en Simscape y respuestas en lazo abierto y cerrado |
| Sintonización | Ganancias PID y captura de la sintonización |
| MATLAB | Cuaderno `.mlx`, exportación `.pdf` y figuras |
| Python | Código `.py` y figuras |
| Diagrama biológico | Figura de BioRender con elementos relacionados al modelo |
| Documentación | `README.md`, `CITATION.cff` e interpretación de los resultados |

### Criterios de presentación académica

En el README final debe distinguirse entre **resultados de referencia reproducidos**, **resultados propios** y **modificaciones realizadas por el estudiante**. La ejecución de scripts y la generación de figuras, por sí solas, no sustituyen la discusión del comportamiento mecánico, matemático y eléctrico del sistema.

[Volver al contenido](#contenido)

---

<a id="referencias"></a>
## Referencias

[1] M. C. Khoo, *Physiological Control Systems: Analysis, Simulation, and Estimation*, 2nd ed., IEEE Press, 2018.

[2] F. C. Hoppensteadt y C. S. Peskin, *Modeling and Simulation in Medicine and the Life Sciences*, Springer.

[3] J. E. Hall, *Guyton and Hall Textbook of Medical Physiology*, 14th ed., Elsevier, 2021.

[4] K. Ogata, *Ingeniería de Control Moderna*, 5.ª ed., Pearson Educación, 2010.

[5] N. S. Nise, *Control Systems Engineering*, 8th ed., John Wiley & Sons, 2020.

[6] P. A. Valle, *Syllabus para Modelado de Sistemas Fisiológicos*, Tecnológico Nacional de México / Instituto Tecnológico de Tijuana, Tijuana, B. C., México, 2026. Disponible en: https://biomath.xyz/course/

[7] Tecnológico Nacional de México, *Programa de estudio de la asignatura Modelado de Sistemas Fisiológicos, IBF-1020*.

---

## Uso académico

Este repositorio se utiliza como evidencia de la práctica **Modelado y control del sistema musculoesquelético** de la asignatura Modelado de Sistemas Fisiológicos. El procedimiento se fundamenta en los materiales proporcionados por la asignatura. Su finalidad es desarrollar habilidades de modelado, simulación, control y documentación técnica; la estrategia de control tiene propósitos exclusivamente académicos y de simulación, y cualquier cambio de parámetros, estructura del controlador o procedimiento debe identificarse y justificarse.
