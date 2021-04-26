---
title: Risk nedir? Azure AD Kimlik Koruması
description: Azure AD Kimlik Koruması risk hakkında
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87516ddcce32ab205b13139c057a2ab999146b74
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376367"
---
# <a name="what-is-risk"></a>Risk nedir?

Azure AD Kimlik Koruması risk algılamaları, dizindeki Kullanıcı hesaplarıyla ilgili tüm tanınan şüpheli eylemleri içerir.

Kimlik koruması, kuruluşların bu şüpheli eylemlere kolayca bakmak ve yanıt vermesi için güçlü kaynaklara erişmesini sağlar. 

![Riskli kullanıcıları ve oturum açma işlemlerini gösteren güvenliğe genel bakış](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

> [!NOTE]
> Kimlik koruması yalnızca doğru kimlik bilgileri kullanıldığında risk algılamalarını oluşturur. Oturum açma sırasında yanlış kimlik bilgileri kullanılıyorsa, kimlik bilgilerinin güvenliğinin riskini temsil etmez.

## <a name="risk-types-and-detection"></a>Risk türleri ve algılama

İki tür risk **kullanıcısı** ve **oturum açma** ve iki tür algılama veya hesaplama **Gerçek** zamanlı ve **çevrimdışı** olabilir.

Gerçek zamanlı algılamalar, beş ila on dakika raporlama için görüntülenmeyebilir. Çevrimdışı algılamalar iki ile yirmi dört saat için raporlama içinde görüntülenmeyebilir.

### <a name="user-risk"></a>Kullanıcı riski

Bir Kullanıcı riski, belirli bir kimliğin veya hesabın güvenliğinin aşıldığına ilişkin olasılığı temsil eder. 

Bu riskler, Microsoft 'un güvenlik araştırmacıları, hukuk uzmanları, Microsoft 'ta güvenlik takımları ve diğer güvenilen kaynaklar dahil olmak üzere Microsoft 'un dahili ve harici tehdit bilgileri kaynakları kullanılarak çevrimdışı olarak hesaplanır.

| Risk algılama | Açıklama |
| --- | --- |
| Sızdırılan kimlik bilgileri | Bu risk algılama türü, kullanıcının geçerli kimlik bilgilerinin sızdırdığını gösterir. Sidolandırıcılar meşru kullanıcıların geçerli parolalarını tehlikeye ayorsa, genellikle bu kimlik bilgilerini paylaşır. Bu paylaşım genellikle koyu Web üzerinden genel olarak, siteler yapıştırarak veya ticari ve kimlik bilgilerini siyah pazarda satarak yapılır. Microsoft sızdırılan kimlik bilgileri hizmeti, koyu Web, siteleri veya diğer kaynaklardan Kullanıcı kimlik bilgilerini edindiğinde, geçerli eşleşmeleri bulmak için Azure AD kullanıcılarının geçerli geçerli kimlik bilgileriyle denetlenir. Sızdırılan kimlik bilgileri hakkında daha fazla bilgi için bkz. [genel sorular](#common-questions). |
| Azure AD tehdit bilgileri | Bu risk algılama türü, belirli bir kullanıcı için olağandışı olan veya Microsoft 'un dahili ve dış tehdit bilgileri kaynaklarına dayanan bilinen saldırı desenleriyle tutarlı olan kullanıcı etkinliklerini gösterir. |

### <a name="sign-in-risk"></a>Oturum açma riski

Bir oturum açma riski, belirli bir kimlik doğrulama isteğinin kimlik sahibi tarafından yetkilendirilmemiş olma olasılığını temsil eder. 

Bu riskler, gerçek zamanlı olarak hesaplanabilir veya güvenlik araştırmacıları, hukuk uzmanları, Microsoft 'taki güvenlik ekipleri ve diğer güvenilen kaynaklar dahil olmak üzere Microsoft 'un dahili ve harici tehdit bilgileri kaynakları kullanılarak, çevrimdışı olarak hesaplanabilir.

| Risk algılama | Algılama türü | Açıklama |
| --- | --- | --- |
| Anonim IP adresi | Gerçek zamanlı | Bu risk algılama türü, anonim bir IP adresinden (örneğin, Tor tarayıcısı veya anonim VPN) oturum açma işlemlerini gösterir. Bu IP adresleri genellikle kötü amaçlı olabilecek bir amaç için oturum açma telemetrisini (IP adresi, konum, cihaz vb.) gizlemek isteyen aktörler tarafından kullanılır. |
| Olağandışı yolculuk | Çevrimdışı | Bu risk algılama türü, coğrafi olarak uzak konumlardan kaynaklanan iki oturum açma işlemini tanımlar. Bu konumlar, konumların en az birinin Kullanıcı için de normal bir şekilde (geçmiş davranışa göre) de olabileceğini belirtir. Bu makine öğrenimi algoritması, farklı bir kullanıcının aynı kimlik bilgilerini kullandığını belirten, iki oturum açma işlemi ve kullanıcının ilk konumdan ikincisine geçebileceği zaman arasındaki süreyi dikkate alır. <br><br> Algoritma, kuruluştaki diğer kullanıcılar tarafından düzenli olarak kullanılan VPN 'Ler ve konumlar gibi mümkün olmayan seyahat koşullarına katkıda bulunan açık "yanlış pozitif sonuçlar" i yoksayar. Sistemin ilk öğrenme süresi en fazla 14 gün veya 10 oturum açma, yeni bir kullanıcının oturum açma davranışını öğreniyor. |
| Kötü amaçlı yazılım bağlı IP adresi | Çevrimdışı | Bu risk algılama türü, bir bot sunucusuyla etkin bir şekilde iletişim kuran bilinen kötü amaçlı yazılımdan etkilenen IP adreslerinden oturum açma işlemlerini gösterir. Bu algılama, Kullanıcı cihazının IP adresleri, bot sunucusu etkinken bir bot sunucusu ile iletişim içinde olan IP adreslerine göre belirlenir. |
| Bilinmeyen oturum açma özellikleri | Gerçek zamanlı | Bu risk algılama türü, anormal oturum açma işlemlerini aramak için geçen oturum açma geçmişini (IP, Enlem/Boylam ve ASN) dikkate alır. Sistem, bir kullanıcı tarafından kullanılan önceki konumlara ilişkin bilgileri depolar ve bu "tanıdık" konumları dikkate alır. Risk algılama, oturum açma, zaten tanıdık konumlar listesinde olmayan bir konumdan gerçekleştirildiğinde tetiklenir. Yeni oluşturulan kullanıcılar "öğrenme modunda", algoritmalarımız kullanıcının davranışını öğrenirken, bilinmeyen oturum açma özelliklerinin risk algılamalarının devre dışı bırakıldığına ilişkin bir süre için "öğrenme modunda" olacaktır. Öğrenme modu süresi dinamiktir ve kullanıcının oturum açma desenleriyle ilgili yeterli bilgi toplamak için algoritmanın ne kadar zaman aldığını bağlıdır. En kısa süre beş gündür. Bir Kullanıcı, uzun bir süre etkinlik dışı kaldıktan sonra öğrenme moduna geçebilir. Sistem ayrıca tanıdık cihazlardan oturum açma işlemlerini ve coğrafi olarak tanıdık bir konuma yakın konumları yoksayar. <br><br> Bu algılamayı Ayrıca temel kimlik doğrulaması (veya eski protokoller) için de çalıştırdık. Bu protokollerin istemci KIMLIĞI gibi modern özellikleri olmadığından, hatalı pozitif sonuçları azaltmak için sınırlı telemetri vardır. Müşterilerimizin modern kimlik doğrulamasına taşınmasını öneririz. |
| Yönetici tarafından onaylanan Kullanıcı güvenliği aşılmış | Çevrimdışı | Bu algılama, bir yöneticinin riskli kullanıcılar Kullanıcı arabiriminde veya riskyUsers API 'sini kullanarak ' Kullanıcı güvenliğinin aşılmasına karşı ' yı seçtiğinizi gösterir. Bu kullanıcının hangi yönetici tarafından tehlikede olduğunu görmek için kullanıcının risk geçmişini (UI veya API aracılığıyla) denetleyin. |
| Kötü amaçlı IP adresi | Çevrimdışı | Bu algılama, kötü amaçlı bir IP adresinden oturum açma işlemini gösterir. IP adresi veya diğer IP saygınlığı kaynaklarından alınan geçersiz kimlik bilgileri nedeniyle, bir IP adresi yüksek hata ücretleri temelinde kötü amaçlı olarak değerlendirilir. |
| Şüpheli gelen kutusu düzenleme kuralları | Çevrimdışı | Bu algılama [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules)tarafından keşfedilir. Bu algılama, ortamınızı profiller ve iletileri veya klasörleri silen veya taşıyan şüpheli kurallar bir kullanıcının gelen kutusunda ayarlandığında uyarıları tetikler. Bu algılama, Kullanıcı hesabının güvenliğinin aşıldığını, iletilerin kasıtlı olarak gizlendiğini ve posta kutusunun kuruluşunuzda istenmeyen posta veya kötü amaçlı yazılım dağıtmak için kullanıldığını belirtebilir. |
| Parola spreyi | Çevrimdışı | Bir parola spreyi saldırısı, birden çok Kullanıcı adı, Birleşik bir deneme yanılma, yetkisiz erişim elde etmek için ortak parolalar kullanan saldırıya neden olur. Bu risk algılama, bir parola spreyi saldırısı gerçekleştirildiğinde tetiklenir. |
| Mümkün olmayan seyahat | Çevrimdışı | Bu algılama [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#impossible-travel)tarafından keşfedilir. Bu algılama, farklı bir kullanıcının aynı kimlik bilgilerini kullandığını gösteren bir zaman dilimi içinde coğrafi olarak uzak konumların ikinci kez, ilk konumdan saniye içinde hareket etmek için geçen süreden daha kısa bir süre içinde olan iki kullanıcı etkinliğini tanımlar (tek veya birden çok oturumdur). |
| Yeni ülke | Çevrimdışı | Bu algılama [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#activity-from-infrequent-country)tarafından keşfedilir. Bu algılama, geçmiş etkinlik konumlarını göz önünde bulundurur ve yeni ve seyrek bulunan konumları belirlemektir. Anomali algılama altyapısı, kuruluştaki kullanıcılar tarafından kullanılan önceki konumlara ilişkin bilgileri depolar. |
| Anonim IP adresinden etkinlik | Çevrimdışı | Bu algılama [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#activity-from-anonymous-ip-addresses)tarafından keşfedilir. Bu algılama, kullanıcıların anonim proxy IP adresi olarak tanımlanmış bir IP adresinden etkin olduğunu tanımlar. |
| Şüpheli gelen kutusu iletme | Çevrimdışı | Bu algılama [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-forwarding)tarafından keşfedilir. Bu algılama, şüpheli e-posta iletme kurallarını arar, örneğin, bir Kullanıcı tüm e-postaların bir kopyasını bir dış adrese ileten bir gelen kutusu kuralı oluşturmuşsam. |

### <a name="other-risk-detections"></a>Diğer risk algılamaları

| Risk algılama | Algılama türü | Açıklama |
| --- | --- | --- |
| Ek risk algılandı | Gerçek zamanlı veya çevrimdışı | Bu algılama, yukarıdaki Premium algılamalardan birinin algılandığını gösterir. Premium algılamalar yalnızca Azure AD Premium P2 müşterilerine görünür olduğundan, müşteriler için Azure AD Premium P2 lisansları olmayan "ek risk algılandı" olarak adlandırılmış olurlar. |

## <a name="common-questions"></a>Sık sorulan sorular

### <a name="risk-levels"></a>Risk düzeyleri

Kimlik koruması, riski üç katmana kategorilere ayırır: düşük, orta ve yüksek. [Özel kimlik koruma ilkelerini](./concept-identity-protection-policies.md#custom-conditional-access-policy)yapılandırırken, bunu **risk düzeyi olmadan** tetiklenecek şekilde de yapılandırabilirsiniz. Risk yok, kullanıcının kimliğinin tehlikeye girdiği etkin bir gösterge olmadığı anlamına gelir.

Microsoft, riskin nasıl hesaplandığına ilişkin belirli ayrıntıları sağlamadığında, her bir düzeyin, kullanıcının veya oturum açma güvenliğinin tehlikeye girdiği daha yüksek güvenilirliğe neden olduğunu varsayalım. Örneğin, bir kullanıcı için bilmediğiniz bir oturum açma özelliklerinin bir örneği, başka bir kullanıcı için sızdırıcı kimlik bilgileri olarak tehdit etmeyebilir.

### <a name="password-hash-synchronization"></a>Parola karması eşitleme

Sızdırılan kimlik bilgileri gibi risk algılamaları, algılamanın gerçekleşmesi için parola karmaları varlığını gerektirir. Parola karması eşitlemesi hakkında daha fazla bilgi için, [Azure AD Connect eşitleme ile parola karması eşitlemeyi uygulama](../hybrid/how-to-connect-password-hash-synchronization.md)başlıklı makaleye bakın.

### <a name="leaked-credentials"></a>Sızdırılan kimlik bilgileri

#### <a name="where-does-microsoft-find-leaked-credentials"></a>Microsoft, sızdırılan kimlik bilgilerini nerede bulabilirim?

Microsoft, sızdırılan kimlik bilgilerini çeşitli yerlerde bulur, örneğin:

- Pastebin.com ve paste.ca gibi genel yapıştırma siteleri, kötü aktörlerin genellikle böyle malzemeler göndermesinden sonra. Bu konum, çalınan kimlik bilgilerini bulmak için, her bir kötü aktör için, kendi aratlarını durdurur.
- Yasalar zorlama kurumları.
- Microsoft 'un karanlık Web araştırması yaptığı diğer gruplar.

#### <a name="why-arent-i-seeing-any-leaked-credentials"></a>Neden sızdırılan kimlik bilgilerini görmüyorum?

Sızdırılan kimlik bilgileri Microsoft 'un her zaman yeni, genel kullanıma açık bir toplu iş bulduğu Hassas doğası nedeniyle, sızdırılan kimlik bilgileri işlemden sonra kısa bir süre sonra silinir. Parola karması eşitlemesini (PHS) etkinleştirdikten sonra yalnızca yeni sızdırılan kimlik bilgileri, kiracınıza karşı işlenir. Daha önce bulunan kimlik bilgisi çiftlerine karşı doğrulama yapılmaz. 

#### <a name="i-havent-seen-any-leaked-credential-risk-events-for-quite-some-time"></a>Hiç sızdırılan kimlik bilgisi risk olaylarını çok bir süre görmemiş mıyım?

Herhangi bir sızdırılan kimlik bilgisi risk olayı görmediyseniz, bunun nedeni aşağıdaki nedenlerden kaynaklanır:

- Kiracınız için PHS özelliği etkin değil.
- Microsoft, kullanıcılarınız ile eşleşen herhangi bir sızdırılan kimlik bilgisi çifti bulamadı.

#### <a name="how-often-does-microsoft-process-new-credentials"></a>Microsoft yeni kimlik bilgilerini ne sıklıkla işler?

Kimlik bilgileri, normalde her gün birden çok işlem halinde bulunduklarında hemen işlenir.

## <a name="next-steps"></a>Sonraki adımlar

- [Riskleri azaltmak için kullanılabilir ilkeler](concept-identity-protection-policies.md)
- [Güvenliğe genel bakış](concept-identity-protection-security-overview.md)