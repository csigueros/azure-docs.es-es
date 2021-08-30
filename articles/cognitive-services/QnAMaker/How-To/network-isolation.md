---
title: Aislamiento de red
description: Los usuarios pueden restringir el acceso público a los recursos de QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/13/2021
ms.openlocfilehash: 91bfa04c9fab179cf3935e703f13004f3aadd170
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114220134"
---
# <a name="recommended-settings-for-network-isolation"></a>Configuración recomendada para el aislamiento de red

Siga los pasos siguientes para restringir el acceso público a los recursos de QnA Maker. Proteja un recurso de Cognitive Services del acceso público mediante la [configuración de la red virtual](../../cognitive-services-virtual-networks.md?tabs=portal).

## <a name="restrict-access-to-app-service-qna-runtime"></a>Restricción del acceso a App Service (entorno de ejecución de QnA)

# <a name="qna-maker-ga-stable-release"></a>[Disponibilidad general de QnA Maker (versión estable)](#tab/v1)

Puede usar ServiceTag `CognitiveServicesMangement` para restringir el acceso entrante a reglas de entrada de grupos de seguridad de red (App Service Environment) de App Service o ASE. Consulte más información sobre etiquetas de servicio en el  [artículo Etiquetas de servicio de red virtual](../../../virtual-network/service-tags-overview.md). 

### <a name="regular-app-service"></a>App Service normal

1. Abra Cloud Shell (PowerShell) desde Azure Portal.
2. Ejecute el siguiente comando en la ventana de PowerShell en la parte inferior de la página:

