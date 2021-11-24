---
title: 'Guía de solución de problemas: Azure DNS'
description: En esta ruta de aprendizaje, obtendrá información sobre la solución de problemas comunes con Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: troubleshooting
ms.date: 11/10/2021
ms.author: rohink
ms.openlocfilehash: bc003c640e51b855f446878aa0b5829b2d2c09c5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400364"
---
# <a name="azure-dns-troubleshooting-guide"></a>Guía de solución de problemas de Azure DNS

En este artículo se proporciona información para solucionar problemas para preguntas que se plantean con frecuencia sobre Azure DNS.

Si estos pasos no resuelven el problema, también puede buscar o publicar su incidencia en nuestra [página de preguntas y respuestas de Microsoft Q&A para obtener soporte técnico de la comunidad](/answers/topics/azure-virtual-network.html). O bien, puede abrir una solicitud de soporte técnico de Azure.

## <a name="i-cant-create-a-dns-zone"></a>No puedo crear una zona DNS

Para resolver problemas habituales, pruebe uno o varios de los pasos siguientes:

1.  Revise los registros de auditoría de Azure DNS para determinar el motivo del error.
2.  Cada nombre de zona DNS debe ser único dentro de su grupo de recursos. Es decir, dos zonas DNS con el mismo nombre no pueden compartir un grupo de recursos. Intente usar otro nombre de zona u otro grupo de recursos.
3.  Es posible que vea el mensaje "Se alcanzó o superó el número máximo de zonas en la suscripción {ID de la suscripción}". Use otra suscripción de Azure, elimine algunas zonas o póngase en contacto con el soporte técnico de Azure para aumentar el límite de su suscripción.
4.  Es posible que vea el mensaje "La zona '{nombre de la zona}' no está disponible". Este error significa que Azure DNS no pudo asignar servidores de nombres para esta zona DNS. Pruebe a usar otro nombre de zona. O bien, si es el propietario del nombre de dominio, puede ponerse en contacto con el soporte técnico de Azure para que le asignen servidores de nombres.

### <a name="recommended-articles"></a>Artículos recomendados

* [Registros y zonas DNS](dns-zones-records.md)
* [Creación de una zona DNS](./dns-getstarted-portal.md)

## <a name="i-cant-create-a-dns-record"></a>No puedo crear un registro de DNS

Para resolver problemas habituales, pruebe uno o varios de los pasos siguientes:

1.  Revise los registros de auditoría de Azure DNS para determinar el motivo del error.
2.  ¿El conjunto de registros ya existe?  El DNS de Azure administra los registros mediante los *conjuntos* de registros, que son una colección de registros con el mismo nombre y el mismo tipo. Si ya existe un registro con el mismo nombre y tipo, para agregar otro registro de dicho tipo debe editar el conjunto de registros existente.
3.  ¿Está intentando crear un registro en el vértice de la zona DNS (la "raíz" de la zona)? Si es así, la convención de DNS es usar el carácter ‘@’ como nombre del registro. Tenga en cuenta también que los estándares de DNS no permiten registros CNAME en el vértice de la zona.
4.  ¿Tiene un conflicto de CNAME?  Los estándares de DNS no permiten un registro CNAME con el mismo nombre que un registro de cualquier otro tipo. Si tiene un CNAME existente, la creación de un registro con el mismo nombre, pero de un tipo diferente genera un error.  Del mismo modo, la creación de un CNAME genera un error si el nombre coincide con un registro existente de otro tipo. Elimine el conflicto quitando el otro registro o eligiendo otro nombre de registro.
5.  ¿Ha alcanzado el límite en el número de conjuntos de registros permitido en una zona DNS? En Azure Portal se muestra el número actual y el número máximo de conjuntos de registros, en las propiedades de la zona. Si ha alcanzado este límite, elimine algunos conjuntos de registros o póngase en contacto con el soporte técnico de Azure para aumentar el límite de conjuntos de registros para esta zona y vuelva a intentarlo. 

### <a name="recommended-articles"></a>Artículos recomendados

* [Registros y zonas DNS](dns-zones-records.md)
* [Creación de una zona DNS](./dns-getstarted-portal.md)

## <a name="i-cant-resolve-my-dns-record"></a>No puedo resolver mi registro de DNS

La resolución de nombres de DNS es un proceso de varios pasos que puede generar un error por diversos motivos. Los pasos siguientes ayudan a investigar por qué se producen errores en la resolución de DNS para un registro de DNS en una zona hospedada en Azure DNS.

