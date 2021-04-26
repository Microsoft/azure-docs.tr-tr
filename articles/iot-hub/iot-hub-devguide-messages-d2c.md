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
ms.custom:
- 'Role: Cloud Development'
- devx-track-csharp
ms.openlocfilehash: 07bbd50dbc415b86aa0c511d46ead9f0612df107
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642500"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Farklı uç noktalara cihazdan buluta iletiler göndermek için IoT Hub ileti yönlendirmeyi kullanma

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

İleti yönlendirme, cihazlarınızdan bulut hizmetlerine otomatik, ölçeklenebilir ve güvenilir bir şekilde ileti göndermenizi sağlar. İleti yönlendirme şu şekilde kullanılabilir: 

* Cihaz **telemetri iletilerinin yanı sıra** , cihaz yaşam döngüsü olayları, cihaz ikizi değişiklik olayları ve dijital ikizi değişiklik olayları yerleşik uç nokta ve özel uç noktalara gönderme. [Yönlendirme uç noktaları](#routing-endpoints)hakkında bilgi edinin. IoT Tak ve Kullan cihazlarından gönderilen olaylar hakkında daha fazla bilgi edinmek için bkz. [ıot Tak ve kullan dijital TWINS 'ı anlama](../iot-pnp/concepts-digital-twin.md).

* Zengin sorgular uygulayarak **verileri çeşitli uç noktalara yönlendirmeden önce verileri filtreleme** . İleti yönlendirme, ileti özellikleri ve ileti gövdesinde, Device ikizi etiketleri ve Device ikizi özellikleri ile sorgulama yapmanıza olanak sağlar. [İleti yönlendirmesinde sorguları](iot-hub-devguide-routing-query-syntax.md)kullanma hakkında daha fazla bilgi edinin.

IoT Hub, ileti yönlendirmenin çalışması için bu hizmet uç noktalarına yazma erişimi gerektirir. Uç noktalarınızı Azure portal aracılığıyla yapılandırırsanız, sizin için gerekli izinler eklenir. Hizmetlerinizi beklenen aktarım hızını destekleyecek şekilde yapılandırmadığınızdan emin olun. Örneğin, özel bir uç nokta olarak Event Hubs kullanıyorsanız, bu olay hub 'ının **üretilen iş birimlerini** , IoT Hub ileti yönlendirme aracılığıyla gönderilmesini planladığınız olayların giriş işlemesini işleyebilecek şekilde yapılandırmanız gerekir. Benzer şekilde, bir Service Bus kuyruğu bitiş noktası olarak kullanıldığında, bir sıranın, tüketicileri görünene kadar tüm verileri tutabileceği şekilde **maksimum boyutu** yapılandırmanız gerekir. IoT çözümünüzü ilk kez yapılandırdığınızda, ek uç noktalarınızı izlemeniz ve gerçek yük için gereken ayarlamaları yapmanız gerekebilir.

IoT Hub, protokollerde birlikte çalışabilirlik için tüm cihazdan buluta mesajlaşma için [ortak bir biçim](iot-hub-devguide-messages-construct.md) tanımlar. Bir ileti aynı uç noktayı işaret eden birden çok yol ile eşleşiyorsa, IoT Hub ileti yalnızca bir kez bu uç noktaya teslim edilir. Bu nedenle, Service Bus kuyruğunuza veya konusunda Yinelenenleri kaldırmayı yapılandırmanız gerekmez. Bölümlenmiş sıralarda, Bölüm benzeşimi ileti sıralamasını garanti eder. [İleti yönlendirmeyi nasıl yapılandıracağınızı](tutorial-routing.md)öğrenmek için bu öğreticiyi kullanın.

## <a name="routing-endpoints"></a>Yönlendirme uç noktaları

IoT Hub 'ı, Event Hubs ile uyumlu bir varsayılan yerleşik uç noktaya (**iletiler/olaylar**) sahiptir. Aboneliğinizdeki diğer hizmetleri IoT Hub bağlayarak, iletileri yönlendirmek için [Özel uç noktalar](iot-hub-devguide-endpoints.md#custom-endpoints) oluşturabilirsiniz. 

Her ileti, yönlendirme sorguları eşleşen tüm uç noktalara yönlendirilir. Diğer bir deyişle, bir ileti birden çok uç noktaya yönlendirilebilir.

Özel uç noktanıza güvenlik duvarı yapılandırmalarına sahipseniz, IoT Hub belirli bir uç nokta [Azure depolama](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing), [azure Event Hubs](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing) ve [Azure Service Bus](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing)için erişim sağlamak üzere Microsoft 'un güvendiği ilk taraf özel durumunu kullanmayı düşünün. Bu, [yönetilen hizmet kimliği](./virtual-network-support.md)olan IoT Hub 'ları için seçim bölgelerinde kullanılabilir.

IoT Hub Şu anda aşağıdaki uç noktaları desteklemektedir:

 - Yerleşik uç nokta
 - Azure Depolama
 - Service Bus kuyrukları ve Service Bus konuları
 - Event Hubs

## <a name="built-in-endpoint-as-a-routing-endpoint"></a>Yönlendirme uç noktası olarak yerleşik uç nokta

Yerleşik uç noktadan (**iletiler/olaylar**) cihazdan buluta iletileri almak için standart [Event Hubs tümleştirme ve SDK](iot-hub-devguide-messages-read-builtin.md) 'lar kullanabilirsiniz. Bir yol oluşturulduktan sonra, bu uç nokta için bir yol oluşturulmadığı takdirde veriler yerleşik uç noktaya akar.

## <a name="azure-storage-as-a-routing-endpoint"></a>Yönlendirme uç noktası olarak Azure depolama

İki depolama hizmeti vardır IoT Hub-- [Azure Blob depolama](../storage/blobs/storage-blobs-introduction.md) ve [Azure Data Lake Storage 2.](../storage/blobs/data-lake-storage-introduction.md) (ADLS 2.) hesaplarına iletileri yönlendirebilir. Azure Data Lake Storage hesapları, BLOB depolama alanı üzerinde oluşturulmuş [hiyerarşik ad alanı](../storage/blobs/data-lake-storage-namespace.md)etkinleştirilmiş depolama hesaplarıdır. Bunların her ikisi de depolama alanı için blob 'ları kullanır.

IoT Hub, verileri Azure depolama 'ya [Apache avro](https://avro.apache.org/) BIÇIMINDE ve JSON biçiminde yazmayı destekler. Varsayılan değer AVRO ' dir. JSON kodlaması kullanılırken, ileti [sistemi özelliklerinde](iot-hub-devguide-routing-query-syntax.md#system-properties)ContentType öğesini **Application/JSON** ve Contentenencoding olarak **UTF-8** olarak ayarlamanız gerekir. Bu değerlerin her ikisi de büyük/küçük harfe duyarsızdır. İçerik kodlaması ayarlanmamışsa, IoT Hub iletileri temel 64 kodlu biçimde yazar.

Kodlama biçimi yalnızca BLOB depolama uç noktası yapılandırıldığında ayarlanabilir; Mevcut bir uç nokta için düzenlenemez. Varolan bir uç nokta için kodlama biçimlerini değiştirmek için, Özel uç noktayı istediğiniz biçimde silip yeniden oluşturmanız gerekir. Bir yardımcı strateji, istediğiniz kodlama biçiminizle yeni bir özel uç nokta oluşturmak ve bu uç noktaya paralel bir yol eklemek olabilir. Bu şekilde, var olan uç noktasını silmeden önce verilerinizi doğrulayabilirsiniz.

IoT Hub oluşturma veya güncelleştirme REST API, özellikle [Routingstoragecontainerproperties](/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), Azure Portal, [Azure CLI](/cli/azure/iot/hub/routing-endpoint)veya [Azure PowerShell](/powershell/module/az.iothub/add-aziothubroutingendpoint)kullanarak kodlama biçimini seçebilirsiniz. Aşağıdaki görüntüde Azure portal kodlama biçiminin nasıl ayarlanacağı gösterilmektedir.

![BLOB depolama uç noktası kodlama](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

Toplu iş iletileri IoT Hub ve yığın belirli bir boyuta ulaştığında veya belirli bir süre geçtiğinde verileri depolamaya yazar. IoT Hub varsayılan olarak aşağıdaki dosya adlandırma kuralına sahiptir:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Herhangi bir dosya adlandırma kuralını kullanabilirsiniz, ancak listelenen tüm belirteçleri kullanmanız gerekir. Yazılacak veri yoksa IoT Hub boş bir bloba yazılır.

Tüm Blobların veya dosyaların bölüm varsayımlarını yapmadan okunmalarını sağlamak için Blobları veya dosyaları listeleyip daha sonra bu dosyaların üzerinde değişiklik yapmanızı öneririz. Bölüm aralığı, [Microsoft tarafından başlatılan bir yük devretme](iot-hub-ha-dr.md#microsoft-initiated-failover) veya [el ile yük devretme](iot-hub-ha-dr.md#manual-failover)IoT Hub değişebilir. Dosya listesi için Blobların listesini veya [ADLS 2. API](/rest/api/storageservices/datalakestoragegen2/path) listesini listelemek Için, [LISTE bloblarını API](/rest/api/storageservices/list-blobs) 'sini kullanabilirsiniz. Lütfen kılavuz olarak aşağıdaki örneğe bakın.

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

## <a name="service-bus-queues-and-service-bus-topics-as-a-routing-endpoint"></a>Service Bus kuyrukları ve Service Bus konuları yönlendirme uç noktası olarak

IoT Hub uç noktaları olarak kullanılan Service Bus kuyrukları ve konuları, **oturumlara** veya **yinelenen algılamanın** etkin olmasını gerektirmemelidir. Bu seçeneklerden biri etkinse, uç nokta Azure portal **ulaşılamaz** olarak görünür.

## <a name="event-hubs-as-a-routing-endpoint"></a>Yönlendirme uç noktası olarak Event Hubs

Yerleşik Event Hubs uyumlu uç noktasından ayrı olarak, verileri Event Hubs türündeki özel uç noktalara de yönlendirebilirsiniz. 

## <a name="reading-data-that-has-been-routed"></a>Yönlendirilen verileri okuma

Bu [öğreticiyi](tutorial-routing.md)izleyerek bir rota yapılandırabilirsiniz.

Bir uç noktadan ileti okumayı öğrenmek için aşağıdaki öğreticileri kullanın.

* [Yerleşik uç](quickstart-send-telemetry-node.md) noktadan okuma

* [BLOB depolamadan](../storage/blobs/storage-blob-event-quickstart.md) okuma

* [Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) okunuyor

* [Service Bus kuyruklarından](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) okuma

* [Service Bus konulardan](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md) okuyun


## <a name="fallback-route"></a>Geri dönüş yolu

Geri dönüş yolu, var olan yolların herhangi birine (**iletiler/olaylar**) [Event Hubs](../event-hubs/index.yml)ile uyumlu olan herhangi bir mevcut Event Hubs rotadaki sorgu koşullarını karşılamayan tüm iletileri gönderir. İleti yönlendirme açıksa, geri dönüş yolu özelliğini etkinleştirebilirsiniz. Bir yol oluşturulduktan sonra, bu uç nokta için bir yol oluşturulmadığı takdirde veriler yerleşik uç noktaya akar. Yerleşik uç noktaya bir yol yoksa ve bir geri dönüş yolu etkinleştirilirse, yalnızca rotalardaki sorgu koşullarına uymamaları, yerleşik uç noktaya gönderilir. Ayrıca, mevcut tüm yollar silinirse, tüm verileri yerleşik uç noktada almak için geri dönüş yolunun etkinleştirilmesi gerekir.

Azure portal >Ileti yönlendirme dikey penceresinde geri dönüş yolunu etkinleştirebilir/devre dışı bırakabilirsiniz. Ayrıca, geri dönüş yolu için özel bir uç nokta kullanmak üzere [Fallbackrouteproperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) için Azure Resource Manager de kullanabilirsiniz.

## <a name="non-telemetry-events"></a>Telemetri olmayan olaylar

Cihaz telemetrisine ek olarak, ileti yönlendirme cihaz ikizi değişiklik olayları, cihaz yaşam döngüsü olayları ve dijital ikizi değişiklik olaylarının gönderilmesini de sağlar. Örneğin, veri kaynağı **cihaz ikizi değişiklik olayları** olarak ayarlanmış bir yol oluşturulduysa, IoT Hub cihaz ikizi 'deki değişikliği içeren uç noktaya iletiler gönderir. Benzer şekilde, veri kaynağı **cihaz yaşam döngüsü olaylarına** ayarlanmış bir yol oluşturulduysa, IoT Hub cihazın silinip silinmediğini veya oluşturulduğunu belirten bir ileti gönderir. Son olarak, [Azure ıot Tak ve kullan](../iot-pnp/overview-iot-plug-and-play.md)'nin bir parçası olarak, bir geliştirici veri kaynağı ile **dijital ikizi değişiklik olayları** olarak ayarlanmış yollar oluşturabilir IoT Hub ve bir Digital ikizi özelliği ayarlandığında veya değiştirildiğinde, dijital bir ikizi değiştirilirse veya temeldeki cihaz ikizi için bir değişiklik olayı gerçekleştiğinde ileti gönderebilir.

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

IoT Hub, Yönlendirme ve uç noktalarla ilgili olarak, hub 'ınızın ve gönderilen iletilerinizin sistem durumuna ilişkin bir genel bakış sunan çeşitli ölçümler sağlar. İşlevsel kategoriye göre bölünmüş tüm IoT Hub ölçümlerinin bir listesi için bkz. [izleme verileri başvurusunda ölçümler](monitor-iot-hub-reference.md#metrics). Bir yönlendirme sorgusunun değerlendirmesi sırasında oluşan hataları ve [IoT Hub kaynak günlüklerinde **rotalar** kategorisiyle](monitor-iot-hub-reference.md#routes)IoT Hub tarafından algılanan uç nokta durumunu izleyebilirsiniz. IoT Hub ile ölçümleri ve kaynak günlüklerini kullanma hakkında daha fazla bilgi için bkz. [Monitor IoT Hub](monitor-iot-hub.md).

Uç noktaların [sistem durumunu](iot-hub-devguide-endpoints.md#custom-endpoints) almak Için [uç nokta durumu Al](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) REST API kullanabilirsiniz.

Yönlendirme [için sorun giderme kılavuzunu](troubleshoot-message-routing.md) , daha fazla ayrıntı ve sorun giderme için destek için kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* Ileti yollarının nasıl oluşturulacağını öğrenmek için bkz. [yolları kullanarak cihazdan buluta Iletileri işleme IoT Hub](tutorial-routing.md).

* [Cihazdan buluta iletileri gönderme](quickstart-send-telemetry-node.md)

* Cihazdan buluta iletileri göndermek için kullanabileceğiniz SDK 'lar hakkında daha fazla bilgi için bkz. [Azure IoT SDK 'ları](iot-hub-devguide-sdks.md).
