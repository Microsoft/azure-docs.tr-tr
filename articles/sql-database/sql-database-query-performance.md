---
title: Sorgu Performansı İçgörüleri
description: Sorgu performansı izleme, bir Azure SQL veritabanı için en fazla CPU kullanan sorguları tanımlar.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 01/03/2019
ms.openlocfilehash: 56daca0aa817d03298bad971506402739d71482e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821246"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Azure SQL veritabanı için Sorgu Performansı İçgörüleri

İlişkisel veritabanlarının performansını yönetme ve ayarlama, uzmanlık ve zaman alır. Sorgu Performansı İçgörüleri, Azure SQL veritabanı Intelligent Performance ürün hattının bir parçasıdır. Aşağıdakileri sağlayarak veritabanı performansına sorun gidermeye daha az zaman harcamanıza yardımcı olur:

* Veritabanları kaynağı (DTU) tüketiminiz hakkında daha derin Öngörüler.
* CPU, süre ve yürütme sayısına göre en iyi veritabanı sorgularının ayrıntıları (performans iyileştirmeleri için olası ayarlama adayları).
* Kaynak kullanımının sorgu metnini ve geçmişini görüntülemek için bir sorgunun ayrıntılarına gitme özelliği.
* [SQL veritabanı Danışmanı](sql-database-advisor.md)performans önerilerini gösteren ek açıklamalar.

![Sorgu Performansı İçgörüleri](./media/sql-database-query-performance/opening-title.png)

> [!TIP]
> Azure SQL veritabanı ile temel performans izleme için Sorgu Performansı İçgörüleri önerilir. Bu makalede yayımlanan ürün sınırlamalarını aklınızda edin. Veritabanı performansının ölçekteki gelişmiş izlenmesi için [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md)önerilir. Otomatik performans sorunlarını gidermek için yerleşik zeka sahiptir. En yaygın veritabanı performans sorunlarından bazılarını otomatik olarak ayarlamak için [otomatik ayarlamayı](sql-database-automatic-tuning.md)öneririz.

## <a name="prerequisites"></a>Ön koşullar

