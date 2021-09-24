---
title: Configuración del origen de identidad externo de vCenter
description: Obtenga información sobre cómo configurar Active Directory a través de LDAP o LDAPS para vCenter como origen de identidad externa.
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: 77644c2d52a5eed87ab4dca83632b69834dd4c58
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123537119"
---
# <a name="configure-external-identity-source-for-vcenter"></a>Configuración del origen de identidad externo de vCenter



[!INCLUDE [vcenter-access-identity-description](includes/vcenter-access-identity-description.md)]

>[!NOTE]
>Los comandos de ejecución se ejecutan de uno en uno en el orden enviado.

En este procedimiento, aprenderá a:

> [!div class="checklist"]
> * Enumerar todos los orígenes de identidad externa integrados con el inicio de sesión único de vCenter.
> * Agregar Active Directory a través de LDAP, con o sin SSL. 
> * Agregar un grupo de AD existente a otro de cloudadmin.
> * Eliminar un grupo de AD del rol cloudadmin.
> * Eliminar orígenes de identidad externa ya existentes.



## <a name="prerequisites"></a>Requisitos previos

- Establezca la conectividad entre su red local y su nube privada.

- Si tiene AD con SSL, descargue el certificado para la autenticación de AD y cárguelo en una cuenta de Azure Storage como almacenamiento de blobs. A continuación, tendrá que [conceder acceso a recursos de Azure Storage usando una firma de acceso compartido (SAS)](../storage/common/storage-sas-overview.md).  

- Si usa FQDN, habilite la resolución DNS en el AD de su entorno local.

 

## <a name="list-external-identity"></a>Enumeración de identidades externas



Ejecute el cmdlet `Get-ExternalIdentitySources` para enumerar todos los orígenes de identidad externa ya integrados con el inicio de sesión único de vCenter.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Seleccione **Comando de ejecución** > **Paquetes** > **Get-ExternalIdentitySources**.

   :::image type="content" source="media/run-command/run-command-overview.png" alt-text="Captura de pantalla en la que se muestra cómo acceder a los comandos de ejecución disponibles" lightbox="media/run-command/run-command-overview.png":::

1. Proporcione los valores necesarios o cambie los predeterminados y, luego, seleccione **Ejecutar**.

   :::image type="content" source="media/run-command/run-command-get-external-identity-sources.png" alt-text="Captura de pantalla en la que se muestra cómo enumerar los orígenes de identidad externa":::
   
   | **Campo** | **Valor** |
   | --- | --- |
   | **Conservar hasta**  |Período de retención de la salida del cmdlet. El valor predeterminado es 60 días.   |
   | **Especificar el nombre de la ejecución**  | Nombre alfanumérico, por ejemplo, **getExternalIdentity**.  |
   | **Tiempo de espera**  |  Período después del cual se cierra un cmdlet si tarda demasiado tiempo en finalizar.  |

1. Active **Notificaciones** o el panel **Estado de la ejecución del proceso** para ver el progreso.


## <a name="add-active-directory-over-ldap-with-ssl"></a>Incorporación de Active Directory a través de LDAP con SSL

Ejecute el cmdlet `New-AvsLDAPSIdentitySource` para agregar un AD a través de LDAP con SSL como origen de identidad externa para con el fin de usarlo con SSO en vCenter. 

1. Descargue el certificado para la autenticación de AD y cárguelo en una cuenta de Azure Storage como almacenamiento de blobs. Si se requieren varios certificados, cargue cada certificado individualmente.  

1. Para cada certificado, [conceda acceso a recursos de Azure Storage mediante una firma de acceso compartido (SAS)](../storage/common/storage-sas-overview.md). Estas cadenas SAS se proporcionan al cmdlet como parámetro. 

   >[!IMPORTANT]
   >Asegúrese de que copia cada cadena de SAS, ya que estas dejarán de estar disponibles una vez que salga de esta página.  
   
1. Seleccione **Comando de ejecución** > **Paquetes** > **New-AvsLDAPSIdentitySource**.

