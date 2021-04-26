---
title: Azure Event Grid teslimi ve yeniden dene
description: Azure Event Grid olayların nasıl teslim edildiğini ve teslim edilmemiş iletileri nasıl işlediğini açıklar.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: e24b7540ea1ac41774e2c23781265f9a61940cb1
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276748"
---
# <a name="event-grid-message-delivery-and-retry"></a>İleti teslimini Event Grid ve yeniden deneyin

Bu makalede, teslimin onaylanmaması durumunda Azure Event Grid olayların nasıl işleyeceği açıklanır.

Event Grid dayanıklı teslim sağlar. Her bir abonelik için her iletiyi **en az bir kez** sunar. Olaylar, her aboneliğin kayıtlı uç noktasına hemen gönderilir. Bir uç nokta bir olayın alındığını kabul etmezse, olayın yeniden denenmesini Event Grid.

> [!NOTE]
> Event Grid, olay tesliminin sırasını garanti etmez, bu nedenle aboneler onları sıra dışı alabilir. 

## <a name="batched-event-delivery"></a>Toplu olay teslimi

Her bir olayı abonelere ayrı olarak göndermek Event Grid varsayılan değer. Abone tek bir olaya sahip bir dizi alır. Yüksek işleme senaryolarında iyileştirilmiş HTTP performansına yönelik teslimin gönderilmesi için Event Grid yapılandırabilirsiniz.

Toplu teslimin iki ayarı vardır:

* **Toplu işlem başına en fazla olay** sayısı-Event Grid toplu işlem başına teslim edilir. Bu sayı hiçbir zaman aşılmayacak, ancak yayımlama sırasında başka hiçbir olay yoksa daha az olay teslim edilebilir. Event Grid daha az etkinlik varsa Batch oluşturmak için olayları gecikmez. 1 ile 5.000 arasında olmalıdır.
* Kilobayt cinsinden toplu iş boyutu için **tercih edilen yığın boyutu** (kilobayt cinsinden). En yüksek olaylara benzer şekilde, yayımlama sırasında daha fazla olay yoksa, toplu iş boyutu daha küçük olabilir. Tek bir olay tercih edilen boyuttan daha büyükse bir toplu iş, tercih edilen toplu *iş boyutundan daha* büyük olabilir. Örneğin, tercih edilen boyut 4 KB ise ve 10 KB 'lik bir olay Event Grid 'e itiliyorsa, 10 KB 'lik olay bırakılması yerine kendi toplu işinde de teslim edilir.

Portal, CLı, PowerShell veya SDK 'lar aracılığıyla olay başına abonelik temelinde yapılandırılmış toplu teslim.

