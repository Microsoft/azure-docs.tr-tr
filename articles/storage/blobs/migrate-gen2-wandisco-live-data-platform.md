---
title: Azure için Data Lake Storage ve WANdisco LiveData platform (Önizleme)
description: Azure için WANdisco LiveData platformunu kullanarak şirket içi Hadoop verilerini Azure Data Lake Storage 2. 'e geçirin.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 11/17/2020
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: a0d02530ba2b8758b467b77ff639437675e4cc81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99508938"
---
# <a name="meet-demanding-migration-requirements-with-wandisco-livedata-platform-for-azure-preview"></a>Azure için WANdisco LiveData platform ile yoğun geçiş gereksinimlerini karşılayın (Önizleme)

[Azure Için WANdisco LiveData platformunu](https://docs.wandisco.com/live-data-platform/docs/landing/)kullanarak şirket içi Hadoop verilerini Azure Data Lake Storage 2. 'e geçirin. Bu platform, uygulama kapalı kalma süresini ortadan kaldırır, veri kaybı olasılığını ortadan kaldırır ve işlemler şirket içinde devam ederken bile veri tutarlılığı güvence altına alır.  

> [!NOTE]
> Azure için WANdisco LiveData platform genel önizlemede. Bölgesel kullanılabilirlik için bkz. [Desteklenen bölgeler](https://docs.wandisco.com/live-data-platform/docs/prereq#supported-regions).

Platform iki hizmetten oluşur: [Azure Için LiveData Migrator](https://www.wandisco.com/products/livedata-migrator-for-azure) , şirket içi ortamlardan Azure depolama 'ya etkin şekilde kullanılan verileri geçirmek ve tüm değiştirilen verilerin veya veri alma verilerinin tutarlı bir şekilde çoğaltılmasını sağlayan [Azure Için LiveData düzlemi](https://www.wandisco.com/products/livedata-plane-for-azure) . 

> [!div class="mx-imgBorder"]
> ![Canlı veri platformuna genel bakış çizimi](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

Her iki hizmeti de Azure portal ve Azure CLı 'yi kullanarak yönetebilir ve her ikisi de aynı tarifeli, Kullandıkça Öde faturalandırma modelini diğer tüm Azure hizmetleri olarak takip edebilir. Azure için LiveData platform tüketimi aynı aylık Azure faturanızda görünür ve kullanımınızı izlemek ve izlemek için tutarlı ve uygun bir yol sağlar.

[Statik bilgileri Azure Data Box kopyalayarak](./data-lake-storage-migrate-on-premises-hdfs-cluster.md)veya [Distcp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html)gibi Hadoop araçlarını kullanarak verileri _çevrimdışına_ geçirmeden farklı olarak, Azure için WANdisco LiveData ile _çevrimiçi_ geçiş sırasında iş sistemlerinizin tam işlemlerini koruyabilirsiniz. Büyük veri ortamlarınızı, verilerini Azure 'a taşırken bile çalışmaya devam edin.

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>Azure için WANdisco LiveData platformunun temel özellikleri

[Azure Için WANdisco LiveData platformu](https://docs.wandisco.com/live-data-platform/docs/landing/) , veri tutarlılığı elde etmek ve veriler çoğaltma altındaki verileri değiştirmeye devam edebildiği sırada veri çoğaltma yapmak için benzersiz, geniş ölçekli bir ağ uyumlu uyumluluk altyapısı kullanır. <br><br>

>[!VIDEO https://www.youtube.com/embed/KRrmcYPxEho] 

Platformun temel özellikleri şunları içerir:

- **Veri tutarlılığı**: büyük veri birimlerini ortamlar arasında geçirme ve bu verileri sürekli değişiklik altında olsa bile depolama sistemleri aktarım hızı geçişi arasında tutarlı tutma sorunlarını çözün. Veri tutarlılığı elde etmek ve veri değişiklikleri boyunca tutarlılık garantilerine sahip verileri geçirmek için WANdisco 'in benzersiz ve geniş alan ağı yetenekli konsensus altyapısını doğrudan Azure 'da gerçekleştirin.

- **Işlemleri sürdürme**: uygulamalar geçiş sırasında verileri oluşturmaya, değiştirmeye, okumaya ve silmeye devam edebildiğinden, çok büyük verileri Azure 'a geçirmek için iş işlemlerini kesintiye uğratan veya bir kesinti penceresi tanıtılmalıdır. Uygulamalar, analiz altyapısı, alma işleri ve diğer işlemleri çalıştırmaya devam edin.

- **Sonuçları doğrula**: Azure 'a geçiş yapıldıktan sonra verilerinizin etkili bir şekilde kullanılabileceğini belirten uçtan uca doğrulama, üretim uygulaması iş yüklerini bunlara karşı çalıştırmanızı gerektirir. Yalnızca bir LiveData hizmeti, geçiş işleminizin kaynağı veya hedefi üzerinde değişiklik yapılıp olmamasından bağımsız olarak veri tutarlılığının korunmasından bağımsız olarak, veri tutarlılığı sağlamak zorunda kalmadan bunu sağlar. İşlem ve sistemlerinizde risk olmadan uygulama davranışını test edin ve doğrulayın.

- **Karmaşıklığı azaltın**: otomasyon ile verileri geçirerek verileri kopyalamak için zamanlanmış işler oluşturma ve yönetme ihtiyacını ortadan kaldırın. Seçmeli veri çoğaltma, Hive meta verileri, veri güvenliği ve gizlilik dahil geçiş ilerlemesini yönetmek ve izlemek için Azure ile derin tümleştirmeyi bir denetim düzlemi olarak kullanın.

- **Verimlilik**: yüksek aktarım hızı ve performansı koruyun ve büyük veri birimlerine kolayca ölçeklendirin. Bant genişliği tüketiminin denetimiyle, diğer sistem işlemlerini etkilemeden geçiş hedeflerinizi karşılamanıza da emin olabilirsiniz.

## <a name="migrate-big-data-faster-without-risk"></a>Büyük verileri riske girmeden daha hızlı geçirme

Azure için WANdisco LiveData platformunun ilk hizmeti, [Azure Için LiveData Migrator](https://www.wandisco.com/products/livedata-migrator-for-azure)' dir; Şirket içi ortamlardan Azure depolama 'ya etkin şekilde kullanılan verileri geçirmeye yönelik bir çözüm. Azure için LiveData Migrator, tamamen Azure portal veya Azure CLı 'dan sağlanır ve yönetilir ve verileri hemen geçirmeye başlamak için herhangi bir yapılandırma değişikliği, uygulama değişikliği veya hizmet yeniden başlatmaları olmadan şirket içi Hadoop kümenizin yanı sıra çalışır.

> [!div class="mx-imgBorder"]
> ![Azure mimarisi için LiveData Migrator](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture.png)

Büyük veri geçişleri karmaşık ve zor olabilir. İş işlemlerini kesintiye uğratmadan bilgilerin petabaytlarca taşınması, çevrimdışı veri kopyalama teknolojileriyle elde etmek imkansız. [Azure Için LiveData Migrator](https://www.wandisco.com/products/livedata-migrator-for-azure) , basit dağıtım sağlar ve uygulamalar geçirilmekte olan verileri okurken, yazarken ve değiştirirken sürekli veri geçişi ve çoğaltma Ile bir LiveData hizmeti kurabilir.

Geçiş gerçekleştirmek şu üç adım kadar basittir:

1. Azure portal LiveData Migrator örneğini şirket içi Hadoop kümenize sağlayın. Küme değişikliği veya kapalı kalma süresi gerekli değildir ve uygulamalar çalışmaya devam edebilir.

   > [!div class="mx-imgBorder"]
   >![LiveData Migrator örneği oluşturma](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

2. Hedef Azure Data Lake Storage 2. etkinleştirilmiş depolama hesabını tanımlayın.

   > [!div class="mx-imgBorder"]
   >![LiveData Migrator hedefi oluşturma](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

3. Geçirmek istediğiniz verilerin konumunu tanımlayın, örneğin: `/user/hive/warehouse` , ve geçişi Başlat.

   > [!div class="mx-imgBorder"]
   > ![LiveData Migrator geçişi oluşturma](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

Azure CLı ve Azure portal dahil olmak üzere standart Azure araçları aracılığıyla geçiş ilerlemenizi izleyin ve şirket içi ortamınızı birlikte kullanmaya devam edin. Başlamadan önce, bu [önkoşulları](https://docs.wandisco.com/live-data-platform/docs/prereq/)gözden geçirin.

## <a name="replicate-data-under-active-change"></a>Etkin değişiklik altında verileri Çoğalt

Şirket içi veri Lakes 'in Azure 'da büyük ölçekli geçişlerde uygulama testi ve doğrulama gerekir. Bu işlemi, kolayca mutabık kılınamayacak birden fazla gerçeği kaynağı oluşturacak şekilde, riski ortadan kaldırmak ve Azure 'a geçme maliyetini en aza indirmek için kritik öneme sahip olmadan yapabilirsiniz. [Azure Için LiveData düzlemi](https://www.wandisco.com/products/livedata-plane-for-azure) , bu kaygıları aşmak için WANdisco 'in koordinasyon altyapısı teknolojisini kullanır.

> [!div class="mx-imgBorder"]
> ![Azure mimarisi için LiveData düzlemi](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

İlk geçişten sonra verilerinizi Azure için LiveData düzlemi ile şirket içi Hadoop kümelerinde ve Azure depolama alanı genelinde tutarlı tutun:

1. Azure portal başlayarak şirket içi ve Azure 'da Azure için LiveData düzlemi sağlayın. Uygulama değişikliği gerekli değildir.

2. Tutarlı tutmak istediğiniz veri konumlarını kapsayan çoğaltma kurallarını yapılandırın, örneğin: `/user/contoso/sales/region/WA` .

3. Her iki konumdaki verileri, gerek duyduğunuz bir Hadoop uyumlu dosya sistemi olarak erişen ve değiştiren uygulamalar çalıştırın.

Azure için LiveData düzlemi, küme işleminin veya uygulama performansının önemli bir yükünü ortadan kaldırmadan verilerinizin tutarlılığını korur. Tüm değişiklikler tutarlı bir şekilde çoğaltılırsa verileri değiştirin veya alın.

## <a name="next-steps"></a>Sonraki adımlar

- Azure için [Azure Için LiveData platformu](https://docs.wandisco.com/live-data-platform/docs/landing/) , diğer tüm Azure kaynakları gibi kullanılır ve şimdi önizlemede kullanıma sunulmuştur. 

- Azure için LiveData Migrator sayesinde [önkoşulları](https://docs.wandisco.com/live-data-platform/docs/prereq/)anlayın, geçişinizi planlayın ve büyük ölçekli bir geçişi hemen doldurun.

- Migrator, bir şirket içi Hadoop [kümesine, bu şekilde,](https://docs.wandisco.com/live-data-platform/docs/create-sandbox-intro/)

## <a name="see-also"></a>Ayrıca bkz.

- [Azure Market 'te Azure için LiveData Migrator](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=Overview)

- [Azure Market 'te Azure için LiveData düzlemi](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=Overview)

- [Azure planları ve fiyatlandırma için LiveData Migrator](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=PlansAndPrice)

- [Azure planları ve fiyatlandırma için LiveData düzlemi](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=PlansAndPrice) 

- [Azure hakkında sık sorulan sorular için LiveData platformu](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [Azure için LiveData platformu ile ilgili bilinen sorunlar](https://docs.wandisco.com/live-data-platform/docs/known-issues/)

- [Azure Data Lake Storage 2. Nesil'e giriş](data-lake-storage-introduction.md)