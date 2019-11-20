---
title: Azure Stream Analytics çıkışları sorunlarını giderme
description: Bu makalede, çıkış bağlantılarınızı Azure Stream Analytics işlerinde sorun giderme teknikleri açıklar.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: a07ac40ad3adda486b5216e83d683e00ec93265d
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620786"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Azure Stream Analytics çıkışları sorunlarını giderme

Bu sayfa, çıkış bağlantılarını ve sorun giderme ve onları adreslemek ile ilgili yaygın sorunları açıklar.

## <a name="output-not-produced-by-job"></a>Çıktı iş tarafından üretilen değil 
1.  Kullanarak çıkış bağlantısını doğrulayın **Test Bağlantısı** her çıkış düğmesi.

2.  Bakmak [ **izleme ölçümleri** ](stream-analytics-monitoring.md) üzerinde **İzleyici** sekmesi. Değerler toplanır çünkü ölçümleri birkaç dakika gecikir.
    - Giriş olayları > 0 ise, iş giriş verilerini okuyabilir. Giriş olayları > 0 ise, ardından değilse:
      - Veri kaynağı geçerli veriler olup olmadığını görmek için onu kullanarak kontrol [hizmet veri yolu Gezgini](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Bu denetim, iş girdisi olarak olay hub'ı kullanıyorsanız geçerlidir.
      - Veri seri hale getirme biçiminin ve kodlama verileri beklendiği gibi olup olmadığını denetleyin.
      - İleti gövdesi olup olmadığını görmek için işin bir olay hub'ı kullanıyorsanız, denetleyin *Null*.
      
    - Veri dönüştürme hataları > 0 ve tırmanma, aşağıdaki olabilir true:
      - Çıkış olayı bir hedef havuz şemaya uymuyor. 
      - Olay şeması sorguda olayların tanımlı veya beklenen şema eşleşmeyebilir.
      - Bazı alanların veri türlerini beklentileri olay eşleşmeyebilir.
      
    - Çalışma zamanı hataları > 0 ise, geldiğini iş verileri alabilir ancak sorguyu işlerken hata oluşturuyor.
      - Hataları bulmak için Git [denetim günlüklerini](../azure-resource-manager/resource-group-audit.md) ve filtre *başarısız* durumu.
      
    - Varsa Inputevents > 0 ve OutputEvents = 0, aşağıdakilerden birini true olduğu anlamına gelir:
      - Sorgu işleme sıfır çıkış olayıyla sonuçlandı.
      - Olaylar veya bunların alanları sonra sorgu işleme sıfır çıkış kaynaklanan hatalı olabilir.
      - İş çıkış havuzuna bağlantı veya kimlik doğrulama nedenleriyle veri gönderme oluşturamadı.
      
    - Tüm daha önce bahsedilen hata durumlarda, işlem günlüğü iletilerine (olanlar dahil) ek ayrıntılar açıklayan dışındaki sorgu mantığının tüm olayları da burada filtre uygulanmış durumda. Birden çok olayın işlenmesi hataların oluşturursa, işlem günlükleri 10 dakika içerisinde aynı türdeki ilk üç hata iletileri Stream Analytics günlüğe kaydeder. Ardından aynı hataların diğer örneklerini "Hatalar çok hızlı gerçekleşiyor, bunlar gizlenen olay meydana gelir." yazan iletisiyle bastırır
    
## <a name="job-output-is-delayed"></a>İş çıktısı ertelendi

### <a name="first-output-is-delayed"></a>İlk çıkış ertelendi
Bir Stream Analytics işi başladığında, giriş olayları okumak, ancak bazı durumlarda üretilen çıktıda bir gecikme olabilir.

Zamana bağlı sorgu öğeleri büyük saat değerleri için çıkış gecikmesi katkıda bulunabilir. Büyük zaman pencereleri doğru çıktı oluşturmak için iş akışında zaman penceresi doldurmak için en son zaman mümkün (en fazla yedi gün önce) verilerini okuyarak başlatılır. Bekleyen giriş olaylarını yakalama okuma işlemi tamamlanana kadar bu süre boyunca hiçbir çıktı üretilmiştir. Sistem, böylece iş yeniden başlatma akış işi, yükseltildiğinde bu sorun ortaya çıkabilir. Bu tür yükseltmeler, genellikle bir kez her birkaç ay oluşur. 

Bu nedenle, Stream Analytics sorgunuz tasarlarken dikkatli olun. Büyük bir zaman penceresinde'nı kullanıyorsanız (birden fazla birkaç saat ayarlama için yedi gün) iş başlatıldığında veya yeniden başlatıldığında işin sorgu söz dizimi, zamana bağlı öğeleri için bunu bir gecikme için ilk çıktı yol açabilir.  

İlk çıkış gecikme bu tür için bir risk azaltma, sorgunun paralelleştirme teknikleri (veriyi bölümlendirme) kullanın veya daha fazla akış işi arayı kapatıncaya kadar işleme oranını artırmak için birim ekleyin sağlamaktır.  Daha fazla bilgi için [Stream Analytics işleri oluştururken dikkat edilecek noktalar](stream-analytics-concepts-checkpoint-replay.md)

Bu etkenler oluşturulan ilk çıkışın dakikliğini etkiler:

1. Pencereli toplamlar (grup tarafından atlaması ve windows kayan atlayan,) kullanımı
   - Atlayan veya atlamalı pencere toplamlar için sonuçları penceresi süre sonunda oluşturulur. 
   - Bir olay girer veya kayan pencere çıkar, kayan bir pencere için sonuçları üretilir. 
   - Büyük pencere boyutu (> 1 saat) kullanmayı planlıyorsanız, böylece çıkışı daha sık bakın atlamalı veya kayan bir pencere seçmek idealdir.

2. Zamana bağlı birleşimler (DATEDIFF katılma) kullanımı
   - Eşleşen her iki tarafında eşleşen olaylar geldiğinde hemen sonra oluşturulur.
   - Bir eşleşme (sol dış birleştirme) eksik veri sonunda, DATEDIFF pencerenin sol tarafındaki her bir olay göre oluşturulur.

3. Zamana bağlı analitik işlevler (ISFİRST, LAST ve GECİKME süresi sınırı) kullanımı
   - Analitik İşlevler, her olay için bir çıktı oluşturulur, gecikme.

### <a name="output-falls-behind"></a>Çıkış geride
İşin normal işlem sırasında işin çıktısını (uzun ve daha uzun gecikme), geride kalıyor fark ederseniz bu faktörleri inceleyerek nedenlerini saptayabilirler:
- Aşağı Akış havuz olup kısıtlandı
- Yukarı Akış kaynağı olup olmadığını kısıtlandı
- Sorgu işleme mantığı yoğun işlem gücü kullanımlı olup olmadığı

Azure portalında ayrıntılarını görmek için iş akışında seçip **iş diyagramı**. Her bir giriş var olan bir bölüm biriktirme listesi olay ölçüm. Biriktirme listesi olay ölçümü artmaya devam ederse, sistem kaynaklarının sınırlı olduğu bir göstergesidir. Potansiyel olarak verilecek çıkış havuzu kısıtlama veya yüksek CPU olmasıdır. İş diyagramı kullanma hakkında daha fazla bilgi için bkz. [veri odaklı iş diyagramı kullanarak hata ayıklama](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Azure SQL veritabanı çıkışı ile anahtar ihlali uyarısı

Azure SQL veritabanı için bir Stream Analytics işi çıktı olarak yapılandırdığınızda, yığın kayıtları hedef tabloya ekler. Genel olarak, Azure stream analytics garanti eder [en az bir kere teslim](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) bir çıkış havuzuna yine de [tam olarak elde-kez teslim]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) SQL tablosu, tanımlı bir kısıtlama olduğunda SQL çıktı. 

Azure Stream Analytics, benzersiz anahtar kısıtlamaları SQL tablosunda ayarlanır ve SQL tablosuna eklenen yinelenen kayıt sonra yinelenen kayıt kaldırır. Toplu işleri ve yinelemeli olarak tek bir yinelenen kayıt bulunana kadar toplu ekleme verileri ayırır. Akış işi önemli sayıda yinelenen satır, bu bölme olduğundan ve işlem eklemek daha az verimli ve zaman alıcı olan tek, yinelenenleri yok saymak vardır. Etkinlik günlüğü'nde son bir saat içinde birden çok anahtar ihlali uyarı iletisi görürseniz, SQL çıkışınızı tüm işin yavaşlattığını olasıdır. 

Bu sorunu gidermek için şunları yapmalısınız [dizini yapılandırma]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) , neden anahtar ihlali IGNORE_DUP_KEY seçeneğini etkinleştirerek. Bu seçeneğin etkinleştirilmesi, SQL toplu ekleme sırasında yoksayılacak yinelenen değerler sağlar ve SQL Azure, yalnızca bir uyarı iletisi yerine bir hata üretir. Azure Stream Analytics artık birincil anahtar ihlali hatalarını üretmez.

