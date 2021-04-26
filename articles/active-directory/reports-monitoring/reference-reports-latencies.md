---
title: Azure Active Directory raporlama gecikmeleri | Microsoft Docs
description: Raporlama olaylarının Azure portal göstermesi için gereken süre miktarını öğrenin
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0498ee1c57cfa661884fe3209d4e089b54996fae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89231070"
---
# <a name="azure-active-directory-reporting-latencies"></a>Azure Active Directory raporlama gecikme süreleri

Gecikme süresi Azure Active Directory (Azure AD) raporlama verilerinin [Azure Portal](https://portal.azure.com)gösterilmesi için gereken süredir. Bu makalede, farklı rapor türleri için beklenen gecikme süresi listelenmektedir. 

## <a name="activity-reports"></a>Etkinlik raporları

İki tür etkinlik raporu vardır:

- [Oturum açma](concept-sign-ins.md) işlemleri – yönetilen uygulamaların kullanımı ve Kullanıcı oturum açma etkinlikleri hakkında bilgi sağlar
- [Denetim günlükleri](concept-audit-logs.md) -kullanıcılar ve gruplar, yönetilen uygulamalar ve Dizin etkinlikleri hakkında sistem etkinliği bilgileri sağlar

Aşağıdaki tabloda etkinlik raporları için gecikme bilgileri listelenmektedir. 

> [!NOTE]
> **Gecikme süresi (95. yüzdebirlik)** , günlüklerin %95 ' i bildirilecek süreyi ifade eder ve **gecikme süresi (99th)** , günlüklerin bildirildiği 99 süreyi belirtir. 
>

| Rapor | Gecikme süresi (95. yüzdebirlik) |Gecikme süresi (99th yüzdebirlik)|
| :-- | --- | --- |
| Denetim günlükleri | 2 dk.  | 5 dk.  |
| Oturum açma işlemleri | 2 dk.  | 5 dk. |

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Premium lisans aldıktan sonra etkinlik verilerini ne kadar yakında görebilirim?

Ücretsiz lisansınız olan etkinlik verileriniz zaten varsa, yükseltme sırasında hemen görebilirsiniz. Hiç veriniz yoksa, Premium lisansa yükselttikten sonra verilerin raporlarda gösterilmesi bir veya iki gün sürer.

## <a name="security-reports"></a>Güvenlik raporları

İki tür güvenlik raporu vardır:

- [Riskli oturum](../identity-protection/overview-identity-protection.md) açma işlemleri-riskli oturum açma, bir kullanıcı hesabının meşru sahibi olmayan birisi tarafından gerçekleştirilmiş olabilecek oturum açma girişimine yönelik bir göstergedir. 
- [Risk için Işaretlenen kullanıcılar](../identity-protection/overview-identity-protection.md) -riskli bir Kullanıcı, tehlikeye girmiş olabilecek bir kullanıcı hesabı göstergesidir. 

Aşağıdaki tabloda güvenlik raporlarının gecikme süresi bilgileri listelenmektedir.

| Rapor | Minimum | Ortalama | Maksimum |
| :-- | --- | --- | --- |
| Risk altındaki kullanıcılar          | 5 dakika   | 15 dakika  | 2 saat  |
| Riskli oturum açma işlemleri         | 5 dakika   | 15 dakika  | 2 saat  |

## <a name="risk-detections"></a>Risk algılamaları

Azure AD, kullanıcı hesaplarınızla ilgili şüpheli eylemleri algılamak için uyarlamalı makine öğrenimi algoritmaları ve buluşsal yöntemler kullanır. Algılanan her şüpheli eylem, **risk algılama** adlı bir kayıtta saklanır.

Aşağıdaki tabloda risk algılamaları için gecikme bilgileri listelenmektedir.

| Rapor | Minimum | Ortalama | Maksimum |
| :-- | --- | --- | --- |
| Anonim IP adreslerinden oturum açma işlemleri |5 dakika |15 dakika |2 saat |
| Alışılmadık konumlardan oturum açma işlemleri |5 dakika |15 dakika |2 saat |
| Sızan kimlik bilgilerine sahip kullanıcılar |2 saat |4 saat |8 saat |
| Alışılmadık konumlara imkansız seyahat |5 dakika |1 saat |8 saat  |
| Bulaşma olan cihazlardan oturum açma işlemleri |2 saat |4 saat |8 saat  |
| Şüpheli etkinlik gösteren IP adreslerinden gerçekleştirilen oturum açma işlemleri |2 saat |4 saat |8 saat  |


## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD raporlarına genel bakış](overview-reports.md)
* [Azure AD raporlarına programlı erişim](concept-reporting-api.md)
* [Risk algılamalarını Azure Active Directory](../identity-protection/overview-identity-protection.md)