# V6b. Evaluación de riesgo

![V6b. Agregados, comandos y eventos - Evaluación de riesgo](../vistas/V06b.%20Agregados,%20comandos%20y%20eventos%20-%20Evaluaci%C3%B3n%20de%20riesgo.png)

Dominio **Supporting**. Decide si un arriendo puede continuar y bajo qué condiciones. La vista muestra dos agregados: la evaluación propiamente dicha y el expediente del cliente con lo que se reutiliza entre arriendos.

## Agregados

| Raíz de agregado | Entidades y objetos de valor | Invariante principal |
| --- | --- | --- |
| **Evaluación de riesgo** | Verificación (entidad), Resultado de riesgo (valor), Condición especial (valor) | Ningún resultado es «apto» si falta un requisito obligatorio. La recomendación externa informa, pero no decide por sí sola. |
| **Expediente del cliente** | Requisito del cliente (entidad), Habilitación del cliente (valor), Antecedente (valor) | Solo guarda lo relevante para la elegibilidad; los datos comerciales viven en el CRM. Una habilitación vencida no permite aprobar. |

Evaluación de riesgo y Requisito del cliente ya existían. El expediente del cliente es nuevo: en V5 los datos del cliente aparecían sueltos, pero comparten una misma regla de vigencia y conviene tratarlos como un agregado con raíz propia.

## Comandos y eventos

| Comando | Agregado | Evento |
| --- | --- | --- |
| Evaluar solicitud | Evaluación de riesgo | Evaluación de riesgo iniciada *(dominio)* |
| Registrar verificación | Evaluación de riesgo | Verificación registrada *(dominio)* |
| Solicitar evaluación externa | Evaluación de riesgo | Evaluación externa solicitada *(dominio)* |
| Registrar recomendación externa | Evaluación de riesgo | Recomendación externa recibida *(dominio)* |
| Aprobar elegibilidad | Evaluación de riesgo | Elegibilidad aprobada *(negocio)* |
| Rechazar elegibilidad | Evaluación de riesgo | Elegibilidad rechazada *(dominio)* |
| Imponer condición especial | Evaluación de riesgo | Condición especial impuesta *(dominio)* |
| Actualizar habilitaciones | Expediente del cliente | Habilitaciones actualizadas *(dominio)* |

## Notas de diseño

Se guardó la recomendación externa como un insumo más y no como la decisión final. Eso responde a una distinción de la fase 3: el evaluador externo aporta criterio, pero el negocio conserva la decisión. Por eso la recomendación externa genera un evento de dominio y la aprobación, un evento de negocio.

La aprobación de elegibilidad se modeló como evento de negocio porque es la señal que habilita la confirmación de la reserva en Gestión del arriendo. Es uno de los pocos puntos donde un dominio Core depende de un resultado Supporting, y se quiso dejar explícito.

Imponer una condición especial no la vuelve un requisito obligatorio general: es una regla para ese arriendo concreto. Por eso la condición especial es un objeto de valor dentro de la evaluación y no una propiedad del expediente del cliente.
