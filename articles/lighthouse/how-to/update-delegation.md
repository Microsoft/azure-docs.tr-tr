---
title: Temsilciyi güncelleştirme
description: Azure 'da daha önce eklendi bir müşteri için bir temsilciyi güncelleştirme hakkında bilgi edinin.
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: f0ed5222cdbac3d0e4d193941c2a6f233d15938c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555763"
---
# <a name="update-a-delegation"></a>Temsilciyi güncelleştirme

Azure 'da bir abonelik (veya kaynak grubu) eklendi yükledikten sonra değişiklik yapmanız gerekebilir. Örneğin, müşteriniz, farklı bir Azure yerleşik rolü gerektiren ek yönetim görevlerini gerçekleştirebilir veya bir müşteri aboneliğinin temsilci olarak bulunduğu kiracıyı değiştirmeniz gerekebilir.

> [!TIP]
> Bu konudaki hizmet sağlayıcılarına ve müşterilere başvuruyoruz, ancak [birden çok kiracıyı yöneten kuruluşlar](../concepts/enterprise.md) , Azure açık thouse 'u ayarlamak ve yönetim deneyimlerini birleştirmek için aynı süreci kullanabilir.

[Azure Resource Manager şablonlar (ARM şablonları) aracılığıyla müşteriniz eklendi](onboard-customer.md), bu müşteri için yeni bir dağıtım gerçekleştirilmesi gerekir. Değişdiklerinize bağlı olarak, özgün teklifi güncelleştirmek veya orijinal teklifi kaldırmak ve yeni bir tane oluşturmak isteyebilirsiniz.

- **Yalnızca yetkilendirmeleri değiştiriyorsanız**: Yalnızca ARM şablonunun **yetkilendirmeler** bölümünü değiştirerek temsilcinizi güncelleştirebilirsiniz.
- **Yöneten kiracıyı değiştiriyorsanız**: önceki teklifinizin farklı bir **mspoffername** Ile kullanarak yeni bir ARM şablonu oluşturmanız gerekir.

## <a name="update-your-arm-template"></a>ARM şablonunuzu güncelleştirme

Temsilcinizi güncelleştirmek için, yapmak istediğiniz değişiklikleri içeren bir ARM şablonu dağıtmanız gerekir.

