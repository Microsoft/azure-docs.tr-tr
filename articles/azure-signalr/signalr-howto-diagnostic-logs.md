---
title: Azure SignalR hizmeti için kaynak günlükleri
description: Azure SignalR hizmeti için kaynak günlüklerini ayarlamayı ve bunu kendi kendine sorun gidermeye nasıl kullanacağınızı öğrenin.
author: wanlwanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 5650ff0e039d1e9211b8d0013726e101efdfab78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572253"
---
# <a name="resource-logs-for-azure-signalr-service"></a>Azure SignalR hizmeti için kaynak günlükleri

Bu öğreticide, Azure SignalR hizmeti için kaynak günlüklerinin, nasıl ayarlanacağı ve bunlarla ilgili sorunların nasıl giderileceği açıklanmaktadır. 

## <a name="prerequisites"></a>Önkoşullar
Kaynak günlüklerini etkinleştirmek için, günlük verilerinizi depolamak üzere bir yere ihtiyacınız vardır. Bu öğretici, Azure depolama ve Log Analytics kullanır.

* [Azure depolama](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) -ilke denetimi, statik analiz veya yedekleme için kaynak günlüklerini tutar.
* [Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) -bir Azure kaynağı tarafından oluşturulan ham günlüklerin analizine izin veren esnek bir günlük araması ve Analiz Aracı.

## <a name="set-up-resource-logs-for-an-azure-signalr-service"></a>Azure SignalR hizmeti için kaynak günlüklerini ayarlama

Azure SignalR hizmeti için kaynak günlüklerini görüntüleyebilirsiniz. Bu Günlükler, Azure SignalR hizmet örneğiniz için daha zengin bir bağlantı görünümü sağlar. Kaynak günlükleri her bağlantı hakkında ayrıntılı bilgi sağlar. Örneğin, temel bilgiler (Kullanıcı KIMLIĞI, bağlantı KIMLIĞI ve aktarım türü vb.) ve bağlantının olay bilgileri (Connect, bağlantıyı kes ve iptal olayı vb.). Kaynak günlükleri, sorun tanımlama, bağlantı izleme ve analiz için kullanılabilir.

### <a name="enable-resource-logs"></a>Kaynak günlüklerini etkinleştirme

