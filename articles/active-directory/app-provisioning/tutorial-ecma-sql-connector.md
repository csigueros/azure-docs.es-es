---
title: Tutorial sobre el conector SQL genérico del host del conector ECMA de Azure AD
description: En este tutorial se explica cómo usar el conector SQL genérico de aprovisionamiento de aplicaciones local.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/13/2021
ms.subservice: hybrid
ms.author: billmath
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8ef75392676b2c766064eefb3c8113734bfa622
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437544"
---
# <a name="azure-ad-ecma-connector-host-generic-sql-connector-tutorial"></a>Tutorial sobre el conector SQL genérico del host del conector ECMA de Azure AD

>[!IMPORTANT]
> La versión preliminar de aprovisionamiento local se encuentra actualmente en una versión preliminar solo por invitación. Para solicitar acceso a la capacidad, use el [formulario de solicitud de acceso](https://aka.ms/onpremprovisioningpublicpreviewaccess). La versión preliminar estará a disposición de más clientes y conectores durante los próximos meses, cuando se prepare la disponibilidad general.

En este tutorial se describen los pasos que hay que realizar para aprovisionar y desaprovisionar automáticamente usuarios de Azure Active Directory (Azure AD) en una base de datos SQL. Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

En este tutorial se explica cómo configurar y usar el conector SQL genérico con el host del conector ECMA de Azure AD. 

## <a name="prepare-the-sample-database"></a>Preparación de la base de datos de ejemplo
En un servidor con SQL Server, ejecute el script SQL que se encuentra en el [Apéndice A](#appendix-a). Este script crea una base de datos de ejemplo con el nombre CONTOSO. Esta es la base de datos en la que se van a aprovisionar usuarios.


## <a name="create-the-dsn-connection-file"></a>Creación del archivo de conexión DNS
El conector SQL genérico es un archivo DSN para conectarse al servidor SQL. Primero hay que crear un archivo con la información de conexión ODBC.

1. Inicie la utilidad de administración ODBC en el servidor.
  
     ![Captura de pantalla que muestra la administración ODBC.](./media/tutorial-ecma-sql-connector/odbc.png)
1. Seleccione la pestaña **Archivo DNS** y luego **Agregar**. 
 
     ![Captura de pantalla que muestra la pestaña Archivo DNS.](./media/tutorial-ecma-sql-connector/dsn-2.png)
1. Seleccione **SQL Server Native Client 11.0** y luego **Siguiente**. 
 
     ![Captura de pantalla que muestra la selección de un cliente nativo.](./media/tutorial-ecma-sql-connector/dsn-3.png)
1. Asigne un nombre al archivo, como **GenericSQL**, y seleccione **Siguiente**. 
 
     ![Captura de pantalla que muestra la asignación de nombre al conector.](./media/tutorial-ecma-sql-connector/dsn-4.png)
1. Seleccione **Finalizar**. 
 
     ![Captura de pantalla que muestra Finalizar.](./media/tutorial-ecma-sql-connector/dsn-5.png)
1. Ahora configure la conexión. Escriba **APP1** como nombre del servidor y seleccione **Siguiente**.

     ![Captura de pantalla que muestra la asignación del nombre del servidor.](./media/tutorial-ecma-sql-connector/dsn-6.png)
1. Mantenga la autenticación de Windows y seleccione **Siguiente**.

     ![Captura de pantalla que muestra la autenticación de Windows.](./media/tutorial-ecma-sql-connector/dsn-7.png)
1. Escriba el nombre de la base de datos de ejemplo, que es **CONTOSO**.

     ![Captura de pantalla que muestra la asignación del nombre de la base de datos.](./media/tutorial-ecma-sql-connector/dsn-8.png)
1. Mantenga los valores predeterminados de esta pantalla y seleccione **Finalizar**.

     ![Captura de pantalla que muestra la selección de Finalizar.](./media/tutorial-ecma-sql-connector/dsn-9.png)
1. Para comprobar que todo funciona según lo previsto, seleccione **Probar origen de datos**. 
 
     ![Captura de pantalla que muestra Probar origen de datos.](./media/tutorial-ecma-sql-connector/dsn-10.png)
1. Asegúrese de que la prueba se ha realizado correctamente.

     ![Captura de pantalla que muestra que la prueba se ha superado.](./media/tutorial-ecma-sql-connector/dsn-11.png)
1. Seleccione **Aceptar** dos veces. Cierre el Administrador de orígenes de datos ODBC.

## <a name="download-and-install-the-azure-ad-connect-provisioning-agent-package"></a>Descarga e instalación del paquete del agente de aprovisionamiento de Azure AD Connect

 1. Inicie sesión en el servidor que va a usar con permisos de administrador de empresa.
 1. Inicie sesión en Azure Portal y, a continuación, vaya a **Azure Active Directory**.
 1. En el menú de la izquierda, seleccione **Azure AD Connect**.
 1. Seleccione **Manage cloud sync** (Administrar sincronización en la nube)  > **Revisar todos los agentes**.
 1. Descargue el paquete del agente de aprovisionamiento de Azure AD Connect desde Azure Portal.
 1. Acepte los términos y seleccione **Descargar**.
 1. Ejecute el instalador de aprovisionamiento de Azure AD Connect, AADConnectProvisioningAgentSetup.msi.
 1. En la pantalla **Microsoft Azure AD Connect Provisioning Agent Package**, seleccione **Instalar**.
 
    ![Captura de pantalla que muestra la pantalla Microsoft Azure AD Connect Provisioning Agent Package.](media/on-premises-ecma-install/install-1.png)</br>
 1. Una vez finalizada esta operación, se inicia el Asistente para configuración. Seleccione **Siguiente**.
 
    ![Captura de pantalla que muestra la pantalla inicial.](media/on-premises-ecma-install/install-2.png)</br>
 1. En la pantalla **Seleccionar extensión**, seleccione **On-premises application provisioning (Azure AD to application)** (Aprovisionamiento de aplicaciones local (Azure AD a aplicación)) y luego **Siguiente**. 
 
    ![Captura de pantalla que muestra la pantalla Seleccionar extensión.](media/on-premises-ecma-install/install-3.png)</br>
 1. Use la cuenta de administrador global e inicie sesión en Azure AD.
 
     ![Captura de pantalla que muestra la página de inicio de sesión de Azure.](media/on-premises-ecma-install/install-4.png)</br>
 1. En la pantalla **Configuración del agente**, seleccione **Confirmar**.
 
     ![Captura de pantalla que muestra la confirmación de la instalación.](media/on-premises-ecma-install/install-5.png)</br>
 1. Una vez completada la instalación, debería ver un mensaje en la parte inferior del asistente. Seleccione **Salir**.
 
     ![Captura de pantalla que muestra el botón Salir.](media/on-premises-ecma-install/install-6.png)</br>
 
## <a name="configure-the-azure-ad-ecma-connector-host"></a>Configuración del host del conector ECMA de Azure AD
1. En el escritorio, seleccione el acceso directo ECMA.
1. Una vez que se inicie la configuración del host del conector ECMA, deje el puerto predeterminado **8585** y seleccione **Generar** para generar un certificado. El certificado autogenerado se firma automáticamente como parte de la raíz de confianza. SAN coincide con el nombre de host.

     ![Captura de pantalla que muestra la configuración de los valores.](.\media\on-premises-ecma-configure\configure-1.png)
1. Seleccione **Guardar**.

## <a name="create-a-generic-sql-connector"></a>Creación de un conector SQL genérico
 1. Seleccione el acceso directo Host del conector ECMA en el escritorio.
 1. Seleccione **Nuevo conector**.
 
     ![Captura de pantalla que muestra la selección de Nuevo conector.](.\media\on-premises-sql-connector-configure\sql-1.png)

 1. En la página **Propiedades**, rellene los cuadros con los valores especificados en la tabla que sigue a la imagen y seleccione **Siguiente**.
 
     ![Captura de pantalla que muestra la especificación de propiedades.](.\media\tutorial-ecma-sql-connector\conn-1.png)

     |Propiedad|Value|
     |-----|-----|
     |Nombre|SQL|
     |Temporizador de sincronización automática (minutos)|120|
     |Token secreto|Escriba aquí su propia clave. Debe tener un mínimo de 12 caracteres.|
       |DLL de extensión|Para un conector SQL genérico, seleccione **Microsoft.IAM.Connector.GenericSql.dll**.|
 1. En la página **Conectividad**, rellene los cuadros con los valores especificados en la tabla que sigue a la imagen y seleccione **Siguiente**.
 
     ![Captura de pantalla que muestra la página Conectividad.](.\media\tutorial-ecma-sql-connector\conn-2.png)

     |Propiedad|Value|
     |-----|-----|
     |Archivo DSN|Vaya al archivo creado al principio del tutorial en "Creación del archivo de conexión DNS".|
     |Nombre de usuario|contoso\administrator|
     |Contraseña|Escriba la contraseña del administrador.|
 1. En la página **Esquema 1**, rellene los cuadros con los valores especificados en la tabla que sigue a la imagen y seleccione **Siguiente**.
 
     ![Captura de pantalla que muestra la página Esquema 1.](.\media\tutorial-ecma-sql-connector\conn-3.png)

     |Propiedad|Value|
     |-----|-----|
     |Método de detección de tipo de objeto|Fixed Value|
     |Lista de valores fijos/Tabla/Vista/SP|Usuario|
 1. En la página **Esquema 2**, rellene los cuadros con los valores especificados en la tabla que sigue a la imagen y seleccione **Siguiente**.
 
     ![Captura de pantalla que muestra la página Esquema 2.](.\media\tutorial-ecma-sql-connector\conn-4.png)

     |Propiedad|Value|
     |-----|-----|
     |User:Attribute Detection|Tabla|
     |User:Table/View/SP|Employees|
 1. En la página **Esquema 3**, rellene los cuadros con los valores especificados en la tabla que sigue a la imagen y seleccione **Siguiente**.
 
     ![Captura de pantalla que muestra la página Esquema 3.](.\media\tutorial-ecma-sql-connector\conn-5.png)

     |Propiedad|Descripción|
     |-----|-----|
     |Seleccionar un delimitador para User|User:ContosoLogin|
     |Seleccionar atributo DN para User|AzureID|
 1. En la página **Esquema 4**, deje los valores predeterminados y seleccione **Siguiente**.
 
     ![Captura de pantalla que muestra la página Esquema 4.](.\media\tutorial-ecma-sql-connector\conn-6.png)
 1. En la página **Global**, rellene los cuadros y seleccione **Siguiente**. Use la tabla que sigue a la imagen como guía sobre los cuadros individuales.
 
     ![Captura de pantalla que muestra la página Global.](.\media\tutorial-ecma-sql-connector\conn-7.png)

     |Propiedad|Descripción|
     |-----|-----|
     |Formato de fecha y hora del origen de datos|aaaa/MM/dd HH:mm:ss|
 1. En la página **Particiones**, seleccione **Siguiente**.
 
     ![Captura de pantalla que muestra la página Particiones.](.\media\tutorial-ecma-sql-connector\conn-8.png)

 1. En la página **Perfiles de ejecución**, mantenga activada la casilla **Exportar**. Active la casilla **Importación completa** y seleccione **Siguiente**.
 
     ![Captura de pantalla que muestra la página Perfiles de ejecución.](.\media\tutorial-ecma-sql-connector\conn-9.png)

 1. En la página **Exportar**, rellene los cuadros y seleccione **Siguiente**. Use la tabla que sigue a la imagen como guía sobre los cuadros individuales. 
 
     ![Captura de pantalla que muestra la página Exportar.](.\media\tutorial-ecma-sql-connector\conn-10.png)

     |Propiedad|Descripción|
     |-----|-----|
     |Método de operación|Tabla|
     |Tabla/Vista/SP|Employees|
 
 1. En la página **Importación completa**, rellene los cuadros y seleccione **Siguiente**. Use la tabla que sigue a la imagen como guía sobre los cuadros individuales. 
 
     ![Captura de pantalla que muestra la página Importación completa.](.\media\tutorial-ecma-sql-connector\conn-11.png)

     |Propiedad|Descripción|
     |-----|-----|
     |Método de operación|Tabla|
     |Tabla/Vista/SP|Employees|
 
 1. En la página **Tipos de objeto**, rellene los cuadros y seleccione **Siguiente**. Use la tabla que sigue a la imagen como guía sobre los cuadros individuales.

      - **Delimitador**: este atributo debe ser único en el sistema de destino. El servicio de aprovisionamiento de Azure AD consulta al host ECMA mediante este atributo después del ciclo inicial. Este valor de delimitador debe ser igual que el valor de delimitador del esquema 3.
      - **Atributo de consulta**: lo usa el host ECMA para consultar la caché en memoria. Este atributo debe ser único.
      - **DN**: la opción **Generado automáticamente** debe seleccionarse en la mayoría de los casos. Si no está seleccionada, asegúrese de que el atributo DN esté asignado a un atributo de Azure AD que almacene el DN en este formato: CN = anchorValue, Object = objectType.

        ![Captura de pantalla que muestra la página Tipos de objeto.](.\media\tutorial-ecma-sql-connector\conn-12.png)

      |Propiedad|Descripción|
      |-----|-----|
      |Objeto de destino|Usuario|
      |Delimitador|ContosoLogin|
      |Atributo de consulta|AzureID|
      |DN|AzureID|
      |Generado automáticamente|Activada|
      

 1. En la página **Seleccionar atributos**, agregue todos los atributos de la lista desplegable y seleccione **Siguiente**. 
 
     ![Captura de pantalla que muestra la página Seleccionar atributos.](.\media\tutorial-ecma-sql-connector\conn-13.png)

      La lista desplegable **Atributo** muestra cualquier atributo que se haya detectado en el sistema de destino y que *no* se haya elegido en la página **Seleccionar atributos** anterior. 
 1. En la página **Desaprovisionando**, en **Deshabilitar flujo**, seleccione **Eliminar**. Seleccione **Finalizar**.
 
     ![Captura de pantalla que muestra la página Desaprovisionando.](.\media\tutorial-ecma-sql-connector\conn-14.png)

## <a name="ensure-ecma2host-service-is-running"></a>Comprobación de que el servicio ECMA2Host se está ejecutando
1. En el servidor en el que se ejecuta el host del conector ECMA de Azure AD, seleccione **Iniciar**.
1. Escriba **run** y luego **services.msc** en el cuadro.
1. En la lista **Servicios**, asegúrese de que **Microsoft ECMA2Host** esté presente y en ejecución. Si no es así, seleccione **Iniciar**.

   ![Captura de pantalla que muestra que el servicio se está ejecutando.](.\media\on-premises-ecma-configure\configure-2.png)

## <a name="add-an-enterprise-application"></a>Adición de una aplicación empresarial
1. Inicie sesión en Azure Portal como administrador de la aplicación.
1. En el portal, vaya a **Azure Active Directory** > **Aplicaciones empresariales**.
1. Seleccione **Nueva aplicación**.

   ![Captura de pantalla que muestra la incorporación de una nueva aplicación.](.\media\on-premises-ecma-configure\configure-4.png)
1. Busque en la galería **Aplicación ECMA local** y seleccione **Crear**.

## <a name="configure-the-application-and-test"></a>Configuración de la aplicación y prueba
1. Una vez que se haya creado, seleccione la página **Aprovisionamiento**.
1. Seleccione **Comenzar**.

     ![Captura de pantalla que muestra Comenzar.](.\media\on-premises-ecma-configure\configure-1.png)
1. En la página **Aprovisionamiento**, cambie el modo a **Automático**.

     ![Captura de pantalla que muestra el cambio de modo a Automático.](.\media\on-premises-ecma-configure\configure-7.png)
1. En la sección **Conectividad local**, seleccione el agente que acaba de implementar y luego **Asignar agentes**.
     >[!NOTE]
     >Después de agregar el agente, espere 10 minutos a que se complete el registro. La prueba de conectividad no funciona hasta que se completa el registro.
     >
     >Como alternativa, puede reiniciar el agente de aprovisionamiento en el servidor para forzar que el registro del agente se complete. Vaya al servidor, busque **servicios** en la barra de búsqueda de Windows, identifique el **servicio del agente de aprovisionamiento de Azure AD Connect**, haga clic con el botón derecho en el servicio y reinicie.
   
     ![Captura de pantalla que muestra el reinicio de un agente.](.\media\on-premises-ecma-configure\configure-8.png)
1.  Pasados 10 minutos, en la sección **Credenciales de administración**, escriba la siguiente dirección URL. Reemplace la parte `connectorName` por el nombre del conector en el host ECMA. También puede reemplazar `localhost` por el nombre de host.

     |Propiedad|Valor|
     |-----|-----|
     |URL de inquilino|https://localhost:8585/ecma2host_connectorName/scim|

1. Escriba el valor de **Token secreto** que ha definido al crear el conector.
1. Seleccione **Probar conexión** y espere un minuto.

     ![Captura de pantalla que muestra la asignación de un agente.](.\media\on-premises-ecma-configure\configure-5.png)
1. Una vez que se supere la prueba de conexión, seleccione **Guardar**.</br>

     ![Captura de pantalla que muestra las pruebas de un agente.](.\media\on-premises-ecma-configure\configure-9.png)

## <a name="assign-users-to-an-application"></a>Asignación de usuarios a una aplicación
Ahora que el host del conector ECMA de Azure AD ya se comunica con Azure AD, puede pasar a configurar quién está dentro del ámbito de aprovisionamiento. 

1. En Azure Portal, seleccione **Aplicaciones empresariales**.
1. Seleccione la aplicación **Aprovisionamiento local**.
1. A la izquierda, en **Administrar**, seleccione **Usuarios y grupos**.
1. Seleccione **Agregar usuario o grupo**.

     ![Captura de pantalla que muestra la incorporación de un usuario.](.\media\tutorial-ecma-sql-connector\app-2.png)
1. En **Usuarios**, seleccione **Ninguno seleccionado**.

     ![Captura de pantalla que muestra la opción Ninguno seleccionado.](.\media\tutorial-ecma-sql-connector\app-3.png)
1. Seleccione usuarios de la derecha y luego el botón **Seleccionar**.</br>

     ![Captura de pantalla que muestra la selección de usuarios.](.\media\tutorial-ecma-sql-connector\app-4.png)
1. Ahora seleccione **Asignar**.

     ![Captura de pantalla que muestra la asignación de usuarios.](.\media\tutorial-ecma-sql-connector\app-5.png)

## <a name="configure-attribute-mappings"></a>configuración de las asignaciones de atributos
Ahora debe asignar atributos entre la aplicación local y el servidor SQL.

#### <a name="configure-attribute-mapping"></a>Configuración de la asignación de atributos
 1. En el portal de Azure AD, en **Aplicaciones empresariales**, seleccione la página **Aprovisionamiento**.
 1. Seleccione **Comenzar**.
 1. Expanda **Asignaciones** y seleccione **Aprovisionar usuarios de Azure Active Directory**.
 
    ![Captura de pantalla que muestra el aprovisionamiento de un usuario.](.\media\on-premises-ecma-configure\configure-10.png)
 1. Seleccione **Agregar nueva asignación**.
 
     ![Captura de pantalla que muestra Agregar nueva asignación.](.\media\on-premises-ecma-configure\configure-11.png)
 1. Especifique los atributos de origen y destino y agregue todas las asignaciones de la tabla siguiente.

      |Tipo de asignación|Atributo de origen|Atributo de destino|
      |-----|-----|-----|
      |Directo|userPrincipalName|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:ContosoLogin|
      |Directo|objectID|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:AzureID|
      |Directo|mail|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:Email|
      |Directo|givenName|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:FirstName|
      |Directo|surName|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:LastName|
      |Directo|mailNickname|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:textID|

 1. Seleccione **Guardar**.
 
     ![Captura de pantalla que muestra el guardado de la asignación.](.\media\tutorial-ecma-sql-connector\app-6.png)

## <a name="test-provisioning"></a>Prueba del aprovisionamiento
Ahora que los atributos están asignados, puede probar el aprovisionamiento a petición con uno de los usuarios.
 
 1. En Azure Portal, seleccione **Aplicaciones empresariales**.
 1. Seleccione la aplicación **Aprovisionamiento local**.
 1. A la izquierda, seleccione **Aprovisionamiento**.
 1. Seleccione **Aprovisionamiento a petición**.
 1. Busque alguno de los usuarios de prueba y seleccione **Aprovisionar**.
 
     ![Captura de pantalla que muestra las pruebas de aprovisionamiento.](.\media\on-premises-ecma-configure\configure-13.png)

## <a name="start-provisioning-users"></a>Inicio del aprovisionamiento de usuarios
 1. Una vez que el aprovisionamiento a petición se realice correctamente, vuelva a la página de configuración del aprovisionamiento. Asegúrese de que el ámbito esté establecido solo en los usuarios y grupos asignados, **active el aprovisionamiento** y seleccione **Guardar**.
 
    ![Captura de pantalla que muestra el inicio del aprovisionamiento.](.\media\on-premises-ecma-configure\configure-14.png)
 1. Espere varios minutos para que se inicie el aprovisionamiento. Puede tardar hasta 40 minutos. Una vez completado el trabajo de aprovisionamiento, como se explica en la sección siguiente, puede **desactivar el estado de aprovisionamiento** y seleccionar **Guardar**. Esta acción evita que el servicio de aprovisionamiento se ejecute en el futuro.

## <a name="check-that-users-were-successfully-provisioned"></a>Comprobación de aprovisionamiento correcto de los usuarios
Después de esperar, compruebe la base de datos SQL para asegurarse de que los usuarios se están aprovisionando.

 ![Captura de pantalla que muestra la comprobación de que los usuarios se están aprovisionando.](.\media\on-premises-ecma-configure\configure-15.png)

## <a name="appendix-a"></a>Apéndice A
Use el siguiente script SQL para crear la base de datos de ejemplo.

```SQL
---Creating the Database---------
Create Database CONTOSO
Go
-------Using the Database-----------
Use [CONTOSO]
Go
-------------------------------------

/****** Object:  Table [dbo].[Employees]    Script Date: 1/6/2020 7:18:19 PM ******/
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

CREATE TABLE [dbo].[Employees](
    [ContosoLogin] [nvarchar](128) NULL,
    [FirstName] [nvarchar](50) NOT NULL,
    [LastName] [nvarchar](50) NOT NULL,
    [Email] [nvarchar](128) NULL,
    [InternalGUID] [uniqueidentifier] NULL,
    [AzureID] [uniqueidentifier] NULL,
    [textID] [nvarchar](128) NULL
) ON [PRIMARY]
GO

ALTER TABLE [dbo].[Employees] ADD  CONSTRAINT [DF_Employees_InternalGUID]  DEFAULT (newid()) FOR [InternalGUID]
GO

```




## <a name="next-steps"></a>Pasos siguientes

- [Solucione los problemas de aprovisionamiento local de aplicaciones](on-premises-ecma-troubleshoot.md)
- [Revise las limitaciones conocidas](known-issues.md)
- [Requisitos previos del aprovisionamiento local](on-premises-ecma-prerequisites.md)
- [Revise los requisitos previos del aprovisionamiento local](on-premises-ecma-prerequisites.md)
