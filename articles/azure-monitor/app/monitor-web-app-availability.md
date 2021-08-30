---
title: 'Supervisión de la disponibilidad con pruebas de ping de URL: Azure Monitor'
description: Configure pruebas de ping en Application Insights. Obtenga alertas si un sitio web deja de estar disponible o responde con lentitud.
ms.topic: conceptual
ms.date: 07/13/2021
ms.reviewer: sdash
ms.openlocfilehash: 55e396f7d1af6bc05d722e90d7dad99b2ac8b2d7
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122271648"
---
# <a name="monitor-availability-with-url-ping-tests"></a>Supervisión de la disponibilidad con pruebas de ping de URL

El nombre *prueba de ping de dirección URL* es un poco inapropiado. Estas pruebas no usan el Protocolo de mensajes de control de Internet (ICMP) para comprobar la disponibilidad del sitio. En cambio, usan funcionalidad más avanzada de solicitud HTTP para validar si un punto de conexión responde. Miden el rendimiento asociado a esa respuesta. También ofrecen la posibilidad de establecer criterios de éxito personalizados, junto con características más avanzadas, como analizar solicitudes dependientes y permitir reintentos.

Para crear una prueba de disponibilidad, debe usar un recurso existente de Application Insights o crear un [recurso de Application Insights](create-new-resource.md).

> [!NOTE]
> Las pruebas de ping de dirección URL se clasifican como pruebas clásicas. Puede encontrarlas en **Add Classic Test** (Agregar prueba clásica) en el panel **Disponibilidad**. Para información sobre características más avanzadas, consulte [Pruebas estándar (versión preliminar)](availability-standard-tests.md).
 
## <a name="create-a-test"></a>Creación de una prueba

Para crear el primer grupo de disponibilidad:
1. En el recurso Application Insights, abra el panel **Disponibilidad** y seleccione   **Add Classic Test** (Agregar prueba clásica).

    :::image type="content" source="./media/monitor-web-app-availability/create-test.png" alt-text="Captura de pantalla que muestra el panel Disponibilidad y el botón para agregar una prueba clásica." lightbox ="./media/monitor-web-app-availability/create-test.png":::
1. Asigne un nombre a la prueba y seleccione **URL ping** (Ping de dirección URL) como **SKU**.
1. Escriba la dirección URL que quiere probar.
1. Ajuste la configuración (descrita en la tabla siguiente) a sus necesidades y seleccione **Crear**.

   |Configuración| Explicación |
   |----|----|
   |**URL** |  La dirección URL puede ser cualquier página web que quiera probar, pero debe ser visible desde la red pública de Internet. La dirección URL puede incluir una cadena de consulta. Por ejemplo, se puede ejercitar un poco la base de datos. Si la dirección URL se resuelve en una redirección, podemos seguirla, hasta 10 redirecciones.|
   |**Analizar solicitudes dependientes**| La prueba solicitará imágenes, scripts, archivos de estilo y otros archivos que forman parte de la página web en pruebas. El tiempo de respuesta registrado incluye el tiempo dedicado a obtener estos archivos. La prueba da error si cualquiera de estos recursos no se puede descargar correctamente dentro del tiempo de espera de la prueba entera. Si la opción no está habilitada, la prueba solo solicita el archivo en la dirección URL que especificó. Si se habilita esta opción, se realiza una comprobación más estricta. La prueba podría producir un error en los casos que no se aprecien al examinar manualmente el sitio.
   |**Habilitar reintentos**|Cuando la prueba da error, se reintenta tras un corto intervalo. Se notifica un error únicamente si los tres intentos sucesivos producen un error. Las sucesivas pruebas se realizan según la frecuencia habitual de la prueba. El reintento se suspende temporalmente hasta que uno se complete correctamente. Esta regla se aplica independientemente en cada ubicación de la prueba. *Se recomienda esta opción*. Como media, cerca del 80 % de los errores desaparecen con el reintento.|
   |**Frecuencia de prueba**| Este valor establece la frecuencia con que se ejecuta la prueba desde cada ubicación de prueba. Con una frecuencia predeterminada de cinco minutos y cinco ubicaciones de prueba, el sitio se prueba, por término medio, cada minuto.|
   |**Ubicaciones de prueba**| Los valores de esta configuración son los lugares desde los que los servidores envían solicitudes web a la dirección URL. *Se recomienda un mínimo de cinco ubicaciones de prueba* para asegurarse de que puede distinguir los problemas del sitio web de los problemas de red. Puede seleccionar hasta 16 ubicaciones.

