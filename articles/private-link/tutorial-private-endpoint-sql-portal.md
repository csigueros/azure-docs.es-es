---
title: 'Tutorial: Conexión a un servidor SQL de Azure mediante un punto de conexión privado de Azure: Portal'
description: Use este tutorial para aprender a crear un servidor SQL de Azure con un punto de conexión privado mediante Azure Portal.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 6a18c9e0dc8f7d6f78985b626fa36c84fe15876c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2021
ms.locfileid: "114292863"
---
# <a name="tutorial-connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-portal"></a>Tutorial: Conexión a un servidor SQL de Azure mediante un punto de conexión privado de Azure: Azure Portal

Un punto de conexión privado de Azure es el bloque de creación fundamental para Private Link en Azure. Permite que los recursos de Azure, como las máquinas virtuales, se comuniquen con recursos de Private Link de manera privada.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una red virtual y un host bastión.
> * Cree una máquina virtual.
> * Crear un servidor SQL de Azure y un punto de conexión privado.
> * Probar la conectividad con el punto de conexión privado del servidor SQL.

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en https://portal.azure.com.


## <a name="create-a-virtual-network-and-bastion-host"></a>Creación de una red virtual y un host bastión

En esta sección, creará una red virtual, una subred y un host bastión. 

El host bastión se utilizará para conectarse de forma segura a la máquina virtual a fin de probar el punto de conexión privado.

1. En la parte superior izquierda de la pantalla, seleccione **Crear un recurso > Redes > Red virtual** o busque **Red virtual** en el cuadro de búsqueda.

2. En **Crear red virtual**, escriba o seleccione esta información en la pestaña **Conceptos básicos**:

    | **Configuración**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalles del proyecto**  |                                                                 |
    | Suscripción     | Selección de su suscripción a Azure                                  |
    | Grupo de recursos   | Seleccione **CreateSQLEndpointTutorial-rg**. |
    | **Detalles de instancia** |                                                                 |
    | Nombre             | Escriba **myVNet**.                                    |
    | Region           | Seleccione **Este de EE. UU**. |

3. Seleccione la pestaña **Direcciones IP** o el botón **Siguiente: Direcciones IP** situado en la parte inferior de la página.

4. En la pestaña **Direcciones IP**, especifique esta información:

    | Configuración            | Value                      |
    |--------------------|----------------------------|
    | Espacio de direcciones IPv4 | Escriba **10.1.0.0/16**. |

5. En **Nombre de subred**, seleccione la palabra **predeterminada**.

6. En **Editar subred**, especifique esta información:

    | Configuración            | Value                      |
    |--------------------|----------------------------|
    | Nombre de subred | Escriba **mySubnet**. |
    | Intervalo de direcciones de subred | Escriba **10.1.0.0/24**. |

7. Seleccione **Guardar**.

8. Seleccione la pestaña **Seguridad** .

9. En **BastionHost**, seleccione **Habilitar**. Escriba esta información:

    | Configuración            | Value                      |
    |--------------------|----------------------------|
    | Nombre del bastión | Escriba **myBastionHost**. |
    | Espacio de direcciones de AzureBastionSubnet | Escriba **10.1.1.0/24**. |
    | Dirección IP pública | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **myBastionIP**. </br> Seleccione **Aceptar**. |


8. Seleccione la pestaña **Revisar y crear** o el botón **Revisar y crear**.

9. Seleccione **Crear**.

## <a name="create-a-virtual-machine"></a>Creación de una máquina virtual

En esta sección, creará una máquina virtual que se utilizará para probar el punto de conexión privado.

1. En la parte superior izquierda del portal, seleccione **Crear un recurso** > **Proceso** > **Máquina virtual** o **Máquina virtual** en el cuadro de búsqueda.
   
