---
title: Azure Active Directory Kimlik Koruması nedir?
description: Azure AD Kimlik Koruması riski algılayın, düzeltin, araştırın ve çözümleyin
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e928d67ba7102df3d342e77705ea895f9230ff3
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887663"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Azure Active Directory Kimlik Koruması nedir?

Kimlik koruması, kuruluşların üç önemli görevi yerine getirmelerine olanak tanıyan bir araçtır:

- Kimlik tabanlı risklerin algılanmasını ve düzeltmesini otomatikleştirin.
- Portaldaki verileri kullanarak riskleri araştırın.
- Daha fazla analiz için risk algılama verilerini üçüncü taraf yardımcı programlara dışarı aktarın.

Kimlik koruması, Microsoft 'un kuruluşların konumlarından Azure AD, Microsoft hesaplarıyla tüketici alanı ve Xbox ile oyun oynarken kullanıcılarınızı koruduğu dersleri kullanır. Microsoft analizler 6.500.000.000.000 sinyalleri, müşterileri tespit etmek ve tehditlere karşı korumak için günde.

Tarafından oluşturulan ve kimlik koruması ile beslenir sinyalleri, erişim kararları vermek için koşullu erişim gibi araçlara daha fazla eklenebilir veya kuruluşunuzun adına göre daha fazla araştırma için bir güvenlik bilgileri ve olay yönetimi (SıEM) aracına geri alınabilir. zorlanan ilkeler.

## <a name="why-is-automation-important"></a>Otomasyon ne neden önemlidir?

Microsoft 'un kimlik güvenliği ve koruma ekibine bakmakta olan Ekim 2018 Alex Weinert [içindeki blog gönderisine](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843) , olay hacminde çalışırken otomasyonun neden bu kadar önemli olduğunu anlatmaktadır:

> Her gün, makine öğrenimi ve buluşsal sistemlerimiz, 18.000.000.000 farklı 300.000.000 hesaplar 800.000.000 için oturum açma denemesi için risk puanları sağlar (örneğin: cezai aktörler, saldırganlar gibi varlıklar).
>
> En son yılda, kimlik sistemlerimiz üzerinde en iyi 3 saldırıları hakkında konuşdum. Bu saldırıların son hacmi aşağıda verilmiştir
>   
>   - **İhlal yeniden yürütme**: Mayıs 2018 ' de 4.6 milyar TL saldırıları algılandı
>   - **Parola spreyi**: 2018 Nisan 'da 350k
>   - **Sızdırma**: Bu, tam olarak niceleştik, ancak her biri de ilgili olan 2018 Mart 'Ta 23m risk olaylarını gördük.

## <a name="risk-detection-and-remediation"></a>Risk algılama ve düzeltme

Kimlik koruması, riskleri aşağıdaki sınıflandırmalarla tanımlar:

| Risk algılama türü | Açıklama |
| --- | --- |
| Atipik seyahat | Kullanıcının en son oturum açma işlemlerini temel alarak sıradan bir konumdan oturum açın. |
| Anonim IP adresi | Anonim bir IP adresinden oturum açın (örneğin: Tor tarayıcısı, anonimleştirici VPN 'Ler). |
| Bilmediğiniz oturum açma özellikleri | Belirtilen kullanıcı için son gördüğdiğimiz özelliklerle oturum açın. |
| Kötü amaçlı yazılım bağlı IP adresi | Kötü amaçlı yazılımdan bağlantılı IP adresinden oturum açın |
| Sızdırılan kimlik bilgileri | Bu risk algılama, kullanıcının geçerli kimlik bilgilerinin sızdırdığını belirtir |
| Azure AD tehdit bilgileri | Microsoft 'un dahili ve dış tehdit bilgileri kaynakları, bilinen bir saldırı modelini tanımladı |

Bu riskler hakkında daha fazla ayrıntı ve bunların hesaplanmaları, makalede [risk](concept-identity-protection-risks.md)altında bulunabilir.

Risk sinyalleri, kullanıcıların şunları yapmasını isteme gibi düzeltme çabalarını tetikleyebilirler: Azure Multi-Factor Authentication gerçekleştirme, kendi kendine parola sıfırlama kullanarak parolalarını sıfırlama veya bir yönetici işlem yapana kadar engelleme.

## <a name="risk-investigation"></a>Risk Araştırması

Yöneticiler algılamaları gözden geçirebilir ve gerekirse el ile eylem gerçekleştirebilir. Yöneticilerin kimlik koruması 'nda araştırmalar için kullandığı üç temel rapor vardır:

- Riskli kullanıcılar
- Riskli oturum açma işlemleri
- Risk algılamaları

Makalesinde daha fazla bilgi bulabilirsiniz [: riski araştırma](howto-identity-protection-investigate-risk.md).

## <a name="exporting-risk-data"></a>Risk verileri dışarı aktarılıyor

Kimlik korumasından alınan veriler, arşiv ve daha fazla inceleme ve corelation için diğer araçlara aktarılabilir. Microsoft Graph tabanlı API 'Ler, kuruluşların bu verileri SıEM gibi bir araçla daha fazla işlenmek üzere toplamasına olanak tanır. Kimlik koruma API 'sine erişme hakkında bilgiler, [Azure Active Directory kimlik koruması ve Microsoft Graph kullanmaya başlama](howto-identity-protection-graph-api.md) hakkında bilgi edinebilirsiniz.

Kimlik koruması bilgilerini Azure Sentinel ile tümleştirme hakkında bilgi için, [Azure AD kimlik koruması verileri bağlama](../../sentinel/connect-azure-ad-identity-protection.md)makalesinde bulabilirsiniz.

## <a name="permissions"></a>İzinler

Kimlik koruması, kullanıcıların erişebilmeleri için bir güvenlik okuyucusu, güvenlik operatörü, Güvenlik Yöneticisi, genel okuyucu veya küresel yönetici olmasını gerektirir.

## <a name="license-requirements"></a>Lisans gereksinimleri

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Özellik | Ayrıntılar | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Temel/ücretsiz |
| --- | --- | --- | --- | --- |
| Risk ilkeleri | Kullanıcı risk ilkesi (kimlik koruması aracılığıyla) | Yes | Hayır | Hayır |
| Risk ilkeleri | Oturum açma risk ilkesi (kimlik koruması veya koşullu erişim aracılığıyla) | Yes | Hayır | Hayır |
| Güvenlik raporları | Genel Bakış | Yes | Hayır | Hayır |
| Güvenlik raporları | Riskli kullanıcılar | Tam erişim | Sınırlı bilgi | Sınırlı bilgi |
| Güvenlik raporları | Riskli oturum açma işlemleri | Tam erişim | Sınırlı bilgi | Sınırlı bilgi |
| Güvenlik raporları | Risk algılamaları | Tam erişim | Sınırlı bilgi | Hayır |
| Bildirimler | Risk altındaki kullanıcılar uyarılar tespit etti | Yes | Hayır | Hayır |
| Bildirimler | Haftalık Özet | Yes | Hayır | Hayır |
| | MFA kayıt ilkesi | Yes | Hayır | Hayır |

## <a name="next-steps"></a>Sonraki adımlar

- [Güvenliğe genel bakış](concept-identity-protection-security-overview.md)

- [Risk nedir?](concept-identity-protection-risks.md)

- [Riskleri azaltmak için kullanılabilir ilkeler](concept-identity-protection-policies.md)
