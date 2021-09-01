---
title: Errores de conectividad transitorios en Azure Database for MySQL
description: Obtenga información sobre cómo controlar los errores de conectividad transitorios y conectarse de forma eficiente a Azure Database for MySQL.
keywords: mysql connection,connection string,connectivity issues,transient error,connection error,connect efficiently
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: eb28d45c9656b6a1693532b041deadbb903dda94
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122651591"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>Controlar los errores transitorios y conectarse de forma eficaz a Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

En este artículo se describe cómo controlar los errores transitorios y conectarse de forma eficiente a Azure Database for MySQL.

## <a name="transient-errors"></a>Errores transitorios

Un error transitorio es un error que se solucionará automáticamente. Normalmente, estos errores se manifiestan como la interrupción de una conexión con el servidor de base de datos. Tampoco se pueden abrir las nuevas conexiones a un servidor. Los errores transitorios pueden producirse, por ejemplo, cuando se produce un error de hardware o de red. Otra razón podría ser que se está implantando una nueva versión de un servicio PaaS. El sistema mitiga automáticamente la mayoría de estos eventos en menos de 60 segundos. Un procedimiento recomendado a la hora de diseñar y desarrollar aplicaciones en la nube es esperar errores transitorios. Tenga en cuenta que pueden ocurrir en cualquier componente y en cualquier momento, por lo que debe disponer de la lógica adecuada para controlar estas situaciones.

## <a name="handling-transient-errors"></a>Control de errores transitorios

Los errores transitorios se deben controlar con lógica de reintento. Situaciones que se deben tener en cuenta:

* Se produce un error al intentar abrir una conexión.
* Se interrumpe una conexión inactiva en el lado servidor. Cuando se intenta emitir un comando, no se puede ejecutar.
* Se interrumpe una conexión activa que actualmente ejecuta un comando.

El primer y segundo caso son bastante sencillos de controlar. Pruebe a abrir la conexión de nuevo. Si se abre correctamente, el sistema ha mitigado el error transitorio. Puede usar de nuevo la instancia de Azure Database for MySQL. Se recomienda esperar antes de reintentar la conexión. Retroceda si los reintentos iniciales generan errores. De este modo, el sistema puede utilizar todos los recursos disponibles para solucionar la situación de error. Un buen patrón que seguir es el siguiente:

* Espere 5 segundos antes del primer reintento.
* Para cada intento siguiente, aumente la espera exponencialmente, hasta 60 segundos.
* Establezca un número máximo de reintentos como momento en que la aplicación considera que se produjo un error en la operación.

Cuando se produce un error en una conexión con una transacción activa, la recuperación es más difícil de controlar correctamente. Hay dos casos: si la transacción era de solo lectura por naturaleza, es seguro volver a abrir la conexión y volver a intentar la transacción. No obstante, si la transacción también estaba escribiendo en la base de datos, debe determinar si la transacción se revirtió o si se ha ejecutado correctamente antes de producirse el error transitorio. En ese caso, es posible que no haya recibido el reconocimiento de confirmación desde el servidor de bases de datos.

Una manera de hacerlo consiste en generar un id. único en el cliente que se usará para todos los reintentos. Este id. único se pasa como parte de la transacción al servidor y para almacenarlo en una columna con una restricción única. De esta forma, puede reintentar la transacción de forma segura. Se realizará correctamente si se ha revertido la transacción anterior y el identificador único del cliente generado aún no existe en el sistema. Producirá un error que indica una infracción de clave duplicada si el id. único se almacenó previamente porque la transacción anterior se completó correctamente.

Si el programa se comunica con Azure Database for MySQL a través de un middleware de otros fabricantes, consulte con el proveedor si el middleware contiene lógica de reintento para errores transitorios.

Asegúrese de probar la lógica de reintento. Por ejemplo, pruebe a ejecutar el código mientras realiza el escalado o reducción vertical de los recursos de proceso del servidor Azure Database for MySQL. La aplicación debe controlar sin problemas el breve tiempo de inactividad que se encuentre durante esta operación.

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>Conexión eficiente a Azure Database for MySQL

Las conexiones de base de datos son un recurso limitado, por lo que usar de forma eficiente la agrupación de conexiones para acceder a Azure Database for MySQL optimiza el rendimiento. En la sección siguiente se explica cómo usar la agrupación de conexiones o las conexiones persistentes para acceder de forma más eficiente a Azure Database for MySQL.

## <a name="access-databases-by-using-connection-pooling-recommended"></a>Acceso a bases de datos mediante la agrupación de conexiones (recomendado)

La administración de conexiones de base de datos puede tener un impacto significativo en el rendimiento de la aplicación en su conjunto. Para optimizar el rendimiento de la aplicación, el objetivo debe ser reducir el número de veces que se establecen las conexiones y el tiempo para establecer las conexiones en las rutas de acceso clave del código. Es muy recomendable usar la agrupación de conexiones de base de datos o conexiones persistentes para conectarse a Azure Database for MySQL. La agrupación de conexiones de base de datos controla la creación, administración y asignación de conexiones de base de datos. Cuando un programa solicita una conexión de base de datos, da prioridad a la asignación de las conexiones de base de datos inactivas existentes, en lugar de crear una nueva conexión. Una vez que el programa ha terminado de usar la conexión de base de datos, se recupera y se prepara para más usos, en lugar de simplemente cerrarse.