### <a name="azure-portal"></a>Azure portal: 
![Toplu teslim ayarları](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure CLI’si
Bir olay aboneliği oluştururken aşağıdaki parametreleri kullanın: 

- **en fazla etkinlik-toplu** işlem-toplu iş içindeki en fazla olay sayısı. 1 ile 5000 arasında bir sayı olmalıdır.
- **tercih edilen-kilobayt** olarak tercih edilen toplu işlem boyutu kilobayt olarak tercih edilir. 1 ile 1024 arasında bir sayı olmalıdır.

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

Azure CLı 'yı Event Grid kullanma hakkında daha fazla bilgi için bkz. [Azure CLI ile Depolama olaylarını Web uç noktasına yönlendirme](../storage/blobs/storage-blob-event-quickstart.md).

## <a name="retry-schedule-and-duration"></a>Zamanlamayı ve süreyi yeniden dene

EventGrid bir olay teslim girişimi için bir hata aldığında, EventGrid teslimatı yeniden denemek mi, olayı teslim etmek mi yoksa olayı hata türüne göre mi bırakmak gerektiğine karar verir. 

Abone olunan uç noktası tarafından döndürülen hata, yeniden denemeler ile çözülebilecek yapılandırmayla ilgili bir hatadır (örneğin, uç nokta silinirse), EventGrid olay üzerinde atılacak işlemleri gerçekleştirir veya atılacak harf yapılandırılmamışsa olayı çözmez.

Aşağıdaki tabloda, yeniden deneme gerçekleşmeyen uç nokta ve hata türleri açıklanmaktadır:

| Uç nokta türü | Hata kodları |
| --------------| -----------|
| Azure Kaynakları | 400 Hatalı Istek, 413 Istek varlığı çok büyük, 403 Yasak | 
| Web Kancası | 400 Hatalı Istek, 413 Istek varlığı çok büyük, 403 Yasak, 404 bulunamadı, 401 yetkilendirilmemiş |
 
> [!NOTE]
> Bir uç nokta için Dead-Letter yapılandırılmamışsa, yukarıdaki hatalar gerçekleştiğinde olaylar bırakılır. Bu tür olayların kesilmesini istemiyorsanız Dead-Letter yapılandırmayı düşünün.

Abone olunan uç nokta tarafından döndürülen hata yukarıdaki listede değilse, EventGrid aşağıda açıklanan ilkeleri kullanarak yeniden denemeyi gerçekleştirir:

Event Grid bir ileti teslim ettikten sonra yanıt için 30 saniye bekler. 30 saniye sonra, uç nokta yanıt vermediyse ileti yeniden denenmek üzere sıraya alınır. Event Grid, olay teslimi için bir üstel geri alma yeniden deneme İlkesi kullanır. En iyi çaba temelinde aşağıdaki zamanlamaya göre teslimi yeniden Event Grid:

- 10 saniye
- 30 saniye
- 1 dakika
- 5 dakika
- 10 dakika
- 30 dakika
- 1 saat
- 3 saat
- 6 saat
- 24 saate kadar her 12 saatte bir


Uç nokta 3 dakika içinde yanıt verirse Event Grid, olayı en iyi çaba temelinde yeniden deneme sırasından kaldırmayı dener, ancak yinelemeler yine de alınabilir.

Event Grid, tüm yeniden deneme adımlarına küçük bir rastgele seçim ekler ve bir uç nokta sürekli sağlıksız, uzun bir süre boyunca azaltılamadığında ya da yoğun bir şekilde göründüğünden belirli yeniden denemeleri atlayabilir mümkün olduğunda olabilir.

Belirleyici davranış için, [abonelik yeniden deneme ilkelerindeki](manage-event-delivery.md)olay yaşam süresi ve en fazla teslimat girişimlerini ayarlayın.

Varsayılan olarak Event Grid, 24 saat içinde teslim edilmeyen tüm olayları sona ermez. Olay aboneliği oluştururken [yeniden deneme ilkesini özelleştirebilirsiniz](manage-event-delivery.md) . Maksimum teslim denemesi sayısı (varsayılan değer 30) ve olay yaşam süresi (varsayılan değer 1440 dakikadır) sağlarsınız.

## <a name="delayed-delivery"></a>Gecikmeli teslim

Bir uç nokta teslim hatalarıyla karşılaşıyorsa, Event Grid bu uç noktada olayların teslimini ve yeniden denenmesini geciktirmeyi dener. Örneğin, bir uç noktada yayımlanan ilk 10 olay başarısız olursa Event Grid, uç noktanın sorun yaşadığını varsayar ve sonraki tüm yeniden denemeleri *ve yeni* teslimler için birkaç saate kadar bir süre sonra geciktirecek olur.

Gecikmeli teslimin işlevsel amacı, sağlıksız uç noktaları ve Event Grid sistemini koruyasağlamaktır. Arka arkaya ve sağlıklı uç noktalara teslim olmadan, Event Grid yeniden deneme ilkesi ve birim özellikleri bir sistemi kolayca açabilir.

## <a name="dead-letter-events"></a>Atılacak mektup olayları
Event Grid belirli bir süre içinde veya olayı belirli bir sayıda teslim etmeye çalıştıktan sonra, teslim edilmemiş olayı bir depolama hesabına gönderebilir. Bu işlem, **atılacak** olarak bilinir. **Aşağıdaki koşullardan biri** karşılandığında bir olay atılacak Event Grid. 

- Etkinlik, **yaşam süresi** içinde teslim edilmemiş. 
- Olayı teslim etmeye yönelik **denemeler sayısı** sınırı aştı.

Koşullardan biri karşılanıyorsa, olay bırakılır veya atılacak.  Event Grid, varsayılan olarak, atılacak bir duruma getirin. Bunu etkinleştirmek için, olay aboneliğini oluştururken teslim edilmemiş olayları barındıracak bir depolama hesabı belirtmeniz gerekir. Teslimleri çözümlemek için bu depolama hesabından olay çekebilirsiniz.

Event Grid, tüm yeniden deneme girişimlerini denediği zaman atılacak harf konumuna bir olay gönderir. Event Grid bir 400 (Hatalı Istek) veya 413 (varlık çok büyük Istek) yanıt kodu alırsa, olayı atılacak şekilde anında zamanlar. Bu yanıt kodları olayın teslimini belirtir hiçbir şekilde başarısız olur.

Yaşam süresi sonu yalnızca bir sonraki zamanlanan teslim denemede denetlenir. Bu nedenle, bir sonraki zamanlanan teslim denemesinden önce yaşam süresi dolsa bile, olay süre sonu yalnızca sonraki teslimat sırasında denetlenir ve ardından atılacak. 

Son bir olay teslim girişimi ve atılacak ileti konumuna teslim edildiğinde oluşan beş dakikalık bir gecikme vardır. Bu gecikme, BLOB depolama işlemlerinin sayısını azaltmaya yöneliktir. Atılacak ileti konumu dört saat kullanılamaz durumdaysa, olay bırakılır.

Atılacak mektup konumunu ayarlamadan önce, kapsayıcısı olan bir depolama hesabınız olmalıdır. Olay aboneliği oluştururken bu kapsayıcı için uç noktayı sağlarsınız. Uç nokta şu biçimdedir: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Atılacak ileti konumuna bir olay gönderildiğinde bildirim almak isteyebilirsiniz. Teslim edilmemiş olaylara yanıt vermek için Event Grid kullanmak için, atılacak ileti blobu depolaması için [bir olay aboneliği oluşturun](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) . Atılacak ileti BLOB depolama alanı teslim edilmemiş bir olay aldığında Event Grid işleyicisine bildirir. İşleyici, teslim edilmemiş olayları uzlaştırmak için almak istediğiniz eylemlerle yanıt verir. Bir atılacak mektup konumu ayarlama ve ilkeleri yeniden deneme hakkında bir örnek için bkz. [atılacak mektup ve yeniden deneme ilkeleri](manage-event-delivery.md).

## <a name="delivery-event-formats"></a>Teslim olayı biçimleri
Bu bölüm, farklı teslim şeması biçimlerinde (Event Grid şeması, CloudEvents 1,0 şeması ve özel şema) olayları ve kullanılmayan olayları örnekler sağlar. Bu biçimler hakkında daha fazla bilgi için bkz. [Event Grid şeması](event-schema.md) ve [bulut olayları 1,0 şema](cloud-event-schema.md) makaleleri. 

### <a name="event-grid-schema"></a>Olay Kılavuz şeması

#### <a name="event"></a>Olay 
```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/000000000-0000-0000-0000-00000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    }
}
```

#### <a name="dead-letter-event"></a>Atılacak mektup olayı

```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    },

    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T17:18:14.0265758Z",
    "lastDeliveryAttemptTime": "2020-08-13T17:18:14.0465788Z" 
}
```

### <a name="cloudevents-10-schema"></a>CloudEvents 1,0 şeması

#### <a name="event"></a>Olay

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    }
}
```

#### <a name="dead-letter-event"></a>Atılacak mektup olayı

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    },

    "deadletterreason": "MaxDeliveryAttemptsExceeded",
    "deliveryattempts": 1,
    "lastdeliveryoutcome": "NotFound",
    "publishtime": "2020-08-13T21:21:36.4018726Z",
}
```

