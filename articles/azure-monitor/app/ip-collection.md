---
title: Azure Application Insights IP adresi koleksiyonu | Microsoft Docs
description: IP adreslerinin ve coğrafi konum 'un Azure Application Insights nasıl işlendiğini anlama
ms.topic: conceptual
ms.date: 09/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 91b3aa07720e39aa8aeeceb9c35e38205e7d7c76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584073"
---
# <a name="geolocation-and-ip-address-handling"></a>Coğrafi konum ve IP adresi işleme

Bu makalede, coğrafi arama ve IP adresi işlemenin Application Insights ' de nasıl çalıştığı ve varsayılan davranışın nasıl değiştirileceği açıklanmaktadır.

## <a name="default-behavior"></a>Varsayılan davranış

Varsayılan olarak IP adresleri geçici olarak toplanır, ancak Application Insights depolanmaz. Temel işlem aşağıdaki gibidir:

Telemetri Azure 'a gönderildiğinde, IP adresi [Maxakılda GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/)kullanarak coğrafi konum araması yapmak için kullanılır. Bu aramanın sonuçları, ve alanlarını doldurmak için kullanılır `client_City` `client_StateOrProvince` `client_CountryOrRegion` . Daha sonra adres atılır ve `0.0.0.0` `client_IP` alanına yazılır.

* Tarayıcı telemetrisi: gönderenin IP adresini geçici olarak topladık. IP adresi, giriş uç noktası tarafından hesaplanır.
* Sunucu telemetrisi: Application Insights telemetri modülü, istemci IP adresini geçici olarak toplar. Üst bilgi ayarlandığında IP adresi yerel olarak toplanmaz `X-Forwarded-For` .

Bu davranış, gereksiz kişisel veri toplamayı önlemeye yardımcı olmak için tasarımdır. Mümkün olduğunda, kişisel veri toplamayı önetmenizi öneririz. 

## <a name="overriding-default-behavior"></a>Varsayılan davranışı geçersiz kılma

Varsayılan olarak IP adreslerini toplamayın. Bu davranışı geçersiz kılma esnekliği de sunuyoruz. Ancak, koleksiyonun herhangi bir uyumluluk gereksinimini veya yerel düzenlemeleri bozmadığından emin olmanız önerilir. 

Application Insights kişisel veri işleme hakkında daha fazla bilgi edinmek için [kişisel veriler kılavuzuna](../logs/personal-data-mgmt.md)bakın.

## <a name="storing-ip-address-data"></a>IP adresi verileri depolanıyor

IP toplamayı ve depolamayı etkinleştirmek için `DisableIpMasking` Application Insights bileşenin özelliği olarak ayarlanmalıdır `true` . Bu özellik Azure Resource Manager şablonlar aracılığıyla veya REST API çağırarak ayarlanabilir. 

### <a name="azure-resource-manager-template"></a>Azure Resource Manager Şablonu

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>Portal 

Yalnızca tek bir Application Insights kaynağı için davranışı değiştirmeniz gerekiyorsa, Azure portal kullanın. 

1. **Otomasyon**  >  **dışarı aktarma şablonuna** Application Insights kaynak > gidin 

2. **Dağıt** 'ı seçin

    !["Dağıt" sözcüğünün kırmızı renkle vurgulanmış düğme](media/ip-collection/deploy.png)

3. **Şablonu Düzenle**' yi seçin.

    ![Kırmızı renkle vurgulanmış "Düzenle" sözcüğünün bulunduğu düğme](media/ip-collection/edit-template.png)

4. Kaynağınız için JSON 'da aşağıdaki değişiklikleri yapın ve ardından **Kaydet**' i seçin:

    ![Ekran görüntüsü "ıbizaaiextension" öğesinden sonra virgül ekler ve "Disableipmaskeleme" ile aşağıya yeni bir satır ekler: true](media/ip-collection/save.png)

    > [!WARNING]
    > Şöyle bir hata yaşarsanız: **_kaynak grubu, şablondaki bir veya daha fazla kaynak tarafından desteklenmeyen bir konumda. Lütfen farklı bir kaynak grubu seçin._** Geçici olarak, açılan listeden farklı bir kaynak grubu seçin ve ardından hatayı çözümlemek için özgün kaynak grubunuzu yeniden seçin.

5. Satın **almayı kabul ediyorum**' u seçin  >  . 

    !["Satın al" sözcüğünün kırmızı renkle vurgulanmış bir düğmenin üzerinde kırmızı renkle vurgulanmış "kelimeyle belirtilen hüküm ve koşulları kabul ediyorum" sözcüklerini içeren onay kutusu işaretlendi.](media/ip-collection/purchase.png)

    Bu durumda, aslında yeni bir şey satın alınmamıştır. Yalnızca var olan Application Insights kaynağının yapılandırmasını güncelleştiriyoruz.

