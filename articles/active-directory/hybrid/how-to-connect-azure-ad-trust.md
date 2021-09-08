---
title: 'Azure AD Connect: administración de confianza de AD FS con Azure AD mediante Azure AD Connect | Microsoft Docs'
description: Detalles de funcionamiento del control de la confianza de Azure AD por Azure AD Connect.
services: active-directory
documentationcenter: ''
ms.reviewer: anandyadavmsft
manager: daveba
ms.subservice: hybrid
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/28/2018
ms.author: billmath
author: billmath
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8fea0133b7aa93b16a7e65e9e573723e7e21bae
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112460295"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>Administración de la confianza de AD FS con Azure AD mediante Azure AD Connect

## <a name="overview"></a>Información general

Al federar un entorno local con Azure AD, establece una relación de confianza entre el proveedor de identidades local y Azure AD.  Azure AD Connect puede administrar la federación entre los Servicios de federación de Active Directory (AD FS) locales y Azure AD. En este artículo se proporciona información general de:

* Las distintas configuraciones de la confianza de Azure AD Connect
* Las reglas de transformación de emisión (reglas de notificación) establecidas por Azure AD Connect
* Cómo realizar una copia de seguridad de las reglas de notificación, y restaurarlas, entre actualizaciones y actualizaciones de configuración. 
* Procedimiento recomendado para proteger y supervisar la confianza de AD FS con Azure AD

## <a name="settings-controlled-by-azure-ad-connect"></a>Configuración controlada por Azure AD Connect

Azure AD Connect **solo** administra la configuración relacionada con la confianza de Azure AD. Azure AD Connect no modifica ninguna configuración de otras confianzas para usuarios autenticados de AD FS. En la tabla siguiente se indica la configuración que se controla mediante Azure AD Connect.

| Configuración | Descripción |
| :--- | :--- |
| Certificado de firma de tokens | Azure AD Connect puede usarse para restablecer y volver a crear la confianza con Azure AD. Azure AD Connect realiza una sustitución inmediata puntual de los certificados de firma de tokens para AD FS y actualiza la configuración de federación de dominios de Azure AD.|
| Algoritmo de firma de token | Microsoft recomienda usar SHA-256 como algoritmo de firma de token. Azure AD Connect puede detectar si el algoritmo de firma de tokens está establecido en un valor menos seguro que SHA-256, y actualizará la configuración a SHA-256 en la siguiente operación de configuración posible. El usuario de confianza debe actualizarse para utilizar el nuevo certificado de firma de tokens. |
| Identificador de confianza de Azure AD | Azure AD Connect establece el valor de identificador correcto para la confianza de Azure AD. AD FS identifica de forma única la confianza de Azure AD con el valor de identificador. |
| Puntos de conexión de Azure AD | Azure AD Connect se asegura de que los puntos de conexión configurados para la confianza de Azure AD sigan siempre los valores recomendados más recientes de resiliencia y rendimiento. |
| Reglas de transformación de emisión | Hay varias reglas de notificación que son necesarias para un rendimiento óptimo de las características de Azure AD en un escenario federado. Azure AD Connect garantiza que la confianza de Azure AD siempre esté configurada con el conjunto adecuado de reglas de notificación recomendadas. |
| Identificador alternativo | Si la sincronización está configurada para usar el identificador alternativo, Azure AD Connect configura AD FS para realizar la autenticación mediante dicho identificador. |
| Actualización automática de metadatos | La confianza con Azure AD está configurada para la actualización automática de los metadatos. AD FS comprueba periódicamente los metadatos de la confianza de Azure AD y los mantiene actualizados en caso de que cambien por parte de Azure AD. |
| Autenticación integrada de Windows (IWA) | Durante la operación de combinación de Azure AD híbrido, IWA está habilitada para el registro de dispositivos para facilitar la combinación de Azure AD híbrido en los dispositivos de nivel inferior. |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Flujos de ejecución y configuración de federación realizada por Azure AD Connect

Azure AD Connect no actualiza toda la configuración de la confianza de Azure AD durante los flujos de configuración. La configuración modificada depende de la tarea o flujo de ejecución que se ejecute. En la tabla siguiente se enumera la configuración afectada en distintos flujos de ejecución.

