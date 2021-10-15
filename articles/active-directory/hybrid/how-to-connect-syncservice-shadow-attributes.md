---
title: Atributos paralelos del servicio de sincronización de Azure AD Connect | Microsoft Docs
description: Describe cómo funcionan los atributos paralelos en el servicio de sincronización de Azure AD Connect.
services: active-directory
author: billmath
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/29/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b2274099803961fb477f0929c801e2fc341dd4b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355274"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Atributos paralelos del servicio de sincronización de Azure AD Connect
La mayoría de los atributos se representan del mismo modo en Azure AD, ya que se encuentran en el Active Directory local. Pero algunos atributos tienen algún tratamiento especial y el valor del atributo en Azure AD puede ser distinto de lo que sincroniza Azure AD Connect.

## <a name="introducing-shadow-attributes"></a>Introducción a los atributos paralelos
Algunos atributos tienen dos representaciones en Azure AD. Se almacenan el valor local y un valor calculado. Estos atributos adicionales se denominan atributos paralelos. Los dos atributos más comunes donde verá este comportamiento son **userPrincipalName** y **proxyAddress**. El cambio de los valores de atributo se produce cuando hay valores de estos atributos que representan los dominios no comprobados. Pero el motor de sincronización de Connect lee el valor del atributo paralelo, por lo que desde su perspectiva, el atributo se ha confirmado por Azure AD.

No se pueden ver los atributos paralelos mediante Azure Portal o con PowerShell. Pero entender el concepto le ayudará a solucionar los problemas de determinados escenarios donde el atributo tiene valores diferentes tanto en local como en la nube.

Para comprender mejor el comportamiento, vea este ejemplo de Fabrikam:  
![Captura de pantalla que muestra el sufijo UPN de Active Directory para varios ejemplos con un valor de dominio de Azure AD correspondiente de No se ha agregado, No se ha comprobado y Comprobado.](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
Tienen varios sufijos UPN en su instancia de Active Directory local, pero solo se ha comprobado uno.

### <a name="userprincipalname"></a>userPrincipalName
Un usuario tiene los siguientes valores de atributo en un dominio no comprobado:

| Atributo | Value |
| --- | --- |
| userPrincipalName local | lee.sperry@fabrikam.com |
| shadowUserPrincipalName de Azure AD | lee.sperry@fabrikam.com |
| userPrincipalName de Azure AD | lee.sperry@fabrikam.onmicrosoft.com |

El atributo userPrincipalName es el valor que aparece cuando se usa PowerShell.

Puesto que el valor del atributo local real se almacena en Azure AD, al comprobar el dominio fabrikam.com, Azure AD actualiza el atributo userPrincipalName con el valor de shadowUserPrincipalName. No es necesario sincronizar los cambios de Azure AD Connect para que se actualicen estos valores.

### <a name="proxyaddresses"></a>proxyAddresses
El mismo proceso para incluir solo dominios comprobados también se produce para proxyAddresses, pero con alguna lógica adicional. La comprobación de dominios comprobados solo se produce para los usuarios de buzón. Un usuario habilitado para correo o un contacto representa un usuario de otra organización de Exchange y puede agregar los valores de proxyAddresses en estos objetos.

Para un usuario del buzón, de forma local o en Exchange Online, aparecen únicamente los valores para los dominios comprobados. Debería ser parecido a esto:

| Atributo | Value |
| --- | --- |
| proxyAddresses local | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| ProxyAddresses de Exchange Online | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

En este caso **smtp:abbie.spencer\@fabrikam.com** se quitó porque no se ha comprobado el dominio. Pero Exchange también ha agregado **SIP:abbie.spencer\@fabrikamonline.com**. Fabrikam no ha usado Lync o Skype local, pero Azure AD y Exchange Online se preparan para ello.

Esta lógica para proxyAddresses se conoce como **ProxyCalc**. ProxyCalc se llama con cada cambio en un usuario cuando:

- Al usuario se ha asignado un plan de servicio que incluye Exchange Online, incluso si el usuario no se tiene licencia para Exchange. Por ejemplo, si al usuario se le asigna la SKU de Office E3, pero solo se asignó SharePoint Online. Esta condición ocurre incluso si el buzón sigue siendo local.
- El atributo msExchRecipientTypeDetails tiene un valor.
- Realiza un cambio en proxyAddresses o userPrincipalName.

ProxyCalc corregirá una dirección si ShadowProxyAddresses contiene un dominio no comprobado y el usuario en la nube tiene una de las siguientes propiedades configuradas. 
- El usuario tiene una licencia con un plan de tipo de servicio de EXO habilitado (excepto MyAnalytics).  
- El usuario tiene MSExchRemoteRecipientType establecido (no null).  
- El usuario se considera un recurso compartido.

Para que se considere un recurso compartido, el usuario en la nube tendrá uno de los siguientes valores establecidos en CloudMSExchRecipientDisplayType. 

 |Tipo para mostrar de objeto|Valor (decimal)|
 |-----|-----|
 |MailboxUser|  0|
 |DistributionGroup|    1|
 |PublicFolder| 2|
 |DynamicDistributionGroup| 3|
 |Organización| 4|
 |PrivateDistributionList|  5|
 |RemoteMailUser|   6|
 |ConferenceRoomMailbox|    7|
 |EquipmentMailbox| 8|
 |ArbitrationMailbox|   10|
 |MailboxPlan|  11|
 |LinkedUser|   12|
 |RoomList| 15|
 |SyncedMailboxUser|    -2147483642|
 |SyncedUDGasUDG|   -2147483391|
 |SyncedUDGasContact|   -2147483386|
 |SyncedPublicFolder|   -2147483130|
 |SyncedDynamicDistributionGroup|   -2147482874|
 |SyncedRemoteMailUser| -2147482106|
 |SyncedConferenceRoomMailbox|  -2147481850|
 |SyncedEquipmentMailbox|   -2147481594|
 |SyncedUSGasUDG|   -2147481343|
 |SyncedUSGasContact|   -2147481338|
 |ACLableSyncedMailboxUser| -1073741818|
 |ACLableSyncedRemoteMailUser|  -1073740282|
 |ACLableSyncedUSGasContact|    -1073739514|
 |SyncedUSGasUSG|   -1073739511|
 |SecurityDistributionGroup|    1043741833|
 |SyncedUSGasUSG|   1073739511|
 |ACLableSyncedUSGasContact|    1073739514|
 |Grupo de roles RBAC|  1073741824|
 |ACLableMailboxUser|   1073741824|
 |ACLableRemoteMailUser|    1073741830|


>[!NOTE]
> CloudMSExchRecipientDisplayType no es visible desde el lado de Azure AD y solo se puede ver mediante algo como el cmdlet [Get-Recipient](/powershell/module/exchange/get-recipient) de Exchange Online.  
>
>Ejemplo:
> ```PowerShell
>   Get-Recipient admin | fl *type*
> ```
>

ProxyCalc puede tardar algún tiempo en procesar un cambio en un usuario y no está sincronizado con el proceso de exportación de Azure AD Connect.

> [!NOTE]
> La lógica de ProxyCalc tiene algunos comportamientos adicionales para escenarios avanzados que no se documentan en este tema. Este tema se proporciona para que pueda entender el comportamiento y no tener que documentar toda la lógica interna.

### <a name="quarantined-attribute-values"></a>Valores de atributo en cuarentena
Los atributos paralelos también se utilizan cuando hay valores de atributo duplicados. Para más información, consulte [Resistencia de atributos duplicados](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Consulte también
* [Sincronización de Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](whatis-hybrid-identity.md).