Sorgu Performansı İçgörüleri, veritabanınızda [sorgu deposunun](https://msdn.microsoft.com/library/dn817826.aspx) etkin olmasını gerektirir. Varsayılan olarak tüm Azure SQL veritabanları için otomatik olarak etkinleştirilir. Sorgu deposu çalışmıyorsa, Azure portal etkinleştirmek isteyip istemeyecektir.

> [!NOTE]
> "Sorgu deposu bu veritabanında düzgün şekilde yapılandırılmamışsa" ileti portalda görüntülenirse, bkz. [sorgu deposu yapılandırmasını iyileştirme](#optimize-the-query-store-configuration-for-query-performance-insight).
>

## <a name="permissions"></a>İzinler

Sorgu Performansı İçgörüleri kullanmak için aşağıdaki [rol tabanlı erişim denetimi](../role-based-access-control/overview.md) izinlerine sahip olmanız gerekir:

* En üstteki kaynak kullanan sorguları ve grafikleri görüntülemek için **okuyucu**, **sahip**, **katkıda bulunan**, **SQL DB katılımcısı**veya **SQL Server katkıda** bulunan izinleri gereklidir.
* Sorgu metnini görüntülemek için **sahip**, **katkıda**bulunan, **SQL DB katılımcısı**veya **SQL Server katkıda** bulunan izinleri gereklidir.

## <a name="use-query-performance-insight"></a>Sorgu Performansı İçgörüleri’ni kullanma

Sorgu Performansı İçgörüleri kullanımı kolaydır:

1. [Azure Portal](https://portal.azure.com/) açın ve incelemek istediğiniz bir veritabanını bulun.
2. Sol taraftaki menüden **akıllı performans** > **sorgu performansı içgörüleri**açın.
  
   ![Menüdeki Sorgu Performansı İçgörüleri](./media/sql-database-query-performance/tile.png)

3. İlk sekmede, en üstteki kaynak kullanan sorguların listesini gözden geçirin.
4. Ayrıntılarını görüntülemek için tek bir sorgu seçin.
5. **Akıllı performans** > **performans önerilerini** açın ve performans önerilerinin kullanılabilir olup olmadığını denetleyin. Yerleşik performans önerileri hakkında daha fazla bilgi için bkz. [SQL veritabanı Danışmanı](sql-database-advisor.md).
6. Gözlemlenen aralığı değiştirmek için kaydırıcıları veya yakınlaştırma simgelerini kullanın.

   ![Performans panosu](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> SQL veritabanı 'nın Sorgu Performansı İçgörüleri bilgileri işlemesi için Query Store 'un birkaç saat veri yakalamasına ihtiyacı vardır. Veritabanında etkinlik yoksa veya sorgu deposu belirli bir süre içinde etkin değilse, Sorgu Performansı İçgörüleri bu zaman aralığını görüntülediğinde grafikler boş olur. Sorgu deposunu, çalışmıyorsa istediğiniz zaman etkinleştirebilirsiniz. Daha fazla bilgi için bkz. [sorgu deposu Ile en iyi uygulamalar](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store).

## <a name="review-top-cpu-consuming-queries"></a>En yüksek CPU kullanan sorguları gözden geçirme

Varsayılan olarak, Sorgu Performansı İçgörüleri ilk kez açtığınızda en fazla beş CPU kullanan sorguyu gösterir.

1. Onay kutularını kullanarak bunları grafiğe dahil etmek veya kümeden dışlamak için tek tek sorguları seçin veya temizleyin.

    En üstteki satır, veritabanının genel DTU yüzdesini gösterir. Çubuklar seçili Aralık boyunca seçilen sorguların tükettiği CPU yüzdesini gösterir. Örneğin, **geçmiş hafta** seçiliyse, her çubuk tek bir günü temsil eder.

    ![Popüler sorgular](./media/sql-database-query-performance/top-queries.png)

   > [!IMPORTANT]
   > Gösterilen DTU hattı, bir saatlik dönemlerde maksimum tüketim değerine toplanır. Yalnızca sorgu yürütme istatistikleriyle yüksek düzey bir karşılaştırma için tasarlanmıştır. Bazı durumlarda, DTU kullanımı yürütülen sorgularla karşılaştırıldığında çok yüksek görünebilir, ancak böyle bir durum olmayabilir.
   >
   > Örneğin, bir sorgu %100 oranında yalnızca birkaç dakika boyunca% ' a kadar DTU, Sorgu Performansı İçgörüleri DTU hattı tüm tüketim saatini %100 (en fazla toplanan değerin sonucu) olarak gösterir.
   >
   > Daha ayrıntılı bir karşılaştırma için (bir dakikaya kadar) özel bir DTU kullanım grafiği oluşturmayı düşünün:
   >
   > 1. Azure portal **Azure SQL veritabanı** > **izleme**' yi seçin.
   > 2. **Ölçümler**’i seçin.
   > 3. **+ Grafik Ekle ' yi**seçin.
   > 4. Grafikteki DTU yüzdesini seçin.
   > 5. Ayrıca, sol üst menüden **son 24 saat** ' i seçin ve bir dakika olarak değiştirin.
   >
   > Sorgu yürütme grafiğiyle karşılaştırmak için, daha ayrıntılı bir ayrıntı düzeyiyle özel DTU grafiğini kullanın.

   Alt kılavuz, görünür sorgular için toplu bilgileri gösterir:

   * Veritabanında sorgu için benzersiz bir tanımlayıcı olan sorgu KIMLIĞI.
   * , Toplama işlevine bağlı olan bir observable aralığı sırasında sorgu başına CPU.
   * Toplama işlevine de bağlı olan sorgu başına süre.
   * Belirli bir sorgu için toplam yürütme sayısı.

2. Verileriniz eski haline gelirse **Yenile** düğmesini seçin.

3. Gözlem aralığını değiştirmek ve tüketim artışlarını araştırmak için kaydırıcıları ve yakınlaştırma düğmelerini kullanın:

   ![Aralığı değiştirmek için sürgüler ve yakınlaştırma düğmeleri](./media/sql-database-query-performance/zoom.png)

4. İsteğe bağlı olarak, görünümünü özelleştirmek için **özel** sekmesini seçebilirsiniz:

   * Ölçüm (CPU, süre, yürütme sayısı).
   * Zaman aralığı (son 24 saat, geçen hafta veya geçmiş ay).
   * Sorgu sayısı.
   * Toplama işlevi.
  
   ![Özel sekme](./media/sql-database-query-performance/custom-tab.png)
  
5. Özelleştirilmiş görünümü görmek için **git >** düğmesini seçin.

   > [!IMPORTANT]
   > Sorgu Performansı İçgörüleri, seçiminize bağlı olarak en üstteki 5-20 kullanan sorguları görüntüleyecek şekilde sınırlandırılmıştır. Veritabanınız gösterilenlerin ötesinde çok daha fazla sorgu çalıştırabilir ve bu sorgular grafiğe dahil edilmez.
   >
   > Çok sayıda daha küçük sorgu, gösterilenlerin ötesinde sık çalıştırılan ve en büyük DTU 'ın çoğunu kullanan bir veritabanı iş yükü türü bulunabilir. Bu sorgular Performans grafiğinde görünmez.
   >
   > Örneğin, bir sorgu bir süredir önemli miktarda DTU tüketiyor olabilir, ancak gözlemlenen dönemdeki toplam tüketim diğer en iyi kullanan sorgulardan daha az. Böyle bir durumda, bu sorgunun kaynak kullanımı grafikte görünmez.
   >
   > Sorgu Performansı İçgörüleri sınırlamaların ötesinde en üstteki sorgu yürütmelerinin anlaşılması gerekiyorsa, Gelişmiş veritabanı performansı izleme ve sorun giderme için [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) kullanmayı göz önünde bulundurun.
   >

## <a name="view-individual-query-details"></a>Tek tek sorgu ayrıntılarını görüntüleme

Sorgu ayrıntılarını görüntülemek için:

1. En üst sorgular listesinden herhangi bir sorgu seçin.

    ![En popüler sorgular listesi](./media/sql-database-query-performance/details.png)

   Ayrıntılı bir görünüm açılır. Zaman içinde CPU tüketimini, süresini ve yürütme sayısını gösterir.

2. Ayrıntılar için grafik özelliklerini seçin.

   * Üstteki grafik, genel veritabanı DTU yüzdesinin bulunduğu bir satır gösterir. Çubuklar, seçilen sorgunun tükettiği CPU yüzdesidir.
   * İkinci grafik seçili sorgunun toplam süresini gösterir.
   * Alt grafik seçili sorgu tarafından toplam yürütme sayısını gösterir.

   ![Sorgu Ayrıntıları](./media/sql-database-query-performance/query-details.png)

3. İsteğe bağlı olarak, kaydırıcıları kullanın, yakınlaştırma düğmelerini kullanın veya sorgu verilerinin nasıl görüntülendiğini özelleştirmek veya farklı bir zaman aralığı seçmek için **Ayarlar** ' ı seçin.

   > [!IMPORTANT]
   > Sorgu Performansı İçgörüleri DDL sorgularını yakalamaz. Bazı durumlarda, tüm geçici sorguları yakalamamayabilir.
   >

## <a name="review-top-queries-per-duration"></a>Süre başına en popüler sorguları gözden geçirin

Sorgu Performansı İçgörüleri iki ölçüm olası performans sorunlarını bulmanıza yardımcı olabilir: süre ve yürütme sayısı.

Uzun süre çalışan sorgular, kaynakları daha uzun süre kilitlemek, diğer kullanıcıları engellemek ve ölçeklenebilirliği kısıtlamak için en büyük olasılıklardır. En iyi duruma getirme için de en iyi adaylardır.

Uzun süre çalışan sorguları belirlemek için:

1. Seçili veritabanı için Sorgu Performansı İçgörüleri **özel** sekmesini açın.
2. Ölçümleri **süre**olarak değiştirin.
3. Sorgu sayısını ve gözlemme aralığını seçin.
4. Toplama işlevini seçin:

   * **Sum** , tüm gözlemlik aralığı için tüm sorgu yürütme süresini ekler.
   * **Maksimum** , yürütme zamanının tam izleme aralığı için en fazla olduğu sorguları bulur.
   * **AVG** , tüm sorgu yürütmelerinin ortalama yürütme süresini bulur ve bu Ortalamalar için en iyi olanları gösterir.

   ![Sorgu süresi](./media/sql-database-query-performance/top-duration.png)

5. Özelleştirilmiş görünümü görmek için **git >** düğmesini seçin.

   > [!IMPORTANT]
   > Sorgu görünümünü ayarlamak DTU satırını güncelleştirmez. DTU çizgisi her zaman Aralık için maksimum tüketim değerini gösterir.
   >
   > Veritabanı DTU kullanımını daha fazla ayrıntı ile (bir dakikaya kadar) anlamak için Azure portal özel bir grafik oluşturmayı düşünün:
   >
   > 1. Izleme > **Azure SQL veritabanı** ' **nı**seçin.
   > 2. **Ölçümler**’i seçin.
   > 3. **+ Grafik Ekle ' yi**seçin.
   > 4. Grafikteki DTU yüzdesini seçin.
   > 5. Ayrıca, sol üst menüden **son 24 saat** ' i seçin ve bir dakika olarak değiştirin.
   >
   > Sorgu performans grafiğine göre karşılaştırmak için özel DTU grafiğini kullanmanızı öneririz.
   >

## <a name="review-top-queries-per-execution-count"></a>Yürütme sayısı başına en popüler sorguları gözden geçirin

Veritabanını kullanan bir kullanıcı uygulaması, yüksek sayıda yürütme veritabanının kendisini etkilemese ve kaynakların kullanımı düşük olsa bile yavaş olabilir.

Bazı durumlarda, yüksek bir yürütme sayısı daha fazla ağ gidiş dönüşlere neden olabilir. Gidiş dönüşler performansı etkiler. Bunlar ağ gecikme süresine ve aşağı akış sunucusu gecikme süresine tabidir.

Örneğin, çok sayıda veri tabanlı Web sitesi, her kullanıcı isteği için veritabanına yoğun bir şekilde erişir. Bağlantı havuzu size yardımcı olmakla birlikte, veritabanı sunucusundaki artan ağ trafiği ve işleme yükü performansı düşürebilir. Genel olarak, gidiş dönüş ' ı en düşük tutmaya devam edin.

Sık yürütülen ("geveze") sorgularını belirlemek için:

1. Seçili veritabanı için Sorgu Performansı İçgörüleri **özel** sekmesini açın.
2. Ölçümleri **yürütme sayısı**olarak değiştirin.
3. Sorgu sayısını ve gözlemme aralığını seçin.
4. Özelleştirilmiş görünümü görmek için **git >** düğmesini seçin.

   ![Sorgu yürütme sayısı](./media/sql-database-query-performance/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Performans ayarlama ek açıklamalarını anlama

İş yükünüzü Sorgu Performansı İçgörüleri araştırırken, grafiğin üstünde dikey bir çizgi ile simgeler fark edebilirsiniz.

Bu simgeler ek açıklamalardır. [SQL veritabanı Danışmanı](sql-database-advisor.md)performans önerilerini gösterir. Ek açıklamanın üzerine gelindiğinde, performans önerileri hakkında özet bilgiler edinebilirsiniz.

   ![Sorgu ek açıklaması](./media/sql-database-query-performance/annotation.png)

Daha fazlasını anlamak veya Advisor 'ın önerisini uygulamak istiyorsanız, önerilen eylemin ayrıntılarını açmak için simgeyi seçin. Bu etkin bir öneriyiysa, portaldan hemen uygulayabilirsiniz.

   ![Sorgu ek açıklaması ayrıntıları](./media/sql-database-query-performance/annotation-details.png)

Bazı durumlarda, yakınlaştırma düzeyi nedeniyle, birbirini kapatan ek açıklamaların tek bir ek açıklama halinde daraltılması mümkündür. Sorgu Performansı İçgörüleri bunu bir grup ek açıklaması simgesi olarak temsil eder. Grup ek açıklaması simgesi seçildiğinde, ek açıklamaları listeleyen yeni bir dikey pencere açılır.

Sorguları ve performans ayarlama eylemlerinin ilişkilendirilmesi, iş yükünüzü daha iyi anlamanıza yardımcı olabilirler.

## <a name="optimize-the-query-store-configuration-for-query-performance-insight"></a>Sorgu Performansı İçgörüleri için sorgu deposu yapılandırmasını iyileştirin

Sorgu Performansı İçgörüleri kullanırken aşağıdaki sorgu deposu hata iletilerini görebilirsiniz:

* "Query Store bu veritabanında düzgün şekilde yapılandırılmamış. Daha fazla bilgi için buraya tıklayın. "
* "Query Store bu veritabanında düzgün şekilde yapılandırılmamış. Ayarları değiştirmek için buraya tıklayın. "

Bu iletiler genellikle sorgu deposu yeni veri toplayamıyorum görüntülenir.

İlk durum, sorgu deposu salt okuma durumundaysa ve parametreler en iyi şekilde ayarlanamadığında oluşur. Veri deposunun boyutunu artırarak veya sorgu deposunu temizleyerek bu hatayı çözebilirsiniz. (Sorgu deposunu temizlerseniz, daha önce toplanan tüm telemetri kaybedilir.)

   ![Sorgu deposu ayrıntıları](./media/sql-database-query-performance/qds-off.png)

İkinci durum, sorgu deposu etkinleştirilmediğinde veya parametreler en iyi şekilde ayarlanamadığında oluşur. [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) veya Azure Portal tarafından sunulan aşağıdaki komutları çalıştırarak bekletme ve yakalama ilkesini değiştirebilir ve ayrıca sorgu deposunu etkinleştirebilirsiniz.

### <a name="recommended-retention-and-capture-policy"></a>Önerilen bekletme ve yakalama ilkesi

İki tür bekletme ilkesi vardır:

* **Boyut tabanlı**: Bu ilke **Otomatik**olarak ayarlandıysa, en büyük boyuta yaklaştıklarında verileri otomatik olarak temizler.
* **Zaman tabanlı**: Bu ilke varsayılan olarak 30 güne ayarlanır. Sorgu deposunda boş alan biterse, 30 günden eski olan sorgu bilgilerini silecektir.

Yakalama ilkesini şu şekilde ayarlayabilirsiniz:

* **Tümü**: sorgu deposu tüm sorguları yakalar.
* **Otomatik**: sorgu deposu seyrek olarak derleme ve yürütme süresine sahip seyrek sorguları ve sorguları yoksayar. Yürütme sayısı, derleme süresi ve çalışma zamanı süresi eşikleri dahili olarak belirlenir. Bu varsayılan seçenektir.
* **Hiçbiri**: sorgu deposu yeni sorgular yakalamayı durduruyor, ancak zaten yakalanan sorgulara yönelik çalışma zamanı istatistikleri yine de toplanmaktadır.

[SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) veya Azure Portal aşağıdaki komutları yürüterek tüm ilkelerin **Otomatik** olarak ve temizleme ilkesini 30 güne ayarlamamız önerilir. (`YourDB` veritabanı adıyla değiştirin.)

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

[SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) veya Azure Portal aracılığıyla bir veritabanına bağlanarak sorgu deposunun boyutunu artırın ve aşağıdaki sorguyu çalıştırın. (`YourDB` veritabanı adıyla değiştirin.)

```T-SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

Bu ayarların uygulanması, sorgu deposunun yeni sorgular için telemetri toplamasını sağlayacak. Query Store 'un hemen işlemsel olması gerekiyorsa, SSMS veya Azure portal aracılığıyla aşağıdaki sorguyu çalıştırarak Query Store 'u temizleyebilirsiniz. (`YourDB` veritabanı adıyla değiştirin.)

> [!NOTE]
> Aşağıdaki sorgu çalıştırıldığında, daha önce toplanan tüm telemetri sorgu deposunda silinir.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="summary"></a>Özet

Sorgu Performansı İçgörüleri sorgu iş yükünün etkisini ve veritabanı kaynaklarının tüketimiyle ilişkisini anlamanıza yardımcı olur. Bu özellikle, veritabanınızdaki en çok kullanılan sorgular hakkında bilgi edineceksiniz ve bir sorun haline gelmeden önce optimize edilecek sorgular bulacaksınız.

## <a name="next-steps"></a>Sonraki adımlar

* Veritabanı performans önerileri için Sorgu Performansı İçgörüleri gezinti dikey penceresinde [öneriler](sql-database-advisor.md) ' i seçin.

    ![Öneriler sekmesi](./media/sql-database-query-performance/ia.png)

* Ortak veritabanı performans sorunları için [otomatik ayarlamayı](sql-database-automatic-tuning.md) etkinleştirmeyi düşünün.
* [Akıllı içgörüler](sql-database-intelligent-insights.md) , veritabanı performans sorunlarını otomatik olarak gidermeye nasıl yardımcı olabileceğini öğrenin.
* Yerleşik zeka ile SQL veritabanlarının büyük bir bölümünü, elastik havuzları ve yönetilen örnekleri gelişmiş performans izleme için [Azure SQL Analytics]( ../azure-monitor/insights/azure-sql.md) kullanmayı düşünün.
