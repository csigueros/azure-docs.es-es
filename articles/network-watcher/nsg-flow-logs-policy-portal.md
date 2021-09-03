---
title: 'Guía de inicio rápido: implementación y administración de registros de flujo de NSG mediante Azure Policy'
titleSuffix: Azure Network Watcher
description: En este artículo se explica cómo usar las directivas integradas para administrar la implementación de registros de flujo de NSG
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 2e0ab4a22665741abb7ed4ab2569d1b55e090457
ms.sourcegitcommit: 6f4378f2afa31eddab91d84f7b33a58e3e7e78c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2021
ms.locfileid: "113688558"
---
# <a name="quickstart-deploy-and-manage-nsg-flow-logs-using-azure-policy"></a>Inicio rápido: Implementación y administración de registros de flujo de NSG con Azure Policy 

## <a name="overview"></a>Introducción
Azure Policy ayuda a aplicar los estándares de la organización y a evaluar el cumplimiento a escala. Entre los casos de uso comunes de Azure Policy se incluye la implementación de la gobernanza para la coherencia de los recursos, el cumplimiento normativo, la seguridad, el costo y la administración. En este artículo, usaremos dos directivas integradas disponibles en los registros de flujo de NSG, y así poder administrar la configuración de los registros de flujo. La primera directiva marca cualquier grupo de seguridad de red que no tenga registros de flujo habilitados. La segunda directiva implementa automáticamente los registros de flujo de los grupos de seguridad de red que no tengan registros de flujo habilitados. 

