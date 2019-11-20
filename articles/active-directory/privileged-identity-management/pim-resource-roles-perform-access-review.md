---
title: PıM-Azure AD 'de Azure Kaynak rollerine erişimi gözden geçirme | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) içindeki Azure Kaynak rollerinin erişimini incelemeyi öğrenin.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ddb4d0ff1339f1427c5041528cdbe464a345b37
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847010"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management Azure Kaynak rollerine erişimi gözden geçirin

Privileged Identity Management (PıM) erişim incelemeleri, Azure Active Directory (Azure AD) ayrıcalıklı rollere güvenli erişim sağlanmasına yardımcı olabilir. Bu makalede, bir Azure AD erişim incelemesinin ayrıcalıklı rol atamalarınızı gözden geçirmeyi tamamlamaya yönelik adımlar.

Bir Yönetim rolüne atandıysanız, bir rol gereksinimini onaylamak için yöneticiniz tarafından bir erişim incelemesi gerçekleştirmeniz gerekebilir. Onay isteği bir bağlantı içeren bir e-posta veya [Azure Portal](https://portal.azure.com)doğrulayabilirsiniz.

Erişim gözden geçirmeleriyle ilgilenen ayrıcalıklı bir rol yöneticisiyseniz, [erişim incelemesi başlatma hakkında](pim-resource-roles-start-access-review.md)daha fazla ayrıntı alın.

## <a name="approve-or-deny-access"></a>Erişimi onayla veya Reddet

Bu rolü kullanmaya devam etmenize bağlı olarak erişimi onaylayabilir veya reddedebilirsiniz. Rol üzerinde kalmak istiyorsanız **Onayla** ' yı seçin veya artık erişime Ihtiyacınız yoksa **Reddet** ' i seçin. Durumunuz yalnızca gözden geçiren sonuçları uyguladıktan sonra değişir.

Erişim gözden geçirmesini bulmak ve gerçekleştirmek için şu adımları izleyin:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. **Azure Active Directory** seçin ve **Privileged Identity Management**açın.
1. **Erişimi gözden geçir**' i seçin.

   ![Gözden geçirme erişimi dikey penceresi seçiliyken Privileged Identity Management uygulamasının ekran görüntüsü](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. Gerçekleştirmek istediğiniz gözden geçirmeyi seçin.
1. **Onayla** veya **Reddet**' i seçin. **Bir neden belirtin kutusunda**, gerekirse kararınız için bir iş gerekçe girin.

   ![Ayrıntıları gözden geçirme sayfasının ekran görüntüsü](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure AD rollerimin erişim incelemesini gerçekleştirin](pim-how-to-perform-security-review.md)