```ps
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "<resource group name>" -WebAppName "<app service name>" -Name "cognitive services Tag" -Priority 100 -Action Allow -ServiceTag "CognitiveServicesManagement" 
```
3.  Compruebe que la regla de acceso agregada está presente en la sección **Restricciones de acceso** de la pestaña **Redes**:  

    > [!div class="mx-imgBorder"]
    > [ ![Captura de pantalla de la regla de restricción de acceso]( ../media/network-isolation/access-restrictions.png) ](  ../media/network-isolation/access-restrictions.png#lightbox)

4. Para acceder al **panel de prueba** en el portal https://qnamaker.ai, agregue la **dirección IP pública de la máquina** desde la que desea acceder al portal. En la página **Restricciones de acceso**, seleccione **Agregar regla** y permita el acceso a su dirección IP de cliente. 

    > [!div class="mx-imgBorder"]
    > [ ![Captura de pantalla de la regla de restricción de acceso con la adición de la dirección IP pública]( ../media/network-isolation/public-address.png) ](  ../media/network-isolation/public-address.png#lightbox)

### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>Configuración de App Service Environment para hospedar una instancia de App Service de QnA Maker

App Service Environment (ASE) se puede usar para hospedar la instancia de App Service para QnA Maker. Siga estos pasos:

1. Cree un [nuevo recurso de Azure Cognitive Search](https://ms.portal.azure.com/#create/Microsoft.Search).
2. Cree un ASE externo con App Service.
    - Siga este [inicio rápido de App Service](../../../app-service/environment/create-external-ase.md#create-an-ase-and-an-app-service-plan-together) para obtener instrucciones. Este proceso puede tardar hasta 1-2 horas.
    - Por último, tendrá un punto de conexión de App Service que será similar a: `https://<app service name>.<ASE name>.p.azurewebsite.net`. 
    - Ejemplo: `https:// mywebsite.myase.p.azurewebsite.net`  
3. Agregue las siguientes configuraciones de App Service:
    
    | Nombre                       | Valor                                                     |
    |:---------------------------|:----------------------------------------------------------| 
    | PrimaryEndpointKey         | `<app service name>-PrimaryEndpointKey`                   | 
    | AzureSearchName            | `<Azure Cognitive Search Resource Name from step #1>`     | 
    | AzureSearchAdminKey        | `<Azure Cognitive Search Resource admin Key from step #1>`| 
    | QNAMAKER_EXTENSION_VERSION | `latest`                                                  |
    | DefaultAnswer              | `no answer found`                                         |

4. Agregue el origen de CORS "*" en App Service para permitir el acceso al panel de prueba del portal https://qnamaker.ai. **CORS** se encuentra en el encabezado de API del panel App Service.

    > [!div class="mx-imgBorder"]
    > [ ![Captura de pantalla de la interfaz de CORS dentro de la interfaz de usuario de App Service]( ../media/network-isolation/cross-orgin-resource-sharing.png) ](  ../media/network-isolation/cross-orgin-resource-sharing.png#lightbox)

5. Cree una instancia de Cognitive Services de QnA Maker (Microsoft.CognitiveServices/accounts) mediante Azure Resource Manager. El punto de conexión de QnA Maker debe establecerse en el punto de conexión de App Service creado anteriormente (`https:// mywebsite.myase.p.azurewebsite.net`). Esta es una [plantilla de Azure Resource Manager de ejemplo que puede usar como referencia](https://github.com/pchoudhari/QnAMakerBackupRestore/tree/master/QnAMakerASEArmTemplate).

### <a name="related-questions"></a>Preguntas relacionadas

#### <a name="can-qna-maker-be-deployed-to-an-internal-ase"></a>¿Puede implementarse QnA Maker en un ASE interno? 

La razón principal para usar un ASE externo es que el back-end del servicio QnAMaker (creación de API) puede llegar a App Service a través de Internet. Sin embargo, todavía puede protegerlo agregando restricciones de acceso de entrada para permitir solo conexiones desde direcciones asociadas a la etiqueta de servicio `CognitiveServicesManagement`.

Si todavía quiere usar un ASE interno, debe exponer esa aplicación de QnA Maker específica en el ASE en un dominio público a través del certificado TLS/SSL DNS de App Gateway. Para obtener más información, consulte este [artículo sobre la implementación Enterprise de App Services](/azure/architecture/reference-architectures/enterprise-integration/ase-standard-deployment).

    
# <a name="custom-question-answering-preview-release"></a>[Respuesta a preguntas personalizada (versión preliminar)](#tab/v2)

App Service no se implementa con la característica de respuesta a preguntas personalizada.

---

## <a name="restrict-access-to-cognitive-search-resource"></a>Restricción del acceso a los recursos de Cognitive Search

# <a name="qna-maker-ga-stable-release"></a>[Disponibilidad general de QnA Maker (versión estable)](#tab/v1)

La instancia de Cognitive Search se puede aislar a través de un punto de conexión privado después de haber creado los recursos de QnA Maker. Siga estos pasos para bloquear el acceso:

1. Cree una nueva [red virtual (VNet)](https://portal.azure.com/#create/Microsoft.VirtualNetwork-ARM) o use la red virtual existente de ASE (App Service Environment).
2. Abra el recurso de red virtual y, en la pestaña **Subredes**, cree dos subredes. Una para App Service **(appservicesubnet)** y otra subred **(searchservicesubnet)** para el recurso de Cognitive Search sin delegación. 

    > [!div class="mx-imgBorder"]
    > [ ![Captura de pantalla de la interfaz de usuario de subredes de redes virtuales]( ../media/network-isolation/subnets.png) ](  ../media/network-isolation/subnets.png#lightbox)

3. En la pestaña **Redes** de la instancia del servicio Cognitive Search, cambie los datos de conectividad de punto de conexión de públicos a privados. Esta operación es un proceso de larga duración y **puede tardar hasta 30 minutos** en completarse.

    > [!div class="mx-imgBorder"]
    > [ ![Captura de pantalla de la interfaz de usuario de Redes con el botón de alternancia público/privado]( ../media/network-isolation/private.png) ](  ../media/network-isolation/private.png#lightbox)

4. Una vez que el recurso de Search se cambia a privado, seleccione Agregar **punto de conexión privado**.
    - **Pestaña Aspectos básicos**: asegúrese de crear el punto de conexión en la misma región que el recurso de Search.
    - **Pestaña Recurso**: seleccione el recurso de Search necesario de tipo `Microsoft.Search/searchServices`.

    > [!div class="mx-imgBorder"]
    > [ ![Captura de pantalla de la ventana de la interfaz de usuario Crear un punto de conexión privado]( ../media/network-isolation/private-endpoint.png) ](  ../media/network-isolation/private-endpoint.png#lightbox)

    - **Pestaña Configuración**:  use la red virtual, subred (searchservicesubnet) creada en el paso 2. Después de eso, en la sección **Integración de DNS privado**, seleccione la suscripción correspondiente y cree una nueva zona DNS privada denominada **privatelink.search.windows.net**.

     > [!div class="mx-imgBorder"]
     > [ ![Captura de pantalla de la ventana de la interfaz de usuario Crear un punto de conexión privado con el campo de subred rellenado ]( ../media/network-isolation/subnet.png) ](  ../media/network-isolation/subnet.png#lightbox)

    5. Habilite la integración con red virtual para App Service normal. Puede omitir este paso para ASE, ya que ya tiene acceso a la red virtual.
        - Vaya a la sección **Redes** de App Service y abra **Integración con red virtual**.
        - Cree un vínculo a la red virtual de App Service dedicada, subred (appservicevnet) creada en el paso 2.
    
     > [!div class="mx-imgBorder"]
     > [ ![Captura de pantalla de la interfaz de usuario de Integración con red virtual]( ../media/network-isolation/integration.png) ](  ../media/network-isolation/integration.png#lightbox)


# <a name="custom-question-answering-preview-release"></a>[Respuesta a preguntas personalizada (versión preliminar)](#tab/v2)

[Cree puntos de conexión privados](../reference-private-endpoint.md) al recurso de Azure Search.

Siga los pasos siguientes para restringir el acceso público a los recursos de QnA Maker. Proteja un recurso de Cognitive Services del acceso público mediante la [configuración de la red virtual](../../cognitive-services-virtual-networks.md?tabs=portal).

Tras restringir el acceso al recurso de Cognitive Services basado en la red virtual, vaya a las bases de conocimiento del portal https://qnamaker.ai desde su red local o su explorador local.
- Conceda acceso a la [red local](../../cognitive-services-virtual-networks.md?tabs=portal#configuring-access-from-on-premises-networks).
- Conceda acceso al [explorador o máquina local](../../cognitive-services-virtual-networks.md?tabs=portal#managing-ip-network-rules).
- Agregue la **dirección IP pública de la máquina en la sección Firewall** de la pestaña **Redes**. De forma predeterminada, `portal.azure.com` muestra la dirección IP pública de la máquina de exploración actual (seleccione esta entrada) y, a continuación, seleccione **Guardar**.

     > [!div class="mx-imgBorder"]
     > [ ![Captura de pantalla de la interfaz de usuario de configuración de redes virtuales y firewall]( ../media/network-isolation/firewall.png) ](  ../media/network-isolation/firewall.png#lightbox)

---

