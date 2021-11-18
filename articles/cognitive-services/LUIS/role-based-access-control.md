---
title: Control de acceso basado en rol de LUIS
titleSuffix: Azure Cognitive Services
description: Use este artículo para obtener información sobre cómo agregar el control de acceso a su recurso de LUIS.
author: aahill
ms.author: aahi
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 11/09/2021
ms.topic: conceptual
ms.openlocfilehash: 2c1f9d8783ca293898ff77f851ac928c1b94413e
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137883"
---
# <a name="luis-role-based-access-control"></a>Control de acceso basado en rol de LUIS

LUIS admite el control de acceso basado en rol de Azure (Azure RBAC), un sistema de autorización que permite administrar el acceso individual a los recursos de Azure. Con Azure RBAC, se asignan diferentes miembros del equipo a distintos niveles de permisos para los recursos de creación de LUIS. Para obtener más información, consulte la [documentación de Azure RBAC](/azure/role-based-access-control/).

## <a name="add-role-assignment-to-language-understanding-authoring-resource"></a>Adición de la asignación de roles al recurso de creación de Language Understanding

Azure RBAC se puede asignar a un recurso de creación de Language Understanding. Para conceder acceso a un recurso de Azure, se agrega una asignación de roles.
1. En [Azure Portal](https://ms.portal.azure.com/), seleccione **Todos los servicios**. 
2. Seleccione **Cognitive Services** y vaya a su recurso de creación de Language Understanding.
   > [!NOTE]
   > También puede configurar Azure RBAC para grupos de recursos completos, suscripciones o grupos de administración. Para ello, seleccione el nivel de ámbito deseado y, después, vaya al elemento deseado. Por ejemplo, seleccione **Grupos de recursos** y vaya a un grupo de recursos específico.

1. Seleccione **Control de acceso (IAM)** en el panel de navegación izquierdo.
1. Seleccione **Agregar** y, luego, **Agregar asignación de roles**.
1. En la pestaña **Rol** de la siguiente pantalla, seleccione el rol que desea agregar.
1. En la pestaña **Miembros**, seleccione un usuario, grupo, entidad de servicio o identidad administrada.
1. En la pestaña **Revisión y asignación**, seleccione **Revisión y asignación** para asignar el rol.

En cuestión de minutos, al destino se le asignará el rol seleccionado en el ámbito seleccionado. Para obtener ayuda con estos pasos, consulte [Asignación de roles de Azure mediante Azure Portal](/azure/role-based-access-control/role-assignments-portal).

## <a name="security"></a>Seguridad 

LUIS admite la autenticación de Azure Active Directory (AAD). Para obtener más información, vea [Autenticación mediante Azure Active Directory](/azure/cognitive-services/authentication#authenticate-with-azure-active-directory).

## <a name="luis-role-types"></a>Tipos de roles de LUIS

Use la tabla siguiente para determinar las necesidades de acceso para la aplicación de LUIS.

Estos roles personalizados solo se aplican a los recursos de creación (creación de Language Understanding) y no a los recursos de predicción (Language Understanding).

### <a name="cognitive-services-luis-reader"></a>Lector de LUIS de Cognitive Services

Un usuario que solo debería validar y revisar las aplicaciones de LUIS. Normalmente se trata de un evaluador, para asegurarse de que la aplicación funciona bien antes de implementar el proyecto. Es posible que este usuario quiera revisar los recursos de la aplicación (expresiones, intenciones o entidades) para notificar a los desarrolladores de las aplicaciones sobre los cambios que deben realizarse, pero no tiene acceso directo para realizarlos.


:::row:::
    :::column span="":::
        **Capabilities**
    :::column-end:::
    :::column span="":::
        **Acceso de API**
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        * Lectura de expresiones
        * Intenciones 
        * Entidades
        * Prueba de la aplicación
    :::column-end:::
    :::column span="":::
      Todas las API GET en: 
        * [API de programación de LUIS v3.0 (versión preliminar)](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f)
        * [API de programación de LUIS v2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)

      Todas las API en: 
        * [API de punto de conexión de LUIS v2.0](https://chinaeast2.dev.cognitive.azure.cn/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)
        * [API de punto de conexión de LUIS v3.0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/operations/5cb0a9459a1fe8fa44c28dd8)
        * [API de punto de conexión de LUIS v3.0 (versión preliminar)](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5cb0a9459a1fe8fa44c28dd8)

      Todas las API web de pruebas por lotes
    :::column-end:::
:::row-end:::

### <a name="cognitive-services-luis-writer"></a>Escritor de LUIS de Cognitive Services

Un usuario que es responsable de compilar y modificar la aplicación de LUIS como colaborador en un equipo más grande. El colaborador puede modificar la aplicación de LUIS de cualquier manera, entrenar esos cambios y validarlos o probarlos en el portal. Pero este usuario no tiene acceso a la implementación de esta aplicación en el entorno de ejecución, ya que podría reflejar accidentalmente sus cambios en un entorno de producción. Tampoco puede eliminar la aplicación ni modificar sus recursos de predicción o la configuración del punto de conexión (por ejemplo, asignando o desasignando recursos de predicción o haciendo que el punto de conexión sea público). Esto impide que este rol modifique una aplicación que se usa actualmente en un entorno de producción. Aunque puede crear nuevas aplicaciones en este recurso, pero con las restricciones mencionadas.

:::row:::
    :::column span="":::
        **Capabilities**
    :::column-end:::
    :::column span="":::
        **Acceso de API**
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
      * Todas las funcionalidades del rol de lector de LUIS de Cognitive Services. 

      La capacidad de agregar: 
        * Grabaciones de voz
        * Intenciones
        * Entidades
    :::column-end:::
    :::column span="":::
      * Todas las API del lector de LUIS

      Todas las API POST, PUT y DELETE en:

        * [API de programación de LUIS v3.0 (versión preliminar)](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f)
        * [API de programación de LUIS v2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2d)

        Excepto para:
        * [Eliminar aplicación](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c39)
        * [Trasladar la aplicación a otro recurso de Azure de creación de LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/apps-move-app-to-another-luis-authoring-azure-resource)
        * [Publicar una aplicación](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c3b)
        * [ Actualización de la configuración de la aplicación](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/58aeface39e2bb03dcd5909e)
        * [Asignar una cuenta de Azure de LUIS a una aplicación](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5be32228e8473de116325515)
        * [Eliminar una cuenta de Azure de LUIS asignada de una aplicación](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5be32554f8591db3a86232e1)
    :::column-end:::
:::row-end:::

### <a name="cognitive-services-luis-owner"></a>Propietario de LUIS de Cognitive Services

Estos usuarios son los guardianes de las aplicaciones de LUIS en un entorno de producción. Deben tener acceso total a cualquiera de las funciones subyacentes y, por tanto, pueden ver todo en la aplicación y tener acceso directo para editar los cambios tanto en entornos de creación como en entornos de ejecución.

:::row:::
    :::column span="":::
        **Funcionalidad**
    :::column-end:::
    :::column span="":::
        **Acceso de API**
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
      * Todas las funcionalidades del rol de escritor de LUIS de Cognitive Services
      * Implementar un modelo
      * Eliminar una aplicación
    :::column-end:::
    :::column span="":::
      * Todas las API disponibles para LUIS
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Pasos siguientes

* [Administración de recursos de Azure](/azure/cognitive-services/luis/luis-how-to-azure-subscription?branch=pr-en-us-171715&tabs=portal#authoring-resource)
