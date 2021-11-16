---
title: Trasladar la aplicación de funciones entre regiones de Azure Functions
description: Aprenda a trasladar recursos de Azure Functions de una región a otra mediante la creación de una copia de los recursos de Azure Function existentes en la región de destino.
ms.topic: how-to
ms.service: azure-functions
author: nzthiago
ms.date: 05/11/2021
ms.custom: subject-moving-resources
ms.openlocfilehash: c106ec2e5e4592937974f040e1ae14aabc45f354
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138663"
---
# <a name="move-your-function-app-between-regions-in-azure-functions"></a>Trasladar la aplicación de funciones entre regiones de Azure Functions

En este artículo, se explica cómo pueden trasladarse los recursos de Azure Functions a otra región. Puede trasladar sus recursos a otra región por uno de los siguientes motivos:
 + Aprovechar las ventajas de una nueva región de Azure
 + Implementar características o servicios que solo están disponibles en regiones específicas
 + Cumplir requisitos internos de gobernanza y directivas
 + Responder a los requisitos de planeamiento de capacidad

Los recursos de Azure Functions son específicos de una región y no pueden moverse entre regiones. Debe crear una copia de los recursos existentes de la aplicación de funciones en la región de destino y, a continuación, volver a implementar el código de funciones en la nueva aplicación.

Si el tiempo de inactividad mínimo es un requisito, considere la posibilidad de ejecutar la aplicación de funciones en ambas regiones para implementar una arquitectura de recuperación ante desastres:
+ [Recuperación ante desastres geográfica de Azure Functions](functions-geo-disaster-recovery.md)
+ [Recuperación ante desastres y distribución geográfica en Azure Durable Functions](durable/durable-functions-disaster-recovery-geo-distribution.md)

## <a name="prerequisites"></a>Requisitos previos

+ Compruebe que la región de destino admite Azure Functions y los servicios relacionados, cuyos recursos desea trasladar
+ Tenga acceso al código fuente original de las funciones que va a migrar

## <a name="prepare"></a>Preparación

Identifique todos los recursos de la aplicación de funciones usados en la región de origen, que pueden incluir lo siguiente:

+ Aplicación de función
+ [Plan de hospedaje](functions-scale.md#overview-of-plans)
+ [Ranuras de implementación](functions-deployment-slots.md)
+ [Dominios personalizados adquiridos en Azure](../app-service/manage-custom-dns-buy-domain.md)
+ [Configuración y certificados TLS/SSL](../app-service/configure-ssl-certificate.md)
+ [Configure las opciones de red](functions-networking-options.md)
+ [Identidades administradas](../app-service/overview-managed-identity.md)
+ [Configuración de la aplicación](functions-how-to-use-azure-function-app-settings.md): los usuarios con el acceso suficiente pueden copiar toda la configuración de la aplicación de origen mediante la característica Edición avanzada en el portal
+ [Escalado de configuraciones](functions-scale.md#scale)

Las funciones pueden conectarse a otros recursos mediante desencadenadores o enlaces. Para más información acerca de cómo migrar esos recursos entre regiones, consulte la documentación de los servicios correspondientes.

También debería poder [exportar una plantilla de los recursos existentes](../azure-resource-manager/templates/export-template-portal.md).

## <a name="move"></a>Move

Implemente la aplicación de funciones en la región de destino y revise los recursos configurados. 

### <a name="redeploy-function-app"></a>Volver a implementar la aplicación de funciones

Si tiene acceso a los recursos de implementación y automatización que crearon la aplicación de funciones en la región de origen, vuelva a ejecutar los mismos pasos de implementación en la región de destino para crear y volver a implementar la aplicación. 

Si solo tiene acceso al código fuente, pero no a los recursos de implementación y automatización, puede implementar y configurar la aplicación de funciones en la región de destino mediante cualquiera de las [tecnologías de implementación](functions-deployment-technologies.md) disponibles o mediante uno de los [métodos de implementación continua](functions-continuous-deployment.md).

### <a name="review-configured-resources"></a>Revisión de los recursos configurados

Revise y configure los recursos identificados en el paso anterior de [Preparación](#prepare) en la región de destino si no se configuraron durante la implementación.

### <a name="move-considerations"></a>Traslado de consideraciones
+ Si los recursos de implementación y la automatización no crean una aplicación de funciones, [cree una aplicación del mismo tipo en un nuevo plan de hospedaje](functions-scale.md#overview-of-plans) en la región de destino
+ Los nombres de aplicación de funciones de Azure son únicos a nivel global, por lo que la aplicación de la región de destino no puede tener el mismo nombre que el de la región de origen
+ Las referencias y la configuración de la aplicación que conectan la aplicación de funciones a las dependencias deben revisarse y, cuando sea necesario, actualizarse. Por ejemplo, al trasladar una base de datos a la que llaman las funciones, también debe actualizar la configuración de la aplicación para conectarse a la base de datos en la región de destino. Algunas opciones de configuración de la aplicación, como la clave de instrumentación de Application Insights o la cuenta de almacenamiento de Azure que usa la aplicación de funciones, ya se pueden configurar en la región de destino y no es necesario actualizarla
+ No olvide comprobar la configuración y probar las funciones en la región de destino
+ Si tenía un dominio personalizado configurado, [vuelva a asignar el nombre de dominio](../app-service/manage-custom-dns-migrate-domain.md#remap-the-active-dns-name)
+ En el caso de las funciones que se ejecutan en planes dedicados, revise también el [Plan de migración de App Service](../app-service/manage-move-across-regions.md) en caso de que el plan se comparta con aplicaciones web

## <a name="clean-up-source-resources"></a>Limpieza de los recursos de origen

Una vez completado el traslado, elimine la aplicación de funciones y el plan de hospedaje de la región de origen. Paga por las aplicaciones de funciones en Premium o Planes dedicados, incluso cuando la propia aplicación no se está ejecutando.

## <a name="next-steps"></a>Pasos siguientes

+ En el [Centro de arquitectura de Azure](/azure/architecture/browse/?expanded=azure&products=azure-functions) busque ejemplos en los que Azure Functions se ejecuta en varias regiones como parte de arquitecturas de soluciones más avanzadas
