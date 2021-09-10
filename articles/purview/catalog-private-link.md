---
title: Uso de puntos de conexión privados para el acceso seguro a Azure Purview
description: En este artículo se describe cómo puede configurar un punto de conexión privado para la cuenta de Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/11/2021
ms.openlocfilehash: 8a5fb386cfbf8c31abc92d75d49854ca237d4978
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121721991"
---
# <a name="use-private-endpoints-for-your-azure-purview-account"></a>Uso de puntos de conexión privados para la cuenta de Azure Purview

Puede usar puntos de conexión privados para sus cuentas de Azure Purview para que los clientes y usuarios de una red virtual (VNet) puedan acceder de forma segura al catálogo a través de una instancia de Azure Private Link. El punto de conexión privado usa una dirección IP del espacio de direcciones de la red virtual para la cuenta de Azure Purview. El tráfico de red entre los clientes de la red virtual y la cuenta de Azure Purview atraviesa la red virtual y un vínculo privado de la red troncal de Microsoft. Esta solución elimina la exposición a la red pública de Internet.

   :::image type="content" source="media/catalog-private-link/purview-private-link-architecture.png" alt-text="Diagrama que muestra la arquitectura de Azure Purview y Private Link.":::

Revise las [Preguntas frecuentes sobre los puntos de conexión privados de Azure Purview](./catalog-private-link-faqs.md).

## <a name="create-an-azure-purview-account-with-a-private-endpoint"></a>Creación de una cuenta de Azure Purview con un punto de conexión privado

