---
ms.openlocfilehash: e1afd9219a1f6943fbee65a88538858fdce6aacf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090946"
---
En este documento se describen los pasos que hay que realizar para aprovisionar y desaprovisionar automáticamente usuarios de Azure Active Directory (Azure AD) en un directorio LDAP. El documento se centra en AD LDS, pero puede aprovisionar en cualquiera de los directorios LDAP admitidos que se mencionan a continuación. No se admite el aprovisionamiento de usuarios en Active Directory Domain Services a través de esta solución. 
 
Para obtener información importante acerca de lo que hace este servicio, cómo funciona y ver preguntas frecuentes al respecto, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](../articles/active-directory/app-provisioning/user-provisioning.md).

## <a name="prerequisites-for-provisioning-users-into-an-ldap-directory"></a>Requisitos previos para aprovisionar usuarios en un directorio LDAP

>[!IMPORTANT]
> La versión preliminar de aprovisionamiento local se encuentra actualmente en una versión preliminar solo por invitación. Para solicitar acceso a la capacidad, use el [formulario de solicitud de acceso](https://aka.ms/onpremprovisioningpublicpreviewaccess). La versión preliminar estará a disposición de más clientes y conectores durante los próximos meses, cuando se prepare la disponibilidad general. El aprovisionamiento de usuarios en Active Directory Domain Services no se admite a través de esta versión preliminar. 


### <a name="on-premises-prerequisites"></a>Requisitos previos locales

 - Un sistema de destino, como Active Directory Lightweight Services (AD LDS), donde los usuarios se pueden crear, actualizar y eliminar. Esta instancia de AD LDS no debe usarse para aprovisionar usuarios en Azure AD porque puede crear un bucle con Azure AD Connect. 
 - Un equipo con Windows Server 2016 o posterior con una dirección TCP/IP accesible desde Internet, conectividad con el sistema de destino, y conectividad saliente a login.microsoftonline.com. Un ejemplo es una máquina virtual de Windows Server 2016 hospedada en IaaS de Azure o detrás de un proxy. El servidor debe tener al menos 3 GB de RAM.
 - Un equipo con .NET Framework 4.7.1.
 - Opcional: aunque no es necesario, se recomienda descargar [Microsoft Edge para Windows Server](https://www.microsoft.com/en-us/edge?r=1) y usarlo en lugar de Internet Explorer.

En función de las opciones que seleccione, es posible que algunas de las pantallas del asistente no estén disponibles y que la información sea ligeramente diferente. Para los fines de esta configuración, se usa el tipo de objeto de usuario. Use la siguiente información como orientación para la configuración. 

#### <a name="supported-systems"></a>Sistemas compatibles
* OpenLDAP
* Microsoft Active Directory Lightweight Directory Services
* 389 Directory Server
* Apache Directory Server
* IBM Tivoli DS
* Isode Directory
* NetIQ eDirectory
* Novell eDirectory
* Open DJ
* Open DS
* Open LDAP (openldap.org)
* Oracle (anteriormente Sun) Directory Server Enterprise Edition
* RadiantOne Virtual Directory Server (VDS)
* Sun One Directory Server



### <a name="cloud-requirements"></a>Requisitos de la nube

 - Un inquilino de Azure AD con Azure AD Premium P1 o Premium P2 (o EMS E3 o E5). 
 
    [!INCLUDE [active-directory-p1-license.md](active-directory-p1-license.md)]
 - El rol Administrador híbrido para configurar el agente de aprovisionamiento y los roles administrador de aplicaciones o administrador de la nube para configurar el aprovisionamiento en Azure Portal.

### <a name="more-recommendations-and-limitations"></a>Más limitaciones y recomendaciones
Los siguientes puntos de viñeta son más recomendaciones y limitaciones.
- No se recomienda usar el mismo agente para la sincronización en la nube y el aprovisionamiento de aplicaciones local.  Microsoft recomienda usar un agente independiente para la sincronización en la nube y otro para el aprovisionamiento de aplicaciones local.
- Actualmente, para AD LDS, los usuarios no se pueden aprovisionar con contraseñas.  Por lo tanto, deberá deshabilitar la directiva de contraseñas para AD LDS o aprovisionar los usuarios en estado deshabilitado.
- No se admite el aprovisionamiento de Azure Active Directory a Active Directory Domain Services.
- No se admite el aprovisionamiento de usuarios de LDAP a Azure AD.

## <a name="prepare-the-ldap-directory"></a>Preparación del directorio LDAP
Se proporciona la siguiente información para ayudar a crear un entorno AD LDS de prueba.  Esta configuración usa PowerShell y ADAMInstall.exe con un archivo de respuestas.  En este documento no se trata información detallada sobre AD LDS.  Para obtener mas información, consulte [Active Directory Lightweight Directory Services](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)). 

Si ya ha configurado AD LDS en un entorno de prueba, puede omitir las secciones siguientes y pasar a la sección de instalación del conector de host de ECMA.

### <a name="create-an-ssl-certificate-a-test-directory-and-install-ad-lds"></a>Cree un certificado SSL, un directorio de prueba e instale AD LDS.
Use el script de PowerShell del [Apéndice A.](#appendix-a---install-ad-lds-powershell-script) El script realiza las siguientes acciones:
  - Crea un certificado autofirmado que se usará el conector LDAP
  - Crea un directorio para el registro de instalación de características
  - Exporta el certificado del almacén personal al directorio
  - Importa el certificado a la raíz de confianza de la máquina local
  - Instala el rol AD LDS en nuestra máquina virtual 

En la máquina virtual de Windows Server, se usa para probar que el conector LDAP ejecute el script mediante Windows PowerShell con privilegios administrativos.  

### <a name="create-an-instance-of-ad-lds"></a>Creación de una instancia de AD LDS
Ahora que el rol se ha instalado, debe crear una instancia de AD LDS.  Para crear una instancia, puede usar el archivo de respuesta que se proporciona a continuación.  Este archivo instalará la instancia de forma silenciosa sin usar la interfaz de usuario.

Copie el contenido del [Apéndice B](#appendix-b---answer-file) en el Bloc de notas y guárdelo como **answer.txt** en **"C:\Windows\ADAM"** .

Ahora abra un símbolo del sistema con privilegios administrativos y ejecute el siguiente ejecutable:

```
C:\Windows\ADAM> ADAMInstall.exe /answer:answer.txt
```

### <a name="create-containers-and-a-service-account-for-ad-lds"></a>Cree contenedores y una cuenta de servicio para AD LDS.
Use el script de PowerShell del [Apéndice A.](#appendix-c---populate-ad-lds-powershell-script) El script realiza las siguientes acciones:
  - Crea un contenedor para la cuenta de servicio que se usará con el conector LDAP
  - Crea un contenedor para los usuarios en la nube.  Este contenedor es donde se aprovisionarán los usuarios.
  - Crea la cuenta de servicio en AD LDS
  - Habilita la cuenta de servicio
  - Agrega la cuenta de servicio al rol de administradores de AD LDS

En la máquina virtual de Windows Server, se usa para probar que el conector LDAP ejecute el script mediante Windows PowerShell con privilegios administrativos.  

## <a name="grant-the-network-service-read-permissions-to-the-ssl-cert"></a>Concesión de permisos de lectura de SERVICIO DE RED al certificado SSL
Para permitir que SSL funcione, debe conceder permisos de lectura de SERVICIO DE RED a nuestro certificado recién creado.  Para conceder los permisos, siga estos pasos.

 1. Vaya a **C:\Program Data\Microsoft\Crypto\Keys**.
 2. Haga clic con el botón derecho en el archivo del sistema que se encuentra aquí.  Será un GUID.  Este contenedor almacena nuestro certificado.
    a. Selección de propiedades.
    b. Seleccione la pestaña **Seguridad** en la parte superior. c. Seleccione **Editar**.
    d. Haga clic en **Agregar**.
    e. En el cuadro, escriba **Servicio de red** y seleccione **Comprobar nombres**.
    f. Seleccione **SERVICIO DE RED** en la lista y haga clic en **Aceptar**.
    g. Haga clic en **Aceptar**.
    h. Asegúrese de que la cuenta de servicio de red tiene permisos de lectura y ejecución y haga clic en **Aplicar** y, posteriormente, en **Aceptar**.

## <a name="verify-ssl-connectivity-with-ad-lds"></a>Comprobación de la conectividad SSL con AD LDS
Ahora que hemos configurado el certificado y hemos concedidos permisos a la cuenta de servicio de red, pruebe la conectividad para comprobar que funciona.
 1. Abra el Administrador del servidor y seleccione AD LDS a la izquierda
 2. Haga clic con el botón derecho en la instancia de AD LDS y seleccione ldp.exe en el elemento emergente.
   [![Ubicación de la herramienta ldp](media/active-directory-app-provisioning-ldap/ldp-1.png)](media/active-directory-app-provisioning-ldap/ldp-1.png#lightbox)</br>
 3. En la parte superior de ldp.exe, seleccione **Conexión** y **Conectar**.
 4. Escriba la siguiente información y haga clic en **Aceptar**.
    - Servidor: APP3
    - Puerto: 636
    - Coloque una marca de verificación en la casilla de SSL [![Configuración de la conexión de lpd](media/active-directory-app-provisioning-ldap/ldp-2.png)</br>
 5.  Debería ver una respuesta similar a la captura de pantalla siguiente.
   ![Configuración de la conexión de lpd correcta](media/active-directory-app-provisioning-ldap/ldp-3.png)](media/active-directory-app-provisioning-ldap/ldp-3.png#lightbox)</br>
 6.  En la parte superior, en **Conexión** seleccione **Enlazar**.
 7. Deje el valor predeterminado y haga clic en **Aceptar**.
   [![Enlace de ldp](media/active-directory-app-provisioning-ldap/ldp-4.png)](media/active-directory-app-provisioning-ldap/ldp-4.png#lightbox)</br>
 8. Ahora debería enlazarse correctamente a la instancia.
   [![Enlace de ldp correcto](media/active-directory-app-provisioning-ldap/ldp-5.png)](media/active-directory-app-provisioning-ldap/ldp-5.png#lightbox)</br>



## <a name="download-install-and-configure-the-azure-ad-connect-provisioning-agent-package"></a>Descargar, instalar y configurar el paquete del agente de aprovisionamiento de Azure AD Connect

 1. Inicie sesión en Azure Portal.
 2. Vaya a **Aplicaciones empresariales** > **Agregar una nueva aplicación**.
 3. Busque la **aplicación ECMA local** y agréguela a la imagen del inquilino.
 4. Seleccione la **aplicación ECMA local** que agregó.
 5. En **Introducción**, en **3. Aprovisionar cuentas de usuario**, seleccione **Comenzar**.
 6. En la parte superior, en la lista desplegable, cambie el aprovisionamiento a **automático**.  Esta acción mostrará la **conectividad local** a continuación.
 7. En **Conectividad local**, descargue el agente de aprovisionamiento.
 8. Ejecute el instalador de aprovisionamiento de Azure AD Connect, **AADConnectProvisioningAgentSetup.msi**.
 9. En la pantalla **Paquete del agente de aprovisionamiento de Microsoft Azure AD Connect**, acepte los términos de la licencia y seleccione **Instalar**.
     [![Pantalla del paquete del agente de aprovisionamiento de Microsoft Azure AD Connect.](media/active-directory-app-provisioning-sql/install-1.png)](media/active-directory-app-provisioning-sql/install-1.png#lightbox)</br>
 10. Una vez finalizada esta operación, se inicia el Asistente para configuración. Seleccione **Siguiente**.
     [![Captura de pantalla que muestra la pantalla inicial.](media/active-directory-app-provisioning-sql/install-2.png)](media/active-directory-app-provisioning-sql/install-2.png#lightbox)</br>
 11. En la pantalla **Seleccionar extensión**, seleccione **Aprovisionamiento de aplicaciones locales (de Azure AD a la aplicación)** . Seleccione **Siguiente**.
     [![Captura de pantalla que muestra la selección de extensión.](media/active-directory-app-provisioning-sql/install-3.png)](media/active-directory-app-provisioning-sql/install-3.png#lightbox)</br>
 12. Use la cuenta de administrador global para iniciar sesión en Azure AD.
     [![Captura de pantalla que muestra el inicio de sesión de Azure.](media/active-directory-app-provisioning-sql/install-4.png)](media/active-directory-app-provisioning-sql/install-4.png#lightbox)</br>
 13. En la pantalla **Configuración del agente**, seleccione **Confirmar**.
     [![Captura de pantalla que muestra la confirmación de la instalación.](media/active-directory-app-provisioning-sql/install-5.png)](media/active-directory-app-provisioning-sql/install-5.png#lightbox)</br>
 14. Una vez completada la instalación, debería ver un mensaje en la parte inferior del asistente. Seleccione **Salir**.
     [![Captura de pantalla que muestra la finalización.](media/active-directory-app-provisioning-sql/install-6.png)](media/active-directory-app-provisioning-sql/install-6.png#lightbox)</br>
 15. Vuelva a la página Azure Portal en la **aplicación ECMA local** y vuelva a **Editar aprovisionamiento**.
 16. En la página **Aprovisionamiento**, cambie el modo a **Automático**.
     [![Captura de pantalla que muestra el cambio de modo a Automático.](.\media\active-directory-app-provisioning-sql\configure-7.png)](.\media\active-directory-app-provisioning-sql\configure-7.png#lightbox)</br>
 17. En la sección **Conectividad local**, seleccione el agente que acaba de implementar y luego **Asignar agentes**.
     [![Captura de pantalla que muestra el reinicio de un agente.](.\media\active-directory-app-provisioning-ldap\assign-1.png)](.\media\active-directory-app-provisioning-ldap\assign-1.png#lightbox)</br>
     >[!NOTE]
     >Después de agregar el agente, espere 10 minutos a que se complete el registro. La prueba de conectividad no funciona hasta que se completa el registro.
     >
     >Como alternativa, puede reiniciar el agente de aprovisionamiento en el servidor para forzar que el registro del agente se complete. Vaya al servidor, busque **servicios** en la barra de búsqueda de Windows, identifique el **servicio del agente de aprovisionamiento de Azure AD Connect**, haga clic con el botón derecho en el servicio y reinicie.

 ## <a name="configure-the-azure-ad-ecma-connector-host-certificate"></a>Configuración del certificado de host del conector ECMA de Azure AD
 1. En el escritorio, seleccione el acceso directo ECMA.
 2. Una vez que se inicie la configuración del host del conector ECMA, deje el puerto predeterminado **8585** y seleccione **Generar** para generar un certificado. El certificado autogenerado se firma automáticamente como parte de la raíz de confianza. SAN coincide con el nombre de host.
     [![Captura de pantalla que muestra la configuración de los valores.](.\media\active-directory-app-provisioning-sql\configure-1.png)](.\media\active-directory-app-provisioning-sql\configure-1.png#lightbox)
 3. Seleccione **Guardar**.


## <a name="configure-a-generic-ldap-connector"></a>Configuración de un conector LDAP genérico
 1. Seleccione el acceso directo Host del conector ECMA en el escritorio.
 2. Seleccione **Nuevo conector**.
     [![Captura de pantalla que muestra la selección de Nuevo conector.](.\media\active-directory-app-provisioning-sql\sql-3.png)](.\media\active-directory-app-provisioning-sql\sql-3.png#lightbox)</br>
 3. En la página **Propiedades**, rellene los cuadros con los valores especificados en la tabla que sigue a la imagen y seleccione **Siguiente**.
     [![Captura de pantalla que muestra la especificación de propiedades.](.\media\active-directory-app-provisioning-ldap\create-1.png)](.\media\active-directory-app-provisioning-ldap\create-1.png#lightbox)

     |Propiedad|Valor|
     |-----|-----|
     |Nombre|LDAP|
     |Temporizador de sincronización automática (minutos)|120|
     |Token secreto|Escriba aquí su propia clave. Debe tener un mínimo de 12 caracteres.|
     |DLL de extensión|Para un conector SQL genérico, seleccione **Microsoft.IAM.Connector.GenericLdap.dll**.|
4. En la página **Conectividad**, rellene los cuadros con los valores especificados en la tabla que sigue a la imagen y seleccione **Siguiente**.
     [![Captura de pantalla que muestra la página Conectividad.](.\media\active-directory-app-provisioning-ldap\create-2.png)](.\media\active-directory-app-provisioning-ldap\create-2.png#lightbox)</br>
     
     |Propiedad|Valor|
     |-----|-----|
     |Host|APP3|
     |Puerto|636|
     |Tiempo de espera de la conexión|180|
     |Enlace|SSL|
     |Nombre de usuario|CN=svcAccount,CN=ServiceAccounts,CN=App,DC=contoso,DC=lab|
     |Contraseña|La contraseña del nombre de usuario especificado|

     >[!NOTE]
     >Si experimenta un problema al intentar conectarse, asegúrese de que la cuenta de servicio de AD LDS está habilitada. 
     
 5. En la página **Global**, seleccione **Siguiente**.
 6. En la página **Particiones**, mantenga el valor predeterminado y seleccione **Siguiente.**
 7. En la página **Perfiles de ejecución**, mantenga activada la casilla **Exportar**. Active la casilla **Importación completa** y seleccione **Siguiente**.
     [![Captura de pantalla que muestra la página Perfiles de ejecución.](.\media\active-directory-app-provisioning-ldap\create-3.png)](.\media\active-directory-app-provisioning-ldap\create-3.png#lightbox)</br>
     
     |Propiedad|Descripción|
     |-----|-----|
     |Exportación|Perfil de ejecución que exportará datos a SQL. Este perfil de ejecución es obligatorio.|
     |Importación completa|Perfil de ejecución que importará todos los datos de orígenes SQL especificados anteriormente.|
     |Importación diferencial|Perfil de ejecución que importará solo los cambios de SQL desde la última importación completa o diferencial.|
 12. En la página **Exportar**, deje los valores predeterminados y haga clic en **Siguiente.** 
 13. En la página **Importación completa**, deje los valores predeterminados y haga clic en **Siguiente.** 
 14. En la página **Tipos de objeto**, rellene los cuadros y seleccione **Siguiente**. Use la tabla que sigue a la imagen como guía sobre los cuadros individuales.
      - **Objeto de destino**: este objeto es el objeto de destino en el directorio LDAP.
      - **Delimitador**: este atributo debe ser único en el sistema de destino. El servicio de aprovisionamiento de Azure AD consulta al host ECMA mediante este atributo después del ciclo inicial. Este valor de delimitador debe ser igual que el valor de delimitador del esquema 3.
      - **Atributo de consulta**: lo usa el host ECMA para consultar la caché en memoria. Este atributo debe ser único.
      - **DN**: el valor distinguishedName del objeto de destino.
     [![Captura de pantalla que muestra la página Tipos de objeto.](.\media\active-directory-app-provisioning-ldap\create-4.png)](.\media\active-directory-app-provisioning-ldap\create-4.png#lightbox)</br>
     
     |Propiedad|Descripción|
     |-----|-----|
     |Objeto de destino|Usuario|
     |Delimitador|objectGUID|
     |Atributo de consulta|_distingusishedName|
     |DN|dn|
     |Generado automáticamente|unchecked|      
 15. El host ECMA detecta los atributos que admite el sistema de destino. Puede elegir cuál de esos atributos desea exponer a Azure AD. Posteriormente, estos atributos se pueden configurar en Azure Portal para el aprovisionamiento. En la página **Seleccionar atributos**, agregue todos los atributos en la lista desplegable y seleccione **Siguiente**.
     [![Captura de pantalla que muestra la página Seleccionar atributos.](.\media\active-directory-app-provisioning-ldap\create-5.png)](.\media\active-directory-app-provisioning-ldap\create-5.png#lightbox)</br>
      La lista desplegable **Atributo** muestra cualquier atributo que se haya detectado en el sistema de destino y que *no* se haya elegido en la página **Seleccionar atributos** anterior. 
 
 16. En la página **Desaprovisionando**, en **Deshabilitar flujo**, seleccione **Eliminar**. Los atributos seleccionados en la página anterior no estarán disponibles para seleccionar en la página Desaprovisionando. Seleccione **Finalizar**.

## <a name="ensure-ecma2host-service-is-running"></a>Comprobación de que el servicio ECMA2Host se está ejecutando
 1. En el servidor en el que se ejecuta el host del conector ECMA de Azure AD, seleccione **Iniciar**.
 2. Escriba **run** y luego **services.msc** en el cuadro.
 3. En la lista **Servicios**, asegúrese de que **Microsoft ECMA2Host** esté presente y en ejecución. Si no es así, seleccione **Iniciar**.
     [![Captura de pantalla que muestra que el servicio se está ejecutando.](.\media\active-directory-app-provisioning-sql\configure-2.png)](.\media\active-directory-app-provisioning-sql\configure-2.png#lightbox)



## <a name="test-the-application-connection"></a>Prueba de la conexión con la aplicación
 1. Inicie sesión en Azure Portal.
 2. Vaya a **Aplicaciones empresariales** y seleccione la **aplicación ECMA local**.
 3. Vaya a **Editar aprovisionamiento**.
 4. Pasados 10 minutos, en la sección **Credenciales de administración**, escriba la siguiente dirección URL. Reemplace la parte `connectorName` por el nombre del conector en el host ECMA. También puede reemplazar `localhost` por el nombre de host.

    |Propiedad|Valor|
    |-----|-----|
    |URL de inquilino|https://localhost:8585/ecma2host_connectorName/scim|
 
 5. Escriba el valor de **Token secreto** que ha definido al crear el conector.
 6. Seleccione **Probar conexión** y espere un minuto.
     [![Captura de pantalla que muestra la asignación de un agente.](.\media\active-directory-app-provisioning-ldap\test-1.png)](.\media\active-directory-app-provisioning-ldap\test-1.png#lightbox)
 7. Una vez que se supere la prueba de conexión, seleccione **Guardar**.</br>
     [![Captura de pantalla que muestra las pruebas de un agente.](.\media\active-directory-app-provisioning-sql\configure-9.png)](.\media\active-directory-app-provisioning-sql\configure-9.png#lightbox)
## <a name="assign-users-to-an-application"></a>Asignación de usuarios a una aplicación
Ahora que el host del conector ECMA de Azure AD ya se comunica con Azure AD, puede pasar a configurar quién está dentro del ámbito de aprovisionamiento. 

 1. En Azure Portal, seleccione **Aplicaciones empresariales**.
 2. Seleccione la aplicación **Aprovisionamiento local**.
 3. A la izquierda, en **Administrar**, seleccione **Usuarios y grupos**.
 4. Seleccione **Agregar usuario o grupo**.
     [![Captura de pantalla que muestra la incorporación de un usuario.](.\media\active-directory-app-provisioning-sql\app-2.png)](.\media\active-directory-app-provisioning-sql\app-2.png#lightbox)
5. En **Usuarios**, seleccione **Ninguno seleccionado**.
     [![Captura de pantalla que muestra la opción Ninguno seleccionado.](.\media\active-directory-app-provisioning-sql\app-3.png)](.\media\active-directory-app-provisioning-sql\app-3.png#lightbox)
 6. Seleccione usuarios de la derecha y luego el botón **Seleccionar**.</br>
     [![Captura de pantalla que muestra la selección de usuarios.](.\media\active-directory-app-provisioning-sql\app-4.png)](.\media\active-directory-app-provisioning-sql\app-4.png#lightbox)
 7. Ahora seleccione **Asignar**.
     [![Captura de pantalla que muestra la asignación de usuarios.](.\media\active-directory-app-provisioning-sql\app-5.png)](.\media\active-directory-app-provisioning-sql\app-5.png#lightbox)


#### <a name="configure-attribute-mapping"></a>Configuración de la asignación de atributos
 1. En el portal de Azure AD, en **Aplicaciones empresariales**, seleccione la página **Aprovisionamiento**.
 2. Seleccione **Comenzar**.
 3. Expanda **Asignaciones** y seleccione **Aprovisionar usuarios de Azure Active Directory**.
 4. Seleccione **Agregar nueva asignación**.
 5. Especifique los atributos de origen y destino y agregue todas las asignaciones de la tabla siguiente.

     |Tipo de asignación|Atributo de origen|Atributo de destino|
     |-----|-----|-----|
     |Directo|userPrincipalName|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:userPrincipalName|
     |Expression|Join("", "CN=", Word([userPrincipalName], 1, "@"), ",CN=CloudUsers,CN=App,DC=Contoso,DC=lab")|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:-dn-|
     |Directo|isSoftDeleted|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:msDS-UserAccountDisabled|
     |Directo|DisplayName|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:displayName|
     |Directo|objectId|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:objectGUID|
  
     [![Captura de pantalla de asignaciones de correlación.](.\media\active-directory-app-provisioning-ldap\map-1.png)](.\media\active-directory-app-provisioning-ldap\map-1.png#lightbox)
 
 6. Seleccione **Guardar**.

## <a name="disable-the-local-password-policy"></a>Deshabilitación de la directiva de contraseña local
Actualmente, el conector LDAP aprovisiona a los usuarios con una contraseña en blanco.  Este aprovisionamiento no cumplirá la directiva de contraseñas local en nuestro servidor, por lo que vamos a deshabilitarla con fines de prueba.  Para deshabilitar la complejidad de la contraseña, siga estos pasos.

>[!IMPORTANT]
>Dado que la sincronización de contraseñas en ejecución no es una característica del aprovisionamiento LDAP local, Microsoft recomienda que AD LDS se utilice específicamente con aplicaciones federadas, cuando se usa junto con AD DS o al actualizar los usuarios existentes en una instancia de AD LDS.
 
 1. En el servidor, haga clic en **Inicio**, **Ejecutar** y, posteriormente, en **gpedit.msc**
 2. En el **editor de directivas de grupo locales**, vaya a Configuración del equipo > Directivas > Configuración de Windows > Configuración de seguridad > Directivas de cuenta > Directiva de contraseñas
 3. A la derecha, haga doble clic en **La contraseña debe cumplir los requisitos de complejidad** y seleccione **Deshabilitado.**
  [![Captura de pantalla de los requisitos de complejidad de la contraseña.](.\media\active-directory-app-provisioning-ldap\local-1.png)](.\media\active-directory-app-provisioning-ldap\local-1.png#lightbox)</br>
 5. Haga clic en **Aplicar** y en **Aceptar**
 6. Cierre el editor de directivas de grupo locales
 
## <a name="test-provisioning"></a>Prueba del aprovisionamiento
Ahora que los atributos están asignados, puede probar el aprovisionamiento a petición con uno de los usuarios.
 
 1. En Azure Portal, seleccione **Aplicaciones empresariales**.
 2. Seleccione la aplicación **Aprovisionamiento local**.
 3. A la izquierda, seleccione **Aprovisionamiento**.
 4. Seleccione **Aprovisionamiento a petición**.
 5. Busque alguno de los usuarios de prueba y seleccione **Aprovisionar**.
 [![Captura de pantalla de la prueba del aprovisionamiento a petición](.\media\active-directory-app-provisioning-ldap\test-2.png)](.\media\active-directory-app-provisioning-ldap\test-2.png#lightbox)</br>

## <a name="start-provisioning-users"></a>Inicio del aprovisionamiento de usuarios
 1. Una vez que el aprovisionamiento a petición se realice correctamente, vuelva a la página de configuración del aprovisionamiento. Asegúrese de que el ámbito esté establecido solo en los usuarios y grupos asignados, **active el aprovisionamiento** y seleccione **Guardar**.
 
 2. Espere varios minutos para que se inicie el aprovisionamiento. Puede tardar hasta 40 minutos. Una vez completado el trabajo de aprovisionamiento, como se explica en la sección siguiente, puede **desactivar el estado de aprovisionamiento** y seleccionar **Guardar**. Esta acción evita que el servicio de aprovisionamiento se ejecute en el futuro.

## <a name="check-that-users-were-successfully-provisioned"></a>Comprobación de aprovisionamiento correcto de los usuarios
Después de esperar, compruebe AD LDS para asegurarse de que los usuarios se aprovisionan.

 1. Abra el Administrador del servidor y seleccione AD LDS a la izquierda
 2. Haga clic con el botón derecho en la instancia de AD LDS y seleccione ldp.exe en el elemento emergente.
   [![Ubicación de la herramienta ldp](media/active-directory-app-provisioning-ldap/ldp-1.png)](media/active-directory-app-provisioning-ldap/ldp-1.png#lightbox)</br>
 3. En la parte superior de ldp.exe, seleccione **Conexión** y **Conectar**.
 4. Escriba la siguiente información y haga clic en **Aceptar**.
   - Servidor: APP3
   - Puerto: 636
   - Coloque una marca de verificación en la casilla de SSL [![Conexión de ldp para verificar usuarios](media/active-directory-app-provisioning-ldap/ldp-2.png)](media/active-directory-app-provisioning-ldap/ldp-2.png#lightbox)</br>
 5. En la parte superior, en **Conexión** seleccione **Enlazar**.
 6. Deje el valor predeterminado y haga clic en **Aceptar**.
 7. En la parte superior, seleccione **Ver** y **Árbol**
 8. En BaseDN, escriba **CN=App,DC=contoso,DC=lab** y haga clic en **Aceptar.**
 9. A la izquierda, expanda el DN y haga clic en **CN=CloudUsers,CN=App,DC=contoso,DC=lab**.  Debería ver a los usuarios en la nube.
  [![Enlace de ldp para usuarios](media/active-directory-app-provisioning-ldap/test-3.png)](media/active-directory-app-provisioning-ldap/test-3.png#lightbox)</br>

## <a name="appendix-a---install-ad-lds-powershell-script"></a>Apéndice A: Instalación del script de PowerShell de AD LDS
Script de PowerShell para automatizar la instalación de Active Directory Lightweight Directory Services.



```powershell
# Filename:    1_SetupADLDS.ps1
# Description: Creates a certificate that will be used for SSL and installs Active Directory Lighetweight Directory Services.
#
# DISCLAIMER:
# Copyright (c) Microsoft Corporation. All rights reserved. This 
# script is made available to you without any express, implied or 
# statutory warranty, not even the implied warranty of 
# merchantability or fitness for a particular purpose, or the 
# warranty of title or non-infringement. The entire risk of the 
# use or the results from the use of this script remains with you.
#
#
#
#
#Declare variables
$DNSName = 'APP3'
$CertLocation = 'cert:\LocalMachine\MY'
$logpath = "c:\" 
$dirname = "test"
$dirtype = "directory"
$featureLogPath = "c:\test\featurelog.txt" 

#Create a new self-signed certificate
New-SelfSignedCertificate -DnsName $DNSName -CertStoreLocation $CertLocation

#Create directory
New-Item -Path $logpath -Name $dirname -ItemType $dirtype

#Export the certifcate from the local machine personal store
Get-ChildItem -Path cert:\LocalMachine\my | Export-Certificate -FilePath c:\test\allcerts.sst -Type SST

#Import the certificate in to the trusted root
Import-Certificate -FilePath "C:\test\allcerts.sst" -CertStoreLocation cert:\LocalMachine\Root


#Install AD LDS
start-job -Name addFeature -ScriptBlock { 
Add-WindowsFeature -Name "ADLDS" -IncludeAllSubFeature -IncludeManagementTools 
 } 
Wait-Job -Name addFeature 
Get-WindowsFeature | Where installed >>$featureLogPath


 ```

## <a name="appendix-b---answer-file"></a>Apéndice B: Archivo de respuesta
Este archivo se usa para automatizar y crear una instancia de AD LDS.

>[!IMPORTANT]
> Este script usa el administrador local para la cuenta de servicio de AD LDS y tiene su contraseña codificada de forma segura en las respuestas.  Esta acción es **solo para pruebas** y nunca debe usarse en un entorno de producción.

```
 [ADAMInstall]
 InstallType=Unique
 InstanceName=AD-APP-LDAP
 LocalLDAPPortToListenOn=51300
 LocalSSLPortToListenOn=51301
 NewApplicationPartitionToCreate=CN=App,DC=contoso,DC=lab
 DataFilesPath=C:\Program Files\Microsoft ADAM\AD-APP-LDAP\data
 LogFilesPath=C:\Program Files\Microsoft ADAM\AD-APP-LDAP\data
 ServiceAccount=APP3\Administrator
 ServicePassword=Pa$$Word1
 AddPermissionsToServiceAccount=Yes
 Administrator=APP3\Administrator
 ImportLDIFFiles="MS-User.LDF"
 SourceUserName=APP3\Administrator
 SourcePassword=Pa$$Word1
 ```
## <a name="appendix-c---populate-ad-lds-powershell-script"></a>Apéndice C: Relleno del script de PowerShell de AD LDS
Script de PowerShell para rellenar AD LDS con contenedores y una cuenta de servicio.



```powershell
# Filename:    2_PopulateADLDS.ps1
# Description: Populates our AD LDS environment with 2 containers and a service account

# DISCLAIMER:
# Copyright (c) Microsoft Corporation. All rights reserved. This 
# script is made available to you without any express, implied or 
# statutory warranty, not even the implied warranty of 
# merchantability or fitness for a particular purpose, or the 
# warranty of title or non-infringement. The entire risk of the 
# use or the results from the use of this script remains with you.
#
#
#
#
# Create service accounts container
New-ADObject -Name "ServiceAccounts" -Type "container" -Path "CN=App,DC=contoso,DC=lab" -Server "APP3:389"
Write-Output "Creating ServiceAccounts container"

# Create cloud users container
New-ADObject -Name "CloudUsers" -Type "container" -Path "CN=App,DC=contoso,DC=lab" -Server "APP3:389"
Write-Output "Creating CloudUsers container"

# Create a new service account
New-ADUser -name "svcAccountLDAP" -accountpassword  (ConvertTo-SecureString -AsPlainText 'Pa$$1Word' -Force) -Displayname "LDAP Service Account" -server 'APP3:389' -path "CN=ServiceAccounts,CN=App,DC=contoso,DC=lab"
Write-Output "Creating service account"

# Enable the new service account
Enable-ADAccount -Identity "CN=svcAccountLDAP,CN=ServiceAccounts,CN=App,DC=contoso,DC=lab" -Server "APP3:389"
Write-Output "Enabling service account"

# Add the service account to the Administrators role
Get-ADGroup -Server "APP3:389" -SearchBase "CN=Administrators,CN=Roles,CN=App,DC=contoso,DC=lab" -Filter "name -like 'Administrators'" | Add-ADGroupMember -Members "CN=svcAccountLDAP,CN=ServiceAccounts,CN=App,DC=contoso,DC=lab"
Write-Output "Adding service accounnt to Administrators role"


 ```
