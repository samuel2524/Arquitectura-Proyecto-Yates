# Fase 5. Mapa de contextos delimitados

La fase 4 dejó cada dominio con sus piezas internas: agregados, comandos y eventos. Esta fase decide cómo se relacionan esos dominios entre sí y con el mundo exterior. Los cinco dominios de la fase 3 se vuelven aquí **contextos delimitados**, cada uno con su propio modelo, y a cada relación se le asigna un **patrón de integración** según quién necesita qué cosa.

- [V8. Mapa de contextos delimitados y patrones](V08-mapa-de-contextos.md)
- [V9. Interoperabilidad y anticorrupción en Aplicación](V09-interoperabilidad-acl.md)
- [Glosario de patrones](#glosario-de-patrones)

Las vistas salen del mismo [modelo ArchiMate](../modelo/arrendamiento_yates.archimate), esta vez en la capa de Aplicaciones. Se crearon componentes, servicios e interfaces de aplicación; las relaciones quedaron en la carpeta de Relaciones y cada una lleva en su nombre el patrón que representa, para encontrarlas de un vistazo.

## Los cinco contextos

| Contexto | Clasificación (fase 3) | Papel en el mapa |
| --- | --- | --- |
| Gestión del arriendo | Core | Eje del mapa: confirma reservas y concentra la mayor parte de los compromisos |
| Operación y seguimiento del yate | Core | Ejecuta lo que Gestión comprometió y alimenta a Liquidación |
| Evaluación de riesgo | Supporting | Proveedor aguas arriba de Gestión |
| Liquidación | Supporting | Consumidor aguas abajo de Operación |
| Reputación | Supporting | Consumidor conformista del servicio que publica Gestión |

Gestión del arriendo y Operación del yate son los dos contextos Core. Por eso casi todos los flujos pasan por ellos: la evaluación de riesgo les entrega resultados, y de ellos sale lo que consumen la liquidación y la reputación.

## Cómo se representaron los patrones

ArchiMate no trae estereotipos de patrón DDD. La correspondencia elegida:

| Concepto DDD | Elemento ArchiMate |
| --- | --- |
| Contexto delimitado | `ApplicationComponent` |
| Servicio que el contexto publica | `ApplicationService` |
| Puerta de acceso al servicio | `ApplicationInterface` |
| Datos que viajan entre contextos | `FlowRelationship`, con el patrón como nombre |
| Enlace «servido por» | `ServingRelationship` |
| Núcleo compartido | `Grouping` conectado por `Association` |

Los contextos externos (case, seguros, pasarela de pagos, etc.) son los mismos sistemas que ya se identificaron en la fase 2 y se representan también como `ApplicationComponent` externos.

## Glosario de patrones

- **Cliente-proveedor:** un proveedor publica un servicio y un consumidor lo invoca. Es el patrón por defecto cuando los dos lados autonomía total.
- **Servicio abierto y lenguaje publicado:** el contexto que más consenso genera publica su modelo como contrato público en un lenguaje formal (interfaz de aplicación, en este caso).
- **Conformista:** un consumidor acepta el contrato tal como se lo dan, aun si ese lenguaje no es el que él habría elegido. Cuesta menos que montar su propia versión.
- **Núcleo compartido:** dos contextos comparten físicamente un modelo en común (el arriendo, el yate y el participante, que casi todos consultan). El riesgo es que se acoplen; por eso se declara explícito y se restringe a lo verdaderamente común.
- **Capa anticorrupción (ACL):** una frontera que traduce el lenguaje de un sistema externo al del contexto interno, para que la jerga de afuera no se cuele en el dominio.
- **Contexto delimitado:** qué concepto del dominio pertenece a qué contexto. Un concepto no se comparte; solo se comparte aquello que se publica como contrato.

---
[Volver al índice general](../README.md)