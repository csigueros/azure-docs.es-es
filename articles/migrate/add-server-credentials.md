---
title: Proporcionar credenciales de servidor para detectar el inventario de software, dependencias, aplicaciones web e instancias y bases de datos de SQL Server
description: Obtenga información sobre cómo proporcionar credenciales de servidor en el administrador de configuración del dispositivo
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: 2128145e0f2efa7a443c7a0c972117f29de73765
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "122965419"
---
# <a name="provide-server-credentials-to-discover-software-inventory-dependencies-web-apps-and-sql-server-instances-and-databases"></a>Proporcionar credenciales de servidor para detectar el inventario de software, dependencias, aplicaciones web e instancias y bases de datos de SQL Server

Siga este artículo para aprender a agregar varias credenciales de servidor en el administrador de configuración del dispositivo a fin de realizar el inventario de software (detectar aplicaciones instaladas) y el análisis de dependencia sin agente, y detectar aplicaciones web, y bases de datos e instancias de SQL Server.

El [dispositivo de Azure Migrate](migrate-appliance.md) es un dispositivo ligero que Azure Migrate: detección y evaluación usa para detectar los servidores locales que se ejecutan en el entorno de VMware y enviar los metadatos de configuración y rendimiento del servidor a Azure. El dispositivo también se puede usar para realizar el inventario de software, el análisis de dependencias sin agente y la detección de aplicaciones web e instancias y bases de datos de SQL Server.

Si quiere aprovechar estas características, proporcione las credenciales del servidor siguiendo los pasos que se indican a continuación. El dispositivo intentará asignar automáticamente las credenciales a los servidores para ejecutar las características de detección.

## <a name="add-credentials-for-servers-running-in-vmware-environment"></a>Agregar credenciales para servidores que se ejecutan en el entorno de VMware

### <a name="types-of-server-credentials-supported"></a>Tipos de credenciales de servidor admitidas

Puede agregar varias credenciales de servidor en el administrador de configuración del dispositivo, que pueden ser credenciales de autenticación de dominio, no de dominio (Windows o Linux) o SQL Server.

Los tipos de credenciales de servidor admitidos se indican en la tabla siguiente:

Tipo de credenciales | Descripción
--- | ---
**Credenciales de dominio** | Puede agregar **Credenciales del dominio** seleccionando la opción en la lista desplegable del modal **Agregar credenciales**. <br/><br/> Para proporcionar las credenciales de dominio, debe especificar el **Nombre de dominio** que se debe proporcionar en el formato de FQDN (por ejemplo, prod.corp.contoso.com). <br/><br/> También debe especificar un nombre descriptivo para las credenciales, el nombre de usuario y la contraseña. <br/><br/> Se validará automáticamente la autenticidad de las credenciales de dominio agregadas en Active Directory del dominio. Esto es para evitar bloqueos de cuentas cuando el dispositivo intente asignar las credenciales de dominio en los servidores detectados. <br/><br/>Para que el dispositivo valide las credenciales con el controlador de dominio, debe poder resolver el nombre de este. Asegúrese de haber proporcionado el nombre de dominio correcto al agregar las credenciales; de lo contrario, se producirá un error en la validación.<br/><br/> El dispositivo no intentará asignar las credenciales de dominio que no han superado la validación. Debe tener al menos una credencial de dominio validada correctamente o al menos una credencial no de dominio para continuar con el inventario de software.<br/><br/>Las credenciales de dominio asignadas automáticamente a instancias de Windows Server se usarán para realizar el inventario de software y también se pueden usar para detectar aplicaciones web e instancias y bases de datos de SQL Server _(si ha configurado el modo de autenticación de Windows en las instancias de SQL Server)_ .<br/> [Obtenga más información](/dotnet/framework/data/adonet/sql/authentication-in-sql-server) sobre los tipos de modos de autenticación admitidos en los servidores SQL Server.
**Credenciales que no son de dominio (Windows o Linux)** | Puede agregar credenciales **Windows (Non-domain)** [Windows (no de dominio)] o **Linux (Non-domain)** [Linux (no de dominio)] seleccionando la opción correspondiente en la lista desplegable del modal **Agregar credenciales**. <br/><br/> Debe especificar un nombre descriptivo para las credenciales, el nombre de usuario y la contraseña.
**Credenciales de autenticación de SQL Server** | Puede agregar las credenciales de **Autenticación de SQL Server** seleccionando la opción en la lista desplegable del modal **Agregar credenciales**. <br/><br/> Debe especificar un nombre descriptivo para las credenciales, el nombre de usuario y la contraseña. <br/><br/> Puede agregar este tipo de credenciales para detectar instancias y bases de datos de SQL Server que se ejecutan en el entorno de VMware, si ha configurado el modo de autenticación de SQL Server en las instancias de SQL Server.<br/> [Obtenga más información](/dotnet/framework/data/adonet/sql/authentication-in-sql-server) sobre los tipos de modos de autenticación admitidos en los servidores SQL Server.<br/><br/> Debe proporcionar al menos una credencial de dominio validada correctamente o al menos una credencial de Windows (no de dominio) para que el dispositivo pueda completar el inventario de software para detectar el SQL instalado en los servidores antes de usar las credenciales de autenticación de SQL Server para detectar las instancias y bases de datos de SQL Server.

