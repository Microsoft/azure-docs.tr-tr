---
title: Linux 'ta uygulamalar için sertifikaları yapılandırma
description: Linux kümesinde Service Fabric çalışma zamanına sahip uygulamanıza yönelik sertifikaları yapılandırın
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: pepogors
ms.openlocfilehash: 70f9cc38d84681f68c10882889214648a4dd2624
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785575"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Linux kümelerinde sertifikalar ve güvenlik

Bu makalede, Linux kümelerinde X. 509.440 sertifikalarını yapılandırma hakkında bilgi sağlanır.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Linux düğümlerinde X. 509.440 sertifikalarının konumu ve biçimi

Service Fabric, genellikle Linux küme düğümlerinde */var/lib/sfcerts* dizininde X. 509.440 sertifikalarının bulunmasını bekler. Bu, küme sertifikalarının, istemci sertifikalarının vb. için geçerlidir. Bazı durumlarda, sertifikalar için *var/lib/sfcert* klasöründen farklı bir konum belirtebilirsiniz. Örneğin, Service Fabric Java SDK 'Sı kullanılarak oluşturulan Reliable Services, uygulamaya özgü bazı sertifikaların yapılandırma paketiyle (Settings.xml) farklı bir konum belirtebilirsiniz. Daha fazla bilgi edinmek için bkz. [yapılandırma paketinde başvurulan Sertifikalar (Settings.xml)](#certificates-referenced-in-the-configuration-package-settingsxml).

Linux kümeleri için Service Fabric, sertifikaların hem sertifikayı hem de özel anahtarı içeren bir. ped dosyası olarak veya sertifikayı içeren bir. CRT dosyası olarak veya özel anahtarı içeren bir. Key dosyası olarak mevcut olmasını bekler. Tüm dosyalar pek biçiminde olmalıdır. 

Sertifikanızı, bir [Kaynak Yöneticisi şablonu](./service-fabric-cluster-creation-create-template.md) veya [PowerShell](/powershell/module/az.servicefabric/) komutları kullanarak Azure Key Vault yüklerseniz, sertifika her bir düğümdeki */var/lib/sfcerts* dizininde doğru biçimde yüklenir. Bir sertifikayı başka bir yöntemle yüklerseniz, sertifikanın küme düğümlerine doğru yüklendiğinden emin olmalısınız.

## <a name="certificates-referenced-in-the-application-manifest"></a>Uygulama bildiriminde başvurulan sertifikalar

