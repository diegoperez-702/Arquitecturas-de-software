# Fase 1 — Exploración del dominio y atributos de calidad

## 1. Objetivo

En esta primera fase se busca identificar los atributos de calidad que realmente van a condicionar la arquitectura del sistema.

El enunciado ya da tres condiciones importantes: la aplicación mobile debe responder en un máximo de 200 ms, la disponibilidad esperada es de 99.9 % y la solución debe ser capaz de soportar hasta 10.000 usuarios concurrentes.

A partir de estos puntos se analizarán también otros aspectos que, aunque no aparecen de forma explícita en el reto, son necesarios para que la solución pueda operar de forma estable, mantenerse en el tiempo y responder adecuadamente ante fallos.

También es importante separar lo que realmente está definido en el ejercicio de aquellas cosas que todavía no conocemos y que, en un proyecto real, deberían ser validadas con negocio, desarrollo y operaciones.

---

## 2. Contexto general

La solución está pensada para ser consumida desde aplicaciones web y mobile. Esto implica que la arquitectura debe soportar diferentes canales sin depender directamente de la tecnología usada por cada uno.

Los principales interesados identificados son:

### Equipo de desarrollo

Necesita una solución que sea fácil de mantener y evolucionar, con servicios desacoplados y contratos claros entre componentes.

También es importante que los cambios realizados en un servicio tengan el menor impacto posible sobre otros componentes.

### Equipo de operaciones

Necesita visibilidad sobre lo que ocurre en producción.

Para esto se requiere contar con información suficiente sobre errores, tiempos de respuesta, consumo de recursos y comportamiento de los servicios.

Además, la arquitectura debería facilitar la recuperación ante fallos y la ampliación de capacidad cuando la demanda aumente.

### Stakeholders de negocio

Su principal interés es que la plataforma esté disponible, responda de forma adecuada y pueda crecer sin que cada incremento de usuarios implique un rediseño completo de la solución.

---

# 3. Requisitos identificados en el enunciado

Los tres requisitos principales del reto son:

| Requisito | Objetivo |
|---|---|
| Latencia | Máximo 200 ms en aplicaciones mobile |
| Disponibilidad | SLA de 99.9 % |
| Escalabilidad | Soportar 10.000 usuarios concurrentes |

Estos tres puntos serán los principales drivers de arquitectura y deberán estar relacionados directamente con las decisiones que se tomen en la siguiente fase.

---

# 4. Atributos de calidad

## 4.1 Rendimiento y latencia

El primer atributo relevante es el rendimiento, especialmente porque el reto establece una latencia máxima de 200 ms para aplicaciones mobile.

Este requisito afecta directamente la forma en que se comunican los componentes. Si una operación necesita pasar por demasiados servicios antes de responder, es muy probable que el tiempo total aumente.

Por esta razón, durante el diseño será necesario revisar aspectos como:

- cantidad de llamadas síncronas;
- acceso a bases de datos;
- uso de caché;
- dependencias externas;
- procesamiento asíncrono;
- tiempos de espera entre servicios.

En general, las operaciones que no requieran una respuesta inmediata deberían evaluarse para ser procesadas de forma asíncrona.

---

## 4.2 Disponibilidad

El sistema debe alcanzar una disponibilidad de 99.9 %.

Esto significa que la arquitectura debe estar preparada para continuar funcionando incluso cuando alguno de sus componentes falle.

No significa que ningún servicio pueda fallar, sino que el impacto de ese fallo debe ser controlado.

Algunas decisiones que deberán considerarse son:

- redundancia de componentes;
- balanceo de carga;
- despliegue de varias instancias;
- health checks;
- failover;
- replicación;
- recuperación automática.

También será importante identificar qué componentes realmente son críticos y cuáles podrían degradarse temporalmente sin provocar la caída completa del sistema.

---

## 4.3 Escalabilidad

El reto establece que la solución debe soportar 10.000 usuarios concurrentes.

Para poder lograrlo, la arquitectura debe permitir aumentar capacidad sin realizar cambios estructurales importantes.

Una de las estrategias más importantes será evitar, cuando sea posible, mantener estado local dentro de los servicios. Esto facilita levantar nuevas instancias y distribuir la carga.

También será necesario considerar:

- escalamiento horizontal;
- auto-scaling;
- balanceo de tráfico;
- desacoplamiento de procesos;
- uso de colas;
- caching;
- distribución adecuada de carga.

