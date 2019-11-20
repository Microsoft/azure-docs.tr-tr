---
title: Ölçeği genişletme
description: Hizmet olarak yazılım (SaaS) geliştiricileri, bu araçları kullanarak bulutta esnek, ölçeklenebilir veritabanlarını kolayca oluşturabilir
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 3e30e54eb18eb5d487da6821d285109b1a22297f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823610"
---
# <a name="scaling-out-with-azure-sql-database"></a>Azure SQL Veritabanı ile ölçek genişletme
**Esnek veritabanı** araçlarını kullanarak Azure SQL veritabanlarını kolayca ölçeklendirebilirsiniz. Bu araçlar ve özellikler, işlemsel iş yükleri ve özellikle hizmet olarak yazılım (SaaS) uygulamaları için çözümler oluşturmak üzere **Azure SQL veritabanı** 'nın veritabanı kaynaklarını kullanmanıza olanak sağlar. Elastik veritabanı özellikleri şunlardan oluşur:

* [Elastik veritabanı istemci kitaplığı](sql-database-elastic-database-client-library.md): istemci kitaplığı, parçalı veritabanları oluşturmanıza ve bakımını yapmanıza olanak tanıyan bir özelliktir.  Bkz. [elastik veritabanı araçlarını kullanmaya başlama](sql-database-elastic-scale-get-started.md).
* [Elastik veritabanı bölünmüş birleştirme aracı](sql-database-elastic-scale-overview-split-and-merge.md): verileri parçalı veritabanları arasında taşıdı. Bu araç, verileri çok kiracılı bir veritabanından tek kiracılı bir veritabanına (veya tam tersi) taşımak için faydalıdır. Bkz. [elastik veritabanı bölünmüş birleştirme araç öğreticisi](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Elastik veritabanı işleri](elastic-jobs-overview.md): çok SAYıDA Azure SQL veritabanını yönetmek için işleri kullanın. İşleri kullanarak şema değişiklikleri, kimlik bilgileri yönetimi, başvuru veri güncelleştirmeleri, performans verileri toplama veya kiracı (müşteri) telemetri koleksiyonu gibi yönetim işlemlerini kolayca gerçekleştirin.
* [Elastik veritabanı sorgusu](sql-database-elastic-query-overview.md) (Önizleme): birden çok veritabanına yayılan bir Transact-SQL sorgusu çalıştırmanızı sağlar. Bu, Excel, Power BI, Tableau vb. gibi raporlama araçlarına bağlantı sağlar.
* [Elastik işlemler](sql-database-elastic-transactions-overview.md): Bu özellik, Azure SQL veritabanı 'nda birkaç veritabanına yayılan işlemleri çalıştırmanızı sağlar. Esnek veritabanı işlemleri, ADO .NET kullanan .NET uygulamalarında kullanılabilir ve [System. Transaction sınıflarını](https://msdn.microsoft.com/library/system.transactions.aspx)kullanarak tanıdık programlama deneyimiyle tümleştirilebilir.

Aşağıdaki grafikte, bir veritabanı koleksiyonuyla ilişkili **elastik veritabanı özelliklerini** içeren bir mimari gösterilmektedir.

Bu grafikte, veritabanının renkleri şemaları temsil eder. Aynı renge sahip veritabanları aynı şemayı paylaşır.

1. **Azure SQL veritabanlarının** bir kümesi, Azure 'da parça mimarisi kullanılarak barındırılır.
2. **Elastik veritabanı istemci kitaplığı** , parça kümesini yönetmek için kullanılır.
3. Veritabanlarının bir alt kümesi, **elastik bir havuza**konur. (Bkz. [Havuz nedir?](sql-database-elastic-pool.md)).
4. **Esnek veritabanı işi** , tüm veritabanlarına göre zamanlanmış veya geçici T-SQL betikleri çalıştırır.
5. **Bölünmüş birleştirme aracı** , verileri bir parçadan diğerine taşımak için kullanılır.
6. **Elastik veritabanı sorgusu** , parça kümesindeki tüm veritabanlarına yayılan bir sorgu yazmanızı sağlar.
7. **Elastik işlemler** , çeşitli veritabanlarına yayılan işlemler çalıştırmanızı sağlar. 

![Elastik Veritabanı araçları][1]

