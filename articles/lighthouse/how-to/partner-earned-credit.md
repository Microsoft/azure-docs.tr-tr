---
title: Temsilcili kaynaklar üzerindeki etkisini izlemek için iş ortağı KIMLIĞINIZI bağlayın
description: İş ortağı KIMLIĞINIZI, Azure açık Thouse aracılığıyla yönettiğiniz müşteri kaynaklarında iş ortağı kazanılan kredisi (PEC) alacak şekilde ilişkilendirmeyi öğrenin.
ms.date: 02/12/2021
ms.topic: how-to
ms.openlocfilehash: 4c18aae38570ab3fd84df7d45fb18e35404158be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100372102"
---
# <a name="link-your-partner-id-to-track-your-impact-on-delegated-resources"></a>Temsilcili kaynaklar üzerindeki etkisini izlemek için iş ortağı KIMLIĞINIZI bağlayın 

[Microsoft iş ortağı ağı](https://partner.microsoft.com/)üyesiyse, Iş ortağı kimliğinizi, Microsoft tarafından verilen müşteri kaynaklarını yönetmek için kullanılan kimlik bilgileriyle bağlantılandırın ve Microsoft 'un, Azure müşteri başarısını çalıştıran iş ortaklarını belirlemesine ve tanımasına izin verebilirsiniz. Bu bağlantı ayrıca [CSP (Bulut Çözümü Sağlayıcısı)](/partner-center/csp-overview) iş ortaklarının [Microsoft Müşteri Sözleşmesi'ni (MCA) imzalayan](/partner-center/confirm-customer-agreement) ve [Azure planı kapsamında olan](/partner-center/azure-plan-get-started) müşteriler için [yönetilen hizmetlerde iş ortağı tarafından kazanılan krediyi](/partner-center/partner-earned-credit) almalarına da olanak tanır.

Azure açık kullanım etkinlikleri için tanınma kazanmak için, [MPN kimliğinizi](../../cost-management-billing/manage/link-partner-id.md) yönettiğiniz kiracınızdaki en az bir kullanıcı hesabıyla bağlamanız ve bağlı hesabın eklendi aboneliklerinizin her birine erişebildiğinden emin olmanız gerekir.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Yeni müşterileri eklediğinizde iş ortağı KIMLIĞINIZI ilişkilendirin

İş ortağı KIMLIĞINIZI bağlamak için aşağıdaki işlemi kullanın (varsa, iş ortağı kazanılan krediyi etkinleştirin). Bu adımları tamamlayabilmeniz için [MPN iş ortağı kimliğinizi](/partner-center/partner-center-account-setup#locate-your-mpn-id) bilmeniz gerekir. İş ortağı profilinizde gösterilen **İlişkili MPN kimliğini** kullandığınızdan emin olun.

Kolaylık olması için kiracınızda bir hizmet sorumlusu hesabı oluşturmanızı, **ilişkili MPN Kimliğinizle** bağlamayı ve ardından, sahip olduğunuz her MÜŞTERIYE, [PEC için uygun olan bir Azure yerleşik rolüyle](/partner-center/azure-roles-perms-pec)birlikte erişim vermeyi öneririz.

1. Yönettiğiniz kiracınızda [bir hizmet sorumlusu Kullanıcı hesabı oluşturun](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) . Bu örnekte, bu hizmet sorumlusu hesabı için ad *sağlayıcı Otomasyonu hesabını* kullanacağız.
1. Bu hizmet sorumlusu hesabını kullanarak, yönetim kiracınızdaki [ilişkili MPN Kimliğinizle bağlantı](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) yapın. Bunu yalnızca bir kez yapmanız gerekir.
1. [ARM şablonlarını](onboard-customer.md) veya [yönetilen hizmet tekliflerini](publish-managed-services-offers.md)kullanarak BIR müşteriyi eklediğinizde, [PEC için uygun olan Azure yerleşik rolüne](/partner-center/azure-roles-perms-pec)sahip bir kullanıcı olarak sağlayıcı Otomasyonu hesabını içeren bir yetkilendirme eklediğinizden emin olun.

Bu adımları izleyerek, yönettiğiniz her müşteri kiracının iş ortağı KIMLIĞINIZLE ilişkilendirilmesi gerekir. Sağlayıcı Otomasyonu hesabının, müşteri kiracısında herhangi bir eylem doğrulaması veya yapması gerekmez.

:::image type="content" source="../media/lighthouse-pal.jpg" alt-text="Azure ınthouse ile iş ortağı KIMLIĞI bağlama işlemini gösteren diyagram.":::

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>İş ortağı KIMLIĞINIZI daha önce eklendi müşterilerine ekleyin

Zaten bir müşteriyi eklendi, sağlayıcı Otomasyon hesabı hizmet sorumlunuzu eklemek için başka bir dağıtım gerçekleştirmek istemeyebilirsiniz. Bunun yerine, **ilişkili MPN kimliğinizi** bu müşterinin kiracısında çalışmaya erişimi olan bir kullanıcı hesabıyla bağlayabilirsiniz. Hesaba [PEC için uygun bir Azure yerleşik rolü](/partner-center/azure-roles-perms-pec)verildiğinden emin olun.

Hesap, yönetim kiracınızda [ilişkili MPN Kimliğinizle bağlantı](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) kurulduktan sonra, bu müşteriyle ilgili olarak tanınabilmesi için tanınma izini sağlayacaksınız.

## <a name="confirm-partner-earned-credit"></a>Ortağın kazanılmış krediyi Onayla

[PEC ayrıntılarını Azure Portal görüntüleyebilir](/partner-center/partner-earned-credit-explanation#azure-cost-management) ve Pec 'in avantajına hangi maliyetlerin alındığını doğrulayabilirsiniz. PEC 'in yalnızca, MCA 'yi imzalayan ve Azure planının altında olan CSP müşterileri için geçerli olduğunu unutmayın.

Yukarıdaki adımları izlediyseniz ve beklenen ilişkilendirmeyi görmüyorsanız, Azure portal bir destek isteği açın.

Ayrıca, [Iş Ortağı Merkezi SDK 'sını](/partner-center/develop/get-invoice-unbilled-consumption-lineitems) kullanabilir ve `rateOfPartnerEarnedCredit` BIR abonelik için PEC doğrulamasını otomatik hale getirmek için filtre uygulayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Microsoft iş ortağı ağı](/partner-center/mpn-overview)hakkında daha fazla bilgi edinin.
- [PEC 'in nasıl hesaplanacağını ve ödendiğini](/partner-center/partner-earned-credit-explanation)öğrenin.
- [Müşterileri](onboard-customer.md) Azure ışıklı kullanmaya ekleme.