---
title: Performans önerileri
description: Azure SQL veritabanı, geçerli sorgu performansını iyileştirebilecek SQL veritabanlarınıza yönelik öneriler sağlar.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik
ms.date: 11/12/2019
ms.openlocfilehash: a113ea3fd4828a498d1f53ea7604df7bc8588eb5
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048401"
---
# <a name="performance-recommendations-for-sql-database"></a>SQL veritabanı için performans önerileri

Azure SQL veritabanı öğrenir ve uygulamanızla uyum sağlar. SQL veritabanlarınızın performansını en üst düzeye çıkarmanıza olanak sağlayan özelleştirilmiş öneriler sağlar. SQL veritabanı sürekli olarak değerlendirir ve SQL veritabanlarınızın kullanım geçmişini analiz eder. Sağlanan öneriler veritabanı benzersiz iş yükü desenlerine dayalıdır ve performansı artırmaya yardımcı olur.

> [!TIP]
> [Otomatik ayarlama](sql-database-automatic-tuning.md) , en yaygın veritabanı performans sorunlarından bazılarını otomatik olarak ayarlamak için önerilen yöntemdir. [Sorgu performansı öngörüleri](sql-database-query-performance.md) , temel Azure SQL veritabanı performans izleme ihtiyaçları için önerilen yöntemdir. [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) , otomatik performans sorunlarını gidermeye yönelik yerleşik zeka ile veritabanı performansının ölçekteki gelişmiş izlenmesi için önerilen yöntemdir.
>

## <a name="performance-recommendations-options"></a>Performans önerileri seçenekleri

Performans önerisi seçenekleri kullanılabilir Azure SQL veritabanı:

| Performans önerisi | Tek veritabanı ve havuza alınmış veritabanı desteği | Örnek veritabanı desteği |
| :----------------------------- | ----- | ----- |
| **Dizin önerileri oluşturma** -iş yükünüzün performansını iyileştirebilecek dizinlerin oluşturulmasını önerir. | Yes | Hayır | 
| **Dizin önerilerini bırakma** -benzersiz dizinler ve uzun süredir kullanılmamış dizinler dışında, yedekli ve yinelenen dizinlerin her gün kaldırılmasını önerir (> 90 gün). Bu seçeneğin bölüm değiştirme ve Dizin ipuçlarını kullanan uygulamalarla uyumlu olmadığına lütfen unutmayın. Kullanılmayan dizinleri bırakma, Premium ve İş Açısından Kritik hizmet katmanlarında desteklenmez. | Yes | Hayır |
| **Sorgu önerilerini Parametreleştirme (Önizleme)** -sürekli olarak yeniden derlenen ancak aynı sorgu yürütme planına sahip bir veya daha fazla sorgunuz olduğunda zorlamalı parametrization önerilir. | Yes | Hayır |
| **Şema sorunları önerilerini düzeltme (Önizleme)** -SQL veritabanı HIZMETI, SQL veritabanınızda oluşan şema Ile ilgili SQL hatalarının sayısında bir anomali fark edildiğinde şema düzeltme için öneriler görüntülenir. Microsoft şu anda "şema sorununu çözme" önerilerini kullanımdan kaldıramamaktadır. | Yes | Hayır |

## <a name="create-index-recommendations"></a>Dizin önerileri oluşturma
SQL veritabanı, çalıştıran sorguları sürekli izler ve performansı iyileştirebilecek dizinleri tanımlar. Belirli bir dizinin eksik olduğu güvenden sonra yeni bir **Dizin oluşturma** önerisi oluşturulur.

 Azure SQL veritabanı, dizinin bir süre içinde dolaştıracağından emin olarak güven oluşturur. Tahmini performans kazanmasına bağlı olarak, öneriler yüksek, orta veya düşük olarak kategorize edilir. 

Öneriler kullanılarak oluşturulan dizinler her zaman otomatik olarak oluşturulan dizinler olarak işaretlenir. Sys. Indexes görünümüne bakarak hangi dizinlerin otomatik olarak oluşturulduğunu görebilirsiniz. Otomatik oluşturulan dizinler DEĞIŞTIRME/yeniden adlandırma komutlarını engellemez. 

Otomatik olarak oluşturulmuş bir dizine sahip sütunu bırakmaya çalışırsanız, komut geçer. Otomatik oluşturulan dizin, komutla birlikte bırakılır. Normal dizinler, dizini oluşturulmuş sütunlarda ALTER/RENAME komutunu engeller.

