---
title: PıM-Azure AD 'de Azure AD rollerine erişimi gözden geçirme | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) içindeki Azure Active Directory rollerinin erişimini incelemeyi öğrenin.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76eccb5d62b68865b7a117312be62753f203e2cb
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847101"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management Azure AD rollerine erişimi gözden geçirin

Privileged Identity Management (PıM), kuruluşların Azure Active Directory (AD) ve Office 365 veya Microsoft Intune gibi diğer Microsoft çevrimiçi hizmetler kaynaklara yönelik ayrıcalıklı erişimi nasıl yöneteceğini basitleştirir. Atanan rollerinizi başarılı bir şekilde kendinize gözden geçirmek için bu makaledeki adımları izleyin.

Bir Yönetim rolüne atandıysanız, kuruluşunuzun ayrıcalıklı rol yöneticisi, işiniz için hala bu rolün gerekli olduğunu düzenli olarak doğrulamanızı isteyebilir. Bir bağlantı içeren bir e-posta alabilir veya [Azure Portal](https://portal.azure.com) doğrudan ve başlamadan başlayabilirsiniz.

Erişim gözden geçirmeleriyle ilgilenen ayrıcalıklı bir rol yöneticisiyseniz veya küresel yöneticisiyseniz, [bir erişim incelemesi başlatma hakkında](pim-how-to-start-security-review.md)daha fazla bilgi alın.

## <a name="add-a-pim-dashboard-tile"></a>PıM panosu kutucuğu ekleme

Azure AD Privileged Identity Management hizmeti Azure portal panonuza sabitlenmemişse, başlamak için bu adımları izleyin.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Azure portal sağ üst köşesindeki Kullanıcı adınızı seçin ve çalıştıracağınız dizini seçin.
3. **Tüm hizmetler** seçeneğini belirleyin ve **Azure AD Privileged Identity Management** araması yapmak için Filtre metin kutusunu kullanın.
4. **Panoya sabitle**'yi işaretleyin ve ardından **Oluştur**’a tıklayın. Privileged Identity Management uygulaması açılır.

## <a name="approve-or-deny-access"></a>Erişimi onayla veya Reddet

Erişimi onayladığınızda veya reddetmeniz durumunda, gözden geçireni bu rolü hâlâ kullanıp kullanmayacağınızı söylemiş olursunuz. Rol üzerinde kalmak istiyorsanız **Onayla** ' yı seçin veya artık erişime Ihtiyacınız yoksa **Reddet** ' i seçin. Gözden geçiren sonuçları uygulayaana kadar durumunuz hemen değişmez.
Erişim gözden geçirmesini bulmak ve gerçekleştirmek için şu adımları izleyin:

1. Privileged Identity Management hizmetinde, **ayrıcalıklı erişimi gözden geçir**' i seçin. Bekleyen erişim gözden geçirmeleri varsa, bunlar Azure AD **erişim İncelemeleri** sayfasında görünürler.
2. Gerçekleştirmek istediğiniz gözden geçirmeyi seçin.
3. Gözden geçirmeyi oluşturmadığınız sürece, incelemede yalnızca Kullanıcı olarak görüntülenir. Adınızın yanındaki onay işaretini seçin.
4. **Onayla** veya **Reddet**' i seçin. **Bir neden belirtin** metin kutusu içinde kararınız için bir neden eklemeniz gerekebilir.  
5. **Azure AD rollerini gözden geçir** dikey penceresini kapatın.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Sonraki adımlar

- [PIM hizmetinde Azure kaynak rollerimin erişim gözden geçirmesini gerçekleştirme](pim-resource-roles-perform-access-review.md)
