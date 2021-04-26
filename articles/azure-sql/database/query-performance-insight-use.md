---
title: Sorgu Performansı İçgörüleri
description: Sorgu performansı izleme, Azure SQL veritabanı 'nda tek ve havuza alınmış veritabanları için en fazla CPU kullanan ve uzun süre çalışan sorguları belirler.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 1/14/2021
ms.openlocfilehash: db24f280f66e567572821297cfc9bb9b1e19743b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222352"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Azure SQL veritabanı için Sorgu Performansı İçgörüleri
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Sorgu Performansı İçgörüleri tek veritabanı ve havuza alınmış veritabanları için akıllı sorgu analizi sağlar. İş yükünüzdeki en çok kaynak tüketen ve uzun süre çalışan sorguları belirlemeye yardımcı olur. Genel olarak iş yükü performansını geliştirmek ve karşılığını ödediğiniz kaynağı verimli kullanmak için iyileştirilecek sorguları bulmanıza yardım eder. Sorgu Performansı İçgörüleri, aşağıdakileri sağlayarak veritabanı performansını sorun gidermeye daha az zaman harcamanıza yardımcı olur:

* Veritabanları kaynağınız (DTU) tüketiminize daha derin Öngörüler
* CPU, süre ve yürütme sayısına göre en iyi veritabanı sorgularının ayrıntıları (performans iyileştirmeleri için olası ayarlama adayları)
* Sorgu metnini ve kaynak kullanımının geçmişini görüntülemek için bir sorgunun ayrıntılarına gitme özelliği
* [Veritabanı danışmanlarından](database-advisor-implement-performance-recommendations.md) performans önerilerini gösteren ek açıklamalar

![Sorgu Performansı İçgörüleri](./media/query-performance-insight-use/opening-title.png)

## <a name="prerequisites"></a>Önkoşullar

Sorgu Performansı İçgörüleri, veritabanınızda [sorgu deposunun](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) etkin olmasını gerektirir. Azure SQL veritabanı 'ndaki tüm veritabanları için varsayılan olarak otomatik olarak etkinleştirilir. Sorgu deposu çalışmıyorsa, Azure portal etkinleştirmek isteyip istemeyecektir.

