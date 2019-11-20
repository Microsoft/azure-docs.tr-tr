---
title: PıM-Azure AD 'de Azure AD rollerinin erişim incelemesini tamamlama | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) içinde Azure AD rollerinin erişim incelemesini tamamlamayı ve sonuçları görüntülemeyi öğrenin
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe2d85d605b9ee418a5709ddcdb448c56be1d918
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022274"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management Azure AD rollerinin erişim incelemesini tamamlar

Ayrıcalıklı rol yöneticileri, [erişim incelemesi başlatıldıktan](pim-how-to-start-security-review.md)sonra ayrıcalıklı erişimi gözden geçirebilir.  Privileged Identity Management (PıM), kullanıcıların erişimini gözden geçirmesini isteyen Azure Active Directory (Azure AD) kuruluşunuzdaki kullanıcılara otomatik olarak bir e-posta gönderir. Bir Kullanıcı bir e-posta göndermediyseniz, bunlara [erişim incelemesi gerçekleştirme](pim-how-to-perform-security-review.md)bölümündeki yönergeleri gönderebilirsiniz.

Erişim gözden geçirme süresi dolduktan sonra veya tüm kullanıcılar kendi kendini gözden geçirmeyi tamamladıktan sonra, gözden geçirmeyi yönetmek ve sonuçları görmek için bu makaledeki adımları izleyin.

## <a name="manage-access-reviews"></a>Erişim incelemelerini yönetme

1. [Azure Portal](https://portal.azure.com/) gidin ve panonuzda **Azure AD Privileged Identity Management** hizmeti seçin.
1. Panonun **erişim İncelemeleri** bölümünü seçin.
1. Yönetmek istediğiniz erişim gözden geçirmeyi seçin.

Erişim incelemesinin ayrıntı dikey penceresinde, gözden geçirmeyi yönetmeye yönelik çeşitli seçenekler vardır.

![Privileged Identity Management erişim gözden geçirme düğmeleri-ekran görüntüsü](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Tmak

Kullanıcıların kendilerini gözden geçirmesi için bir erişim incelemesi ayarlandıysa, **hatırlat** düğmesi bir bildirim gönderir.

### <a name="stop"></a>Durdur

Tüm erişim incelemelerinin bitiş tarihi vardır, ancak daha önce bitirmek için **Durdur** düğmesini kullanabilirsiniz. Herhangi bir Kullanıcı bu süre içinde incelenmediyse, gözden geçirmeyi durdurduktan sonra bu kullanıcılara izin vermez. Bir gözden geçirmeyi durdurduktan sonra yeniden başlatmazsanız.

### <a name="apply"></a>Uygula

Bir erişim incelemesi tamamlandıktan sonra, bitiş tarihine ulaştığınızdan veya el ile durdurduğundan, **Uygula** düğmesi gözden geçirme sonucunu uygular. Gözden geçirme sırasında bir kullanıcının erişimi reddedildiyse, bu adım rol atamasını kaldıracak olan adımdır.  

### <a name="export"></a>Dışarı Aktarma

Erişim gözden geçirmesi sonuçlarını el ile uygulamak istiyorsanız, incelemeyi dışarı aktarabilirsiniz. **Dışarı aktar** DÜĞMESI bir CSV dosyasını indirmeye başlayacaktır. Sonuçları Excel veya CSV dosyalarını açan diğer programlarda yönetebilirsiniz.

### <a name="delete"></a>Sil

Daha fazla inceleme ile ilgilenmiyorsanız, silin. **Sil** düğmesi Privileged Identity Management hizmetten gözden geçirmeyi kaldırır.

> [!IMPORTANT]
> Bu bozucu değişikliği onaylamanız gerekmez, bu nedenle gözden geçirmeyi silmek istediğinizi doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure AD rolleri için erişim gözden geçirmesi başlatma](pim-how-to-start-security-review.md)
- [Privileged Identity Management Azure AD rollerimin erişim incelemesini gerçekleştirin](pim-how-to-perform-security-review.md)
