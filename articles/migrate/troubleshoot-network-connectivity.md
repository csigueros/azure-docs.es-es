---
title: Solución de problemas de conectividad de red | Microsoft Docs
description: Se proporcionan sugerencias para solucionar errores comunes al usar Azure Migrate con puntos de conexión privados.
author: SGSneha
ms.author: v-ssudhir
ms.manager: deseelam
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/19/2021
ms.openlocfilehash: f6f63c24f98cd362619823ca4ebe1d66d35e6ced
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122598042"
---
# <a name="troubleshoot-network-connectivity"></a>Solución de problemas de conectividad de red
Este artículo le ayuda a solucionar problemas de conectividad de red mediante Azure Migrate con puntos de conexión privados.

## <a name="validate-private-endpoints-configuration"></a>Validación de la configuración de puntos de conexión privados

Asegúrese de que el punto de conexión privado es un estado aprobado.  

1. Vaya a la página **Azure Migrate**: **Discovery and Assessment** (Detección y evaluación) y **Server Migration** (Migración del servidor).

2. La página de propiedades contiene la lista de puntos de conexión privados y FQDN de vínculo privado creados automáticamente por Azure Migrate.  

3. Filtre y seleccione el punto de conexión privado que quiera diagnosticar.  
   a. Compruebe que el estado de la conexión es Aprobado.           
   b. Si la conexión está en estado Pendiente, debe aprobarla.                         
   c. También puede ir al recurso de punto de conexión privado y revisar si la red virtual coincide con la red virtual del punto de conexión privado del proyecto de migración.                                                        

     ![Visualización de la conexión del punto de conexión privado](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection.png)


## <a name="validate-the-data-flow-through-the-private-endpoints"></a>Validación del flujo de datos mediante los puntos de conexión privados
Revise las métricas del flujo de datos para comprobar el flujo de tráfico a través de puntos de conexión privados. Seleccione el punto de conexión privado en la página de propiedades de Azure Migrate: Server Assessment y Server Migration. Esto redirigirá a la sección de información general del punto de conexión privado de Azure Private Link Center. En el menú de la izquierda, seleccione **Métricas** para ver la información sobre _bytes de datos de entrada_ y _bytes de datos de salida_ para ver el flujo de tráfico.

## <a name="verify-dns-resolution"></a>Verificación de resolución de DNS

El dispositivo local (o proveedor de replicación) accederá a los recursos de Azure Migrate mediante sus nombres de dominio de vínculo privado (FQDN) completos. Puede requerir una configuración adicional de DNS para resolver la dirección IP privada de los puntos de conexión privados desde el entorno de origen. [Consulte este artículo](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder) para comprender los escenarios de configuración de DNS que pueden ayudar a solucionar cualquier problema de conectividad de red.  

