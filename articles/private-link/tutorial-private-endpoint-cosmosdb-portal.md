---
title: 'Tutorial: Conexión a una cuenta de Azure Cosmos mediante un punto de conexión privado de Azure'
titleSuffix: Azure Private Link
description: Comience a trabajar con este tutorial sobre el uso de un punto de conexión privado de Azure para conectarse a una cuenta de Azure Cosmos de forma privada.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: c7c9caf5bbfd842c635b0cb887540457f15c081f
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2021
ms.locfileid: "114297044"
---
# <a name="tutorial-connect-to-an-azure-cosmos-account-using-an-azure-private-endpoint"></a>Tutorial: Conexión a una cuenta de Azure Cosmos mediante un punto de conexión privado de Azure

Un punto de conexión privado de Azure es el bloque de creación fundamental para Private Link en Azure. Permite que los recursos de Azure, como las máquinas virtuales, se comuniquen con recursos de Private Link de manera privada.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una red virtual y un host bastión.
> * Cree una máquina virtual.
> * Cree una cuenta de Cosmos DB con un punto de conexión privado.
> * Pruebe la conectividad con el punto de conexión privado de la cuenta de Cosmos DB.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-network-and-bastion-host"></a>Creación de una red virtual y un host bastión

En esta sección, creará una red virtual, una subred y un host bastión. 

El host bastión se utilizará para conectarse de forma segura a la máquina virtual a fin de probar el punto de conexión privado.

1. En la parte superior izquierda de la pantalla, seleccione **Crear un recurso > Redes > Red virtual** o busque **Red virtual** en el cuadro de búsqueda.

2. En **Crear red virtual**, escriba o seleccione esta información en la pestaña **Conceptos básicos**:

    | **Configuración**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalles del proyecto**  |                                                                 |
    | Suscripción     | Selección de su suscripción a Azure                                  |
    | Grupo de recursos   | Seleccione **myResourceGroup**. |
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
    | Grupo de recursos | Seleccione **myResourceGroup**. |
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

## <a name="create-a-cosmos-db-account-with-a-private-endpoint"></a>Creación de una cuenta de Cosmos DB con un punto de conexión privado

En esta sección creará una cuenta de Cosmos DB y configurará el punto de conexión privado.

1. En el menú izquierdo, seleccione **Crear un recurso** > **Bases de datos** > **Cuenta de Cosmos DB** o busque **Cuenta de Cosmos DB** en el cuadro de búsqueda.

2. En la pestaña **Aspectos básicos** de la página **Crear cuenta de Cosmos DB**, escriba o seleccione la información siguiente:

    | Parámetro | Value                                          |
    |-----------------------|----------------------------------|
    | **Detalles del proyecto** |  |
    | Suscripción | Seleccione su suscripción a Azure. |
    | Grupo de recursos | Seleccione **myResourceGroup**. |
    | **Detalles de instancia** |  |
    | Nombre de la cuenta | Escriba **mycosmosdb**. Si el nombre no está disponible, escriba un nombre único. |
    | API | Seleccione **Core (SQL)**. |
    | Location | Seleccione **Este de EE. UU**. |
    | Capacity mode (Modo de capacidad) | Deje el valor **Provisioned throughput** (Rendimiento aprovisionado) predeterminado. |
    | Aplicación de descuento por nivel Gratis | Deje el valor **No aplicar** predeterminado. |
    | Redundancia geográfica | Deje el valor **Deshabilitado** predeterminado. |
    | Escrituras en varias regiones | Deje el valor **Deshabilitado** predeterminado. |
   
3. Seleccione la pestaña **Redes** o el botón **Siguiente: Redes**.

4. En la pestaña **Redes**, escriba o seleccione la siguiente información:

    | Parámetro | Valor |
    | ------- | ----- |
    | **Conectividad de red** | |
    | Método de conectividad | Seleccione **Punto de conexión privado**. |
    | **Configurar firewall** | |
    | Allow access from the Azure portal (Permitir acceso desde Azure Portal) | Deje el valor **Permitir** predeterminado. |
    | Permitir el acceso desde mi dirección IP | Deje el valor **Denegar** predeterminado. |

5. En **Punto de conexión privado**, seleccione **+ Agregar**.

6. En **Crear un punto de conexión privado**, escriba o seleccione la siguiente información:

    | Configuración | Value                                          |
    |-----------------------|----------------------------------|
    | Suscripción | Selección de su suscripción a Azure |
    | Grupo de recursos | Seleccione **myResourceGroup**. |
    | Location | Seleccione **Este de EE. UU**. |
    | Nombre | Escriba **myPrivateEndpoint**. |
    | Subrecurso de destino | Deje el valor **Core (SQL)** predeterminado. |
    | **Redes** |  |
    | Virtual network | Seleccione **myVNet**. |
    | Subnet | Seleccione **mySubnet**. |
    | **Integración de DNS privado** |
    | Integración con una zona DNS privada | Deje el valor predeterminado **Sí**. |
    | Zona DNS privada | Deje el valor (New) privatelink.documents.azure.com ([Nuevo] privatelink.documents.azure.com) predeterminado. |

