---
title: Örnek verilerle Azure Stream Analytics işi test etme
description: Bu makalede, bir Azure Stream Analytics işini, örnek girişi test etmek ve örnek verileri karşıya yüklemek için Azure portal nasıl kullanılacağı açıklanır.
author: ajetasin
ms.author: ajetasi
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: eff9103f476e6074ab46198ff8cc78588675569f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020052"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>Portalda Azure Stream Analytics işi test etme

Azure Stream Analytics, işinizi başlatmadan veya durdurmaksızın sorgunuzu test edebilirsiniz. Akış kaynaklarınızdaki gelen verilerde bulunan sorguları test edebilir veya Azure portalında yerel bir dosyadan örnek verileri yükleyebilirsiniz. Sorguları yerel örnek verilerden veya [Visual Studio](stream-analytics-live-data-local-testing.md) 'daki canlı verilerden veya [Visual Studio Code](visual-studio-code-local-run-live-input.md)yerel olarak da test edebilirsiniz.

## <a name="automatically-sample-incoming-data-from-input"></a>Girişten gelen verileri otomatik olarak örnekle

Azure Stream Analytics akış girişlerinizin olaylarını otomatik olarak getirir. Varsayılan örnekteki sorguları çalıştırabilir veya örnek için belirli bir zaman çerçevesini ayarlayabilirsiniz.

1. Azure portalında oturum açın.

2. Mevcut Stream Analytics işinizi bulun ve seçin.

3. Stream Analytics işi sayfasında, **Iş topolojisi** başlığı altında **sorgu** ' yı seçerek sorgu Düzenleyicisi penceresini açın. 

4. Gelen olayların örnek listesini görmek için dosya simgesi olan giriş ' i seçin ve örnek olaylar **giriş önizlemede** otomatik olarak görünür.

   a. Verileriniz için serileştirme türü, JSON veya CSV olursa otomatik olarak algılanır. Açılır menüdeki seçeneğini değiştirerek JSON, CSV, AVRO el ile de değiştirebilirsiniz.
    
   b. Verilerinizi **tablo** veya **Ham** biçimde görüntülemek için seçiciyi kullanın.
    
   c. Gösterilen veriniz güncel değilse, en son olayları görmek için **Yenile** ' yi seçin.

   Aşağıdaki tablo, **tablo biçimindeki** verilerin bir örneğidir:

   ![Tablo biçiminde örnek girişi Azure Stream Analytics](./media/stream-analytics-test-query/asa-sample-table.png)

   Aşağıdaki tablo, **Ham biçimdeki** verilerin bir örneğidir:

   ![Ham biçimde Azure Stream Analytics örnek girişi](./media/stream-analytics-test-query/asa-sample-raw.png)

5. Sorgunuzu gelen verilerle test etmek için **Test sorgusu**' nu seçin. Sonuçlar, **test sonuçları** sekmesinde görünür. Sonuçları indirmek için **sonuçları indir** ' i de seçebilirsiniz.

   ![Azure Stream Analytics örnek testi sorgu sonuçları](./media/stream-analytics-test-query/asa-test-query.png)

6. Sorgunuzu belirli bir zaman aralığı gelen olaylara karşı test etmek için **Saat aralığını Seç**' i seçin.
   
   ![Gelen örnek olaylar için zaman aralığını Azure Stream Analytics](./media/stream-analytics-test-query/asa-select-time-range.png)

7. Sorgunuzu test etmek için kullanmak istediğiniz olayların zaman aralığını ayarlayın ve **örnek**' i seçin. Bu zaman çerçevesinde, en fazla 1000 olay veya 1 MB, hangisi önce geldiğini elde edebilirsiniz.

   ![Gelen örnek olaylar için zaman aralığını ayarla Azure Stream Analytics](./media/stream-analytics-test-query/asa-set-time-range.png)

8. Olaylar seçili zaman aralığı için örneklendikten sonra, **giriş önizleme** sekmesinde görünürler.

   ![Azure Stream Analytics test sonuçlarını görüntüle](./media/stream-analytics-test-query/asa-view-test-results.png)

9. Gelen olayların örnek listesini görmek için **Sıfırla** ' yı seçin. **Sıfırla**' yı seçerseniz, zaman aralığı seçiminiz kaybedilir. Sorgunuzu test etmek için **Test sorgusu** ' nu seçin ve sonuçları **test sonuçları** sekmesinde gözden geçirin.

10. Sorgunuzda değişiklik yaptığınızda, yeni sorgu mantığını test etmek için **sorguyu Kaydet** ' i seçin. Bu, sorgunuzu yinelemeli olarak değiştirmenize ve çıktının nasıl değiştiği hakkında daha fazla test etmenize olanak tanır.

11. Tarayıcıda gösterilen sonuçları doğruladıktan sonra işi **başlatmaya** hazırsınız demektir.

## <a name="upload-sample-data-from-a-local-file"></a>Yerel bir dosyadan örnek verileri karşıya yükleme

Canlı verileri kullanmak yerine, Azure Stream Analytics sorgunuzu test etmek için yerel bir dosyadaki örnek verileri kullanabilirsiniz.

