---
title: Log Analytics veri güvenliği | Microsoft Docs
description: Log Analytics gizliliğinizi nasıl koruduğunu ve verilerinizin güvenliğini nasıl sağlayacağınızı öğrenin.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 03/04/2019
ms.openlocfilehash: 3ff69928f4d6aa1692cdb1d4fd7e846b3a6b7a5c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932548"
---
# <a name="log-analytics-data-security"></a>Log Analytics veri güvenliği
Bu belge, [Azure Güven Merkezi](../../security/fundamentals/trust-center.md)bilgileri tamamlayacak bir Azure izleyici özelliği olan Log Analytics özgü bilgiler sağlamaya yöneliktir.  

Bu makalede verilerin Log Analytics tarafından nasıl toplandığı, işlendiği ve güvenlik altına alındığı açıklanır. Web hizmetine bağlanmak için aracıları kullanabilir, işletimsel verileri toplamak için System Center Operations Manager kullanabilir veya Log Analytics tarafından kullanılmak üzere Azure tanılama 'dan veri alabilirsiniz. 

Log Analytics hizmeti, aşağıdaki yöntemleri kullanarak bulut tabanlı verilerinizi güvenli bir şekilde yönetir:

* Veri ayırma
* Veri saklama
* Fiziksel güvenlik
* Olay yönetimi
* Uyumluluk
* Güvenlik standartları sertifikaları