IGNORE_DUP_KEY dizin çeşitli türleri için yapılandırırken aşağıdaki gözlemlere unutmayın:

* Bir birincil anahtar veya ALTER INDEX kullanan benzersiz kısıtlama IGNORE_DUP_KEY ayarlayamazsınız, bırakın ve dizini yeniden oluşturmanız gerekir.  
* BİRİNCİL anahtar benzersiz kısıtlamasından farklıdır ve CREATE INDEX veya dizin tanımı kullanılarak oluşturulan benzersiz bir dizin için ALTER INDEX kullanarak IGNORE_DUP_KEY seçeneği ayarlayabilirsiniz.  
* Bu dizinlerin benzersizlik olamaz çünkü IGNORE_DUP_KEY sütun deposu dizinleri için geçerli değildir.  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>Azure Stream Analytics tarafından küçük harfleri sütun adları
Özgün uyumluluk düzeyini (1.0) kullanırken, Azure Stream Analytics küçük harflere sütun adlarını değiştirmek için kullanılır. Bu davranış, sonraki Uyumluluk Düzeyleri düzeltildi. Durum korumak için biz 1.1 ve üzeri uyumluluk düzeyine geçmeye müşterilerimize. Daha fazla bilgi bulabilirsiniz [Azure Stream Analytics işleri için uyumluluk düzeyi](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level).


## <a name="get-help"></a>Yardım alın

Daha fazla yardım için deneyin bizim [Azure Stream Analytics forumumuzu](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stream analytics'e giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)
