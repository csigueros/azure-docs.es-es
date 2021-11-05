---
title: 'Tutorial: Creación de un equilibrador de carga de puerta de enlace: Azure Portal'
titleSuffix: Azure Load Balancer
description: Use este tutorial para aprender a crear un equilibrador de carga de puerta de enlace mediante Azure Portal.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: template-tutorial, ignite-fall-2021
ms.openlocfilehash: 918f2da42a57752db2373e65665804d0a49ad31a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092942"
---
# <a name="tutorial-create-a-gateway-load-balancer-using-the-azure-portal"></a>Tutorial: Creación de un equilibrador de carga de puerta de enlace mediante Azure Portal

Azure Load Balancer consta de SKU estándar, básicas y de puerta de enlace. La instancia de Load Balancer de puerta de enlace se usa para la inserción transparente de aplicaciones virtuales de red (NVA). Use la instancia de Load Balancer de puerta de enlace en escenarios que requieran alto rendimiento y alta escalabilidad de NVA.

En este tutorial aprenderá a:

> [!div class="checklist"]
> * Registre la característica en vista previa.
> * Cree una red virtual.
> * Cree un grupo de seguridad de red.
> * Cree un equilibrador de carga de puerta de enlace.
> * Encadene un front-end de equilibrador de carga a un equilibrador de carga de puerta de enlace.

> [!IMPORTANT]
> La instancia de Azure Load Balancer para puerta de enlace se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerrequisitos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Una SKU estándar pública existente en Azure Load Balancer. Para obtener más información sobre cómo crear un equilibrador de carga, consulte **[Creación de un equilibrador de carga público mediante Azure Portal](quickstart-load-balancer-standard-public-portal.md)** .
    - Para los fines detallados en este tutorial, el equilibrador de carga de los ejemplos se denomina **myLoadBalancer**.

## <a name="register-preview-feature"></a>Registrar la característica en vista previa

Como parte de la versión preliminar pública, el proveedor debe estar registrado en su suscripción de Azure. Use los siguientes ejemplos de PowerShell o de la CLI de Azure para habilitar la suscripción.

### <a name="powershell"></a>PowerShell

Para registrar la característica de proveedor **AllowGatewayLoadBalancer**, use [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature):

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowGatewayLoadBalancer

```

Para registrar el proveedor de recursos **Microsoft.Network**, use [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider):

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network

```

### <a name="azure-cli"></a>Azure CLI

