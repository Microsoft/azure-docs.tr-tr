---
title: Veritabanı güvenliği-Azure Cosmos DB
description: Azure Cosmos DB, verilerinize yönelik veritabanı koruması ve veri güvenliği sağlar.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: mjbrown
ms.openlocfilehash: 19b4c8466e88159839ce1f43a5ba282b1bb3ec9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94636937"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Azure Cosmos DB’de güvenlik - genel bakış
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Bu makalede veritabanı ihlallerini önlemenize, algılamanıza ve yanıtlamanıza yardımcı olmak üzere Azure Cosmos DB tarafından sunulan veritabanı güvenliği en iyi yöntemleri ve önemli özellikleri açıklanır.

## <a name="whats-new-in-azure-cosmos-db-security"></a>Azure Cosmos DB güvenliği yenilikleri

Bekleyen şifreleme artık tüm Azure bölgelerinde Azure Cosmos DB depolanan belgeler ve yedeklemeler için kullanılabilir. Bekleyen şifreleme, bu bölgelerdeki hem yeni hem de mevcut müşteriler için otomatik olarak uygulanır. Herhangi bir şey yapılandırmaya gerek yoktur; ve bekleyen şifreleme sayesinde verilerinizin güvende ve güvenli olduğunu bilmenin avantajlarından faydalanmadan önceki gibi harika gecikme süresi, aktarım hızı, kullanılabilirlik ve işlevleri de elde edersiniz.

## <a name="how-do-i-secure-my-database"></a>Veritabanım güvenli Nasıl yaparım?

Veri güvenliği, siz, müşteri ve veritabanı sağlayıcınız arasında paylaşılan bir sorumluluktur. Seçtiğiniz veritabanı sağlayıcısına bağlı olarak, gerçekleştirdiğiniz sorumluluk miktarı farklılık gösterebilir. Şirket içi bir çözüm seçerseniz, uç nokta korumasından her şeyi donanımınızın fiziksel güvenliğine (kolay bir görev olmadan) sağlamanız gerekir. Azure Cosmos DB gibi bir PaaS bulut veritabanı sağlayıcısı seçerseniz, sorun alanı önemli ölçüde küçülür. Microsoft 'un [bulut bilgi işlem teknik incelemesi Için paylaşılan sorumluluklarından](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91) ödünç alınan aşağıdaki görüntüde, sorumluluğun Azure Cosmos DB gibi bir PaaS sağlayıcısıyla nasıl azaldığını gösterir.

:::image type="content" source="./media/database-security/nosql-database-security-responsibilities.png" alt-text="Müşteri ve veritabanı sağlayıcısı sorumlulukları":::

Önceki diyagramda, üst düzey bulut güvenlik bileşenleri gösteriliyor, ancak veritabanı çözümünüz için özel olarak hangi öğeleri merak etmeniz gerekiyor? Çözümleri birbirleriyle nasıl karşılaştırabileceğiniz.

Veritabanı sistemlerini karşılaştırmak için aşağıdaki gereksinimlerin denetim listesini öneririz:

- Ağ güvenliği ve güvenlik duvarı ayarları
- Kullanıcı kimlik doğrulaması ve hassas Kullanıcı denetimleri
- Bölgesel hatalarda verileri küresel olarak çoğaltabilme
- Bir veri merkezinden diğerine yük devretme olanağı
- Veri merkezi içinde yerel veri çoğaltma
- Otomatik veri yedeklemeleri
- Yedeklerden silinen verilerin geri yüklenmesi
- Hassas verileri koruyun ve yalıtın
- Saldırıları izleme
- Saldırılara yanıt verme
- Veri idare kısıtlamalarına uyacak şekilde coğrafi çit verileri
- Korunan veri merkezlerindeki sunucuların fiziksel koruması
- Sertifikalar

