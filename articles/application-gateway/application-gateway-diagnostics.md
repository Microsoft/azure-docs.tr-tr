---
title: Arka uç sistem durumu ve tanılama günlükleri
titleSuffix: Azure Application Gateway
description: Azure Application Gateway erişim günlüklerini ve performans günlüklerini etkinleştirme ve yönetme hakkında bilgi edinin
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 448e5bf798f5b1c3006888f846722e54fec46ef8
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075308"
---
# <a name="back-end-health-and-diagnostic-logs-for-application-gateway"></a>Application Gateway için arka uç sistem durumu ve tanılama günlükleri

Azure Application Gateway kaynaklarını aşağıdaki yollarla izleyebilirsiniz:

* [Arka uç sistem durumu](#back-end-health): Application Gateway, arka uç havuzlarındaki sunucuların sistem durumunu Azure Portal ve PowerShell aracılığıyla izleme yeteneği sağlar. Ayrıca, arka uç havuzlarının sistem durumunu performans tanılama günlükleri aracılığıyla bulabilirsiniz.

* [Günlükler](#diagnostic-logging): Günlükler performans, erişim ve diğer verilerin izleme amacıyla bir kaynaktan kaydedilmesini veya tüketilmesi için izin verir.

* [Ölçümler](application-gateway-metrics.md): Application Gateway, sisteminizin beklendiği gibi çalıştığını doğrulamanıza yardımcı olan birkaç ölçüm içerir.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="back-end-health"></a>Arka uç sistem durumu

Application Gateway, Portal, PowerShell ve komut satırı arabirimi (CLı) aracılığıyla arka uç havuzlarının bireysel üyelerinin sistem durumunu izleme yeteneği sağlar. Ayrıca, arka uç havuzlarının toplu bir sistem durumu özetini performans tanılama günlükleri aracılığıyla bulabilirsiniz. 

Arka uç sistem durumu raporu, Application Gateway sistem durumu araştırmasının çıkışını arka uç örneklerine yansıtır. Yoklama başarılı olduğunda ve arka uç trafik alabileceği zaman sağlıklı olarak kabul edilir. Aksi takdirde, sağlıksız olarak kabul edilir.

> [!IMPORTANT]
> Bir Application Gateway alt ağında ağ güvenlik grubu (NSG) varsa, v1 SKU 'Ları için 65503-65534 bağlantı noktası aralıklarını ve gelen trafik için Application Gateway alt ağında v2 SKU 'Ları için 65200-65535 ' u açın. Bu bağlantı noktası aralığı, Azure altyapı iletişimi için gereklidir. Bunlar Azure sertifikaları tarafından korunur (kilitlenir). Uygun sertifikalar olmadan, bu ağ geçitlerinin müşterileri de dahil olmak üzere dış varlıklar bu uç noktalar üzerinde herhangi bir değişiklik başlatamaz.


### <a name="view-back-end-health-through-the-portal"></a>Portal aracılığıyla arka uç durumunu görüntüleme

Portalda, arka uç sistem durumu otomatik olarak sağlanır. Mevcut bir uygulama ağ geçidinde **izleme** > **arka uç durumu**' nu seçin. 

Arka uç havuzundaki her üye bu sayfada listelenir (NIC, IP veya FQDN olup olmadığı). Arka uç havuzu adı, bağlantı noktası, arka uç HTTP ayarları adı ve sistem durumu görüntülenir. Sağlık durumu için geçerli değerler **sağlıklı**, **sağlıksız**ve **bilinmiyor**durumlardır.

> [!NOTE]
> **Bilinmeyen**bir arka uç sistem durumu görürseniz, arka uca erişimin bir NSG kuralı, Kullanıcı tanımlı yol (UDR) veya sanal ağda özel bir DNS tarafından engellenmediğinden emin olun.

![Arka uç sistem durumu][10]

### <a name="view-back-end-health-through-powershell"></a>PowerShell aracılığıyla arka uç durumunu görüntüleme

Aşağıdaki PowerShell kodu, `Get-AzApplicationGatewayBackendHealth` cmdlet 'ini kullanarak arka uç durumunun nasıl görüntüleneceğini göstermektedir:

```powershell
Get-AzApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>Azure CLı aracılığıyla arka uç durumunu görüntüleme

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>Sonuçlar

Aşağıdaki kod parçacığında yanıt örneği gösterilmektedir:

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logging"></a>Tanılama günlükleri

Uygulama ağ geçitlerini yönetmek ve sorunlarını gidermek için Azure 'da farklı türlerde Günlükler kullanabilirsiniz. Bu günlüklerden bazılarına portaldan erişebilirsiniz. Tüm Günlükler Azure Blob depolama alanından ayıklanabilir ve [Azure izleyici günlükleri](../azure-monitor/insights/azure-networking-analytics.md), Excel ve Power BI gibi farklı araçlarla görüntülenebilir. Aşağıdaki listeden farklı türlerdeki Günlükler hakkında daha fazla bilgi edinebilirsiniz:

* **Etkinlik günlüğü**: Azure aboneliğinize gönderilen tüm işlemleri ve bunların durumunu görüntülemek için [Azure etkinlik günlüklerini](../monitoring-and-diagnostics/insights-debugging-with-events.md) (eski adıyla işlemsel Günlükler ve denetim günlükleri olarak bilinir) kullanabilirsiniz. Etkinlik günlüğü girişleri varsayılan olarak toplanır ve bunları Azure portalda görüntüleyebilirsiniz.
* **Erişim günlüğü**: Bu günlüğü, Application Gateway erişim düzenlerini görüntülemek ve önemli bilgileri çözümlemek için kullanabilirsiniz. Bu, arayanın IP, istenen URL, yanıt gecikmesi, dönüş kodu ve gelen ve giden baytları içerir. Bir erişim günlüğü her 300 saniyede toplanır. Bu günlük Application Gateway örneği başına bir kayıt içerir. Application Gateway örneği InstanceId özelliği tarafından tanımlanır.
* **Performans günlüğü**: Bu günlüğü, Application Gateway örneklerinin nasıl çalıştığını görüntülemek için kullanabilirsiniz. Bu günlük, her bir örnek için performans bilgilerini, sunulan toplam istek sayısı, bayt cinsinden aktarım hızı, sunulan toplam istek sayısı, başarısız istek sayısı, sağlıklı ve sağlıksız arka uç örnek sayısı dahil olmak üzere yakalar. Performans günlüğü her 60 saniyede toplanır. Performans günlüğü yalnızca v1 SKU 'SU için kullanılabilir. V2 SKU 'SU için performans verileri [ölçümlerini](application-gateway-metrics.md) kullanın.
* **Güvenlik duvarı günlüğü**: Web uygulaması güvenlik duvarıyla yapılandırılmış bir uygulama ağ geçidinin algılama veya önleme modu aracılığıyla günlüğe kaydedilen istekleri görüntülemek için bu günlüğü kullanabilirsiniz.

> [!NOTE]
> Günlükler yalnızca Azure Resource Manager dağıtım modelinde dağıtılan kaynaklar için kullanılabilir. Klasik dağıtım modelinde kaynaklar için günlük kullanamazsınız. İki modeli daha iyi anlamak için bkz. [Kaynak Yöneticisi dağıtımı ve klasik dağıtımı anlama](../azure-resource-manager/resource-manager-deployment-model.md) makalesi.

Günlüklerinizi depolamak için kullanabileceğiniz üç seçenek vardır:

* **Depolama hesabı**: Depolama hesaplarının en iyi kullanım amacı, günlüklerin uzun süre depolanması ve ihtiyaç duyulduğunda gözden geçirilmesi durumlarıdır.
* **Olay**hub 'ları: Olay Hub 'ları, kaynaklarınızda uyarı almak için diğer güvenlik bilgileri ve olay yönetimi (SIEM) araçlarıyla tümleştirme için harika bir seçenektir.
* **Azure izleyici günlükleri**: Azure izleyici günlükleri, uygulamanızın genel gerçek zamanlı izleme için en iyi şekilde kullanılır veya eğilimleri göz yormaktadır.

### <a name="enable-logging-through-powershell"></a>PowerShell aracılığıyla günlüğü etkinleştir

Etkinlik günlüğü tüm Kaynak Yöneticisi kaynakları için otomatik olarak etkinleştirilir. Bu Günlükler aracılığıyla kullanılabilir verileri toplamaya başlamak için erişim ve performans günlüğünü etkinleştirmeniz gerekir. Günlüğe kaydetmeyi etkinleştirmek için aşağıdaki adımları kullanın:

1. Günlük verilerinin depolandığı depolama hesabınızın kaynak kimliğini not edin. Bu değer şu biçimdedir:/Subscriptions/\<SubscriptionID\>/resourceGroups/\<kaynak grubu adı\>/providers/Microsoft.Storage/storageAccounts/\<depolama hesabı adı\>. Aboneliğinizdeki herhangi bir depolama hesabını kullanabilirsiniz. Bu bilgileri Azure portalda bulabilirsiniz.

    ![Portal: depolama hesabı için kaynak KIMLIĞI](./media/application-gateway-diagnostics/diagnostics1.png)

2. Uygulama ağ geçidinizin etkin olan kaynak KIMLIĞI ' ne göz atalım. Bu değer şu biçimdedir:/Subscriptions/\<SubscriptionID\>/resourceGroups/\<kaynak grubu adı\>/providers/Microsoft.Network/applicationGateways/\<Application Gateway Name\>. Bu bilgileri portalda bulabilirsiniz.

    ![Portal: uygulama ağ geçidi için kaynak KIMLIĞI](./media/application-gateway-diagnostics/diagnostics2.png)

3. Tanılama günlüğüne kaydetmeyi etkinleştirmek için aşağıdaki PowerShell cmdlet'ini kullanın:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>Etkinlik günlükleri ayrı bir depolama hesabı gerektirmez. Depolama alanının erişim ve performans günlüğü kaydı için kullanılması durumunda hizmet ücreti tahsil edilir.

### <a name="enable-logging-through-the-azure-portal"></a>Azure portaldan günlüğe kaydetmeyi etkinleştirme

1. Azure portal, kaynağınız bulun ve **Tanılama ayarları**' nı seçin.

   Application Gateway için üç günlük mevcuttur:

   * Erişim günlüğü
   * Performans günlüğü
   * Güvenlik duvarı günlüğü

2. Veri toplamaya başlamak için **tanılamayı aç**' ı seçin.

   ![Tanılamayı açma][1]

3. **Tanılama ayarları** sayfasında tanılama günlükleriyle ilgili ayarlar bulunur. Bu örnekte, günlükleri Log Analytics depolar. Tanılama günlüklerini kaydetmek için Event Hubs'ı veya depolama hesabını da kullanabilirsiniz.

   ![Yapılandırma işlemi başlatılıyor][2]

5. Ayarlar için bir ad yazın, ayarları onaylayın ve **Kaydet**' i seçin.

### <a name="activity-log"></a>Etkinlik günlüğü

Azure, etkinlik günlüğünü varsayılan olarak oluşturur. Günlükler, Azure olay günlüğü deposunda 90 gün boyunca korunur. [Olayları ve etkinlik günlüğünü görüntüle](../monitoring-and-diagnostics/insights-debugging-with-events.md) makalesini okuyarak bu Günlükler hakkında daha fazla bilgi edinin.

### <a name="access-log"></a>Erişim günlüğü

Erişim günlüğü, yalnızca, önceki adımlarda açıklandığı şekilde, her bir Application Gateway örneği üzerinde etkinleştirdiyseniz oluşturulur. Veriler, günlüğü etkinleştirdiğinizde belirttiğiniz depolama hesabında depolanır. Her Application Gateway erişimi, v1 için aşağıdaki örnekte gösterildiği gibi JSON biçiminde günlüğe kaydedilir:

|Değer  |Açıklama  |
|---------|---------|
|instanceId     | İsteği sunan Application Gateway örneği.        |
|ClientIP     | İstek için kaynak IP 'si.        |
|Istemci bağlantı noktası     | İstek için kaynak bağlantı noktası.       |
|httpMethod     | İstek tarafından kullanılan HTTP yöntemi.       |
|requestUri     | Alınan isteğin URI 'SI.        |
|RequestQuery     | **Sunucu-yönlendirildi**: isteği gönderen arka uç havuzu örneği.</br>**X-AzureApplicationGateway-log-ID**: istek için kullanılan bağıntı kimliği. Arka uç sunucularındaki trafik sorunlarını gidermek için kullanılabilir. </br>**Sunucu-durumu**: arka uçtan alınan Application Gateway http yanıt kodu.       |
|Kullanıcı     | HTTP istek üst bilgisinden Kullanıcı Aracısı.        |
|Http durumu     | Application Gateway istemcisinden istemciye döndürülen HTTP durum kodu.       |
|httpVersion     | İsteğin HTTP sürümü.        |
|Alınma baytları     | Bayt cinsinden alınan paket boyutu.        |
|sentBytes| Bayt cinsinden gönderilen paket boyutu.|
|timeTaken| Bir isteğin işlenmesi için gereken süre (milisaniye cinsinden) ve yanıtının gönderilmesi için gereken süre (milisaniye cinsinden). Bu, yanıt gönderme işleminin bittiği zaman Application Gateway bir HTTP isteğinin ilk baytını aldığında zaman aralığı olarak hesaplanır. Zaman alan alanın genellikle istek ve Yanıt paketlerinin ağ üzerinden seyahat süresini içerdiğine dikkat edin. |
|sslEnabled| Arka uç havuzlarıyla iletişimin SSL kullanıp kullanmadığını belirtir. Geçerli değerler açık ve kapalı.|
|host| İsteğin arka uç sunucusuna gönderildiği ana bilgisayar adı. Arka uç ana bilgisayar adı geçersiz kılınırsa, bu ad bu adı yansıtır.|
|originalHost| İstemciden Application Gateway tarafından isteğin alındığı ana bilgisayar adı.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
Application Gateway ve WAF v2 için Günlükler biraz daha fazla bilgi gösterir:

|Değer  |Açıklama  |
|---------|---------|
|instanceId     | İsteği sunan Application Gateway örneği.        |
|ClientIP     | İstek için kaynak IP 'si.        |
|Istemci bağlantı noktası     | İstek için kaynak bağlantı noktası.       |
|httpMethod     | İstek tarafından kullanılan HTTP yöntemi.       |
|requestUri     | Alınan isteğin URI 'SI.        |
|Kullanıcı     | HTTP istek üst bilgisinden Kullanıcı Aracısı.        |
|Http durumu     | Application Gateway istemcisinden istemciye döndürülen HTTP durum kodu.       |
|httpVersion     | İsteğin HTTP sürümü.        |
|Alınma baytları     | Bayt cinsinden alınan paket boyutu.        |
|sentBytes| Bayt cinsinden gönderilen paket boyutu.|
|timeTaken| Bir isteğin işlenmesi için geçen sürenin süresi ( **saniye**cinsinden) ve Yanıt gönderilmesi. Bu, yanıt gönderme işleminin bittiği zaman Application Gateway bir HTTP isteğinin ilk baytını aldığında zaman aralığı olarak hesaplanır. Zaman alan alanın genellikle istek ve Yanıt paketlerinin ağ üzerinden seyahat süresini içerdiğine dikkat edin. |
|sslEnabled| Arka uç havuzlarıyla iletişimin SSL kullanıp kullanmadığını belirtir. Geçerli değerler açık ve kapalı.|
|sslCipher| SSL iletişimi için kullanılan şifre paketi (SSL etkinse).|
|sslProtocol| Kullanılan SSL/TLS Protokolü (SSL etkinse).|
|Sunucu yönlendirmeli| Application Gateway 'in isteği yönlendiren arka uç sunucusu.|
|serverStatus| Arka uç sunucusunun HTTP durum kodu.|
|serverResponseLatency| Arka uç sunucusundan gelen yanıtın gecikmesi.|
|host| İsteğin ana bilgisayar üstbilgisinde listelenen adres.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_1",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "www.contoso.com",
    }
}
```

### <a name="performance-log"></a>Performans günlüğü

Performans günlüğü, yalnızca, önceki adımlarda açıklandığı gibi her bir Application Gateway örneği üzerinde etkinleştirdiyseniz oluşturulur. Veriler, günlüğü etkinleştirdiğinizde belirttiğiniz depolama hesabında depolanır. Performans günlüğü verileri 1 dakikalık aralıklarla oluşturulur. Yalnızca v1 SKU 'SU için kullanılabilir. V2 SKU 'SU için performans verileri [ölçümlerini](application-gateway-metrics.md) kullanın. Aşağıdaki veriler günlüğe kaydedilir:


|Değer  |Açıklama  |
|---------|---------|
|instanceId     |  Performans verilerinin oluşturulduğu örnek Application Gateway. Birden çok örnekli bir uygulama ağ geçidi için örnek başına bir satır vardır.        |
|Healthyıhostcount     | Arka uç havuzundaki sağlıklı ana bilgisayar sayısı.        |
|Unhealthyıhostcount     | Arka uç havuzundaki sağlıksız ana bilgisayar sayısı.        |
|Istek sayısı     | Hizmet verilen istek sayısı.        |
|dönemlerinde | Örneğinden istekleri sunan arka uca yapılan isteklerin ortalama gecikme süresi (milisaniye cinsinden). |
|failedRequestCount| Başarısız istek sayısı.|
|throughput| Son günlüğün bu yana saniye başına bayt cinsinden ölçülen ortalama aktarım hızı.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> Gecikme süresi, http isteğinin ilk baytını HTTP yanıtının son baytındaki zamana alındığında hesaplanır. Bu, Application Gateway işleme süresinin toplamı ile arka uçta ağ maliyetinin yanı sıra, isteği işlemek için arka ucun ne kadar sürer.

### <a name="firewall-log"></a>Güvenlik duvarı günlüğü

Güvenlik duvarı günlüğü, önceki adımlarda açıklandığı şekilde, yalnızca her bir uygulama ağ geçidi için etkinleştirdiyseniz oluşturulur. Bu günlük Ayrıca, Web uygulaması güvenlik duvarının bir uygulama ağ geçidinde yapılandırılmasını gerektirir. Veriler, günlüğü etkinleştirdiğinizde belirttiğiniz depolama hesabında depolanır. Aşağıdaki veriler günlüğe kaydedilir:


|Değer  |Açıklama  |
|---------|---------|
|instanceId     | Güvenlik Duvarı verilerinin oluşturulduğu örnek Application Gateway. Birden çok örnekli bir uygulama ağ geçidi için örnek başına bir satır vardır.         |
|ClientIP     |   İstek için kaynak IP 'si.      |
|Istemci bağlantı noktası     |  İstek için kaynak bağlantı noktası.       |
|requestUri     | Alınan isteğin URL 'SI.       |
|ruleSetType     | Kural kümesi türü. Kullanılabilir değer OWASP ' dir.        |
|ruleSetVersion     | Kural kümesi sürümü kullanıldı. Kullanılabilir değerler 2.2.9 ve 3,0 ' dir.     |
|RuleId     | Tetikleme olayının kural KIMLIĞI.        |
|message     | Tetikleme olayı için Kullanıcı dostu ileti. Ayrıntılar bölümünde daha fazla ayrıntı sağlanır.        |
|action     |  İstek üzerinde gerçekleştirilen eylem. Kullanılabilir değerler engellendi ve Izin verilir.      |
|bölgesi     | Günlüğün oluşturulduğu site. Şu anda, kurallar genel olduğundan yalnızca küresel olarak listelenir.|
|details     | Tetikleme olayının ayrıntıları.        |
|Ayrıntılar. ileti     | Kuralın açıklaması.        |
|details. Data     | Kuralla eşleşen istekte belirli veriler bulundu.         |
|Ayrıntılar. dosya     | Kuralın bulunduğu yapılandırma dosyası.        |
|details. Line     | Olayı tetikleyen yapılandırma dosyasındaki satır numarası.       |
|Konak   | Application Gateway ana bilgisayar adı veya IP adresi.    |
|transactionId  | Aynı istek içinde oluşan birden çok kural ihlallerinin gruplandırbir şekilde yapılmasına yardımcı olan belirli bir işlemin benzersiz KIMLIĞI.   |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
    "hostname": "40.90.218.100", 
    "transactionId": "AYAcUqAcAcAcAcAcASAcAcAc"
  }
} 