1. Vaya a [Azure Portal](https://portal.azure.com) y, después, diríjase a la cuenta de Azure Purview.

1. Rellene la información básica y establezca el método de conectividad como **Punto de conexión privado** en la pestaña **Redes**. Para configurar los puntos de conexión privados de ingesta, indique los detalles de **Suscripción**, **Red virtual** y **Subred** que quiera emparejar con el punto de conexión privado.

   > [!NOTE]
   > Cree un punto de conexión privado de ingesta solo si desea habilitar el aislamiento de red para escenarios de análisis de un extremo a otro, tanto para los orígenes locales como de Azure. Actualmente no se admiten los puntos de conexión privados de ingesta que funcionan con orígenes de AWS.

   :::image type="content" source="media/catalog-private-link/create-pe-azure-portal.png" alt-text="Captura de pantalla que muestra cómo crear un punto de conexión privado en Azure Portal.":::

1. Opcionalmente, configure una **Zona DNS privada** para cada punto de conexión privado de ingesta.

1. Seleccione **+ Agregar** para agregar un punto de conexión privado para la cuenta de Azure Purview.

1. En la página **Crear un punto de conexión privado**, como **subrecurso de Purview**, seleccione **cuenta**. Elija su red y subred virtual y seleccione la zona DNS privada en la que se registrará el DNS. También se pueden usar los servidores DNS propios o bien crear registros DNS con los archivos host de las máquinas virtuales.

    :::image type="content" source="media/catalog-private-link/create-pe-account.png" alt-text="Captura de pantalla que muestra las selecciones de la página Crear un punto de conexión privado.":::

1. Seleccione **Aceptar**.

1. Seleccione **Revisar + crear**. En la página **Revisar y crear**, Azure valida la configuración.

1. Cuando vea el mensaje "Validación superada", seleccione **Crear**.

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="Captura de pantalla que muestra la validación superada para la creación de cuentas.":::

## <a name="create-a-private-endpoint-for-the-azure-purview-web-portal"></a>Creación de un punto de conexión privado para el portal web de Azure Purview

1. Vaya a la cuenta de Azure Purview que ha creado y, en **Configuración**, seleccione **Redes** > **Conexiones de punto de conexión privado**.

1. Seleccione **+ Punto de conexión privado** para crear uno.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Captura de pantalla que muestra cómo crear un punto de conexión privado del portal.":::

1. Rellene la información básica.

1. En la pestaña **Recurso**, seleccione **Microsoft.Purview/accounts** como **Tipo de recurso**.

1. En **Recurso**, seleccione la cuenta de Azure Purview recién creada. Seleccione **portal** como **Subrecurso de destino**.
    :::image type="content" source="media/catalog-private-link/pe-portal-details.png" alt-text="Captura de pantalla que muestra los detalles del punto de conexión privado del portal.":::

1. En la pestaña **Configuración**, seleccione la red virtual y la zona DNS privada. Vaya a la página de resumen y seleccione **Crear** para crear el punto de conexión privado del portal.

## <a name="private-dns-zone-requirements-for-private-endpoints"></a>Requisitos de zona DNS privada para los puntos de conexión privados

Al crear un punto de conexión privado, el registro del recurso CNAME de DNS de Azure Purview se actualiza a un alias de un subdominio con el prefijo `privatelink`. De forma predeterminada, también se crea una [zona DNS privada](../dns/private-dns-overview.md), que se corresponde con el subdominio `privatelink`, con los registros de recursos A de DNS para los puntos de conexión privados.

Cuando se resuelve la dirección URL del punto de conexión de Azure Purview desde fuera de la red virtual con el punto de conexión privado, se resuelve en el punto de conexión público de Azure Purview. Cuando se resuelve desde la red virtual que hospeda el punto de conexión privado, la dirección URL del punto de conexión de Azure Purview se resuelve en la dirección IP del punto de conexión privado.

Por ejemplo, si un nombre de cuenta de Azure Purview es "PurviewA", cuando se resuelve desde fuera de la red virtual que hospeda el punto de conexión privado, será:

| Nombre | Tipo | Value |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | CNAME | \<Purview public endpoint\> |
| \<Purview public endpoint\> | A | \<Purview public IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview public endpoint\> |

Los registros de recursos DNS para PurviewA, cuando se resuelvan en la red virtual que hospeda el punto de conexión privado, serán:

| Nombre | Tipo | Value |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | A | \<private endpoint IP address\> |
| `Web.purview.azure.com` | CNAME | \<private endpoint IP address\> |

<br>

 > [!Important]
 > Si no usa reenviadores DNS y, en su lugar, administra registros A directamente en los servidores DNS locales para resolver los puntos de conexión a través de sus direcciones IP privadas, es posible que tenga que crear registros A adicionales en los servidores DNS.

| Nombre | Tipo | Value |
| ---------- | -------- | --------------- |
| `PurviewA.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview> |
| `PurviewA.scan.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview> |
| `PurviewA.catalog.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.proxy.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.guardian.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.manifest.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.cdn.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.hub.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.catalog.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.cseo.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datascan.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datashare.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datasource.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.policy.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.sensitivity.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |

<br> 

En el ejemplo siguiente se muestra la resolución de nombres DNS de Azure Purview desde fuera de la red virtual o cuando no hay un punto de conexión privado de Azure configurado.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-external.png" alt-text="Captura de pantalla que muestra la resolución de nombres de Azure Purview desde fuera de CorpNet.":::

En el ejemplo siguiente se muestra la resolución de nombres DNS de Azure Purview desde dentro de la red virtual.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-private-link.png" alt-text="Captura de pantalla que muestra la resolución de nombres de Purview desde dentro de CorpNet.":::

Es importante definir correctamente la configuración de DNS para resolver la dirección IP del punto de conexión privado en el nombre de dominio completo (FQDN) de la cadena de conexión.

Si está usando un servidor DNS personalizado en la red, los clientes deben ser capaces de resolver el FQDN del punto de conexión de Azure Purview en la dirección IP del punto de conexión privado. Configure el servidor DNS para delegar el subdominio de Private Link en la zona DNS privada de la red virtual. O bien, configure los registros A para `PurviewA.privatelink.purview.azure.com` con la dirección IP del punto de conexión privado.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-diagram.png" alt-text="Diagrama que muestra la resolución de nombres de Azure Purview.":::

Para obtener más información, consulte [Configuración de DNS para puntos de conexión privados de Azure](../private-link/private-endpoint-dns.md).

## <a name="enable-access-to-azure-active-directory"></a>Habilitar el acceso a Azure Active Directory

> [!NOTE]
> Si la máquina virtual, la puerta de enlace de VPN o la puerta de enlace de Emparejamiento de VNet tienen acceso público a Internet, puede acceder al portal de Azure Purview y a la cuenta de Azure Purview habilitada con puntos de conexión privados. Por este motivo, no es necesario seguir el resto de instrucciones. Si la red privada tiene reglas de grupo de seguridad de red establecidas para denegar todo el tráfico de Internet público, tendrá que agregar algunas reglas para habilitar el acceso a Azure Active Directory (Azure AD). Siga las instrucciones para hacerlo.

Estas instrucciones se proporcionan para acceder de forma segura a Azure Purview desde una máquina virtual de Azure. Deben seguirse pasos similares si usa VPN u otras puertas de enlace de emparejamiento de red virtual.

1. Vaya a su máquina virtual en Azure Portal y, en **Configuración**, seleccione **Redes**. A continuación, seleccione **Reglas de puerto de salida** > **Agregar regla de puerto de salida**.

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

1. Conéctese a la máquina virtual y abra el explorador. Seleccione Ctrl+Mayús+J para ir a la consola del explorador y cambie a la pestaña de red para supervisar las solicitudes de red. Escriba web.purview.azure.com en el cuadro de la dirección URL e intente iniciar sesión con las credenciales de Azure AD. Es posible que se produzca un error de inicio de sesión y que en la pestaña **Red** de la consola vea que Azure AD intenta acceder a aadcdn.msauth.net, pero se bloquea.

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="Captura de pantalla que muestra los detalles del error de inicio de sesión.":::

1. En este caso, abra un símbolo del sistema en la máquina virtual, haga ping a aadcdn.msauth.net, obtenga su dirección IP y, a continuación, agregue una regla de puerto de salida para la IP en las reglas de seguridad de red de la máquina virtual. Establezca **Destino** en **Dirección IP** y establezca las **Direcciones IP de destino** en la dirección IP de aadcdn. Debido a Azure Load Balancer y Azure Traffic Manager, la dirección IP de Azure AD Content Delivery Network puede ser dinámica. Después de obtener la dirección IP, es mejor agregarla al archivo host de la VM para forzar al explorador a visitar esa IP para conectarse con Azure AD Content Delivery Network.

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="Captura de pantalla que muestra el ping de prueba.":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="Captura de pantalla que muestra la regla de Content Delivery Network de Azure AD.":::

1. Una vez creada la regla, vuelva a la VM e intente volver a iniciar sesión con las credenciales de Azure AD. Si logra iniciar sesión, el portal de Azure Purview está listo para usar. Sin embargo, en algunos casos, Azure AD redirige a otros dominios para iniciar sesión en función del tipo de cuenta de un cliente. Por ejemplo, para una cuenta live.com, Azure AD redirige a live.com para iniciar sesión y, a continuación, esas solicitudes se bloquean de nuevo. Para las cuentas de empleado de Microsoft, Azure AD accede a msft.sts.microsoft.com a fin de obtener información de inicio de sesión.

   Compruebe las solicitudes de redes en la pestaña **Redes** del explorador para ver qué solicitudes de dominio se están bloqueando, repita el paso anterior para obtener su IP y agregue reglas de puerto de salida en el grupo de seguridad de red para permitir las solicitudes para esa IP. Si es posible, agregue la dirección URL y la IP al archivo host de la VM para corregir la resolución de DNS. Si conoce los intervalos de IP exactos del dominio de inicio de sesión, también puede agregarlos directamente a las reglas de red.

1. Ahora, el inicio de sesión de Azure AD debería realizarse correctamente. El portal de Azure Purview se cargará correctamente, pero el listado de todas las cuentas de Azure Purview no funcionará, ya que solo puede acceder a una cuenta de Azure Purview específica. Escriba `web.purview.azure.com/resource/{PurviewAccountName}` para visitar directamente la cuenta de Azure Purview para la que ha configurado correctamente un punto de conexión privado.

## <a name="ingestion-private-endpoints-and-scanning-sources"></a>Puntos de conexión privados de ingesta y examen de orígenes

Debe examinar los orígenes en las redes privadas, redes virtuales y detrás de puntos de conexión privados. Para garantizar el aislamiento de red de los metadatos que fluyen desde el origen que se está examinando hasta la instancia de DataMap de Azure Purview:

1. Siga los pasos de [esta sección](#create-an-ingestion-private-endpoint) para habilitar un punto de conexión privado de ingesta.

1. Examine el origen con un entorno de ejecución de integración (IR) autohospedado.

    1. Actualmente, todos los tipos de orígenes locales como Azure SQL Server, Oracle, SAP, etc., solo se admiten a través de exámenes basados en IR autohospedado. El IR autohospedado debe ejecutarse dentro de la red privada y, a continuación, emparejarse con la red virtual en Azure. Siga [estos pasos](#create-an-ingestion-private-endpoint) para habilitar su red virtual de Azure en el punto de conexión privado de ingesta.

    2. En el caso de todos los tipos de origen de Azure, como Azure Blob Storage y Azure SQL Database, debe elegir explícitamente la ejecución del examen mediante un IR autohospedado para garantizar el aislamiento de red. Siga los pasos descritos en [Creación y administración de un entorno de ejecución de integración autohospedado](manage-integration-runtimes.md) para configurar un entorno de ejecución de integración autohospedado. A continuación, configure el examen en el origen de Azure; para ello, elija el IR autohospedado en la lista desplegable **Conectar mediante el entorno de ejecución de integración** para garantizar el aislamiento de red.
    
       :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="Captura de pantalla que muestra la ejecución de un examen de Azure con IR autohospedado.":::

> [!NOTE]
> Cuando se usa un punto de conexión privado para la ingesta, puede usar una instancia de Azure Integration Runtime para examinar solo los orígenes de datos siguientes:
>
> - Azure Blob Storage
> - Azure Data Lake Gen 2
>
> Para otros orígenes de datos, se requiere un IR autohospedado. Actualmente no se admite el método de credenciales de MSI cuando se examinan los orígenes de Azure mediante un IR autohospedado. Debe usar alguno de los otros métodos de credenciales admitidos para ese origen de Azure.

## <a name="enable-a-private-endpoint-on-existing-azure-purview-accounts"></a>Habilitación de un punto de conexión privado en cuentas de Azure Purview existentes

Hay dos maneras de agregar puntos de conexión privados de Azure Purview después de crear la cuenta de Azure Purview:

- Use Azure Portal (cuenta de Azure Purview).
- Use Private Link Center.

### <a name="use-the-azure-portal-azure-purview-account"></a>Uso de Azure Portal (cuenta de Azure Purview)

1. Vaya a la cuenta de Azure Purview desde Azure Portal y, en **Configuración** > **Redes**, seleccione **Conexiones de punto de conexión privado**.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Captura de pantalla que muestra cómo crear un punto de conexión privado de la cuenta.":::

1. Seleccione **+ Punto de conexión privado** para crear uno.

1. Rellene la información básica.

1. En la pestaña **Recurso**, seleccione **Microsoft.Purview/accounts** como **Tipo de recurso**.

1. En **Recurso**, seleccione la cuenta de Azure Purview y en **Subrecurso de destino**, seleccione **cuenta**.

1. En la pestaña **Configuración**, seleccione la red virtual y la zona DNS privada. Vaya a la página de resumen y seleccione **Crear** para crear el punto de conexión privado de la cuenta.

> [!NOTE]
> Siga los mismos pasos al seleccionar el **portal** como **Subrecurso de destino**.

#### <a name="create-an-ingestion-private-endpoint"></a>Creación de un punto de conexión privado de ingesta

1. Vaya a la cuenta de Azure Purview desde Azure Portal y, en **Configuración** > **Redes**, seleccione **Conexiones de punto de conexión privado**.

1. Vaya a la pestaña **Ingestion private endpoint connections** (Conexiones de punto de conexión privado de ingesta) y seleccione **+Nuevo** para crear un punto de conexión privado de ingesta.

1. Rellene la información básica y los detalles de la red virtual.

   :::image type="content" source="media/catalog-private-link/ingestion-pe-fill-details.png" alt-text="Captura de pantalla que muestra cómo se rellenan los detalles del punto de conexión privado.":::

1. Seleccione **Crear** para finalizar la configuración.

> [!NOTE]
> Los puntos de conexión privados de la ingesta solo se pueden crear a través de la experiencia del portal de Azure Purview que se describe en los pasos anteriores. No se pueden crear desde Private Link Center.

### <a name="use-the-private-link-center"></a>Uso de Private Link Center

1. Vaya a [Azure Portal](https://portal.azure.com).

1. En la barra de búsqueda de la parte superior de la página, busque **private link** y seleccione la primera opción para navegar hasta el panel **Private Link**.

1. Seleccione **+ Agregar** y rellene los detalles básicos.

   :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="Captura de pantalla que muestra la creación de puntos de conexión privados desde Private Link Center.":::

1. En **Recurso**, seleccione la cuenta de Azure Purview ya creada. Seleccione **cuenta** como **Subrecurso de destino**.

1. En la pestaña **Configuración**, seleccione la red virtual y la zona DNS privada. Vaya a la página de resumen y seleccione **Crear** para crear el punto de conexión privado de la cuenta.

> [!NOTE]
> Siga los mismos pasos al seleccionar el **portal** como **Subrecurso de destino**.

## <a name="firewalls-to-restrict-public-access"></a>Firewalls para la restricción del acceso público

Para cortar completamente el acceso a la cuenta de Azure Purview desde la red pública de Internet, siga estos pasos. Esta configuración se aplica a las conexiones de punto de conexión privado y el punto de conexión privado de ingesta.

1. Vaya a la cuenta de Azure Purview desde Azure Portal y, en **Configuración** > **Redes**, seleccione **Conexiones de punto de conexión privado**.

1. Vaya a la pestaña **Firewall** y asegúrese de que el botón de alternancia esté establecido en **Denegar**.

   :::image type="content" source="media/catalog-private-link/private-endpoint-firewall.png" alt-text="Captura de pantalla que muestra la configuración del firewall del punto de conexión privado.":::

## <a name="next-steps"></a>Pasos siguientes

- [Navegación por el catálogo de datos de Azure Purview](how-to-browse-catalog.md)
- [Búsqueda en el catálogo de datos de Azure Purview](how-to-search-catalog.md)
