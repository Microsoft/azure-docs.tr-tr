---
title: -Azure Active Directory grup silme | Microsoft Docs
description: Azure Active Directory'yi kullanarak grup silme hakkında yönergeler.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdba55e0655a13e65e403f5da73fcb69db5dbca5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561906"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Azure Active Directory'yi kullanarak bir grubu silme
Bir Azure Active Directory (Azure AD) grubu için birkaç nedenden silebilirsiniz ancak genellikle çünkü olur:

- **Grup türü** yanlış seçeneğe yanlış ayarlandı.

- Yanlışlıkla yanlış veya yinelenen bir grup oluşturuldu. 

- Artık gruba gerek yok.

## <a name="to-delete-a-group"></a>Bir grubu silmek için
1. Dizin için bir Genel yönetici hesabı kullanarak [Azure portalda](https://portal.azure.com) oturum açın.

2. Seçin **Azure Active Directory**ve ardından **grupları**.

3. Gelen **gruplar - tüm gruplar** sayfasında aramak ve silmek istediğiniz grubu seçin. Bu adımlar için kullanacağız **MDM İlkesi - Doğu**.

    ![Tüm grupları grupları sayfasında grubu adı vurgulanmış](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. Üzerinde **MDM İlkesi - Doğu genel bakış** sayfasında ve ardından **Sil**.

    Grubu Azure Active Directory kiracınızdan silindi.

    ![MDM İlkesi - Doğu genel bakış sayfasında, Sil seçeneği vurgulanmış](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Sonraki adımlar

- Bir grubu yanlışlıkla silerseniz, yeniden oluşturabilirsiniz. Daha fazla bilgi için [temel bir grup oluşturma ve üye ekleme](active-directory-groups-create-azure-portal.md).

- Bir Office 365 grubunu yanlışlıkla silerseniz, bu geri yüklenmesi mümkün olabilir. Daha fazla bilgi için [silinen bir Office 365 grubunu geri](../users-groups-roles/groups-restore-deleted.md).
