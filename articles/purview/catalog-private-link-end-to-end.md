---
title: Conexión a Azure Purview y examen de los orígenes de datos de forma privada y segura
description: En este artículo se describe cómo puede configurar un punto de conexión privado para conectarse a su cuenta de Purview y examinar orígenes de datos desde una red restringida para un aislamiento de un extremo a otro.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 4f15d39da8f97897498f07892eb90d37a1bd8574
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209344"
---
# <a name="connect-to-your-azure-purview-and-scan-data-sources-privately-and-securely"></a>Conexión a Azure Purview y examen de los orígenes de datos de forma privada y segura

En esta guía, aprenderá a implementar puntos de conexión privados de _cuenta_, _portal_ e _ingesta_ para su cuenta de Azure Purview, para acceder a la cuenta de Purview, y a analizar los orígenes de datos mediante un entorno de ejecución de integración autohospedado de manera segura y privada, lo que permite el aislamiento de red de un extremo a otro.

El punto de conexión privado de la _cuenta_ de Azure Purview se usa para agregar una capa adicional de seguridad mediante la habilitación de escenarios en que solo las llamadas de cliente procedentes de la red virtual puedan acceder a dicha cuenta. Este punto de conexión privado también es un requisito previo para el punto de conexión privado del portal.

El punto de conexión privado del _portal_ de Azure Purview es necesario para habilitar la conectividad a [Azure Purview Studio](https://web.purview.azure.com/resource/) mediante una red privada.

Azure Purview puede examinar orígenes de datos que estén en Azure o en un entorno local usando puntos de conexión privados de _ingesta_. Cuando se implementan puntos de conexión privados de ingesta, es necesario implementar tres recursos de punto de conexión privado adicionales y vincularlos a los recursos administrados de Azure Purview:

 - Un punto de conexión privado de blob se vincula a una cuenta de almacenamiento administrada de Azure Purview.
 - Un punto de conexión privado de cola se vincula a una cuenta de almacenamiento administrada de Azure Purview.
 - Un punto de conexión privado de espacio de nombres se vincula al espacio de nombres de un centro de eventos administrado de Azure Purview.

  :::image type="content" source="media/catalog-private-link/purview-private-link-architecture.png" alt-text="Diagrama en el que se muestra la arquitectura de Azure Purview y Private Link":::

## <a name="deployment-checklist"></a>Lista de comprobación de la implementación
Con una de las opciones de implementación que se explican más en detalle en esta guía, puede implementar una nueva cuenta de Azure Purview con puntos de conexión privados de _cuenta_, _portal_ e _ingesta_, o bien puede optar por implementar estos puntos de conexión privados para una cuenta de Azure Purview existente:

1. Elija una red virtual de Azure adecuada y una subred para implementar puntos de conexión privados de Azure Purview. Seleccione una de las siguientes opciones:
   - Implemente una [nueva red virtual ](../virtual-network/quick-create-portal.md) en su suscripción de Azure.
   - Busque una red virtual de Azure existente y una subred en la suscripción de Azure.
  
2. Defina un [método de resolución de nombres DNS](./catalog-private-link-name-resolution.md#deployment-options) adecuado, de modo que pueda acceder a la cuenta de Azure Purview y examinar orígenes de datos mediante una red privada. Puede usar cualquiera de las opciones siguientes:
   - Implemente nuevas zonas de Azure DNS mediante los pasos que se explican más adelante en esta guía.
   - Agregue los registros DNS necesarios a las zonas de Azure DNS existentes mediante los pasos que se explican más adelante en esta guía.
   - Después de completar los pasos de esta guía, agregue manualmente los registros DNS A necesarios a los servidores DNS existentes.
3. Implemente una [nueva cuenta de Purview](#option-1---deploy-a-new-azure-purview-account-with-account-portal-and-ingestion-private-endpoints) con puntos de conexión privados de cuenta, portal e ingesta, o bien implemente puntos de conexión privados para una [cuenta de Purview existente](#option-2---enable-account-portal-and-ingestion-private-endpoint-on-existing-azure-purview-accounts).
4. [Habilite el acceso a Azure Active Directory](#enable-access-to-azure-active-directory) si la red privada tiene reglas de grupo de seguridad de red establecidas para denegar todo el tráfico público de Internet.
5. Implemente y registre el [entorno de ejecución de integración autohospedado](#deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources) en la misma red virtual donde están implementados los puntos de conexión privados de ingesta de Azure Purview.
6. Cuando complete esta guía, ajuste las configuraciones de DNS si es necesario.
7. Valide la red y la resolución de nombres entre la máquina de administración, la VM de IR autohospedado y los orígenes de datos en Azure Purview. 

## <a name="option-1---deploy-a-new-azure-purview-account-with-_account_-_portal_-and-_ingestion_-private-endpoints"></a>Opción 1: Implementar una nueva cuenta de Azure Purview con puntos de conexión privados de _cuenta_, _portal_ e _ingesta_

1. Vaya a [Azure Portal](https://portal.azure.com) y, después, diríjase a la página **Cuentas de Purview**. Seleccione **+ Crear** para crear la cuenta de Azure Purview.

2. Rellene la información básica y, en la pestaña **Redes**, establezca el método de conectividad como **Punto de conexión privado**. Establezca el punto de conexión privado en **Cuenta, portal e ingesta**.

3. Seleccione **Cuenta y portal**, seleccione **+ Agregar** para agregar un punto de conexión privado para la cuenta de Azure Purview.

   :::image type="content" source="media/catalog-private-link/purview-pe-deploy-end-to-end.png" alt-text="Captura de pantalla que muestra las distintas selecciones de la página Crear un punto de conexión privado.":::

4. En la página **Crear un punto de conexión privado**, en **Subrecurso de Purview**, elija su ubicación, proporcione un nombre para el punto de conexión privado de _cuenta_ y seleccione **cuenta**. En **Redes**, seleccione la red virtual y la subred y, opcionalmente, seleccione **Integrar con la zona DNS privada** para crear una nueva zona DNS privada de Azure. 

   :::image type="content" source="media/catalog-private-link/purview-pe-deploy-account.png" alt-text="Captura de pantalla que muestra la página de creación de un punto de conexión privado de la cuenta.":::

   > [!NOTE]
   > También puede usar las zonas DNS privadas de Azure existentes o crear registros de DNS en los servidores DNS manualmente más adelante. Para obtener más información, consulte [Configuración de la resolución de nombres DNS para puntos de conexión privados](./catalog-private-link-name-resolution.md).

5. Seleccione **Aceptar**.
   
6. En el asistente para **Cuenta y portal**, nuevamente seleccione **+ Agregar** para agregar el punto de conexión privado de _portal_. 
  
7. En la **página Crear un punto de conexión privado**, en **Subrecurso de Purview**, elija su ubicación, proporcione un nombre para el punto de conexión privado de _portal_ y seleccione **portal**. En **Redes**, seleccione la red virtual y la subred y, opcionalmente, seleccione **Integrar con la zona DNS privada** para crear una nueva zona DNS privada de Azure. 

   :::image type="content" source="media/catalog-private-link/purview-pe-deploy-portal.png" alt-text="Captura de pantalla que muestra la página de creación de un punto de conexión privado del portal.":::
   
   > [!NOTE]
   > También puede usar las zonas DNS privadas de Azure existentes o crear registros de DNS en los servidores DNS manualmente más adelante. Para obtener más información, consulte [Configuración de la resolución de nombres DNS para puntos de conexión privados](./catalog-private-link-name-resolution.md).

8. Seleccione **Aceptar**.

9.  En **Ingesta**, para configurar los puntos de conexión privados de ingesta, indique los detalles de **Suscripción**, **Red virtual** y **Subred** que quiera emparejar con el punto de conexión privado.

10. De manera opcional, seleccione **Integración de DNS privado** para usar Azure Private DNS Zones.
   
      :::image type="content" source="media/catalog-private-link/purview-pe-deploy-ingestion.png" alt-text="Captura de pantalla que muestra la página de información general de creación del punto de conexión privado.":::

      > [!IMPORTANT]
      > Es importante seleccionar Azure DNS Private Zones para permitir la correcta resolución de nombres entre Azure Purview y los orígenes de datos. También puede usar sus zonas DNS privadas de Azure ya existentes o crear registros de DNS en sus servidores DNS manualmente más adelante. Para obtener más información, consulte [Configuración de la resolución de nombres DNS para puntos de conexión privados](./catalog-private-link-name-resolution.md).

11. Seleccione **Revisar + crear**. En la página **Revisar y crear**, Azure valida la configuración.

12. Cuando vea el mensaje "Validación superada", seleccione **Crear**.
   

## <a name="option-2---enable-_account_-_portal_-and-_ingestion_-private-endpoint-on-existing-azure-purview-accounts"></a>Opción 2: Habilitar el punto de conexión privado de _cuenta_, _portal_ e _ingesta_ en cuentas de Azure Purview existentes

1. Vaya a [Azure Portal](https://portal.azure.com) y, a continuación, seleccione su cuenta de Azure Purview. En **Configuración**, seleccione **Redes** y luego **Conexiones de punto de conexión privado**.

    :::image type="content" source="media/catalog-private-link/purview-pe-add-to-existing.png" alt-text="Captura de pantalla que muestra cómo crear un punto de conexión privado de la cuenta.":::

2. Seleccione **+ Punto de conexión privado** para crear uno.

3. Rellene la información básica.

4. En la pestaña **Recurso**, seleccione **Microsoft.Purview/accounts** como **Tipo de recurso**.

5. En **Recurso**, seleccione la cuenta de Azure Purview y en **Subrecurso de destino**, seleccione **cuenta**.

6. En la pestaña **Configuración**, seleccione la red virtual y, opcionalmente, seleccione la zona DNS privada de Azure para crear una nueva zona DNS de Azure.
   
   > [!NOTE]
   > Para la configuración de DNS, también puede usar las zonas DNS privadas de Azure existentes de la lista desplegable o agregar los registros DNS necesarios a los servidores DNS manualmente más adelante. Para obtener más información, consulte [Configuración de la resolución de nombres DNS para puntos de conexión privados](./catalog-private-link-name-resolution.md).

7. Vaya a la página de resumen y seleccione **Crear** para crear el punto de conexión privado del portal.
   
8. Siga los mismos pasos al seleccionar el **portal** como **Subrecurso de destino**.
   
9. En su cuenta de Azure Purview, en **Configuración**, seleccione **Redes** y luego **Ingestion private endpoint connections** (Conexiones de punto de conexión privado).

10. En "Conexiones de punto de conexión privado de ingesta", seleccione **+ Nuevo** para crear un punto de conexión privado de ingesta.

      :::image type="content" source="media/catalog-private-link/purview-pe-add-ingestion-to-existing.png" alt-text="Captura de pantalla que muestra cómo agregar un punto de conexión privado a una cuenta existente.":::

11. Rellene la información básica y seleccione la red virtual existente y los detalles de una subred. De manera opcional, seleccione **Integración de DNS privado** para usar Azure Private DNS Zones. Seleccione la instancia correcta de Azure Private DNS Zones en cada lista.

      > [!NOTE]
      > También puede usar la instancia de Azure Private DNS Zones existente o crear registros de DNS en los servidores DNS manualmente más adelante. Para más información, consulte [Configuración de la resolución de nombres DNS para puntos de conexión privados](./catalog-private-link-name-resolution.md).

12. Seleccione **Crear** para finalizar la configuración.

## <a name="enable-access-to-azure-active-directory"></a>Habilitación del acceso a Azure Active Directory

> [!NOTE]
> Si la VM, VPN Gateway o la puerta de enlace de Emparejamiento de VNet tienen acceso público a Internet, puede acceder al portal de Azure Purview y a la cuenta de Azure Purview habilitada con puntos de conexión privados. Por este motivo, no es necesario seguir el resto de instrucciones. Si la red privada tiene reglas de grupo de seguridad de red establecidas para denegar todo el tráfico de Internet público, tendrá que agregar algunas reglas para habilitar el acceso a Azure Active Directory (Azure AD). Siga las instrucciones para hacerlo.

Estas instrucciones se proporcionan para acceder de forma segura a Azure Purview desde una VM de Azure. Deben seguirse pasos similares si usa VPN u otras puertas de enlace de Emparejamiento de VNet.

1. Vaya a su máquina virtual en Azure Portal y, en **Configuración**, seleccione **Redes**. A continuación, seleccione **Reglas de puerto de salida** > **Agregar regla de puerto de salida**.

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="Captura de pantalla que muestra cómo agregar una regla de salida.":::

1. En el panel **Agregar regla de seguridad de salida**:

   1. En **Destino**, seleccione **Etiqueta de servicio**.
   1. En **Etiqueta de servicio de destino**, seleccione **AzureActiveDirectory**.
   1. En **Intervalos de puertos de destino**, seleccione *.
   1. En **Acción**, seleccione **Permitir**.
   1. En **Prioridad**, el valor debe ser mayor que la regla que denegó todo el tráfico de Internet.
   
   Cree la regla.

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="Captura de pantalla que muestra cómo agregar detalles de la regla de salida.":::

1. Siga los mismos pasos para crear otra regla que permita la etiqueta de servicio **AzureResourceManager**. Si necesita acceder a Azure Portal, también puede agregar una regla para la etiqueta de servicio **AzurePortal**.

1. Conéctese a la VM y abra el explorador. Seleccione Ctrl+Mayús+J para ir a la consola del explorador y cambie a la pestaña de red para supervisar las solicitudes de red. Escriba web.purview.azure.com en el cuadro de la dirección URL e intente iniciar sesión con las credenciales de Azure AD. Es posible que se produzca un error de inicio de sesión y que, en la pestaña **Red** de la consola, vea que Azure AD intenta acceder a aadcdn.msauth.net, pero se bloquea.

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="Captura de pantalla que muestra los detalles del error de inicio de sesión.":::

1. En este caso, abra un símbolo del sistema en la VM, haga ping a aadcdn.msauth.net, obtenga su dirección IP y, a continuación, agregue una regla de puerto de salida para la IP en las reglas de seguridad de red de la VM. Establezca **Destino** en **Direcciones IP** y establezca **Direcciones IP de destino** en la dirección IP de aadcdn. Debido a Azure Load Balancer y Azure Traffic Manager, la dirección IP de Azure AD Content Delivery Network puede ser dinámica. Después de obtener la dirección IP, es mejor agregarla al archivo host de la VM para forzar al explorador a visitar esa IP a fin de acceder a Azure AD Content Delivery Network.

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="Captura de pantalla que muestra el ping de prueba.":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="Captura de pantalla que muestra la regla de Azure AD Content Delivery Network.":::

1. Una vez creada la regla, vuelva a la VM e intente volver a iniciar sesión con las credenciales de Azure AD. Si logra iniciar sesión, el portal de Azure Purview estará listo para usar. Sin embargo, en algunos casos, Azure AD redirige a otros dominios para iniciar sesión en función del tipo de cuenta de un cliente. Por ejemplo, para una cuenta de live.com, Azure AD redirige a live.com para iniciar sesión y, a continuación, esas solicitudes se bloquean de nuevo. Para las cuentas de empleado de Microsoft, Azure AD accede a msft.sts.microsoft.com a fin de obtener información de inicio de sesión.

   Compruebe las solicitudes de redes en la pestaña **Redes** del explorador para ver qué solicitudes de dominio se están bloqueando, repita el paso anterior para obtener su IP y agregue reglas de puerto de salida en el grupo de seguridad de red para permitir las solicitudes para esa IP. Si es posible, agregue la dirección URL y la IP al archivo host de la VM para corregir la resolución de DNS. Si conoce los intervalos IP exactos del dominio de inicio de sesión, también puede agregarlos directamente a las reglas de redes.

1. Ahora, el inicio de sesión de Azure AD debería realizarse correctamente. El portal de Azure Purview se cargará correctamente, pero el listado de todas las cuentas de Azure Purview no funcionará, ya que solo puede acceder a una cuenta de Azure Purview específica. Escriba `web.purview.azure.com/resource/{PurviewAccountName}` para visitar directamente la cuenta de Azure Purview para la que ha configurado correctamente un punto de conexión privado.

## <a name="deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources"></a>Implemente el entorno de ejecución de integración (IR) autohospedado y examine los orígenes de datos.
Una vez que implemente puntos de conexión privados de ingesta para Azure Purview, tiene que configurar y registrar al menos un entorno de ejecución de integración autohospedado:

- Actualmente, todos los tipos de orígenes locales, como Microsoft SQL Server, Oracle, SAP, etc., solo se admiten a través de exámenes basados en IR autohospedado. El IR autohospedado debe ejecutarse dentro de la red privada y, a continuación, emparejarse con la red virtual en Azure. 
   
- Para todos los tipos de origen de Azure, como Azure Blob Storage y Azure SQL Database, debe elegir explícitamente ejecutar el examen mediante un entorno de ejecución de integración autohospedado que se haya implementado en la misma red virtual que el punto de conexión privado de ingesta de Azure Purview. 

Siga los pasos descritos en [Creación y administración de un entorno de ejecución de integración autohospedado](manage-integration-runtimes.md) para configurar un entorno de ejecución de integración autohospedado. A continuación, configure el examen en el origen de Azure; para ello, elija el IR autohospedado en la lista desplegable **Conectar mediante el entorno de ejecución de integración** para garantizar el aislamiento de red.
    
   :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="Captura de pantalla en la que se muestra la ejecución de un examen de Azure con un IR autohospedado":::

> [!IMPORTANT]
> Si ha creado la cuenta de Azure Purview después del 18 de agosto de 2021, asegúrese de descargar e instalar la versión más reciente del entorno de ejecución de integración autohospedado desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="firewalls-to-restrict-public-access"></a>Firewalls para la restricción del acceso público

Para cortar completamente el acceso a la cuenta de Azure Purview desde la red pública de Internet, siga estos pasos. Esta configuración se aplica a las conexiones de punto de conexión privado y el punto de conexión privado de ingesta.

1. Vaya a la cuenta de Azure Purview desde Azure Portal y, en **Configuración** > **Redes**, seleccione **Conexiones de punto de conexión privado**.

1. Vaya a la pestaña **Firewall** y asegúrese de que el botón de alternancia esté establecido en **Denegar**.

   :::image type="content" source="media/catalog-private-link/private-endpoint-firewall.png" alt-text="Captura de pantalla que muestra la configuración del firewall del punto de conexión privado.":::

## <a name="next-steps"></a>Pasos siguientes

-  [Comprobación de la resolución de puntos de conexión privados](./catalog-private-link-name-resolution.md)
-  [Administración de orígenes de datos en Azure Purview](./manage-data-sources.md)
-  [Solución de problemas de configuración de punto de conexión privado de la cuenta de Azure Purview](./catalog-private-link-troubleshoot.md)
