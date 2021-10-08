---
title: Introducción a los puntos de conexión privados
description: Comprenda el uso de los puntos de conexión privados para Azure Backup y los escenarios en los que el uso de puntos de conexión privados ayuda a preservar la seguridad de los recursos.
ms.topic: conceptual
ms.date: 09/28/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3070cb72b6e5949b94972f9dad54d4e57e5bf591
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154971"
---
# <a name="overview-and-concepts-of-private-endpoints-for-azure-backup"></a>Introducción y conceptos de puntos de conexión privados para Azure Backup

Azure Backup permite realizar copias de seguridad de los datos y restaurarlos de forma segura desde los almacenes de Recovery Services mediante [puntos de conexión privados](../private-link/private-endpoint-overview.md). Los puntos de conexión privados usan una o más direcciones IP privadas de la red virtual de Azure (VNet) para incorporar el servicio de manera eficaz a VNet.

Este artículo le ayudará a comprender cómo funcionan los puntos de conexión privados para Azure Backup y los escenarios en los que el uso de puntos de conexión privados ayuda a preservar la seguridad de los recursos.

## <a name="before-you-start"></a>Antes de comenzar

- Los puntos de conexión privados solo se pueden crear para almacenes de Recovery Services nuevos (que no tienen elementos registrados). Por lo tanto, los puntos de conexión privados deben crearse antes de intentar proteger los elementos en el almacén.
- Una red virtual puede contener puntos de conexión privados para varios almacenes de Recovery Services. Además, un almacén de Recovery Services puede tener puntos de conexión privados en varias redes virtuales. Sin embargo, el número máximo de puntos de conexión privados que se pueden crear para un almacén es 12.
- Una vez que se crea un punto de conexión privado para un almacén, el almacén se bloqueará. No será accesible (para copias de seguridad y restauraciones) desde redes que no contengan un punto de conexión privado para el almacén. Si se quitan todos los puntos de conexión privados para el almacén, se podrá acceder al almacén desde todas las redes.
- Una conexión de punto de conexión privado para la copia de seguridad usa un total de 11 direcciones IP privadas de la subred, incluidas las que usa Azure Backup para el almacenamiento. Este número puede ser superior (hasta veinticinco) para ciertas regiones de Azure. Por lo tanto, se recomienda disponer de suficientes direcciones IP privadas cuando intente crear puntos de conexión privados para la copia de seguridad.
- Aunque tanto Azure Backup como Azure Site Recovery usan un almacén de Recovery Services, en este artículo solo se describe el uso de puntos de conexión privados para Azure Backup.
- Los puntos de conexión privados de Backup no incluyen el acceso a Azure Active Directory (Azure AD) y es necesario garantizar lo mismo por separado. Por tanto, las direcciones IP y los FQDN necesarios para que Azure AD funcione en una región necesitarán acceso de salida desde la red protegida al realizar copias de seguridad de bases de datos en máquinas virtuales de Azure y copias de seguridad mediante el agente de MARS. También puede usar etiquetas NSG y etiquetas de Azure Firewall para permitir el acceso a Azure AD, según corresponda.
- No se admiten redes virtuales con directivas de red para los puntos de conexión privados. Deberá [deshabilitar las directivas de red](../private-link/disable-private-endpoint-network-policy.md) antes de continuar.
- Debe volver a registrar el proveedor de recursos de Recovery Services con la suscripción si lo registró antes del 1 de mayo de 2020. Para volver a registrar el proveedor, vaya a la suscripción en Azure Portal, haga clic en el **Proveedor de recursos** en la barra de navegación izquierda, seleccione **Microsoft.RecoveryServices** y **Volver a registrar**.
- Si el almacén tiene habilitados los puntos de conexión privados, no se puede realizar la [restauración entre regiones](backup-create-rs-vault.md#set-cross-region-restore) de las copias de seguridad de bases de datos SQL y SAP HANA.
- Al trasladar un almacén de Recovery Services que ya usa puntos de conexión privados a un nuevo inquilino, deberá actualizar el almacén de Recovery Services para volver a crear y configurar la identidad administrada del almacén y crear nuevos puntos de conexión privados según sea necesario (que deben estar en el nuevo inquilino). Si no lo hace, las operaciones de copia de seguridad y restauración comenzarán a generar errores. Además, los permisos del control de acceso basado en roles de Azure (Azure RBAC) que se hayan configurado en la suscripción deberán volver a configurarse.

## <a name="recommended-and-supported-scenarios"></a>Escenarios recomendados y admitidos

Aunque los puntos de conexión privados están habilitados para el almacén, solo se usan para la copia de seguridad y restauración de las cargas de trabajo de SQL y SAP HANA en una máquina virtual de Azure y en las copias de seguridad del agente de MARS. También puede usar el almacén para realizar copias de seguridad de otras cargas de trabajo (si bien no requerirán los puntos de conexión privados). Además de la copia de seguridad de las cargas de trabajo de SQL y SAP HANA y de las copias de seguridad mediante el agente de MARS, los puntos de conexión privados también se usan para realizar la recuperación de archivos para la copia de seguridad de máquinas virtuales de Azure. Para más información, vea la tabla siguiente:

| Escenarios | Recomendaciones |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Copia de seguridad de cargas de trabajo en máquinas virtuales de Azure (SQL, SAP HANA), copia de seguridad mediante el agente de MARS | Se recomienda el uso de puntos de conexión privados para permitir copias de seguridad y restauración sin necesidad de agregar las listas de direcciones IP o FQDN a una lista de permitidos para Azure Backup o Azure Storage desde las redes virtuales. En ese escenario, asegúrese de que las máquinas virtuales que hospedan las bases de datos SQL pueden comunicarse con direcciones IP o FQDN de Azure AD. |
| **Copia de seguridad de la máquina virtual de Azure**                                         | La copia de seguridad de la máquina virtual no requiere que permita el acceso a direcciones IP o FQDN. Por lo tanto, no requiere puntos de conexión privados para la copia de seguridad y restauración de discos.  <br><br>   Sin embargo, la recuperación de archivos desde un almacén que contiene puntos de conexión privados se restringe a redes virtuales que contienen un punto de conexión privado para el almacén. <br><br>    Al usar discos no administrados de la ACL, asegúrese de que la cuenta de almacenamiento que contiene los discos permite el acceso a **servicios de Microsoft de confianza** si forma parte de la ACL. |
| **Copia de seguridad de Azure Files**                                      | Las copias de seguridad de Azure Files se almacenan en la cuenta de almacenamiento local. Por lo tanto, no requiere puntos de conexión privados para la copia de seguridad y restauración. |

>[!Note]
>Los puntos de conexión privados no se admiten con los servidores DPM y MABS. 

## <a name="difference-in-network-connections-due-to-private-endpoints"></a>Diferencia en las conexiones de red debido a puntos de conexión privados

Como se mencionó anteriormente, los puntos de conexión privados son especialmente útiles para la copia de seguridad de cargas de trabajo (SQL, SAP HANA) en máquinas virtuales de Azure y copias de seguridad del agente de MARS. En todos los escenarios (con o sin puntos de conexión privados), tanto las extensiones de carga de trabajo (para la copia de seguridad de instancias de SQL y SAP HANA que se ejecutan dentro de máquinas virtuales de Azure) como el agente de MARS hacen llamadas de conexión a AAD (a FQDN mencionados en las secciones 56 y 59 de [Microsoft 365 Common y Office Online](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)).

Además de estas conexiones cuando se instala la extensión de carga de trabajo o el agente de MARS para el almacén de Recovery Services _sin puntos de conexión privados_, también se requiere conectividad a los siguientes dominios:

| Servicio | Nombres de dominio |
| ------- | ------------ |
| Azure Backup  | *.backup.windowsazure.com |
| Azure Storage | *.blob.core.windows.net <br> *.queue.core.windows.net <br> *.blob.storage.azure.net |

Cuando se instala la extensión de carga de trabajo o el agente de MARS para el almacén de Recovery Services con un punto de conexión privado, se alcanzarán los siguientes puntos de conexión:

| Servicio | Nombres de dominio |
| ------- | ------------ |
| Azure Backup  | `*.privatelink.<geo>.backup.windowsazure.com` |
| Azure Storage | *.blob.core.windows.net <br> *.queue.core.windows.net <br> *.blob.storage.azure.net |

>[!Note]
>En el texto anterior, `<geo>` hace referencia al código de región (por ejemplo, **eus** para Este de EE. UU. y **ne** para Norte de Europa). Consulte las siguientes listas para ver los códigos de región:
>- [Todas las nubes públicas](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
>- [China](/azure/china/resources-developer-guide#check-endpoints-in-azure)
>- [Alemania](../germany/germany-developer-guide.md#endpoint-mapping)
>- [US Gov](../azure-government/documentation-government-developer-guide.md)

Los FQDN de almacenamiento alcanzados en ambos escenarios son los mismos. Sin embargo, para un almacén de Recovery Services con la configuración de punto de conexión privado, la resolución de nombres debe devolver una dirección IP privada. Esto se puede lograr mediante el uso de zonas DNS privadas, mediante la creación de entradas DNS para la cuenta de almacenamiento en archivos host o mediante el uso de reenviadores condicionales a DNS personalizado con las entradas DNS correspondientes. Las asignaciones de IP privadas para la cuenta de almacenamiento se muestran en la hoja del punto de conexión privado de la cuenta de almacenamiento en el portal.

>Los puntos de conexión privados para blobs y colas siguen un patrón de nomenclatura estándar, comienzan con  **\<the name of the private endpoint>_ecs** o **\<the name of the private endpoint>_prot** y tienen el sufijo  **\_blob** y  **\_queue** respectivamente.

Los puntos de conexión del servicio Azure Backup se modifican para los almacenes habilitados para el punto de conexión privado.  
Si ha configurado un servidor proxy DNS mediante servidores proxy y firewalls de terceros, los nombres de dominio anteriores se deben permitir y redirigir a un DNS personalizado (con asignaciones de direcciones IP privadas) o a 169.63.129.16 con un vínculo de red virtual a una zona DNS privada con estas asignaciones de direcciones IP privadas.

En el ejemplo siguiente se muestra el firewall de Azure usado como proxy DNS para redirigir las consultas de nombre de dominio para el almacén de Recovery Services, el blob, las colas y AAD a 169.63.129.16.

:::image type="content" source="./media/private-endpoints-overview/azure-firewall-used-as-dns-proxy-inline.png" alt-text="Diagrama que muestra el uso de Azure Firewall como proxy DNS para redirigir las consultas de nombre de dominio." lightbox="./media/private-endpoints-overview/azure-firewall-used-as-dns-proxy-expanded.png":::

Para más información, consulte [Creación de un punto de conexión privado](private-endpoints.md).

## <a name="network-connectivity-setup-for-vault-with-private-endpoints"></a>Configuración de conectividad de red para el almacén con puntos de conexión privados

El punto de conexión privado de Recovery Services está asociado a una interfaz de red (NIC) que tiene una dirección IP privada. Para que las conexiones de punto de conexión privado funcionen (enrutar todo el tráfico al servicio a través de la red troncal de Azure y restringir el acceso al servicio a los clientes dentro de la red virtual), es necesario que todo el tráfico de comunicación del servicio se redirija a esa interfaz de red. Esto se puede lograr mediante el uso de DNS vinculado a las entradas de la red virtual o del archivo host en el equipo donde se ejecuta la extensión o el agente.

La extensión de copia de seguridad de carga de trabajo y el agente de MARS se ejecutan en una máquina virtual de Azure en una red virtual o en una máquina virtual local emparejada con la red virtual. Cuando se registra en un almacén de Recovery Services con un punto de conexión privado unido a esta red virtual, la dirección URL del servicio de los servicios en la nube de Azure Backup para la extensión y el agente cambian de `<azure_backup_svc >.<geo>.backup.windowsazure.com` a `<vault_id>.<azure_backup_svc>.privatelink.<geo>.backup`.windowsazure.com**.

>[!Note]
>En el texto anterior, `<geo>` hace referencia al código de región (por ejemplo, **eus** para Este de EE. UU. y **ne** para Norte de Europa). Consulte las siguientes listas para ver los códigos de región:
>- [Todas las nubes públicas](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
>- [China](/azure/china/resources-developer-guide#check-endpoints-in-azure)
>- [Alemania](../germany/germany-developer-guide.md#endpoint-mapping)
>- [US Gov](../azure-government/documentation-government-developer-guide.md)

Las direcciones URL modificadas son específicas de un almacén.  Consulte `<vault_id>` en el nombre de la dirección URL. Solo las extensiones y los agentes registrados en este almacén pueden comunicarse con Azure Backup a través de estos puntos de conexión. Esto restringe el acceso a los clientes dentro de esta red virtual. La extensión o el agente se comunicará a través de `*.privatelink.<geo>.backup.windowsazure.com`, que necesita resolver la dirección IP privada correspondiente en la NIC.

Cuando el punto de conexión privado para los almacenes de Recovery Services se crea a través de Azure Portal con la opción **Integrar con zona DNS privada**, las entradas DNS necesarias para las direcciones IP privadas de los servicios de Azure Backup (`*.privatelink.<geo>backup.windowsazure.com`) se crean automáticamente cada vez que se asigna el recurso. De lo contrario, debe crear manualmente las entradas DNS para estos FQDN en el DNS personalizado o en los archivos host.

Para la administración manual de registros DNS después de la detección de máquinas virtuales para el canal de comunicación: blob/cola, consulte [Registros DNS para blobs y colas (solo para servidores DNS personalizados o archivos host) después del primer registro](./private-endpoints.md#dns-records-for-blobs-and-queues-only-for-custom-dns-servershost-files-after-the-first-registration). Para la administración manual de registros DNS después de la primera copia de seguridad para el blob de la cuenta de almacenamiento de copia de seguridad, consulte [Registros DNS para blobs (solo para servidores DNS personalizados o archivos host) después de la primera copia de seguridad](./private-endpoints.md#dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup).

>Las direcciones IP privadas de los FQDN se pueden encontrar en la hoja del punto de conexión privado del punto de conexión privado creado para el almacén de Recovery Services.

En el diagrama siguiente se muestra cómo funciona la resolución cuando se usa una zona DNS privada para resolver estos FQDN de servicio modificados. 

:::image type="content" source="./media/private-endpoints-overview/use-private-dns-zone-to-resolve-modified-service-fqdns-inline.png" alt-text="Diagrama que muestra cómo funciona la resolución mediante una zona DNS privada para resolver los FQDN de servicio modificados." lightbox="./media/private-endpoints-overview/use-private-dns-zone-to-resolve-modified-service-fqdns-expanded.png":::

Además de la conexión a los servicios en la nube de Azure Backup, la extensión de carga de trabajo y el agente requieren la conexión a las cuentas de almacenamiento de Azure y Azure Active Directory. La extensión de carga de trabajo que se ejecuta en la máquina virtual de Azure requiere la conexión a un mínimo de dos cuentas de almacenamiento: la primera se usa como canal de comunicación (a través de mensajes de cola) y la segunda para almacenar datos de copia de seguridad. El agente de MARS requiere acceso a una cuenta de almacenamiento que se usa para almacenar los datos de copia de seguridad.

Para un almacén habilitado para un punto de conexión privado, Azure Backup crea un punto de conexión privado para estas cuentas de almacenamiento que enruta el tráfico para el canal de comunicación y los datos de copia de seguridad a través de la red troncal de Azure. Esto evita que el tráfico de red relacionado con Azure Backup salga de la red virtual.

Como requisito previo, el almacén de Recovery Services requiere permisos para crear puntos de conexión privados adicionales en el mismo grupo de recursos. También se recomienda proporcionar al almacén de Recovery Services los permisos para crear entradas DNS en las zonas DNS privadas (privatelink.blob.core.windows.net, privatelink.queue.core.windows.net). El almacén de Recovery Services busca zonas DNS privadas en los grupos de recursos donde se crean la red virtual y el punto de conexión privado. Si tiene los permisos para agregar entradas DNS en estas zonas, el almacén las creará; de lo contrario, el usuario debe crearlas manualmente en su DNS personalizado o en una zona DNS privada vinculada a la red virtual.

>Las asignaciones de IP privadas están disponibles en la hoja del punto de conexión privado para los blobs y colas en el portal.

En el diagrama siguiente se muestra cómo funciona la resolución de nombres para las cuentas de almacenamiento que usan una zona DNS privada.

:::image type="content" source="./media/private-endpoints-overview/name-resolution-works-for-storage-accounts-using-private-dns-zone-inline.png" alt-text="Diagrama que muestra cómo funciona la resolución de nombres para las cuentas de almacenamiento que usan una zona DNS privada." lightbox="./media/private-endpoints-overview/name-resolution-works-for-storage-accounts-using-private-dns-zone-expanded.png":::

## <a name="next-steps"></a>Pasos siguientes

- [Creación y uso de puntos de conexión privados](private-endpoints.md)