| Flujo de ejecución | Configuración afectada |
| :--- | :--- |
| Primera instalación de paso (rápida) | None |
| Instalación de primer paso (nueva granja de servidores de AD FS) | Se crea una nueva granja de servidores de AD FS y se crea una confianza con Azure AD desde el principio. |
| Instalación de primer paso (granja de servidores de AD FS existente, confianza de Azure AD existente) | Identificador de confianza de Azure AD, reglas de transformación de emisión, puntos de conexión de Azure AD, identificador alternativo (si es necesario), actualización automática de metadatos |
| Restablecer la confianza de Azure AD | Certificado de forma de tokens, algoritmo de forma de tokens, identificador de confianza de Azure AD, reglas de transformación de emisión, puntos de conexión de Azure AD, identificador alternativo (si es necesario), actualización automática de metadatos |
| Agregar el servidor de federación | None |
| Agregar el servidor WAP | None |
| Opciones de dispositivos | Reglas de transformación de emisión, IWA para el registro de dispositivos |
| Agregar el dominio federado | Si el dominio se agrega por primera vez, es decir, se está cambiando la configuración de federación de dominio único a federación de varios dominios, Azure AD Connect volverá a crear la confianza desde el principio. Si la confianza con Azure AD ya está configurada para varios dominios, solo se modifican las reglas de transformación de emisión. |
| Actualizar TLS | None |

Durante todas las operaciones, en las que se modifica cualquier configuración, Azure AD Connect realiza una copia de seguridad de las configuraciones de confianza actuales en **%ProgramData%\AADConnect\ADFS**.

