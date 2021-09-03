---
title: 'Configuración de parámetros de servidor de Azure Database for PostgreSQL: Servidor flexible mediante Azure Portal'
description: En este artículo se explica cómo configurar los parámetros de Postgres en Azure Database for PostgreSQL con la opción Servidor flexible mediante Azure Portal.
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.topic: how-to
ms.date: 08/04/2021
ms.openlocfilehash: 1ca1501f790f914412fa61fce5ceed5ba45130b2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736396"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---flexible-server-via-the-azure-portal"></a>Configuración de parámetros de servidor en Azure Database for PostgreSQL: Servidor flexible mediante Azure Portal 
Puede enumerar, mostrar y actualizar los parámetros de configuración de un servidor de Azure Database for PostgreSQL a través de Azure Portal.

## <a name="prerequisites"></a>Prerequisites
Para seguir esta guía, necesitará:
- [Azure Database for PostgreSQL con la opción Servidor flexible](quickstart-create-server-portal.md)

## <a name="viewing-and-editing-parameters"></a>Ver y editar parámetros
1. Abra [Azure Portal](https://portal.azure.com).

2. Seleccione su servidor de Azure Database for PostgreSQL.

3. En la sección **CONFIGURACIÓN**, seleccione **Parámetros del servidor**. La página muestra una lista de parámetros, sus valores y descripciones.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png" alt-text="Página de información general de parámetros":::

4. Seleccione el botón **desplegable** para ver los posibles valores para los parámetros de tipo enumerado, como client_min_messages.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png" alt-text="Menú desplegable de enumeración":::

5. Seleccione o mantenga el puntero sobre el botón **i** (información) para ver el rango de valores posibles para los parámetros numéricos, como cpu_index_tuple_cost.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-information-button.png" alt-text="botón de información":::

6. Si es necesario, utilice el **cuadro de búsqueda** para reducir a un parámetro específico. Se busca en el nombre y la descripción de los parámetros.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/5-search.png" alt-text="Resultados de la búsqueda":::

7. Cambie los valores de los parámetros que le gustaría ajustar. Todos los cambios que realiza en una sesión se resaltan en color púrpura. Una vez que haya cambiado los valores, puede seleccionar **Guardar**. O bien puede **descartar** los cambios.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png" alt-text="Guardar o descartar cambios":::

8. Si ha guardado los nuevos valores para los parámetros, siempre puede revertir todos los elementos a los valores predeterminados; para ello, seleccione **Restablecer todos los valores predeterminados**.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png" alt-text="Restablecer todos los valores predeterminados":::

## <a name="working-with-time-zone-parameters"></a>Trabajo con el parámetro de zona horaria
Si planea trabajar con datos de fecha y hora en PostgreSQL, debe asegurarse de que ha establecido la zona horaria correcta para su ubicación. Todas las fechas y horas que reconocen la zona horaria se almacenan internamente en Postgres en formato UTC. Se convierten a la hora local en la zona especificada mediante el parámetro de servidor **TimeZone** antes de mostrarse al cliente.  Este parámetro se puede editar en la página **Parámetros del servidor** como se explicó anteriormente. PostgreSQL permite especificar zonas horarias de tres formas diferentes:
1. Un nombre de zona horaria completo, por ejemplo, América/Nueva_York. Los nombres de zona horaria reconocidos se muestran en la vista [**pg_timezone_names**](https://www.postgresql.org/docs/9.2/view-pg-timezone-names.html).  
   Ejemplo para consultar esta vista en psql y obtener una lista de nombres de zona horaria:
   <pre>select name FROM pg_timezone_names LIMIT 20;</pre>

   Verá un conjunto de resultados parecido al siguiente:

   <pre>
            name
        -----------------------
        GMT0
        Iceland
        Factory
        NZ-CHAT
        America/Panama
        America/Fort_Nelson
        America/Pangnirtung
        America/Belem
        America/Coral_Harbour
        America/Guayaquil
        America/Marigot
        America/Barbados
        America/Porto_Velho
        America/Bogota
        America/Menominee
        America/Martinique
        America/Asuncion
        America/Toronto
        America/Tortola
        America/Managua
        (20 rows)
    </pre>
   
2. Abreviatura de zona horaria, por ejemplo, PST. Esta especificación simplemente define una diferencia horaria particular con UTC, al contrario que los nombres de zona horaria completos, que también pueden implicar un conjunto de reglas de fecha de transición del horario de verano. Las abreviaturas reconocidas se muestran en el ejemplo de la [**vista pg_timezone_abbrevs**](https://www.postgresql.org/docs/9.4/view-pg-timezone-abbrevs.html) para consultar esta vista en psql y obtener la lista de abreviaturas de zona horaria:

   <pre> select abbrev from pg_timezone_abbrevs limit 20;</pre>

    Verá un conjunto de resultados parecido al siguiente:

     <pre>
        abbrev|
        ------+
        ACDT  |
        ACSST |
        ACST  |
        ACT   |
        ACWST |
        ADT   |
        AEDT  |
        AESST |
        AEST  |
        AFT   |
        AKDT  |
        AKST  |
        ALMST |
        ALMT  |
        AMST  |
        AMT   |
        ANAST |
        ANAT  |
        ARST  |
        ART   |
    </pre>

3. Además de los nombres y las abreviaturas de zona horaria, PostgreSQL aceptará especificaciones de zona horaria de estilo POSIX con el formato STDoffset o STDoffsetDST, donde STD es una abreviatura de zona, offset es una diferencia numérica en horas al oeste con UTC, y DST es una abreviatura opcional de la zona de horario de verano, que se supone que está una hora por delante de la hora especificada. 
   

## <a name="next-steps"></a>Pasos siguientes
Más información sobre lo siguiente:
- [Información general de los parámetros del servidor en Azure Database for PostgreSQL](concepts-server-parameters.md)
- [Configuración de los parámetros de Azure Database for PostgreSQL con la opción Servidor flexible mediante la CLI](howto-configure-server-parameters-using-cli.md)
  