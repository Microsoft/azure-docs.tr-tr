---
title: Azure VMware çözümünün ayrılmış örnekleri
description: Azure VMware çözümü için ayrılmış bir örnek satın almayı öğrenin. Ayrılmış örnek yalnızca kullanımınızın işlem parçasını kapsar ve yazılım lisanslama maliyetlerini içerir.
ms.topic: how-to
ms.date: 04/09/2021
ms.openlocfilehash: 6f181d4b6ee7e2098130633fef0eb8558add0308
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210033"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Azure VMware çözümü ile maliyetleri kaydetme

[Azure VMware çözümünün](introduction.md)ayrılmış bir örneğine kayıt yaptığınızda para tasarrufu yapın.  Rezervasyon indirimi, ayırma kapsamı ve öznitelikleriyle eşleşen çalışan Azure VMware Çözüm konaklarına otomatik olarak uygulanır. Ayrılmış örnek satın alma yalnızca kullanımınızın işlem parçasını kapsar ve yazılım lisanslama maliyetlerini içerir. 

## <a name="purchase-restriction-considerations"></a>Satın alma kısıtlaması konuları

Ayrılmış örnekler bazı özel durumlarla birlikte kullanılabilir.

-   **Bulutlar** -ayırmalar yalnızca [bölgeye göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) sayfasında listelenen bölgelerde kullanılabilir.

-   **Yetersiz kota** -tek/paylaşılan bir aboneliğe kapsamlı bir ayırma, yeni ayrılmış örnek için abonelikte kullanılabilir Konaklar kotasına sahip olmalıdır. Bu sorunu çözmek için [Kota artışı isteği oluşturabilirsiniz](enable-azure-vmware-solution.md) .

-   **Teklif uygunluğu**-Microsoft Ile bir [Azure Kurumsal Anlaşma (EA)](../cost-management-billing/manage/ea-portal-agreements.md) gerekir.

-   **Kapasite kısıtlamaları** -nadir koşullarda Azure, bir bölgedeki düşük kapasiteden dolayı Azure VMware Çözüm Konağı SKU 'ları için yeni rezervasyonların satın alınmasını sınırlandırır.

## <a name="buy-a-reservation"></a>Ayırma satın alma

Azure VMware Çözüm ana bilgisayar örneğinin ayrılmış bir örneğini [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)satın alabilirsiniz.

Rezervasyon için [veya aylık ödemeler ile](../cost-management-billing/reservations/prepare-buy-reservation.md)ödeme yapabilirsiniz.

Bu gereksinimler ayrılmış bir ayrılmış ana bilgisayar örneği satın almak için geçerlidir:

-   En az bir EA aboneliği veya Kullandıkça Öde tarifesine sahip bir abonelik için *sahip* rolünde olmanız gerekir.

