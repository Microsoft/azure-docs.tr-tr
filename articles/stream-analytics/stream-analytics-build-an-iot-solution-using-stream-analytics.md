---
title: Azure Stream Analytics'i kullanarak IOT çözümü oluşturma
description: Stream Analytics IOT çözümünün gişe senaryosu için başlangıç Öğreticisi
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 4b250a5e14ab37553d93453d05f8ff388bf1ba84
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620515"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Stream Analytics kullanarak bir IOT çözümü oluşturma

## <a name="introduction"></a>Giriş
Bu çözümde, Azure Stream Analytics verilerinizden gerçek zamanlı öngörüleri almak için kullanmayı öğrenin. Geçmiş kayıtlarını veya iş bilgileri türetebilir için başvuru verileri ile geliştiriciler tıklatın akışları, günlükler ve olaylar, cihaz tarafından üretilen gibi bir veri akışları kolayca birleştirebilirsiniz. Microsoft Azure'da barındırılan bir tam olarak yönetilen, gerçek zamanlı akış hesaplama hizmeti olan Azure Stream Analytics yerleşik dayanıklılık, düşük gecikme süresi ve dakikalar içinde çalışmaya almak için ölçeklenebilirlik sağlar.

Bu çözüm tamamlandıktan sonra yapabilirsiniz:

* Azure Stream Analytics portalı ile edinin.
* Yapılandırın ve akış işi dağıtın.
* Gerçek dünyadaki sorunları ortaya çıkaran ve Stream Analytics sorgu dilini kullanarak bunları çözün.
* Güvenle Stream Analytics kullanarak müşterileriniz için akış çözümleri geliştirin.
* Sorunları gidermek için izleme ve günlüğe kaydetme deneyimini kullanın.

