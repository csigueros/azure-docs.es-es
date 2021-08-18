---
title: Configuración de seguridad de división y combinación
description: Configure certificados x409 para el cifrado con el servicio de división y combinación para escala elástica.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: mathoma
ms.date: 12/18/2018
ms.openlocfilehash: 3f87c61d5c77d3c355d4536e06eba8619da60e21
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730446"
---
# <a name="split-merge-security-configuration"></a>Configuración de seguridad de división y combinación
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Para usar el servicio de división y combinación, debe configurar correctamente la seguridad. El servicio forma parte de la característica Escalado elástico de Azure SQL Database. Para obtener más información, vea el [Tutorial del servicio de división y combinación de Escalado elástico](elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Configuración de certificados

Los certificados se configuran de dos maneras. 

1. [Para configuración el certificado TLS/SSL](#to-configure-the-tlsssl-certificate)
2. [Configuración del certificado de cliente](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Obtención de certificados

Los certificados se pueden obtener de Entidades de certificación (CA) públicas o desde el [Servicio de certificados de Windows](/windows/win32/seccrypto/certificate-services). Estos son los métodos preferidos para obtener certificados.

Si esas opciones no están disponibles, puede generar **certificados autofirmados**.

## <a name="tools-to-generate-certificates"></a>Herramientas para generar certificados

* [makecert.exe](/previous-versions/dotnet/netframework-4.0/bfsktky3(v=vs.100))
* [pvk2pfx.exe](/windows-hardware/drivers/devtest/pvk2pfx)

### <a name="to-run-the-tools"></a>Ejecución de las herramientas

* Desde el Símbolo del sistema para desarrolladores de Visual Studio, consulte [Símbolo del sistema de Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs) 
  
    Si está instalado, vaya a:
  
    ```console
    %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
    ```

* Obtener el WDK de [Windows 8.1: descargar kits y herramientas](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-tlsssl-certificate"></a>Para configuración el certificado TLS/SSL

Se requiere un certificado TLS/SSL para cifrar la comunicación y autenticar el servidor. Elija el escenario más aplicable entre los tres que aparecen a continuación y ejecute todos sus pasos:

### <a name="create-a-new-self-signed-certificate"></a>Creación de un nuevo certificado autofirmado

1. [Creación de un certificado autofirmado](#create-a-self-signed-certificate)
2. [Creación del archivo PFX para el certificado TLS/SSL autofirmado](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Carga del certificado TLS/SSL en el servicio en la nube](#upload-tlsssl-certificate-to-cloud-service)
4. [Actualización del certificado TLS/SSL en el archivo de configuración de servicio](#update-tlsssl-certificate-in-service-configuration-file)
5. [Importación de la entidad de certificación TLS/SSL](#import-tlsssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Para utilizar un certificado existente desde el almacén de certificados
1. [Exportación del certificado TLS/SSL desde el almacén de certificados](#export-tlsssl-certificate-from-certificate-store)
2. [Carga del certificado TLS/SSL en el servicio en la nube](#upload-tlsssl-certificate-to-cloud-service)
3. [Actualización del certificado TLS/SSL en el archivo de configuración de servicio](#update-tlsssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Para utilizar un certificado existente en un archivo PFX
1. [Carga del certificado TLS/SSL en el servicio en la nube](#upload-tlsssl-certificate-to-cloud-service)
2. [Actualización del certificado TLS/SSL en el archivo de configuración de servicio](#update-tlsssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Configuración del certificado de cliente
Se requieren certificados de cliente para autenticar solicitudes al servicio. Elija el escenario más aplicable entre los tres que aparecen a continuación y ejecute todos sus pasos:

### <a name="turn-off-client-certificates"></a>Desactivación de los certificados de cliente
1. [Desactivación de la autenticación basada en certificado de cliente](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Emisión de nuevos certificados de cliente autofirmados
1. [Creación de una Entidad de certificación autofirmada](#create-a-self-signed-certification-authority)
2. [Carga de un certificado de Entidad de certificación en el servicio en la nube](#upload-ca-certificate-to-cloud-service)
3. [Actualización de un certificado de Entidad de certificación en un archivo de configuración de servicio](#update-ca-certificate-in-service-configuration-file)
4. [Emisión de certificados de cliente](#issue-client-certificates)
5. [Creación de archivos PFX para certificados de cliente](#create-pfx-files-for-client-certificates)
6. [Importación de certificados de cliente](#import-client-certificate)
7. [Copia de las huellas digitales de certificados de cliente](#copy-client-certificate-thumbprints)
8. [Configuración de clientes autorizados en el archivo de configuración de servicio](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Uso de certificados de cliente existentes
1. [Find CA Public Key](#find-ca-public-key)
2. [Carga de un certificado de Entidad de certificación en el servicio en la nube](#upload-ca-certificate-to-cloud-service)
3. [Actualización de un certificado de Entidad de certificación en un archivo de configuración de servicio](#update-ca-certificate-in-service-configuration-file)
4. [Copia de las huellas digitales de certificados de cliente](#copy-client-certificate-thumbprints)
5. [Configuración de clientes autorizados en el archivo de configuración de servicio](#configure-allowed-clients-in-the-service-configuration-file)
6. [Configuración de la comprobación de revocación de certificado de cliente](#configure-client-certificate-revocation-check)

## <a name="allowed-ip-addresses"></a>Direcciones IP permitidas
El acceso a los extremos de servicio puede estar limitado a intervalos concretos de direcciones IP.

## <a name="to-configure-encryption-for-the-store"></a>Configuración del cifrado para el almacén
Se requiere un certificado para cifrar las credenciales que se almacenan en el almacén de metadatos. Elija el escenario más aplicable entre los tres que aparecen a continuación y ejecute todos sus pasos:

### <a name="use-a-new-self-signed-certificate"></a>Use un nuevo certificado autofirmado
1. [Creación de un certificado autofirmado](#create-a-self-signed-certificate)
2. [Crear archivo PFX para certificado de cifrado autofirmado](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Cargar el certificado de cifrado en el servicio en la nube](#upload-encryption-certificate-to-cloud-service)
4. [Actualización del certificado de cifrado en el archivo de configuración de servicio](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Utilizar un certificado existente desde el almacén de certificados
1. [Exportar el certificado de cifrado del almacén de certificados](#export-encryption-certificate-from-certificate-store)
2. [Cargar el certificado de cifrado en el servicio en la nube](#upload-encryption-certificate-to-cloud-service)
3. [Actualización del certificado de cifrado en el archivo de configuración de servicio](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Utilizar un certificado existente de un archivo PFX
1. [Cargar el certificado de cifrado en el servicio en la nube](#upload-encryption-certificate-to-cloud-service)
2. [Actualización del certificado de cifrado en el archivo de configuración de servicio](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>La configuración predeterminada
La configuración predeterminada deniega todo acceso al extremo HTTP. Esta es la configuración recomendada, dado que las solicitudes a estos extremos pueden llevar información delicada, como pueden ser las credenciales de base de datos.
La configuración predeterminada permite todo acceso al extremo HTTPS. Esta configuración puede ser aún más restrictiva.

### <a name="changing-the-configuration"></a>Cambio de la configuración
El grupo de reglas de control de acceso que se aplican a un extremo se configuran en la sección **\<EndpointAcls>** del **archivo de configuración de servicio**.

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

Las reglas de un grupo de control de acceso se configuran en una sección \<AccessControl name=""> del archivo de configuración de servicio. 

El formato se explica en la documentación de listas de control de acceso de red.
Por ejemplo, para permitir únicamente a las direcciones IP del intervalo comprendido entre 100.100.0.0 y 100.100.255.255 el acceso al extremo HTTPS, las reglas se parecerían a esta:

```xml
<AccessControl name="Retricted">
    <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
    <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
</AccessControl>
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />
</EndpointAcls>
```

## <a name="denial-of-service-prevention"></a>Prevención de la denegación de servicio
Existen dos mecanismos distintos compatibles para detectar y evitar los ataques de denegación de servicio:

* Restringir la cantidad de solicitudes simultáneas por host remoto (desactivado de manera predeterminada)
* Restringir la velocidad de acceso por host remoto (activado de manera predeterminada)

Estos mecanismos se basan en las características más documentadas en Seguridad de IP dinámica en IIS. Cuando cambie esta configuración, tenga cuidado con los siguientes factores:

* El comportamiento de los servidores proxy y los dispositivos de traducción de direcciones de red sobre la información de host remoto
* Se consideran todas las solicitudes a cualquier recurso en el rol web (por ejemplo, carga de scripts, imágenes, etc.)

## <a name="restricting-number-of-concurrent-accesses"></a>Restricción de la cantidad de acceso simultáneos
Los ajustes que configuran este comportamiento son:

```xml
<Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
<Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />
```

Cambie DynamicIpRestrictionDenyByConcurrentRequests a true para habilitar esta protección.

## <a name="restricting-rate-of-access"></a>Restricción de la velocidad del acceso
Los ajustes que configuran este comportamiento son:

```xml
<Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
<Setting name="DynamicIpRestrictionMaxRequests" value="100" />
<Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />
```

## <a name="configuring-the-response-to-a-denied-request"></a>Configuración de la respuesta a una solicitud denegada
Los siguientes ajustes configuran la respuesta a una solicitud denegada:

```xml
<Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
```

Consulte la documentación para Seguridad de IP dinámica en IIS para otros valores compatibles.

## <a name="operations-for-configuring-service-certificates"></a>Operaciones para configurar certificados de servicio
Este tema es solo para referencia. Siga los pasos de configuración descritos en:

* Configuración del certificado TLS/SSL
* Configuración de certificados de cliente

## <a name="create-a-self-signed-certificate"></a>Creación de un certificado autofirmado
Ejecute:

```console
makecert ^
  -n "CN=myservice.cloudapp.net" ^
  -e MM/DD/YYYY ^
  -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
  -a sha256 -len 2048 ^
  -sv MySSL.pvk MySSL.cer
```

Personalizar:

* -n con la dirección URL de servicio. Caracteres comodín ("CN=*.cloudapp.net") y nombres alternativos ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net") son compatibles.
* -e con la fecha de caducidad del certificado Cree una contraseña segura y especifíquela cuando se le solicite.

## <a name="create-pfx-file-for-self-signed-tlsssl-certificate"></a>Creación del archivo PFX para el certificado TLS/SSL autofirmado
Ejecute:

```console
pvk2pfx -pvk MySSL.pvk -spc MySSL.cer
```

Escriba la contraseña y, a continuación, exporte el certificado con estas opciones:

* Sí, exportar la clave privada
* Exportar todas las propiedades extendidas

## <a name="export-tlsssl-certificate-from-certificate-store"></a>Exportación del certificado TLS/SSL desde el almacén de certificados
* Busque el certificado
* Haga clic en Acciones -> Todas las tareas -> Exportar…
* Exporte el certificado a un archivo .PFX con estas opciones:
  * Sí, exportar la clave privada
  * Incluir todos los certificados en la ruta de certificación si es posible *Exportar todas las propiedades extendidas

## <a name="upload-tlsssl-certificate-to-cloud-service"></a>Carga del certificado TLS/SSL en el servicio en la nube
Cargue el certificado con el archivo .PFX existente o generado con el par de claves TLS:

* Escriba la contraseña para proteger la información de clave privada

## <a name="update-tlsssl-certificate-in-service-configuration-file"></a>Actualización del certificado TLS/SSL en el archivo de configuración de servicio
Actualice el valor de huella digital de la siguiente configuración en el archivo de configuración de servicio con la huella digital del certificado cargado al servicio en la nube:

```console
<Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="import-tlsssl-certification-authority"></a>Importación de la entidad de certificación TLS/SSL
Siga estos pasos en todas las cuentas/máquinas que se comunicarán con el servicio:

* Haga doble clic en el archivo .CER en el Explorador de Windows
* En el cuadro de diálogo Certificado, haga clic en Instalar certificado...
* Importe el certificado al almacén de Entidades de certificación de raíz de confianza

## <a name="turn-off-client-certificate-based-authentication"></a>Desactivación de la autenticación basada en certificado de cliente
Solo se admite la autenticación basada en certificado de cliente y deshabilitarla permitirá el acceso público a los puntos de conexión del servicio, a menos que existan otros mecanismos (por ejemplo, Microsoft Azure Virtual Network).

Cambie esta configuración a falso en el archivo de configuración del servicio para deshabilitar la característica:

```xml
<Setting name="SetupWebAppForClientCertificates" value="false" />
<Setting name="SetupWebserverForClientCertificates" value="false" />
```

A continuación, copie la misma huella digital del certificado TLS/SSL en la configuración del certificado de entidad de certificación:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>Creación de una Entidad de certificación autofirmada
Ejecute los siguientes pasos para crear un certificado autofirmado que actúe como Entidad de certificación:

```console
makecert ^
-n "CN=MyCA" ^
-e MM/DD/YYYY ^
 -r -cy authority -h 1 ^
 -a sha256 -len 2048 ^
  -sr localmachine -ss my ^
  MyCA.cer
```

Para personalizarlo

* -e con la fecha de caducidad de la certificación

## <a name="find-ca-public-key"></a>Búsqueda de clave pública de Entidad de certificación
Una Entidad de certificación de la confianza del servicio debe haber emitido todos los certificados de cliente. Encuentre la clave pública para la Entidad de certificación que emitió los certificados de cliente que se van a usar para autenticación a fin de cargarla al servicio en la nube.

Si el archivo con la clave pública no está disponible, expórtelo desde el almacén de certificados:

* Busque el certificado
  * Busque un certificado de cliente emitido por la misma Entidad de certificación
* Haga doble clic en el certificado.
* Seleccione la pestaña Ruta de certificación en el cuadro de diálogo Certificado.
* Haga doble clic en la entrada de CA de la ruta.
* Tome notas de las propiedades del certificado.
* Cierre el cuadro de diálogo **Certificado** .
* Busque el certificado
  * Busque la CA anotada anteriormente.
* Haga clic en Acciones -> Todas las tareas -> Exportar…
* Exporte el certificado a un archivo .CER con estas opciones:
  * **No, no exportar la clave privada**
  * Incluir todos los certificados en la ruta de certificación si es posible.
  * Exportar todas las propiedades extendidas.

## <a name="upload-ca-certificate-to-cloud-service"></a>Carga de un certificado de Entidad de certificación en el servicio en la nube
Cargue el certificado con el archivo .CER existente o generado con la clave pública de CA.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Actualización de un certificado de Entidad de certificación en un archivo de configuración de servicio
Actualice el valor de huella digital de la siguiente configuración en el archivo de configuración de servicio con la huella digital del certificado cargado al servicio en la nube:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

Actualice el valor de la siguiente configuración con la misma huella digital:

```xml
<Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
```

## <a name="issue-client-certificates"></a>Emisión de certificados de cliente
Cada persona autorizada a tener acceso al servicio debe tener un certificado de cliente emitido para su uso exclusivo y debe elegir su propia contraseña segura para proteger su clave privada. 

Los siguientes pasos se deben ejecutar en la misma máquina donde se generó y almacenó el certificado de CA autofirmado:

```console
makecert ^
  -n "CN=My ID" ^
  -e MM/DD/YYYY ^
  -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
  -a sha256 -len 2048 ^
  -in "MyCA" -ir localmachine -is my ^
  -sv MyID.pvk MyID.cer
```

Personalización:

* -n con un identificador para el cliente que se autenticará con este certificado
* -e con la fecha de caducidad del certificado
* MyID.pvk y MyID.cer con nombres de archivo únicos para este certificado de cliente

Este comando solicitará que se cree una contraseña y se use una vez. Utilice una contraseña segura.

## <a name="create-pfx-files-for-client-certificates"></a>Creación de archivos PFX para certificados de cliente
Para cada certificado de cliente generado, ejecute:

```console
pvk2pfx -pvk MyID.pvk -spc MyID.cer
```

Personalización:

```console
MyID.pvk and MyID.cer with the filename for the client certificate
```

Escriba la contraseña y, a continuación, exporte el certificado con estas opciones:

* Sí, exportar la clave privada
* Exportar todas las propiedades extendidas
* La persona para quien se emite este certificado debe elegir la contraseña de exportación

## <a name="import-client-certificate"></a>Importación de certificados de cliente
Cada persona para quien se ha emitido un certificado de cliente debe importar el par de claves en las máquinas que usará para comunicarse con el servicio:

* Haga doble clic en el archivo .PFX en el Explorador de Windows
* Importe el certificado al almacén personal con al menos esta opción:
  * Incluir todas las propiedades extendidas comprobadas

## <a name="copy-client-certificate-thumbprints"></a>Copia de las huellas digitales de certificados de cliente
Cada usuario para el que se ha emitido un certificado de cliente debe seguir estos pasos para obtener la huella digital de su certificado, el que se agregará al archivo de configuración de servicio:

* Ejecute certmgr.exe
* Seleccione la pestaña Personal
* Haga doble clic en el certificado de cliente que se usará para la autenticación
* En el cuadro de diálogo Certificado que se abre, seleccione la pestaña Detalles
* Asegúrese de que Mostrar esté mostrando Todo
* Seleccione el campo denominado Huella digital en la lista
* Copie el valor de la huella digital
  * Elimine los caracteres Unicode no visibles delante del primer dígito
  * Elimine todos los espacios

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Configuración de clientes autorizados en el archivo de configuración de servicio
Actualice el valor de los siguientes ajustes en el archivo de configuración de servicio con una lista delimitada por comas de las huellas digitales de los certificados de cliente que tienen permitido el acceso al servicio:

```xml
<Setting name="AllowedClientCertificateThumbprints" value="" />
```

## <a name="configure-client-certificate-revocation-check"></a>Configuración de la comprobación de revocación de certificado de cliente
La configuración predeterminada no se comprueba con la Entidad de certificación para el estado de revocación de certificado de cliente. Para activar las comprobaciones, si la Entidad de certificación que emitió los certificados de cliente admite dichas comprobaciones, cambie la siguiente configuración por uno de los valores definidos en la enumeración X509RevocationMode:

```xml
<Setting name="ClientCertificateRevocationCheck" value="NoCheck" />
```

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Crear archivo PFX para certificados de cifrado autofirmados
Para un certificado de cifrado, ejecute:

```console
pvk2pfx -pvk MyID.pvk -spc MyID.cer
```

Personalización:

```console
MyID.pvk and MyID.cer with the filename for the encryption certificate
```

Escriba la contraseña y, a continuación, exporte el certificado con estas opciones:

* Sí, exportar la clave privada
* Exportar todas las propiedades extendidas
* Necesitará la contraseña al cargar el certificado en el servicio en la nube.

## <a name="export-encryption-certificate-from-certificate-store"></a>Exportar el certificado de cifrado del almacén de certificados
* Busque el certificado
* Haga clic en Acciones -> Todas las tareas -> Exportar…
* Exporte el certificado a un archivo .PFX con estas opciones: 
  * Sí, exportar la clave privada
  * Incluir todos los certificados en la ruta de certificación si es posible 
* Exportar todas las propiedades extendidas

## <a name="upload-encryption-certificate-to-cloud-service"></a>Cargar el certificado de cifrado en el servicio en la nube
Cargue el certificado con el archivo .PFX existente o generado con el par de claves de cifrado:

* Escriba la contraseña para proteger la información de clave privada

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Actualización del certificado de cifrado en el archivo de configuración de servicio
Actualice el valor de huella digital de la siguiente configuración en el archivo de configuración de servicio con la huella digital del certificado cargado al servicio en la nube:

```xml
<Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="common-certificate-operations"></a>Operaciones comunes de certificado
* Configuración del certificado TLS/SSL
* Configuración de certificados de cliente

## <a name="find-certificate"></a>Busque el certificado
Siga estos pasos:

1. Ejecute mmc.exe.
2. Archivo -> Agregar o quitar complemento…
3. Seleccione **Certificados**.
4. Haga clic en **Agregar**.
5. Elija la ubicación del almacén de certificados.
6. Haga clic en **Finalizar**
7. Haga clic en **OK**.
8. Expanda **Certificados**.
9. Expanda el nodo del almacén de certificados.
10. Expanda el nodo secundario Certificado.
11. Seleccione un certificado en la lista.

## <a name="export-certificate"></a>Exportación de certificado
En el **asistente para exportar certificados**:

1. Haga clic en **Next**.
2. Seleccione **Sí** y **Exportar la clave privada**.
3. Haga clic en **Next**.
4. Seleccione el formato deseado del archivo de salida.
5. Marque las opciones deseadas.
6. Marque **Contraseña**.
7. Escriba una contraseña segura y confírmela.
8. Haga clic en **Next**.
9. Escriba o busque un nombre de archivo donde almacenar el certificado (use una extensión .PFX).
10. Haga clic en **Next**.
11. Haga clic en **Finalizar**
12. Haga clic en **OK**.

## <a name="import-certificate"></a>Importación de certificado
En el asistente para importar certificados:

1. Seleccione la ubicación del almacén.
   
   * Seleccione **Usuario actual** , si solo los procesos en ejecución bajo el usuario actual tendrán acceso al servicio
   * Seleccione **Máquina local** , si otros procesos de este equipo tendrán acceso al servicio
2. Haga clic en **Next**.
3. Si se importa desde un archivo, confirme la ruta del archivo.
4. Si se importa un archivo .PFX:
   1. Escriba la contraseña para proteger la clave privada
   2. Seleccione las opciones de importación
5. Seleccione "Colocar" certificados en el siguiente almacén
6. Haga clic en **Examinar**.
7. Seleccione el almacén deseado.
8. Haga clic en **Finalizar**
   
   * Si se eligió el almacén de Entidad de certificación raíz de confianza, haga clic en **Sí**.
9. Haga clic en **Aceptar** en todas las ventanas de diálogo.

## <a name="upload-certificate"></a>Carga del certificado
En [Azure Portal](https://portal.azure.com/)

1. Seleccione **Cloud Services**.
2. Seleccione el servicio en la nube.
3. Haga clic en **Certificados** en el menú superior.
4. En la barra inferior, haga clic en **Cargar**.
5. Seleccione el archivo de certificado.
6. Si es un archivo .PFX, escriba la contraseña de la clave privada.
7. Una vez realizado, copie la huella digital del certificado desde la entrada nueva en la lista.

## <a name="other-security-considerations"></a>Otras consideraciones de seguridad
La configuración TLS descrita en este documento cifra la comunicación entre el servicio y sus clientes cuando se usa el punto de conexión HTTPS. Esto es importante, porque las credenciales para el acceso a la base de datos y potencialmente a otra información confidencial están contenidas en la comunicación. Pero tenga en cuenta que el servicio conserva el estado interno, incluidas las credenciales, en sus tablas internas de la base de datos de Azure SQL Database que ha proporcionado para el almacenamiento de metadatos en la suscripción de Microsoft Azure. Esa base de datos se definió como parte de los siguientes ajustes en el archivo de configuración de servicio (archivo .CSCFG): 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

Las credenciales almacenadas en esta base de datos están cifradas. Sin embargo, como práctica recomendada, asegúrese de que los roles de web y de trabajo de sus implementaciones de servicio se mantienen actualizados y seguros, a medida que ambos tienen acceso a la base de datos de metadatos y el certificado usado para el cifrado y descifrado de credenciales almacenadas. 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]