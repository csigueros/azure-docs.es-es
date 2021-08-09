---
title: Solución de problemas de conectividad de red | Microsoft Docs
description: Se proporcionan sugerencias para solucionar errores comunes al usar Azure Migrate con puntos de conexión privados.
author: SGSneha
ms.author: v-ssudhir
ms.manager: deseelam
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/21/2021
ms.openlocfilehash: 9e987b4db88379e0dfe40b8839b073b893811fb4
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/27/2021
ms.locfileid: "110547946"
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

- Escriba _nslookup< nombre_de_la_cuenta_de_almacenamiento>_ .blob.core.windows.net.  Reemplace <nombre_de_la_cuenta_de_almacenamiento> por el nombre de la cuenta de almacenamiento usada en Azure Migrate.  

    Recibirá un mensaje similar a este:  

   ![Ejemploo de resolución DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- Se devuelve una dirección IP privada de 10.1.0.5 para la cuenta de almacenamiento. Esta dirección pertenece a la subred de la red virtual del punto de conexión privado.   

Puede comprobar la resolución DNS de otros artefactos de Azure Migrate mediante un enfoque similar.   

Si la resolución DNS es incorrecta, siga estos pasos:  

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
