---
title: 'Tutorial: Conexión a una aplicación web mediante un punto de conexión privado de Azure'
titleSuffix: Azure Private Link
description: Comience a trabajar con este tutorial sobre el uso de un punto de conexión privado de Azure para conectarse a una aplicación web de forma privada.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 10/19/2020
ms.openlocfilehash: 67c4b96ebb9e578e6a617b37e5a05084e72c4f56
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2021
ms.locfileid: "114285071"
---
# <a name="tutorial-connect-to-a-web-app-using-an-azure-private-endpoint"></a>Tutorial: Conexión a una aplicación web mediante un punto de conexión privado de Azure

Un punto de conexión privado de Azure es el bloque de creación fundamental para Private Link en Azure. Permite que los recursos de Azure, como las máquinas virtuales, se comuniquen con recursos de Private Link de manera privada.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una red virtual y un host bastión.
> * Cree una máquina virtual.
> * Crear una aplicación web.
> * Crear un punto de conexión privado.
> * Probar la conectividad con el punto de conexión privado de la aplicación web.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

> [!Note]
> El punto de conexión privado está disponible en regiones públicas para las aplicaciones web de Windows de nivel PremiumV2, PremiumV3, las aplicaciones web de Linux y el plan de Azure Functions Premium (a veces llamado plan Elástico Premium). 

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
    | Region           | Seleccione **Oeste de Europa**. |

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

## <a name="create-web-app"></a>Crear una aplicación web

En esta sección, creará una aplicación web.

1. En el menú izquierdo, seleccione **Crear un recurso** > **Almacenamiento** > **Aplicación web** o busque **Aplicación web** en el cuadro de búsqueda.

2. En la pestaña **Básico** de **Crear aplicación web**, escriba o seleccione la información siguiente:

    | Configuración | Value                                          |
    |-----------------------|----------------------------------|
    | **Detalles del proyecto** |  |
    | Suscripción | Selección de su suscripción a Azure |
    | Grupo de recursos | Seleccione **myResourceGroup**. |
    | **Detalles de instancia** |  |
    | Nombre | Escriba **mywebapp**. Si el nombre no está disponible, escriba un nombre único. |
    | Publicar | Seleccione **Código**. |
    | Pila en tiempo de ejecución | Seleccione **.NET Core 3.1 (LTS)** . |
    | Sistema operativo | Seleccione **Windows**. |
    | Region | Seleccione **Oeste de Europa**. |
    | **Plan de App Service** |  |
    | Plan de Windows (Oeste de Europa) | Seleccione **Crear nuevo**. </br> Escriba **myServicePlan** en **Nombre**. |
    | SKU y tamaño | Seleccione **Cambiar el tamaño**. </br> Seleccione **P2V2** en la pantalla **Selector de especificaciones**. </br> Seleccione **Aplicar**. |
   
3. Seleccione **Revisar + crear**.

4. Seleccione **Crear**.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/create-web-app.png" alt-text="Pestaña de conceptos básicos de la creación de una aplicación web en Azure Portal." border="true":::

## <a name="create-private-endpoint"></a>Creación de un punto de conexión privado

1. En el menú de la izquierda, seleccione **Todos los recursos** > **mywebapp** o el nombre que eligió durante la creación.

2. En la información general de la aplicación web, seleccione **Configuración** > **Redes**.

3. En **Redes**, seleccione **Configurar las conexiones de punto de conexión privado**.

4. En la pantalla **Conexiones de punto de conexión privado**, seleccione **+ Agregar**.

5. En la pantalla **Agregar punto de conexión privado**, escriba o seleccione la siguiente información:

    | Configuración | Value |
    | ------- | ----- |
    | Nombre | Escriba **mywebappendpoint**. |
    | Subscription | Seleccione su suscripción. |
    | Virtual network | Seleccione **myVNet**. |
    | Subnet | Seleccione **mySubnet**. |
    | Integración con una zona DNS privada | Seleccione **Sí**. |

6. Seleccione **Aceptar**.
    

## <a name="test-connectivity-to-private-endpoint"></a>Prueba de la conectividad con el punto de conexión privado

En esta sección, utilizará la máquina virtual creada en el paso anterior para conectarse a la aplicación web mediante el punto de conexión privado.

1. En el panel de navegación de la izquierda, seleccione **Grupos de recursos**.

2. Seleccione **myResourceGroup**.

3. Seleccione **myVM**.

4. En la página de información general para **myVM**, seleccione **Conectar** y, luego, **Bastion**.

5. Seleccione el botón **Usar bastión** azul.

6. Especifique el nombre de usuario y la contraseña proporcionados durante la creación de la máquina virtual.

7. Abra Windows PowerShell en el servidor después de conectarse.

8. Escriba `nslookup <webapp-name>.azurewebsites.net`. Reemplace **\<webapp-name>** por el nombre de la aplicación web que creó en los pasos anteriores.  Recibirá un mensaje similar al que se muestra a continuación:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Se devuelve la dirección IP privada **10.1.0.5** para el nombre de la aplicación web.  Esta dirección se encuentra en la subred de la red virtual que creó anteriormente.

9. Abra un explorador web en el equipo local y escriba la dirección URL externa de la aplicación web, **https://\<webapp-name>.azurewebsites.net**.

10. Compruebe que recibe una página **403**. Esta página indica que la aplicación web no es accesible externamente.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-ext-403.png" alt-text="Página 403 para la dirección externa de la aplicación web." border="true":::

11. En la conexión bastión a **myVM**, abra Internet Explorer.

12. Escriba la dirección URL de la aplicación web, **https://\<webapp-name>.azurewebsites.net**.

13. Compruebe que recibe la página predeterminada de la aplicación web.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-default-page.png" alt-text="Página predeterminada de la aplicación web." border="true":::

18. Cierre la conexión con **myVM**.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine la red virtual, la máquina virtual y la aplicación web mediante los pasos siguientes:

1. En el menú izquierdo, seleccione **Grupos de recursos**.

2. Seleccione **myResourceGroup**.

3. Seleccione **Eliminar grupo de recursos**.

4. Especifique **myResourceGroup** en **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS**.

5. Seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a crear un servicio de Private Link:
> [!div class="nextstepaction"]
> [Creación de un servicio Private Link](create-private-link-service-portal.md)
