---
title: Azure Stream Analytics çıkışları sorunlarını giderme
description: Bu makalede Azure Stream Analytics işlerinde çıkış bağlantılarınızın sorunlarını gidermeye yönelik teknikler açıklanmaktadır.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 10/05/2020
ms.custom: seodec18
ms.openlocfilehash: 02a3a7ad73bf0434a215c5ab7a6e89c299e9518b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019865"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Azure Stream Analytics çıkışları sorunlarını giderme

Bu makalede Azure Stream Analytics çıkış bağlantılarıyla ilgili yaygın sorunlar ve çıkış sorunlarının nasıl giderileceği açıklanmaktadır. Birçok sorun giderme adımı, Stream Analytics işiniz için kaynak ve diğer tanılama günlüklerinin etkinleştirilmesini gerektirir. Kaynak günlüklerinizi etkinleştirmediyseniz, bkz. [Azure Stream Analytics sorun giderme kaynak günlüklerini kullanarak](stream-analytics-job-diagnostic-logs.md).

## <a name="the-job-doesnt-produce-output"></a>İş çıkış üretmez

1. Her çıkış için **Bağlantıyı Sına** düğmesini kullanarak çıkışlara bağlantıyı doğrulayın.
1. **İzleyici** sekmesinde [izleme ölçümleri](stream-analytics-monitoring.md) ' ne bakın. Değerler toplanmış olduğundan ölçümler birkaç dakika gecikiyor.

   * **Giriş olayı** değeri sıfırdan büyükse, iş giriş verilerini okuyabilir. **Giriş olayları** değeri sıfırdan büyük değilse, iş girişiyle ilgili bir sorun vardır. Daha fazla bilgi için bkz. [giriş bağlantılarında sorun giderme](stream-analytics-troubleshoot-input.md) . İşinizin başvuru verileri girişi varsa, **giriş olayları** ölçümünü ararken mantıksal ada göre bölme uygulayın. Başvuru verilerinizde tek başına giriş olayı yoksa, bu giriş kaynağının doğru başvuru veri kümesini getirmek için doğru şekilde yapılandırılmadığı anlamına gelir.
   * **Veri dönüştürme hatalarının** değeri sıfırdan büyükse ve bu hata, veri dönüştürme hatalarıyla ilgili ayrıntılı bilgi için bkz. [Azure Stream Analytics veri hataları](data-errors.md) .
   * **Çalışma zamanı hataları** değeri sıfırdan büyükse, işiniz verileri alır ancak sorguyu işlerken hata oluşturur. Hataları bulmak için [Denetim günlüklerine](../azure-resource-manager/management/view-activity-logs.md)gidin ve **başarısız** durumuna filtre uygulayın.
   * **Giriş olayları** değeri sıfırdan büyükse ve **Çıkış olayları** değeri sıfıra eşitse, aşağıdaki deyimlerden biri doğrudur:
      * Sorgu işleme sıfır çıkış olayları ile sonuçlandı.
      * Olaylar veya alanlar hatalı olabilir ve sorgu işlemeden sonra sıfır çıkış elde edilir.
      * İş, bağlantı veya kimlik doğrulama nedenleriyle verileri çıkış havuzuna gönderemedi.

   İşlem günlüğü iletileri, sorgu mantığının tüm olayları filtrelediğini kapsayan durumlar dahil olmak üzere ek ayrıntılar açıklar. Birden çok olayın işlenmesi hata oluşturursa, hatalar her 10 dakikada bir toplar.

## <a name="the-first-output-is-delayed"></a>İlk çıkış gecikiyor

Stream Analytics bir iş başladığında, giriş olayları okundu. Ancak belirli durumlarda çıktıda bir gecikme olabilir.

Zamana bağlı sorgu öğelerinde büyük saat değerleri, çıkış gecikmesine katkıda bulunabilir. Büyük zaman pencereleri üzerinde doğru çıktıyı oluşturmak için, akış işi zaman penceresini dolduracak olabilecek en son zamandan verileri okur. Veriler, en fazla yedi gün geçmiş olabilir. Bekleyen giriş olayları okunana kadar çıkış üretmez. Bu sorun, sistem akış işlerini yükselttiğinde yüzeysel olabilir. Bir yükseltme gerçekleştiğinde iş yeniden başlatılır. Bu tür yükseltmeler genellikle her iki ayda bir kez gerçekleşir.

Stream Analytics sorgunuzu tasarlarken dikkatli kullanın. İşin sorgu sözdiziminde zamana bağlı öğeler için büyük bir zaman penceresi kullanırsanız, iş başladığında veya yeniden başlatıldığında ilk çıktıda gecikmeye neden olabilir. Birkaç saat, yedi güne kadar, büyük bir zaman penceresi olarak kabul edilir.

