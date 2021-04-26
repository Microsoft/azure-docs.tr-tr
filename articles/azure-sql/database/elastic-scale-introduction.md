---
title: Ölçeği genişletme
description: Hizmet olarak yazılım (SaaS) geliştiricileri, bu araçları kullanarak bulutta esnek, ölçeklenebilir veritabanlarını kolayca oluşturabilir
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 3f00b2c1a8a8264267aa8ae68d80890adeb642ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98059154"
---
# <a name="scaling-out-with-azure-sql-database"></a>Azure SQL Veritabanı ile ölçek genişletme
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

**Elastik veritabanı** araçlarını kullanarak Azure SQL veritabanı 'nda veritabanlarını kolayca ölçeklendirebilirsiniz. Bu araçlar ve özellikler, işlemsel iş yükleri ve özellikle hizmet olarak yazılım (SaaS) uygulamaları için çözümler oluşturmak üzere **Azure SQL veritabanı** 'nın veritabanı kaynaklarını kullanmanıza olanak sağlar. Elastik veritabanı özellikleri şunlardan oluşur:

* [Elastik veritabanı istemci kitaplığı](elastic-database-client-library.md): istemci kitaplığı, parçalı veritabanları oluşturmanıza ve bakımını yapmanıza olanak tanıyan bir özelliktir.  Bkz. [elastik veritabanı araçlarını kullanmaya başlama](elastic-scale-get-started.md).
* [Elastik veritabanı bölünmüş birleştirme aracı](elastic-scale-overview-split-and-merge.md): verileri parçalı veritabanları arasında taşıdı. Bu araç, verileri çok kiracılı bir veritabanından tek kiracılı bir veritabanına (veya tam tersi) taşımak için faydalıdır. Bkz. [elastik veritabanı Split-Merge araç öğreticisi](elastic-scale-configure-deploy-split-and-merge.md).
* [Elastik veritabanı işleri](elastic-jobs-overview.md) (Önizleme): Azure SQL veritabanındaki çok sayıda veritabanını yönetmek Için işleri kullanın. İşleri kullanarak şema değişiklikleri, kimlik bilgileri yönetimi, başvuru veri güncelleştirmeleri, performans verileri toplama veya kiracı (müşteri) telemetri koleksiyonu gibi yönetim işlemlerini kolayca gerçekleştirin.
* [Elastik veritabanı sorgusu](elastic-query-overview.md) (Önizleme): birden çok veritabanına yayılan bir Transact-SQL sorgusu çalıştırmanızı sağlar. Bu, Excel, Power BI, Tableau vb. gibi raporlama araçlarına bağlantı sağlar.
* [Elastik işlemler](elastic-transactions-overview.md): Bu özellik, birkaç veritabanına yayılan işlemleri çalıştırmanızı sağlar. Esnek veritabanı işlemleri, ADO .NET kullanan .NET uygulamalarında kullanılabilir ve [System. Transaction sınıflarını](/dotnet/api/system.transactions)kullanarak tanıdık programlama deneyimiyle tümleştirilebilir.

Aşağıdaki grafikte, bir veritabanı koleksiyonuyla ilişkili **elastik veritabanı özelliklerini** içeren bir mimari gösterilmektedir.

Bu grafikte, veritabanının renkleri şemaları temsil eder. Aynı renge sahip veritabanları aynı şemayı paylaşır.

1. **SQL veritabanlarının** bir kümesi, Azure 'da parça mimarisi kullanılarak barındırılır.
2. **Elastik veritabanı istemci kitaplığı** , parça kümesini yönetmek için kullanılır.
3. Veritabanlarının bir alt kümesi, **elastik bir havuza** konur. (Bkz. [Havuz nedir?](elastic-pool-overview.md)).
4. **Esnek veritabanı işi** , tüm veritabanlarına göre zamanlanmış veya geçici T-SQL betikleri çalıştırır.
5. **Bölünmüş birleştirme aracı** , verileri bir parçadan diğerine taşımak için kullanılır.
6. **Elastik veritabanı sorgusu** , parça kümesindeki tüm veritabanlarına yayılan bir sorgu yazmanızı sağlar.
7. **Elastik işlemler** , çeşitli veritabanlarına yayılan işlemler çalıştırmanızı sağlar. 

![Elastik Veritabanı araçları][1]

## <a name="why-use-the-tools"></a>Araçlar neden kullanılmalıdır?

Bulut uygulamaları için esneklik ve ölçek sağlamak, VM 'Ler ve BLOB depolaması için basit bir işlemdir; birimleri ekleme veya çıkarma veya güç artırma. Ancak, ilişkisel veritabanlarında durum bilgisi olan veri işleme için bir zorluk bıraktı. Bu senaryolarda sorunlar ortaya çıktı:

* İş yükünüzün ilişkisel veritabanı bölümü için büyüyen ve küçültüme kapasitesi.
* Meşgul son müşteri (kiracı) gibi belirli bir veri alt kümesini etkileyerek ortaya çıkabilecek etkin noktaları yönetme.

