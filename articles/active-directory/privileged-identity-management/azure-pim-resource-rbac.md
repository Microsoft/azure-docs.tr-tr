---
title: PıM-Azure AD 'de Azure Kaynak rolleri için denetim raporunu görüntüleme | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) içindeki Azure Kaynak rolleri için etkinlik ve denetim geçmişini görüntüleyin.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2585457d22b773407c41bb6ea82c30a25fbfb88e
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847163"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management Azure Kaynak rolleri için etkinlik ve denetim geçmişini görüntüleme

Azure Active Directory (Azure AD) Privileged Identity Management (PıM) ile kuruluşunuzdaki Azure kaynakları rollerinin etkinlik, etkinleştirmeler ve denetim geçmişini görüntüleyebilirsiniz. Buna abonelikler, kaynak grupları ve hatta sanal makineler dahildir. Azure rol tabanlı erişim denetimi (RBAC) işlevselliğiyle ilgili Azure portal içindeki herhangi bir kaynak, Privileged Identity Management güvenlik ve yaşam döngüsü yönetim özelliğinden yararlanabilir.

## <a name="view-activity-and-activations"></a>Etkinlik ve etkinleştirmeleri görüntüleme

Belirli bir kullanıcının çeşitli kaynaklarda hangi işlemleri yaptığını görmek için, belirli bir etkinleştirme süresiyle ilişkili Azure Kaynak etkinliğini görüntüleyebilirsiniz.

1. **Azure AD Privileged Identity Management**açın.

1. **Azure kaynakları**' na tıklayın.

1. Etkinlik ve etkinleştirmeleri görüntülemek istediğiniz kaynağa tıklayın.

1. **Roller** veya **Üyeler**' e tıklayın.

1. Bir kullanıcıya tıklayın.

    Azure kaynaklarında tarihe göre Kullanıcı eylemlerinin grafik görünümünü görürsünüz. Ayrıca, aynı dönemdeki son rol etkinleştirmeleri da gösterilmektedir.

    ![Kaynak etkinlik Özeti ve rol etkinleştirmeleri ile kullanıcı ayrıntıları](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Ayrıntıları ve ilgili Kullanıcı etkinken gerçekleşen Azure Kaynak etkinliğini görmek için belirli bir rol etkinleştirmesine tıklayın.

    ![Seçilen rol etkinleştirme ve etkinlik ayrıntıları tarihe göre görüntülendi](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>Rol atamalarını alt öğeler ile dışarı aktarma

Denetçilerin rol atamalarının tamamen bir listesini sağlamanız gereken bir uyumluluk gereksinimine sahip olabilirsiniz. Privileged Identity Management, tüm alt kaynaklar için rol atamaları içeren belirli bir kaynaktaki rol atamalarını sorgulamanızı sağlar. Daha önce, yöneticilerin bir abonelik için rol atamalarının tamamen bir listesini alması ve her belirli kaynak için rol atamalarını dışarı aktarmaları gerekiyordu. Privileged Identity Management kullanarak, tüm kaynak grupları ve kaynakları için rol atamaları dahil olmak üzere bir abonelikte tüm etkin ve uygun rol atamalarını sorgulayabilirsiniz.

1. **Azure AD Privileged Identity Management**açın.

1. **Azure kaynakları**' na tıklayın.

1. Abonelik gibi rol atamalarını dışarı aktarmak istediğiniz kaynağa tıklayın.

1. **Üyeler**' e tıklayın.

1. Dışarı aktar üyelik bölmesini açmak için **dışarı aktar** ' a tıklayın.

    ![Tüm üyeleri dışarı aktarmak için üyelik bölmesini dışarı aktar](media/azure-pim-resource-rbac/export-membership.png)

1. Bir CSV dosyasındaki tüm rol atamalarını dışarı aktarmak için **tüm üyeleri dışarı aktar** ' a tıklayın.

    ![CSV dosyasındaki rol atamaları Excel 'de görüntüle olarak verildi](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Kaynak denetim geçmişini görüntüle

Kaynak denetimi size bir kaynak için tüm rol etkinliklerinin görünümünü sağlar.

1. **Azure AD Privileged Identity Management**açın.

1. **Azure kaynakları**' na tıklayın.

1. Denetim geçmişini görüntülemek istediğiniz kaynağa tıklayın.

1. **Kaynak denetimi**' ne tıklayın.

1. Geçmişi önceden tanımlanmış bir tarih veya özel Aralık kullanarak filtreleyin.

    ![Filtrelerle kaynak denetim listesi](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. **Denetim türü**için Etkinleştir ' i **(atanan + etkin)** seçin.

    ![Etkinleştirme denetim türüne göre filtrelenen kaynak denetim listesi](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. **Eylem**' in altında, kullanıcının Azure kaynaklarında etkinlik ayrıntılarını görmek için **(etkinlik)** seçeneğine tıklayın.

    ![Belirli bir eylem için Kullanıcı Etkinliği Ayrıntıları](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Denetmi görüntüle

My Audit, kişisel rol etkinliğinizi görüntülemenize olanak sağlar.

1. **Azure AD Privileged Identity Management**açın.

1. **Azure kaynakları**' na tıklayın.

1. Denetim geçmişini görüntülemek istediğiniz kaynağa tıklayın.

1. **Denetlememe**tıklayın.

1. Geçmişi önceden tanımlanmış bir tarih veya özel Aralık kullanarak filtreleyin.

    ![Geçerli Kullanıcı için denetim listesi](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure Kaynak rolleri atama](pim-resource-roles-assign-roles.md)
- [Privileged Identity Management Azure Kaynak rolleri için istekleri onaylama veya reddetme](pim-resource-roles-approval-workflow.md)
- [Privileged Identity Management Azure AD rolleri için denetim geçmişini görüntüleme](pim-how-to-use-audit-log.md)
