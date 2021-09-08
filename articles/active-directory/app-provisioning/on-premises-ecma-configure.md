---
title: Configuración del host del conector ECMA de Azure AD
description: En este artículo se describe cómo configurar el host del conector ECMA de Azure AD.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/04/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 769b5a312572a2c10f55ee3efbb54191b4f13707
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449698"
---
# <a name="configure-the-azure-ad-ecma-connector-host-and-the-provisioning-agent"></a>Configuración del host del conector ECMA de Azure AD y el agente de aprovisionamiento

>[!IMPORTANT]
> La versión preliminar de aprovisionamiento local se encuentra actualmente en una versión preliminar solo por invitación. Para solicitar acceso a la capacidad, use el [formulario de solicitud de acceso](https://aka.ms/onpremprovisioningpublicpreviewaccess). La versión preliminar estará a disposición de más clientes y conectores durante los próximos meses, cuando se prepare la disponibilidad general.

En este artículo se proporciona una guía sobre cómo configurar el host del conector ECMA de Azure Active Directory (Azure AD) y el agente de aprovisionamiento una vez que los ha instalado correctamente.

Este flujo le guía a través del proceso de instalación y configuración del host del conector ECMA de Azure AD. 

 ![Diagrama que muestra el flujo de instalación.](./media/on-premises-ecma-configure/flow-1.png)

Para más información sobre la instalación y configuración, consulte:
   - [Requisitos previos del host del conector ECMA de Azure AD](on-premises-ecma-prerequisites.md)
   - [Instalación del host del conector ECMA de Azure AD](on-premises-ecma-install.md)
   - [Configuración del conector SQL genérico del host del conector ECMA de Azure AD](on-premises-sql-connector-configure.md)
   
## <a name="configure-the-azure-ad-ecma-connector-host"></a>Configuración del host del conector ECMA de Azure AD
La configuración del host del conector ECMA de Azure AD se produce en dos partes:
    
   - **Configuración de los valores**: configure el puerto y el certificado del host del conector ECMA de Azure AD que se usará. Este paso solo se realiza la primera vez que se inicia el host del conector ECMA.
   - **Crear un conector**: cree un conector (por ejemplo, SQL o LDAP) para permitir que el host del conector ECMA de Azure AD exporte o importe datos a un origen de datos.

### <a name="configure-the-settings"></a>Configuración de los valores
La primera vez que inicie Azure AD host del conector ECMA, verá un número de puerto que se rellena con el valor predeterminado **8585**. 

 ![Captura de pantalla que muestra la configuración de los valores.](.\media\on-premises-ecma-configure\configure-1.png)

Para la versión preliminar, tendrá que generar un certificado autofirmado nuevo.

 >[!NOTE]
 >Esta versión preliminar usa un certificado sujeto a limitación temporal. El certificado autogenerado se firma automáticamente. Parte de la raíz de confianza y la SAN coinciden con el nombre de host.


### <a name="create-a-connector"></a>Creación de un conector
Ahora tiene que crear un conector para su uso por parte del host del conector ECMA de Azure AD. Este conector permitirá que el host del conector ECMA exporte datos al origen de datos para el conector que cree. También puede usarlo para importar datos si lo desea.

Los pasos de configuración para cada uno de los conectores individuales son más largos y se incluyen en sus propios documentos.

Para crear y configurar un conector, use el [conector SQL genérico](on-premises-sql-connector-configure.md). Este conector funcionará con bases de datos de SQL de Microsoft, como Azure SQL Database o Azure Database for MySQL.

## <a name="establish-connectivity-between-azure-ad-and-the-azure-ad-ecma-connector-host"></a>Establecimiento de conectividad entre Azure AD y el host del conector ECMA de Azure AD
En las secciones siguientes encontrará una guía para establecer la conectividad con el host del conector ECMA de Azure AD local y Azure AD.

### <a name="ensure-the-ecma2host-service-is-running"></a>Comprobación de que el servicio ECMA2Host se está ejecutando
1. En el servidor en el que se ejecuta el host del conector ECMA de Azure AD, seleccione **Iniciar**.
1. Escriba **run** y luego **services.msc** en el cuadro.
1. En la lista **Servicios**, asegúrese de que **Microsoft ECMA2Host** esté presente y en ejecución. Si no es así, seleccione **Iniciar**.

   ![Captura de pantalla que muestra que el servicio se está ejecutando.](.\media\on-premises-ecma-configure\configure-2.png)

### <a name="add-an-enterprise-application"></a>Adición de una aplicación empresarial
1. Inicie sesión en Azure Portal como administrador de la aplicación.
1. En el portal, vaya a **Azure Active Directory** > **Aplicaciones empresariales**.
1. Seleccione **Nueva aplicación**.

   ![Captura de pantalla que muestra Agregar una nueva aplicación.](.\media\on-premises-ecma-configure\configure-4.png)
1. Busque la aplicación **Aprovisionamiento local** en la galería y haga clic en **Crear**.

### <a name="configure-the-application-and-test"></a>Configuración de la aplicación y prueba
  1. Una vez que se haya creado la aplicación, seleccione la página **Aprovisionamiento**.
  1. Seleccione **Comenzar**.
  
     ![Captura de pantalla que muestra Comenzar.](.\media\on-premises-ecma-configure\configure-6.png)
  1. En la página **Aprovisionamiento**, cambie el **Modo de aprovisionamiento** al **Modo automático**.

      ![Captura de pantalla que muestra el cambio de modo.](.\media\on-premises-ecma-configure\configure-7.png)
  1. En la sección **Conectividad local**, seleccione el agente que ha implementado y seleccione **Asignar agentes**.
  
      ![Captura de pantalla que muestra la asignación de un agente.](.\media\on-premises-ecma-configure\configure-8.png)</br>

      >[!NOTE]
      >Después de agregar el agente, espere de 10 a 20 minutos a que se complete el registro. La prueba de conectividad no funciona hasta que se completa el registro.
      >
      >Como alternativa, puede reiniciar el agente de aprovisionamiento en el servidor para forzar que el registro del agente se complete. Vaya al servidor, busque **servicios** en la barra de búsqueda de Windows, identifique el **servicio del agente de aprovisionamiento de Azure AD Connect**, haga clic con el botón derecho en el servicio y reinicie.

  1. Pasados 10 minutos, en la sección **Credenciales de administración**, escriba la siguiente dirección URL. Reemplace la parte `"connectorName"` por el nombre del conector en el host ECMA.

      |Propiedad|Valor|
      |-----|-----|
      |URL de inquilino|https://localhost:8585/ecma2host_connectorName/scim|

  1. Escriba el valor del token secreto que ha definido al crear el conector.
  1. Seleccione **Probar conexión** y espere un minuto.
  
     ![Captura de pantalla que muestra Probar conexión.](.\media\on-premises-ecma-configure\configure-5.png)

     >[!NOTE]
     >Asegúrese de esperar de 10 a 20 minutos después de asignar el agente para probar la conexión. Se producirá un error en la conexión si no se ha completado el registro.

  1. Una vez que se supere la prueba de conexión, seleccione **Guardar**.</br>
  
     ![Captura de pantalla que muestra que la prueba se ha hecho correctamente.](.\media\on-premises-ecma-configure\configure-9.png)

## <a name="configure-whos-in-scope-for-provisioning"></a>Configuración de quién está en el ámbito de aprovisionamiento
Ahora que el host del conector ECMA de Azure AD ya se comunica con Azure AD, puede pasar a configurar quién está dentro del ámbito de aprovisionamiento. En las secciones siguientes encontrará información sobre definir el ámbito de los usuarios.

### <a name="assign-users-to-your-application"></a>Asignación de usuarios a la aplicación
Con el uso de Azure AD, puede establecer el ámbito de quién se debe aprovisionar en función de la asignación a una aplicación o mediante el filtrado por un atributo determinado. Decida quién debe estar en el ámbito del aprovisionamiento y defina las reglas de limitación del ámbito según sea necesario. Para más información, consulte [Administración de la asignación de usuarios para una aplicación en Azure Active Directory](../../active-directory/manage-apps/assign-user-or-group-access-portal.md).

### <a name="configure-your-attribute-mappings"></a>Configuración de las asignaciones de atributos
Ahora asigne los atributos de usuario en Azure AD a los atributos de la aplicación de destino. El servicio de aprovisionamiento de Azure AD se basa en el estándar SCIM para el aprovisionamiento. Como resultado, los atributos que se han presentado tienen el espacio de nombres SCIM. En el ejemplo siguiente se muestra cómo puede asignar los atributos **mail** y **objectId** de Azure AD a los atributos **Email** e **InternalGUID** de una aplicación.

>[!NOTE]
>La asignación predeterminada conecta **userPrincipalName** a un nombre de atributo *PLACEHOLDER*. Tendrá que cambiar el atributo *PLACEHOLDER* a uno que se encuentre en la aplicación. Para más información, consulte [Emparejamiento de usuarios en los sistemas de origen y destino](customize-application-attributes.md#matching-users-in-the-source-and-target--systems).

|Nombre del atributo en Azure AD|Nombre de atributo en SCIM|Nombre de atributo en la aplicación de destino|
|-----|-----|-----|
|mail|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:Email|Email|
|objectId|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:InternalGUID|InternalGUID|

### <a name="configure-attribute-mapping"></a>Configuración de la asignación de atributos
 1. En el portal de Azure AD, en **Aplicaciones empresariales**, seleccione la página **Aprovisionamiento**.
 2. Seleccione **Comenzar**.
 3. Expanda **Asignaciones** y seleccione **Aprovisionar usuarios de Azure Active Directory**.
 
    ![Captura de pantalla que muestra Aprovisionar usuarios de Azure Active Directory.](.\media\on-premises-ecma-configure\configure-10.png)
 1. Seleccione **Agregar nueva asignación**.
 
    ![Captura de pantalla que muestra Agregar nueva asignación.](.\media\on-premises-ecma-configure\configure-11.png)
 1. Especifique los atributos de origen y destino, y seleccione **Aceptar**.</br>
 
    ![Captura de pantalla que muestra el panel de Edición de atributo.](.\media\on-premises-ecma-configure\configure-12.png)


Para más información sobre la asignación de atributos de usuario de aplicaciones a Azure AD, consulte [Tutorial: Personalización de las asignaciones de atributos de aprovisionamiento de usuarios para aplicaciones SaaS en Azure Active Directory](customize-application-attributes.md).

### <a name="test-your-configuration-by-provisioning-users-on-demand"></a>Prueba de la configuración mediante el aprovisionamiento de usuarios a petición
Para probar la configuración, puede usar el aprovisionamiento de usuarios a petición. Para más información sobre el aprovisionamiento de usuarios a petición, consulte [Aprovisionamiento a petición](provision-on-demand.md).

 1. Vaya al panel de inicio de sesión único y vuelva al panel de aprovisionamiento. En el nuevo panel de información general de aprovisionamiento, seleccione **A petición**.
 1. Pruebe el aprovisionamiento de algunos usuarios a petición como se describe en [Aprovisionamiento a petición en Azure Active Directory](provision-on-demand.md).
 
    ![Captura de pantalla que muestra las pruebas de aprovisionamiento.](.\media\on-premises-ecma-configure\configure-13.png)

### <a name="start-provisioning-users"></a>Inicio del aprovisionamiento de usuarios
 1. Una vez que el aprovisionamiento a petición se realice correctamente, vuelva a la página de configuración del aprovisionamiento. Asegúrese de que el ámbito esté establecido solo en los usuarios y grupos asignados, **active** el aprovisionamiento y seleccione **Guardar**.
 
    ![Captura de pantalla que muestra el inicio del aprovisionamiento.](.\media\on-premises-ecma-configure\configure-14.png)

1. Espere varios minutos para que se inicie el aprovisionamiento. Puede tardar hasta 40 minutos. Una vez completado el trabajo de aprovisionamiento, como se explica en la sección siguiente, puede **desactivar** el estado de aprovisionamiento y seleccionar **Guardar**. Este paso impedirá que el servicio de aprovisionamiento se ejecute en el futuro.

### <a name="verify-users-were-successfully-provisioned"></a>Comprobación de aprovisionamiento correcto de los usuarios
Después de esperar, compruebe el origen de datos para ver si se están aprovisionando nuevos usuarios.

 ![Captura de pantalla que muestra la comprobación de que los usuarios se están aprovisionando.](.\media\on-premises-ecma-configure\configure-15.png)

## <a name="monitor-your-deployment"></a>Supervisión de la implementación

1. Use los registros de aprovisionamiento para determinar los usuarios que se han aprovisionado correctamente y los que no.
1. Cree alertas, paneles y consultas personalizados mediante la integración de Azure Monitor.
1. Si la configuración de aprovisionamiento parece ser incorrecta, la aplicación pasará a estar en cuarentena. Obtenga más información sobre los [estados de cuarentena](https://github.com/MicrosoftDocs/azure-docs-pr/compare/application-provisioning-quarantine-status.md?expand=1).

## <a name="next-steps"></a>Pasos siguientes

- [Requisitos previos del host del conector ECMA de Azure AD](on-premises-ecma-prerequisites.md)
- [Instalación del host del conector ECMA de Azure AD](on-premises-ecma-install.md)
- [Conector SQL genérico](on-premises-sql-connector-configure.md)
