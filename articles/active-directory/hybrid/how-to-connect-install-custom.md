---
title: Personalización de una instalación de Azure Active Directory Connect
description: En este artículo se explican las opciones de instalación personalizada de Azure AD Connect. Siga estas instrucciones para instalar Active Directory mediante Azure AD Connect.
services: active-directory
keywords: qué es Azure AD Connect, instalar Active Directory, componentes necesarios para Azure AD
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 6d42fb79-d9cf-48da-8445-f482c4c536af
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29cc31121a4888c23ccbec1c549f2313d0c9e165
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114439328"
---
# <a name="custom-installation-of-azure-active-directory-connect"></a>Instalación personalizada de Azure Active Directory Connect
Se utiliza *Configuración personalizada* de Azure Active Directory (Azure AD) Connect cuando se desea contar con más opciones para la instalación. Utilice esta configuración, por ejemplo, si tiene varios bosques o si desea configurar características opcionales. Utilice esta configuración personalizada en todos aquellos casos en que la opción [Instalación rápida](how-to-connect-install-express.md) no satisfaga sus necesidades de implementación o topología.

Requisitos previos:
- [Descarga de Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).
- Complete los pasos de requisitos previos de [Azure AD Connect: Hardware y requisitos previos](how-to-connect-install-prerequisites.md). 
- Asegúrese de que posee las cuentas que se describen en [Azure AD Connect: Cuentas y permisos](reference-connect-accounts-permissions.md).

## <a name="custom-installation-settings"></a>Configuración de la instalación personalizada 

Para configurar una instalación personalizada de Azure AD Connect, siga las páginas del asistente que se describen en las siguientes secciones.

### <a name="express-settings"></a>Configuración rápida
En la página **Configuración rápida**, seleccione **Personalizar** para iniciar la instalación con configuración personalizada.  El resto de este artículo le guiará en el proceso de instalación personalizada. Use los vínculos siguientes para ir rápidamente a la información de una página determinada:

