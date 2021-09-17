---
title: Configuración de la recopilación de datos para el agente de Azure Monitor (versión preliminar)
description: Se describe cómo crear una regla de recopilación de datos para recopilar datos de máquinas virtuales mediante el agente de Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/16/2021
ms.openlocfilehash: 81c82152bf87944c7aed191c12e067567ec2b086
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122662250"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent"></a>Configuración de la recopilación de datos para el agente de Azure Monitor

Las reglas de recopilación de datos (DCR) definen los datos que entran en Azure Monitor y especifican dónde se deben enviar. En este artículo se describe cómo crear una regla de recopilación de datos para recopilar datos de máquinas virtuales mediante el agente de Azure Monitor.

Para una descripción completa de las reglas de recopilación de datos, consulte [Reglas de recopilación de datos en Azure Monitor](data-collection-rule-overview.md).

> [!NOTE]
> En este artículo se describe cómo configurar los datos de las máquinas virtuales solo con el agente de Azure Monitor.

## <a name="data-collection-rule-associations"></a>Asociaciones de reglas de recopilación de datos

Para aplicar una reglas de recopilación de datos a una máquina virtual, cree una asociación para la máquina virtual. Una máquina virtual puede tener una asociación a varias reglas de recopilación de datos y una de estas reglas puede tener varias máquinas virtuales asociadas. Esto le permite definir un conjunto de reglas de recopilación de datos, cada una de las cuales coincide con un requisito determinado y aplicarlas solo a las máquinas virtuales en las que se aplican. 

Por ejemplo, imagine un entorno con un conjunto de máquinas virtuales que ejecutan una aplicación de línea de negocio y otras que se ejecutan SQL Server. Es posible que tenga una regla de recopilación de datos predeterminada que se aplique a todas las máquinas virtuales y a reglas de recopilación de datos independientes que recopilen datos específicamente para la aplicación de línea de negocio y SQL Server. Las asociaciones de las máquinas virtuales con las reglas de recopilación de datos tendrían un aspecto similar al siguiente diagrama.

![En el diagrama se muestran las máquinas virtuales que hospedan la aplicación de línea de negocio y SQL Server asociadas a las reglas de recopilación de datos denominadas central-i t-default y lob-app para la aplicación de línea de negocio y central-i t-default y s q l para SQL Server.](media/data-collection-rule-azure-monitor-agent/associations.png)



## <a name="create-rule-and-association-in-azure-portal"></a>Creación de reglas y asociaciones en Azure Portal

Puede usar Azure Portal para crear una regla de recopilación de datos y asociar las máquinas virtuales de su suscripción a esa regla. El agente de Azure Monitor se instalará automáticamente y se creará una identidad administrada para las máquinas virtuales que no lo tengan instalado.