Compruebe los permisos necesarios en las credenciales de Windows o Linux para realizar el inventario de software, el análisis de dependencias sin agente y la detección de aplicaciones web e instancias y bases de datos de SQL Server.

### <a name="required-permissions"></a>Permisos necesarios

En la tabla siguiente se enumeran los permisos necesarios en las credenciales del servidor proporcionadas en el dispositivo para ejecutar las características respectivas:

Característica | Credenciales de Windows | Credenciales de Linux
---| ---| ---
**Inventario de software** | Cuenta de usuario invitado | Cuenta de usuario normal (permisos de acceso no sudo)
**Detección de instancias y bases de datos de SQL Server** | Cuenta de usuario que es miembro del rol de servidor sysadmin. | _En la actualidad no se admite_
**Detección de aplicaciones web de ASP.NET** | Cuenta de dominio o no de dominio (local) con permisos administrativos | _En la actualidad no se admite_
**Análisis de dependencia sin agentes** | Cuenta de dominio o no de dominio (local) con permisos administrativos | Cuenta de usuario raíz o <br/> una cuenta con estos permisos en los archivos /bin/netstat y /bin/ls: CAP_DAC_READ_SEARCH y CAP_SYS_PTRACE.<br/><br/> Establezca estas funciones con los siguientes comandos: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat

### <a name="recommended-practices-to-provide-credentials"></a>Procedimientos recomendados para proporcionar credenciales

- Se recomienda crear una cuenta de usuario de dominio dedicada con los [permisos necesarios](add-server-credentials.md#required-permissions), cuyo ámbito sea realizar el inventario de software, el análisis de dependencias sin agente y la detección de aplicaciones web e instancias y bases de datos de SQL Server en los servidores deseados.
- Se recomienda proporcionar al menos una credencial de dominio validada correctamente o al menos una credencial no de dominio para iniciar el inventario de software.
- Para detectar las instancias y bases de datos de SQL Server, puede proporcionar credenciales de dominio, si ha configurado el modo de autenticación de Windows en las instancias de SQL Server.
- También puede proporcionar credenciales de autenticación de SQL Server si ha configurado el modo de autenticación de SQL Server en las instancias de SQL Server, pero se recomienda proporcionar al menos una credencial de dominio validada correctamente o al menos una credencial de Windows (no de dominio) para que el dispositivo pueda completar primero el inventario de software.

## <a name="credentials-handling-on-appliance"></a>Control de credenciales en el dispositivo

- Todas las credenciales proporcionadas en el administrador de configuración del dispositivo se almacenan localmente en el servidor del dispositivo y no se envían a Azure.
- Las credenciales almacenadas en el servidor del dispositivo se cifran mediante la API de protección de datos (DPAPI).
- Una vez agregadas las credenciales, el dispositivo intenta asignar automáticamente las credenciales para realizar la detección en los servidores respectivos.
- El dispositivo usa las credenciales asignadas automáticamente en un servidor para todos los ciclos de detección posteriores hasta que las credenciales puedan capturar los datos de detección necesarios. Si las credenciales dejan de funcionar, el dispositivo intenta de nuevo realizar la asignación desde la lista de credenciales agregadas y continuar la detección en curso en el servidor.
- Se validará automáticamente la autenticidad de las credenciales de dominio agregadas en Active Directory del dominio. Esto es para evitar bloqueos de cuentas cuando el dispositivo intente asignar las credenciales de dominio en los servidores detectados. El dispositivo no intentará asignar las credenciales de dominio que no han superado la validación.
- Si el dispositivo no puede asignar ninguna credencial de dominio o no de dominio en un servidor, verá el estado "Las credenciales no están disponibles" en el servidor del proyecto.

## <a name="next-steps"></a>Pasos siguientes

Revise los tutoriales para la [detección de servidores que se ejecutan en su entorno de VMware](tutorial-discover-vmware.md).