Geleneksel olarak, bunlar gibi senaryolar uygulamayı desteklemek için daha büyük ölçekli sunuculara yatırım yaparak karşılanır. Ancak, bu seçenek, tüm işlemlerin önceden tanımlanmış emtia donanımında gerçekleştiği bulutta sınırlıdır. Bunun yerine, verileri ve işlemeyi birçok özdeş yapılandırılmış veritabanına ("parçalama" olarak bilinen bir genişleme deseninin) dağıtmak, hem maliyet hem de esneklik bakımından geleneksel ölçek artırma yaklaşımına bir alternatif sağlar.

## <a name="horizontal-and-vertical-scaling"></a>Yatay ve dikey ölçekleme

Aşağıdaki şekilde, elastik veritabanlarının ölçeklendirilebileceği temel yollar olan ölçeklendirmenin yatay ve dikey boyutları gösterilmektedir.

![Yatay ve dikey ölçeğe karşı][2]

Yatay ölçeklendirme, "ölçeklendirin" olarak da bilinen kapasiteyi veya genel performansı ayarlamak için veritabanlarını ekleme veya kaldırma anlamına gelir. Verilerin özdeş olarak yapılandırılmış veritabanlarının bir koleksiyonu genelinde bölümlendiği parçalama, yatay ölçeklendirmeyi uygulamak için ortak bir yoldur.  

Dikey ölçeklendirme, "ölçeği artırma" olarak da bilinen tek bir veritabanının işlem boyutunu arttırmayı veya azaltmayı ifade eder.

Çoğu bulut ölçeğinde veritabanı uygulaması bu iki stratejinin birleşimini kullanır. Örneğin, bir hizmet uygulaması olan yazılım, her bir son müşteri veritabanının, iş yükünün gerektirdiği kaynakları büyümesini veya daralmasına izin vermek üzere yeni son müşteriler ve dikey ölçekleme sağlamak için yatay ölçeklendirmeyi kullanabilir.

* Yatay ölçeklendirme, [elastik veritabanı istemci kitaplığı](elastic-database-client-library.md)kullanılarak yönetilir.
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

Diğer senaryolar, farklı veritabanlarına yalıtmak yerine birden çok kiracıyı veritabanları halinde paketler. Bu model tipik bir çok kiracılı parçalama düzenlidir ve bir uygulamanın çok sayıda küçük **kiracıyı** yönettiği bir olay olabilir. Çok kiracılı parçalar halinde, veritabanı tablolarındaki satırlar, kiracı KIMLIĞINI veya parçalama anahtarını tanımlayan bir anahtarı yürütmek üzere tasarlanmıştır. Yine, uygulama katmanı, kiracının uygun veritabanına yönelik isteğini yönlendirmekten sorumludur ve bu, elastik veritabanı istemci kitaplığı tarafından desteklenebilir. Ayrıca, her kiracının erişebileceği satırları filtrelemek için satır düzeyi güvenlik kullanılabilir-Ayrıntılar için bkz. [elastik veritabanı araçları ve satır düzeyi güvenliği olan çok kiracılı uygulamalar](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md). Veritabanları arasında verileri yeniden dağıtma, çok kiracılı parçalama düzeniyle gerekli olabilir ve elastik veritabanı bölünmüş birleştirme aracı tarafından kolaylaştıralınmıştır. Elastik havuzları kullanan SaaS uygulamalarının tasarım desenleri hakkında daha fazla bilgi edinmek için bkz. [Azure SQL Veritabanı kullanan Çok Kiracılı SaaS Uygulamaları için Tasarım Desenleri](saas-tenancy-app-design-patterns.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Verileri birden çok sunucudan tek kiracılı veritabanlarına taşıma
SaaS uygulaması oluştururken, olası müşterilere Yazılımın deneme sürümünü sunma normaldir. Bu durumda, veriler için çok kiracılı bir veritabanı kullanmak uygun maliyetli bir durumdur. Bununla birlikte, müşteri adayı müşteri olduğunda, daha iyi performans sağladığından tek kiracılı bir veritabanı daha iyidir. Müşterinin deneme süresinde veri oluşturmuş olması halinde, verileri çok kiracılı yeni tek kiracılı veritabanına taşımak için [bölünmüş birleştirme aracını](elastic-scale-overview-split-and-merge.md) kullanın.

## <a name="next-steps"></a>Sonraki adımlar
İstemci kitaplığını gösteren örnek bir uygulama için bkz. [elastik veritabanı araçlarıyla çalışmaya başlama](elastic-scale-get-started.md).

Mevcut veritabanlarını araçları kullanacak şekilde dönüştürmek için bkz. [genişleme için mevcut veritabanlarını geçirme](elastic-convert-to-use-elastic-tools.md).

Elastik havuzun özelliklerini görmek için bkz. [elastik havuz Için fiyat ve performans konuları](elastic-pool-overview.md), [elastik havuzlarla](elastic-pool-manage.md)yeni bir havuz oluşturma.  

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/elastic-scale-introduction/tools.png
[2]:./media/elastic-scale-introduction/h_versus_vert.png
[3]:./media/elastic-scale-introduction/overview.png
[4]:./media/elastic-scale-introduction/single_v_multi_tenant.png
