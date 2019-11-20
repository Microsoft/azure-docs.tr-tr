---
title: Azure Blob depolamada akışı değiştirme (Önizleme) | Microsoft Docs
description: Azure Blob depolamada değişiklik akışı günlüklerini ve bunların nasıl kullanılacağını öğrenin.
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: f48c8712a2f4fbd69db7de5247e3293ad57ae1e6
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112829"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>Azure Blob depolamada akış desteğini değiştirme (Önizleme)

Değişiklik akışı amacı, bloblarda oluşan tüm değişikliklerin işlem günlüklerini ve Depolama hesabınızdaki blob meta verilerini sağlamaktır. Değişiklik akışı, bu değişikliklerin **sıralı**, **garantili**, **dayanıklı**, **sabit**ve **salt** yazılır günlüklerini sağlar. İstemci uygulamaları, akış veya toplu işlem modunda bu günlükleri dilediğiniz zaman okuyabilir. Değişiklik akışı, BLOB depolama hesabınızda gerçekleşen değişiklik olaylarını düşük bir maliyetle işleyen etkili ve ölçeklenebilir çözümler oluşturmanıza olanak sağlar.

Değişiklik akışı, standart [BLOB fiyatlandırma](https://azure.microsoft.com/pricing/details/storage/blobs/) maliyetinde Depolama hesabınızdaki özel bir kapsayıcıda [BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) olarak depolanır. Gereksinimlerinize göre bu dosyaların bekletme süresini denetleyebilirsiniz (geçerli yayının [koşullarına](#conditions) bakın). Değişiklik olayları, [Apache avro](https://avro.apache.org/docs/1.8.2/spec.html) biçim belirtiminde kayıt olarak değişiklik akışına eklenir: satır içi şema ile zengin veri yapıları sağlayan kompakt, hızlı, ikili bir biçimdir. Bu biçim, Hadoop ekosistemi, Stream Analytics ve Azure Data Factory yaygın olarak kullanılır.

Bu günlükleri zaman uyumsuz, artımlı veya tam olarak işleyebilirsiniz. Herhangi bir sayıda istemci uygulaması değişiklik akışını paralel olarak ve kendi hızda okuyabilir. [Apache detaya gitme](https://drill.apache.org/docs/querying-avro-files/) veya [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) gibi analitik uygulamalar, günlükleri doğrudan avro dosyaları olarak tüketebilir, bu da bunları yüksek bant genişliğine sahip ve özel bir uygulama yazmak zorunda kalmadan düşük maliyetli bir şekilde işlemenizi sağlar.

Değişiklik akışı desteği, değiştirilen nesnelere göre verileri işleyen senaryolar için uygundur. Örneğin, uygulamalar şunları yapabilir:

  - İkincil bir dizini güncelleştirin, önbellek, arama motoru veya diğer içerik yönetimi senaryolarıyla eşitler.
  
  - Bir akış veya toplu modda nesnelerinize gerçekleşen değişikliklere göre iş analizi öngörülerini ve ölçümlerini ayıklayın.
  
  - Şirket veri yönetimi için güvenlik, uyumluluk veya zeka için, nesnelerdeki değişiklikleri istediğiniz zaman boyunca depolayın, denetleyin ve çözümleyin.

  - Olağanüstü durum yönetimi veya uyumluluk için hesabınızdaki nesne durumunu yedeklemeye, yansıtmaya veya çoğaltmaya yönelik çözümler oluşturun.

  - Oluşturulan veya değiştirilen nesneye göre değişiklik olaylarına tepki veren veya yürütmeleri zamanlamaya dayalı bağlantılı uygulama işlem hatları oluşturun.

> [!NOTE]
> [BLOB depolama olayları](storage-blob-event-overview.md) , Azure işlevlerinizin veya uygulamalarınızın blob 'da oluşan değişikliklere tepki vermesini sağlayan gerçek zamanlı bir kerelik olaylar sağlar. Değişiklik akışı, değişikliklerin dayanıklı ve sıralı bir günlük modelini sağlar. Değişiklik akışınızdaki değişiklikler, değişiklik akışınızda, değişikliğin birkaç dakikasında bir sıra içinde kullanılabilir hale getirilir. Uygulamanızın olaylara çok daha hızlı yanıt vermesini istiyorsanız, bunun yerine [BLOB Storage olaylarını](storage-blob-event-overview.md) kullanmayı göz önünde bulundurun. BLOB depolama olayları, Azure Işlevlerinizin veya uygulamalarınızın bağımsız olayları gerçek zamanlı olarak tepki vermesini sağlar.

## <a name="enable-and-disable-the-change-feed"></a>Değişiklik akışını etkinleştirme ve devre dışı bırakma

Değişiklikleri yakalamaya başlamak için depolama hesabınızda değişiklik akışını etkinleştirmeniz gerekir. Değişiklikleri yakalamayı durdurmak için değişiklik akışını devre dışı bırakın. Portalda veya PowerShell 'de Azure Resource Manager şablonlarını kullanarak değişiklikleri etkinleştirebilir ve devre dışı bırakabilirsiniz.

Değişiklik akışını etkinleştirdiğinizde göz önünde bulundurmanız gereken birkaç nokta aşağıda verilmiştir.

- **$Blobchangefeed** kapsayıcısında depolanan her bir depolama hesabında blob hizmeti için yalnızca bir değişiklik akışı vardır.

- Değişiklikler yalnızca blob hizmeti düzeyinde yakalanır.

- Değişiklik akışı, hesapta oluşan tüm kullanılabilir olaylar için *Tüm* değişiklikleri yakalar. İstemci uygulamaları, gereken şekilde olay türlerini filtreleyebilir. (Geçerli yayının [koşullarına](#conditions) bakın).

- Yalnızca GPv2 ve BLOB depolama hesapları değişiklik akışını etkinleştirebilir. GPv1 depolama hesapları, Premium blok Blobstorage hesapları ve hiyerarşik ad alanı etkinleştirilmiş hesaplar Şu anda desteklenmemektedir.

> [!IMPORTANT]
> Değişiklik akışı genel önizlemededir ve **westcentralus** ve **westus2** bölgelerinde kullanılabilir. Bu makalenin [koşullar](#conditions) bölümüne bakın. Önizlemeye kaydolmak için bu makalenin [aboneliğinizi kaydetme](#register) bölümüne bakın. Depolama hesaplarınızda değişiklik akışını etkinleştirebilmeniz için aboneliğinizi kaydetmeniz gerekir.

### <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak şablonu dağıtmak için:

1. Azure portal **kaynak oluştur**' u seçin.

2. **Market içinde ara** alanına **şablon dağıtımı** yazın ve **ENTER** tuşuna basın.

3. **Şablon dağıtımı**öğesini seçin, **Oluştur**' u seçin ve ardından **düzenleyicide kendi şablonunuzu oluştur**' u seçin.

4. Şablon Düzenleyicisi 'nde aşağıdaki JSON öğesine yapıştırın. `<accountName>` yer tutucusunu depolama hesabınızın adıyla değiştirin.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts/blobServices",
           "apiVersion": "2019-04-01",
           "name": "<accountName>/default",
           "properties": {
               "changeFeed": {
                   "enabled": true
               }
           } 
        }]
   }
   ```
    
5. **Kaydet** düğmesini seçin, hesabın kaynak grubunu belirtin ve ardından şablonu dağıtmak ve değişiklik akışını etkinleştirmek Için **satın al** düğmesini seçin.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak şablonu dağıtmak için:

1. En son PowershellGet 'i yükler.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. ' I kapatın ve ardından PowerShell konsolunu yeniden açın.

3. **Az. Storage** Preview modülünü yükler.

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. Azure aboneliğinizde oturum açın `Connect-AzAccount` izleyin ve komut ekrandaki kimlik doğrulaması yapın.

   ```powershell
   Connect-AzAccount
   ```

5. Depolama hesabınızın değişiklik akışını etkinleştirin.

   ```powershell
   Update-AzStorageBlobServiceProperty -ResourceGroupName -StorageAccountName -EnableChangeFeed $true
   ```

---

## <a name="understand-change-feed-organization"></a>Değişiklik akışı organizasyonunu anlama

<a id="segment-index"></a>

### <a name="segments"></a>Kesimler

Değişiklik akışı, **saatlik** *kesimlerde* düzenlenen, ancak birkaç dakikada bir eklenen ve güncellenen bir değişiklik günlüğü olur. Bu segmentler yalnızca o saat içinde oluşan blob değişiklik olayları olduğunda oluşturulur. Bu, istemci uygulamanızın tüm günlük içinde arama yapmak zorunda kalmadan belirli zaman aralıklarında oluşan değişiklikleri kullanmasını sağlar. Daha fazla bilgi edinmek için bkz. [Özellikler](#specifications).

Değişiklik akışına ait kullanılabilir saatlik bir kesim, bu segmentin değişiklik akışı dosyalarının yollarını belirten bir bildirim dosyasında açıklanmıştır. `$blobchangefeed/idx/segments/` sanal dizin listesi, bu segmentleri zamana göre sıralanmış olarak gösterir. Segmentin yolu, segmentin gösterdiği saatlik zaman aralığının başlangıcını açıklar. Sizi ilgilendiren günlüklerin segmentlerini filtrelemek için bu listeyi kullanabilirsiniz.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> Değişiklik akışını etkinleştirdiğinizde `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` otomatik olarak oluşturulur. Bu dosyayı güvenle yoksayabilirsiniz. Her zaman boş bir başlatma dosyasıdır. 

Segment bildirim dosyası (`meta.json`), `chunkFilePaths` özelliğindeki bu segmentin değişiklik akışı dosyalarının yolunu gösterir. Segment bildirim dosyasına bir örnek aşağıda verilmiştir.

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> Depolama hesabınızdaki kapsayıcıları listelediyseniz, `$blobchangefeed` kapsayıcısı yalnızca hesabınızdaki akışı Değiştir özelliğini etkinleştirdikten sonra görünür. Kapsayıcıyı görebilmeniz için önce değişiklik akışını etkinleştirdikten sonra birkaç dakika beklemeniz gerekir.

<a id="log-files"></a>

### <a name="change-event-records"></a>Olay kayıtlarını değiştirme

Değişiklik akışı dosyaları bir dizi değişiklik olay kaydını içerir. Her değişiklik olay kaydı, tek bir Blobun bir değişikliğe karşılık gelir. Kayıtlar serileştirilir ve [Apache avro](https://avro.apache.org/docs/1.8.2/spec.html) biçim belirtimi kullanılarak dosyaya yazılır. Kayıtlar avro dosya biçimi belirtimi kullanılarak okunabilir. Bu biçimdeki dosyaları işlemek için kullanabileceğiniz çeşitli kitaplıklar vardır.

Değişiklik akışı dosyaları `$blobchangefeed/log/` sanal dizininde, [ekleme Blobları](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)olarak depolanır. Her yolun altındaki ilk değişiklik akış dosyası `00000` dosya adında olur (örneğin `00000.avro`). Bu yola eklenen her bir sonraki günlük dosyasının adı 1 ile artacaktır (örneğin: `00001.avro`).

Değişiklik akışı dosyasından JSON 'a dönüştürülmüş değişiklik olay kaydı örneği aşağıda verilmiştir.

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}
```

Her bir özelliğin açıklaması için bkz. [BLOB depolama için Azure Event Grid olay şeması](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties).

> [!NOTE]
> Bir kesim için değişiklik akışı dosyaları bir segment oluşturulduktan sonra hemen görünmez. Gecikme süresi, değişikliğin birkaç dakika içinde olan değişiklik akışında bulunan normal Yayımlanma zaman aralığı içindedir.

## <a name="consume-the-change-feed"></a>Değişiklik akışını tüketme

Değişiklik akışı birkaç meta veri ve günlük dosyası üretir. Bu dosyalar, depolama hesabının **$blobchangefeed** kapsayıcısında bulunur. 

> [!NOTE]
> Geçerli yayında **$blobchangefeed** kapsayıcısı Azure Depolama Gezgini veya Azure Portal görünür değil. Şu anda ListContainers API 'sini çağırdığınızda $blobchangefeed kapsayıcısını göremezsiniz, ancak Blobları görmek için Listblobları API 'sini doğrudan kapsayıcıda çağırabilirsiniz.

İstemci uygulamalarınız, değişiklik akışı işlemcisi SDK 'Sı ile birlikte sunulan blob değişiklik akışı işlemci kitaplığını kullanarak değişiklik akışını kullanabilir. 

Bkz. [Azure Blob depolamada işlem değişiklik akışı günlükleri](storage-blob-change-feed-how-to.md).

<a id="specifications"></a>

## <a name="specifications"></a>Belirtimler

- Değişiklik olayları kayıtları yalnızca değişiklik akışına eklenir. Bu kayıtlar eklendikten sonra sabit ve kayıt konumu kararlı olur. İstemci uygulamaları, değişiklik akışındaki okuma konumunda kendi denetim noktalarını koruyabilir.

- Değişiklik olay kayıtları, değişikliğin birkaç dakikalık bir sırası içine eklenir. İstemci uygulamaları, akış erişimi veya toplu olarak herhangi bir zamanda eklendiği için kayıtları tüketmek üzere seçim yapabilir.

- Değişiklik olay kayıtları, **BLOB başına**değişiklik sırasına göre sıralanır. Blob 'larda değişiklik sırası, Azure Blob depolamada tanımsızdır. Önceki kesimdeki tüm değişiklikler sonraki kesimlerdeki değişikliklerden önceye ait.

- Değişiklik olay kayıtları, [Apache avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html) biçim belirtimi kullanılarak günlük dosyasına serileştirilir.

- `eventType` bir `Control` değeri olan olay kayıtlarını değiştirin, iç sistem kayıtlardır ve hesabınızdaki nesnelerde yapılan değişiklikleri yansıtmaz. Bu kayıtları güvenle yoksayabilirsiniz.

- `storageDiagnonstics` Özellik çantasındaki değerler yalnızca dahili kullanım içindir ve uygulamanız tarafından kullanılmak üzere tasarlanmamıştır. Uygulamalarınızın bu verilere yönelik bir sözleşme bağımlılığı olması gerekmez. Bu özellikleri güvenle yoksayabilirsiniz.

- Segment tarafından temsil edilen zaman, 15 dakikalık sınırlara göre **yaklaşık** olur. Bu nedenle, belirli bir süre içinde tüm kayıtların tüketimini sağlamak için, ardışık önceki ve sonraki saat segmentini kullanın.

- Her segment farklı sayıda `chunkFilePaths`olabilir. Bunun nedeni, yayımlama aktarım hızını yönetmek için günlük akışının dahili bölümleniyor. Her bir `chunkFilePath` günlük dosyalarının birbirini dışlayan blob 'ları içermesi garanti edilir ve yineleme sırasında blob başına değişiklik sıralamasını ihlal etmeden paralel olarak tüketilebilir ve işlenebilir.

- Segmentlerin `Publishing` durumu başlangıcı. Kayıtların segmente eklenmesi tamamlandıktan sonra `Finalized`olur. `$blobchangefeed/meta/Segments.json` dosyasındaki `LastConsumable` özelliğinin tarihinden sonra tarihli tüm kesimlerdeki günlük dosyaları, uygulamanız tarafından tüketilmemelidir. Bir `$blobchangefeed/meta/Segments.json` dosyasında `LastConsumable`özelliğine bir örnek aşağıda verilmiştir:

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="register"></a>

## <a name="register-your-subscription-preview"></a>Aboneliğinizi kaydetme (Önizleme)

Değişiklik akışı yalnızca genel önizlemede olduğundan, özelliği kullanmak için aboneliğinizi kaydetmeniz gerekir.

### <a name="register-by-using-powershell"></a>PowerShell kullanarak kaydetme

Bir PowerShell konsolunda şu komutları çalıştırın:

```powershell
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
   
### <a name="register-by-using-azure-cli"></a>Azure CLı kullanarak kaydolun

Azure Cloud Shell, şu komutları çalıştırın:

```cli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>Koşullar ve bilinen sorunlar (Önizleme)

Bu bölümde, değişiklik akışında geçerli genel önizlemede bulunan bilinen sorunlar ve koşullar açıklanmaktadır.
- Önizleme için, westcentralus veya westus2 bölgelerinde depolama hesabınızın değişiklik akışını etkinleştirebilmeniz için önce [aboneliğinizi kaydetmeniz](#register) gerekir. 
- Değişiklik akışı yalnızca oluşturma, güncelleştirme, silme ve kopyalama işlemlerini yakalar. Meta veri güncelleştirmeleri Şu anda önizlemede yakalanmıyor.
- Değişiklik akışınızda tek bir değişikliğin değişiklik olay kayıtları birden çok kez görüntülenebilir.
- Bunlara zaman tabanlı bekletme ilkesi ayarlayarak değişiklik akışı günlük dosyalarının yaşam süresini henüz yönetemezsiniz.
- Günlük dosyasının `url` özelliği her zaman boştur.
- Segment. json dosyasının `LastConsumable` özelliği, değişiklik akışı 'nın sonlandırabildiği ilk parçayı listelemez. Bu sorun yalnızca ilk kesim sonlandırıldıktan sonra oluşur. İlk saat sonrasındaki sonraki tüm segmentler `LastConsumable` özelliğinde doğru bir şekilde yakalanır.

## <a name="faq"></a>SSS

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Değişiklik akışı ve Depolama Analizi günlüğe kaydetme arasındaki fark nedir?
Değişiklik akışı, değişiklik akışı günlüğüne yalnızca başarılı blob oluşturma, değiştirme ve silme olayları kaydedildiğinde uygulama geliştirme için en iyi duruma getirilmiştir. Analiz günlüğü, okuma ve listeleme işlemleri de dahil olmak üzere tüm işlemlerde tüm başarılı ve başarısız istekleri kaydeder. Değişiklik akışından yararlanarak, bir işlem ağır hesabında günlük gürültüsünü filtreleme ve yalnızca blob değişiklik olaylarına odaklanma konusunda endişelenmeniz gerekmez.

### <a name="should-i-use-change-feed-or-storage-events"></a>Değişiklik akışı veya depolama olaylarını kullanmalıyım mi?
Hem değişiklik akışı hem de [BLOB depolama olayları](storage-blob-event-overview.md) , olay kayıtlarının gecikmesi, sıralaması ve depolanması ile ilgili önemli bir farklılık ile aynı şekilde her iki özellikten da yararlanabilirsiniz. Değişiklik akışı, blob değişiklik işlemlerinin sırasını garanti ederken her birkaç dakikada bir değişiklik akışı günlüğüne kayıt yazar. Depolama olayları gerçek zamanlı olarak gönderilir ve sıralanmayabilir. Depolama olayları geçicidir ve olay işleyicisi tarafından açık bir şekilde depolanmadığı sürece, akış olayları değişiklik, depolama hesabınız içinde silinir.

## <a name="next-steps"></a>Sonraki adımlar

- .NET istemci uygulaması kullanarak değişiklik akışını okuma hakkında bir örnek için bkz. Bkz. [Azure Blob depolamada işlem değişiklik akışı günlükleri](storage-blob-change-feed-how-to.md).
- Olaylara gerçek zamanlı olarak tepki verme hakkında bilgi edinin. Bkz. [BLOB depolama olaylarına yeniden davranma](storage-blob-event-overview.md)
- Tüm istekler için hem başarılı hem de başarısız işlemler için ayrıntılı günlük bilgileri hakkında daha fazla bilgi edinin. Bkz. [Azure Storage Analytics günlüğe kaydetme](../common/storage-analytics-logging.md)