Dizin oluşturma önerisi uygulandıktan sonra Azure SQL veritabanı, sorguların performansını taban çizgisi performansına göre karşılaştırır. Yeni Dizin performansı artırdıysa, öneri başarılı olarak işaretlenir ve etki raporu kullanılabilir. Dizin performansı iyileştirmediyse, otomatik olarak geri döndürülür. SQL veritabanı, önerilerin veritabanı performansını iyileştirdiğinden emin olmak için bu işlemi kullanır.

Herhangi bir **Dizin oluşturma** önerisi, bir veritabanının veya havuzun kaynak kullanımı yüksek olduğunda öneriyi uygulamaya izin veren bir geri dönüş ilkesine sahiptir. Geri ödeme ilkesi, hesap CPU 'SU, veri GÇ, günlük GÇ ve kullanılabilir depolama alanına girer. 

CPU, veri GÇ veya günlük GÇ, önceki 30 dakika içinde %80 ' den yüksekse, dizin oluşturma önerisi ertelenir. Kullanılabilir depolama alanı dizin oluşturulduktan sonra %10 ' un altında olursa öneri bir hata durumuna geçer. Birkaç gün sonra otomatik ayarlama, dizinin faydalı olacağını düşünmeye devam ediyorsa, işlem yeniden başlatılır. 

Bu işlem, dizin oluşturmak için yeterli kullanılabilir depolama alanı olana kadar veya dizin artık yararlı olarak görülene kadar yinelenir.

## <a name="drop-index-recommendations"></a>Dizin önerilerini bırak
Eksik dizinlerin algılanmasının yanı sıra SQL veritabanı, mevcut dizinlerin performansını sürekli olarak analiz eder. Bir dizin kullanılmıyorsa, Azure SQL veritabanı bunu bırakmayı önerir. İki durumda da dizin bırakma önerilir:
* Dizin, başka bir dizinin yinelemesi (aynı dizinli ve dahil edilen sütun, Bölüm şeması ve filtreler).
* Dizin, uzun süreli bir süre (93 gün) için kullanılmadı.

Bırakma dizini önerileri, Ayrıca, uygulamadan sonra doğrulama aracılığıyla da gider. Performans artdığı takdirde, etki raporu kullanılabilir. Performans düşer ise, öneri geri döndürülür.

## <a name="parameterize-queries-recommendations-preview"></a>Sorgu önerilerini Parametreleştirme (Önizleme)
*Parametreli* sorgu önerileri, sürekli olarak yeniden derlenmesi gereken ancak aynı sorgu yürütme planıyla biten bir veya daha fazla sorgunuz olduğunda görünür. Bu koşul zorunlu Parametreleştirme uygulamak için bir fırsat oluşturur. Zorunlu Parametreleştirme, daha sonra sorgu planlarının önbelleğe alınıp daha sonra yeniden kullanılmasını sağlar ve bu da performansı artırır ve kaynak kullanımını azaltır. 

Başlangıçta SQL Server karşı verilen her sorgunun bir yürütme planı oluşturmak için derlenmesi gerekir. Oluşturulan her plan plan önbelleğine eklenir. Aynı sorgunun sonraki yürütmeleri bu planı önbellekten yeniden kullanabilir ve bu da ek derleme ihtiyacını ortadan kaldırır. 

Parametreli olmayan değerlere sahip sorgular, parametreli olmayan değerler farklı olan her seferinde yürütme planı yeniden derlendiğinden performans yüküne neden olabilir. Çoğu durumda, farklı parametre değerlerine sahip aynı sorgular aynı yürütme planlarını oluşturur. Ancak, bu planlar hala plan önbelleğine ayrı olarak eklenir. 

Yürütme planlarını yeniden derleme işlemi veritabanı kaynaklarını kullanır, sorgu süresi süresini artırır ve plan önbelleğinin dışına taşıyor. Bu olaylar, sırayla önbellekten çıkarılmasına neden olur. Bu SQL Server davranışı, veritabanında zorlanan Parametreleştirme seçeneği ayarlanarak değiştirilebilir. 

Bu önerinin etkisini tahmin etmenize yardımcı olmak için, gerçek CPU kullanımı ve öngörülen CPU kullanımı (öneri uygulanmış gibi) arasında bir karşılaştırma sağlanır. Bu öneri, CPU tasarrufu elde etmenize yardımcı olabilir. Ayrıca, plan önbelleğinin sorgu süresini ve ek yükünü azaltmanıza yardımcı olabilir. Bu, planlardan daha fazla planın önbellekte kalabileceği ve yeniden kullanılması anlamına gelir. **Uygula** komutunu seçerek bu öneriyi hızlıca uygulayabilirsiniz. 

