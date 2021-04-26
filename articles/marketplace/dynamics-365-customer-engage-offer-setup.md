---
title: Microsoft AppSource (Azure Marketi) üzerinde PowerApps teklifini & müşteri katılımı için Dynamics 365 oluşturun.
description: Microsoft AppSource (Azure Marketi) üzerinde PowerApps teklifini & müşteri katılımı için Dynamics 365 oluşturun.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 04/20/2021
ms.openlocfilehash: d8b3a2da7ccbbbf866763dbe5c8c59b00e7abd10
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820505"
---
# <a name="how-to-create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>PowerApps teklifi & müşteri katılımı için Dynamics 365 oluşturma

Bu makalede, PowerApps teklifinin & müşteri katılımı için Dynamics 365 nasıl oluşturulacağı açıklanır. Dynamics 365 için tüm teklifler, sertifika sürecimize göre gider. Deneme deneyimi, kullanıcıların çözümünüzü canlı bir Dynamics 365 ortamına dağıtmasını sağlar.

Başlamadan önce, [Iş Ortağı Merkezi](partner-center-portal/create-account.md) 'nde ticari bir market hesabı oluşturun ve ticari Market programına kaydedildiğinden emin olun.

## <a name="before-you-begin"></a>Başlamadan önce

[Bir Dynamics 365 teklifini](marketplace-dynamics-365.md)gözden geçirin. Bu teklif için teknik gereksinimleri açıklayacak ve bunu oluştururken ihtiyaç duyacağınız bilgileri ve varlıkları listelecektir.

## <a name="create-a-new-offer"></a>Yeni teklif oluşturma

1. [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/home)' nde oturum açın.
2. Sol gezinti menüsünde **ticari Market**  >  **genel bakış**' ı seçin.
3. Genel Bakış sayfasında,   >  **PowerApps & müşteri katılımı için**+ yeni teklif Dynamics 365 ' i seçin.

    :::image type="content" source="media/dynamics-365/new-offer-dynamics-365-customer-engagement.png" alt-text="Sol bölmedeki menü seçeneklerini ve ' yeni teklif ' düğmesini müşteri katılımı seçimi ile gösterir.":::

> [!IMPORTANT]
> Bir teklif yayımlandıktan sonra, Iş Ortağı Merkezi 'nde üzerinde yaptığınız tüm düzenlemeler, yalnızca teklifi yeniden yayımladığınızda Microsoft AppSource görüntülenir. Bir teklifi değiştirdikten sonra her zaman yeniden yayımlamayı unutmayın.

## <a name="new-offer"></a>Yeni teklif

Bir **TEKLIF kimliği** girin. Bu, hesabınızdaki her teklif için benzersiz bir tanımlayıcıdır.

