---
title: Azure Active Directory Kimlik Koruması sözlüğü
description: Azure Active Directory Kimlik Koruması sözlüğü
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5439b8626d1fa0ea502d33455db8b2564fbd45f0
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72886928"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory Kimlik Koruması sözlüğü

### <a name="at-risk-user"></a>Risk altında (Kullanıcı)
Bir veya daha fazla etkin risk algılamaları olan bir kullanıcı. 

### <a name="atypical-sign-in-location"></a>Atipik oturum açma konumu
Belirli bir Kullanıcı, benzer kullanıcılar veya kiracı için tipik olmayan coğrafi bir konumdan oturum açma.

### <a name="azure-ad-identity-protection"></a>Azure AD Kimlik Koruması
Risk algılamaları ve kuruluşun kimliklerini etkileyen olası güvenlik açıklarına birleştirilmiş bir görünüm sağlayan Azure Active Directory güvenlik modülü.

### <a name="conditional-access"></a>Koşullu Erişim
Kaynaklara erişimi güvenli hale getirmek için bir ilke. Koşullu erişim kuralları Azure Active Directory depolanır ve kaynağa erişim izni vermeden önce Azure AD tarafından değerlendirilir.  Örnek kurallar, Kullanıcı konumuna, cihaz durumuna veya Kullanıcı kimlik doğrulama yöntemine göre erişimi kısıtlamayı içerir.

### <a name="credentials"></a>Kimlik bilgileri
Yerel ve ağ kaynaklarına erişim kazanmak için kullanılan tanımlama ve kimlik kanıtı bilgilerini içeren bilgiler. Kimlik bilgileri örnekleri Kullanıcı adları ve parolalar, akıllı kartlar ve sertifikalardır.

### <a name="event"></a>Olay
Azure Active Directory bir etkinliğin kaydı.

### <a name="false-positive-risk-detection"></a>False-pozitif (risk algılama)
Risk algılamada bir kimlik koruması kullanıcısı tarafından el ile ayarlanan ve risk algılamanın araştırıldığına ve yanlış bir risk algılaması olarak işaretlenmediğini belirten risk algılama durumu.

### <a name="identity"></a>Kimlik
Parola veya sertifika gibi ölçütlere bağlı olarak, kimlik doğrulama yoluyla doğrulanması gereken bir kişi veya varlık.

### <a name="identity-risk-detection"></a>Kimlik riski algılama
Kimlik koruması tarafından anormal olarak işaretlenen AAD olayı ve bir kimliğin tehlikeye girdiği anlamına gelebilir.

### <a name="ignored-risk-detection"></a>Yoksayıldı (risk algılama)
Bir kimlik koruma kullanıcısı tarafından el ile ayarlanan bir risk algılama durumu, risk algılamanın bir düzeltme eylemi yapılmadan kapatıldığını gösterir.

### <a name="impossible-travel-from-atypical-locations"></a>Atipik konumlardan mümkün olmayan seyahat
Aynı kullanıcı için iki oturum açma algılandığında tetiklenen bir risk algılama, en az bir tane bir adet bir oturum açma konumundan olduğu ve oturum açma işlemleri arasındaki sürenin, bunlar arasında fiziksel olarak seyahat altına alabileceği en kısa süreden kısa olduğu durumlarda tetiklenir. yerlerini.  

### <a name="investigation"></a>Araştırma
Düzeltme veya azaltma adımlarının gerekli olup olmadığına karar vermek için risk algılamasıyla ilgili etkinlikleri, günlükleri ve diğer ilgili bilgileri gözden geçirme işlemi, kimliğin nasıl ve nasıl tehlikede olduğunu anlayın ve tehlikede nasıl riskli olduğunu anlayın kimlik kullanıldı.

### <a name="leaked-credentials"></a>Sızdırılan kimlik bilgileri
Geçerli Kullanıcı kimlik bilgileri (Kullanıcı adı ve parola), araştırmacılarımız tarafından koyu Web 'de herkese açık olarak bulunduğunda tetiklenen bir risk algılama.

### <a name="mitigation"></a>Risk azaltma
Bir saldırganın kimliği veya cihazı güvenli bir duruma geri yüklemeden güvenliği aşılmış bir kimlik veya cihazdan yararlanma yeteneğini sınırlamak veya ortadan kaldırmak için bir eylem. Risk azaltma, kimlik veya cihazla ilişkili önceki risk algılamalarını çözmez.

