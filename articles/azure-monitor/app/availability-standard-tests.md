---
title: 'Prueba estándar de disponibilidad: Azure Monitor Application Insights'
description: Configure pruebas estándar en Application Insights para comprobar la disponibilidad de un sitio web con una sola prueba de solicitud.
ms.topic: conceptual
ms.date: 07/13/2021
ms.openlocfilehash: 2a8741d874fdfad8a76465fd8127dfb4752a1a55
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113799486"
---
# <a name="standard-test"></a>Prueba estándar

Las pruebas estándar son una prueba de solicitud única parecida a la [prueba de ping de URL](monitor-web-app-availability.md), pero más avanzadas. Además de validar si un punto de conexión responde y mide el rendimiento, las pruebas estándar también incluyen la validez del certificado SSL, la comprobación proactiva de la duración, el verbo de solicitud HTTP (por ejemplo, `GET`, `HEAD`, `POST`, etc.), los encabezados personalizados y los datos personalizados asociados a la solicitud HTTP.

> [!NOTE]
> Las pruebas estándar están actualmente en versión preliminar pública. Estas versiones preliminares se proporcionan sin contrato de nivel de servicio. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.

> [!NOTE]
> Actualmente no se cobran cargos adicionales por la característica en versión preliminar de pruebas estándar. Los precios de las características en versión preliminar se anunciarán en el futuro y se avisará antes del inicio de la facturación. Si decide seguir usando pruebas estándar después del período de aviso, se le facturará según la tarifa aplicable.

Para crear una prueba de disponibilidad, debe usar un recurso existente de Application Insights o crear un [recurso de Application Insights](create-new-resource.md).

> [!TIP]
> Si actualmente usa otras pruebas de disponibilidad, como las pruebas de ping de URL, puede agregar pruebas estándar junto con las demás. Si quiere usar pruebas estándar en lugar de otras pruebas, agregue una prueba estándar y elimine la prueba anterior. 

## <a name="create-a-standard-test"></a>Creación de una prueba estándar

Para crear una prueba estándar: 

1. Vaya al recurso Application Insights y seleccione el panel **Disponibilidad**.
1. Seleccione **Add Standard (preview) test** (Agregar prueba estándar [versión preliminar]).
    
    :::image type="content" source="./media/availability-standard-test/standard-test.png" alt-text="Captura de pantalla del panel Disponibilidad con la pestaña Add Standard test (Agregar prueba estándar) abierta." lightbox="./media/availability-standard-test/standard-test.png":::

1. Escriba el nombre de la prueba, la dirección URL y valores de configuración adicionales (se explican a continuación) y, luego, seleccione **Crear**.


|Configuración | Explicación |
|--------|-------------|
|**URL** |  La dirección URL puede ser cualquier página web que desee probar, pero debe ser visible desde la red pública de Internet. La dirección URL puede incluir una cadena de consulta. Así, por ejemplo, se puede ejercitar un poco la base de datos. Si la dirección URL se resuelve en una redirección, la seguimos, hasta 10 redirecciones.|
|**Analizar solicitudes dependientes**| La prueba solicitará imágenes, scripts, archivos de estilo y otros archivos que forman parte de la página web en pruebas. El tiempo de respuesta registrado incluye el tiempo dedicado a obtener estos archivos. La prueba da error si cualquiera de estos recursos no se puede descargar correctamente dentro del tiempo de espera de la prueba entera. Si la opción no está activada, la prueba solo solicita el archivo en la dirección URL que especificó. Si se habilita esta opción, se realiza una comprobación más estricta. Podría producirse un error en la prueba para los casos que puede que no sean evidentes al examinar el sitio de forma manual. |
|**Habilitar reintentos**| Cuando la prueba da error, se reintenta tras un corto intervalo. Se notifica un error únicamente si los tres intentos sucesivos producen un error. Las sucesivas pruebas se realizan según la frecuencia habitual de la prueba. El reintento se suspende temporalmente hasta que uno se complete correctamente. Esta regla se aplica independientemente en cada ubicación de la prueba. **Se recomienda esta opción**. Como media, cerca del 80 % de los errores desaparecen al reintentar.|
| **SSL certificate validation test** (Prueba de validación del certificado SSL) | Puede comprobar el certificado SSL de su sitio web para asegurarse de que está instalado correctamente, es válido, es de confianza y no da ningún error a ninguno de los usuarios. |
| **Proactive lifetime check** (Comprobación proactiva de la duración) | Le permite definir un período de tiempo establecido antes de que expire el certificado SSL. Una vez que expire, se producirá un error en la prueba. |
|**Frecuencia de prueba**| establece la frecuencia con que se ejecuta la prueba desde cada ubicación de prueba. Con una frecuencia predeterminada de cinco minutos y cinco ubicaciones de prueba, el sitio se prueba, de media, cada minuto.|
|**Ubicaciones de prueba**|  Son los lugares desde donde nuestros servidores envían solicitudes web a la dirección URL. **El número mínimo de ubicaciones de prueba recomendado es cinco** para garantizar que puede distinguir los problemas del sitio web de los problemas de la red. Puede seleccionar hasta 16 ubicaciones.|
| **Encabezados personalizados** | Pares clave-valor que definen los parámetros operativos. |
| **HTTP request verb** (Verbo de la solicitud HTTP) | Indique qué acción le gustaría realizar con la solicitud. |
| **Cuerpo de la solicitud** | Datos personalizados asociados a la solicitud HTTP. Puede cargar sus propios archivos, escribir el contenido o deshabilitar esta característica. |

