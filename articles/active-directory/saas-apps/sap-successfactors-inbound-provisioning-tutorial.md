---
title: 'Tutorial: Configuración del aprovisionamiento de entrada de SuccessFactors en AD y Azure AD'
description: Aprenda a configurar el aprovisionamiento de entrada de SuccessFactors
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: d4a1350999258b47db4a5188fa9eab92aa56e567
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604953"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning"></a>Tutorial: Configuración del aprovisionamiento de usuarios de SAP SuccessFactors a Active Directory 
El objetivo de este tutorial es mostrar los pasos que debe realizar para aprovisionar usuarios de SuccessFactors Employee Central a Active Directory (AD) y Azure AD, con escritura diferida opcional de la dirección de correo electrónico en SuccessFactors. 

>[!NOTE]
>Use este tutorial si los usuarios que quiere aprovisionar de SuccessFactors necesitan una cuenta de AD local y, opcionalmente, una cuenta de Azure AD. Si los usuarios de SuccessFactors solo necesitan una cuenta de Azure AD (usuarios de solo nube), consulte el tutorial sobre cómo [configurar SAP SuccessFactors para el aprovisionamiento de usuarios de Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md). 


## <a name="overview"></a>Información general

El [servicio de aprovisionamiento de usuarios de Azure Active Directory](../app-provisioning/user-provisioning.md) se integra con [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) para administrar el ciclo de vida de identidad de los usuarios. 

Los flujos de trabajo de aprovisionamiento de usuarios de SuccessFactors que admite el servicio de aprovisionamiento de usuarios de Azure AD permiten la automatización de los siguientes escenarios de recursos humanos y de administración del ciclo de vida de identidad:

* **Contratación de nuevos empleados**: cuando se agrega un nuevo empleado a SuccessFactors, se crea automáticamente una cuenta de usuario en Active Directory, Azure Active Directory y, opcionalmente, en Microsoft 365, así como en [otras aplicaciones SaaS compatibles con Azure AD](../app-provisioning/user-provisioning.md), con escritura diferida de la dirección de correo electrónico en SuccessFactors.

* **Actualizaciones de atributos y perfiles de empleados**: si se actualiza un registro de empleado en SuccessFactors (por ejemplo, el nombre, el cargo o el jefe), su cuenta de usuario se actualizará automáticamente en Active Directory, Azure Active Directory y, opcionalmente, en Microsoft 365, así como en [otras aplicaciones SaaS compatibles con Azure AD](../app-provisioning/user-provisioning.md).

* **Despidos de empleados**: cuando se prescinde de un empleado en SuccessFactors, su cuenta de usuario se deshabilita automáticamente en Active Directory, Azure Active Directory y, opcionalmente, en Microsoft 365, así como en [otras aplicaciones SaaS compatibles con Azure AD](../app-provisioning/user-provisioning.md).

