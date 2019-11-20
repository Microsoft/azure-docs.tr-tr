---
title: Azure Ayırmaları nedir?
description: Sanal makineleriniz, SQL veritabanlarınız, Azure Cosmos DB ve diğer kaynak maliyetlerinde tasarruf etmek için Azure Ayrılmış Sanal Makine Örnekleri ve fiyatlandırma hakkında bilgi edinin.
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: banders
ms.openlocfilehash: a002f5a1657454ff5048ab695e62eccd268a5891
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580086"
---
# <a name="what-are-azure-reservations"></a>Azure Ayırmaları nedir?

Azure Ayrılmış Sanal Makine Örnekleri sayesinde sanal makine, Azure Blob depolama veya Azure Data Lake Storage 2. Nesil, SQL Veritabanı işlem kapasitesi, Azure Cosmos DB aktarım hızı ve diğer Azure kaynakları için bir yıldan üç yıla kadar taahhütte bulunarak indirimlerden faydalanabilirsiniz. Taahhütte bulunarak kullandığınız kaynakları indirimli satın alabilirsiniz. Rezervasyonlar, kaynak maliyetlerinizi kullandıkça öde fiyatlarına kıyasla %72'ye varan oranda azaltabilir. Rezervasyonlar bir faturalandırma indirimi sağlar ve kaynaklarınızın çalışma zamanı durumunu etkilemez.

Bir rezervasyonun ödemesini peşin veya aylık olarak yapabilirsiniz. Peşin ve aylık rezervasyonların toplam maliyeti aynıdır ve aylık ödemeyi seçtiğinizde ekstra ücret ödemezsiniz. Aylık ödeme, üçüncü taraf ürünleri değil, Azure rezervasyonları için kullanılabilir.

[Azure portalından](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) da rezervasyon satın alabilirsiniz.

## <a name="why-buy-a-reservation"></a>Neden rezervasyon satın almalıyım?

Önemli miktarda kapasite veya aktarım hızı kullanan ya da uzun süreler boyunca çalışan sanal makineleriniz, Blob depolama verileriniz, Azure Cosmos DB veya SQL veritabanlarınız varsa, rezervasyon satın almak size en uygun maliyetli seçeneği sağlar. Örneğin, bir hizmetin dört örneğini rezervasyon olmadan sürekli olarak çalıştırdığınızda kullandıkça öde tarifesine göre ücretlendirilirsiniz. Bu kaynaklar için bir rezervasyon satın aldığınız anda rezervasyon indirimini elde edersiniz. Kaynaklar artık kullandıkça öde tarifelerine göre ücretlendirilmez.

## <a name="charges-covered-by-reservation"></a>Rezervasyon kapsamındaki ücretler

Hizmet planları:

- **Ayrılmış Sanal Makine Örneği** - Bir rezervasyon yalnızca sanal makine işlem maliyetlerini içerir. Ek yazılım, ağ veya depolama ücretlerini kapsamaz.
- **Azure Depolama ayrılmış kapasitesi** - Bir rezervasyon, Blob depolama veya Azure Data Lake 2. Nesil depolama için standart depolama hesaplarının depolama kapasitesini kapsar. Rezervasyon, bant genişliğini veya işlem oranlarını kapsamaz.
- **Azure Cosmos DB ayrılmış kapasitesi** - Rezervasyon yalnızca kaynaklarınız için sağlanan aktarım hızını kapsar. Depolama ve ağ ücretlerini kapsamaz.
- **SQL veritabanı ayrılmış sanal çekirdeği** - Rezervasyona yalnızca işlem maliyetleri dahildir. Lisans ayrı olarak faturalandırılır.
- **SQL Veri Ambarı** - Rezervasyon cDWU kullanımını kapsar. SQL Veri Ambarı’nın kullanımıyla ilişkili depolama ve ağ ücretlerini kapsamaz.
- **App Service taban fiyatı** - Rezervasyon, damga pulu kullanımını kapsar. Çalışanlara uygulanmaz, bu nedenle damga puluyla ilişkili diğer tüm kaynaklar ayrı olarak ücretlendirilir.

Windows sanal makineleri ve SQL veritabanı için [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/) ile lisanslama maliyetlerini de kapsama alabilirsiniz.

## <a name="whos-eligible-to-purchase-a-reservation"></a>Kimler rezervasyon satın almaya uygundur?

