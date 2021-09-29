---
title: 'Novedades de Azure Database for MySQL: servidor flexible'
description: 'Conozca las actualizaciones recientes de Azure Database for MySQL: servidor flexible, un servicio en la nube de base de datos relacional de Microsoft basado en MySQL Community Edition.'
author: hjtoland3
ms.service: mysql
ms.author: jtoland
ms.custom: mvc
ms.topic: conceptual
ms.date: 08/17/2021
ms.openlocfilehash: cfbff4be8048090ec606fd8640281dccd17fe084
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "128598011"
---
# <a name="whats-new-in-azure-database-for-mysql---flexible-server-preview"></a>Novedades de Azure Database for MySQL: servidor flexible (versión preliminar)

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

[Azure Database for MySQL: servidor flexible](./overview.md#azure-database-for-mysql---flexible-server-preview) es un modo de implementación diseñado para proporcionar un control más granular y una mayor flexibilidad sobre las funciones de administración de bases de datos y las opciones de configuración, si se compara con el modo de implementación de servidor único. El servicio admite actualmente la versiones de la comunidad de MySQL 5.7 y 8.0.

En este artículo se resumen las nuevas versiones y características de Azure Database for MySQL: servidor flexible a partir de enero de 2021. Las listas aparecen en orden cronológico inverso, con las actualizaciones más recientes en primer lugar.

## <a name="august-2021"></a>Agosto de 2021

Esta versión de Azure Database for MySQL: servidor flexible incluye las siguientes actualizaciones.

- **Alta disponibilidad dentro de una sola zona mediante alta disponibilidad en la misma zona**

  El servicio ahora brinda a los clientes la flexibilidad de elegir la zona de disponibilidad preferida para su servidor en espera cuando habilitan la alta disponibilidad. Con esta característica, los clientes pueden colocar un servidor en espera en la misma zona que el servidor principal, lo que reduce el retraso de replicación entre el servidor principal y el que está en espera. Esto también logra latencias inferiores entre el servidor de aplicaciones y el servidor de bases de datos si se colocan dentro de la misma zona de Azure. [Más información](/azure/mysql/flexible-server/concepts-high-availability#same-zone-high-availability).

- **Selección de zona en espera con alta disponibilidad con redundancia de zona**

  El servicio ahora brinda a los clientes la capacidad de elegir la ubicación de la zona del servidor en espera. Con esta característica, los clientes pueden colocar el servidor en espera en la zona que prefieran. La colocación de los servidores de bases de datos y las aplicaciones en espera en la misma zona reduce las latencias y permite a los clientes prepararse mejor para situaciones de recuperación ante desastres y escenarios de "zona fuera de servicio". [Más información](/azure/mysql/flexible-server/concepts-high-availability#standby-zone-selection).

- **Integración de zonas DNS privadas**

  [Azure DNS privado](../../dns/private-dns-privatednszone.md) ofrece un servicio DNS confiable y seguro (responsable de traducir un nombre de servicio a una dirección IP) para la red virtual. Azure Private DNS administra y resuelve los nombre de dominio en la red virtual sin necesidad de configurar una solución DNS personalizada. Esto le permite conectar la aplicación que se ejecuta en una red virtual al servidor flexible que se ejecuta en una red virtual emparejada local o globalmente. Azure Database for MySQL: servidor flexible ahora ofrece integración con una zona Azure DNS privada para permitir una resolución sin problemas del DNS privado dentro de la red virtual actual o de cualquier red virtual emparejada a la que esté vinculada la zona DNS privada. Con esta integración, si la dirección IP del servidor flexible de back-end cambia durante la conmutación por error o cualquier otro evento, la zona DNS privada integrada se actualizará automáticamente para asegurarse de que la conectividad de la aplicación se reanude automáticamente una vez que el servidor esté en línea. [Más información](./concepts-networking-vnet.md).

- **Restauración a un momento dado para un servidor de una red virtual especificada**

  La experiencia de restauración a un momento dado para el servicio ahora permite a los clientes configurar las opciones de red, lo que permite a los usuarios cambiar entre las opciones de red pública y privada al realizar una operación de restauración. Esta característica da a los clientes la flexibilidad de insertar un servidor que se está restaurando en una red virtual especificada, lo que protege sus puntos de conexión. [Más información](./how-to-restore-server-portal.md).

- **Restauración a un momento dado para un servidor en una zona de disponibilidad**

  La experiencia de restauración a un momento dado para el servicio ahora permite a los clientes configurar la zona de disponibilidad. Colocalizar los servidores de bases de datos y las aplicaciones en espera en la misma zona reduce las latencias y permite a los clientes prepararse mejor para situaciones de recuperación ante desastres y escenarios de "zona fuera de servicio". [Más información](/azure/mysql/flexible-server/concepts-high-availability#standby-zone-selection).

- **Complemento validate_password y caching_sha2_password disponibles en versión preliminar privada**

  Servidor flexible ahora admite la habilitación de los complementos validate_password y caching_sha2_password en versión preliminar privada. Envíenos un correo electrónico a AskAzureDBforMySQL@service.microsoft.com.

- **Disponibilidad en cuatro regiones de Azure adicionales**

  La versión preliminar pública de Azure Database for MySQL: servidor flexible ya está disponible en las siguientes regiones de Azure. [Más información](overview.md#azure-regions):

  - Sudeste de Australia
  - Norte de Sudáfrica
  - Asia Oriental (Hong Kong)
  - Centro de la India

- **Problema conocido**

  - Inmediatamente después de la conmutación por error del  servidor de alta disponibilidad con redundancia de zona, los clientes no se pueden conectar al servidor si usan SSL con ssl_mode VERIFY_IDENTITY. Este problema se puede mitigar si se usa ssl_mode como VERIFY_CA.
  - No se puede crear el servidor de alta disponibilidad en la misma zona en las siguientes regiones: Centro de la India, Este de Asia, Centro de Corea del Sur, Norte de Sudáfrica, Norte de Suiza.
  - En un escenario poco frecuente y después de la conmutación por error de alta disponibilidad, el servidor principal estará en modo read_only. Para resolver el problema, actualice el valor "read_only" de la hoja de parámetros del servidor a OFF.
  - Después de escalar correctamente Proceso en la hoja Proceso y almacenamiento, las IOPS se restablecen al valor predeterminado de SKU. Para evitar el problema, los clientes pueden volver a escalar las IOPS en la hoja Proceso y almacenamiento al valor deseado (establecido previamente) después de la implementación de proceso y el consiguiente restablecimiento de las IOPS.

## <a name="july-2021"></a>Julio de 2021

Esta versión de Azure Database for MySQL: servidor flexible incluye las siguientes actualizaciones.

- **Migración en línea de servidor único a servidor flexible**

  Los clientes ahora pueden migrar una instancia de Azure Database for MySQL: servidor único a servidor flexible con un tiempo de inactividad mínimo para las aplicaciones mediante Replicación de datos de entrada. Para obtener instrucciones detalladas y paso a paso, consulte [Migración de Azure Database for MySQL: servidor único a servidor flexible con tiempo de inactividad mínimo](../howto-migrate-single-flexible-minimum-downtime.md).

- **Disponibilidad en Oeste de EE. UU. y Centro-oeste de Alemania**

  La versión preliminar pública de Azure Database for MySQL: servidor flexible ya está disponible en las regiones Oeste de EE. UU. y Centro-oeste de Alemania.

## <a name="june-2021"></a>Junio de 2021

Esta versión de Azure Database for MySQL: servidor flexible incluye las siguientes actualizaciones.

- **Rendimiento mejorado en servidores de almacenamiento más pequeños**

    A partir del 21 de junio de 2021, el tamaño de almacenamiento aprovisionado mínimo permitido para todos los servidores recién creados aumenta de 5 GB a 20 GB. Además, las IOPS gratuitas disponibles aumentan de 100 a 300. Estos cambios se resumen en la siguiente tabla:

    | **Current** | **A partir del 21 de junio de 2021** |
    |:----------|:----------|
    | Tamaño de almacenamiento mínimo permitido: 5 GB | Tamaño de almacenamiento mínimo permitido: 20 GB |
    | IOPS disponible: Máx(100, 3 * [almacenamiento aprovisionado en GB]) | IOPS disponible: Máx(300, 3 * [almacenamiento aprovisionado en GB]) |

- **Oferta de 12 meses gratis**

  A partir del 15 de junio de 2021, la [cuenta gratis de Azure](https://azure.microsoft.com/free/) brinda a los clientes hasta 12 meses de acceso gratis a Azure Database for MySQL: servidor flexible, con 750 horas de uso y 32 GB de almacenamiento al mes. Los clientes pueden aprovechar esta oferta para desarrollar e implementar aplicaciones que usen Azure Database for MySQL: servidor flexible. [Más información](./how-to-deploy-on-azure-free-account.md).

- **Crecimiento automático del almacenamiento**

  El crecimiento automático del almacenamiento impide que un servidor se quede sin almacenamiento y se vuelva de solo lectura. Si el crecimiento automático del almacenamiento está habilitado, el almacenamiento crece automáticamente sin afectar a la carga de trabajo. A partir del 21 de junio de 2021, todos los servidores recién creados tendrán habilitado el crecimiento automático del almacenamiento de manera predeterminada. [Más información](concepts-compute-storage.md#storage-auto-grow).

- **Replicación de datos de entrada**

    Servidor flexible ahora admite [Replicación de datos de entrada](concepts-data-in-replication.md). Use esta característica para sincronizar y migrar datos a Azure Database for MySQL: servidor flexible desde un servidor MySQL que se ejecuta localmente, en máquinas virtuales, en Azure Database for MySQL: servidor único, o en servicios de base de datos fuera de Azure. Más información sobre la [Configuración de Replicación de datos de entrada](how-to-data-in-replication.md).

- **Compatibilidad de acciones de GitHub con la CLI de Azure**

  La CLI de servidor flexible ahora permite a los clientes automatizar los flujos de trabajo para implementar actualizaciones con acciones de GitHub. Esta característica ayuda a configurar e implementar actualizaciones de base de datos con el flujo de trabajo MySQL en acciones de GitHub. Estos comandos de la CLI ayudan a configurar un repositorio para habilitar la implementación continua para facilitar el desarrollo. [Más información](/cli/azure/mysql/flexible-server/deploy?view=azure-cli-latest&preserve-view=true).

- **Correcciones de conmutación por error forzada de alta disponibilidad con redundancia de zona**

  Esta versión incluye correcciones para problemas conocidos relacionados con la conmutación por error forzada para asegurarse de que los parámetros del servidor y los cambios de IOPS adicionales se conserven entre las conmutaciones por error.

- **Problema conocido**

  - El intento de realizar una operación de escalado o reducción verticales de proceso en un servidor existente con menos de 20 GB de almacenamiento aprovisionado no se completará correctamente. Para resolver el problema, escale verticalmente el almacenamiento aprovisionado a 20 GB y vuelve a intentar la operación de escalado de proceso.

## <a name="may-2021"></a>Mayo de 2021

Esta versión de Azure Database for MySQL: servidor flexible incluye las siguientes actualizaciones.

- **Disponibilidad regional extendida (Centro de Francia, Sur de Brasil y Norte de Suiza)**

    La versión preliminar pública de Azure Database for MySQL: servidor flexible ya está disponible en las regiones Centro de Francia, Sur de Brasil y Norte de Suiza. [Más información](overview.md#azure-regions).

- **Se puede deshabilitar el cumplimiento de SSL/TLS 1.2**

   Esta versión proporciona la flexibilidad mejorada para personalizar el cumplimiento de SSL y la versión mínima de TLS. Para más información, consulte [Conexión al servidor flexible de Azure Database for MySQL con conexiones cifradas](how-to-connect-tls-ssl.md).

- **Alta disponibilidad con redundancia de zona disponible en las regiones Sur de Reino Unido y Este de Japón**

   Azure Database for MySQL: servidor flexible ahora ofrece alta disponibilidad con redundancia de zona en dos regiones adicionales: Sur de Reino Unido y Este de Japón. [Más información](overview.md#azure-regions).

- **Problemas conocidos**

  - Los cambios de IOPS adicionales no surten efecto en los servidores habilitados con alta disponibilidad con redundancia de zona. Para evitar el problema, los clientes pueden deshabilitar la alta disponibilidad, escalar las IOPS y volver a habilitar la alta disponibilidad con redundancia de zona.
  - Después de forzar la conmutación por error, la zona de disponibilidad en espera no se refleja de manera exacta en el portal. (No hay solución alternativa)
  - Los cambios en los parámetros del servidor no surten efecto en el servidor habilitado para la alta disponibilidad con redundancia de zona después de la conmutación por error forzada. (No hay solución alternativa)

## <a name="april-2021"></a>Abril de 2021

Esta versión de Azure Database for MySQL: servidor flexible incluye las siguientes actualizaciones.

- **Se lanzó la capacidad de forzar la conmutación por error en el servidor en espera con alta disponibilidad con redundancia de zona**

  Ahora los clientes pueden forzar manualmente una conmutación por error para probar la funcionalidad en los escenarios de su aplicación, lo que puede ayudarles a prepararse en caso de interrupciones. [Más información](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/forced-failover-for-azure-database-for-mysql-flexible-server/ba-p/2280671).

- **Se publicó el módulo de PowerShell para servidor flexible**

  Los desarrolladores ahora pueden usar PowerShell para aprovisionar, administrar, operar y admitir servidores flexibles de MySQL y recursos dependientes. [Más información](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/introducing-the-mysql-flexible-server-powershell-module/ba-p/2203383).

- **Conectar, probar y ejecutar consultas mediante la CLI de Azure**

  Azure Database for MySQL: servidor flexible ahora brinda una experiencia de desarrollo mejorada que permite a los clientes conectarse y ejecutar consultas a sus servidores mediante la CLI de Azure con los comandos "az mysql flexible-server connect" y "az mysql flexible-server execute". [Más información](connect-azure-cli.md#view-all-the-arguments).

- **Correcciones de errores de aprovisionamiento para la creación de servidores en una red virtual con acceso privado**

  Se han corregido todos los errores de aprovisionamiento causados al crear un servidor en la red virtual. Con esta versión, los usuarios pueden crear correctamente servidores flexibles con acceso privado en todo momento.  

## <a name="march-2021"></a>Marzo de 2021

Esta versión de Azure Database for MySQL: servidor flexible incluye las siguientes actualizaciones.

- **Se publicó MySQL 8.0.21**

  MySQL 8.0.21 ahora está disponible en Servidor flexible en todas las [regiones de Azure](overview.md#azure-regions) principales. Los clientes pueden usar Azure Portal, la CLI de Azure o plantillas de Azure Resource Manager para aprovisionar MySQL, versión 8.0.21. [Más información](quickstart-create-server-portal.md#create-an-azure-database-for-mysql-flexible-server).

- **Se publicó la compatibilidad con la selección de ubicación de la zona de disponibilidad durante la creación de un servidor**

  Ahora los clientes pueden especificar su zona de disponibilidad preferida en el momento de crear el servidor. Esta funcionalidad permite a los clientes colocar sus aplicaciones hospedadas en VM de Azure, conjuntos de escalado de máquinas virtuales o AKS y bases de datos en las mismas zonas de disponibilidad para minimizar la latencia de las bases de datos y mejorar el rendimiento. [Más información](quickstart-create-server-portal.md#create-an-azure-database-for-mysql-flexible-server).

- **Correcciones de rendimiento para problemas al ejecutar un servidor flexible en una red virtual con acceso privado**

  Antes de esta versión, el rendimiento del servidor flexible se degradaba significativamente al ejecutarse en una configuración de red virtual. Esta versión incluye las correcciones para el problema, lo que permitirá que los usuarios tengan un rendimiento mejorado en un servidor flexible en la red virtual.

- **Problemas conocidos**

  - Se aplica SSL\TLS 1.2 y no se puede deshabilitar. (No hay soluciones alternativas)
  - Hay errores de aprovisionamiento intermitentes en los servidores aprovisionados en una red virtual. La solución alternativa es reintentar el aprovisionamiento del servidor hasta que se ejecute correctamente.

## <a name="february-2021"></a>Febrero de 2021

Esta versión de Azure Database for MySQL: servidor flexible incluye las siguientes actualizaciones.

- **Se publicó una característica de IOPS adicional**

  Azure Database for MySQL: servidor flexible admite el aprovisionamiento de [IOPS](concepts-compute-storage.md#iops) adicionales, de manera independiente del almacenamiento aprovisionado. Los clientes pueden usar esta característica para aumentar o disminuir el número de IOPS en cualquier momento en función de sus requisitos de carga de trabajo.

- **Problemas conocidos**

  El rendimiento de Azure Database for MySQL: servidor flexible se degrada con el aislamiento de red virtual de acceso privado (no hay solución alternativa).

## <a name="january-2021"></a>Enero de 2021

Esta versión de Azure Database for MySQL: servidor flexible incluye las siguientes actualizaciones.

- **Hasta 10 réplicas de lectura para MySQL: servidor flexible**

  Servidor flexible ahora admite la replicación asincrónica de datos desde un servidor de Azure Database for MySQL (el "origen") hasta 10 servidores de Azure Database for MySQL (las "réplicas") en la misma región. Esta funcionalidad permite que las cargas de trabajo con lectura intensiva se escalen horizontalmente y se equilibren entre los servidores de réplica según las preferencias del usuario. [Más información](concepts-read-replicas.md).

## <a name="contacts"></a>Contactos

Si tiene preguntas o sugerencias sobre el trabajo con Azure Database for MySQL, considere los siguientes puntos de contacto según corresponda:

- Para ponerse en contacto con el servicio de soporte técnico de Azure, [presente una incidencia de soporte técnico en Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para corregir un problema con la cuenta, envíe una [solicitud de soporte técnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) en Azure Portal.
- Para proporcionar comentarios o solicitar nuevas características, envíenos un correo electrónico a AskAzureDBforMySQL@service.microsoft.com.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre los [precios de Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/server/).
- Examine la [documentación pública](index.yml) de Azure Database for MySQL: servidor flexible.
- Revise los detalles sobre la [solución de errores de migración comunes](../howto-troubleshoot-common-errors.md).