### <a name="multi-factor-authentication"></a>Çok faktörlü kimlik doğrulama
İki veya daha fazla kimlik doğrulama yöntemi gerektiren bir kimlik doğrulama yöntemi, örneğin bir sertifika, kullanıcının sahip olduğu bir şeyi içerebilir; Kullanıcı adları, parolalar veya geçiş ifadeleri gibi kullanıcıların bildiği bir şey; parmak izi gibi fiziksel öznitelikler; Kişisel imza gibi kişisel öznitelikleri.

### <a name="offline-detection"></a>Çevrimdışı algılama
Daha önce gerçekleşmiş bir olay için, olgudan sonra oturum açma girişimi gibi bir olay riskini algılama ve değerlendirmesi.

### <a name="policy-condition"></a>İlke koşulu
İlke içinde yer alan veya gruptan çıkarılan varlıkları (gruplar, kullanıcılar, uygulamalar, cihaz platformları, cihaz durumları, IP aralıkları, istemci türleri) tanımlayan bir güvenlik ilkesinin parçası.

### <a name="policy-rule"></a>İlke kuralı
Bir güvenlik ilkesinin ilkeyi tetikleyen koşulları ve ilke tetiklendiğinde gerçekleştirilecek eylemleri açıklayan bölümü.

### <a name="prevention"></a>Önleme
Bir kimlik veya cihazın kötüye kullanımı aracılığıyla kuruluşa zarar verme veya tehlikeye geçme durumunu önleme eylemi. Bir önleme eylemi, cihazın veya kimliğin güvenliğini sağlamaz ve önceki risk algılamalarını çözmez.

### <a name="privileged-user"></a>Ayrıcalıklı (Kullanıcı)
Risk algılaması sırasında bir Kullanıcı, bir veya daha fazla Azure Active Directory, genel yönetici, faturalandırma Yöneticisi, hizmet yöneticisi, Kullanıcı Yöneticisi ve parola gibi bir veya daha fazla kaynak için kalıcı veya geçici yönetici izinlerine sahiptir Danışın. 

### <a name="real-time"></a>Gerçek zamanlı
Bkz. gerçek zamanlı algılama.

### <a name="real-time-detection"></a>Gerçek zamanlı algılama
Olayın devam etmesini izin vermeden önce oturum açma denemesi gibi bir olay riskini algılama ve değerlendirme.

### <a name="remediated-risk-detection"></a>Düzeltilen (risk algılama)
Risk algılama durumu, kimlik koruması tarafından otomatik olarak ayarlanan, risk algılamanın bu tür risk algılama için standart Düzeltme eylemi kullanılarak düzeltildiğini gösterir. Örneğin, Kullanıcı parolası sıfırlandığında, önceki parolanın güvenliğinin aşıldığını belirten birçok risk algılamaları otomatik olarak düzeltilebilir.

### <a name="remediation"></a>Düzeltmesi
Daha önce şüpheli veya tehlikede bilinen bir kimlik veya cihazın güvenliğini sağlamaya yönelik bir eylem. Düzeltme eylemi, kimlik veya cihazı güvenli bir duruma geri yükler ve kimlik veya cihazla ilişkili önceki risk algılamalarını çözer.

### <a name="resolved-risk-detection"></a>Çözümlendi (risk algılama)
Bir kimlik koruması kullanıcısı tarafından el ile ayarlanan bir risk algılama durumu, kullanıcının kimlik koruması dışında uygun bir düzeltme eylemi gerçekleşmekte olduğunu ve risk algılamanın kapalı kabul edilmesi gerektiğini gösterir.

### <a name="risk-detection-status"></a>Risk algılama durumu
Etkinliğin etkin olup olmadığını ve kapatıldıysa kapatma nedeninizi belirten bir risk algılama özelliği.

### <a name="risk-detection-type"></a>Risk algılama türü
Etkinliğin riskli olarak kabul edilmesine neden olan anomali türünü gösteren risk algılama için bir kategori.

### <a name="risk-level-risk-detection"></a>Risk düzeyi (risk algılama)
Kimlik koruma kullanıcılarının kuruluşlarındaki riski azaltmak için gerçekleştirdikleri eylemlere öncelik vermelerine yardımcı olmak için risk algılamasında önem derecesi (yüksek, orta veya düşük). 

### <a name="risk-level-sign-in"></a>Risk düzeyi (oturum açma)
Belirli bir oturum açma ihtimalinin bir göstergesi (yüksek, orta veya düşük), başka biri de kullanıcının kimliğini kullanmaya çalışıyor.

