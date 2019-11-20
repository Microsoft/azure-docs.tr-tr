---
title: Bulut çözümü sağlayıcıları | Azure Marketi
description: Yayımcılar artık Microsoft Bulut çözüm sağlayıcısı (CSP) iş ortağı kanalı aracılığıyla teklifleri satın alabilir.
services: Azure, Marketplace, Compute, Storage, Networking, Partner Center
author: ChJenk
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/12/2019
ms.author: v-chjen
ms.openlocfilehash: 546702af671cfe9506a4fc0448f40b7b8353960c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038782"
---
# <a name="cloud-solution-providers"></a>Bulut Çözüm Sağlayıcıları

Yazılım teklifleri, bulut çözümü sağlayıcısı (CSP) programında iş ortakları tarafından sunulan milyonlarca uygun Microsoft müşterilerine, [Microsoft Web storets](https://docs.microsoft.com/azure/marketplace/comparing-appsource-azure-marketplace)aracılığıyla sunulan tekliflerin genel kullanıma sunulmalarına ek olarak erişebilir.

Yayımcılar, CSP programında kullanılabilirlik için teklifleri, yeni bir teklif veya mevcut bir teklif için, iş ortaklarının ürünlerinizi satmasını ve müşteriler için paketlenmiş çözümler oluşturmasını sağlar.

Yayımcılar, son müşterilere onarım desteği sağlamaktan ve CSP programındaki iş ortakları ve/veya müşterilerin destek için sizinle iletişim kurabilmesi için bir mekanizma sağlamaktan sorumludur. CSP programındaki iş ortaklarının, müşterilerden katman 1 destek taleplerini işlemesini sağlamak için, CSP programındaki iş ortaklarını Kullanıcı belgeleri, eğitim ve hizmet durumu/kesinti bildirimleri (geçerli olduğu şekilde) sağlamak en iyi uygulamadır.  

Aşağıdaki teklifler, CSP programındaki iş ortakları tarafından satılmasını kabul etmek için uygundur:

- Hizmet olarak yazılım (SaaS) Transact teklifleri
- Sanal Makineler (VM'ler)
- Çözüm şablonları
- Yönetilen uygulamalar

> [!NOTE]
> Kapsayıcılar ve kendi lisansını getir (KLG) VM SKU 'Ları, varsayılan olarak CSP programındaki iş ortakları tarafından satılmasını tercih edilir.

## <a name="how-to-configure-an-offering"></a>Bir teklifi yapılandırma

CSP program kabul etme ayarı, Iş Ortağı Merkezi 'nde veya Bulut İş Ortağı Portalı teklif oluşturma deneyimi ' nde yapılandırılır. [Değişen yayımcı deneyimi hakkında daha fazla bilgi edinin](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293).

### <a name="partner-center-opt-in"></a>İş Ortağı Merkezi kabul etme

Iş Ortağı Merkezi 'nde, CSP satıcısı hedef kitle modülü kapsamında katılım deneyimini bulacaksınız.

![CSP satıcı hedef kitlesi](media/marketplace-publishers-guide/csp-reseller-audience.png)

CSP satıcı kitlesi modülünde seçebileceğiniz üç seçeneğiniz vardır:

- Seçenek One: CSP programındaki herhangi bir iş ortağı
- İki seçenek: seçtiğiniz CSP programında belirli iş ortakları
- Seçenek üç: CSP programında iş ortağı yok

#### <a name="option-one-any-partner-in-the-csp-program"></a>Seçenek One: CSP programındaki herhangi bir iş ortağı

![CSP programındaki tüm iş ortakları](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Bu seçeneği belirleyerek, CSP programındaki tüm iş ortakları teklifinizi müşterilere yeniden satmaya uygundur.

#### <a name="option-two-specific-partners-in-the-csp-program-i-select"></a>İki seçenek: seçtiğiniz CSP programında belirli iş ortakları

![Seçtiğiniz CSP programındaki belirli iş ortakları](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Bu seçeneği belirleyerek, CSP programındaki hangi iş ortaklarının teklifinizi yeniden satmaya uygun olduğunu yetkilendirirsiniz.

İş ortaklarını yetkilendirmek için, **CSP Iş ortakları Seç** ' e tıklayın ve iş ortağı ADıNA veya CSP Azure ACTIVE DIRECTORY (AAD) kiracı kimliğine göre arama yapmanızı sağlayan bir menü görünür.

![CSP menüsünü seçin](media/marketplace-publishers-guide/csp-pop-up-module.png)

**Ülke**, **uzmanlık**veya **beceri**gibi arama filtreleri uygulayabilirsiniz.

![İş ortağı arama için ülke, uzmanlık ve yetenek filtreleri](media/marketplace-publishers-guide/csp-add-resellers.png)

İş ortakları listesini seçtikten sonra **Ekle**' yi seçin.

![CSP programındaki yetkili iş ortaklarının örnek listesi](media/marketplace-publishers-guide/csp-add-resellers-details.png)

Seçtiğiniz iş ortaklarının listesini gösteren bir tablo, CSP satıcı hedef kitle sayfasında görüntülenir.

![CSP satıcı kitlesi sayfasında iş ortakları listesini içeren tablo](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

Değişikliklerinizi kaydetmek için **Taslağı kaydet** ' i seçin.

Bu teklif yayımdan kaldırılmış ise, teklifinizi seçtiğiniz iş ortaklarınız için kullanılabilir hale getirmek üzere yayımlamanız gerekir.

>[!NOTE]
>Belirli bir bölgedeki CSP programında bir iş ortağı yetkilendirmeniz durumunda bu, söz konusu bölgeye ait olan herhangi bir müşteriye teklifi satabilir. CSP tekliflerinin bölgeler bölümünde nasıl sınıflandırıldığı hakkında daha fazla bilgi için bkz. [bulut çözümü sağlayıcısı programı bölgesel pazarlar ve para birimi](https://docs.microsoft.com/partner-center/regional-authorization-overview) .

Zaten yayımlanmış bir teklifin CSP listesini güncelleştiriyorsanız, ek iş ortakları ekleyin ve **CSP hedef kitleleri**' ni seçin.

Zaten yetkili iş ortakları listesine sahip bir teklifiniz varsa ve başka bir teklif için aynı listeyi kullanmak istiyorsanız **içeri/dışarı aktarma**' yı kullanın. CSP listesine sahip olan teklifine gidin ve **CSP 'Leri dışarı aktar**' ı seçin. İşlevi, başka bir teklifine aktarılabilecek bir. csv dosyası geliştirir.

#### <a name="option-three-no-partners-in-the-csp-program"></a>Seçenek üç: CSP programında iş ortağı yok

![CSP programında iş ortağı yok](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Bu seçeneği belirleyerek, sizin teklifinizin CSP programını kullanıma sunuyorsunuz. Bu seçimi dilediğiniz zaman değiştirebilirsiniz.

### <a name="cloud-partner-portal-opt-in"></a>Bulut İş Ortağı Portalı kabul etme

Bulut İş Ortağı Portalı, katılım Market veya storefront sekmesinde ayarlanır. CSP programında belirli iş ortakları seçebilme özelliği yalnızca Iş Ortağı Merkezi 'nde kullanılabilir.

![CPP içinde CSP katılım deneyimi](media/marketplace-publishers-guide/csp-opt-in.png)

## <a name="deauthorize-partners-in-the-csp-program"></a>CSP programındaki Iş ortaklarının yetkisini kaldırma

CSP programında bir iş ortağı yetkilendiriyorsunuz ve bu iş ortağı ürünü müşterilerine zaten borçlu olduysa, bu iş ortağının yetkisini kaldırma izni verilmez.

CSP programındaki bir iş, ürününüzü müşterilerine satmamışsa ve teklifiniz yayımlandıktan sonra CSP 'yi kaldırmak istiyorsanız aşağıdaki yönergeleri kullanın:

1. [Destek isteği sayfasına](https://aka.ms/marketplacepublishersupport)gidin. İlk birkaç açılan menü, sizin için otomatik olarak doldurulur.

   > [!NOTE]
   > Önceden doldurulmuş açılan menü seçimlerini değiştirmeyin.

2. **Ürün sürümünü seçin**için **canlı teklif yönetimi**' ni seçin.
3. **Sorunu en iyi şekilde tanımlayan bir kategori seçin**için teklifinizin başvurduğu kategoriyi seçin.
4. **Sorunu en iyi açıklayan bir sorun seçin**için **Mevcut teklifi Güncelleştir**' i seçin.
5. Sorun **ayrıntıları sayfasına** yönlendirilmek için **İleri ' yi** seçin ve sorununuz hakkında daha fazla ayrıntı girin.
6. Sorun başlığı olarak **Deyetkilendir CSP** kullanın ve gerekli bölümlerin geri kalanını doldurun.




## <a name="navigate-between-options"></a>Seçenekler arasında gezinme

Üç CSP satıcı seçeneği arasında gezinmek için bu bölümü kullanın.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>Seçenek One: CSP programındaki tüm iş ortakları

Teklifiniz Şu anda **1: varsa, CSP programında herhangi bir iş ortağı** varsa ve diğer iki seçenekten birine gitmek istiyorsanız, bir istek oluşturmak için aşağıdaki yönergeleri kullanın:

1. [Destek isteği sayfasına](https://aka.ms/marketplacepublishersupport)gidin. İlk birkaç açılan menü, sizin için otomatik olarak doldurulur.

   > [!NOTE]
   > Önceden doldurulmuş açılan menü seçimlerini değiştirmeyin.

2. **Ürün sürümünü seçin**için **canlı teklif yönetimi**' ni seçin.
3. **Sorunu en iyi şekilde tanımlayan bir kategori seçin**için teklifinizin başvurduğu kategoriyi seçin.
4. **Sorunu en iyi açıklayan bir sorun seçin**için **Mevcut teklifi Güncelleştir**' i seçin.
5. Sorun **ayrıntıları sayfasına** yönlendirilmek için **İleri ' yi** seçin ve sorununuz hakkında daha fazla ayrıntı girin.
6. Sorun başlığı olarak **Deyetkilendir CSP** kullanın ve gerekli bölümlerin geri kalanını doldurun.

> [!NOTE]
> İkinci seçenek seçeneğine gitmeye çalışıyorsanız ve CSP programındaki bir iş ortağı müşterilerine zaten bir teklif içeriyorsa, bu iş ortağı varsayılan olarak yetkili iş ortakları listenizde zaten vardır.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>İki seçenekten birinde gezin: CSP programında seçdiğim belirli iş ortakları

Teklifiniz Şu anda **2. seçenek ise, CSP programındaki belirli iş ortakları ' i seçin** ve tek yapmanız gerekırse, **CSP programındaki herhangi bir iş ortağı**olarak bir istek oluşturmak için aşağıdaki yönergeleri kullanın:

1. [Destek isteği sayfasına](https://aka.ms/marketplacepublishersupport)gidin. İlk birkaç açılan menü, sizin için otomatik olarak doldurulur.

   > [!NOTE]
   > Önceden doldurulmuş açılan menü seçimlerini değiştirmeyin.

2. **Ürün sürümünü seçin**için **canlı teklif yönetimi**' ni seçin.
3. **Sorunu en iyi şekilde tanımlayan bir kategori seçin**için teklifinizin başvurduğu kategoriyi seçin.
4. **Sorunu en iyi açıklayan bir sorun seçin**için **Mevcut teklifi Güncelleştir**' i seçin.
5. Sorun **ayrıntıları sayfasına** yönlendirilmek için **İleri ' yi** seçin ve sorununuz hakkında daha fazla ayrıntı girin.
6. Sorun başlığı olarak **Deyetkilendir CSP** kullanın ve gerekli bölümlerin geri kalanını doldurun.

 Teklifiniz Şu anda **2. seçenek: CSP programındaki belirli iş ortakları** ve **seçenek 3: CSP programında iş ortağı yok**' a gitmek istiyorsanız bu seçeneğe giderek yalnızca daha önce yetkilendirdiğiniz CSP programındaki iş ortakları, son müşterilere teklifinizi yeniden kişiselleştirmez. Bir istek oluşturmak için lütfen aşağıdaki yönergeleri kullanın:

1. [Destek isteği sayfasına](https://aka.ms/marketplacepublishersupport)gidin. İlk birkaç açılan menü, sizin için otomatik olarak doldurulur.

   > [!NOTE]
   > Önceden doldurulmuş açılan menü seçimlerini değiştirmeyin.

2. **Ürün sürümünü seçin**için **canlı teklif yönetimi**' ni seçin.
3. **Sorunu en iyi şekilde tanımlayan bir kategori seçin**için teklifinizin başvurduğu kategoriyi seçin.
4. **Sorunu en iyi açıklayan bir sorun seçin**için **Mevcut teklifi Güncelleştir**' i seçin.
5. Sorun **ayrıntıları sayfasına** yönlendirilmek için **İleri ' yi** seçin ve sorununuz hakkında daha fazla ayrıntı girin.
6. Sorun başlığı olarak **Deyetkilendir CSP** kullanın ve gerekli bölümlerin geri kalanını doldurun.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>3\. seçenek: CSP programında iş ortakları olmadan gezinme

Teklifiniz Şu anda **seçenek 3: CSP programında iş ortakları**yoksa dilediğiniz zaman diğer iki seçenekten birine gidebilirsiniz.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>CSP programındaki iş ortaklarıyla satış ve destek malzemeleri paylaşma

Bulut çözümü sağlayıcısı programındaki iş ortaklarının teklifinizi en verimli şekilde temsil etmesini ve kuruluşunuzla birlikte çalışmasını sağlamak için, satıcılar tarafından kullanılabilecek satış ve destek malzemeleri göndermeniz gerekir. Bu kaynaklar Market storets 'deki müşterilere sunulmayacak.

### <a name="partner-center-csp-channel"></a>İş Ortağı Merkezi CSP kanalı

Iş Ortağı Merkezi 'nde CSP kanalını kabul ettiyseniz, yayımcılar teklif listesi modülünün altındaki CSP kanalına ilgili pazarlama malzemeleri ve kanal iletişim bilgilerini barındıran bir URL girmelidir:

![İş Ortağı Merkezi CSP yardımcı bilgileri](media/marketplace-publishers-guide/pc-csp-channel.png)

### <a name="cloud-partner-portal-csp-channel"></a>Bulut İş Ortağı Portalı CSP kanalı

Bulut İş Ortağı Portalı içinde CSP kanalını kabul ettiyseniz, yayımcılar ilgili pazarlama malzemeleri ve kanal iletişim bilgilerini CSP kanalına barındıran bir URL girmelidir:

![Bulut İş Ortağı Portalı CSP yardımcı malzemeleri bilgileri](media/marketplace-publishers-guide/cpp-csp-information.png)

## <a name="next-steps"></a>Sonraki adımlar

[Azure Marketi ve AppSource yayımcı kılavuzunu](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)ziyaret edin.

Market GTM Hizmetleri hakkında daha fazla bilgi edinmek için bkz. [Go-to-market Services](https://partner.microsoft.com/reach-customers/gtm).

Teklifinizi oluşturmak ve yapılandırmak için [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) 'nde veya [bulut iş ortağı portalı](https://cloudpartner.azure.com/) oturum açın.