Ayrıca, [çok sayıda büyük ölçekli veritabanı ihlal](https://thehackernews.com/2017/01/mongodb-database-security.html) etmekle birlikte, aşağıdaki gereksinimlerin basit ancak kritik önem derecesini hatırlatır:

- Güncel tutulan ve düzeltme eki uygulanan sunucular
- Varsayılan/TLS şifrelemesi ile HTTPS
- Güçlü parolalara sahip yönetim hesapları

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Veritabanımın güvenliğini nasıl Azure Cosmos DB?

Önceki listeye geri bakalım: Bu güvenlik gereksinimlerinden kaç tane Azure Cosmos DB sağlar? Her tek bir.

Her birine ayrıntılı bir şekilde bakalım.

|Güvenlik gereksinimi|Azure Cosmos DB güvenlik yaklaşımı|
|---|---|
|Ağ güvenliği|IP güvenlik duvarı kullanmak, veritabanınızı güvenli hale getirmeye yönelik ilk koruma katmanıdır. Azure Cosmos DB, gelen güvenlik duvarı desteği için ilke temelli IP tabanlı erişim denetimlerini destekler. IP tabanlı erişim denetimleri geleneksel veritabanı sistemleri tarafından kullanılan güvenlik duvarı kurallarına benzerdir, ancak Azure Cosmos veritabanı hesabına yalnızca onaylanan bir makine veya bulut hizmeti tarafından erişilebilmeleri için genişletilir. [Azure Cosmos DB güvenlik duvarı destek](how-to-configure-firewall.md) makalesinde daha fazla bilgi edinin.<br><br>Azure Cosmos DB belirli bir IP adresini (168.61.48.0), IP aralığını (168.61.48.0/8) ve IP ve Aralık birleşimlerini etkinleştirmenizi sağlar. <br><br>İzin verilen bu liste dışındaki makinelerden kaynaklanan tüm istekler Azure Cosmos DB tarafından engellenir. Onaylanan makinelerden ve bulut hizmetlerinden gelen istekler, kaynaklara erişim denetimi verilecek şekilde kimlik doğrulama işlemini tamamlamalıdır.<br><br> Ağ yalıtımı elde etmek ve Azure Cosmos DB kaynaklarınızı genel Internet 'ten korumak için [sanal ağ hizmeti etiketlerini](../virtual-network/service-tags-overview.md) kullanabilirsiniz. Güvenlik kuralları oluştururken belirli IP adreslerinin yerine hizmet etiketleri kullanın. Bir kuralın uygun kaynak veya hedef alanındaki hizmet etiketi adını (örneğin, Azuversmosdb) belirterek, karşılık gelen hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz.|
|Yetkilendirme|Azure Cosmos DB yetkilendirme için karma tabanlı ileti kimlik doğrulama kodu (HMAC) kullanır. <br><br>Her istek, gizli hesap anahtarı kullanılarak karma hale getirilir ve sonraki temel 64 kodlu karma, Azure Cosmos DB her çağrısıyla birlikte gönderilir. İsteği doğrulamak için Azure Cosmos DB hizmeti, karma oluşturmak için doğru gizli anahtar ve özellikleri kullanır, ardından değeri istekteki bir değerle karşılaştırır. İki değer eşleşiyorsa, işlem başarıyla yetkilendirilir ve istek işlendiğinde, aksi takdirde bir yetkilendirme hatası olur ve istek reddedilir.<br><br>Bir [birincil anahtar](#primary-keys)veya bir [kaynak belirteci](secure-access-to-data.md#resource-tokens) kullanarak, bir belge gibi bir kaynağa ayrıntılı erişime izin verebilirsiniz.<br><br>[Azure Cosmos DB kaynaklarına erişimi güvenli hale getirme](secure-access-to-data.md)hakkında daha fazla bilgi edinin.|
|Kullanıcılar ve izinler|Hesap için birincil anahtarı kullanarak, veritabanı başına kullanıcı kaynakları ve izin kaynakları oluşturabilirsiniz. Kaynak belirteci, veritabanındaki bir izinle ilişkilendirilir ve kullanıcının veritabanında bir uygulama kaynağına erişimi olup olmadığını (salt okuma yazma, salt okuma veya erişim yok) belirler. Uygulama kaynakları kapsayıcı, belgeler, ekler, saklı yordamlar, Tetikleyiciler ve UDF 'ler içerir. Kaynak belirteci daha sonra kaynak erişimi sağlamak veya reddetmek için kimlik doğrulama sırasında kullanılır.<br><br>[Azure Cosmos DB kaynaklarına erişimi güvenli hale getirme](secure-access-to-data.md)hakkında daha fazla bilgi edinin.|
|Active Directory Tümleştirmesi (Azure RBAC)| Ayrıca, Cosmos hesabı, veritabanı, kapsayıcı ve Azure portal erişim denetimi (ıAM) kullanarak (aktarım hızı) erişimi de sağlayabilirsiniz veya kısıtlayabilirsiniz. IAM, rol tabanlı erişim denetimi sağlar ve Active Directory ile tümleşir. Yerleşik roller veya bireyler ve gruplar için özel roller kullanabilirsiniz. Daha fazla bilgi için bkz. [Active Directory tümleştirme](role-based-access-control.md) makalesi.|
|Genel çoğaltma|Azure Cosmos DB, bir düğmeye tıklayarak verilerinizi Azure 'un Dünya çapındaki veri merkezlerinden herhangi birine çoğaltmanızı sağlayan, anahtar temelli genel dağıtım sunar. Küresel çoğaltma, genel olarak ölçeklendirmenize ve dünyanın dört bir yanındaki verilerinize düşük gecikmeli erişim sağlamanıza olanak tanır.<br><br>Güvenlik bağlamında, genel çoğaltma, bölgesel hatalara karşı veri koruma sağlar.<br><br>[Verileri küresel olarak dağıtma](distribute-data-globally.md) bölümünde daha fazlasını öğrenin.|
|Bölgesel yük devretme|Verilerinizi birden fazla veri merkezinde çoğaltdıysanız, bölgesel veri merkezi 'nin çevrimdışı olması için Azure Cosmos DB otomatik olarak işlemlerinizi kaydeder. Verilerinizin çoğaltılacağı bölgeleri kullanarak, yük devretme bölgelerinin öncelikli bir listesini oluşturabilirsiniz. <br><br>[Azure Cosmos DB bölgesel](high-availability.md)yük devretmeler hakkında daha fazla bilgi edinin.|
|Yerel çoğaltma|Tek bir veri merkezi içinde bile, Azure Cosmos DB [tutarlılık düzeyi](consistency-levels.md)seçimi sunarak yüksek kullanılabilirliğe yönelik verileri otomatik olarak çoğaltır. Bu çoğaltma, tüm tek bölge hesapları için% 99,99 [kullanılabilirlik SLA 'sını](https://azure.microsoft.com/support/legal/sla/cosmos-db) ve gevşek tutarlılığa sahip tüm çok bölgeli hesapları garanti eder ve tüm çok bölgeli veritabanı hesaplarında% 99,999 okuma kullanılabilirliği sağlar.|
|Otomatik çevrimiçi yedeklemeler|Azure Cosmos veritabanları düzenli olarak yedeklenir ve coğrafi olarak yedekli bir depoda depolanır. <br><br>[Azure Cosmos DB Ile otomatik çevrimiçi yedekleme ve geri yükleme](online-backup-and-restore.md)hakkında daha fazla bilgi edinin.|
|Silinen verileri geri yükleme|Otomatik çevrimiçi yedeklemeler, yanlışlıkla en fazla ~ 30 gün sonra silinmiş olabilecek verileri kurtarmak için kullanılabilir. <br><br>[Azure Cosmos DB Ile otomatik çevrimiçi yedekleme ve geri yükleme](online-backup-and-restore.md) hakkında daha fazla bilgi edinin|
|Hassas verileri koruyun ve yalıtın|Bölgelerdeki tüm veriler, yenilikler bölümünde listelendi. Artık Rest 'de şifrelenir.<br><br>Kişisel veriler ve diğer gizli veriler belirli bir kapsayıcıya yalıtılabilir ve okuma/yazma veya salt okuma erişimi belirli kullanıcılarla sınırlı olabilir.|
|Saldırıları izle|[Denetim günlüğü ve etkinlik günlüklerini](./monitor-cosmos-db.md)kullanarak, hesabınızı normal ve anormal etkinlikler için izleyebilirsiniz. Kaynaklarınız üzerinde gerçekleştirilen işlemleri, işlemi kimin yaptığını, işlem ne zaman oluştuğunu, işlemin durumunu ve bu tablonun altındaki ekran görüntüsünde gösterilen çok daha fazlasını görüntüleyebilirsiniz.|
|Saldırılara yanıt verme|Olası bir saldırı bildirmek üzere Azure Destek ile iletişim kurduktan sonra, 5 adımlı bir olay yanıtı işlemi kapatılır. 5 adımlı işlemin amacı, bir sorun saptandıktan ve bir araştırma başladıktan sonra normal hizmet güvenliğini ve işlemlerini mümkün olduğunca hızlı bir şekilde geri yüklemektir.<br><br>[Bulutta Microsoft Azure Güvenlik yanıtı](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)hakkında daha fazla bilgi edinin.|
|Coğrafi sınırlama|Azure Cosmos DB, evereign bölgeleri için veri yönetimini sağlar (örneğin, Almanya, Çin, US Gov).|
|Korumalı tesisler|Azure Cosmos DB veriler, SSD 'lerde Azure 'un korunan veri merkezlerinde depolanır.<br><br>[Microsoft Global veri merkezlerinde](https://www.microsoft.com/en-us/cloud-platform/global-datacenters) daha fazla bilgi edinin|
|HTTPS/SSL/TLS şifrelemesi|Azure Cosmos DB tüm bağlantıları HTTPS 'yi destekler. Azure Cosmos DB ayrıca TLS 1,2 ' i destekler.<br>En düşük TLS sürümü sunucu tarafını zorlamak mümkündür. Bunu yapmak için lütfen iletişim kurun [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com) .|
|Bekleme sırasında şifreleme|Azure Cosmos DB depolanan tüm veriler, bekleyen bir şekilde şifrelenir. [Rest 'te Azure Cosmos DB şifreleme](./database-encryption-at-rest.md) hakkında daha fazla bilgi edinin|
|Düzeltme eki uygulanan sunucular|Yönetilen bir veritabanı olarak Azure Cosmos DB, sizin için otomatik olarak gerçekleştirilen sunucuları yönetme ve düzeltme eki uygulama ihtiyacını ortadan kaldırır.|
|Güçlü parolalara sahip yönetim hesapları|Bu gereksinimimizden de bahseder ve bazı Rakiplerden farklı olarak, Azure Cosmos DB hiçbir parola olmadan bir yönetim hesabı olması olanaksızdır.<br><br> TLS ve HMAC gizli anahtarı tabanlı kimlik doğrulaması ile güvenlik, varsayılan olarak ' de kullanıma hazır.|
|Güvenlik ve veri koruma sertifikaları| Sertifikaların en güncel listesi için genel [Azure uyumluluk sitesinin](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) yanı sıra tüm sertifikalarla en son [Azure uyumluluk belgesine](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) (Cosmos arama) bakın. Daha fazla odaklanmış bir okuma için, 25 Nisan 2018 sonrası [Azure #CosmosDB: SOCS 1/2 tür 2, HITRUST, PCI DSS Level 1, ISO 27001, HIPAA, Fedratin ve diğer birçok kişi içeren güvenli, özel, uyumlu bir şekilde kullanıma alın.

Aşağıdaki ekran görüntüsünde, hesabınızı izlemek için denetim günlüğü ve etkinlik günlüklerini nasıl kullanabileceğiniz gösterilmektedir: :::image type="content" source="./media/database-security/nosql-database-security-application-logging.png" alt-text="Azure Cosmos DB Için etkinlik günlükleri":::

<a id="primary-keys"></a>

## <a name="primary-keys"></a>Birincil anahtarlar

Birincil anahtarlar, veritabanı hesabının tüm yönetim kaynaklarına erişim sağlar. Birincil anahtarlar:

- Hesaplara, veritabanlarına, kullanıcılara ve izinlere erişim sağlayın. 
- Kapsayıcılara ve belgelere parçalı erişim sağlamak için kullanılamaz.
- , Bir hesabın oluşturulması sırasında oluşturulur.
- Herhangi bir zamanda yeniden oluşturulabilir.

Her hesap iki birincil anahtardan oluşur: birincil anahtar ve ikincil anahtar. Çift anahtarların amacı, hesap ve verilerinize sürekli erişim sağlamak için, anahtarları yeniden oluşturmak veya almak için kullanılır.

Cosmos DB hesabının iki birincil anahtarına ek olarak, iki salt okunurdur. Salt okunurdur bu anahtarlar yalnızca hesapta okuma işlemlerine izin verir. Salt okuma anahtarları okuma izni kaynaklarına erişim sağlamaz.

Birincil, ikincil, salt okuma ve okuma-yazma birincil anahtarları Azure portal kullanılarak alınabilir ve yeniden oluşturulabilir. Yönergeler için bkz. [erişim anahtarlarını görüntüleme, kopyalama ve yeniden](manage-with-cli.md#regenerate-account-key)oluşturma.

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Azure portal-gösteren NoSQL veritabanı güvenliğine erişim denetimi (ıAM)":::

## <a name="next-steps"></a>Sonraki adımlar

Birincil anahtarlar ve kaynak belirteçleri hakkında daha fazla bilgi için bkz. [Azure Cosmos DB verilerine erişimi güvenli hale getirme](secure-access-to-data.md).

Denetim günlüğü hakkında daha fazla bilgi için bkz. [Azure Cosmos DB tanılama günlüğü](./monitor-cosmos-db.md).

Microsoft sertifikaları hakkında daha fazla bilgi için bkz. [Azure Güven Merkezi](https://azure.microsoft.com/support/trust-center/).