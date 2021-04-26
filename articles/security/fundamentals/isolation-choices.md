---
title: Azure genel bulutu 'nda yalıtım | Microsoft Docs
description: Azure 'un hem kötü amaçlı hem de kötü amaçlı olmayan kullanıcılara yönelik yalıtımı nasıl sağladığını öğrenin ve Mimarlar için çeşitli yalıtım seçenekleri sunar.
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: TomSh
ms.openlocfilehash: c06fb0830ae709918b668ed60efbaaf47a63ce84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94842847"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Azure genel bulutunda yalıtım

Azure, paylaşılan fiziksel altyapıda uygulama ve sanal makine (VM) çalıştırmanızı sağlar. Uygulamaları bir bulut ortamında çalıştırmaya yönelik başlıca ekonobilirlerden biri, paylaşılan kaynakların maliyetini birden çok müşteri arasında dağıtabilme yeteneğidir. Çok kiracılı bu uygulama, düşük maliyetlerde farklı müşteriler arasında kaynakları çoğullama açısından verimliliği artırır. Ne yazık ki, önemli uygulamalarınızı ve diğer altyapı kaynaklarını, rastgele ve potansiyel olarak kötü amaçlı bir kullanıcıya ait olabilecek duyarlı uygulamalarınızı ve VM 'Leri çalıştırmak üzere paylaşma riskini de beraberinde getirir.

Bu makalede, Azure 'un hem kötü amaçlı hem de kötü amaçlı olmayan kullanıcılara karşı yalıtım sağladığı ve mimarlara çeşitli yalıtım seçenekleri sunarak bulut çözümlerini mimari olarak sunan bir kılavuz olarak hizmet verdiği özetlenmektedir.

## <a name="tenant-level-isolation"></a>Kiracı düzeyi yalıtımı

Bulut bilgi işlemin başlıca avantajlarından biri, aynı anda çok sayıda müşteriyle paylaşılan, ortak bir altyapının kavramıdır ve bu da ölçek ekonomisine göre önde olur. Bu kavram çok kiracılı olarak adlandırılır. Microsoft, Microsoft Bulut Azure 'un çok kiracılı mimarisinin güvenlik, gizlilik, gizlilik, bütünlük ve kullanılabilirlik standartlarını desteklediğinden emin olmak için sürekli olarak çalışmaktadır.

Bulutun etkin olduğu çalışma alanında kiracı, bulut hizmetinin belirli bir örneğine sahip olan ve o örneği yöneten bir istemci veya kuruluş olarak tanımlanabilir. Microsoft Azure tarafından sunulan kimlik platformu ile bir kiracı, bir Microsoft bulut hizmetine kaydolduğunda kuruluşunuzun aldığı ve sahip olduğu adanmış bir Azure Active Directory (Azure AD) örneğidir.

Her Azure AD dizini, diğer Azure AD dizinlerinden farklı ve ayrıdır. Kurumsal ofis binasının yalnızca kuruluşunuza özel güvenilir bir varlık olması gibi, Azure AD dizini de yalnızca sizin kuruluşunuz tarafından kullanılmak üzere tasarlanan güvenilir bir varlıktır. Azure AD mimarisi, müşteri verilerini ve kimlik bilgilerini ortak karıştırma alanından yalıtır. Bu, bir Azure AD dizinindeki kullanıcıların ve yöneticilerin yanlışlıkla veya kötü amaçlı olarak başka bir dizindeki verilere erişemeyeceği anlamına gelir.

### <a name="azure-tenancy"></a>Azure Kiralama

Azure Kiralama (Azure aboneliği), [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)bir "müşteri/Faturalandırma" ilişkisine ve benzersiz bir [kiracıya](../../active-directory/develop/quickstart-create-new-tenant.md) başvurur. Microsoft Azure 'de kiracı düzeyi yalıtımı, BT tarafından sunulan Azure Active Directory ve [Azure rol tabanlı erişim denetimi](../../role-based-access-control/overview.md) kullanılarak elde edilir. Her Azure aboneliği bir Azure Active Directory (AD) diziniyle ilişkilendirilir.

Bu dizindeki kullanıcılar, gruplar ve uygulamalar, Azure aboneliğindeki kaynakları yönetebilir. Azure portal, Azure komut satırı araçları ve Azure Yönetim API 'Lerini kullanarak bu erişim haklarını atayabilirsiniz. Bir Azure AD kiracısı güvenlik sınırları kullanılarak mantıksal olarak yalıtılmıştır; böylece hiçbir müşteri kötü amaçlı olarak veya yanlışlıkla, ortak kiracılara erişemez veya bunları tehlikeye atabilir. Azure AD, ana bilgisayar düzeyindeki paket filtrelemesinin ve Windows güvenlik duvarının istenmeyen bağlantıları ve trafiği engelleyebildiği, ayrılmış bir ağ segmentinde yalıtılmış olan "çıplak" sunucularda çalışır.

- Azure AD 'deki verilere erişim, bir güvenlik belirteci hizmeti (STS) aracılığıyla Kullanıcı kimlik doğrulaması gerektirir. Kullanıcının varlığı, etkin durumu ve rolü hakkındaki bilgiler, bu oturumdaki bu kullanıcı için istenen hedef kiracıya erişim yetkisi olup olmadığını öğrenmek üzere yetkilendirme sistemi tarafından kullanılır.

