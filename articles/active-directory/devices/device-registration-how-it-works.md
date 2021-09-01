---
title: Funcionamiento del registro de dispositivos en Azure AD
description: Flujos de registro de dispositivos en Azure AD para dominios administrados y federados
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bc24ecd387c011b5a8a36f380916ab815c1f0ab
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254288"
---
# <a name="how-it-works-device-registration"></a>Funcionamiento: Registro de dispositivos

El registro de dispositivos es un requisito previo para la autenticación basada en la nube. Normalmente, se realiza una unión de los dispositivos a Azure AD o a Azure AD híbrido para completar su registro. En este artículo se proporcionan detalles sobre cómo funciona la unión a Azure AD y a Azure AD híbrido en entornos administrados y federados. Para más información sobre cómo funciona la autenticación de Azure AD en estos dispositivos, consulte el artículo [Tokens de actualización principales](concept-primary-refresh-token.md#detailed-flows).

## <a name="azure-ad-joined-in-managed-environments"></a>Unión a Azure AD en entornos administrados

:::image type="content" source="media/device-registration-how-it-works/device-registration-azure-ad-managed.png" alt-text="Flujo de dispositivo unido a Azure AD en un entorno administrado" lightbox="media/device-registration-how-it-works/device-registration-azure-ad-managed.png":::

| Fase | Descripción |
| :----: | :----------- |
| Un | La manera más común de registrar dispositivos unidos a Azure AD es durante la configuración rápida (OOBE), donde se carga la aplicación web de unión a Azure AD en la aplicación Cloud Experience Host (CXH). La aplicación envía una solicitud GET al punto de conexión de configuración de OpenID de Azure AD para detectar puntos de conexión de autorización. Azure AD devuelve la configuración de OpenID, que incluye los puntos de conexión de autorización, a la aplicación como documento JSON. |
| B | La aplicación compila una solicitud de inicio de sesión para el punto de conexión de autorización y recopila las credenciales de usuario. |
| C | Una vez que el usuario proporciona su nombre de usuario (en formato UPN), la aplicación envía una solicitud GET a Azure AD para detectar la información de dominio correspondiente del usuario. Esta información determina si el entorno es administrado o federado. Azure AD devuelve la información en un objeto JSON. La aplicación determina que el entorno es administrado (no federado).<br><br>El último paso de esta fase hace que la aplicación cree un búfer de autenticación y, si está en la configuración rápida, lo almacene temporalmente en caché para el inicio de sesión automático al final de esta configuración. La aplicación envía con POST las credenciales a Azure AD donde se validan. Azure AD devuelve un token de identificador con notificaciones. |
| D | La aplicación busca las condiciones de uso de MDM (la notificación mdm_tou_url). Si existen, la aplicación las recupera del valor de la notificación, presenta el contenido al usuario y espera a que el usuario las acepte. Este paso es opcional y se omite si la notificación no está presente o si su valor está vacío. |
| E | La aplicación envía una solicitud de detección de registro de dispositivos a Azure Device Registration Service (ADRS). Azure DRS devuelve un documento de datos de detección, que devuelve los URI específicos del inquilino para completar el registro del dispositivo. |
| F | La aplicación crea un par de claves RSA de 2048 bits enlazado a TPM (preferido) conocido como clave de dispositivo (dkpub/dkpriv). La aplicación crea una solicitud de certificado mediante dkpub y la clave pública y firma la solicitud de certificado con el uso de dkpriv. A continuación, la aplicación deriva el segundo par de claves de la clave raíz de almacenamiento de TPM. Esta clave es la clave de transporte (tkpub/tkpriv). |
| G | La aplicación envía una solicitud de registro de dispositivo a Azure DRS que incluye el token de identificador, la solicitud de certificado, tkpub y los datos de atestación. Azure DRS valida el token de identificador, crea un identificador de dispositivo y crea un certificado basado en la solicitud de certificado incluida. A continuación, Azure DRS escribe un objeto de dispositivo en Azure AD y envía el identificador de dispositivo y el certificado de dispositivo al cliente. |
| H | El registro del dispositivo se completa al recibir el identificador de dispositivo y el certificado de dispositivo de Azure DRS. El identificador de dispositivo se guarda para futuras referencias (se puede ver desde `dsregcmd.exe /status`) y el certificado de dispositivo se instala en el almacén Personal del equipo. Una vez completado el registro del dispositivo, el proceso continúa con la inscripción de MDM. |

## <a name="azure-ad-joined-in-federated-environments"></a>Unión a Azure AD en entornos federados

:::image type="content" source="media/device-registration-how-it-works/device-registration-azure-ad-federated.png" alt-text="Flujo de dispositivo unido a Azure AD en un entorno federado" lightbox="media/device-registration-how-it-works/device-registration-azure-ad-federated.png":::

| Fase | Descripción |
| :----: | :----------- |
| Un | La manera más común de registrar dispositivos unidos a Azure AD es durante la configuración rápida (OOBE), donde se carga la aplicación web de unión a Azure AD en la aplicación Cloud Experience Host (CXH). La aplicación envía una solicitud GET al punto de conexión de configuración de OpenID de Azure AD para detectar puntos de conexión de autorización. Azure AD devuelve la configuración de OpenID, que incluye los puntos de conexión de autorización, a la aplicación como documento JSON. |
| B | La aplicación compila una solicitud de inicio de sesión para el punto de conexión de autorización y recopila las credenciales de usuario. |
| C | Una vez que el usuario proporciona su nombre de usuario (en formato UPN), la aplicación envía una solicitud GET a Azure AD para detectar la información de dominio correspondiente del usuario. Esta información determina si el entorno es administrado o federado. Azure AD devuelve la información en un objeto JSON. La aplicación determina que el entorno es federado.<br><br>La aplicación redirige al valor AuthURL (página de inicio de sesión del servicio de token de seguridad local) en el objeto de dominio JSON devuelto. La aplicación recopila credenciales por medio de la página web del servicio de token de seguridad. |
| D | La aplicación envía con POST la credencial al servicio de token de seguridad local, lo que puede requerir factores adicionales de autenticación. El servicio de token de seguridad local autentica al usuario y devuelve un token. La aplicación envía con POST el token a Azure AD para la autenticación. Azure AD valida el token y devuelve un token de identificador con notificaciones. |
| E | La aplicación busca las condiciones de uso de MDM (la notificación mdm_tou_url). Si existen, la aplicación las recupera del valor de la notificación, presenta el contenido al usuario y espera a que el usuario las acepte. Este paso es opcional y se omite si la notificación no está presente o si su valor está vacío. |
| F | La aplicación envía una solicitud de detección de registro de dispositivos a Azure Device Registration Service (ADRS). Azure DRS devuelve un documento de datos de detección, que devuelve los URI específicos del inquilino para completar el registro del dispositivo. |
| G | La aplicación crea un par de claves RSA de 2048 bits enlazado a TPM (preferido) conocido como clave de dispositivo (dkpub/dkpriv). La aplicación crea una solicitud de certificado mediante dkpub y la clave pública y firma la solicitud de certificado con el uso de dkpriv. A continuación, la aplicación deriva el segundo par de claves de la clave raíz de almacenamiento de TPM. Esta clave es la clave de transporte (tkpub/tkpriv). |
| H | La aplicación envía una solicitud de registro de dispositivo a Azure DRS que incluye el token de identificador, la solicitud de certificado, tkpub y los datos de atestación. Azure DRS valida el token de identificador, crea un identificador de dispositivo y crea un certificado basado en la solicitud de certificado incluida. A continuación, Azure DRS escribe un objeto de dispositivo en Azure AD y envía el identificador de dispositivo y el certificado de dispositivo al cliente. |
| I | El registro del dispositivo se completa al recibir el identificador de dispositivo y el certificado de dispositivo de Azure DRS. El identificador de dispositivo se guarda para futuras referencias (se puede ver desde `dsregcmd.exe /status`) y el certificado de dispositivo se instala en el almacén Personal del equipo. Una vez completado el registro del dispositivo, el proceso continúa con la inscripción de MDM. |

## <a name="hybrid-azure-ad-joined-in-managed-environments"></a>Unión a Azure AD híbrido en entornos administrados

:::image type="content" source="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-managed.png" alt-text="Flujo de dispositivo unido a Azure AD híbrido en un entorno administrado" lightbox="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-managed.png":::

| Fase | Descripción |
| :----: | ----------- |
| Un | El usuario inicia sesión en un equipo con Windows 10 unido a un dominio con credenciales de dominio. Esta credencial puede ser el nombre de usuario y la contraseña o la autenticación con tarjeta inteligente. El inicio de sesión del usuario desencadena la tarea de combinación automática de dispositivos. La tarea de combinación automática de dispositivos se desencadena en la unión a un dominio y se reintenta cada hora. No depende únicamente del inicio de sesión del usuario. |
| B | La tarea consulta Active Directory mediante el protocolo LDAP para localizar el atributo keywords en el punto de conexión de servicio almacenado en la partición de configuración de Active Directory (`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=corp,DC=contoso,DC=com`). El valor devuelto en el atributo keywords determina si el registro de dispositivos se dirige a Azure Device Registration Service (ADRS) o al servicio de registro de dispositivos de empresa hospedado en el entorno local. |
| C | Para el entorno administrado, la tarea crea una credencial de autenticación inicial en forma de certificado autofirmado. La tarea escribe el certificado en el atributo userCertificate del objeto de equipo en Active Directory mediante LDAP. |
| D | El equipo no puede autenticarse en Azure DRS hasta que se cree un objeto de dispositivo que represente el equipo que incluye el certificado en el atributo userCertificate en Azure AD. Azure AD Connect detecta un cambio de atributo. En el siguiente ciclo de sincronización, Azure AD Connect envía el atributo userCertificate, el GUID del objeto y el SID del equipo a Azure DRS. Azure DRS usa la información del atributo para crear un objeto de dispositivo en Azure AD. |
| E | La tarea de combinación automática de dispositivos se desencadena con cada inicio de sesión de usuario o cada hora, e intenta autenticar el equipo en Azure AD mediante la clave privada correspondiente de la clave pública en el atributo userCertificate. Azure AD autentica el equipo y emite un token de identificador al equipo. |
| F | La tarea crea un par de claves RSA de 2048 bits enlazado a TPM (preferido) conocido como clave de dispositivo (dkpub/dkpriv). La aplicación crea una solicitud de certificado mediante dkpub y la clave pública y firma la solicitud de certificado con el uso de dkpriv. A continuación, la aplicación deriva el segundo par de claves de la clave raíz de almacenamiento de TPM. Esta clave es la clave de transporte (tkpub/tkpriv). |
| G | La tarea envía una solicitud de registro de dispositivo a Azure DRS que incluye el token de identificador, la solicitud de certificado, tkpub y los datos de atestación. Azure DRS valida el token de identificador, crea un identificador de dispositivo y crea un certificado basado en la solicitud de certificado incluida. A continuación, Azure DRS actualiza el objeto de dispositivo en Azure AD y envía el identificador de dispositivo y el certificado de dispositivo al cliente. |
| H | El registro del dispositivo se completa al recibir el identificador de dispositivo y el certificado de dispositivo de Azure DRS. El identificador de dispositivo se guarda para futuras referencias (se puede ver desde `dsregcmd.exe /status`) y el certificado de dispositivo se instala en el almacén Personal del equipo. Una vez completado el registro del dispositivo, se cierra la tarea. |

## <a name="hybrid-azure-ad-joined-in-federated-environments"></a>Unión a Azure AD híbrido en entornos federados

:::image type="content" source="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-federated.png" alt-text="Flujo de dispositivo unido a Azure AD híbrido en un entorno administrado" lightbox="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-federated.png":::

| Fase | Descripción |
| :----: | :----------- |
| Un | El usuario inicia sesión en un equipo con Windows 10 unido a un dominio con credenciales de dominio. Esta credencial puede ser el nombre de usuario y la contraseña o la autenticación con tarjeta inteligente. El inicio de sesión del usuario desencadena la tarea de combinación automática de dispositivos. La tarea de combinación automática de dispositivos se desencadena en la unión a un dominio y se reintenta cada hora. No depende únicamente del inicio de sesión del usuario. |
| B | La tarea consulta Active Directory mediante el protocolo LDAP para localizar el atributo keywords en el punto de conexión de servicio almacenado en la partición de configuración de Active Directory (`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=corp,DC=contoso,DC=com`). El valor devuelto en el atributo keywords determina si el registro de dispositivos se dirige a Azure Device Registration Service (ADRS) o al servicio de registro de dispositivos de empresa hospedado en el entorno local. |
| C | Para los entornos federados, el equipo autentica el punto de conexión de registro de dispositivos de empresa mediante la autenticación integrada de Windows. El servicio de registro de dispositivos de empresa crea y devuelve un token que incluye notificaciones para el GUID del objeto, el SID del equipo y el estado de unión al dominio. La tarea envía el token y las notificaciones a Azure AD, donde se validan. Azure AD devuelve un token de identificador a la tarea que se está ejecutando. |
| D | La aplicación crea un par de claves RSA de 2048 bits enlazado a TPM (preferido) conocido como clave de dispositivo (dkpub/dkpriv). La aplicación crea una solicitud de certificado mediante dkpub y la clave pública y firma la solicitud de certificado con el uso de dkpriv. A continuación, la aplicación deriva el segundo par de claves de la clave raíz de almacenamiento de TPM. Esta clave es la clave de transporte (tkpub/tkpriv). |
| E | Para proporcionar el inicio de sesión único para una aplicación federada local, la tarea solicita un PRT empresarial desde el servicio de token de seguridad local. Windows Server 2016 ejecuta el rol Servicios de federación de Active Directory (AD FS) para validar la solicitud y devolverla a la tarea en ejecución. |
| F | La tarea envía una solicitud de registro de dispositivo a Azure DRS que incluye el token de identificador, la solicitud de certificado, tkpub y los datos de atestación. Azure DRS valida el token de identificador, crea un identificador de dispositivo y crea un certificado basado en la solicitud de certificado incluida. A continuación, Azure DRS escribe un objeto de dispositivo en Azure AD y envía el identificador de dispositivo y el certificado de dispositivo al cliente. El registro del dispositivo se completa al recibir el identificador de dispositivo y el certificado de dispositivo de Azure DRS. El identificador de dispositivo se guarda para futuras referencias (se puede ver desde `dsregcmd.exe /status`) y el certificado de dispositivo se instala en el almacén Personal del equipo. Una vez completado el registro del dispositivo, se cierra la tarea. |
| G | Si se habilita la escritura diferida de dispositivo en Azure AD Connect, esta herramienta solicita actualizaciones de Azure AD en su siguiente ciclo de sincronización (la escritura diferida de dispositivo se requiere para la implementación híbrida mediante la confianza de certificados). Azure AD correlaciona el objeto de dispositivo con un objeto de equipo sincronizado correspondiente. Azure AD Connect recibe el objeto de dispositivo que incluye el GUID del objeto y el SID del equipo, y escribe el objeto de dispositivo en Active Directory. |

## <a name="next-steps"></a>Pasos siguientes

- [Dispositivos unidos a Azure AD](concept-azure-ad-join.md)
- [Dispositivos registrados en Azure AD](concept-azure-ad-register.md)
- [Dispositivos híbridos unidos a Azure AD](concept-azure-ad-join-hybrid.md)
- [¿Qué es un token de actualización principal?](concept-primary-refresh-token.md)
- [Azure AD Connect: Opciones de dispositivos](../hybrid/how-to-connect-device-options.md)
