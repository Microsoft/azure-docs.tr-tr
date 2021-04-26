---
title: Azure Cosmos DB içinde nokta geri yükleme özelliği ile sürekli yedekleme
description: Azure Cosmos DB zaman içinde geri yükleme özelliği, yanlışlıkla yazma, silme işleminden verileri kurtarmaya veya verileri herhangi bir bölgeye geri yüklemenize yardımcı olur. Fiyatlandırma ve geçerli sınırlamalar hakkında bilgi edinin.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: references_regions
ms.openlocfilehash: d1dc108ecec93dddeb768eb61af425ba67f23002
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393148"
---
# <a name="continuous-backup-with-point-in-time-restore-preview-feature-in-azure-cosmos-db"></a>Azure Cosmos DB içindeki noktadan noktaya geri yükleme (Önizleme) özelliği ile sürekli yedekleme
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB için zaman noktası geri yükleme özelliği (sürekli yedekleme modu) Şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB zaman içinde geri yükleme özelliği (Önizleme), aşağıdakiler gibi birden çok senaryoya yardımcı olur:

* Bir kapsayıcı içindeki yanlışlıkla yazma veya silme işleminden kurtarmak için.
* Silinen bir hesabı, veritabanını veya kapsayıcıyı geri yüklemek için.
* Geri yükleme noktasında herhangi bir bölgeye (yedeklemeler vardı) geri yüklemek için.

Azure Cosmos DB, ek sağlanmış üretilen iş (ru) kullanmadan veya veritabanınızın performansını ve kullanılabilirliğini etkilemeden arka planda veri yedekleme gerçekleştirir. Sürekli yedeklemeler, hesabın bulunduğu her bölgede alınır. Aşağıdaki görüntüde, Batı ABD 'de yazma bölgesi olan bir kapsayıcının, Doğu ve Doğu ABD 2 okuma bölgelerini ilgili bölgelerde uzak bir Azure Blob depolama hesabına nasıl yedeklenebileceği gösterilmektedir. Varsayılan olarak, her bölge yedeklemeyi yerel olarak yedekli depolama hesaplarında depolar. Bölgede [kullanılabilirlik alanları](high-availability.md#availability-zone-support) etkinse, yedekleme Zone-Redundant depolama hesaplarında depolanır.

:::image type="content" source="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" alt-text="Azure Blob depolama alanına veri yedeklemesini Azure Cosmos DB." lightbox="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" border="false":::

Geri yükleme için kullanılabilir zaman penceresi (Bekletme dönemi olarak da bilinir) şu iki değerin alt değeridir: *30 gün* sonra, bundan sonra veya *kaynak oluşturma zamanına kadar*. Geri yükleme için zaman içindeki nokta, bekletme dönemi içinde herhangi bir zaman damgası olabilir.

Genel önizlemede, SQL API 'SI veya MongoDB içerik noktası Azure Cosmos DB hesabını [Azure Portal](continuous-backup-restore-portal.md), [Azure komut satırı ARABIRIMI](continuous-backup-restore-command-line.md) (az CLI), [Azure PowerShell](continuous-backup-restore-powershell.md)veya [Azure Resource Manager](continuous-backup-restore-template.md)kullanarak başka bir hesaba geri yükleyebilirsiniz.

## <a name="what-is-restored"></a>Neler geri yüklenir?

Sürekli bir durumda, kaynak hesapta (veritabanları, kapsayıcılar ve öğeler dahil) gerçekleştirilen tüm kumalar, zaman uyumsuz olarak 100 saniye içinde yedeklenir. Yedekleme medyası (Azure Storage) devre dışı bırakılırsa veya kullanılamıyorsa, bu işlem, medya yeniden kullanılabilir olana kadar yerel olarak kalıcı hale getirilir ve geri yüklenebilecek işlemlerin Aslına göre kaybedilmesini engellemek için temizlenir.

