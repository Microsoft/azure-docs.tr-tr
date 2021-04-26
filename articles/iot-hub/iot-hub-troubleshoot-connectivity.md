---
title: Azure IoT Hub ile kesilen bağlantıları izleme ve sorun giderme
description: Azure IoT Hub cihaz bağlantısıyla sık karşılaşılan hataları izlemeyi ve sorun gidermeyi öğrenin
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
- fasttrack-edit
- iot
ms.openlocfilehash: 19094b6d2c10a77e5e99b698f2e7f5170677110b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061392"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Azure IoT Hub bağlantısını izleme, tanılama ve sorun giderme

Birçok olası hata noktası olduğundan, IoT cihazlarına yönelik bağlantı sorunlarının giderilmesi zor olabilir. Uygulama mantığı, fiziksel ağlar, protokoller, donanım, IoT Hub ve diğer bulut Hizmetleri, sorun oluşmasına neden olabilir. Bir sorunun kaynağını tespit etme ve oluşturma özelliği kritik öneme sahiptir. Ancak, ölçekteki bir IoT çözümünde binlerce cihaz olabilir, bu nedenle tek tek cihazları el ile denetlemek pratik değildir. IoT Hub, size yardımcı olmak için iki Azure hizmeti ile tümleşir:

* **Azure izleyici** Bu sorunları ölçeklendirmek, tanılamak ve sorunlarını gidermenize yardımcı olmak için Azure Izleyici aracılığıyla IoT Hub izleme yeteneklerini kullanın. Bu, kesilen bağlantı kesildiğinde bildirimleri ve eylemleri tetiklemek için uyarıları ayarlamayı ve bağlantı kesilmesinin neden olduğu koşulları saptamak için kullanabileceğiniz günlükleri yapılandırmayı içerir.

* **Azure Event Grid** Kritik altyapı ve cihaz başına bağlantı kesildiğinde, cihaz Connect 'e abone olmak ve IoT Hub tarafından yayılan olayların bağlantısını kesmek için Azure Event Grid kullanın.

Her iki durumda da, bu özellikler IoT Hub neler gözlemleyebileceği için sınırlı olduğundan, cihazlarınız ve diğer Azure hizmetleri için en iyi uygulamaları izlemeyi izlemeniz önerilir.

## <a name="event-grid-vs-azure-monitor"></a>Event Grid ve Azure Izleyici karşılaştırması

Event Grid, kritik cihazlar ve altyapıya yönelik cihaz bağlantılarını izlemek için kullanabileceğiniz düşük gecikmeli, cihaz başına izleme çözümü sağlar. Azure Izleyici, IoT Hub bağlı cihazların sayısını izlemek ve bu sayı statik eşiğin altına düştüğünde bir uyarı tetiklemek için kullanabileceğiniz bir ölçüm, *bağlı cihazlar* sağlar.

Belirli bir senaryo için Event Grid veya Azure Izleyici 'yi kullanmaya karar verirken aşağıdakileri göz önünde bulundurun:

* Uyarı gecikmesi: IoT Hub bağlantı olayları Event Grid aracılığıyla çok daha hızlı bir şekilde dağıtılır. Bu, hızlı bildirimin tercih edildiği senaryolar için Event Grid daha iyi bir seçenek sunar.

* Cihaz başına bildirimler: Event Grid, tek tek cihazlar için bağlantıları izleme ve bağlantılarının kesilme olanağı sağlar. Bu, kritik cihazların bağlantılarını izlemeniz gereken senaryolar için Event Grid daha iyi bir seçenektir.

* Hafif Kurulum: Azure Izleyici ölçüm uyarıları, e-posta, SMS, ses ve diğer bildirimlerle bildirim teslim etmek için diğer hizmetlerle tümleştirme gerektirmeyen hafif bir kurulum deneyimi sağlar.  Event Grid ile, bildirim göndermek için diğer Azure hizmetleriyle tümleştirmeniz gerekir. Her iki hizmet de daha karmaşık eylemleri tetiklemek için diğer hizmetlerle tümleştirilebilir.