## <a name="success-criteria"></a>Criterios de éxito

|Configuración| Explicación|
|-------|------------|
| **Tiempo de espera de prueba** |reduzca este valor para recibir una alerta sobre las respuestas lentas. La prueba se considera un error si no se han recibido respuestas de su sitio dentro de este período. Si seleccionó **Analizar solicitudes dependientes**, todas las imágenes, archivos de estilo, scripts y otros recursos dependientes se deben haber recibido durante este período.|
| **Respuesta HTTP** | el código de estado devuelto que se considera correcto. 200 es el código que indica que se ha devuelto una página web normal.|
| **Coincidencia de contenido** | Una cadena, como "Bienvenido". Probamos que se produce una coincidencia exacta entre mayúsculas y minúsculas en todas las respuestas. Debe ser una cadena sin formato, sin caracteres comodín. No se olvide de que si el contenido cambia, es posible que tenga que actualizarla. **En la coincidencia de contenido solo se admiten caracteres en inglés** |

## <a name="alerts"></a>Alertas

|Configuración| Explicación|
|-------|------------|
|**Casi en tiempo real (versión preliminar)** | Se recomienda usar alertas casi en tiempo real. La configuración de este tipo de alertas se realiza después de crear la prueba de disponibilidad.  |
|**Umbral de la ubicación de la alerta**|se recomienda un mínimo de 3/5 ubicaciones. La relación óptima entre el umbral de ubicación de la alerta y el número de ubicaciones de prueba es **umbral de ubicación de la alerta** = **número de ubicaciones de prueba - 2, con un mínimo de cinco ubicaciones de prueba.**|

## <a name="location-population-tags"></a>Etiquetas para rellenar la ubicación

Se pueden usar las siguientes etiquetas para rellenar el atributo de ubicación geográfica al implementar una prueba de ping de la dirección URL para averiguar la disponibilidad mediante Azure Resource Manager.

### <a name="azure-gov"></a>Azure Gov

| Display Name (Nombre para mostrar)   | Nombre de rellenado     |
|----------------|---------------------|
| USGov Virginia | usgov-va-azr        |
| USGov: Arizona  | usgov-phx-azr       |
| USGov Texas    | usgov-tx-azr        |
| Departamento de Defensa del este de EE. UU     | usgov-ddeast-azr    |
| Departamento de Defensa de centro de EE. UU.  | usgov-ddcentral-azr |

### <a name="azure-china"></a>Azure China

| Display Name (Nombre para mostrar)   | Nombre de rellenado     |
|----------------|---------------------|
| Este de China     | mc-cne-azr          |
| Este de China 2   | mc-cne2-azr         |
| Norte de China    | mc-cnn-azr          |
| Norte de China 2  | mc-cnn2-azr         |

#### <a name="azure"></a>Azure

| Display Name (Nombre para mostrar)                           | Nombre de rellenado   |
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

