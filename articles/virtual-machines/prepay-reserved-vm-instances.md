---
title: Azure sanal makineleri için para tasarrufu sağlamak üzere ön ödeme
description: Azure ayrılmış sanal makine örneklerini, işlem maliyetlerinizi kaydetmek için nasıl satın alacağınızı öğrenin.
author: vikramdesai01
manager: vikramdesai01
ms.service: virtual-machines
ms.subservice: reserved-instances
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/30/2017
ms.author: vikdesai
ms.openlocfilehash: 7eab410bb2344617e555a7a9d1d44cd9adcc676d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674724"
---
# <a name="save-costs-with-azure-reserved-vm-instances"></a>Azure ayrılmış VM örnekleri ile maliyetleri kaydetme


Bir Azure ayrılmış VM örneğine kaydetme yaptığınızda para tasarrufu yapabilirsiniz. Ayırma indirimi, ayırma kapsamı ve öznitelikleriyle eşleşen çalışan sanal makine sayısına otomatik olarak uygulanır. İndirimleri almak için bir sanal makineye bir ayırma atamanız gerekmez. Ayrılmış örnek satın alma yalnızca VM kullanımınızın işlem parçasını içerir. Windows VM 'Leri için kullanım ölçümü iki ayrı ölçü olarak bölünür. Linux ölçümü ve Windows IP ölçümü ile aynı olan bir işlem ölçümü vardır. Satın almayı yaparken gördüğünüz ücretler yalnızca işlem maliyetlerine yöneliktir. Ücretler Windows Yazılım maliyetlerini içermez. Yazılım maliyetleri hakkında daha fazla bilgi için bkz. [Azure ayrılmış VM örneklerine yazılım maliyetleri dahil değildir](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Satın almadan önce doğru VM boyutunu belirleme

Bir ayırma satın almadan önce, ihtiyacınız olan sanal makinenin boyutunu belirlemeniz gerekir. Aşağıdaki bölümler, doğru VM boyutunu belirlemenize yardımcı olur.

### <a name="use-reservation-recommendations"></a>Ayırma önerilerini kullanma

Satın almanız gereken rezervasyonları belirlemede yardımcı olması için rezervasyon önerilerini kullanabilirsiniz.

