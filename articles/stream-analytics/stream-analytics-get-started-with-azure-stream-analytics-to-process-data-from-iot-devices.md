---
title: Azure Stream Analytics kullanan IoT gerçek zamanlı veri akışları
description: Akış analizi ve gerçek zamanlı veri işleme ile birlikte IoT algılayıcı etiketleri ve veri akışları
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: f6a1d5e5a15a2af7db5b6256a6a0c5f19f0e7cf5
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620991"
---
# <a name="get-started-with-azure-stream-analytics-to-process-data-from-iot-devices"></a>IoT cihazlarından veri işlemek için Azure Stream Analytics'i kullanmaya başlama

Bu öğreticide, nesnelerin interneti (IOT) cihazlarından veri toplamak üzere akış işleme mantığı oluşturmayı öğrenin. Çözümünüzü hızlı ve ekonomik bir şekilde nasıl oluşturacağınızı göstermek için gerçek hayattaki bir Nesnelerin İnterneti (IoT) kullanım örneğinden yararlanacağız.

## <a name="prerequisites"></a>Önkoşullar

* [Azure aboneliği](https://azure.microsoft.com/pricing/free-trial/)
* Örnek sorgu ve veri dosyaları [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)'dan indirilebilir

## <a name="scenario"></a>Senaryo

Endüstriyel otomasyon alanında faaliyet gösteren Contoso şirketi, üretim süreçlerini tamamen otomatik hale getirmiştir. Bu fabrikada bulunan makineler, gerçek zamanlı olarak veri akışları yayabilen algılayıcılara sahiptir. Bu senaryoda bir üretim katı yöneticisi, algılayıcı verilerinden gerçek zamanlı bilgiler alarak belirli kalıpları aramak ve bunlara yönelik işlemler yapmak istiyor. Gelen veri akışındaki ilginç kalıpları bulmak için algılayıcı verilerinden Akış Analizi Sorgu Dili'ni (SAQL) kullanacağız.

Burada, veriler bir Texas Instruments algılayıcı etiketi cihazında oluşturulmaktadır. Verilerin yükü JSON biçimindedir ve aşağıdaki gibi görünür:

```json
{
    "time": "2016-01-26T20:47:53.0000000",  
    "dspl": "sensorE",  
    "temp": 123,  
    "hmdt": 34  
}  
```

Gerçek hayattaki bir senaryoda, olayları bir akış şeklinde oluşturan bunun gibi yüzlerce algılayıcınız olabilir. Bir ağ geçidi cihazının bu olayları [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)'a veya [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)'larına göndermek için kod çalıştırması idealdir. Akış Analizi işiniz, bu olayları Event Hubs'dan alır ve akışlara yönelik gerçek zamanlı analiz sorguları çalıştırır. Daha sonra, [desteklenen çıktılardan](stream-analytics-define-outputs.md) birine sonuçları gönderebilirsiniz.

Kullanım kolaylığı için, bu başlangıç kılavuzunda gerçek algılayıcı etiket cihazlarından yakalanan bir örnek veri dosyası sunulmaktadır. Örnek veriler üzerinde sorgu çalıştırabilir ve sonuçları görebilirsiniz. Daha sonraki öğreticilerde, işinizi girişlere ve çıkışlara nasıl bağlayacağınızı ve bunları Azure hizmetine nasıl dağıtacağınızı öğreneceksiniz.

## <a name="create-a-stream-analytics-job"></a>Akış Analizi işi oluşturma
1. [Azure portal](https://portal.azure.com)’da, artı işaretine tıklayın ve ardından sağdaki metin penceresine **AKIŞ ANALİZİ** yazın. Ardından sonuçlar listesinde **Akış Analizi işi**’ni seçin.
   
    ![Yeni bir Akış Analizi işi oluşturma](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)
2. Benzersiz bir iş adı girin ve aboneliğin işiniz için doğru olduğundan emin olun. Ardından yeni bir kaynak grubu oluşturun veya aboneliğinizdeki mevcut bir kaynak grubunu seçin.
3. İşiniz için bir konum seçin. İşleme hızını artırmak ve veri aktarım maliyetlerini azaltmak için kaynak grubu ve hedeflenen depolama hesabıyla aynı konumun seçilmesi önerilir.
   
    ![Yeni bir Akış Analizi işi oluşturma ayrıntıları](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)
   
   > [!NOTE]
   > Bu depolama hesabını her bölge için yalnızca bir kez oluşturmanız gerekir. Bu depolama birimi, ilgili bölgede oluşturulan tüm Akış Analizi işlerinde paylaşılır.
   > 
   > 
4. Kutuyu işaretleyerek işinizi panonuza yerleştirin ve ardından **OLUŞTUR**’a tıklayın.
   
    ![Stream analytics işi oluşturma sürüyor](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03a.png)
5. Tarayıcı pencerenizin sağ üst köşesinde 'Dağıtım başladı...' yazısını görürsünüz. Bu pencere, kısa bir süre sonra aşağıda gösterildiği gibi tamamlandı penceresi olarak değişecektir.
   
    ![Stream analytics dağıtım başarılı](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03b.png)

## <a name="create-an-azure-stream-analytics-query"></a>Azure Akış Analizi sorgusu oluşturma
İşiniz oluşturulduktan sonra, işinizi açıp sorgu oluşturabilirsiniz. İlgili kutucuğa tıklayarak işinize kolayca erişebilirsiniz.

![Azure portalında Stream Analytics işi kutucuğu](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

**İş Topolojisi** bölmesinde **SORGU** kutusuna tıklayarak Sorgu Düzenleyicisi'ne gidin. **SORGU** düzenleyicisi, gelen olay verilerinde dönüştürme işlemini gerçekleştiren bir T-SQL sorgusu girmenizi sağlar.

![Stream Analytics Panosu sorgu kutucuğu](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

### <a name="query-archive-your-raw-data"></a>Sorgu: Ham verilerinizi arşivleme
En basit sorgu biçimi, tüm giriş verilerini belirlenmiş çıkışlarına arşivleyen bir doğrudan sorgudur. Örnek veri dosyasını [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)'dan bilgisayarınızdaki bir konuma indirin. 

1. PassThrough.txt dosyasından sorguyu yapıştırın. 
   
    ![Sorgu Stream Analytics sorgu düzenleyiciye yapıştırın.](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)
2. Girişinizin yanındaki üç noktaya tıklayın ve **Örnek verileri dosyadan karşıya yükle** kutusunu seçin.
   
    ![Örnek verileri karşıya yükleme dosyası seçin](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06a.png)
3. Sağda bir bölme açılır. Bu bölmede, indirdiğiniz konumdan HelloWorldASA InputStream.json veri dosyasını seçin ve bölmenin en altındaki **Tamam**’a tıklayın.
   
    ![JSON örnek veri dosyasını karşıya yükle](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06b.png)
4. Ardından pencerenin sol üst bölümündeki **Test** dişlisine tıklayın ve test sorgunuzu örnek veri kümesine göre işleyin. İşlem tamamlandığında sorgunuzun altında bir sonuç penceresi açılır.
   
    ![Stream Analytics sorgu için test sonuçları](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Sorgu: Verileri bir koşula göre filtreleme
Sonuçları bir koşula göre filtrelemeyi deneyelim. Yalnızca "sensorA" gelen olaylar için sonuçları göstermek istiyoruz Sorgu, Filtering.txt dosyasında yer alır.

![Bir veri akışını filtreleme](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Büyük/küçük harfe duyarlı sorgular bir dize değerini karşılaştırır. Sorguyu yürütmek için **Test** dişlisine yeniden tıklayın. Sorgu 1860 olay içinden 389 satır döndürmelidir.

![Sorgu testine ilişkin ikinci çıktı sonuçları](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Sorgu: İş akışı tetiklemesi için uyarı
Şimdi sorgumuzu daha ayrıntılı hale getirelim. Her algılayıcı türü için ortalama sıcaklığı 30 saniyelik aralıklarda izlemek ve yalnızca ortalama sıcaklık 100 derecenin üzerinde olduğu zaman sonuçları görüntülemek istiyoruz. Aşağıdaki sorguyu yazmamız ve sonuçları görmek için **Test** düğmesine tıklamamız gerekiyor. Sorgu ThresholdAlerting.txt dosyasındadır.

![30 saniyelik filtre sorgusu](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Şimdi sonuçların yalnızca 245 satır içerdiğini ve ortalama sıcaklığın 100 dereceden fazla olduğu algılayıcı adlarını listelediğini göreceksiniz. Bu sorguda olay akışı, 30 saniyelik bir **Atlayan Pencere** üzerinden algılayıcı adı olan **dspl**'ye göre gruplanır. Zamana bağlı sorgular, zamanın nasıl ilerleyeceğini belirtmelidir. **TIMESTAMP BY** yan tümcesini kullanarak, zamanları zamana bağlı tüm hesaplamalarla ilişkilendirmek için **OUTPUTTIME** sütununu belirttik. Ayrıntılı bilgi için [Zaman Yönetimi](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) ve [Pencereleme işlevleri](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) hakkındaki MSDN makalelerini okuyun.

### <a name="query-detect-absence-of-events"></a>Sorgu: Var olmayan olayları algılama
Eksik giriş olaylarını bulmak için nasıl sorgu yazabiliriz? Bir algılayıcının veri gönderip sonraki beş saniye boyunca hiç olay göndermediği son zamanı bulalım. Sorgu AbsenceOfEvent.txt dosyasıdır.

![Var olmayan olayları algılama](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

Burada, **LEFT OUTER** deyimini aynı veri akışı üzerinde kullanırız (kendi kendine birleşme). Bir **INNER** birleşimde, yalnızca bir eşleşme bulunduğu zaman bir sonuç döndürülür.  Bir **LEFT OUTER** birleşimde, birleştirmenin sol tarafındaki bir olay eşleşmemişse sağ taraftaki tüm sütunlar için NULL değerine sahip bir satır döndürülür. Bu teknik, var olmayan olayların bulunması için oldukça kullanışlıdır. [JOIN](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) hakkında daha fazla bilgi edinmek için MSDN belgelerimize bakın.

## <a name="conclusion"></a>Sonuç
Bu öğreticide, farklı Akış Analizi Sorgu Dili sorgularının nasıl yazılacağı ve sonuçların tarayıcıda nasıl görüntüleneceği gösterilecek. Ancak bu sadece bir başlangıçtır. Akış Analizi ile yapabileceğiniz daha birçok şey bulunmaktadır. Akış Analizi birçok giriş ve çıkışı desteklemenin yanı sıra Azure Machine Learning'deki işlevlerden de faydalanır. Bu da onu veri akışlarının analizi için sağlam bir araç haline getirir. [Öğrenme haritamızı](https://docs.microsoft.com/azure/stream-analytics/) kullanarak Akış Analizi’ni keşfetmeye başlayabilirsiniz. Sorgu yazma hakkında daha fazla bilgi için [ortak sorgu desenleri](stream-analytics-stream-analytics-query-patterns.md) hakkındaki makaleyi okuyun.