Bu öneriyi uyguladıktan sonra, veritabanınızda dakika içinde zorunlu Parametreleştirme etkinleştirilir. Yaklaşık 24 saat süren izleme işlemini başlatır. Bu dönemden sonra doğrulama raporunu görebilirsiniz. Bu rapor, önerinin uygulandıktan önce ve sonra veritabanınızın CPU kullanımını 24 saat gösterir. SQL Veritabanı Danışmanı, performans gerileme algılanırsa uygulanan öneriyi otomatik olarak geri alan bir güvenlik mekanizmasına sahiptir.

## <a name="fix-schema-issues-recommendations-preview"></a>Şema sorunları önerilerini çözme (Önizleme)

> [!IMPORTANT]
> Microsoft şu anda "şema sorununu çözme" önerilerini kullanımdan kaldıramamaktadır. Daha önce bahsedilen "şema sorununu çözme" önerilerini içeren şema sorunları da dahil olmak üzere, veritabanı performans sorunlarınızı izlemek için [akıllı içgörüler](sql-database-intelligent-insights.md) kullanmanızı öneririz.
> 

SQL veritabanı hizmeti, SQL veritabanınızda oluşan şema ile ilgili SQL hatalarının sayısında bir anomali fark edildiğinde, **şema sorunları önerilerini düzeltir** . Bu öneri genellikle veritabanınız bir saat içinde birden fazla şemaya ilişkin hatayla (geçersiz sütun adı, geçersiz nesne adı vb.) karşılaştığında görüntülenir.

"Şema sorunları" SQL Server bir sözdizimi hataları sınıfıdır. SQL sorgusunun tanımı ve veritabanı şemasının tanımı hizalanmazsa bunlar oluşur. Örneğin, sorgu tarafından beklenen sütunlardan biri hedef tabloda eksik olabilir veya bunun tersi de geçerlidir. 

Azure SQL veritabanı hizmeti, SQL veritabanınızda oluşan şemaya ilişkin SQL hatalarının sayısında bir anomali fark edildiğinde "şema sorununu çözme" önerisi görüntülenir. Aşağıdaki tabloda, şema sorunlarıyla ilgili hatalar gösterilmektedir:

| SQL hata kodu | İleti |
| --- | --- |
| 201 |' ' Yordamı veya işlevi, sağlanmamış *' ' parametresini bekliyor*. |
| 207 |Geçersiz sütun adı ' * '. |
| 208 |Geçersiz nesne adı ' * '. |
| 213 |Girilen değerlerin sütun adı veya numarası tablo tanımıyla eşleşmiyor. |
| 2812 |' * ' Saklı yordamı bulunamadı. |
| 8144 |Yordam veya işlev *, çok fazla sayıda bağımsız değişken belirtti. |

## <a name="custom-applications"></a>Özel uygulamalar

Geliştiriciler, Azure SQL veritabanı için performans önerilerini kullanarak özel uygulamalar geliştirmeyi düşünebilirler. Bir veritabanı için Portalda listelenen tüm önerilere [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) API aracılığıyla erişilebilir.

## <a name="next-steps"></a>Sonraki adımlar
Önerilerinizi izleyin ve performansı iyileştirmek için bunları uygulamaya devam edin. Veritabanı iş yükleri dinamiktir ve sürekli olarak değişir. SQL Veritabanı Danışmanı, veritabanınızın performansını iyileştirebilecek öneriler sunmaya devam eder. 

* Veritabanı dizinlerinin ve sorgu yürütme planlarının otomatik olarak ayarlanması hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı otomatik ayarlama](sql-database-automatic-tuning.md).
* Performans sorunlarının otomatik tanılama ve kök neden analizine sahip veritabanı performansını otomatik olarak izleme hakkında daha fazla bilgi için bkz. [Azure SQL akıllı içgörüler](sql-database-intelligent-insights.md).
*  Azure portal performans önerilerini kullanma hakkında daha fazla bilgi için [Azure Portal performans önerilerine](sql-database-advisor-portal.md)bakın.
* En iyi sorguların performans etkisini öğrenmek ve görüntülemek için bkz. [sorgu Performans öngörüleri](sql-database-query-performance.md) .