Si va a crear una directiva de Azure por primera vez, puede leer lo siguiente: 
- [Introducción a Azure Policy](../governance/policy/overview.md). 
- [Tutorial para crear directivas](../governance/policy/assign-policy-portal.md#create-a-policy-assignment).


## <a name="locate-the-policies"></a>Búsqueda de las directivas
1. Vaya Azure Portal en [portal.azure.com](https://portal.azure.com) 

Acceda a la página de Azure Policy; para ello, busque la palabra "Directiva" en la barra de búsqueda ![Policy Home Page](./media/network-watcher-builtin-policy/1_policy-search.png) (Página principal de la directiva) de la parte superior.

2. Vaya a la pestaña **Asignaciones** del panel izquierdo.

![Ficha de asignaciones](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. Haga clic en botón **Asignar directiva**. 

![Botón para asignar la directiva](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. Haga clic en el menú de tres puntos en "Definiciones de la directiva" para ver las directivas disponibles.

5. Use el filtro de tipo y elija "Integrado". A continuación, busque "Registro de flujo".

Debería ver las dos directivas integradas para los registros de flujo en la ![Lista de directivas](./media/network-watcher-builtin-policy/4_filter-for-flow-log-policies.png)

6. Elija la directiva que quiera asignar.

- *"El registro de flujo se debe configurar para cada grupo de seguridad de red"* es la directiva de auditoría que marca aquellos grupos de seguridad de red que no son compatibles; esto es, los grupos de seguridad de red que no tienen el registro de flujo habilitado.
- *"Implementación de un recurso de registro de flujo con el grupo de seguridad de red de destino"* es la directiva que cuenta con una acción de implementación y que habilita los registros de flujo en todos los grupos de seguridad de red que no tengan registros de flujo.

A continuación encontrará instrucciones independientes para cada directiva.  

## <a name="audit-policy"></a>Directiva de auditoría 

### <a name="how-the-policy-works"></a>Cómo funciona la directiva

La directiva comprueba todos los objetos ARM existentes de tipo "Microsoft.Network/networkSecurityGroups"; es decir, examina todos los grupos de seguridad de red de un ámbito determinado y comprueba la existencia de registros de flujo vinculados a través de la propiedad de registros de flujo del grupo de seguridad de red. Si la propiedad no existe, se marca ese grupo de seguridad de red.

Si quiere ver la definición completa de la directiva, puede visitar la pestaña de [Definiciones](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) y buscar "Registros de flujo" para buscar la directiva.

### <a name="assignment"></a>Asignación

1. Rellene los detalles de la directiva.

- Ámbito: una suscripción es la opción más común, aunque también puede elegir un grupo de administración o un grupo de recursos según sea necesario.  
- Definición de la directiva: debe elegirse tal y como se muestra en la sección "Buscar directivas".
- AssignmentName: debe elegir un nombre descriptivo. 

2. Haga clic en "Revisar y crear" para revisar la asignación.

La directiva no requiere ningún parámetro. Cuando se asigna una directiva de auditoría, no es necesario rellenar los detalles en la pestaña de "Corrección".  

![Revisión de la directiva de auditoría](./media/network-watcher-builtin-policy/5_1_audit-policy-review.png)

### <a name="results"></a>Results

Para comprobar los resultados, abra la pestaña Cumplimiento y busque el nombre de la asignación.
Debería ver algo parecido a la siguiente captura de pantalla, una vez que se ejecuta la directiva. En caso de que no se haya ejecutado la directiva, espere un momento. 

![Resultados de la directiva de auditoría](./media/network-watcher-builtin-policy/7_1_audit-policy-results.png)

## <a name="deploy-if-not-exists-policy"></a>Directiva "Implementar si no EXISTE" 

### <a name="policy-structure"></a>Estructura de la directiva

La directiva comprueba todos los objetos ARM existentes de tipo "Microsoft.Network/networkSecurityGroups"; es decir, examina todos los grupos de seguridad de red de un ámbito determinado y comprueba la existencia de registros de flujo vinculados a través de la propiedad de registros de flujo del grupo de seguridad de red. Si la propiedad no existe, la directiva implementa un registro de flujo. 

Si quiere ver la definición completa de la directiva, puede visitar la pestaña de [Definiciones](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) y buscar "Registros de flujo" para buscar la directiva. 

### <a name="assignment"></a>Asignación

1. Rellene los detalles de la directiva.

- Ámbito: una suscripción es la opción más común, aunque también puede elegir un grupo de administración o un grupo de recursos según sea necesario.  
- Definición de la directiva: debe elegirse tal y como se muestra en la sección "Buscar directivas".
- AssignmentName: debe elegir un nombre descriptivo. 

2. Agregar parámetros de directiva 

El servicio Network Watcher es un servicio regional. Estos parámetros permiten que se ejecute la acción de la directiva para la implementación de registros de flujo. 
- Región NSG: son las regiones de Azure a las que se destina la directiva.
- Id. de almacenamiento: es el id. de recurso de la cuenta de almacenamiento. Nota: esta cuenta de almacenamiento debe estar en la misma región que el NSG. 
- Grupo de recursos de Network Watcher: es el nombre del grupo de recursos que contiene el recurso de Network Watcher. Si no se le ha cambiado el nombre, puede escribir "NetworkWatcherRG", que es el valor predeterminado.
- Nombre de la instancia de Network Watcher: es el nombre del servicio de Network Watcher regional. Formato: NetworkWatcher_NombreDeRegión. Ejemplo: NetworkWatcher_centralus. Consulte la lista completa.

![Parámetros de la directiva DINE](./media/network-watcher-builtin-policy/5_2_1_dine-policy-details-alt.png)

3. Agregar detalles de corrección

- Seleccione la marca de verificación en "Crear tarea de corrección" si quiere que la directiva afecte a los recursos existentes. 
- La opción "Crear una identidad administrada" debe estar ya seleccionada.
- Debe seleccionar la misma ubicación que la que seleccionó para la identidad administrada. 
- Necesitará permisos de colaborador o de propietario para usar esta directiva. Si tiene estos permisos, no debería ver ningún error.

![Corrección de la directiva DINE](./media/network-watcher-builtin-policy/5_2_2_dine-remediation.png) 

4. Haga clic en "Revisar y crear" para revisar la asignación. Debería ver algo parecido a la captura de pantalla siguiente.

![Revisión de la directiva DINE](./media/network-watcher-builtin-policy/5_2_3_dine-review.png) 


### <a name="results"></a>Results

Para comprobar los resultados, abra la pestaña Cumplimiento y busque el nombre de la asignación.
Debería ver algo parecido a la siguiente captura de pantalla de la directiva. En caso de que no se haya ejecutado la directiva, espere un momento.

![Resultados de la directiva DINE](./media/network-watcher-builtin-policy/7_2_dine-policy-results.png)  


## <a name="next-steps"></a>Pasos siguientes 

-   Más información sobre [Directivas integradas de Análisis de tráfico](./traffic-analytics-policy-portal.md)
-   Use este [tutorial](./quickstart-configure-network-security-group-flow-logs-from-arm-template.md) para profundizar en el uso de plantillas de ARM para implementar registros de flujo y el Análisis de tráfico.
-   Obtenga más información sobre [Network Watcher](./index.yml).