## <a name="prerequisites"></a>Önkoşullar
Bu çözüm tamamlamak için aşağıdaki önkoşulları ihtiyacınız vardır:
* [Azure aboneliği](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Senaryo giriş: "Hello, ücretli!"
Ücretli istasyonu ortak olguya ' dir. Bunların çoğu expressways, köprüler ve tüneller dünya genelindeki karşılaştığınız. Her bir Ücretli istasyonu birden fazla Ücretli booths sahiptir. El ile booths Ücretli bir Katılımcısı için ödeme durdurun. Otomatik booths Ücretli standına geçirirken, araç, ön için yapıştırılmış bir RFID kartını her standına üzerinde algılayıcı tarar. Bu Ücretli istasyonları aracılığıyla taşıtlardan geçişini üzerinden ilgi çekici işlemleri gerçekleştirilebilir bir olay akışı görselleştirmek kolay bir işlemdir.

![Ücretli booths adresindeki otomobiller resmi](media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth.jpg)

## <a name="incoming-data"></a>Gelen veri
Bu çözüm, iki veri akışları ile çalışır. Giriş ve çıkış Ücretli istasyonları yüklü sensörlerden ilk akışı üretir. İkinci akış vehicle kayıt veriler içeren bir statik arama veri kümesidir.

### <a name="entry-data-stream"></a>Giriş veri akışı
Ücretli istasyonları girmeleri gibi giriş veri akışını otomobiller hakkında bilgi içerir. Çıkış veri örnek uygulamasındaki bir Web uygulamasından bir Event Hub kuyruğuna akış Canlı olaylardır.

| TollID | EntryTime | LicensePlate | Durum | Yapın | Model | VehicleType | VehicleWeight | Ücretli | Etiket |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |7001 JNB |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Aşağıda, sütunları kısa bir açıklaması verilmiştir:

| Sütun | Açıklama |
| --- | --- |
| TollID |Ücretli standına benzersiz olarak tanımlayan Ücretli standına kimliği |
| EntryTime |Tarihi ve saati UTC Ücretli standına için araç girişi |
| LicensePlate |Araç lisans blondan sayısı |
| Durum |Amerika Birleşik Devletleri bir durumda |
| Yapın |Otomobil üreticisi |
| Model |Otomobil model numarası |
| VehicleType |Yolcular araçları veya ticari araçlar için 2 ya da 1 |
| WeightType |Araç ağırlık ton; yolcular araçları için 0 |
| Ücretli |ABD Doları Ücretli değeri |
| Etiket |E-Tag; ödeme otomatikleştirir otomobil üzerinde Ödeme el ile yapılan burada boş |

### <a name="exit-data-stream"></a>Çıkış veri akışı
Çıkış veri akışını Ücretli istasyon bırakarak otomobiller hakkında bilgi içerir. Çıkış veri örnek uygulamasındaki bir Web uygulamasından bir Event Hub kuyruğuna akış Canlı olaylardır.

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |7001 JNB |
| 1 |2014-09-10T12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

Aşağıda, sütunları kısa bir açıklaması verilmiştir:

| Sütun | Açıklama |
| --- | --- |
| TollID |Ücretli standına benzersiz olarak tanımlayan Ücretli standına kimliği |
| ExitTime |Tarihi ve saati UTC Ücretli standına araç, çıkın |
| LicensePlate |Araç lisans blondan sayısı |

### <a name="commercial-vehicle-registration-data"></a>Ticari vehicle kayıt verisi
Çözüm, statik bir anlık görüntü kayıt ticari araç veritabanının kullanır. Bu verileri Azure blob depolama, örnekte bulunan bir JSON dosyası olarak kaydedilir.

| LicensePlate | RegistrationId | Süresi dolmuş |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| 1005 ARKA |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Aşağıda, sütunları kısa bir açıklaması verilmiştir:

| Sütun | Açıklama |
| --- | --- |
| LicensePlate |Araç lisans blondan sayısı |
| RegistrationId |Araç'ın kayıt kimliği |
| Süresi dolmuş |Araç kayıt durumu: 0 vehicle kayıt etkinse, kayıt süresi 1 |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Azure Stream Analytics için ortamı ayarlama
Bu çözüm tamamlamak için bir Microsoft Azure aboneliğinizin olması gerekir. Azure hesabınız yoksa, şunları yapabilirsiniz [ücretsiz deneme sürümü iste](https://azure.microsoft.com/pricing/free-trial/).

Azure kredinizi en iyi kullanımı yapabilmeleri için bu makalenin sonunda yer alan "Azure hesabınızı temizlemek" bölümündeki adımları takip ettiğinizden emin olun.

## <a name="deploy-the-sample"></a>Örneği dağıtma
Bir kaynak grubunda birlikte birkaç tıklamayla kolayca dağıtılabilir çeşitli kaynaklar vardır. Çözüm tanımı GitHub deposundaki barındırılan [ https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp ](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp).

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Azure portalında TollApp şablonu dağıtma
1. Azure'a TollApp ortamı dağıtmak için bu bağlantıyı kullanmak [TollApp Azure şablonu Dağıt](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json).

2. İstendiğinde Azure portalında oturum açın.

3. Çeşitli kaynaklara faturalanacağı aboneliği seçin.

4. Benzersiz bir adla yeni bir kaynak grubu belirtin, örneğin `MyTollBooth`.

5. Bir Azure konumu seçin.

6. Belirtin bir **aralığı** saniye sayısı. Bu değer, ne sıklıkla olay Hub'ına veri göndermek örnek web uygulaması kullanılır.

7. **Denetleme** hüküm ve koşulları kabul edin.

8. Seçin **panoya Sabitle** böylece kaynaklar daha sonra kolayca bulun.

9. Seçin **satın alma** örnek şablonu dağıtabilirsiniz.

10. Birkaç dakika sonra onaylamak için bir bildirim görüntülenir. **dağıtım başarılı**.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Azure Stream Analytics TollApp kaynakları gözden geçirin

1. Azure portalında oturum açın

2. Önceki bölümde adlı kaynak grubunu bulun.

3. Aşağıdaki kaynaklar kaynak grubunda listelendiğini doğrulayın:
   - Bir Cosmos DB hesabı
   - Bir Azure Stream Analytics işi
   - Bir Azure depolama hesabı
   - Bir Azure olay hub'ı
   - İki Web uygulaması

## <a name="examine-the-sample-tollapp-job"></a>Örnek TollApp iş inceleyin
1. Önceki bölümde, kaynak grubunda başlayarak adı ile başlayan bir Stream Analytics akış işi seçin **tollapp** (ad benzersizliğini rastgele karakterler içeriyor).

2. Üzerinde **genel bakış** işinin dikkat edin sayfasında **sorgu** sorgu söz dizimini görüntülemek için onay kutusunu.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   Sorgunun amacı paraphrase için ücretli standına girin taşıtlardan sayısını gerektiğini varsayalım. Otoyol Ücretli standına girme taşıtlardan sürekli bir akışı olduğundan, hiçbir zaman durduran bir akışa giriş olayları benzer olanlardır. Bir "süre" tanımlamak zorunda akış ölçmek için üzerinden ölçmek için. Şimdi "kaç taşıtlardan Ücretli standına üç dakikada bir için enter?" sorusuna daha İyileştir Bu genellikle atlayan sayım olarak adlandırılır.

   Gördüğünüz gibi Azure Stream Analytics sorgu zamanla ilişkili yönlerini belirlemek için birkaç uzantıları ekler ve SQL gibi bir sorgu dili kullanır.  Hakkında daha fazla ayrıntı için okuma [zaman Yönetimi](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) ve [Pencereleme](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) sorguda kullanılan oluşturur.

3. TollApp örnek iş girişleri inceleyin. Geçerli sorguyu yalnızca EntryStream giriş kullanılır.
   - **EntryStream** giriştir kuyruklar her zaman bir araba Otoyol üzerinde bir gişe girer temsil eden veri bir olay hub'ı bağlantısı. Örnek bir parçası olan bir web uygulaması, olayları oluşturma ve bu verileri bu olay Hub'ında sıraya alınır. Bu giriş akış sorgusunun FROM yan tümcesinde sorgulanır unutmayın.
   - **ExitStream** giriştir kuyruklar her zaman bir araba Otoyol üzerinde bir gişe çıkar temsil eden veri bir olay hub'ı bağlantısı. Bu akış girişi sorgu söz dizimi, daha sonra farklılığı kullanılır.
   - **Kayıt** giriştir gerektiğinde aramalar için kullanılan bir statik registration.json dosyasına işaret eden bir Azure Blob Depolama bağlantısı. Bu başvuru veri girişi, daha sonra farklılığı sorgu söz dizimi kullanılır.

4. TollApp örnek iş çıktıları inceleyin.
   - **Cosmos DB** çıktı, çıkış havuzu olaylarını alır bir Cosmos veritabanı kapsayıcıdır. Bu çıkış akış sorgu yan TÜMCESİNE kullanıldığını unutmayın.

## <a name="start-the-tollapp-streaming-job"></a>İş akışında TollApp Başlat
Akış işi başlatmak için aşağıdaki adımları izleyin:

1. Üzerinde **genel bakış** sayfası seçin işin **Başlat**.

2. Üzerinde **başlangıç işi** bölmesinde **artık**.

3. İş, üzerinde çalışmaya başladıktan sonra birkaç dakika sonra **genel bakış** sayfa görünümü akış işinin **izleme** grafiği. Graf birkaç bin giriş olayları ve output olayları onlarca göstermelidir.

## <a name="review-the-cosmosdb-output-data"></a>CosmosDB çıkış verileri gözden geçirin
1. TollApp kaynakları içeren kaynak grubunu bulun.

2. Ad deseni ile Azure Cosmos DB hesabı seçin **tollapp\<rastgele\>-cosmos**.

3. Seçin **Veri Gezgini** Veri Gezgini sayfası açmak için.

4. Genişletin **tollAppDatabase** > **tollAppCollection** > **belgeleri**.

5. Çıkış kullanılabilir olduğunda birkaç docs kimlikleri listesinde gösterilir.

6. JSON belgesini gözden geçirmek için her kimliğini seçin. Her tollid windowend fark zaman ve penceresinde bu arabalar sayısı.

7. Ek bir üç dakika sonra başka bir dört belge kümesini kullanılabilir tollid başına tek bir belge.


## <a name="report-total-time-for-each-car"></a>Her bir otomobil için toplam süreyi raporu
İşlem ve müşteri deneyimini verimliliğini değerlendirmek için bir araba Ücretli geçirmek için gereken ortalama süreyi sağlar.

Toplam süre bulmak için ExitTime akış EntryTime stream'le katılın. İki giriş akışları TollId ve LicencePlate eşit eşleşen sütunları katılın. **Katılın** işleci birleştirilmiş olayları arasındaki kabul edilebilir zaman farkı açıklayan zamana bağlı leeway belirtmenizi gerektirir. Kullanım **DATEDIFF** olayları birbirinden en fazla 15 dakika olması gerektiğini belirtmek için işlev. Ayrıca uygulama **DATEDIFF** çıkmak için işlevi ve gerçek zaman işlem için giriş saatleri bir araba geçirdiği Ücretli bir istasyonu. Kullanımını farka dikkat edin **DATEDIFF** içinde kullanıldığında bir **seçin** deyimi yerine **katılın** koşul.

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Akış işi sorgu söz dizimi TollApp güncelleştirmek için:

1. Üzerinde **genel bakış** sayfası seçin işin **Durdur**.

2. İş durdu bildirim birkaç dakika bekleyin.

3. İş TOPOLOJİSİ başlığı altında seçin **< > sorgu**

4. Ayarlanan akış SQL sorguyu yapıştırın.

5. Seçin **Kaydet** sorguyu kaydetmek için. Onayla **Evet** değişiklikleri kaydedin.

6. Üzerinde **genel bakış** sayfası seçin işin **Başlat**.

7. Üzerinde **başlangıç işi** bölmesinde **artık**.

### <a name="review-the-total-time-in-the-output"></a>Çıkış toplam zaman gözden geçirin
Akış işi CosmosDB çıkış verilerini gözden geçirmek için önceki bölümdeki adımları yineleyin. En son JSON belgelerini gözden geçirin.

Örneğin, bu belgede belirli bir lisans blondan entrytime ve çıkış saati ve Ücretli standına süresi iki dakika olarak DATEDIFF hesaplanan dakika cinsiden süre alanı ile bir örnek araba gösterir:
```JSON
{
    "tollid": 4,
    "entrytime": "2018-04-05T06:51:39.0491173Z",
    "exittime": "2018-04-05T06:53:09.0491173Z",
    "licenseplate": "JVR 9425",
    "durationinminutes": 2,
    "id": "ff52eb25-d580-7566-2879-1f52bba6601e",
    "_rid": "+8E4AI1DZgBjAAAAAAAAAA==",
    "_self": "dbs/+8E4AA==/colls/+8E4AI1DZgA=/docs/+8E4AI1DZgBjAAAAAAAAAA==/",
    "_etag": "\"ad02f6b8-0000-0000-0000-5ac5c8330000\"",
    "_attachments": "attachments/",
    "_ts": 1522911283
}
```

## <a name="report-vehicles-with-expired-registration"></a>Süresi dolan kaydı ile rapor araçları
Azure Stream Analytics, zamana bağlı veri akışları ile birleştirmek için statik başvuru verileri anlık görüntülerini kullanabilirsiniz. Bu özellik göstermek için aşağıdaki örnek soru kullanın. Kayıt girişi lisans etiketlerin süresinin sona ermesinin listeleyen statik blob bir json dosyasıdır. Lisans makinesinde katılarak, başvuru verilerini Ücretli her iki geçirme her araç karşılaştırılır.

Bir ticari vehicle Ücretli şirketi ile kayıtlıysa, ücretli standına denetleme için durdurulmadan geçirebilirsiniz. Kayıt arama tablosu kayıtları süresi dolan tüm ticari Araçlar tanımlamak için kullanın.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Akış işi sorgu söz dizimi TollApp güncelleştirmek için önceki bölümdeki adımları yineleyin.

2. Akış işi CosmosDB çıkış verilerini gözden geçirmek için önceki bölümdeki adımları yineleyin.

Örnek çıktı:
```json
    {
        "entrytime": "2018-04-05T08:01:28.0252168Z",
        "licenseplate": "GMT 3221",
        "tollid": 1,
        "registrationid": "763220582",
        "id": "47db0535-9716-4eb2-db58-de7886966cbf",
        "_rid": "y+F8AJ9QWACSAQAAAAAAAA==",
        "_self": "dbs/y+F8AA==/colls/y+F8AJ9QWAA=/docs/y+F8AJ9QWACSAQAAAAAAAA==/",
        "_etag": "\"88007d8d-0000-0000-0000-5ac5d7e20000\"",
        "_attachments": "attachments/",
        "_ts": 1522915298
    }
```

## <a name="scale-out-the-job"></a>Projeyi ölçeklendirin
Azure Stream Analytics, büyük hacimli verileri işleyebilmeniz esnek Ölçekle tasarlanmıştır. Azure Stream Analytics sorgu kullanabileceğiniz bir **PARTITION BY** sistem Ölçeklendirmesi eşitlenene bu adımı bildirmek için yan tümcesi. **PartitionID** sistem, bölüm kimliği ' % s'giriş (event hub) eşleşen ekleyen özel bir sütundur.

Bölümleri sorgu ölçeğini genişletmek için sorgu söz dizimi aşağıdaki koda düzenleyin:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream
TIMESTAMP BY EntryTime
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Akış işi daha fazla akış birimi için ölçeklendirmek için:

1. **Durdur** geçerli iş.

2. Sorgu söz dizimi içinde güncelleştirme **< > sorgu** sayfasında ve değişiklikleri kaydedin.

3. Akış işi yapılandırma başlığı altında seçin **ölçek**.

4. Slayt **akış birimleri** 1 kaydırıcısından 6. Akış birimleri, iş alabilir işlem gücü miktarını tanımlayın. **Kaydet**’i seçin.

5. **Başlangıç** ek ölçek göstermek için akış işi. Azure Stream Analytics işini daha fazla işlem kaynakları genelinde dağıtır ve bölümlendirme çalışması PARTITION BY yan tümcesinde belirtilen sütun kullanarak kaynakları arasında daha iyi verim elde.

## <a name="monitor-the-job"></a>İş izleme
**İZLEYİCİ** çalışan işle ilgili istatistikleri alanı içerir. Depolama hesabının aynı bölgede (Bu belgenin geri kalan gibi Ücretli adı) kullanmak için ilk kez bir yapılandırma gerekmez.

![Azure Stream Analytics işi izleme](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

Erişebildiğiniz **etkinlik günlüklerini** işi panodan **ayarları** de alan.

## <a name="clean-up-the-tollapp-resources"></a>TollApp kaynakları temizleme
1. Azure portalında Stream Analytics işi durdurun.

2. TollApp şablonla ilgili sekiz kaynakları içeren kaynak grubunu bulun.

3. **Kaynak grubunu sil**'i seçin. Silme işlemini onaylamak için kaynak grubunun adını yazın.

## <a name="conclusion"></a>Sonuç
Bu çözüm için Azure Stream Analytics hizmeti kullanıma sunuldu. Bu giriş ve çıkışları Stream Analytics işi yapılandırma gösterilmektedir. Ücretli veri senaryosu kullanarak çözüm hareket ve bunların nasıl çözülebileceğini verilerle Azure Stream analytics'te basit SQL benzeri sorguları alanının içinde çıkan sorunların ortak türleri açıklanmaktadır. Zamana bağlı veriler üzerinde çalışmak için SQL uzantı yapıları çözümü açıklanmaktadır. Veri akışları nasıl nasıl veri akışı statik başvuru verileri ile zenginleştirin ve daha yüksek performans sağlamak için bir sorgunun nasıl oluşturulacağını gösterir.

Bu çözümün iyi giriş sağlasa da, herhangi bir yolla tam değil. Daha fazla sorgu desenleri SAQL dil kullanarak bulabilirsiniz [sorgu örnekleri için sık kullanılan Stream Analytics kullanım desenlerini](stream-analytics-stream-analytics-query-patterns.md).
