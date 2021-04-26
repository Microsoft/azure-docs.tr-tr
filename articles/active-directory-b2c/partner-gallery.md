---
title: Azure AD B2C için ISV Iş ortağı Galerisi
titleSuffix: Azure AD B2C
description: Son Kullanıcı deneyiminizi gereksinimlerinize göre uyarlamak için ISV iş ortaklarımız ile tümleştirmeyi öğrenin. İş ortağı ağımız çözüm olanaklarımızı genişletiyor; MFA 'yı etkinleştirme, güvenli müşteri kimlik doğrulaması, rol tabanlı erişim denetimi; Kimlik doğrulama yazım denetimi aracılığıyla sahtekarlık ile mücadele edin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b555c8651c6e1608193a6ae06c39b20f633e4ea9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813677"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>Azure Active Directory B2C ISV iş ortakları

ISV iş ortağı ağı, sorunsuz son kullanıcı deneyimleri oluşturmanıza yardımcı olmak için çözüm yeteneklerini genişletiyor. Azure AD B2C, çok faktörlü kimlik doğrulaması (MFA) yöntemlerini etkinleştirmek, rol tabanlı erişim denetimi yapmak, kimlik doğrulama ve sağlamayı etkinleştirmek, bot algılama ve sahtekarlık koruması ile güvenliği artırmak ve ödeme hizmetleri yönergesi 2 (PSD2) güvenli müşteri kimlik doğrulaması (SCA) gereksinimlerini karşılamak için ISV iş ortakları ile tümleştirilebilir. Uygulamaları ISV iş ortaklarıyla tümleştirme hakkında bilgi edinmek için ayrıntılı örnek yönerglerimizi kullanın.