## <a name="why-use-the-tools"></a>Araçlar neden kullanılmalıdır?
Bulut uygulamaları için esneklik ve ölçek sağlamak, VM 'Ler ve BLOB depolaması için basit bir işlemdir; birimleri ekleme veya çıkarma veya güç artırma. Ancak, ilişkisel veritabanlarında durum bilgisi olan veri işleme için bir zorluk bıraktı. Bu senaryolarda sorunlar ortaya çıktı:

* İş yükünüzün ilişkisel veritabanı bölümü için büyüyen ve küçültüme kapasitesi.
* Meşgul son müşteri (kiracı) gibi belirli bir veri alt kümesini etkileyerek ortaya çıkabilecek etkin noktaları yönetme.

Geleneksel olarak, bunlar gibi senaryolar, uygulamayı desteklemek için büyük ölçekli veritabanı sunucularına yatırım yaparak karşılanır. Ancak, bu seçenek, tüm işlemlerin önceden tanımlanmış emtia donanımında gerçekleştiği bulutta sınırlıdır. Bunun yerine, verileri ve işlemeyi birçok özdeş yapılandırılmış veritabanına ("parçalama" olarak bilinen bir genişleme deseninin) dağıtmak, hem maliyet hem de esneklik bakımından geleneksel ölçek artırma yaklaşımına bir alternatif sağlar.

## <a name="horizontal-and-vertical-scaling"></a>Yatay ve dikey ölçekleme
Aşağıdaki şekilde, elastik veritabanlarının ölçeklendirilebileceği temel yollar olan ölçeklendirmenin yatay ve dikey boyutları gösterilmektedir.

![Yatay ve dikey ölçeğe karşı][2]

Yatay ölçeklendirme, "ölçeklendirin" olarak da bilinen kapasiteyi veya genel performansı ayarlamak için veritabanlarını ekleme veya kaldırma anlamına gelir. Verilerin özdeş olarak yapılandırılmış veritabanlarının bir koleksiyonu genelinde bölümlendiği parçalama, yatay ölçeklendirmeyi uygulamak için ortak bir yoldur.  

Dikey ölçeklendirme, "ölçeği artırma" olarak da bilinen tek bir veritabanının işlem boyutunu arttırmayı veya azaltmayı ifade eder.

Çoğu bulut ölçeğinde veritabanı uygulaması bu iki stratejinin birleşimini kullanır. Örneğin, bir hizmet uygulaması olan yazılım, her bir son müşteri veritabanının, iş yükünün gerektirdiği kaynakları büyümesini veya daralmasına izin vermek üzere yeni son müşteriler ve dikey ölçekleme sağlamak için yatay ölçeklendirmeyi kullanabilir.

* Yatay ölçeklendirme, [elastik veritabanı istemci kitaplığı](sql-database-elastic-database-client-library.md)kullanılarak yönetilir.
* Dikey ölçeklendirme, hizmet katmanını değiştirmek veya elastik bir havuza veritabanları yerleştirmek için Azure PowerShell cmdlet 'leri kullanılarak gerçekleştirilir.

## <a name="sharding"></a>Parçalama
Parçalama *, büyük* miktarlarda özdeş yapılandırılmış verileri bir dizi bağımsız veritabanına dağıtmaya yönelik bir tekniktir. Özellikle, son müşteriler veya işletmeler için hizmet olarak yazılım (SAAS) teklifleri oluşturan bulut geliştiricileri ile yaygındır. Bu son müşteriler genellikle "kiracılar" olarak adlandırılır. Parçalama, herhangi bir sayıda nedenden dolayı gerekebilir:  

* Toplam veri miktarı tek bir veritabanının kısıtlamalarına sığmayacak kadar büyük
* Genel iş yükünün işlem performansı tek bir veritabanının yeteneklerini aşıyor
* Kiracılar her kiracı için ayrı veritabanları gerektirdiğinden her bir kiracı için fiziksel yalıtım gerektirebilir
* Bir veritabanının farklı bölümlerinin uyumluluk, performans veya politik nedenlerle farklı coğrafi bölgelerde bulunması gerekebilir.

Dağıtılmış cihazlardan verilerin alımı gibi diğer senaryolarda, parça zamana bağlı düzenlenmiş bir veritabanı kümesini dolduracak şekilde kullanılabilir. Örneğin, ayrı bir veritabanı her gün veya haftaya ayrılabilir. Bu durumda, parçalı anahtar, tarihi temsil eden bir tamsayı olabilir (parçalı tabloların tüm satırlarında mevcuttur) ve bir tarih aralığı için bilgi alma sorguları uygulama tarafından söz konusu aralığı kapsayan veritabanlarının alt kümesine yönlendirilmelidir.