Bu tür ilk çıkış gecikmesi için bir hafifletme, verileri bölümlemek gibi sorgu paralelleştirme tekniklerini kullanmaktır. Ya da iş bitene kadar aktarım hızını artırmak için daha fazla akış birimi ekleyebilirsiniz.  Daha fazla bilgi için bkz. [Stream Analytics işleri oluşturma konuları](stream-analytics-concepts-checkpoint-replay.md).

Bu faktörler, ilk çıktının zaman çizelgesini etkiler:

* İçe geçmiş, atlamalı ve kayan pencerelerin GROUP BY yan tümcesi gibi pencereli toplamaların kullanımı:

  * Atlayan veya atlamalı pencere toplamaları için sonuçlar pencere zaman dilimi sonunda oluşturulur.
  * Kayan bir pencere için sonuçlar, bir olay, kayan pencere girdiğinde ya da çıktığında oluşur.
  * Birden çok saat gibi büyük bir pencere boyutu kullanmayı planlıyorsanız, bir atlamalı veya kayan pencere seçmek en iyisidir. Bu pencere türleri çıktıyı daha sık görmenizi sağlar.

* DATEDIFF ile BIRLEŞTIRME gibi zamana bağlı birleşimler kullanımı:
  * Eşleşen olayların her iki tarafı geldikçe oluştur ' a uyar.
  * Sol dış BIRLEŞIM gibi bir eşleşmede olmayan veriler, sol taraftaki her olay için DATEDıFF penceresinin sonunda oluşturulur.

* ISFIRST, LAST ve LIMIT SÜRESI ile GECIKME gibi zamana bağlı analitik işlevlerin kullanımı:
  * Analitik işlevler için, çıktı her olay için oluşturulur. Gecikme yok.

## <a name="the-output-falls-behind"></a>Çıkış geride

Bir işin normal işlemi sırasında, çıktıda daha uzun ve daha uzun gecikme süreleri olabilir. Çıkış, bunun gibi kalırsa, aşağıdaki faktörleri inceleyerek kök nedenlerini de belirleyebilirsiniz:

* Aşağı akış havuzunun kısıtlanıp kısıtlanmayacağı
* Yukarı akış kaynağının kısıtlanıp kısıtlanmayacağı
* Sorgudaki işleme mantığının işlem yoğunluğu olup olmadığı

Çıkış ayrıntılarını görmek için Azure portal akış işini seçin ve sonra **iş diyagramı**' nı seçin. Her giriş için, bölüm başına bir kapsam olay ölçümü vardır. Ölçüm artmaya devam ederse, sistem kaynaklarının kısıtlandığı bir göstergedir. Artış, büyük olasılıkla çıkış havuzu azaltma veya yüksek CPU kullanımı nedeniyle olabilir. Daha fazla bilgi için, bkz. [iş diyagramını kullanarak veri odaklı hata ayıklama](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Azure SQL veritabanı çıkışıyla anahtar ihlali uyarısı

Bir Azure SQL veritabanını bir Stream Analytics işine çıktı olarak yapılandırdığınızda, kayıt, hedef tabloya toplu ekler. Genel olarak, Azure Stream Analytics çıkış havuzuna [en az bir kez teslim](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) garantisi verir. Bir SQL tablosu tanımlı benzersiz bir kısıtlama olduğunda, bir SQL çıktısına [tam bir kez gönderim elde]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) edebilirsiniz.

SQL tablosunda benzersiz anahtar kısıtlamalarını ayarlarken Azure Stream Analytics yinelenen kayıtları kaldırır. Verileri toplu işlemlere böler ve tek bir yinelenen kayıt bulunana kadar toplu işleri ekler. Bölünmüş ve araya ekleme işlemi, yinelenenleri tek seferde yoksayar. Çok sayıda yinelenen satır içeren bir akış işi için, işlem verimsiz ve zaman alabilir. Önceki saatte etkinlik günlüğünde birden çok anahtar ihlali uyarı iletisi görürseniz, büyük olasılıkla SQL çıktınızın tüm işi yavaşlatıyor olması olasıdır.

Bu sorunu çözmek için, IGNORE_DUP_KEY seçeneğini etkinleştirerek anahtar ihlaline neden olan [dizini yapılandırın]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) . Bu seçenek, toplu ekleme sırasında SQL 'in yinelenen değerleri yoksaymasına olanak tanır. Azure SQL veritabanı, hata yerine yalnızca bir uyarı iletisi üretir. Sonuç olarak, Azure Stream Analytics artık birincil anahtar ihlali hataları üremez.

Birkaç dizin türü için IGNORE_DUP_KEY yapılandırırken aşağıdaki gözlemleri aklınızda edin:

