---
title: Azure Service Fabric ters proxy güvenli iletişimi | Microsoft Docs
description: Uçtan uca iletişimin güvenliğini sağlamak için ters proxy 'yi yapılandırın.
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: e915e689f09ba7f5c92958ebf8531aa67eef4493
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933947"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Ters ara sunucu ile güvenli bir hizmete bağlanma

Bu makalede, ters proxy ve hizmetler arasında güvenli bağlantı kurmayı ve böylece uçtan uca güvenli kanalı etkinleştirmeyi açıklar. Ters proxy hakkında daha fazla bilgi edinmek için bkz. [Azure 'Da ters proxy Service Fabric](service-fabric-reverseproxy.md)

Güvenli hizmetlere bağlanma yalnızca ters proxy, HTTPS üzerinde dinlemek üzere yapılandırıldığında desteklenir. Bu makalede bu durum olduğu varsayılır.
Service Fabric ' de ters proxy 'yi yapılandırmak için [Azure Service Fabric 'da ters proxy ayarlama](service-fabric-reverseproxy-setup.md) bölümüne bakın.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Ters proxy ve hizmetler arasında güvenli bağlantı kurulumu 

### <a name="reverse-proxy-authenticating-to-services"></a>Hizmetlere ters proxy kimlik doğrulaması:
Ters proxy, sertifikasını kullanarak kendisini hizmetlere tanıtır. Azure kümeleri için sertifika, Kaynak Yöneticisi şablonunun [**Microsoft. ServiceFabric/kümeler**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [kaynak türü bölümünde](../azure-resource-manager/resource-group-authoring-templates.md) bulunan ***smarproxycertificate*** özelliği ile belirtilir. Tek başına kümeler için, sertifika, ClusterConfig. json ' un **güvenlik** bölümünde bulunan bir ***smarproxycertificate*** veya ***smarproxycertificatecommonnames*** özelliği ile belirtilir. Daha fazla bilgi için bkz. [tek başına kümelerde ters proxy 'Yi etkinleştirme](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters). 

Hizmetler, ters proxy tarafından sunulan sertifikayı doğrulamak için mantığı uygulayabilir. Hizmetler, kabul edilen istemci sertifikası ayrıntılarını yapılandırma paketindeki yapılandırma ayarları olarak belirtebilir. Bu, çalışma zamanında okunabilir ve ters proxy tarafından sunulan sertifikayı doğrulamak için kullanılabilir. Yapılandırma ayarlarını eklemek için [Uygulama parametrelerini yönetme](service-fabric-manage-multiple-environment-app-configuration.md) bölümüne bakın. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Hizmet tarafından sunulan sertifika aracılığıyla hizmetin kimliğini ters proxy olarak doğrulayan:
Ters proxy, hizmetler tarafından sunulan sertifikaların sunucu sertifikası doğrulamasını gerçekleştirmek için aşağıdaki ilkeleri destekler: None, ServiceCommonNameAndIssuer ve ServiceCertificateThumbprints.
Kullanılacak ters proxy ilkesini seçmek için, [Fabricsettings](service-fabric-cluster-fabric-settings.md)altındaki **Applicationgateway/http** bölümünde **applicationcertificatevalidationpolicy** ' ı belirtin.

Sonraki bölümde, bu seçeneklerin her biri için yapılandırma ayrıntıları gösterilmektedir.

### <a name="service-certificate-validation-options"></a>Hizmet sertifikası doğrulama seçenekleri 

- **Hiçbiri**: ters proxy, proxy kullanan hizmet sertifikasının doğrulanmasını atlar ve güvenli bağlantı kurar. Bu, varsayılan davranıştır.
[**Applicationgateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) bölümünde **none** değeriyle **applicationcertificatevalidationpolicy** değerini belirtin.

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "None"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **Servicecommonnameandissuer**: ters proxy, sertifikanın ortak adına ve anında verenin parmak izine göre hizmet tarafından sunulan sertifikayı doğrular: değer **Ile applicationcertificatevalidationpolicy belirtin**  [**Applicationgateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) bölümünde ServiceCommonNameAndIssuer.

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCommonNameAndIssuer"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Hizmet ortak adı ve veren parmak izleri listesini belirtmek için, aşağıda gösterildiği gibi **Fabricsettings**altına bir [**Applicationgateway/http/ServiceCommonNameAndIssuer**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) bölümü ekleyin. **Parametreler** dizisine birden çok sertifika ortak adı ve veren parmak izi çiftleri eklenebilir. 

   Uç nokta ters ara sunucusu, ortak ad ve veren parmak izi burada belirtilen değerlerden biriyle eşleşen bir sertifika sunduğunda, SSL kanalı oluşturulur. 
   Sertifika ayrıntılarının eşleşmemesi başarısız olduktan sonra, ters proxy istemcinin isteği 502 (Hatalı ağ geçidi) durum kodu ile başarısız olur. HTTP durum satırı, "geçersiz SSL sertifikası" ifadesini de içerecektir. 

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
               "parameters": [
                 {
                   "name": "WinFabric-Test-Certificate-CN1",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
                 },
                 {
                   "name": "WinFabric-Test-Certificate-CN2",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCertificateThumbprints**: ters proxy, kendi parmak izine bağlı olarak proxy hizmet sertifikasını doğrular. Hizmetler kendinden imzalı sertifikalarla yapılandırıldığında bu yolu kullanmaya devam edebilirsiniz: [**Applicationgateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) içinde **ServiceCertificateThumbprints** değeri Ile **applicationcertificatevalidationpolicy** belirtin kısmı.

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCertificateThumbprints"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Ayrıca, **ServiceCertificateThumbprints** girişi ile birlikte parmak Izlerini **Applicationgateway/http** bölümünde de belirtin. Birden çok parmak izi, değer alanında aşağıda gösterildiği gibi, virgülle ayrılmış bir liste olarak belirtilebilir:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                   ...
                 {
                   "name": "ServiceCertificateThumbprints",
                   "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Sunucu sertifikasının parmak izi bu yapılandırma girişinde listeleniyorsa, ters proxy, SSL bağlantısını başarılı bir şekilde başarılı olur. Aksi takdirde, bağlantıyı sonlandırır ve istemcinin isteği 502 (Hatalı ağ geçidi) ile başarısız olur. HTTP durum satırı, "geçersiz SSL sertifikası" ifadesini de içerecektir.

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Hizmetler güvenli hale geldiğinde ve güvenli olmayan uç noktalar sunan uç nokta seçim mantığı
Service Fabric, bir hizmet için birden çok uç noktanın yapılandırılmasını destekler. Daha fazla bilgi için bkz. [hizmet bildiriminde kaynakları belirtme](service-fabric-service-manifest-resources.md).

Ters proxy, isteği [HIZMET URI](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy)'Sindeki **listenername** sorgu parametresine göre iletmek için uç noktalardan birini seçer. **Listenername** parametresi belirtilmemişse, ters proxy uç noktalar listesinden herhangi bir uç nokta seçebilir. Hizmet için yapılandırılan uç noktalara bağlı olarak, seçilen uç nokta bir HTTP veya HTTPS uç noktası olabilir. Ters proxy 'nin "yalnızca güvenli modda" çalışmasını istediğiniz senaryolar veya gereksinimler olabilir; diğer bir deyişle, güvenli ters proxy 'nin istekleri güvenli olmayan uç noktalara iletmesini istemezsiniz. Ters proxy 'yi güvenli moda ayarlamak için, [**Applicationgateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) bölümünde **true** değerine sahip **secureonlymode** yapılandırma girdisini belirtin.   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> [!NOTE]
> **Secureonlymode**'da çalışırken, istemci http (güvenli olmayan) uç noktasına karşılık gelen bir **listenername** belirttiyseniz, ters proxy, isteği 404 (bulunamadı) http durum kodu ile başarısız olur.

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Ters ara sunucu aracılığıyla istemci sertifikası kimlik doğrulamasını ayarlama
SSL sonlandırma, ters proxy 'de gerçekleşir ve tüm istemci sertifikası verileri kaybedilir. Hizmetlerin istemci sertifikası kimlik doğrulaması gerçekleştirmesi için [**Applicationgateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) bölümünde **forwardclientcertificate** ayarını belirtin.

1. **Forwardclientcertificate** **false**olarak ayarlandığında, ters proxy, istemci ile SSL el sıkışması sırasında istemci sertifikasını istemeyecektir.
Bu, varsayılan davranıştır.

2. **Forwardclientcertificate** **değeri true**olarak ayarlandığında ters proxy, istemci ile SSL el sıkışması sırasında istemcinin sertifikasını ister.
Daha sonra istemci sertifikası verilerini **X-Client-Certificate**adlı özel bir http üst bilgisinde iletecektir. Üst bilgi değeri, istemci sertifikasının Base64 kodlamalı pek biçim dizesidir. Hizmet, sertifika verilerini inceledikten sonra uygun durum koduna sahip isteği başarılı bir şekilde devreder.
İstemci bir sertifika sunmıyorsa, ters proxy boş bir üst bilgiyi iletir ve hizmetin durumu işlemesini sağlar.

> [!NOTE]
> Ters proxy bir boyutundaydı ileticidir. İstemci sertifikasının herhangi bir doğrulamasını gerçekleştirmeyecektir.


## <a name="next-steps"></a>Sonraki adımlar
* [Bir kümede ters proxy ayarlayın ve yapılandırın](service-fabric-reverseproxy-setup.md).
* [Güvenli hizmetlere bağlanmak için ters proxy 'Yi yapılandırma](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services) konusuna bakın
* [GitHub üzerinde örnek bir projede](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)bulunan HIZMETLER arasındaki HTTP iletişimi örneğini inceleyin.
* [Reliable Services uzaktan iletişim ile uzak yordam çağrıları](service-fabric-reliable-services-communication-remoting.md)
* [Reliable Services 'de OWıN kullanan Web API 'SI](service-fabric-reliable-services-communication-webapi.md)
* [Küme sertifikalarını yönetme](service-fabric-cluster-security-update-certs-azure.md)