Para validar la conexión del enlace privado, realice una resolución DNS de los puntos de conexión de recursos de Azure Migrate (FQDN de los recursos de vínculo privado) desde el servidor local en el que se aloja el dispositivo, y asegúrese de que se resuelve en una dirección IP privada.
Los detalles del punto de conexión privado y la información de los FQDN de recursos de vínculo privado están disponibles en las páginas de propiedades Detección y valoración, y Migración del servidor. Seleccione **Descargar configuración de DNS** para ver la lista.   

 ![Propiedades de Azure Migrate: Detección y evaluación](./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png)

 [![Página de propiedades de Azure Migrate: Server Migration](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties-expanded.png#lightbox)

Ejemplo ilustrativo de la resolución DNS del FQDN del vínculo privado de la cuenta de almacenamiento.  

- Escriba _nslookup ```<storage-account-name>_.blob.core.windows.net.``` Reemplace ```<storage-account-name>``` por el nombre de la cuenta de almacenamiento usada en Azure Migrate.  

    Recibirá un mensaje similar a este:  

   ![Ejemploo de resolución DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- Se devuelve una dirección IP privada de 10.1.0.5 para la cuenta de almacenamiento. Esta dirección pertenece a la subred de la red virtual del punto de conexión privado.   

Puede comprobar la resolución DNS de otros artefactos de Azure Migrate mediante un enfoque similar.   

Si la resolución DNS es incorrecta, siga estos pasos:  

**Recomendación** para la prueba: puede actualizar manualmente los registros DNS del entorno de origen editando el archivo de hosts DNS en el dispositivo local con los FQDN del recurso de vínculo privado y sus direcciones IP privadas asociadas.
- Si usa un DNS personalizado, revise la configuración personalizada de DNS y compruebe que esta sea correcta. Para más información, consulte [Introducción al punto de conexión privado: Configuración de DNS](../private-link/private-endpoint-overview.md#dns-configuration).
- Si usa servidores DNS proporcionados por Azure, consulte la sección siguiente para solucionar más problemas.  

> [!Tip]
> Puede actualizar manualmente los registros DNS del entorno de origen editando el archivo de hosts DNS en el dispositivo local con los FQDN del recurso de vínculo privado y sus direcciones IP privadas asociadas. Esta opción solo se recomienda para pruebas. <br/>  


## <a name="validate-the-private-dns-zone"></a>Valide la zona DNS privada   
Si la resolución DNS no funciona tal como se describe en la sección anterior, es posible que haya un problema con la zona DNS privada.  

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Confirmación de que existe el recurso de zona DNS privada requerido  
De forma predeterminada, Azure Migrate también crea una zona DNS privada correspondiente al subdominio *privatelink* para cada tipo de recurso. La zona DNS privada se creará en el mismo grupo de recursos de Azure que el grupo de recursos del punto de conexión privado. El grupo de recursos de Azure debe contener recursos de zona DNS privados con el formato siguiente:
- privatelink.vaultcore.azure.net para el almacén de claves
- privatelink.blob.core.windows.net para la cuenta de almacenamiento
- privatelink.siterecovery.windowsazure.com para el almacén de Recovery Services (para replicaciones basadas en agente y Hyper-V)
- privatelink.prod.migration.windowsazure.com: proyecto de migración, el proyecto de evaluación y el sitio de detección.   

Azure Migrate crea automáticamente la zona DNS privada (excepto para la cuenta de almacenamiento de caché o replicación seleccionada por el usuario). Para buscar la zona DNS privada vinculada, vaya a la página del punto de conexión privado y seleccione Configuraciones de DNS. Aquí, debería ver la zona DNS privada en la sección de integración de DNS privado.

[![Captura de pantalla de Configuración de DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration-expanded.png#lightbox)

Si la zona DNS no está presente (como se muestra a continuación), cree un nuevo [recurso de zona DNS privada.](../dns/private-dns-getstarted-portal.md)  

[![Creación de una zona DNS privada](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone-expanded.png#lightbox)

### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Confirme que la zona DNS privada está vinculada una red virtual.  
La zona DNS privada debe estar vinculada a la red virtual que contiene el punto de conexión privado de la consulta de DNS para resolver la dirección IP privada del punto de conexión del recurso. Si la zona DNS privada no está vinculada a la instancia de red virtual correcta, cualquier resolución DNS de esa instancia de red virtual omitirá la zona DNS privada.   

Vaya al recurso de zona DNS privada de Azure Portal y seleccione los vínculos de la red virtual en el menú de la izquierda. Debería ver las redes virtuales vinculadas.

[![Visualización de vínculos de red virtual](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links-expanded.png#lightbox)

Esta opción mostrará una lista de vínculos, cada uno con el nombre de una instancia de la red virtual de la suscripción. La instancia de red virtual que contiene el recurso de punto de conexión privado debe aparecer aquí. De lo contrario, [siga las instrucciones de este artículo](../dns/private-dns-getstarted-portal.md#link-the-virtual-network) para vincular la zona DNS privada a una red virtual.    

Una vez que la zona DNS privada esté vinculada a la instancia de red virtual, las solicitudes DNS procedentes de esa instancia de red virtual buscarán nombres en la zona DNS privada. Esto es necesario para la correcta resolución de direcciones que se realiza en las máquinas virtuales y que está conectada a la instancia de red virtual donde se creó el punto de conexión privado.   

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-records"></a>Confirmación de que la zona DNS privada contiene el registro A correcto

Vaya a la zona DNS privada que desea solucionar. La página Información general muestra todos los registros DNS de esa zona DNS privada. Compruebe que existe un registro DNS A para el recurso. El valor del registro A (la dirección IP) debe ser la dirección IP privada del recurso. Si encuentra el registro A con la dirección IP incorrecta, debe quitar la dirección IP equivocada y agregar una nueva. Se recomienda quitar todo el registro A y agregar uno nuevo, y realizar un vaciado de DNS en el dispositivo de origen local.   

Un ejemplo ilustrativo del registro DNS A de la cuenta de almacenamiento en la zona DNS privada:

![Registros DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-a-records.png)   

Un ejemplo ilustrativo de registros DNS A de microservicios de almacén de Recovery Services en la zona DNS privada:

[![Registros DNS para el almacén de Recovery Services](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records-expanded.png#lightbox)  

>[!Note]
> Cuando se quita o se modifica un registro A, es posible que la máquina siga resolviendo la dirección IP anterior porque el valor de TTL (período de vida) no ha expirado todavía.  

### <a name="items-that-may-affect-private-link-connectivity"></a>Elementos que pueden afectar a la conectividad de vínculo privado  

Esta es una lista no exhaustiva de elementos que se pueden encontrar en escenarios avanzados o complejos:

- Configuración del firewall, ya sea Azure Firewall conectado a Virtual Network o una solución de firewall personalizada que se implementa en el equipo.  
- Emparejamiento de redes, que puede afectar a los servidores DNS que se usan y a cómo se enruta el tráfico.  
- Las soluciones de puerta de enlace personalizada (NAT) pueden afectar a la forma en que se enruta el tráfico, incluido el tráfico de las consultas de DNS.

Para obtener más información, consulte la [guía de solución de problemas de conectividad de puntos de conexión privados](../private-link/troubleshoot-private-endpoint-connectivity.md).  

## <a name="common-issues-while-using-azure-migrate-with-private-endpoints"></a>Problemas comunes al usar Azure Migrate con puntos de conexión privados
En esta sección, enumeraremos algunos de los problemas que se producen habitualmente y sugeriremos pasos para solucionar el problema.

### <a name="appliance-registration-fails-with-the-error-forbiddentoaccesskeyvault"></a>Se produce un error en el registro del dispositivo con el error ForbiddenToAccessKeyVault
Error en la operación de creación o actualización de Azure Key Vault para <_KeyVaultName_> debido al error <_ErrorMessage_>
#### <a name="possible-causes"></a>Causas posibles:
Este problema puede producirse si la cuenta de Azure que se usa para registrar el dispositivo no tiene los permisos necesarios o el dispositivo de Azure Migrate no puede acceder a Key Vault.
#### <a name="remediation"></a>Corrección:
**Pasos para solucionar problemas de acceso a Key Vault:**
1. Asegúrese de que la cuenta de usuario de Azure que se ha usado para registrar el dispositivo tiene al menos el permiso de colaborador de la suscripción.
2. Asegúrese de que el usuario que intenta registrar el dispositivo tiene acceso al Key Vault y tiene una directiva de acceso asignada en la sección Key Vault>Directiva de acceso. [Más información](../key-vault/general/assign-access-policy-portal.md)
- [Más información](./migrate-appliance.md#appliance---vmware) sobre los roles y permisos de Azure necesarios.

**Pasos para solucionar problemas de conectividad con Key Vault:** si ha habilitado el dispositivo para la conectividad de punto de conexión privado, siga estos pasos para solucionar problemas de conectividad de red:
- Asegúrese de que el dispositivo se hospeda en la misma red virtual o está conectado a la red virtual de Azure de destino (donde se ha creado el punto de conexión privado de Key Vault) a través de un vínculo privado. El punto de conexión privado de Key Vault se creará en la red virtual seleccionada durante la experiencia de creación del proyecto. Puede comprobar los detalles de la red virtual en la página **Azure Migrate > Propiedades**.
![Propiedades de Azure Migrate](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-properties-page.png)  

- Asegúrese de que el dispositivo tiene conectividad de red con Key Vault a través de un vínculo privado. Para validar la conectividad del enlace privado, realice una resolución DNS del punto de conexión de recursos de Key Vault desde el servidor local en el que se hospeda el dispositivo y asegúrese de que se resuelve en una dirección IP privada.
- Vaya a **Azure Migrate: Discovery and assessment (Azure Migrate: Detección y evaluación) > Propiedades** para encontrar los detalles de los puntos de conexión privados para recursos como la instancia de Key Vault creada durante el paso de generación de claves.  

    ![Propiedades de evaluación del servidor de Azure Migrate](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-assessment-properties.png)  
- Seleccione **Download DNS settings** (Descargar configuración de DNS) para descargar las asignaciones DNS.

    ![Descarga de la configuración de DNS](./media/how-to-use-azure-migrate-with-private-endpoints/download-dns-settings.png)  

- Abra la línea de comandos y ejecute el siguiente comando nslookup para comprobar la conectividad de red con la dirección URL de Key Vault mencionada en el archivo de configuración DNS.   

    ```console
    nslookup <your-key-vault-name>.vault.azure.net
    ```

    Si ejecuta el comando de búsqueda ns para resolver la dirección IP de un almacén de claves sobre un punto de conexión público, verá un resultado similar al siguiente:

    ```console
    c:\ >nslookup <your-key-vault-name>.vault.azure.net

    Non-authoritative answer:
    Name:    
    Address:  (public IP address)
    Aliases:  <your-key-vault-name>.vault.azure.net
    ```

    Si ejecuta el comando de búsqueda ns para resolver la dirección IP de un almacén de claves sobre un punto de conexión privado, verá un resultado similar al siguiente:

    ```console
    c:\ >nslookup your_vault_name.vault.azure.net

    Non-authoritative answer:
    Name:    
    Address:  10.12.4.20 (private IP address)
    Aliases:  <your-key-vault-name>.vault.azure.net
              <your-key-vault-name>.privatelink.vaultcore.azure.net
    ```

    El comando nslookup debe resolverse en una dirección IP privada como se mencionó anteriormente. La dirección IP privada debe coincidir con la que aparece en el archivo de configuración DNS.

Si la resolución DNS es incorrecta, siga estos pasos:
1. Actualice manualmente los registros DNS del entorno de origen editando el archivo de hosts DNS en el dispositivo local con las asignaciones DNS y las direcciones IP privadas asociadas. Esta opción solo se recomienda para pruebas.

   ![Archivo de hosts DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-hosts-file-1.png)

2. Si usa un servidor DNS personalizado, revise la configuración personalizada de DNS y valide que esta sea correcta. Para más información, consulte [Introducción al punto de conexión privado: Configuración de DNS](../private-link/private-endpoint-overview.md#dns-configuration).
3. Si el problema persiste, [consulte esta sección](#validate-the-private-dns-zone) para obtener más información sobre la solución de problemas.

Después de comprobar la conectividad, vuelva a intentar el proceso de registro.

### <a name="start-discovery-fails-with-the-error-agentnotconnected"></a>Se produce un error al iniciar la detección con el error AgentNotConnected
El dispositivo no pudo iniciar la detección, ya que el agente local no puede comunicarse con el punto de conexión de servicio de Azure Migrate: <_NombreURL_> en Azure.

![Error de agente no conectado](./media/how-to-use-azure-migrate-with-private-endpoints/agent-not-connected-error.png)  

#### <a name="possible-causes"></a>Causas posibles:
Este problema puede producirse si el dispositivo no puede acceder a los puntos de conexión de servicio mencionados en el mensaje de error.
#### <a name="remediation"></a>Corrección:
Asegúrese de que el dispositivo tenga conectividad, ya sea directamente o a través de proxy, y que pueda resolver el punto de conexión de servicio proporcionado en el mensaje de error.  

Si ha habilitado la conectividad de punto de conexión privado en el dispositivo, asegúrese de que este esté conectado a la red virtual de Azure a través de un vínculo privado y pueda resolver los puntos de conexión de servicio proporcionados en el mensaje de error.

**Pasos para solucionar problemas de conectividad de vínculo privado con el servicio Azure Migrate:**

Si ha habilitado el dispositivo para la conectividad de punto de conexión privado, siga estos pasos para solucionar problemas de conectividad de red:

- Asegúrese de que el dispositivo se hospeda en la misma red virtual o está conectado a la red virtual de Azure de destino (donde se ha creado el punto de conexión privado) a través de un vínculo privado. Los puntos de conexión privados para los servicios Azure Migrate se crean en la red virtual seleccionada durante la experiencia de creación del proyecto. Puede comprobar los detalles de la red virtual en la página **Azure Migrate > Propiedades**.

![Propiedades de Azure Migrate](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-properties-page.png)   

- Asegúrese de que el dispositivo tiene conectividad de red con las direcciones URL del punto de conexión de servicio y otras direcciones URL, mencionadas en el mensaje de error, a través de una conexión de vínculo privado. Para validar la conectividad del vínculo privado, realice una resolución DNS de las direcciones URL del servidor local en el que se hospeda el dispositivo y asegúrese de que se resuelve en una dirección IP privada.
- Vaya a **Azure Migrate: Discovery and assessment (Azure Migrate: Detección y evaluación) > Propiedades** para encontrar los detalles de los puntos de conexión privados para los puntos de conexión de servicio creados durante el paso de generación de claves.  
    ![Propiedades de evaluación del servidor de Azure Migrate](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-assessment-properties.png)  
- Seleccione **Download DNS settings** (Descargar configuración de DNS) para descargar las asignaciones DNS.

    ![Descarga de la configuración de DNS](./media/how-to-use-azure-migrate-with-private-endpoints/download-dns-settings.png)   

|**Asignaciones de DNS que contienen direcciones URL de punto de conexión privado**  | **Detalles** |
|--- | ---|
|*.disc.privatelink.test.migration.windowsazure.com | Punto de conexión de servicio de detección de Azure Migrate
|*.asm.privatelink.test.migration.windowsazure.com  | Punto de conexión de servicio de evaluación de Azure Migrate  
|*.hub.privatelink.test.migration.windowsazure.com  | Punto de conexión del centro de Azure Migrate para recibir datos de otras ofertas de Microsoft o de [proveedores de software independientes (ISV)](./migrate-services-overview.md#isv-integration) externos
|*.vault.azure.net | Punto de conexión de Key Vault
|*.blob.core.windows.net | Punto de conexión de la cuenta de almacenamiento para los datos de dependencia y rendimiento  

Además de las direcciones URL anteriores, el dispositivo necesita acceso a las siguientes direcciones URL a través de Internet, directamente o a través de proxy.

| **Otras direcciones URL de nube pública <br> (direcciones URL de punto de conexión público)** | **Detalles** |
|--- | ---|
|*.portal.azure.com | Vaya a Azure Portal.
|\* .windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com <br/> *.microsoftonline.com <br/> *.microsoftonline-p.com <br/> | Azure Active Directory lo usa para la administración de identidades y el control de acceso
|management.azure.com | Para desencadenar implementaciones de Azure Resource Manager.
|*.services.visualstudio.com (opcional) | Cargue los registros del dispositivo que se usan para la supervisión interna.
|aka.ms/* (opcional) | Permita el acceso a los vínculos aka; se usa para descargar e instalar las actualizaciones más recientes de los servicios del dispositivo.
|download.microsoft.com/download | Permita descargas del centro de descargas de Microsoft.    

- Abra la línea de comandos y ejecute el siguiente comando nslookup para comprobar la conectividad del vínculo privado con las direcciones URL enumeradas en el archivo de configuración DNS. Repita este paso para todas las direcciones URL del archivo de configuración DNS.

    _**Ilustración**: Comprobación de la conectividad del vínculo privado con el punto de conexión del servicio de detección_

    ```console
    nslookup 04b8c9c73f3d477e966c8d00f352889c-agent.cus.disc.privatelink.prod.migration.windowsazure.com
    ```
    Si la solicitud puede llegar al punto de conexión del servicio de detección a través de un punto de conexión privado, verá un resultado similar al siguiente:

    ```console
    nslookup 04b8c9c73f3d477e966c8d00f352889c-agent.cus.disc.privatelink.prod.migration.windowsazure.com

    Non-authoritative answer:
    Name:    
    Address:  10.12.4.23 (private IP address)
    Aliases:  04b8c9c73f3d477e966c8d00f352889c-agent.cus.disc.privatelink.prod.migration.windowsazure.com
              prod.cus.discoverysrv.windowsazure.com
    ```

    El comando nslookup debe resolverse en una dirección IP privada como se mencionó anteriormente. La dirección IP privada debe coincidir con la que aparece en el archivo de configuración DNS.

Si la resolución DNS es incorrecta, siga estos pasos:
1. Actualice manualmente los registros DNS del entorno de origen editando el archivo de hosts DNS en el dispositivo local con las asignaciones DNS y las direcciones IP privadas asociadas. Esta opción solo se recomienda para pruebas.

   ![Archivo de hosts DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-hosts-file-1.png)

2. Si usa un servidor DNS personalizado, revise la configuración personalizada de DNS y valide que esta sea correcta. Para más información, consulte [Introducción al punto de conexión privado: Configuración de DNS](../private-link/private-endpoint-overview.md#dns-configuration).
3. Si el problema persiste, [consulte esta sección](#validate-the-private-dns-zone) para obtener más información sobre la solución de problemas.

Después de comprobar la conectividad, vuelva a intentar el proceso de detección.

### <a name="importexport-request-fails-with-the-error-403-this-request-is-not-authorized-to-perform-this-operation"></a>Se produce un error en la solicitud de importación o exportación con el error "403: Esta solicitud no está autorizada para realizar esta operación" 

Se produce un error en la solicitud de informe de exportación, importación o descarga con el error *"403: Esta solicitud no está autorizada para realizar esta operación"* para los proyectos con conectividad de punto de conexión privado.

#### <a name="possible-causes"></a>Causas posibles: 
Este error se puede producir si la solicitud de importación, exportación o descarga no se ha iniciado desde una red autorizada. Esto puede ocurrir si la solicitud de importación, exportación o descarga se ha iniciado desde un cliente que no está conectado al servicio Azure Migrate (red virtual de Azure) mediante una red privada. 

#### <a name="remediation"></a>Corrección
**Opción 1** *(recomendada)* :
  
Para resolver este error, vuelva a intentar la operación de importación, exportación o descarga desde un cliente que resida en una red virtual conectada a Azure mediante un vínculo privado. Puede abrir Azure Portal en la red local o en la máquina virtual del dispositivo y volver a intentar la operación. 

**Opción 2**:

La solicitud de importación, exportación o descarga realiza una conexión a una cuenta de almacenamiento para cargar o descargar informes. También puede cambiar la configuración de red de la cuenta de almacenamiento usada para la operación de importación, exportación o descarga, y permitir el acceso a la cuenta de almacenamiento por medio de otras redes (redes públicas).  

Para configurar la cuenta de almacenamiento para la conectividad de punto de conexión público,

1. **Busque la cuenta de almacenamiento**: el nombre de la cuenta de almacenamiento está disponible en la página de propiedades Azure Migrate: Detección y evaluación. El nombre de la cuenta de almacenamiento tendrá el sufijo *usa*. 

   :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png" alt-text="Instantánea de la configuración de DNS de descarga."::: 

2. Vaya a la cuenta de almacenamiento y edite las propiedades de red de la cuenta de almacenamiento para permitir el acceso desde todas las redes u otras redes. 

    :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/networking-firewall-virtual-networks.png" alt-text="Instantánea de las propiedades de red de la cuenta de almacenamiento.":::

3. Como alternativa, puede limitar el acceso a redes concretas y agregar la dirección IP pública del cliente desde donde intente acceder a Azure Portal.  

    :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/networking-firewall.png" alt-text="Instantánea de adición de la dirección IP pública del cliente.":::