6. Dağıtım tamamlandıktan sonra yeni telemetri verileri kaydedilir.

    Şablonu yeniden seçer ve düzenlerseniz, yalnızca yeni eklenen özellik olmadan varsayılan şablonu görürsünüz. IP adresi verilerini görmüyorsanız ve ayarlandığını doğrulamak istiyorsanız `"DisableIpMasking": true` , aşağıdaki PowerShell 'i çalıştırın: 
    
    ```powershell
    # Replace `Fabrikam-dev` with the appropriate resource and resource group name.
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    Sonuç olarak bir özellik listesi döndürülecek. Özelliklerden biri okunmalıdır `DisableIpMasking: true` . Yeni özelliği Azure Resource Manager dağıtım öncesinde PowerShell 'i çalıştırırsanız, özellik yok.

### <a name="rest-api"></a>Rest API

Aynı değişiklikleri yapmak için [REST API](/rest/api/azure/) yükü aşağıdaki gibidir:

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>Telemetri başlatıcısı

Daha esnek bir alternatif gerekirse `DisableIpMasking` , IP adresini bir özel alana kopyalamak için [telemetri başlatıcısı](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) kullanabilirsiniz. 

# <a name="net"></a>[.NET](#tab/net)

### <a name="aspnet--aspnet-core"></a>ASP.NET/ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> Erişeerişemiyorsanız `ISupportProperties` , APPLICATION INSIGHTS SDK 'nın en son kararlı sürümünü çalıştırdığınızdan emin olun. `ISupportProperties` , yüksek kardinalite değerlerine yöneliktir, ancak `GlobalProperties` bölge adı, ortam adı vb. gibi düşük kardinalite değerleri için daha uygundur. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>ASP.NET için telemetri başlatıcısı 'nı etkinleştir

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>ASP.NET Core için telemetri başlatıcısı 'nı etkinleştir

Telemetri başlatıcısını ASP.NET olarak ASP.NET Core, ancak başlatıcıyı etkinleştirmek için aşağıdaki örneği başvuru için kullanabilirsiniz:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```
# <a name="nodejs"></a>[Node.js](#tab/nodejs)

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```
# <a name="client-side-javascript"></a>[İstemci tarafı JavaScript](#tab/javascript)

### <a name="client-side-javascript"></a>İstemci tarafı JavaScript

Sunucu tarafı SDK 'ların aksine, istemci tarafı JavaScript SDK 'Sı IP adresini hesaplamaz. Varsayılan olarak, istemci tarafı telemetri için IP adresi hesaplaması, Azure 'daki alma uç noktasında oluşur. 

IP adresini doğrudan istemci tarafında hesaplamak istiyorsanız, kendi özel mantığınızı eklemeniz ve sonucu kullanarak etiketi ayarlamanız gerekir `ai.location.ip` . `ai.location.ip`Ayarlandığında, IP adresi hesaplaması alma uç noktası tarafından gerçekleştirilmez ve BELIRTILEN IP adresi coğrafi konum araması için kullanılır. Bu senaryoda, IP adresi varsayılan olarak yine de sıfırlanacaktır. 

Özel mantığınızdan hesaplanan tüm IP adresini korumak için, içinde verdiğiniz IP adresi verilerini `ai.location.ip` ayrı bir özel alana kopyalayacak bir telemetri başlatıcısı kullanabilirsiniz. Ancak, sunucu tarafı SDK 'ların aksine, üçüncü taraf kitaplıklara veya kendi özel koleksiyon mantığınıza bağlı kalmadan, istemci tarafı SDK sizin için adresi hesaplamaz.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

İstemci tarafı verileri, alma uç noktasına iletilmeden önce bir ara sunucuya geçiş yaparken, IP adresi hesaplaması istemcinin değil, proxy 'nin IP adresini gösterebilir. 

---

### <a name="view-the-results-of-your-telemetry-initializer"></a>Telemetri başlatıcısının sonuçlarını görüntüleme

Sitenize yeni trafik gönderirseniz birkaç dakika bekleyin. Daha sonra, koleksiyonun çalıştığını onaylamak için bir sorgu çalıştırabilirsiniz:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Yeni toplanan IP adresleri `customDimensions_client-ip` sütununda görüntülenir. Varsayılan sütunda, her `client-ip` dört sekizli de sıfırlanacaktır. 

Localhost 'tan test ediliyorsa ve değeri `customDimensions_client-ip` ise `::1` , bu değer beklenen davranıştır. `::1` IPv6 'daki geri döngü adresini temsil eder. Bu `127.0.0.1` , IPv4 ile eşdeğerdir.

## <a name="next-steps"></a>Sonraki Adımlar

* Application Insights 'de [kişisel veri toplama](../logs/personal-data-mgmt.md) hakkında daha fazla bilgi edinin.

* Application Insights içindeki [IP adresi koleksiyonunun](https://apmtips.com/posts/2016-07-05-client-ip-address/) nasıl çalıştığı hakkında daha fazla bilgi edinin. (Bu makalede mühendislerimizden biri tarafından yazılan eski bir dış blog gönderisi. IP adresinin kaydedildiği geçerli varsayılan davranışın ön tarihlerini `0.0.0.0` alır, ancak yerleşik olarak yerleşik olarak daha fazla derinliğe gider `ClientIpHeaderTelemetryInitializer` .)