Uygulama bildiriminde belirtilen sertifikaların (örneğin, [**Secretscercertificate**](./service-fabric-service-model-schema-elements.md#secretscertificate-element) veya [**endpointcertificate**](./service-fabric-service-model-schema-elements.md#endpointcertificate-element) öğelerinden) */var/lib/sfcerts* dizininde bulunması gerekir. Uygulama bildiriminde sertifikaları belirtmek için kullanılan öğeler bir yol özniteliği almaz, bu nedenle sertifikaların varsayılan dizinde mevcut olması gerekir. Bu öğeler isteğe bağlı bir **X509StoreName** özniteliği alır. Varsayılan değer, Linux düğümlerinde */var/lib/sfcerts* dizinini gösteren "My" ("My") ' dir. Linux kümesinde başka herhangi bir değer tanımsızdır. Linux kümelerinde çalışan uygulamalar için **X509StoreName** özniteliğini atlamanızı öneririz. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>Yapılandırma paketinde başvurulan Sertifikalar (Settings.xml)

Bazı hizmetlerde, [Configpackage](./service-fabric-application-and-service-manifests.md) 'de X. 509.440 sertifikalarını yapılandırabilirsiniz (varsayılan olarak, Settings.xml). Örneğin, Service Fabric .NET Core veya Java SDK 'Ları ile oluşturulan Reliable Services Hizmetleri için RPC kanallarının güvenliğini sağlamak için kullanılan sertifikaları bildirdiğinizde bu durum söz konusu olur. Yapılandırma paketindeki sertifikalara başvurmanın iki yolu vardır. Destek, .NET Core ve Java SDK 'Ları arasında farklılık gösterir.

### <a name="using-x509-securitycredentialstype"></a>X509 SecurityCredentialsType kullanma

.NET veya Java SDK 'Ları Ile **Securitycredentialstype** için **x509** belirtebilirsiniz. Bu (.net Java) `X509Credentials` türünde ([.net Java](/previous-versions/azure/reference/mt124925(v=azure.100)) / [](/java/api/system.fabric.x509credentials) `SecurityCredentials` [](/previous-versions/azure/reference/mt124894(v=azure.100)) / [](/java/api/system.fabric.securitycredentials)) buna karşılık gelir.

**X509** başvurusu, sertifikayı bir sertifika deposunda konumlandırır. Aşağıdaki XML, sertifikanın konumunu belirtmek için kullanılan parametreleri gösterir:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

Linux üzerinde çalışan bir hizmet için, **LocalMachine** / **My** , */var/lib/sfcerts* dizinini, sertifikalar için varsayılan konuma yönlendirir. Linux için, **Certificatestorelocation** ve **CertificateStoreName** 'in diğer birleşimleri tanımsızdır. 

Her zaman **Certificatestorelocation** parametresi için **LocalMachine** belirtin. Varsayılan "My" olarak ayarlandığından **CertificateStoreName** parametresinin belirtilmesi gerekmez. **X509** başvurusuyla, sertifika dosyalarının küme düğümündeki */var/lib/sfcerts* dizininde bulunması gerekir.  

Aşağıdaki XML, bu stile dayalı bir **TransportSettings** bölümünü gösterir:

```xml
<Section Name="HelloWorldStatefulTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
    <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
    <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
</Section>
```

### <a name="using-x509_2-securitycredentialstype"></a>X509_2 SecurityCredentialsType kullanma

Java SDK 'Sı ile **Securitycredentialstype** için **X509_2** belirtebilirsiniz. Bu, (Java `X509Credentials2` )[](/java/api/system.fabric.x509credentials2)türüne karşılık gelir `SecurityCredentials` .[](/java/api/system.fabric.securitycredentials) 

Bir **X509_2** başvurusuyla, sertifikayı */var/lib/sfcerts* dışında bir dizinde bulabilmeniz için bir yol parametresi belirtirsiniz.  Aşağıdaki XML, sertifikanın konumunu belirtmek için kullanılan parametreleri gösterir: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

Aşağıdaki XML, bu stile dayalı bir **TransportSettings** bölümünü gösterir.

```xml
<!--Section name should always end with "TransportSettings".-->
<!--Here we are using a prefix "HelloWorldStateless".-->
<Section Name="HelloWorldStatelessTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509_2" />
    <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
    <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
</Section>
```

> [!NOTE]
> Sertifika, önceki XML 'de bir. CRT dosyası olarak belirtilir. Bu, aynı konumda özel anahtarı içeren bir. Key dosyası da olduğu anlamına gelir.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Reliable Services uygulamasını Linux kümelerinde çalışacak şekilde yapılandırma

Service Fabric SDK 'Ları, platformdan yararlanmak için Service Fabric çalışma zamanı API 'Leriyle iletişim kurmanıza olanak tanır. Güvenli Linux kümelerinde bu işlevselliği kullanan herhangi bir uygulamayı çalıştırdığınızda, uygulamanızı Service Fabric çalışma zamanına göre doğrulamak için kullanabileceği bir sertifika ile yapılandırmanız gerekir. .NET Core veya Java SDK 'Ları kullanılarak yazılan Service Fabric güvenilir hizmet hizmetleri içeren uygulamalar bu yapılandırmayı gerektirir. 

Bir uygulamayı yapılandırmak için, *ApplicationManifest.xml* dosyasındaki **ApplicationManifest** etiketinin altında bulunan **Sertifikalar** etiketinin altına bir [**secretscercertificate**](./service-fabric-service-model-schema-elements.md#secretscertificate-element) öğesi ekleyin. Aşağıdaki XML, parmak izi tarafından başvurulan bir sertifikayı gösterir: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

Küme sertifikasına veya her bir küme düğümüne yüklediğiniz bir sertifikaya başvurabilirsiniz. Linux 'ta, sertifika dosyalarının */var/lib/sfcerts* dizininde mevcut olması gerekir. Daha fazla bilgi edinmek için bkz. [Linux düğümlerinde X. 509.440 sertifikalarının konumu ve biçimi](#location-and-format-of-x509-certificates-on-linux-nodes).