Bir plan satın almak için bir Kurumsal (MS-AZR-0017P veya MS-AZR-0148P) ya da Kullandıkça Öde aboneliğinde (MS-AZR-0003P veya MS-AZR-0023P) veya Microsoft Müşteri Sözleşmesi aboneliğinde abonelik sahibi rolünüz olmalıdır. Bulut çözümü sağlayıcıları, Azure Ayrılmış Sanal Makine Örnekleri satın almak için Azure portalını veya  [İş Ortağı Merkezi](/partner-center/azure-reservations) ’ni kullanabilir.

Kurumsal Anlaşma (EA) müşterileri, EA Portalındaki **Ayrılmış Örnekler Ekle** seçeneğini devre dışı bırakarak satın alma işlemlerini EA yöneticileriyle sınırlayabilir. EA yöneticilerinin bir rezervasyon satın almak için en az bir EA aboneliğinin abonelik sahibi olması gerekir. Bu seçenek, merkezi bir ekibin farklı maliyet merkezleri için rezervasyonlar satın almasını isteyen kuruluşlar için yararlıdır. Satın alma işleminden sonra merkezi ekipler, rezervasyonlara maliyet merkezi sahiplerini ekleyebilirler. Bundan sonra sahipler, rezervasyonu aboneliklerinin kapsamına alabilirler. Merkezi ekibin, rezervasyonun satın alındığı yerde abonelik sahibi erişimine sahip olması gerekmez.

Rezervasyon indirimi yalnızca Kurumsal, Bulut Çözümü Sağlayıcısı (CSP) ve Microsoft Müşteri Sözleşmesi ile satın alınan abonelikler ve kullandıkça öde tarifelerine sahip bireysel planlar ile ilişkili kaynaklar için geçerlidir.

## <a name="scope-reservations"></a>Rezervasyonları kapsama alma

Bir rezervasyonu bir abonelik veya kaynak gruplarının kapsamına alabilirsiniz. Bir rezervasyon kapsamının ayarlanması, ayırma tasarruflarının nereye uygulanacağını seçer. Rezervasyonu bir kaynak grubunun kapsamına aldığınızda rezervasyon indirimleri yalnızca kaynak grubu için geçerli olur — aboneliğin tamamı için değil.

### <a name="reservation-scoping-options"></a>Rezervasyon kapsamı belirleme seçenekleri

Kaynak grubu kapsamı ile, gereksinimlerinize bağlı olarak bir rezervasyonu kapsama almak için üç seçeneğiniz vardır:

- **Tek kaynak grubu kapsamı**: Yalnızca seçilen kaynak grubunda eşleşen kaynaklara rezervasyon indirimini uygular.
- **Tek abonelik kapsamı**: Yalnızca seçilen abonelikte eşleşen kaynaklara rezervasyon indirimini uygular.
- **Paylaşılan kapsam**: Faturalama bağlamında bulunan uygun aboneliklerdeki eşleşen kaynaklara rezervasyon indirimini uygular. Kurumsal Anlaşma müşterileri için faturalama bağlamı kayıttır. Microsoft Müşteri Sözleşmesi müşterileri için faturalama kapsamı, faturalama profilidir. Kullandıkça öde fiyatlarına tabi bireysel abonelikler için faturalama kapsamı, hesap yöneticisi tarafından oluşturulan tüm uygun aboneliklerdir.

Kullanımınıza rezervasyon indirimleri uygulanırken, Azure rezervasyonu aşağıdaki sırayla işler:

1. Kaynak grubu kapsamındaki rezervasyonlar
2. Tek kapsam rezervasyonları
3. Paylaşılan kapsam rezervasyonları

Tek bir kaynak grubu, rezervasyonların kapsamını nasıl belirlediğinize bağlı olarak birden çok rezervasyondan rezervasyon indirimleri alabilir.

### <a name="scope-a-reservation-to-a-resource-group"></a>Rezervasyonu bir kaynak grubunun kapsamına alma

Rezervasyonu satın alırken rezervasyonu kaynak grubunun kapsamına alabilir veya kapsamı satın alma sonrasında ayarlayabilirsiniz. Rezervasyonu bir kaynak grubunun kapsamına almak için abonelik sahibi olmanız gerekir.