Para ilustrar este concepto, en este artículo se proporciona [un fragmento de código de ejemplo](./sample-scripts-java-connection-pooling.md) que usa JAVA. Para más información, consulte los [grupos de conexiones de base de datos comunes de Apache](https://commons.apache.org/proper/commons-dbcp/).

> [!NOTE]
> El servidor configura un mecanismo de tiempo de espera para cerrar una conexión que ha estado inactiva durante un tiempo con el fin de liberar recursos. Asegúrese de configurar el sistema de comprobación para garantizar la eficiencia de las conexiones persistentes cuando se usan. Para más información, consulte [Configuarción de sistemas de comprobación en el lado cliente para garantizar la eficiencia de las conexiones persistentes](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections).

## <a name="access-databases-by-using-persistent-connections-recommended"></a>Acceso a bases de datos mediante conexiones persistentes (recomendado)

El concepto de conexiones persistentes es similar al de la agrupación de conexiones. Reemplazar conexiones cortas por conexiones persistentes solo requiere cambios menores en el código, pero tiene un efecto importante en cuanto a la mejora del rendimiento en muchos escenarios de aplicación típicos.

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>Acceso a bases de datos mediante un mecanismo de espera y reintento con conexiones cortas

Si tiene limitaciones de recursos, es muy recomendable utilizar la agrupación de conexiones de base de datos o las conexiones persistentes para acceder a las bases de datos. Si la aplicación usa conexiones cortas y tiene errores de conexión cuando se aproxima al límite superior de conexiones simultáneas, puede probar el mecanismo de espera y reintento. Puede establecer un tiempo de espera adecuado, con un tiempo de espera más corto después del primer intento. A partir de ese momento, puede intentar esperar eventos varias veces.

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>Configuración de mecanismos de comprobación en clientes para confirmar la eficiencia de las conexiones persistentes

El servidor configura un mecanismo de tiempo de espera para cerrar una conexión que ha estado inactiva durante un tiempo con el fin de liberar recursos. Cuando el cliente accede a la base de datos de nuevo, equivale a crear una nueva solicitud de conexión entre el cliente y el servidor. Para garantizar la eficiencia de las conexiones durante el proceso de uso, configure un mecanismo de comprobación en el cliente. Tal y como se muestra en el ejemplo siguiente, puede usar la agrupación de conexiones JDBC de Tomcat para configurar este mecanismo de comprobación.

Al establecer el parámetro TestOnBorrow, cuando hay una nueva solicitud, el grupo de conexiones comprueba automáticamente la eficiencia de las conexiones inactivas disponibles. Si dicha conexión es eficiente, se devuelve directamente; de lo contrario, el grupo de conexiones retira la conexión. Después, el grupo de conexiones crea una nueva conexión eficiente y la devuelve. Este proceso garantiza el acceso eficiente a la base de datos. 

Para más información sobre la configuración específica, consulte el [documento de introducción oficial de los grupos de conexiones JDBC](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes). Necesita configurar los siguientes tres parámetros: TestOnBorrow (establecido en true), ValidationQuery (establecido en SELECT 1) y ValidationQueryTimeout (establecido en 1). A continuación se muestra el código de ejemplo específico:

```java
public class SimpleTestOnBorrowExample {
      public static void main(String[] args) throws Exception {
          PoolProperties p = new PoolProperties();
          p.setUrl("jdbc:mysql://localhost:3306/mysql");
          p.setDriverClassName("com.mysql.jdbc.Driver");
          p.setUsername("root");
          p.setPassword("password");
            // The indication of whether objects will be validated by the idle object evictor (if any). 
            // If an object fails to validate, it will be dropped from the pool. 
            // NOTE - for a true value to have any effect, the validationQuery or validatorClassName parameter must be set to a non-null string. 
          p.setTestOnBorrow(true); 

            // The SQL query that will be used to validate connections from this pool before returning them to the caller.
            // If specified, this query does not have to return any data, it just can't throw a SQLException.
          p.setValidationQuery("SELECT 1");

            // The timeout in seconds before a connection validation queries fail. 
            // This works by calling java.sql.Statement.setQueryTimeout(seconds) on the statement that executes the validationQuery. 
            // The pool itself doesn't timeout the query, it is still up to the JDBC driver to enforce query timeouts. 
            // A value less than or equal to zero will disable this feature.
          p.setValidationQueryTimeout(1);
            // set other useful pool properties.
          DataSource datasource = new DataSource();
          datasource.setPoolProperties(p);

          Connection con = null;
          try {
            con = datasource.getConnection();
            // execute your query here
          } finally {
            if (con!=null) try {con.close();}catch (Exception ignore) {}
          }
      }
  }
```

## <a name="next-steps"></a>Pasos siguientes

* [Troubleshoot connection issues to Azure Database for MySQL](howto-troubleshoot-common-connection-issues.md) (Solución de problemas de conexión a Azure Database for MySQL)