Si la dirección URL no es visible desde la red pública de Internet, tiene la opción de abrir selectivamente el firewall para permitir solo las transacciones de prueba. Para más información sobre las excepciones de firewall para nuestros agentes de prueba de disponibilidad, consulte la [guía de direcciones IP](./ip-addresses.md#availability-tests).

> [!NOTE]
> Se recomienda probar desde varias ubicaciones con un mínimo de cinco ubicaciones. Este enfoque ayuda a evitar falsas alarmas que pueden deberse a problemas transitorios con una ubicación específica. También hemos descubierto que la configuración óptima es que el *número de ubicaciones de prueba sea igual que el umbral de ubicación de la alerta + 2*.

## <a name="success-criteria"></a>Criterios de éxito

|Configuración| Explicación |
|----|----|
| **Tiempo de espera de prueba** |reduzca este valor para recibir una alerta sobre las respuestas lentas. La prueba se considera un error si no se han recibido respuestas de su sitio dentro de este período. Si seleccionó **Analizar solicitudes dependientes**, todas las imágenes, archivos de estilo, scripts y otros recursos dependientes se deben haber recibido durante este período.|
| **Respuesta HTTP** | El código de estado devuelto que se considera correcto. El código que indica que se ha devuelto una página web normal es 200.|
| **Coincidencia de contenido** | Probamos que se produce una coincidencia exacta entre mayúsculas y minúsculas con una cadena en todas las respuestas. Debe ser una cadena sin formato, sin caracteres comodín (como "Welcome!"). No olvide que, si el contenido cambia, es posible que tenga que actualizarla. *La coincidencia de contenido solo admite caracteres en inglés.* |

## <a name="alerts"></a>Alertas

|Configuración| Explicación |
|----|----|
|**Casi en tiempo real (versión preliminar)** | Se recomienda usar alertas que funcionen casi en tiempo real. Este tipo de alerta se configura después de crear la prueba de disponibilidad.  |
|**Umbral de la ubicación de la alerta**| La relación óptima entre el umbral de la ubicación de alerta y el número de ubicaciones de prueba es *umbral de ubicación de alerta = número de ubicaciones de prueba - 2*, con un mínimo de cinco ubicaciones de prueba.|

## <a name="location-population-tags"></a>Etiquetas para rellenar la ubicación

Se pueden usar las siguientes etiquetas de rellenado para el atributo de ubicación geográfica al implementar una prueba de ping de dirección URL de disponibilidad mediante Azure Resource Manager.

### <a name="azure-government"></a>Azure Government

| Nombre para mostrar   | Nombre de rellenado     |
|----------------|---------------------|
| USGov Virginia | usgov-va-azr        |
| USGov: Arizona  | usgov-phx-azr       |
| USGov Texas    | usgov-tx-azr        |
| Departamento de Defensa del este de EE. UU     | usgov-ddeast-azr    |
| Departamento de Defensa de centro de EE. UU.  | usgov-ddcentral-azr |

### <a name="azure-china"></a>Azure China

| Nombre para mostrar   | Nombre de rellenado     |
|----------------|---------------------|
| Este de China     | mc-cne-azr          |
| Este de China 2   | mc-cne2-azr         |
| Norte de China    | mc-cnn-azr          |
| Norte de China 2  | mc-cnn2-azr         |

### <a name="azure"></a>Azure

| Nombre para mostrar                           | Nombre de rellenado   |
|----------------------------------------|-------------------|
| Este de Australia                         | emea-au-syd-edge  |
| Sur de Brasil                           | latam-br-gru-edge |
| Centro de EE. UU.                             | us-fl-mia-edge    |
| Este de Asia                              | apac-hk-hkn-azr   |
| Este de EE. UU.                                | us-va-ash-azr     |
| Sur de Francia (anteriormente Centro de Francia) | emea-ch-zrh-edge  |
| Centro de Francia                         | emea-fr-pra-edge  |
| Japón Oriental                             | apac-jp-kaw-edge  |
| Norte de Europa                           | emea-gb-db3-azr   |
| Centro-Norte de EE. UU                       | us-il-ch1-azr     |
| Centro-sur de EE. UU.                       | us-tx-sn1-azr     |
| Sudeste de Asia                         | apac-sg-sin-azr   |
| Oeste de Reino Unido                                | emea-se-sto-edge  |
| Oeste de Europa                            | emea-nl-ams-azr   |
| Oeste de EE. UU.                                | us-ca-sjc-azr     |
| Sur de Reino Unido                               | emea-ru-msa-edge  |

## <a name="see-your-availability-test-results"></a>Visualización de los resultados de las pruebas de disponibilidad

Puede visualizar los resultados de la prueba de disponibilidad con vistas de líneas y gráficos de dispersión.

Al cabo de unos minutos, seleccione **Actualizar** para ver los resultados de la prueba.

:::image type="content" source="./media/monitor-web-app-availability/refresh.png" alt-text="Captura de pantalla que muestra la página Disponibilidad con el botón Actualizar resaltado." lightbox="./media/monitor-web-app-availability/refresh.png":::

En la vista de dispersión se muestran ejemplos de los resultados de las pruebas, que incluyen detalles sobre los pasos de las pruebas de diagnóstico. El motor de pruebas almacena los detalles de diagnóstico de las pruebas que tienen errores. En el caso de las pruebas correctas, los detalles de diagnóstico se almacenan para un subconjunto de las ejecuciones. Mantenga el mouse sobre cualquiera de los puntos rojos o verdes para ver el nombre de la prueba y su ubicación.

:::image type="content" source="./media/monitor-web-app-availability/availability-scatter-plot-003.png" alt-text="Captura de pantalla que muestra la vista de líneas." border="false":::

Seleccione una prueba o una ubicación determinada, o bien reduzca el período de tiempo para ver más resultados del período de tiempo que le interese. Use el Explorador de búsqueda para ver los resultados de todas las ejecuciones, o use consultas de análisis para ejecutar informes personalizados en estos datos.

## <a name="inspect-and-edit-tests"></a>Inspección y edición de pruebas

Para editar, eliminar o deshabilitar temporalmente una prueba, seleccione los puntos suspensivos ( **...** ) junto al nombre de la prueba. Los cambios en la configuración pueden tardar hasta 20 minutos en propagarse a todos los agentes de prueba.

:::image type="content" source="./media/monitor-web-app-availability/edit.png" alt-text="Captura de pantalla que muestra los detalles de la prueba, con opciones para editar y deshabilitar una prueba." border="false":::

Es posible que quiera deshabilitar las pruebas de disponibilidad o las reglas de alerta asociadas a ellas mientras realiza el mantenimiento del servicio.

## <a name="actions-if-you-see-failures"></a>Acciones si observa errores

Seleccione un punto rojo.

:::image type="content" source="./media/monitor-web-app-availability/end-to-end.png" alt-text="Captura de pantalla de la pestaña de detalles de una transacción completa." border="false":::

Puede ver los detalles de transacción en todos los componentes desde el resultado de la prueba de disponibilidad. Luego, puede:

* Revisar el informe de solución de problemas para determinar qué puede haber provocado que la prueba fracase, aunque la aplicación siga estando disponible.
* Inspeccionar la respuesta recibida desde el servidor.
* Diagnosticar errores con la telemetría de lado servidor correlacionada que se recopiló durante el procesamiento de la prueba de disponibilidad errónea.
* Registrar un problema o elemento de trabajo en GIT o Azure Boards para realizar un seguimiento del problema. El error contiene un vínculo a este evento.
* Abra el resultado de la prueba web en Visual Studio.

Para más información sobre el diagnóstico completo de transacciones, consulte la [documentación sobre el diagnóstico de transacciones](./transaction-diagnostics.md).

Seleccione la fila de excepciones para ver los detalles de la excepción del lado servidor que ha provocado un error en la prueba de disponibilidad sintética. También puede obtener la [instantánea de depuración](./snapshot-debugger.md) para realizar diagnósticos de nivel de código más completos.

:::image type="content" source="./media/monitor-web-app-availability/open-instance-4.png" alt-text="Captura de pantalla que muestra los diagnósticos del lado servidor.":::

Además de los resultados sin formato, también puede ver dos métricas de disponibilidad principales en el [Explorador de métricas](../essentials/metrics-getting-started.md):

- **Disponibilidad**: porcentaje de las pruebas que obtuvieron resultados satisfactorios en todas las ejecuciones.
- **Duración de la prueba**: duración media de las pruebas en todas las ejecuciones.

## <a name="automation"></a>Automation

* [Use scripts de PowerShell para configurar una prueba de disponibilidad](./powershell.md#add-an-availability-test) automáticamente.
* Configure un [webhook](../alerts/alerts-webhooks.md) que se llame cuando se genere una alerta.


## <a name="next-steps"></a>Pasos siguientes

* [Alertas de disponibilidad](availability-alerts.md)
* [Pruebas web de varios pasos](availability-multistep.md)
* [Solución de problemas](troubleshoot-availability.md)
* [Plantilla de Azure Resource Manager para pruebas web](/azure/templates/microsoft.insights/webtests?tabs=json)