### <a name="risk-level-user-compromise"></a>Risk düzeyi (Kullanıcı güvenliğinin aşılmasına neden)
Bir kimliğin tehlikeye düşmesi olasılığının bir göstergesi (yüksek, orta veya düşük).

### <a name="risk-level-vulnerability"></a>Risk düzeyi (güvenlik açığı)
Kimlik koruması kullanıcılarının kuruluşlarındaki riski azaltmak için gerçekleştirdikleri eylemlere öncelik vermelerine yardımcı olmak üzere güvenlik açığının önem derecesi (yüksek, orta veya düşük).

### <a name="secure-identity"></a>Güvenli (kimlik)
Bir parola değişikliği veya makine yeniden görüntüsü oluşturma gibi bir düzeltme eylemi gerçekleştirmek için, riskli olabilecek bir kimliği tehlikeye ayrılmamış bir duruma geri yükleyin.

### <a name="security-policy"></a>Güvenlik ilkesi
İlke kuralları ve koşul koleksiyonu. Kullanıcılar, gruplar, uygulamalar, cihazlar, cihaz platformları, cihaz durumları, IP aralıkları ve Auth 2.0 istemci türleri gibi varlıklara bir ilke uygulanabilir. Bir ilke etkinleştirildiğinde, ilkeye dahil edilen bir varlık bir kaynak için belirteç verildiğinde değerlendirilir.

### <a name="sign-in-v"></a>Oturum aç (v)
Azure Active Directory bir kimliğin kimliğini doğrulamak için.

### <a name="sign-in-n"></a>Oturum açma (n)
Azure Active Directory bir kimliğin kimliğini doğrulayan işlem veya eylem ve bu işlemi yakalayan olay.

### <a name="sign-in-from-anonymous-ip-address"></a>Anonim IP adresinden oturum açın
Bir anonim proxy IP adresi olarak tanımlanan IP adresinden başarılı bir oturum açma işleminden sonra tetiklenen bir risk algılama işlemi.

### <a name="sign-in-from-infected-device"></a>Virüslü cihazdan oturum aç
Bir oturum açma işlemi bir IP adresinden kaynaklanıyorsa, bir veya daha fazla güvenliği aşılmış bir cihaz tarafından kullanılmak üzere bilinen ve bir bot sunucusuyla iletişim kurmaya çalışan bir risk algılamasında tetiklenir.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Şüpheli etkinlikle IP adresinden oturum açın
Kısa bir süre içinde birden çok kullanıcı hesabında yüksek sayıda başarısız oturum açma girişimi olan bir IP adresinden başarılı bir oturum açma denemesi sonrasında tetiklenen bir risk algılama işlemi.

### <a name="sign-in-from-unfamiliar-location"></a>Bilmediğiniz konumdan oturum aç
Bir Kullanıcı yeni bir konumdan (IP, Enlem/Boylam ve ASN) başarıyla oturum açtığında tetiklenen bir risk algılama.

### <a name="sign-in-risk"></a>Oturum açma riski
Risk düzeyine bakın (oturum açma)

### <a name="sign-in-risk-policy"></a>Oturum açma risk ilkesi
Belirli bir oturum açma riskini değerlendiren ve önceden tanımlanmış koşullara ve kurallara göre azaltmaları uygulayan koşullu erişim ilkesi.

### <a name="user-compromise-risk"></a>Kullanıcı güvenliğinin aşılmasına karşı risk
Risk düzeyine (Kullanıcı güvenliğinin aşılmasına) bakın

### <a name="user-risk"></a>Kullanıcı riski
Risk düzeyine (Kullanıcı güvenliğinin aşılmasına) bakın.

### <a name="user-risk-policy"></a>Kullanıcı risk ilkesi
Oturum açmayı göz önünde bulundurur ve önceden tanımlanmış koşullara ve kurallara göre azaltmaları uygulayan koşullu erişim ilkesi.

### <a name="users-flagged-for-risk"></a>Riskli oldukları belirlenen kullanıcılar
Etkin veya düzeltilmiş olan risk algılamaları olan kullanıcılar

### <a name="vulnerability"></a>Güvenlik Açığı
Azure Active Directory ' deki bir yapılandırma veya koşul, bu dizin, güvenlik açıklarından veya tehditlere karşı savunmasız hale gelir.

## <a name="see-also"></a>Ayrıca bkz.

- [Azure Active Directory Kimlik Koruması](../active-directory-identityprotection.md)