Los resultados de la prueba de disponibilidad se pueden visualizar con vistas de línea o vistas de trazado de dispersión.

Al cabo de unos minutos, seleccione **Actualizar** para ver los resultados de la prueba.

:::image type="content" source="./media/monitor-web-app-availability/availability-refresh-002.png" alt-text="Captura de pantalla que muestra la página Disponibilidad con el botón Actualizar resaltado.":::

En la vista de dispersión se muestran ejemplos de los resultados de las pruebas, que incluyen detalles sobre los pasos de las pruebas de diagnóstico. El motor de pruebas almacena los detalles de diagnóstico de las pruebas con errores. En el caso de las pruebas correctas, los detalles de diagnóstico se almacenan para un subconjunto de las ejecuciones. Mantenga el mouse sobre cualquiera de los puntos rojos o verdes para ver la prueba, el nombre de esta y su ubicación.

:::image type="content" source="./media/monitor-web-app-availability/availability-scatter-plot-003.png" alt-text="Vista de línea" border="false":::

Seleccione una prueba o una ubicación determinadas, o bien reduzca el período de tiempo para ver más resultados del período de tiempo que le interese. Use el Explorador de búsqueda para ver los resultados de todas las ejecuciones, o use consultas de Analytics para ejecutar informes personalizados en estos datos.

## <a name="inspect-and-edit-tests"></a>Inspección y edición de pruebas

Para editar, deshabilitar temporalmente o eliminar una prueba, seleccione los puntos suspensivos junto al nombre de la prueba. Pueden tardar hasta 20 minutos para que los cambios se propaguen a todos los agentes de prueba después de realizar un cambio.

:::image type="content" source="./media/monitor-web-app-availability/edit.png" alt-text="Vea los detalles de la prueba. Edite y deshabilitar una prueba web." border="false":::

Tal vez le interese deshabilitar las pruebas de disponibilidad o las reglas de alerta asociadas a ellas mientras esté realizando el mantenimiento del servicio.

## <a name="if-you-see-failures"></a>Si ve errores

Seleccione un punto rojo.

:::image type="content" source="./media/monitor-web-app-availability/end-to-end.png" alt-text="Captura de pantalla de la pestaña de detalles de una transacción completa." border="false":::

Puede ver los detalles de transacción en todos los componentes desde el resultado de la prueba de disponibilidad. Aquí puede:

* Revise el informe de solución de problemas para determinar qué puede haber provocado que la prueba fallase, pero la aplicación siga estando disponible.
* Inspeccionar la respuesta recibida desde el servidor.
* Diagnosticar errores con la telemetría de lado servidor correlacionada que se recopiló durante el procesamiento de la prueba de disponibilidad con error.
* Registrar un problema o elemento de trabajo en GIT o Azure Boards para realizar un seguimiento del problema. El error contiene un vínculo a este evento.
* Abra el resultado de la prueba web en Visual Studio.

Para más información sobre la experiencia completa de diagnóstico de transacciones, consulte la [documentación sobre el diagnóstico de transacciones](./transaction-diagnostics.md).

Seleccione la fila de excepciones para ver los detalles de la excepción del lado servidor que ha provocado un error en la prueba de disponibilidad sintética. También puede obtener la [instantánea de depuración](./snapshot-debugger.md) para realizar diagnósticos de nivel de código más completos.

:::image type="content" source="./media/monitor-web-app-availability/open-instance-4.png" alt-text="Diagnósticos del servidor":::

Además de los resultados sin formato, también puede ver dos métricas de disponibilidad clave en el [Explorador de métricas](../essentials/metrics-getting-started.md):

* Disponibilidad: Porcentaje de las pruebas que obtuvieron resultados satisfactorios en todas las ejecuciones de prueba.
* Duración de la prueba: Duración media de las pruebas en todas las ejecuciones de prueba.

## <a name="next-steps"></a>Pasos siguientes

* [Alertas de disponibilidad](availability-alerts.md)
* [Pruebas web de varios pasos](availability-multistep.md)
* [Solución de problemas](troubleshoot-availability.md)
* [Plantilla de Azure Resource Manager para pruebas web](/azure/templates/microsoft.insights/webtests?tabs=json)
