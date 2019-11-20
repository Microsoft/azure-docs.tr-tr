---
title: PıM-Azure AD 'de Azure AD rolleri için denetim raporunu görüntüleme | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) içinde Azure AD rollerinin denetim geçmişini görüntülemeyi öğrenin.
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
ms.openlocfilehash: 7c4a157d8d5bcd281ca9fee488e58c455034e898
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022057"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>PıM 'de Azure AD rollerinin denetim geçmişini görüntüleme

Tüm ayrıcalıklı roller için son 30 gün içinde tüm rol atamalarını ve etkinleştirmeleri görmek üzere Privileged Identity Management (PıM) denetim geçmişini kullanabilirsiniz. Yönetici, son kullanıcı ve eşitleme etkinliği dahil olmak üzere Azure Active Directory (Azure AD) kuruluşunuzda etkinliğin tam denetim geçmişini görmek isterseniz, [Azure Active Directory güvenlik ve etkinlik raporlarını](../reports-monitoring/overview-reports.md)kullanabilirsiniz.

## <a name="view-audit-history"></a>Denetim geçmişini görüntüleme

Azure AD rollerinin denetim geçmişini görüntülemek için bu adımları izleyin.

1. [Ayrıcalıklı rol yöneticisi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) rolünün üyesi olan bir kullanıcıyla [Azure Portal](https://portal.azure.com/) oturum açın.

1. **Azure AD Privileged Identity Management**açın.

1. **Azure AD rolleri**' ne tıklayın.

1. **Dizin rolleri denetim geçmişi**' ne tıklayın.

    Denetim geçmişinize bağlı olarak, toplam etkinleştirmeler, günde maksimum etkinleştirme ve günlük ortalama etkinleştirme ile birlikte bir sütun grafiği görüntülenir.

    ![Dizin rolleri denetim geçmişi](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Sayfanın en altında, kullanılabilir denetim geçmişinde her eylem hakkında bilgi içeren bir tablo görüntülenir. Sütunlar aşağıdaki anlamlara sahiptir:

    | Sütun | Açıklama |
    | --- | --- |
    | Zaman | Eylem oluştuğunda. |
    | Sahibinin | Rol etkinleştirmeyi veya değiştirmeyi isteyen kullanıcı. Değer **Azure sistemise**daha fazla bilgi için Azure denetim geçmişini kontrol edin. |
    | Eylem | İstek sahibi tarafından gerçekleştirilen eylemler. Eylemler, atama, atamayı kaldırma, etkinleştirme, devre dışı bırakma veya AddedOutsidePIM içerebilir. |
    | Üye | Rolü etkinleştiren veya bir role atanan kullanıcı. |
    | Rol | Kullanıcı tarafından atanan veya etkinleştirilen rol. |
    | Mantık yürütme | Etkinleştirme sırasında neden alanına girilen metin. |
    | dolmadan | Etkinleştirilen bir rolün süresi dolar. Yalnızca uygun rol atamaları için geçerlidir. |

1. Denetim geçmişini sıralamak için **saat**, **eylem**ve **rol** düğmelerine tıklayın.

## <a name="filter-audit-history"></a>Denetim geçmişini filtrele

1. Denetim geçmişi sayfasının en üstünde bulunan **filtre** düğmesine tıklayın.

    **Grafik parametrelerini Güncelleştir** bölmesi görüntülenir.

1. **Zaman aralığı**' nda bir zaman aralığı seçin.

1. **Roller**' de, görüntülemek istediğiniz rolleri belirtmek için onay kutularını seçin.

    ![Grafik parametreleri bölmesini Güncelleştir](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Filtrelenmiş denetim geçmişini görüntülemek için **bitti** ' ye tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure Kaynak rolleri için etkinlik ve denetim geçmişini görüntüleme](azure-pim-resource-rbac.md)
