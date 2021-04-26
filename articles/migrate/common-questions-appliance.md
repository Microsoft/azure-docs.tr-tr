---
title: Azure geçişi gereç hakkında SSS
description: Azure geçişi gereci hakkında sık sorulan soruların yanıtlarını alın.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 059a1888b529487f2b0d17509370897222a20d83
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563030"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure geçişi gereci: genel sorular

Bu makalede, Azure geçişi gereci hakkında sık sorulan sorular yanıtlanmaktadır. Başka sorularınız varsa şu kaynakları kontrol edin:

- Azure geçişi hakkında [genel sorular](resources-faq.md)
- [Bulma, değerlendirme ve bağımlılık görselleştirmesiyle](common-questions-discovery-assessment.md) ilgili sorular
- [Sunucu geçişi](common-questions-server-migration.md) hakkında sorular
- [Azure geçişi forumundaki](https://aka.ms/AzureMigrateForum) soruların yanıtlarını alın

## <a name="what-is-the-azure-migrate-appliance"></a>Azure geçişi gereci nedir?

Azure geçişi gereci, Azure geçişi: bulma ve değerlendirme aracının şirket içi veya herhangi bir buluttan fiziksel veya sanal sunucuları bulmak ve değerlendirmek için kullandığı hafif bir gereç. Azure geçişi: sunucu geçiş aracı Ayrıca, VMware ortamında çalışan şirket içi sunucuların aracısız geçişi için gereç kullanır.

Azure geçişi gereci hakkında daha fazla bilgi edinebilirsiniz:

- Gereç, şirket içinde fiziksel bir sunucu veya sanallaştırılmış bir sunucu olarak dağıtılır.
- Gereç, şirket içi sunucuları bulur ve sürekli olarak sunucu meta verilerini ve performans verilerini Azure geçişi 'ne gönderir.
- Gereç keşfi aracısız. Bulunan sunucularda hiçbir şey yüklü değil.

Gereç hakkında [daha fazla bilgi edinin](migrate-appliance.md) .

## <a name="how-can-i-deploy-the-appliance"></a>Gereci nasıl dağıtırım?

Gereç birkaç yöntem kullanılarak dağıtılabilir:

- Gereç, VMware veya Hyper-V ortamında ([VMware Için ova şablonu](how-to-set-up-appliance-vmware.md) veya [Hyper-v için VHD](how-to-set-up-appliance-hyper-v.md)) çalışan sunucular için bir şablon kullanılarak dağıtılabilir.
- Bir şablon kullanmak istemiyorsanız, bir [PowerShell yükleyici betiği](deploy-appliance-script.md)kullanarak VMware veya Hyper-V ortamı için gereci dağıtabilirsiniz.
- Azure Kamu 'da, bir PowerShell yükleyici betiği kullanarak gereci dağıtmanız gerekir. [Burada](deploy-appliance-script-government.md)Dağıtım adımlarına bakın.
- Şirket içinde veya başka bir bulutta bulunan fiziksel veya sanallaştırılmış sunucular için, her zaman gereci bir PowerShell yükleyici betiği kullanarak dağıtırsınız. [Burada](how-to-set-up-appliance-physical.md)Dağıtım adımlarına bakın.

## <a name="how-does-the-appliance-connect-to-azure"></a>Gereç Azure 'a nasıl bağlanır?

Gereç Internet üzerinden veya Azure ExpressRoute kullanarak bağlanabilir. 

- Gerecin bu [Azure URL 'lerine](./migrate-appliance.md#url-access)bağlanabildiğinizden emin olun. 
- ExpressRoute 'ı Microsoft eşlemesi ile birlikte kullanabilirsiniz. Ortak eşleme kullanım dışıdır ve yeni ExpressRoute devreleri için kullanılamaz.
- Yalnızca özel eşleme desteklenmez.


## <a name="does-appliance-analysis-affect-performance"></a>Gereç Analizi performansı etkiler mi?

Azure, alet profillerini şirket içi sunuculara, performans verilerini ölçmek için sürekli olarak geçirin. Bu profil oluşturma, profili oluşturulmuş sunucular üzerinde neredeyse performans etkisine sahip değildir.

## <a name="can-i-harden-the-appliance"></a>Gereci kullanabilir miyim?

Gereci oluşturmak için indirilen şablonu kullandığınızda, Azure geçişi gereci için gerekli olan iletişim ve güvenlik duvarı kurallarını yerinde bırakırsanız şablona bileşen ekleyebilirsiniz (örneğin, virüsten koruma).

## <a name="what-network-connectivity-is-required"></a>Hangi ağ bağlantısı gerekir?

Gerecin Azure URL 'Lerine erişmesi gerekiyor. URL listesini [gözden geçirin](migrate-appliance.md#url-access) .

## <a name="what-data-does-the-appliance-collect"></a>Gereç hangi verileri toplar?

Azure geçişi gerecinin sunucularda topladığı veriler hakkında bilgi edinmek için aşağıdaki makalelere bakın:

- **VMware ortamındaki sunucular**: toplanan verileri [gözden geçirin](migrate-appliance.md#collected-data---vmware) .
- **Hyper-V ortamındaki sunucular**: toplanan verileri [gözden geçirin](migrate-appliance.md#collected-data---hyper-v) .
- **Fiziksel veya sanal sunucular**: toplanan verileri [gözden geçirin](migrate-appliance.md#collected-data---physical) .

## <a name="how-is-data-stored"></a>Veriler nasıl depolanır?

Azure geçişi gereci tarafından toplanan veriler, projeyi oluşturduğunuz Azure konumunda depolanır.

Verilerin nasıl depolandığı hakkında daha fazla bilgi aşağıdadır:

- Toplanan veriler, CosmosDB 'de bir Microsoft aboneliğine güvenli bir şekilde depolanır. Projeyi sildiğinizde veriler silinir. Depolama, Azure geçişi tarafından işlenir. Toplanan veriler için özel olarak bir depolama hesabı seçemezsiniz.
- [Bağımlılık görselleştirmesi](concepts-dependency-visualization.md)kullanıyorsanız, toplanan veriler Azure aboneliğinizde oluşturulan bir Azure Log Analytics çalışma alanında depolanır. Aboneliğinizdeki Log Analytics çalışma alanını sildiğinizde veriler silinir.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Sürekli profil oluşturma sırasında karşıya ne kadar veri yüklendi?

Azure geçişi 'ne gönderilen verilerin hacmi birden çok parametreye bağlıdır. Örnek olarak, 10 sunucu (her biri bir disk ve bir NIC ile) olan bir proje günde yaklaşık 50 MB veri gönderir. Bu değer yaklaşık değerlerdir; Gerçek değer, disklerin ve NIC 'Lerin veri noktalarının sayısına bağlı olarak değişir. Sunucu, disk veya NIC sayısı arttıkça, gönderilen verilerdeki artış doğrusal değildir.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Veriler, bekleyen ve aktarım sırasında şifrelenir mi?

Evet, her ikisi için:

- Meta veriler, HTTPS üzerinden Internet üzerinden Azure geçişi hizmetine güvenli bir şekilde gönderilir.
- Meta veriler bir [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) veritabanında ve [Azure Blob depolamada](../storage/common/storage-service-encryption.md) bir Microsoft aboneliğine depolanır. Meta veriler, depolama için bekleyen bir şekilde şifrelenir.
- Bağımlılık Analizi verileri de aktarım sırasında şifrelenir (güvenli HTTPS tarafından). Bu, aboneliğinizdeki bir Log Analytics çalışma alanında depolanır. Veriler, bağımlılık analizi için geri kalanında şifrelenir.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Gereç vCenter Server nasıl bağlanır?

Bu adımlar, gerecin VMware vCenter Server nasıl bağlandığını anlatmaktadır:

1. Gereç, gereci ayarlarken verdiğiniz kimlik bilgilerini kullanarak vCenter Server (bağlantı noktası 443) öğesine bağlanır.
2. Gereç, vCenter Server tarafından yönetilen sunucularla ilgili meta verileri toplamak üzere vCenter Server sorgulamak için VMware PowerCLI kullanır.
3. Gereç, sunucular (çekirdek, bellek, diskler, NIC 'ler) ve geçmiş ay için her bir sunucunun performans geçmişi hakkında yapılandırma verilerini toplar.
4. Toplanan meta veriler, değerlendirme için Azure geçişi: bulma ve değerlendirme aracına (HTTPS üzerinden Internet üzerinden) gönderilir.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Azure geçişi gereci birden çok vCenter sunucusuna mi bağlanabilir?

Hayır. [Azure geçişi](migrate-appliance.md) gereci ve vCenter Server arasında bire bir eşleme var. Birden çok vCenter Server örneği üzerindeki sunucuları saptamak için birden çok gereç dağıtmanız gerekir.

## <a name="can-a-project-have-multiple-appliances"></a>Bir projede birden çok gereç olabilir mi?

Bir projeye kayıtlı birden fazla gereç bulunabilir. Ancak, bir gereç yalnızca bir projeyle kaydedilebilir.

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Azure geçiş gereci/çoğaltma gereci aynı vCenter 'a bağlanmasına mi?

Evet. Hem Azure geçiş gereci (değerlendirme ve aracısız VMware geçişi için kullanılır) hem de çoğaltma gereci (VMware 'de çalışan sunucuların aracı tabanlı geçirilmesi için kullanılan) aynı vCenter sunucusuna ekleyebilirsiniz. Ancak, aynı sunucuda her iki gereç de ayarlamadığınızdan emin olun ve şu anda desteklenmemektedir.

## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>Bir gereç ile kaç sunucu keşfedebilirim?

VMware ortamında en fazla 10.000 sunucusu, Hyper-V ortamında en fazla 5.000 sunucuya ve tek bir gereç ile en fazla 1000 fiziksel sunucuya kadar bulabilirsiniz. Şirket içi ortamınızda daha fazla sunucunuz varsa, [Hyper-V değerlendirmesi ölçekleme](scale-hyper-v-assessment.md), [VMware değerlendirmesi ölçekleme](scale-vmware-assessment.md)ve [fiziksel sunucu değerlendirmesini ölçeklendirme](scale-physical-assessment.md)hakkında bilgi edinin.

## <a name="can-i-delete-an-appliance"></a>Bir gereci silebilir miyim?

Şu anda, bir gereci projeden silme desteklenmez.

Gereci silmenin tek yolu, Gereç ile ilişkili projeyi içeren kaynak grubunu silmektir.

Ancak, kaynak grubu silindiğinde diğer kayıtlı gereçler, bulunan envanter, değerlendirmeler ve proje ile ilişkili kaynak grubundaki diğer tüm Azure bileşenleri de silinir.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Gereci farklı bir abonelik ya da projeyle kullanabilir miyim?

Gereci farklı bir abonelik veya projeyle birlikte kullanmak için, Gereç üzerindeki belirli bir senaryo (VMware/Hyper-V/fiziksel) için PowerShell yükleyicisi betiğini çalıştırarak mevcut gereci yeniden yapılandırmanız gerekir. Komut dosyası, yeni bir gereç dağıtmak için mevcut gereç bileşenlerini ve ayarlarını temizler. Yeni dağıtılan gereç yapılandırma yöneticisini kullanmaya başlamadan önce tarayıcı önbelleğini temizlemeniz gerekir.

Ayrıca, yeniden yapılandırılmış bir gereç üzerinde varolan bir proje anahtarını yeniden kullanamazsınız. Gereç kaydını tamamlayabilmeniz için istenen abonelik/projeden yeni bir anahtar oluşturmadığınızdan emin olun.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Gereci bir Azure VM üzerinde ayarlayabilir miyim?

Hayır. Şu anda bu seçenek desteklenmez.

## <a name="can-i-discover-on-an-esxi-host"></a>ESXi konağında bulunabilir miyim?

Hayır. VMware ortamındaki sunucuları bulmaya yönelik vCenter Server sahip olmanız gerekir.

## <a name="how-do-i-update-the-appliance"></a>Nasıl yaparım? gereç güncelleştirilsin mi?

Varsayılan olarak, Gereç ve yüklü aracıları otomatik olarak güncelleştirilir. Gereç, güncelleştirmeleri 24 saatte bir denetler. Başarısız olan güncelleştirmeler yeniden denenir. 

Yalnızca Gereç ve gereç aracıları bu otomatik güncelleştirmeler tarafından güncelleştirilir. İşletim sistemi, Azure tarafından otomatik güncelleştirme geçişi tarafından güncelleştirilmedi. İşletim sistemini güncel tutmak için Windows güncelleştirmelerini kullanın.

## <a name="can-i-check-agent-health"></a>Aracı sistem durumunu denetleyebilir miyim?

Evet. Portalda Azure geçişi: bulma ve değerlendirme ya da Azure geçişi: sunucu geçiş aracı için **Aracı sistem durumu** sayfasına gidin. Burada, aracıda Azure ile bulma ve değerlendirme aracıları arasındaki bağlantı durumunu kontrol edebilirsiniz.

## <a name="can-i-add-multiple-server-credentials-on-vmware-appliance"></a>VMware gerecine birden çok sunucu kimlik bilgisi ekleyebilir miyim?

Evet, artık yazılım envanterini (yüklü uygulamaların bulunması), aracısız bağımlılık analizini ve SQL Server örneklerinin ve veritabanlarının bulunmasını sağlamak için birden çok sunucu kimlik bilgisini destekliyoruz. Gereç Yapılandırma Yöneticisi 'nde kimlik bilgileri sağlama hakkında [daha fazla bilgi edinin](tutorial-discover-vmware.md#provide-server-credentials) .

## <a name="what-type-of-server-credentials-can-i-add-on-the-vmware-appliance"></a>VMware gerecine ne tür sunucu kimlik bilgileri ekleyebilirim?
Gereç Yapılandırma Yöneticisi 'nde etki alanı/Windows (etki alanı olmayan)/Linux (etki alanı olmayan)/SQL Server kimlik doğrulama kimlik bilgileri sağlayabilirsiniz. Kimlik bilgilerini ve bunları nasıl işleyeceğinizi [öğrenmek hakkında daha fazla bilgi edinin](add-server-credentials.md) .

## <a name="what-type-of-sql-server-connection-properties-are-supported-by-azure-migrate-for-sql-discovery"></a>SQL bulma için Azure geçişi tarafından desteklenen SQL Server bağlantı özellikleri türü nedir?
Azure geçişi, Azure geçişi gereci ve kaynak SQL Server örnekleri (şifreleme bağlantısı özelliği TRUE olarak ayarlanır) arasındaki iletişimi şifreler. Bu bağlantılar [TrustServerCertificate](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) ile ŞIFRELENIR (true olarak ayarlanır); Aktarım katmanı, kanalı şifrelemek ve güveni doğrulamak üzere sertifika zincirini atlamak için SSL kullanır. Gereç sunucusunun, [sertifikanın kök yetkilisine güvenecek](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)şekilde ayarlanması gerekir.

Sunucu başlatıldığında sunucuda bir sertifika sağlanmadıysa SQL Server, oturum açma paketlerini şifrelemek için kullanılan kendinden imzalı bir sertifika oluşturur. [Daha fazla bilgi edinin](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).


## <a name="next-steps"></a>Sonraki adımlar

[Azure geçişi 'ne genel bakış](migrate-services-overview.md)konusunu okuyun.