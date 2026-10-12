# Fase 4. Diseño táctico (DDD)

Esta fase baja los cinco dominios de la fase 3 a piezas concretas: las **entidades, agregados, comandos y eventos** que sostienen las reglas del negocio. Cada dominio tiene su propia vista V6, con el agregado a la izquierda, los comandos que lo modifican en el centro y los eventos que producen a la derecha.

- [V6a. Gestión del arriendo](V06a-gestion-del-arriendo.md)
- [V6b. Evaluación de riesgo](V06b-evaluacion-de-riesgo.md)
- [V6c. Operación y seguimiento del yate](V06c-operacion-y-seguimiento.md)
- [V6d. Liquidación](V06d-liquidacion.md)
- [V6e. Reputación](V06e-reputacion.md)
- [Glosario táctico](#glosario-tactico)

Las vistas siguen saliendo del mismo [modelo ArchiMate](../modelo/arrendamiento_yates.archimate). No se creó un archivo aparte: los agregados, comandos y eventos se agregaron a la carpeta de Aplicación y las relaciones a la de Relaciones.

## Cómo se representó el diseño táctico

ArchiMate no trae un estereotipo de agregado ni de comando, así que se eligió una correspondencia sencilla y se dejó documentada:

| Concepto DDD | Elemento ArchiMate | Por qué |
| --- | --- | --- |
| Agregado, entidad, objeto de valor | `ApplicationDataObject` | Son información con reglas; ya se venían usando `DataObject` para los conceptos de cada dominio en V5. |
| Comando | `ApplicationProcess` | Un comando es una acción que cambia el estado de un agregado. |
| Evento de dominio | `ApplicationEvent` | Hecho interno que ocurre dentro del dominio al ejecutarse un comando. |
| Evento de negocio | `BusinessEvent` | Hito que le interesa al negocio entero y no solo a un dominio. |

Las relaciones dibujadas son: acceso de escritura (`Access`) del comando al agregado que modifica, disparo (`Triggering`) del comando al evento que produce, y composición (`Aggregation`) del agregado hacia sus entidades y objetos de valor. Además, cada raíz de agregado realiza (`Realization`) el término de negocio equivalente de V7, aunque ese enlace no se dibuja para no saturar la vista.

Se reutilizaron los `DataObject` que ya existían desde la fase 3 como raíces de agregado o como entidades (Arriendo, Reserva, Disponibilidad del yate, Evaluación de riesgo, Garantía, entre otros). A esos elementos solo se les añadieron las propiedades «Tipo DDD» e «Invariante»; no se duplicaron. Los términos que faltaban se crearon como nuevos `DataObject`.

## Glosario táctico

- **Agregado:** grupo de objetos que cambian juntos y que se protegen mediante una sola raíz. Las operaciones entran por la raíz, nunca directo a las partes.
- **Raíz de agregado:** el objeto que representa todo el conjunto y garantiza sus invariantes.
- **Entidad:** objeto con identidad propia dentro del agregado.
- **Objeto de valor:** dato sin identidad, definido por su valor (un periodo, un estado, un saldo).
- **Comando:** petición de cambio, con nombre en imperativo. Su efecto o se aplica completo o no se aplica.
- **Evento de dominio:** hecho ya ocurrido dentro del dominio, con nombre en participio.
- **Evento de negocio:** hecho que marca un hito del arriendo y que otras áreas o sistemas observan.
- **Invariante:** regla que el agregado debe cumplir siempre y que ningún comando puede romper.
