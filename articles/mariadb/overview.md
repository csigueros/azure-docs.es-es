---
title: 'Introducción: Azure Database for MariaDB'
description: Obtenga más información acerca de Azure Database for MariaDB, que es un servicio de base de datos relacional de Microsoft Cloud basado en MariaDB Community Edition.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: overview
ms.custom: mvc
ms.date: 3/18/2020
ms.openlocfilehash: bf00ecfe947f8d34f8bd9a2520d48140fe876550
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/28/2021
ms.locfileid: "114726916"
---
# <a name="what-is-azure-database-for-mariadb"></a>¿Qué es Azure Database for MariaDB?

Azure Database for MariaDB es un servicio de base de datos relacional en la nube de Microsoft. Azure Database for MariaDB usa la versión 10.2 y 10.3 del motor de base de datos de [MariaDB Community Edition](https://mariadb.org/download/) (disponible bajo la licencia de GPLv2).

Azure Database for MariaDB proporciona:

- Alta disponibilidad integrada sin coste adicional.
- Rendimiento predecible, con precios de pago por uso inclusivos.
- Escalado según sea necesario en cuestión de segundos.
- Protección segura de los datos confidenciales en reposo y en movimiento.
- Copias de seguridad automáticas y restauración a un momento dado durante un máximo de 35 días.
- Seguridad y cumplimiento de nivel empresarial.

Estas funcionalidades no requieren casi administración. Se proporcionan sin costo adicional. Azure Database for MariaDB puede ayudarle a desarrollar su aplicación rápidamente y a acelerar el tiempo de comercialización. No es necesario dedicarle un tiempo precioso y recursos a administrar máquinas virtuales e infraestructura. También puede seguir desarrollando la aplicación mediante las herramientas de código abierto y la plataforma de su elección. Entregue con la rapidez y la eficiencia demandadas por su empresa, sin tener que aprender nuevas habilidades.

Para información sobre los principales conceptos y características de Azure Database for MariaDB, incluido el rendimiento, la escalabilidad y la capacidad de administración, consulte estas guías de inicio rápido:

- [Creación de un servidor de Azure Database for MariaDB mediante Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Creación de un servidor de Azure Database for MariaDB mediante la CLI de Azure](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajustar el rendimiento y la escala en cuestión de segundos

El servicio Azure Database for MariaDB ofrece varios niveles de servicio: Básico, Uso general y Optimizada para memoria. Cada nivel ofrece diferentes niveles de rendimiento y funcionalidades para admitir cargas de trabajo de bases de datos, de ligeras a pesadas. Puede compilar su primera aplicación en una base de datos pequeña por poco dinero al mes y, después, ajustar la escala para satisfacer las necesidades de la solución. La escalabilidad dinámica ayuda a la base de datos a responder de manera transparente a los cambiantes requisitos de recursos. Solo paga por los recursos que necesite y cuando los necesite. Vea los [Planes de tarifa](concepts-pricing-tiers.md) para obtener más información.

## <a name="monitoring-and-alerting"></a>Supervisión y alertas

¿Cómo se decide cuándo escalar o reducir verticalmente? Utilice las características de supervisión y alertas de rendimiento integradas de Azure Database for MariaDB, combinadas con la clasificación del rendimiento basado en los núcleos virtuales. Con estas herramientas puede evaluar rápidamente el impacto de ajustar la escala de los núcleos virtuales en función de sus necesidades de rendimiento actuales o futuras. Vea las [Alertas](howto-alert-metric.md) para obtener más información.

## <a name="keep-your-app-and-business-running"></a>Mantenimiento de la aplicación y el negocio en funcionamiento

El Acuerdo de Nivel de Servicio de Azure, con una disponibilidad líder del sector del 99,99 %, se refuerza con la tecnología de una red global de centros de datos administrados por Microsoft. La red ayuda a mantener las aplicaciones en funcionamiento permanentemente. Podrá beneficiarse de la seguridad integrada, la tolerancia a errores y la protección de datos en Azure Database for MariaDB. Con Azure Database for MariaDB puede usar la restauración a un momento dado para recuperar un servidor a un estado anterior, con un plazo máximo de 35 días.

## <a name="secure-your-data"></a>Protección de los datos

Los servicios de base de datos de Azure tienen una tradición de seguridad de datos que Azure Database for MariaDB mantiene. Azure Database for MariaDB ofrece características que limitan el acceso, prtegen los datos en reposo y en movimiento, y le ayudan a supervisar la actividad. Para obtener más información sobre la seguridad de plataforma de Azure, vaya al [Centro de confianza de Azure](https://www.microsoft.com/trustcenter/security). Para más información acerca de las características de seguridad de Azure Database for MariaDB, consulte la [introducción a la seguridad](concepts-security.md).

## <a name="contacts"></a>Contactos

Envíe cualquier pregunta o sugerencia que pueda tener con respecto al uso de Azure Database for MariaDB al [equipo de Azure Database for MariaDB](mailto:AskAzureDBforMariaDB@service.microsoft.com) (no a un alias del soporte técnico).

También puede usar los siguientes puntos de contacto:
- Para ponerse en contacto con soporte técnico de Azure, [abra una solicitud de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) en Azure Portal.
- Para corregir los problemas con la cuenta, [abra una solicitud de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) en Azure Portal.
- Para proporcionar comentarios o solicitar nuevas características, cree una entrada en los [foros de comentarios de Azure](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha leído la introducción a Azure Database for MariaDB, ya está listo para:
- Ver la página de [precios](https://azure.microsoft.com/pricing/details/mariadb/) para comparaciones de costos y calculadoras. 
- Comenzar por [crear el primer servidor](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
