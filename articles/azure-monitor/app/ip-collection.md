---
title: Azure Application Insights IP adresi koleksiyonu | Microsoft Docs
description: IP adreslerinin ve coğrafi konum 'un Azure Application Insights nasıl işlendiğini anlama
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/11/2019
ms.openlocfilehash: 356c8389ed486246ce55b5006e1e489ac7c3c1e3
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73884778"
---
# <a name="geolocation-and-ip-address-handling"></a>Coğrafi konum ve IP adresi işleme

Bu makalede, varsayılan davranışı değiştirme ile birlikte, coğrafi konum arama ve IP adresi işlemenin Application Insights nasıl oluştuğu açıklanmaktadır.

## <a name="default-behavior"></a>Varsayılan davranış

Varsayılan olarak IP adresleri geçici olarak toplanır, ancak Application Insights depolanmaz. Temel işlem aşağıdaki gibidir:

IP adresleri Telemetri verilerinin bir parçası olarak Application Insights gönderilir. Azure 'daki alma uç noktasına ulaştıktan sonra, IP adresi [Maxakılda GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/)kullanarak coğrafi konum araması gerçekleştirmek için kullanılır. Bu aramanın sonuçları, `client_StateOrProvince``client_CountryOrRegion``client_City`aşağıdaki alanları doldurmak için kullanılır. Bu noktada, IP adresi atılır ve `0.0.0.0` `client_IP` alanına yazılır.

* Tarayıcı telemetrisi: gönderenin IP adresini geçici olarak topladık. IP adresi, giriş uç noktası tarafından hesaplanır.
* Sunucu telemetrisi: Application Insights modülü, istemci IP adresini geçici olarak toplar. `X-Forwarded-For` ayarlandıysa toplanmaz.

Bu davranış, gereksiz kişisel veri toplamayı önlemeye yardımcı olmak için tasarımdır. Mümkün olduğunda, kişisel veri toplamayı önetmenizi öneririz. 

## <a name="overriding-default-behavior"></a>Varsayılan davranışı geçersiz kılma

Varsayılan davranış kişisel verileri toplamayı en aza indirirken, IP adresi verilerini toplama ve depolama esnekliği de sunuyoruz. IP adresleri gibi kişisel verileri depolamayı seçmeden önce, bunun herhangi bir uyumluluk gereksinimini veya tabi olduğunuz yerel düzenlemeleri bozmadığının doğrulanması önemle önerilir. Application Insights kişisel veri işleme hakkında daha fazla bilgi edinmek için [kişisel veriler kılavuzuna](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt)bakın.

## <a name="storing-ip-address-data"></a>IP adresi verileri depolanıyor

IP toplamayı ve depolamayı etkinleştirmek için, Application Insights bileşeninin `DisableIpMasking` özelliği `true`olarak ayarlanmalıdır. Bu özellik Azure Resource Manager şablonlar aracılığıyla ya da REST API çağırarak ayarlanabilir. 

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

Yalnızca tek bir Application Insights kaynağı için davranışı değiştirmeniz gerekiyorsa, bunu yapmanın en kolay yolu Azure portal aracılığıyla yapılır.  

1. Application Insights kaynak > **ayarları** > **şablonu dışarı aktarma** 

    ![Şablonu dışarı aktar](media/ip-collection/export-template.png)

2. **Dağıt** 'ı seçin

    ![Düğme, kırmızı renkle dağıt](media/ip-collection/deploy.png)

3. **Şablonu Düzenle**' yi seçin. (Şablonunuzda bu örnek şablonda görünmeyen ek özellikler veya kaynaklar varsa, tüm kaynakların şablon dağıtımını artımlı bir değişiklik/güncelleştirme olarak kabul etmesini sağlamak için dikkatli ilerleyin.)

    ![Şablon düzenleme](media/ip-collection/edit-template.png)

4. Kaynağınız için JSON 'da aşağıdaki değişiklikleri yapın ve **Kaydet**' e tıklayın:

    ![Ekran görüntüsü "ıbizaaiextension" öğesinden sonra virgül ekler ve "Disableipmaskeleme" ile aşağıya yeni bir satır ekler: true](media/ip-collection/save.png)

    > [!WARNING]
    > Şöyle bir hata yaşarsanız:  **_kaynak grubu, şablondaki bir veya daha fazla kaynak tarafından desteklenmeyen bir konumda. Lütfen farklı bir kaynak grubu seçin._** Geçici olarak, açılan listeden farklı bir kaynak grubu seçin ve ardından hatayı çözümlemek için özgün kaynak grubunuzu yeniden seçin.

5. **Satın alma** > **kabul** ediyorum ' u seçin. 

    ![Şablon düzenleme](media/ip-collection/purchase.png)

    Bu durumda yeni bir şey satın alınmazız, yalnızca mevcut Application Insights kaynağının yapılandırmasını güncelleştiriyoruz.

