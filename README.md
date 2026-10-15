# Arquitectura del arrendamiento de yates de terceros

La empresa arrienda yates de terceros que están en distintas marinas. Debe saber cuáles se pueden ofrecer, gestionar las reservas y coordinar lo que pasa entre la entrega y la devolución. También se ocupa de los pagos, el seguimiento del yate durante el arriendo y las calificaciones entre clientes y propietarios. Como las embarcaciones no son suyas, necesita dejar constancia de su estado y de lo ocurrido en cada alquiler.

Aquí reunimos la entrevista de la fase 0, las vistas del negocio de la fase 1, la descomposición de servicios de la fase 2, el análisis de dominios de la fase 3, el diseño táctico de la fase 4, el mapa de contextos de la fase 5 y los Canvas de la fase 6. Las vistas salen de un mismo [modelo ArchiMate](modelo/arrendamiento_yates.archimate). La IA respondió preguntas sobre el negocio; el equipo decidió cómo representarlo.

## Índice

- [1. Contexto y entrevista (fase 0)](#1-contexto-y-entrevista-fase-0)
- [2. Motivación: V1 (fase 1)](#2-motivacion-v1-fase-1)
- [3. Mapa de capacidades: V2 (fase 1)](#3-mapa-de-capacidades-v2-fase-1)
- [4. Cadena de valor y proceso E2E: V3 (fase 1)](#4-cadena-de-valor-y-proceso-e2e-v3-fase-1)
- [5. Descomposición de servicios: V4 (fase 2)](#5-descomposicion-de-servicios-v4-fase-2)
- [6. Servicios y proceso E2E: V4b (fase 2)](#6-servicios-y-proceso-e2e-v4b-fase-2)
- [7. Funciones y roles: V4c (fase 2)](#7-funciones-y-roles-v4c-fase-2)
- [8. Contratos de servicio: V4d (fase 2)](#8-contratos-de-servicio-v4d-fase-2)
- [9. Inclusión y exclusión de servicios](#9-inclusion-y-exclusion-de-servicios)
- [10. Interoperabilidad de negocio: V9b (fase 2)](#10-interoperabilidad-de-negocio-v9b-fase-2)
- [11. Análisis del ecosistema (fase 3)](#11-analisis-del-ecosistema-fase-3)
- [12. DDD estratégico: dominios y clasificación](#12-ddd-estrategico-dominios-y-clasificacion)
- [13. Dominios: V5 (fase 3)](#13-dominios-v5-fase-3)
- [14. Lenguaje ubicuo: V7 (fase 3)](#14-lenguaje-ubicuo-v7-fase-3)
- [15. Diseño táctico: agregados, comandos y eventos (fase 4)](#15-diseno-tactico-agregados-comandos-y-eventos-fase-4)
- [16. Mapa de contextos delimitados (fase 5)](#16-mapa-de-contextos-delimitados-fase-5)
- [17. Trazabilidad vertical: V10](#17-trazabilidad-vertical-v10)
  - [V10a. Gestión del arriendo](#171-v10a-gestion-del-arriendo)
  - [V10b. Evaluación de riesgo](#172-v10b-evaluacion-de-riesgo)
  - [V10c. Operación y seguimiento](#173-v10c-operacion-y-seguimiento-del-yate)
  - [V10d. Liquidación](#174-v10d-liquidacion)
  - [V10e. Reputación](#175-v10e-reputacion)
- [18. Bounded Context Canvas: V11 (fase 6)](#18-bounded-context-canvas-v11-fase-6)
  - [V11a. Gestión del arriendo](#181-v11a-gestion-del-arriendo-el-contexto-principal)
  - [V11b. Evaluación de riesgo](#182-v11b-evaluacion-de-riesgo)
  - [V11c. Operación y seguimiento](#183-v11c-operacion-y-seguimiento-del-yate)
  - [V11d. Liquidación](#184-v11d-liquidacion)
  - [V11e. Reputación](#185-v11e-reputacion)
- [19. Modelo y evidencias](#19-modelo-y-evidencias)
- [20. Aportes del equipo](#20-aportes-del-equipo)

## 1. Contexto y entrevista (fase 0)

La solicitud pide gestionar el arriendo desde que el cliente consulta un yate hasta que lo devuelve. El propietario decide si lo habilita o bloquea, y la empresa coordina con él y con la marina. El trabajo también abarca alquiler, depósito, seguro, combustible, ubicación durante el arriendo y calificaciones mutuas.

Preparamos un [guion de 28 preguntas](Entrevista/01_guion_entrevista.md) sobre el recorrido del arriendo, las personas que intervienen, las reglas, los riesgos y lo que ocurre cuando algo falla. También preguntamos por los indicadores y las restricciones del negocio. Un bloque se dedica a la información que se intercambia con el CRM, el área de contratos, las aseguradoras, las marinas, los proveedores de riesgo, la pasarela de pagos y las fuentes de ubicación y combustible.

La [instrucción de rol](Entrevista/02_instruccion_rol_ia.md) pidió a la IA que respondiera como responsable de operaciones de una empresa simulada y se limitara a explicar el negocio. Guardamos la [conversación completa](Entrevista/03_conversacion_experto.md) y revisamos sus respuestas en el [análisis de hallazgos](Entrevista/04_analisis_hallazgos.md). Allí distinguimos lo que ya decía la solicitud de los supuestos y ejemplos que aparecieron en la entrevista.

Esa revisión nos ayudó a separar momentos que pueden confundirse. Una solicitud aún no es una reserva confirmada; una reserva confirmada no garantiza que el yate pueda salir; y recibirlo de vuelta no significa que estén resueltos los cobros y reclamaciones. Para discutir daños o combustible también hace falta comparar su estado al salir y al regresar. Son distinciones útiles para el modelo, aunque las condiciones contractuales de una empresa real podrían variar.

No usamos como reglas los precios y porcentajes de los ejemplos, la escala ilustrativa de riesgo ni la ubicación en Cartagena. El análisis de hallazgos también distingue lo dicho por la IA sobre trámites marítimos, datos personales, seguros y responsabilidades de lo que puede darse por comprobado.

## 2. Motivación: V1 (fase 1)

![V1. Motivación del arrendamiento de yates](vistas/Fase%201/V01-motivacion.png)

### Análisis y decisiones

La vista comienza con cinco interesados: cliente, propietario, marina, responsable de operaciones y dirección general. El cliente quiere recibir el servicio acordado sin correr riesgos innecesarios; el propietario quiere conservar el control de su yate; la marina necesita organizar la salida y el regreso. Operaciones se preocupa por la seguridad de la entrega, mientras la dirección busca aprovechar las fechas disponibles y obtener ingresos. Cada preocupación aparece vinculada a un impulsor del negocio.

En el centro aparecen problemas que surgieron en la entrevista. Dos clientes podrían pedir el mismo yate en fechas que se cruzan; el propietario podría retirarlo después de confirmada una reserva; o una falla podría impedir la salida. También se contempla que el cliente no esté habilitado para conducir, que falten lecturas de ubicación o combustible y que un daño detectado al regreso no baste para culparlo. Estas evaluaciones explican por qué se fijaron las metas. No describen incidentes comprobados de una empresa real.

Se definieron cuatro metas: aprovechar la flota y cumplir las reservas, entregar los yates de forma segura, coordinar la entrega y la devolución, y cerrar cada arriendo con pagos y responsabilidades resueltos. Los resultados bajan esas metas a situaciones observables. No tener dos reservas confirmadas para fechas incompatibles, por ejemplo, contribuye a cumplir los compromisos. Las actas y las verificaciones antes de la salida ayudan a cuidar un bien ajeno. La tasa de ocupación figura sin una cifra objetivo porque la entrevista no proporcionó una.

Abajo están los principios y requisitos. El modelo separa la reserva confirmada de la entrega autorizada, da prioridad a la seguridad de las personas y exige evidencia antes de atribuir un cobro. De ahí salen necesidades como consultar yates por marina, avisar cambios de reserva, verificar condiciones de entrega, registrar inspecciones y mostrar cuándo se midieron la ubicación y el combustible. Los contratos, las pólizas, los medios de pago y las normas aparecen como restricciones cuyo contenido depende de la operación concreta.

Usamos los elementos de Motivación ArchiMate para que se pueda seguir el razonamiento, no solo ver una lista de necesidades. El riesgo de entregar un yate ajeno, por ejemplo, se vincula con la meta de entrega segura, con el resultado de verificar las condiciones antes de salir y con los requisitos de revisar al cliente y dejar constancia de la inspección. El [análisis de la entrevista](Entrevista/04_analisis_hallazgos.md) indica de dónde salieron esas ideas y cuáles siguen siendo supuestos.

## 3. Mapa de capacidades: V2 (fase 1)

![V2. Mapa de capacidades del negocio](vistas/Fase%201/V02-capacidades.png)

### Análisis y decisiones

El mapa reúne 28 capacidades en tres grupos. Las estratégicas incluyen dirección, alianzas, riesgo y cumplimiento. Las misionales cubren la relación con propietarios y clientes, las reservas y la operación de los arriendos. Las de apoyo abarcan, entre otras, contratos, pagos, seguros, contabilidad, mantenimiento y gestión jurídica. Mostrar el conjunto ayuda a ubicar la aplicación dentro de la empresa.

Las capacidades naranjas son las que el modelo marca como impactadas. En el grupo misional están el inventario de yates, las reservas, la evaluación del riesgo del arriendo, la coordinación de entrega y devolución, el monitoreo, la liquidación y la reputación. En el grupo de apoyo se destacan contratos, pagos y seguros. Las capacidades amarillas completan el mapa de la empresa, aunque no se marquen como impactadas directamente.

También aparecen dos cursos de acción: digitalizar el ciclo de arriendo y controlar el riesgo con evidencia y apoyo de terceros especializados. Las relaciones muestran qué capacidades contribuyen a esos cursos de acción y qué resultados se buscan, como evitar reservas incompatibles o resolver reclamaciones con fundamento. La flota de terceros y la red de marinas se representan como recursos que la empresa utiliza para prestar el servicio, aun cuando no sea dueña de los yates.

Elegimos capacidades porque describen qué hace la empresa sin decidir todavía qué área o sistema ejecutará cada tarea. Por eso el inventario, la coordinación de entrega y los seguros aparecen separados. Los cursos de acción y los recursos dan contexto a esa elección, mientras las relaciones con los resultados de V1 explican para qué se quiere intervenir. Marcar una capacidad como impactada no significa que la aplicación vaya a encargarse de todo lo que esa capacidad comprende.

## 4. Cadena de valor y proceso E2E: V3 (fase 1)

![V3. Cadena de valor y proceso de arriendo de extremo a extremo](vistas/Fase%201/V03-cadena-valor-e2e.png)

### Análisis y decisiones

Arriba se ve la cadena de valor: captar yates de terceros, atraer clientes, reservar y garantizar, preparar y entregar, acompañar la navegación, y devolver y cerrar. La captación de yates y clientes explica cómo llegan la oferta y la demanda. Debajo se desarrolla el arriendo, desde la solicitud del cliente hasta el evento «Arriendo cerrado».

El recorrido empieza con la consulta de yates y la solicitud de reserva. Después vienen la revisión de requisitos y riesgo, el acuerdo, el anticipo y la confirmación que se comunica al propietario y a la marina. Antes de salir se gestionan los pagos y la garantía, se inspecciona el yate y operaciones autoriza la entrega. Durante el uso se consulta su ubicación y combustible. Al regreso se recibe e inspecciona, se ajustan los cobros y se cierra el arriendo. Los roles junto a cada actividad indican quién participa en ese tramo.

No todas las solicitudes siguen el camino principal. La vista muestra el rechazo tras la evaluación y la posibilidad de buscar una alternativa o cancelar si la entrega no se autoriza. También incluye una autorización individual del propietario, que solo sería necesaria cuando así se haya acordado con él. «Yate devuelto» y «Arriendo cerrado» son eventos diferentes: después de recibir la embarcación aún pueden quedar la inspección y los ajustes económicos. Las calificaciones se abren tras la devolución.

Usamos flujos de valor de Estrategia para situar el arriendo en la actividad de la empresa, y elementos de Negocio para mostrar qué pasa y quién interviene. Las relaciones con las capacidades explican por qué reservas y riesgo se ubican antes de confirmar, pagos y seguros intervienen antes de entregar y el monitoreo ocurre durante la navegación. Separar devolución y cierre conserva una distinción que apareció en la entrevista y afecta tanto el trabajo operativo como los cobros.

## 5. Descomposición de servicios: V4 (fase 2)

![V4. Descomposición de servicios de negocio](vistas/Fase%202/V04-descomposicion-servicios.png)

El servicio **SP-00 Gestión del arriendo de yates de terceros** representa el recorrido completo descrito en V3. Debajo hay cuatro servicios de etapa: reserva y garantía, preparación y entrega, acompañamiento de la navegación, y devolución y cierre. Esta división conserva dos diferencias del negocio: reservar no es entregar, y recibir el yate no es cerrar el arriendo.

La tercera capa baja a tareas más concretas, como consultar disponibilidad, evaluar al cliente, registrar inspecciones, autorizar la entrega o liquidar cargos. La cuarta reúne información compartida por varias tareas: yates, clientes, propietarios, expedientes y marinas. La quinta agrupa servicios transversales, como notificaciones, evidencias, control de acceso y auditoría. La sexta reúne los intercambios con otros procesos y participantes. Son seis capas de **servicios de negocio** en el modelo; aquí «capa» indica el nivel de descomposición, no una tecnología.

Los servicios en gris se identificaron para entender el proceso, pero quedaron fuera de lo que se implementaría en la aplicación. Por ejemplo, la atención de una emergencia sigue siendo una actividad del negocio aunque no se construya un servicio para ejecutarla. En la [tabla de inclusión y exclusión](#9-inclusion-y-exclusion-de-servicios) se explica la decisión para cada servicio. La vista reúne muchas relaciones; las siguientes acercan el proceso, los responsables y los contratos para poder leerlos por separado.

## 6. Servicios y proceso E2E: V4b (fase 2)

![V4b. Relación entre servicios de tarea y proceso E2E](vistas/Fase%202/V04b-servicios-proceso.png)

Aquí se ve qué servicio apoya cada paso del recorrido. **ST-01** sirve a la consulta de yates; **ST-02** atiende la solicitud y el apartado temporal; **ST-03** reúne la evaluación del cliente y del riesgo. Más adelante, **ST-08** conserva la evidencia de la inspección, **ST-09** apoya la decisión de entrega y **ST-12** permite consultar ubicación y combustible. Al regreso, **ST-14** calcula los ajustes y **ST-16** recoge las calificaciones.

No hay una correspondencia rígida de un servicio por cada caja del proceso. Cobros y garantías (**ST-06**) se necesitan tanto para confirmar como antes de entregar y al cerrar; el registro de inspecciones (**ST-08**) sirve en la entrega y la devolución. El servicio de cancelaciones (**ST-10**) atiende el recorrido alternativo. Las relaciones con cliente, propietario y marina muestran a quién se presta cada actividad, sin dar por hecho que todos actúan en todos los arriendos.

El proceso de negocio conserva el paso de liquidar al propietario y calcular la comisión. En el alcance elegido, el sistema comunica los importes a contabilidad mediante **SA-09**; el pago al propietario como servicio propio (**ST-15**) no se incluye. Así distinguimos una actividad que la empresa debe coordinar de un servicio que la aplicación implementaría.

## 7. Funciones y roles: V4c (fase 2)

![V4c. Funciones de negocio y roles](vistas/Fase%202/V04c-funciones-roles.png)

Esta vista agrupa las tareas por el trabajo que realizan. Gestión de reservas reúne consulta, solicitudes, autorización del propietario, formalización, confirmación y cancelaciones. Evaluación de riesgo, operación de entrega y devolución, monitoreo, finanzas y reputación se muestran como funciones separadas. También figuran la información maestra, los servicios transversales y la interoperabilidad, porque varias tareas dependen de ellos.

Los roles se vinculan con las funciones que ejercen: el coordinador de reservas y el asesor comercial intervienen en reservas; el analista de riesgo en la evaluación; operaciones, el inspector y el operario de marina en la entrega y recepción; el analista financiero en los cobros y la liquidación. En la entrevista no quedó fijado que todas las marinas hagan inspecciones: por eso el rol de inspector puede corresponder a la marina o a la empresa según el acuerdo. La vista separa **quién hace el trabajo** de **qué servicio se ofrece**.

## 8. Contratos de servicio: V4d (fase 2)

![V4d. Contratos y objetos de negocio asociados a los servicios](vistas/Fase%202/V04d-contratos.png)

Cada uno de los **39 servicios incluidos** tiene un elemento *Contract* en el modelo ArchiMate. La vista lo coloca junto al servicio y los objetos de negocio que intervienen: la consulta usa la ficha del yate, el calendario y la marina; la evaluación considera la solicitud y la ficha del cliente; la inspección trabaja con actas y evidencias; la liquidación, con las actas y la garantía.

El diagrama permite ubicar esos contratos, mientras que su contenido está en la documentación del [archivo fuente](modelo/arrendamiento_yates.archimate). Allí se describen propósito, operaciones, entradas, salidas, errores, reglas y condiciones de cada servicio. Por ejemplo, el contrato de **ST-02** distingue una solicitud apartada de una reserva confirmada y contempla liberar el apartado; el de **ST-12** devuelve la hora de la medición junto con la última ubicación y lectura de combustible. Este enfoque *contract first* permite discutir qué ofrece cada servicio y qué necesita recibir antes de decidir cómo construirlo.

## 9. Inclusión y exclusión de servicios

La decisión se refiere a los servicios que la aplicación apoyaría o conectaría. «Excluido» no significa que esa actividad deje de existir en el negocio. La siguiente tabla recoge los **50 servicios identificados** en V4: 39 incluidos y 11 excluidos. Las siglas distinguen proceso (SP), etapa (SE), tarea (ST), entidad (SN), utilitarios (SU) e interoperabilidad (SA).

| Capa | Servicio | Decisión | Motivo |
| --- | --- | --- | --- |
| Proceso | SP-00 Gestión del arriendo | Incluido | Da continuidad al arriendo desde la solicitud hasta el cierre. |
| Etapa | SE-01 Reserva y garantía | Incluido | Coordina las verificaciones necesarias antes de confirmar una reserva. |
| Etapa | SE-02 Preparación y entrega | Incluido | Separa la confirmación de la autorización para entregar el yate. |
| Etapa | SE-03 Acompañamiento de la navegación | Incluido | Reúne seguimiento y atención de alertas durante el uso. |
| Etapa | SE-04 Devolución y cierre | Incluido | Distingue la recepción física del cierre económico. |
| Tarea | ST-01 Consulta por marina | Incluido | El cliente necesita ver yates disponibles cerca de una marina. |
| Tarea | ST-02 Solicitudes de reserva | Incluido | Gestiona la solicitud y evita apartados incompatibles sobre un mismo yate. |
| Tarea | ST-03 Elegibilidad y riesgo | Incluido | Permite revisar los requisitos del cliente antes de ofrecer un bien ajeno. |
| Tarea | ST-04 Autorización del propietario | Incluido | Respeta los acuerdos que exijan su aprobación individual; no aplica a todos los casos. |
| Tarea | ST-05 Formalización contractual | Incluido | Conserva las condiciones aceptadas para cada arriendo. |
| Tarea | ST-06 Cobros y garantías | Incluido | Atiende alquiler, seguro, depósito y devoluciones en distintas etapas. |
| Tarea | ST-07 Confirmación y avisos | Incluido | Confirma la reserva y la comunica al propietario y a la marina. |
| Tarea | ST-08 Inspecciones y actas | Incluido | Deja evidencia comparable entre entrega y devolución. |
| Tarea | ST-09 Autorización de entrega | Incluido | Registra la decisión operativa tras comprobar las condiciones de salida. |
| Tarea | ST-10 Cancelaciones y alternativas | Incluido | Registra la causa y la respuesta cuando una reserva no puede cumplirse. |
| Tarea | ST-11 Coordinación con la marina | Incluido | Comunica la reserva y recibe datos de preparación, entrega y recepción. |
| Tarea | ST-12 Ubicación y combustible | Incluido | Pone a disposición del cliente y la marina la última lectura conocida. |
| Tarea | ST-13 Alertas de navegación | Incluido | Permite revisar pérdida de señal y otras situaciones anómalas. |
| Tarea | ST-14 Liquidación del arriendo | Incluido | Calcula combustible, daños sustentados y saldo de la garantía. |
| Tarea | ST-15 Liquidación y pago al propietario | Excluido | El pago al propietario corresponde a contabilidad; el arriendo le comunica los importes por SA-09. |
| Tarea | ST-16 Calificaciones mutuas | Incluido | Recoge la valoración del cliente y del propietario después de la devolución. |
| Tarea | ST-17 Reclamaciones de cobro | Incluido | Permite registrar y resolver cargos discutidos con evidencia. |
| Tarea | ST-18 Captación de propietarios y yates | Excluido | La captación pertenece al tramo anterior a la reserva; SN-01 guarda los datos necesarios del yate. |
| Tarea | ST-19 Atracción y fidelización de clientes | Excluido | Es trabajo comercial que se atiende mediante el CRM; SA-01 intercambia sus datos con el arriendo. |
| Tarea | ST-20 Tramitación de siniestros | Excluido | La aseguradora decide la cobertura; el arriendo puede comunicar el caso y sus evidencias por SA-04. |
| Tarea | ST-21 Atención de emergencias marítimas | Excluido | La respuesta corresponde a personas y servicios de rescate; el arriendo aporta alertas y la última posición conocida. |
| Entidad | SN-01 Yates y disponibilidad | Incluido | Comparte datos del yate, marina, habilitación y calendario entre varias tareas. |
| Entidad | SN-02 Clientes | Incluido | Permite verificar identidad y habilitaciones sin sustituir la gestión comercial del CRM. |
| Entidad | SN-03 Propietarios | Incluido | Identifica a quién avisar y qué modalidad de autorización se pactó. |
| Entidad | SN-04 Expediente del arriendo | Incluido | Conserva estado e historial del arriendo para las distintas tareas. |
| Entidad | SN-05 Marinas | Incluido | Aporta ubicación, horarios y servicios disponibles para consultar y coordinar. |
| Utilitario | SU-01 Notificaciones | Incluido | Varios pasos necesitan avisar a cliente, propietario o marina. |
| Utilitario | SU-02 Documentos y evidencias | Incluido | Actas, acuerdos y soportes de cobro deben poder consultarse en más de una tarea. |
| Utilitario | SU-03 Identidad y acceso | Incluido | Los participantes tienen permisos diferentes sobre arriendos y ubicación. |
| Utilitario | SU-04 Cálculo de cercanía | Incluido | Apoya la consulta de yates por la marina más cercana. |
| Utilitario | SU-05 Auditoría y trazabilidad | Incluido | Permite reconstruir autorizaciones, cambios y cobros ante una reclamación. |
| Interoperabilidad | SA-01 CRM comercial | Incluido | Intercambia datos del cliente y estados del arriendo sin duplicar el proceso comercial. |
| Interoperabilidad | SA-02 Gestión de contratos | Incluido | Obtiene condiciones vigentes y comunica acuerdos o cambios que requieren aceptación. |
| Interoperabilidad | SA-03 Evaluador de riesgo | Incluido | Permite solicitar una evaluación externa cuando corresponda. |
| Interoperabilidad | SA-04 Aseguradora | Incluido | Consulta cobertura y comunica siniestros con su evidencia. |
| Interoperabilidad | SA-05 Marinas | Incluido | Recibe novedades y coordina con marinas que pueden trabajar de forma distinta. |
| Interoperabilidad | SA-06 Pasarela de pagos | Incluido | Intercambia cobros, garantías, reembolsos y sus resultados. |
| Interoperabilidad | SA-07 Ubicación y combustible | Incluido | Recibe o consulta lecturas de distintas fuentes durante el arriendo. |
| Interoperabilidad | SA-08 Autoridad marítima | Excluido | Los trámites se atienden por los canales de la autoridad; su jurisdicción determina las condiciones aplicables. |
| Interoperabilidad | SA-09 Contabilidad | Incluido | Comunica pagos y valores calculados para que contabilidad registre y concilie. |
| Interoperabilidad | SA-10 Mantenimiento y combustible | Excluido | La gestión de estos proveedores depende del propietario o la marina; sus soportes pueden guardarse como evidencia. |
| Interoperabilidad | SA-11 Servicios de emergencia | Excluido | La comunicación de una emergencia la realizan personas por los canales de rescate; el sistema aporta datos. |
| Interoperabilidad | SA-12 Área jurídica | Excluido | Las consultas jurídicas se tramitan caso por caso usando contratos y evidencias del arriendo. |
| Interoperabilidad | SA-13 Capitanes y tripulación | Excluido | La contratación de tripulación no forma parte de los servicios seleccionados; el acuerdo registra al capitán cuando aplica. |
| Interoperabilidad | SA-14 Migración de reservas actuales | Excluido | Una migración, si existe un registro anterior, sería puntual y no un servicio permanente de intercambio. |

## 10. Interoperabilidad de negocio: V9b (fase 2)

![V9b. Participantes e intercambios de información del arriendo](vistas/Fase%202/V09b-interoperabilidad-negocio.png)

La vista reúne los intercambios de la sexta capa. A la izquierda están los servicios del arriendo que necesitan información; en el centro, los servicios que median el intercambio; a la derecha, los servicios y participantes con los que se relaciona la empresa. Las flechas de ida y vuelta indican qué sale y qué entra, en vez de llamar «integración» a cualquier contacto con un tercero.

Por ejemplo, al CRM se le comunican estados de la reserva y se reciben datos comerciales del cliente. La marina recibe horarios y personas autorizadas, y devuelve restricciones, estado de preparación y novedades de entrega o recepción. A la pasarela se le solicitan cobros, garantías y devoluciones, y se esperan respuestas identificables para cada operación. La fuente de ubicación y combustible aporta la lectura y el momento en que se tomó; este último dato importa porque recibir una posición ahora no significa que se haya medido ahora.

También se muestran intercambios con contratos, evaluación de riesgo, aseguradora y contabilidad. Algunos ocurren solo bajo una condición: la evaluación externa, por ejemplo, no interviene en todos los arriendos. En gris aparecen enlaces identificados que no se implementarían como servicio de la aplicación, entre ellos autoridad marítima, mantenimiento, emergencias, asesoría jurídica y tripulación. El intercambio puede seguir existiendo mediante otros canales. Esta vista está construida con elementos de **Negocio ArchiMate**, de acuerdo con la descomposición de la fase 2; sus flujos permiten leer el sentido de la información y las decisiones de inclusión.

La interoperabilidad se muestra en dos niveles. V9b la describe en la capa de Negocio: qué información se intercambia con cada participante y en qué dirección. Su correspondencia en la capa de Aplicación es [V9](#162-v9-interoperabilidad-y-anticorrupción-en-aplicación-fase-5), donde los ocho intercambios incluidos aquí aparecen como sistemas externos conectados a la aplicación a través de un adaptador de la capa anticorrupción, con un flujo de entrada y otro de salida por cada uno. Las dos vistas se leen juntas: V9b responde qué intercambia el negocio y V9, por qué adaptador pasa y qué contexto lo consume.

## 11. Análisis del ecosistema (fase 3)

La aplicación apoya el arriendo de yates que pertenecen a terceros. Su ámbito va desde la disponibilidad y la reserva hasta la devolución y el cierre económico. No se ocupa por sí sola de todos los procesos de la empresa: el cliente solicita el servicio, el propietario controla la disponibilidad de su yate, la marina participa en la entrega y recepción, y la empresa coordina el arriendo.

El proceso también necesita información que se produce fuera de él. El CRM lleva la relación comercial con los clientes, la gestión de contratos aporta las condiciones acordadas y contabilidad recibe los valores que debe registrar o conciliar. Según el arriendo, intervienen además proveedores de evaluación de riesgo, aseguradoras, pasarelas de pago y fuentes de datos de ubicación y combustible. Los intercambios identificados en la [vista de interoperabilidad de negocio](#10-interoperabilidad-de-negocio-v9b-fase-2) ayudan a ubicar esas responsabilidades sin convertir a cada participante externo en parte de la aplicación.

Para comenzar el análisis DDD comparamos las reglas de reserva, disponibilidad, riesgo, entrega, seguimiento, devolución, dinero y calificaciones. No convertimos cada servicio de la fase 2 en un dominio: agrupamos lo que comparte decisiones y lenguaje. De esa comparación salen los cinco dominios descritos a continuación.

## 12. DDD estratégico: dominios y clasificación

Aquí llamamos dominio a un área del negocio con reglas y conceptos propios. No equivale a un servicio individual ni define, por sí sola, cuántos componentes tendrá el software. El equipo identificó cinco dominios: Gestión del arriendo, Evaluación de riesgo, Operación y seguimiento del yate, Liquidación y Reputación.

### 12.1 Gestión del arriendo (Core)

Se ocupa de que un yate pueda reservarse para un cliente en unas fechas y una marina determinadas. Reúne la habilitación o bloqueo por parte del propietario, el calendario, los apartados temporales, las solicitudes, la confirmación, las cancelaciones y los estados generales del arriendo. También coordina los avisos de reserva y consulta las condiciones del contrato. La disponibilidad se queda aquí porque bloquear un período, apartarlo y confirmarlo afectan el mismo compromiso sobre el yate.

Lo clasificamos como **Core** porque coordinar el arriendo de un bien ajeno es la actividad alrededor de la cual funciona la aplicación. Este dominio puede saber que un pago o una garantía cumplen una condición para avanzar, pero no calcula saldos ni decide cuánto retener. Tampoco evalúa por sí mismo el riesgo del cliente ni inspecciona la embarcación.

### 12.2 Evaluación de riesgo (Supporting)

Revisa los requisitos del cliente y las características del arriendo que puedan cambiar el riesgo de entregar el yate. Considera habilitaciones para navegar, antecedentes pertinentes y, cuando corresponda, una recomendación externa. Puede indicar que la solicitud necesita otra verificación, proponer condiciones especiales o recomendar que no continúe. La decisión del negocio no queda automáticamente en manos del proveedor externo.

Es **Supporting** porque protege al propietario y ayuda a decidir si se acepta un arriendo, pero su conocimiento no organiza el ciclo completo. Su resultado tampoco autoriza la salida: aun con un cliente aceptado, una falla o una restricción detectada antes de entregar el yate puede impedirla. El cobro del depósito y la comprobación física de la embarcación pertenecen a otras responsabilidades.

### 12.3 Operación y seguimiento del yate (Core)

Comienza con la preparación para la entrega y termina cuando el yate regresa y se inspecciona. Registra el estado inicial, las fotografías y el combustible; coordina la autorización operativa de salida; sigue la ubicación y el combustible durante el uso; atiende alertas e incidentes; y deja constancia del estado final. Así se pueden comparar entrega y devolución sin depender de recuerdos o de una lectura remota aislada.

Se decidió mantener entrega, seguimiento y devolución juntos porque comparten el conocimiento sobre el estado del mismo yate durante su uso. Lo clasificamos como **Core**: en un negocio que administra embarcaciones ajenas de alto valor, controlar su entrega y recepción con evidencia es parte de la confianza que ofrece a propietarios y clientes. Operación registra lo sucedido, pero no decide por sí sola quién debe pagar un daño ni cuánto cuesta resolverlo.

### 12.4 Liquidación (Supporting)

Reúne las reglas económicas del arriendo: alquiler pendiente, depósito, combustible, daños sustentados, penalizaciones pactadas, devoluciones y saldo final. También trata las reclamaciones sobre cobros. Su trabajo puede empezar antes de la entrega, cuando hay pagos y garantías que comprobar, y continúa después de que el yate regresa hasta resolver el cierre económico.

Es **Supporting** porque permite completar el arriendo y dar cuentas a las partes, aunque no define la disponibilidad ni controla el uso físico del yate. Toma las actas y demás evidencias de Operación como entrada; encontrar una diferencia en la inspección no basta para cobrarla al cliente. Liquidación determina los importes conforme a las condiciones acordadas, mientras la pasarela procesa las operaciones de pago y contabilidad recibe los valores para registrar o pagar al propietario según corresponda.

### 12.5 Reputación (Supporting)

Gestiona las calificaciones y comentarios que se intercambian cliente y propietario después del arriendo. Conserva el historial, permite consultar valoraciones y contempla la revisión de una calificación cuestionada. Esa información puede consultarse al estudiar futuros arriendos, sin sustituir la evaluación de riesgo ni aprobar o rechazar por sí sola una reserva.

Es **Supporting**: ayuda a generar confianza entre participantes, pero el arriendo puede realizarse y cerrarse sin que exista una calificación previa. Tampoco resuelve reclamaciones económicas ni reemplaza la relación comercial que lleva el CRM.

### 12.6 Clasificación de los dominios

| Dominio | Clasificación | Razón principal |
| --- | --- | --- |
| Gestión del arriendo | Core | Coordina la disponibilidad, la reserva y los estados del arriendo. |
| Evaluación de riesgo | Supporting | Aporta criterios para decidir si un arriendo puede continuar y bajo qué condiciones. |
| Operación y seguimiento del yate | Core | Controla con evidencia la entrega, el uso y la devolución de un bien ajeno. |
| Liquidación | Supporting | Resuelve los importes, ajustes y reclamaciones hasta el cierre económico. |
| Reputación | Supporting | Conserva valoraciones útiles para futuros arriendos. |

No clasificamos ninguno de estos cinco como **Generic**. Identidad y acceso, notificaciones y auditoría son funciones reutilizables que ya aparecen como servicios transversales en la fase 2; no las tratamos como un dominio estratégico del arrendamiento solo para completar la categoría.

## 13. Dominios: V5 (fase 3)

![V5. Dominios DDD y conceptos representativos](vistas/Fase%203/V05.Dominios%20DDD.png)

La vista muestra los cinco dominios de la sección anterior. Gestión del arriendo y Operación y seguimiento del yate están en **Core**; Evaluación de riesgo, Liquidación y Reputación están en **Supporting**. Bajo cada nombre aparecen unos pocos conceptos representativos, no un inventario exhaustivo. Por ejemplo, Arriendo, Reserva y Disponibilidad del yate se agrupan porque comprometer fechas de un yate forma parte de las reglas de reserva. Inspección, Lectura operativa e Incidente quedan con el seguimiento del mismo yate desde la entrega hasta la recepción.

Se eligió una `ApplicationFunction` para señalar el área de responsabilidad de cada dominio en esta vista y `DataObject` para representar información que esa función utiliza. Las flechas de acceso unen cada función con sus conceptos. Un dominio DDD abarca reglas y lenguaje de negocio: la función de aplicación es una forma de ubicarlo en ArchiMate, no una afirmación de que todo el dominio vaya a implementarse como una sola función o componente.

La separación entre Operación y Liquidación tiene una consecuencia práctica: la primera registra una novedad y las evidencias del estado del yate; la segunda determina si eso da lugar a un ajuste económico. Del mismo modo, el resultado de Evaluación de riesgo informa a Gestión del arriendo, pero no confirma una reserva ni autoriza por sí solo la entrega. Así se mantienen los límites argumentados en la clasificación sin copiar la lista de servicios de la fase 2.

## 14. Lenguaje ubicuo: V7 (fase 3)

![V7. Lenguaje ubicuo por dominio](vistas/Fase%203/V07.%20Lenguaje%20ubicuo.png)

V7 agrupa los términos según el dominio donde se usan. Cada término está representado como `BusinessObject` y se conecta con un elemento `Meaning` que muestra su definición. En el modelo ArchiMate también se conserva una `Representation` de glosario por dominio; en esta vista se prefirió mostrar directamente cada término y su significado para poder leerlos uno junto al otro. Los ejemplos están guardados como propiedades de los términos y se transcriben aquí para que también sean visibles desde GitHub. Son ejemplos del caso simulado, no registros de arriendos reales.

| Dominio | Término | Definición | Ejemplo |
| --- | --- | --- | --- |
| Gestión del arriendo | Arriendo | Acuerdo mediante el cual un cliente obtiene el uso temporal de un yate bajo unas condiciones determinadas. | Arriendo del yate Y-15 durante el sábado de 9:00 a 17:00. |
| Gestión del arriendo | Solicitud de reserva | Petición inicial del cliente para utilizar un yate en un periodo determinado. Todavía no garantiza el uso del yate. | El cliente solicita un yate para el próximo domingo. |
| Gestión del arriendo | Reserva confirmada | Reserva que cumplió las condiciones necesarias para ser aceptada por el negocio. | El cliente cumple las condiciones y recibe la confirmación. |
| Gestión del arriendo | Disponibilidad | Condición que indica que un yate puede comprometerse para un periodo determinado. | El yate está disponible el sábado y bloqueado el domingo. |
| Gestión del arriendo | Bloqueo | Periodo durante el cual un yate no puede ser reservado. | El propietario bloquea el yate porque lo utilizará personalmente. |
| Gestión del arriendo | Periodo de arriendo | Intervalo de tiempo durante el cual se acuerda el uso del yate. | 10 de octubre, de 08:00 a 18:00. |
| Gestión del arriendo | Cancelación | Terminación de una solicitud o reserva antes de ejecutar el arriendo. | El cliente cancela antes de la entrega. |
| Gestión del arriendo | Estado del arriendo | Situación del ciclo administrativo del arriendo. | Solicitado, confirmado, en ejecución o finalizado. |
| Evaluación de riesgo | Evaluación de riesgo | Análisis para determinar si las condiciones de un posible arriendo son aceptables desde la perspectiva del riesgo. | Se revisan requisitos del cliente antes de permitir que el proceso continúe. |
| Evaluación de riesgo | Elegibilidad | Condición que indica si el cliente cumple los requisitos mínimos necesarios para continuar con el arriendo. | El cliente acredita las habilitaciones requeridas. |
| Evaluación de riesgo | Requisito del cliente | Condición que el cliente debe satisfacer para participar en el arriendo. | Identificación o habilitación necesaria para navegar. |
| Evaluación de riesgo | Resultado de riesgo | Resultado de la evaluación utilizado como insumo para decidir si el arriendo continúa y bajo qué condiciones. | La evaluación solicita una verificación adicional. |
| Evaluación de riesgo | Condición especial | Requisito adicional establecido para permitir un arriendo determinado. | El arriendo puede realizarse únicamente con capitán. |
| Evaluación de riesgo | Evaluador externo | Organización externa que proporciona información o una recomendación para apoyar la evaluación de riesgo. | Una empresa externa devuelve una recomendación sobre el arriendo. |
| Evaluación de riesgo | Riesgo del arriendo | Posibilidad de que las características del cliente o del arriendo generen una condición no aceptable para el negocio. | Las condiciones del cliente requieren una revisión adicional. |
| Operación y seguimiento del yate | Inspección | Revisión del estado del yate realizada antes de la entrega o después de la devolución. | Se revisan casco, equipos, documentación y combustible. |
| Operación y seguimiento del yate | Acta de entrega | Evidencia del estado del yate en el momento previo a su entrega. | El acta registra un rayón que ya existía antes del arriendo. |
| Operación y seguimiento del yate | Acta de devolución | Evidencia del estado del yate después de finalizar su uso. | Se registra el combustible final y las novedades encontradas. |
| Operación y seguimiento del yate | Autorización de entrega | Decisión operativa que determina que el yate puede ser entregado en ese momento. | Operaciones autoriza la salida después de completar las verificaciones. |
| Operación y seguimiento del yate | Lectura operativa | Registro de ubicación, combustible u otra información obtenida durante el uso del yate. | Última posición conocida registrada a las 14:35. |
| Operación y seguimiento del yate | Incidente | Situación anormal ocurrida durante la operación que requiere seguimiento o registro. | Pérdida prolongada de señal o una falla del motor. |
| Operación y seguimiento del yate | Novedad | Hallazgo o cambio observado durante una inspección o durante el uso del yate. | Se detecta un golpe al devolver la embarcación. |
| Operación y seguimiento del yate | Devolución | Momento en el cual el cliente retorna físicamente el yate. | El cliente regresa el yate a la marina a la hora acordada. |
| Operación y seguimiento del yate | Estado del yate | Condición física u operativa de la embarcación en un momento determinado. | Preparado, con novedad o no apto para entrega. |
| Operación y seguimiento del yate | Riesgo operativo | Situación durante la entrega, navegación o devolución que puede afectar la seguridad o la operación. | Una falla mecánica impide autorizar la salida. |
| Liquidación | Liquidación del arriendo | Proceso mediante el cual se determinan los valores económicos finales asociados al arriendo. | Se calculan combustible, cargos sustentados y saldo de la garantía. |
| Liquidación | Garantía / depósito | Valor destinado a respaldar determinadas obligaciones del cliente durante el arriendo. | Se constituye una garantía antes de la entrega. |
| Liquidación | Ajuste económico | Modificación del saldo final producida por una condición posterior al uso. | Se genera un cobro adicional por combustible. |
| Liquidación | Cargo | Valor atribuido al cliente cuando existe fundamento contractual y evidencia suficiente. | Se determina un cargo por un daño debidamente sustentado. |
| Liquidación | Reembolso | Devolución total o parcial de un valor previamente cobrado. | Se devuelve el saldo restante de la garantía. |
| Liquidación | Reclamación de cobro | Objeción realizada frente a un cargo asociado al arriendo. | El cliente cuestiona un cobro por daño. |
| Liquidación | Cierre económico | Estado en el que se han resuelto los valores, garantías y reclamaciones necesarias del arriendo. | La garantía se libera y no quedan cargos pendientes. |
| Reputación | Calificación | Valoración realizada después de la experiencia de arriendo. | El propietario califica al cliente después de la devolución. |
| Reputación | Reputación | Información acumulada a partir de experiencias anteriores de un participante. | El cliente posee varias valoraciones previas. |
| Reputación | Historial de reputación | Conjunto de calificaciones y antecedentes relacionados con experiencias anteriores. | Se consultan las valoraciones previas antes de un nuevo arriendo. |
| Reputación | Valoración cuestionada | Calificación cuya validez o contenido es objeto de una reclamación. | El cliente afirma que una valoración contiene información incorrecta. |
| Reputación | Participante evaluado | Persona sobre la cual se registra una calificación. | Cliente o propietario evaluado después de un arriendo. |

## 15. Diseño táctico: agregados, comandos y eventos (fase 4)

La fase 3 respondió *qué partes tiene el negocio*; esta responde *cómo se guardan y cómo cambian*. Cada uno de los cinco dominios tiene su propia vista V6. En todas, el agregado queda a la izquierda con sus entidades y objetos de valor, los comandos que lo modifican en el centro y los eventos que producen a la derecha. Así se lee de un vistazo quién cambia qué y qué queda registrado.

ArchiMate no trae estereotipos de agregado ni de comando. Representamos agregados, entidades y objetos de valor con `DataObject`; comandos con `ApplicationProcess`; eventos de dominio con `ApplicationEvent`; e hitos de negocio con `BusinessEvent`. Las V6 muestran el acceso del comando al agregado y el evento que dispara. V6a dibuja las relaciones de composición entre raíces y partes; V6b–V6e delimitan sus agregados mediante recuadros titulados. Así se pueden distinguir las raíces y sus partes sin trazar todas las relaciones sobre los comandos. Las raíces y varios conceptos ya existían desde V5: se reutilizaron y se les añadieron sus propiedades de tipo DDD e invariante.

### 15.1 Gestión del arriendo: V6a (fase 4)

![V6a. Agregados, comandos y eventos - Gestión del arriendo](vistas/Fase%204/V06a-agregados-comandos-eventos-gestion-del-arriendo.png)

El agregado **Arriendo** contiene la reserva, el estado y la cancelación. Su invariante central es que solo se confirma con la elegibilidad aprobada y, cuando el acuerdo lo exija, con la autorización del propietario; una reserva confirmada no autoriza la entrega. **Disponibilidad del yate** conserva el periodo, el bloqueo y el apartado temporal, y garantiza que dos periodos confirmados sobre el mismo yate no se solapen. **Yate** agrupa la ficha y la habilitación que decide el propietario.

Se separaron el apartado temporal y el bloqueo porque no son lo mismo: el primero aparta unas horas mientras se cierran las condiciones y el segundo inhabilita el periodo por decisión del propietario o por mantenimiento. Confirmar, rechazar y cancelar producen eventos de negocio, no solo de dominio, porque cambian el estado del yate frente al propietario y la marina. La comprobación de riesgo se dejó como invariante del arriendo, ya que Gestión del arriendo consulta un resultado que produce otro dominio pero no lo calcula.

### 15.2 Evaluación de riesgo: V6b (fase 4)

![V6b. Agregados, comandos y eventos - Evaluación de riesgo](vistas/Fase%204/V06b-agregados-comandos-eventos-evaluacion-de-riesgo.png)

Los dos recuadros de la izquierda delimitan los agregados. **Evaluación de riesgo** es la raíz del primero, con la verificación, el resultado y la condición especial; **Expediente del cliente** es la raíz del segundo, con el requisito, la habilitación y los antecedentes. La evaluación exige que ningún resultado sea «apto» si falta un requisito obligatorio, y aclara que la recomendación externa informa pero no decide. El expediente mantiene la regla de que una habilitación vencida no permite aprobar y que los datos comerciales no viven aquí sino en el CRM.

La recomendación externa se guardó como insumo y no como decisión final, tal como se argumentó en la fase 3. Por eso registrarla es un evento de dominio, mientras que aprobar la elegibilidad sí es un evento de negocio: es la señal que habilita la confirmación de la reserva. Imponer una condición especial no la convierte en un requisito general, así que quedó como objeto de valor de esa evaluación y no del expediente.

### 15.3 Operación y seguimiento del yate: V6c (fase 4)

![V6c. Agregados, comandos y eventos - Operación y seguimiento del yate](vistas/Fase%204/V06c-agregados-comandos-eventos-operacion-y-seguimiento-del-yate.png)

Los dos recuadros de la izquierda separan los agregados. **Operación del arriendo** reúne la inspección, la autorización de entrega, la novedad, el estado del yate y la evidencia. Su invariante impide entregar sin inspección completa y yate apto, exige el mismo checklist en la entrega y la devolución, y recuerda que registrar una novedad no atribuye responsabilidad. **Seguimiento del yate** agrupa la lectura, el incidente, la ubicación y la marca de tiempo, y fija que cada lectura lleva su hora de medición y que una lectura ausente no equivale a cero.

Autorizar entrega y registrar entrega quedaron como comandos distintos porque no son la misma decisión: uno es una autorización operativa y el otro deja constancia de que el yate salió. El estado del yate y la evidencia son objetos de valor porque se comparan por su contenido y no tienen identidad propia. Registrar una novedad no dispara evento de negocio a propósito: el cobro se decide en Liquidación a partir de las actas, y el único hito de negocio aquí es la devolución del yate.

### 15.4 Liquidación: V6d (fase 4)

![V6d. Agregados, comandos y eventos - Liquidación](vistas/Fase%204/V06d-agregados-comandos-eventos-liquidacion.png)

Los tres recuadros de la izquierda tienen como raíces **Liquidación del arriendo**, con el cargo, el ajuste y el saldo; **Garantía**, con la retención y su saldo; y **Reclamación de cobro**, con su estado. La liquidación exige que todo cargo se sustente en evidencia comparada y en una condición contractual, y que el saldo no quede negativo sin un reembolso explícito. La garantía deja claro que no es ingreso: solo se retiene lo sustentado y el resto se libera o se reembolsa. La reclamación no permite cerrar el arriendo mientras siga abierta.

Se separó la garantía de la liquidación aunque las dos tocan dinero, porque constituir, retener o liberar un depósito no es lo mismo que calcular el saldo final. Cerrar arriendo es el único comando de este dominio que produce un evento de negocio, ya que es el hito que deja el arriendo sin pagos, garantías ni reclamaciones pendientes. La retención de dinero en la pasarela se dejó como evento de dominio, porque el procesamiento real ocurre fuera del dominio.

### 15.5 Reputación: V6e (fase 4)

![V6e. Agregados, comandos y eventos - Reputación](vistas/Fase%204/V06e-agregados-comandos-eventos-reputacion.png)

Los dos recuadros de la izquierda delimitan **Perfil de reputación** y **Revisión de valoración**. El primero contiene la calificación, el historial y el promedio, con la regla de una única calificación por participante y por arriendo y con la aclaración de que la reputación informa pero no aprueba ni rechaza una reserva. El segundo agrupa la valoración cuestionada y su estado, y mantiene que una valoración cuestionada no se publica hasta resolverse.

Ningún comando de este dominio produce evento de negocio: el arriendo puede existir sin depender de la reputación, como se concluyó al clasificarla como Supporting. La revisión se modeló como agregado aparte del perfil para no mezclar el historial publicado con lo que está en discusión.

### 15.6 Correspondencia con el lenguaje ubicuo

Las raíces de agregado realizan los términos de negocio de V7 mediante relaciones `Realization` en el modelo: Arriendo, Disponibilidad del yate, Evaluación de riesgo, Garantía, Reclamación de cobro y los demás ya aparecían como términos del glosario. Ese enlace se dejó en la carpeta de Relaciones pero no se dibujó en V6 para no saturar la lectura. Así, cada agregado queda atado al término del negocio que implementa.

## 16. Mapa de contextos delimitados (fase 5)

La fase 4 dejó cada dominio con sus agregados y eventos; esta decide cómo se relacionan los dominios entre sí y con el exterior. Los cinco dominios de la fase 3 se representan como contextos delimitados de aplicación, cada uno con su servicio y su interfaz. La relación entre ellos indica el patrón utilizado. V8 y V9 salen del mismo modelo ArchiMate, ahora en la capa de Aplicación.

### 16.1 V8. Mapa de contextos delimitados y patrones (fase 5)

![V8. Mapa de contextos delimitados y patrones](vistas/Fase%205/V08-mapa-de-contextos-delimitados-y-patrones-v2.png)

La vista junta los cinco contextos, el núcleo compartido y la leyenda de patrones. Gestión del arriendo y Operación y seguimiento del yate, los dos Core, forman la columna de la solución: la evaluación de riesgo les entrega resultados y de ellos salen los flujos hacia la liquidación, todos como cliente-proveedor. El servicio de reserva que publica Gestión lo consume Reputación como conformista (servicio abierto y lenguaje publicado).

El recuadro «Arriendo, Yate y Participante» resume la parte común que permite a Gestión y Operación referirse al mismo arriendo, yate y participante. No representa que ambos compartan el agregado completo de Arriendo ni sus estados: Gestión conserva las reglas de disponibilidad y reserva; Operación, las de entrega, inspección y seguimiento. V8 no desglosa esa parte común en elementos individuales. Compartir su identidad y significado introduce un acoplamiento limitado: cualquier cambio en esas definiciones debe acordarse entre ambos contextos antes de aplicarlo.

Cada contexto realiza su servicio de aplicación, que apoya los servicios de negocio identificados en la fase 2; estos no eran `ApplicationService`. Los eventos de dominio de la fase 4 no aparecen porque aquí se muestran las relaciones entre contextos. Los contratos de servicio de negocio se documentan en V4d; V9b muestra la información intercambiada con participantes externos.

### 16.2 V9. Interoperabilidad y anticorrupción en Aplicación (fase 5)

![V9. Interoperabilidad y anticorrupción en la capa de Aplicación](vistas/Fase%205/V09-interoperabilidad-anticorrupcion.png)

La vista tiene tres columnas: los contextos a la izquierda, la capa anticorrupción (ACL) al centro y los sistemas externos a la derecha. Los ocho sistemas de V9b se conectan con una interfaz de la ACL mediante dos flujos, uno de entrada y otro de salida; la interfaz sirve a su contexto y la capa anticorrupción agrega sus ocho interfaces. La ACL traduce formato y terminología, nunca reglas de negocio, y se mantiene el criterio de exclusión de la fase 2: no entró ningún sistema nuevo ni se agregó ninguna decisión dentro de la frontera.

Esta es la vista de interoperabilidad y anticorrupción en la capa de Aplicación, y corresponde a la [V9b de Negocio](#10-interoperabilidad-de-negocio-v9b-fase-2). Cada servicio de interoperabilidad incluido en la fase 2 tiene aquí su adaptador:

| Servicio de negocio (V9b) | Adaptador de aplicación (V9) | Contexto que lo consume |
| --- | --- | --- |
| SA-01 CRM comercial | ACL - CRM comercial | Gestión del arriendo |
| SA-02 Gestión de contratos | ACL - Gestión de contratos | Gestión del arriendo |
| SA-03 Evaluador de riesgo | ACL - Evaluación de riesgo externa | Evaluación de riesgo |
| SA-04 Aseguradora | ACL - Aseguradora | Operación y seguimiento del yate |
| SA-05 Marinas | ACL - Marinas | Operación y seguimiento del yate |
| SA-06 Pasarela de pagos | ACL - Pasarela de pagos | Liquidación |
| SA-07 Ubicación y combustible | ACL - Fuente de ubicación y combustible | Operación y seguimiento del yate |
| SA-09 Contabilidad | ACL - Contabilidad | Liquidación |

Los seis servicios de interoperabilidad excluidos en la fase 2 (SA-08 y SA-10 a SA-14) no tienen adaptador. Cada adaptador tiene dos flujos, lo que entra y lo que sale, con el mismo contenido que su intercambio en V9b. En Contabilidad el flujo principal es de salida: la aplicación le entrega los valores calculados y recibe de vuelta las conciliaciones.

## 17. Trazabilidad vertical: V10

V10 conecta las razones del negocio con lo que se construye en la aplicación. Las cinco vistas recorren Motivación, Estrategia, Negocio y Aplicación para mostrar por qué existe cada contexto. Son vistas transversales: cada una escoge un hilo legible, mientras el modelo fuente conserva las relaciones entre las capas.

### 17.1 V10a. Gestión del arriendo

![V10a. Trazabilidad vertical de Gestión del arriendo](vistas/Trazabilidad%20Vertical%20V10/V10a.%20Trazabilidad%20vertical%20del%20contexto%20Gestion%20del%20arriendo.png)

La vista sigue dos preocupaciones de la reserva: evitar compromisos incompatibles sobre un mismo yate y coordinar su confirmación con propietario y marina. Relaciona esas metas y resultados con la capacidad de Gestión de reservas, el tramo «Reservar y garantizar», los servicios de solicitud y confirmación y los procesos que atienden al cliente. En Aplicación, el servicio de reserva, la función Gestión del arriendo y su componente muestran qué parte de la solución respalda ese trabajo. La capacidad estratégica y la función de negocio comparten nombre, pero no significado: una expresa lo que la empresa puede hacer; la otra organiza el comportamiento que presta el servicio.

### 17.2 V10b. Evaluación de riesgo

![V10b. Trazabilidad vertical de Evaluación de riesgo](vistas/Trazabilidad%20Vertical%20V10/V10b.%20Trazabilidad%20vertical%20del%20contexto%20Evaluacion%20de%20riesgo.png)

El hilo empieza en el riesgo de entregar un bien ajeno a un cliente que quizá no cumpla los requisitos. La meta de entrega segura se vincula con verificar elegibilidad antes de confirmar la reserva. La capacidad de Evaluación del riesgo del arriendo y el servicio de negocio ST-03 sitúan esa verificación en el proceso; el servicio y componente de aplicación de Evaluación de riesgo proporcionan su soporte. Mantener separados el resultado de riesgo y la autorización de entrega evita atribuir al evaluador una decisión operativa que corresponde a otro contexto.

### 17.3 V10c. Operación y seguimiento del yate

![V10c. Trazabilidad vertical de Operación y seguimiento del yate](vistas/Trazabilidad%20Vertical%20V10/V10c.%20Trazabilidad%20vertical%20del%20contexto%20Operacion%20del%20yate.png)

Esta vista toma como hilo la evidencia del estado del yate. La preocupación por daños y cobros sin sustento lleva al resultado de registrar la entrega y la devolución con actas comparables. La capacidad de Coordinación de entrega y devolución se relaciona con los procesos de preparación, entrega e inspección y con ST-08, que conserva las actas. En Aplicación, el servicio de operación y su componente respaldan esas actividades. Así se puede rastrear una inspección hasta la meta de cerrar el arriendo con responsabilidades resueltas, sin afirmar que la inspección decida por sí misma quién paga un daño.

### 17.4 V10d. Liquidación

![V10d. Trazabilidad vertical de Liquidación](vistas/Trazabilidad%20Vertical%20V10/V10d.%20Trazabilidad%20vertical%20del%20contexto%20Liquidacion.png)

La devolución física no resuelve automáticamente combustible, garantías, daños ni reclamaciones. La vista conecta la meta de cerrar el arriendo con pagos y responsabilidades resueltos con la capacidad de Liquidación del arriendo, el servicio ST-14 y el proceso que calcula ajustes con el cliente. El servicio de aplicación, la función y el contexto de Liquidación completan el hilo hasta el componente que soporta el cálculo. La pasarela y contabilidad ejecutan o registran operaciones económicas, pero las reglas para determinar el saldo pertenecen a Liquidación.

### 17.5 V10e. Reputación

![V10e. Trazabilidad vertical de Reputación](vistas/Trazabilidad%20Vertical%20V10/V10e.%20Trazabilidad%20vertical%20del%20contexto%20Reputacion.png)

Las calificaciones mutuas después de la devolución se relacionan con la satisfacción de clientes y propietarios. V10e sitúa ese resultado junto a la capacidad de Gestión de la reputación, el tramo «Devolver y cerrar» y el servicio ST-16, que atiende las calificaciones del cliente y del propietario. En Aplicación, el servicio y el contexto de Reputación dan continuidad a esa información. La trazabilidad explica por qué existe este contexto de soporte sin convertir una calificación, por sí sola, en una autorización o rechazo de reserva.

## 18. Bounded Context Canvas: V11 (fase 6)

### 18.1 V11a. Gestión del arriendo, el contexto principal

![V11a. Bounded Context Canvas del contexto Gestión del arriendo](vistas/Fase%206/V11a-canvas-gestion-del-arriendo.png)

El canvas resume en una sola página el contexto principal, Gestión del arriendo, siguiendo las secciones del Bounded Context Canvas de ddd-crew: nombre y propósito, clasificación estratégica, rol del dominio, comunicación entrante y saliente, lenguaje ubicuo, decisiones de negocio, supuestos, preguntas abiertas y métricas de verificación.

Escogimos Gestión del arriendo porque es el contexto Core que coordina el compromiso sobre el yate: Evaluación de riesgo le entrega la elegibilidad, Operación ejecuta lo que él confirma y Reputación consume su servicio de reserva. En la clasificación estratégica lo leemos en tres ejes: es Core en el dominio, genera ingresos en el modelo de negocio porque cada reserva confirmada es un alquiler, y es un desarrollo a medida en su evolución. Su rol es de ejecución y de aprobación: lleva la solicitud hasta su estado final y decide la confirmación, pero no evalúa el riesgo del cliente, no entrega el yate ni calcula importes.

Lo construimos como vista ArchiMate y no como Canvas de Archi para que cada sección use elementos del mismo modelo y no texto suelto. El centro es el componente «Contexto de Gestión del arriendo» con su servicio de reserva. A la izquierda están quienes le envían mensajes: el cliente, el propietario, Evaluación de riesgo y los adaptadores de la capa anticorrupción para el CRM y la gestión de contratos. A la derecha están quienes los reciben: Operación, Reputación, el núcleo compartido y los tres eventos de negocio que produce. Las relaciones dibujadas son las mismas de V8 y V9: cliente-proveedor con Riesgo y con Operación, servicio abierto hacia Reputación, núcleo compartido con Operación y servicio traducido desde la capa anticorrupción.

Cada mensaje lleva su tipo, porque no es lo mismo una orden que un hecho. «Solicitar reserva» y «Bloquear disponibilidad» son comandos; «Elegibilidad aprobada» y «Reserva confirmada» son eventos; los datos del CRM y las condiciones del contrato son consultas. El lenguaje ubicuo usa los ocho términos de V7 para este dominio, incluidas «Solicitud de reserva» y «Reserva confirmada» por separado. Las decisiones de negocio muestran las tres raíces de agregado de V6a con sus invariantes, y las métricas son los tres resultados de V1 que este contexto ayuda a cumplir.

Quedan tres preguntas abiertas que la entrevista no respondió: cuánto dura un apartado temporal, cuánto tiempo de preparación queda entre dos arriendos del mismo yate y qué tasa de ocupación fija la dirección como meta.

Los otros cuatro Canvas usan la misma estructura para comparar los límites y las comunicaciones de los contextos. Al cruzarlos aparecieron tres huecos en el mapa de contextos que dejamos como preguntas abiertas en vez de inventar relaciones: Gestión no tiene un canal para enterarse de una entrega no autorizada ni del pago del anticipo antes de confirmar, y a Reputación nada le avisa que el arriendo terminó. También queda abierto si Evaluación de riesgo debe consultar el historial de Reputación.

### 18.2 V11b. Evaluación de riesgo

![V11b. Bounded Context Canvas del contexto Evaluación de riesgo](vistas/Fase%206/V11b-canvas-evaluacion-de-riesgo.png)

Evaluación de riesgo es Supporting: comprueba requisitos y habilitaciones del cliente, registra verificaciones y puede solicitar una recomendación externa. Esa recomendación llega por la ACL, pero el contexto conserva la decisión sobre elegibilidad y condiciones especiales. Entrega a Gestión del arriendo el resultado que esta necesita para decidir sobre la reserva; no confirma reservas ni autoriza la salida del yate. La invariante mostrada en el Canvas impide declarar apta una evaluación con requisitos obligatorios pendientes. La consulta del historial de Reputación aparece como pregunta, no como relación existente en V8.

### 18.3 V11c. Operación y seguimiento del yate

![V11c. Bounded Context Canvas del contexto Operación y seguimiento del yate](vistas/Fase%206/V11c-canvas-operacion-y-seguimiento-del-yate.png)

Este contexto Core recibe la reserva confirmada de Gestión y coordina preparación, entrega, seguimiento y recepción. Los datos de marinas, telemetría y aseguradora cruzan la capa anticorrupción; dentro del contexto se registran actas, lecturas e incidentes y se decide si el yate está en condiciones de salir. Envía a Liquidación la devolución y sus evidencias para que allí se determine cualquier ajuste económico. Separar esos roles impide convertir una novedad observada en un cobro automático. El Canvas deja abierta la manera de avisar a Gestión cuando la entrega no se autoriza, porque V8 aún no define ese flujo.

### 18.4 V11d. Liquidación

![V11d. Bounded Context Canvas del contexto Liquidación](vistas/Fase%206/V11d-canvas-liquidacion.png)

Liquidación es Supporting y toma las actas y novedades que entrega Operación para calcular combustible, cargos sustentados y saldo de la garantía. Distingue ese cálculo de ejecutar un cobro: la pasarela procesa pagos y reembolsos, mientras contabilidad recibe los valores para registro y conciliación mediante sus adaptadores. Sus reglas exigen evidencia y una condición contractual para cada cargo; tampoco permiten cerrar el arriendo con una reclamación abierta. La confirmación del anticipo es una entrada necesaria para la reserva, pero el Canvas señala que V8 no muestra aún cómo se comunica ese resultado a Gestión.

### 18.5 V11e. Reputación

![V11e. Bounded Context Canvas del contexto Reputación](vistas/Fase%206/V11e-canvas-reputacion.png)

Reputación, también Supporting, consulta el arriendo y sus participantes mediante el servicio publicado por Gestión; por eso se presenta como consumidor conformista. Guarda una calificación por participante y arriendo, conserva el historial y separa la revisión de valoraciones cuestionadas de las ya publicadas. Sus calificaciones pueden informar una decisión futura, pero no aprueban una reserva ni sustituyen la evaluación formal del riesgo. Para habilitar las calificaciones después del uso necesita saber que el arriendo terminó: el Canvas identifica ese aviso como una comunicación aún no definida en V8.

## 19. Modelo y evidencias

- [Modelo ArchiMate fuente](modelo/arrendamiento_yates.archimate), que contiene las vistas anteriores en un solo archivo.
- [Guion de entrevista](Entrevista/01_guion_entrevista.md).
- [Instrucción de rol enviada a la IA](Entrevista/02_instruccion_rol_ia.md).
- [Conversación con el experto simulado](Entrevista/03_conversacion_experto.md).
- [Análisis de hallazgos](Entrevista/04_analisis_hallazgos.md).

## 20. Aportes del equipo

Los aportes se describen según los archivos y commits que aparecen en el historial del repositorio. Los enlaces permiten comprobar el trabajo registrado de cada integrante.

| Integrante (autor en Git) | Aporte verificable | Commits de evidencia |
| --- | --- | --- |
| Samuel (`samuel2524`) | Guion, instrucción de rol, conversación y análisis de la entrevista; documentación de dominios y lenguaje ubicuo; integración del README y mejoras de V6. | [`43f88f3`](https://github.com/samuel2524/Arquitectura-Proyecto-Yates/commit/43f88f3), [`92b6c2a`](https://github.com/samuel2524/Arquitectura-Proyecto-Yates/commit/92b6c2a), [`20dc5ff`](https://github.com/samuel2524/Arquitectura-Proyecto-Yates/commit/20dc5ff) |
| Andrés (`Andres`) | Mapas de motivación, capacidades y proceso E2E; descomposición de servicios; vistas de interoperabilidad y Canvas V11. | [`ccf81da`](https://github.com/samuel2524/Arquitectura-Proyecto-Yates/commit/ccf81da), [`cfa2d25`](https://github.com/samuel2524/Arquitectura-Proyecto-Yates/commit/cfa2d25), [`2c2e9a8`](https://github.com/samuel2524/Arquitectura-Proyecto-Yates/commit/2c2e9a8), [`8f646f9`](https://github.com/samuel2524/Arquitectura-Proyecto-Yates/commit/8f646f9) |
| Juan García (`Juan Garcia`) | Diseño táctico V6, mapa de contextos, ajustes del modelo e imágenes y correcciones de legibilidad. | [`320e8ff`](https://github.com/samuel2524/Arquitectura-Proyecto-Yates/commit/320e8ff), [`9fa3efa`](https://github.com/samuel2524/Arquitectura-Proyecto-Yates/commit/9fa3efa), [`78586e7`](https://github.com/samuel2524/Arquitectura-Proyecto-Yates/commit/78586e7) |
| José (`Josemjaram`) | Vistas de trazabilidad vertical V10a–V10e en el modelo, Componente Investigativo del Canvas y revisión de V6a. | [`06a77ed`](https://github.com/samuel2524/Arquitectura-Proyecto-Yates/commit/06a77ed), [`10e425b`](https://github.com/samuel2524/Arquitectura-Proyecto-Yates/commit/10e425b), [`39b0225`](https://github.com/samuel2524/Arquitectura-Proyecto-Yates/commit/39b0225) |