Yalnızca yetkilendirmeleri (daha önce dahil ettiğiniz bir role sahip yeni bir Kullanıcı grubu ekleme veya mevcut bir kullanıcının rolünü değiştirme gibi) güncelleştiriyorsanız, önceki temsilcide kullandığınız [ARM şablonunda](onboard-customer.md#create-an-azure-resource-manager-template) aynı **mspoffername** öğesini kullanabilirsiniz. Önceki şablonunuzu başlangıç noktası olarak kullanabilirsiniz. Daha sonra, bir Azure yerleşik rolünü başka bir ile değiştirme veya şablona tamamen yeni bir yetkilendirme ekleme gibi değişiklikleri yapmanız gerekir.

**Mspoffername**' ı değiştirirseniz, bu, yeni ve ayrı bir teklif olarak kabul edilir. Yöneten kiracıyı değiştiriyorsanız bu gereklidir.

Yönetim kiracısı aynı kalırsa **Mspoffername** öğesinin değiştirilmesi gerekli değildir. Çoğu durumda, aynı müşteri tarafından yalnızca bir **Mspoffername** kullanılması ve kiracının yönetilmesi önerilir. Yine de değiştirmeyi seçerseniz, yeni bir temsilciyi dağıtılmadan önce müşterinin önceki temsilinin kaldırıldığından emin olun.

## <a name="remove-the-previous-delegation"></a>Önceki temsilciyi kaldır

Yeni bir dağıtım gerçekleştirmeden önce, [önceki temsilciye erişimi kaldırmak](remove-delegation.md)isteyebilirsiniz. Bu, tüm önceki izinlerin kaldırılmasını sağlar ve ileride uygulanması gereken tam Kullanıcı/Grup ve rollerle temizlemeye başlayabilmenizi sağlar.

> [!IMPORTANT]
> Yeni bir **Mspoffername** kullanır ve aynı **PrincipalId** değerlerinden birini tutarsanız, yeni teklifi dağıtabilmeniz için önceki temsilciye erişimi kaldırmanız gerekir. Önce teklifi kaldırmazsanız, daha önce izin vermiş olan kullanıcılar çakışan atamalar nedeniyle tamamen erişimi kaybedebilir.

Yönetim kiracısını değiştiriyorsanız, her iki kiracının erişime sahip olmaya devam etmesini istiyorsanız, önceki teklifi yerinde bırakabilirsiniz. Yalnızca yeni yönetim kiracının erişime sahip olmasını istiyorsanız, önceki teklifin kaldırılması gerekir. Bu işlem, yeni teklifi ekledikten önce veya sonra yapılabilir.

Yalnızca yetkilendirmeleri ayarlamak için teklifi güncelleştiriyorsanız ve aynı **Mspoffername** öğesini koruyarak, önceki temsilciyi kaldırmanız gerekmez. Yeni dağıtım önceki temsilcinin yerini alacak ve yalnızca en yeni şablondaki yetkilendirmeler uygulanacaktır.

:::image type="content" source="../media/update-delegation.jpg" alt-text="MspOfferName 'ın ne zaman değiştirileceği ve önceki bir temsilciyi nasıl kaldıracağınız diyagramını gösteren diyagram.":::

Temsilciye erişimin kaldırılması, özgün temsilde [yönetilen hizmetler kayıt ataması silme rolü](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) verilmiş olan Kiracıdaki herhangi bir kullanıcı tarafından yapılabilir. Yönettiğiniz kiracınızda hiçbir kullanıcının bu rolü yoksa, müşteriden [Azure Portal teklifine erişimi kaldırmasını](view-manage-service-providers.md#add-or-remove-service-provider-offers)isteyebilirsiniz.

> [!TIP]
> Yukarıdaki adımları izleyerek önceki temsilciyi kaldırdıysanız ve yine de yeni ARM şablonunu dağıtmıyorsa, [kayıt tanımını tamamen kaldırmanız](/powershell/module/az.managedservices/remove-azmanagedservicesdefinition)gerekebilir. Bu, sahibi gibi izne sahip bir role sahip herhangi bir kullanıcı tarafından `Microsoft.Authorization/roleAssignments/write` , müşteri kiracısında [](../../role-based-access-control/built-in-roles.md#owner)yapılabilir.  

## <a name="deploy-the-arm-template"></a>ARM şablonunu dağıtma

Müşteriniz, [güncelleştirilmiş şablonu](onboard-customer.md#deploy-the-azure-resource-manager-templates) , daha önce olduğu gibi Azure Portal, PowerShell kullanarak veya Azure CLI kullanarak dağıtabilir.

Dağıtım tamamlandıktan sonra, [başarılı olduğunu doğrulayın](onboard-customer.md#confirm-successful-onboarding). Daha sonra, güncelleştirilmiş yetkilendirmeler, müşterinin Temsilcili abonelik veya kaynak grupları için geçerli olacaktır.

## <a name="updating-managed-service-offers"></a>Yönetilen hizmet tekliflerini güncelleştirme

Müşterinizin Azure Marketi 'Nde yayınlanan yönetilen bir hizmet teklifiyle eklendi ve yetkilendirmeleri güncelleştirmek istiyorsanız, bu müşterinin planında güncelleştirilmesini istediğiniz [yetkilendirmeler](../../marketplace/plan-managed-service-offer.md) ile [teklifinizin yeni bir sürümünü yayımlayarak](../../marketplace/partner-center-portal/update-existing-offer.md) temsilciyi güncelleştirebilirsiniz. Müşteri daha sonra Azure portal en yeni sürüme güncelleştirebilir.

Yönetim kiracısını değiştirmek istiyorsanız müşterinin kabul etmesi için [Yeni bir yönetilen hizmet teklifi oluşturmanız ve yayımlamanız](../../marketplace/plan-managed-service-offer.md) gerekir.

> [!TIP]
> Daha önce belirtildiği gibi, aynı müşteri ve kiracı yönetimi arasında birden çok farklı teklif kullanmanıza gerek kalmaz. Aynı müşteri için aynı yönetim kiracısını kullanan yeni bir teklif yayımlarsanız, müşteri daha yeni teklifi kabul etmeden önce önceki teklifin kaldırıldığından emin olun.

## <a name="next-steps"></a>Sonraki adımlar

- Azure portal **müşterilerime** giderek [müşterileri görüntüleyin ve yönetin](view-manage-customers.md) .
- Daha önce eklendi [bir temsilciye erişimi kaldırmayı](remove-delegation.md) öğrenin.
