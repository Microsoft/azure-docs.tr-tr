---
title: Risk nedir? Azure AD Kimlik Koruması
description: Azure AD Kimlik Koruması risk hakkında
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fd0b562ae0570917c9127e301964d089d49b087
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887032"
---
# <a name="what-is-risk"></a>Risk nedir?

Azure AD Kimlik Koruması risk algılamaları, dizindeki Kullanıcı hesaplarıyla ilgili tüm tanınan şüpheli eylemleri içerir.

Kimlik koruması, kuruluşların bu şüpheli eylemlere kolayca bakmak ve yanıt vermesi için güçlü kaynaklara erişmesini sağlar. 

![Riskli kullanıcıları ve oturum açma işlemlerini gösteren güvenliğe genel bakış](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>Risk türleri ve algılama

İki tür risk **kullanıcısı** ve **oturum açma** ve iki tür algılama veya hesaplama **Gerçek** zamanlı ve **çevrimdışı**olabilir.

### <a name="user-risk"></a>Kullanıcı riski

Bir Kullanıcı riski, belirli bir kimliğin veya hesabın güvenliğinin aşıldığına ilişkin olasılığı temsil eder. 

Bu riskler, Microsoft 'un güvenlik araştırmacıları, hukuk uzmanları, Microsoft 'ta güvenlik takımları ve diğer güvenilen kaynaklar dahil olmak üzere Microsoft 'un dahili ve harici tehdit bilgileri kaynakları kullanılarak çevrimdışı olarak hesaplanır.

| Risk algılama | Açıklama |
| --- | --- |
| Sızdırılan kimlik bilgileri | Bu risk algılama türü, kullanıcının geçerli kimlik bilgilerinin sızdırdığını gösterir. Sidolandırıcılar meşru kullanıcıların geçerli parolalarını tehlikeye ayorsa, genellikle bu kimlik bilgilerini paylaşır. Bu paylaşım genellikle koyu Web üzerinden genel olarak, siteler yapıştırarak veya ticari ve kimlik bilgilerini siyah pazarda satarak yapılır. Microsoft sızdırılan kimlik bilgileri hizmeti, koyu Web, siteleri veya diğer kaynaklardan Kullanıcı kimlik bilgilerini edindiğinde, geçerli eşleşmeleri bulmak için Azure AD kullanıcılarının geçerli geçerli kimlik bilgileriyle denetlenir. |
| Azure AD tehdit bilgileri | Bu risk algılama türü, belirli bir kullanıcı için olağandışı olan veya Microsoft 'un dahili ve dış tehdit bilgileri kaynaklarına dayanan bilinen saldırı desenleriyle tutarlı olan kullanıcı etkinliklerini gösterir. |

### <a name="sign-in-risk"></a>Oturum açma riski

Bir oturum açma riski, belirli bir kimlik doğrulama isteğinin kimlik sahibi tarafından yetkilendirilmemiş olma olasılığını temsil eder. 

Bu riskler, gerçek zamanlı olarak hesaplanabilir veya güvenlik araştırmacıları, hukuk uzmanları, Microsoft 'taki güvenlik ekipleri ve diğer güvenilen kaynaklar dahil olmak üzere Microsoft 'un dahili ve harici tehdit bilgileri kaynakları kullanılarak, çevrimdışı olarak hesaplanabilir.

| Risk algılama | Algılama türü | Açıklama |
| --- | --- | --- |
| Anonim IP adresi | Gerçek zamanlı | Bu risk algılama türü, anonim bir IP adresinden (örneğin, Tor tarayıcısı veya anonim VPN) oturum açma işlemlerini gösterir. Bu IP adresleri genellikle kötü amaçlı olabilecek bir amaç için oturum açma telemetrisini (IP adresi, konum, cihaz vb.) gizlemek isteyen aktörler tarafından kullanılır. |
| Atipik seyahat | Çevrimdışı | Bu risk algılama türü, coğrafi olarak uzak konumlardan kaynaklanan iki oturum açma işlemini tanımlar. Bu konumlar, konumların en az birinin Kullanıcı için de normal bir şekilde (geçmiş davranışa göre) de olabileceğini belirtir. Bu makine öğrenimi algoritması, bu iki oturum açma işlemi arasındaki süreyi ve kullanıcının ilk konumdan ikincisine geçebileceği süreyi, farklı bir kullanıcının aynı kullandığını belirten şekilde hesaba götürüyordu. Credentials. <br><br> Algoritma, kuruluştaki diğer kullanıcılar tarafından düzenli olarak kullanılan VPN 'Ler ve konumlar gibi mümkün olmayan seyahat koşullarına katkıda bulunan açık "yanlış pozitif sonuçlar" i yoksayar. Sistemin ilk öğrenme süresi en fazla 14 gün veya 10 oturum açma, yeni bir kullanıcının oturum açma davranışını öğreniyor. |
| Kötü amaçlı yazılım bağlı IP adresi | Çevrimdışı | Bu risk algılama türü, bir bot sunucusuyla etkin bir şekilde iletişim kuran bilinen kötü amaçlı yazılımdan etkilenen IP adreslerinden oturum açma işlemlerini gösterir. Bu algılama, Kullanıcı cihazının IP adresleri, bot sunucusu etkinken bir bot sunucusu ile iletişim içinde olan IP adreslerine göre belirlenir. |
| Bilmediğiniz oturum açma özellikleri | Gerçek zamanlı | Bu risk algılama türü, anormal oturum açma işlemlerini aramak için geçen oturum açma geçmişini (IP, Enlem/Boylam ve ASN) dikkate alır. Sistem, bir kullanıcı tarafından kullanılan önceki konumlara ilişkin bilgileri depolar ve bu "tanıdık" konumları dikkate alır. Risk algılama, oturum açma, zaten tanıdık konumlar listesinde olmayan bir konumdan gerçekleştirildiğinde tetiklenir. Yeni oluşturulan kullanıcılar "öğrenme modunda", algoritmalarımız kullanıcının davranışını öğrenirken, bilinmeyen oturum açma özelliklerinin risk algılamalarının devre dışı bırakıldığına ilişkin bir süre için "öğrenme modunda" olacaktır. Öğrenme modu süresi dinamiktir ve kullanıcının oturum açma desenleriyle ilgili yeterli bilgi toplamak için algoritmanın ne kadar zaman aldığını bağlıdır. En kısa süre beş gündür. Bir Kullanıcı, uzun bir süre etkinlik dışı kaldıktan sonra öğrenme moduna geçebilir. Sistem ayrıca tanıdık cihazlardan oturum açma işlemlerini ve coğrafi olarak tanıdık bir konuma yakın konumları yoksayar. <br><br> Bu algılamayı Ayrıca temel kimlik doğrulaması (veya eski protokoller) için de çalıştırdık. Bu protokollerin istemci KIMLIĞI gibi modern özellikleri olmadığından, hatalı pozitif sonuçları azaltmak için sınırlı telemetri vardır. Müşterilerimizin modern kimlik doğrulamasına taşınmasını öneririz. |
| Yönetici tarafından onaylanan Kullanıcı güvenliği aşılmış | Çevrimdışı | Bu algılama, bir yöneticinin riskli kullanıcılar Kullanıcı arabiriminde veya riskyUsers API 'sini kullanarak ' Kullanıcı güvenliğinin aşılmasına karşı ' yı seçtiğinizi gösterir. Bu kullanıcının hangi yönetici tarafından tehlikede olduğunu görmek için kullanıcının risk geçmişini (UI veya API aracılığıyla) denetleyin. |
| Kötü amaçlı IP adresi | Çevrimdışı | Bu algılama, kötü amaçlı bir IP adresinden oturum açma işlemini gösterir. IP adresi veya diğer IP saygınlığı kaynaklarından alınan geçersiz kimlik bilgileri nedeniyle, bir IP adresi yüksek hata ücretleri temelinde kötü amaçlı olarak değerlendirilir. |

### <a name="other-risk-detections"></a>Diğer risk algılamaları

| Risk algılama | Algılama türü | Açıklama |
| --- | --- | --- |
| Ek risk algılandı | Gerçek zamanlı veya çevrimdışı | Bu algılama, yukarıdaki Premium algılamalardan birinin algılandığını gösterir. Premium algılamalar yalnızca Azure AD Premium P2 müşterilerine görünür olduğundan, müşteriler için Azure AD Premium P2 lisansları olmayan "ek risk algılandı" olarak adlandırılmış olurlar. |

## <a name="next-steps"></a>Sonraki adımlar

- [Riskleri azaltmak için kullanılabilir ilkeler](concept-identity-protection-policies.md)

- [Güvenliğe genel bakış](concept-identity-protection-security-overview.md)
