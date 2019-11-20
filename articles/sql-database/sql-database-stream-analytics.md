---
title: Stream Analytics tümleştirme kullanarak verileri akış (Önizleme)
description: Azure SQL veritabanında veri akışı yapmak için Azure Stream Analytics kullanın.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: d233d3c98cc495e4b9e84142781f5eb9faa7eec8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820835"
---
# <a name="stream-data-by-using-azure-sql-database-stream-analytics-integration-preview"></a>Azure SQL veritabanı Stream Analytics Tümleştirme (Önizleme) kullanarak veri akışı

Kullanıcılar artık [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)kullanarak Azure Portal bir SQL veritabanından bir tabloya doğrudan gerçek zamanlı akış verileri alabilir, işleyebilir, görüntüleyebilir ve analiz edebilir. Bu deneyim, bağlı araba, uzaktan izleme, sahtekarlık algılaması ve çok daha fazlası gibi çok çeşitli senaryolar sunar. Azure portal, bir olay kaynağı (Olay Hub 'ı/IoT Hub) seçebilir, gelen gerçek zamanlı olayları görüntüleyebilir ve olayları depolayabilen bir tablo seçebilirsiniz. Ayrıca, gelen olayları dönüştürmek ve bunları seçili tabloda depolamak için portala Stream Analytics sorgu dili sorguları yazabilirsiniz. Bu yeni giriş noktası, Stream Analytics zaten bulunan oluşturma ve yapılandırma deneyimlerine ek niteliğindedir. Bu deneyim, veritabanınızın bağlamlarından itibaren başlar ve Azure SQL veritabanı ile Stream Analytics deneyimleriyle sorunsuz Stream Analytics bir şekilde gezinmenize olanak tanır.

![Stream Analytics akışı](media/sql-database-stream-analytics/stream-analytics-flow.png)

## <a name="key-benefits"></a>Önemli avantajlar

- En düşük bağlam değiştirme: portalda bir SQL veritabanından başlayabilir ve gerçek zamanlı verileri başka bir hizmete geçmeden bir tabloya gönderebilirsiniz. 
- Azaltılan adım sayısı: veritabanınızın ve tablonuzun bağlamı Stream Analytics bir işi önceden yapılandırmak için kullanılır.
- Önizleme verileri ile ilgili ek kullanım kolaylığı: seçili tablo bağlamında olaylar kaynağından (Olay Hub/IoT Hub) gelen verileri önizleyin 


## <a name="prerequisites"></a>Ön koşullar

Bu makaledeki adımları tamamlayabilmeniz için aşağıdaki kaynaklara ihtiyacınız vardır:

- Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun. 
- Bir SQL veritabanı. Ayrıntılar için bkz. [Azure SQL veritabanı 'nda tek bir veritabanı oluşturma](sql-database-single-database-get-started.md).
- Bilgisayarınızın Azure SQL Server 'a bağlanmasına izin veren bir güvenlik duvarı kuralı. Ayrıntılar için bkz. [sunucu düzeyinde güvenlik duvarı kuralı oluşturma](sql-database-server-level-firewall-rule.md).


## <a name="configure-stream-analytics-integration"></a>Stream Analytics tümleştirmesini yapılandırma

1. Azure Portal’da oturum açın. 
2. Akış verilerinizi almak istediğiniz SQL veritabanınıza gidin. **Stream Analytics (Önizleme)** öğesini seçin. 

    ![Stream Analytics](media/sql-database-stream-analytics/stream-analytics.png)

3. Akış verilerinizi bu SQL veritabanına aktarmak için **Oluştur** ' u seçin ve akış işinize bir ad verin ve ardından **İleri: giriş**' i seçin. 

    ![Stream Analytics işi oluştur](media/sql-database-stream-analytics/create-job.png)

4. Olaylarınızın kaynak ayrıntılarını girin ve ardından **İleri: çıkış**' ı seçin.

   - **Giriş türü**: Olay Hub 'ı/IoT Hub
   - **Giriş diğer adı**: olay kaynağınızı tanımlamak için bir ad girin 
   - **Abonelik**: SQL veritabanı aboneliğiyle aynı 
   - **Olay Hub 'ı ad alanı**: ad alanı adı 
   - **Olay Hub 'ı adı**: seçili ad alanı içindeki olay hub 'ının adı 
   - **Olay Hub 'ı ilke adı** (yeni oluşturulacak varsayılan): Ilke adı verme 
   - **Olay Hub 'ı Tüketici grubu** (yeni oluşturulacak varsayılan): Tüketici grubu adı verme  
     - Buradan oluşturduğunuz her yeni Azure Stream Analytics işi için bir tüketici grubu ve ilke oluşturmanızı öneririz. Tüketici grupları yalnızca beş eşzamanlı okuyucu sağlar; bu nedenle her iş için adanmış bir tüketici grubu sağlanması bu sınırı aşmaktan kaynaklanan hatalardan kaçınacaktır. Adanmış bir ilke, diğer kaynakları etkilemeden anahtarınızı döndürmenize veya izinleri iptal etmenize olanak tanır.

    ![Stream Analytics işi oluştur](media/sql-database-stream-analytics/create-job-output.png)

5. Akış verilerinizi almak istediğiniz tabloyu seçin. İşiniz bittiğinde **Oluştur**' u seçin.
   - **Kullanıcı adı**, **parola**: SQL Server kimlik doğrulaması için kimlik bilgilerinizi girin. **Doğrula**'yı seçin.
   - **Tablo**: **Yeni oluştur** ' u seçin veya **var olanı kullanın**. Bu akışta **Oluştur**' u seçlim. Bu, Stream Analytics işini başlattığınızda yeni bir tablo oluşturur.

    ![Stream Analytics işi oluştur](media/sql-database-stream-analytics/create.png)

6. Aşağıdaki ayrıntılarla bir sorgu sayfası açılır:

   - Veri aldığınız **giriş** (giriş olayları kaynağı)  
   - Dönüştürülmüş verileri depolayacak olan **çıktı** (çıkış tablosu) 
   - SELECT ifadesiyle örnek [Saql sorgusu](../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) . 
   - **Giriş önizleme**: giriş olayları kaynağından en son gelen verilerin anlık görüntüsünü gösterir.  
     - Verilerinizde serileştirme türü otomatik olarak algılanır (JSON/CSV). Bunu, JSON/CSV/AVRO ile el ile değiştirebilirsiniz. 
     - Gelen verileri tablo biçiminde veya ham biçimde önizleyebilirsiniz. 
     - Gösterilen veriniz güncel değilse, en son olayları görmek için **Yenile** ' yi seçin. 
     - Sorgunuzu belirli bir zaman aralığında gelen olaylara karşı test etmek için **zaman aralığını Seç** ' i seçin. 
     - Örnek bir JSON/CSV dosyası yükleyerek sorgunuzu test etmek için **örnek girişi karşıya yükle** ' yi seçin. Bir SAQL sorgusunun test edilmesi hakkında daha fazla bilgi için bkz. [Azure Stream Analytics işini örnek verilerle test](../stream-analytics/stream-analytics-test-query.md)etme. 

    ![test sorgusu](media/sql-database-stream-analytics/test-query.png)


   - **Test sonuçları**: **Test sorgusunu** seçin ve akış sorgunuzun sonuçlarını görebilirsiniz 

    ![test sonuçları](media/sql-database-stream-analytics/test-results.png)

   - **Test sonuçları şeması**: test ettikten sonra akış sorgunuzun sonuçlarının şemasını gösterir. Test sonuçları şemasının çıkış şemanız ile eşleştiğinden emin olun. 

    ![test sonuçları şeması](media/sql-database-stream-analytics/test-results-schema.png)


   - **Çıkış şeması**: Bu, 5. adımda (yeni veya var olan) seçtiğiniz tablonun şemasını içerir.
     - Yeni oluştur: 5. adımda bu seçeneği belirlediyseniz, akış işini başlatana kadar şemayı henüz görmezsiniz. Yeni bir tablo oluştururken uygun tablo dizinini seçin. Tablo dizini oluşturma hakkında daha fazla bilgi için, bkz. [kümelenmiş ve kümelenmemiş dizinler](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/).
    - Mevcut olanı kullan: 5. adımda bu seçeneği belirlediyseniz seçili tablonun şemasını görürsünüz. 
 
7. Sorguyu test etmeyi bitirdiğinizde & sorguyu **Kaydet**' i seçin. Dönüştürülmüş verileri SQL tablosuna almaya başlamak için **Stream Analytics Işini Başlat** ' ı seçin. Aşağıdaki alanları sonlandırdıktan sonra işi **başlatın** . 
   - **Çıkış başlangıç zamanı**: Bu, işin ilk çıktısının saatini tanımlar.  
     - Şimdi: iş şimdi başlayacak ve yeni gelen verileri işleyecek.
     - Özel: iş şimdi başlayacaktır, ancak belirli bir zaman noktasından (geçmişte veya gelecekte olabilir) veri işleyecek. Daha fazla bilgi için bkz. [Azure Stream Analytics işi başlatma](../stream-analytics/start-job.md).
   - **Akış birimleri**: Azure Stream Analytics, verileri hizmette işlemek için gereken Akış Birimi sayısına göre fiyatlandırılır. Daha fazla bilgi için bkz. [Azure Stream Analytics fiyatlandırması](https://azure.microsoft.com/pricing/details/stream-analytics/). 
   - **Çıkış verileri hata işleme**:  
     - Yeniden deneme: bir hata oluştuğunda, yazma başarılı olana kadar olayın süresiz olarak yazılmasına yeniden denemeler Azure Stream Analytics. Yeniden denemeler için zaman aşımı yok. Sonuç olarak, sonraki tüm olayların yeniden denenecek olay tarafından işlenmesi engellenir. Bu seçenek, varsayılan çıkış hatası işleme ilkesidir. 
     - Bırak: Azure Stream Analytics, veri dönüştürme hatasıyla sonuçlanan tüm çıktı olaylarını bırakacak. Bırakılan olaylar daha sonra yeniden işleme için kurtarılamaz. Tüm geçici hatalar (örneğin, ağ hataları), ilke yapılandırmasından oluşan çıkış hatası işleme ne olursa olsun yeniden denenir. 
   - **SQL veritabanı çıkış ayarları**: tabloya birden çok yazıcı ile tam paralel topolojiyi etkinleştirmek için önceki sorgu adımlarınızın bölümleme şemasını devralma seçeneği. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'na Azure Stream Analytics çıktısı](../stream-analytics/stream-analytics-sql-output-perf.md).
   - **En fazla yığın sayısı**: her toplu ekleme hareketiyle gönderilen kayıt sayısı için önerilen üst sınır.  
    Çıkış hatası işleme hakkında daha fazla bilgi için bkz. [Azure Stream Analytics çıkış hatası ilkeleri](../stream-analytics/stream-analytics-output-error-policy.md).  

    ![işi Başlat](media/sql-database-stream-analytics/start-job.png)

8. İşi başlattığınızda, çalışmakta olan işi listede görürsünüz ve aşağıdaki eylemleri gerçekleştirebilirsiniz: 
   - **Işi Başlat/Durdur**: iş çalışıyorsa işi durdurabilirsiniz. İş durdurulmuşsa, işi başlatabilirsiniz. 
   - **Işi Düzenle**: sorguyu düzenleyebilirsiniz. İş EX 'de daha fazla değişiklik yapmak istiyorsanız, daha fazla giriş/çıkış ekleyin ve sonra Stream Analytics içinde işi açın. İş çalışırken Düzenle seçeneği devre dışıdır. 
   - **Önizleme çıkış tablosu**: SQL sorgu Düzenleyicisi 'nde tablonun önizlemesini yapabilirsiniz. 
   - **Stream Analytics açın**: işi Stream Analytics hizmetinde açarak izleme, işin hata ayıklama ayrıntılarını görüntüleyin. 


    ![Stream Analytics işleri](media/sql-database-stream-analytics/jobs.png)






## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stream Analytics belgeleri](https://docs.microsoft.com/azure/stream-analytics/)
- [Azure Stream Analytics çözüm desenleri](../stream-analytics/stream-analytics-solution-patterns.md)