Parçalara ayırma en iyi şekilde çalışarak bir uygulamadaki her işlem, bir parçalama anahtarının tek bir değeriyle kısıtlanabilir. Bu, tüm işlemlerin belirli bir veritabanına yerelmesini sağlar.

## <a name="multi-tenant-and-single-tenant"></a>Çok kiracılı ve tek kiracılı
Bazı uygulamalar her kiracı için ayrı bir veritabanı oluşturmanın en basit yaklaşımını kullanır. Bu yaklaşım, kiracının ayrıntı düzeyinde yalıtım, yedekleme/geri yükleme yeteneği ve kaynak ölçekleme sağlayan **tek kiracılı** parçalara ayırma modelidir. Tek kiracılı parçalar ile, her veritabanı belirli bir kiracı KIMLIĞI değeri (veya müşteri anahtar değeri) ile ilişkilendirilir, ancak bu anahtar, her zaman verilerin kendisinde bulunmamalıdır. Her isteği uygun veritabanına yönlendirmek uygulamanın sorumluluğundadır ve istemci kitaplığı bunu basitleştirebilir.

![Tek kiracılı ve çok kiracılı][4]

Diğer senaryolar, farklı veritabanlarına yalıtmak yerine birden çok kiracıyı veritabanları halinde paketler. Bu model tipik bir çok kiracılı parçalama düzenlidir ve bir uygulamanın çok sayıda küçük **kiracıyı** yönettiği bir olay olabilir. Çok kiracılı parçalar halinde, veritabanı tablolarındaki satırlar, kiracı KIMLIĞINI veya parçalama anahtarını tanımlayan bir anahtarı yürütmek üzere tasarlanmıştır. Yine, uygulama katmanı, kiracının uygun veritabanına yönelik isteğini yönlendirmekten sorumludur ve bu, elastik veritabanı istemci kitaplığı tarafından desteklenebilir. Ayrıca, her kiracının erişebileceği satırları filtrelemek için satır düzeyi güvenlik kullanılabilir-Ayrıntılar için bkz. [elastik veritabanı araçları ve satır düzeyi güvenliği olan çok kiracılı uygulamalar](sql-database-elastic-tools-multi-tenant-row-level-security.md). Veritabanları arasında verileri yeniden dağıtma, çok kiracılı parçalama düzeniyle gerekli olabilir ve elastik veritabanı bölünmüş birleştirme aracı tarafından kolaylaştıralınmıştır. Elastik havuzları kullanan SaaS uygulamalarının tasarım desenleri hakkında daha fazla bilgi edinmek için bkz. [Azure SQL Veritabanı kullanan Çok Kiracılı SaaS Uygulamaları için Tasarım Desenleri](sql-database-design-patterns-multi-tenancy-saas-applications.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Verileri birden çok sunucudan tek kiracılı veritabanlarına taşıma
SaaS uygulaması oluştururken, olası müşterilere Yazılımın deneme sürümünü sunma normaldir. Bu durumda, veriler için çok kiracılı bir veritabanı kullanmak uygun maliyetli bir durumdur. Bununla birlikte, müşteri adayı müşteri olduğunda, daha iyi performans sağladığından tek kiracılı bir veritabanı daha iyidir. Müşterinin deneme süresinde veri oluşturmuş olması halinde, verileri çok kiracılı yeni tek kiracılı veritabanına taşımak için [bölünmüş birleştirme aracını](sql-database-elastic-scale-overview-split-and-merge.md) kullanın.

## <a name="next-steps"></a>Sonraki adımlar
İstemci kitaplığını gösteren örnek bir uygulama için bkz. [elastik veritabanı araçlarıyla çalışmaya başlama](sql-database-elastic-scale-get-started.md).

Mevcut veritabanlarını araçları kullanacak şekilde dönüştürmek için bkz. [genişleme için mevcut veritabanlarını geçirme](sql-database-elastic-convert-to-use-elastic-tools.md).

Elastik havuzun özelliklerini görmek için bkz. [elastik havuz Için fiyat ve performans konuları](sql-database-elastic-pool.md), [elastik havuzlarla](sql-database-elastic-pool-manage-portal.md)yeni bir havuz oluşturma.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