![Página de Azure AD Connect que muestra un mensaje sobre la copia de seguridad de la confianza de Azure AD existente](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> Antes de la versión 1.1.873.0, la copia de seguridad constaba únicamente de reglas de transformación de emisión y estas se copiaban en el archivo de registro de seguimiento del asistente.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Reglas de transformación de emisión establecidas por Azure AD Connect

Azure AD Connect garantiza que la confianza de Azure AD siempre esté configurada con el conjunto adecuado de reglas de notificación recomendadas. Microsoft recomienda usar Azure AD Connect para administrar la confianza de Azure AD. En esta sección se enumeran las reglas de transformación de emisión establecidas y su descripción.

| Nombre de la regla | Descripción |
| --- | --- |
| Emitir UPN | Esta regla consulta el valor de userprincipalname a partir del atributo configurado en la configuración de sincronización de userprincipalname.|
| Consultar objectguid y msdsconsistencyguid para la notificación de ImmutableId personalizada | Esta regla agrega un valor temporal en la canalización para el valor de objectguid y msdsconsistencyguid si existe. |
| Comprobar la existencia de msdsconsistencyguid | Según si el valor de msdsconsistencyguid existe o no, se establece una marca temporal para indicar qué usar como ImmutableId. |
| Emitir msdsconsistencyguid como identificador inmutable si existe | Emitir msdsconsistencyguid como ImmutableId si el valor existe |
| Emitir objectGuidRule si la regla msdsConsistencyGuid no existe | Si el valor de msdsconsistencyguid no existe, se emitirá el valor de objectguid se emitirá como ImmutableId. |
| Emitir nameidentifier | Esta regla emite el valor de la notificación nameidentifier.|
| Emitir accounttype para equipos unidos a un dominio | Si la entidad que se va a autenticar es un dispositivo unido a un dominio, esta regla emite el tipo de cuenta como DJ para indicarlo. |
| Emitir AccountType con el valor USER cuando no es una cuenta de equipo | Si la entidad que se autentica es un usuario, esta regla emite el tipo de cuenta como User. |
| Emitir issuerid cuando no sea una cuenta de equipo | Esta regla emite el valor de issuerId cuando la entidad de autenticación no es un dispositivo. El valor se crea mediante una expresión regular, que ha configurado Azure AD Connect. La expresión regular se crea después teniendo en cuenta todos los dominios federados con Azure AD Connect. |
| Emitir issuerid para la autenticación de equipos DJ | Esta regla emite el valor de issuerId cuando la entidad de autenticación es un dispositivo. |
| Emitir onpremobjectguid para equipos unidos a un dominio | Si la entidad que se va a autenticar es un dispositivo unido a un dominio, esta regla emite para él el valor de objectguid local. |
| SID principal de paso | Esta regla emite el SID principal de la entidad de autenticación. |
| Notificación de paso: insideCorporateNetwork | Esta regla emite una notificación que ayuda a Azure AD a saber si la autenticación procede de dentro o fuera de la red corporativa. |
| Notificación de paso: Psso |   |
| Emitir notificaciones de expiración de contraseña | Esta regla emite tres notificaciones de tiempo de expiración de contraseña, el número de días que falta para que expire la contraseña de la entidad que se autentica y la dirección URL adonde dirigirse para el cambio de contraseña.|
| Notificación de paso: authnmethodsreferences | El valor de la notificación emitida en esta regla indica qué tipo de autenticación se realizó para la entidad. |
| Notificación de paso: multifactorauthenticationinstant | El valor de esta notificación especifica la hora, en UTC, en que el usuario realizó por última vez la autenticación multifactor. |
| Notificación de paso: AlternateLoginID | Esta regla emite la notificación AlternateLoginID si la autenticación se realizó con el identificador de inicio de sesión alternativo. |

> [!NOTE]
> Las reglas de notificación de emisión de UPN e ImmutableId son diferentes si se usa una opción no predeterminada durante la configuración de Azure AD Connect.

## <a name="restore-issuance-transform-rules"></a>Restauración de las reglas de transformación de emisión

Azure AD Connect, versión 1.1.873.0 o posterior, crea una copia de seguridad de la configuración de confianza de Azure AD cada vez que se realiza una actualización a la configuración de confianza de Azure AD. La configuración de confianza de Azure AD se copia en **%ProgramData%\AADConnect\ADFS**. El nombre de archivo se encuentra en el siguiente formato AadTrust-&lt;date&gt;-&lt;time&gt;.txt, por ejemplo, AadTrust-20180710-150216.txt

![Una captura de pantalla del ejemplo de copia de seguridad de la confianza de Azure AD](./media/how-to-connect-azure-ad-trust/backup.png)

Puede restaurar las reglas de transformación de emisión mediante los siguientes pasos sugeridos:

1. Abra la interfaz de usuario de administración de AD FS en el Administrador del servidor.
2. Vaya a **AD FS &gt; Relying Party Trusts &gt; Microsoft Office 365 Identity Platform &gt; Edit Claims Issuance Policy** (AD FS > Confianzas para usuarios autenticados > Plataforma de identidad de Microsoft Office 365 > Editar directiva de emisión de notificaciones) para abrir las propiedades de confianza de Azure AD.
3. Haga clic en **Add rule** (Agregar regla).
4. En la plantilla de regla de notificación, seleccione Send Claims Using a Custom Rule (Enviar notificaciones mediante una regla personalizada) y haga clic en **Next** (Siguiente).
5. Copie el nombre de la regla de notificación del archivo de copia de seguridad y péguelo en el campo **Claim rule name** (Nombre de regla de notificación).
6. Copie la regla de notificación del archivo de copia de seguridad en el campo de texto **Custom rule** (Regla personalizada) y haga clic en **Finish** (Finalizar).

> [!NOTE]
> Asegúrese de que las reglas adicionales no entren en conflicto con las reglas configuradas por Azure AD Connect.

## <a name="best-practice-for-securing-and-monitoring-the-ad-fs-trust-with-azure-ad"></a>Procedimiento recomendado para proteger y supervisar la confianza de AD FS con Azure AD
Al federar AD FS con Azure AD, es fundamental que la configuración de la federación (relación de confianza configurada entre AD FS y Azure AD) se supervise de forma estrecha y que se capture cualquier actividad inusual o sospechosa. Para ello, se recomienda configurar alertas y recibir notificaciones cada vez que se realicen cambios en la configuración de la federación. Para aprender a configurar alertas, consulte [Supervisión de cambios en la configuración de la federación](how-to-connect-monitor-federation-changes.md). 



## <a name="next-steps"></a>Pasos siguientes
* [Administre y personalice Servicios de federación de Active Directory con Azure AD Connect](how-to-connect-fed-management.md)
