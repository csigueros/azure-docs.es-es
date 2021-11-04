---
title: Referencia del motor de reglas de Azure CDN | Microsoft Docs
description: Documentación de referencia sobre las condiciones y características de coincidencia del motor de reglas de Azure CDN.
services: cdn
author: duongau
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: duau
ms.openlocfilehash: 928e3fb96cd1acdbbfe7aceea5f5ab9ceca97909
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461421"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Referencia del motor de reglas de Azure CDN de Verizon Premium

En este artículo se muestran descripciones detalladas de las condiciones de coincidencia disponibles para el [motor de reglas](cdn-verizon-premium-rules-engine.md) de Azure Content Delivery Network (CDN).

El motor de reglas está diseñado para ser la entidad de certificación final sobre cómo la red CDN procesa tipos específicos de solicitudes.

**Usos habituales**:

- Invalidar o definir una directiva de memoria caché personalizada.
- Proteger o denegar las solicitudes de contenido confidencial.
- Redirigir solicitudes.
- Almacenar datos de registro personalizados.
## <a name="key-concepts"></a>Conceptos clave
A continuación se describen los conceptos clave para configurar el motor de reglas.
### <a name="draft"></a>Borrador
Un borrador de una directiva consta de una o varias reglas para identificar las solicitudes y el conjunto de acciones que se aplicarán a ellas. Un borrador es un trabajo en curso que permite actualizaciones de configuración frecuentes sin que ello afecte al tráfico del sitio. Cuando el borrador esté listo para finalizarse, debe convertirse en una directiva de solo lectura.

### <a name="rule"></a>Regla
Una regla identifica uno o más tipos de solicitudes y el conjunto de acciones que se les aplicará.

Consta de: 

- Un conjunto de expresiones condicionales que definen la lógica a través de la cual se identifican las solicitudes.
- Un conjunto de condiciones de coincidencia que definen los criterios que se usan para identificar las solicitudes.
- Un conjunto de características que definen cómo la red CDN controlará las solicitudes anteriores.
Estos elementos se identifican en la siguiente ilustración.

![Captura de pantalla con etiquetas que muestra la expresión condicional, la coincidencia y las características de una regla.](./media/cdn-verizon-premium-rules-engine-reference/verizon-rules-engine-reference.png)

### <a name="policy"></a>Directiva
Una directiva, que consta de un conjunto de reglas de solo lectura, proporciona los medios para:

- Crear, almacenar y administrar varias variantes de las reglas
- Revertir a una versión implementada previamente
- Preparar reglas específicas de eventos por adelantado (por ejemplo, una regla que redirija el tráfico como resultado de un mantenimiento de origen del cliente)

> [!NOTE]
> Aunque solo se permite una directiva por entorno, se pueden implementar directivas según sea necesario.

### <a name="deploy-request"></a>Solicitud de implementación
Una solicitud de implementación proporciona un procedimiento simple y simplificado a través del cual se puede aplicar rápidamente una directiva al entorno de ensayo o de producción. Se proporciona un historial de las solicitudes de implementación para facilitar el seguimiento de los cambios aplicados a esos entornos.

> [!NOTE]
> Solo las solicitudes que no superan nuestro sistema de validación automatizada y detección de errores requerirán revisión y aprobación manual.

### <a name="rule-precedence"></a>Prioridad de la regla
Las reglas contenidas en una directiva se procesan normalmente en el orden en que se enumeran (es decir, de arriba abajo). Si la solicitud coincide con las reglas en conflicto, la última regla que se va a procesar tendrá prioridad.

### <a name="policy-deployment-workflow"></a>Flujo de trabajo de implementación de directivas
A continuación se muestra el flujo de trabajo a través del cual se puede aplicar una directiva al entorno de producción o de ensayo.

![Flujo de trabajo de implementación de directivas](./media/cdn-verizon-premium-rules-engine-reference/policy-deployment-workflow.png)

