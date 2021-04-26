---
title: Erişim gözden geçirmeleri ile Kullanıcı erişimini yönetme-Azure AD
description: Azure Active Directory erişim gözden geçirmeleri ile bir grup üyeliği veya bir uygulamaya atama aracılığıyla kullanıcı erişimini yönetmeyi öğrenin
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc12b4cb7e97a0808405baebc64ca83cdb742bf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94696957"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Azure AD erişim gözden geçirmeleriyle kullanıcı erişimini yönetme

Azure Active Directory (Azure AD) sayesinde kullanıcıların uygun erişime sahip olduğundan kolayca emin olabilirsiniz. Kullanıcılardan veya bir karar verenden erişim gözden geçirmesine katılmasını ve kullanıcıların erişimini yeniden onaylamasını (veya doğrulamasını) isteyebilirsiniz. Gözden geçirenler, Azure AD önerilerine dayanarak her kullanıcının erişiminin devam edip etmemesine yönelik girişler ekleyebilir. Bir erişim gözden geçirmesi tamamlandığında, değişiklikler yapabilir ve artık erişime ihtiyaç duymayan kullanıcıların erişimini kaldırabilirsiniz.

> [!NOTE]
> Tüm kullanıcı türlerinin erişimini yerine yalnızca konuk kullanıcıların erişimini gözden geçirmek istiyorsanız [Erişim gözden geçirmeleriyle konuk kullanıcı erişimini yönetme](manage-guest-access-with-access-reviews.md) konusunu inceleyin. Genel yönetici gibi yönetim rollerindeki kullanıcı üyeliklerini gözden geçirmek istiyorsanız [Azure AD Privileged Identity Management’ta erişim gözden geçirmesi başlatma](../privileged-identity-management/pim-how-to-start-security-review.md) konusunu inceleyin.

## <a name="prerequisites"></a>Önkoşullar

- Azure AD Premium P2

Daha fazla bilgi için bkz. [Lisans gereksinimleri](access-reviews-overview.md#license-requirements).

## <a name="create-and-perform-an-access-review"></a>Erişim gözden geçirmesi oluşturma ve gerçekleştirme

Bir erişim gözden geçirmesinde bir veya daha çok kullanıcı gözden geçiren olabilir.  

1. Azure AD'de bir veya daha fazla üyesi olan bir grup seçin. Veya Azure AD’ye bağlı olup bir ya da daha fazla kullanıcı atanmış bir uygulama seçin. 

2. Her kullanıcının kendi erişimini mi gözden geçireceğine yoksa bir veya daha fazla kullanıcının tüm kullanıcıların erişimini mi gözden geçireceğine karar verin.

3. Aşağıdaki rollerden birinde: bir genel yönetici, Kullanıcı Yöneticisi veya gözden geçirilecek grubun bir M365 veya AAD güvenlik grubu sahibi, [Identity idare sayfasına](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)gidin.

4. Erişim gözden geçirmesini oluşturun. Daha fazla bilgi için bkz. [gruplar veya uygulamalar için erişim gözden geçirmesi oluşturma](create-access-review.md).

5. Erişim incelemesi başladığında, gözden geçirenlerden giriş vermesini isteyin. Varsayılan olarak, her biri Azure AD 'den, [gruplara veya uygulamalara erişimi gözden geçirdikleri](perform-access-review.md)erişim paneli bağlantısı ile bir e-posta alır.

6. Gözden geçirenler bilgi girmediyse, Azure AD’nin onlara bir anımsatıcı göndermesini isteyebilirsiniz. Varsayılan olarak, bitiş tarihine kadar olan sürenin yarısına ulaşıldığında, Azure AD henüz yanıt vermemiş olan gözden geçirenlere bir anımsatıcı gönderir.

7. Gözden geçirenler bilgileri girdikten sonra erişim gözden geçirmesini durdurun ve değişiklikleri uygulayın. Daha fazla bilgi için bkz. [grupların veya uygulamaların erişim Incelemesini tamamlamaya](complete-access-review.md)yönelik.


## <a name="next-steps"></a>Sonraki adımlar

[Gruplar veya uygulamalar için erişim gözden geçirmesi oluşturma](create-access-review.md)