![Azure Kiralama](./media/isolation-choices/azure-isolation-fig1.png)

- Kiracılar ayrı kapsayıcılardır ve bunlar arasında bir ilişki yoktur.

- Kiracı Yöneticisi tarafından Federasyon veya diğer kiracılardan Kullanıcı hesapları sağlama izni verilmediği müddetçe kiracılar arasında erişim yoktur.

- Azure AD hizmetini oluşturan sunuculara fiziksel erişim ve Azure AD 'nin arka uç sistemlerine doğrudan erişim kısıtlanmıştır.

- Azure AD kullanıcılarının fiziksel varlıklara veya konumlara erişimi yoktur ve bu nedenle, aşağıda belirtilen mantıksal Azure RBAC ilke denetimlerini atlaması mümkün değildir.

Tanılama ve bakım ihtiyaçları için, tam zamanında ayrıcalık yükseltme sistemini kullanan bir işlem modeli gereklidir ve kullanılır. Azure AD Privileged Identity Management (PıM) uygun bir yönetici kavramını tanıtır. [uygun yöneticiler](../../active-directory/privileged-identity-management/pim-configure.md) , her gün değil, daha önce ayrıcalıklı erişime ihtiyacı olan kullanıcılar olmalıdır. Bu rol, kullanıcı erişime ihtiyaç duyana kadar devre dışıdır ancak kullanıcı bir etkinleştirme işlemini tamamladıktan sonra önceden belirlenen süre boyunca etkin bir yönetici olur.

![Azure AD Privileged Identity Management](./media/isolation-choices/azure-isolation-fig2.png)

Azure Active Directory, her kiracıyı kendi korunan kapsayıcısında barındırır ve yalnızca kiracı tarafından sahip olunan ve içindeki ve içindeki ve içindeki ve içindeki ve içindeki izinlerle ilgili ilkeler

Kiracı kapsayıcıları kavramı, portallardan ve kalıcı depolamaya kadar her katmandaki dizin hizmetinde daha ayrıntılı bir şekilde yapılır.

Birden çok Azure Active Directory kiracısından gelen meta veriler aynı fiziksel diskte depolandığında bile, dizin hizmeti tarafından tanımlandıkları dışındaki kapsayıcılar arasında hiçbir ilişki yoktur, bu da kiracı yöneticisi tarafından belirlenir.

### <a name="azure-role-based-access-control-azure-rbac"></a>Azure rol tabanlı erişim denetimi (Azure RBAC)

Azure [rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md) , Azure için ayrıntılı erişim yönetimi sağlayarak bir Azure aboneliğinde bulunan çeşitli bileşenleri paylaşmanıza yardımcı olur. Azure RBAC, kuruluşunuzdaki görevleri ayırabilirsiniz ve kullanıcıların işlerini gerçekleştirmesi için ihtiyaç duyduğu kullanıcılara göre erişim izni vermenizi sağlar. Azure aboneliğinde veya kaynaklarında herkes için sınırsız izin vermek yerine yalnızca belirli eylemlere izin verebilirsiniz.

Azure RBAC, tüm kaynak türleri için uygulanan üç temel role sahiptir:

- **Sahibinin** , başkalarına erişim yetkisi verme hakkı dahil tüm kaynaklara tam erişimi vardır.

- **Katkıda bulunan** her türlü Azure kaynağı oluşturabilir ve yönetebilir, ancak diğer kullanıcılara erişim izni veremez.

- **Okuyucu** , mevcut Azure kaynaklarını görüntüleyebilir.

![Azure rol tabanlı erişim denetimi (Azure RBAC)](./media/isolation-choices/azure-isolation-fig3.png)

Azure 'daki Azure rollerinin geri kalanı belirli Azure kaynaklarının yönetimine izin verir. Örneğin Sanal Makine Katılımcısı rolü, kullanıcının sanal makine oluşturmasını ve yönetmesini sağlar. Azure sanal ağına veya sanal makinenin bağlandığı alt ağa erişim vermez.

Azure [yerleşik rolleri](../../role-based-access-control/built-in-roles.md) , Azure 'da kullanılabilen rolleri listeler. Her yerleşik rolün kullanıcılara verdiği işlemleri ve kapsamı belirtir. Daha da fazla denetim için kendi rollerinizi tanımlamak istiyorsanız bkz. [Azure RBAC 'de özel roller](../../role-based-access-control/custom-roles.md)oluşturma.

Azure Active Directory için bazı diğer yetenekler şunlardır:

