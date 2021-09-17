---
title: 'Uso de una cuenta gratuita de Azure para probar Azure Database for PostgreSQL: servidor flexible de forma gratuita'
description: 'Guía sobre cómo implementar una instancia de Azure Database for PostgreSQL: servidor flexible de forma gratuita mediante una cuenta gratuita de Azure.'
author: shreyaaithal
ms.author: shaithal
ms.service: postgresql
ms.topic: how-to
ms.date: 08/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 38a1e264d3febfc114df253a24479d9580c86bb5
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123538370"
---
# <a name="use-an-azure-free-account-to-try-azure-database-for-postgresql---flexible-server-for-free"></a>Uso de una cuenta gratuita de Azure para probar Azure Database for PostgreSQL: servidor flexible de forma gratuita

Azure Database for PostgreSQL: servidor flexible (versión preliminar) es un servicio administrado que se usa para ejecutar, administrar y escalar bases de datos PostgreSQL de alta disponibilidad en la nube. Con una cuenta gratuita de Azure, puede usar la opción de servidor flexible **gratis durante 12 meses** con **límites mensuales** de hasta:
- **750 horas** de instancia **B1MS ampliable**, suficientes para ejecutar una instancia de base de datos continuamente cada mes.
- **32 GB de almacenamiento y 32 GB de almacenamiento de copia de seguridad.**