Sağlanan kapsayıcı işleme birimlerinin herhangi bir bileşimini, paylaşılan veritabanı işleme birimini veya hesabın tamamını geri yüklemeyi seçebilirsiniz. Geri yükleme eylemi tüm verileri ve bunların dizin özelliklerini yeni bir hesaba geri yükler. Geri yükleme işlemi bir hesapta, veritabanında veya kapsayıcıda geri yüklenen tüm verilerin belirtilen geri yükleme süresince tutarlı olmasını garanti eder. Geri yükleme süresi, geri yüklenmesi gereken verilerin miktarına bağlıdır.

> [!NOTE]
> Sürekli yedekleme moduyla, yedeklemeler Azure Cosmos DB hesabınızın kullanılabildiği her bölgede alınır. Her bölge hesabı için alınan yedeklemeler, varsayılan olarak yerel olarak yedekli ve hesabınızın [kullanılabilirlik bölgesi](high-availability.md#availability-zone-support) özelliği bu bölge için etkinleştirilmişse, bölgesel olarak yedekli. Geri yükleme eylemi her zaman verileri yeni bir hesaba geri yükler.

## <a name="what-is-not-restored"></a>Neler geri yüklenmez?

Aşağıdaki konfigürasyonlar, zaman içinde, noktadan sonra kurtarma işleminden sonra geri yüklenmez:

* Güvenlik Duvarı, VNET, Özel uç nokta ayarları.
* Tutarlılık ayarları. Varsayılan olarak, hesap oturum tutarlılığı ile geri yüklenir.  
* Düzenleye.
* Saklı yordamlar, Tetikleyiciler, UDF 'ler.

Geri yükleme tamamlandıktan sonra bu konfigürasyonları geri yüklenen hesaba ekleyebilirsiniz.

## <a name="restore-scenarios"></a>Geri yükleme senaryoları

Aşağıda, zaman içinde belirli bir noktaya geri yükleme özelliği tarafından giderilen bazı önemli senaryolar verilmiştir. [A] aracılığıyla [c] senaryoları geri yükleme zaman damgası önceden biliniyorsa geri yüklemenin nasıl tetikleneceğini gösterir.
Ancak, yanlışlıkla silmenin veya bozulmanın tam süresini bildiğiniz senaryolar olabilir. Senaryolar [d] ve [e] geri yüklenebilen veritabanı veya kapsayıcılarındaki yeni olay akışı API 'Lerini kullanarak geri yükleme zaman damgasını _bulmayı_ gösterir.

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" alt-text="Geri yüklenebilen bir hesap için zaman damgalarına sahip ömür döngüsü olayları." lightbox="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" border="false":::

a. **Silinen hesabı geri yükle** -geri yüklediğiniz tüm silinen hesaplar **geri yükleme** bölmesinden görünür. Örneğin, *A hesabı* T3 zaman damgasında silinirse. Bu durumda, T3, konum, hedef hesap adı, kaynak grubu ve hedef hesap adından hemen önce gelen zaman damgası [Azure Portal](continuous-backup-restore-portal.md#restore-deleted-account), [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)veya [CLI](continuous-backup-restore-command-line.md#trigger-restore)'dan geri yüklemek için yeterlidir.  

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" alt-text="Geri yüklenebilen bir veritabanı ve kapsayıcı için zaman damgalarına sahip ömür döngüsü olayları." lightbox="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" border="false":::

b. **Belirli bir bölgedeki hesabın verilerini geri yükleme** -Örneğin, Iki bölgede *hesap a* *Doğu ABD* ve *Batı ABD* zaman damgası: T3. *Batı ABD* hesap a 'nın bir kopyasına ihtiyacınız varsa, hedef konum olarak Batı ABD ile [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)veya [CLI](continuous-backup-restore-command-line.md#trigger-restore) 'dan bir zaman noktası geri yüklemesi yapabilirsiniz.

c. **Bilinen bir geri yükleme zaman damgasıyla kapsayıcı içindeki yanlışlıkla yazma veya silme Işleminden kurtarma** yapın; Örneğin, *veritabanı 1* içindeki *kapsayıcı 1* ' in içeriğinin zaman damgası T3 ' de yanlışlıkla değiştirildiğini **biliyorsanız** . İstediğiniz kapsayıcı durumunu kurtarmak için [Azure Portal](continuous-backup-restore-portal.md#restore-live-account), [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)veya [CLI](continuous-backup-restore-command-line.md#trigger-restore) 'dan zaman damgası T3 ' den başka bir hesaba geri yükleme yapabilirsiniz.

d. **Veritabanını yanlışlıkla silmeden önce bir hesabı önceki bir noktaya geri yükleme** - [Azure Portal](continuous-backup-restore-portal.md#restore-live-account), bir veritabanının ne zaman silindiğini ve geri yükleme zamanını bulmak için olay akışı bölmesini kullanabilirsiniz. Benzer şekilde, [Azure CLI](continuous-backup-restore-command-line.md#trigger-restore) ve [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)ile veritabanı olayları akışını numaralandırarak veritabanı silme olayını bulabilir ve ardından restore komutunu gerekli parametrelerle tetikleyebilirsiniz.

e. **Kapsayıcı özelliklerini yanlışlıkla silme veya değiştirme işleminden önce bir hesabı önceki bir zaman noktasına geri yükleyin.** [Azure Portal](continuous-backup-restore-portal.md#restore-live-account), geri yükleme süresini bulmak için bir kapsayıcının ne zaman oluşturulduğunu, değiştirildiğini veya silindiğini öğrenmek için olay akışı bölmesini kullanabilirsiniz. Benzer şekilde, [Azure CLI](continuous-backup-restore-command-line.md#trigger-restore) ve [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)ile kapsayıcı olayları akışını silerek tüm kapsayıcı olaylarını bulabilir ve ardından restore komutunu gerekli parametrelerle tetikleyebilirsiniz.

## <a name="permissions"></a>İzinler

Azure Cosmos DB, sürekli yedekleme hesabı için geri yükleme izinlerini belirli bir role veya bir sorumluya ayırmanızı ve kısıtlayamanızı sağlar. Hesap sahibi bir geri yüklemeyi tetikleyebilir ve geri yükleme işlemini gerçekleştirmek için diğer sorumlularına bir rol atayabilir. Daha fazla bilgi edinmek için [izinler](continuous-backup-restore-permissions.md) makalesine bakın.

## <a name="pricing"></a><a id="continuous-backup-pricing"></a>Fiyatlandırma

Sürekli yedeklemenin etkin olduğu Azure Cosmos DB hesapları *, yedeklemenin depolanması* ve *verilerinizi geri yüklemek* için ek bir aylık ücret doğuracaktır. Geri yükleme maliyeti, geri yükleme işlemi her başlatıldığında eklenir. Sürekli yedekleme ile bir hesap yapılandırırsanız ancak verileri geri yüklüyorsanız, faturanızda yalnızca yedekleme depolama alanı maliyeti yer alır.

Aşağıdaki örnek, ABD 'deki devlet dışı bir bölgede dağıtılan bir Azure Cosmos hesabının fiyatını temel alır. Fiyatlandırma ve hesaplama, kullandığınız bölgeye bağlı olarak değişebilir, en son fiyatlandırma bilgileri için [Azure Cosmos DB fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/cosmos-db/) bakın.

* Sürekli yedekleme ilkesiyle etkinleştirilen tüm hesaplar, yedekleme depolaması için aşağıdaki şekilde hesaplanan ek bir aylık ücret doğurur:

  $0.20/GB * hesapta GB cinsinden veri boyutu * bölge sayısı

* Her geri yükleme API 'SI çağrısı bir zaman ücreti doğurur. Ücret, veri geri yükleme miktarının bir işlevidir ve aşağıdaki şekilde hesaplanır:

  GB cinsinden $0.15/GB * veri boyutu.

Örneğin, iki bölgede 1 TB veri varsa:

* Yedekleme depolama maliyeti (1000 * 0,20 * 2) = $400/ayda hesaplanır

* Geri yükleme maliyeti, geri yükleme başına (1000 * 0,15) = $150 olarak hesaplanır

## <a name="current-limitations-public-preview"></a>Geçerli sınırlamalar (Genel Önizleme)

Şu anda zaman içindeki nokta geri yükleme işlevselliği genel önizlemede ve aşağıdaki sınırlamalara sahiptir:

* Sürekli yedeklemede yalnızca SQL ve MongoDB için Azure Cosmos DB API'leri desteklenir. Cassandra, Tablo ve Gremlin API’leri henüz desteklenmemektedir.

* Varsayılan dönemsel yedekleme ilkesiyle var olan bir hesap sürekli yedekleme modunu kullanacak şekilde dönüştürülemez.

* Azure sogeign ve Azure Kamu bulut bölgeleri henüz desteklenmiyor.

* Müşteri tarafından yönetilen anahtarları olan hesapların sürekli yedekleme kullanabilmesi desteklenmez.

* Çok bölgeli yazma hesapları desteklenmez.

* SYNAPSE bağlantısı etkin olan hesaplar desteklenmez.

* Geri yüklenen hesaplar kaynak hesabınızın bulunduğu bölgede oluşturulur. Hesabı, kaynak hesabın bulunmadığı bir bölgeye geri yükleyemezsiniz.

* Geri yükleme penceresi yalnızca 30 gündür ve değiştirilemez.

* Yedeklemeler coğrafi bölgedeki olağanüstü durumlara otomatik olarak dayanıklı değildir. Hesap ve yedeklemeye dayanıklılık getirmek için başka bir bölgeyi açıkça eklemeniz gerekir.

* Bir geri yükleme devam ederken, hesap için izinleri veren kimlik ve erişim yönetimi (ıAM) ilkelerini değiştirmeyin veya silin veya VNET, güvenlik duvarı yapılandırmasını değiştirin.

* Kapsayıcı oluşturulduktan sonra benzersiz dizini oluşturan SQL veya MongoDB için Azure Cosmos DB API'si hesapları sürekli yedeklemede desteklenmez. Yalnızca ilk kapsayıcı oluşturma işlemi kapsamında benzersiz dizini oluşturan kapsayıcılar desteklenir. MongoDB hesaplarında, [uzantı komutlarını](mongodb-custom-commands.md)kullanarak benzersiz dizin oluşturursunuz.

* Belirli bir noktaya geri yükleme işlevi her zaman yeni bir Azure Cosmos hesabına geri yükleme yapar. Mevcut hesaba geri yükleme şu anda desteklenmemektedir. Yerinde geri yükleme hakkında geri bildirim sağlamaya ilgileniyorsanız, hesap temsilciniz veya [UserVoice](https://feedback.azure.com/forums/263030-azure-cosmos-db)aracılığıyla Azure Cosmos DB ekibine başvurun.

* ,,,,,,,,,,,,,,,, Özelliği önizleme aşamasında olan tüm yeni API 'ler `RestorableDatabaseAccount` `RestorableSqlDatabases` `RestorableSqlContainer` `RestorableMongodbDatabase` `RestorableMongodbCollection` değişikliklere tabidir

* Geri yüklendikten sonra, belirli koleksiyonlar için tutarlı dizinin yeniden derlenmesi mümkündür. [Indexdönüşümtionprogress](how-to-manage-indexing-policy.md) özelliği aracılığıyla yeniden oluşturma işleminin durumunu kontrol edebilirsiniz.

* Geri yükleme işleminde TTL yapılandırması da dahil olmak üzere kapsayıcının tüm özellikleri geri yüklenir. Sonuç olarak, bu şekilde yapılandırırsanız geri yüklenen veriler hemen silinebilir. Bu durumu önlemek için geri yükleme zaman damgası TTL özelliklerinin kapsayıcıya eklenmesinden önceki bir tarih olmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)veya [Azure Resource Manager](continuous-backup-restore-template.md)kullanarak sürekli yedeklemeyi yapılandırın ve yönetin.
* Sürekli yedekleme moduyla verileri geri yüklemek için gereken [Izinleri yönetin](continuous-backup-restore-permissions.md) .
* [Sürekli yedekleme modunun kaynak modeli](continuous-backup-restore-resource-model.md)