|Paso |Descripción |
|---------|---------|
|[Crear borrador](https://docs.vdms.com/cdn/index.html#HRE/AdministeringDraftsandRules.htm#Create)    |    Un borrador se compone de un conjunto de reglas que definen cómo la red CDN debe controlar las solicitudes para el contenido.     |
|Bloquear borrador   |     Una vez finalizado un borrador, se debe bloquear y convertir en una directiva de solo lectura.    |
|[Enviar solicitud de implementación](https://docs.vdms.com/cdn/index.html#HRE/DeployRequest.htm)   |   <br> Una solicitud de implementación permite aplicar una directiva en el tráfico de prueba o de producción.</br> <br>Envíe una solicitud de implementación al entorno de ensayo o de producción.</br>     |
|Implementar revisión de solicitud   |    <br>Una solicitud de implementación se somete a una validación automatizada y a la detección de errores.</br><br>Aunque la mayoría de las solicitudes de implementación se aprueban automáticamente, se requiere una revisión manual en las directivas más complejas.</br>   |
|Implementación de directiva ([Ensayo](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Staging))   |  <br> Tras la aprobación de una solicitud de implementación en el entorno de ensayo, se aplicará una directiva al entorno de ensayo. Este entorno permite probar una directiva con el tráfico ficticio del sitio.</br><br>Cuando la directiva esté lista para aplicarse al tráfico del sitio activo, se debe enviar una nueva solicitud de implementación para el entorno de producción.</br>      |
|Implementación de directiva ([Producción](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Producti))   |  Tras la aprobación de una solicitud de implementación en el entorno de producción, se aplicará una directiva al entorno de producción. Este entorno permite que una directiva actúe como la autoridad final para determinar cómo la red CDN debe controlar el tráfico real.     |
## <a name="syntax"></a>Sintaxis

La manera en que se tratarán los caracteres especiales varía según la forma en la que una condición de coincidencia o función administra los valores de texto. Una condición de coincidencia o función puede interpretar el texto de una de las siguientes maneras:

- [**Valores literales**](#literal-values)
- [**Valores de carácter comodín**](#wildcard-values)
- [**Expresiones regulares**](#regular-expressions)

### <a name="literal-values"></a>Valores literales

El texto que se interpreta como un valor literal trata todos los caracteres especiales, excepto el símbolo %, como parte del valor que debe coincidir. En otras palabras, una condición de coincidencia literal establecida en `\'*'\` solo se puede satisfacer cuando se encuentra ese valor exacto (es decir, `\'*'\`).

Se usa un símbolo de porcentaje para indicar la codificación de direcciones URL (p. ej., `%20`).

### <a name="wildcard-values"></a>Valores de carácter comodín

El texto que se interpreta como un valor comodín asigna un significado adicional a los caracteres especiales. En la tabla siguiente se describe cómo se interpreta el siguiente conjunto de caracteres:

Carácter | Descripción
----------|------------
\ | Se usa una barra diagonal inversa para el escape de cualquier carácter especificado en esta tabla. Debe especificarse una barra diagonal inversa directamente antes del carácter especial al que debe aplicarse el escape.<br/>Por ejemplo, la sintaxis siguiente aplica el escape a un asterisco: `\*`
% | Se usa un símbolo de porcentaje para indicar la codificación de direcciones URL (p. ej., `%20`).
\* | Un asterisco es un carácter comodín que representa uno o más caracteres.
Space | Un carácter de espacio indica que una condición de coincidencia debe cumplirse mediante uno de los patrones o valores especificados.
'valor' | Las comillas simples no tienen un significado especial. Sin embargo, se utiliza un conjunto de comillas simples para indicar que un valor debe tratarse como un valor literal. Puede usarse de las siguientes maneras:<br><br/>- Permite que se cumpla una condición de coincidencia siempre que el valor especificado coincida con cualquier parte del valor de comparación.  Por ejemplo, `'ma'` coincidiría con cualquiera de las siguientes cadenas: <br/><br/>/business/**ma** rathon/asset.htm<br/>**ma** p.gif<br/>/business/template.**ma** p<br /><br />- Permite a un carácter especial que se especifique como un carácter literal. Por ejemplo, puede especificar un carácter de espacio literal mediante la inclusión de un carácter de espacio entre un conjunto de comillas simples (es decir, `' '` o `'sample value'`).<br/>- Permite que se especifique un valor en blanco. Especifique un valor en blanco mediante la especificación de un conjunto de comillas simples (es decir, '').<br /><br/>**Importante:**<br/>-Si el valor especificado no contiene un carácter comodín, entonces se considera automáticamente un valor literal, lo que significa que no es necesario especificar un juego de comillas simples.<br/>- Si una barra diagonal inversa no aplica el escape a otro carácter de esta tabla, se omite cuando se especifique dentro de un conjunto de comillas simples.<br/>- Otra manera de especificar un carácter especial como un carácter literal es aplicarle el escape con una barra diagonal inversa (es decir, `\`).

### <a name="regular-expressions"></a>Expresiones regulares

Las expresiones regulares definen un patrón que se busca dentro de un valor de texto. La notación de expresiones regulares define significados concretos en una variedad de símbolos. La tabla siguiente indica cómo se tratan los caracteres especiales mediante las condiciones de coincidencia y funciones que admiten expresiones regulares.

Carácter especial | Descripción
------------------|------------
\ | Una barra diagonal inversa aplica el escape al carácter que le sigue, lo que hace que ese carácter se trate como un valor literal en lugar de tomar el significado de su expresión regular. Por ejemplo, la sintaxis siguiente aplica el escape a un asterisco: `\*`
% | El significado de un símbolo de porcentaje depende de su uso.<br/><br/> `%{HTTPVariable}`: esta sintaxis identifica una variable HTTP.<br/>`%{HTTPVariable%Pattern}`: esta sintaxis utiliza un símbolo de porcentaje para identificar una variable HTTP y se usa como delimitador.<br />`\%`: El escape de un símbolo de porcentaje permite que se use como valor literal o para indicar la codificación de la dirección URL (p. ej., `\%20`).
\* | Un asterisco permite que el carácter anterior coincida con cero o más veces.
Space | Normalmente, un carácter de espacio se trata como un carácter literal.
'valor' | Las comillas simples se tratan como caracteres literales. Un conjunto de comillas simples no tiene un significado especial.

Coincidencias de coincidencia y características que admiten expresiones regulares, excepto los patrones definidos por las expresiones compatibles con Perl.



## <a name="next-steps"></a>Pasos siguientes

- [Condiciones de coincidencia del motor de reglas](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Expresiones condicionales del motor de reglas](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Características del motor de reglas](cdn-verizon-premium-rules-engine-reference-features.md)
- [Invalidación del comportamiento HTTP mediante el motor de reglas](cdn-verizon-premium-rules-engine.md)
- [Información general de Azure CDN](cdn-overview.md)
