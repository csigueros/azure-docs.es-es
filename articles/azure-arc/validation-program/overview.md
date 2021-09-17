---
title: Introducción a la validación de los servicios habilitados para Azure Arc
description: Explica el proceso de validación para Azure Arc de manera que se ajuste a Kubernetes, Data Services y las extensiones de clúster habilitados para Arc.
ms.date: 07/30/2021
ms.topic: overview
ms.openlocfilehash: 1cf208b9a476f4a2a16df157ebfa0bb7661cfc1c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121788226"
---
# <a name="overview-of-azure-arc-enabled-service-validation"></a>Introducción a la validación de los servicios habilitados para Azure Arc

Microsoft recomienda ejecutar los servicios habilitados para Azure Arc en plataformas validadas. En este artículo se le indica contenido que explica cómo se validan varios componentes habilitados para Azure Arc. 

Actualmente, las soluciones validadas están disponibles de los asociados para Kubernetes y los servicios de datos.

## <a name="kubernetes"></a>Kubernetes

Kubernetes habilitado para Azure Arc funciona con todos los clústeres de Kubernetes con la certificación de Cloud Native Computing Foundation (CNCF). El equipo de Azure Arc ha trabajado con proveedores clave del sector que ofrecen Kubernetes para asegurarse de validar Kubernetes con sus [distribuciones de Kubernetes](../kubernetes/validation-program.md). Se asegurará la compatibilidad de las futuras versiones principales y secundarias de las distribuciones de Kubernetes publicadas por estos proveedores con Kubernetes habilitado para Azure Arc.

## <a name="data-services"></a>Servicios de datos

También nos hemos asociado con fabricantes de equipos originales (OEM) y proveedores de almacenamiento asociados para validar soluciones de [servicios de datos habilitados para Azure Arc](../data/validation-program.md).

## <a name="validation-process"></a>Proceso de validación

El proceso de validación para Azure Arc está disponible en GitHub. Para más detalles sobre cómo validar la oferta con Azure Arc, la herramienta de ejecución de pruebas y la estrategia, consulte el [proceso de validación para Azure Arc](https://github.com/Azure/azure-arc-validation/) en GitHub.

## <a name="next-steps"></a>Pasos siguientes

* [Distribuciones de Kubernetes validadas](../kubernetes/validation-program.md?toc=/azure/azure-arc/toc.json&bc=/azure/azure-arc/breadcrumb/toc.json)

* [Distribuciones de Kubernetes validadas para los servicios de datos](../data/validation-program.md?toc=/azure/azure-arc/toc.json&bc=/azure/azure-arc/breadcrumb/toc.json)