1. Proporcione los valores necesarios o cambie los predeterminados y, luego, seleccione **Ejecutar**.

   | **Campo** | **Valor** |
   | --- | --- |
   | **Nombre**  | Nombre descriptivo del origen de identidad externa, por ejemplo, **avslap.local**.  |
   | **DomainName**  | FQDN del dominio.   |
   | **DomainAlias**  | En el caso de orígenes de identidad de Active Directory, nombre NetBIOS del dominio. Si usa autenticaciones de SSPI, agregue el nombre de NetBIOS del dominio de AD como alias del origen de identidad.     |
   | **PrimaryUrl**  | Dirección URL principal del origen de identidad externa, por ejemplo, **ldap://suservidor:389**.  |
   | **SecondaryURL**  | Dirección URL secundaria de reserva si se produce un error en la principal.  |
   | **BaseDNUsers**  |  Dónde se deben buscar usuarios válidos, por ejemplo, **CN=users,DC=yourserver,DC=internal**.  El DN base es necesario para usar la autenticación LDAP.  |
   | **BaseDNGroups**  | Dónde se deben buscar grupos, por ejemplo, **CN=group1, DC=yourserver,DC= internal**. El DN base es necesario para usar la autenticación LDAP.  |
   | **Credential:**  | Nombre de usuario y contraseña usados para la autenticación con el origen de AD (no cloudadmin).  |
   | **CertificateSAS** | Ruta de acceso a cadenas SAS con los certificados para la autenticación en el origen de AD. Si usa varios certificados, separe cada cadena SAS con una coma. Por ejemplo, **pathtocert1,pathtocert2**.  |
   | **NombreDeGrupo**  | Grupo del origen de identidad externo que proporciona acceso a cloudadmin. Por ejemplo, **avs-admins**.  |
   | **Conservar hasta**  | Período de retención de la salida del cmdlet. El valor predeterminado es 60 días.   |
   | **Especificar el nombre de la ejecución**  | Nombre alfanumérico, por ejemplo, **addexternalIdentity**.  |
   | **Tiempo de espera**  |  Período después del cual se cierra un cmdlet si tarda demasiado tiempo en finalizar.  |

1. Active **Notificaciones** o el panel **Estado de la ejecución del proceso** para ver el progreso.



## <a name="add-active-directory-over-ldap"></a>Incorporación de Active Directory a través de LDAP

