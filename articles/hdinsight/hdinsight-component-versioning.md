---
title: 'Componentes y versiones de Apache Hadoop: Azure HDInsight'
description: Conozca los componentes y las versiones de Apache Hadoop disponibles en Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: 16b9e7f2b790e6f4e758f07c4f352b30357ae32b
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129277316"
---
# <a name="azure-hdinsight-versions"></a>Versiones de Azure HDInsight

HDInsight agrupa componentes de entorno de Apache Hadoop y la plataforma HDInsight en un paquete que se implementa en un clúster. Para obtener más información, consulte el artículo sobre [cómo funciona el control de versiones de HDInsight](hdinsight-overview-versioning.md).

## <a name="supported-hdinsight-versions"></a>Versiones compatibles de HDInsight

En la tabla siguiente se enumeran las versiones de HDInsight que están disponibles en Azure Portal y otros métodos de implementación como PowerShell, la CLI y el SDK de .NET.

| Versión de HDInsight | SISTEMA OPERATIVO DE LA MÁQUINA VIRTUAL | Fecha de la versión| Tipo de soporte técnico | Fecha de expiración del soporte técnico | Fecha de retirada | Alta disponibilidad |
| --- | --- | --- | --- | --- | --- | ---|
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 18.0.4 LTS |24 de septiembre de 2018 | [Estándar](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | | |Sí |
| [HDInsight 3.6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4 LTS |4 de abril de 2017      | [Basic](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | La compatibilidad estándar expiró el 30 de junio de 2021 para todos los tipos de clúster.<br> El soporte técnico Basic expira el 3 de abril de 2022. Consulte las [versiones de componentes de HDInsight 3.6](hdinsight-36-component-versioning.md) para ver los detalles del tipo de clúster. |4 de abril de 2022 |Sí |

La **Expiración del soporte técnico** significa que Microsoft ya no proporcionará soporte técnico para la versión especificada de HDInsight. Y puede que ya no esté disponible mediante Azure Portal para la creación del clúster.

La **retirada** de una versión de HDInsight significa que los clústeres existentes seguirán ejecutándose tal cual. Los nuevos clústeres de esta versión no se pueden crear por ningún medio (ni con la CLI ni con los SDK). No se garantiza que otras características del plano de control, como el escalado manual y la escalabilidad automática, funcionen después de la fecha de retirada. El soporte técnico no está disponible para las versiones retiradas.

## <a name="support-options-for-hdinsight-versions"></a>Opciones de soporte técnico para las versiones de HDInsight

El soporte técnico se define como el período durante el cual la versión de HDInsight recibe soporte técnico y servicio al cliente de Microsoft. HDInsight ofrece dos tipos de soporte técnico: 
- **Soporte técnico Standard**
- **Soporte técnico Basic**

### <a name="standard-support"></a>Soporte técnico Standard

El soporte técnico Standard proporciona actualizaciones y soporte técnico para los clústeres de HDInsight. Microsoft recomienda crear soluciones con la versión totalmente compatible más reciente. 

El soporte técnico Standard incluye lo siguiente:
- Posibilidad de crear solicitudes de soporte técnico en clústeres de HDInsight 4.0.
- Soporte técnico para soluciones de problemas integradas en los clústeres 4.0. 
- Solicitudes para reiniciar servicios o nodos.
- Investigaciones de análisis de la causa principal en solicitudes de soporte técnico.
- Análisis de la causa principal o correcciones para mejorar el rendimiento de trabajos o consultas.
- Análisis de la causa principal o correcciones para mejorar los cambios iniciados por el cliente, por ejemplo, cambios en las configuraciones de servicio o problemas debidos a acciones de script personalizadas.
- Actualizaciones de productos con correcciones de seguridad críticas hasta la retirada de la versión.
- Actualizaciones de productos limitadas al proveedor de recursos de HDInsight.
- Correcciones selectivas o cambios en imágenes de HDInsight 4.0 o versiones de componentes de software de código abierto (OSS).

### <a name="basic-support"></a>Soporte técnico Basic

El soporte técnico Basic proporciona servicio limitado al proveedor de recursos de HDInsight. No se dará servicio a las imágenes de HDInsight ni a los componentes de software de código abierto (OSS). Solo se aplicarán revisiones de seguridad críticas en los clústeres de HDInsight. 

Incluye lo siguiente:
- Uso continuado de clústeres de HDInsight 3.6 existentes.
- Posibilidad de que los clientes existentes de HDInsight 3.6 creen clústeres 3.6.
- Posibilidad de escalar y reducir verticalmente clústeres de HDInsight 3.6 mediante escalado automático o escalado manual.
- Actualizaciones de productos limitadas al proveedor de recursos de HDInsight.
- Actualizaciones de productos con correcciones de seguridad críticas hasta la retirada de la versión.
- Posibilidad de crear solicitudes de soporte técnico en clústeres de HDInsight 3.6.
- Solicitudes para reiniciar servicios o nodos.

El soporte técnico Basic no incluye lo siguiente:
- Correcciones o cambios en imágenes de HDInsight 3.6 o versiones de componentes de software de código abierto (OSS).
- Compatibilidad con soluciones para resolver problemas creadas en clústeres 3.6. 
- Adición de nuevas características o funcionalidades.
- Ayuda con consejos o consultas ad hoc.
- Investigaciones de análisis de la causa principal en solicitudes de soporte técnico.
- Análisis de la causa principal o correcciones para mejorar el rendimiento de trabajos o consultas.
- Análisis de la causa principal o correcciones para mejorar los cambios iniciados por el cliente, por ejemplo, cambios en las configuraciones de servicio o problemas debidos a acciones de script personalizadas.

Microsoft no recomienda la creación de canalizaciones o soluciones de análisis en clústeres con soporte técnico básico. Se recomienda migrar los clústeres existentes a la versión totalmente compatible más reciente. 

## <a name="release-notes"></a>Notas de la versión

Consulte las [Notas de la versión de HDInsight](hdinsight-release-notes.md) para conocer otras notas de las últimas versiones de HDInsight.

## <a name="versioning-considerations"></a>Consideraciones de control de versiones
- Una vez que un clúster se implementa con una imagen, ese clúster no se actualiza automáticamente a la versión de la imagen más reciente. Al crear nuevos clústeres, se implementará la versión más reciente de la imagen.
- Los clientes deben probar y validar que las aplicaciones se ejecutan correctamente cuando se usa la nueva versión de HDInsight.
- HDInsight se reserva el derecho de cambiar la versión predeterminada sin previo aviso. Si tiene una dependencia de la versión, especifique la versión de HDInsight al crear clústeres.
- HDInsight puede retirar una versión de componente de software de código abierto antes de retirar la versión de HDInsight.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de clúster para Apache Hadoop, Spark, etc. en HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Paquete de seguridad de la empresa](./enterprise-security-package.md)
- [Trabajo en Apache Hadoop en HDInsight desde un equipo Windows](hdinsight-hadoop-windows-tools.md)