> [!NOTE]
> "Sorgu deposu bu veritabanında düzgün şekilde yapılandırılmamışsa" ileti portalda görüntülenirse, bkz. [sorgu deposu yapılandırmasını iyileştirme](#optimize-the-query-store-configuration).

## <a name="permissions"></a>İzinler

Sorgu Performansı İçgörüleri kullanmak için aşağıdaki [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md) izinlerinizin olması gerekir:

* En üstteki kaynak kullanan sorguları ve grafikleri görüntülemek için **okuyucu**, **sahip**, **katkıda bulunan**, **SQL DB katılımcısı** veya **SQL Server katkıda** bulunan izinleri gereklidir.
* Sorgu metnini görüntülemek için **sahip**, **katkıda** bulunan, **SQL DB katılımcısı** veya **SQL Server katkıda** bulunan izinleri gereklidir.

## <a name="use-query-performance-insight"></a>Sorgu Performansı İçgörüleri’ni kullanma

Sorgu Performansı İçgörüleri kullanımı kolaydır:

1. [Azure Portal](https://portal.azure.com/) açın ve incelemek istediğiniz bir veritabanını bulun.
2. Sol taraftaki menüden, **akıllı performans**  >  **sorgu performansı içgörüleri** açın.
  
   ![Menüdeki Sorgu Performansı İçgörüleri](./media/query-performance-insight-use/tile.png)

3. İlk sekmede, en üstteki kaynak kullanan sorguların listesini gözden geçirin.
4. Ayrıntılarını görüntülemek için tek bir sorgu seçin.
5. **Akıllı performans**  >  **performansı önerilerini** açın ve performans önerilerinin kullanılabilir olup olmadığını denetleyin. Yerleşik performans önerileri hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı Danışmanı](database-advisor-implement-performance-recommendations.md).
6. Gözlemlenen aralığı değiştirmek için kaydırıcıları veya yakınlaştırma simgelerini kullanın.

   ![Performans panosu](./media/query-performance-insight-use/performance.png)

> [!NOTE]
> Azure SQL veritabanı 'nın Sorgu Performansı İçgörüleri bilgileri işlemesi için Query Store 'un birkaç saat veri yakalamasına ihtiyacı vardır. Veritabanında etkinlik yoksa veya sorgu deposu belirli bir süre içinde etkin değilse, Sorgu Performansı İçgörüleri bu zaman aralığını görüntülediğinde grafikler boş olur. Sorgu deposunu, çalışmıyorsa istediğiniz zaman etkinleştirebilirsiniz. Daha fazla bilgi için bkz. [sorgu deposu Ile en iyi uygulamalar](/sql/relational-databases/performance/best-practice-with-the-query-store).
>

Veritabanı performans önerileri için Sorgu Performansı İçgörüleri gezinti dikey penceresinde [öneriler](database-advisor-implement-performance-recommendations.md) ' i seçin.

![Öneriler sekmesi](./media/query-performance-insight-use/ia.png)

## <a name="review-top-cpu-consuming-queries"></a>En yüksek CPU kullanan sorguları gözden geçirme

Varsayılan olarak, Sorgu Performansı İçgörüleri ilk kez açtığınızda en fazla beş CPU kullanan sorguyu gösterir.

1. Onay kutularını kullanarak bunları grafiğe dahil etmek veya kümeden dışlamak için tek tek sorguları seçin veya temizleyin.

   En üstteki satır, veritabanının genel DTU yüzdesini gösterir. Çubuklar seçili Aralık boyunca seçilen sorguların tükettiği CPU yüzdesini gösterir. Örneğin, **geçmiş hafta** seçiliyse, her çubuk tek bir günü temsil eder.

   ![Popüler sorgular](./media/query-performance-insight-use/top-queries.png)

   > [!IMPORTANT]
   > Gösterilen DTU hattı, bir saatlik dönemlerde maksimum tüketim değerine toplanır. Yalnızca sorgu yürütme istatistikleriyle yüksek düzey bir karşılaştırma için tasarlanmıştır. Bazı durumlarda, DTU kullanımı yürütülen sorgularla karşılaştırıldığında çok yüksek görünebilir, ancak böyle bir durum olmayabilir.
   >
   > Örneğin, bir sorgu %100 oranında yalnızca birkaç dakika boyunca% ' a kadar DTU, Sorgu Performansı İçgörüleri DTU hattı tüm tüketim saatini %100 (en fazla toplanan değerin sonucu) olarak gösterir.
   >
   > Daha ayrıntılı bir karşılaştırma için (bir dakikaya kadar) özel bir DTU kullanım grafiği oluşturmayı düşünün:
   >
   > 1. Azure Portal **Azure SQL veritabanı**  >  **izleme**' yi seçin.
   > 2. **Ölçümler**’i seçin.
   > 3. **+ Grafik Ekle ' yi** seçin.
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

   ![Aralığı değiştirmek için sürgüler ve yakınlaştırma düğmeleri](./media/query-performance-insight-use/zoom.png)

4. İsteğe bağlı olarak, görünümünü özelleştirmek için **özel** sekmesini seçebilirsiniz:

   * Ölçüm (CPU, süre, yürütme sayısı).
   * Zaman aralığı (son 24 saat, geçen hafta veya geçmiş ay).
   * Sorgu sayısı.
   * Toplama işlevi.
  
   ![Özel sekme](./media/query-performance-insight-use/custom-tab.png)
  
5. Özelleştirilmiş görünümü görmek için **git >** düğmesini seçin.

   > [!IMPORTANT]
   > Sorgu Performansı İçgörüleri, seçiminize bağlı olarak en üstteki 5-20 kullanan sorguları görüntüleyecek şekilde sınırlandırılmıştır. Veritabanınız gösterilenlerin ötesinde çok daha fazla sorgu çalıştırabilir ve bu sorgular grafiğe dahil edilmez.
   >
   > Çok sayıda daha küçük sorgu, gösterilenlerin ötesinde sık çalıştırılan ve en büyük DTU 'ın çoğunu kullanan bir veritabanı iş yükü türü bulunabilir. Bu sorgular Performans grafiğinde görünmez.
   >
   > Örneğin, bir sorgu bir süredir önemli miktarda DTU tüketiyor olabilir, ancak gözlemlenen dönemdeki toplam tüketim diğer en iyi kullanan sorgulardan daha az. Böyle bir durumda, bu sorgunun kaynak kullanımı grafikte görünmez.
   >
   > Sorgu Performansı İçgörüleri sınırlamaların ötesinde en üstteki sorgu yürütmelerinin anlaşılması gerekiyorsa, Gelişmiş veritabanı performansı izleme ve sorun giderme için [Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md) kullanmayı göz önünde bulundurun.
   >

## <a name="view-individual-query-details"></a>Tek tek sorgu ayrıntılarını görüntüleme

Sorgu ayrıntılarını görüntülemek için:

1. En üst sorgular listesinden herhangi bir sorgu seçin.

    ![En popüler sorgular listesi](./media/query-performance-insight-use/details.png)

   Ayrıntılı bir görünüm açılır. Zaman içinde CPU tüketimini, süresini ve yürütme sayısını gösterir.

2. Ayrıntılar için grafik özelliklerini seçin.

   * Üstteki grafik, genel veritabanı DTU yüzdesinin bulunduğu bir satır gösterir. Çubuklar, seçilen sorgunun tükettiği CPU yüzdesidir.
   * İkinci grafik seçili sorgunun toplam süresini gösterir.
   * Alt grafik seçili sorgu tarafından toplam yürütme sayısını gösterir.

   ![Sorgu Ayrıntıları](./media/query-performance-insight-use/query-details.png)

3. İsteğe bağlı olarak, kaydırıcıları kullanın, yakınlaştırma düğmelerini kullanın veya sorgu verilerinin nasıl görüntülendiğini özelleştirmek veya farklı bir zaman aralığı seçmek için **Ayarlar** ' ı seçin.

   > [!IMPORTANT]
   > Sorgu Performansı İçgörüleri DDL sorgularını yakalamaz. Bazı durumlarda, tüm geçici sorguları yakalamamayabilir.
   >

## <a name="review-top-queries-per-duration"></a>Süre başına en popüler sorguları gözden geçirin

Sorgu Performansı İçgörüleri iki ölçüm olası performans sorunlarını bulmanıza yardımcı olabilir: süre ve yürütme sayısı.

Uzun süre çalışan sorgular, kaynakları daha uzun süre kilitlemek, diğer kullanıcıları engellemek ve ölçeklenebilirliği kısıtlamak için en büyük olasılıklardır. En iyi duruma getirme için de en iyi adaylardır. Daha fazla bilgi için bkz. [Azure SQL engelleme sorunlarını anlama ve çözme](understand-resolve-blocking.md).

Uzun süre çalışan sorguları belirlemek için:

1. Seçili veritabanı için Sorgu Performansı İçgörüleri **özel** sekmesini açın.
2. Ölçümleri **süre** olarak değiştirin.
3. Sorgu sayısını ve gözlemme aralığını seçin.
4. Toplama işlevini seçin:

   * **Sum** , tüm gözlemlik aralığı için tüm sorgu yürütme süresini ekler.
   * **Maksimum** , yürütme zamanının tam izleme aralığı için en fazla olduğu sorguları bulur.
   * **AVG** , tüm sorgu yürütmelerinin ortalama yürütme süresini bulur ve bu Ortalamalar için en iyi olanları gösterir.

   ![Sorgu süresi](./media/query-performance-insight-use/top-duration.png)

5. Özelleştirilmiş görünümü görmek için **git >** düğmesini seçin.

   > [!IMPORTANT]
   > Sorgu görünümünü ayarlamak DTU satırını güncelleştirmez. DTU çizgisi her zaman Aralık için maksimum tüketim değerini gösterir.
   >
   > Veritabanı DTU kullanımını daha fazla ayrıntı ile (bir dakikaya kadar) anlamak için Azure portal özel bir grafik oluşturmayı düşünün:
   >
   > 1. **Azure SQL veritabanı**  >  **izleme**'yi seçin.
   > 2. **Ölçümler**’i seçin.
   > 3. **+ Grafik Ekle ' yi** seçin.
   > 4. Grafikteki DTU yüzdesini seçin.
   > 5. Ayrıca, sol üst menüden **son 24 saat** ' i seçin ve bir dakika olarak değiştirin.
   >
   > Sorgu performans grafiğine göre karşılaştırmak için özel DTU grafiğini kullanmanızı öneririz.
   >

## <a name="review-top-queries-per-execution-count&quot;></a>Yürütme sayısı başına en popüler sorguları gözden geçirin

Veritabanını kullanan bir kullanıcı uygulaması, yüksek sayıda yürütme veritabanının kendisini etkilemese ve kaynakların kullanımı düşük olsa bile yavaş olabilir.

Bazı durumlarda, yüksek bir yürütme sayısı daha fazla ağ gidiş dönüşlere neden olabilir. Gidiş dönüşler performansı etkiler. Bunlar ağ gecikme süresine ve aşağı akış sunucusu gecikme süresine tabidir.

Örneğin, çok sayıda veri tabanlı Web sitesi, her kullanıcı isteği için veritabanına yoğun bir şekilde erişir. Bağlantı havuzu size yardımcı olmakla birlikte, sunucuda artan ağ trafiği ve işleme yükü performansı düşürebilir. Genel olarak, gidiş dönüş ' ı en düşük tutmaya devam edin.

Sık yürütülen (&quot;geveze") sorgularını belirlemek için:

1. Seçili veritabanı için Sorgu Performansı İçgörüleri **özel** sekmesini açın.
2. Ölçümleri **yürütme sayısı** olarak değiştirin.
3. Sorgu sayısını ve gözlemme aralığını seçin.
4. Özelleştirilmiş görünümü görmek için **git >** düğmesini seçin.

   ![Sorgu yürütme sayısı](./media/query-performance-insight-use/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Performans ayarlama ek açıklamalarını anlama

İş yükünüzü Sorgu Performansı İçgörüleri araştırırken, grafiğin üstünde dikey bir çizgi ile simgeler fark edebilirsiniz.

Bu simgeler ek açıklamalardır. [Azure SQL veritabanı Danışmanı](database-advisor-implement-performance-recommendations.md)performans önerilerini gösterir. Ek açıklamanın üzerine gelindiğinde, performans önerileri hakkında özet bilgiler edinebilirsiniz.

   ![Sorgu ek açıklaması](./media/query-performance-insight-use/annotation.png)

Daha fazlasını anlamak veya Advisor 'ın önerisini uygulamak istiyorsanız, önerilen eylemin ayrıntılarını açmak için simgeyi seçin. Bu etkin bir öneriyiysa, portaldan hemen uygulayabilirsiniz.

   ![Sorgu ek açıklaması ayrıntıları](./media/query-performance-insight-use/annotation-details.png)

Bazı durumlarda, yakınlaştırma düzeyi nedeniyle, birbirini kapatan ek açıklamaların tek bir ek açıklama halinde daraltılması mümkündür. Sorgu Performansı İçgörüleri bunu bir grup ek açıklaması simgesi olarak temsil eder. Grup ek açıklaması simgesi seçildiğinde, ek açıklamaları listeleyen yeni bir dikey pencere açılır.

Sorguları ve performans ayarlama eylemlerinin ilişkilendirilmesi, iş yükünüzü daha iyi anlamanıza yardımcı olabilirler.

## <a name="optimize-the-query-store-configuration"></a>Sorgu deposu yapılandırmasını iyileştirme

Sorgu Performansı İçgörüleri kullanırken aşağıdaki sorgu deposu hata iletilerini görebilirsiniz:

* "Query Store bu veritabanında düzgün şekilde yapılandırılmamış. Daha fazla bilgi için buraya tıklayın. "
* "Query Store bu veritabanında düzgün şekilde yapılandırılmamış. Ayarları değiştirmek için buraya tıklayın. "

Bu iletiler genellikle sorgu deposu yeni veri toplayamıyorum görüntülenir.

İlk durum, sorgu deposu salt okuma durumundaysa ve parametreler en iyi şekilde ayarlanamadığında oluşur. Veri deposunun boyutunu artırarak veya sorgu deposunu temizleyerek bu hatayı çözebilirsiniz. (Sorgu deposunu temizlerseniz, daha önce toplanan tüm telemetri kaybedilir.)

   ![Sorgu deposu ayrıntıları](./media/query-performance-insight-use/qds-off.png)

İkinci durum, sorgu deposu etkinleştirilmediğinde veya parametreler en iyi şekilde ayarlanamadığında oluşur. [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) veya Azure Portal tarafından sunulan aşağıdaki komutları çalıştırarak bekletme ve yakalama ilkesini değiştirebilir ve ayrıca sorgu deposunu etkinleştirebilirsiniz.

### <a name="recommended-retention-and-capture-policy"></a>Önerilen bekletme ve yakalama ilkesi

İki tür bekletme ilkesi vardır:

* **Boyut tabanlı**: Bu ilke **Otomatik** olarak ayarlandıysa, en büyük boyuta yaklaştıklarında verileri otomatik olarak temizler.
* **Zaman tabanlı**: Bu ilke varsayılan olarak 30 güne ayarlanır. Sorgu deposunda boş alan biterse, 30 günden eski olan sorgu bilgilerini silecektir.

Yakalama ilkesini şu şekilde ayarlayabilirsiniz:

* **Tümü**: sorgu deposu tüm sorguları yakalar.
* **Otomatik**: sorgu deposu seyrek olarak derleme ve yürütme süresine sahip seyrek sorguları ve sorguları yoksayar. Yürütme sayısı, derleme süresi ve çalışma zamanı süresi eşikleri dahili olarak belirlenir. Bu varsayılan seçenektir.
* **Hiçbiri**: sorgu deposu yeni sorgular yakalamayı durduruyor, ancak zaten yakalanan sorgulara yönelik çalışma zamanı istatistikleri yine de toplanmaktadır.

[SSMS](/sql/ssms/download-sql-server-management-studio-ssms) veya Azure Portal aşağıdaki komutları yürüterek tüm ilkelerin **Otomatik** olarak ve temizleme ilkesini 30 güne ayarlamamız önerilir. ( `YourDB` Veritabanı adıyla değiştirin.)

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

[SSMS](/sql/ssms/download-sql-server-management-studio-ssms) veya Azure Portal aracılığıyla bir veritabanına bağlanarak sorgu deposunun boyutunu artırın ve aşağıdaki sorguyu çalıştırın. ( `YourDB` Veritabanı adıyla değiştirin.)

```SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

Bu ayarların uygulanması, sorgu deposunun yeni sorgular için telemetri toplamasını sağlayacak. Query Store 'un hemen işlemsel olması gerekiyorsa, SSMS veya Azure portal aracılığıyla aşağıdaki sorguyu çalıştırarak Query Store 'u temizleyebilirsiniz. ( `YourDB` Veritabanı adıyla değiştirin.)

> [!NOTE]
> Aşağıdaki sorgu çalıştırıldığında, daha önce toplanan tüm telemetri sorgu deposunda silinir.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="next-steps"></a>Sonraki adımlar

Tek ve havuza alınmış veritabanlarının, elastik havuzların, yönetilen örneklerin ve örnek veritabanlarının gelişmiş performans izleme için [Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md) kullanmayı düşünün.