Sin embargo, 10.000 usuarios concurrentes no necesariamente significa 10.000 transacciones por segundo. Este es uno de los puntos que deberían ser aclarados antes de realizar pruebas de capacidad.

---

# 5. Otros atributos necesarios

Aunque no aparecen directamente en el enunciado, hay otros atributos que son importantes porque ayudan a cumplir los tres requisitos principales.

## 5.1 Resiliencia

En una arquitectura distribuida es normal que algún componente falle.

Puede fallar una base de datos, un servicio externo, una conexión de red o incluso un servicio propio.

La arquitectura debe ser capaz de limitar el impacto de estos fallos.

Para esto se pueden considerar mecanismos como:

- timeouts;
- retries controlados;
- circuit breaker;
- bulkhead;
- dead letter queues;
- mecanismos de fallback cuando sean válidos para negocio.

La idea principal es evitar que el fallo de un componente genere una caída en cascada.

---

## 5.2 Observabilidad

No es posible garantizar rendimiento o disponibilidad si no existe una forma de medirlos.

Por esta razón, la solución debe permitir conocer qué está ocurriendo dentro del sistema.

Como mínimo deberían existir:

- métricas;
- logs estructurados;
- trazabilidad distribuida;
- identificadores de correlación;
- alertas;
- dashboards operativos.

Esto permitiría identificar, por ejemplo, si una degradación de latencia está ocurriendo en el API Gateway, en un servicio, en una base de datos o en una dependencia externa.

---

## 5.3 Seguridad

Aunque el ejercicio no establece requisitos específicos de seguridad, cualquier solución expuesta a aplicaciones web y mobile debe contemplar controles básicos.

Entre ellos:

- autenticación;
- autorización;
- cifrado de información en tránsito;
- manejo seguro de credenciales;
- auditoría;
- control de acceso.

No se propone todavía una tecnología específica porque el mecanismo de seguridad debería definirse una vez se conozcan los requisitos del negocio y las restricciones reales del proyecto.

---

## 5.4 Mantenibilidad

La solución debe poder evolucionar sin que cualquier cambio implique modificar múltiples componentes.

Para esto será importante mantener una separación clara de responsabilidades y reducir el acoplamiento.

Algunas características deseables son:

- contratos bien definidos;
- versionamiento;
- modularidad;
- pruebas automatizadas;
- independencia entre consumidores y proveedores.

---

## 5.5 Interoperabilidad

Este punto es especialmente relevante dentro de un enfoque SOA.

Los diferentes servicios deberían poder comunicarse utilizando contratos y estándares conocidos, sin depender de la implementación interna de cada componente.

Esto facilita tanto la integración entre sistemas existentes como futuras evoluciones tecnológicas.

---

# 6. Escenarios de calidad

Para evitar que los atributos queden únicamente como conceptos generales, es útil expresarlos mediante escenarios concretos.

## Escenario 1 — Latencia

Un usuario realiza una operación desde la aplicación mobile.

El sistema debe procesar la solicitud y entregar una respuesta en un tiempo no mayor a 200 ms.

Para efectos de medición, sería recomendable definir este objetivo utilizando percentiles, por ejemplo:

**p95 ≤ 200 ms**

Esto sería más representativo que medir únicamente el promedio.

---

## Escenario 2 — Disponibilidad

Una instancia de un servicio deja de responder.

El sistema debería continuar atendiendo solicitudes utilizando otra instancia disponible, evitando una interrupción total del servicio.

El objetivo general sería mantener el SLA de 99.9 %.

---

## Escenario 3 — Escalabilidad

El número de usuarios conectados aumenta progresivamente hasta llegar a 10.000 usuarios concurrentes.

La plataforma debe incrementar su capacidad sin requerir una intervención manual y sin afectar significativamente los tiempos de respuesta.

---

## Escenario 4 — Fallo de una dependencia

Un servicio externo presenta alta latencia o deja de responder.

El servicio consumidor debe detectar la situación mediante timeouts y mecanismos de resiliencia, evitando que el problema afecte al resto de la solución.

---

## Escenario 5 — Diagnóstico de problemas

El equipo de operaciones detecta un aumento en los tiempos de respuesta.

La plataforma debe permitir seguir la solicitud entre los distintos componentes para identificar dónde se está produciendo la degradación.

---

# 7. Restricciones conocidas

A partir del enunciado se pueden identificar algunas restricciones claras.

La solución debe soportar aplicaciones web y mobile.

