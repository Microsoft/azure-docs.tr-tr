---
title: Azure SQL veritabanı 'na Azure Stream Analytics çıkışı
description: Azure Stream Analytics SQL Azure veri çıktısı alma ve daha yüksek yazma işleme ücretleri elde etme hakkında bilgi edinin.
services: stream-analytics
author: chetanmsft
ms.author: chetang
manager: katiiceva
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 7845833a0269514c8fdbd093e18d4516ff9567d9
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173011"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>Azure SQL veritabanı 'na Azure Stream Analytics çıkışı

Bu makalede Azure Stream Analytics kullanarak SQL Azure veritabanına veri yüklerken daha iyi yazma performansı elde etme ipuçları ele alınmaktadır.

Azure Stream Analytics içindeki SQL çıktısı, bir seçenek olarak paralel yazmayı destekler. Bu seçenek, birden çok çıkış bölümünün paralel olarak hedef tabloya yazıldığı [tam paralel](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) iş Topolojilerine izin verir. Azure Stream Analytics bu seçeneğin etkinleştirilmesi, SQL Azure veritabanı yapılandırmanıza ve tablo şemanıza önemli ölçüde bağlı olduğundan daha yüksek bir işlem elde etmek için yeterli olmayabilir. Dizinler, kümeleme anahtarı, Dizin doldurma faktörü ve sıkıştırma seçimi, tabloları yükleme zamanına göre bir etkiye sahiptir. SQL Azure veritabanınızı, iç kıyaslamalar temelinde sorgu ve yükleme performansını iyileştirmek üzere en iyi hale getirmeye yönelik daha fazla bilgi için bkz. [SQL veritabanı performans Kılavuzu](../sql-database/sql-database-performance-guidance.md). SQL Azure veritabanına paralel yazma sırasında yazma sıralaması garanti edilmez.

