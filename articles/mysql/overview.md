---
title: 'Introducción: Azure Database for MySQL'
description: Obtenga más información acerca de Azure Database for MySQL, que es un servicio de base de datos relacional de Microsoft Cloud basado en MySQL Community Edition.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: e93432347de7df3b2743143c68078b3dfc853848
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894362"
---
# <a name="what-is-azure-database-for-mysql"></a>¿Qué es Azure Database for MySQL?

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Azure Database for MySQL es un servicio de base de datos relacional de Microsoft Cloud basado en el motor de base de datos de [MySQL Community Edition](https://www.mysql.com/products/community/) (disponible bajo la licencia GPLv2), versiones 5.6 (retirada), 5.7 y 8.0. Azure Database for MySQL ofrece lo siguiente:

- Alta disponibilidad con redundancia de zona y en la misma zona.
- Control máximo con la capacidad de seleccionar la ventana de mantenimiento programado.
- Protección de datos mediante copias de seguridad automáticas y restauración a un momento dado durante un máximo de 35 días.
- Aplicación automatizada de revisiones y mantenimiento automatizado para el hardware subyacente, el sistema operativo y el motor de base de datos para mantener el servicio seguro y actualizado.
- Rendimiento predecible, con precios de pago por uso inclusivos.
- Escalado elástico en cuestión de segundos.
- Controles de optimización de costes con SKU ampliable de bajo coste y capacidad para detener o iniciar el servidor.
- Seguridad de nivel empresarial, cumplimiento normativo líder en el mercado y privacidad para proteger la información confidencial en reposo y en movimiento.
- Supervisión y automatización para simplificar la administración y la supervisión de las implementaciones a gran escala.
- Experiencia de soporte técnico líder del sector.

Estas funcionalidades no requieren casi ninguna tarea de administración y todas se proporcionan sin ningún costo adicional. Le permiten centrarse en el desarrollo rápido de aplicaciones y en reducir el plazo de acceso al mercado, en lugar de tener que dedicar tiempo y recursos a la administración tanto de máquinas virtuales como de infraestructura. Además, puede seguir desarrollando su aplicación con las herramientas y la plataforma de código abierto de su elección, y entregar con la velocidad y eficacia que exige su negocio sin tener que aprender nuevas habilidades.

:::image type="content" source="media/overview/1-azure-db-for-mysql-conceptual-diagram.png" alt-text="Diagrama conceptual de Azure Database for MySQL":::

## <a name="deployment-models"></a>Modelos de implementación

Azure Database for MySQL, con tecnología de MySQL Community Edition, está disponible en dos modos de implementación:
- Servidor flexible
- Servidor único 

### <a name="azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL con la opción Servidor flexible

Servidor flexible de Azure Database for MySQL es un servicio de base de datos totalmente administrado y listo para la producción diseñado para lograr un control más pormenorizado y una mayor flexibilidad de las funciones de administración de bases de datos y las opciones de configuración. La arquitectura de servidor flexible permite a los usuarios optar por la alta disponibilidad dentro de una única zona de disponibilidad o entre varias. Los servidores flexibles proporcionan mejores controles de optimización de costos con la capacidad de iniciar o detener el servidor y niveles de proceso flexibles, que son idóneos para cargas de trabajo que no necesitan una capacidad de proceso completa de forma continua. El servidor flexible también admite instancias reservadas, de modo que se puede ahorrar hasta un 63 % del coste, lo que resulta ideal para las cargas de trabajo de producción con requisitos de capacidad de proceso predecibles. El servicio es compatible con la versión Community de MySQL 5.7 y 8.0. El servicio está disponible con carácter general hoy en muchas [regiones de Azure](flexible-server/overview.md#azure-regions).

La opción de implementación de servidor flexible ofrece tres niveles de proceso: ampliable, de uso general y optimizado para memoria. Cada nivel ofrece una capacidad de proceso y de memoria diferente para admitir las cargas de trabajo de base de datos. Puede compilar su primera aplicación en un nivel ampliable por poco dinero al mes y, después, ajustar la escala para satisfacer las necesidades de la solución. La escalabilidad dinámica permite a la base de datos responder de manera transparente a los cambiantes requisitos de recursos. Solo paga por los recursos que necesite y cuando los necesite. Consulte [Proceso y almacenamiento](flexible-server/concepts-compute-storage.md) para obtener más información.

Los servidores flexibles son idóneos para lo siguiente:
- Facilidad de las implementaciones, escalado simplificado y baja sobrecarga de administración de bases de datos para funciones como copias de seguridad, alta disponibilidad, seguridad y supervisión
- Desarrollos de aplicaciones que requieren la versión de la comunidad de MySQL con un mejor control y personalizaciones
- Cargas de trabajo de producción con ventanas de mantenimiento administradas y de alta disponibilidad con redundancia de zona y en la misma zona
- Experiencia de desarrollo simplificada 
- Seguridad de clase empresarial

Para obtener información general detallada sobre el modo de implementación de servidor flexible, consulte [Información general de servidor flexible](flexible-server/overview.md). Para obtener las actualizaciones más recientes en servidor flexible, consulte [Novedades de Azure Database for MySQL: servidor flexible](flexible-server/whats-new.md).

### <a name="azure-database-for-mysql---single-server"></a>Servidor único de Azure Database for MySQL 

Servidor único de Azure Database for MySQL es un servicio de base de datos totalmente administrado diseñado para una personalización mínima. La plataforma de servidor único está diseñada para administrar la mayoría de las funciones de administración de bases de datos, como las de aplicación de revisión, copias de seguridad, alta disponibilidad y seguridad con una configuración y un control mínimos para el usuario. La arquitectura está optimizada para una alta disponibilidad integrada, ya que ofrece una disponibilidad del 99,99 % en una zona de disponibilidad única. Es compatible con la versión Community de MySQL 5.6 (retirado), 5.7 y 8.0. El servicio está disponible con carácter general hoy en muchas [regiones de Azure](https://azure.microsoft.com/global-infrastructure/services/).

Los servidores únicos son más adecuados **solo para las aplicaciones existentes que ya aprovechan un servidor único**. Para todos los nuevos desarrollos o migraciones, un servidor flexible sería la opción de implementación recomendada. Para obtener información sobre las diferencias entre las opciones de implementación de servidor flexible y servidor único, consulte la documentación sobre la [selección de la opción de implementación adecuada para usted](select-right-deployment-type.md).

Para obtener información general detallada sobre el modo de implementación de servidor único, consulte [Información general de servidor único](single-server-overview.md). Para obtener las actualizaciones más recientes del servidor flexible, consulte [Novedades de Azure Database for MySQL: servidor único](single-server-whats-new.md).

## <a name="contacts"></a>Contactos
Para cualquier pregunta o sugerencia que pueda tener con respecto al uso de Azure Database for MySQL, envíe un correo electrónico al equipo de Azure Database for MySQL ([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Esta dirección de correo electrónico no es un alias de soporte técnico.

Además, tenga en cuenta los siguientes puntos de contacto según corresponda:

- Para ponerse en contacto con el servicio de soporte técnico de Azure, [presente una incidencia de soporte técnico en Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para corregir un problema con la cuenta, envíe una [solicitud de soporte técnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) en Azure Portal.
- Para proporcionar comentarios o solicitar nuevas características, cree una entrada mediante [UserVoice](https://feedback.azure.com/d365community/forum/47b1e71d-ee24-ec11-b6e6-000d3a4f0da0).

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre los dos modos de implementación de Azure Database for MySQL y elija las opciones adecuadas en función de sus necesidades.

- [Servidor único](single-server/index.yml)
- [Servidor flexible](flexible-server/index.yml)
- [Elección de la opción de implementación de MySQL adecuada para su carga de trabajo](select-right-deployment-type.md)