2. En **Crear una máquina virtual**, escriba o seleccione los valores en la pestaña **Básico**:

    | Configuración | Value                                          |
    |-----------------------|----------------------------------|
    | **Detalles del proyecto** |  |
    | Suscripción | Selección de su suscripción a Azure |
    | Grupo de recursos | Seleccione **CreateSQLEndpointTutorial**. |
    | **Detalles de instancia** |  |
    | Nombre de la máquina virtual | Escriba **myVM**. |
    | Region | Seleccione **Este de EE. UU**. |
    | Opciones de disponibilidad | Seleccione **No se requiere redundancia de la infraestructura** |
    | Imagen | Seleccione **Windows Server 2019 Datacenter - Gen1**. |
    | Instancia de Azure Spot | Seleccione **No**. |
    | Size | Elija el tamaño de la máquina virtual o acepte la configuración predeterminada. |
    | **Cuenta de administrador** |  |
    | Nombre de usuario | Escriba un nombre de usuario. |
    | Contraseña | Escriba una contraseña. |
    | Confirmar contraseña | Vuelva a escribir la contraseña. |

3. Seleccione la pestaña **Redes** o seleccione **Siguiente: Discos** y, después, **Siguiente: Redes**.
  
4. En la pestaña Redes, seleccione o escriba:

    | Configuración | Value |
    |-|-|
    | **Interfaz de red** |  |
    | Virtual network | **myVNet** |
    | Subnet | **mySubnet** |
    | Dirección IP pública | Seleccione **Ninguno**. |
    | Grupo de seguridad de red de NIC | **Basic**|
    | Puertos de entrada públicos | Seleccione **Ninguno**. |
   
5. Seleccione **Revisar + crear**. 
  
6. Revise la configuración y, a continuación, seleccione **Crear**.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-an-azure-sql-server-and-private-endpoint"></a><a name ="create-a-private-endpoint"></a>Creación de un servidor SQL de Azure y un punto de conexión privado

En esta sección, va a crear un servidor de SQL en Azure. 

1. En la parte superior izquierda de la pantalla en Azure Portal, seleccione **Crear un recurso** > **Bases de datos** > **SQL Database**.

1. En la pestaña **Básico** de **Crear base de datos SQL**, escriba o seleccione esta información:

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** | |
    | Suscripción | Seleccione su suscripción. |
    | Resource group | Seleccione **CreateSQLEndpointTutorial**. Ha creado este grupo de recursos en la sección anterior.|
    | **Detalles de la base de datos** |  |
    | Nombre de la base de datos  | Escriba **mysqldatabase**. Si el nombre ya existe, cree uno único. |
    | Servidor | Seleccione **Crear nuevo**. |

6. En **Nuevo servidor**, escriba o seleccione esta información:

    | Configuración | Value |
    | ------- | ----- |
    | Nombre del servidor  | Escriba **mysqlserver**. Si el nombre ya existe, cree uno único.|
    | Inicio de sesión de administrador de servidor | Escriba el nombre de administrador que prefiera. |
    | Contraseña | Escriba una contraseña de su elección. La contraseña debe tener al menos ocho caracteres y cumplir con los requisitos definidos. |
    | Location | Seleccione **Este de EE. UU**. |
    
7. Seleccione **Aceptar**.

8. Seleccione la pestaña **Redes** o el botón **Siguiente: Redes**.

9. En la pestaña **Redes**, escriba o seleccione esta información:

    | Configuración | Valor |
    | ------- | ----- |
    | **Conectividad de red** | |
    | Método de conectividad | Seleccione **Punto de conexión privado**. |
   
10. Seleccione **+ Agregar punto de conexión privado** en **Puntos de conexión privados**.