Çözümünüzün genel verimini artırmaya yardımcı olabilecek her bir hizmet içindeki bazı konfigürasyonlar aşağıda verilmiştir.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **Bölümlendirmeyi devralma** – bu SQL çıkış yapılandırma seçeneği, önceki sorgu adımlarınızın veya girişinin bölümleme düzeninin devralınmasını mümkün. Bu etkinken, disk tabanlı bir tabloya yazma ve işiniz için [tamamen paralel](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) topolojiye sahip olmak için daha iyi işlem görmeniz beklenir. Bu bölümlendirme diğer birçok [çıktı](stream-analytics-parallelization.md#partitions-in-sources-and-sinks)için zaten otomatik olarak yapılır. Bu seçenekle yapılan toplu eklemeler için tablo kilitleme (TABLOCK) de devre dışı bırakıldı.

> [!NOTE] 
> 8 ' den fazla giriş bölümü olduğunda, giriş bölümleme düzenini devralma uygun bir seçenek olmayabilir. Bu üst sınır, tek bir kimlik sütunu ve bir kümelenmiş dizin içeren bir tabloda gözlemlendi. Bu durumda, çıkış yazıcılarının sayısını açıkça belirtmek için sorgunuzda 8 ' [e](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) kadar kullanmayı düşünün. Şemanıza ve Dizin seçimine bağlı olarak, gözlemlerinizin farklılık gösterebilir.

- **Toplu Iş boyutu** -SQL çıkış yapılandırması, hedef tablonuzun/iş yükünüzün doğasına göre Azure Stream ANALYTICS bir SQL çıkışında en büyük toplu iş boyutunu belirtmenize olanak tanır. Toplu iş boyutu, her toplu ekleme işlemi ile gönderilen en fazla kayıt sayısıdır. Kümelenmiş columnstore dizinlerinde, daha paralelleştirme, en az günlüğe kaydetme ve kilitleme iyileştirmeleri için [100K](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) etrafında toplu işlem boyutları. Disk tabanlı tablolarda, en fazla Batch boyutları toplu ekleme sırasında kilit yükseltmeyi tetikleyebilen için 10.000 (varsayılan) veya daha düşük bir çözüm, çözümünüz için en uygun olabilir.

- **Giriş Iletisi ayarlama** – bölümleme ve toplu iş boyutunu devralma kullanarak en iyi duruma getirildikten sonra, bölüm başına ileti başına giriş olaylarının sayısını artırmak, yazma aktarım hızınızı daha da ileri dağıtmaya yardımcı olur. Giriş iletisi ayarlama, Azure Stream Analytics içindeki toplu iş boyutlarının belirtilen toplu iş boyutuna kadar olmasına olanak tanır ve böylece üretilen işi geliştirir. Bu, bu veya EventHub ya [](stream-analytics-define-inputs.md) da blob 'daki giriş iletisi boyutlarının artması veya artırılması aracılığıyla elde edilebilir.

## <a name="sql-azure"></a>SQL Azure

- **Bölümlenmiş tablo ve dizinler** – bölümlemeli [](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) bir SQL tablosu ve bölümlenmiş dizinleri, Bölüm anahtarınızla aynı sütunla (örneğin, PartitionID) kullanarak, yazma işlemleri sırasında bölümler arasındaki çekişmeleri önemli ölçüde azaltabilir. Bölümlenmiş bir tablo için, BIRINCIL dosya grubunda bir [bölüm işlevi](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017) ve bir [bölüm düzeni](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017) oluşturmanız gerekir. Bu, yeni veriler yüklenirken mevcut verilerin kullanılabilirliğini de artırır. Günlük GÇ sınırı, SKU ile yükseltilerek artırılabilir olan bölüm sayısına bağlı olarak gelebilir.

- **Benzersiz anahtar Ihlallerinden kaçının** – Azure Stream Analytics etkinlik günlüğünde [birden çok anahtar ihlali uyarı iletisi](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) alırsanız, işinizin kurtarma durumları sırasında gerçekleşmesi muhtemel olan benzersiz kısıtlama ihlallerinden etkilenmediğinden emin olun. Dizininizdeki [\_\_anahtar yok sayma](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) seçeneği ayarlanarak bu kaçınılabilir.

## <a name="azure-data-factory-and-in-memory-tables"></a>Azure Data Factory ve bellek Içi tablolar

- **Geçici tablo olarak bellek Içi tablo** : [bellek içi tablolar](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) çok yüksek hızlı veri yükleri sağlar ancak verilerin belleğe sığması gerekir. Kıyaslamalar, bir bellek içi tablodan disk tabanlı bir tabloya toplu yükleme işlemini, bir kimlik sütunuyla ve kümelenmiş bir dizinle disk tabanlı tabloya tek bir yazıcı kullanarak doğrudan toplu ekleme işlemi için yaklaşık 10 kat daha hızlıdır. Bu toplu ekleme performansından yararlanmak için, bellek içi tablodaki verileri disk tabanlı tabloya kopyalayan [Azure Data Factory kullanarak bir kopyalama işi](../data-factory/connector-azure-sql-database.md) ayarlayın.

## <a name="avoiding-performance-pitfalls"></a>Performans Tuzaslarını önleme
Toplu ekleme verileri, tek eklemeleri olan verileri yüklemeye göre çok daha hızlıdır, çünkü verileri aktarma tekrarlanması, INSERT ifadesini ayrıştırma, ifadeyi çalıştırma ve bir işlem kaydı verme önlenmiş olur. Bunun yerine, verileri akışa almak için depolama altyapısına daha verimli bir yol kullanılır. Bu yolun kurulum maliyeti, disk tabanlı tablodaki tek bir INSERT ifadesinden çok daha yüksektir. Satır içi yüklemenin neredeyse her zaman daha verimli olması için, kesme çift noktası genellikle 100 satırı etrafında olur. 

Gelen olaylar oranı düşükse, toplu ekleme verimsiz ve çok fazla disk alanı kullanan 100 satırdan daha düşük toplu işlem boyutları kolayca oluşturulabilir. Bu kısıtlamayı geçici olarak çözmek için şu eylemlerden birini yapabilirsiniz:
* Her satır için basit ekleme kullanmak üzere bir INSTEAD OF [tetikleyicisi](/sql/t-sql/statements/create-trigger-transact-sql) oluşturun.
* Önceki bölümde açıklandığı gibi bellek Içi geçici tablo kullanın.

Bu tür bir senaryo, kümelenmemiş bir columnstore dizinine (NCCı) yazılırken meydana gelir; burada daha küçük toplu ekler çok fazla bölüt oluşturabilir ve bu da dizin kilitlenebilirler. Bu durumda, bunun yerine kümelenmiş bir columnstore dizininin kullanılması önerilir.

## <a name="summary"></a>Özet

Özet olarak, SQL çıktısı için Azure Stream Analytics bölümlenmiş çıkış özelliği ile, bir SQL Azure bölümlenmiş tablo ile işinizin hizalı paralelleştirme, size önemli bir işleme iyileştirmeleri vermelidir. Bellek Içi bir tablodan disk tabanlı tablolara veri hareketini düzenlemek için Azure Data Factory kullanma, büyüklük halinde üretilen iş kazanımları verebilir. Mümkünse, ileti yoğunluğunu artırmak, genel üretilen işi iyileştirmeye yönelik bir büyük etken de olabilir.