En este artículo se muestra cómo crear y usar un servidor flexible de forma gratuita mediante una [cuenta gratuita de Azure](https://azure.microsoft.com/free/). 

> [!IMPORTANT]
> Actualmente Servidor flexible de Azure Database for PostgreSQL se encuentra en versión preliminar.


## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesita:

- Una cuenta gratuita de Azure. Si no tiene ninguna, [cree una cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar. 


## <a name="create-an-azure-database-for-postgresql---flexible-server"></a>Creación de una instancia de Servidor flexible de Azure Database for PostgreSQL

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con su cuenta gratuita de Azure.
    
    La vista predeterminada es el panel del servicio.

1. Para crear una base de datos de servidor flexible PostgreSQL, busque y seleccione **Servidores de Azure Database for PostgreSQL**:

    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/select-postgresql.png" alt-text="Captura de pantalla que muestra cómo buscar y seleccionar Azure Database for PostgreSQL.":::


    Como alternativa, puede buscar **Servicios gratuitos** y desplazarse hasta allí y, luego, seleccione el icono de **Azure Database for PostgreSQL** de la lista:


    :::image type="content" source="media/how-to-deploy-on-azure-free-account/free-services-all-postgresql.png" alt-text="Captura de pantalla que muestra una lista de todos los servicios gratuitos en Azure Portal, donde se resalta PostgreSQL.":::

1. Seleccione **Crear**.

1. En la página **Select Azure Database for PostgreSQL deployment option** (Seleccionar opción de implementación de Azure Database for PostgreSQL), seleccione **Flexible Server (Preview)** (Servidor flexible [versión preliminar]).
    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/select-postgresql-deployment-option.png" alt-text="Captura de pantalla que muestra la opción de implementación de servidor flexible que se va a elegir.":::

1. Escriba la configuración básica para un nuevo **servidor flexible**.

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/basic-settings-postgresql.png" alt-text="Captura de pantalla que muestra la configuración básica para crear un servidor flexible.":::

    |Configuración    |Valor sugerido    |Descripción |
    |-------|------|------|
    |Subscription   |Nombre de la suscripción |Seleccione la suscripción de evaluación gratuita de Azure.|
    |Resource group |Su grupo de recursos    |Escriba un nuevo grupo de recursos o uno existente de la suscripción.|
    |Nombre de servidor    |mydemoserver-pgsql |Especifique un nombre único para identificar el servidor flexible. El nombre de dominio postgres.database.azure.com se anexa al nombre del servidor proporcionado. El nombre del servidor solo puede contener letras minúsculas, números y el carácter de guion (-). Debe contener entre 3 y 63 caracteres. |
    |Region |Región más cercana a los usuarios   |Seleccione una ubicación de la lista, preferiblemente la ubicación más cercana a los usuarios.|
    |Tipo de carga de trabajo  |Desarrollo    |Para una evaluación gratuita, seleccione la carga de trabajo **Desarrollo**. En el caso de la carga de trabajo de producción, elija el tamaño pequeño/mediano o grande en función de sus requisitos. |
    |Zona de disponibilidad  |Ninguna preferencia  |Si su aplicación (hospedada en las máquinas virtuales de Azure, los conjuntos de escalado de máquinas virtuales o la instancia de AKS) se aprovisiona en una zona de disponibilidad específica, cree el servidor flexible en la misma zona de disponibilidad. La colocación de la aplicación y la base de datos en la misma ubicación mejora el rendimiento al reducir la latencia de red entre zonas. Si elige **Sin preferencias**, se selecciona automáticamente una zona de disponibilidad predeterminada.|
    |Versión de PostgreSQL |La versión principal más reciente   |Use la versión principal más reciente de PostgreSQL, a menos que tenga requisitos específicos.|
    |Alta disponibilidad  |Valor predeterminado    |Deje desactivada la opción Alta disponibilidad.|
    |Nombre de usuario administrador |myadmin    |Cree una cuenta de inicio de sesión para usarla al conectarse al servidor. El nombre de usuario de administrador no puede comenzar con **pg_** y no puede ser **azure_superuser, azure_pg_admin, admin, administrator, root, guest,** o **public**. |
    |Contraseña   |La contraseña  |Especifique una contraseña para la cuenta de administrador del servidor. Debe tener entre 8 y 128 caracteres. También debe contener caracteres de tres de las cuatro categorías siguientes: Letras del alfabeto inglés mayúsculas y minúsculas, números (0 a 9) y caracteres no alfanuméricos (!, $, #, %, etc.).|

1. En la opción **Proceso y almacenamiento**, tras seleccionar el tipo de carga de trabajo **Desarrollo**, mantenga los valores predeterminados rellenados. 
    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/compute-storage-default-postgresql.png" alt-text="Captura de pantalla que muestra los valores predeterminados para la configuración de proceso y almacenamiento.":::

    Seleccione **Configurar servidor** para revisar y personalizar la opción **Proceso y almacenamiento**.
    
    Asegúrese de seleccionar la instancia B1MS ampliable (1 o 2 núcleos virtuales), especifique que se incluya el almacenamiento de 32 GB o inferior y mantenga la configuración predeterminada del resto de opciones.

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/compute-storage-postgresql.png" alt-text="Captura de pantalla que muestra la hoja Configurar servidor de Proceso y almacenamiento para elegir la SKU de B1MS y el almacenamiento de 32 GB.":::

    Seleccione **Guardar** para continuar con la configuración.

1.  Seleccione la pestaña **Redes** para configurar el modo de acceso al servidor.

    Azure Database for PostgreSQL: servidor flexible proporciona dos formas de conectarse:
    - Acceso público (direcciones IP permitidas)
    - Acceso privado (integración con red virtual)
    
    Con el acceso público, el acceso al servidor se limita a las direcciones IP permitidas que incluye en una regla de firewall. Este método evita que herramientas y aplicaciones externas se conecten al servidor o a las bases de datos de este, a menos que cree una regla para abrir el firewall para una dirección IP o un intervalo específico.
    
    Con el acceso privado (integración con red virtual), el acceso al servidor se limita a la red virtual. Para más información sobre los métodos de conectividad, consulte Introducción a las redes.
    
    Siguiendo este tutorial, habilite el acceso público para conectarse al servidor.

1. En la pestaña **Redes**, en **Método de conectividad**, seleccione **Acceso público**.

1. Para la configuración de **Reglas de firewall**, seleccione **Agregar dirección IP del cliente actual**.
    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/networking-postgresql.png" alt-text="Captura de pantalla que muestra las opciones de redes que se elegirán y resalta el botón Agregar dirección IP del cliente actual.":::

1. Seleccione **Revisar y crear** para revisar la configuración del servidor flexible.

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/review-create-postgresql.png" alt-text="Captura de pantalla que muestra la hoja Revisar y crear.":::

    >[!IMPORTANT]
    >Al crear la instancia de servidor flexible a partir de la cuenta gratuita de Azure, verá un **costo estimado al mes** en la hoja **Proceso y almacenamiento: Resumen del costo** y la pestaña **Revisar y crear**. Sin embargo, siempre que use su cuenta gratuita de Azure y el uso del servicio gratuito esté dentro de los límites mensuales (para ver la información de uso, consulte la sección [**Supervisión y seguimiento del uso de los servicios gratuitos**](#monitor-and-track-free-services-usage) a continuación), no se le cobrará por el servicio. Actualmente estamos trabajando para mejorar la experiencia de **resumen de costos** para los servicios gratuitos. 

1. Seleccione **Crear** para realizar el aprovisionamiento del servidor.

    El aprovisionamiento puede tardar unos minutos.

1.  En la barra de herramientas, seleccione **Notificaciones** (icono de campana) para supervisar el proceso de implementación.

    Una vez realizada la implementación, puede seleccionar **Anclar al panel** para crear un icono para el servidor flexible en el panel de Azure Portal. Este icono es un acceso directo a la página de **información general** del servidor. Al seleccionar **Ir al recurso**, se abre la página **Información general** del servidor.

    De forma predeterminada, una base de datos de **postgres** se crea en el servidor. La base de datos de postgres es una base de datos predeterminada pensada para que la usen los usuarios, las utilidades y aplicaciones de otros fabricantes. (La otra base de datos predeterminada es **azure_maintenance**. Su función consiste en separar los procesos de servicio administrados de las acciones del usuario. No se puede acceder esta base de datos).


## <a name="connect-and-query"></a>Conexión y consultas

Ahora que ha creado un servidor flexible de Azure Database for PostgreSQL en un grupo de recursos, puede conectarse a bases de datos de servidor y de consulta mediante los siguientes inicios rápidos de conexión y consulta:
- [psql](quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql)
- [CLI de Azure](connect-azure-cli.md)
- [Python](connect-python.md)
- [Java](connect-java.md)
- [.NET](connect-csharp.md)
- [Conexión a un servidor en una red virtual](quickstart-create-connect-server-vnet.md)


## <a name="monitor-and-track-free-services-usage"></a>Supervisión y seguimiento del uso de los servicios gratuitos

No se le cobrará por los servicios de Azure Database for PostgreSQL: servidor flexible que se incluyan de forma gratuita con su cuenta gratuita de Azure, a menos que supere los límites de estos servicios. Para permanecer dentro de los límites, use Azure Portal para realizar el seguimiento y la supervisión del uso de los servicios gratuitos.

1.  En Azure Portal, busque **Suscripciones** y seleccione la suscripción **Evaluación gratuita** de la cuenta de Azure gratuita.
1.  En la página **Información general**, desplácese hacia abajo para mostrar el icono **Top free services by usage** (Principales servicios gratuitos por uso) y, a continuación, seleccione **View all free services** (Ver todo servicios gratuitos).

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/free-services-usage-overview.png" alt-text="Captura de pantalla que muestra la página de información general de la suscripción de evaluación gratuita con la opción de ver todos los servicios gratuitos resaltada.":::

1. Busque los medidores relacionados con **Azure Database for PostgreSQL: servidor flexible** para realizar un seguimiento del uso:

    |Medidor  |Descripción    |Límite mensual|
    |-------|---------|-------|
    |Azure Database for PostgreSQL, proceso de la serie BS ampliable de servidor flexible, B1MS   |Realiza un seguimiento del uso del proceso en términos de número de horas de ejecución    |750 horas al mes: nivel de proceso B1MS ampliable|
    |Azure Database for PostgreSQL, almacenamiento de servidor flexible, datos almacenados    |Realiza un seguimiento del almacenamiento de datos aprovisionado en términos de GB usados al mes  |32 GB al mes|

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/free-services-tracking.png" alt-text="Captura de pantalla que muestra la hoja de visualización y seguimiento de la información de uso en Azure Portal para todos los servicios gratuitos.":::

    - Medidor: identifica la unidad de medida del recurso que se consume.
    - Uso y límite: uso y límite del mes actual para el medidor.
    - Estado: estado de uso del servicio. En función del uso, puede tener uno de los siguientes estados:
    - No está en uso: no ha usado el medidor o el uso de este no llega al sistema de facturación.
    - Lo ha superado el \<Date\>: ha superado el límite del medidor el \<Date\>.
    - Superación improbable: es poco probable que supere el límite del medidor.
    - Lo superará el \<Date\>: Es probable que supere el límite del medidor el \<Date\>.

    >[!IMPORTANT]
    >Junto con la cuenta gratuita de Azure, obtendrá un saldo de 200 USD para gastar en un plazo de 30 días. Durante este tiempo, cualquier uso que supere las cantidades mensuales gratuitas de servicios se deducirá de este crédito.
    >Al final de los primeros 30 días o después de gastar el crédito de 200 USD (lo que pase primero), solo pagará por lo que use más allá de las cantidades mensuales gratuitas de servicios. Para seguir obteniendo servicios gratuitos después de 30 días, cambie a una tarifa de pago por uso. Si no cambia al pago por uso, no podrá comprar servicios de Azure más allá del crédito de 200 USD y, finalmente, la cuenta y los servicios se deshabilitarán.
    >Para más información consulte las [**preguntas frecuentes sobre la cuenta gratuita de Azure**](https://azure.microsoft.com/free/free-account-faq/).


## <a name="clean-up-resources"></a>Limpieza de recursos
    
Si usa el servidor flexible para desarrollo, pruebas o cargas de trabajo de producción predecibles con duración definida, inicie y detenga el servidor a petición para optimizar el uso. Después de detener el servidor, permanece en ese estado durante siete días, a menos que se reinicie antes. Para obtener más información, consulte Iniciar o detener el servidor para reducir el TCO. Cuando el servidor flexible se detiene, no hay ningún uso de proceso, pero todavía se tiene en cuenta el uso de almacenamiento.

Como alternativa, si no cree que vaya a necesitar estos recursos en el futuro, elimine el grupo de recursos para eliminarlos o bien elimine simplemente el servidor PostgreSQL.

- Para eliminar el grupo de recursos, siga estos pasos:
    1. En Azure Portal, busque y seleccione **Grupos de recursos**.
    1. En la lista, seleccione el nombre de su grupo de recursos.
    1. En la página **Información general** del grupo de recursos, seleccione **Eliminar grupo de recursos**.
    1. En el cuadro de diálogo de confirmación, escriba el nombre del grupo de recursos y seleccione **Eliminar**.
    
- Para eliminar el servidor PostgreSQL, en la página **Información general** del servidor, seleccione **Eliminar**.
 


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
>[Implementación de una aplicación de Django con App Service y PostgreSQL](tutorial-django-app-service-postgres.md)