> [!IMPORTANT]
> La creación de una regla de recopilación de datos mediante el portal también permite una identidad administrada asignada por el sistema en los recursos de destino, además de las identidades asignadas por el usuario (si las hay). En el caso de las aplicaciones existentes, y a menos que se especifique la identidad asignada por el usuario en la solicitud, la máquina usará de manera predeterminada la identidad asignada por el sistema. [Más información](../../active-directory/managed-identities-azure-resources/managed-identities-faq.md#what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request)

                    

> [!NOTE]
> Si desea enviar datos a Log Analytics, debe crear la regla de recopilación de datos en la **misma región** donde reside el área de trabajo de Log Analytics. La regla se puede asociar a máquinas de otras regiones admitidas.

En el menú **Azure Monitor** de Azure Portal, seleccione las **reglas de recopilación de datos** en la sección **Configuración**. Haga clic en **Crear** para crear una nueva regla y asignación de recopilación de datos.

[![Reglas de recopilación de datos](media/data-collection-rule-azure-monitor-agent/data-collection-rules-updated.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rules-updated.png#lightbox)

Haga clic en **Agregar** para crear una nueva regla y un conjunto de asociaciones. Dé un **nombre de regla** y especifique una **suscripción**, un **grupo de recursos** y una **región**. Esto especifica dónde se creará la regla de recopilación de datos. Las máquinas virtuales y sus asociaciones pueden estar en cualquier suscripción o grupo de recursos del inquilino.
Además, elija el **tipo de plataforma** adecuado, el cual permite especificar el tipo de recursos a los que se puede aplicar esta regla. La opción personalizada permitirá tipos de Windows y Linux. Esto permite experiencias de creación previamente seleccionadas con opciones con ámbito en el tipo de plataforma seleccionado.

[![Nombre de la regla de recopilación de datos](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics-updated.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics-updated.png#lightbox)

En la pestaña **Recursos**, agregue los recursos (máquinas virtuales, conjuntos de escalado de máquinas virtuales, Arc para servidores) que deben tener aplicada la regla de recopilación de datos. El agente de Azure Monitor se instalará en los recursos que aún no lo tengan instalado y también habilitará la identidad administrada de Azure.

[![Máquinas virtuales de reglas de recopilación de datos](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines-updated.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines-updated.png#lightbox)

En la pestaña **Collect and deliver** (Recopilar y entregar), haga clic en **Add data source** (Agregar origen de datos) para agregar un origen de datos y un conjunto de destino. Seleccione un **tipo de origen de datos** y se mostrarán los detalles correspondientes. Para los contadores de rendimiento, puede seleccionar entre un conjunto predefinido de objetos y su frecuencia de muestreo. En el caso de los eventos, puede seleccionar entre un conjunto de registros o instalaciones y el nivel de gravedad. 

[![Origen de datos básico](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic-updated.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic-updated.png#lightbox)


Para especificar otros registros y contadores de rendimiento de los [orígenes de datos admitidos actualmente](azure-monitor-agent-overview.md#data-sources-and-destinations) o para filtrar eventos mediante consultas XPath, seleccione **Custom** (Personalizado). Después, puede especificar un [XPath](https://www.w3schools.com/xml/xpath_syntax.asp) para los valores específicos que se van a recopilar. Consulte la [regla de recopilación de datos de ejemplo](data-collection-rule-overview.md#sample-data-collection-rule) para ver ejemplos.

[![Origen de datos personalizado](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom-updated.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom-updated.png#lightbox)

En la pestaña **Destination** (Destino), agregue uno o varios destinos para el origen de datos. Los orígenes de datos de eventos de Windows y Syslog solo pueden enviar a registros de Azure Monitor. Los contadores de rendimiento pueden enviar a métricas de Azure Monitor y registros de Azure Monitor.

[![Destination](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png#lightbox)

Haga clic en **Agregar origen de datos** (Agregar origen de datos) y, después, en **Revisar y crear** para revisar los detalles de la regla de recopilación de datos y la asociación con el conjunto de máquinas virtuales. Haga clic en **Crear** para crearlo.

> [!NOTE]
> Una vez creadas las asociaciones y la regla de recopilación de datos, los datos pueden tardar hasta cinco minutos en enviarse a los destinos.

## <a name="limit-data-collection-with-custom-xpath-queries"></a>Limitar la recopilación de datos con consultas XPath personalizadas
Dado que se le cobran los datos recopilados en un área de trabajo Log Analytics, debe recopilar solo los datos que necesite. Con la configuración básica en el Azure Portal, solo tiene una capacidad limitada para filtrar los eventos que se van a recopilar. En el caso de los registros del sistema y de la aplicación, se trata de todos los registros con una gravedad determinada. En el caso de los registros de seguridad, se trata de todos los registros de auditoría correcta o error de auditoría.

Para especificar filtros adicionales, debe usar la configuración personalizada y especificar un XPath que filtre los eventos que no tiene. Las entradas XPath se escriben en el formulario `LogName!XPathQuery`. Por ejemplo, puede que desee devolver solo los eventos del registro de eventos de aplicación con el identificador de evento 1035. El XPathQuery para estos eventos sería `*[System[EventID=1035]]`. Dado que desea recuperar los eventos del registro de eventos de la aplicación, el XPath sería `Application!*[System[EventID=1035]]`

Vea [limitaciones de XPath 1.0](/windows/win32/wes/consuming-events#xpath-10-limitations) para obtener una lista de las limitaciones de XPath que admite el registro de eventos de Windows.

> [!TIP]
> Use el cmdlet de PowerShell `Get-WinEvent` con el `FilterXPath` parámetro para probar la validez de un XPathQuery. El script siguiente muestra un ejemplo de la edición.
> 
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - **En el cmdlet anterior, el valor del parámetro "-LogName" es la parte inicial de la consulta XPath hasta "!", mientras que solo el resto de la consulta XPath entra en el parámetro $XPath.**
> - Si se devuelven eventos, la consulta es válida.
> - Si recibe el mensaje *No se encontraron eventos que coincidan con los criterios de selección especificados.* , la consulta puede ser válida, pero no hay eventos coincidentes en el equipo local.
> - Si recibe el mensaje *La consulta especificada no es válida*, la sintaxis de la consulta no es válida. 

En la tabla siguiente se muestran ejemplos de filtrado de eventos mediante un XPath personalizado.

| Descripción |  XPath |
|:---|:---|
| Recopilar solo eventos del Sistema con id. de Evento = 4648 |  `System!*[System[EventID=4648]]`
| Recopilar solo eventos del Sistema con id. de evento = 4648 y un nombre de proceso de consent.exe | `Security!*[System[(EventID=4648)]] and *[EventData[Data[@Name='ProcessName']='C:\Windows\System32\consent.exe']]` |
| Recopilar todos los eventos críticos, de error, de advertencia y de información del registro de eventos del Sistema, excepto el id. de evento = 6 (controlador cargado) |  `System!*[System[(Level=1 or Level=2 or Level=3) and (EventID != 6)]]` |
| Recopilación de todos los eventos de seguridad correctos y erróneos excepto el id. de evento 4624 (inicio de sesión correcto) |  `Security!*[System[(band(Keywords,13510798882111488)) and (EventID != 4624)]]` |


## <a name="create-rule-and-association-using-rest-api"></a>Creación de reglas y asociaciones mediante una API REST

Siga los pasos que se indican a continuación para crear una regla de recopilación de datos y asociaciones mediante la API REST.

> [!NOTE]
> Si desea enviar datos a Log Analytics, debe crear la regla de recopilación de datos en la **misma región** donde reside el área de trabajo de Log Analytics. La regla se puede asociar a máquinas de otras regiones admitidas.

1. Cree manualmente el archivo de regla de recopilación de datos con el formato JSON que se muestra en [DCR de ejemplo](data-collection-rule-overview.md#sample-data-collection-rule).

2. Cree la regla con la [API REST](/rest/api/monitor/datacollectionrules/create#examples).

3. Cree una asociación para cada máquina virtual a la regla de recopilación de datos mediante la [API REST](/rest/api/monitor/datacollectionruleassociations/create#examples).


## <a name="create-rule-and-association-using-resource-manager-template"></a>Creación de una regla y asociación mediante plantillas de Resource Manager

> [!NOTE]
> Si desea enviar datos a Log Analytics, debe crear la regla de recopilación de datos en la **misma región** donde reside el área de trabajo de Log Analytics. La regla se puede asociar a máquinas de otras regiones admitidas.

Puede crear una regla y una asociación para una máquina virtual de Azure o un servidor habilitado para Azure Arc mediante plantillas de Resource Manager. Consulte [Ejemplos de plantillas de Resource Manager para reglas de recopilación de datos en Azure Monitor](./resource-manager-data-collection-rules.md) para obtener plantillas de ejemplo.


## <a name="manage-rules-and-association-using-powershell"></a>Administración de reglas y asociación mediante PowerShell

> [!NOTE]
> Si desea enviar datos a Log Analytics, debe crear la regla de recopilación de datos en la **misma región** donde reside el área de trabajo de Log Analytics. La regla se puede asociar a máquinas de otras regiones admitidas.

**Reglas de recopilación de datos**

| Acción | Comando |
|:---|:---|
| Obtiene reglas | [Get-AzDataCollectionRule](/powershell/module/az.monitor/get-azdatacollectionrule?view=azps-5.4.0&preserve-view=true) |
| Crear una regla | [New-AzDataCollectionRule](/powershell/module/az.monitor/new-azdatacollectionrule?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |
| Actualiza una regla | [Set-AzDataCollectionRule](/powershell/module/az.monitor/set-azdatacollectionrule?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |
| Eliminar una regla | [Remove-AzDataCollectionRule](/powershell/module/az.monitor/remove-azdatacollectionrule?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |
| Actualiza "Etiquetas" para una regla | [Update-AzDataCollectionRule](/powershell/module/az.monitor/update-azdatacollectionrule?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |

**Asociaciones de reglas de recopilación de datos**

| Acción | Comando |
|:---|:---|
| Obtiene asociaciones | [Get-AzDataCollectionRuleAssociation](/powershell/module/az.monitor/get-azdatacollectionruleassociation?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |
| Creación de una asociación | [New-AzDataCollectionRuleAssociation](/powershell/module/az.monitor/new-azdatacollectionruleassociation?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |
| Eliminación de una asociación | [Remove-AzDataCollectionRuleAssociation](/powershell/module/az.monitor/remove-azdatacollectionruleassociation?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |



## <a name="manage-rules-and-association-using-azure-cli"></a>Administra reglas y asociaciones mediante la CLI de Azure

> [!NOTE]
> Si desea enviar datos a Log Analytics, debe crear la regla de recopilación de datos en la **misma región** donde reside el área de trabajo de Log Analytics. La regla se puede asociar a máquinas de otras regiones admitidas.

Esto está habilitado como parte de la extensión **monitor-control-service** de la CLI de Azure. [Ver todos los comandos](/cli/azure/monitor/data-collection/rule?view=azure-cli-latest&preserve-view=true)


## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el [agente de Azure Monitor](azure-monitor-agent-overview.md).
- Más información sobre las [reglas de recopilación de datos](data-collection-rule-overview.md).
