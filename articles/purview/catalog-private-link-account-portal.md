---
title: Conexión de forma privada y segura a la cuenta de Purview
description: En este artículo se describe cómo puede configurar un punto de conexión privado para conectarse a su cuenta de Purview desde una red restringida.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 0da3f53c41296f3cb467c00bb13649288ebd53c6
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213848"
---
# <a name="connect-privately-and-securely-to-your-purview-account"></a>Conexión de forma privada y segura a la cuenta de Purview
En esta guía, aprenderá a implementar puntos de conexión privados para su cuenta de Purview, lo que le permitirá conectarse a su cuenta de Azure Purview solo desde redes virtuales y privadas. Para lograr este objetivo, debe implementar puntos de conexión privados de la _cuenta_ y del _portal_ para la cuenta de Azure Purview.

El punto de conexión privado de la _cuenta_ de Azure Purview se usa para agregar una capa adicional de seguridad mediante la habilitación de escenarios en que solo las llamadas de cliente procedentes de la red virtual puedan acceder a dicha cuenta. Este punto de conexión privado también es un requisito previo para el punto de conexión privado del portal.

El punto de conexión privado del _portal_ de Azure Purview es necesario para habilitar la conectividad a [Azure Purview Studio](https://web.purview.azure.com/resource/) mediante una red privada.

> [!NOTE]
> Si solo crea puntos de conexión privados de la _cuenta_ y del _portal_, no podrá ejecutar ningún examen. Para habilitar el examen en una red privada, deberá [crear también un punto de conexión privado de ingesta](catalog-private-link-end-to-end.md).

   :::image type="content" source="media/catalog-private-link/purview-private-link-account-portal.png" alt-text="Diagrama que muestra la arquitectura de Azure Purview y Private Link.":::

Para obtener más información sobre el servicio Azure Private Link, consulte el tema sobre [vínculos privados y puntos de conexión privados](../private-link/private-endpoint-overview.md) para obtener más información.

## <a name="deployment-checklist"></a>Lista de comprobación de la implementación
Con una de las opciones de implementación de esta guía, puede implementar una nueva cuenta de Azure Purview con puntos de conexión privados de _cuenta_ y _portal_, o bien puede optar por implementar estos puntos de conexión privados para una cuenta de Azure Purview existente:

1. Elija una red virtual de Azure adecuada y una subred para implementar puntos de conexión privados de Azure Purview. Seleccione una de las siguientes opciones:
   - Implemente una [nueva red virtual ](../virtual-network/quick-create-portal.md) en su suscripción de Azure.
   - Busque una red virtual de Azure existente y una subred en la suscripción de Azure.
  
2. Defina un [método de resolución de nombres DNS](./catalog-private-link-name-resolution.md#deployment-options) adecuado, de modo que la cuenta de Azure Purview y el portal web sean accesibles a través de direcciones IP privadas. Puede usar cualquiera de las opciones siguientes:
   - Implemente nuevas zonas de Azure DNS mediante los pasos que se explican más adelante en esta guía.
   - Agregue los registros DNS necesarios a las zonas de Azure DNS existentes mediante los pasos que se explican más adelante en esta guía.
   - Después de completar los pasos de esta guía, agregue manualmente los registros DNS A necesarios a los servidores DNS existentes.
3. Implemente una [nueva cuenta de Purview](#option-1---deploy-a-new-azure-purview-account-with-account-and-portal-private-endpoints) con puntos de conexión privados de cuenta y portal, o bien implemente puntos de conexión privados de cuenta y portal para una [cuenta de Purview existente](#option-2---enable-account-and-portal-private-endpoint-on-existing-azure-purview-accounts).
4. [Habilite el acceso a Azure Active Directory](#enable-access-to-azure-active-directory) si la red privada tiene reglas de grupo de seguridad de red establecidas para denegar todo el tráfico público de Internet.
5. Cuando complete esta guía, ajuste las configuraciones de DNS si es necesario.
6. Valide la red y la resolución de nombres de la máquina de administración en Azure Purview. 

## <a name="option-1---deploy-a-new-azure-purview-account-with-_account_-and-_portal_-private-endpoints"></a>Opción 1: Implementación de una nueva cuenta de Azure Purview con puntos de conexión privados de _cuenta_ y _portal_

1. Vaya a [Azure Portal](https://portal.azure.com) y, después, diríjase a la página **Cuentas de Purview**. Seleccione **+ Crear** para crear la cuenta de Azure Purview.

2. Rellene la información básica y, en la pestaña **Redes**, establezca el método de conectividad como **Punto de conexión privado**. Establezca el punto de conexión privado en **Account and portal only** (Solo cuenta y portal).

3. Seleccione **Account and portal** (Cuenta y portal) seleccione **+ Agregar** para agregar un punto de conexión privado para la cuenta de Azure Purview.

   :::image type="content" source="media/catalog-private-link/purview-pe-deploy-account-portal.png" alt-text="Captura de pantalla que muestra cómo crear un punto de conexión privado para las selecciones de página de cuenta y portal.":::

4. En la página **Crear un punto de conexión privado**, en **Purview sub-resource** (Subrecurso de Purview), elija su ubicación, proporcione un nombre para el punto de conexión privado de la _cuenta_ y seleccione **cuenta**. En **Redes**, seleccione la red virtual y la subred y, opcionalmente, seleccione **Integrar con la zona DNS privada** para crear una nueva zona DNS privada de Azure. 
   
   :::image type="content" source="media/catalog-private-link/purview-pe-deploy-account.png" alt-text="Captura de pantalla que muestra la página de creación de un punto de conexión privado de la cuenta.":::

      > [!NOTE]
      > También puede usar las zonas DNS privadas de Azure existentes o crear registros de DNS en los servidores DNS manualmente más adelante. Para obtener más información, consulte [Configuración de la resolución de nombres DNS para puntos de conexión privados](./catalog-private-link-name-resolution.md).

5. Seleccione **Aceptar**.
   
6. En el asistente **Create Purview account** (Crear cuenta de Purview), seleccione **+Agregar** de nuevo para agregar el punto de conexión privado del _portal_.
     
7. En la **página Crear un punto de conexión privado**, en **Purview sub-resource** (Subrecurso de Purview), elija su ubicación, proporcione un nombre para el punto de conexión privado del _portal_ y seleccione **portal**. En **Redes**, seleccione la red virtual y la subred y, opcionalmente, seleccione **Integrar con la zona DNS privada** para crear una nueva zona DNS privada de Azure. 

   :::image type="content" source="media/catalog-private-link/purview-pe-deploy-portal.png" alt-text="Captura de pantalla que muestra la página de creación de un punto de conexión privado del portal.":::
   
   > [!NOTE]
   > También puede usar las zonas DNS privadas de Azure existentes o crear registros de DNS en los servidores DNS manualmente más adelante. Para obtener más información, consulte [Configuración de la resolución de nombres DNS para puntos de conexión privados](./catalog-private-link-name-resolution.md).

8.  Seleccione **Aceptar**.
   
9.  Seleccione **Revisar + crear**. En la página **Revisar y crear**, Azure valida la configuración.
      
      :::image type="content" source="media/catalog-private-link/purview-pe-deploy-account-portal-2.png" alt-text="Captura de pantalla que muestra la página de revisión de creación del punto de conexión privado.":::


10. Cuando vea el mensaje "Validación superada", seleccione **Crear**.

## <a name="option-2---enable-_account_-and-_portal_-private-endpoint-on-existing-azure-purview-accounts"></a>Opción 2: Habilitación del punto de conexión privado de _cuenta_ y _portal_ en cuentas de Azure Purview existentes

Hay dos maneras de agregar puntos de conexión privados de _cuenta_ y _portal_ de Azure Purview para una cuenta de Azure Purview existente:

- Usar Azure Portal (cuenta de Azure Purview).
- Usar Private Link Center.

### <a name="use-the-azure-portal-azure-purview-account"></a>Uso de Azure Portal (cuenta de Azure Purview)

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

### <a name="use-the-private-link-center"></a>Uso de Private Link Center

1. Vaya a [Azure Portal](https://portal.azure.com).

1. En la barra de búsqueda de la parte superior de la página, busque **private link** y seleccione la primera opción para navegar hasta el panel **Private Link**.

1. Seleccione **+ Agregar** y rellene los detalles básicos.

   :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="Captura de pantalla que muestra la creación de puntos de conexión privados desde Private Link Center.":::

1. En **Recurso**, seleccione la cuenta de Azure Purview ya creada. Seleccione **cuenta** como **Subrecurso de destino**.

1. En la pestaña **Configuración**, seleccione la red virtual y la zona DNS privada. Vaya a la página de resumen y seleccione **Crear** para crear el punto de conexión privado de la cuenta.

> [!NOTE]
> Siga los mismos pasos al seleccionar el **portal** como **Subrecurso de destino**.

## <a name="enable-access-to-azure-active-directory"></a>Habilitar el acceso a Azure Active Directory

> [!NOTE]
> Si la VM, VPN Gateway o la puerta de enlace de Emparejamiento de VNet tienen acceso público a Internet, puede acceder al portal de Azure Purview y a la cuenta de Azure Purview habilitada con puntos de conexión privados. Por este motivo, no es necesario seguir el resto de instrucciones. Si la red privada tiene reglas de grupo de seguridad de red establecidas para denegar todo el tráfico de Internet público, tendrá que agregar algunas reglas para habilitar el acceso a Azure Active Directory (Azure AD). Siga las instrucciones para hacerlo.

Estas instrucciones se proporcionan para acceder de forma segura a Azure Purview desde una VM de Azure. Deben seguirse pasos similares si usa VPN u otras puertas de enlace de Emparejamiento de VNet.

1. Vaya a su VM en Azure Portal y, en **Configuración**, seleccione **Redes**. A continuación, seleccione **Reglas de puerto de salida**, **Agregar regla de puerto de salida**.

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

## <a name="next-steps"></a>Pasos siguientes

-  [Comprobación de la resolución de puntos de conexión privados](./catalog-private-link-name-resolution.md)
-  [Administración de orígenes de datos en Azure Purview](./manage-data-sources.md)
-  [Solución de problemas de configuración de punto de conexión privado de la cuenta de Azure Purview](./catalog-private-link-troubleshoot.md)
