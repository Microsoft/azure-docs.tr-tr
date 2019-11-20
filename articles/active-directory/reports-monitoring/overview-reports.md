---
title: Azure Active Directory raporları nelerdir? | Microsoft Belgeleri
description: Azure Active Directory raporlarının genel bakışını sağlar.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b655fa2f12eac894e97c61cc931983aa58fff7a9
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007919"
---
# <a name="what-are-azure-active-directory-reports"></a>Azure Active Directory raporları nelerdir?

Azure Active Directory (Azure AD) raporları, ortamınızda etkinliğin kapsamlı bir görünümünü sağlar. Sağlanan verilerle:

- Uygulama ve hizmetlerinizin kullanıcılarınız tarafından nasıl kullanıldığını saptayabilirsiniz
- Ortamınızın durumunu etkileyebilecek olası riskleri algılayabilirsiniz
- Kullanıcılarınızın işlerini yapmalarını engelleyen sorunları giderebilirsiniz  

Raporlama mimarisinin iki ana dayanağı vardır:

- [Güvenlik raporları](#security-reports)
- [Etkinlik raporları](#activity-reports)

![Raporlama](./media/overview-reports/01.png)


## <a name="security-reports"></a>Güvenlik raporları

Güvenlik raporları, kuruluşunuzun kimliklerini korumanıza yardımcı olur. İki tür güvenlik raporu vardır:

- **Riskli oldukları belirlenen kullanıcılar** - [Riskli oldukları belirlenen kullanıcılar güvenlik raporundan](concept-user-at-risk.md), gizliliği bozulmuş olabilecek kullanıcı hesaplarına genel bir bakış elde edersiniz.

- **Riskli oturum açma işlemleri** - [Riskli oturum açma işlemleri güvenlik raporuyla](concept-risky-sign-ins.md), kullanıcı hesabının meşru sahibi olmayan biri tarafından gerçekleştirilmiş olabilecek oturum açma işlemleriyle ilgili göstergeler elde edersiniz. 

### <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Güvenlik raporuna erişebilmek için hangi Azure AD lisansınızın olması gerekir?  

Tüm Azure AD sürümlerinde, risk ve riskli oturum açma işlemleri raporları için bayrak eklenmiş kullanıcılar sağlanmıştır. Bununla birlikte, rapordaki ayrıntı düzeyi sürümler arasında değişiklik gösterir: 

- **Azure Active Directory ücretsiz ve temel sürümlerde**, risk ve riskli oturum açma işlemleri için işaretlenen kullanıcıların bir listesini alırsınız. 

- **Azure Active Directory Premium 1** sürümü bu modeli genişleterek her rapor için algılanan temel risk algılamalarını incelemenizi sağlar. 

- **Azure Active Directory Premium 2** sürümü, temel risk algılamaları hakkında en ayrıntılı bilgileri sağlar ve ayrıca, yapılandırılmış risk düzeylerine otomatik olarak yanıt veren güvenlik ilkelerini yapılandırmanıza olanak tanır.


## <a name="activity-reports"></a>Etkinlik raporları

Etkinlik raporları, kuruluşunuzdaki kullanıcıların davranışını anlamanıza yardımcı olur. Azure AD 'de iki tür etkinlik raporu vardır:

- **Denetim günlükleri** - [Denetim günlükleri etkinlik raporu](concept-audit-logs.md), kiracınızda gerçekleştirilen her görevin geçmişine erişmenizi sağlar.

- **Oturum açma işlemleri** - [Oturum açma işlemleri etkinlik raporuyla](concept-sign-ins.md), denetim günlükleri raporunda bildirilen görevleri kimlerin gerçekleştirdiğini saptayabilirsiniz.


### <a name="audit-logs-report"></a>Denetim günlükleri raporu 

[Denetim günlükleri raporu](concept-audit-logs.md) uyumluluk amacıyla sistem etkinliklerinin kayıtlarını sağlar. Bu veriler, şu yaygın senaryolara çözüm getirmenize olanak sağlar:

- Kiracımda birisi yönetici grubuna erişim aldı. Ona kim erişim verdi? 

- Belirli bir uygulamayı yeni ekledim ve iyi çalışıp çalışmadığını bilmek istiyorum; bu nedenle uygulamada oturum açan kullanıcıların listesini öğrenmek istiyorum

- Kiracımda kaç parola sıfırlama işlemi yapıldığını bilmek istiyorum


#### <a name="what-azure-ad-license-do-you-need-to-access-the-audit-logs-report"></a>Denetim günlükleri raporuna erişmek için hangi Azure AD lisansının olması gerekir?  

Denetim günlükleri raporu, lisansınız olan özellikler için sağlanır. Belirli bir özelliğin lisansına sahipseniz, o özelliğin denetim günlüğü bilgilerine de erişebilirsiniz. Daha ayrıntılı bilgi için bkz. [Azure Active Directory Özellikler ve yetenekler](https://www.microsoft.com/cloud-platform/azure-active-directory-features).   

### <a name="sign-ins-report"></a>Oturum açma işlemleri raporu

[Oturum açma işlemleri raporu](concept-sign-ins.md) , şu gibi soruların yanıtlarını bulmanıza olanak sağlar:

- Belirli bir kullanıcının oturum açma düzeni nedir?
- Bir hafta içerisinde kaç adet kullanıcı oturum açtı?
- Bu açılan oturumların durumu nedir?

#### <a name="what-azure-ad-license-do-you-need-to-access-the-sign-ins-activity-report"></a>Kaydolma işlemleri etkinlik raporuna erişmek için hangi Azure AD lisansınızın olması gerekir?  

Oturum açma işlemleri etkinlik raporuna erişebilmek için, kiracınızın ilişkili bir Azure AD Premium lisansı olması gerekir.

## <a name="programmatic-access"></a>Programlı erişim

Azure AD, Kullanıcı arabirimine ek olarak, bir dizi REST tabanlı API aracılığıyla rapor verilerine [programlı erişim](concept-reporting-api.md) olanağı da sağlar. Çeşitli programlama dilleri ve araçlarından bu API'leri çağırabilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

- [Riskli oturum açma işlemleri raporu](concept-risky-sign-ins.md)
- [Denetim günlükleri raporu](concept-audit-logs.md)
- [Oturum açma günlükleri raporu](concept-sign-ins.md)
