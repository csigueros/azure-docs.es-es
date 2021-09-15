---
title: Examen de los orígenes de datos de forma privada y segura
description: En este artículo se describe cómo configurar un punto de conexión privado para examinar orígenes de datos desde una red restringida.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: cbdf2220d1b4087376bc40db5b7da167144e5d9b
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123257205"
---
# <a name="scan-your-data-sources-privately-and-securely"></a>Examen de los orígenes de datos de forma privada y segura

Si tiene previsto examinar sus orígenes de datos en redes privadas, redes virtuales y detrás de puntos de conexión privados, debe implementar puntos de conexión privados de ingesta de Azure Purview, con el fin de garantizar el aislamiento de red de los metadatos que fluyan desde los orígenes de datos que se van a examinar hasta el mapa de datos de Azure Purview.

Azure Purview puede examinar orígenes de datos que estén en Azure o en un entorno local usando puntos de conexión privados de _ingesta_. Cuando se implementan puntos de conexión privados de ingesta, es necesario implementar tres recursos de punto de conexión privado adicionales y vincularlos a los recursos administrados de Azure Purview:

- Un punto de conexión privado de blob se vincula a una cuenta de almacenamiento administrada de Azure Purview.
- Un punto de conexión privado de cola se vincula a una cuenta de almacenamiento administrada de Azure Purview.
- Un punto de conexión privado de espacio de nombres se vincula al espacio de nombres de un centro de eventos administrado de Azure Purview.

:::image type="content" source="media/catalog-private-link/purview-private-link-architecture-ingestion.png" alt-text="Diagrama en el que se muestra la arquitectura de Azure Purview y Private Link":::

## <a name="deployment-checklist"></a>Lista de comprobación de la implementación
Usando una de las opciones de implementación de esta guía, habilite los puntos de conexión privados de ingesta para su cuenta de Azure Purview:

1. Elija una red virtual de Azure adecuada y una subred para implementar puntos de conexión privados de ingesta de Azure Purview. Seleccione una de las siguientes opciones:
   - Implemente una [nueva red virtual ](../virtual-network/quick-create-portal.md) en su suscripción de Azure.
   - Busque una red virtual de Azure existente y una subred en la suscripción de Azure.
  
