---
title: MariaDB için Azure veritabanı için doğru dağıtım türünü seçme
description: Bu makalede, MariaDB için Azure veritabanı 'nı hizmet olarak altyapı (IaaS) veya hizmet olarak platform (PaaS) olarak dağıtmadan önce göz önünde bulundurmanız gereken faktörler açıklanmaktadır.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: 59064c71d19f81a5edc322bc4c8158773a104b5d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151992"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>Azure 'da doğru MariaDB sunucu seçeneğini belirleyin

Azure ile, MariaDB sunucu iş yükleriniz barındırılan bir sanal makine altyapısı (IaaS) veya hizmet olarak barındırılan bir platform (PaaS) olarak çalışabilir. PaaS 'in birden çok dağıtım seçeneği vardır ve her bir dağıtım seçeneğinde hizmet katmanları vardır. IaaS ve PaaS arasında seçim yaptığınızda, veritabanınızı yönetmek, yama uygulamak ve yedeklemeler yapmak istediğinize ya da bu işlemleri Azure 'a atamak istediğinize karar vermelisiniz.

Kararlarınızı yaparken aşağıdaki iki seçeneği göz önünde bulundurun:

- **MariaDB Için Azure veritabanı:** Bu seçenek, MariaDB Community Edition 'ın kararlı sürümünü temel alan tam olarak yönetilen bir MariaDB veritabanı altyapısıdır. Azure bulut platformunda barındırılan, hizmet olarak bu ilişkisel veritabanı (DBaaS) PaaS sektör kategorisine denk gelir.

  Azure üzerinde MariaDB 'nin yönetilen bir örneğiyle, MariaDB sunucusu şirket içinde ya da bir Azure VM 'de olduğunda kapsamlı yapılandırma gerektiren yerleşik özellikleri kullanabilirsiniz.

  MariaDB 'yi bir hizmet olarak kullanırken, kesintiye uğramaması durumunda daha fazla denetim için ölçeği büyütme veya genişletme seçeneklerine giderek ödeyin. Ve tek başına MariaDB sunucusunun aksine, MariaDB için Azure veritabanı yerleşik yüksek kullanılabilirlik, zeka ve yönetim gibi ek özelliklere sahiptir.

- **Azure VM 'Lerinde MariaDB:** Bu seçenek IaaS sektör kategorisine denk gelir. Bu hizmetle, MariaDB sunucusunu Azure bulut platformunda tam olarak yönetilen bir sanal makine içinde çalıştırabilirsiniz. MariaDB 'nin tüm son sürümleri, IaaS sanal makinesine yüklenebilir.

  MariaDB için Azure veritabanı 'nın en önemli farkından, Azure VM 'lerinde MariaDB, veritabanı altyapısı üzerinde denetim sağlar. Ancak, bu denetim, VM 'Leri ve birçok veritabanı yönetimi (DBA) görevini yönetme sorumluluğunda gelir. Bu görevler veritabanı sunucularını, veritabanı kurtarmayı ve yüksek kullanılabilirlik tasarımını koruma ve düzeltme eki uygulama içerir.

Bu seçenekler arasındaki temel farklılıklar aşağıdaki tabloda listelenmiştir:

|            | MariaDB için Azure Veritabanı | Azure VM 'lerinde MariaDB    |
|:-------------------|:-----------------------------|:--------------------|
| Hizmet düzeyi sözleşmesi (SLA)                | % 99,99 kullanılabilirlik için SLA sunar| Aynı Kullanılabilirlik kümesinde iki veya daha fazla örnek ile en fazla% 99,95 kullanılabilirlik vardır.<br/><br/>Premium depolama kullanan tek örnekli bir VM ile% 99,9 kullanılabilirlik.<br/><br/>birden çok kullanılabilirlik kümesindeki birden çok örneğe sahip Kullanılabilirlik Alanları kullanarak% 99,99.<br/><br/>Bkz. [sanal makineler SLA 'sı](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). |
| İşletim sistemi düzeltme eki uygulama        | Otomatik  | Müşteriler tarafından yönetiliyor |
| MariaDB düzeltme eki uygulama     | Otomatik  | Müşteriler tarafından yönetiliyor |
| Yüksek kullanılabilirlik | Yüksek kullanılabilirlik (HA) modeli, düğüm düzeyinde bir kesinti oluştuğunda için yerleşik yük devretme mekanizmalarına dayanır. Bu gibi durumlarda, hizmet otomatik olarak yeni bir örnek oluşturur ve depolama alanını bu örneğe ekler. | Müşteriler yüksek kullanılabilirliği mimarileri, uygular, test edin ve bakımını yapın. Yetenekler her zaman açık yük devretme kümelemesi, her zaman açık grup çoğaltması, günlük aktarma veya işlemsel çoğaltma içerebilir.|
| Bölge artıklığı | Şu anda desteklenmiyor | Azure VM 'Leri, farklı kullanılabilirlik bölgelerinde çalışacak şekilde ayarlanabilir. Şirket içi bir çözüm için müşterilerin kendi ikincil veri merkezini oluşturması, yönetmesi ve sürdürmeleri gerekir.|
| Karma senaryolar | [Gelen verileri çoğaltma](https://docs.microsoft.com/azure/MariaDB/concepts-data-in-replication)ile, bir dış MariaDB sunucusundan verileri MariaDB hizmeti Için Azure veritabanı 'na aktarabilirsiniz. Dış sunucu şirket içinde, sanal makinelerde veya diğer bulut sağlayıcıları tarafından barındırılan bir veritabanı hizmeti olabilir.<br/><br/> [Çoğaltma oku](https://docs.microsoft.com/azure/mariadb/concepts-read-replicas) özelliği sayesinde, MariaDB ana sunucusu Için Azure veritabanı 'ndan en fazla beş salt okuma Çoğaltma sunucusuna veri çoğaltabilirsiniz. Çoğaltmalar aynı Azure bölgesinde ya da bölgeler arasında bulunur. Salt okuma çoğaltmaları, binlog çoğaltma teknolojisi kullanılarak zaman uyumsuz olarak güncelleştirilir.<br/><br/>Bölgeler arası okuma çoğaltması Şu anda genel önizlemededir.| Müşteriler tarafından yönetiliyor
| Yedekleme ve geri yükleme | Otomatik olarak [Sunucu yedeklemeleri](https://docs.microsoft.com/azure/MariaDB/concepts-backup#backups) oluşturur ve bunları yerel olarak yedekli veya coğrafi olarak yedekli olan kullanıcı tarafından yapılandırılmış depolamada depolar. Hizmet tam, fark ve işlem günlüğü yedeklemeleri alır | Müşteriler tarafından yönetiliyor |
| Veritabanı işlemlerini izleme | Müşterilerine, veritabanı işleminde [Uyarı ayarlama](https://docs.microsoft.com/azure/MariaDB/concepts-monitoring) ve eşiklere ulaşma olanağı sunan bir işlev sağlar. | Müşteriler tarafından yönetiliyor |
| Gelişmiş Tehdit Koruması | [Gelişmiş tehdit koruması](https://docs.microsoft.com/azure/MariaDB/howto-database-threat-protection-portal)sağlar. Bu koruma, veritabanlarına erişmek veya veritabanına yararlanmak için olağan dışı ve zararlı olabilecek girişimleri gösteren anormal etkinlikleri algılar.<br/><br/>Gelişmiş tehdit koruması Şu anda genel önizleme aşamasındadır.| Müşteriler bu korumanın kendileri için derlenmelidir.
| Olağanüstü durum kurtarma | Otomatik yedeklemeleri Kullanıcı tarafından yapılandırılmış [yerel olarak yedekli veya coğrafi olarak yedekli depolamada](https://docs.microsoft.com/azure/MariaDB/howto-restore-server-portal)depolar. Yedeklemeler Ayrıca bir sunucuyu zaman noktasına geri yükleyebilir. Bekletme süresi 7 ile 35 gün arasında bir süredir. Geri yükleme Azure portal kullanılarak gerçekleştirilir. | Müşteriler tarafından tam olarak yönetilir. Sorumluluklar, zamanlama, test etme, arşivleme, depolama ve bekletme ile sınırlı değildir. Diğer bir seçenek de Azure kurtarma hizmetleri kasasını VM 'lerde Azure VM 'Leri ve veritabanlarını yedeklemek için kullanmaktır. Bu seçenek önizlemededir. |
| Performans önerileri | Müşterilere, sistem tarafından oluşturulan kullanım günlüğü dosyalarını temel alan [performans önerileri](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) sağlar. Öneriler, iş yüklerini iyileştirmenize yardımcı olur.<br/><br/>Performans önerileri şu anda genel önizlemededir. | Müşteriler tarafından yönetiliyor |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>PaaS veya IaaS seçimi için iş kesintileri

MariaDB veritabanlarınızı barındırmak için PaaS veya IaaS seçme kararlarınızı etkileyebilecek birkaç etken vardır.

### <a name="cost"></a>Maliyet

Sınırlı komik genellikle veritabanlarınızı barındırmak için en iyi çözümü belirleyen birincil noktadır. Bu, sıkı bütçe kısıtlamaları altında çalışan kurulu bir şirkette çok az nakit veya ekip ile bir başlangıç yaptığınız için geçerlidir. Bu bölümde, Azure VM 'lerinde MariaDB ve MariaDB için Azure veritabanı 'na uygulanan Azure 'da faturalandırma ve lisanslama temelleri açıklanmaktadır.

#### <a name="billing"></a>Faturalandırma

MariaDB için Azure veritabanı şu anda birkaç katmanda kaynaklar için farklı fiyatlarla bir hizmet olarak sunulmaktadır. Tüm kaynaklar, saatlik olarak sabit bir fiyat üzerinden faturalandırılır. Şu anda desteklenen hizmet katmanları, işlem boyutları ve depolama miktarları hakkında en son bilgiler için bkz. [sanal çekirdek tabanlı satın alma modeli](https://docs.microsoft.com/azure/MariaDB/concepts-pricing-tiers). Hizmet katmanlarını ve işlem boyutlarını, uygulamanızın değişen aktarım hızı gereksinimleriyle eşleşecek şekilde dinamik olarak ayarlayabilirsiniz. Giden Internet trafiği için düzenli [veri aktarımı fiyatları](https://azure.microsoft.com/pricing/details/data-transfers/)üzerinden faturalandırılırsınız.

MariaDB için Azure veritabanı ile, Microsoft, veritabanı yazılımını otomatik olarak yapılandırır, yaekler ve yükseltir. Bu otomatik eylemler, yönetim maliyetlerinizi azaltır. Ayrıca, MariaDB için Azure veritabanı ['nda yerleşik yedekleme](https://docs.microsoft.com/azure/MariaDB/concepts-backup) özellikleri vardır. Bu yetenekler, özellikle çok sayıda veritabanınız olduğunda önemli maliyet tasarrufları elde etmenize yardımcı olur. Buna karşılık, Azure VM 'lerinde MariaDB ile herhangi bir MariaDB sürümü seçip çalıştırabilirsiniz. Kullandığınız MariaDB sürümü ne olduğuna bakılmaksızın, sağlanan VM için ve kullanılan belirli MariaDB lisans türünün maliyetleri için ödeme yaparsınız.

MariaDB için Azure veritabanı, hizmet için% 99,99 SLA garantisi sağlarken her türlü düğüm düzeyinde kesinti için yerleşik yüksek kullanılabilirlik sağlar. Ancak, VM 'Lerde veritabanı yüksek kullanılabilirliği için, müşteriler bir MariaDB veritabanında bulunan [MariaDB çoğaltması](https://mariadb.com/kb/en/library/setting-up-replication/) gibi yüksek kullanılabilirlik seçeneklerini kullanmalıdır. Desteklenen yüksek kullanılabilirlik seçeneğinin kullanılması ek bir SLA sağlamaz. Ancak bu, ek maliyet ve yönetim yüküyle% 99,99 ' den fazla veritabanı kullanılabilirliği elde etmenizi sağlar.

Fiyatlandırma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:
* [MariaDB için Azure veritabanı fiyatlandırması](https://azure.microsoft.com/pricing/details/MariaDB/)
* [Sanal makine fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Yönetim

Birçok işletme için, bir bulut hizmetine geçiş kararı, maliyetle ilgili olduğu gibi yönetim karmaşıklığını boşaltma konusunda çok önemlidir. IaaS ve PaaS ile Microsoft:

- Temel altyapıyı yönetir.
- Olağanüstü durum kurtarma sağlamak için tüm verileri otomatik olarak çoğaltır.
- Veritabanı yazılımını yapılandırır ve yükseltir.
- Yük dengelemeyi yönetir.
- Sunucu arızası durumunda saydam yük devreder.

Aşağıdaki listede her bir seçeneğe ilişkin yönetim konuları açıklanmaktadır:

* MariaDB için Azure veritabanı ile veritabanınızı yönetmeye devam edebilirsiniz. Ancak artık veritabanı altyapısını, işletim sistemini veya donanımı yönetmeniz gerekmez. Yönetmeye devam edebilmeniz için öğelerin örnekleri şunlardır:

  - Veritabanları
  - Oturum açma
  - Dizin ayarlama
  - Sorgu ayarlama
  - Denetim
  - Güvenlik

  Ayrıca, başka bir veri merkezine yüksek kullanılabilirliği yapılandırmak, yapılandırma veya yönetim gerektirmeyen en az düzeyde olmalıdır.

* Azure VM 'lerinde MariaDB ile, işletim sistemi ve MariaDB sunucu örneği yapılandırması üzerinde tam denetime sahip olursunuz. Bir VM ile, işletim sisteminin ve veritabanı yazılımının ne zaman güncelleşeceğine veya yükseltileceğine karar verirsiniz. Ayrıca, virüsten koruma uygulaması gibi ek yazılımları ne zaman yükleyeceğinize karar verirsiniz. Düzeltme eki uygulama, yedekleme ve yüksek kullanılabilirliği büyük ölçüde basitleştirmek için bazı otomatik özellikler sağlanır. VM boyutunu, disk sayısını ve bunların depolama yapılandırmalarının boyutunu kontrol edebilirsiniz. Daha fazla bilgi için bkz. [Azure Için sanal makine ve bulut hizmeti boyutları](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="time-to-move-to-azure"></a>Azure 'a geçiş süresi

* MariaDB için Azure veritabanı, geliştirici üretkenliği ve yeni çözümler için hızlı pazar süresi kritik olduğunda bulutta tasarlanan uygulamalar için doğru çözümdür. DBA gibi programlama işlevselliğiyle, hizmet, temel işletim sisteminin ve veritabanının yönetilmesi gereksinimini düşürür, bulut mimarları ve geliştiriciler için uygundur.

* Yeni şirket içi donanım alma zamandan ve masrafından kaçınmak istediğinizde, Azure VM 'lerinde MariaDB, MariaDB veritabanı gerektiren uygulamalar için doğru çözümdür veya Windows ya da Linux 'ta MariaDB özelliklerine erişim sağlar. Bu çözüm Ayrıca, MariaDB için Azure veritabanı kötü bir uyum olduğu durumlarda, mevcut şirket içi uygulamaları ve veritabanlarını Azure 'a dokunulmadan geçirmek için de uygundur.

  Sunu, uygulama ve veri katmanlarını değiştirmenize gerek olmadığından, mevcut çözümünüzü yeniden tasarlayarak zamandan ve bütçelere tasarruf edersiniz. Bunun yerine, tüm Çözümlerinizi Azure 'a geçirmeye ve Azure platformunun gerek duyduğu bazı performans iyileştirmelerini gidermeye odaklanırsınız.

## <a name="next-steps"></a>Sonraki adımlar

* Bkz. [MariaDB fiyatlandırması Için Azure veritabanı](https://azure.microsoft.com/pricing/details/MariaDB/).
* [İlk sunucunuzu oluşturarak](https://review.docs.microsoft.com/azure/MariaDB/quickstart-create-MariaDB-server-database-using-azure-portal) başlayın.