- Satın alma önerileri ve Önerilen miktarlar, Azure portal bir VM ayrılmış örneği satın aldığınızda gösterilir.
- Azure Advisor bireysel abonelikler için satın alma önerileri sağlar.  
- Hem paylaşılan kapsam hem de tek abonelik kapsamı için satın alma önerileri almak üzere API 'Leri kullanabilirsiniz. Daha fazla bilgi için bkz. [Kurumsal müşterilere yönelik ayrılmış örnek satın alma önerisi API 'leri](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Kurumsal Anlaşma (EA) ve Microsoft Müşteri Sözleşmesi (MCA) müşterileri için, [Azure Consumption Insights Power BI içerik paketi](/power-bi/service-connect-to-azure-consumption-insights)ile paylaşılan ve tek abonelik kapsamlarına yönelik satın alma önerileri sunulmaktadır.

### <a name="services-that-get-vm-reservation-discounts"></a>VM rezervasyon indirimlerini alan hizmetler

VM rezervasyonlarınız yalnızca VM dağıtımlarınıza değil çeşitli hizmetlerden çıkan VM kullanımına da uygulanabilir. Rezervasyon indirimi alan kaynaklar örnek boyutu esnekliği ayarına bağlı olarak değişir.

#### <a name="instance-size-flexibility-setting"></a>Örnek boyut esnekliği ayarı

Örnek boyutu esnekliği ayarı, ayrılmış örnek indirimlerini hangi hizmetlerin alacağını belirler.

*ConsumedService*`Microsoft.Compute` olduğunda, ayarın açık veya kapalı olmasına bakılmaksızın eşleşen her VM kullanımına rezervasyon indirimleri uygulanır. Dolayısıyla kullanım verilerinizde *ConsumedService* değerini gözden geçirin. Bazı örnekler:

- Sanal makineler
- Sanal makine ölçek kümeleri
- Kapsayıcı hizmeti
- Azure Batch dağıtımları (kullanıcı abonelikleri modunda)
- Azure Kubernetes Hizmeti (AKS)
- Service Fabric

Ayar açık olduğunda, eşleşen her VM kullanımına rezervasyon indirimlerinin uygulanması için *ConsumedService* aşağıdaki öğelerden biri olmalıdır:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Kullanımın rezervasyon indirimlerine uygun olup olmadığını saptamak için kullanım verilerinizde *ConsumedService* değerini gözden geçirin.

Örnek boyutu esnekliği hakkında daha fazla bilgi için bkz. [Ayrılmış VM Örnekleriyle sanal makine boyutu esnekliği](reserved-vm-instance-size-flexibility.md).

### <a name="analyze-your-usage-information"></a>Kullanım bilgilerinizi çözümleyin

Satın almanız gereken rezervasyonları belirlemede yardımcı olması için kullanım bilgilerinizi çözümleyin. Kullanım verileri, kullanım dosyası ve API 'lerde kullanılabilir. Hangi ayırmayı satın almak istediğinizi öğrenmek için bunları birlikte kullanın. Satın alınacak rezervasyonların miktarını öğrenmek için günlük olarak yüksek kullanıma sahip sanal makine örneklerini denetleyin. `Meter`Kullanım verilerinde alt kategori ve alanları kullanmaktan kaçının `Product` . Premium depolama kullanan VM boyutları arasında ayrım yapmazlar. Bu alanları, rezervasyon satın alma için VM boyutunu tespit etmek üzere kullanırsanız, yanlış boyut satın alabilirsiniz. Daha sonra, beklediğinizi rezervasyon indirimi almazsınız. Bunun yerine, `AdditionalInfo` doğru VM boyutunu öğrenmek için kullanım dosyanızda veya kullanım API 'sindeki alana bakın.

Kullanım dosyanız, faturalandırma dönemine ve günlük kullanıma göre ücretlerinizi gösterir. Kullanım dosyanızı indirme hakkında daha fazla bilgi için bkz. [Azure kullanımınızı ve ücretlerinizi görüntüleme ve indirme](../cost-management-billing/understand/download-azure-daily-usage.md). Ardından, kullanım dosyası bilgilerini kullanarak [hangi ayırmayı satın almayı belirleyebilirsiniz](../cost-management-billing/reservations/determine-reservation-purchase.md).

### <a name="purchase-restriction-considerations"></a>Satın alma kısıtlaması konuları

Ayrılmış VM örnekleri, bazı özel durumlarla birçok VM boyutu için kullanılabilir. Aşağıdaki VM 'Ler için rezervasyon iskontoları uygulanmaz:

- **VM Serisi** -A serisi, AV2 serisi veya G serisi.

- **Önizleme veya promosyon VM 'leri** -önizlemede olan veya promosyon ölçümü kullanan tüm VM serisi veya boyutları.

- **Bulutlar** -rezervasyonlar Almanya veya Çin bölgelerinde satın alınabilir.

- **Yetersiz kota** -tek bir abonelik kapsamındaki bir ayırma, yeni RI Için abonelikte vCPU kotası kullanılabilir olmalıdır. Örneğin, hedef aboneliğin D serisi için 10 vCPU kota sınırı varsa, 11 Standard_D1 örnekleri için bir ayırma satın alamazsınız. Rezervasyonların kota denetimi abonelikte zaten dağıtılan VM 'Leri içerir. Örneğin, aboneliğin D serisi için 10 vCPU kotası varsa ve dağıtılan iki standard_D1 örneği varsa, bu abonelikte 10 standard_D1 örneği için bir ayırma satın alabilirsiniz. Bu sorunu çözmek için [teklif artışı isteği oluşturabilirsiniz](../azure-portal/supportability/resource-manager-core-quotas-request.md) .

- **Kapasite kısıtlamaları** -nadir koşullarda Azure, bir bölgedeki düşük KAPASITE nedeniyle VM boyutlarının alt kümesi için yeni rezervasyonların satın alınmasını sınırlandırır.

## <a name="buy-a-reserved-vm-instance"></a>Ayrılmış VM Örneği Satın Alma

[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)ayrılmış bir sanal makine örneği satın alabilirsiniz. [Peşin olarak veya aylık ödemelerle](../cost-management-billing/reservations/prepare-buy-reservation.md) rezervasyon ödemesi yapın.
Bu gereksinimler ayrılmış bir sanal makine örneği satın almak için geçerlidir:

- En az bir EA aboneliği veya Kullandıkça Öde tarifesine sahip bir abonelik için sahip rolünde olmanız gerekir.
- EA abonelikleri için, **ayrılmış örnekler Ekle** seçeneğinin [EA portalında](https://ea.azure.com/)etkinleştirilmiş olması gerekir. Veya, bu ayar devre dışı bırakıldıysa, aboneliğin EA Yöneticisi olmanız gerekir.
- Bulut çözümü sağlayıcısı (CSP) programı için yalnızca yönetici aracıları veya satış aracıları ayırmaları satın alabilir.

Örnek satın almak için:

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. **Tüm hizmetler** > **Rezervasyonlar**’ı seçin.
1. Yeni rezervasyon satın almak için **Ekle**'yi seçin ve ardından **Sanal makine**'ye tıklayın.
1. Gerekli alanları doldurun. Seçtiğiniz özniteliklerle eşleşen VM örneklerinin çalıştırılması, rezervasyon indirimi almaya uygundur. İndirim alan VM örneklerinizin gerçek sayısı, seçilen kapsama ve miktara bağlıdır.

EA sözleşmeniz varsa, daha fazla örnek eklemek için **daha fazla ek ekle seçeneğini** kullanabilirsiniz. Bu seçenek diğer abonelik türleri için kullanılamaz.


| Alan      | Açıklama|
|------------|--------------|
|Abonelik|Ayırma için ödeme yapmak üzere kullanılan abonelik. Rezervasyon maliyetleri, aboneliğin ödeme yöntemiyle ücretlendirilir. Abonelik türü bir kurumsal anlaşma (teklif numaraları: MS-AZR-0017P veya MS-AZR-0148P) veya Microsoft Müşteri Sözleşmesi ya da Kullandıkça Öde tarifesine sahip tek bir abonelik olmalıdır (teklif numaraları: MS-AZR-0003P veya MS-AZR-0023P). Ücretler, Azure Ön Ödemesi (eski adıyla parasal taahhüt) bakiyesinden düşülür veya varsa fazla kullanım olarak ücretlendirilir. Kullandıkça Öde tarifesine sahip bir abonelik için ücretler, abonelik üzerindeki kredi kartına veya fatura ödeme yöntemine faturalandırılır.|    
|Kapsam       |Ayırma kapsamı bir aboneliği veya birden çok aboneliği (paylaşılan kapsamı) kapsayabilir. Şunları seçerseniz: <ul><li>**Tek kaynak grubu kapsamı**: Yalnızca seçilen kaynak grubunda eşleşen kaynaklara rezervasyon indirimini uygular.</li><li>**Tek abonelik kapsamı**: Yalnızca seçilen abonelikte eşleşen kaynaklara rezervasyon indirimini uygular.</li><li>**Paylaşılan kapsam**: Faturalama bağlamında bulunan uygun aboneliklerdeki eşleşen kaynaklara rezervasyon indirimini uygular. EA müşterileri için, faturalandırma bağlamı kayıt olur. Kullandıkça öde fiyatlarına tabi bireysel abonelikler için faturalama kapsamı, hesap yöneticisi tarafından oluşturulan tüm uygun aboneliklerdir.</li></ul>|
|Region    |Ayırma kapsamındaki Azure bölgesi.|    
|VM Boyutu     |Sanal makine örneklerinin boyutu.|
|İçin iyileştirin     |VM örnek boyutu esnekliği varsayılan olarak seçilidir. Aynı [VM Boyut grubundaki](reserved-vm-instance-size-flexibility.md)diğer VM 'lere rezervasyon iskontosunu uygulamak için örnek boyutu esneklik değerini değiştirmek için **Gelişmiş ayarlar** ' a tıklayın. Kapasite önceliği dağıtımlarınızda veri merkezi kapasitesine öncelik verir. İhtiyaç duyduğunuzda sanal makine örneklerini başlatma yeteneizin daha fazla güvenilirlik sağlar. Kapasite önceliği yalnızca rezervasyon kapsamı tek abonelik olduğunda kullanılabilir. |
|Süre        |Bir yıl veya üç yıl. Ayrıca, yalnızca HBv2 VM 'Leri için kullanılabilir 5 yıllık bir terim vardır.|
|Miktar    |Ayırma içinde satın alınan örneklerin sayısı. Miktar, fatura iskontosunu elde eden çalışan sanal makine örneklerinin sayısıdır. Örneğin, Doğu ABD 10 Standard_D2 VM çalıştırıyorsanız, çalışan tüm VM 'Lerin avantajını en üst düzeye çıkarmak için miktarı 10 olarak belirtirsiniz. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Kullanım verileri ve ayırma kullanımı

Kullanım verileriniz, kullanım için sıfır geçerli fiyata sahip olup rezervasyon indirimi alır. Her rezervasyon için rezervasyon iskontosunu hangi VM örneğinin aldığını görebilirsiniz.

Kullanım verilerinde rezervasyon iskontolarının nasıl göründüğü hakkında daha fazla bilgi için bkz. EA müşterisiyseniz [Kurumsal kaydınız Için Azure ayırma kullanımını anlayın](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) . Tek bir aboneliğiniz varsa, bkz. [Kullandıkça Öde aboneliğiniz Için Azure ayırma kullanımını anlayın](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Satın alma işleminden sonra rezervasyonu değiştirme

Satın alma işleminden sonra bir rezervasyon üzerinde aşağıdaki değişiklikleri yapabilirsiniz:

- Rezervasyon kapsamını güncelleştirme
- Örnek boyutu esnekliği (varsa)
- Sahiplik

Ayrıca, bir ayırmayı daha küçük parçalara bölebilir ve zaten bölünmüş rezervasyonları birleştirebilirsiniz. Değişikliklerden hiçbiri yeni bir ticari işleme neden olmaz ya da ayırmanın bitiş tarihini değiştirmez.

Satın alma işleminden sonra aşağıdaki değişiklik türlerini yapamazsınız:

- Mevcut bir ayırmanın bölgesi
- SKU
- Miktar
- Süre

Ancak, değişiklik yapmak istiyorsanız bir rezervasyon *alışverişi* yapabilirsiniz.

## <a name="cancel-exchange-or-refund-reservations"></a>Rezervasyonları iptal etme, değiştirme veya para iadesi alma

Belirli sınırlamalarla rezervasyonları iptal edebilir, değiştirebilir veya para iadesi alabilirsiniz. Daha fazla bilgi için bkz. [Azure Ayrılmış Sanal Makine Örnekleri için self servis değişimler ve para iadeleri](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Sonraki adımlar

- Rezervasyonu yönetme hakkında bilgi edinmek için bkz. [Azure Ayrılmış Sanal Makine Örnekleri’ni Yönetme](../cost-management-billing/reservations/manage-reserved-vm-instance.md).
- Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
    - [Azure Ayrılmış Sanal Makine Örnekleri nedir?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [Azure’da Rezervasyonları Yönetme](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [Rezervasyon indiriminin nasıl uygulandığını anlama](../cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [Kullandıkça öde fiyatlarına tabi bir abonelik için rezervasyon kullanımını anlama](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [Kurumsal kaydınız için rezervasyon kullanımını anlama](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [Rezervasyonlara dahil olmayan Windows yazılımı maliyetleri](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [İş Ortağı Merkezi Bulut Çözümü Sağlayıcısı (CSP) programındaki Azure Ayrılmış Sanal Makine Örnekleri](/partner-center/azure-reservations)