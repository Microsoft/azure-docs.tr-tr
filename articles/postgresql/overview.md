---
title: PostgreSQL için Azure veritabanı nedir?
description: Esnek sunucu bağlamında PostgreSQL için Azure veritabanı ilişkisel veritabanı hizmetine genel bir bakış sağlar.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: c3ea7930f41fe89538a817da032e993e534db9cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92491334"
---
# <a name="what-is-azure-database-for-postgresql"></a>PostgreSQL için Azure Veritabanı nedir?

PostgreSQL için Azure veritabanı, Microsoft bulutundaki [PostgreSQL Community Edition](https://www.postgresql.org/) 'ı temel alan ilişkisel bir veritabanı hizmetidir (GPLv2 lisansı altında bulunabilir) veritabanı altyapısı. PostgreSQL için Azure Veritabanı şunları getirir:

- Yerleşik yüksek kullanılabilirlik.
- Otomatik yedeklemeler ve en çok 35 güne kadar bir noktadan sonra geri yükleme kullanarak veri koruma.
- Hizmeti güvenli ve güncel tutmak için temel alınan donanım, işletim sistemi ve veritabanı altyapısı için otomatik bakım.
- Kullandıkça öde fiyatlandırması ile tahmin edilebilir performans.
- Saniyeler içinde elastik ölçekleme.
- Önemli verileri REST ve hareket halindeyken korumak için kurumsal düzeyde güvenlik ve sektör lideri uyumluluk.
- Büyük ölçekli dağıtımlar için yönetimi ve izlemeyi basitleştirmek üzere izleme ve otomatikleştirme.
- Sektör lideri destek deneyimi.

 :::image type="content" source="./media/overview/overview-what-is-azure-postgres.png" alt-text="PostgreSQL için Azure Veritabanı":::

Bu özellikler neredeyse hiç yönetim gerektirmez ve tümüyle ek ücret ödemeden sağlanır. Bu kişiler, sanal makineleri ve altyapıyı yönetmek için değerli zaman ve kaynakları ayırmak yerine hızlı uygulama geliştirmeye odaklanmanıza ve zamanınızı pazara sunma olanağı sağlar. Buna ek olarak, kendi seçtiğiniz açık kaynak araçları ve platformuyla uygulamanızı geliştirmeye devam edebilir, böylelikle yeni beceriler edinmek zorunda kalmadan işlerinizin gerektirdiği hızla ve verimlilikle kullanıma sunabilirsiniz.

## <a name="deployment-models"></a>Dağıtım modelleri

PostgreSQL Community Edition tarafından desteklenen PostgreSQL için Azure veritabanı, üç dağıtım modunda sunulmaktadır:

- Tek sunucu
- Esnek Sunucu (Önizleme)
- Hiper Ölçek (Citus)

### <a name="azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure Veritabanı - Tek Sunucu

PostgreSQL için Azure veritabanı tek sunucu, veritabanı özelleştirmeleri için en az gereksinimle tam olarak yönetilen bir veritabanı hizmetidir. Tek sunucu platformu, düzeltme eki uygulama, yedekleme, yüksek kullanılabilirlik, en az Kullanıcı Yapılandırması ve denetimiyle güvenlik gibi veritabanı yönetim işlevlerinin çoğunu işleyecek şekilde tasarlanmıştır. Mimari, tek kullanılabilirlik bölgesinde% 99,99 kullanılabilirlik ile yerleşik yüksek kullanılabilirlik için iyileştirilmiştir. PostgreSQL 9,5, 9, 6, 10 ve 11 Community sürümünü destekler. Hizmet, günümüzde çok çeşitli [Azure bölgelerinde](https://azure.microsoft.com/global-infrastructure/services/)kullanılabilir.

Tek Sunucu dağıtım seçeneği şu üç fiyatlandırma katmanına sahiptir: Temel, Genel Amaçlı ve Bellek İçin İyileştirilmiş. Her katman veritabanı iş yükünüzü desteklemek için farklı kaynak özellikleri sunar. İlk uygulamanızı aylık birkaç dolar ücretle küçük bir veritabanı üzerinde oluşturabilir ve sonra çözümünüzün gereksinimlerine göre ölçeği ayarlayabilirsiniz. Dinamik ölçeklendirebilirlik, veritabanınızın hızla değişen kaynak gereksinimlerine saydam bir şekilde yanıt verebilmesini sağlar. Yalnızca ihtiyacınız olan kaynaklar için ve yalnızca bunlara ihtiyacınız olduğunda ödeme yaparsınız. Ayrıntılar için bkz. [Fiyatlandırma katmanları](./concepts-pricing-tiers.md).

Tek sunucular, düzeltme eki uygulama ve özel PostgreSQL yapılandırma ayarları üzerinde ayrıntılı denetim gereksinimi olmadan otomatik düzeltme eki uygulamayı işleyecek şekilde tasarlanan bulut Yerel uygulamaları için idealdir.

Tek sunuculu dağıtım moduna yönelik ayrıntılı genel bakış için [tek sunucuya genel bakış](./overview-single-server.md)konusuna bakın.

### <a name="azure-database-for-postgresql---flexible-server-preview"></a>PostgreSQL için Azure Veritabanı - Esnek Sunucu (Önizleme)

PostgreSQL için Azure veritabanı esnek sunucu, veritabanı yönetim işlevleri ve yapılandırma ayarları üzerinde daha ayrıntılı denetim ve esneklik sağlamak için tasarlanmış, tam olarak yönetilen bir veritabanı hizmetidir. Genel olarak, hizmet kullanıcı gereksinimlerine göre daha fazla esneklik ve özelleştirmeler sağlar. Esnek sunucu mimarisi, kullanıcıların tek kullanılabilirlik bölgesinde ve birden çok kullanılabilirlik alanında yüksek kullanılabilirliği kabul etmesine olanak tanır. Esnek sunucu, sürekli olarak tam işlem kapasitesi gerektirmeyen iş yükleri için ideal olan sunucu ve Burstable işlem katmanını durdurma/başlatma yeteneğine sahip daha iyi maliyet iyileştirme denetimleri sağlar. Hizmet şu anda daha yeni sürümler ekleme planlarından PostgreSQL 11 ve 12 ' nin topluluk sürümünü desteklemektedir. Hizmet şu anda genel önizleme aşamasındadır ve çok çeşitli Azure bölgelerinde kullanılabilir.

Esnek sunucular için en uygun

- Daha iyi denetim ve özelleştirmeler gerektiren uygulama geliştirmeleri.
- Sunucuyu durdurma/başlatma yeteneğine sahip maliyet iyileştirme denetimleri.
- Bölge yedekli yüksek kullanılabilirlik
- Yönetilen bakım pencereleri
  
Esnek sunucu dağıtım moduna yönelik ayrıntılı bir genel bakış için bkz. [esnek sunucuya genel bakış](./flexible-server/overview.md).

### <a name="azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL için Azure veritabanı – hiper ölçek (Citus)

Hiper Ölçek (Citus) seçeneği, parçalama özelliğinden faydalanarak sorguları yatay olarak birden fazla makineye ölçeklendirir. Sorgu altyapısı, büyük veri kümelerinde daha hızlı yanıt elde etmek için gelen SQL sorgularını paralel bir şekilde bu sunuculara dağıtır. Genellikle 100 GB ve daha fazla veri içeren iş yükleri gibi yüksek ölçek ve performans gereksinimlerine sahip olan uygulamalar için kullanılır.

Hiper Ölçek (Citus) dağıtım seçeneği şu özellikleri sunar:

- Parçalama kullanarak birden çok makinenin yatay olarak ölçeklendirilmesi
- Büyük veri kümelerinde daha hızlı yanıt almak için sunucular arasında sorgu paralelleştirme
- Çok kiracılı uygulamalar, gerçek zamanlı işlem analizi ve yüksek aktarım hızı gerektiren işlem tabanlı iş yükleri için tam destek
  
PostgreSQL için derlenmiş uygulamalar, standart [bağlantı kitaplıkları](./concepts-connection-libraries.md) ve en az değişiklikle hiper ölçekte (Citus) dağıtılmış sorgular çalıştırabilir.

## <a name="next-steps"></a>Sonraki adımlar

PostgreSQL için Azure veritabanı 'nın üç dağıtım modu hakkında daha fazla bilgi edinin ve gereksinimlerinize göre doğru seçenekleri belirleyin.

- [Tek sunucu](./overview-single-server.md)
- [Esnek sunucu](./flexible-server/overview.md)
- Hiper Ölçek (Citus)