- Bu KIMLIK, varsa, teklif için Web adresindeki müşterilere ve Azure Resource Manager şablonlarına yönelik olarak görünür.
- Yalnızca küçük harfleri ve rakamları kullanın. KIMLIK, kısa çizgi ve alt çizgi içerebilir, ancak boşluk içeremez ve 50 karakterle sınırlıdır. Örneğin, yayımcı KIMLIĞINIZ ise `testpublisherid` ve **Test-teklif-1** girerseniz, teklif Web adresi olur `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- Teklif KIMLIĞI, **Oluştur**' u seçtikten sonra değiştirilemez.

Bir **teklif diğer adı** girin. Bu, Iş Ortağı Merkezi 'nde teklif için kullanılan addır.

- Bu ad AppSource 'ta kullanılmıyor. Teklif adından ve müşteriler tarafından gösterilen diğer değerlerden farklıdır.
- Bu ad, **Oluştur**' u seçtikten sonra değiştirilemez.

Teklifi oluşturmak için **Oluştur** ' u seçin. İş Ortağı Merkezi, **teklif kurulumu** sayfasını açar.

## <a name="alias"></a>Diğer ad

Bu teklifine yalnızca Iş Ortağı Merkezi içinde başvurmak için kullanacağımız açıklayıcı bir ad girin. Teklif diğer adı (teklif oluşturduğunuzda girdiğiniz şekilde önceden doldurulur) Market 'te kullanılmaz ve müşteriler tarafından gösterilen teklif adından farklıdır. Teklif adını daha sonra güncelleştirmek istiyorsanız [teklif listesi](dynamics-365-customer-engage-offer-listing.md) sayfasına bakın.

## <a name="setup-details"></a>Kurulum Ayrıntıları

**Potansiyel müşterilerin bu liste teklifiyle nasıl etkileşime geçmesini istiyorsunuz?**, bu teklif için kullanmak istediğiniz seçeneği belirleyin.

- **Şimdi alın (ücretsiz)** – teklifinizi ücretsiz olarak listeleyin.
- **Ücretsiz deneme (listeleme)** – teklifinizi ücretsiz bir deneme bağlantısı olan müşterilere listeleyin. Ücretsiz denemelerdeki teklif listesi, hizmetiniz tarafından oluşturulur, yönetilir ve yapılandırılır ve Microsoft tarafından yönetilen abonelikler içermez.

    > [!NOTE]
    > Uygulamanızın deneme bağlantınız aracılığıyla alacağı belirteçler, uygulamanızda hesap oluşturmayı otomatikleştirmek için yalnızca Azure Active Directory (Azure AD) aracılığıyla Kullanıcı bilgilerini almak üzere kullanılabilir. Bu belirteci kullanarak kimlik doğrulaması için Microsoft hesapları desteklenmez.

- **Benimle Iletişim kurun** – müşteri ilişkileri YÖNETIMI (CRM) sisteminizi bağlayarak müşteri iletişim bilgilerini toplayın. Müşterinin, bilgilerini paylaşması için izin istenir. Bu müşteri ayrıntıları, teklifinizin bulunduğu teklif adı, KIMLIĞI ve Market kaynağıyla birlikte, yapılandırdığınız CRM sistemine gönderilir. CRM 'nizi yapılandırma hakkında daha fazla bilgi için bkz. [müşteri adayları](#customer-leads).

## <a name="test-drive"></a>Sınama sürücüsü

Test sürücüsü, bu kullanıcılara, bir sabit sayıda saat boyunca önceden yapılandırılmış bir ortama erişim vererek, teklifinizi olası müşterilere sergilemenin harika bir yoludur. Sınama sürücüsü sunumu, daha fazla dönüştürme hızına neden olur ve yüksek oranda nitelikli müşteri adayları üretir. Daha fazla bilgi edinmek için [test sürücüsü nedir?](what-is-test-drive.md)ile başlayın.

> [!TIP]
> Bir test sürücüsü, ücretsiz deneme sürümünden farklıdır. Bir test sürücüsü, ücretsiz deneme ya da her ikisini birden sağlayabilirsiniz. Bunlar her ikisi de kendi çözümünüzü sabit bir süre için sağlar. Ancak, bir sınama sürücüsü Ayrıca, ürününüzün temel özelliklerine ve avantajlarına yönelik uygulamalı, kendinden kılavuzlu bir tura sahip olan gerçek hayatta bir uygulama senaryosunda gösterilmekte olan avantajlı bir geliştirme içerir.

Bir sınama sürücüsünü etkinleştirmek için, **Test sürücüsünü etkinleştir** onay kutusunu seçin ve **test sürücüsü türünü** seçin. Test sürücüsünü daha sonra yapılandıracaksınız. Test sürücüsüyle, teklifinizi müşteri adayları için bir CRM sistemine de yapılandırmanız gerekir (sonraki bölüme bakın). Test sürücüsünü teklifinizden kaldırmak için bu onay kutusunu temizleyin.

## <a name="customer-leads"></a>Müşteri liderleri

[!INCLUDE [Connect lead management](partner-center-portal/includes/connect-lead-management.md)]

Daha fazla bilgi için bkz. [ticari Market teklifinizin müşteri adayları](partner-center-portal/commercial-marketplace-get-customer-leads.md).

Sol gezinti menüsündeki **Özellikler**' in sonraki sekmesine geçmeden önce **Taslağı kaydet** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Teklif özelliklerini yapılandırma](dynamics-365-customer-engage-properties.md)
- [En iyi teklif listeleme deneyimleri](gtm-offer-listing-best-practices.md)