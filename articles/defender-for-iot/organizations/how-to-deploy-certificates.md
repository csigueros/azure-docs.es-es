---
title: Implementación de certificados
description: Aprenda a configurar e implementar certificados para Defender para IoT.
ms.date: 08/29/2021
ms.topic: how-to
ms.openlocfilehash: 83345d9ad0267e39093bc310c8ebd940f878c47e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440225"
---
# <a name="about-certificates"></a>Acerca de los certificados

En este artículo se proporciona la información necesaria para crear e implementar certificados para Azure Defender para IoT. Un responsable de certificados de seguridad, PKI u otros cualificado debería controlar la creación e implementación de certificados.

Defender para IoT usa certificados SSL/TLS para proteger la comunicación entre los siguientes componentes del sistema: 

- Entre los usuarios y la consola web del dispositivo. 
- Entre los sensores y la consola de administración local. 
- Entre una consola de administración y una consola de administración de alta disponibilidad.
- A la API REST en el sensor y la consola de administración local. 

Los usuarios administradores de Defender para IoT pueden cargar un certificado en las consolas de sensor y su consola de administración local desde el cuadro de diálogo Certificados SSL/TLS.

:::image type="content" source="media/how-to-deploy-certificates/certificate-upload.png" alt-text="Cuadro de diálogo de carga de certificados.":::

## <a name="about-certificate-generation-methods"></a>Acerca de los métodos de generación de certificados

Se admiten todos los métodos de generación de certificados con:  

- Infraestructuras de clave empresarial y privada (PKI privada) 
- Infraestructura de clave pública (PKI pública) 
- Certificados generados localmente en el dispositivo (autofirmados localmente) 

> [!Important]
> No se recomienda usar certificados autofirmados localmente. Este tipo de conexión no es segura y debe usarse solo para entornos de prueba. Dado que no se puede validar el propietario del certificado ni mantener la seguridad del sistema, los certificados autofirmados nunca deben usarse en redes de producción.

## <a name="about-certificate-validation"></a>Acerca de la validación de certificados

Además de proteger la comunicación entre los componentes del sistema, los usuarios también pueden llevar a cabo la validación de certificados.  

La validación se efectúa en:

- Una lista de revocación de certificados (CRL)
- La fecha de expiración del certificado  
- La cadena de confianza del certificado

La validación se lleva a cabo dos veces:

