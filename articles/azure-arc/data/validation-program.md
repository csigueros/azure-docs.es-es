---
title: Validación de servicios de datos habilitados para Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: conceptual
author: MikeRayMSFT
ms.author: mikeray
description: En este artículo se describe el programa de validación para las distribuciones de Kubernetes para los servicios de datos habilitados para Azure Arc.
keywords: Kubernetes, Arc, Azure, K8s, validación, servicios de datos, SQL Managed Instance
ms.openlocfilehash: 1935648b2d3de48aecf9878865f377c41c8498cc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780480"
---
# <a name="azure-arc-enabled-data-services-kubernetes-validation"></a>Validación de Kubernetes de los servicios de datos habilitados para Azure Arc

El equipo de servicios de datos habilitados para Azure Arc ha trabajado con asociados del sector a fin de validar distribuciones y soluciones específicas para hospedar servicios de datos habilitados para Azure Arc. Esta validación extiende la [validación de Kubernetes habilitada para Azure Arc](../kubernetes/validation-program.md) para los servicios de datos. En este artículo se identifican soluciones de asociados, versiones, versiones de Kubernetes, versiones de SQL Server y versiones de Hiperescala de PostgreSQL que se han comprobado como compatibles con los servicios de datos. 

Si desea ver cómo se validan todos los componentes habilitados para Azure Arc, consulte el artículo de [información general sobre el programa de validación](../validation-program/overview.md).

> [!NOTE]
> En estos momentos, SQL Managed Instance habilitado para Azure Arc está disponible con carácter general en regiones seleccionadas.
>
> Hiperescala de PostgreSQL habilitada para Azure Arc está disponible para su versión preliminar en regiones seleccionadas.

## <a name="partners"></a>Asociados

### <a name="dell"></a>Dell

|Solución y versión | Versión de Kubernetes | Versión de servicios de datos habilitados para Azure Arc | Versión del motor de SQL | Versión de Hiperescala de PostgreSQL
|-----|-----|-----|-----|-----|
| Dell EMC PowerFlex |1.19.7|v1.0.0_2021-07-30|SQL Server 2019 (15.0.4123) | |
| PowerFlex, versión 3.6 |1.19.7|v1.0.0_2021-07-30|SQL Server 2019 (15.0.4123) | |
| PowerFlex CSI, versión 1.4 |1.19.7|v1.0.0_2021-07-30|SQL Server 2019 (15.0.4123) | |
| PowerStore X|1.20.6|v1.0.0_2021-07-30|SQL Server 2019 (15.0.4123) |postgres 12.3 (Ubuntu 12.3-1) |
| Powerstore T|1.20.6|v1.0.0_2021-07-30|SQL Server 2019 (15.0.4123) |postgres 12.3 (Ubuntu 12.3-1)|

### <a name="nutanix"></a>Nutanix

|Solución y versión | Versión de Kubernetes | Versión de servicios de datos habilitados para Azure Arc | Versión de SQL Server | Versión de Hiperescala de PostgreSQL
|-----|-----|-----|-----|-----|
| Karbon 2.2<br/>AOS: 5.19.1.5<br/>AHV:20201105.1021<br/>PC: versión pc.2021.3.02<br/> | 1.19.8-0 | v1.0.0_2021-07-30 | SQL Server 2019 (15.0.4123)|postgres 12.3 (Ubuntu 12.3-1)|

### <a name="purestorage"></a>Pure Storage

|Solución y versión | Versión de Kubernetes | Versión de servicios de datos habilitados para Azure Arc | Versión de SQL Server | Versión de Hiperescala de PostgreSQL
|-----|-----|-----|-----|-----|
| Portworx Enterprise 2.7 | 1.20.7 | v1.0.0_2021-07-30 | SQL Server 2019 (15.0.4138)||

### <a name="red-hat"></a>Red Hat

|Solución y versión | Versión de Kubernetes | Versión de servicios de datos habilitados para Azure Arc | Versión de SQL Server | Versión de Hiperescala de PostgreSQL
|-----|-----|-----|-----|-----|
| OpenShift 7.13 | 1.20.0 | v1.0.0_2021-07-30 | SQL Server 2019 (15.0.4138)|postgres 12.3 (Ubuntu 12.3-1)|

### <a name="vmware"></a>VMware

|Solución y versión | Versión de Kubernetes | Versión de servicios de datos habilitados para Azure Arc | Versión de SQL Server | Versión de Hiperescala de PostgreSQL
|-----|-----|-----|-----|-----|
| TKGm 1.3.1 | 1.20.5 | v1.0.0_2021-07-30 | SQL Server 2019 (15.0.4123)|postgres 12.3 (Ubuntu 12.3-1)|

## <a name="data-services-validation-process"></a>Proceso de validación de servicios de datos

El complemento de servicios de datos habilitados para Azure Arc de Sonobuoy automatiza el aprovisionamiento y la prueba de Servicios de datos habilitados para Azure Arc en un clúster de Kubernetes.

### <a name="prerequisites"></a>Requisitos previos

Instalación de herramientas: 

- [CLI de Azure Data (`azdata`)](/sql/azdata/install/deploy-install-azdata)
- [kubectl](https://kubernetes.io/docs/home/)
- [Azure Data Studio, compilación de Insider](https://github.com/microsoft/azuredatastudio)

Cree un archivo de configuración de Kubernetes configurado para acceder al clúster de Kubernetes de destino y establézcalo como el contexto actual. La forma en que se genera y se lleva localmente al equipo varía en las distintas plataformas. Consulte [Kubernetes.io](https://kubernetes.io/docs/home/)

### <a name="process"></a>Proceso

Las pruebas de conformidad se ejecutan como parte de la validación de los servicios de datos habilitados para Azure Arc. Un requisito previo para ejecutar estas pruebas es pasar las pruebas de Kubernetes habilitado para Azure Arc correspondientes a la distribución de Kubernetes que esté en uso.

Estas pruebas comprueban que el producto cumple con los requisitos de ejecución y funcionamiento de los servicios de datos. Esto le permitirá evaluar si el producto está preparado para su implementación en un entorno empresarial.

Las pruebas de los servicios de datos abarcan lo siguiente en el modo de conexión indirecta.

1. Implementación del controlador de datos en modo indirecto
2. Implementación de [SQL Managed Instance habilitado para Azure Arc](create-sql-managed-instance.md)
3. Implementación de [Hiperescala de PostgreSQL habilitada para Azure Arc](create-postgresql-hyperscale-server-group.md)
4. Escalado horizontal de [Hiperescala de PostgreSQL](scale-out-in-postgresql-hyperscale-server-group.md)

Se agregarán más pruebas en versiones futuras de los servicios de datos habilitados para Azure Arc.

## <a name="additional-information"></a>Información adicional

- [Información general sobre el programa de validación](../validation-program/overview.md)
- [Validación de Kubernetes habilitado para Azure Arc](../kubernetes/validation-program.md)
- [Programa de validación de Azure Arc: proyecto de GitHub](https://github.com/Azure/azure-arc-validation/)

## <a name="next-steps"></a>Pasos siguientes

[Creación de un controlador de datos](create-data-controller.md)
