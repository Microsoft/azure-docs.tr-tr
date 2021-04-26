---
title: Microsoft ticari Market için yönetilen bir hizmet teklifi planlayın
description: Microsoft Iş Ortağı Merkezi 'nde ticari Market programını kullanarak Azure Marketi için yeni bir yönetilen hizmet teklifi planlayın.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.date: 12/23/2020
ms.openlocfilehash: f096e53f8054039f361bde1c5f2adffac615c53d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371949"
---
# <a name="how-to-plan-a-managed-service-offer-for-the-microsoft-commercial-marketplace"></a>Microsoft Commercial Market için yönetilen hizmet teklifi planlaması

Bu makalede, Iş Ortağı Merkezi aracılığıyla Microsoft ticari Market 'e yönetilen hizmet teklifi yayımlama gereksinimleri tanıtılmaktadır.

Yönetilen hizmetler, Azure açık Thouse ile ormanlar arası ve çok kiracılı yönetimi sağlayan Azure Marketi teklifleridir. Daha fazla bilgi edinmek için bkz. [Azure ışıklı kullanımı nedir?](../lighthouse/overview.md) Bir müşteri yönetilen bir hizmet teklifi satın aldığında, bir veya daha fazla abonelik veya kaynak grubu için temsilci seçebilirler. Daha sonra bu kaynaklar üzerinde Azure açık Thouse 'ın [Azure Temsilcili kaynak yönetimi](../lighthouse/concepts/azure-delegated-resource-management.md) yeteneklerini kullanarak çalışabilirsiniz.

## <a name="eligibility-requirements"></a>Uygunluk gereksinimleri

