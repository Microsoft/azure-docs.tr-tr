---
title: In-Memory OLTP SQL TXN perf 'yi geliştirir
description: Azure SQL veritabanı ve Azure SQL yönetilen örneği 'nde var olan bir veritabanında işlemsel performansı artırmak için In-Memory OLTP benimseyin.
services: sql-database
ms.service: sql-database
ms.custom: sqldbrb=2
ms.subservice: development
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 11/07/2018
ms.openlocfilehash: e17e98e784b7453c87814c5cce5c03568f66b1cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91619755"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL veritabanı ve Azure SQL yönetilen örneği 'nde uygulama performansınızı geliştirmek için In-Memory OLTP kullanın
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

[Bellek ıçı OLTP](in-memory-oltp-overview.md) , işlem işleme, veri alımı ve geçici veri senaryolarında, fiyatlandırma katmanını arttırmadan [Premium ve iş açısından kritik katmanı](database/service-tiers-vcore.md) veritabanlarında performansı geliştirmek için kullanılabilir.

> [!NOTE]
> [Azure SQL veritabanı Ile DTU 'yu %70 oranında düşürürken, çekirdeğin anahtar veritabanı iş yükünü nasıl çift katına](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database) kullandığını öğrenin

Mevcut veritabanınızda In-Memory OLTP benimsemek için aşağıdaki adımları izleyin.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>1. Adım: Premium ve İş Açısından Kritik katmanı veritabanı kullandığınızdan emin olun

In-Memory OLTP yalnızca Premium ve İş Açısından Kritik katmanı veritabanlarında desteklenir. Döndürülen sonuç 1 (0 değil) ise In-Memory desteklenir:

```sql
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* , *aşırı işlem işleme* için temsil eder

## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>2. Adım: In-Memory OLTP 'a geçirilecek nesneleri tanımla

SSMS, etkin iş yüküne sahip bir veritabanında çalıştırabileceğiniz bir **Işlem performansı analizine genel bakış** raporu içerir. Rapor, In-Memory OLTP 'e geçiş için aday olan tabloları ve saklı yordamları tanımlar.

SSMS 'de, raporu oluşturmak için:

* **Nesne Gezgini**, veritabanı düğümünüz ' a sağ tıklayın.
* **Raporlar**  >  **Standart raporlar**  >  **işlem performansı analizine genel bakış ' a** tıklayın.

Daha fazla bilgi için bkz. [In-Memory OLTP 'a bir tablo veya saklı yordam arasında bağlantı olup olmadığını belirleme](/sql/relational-databases/in-memory-oltp/determining-if-a-table-or-stored-procedure-should-be-ported-to-in-memory-oltp).

## <a name="step-3-create-a-comparable-test-database"></a>3. Adım: karşılaştırılabilir bir test veritabanı oluşturma

Raporun, veritabanınızın, bellek için iyileştirilmiş tabloya dönüştürülmesinin avantajına sahip bir tablo olduğunu olduğunu varsayalım. Test yaparak bildirimi doğrulamak için ilk test etmenizi öneririz.

Üretim veritabanınızın bir test kopyasına ihtiyacınız vardır. Test veritabanı, üretim veritabanınız ile aynı hizmet katmanı düzeyinde olmalıdır.

Testi kolaylaştırmak için test veritabanınızı şu şekilde ince ayar:

1. SSMS kullanarak test veritabanına bağlanın.
2. Sorgularda WıTH (SNAPSHOT) seçeneğinin olmasını önlemek için, aşağıdaki T-SQL ifadesinde gösterildiği gibi Database seçeneğini ayarlayın:

   ```sql
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>4. Adım: tabloları geçirme

Sınamak istediğiniz tablonun bellek için iyileştirilmiş bir kopyasını oluşturmanız ve doldurmanız gerekir. Bu, aşağıdakilerden birini kullanarak oluşturabilirsiniz:

* SSMS 'de kullanışlı bellek Iyileştirme Sihirbazı.
* El ile T-SQL.

### <a name="memory-optimization-wizard-in-ssms"></a>SSMS 'de bellek Iyileştirme Sihirbazı

Bu geçiş seçeneğini kullanmak için:

1. SSMS ile test veritabanına bağlanın.
2. **Nesne Gezgini**, tabloya sağ tıklayın ve ardından **bellek iyileştirme Danışmanı**' na tıklayın.

   **Tablo belleği Iyileştirici Danışmanı** Sihirbazı görüntülenir.
3. Sihirbazda, bellek için iyileştirilmiş tablolarda desteklenmeyen desteklenmeyen özelliklere sahip olup olmadığını görmek için **geçiş doğrulaması** (veya **sonraki** düğme) seçeneğine tıklayın. Daha fazla bilgi için bkz.

   * [Bellek Iyileştirme Danışmanı](/sql/relational-databases/in-memory-oltp/memory-optimization-advisor)'nda *bellek iyileştirme denetim listesi* .
   * [Transact-SQL yapıları In-Memory OLTP tarafından desteklenmiyor](/sql/relational-databases/in-memory-oltp/transact-sql-constructs-not-supported-by-in-memory-oltp).
   * [In-Memory OLTP 'A geçiriliyor](/sql/relational-databases/in-memory-oltp/plan-your-adoption-of-in-memory-oltp-features-in-sql-server).
4. Tabloda desteklenmeyen özellikler yoksa, danışman gerçek şemayı ve veri geçişini sizin için gerçekleştirebilir.

### <a name="manual-t-sql"></a>El ile T-SQL

Bu geçiş seçeneğini kullanmak için:

1. SSMS (veya benzer bir yardımcı program) kullanarak test veritabanınıza bağlanın.
2. Tablonuz ve dizinleri için tam T-SQL betiğini edinin.

   * SSMS 'de tablo düğümünüz ' a sağ tıklayın.
   * **Betik tablosu**' na  >    >  **Yeni sorgu** oluştur ' a tıklayın.
3. Betik penceresinde, CREATE TABLE bildirimine (MEMORY_OPTIMIZED = ON) ekleyin.
4. KÜMELENMIŞ bir dizin varsa, KÜMELENMEMIŞ olarak değiştirin.
5. SP_RENAME kullanarak mevcut tabloyu yeniden adlandırın.
6. Düzenlediğiniz CREATE TABLE betiğini çalıştırarak tablonun bellek için iyileştirilmiş yeni kopyasını oluşturun.
7. INSERT... kullanarak verileri bellek için iyileştirilmiş tablonuza kopyalayın Seç:

```sql
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```

## <a name="step-5-optional-migrate-stored-procedures"></a>5. adım (isteğe bağlı): saklı yordamları geçirme

In-Memory özelliği, gelişmiş performans için bir saklı yordamı da değiştirebilir.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Yerel koda derlenmiş saklı yordamlar ile ilgili konular

Yerel koda derlenmiş bir saklı yordamın T-SQL WıTH yan tümcesinde aşağıdaki seçeneklere sahip olması gerekir:

* NATIVE_COMPILATION
* SCHEMABINDING: saklı yordamı bırakmadığınız müddetçe saklı yordamın sütun tanımlarının, saklı yordamı etkileyebilecek herhangi bir şekilde değiştirilmeyeceği anlamına gelir.

Yerel bir modülün işlem yönetimi için bir büyük [atomik bloklar](/sql/relational-databases/in-memory-oltp/atomic-blocks-in-native-procedures) kullanması gerekir. Açık bir BEGIN TRANSACTION veya GERI ALMA IŞLEMI için rol yoktur. Kodunuz bir iş kuralını ihlal ederse, bir [throw](/sql/t-sql/language-elements/throw-transact-sql) ifadesiyle atomik bloğu sonlandırabilir.

### <a name="typical-create-procedure-for-natively-compiled"></a>Yerel koda derlenmiş normal oluşturma yordamı

Genellikle, yerel koda derlenmiş bir saklı yordam oluşturmak için T-SQL aşağıdaki şablona benzerdir:

```sql
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

* TRANSACTION_ISOLATION_LEVEL için, anlık görüntü, yerel koda derlenmiş saklı yordamın en yaygın değeridir. Ancak, diğer değerlerin bir alt kümesi de desteklenir:
  
  * YINELENEBILIR OKUMA
  * SERI hale GETIRILEBILIR
* DIL değeri sys. Languages görünümünde bulunmalıdır.

### <a name="how-to-migrate-a-stored-procedure"></a>Saklı yordam geçirme

Geçiş adımları şunlardır:

1. Normal yorumlanan saklı yordama yordam oluşturma betiği alın.
2. Üst bilgisini önceki şablonla eşleşecek şekilde yeniden yazın.
3. Saklı yordam T-SQL kodu, yerel koda derlenmiş saklı yordamlar için desteklenmeyen herhangi bir özelliği kullanıp kullanmadığını yokermez. Gerekirse geçici çözümler uygulayın.

   Ayrıntılar için bkz. [yerel koda derlenmiş saklı yordamlar Için geçiş sorunları](/sql/relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables).
4. SP_RENAME kullanarak eski saklı yordamı yeniden adlandırın. Ya da basitçe BıRAKMALıSıNıZ.
5. Düzenlediğiniz oluşturma YORDAMıNı çalıştırın T-SQL komut dosyası.

## <a name="step-6-run-your-workload-in-test"></a>6. Adım: test sırasında iş yükünüzü çalıştırma

Test veritabanınızda, üretim veritabanınızda çalışan iş yüküne benzer bir iş yükü çalıştırın. Bu, tablolar ve saklı yordamlar için In-Memory özelliğini kullanmanız ile elde edilen performans kazancını açığa çıkarmalıdır.

İş yükünün ana öznitelikleri şunlardır:

* Eşzamanlı bağlantı sayısı.
* Okuma/yazma oranı.

Test iş yükünü uyarlamak ve çalıştırmak için `ostress.exe` Bu [bellek içi](in-memory-oltp-overview.md) makalede gösterilen kullanışlı aracı kullanmayı göz önünde bulundurun.

Ağ gecikmesini en aza indirmek için, testinizi, veritabanının bulunduğu aynı Azure coğrafi bölgesinde çalıştırın.

## <a name="step-7-post-implementation-monitoring"></a>7. Adım: uygulama sonrası izleme

Üretimde In-Memory uygulamalarınızın performans efektlerini izlemeyi düşünün:

* [In-Memory depolamayı izleyin](in-memory-oltp-monitor-space.md).
* [Dinamik yönetim görünümlerini kullanarak izleme](database/monitoring-with-dmvs.md)

## <a name="related-links"></a>İlgili bağlantılar

* [Bellek içi OLTP (bellek Içi Iyileştirme)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
* [Yerel koda derlenmiş saklı yordamlara giriş](/sql/relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables)
* [Bellek Iyileştirme Danışmanı](/sql/relational-databases/in-memory-oltp/memory-optimization-advisor)
