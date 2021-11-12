---
title: Característica de vínculo
titleSuffix: Azure SQL Managed Instance
description: Obtenga información sobre la característica de vínculo de Azure SQL Managed Instance para replicar continuamente datos de SQL Server en la nube, o bien migrar las bases de datos de SQL Server con el mejor tiempo de inactividad mínimo posible.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1, ignite-fall-2021
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: mathoma, danil
ms.date: 11/05/2021
ms.openlocfilehash: 19851f1ff3bef1ab57d23013ada78bec5665b7c5
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989819"
---
# <a name="link-feature-for-azure-sql-managed-instance-limited-preview"></a>Característica de vínculo para Azure SQL Managed Instance (versión preliminar limitada)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

La nueva característica de vínculo de Azure SQL Managed Instance conecta los servidores SQL hospedados en cualquier lugar a SQL Managed Instance, lo que proporciona flexibilidad híbrida y movilidad de bases de datos. Con un enfoque que usa la replicación de datos casi en tiempo real en la nube, puede descargar cargas de trabajo en una base de datos secundaria de solo lectura en Azure para aprovechar las características, el rendimiento y la escala exclusivos de Azure. 

Después de un evento desastroso, puede seguir ejecutando las cargas de trabajo de solo lectura en SQL Managed Instance en Azure. También puede optar por migrar una o varias aplicaciones de SQL Server a SQL Managed Instance al mismo tiempo, a su propio ritmo y con el mejor tiempo de inactividad mínimo posible en comparación con otras soluciones actuales de Azure.

