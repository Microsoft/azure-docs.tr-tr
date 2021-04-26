---
title: Azure Blob depolamada akışı değiştirme | Microsoft Docs
description: Azure Blob depolamada değişiklik akışı günlüklerini ve bunların nasıl kullanılacağını öğrenin.
author: normesta
ms.author: normesta
ms.date: 02/08/2021
ms.topic: how-to
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 6da83ceb6d8ee51916d25949309d7ddfba0e4b30
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503612"
---
# <a name="change-feed-support-in-azure-blob-storage"></a>Azure Blob depolamada akış desteğini değiştirme

Değişiklik akışı amacı, bloblarda oluşan tüm değişikliklerin işlem günlüklerini ve Depolama hesabınızdaki blob meta verilerini sağlamaktır. Değişiklik akışı, bu değişikliklerin **sıralı**, **garantili**, **dayanıklı**, **sabit** ve **salt** yazılır günlüklerini sağlar. İstemci uygulamaları, akış veya toplu işlem modunda bu günlükleri dilediğiniz zaman okuyabilir. Değişiklik akışı, BLOB depolama hesabınızda gerçekleşen değişiklik olaylarını düşük bir maliyetle işleyen etkili ve ölçeklenebilir çözümler oluşturmanıza olanak sağlar.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-the-change-feed-works"></a>Değişiklik akışı nasıl kullanılır?