6. Dağıtım tamamlandıktan sonra yeni telemetri verileri kaydedilir.

    Şablonu yeniden seçip düzenleydiyseniz, yalnızca varsayılan şablonu görürsünüz ve yeni eklenen özelliği ve ilişkili değerini göremez. IP adresi verilerini görmüyorsanız ve `"DisableIpMasking": true` ayarlandığını doğrulamak istiyorsanız. Şu PowerShell 'i çalıştırın: (`Fabrikam-dev` uygun kaynak ve kaynak grubu adıyla değiştirin.)
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    Sonuç olarak bir özellik listesi döndürülecek. Özelliklerden biri `DisableIpMasking: true`okumalı. Yeni özelliği Azure Resource Manager dağıtmadan önce PowerShell 'i çalıştırırsanız, özellik mevcut olmayacaktır.

### <a name="rest-api"></a>REST API 'SI

Aynı değişiklikleri yapmak için [REST API](https://docs.microsoft.com/rest/api/azure/) yükü aşağıdaki gibidir:

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

IP adreslerinin tümünü veya bir kısmını kaydetmek için `DisableIpMasking` daha esnek bir alternatif gerekirse, IP 'nin tamamını veya bir kısmını özel bir alana kopyalamak için bir [telemetri başlatıcısı](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) kullanabilirsiniz. 

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
> `ISupportProperties`erişeerişemiyorsanız, Application Insights SDK 'nın en son kararlı sürümünü çalıştırdığınızdan emin olun. `ISupportProperties` yüksek kardinalite değerlerine yöneliktir, ancak `GlobalProperties` bölge adı, ortam adı vb. gibi düşük kardinalite değerleri için daha uygundur. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>Telemetri başlatıcısı 'nı etkinleştirin. ASP.NET

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

### <a name="client-side-javascript"></a>İstemci tarafı JavaScript

Sunucu tarafı SDK 'ların aksine, istemci tarafı JavaScript SDK 'Sı IP adresini hesaplamaz. Varsayılan olarak, istemci tarafı telemetri için IP adresi hesaplaması, telemetri gelişmesinden sonra Azure 'daki alma uç noktasında gerçekleştirilir. Bu, bir ara sunucuya istemci tarafı verileri gönderiyorsanız ve sonra giriş uç noktasına iletiyorsanız, IP adresi hesaplamasının istemci için değil, proxy 'nin IP adresini gösterebileceği anlamına gelir. Hiçbir proxy kullanılmıyorsa bunun bir sorun olmaması gerekir.

IP adresini doğrudan istemci tarafında hesaplamak isterseniz, bu hesaplamayı gerçekleştirmek için kendi özel mantığınızı eklemeniz ve sonucu kullanarak `ai.location.ip` etiketini ayarlamanız gerekir. `ai.location.ip` ayarlandığında, IP adresi hesaplaması alma uç noktası tarafından gerçekleştirilmez ve belirtilen IP adresi kabul edilir ve coğrafi aramayı gerçekleştirmek için kullanılır. Bu senaryoda, IP adresi varsayılan olarak yine de sıfırlanacaktır. 

Özel mantığınızdan hesaplanan tüm IP adresini sürdürmek için, `ai.location.ip` içinde verdiğiniz IP adresi verilerini ayrı bir özel alana kopyalayacak bir telemetri başlatıcısı kullanabilirsiniz. Ancak, sunucu tarafı SDK 'Lardan farklı olarak, 3. taraf kitaplıklara veya kendi özel istemci tarafı IP koleksiyonu mantığınıza bağlı kalmadan, istemci tarafı SDK sizin için IP 'yi hesaplamaz.    


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

### <a name="view-the-results-of-your-telemetry-initializer"></a>Telemetri başlatıcısının sonuçlarını görüntüleme

Daha sonra sitenize karşı yeni trafik tetikleyip yaklaşık 2-5 dakika beklediğinden, IP adresi koleksiyonunun çalışıp çalışmadığını görmek için bir kusto sorgusu çalıştırabilirsiniz:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Yeni toplanan IP adresleri `customDimensions_client-ip` sütununda görünmelidir. Varsayılan `client-ip` sütununda, her 4 sekizli ya da yalnızca, bileşen düzeyinde IP adresi toplamayı nasıl yapılandırdığınıza bağlı olarak ilk üç sekizli görüntülenir. Telemetri başlatıcısı uygulandıktan sonra yerel olarak test ediyorsanız ve `customDimensions_client-ip` için gördüğünüz değer `::1`, bu beklenen davranıştır. `::1` IPv6 'daki geri döngü adresini temsil eder. IPv4 'deki `127.0.01` eşdeğerdir ve localhost 'tan test edilirken göreceğiniz sonuçtur.

## <a name="next-steps"></a>Sonraki Adımlar

* Application Insights 'de [kişisel veri toplama](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt) hakkında daha fazla bilgi edinin.

* Application Insights içindeki [IP adresi koleksiyonunun](https://apmtips.com/blog/2016/07/05/client-ip-address/) nasıl çalıştığı hakkında daha fazla bilgi edinin. (Bu, mühendislerimizden biri tarafından yazılan eski bir dış blog gönderisine sahiptir. IP adresinin `0.0.0.0`olarak kaydedildiği geçerli varsayılan davranışı ön tarihlere ayırır, ancak yerleşik `ClientIpHeaderTelemetryInitializer`mekanizması üzerinde daha fazla derinliğe gider.)