1. Azure portalında oturum açın.
   
2. Mevcut Stream Analytics işinizi bulun ve seçin.

3. Stream Analytics işi sayfasında, **Iş topolojisi** başlığı altında **sorgu** ' yı seçerek sorgu Düzenleyicisi penceresini açın.

4. Sorgunuzu yerel bir dosya ile test etmek için, **giriş önizleme** sekmesinde **örnek girişi yükle** ' yi seçin. 

   ![Ekran görüntüsü örnek girişi yükle seçeneğini gösterir.](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. Sorguyu test etmek için yerel dosyanızı karşıya yükleyin. Dosyaları yalnızca JSON, CSV veya AVRO biçimleriyle karşıya yükleyebilirsiniz. **Tamam**’ı seçin.

   ![Ekran görüntüsü, dosya seçebileceğiniz örnek verileri karşıya yükle iletişim kutusunu gösterir.](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. Dosyayı karşıya yükledikten hemen sonra form içindeki dosya içeriğini tablo olarak veya ham biçiminde de görebilirsiniz. **Sıfırla**' yı seçerseniz, örnek veriler önceki bölümde açıklanan gelen giriş verilerine geri döner. Sorguyu dilediğiniz zaman test etmek için başka bir dosya yükleyebilirsiniz.

7. Sorguyu karşıya yüklenen örnek dosyaya karşı test etmek için **Test sorgusu** ' nu seçin.

8. Test sonuçları, sorgunuza göre gösterilir. Sorgunuzu değiştirebilir ve sorguyu **Kaydet** ' i seçerek yeni sorgu mantığını test edebilirsiniz. Bu, sorgunuzu yinelemeli olarak değiştirmenize ve çıktının nasıl değiştiği hakkında daha fazla test etmenize olanak tanır.

9. Sorguda birden çok çıktı kullandığınızda, sonuçlar seçili çıktıya göre gösterilir. 

   ![Seçili çıktıyı Azure Stream Analytics](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. Tarayıcıda gösterilen sonuçları doğruladıktan sonra, işi **başlatabilirsiniz** .

## <a name="limitations"></a>Sınırlamalar

1.  Portal testinde zaman ilkesi desteklenmez:

    * Sıra dışı: tüm gelen olaylar sıralanacaktır.
    * Geç varış: Stream Analytics test için yalnızca varolan verileri kullanabilmesi için geç varış olayı olmayacaktır.
   
2.  C# UDF desteklenmez.

3.  Tüm testler, bir akış birimi olan bir iş ile çalıştırılır.

4.  Zaman aşımı boyutu bir dakikadır. Böylece pencere boyutu bir dakikadan büyük olan herhangi bir sorgu herhangi bir veri alamaz.

5.  Machine Learning desteklenmez.

6. Örnek veri API 'SI, 15 dakikalık bir penceredeki beş istekten sonra kısıtlanıyor. 15 dakikalık pencerenin sonundan sonra daha fazla örnek veri isteği gerçekleştirebilirsiniz. Bu sınırlama, abonelik düzeyinde uygulanır.

## <a name="troubleshooting"></a>Sorun giderme

1.  Bu hatayı alırsanız "sonuçlar getirilirken bir ağ bağlantısı sorunu vardı. Lütfen ağınızı ve güvenlik duvarı ayarlarınızı denetleyin. ", aşağıdaki adımları izleyin:

  * Hizmetle bağlantıyı denetlemek için [https://queryruntime.azurestreamanalytics.com/api/home/index](https://queryruntime.azurestreamanalytics.com/api/home/index) bir tarayıcıda açın. Bu bağlantıyı açamazsınız, güvenlik duvarı ayarlarınızı güncelleştirin.
  
2. Bu hatayı alırsanız "istek boyutu çok büyük. Lütfen giriş verilerinin boyutunu azaltın ve yeniden deneyin. ", aşağıdaki adımları izleyin:

  * Giriş boyutunu küçültün: sorgunuzu daha küçük boyutlu örnek dosyayla veya daha küçük bir zaman aralığıyla test edin.
  * Sorgu boyutunu azalt – bir sorgu seçimini test etmek Için, sorgunun bir kısmını seçin ve ardından **Seçili sorguyu sına**' ya tıklayın.


## <a name="next-steps"></a>Sonraki adımlar
* [Stream Analytics kullanarak bir IoT çözümü oluşturun](./stream-analytics-build-an-iot-solution-using-stream-analytics.md): Bu öğretici, ücretsiz olarak trafiği taklit edecek bir veri Oluşturucu ile uçtan uca bir çözüm oluşturmanıza kılavuzluk eder.

* [Azure Akış Analizi Sorgu Dili Başvurusu](/stream-analytics-query/stream-analytics-query-language-reference)

* [Ortak Stream Analytics kullanım desenlerine yönelik sorgu örnekleri](stream-analytics-stream-analytics-query-patterns.md)

* [Azure Stream Analytics için girişleri anlayın](stream-analytics-add-inputs.md)

* [Azure Stream Analytics çıkışlarını anlayın](stream-analytics-define-outputs.md)