2. Defina un [método de resolución de nombres DNS](./catalog-private-link-name-resolution.md#deployment-options) adecuado para la ingesta, de modo que Azure Purview pueda examinar orígenes de datos mediante una red privada. Puede usar cualquiera de las opciones siguientes:
   - Implemente nuevas zonas de Azure DNS mediante los pasos que se explican más adelante en esta guía.
   - Agregue los registros DNS necesarios a las zonas de Azure DNS existentes mediante los pasos que se explican más adelante en esta guía.
   - Después de completar los pasos de esta guía, agregue manualmente los registros DNS A necesarios a los servidores DNS existentes.
3. Implemente una [nueva cuenta de Purview](#option-1---deploy-a-new-azure-purview-account-with-ingestion-private-endpoint) con puntos de conexión privados de ingesta, o bien implemente puntos de conexión privados de ingesta para una [cuenta de Purview existente](#option-2---enable-ingestion-private-endpoint-on-existing-azure-purview-accounts).
4. Implemente y registre el [entorno de ejecución de integración autohospedado](#deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources) en la misma red virtual donde están implementados los puntos de conexión privados de ingesta de Azure Purview.
5. Cuando complete esta guía, ajuste las configuraciones de DNS si es necesario.
6. Valide la red y la resolución de nombres entre la máquina de administración, la VM de IR autohospedado y los orígenes de datos en Azure Purview. 

## <a name="option-1---deploy-a-new-azure-purview-account-with-_ingestion_-private-endpoint"></a>Opción 1: Implementación de una nueva cuenta de Azure Purview con puntos de conexión privados de _ingesta_

1. Vaya a [Azure Portal](https://portal.azure.com) y, después, diríjase a la página **Cuentas de Purview**. Seleccione **+ Crear** para crear la cuenta de Azure Purview.

2. Rellene la información básica y, en la pestaña **Redes**, establezca el método de conectividad como **Punto de conexión privado**. Establezca el punto de conexión privado en **Solo ingesta**.

      :::image type="content" source="media/catalog-private-link/purview-pe-scenario-2-1.png" alt-text="Captura de pantalla en la que se muestran las selecciones de la página &quot;Crear un punto de conexión privado&quot;":::

3. Para configurar los puntos de conexión privados de ingesta, indique los detalles de **Suscripción**, **Red virtual** y **Subred** que quiera emparejar con el punto de conexión privado.

4. También puede seleccionar **Integración de DNS privado** para usar zonas DNS privadas de Azure.
   
   > [!IMPORTANT]
   > Es importante seleccionar zonas DNS privadas correctas de Azure para permitir una correcta resolución de nombres entre Azure Purview y los orígenes de datos. También puede usar sus zonas DNS privadas de Azure ya existentes o crear registros de DNS en sus servidores DNS manualmente más adelante. Para obtener más información, consulte [Configuración de la resolución de nombres DNS para puntos de conexión privados](./catalog-private-link-name-resolution.md).

5.  Seleccione **Revisar + crear**. En la página **Revisar y crear**, Azure valida la configuración.

6.  Cuando vea el mensaje "Validación superada", seleccione **Crear**.

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="Captura de pantalla en la que se muestra la validación superada para la creación de cuentas":::

## <a name="option-2---enable-_ingestion_-private-endpoint-on-existing-azure-purview-accounts"></a>Opción 2: Habilitación de un punto de conexión privado de _ingesta_ en cuentas de Azure Purview existentes

1.  Vaya a [Azure Portal](https://portal.azure.com) y, a continuación, haga clic en su cuenta de Azure Purview. En **Configuración**, seleccione **Redes** y luego **Conexiones de punto de conexión privado de ingesta**.

2. En "Conexiones de punto de conexión privado de ingesta", seleccione **+ Nuevo** para crear un punto de conexión privado de ingesta.

3. Rellene la información básica y seleccione la red virtual existente y los detalles de una subred. También puede seleccionar **Integración de DNS privado** para usar zonas DNS privadas de Azure. 
   
   :::image type="content" source="media/catalog-private-link/ingestion-pe-fill-details.png" alt-text="Captura de pantalla en la que se muestra cómo se rellenan los detalles del punto de conexión privado":::
   
   > [!IMPORTANT]
   > Es importante seleccionar zonas DNS privadas correctas de Azure para permitir una correcta resolución de nombres entre Azure Purview y los orígenes de datos. También puede usar sus zonas DNS privadas de Azure ya existentes o crear registros de DNS en sus servidores DNS manualmente más adelante. 
   > 
   >Para obtener más información, consulte [Configuración de la resolución de nombres DNS para puntos de conexión privados](./catalog-private-link-name-resolution.md).


4. Seleccione **Crear** para finalizar la configuración.

> [!NOTE]
> Los puntos de conexión privados de ingesta solo se pueden crear a través de la experiencia del portal de Azure Purview que se describe en los pasos anteriores. No se pueden crear desde Private Link Center.

## <a name="deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources"></a>Implemente el entorno de ejecución de integración (IR) autohospedado y examine los orígenes de datos.
Una vez que implemente puntos de conexión privados de ingesta para Azure Purview, tiene que configurar y registrar al menos un entorno de ejecución de integración autohospedado:

- Actualmente, todos los tipos de orígenes locales, como Microsoft SQL Server, Oracle, SAP, etc., solo se admiten a través de exámenes basados en IR autohospedado. El IR autohospedado debe ejecutarse dentro de la red privada y, a continuación, emparejarse con la red virtual en Azure. 
   
- Para todos los tipos de origen de Azure, como Azure Blob Storage y Azure SQL Database, debe elegir explícitamente ejecutar el examen mediante un entorno de ejecución de integración autohospedado que se haya implementado en la misma red virtual que el punto de conexión privado de ingesta de Azure Purview. 

Siga los pasos descritos en [Creación y administración de un entorno de ejecución de integración autohospedado](manage-integration-runtimes.md) para configurar un entorno de ejecución de integración autohospedado. A continuación, configure el examen en el origen de Azure; para ello, elija el IR autohospedado en la lista desplegable **Conectar mediante el entorno de ejecución de integración** para garantizar el aislamiento de red.
    
   :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="Captura de pantalla en la que se muestra la ejecución de un examen de Azure con un IR autohospedado":::

> [!IMPORTANT]
> Si ha creado su cuenta de Azure Purview después del 18 de agosto de 2021, asegúrese de descargar e instalar la versión más reciente del entorno de ejecución de integración autohospedado desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
> 
## <a name="next-steps"></a>Pasos siguientes

-  [Comprobación de la resolución de puntos de conexión privados](./catalog-private-link-name-resolution.md)
-  [Administración de orígenes de datos en Azure Purview](./manage-data-sources.md)
-  [Solución de problemas de configuración de punto de conexión privado de la cuenta de Azure Purview](./catalog-private-link-troubleshoot.md)