### <a name="custom-schema"></a>Özel şema

#### <a name="event"></a>Olay

```json
{
    "prop1": "my property",
    "prop2": 5,
    "myEventType": "fooEventType"
}

```

#### <a name="dead-letter-event"></a>Atılacak mektup olayı
```json
{
    "id": "8bc07e6f-0885-4729-90e4-7c3f052bd754",
    "eventTime": "2020-08-13T18:11:29.4121391Z",
    "eventType": "myEventType",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/00000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.EventGrid/topics/myCustomSchemaTopic",
    "subject": "subjectDefault",
  
    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T18:11:29.4121391Z",
    "lastDeliveryAttemptTime": "2020-08-13T18:11:29.4277644Z",
  
    "data": {
        "prop1": "my property",
        "prop2": 5,
        "myEventType": "fooEventType"
    }
}
```


## <a name="message-delivery-status"></a>İleti teslimi durumu

Event Grid, olayların alındığını bildirmek için HTTP yanıt kodlarını kullanır. 

### <a name="success-codes"></a>Başarı kodları

Event Grid, **yalnızca** aşağıdaki http yanıt kodlarını başarılı teslimatlar olarak değerlendirir. Diğer tüm durum kodları başarısız teslimler olarak değerlendirilir ve uygun şekilde yeniden denenecek veya devredilecek. Başarılı bir durum kodu aldıktan sonra, teslimi tamamlandı olarak kabul eder Event Grid.