```

### <a name="view-and-analyze-the-activity-log"></a>Etkinlik günlüğünü görüntüleme ve analiz etme

Aşağıdaki yöntemlerden birini kullanarak etkinlik günlüğü verilerini görüntüleyebilir ve analiz edebilirsiniz:

* **Azure araçları**: Etkinlik günlüğü verilerini Azure PowerShell, Azure CLI, Azure REST API veya Azure portal üzerinden alabilirsiniz. Her yöntemle ilgili ayrıntılı adımlar [Kaynak Yöneticisi etkinlik işlemleri](../azure-resource-manager/resource-group-audit.md) makalesinde ayrıntılı bir şekilde anlatılmıştır.
* **Power BI**: [Power BI](https://powerbi.microsoft.com/pricing) hesabınız yoksa ücretsiz oluşturabilirsiniz. [Power BI şablonu uygulamalarını](https://docs.microsoft.com/power-bi/service-template-apps-overview)kullanarak verilerinizi çözümleyebilirsiniz.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Erişim, performans ve güvenlik duvarı günlüklerini görüntüleme ve çözümleme

[Azure izleyici günlükleri](../azure-monitor/insights/azure-networking-analytics.md) , BLOB depolama hesabınızdan sayaç ve olay günlüğü dosyalarını toplayabilir. Günlüklerinizi analiz etmek için görselleştirmelere ve güçlü arama özelliklerine sahiptir.

Dilerseniz depolama hesabınıza bağlanabilir ve JSON erişim günlüklerini ve performans günlüklerini alabilirsiniz. İndirdiğiniz JSON dosyalarını CSV biçimine dönüştürebilir ve Excel, Power BI veya diğer veri görselleştirme araçlarında görüntüleyebilirsiniz.

> [!TIP]
> Visual Studio ve C# ile sabit ve değişken değerlerini değiştirme konusunda temel kavramlara hakimseniz GitHub'daki [günlük dönüştürücü araçlarını](https://github.com/Azure-Samples/networking-dotnet-log-converter) kullanabilirsiniz.
> 
> 

#### <a name="analyzing-access-logs-through-goaccess"></a>Erişim günlüklerini GoAccess aracılığıyla çözümleme

Application Gateway erişim günlükleri için popüler [Goaccess](https://goaccess.io/) Log Analyzer 'ı yükleyen ve çalıştıran bir kaynak yöneticisi şablonu yayımladık. GoAccess benzersiz ziyaretçiler, Istenen dosyalar, konaklar, Işletim sistemleri, tarayıcılar, HTTP durum kodları ve daha fazlası gibi değerli HTTP trafiği istatistiklerini sağlar. Daha fazla ayrıntı için lütfen [GitHub 'daki Kaynak Yöneticisi Şablon klasöründeki Benioku dosyasına](https://aka.ms/appgwgoaccessreadme)bakın.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure izleyici günlüklerini](../azure-monitor/insights/azure-networking-analytics.md)kullanarak sayaç ve olay günlüklerini görselleştirin.
* [Azure etkinlik günlüğünüzü Power BI blog gönderisi Ile görselleştirin](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
* [Power BI ve daha fazla blog gönderisine Azure etkinlik günlüklerini görüntüleyin ve çözümleyin](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) .

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