[Azure destek seçeneklerinde](https://azure.microsoft.com/support/options/)güvenlik ilkeleriniz dahil olmak üzere, aşağıdaki bilgilerden herhangi biri hakkında sorularınız, öneriler veya sorunlar ile bizimle iletişim kurun.

## <a name="sending-data-securely-using-tls-12"></a>TLS 1,2 kullanarak güvenli bir şekilde veri gönderme 

Log Analytics yoldaki verilerin güvenliğini sağlamak için, aracıyı en az Aktarım Katmanı Güvenliği (TLS) 1,2 kullanacak şekilde yapılandırmanızı önemle öneririz. TLS/Güvenli Yuva Katmanı (SSL) uygulamasının güvenlik açığı olduğu ve geriye dönük uyumlulukla hala çalışmaya devam eden daha eski sürümleri, bunlar **önerilmez**ve sektör bu eski sürümler için hızlı bir şekilde devam eder ekledikten. 

[PCI güvenlik standartları Council](https://www.pcisecuritystandards.org/) , TLS/SSL 'nin eski sürümlerini devre dışı bırakmak ve daha güvenli protokollere yükseltmek Için [30 Haziran 2018 ' nin son tarihini](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) ayarladı. Azure eski desteği düşürdüğünde, aracılarınız en az TLS 1,2 üzerinden iletişim kuramıyorsa, Log Analytics veri gönderemeyebilirsiniz. 

Yalnızca, kullanılabilir hale geldiğinde daha yeni güvenli protokollerle otomatik olarak algılama ve bu özelliklerden faydalanmanıza olanak tanıyan platform düzeyi güvenlik özelliklerini bozabileceği için, aracınız 'yi yalnızca TLS 1,2 kullanacak şekilde kesinlikle ayarlamamız önerilmez. as TLS 1,3. 

### <a name="platform-specific-guidance"></a>Platforma özgü kılavuz

|Platform/dil | Destek | Daha Fazla Bilgi |
| --- | --- | --- |
|Linux | Linux dağıtımları, TLS 1,2 desteği için [OpenSSL](https://www.openssl.org) 'yi kullanır.  | OpenSSL sürümünüzü doğrulamak için [OpenSSL changelog](https://www.openssl.org/news/changelog.html) ' yı denetleyin.|
| Windows 8,0-10 | Desteklenir ve varsayılan olarak etkindir. | Hala [varsayılan ayarları](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)kullandığınızdan emin olun.  |
| Windows Server 2012-2016 | Desteklenir ve varsayılan olarak etkindir. | [Varsayılan ayarları](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) hala kullandığınızı doğrulamak için |
| Windows 7 SP1 ve Windows Server 2008 R2 SP1 | Desteklenir, ancak varsayılan olarak etkinleştirilmez. | ' Nin nasıl etkinleştirileceği hakkında ayrıntılı bilgi için bkz. [Aktarım Katmanı Güvenliği (TLS) kayıt defteri ayarları](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) sayfası.  |

## <a name="data-segregation"></a>Veri ayırma
Verileriniz Log Analytics hizmeti tarafından alındıktan sonra, veriler hizmet genelinde her bir bileşen üzerinde mantıksal olarak ayrı tutulur. Tüm veriler çalışma alanı başına etiketlendi. Bu etiketleme, veri yaşam döngüsü boyunca devam eder ve her bir hizmet katmanında uygulanır. Verileriniz, seçtiğiniz bölgedeki depolama kümesindeki özel bir veritabanında depolanır.

## <a name="data-retention"></a>Veri saklama
Dizinli günlük arama verileri, fiyatlandırma planınıza göre depolanır ve korunur. Daha fazla bilgi için bkz. [Log Analytics fiyatlandırması](https://azure.microsoft.com/pricing/details/log-analytics/).

[Abonelik sözleşmenizin](https://azure.microsoft.com/support/legal/subscription-agreement/)bir parçası olarak Microsoft, verilerinizi sözleşme koşullarına göre korur.  Müşteri verileri kaldırıldığında, hiçbir fiziksel sürücü yok edilmez.  

Aşağıdaki tabloda, kullanılabilir çözümlerin bazıları listelenmekte ve topladıkları veri türü örnekleri verilmektedir.

| **Çözüm** | **Veri türleri** |
| --- | --- |
| Kapasite ve Performans |Performans verileri ve meta veriler |
| Güncelleştirme Yönetimi |Meta veriler ve durum verileri |
| Günlük Yönetimi |Kullanıcı tanımlı olay günlükleri, Windows olay günlükleri ve/veya IIS günlükleri |
| Değişiklik İzleme |Yazılım envanteri, Windows hizmeti ve Linux Daemon meta verileri ve Windows/Linux dosya meta verileri |
| SQL ve Active Directory Değerlendirmesi |WMI verileri, kayıt defteri verileri, performans verileri ve SQL Server dinamik yönetim görünümü sonuçları |

Aşağıdaki tabloda veri türü örnekleri gösterilmektedir:

| **Veri türü** | **Alanını** |
| --- | --- |
| Uyarı |Uyarı adı, uyarı açıklaması, BaseManagedEntityId, sorun KIMLIĞI, ısmonitoralert, RuleId, ResolutionState, Priority, önem derecesi, kategori, sahip, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, Timeresoliner, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Yapılandırma |CustomerID, bjecttype TID, EntityId, ManagedTypeId, Managedtypepropertyıd, CurrentValue, ChangeDate |
| Olay |EventID, EventOriginalID, Basemanagedentityınternalıd, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Note:** Windows olay günlüğünde özel alanlarıyla olayları yazdığınızda, Log Analytics toplar. |
| Meta Veriler |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, Physicalişlemcilerle, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, Lastınventorydate, HostServerNameIsVirtualMachine, IP Adres, NetbiosDomainName, Logicalişlemcilerle, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Performans |ObjectName, CounterName, Perfmonınstancename, Performancedataıd, performanslı Cesourceınternalıd, SampleValue, Timeörneklenmiş, TimeAdded |
| Eyalet |Statechangeeventıd, stateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, Monitorıd, HealthState, LastModified, Lastlıalertgenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Fiziksel güvenlik
Log Analytics hizmeti Microsoft personeli tarafından yönetilir ve tüm etkinlikler günlüğe kaydedilir ve denetlenebilir. Log Analytics bir Azure hizmeti olarak çalıştırılır ve tüm Azure uyumluluk ve güvenlik gereksinimlerini karşılar. [Microsoft Azure güvenliğine genel bakış](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf)'ın 18. sayfasında Azure varlıklarının fiziksel güvenliği hakkındaki ayrıntıları görüntüleyebilirsiniz. Güvenli alanlara yönelik fiziksel erişim hakları, artık Log Analytics hizmeti sorumluluğunu olmayan herkes için, aktarım ve sonlandırma dahil olmak üzere bir iş günü içinde değiştirilir. [Microsoft veri merkezlerinde](https://azure.microsoft.com/global-infrastructure/)kullandığımız küresel fiziksel altyapıyı bulabilirsiniz.

## <a name="incident-management"></a>Olay yönetimi
Log Analytics, tüm Microsoft hizmetlerinin bağlı olduğu bir olay yönetimi işlemine sahiptir. Özetlemek gerekirse şunları yaptık:

* Güvenlik sorumluluğunun bir kısmının Microsoft 'a ait olduğu ve bir bölümün müşteriye ait olduğu bir paylaşılan sorumluluk modeli kullanın
* Azure Güvenlik olaylarını yönetme:
  * Bir olayın algılanması üzerine araştırma başlatma
  * Bir olayın etkisini ve önem derecesini, bir çağrı olayı yanıt ekibi üyesine göre değerlendirin. Kanıt temelinde, değerlendirme güvenlik yanıtı ekibine daha fazla yükseltme ile sonuçlanmayabilir veya bu olmayabilir.
  * Teknik veya Forli araştırma yapmak, kapsama, azaltma ve geçici çözüm stratejilerini belirlemek için güvenlik yanıtı uzmanlarıyla bir olay tanılayın. Güvenlik ekibi müşteri verilerinin izinsiz veya yetkisiz bir kişiye sunulduğunu düşündüğü takdirde, müşteri olay bildirim işleminin paralel yürütmesi paralel olarak başlar.  
  * Olaydan sabitleştir ve kurtarın. Olay yanıtı ekibi, sorunu azaltmak için bir kurtarma planı oluşturur. Diğer bir deyişle, etkilenen sistemleri karantinaya alma gibi kapsama adımları anında ve Tanılama ile paralel olarak gerçekleşebilir. Daha uzun süreli azaltmaları, hemen risk geçtikten sonra gerçekleşecek şekilde planlanabilir.  
  * Olayı kapatın ve mortem gerçekleştirin. Olay yanıtı ekibi, olayın tekrarlanmasını engellemek için ilkeleri, yordamları ve işlemleri gözden geçirme amacıyla, olayın ayrıntılarını özetleyen bir post-The-mortıtem oluşturur.
* Müşterilerimize güvenlik olaylarını bildir:
  * Etkilenen müşterilerin kapsamını belirleme ve mümkün olduğunca ayrıntılı bir şekilde etkilenmiş olan herkes için bir bildirim sağlama
  * Kullanıcılara, son kullanıcılarına yaptığı tüm taahhütlere bir araştırma gerçekleştirebilmesi ve bildirim işlemini etkilenmemesi bir süre erteleyerek son kullanıcılar için yapmış oldukları taahhütler sağlamak üzere yeterince ayrıntılı bilgiler sunabilmesi için bir bildirim oluşturun.
  * Gerektiğinde olayı onaylayın ve bildirin.
  * Müşterilere makul bir gecikme olmadan ve yasal ya da sözleşmeli taahhütte uygun olmayan bir olay bildirimi bildirin. Güvenlik olaylarının bildirimleri bir veya daha fazla müşterinin yöneticilerine, e-posta ile birlikte dahil olmak üzere Microsoft tarafından seçilen her türlü şekilde gönderilir.
* Takım hazırlığı ve eğitimi yürütün:
  * Microsoft personeli, güvenlik ve tanıma eğitiminin tamamlanabilmesi için gereklidir ve bu da şüpheli güvenlik sorunlarını tanımlayıp rapor eder.  
  * Microsoft Azure hizmetinde çalışan operatörler, müşteri verilerini barındıran hassas sistemlere erişimi çevreleyen ek eğitim yükümlülüklerine sahiptir.
  * Microsoft Güvenlik Yanıt personeli, rolleri için özelleştirilmiş eğitim alıyor

Müşteri verilerinin kaybolması meydana gelirse, her müşteriyi bir gün içinde bilgilendiririz. Ancak, hizmette müşteri veri kaybı hiçbir şekilde gerçekleşmez. 

Microsoft 'un güvenlik olaylarına nasıl yanıt verdiği hakkında daha fazla bilgi için bkz. [bulutta Microsoft Azure Güvenlik yanıtı](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf).

## <a name="compliance"></a>Uyumluluk
Log Analytics yazılım geliştirme ve hizmet ekibinin bilgi güvenliği ve idare programı, iş gereksinimlerini destekler ve [Microsoft Azure Güven Merkezi](https://azure.microsoft.com/support/trust-center/) ve [Microsoft Güven Merkezi ' nde açıklandığı gibi yasalar ve yönetmeliklere uyar Uyumluluk](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx). Log Analytics güvenlik gereksinimlerini nasıl belirler, güvenlik denetimlerini tanımlar, yönetir ve izleyiciler riskleri de burada açıklanmıştır. Yıllık olarak, ilkeleri, standartları, yordamları ve yönergeleri gözden geçiririz.

Her geliştirme ekibi üyesi resmi uygulama güvenliği eğitimi alır. Dahili olarak, yazılım geliştirme için bir sürüm denetim sistemi kullanırız. Her yazılım projesi, sürüm denetim sistemi tarafından korunur.

Microsoft, Microsoft 'taki tüm hizmetleri fazla olarak gördüğü ve değerlendirir bir güvenlik ve uyumluluk ekibine sahiptir. Bilgi güvenliği ofislerinin ekibi, Log Analytics geliştiren mühendislik ekipleriyle ilişkili değildir. Güvenlik ofislerinin kendi yönetim zinciri vardır ve güvenlik ve uyumluluk sağlamak için ürün ve hizmetlerin bağımsız değerlendirmelerini sağlar.

Microsoft 'un yöneticileri panosu, Microsoft 'ta tüm bilgi güvenliği programları hakkında yıllık bir rapor tarafından bildirilir.

Log Analytics yazılım geliştirme ve hizmet ekibi, çeşitli sertifikalar elde etmek üzere Microsoft yasal ve uyumluluk ekipleriyle ve diğer sektör ortaklarıyla etkin bir şekilde çalışır.

## <a name="certifications-and-attestations"></a>Sertifikalamaları ve belirlediğimizi karşıladığımızı
Azure Log Analytics aşağıdaki gereksinimleri karşılar:

* [ISO/ıEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/ıEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* PCI güvenlik standartları Council tarafından [ödeme kartı sektörü (PCI uyumlu) veri güvenliği standardı (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) .
* [Hizmet kuruluşu denetimleri (SoC) 1 tür 1 ve SOC 2 tür 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) uyumlu
* HIPAA Iş ilişkisi sözleşmesi olan şirketler için [HIPAA ve HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa)
* Windows ortak mühendislik ölçütleri
* Microsoft Güvenilir Bilgi İşlem
* Azure hizmeti olarak, Log Analytics kullandığı bileşenler Azure uyumluluk gereksinimlerine bağlı olarak kullanılır. [Microsoft Güven Merkezi uyumluluğuna](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx)daha fazla bilgi edinebilirsiniz.

> [!NOTE]
> Bazı sertifikalarında/belirlediğimizi karşıladığımızı, Log Analytics önceki *operasyonel*içgörüler adı altında listelenir.
>
>

## <a name="cloud-computing-security-data-flow"></a>Bulut bilgi işlem güvenliği veri akışı
Aşağıdaki diyagramda bir bulut güvenlik mimarisi, şirketinizdeki bilgilerin akışı ve güvenlik altına alındıkça Log Analytics hizmetine taşındıkça, sonunda sizin tarafınızdan Azure portal görülür. Her adım hakkında daha fazla bilgi diyagrama uyar.

![Log Analytics veri toplama ve güvenlik görüntüsü](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Log Analytics ve veri toplamak için kaydolun
Kuruluşunuzun Log Analytics veri gönderebilmesi için, Azure sanal makinelerinde çalışan bir Windows veya Linux aracısını ya da ortamınızdaki veya diğer bulut sağlayıcılarınızdaki sanal veya fiziksel bilgisayarları yapılandırın.  Operations Manager kullanıyorsanız, yönetim grubundan Operations Manager aracısını yapılandırırsınız. Kullanıcılar (siz, diğer bireysel kullanıcılar veya bir grup kişi olabilir) bir veya daha fazla Log Analytics çalışma alanı oluşturabilir ve aşağıdaki hesaplardan birini kullanarak aracıları kaydedebilirsiniz:

* [Kuruluş KIMLIĞI](../../active-directory/fundamentals/sign-up-organization.md)
* [Microsoft hesabı-Outlook, Office Live, MSN](https://account.microsoft.com/account)

Log Analytics çalışma alanı, verilerin toplandığı, toplanmış, çözümlenmiş ve sunulduğu yerdir. Çalışma alanı birincil olarak, verileri bölümlemek için bir yol olarak kullanılır ve her çalışma alanı benzersizdir. Örneğin, üretim verilerinizin bir çalışma alanıyla yönetilmesini ve test verilerinizin başka bir çalışma alanıyla yönetilmesini isteyebilirsiniz. Çalışma alanları, bir yöneticinin verilere erişimi denetlemesine de yardımcı olur. Her çalışma alanı kendisiyle ilişkilendirilmiş birden çok kullanıcı hesabı içerebilir ve her kullanıcı hesabı birden çok Log Analytics çalışma alanına erişebilir. Veri merkezi bölgesini temel alan çalışma alanları oluşturursunuz.

Operations Manager için Operations Manager yönetim grubu Log Analytics hizmetiyle bir bağlantı kurar. Ardından, yönetim grubundaki aracıyla yönetilen sistemlerin hizmete veri toplayıp gönderebileceği için izin verileceğini yapılandırırsınız. Etkinleştirdiğiniz çözüme bağlı olarak, bu çözümlerdeki veriler doğrudan bir Operations Manager yönetim sunucusundan Log Analytics hizmetine gönderilir ya da aracıyla yönetilen sistemle toplanan verilerin hacmi nedeniyle doğrudan gönderilir hizmetin aracı. Operations Manager tarafından izlenmeyen sistemler için, her biri Log Analytics hizmetine doğrudan güvenli bir şekilde bağlanır.

Bağlı sistemler ve Log Analytics hizmeti arasındaki tüm iletişimler şifrelenir. Şifreleme için TLS (HTTPS) protokolü kullanılır.  Microsoft SDL süreci, şifreleme protokollerinde en son gelişmelerden Log Analytics güncel olduğundan emin olmak için izlenir.

Her aracı türü Log Analytics için veri toplar. Toplanan verilerin türü, kullanılan çözüm türlerine bağlıdır. Veri toplamanın bir özetini [, Çözüm Galerisi Log Analytics çözüm Ekle](../../azure-monitor/insights/solutions.md)' de görebilirsiniz. Ayrıca, çoğu çözüm için daha ayrıntılı toplama bilgileri de mevcuttur. Bir çözüm, önceden tanımlanmış görünümler, günlük arama sorguları, veri toplama kuralları ve işlem mantığının bir paketidir. Yalnızca Yöneticiler, bir çözümü içeri aktarmak için Log Analytics kullanabilir. Çözüm alındıktan sonra, Operations Manager yönetim sunucularına (kullanılıyorsa) ve ardından seçtiğiniz aracılarla taşınır. Daha sonra, aracılar verileri toplar.

## <a name="2-send-data-from-agents"></a>2. aracılardan veri gönderme
Tüm aracı türlerini bir kayıt anahtarı ile kaydedersiniz ve bağlantı noktası 443 ile sertifika tabanlı kimlik doğrulaması ve SSL kullanarak aracı ve Log Analytics hizmeti arasında güvenli bir bağlantı oluşturulur. Log Analytics, anahtar oluşturmak ve sürdürmek için gizli bir depo kullanır. Özel anahtarlar her 90 günde bir döndürülür ve Azure 'da depolanır ve katı mevzuata ve uyumluluk uygulamalarını izleyen Azure işlemleri tarafından yönetilir.

Operations Manager, bir Log Analytics çalışma alanıyla kaydedilen yönetim grubu, bir Operations Manager yönetim sunucusuyla güvenli bir HTTPS bağlantısı kurar.

Azure sanal makineler üzerinde çalışan Windows veya Linux aracıları için, Azure tablolarındaki Tanılama olaylarını okumak üzere salt okunurdur bir depolama anahtarı kullanılır.  

Log Analytics ile tümleştirilmiş bir Operations Manager yönetim grubuna raporlama ile, yönetim sunucusu herhangi bir nedenle hizmetle iletişim kuramıyorsa, toplanan veriler yönetim üzerindeki geçici bir önbellekte yerel olarak depolanır. Server.   Verileri iki saat boyunca sekiz dakikada bir yeniden göndermeye çalışır.  Yönetim sunucusunu atlayan ve doğrudan Log Analytics gönderilen veriler için, davranış Windows aracısıyla tutarlıdır.  

Önbelleğe alınan Windows veya yönetim sunucusu Aracısı verileri, işletim sisteminin kimlik bilgisi deposu tarafından korunur. Hizmet iki saatten sonra verileri işleyemeyecek şekilde, aracılar verileri sıraya alacak. Sıra dolarsa, aracı, performans verilerinden başlayarak veri türlerini bırakmayı başlatır. Aracı sırası sınırı, gerekirse değişiklik yapabilmeniz için bir kayıt defteri anahtarıdır. Toplanan veriler sıkıştırılır ve hizmete gönderilir, Operations Manager yönetim grubu veritabanlarını atlayarak bunlara hiçbir yük eklemez. Toplanan veriler gönderildikten sonra önbellekten kaldırılır.

Yukarıda açıklandığı gibi, yönetim sunucusu veya doğrudan bağlı aracılardan veriler Microsoft Azure veri merkezlerine SSL üzerinden gönderilir. İsteğe bağlı olarak, ExpressRoute kullanarak veriler için ek güvenlik sağlayabilirsiniz. ExpressRoute, bir ağ hizmeti sağlayıcısı tarafından sağlanmış olan çok protokollü etiket anahtarlama (MPLS) VPN gibi mevcut WAN ağınızdan Azure 'a doğrudan bağlanmak için bir yoldur. Daha fazla bilgi için bkz. [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. Log Analytics hizmeti verileri alır ve işler
Log Analytics hizmeti, sertifikaları ve Azure kimlik doğrulamasıyla veri bütünlüğünü doğrulayarak gelen verilerin güvenilen bir kaynaktan olmasını sağlar. İşlenmemiş ham veriler daha sonra, verilerin sonunda bekleyen bir Azure Olay Hub 'ında depolanacaktır. Depolanan verilerin türü, içeri aktarılan ve veri toplamak için kullanılan çözümlerin türlerine bağlıdır. Daha sonra, Log Analytics hizmet ham verileri işler ve veritabanına geri gelir.

Veritabanında depolanan toplanan verilerin saklama süresi, seçilen fiyatlandırma planına bağlıdır. *Ücretsiz* katman için toplanan veriler yedi gün boyunca kullanılabilir. *Ücretli* katmanda, toplanan veriler varsayılan olarak 31 gün kullanılabilir, ancak 730 güne genişletilebilir. Veriler, verilerin gizliliğini sağlamak için Azure depolama 'da Rest 'ten şifrelenmiş olarak depolanır ve veriler yerel olarak yedekli depolama (LRS) kullanılarak yerel bölgede çoğaltılır. Verilerin son iki haftası da SSD tabanlı önbellekte depolanır ve bu önbellek şifrelenir.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. verilere erişmek için Log Analytics kullanın
Log Analytics çalışma alanınıza erişmek için, daha önce ayarladığınız kuruluş hesabını veya Microsoft hesabı kullanarak Azure portal oturum açın. Portal ve Log Analytics hizmeti arasındaki tüm trafik güvenli bir HTTPS kanalı üzerinden gönderilir. Portalı kullanırken Kullanıcı istemcisinde (Web tarayıcısı) bir oturum KIMLIĞI oluşturulur ve veriler, oturum sonlandırılana kadar yerel önbellekte depolanır. Sonlandırıldığında, önbellek silinir. Kişisel olarak tanımlanabilen bilgiler içermeyen istemci tarafı tanımlama bilgileri otomatik olarak kaldırılmaz. Oturum tanımlama bilgileri HTTPOnly olarak işaretlenir ve güvenli hale getirilir. Önceden belirlenmiş bir boşta kalma süresinden sonra, Azure portal oturumu sonlandırılır.

## <a name="next-steps"></a>Sonraki adımlar
* Azure [VM hızlı başlangıç](../../azure-monitor/learn/quick-collect-azurevm.md)adımlarını izleyerek Azure VM 'leriniz için Log Analytics verileri nasıl toplayacağınızı öğrenin.  

*  Ortamınızdaki fiziksel veya sanal Windows veya Linux bilgisayarlardan veri toplamak istiyorsanız bkz. [Linux bilgisayarları Için hızlı başlangıç](../../azure-monitor/learn/quick-collect-linux-computer.md) veya [Windows bilgisayarları için hızlı başlangıç](../../azure-monitor/learn/quick-collect-windows-computer.md)