>[!NOTE]
>[GitHub 'daki Azure Active Directory B2C Community sitesi](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/) , topluluktan örnek özel ilkeler de sağlar.

## <a name="identity-verification-and-proofing"></a>Kimlik doğrulama ve sağlama

Kimlik doğrulama ve sağlama için aşağıdaki ISV 'Lere sahip Microsoft iş ortakları.

| ISV iş ortağı | Açıklama ve tümleştirme izlenecek yolları |
|:-------------------------|:--------------|
|![Bir Experian logosunun ekran görüntüsü.](./media/partner-gallery/experian-logo.png) | [Experian](./partner-experian.md) , sahtekarlık önlemek için Kullanıcı özniteliklerine dayalı risk değerlendirmeleri gerçekleştiren bir kimlik doğrulama ve sağlama sağlayıcıdır. |
|![Bir IDology logosunun ekran görüntüsü.](./media/partner-gallery/idology-logo.png) | [IDology](./partner-idology.md) kimlik doğrulama çözümlerini, sahtekarlık önleme çözümlerini, uyumluluk çözümlerini ve diğerlerini içeren bir kimlik doğrulama ve sağlama sağlayıcıdır.|
|![Jumio logosunun ekran görüntüsü.](./media/partner-gallery/jumio-logo.png) | [Jumio](./partner-jumio.md) , gerçek ZAMANLı otomatik kimlik doğrulamaya izin veren ve müşteri verilerini koruma sağlayan bir kimlik doğrulama hizmetidir. |
| ![Lexisnexof logosunun ekran görüntüsü.](./media/partner-gallery/lexisnexis-logo.png) | [Lexisnex,](./partner-lexisnexis.md) Kullanıcı kimliğini doğrulayan ve kullanıcının cihazına göre kapsamlı risk değerlendirmesi sağlayan bir profil oluşturma ve kimlik doğrulama sağlayıcısıdır. |
| ![Onfido logosunun ekran görüntüsü](./media/partner-gallery/onfido-logo.png) | [Onfido](./partner-onfido.md) , şirketlerin müşteri ve kimlik gereksinimlerinizi gerçek zamanlı olarak *bilmesini* sağlayan bir belge kimliği ve yüz Biyometri doğrulama çözümüdür.  |

## <a name="mfa-and-passwordless-authentication"></a>MFA ve passwordless kimlik doğrulaması

MFA ve passwordless kimlik doğrulaması için aşağıdaki ISV 'Ler ile Microsoft iş ortakları.

| ISV iş ortağı | Açıklama ve tümleştirme izlenecek yolları |
|:-------------------------|:--------------|
| ![Hypr logosunun ekran görüntüsü](./media/partner-gallery/hypr-logo.png) | [Hypr](./partner-hypr.md) , ortak anahtar şifrelemeleri olan parolaların sahteciliği, kimlik avlarını ve kimlik bilgilerini yeniden kullanımını ortadan kaldıran, parolasız bir kimlik doğrulama sağlayıcısıdır. |
| ![Itsme logosunun ekran görüntüsü](./media/partner-gallery/itsme-logo.png) | [ıtsme](./partner-itsme.md) , kullanıcıların kart okuyucuları, parolalar, iki öğeli kimlik doğrulaması ve bırden çok PIN kodu olmadan güvenli bir şekilde oturum açmalarına olanak tanımak Için bir elektronik tanımlama, kimlik doğrulama ve güven Hizmetleri (eidas) ile uyumlu dijital kimlik çözümüdür. |
|![Keyless logosunun ekran görüntüsü.](./media/partner-gallery/keyless-logo.png) | [Keyless](./partner-keyless.md) , bir yüz Biyometrik tarama biçiminde kimlik doğrulaması sağlayan ve sahtekarlık, kimlik avı ve kimlik bilgisi yeniden kullanımını ortadan kaldıran, passwordless bir kimlik doğrulama sağlayıcısıdır.
| ![Nevis logosunun ekran görüntüsü](./media/partner-gallery/nevis-logo.png) | [Nevis](./partner-nevis.md) , passwordless kimlik doğrulamasını sağlar ve güçlü müşteri kimlik doğrulaması IÇIN ve PSD2 işlem gereksinimleriyle uyum sağlamak üzere Nevis Access uygulamasıyla mobil ve tamamen markalı Son Kullanıcı deneyimi sağlar. |
| ![Trusona logosunun ekran görüntüsü](./media/partner-gallery/trusona-logo.png) | [Truslonıntegration](./partner-trusona.md) , güvenli bir şekilde oturum açmanıza ve passwordless kimlik doğrulaması, MFA ve dijital lisans taramasını başlatmanıza yardımcı olur. |
| ![Bir Twilio logosunun ekran görüntüsü.](./media/partner-gallery/twilio-logo.png) | [Twilio Verify App](./partner-twilio.md) , SMS tek seferlik parola (OTP), zaman tabanlı bir kerelik parola (TOTP) ve anında iletme BILDIRIMLERI aracılığıyla MFA 'yı ETKINLEŞTIRMEK ve PSD2 için SCA gereksinimleriyle uyum sağlamak için birden çok çözüm sağlar. |
| ![TypingDNA logosunun ekran görüntüsü](./media/partner-gallery/typingdna-logo.png) | [Typingdna](./partner-typingdna.md) , bir kullanıcının yazma modelini çözümleyerek güçlü müşteri kimlik doğrulamasını mümkün bir şekilde sunar. Şirketlerin sessiz MFA 'yı etkinleştirmesine ve PSD2 için SCA gereksinimleriyle uyumlu hale belirlemesine yardımcı olur. |
| ![Whoıam logosunun ekran görüntüsü](./media/partner-gallery/whoiam-logo.png) | [Whoıam](./partner-whoiam.md) , kuruluşların Kullanıcı temelini seslı, SMS ve e-posta ile doğrulamasını sağlayan bir markalı kimlik yönetimi SISTEMI (brims) uygulamasıdır. |

## <a name="role-based-access-control"></a>Rol tabanlı erişim denetimi 
 
Rol tabanlı erişim denetimi için aşağıdaki ISV 'Ler ile Microsoft iş ortakları.

| ISV iş ortağı | Açıklama ve tümleştirme izlenecek yolları |
|:-------------------------|:--------------|
| ![N8identity logosunun ekran görüntüsü](./media/partner-gallery/n8identity-logo.png) | [N8Identity](./partner-n8identity.md) , Microsoft Azure üzerinde çalışan müşteri hesapları geçişi ve müşteri hizmet ISTEKLERI (CSR) yönetimine yönelik çözüm sağlayan bir hizmet olarak kimlik idare platformudur. |
| ![Saviynt logosunun ekran görüntüsü](./media/partner-gallery/saviynt-logo.png) | [Saviynt](./partner-Saviynt.md) Cloud-Native platform akıllı analizler aracılığıyla daha iyi güvenlik, uyumluluk ve Idare ve BT modernleştirmeyi kolaylaştırmak için Uygulama tümleştirmesini yükseltir. |

## <a name="secure-hybrid-access-to-on-premises-application"></a>Şirket içi uygulamaya güvenli karma erişim

Microsoft, şirket içi uygulamaya güvenli karma erişim sağlamak için aşağıdaki ISV 'Lere sahip iş ortakları. 

| ISV iş ortağı | Açıklama ve tümleştirme izlenecek yolları |
|:-------------------------|:--------------|
| ![Ping logosunun ekran görüntüsü](./media/partner-gallery/ping-logo.png) | [Ping kimliği](./partner-ping-identity.md) , birden çok bulutta şirket içi eski uygulamalara güvenli karma erişim sağlar. |
| ![Bir konuşmasıyla Strata logosunun ekran görüntüsü](./media/partner-gallery/strata-logo.png) | [Strata](./partner-strata.md) , tutarlı erişim ilkeleri zorunlu kılarak, kimlikleri eşitlenmiş halde tutarak ve eski kimlik sistemlerinden uygulamaları Azure AD B2C tarafından sağlanan erişim denetimine geçirmeye basit hale getirerek şirket içi uygulamalara güvenli karma erişim sağlar. |
| ![Zscaler logosunun ekran görüntüsü](./media/partner-gallery/zscaler-logo.png) | [Zscaler](./partner-zscaler.md) , bir VPN 'nin maliyet, sorun veya güvenlik riskleri olmadan özel uygulamalara ve varlıklara ilke tabanlı, güvenli erişim sağlar. |

## <a name="fraud-protection"></a>Sahtekarlık koruması

Microsoft iş ortakları, sahtekarlık algılama ve önleme için aşağıdaki ISV 'Lere sahiptir. 

| ISV iş ortağı | Açıklama ve tümleştirme izlenecek yolları |
|:-------------------------|:--------------|
| ![Bir Arkoo Lab logosunun ekran görüntüsü](./media/partner-gallery/arkose-logo.png) | [Arkoo Labs](./partner-arkose-labs.md) , kuruluşların bot saldırılarına karşı korunmasına yardımcı olan bir sahtekarlık önleme çözümü sağlayıcısıdır, hesap, saldırıları ve sahte hesap açılışlarına karşı koruma sağlar. |
| ![BioCatch logosunun ekran görüntüsü](./media/partner-gallery/biocatch-logo.png) | [Biocatch](./partner-biocatch.md) , meşru müşteriler ve siber dolandırıcılıkları birbirinden ayırt eden Öngörüler oluşturmak için bir kullanıcının fiziksel ve bilişsel dijital davranışlarını çözümleyen bir sahtekarlık engellemesi çözüm sağlayıcısıdır. |
| ![Microsoft Dynamics 365 logosunun ekran görüntüsü](./media/partner-gallery/microsoft-dynamics365-logo.png) | [Microsoft Dynamics 365 sahtekarlık koruması](./partner-dynamics-365-fraud-protection.md) , kuruluşların cihaz parmak izi aracılığıyla sahte hesap açılışlarına karşı korunmasına yardımcı olan bir çözümdür. |


## <a name="additional-information"></a>Ek bilgiler

- [Azure AD B2C'deki özel ilkeler](./custom-policy-overview.md)

- [Azure AD B2C özel ilkeleri kullanmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

## <a name="next-steps"></a>Sonraki adımlar

Çözümlerini Azure AD B2C ile tümleştirmeyi öğrenmek için bahsedilen tablolarda bir iş ortağı seçin.
