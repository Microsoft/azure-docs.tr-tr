---
title: Azure Stream Analytics ile Power BI Pano tümleştirmesi
description: Bu makalede, Azure Stream Analytics işi verileri görselleştirmek için Power BI panolarına gerçek zamanlı kullanmayı açıklar.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: c415bdecdaf55f3068dcd804ab34de402fe7a31f
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612282"
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics ve Power BI: Veri akışı için gerçek zamanlı analiz Panosu

Azure Stream Analytics lider iş zekası araçları birini avantajlarından yararlanmanıza olanak tanır [Microsoft Power BI](https://powerbi.com/). Bu makalede, şunların nasıl iş zekası araçları, Azure Stream Analytics işleri için çıkış olarak Power BI kullanarak oluşturun. Ayrıca nasıl gerçek zamanlı bir Pano oluşturup kullanacağınızı öğrenin.

Bu makalede, Stream Analytics'ten devam [gerçek zamanlı sahtekarlık algılama](stream-analytics-real-time-fraud-detection.md) öğretici. Bu öğreticide oluşturulan iş akışı oluşturur ve böylece bir akış analizi işi tarafından algılanan sahte telefon aramaları görselleştirebilirsiniz çıkış bir Power BI ekler. 

İzleyebilir [video](https://www.youtube.com/watch?v=SGUpT-a99MA) , bu senaryo gösterilmektedir.


## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce şunlara sahip olduğunuzdan emin olun:

* Bir Azure hesabı.
* Power BI için bir hesap. Bir iş veya Okul hesabı kullanabilirsiniz.
* Tamamlanmış bir sürümünü [gerçek zamanlı sahtekarlık algılama](stream-analytics-real-time-fraud-detection.md) öğretici. Öğretici, kurgusal telefonla meta verileri oluşturan bir uygulamayı içerir. Öğreticide, bir olay hub'ı oluşturup olay hub'ına akış telefon araması verileri gönderebilirsiniz. Sahte aramaları (aynı anda aynı sayısından farklı konumlarda) algılayan bir sorgu yazmanız. 


## <a name="add-power-bi-output"></a>Power BI çıkışına Ekle
Gerçek zamanlı sahtekarlık algılama öğreticide çıktı Azure Blob depolama alanına gönderilir. Bu bölümde, bilgileri Power BI'a gönderen bir çıktı ekleyin.

1. Azure portalında, daha önce oluşturduğunuz Streaming Analytics işini açın. Önerilen ad kullandıysanız, işi adı `sa_frauddetection_job_demo`.

2. Sol menüden **çıkışları** altında **iş topolojisi**. Ardından, **+ Ekle** ve **Power BI** açılan menüden.

3. **+ Ekle** > **Power BI**'ı seçin. Ardından formu aşağıdaki bilgilerle doldurun ve **Yetkilendir**'i seçin:

   |**Ayar**  |**Önerilen değer**  |
   |---------|---------|
   |Çıktı diğer adı  |  CallStream Power BI  |
   |Veri kümesi adı  |   SA veri kümesi  |
   |Tablo adı |  dolandırıcılık amaçlı çağrıları  |

   ![Stream Analytics çıktı yapılandırma](media/stream-analytics-power-bi-dashboard/configure-stream-analytics-output.png)

   > [!WARNING]
   > Power BI veri kümesi ve Stream Analytics işinde belirttiğiniz ayarlara olarak aynı ada sahip bir tablo varsa, mevcut üzerine yazılır.
   > Açıkça bu veri kümesi ve tablo, Power BI hesabınızda oluşturduğunuz değil öneririz. Stream Analytics işinizi başlattığınızda ve iş parçacıklarının çıkış Power BI'da oturum başlatır. otomatik olarak oluşturulur. İş sorgusu hiçbir sonuç döndürmezse, tablo ve veri kümesi oluşturulmaz.
   >

4. **Yetkilendir**'i seçtiğinizde bir açılır pencere görünür ve Power BI hesabınızda kimlik doğrulaması için sizden kimlik bilgilerini sağlamanız istenir. Yetkilendirme başarılı olduktan sonra **Kaydet** seçeneğine tıklayarak ayarları kaydedin.

8.           **Oluştur**'a tıklayın.

Veri kümesi aşağıdaki ayarlara sahip oluşturulur:

* **defaultRetentionPolicy: Basicfıfo** -FIFO, en çok 200.000 satır uzunluğunda verilerdir.
* **defaultMode: pushStreaming** -veri kümesi, hem akış kutucukları hem de Geleneksel rapor tabanlı görselleri (anında iletme olarak da bilinir) destekler.

Şu anda veri kümeleri ile diğer bayraklar oluşturamazsınız.

Power BI veri kümeleri hakkında daha fazla bilgi için bkz. [Power BI REST API'si](https://msdn.microsoft.com/library/mt203562.aspx) başvuru.


## <a name="write-the-query"></a>Sorgu yazma

1. Kapat **çıkışları** dikey penceresini ve iş dikey penceresine dönün.

2. Tıklayın **sorgu** kutusu. 

3. Aşağıdaki sorguyu girin. Bu sorgu, sahtekarlık algılama öğreticide oluşturulan kendi kendine birleşme sorgu benzerdir. Bu sorgu oluşturduğunuz yeni çıktı sonuçları gönderdiğini fark (`CallStream-PowerBI`). 

    >[!NOTE]
    >Giriş adı değil `CallStream` , sahtekarlık algılama öğreticide değiştirin `CallStream` içinde **FROM** ve **katılın** sorgu yan tümcelerinde.

   ```SQL
   /* Our criteria for fraud:
   Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "CallStream-PowerBI"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime

   /* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
   ON CS1.CallingIMSI = CS2.CallingIMSI

   /* ...and date between CS1 and CS2 is between one and five seconds */
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

   /* Where the switch location is different */
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

4. **Kaydet**’e tıklayın.


## <a name="test-the-query"></a>Sorguyu test edin

Bu bölümde, isteğe bağlı ancak önerilen değerdir. 

1. TelcoStreaming uygulama şu anda çalışmıyorsa başlatın şu adımları izleyin:

    * Komut istemi açın.
    * Telcogenerator.exe ve değiştirilen telcodatagen.exe.config dosyaları olduğu klasöre gidin.
    * Şu komutu çalıştırın:

       `telcodatagen.exe 1000 .2 2`

2. Üzerinde **sorgu** noktaya tıklayın, sayfa için Stream Analytics işinizi `CallStream` seçin ve sonra **örnek giriş verileri**.

3. Veri tıklatın ve üç dakika değerini istediğinizi belirtin **Tamam**. Veri örneğinin alındığını belirten bildirim gelene kadar bekleyin.

4. Tıklayın **Test** ve sonuçları gözden geçirin.

## <a name="run-the-job"></a>İşi çalıştırma

1. TelcoStreaming uygulamayı çalışır durumda olduğundan emin olun.

2. Gidin **genel bakış** için Stream Analytics işinizi sayfasından seçim yapıp **Başlat**.

    ![Stream Analytics işini başlatın](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

Streaming Analytics işinizi gelen akışındaki sahte çağrıları için arama başlatır. İş Ayrıca, Power BI'da veri kümesi ve tablo oluşturur ve onlara sahte çağrıları ile ilgili veri göndermeye başlar.


## <a name="create-the-dashboard-in-power-bi"></a>Power BI'da Pano oluşturma

1. Git [Powerbi.com](https://powerbi.com) ve iş veya Okul hesabınızla oturum açın. Stream Analytics işi sorgusu, sonuçların çıkışını veriyorsa veri kümenizin zaten oluşturulmuş olduğunu görürsünüz:

    ![Power BI akış veri kümesi konum](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. Çalışma alanınızda tıklayın  **+ &nbsp;Oluştur**.

    ![Power BI çalışma oluştur düğmesi](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Yeni bir pano oluşturun ve adlandırın `Fraudulent Calls`.

    ![Bir pano oluşturun ve Power BI çalışma alanındaki bir ad verin](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. Pencerenin üst kısmında tıklayın **kutucuk Ekle**seçin **özel akış verileri**ve ardından **sonraki**.

    ![Özel veri kümesi kutucuğu Power bı'da akış](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. Altında **bilgisayarınızı DATSETS**, Veri kümenizi seçin ve ardından **sonraki**.

    ![Power BI Akış Veri kümenizi](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. Altında **görselleştirme türünü**seçin **kart**ve ardından **alanları** listesinden **fraudulentcalls**.

    ![Yeni bir kutucuk için görselleştirme ayrıntıları](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7.           **İleri**'ye tıklayın.

8. Kutucuk ayrıntıları gibi bir başlık ve alt konu başlığını doldurun.

    ![Başlık ve alt konu başlığı için yeni bir kutucuk](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. **Uygula**'ya tıklayın.

    Artık bir sahtekarlık sayacı var!

    ![Power BI panosunda sahtekarlık sayacı](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. Yeniden (4. adım ile başlayarak) bir kutucuk eklemek için adımları izleyin. Bu kez, aşağıdakileri yapın:

    * Ulaştığınızda **görselleştirme türünü**seçin **çizgi grafik**. 
    * Eksen ekleyin ve **windowend** seçeneğini belirleyin. 
    * Değer ekleyip **fraudulentcalls** seçeneğini belirleyin.
    * **Görüntülenecek zaman penceresini** için son 10 dakikayı seçin.

      ![Power BI'da kutucuk için çizgi grafik oluşturma](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Tıklayın **sonraki**, başlık ve alt konu başlığını eklemek ve tıklayın **Uygula**.

     Power BI Panosu artık, sahte çağrıları ile ilgili iki görünüm veri akış verilerini algıladığından sağlar.

     ![Dolandırıcılık amaçlı çağrıları için iki kutucuk gösteren Power BI panosunun tamamlandı](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>Power BI hakkında bilgi alın

Bu öğreticide, yalnızca birkaç türde bir veri kümesi için görselleştirmeler oluşturma gösterilmektedir. Power BI, kuruluşunuzun müşteri iş zekası araçlarının oluşturmanıza yardımcı olabilir. Daha fazla fikir için aşağıdaki kaynaklara bakın:

* Power BI panolarına başka bir örneği için izleme [Power BI ile çalışmaya başlama](https://youtu.be/L-Z_6P56aas?t=1m58s) video.
* Streaming Analytics yapılandırma hakkında daha fazla bilgi için proje çıktı Power BI ve Power BI grupları kullanarak gözden [Power BI](stream-analytics-define-outputs.md#power-bi) bölümünü [Stream Analytics çıkarır](stream-analytics-define-outputs.md) makalesi. 
* Power BI genellikle kullanma hakkında daha fazla bilgi için bkz: [Power bı'da panolar](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).


## <a name="learn-about-limitations-and-best-practices"></a>Sınırlamalar ve en iyi uygulamalar hakkında bilgi edinin
Şu anda Power BI yaklaşık saniyede bir kez çağrılabilir. Akış görselleri, 15 KB paketlerini destekler. Ötesinde, akış görselleri başarısız (ancak anında iletme çalışmaya devam eder). Bu sınırlamalar nedeniyle Power BI kendisini en doğal olarak Azure Stream Analytics önemli veri yükünü azaltma değerlendirildiğinde çalışmaları için uygundur. Veri gönderimi en fazla saniye başına bir anında iletme olduğunu ve sorgunuzu aktarım hızı gereksinimlerini gölünüzdeki emin olmak için bir atlayan pencere ya da Hopping penceresini kullanmanızı öneririz.

Aşağıdaki denklemi saniyeler içinde pencereniz verilecek değer hesaplamak için kullanabilirsiniz:

![Saniyeler içinde penceresi vermek için değeri hesaplamak için eşitlik](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

Örneğin:

* Bir saniyelik aralıklarla veri gönderen 1.000 cihazları var.
* Power BI Pro saat başına 1.000.000 satır destekleyen SKU kullanıyor.
* Cihaz başına ortalama veri miktarı için Power BI yayımlamak istiyorsunuz.

Sonuç olarak, eşitlik olur:

![Örnek ölçütlere göre eşitlik](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

Bu yapılandırmayı göz önünde bulundurulduğunda, özgün sorguyu aşağıdaki değiştirebilirsiniz:

```SQL
    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO "CallStream-PowerBI"
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl
```

### <a name="renew-authorization"></a>Yetkilendirmeyi Yenile
Parola işinizi oluşturulduğu veya en son kimlik doğrulaması bu yana değişti, Power BI hesabınızı yeniden kimlik doğrulamaya zorlayabilir gerekir. Azure multi-Factor Authentication, Azure Active Directory (Azure AD) kiracınız yapılandırılmışsa, ayrıca Power BI yetkilendirme iki haftada yenilemeniz gerekir. Yenilemezseniz, iş çıktısı olmaması gibi belirtileri görebilir veya `Authenticate user error` işlem günlüğüne kaydeder.

Benzer şekilde, belirtecin süresi dolduktan sonra iş başlarsa, bir hata oluşur ve işi başarısız olur. Bu sorunu çözmek için çalışan işini durdurma ve Power BI çıkışınızı gidin. Veri kaybını önlemek için seçin **yetkilendirmeyi yenilemek** bağlantı ve iş öğesinden yeniden **son durduruldu zamanı**.

Power BI ile yetkilendirme yenilendikten yeşil bir uyarı sorunun giderilmiş olduğunu yansıtmak için yetkilendirme alanında görüntülenir.

## <a name="get-help"></a>Yardım alın
Daha fazla yardım için deneyin bizim [Azure Stream Analytics forumumuzu](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Stream analytics'e giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics sorgu dili başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Yönetimi REST API Başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)
