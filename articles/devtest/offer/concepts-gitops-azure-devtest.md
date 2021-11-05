---
title: GitOps y la oferta Desarrollo/pruebas de Azure
description: Uso de GitOps en asociación con Azure Dev/Test
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/20/2021
ms.custom: devtestoffer
ms.openlocfilehash: df3fd830c04f90315126bd68a07a22a95cbfbc53
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093218"
---
# <a name="using-gitops-with-azure-devtest-offer-to-optimize-and-secure-applications"></a>Uso de GitOps con la oferta de desarrollo y pruebas de Azure para optimizar y proteger aplicaciones

GitOps es un marco operativo. Toma DevOps procedimientos recomendados que se usan para desarrollar aplicaciones y las aplica a la automatización de la infraestructura.  

Cuando los equipos practicarán GitOps, usarán archivos de configuración almacenados como código (infraestructura como código). Estos archivos generan el mismo entorno cada vez que se implementa. Piense en él como el código fuente de la aplicación que genera los mismos archivos binarios de aplicación cada vez que se compila.  

## <a name="gitops-methodology"></a>Metodología de GitOps  

Este proceso, o metodología, usa repositorios de Git. Estos repositorios son la fuente de confianza para un estado y una configuración que defina para la aplicación. Contienen descripciones declarativas de la infraestructura que necesita en producción. Un proceso automatizado hace que ese entorno coincida con el estado descrito en el repositorio.  

Para implementar una nueva aplicación o actualizar una existente, solo tiene que actualizar el repositorio: el proceso automatizado controla todo lo demás.  

## <a name="benefits-of-gitops"></a>Ventajas de GitOps  

- Habilita la colaboración en los cambios de infraestructura  
- Control de acceso mejorado  
- Menor tiempo de llegada al mercado  
- Menor riesgo  
- Menores costos.  
- Menos propenso a errores  

## <a name="use-gitops-with-devtest"></a>Uso de GitOps con desarrollo y pruebas  

GitOps como proceso y marco se debe aplicar a las instancias que no son de producción. Se puede comprobar o usar en los entornos de DevTest. Puede usar principios de GitOps para mejorar los procesos DevOps cliente. Use sus ventajas y entornos de DevTest con principios de GitOps para optimizar las actividades y mantener la seguridad y la confiabilidad de las aplicaciones.  

GitOps combina automatización y marcos de colaboración de uso frecuente, como Git. Se pueden combinar para proporcionar una entrega rápida de la infraestructura en la nube a la vez que se cumplen los estándares de seguridad de la empresa.  

Más información sobre GitOps y Azure:  

- [Vídeo de Azure Friday: Azure Arc Kubernetes habilitado con GitOps](https://azure.microsoft.com/resources/videos/azure-friday-azure-arc-enabled-kubernetes-with-gitops/)  
- [Blog de Azure Friday: Azure Arc Habilitado para Kubernetes con GitOps](https://techcommunity.microsoft.com/t5/azure-arc/azure-arc-enabled-kubernetes-with-gitops/ba-p/1654171?ocid=AID754288&wt.mc_id=azfr-c9-scottha&wt.mc_id=CFID0570)  
- [GitOps para Azure Infrastructure Lifecycle Automation](https://github.com/travisnielsen/azure-gitops)
