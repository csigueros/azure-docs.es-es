---
title: Implementación y administración del Análisis de tráfico mediante Azure Policy
titleSuffix: Azure Network Watcher
description: En este artículo se explica cómo usar las directivas integradas para administrar la implementación del Análisis de tráfico.
services: network-watcher
documentationcenter: na
author: moagra
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2021
ms.author: moagra
ms.openlocfilehash: 3be6d5b30d270c9687b7100c07ee675268cee5c0
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122609153"
---
# <a name="deploy-and-manage-traffic-analytics-using-azure-policy"></a>Implementación y administración del Análisis de tráfico mediante Azure Policy 

Azure Policy ayuda a aplicar los estándares de la organización y a evaluar el cumplimiento a escala. Entre los casos de uso comunes de Azure Policy se incluye la implementación de la gobernanza para la coherencia de los recursos, el cumplimiento normativo, la seguridad, el costo y la administración. En este artículo, veremos tres directivas integradas disponibles en el [Análisis de tráfico](./traffic-analytics.md) para administrar la configuración.

Si va a crear una definición de Azure Policy por primera vez, puede leer lo siguiente: 
- [Introducción a Azure Policy](../governance/policy/overview.md). 
- [Tutorial para crear una asignación de Azure Policy](../governance/policy/assign-policy-portal.md#create-a-policy-assignment).


## <a name="locate-the-policies"></a>Búsqueda de las directivas
1. Vaya Azure Portal en [portal.azure.com](https://portal.azure.com) 

Acceda a la página de Azure Policy; para ello, busque la palabra "Directiva" en la barra de búsqueda ![Policy Home Page](./media/network-watcher-builtin-policy/1_policy-search.png) (Página principal de la directiva) de la parte superior.

2. Vaya a la pestaña **Asignaciones** del panel izquierdo.

![Ficha de asignaciones](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. Haga clic en botón **Asignar directiva**. 

![Botón para asignar la directiva](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. Haga clic en el menú de tres puntos en "Definiciones de la directiva" para ver las directivas disponibles.

5. Use el filtro de tipo y elija "Integrado". Después, busque "análisis de tráfico".

Debería ver las tres directivas integradas. ![Lista de directivas para el análisis de tráfico](./media/traffic-analytics/policy-filtered-view.png)

6. Elija la directiva que quiera asignar.

- *"Network Watcher flow logs should have traffic analytics enabled"* (Los registros de flujo de Network Watcher deben tener habilitado el análisis de tráfico) es la directiva de auditoría que marca los registros de flujo no compatibles, es decir, aquellos que no tienen el análisis de tráfico habilitado.
- *"Configure network security groups to use specific workspace for traffic analytics"* (Configurar grupos de seguridad de red para usar un área de trabajo específica para el análisis de tráfico) y *"Configure network security groups to enable Traffic Analytics"* (Configurar grupos de seguridad de red para habilitar el Análisis de tráfico) son las directivas con una acción de implementación. Habilitan el análisis de tráfico en todos los NSG, para lo que podrían sobrescribir (o no) valores ya configurados en función de la directiva habilitada.

A continuación encontrará instrucciones independientes para cada directiva.  

## <a name="audit-policy"></a>Directiva de auditoría 

### <a name="network-watcher-flow-logs-should-have-traffic-analytics-enabled"></a>Los registros de flujo de Network Watcher deben tener habilitado el análisis de tráfico

La directiva audita todos los objetos de Azure Resource Manager existentes de tipo "Microsoft.Network/networkWatchers/flowLogs" y comprueba si el Análisis de tráfico está habilitado mediante la propiedad "networkWatcherFlowAnalyticsConfiguration.enabled" del recurso de registros de flujo. Marca el recurso de registros de flujo que tenga la propiedad establecida en false.

Si quiere ver la definición completa de la directiva, vaya a la pestaña [Definiciones](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) y busque "análisis de tráfico" para consultar la directiva.

### <a name="assignment"></a>Asignación

1. Rellene los detalles de la directiva.

- Ámbito: puede ser una suscripción o un grupo de recursos. En este último caso, seleccione el grupo de recursos que contenga el recurso de registros de flujo (y no el grupo de seguridad de red).
- Definición de la directiva: debe elegirse tal y como se muestra en la sección "Buscar directivas".
- AssignmentName: debe elegir un nombre descriptivo. 

2. Haga clic en "Revisar y crear" para revisar la asignación.

La directiva no requiere ningún parámetro. Cuando se asigna una directiva de auditoría, no es necesario rellenar los detalles en la pestaña de "Corrección".  

![Revisión de la directiva de auditoría de Análisis de tráfico](./media/traffic-analytics/policy-one-assign.png)

### <a name="results"></a>Results

Para comprobar los resultados, abra la pestaña Cumplimiento y busque el nombre de la asignación.
Debería ver algo parecido a la siguiente captura de pantalla, una vez que se ejecuta la directiva. En caso de que no se haya ejecutado la directiva, espere un momento. 

![Resultados de la directiva de auditoría de Análisis de tráfico](./media/traffic-analytics/policy-one-results.png)

## <a name="deploy-if-not-exists-policy"></a>Directiva "Implementar si no EXISTE" 

### <a name="configure-network-security-groups-to-use-specific-workspace-for-traffic-analytics"></a>Configurar grupos de seguridad de red para usar el área de trabajo específica para el análisis de tráfico 

Marca el NSG que no tiene el Análisis de tráfico habilitado. Esto significa que, para el NSG marcado, o bien no existe el recurso de registros de flujo correspondiente, o bien sí existe, pero el análisis de tráfico no está habilitado en él. Puede crear una tarea de corrección si quiere que la directiva afecte a los recursos existentes.
Network Watcher es un servicio regional, por lo que esta directiva se aplicará a los NSG que pertenezcan a una región determinada solo en el ámbito seleccionado. (Para una región diferente, cree otra asignación de directiva).
 
La corrección se puede asignar al mismo tiempo que se asigna la directiva, o bien después de asignarla y evaluarla. La corrección habilitará el Análisis de tráfico en todos los recursos marcados con los parámetros proporcionados. Tenga en cuenta que, si un NSG ya tiene habilitados los registros de flujo en un identificador de almacenamiento determinado, pero no tiene habilitado el Análisis de tráfico, la corrección lo habilitará en este NSG con los parámetros proporcionados. Si en el NSG marcado el identificador de almacenamiento proporcionado en los parámetros es diferente del que está habilitado para los registros de flujo, este último se sobrescribirá con el identificador de almacenamiento proporcionado en la tarea de corrección. Si no quiere que se sobrescriba, use la directiva *"Configure network security groups to enable Traffic Analytics"* (Configurar grupos de seguridad de red para habilitar el Análisis de tráfico) que se describe a continuación.

Si quiere ver la definición completa de la directiva, vaya a la pestaña [Definiciones](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) y busque "análisis de tráfico" para consultar la directiva. 

### <a name="configure-network-security-groups-to-enable-traffic-analytics"></a>Configuración de grupos de seguridad de red para habilitar el Análisis de tráfico

Esta directiva es igual que la anterior, con la diferencia de que, durante la corrección, no sobrescribe la configuración de los registros de flujo en los NSG marcados que tienen habilitados los registros de flujo, pero deshabilitado el Análisis de tráfico con el parámetro proporcionado en la asignación de directiva.

### <a name="assignment"></a>Asignación

1. Rellene los detalles de la directiva.

- Ámbito: puede ser una suscripción o un grupo de recursos.  
- Definición de la directiva: debe elegirse tal y como se muestra en la sección "Buscar directivas".
- AssignmentName: debe elegir un nombre descriptivo. 

2. Agregar parámetros de directiva 

- Región NSG: son las regiones de Azure a las que se destina la directiva.
- Id. de almacenamiento: es el id. de recurso de la cuenta de almacenamiento. esta cuenta de almacenamiento debe estar en la misma región que el NSG.
- Grupo de recursos de Network Watcher: es el nombre del grupo de recursos que contiene el recurso de Network Watcher. Si no se le ha cambiado el nombre, puede escribir "NetworkWatcherRG", que es el valor predeterminado.
- Nombre de la instancia de Network Watcher: es el nombre del servicio de Network Watcher regional. Formato: NetworkWatcher_NombreDeRegión. Ejemplo: NetworkWatcher_centralus.
- Identificador de recurso del área de trabajo: identificador de recurso del área de trabajo donde se debe habilitar el Análisis de tráfico. El formato es /subscriptions/<SubscriptionID>/resourceGroups/<ResouceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>".
- WorkspaceID: GUID del área de trabajo.
- WorkspaceRegion: región del área de trabajo (tenga en cuenta que no es necesario que sea igual que la región del NSG).
- TimeInterval: frecuencia con la que se insertarán los registros procesados en el área de trabajo. Los valores permitidos actualmente son 60 minutos y 10 minutos. El valor predeterminado es 60 minutos.
- Efecto: DeployIfNotExists (valor ya asignado).

3. Agregar detalles de corrección

- Seleccione la marca de verificación en *"Crear tarea de corrección"* si quiere que la directiva afecte a los recursos existentes.
- La opción *"Crear una identidad administrada"* debe estar ya seleccionada.
- Debe seleccionar la misma ubicación que la que seleccionó para la identidad administrada.
- Necesitará permisos de colaborador o de propietario para usar esta directiva. Si tiene estos permisos, no debería ver ningún error.

4. Haga clic en "Revisar y crear" para revisar la asignación. Debería ver algo parecido a la captura de pantalla siguiente.

![Revisión de la directiva DINE de Análisis de tráfico](./media/traffic-analytics/policy-two-review.png) 


### <a name="results"></a>Results

Para comprobar los resultados, abra la pestaña Cumplimiento y busque el nombre de la asignación.
Debería ver algo parecido a la siguiente captura de pantalla de la directiva. En caso de que no se haya ejecutado la directiva, espere un momento.

![Resultados de la directiva DINE de Análisis de tráfico](./media/traffic-analytics/policy-two-results.png)  

### <a name="remediation"></a>Corrección

Para realizar la corrección manualmente, seleccione *"Crear tarea de corrección"* en la pestaña Cumplimiento que se muestra anteriormente.

![Corrección de la directiva DINE de Análisis de tráfico](./media/traffic-analytics/policy-two-remediate.png) 


## <a name="troubleshooting"></a>Solución de problemas

### <a name="remediation-task-fails-with-policyauthorizationfailed-error-code"></a>Se produce un error en la tarea de corrección con el código de error "PolicyAuthorizationFailed".

Ejemplo de error: "The policy assignment '/subscriptions/123ds-fdf3657-fdjjjskms638/resourceGroups/DummyRG/providers/Microsoft.Authorization/policyAssignments/b67334e8770a4afc92e7a929/' resource identity does not have the necessary permissions to create deployment" (La identidad del recurso "/subscriptions/123ds-fdf3657-fdjjjskms638/resourceGroups/DummyRG/providers/Microsoft.Authorization/policyAssignments/b67334e8770a4afc92e7a929/" de la asignación de directiva no tiene los permisos necesarios para crear la implementación).

En estos casos, hay que conceder acceso manualmente a la identidad administrada de la asignación. Vaya a la suscripción o al grupo de recursos que corresponda (es decir, que contenga los recursos proporcionados en los parámetros de la directiva) y conceda acceso de colaborador a la identidad administrada creada por la directiva. En el ejemplo anterior, "b67334e8770a4afc92e7a929" debe ser colaborador.


## <a name="next-steps"></a>Pasos siguientes 

-   Obtenga más información sobre las [directivas integradas de registros de flujo de NSG](./nsg-flow-logs-policy-portal.md).
-   Obtenga más información sobre el [Análisis de tráfico](./traffic-analytics.md).
-   Obtenga más información sobre [Network Watcher](./index.yml).