- 200 TAMAM
- 201 oluşturuldu
- 202 kabul edildi
- 203 yetkili olmayan bilgiler
- 204 Içerik yok

### <a name="failure-codes"></a>Hata kodları

Yukarıdaki küme içinde olmayan diğer tüm kodlar (200-204) başarısızlık olarak değerlendirilir ve (gerekirse) yeniden denenir. Bazıları aşağıda özetlenen özel yeniden deneme ilkelerine sahiptir ve tüm diğerleri standart üstel geri ödeme modelini izler. Event Grid mimarisinin mimarisinden kaynaklanan, yeniden deneme davranışının belirleyici olmadığından emin olmak önemlidir. 

| Durum kodu | Yeniden deneme davranışı |
| ------------|----------------|
| 400 Hatalı İstek | Yeniden denenmedi |
| 401 Yetkisiz | Azure kaynakları uç noktaları için 5 dakika veya daha fazla süre sonra yeniden deneyin |
| 403 Yasak | Yeniden denenmedi |
| 404 Bulunamadı | Azure kaynakları uç noktaları için 5 dakika veya daha fazla süre sonra yeniden deneyin |
| 408 İstek Zaman Aşımı | 2 dakika veya daha uzun bir süre sonra yeniden deneyin |
| 413 istek varlığı çok büyük | Yeniden denenmedi |
| 503 Hizmet Kullanılamıyor | 30 saniye veya daha uzun bir süre sonra yeniden deneyin |
| Tüm diğerleri | 10 saniye veya daha fazla süre sonra yeniden deneyin |

## <a name="custom-delivery-properties"></a>Özel teslim özellikleri
Olay abonelikleri, teslim edilen olaylara dahil edilen HTTP üstbilgilerini ayarlamanıza olanak sağlar. Bu özellik, bir hedef için gereken özel üstbilgileri ayarlamanıza olanak sağlar. Bir olay aboneliği oluştururken en fazla 10 üst bilgi ayarlayabilirsiniz. Her üst bilgi değeri 4.096 (4K) bayttan büyük olmamalıdır. Aşağıdaki hedeflere teslim edilen olaylar üzerinde özel üstbilgiler belirleyebilirsiniz:

- Web Kancaları
- Azure Service Bus konuları ve kuyrukları
- Azure Event Hubs
- Geçiş Karma Bağlantılar

Daha fazla bilgi için bkz. [özel teslim özellikleri](delivery-properties.md). 

## <a name="next-steps"></a>Sonraki adımlar

* Olay tesliminin durumunu görüntülemek için bkz. [izleyici Event Grid ileti teslimi](monitor-event-delivery.md).
* Olay teslimi seçeneklerini özelleştirmek için, bkz. [atılacak mektup ve yeniden deneme ilkeleri](manage-event-delivery.md).