Değişiklik akışı, standart [BLOB fiyatlandırma](https://azure.microsoft.com/pricing/details/storage/blobs/) maliyetinde Depolama hesabınızdaki özel bir kapsayıcıda [BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) olarak depolanır. Gereksinimlerinize göre bu dosyaların bekletme süresini denetleyebilirsiniz (geçerli yayının [koşullarına](#conditions) bakın). Değişiklik olayları, [Apache avro](https://avro.apache.org/docs/1.8.2/spec.html) biçim belirtiminde kayıt olarak değişiklik akışına eklenir: satır içi şema ile zengin veri yapıları sağlayan kompakt, hızlı, ikili bir biçimdir. Bu biçim, Hadoop ekosisteminde, Stream Analytics ve Azure Data Factory yaygın olarak kullanılır.

Bu günlükleri zaman uyumsuz, artımlı veya tam olarak işleyebilirsiniz. Herhangi bir sayıda istemci uygulaması değişiklik akışını paralel olarak ve kendi hızda okuyabilir. [Apache detaya gitme](https://drill.apache.org/docs/querying-avro-files/) veya [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) gibi analitik uygulamalar, günlükleri doğrudan avro dosyaları olarak tüketebilir, bu da bunları yüksek bant genişliğine sahip ve özel bir uygulama yazmak zorunda kalmadan düşük maliyetli bir şekilde işlemenizi sağlar.

Aşağıdaki diyagramda kayıtların değişiklik akışına nasıl eklendiği gösterilmektedir:

:::image type="content" source="media/storage-blob-change-feed/change-feed-diagram.png" alt-text="Değişiklik beslemenin bloblarda sıralı değişiklik günlüğü sağlamak için nasıl çalıştığını gösteren diyagram":::

Değişiklik akışı desteği, değiştirilen nesnelere göre verileri işleyen senaryolar için uygundur. Örneğin, uygulamalar şunları yapabilir:

  - İkincil dizini güncelleştirme, önbellek ile eşitleme, arama motoru veya diğer içerik yönetim senaryoları.
  
  - Akış şeklinde veya toplu iş modunda nesnelerinize yapılan değişikliklere göre iş analizi içgörülerini ve ölçümlerini ayıklama.
  
  - Kurumsal veri yönetimi için güvenlik, uyumluluk veya iş zekası amacıyla nesnelerinizde zaman içinde gerçekleşen değişiklikleri depolama, denetleme ve analiz etme.

  - Olağanüstü durum yönetimi veya uyumluluk için hesabınızdaki nesne durumunu yedeklemeye, yansıtmaya veya çoğaltmaya yönelik çözümler oluşturun.

  - Oluşturulan veya değiştirilen nesneye göre değişiklik olaylarına tepki veren veya yürütmeleri zamanlamaya dayalı bağlantılı uygulama işlem hatları oluşturun.
  
Değişiklik akışı, [nesne çoğaltma](object-replication-overview.md) ve [blok Blobları için noktadan noktaya geri yükleme](point-in-time-restore-overview.md)için bir önkoşul özelliğidir.

> [!NOTE]
> Değişiklik akışı, blob 'a gerçekleşen değişikliklerin dayanıklı ve sıralı bir günlük modelini sağlar. Değişiklikler yazılır ve değişiklik akışı günlüğünde değişiklik için birkaç dakika içinde kullanılabilir hale getirilir. Uygulamanızın olaylara çok daha hızlı yanıt vermesini istiyorsanız, bunun yerine [BLOB Storage olaylarını](storage-blob-event-overview.md) kullanmayı göz önünde bulundurun. [BLOB depolama olayları](storage-blob-event-overview.md) , Azure işlevlerinizin veya uygulamalarınızın blob 'da oluşan değişikliklere hızlı bir şekilde tepki vermesini sağlayan gerçek zamanlı bir kerelik olaylar sağlar. 

## <a name="enable-and-disable-the-change-feed"></a>Değişiklik akışını etkinleştirme ve devre dışı bırakma

Değişiklikleri yakalamaya ve kaydetmeye başlamak için depolama hesabınızda değişiklik akışını etkinleştirmeniz gerekir. Değişiklikleri yakalamayı durdurmak için değişiklik akışını devre dışı bırakın. Portalda veya PowerShell 'de Azure Resource Manager şablonlarını kullanarak değişiklikleri etkinleştirebilir ve devre dışı bırakabilirsiniz.

Değişiklik akışını etkinleştirdiğinizde göz önünde bulundurmanız gereken birkaç nokta aşağıda verilmiştir.

- Her depolama hesabındaki blob hizmeti için yalnızca bir değişiklik akışı vardır ve **$blobchangefeed** kapsayıcısında depolanır.

- Oluşturma, güncelleştirme ve silme değişiklikleri yalnızca blob hizmeti düzeyinde yakalanır.

- Değişiklik akışı, hesapta oluşan tüm kullanılabilir olaylar için *Tüm* değişiklikleri yakalar. İstemci uygulamaları, gereken şekilde olay türlerini filtreleyebilir. (Geçerli yayının [koşullarına](#conditions) bakın).

- Yalnızca GPv2 ve BLOB depolama hesapları değişiklik akışını etkinleştirebilir. Premium blok Blobstorage hesapları ve hiyerarşik ad alanı etkinleştirilmiş hesaplar Şu anda desteklenmemektedir. GPv1 depolama hesapları desteklenmez, ancak kapalı kalma süresi olmadan GPv2 sürümüne yükseltilebilir. daha fazla bilgi için bkz. [GPv2 Storage hesabına yükseltme](../common/storage-account-upgrade.md) .

### <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak depolama hesabınızda değişiklik akışını etkinleştirin:

1. [Azure Portal](https://portal.azure.com/)depolama hesabınızı seçin.
1. **Veri yönetimi** altında **veri koruma** seçeneğine gidin.
1. **İzleme** altında **BLOB değişiklik akışını etkinleştir**' i seçin.
1. Veri koruma ayarlarınızı onaylamak için **Kaydet** düğmesini seçin.

    :::image type="content" source="media/storage-blob-change-feed/change-feed-enable-portal.png" alt-text="Azure portal değişiklik akışını etkinleştirmeyi gösteren ekran görüntüsü":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak akışı değiştirme özelliğini etkinleştir:

1. En son PowershellGet 'i yükler.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. ' I kapatın ve ardından PowerShell konsolunu yeniden açın.

3. **Az. Storage** modülünün sürüm 2.5.0 veya üstünü yükler.

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 2.5.0 –AllowClobber –Force
   ```

4. Komutuyla Azure aboneliğinizde oturum açın `Connect-AzAccount` ve kimlik doğrulaması yapmak için ekrandaki yönergeleri izleyin.

   ```powershell
   Connect-AzAccount
   ```

5. Depolama hesabınızın değişiklik akışını etkinleştirin.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[Şablon](#tab/template)
Azure portal aracılığıyla mevcut depolama hesabınızda değişiklik akışını etkinleştirmek için bir Azure Resource Manager şablonu kullanın:

1. Azure portal **kaynak oluştur**' u seçin.

2. **Market 'Te ara**' te, **şablon dağıtımı** yazın ve ardından **ENTER** tuşuna basın.

3. **[Özel bir şablon dağıt](https://portal.azure.com/#create/Microsoft.Template)**' ı seçin ve ardından **düzenleyicide kendi şablonunuzu oluştur**' u seçin.

4. Şablon Düzenleyicisi 'nde aşağıdaki JSON öğesine yapıştırın. `<accountName>`Yer tutucusunu depolama hesabınızın adıyla değiştirin.

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

---

## <a name="consume-the-change-feed"></a>Değişiklik akışını tüketme

Değişiklik akışı birkaç meta veri ve günlük dosyası üretir. Bu dosyalar, depolama hesabının **$blobchangefeed** kapsayıcısında bulunur. 

> [!NOTE]
> Geçerli yayında $blobchangefeed kapsayıcısı yalnızca Azure portal görünür ancak Azure Depolama Gezgini görünmez. Şu anda ListContainers API 'sini çağırdığınızda $blobchangefeed kapsayıcısını göremez, ancak Blobları görmek için Listblobları API 'sini doğrudan kapsayıcıda çağırabilirsiniz

İstemci uygulamalarınız, değişiklik akışı işlemcisi SDK 'Sı ile birlikte sunulan blob değişiklik akışı işlemci kitaplığını kullanarak değişiklik akışını kullanabilir. 

Bkz. [Azure Blob depolamada işlem değişiklik akışı günlükleri](storage-blob-change-feed-how-to.md).

## <a name="understand-change-feed-organization"></a>Değişiklik akışı organizasyonunu anlama

<a id="segment-index"></a>

### <a name="segments"></a>Kesimler

Değişiklik akışı, **saatlik** *kesimlerde* düzenlenmiş, ancak birkaç dakikada bir eklenen ve güncellenen bir değişiklik günlüğü olur. Bu segmentler yalnızca o saat içinde oluşan blob değişiklik olayları olduğunda oluşturulur. Bu, istemci uygulamanızın tüm günlük içinde arama yapmak zorunda kalmadan belirli zaman aralıklarında oluşan değişiklikleri kullanmasını sağlar. Daha fazla bilgi edinmek için bkz. [Özellikler](#specifications).

Değişiklik akışına ait kullanılabilir saatlik bir kesim, bu segmentin değişiklik akışı dosyalarının yollarını belirten bir bildirim dosyasında açıklanmıştır. `$blobchangefeed/idx/segments/`Sanal dizin listesi bu segmentleri zamana göre sıralanmış olarak gösterir. Segmentin yolu, segmentin gösterdiği saatlik zaman aralığının başlangıcını açıklar. Sizi ilgilendiren günlüklerin segmentlerini filtrelemek için bu listeyi kullanabilirsiniz.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> , `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` Değişiklik akışını etkinleştirdiğinizde otomatik olarak oluşturulur. Bu dosyayı güvenle yoksayabilirsiniz. Her zaman boş bir başlatma dosyasıdır. 

Segment bildirim dosyası ( `meta.json` ), özelliğindeki bu segmentin değişiklik akışı dosyalarının yolunu gösterir `chunkFilePaths` . Segment bildirim dosyasına bir örnek aşağıda verilmiştir.

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
> `$blobchangefeed`Kapsayıcı yalnızca hesabınızda değişiklik akışı özelliğini etkinleştirdikten sonra görünür. Kapsayıcıda blob 'ları listelebilmeniz için önce değişiklik akışını etkinleştirdikten sonra birkaç dakika beklemeniz gerekir. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Olay kayıtlarını değiştirme

Değişiklik akışı dosyaları bir dizi değişiklik olay kaydını içerir. Her değişiklik olay kaydı, tek bir Blobun bir değişikliğe karşılık gelir. Kayıtlar serileştirilir ve [Apache avro](https://avro.apache.org/docs/1.8.2/spec.html) biçim belirtimi kullanılarak dosyaya yazılır. Kayıtlar avro dosya biçimi belirtimi kullanılarak okunabilir. Bu biçimdeki dosyaları işlemek için kullanabileceğiniz çeşitli kitaplıklar vardır.

Değişiklik akışı dosyaları, `$blobchangefeed/log/` sanal dizinde [ekleme Blobları](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)olarak depolanır. Her yolun altındaki ilk değişiklik akışı dosyası `00000` dosya adında olacaktır (örneğin `00000.avro` ). Bu yola eklenen her bir sonraki günlük dosyasının adı 1 ile artacaktır (örneğin: `00001.avro` ).

Aşağıdaki olay türleri değişiklik akışı kayıtlarında yakalanır:
- BlobCreated
- BlobDeleted
- BlobPropertiesUpdated
- Blobsnapshotoluşturuldu

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

Her bir özelliğin açıklaması için bkz. [BLOB depolama için Azure Event Grid olay şeması](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties). BlobPropertiesUpdated ve BlobSnapshotCreated olayları şu anda akışı değiştirebilir ve BLOB depolama olayları için henüz desteklenmemiştir.

> [!NOTE]
> Bir kesim için değişiklik akışı dosyaları bir segment oluşturulduktan sonra hemen görünmez. Gecikme süresi, değişikliğin birkaç dakika içinde olan değişiklik akışında bulunan normal Yayımlanma zaman aralığı içindedir.

<a id="specifications"></a>

## <a name="specifications"></a>Belirtimler

- Değişiklik olayları kayıtları yalnızca değişiklik akışına eklenir. Bu kayıtlar eklendikten sonra sabit ve kayıt konumu kararlı olur. İstemci uygulamaları, değişiklik akışındaki okuma konumunda kendi denetim noktalarını koruyabilir.

- Değişiklik olay kayıtları, değişikliğin birkaç dakikalık bir sırası içine eklenir. İstemci uygulamaları, akış erişimi veya toplu olarak herhangi bir zamanda eklendiği için kayıtları tüketmek üzere seçim yapabilir.

- Değişiklik olay kayıtları, **BLOB başına** değişiklik sırasına göre sıralanır. Blob 'larda değişiklik sırası, Azure Blob depolamada tanımsızdır. Önceki kesimdeki tüm değişiklikler sonraki kesimlerdeki değişikliklerden önceye ait.

- Değişiklik olay kayıtları, [Apache avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html) biçim belirtimi kullanılarak günlük dosyasına serileştirilir.

- Öğesinin değeri olan olay kayıtlarını değiştirme, `eventType` `Control` iç sistem kayıtlardır ve hesabınızdaki nesnelerde yapılan değişiklikleri yansıtmaz. Bu kayıtları güvenle yoksayabilirsiniz.

- `storageDiagnostics`Özellik çantasındaki değerler yalnızca dahili kullanım içindir ve uygulamanız tarafından kullanılmak üzere tasarlanmamıştır. Uygulamalarınızın bu verilere yönelik bir sözleşme bağımlılığı olması gerekmez. Bu özellikleri güvenle yoksayabilirsiniz.

- Segment tarafından temsil edilen zaman, 15 dakikalık sınırlara göre **yaklaşık** olur. Bu nedenle, belirli bir süre içinde tüm kayıtların tüketimini sağlamak için, ardışık önceki ve sonraki saat segmentini kullanın.

- Her segment `chunkFilePaths` , yayımlama aktarım hızını yönetmek için günlük akışının iç bölümlenmesi nedeniyle farklı bir sayıda olabilir. Her birinde bulunan günlük dosyalarının `chunkFilePath` birbirini dışlayan blob 'lar içermesi garanti edilir ve yineleme sırasında blob başına değişiklik sıralamasını ihlal etmeden paralel olarak tüketilebilir ve işlenebilir.

- Kesimlerde durum başlangıcı vardır `Publishing` . Kayıtların segmente eklenmesi tamamlandıktan sonra, olacaktır `Finalized` . Dosyadaki özelliğin tarihinden sonra tarihli tüm kesimlerdeki günlük dosyaları `LastConsumable` `$blobchangefeed/meta/Segments.json` , uygulamanız tarafından tüketilmemelidir. İşte `LastConsumable` bir dosyadaki özelliğe bir örnek `$blobchangefeed/meta/Segments.json` :

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

<a id="conditions"></a>

## <a name="conditions-and-known-issues"></a>Koşullar ve bilinen sorunlar

Bu bölümde, değişiklik beslemenin geçerli sürümündeki bilinen sorunlar ve koşullar açıklanmaktadır. 

- Değişiklik akışınızda tek bir değişikliğin değişiklik olay kayıtları birden çok kez görüntülenebilir.
- Bunlara zaman tabanlı bekletme ilkesi ayarlayarak değişiklik akışı günlük dosyalarının yaşam süresini henüz yönetemezsiniz ve Blobları silemezsiniz.
- `url`Günlük dosyasının özelliği şu anda her zaman boştur.
- `LastConsumable`Dosyadaki segments.jsözelliği, değişiklik akışı 'nın sonlandıraldığı ilk parçayı listelemez. Bu sorun yalnızca ilk kesim sonlandırıldıktan sonra oluşur. İlk saatin ardından gelen tüm bölümler, özellikte doğru bir şekilde yakalanır `LastConsumable` .
- Şu anda ListContainers API 'sini çağırdığınızda **$blobchangefeed** kapsayıcısını göremez ve kapsayıcı Azure portal veya Depolama Gezgini gösterilmez. Listblobları API 'sini doğrudan $blobchangefeed kapsayıcısında çağırarak içeriği görüntüleyebilirsiniz.
- Daha önce bir [Hesap yük devretmesini](../common/storage-disaster-recovery-guidance.md) Başlatan depolama hesaplarında, günlük dosyası görünmediğinden ilgili sorunlar olabilir. Gelecekteki tüm hesap yük devretme işlemleri günlük dosyasını da etkileyebilir.

## <a name="faq"></a>SSS

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Değişiklik akışı ve Depolama Analizi günlüğe kaydetme arasındaki fark nedir?
Analiz günlüklerinde tüm işlemlerde başarılı ve başarısız isteklerle tüm okuma, yazma, listeleme ve silme işlemlerinin kayıtları vardır. Analiz günlükleri en iyi çabadır ve hiçbir sıralama garanti edilmez.

Değişiklik akışı, başarılı mutasyonların veya hesabınızda blob oluşturma, değiştirme ve silme gibi değişiklikler için işlem günlüğü sağlayan bir çözümdür. Değişiklik akışı, tüm olayların blob başına başarılı değişiklikler sırasına göre kaydedilmesini ve görüntülenmesini sağlar; bu nedenle, çok büyük hacimli okuma işlemleri veya başarısız istekler için paraziti filtrelemeniz gerekmez. Değişiklik akışı temel olarak tasarlanır ve belirli garanti gerektiren uygulama geliştirmesi için iyileştirilmiştir.

### <a name="should-i-use-change-feed-or-storage-events"></a>Değişiklik akışı veya depolama olaylarını kullanmalıyım mi?
Hem değişiklik akışı hem de [BLOB depolama olayları](storage-blob-event-overview.md) ile aynı bilgileri, olay kayıtlarının gecikmesi, sıralaması ve depolanması ile aynı teslimat güvenilirliği garantisi ile birlikte sağlayabilirsiniz. Değişiklik akışı, değişikliği birkaç dakika içinde günlüğe kaydeder ve ayrıca blob başına değişiklik işlemleri sırasını garanti eder. Depolama olayları gerçek zamanlı olarak gönderilir ve sıralanmayabilir. Değişiklik akışı olayları, depolama hesabınızda, kendi tanımladığınız saklama ile Salt okunabilir tutarlı Günlükler olarak depolanır, ancak depolama olayları açıkça depolamadığınız sürece olay işleyicisi tarafından tüketilmeye devam edilir. Değişiklik akışı ile, herhangi bir sayıda uygulamanız blob API 'Leri veya SDK 'Ları kullanarak günlükleri kendi rahatında kullanabilir. 

## <a name="next-steps"></a>Sonraki adımlar

- .NET istemci uygulaması kullanarak değişiklik akışını okuma hakkında bir örnek için bkz.. Bkz. [Azure Blob depolamada işlem değişiklik akışı günlükleri](storage-blob-change-feed-how-to.md).
- Olaylara gerçek zamanlı olarak tepki verme hakkında bilgi edinin. Bkz. [BLOB depolama olaylarına yeniden davranma](storage-blob-event-overview.md)
- Tüm istekler için hem başarılı hem de başarısız işlemler için ayrıntılı günlük bilgileri hakkında daha fazla bilgi edinin. Bkz. [Azure Storage Analytics günlüğe kaydetme](../common/storage-analytics-logging.md)
