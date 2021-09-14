---
title: Detección de soluciones de terceros de asociados de Azure Synapse a través de Synapse Studio
description: Información sobre cómo detectar nuevas soluciones de terceros que están estrechamente integradas con asociados de Azure Synapse
services: synapse-analytics
manager: santoshb
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/14/2021
author: periclesrocha
ms.author: procha
ms.reviewer: omafnan, wiassaf
ms.custom: seo-lt-2019
ms.openlocfilehash: c765fcce0b95027ffa28915752bd3f370070a894
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481592"
---
# <a name="discover-partner-solutions-through-synapse-studio"></a>Detección de soluciones de asociados mediante Synapse Studio

Synapse Studio permite la detección de soluciones de asociados que amplían las funcionalidades de Azure Synapse. Desde conectores de datos hasta herramientas de limpieza y transformación de datos, motores de orquestación y otras cargas de trabajo, la página de **examen de asociados** sirve como centro para detectar aplicaciones y soluciones de ISV de terceros comprobadas para trabajar con Azure Synapse Analytics. Synapse Studio simplifica la introducción a estos asociados, en muchos casos con la configuración automatizada de la conexión inicial a la plataforma del asociado.

## <a name="participating-partners"></a>Asociados participantes
En la tabla siguiente se enumeran las soluciones de asociados que se admiten actualmente. Asegúrese de volverla a comprobar a menudo ya que vamos agregando nuevos asociados a esta lista. 

| Asociado | Nombre de la solución |
| ------- | ------------- |
| ![Incorta](./media/data-integration/incorta-logo.png) | Incorta Intelligent Ingest para Azure Synapse |
| ![Informatica](./media/data-integration/informatica_logo.png) | Informatica Intelligent Data Management Cloud |
| ![Qlik Data Integration (anteriormente Attunity)](./media/business-intelligence/qlik_logo.png) | Qlik Data Integration (anteriormente Attunity) |

## <a name="requirements"></a>Requisitos
Al elegir una aplicación de asociado, Azure Synapse Studio aprovisiona un entorno de espacio aislado que puede usar para la evaluación, lo que garantiza que pueda experimentar con soluciones de asociados rápidamente antes de decidir usarlo con los datos de producción. Se crean los siguientes objetos: 

|  Objeto  |    Detalles    |
| -------- | ------------- |
| Un [grupo de SQL dedicado](../overview-what-is.md) denominado **Partner_[NombreAsociado]_pool** | Nivel de rendimiento DW100c. |
| Un [inicio de sesión de SQL](/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) denominado **Partner_[NombreAsociado]_pool** | Se crea en la base de datos `master`. El usuario especifica la contraseña de inicio de sesión de SQL en la creación de la versión de evaluación.|
| Un [usuario de base de datos](../../azure-sql/database/logins-create-manage.md) | Un nuevo usuario de base de datos, asignado al nuevo inicio de sesión de SQL. Este usuario se agrega al rol de db_owner de la base de datos recién creada. |

En todos los casos, **[NombreAsociado]** es el nombre del ISV de terceros que ofrece la evaluación. 

### <a name="security"></a>Seguridad 
Una vez creados los objetos necesarios, Synapse Studio envía información sobre el nuevo entorno de espacio aislado a la aplicación asociada, lo que permite una experiencia de evaluación personalizada. Se envía la siguiente información a nuestros asociados: 
- Nombre
- Apellido
- Dirección de correo electrónico
-  Detalles sobre el entorno de Synapse necesario para establecer una conexión:     
    - Nombre DNS del área de trabajo de Synapse (nombre del servidor)
    - Nombre del grupo de SQL (base de datos)
    - Inicio de sesión de SQL (solo nombre de usuario)
    
Nunca compartimos ninguna contraseña con la aplicación asociada, incluida la contraseña del inicio de sesión de SQL recién creado. Tendrá que escribir la contraseña en la aplicación del asociado.

### <a name="costs"></a>Costos
El grupo de SQL dedicado que se crea para la evaluación del asociado genera costes continuos, que se basan en el número de bloques DWU y las horas de ejecución. Asegúrese de pausar el grupo de SQL creado para la evaluación de asociado cuando no esté en uso, para evitar cargos innecesarios. 

## <a name="starting-a-new-partner-trial"></a>Inicio de una nueva evaluación de asociado 

1) En la página principal de Synapse Studio, en **Descubrir más**, seleccione **browse partners** (examinar asociados).
2) En la página Browse partners (Examinar asociados) se muestran todos los asociados que actualmente ofrecen evaluaciones que permiten la conectividad directa con Azure Synapse. Elija una solución de asociado.
3) En la página de detalles del asociado se muestra la información relevante sobre esta aplicación y los vínculos para obtener más información sobre su solución. Cuando esté listo para iniciar una evaluación, seleccione **Connect to partner** (Conectar a asociado).
4) En la página **Connect to [PartnerName] Solution** (Conectar a la solución de [NombreAsociado]), observe los requisitos de esta conexión de asociado. Cambie el nombre del grupo de SQL y los parámetros de inicio de sesión de SQL, si quiere, (o acepte los valores predeterminados), escriba la contraseña del nuevo inicio de sesión de SQL y seleccione **Conectar**.

Se crearán los objetos necesarios para la evaluación de asociado. A continuación, se le reenviará a una página del asociado para proporcionar información adicional (si es necesario) e iniciar la evaluación. 

> [!NOTE]
> Microsoft no controla la experiencia de evaluación de asociados. Los asociados ofrecen las evaluaciones de productos con sus propios términos y la experiencia, la disponibilidad de la evaluación y las características pueden variar en función del asociado. Microsoft no ofrece compatibilidad con las aplicaciones de terceros que se ofrecen en Synapse Studio. 

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre algunos de nuestros otros asociados, consulte [Asociados de integración de datos](data-integration.md), [Asociados de Administración de datos](data-management.md) y [Asociados de IA y aprendizaje automático](machine-learning-ai.md).