Kapsamı ayarlamak için Azure portalındaki [Rezervasyon satın alın](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand) sayfasına gidin. Satın almak istediğiniz rezervasyon türünü seçin. **Satın almak istediğiniz ürünü seçin** seçim formunda Kapsam değerini Tek kaynak grubu olarak değiştirin. Ardından bir kaynak grubu seçin.

![VM rezervasyon satın alma seçimini gösteren örnek](./media/billing-save-compute-costs-reservations/select-product-to-purchase.png)

Sanal makine rezervasyonundaki kaynak grubuna yönelik satın alma önerileri gösterilir. Öneriler, son 30 gün boyunca kullanımınız analiz edilerek hesaplanır. Ayrılmış örneklerle kaynakları çalıştırmanın maliyeti kullandıkça öde tarifesine sahip kaynakları çalıştırmanın maliyetinden düşükse bir satın alma önerisi oluşturulur. Rezervasyon satın alma önerileri hakkında daha fazla bilgi için bkz. [Kullanım düzenine göre Ayrılmış Örnek satın alma önerileri alın](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations).

Rezervasyon satın aldıktan sonra her zaman kapsamı güncelleştirebilirsiniz. Bunu yapmak için rezervasyona gidin, **Yapılandırma**'ya tıklayın ve rezervasyon kapsamını yeniden belirleyin. Rezervasyon kapsamını yeniden belirlemek ticari bir işlem değildir. Rezervasyon süreniz değişmez. Kapsamı güncelleştirme hakkında daha fazla bilgi için bkz. [Rezervasyon satın aldıktan sonra kapsamı güncelleştirme](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

