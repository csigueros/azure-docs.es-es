---
title: Introducción a la arquitectura de Azure Fluid Relay
description: Introducción a la arquitectura de Azure Fluid Relay
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/build/overview/
ms.openlocfilehash: 6b58c90692a7d4eba404e4d5da84b175d229f343
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661646"
---
# <a name="overview-of-azure-fluid-relay-architecture"></a>Introducción a la arquitectura de Azure Fluid Relay

> [!NOTE]
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.

Hay tres conceptos principales que se pueden entender al compilar una aplicación con Fluid.

- Servicio
- Contenedor
- Objetos compartidos

## <a name="service"></a>Servicio

Los clientes de Fluid requieren un servicio centralizado que todos los clientes conectados usan para enviar y recibir operaciones. Cuando se usa Fluid en una aplicación, debe usar el paquete correcto correspondiente al servicio subyacente al que se está conectando.

Para el servicio Azure Fluid Relay, este paquete es **@fluidframework/azure-client** . Este paquete le permite crear y cargar contenedores de Fluid hospedados en Azure a través de Azure Fluid Relay.

## <a name="container"></a>Contenedor

El **contenedor** es la unidad principal de encapsulación en Fluid. Consta de una colección de objetos compartidos y API de apoyo para administrar el ciclo de vida del contenedor y los objetos que incluye.

La creación de nuevos contenedores es una acción controlada por el cliente, y la duración del contenedor está relacionada con los datos almacenados en el servidor compatible. Al obtener los contenedores existentes, es importante tener en cuenta el estado anterior del contenedor.

Para obtener más información sobre los contenedores, consulte la sección [Contenedores](https://fluidframework.com/docs/build/containers/) en fluidframework.com.

## <a name="shared-objects"></a>Objetos compartidos

Un **objeto compartido** es un tipo de objeto que impulsa los datos de colaboración mediante la exposición de una API específica. Muchos objetos compartidos pueden existir en el contexto de un contenedor y se pueden crear estática o dinámicamente. Las **Estructuras de datos distribuidas (DDS)** y **DataObjects** son tipos de objetos compartidos.

Para obtener más información, consulte [Modelo de datos](https://fluidframework.com/docs/build/data-modeling/) en fluidframework.com.

## <a name="package-structure"></a>Estructura del paquete

Hay dos **paquetes** principales que se usarán al compilar contenido con Fluid. El paquete **fluid-framework** y un paquete de cliente específico del servicio, como **azure-client**.

Para obtener más información, consulte [Paquetes](https://fluidframework.com/docs/build/packages/) en fluidframework.com.

### <a name="the-fluid-framework-package"></a>El paquete fluid-framework

El paquete **fluid-framework** es una colección de API de Fluid básicas que facilitan la compilación y el uso de aplicaciones. Este paquete contiene todas las definiciones de tipo comunes, así como todos los objetos compartidos primitivos.

### <a name="the-fluidframeworkazure-client-package"></a>Paquete @fluidframework/azure-client

El paquete **@fluidframework/azure-client** proporciona una API para conectarse a instancias del servicio Azure Fluid Relay para crear y cargar contenedores de Fluid. Consulte [Cómo conectarse a un servicio Azure Fluid Relay](../how-tos/connect-fluid-azure-service.md) para obtener más información sobre cómo usar estas API.
