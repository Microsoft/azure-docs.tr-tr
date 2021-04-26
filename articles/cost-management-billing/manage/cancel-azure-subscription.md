---
title: Azure aboneliğinizi iptal etme
description: Ücretsiz Deneme aboneliği gibi Azure aboneliğinizin nasıl iptal edileceğini açıklar
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: banders
ms.openlocfilehash: b20134cbfb85c2dcaef1227e6390c8c6047ad5be
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109399"
---
# <a name="cancel-your-azure-subscription"></a>Azure aboneliğinizi iptal etme

Artık gerekmiyorsa Azure portalda Azure aboneliğinizi iptal edebilirsiniz.

Microsoft, gerekli olmasa da aboneliğinizi iptal etmeden önce aşağıdaki işlemleri yapmanızı *önerir*:

* Verilerinizi yedekleyin. Örneğin, verileri Azure depolama alanında veya SQL’de depoluyorsanız bir kopya indirin. Sanal makineniz varsa, bir görüntüsünü yerel olarak kaydedin.
* Hizmetlerinizi kapatın. [Yönetim portalında kaynaklar sayfasına](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) gidin ve tüm çalışan sanal makineleri, uygulamaları ve diğer hizmetleri **Durdurun**.
* Verilerinizi geçirmeyi düşünün. Bkz. [Kaynakları yeni kaynak grubuna veya aboneliğe taşıma](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Tüm kaynakları ve tüm kaynak gruplarını silin.
* `AssignableScopes` içinde bu aboneliğe başvuran özel bir rolünüz varsa, aboneliği kaldırmak için bu özel rolleri güncelleştirmeniz gerekir. Bir aboneliği iptal ettikten sonra özel bir rolü güncelleştirmeye çalışırsanız bir hata alabilirsiniz. Daha fazla bilgi için bkz. [Özel rollerle ilgili sorunları giderme](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles) ve [Azure özel rolleri](../../role-based-access-control/custom-roles.md).

Ücretli bir Azure Desteği planını iptal ederseniz abonelik döneminin kalan bölümü için faturalandırılırsınız. Daha fazla bilgi için bkz. [Azure destek planları](https://azure.microsoft.com/support/plans/).

## <a name="who-can-cancel-a-subscription"></a>Aboneliği kim iptal edebilir?

Aşağıdaki tabloda, bir aboneliği iptal etmek için gereken izinler açıklanmaktadır.

|Abonelik türü     |İptal edebilen kişi  |
|---------|---------|
|Azure web sitesi üzerinden Azure’a kaydolduğunuzda oluşturulan abonelikler. Örneğin, bir [Ücretsiz Azure Hesabına](https://azure.microsoft.com/offers/ms-azr-0044p/), [kullandıkça öde fiyatlarına tabi hesaba](https://azure.microsoft.com/offers/ms-azr-0003p/) veya [Visual Studio abonesi](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/) olarak kaydolduğunuzda. |  Aboneliğin hesap yöneticisi ve sahipleri  |
|[Microsoft Kurumsal Anlaşma](https://azure.microsoft.com/pricing/enterprise-agreement/) ve [Kurumsal Geliştirme ve Test](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Aboneliğin hesap sahibi ve sahipleri       |
|[Azure planı](https://azure.microsoft.com/offers/ms-azr-0017g/) ve [DevTest için Azure planı](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Aboneliğin sahipleri      |


## <a name="cancel-subscription-in-the-azure-portal"></a>Azure portalında aboneliği iptal etme

1. [Azure portalındaki abonelik sayfasından](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) aboneliğinizi seçin.
1. İptal etmek istediğiniz aboneliği seçin.
1. **Genel Bakış**’ı ve sonra **Aboneliği iptal et**’i seçin.
    ![İptal düğmesini gösteren ekran görüntüsü](./media/cancel-azure-subscription/cancel_ibiza.png)
1. Yönergeleri izleyerek iptal etme işlemini tamamlayın.

> [!NOTE]
> Müşteri tarafından istenmesi ya da ödeme yapılmaması veya sahtekarlık yapılması durumunda iş ortakları aboneliği askıya alabilir veya iptal edebilir. Daha fazla bilgi için bkz. [Aboneliği askıya alma veya iptal etme](/partner-center/create-a-new-subscription#suspend-or-cancel-a-subscription).

## <a name="cancel-a-support-plan"></a>Bir destek planını iptal etme

Destek planınızı Azure web sitesi ya da Azure portal üzerinden satın aldıysanız veya Microsoft Müşteri Anlaşması kapsamında bir planınız varsa bir destek planını iptal edebilirsiniz. Destek planınızı bir Microsoft temsilcisi veya iş ortağı aracılığıyla satın aldıysanız yardım için onlarla iletişime geçin. 

1. Azure portalda **Maliyet Yönetimi + Faturalama**’ya gidin.
1. **Faturalama** bölümünde **Yinelenen ücretler**’i seçin.
1. Destek planı satır öğesinin sağ tarafındaki üç noktayı ( **...** ) seçin ve **Otomatik yenilemeyi kapat**’ı seçin.

## <a name="what-happens-after-subscription-cancellation"></a>Abonelik iptal edildikten sonra ne olur?

İptal etmenizin ardından faturalama hemen durdurulur. Ancak iptalin portalda gösterilmesi 10 dakika kadar sürebilir. Bir faturalama döneminin ortasında iptal ederseniz, son faturayı dönem sona erdikten sonra normal fatura tarihinizde göndeririz.

İptal etmenizin ardından hizmetleriniz devre dışı bırakılır. Başka bir deyişle, sanal makineleriniz serbest bırakılır, geçici IP adresleri serbest bırakılır ve depolama salt okunur olur.

Siparişiniz iptal edildikten sonra verilerinize erişmeniz gerekmesi veya fikrinizi değiştirmeniz durumunda verilerinizi kalıcı olarak silmeden önce Microsoft 30 ile 90 gün arasında bir süre boyunca bekler. Size veri depolama ücreti yansıtmayız. Daha fazla bilgi edinmek için bkz. [Microsoft Güven Merkezi - Verilerinizi nasıl yönetiriz?](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)

## <a name="delete-free-trial-subscription"></a>Ücretsiz deneme aboneliğini silme

Ücretsiz deneme aboneliğiniz varsa aboneliğin otomatik olarak silinmesi için 30 gün beklemeniz gerekmez. Aboneliğinizi iptal ettikten *üç gün* sonra silebilirsiniz. Aboneliğinizi iptal etmenizi izleyen üç gün boyunca **Aboneliği sil** seçeneği kullanılamaz.

1. Aboneliğinizi iptal ettiğiniz tarihten sonra üç gün bekleyin.
1. Azure portaldaki [Abonelikler](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) sayfasında aboneliğinizi seçin.
1. Silmek istediğiniz aboneliği seçin.
1. **Genel Bakış**’ı ve sonra **Aboneliği sil**’i seçin.

## <a name="delete-other-subscriptions"></a>Diğer abonelikleri Sil

El ile silebilmeniz gereken tek abonelik türü, ücretsiz bir deneme aboneliğinden faydalanamaz. Kullandıkça Öde abonelikleri de dahil olmak üzere diğer tüm abonelik türleri yalnızca [abonelik iptal](#cancel-subscription-in-the-azure-portal) işlemi aracılığıyla silinir. Diğer bir deyişle, ücretsiz bir deneme aboneliği olmadığı takdirde aboneliği doğrudan silemezsiniz. Ancak, bir aboneliği iptal ettikten sonra, aboneliğin hemen silinmesini istemek için bir [Azure destek isteği](https://go.microsoft.com/fwlink/?linkid=2083458) oluşturabilirsiniz.

## <a name="reactivate-a-subscription"></a>Aboneliği yeniden etkinleştirme

Aboneliğinizi Kullandıkça Öde tarifesine yanlışlıkla iptal ederseniz, [Azure Portal yeniden etkinleştirebilirsiniz](subscription-disabled.md).

Aboneliğiniz, Kullandıkça Öde fiyatlarına tabi bir abonelik değilse, aboneliğinizi yeniden etkinleştirmek için iptali izleyen 90 gün içinde desteğe başvurun.

## <a name="why-dont-i-see-the-cancel-subscription-option-on-the-azure-portal"></a>Azure portalda Aboneliği İptal Et seçeneğini neden görmüyorum? 

Aboneliği iptal etmek için gerekli izinlere sahip olmayabilirsiniz. Çeşitli abonelik türlerini iptal edebilecek kişilerle ilgili açıklama için bkz. [Aboneliği kim iptal edebilir?](#who-can-cancel-a-subscription).

## <a name="how-do-i-delete-my-azure-account"></a>Azure hesabımı nasıl silerim?

*Tüm kişisel bilgilerim dahil olmak üzere hesabımı kaldırdım. Etkin (ücretsiz deneme) aboneliklerimi zaten iptal ediyorum. Etkin Aboneliklerim yok ve Hesabımı tamamen silmek istiyor*.

* Kuruluşunuz aracılığıyla edindiğiniz bir Azure Active Directory hesabınız varsa Azure AD yöneticisi hesabı silebilir. Ardından hizmetleriniz devre dışı bırakılır. Başka bir deyişle, sanal makineleriniz serbest bırakılır, geçici IP adresleri serbest bırakılır ve depolama salt okunur olur. Özetle, iptal ettiğinizde faturalama hemen durdurulur.

* Kuruluşunuz aracılığıyla edindiğiniz bir Azure AD hesabınız yoksa Azure aboneliklerinizi iptal ettikten sonra silebilir ve kredi kartı bilgilerinizi hesaptan kaldırabilirsiniz. Bu işlem hesabı silmez, ancak çalışamaz hale getirir. Bir adım ileri gidebilir ve bir amaç için kullanılmıyorsa ilişkili Microsoft hesabınızı da silebilirsiniz.

## <a name="how-do-i-cancel-a-visual-studio-professional-account"></a>Visual Studio Professional hesabımı nasıl iptal ederim?

[Yenileme ve İptal](/visualstudio/subscriptions/faq/admin/renewal-cancellation) makalesine göz atın. Visual Studio Azure aboneliğiniz yoksa bunları da iptal edip silmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- Gerekirse, [Azure Portal](subscription-disabled.md)Kullandıkça Öde aboneliğini yeniden etkinleştirebilirsiniz.