11. En **Crear un punto de conexión privado**, escriba o seleccione esta información:

    | Configuración | Value |
    | ------- | ----- |
    | Suscripción | Seleccione su suscripción. |
    | Resource group | Seleccione **CreateSQLEndpointTutorial**. |
    | Location | Seleccione **Este de EE. UU**. |
    | Nombre | Escriba **myPrivateSQLendpoint**. |
    | Recurso secundario de destino | Seleccione **SQLServer**. |
    | **Redes** |  |
    | Virtual network | Seleccione **myVNet**. |
    | Subnet | Seleccione **mySubnet**. |
    | **Integración de DNS privado** | |
    | Integración con una zona DNS privada | Deje el valor predeterminado **Sí**. |
    | Zona DNS privada | Deje el valor predeterminado **(nuevo) privatelink.database.windows.net**. |

12. Seleccione **Aceptar**. 

13. Seleccione **Revisar + crear**.

14. Seleccione **Crear**.

## <a name="test-connectivity-to-private-endpoint"></a>Prueba de la conectividad con el punto de conexión privado

En esta sección, utilizará la máquina virtual creada en el paso anterior para conectarse al servidor SQL mediante el punto de conexión privado.

1. En el panel de navegación de la izquierda, seleccione **Grupos de recursos**.

2. Seleccione **CreateSQLEndpointTutorial**.

3. Seleccione **myVM**.

4. En la página de información general para **myVM**, seleccione **Conectar** y, luego, **Bastion**.

5. Seleccione el botón **Usar bastión** azul.

6. Especifique el nombre de usuario y la contraseña proporcionados durante la creación de la máquina virtual.

7. Abra Windows PowerShell en el servidor después de conectarse.

8. Escriba `nslookup <sqlserver-name>.database.windows.net`. Reemplace **\<sqlserver-name>** por el nombre del servidor SQL que creó en los pasos anteriores.  Recibirá un mensaje similar al que se muestra a continuación:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.1.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    Se devuelve la dirección IP privada **10.1.0.5** para el nombre del servidor SQL.  Esta dirección se encuentra en la subred de la red virtual que creó anteriormente.


9. Instale [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) en **myVM**.

10. Abra **SQL Server Management Studio**.

4. En **Conectar con el servidor**, escriba o seleccione esta información:

    | Configuración | Value |
    | ------- | ----- |
    | Tipo de servidor | Seleccione **Motor de base de datos**.|
    | Nombre de servidor | Escriba **\<sqlserver-name>.database.windows.net**. |
    | Autenticación | Seleccione **Autenticación de SQL Server**. |
    | Nombre de usuario | Escriba el nombre de usuario que especificó durante la creación del servidor. |
    | Contraseña | Escriba la contraseña que especificó durante la creación del servidor. |
    | Recordar contraseña | Seleccione **Sí**. |

1. Seleccione **Conectar**.
2. Examine las bases de datos en el menú izquierdo.
3. (Opcionalmente) Cree o consulte información de **mysqldatabase**.
4. Cierre la conexión de Escritorio remoto a **myVM**. 

## <a name="clean-up-resources"></a>Limpieza de recursos 
Cuando haya terminado mediante el punto de conexión privado, el servidor SQL y la máquina virtual, elimine el grupo de recursos y todos los recursos que contiene: 
1. Escriba **CreateSQLEndpointTutorial** en el cuadro **Buscar** situado en la parte superior del portal y seleccione **CreateSQLEndpointTutorial** en los resultados de la búsqueda. 
2. Seleccione **Eliminar grupo de recursos**. 
3. Escriba CreateSQLEndpointTutorial en **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS** y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha creado:

* Una red virtual y un host bastión.
* Una máquina virtual.
* Un servidor SQL de Azure con un punto de conexión privado.

Ha utilizado la máquina virtual para probar la conectividad de forma segura al servidor SQL mediante el punto de conexión privado.

Como paso siguiente, puede que también le interese el escenario de la arquitectura de **aplicación web con conectividad privada a Azure SQL Database**, que conecta una aplicación web situada fuera de la red virtual con el punto de conexión privado de una base de datos.
> [!div class="nextstepaction"]
> [Conectividad privada de una aplicación web a Azure SQL Database](/azure/architecture/example-scenario/private-web-app/private-web-app)