---
title: 'Inicio rápido: Creación de un punto de conexión privado mediante Azure Portal'
description: Use este inicio rápido para aprender a crear un punto de conexión privado mediante Azure Portal.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: allensu
ms.openlocfilehash: 02d9ca3ba6a16e06c4357a46a050f8803147aaaf
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2021
ms.locfileid: "114292880"
---
# <a name="quickstart-create-a-private-endpoint-using-the-azure-portal"></a>Inicio rápido: Creación de un punto de conexión privado mediante Azure Portal

Comience a trabajar con Azure Private Link usando un punto de conexión privado para conectarse de forma segura a una aplicación web de Azure.

En este inicio rápido, creará un punto de conexión privado para una aplicación web de Azure e implementará una máquina virtual para probar la conexión privada.  

Se pueden crear puntos de conexión privados para distintos tipos de servicios de Azure, como Azure SQL y Azure Storage.

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Una aplicación web de Azure con un plan de servicio de aplicaciones de **nivel PremiumV2** o superior implementado en la suscripción de Azure.  
    * Para más información y ver un ejemplo, consulte [Inicio rápido: Creación de una aplicación web ASP.NET Core en Azure](../app-service/quickstart-dotnetcore.md). 
    * Para ver un tutorial detallado sobre cómo crear una aplicación web y un punto de conexión, consulte [Tutorial: Conexión a una aplicación web mediante un punto de conexión privado de Azure](tutorial-private-endpoint-webapp-portal.md).

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
    | Grupo de recursos   | Seleccione **CreatePrivateEndpointQS-rg**. |
    | **Detalles de instancia** |                                                                 |
    | Nombre             | Escriba **myVNet**.                                    |
    | Region           | Seleccione **Oeste de Europa**.|

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
    | Grupo de recursos | Seleccione **CreatePrivateEndpointQS-rg**. |
    | **Detalles de instancia** |  |
    | Nombre de la máquina virtual | Escriba **myVM**. |
    | Region | Seleccione **Oeste de Europa**. |
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

## <a name="create-a-private-endpoint"></a>Creación de un punto de conexión privado

En esta sección, creará un punto de conexión privado para la aplicación web que creó en la sección de requisitos previos.

1. En la parte superior izquierda de la pantalla en el portal, seleccione **Crear un recurso** > **Redes** > **Private Link**, o bien, en el cuadro de búsqueda, escriba **Private Link**.

2. Seleccione **Crear**.

3. En **Private Link Center**, seleccione **Private endpoints** (Puntos de conexión privados) en el menú izquierdo.

4. En **Private endpoints** (Puntos de conexión privados), select **+ Add** (+ Agregar).

5. En la pestaña **Aspectos básicos** de **Crear un punto de conexión privado**, escriba o seleccione esta información:

    | Parámetro | Value |
    | ------- | ----- |
    | **Detalles del proyecto** | |
    | Suscripción | Seleccione su suscripción. |
    | Resource group | Seleccione **CreatePrivateEndpointQS-rg**. Ha creado este grupo de recursos en la sección anterior.|
    | **Detalles de instancia** |  |
    | Nombre  | Escriba **myPrivateEndpoint**. |
    | Region | Seleccione **Oeste de Europa**. |

6. Seleccione la pestaña **Recurso** o el botón **Siguiente: Recurso** en la parte inferior de la página.
    
7. En **Recurso**, escriba o seleccione esta información:

    | Parámetro | Value |
    | ------- | ----- |
    | Método de conexión | Seleccione **Conectarse a un recurso de Azure en mi directorio**. |
    | Suscripción | Seleccione su suscripción. |
    | Tipo de recurso | Seleccione **Microsoft.Web/sites**. |
    | Recurso | Seleccione **\<your-web-app-name>**. </br> Seleccione el nombre de la aplicación web que creó en los requisitos previos. |
    | Recurso secundario de destino | Seleccione **sitios**. |

8. Seleccione la pestaña **Configuración** o el botón **Siguiente: Configuración** situado en la parte inferior de la pantalla.

9. En **Configuración**, escriba o seleccione esta información:

    | Configuración | Value |
    | ------- | ----- |
    | **Redes** |  |
    | Virtual network | Seleccione **myVNet**. |
    | Subnet | Seleccione **mySubnet**. |
    | **Integración de DNS privado** |  |
    | Integración con una zona DNS privada | Deje el valor predeterminado de **Sí**. |
    | Subscription | Seleccione su suscripción. |
    | Zonas DNS privadas | Deje el valor predeterminado de **(Nuevo) privatelink.azurewebsites.net**.
    

13. Seleccione **Revisar + crear**.

14. Seleccione **Crear**.

## <a name="test-connectivity-to-private-endpoint"></a>Prueba de la conectividad con el punto de conexión privado

En esta sección, utilizará la máquina virtual creada en el paso anterior para conectarse a la aplicación web mediante el punto de conexión privado.

1. En el panel de navegación de la izquierda, seleccione **Grupos de recursos**.

2. Seleccione **CreatePrivateEndpointQS-rg**.

3. Seleccione **myVM**.

4. En la página de información general para **myVM**, seleccione **Conectar** y, luego, **Bastion**.

5. Seleccione el botón **Usar bastión** azul.

6. Especifique el nombre de usuario y la contraseña proporcionados durante la creación de la máquina virtual.

7. Abra Windows PowerShell en el servidor después de conectarse.

8. Escriba `nslookup <your-webapp-name>.azurewebsites.net`. Reemplace **\<your-webapp-name>** por el nombre de la aplicación web que creó en los pasos anteriores.  Recibirá un mensaje similar al que se muestra a continuación:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Se devuelve la dirección IP privada **10.1.0.5** para el nombre de la aplicación web.  Esta dirección se encuentra en la subred de la red virtual que creó anteriormente.

11. En la conexión bastión a **myVM**, abra Internet Explorer.

12. Escriba la dirección URL de la aplicación web, **https://\<your-webapp-name>.azurewebsites.net**.

13. Recibirá la página de aplicación web predeterminada si no se ha implementado la aplicación:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Página de aplicación web predeterminada" border="true":::

18. Cierre la conexión con **myVM**.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine la red virtual, la máquina virtual y la aplicación web mediante los pasos siguientes:

1. En el menú izquierdo, seleccione **Grupos de recursos**.

2. Seleccione **CreatePrivateEndpointQS-rg**.

3. Seleccione **Eliminar grupo de recursos**.

4. Escriba **CreatePrivateEndpointQS-rg** en **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS**.

5. Seleccione **Eliminar**.


## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha creado lo siguiente:

* Una red virtual y un host bastión.
* Una máquina virtual.
* Un punto de conexión privado para una aplicación web de Azure.

Usó la máquina virtual para probar la conectividad de forma segura a la aplicación web a través del punto de conexión privado.



Para más información sobre los servicios que admiten un punto de conexión privado, consulte:
> [!div class="nextstepaction"]
> [Disponibilidad de Private Link](private-link-overview.md#availability)
