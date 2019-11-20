---
title: Azure IoT Hub ileti yönlendirmeyi anlama | Microsoft Docs
description: Geliştirici Kılavuzu-cihazdan buluta iletileri göndermek için ileti yönlendirmeyi kullanma. Hem telemetri hem de telemetri olmayan verileri gönderme hakkında bilgi içerir.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: ff50d972ad9590fb70dbcf67e21f8b5dc8c32fad
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748049"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Farklı uç noktalara cihazdan buluta iletiler göndermek için IoT Hub ileti yönlendirmeyi kullanma

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

İleti yönlendirme, cihazlarınızdan bulut hizmetlerine otomatik, ölçeklenebilir ve güvenilir bir şekilde ileti göndermenizi sağlar. İleti yönlendirme şu şekilde kullanılabilir: 

* Cihaz **telemetri iletilerinin yanı sıra** , cihaz yaşam döngüsü olayları ve cihaz ikizi değişiklik olayları yerleşik uç nokta ve özel uç noktalara gönderilmesi. [Yönlendirme uç noktaları](#routing-endpoints)hakkında bilgi edinin.

* Zengin sorgular uygulayarak **verileri çeşitli uç noktalara yönlendirmeden önce verileri filtreleme** . İleti yönlendirme, ileti özellikleri ve ileti gövdesinde, Device ikizi etiketleri ve Device ikizi özellikleri ile sorgulama yapmanıza olanak sağlar. [İleti yönlendirmesinde sorguları](iot-hub-devguide-routing-query-syntax.md)kullanma hakkında daha fazla bilgi edinin.

IoT Hub, ileti yönlendirmenin çalışması için bu hizmet uç noktalarına yazma erişimi gerektirir. Uç noktalarınızı Azure portal aracılığıyla yapılandırırsanız, sizin için gerekli izinler eklenir. Hizmetlerinizi beklenen aktarım hızını destekleyecek şekilde yapılandırmadığınızdan emin olun. Örneğin, özel bir uç nokta olarak Event Hubs kullanıyorsanız, bu olay hub 'ının **üretilen iş birimlerini** , IoT Hub ileti yönlendirme aracılığıyla gönderilmesini planladığınız olayların giriş işlemesini işleyebilecek şekilde yapılandırmanız gerekir. Benzer şekilde, bir Service Bus kuyruğu bitiş noktası olarak kullanıldığında, bir sıranın, tüketicileri görünene kadar tüm verileri tutabileceği şekilde **maksimum boyutu** yapılandırmanız gerekir. IoT çözümünüzü ilk kez yapılandırdığınızda, ek uç noktalarınızı izlemeniz ve gerçek yük için gereken ayarlamaları yapmanız gerekebilir.

IoT Hub, protokollerde birlikte çalışabilirlik için tüm cihazdan buluta mesajlaşma için [ortak bir biçim](iot-hub-devguide-messages-construct.md) tanımlar. Bir ileti aynı uç noktayı işaret eden birden çok yol ile eşleşiyorsa, IoT Hub ileti yalnızca bir kez bu uç noktaya teslim edilir. Bu nedenle, Service Bus kuyruğunuza veya konusunda Yinelenenleri kaldırmayı yapılandırmanız gerekmez. Bölümlenmiş sıralarda, Bölüm benzeşimi ileti sıralamasını garanti eder. [İleti yönlendirmeyi nasıl yapılandıracağınızı](tutorial-routing.md)öğrenmek için bu öğreticiyi kullanın.

## <a name="routing-endpoints"></a>Yönlendirme uç noktaları

IoT Hub 'ı, Event Hubs ile uyumlu bir varsayılan yerleşik uç noktaya (**iletiler/olaylar**) sahiptir. Aboneliğinizdeki diğer hizmetleri IoT Hub bağlayarak, iletileri yönlendirmek için [Özel uç noktalar](iot-hub-devguide-endpoints.md#custom-endpoints) oluşturabilirsiniz. 

Her ileti, yönlendirme sorguları eşleşen tüm uç noktalara yönlendirilir. Diğer bir deyişle, bir ileti birden çok uç noktaya yönlendirilebilir.

IoT Hub Şu anda özel uç noktalar olarak aşağıdaki hizmetleri desteklemektedir:

### <a name="built-in-endpoint"></a>Yerleşik uç nokta

Yerleşik uç noktadan (**iletiler/olaylar**) cihazdan buluta iletileri almak için standart [Event Hubs tümleştirme ve SDK](iot-hub-devguide-messages-read-builtin.md) 'lar kullanabilirsiniz. Bir yol oluşturulduktan sonra, bu uç nokta için bir yol oluşturulmadığı takdirde veriler yerleşik uç noktaya akar.

### <a name="azure-storage"></a>Azure Storage

İki depolama hizmeti vardır IoT Hub-- [Azure Blob depolama](../storage/blobs/storage-blobs-introduction.md) ve [Azure Data Lake Storage 2.](../storage/blobs/data-lake-storage-introduction.md) (ADLS 2.) hesaplarına iletileri yönlendirebilir. Azure Data Lake Storage hesapları, BLOB depolama alanı üzerinde oluşturulmuş [hiyerarşik ad alanı](../storage/blobs/data-lake-storage-namespace.md)etkinleştirilmiş depolama hesaplarıdır. Bunların her ikisi de depolama alanı için blob 'ları kullanır.

IoT Hub, verileri Azure depolama 'ya [Apache avro](https://avro.apache.org/) BIÇIMINDE ve JSON biçiminde yazmayı destekler. Varsayılan değer AVRO ' dir. Kodlama biçimi yalnızca BLOB depolama uç noktası yapılandırıldığında ayarlanabilir. Biçim varolan bir uç nokta için düzenlenemiyor. JSON kodlaması kullanılırken, ileti [sistemi özelliklerinde](iot-hub-devguide-routing-query-syntax.md#system-properties)ContentType öğesini **Application/JSON** ve Contentenencoding olarak **UTF-8** olarak ayarlamanız gerekir. Bu değerlerin her ikisi de büyük/küçük harfe duyarsızdır. İçerik kodlaması ayarlanmamışsa, IoT Hub iletileri temel 64 kodlu biçimde yazar. Kod oluşturma veya güncelleştirme REST API, özellikle [Routingstoragecontainerproperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), Azure Portal, [Azure CLI](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)veya [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0)IoT Hub kullanarak kodlama biçimini seçebilirsiniz. Aşağıdaki diyagramda Azure portal kodlama biçiminin nasıl ayarlanacağı gösterilmektedir.

![BLOB depolama uç noktası kodlama](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

Toplu iş iletileri IoT Hub ve yığın belirli bir boyuta ulaştığında veya belirli bir süre geçtiğinde verileri depolamaya yazar. IoT Hub varsayılan olarak aşağıdaki dosya adlandırma kuralına sahiptir: 

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Herhangi bir dosya adlandırma kuralını kullanabilirsiniz, ancak listelenen tüm belirteçleri kullanmanız gerekir. Yazılacak veri yoksa IoT Hub boş bir bloba yazılır.

Tüm Blobların veya dosyaların bölüm varsayımlarını yapmadan okunmalarını sağlamak için Blobları veya dosyaları listeleyip daha sonra bu dosyaların üzerinde değişiklik yapmanızı öneririz. Bölüm aralığı, [Microsoft tarafından başlatılan bir yük devretme](iot-hub-ha-dr.md#microsoft-initiated-failover) veya [el ile yük devretme](iot-hub-ha-dr.md#manual-failover)IoT Hub değişebilir. Dosya listesi için Blobların listesini veya [ADLS 2. API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) listesini listelemek Için, [LISTE bloblarını API](https://docs.microsoft.com/rest/api/storageservices/list-blobs) 'sini kullanabilirsiniz. Lütfen kılavuz olarak aşağıdaki örneğe bakın.

```csharp
public void ListBlobsInContainer(string containerName, string iothub)
{
    var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
    var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
    if (cloudBlobContainer.Exists())
    {
        var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
        foreach (IListBlobItem item in results)
        {
            Console.WriteLine(item.Uri);
        }
    }
}
```

Azure Data Lake Gen2 uyumlu bir depolama hesabı oluşturmak için yeni bir v2 depolama hesabı oluşturun ve aşağıdaki görüntüde gösterildiği gibi **Gelişmiş** sekmesinde *hiyerarşik ad alanı* alanında *etkin* ' i seçin:

![Azure Date Lake Gen2 Storage seçin](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)


### <a name="service-bus-queues-and-service-bus-topics"></a>Service Bus kuyrukları ve Service Bus konuları

IoT Hub uç noktaları olarak kullanılan Service Bus kuyrukları ve konuları, **oturumlara** veya **yinelenen algılamanın** etkin olmasını gerektirmemelidir. Bu seçeneklerden biri etkinse, uç nokta Azure portal **ulaşılamaz** olarak görünür.

### <a name="event-hubs"></a>Event Hubs

Yerleşik Event Hubs uyumlu uç noktasından ayrı olarak, verileri Event Hubs türündeki özel uç noktalara de yönlendirebilirsiniz. 

## <a name="reading-data-that-has-been-routed"></a>Yönlendirilen verileri okuma

Bu [öğreticiyi](tutorial-routing.md)izleyerek bir rota yapılandırabilirsiniz.

Bir uç noktadan ileti okumayı öğrenmek için aşağıdaki öğreticileri kullanın.

* [Yerleşik uç](quickstart-send-telemetry-node.md) noktadan okuma

* [BLOB depolamadan](../storage/blobs/storage-blob-event-quickstart.md) okuma

* [Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) okunuyor

* [Service Bus kuyruklarından](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) okuma

* [Service Bus konulardan](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions) okuyun

## <a name="fallback-route"></a>Geri dönüş yolu

Geri dönüş yolu, var olan yolların herhangi birine (**iletiler/olaylar**) [Event Hubs](/azure/event-hubs/)ile uyumlu olan herhangi bir mevcut Event Hubs rotadaki sorgu koşullarını karşılamayan tüm iletileri gönderir. İleti yönlendirme açıksa, geri dönüş yolu özelliğini etkinleştirebilirsiniz. Bir yol oluşturulduktan sonra, bu uç nokta için bir yol oluşturulmadığı takdirde veriler yerleşik uç noktaya akar. Yerleşik uç noktaya bir yol yoksa ve bir geri dönüş yolu etkinleştirilirse, yalnızca rotalardaki sorgu koşullarına uymamaları, yerleşik uç noktaya gönderilir. Ayrıca, mevcut tüm yollar silinirse, tüm verileri yerleşik uç noktada almak için geri dönüş yolunun etkinleştirilmesi gerekir.

Azure portal > Ileti yönlendirme dikey penceresinde geri dönüş yolunu etkinleştirebilir/devre dışı bırakabilirsiniz. Ayrıca, geri dönüş yolu için özel bir uç nokta kullanmak üzere [Fallbackrouteproperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) için Azure Resource Manager de kullanabilirsiniz.

## <a name="non-telemetry-events"></a>Telemetri olmayan olaylar

Cihaz telemetrisine ek olarak, ileti yönlendirme Ayrıca cihaz ikizi değişiklik olayları, cihaz yaşam döngüsü olayları ve dijital ikizi değişiklik olayları (genel önizlemede) gönderilmesini sağlar. Örneğin, veri kaynağı **cihaz ikizi değişiklik olayları**olarak ayarlanmış bir yol oluşturulduysa, IoT Hub cihaz ikizi 'deki değişikliği içeren uç noktaya iletiler gönderir. Benzer şekilde, veri kaynağı **cihaz yaşam döngüsü olaylarına**ayarlanmış bir yol oluşturulduysa, IoT Hub cihazın silinip silinmediğini veya oluşturulduğunu belirten bir ileti gönderir. Son olarak, [ıot Tak ve Kullan genel önizleme](../iot-pnp/overview-iot-plug-and-play.md)kapsamında bir geliştirici, veri kaynağı olarak **dijital ikizi değişiklik olayları** olarak ayarlanmış yollar oluşturabilir IoT Hub ve bir Digital ikizi [özelliği](../iot-pnp/iot-plug-and-play-glossary.md) ayarlandığında veya değiştirildiğinde bir dijital [ikizi ](../iot-pnp/iot-plug-and-play-glossary.md)değiştirilir veya temel alınan cihaz ikizi için bir değişiklik olayı meydana gelir.

IoT Hub Ayrıca, bu olaylara göre gerçek zamanlı tümleştirmeleri ve iş akışlarının otomatikleştirilmesini desteklemek üzere cihaz olaylarını yayımlamak için [Azure Event Grid ile tümleşir](iot-hub-event-grid.md) . Senaryonuza en uygun olanı öğrenmek için [ileti yönlendirme ve Event Grid arasındaki önemli farklılıkları](iot-hub-event-grid-routing-comparison.md) inceleyin.

## <a name="testing-routes"></a>Yolları test etme

Yeni bir rota oluşturduğunuzda veya mevcut bir yolu düzenlediğinizde, rota sorgusunu örnek bir iletiyle test etmelisiniz. Tek seferde tüm yolları test edebilir veya test sırasında uç noktalara hiçbir ileti yönlendirilemez. Azure portal, Azure Resource Manager, Azure PowerShell ve Azure CLı test için kullanılabilir. Sonuçlar, örnek iletinin sorguyla eşleşip eşleşmediğini, iletinin sorguyla eşleşmedi olduğunu veya örnek ileti ya da sorgu söz dizimi yanlış olduğu için test çalıştırılamadı sınamasını belirlemesine yardımcı olur. Daha fazla bilgi için bkz. [Test rotası](/rest/api/iothub/iothubresource/testroute) ve [tüm yolları test](/rest/api/iothub/iothubresource/testallroutes)etme.

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>En az bir kez tesliminde sıralama garantisi

İleti yönlendirme IoT Hub sıralı ve en az bir kez bitiş noktalarına ileti teslimi garantisi sağlar. Bu, yinelenen iletiler olabileceği ve özgün ileti sıralamasını yeniden aktarılan bir dizi mesaj olabileceği anlamına gelir. Örneğin, özgün ileti sırası [1, 2, 3, 4] ise, [1, 2, 1, 2, 3, 1, 2, 3, 4] gibi bir ileti sırası alabilirsiniz. Sıralama garantisi, [1] iletisini almanız durumunda her zaman [2, 3, 4] gelmelidir.

İleti yinelemelerini işlemek için, genellikle bir cihaz veya modül olan kaynak noktasındaki iletinin uygulama özelliklerinde benzersiz bir tanımlayıcı damgalaması önerilir. İletileri kullanan hizmet, bu tanımlayıcıyı kullanarak yinelenen iletileri işleyebilir.

## <a name="latency"></a>Gecikme süresi

Yerleşik uç noktaları kullanarak cihazdan buluta telemetri iletilerini yönlendirdiğinizde, ilk yol oluşturulduktan sonra uçtan uca gecikmede hafif bir artış vardır.

Çoğu durumda, gecikmede ortalama artış 500 MS 'den düşüktür. Yönlendirmeyi kullanarak gecikme süresini izleyebilirsiniz **: iletiler/olaylar için ileti gecikmesi** veya **D2C. endpoints. Latency. builtIn. Events** IoT Hub ölçümü. Birinci bir yol oluşturulması veya silinmesi, uçtan uca gecikme süresini etkilemez.

## <a name="monitoring-and-troubleshooting"></a>İzleme ve sorun giderme

IoT Hub, Yönlendirme ve uç noktalarla ilgili olarak, hub 'ınızın ve gönderilen iletilerinizin sistem durumuna ilişkin bir genel bakış sunan çeşitli ölçümler sağlar. Sorunların temel nedenini belirlemek için birden fazla ölçüden bilgi birleştirebilirsiniz. Örneğin, ölçüm yönlendirmesi kullanın **: bırakılan telemetri iletileri** veya **D2C. telemetri. çıkış.** bırakılan telemetri iletileri ve geri dönüş rotası devre dışı bırakılmışsa yapılan sorgularla eşleşmediği zaman bırakılan ileti sayısını belirlemek için bırakıldı. [IoT Hub ölçümler](iot-hub-metrics.md) , IoT Hub varsayılan olarak etkinleştirilen tüm ölçümleri listeler.

Uç noktaların [sistem durumunu](iot-hub-devguide-endpoints.md#custom-endpoints) almak Için [uç nokta durumu Al](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) REST API kullanabilirsiniz. Uç nokta durumu ölü veya sağlıksız olduğunda hataları tanımlamak ve hatalarını ayıklamak için ileti gecikmesini yönlendirme ile ilgili [IoT Hub ölçümlerini](iot-hub-metrics.md) kullanmanızı öneririz. Örneğin, Event Hubs uç nokta türü için, **D2C. endpoints. Latency. eventHubs**' ı izleyebilirsiniz. Sağlıklı olmayan bir uç noktanın durumu, IoT Hub sonunda tutarlı bir sistem durumu oluşturulduğunda sağlıklı olarak güncelleştirilecektir.

Azure Izleyici [Tanılama ayarlarındaki](../iot-hub/iot-hub-monitor-resource-health.md)tanılama günlüklerini **rotalar** ' ı kullanarak, bir yönlendirme sorgusunun değerlendirmesi sırasında oluşan hataları ve IoT Hub tarafından algılanan bitiş noktası durumunu (örneğin, bir uç nokta yok edildiğinde) izleyebilirsiniz. Bu tanılama günlükleri, Azure Izleyici günlüklerine, Event Hubs veya özel işleme için Azure depolama 'ya gönderilebilir.

## <a name="next-steps"></a>Sonraki adımlar

* Ileti yollarının nasıl oluşturulacağını öğrenmek için bkz. [yolları kullanarak cihazdan buluta Iletileri işleme IoT Hub](tutorial-routing.md).

* [Cihazdan buluta iletileri gönderme](quickstart-send-telemetry-node.md)

* Cihazdan buluta iletileri göndermek için kullanabileceğiniz SDK 'lar hakkında daha fazla bilgi için bkz. [Azure IoT SDK 'ları](iot-hub-devguide-sdks.md).