-   EA abonelikleri için [EA portalındaki](https://ea.azure.com/) **ayrılmış örnekleri Ekle** seçeneğini etkinleştirmeniz gerekir. Devre dışı bırakılırsa, aboneliğin etkinleştirilmesi için bir EA yöneticisi olmanız gerekir.

-   Bulut çözümü sağlayıcısı (CSP) Azure planı kapsamındaki abonelik için iş ortağı, ayrılmış örnekleri müşterinin Azure portal satın almalıdır. 

### <a name="buy-reserved-instances-for-an-ea-subscription"></a>EA aboneliği için ayrılmış örnekler satın alın

1. [Azure Portal](https://portal.azure.com/) oturum açın.

2. **Tüm hizmetler** > **Rezervasyonlar**’ı seçin.

3. **Şimdi satın al** ' ı seçin ve ardından **Azure VMware çözümünü** seçin.

4. Gerekli alanları girin. Çalışan Azure VMware Çözüm ana bilgisayarları ile eşleşen seçili öznitelikler, rezervasyon indirimine hak kazanın.  Öznitelikler SKU, bölgeler (uygun olduğunda) ve kapsamdan oluşur. Rezervasyon tasarruflarının nereye uygulanacağını rezervasyon kapsamı belirler.

   EA sözleşmeniz varsa, örnekleri hızlıca eklemek için **daha fazla Ekle seçeneğini** kullanabilirsiniz. Bu seçenek diğer abonelik türleri için kullanılamaz.

   | Alan        |  Açıklama |
   | ------------ | ------------ |
   | Abonelik | Ayırma için ödeme yapmak üzere kullanılan abonelik. Rezervasyon maliyetleri, aboneliğin ödeme yöntemiyle ücretlendirilir. Abonelik türü bir kurumsal anlaşma (teklif numaraları: MS-AZR-0017P veya MS-AZR-0148P), Microsoft Müşteri Sözleşmesi veya Kullandıkça Öde tarifesine sahip bireysel bir abonelik olmalıdır (teklif numaraları: MS-AZR-0003P veya MS-AZR-0023P). Ücretler, Azure Ön Ödemesi (eski adıyla parasal taahhüt) bakiyesinden düşülür veya varsa fazla kullanım olarak ücretlendirilir. Kullandıkça Öde tarifesine sahip bir abonelik için ücretler, aboneliğin kredi kartına veya fatura ödeme yöntemine faturalandırılır. |
   | Kapsam        | Ayırma kapsamı bir aboneliği veya birden çok aboneliği (paylaşılan kapsamı) kapsayabilir. Şunları seçerseniz:<br><ul><li><b>Tek kaynak grubu kapsamı</b> -rezervasyon iskontosunu yalnızca seçili kaynak grubundaki eşleşen kaynaklara uygular.</li><li><b>Tek abonelik kapsamı</b> -rezervasyon iskontosunu seçili abonelikteki eşleşen kaynaklara uygular.</li><li><b>Paylaşılan kapsam</b> -fatura bağlamındaki uygun aboneliklerde bulunan kaynaklara karşılık gelen rezervasyon iskontosunu uygular. EA müşterileri için, faturalandırma bağlamı kayıt olur. Kullandıkça öde fiyatlarına tabi bireysel abonelikler için faturalama kapsamı, hesap yöneticisi tarafından oluşturulan tüm uygun aboneliklerdir.</li></ul>       |
   | Bölge       | Ayırma kapsamındaki Azure bölgesi.   |
   | Ana bilgisayar boyutu    | AV36    |
   | Süre         | Bir yıl veya üç yıl.  |
   | Miktar     | Ayırma içinde satın alınabilecek örneklerin sayısı. Bu miktar, fatura iskontosunu elde eden, çalışan Azure VMware Çözüm ana bilgisayarlarının sayısıdır.    |

### <a name="buy-reserved-instances-for-a-csp-subscription"></a>CSP aboneliği için ayrılmış örnekler satın alın

Müşterileri için ayrılmış örnekler satın almak isteyen CSP 'Ler [Iş Ortağı Merkezi belgelerinden](/partner-center/azure-plan-manage) **yönetici adına** (Aobo) yordamını kullanmalıdır. Daha fazla bilgi için [yönetici adına (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) videoyu görüntüleyin.

1. [Iş Ortağı Merkezi](https://partner.microsoft.com)' nde oturum açın.

2. **Müşteriler** alanına erişmek için **CSP** 'yi seçin.

3. Müşteri ayrıntıları ' nı genişletin ve **Microsoft Azure yönetim portalı**' yi seçin. 

   :::image type="content" source="media/reserved-instances/csp-partner-center-aobo.png" alt-text="Microsoft Iş ortağı merkezi müşterileri alanı" lightbox="media/reserved-instances/csp-partner-center-aobo.png":::

4. Azure Portal, **tüm hizmetler**  >  **ayırmaları**' ni seçin.

5. **Şimdi satın al** ' ı seçin ve ardından **Azure VMware çözümünü** seçin.

   :::image type="content" source="media/reserved-instances/csp-buy-ri-azure-portal.png" alt-text="Microsoft Azure portal ayırmalar" lightbox="media/reserved-instances/csp-buy-ri-azure-portal.png":::

6. Gerekli alanları girin. Çalışan Azure VMware Çözüm ana bilgisayarları ile eşleşen seçili öznitelikler, rezervasyon indirimine hak kazanın.  Öznitelikler SKU, bölgeler (uygun olduğunda) ve kapsamdan oluşur. Rezervasyon tasarruflarının nereye uygulanacağını rezervasyon kapsamı belirler.

   | Alan        |  Açıklama |
   | ------------ | ------------ |
   | Abonelik | Ayırma için ödeme yapmak üzere kullanılan abonelik. Rezervasyon maliyetleri, aboneliğin ödeme yöntemiyle ücretlendirilir. Abonelik türü uygun bir değer olmalıdır, bu durumda bir CSP aboneliği vardır|
   | Kapsam        | Ayırma kapsamı bir aboneliği veya birden çok aboneliği (paylaşılan kapsamı) kapsayabilir. Şunları seçerseniz:<br><ul><li><b>Tek kaynak grubu kapsamı</b> -rezervasyon iskontosunu yalnızca seçili kaynak grubundaki eşleşen kaynaklara uygular.</li><li><b>Tek abonelik kapsamı</b> -rezervasyon iskontosunu seçili abonelikteki eşleşen kaynaklara uygular.</li><li><b>Paylaşılan kapsam</b> -fatura bağlamındaki uygun aboneliklerde bulunan kaynaklara karşılık gelen rezervasyon iskontosunu uygular. EA müşterileri için, faturalandırma bağlamı kayıt olur. Kullandıkça öde fiyatlarına tabi bireysel abonelikler için faturalama kapsamı, hesap yöneticisi tarafından oluşturulan tüm uygun aboneliklerdir.</li></ul>       |
   | Bölge       | Ayırma kapsamındaki Azure bölgesi.   |
   | Ana bilgisayar boyutu    | AV36    |
   | Süre         | Bir yıl veya üç yıl.  |
   | Miktar     | Ayırma içinde satın alınabilecek örneklerin sayısı. Bu miktar, fatura iskontosunu elde eden, çalışan Azure VMware Çözüm ana bilgisayarlarının sayısıdır.     |

Müşteriniz için satın alınan rezervasyonları görüntüleme hakkında daha fazla bilgi edinmek için bkz. [Azure ayırmalarını bulut çözümü sağlayıcısı (CSP) olarak görüntüleme](../cost-management-billing/reservations/how-to-view-csp-reservations.md) .

## <a name="usage-data-and-reservation-usage"></a>Kullanım verileri ve ayırma kullanımı

Rezervasyon indirimi alan kullanımınızın etkin bir fiyatı vardır. Hangi Azure VMware Çözüm örneğinin her rezervasyon için rezervasyon iskontosunu aldığını görebilirsiniz.

Kullanım verilerinde rezervasyon iskontolarının nasıl göründüğü hakkında daha fazla bilgi için:

- EA müşterileri için bkz. [Kurumsal kaydınız Için Azure ayırma kullanımını anlama](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- Bireysel abonelikler için bkz. [Kullandıkça Öde aboneliğiniz Için Azure rezervasyon kullanımını anlama](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="change-a-reservation-after-purchase"></a>Satın alma işleminden sonra rezervasyonu değiştirme

Satın alma işleminden sonra bu değişiklikleri bir rezervasyona yapabilirsiniz:

-   Rezervasyon kapsamını güncelleştirme

-   Örnek boyutu esnekliği (varsa)

-   Sahiplik

Ayrıca, bir ayırmayı daha küçük parçalara bölebilir veya rezervasyonları birleştirebilirsiniz. Değişikliklerden hiçbiri yeni bir ticari işleme neden olmaz ya da ayırmanın bitiş tarihini değiştirmez.

CSP ile yönetilen rezervasyonlar hakkında daha fazla bilgi için bkz. [Iş Ortağı Merkezi, Azure Portal veya API 'leri kullanarak müşterilere Microsoft Azure ayırmaları satma](/partner-center/azure-reservations).



>[!NOTE]
>Ayırmayı satın aldıktan sonra, bu tür değişiklikleri doğrudan yapamazsınız:
>
> - Mevcut bir ayırmanın bölgesi
> - SKU
> - Miktar
> - Süre
>
>Ancak, değişiklik yapmak istiyorsanız bir rezervasyon *alışverişi* yapabilirsiniz.

## <a name="cancel-exchange-or-refund-reservations"></a>Rezervasyonları iptal etme, değiştirme veya para iadesi alma

Belirli sınırlamalarla rezervasyonları iptal edebilir, değiştirebilir veya para iadesi alabilirsiniz. Daha fazla bilgi için bkz. [Azure Ayrılmış Sanal Makine Örnekleri için self servis değişimler ve para iadeleri](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

CSP 'Ler, müşterileri için satın alınan belirli sınırlamalar sayesinde rezervasyonları iptal edebilir, değiştirebilir veya iade edebilir. Daha fazla bilgi için bkz. [müşterilere yönelik ayırmaları yönetme, iptal etme, Exchange veya para iadesi Microsoft Azure](/partner-center/azure-reservations-manage).

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure VMware çözümünün ayrılmış örneğini kapsadığınıza göre, şunları öğrenmek isteyebilirsiniz:

- [Azure VMware Çözüm değerlendirmesi oluşturma](../migrate/how-to-create-azure-vmware-solution-assessment.md).
- [Azure VMware çözümü IÇIN DHCP 'Yi yönetme](manage-dhcp.md).
- [Azure VMware Çözüm VM 'Lerinin yaşam döngüsü yönetimi](lifecycle-management-of-azure-vmware-solution-vms.md).
 