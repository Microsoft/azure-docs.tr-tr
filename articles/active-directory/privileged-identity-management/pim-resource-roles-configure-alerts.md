---
title: Privileged Identity Management Azure Active Directory Azure Kaynak rolleri için güvenlik uyarılarını yapılandırma | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) içindeki Azure Kaynak rolleri için güvenlik uyarılarını yapılandırma hakkında bilgi edinin.
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
ms.openlocfilehash: f0b938dc808d9b02ad4105d85a5b3125135c51d6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023080"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management Azure Kaynak rolleri için güvenlik uyarılarını yapılandırma

Privileged Identity Management (PıM), Azure Active Directory (Azure AD) kuruluşunuzda şüpheli veya güvenli olmayan bir etkinlik olduğunda uyarılar oluşturur. Bir uyarı tetiklendiğinde Uyarılar sayfasında görüntülenir.

![Azure kaynakları-uyarılar sayfası listeleme uyarısı, risk düzeyi ve sayı](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Uyarıları gözden geçirin

Uyarıyı tetikleyen Kullanıcı veya rolleri, düzeltme kılavuzlarıyla birlikte listeleyen bir rapor görmek için bir uyarı seçin.

![Son tarama zamanını, açıklamayı, risk azaltma adımlarını, türü, önem derecesini, güvenlik etkisini ve bir sonraki sefer nasıl önlemeyi gösteren uyarı raporu](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Uyarılar

| Uyarı | Severity | Tetikleyici | Öneri |
| --- | --- | --- | --- |
| **Bir kaynağa çok fazla sahip atandı** |Orta |Çok fazla kullanıcı sahip rolüne sahip. |Listedeki kullanıcıları gözden geçirin ve bazılarını daha az ayrıcalıklı rollere yeniden atayın. |
| **Bir kaynağa çok fazla sayıda kalıcı sahip atandı** |Orta |Rol için çok fazla sayıda kullanıcı kalıcı olarak atandı. |Listedeki kullanıcıları gözden geçirin ve rol kullanımı için etkinleştirmeyi gerektirmek üzere bazılarını yeniden atayın. |
| **Yinelenen rol oluşturuldu** |Orta |Birden çok rol aynı ölçütlere sahiptir. |Bu rollerden yalnızca birini kullanın. |

### <a name="severity"></a>Severity

- **Yüksek**: bir ilke ihlali nedeniyle hemen eyleme gerek duyar. 
- **Orta**: anında eyleme gerek yoktur, ancak olası bir ilke ihlaline işaret eder.
- **Düşük**: hemen eyleme gerek yoktur, ancak tercih edilen bir ilke değişikliği önerir.

## <a name="configure-security-alert-settings"></a>Güvenlik Uyarısı ayarlarını yapılandırma

Uyarılar sayfasından **Ayarlar**' a gidin.

![Ayarlar vurgulanmış olarak uyarılar sayfası](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Farklı uyarılardaki ayarları, ortamınız ve güvenlik hedeflerinizle çalışacak şekilde özelleştirin.

![Ayarları etkinleştirmek ve yapılandırmak için bir uyarının sayfası ayarlanıyor](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure Kaynak rolü ayarlarını yapılandırma](pim-resource-roles-configure-role-settings.md)
