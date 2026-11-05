# Práctica Tres — Modelado y control del sistema musculoesquelético

Repositorio académico de la asignatura **Modelado de Sistemas Fisiológicos (IBF-1020)**, correspondiente al programa de **Ingeniería Biomédica** del Tecnológico Nacional de México / Instituto Tecnológico de Tijuana.

En esta práctica se analiza un **modelo mecánico simplificado del músculo** (elemento contráctil, elemento viscoso, tendón y estructuras elásticas), se convierte a un **circuito eléctrico equivalente** mediante la analogía fuerza–voltaje, se obtiene su función de transferencia por **superposición**, y se compara la respuesta de un individuo sano (**control**) contra un caso simulado de **atrofia muscular** (**caso**). Finalmente se diseña, de forma exclusivamente *in silico*, un controlador PID que reduce la diferencia entre ambas respuestas.

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
2. [Modelo mecánico](#mecanico)
3. [Analogía electromecánica y parámetros](#analogia)
4. [Desarrollo matemático](#modelo)
   - [Circuito 1: apagando F₀(t)](#circuito1)
   - [Circuito 2: apagando F(t)](#circuito2)
   - [Función de transferencia total](#tf)
   - [Representación mediante EDO de primer orden](#edo)
5. [Control vs. caso: análisis del modelo](#analisis)
6. [Implementación en MATLAB y Simulink](#matlab)
7. [Control en lazo cerrado con PID](#pid)
8. [Resultados](#resultados)
9. [Discusión y conclusiones](#discusion)
10. [Actividades del estudiante](#actividades)
11. [Estructura del repositorio](#estructura)
12. [Requisitos](#requisitos)
13. [Referencias](#referencias)

---

<a id="resumen"></a>
## Resumen

El sistema musculoesquelético se representa con un modelo viscoelástico de tres elementos pasivos (un amortiguador viscoso $R$, un elemento elástico en serie $C_s$ y un elemento elástico en paralelo $C_p$) más una fuente de fuerza activa $F_0(t)=\alpha F(t)$ que representa al elemento contráctil. Mediante la analogía fuerza–voltaje se obtiene un circuito RC equivalente, que se resuelve por **superposición**: primero con la fuente $F_0(t)$ apagada y después con $F(t)$ apagada. La suma de ambas contribuciones da la función de transferencia

$$
\boxed{\frac{F_s(s)}{F(s)}=\frac{R\,C_s\,s+1-\alpha}{R\,(C_p+C_s)\,s+1}}
$$

El caso de atrofia se representa **únicamente** aumentando $R$ de $100\,\Omega$ a $10\,\mathrm{k}\Omega$. Esto no modifica la ganancia en estado estacionario, pero vuelve la respuesta 100 veces más lenta: la constante de tiempo pasa de $11\,\mathrm{ms}$ a $1.1\,\mathrm{s}$.

**Palabras clave:** sistema musculoesquelético; modelo viscoelástico; analogía fuerza–voltaje; superposición; función de transferencia; controlador PID; MATLAB; Simulink; atrofia muscular; simulación *in silico*.

> **Alcance:** la estrategia de control de esta práctica tiene fines exclusivamente académicos y de simulación; no constituye una estrategia terapéutica para el tratamiento de la atrofia muscular.

[Volver al contenido](#contenido)

---

<a id="mecanico"></a>
## Modelo mecánico

El modelo mecánico (Figura 5.3 del syllabus, adaptado de Khoo [1]) se compone de los siguientes elementos:

| Elemento | Significado en el modelo | Estructura fisiológica asociada |
|---|---|---|
| $F_0(t)$ | Fuerza del elemento contráctil activo, $F_0(t)=\alpha F(t)$ con $0<\alpha<1$ | Elemento contráctil muscular (sarcómeros) |
| $F(t)$ | Fuerza de entrada del modelo | Fuerza generada |
| $R$ | Amortiguamiento viscoso del tejido | Comportamiento viscoso del tejido muscular |
| $C_s$ | Compliancia del elemento elástico **en serie** | Tendón |
| $C_p$ | Compliancia del elemento elástico **en paralelo** | Sarcolema y estructuras musculares pasivas |
| $x(t)$ | Desplazamiento del elemento paralelo $C_p$ | Desplazamiento total |
| $x_1(t)$ | Desplazamiento del elemento en serie $C_s$ | Deformación del tendón |

La deformación del amortiguador es $x(t)-x_1(t)$, por lo que su velocidad relativa es $\frac{d}{dt}[x(t)-x_1(t)]$.

[Volver al contenido](#contenido)

---

<a id="analogia"></a>
## Analogía electromecánica y parámetros

Se utiliza la analogía **fuerza–voltaje**:

| Sistema mecánico | Sistema eléctrico equivalente |
|---|---|
| Fuerza $F(t)$ | Voltaje $V(t)$ |
| Velocidad $\dot{x}(t)$ | Corriente $I(t)$ |
| Desplazamiento $x(t)$ | Carga $q(t)$ |
| Amortiguador | Resistencia $R$ |
| Compliancia mecánica | Capacitancia $C$ |
| Masa | Inductancia $L$ (no presente en este modelo) |

> **Nota de notación:** en los cálculos manuscritos de la siguiente sección, $x(t)$ designa la **corriente** (velocidad mecánica) que circula por la rama de $C_p$, de acuerdo con la relación $x(t)=C_p\,\dfrac{dF_{s1}(t)}{dt}$; por eso aparece como corriente en las ecuaciones de malla y no como desplazamiento.

### Parámetros de simulación

| Parámetro | Control (sano) | Caso (atrofia) | Unidad |
|---|---:|---:|---|
| $F(t)$ | $1$ | $1$ | V |
| $\alpha$ | $0.25$ | $0.25$ | — |
| $C_s$ | $10$ | $10$ | µF |
| $C_p$ | $100$ | $100$ | µF |
| $R$ | $100$ | $10\,000$ | Ω |

La **única** diferencia entre control y caso es el incremento de $R$.

[Volver al contenido](#contenido)

---

<a id="modelo"></a>
## Desarrollo matemático

El circuito tiene dos fuentes, $F(t)$ y $F_0(t)=\alpha F(t)$. Como el sistema es lineal, la salida $F_s(t)$ se obtiene como la suma de dos contribuciones:

$$
F_s(t)=F_{s1}(t)+F_{s2}(t),
$$

donde $F_{s1}$ es la respuesta con $F_0(t)$ apagada y $F_{s2}$ la respuesta con $F(t)$ apagada.

<a id="circuito1"></a>
### Circuito 1: apagando $F_0(t)$

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
C_p s F_{s1}(s)=C_s s\left[F(s)-F_{s1}(s)\right]+\frac{F(s)-F_{s1}(s)}{R}
$$

Agrupando los términos en $F_{s1}(s)$ a la izquierda y los de $F(s)$ a la derecha:

$$
\left(C_p s+C_s s+\frac{1}{R}\right)F_{s1}(s)=\left(C_s s+\frac{1}{R}\right)F(s)
$$

$$
\frac{R(C_p+C_s)s+1}{R}\,F_{s1}(s)=\frac{C_sRs+1}{R}\,F(s)
$$

Despejando:

$$
\boxed{\frac{F_{s1}(s)}{F(s)}=\frac{R\,C_s\,s+1}{R\,(C_p+C_s)\,s+1}}
$$

<a id="circuito2"></a>
### Circuito 2: apagando $F(t)$

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

De la primera ecuación, $-\alpha F(s)=X(s)\,\dfrac{R(C_p+C_s)s+1}{(C_p+C_s)s}$. Dividiendo la segunda entre la primera, $X(s)$ se cancela:

$$
\frac{F_{s2}(s)}{-\alpha F(s)}=\frac{\dfrac{X(s)}{(C_p+C_s)s}}{X(s)\,\dfrac{R(C_p+C_s)s+1}{(C_p+C_s)s}}=\frac{1}{R(C_p+C_s)s+1}
$$

$$
\boxed{\frac{F_{s2}(s)}{F(s)}=\frac{-\alpha}{R\,(C_p+C_s)\,s+1}}
$$

<a id="tf"></a>
### Función de transferencia total

Por superposición, $F_s(s)=F_{s1}(s)+F_{s2}(s)$:

$$
F_s(s)=\frac{(C_sRs+1)\,F(s)}{R(C_p+C_s)s+1}-\frac{\alpha\,F(s)}{R(C_p+C_s)s+1}
$$

$$
\boxed{G(s)=\frac{F_s(s)}{F(s)}=\frac{R\,C_s\,s+1-\alpha}{R\,(C_p+C_s)\,s+1}}
$$

<a id="edo"></a>
### Representación mediante EDO de primer orden

Sea $\tau=R(C_p+C_s)$. Separando $G(s)$ en una ganancia directa más un filtro de primer orden:

$$
G(s)=\frac{C_s}{C_p+C_s}+\frac{1-\alpha-\dfrac{C_s}{C_p+C_s}}{\tau s+1}.
$$

Definiendo la variable auxiliar $x(t)$ como la salida del filtro de primer orden con entrada $F(t)$, es decir $X(s)=F(s)/(\tau s+1)$, el modelo queda:

$$
\begin{aligned}
\dot{x}(t)&=\frac{F(t)-x(t)}{R\,(C_p+C_s)},\\[4pt]
F_s(t)&=\frac{C_s}{C_p+C_s}\,F(t)+\left(1-\alpha-\frac{C_s}{C_p+C_s}\right)x(t).
\end{aligned}
$$

Esta es exactamente la forma que se integra por el método de Euler en la función `musculoesqueletico` de la sección [Implementación](#matlab). La variable auxiliar $x(t)$ de este modelo de estado **no** es la misma $x(t)$ de los cálculos por superposición.

[Volver al contenido](#contenido)

---

<a id="analisis"></a>
## Control vs. caso: análisis del modelo

Sustituyendo $\alpha=0.25$, $C_s=10\,\mu\mathrm{F}$, $C_p=100\,\mu\mathrm{F}$:

| Característica | Control ($R=100\,\Omega$) | Caso ($R=10\,\mathrm{k}\Omega$) |
|---|---:|---:|
| $G(s)$ | $\dfrac{0.001\,s+0.75}{0.011\,s+1}$ | $\dfrac{0.1\,s+0.75}{1.1\,s+1}$ |
| Constante de tiempo $\tau=R(C_p+C_s)$ | $11\ \mathrm{ms}$ | $1.1\ \mathrm{s}$ |
| Polo | $-90.91\ \mathrm{s^{-1}}$ | $-0.909\ \mathrm{s^{-1}}$ |
| Cero | $-750\ \mathrm{s^{-1}}$ | $-7.5\ \mathrm{s^{-1}}$ |
| Ganancia en estado estacionario $G(0)=1-\alpha$ | $0.75$ | $0.75$ |
| Salto inicial $G(\infty)=\dfrac{C_s}{C_p+C_s}$ | $0.0909$ | $0.0909$ |
| Tiempo de establecimiento (≈ $4\tau$) | $\approx 44\ \mathrm{ms}$ | $\approx 4.4\ \mathrm{s}$ |

### Error en estado estacionario

Por el teorema del valor final, para una entrada escalón $F(s)=1/s$:

$$
\lim_{t\to\infty}F_s(t)=\lim_{s\to0}sG(s)\frac{1}{s}=G(0)=1-\alpha=0.75\ \mathrm{V}
$$

$$
e_{ss}=1-G(0)=\alpha=0.25
$$

$G(0)$ no depende de $R$: **el incremento de $R$ no modifica el valor final**, solo la rapidez con la que se alcanza. Control y caso tienen el mismo error estacionario.

### Estabilidad

El denominador $R(C_p+C_s)s+1$ es de primer grado y con coeficientes positivos, por lo que cada modelo tiene un único polo real negativo, $p=-1/[R(C_p+C_s)]$. Ambos modelos son **estables**. Al aumentar $R$ el polo se desplaza hacia el origen (de $-90.9$ a $-0.909$): la respuesta se vuelve más lenta, no inestable.

### Interpretación del caso de atrofia

Con la misma fuerza de entrada y el mismo valor final, el caso responde 100 veces más lento. Esa lentitud es la diferencia que el controlador PID debe reducir.

[Volver al contenido](#contenido)

---

<a id="matlab"></a>
## Implementación en MATLAB y Simulink

El modelo se implementó en Simulink (`MPSFP3NFGA.slx`) y se controla desde un cuaderno de MATLAB (`.mlx`), que ejecuta la simulación, recupera las señales en el *Workspace* y las grafica con la función `plotsignals`.

### Datos de la simulación

```matlab
clc; clear; close all; warning('off','all')
open_system('MPSFP3NFGA.slx')
parameters.StopTime = '10';
parameters.Solver = 'ode15s';
parameters.MaxStep ='1e-3';
```

### Respuesta del modelo de Simulink

```matlab
X = sim('MPSFP3NFGA.slx',parameters);
plotsignals(X.t,X.F,X.Fsx,X.FsY,X.PID, 'Simulink')
```

### Modelo de EDO

La entrada es un pulso de $1\,\mathrm{V}$ y $1\,\mathrm{s}$ de duración que comienza en $t=1\,\mathrm{s}$. Se integra el modelo de la sección [EDO de primer orden](#edo) con el método de Euler y paso $\Delta t=10^{-3}\,\mathrm{s}$, para el control y para el caso, y se compara con la respuesta de Simulink.

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

El vector `par` tiene el orden `[alpha, Cs, Cp, R]`.

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

Las dos líneas dentro del ciclo `for` son la discretización de Euler de las ecuaciones de la sección [EDO de primer orden](#edo):

| Línea del código | Ecuación |
|---|---|
| `x(i+1) = x(i) + ((F(i)-x(i))/(R*(Cp+Cs)))*dt` | $\dot{x}=\dfrac{F-x}{R(C_p+C_s)}$ |
| `Fs(i+1) = Cs*F(i)/(Cp+Cs) + (1-alpha-Cs/(Cp+Cs))*x(i)` | $F_s=\dfrac{C_s}{C_p+C_s}F+\left(1-\alpha-\dfrac{C_s}{C_p+C_s}\right)x$ |

### Función de graficación

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

La función grafica $F(t)$, $F_s(t)$ del control, $F_s(t)$ del caso y la respuesta del sistema con PID, y guarda la figura en PDF vectorial (`Simulink_matlab.pdf` y `EDOsys_matlab.pdf`).

[Volver al contenido](#contenido)

---

<a id="pid"></a>
## Control en lazo cerrado con PID

La respuesta del individuo sano se usa como **referencia** para el caso. El PID se coloca en el modelo del caso y actúa sobre la señal de error

$$
e(t)=x_{control}(t)-x_{caso}(t),
$$

con el esquema de la Figura 5.4 del syllabus (individuo sano → comparación → PID → paciente/tratamiento). La ley de control ideal es

$$
u(t)=k_Pe(t)+k_I\int_0^t e(\tau)\,d\tau+k_D\frac{de(t)}{dt},\qquad K(s)=k_P+\frac{k_I}{s}+k_Ds.
$$

### Ganancias sintonizadas

Obtenidas con la herramienta **Tune** del bloque `PID Controller` de Simulink (forma paralela, tiempo continuo):

| Ganancia | Valor |
|---|---:|
| $k_P$ | $0.021475$ |
| $k_I$ | $42\,258.7709$ |
| $k_D$ | $0$ |
| $N$ (filtro derivativo) | $100$ |

Con $k_D=0$ el controlador sintonizado es un **PI**.

[Volver al contenido](#contenido)

---

<a id="resultados"></a>
## Resultados

### Lazo abierto: control vs. caso ante un pulso de 1 V y 1 s

Valores obtenidos de la función de transferencia $G(s)$ de cada condición con un pulso unitario de 1 s que inicia en $t=1\,\mathrm{s}$:

| Instante | Control ($\tau=11\,\mathrm{ms}$) | Caso ($\tau=1.1\,\mathrm{s}$) |
|---|---:|---:|
| Salto inicial al subir el pulso | $\approx 0.09\ \mathrm{V}$ | $\approx 0.09\ \mathrm{V}$ |
| Valor al final del pulso ($t=2\,\mathrm{s}$) | $\approx 0.75\ \mathrm{V}$ (meseta alcanzada) | $\approx 0.48\ \mathrm{V}$ (sin alcanzar la meseta) |
| Valor inmediatamente después de bajar el pulso | $\approx 0.66\ \mathrm{V}$ | $\approx 0.39\ \mathrm{V}$ |
| Regreso a cero | $\approx 44\ \mathrm{ms}$ | $\approx 4.4\ \mathrm{s}$ |

### Figuras

| Figura | Archivo |
|---|---|
| Respuesta del modelo de Simulink (control, caso y PID) | [`Simulink_matlab.pdf`](Simulink_matlab.pdf) |
| Respuesta del modelo de EDO (control, caso y PID de Simulink) | [`EDOsys_matlab.pdf`](EDOsys_matlab.pdf) |

> **Pendiente de agregar:** capturas de Simulink, evidencia de la sintonización del PID, figuras de Python y el diagrama biológico de BioRender.

[Volver al contenido](#contenido)

---

<a id="discusion"></a>
## Discusión y conclusiones

- **Superposición.** Resolver el circuito con una fuente apagada a la vez simplifica el álgebra: cada subcircuito se reduce a una ecuación de primer orden y la función de transferencia total es la suma de ambas.
- **Efecto de $R$.** El caso de atrofia se modela solo aumentando $R$. Eso mueve el polo de $-90.9$ a $-0.909$ y conserva la ganancia $G(0)=0.75$: el músculo del caso llega al mismo valor final, pero mucho más lento. Ante un pulso de 1 s, el control alcanza su meseta y el caso no.
- **Consistencia entre representaciones.** La función de transferencia, el modelo de Simulink y la integración de EDO por Euler describen la misma dinámica de primer orden (Euler es estable aquí, pues $\Delta t/\tau=0.09$ para el control, el caso más exigente).
- **Salto instantáneo.** Como el numerador y el denominador de $G(s)$ son del mismo grado, la salida salta de forma inmediata a $C_s/(C_p+C_s)\approx 0.09$ al subir o bajar la entrada; esto es lo que se observa como escalón en la gráfica.
- **Controlador.** Con $k_D=0$, el PID sintonizado se comporta como PI: la acción integral reduce el error entre el caso y la referencia sana.

> **Pendiente:** completar con la comparación cuantitativa del lazo cerrado (amplitud, velocidad, amortiguamiento y error residual entre $x_{caso}$ y $x_{control}$) y la verificación cruzada con Python y Simscape.

[Volver al contenido](#contenido)

---

<a id="actividades"></a>
## Actividades del estudiante

### Etapa 1. Análisis y formulación

- [x] Identificar los elementos del modelo mecánico.
- [x] Construir la analogía electromecánica fuerza–voltaje.
- [x] Obtener las ecuaciones por superposición (circuitos 1 y 2).
- [x] Obtener la función de transferencia $F_s(s)/F(s)$.
- [x] Obtener las funciones del control y del caso.
- [x] Calcular el error en estado estacionario.
- [x] Analizar la estabilidad (polos).

### Etapa 2. Simulación

- [x] Modelo en Simulink (`MPSFP3NFGA.slx`).
- [x] Cuaderno de MATLAB con función de graficación y exportación a PDF.
- [x] Modelo de EDO en MATLAB.
- [ ] Circuito equivalente en Simscape Electrical.
- [ ] Verificación cruzada de las representaciones.

### Etapa 3. Control

- [x] Sintonización del PID con *Tune*.
- [ ] Análisis completo de la respuesta en lazo cerrado.

### Etapa 4. Python, diagrama y documentación

- [ ] Implementación en Python y comparación con MATLAB/Simulink.
- [ ] Diagrama biológico en BioRender.
- [ ] `CITATION.cff`.

[Volver al contenido](#contenido)

---

<a id="estructura"></a>
## Estructura del repositorio

Organización **sugerida**; ajustar los nombres de archivos que no estén especificados:

```text
.
├── README.md                    # Reporte de la práctica
├── CITATION.cff                 # Datos de citación
├── MPSFP3NFGA.slx               # Modelo Simulink
├── cuaderno.mlx                 # Cuaderno MATLAB (nombre por definir)
├── cuaderno.pdf                 # Exportación del cuaderno
├── Simulink_matlab.pdf          # Figura del modelo de Simulink
├── EDOsys_matlab.pdf            # Figura del modelo de EDO
├── apuntes/                     # Cálculos manuscritos (superposición)
├── python/                      # Implementación en Python
├── biorender/                   # Diagrama biológico
└── evidencias/                  # Capturas de Simulink, Simscape y Tune
```

[Volver al contenido](#contenido)

---

<a id="requisitos"></a>
## Requisitos

- **MATLAB** y **Simulink** (solver `ode15s`).
- **Simscape Electrical** para el circuito equivalente.
- **Python** y **Visual Studio Code** para la implementación complementaria.
- **Git y GitHub** para documentar la práctica.

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

Este repositorio se utiliza como evidencia de la práctica **Modelado y control del sistema musculoesquelético** de la asignatura Modelado de Sistemas Fisiológicos. La estrategia de control tiene propósitos exclusivamente académicos y de simulación.
