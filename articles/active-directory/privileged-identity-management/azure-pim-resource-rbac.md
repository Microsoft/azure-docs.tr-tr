---
title: Privileged Identity Management (PıM)-Azure AD | içindeki Azure Kaynak rolleri için denetim raporunu görüntüleme | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) içindeki Azure Kaynak rolleri için etkinlik ve denetim geçmişini görüntüleyin.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45144f64789a19390984c3f9f6a660e3c3300215
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002502"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management Azure Kaynak rolleri için etkinlik ve denetim geçmişini görüntüleme

Azure Active Directory (Azure AD) Privileged Identity Management (PıM) ile kuruluşunuzdaki Azure kaynakları rollerinin etkinlik, etkinleştirmeler ve denetim geçmişini görüntüleyebilirsiniz. Buna abonelikler, kaynak grupları ve hatta sanal makineler dahildir. Azure rol tabanlı erişim denetimi işlevlerinden yararlanan Azure portal içindeki herhangi bir kaynak, Privileged Identity Management güvenlik ve yaşam döngüsü yönetim özelliğinden yararlanabilir.

> [!NOTE]
> Kuruluşunuzun, [Azure tarafından yetkilendirilen kaynak yönetimi](../../lighthouse/concepts/azure-delegated-resource-management.md)kullanan bir hizmet sağlayıcısına dış kaynaklı yönetim işlevleri varsa, bu hizmet sağlayıcısı tarafından yetkilendirilen rol atamaları burada gösterilmez.

## <a name="view-activity-and-activations"></a>Etkinlik ve etkinleştirmeleri görüntüleme

Belirli bir kullanıcının çeşitli kaynaklarda hangi işlemleri yaptığını görmek için, belirli bir etkinleştirme süresiyle ilişkili Azure Kaynak etkinliğini görüntüleyebilirsiniz.

1. **Azure AD Privileged Identity Management** açın.

1. **Azure kaynakları**' nı seçin.

1. Etkinlik ve etkinleştirmeleri görüntülemek istediğiniz kaynağı seçin.

1. **Rolleri** veya **üyeleri** seçin.

