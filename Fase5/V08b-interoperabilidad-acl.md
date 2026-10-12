# V8b. Interoperabilidad de aplicación y anticorrupción

![V8b. Interoperabilidad de aplicación y anticorrupción](../vistas/V08b.%20Interoperabilidad%20de%20aplicaci%C3%B3n%20y%20anticorrupci%C3%B3n.png)

La vista tiene tres columnas: a la izquierda los contextos delimitados de la aplicación de arriendo, al centro la **capa anticorrupción (ACL)**, a la derecha los sistemas y procesos externos. La ACL traduce el lenguaje de cada sistema de afuera al del contexto interno; nada externo llama directo a un contexto, todo pasa por la frontera.

## Los sistemas externos y a qué contexto sirven

| Sistema externo | Interfaz ACL | Contexto que lo consume |
| --- | --- | --- |
| CRM comercial | Interfaz de reserva | Gestión del arriendo |
| Gestión de contratos | Interfaz de reserva | Gestión del arriendo |
| Evaluación de riesgo externa | Interfaz de evaluación de riesgo | Evaluación de riesgo |
| Aseguradora | Interfaz de evaluación de riesgo | Evaluación de riesgo |
| Marinas | Interfaz de operación del yate | Operación y seguimiento del yate |
| Fuente de ubicación y combustible | Interfaz de operación del yate | Operación y seguimiento del yate |
| Pasarela de pagos | Interfaz de liquidación | Liquidación |
| Contabilidad | Interfaz de reputación | Reputación |

Los flujos entran por la interfaz de la ACL (`Flow`), la interfaz sirve a su contexto (`Serving`) y el contexto está agregado a la ACL (`Aggregation`). El sistema externo trae su propio formato; la ACL lo convierte al contrato que el contexto espera, y en sentido inverso cuando el contexto responde.

## Qué traduce la ACL en cada caso

La traducción no es solo de campos. Cada caso cambia forma, nombre o ambos:

- **CRM y Gestión de contratos:** llegan clientes, propietarios y las condiciones pactadas del canal. La ACL los proyecta a los términos del arriendo (cuándo se aparta, cuánto tarda en confirmar).
- **Evaluación de riesgo externa:** entrega puntajes con su propio esquema. La ACL los convierte en el veredicto y la elegibilidad que Evaluación de riesgo necesita.
- **Marinas:** confirman disponibilidad, amarres y la liberación del yate. La ACL convierte esos estados en la operación que el contexto entiende.
- **Fuente de ubicación y combustible:** posiciones y niveles de llenado, que la ACL traduce a hitos de seguimiento (entrega, circulación, devolución).
- **Pasarela de pagos:** intentos y resultados de cobro, traducidos a los saldos de la liquidación.
- **Contabilidad:** los cargos ya liquidados, traducidos a los hechos que alimentan la reputación.

## Notas de diseño

**Por qué una sola ACL en lugar de una por contexto.** Los sistemas externos son pocos y estables, y sus formatos no cambian a cada rato. Una frontera única mantiene la traducción en un solo lugar y a un mismo idioma en toda la aplicación. Si algún día un contexto necesitara su propia puerta, el patrón no cambia: se agrega una interfaz a la ACL.

**Coherencia con fase 2.** Los ocho sistemas de la derecha son los mismos que aparecen en V9. El criterio de exclusión de la fase 2 se mantiene: no se agregó aquí ningún sistema nuevo, y los que quedaron fuera (autoridad marítima, reportes de zona, etc.) siguen fuera porque no participan en los procesos core que consume esta aplicación.

**Qué no hace la ACL.** No contiene reglas de negocio: solo transforma formato y terminología. Ni la evaluación de riesgo ni la liquidación deciden nada dentro de la frontera; la ACL las sirve, no las reemplaza. Por eso todas sus relaciones con los contextos son `Serving` o de agregación, nunca de composición lógica.