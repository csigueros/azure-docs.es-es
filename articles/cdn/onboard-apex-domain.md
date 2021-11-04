---
title: 'Incorporación de un dominio raíz o de vértice a un punto de conexión existente de Azure CDN: Azure Portal'
description: Obtenga información sobre cómo incorporar un dominio raíz o de vértice a un punto de conexión existente de Azure CDN mediante Azure Portal.
services: cdn
author: duongau
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/07/2020
ms.author: duau
ms.openlocfilehash: 9962ee3d86147520206aee84ec272069577c2eb5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426324"
---
# <a name="onboard-a-root-or-apex-domain-to-an-existing-azure-cdn-endpoint"></a>Incorporación de un dominio raíz o de vértice a un punto de conexión existente de Azure CDN

Azure CDN usa registros CNAME a fin de validar la propiedad del dominio para la incorporación de dominios personalizados. CDN no expone la dirección IP de front-end asociada a su perfil de CDN. No puede asignar el dominio de vértice a una dirección IP si su intención es incorporarlo a Azure CDN.

El protocolo DNS evita la asignación de registros CNAME en el vértice de zona. Por ejemplo, si el dominio es `contoso.com`, puede crear registros CNAME para `somelabel.contoso.com`, pero no puede crear CNAME para `contoso.com`. Esta restricción presenta un problema para los propietarios de aplicaciones que tienen aplicaciones con equilibrio de carga detrás de Azure CDN. Puesto que el uso de un perfil de CDN requiere la creación de un registro CNAME, no es posible apuntar al perfil de CDN desde el vértice de la zona.

Este problema se puede resolver mediante registros de alias en Azure DNS. A diferencia de los registros CNAME, los registros de alias se crean en el vértice de zona. Los propietarios de aplicaciones pueden usarlo para apuntar su registro de vértice de zona a un perfil de CDN que tenga puntos de conexión públicos. Los propietarios de aplicaciones pueden apuntar al mismo perfil de CDN que se use para cualquier otro dominio dentro de su zona DNS. Por ejemplo, `contoso.com` y `www.contoso.com` pueden apuntar al mismo perfil de CDN. 

La asignación del dominio de vértice o raíz a su perfil de CDN requiere básicamente el acoplamiento de CNAME o la búsqueda de DNS. Mecanismo en el que el proveedor de DNS resuelve de forma recursiva la entrada CNAME hasta que encuentra una dirección IP. Esta funcionalidad es compatible con Azure DNS para puntos de conexión de CDN. 

> [!NOTE]
> También hay otros proveedores de DNS que admiten la eliminación del formato de CNAME o la búsqueda de DNS, pero Azure CDN recomienda a sus clientes el uso de Azure DNS para hospedar sus dominios.

Puede usar Azure Portal para incorporar un dominio de vértice en CDN y habilitar HTTPS si lo asocia con un certificado para la terminación TSL. Los dominios de vértice también se conocen como dominios raíz o "desnudos".

## <a name="create-an-alias-record-for-zone-apex"></a>Creación de un registro de alias para el vértice de la zona

1. Abra la configuración de **Azure DNS** del dominio que se va a incorporar.

2. Seleccione **+ Conjunto de registros**.

3. En **Agregar conjunto de registros**, escriba o seleccione la siguiente información:

    | Configuración | Value |
    | ------- | ------|
    | Nombre | Escriba **@** . |
    | Tipo | seleccione **A**. |
    | Conjunto de registros de alias | Seleccione **Sí**. |
    | Tipo de alias | Seleccione **Recurso de Azure**. |
    | Elija una suscripción | Seleccione su suscripción. |
    | Recurso de Azure | Seleccione el punto de conexión de CDN. |

4. Escriba el valor de **TTL**.

5. Seleccione **Aceptar** para enviar los cambios.

    :::image type="content" source="./media/onboard-apex-domain/cdn-apex-alias-record.png" alt-text="Registro de alias para el vértice de la zona":::

6. El paso anterior creará un registro del vértice de zona que apuntará al recurso de CDN. Se usa una asignación de registros CNAME **cdnverify** para incorporar el dominio en el perfil de CDN.
    1. Por ejemplo, **cdnverify.contoso.com**.
    

## <a name="onboard-the-custom-domain-on-your-cdn"></a>Incorporación del dominio personalizado en CDN

Una vez que haya registrado un dominio personalizado, puede agregarlo a su punto de conexión de CDN. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y vaya al perfil de CDN que contenga el punto de conexión que desea asignar a un dominio personalizado.
    
2. En la página **Perfil de CDN**, seleccione el punto de conexión de CDN que se va a asociar con el dominio personalizado.

    :::image type="content" source="media/onboard-apex-domain/cdn-endpoint-selection.png" alt-text="Selección de punto de conexión de CDN" border="true":::
    
3. Seleccione **+ Dominio personalizado**. 

   :::image type="content" source="media/onboard-apex-domain/cdn-custom-domain-button.png" alt-text="Botón Agregar dominio personalizado" border="true":::

4. En **Agregar un dominio personalizado**, el **Nombre de host del punto de conexión** se rellena previamente y se deriva de la dirección URL del punto de conexión de CDN: **\<endpoint-hostname>** .azureedge.net. No se puede modificar.

5. Para **Nombre de host personalizado**, escriba el dominio de vértice o raíz personalizado, que se usará como el dominio de origen del registro CNAME. 
    1. Por ejemplo, **contoso.com**. **No utilice el nombre de subdominio cdnverify**.

    :::image type="content" source="media/onboard-apex-domain/cdn-add-custom-domain.png" alt-text="Adición de un dominio personalizado" border="true":::

6. Seleccione **Agregar**.

   Azure comprueba que el registro CNAME existe para el nombre de dominio personalizado que ha especificado. Si el registro CNAME es correcto, el dominio personalizado se valida. 

   Puede tardar algún tiempo la propagación de la nueva configuración de dominio personalizado a todos los nodos perimetrales de la red CDN: 
    - En los perfiles de **Azure CDN Estándar de Microsoft**, la propagación se completa normalmente en 10 minutos. 
    - En los perfiles de **Azure CDN estándar**, la propagación normalmente se completa en un minuto. 
    - En los perfiles **Azure CDN Estándar de Verizon** y **Azure CDN Premium de Verizon**, la propagación se completa normalmente en 10 minutos.   

## <a name="enable-https-on-your-custom-domain"></a>Habilitación de HTTPS en el dominio personalizado

Para obtener más información sobre cómo habilitar HTTPS en su dominio personalizado para Azure CDN, vea [Tutorial: Configuración de HTTPS en un dominio personalizado de Azure CDN](cdn-custom-ssl.md)

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [crear un punto de conexión de CDN](cdn-create-new-endpoint.md).