1. Al cargar el certificado en los sensores y las consolas de administración locales. Si se produce un error durante la validación, no se puede cargar el certificado.
1. Al iniciar la comunicación cifrada entre:

    - Los componentes del sistema de Defender para IoT, por ejemplo, un sensor y una consola de administración local.

    - Defender para IoT y determinados servidores de terceros definidos en reglas de reenvío.  Vea [Acerca de la información de alertas reenviada](how-to-forward-alert-information-to-partners.md#about-forwarded-alert-information) para obtener más información.  

Si se produce un error durante la validación, la comunicación entre los componentes relevantes se detiene y aparece un error de validación en la consola.

## <a name="about-certificate-upload-to-defender-for-iot"></a>Acerca de la carga de certificados en Defender para IoT

Tras la instalación del sensor y la consola de administración local, se genera un certificado autofirmado local que se usa para acceder al sensor y la aplicación web de la consola de administración local.

Al iniciar sesión en el sensor y la consola de administración local por primera vez, se pide a los usuarios administradores que carguen un certificado SSL/TLS. Se recomienda encarecidamente usar certificados SSL/TLS.

Si el responsable de certificados no crea correctamente el certificado o hay problemas de conexión, el certificado no se puede cargar y los usuarios se ven obligados a trabajar con un certificado firmado localmente.  

La opción de validar el certificado cargado y los certificados de terceros está habilitada automáticamente, pero se puede deshabilitar. Cuando se deshabilita, las comunicaciones cifradas entre componentes continúan, aunque un certificado no sea válido.

## <a name="certificate-deployment-tasks"></a>Tareas de implementación de certificados

En esta sección se indican los pasos que se deben seguir para asegurarse de que la implementación de un certificado se efectúa sin problemas.

**Para implementar certificados, compruebe que:**

- Un especialista en seguridad, PKI o certificados está creando o supervisando la creación de certificados. 
- Se crea un certificado único para cada sensor, consola de administración y máquina de alta disponibilidad.
- Se cumplen los requisitos de creación de certificados. Vea  [Requisitos de creación de certificados](#certificate-creation-requirements).
- Los usuarios administradores que inician sesión en cada sensor, consola de administración local y máquina de alta disponibilidad de Defender para IoT tienen acceso al certificado.

## <a name="certificate-creation-requirements"></a>Requisitos de creación de certificados

En esta sección se habla de los requisitos de creación de certificados, que incluyen:

- [Requisitos de acceso a puertos para la validación de certificados](#port-access-requirements-for-certificate-validation)

- [Requisitos de tipo de archivo](#file-type-requirements)

- [Requisitos de archivo de claves](#key-file-requirements)

- [Requisitos de archivo de cadena de certificados (si se usa .pem)](#certificate-chain-file-requirements-if-pem-is-used)

### <a name="port-access-requirements-for-certificate-validation"></a>Requisitos de acceso a puertos para la validación de certificados

Si está trabajando en la validación de certificados, compruebe que el acceso al puerto 80 está disponible.

La validación de certificados se evalúa en una lista de revocación de certificados y la fecha de expiración del certificado. Esto significa que el dispositivo debe ser capaz de establecer la conexión con el servidor de CRL definido por el certificado. De forma predeterminada, el certificado hará referencia a la dirección URL de CRL en el puerto HTTP 80. 

Algunas directivas de seguridad de la organización pueden bloquear el acceso a este puerto. Si su organización no tiene acceso al puerto 80, puede hacer lo siguiente: 

1. Defina otra dirección URL y un puerto específico en el certificado.

    - La dirección URL debe definirse como http:// en lugar de https://.

    - Compruebe que el servidor CRL de destino puede escuchar en el puerto definido. 

1. Use un servidor proxy que acceda a la CRL en el puerto 80.

### <a name="file-type-requirements"></a>Requisitos de tipo de archivo

Defender para IoT exige que cada certificado firmado por la entidad de certificación contenga un archivo .key y un archivo .crt. Estos archivos se cargan en el sensor y en la consola de administración local después del inicio de sesión. Algunas organizaciones pueden exigir un archivo .pem. Defender para IoT no necesita este tipo de archivo.

**.crt: archivo contenedor de certificados**  
Archivo en formato .pem o .der con otra extensión. El Explorador de Windows lo reconoce como certificado. El Explorador de Windows no reconoce el archivo .pem.

**.key: archivo de claves privadas**  
Un archivo de claves tiene el mismo formato que un archivo PEM, pero con una extensión diferente.

**.pem: archivo contenedor de certificados (opcional)** PEM es un archivo de texto que contiene la codificación Base64 del texto del certificado, un encabezado de texto sin formato y un pie de página que marca el principio y el final del certificado.

Es posible que tenga que convertir tipos de archivos existentes en tipos compatibles. Vea [Conversión de archivos existentes en archivos compatibles](#convert-existing-files-to-supported-files) para obtener detalles.

### <a name="certificate-file-parameter-requirements"></a>Requisitos de parámetros de archivo de certificado

Compruebe que ha cumplido los siguientes requisitos de parámetros antes de crear un certificado:

- [Requisitos de archivo CRT](#crt-file-requirements)
- [Requisitos de archivo de claves](#key-file-requirements)
- [Requisitos de archivo de cadena de certificados (si se usa .pem)](#certificate-chain-file-requirements-if-pem-is-used)

### <a name="crt-file-requirements"></a>Requisitos de archivo CRT

En esta sección se habla de los requisitos del campo .crt.

- Algoritmo de firma = SHA256RSA 
- Algoritmo hash de firma = SHA256 
- Válido desde = fecha pasada válida 
- Válido hasta = Fecha futura válida 
- Clave pública = RSA de 2048 bits (mínimo) o 4096 bits 
- Punto de distribución de CRL = Dirección URL para el archivo .crl 
- CN del firmante (nombre común) = nombre de dominio del dispositivo; por ejemplo, Sensor.contoso.com o *.contoso.com.  
- (P)aís del firmante = Definido, por ejemplo, EE. UU. 
- Unidad organizativa (UO) del firmante = Definida; por ejemplo, Contoso Labs 
- (O)rganización del firmante = Definida; por ejemplo, Contoso Inc. 

Los certificados con otros parámetros podrían funcionar, pero Microsoft no los admite.  

### <a name="key-file-requirements"></a>Requisitos de archivo de claves

Use RSA de 2048 bits o 4096 bits.

Cuando se usa una longitud de clave de 4096 bits, el protocolo de enlace SSL al principio de cada conexión es más lento. Además, hay un aumento en el uso de CPU durante los protocolos de enlace.

### <a name="certificate-chain-file-requirements-if-pem-is-used"></a>Requisitos de archivo de cadena de certificados (si se usa .pem)

Archivo .pem que contiene los certificados de todas las entidades de certificación de la cadena de confianza que lleva al certificado.  

Los atributos de colección se admiten en el archivo de cadena de certificados.

## <a name="create-certificates"></a>Creación de certificados

Use una plataforma de administración de certificados para crear un certificado, por ejemplo, una plataforma de administración de PKI automatizada. Compruebe que los certificados cumplen los requisitos del archivo de certificados. Vea Prueba de certificados para obtener información sobre cómo probar los archivos que se crean.

Si no está realizando la validación de certificados, quite la referencia de la dirección URL de CRL del certificado. Vea [Requisitos de archivo CRT](#crt-file-requirements) para obtener información sobre este parámetro.

Si no tiene una aplicación que pueda crear certificados automáticamente, hable con un responsable de certificados de seguridad, PKI u otros cualificado.

Es posible realizar [Pruebas de los certificados creados](#test-certificates-you-create).  

También puede convertir archivos de certificado existentes si no quiere crear otros nuevos. Vea [Conversión de archivos existentes en archivos compatibles](#convert-existing-files-to-supported-files) para obtener detalles.

### <a name="sample-certificate"></a>Certificado de ejemplo

Puede comparar su certificado con el certificado de ejemplo siguiente. Compruebe que tienen los mismos campos y que el orden de estos es el mismo.

:::image type="content" source="media/how-to-deploy-certificates/sample-certificate.png" alt-text="sample-certificate.":::

## <a name="test-certificates-you-create"></a>Pruebas de los certificados creados

Puede probar los certificados antes de implementarlos en los sensores y las consolas de administración locales. Si quiere comprobar la información del archivo .csr del certificado o del archivo de claves privadas, use estos comandos:

| **Prueba** | **Comando de la CLI** |
|--|--|
| Comprobar una solicitud de firma de certificado (CSR) | openssl req -text -noout -verify -in CSR.csr |
| Comprobar una clave privada  | openssl rsa -in privateKey.key -check  |
| Comprobar un certificado  | openssl x509 -in certificate.crt -text -noout |

Si se produce un error en estas pruebas, vea [Requisitos de parámetros de archivo de certificado](#certificate-file-parameter-requirements) para comprobar que los parámetros del archivo son precisos, o hable con el responsable de certificados.

## <a name="convert-existing-files-to-supported-files"></a>Conversión de archivos existentes en archivos compatibles 

En esta sección se explica cómo convertir los archivos de certificados existentes a formatos compatibles.

|**Descripción** | **Comando de la CLI** |
|--|--|
| Conversión de un archivo .crt en un archivo .pem   | openssl x509 -inform PEM -in <full path>/<pem-file-name>.pem -out <fullpath>/<crt-file-name>.crt  | 
| Conversión de un archivo .pem en un archivo .crt   | openssl x509 -inform PEM -in <full path>/<pem-file-name>.pem -out <fullpath>/<crt-file-name>.crt |  
| Conversión de un archivo PKCS#12 (.pfx .p12) que contiene una clave privada y certificados a .pem   | openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes. Puede agregar -nocerts para que solo se genere la clave privada, o bien agregar -nokeys para generar solo los certificados.  |  

## <a name="troubleshooting"></a>Solución de problemas  

En esta sección se indican varios problemas que pueden producirse durante la carga y la validación de certificados, así como los pasos que se deben seguir para resolverlos.

### <a name="troubleshoot-ca-certificate-upload"></a>Solución de problemas de carga de certificados de CA  

Los usuarios administradores que intentan iniciar sesión en el sensor o en la consola de administración local por primera vez no pueden cargar el certificado firmado por la entidad de certificación si este no se ha creado correctamente o no es válido. Si se produce un error al cargar el certificado, aparecen uno o varios de los mensajes de error:

| **Error de validación de certificado** | **Recomendación** |
|--|--|
| La frase de contraseña no coincide con la clave | Compruebe que ha escrito la frase de contraseña correcta. Si el problema continúa, intente volver a crear el certificado con la frase de contraseña correcta. |
| No se puede validar la cadena de confianza. El certificado y la CA raíz proporcionados no coinciden.  | Asegúrese de que el archivo .pem se correlaciona con el archivo .crt. Si el problema continúa, intente volver a crear el certificado mediante la cadena de confianza correcta (definida por el archivo .pem). |
| Este certificado SSL ha expirado y no se considera válido.  | Cree un certificado nuevo con fechas válidas.|
| Este certificado SSL ha expirado y no se considera válido.  | Cree un certificado nuevo con fechas válidas.|
|La CRL ha revocado este certificado y no se puede confiar en él para una conexión segura | Cree un nuevo certificado sin revocar. |
|No se puede acceder a la ubicación de la CRL (lista de revocación de certificados). Compruebe que se puede acceder a la dirección URL desde este dispositivo | Asegúrese de que la configuración de red permite que el dispositivo acceda al servidor de CRL definido en el certificado. Puede usar un servidor proxy si hay limitaciones para establecer una conexión directa.  
|Error de validación de certificado  | Esto indica un error general en el dispositivo. Póngase en contacto con [Soporte técnico de Microsoft](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c8f35-1b8e-f274-ec11-c6efdd6dd099).|

### <a name="troubleshoot-file-conversions"></a>Solución de problemas de conversión de archivos  

Es posible que la conversión de archivos no cree un certificado válido. Por ejemplo, la estructura de archivos puede ser imprecisa.

Si hay un error de conversión:  

- Use los comandos de conversión descritos en [Conversión de archivos existentes en archivos compatibles](#convert-existing-files-to-supported-files).
- Asegúrese de que los parámetros del archivo son precisos. Vea [Requisitos de tipo de archivo](#file-type-requirements) y [Requisitos de parámetros de archivo de certificado](#certificate-file-parameter-requirements) para obtener detalles.  
- Hable con el responsable de certificados.