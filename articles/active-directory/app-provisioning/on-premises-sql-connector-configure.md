---
title: Configuración del conector SQL genérico del host del conector ECMA de Azure AD
description: En este documento se describe cómo configurar el conector SQL genérico del host del conector ECMA de Azure AD.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 06/06/2021
ms.author: billmath
ms.reviewer: arvinh
ms.openlocfilehash: a78ae13af84f3453e3a6119f163d90cd37be16bc
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437372"
---
# <a name="azure-ad-ecma-connector-host-generic-sql-connector-configuration"></a>Configuración del conector SQL genérico del host del conector ECMA de Azure AD

>[!IMPORTANT]
> La versión preliminar de aprovisionamiento local se encuentra actualmente en una versión preliminar solo por invitación. Para solicitar acceso a la capacidad, use el [formulario de solicitud de acceso](https://aka.ms/onpremprovisioningpublicpreviewaccess). La versión preliminar estará a disposición de más clientes y conectores durante los próximos meses, cuando se prepare la disponibilidad general.

En este artículo se describe cómo crear un conector SQL con el host del conector ECMA de Azure Active Directory y cómo configurarlo. Tendrá que realizar esta tarea después de haber instalado correctamente el host del conector ECMA de Azure AD.

>[!NOTE] 
> En este artículo solo se trata la configuración del conector SQL genérico. Para obtener un ejemplo paso a paso de cómo configurar el conector SQL genérico, consulte [Tutorial: Conector SQL genérico del host del conector ECMA de Azure AD](tutorial-ecma-sql-connector.md).

 Este flujo le guía a través del proceso de instalación y configuración del host del conector ECMA de Azure AD.

 ![Diagrama que muestra el flujo de instalación.](./media/on-premises-sql-connector-configure/flow-1.png)

Para más información sobre la instalación y configuración, consulte:
   - [Requisitos previos del host del conector ECMA de Azure AD](on-premises-ecma-prerequisites.md)
   - [Instalación del host del conector ECMA de Azure AD](on-premises-ecma-install.md)
   - [Configuración del host del conector ECMA de Azure AD y el agente de aprovisionamiento](on-premises-ecma-configure.md)

En función de las opciones que seleccione, es posible que algunas de las pantallas del asistente no estén disponibles y que la información sea ligeramente diferente. Para los fines de esta configuración, se usa el tipo de objeto de usuario. Use la siguiente información como orientación para la configuración. 

#### <a name="supported-systems"></a>Sistemas compatibles
* Microsoft SQL Server y Azure SQL
* IBM DB2 10.x
* IBM DB2 9.x
* Oracle 10 y 11g
* Oracle 12c y 18c
* MySQL 5.x

## <a name="create-a-generic-sql-connector"></a>Creación de un conector SQL genérico

Para crear un conector SQL genérico:

 1. Seleccione el acceso directo Host del conector ECMA en el escritorio.
 1. Seleccione **Nuevo conector**.
 
     ![Captura de pantalla que muestra la selección de Nuevo conector.](.\media\on-premises-sql-connector-configure\sql-1.png)

 1. En la página **Propiedades**, rellene los cuadros y seleccione **Siguiente**. Use la tabla que sigue a la imagen como guía sobre los cuadros individuales.
 
     ![Captura de pantalla que muestra la especificación de las propiedades.](.\media\on-premises-sql-connector-configure\sql-2.png)

     |Propiedad|Descripción|
     |-----|-----|
     |Nombre|El nombre de este conector.|
     |Temporizador de sincronización automática (minutos)|El mínimo permitido es de 120 minutos.|
     |Token secreto|123456 (el token debe ser una cadena de 10 a 20 caracteres ASCII o dígitos).|
     |Descripción|La descripción del conector.|
     |DLL de extensión|Para un conector SQL genérico, seleccione **Microsoft.IAM.Connector.GenericSql.dll**.|
 1. En la página **Conectividad**, rellene los cuadros y seleccione **Siguiente**. Use la tabla que sigue a la imagen como guía sobre los cuadros individuales.
 
     ![Captura de pantalla que muestra la especificación de los valores de conectividad.](.\media\on-premises-sql-connector-configure\sql-3.png)

     |Propiedad|Descripción|
     |-----|-----|
     |Archivo DSN|El nombre del archivo de origen de datos que se usa para conectarse a la instancia de SQL Server.|
     |Nombre de usuario|El nombre de usuario de una persona con derechos para la instancia de SQL Server. Debe tener el formato hostname\sqladminaccount para servidores independientes o domain\sqladminaccount para servidores miembros del dominio.|
     |Contraseña|Contraseña del nombre de usuario que acaba de proporcionar.|
     |DN es el Delimitador|A menos que sepa que el entorno requiere esta configuración, no active las casillas **DN es el Delimitador** y **Export Type:Object Replace**.|
     |Export Type:Object Replace||
 1. En la página **Esquema 1**, rellene los cuadros y seleccione **Siguiente**. Use la tabla que sigue a la imagen como guía sobre los cuadros individuales.
 
     ![Captura de pantalla que muestra la página Esquema 1.](.\media\on-premises-sql-connector-configure\sql-4.png)

     |Propiedad|Descripción|
     |-----|-----|
     |Método de detección de tipo de objeto|El método que se usa para detectar el tipo de objeto que el conector va a aprovisionar.|
     |Lista de valores fijos/Tabla/Vista/SP|Este cuadro debe contener **Usuario**.|
     |Nombre de columna para Tabla/Vista/SP||
     |Parámetros de procedimiento almacenado||
     |Proporcionar consulta SQL para detectar tipos de objeto||
 1. En la página **Esquema 2**, rellene los cuadros y seleccione **Siguiente**. Use la tabla que sigue a la imagen como guía sobre los cuadros individuales. Esta pantalla de esquema puede ser ligeramente diferente o tener información adicional en función de los tipos de objeto seleccionados en el paso anterior.
 
     ![Captura de pantalla que muestra la página Esquema 2.](.\media\on-premises-sql-connector-configure\sql-5.png)

     |Propiedad|Descripción|
     |-----|-----|
     |User:Attribute Detection|Esta propiedad debería establecerse en **Tabla**.|
     |User:Table/View/SP|Este cuadro debe contener **Empleados**.|
     |Usuario: Nombre de tabla/vistas multivalor||
     |Usuario: Parámetros de procedimiento almacenado||
     |Usuario: Proporcionar consulta SQL para detectar atributos||
 1. En la página **Esquema 3**, rellene los cuadros y seleccione **Siguiente**. Use la tabla que sigue a la imagen como guía sobre los cuadros individuales. Los atributos que aparezcan dependerán de la información que proporcionó en el paso anterior.
 
     ![Captura de pantalla que muestra la página Esquema 3.](.\media\on-premises-sql-connector-configure\sql-6.png)

     |Propiedad|Descripción|
     |-----|-----|
     |Seleccionar atributo DN para User||
 1. En la página **Esquema 4**, revise el atributo **DataType** y la dirección del flujo del conector. Puede ajustarlos si es necesario y seleccionar **Siguiente**.
 
     ![Captura de pantalla que muestra la página Esquema 4.](.\media\on-premises-sql-connector-configure\sql-7.png)  
 1. En la página **Global**, rellene los cuadros y seleccione **Siguiente**. Use la tabla que sigue a la imagen como guía sobre los cuadros individuales.
 
     ![Captura de pantalla que muestra la página Global.](.\media\on-premises-sql-connector-configure\sql-8.png)

     |Propiedad|Descripción|
     |-----|-----|
     |Consulta de marca de agua||
     |Zona horaria del origen de datos|Seleccione la zona horaria en la que se encuentra el origen de datos.|
     |Formato de fecha y hora del origen de datos|Especifique el formato del origen de datos.|
     |Usar parámetros con nombre para ejecutar un procedimiento almacenado||
     |Métodos de operación||
     |Nombre de la extensión||
     |Establecer el nombre del SP de contraseña||
     |Establecer parámetros del SP de contraseña||
 1. En la página **Seleccionar partición**, asegúrese de que estén seleccionadas las particiones correctas y seleccione **Siguiente**.
 
     ![Captura de pantalla que muestra la página Seleccionar partición.](.\media\on-premises-sql-connector-configure\sql-9.png)

 1. En la página **Perfiles de ejecución**, seleccione los perfiles de ejecución que desea usar y seleccione **Siguiente**.
 
     ![Captura de pantalla que muestra la página Perfiles de ejecución.](.\media\on-premises-sql-connector-configure\sql-10.png)

     |Propiedad|Descripción|
     |-----|-----|
     |Exportación|Perfil de ejecución que exportará datos a SQL. Este perfil de ejecución es obligatorio.|
     |Importación completa|Perfil de ejecución que importará todos los datos de orígenes SQL especificados anteriormente.|
     |Importación diferencial|Perfil de ejecución que importará solo los cambios de SQL desde la última importación completa o diferencial.|
 
 1. En la página **Perfiles de ejecución**, rellene los cuadros y seleccione **Siguiente**. Use la tabla que sigue a la imagen como guía sobre los cuadros individuales.
  
     ![Captura de pantalla que muestra las opciones de especificación para exportar información.](.\media\on-premises-sql-connector-configure\sql-11.png)

     |Propiedad|Descripción|
     |-----|-----|
     |Método de operación||
     |Tabla/Vista/SP||
     |Iniciar nombre de parámetro de índice||
     |Finalizar nombre de parámetro de índice||
     |Parámetros de procedimiento almacenado||
 
 1. En la página **Tipos de objeto**, rellene los cuadros y seleccione **Siguiente**. Use la tabla que sigue a la imagen como guía sobre los cuadros individuales. 
 
     ![Captura de pantalla que muestra la página Tipos de objeto.](.\media\on-premises-sql-connector-configure\sql-12.png)

     |Propiedad|Descripción|
     |-----|-----|
     |Objeto de destino|Objeto que está configurando.|
     |Delimitador|Atributo que se usará como delimitador del objeto. Este atributo debe ser único en el sistema de destino. El servicio de aprovisionamiento de Azure AD consulta al host ECMA mediante este atributo después del ciclo inicial. Este valor de delimitador debe ser igual que el valor de delimitador del esquema 3.|
     |Atributo de consulta|Lo usa el host ECMA para consultar la caché en memoria. Este atributo debe ser único.|
     |DN|Atributo que se usa para el nombre distintivo del objeto de destino. La casilla **Generado automáticamente** debe seleccionarse en la mayoría de los casos. Si no está seleccionada, asegúrese de que el atributo DN esté asignado a un atributo de Azure AD que almacene el DN en este formato: CN = anchorValue, Object = objectType.|
 
 1. El host ECMA detecta los atributos que admite el sistema de destino. Puede elegir cuál de esos atributos desea exponer a Azure AD. A continuación, estos atributos se pueden configurar en Azure Portal para el aprovisionamiento. En la página **Seleccionar atributos**, seleccione los atributos que desee agregar de la lista desplegable.
 
     ![Captura de pantalla que muestra la página Seleccionar atributos.](.\media\on-premises-sql-connector-configure\sql-13.png)

1. En la página **Desaprovisionando**, revise la información de desaprovisionamiento y realice los ajustes necesarios. Los atributos seleccionados en la página anterior no estarán disponibles para seleccionar en la página **Desaprovisionando**. Seleccione **Finalizar**.

     ![Captura de pantalla que muestra la página Desaprovisionando.](.\media\on-premises-sql-connector-configure\sql-14.png)

## <a name="next-steps"></a>Pasos siguientes

- [Aprovisionamiento de aplicaciones](user-provisioning.md)
- [Instalación del host del conector ECMA de Azure AD](on-premises-ecma-install.md)
- [Configuración del host del conector ECMA de Azure AD](on-premises-ecma-configure.md)
- [Tutorial: Conector SQL genérico del host del conector ECMA de Azure AD](tutorial-ecma-sql-connector.md)