>[!NOTE]
>Este método no es recomendable. En su lugar, use el método [Incorporación de Active Directory a través de LDAP con SSL](#add-active-directory-over-ldap-with-ssl).

Ejecute el cmdlet `New-AvsLDAPIdentitySource` para agregar un AD a través de LDAP como origen de identidad externa para con el fin de usarlo con SSO en vCenter. 

1. Seleccione **Comando de ejecución** > **Paquetes** > **New-AvsLDAPIdentitySource**.

1. Proporcione los valores necesarios o cambie los predeterminados y, luego, seleccione **Ejecutar**.
   
   | **Campo** | **Valor** |
   | --- | --- |
   | **Nombre**  | Nombre descriptivo del origen de identidad externa, por ejemplo, **avslap.local**.  |
   | **DomainName**  | FQDN del dominio.    |
   | **DomainAlias**  | En el caso de orígenes de identidad de Active Directory, nombre NetBIOS del dominio. Si usa autenticaciones de SSPI, agregue el nombre de NetBIOS del dominio de AD como alias del origen de identidad.      |
   | **PrimaryUrl**  | Dirección URL principal del origen de identidad externa, por ejemplo, **ldap://suservidor:389**.  |
   | **SecondaryURL**  | Dirección URL secundaria de reserva si se produce un error en la principal.  |
   | **BaseDNUsers**  |  Dónde se deben buscar usuarios válidos, por ejemplo, **CN=users,DC=yourserver,DC=internal**.  El DN base es necesario para usar la autenticación LDAP.  |
   | **BaseDNGroups**  | Dónde se deben buscar grupos, por ejemplo, **CN=group1, DC=yourserver,DC= internal**. El DN base es necesario para usar la autenticación LDAP.  |
   | **Credential:**  | Nombre de usuario y contraseña usados para la autenticación con el origen de AD (no cloudadmin).  |
   | **NombreDeGrupo**  | Grupo para proporcionar acceso de administrador de nube en el origen de identidad externa, por ejemplo, **avs-admins**.  |
   | **Conservar hasta**  | Período de retención de la salida del cmdlet. El valor predeterminado es 60 días.   |
   | **Especificar el nombre de la ejecución**  | Nombre alfanumérico, por ejemplo, **addexternalIdentity**.  |
   | **Tiempo de espera**  |  Período después del cual se cierra un cmdlet si tarda demasiado tiempo en finalizar.  |

1. Active **Notificaciones** o el panel **Estado de la ejecución del proceso** para ver el progreso.


## <a name="add-existing-ad-group-to-cloudadmin-group"></a>Agregar un grupo de AD existente a otro de cloudadmin.

Ejecute el cmdlet `Add-GroupToCloudAdmins` para agregar un grupo de AD ya existente al grupo cloudadmin. Los usuarios de este grupo tienen privilegios equivalente al rol cloudadmin (cloudadmin@vsphere.local) que se define en el inicio de sesión único de vCenter.

1. Seleccione **Comando de ejecución** > **Paquetes** > **Add-GroupToCloudAdmins**.

1. Proporcione los valores necesarios o cambie los predeterminados y, luego, seleccione **Ejecutar**.

   | **Campo** | **Valor** |
   | --- | --- |
   | **NombreDeGrupo**  | Nombre del grupo que se va a agregar, por ejemplo, **VcAdminGroup**.  |
   | **Conservar hasta**  | Período de retención de la salida del cmdlet. El valor predeterminado es 60 días.   |
   | **Especificar el nombre de la ejecución**  | Nombre alfanumérico, por ejemplo, **addADgroup**.  |
   | **Tiempo de espera**  |  Período después del cual se cierra un cmdlet si tarda demasiado tiempo en finalizar.  |

1. Active **Notificaciones** o el panel **Estado de la ejecución del proceso** para ver el progreso.



## <a name="remove-ad-group-from-the-cloudadmin-role"></a>Eliminación de un grupo de AD del rol cloudadmin

Ejecute el cmdlet `Remove-GroupFromCloudAdmins` para eliminar del rol cloudadmin un grupo de AD especificado. 

1. Seleccione **Comando de ejecución** > **Paquetes** > **Remove-GroupFromCloudAdmins**.

1. Proporcione los valores necesarios o cambie los predeterminados y, luego, seleccione **Ejecutar**.

   | **Campo** | **Valor** |
   | --- | --- |
   | **NombreDeGrupo**  | Nombre del grupo que se va a eliminar, por ejemplo, **VcAdminGroup**.  |
   | **Conservar hasta**  | Período de retención de la salida del cmdlet. El valor predeterminado es 60 días.   |
   | **Especificar el nombre de la ejecución**  | Nombre alfanumérico, por ejemplo, **removeADgroup**.  |
   | **Tiempo de espera**  |  Período después del cual se cierra un cmdlet si tarda demasiado tiempo en finalizar.  |

1. Active **Notificaciones** o el panel **Estado de la ejecución del proceso** para ver el progreso.






## <a name="remove-existing-external-identity-sources"></a>Eliminación de orígenes de identidad externa ya existentes

Ejecute el cmdlet `Remove-ExternalIdentitySources` para eliminar de forma masiva todos los orígenes de identidad externa ya existentes. 

1. Seleccione **Comando de ejecución** > **Paquetes** > **Remove-ExternalIdentitySources**.

1. Proporcione los valores necesarios o cambie los predeterminados y, luego, seleccione **Ejecutar**.

   | **Campo** | **Valor** |
   | --- | --- |
   | **Conservar hasta**  | Período de retención de la salida del cmdlet. El valor predeterminado es 60 días.   |
   | **Especificar el nombre de la ejecución**  | Nombre alfanumérico, por ejemplo, **remove_externalIdentity**.  |
   | **Tiempo de espera**  |  Período después del cual se cierra un cmdlet si tarda demasiado tiempo en finalizar.  |

1. Active **Notificaciones** o el panel **Estado de la ejecución del proceso** para ver el progreso.


## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a configurar LDAP y LDAPS, puede obtener más información sobre lo siguiente:

- [Configuración de una directiva de almacenamiento:](configure-storage-policy.md) a cada máquina virtual implementada en un almacén de datos vSAN se le asigna al menos una directiva de almacenamiento de máquina virtual. Puede asignar una directiva de almacenamiento de máquina virtual en una implementación inicial de una máquina virtual o al realizar otras operaciones de máquina virtual, como clonación o migración.

- [Conceptos de identidad de Azure VMware Solution:](concepts-identity.md) use vCenter para administrar cargas de trabajo de máquinas virtuales (VM) y NSX-T Manager para administrar y ampliar la nube privada. En la administración de identidades y acceso se usa el rol CloudAdmin para vCenter y derechos de administrador restringidos para NSX-T Manager. 

 