* **Recontrataciones de empleados**: cuando se vuelve a contratar a un empleado en SuccessFactors, se puede reactivar o volver a aprovisionar automáticamente su cuenta antigua (en función de sus preferencias) en Active Directory, Azure Active Directory y, opcionalmente, en Microsoft 365, así como en [otras aplicaciones SaaS compatibles con Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>¿Para quién es más adecuada esta solución de aprovisionamiento de usuarios?

Esta solución de aprovisionamiento de usuarios de SuccessFactors a Active Directory es adecuada para:

* Organizaciones que desean una solución basada en la nube pregenerada para el aprovisionamiento de usuarios de SuccessFactors

* Organizaciones que requieren aprovisionamiento de usuarios directo de SuccessFactors a Active Directory.

* Organizaciones que requieren que los usuarios se aprovisionen con los datos obtenidos de [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizaciones que necesitan combinar y mover usuarios y dejarles que se sincronicen con uno o varios bosques, dominios o unidades organizativas de Active Directory en función de únicamente la información de cambio detectada en [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html).

* Organizaciones que usan Microsoft 365 para el correo electrónico

## <a name="solution-architecture"></a>Arquitectura de la solución

En esta sección se describe la arquitectura de la solución de aprovisionamiento de usuarios de un extremo a otro para entornos híbridos comunes. Hay dos flujos relacionados:

* **Flujo de datos de RR. HH. autorizado, desde SuccessFactors hasta Active Directory local:** en este flujo, los eventos de trabajadores (por ejemplo, nuevas contrataciones, traslados o despidos), se producen en primer lugar en la nube de SuccessFactors Employee Central y, luego, los datos de eventos fluyen hacia Active Directory local mediante Azure AD y el agente de aprovisionamiento. En función del evento, puede dar lugar a las operaciones de creación, actualización, habilitación o deshabilitación en AD.
* **Flujo de escritura diferida de correo electrónico, de Active Directory local a SuccessFactors**: una vez finalizada la creación de la cuenta en Active Directory, se sincroniza con Azure AD mediante Azure AD Connect y el atributo de correo electrónico puede escribirse de forma diferida en SuccessFactors.

  ![Información general](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Flujo de datos de usuarios de un extremo a otro

1. El equipo de RR. HH. realiza las transacciones relacionadas con los trabajadores (empleados que se incorporan a la empresa, se trasladan o se van, o nuevas contrataciones, traslados o despidos) en SuccessFactors Employee Central.
2. El servicio de aprovisionamiento de Azure AD ejecuta sincronizaciones programadas de identidades desde SuccessFactors EC e identifica los cambios que deben procesarse para la sincronización con Active Directory local.
3. El servicio de aprovisionamiento de Azure AD invoca al agente de aprovisionamiento de Azure AD Connect local con una carga de solicitud que contiene las operaciones de creación, actualización, habilitación o deshabilitación de las cuentas de AD.
4. El agente de aprovisionamiento de Azure AD Connect usa una cuenta de servicio para agregar o actualizar los datos de la cuenta de AD.
5. El motor de Azure AD Connect Sync ejecuta una sincronización diferencial para extraer actualizaciones de AD.
6. Las actualizaciones de Active Directory se sincronizan con Azure Active Directory.
7. Si está configurada la [aplicación de escritura diferida de SuccessFactors](sap-successfactors-writeback-tutorial.md), se escribe de forma diferida el atributo de correo electrónico en SuccessFactors, en función del atributo coincidente usado.

## <a name="planning-your-deployment"></a>Planeamiento de la implementación

La configuración del aprovisionamiento de usuarios controlado por RR. HH. en la nube desde SuccessFactors a AD requiere un planeamiento considerable que abarca distintos aspectos, como:
* La configuración del agente de aprovisionamiento de Azure AD Connect. 
* El número de aplicaciones de aprovisionamiento de usuarios de SuccessFactors a AD que se van a implementar.
* Identificador de coincidencia, asignación de atributos y filtros de transformación y ámbito

Consulte el [plan de implementación de RR. HH. en la nube](../app-provisioning/plan-cloud-hr-provision.md) para instrucciones detalladas sobre estos temas. Consulte [Referencia de integración de SAP SuccessFactors](../app-provisioning/sap-successfactors-integration-reference.md) para obtener información sobre las entidades admitidas, los detalles de procesamiento y cómo personalizar la integración para distintos escenarios de recursos humanos. 

## <a name="configuring-successfactors-for-the-integration"></a>Configuración de SuccessFactors para la integración

Un requisito común de todos los conectores de aprovisionamiento de SuccessFactors es que requieren credenciales de una cuenta de SuccessFactors con los permisos correctos para invocar las API OData de SuccessFactors. En esta sección se describen los pasos para crear la cuenta de servicio de SuccessFactors y conceder los permisos adecuados. 

* [Creación e identificación de una cuenta de usuario de API en SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Creación de un rol de permisos de API](#create-an-api-permissions-role)
* [Creación de un grupo de permisos para el usuario de la API](#create-a-permission-group-for-the-api-user)
* [Concesión del rol de permisos al grupo de permisos](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Creación e identificación de una cuenta de usuario de API en SuccessFactors
Trabaje con el equipo de administración de SuccessFactors o con el asociado de implementación para crear o identificar una cuenta de usuario en SuccessFactors que se usará para invocar las API OData. Las credenciales de nombre de usuario y contraseña de esta cuenta se necesitarán al configurar las aplicaciones de aprovisionamiento en Azure AD. 

### <a name="create-an-api-permissions-role"></a>Creación de un rol de permisos de API

1. Inicie sesión en SAP SuccessFactors con una cuenta de usuario que tenga acceso al centro de administración.
1. Busque *Manage Permission Roles* (Administrar roles de permisos) y, después, seleccione **Manage Permission Roles** (Administrar roles de permisos) en los resultados de la búsqueda.
  ![Administración de roles de permisos](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
1. En Permission Role List (Lista de roles de permisos), haga clic en **Create New** (Crear).
    > [!div class="mx-imgBorder"]
    > ![Creación de un rol de permisos](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
1. Agregue valores en **Role Name** (Nombre de rol) y **Description** (Descripción) para el nuevo rol de permisos. El nombre y la descripción deben indicar que el rol se utilizará para los permisos de uso de la API.
    > [!div class="mx-imgBorder"]
    > ![Detalle de rol de permisos](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
1. En Permission settings (Configuración de los permisos), haga clic en **Permission...** (Permiso), desplácese hacia abajo en la lista de permisos y haga clic en **Manage Integration Tools** (Administrar herramientas de integración). Active la casilla **Allow Admin to Access to OData API through Basic Authentication** (Permitir que el administrador acceda a la API OData mediante autenticación básica).
    > [!div class="mx-imgBorder"]
    > ![Administración de herramientas de integración](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
1. Desplácese hacia abajo en el mismo cuadro y seleccione **Employee Central API**. Agregue permisos, tal como se muestra a continuación, de lectura y edición mediante la API ODATA. Seleccione la opción de edición si tiene previsto usar la misma cuenta para el escenario de escritura diferida en SuccessFactors. 
    > [!div class="mx-imgBorder"]
    > ![Permisos de lectura y escritura](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. En el mismo cuadro de permisos, vaya a **User Permissions -> Employee Data** (Permisos de usuario > Datos de empleados) y revise los atributos que la cuenta de servicio puede leer desde el inquilino de SuccessFactors. Por ejemplo, para recuperar el atributo del *nombre de usuario* de SuccessFactors, asegúrese de que se conceda el permiso "View" (Ver) para este atributo. De igual forma, revise cada atributo para ver el permiso. 

    > [!div class="mx-imgBorder"]
    > ![Permisos de datos de empleados](./media/sap-successfactors-inbound-provisioning/review-employee-data-permissions.png)
   

    >[!NOTE]
    >Para obtener la lista completa de atributos recuperados por esta aplicación de aprovisionamiento, consulte [Referencia de atributos de SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md).

1. Haga clic en **Done** (Acabado). Haga clic en **Guardar cambios**.

### <a name="create-a-permission-group-for-the-api-user"></a>Creación de un grupo de permisos para el usuario de la API

1. En el centro de administración de SuccessFactors, busque *Manage Permission Groups* (Administrar grupos de permisos) y, después, seleccione **Manage Permission Groups** (Administrar grupos de permisos) en los resultados de la búsqueda.
    > [!div class="mx-imgBorder"]
    > ![Administración de grupos de permisos](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
1. En la ventana Manage Permission Groups (Administrar grupos de permisos), haga clic en **Create New** (Crear).
    > [!div class="mx-imgBorder"]
    > ![Incorporación de un nuevo grupo](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
1. Agregue un valor en Group Name (Nombre de grupo) para el nuevo grupo. El nombre del grupo debe indicar que se utilizará para los usuarios de la API.
    > [!div class="mx-imgBorder"]
    > ![Nombre del grupo de permisos](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
1. Agregue miembros al grupo. Por ejemplo, puede seleccionar **Username** (Nombre de usuario) en el menú desplegable People Pool (Grupo de personas) y, a continuación, escribir el nombre de usuario de la cuenta de API que se usará para la integración. 
    > [!div class="mx-imgBorder"]
    > ![Adición de miembros del grupo](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
1. Haga clic en **Done** (Listo) para terminar de crear el grupo de permisos.

### <a name="grant-permission-role-to-the-permission-group"></a>Concesión del rol de permisos al grupo de permisos

1. En el centro de administración de SuccessFactors, busque *Manage Permission Roles* (Administrar roles de permisos) y, después, seleccione **Manage Permission Roles** (Administrar roles de permisos) en los resultados de la búsqueda.
1. En **Permission Role List** (Lista de roles de permisos), seleccione el rol que creó para los permisos de uso de la API.
1. En **Grant this role to...** (Conceder este rol a), haga clic en el botón **Add...** (Agregar).
1. Seleccione **Permission Group...** (Grupo de permisos) en el menú desplegable y, a continuación, haga clic en **Select...** (Seleccionar) para abrir la ventana Groups (Grupos), en la que deberá buscar y seleccionar el grupo creado anteriormente. 
    > [!div class="mx-imgBorder"]
    > ![Incorporación del grupo de permisos](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
1. Revise la concesión del rol de permisos al grupo de permisos. 
    > [!div class="mx-imgBorder"]
    > ![Detalles de rol y grupo de permisos](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
1. Haga clic en **Guardar cambios**.

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>Configuración del aprovisionamiento de usuarios de SuccessFactors a Active Directory

En esta sección se describen los pasos para el aprovisionamiento de cuentas de usuario de SuccessFactors a cada dominio de Active Directory dentro del ámbito de su integración.

* [Incorporación de la aplicación de conector de aprovisionamiento y descarga del agente de aprovisionamiento](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Instalación y configuración de agentes de aprovisionamiento en el entorno local](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Configuración de la conectividad a SuccessFactors y Active Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [Configuración de las asignaciones de atributos](#part-4-configure-attribute-mappings)
* [Habilitar e iniciar el aprovisionamiento de usuarios](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Parte 1: Incorporación de la aplicación de conector de aprovisionamiento y descarga del agente de aprovisionamiento

**Para configurar el aprovisionamiento de SuccessFactors a Active Directory:**

1. Vaya a <https://portal.azure.com>.

2. En la barra de navegación de la izquierda, seleccione **Azure Active Directory**.

3. Seleccione **Enterprise Applications** (Aplicaciones empresariales) y **All Applications** (Todas las aplicaciones).

4. Seleccione **Add an application** (Agregar una aplicación) y seleccione la categoría **All** (Todos).

5. Busque **SuccessFactors to Active Directory User Provisioning** y agregue esa aplicación desde la galería.

6. Una vez que se haya agregado la aplicación y se muestre la pantalla de detalles de la aplicación, seleccione **Aprovisionamiento**.

7. Cambie el **Modo de** **aprovisionamiento** a **Automático**.

8. Haga clic en el banner de información que se muestra para descargar el agente de aprovisionamiento. 
   >[!div class="mx-imgBorder"]
   >![Descarga del agente](./media/workday-inbound-tutorial/pa-download-agent.png "Pantalla de descarga de agente")

### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Parte 2: Instalación y configuración de agentes de aprovisionamiento en el entorno local

Para realizar el aprovisionamiento en Active Directory local, el agente de aprovisionamiento debe estar instalado en un servidor unido a un dominio que tenga acceso de red a los dominios de Active Directory deseados.

Transfiera el instalador del agente descargado al host del servidor y siga los pasos indicados [en la sección del agente de instalación](../cloud-sync/how-to-install.md) para completar la configuración del agente.

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>Parte 3: En la aplicación de aprovisionamiento, configure la conectividad a SuccessFactors y Active Directory
En este paso, se establece la conectividad con SuccessFactors y Active Directory en Azure Portal. 

1. En Azure Portal, vuelva a la aplicación de aprovisionamiento de usuarios de SuccessFactors a Active Directory creada en la [parte 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent).

1. Cumplimente la sección **Credenciales de administrador** del siguiente modo:

   * **Nombre de usuario de administrador**: escriba el nombre de usuario de la cuenta de usuario de SuccessFactors API, con el identificador de empresa anexado. El formato es: **username\@companyID**

   * **Contraseña de administrador:** escriba la contraseña de la cuenta de usuario de SuccessFactors API. 

   * **URL de inquilino:** escriba el nombre del punto de conexión de servicios de la API OData de SuccessFactors. Especifique solo el nombre de host del servidor sin http o https. Este valor debería ser similar al siguiente: **\<api-server-name\>.successfactors.com**.

   * **Bosque de Active Directory**: el nombre de su dominio de Active Directory, según se ha registrado con el agente. Use la lista desplegable para seleccionar el dominio de destino para el aprovisionamiento. Este valor suele ser una cadena como: *contoso.com*.

   * **Contenedor de Active Directory:** escriba el DN del contenedor donde el agente debe crear cuentas de usuario de forma predeterminada.
        Ejemplo: *OU=Users,DC=contoso,DC=com*
        > [!NOTE]
        > Esta configuración solo entra en juego para creaciones de cuentas de usuario si el atributo *parentDistinguishedName* no está configurado en las asignaciones de atributos. Esta configuración no se usa para la búsqueda de usuarios o las operaciones de actualización. El subárbol de todo el dominio se encuentra en el ámbito de la operación de búsqueda.

   * **Correo electrónico de notificación**: escriba su dirección de correo electrónico y marque la casilla "Send email if failure occurs" (Enviar una notificación por correo electrónico cuando se produzca un error).
     > [!NOTE]
     > El servicio de aprovisionamiento de Azure AD envía la notificación por correo electrónico si el trabajo de aprovisionamiento entra en un estado de[cuarentena](../app-provisioning/application-provisioning-quarantine-status.md).

   * Haga clic en el botón **Probar conexión**. Si la prueba de conexión se lleva a cabo correctamente, haga clic en el botón **Guardar** situado en la parte superior. Si se produce un error, compruebe que las credenciales de SuccessFactors y las credenciales de AD configuradas en la instalación del agente sean válidas.
     >[!div class="mx-imgBorder"]
     >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * Una vez que las credenciales se guardan correctamente, en la sección **Asignaciones** se mostrará la asignación predeterminada **Synchronize SuccessFactors Users to On Premises Active Directory** (Sincronizar usuarios de SuccessFactors con Active Directory local).

### <a name="part-4-configure-attribute-mappings"></a>Parte 4: configuración de las asignaciones de atributos

En esta sección, configurará cómo fluyen los datos de los usuarios de SuccessFactors a Active Directory.

1. En la pestaña Aprovisionamiento, en **Asignaciones**, haga clic en **Synchronize SuccessFactors Users to On Premises Active Directory** (Sincronizar usuarios de SuccessFactors con Active Directory local).

1. En el campo **Ámbito de objeto de origen** puede seleccionar los conjuntos de usuarios de SuccessFactors que deben estar en el ámbito para el aprovisionamiento en AD; para ello, debe definir un conjunto de filtros basados en atributos. El ámbito predeterminado es "todos los usuarios de SuccessFactors". Filtros de ejemplo:

   * Ejemplo: Ámbito de los usuarios con personIdExternal entre 1 y 2 millones (excepto 2 millones)

      * Atributo: personIdExternal

      * Operador: REGEX Match

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Ejemplo: solo los empleados, no los trabajadores temporales

      * Atributo: EmployeeID

      * Operador: IS NOT NULL

   > [!TIP]
   > Al configurar la aplicación de aprovisionamiento por primera vez, deberá probar y verificar las expresiones y asignaciones de atributos para asegurarse de que ofrece el resultado deseado. Microsoft recomienda usar los filtros de ámbito de **Ámbito de objeto de origen** para probar las asignaciones con algunos usuarios de prueba de SuccessFactors. Una vez haya verificado que las asignaciones funcionan, puede quitar el filtro o expandirlo gradualmente para incluir más usuarios.

   > [!CAUTION] 
   > El comportamiento predeterminado del motor de aprovisionamiento es deshabilitar o eliminar usuarios que salen del ámbito. Puede que esta no sea la situación deseable en la integración de SuccessFactors con AD. Para invalidar este comportamiento predeterminado, consulte el artículo [Omisión de la eliminación de usuarios fuera del ámbito](../app-provisioning/skip-out-of-scope-deletions.md).
  
1. En el campo **Acciones del objeto de destino**, puede filtrar de forma global qué acciones se realizan en Active Directory. **Crear** y **Actualizar** son las más habituales.

1. En la sección **Asignaciones de atributos**, puede definir cómo se asignan los distintos atributos de SuccessFactors a los atributos de Active Directory.

     >[!NOTE]
     >Para obtener la lista completa de atributos de SuccessFactors compatibles con la aplicación, consulte [Referencia de atributos de SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md).

1. Haga clic en una asignación de atributos existente para actualizarla o haga clic en **Agregar nueva asignación** en la parte inferior de la pantalla para agregar asignaciones nuevas. Las asignaciones de atributos admiten estas propiedades:

      * **Tipo de asignación**

         * **Directo**: escribe el valor del atributo de SuccessFactors en el atributo de AD, sin cambios.

         * **Constante**: escribe un valor de cadena estático y constante en el atributo de AD.

         * **Expresión**: permite escribir un valor personalizado en el atributo de AD, en función de uno o varios atributos de SuccessFactors. [Para obtener más información, consulte este artículo sobre las expresiones](../app-provisioning/functions-for-customizing-application-data.md).

      * **Atributo de origen**: el atributo de usuario de SuccessFactors.

      * **Valor predeterminado**: opcional. Si el atributo de origen tiene un valor vacío, la asignación escribirá este valor.
            La configuración más habitual consiste en dejarlo en blanco.

      * **Atributo de destino**: atributo de usuario de Active Directory.

      * **Hacer coincidir objetos con este atributo**: especifica si se debe usar o no esta asignación para identificar de forma unívoca a los usuarios entre SuccessFactors y Active Directory. Este valor se suele establecer en el campo Worker ID (Id. del trabajador) de SuccessFactors, que se suele asignar a uno de los atributos de identificador de empleado de Active Directory.

      * **Precedencia de coincidencia**: se pueden establecer varios atributos coincidentes. Si hay varios, se evalúan en el orden definido por este campo. En el momento en que se encuentre una coincidencia, no se evaluarán más atributos coincidentes.

      * **Aplicar esta asignación**

         * **Siempre**: esta asignación se aplica a las acciones de creación y actualización de usuarios

         * **Solo durante la creación**: esta asignación se aplica solo a las acciones de creación de usuarios

1. Para guardar las asignaciones, haga clic en **Guardar** en la parte superior de la sección Asignación de atributos.

Una vez completada la configuración de la asignación de atributos, puede probar el aprovisionamiento de un solo usuario mediante [el aprovisionamiento a petición](../app-provisioning/provision-on-demand.md) y, a continuación, [habilitar e iniciar el servicio de aprovisionamiento de usuarios](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Habilitar e iniciar el aprovisionamiento de usuarios

Cuando se hayan completado las configuraciones de la aplicación de aprovisionamiento de SuccessFactors y se haya comprobado el aprovisionamiento de un solo usuario con el [aprovisionamiento a petición](../app-provisioning/provision-on-demand.md), puede activar el servicio de aprovisionamiento en Azure Portal.

> [!TIP]
> De forma predeterminada, al activar el servicio de aprovisionamiento, se iniciarán las operaciones de aprovisionamiento para todos los usuarios del ámbito. Si hay errores en la asignación o problemas con los datos de SuccessFactors, puede que se produzcan errores con el trabajo de aprovisionamiento y que entre en estado de cuarentena. Para evitar esto, como procedimiento recomendado, es conveniente configurar el filtro **Ámbito de objeto de origen** y probar las asignaciones de atributos con algunos usuarios de prueba mediante el [aprovisionamiento a petición](../app-provisioning/provision-on-demand.md) antes de iniciar la sincronización completa de todos los usuarios. Una vez haya verificado que las asignaciones funcionan y que obtiene los resultados deseados, puede quitar el filtro o expandirlo gradualmente para incluir más usuarios.

1. Vaya a la hoja **Aprovisionamiento** y haga clic en **Iniciar aprovisionamiento**.

1. Esta operación dará comienzo a la sincronización inicial, que puede tardar una cantidad de horas variable, según el número de usuarios que haya en el inquilino de SuccessFactors. Puede consultar en la barra de progreso el seguimiento del progreso del ciclo de sincronización. 

1. En cualquier momento, compruebe la pestaña **Registros de auditoría** en Azure Portal para ver las acciones que ha realizado el servicio de aprovisionamiento. Los registros de auditoría muestran todos los eventos de sincronización individuales realizados por el servicio de aprovisionamiento, por ejemplo, los usuarios que se leen fuera de SuccessFactors y que luego se agregan o actualizan en Active Directory. 

1. Una vez completada la sincronización inicial, se escribe un informe resumido de auditoría en la pestaña **Aprovisionamiento**, tal y como se muestra a continuación.

   > [!div class="mx-imgBorder"]
   > ![Barra de progreso de aprovisionamiento](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre los atributos de SuccessFactors compatibles para el aprovisionamiento de entrada](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Aprenda a configurar la escritura diferida de correo electrónico en SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](../app-provisioning/check-status-user-account-provisioning.md)
* [Aprenda a configurar el inicio de sesión único entre SuccessFactors y Azure Active Directory](successfactors-tutorial.md)
* [Aprenda a integrar otras aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [Aprenda a exportar e importar las configuraciones de aprovisionamiento](../app-provisioning/export-import-provisioning-configuration.md)