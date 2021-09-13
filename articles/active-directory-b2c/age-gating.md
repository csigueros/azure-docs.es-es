---
title: Habilitar la restricción de acceso por edad en Azure Active Directory B2C | Microsoft Docs
description: Obtenga información acerca de cómo identificar a los menores mediante la aplicación.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f0502f11c9a6e900ae8f25209b6897f8384c2773
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122777761"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Habilitar la restricción de acceso por edad en Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

La restricción de acceso por edad en Azure Active Directory B2C (Azure AD B2C) le permite identificar a los menores que quieren usar la aplicación, con o sin consentimiento parental. Puede optar por impedir que los menores inicien sesión en la aplicación. O bien permitir que los usuarios completen el inicio de sesión y proporcionar a la aplicación el estado secundario. 

>[!IMPORTANT]
>Esta característica está en versión preliminar pública. No use la característica para aplicaciones de producción.
>

Cuando se habilita la restricción de acceso por edad para un flujo de usuario, se pide a los usuarios su fecha de nacimiento y país de residencia. Si inicia sesión un usuario que no ha introducido la información anteriormente, deberá escribirla la próxima vez que inicie sesión. Las reglas se aplican cada vez que un usuario inicia sesión.

![Captura de pantalla del flujo de recopilación de información de la restricción de acceso por edad](./media/age-gating/age-gating-information-gathering.png)

Azure AD B2C usa la información que escribe el usuario para identificar si es un menor. A continuación, el campo **ageGroup** se actualiza en su cuenta. El valor puede ser `null`, `Undefined`, `Minor`, `Adult` y `NotAdult`.  Los campos **ageGroup** y **consentProvidedForMinor** se utilizan para calcular el valor de **legalAgeGroupClassification**.


## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="set-up-your-tenant-for-age-gating"></a>Configurar el inquilino para la restricción de acceso por edad

Para poder usar la restricción de acceso por edad en un flujo de usuario, debe configurar el inquilino para que admita propiedades adicionales.

1. Use [este vínculo](https://portal.azure.com/?Microsoft_AAD_B2CAdmin_agegatingenabled=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) para probar la versión preliminar de la restricción de acceso por edad.
1. Asegúrese de usar el directorio que contiene el inquilino de Azure AD B2C. Para ello, seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Azure AD B2C**.
1. Seleccione la opción **Propiedades** del inquilino en el menú de la izquierda.
1. En **Restricción de acceso por edad**, seleccione **Configurar**.
1. Espere a que finalice la operación y el inquilino tendrá configurada la restricción de acceso por edad.

::: zone pivot="b2c-user-flow"

## <a name="enable-age-gating-in-your-user-flow"></a>Habilitar la restricción de acceso por edad en el flujo de usuario

Una vez que el inquilino esté configurado para usar la restricción de acceso por edad, puede usar esta característica en los [flujos de usuario](user-flow-versions.md) cuando esté habilitada. Para habilitar el acceso según la edad, realice los pasos siguientes:

1. Cree un flujo de usuario con la restricción de acceso por edad habilitada.
1. Después de crear el flujo de usuario, seleccione **Propiedades** en el menú.
1. En la sección **Restricción de acceso por edad**, seleccione **Habilitado**.
1. En **Registro o inicio de sesión**, seleccione cómo desea administrar usuarios:
    - Permitir que los menores de edad accedan a su aplicación.
    - Impedir acceso a la aplicación solo a los menores de 16 años.
    - Impedir acceso a la aplicación a todos los menores.
1. En **En bloque**, seleccione una de las opciones siguientes:
    - **Volver a enviar un archivo JSON a la aplicación**: esta opción envía una respuesta a la aplicación indicando que se bloqueó un menor.
    - **Mostrar una página de error**: se muestra al usuario una página que le indica que no puede acceder a la aplicación.

## <a name="test-your-user-flow"></a>Prueba del flujo de usuario

1. Para probar la directiva, seleccione **Ejecutar flujo de usuario**.
1. En **Aplicación**, seleccione la aplicación web denominada *testapp1* que registró anteriormente. La **dirección URL de respuesta** debe mostrar `https://jwt.ms`.
1. Seleccione el botón **Ejecutar flujo de usuario**.
1. Inicie sesión con una cuenta local o social. Después, seleccione el país de residencia y la fecha de nacimiento que simulan un menor. 
1. Repita la prueba y seleccione una fecha de nacimiento que simule un adulto.  

Cuando inicie sesión como menor, debería ver el siguiente mensaje de error: *Se ha bloqueado su inicio de sesión. En su país, el acceso a cuentas que pertenezcan a menores está prohibido de acuerdo con la legislación relativa a la privacidad y la seguridad en línea.*

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="enable-age-gating-in-your-custom-policy"></a>Habilitación de la restricción de acceso por edad en su directiva personalizada

1. Obtenga el ejemplo de una directiva de restricción de acceso por edad en [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies).
1. En cada archivo, reemplace la cadena `yourtenant` por el nombre del inquilino de Azure AD B2C. Por ejemplo, si el nombre del inquilino de B2C es *contosob2c*, todas las instancias de `yourtenant.onmicrosoft.com` se convierten en `contosob2c.onmicrosoft.com`.
1. Cargue los archivos de directivas.

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [administrar el acceso de usuarios en Azure AD B2C](manage-user-access.md).