![Bir rezervasyon kapsamı değişikliğini gösteren örnek](./media/billing-save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>Rezervasyon kullanımını izleme ve iyileştirme

Rezervasyon kullanımınızı Azure portalı, API'ler veya kullanım verileri aracılığıyla birden çok şekilde izleyebilirsiniz. Erişiminiz olan tüm rezervasyonları görmek için Azure portalında **Rezervasyonlar**'a gidin. Rezervasyonlar kılavuzunda rezervasyon için en son kaydedilen kullanım yüzdesi gösterilir. Rezervasyonun uzun süreli kullanımını görmek için rezervasyona tıklayın.

Ayrıca, bir kurumsal anlaşma veya Microsoft Müşteri Sözleşmesi müşterisiyseniz [API’leri](billing-reservation-apis.md#see-reservation-usage) kullanarak veya [kullanım verilerinizden](billing-understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks) rezervasyon alabilirsiniz.

Kaynak grubu kapsamına aldığınız rezervasyonun kullanımın düşük olduğunu fark ederseniz, rezervasyon kapsamını tek bir aboneliğe güncelleştirebilir veya faturalandırma bağlamı genelinde paylaşabilirsiniz. Ayrıca rezervasyonu bölebilir ve elde edilen rezervasyonları farklı kaynak gruplarına uygulayabilirsiniz.

### <a name="other-considerations"></a>Diğer konular

Kaynak grubunda eşleşen kaynaklarınız yoksa, rezervasyon az kullanılır. Rezervasyon, kullanımın düşük olduğu farklı bir kaynak grubuna veya aboneliğe otomatik olarak uygulanmaz.

Kaynak grubunu bir abonelikten diğerine taşırsanız rezervasyon kapsamı otomatik olarak güncelleştirilmez. Kaynak grubunu silerseniz kapsam güncelleştirilmez. [Rezervasyon kapsamını yeniden belirlemeniz](billing-manage-reserved-vm-instance.md#change-the-reservation-scope) gerekecektir. Aksi takdirde, rezervasyon az kullanılır.

## <a name="discounted-subscription-and-offer-types"></a>İndirimli abonelik ve teklif türleri

Rezervasyon indirimleri aşağıdaki uygun aboneliklere ve teklif türlerine uygulanır.

- Kurumsal anlaşma (teklif numaraları: MS-AZR-0017P veya MS-AZR-0148P)
- Microsoft Müşteri Sözleşmesi abonelikleri.
- Kullandıkça öde ücretlerinin uygulandığı bireysel planlar (teklif numaraları: MS-AZR-0003P veya MS-AZR-0023P)
- CSP abonelikleri

Başka teklif türlerinin kullanıldığı bir abonelikte çalıştırılan kaynaklar rezervasyon indirimi almaz.

## <a name="how-is-a-reservation-billed"></a>Rezervasyon nasıl faturalandırılır?

Rezervasyon ücreti, aboneliğe bağlı ödeme yöntemiyle tahsil edilir. Rezervasyon maliyeti, varsa parasal taahhüt bakiyenizden düşülür. Parasal taahhüt bakiyeniz rezervasyon maliyetini kapsamadığında fazla kullanım üzerinden faturalandırılırsınız. Kullandıkça öde tarifesine sahip tek bir plandan aboneliğiniz varsa, hesabınızda bulunan kredi kartından peşin alma işlemleri için hemen ücret alınır. Aylık ödemeler faturanızda görünür ve kredi kartınızdan aylık olarak ücret alınır. Fatura ile ücret aldığınızda ücretleri bir sonraki faturanızda görürsünüz.

## <a name="how-reservation-discount-is-applied"></a>Rezervasyon indiriminin uygulanması

Rezervasyon indirimi, rezervasyonu satın alırken seçtiğiniz özniteliklerle eşleşen kaynak kullanımına uygulanır. Öznitelikler, eşleşen VM'lerin, SQL veritabanlarının, Azure Cosmos DB veya diğer kaynakların çalıştırıldığı kapsamı içerir. Örneğin, Batı ABD bölgesinde dört Standart D2 sanal makinesi için bir rezervasyon indirimi istiyorsanız, VM'lerin çalıştığı aboneliği seçin.

Rezervasyon indirimi “*kullanılmadığı takdirde hakkı kaybedilen*” bir özelliktir. Herhangi bir saat için eşleşen kaynaklarınız yoksa, o saate ait rezervasyon miktarını kaybedersiniz. Kullanılmayan ayrılmış saatleri devredemezsiniz.

Bir kaynağı kapattığınızda rezervasyon indirimi, belirtilen kapsamdaki başka bir eşleşen kaynağa otomatik olarak uygulanır. Belirtilen kapsamda eşleşen kaynak bulunamazsa, ayrılan saatler *kaybedilir*.

Örneğin, daha sonra bir kaynak oluşturabilirsiniz ve az kullanılan bir eşleşen rezervasyonunuz olabilir. Rezervasyon indirimi yeni eşleşen kaynağa otomatik olarak uygulanır.

Sanal makineler, kaydınız/hesabınız içinde farklı aboneliklerde çalışıyorsa, kapsamı paylaşılan olarak seçin. Paylaşılan kapsam, rezervasyon indiriminin abonelikler arasında uygulanmasını sağlar. Bir rezervasyonu satın aldıktan sonra kapsamı değiştirebilirsiniz. Daha fazla bilgi için bkz. [Azure Ayrılmış Sanal Makine Örneklerini yönetme](billing-manage-reserved-vm-instance.md).

Rezervasyon indirimi yalnızca, Kurumsal, Microsoft Müşteri Sözleşmesi, CSP veya kullandıkça öde tarifesine sahip aboneliklerle ilişkili kaynaklar için geçerlidir. Başka teklif türlerinin kullanıldığı bir abonelikte çalıştırılan kaynaklar rezervasyon indirimi almaz.

## <a name="when-the-reservation-term-expires"></a>Rezervasyon dönemi sona erdiğinde

Rezervasyon döneminin sonunda fatura indiriminin süresi dolar ve kaynaklar kullandıkça öde fiyatıyla faturalanır. Varsayılan olarak rezervasyonlar otomatik olarak yenilenecek şekilde ayarlanmaz. Yenileme ayarlarında ilgili seçeneği belirterek rezervasyonda otomatik yenilemeyi etkinleştirmeyi seçebilirsiniz. Otomatik yenilemeyle, mevcut rezervasyonun süresi dolduğunda onun yerine bir rezervasyon satın alınır. Varsayılan olarak yeni satın alınan rezervasyonun öznitelikleri süresi dolan rezervasyonla aynı olur; isterseniz yenileme ayarlarında faturalama sıklığını, süreyi veya miktarı değiştirebilirsiniz. Rezervasyon üzerinde ve faturalama için kullanılan abonelik üzerinde sahip erişimi olan tüm kullanıcılar yenilemeyi ayarlayabilir.  

## <a name="discount-applies-to-different-sizes"></a>Farklı boyutlar için indirim uygulanır

Bir rezervasyon satın aldığınızda, indirim aynı boyut grubundaki özniteliklere sahip diğer örneklere uygulanabilir. Bu özellik örnek boyutu esnekliği olarak bilinir. İndirim kapsamının esnekliği, rezervasyon türüne ve rezervasyonu satın alırken seçtiğiniz özniteliklere göre değişir.

Hizmet planları:

- Ayrılmış VM Örnekleri: Rezervasyonu satın alırken **Örnek boyutu esnekliği için iyileştirilmiş**'i seçerseniz, indirim kapsamı seçtiğiniz VM boyutuna bağlı olur. Rezervasyon aynı boyut serisi grubundaki sanal makine (VM) boyutlarına uygulanır. Daha fazla bilgi için bkz. [Ayrılmış VM Örnekleriyle sanal makine boyutu esnekliği](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Azure Depolama ayrılmış kapasitesi: Standart Azure Depolama hesapları için aylık 100 TiB veya 1 PiB boyutlu birimler halinde ayrılmış kapasite satın alabilirsiniz. Azure Depolama ayrılmış kapasitesi tüm bölgeler, tüm erişim katmanları (sık erişimli, seyrek erişimli veya arşiv) ve tüm çoğaltma seçenekleri (LRS, GRS veya ZRS) ile kullanılabilir.
- SQL Veritabanı ayrılmış kapasitesi: İndirim kapsamı, seçtiğiniz performans katmanına bağlıdır. Daha fazla bilgi için bkz. [Azure rezervasyonu indiriminin nasıl uygulandığını anlama](billing-understand-reservation-charges.md).
- Azure Cosmos DB ayrılmış kapasitesi: İndirim kapsamı, sağlanan aktarım hızına göre değişir. Daha fazla bilgi için bkz. [Azure Cosmos DB rezervasyon indiriminin nasıl uygulandığını anlama](billing-understand-cosmosdb-reservation-charges.md).

## <a name="reservation-notifications"></a>Rezervasyon bildirimleri

Azure aboneliğiniz için nasıl ödeme yaptığınıza bağlı olarak, kuruluşunuzdaki aşağıdaki kullanıcılara rezervasyon bildirimlerini e-posta ile göndeririz. Bildirimleri aşağıdakiler dahil çeşitli olaylar için gönderilir:

- Satın Al
- Yaklaşan rezervasyon süre sonu
- Süre sonu
- Yenileme
- İptal
- Kapsam değişikliği

EA abonelikleri olan müşteriler için:
- Satın alma bildirimi, satın alan kişiye ve EA bildirimi kişilerine gönderilir.
- Diğer rezervasyon yaşam döngüsü bildirimleri yalnızca EA bildirim kişilerine gönderilir.
- RBAC (IAM) izni kullanılarak rezervasyona eklenen kullanıcılar herhangi bir e-posta bildirimi almaz.

Bireysel abonelikleri olan müşteriler için:
- Satın alan kişi bir satın alma bildirimi alır.
- Satın alma sırasında, abonelik faturalama hesabı sahibi bir satın alma bildirimi alır.
- Hesap sahibi diğer tüm bildirimleri alır.


## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

- Aşağıdaki makalelerle Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinin:
    - [Azure Ayırmalarını yönetme](billing-manage-reserved-vm-instance.md)
    - [Kullandıkça öde fiyatlarına tabi aboneliğiniz için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage.md)
    - [Kurumsal kaydınız için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage-ea.md)
    - [Rezervasyonlara dahil olmayan Windows yazılımı maliyetleri](billing-reserved-instance-windows-software-costs.md)
    - [İş Ortağı Merkezi Bulut Çözümü Sağlayıcısı (CSP) programındaki Azure Ayrılmış Sanal Makine Örnekleri](/partner-center/azure-reservations)

- Hizmet planları için rezervasyonlar hakkında daha fazla bilgi edinin:
    - [Azure Ayrılmış VM Örnekleri ile Sanal Makineler](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Azure Cosmos DB ayrılmış kapasitesine sahip Azure Cosmos DB kaynakları](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Azure SQL Veritabanı ayrılmış kapasitesine sahip SQL Veritabanı işlem kaynakları](../sql-database/sql-database-reserved-capacity.md) Yazılım planlarına yönelik rezervasyonlar hakkında daha fazla bilgi edinin:
    - [Azure Ayrılmış Sanal Makine Örneklerinden Red Hat yazılım planları](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Azure Ayrılmış Sanal Makine Örneklerinden SESU yazılım planları](../virtual-machines/linux/prepay-suse-software-charges.md)