1. Bir kullanıcı seçin.

    Azure kaynaklarında tarihe göre kullanıcının eylemlerinin bir özetini görürsünüz. Ayrıca, aynı dönemdeki son rol etkinleştirmeleri da gösterilmektedir.

    ![Kaynak etkinlik Özeti ve rol etkinleştirmeleri ile kullanıcı ayrıntıları](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Ayrıntıları ve Kullanıcı etkin durumdayken oluşan ilgili Azure Kaynak etkinliğini görmek için belirli bir rol etkinleştirmesini seçin.

    [![Rol etkinleştirme seçildi ve etkinlik ayrıntıları](media/azure-pim-resource-rbac/export-membership.png "Rol etkinleştirme seçildi ve etkinlik ayrıntıları")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>Rol atamalarını alt öğeler ile dışarı aktarma

Denetçilerin rol atamalarının tamamen bir listesini sağlamanız gereken bir uyumluluk gereksinimine sahip olabilirsiniz. Privileged Identity Management, tüm alt kaynaklar için rol atamaları içeren belirli bir kaynaktaki rol atamalarını sorgulamanızı sağlar. Daha önce, yöneticilerin bir abonelik için rol atamalarının tamamen bir listesini alması ve her belirli kaynak için rol atamalarını dışarı aktarmaları gerekiyordu. Privileged Identity Management kullanarak, tüm kaynak grupları ve kaynakları için rol atamaları dahil olmak üzere bir abonelikte tüm etkin ve uygun rol atamalarını sorgulayabilirsiniz.

1. **Azure AD Privileged Identity Management** açın.

1. **Azure kaynakları**' nı seçin.

1. Abonelik gibi rol atamalarını dışarı aktarmak istediğiniz kaynağı seçin.

1. **Üyeler**’i seçin.

1. Dışarı aktar üyelik bölmesini açmak için **dışarı aktar** ' ı seçin.

    [![Tüm üyeleri dışarı aktarmak için üyelik bölmesini dışarı aktar](media/azure-pim-resource-rbac/export-membership.png "Tüm üyeleri dışarı aktarmak için üyeliği dışarı aktar sayfası")](media/azure-pim-resource-rbac/export-membership.png)

1. Bir CSV dosyasındaki tüm rol atamalarını dışarı aktarmak için **tüm üyeleri dışarı aktar** ' ı seçin.

    ![CSV dosyasındaki rol atamaları Excel 'de görüntüle olarak verildi](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Kaynak denetim geçmişini görüntüle

Kaynak denetimi size bir kaynak için tüm rol etkinliklerinin görünümünü sağlar.

1. **Azure AD Privileged Identity Management** açın.

1. **Azure kaynakları**' nı seçin.

1. Denetim geçmişini görüntülemek istediğiniz kaynağı seçin.

1. **Kaynak denetimi**' ni seçin.

1. Geçmişi önceden tanımlanmış bir tarih veya özel Aralık kullanarak filtreleyin.

    [![Filtrelerle kaynak denetim listesi](media/azure-pim-resource-rbac/rbac-resource-audit.png "Filtrelerle kaynak denetim listesi")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. **Denetim türü** için Etkinleştir ' i **(atanan + etkin)** seçin.

    [![Etkinleştirme denetim türüne](media/azure-pim-resource-rbac/rbac-audit-activity.png "Etkinleştir tarafından filtrelenen kaynak denetim listesi")](media/azure-pim-resource-rbac/rbac-audit-activity.png) ![ göre filtrelenen kaynak denetim listesi Etkinleştirme denetim türüne göre filtrelenen kaynak denetim listesi](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. **Eylem**' in altında, kullanıcının Azure kaynaklarında etkinlik ayrıntılarını görmek için **(etkinlik)** seçeneğine tıklayın.

    ![Belirli bir eylem için Kullanıcı Etkinliği Ayrıntıları](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Denetmi görüntüle

My Audit, kişisel rol etkinliğinizi görüntülemenize olanak sağlar.

1. **Azure AD Privileged Identity Management** açın.

1. **Azure kaynakları**' nı seçin.

1. Denetim geçmişini görüntülemek istediğiniz kaynağı seçin.

1. **Denetmi** seçin.

1. Geçmişi önceden tanımlanmış bir tarih veya özel Aralık kullanarak filtreleyin.

    [![Geçerli Kullanıcı için denetim listesi](media/azure-pim-resource-rbac/my-audit-time.png "Geçerli Kullanıcı için denetim listesi")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> Denetim geçmişine erişim, genel yönetici veya ayrıcalıklı rol yöneticisi rolü gerektirir.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Onay olayları için neden, onaylayan ve bilet numarası alın

1. Ayrıcalıklı rol yönetici rolü izinleriyle [Azure Portal](https://aad.portal.azure.com) oturum açın ve Azure AD ' yi açın.
1. **Denetim günlükleri**'ni seçin.
1. Yalnızca ayrıcalıklı kimlik yönetimi hizmeti için denetim olaylarını göstermek üzere **hizmet** filtresini kullanın. **Denetim günlükleri** sayfasında şunları yapabilirsiniz:

    - **Durum nedeni** sütununda bir denetim olayının nedenine bakın.
    - "Rol isteği için üye Ekle" olayı için **başlatan (aktör)** sütunundaki onaylayana bakın.

    [![PıM hizmeti için denetim günlüğünü filtrele](media/azure-pim-resource-rbac/filter-audit-logs.png "PıM hizmeti için denetim günlüğünü filtrele")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. **Ayrıntılar** bölmesinin **etkinlik** sekmesinde bilet numarasını görmek için bir denetim günlüğü olayı seçin.
  
    [![Denetim olayının bilet numarasını denetleyin](media/azure-pim-resource-rbac/audit-event-ticket-number.png "Denetim olayının bilet numarasını denetleyin")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)]

1. Bir denetim olayının **Ayrıntılar** bölmesinin **hedefler** sekmesinde istek sahibine (rolü etkinleştiren kişi) görüntüleyebilirsiniz. Azure Kaynak rolleri için üç hedef türü vardır:

    - Rol (**Type** = Role)
    - İstek sahibi (**tür** = diğer)
    - Onaylayan (**tür** = Kullanıcı)

    [![Hedef türünü denetle](media/azure-pim-resource-rbac/audit-event-target-type.png "Hedef türünü denetle")](media/azure-pim-resource-rbac/audit-event-target-type.png)

Genellikle, onay olayının hemen üzerindeki günlük olayı, **başlatılan (aktör)** istek sahibi olduğu "role üye ekleme tamamlandı" olayıdır. Çoğu durumda, istek sahibine bir denetim perspektifinden onay isteğinde bulmak zorunda kalmazsınız.

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure Kaynak rolleri atama](pim-resource-roles-assign-roles.md)
- [Privileged Identity Management Azure Kaynak rolleri için istekleri onaylama veya reddetme](pim-resource-roles-approval-workflow.md)
- [Privileged Identity Management Azure AD rolleri için denetim geçmişini görüntüleme](pim-how-to-use-audit-log.md)