1.  Confirme que los registros de DNS se hayan configurado correctamente en el DNS de Azure. Revise los registros de DNS en Azure Portal y compruebe que el nombre de la zona, el nombre del registro y el tipo de registro son correctos.
2.  Confirme que los registros de DNS se resuelven correctamente en los servidores de nombres de DNS de Azure.
    - Si realiza consultas de DNS del equipo local, puede ver los resultados almacenados en caché que no reflejen el estado actual de los servidores de nombres.  Además, las redes corporativas suelen usan servidores proxy de DNS que impiden que las consultas de DNS se dirijan a servidores de nombres específicos.  Para evitar estos problemas, utilice un servicio de resolución de nombres basado en web, como [digwebinterface](https://digwebinterface.com).
    - Asegúrese de especificar los servidores de nombres correctos para la zona DNS, que se muestran en Azure Portal.
    - Compruebe que el nombre de DNS (tiene que especificar el nombre completo, incluido el nombre de zona) y el tipo de registro son correctos
3.  Confirme que el nombre de dominio del DNS se ha [delegado correctamente a los servidores de nombres de DNS de Azure](dns-domain-delegation.md). Hay un [muchos sitios web de terceros que ofrecen la validación de la delegación de DNS](https://www.bing.com/search?q=dns+check+tool). Se trata de una prueba de delegación de *zona*, por lo que solo se debe escribir el nombre de la zona DNS, no el nombre de registro completo.
4.  Una vez completado lo anterior, el registro de DNS ahora debe resolverse correctamente. Para comprobarlo, puede volver a usar [digwebinterface](https://digwebinterface.com), pero esta vez con la configuración predeterminada del servidor de nombres.

### <a name="recommended-articles"></a>Artículos recomendados

* [Delegación de un dominio en DNS de Azure](dns-domain-delegation.md)

## <a name="dns-zone-status-and-unhealthy-delegation-scenarios"></a>Estado de zona DNS y escenarios de delegación incorrectos

El estado de zona DNS indica el estado actual de la zona. El estado de zona DNS puede ser **Desconocido**, **Disponible** y **Degradado.**

### <a name="unknown"></a>Unknown

Cuando se acaba de crear un recurso, las señales de estado de estos nuevos recursos no estarán disponibles de inmediato. Puede pasar un máximo de 24 horas para obtener las señales de estado correctas para las zonas DNS. Hasta este momento, el estado de las zonas DNS se mostrará como **Desconocido.**

Cuando la comprobación de estado de los recursos no ha recibido información sobre las zonas DNS durante más de 6 horas, el estado de las zonas se mostrará como Desconocido. Si bien este estado no es una indicación definitiva del estado del recurso, es un punto de datos importante en el proceso de solución de problemas. Si, una vez recibida la señal, el recurso se ejecuta según lo esperado, el estado del recurso cambiará a **Disponible** al cabo de unos minutos.

La captura de pantalla que se muestra a continuación es un ejemplo del mensaje de comprobación de estado de los recursos.

:::image type="content" source="./media/dns-troubleshoot/unknown-status.png" alt-text="Captura de pantalla del estado Desconocido.":::

### <a name="available"></a>Disponible

Un estado **Disponible**  indica que la comprobación de estado de los recursos no ha detectado ningún problema de delegación con sus zonas DNS. Este estado significa que los registros de delegación de NS se mantienen correctamente en la zona primaria y que los registros destinados a zonas secundarias no están presentes en la zona primaria. 

La captura de pantalla que se muestra a continuación es un ejemplo del mensaje de comprobación de estado de los recursos.

:::image type="content" source="./media/dns-troubleshoot/available-status.png" alt-text="Captura de pantalla del estado Disponible.":::

### <a name="degraded"></a>Degradado

Un estado **Degradado** indica que la comprobación de estado de los recursos ha detectado algún problema de delegación con sus zonas DNS. Corrija la configuración de delegación y espere 24 horas para que el estado cambie a **Disponible**.  

La captura de pantalla que se muestra a continuación es un ejemplo del mensaje de comprobación de estado de los recursos.

:::image type="content" source="./media/dns-troubleshoot/degraded-status.png" alt-text="Captura de pantalla del estado Degradado.":::

Si han transcurrido 24 horas después de corregir la configuración y las zonas DNS siguen mostrándose con el estado Degradado, póngase en contacto con el servicio de asistencia.  

### <a name="configuration-error-scenario"></a>Escenario de error de configuración

En el siguiente escenario se muestra un error de configuración que ha provocado el estado incorrecto de las zonas DNS.

**Delegación incorrecta**

Una zona principal contiene registros de delegación NS que ayudan a delegar el tráfico de la zona principal a la secundaria. Si hay algún registro de delegación de NS en la zona primaria, se supone que el servidor DNS enmascarará todos los demás registros por debajo del registro de delegación de NS, excepto los registros glue, y dirigirá el tráfico a la zona secundaria correspondiente en función de la consulta del usuario. Si una zona primaria contiene otros registros diseñados para las zonas secundarias (zonas delegadas) debajo del registro de delegación NS, la zona se marcará como incorrecta y su estado será **Degradado**.

**¿Qué son los registros de adherencia?** - Se trata de registros del registro de delegación que ayudan a dirigir el tráfico a las zonas delegadas o secundarias mediante sus direcciones IP y que se configuran como se muestra a continuación.

| Configuración | Valor |
| ------- | ----- |
| **Zona** | contoso.com |
| **Registro de delegación** | NS secundarios </br> ns1.child.contoso.com |
| **Registro glue** | ns1.child A 1.1.1.1 |

#### <a name="example-of-an-unhealthy-zone"></a>Ejemplo de una zona incorrecta

A continuación se muestra un ejemplo de una zona que contiene registros por debajo de la delegación de NS.

* Nombre de la zona: contoso.com

| Nombre | Tipo | TTL | Valor |
| ---- | ---- | --- | ----- |
| @ | NS | 3600 | ns1-04.azure-dns.com. |
| @ | SOA | 3600 | _Valores de SOA_ |
| * | A | 3600 | 255.255.255.255 |
| **secundario** | **NS** | **3600** | **ns1-08.azure-dns.com** (registro de delegación NS) |
| _**foo.child**_ | _**A**_ | _**3600**_ | _**10.10.10.10**_ |
| _**txt.child**_ | _**TXT**_ | _**3600**_ | _**"registro de texto"**_ |
| abc.test | A | 3600 | 5.5.5.5 |

En el ejemplo anterior, el elemento **secundario** es el registro de delegación de NS. Los registros _**foo.child**_ y _**txt.child**_ son registros que solo deben estar presentes en la zona secundaria, **child.contoso.com**. Estos registros pueden producir incoherencias si no se quitan de la zona primaria, **contoso.com**. Estas incoherencias podrían hacer que la zona se considerara como incorrecta con un estado **Degradado**.

#### <a name="examples-of-when-a-zone-is-considered-healthy-or-unhealthy"></a>Ejemplos de cuándo una zona se considera correcta o incorrecta

| Ejemplo | Estado |
| ------- | ------ |
| La zona no contiene registros de delegación de NS, registros glue ni otros registros. | **Healthy** |
| La zona solo contiene registros de delegación de NS. | **Healthy** |
| La zona solo contiene registros de delegación de NS y registros glue. | **Healthy** |
| La zona contiene registros de delegación NS y otros registros (excepto registros glue) por debajo del registro de delegación, que deberían estar presentes en la zona secundaria. | **Incorrecto** |
| La zona contiene registros de delegación NS, registros de adherencia y otros (excepto registros de adherencia). | **Incorrecto** |

**¿Cómo se puede corregir esto?** - Para resolverlo, busque y elimine todos los registros, excepto los registros glue en los registros de delegación de NS en la zona primaria.

**¿Cómo se pueden localizar los registros de delegación incorrectos?** - Se ha creado un script para buscar los registros de delegación incorrectos en la zona.  El script mostrará los registros que son incorrectos.

1. Guarde el script en: [Búsqueda de registros DNS incorrectos en Azure DNS: ejemplo de script de PowerShell](./scripts/find-unhealthy-dns-records.md)

2. Ejecute el script como se mencionó en el editor de scripts.  Puede editar el script para adaptarlo a sus requisitos.

**Información histórica**: puede obtener acceso al historial de mantenimiento de hasta 14 días en la sección Historial de estado de Resource Health. La sección también contendrá el motivo del tiempo de inactividad (si está disponible) para los tiempos de inactividad notificados por Resource Health. En la actualidad, Azure muestra el tiempo de inactividad del recurso de zonas DNS con una granularidad de 24 horas.

## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>¿Cómo puedo especificar el "servicio" y el "protocolo" para un registro SRV?

El DNS de Azure administra los registros de DNS como conjuntos de registros: la colección de registros con el mismo nombre y el mismo tipo. Para un conjunto de registros SRV, el "servicio" y el "protocolo" deben especificarse como parte del nombre del conjunto de registros. Los otros parámetros SRV (la "prioridad", el "peso", el "puerto" y el "destino") se especifican por separado para cada registro del conjunto.

Ejemplo de nombres de registros SRV (nombre de servicio "sip", protocolo "tcp"):

- \_sip.\_tcp (crea un conjunto de registros en el vértice de la zona)
- \_sip.\_tcp.sipservice (crea un conjunto de registros con el nombre "sipservice")

### <a name="recommended-articles"></a>Artículos recomendados

* [Registros y zonas DNS](dns-zones-records.md)
* [Creación de registros y conjuntos de registros de DNS mediante Azure Portal](./dns-getstarted-portal.md)
* [Tipo de registro SRV (Wikipedia)](https://en.wikipedia.org/wiki/SRV_record)

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre [Registros y zonas DNS](dns-zones-records.md)
* Para empezar a usar DNS de Azure, vea cómo [crear una zona DNS](./dns-getstarted-portal.md) y [crear registros DNS](./dns-getstarted-portal.md).
* Para migrar una zona DNS existente, vea cómo [importar y exportar un archivo de zona DNS](dns-import-export.md).
