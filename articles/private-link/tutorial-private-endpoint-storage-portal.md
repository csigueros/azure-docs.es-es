---
title: 'Tutorial: Conexión a una cuenta de almacenamiento mediante un punto de conexión privado de Azure'
titleSuffix: Azure Private Link
description: Comience a trabajar con este tutorial sobre el uso de un punto de conexión privado de Azure para conectarse a una cuenta de almacenamiento de forma privada.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: 65874a66fd6e3843f7d1a3dc1e7d088d392df645
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2021
ms.locfileid: "114285002"
---
# <a name="tutorial-connect-to-a-storage-account-using-an-azure-private-endpoint"></a>Tutorial: Conexión a una cuenta de almacenamiento mediante un punto de conexión privado de Azure

Un punto de conexión privado de Azure es el bloque de creación fundamental para Private Link en Azure. Permite que los recursos de Azure, como las máquinas virtuales, se comuniquen con recursos de Private Link de manera privada.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una red virtual y un host bastión.
> * Cree una máquina virtual.
> * Crear una cuenta de almacenamiento con un punto de conexión privado.
> * Probar la conectividad con el punto de conexión privado de la cuenta de almacenamiento.

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

## <a name="create-storage-account-with-a-private-endpoint"></a>Creación de una cuenta de almacenamiento con un punto de conexión privado

En esta sección, creará una cuenta de almacenamiento y configurará el punto de conexión privado.

1. En el menú izquierdo, seleccione **Crear un recurso** > **Almacenamiento** > **Cuenta de almacenamiento** o busque **Cuenta de almacenamiento** en el cuadro de búsqueda.

2. En la pestaña **Aspectos básicos** de la página **Crear cuenta de almacenamiento**, escriba o seleccione la información siguiente:

    | Configuración | Value                                          |
    |-----------------------|----------------------------------|
    | **Detalles del proyecto** |  |
    | Suscripción | Selección de su suscripción a Azure |
    | Grupo de recursos | Seleccione **myResourceGroup**. |
    | **Detalles de instancia** |  |
    | Nombre de la cuenta de almacenamiento | Escriba **mystorageaccount**. Si el nombre no está disponible, escriba un nombre único. |
    | Location | Seleccione **Este de EE. UU**. |
    | Rendimiento | Deje el valor predeterminado **Estándar**. |
    | Tipo de cuenta | Deje el valor predeterminado **Storage (uso general v2)** . |
    | Replicación| Deje el valor predeterminado **Almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS)** . |
   
3. Seleccione la pestaña **Redes** o el botón **Siguiente: Redes**.

4. En la pestaña **Redes**, en **Método de conectividad**, seleccione **Punto de conexión privado**.

5. En **Punto de conexión privado**, seleccione **+ Agregar**.

6. En **Crear un punto de conexión privado**, escriba o seleccione la siguiente información:

    | Configuración | Value                                          |
    |-----------------------|----------------------------------|
    | Suscripción | Selección de su suscripción a Azure |
    | Grupo de recursos | Seleccione **myResourceGroup**. |
    | Location | Seleccione **Este de EE. UU**. |
    | Nombre | Escriba **myPrivateEndpoint**. |
    | Subrecurso de almacenamiento | Deje el valor predeterminado **blob**. |
    | **Redes** |  |
    | Virtual network | Seleccione **myVNet**. |
    | Subnet | Seleccione **mySubnet**. |
    | **Integración de DNS privado** |
    | Integración con una zona DNS privada | Deje el valor predeterminado **Sí**. |
    | Zona DNS privada | Deje el valor predeterminado (New) privatelink.blob.core.windows.net. |

7. Seleccione **Aceptar**.

8. Seleccione **Revisar + crear**.

9. Seleccione **Crear**.

10. En el panel de navegación de la izquierda, seleccione **Grupos de recursos**.

11. Seleccione **myResourceGroup**.

12. Seleccione la cuenta de almacenamiento que creó en los pasos previos.

13. En la sección **Configuración** de la cuenta de almacenamiento, seleccione **Claves de acceso**.

14. Seleccione Copiar en **Cadena de conexión** para **key1**.

## <a name="test-connectivity-to-private-endpoint"></a>Prueba de la conectividad con el punto de conexión privado

En esta sección, utilizará la máquina virtual creada en el paso anterior para conectarse a la cuenta de almacenamiento en el punto de conexión privado.

1. En el panel de navegación de la izquierda, seleccione **Grupos de recursos**.

2. Seleccione **myResourceGroup**.

3. Seleccione **myVM**.

4. En la página de información general para **myVM**, seleccione **Conectar** y, luego, **Bastion**.

5. Seleccione el botón **Usar bastión** azul.

6. Especifique el nombre de usuario y la contraseña proporcionados durante la creación de la máquina virtual.

7. Abra Windows PowerShell en el servidor después de conectarse.

8. Escriba `nslookup <storage-account-name>.blob.core.windows.net`. Reemplace **\<storage-account-name>** por el nombre de la cuenta de almacenamiento creada en los pasos anteriores.  Recibirá un mensaje similar al que se muestra a continuación:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mystorageaccount8675.privatelink.blob.core.windows.net
    Address:  10.1.0.5
    Aliases:  mystorageaccount8675.blob.core.windows.net
    ```

    Se devuelve una dirección IP privada de **10.1.0.5** para el nombre de la cuenta de almacenamiento.  Esta dirección se encuentra en la subred de la red virtual que creó anteriormente.

9. Instale el [Explorador de Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) en la máquina virtual.

10. Seleccione **Finalizar** después de instalar el **Explorador de Microsoft Azure Storage**.  Deje activada la casilla para abrir la aplicación.

11. En la pantalla **Conectar a Azure Storage**, seleccione **Usar una cadena de conexión**.

12. Seleccione **Next** (Siguiente).

13. Escriba el nombre de la cuenta de almacenamiento de los pasos anteriores en **Nombre para mostrar**.

14. En el cuadro **Cadena de conexión**, pegue la cadena de conexión de la cuenta de almacenamiento copiada en los pasos anteriores.

15. Seleccione **Next** (Siguiente).

16. Compruebe que la configuración sea correcta en **Resumen de la conexión**.  

17. Seleccione **Conectar**.

18. Cierre la conexión con **myVM**.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine la red virtual, la máquina virtual y la cuenta de almacenamiento mediante los pasos siguientes:

1. En el menú izquierdo, seleccione **Grupos de recursos**.

2. Seleccione **myResourceGroup**.

3. Seleccione **Eliminar grupo de recursos**.

4. Especifique **myResourceGroup** en **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS**.

5. Seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a crear un servicio de Private Link:
> [!div class="nextstepaction"]
> [Creación de un servicio Private Link](create-private-link-service-portal.md)