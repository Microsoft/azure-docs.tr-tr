---
title: Azure Active Directory Kimlik Koruması nedir?
description: Azure AD Kimlik Koruması riski algılayın, düzeltin, araştırın ve çözümleyin
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 01/05/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.custom: contperf-fy21q1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e274d35fde6a3d55c05bcb5a9f22e75a37aa3c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97955408"
---
# <a name="what-is-identity-protection"></a>Kimlik Koruması nedir?

Kimlik koruması, kuruluşların üç önemli görevi yerine getirmelerine olanak tanıyan bir araçtır:

- Kimlik tabanlı risklerin algılanmasını ve düzeltmesini otomatikleştirin.
- Portaldaki verileri kullanarak riskleri araştırın.
- Daha fazla analiz için risk algılama verilerini üçüncü taraf yardımcı programlara dışarı aktarın.

Kimlik koruması, Microsoft 'un kuruluşların konumlarından Azure AD, Microsoft hesaplarıyla tüketici alanı ve Xbox ile oyun oynarken kullanıcılarınızı koruduğu dersleri kullanır. Microsoft analizler 6.500.000.000.000 sinyalleri, müşterileri tespit etmek ve tehditlere karşı korumak için günde.

Tarafından oluşturulan ve kimlik koruması üzerinden yapılan sinyaller, erişim kararları vermek için koşullu erişim gibi araçlara daha fazla eklenebilir veya kuruluşunuzun zorlanan ilkelerine göre daha fazla araştırma için bir güvenlik bilgileri ve olay yönetimi (SıEM) aracına geri gönderilir.

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