- [Componentes necesarios](#install-required-components)
- [Inicio de sesión de usuario](#user-sign-in)
- [Conectarse a Azure AD](#connect-to-azure-ad)
- [Sincronizar](#sync-pages)

### <a name="install-required-components"></a>Instalación de los componentes necesarios
Al instalar los servicios de sincronización, puede dejar sin seleccionar la sección de configuración opcional. Azure AD Connect lo configura todo automáticamente. Configura una instancia de SQL Server 2019 Express LocalDB, crea los grupos apropiados y asigna permisos. Si desea cambiar los valores predeterminados, desactive las casillas correspondientes.  En la tabla siguiente se resumen estas opciones y se proporcionan vínculos a información adicional. 

![Captura de pantalla que muestra selecciones opcionales para los componentes de instalación necesarios en Azure AD Connect.](./media/how-to-connect-install-custom/requiredcomponents2.png)

| Configuración opcional | Descripción |
| --- | --- |
|Especificar una ubicación de instalación personalizada| Permite cambiar la ruta de instalación predeterminada para Azure AD Connect.|
| Usar un SQL Server existente |Permite especificar el nombre de SQL Server y el nombre de la instancia. Elija esta opción si ya dispone de un servidor de base de datos que quiere utilizar. En **Nombre de instancia**, introduzca el nombre de la instancia, una coma y el número de puerto si su instancia de SQL Server no tiene la exploración habilitada.  Luego, especifique el nombre de la base de datos de Azure AD Connect.  Los privilegios de SQL determinan si se puede crear una nueva base de datos o si el administrador de SQL debe crear la base de datos de antemano.  Si tiene permisos de administrador de SQL Server (SA), consulte [Instalación de Azure AD Connect mediante una base de datos existente](how-to-connect-install-existing-database.md).  Si tiene permisos delegados (DBO), consulte [Instalación de Azure AD Connect mediante permisos de administrador delegados de SQL](how-to-connect-install-sql-delegation.md). |
| Usar una cuenta de servicio existente |De forma predeterminada, Azure AD Connect proporciona una cuenta de servicio virtual para los servicios de sincronización. Si usa una instancia remota de SQL Server o un proxy que requiere autenticación, puede usar una *cuenta de servicio administrada* o una cuenta de servicio protegida con contraseña en el dominio. En esos casos, especifique la cuenta que quiere usar. Para ejecutar la instalación, debe ser SA en SQL para poder crear las credenciales de inicio de sesión de la cuenta de servicio. Para más información, consulte [Azure AD Connect: cuentas y permisos](reference-connect-accounts-permissions.md#adsync-service-account). </br></br>Con la compilación más reciente, el administrador de SQL ahora puede aprovisionar la base de datos fuera de banda. A continuación, el administrador de Azure AD Connect puede instalarla con derechos de propietario de la base de datos.  Para más información, consulte [Instalación de Azure AD Connect con permisos de administrador delegado de SQL](how-to-connect-install-sql-delegation.md).|
| Especificar grupos de sincronización personalizada |De forma predeterminada, cuando se instalan los servicios de sincronización, Azure AD Connect crea cuatro grupos que son locales para el servidor. Estos grupos son Administradores, Operadores, Exploración y Restablecimiento de contraseña. Puede especificar sus grupos aquí. Los grupos deben ser locales en el servidor. No se encuentran en el dominio. |
|Importación de la configuración de sincronización (versión preliminar)|Permite importar la configuración de otras versiones de Azure AD Connect.  Para obtener más información, consulte [Importación y exportación de opciones de configuración de Azure AD Connect](how-to-connect-import-export-config.md).|

### <a name="user-sign-in"></a>Inicio de sesión de usuario
Después de instalar los componentes necesarios, seleccione el método de inicio de sesión único de sus usuarios. En la tabla siguiente se describen brevemente las opciones disponibles. Para obtener una descripción completa de los métodos de inicio de sesión, consulte las opciones de [inicio de sesión de usuario](plan-connect-user-signin.md).

![Captura de pantalla que muestra la página "Inicio de sesión de usuario". La opción "Sincronización de hash de contraseñas" está seleccionada.](./media/how-to-connect-install-custom/usersignin4.png)

| Opción Inicio de sesión único | Descripción |
| --- | --- |
| Sincronización de hash de contraseña |Los usuarios pueden iniciar sesión en los servicios en la nube de Microsoft, como Microsoft 365, con la misma contraseña que usan en su red local. Las contraseñas de usuario se sincronizan con Azure AD como un hash de contraseña. La autenticación se produce en la nube. Para más información, consulte [Sincronización de hash de contraseñas](how-to-connect-password-hash-synchronization.md). |
|Autenticación de paso a través|Los usuarios pueden iniciar sesión en los servicios en la nube de Microsoft, como Microsoft 365, con la misma contraseña que usan en su red local.  Para validar las contraseñas de usuario, estas se pasan al controlador de dominio de Active Directory local.
| Federación con AD FS |Los usuarios pueden iniciar sesión en los servicios en la nube de Microsoft, como Microsoft 365, con la misma contraseña que usan en su red local.  Los usuarios se redirigen a su instancia local de Servicios de federación de Active Directory (AD FS) para iniciar sesión. La autenticación se realiza localmente. |
| Federación con PingFederate|Los usuarios pueden iniciar sesión en los servicios en la nube de Microsoft, como Microsoft 365, con la misma contraseña que usan en su red local.  Los usuarios se redirigen a su instancia local de PingFederate para iniciar sesión. La autenticación se realiza localmente. |
| No configurar |No se instala ni configura ninguna característica de inicio de sesión de usuario. Elija esta opción si ya tiene un servidor de federación de terceros u otra solución ya instalada. |
|Habilitar el inicio de sesión único|Esta opción está disponible con la sincronización de hash de contraseñas y la autenticación de paso a través. Proporciona una experiencia de inicio de sesión único para los usuarios de escritorio en redes corporativas. Para más información, consulte [Inicio de sesión único](how-to-connect-sso.md). </br></br>**Nota:** Esta opción no está disponible para los clientes de AD FS. AD FS ya ofrece el mismo nivel de inicio de sesión único.</br>

### <a name="connect-to-azure-ad"></a>Conectarse a Azure
En la página **Conectar a Azure AD**, especifique una cuenta de administrador global y una contraseña. Si seleccionó **Federación con AD FS** en la página anterior, no inicie sesión con una cuenta que esté en un dominio que planea habilitar para la federación. 

Puede utilizar una cuenta en el dominio predeterminado *onmicrosoft.com*, que se incluye con su inquilino de Azure AD. Esta cuenta solo se usa para crear una cuenta de servicio en Azure AD. No se utiliza una vez finalizada la instalación.
  
![Captura de pantalla que muestra la página "Conectar a Azure AD".](./media/how-to-connect-install-custom/connectaad.png)

Si la cuenta de administrador global tiene habilitada la autenticación multifactor, proporcione la contraseña de nuevo en la ventana de inicio de sesión y complete el desafío de autenticación multifactor. El desafío puede ser un código de verificación o una llamada telefónica.  

![Captura de pantalla que muestra la página "Conectar a Azure AD". Un campo de autenticación multifactor solicita al usuario un código.](./media/how-to-connect-install-custom/connectaadmfa.png)

La cuenta de administrador global también puede tener habilitado [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

Si observa un error o tiene problemas con la conectividad, consulte [Solución de problemas de conectividad](tshoot-connect-connectivity.md).

## <a name="sync-pages"></a>Páginas de sincronización

Las secciones siguientes describen las páginas de la sección **Sincronización**.

### <a name="connect-your-directories"></a>Conectar sus directorios
Para conectarse a Active Directory Domain Services (Azure AD DS), Azure AD Connect necesita el nombre del bosque y las credenciales de una cuenta con permisos suficientes.

![Captura de pantalla en la que se muestra la página "Conectar sus directorios".](./media/how-to-connect-install-custom/connectdir01.png)

Después de escribir el nombre del bosque y seleccionar **Agregar directorio**, aparece una ventana. Las opciones se describen en la siguiente tabla.

| Opción | Descripción |
| --- | --- |
| Crear nueva cuenta | Cree la cuenta de Azure AD DS que Azure AD Connect necesita para conectarse al bosque de Active Directory durante la sincronización de directorios. Después de seleccionar esta opción, escriba el nombre de usuario y la contraseña de una cuenta de administrador de organización.  Para crear la cuenta de Azure AD DS requerida, Azure AD Connect usa la cuenta de administrador de organización proporcionada. Puede escribir la parte del dominio en formato de NetBIOS o FQDN. Es decir, escriba *FABRIKAM\administrator* o *fabrikam.com\administrator*. |
| Usar cuenta existente | Proporcione una cuenta de Azure AD DS existente que Azure AD Connect pueda utilizar para conectarse al bosque de Active Directory durante la sincronización de directorios. Puede escribir la parte del dominio en formato de NetBIOS o FQDN. Es decir, escriba *FABRIKAM\syncuser* o *fabrikam.com\syncuser*. Esta cuenta puede ser una cuenta de usuario normal porque solo necesita los permisos de lectura predeterminados. Sin embargo, en función del escenario, puede que necesite permisos adicionales. Para más información, consulte [Azure AD Connect: cuentas y permisos](reference-connect-accounts-permissions.md#create-the-ad-ds-connector-account). |

![Captura de pantalla que muestra la página "Conectar directorio" y la ventana de una cuenta de bosque de AD, donde puede elegir crear una cuenta nueva o usar una cuenta existente.](./media/how-to-connect-install-custom/connectdir02.png)

>[!NOTE]
> A partir de la compilación 1.4.18.0, ya no se puede utilizar una cuenta de administrador de organización o una cuenta de administrador de dominio, como la cuenta del conector de Azure AD DS. Al seleccionar **Usar cuenta existente**, si intenta especificar una cuenta de administrador de organización o una cuenta de administrador de dominio, verá el siguiente error: "No está permitido usar una cuenta de administrador de empresa o dominio para su cuenta de bosque de AD. Deje que Azure AD Connect cree la cuenta o especifique una cuenta de sincronización con los permisos correctos".
>

### <a name="azure-ad-sign-in-configuration"></a>Configuración de inicio de sesión de Azure AD
En la página **Configuración de inicio de sesión de Azure AD**, revise los dominios de nombre principal de usuario (UPN) en el entorno local de Azure AD DS. Estos dominios UPN se han comprobado en Azure AD. En esta página, puede configurar el atributo que se usará para userPrincipalName.

![Captura de pantalla que muestra los dominios no comprobados en la página "Configuración de inicio de sesión de Azure AD".](./media/how-to-connect-install-custom/aadsigninconfig2.png)  

Revise todos los dominios marcados como **Sin agregar** o **No comprobado**. Asegúrese de que los dominios que usa se han comprobado en Azure AD. Después de comprobar los dominios, seleccione el icono de actualización circular. Para más información, consulte [Adición y comprobación del dominio](../fundamentals/add-custom-domain.md).

Los usuarios utilizan el atributo *userPrincipalName* al iniciar sesión en Azure AD y Microsoft 365. Azure AD debe comprobar los dominios, también conocidos como "sufijo UPN", antes de que se sincronicen los usuarios. Microsoft recomienda mantener el atributo userPrincipalName predeterminado. 

Si el atributo userPrincipalName no es enrutable y no se puede comprobar, puede seleccionar otro atributo. Por ejemplo, puede seleccionar email como el atributo que contiene el id. de inicio de sesión. Cuando se usa un atributo distinto de userPrincipalName, se conoce como *identificador alternativo*. 

El valor del atributo de identificador alternativo debe seguir el estándar RFC 822. Se puede utilizar un identificador alternativo con la sincronización de hash de contraseñas, la autenticación de paso a través y la federación. En Active Directory, el atributo no se puede definir como de valores múltiples, aunque solo tenga un valor. Para obtener más información sobre el identificador alternativo, consulte [Autenticación de paso a través: Frequently asked questions](./how-to-connect-pta-faq.yml#does-pass-through-authentication-support--alternate-id--as-the-username--instead-of--userprincipalname--) (inicio de sesión único de conexión directa de Azure Active Directory: preguntas más frecuentes).

>[!NOTE]
> Al habilitar la autenticación de paso a través, debe tener al menos un dominio verificado para continuar con el proceso de instalación personalizada.

> [!WARNING]
> Los identificadores alternativos no son compatibles con todas las cargas de trabajo de Microsoft 365. Para obtener más información, consulte [Configuración de identificador de inicio de sesión alternativo](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).
>

### <a name="domain-and-ou-filtering"></a>Filtrado por dominio y unidad organizativa
De forma predeterminada, todos los dominios y las unidades organizativas (UO) se sincronizan. Si no quiere sincronizar algunos dominios o unidades organizativas con Azure AD, puede borrar las selecciones adecuadas.  

![Captura de pantalla que muestra la página de filtrado por dominio y unidad organizativa.](./media/how-to-connect-install-custom/domainoufiltering.png)  

En esta página se configura el filtrado basado en dominio y en unidad organizativa. Si tiene previsto realizar cambios, consulte [Filtrado basado en dominios](how-to-connect-sync-configure-filtering.md#domain-based-filtering) y [Filtrado basado en unidad organizativa](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Algunas unidades organizativas son esenciales para la funcionalidad, por lo que debe dejarlas seleccionadas.

Si usa el filtrado basado en unidad organizativa con una versión de Azure AD Connect anterior a 1.1.524.0, las unidades organizativas nuevas se sincronizan de forma predeterminada. Si no desea que se sincronicen las nuevas unidades organizativas, puede ajustar el comportamiento predeterminado después del paso [Filtrado basado en unidad organizativa](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Para Azure AD Connect, versión 1.1.524.0 o posteriores, puede indicar si desea que las unidades organizativas nuevas se sincronicen.

Si tiene previsto usar el [Filtrado basado en grupos](#sync-filtering-based-on-groups), asegúrese de que se incluye la unidad organizativa con el grupo y que no se filtra con el filtrado de unidades organizativas. El filtrado de unidad organizativa se evalúa antes del filtrado basado en grupo.

También es posible que algunos dominios no sean accesibles debido a las restricciones del firewall. De forma predeterminada estos dominios no estarán seleccionados y presentan una advertencia.  

![Captura de pantalla que muestra los dominios inaccesibles.](./media/how-to-connect-install-custom/unreachable.png)  

Si ve esta advertencia, asegúrese de que efectivamente no se pueda acceder a estos dominios y de que esta advertencia sea esperada.

### <a name="uniquely-identifying-your-users"></a>Identificación de forma exclusiva de usuarios

En la página **Identificación de usuarios**, elija cómo identificar a los usuarios en los directorios locales y cómo identificarlos mediante el atributo sourceAnchor.

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>Seleccione cómo deben identificarse los usuarios en los directorios locales
Con la característica *Correspondencia entre bosques* puede definir cómo se representan los usuarios de los bosques de AD DS en Azure AD. Un usuario puede estar representado solo una vez en todos los bosques o tener una combinación de cuentas habilitadas y deshabilitadas. En algunos bosques, el usuario también puede aparecer representado como un contacto.

![Captura de pantalla que muestra la página en la que puede identificar a los usuarios de forma exclusiva.](./media/how-to-connect-install-custom/unique2.png)

| Configuración | Descripción |
| --- | --- |
| [Los usuarios solo se representan una vez en todos los bosques](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Todos los usuarios se crean como objetos individuales en Azure AD. Los objetos no se combinan en el metaverso. |
| [Atributo Mail](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Esta opción une a los usuarios y contactos si el atributo Mail tiene el mismo valor en bosques diferentes. Use esta opción si los contactos se han creado mediante GALSync. Si elige esta opción, los objetos de usuario cuyo atributo Mail no esté rellenado no se sincronizarán con Azure AD. |
| [Atributos ObjectSID y msExchangeMasterAccountSID/ msRTCSIP-OriginatorSID](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Esta opción une un usuario habilitado en un bosque de cuentas con un usuario deshabilitado en un bosque de recursos. En Exchange, esta configuración se conoce como buzón vinculado. Puede usar esta opción si solo usa Lync y si Exchange no está presente en el bosque de recursos. |
| Atributos SAMAccountName y MailNickName |Esta opción combina atributos donde se espera que pueda encontrarse el identificador de inicio de sesión del usuario. |
| Elija un atributo específico |Esta opción le permite seleccionar su propio atributo. Si elige esta opción, no se sincronizarán con Azure AD los objetos de usuario cuyo atributo (seleccionado) no esté rellenado. **Limitación:** solo los atributos que ya se encuentran en el metaverso están disponibles para esta opción. |

#### <a name="select-how-users-should-be-identified-by-using-a-source-anchor"></a>Seleccione cómo deben identificarse los usuarios mediante un delimitador de origen
El atributo *sourceAnchor* es inmutable mientras siga vigente un objeto de usuario. Es la clave principal que vincula el usuario local con el usuario de Azure AD.

| Configuración | Descripción |
| --- | --- |
| Permitir que Azure administre el delimitador de origen | Seleccione esta opción si desea que Azure AD elija automáticamente el atributo. Si selecciona esta opción, Azure AD Connect aplica la lógica de selección de atributo sourceAnchor que se describe en [Uso de ms-DS-ConsistencyGuid como sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Una vez finalizada la instalación personalizada, verá qué atributo se seleccionó como el atributo sourceAnchor. |
| Elija un atributo específico | Seleccione esta opción si quiere especificar un atributo existente de AD como atributo sourceAnchor. |

Dado que el atributo sourceAnchor no se puede cambiar, debe elegir un atributo adecuado. Un buen candidato es objectGUID. Este atributo no cambia, salvo que la cuenta de usuario se mueva entre bosques o dominios. No elija atributos que puedan cambiar si una persona se casa o se cambian las asignaciones. 

No puede usar atributos que incluyan un signo de arroba (@), por lo que no puede usar email ni userPrincipalName. El atributo también distingue mayúsculas de minúsculas, por lo que si mueve un objeto entre bosques, asegúrese de conservar las mayúsculas y minúsculas. Los atributos binarios tienen codificación Base64, pero otros tipos de atributo permanecerán en su estado sin codificar. 

En escenarios de federación y en algunas interfaces de Azure AD, el atributo sourceAnchor también se conoce también como *immutableID*. 

Para más información sobre el delimitador de origen, consulte [Conceptos de diseño](plan-connect-design-concepts.md#sourceanchor).

### <a name="sync-filtering-based-on-groups"></a>Filtrado de sincronización basado en grupos
El filtrado por grupos permite sincronizar solo un pequeño subconjunto de objetos para una prueba piloto. Para utilizar esta característica, cree un grupo para este propósito en su instancia de Active Directory local. Luego, agregue los usuarios y grupos que se deben sincronizar con Azure AD como miembros directos. Posteriormente puede agregar y quitar usuarios en este grupo para mantener la lista de objetos que deberían estar presentes en Azure AD. 

Todos los objetos que quiere sincronizar deben ser miembros directos del grupo. Los usuarios, grupos, contactos y equipos o dispositivos deben ser miembros directos. No se resuelve la pertenencia a grupos anidados. Cuando se agrega un grupo como miembro, solo se agrega el grupo en sí. Sus miembros no se agregan.

![Captura de pantalla que muestra la página donde puede elegir cómo filtrar usuarios y dispositivos.](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> Esta característica solo está destinada a admitir una implementación piloto. No la utilice en una implementación de producción completa.
>

En una implementación de producción completa sería difícil mantener un grupo único con todos sus objetos para sincronizar. En lugar de la característica de filtrado basado en grupos, use uno de los métodos descritos en [Configuración del filtrado](how-to-connect-sync-configure-filtering.md).

### <a name="optional-features"></a>Características opcionales
En la página siguiente, puede seleccionar características opcionales para su escenario.

>[!WARNING]
>Las versiones de Azure AD Connect 1.0.8641.0 y las anteriores se basan en Azure Access Control Service para la escritura diferida de contraseñas.  Este servicio se retiró el 7 de noviembre de 2018.  Si usa cualquiera de estas versiones de Azure AD Connect y ha habilitado la escritura diferida de contraseñas, puede que los usuarios pierdan la capacidad de cambiar o restablecer sus contraseñas una vez que el servicio se retire. Estas versiones de Azure AD Connect no admiten la escritura diferida de contraseñas.
>
>Para más información, consulte [Migración desde Azure Access Control Service](../azuread-dev/active-directory-acs-migration.md).
>
>Si quiere usar la escritura diferida de contraseñas, descargue la [última versión de Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

![Captura de pantalla que muestra la página "Características opcionales".](./media/how-to-connect-install-custom/optional2a.png)

> [!WARNING]
> Si Sincronización de Azure AD o Sincronización directa (DirSync) están activas, no active las características de escritura diferida en Azure AD Connect.



| Características opcionales | Descripción |
| --- | --- |
| Implementación híbrida de Exchange |La característica de implementación híbrida de Exchange permite la coexistencia de buzones de Exchange en un entorno local y en Microsoft 365. Azure AD Connect sincroniza un conjunto específico de [atributos](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) de Azure AD en su directorio local. |
| Carpetas públicas de correo de Exchange | La característica Carpetas públicas de correo de Exchange permite sincronizar objetos de carpeta pública habilitada para correo desde su instancia local de Active Directory con Azure AD. |
| Aplicación Azure AD y filtro de atributos |Al habilitar la aplicación Azure AD y el filtrado de atributos, se puede adaptar el conjunto de atributos sincronizados. Esta opción agrega dos páginas más de configuración al asistente. Para más información, consulte [Aplicación Azure AD y filtro de atributos](#azure-ad-app-and-attribute-filtering). |
| Sincronización de hash de contraseña |Si seleccionó la federación como solución de inicio de sesión, puede habilitar la sincronización de hash de contraseñas. A continuación, se puede usar como una opción de copia de seguridad.  </br></br>Si seleccionó la autenticación de paso a través, puede habilitar esta opción para garantizar la compatibilidad de los clientes heredados y como respaldo.</br></br> Para más información, consulte [Sincronización de hash de contraseñas](how-to-connect-password-hash-synchronization.md).|
| escritura diferida de contraseñas |Utilice esta opción para garantizar que la escritura diferida de los cambios de contraseña que se originan en Azure AD en su directorio local. Para más información, consulte [Introducción a la administración de contraseñas](../authentication/tutorial-enable-sspr.md). |
| Escritura diferida de grupos |Si usa grupos de Microsoft 365, puede representar grupos en su instancia local de Active Directory. Esta opción solo está disponible si dispone de Exchange en su instancia de Active Directory local. Para obtener más información, consulte [Escritura diferida de grupos de Azure AD Connect](how-to-connect-group-writeback.md).|
| Escritura diferida de dispositivos |Para los escenarios de acceso condicional, utilice esta opción para la escritura diferida de objetos de dispositivo en Azure AD para su instancia de Active Directory local. Para más información, consulte [Habilitación de la escritura diferida de dispositivos](how-to-connect-device-writeback.md). |
| Sincronización de atributos de las extensiones de directorios |Seleccione esta opción para sincronizar los atributos especificados con Azure AD. Para más información, consulte [Extensiones de directorio](how-to-connect-sync-feature-directory-extensions.md). |

### <a name="azure-ad-app-and-attribute-filtering"></a>Aplicación Azure AD y filtro de atributos
Si desea limitar los atributos que se sincronizan con Azure AD, empiece por seleccionar los servicios que utiliza. Si cambia las selecciones en esta página, debe seleccionar explícitamente un nuevo servicio. Para ello, vuelva a ejecutar el Asistente para la instalación.

![Captura de pantalla que muestra las características opcionales de las aplicaciones de Azure AD.](./media/how-to-connect-install-custom/azureadapps2.png)

En función de los servicios seleccionados en el paso anterior, esta página muestra todos los atributos que se han sincronizado. Esta lista es una combinación de todos los tipos de objeto que se están sincronizando. Si necesita que algunos atributos permanezcan sin sincronizar, puede desactivar su selección.

![Captura de pantalla que muestra las características opcionales de atributos de Azure AD.](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> La eliminación de los atributos puede afectar a la funcionalidad. Para conocer los procedimientos recomendados, consulte [Atributos que sincronizar](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize).
>

### <a name="directory-extension-attribute-sync"></a>Sincronización de atributos de las extensiones de directorios
Puede extender el esquema en Azure AD mediante los atributos personalizados que su organización ha agregado o mediante otros atributos de Active Directory. Para utilizar esta característica, en la página **Características opcionales**, seleccione **Sincronización de atributos de las extensiones de directorios**. En la página **Extensiones de directorio** , puede seleccionar más atributos para sincronizar.

>[!NOTE]
>El campo **Atributos disponibles** distingue mayúsculas de minúsculas.

![Captura de pantalla que muestra la página "Extensiones de directorio".](./media/how-to-connect-install-custom/extension2.png)

Para más información, consulte [Extensiones de directorio](how-to-connect-sync-feature-directory-extensions.md).

### <a name="enabling-single-sign-on"></a>Habilitación del inicio de sesión único
En la página **Inicio de sesión único**, configure el inicio de sesión único para usarlo con la sincronización de contraseña o la autenticación de paso a través. Este paso se realiza una vez para cada bosque que se está sincronizando con Azure AD. La configuración se realiza en dos pasos:

1.  Crear la cuenta de equipo necesaria en su instancia de Active Directory local.
2.  Configurar la zona de intranet de las máquinas cliente para admitir el inicio de sesión único.

#### <a name="create-the-computer-account-in-active-directory"></a>Creación de la cuenta de equipo en Active Directory
Para cada bosque que se ha agregado en Azure AD Connect, debe proporcionar las credenciales de administrador de dominio para poder crear la cuenta de equipo en cada bosque. Las credenciales solo se utilizan para crear la cuenta. No se almacenan ni se usan para ninguna otra operación. Agregue las credenciales en la página **Habilitar el inicio de sesión único**, como se muestra en la siguiente imagen.

![Captura de pantalla de la página "Habilitar el inicio de sesión único". Se agregan las credenciales del bosque.](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>Puede omitir bosques en los que no desea usar el inicio de sesión único.

#### <a name="configure-the-intranet-zone-for-client-machines"></a>Configuración de la zona de intranet para máquinas cliente
Para asegurarse de que el cliente inicia sesión automáticamente en la zona de intranet, compruebe que la dirección URL forma parte de la zona de intranet. Con este paso se asegurará de que el equipo unido a un dominio envía automáticamente un vale de Kerberos cuando está conectado a la red corporativa.

En un equipo que tenga las herramientas de administración de directivas de grupo:

1.  Abra las herramientas de administración de directivas de grupo.
2.  Edite la directiva de grupo que se aplicará a todos los usuarios. Por ejemplo, la directiva de dominio predeterminada.
3.  Vaya a **Configuración de usuario** > **Plantillas administrativas** > **Componentes de Windows** > **Internet Explorer** > **Panel de control de Internet** > **Página de seguridad**. A continuación, seleccione **Lista de asignación de sitio a zona**.
4.  Habilite la directiva. A continuación, en el cuadro de diálogo, escriba un nombre de valor de `https://autologon.microsoftazuread-sso.com` y un valor de `1`. La configuración debería ser similar a la siguiente imagen.
  
    ![Captura de pantalla que muestra zonas de intranet.](./media/how-to-connect-install-custom/sitezone.png)

6.  Seleccione **Aceptar** dos veces.

## <a name="configuring-federation-with-ad-fs"></a>Configuración de federación con AD FS
Puede configurar AD FS con Azure AD Connect con solo unos cuantos clics. Antes de empezar, necesita lo siguiente:

* Un instancia de Windows Server 2012 R2 o posterior para el servidor de federación. La administración remota debe estar habilitada.
* Una instancia de Windows Server 2012 R2 o posterior para el servidor Proxy de aplicación web. La administración remota debe estar habilitada.
* Un certificado TLS/SSL del nombre del servicio de federación que quiere usar (por ejemplo, sts.contoso.com).

>[!NOTE]
>El certificado TLS/SSL de la granja de AD FS se puede actualizar con Azure AD Connect, incluso si no lo usa para administrar la confianza de federación.

### <a name="ad-fs-configuration-prerequisites"></a>Requisitos previos de la configuración de AD FS
Para configurar la granja de AD FS mediante Azure AD Connect, asegúrese de que WinRM está habilitado en los servidores remotos. Asegúrese de haber completado las otras tareas de [Requisitos previos de la federación](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration). Asegúrese también de seguir los requisitos de los puertos que se enumeran en la tabla [Azure AD Connect y servidores de federación AD FS/WAP](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap).

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>Creación de una nueva granja de AD FS o utilización de una granja de AD FS existente
Puede usar una granja de AD FS existente o crear una nueva. Si decide crear una nueva, debe proporcionar el certificado TLS/SSL. Si el certificado TLS/SSL está protegido con contraseña, se le pedirá que la proporcione.

![Captura de pantalla que muestra la página "Granja de AD FS".](./media/how-to-connect-install-custom/adfs1.png)

Si elige usar una granja de AD FS existente, consulte la página donde puede configurar la relación de confianza entre AD FS y Azure AD.

>[!NOTE]
>Azure AD Connect puede usarse para administrar solo una granja de AD FS. Si tiene una confianza de federación existente con Azure AD configurado en la granja de AD FS seleccionada, Azure AD Connect vuelve a crear la confianza desde el principio.

### <a name="specify-the-ad-fs-servers"></a>Especificación de los servidores de AD FS
Especifique los servidores en que desea instalar AD FS. Puede agregar uno o más servidores según su necesidades de capacidad. Antes de realizar esta configuración, una todos los servidores de AD FS a Active Directory. Este paso no es necesario para los servidores Proxy de aplicación web. 

Microsoft recomienda instalar un único servidor de AD FS para las implementaciones piloto y de prueba. Después de la configuración inicial, puede agregar e implementar más servidores para satisfacer sus necesidades de escalado, para lo que debe volver a ejecutar Azure AD Connect.

> [!NOTE]
> Antes de realizar esta configuración, asegúrese de que todos los servidores se han unido a un dominio de Azure AD.
>


![Captura de pantalla que muestra la página "Servidores de federación".](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>Especificación de los servidores Proxy de aplicación web
Especifique los servidores Proxy de aplicación web. El servidor Proxy de aplicación web se implementa en la red perimetral, dirigida a la extranet. Admite solicitudes de autenticación de la extranet. Puede agregar uno o más servidores según su necesidades de capacidad. 

Microsoft recomienda instalar un único servidor Proxy de aplicación web para las implementaciones piloto y de prueba. Después de la configuración inicial, puede agregar e implementar más servidores para satisfacer sus necesidades de escalado, para lo que debe volver a ejecutar Azure AD Connect. Se recomienda tener un número equivalente de servidores proxy para realizar la autenticación desde la intranet.

> [!NOTE]
> - Si la cuenta que usa no es un administrador local en los servidores Proxy de aplicación web, se le solicitarán las credenciales de administrador.
> - Antes de especificar servidores Proxy de aplicación web, asegúrese de que hay conectividad HTTP/HTTPS entre el servidor de Azure AD Connect y el servidor Proxy de aplicación web.
> - Asegúrese también de que hay conectividad HTTP/HTTPS entre el servidor de aplicaciones web y el servidor de AD FS para permitir que fluyan las solicitudes de autenticación.
>


![Captura de pantalla que muestra la página Servidores de Proxy de aplicación web.](./media/how-to-connect-install-custom/adfs3.png)

Se le solicita que escriba las credenciales, con el fin de que el servidor de aplicaciones web pueda establecer una conexión segura con el servidor de AD FS. Estas credenciales deben ser de un administrador local en el servidor de AD FS.

![Captura de pantalla que muestra la página "Credenciales". Las credenciales de administrador se escriben en los campos de nombre de usuario y contraseña.](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>Especificación de la cuenta de servicio para el servicio AD FS
El servicio de AD FS requiere una cuenta de servicio de dominio para autenticar usuarios y para buscar información de estos en Active Directory. Puede admitir dos tipos de cuentas de servicio:

* **Cuenta de servicio administrada de grupo**: este tipo de cuenta se introdujo en AD DS con Windows Server 2012. Este tipo de cuenta proporciona servicios como AD FS. Es una sola cuenta en la que no es necesario actualizar la contraseña con regularidad. Utilice esta opción si tiene controladores de dominio de Windows Server 2012 en el dominio al que pertenecen los servidores de AD FS.
* **Cuenta de usuario de dominio**: Este tipo de cuenta requiere que proporcione una contraseña y la actualice periódicamente cuando expire. Utilice esta opción solo si no tiene controladores de dominio de Windows Server 2012 en el dominio al que pertenecen los servidores de AD FS.

Si ha seleccionado **Crear una cuenta de servicio administrado de grupos** y esta característica no se ha usado nunca en Active Directory, introduzca las credenciales de administrador de organización. Estas se usarán para iniciar el almacén de claves y habilitar la característica en Active Directory.

> [!NOTE]
> Azure AD Connect comprueba si el servicio AD FS ya está registrado como nombre de entidad de seguridad de servicio (SPN) en el dominio.  Azure AD DS no permite el registro de SPN duplicados al mismo tiempo.  Si se encuentra un SPN duplicado, no podrá continuar hasta que este se elimine.

![Captura de pantalla que muestra la página "Cuenta de servicio de AD FS".](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-want-to-federate"></a>Selección del dominio de Azure AD que quiere federar
Utilice la página **Dominio de Azure AD** para configurar la relación de federación entre AD FS y Azure AD. Aquí, debe configure AD FS para proporcionar tokens de seguridad a Azure AD. También se debe configurar Azure AD para que confíe en los tokens de esta instancia de AD FS. 

En esta página, solo se permite configurar un único dominio en la instalación inicial. Si vuelve a ejecutar Azure AD Connect después podrá configurar más dominios.

![Captura de pantalla que muestra la página "Dominio de Azure AD".](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>Comprobación del dominio de Azure AD seleccionado para la federación
Cuando se selecciona el dominio que se quiere federar, Azure AD Connect proporciona la información que puede utilizar para comprobar un dominio no comprobado. Para más información, consulte [Adición y comprobación del dominio](../fundamentals/add-custom-domain.md).

![Captura de pantalla que muestra la página "Dominio de Azure AD", incluida la información que puede usar para comprobar el dominio.](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> Azure AD Connect intenta comprobar el dominio durante la fase de configuración. Si no agrega los registros del Sistema de nombres de dominio (DNS) necesarios, la configuración no se puede completar.
>


## <a name="configuring-federation-with-pingfederate"></a>Configuración de federación con PingFederate
Puede configurar PingFederate con Azure AD Connect con solo unos cuantos clics. Se necesitan los siguientes requisitos previos:
- PingFederate 8.4 o posterior.  Para obtener más información, consulte [Integración de PingFederate con Azure Active Directory y Microsoft 365](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html).
- Un certificado TLS/SSL del nombre del servicio de federación que quiere usar (por ejemplo, sts.contoso.com).

### <a name="verify-the-domain"></a>Comprobar el dominio
Después de optar por realizar la configuración con PingFederate, se le pedirá que compruebe el dominio que quiere federar.  Seleccione el dominio del menú desplegable.

![Captura de pantalla que muestra la página "Dominio de Azure AD". El dominio de ejemplo "contoso.com" está seleccionado.](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>Exportar la configuración de PingFederate


Configure PingFederate como el servidor de federación para cada dominio de Azure federado.  Seleccione **Exportar configuración** para compartir esta información con el administrador de PingFederate.  El administrador del servidor de federación actualiza la configuración y, a continuación, proporciona la dirección URL del servidor PingFederate y el número de puerto para que Azure AD Connect pueda comprobar la configuración de metadatos.  

![Captura de pantalla que muestra la página "Configuración de PingFederate". El botón "Exportar configuración" aparece cerca de la parte superior de la página.](./media/how-to-connect-install-custom/ping2.png)

Póngase en contacto con el administrador de PingFederate para solucionar los problemas de validación.  La imagen siguiente muestra información sobre un servidor de PingFederate que no tiene una relación de confianza válida con Azure.

![Captura de pantalla que muestra información del servidor: Se encontró el servidor PingFederate, pero la conexión del proveedor de servicios de Azure falta o está deshabilitada.](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>Comprobar la conectividad de la federación
Azure AD Connect intenta validar los puntos de conexión de autenticación que ha recuperado de los metadatos de PingFederate en el paso anterior.  Primero, Azure AD Connect intenta resolver los puntos de conexión mediante los servidores DNS locales.  A continuación, intenta resolver los puntos de conexión mediante un proveedor DNS externo.  Póngase en contacto con el administrador de PingFederate para solucionar los problemas de validación.  

![Captura de pantalla que muestra la página "Comprobar la conectividad".](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-sign-in"></a>Comprobación del inicio de sesión de federación
Por último, puede comprobar el flujo de inicio de sesión federado recién configurado. Para ello, inicie sesión en el dominio federado. Si el inicio de sesión es correcto, la federación con PingFederate está configurada correctamente.

![Captura de pantalla que muestra la página "Comprobar el inicio de sesión federado". Un mensaje en la parte inferior indica un inicio de sesión correcto.](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>Configurar y comprobar páginas
La configuración se realiza en la página **Configurar**.

> [!NOTE]
> Si configuró la federación, asegúrese de que también ha configurado [Resolución de nombres para los servidores de federación](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers) antes de seguir con la instalación.
>



![Captura de pantalla que muestra la página "Listo para configurar".](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="use-staging-mode"></a>Uso del modo de almacenamiento provisional
Con el modo de almacenamiento provisional es posible configurar un nuevo servidor de sincronización en paralelo. Si desea usar esta configuración, solo un servidor de sincronización puede realizar la exportación a un directorio en la nube. No obstante, para realizar el movimiento desde otro servidor, por ejemplo, uno que ejecute DirSync, puede habilitar Azure AD Connect en modo de almacenamiento provisional. 

Cuando se habilita la configuración del almacenamiento provisional, el motor de sincronización importa y sincroniza los datos de la forma habitual. Sin embargo, no exporta ningún dato a Azure AD ni a Active Directory. En el modo de almacenamiento provisional, las características de sincronización de contraseñas y de escritura diferida de contraseñas están deshabilitadas.

![Captura de pantalla que muestra la opción "Habilitar modo de almacenamiento provisional".](./media/how-to-connect-install-custom/stagingmode.png)

En el modo de almacenamiento provisional, es posible realizar los cambios necesarios en el motor de sincronización y revisar lo que se va a exportar. Cuando la configuración esté correcta, vuelva a ejecutar al Asistente para la instalación y deshabilite el modo provisional. 

Ahora los datos se exportan a Azure AD desde el servidor. Asegúrese de deshabilitar al otro servidor al mismo tiempo para que solo un servidor esté exportando activamente.

Para más información, consulte [Modo provisional](how-to-connect-sync-staging-server.md).

### <a name="verify-your-federation-configuration"></a>Comprobación de la configuración de la federación
Azure AD Connect comprueba la configuración de DNS cuando selecciona el botón **Comprobar**. Comprueba la siguiente configuración:

* **Conectividad de la intranet**
    * Resolución del FQDN de federación: Azure AD Connect comprueba si el sistema DNS puede resolver el FQDN de federación para garantizar la conectividad. Si Azure AD Connect no puede resolver el FQDN, se produce un error de comprobación. Para completar la comprobación, asegúrese de que existe un registro DNS para el FQDN del servicio de federación.
    * Registro D de DNS: Azure AD Connect comprueba si el servicio de federación tiene un registro D. En ausencia de un registro D, se produce un error de comprobación. Para completar la comprobación, cree un registro D (en lugar de uno CNAME) para el FQDN de federación.
* **Conectividad a la extranet**
    * Resolución del FQDN de federación: Azure AD Connect comprueba si el sistema DNS puede resolver el FQDN de federación para garantizar la conectividad.

      ![Captura de pantalla que muestra la página "Instalación completada".](./media/how-to-connect-install-custom/completed.png)

      ![Captura de pantalla que muestra la página "Instalación completada". Un mensaje indica que se ha comprobado la configuración de la intranet.](./media/how-to-connect-install-custom/adfs7.png)

Para validar la autenticación de extremo a extremo, realice manualmente una o más de las pruebas siguientes:

* Una vez finalizada la sincronización, utilice la tarea adicional **Comprobar el inicio de sesión federado** en Azure AD Connect para comprobar la autenticación de una cuenta de usuario local de su elección.
* Desde una máquina unida a un dominio en la intranet, asegúrese de que puede iniciar sesión desde un explorador. Conectarse a https://myapps.microsoft.com. A continuación, use la cuenta con la que ha iniciado sesión para comprobar el inicio de sesión. La cuenta de administrador de Azure AD DS integrada no está sincronizada y no se puede usar para la verificación.
* Asegúrese de que puede iniciar sesión desde un dispositivo en la extranet. En un equipo doméstico o un dispositivo móvil, conéctese a https://myapps.microsoft.com. A continuación, proporcione sus credenciales.
* Valide el inicio de sesión de un cliente mejorado. Conectarse a https://testconnectivity.microsoft.com. A continuación, seleccione **Office 365** > **Prueba de inicio de sesión único de Office 365**.

## <a name="troubleshoot"></a>Solución de problemas
La siguiente sección contiene información de solución de problemas que puede usar si se produce un problema al instalar Azure AD Connect.

Al personalizar una instalación de Azure AD Connect, en la página **Instalar componentes necesarios**, puede seleccionar **Usar un SQL Server existente**. Puede aparecer el error siguiente: "La base de datos ADSync ya contiene datos y no se puede sobrescribir. Quite la base de datos existente y vuelva a intentarlo".

![Captura de pantalla que muestra la página "Instalar componentes necesarios". Aparece un error en la parte inferior de la página.](./media/how-to-connect-install-custom/error1.png)

Se muestra este error porque ya existe una base de datos denominada *ADSync* en la instancia de SQL de SQL Server que ha especificado.

Se suele mostrar después de desinstalar Azure AD Connect.  La base de datos no se elimina del equipo que ejecuta SQL Server al desinstalar Azure AD Connect.

Para corregir este problema:

1. Compruebe la base de datos ADSync que Azure AD Connect usó antes de desinstalarse. Asegúrese de que la base de datos ya no se utiliza.

2. Realice una copia de seguridad de la base de datos.

3. Elimine la base de datos:
    1. Use **Microsoft SQL Server Management Studio** para conectarse a la instancia de SQL. 
    1. Busque la base de datos **ADSync** y haga clic con el botón derecho en esta.
    1. En el menú contextual, seleccione **Eliminar**.
    1. Seleccione **Aceptar** para eliminar la base de datos.

![Captura de pantalla que muestra Microsoft SQL Server Management Studio. ADSync está seleccionado.](./media/how-to-connect-install-custom/error2.png)

Después de eliminar la base de datos ADSync, seleccione **Instalar** para volver a intentar la instalación.

## <a name="next-steps"></a>Pasos siguientes
Cuando finalice la instalación, cierre la sesión de Windows. A continuación, inicie de sesión de nuevo antes de utilizar Synchronization Service Manager o el Editor de reglas de sincronización.

Ahora que ha instalado Azure AD Connect, puede [comprobar la instalación y asignar licencias](how-to-connect-post-installation.md).

Para obtener más información acerca de las características que ha habilitado durante la instalación, consulte [Evitación de eliminaciones accidentales](how-to-connect-sync-feature-prevent-accidental-deletes.md) y [Azure AD Connect Health](how-to-connect-health-sync.md).

Para obtener más información sobre otros temas comunes, consulte [Sincronización de Azure AD Connect: Scheduler](how-to-connect-sync-feature-scheduler.md) e [Integración de las identidades locales con Azure AD](whatis-hybrid-identity.md).