> [!NOTE]
> La característica de vínculo se ha publicado en versión preliminar pública limitada y actualmente solo es compatible con SQL Server 2019 Enterprise Edition CU13 (o superior). [Regístrese ahora](https://aka.ms/mi-link-signup) para participar en la versión preliminar pública limitada. 

## <a name="overview"></a>Información general

La tecnología subyacente de la replicación de datos casi en tiempo real entre SQL Server y SQL Managed Instance se basa en grupos de disponibilidad distribuidos, parte de la conocida y probada pila de tecnología de grupos de disponibilidad Always On. Amplíe el grupo de disponibilidad local de SQL Server para SQL Managed Instance en Azure de forma segura y segura. 

No es necesario tener un grupo de disponibilidad existente ni varios nodos. El vínculo admite instancias de SQL Server de nodo único sin grupos de disponibilidad existentes, así como instancias de SQL Server de varios nodos con grupos de disponibilidad existentes. Mediante el vínculo, puede aprovechar las ventajas modernas de Azure sin migrar la totalidad del patrimonio de datos de SQL Server a la nube.

Puede seguir ejecutando el vínculo mientras lo necesite, durante meses e incluso años. Y para el recorrido de modernización, si está listo para migrar a Azure, el vínculo permite una experiencia de migración considerablemente mejorada con el tiempo de inactividad mínimo posible en comparación con todas las demás opciones disponibles actualmente, lo que proporciona una verdadera migración en línea a SQL Managed Instance.

## <a name="supported-scenarios"></a>Escenarios admitidos

Los datos replicados mediante la característica de vínculo de SQL Server en Azure SQL Managed Instance se pueden usar con una serie de escenarios, como los siguientes: 

- **Uso de servicios de Azure sin realizar la migración a la nube** 
- **Descarga de cargas de trabajo de solo lectura en Azure** 
- **Migración a Azure**

![Escenario principal de vínculo de instancia administrada](./media/managed-instance-link/mi-link-main-scenario.png)


### <a name="use-azure-services"></a>Uso de servicios de Azure 

Use la característica de vínculo para aprovechar los servicios de datos de SQL Server sin realizar la migración a la nube. Entre los ejemplos se incluyen informes, análisis, copias de seguridad, aprendizaje automático y otros trabajos que envían datos a Azure. 

### <a name="offload-workloads-to-azure"></a>Descarga de cargas de trabajo en Azure 

También puede usar la característica de vínculo para descargar cargas de trabajo en Azure. Por ejemplo, una aplicación podría usar SQL Server para cargas de trabajo de lectura y escritura, al tiempo que descarga cargas de trabajo de solo lectura en SQL Managed Instance en cualquiera de las más de 60 regiones de Azure en todo el mundo. Una vez que se ha establecido el vínculo, la base de datos principal de SQL Server tiene acceso de lectura y escritura, mientras que los datos replicados en SQL Managed Instance en Azure solo tienen acceso de lectura. Esto permite varios escenarios en los que las bases de datos replicadas en SQL Managed Instance se pueden usar para el escalado horizontal de lectura y la descarga de cargas de trabajo de solo lectura en Azure. SQL Managed Instance, en paralelo, también puede hospedar bases de datos de lectura y escritura independientes. Esto permite copiar la base de datos replicada en otra base de datos de lectura y escritura en la misma instancia administrada para el posterior procesamiento de los datos.

El vínculo tiene ámbito de base de datos (un vínculo por cada base de datos), lo que permite la consolidación y desconsolidación de cargas de trabajo en Azure. Por ejemplo, puede replicar bases de datos de varios servidores SQL en una única instancia de SQL Managed Instance en Azure (consolidación), o bien replicar bases de datos desde una única instancia de SQL Server a varias instancias administradas mediante una relación de 1 a 1 entre una base de datos y una instancia administrada, en cualquiera de las regiones de Azure en todo el mundo (desconsolidación). Esta última opción es una manera eficaz de acercar rápidamente las cargas de trabajo a los clientes de cualquier región del mundo, que puede usar como réplicas de solo lectura.

### <a name="migrate-to-azure"></a>Migración a Azure 

La característica de vínculo también facilita la migración de SQL Server a SQL Managed Instance, lo que permite lo siguiente: 

- Migración con el mayor rendimiento y el tiempo de inactividad mínimo en comparación con todas las demás soluciones disponibles en la actualidad
- Verdadera migración en línea a SQL Managed Instance en cualquier nivel de servicio 

Como la característica de vínculo permite la migración con un tiempo de inactividad mínimo, puede migrar a la instancia administrada mientras mantiene la carga de trabajo principal en línea. Aunque antes la migración en línea era posible con otras soluciones al migrar al nivel de servicio de uso general, ahora la característica de vínculo también permite realizar verdaderas migraciones en línea al nivel de servicio crítico para la empresa. 

## <a name="how-it-works"></a>Cómo funciona

La tecnología que subyace a la característica de vínculo para SQL Managed Instance son los grupos de disponibilidad distribuidos. La solución admite sistemas de nodo único sin grupos de disponibilidad existentes, o bien sistemas de varios nodos con grupos de disponibilidad existentes.  

![Funcionamiento de la característica de vínculo de SQL Managed Instance](./media/managed-instance-link/mi-link-ag-dag.png)

La conectividad segura, como VPN o ExpressRoute, se usa entre una red local y Azure. Si SQL Server se hospeda en una máquina virtual de Azure, se puede usar la red troncal interna de Azure entre la máquina virtual y la instancia administrada, como, por ejemplo, el emparejamiento de VNet global. La confianza entre los dos sistemas se establece mediante la autenticación basada en certificados, en la que SQL Server y SQL Managed Instance intercambian sus claves públicas.

Puede haber hasta 100 vínculos del mismo origen de SQL Server, o de varios, a una única instancia de SQL Managed Instance. Este límite se rige por el número de bases de datos que se pueden hospedar en una instancia administrada en este momento. Del mismo modo, una única instancia de SQL Server puede establecer varios vínculos de replicación de base de datos en paralelo con varias instancias administradas en distintas regiones de Azure en una relación de 1 a 1 entre una base de datos y una instancia administrada. Para la característica es necesario instalar CU13 o posterior en SQL Server 2019.

## <a name="sign-up-for-link"></a>Registro para el vínculo

Para usar la característica de vínculo, necesitará lo siguiente:

- SQL Server 2019 Enterprise Edition con [CU13 (o superior)](https://support.microsoft.com/topic/kb5005679-cumulative-update-13-for-sql-server-2019-5c1be850-460a-4be4-a569-fe11f0adc535) instalado en el entorno local o en una máquina virtual de Azure.
- Se necesita conectividad de red entre SQL Server y la instancia administrada. Si SQL Server se ejecuta en el entorno local, use un vínculo VPN o ExpressRoute. Si SQL Server se ejecuta en una máquina virtual de Azure, implemente la máquina virtual en la misma subred que la instancia administrada, o bien use el emparejamiento de VNet global para conectar dos subredes independientes. 
- Azure SQL Managed Instance aprovisionada en cualquier nivel de servicio.

Use el vínculo siguiente a fin de registrarse para obtener la versión preliminar limitada de la característica de vínculo. 

> [!div class="nextstepaction"]
> [Registro para obtener la versión preliminar de la característica de vínculo](https://aka.ms/mi-link-signup)

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la característica de vínculo, vea lo siguiente:

- [Vínculo de instancia administrada: conexión de SQL Server a Azure reimaginada](https://aka.ms/mi-link-techblog).

Para otros escenarios de replicación, considere lo siguiente: 

- [Replicación transaccional con Azure SQL Managed Instance (versión preliminar)](replication-transactional-overview.md)