| Risk algılama türü | Description |
| --- | --- |
| Anonim IP adresi | Anonim bir IP adresinden oturum açın (örneğin: Tor tarayıcısı, anonimleştirici VPN 'Ler). |
| Olağandışı yolculuk | Kullanıcının en son oturum açma işlemlerini temel alarak sıradan bir konumdan oturum açın. |
| Kötü amaçlı yazılım bağlı IP adresi | Kötü amaçlı yazılımdan bağlantılı bir IP adresinden oturum açın. |
| Bilinmeyen oturum açma özellikleri | Belirtilen kullanıcı için son gördüğdiğimiz özelliklerle oturum açın. |
| Sızdırılan kimlik bilgileri | Kullanıcının geçerli kimlik bilgilerinin sızdırdığını gösterir. |
| Parola spreyi | Birden çok Kullanıcı adı Birleşik, deneme yanılma sırasında ortak parolaları kullanarak saldırıya uğradığını gösterir. |
| Azure AD tehdit bilgileri | Microsoft 'un dahili ve dış tehdit bilgileri kaynakları, bilinen bir saldırı modelini tanımladı. |
| Yeni ülke | Bu algılama [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#activity-from-infrequent-country)tarafından keşfedilir. |
| Anonim IP adresinden etkinlik | Bu algılama [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#activity-from-anonymous-ip-addresses)tarafından keşfedilir. |
| Şüpheli gelen kutusu iletme | Bu algılama [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-forwarding)tarafından keşfedilir. |

Bu riskler hakkında daha fazla ayrıntı ve bunların hesaplanmaları, makalede [risk](concept-identity-protection-risks.md)altında bulunabilir.

Risk sinyalleri, kullanıcıların şunları yapmasını isteme gibi düzeltme çalışmalarını tetikleyebilir: Azure AD Multi-Factor Authentication gerçekleştirme, self servis parola sıfırlama kullanarak parolalarını sıfırlama veya bir yönetici tarafından işlem yapana kadar engelleme.

## <a name="risk-investigation"></a>Risk Araştırması

Yöneticiler algılamaları gözden geçirebilir ve gerekirse el ile eylem gerçekleştirebilir. Yöneticilerin kimlik koruması 'nda araştırmalar için kullandığı üç temel rapor vardır:

- Riskli kullanıcılar
- Riskli oturum açma işlemleri
- Risk algılamaları

Makalesinde daha fazla bilgi bulabilirsiniz [: riski araştırma](howto-identity-protection-investigate-risk.md).

### <a name="risk-levels"></a>Risk düzeyleri

Kimlik koruması, riski üç katmana kategorilere ayırır: düşük, orta ve yüksek. 

Microsoft, riskin nasıl hesaplandığına ilişkin belirli ayrıntıları sağlamadığında, her bir düzeyin, kullanıcının veya oturum açma güvenliğinin tehlikeye girdiği daha yüksek güvenilirliğe neden olduğunu varsayalım. Örneğin, bir kullanıcı için bilmediğiniz bir oturum açma özelliklerinin bir örneği, başka bir kullanıcı için sızdırıcı kimlik bilgileri olarak tehdit etmeyebilir.

## <a name="exporting-risk-data"></a>Risk verileri dışarı aktarılıyor

Kimlik Koruması verileri, arşiv ve daha fazla araştırma ve bağıntı için diğer araçlara aktarılabilir. Microsoft Graph tabanlı API 'Ler, kuruluşların bu verileri SıEM gibi bir araçla daha fazla işlenmek üzere toplamasına olanak tanır. Kimlik koruma API 'sine erişme hakkında bilgiler, [Azure Active Directory kimlik koruması ve Microsoft Graph kullanmaya başlama](howto-identity-protection-graph-api.md) hakkında bilgi edinebilirsiniz.

Kimlik koruması bilgilerini Azure Sentinel ile tümleştirme hakkında bilgi için, [Azure AD kimlik koruması verileri bağlama](../../sentinel/connect-azure-ad-identity-protection.md)makalesinde bulabilirsiniz.

## <a name="permissions"></a>İzinler

Kimlik koruması, kullanıcıların erişebilmeleri için bir güvenlik okuyucusu, güvenlik operatörü, Güvenlik Yöneticisi, genel okuyucu veya küresel yönetici olmasını gerektirir.

| Rol | Yapılabilir | Yapılamıyor |
| --- | --- | --- |
| Genel yönetici | Kimlik korumasına tam erişim |   |
| Güvenlik yöneticisi | Kimlik korumasına tam erişim | Kullanıcı parolasını sıfırlama |
| Güvenlik operatörü | Tüm kimlik koruması raporlarını ve genel bakış dikey penceresini görüntüleyin <br><br> Kullanıcı riskini kapatın, güvenli oturum açma 'yı onaylayın, güvenliği tehlikeye atabilir | İlkeleri yapılandırma veya değiştirme <br><br> Kullanıcı parolasını sıfırlama <br><br> Uyarı yapılandırma |
| Güvenlik okuyucusu | Tüm kimlik koruması raporlarını ve genel bakış dikey penceresini görüntüleyin | İlkeleri yapılandırma veya değiştirme <br><br> Kullanıcı parolasını sıfırlama <br><br> Uyarı yapılandırma <br><br> Algılamalar hakkında geri bildirimde bulunun |

Şu anda güvenlik operatörü rolü riskli oturum açma işlemleri raporuna erişemez.

Koşullu erişim yöneticileri, bir koşul olarak oturum açma riskini gösteren ilkeler de oluşturabilir. [Koşullu erişim: koşullar](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)makalesinde daha fazla bilgi bulabilirsiniz.

## <a name="license-requirements"></a>Lisans gereksinimleri

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Özellik | Ayrıntılar  | Azure AD Ücretsiz/Microsoft 365 uygulamalar | Azure AD Premium P1|Azure AD Premium P2 |
| --- | --- | --- | --- | --- |
| Risk ilkeleri | Kullanıcı risk ilkesi (kimlik koruması aracılığıyla)  | Hayır | Hayır |Yes | 
| Risk ilkeleri | Oturum açma risk ilkesi (kimlik koruması veya koşullu erişim aracılığıyla)  | Hayır |  Hayır |Yes |
| Güvenlik raporları | Genel Bakış |  Hayır | Hayır |Yes |
| Güvenlik raporları | Riskli kullanıcılar  | Sınırlı bilgi. Yalnızca orta ve yüksek riskli kullanıcılar gösterilir. Ayrıntılı çekmece veya risk geçmişi yoktur. | Sınırlı bilgi. Yalnızca orta ve yüksek riskli kullanıcılar gösterilir. Ayrıntılı çekmece veya risk geçmişi yoktur. | Tam erişim|
| Güvenlik raporları | Riskli oturum açma işlemleri  | Sınırlı bilgi. Risk ayrıntısı veya risk düzeyi gösterilmez. | Sınırlı bilgi. Risk ayrıntısı veya risk düzeyi gösterilmez. | Tam erişim|
| Güvenlik raporları | Risk algılamaları   | No | Sınırlı bilgi. Ayrıntı çekmecesi yok.| Tam erişim|
| Bildirimler | Risk altındaki kullanıcılar uyarılar tespit etti  | Hayır | Hayır |Yes |
| Bildirimler | Haftalık Özet| Hayır | Hayır | Yes | 
| | Çok faktörlü kimlik doğrulaması kayıt ilkesi | Hayır | Hayır | Yes |

Bu zengin raporlarla ilgili daha fazla bilgiyi makalesinde bulabilirsiniz [: riski araştırma](howto-identity-protection-investigate-risk.md#navigating-the-reports).

## <a name="next-steps"></a>Sonraki adımlar

- [Güvenliğe genel bakış](concept-identity-protection-security-overview.md)

- [Risk nedir?](concept-identity-protection-risks.md)

- [Riskleri azaltmak için kullanılabilir ilkeler](concept-identity-protection-policies.md)