Kaynak günlükleri varsayılan olarak devre dışıdır. Kaynak günlüklerini etkinleştirmek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com), **izleme** altında **Tanılama ayarları**' na tıklayın.

    ![Tanılama ayarlarına bölme gezintisi](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1. Ardından **Tanılama ayarı Ekle**' ye tıklayın.

    ![Kaynak günlükleri ekleme](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1. İstediğiniz arşiv hedefini ayarlayın. Şu anda, **bir depolama hesabına arşivleme** ve **Log Analytics gönderme** destekliyoruz.

1. Arşivlemek istediğiniz günlükleri seçin.

    ![Tanılama ayarları bölmesi](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1. Yeni tanılama ayarlarını kaydedin.

Yeni ayarlar yaklaşık 10 dakika içinde etkili olur. Bundan sonra, **tanılama günlükleri** bölmesinde Günlükler yapılandırılan arşiv hedefinde görüntülenir.

Tanılamayı yapılandırma hakkında daha fazla bilgi için bkz. [Azure Kaynak günlüklerine genel bakış](../azure-monitor/essentials/platform-logs-overview.md).

### <a name="resource-logs-categories"></a>Kaynak günlükleri kategorileri

Azure SignalR hizmeti, kaynak günlüklerini tek bir kategoride yakalar:

* **Tüm Günlükler**: Azure SignalR hizmetine bağlanan bağlantıları izleyin. Günlükler, Bağlan/Bağlantıyı kes, kimlik doğrulama ve daraltma hakkında bilgi sağlar. Daha fazla bilgi için sonraki bölüme bakın.

### <a name="archive-to-a-storage-account"></a>Bir depolama hesabına arşivle

Günlükler, **tanılama günlükleri** bölmesinde yapılandırılan depolama hesabında depolanır. Adlı kapsayıcı `insights-logs-alllogs` , kaynak günlüklerini depolamak için otomatik olarak oluşturulur. Kapsayıcının içinde Günlükler dosyada depolanır `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json` . Temel olarak, yol ve ile birleştirilir `resource ID` `Date Time` . Günlük dosyaları tarafından bölünür `hour` . Bu nedenle, dakikalar her zaman olur `m=00` .

Tüm Günlükler JavaScript Nesne Gösterimi (JSON) biçiminde depolanır. Her girdinin aşağıdaki bölümlerde açıklanan biçimi kullanan dize alanları vardır.

Arşiv günlüğü JSON dizeleri aşağıdaki tablolarda listelenen öğeleri içerir:

**Biçimlendir**

Ad | Açıklama
------- | -------
time | Olay saatini günlüğe kaydet
düzey | Olay düzeyini günlüğe kaydet
resourceId | Azure SignalR hizmetinizin kaynak KIMLIĞI
location | Azure SignalR hizmetinizin konumu
category | Günlük olayının kategorisi
operationName | Etkinliğin işlem adı
callerIpAddress | Sunucunuzun/istemcinizin IP adresi
properties | Bu günlük olayla ilgili ayrıntılı özellikler. Daha fazla ayrıntı için aşağıdaki özellikler tablosuna bakın

**Özellikler tablosu**

Ad | Açıklama
------- | -------
tür | Günlük olayının türü. Şu anda Azure SignalR hizmetine bağlantı hakkında bilgi sağlıyoruz. Yalnızca `ConnectivityLogs` tür kullanılabilir
 koleksiyonu | Günlük olayı koleksiyonu. İzin verilen değerler: `Connection` , `Authorization` ve `Throttling`
ConnectionID | Bağlantının kimliği
transportType | Bağlantının aktarım türü. İzin verilen değerler şunlardır: `Websockets` \| `ServerSentEvents` \|`LongPolling`
Belirtildi | Bağlantı türü. İzin verilen değerler: `Server` \| `Client` . `Server`: sunucu tarafında bağlantı; `Client`: istemci tarafından bağlantı
userId | Kullanıcının kimliği
message | Günlük olayının ayrıntılı iletisi

Aşağıdaki kod arşiv günlüğü JSON dizesinin bir örneğidir:

```json
{
    "properties": {
        "message": "Entered Serverless mode.",
        "type": "ConnectivityLogs",
        "collection": "Connection",
        "connectionId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "userId": "User",
        "transportType": "WebSockets",
        "connectionType": "Client"
    },
    "operationName": "ServerlessModeEntered",
    "category": "AllLogs",
    "level": "Informational",
    "callerIpAddress": "xxx.xxx.xxx.xxx",
    "time": "2019-01-01T00:00:00Z",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX",
    "location": "xxxx"
}
```

### <a name="archive-logs-schema-for-log-analytics"></a>Log Analytics için Arşiv günlüğü şeması

Kaynak günlüklerini görüntülemek için aşağıdaki adımları izleyin:

1. `Logs`Hedef Log Analytics tıklayın.

    ![Log Analytics menü öğesi](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. `SignalRServiceDiagnosticLogs`Kaynak günlüklerini sorgulamak için zaman aralığını girin ve seçin. Gelişmiş sorgu için bkz. [Azure izleyici 'de Log Analytics kullanmaya başlama](../azure-monitor/logs/log-analytics-tutorial.md)

    ![Log Analytics sorgu günlüğü](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

Arşiv günlüğü sütunları aşağıdaki tabloda listelenen öğeleri içerir:

Ad | Açıklama
------- | ------- 
TimeGenerated | Olay saatini günlüğe kaydet
Koleksiyon | Günlük olayı koleksiyonu. İzin verilen değerler: `Connection` , `Authorization` ve `Throttling`
OperationName | Etkinliğin işlem adı
Konum | Azure SignalR hizmetinizin konumu
Level | Olay düzeyini günlüğe kaydet
Callerıpaddress | Sunucunuzun/istemcinizin IP adresi
İleti | Günlük olayının ayrıntılı iletisi
UserId | Kullanıcının kimliği
ConnectionID | Bağlantının kimliği
Belirtildi | Bağlantı türü. İzin verilen değerler: `Server` \| `Client` . `Server`: sunucu tarafında bağlantı; `Client`: istemci tarafından bağlantı
TransportType | Bağlantının aktarım türü. İzin verilen değerler şunlardır: `Websockets` \| `ServerSentEvents` \|`LongPolling`

### <a name="troubleshooting-with-resource-logs"></a>Kaynak günlükleriyle ilgili sorunları giderme

Azure SignalR hizmeti sorunlarını gidermek için, hataları yakalamak üzere sunucu/istemci tarafı günlüklerini etkinleştirebilirsiniz. Azure SignalR hizmeti, mevcut olduğunda, hizmet tarafı için günlükleri de etkinleştirebilirsiniz.

Bağlantının beklenmedik şekilde büyümesi veya düşürülme durumunda, sorun gidermek için kaynak günlüklerinden yararlanabilirsiniz.

Tipik sorunlar genellikle bağlantıların beklenmeyen miktar değişiklikleri, bağlantı bağlantısı sınırlarına ve yetkilendirme hatasına ulaşabiliyor. Sorun giderme hakkında sonraki bölümlere bakın.

#### <a name="unexpected-connection-number-changes"></a>Beklenmeyen bağlantı numarası değişiklikleri

##### <a name="unexpected-connection-dropping"></a>Beklenmeyen bağlantı bırakma

Beklenmedik bağlantı bırakmaya karşılaşıyorsanız, hizmet, sunucu ve istemci taraflarındaki günlükleri ilk olarak etkinleştirin.

Bir bağlantının bağlantısı kesilirse, kaynak günlükleri bu bağlantı kesme olayını kaydeder, burada `ConnectionAborted` veya görürsünüz `ConnectionEnded` `operationName` .

Ve arasındaki fark `ConnectionAborted` , `ConnectionEnded` `ConnectionEnded` istemci veya sunucu tarafı tarafından tetiklenen beklenen bir bağlantı kesilmesi. `ConnectionAborted`Genellikle beklenmeyen bir bağlantı bırakma olayı olsa da, ' de iptal nedeni sağlanacaktır `message` .

Durdurma nedenleri aşağıdaki tabloda listelenmiştir:

Nedeni | Description
------- | ------- 
Bağlantı sayısı sınıra ulaşır | Bağlantı sayısı geçerli fiyat katmanınızın sınırına ulaşır. Hizmet birimi ölçeğini azaltmayı göz önünde bulundurun
Uygulama sunucusu bağlantıyı kapattı | App Server abortion tetikler. Beklenen bir abortion olarak kabul edilebilir
Bağlantı ping zaman aşımı | Genellikle ağ sorunu nedeniyle oluşur. Uygulama sunucunuzun kullanılabilirliğini Internet 'ten denetlemeyi düşünün
Hizmeti yeniden yükleme, yeniden bağlanma | Azure SignalR hizmeti yeniden yükleniyor. Azure SignalR otomatik yeniden bağlanmayı destekler, yeniden bağlanan veya Azure SignalR hizmetine el ile yeniden bağlanana kadar bekleyebilirsiniz
İç sunucu geçici hatası | Azure SignalR hizmetinde geçici hata oluşur, otomatik olarak kurtarılması gerekir
Sunucu bağlantısı bırakıldı | Sunucu bağlantısı bilinmeyen bir hatayla düşerse, önce hizmet/sunucu/istemci tarafı günlüğü ile kendi kendine sorun gidermeyi göz önünde bulundurun. Temel sorunları dışarıda bırakmayı deneyin (örneğin, ağ sorunu, uygulama sunucusu tarafı sorunu vb.). Sorun çözümlenmemişse, daha fazla yardım için bizimle iletişime geçin. Daha fazla bilgi için [yardım alın](#get-help) bölümüne bakın. 

##### <a name="unexpected-connection-growing"></a>Beklenmeyen bağlantı artıyor

Büyüyen beklenmeyen bağlantıyla ilgili sorunları gidermek için yapmanız gereken ilk şey, fazladan bağlantıları filtrelemeniz. Test istemci bağlantınıza benzersiz test Kullanıcı KIMLIĞI ekleyebilirsiniz. Daha sonra kaynak günlükleriyle birlikte doğruladıktan sonra, birden fazla istemci bağlantısının aynı test Kullanıcı KIMLIĞINE veya IP 'ye sahip olduğunu görürseniz, bu durum büyük olasılıkla istemci tarafında beklentiden daha fazla bağlantı oluşturabilir ve bu bağlantıyı kurabilir. İstemci tarafını denetleyin.

#### <a name="authorization-failure"></a>Yetkilendirme hatası

İstemci istekleri için 401 Yetkisiz döndürülürse, kaynak günlüklerinizi denetleyin. Karşılaşırsanız `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>` , erişim belirteçinizdeki tüm seyircilerin geçersiz olduğu anlamına gelir. Günlükte önerilen geçerli izleyicileri kullanmayı deneyin.


#### <a name="throttling"></a>Azaltma

Azure SignalR hizmeti ile SignalR istemci bağlantıları kuramadık, kaynak günlüklerinizi kontrol edin. `Connection count reaches limit`Kaynak günlüğünde karşılaşırsanız, SignalR hizmetine bağlantı sayısı sınırına ulaşan çok fazla bağlantı kurarsınız. SignalR hizmetinizi ölçeklendirin. `Message count reaches limit`Kaynak günlüğünde karşılaşırsanız, ücretsiz katmanı kullandığınızda ve ileti kotasını kullandığınız anlamına gelir. Daha fazla ileti göndermek istiyorsanız, SignalR hizmetinizi Standart katmana değiştirerek ek iletiler gönderilmesini düşünün. Daha fazla bilgi için bkz. [Azure SignalR hizmeti fiyatlandırması](https://azure.microsoft.com/pricing/details/signalr-service/).

### <a name="get-help"></a>Yardım alın

İlk olarak sorun gidermeyi öneririz. Çoğu soruna uygulama sunucusu veya ağ sorunları neden olmuştur. Ana nedeni bulmak için kaynak günlüğü ve [temel sorun giderme kılavuzu](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md) [ile sorun giderme kılavuzunu](#troubleshooting-with-resource-logs) izleyin.
Sorun hala çözülemezse GitHub 'da bir sorun açmayı veya Azure portalında bilet oluşturmayı düşünün.
Girmelisiniz
1. Sorun oluştuğunda yaklaşık 30 dakika boyunca zaman aralığı
2. Azure SignalR hizmetinin kaynak KIMLIĞI
3. Sorun ayrıntıları, mümkün olduğunca özel: Örneğin, appserver ileti göndermez, istemci bağlantısı düşmez ve bu şekilde devam eder
4. Sunucu/istemci tarafında toplanan Günlükler ve yararlı olabilecek diğer malzemeler
5. Seçim Kodu yeniden üretme

> [!NOTE]
> Sorun GitHub 'da açarsanız, hassas bilgilerinizi (örneğin, kaynak KIMLIĞI, sunucu/istemci günlükleri) özel olarak saklayın, yalnızca Microsoft kuruluşundaki üyelere özel olarak gönderin.