Üretim ortamları için düşük gecikme süreli, cihaz başına yetenekler nedeniyle, bağlantıları izlemek için Event Grid kullanmanızı kesinlikle öneririz. Kuşkusuz, seçim özel değildir ve hem Azure Izleyici ölçüm uyarılarını hem de Event Grid kullanabilirsiniz. İzleme bağlantısı kesilmenizin ne olduğuna bakmaksızın, beklenmedik cihaz bağlantısı kesilmesinin nedenlerini gidermeye yardımcı olması için Azure Izleyici kaynak günlüklerini de kullanacaksınız. Aşağıdaki bölümlerde bu seçeneklerin her biri daha ayrıntılı olarak ele alınmaktadır.

## <a name="event-grid-monitor-device-connect-and-disconnect-events"></a>Event Grid: cihaz bağlantısını ve bağlantı kesme olaylarını Izleme

Cihazdaki cihaz bağlantısını ve bağlantı kesmeyi izlemek için, uyarıları tetiklemek ve cihaz bağlantı durumunu izlemek üzere Event Grid ' de [ **DeviceConnected** ve **DeviceConnected** olayları](iot-hub-event-grid.md#event-types) abone olmayı öneririz. Event Grid, Azure Izleyici 'den çok daha düşük olay gecikmesi sağlar ve toplam bağlı cihaz sayısı yerine cihaz başına temelinde izleyebilirsiniz. Bu faktörler, kritik cihazları ve altyapıyı izlemek için tercih edilen yöntemi Event Grid yapar.