Yönetilen bir hizmet teklifini yayımlamak için bulut platformunda bir altın veya gümüş Microsoft uzmanlığına sahip olmanız gerekir. Bu uzmanlık, müşterilere uzmanlığınızı gösterir. Daha fazla bilgi için bkz. [Microsoft iş ortağı ağı yetkinlikleri](https://partner.microsoft.com/membership/competencies).

Teklifler, Azure Marketi 'nde yayımlanacak tüm geçerli [ticari Market sertifika ilkelerini](/legal/marketplace/certification-policies) karşılamalıdır.

## <a name="customer-leads"></a>Müşteri liderleri

Müşteri bilgilerini toplamak için teklifinizi müşteri ilişkileri yönetimi (CRM) sisteminize bağlamanız gerekir. Müşterinin, bilgilerini paylaşması için izin istenir. Teklif adı, KIMLIĞI ve çevrimiçi mağazalarla birlikte bu müşteri ayrıntıları, yapılandırdığınız CRM sistemine gönderilir. Ticari Market, farklı türlerde CRM sistemlerini destekler ve Power otomatikleştirmede bir Azure tablosu kullanma veya bir HTTPS uç noktası yapılandırma seçeneğiyle birlikte.

Teklif oluşturma sırasında veya sonrasında bir CRM bağlantısını dilediğiniz zaman ekleyebilir veya değiştirebilirsiniz. Ayrıntılı kılavuz için bkz. [ticari Market teklifinizin müşteri adayları](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="legal-contracts"></a>Yasal sözleşmeler

Iş Ortağı Merkezi 'nin Özellikler sayfasında teklifinizin kullanımı için **hüküm ve koşullar** sağlamanız istenecektir. Koşullarınızı doğrudan Iş Ortağı Merkezi ' nde girebilir veya URL 'nin bulunabileceği yeri sağlayabilirsiniz. Müşterilerinizin teklifinizi satın almadan önce bu hüküm ve koşulları kabul etmesi gerekir.

## <a name="offer-listing-details"></a>Teklif listeleme ayrıntıları

Yönetilen hizmet teklifinizi Iş Ortağı Merkezi 'nde oluşturduğunuzda metin, görüntüler, belgeler ve diğer teklif ayrıntılarını girersiniz. Bu, müşterilerin Azure Marketi 'nde teklifinizi bulduklarında göreceği şeydir. Aşağıdaki örneğe bakın:

:::image type="content" source="media/example-managed-service.png" alt-text="Yönetilen bir hizmet teklifinin Azure Marketi 'nde nasıl göründüğünü gösterir.":::

**Çağrı açıklamaları**

1. Logo
1. Name
1. Kısa açıklama
1. Kategoriler
1. Yasal sözleşmeler ve Gizlilik ilkesi
1. Description
1. Ekran görüntüleri/videolar
1. Yararlı bağlantılar

Teklif listesinin Azure portal nasıl göründüğünü gösteren bir örnek aşağıda verilmiştir:

:::image type="content" source="media/example-managed-service-azure-portal.png" alt-text="Bu teklifin Azure portal nasıl göründüğünü gösterir.":::

**Çağrı açıklamaları**

1. Ad
2. Açıklama
3. Yararlı bağlantılar
4. Ekran görüntüleri/videolar

> [!NOTE]
> Teklifiniz Ingilizce dışındaki bir dildeyseniz, teklif listesi bu dilde olabilir, ancak açıklama "Bu hizmet, &lt; teklif içeriklerinizin dilinde kullanılabilir>" İngilizce tümcesiyle başlamalıdır veya bitmelidir. Ayrıca, teklif listesi ayrıntılarından farklı bir dilde destekleme belgelerini de sağlayabilirsiniz.

Teklifinizi daha kolay bir şekilde oluşturmaya yardımcı olmak için bu öğelerin bazılarını zaman önce hazırlayın. Aksi belirtilmediği takdirde, aşağıdaki öğeler gereklidir.

**Ad**: Bu, ticari Market 'teki teklif listelerinizin başlığı olarak görünür. Ad trademarked olabilir. Bu, emojıs (ticari marka ve telif hakkı sembolleri olmadığı durumlar dışında) içeremez ve 50 karakterle sınırlı olmalıdır.

**Arama sonuçları Özeti**: teklifinizin 100 karakter veya daha az olması için amacını veya hedefini tanıtın. Bu Özet, ticari Market 'te arama sonuçlarını listelemek için kullanılır. Bu, başlıkla aynı olmamalıdır. En iyi SEO anahtar sözcüklerinizi dahil etmeyi göz önünde bulundurun.

**Kısa açıklama**: teklifinizin kısa bir açıklamasını sağlayın (en fazla 256 karakter). Azure portal, teklif listelemesi üzerinde görüntülenir.

**Açıklama**: teklifinizi 3.000 karakter veya daha az bir şekilde tanıtın. Bu açıklama ticari Market listesinde görüntülenir. Bir değer teklifi, anahtar avantajı, kategori veya sektör ilişkilendirmeleri ve gerekli tüm yemekler dahil etmeyi göz önünde bulundurun.

Tanımlarınızı yazmak için bazı ipuçları aşağıda verilmiştir:

* Teklifinizin değerini, ilk birkaç cümlede aşağıdakiler dahil olmak üzere net bir şekilde Açıklama:
    * Tekliften faydalanan Kullanıcı türü.
    * Müşteri ihtiyaçları veya teklif adreslerini yayınlar.
* İlk birkaç tümcenin arama sonuçlarında görüntülenebileceğini unutmayın.
* Sektöre özgü sözlük kullanın.

Açıklamanızı biçimlendirmek için HTML etiketlerini kullanabilirsiniz. HTML biçimlendirme hakkında daha fazla bilgi için bkz. [ticari Market teklif açıklamalarında desteklenen HTML etiketleri](./supported-html-tags.md).

**Gizlilik ilkesi bağlantısı**: sitenizde barındırılan gizlilik ILKESINE bir URL sağlayın. Önerinizi gizlilik yasaları ve yönetmeliklerle uyumlu hale getirmekten ve geçerli bir gizlilik ilkesi sağlamaya yönelik siz sorumlusunuz.

**Faydalı bağlantılar** (isteğe bağlı): Teklifinizle ilgili ek çevrimiçi belgeleri karşıya yükleyin.

**İletişim bilgileri**: şirketinizdeki iki kişinin adını, e-posta adresini ve telefon numarasını sağlayın (bunlardan biri olabilirsiniz): bir destek kişisi ve mühendislik ilgili kişisi. Teklifiniz hakkında sizinle iletişim kurmak için bu bilgileri kullanacağız. Bu bilgiler müşterilere gösterilmez, ancak bulut çözümü sağlayıcısı (CSP) iş ortaklarına temin edilebilir

**Destek URL 'leri** (isteğe bağlı): Azure küresel müşterileri ve/veya Azure Kamu müşterileri için destek Web siteleriniz varsa, bu URL 'leri belirtin.

**Market medyası – logolar**: teklifinizin büyük boyutlu logosu IÇIN bir PNG dosyası sağlayın. İş Ortağı Merkezi, orta ve küçük logolar oluşturmak için bunu kullanacaktır. İsteğe bağlı olarak, bu logoları daha sonra farklı bir görüntüyle değiştirebilirsiniz.

* Büyük logo (216 x 216 ile 350 x 350 piksel), Azure Marketi 'nde teklif listeleminizin üzerinde görünür.
* Orta logo (90 x 90 piksel), yeni bir kaynak oluşturulduğunda gösterilir.
* Küçük logo (48 x 48 piksel), Azure Marketi arama sonuçlarında kullanılır.

Logolarınız için şu yönergeleri izleyin:

* Görüntünün genişletilmediğinden emin olun.
* Azure tasarımının basit bir renk paleti vardır. Logonuzdaki birincil ve ikincil renklerinin sayısını sınırlandırın.
* Azure portal renkler beyaz ve siyahtır. Bunları logonuz arka planı olarak kullanmayın. Logonuzu belirgin hale getirmek için basit birincil renkler öneririz.
* Saydam bir arka plan kullanıyorsanız logo ve metnin beyaz, siyah ve mavi olmadığından emin olun.
* Logonuzun görünüm ve hisde sabit olması gerekir. Gradyanlar kullanmaktan kaçının. Şirket veya marka adınız dahil olmak üzere logoya metin yerleştirmeyin.

**Market medyası – ekran görüntüleri** (isteğe bağlı): teklifinizin nasıl çalıştığını gösteren en fazla beş görüntü ekleyin. Tüm görüntüler 1280 x 720 piksel boyutunda ve içinde olmalıdır. PNG biçimi.

**Market medyası – videolar** (isteğe bağlı): teklifinizi gösteren beş adede kadar videoyu karşıya yükleyin. Videoların YouTube veya Vimeo 'da barındırılması ve küçük bir resme (1280 x 720 PNG dosyası) sahip olması gerekir.

## <a name="preview-audience"></a>İzleyiciyi Önizle

Bir önizleme hedef kitlesi, test etmek için Azure Market 'te yayımlanmadan önce teklifinizin erişimine açık hale gelir. Iş Ortağı Merkezi 'nin **ön izleme İzleyicisi** sayfasında, sınırlı bir önizleme hedef kitlesi tanımlayabilirsiniz.

> [!NOTE]
> Önizleme hedef kitlesi özel bir plandan farklıdır. Özel bir plan yalnızca seçtiğiniz belirli bir hedef kitle için kullanılabilir hale getirir. Bu, belirli müşterilerle özel bir plana anlaşma yapmanızı sağlar.

Davetleri Microsoft hesabı (MSA) veya Azure Active Directory (Azure AD) e-posta adreslerine gönderebilirsiniz. 10 ' a kadar e-posta adresi ekleyin veya bir. csv dosyası ile 20 ' ye kadar içeri aktarın. Teklifiniz zaten canlı ise, teklifiniz için herhangi bir değişikliği veya güncelleştirmeyi test etmek üzere bir önizleme hedef kitlesi tanımlayabilirsiniz.

## <a name="plans-and-pricing"></a>Planlar ve fiyatlandırma

Yönetilen hizmet teklifleri en az bir plan gerektirir. Bir plan, varsa çözüm kapsamını, limitleri ve ilişkili fiyatlandırmayı tanımlar. Müşterilerinize farklı teknik ve fiyatlandırma seçenekleri sunmak için teklifiniz için birden çok plan oluşturabilirsiniz. Özel planlar dahil olmak üzere planlar hakkında genel rehberlik için bkz. [ticari Market teklifleri Için planlar ve fiyatlandırma](plans-pricing.md).

Yönetilen hizmetler yalnızca bir fiyatlandırma modelini destekler: **kendi lisansınızı getirin (KLG)**. Bu, müşterilerinizin doğrudan faturalandırılmayacağı anlamına gelir ve Microsoft size herhangi bir ücret ödemez.

## <a name="next-steps"></a>Sonraki adımlar

* [Yönetilen Hizmet teklifi oluşturma](./create-managed-service-offer.md)
* [En iyi teklif listeleme deneyimleri](./gtm-offer-listing-best-practices.md)