Además, por el contexto del ejercicio, debe plantearse desde una perspectiva SOA, por lo cual es importante mantener separación entre consumidores y proveedores, contratos de servicio bien definidos e interoperabilidad entre componentes.

Finalmente, cualquier decisión de arquitectura deberá explicar cómo contribuye al menos a uno de los tres objetivos principales:

- latencia;
- disponibilidad;
- escalabilidad.

---

# 8. Ambigüedades que deberían aclararse

El enunciado define los objetivos principales, pero no brinda suficiente información para tomar todas las decisiones de arquitectura.

En un escenario real sería necesario aclarar varios puntos.

## Rendimiento

No se especifica:

- qué operaciones deben cumplir exactamente los 200 ms;
- si el tiempo se mide desde el dispositivo del usuario o únicamente dentro del backend;
- cuál es el percentil esperado;
- cuántas transacciones por segundo se esperan.

## Disponibilidad

No se indican valores de:

- RTO;
- RPO;
- recuperación ante desastres;
- operación multi-región.

## Escalabilidad

No se conoce:

- patrón esperado de crecimiento;
- duración de los picos de carga;
- comportamiento de los usuarios concurrentes;
- distribución geográfica de los usuarios.

## Seguridad

No se define:

- modelo de autenticación;
- modelo de autorización;
- clasificación de información;
- regulación aplicable.

## Integraciones

Tampoco se indica:

- cuántos sistemas externos existen;
- qué tecnologías utilizan;
- cuáles son sus SLA;
- cómo debe comportarse la plataforma cuando alguno de ellos no esté disponible.

---

# 9. Supuestos iniciales

Para poder avanzar hacia la siguiente fase será necesario trabajar con algunos supuestos.

Estos supuestos deberán validarse más adelante y no deben considerarse requisitos definitivos.

Se asume inicialmente que:

- los servicios serán consumidos mediante APIs sobre HTTPS;
- la solución tendrá componentes distribuidos;
- algunos procesos podrán ejecutarse de forma asíncrona;
- pueden existir dependencias externas con tiempos de respuesta variables;
- los servicios deberán permitir escalamiento horizontal;
- se requiere trazabilidad entre componentes.

---

# 10. Priorización

La prioridad principal está dada directamente por el ejercicio.

| Atributo | Prioridad |
|---|---|
| Latencia | Crítica |
| Disponibilidad | Crítica |
| Escalabilidad | Crítica |
| Resiliencia | Alta |
| Observabilidad | Alta |
| Seguridad | Alta |
| Mantenibilidad | Media |
| Interoperabilidad | Media |

La resiliencia y la observabilidad se consideran de alta prioridad porque son necesarias para poder cumplir y verificar los atributos principales.

---

# 11. Trade-offs identificados

Las decisiones arquitectónicas no mejoran todos los atributos al mismo tiempo.

Existen algunos compromisos que deberán evaluarse.

Por ejemplo:

- aumentar la redundancia mejora disponibilidad, pero incrementa costos;
- agregar controles de seguridad puede aumentar ligeramente la latencia;
- usar retries mejora resiliencia ante fallos temporales, pero puede aumentar los tiempos de respuesta;
- dividir demasiado una solución en servicios puede mejorar independencia, pero aumentar la complejidad operacional.

Estos trade-offs deberán ser tenidos en cuenta al momento de definir la arquitectura.

---

# 12. Drivers arquitectónicos

A partir del análisis realizado, se consideran como principales drivers:

1. Mantener una latencia máxima de 200 ms para las operaciones mobile que se definan como críticas.
2. Garantizar una disponibilidad mínima de 99.9 %.
3. Soportar hasta 10.000 usuarios concurrentes.
4. Permitir escalamiento horizontal.
5. Evitar fallos en cascada.
6. Contar con observabilidad de extremo a extremo.
7. Reducir el acoplamiento entre servicios.
8. Facilitar la evolución de la solución.

---

# 13. Conclusión

Antes de seleccionar tecnologías, la arquitectura debe partir de los atributos de calidad que realmente condicionan el diseño.

En este caso, los tres principales son rendimiento, disponibilidad y escalabilidad.

A partir de ellos también aparecen otras necesidades como resiliencia, observabilidad, seguridad e interoperabilidad.

La siguiente fase debería enfocarse en proponer una arquitectura que responda a estos atributos y explicar, para cada decisión importante, qué problema resuelve, qué beneficio aporta y qué compromiso introduce.