Para registrar la característica de proveedor **AllowGatewayLoadBalancer**, use [az feature register](/cli/azure/feature#az_feature_register):

```azurecli-interactive
  az feature register \
    --name AllowGatewayLoadBalancer \
    --namespace Microsoft.Network
```

Para registrar el proveedor de recursos **Microsoft.Network**, use [az provider register](/cli/azure/provider#az_provider_register):

```azurecli-interactive
  az provider register \
    --namespace Microsoft.Network
```

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://preview.portal.azure.com](https://preview.portal.azure.com).

## <a name="create-virtual-network"></a>Creación de una red virtual

Se necesita una red virtual para los recursos que se encuentran en el grupo de back-end del equilibrador de carga de puerta de enlace. 

1. En el cuadro de búsqueda de la parte superior del portal, escriba **Red virtual**. En los resultados de la búsqueda, seleccione **Redes virtuales**.

2. En **Redes virtuales**, seleccione **+ Crear**.

3. En **Crear red virtual**, escriba o seleccione esta información en la pestaña **Conceptos básicos**:

    | **Configuración**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalles del proyecto**  |                                                                 |
    | Suscripción     | Selección de su suscripción a Azure                                  |
    | Grupo de recursos   | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **CasegwLB-rg**. </br> Seleccione **Aceptar**. |
    | **Detalles de instancia** |                                                                 |
    | Nombre             | Escriba **myVNet**.                                    |
    | Region           | Seleccione **Este de EE. UU**. |

4. Seleccione la pestaña **Direcciones IP** o el botón **Siguiente: Direcciones IP** situado en la parte inferior de la página.

5. En la pestaña **Direcciones IP**, especifique esta información:

    | Configuración            | Value                      |
    |--------------------|----------------------------|
    | Espacio de direcciones IPv4 | Escriba **10.1.0.0/16**. |

6. En **Nombre de subred**, seleccione la palabra **predeterminada**.

7. En **Editar subred**, especifique esta información:

    | Configuración            | Value                      |
    |--------------------|----------------------------|
    | Nombre de subred | Escriba **myBackendSubnet**. |
    | Intervalo de direcciones de subred | Escriba **10.1.0.0/24**. |

8. Seleccione **Guardar**.

9. Seleccione la pestaña **Seguridad** .

10. En **BastionHost**, seleccione **Habilitar**. Escriba esta información:

    | Configuración            | Value                      |
    |--------------------|----------------------------|
    | Nombre del bastión | Escriba **myBastionHost**. |
    | Espacio de direcciones de AzureBastionSubnet | Escriba **10.1.1.0/27**. |
    | Dirección IP pública | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **myBastionIP**. </br> Seleccione **Aceptar**. |


11. Seleccione la pestaña **Revisar y crear** o el botón **Revisar y crear**.

12. Seleccione **Crear**.

## <a name="create-nsg"></a>Creación de NSG

Use el ejemplo siguiente para crear un grupo de seguridad de red. Configurará las reglas de NSG necesarias para el tráfico de red en la red virtual creada anteriormente.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Seguridad de red**. En los resultados de la búsqueda, seleccione **Grupos de seguridad de red**.

2. Seleccione **+ Create** (+ Crear).

3. En la pestaña **Conceptos básicos** de la opción **Crear un grupo de seguridad de red**, escriba o seleccione esta información:

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Suscripción | Seleccione su suscripción. |
    | Resource group | Seleccione **TutorGwLB-rg**. |
    | **Detalles de instancia** |   |
    | Nombre | Escriba **myNSG**. |
    | Region | Seleccione **Este de EE. UU**. |

4. Seleccione la pestaña **Revisar y crear** o el botón **Revisar y crear**.

5. Seleccione **Crear**.

6. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Seguridad de red**. En los resultados de la búsqueda, seleccione **Grupos de seguridad de red**.

7. Seleccione **myNSG**.

8. Seleccione **Reglas de seguridad de entrada** en **Configuración** en **myNSG**.

9. Seleccione **+Agregar**.

10. En la ventana **Agregar regla de seguridad de entrada**, especifique o seleccione los siguientes datos.

    | Configuración | Valor |
    | ------- | ----- |
    | Source | Deje el valor predeterminado, **Cualquiera**. |
    | Source port ranges | Deje el valor predeterminado de **"*"** . |
    | Destination | Deje el valor predeterminado, **Cualquiera**. |
    | Servicio | Deje el valor predeterminado, **Personalizado**. |
    | Intervalos de puertos de destino | Escriba **"*"** . |
    | Protocolo | Seleccione **Cualquiera**. |
    | Acción | Deje el valor predeterminado, **Permitir**. |
    | Priority | Escriba **100**. | 
    | Nombre | Escriba **myNSGRule-AllowAll-All**. |

11. Seleccione **Agregar**.

12. En **Configuración**, seleccione **Reglas de seguridad de salida**.

13. Seleccione **+Agregar**.

14. En **Agregar regla de seguridad de entrada**, especifique o seleccione la siguiente información.

    | Configuración | Valor |
    | ------- | ----- |
    | Source | Deje el valor predeterminado, **Cualquiera**. |
    | Source port ranges | Deje el valor predeterminado de **"*"** . |
    | Destination | Deje el valor predeterminado, **Cualquiera**. |
    | Servicio | Deje el valor predeterminado, **Personalizado**. |
    | Intervalos de puertos de destino | Escriba **"*"** . |
    | Protocolo | seleccione **TCP**. |
    | Acción | Deje el valor predeterminado, **Permitir**. |
    | Priority | Escriba **100**. | 
    | Nombre | Escriba **myNSGRule-AllowAll-TCP-Out**. |

15. Seleccione **Agregar**.

Seleccione este NSG al crear las NVA para la implementación.

## <a name="create-gateway-load-balancer"></a>Creación de un equilibrador de carga de puerta de enlace

En esta sección, creará la configuración e implementará el equilibrador de carga de puerta de enlace. 

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **Load Balancer**. Seleccione **Equilibradores de carga** en los resultados de la búsqueda.

2. En la página **Equilibrador de carga**, seleccione **Crear**.

3. En la pestaña **Conceptos básicos** de la página **Crear equilibrador de carga**, escriba o seleccione la siguiente información: 

    | Configuración                 | Value                                              |
    | ---                     | ---                                                |
    | **Detalles del proyecto** |   |
    | Suscripción               | Seleccione su suscripción.    |    
    | Resource group         | Seleccione **TutorGwLB-rg**. |
    | **Detalles de instancia** |   |
    | Nombre                   | Escriba **myLoadBalancer-gw**.                                   |
    | Region         | Seleccione **(EE. UU.) Este de EE. UU.** .                                        |
    | Tipo          | seleccione **Interno**.                                        |
    | SKU           | Seleccione **Puerta de enlace**. |

    :::image type="content" source="./media/tutorial-gateway-portal/create-load-balancer.png" alt-text="Captura de pantalla de la pestaña de conceptos básicos del equilibrador de carga estándar." border="true":::

4. Seleccione **Siguiente: Configuración de IP de front-end** en la parte inferior de la página.

5. En **Configuración de IP de front-end**, seleccione **+ Agregar una IP de front-end**.

6. Escriba **MyFrontEnd** en **Nombre**.

7. Seleccione **myBackendSubnet** en **Subred**.

8. Seleccione **Dinámica** para **Asignación**.

9. Seleccione **Agregar**.

10. Seleccione **Siguiente: Grupos de back-end** en la parte inferior de la página.

11. En la pestaña **Grupos de back-end**, seleccione **+ Agregar un grupo de back-end**.

12. En **Agregar grupo back-end**, escriba o seleccione la siguiente información.

    | Configuración | Valor |
    | ------- | ----- |
    | Nombre | Escriba **myBackendPool**. |
    | Configuración del grupo de back-end | Seleccione **NIC**. |
    | Versión de la dirección IP | Seleccione **IPv4**. |
    | **Configuración del equilibrador de carga de puerta de enlace** |   |
    | Tipo | Seleccione **Interno y externo**. |
    | Puerto interno | Deje el valor predeterminado de **10800**. |
    | Identificador interno | Deje el valor predeterminado de **800**. |
    | Puerto externo | Deje el valor predeterminado de **10801**. |
    | Identificador externo | Deje el valor predeterminado de **801**. |

13. Seleccione **Agregar**.

14. Seleccione el botón **Siguiente: Reglas de entrada** situado en la parte inferior de la página.

15. En **Regla de equilibrio de carga** de la pestaña **Reglas de entrada**, seleccione **+ Agregar regla de equilibrio de carga**.

16. En **Agregar regla de equilibrio de carga**, escriba o seleccione la siguiente información:

    | Configuración | Valor |
    | ------- | ----- |
    | Nombre | Escriba **myLBRule**. |
    | Versión de la dirección IP | Seleccione **IPv4** o **IPv6** en función de sus requisitos. |
    | Dirección IP del front-end | Seleccione **MyFrontend**. |
    | Grupo back-end | Seleccione **MyBackendPool**. |
    | Sondeo de mantenimiento | Seleccione **Crear nuevo**. </br> En **Nombre**, escriba **myHealthProbe**. </br> Seleccione **HTTP** en **Protocolo**. </br> Deje el resto de los valores predeterminados y seleccione **Aceptar**. |
    | Persistencia de la sesión | Seleccione **Ninguno**. |

    :::image type="content" source="./media/tutorial-gateway-portal/add-load-balancing-rule.png" alt-text="Captura de pantalla de cómo se crea una regla de equilibrio de carga." border="true":::

17. Seleccione **Agregar**.

18. Seleccione el botón azul **Revisar y crear** en la parte inferior de la página.

19. Seleccione **Crear**.

## <a name="add-network-virtual-appliances-to-the-gateway-load-balancer-backend-pool"></a>Adición de aplicaciones virtuales de red al grupo de back-end del equilibrar la carga de la puerta de enlace
Implemente las NVA a través de Azure Marketplace. Una vez implementadas, agregue las máquinas virtuales de NVA al grupo de back-end; para ello, vaya a la pestaña Grupos de back-end del equilibrador de carga de puerta de enlace.

## <a name="chain-load-balancer-frontend-to-gateway-load-balancer"></a>Encadene un front-end del equilibrador de carga a un equilibrador de carga de puerta de enlace.

En este ejemplo, encadenará el front-end de un equilibrador de carga estándar al equilibrador de carga de puerta de enlace. 

Asimismo, agregará el front-end a la dirección IP de front-end de un equilibrador de carga existente en la suscripción.

1. En el cuadro de búsqueda de Azure Portal, escriba **Equilibrador de carga**. En los resultados de la búsqueda, seleccione **Equilibradores de carga**.

2. En **Equilibradores de carga** , seleccione **myLoadBalancer** o el nombre de su equilibrador de carga existente.

3. En la página del equilibrador de carga, seleccione **Configuración de IP de front-end** en **Configuración**.

4. Seleccione la dirección IP de front-end del equilibrador de carga. En este ejemplo, el nombre del front-end es **myFrontendIP**.

    :::image type="content" source="./media/tutorial-gateway-portal/frontend-ip.png" alt-text="Captura de pantalla de la configuración de la dirección de IP de front-end." border="true":::

5. Seleccione **myFrontendIP (10.1.0.4)** en el cuadro desplegable situado junto al **Equilibrador de carga de puerta de enlace**.

6. Seleccione **Guardar**.

    :::image type="content" source="./media/tutorial-gateway-portal/select-gateway-load-balancer.png" alt-text="Captura de pantalla para agregar el equilibrador de carga de puerta de enlace a la dirección IP de front-end." border="true":::


## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando no los necesite, elimine el grupo de recursos, el equilibrador de carga y todos los recursos relacionados. Para ello, seleccione el grupo de recursos **TutorGwLB-rg**, que contiene los recursos y, a continuación, seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

Cree aplicaciones virtuales de red en Azure. 

Al crear las aplicaciones virtuales de red, elija los recursos creados en este tutorial:

* Virtual network

* Subnet

* Grupo de seguridad de red

* Equilibrador de carga de puerta de enlace

Pase al siguiente artículo para aprender a crear una instancia de Azure Load Balancer entre regiones.
> [!div class="nextstepaction"]
> [Equilibrador de carga entre regiones](tutorial-cross-region-powershell.md)