Cihaz bağlantısı kesildiğinde uyarıları izlemek veya tetiklemek için Event Grid kullandığınızda, Azure IoT SDK 'larını kullanan cihazlarda SAS belirteci yenilemesi nedeniyle, düzenli aralıklarla kesilen bağlantıları filtrelediğinizden emin olun. Daha fazla bilgi edinmek için bkz. [Azure IoT SDK 'ları Ile MQTT cihaz bağlantısı kesme davranışı](#mqtt-device-disconnect-behavior-with-azure-iot-sdks).

Event Grid ile cihaz bağlantısı olaylarını izleme hakkında daha fazla bilgi edinmek için aşağıdaki konuları inceleyin:

* IoT Hub Event Grid kullanmaya genel bir bakış için bkz. [Event Grid ile IoT Hub olaylara tepki](iot-hub-event-grid.md)verme. [Cihaz bağlantılı ve cihaz bağlantısı kesilen olaylar Için sınırlamalar](iot-hub-event-grid.md#limitations-for-device-connected-and-device-disconnected-events) bölümüne özellikle dikkat edin.

* Cihaz bağlantısı olaylarını sipariş etme hakkında bir öğretici için, bkz. [Azure IoT Hub cihaz bağlantısı olaylarını Azure Cosmos DB kullanarak](iot-hub-how-to-order-connection-state-events.md)sıralama.

* E-posta bildirimleri gönderme hakkında bir öğretici için bkz. Event Grid belgelerindeki [Event Grid ve Logic Apps kullanarak Azure IoT Hub olayları hakkında e-posta bildirimleri gönderme](../event-grid/publish-iot-hub-events-to-logic-apps.md) .

## <a name="azure-monitor-route-connection-events-to-logs"></a>Azure Izleyici: bağlantı olaylarını günlüklere yönlendir

IoT Hub, birkaç işlem kategorisi için kaynak günlüklerini sürekli olarak yayar. Bu günlük verilerini toplamak için, bir tanılama ayarı oluşturmanız gerekir, ancak bunu analiz edilecek veya arşivlenebilen bir hedefe yönlendirin. Bu tür bir hedef, Azure Izleyici günlüklerinde bir Log Analytics çalışma alanı ([bkz. fiyatlandırma](https://azure.microsoft.com/pricing/details/log-analytics/)) yoluyla, verileri kusto sorguları kullanarak analiz edebilirsiniz.

IoT Hub [kaynak günlüğü bağlantıları kategorisi](monitor-iot-hub-reference.md#connections) , cihaz bağlantılarıyla ilgili işlemler ve hatalar yayar. Aşağıdaki ekran görüntüsünde, bu günlükleri bir Log Analytics çalışma alanına yönlendiren bir tanılama ayarı gösterilmektedir:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/create-diagnostic-setting.png" alt-text="Log Analytics çalışma alanına bağlantı günlükleri göndermek için önerilen ayar.":::

IoT Hub 'ınızı oluşturduktan sonra mümkün olduğunca erken bir tanılama ayarı oluşturmanız önerilir, çünkü IoT Hub her zaman kaynak günlüklerini yayar, ancak bunları bir hedefe yönlendirene kadar Azure Izleyici tarafından toplanmaz.

Bir hedefe yönlendirme günlükleri hakkında daha fazla bilgi edinmek için bkz. [koleksiyon ve yönlendirme](monitor-iot-hub.md#collection-and-routing). Tanılama ayarı oluşturma hakkında ayrıntılı yönergeler için bkz. [ölçümleri ve günlükleri kullanma öğreticisi](tutorial-use-metrics-and-diags.md).

## <a name="azure-monitor-set-up-metric-alerts-for-device-disconnect-at-scale"></a>Azure Izleyici: ölçekte cihaz bağlantısı kesildiğinde ölçüm uyarıları ayarlama

IoT Hub tarafından yayılan platform ölçümleri temelinde uyarılar ayarlayabilirsiniz. Ölçüm uyarıları sayesinde, kişilere bir ilgi koşulunun oluştuğunu ve ayrıca söz konusu koşula otomatik olarak yanıt verebilecek eylemleri tetikleyebildiğini bildirebilirsiniz.

[*Bağlı cihazlar (Önizleme)*](monitor-iot-hub-reference.md#device-metrics) ölçümü, IoT Hub kaç cihazın bağlı olduğunu size bildirir. Bu ölçüm bir eşik değerinin altına düşerse tetiklenecek uyarılar oluşturabilirsiniz:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/configure-alert-logic.png" alt-text="Bağlı cihazlar ölçümü için uyarı mantığı ayarları.":::

Ölçüm uyarı kurallarını, cihaz bağlantısını kesme bozuklulıkları için izlemek üzere kullanabilirsiniz. Diğer bir deyişle, önemli sayıda cihazın beklenmedik şekilde bağlantısını keser. Böyle bir oluşum algılandığında, sorunu gidermeye yardımcı olması için günlüklere bakabilirsiniz. Cihaz başına kesilen ve kritik cihazların bağlantısını izlemek için; Ancak, Event Grid kullanmanız gerekir. Event Grid Ayrıca Azure ölçümlerinden daha gerçek zamanlı bir deneyim sağlar.

IoT Hub uyarılarla ilgili daha fazla bilgi için bkz. [izleyici IoT Hub uyarılar](monitor-iot-hub.md#alerts). IoT Hub uyarı oluşturma hakkında daha fazla bilgi için [ölçüm ve günlükleri kullanma öğreticisine](tutorial-use-metrics-and-diags.md)bakın. Uyarılara daha ayrıntılı bir genel bakış için bkz. Azure Izleyici belgelerindeki [Microsoft Azure uyarılara genel bakış](../azure-monitor/alerts/alerts-overview.md) .

## <a name="azure-monitor-use-logs-to-resolve-connectivity-errors"></a>Azure Izleyici: bağlantı hatalarını çözümlemek için günlükleri kullanma

Cihazın bağlantısını kestiğinde, Azure Izleyici ölçüm uyarıları veya Event Grid ile birlikte, nedeni gidermeye yardımcı olması için günlükleri kullanabilirsiniz. Bu bölümde, Azure Izleyici günlüklerinde yaygın sorunların nasıl aranacağı açıklanmaktadır. Aşağıdaki adımlarda, bir Log Analytics çalışma alanına IoT Hub bağlantı günlükleri göndermek için zaten bir [Tanılama ayarı](#azure-monitor-route-connection-events-to-logs) oluşturmuş olduğunuz varsayılmaktadır.

IoT Hub kaynak günlüklerini Azure Izleyici günlüklerine yönlendirmek için bir tanılama ayarı oluşturduktan sonra, günlükleri Azure portal görüntülemek için aşağıdaki adımları izleyin.

1. [Azure Portal](https://portal.azure.com)' de IoT Hub 'ınıza gidin.

1. IoT Hub 'ınızın sol bölmesinde **izleme** ' nin altında **Günlükler**' i seçin.

1. IoT Hub için bağlantı hata günlüklerini yalıtmak için sorgu Düzenleyicisi 'nde aşağıdaki sorguyu girin ve **Çalıştır**' ı seçin:

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Sonuçlar varsa, `OperationName` `ResultType` `ResultDescription` hata hakkında daha fazla ayrıntı almak için, (hata kodu) ve (hata iletisi) bölümüne bakın.

   ![Hata günlüğü örneği](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

Hatayı tanımladıktan sonra, en yaygın hatalarda yardım almak için sorun çözümleme kılavuzlarını izleyin:

* [400027 ConnectionForcefullyClosedOnNewConnection](iot-hub-troubleshoot-error-400027-connectionforcefullyclosedonnewconnection.md)

* [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)

* [401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)

* [409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)

* [500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

* [500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

## <a name="mqtt-device-disconnect-behavior-with-azure-iot-sdks"></a>Azure IoT SDK 'Ları ile MQTT cihaz bağlantısı kesme davranışı

Azure IoT cihaz SDK 'Ları IoT Hub bağlantısını keser ve ardından, SAS belirteçlerini MQTT (ve WebSockets üzerinden MQTT) protokolü üzerinden yenilediklerinde yeniden bağlanır. Günlüklerde bu, bazen hata olaylarıyla birlikte bilgi veren cihaz bağlantısı kesme ve bağlanma olayları olarak gösterilir.

Varsayılan olarak, belirteç ömrü tüm SDK 'lar için 60 dakikadır; Ancak, bazı SDK 'larda geliştiriciler tarafından değiştirilebilir. Aşağıdaki tabloda, SDK 'Lardan her biri için kullanım belirteçleri, belirteç yenileme ve belirteç yenileme davranışı özetlenmektedir:

| SDK | Belirteç kullanım ömrü | Belirteç yenileme | Yenileme davranışı |
|-----|----------|---------------------|---------|
| .NET | 60 dakika, yapılandırılabilir | %85 of Lifespan, yapılandırılabilir | SDK, süre sonunda ve 10 dakikalık bir yetkisiz kullanım süresi ile bağlantı kesilir ve bağlantıyı keser. Günlüklerde oluşturulan bilgilendirme olayları ve hataları. |
| Java | 60 dakika, yapılandırılabilir | %85/ömrü, yapılandırılamaz | SDK, süre sonunda ve 10 dakikalık bir yetkisiz kullanım süresi ile bağlantı kesilir ve bağlantıyı keser. Günlüklerde oluşturulan bilgilendirme olayları ve hataları. |
| Node.js | 60 dakika, yapılandırılabilir | yapılandırılabilir | SDK, belirteç yenilemesinde bağlanır ve bağlantıyı keser. Günlüklerde yalnızca bilgilendirme olayları oluşturulur. |
| Python | 60 dakika, yapılandırılamaz | -- | SDK bağlantısı ve bağlantı kesilen belirteç kullanım belirteçleri. |

Aşağıdaki ekran görüntülerinde, farklı SDK 'lar için Azure Izleyici günlüklerinde belirteç yenileme davranışı gösterilmektedir. Belirteç ömrü ve yenileme eşiği varsayılan olarak belirtildiği şekilde değiştirilmiştir.

* 1200 saniye (20 dakikalık) belirteç ömrü ve yenileme kümesi içeren .NET cihaz SDK 'Sı, %90 ' nin% ' de gerçekleşecektir. her 30 dakikada bir bağlantıyı keser:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/net-mqtt.png" alt-text=".NET SDK ile Azure Izleyici günlüklerinde MQTT üzerinden belirteç yenilemeye yönelik hata davranışı.":::

* 300 saniye (5 dakikalık) belirteç ömrü ve varsayılan %85 olan Java SDK 'Sı, %0. Bağlantıyı kesilen her 15 dakikada bir gerçekleşir:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/java-mqtt.png" alt-text="Java SDK ile Azure Izleyici günlüklerinde MQTT üzerinden belirteç yenilemeye yönelik hata davranışı.":::

* 300 saniye (5 dakikalık) belirteç ömrü ve belirteç yenileme kümesi 3 dakika olarak gerçekleşecek düğüm SDK. Bağlantı kesilen belirteç yenileme durumunda meydana gelir. Ayrıca, hata yok, yalnızca bilgilendirici Connect/Disconnect olayları yayınlanır:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/node-mqtt.png" alt-text="Node SDK ile Azure Izleyici günlüklerinde MQTT üzerinden belirteç yenilemeye yönelik hata davranışı.":::

Aşağıdaki sorgu sonuçları toplamak için kullanıldı. Sorgu, özellik çantasından SDK adını ve sürümünü ayıklar; daha fazla bilgi edinmek için [IoT Hub günlüklerinde SDK sürümü](monitor-iot-hub.md#sdk-version-in-iot-hub-logs)' ne bakın.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s)
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId) , Protocol =  tostring(parsed_json.protocol)
| distinct TimeGenerated, OperationName, Level, ResultType, ResultDescription, DeviceId, Protocol, SDKVersion

```

IoT çözümleri geliştirici veya operatörü olarak, Connect/Disconnect olaylarını ve günlüklerdeki ilgili hataları yorumlamak için bu davranışın farkında olmanız gerekir. Cihazların belirteç ömrü veya yenileme davranışını değiştirmek istiyorsanız, cihazın bir cihaz ikizi ayarı mı yoksa bunu mümkün kılan bir cihaz yöntemi mi uyguladığını denetleyin.

Cihaz bağlantılarını Olay Hub 'ı ile izliyorsanız, SAS belirteci yenilemesi nedeniyle düzenli olarak kesilen bağlantıları filtreleyerek derlediğinizden emin olun; Örneğin, bağlantı kesme olayının ardından belirli bir zaman aralığında bir Connect olayı olduğu sürece, bağlantıları kesilen eylemleri tetiklememe.

> [!NOTE]
> IoT Hub, cihaz başına yalnızca bir etkin MQTT bağlantısını destekler. Aynı cihaz KIMLIĞI adına göre yeni MQTT bağlantısı, IoT Hub var olan bağlantıyı bırakmaya neden olur.
>
> 400027 Connectionforcefullyıclosedonnewconnection IoT Hub günlüklerinde oturum açacaktır

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Adımları denedim, ancak bunlar çalışmadı

Önceki adımlarda yardım olmadıysa, şunları deneyin:

* Fiziksel veya uzaktan (SSH gibi) sorunlu cihazlara erişiminiz varsa, sorun gidermeye devam etmek için [cihaz tarafı sorun giderme kılavuzunu](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) izleyin.

* IoT Hub 'ınızın > IoT cihazlarınızın Azure portal >, cihazlarınızın **etkinleştirildiğini** doğrulayın.

* Cihazınız MQTT protokolünü kullanıyorsa, 8883 numaralı bağlantı noktasının açık olduğunu doğrulayın. Daha fazla bilgi için bkz. [IoT Hub bağlanma (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

* Azure IoT Hub, [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)veya [Azure desteği](https://azure.microsoft.com/support/options/) [için Microsoft Q&soru sayfasından](/answers/topics/azure-iot-hub.html)yardım alın.

Herkesin belgelerinin artırılmasına yardımcı olmak için, bu kılavuz size yardımcı olmadıysa aşağıdaki geri bildirim bölümünde bir yorum bırakın.

## <a name="next-steps"></a>Sonraki adımlar

* Geçici sorunları çözme hakkında daha fazla bilgi için bkz. [geçici hata işleme](/azure/architecture/best-practices/transient-faults).

* Azure IoT SDK ve yeniden denemeleri yönetme hakkında daha fazla bilgi edinmek için bkz. [azure IoT Hub cihaz SDK 'larını kullanarak bağlantı ve güvenilir mesajlaşma yönetimi](iot-hub-reliability-features-in-sdks.md#connection-and-retry).