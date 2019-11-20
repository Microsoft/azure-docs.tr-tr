---
title: PıM-Azure AD 'de Azure Kaynak rolleri için istekleri onaylama | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) içindeki Azure Kaynak rolleri için istekleri onaylamayı veya reddetmeyi öğrenin.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e779f633efccf7b594c193e165a584d22b1d653b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021981"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management Azure Kaynak rolleri için istekleri onaylama veya reddetme

Azure Active Directory (Azure AD) içinde Privileged Identity Management (PıM) ile, rolleri etkinleştirme için onay gerektirecek şekilde yapılandırabilir ve Azure AD kuruluşunuzdaki kullanıcıları veya grupları, temsilci onaylayanlar olarak seçebilirsiniz. Ayrıcalıklı rol yöneticisi için iş yükünü azaltmak üzere her bir rol için iki veya daha fazla onaylayan seçmenizi öneririz. Temsilci onaylamalardan isteklerin onaylaması 24 saati vardır. İstek 24 saat içinde onaylanmamışsa, uygun Kullanıcı yeni bir isteği yeniden göndermesi gerekir. 24 saat onay zaman penceresi yapılandırılamaz.

Azure Kaynak rolleri için istekleri onaylamak veya reddetmek için bu makaledeki adımları izleyin.

## <a name="view-pending-requests"></a>Bekleyen istekleri görüntüle

Bir temsilci onaylayıcı olarak, Onayınızı bekleyen bir Azure Kaynak rolü isteği olduğunda bir e-posta bildirimi alırsınız. Bu bekleyen istekleri Privileged Identity Management görüntüleyebilirsiniz.

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. **Azure AD Privileged Identity Management**açın.

1. **Istekleri Onayla**' yı seçin.

    ![İstekleri onaylama-gözden geçirilecek isteği gösteren Azure kaynakları sayfası](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    **Rol etkinleştirmeleri Için istekler** bölümünde, Onayınızı bekleyen isteklerin bir listesini görürsünüz.

## <a name="approve-requests"></a>İstekleri onaylama

1. Onaylamak istediğiniz isteği bulun ve seçin. Onaylama veya reddetme sayfası görüntülenir.

    ![İstekleri Onayla-Ayrıntılar ve bloklama kutusu ile onaylama veya reddetme bölmesi](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. **Gerekçe** kutusunda iş gerekçe ' nı girin.

1. **Onayla**seçeneğini belirleyin. Onayınız için bir Azure bildirimi alacaksınız.

    ![İsteğin onaylandığını gösteren onay bildirimi](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Reddetme istekleri

1. Reddetmek istediğiniz isteği bulun ve seçin. Onaylama veya reddetme sayfası görüntülenir.

    ![İstekleri Onayla-Ayrıntılar ve bloklama kutusu ile onaylama veya reddetme bölmesi](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. **Gerekçe** kutusunda iş gerekçe ' nı girin.

1. **Reddet**' i seçin. Bir bildirim, geri çevirme ile birlikte görüntülenir.

## <a name="workflow-notifications"></a>İş akışı bildirimleri

Aşağıda, iş akışı bildirimleri hakkında bazı bilgiler verilmiştir:

- Bir rol isteği gözden geçirmeyi beklerken, onaylayanlara e-posta ile bildirilir. E-posta bildirimleri isteğin doğrudan bir bağlantısını, onaylayanın onaylayabileceği veya reddedebileceği bir bağlantı içerir.
- İstekler onaylayan veya reddeden ilk onaylayan tarafından çözümlenir.
- Bir onaylayan isteğe yanıt verdiğinde, tüm onaylayanlara eylem bildirilir.
- Onaylanan bir kullanıcı rolünde etkin hale geldiğinde kaynak yöneticilerine bildirim yapılır.

>[!Note]
>Onaylanan bir kullanıcının etkin olmaması gerektiğini düşündüğü bir kaynak yöneticisi, Privileged Identity Management içinde etkin rol atamasını kaldırabilir. Kaynak yöneticilerine, onaylayan olmadıkları müddetçe bekleyen istekler bildirilmese de, bekleyen istekleri Privileged Identity Management görüntüleyerek tüm kullanıcılar için bekleyen istekleri görüntüleyebilir ve iptal edebilirler.

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure Kaynak rollerini genişletme veya yenileme](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management e-posta bildirimleri](pim-email-notifications.md)
- [Privileged Identity Management Azure AD rolleri için istekleri onaylama veya reddetme](azure-ad-pim-approval-workflow.md)