- Azure AD, barındırıldıkları yerden bağımsız olarak SaaS uygulamaları için SSO özelliğini etkinleştirir. Bazı uygulamalar Azure AD federasyonu kullanırken diğerleri parola SSO hizmetinden yararlanır. Federasyon uygulamaları, Kullanıcı hazırlama ve [parola](https://www.techopedia.com/definition/31415/password-vault)oluşturma işlemlerini de destekleyebilir.

- [Azure Storage](https://azure.microsoft.com/services/storage/) verilerine erişim, kimlik doğrulaması ile denetlenir. Her depolama hesabında birincil anahtar ([depolama hesabı anahtarı](../../storage/common/storage-account-create.md)veya sak) ve ikincil gizli anahtar (paylaşılan erişim IMZASı veya SAS) bulunur.

- Azure AD, şirket içi dizinlerle [Active Directory Federasyon Hizmetleri (AD FS)](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs), eşitleme ve çoğaltma kullanarak Federasyon aracılığıyla bir hizmet olarak kimlik sağlar.

- [Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) , kullanıcıların oturum açma işlemlerini bir mobil uygulama, telefon araması veya kısa mesaj kullanarak doğrulamasını gerektiren Multi-Factor Authentication hizmetidir. Azure Multi-Factor Authentication sunucusu ile şirket içi kaynakların güvenliğini sağlamaya ve ayrıca SDK kullanan özel uygulamalarla ve dizinlerden yardım almak için Azure AD ile birlikte kullanılabilir.

- [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) , Azure sanal makinelerini etki alanı denetleyicilerini dağıtmaya gerek kalmadan bir Active Directory etki alanına aktarmanıza olanak tanır. Şirket Active Directory kimlik bilgilerinizle bu sanal makinelerde oturum açabilir ve tüm Azure sanal makinelerinizde güvenlik temellerini zorlamak için grup ilkesi kullanarak etki alanına katılmış sanal makineleri yönetebilirsiniz.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) , yüz milyonlarca kimliği ölçeklendirirken tüketiciye yönelik uygulamalar için yüksek düzeyde kullanılabilir bir genel kimlik yönetim hizmeti sağlar. Bu hizmet mobil platformlar ve web platformlarıyla tümleştirilebilir. Tüketicileriniz, mevcut sosyal hesaplarını kullanarak veya kimlik bilgileri oluşturarak özelleştirilebilen deneyimler aracılığıyla tüm uygulamalarınızda oturum açabilir.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Microsoft yöneticilerinin yalıtımı & verileri silme

Microsoft, verilerinizi uygunsuz erişimlerden korumak veya yetkisiz kişiler tarafından kullanmak için güçlü ölçüler kullanır. Bu operasyonel işlemler ve denetimler, verilerinize erişimi yöneten sözleşme taahhütlerini sunan [çevrimiçi hizmet koşulları](https://aka.ms/Online-Services-Terms)tarafından desteklenir.

- Microsoft mühendisleri, buluttaki verilerinize varsayılan erişime sahip değildir. Bunun yerine, yönetim gözetim bölümünde yalnızca gerekli olduğunda erişim verilir. Bu erişim dikkatle denetlenir ve günlüğe kaydedilir ve artık gerekli olmadığında iptal edilir.
- Microsoft, diğer şirketlere, kendi adına sınırlı hizmetleri sağlamak için işe alabilir. Alt yükleniciler, müşteri verilerine yalnızca hizmetleri teslim etmek için erişebilir, bunları sağlamak üzere işe sunuyoruz ve diğer amaçlarla kullanmaları yasaktır. Diğer bir deyişle, müşterilerin bilgilerinin gizliliğini korumak için sözleşmeye dayalı bağımlıdır.

ISO/ıEC 27001 gibi denetlenen sertifikaları olan iş hizmetleri, Microsoft ve acalacaklandırılan denetim firmaları tarafından düzenli olarak doğrulanır ve bu da erişimi yalnızca meşru iş amaçlarıyla test etmek için örnek denetimler gerçekleştirir. Her zaman kendi müşteri verilerinize dilediğiniz zaman ve herhangi bir nedenle erişebilirsiniz.

Herhangi bir veriyi silerseniz Microsoft Azure, önbelleğe alınmış veya yedek kopyalar dahil olmak üzere verileri siler. Kapsam içi hizmetler için, bu silme işlemi, bekletme döneminin sonundan sonra 90 gün içinde olur. (Kapsam içi hizmetler, [çevrimiçi hizmetler koşullarımızın](https://aka.ms/Online-Services-Terms)veri işleme koşulları bölümünde tanımlanmıştır.)

Depolama için kullanılan bir disk sürücüsü bir donanım arızası içeriyorsa, Microsoft bu dosyayı değiştirme veya onarma amacıyla üreticiye geri göndermeden önce güvenli bir şekilde [silinir veya yok](https://microsoft.com/trustcenter/privacy/you-own-your-data) edilir. Verilerin herhangi bir şekilde kurtarılamayacağını sağlamak için sürücüdeki verilerin üzerine yazılır.

## <a name="compute-isolation"></a>İşlem yalıtımı

Microsoft Azure, uygulamanızın veya kuruluşunuzun ihtiyaçlarını karşılayacak şekilde otomatik olarak ölçeklenebilen ve en düşük düzeyde işlem örnekleri & hizmetleri içeren çeşitli bulut tabanlı bilgi işlem hizmetleri sağlar. Bu işlem örneği ve hizmeti, verilerin güvenliğini sağlamak için birden çok düzeyde yalıtım sunarak, müşterilerin talep ettiği yapılandırmadaki esnekliğe karşı daha fazla esneklik sunar.

### <a name="isolated-virtual-machine-sizes"></a>Yalıtılmış sanal makine boyutları

[!INCLUDE [virtual-machines-common-isolation](../../../includes/virtual-machines-common-isolation.md)]

### <a name="dedicated-hosts"></a>Ayrılmış konaklar

Önceki bölümde açıklanan yalıtılmış konaklara ek olarak, Azure ayrıca adanmış konaklar da sunar. Azure 'daki adanmış konaklar, bir veya daha fazla sanal makineyi barındırabilen ve tek bir Azure aboneliğine adanmış fiziksel sunucular sağlayan bir hizmettir. Adanmış konaklar, fiziksel sunucu düzeyinde donanım yalıtımı sağlar. Konaklarınıza başka VM 'Ler yerleştirilmeyecektir. Ayrılmış konaklar aynı veri merkezlerinde dağıtılır ve aynı ağı ve temel alınan depolama altyapısını diğer, yalıtılmış olmayan konaklarla paylaşır. Daha fazla bilgi için bkz. [Azure adanmış konaklara](../../virtual-machines/dedicated-hosts.md)yönelik ayrıntılı genel bakış.

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Hyper-V & kök VM & Konuk VM 'Ler arasında kök işletim sistemi yalıtımı

Azure 'un işlem platformu, makine sanallaştırmaya dayalıdır; yani tüm müşteri kodu bir Hyper-V sanal makinesinde yürütülür. Her bir Azure düğümünde (veya ağ uç noktası), doğrudan donanım üzerinde çalışan ve bir düğümü değişken sayıda konuk sanal makineye (VM) ayıran bir hiper yönetici vardır.

![Hyper-V & kök VM & Konuk VM 'Ler arasında kök işletim sistemi yalıtımı](./media/isolation-choices/azure-isolation-fig4.jpg)

Her düğüm Ayrıca konak işletim sistemini çalıştıran bir özel kök sanal makineye sahiptir. Kritik sınır, hiper yönetici ve kök işletim sistemi tarafından yönetilen bir diğeri Konuk VM 'lerden ve konuk sanal makinelerinden gelen kök VM 'nin yalıtımına sahiptir. Hiper yönetici/kök işletim sistemi eşleştirme, Microsoft 'un işletim sistemi güvenlik deneyiminin ve Microsoft 'un Hyper-V ' d e daha yeni öğrenmesinin yanı sıra konuk VM 'lerin güçlü yalıtımının sağlanması için yararlanır.

Azure platformu, sanallaştırılmış bir ortam kullanır. Kullanıcı örnekleri, bir fiziksel ana bilgisayar sunucusuna erişimi olmayan tek başına sanal makineler olarak çalışır.

Azure Hiper Yöneticisi, mikro çekirdek gibi davranır ve konuk sanal makinelerinizdeki tüm donanım erişim isteklerini, VMBus adlı paylaşılan bellek arabirimini kullanarak işleme için konağa geçirir. Bu, kullanıcıların sistemde ham okuma/yazma/yürütme erişimine sahip olmasını önler ve sistem kaynaklarının paylaşımıyla ilgili riskleri azaltır.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Gelişmiş VM yerleştirme algoritması, yan kanal saldırılarına karşı koruma &

Tüm sanal makineler arası saldırılarda iki adım vardır: bir sanal makine, kurban VM 'lerinden biri ile aynı konağa ve daha sonra hassas kurban bilgilerini çalmak veya Greed ya da vandalısm performansını etkilemek için yalıtım sınırına ulaşıyor. Microsoft Azure, gürültülü komşu VM 'Ler dahil tüm bilinen taraf kanal saldırılarına karşı gelişmiş bir VM yerleştirme algoritması ve koruma kullanarak her iki adımda de koruma sağlar.

### <a name="the-azure-fabric-controller"></a>Azure yapı denetleyicisi

Azure yapı denetleyicisi, kiracı iş yüklerine altyapı kaynakları ayırmadan sorumludur ve konaktan sanal makinelere tek yönlü iletişimleri yönetir. Azure Fabric denetleyicisi 'nin VM yerleştirme algoritması, fiziksel ana bilgisayar düzeyi olarak tahmin etmek için yüksek düzeyde gelişmiş ve neredeyse imkansızdır.

![Azure yapı denetleyicisi](./media/isolation-choices/azure-isolation-fig5.png)

Azure Hiper Yöneticisi, sanal makineler arasında bellek ve işlem ayrımı uygular ve ağ trafiğini Konuk işletim sistemi kiracılarına güvenli bir şekilde yönlendirir. Bu, VM düzeyinde ve yan kanal saldırısı olasılığını ortadan kaldırır.

Azure 'da, kök VM özeldir: bir yapı Aracısı (SK) barındıran kök IŞLETIM sistemi olarak adlandırılan, sağlamlaştırılmış bir işletim sistemini çalıştırır. FAs, müşteri VM 'lerinde Konuk işletim sistemleri içinde Konuk aracılarını (GA) yönetmek için kullanılır. FAs Ayrıca depolama düğümlerini da yönetir.

Azure Hiper Yöneticisi, kök işletim sistemi/SK ve müşteri VM 'Leri/gaz koleksiyonu bir işlem düğümünü kapsar. FAs, işlem ve depolama düğümlerinin dışında bulunan bir yapı denetleyicisi (FC) tarafından yönetilir (işlem ve depolama kümeleri ayrı FCs tarafından yönetilir). Bir müşteri, uygulamanın yapılandırma dosyasını çalışırken güncelleştirirken, FC, bu, yapılandırma değişikliğini uygulamaya bildiren, daha sonra gaz ile iletişim kuran SK ile iletişim kurar. Donanım arızası durumunda, FC otomatik olarak kullanılabilir donanımı bulur ve VM 'yi burada yeniden başlatır.

![Azure yapı denetleyicisi](./media/isolation-choices/azure-isolation-fig6.jpg)

Yapı denetleyicisinden aracıya iletişim tek yönlü olur. Aracı yalnızca denetleyicideki isteklere yanıt veren SSL korumalı bir hizmet uygular. Denetleyiciye veya diğer ayrıcalıklı iç düğümlere bağlantı başlatamaz. FC, tüm yanıtları güvenilmeyen gibi davranır.

![Yapı denetleyicisi](./media/isolation-choices/azure-isolation-fig7.png)

Yalıtım, Konuk VM 'lerden ve konuk sanal makinelerden diğerine genişletilir. İşlem düğümleri, daha fazla koruma için depolama düğümlerinden de yalıtılmıştır.

Hiper yönetici ve konak işletim sistemi, güvenilmeyen sanal makinelerin sahte trafik oluşturmamasını veya bunlara değinilmemiş trafiği alamamasını, korumalı altyapı uç noktalarına doğrudan trafik veya uyumsuz yayın trafiği göndermesini veya alamamasını sağlamaya yardımcı olmak için ağ paketi filtreleri sağlar.

### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>VM 'yi yalıtmak için doku denetleyicisi Aracısı tarafından yapılandırılan ek kurallar

Varsayılan olarak, bir sanal makine oluşturulduğunda tüm trafik engellenir ve ardından yapı denetleyicisi Aracısı, yetkili trafiğe izin vermek için kural ve özel durumlar eklemek üzere paket filtresini yapılandırır.

Programlanmış iki kural kategorisi vardır:

- **Makine yapılandırması veya altyapı kuralları:** Varsayılan olarak, tüm iletişimler engellenir. Bir sanal makinenin DHCP ve DNS trafiği gönderebilmesi ve almasına izin vermek için özel durumlar vardır. Sanal makineler Ayrıca "genel" internet 'e trafik gönderebilir ve aynı Azure sanal ağı ve işletim sistemi etkinleştirme sunucusu içindeki diğer sanal makinelere trafik gönderebilir. İzin verilen giden hedeflerin sanal makinelerin listesi Azure yönlendirici alt ağları, Azure yönetimi ve diğer Microsoft özelliklerini içermez.
- **Rol yapılandırma dosyası:** Bu, kiracının hizmet modeline göre gelen Access Control listelerini (ACL 'Ler) tanımlar.

### <a name="vlan-isolation"></a>VLAN yalıtımı

Her kümede üç VLAN vardır:

![VLAN yalıtımı](./media/isolation-choices/azure-isolation-fig8.jpg)

- Ana VLAN – güvenilmeyen Müşteri düğümlerini birbirine bağlar
- FC VLAN: güvenilen FCs ve destekleyici sistemler içerir
- Cihaz VLAN 'ı – güvenilen ağ ve diğer altyapı cihazlarını içerir

FC VLAN ile ana VLAN arasında iletişime izin verilir, ancak ana VLAN 'dan FC VLAN 'a başlatılamaz. Ayrıca iletişim, ana VLAN 'dan cihaz VLAN 'a engellenir. Bu, müşteri kodu çalıştıran bir düğümün tehlikeye düşmesi durumunda olsa da, FC veya cihaz VLAN 'Ları üzerinde düğümlere saldıramaz.

## <a name="storage-isolation"></a>Depolama yalıtımı

### <a name="logical-isolation-between-compute-and-storage"></a>Işlem ve depolama arasındaki mantıksal yalıtım

Temel tasarımının bir parçası olarak Microsoft Azure VM tabanlı hesaplamayı depolamadan ayırır. Bu ayrım, hesaplamanın ve depolamanın bağımsız olarak ölçeklendirilmesine olanak sağlayarak çok kiracılı ve yalıtımın sağlanması kolaylaşır.

Bu nedenle, Azure Storage mantıksal dışında Azure Işlem ile ağ bağlantısı olmayan ayrı donanımlar üzerinde çalışır. Bu, bir sanal disk oluşturulduğunda, tüm kapasiteye yönelik disk alanının ayrılmadığı anlamına gelir. Bunun yerine, sanal disk üzerindeki adresleri fiziksel diskteki alanlarla eşleyen bir tablo oluşturulur ve bu tablo başlangıçta boştur. **Bir müşteri sanal diske veri yazdığında, fiziksel diskteki alan ayrılır ve tabloya bir işaretçi yerleştirilir.**

### <a name="isolation-using-storage-access-control"></a>Depolama erişim denetimi kullanarak yalıtım

**Azure depolamada Access Control** basit bir erişim denetimi modeline sahiptir. Her Azure aboneliği, bir veya daha fazla depolama hesabı oluşturabilir. Her depolama hesabının, bu depolama hesabındaki tüm verilere erişimi denetlemek için kullanılan tek bir gizli anahtarı vardır.

![Depolama erişim denetimi kullanarak yalıtım](./media/isolation-choices/azure-isolation-fig9.png)

**Azure depolama verilerine (tablolar dahil) erişim** , kapsamlı erişim veren bir [SAS (paylaşılan erişim imzası)](../../storage/common/storage-sas-overview.md) belirteci aracılığıyla denetlenebilir. SAS, [sak (depolama hesabı anahtarı)](/previous-versions/azure/reference/ee460785(v=azure.100))ile imzalanmış bir sorgu şablonu (URL) ile oluşturulur. Bu [IMZALı URL](../../storage/common/storage-sas-overview.md) , başka bir işleme (yani, temsilcili) verilebilir, bu, daha sonra sorgunun ayrıntılarını doldurabilir ve depolama hizmeti isteğini oluşturabilir. SAS, depolama hesabının gizli anahtarını göstermeden istemcilere zaman tabanlı erişim sağlamanıza olanak sağlar.

SAS, belirli bir süre boyunca ve belirli bir izin kümesiyle, bir istemci sınırlı izinleri, depolama hesabımızda bulunan nesnelere sağlayabilmemiz anlamına gelir. Hesap erişim anahtarlarınızı paylaşmak zorunda kalmadan bu sınırlı izinleri sağlayabiliriz.

### <a name="ip-level-storage-isolation"></a>IP düzeyi depolama yalıtımı

Güvenlik duvarları oluşturabilir ve güvenilen istemcileriniz için bir IP adresi aralığı tanımlayabilirsiniz. Bir IP adresi aralığı ile yalnızca tanımlı aralıkta bir IP adresi olan istemciler [Azure depolama](../../storage/blobs/security-recommendations.md)'ya bağlanabilir.

IP depolama verileri yetkisiz kullanıcılardan, IP depolama alanına adanmış veya adanmış bir trafik tüneli ayırmak için kullanılan bir ağ mekanizması aracılığıyla korunabilir.

### <a name="encryption"></a>Şifreleme

Azure, verileri korumak için aşağıdaki şifreleme türlerini sunar:

- Aktarım sırasında şifreleme
- Bekleme sırasında şifreleme

#### <a name="encryption-in-transit"></a>Aktarım sırasında şifreleme

Aktarım sırasında şifreleme, ağlar arasında iletilirken verilerin korunmasında bir mekanizmadır. Azure depolama ile, aşağıdakileri kullanarak verileri güvenli hale getirebilirsiniz:

- Azure depolama içine veya dışına veri aktarırken HTTPS gibi [Aktarım düzeyi şifreleme](../../storage/blobs/security-recommendations.md).
- Azure dosya paylaşımları için SMB 3,0 Şifrelemesi gibi bir [hat şifreleme](../../storage/blobs/security-recommendations.md).
- Depolama alanına aktarılmadan önce verileri şifrelemek ve depolama alanı dışına aktarıldıktan sonra verilerin şifresini çözmek için [istemci tarafı şifreleme](../../storage/blobs/security-recommendations.md).

#### <a name="encryption-at-rest"></a>Bekleyen şifreleme

Birçok kuruluş için, [bekleyen veri şifreleme](isolation-choices.md) , veri gizliliği, uyumluluk ve veri egemenlik 'e yönelik zorunlu bir adımdır. "Bekleyen" veri şifrelemesini sağlayan üç Azure özelliği vardır:

- [Depolama hizmeti şifrelemesi](../../storage/blobs/security-recommendations.md) , depolama hizmetinin verileri Azure depolama 'ya yazarken otomatik olarak şifrelemesine olanak tanır.
- [İstemci tarafı şifreleme](../../storage/blobs/security-recommendations.md) , bekleyen şifreleme özelliğini de sağlar.
- [Azure disk şifrelemesi](./azure-disk-encryption-vms-vmss.md) , bir IaaS sanal makinesi tarafından kullanılan işletim sistemi disklerini ve veri disklerini şifrelemenizi sağlar.

#### <a name="azure-disk-encryption"></a>Azure Disk Şifrelemesi

Sanal makineler için [Azure disk şifrelemesi](./azure-disk-encryption-vms-vmss.md) (VM 'ler), [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)DENETLEDIĞINIZ anahtarlar ve ilkelerle VM disklerini (önyükleme ve veri diskleri dahil) şifreleyerek kurumsal güvenlik ve uyumluluk gereksinimlerini sağlamanıza yardımcı olur.

Windows için disk şifreleme çözümü, [Microsoft BitLocker Sürücü Şifrelemesi](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11))tabanlıdır ve Linux çözümü [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt)' i temel alır.

Çözüm, Microsoft Azure ' de etkinleştirildiklerinde IaaS VM 'Leri için aşağıdaki senaryoları destekler:

- Azure Key Vault ile tümleştirme
- Standart katman VM 'Leri: A, D, DS, G, GS, vb., Series IaaS VM 'Leri
- Windows ve Linux IaaS VM 'lerinde şifrelemeyi etkinleştirme
- Windows IaaS VM 'Leri için işletim sistemi ve veri sürücülerinde şifrelemeyi devre dışı bırakma
- Linux IaaS VM 'Leri için veri sürücülerinde şifrelemeyi devre dışı bırakma
- Windows istemci işletim sistemi çalıştıran IaaS sanal makinelerinde şifrelemeyi etkinleştirme
- Bağlama yollarındaki birimlerde şifrelemeyi etkinleştirme
- [Mdaddm](https://en.wikipedia.org/wiki/Mdadm) kullanılarak disk ŞERIDI (RAID) Ile yapılandırılmış Linux VM 'lerinde şifrelemeyi etkinleştirme
- Veri diskleri için [LVM (mantıksal birim Yöneticisi)](/windows/win32/fileio/about-volume-management) kullanarak Linux VM 'lerde şifrelemeyi etkinleştirme
- Depolama alanları kullanılarak yapılandırılan Windows VM 'lerinde şifrelemeyi etkinleştirme
- Tüm Azure ortak bölgeleri desteklenir

Çözüm, sürümde aşağıdaki senaryoları, özellikleri ve teknolojiyi desteklemez:

- Temel katman IaaS VM 'Leri
- Linux IaaS VM 'Leri için bir işletim sistemi sürücüsünde şifrelemeyi devre dışı bırakma
- Klasik VM oluşturma yöntemi kullanılarak oluşturulan IaaS VM 'Leri
- Şirket içi anahtar yönetim hizmetiniz ile tümleştirme
- Azure dosyaları (paylaşılan dosya sistemi), ağ dosya sistemi (NFS), dinamik birimler ve yazılım tabanlı RAID sistemleriyle yapılandırılmış Windows VM 'Leri

## <a name="sql-database-isolation"></a>SQL veritabanı yalıtımı

SQL Veritabanı, piyasa lideri Microsoft SQL Server altyapısını temel alan ve görev açısından kritik iş yüklerini üstlenebilen, Microsoft bulutu tabanlı bir ilişkisel veritabanı hizmetidir. SQL veritabanı, hesap düzeyinde, coğrafi konum/bölge temelinde tahmin edilebilir veri yalıtımı sağlar.

### <a name="sql-database-application-model"></a>SQL veritabanı uygulama modeli

[MICROSOFT SQL veritabanı](../../azure-sql/database/single-database-create-quickstart.md) , SQL Server teknolojileri üzerinde oluşturulmuş bulut tabanlı bir ilişkisel veritabanı hizmetidir. Microsoft tarafından bulutta barındırılan, yüksek oranda kullanılabilir, ölçeklenebilir, çok kiracılı bir veritabanı hizmeti sağlar.

SQL veritabanı, bir uygulama perspektifinden aşağıdaki hiyerarşiyi sağlar: her düzey aşağıda bir-çok düzeyi kapsama sahiptir.

![SQL veritabanı uygulama modeli](./media/isolation-choices/azure-isolation-fig10.png)

Hesap ve abonelik, faturalandırma ve yönetimi ilişkilendirmek için Microsoft Azure platform kavramlardır.

SQL veritabanı özgü kavramlardır ve SQL veritabanı, OData ve TSQL arabirimleri sağlanmış olarak veya Azure portal aracılığıyla yönetilir.

SQL veritabanı 'nda bulunan sunucular, veritabanı koleksiyonları, paylaşım yönetimi ve güvenlik ilkeleri olmak yerine, "mantıksal ana" veritabanı olarak adlandırılan, fiziksel veya VM örnekleri değildir.

![SQL Veritabanı](./media/isolation-choices/azure-isolation-fig11.png)

Mantıksal ana veritabanları şunları içerir:

- Sunucuya bağlanmak için kullanılan SQL oturum açmalar
- Güvenlik duvarı kuralları

Aynı sunucudan veritabanları için faturalandırma ve kullanımla ilgili bilgilerin, kümedeki aynı fiziksel örnekte olması garanti edilmez, bunun yerine uygulamalar bağlanırken hedef veritabanı adını sağlamalıdır.

Bir müşteri perspektifinden, bir coğrafi grafik bölgesinde sunucu oluşturulur ve sunucu gerçek oluşturma bölgedeki kümelerden birinde gerçekleşir.

### <a name="isolation-through-network-topology"></a>Ağ topolojisi ile yalıtma

Sunucu oluşturulduğunda ve DNS adı kaydedildiğinde, DNS adı sunucunun yerleştirildiği belirli veri merkezinde "ağ geçidi VIP" adresine işaret eder.

VIP 'nin (sanal IP adresi) arkasında durum bilgisiz ağ geçidi Hizmetleri koleksiyonudur. Genel olarak, birden çok veri kaynağı (ana veritabanı, Kullanıcı veritabanı vb.) arasında koordinasyon olduğunda, ağ geçitleri söz konusu olabilir. Ağ geçidi Hizmetleri aşağıdakileri uygular:

- **TDS bağlantı proxy 'i.** Bu, arka uç kümesinde kullanıcı veritabanını konumlandırmayı, oturum açma dizisini uygulamayı ve ardından TDS paketlerini arka uca ve geri iletmeyi içerir.
- **Veritabanı yönetimi.** Bu, CREATE/ALTER/DROP DATABASE işlemleri yapmak için bir iş akışı koleksiyonu uygulamayı içerir. Veritabanı işlemleri, algılama TDS paketleri ya da açık OData API 'Leri tarafından çağrılabilir.
- OLUŞTURMA/DEĞIŞTIRME/BıRAKMA oturum açma/kullanıcı işlemleri
- OData API aracılığıyla sunucu yönetim işlemleri

![Ağ topolojisi ile yalıtma](./media/isolation-choices/azure-isolation-fig12.png)

Ağ geçitlerinin arkasındaki katmana "arka uç" adı verilir. Bu, tüm verilerin yüksek oranda kullanılabilir bir biçimde depolandığı yerdir. Her veri parçası, en az üç çoğaltmaya sahip olan bir "Bölüm" veya "yük devretme birimine" ait olarak kabul edilir. Çoğaltmalar SQL Server altyapısı tarafından depolanır ve çoğaltılır ve genellikle "Fabric" olarak adlandırılan bir yük devretme sistemi tarafından yönetilir.

Genellikle, arka uç sistemi bir güvenlik önlemi olarak diğer sistemlerle giden iletişim kurmaz. Bu, ön uç (Gateway) katmanındaki sistemlere ayrılmıştır. Ağ Geçidi katmanı makineleri, saldırı yüzeyini derinlemesine bir savunma mekanizması olarak en aza indirmek için arka uç makinelerde sınırlı ayrıcalıklara sahiptir.

### <a name="isolation-by-machine-function-and-access"></a>Makine Işlevine ve erişime göre yalıtma

SQL veritabanı (farklı makine işlevlerinde çalışan hizmetlerden oluşur. SQL veritabanı, "arka uç" bulut veritabanına ve "ön uç" (ağ geçidi/yönetim) ortamlarına ayrılmıştır ve genel trafik ilkesi yalnızca arka uca ve çıkış haline getirilir. Ön uç ortamı diğer hizmetlerin dış dünyasına iletişim kurabilir ve genel olarak arka uçta yalnızca sınırlı izinlere sahiptir (çağırmak için gereken giriş noktalarını çağırmak yeterlidir).

## <a name="networking-isolation"></a>Ağ yalıtımı

Azure dağıtımında birden çok ağ yalıtımı katmanı vardır. Aşağıdaki diyagramda, Azure 'un müşterilerine sağladığı çeşitli ağ yalıtımı katmanları gösterilmektedir. Bu katmanların ikisi de Azure platformunda yerel ve müşteri tanımlı özelliklerdir. Internet 'ten gelen Azure DDoS, Azure 'da büyük ölçekli saldırılara karşı yalıtımı sağlar. Bir sonraki yalıtım katmanı, bulut hizmetinden sanal ağa hangi trafiğin geçebileceği belirlenmesi için kullanılan müşteri tanımlı genel IP adresleridir (uç noktalar). Yerel Azure sanal ağ yalıtımı, diğer tüm ağlardan tamamen yalıtımı sağlar ve bu trafik yalnızca Kullanıcı tarafından yapılandırılan yollar ve yöntemler aracılığıyla akar. Bu yollar ve Yöntemler, NSGs, UDR ve ağ sanal gereçlerinin, korunan ağdaki uygulama dağıtımlarını korumak üzere yalıtım sınırları oluşturmak için kullanılabileceği bir sonraki katmandır.

![Ağ yalıtımı](./media/isolation-choices/azure-isolation-fig13.png)

**Trafik yalıtımı:** [Sanal ağ](../../virtual-network/virtual-networks-overview.md) , Azure platformunda trafik yalıtımı sınırıdır. Tek bir sanal ağdaki sanal makineler (VM), aynı müşteri tarafından her iki sanal ağ da oluşturulsa bile, farklı bir sanal ağdaki VM 'Ler ile doğrudan iletişim kuramaz. Yalıtım, bir sanal ağ içinde müşteri VM 'lerinin ve iletişimin özel kalmasını sağlayan kritik bir özelliktir.

[Alt ağ](../../virtual-network/virtual-networks-overview.md) , IP aralığına göre sanal ağ ile ek bir yalıtım katmanı sunar. Sanal ağdaki IP adresleri, bir sanal ağı kuruluş ve güvenlik için birden çok alt ağa bölebilirsiniz. Bir sanal ağ içindeki alt ağlara (aynı veya farklı) dağıtılan VM'ler ve PaaS rolü örnekleri, ek bir yapılandırma gerektirmeden birbirleriyle iletişim kurabilir. Ayrıca, NSG erişim denetim listesi 'nde (ACL) yapılandırılan kurallara göre bir VM örneğine ağ trafiğine izin vermek veya reddetmek için [ağ güvenlik grubu (NSG 'ler)](../../virtual-network/virtual-networks-overview.md) yapılandırabilirsiniz. NSG'ler alt ağlarla veya bu alt ağların içindeki tekil VM örnekleriyle ilişkili olabilir. NSG bir alt ağ ile ilişkili olduğunda ACL kuralları bu alt ağdaki tüm VM örnekleri için geçerli olur.

## <a name="next-steps"></a>Sonraki Adımlar

- [Windows Azure sanal ağlarında makineler Için ağ yalıtımı seçenekleri](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/)hakkında bilgi edinin. Bu, belirli bir arka uç ağı veya alt ağı içindeki makinelerin yalnızca belirli bir uç noktaya izin verilen IP adresleri listesine bağlı olarak belirli bir uç noktaya bağlanmasına izin veren klasik ön uç ve arka uç senaryosunu içerir.

- [Azure 'da sanal makine yalıtımı](../../virtual-machines/isolation.md)hakkında bilgi edinin. Azure Işlem, belirli bir donanım türüne yalıtılmış ve tek bir müşteriye adanmış sanal makine boyutları sunar.