* Birincil anahtarda veya ALTER INDEX kullanan benzersiz bir kısıtlamada IGNORE_DUP_KEY ayarlayamazsınız. Dizini bırakıp yeniden oluşturmanız gerekir.  
* Benzersiz bir dizin için ALTER INDEX kullanarak IGNORE_DUP_KEY belirleyebilirsiniz. Bu örnek, bir BIRINCIL ANAHTARDAN/BENZERSIZ kısıtlamadan farklıdır ve oluşturma DIZINI veya DIZIN tanımı kullanılarak oluşturulur.  
* IGNORE_DUP_KEY seçeneği, kendileri üzerinde benzersizlik zorlayamadığı için sütun deposu dizinlerine uygulanmaz.

## <a name="sql-output-retry-logic"></a>SQL çıkışı yeniden deneme mantığı

SQL çıktısı olan Stream Analytics bir iş, ilk olay toplu işlemini aldığında aşağıdaki adımlar oluşur:

1. İş SQL 'e bağlanmaya çalışır.
2. İş, hedef tablonun şemasını getirir.
3. İş, sütun adlarını ve türlerini hedef tablo şemasına göre doğrular.
4. İş, toplu işteki çıktı kayıtlarından bir bellek içi veri tablosu hazırlar.
5. İş, veri tablosunu BulkCopy [API](/dotnet/api/system.data.sqlclient.sqlbulkcopy.writetoserver)kullanarak SQL 'e yazar.

Bu adımlar sırasında, SQL çıktısı aşağıdaki hata türleriyle karşılaşabilir:

* Bir üstel geri alma yeniden deneme stratejisi kullanılarak yeniden denenen geçici [hatalar](../azure-sql/database/troubleshoot-common-errors-issues.md#transient-fault-error-messages-40197-40613-and-others) . En düşük yeniden deneme aralığı, bireysel hata koduna bağlıdır, ancak aralıklar genellikle 60 saniyeden düşüktür. Üst sınır en fazla beş dakika olabilir. 

   [Oturum açma hataları](../azure-sql/database/troubleshoot-common-errors-issues.md#unable-to-log-in-to-the-server-errors-18456-40531) ve [güvenlik duvarı sorunları](../azure-sql/database/troubleshoot-common-errors-issues.md#cannot-connect-to-server-due-to-firewall-issues) , önceki denemelerinden en az 5 dakika sonra yeniden denenir ve başarılı olana kadar yeniden denenir.

* Hataları ve şema kısıtlama ihlallerini atama gibi veri hataları çıktı hata ilkesiyle işlenir. Bu hatalar, hataya neden olan tek bir kayıt, atla veya yeniden dene tarafından işlenene kadar ikili bölme toplu işleri yeniden denenerek işlenir. Birincil benzersiz anahtar kısıtlaması ihlali [her zaman işlenir](./stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output).

* SQL hizmet sorunları veya iç kod hataları olduğunda geçici olmayan hatalar meydana gelebilir. Örneğin, benzer hatalar (kod 1132) depolama sınırına ulaşarak Elastik Havuz, yeniden denemeler hatayı çözmez. Bu senaryolarda Stream Analytics işi [azalmaya neden olur](job-states.md).
* `BulkCopy` 5. adımda zaman aşımları meydana gelebilir `BulkCopy` . `BulkCopy` işlem zaman aşımları zaman zaman oluşabilir. En az yapılandırılan zaman aşımı süresi beş dakikadır ve arka arkaya isabet edildiğinde iki katına çıkar.
Zaman aşımı 15 dakikadan daha uzun bir süre sonra, yığın başına 100 olay için en fazla toplu iş boyutu ipucu değeri `BulkCopy` yarıya düşürülür.

## <a name="column-names-are-lowercase-in-azure-stream-analytics-10"></a>Sütun adları küçük harfle Azure Stream Analytics (1,0)

Orijinal uyumluluk düzeyini (1,0) kullanırken, Azure Stream Analytics sütun adlarını küçük harfe dönüştürür. Bu davranış sonraki uyumluluk düzeylerinde düzeltildi. Durumu korumak için uyumluluk düzeyi 1,1 veya sonraki bir sürüme gidin. Daha fazla bilgi için bkz. [Stream Analytics işler Için uyumluluk düzeyi](./stream-analytics-compatibility-level.md).

## <a name="get-help"></a>Yardım alın

Daha fazla yardım için, [Azure Stream Analytics Için Microsoft Q&soru sayfasını](/answers/topics/azure-stream-analytics.html)deneyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stream Analytics giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics sorgu dili başvurusu](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics yönetim REST API başvurusu](/rest/api/streamanalytics/)