7. Seleccione **Aceptar**.

8. Seleccione **Revisar + crear**.

9. Seleccione **Crear**.

### <a name="add-a-database-and-a-container"></a>Adición de una base de datos y un contenedor

1. Seleccione **Ir al recurso** o, en el menú de la izquierda de Azure Portal, seleccione **Todos los recursos** > **mycosmosdb**.

2. En el menú de la izquierda, seleccione **Explorador de datos**.

3. En la ventana **Explorador de datos**, seleccione **Nuevo contenedor**.

4. En **Agregar contenedor**, escriba o seleccione la siguiente información:

    | Parámetro | Value |
    | ------- | ----- |
    | Identificador de base de datos | Deje el valor **Crear nuevo** predeterminado. </br> Escriba **mydatabaseid** en el cuadro de texto. |
    | Rendimiento (400-100 000 RU/s) | Deje el valor **Manual** predeterminado. </br> Escriba **400** en el cuadro de texto. |
    | Id. de contenedor | Escriba **mycontainerid**. |
    | Clave de partición | Escriba **/mykey**. |

5. Seleccione **Aceptar**.

6. En la sección **Configuración** de la cuenta de Cosmos DB, seleccione **Claves**.

7. Seleccione copiar en la **CADENA DE CONEXIÓN PRINCIPAL**.

## <a name="test-connectivity-to-private-endpoint"></a>Prueba de la conectividad con el punto de conexión privado

En esta sección utilizará la máquina virtual creada en el paso anterior para conectarse a la cuenta de Cosmos DB mediante el punto de conexión privado.

1. En el panel de navegación de la izquierda, seleccione **Grupos de recursos**.

1. Seleccione **myResourceGroup**.

1. Seleccione **myVM**.

1. En la página de información general para **myVM**, seleccione **Conectar** y, luego, **Bastion**.

1. Seleccione el botón **Usar bastión** azul.

1. Especifique el nombre de usuario y la contraseña proporcionados durante la creación de la máquina virtual.

1. Abra Windows PowerShell en el servidor después de conectarse.

1. Escriba `nslookup <cosmosdb-account-name>.documents.azure.com` y valide la resolución de nombres. Reemplace **\<cosmosdb-account-name>** por el nombre de la cuenta de Cosmos DB que creó en los pasos anteriores. 

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mycosmosdb8675.privatelink.documents.azure.com
    Address:  10.1.0.5
    Aliases:  mycosmosdb8675.documents.azure.com
    ```
    Se devuelve una dirección IP privada de **10.1.0.5** para el nombre de la cuenta de Cosmos DB.  Esta dirección se encuentra en la subred de la red virtual que creó anteriormente.
    
1. Obtenga la cadena de conexión principal de Azure Cosmos DB en el portal. Una cadena de conexión válida tiene el siguiente formato:
   
   Para cuentas de la API de SQL: `https://<accountName>.documents.azure.com:443/;AccountKey=<accountKey>;` Para Azure Cosmos DB API para MongoDB: `mongodb://<accountName>:<accountKey>@cdbmongo36.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false`

1. Instale el [Explorador de Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) en la máquina virtual.

1. Seleccione **Finalizar** después de instalar el **Explorador de Microsoft Azure Storage**.  Deje activada la casilla para abrir la aplicación.

1. En la pantalla **Conectar a Azure Storage**, seleccione **Cancelar**.

1. En el Explorador de Storage, seleccione con el botón derecho **Cuentas de Cosmos DB** y seleccione **Conectarse a Cosmos DB**.

1. En **Seleccionar API**, deje el valor **SQL** predeterminado.

1. En el cuadro de **Cadena de conexión**, pegue la cadena de conexión de la cuenta de Cosmos DB copiada en los pasos anteriores.

1. Seleccione **Next** (Siguiente).

1. Compruebe que la configuración sea correcta en **Resumen de la conexión**.  

1. Seleccione **Conectar**.

1. Cierre la conexión con **myVM**.


## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine la red virtual, la máquina virtual y la cuenta de Cosmos DB mediante los pasos siguientes:

1. En el menú izquierdo, seleccione **Grupos de recursos**.

2. Seleccione **myResourceGroup**.

3. Seleccione **Eliminar grupo de recursos**.

4. Especifique **myResourceGroup** en **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS**.

5. Seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha creado:

* Una red virtual y un host bastión.
* Una máquina virtual.
* Una cuenta de Cosmos DB.

Aprenda a crear un servicio de Private Link:
> [!div class="nextstepaction"]
> [Creación de un servicio Private Link](create-private-link-service-portal.md)
