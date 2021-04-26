---
title: Bulut çözümü sağlayıcısı-Microsoft ticari Market
description: Tekliflerinizi ticari Market 'teki Microsoft Bulut çözüm sağlayıcısı (CSP) program iş ortağı kanalı aracılığıyla satma hakkında bilgi edinin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 07/14/2020
ms.openlocfilehash: c906d37a01f0fca2d4114e8ba07078fc46eec88b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93131182"
---
# <a name="cloud-solution-provider-program"></a>Bulut Çözümü Sağlayıcısı programı

Bu makalede, teklifinizin bulut çözümü sağlayıcısı (CSP) programı için kullanılabilir olacak şekilde nasıl yapılandırılacağı açıklanmaktadır. [Ticari Market çevrimiçi mağazalarıyla](overview.md#commercial-marketplace-online-stores)tekliflerinizi yayımlamaya ek olarak, programın hizmet verdiği milyonlarca Microsoft müşterilerine ulaşmak için CSP programını da satın alabilirsiniz.

CSP iş ortaklarının ürünlerinizi satmasını ve müşteriler için paketlenmiş çözümler oluşturmasını sağlayan bir isteğe bağlı olarak, CSP programında kullanılabilirlik için yeni veya mevcut teklifleri yapılandırabilirsiniz.

Yayımcılar, son müşterilere onarım desteği sağlamaktan ve CSP programındaki iş ortakları ve/veya müşterilerin destek için sizinle iletişim kurabilmesi için bir mekanizma sağlamaktan sorumludur. CSP programındaki iş ortaklarının, müşterilerden katman 1 destek taleplerini işlemesini sağlamak için, CSP programındaki iş ortaklarını Kullanıcı belgeleri, eğitim ve hizmet durumu/kesinti bildirimleri (geçerli olduğu şekilde) sağlamak en iyi uygulamadır.  

Aşağıdaki teklifler, CSP programındaki iş ortakları tarafından satılmasını kabul etmek için uygundur:

- Hizmet olarak yazılım (SaaS) Transact teklifleri
- Sanal makineler (VM 'Ler)
- Çözüm şablonları
- Yönetilen uygulamalar

> [!NOTE]
> Kapsayıcılar ve kendi lisansını getir (KLG) VM planları, varsayılan olarak CSP programındaki iş ortakları tarafından satılmasını tercih edilir.

## <a name="how-to-configure-an-offer"></a>Bir teklifi yapılandırma

Teklif Iş Ortağı Merkezi 'nde oluştururken CSP program kabul etme ayarını yapılandırın. [Değişen yayımcı deneyimi hakkında daha fazla bilgi edinin](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293).

### <a name="partner-center-opt-in"></a>İş Ortağı Merkezi kabul etme

Kabul etme deneyimi, CSP satıcısı hedef kitle modülünün altında bulunur:

![CSP satıcı hedef kitlesi](media/marketplace-publishers-guide/csp-reseller-audience.png)

Üç seçenekten birini belirleyin:

1. CSP programındaki tüm iş ortakları.
2. Seçtiğiniz CSP programındaki belirli iş ortakları.
3. CSP programında iş ortağı yok.

#### <a name="option-1-any-partner-in-the-csp-program"></a>Seçenek 1: CSP programındaki herhangi bir iş ortağı

![CSP programındaki iş ortakları](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Bu seçeneği belirleyerek, CSP programındaki tüm iş ortakları teklifinizi müşterilere yeniden satmaya uygundur.

#### <a name="option-2-specific-partners-in-the-csp-program-i-select"></a>Seçenek 2: Select CSP programındaki belirli iş ortakları

![Seçtiğiniz CSP programındaki belirli iş ortakları](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Bu seçeneği belirleyerek, CSP programındaki hangi iş ortaklarının teklifinizi yeniden satmaya uygun olduğunu yetkilendirirsiniz.

İş ortaklarını yetkilendirmek için, **CSP Iş ortakları Seç** ' i seçin ve iş ortağı ADıNA veya CSP Azure Active Directory (Azure AD) Kiracı kimliği ile arama yapmanızı sağlayan bir menü belirir.

![CSP menüsünü seçin](media/marketplace-publishers-guide/csp-pop-up-module.png)

**Ülke**, **uzmanlık** veya **beceri** gibi arama filtreleri uygulayabilirsiniz.

![İş ortağı arama için ülke/bölge, uzmanlık ve yetenek filtreleri](media/marketplace-publishers-guide/csp-add-resellers.png)

İş ortakları listesini seçtikten sonra **Ekle**' yi seçin.

![CSP programındaki yetkili iş ortaklarının örnek listesi](media/marketplace-publishers-guide/csp-add-resellers-details.png)

Seçtiğiniz iş ortaklarının listesini gösteren bir tablo, CSP satıcı hedef kitle sayfasında görüntülenir.

![CSP satıcı kitlesi sayfasında iş ortakları listesini içeren tablo](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

Değişikliklerinizi kaydetmek için **Taslağı kaydet** ' i seçin.

Bu teklif yayımdan kaldırılmış ise, teklifinizi seçtiğiniz iş ortaklarınız için kullanılabilir hale getirmek üzere yayımlamanız gerekir.

>[!NOTE]
>Belirli bir bölgedeki CSP programında bir iş ortağı yetkilendirmeniz durumunda bu, söz konusu bölgeye ait olan herhangi bir müşteriye teklifi satabilir. CSP tekliflerinin bölgeler altında nasıl sınıflandırıldığı hakkında daha fazla bilgi için bkz. [bulut çözümü sağlayıcısı program bölgesel pazarlar ve para birimi](/partner-center/regional-authorization-overview).

Zaten yayımlanmış bir teklifin CSP listesini güncelleştiriyorsanız, ek iş ortakları ekleyin ve **CSP hedef kitleleri**' ni seçin.

Zaten yetkili iş ortakları listesine sahip bir teklifiniz varsa ve başka bir teklif için aynı listeyi kullanmak istiyorsanız **içeri/dışarı aktarma**' yı kullanın. CSP listesine sahip olan teklifine gidin ve **CSP 'Leri dışarı aktar**' ı seçin. İşlevi, başka bir teklifine aktarılabilecek bir. csv dosyası geliştirir.

#### <a name="option-3-no-partners-in-the-csp-program"></a>Seçenek 3: CSP programında iş ortağı yok

![CSP programındaki iş ortaklarından hiçbiri](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Bu seçeneği belirleyerek, sizin teklifinizin CSP programını kullanıma sunuyorsunuz. Bu seçimi dilediğiniz zaman değiştirebilirsiniz.

## <a name="deauthorize-partners-in-the-csp-program"></a>CSP programındaki Iş ortaklarının yetkisini kaldırma

CSP programında bir iş ortağı yetkilendiriyorsunuz ve bu iş ortağı ürünü müşterilerine zaten borçlu olduysa, bu iş ortağının yetkisini kaldırma izni verilmez.

CSP programındaki bir iş, ürününüzü müşterilerine satmamışsa ve teklifiniz yayımlandıktan sonra CSP 'yi kaldırmak istiyorsanız aşağıdaki yönergeleri kullanın:

1. [Destek isteği sayfasına](https://aka.ms/marketplacepublishersupport)gidin. İlk birkaç açılan menü, sizin için otomatik olarak doldurulur.

   > [!NOTE]
   > Önceden doldurulmuş açılan menü seçimlerini değiştirmeyin.

2. **Ürün sürümünü seçin** için **canlı teklif yönetimi**' ni seçin.
3. **Sorunu en iyi şekilde tanımlayan bir kategori seçin** için teklifinizin başvurduğu kategoriyi seçin.
4. **Sorunu en iyi açıklayan bir sorun seçin** için **Mevcut teklifi Güncelleştir**' i seçin.
5. Sorun **ayrıntıları sayfasına** yönlendirilmek için **İleri ' yi** seçin ve sorununuz hakkında daha fazla ayrıntı girin.
6. Sorun başlığı olarak **Deyetkilendir CSP** kullanın ve gerekli bölümlerin geri kalanını doldurun.

## <a name="navigate-between-options"></a>Seçenekler arasında gezinme

Üç CSP satıcı seçeneği arasında gezinmek için bu bölümü kullanın.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>Seçenek One: CSP programındaki tüm iş ortakları

Teklifiniz Şu anda **1: varsa, CSP programında herhangi bir iş ortağı** varsa ve diğer iki seçenekten birine gitmek istiyorsanız, bir istek oluşturmak için aşağıdaki yönergeleri kullanın:

1. [Destek isteği sayfasına](https://aka.ms/marketplacepublishersupport)gidin. İlk birkaç açılan menü, sizin için otomatik olarak doldurulur.

   > [!NOTE]
   > Önceden doldurulmuş açılan menü seçimlerini değiştirmeyin.

2. **Ürün sürümünü seçin** için **canlı teklif yönetimi**' ni seçin.
3. **Sorunu en iyi şekilde tanımlayan bir kategori seçin** için teklifinizin başvurduğu kategoriyi seçin.
4. **Sorunu en iyi açıklayan bir sorun seçin** için **Mevcut teklifi Güncelleştir**' i seçin.
5. Sorun **ayrıntıları sayfasına** yönlendirilmek için **İleri ' yi** seçin ve sorununuz hakkında daha fazla ayrıntı girin.
6. Sorun başlığı olarak **Deyetkilendir CSP** kullanın ve gerekli bölümlerin geri kalanını doldurun.

> [!NOTE]
> İkinci seçenek seçeneğine gitmeye çalışıyorsanız ve CSP programındaki bir iş ortağı müşterilerine zaten bir teklif içeriyorsa, bu iş ortağı varsayılan olarak yetkili iş ortakları listenizde zaten vardır.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>İki seçenekten birinde gezin: CSP programında seçdiğim belirli iş ortakları

Teklifiniz Şu anda **2. seçenek ise, CSP programındaki belirli iş ortakları ' i seçin** ve tek yapmanız gerekırse, **CSP programındaki herhangi bir iş ortağı** olarak bir istek oluşturmak için aşağıdaki yönergeleri kullanın:

1. [Destek isteği sayfasına](https://aka.ms/marketplacepublishersupport)gidin. İlk birkaç açılan menü, sizin için otomatik olarak doldurulur.

   > [!NOTE]
   > Önceden doldurulmuş açılan menü seçimlerini değiştirmeyin.

2. **Ürün sürümünü seçin** için **canlı teklif yönetimi**' ni seçin.
3. **Sorunu en iyi şekilde tanımlayan bir kategori seçin** için teklifinizin başvurduğu kategoriyi seçin.
4. **Sorunu en iyi açıklayan bir sorun seçin** için **Mevcut teklifi Güncelleştir**' i seçin.
5. Sorun **ayrıntıları sayfasına** yönlendirilmek için **İleri ' yi** seçin ve sorununuz hakkında daha fazla ayrıntı girin.
6. Sorun başlığı olarak **Deyetkilendir CSP** kullanın ve gerekli bölümlerin geri kalanını doldurun.

 Teklifiniz Şu anda **2. seçenek: CSP programındaki belirli iş ortakları** ve **seçenek 3: CSP programında iş ortağı yok**' a gitmek istiyorsanız bu seçeneğe giderek yalnızca daha önce yetkilendirdiğiniz CSP programındaki iş ortakları, son müşterilere teklifinizi yeniden kişiselleştirmez. Bir istek oluşturmak için aşağıdaki yönergeleri kullanın:

1. [Destek isteği sayfasına](https://aka.ms/marketplacepublishersupport)gidin. İlk birkaç açılan menü, sizin için otomatik olarak doldurulur.

   > [!NOTE]
   > Önceden doldurulmuş açılan menü seçimlerini değiştirmeyin.

2. **Ürün sürümünü seçin** için **canlı teklif yönetimi**' ni seçin.
3. **Sorunu en iyi şekilde tanımlayan bir kategori seçin** için teklifinizin başvurduğu kategoriyi seçin.
4. **Sorunu en iyi açıklayan bir sorun seçin** için **Mevcut teklifi Güncelleştir**' i seçin.
5. Sorun **ayrıntıları sayfasına** yönlendirilmek için **İleri ' yi** seçin ve sorununuz hakkında daha fazla ayrıntı girin.
6. Sorun başlığı olarak **Deyetkilendir CSP** kullanın ve gerekli bölümlerin geri kalanını doldurun.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>3. seçenek: CSP programında iş ortakları olmadan gezinme

Teklifiniz Şu anda **seçenek 3: CSP programında iş ortakları** yoksa dilediğiniz zaman diğer iki seçenekten birine gidebilirsiniz.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>CSP programındaki iş ortaklarıyla satış ve destek malzemeleri paylaşma

Bulut çözümü sağlayıcısı programındaki iş ortaklarının teklifinizi en verimli şekilde temsil etmesini ve kuruluşunuzla birlikte çalışmasını sağlamak için, satıcıların kullanabildiği satış ve destek malzemeleri göndermeniz gerekir. Bu kaynaklar çevrimiçi mağazalardaki müşterilere gösterilmez.

### <a name="partner-center-csp-channel"></a>İş Ortağı Merkezi CSP kanalı

Iş Ortağı Merkezi 'nde CSP kanalını kabul ettiyseniz, yayımcılar teklif listesi modülü altında ilgili pazarlama materyalleri ve kanal iletişim bilgilerini barındıran bir URL girmelidir.

![İş Ortağı Merkezi CSP yardımcı bilgileri](media/marketplace-publishers-guide/pc-csp-channel.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Pazara çıkış Hizmetleri](https://partner.microsoft.com/reach-customers/gtm)hakkında daha fazla bilgi edinin.
- Teklifinizi oluşturmak ve yapılandırmak için [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) ' nde oturum açın.