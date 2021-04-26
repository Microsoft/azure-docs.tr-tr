---
title: B2B davetiyesi e-postası öğeleri-Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B işbirliği davetiyesi e-posta şablonu
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc80c208b5a48a45b8c47d640dc381f5559c3d4d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304775"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>B2B işbirliği davetiyesi e-postası öğeleri-Azure Active Directory

Davet e-postaları, Azure AD 'de B2B işbirliği kullanıcıları olarak iş ortaklarını panoda getirecek kritik bir bileşendir. [B2B işbirliğiyle birini davet etmek için bir e-posta göndermeniz gerekli olmasa](add-user-without-invite.md)da, bunu yapmanız, kullanıcıya, davetinizi kabul edip etmemeyi öğrenmek için gereken tüm bilgileri verir. Ayrıca, kaynaklara geri dönmeleri gerektiğinde gelecekte her zaman başvurabileceği bir bağlantı sağlar.

![B2B davet e-postasını gösteren ekran görüntüsü](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>E-postayı açıklayan

Özelliklerini kullanmanın en iyi şekilde haberdar olmak için e-postanın birkaç öğesine göz atalım.

### <a name="subject"></a>Konu

E-postanın konusu bu düzene uyar:

&lt;Kullanıcı adı &gt; , BT 'yi kuruluşlarındaki uygulamalara erişmeye davet etti.

### <a name="from-address"></a>Kimden adresi

Kimden adresi için LinkedIn benzeri bir model kullanıyoruz. Bu düzen, e-postanın kaynağından geldiğinden invites@microsoft.com , davetin başka bir kuruluştan geldiğinden emin olmak için bunu temizlemelidir. Biçim: Microsoft davetleri  <invites@microsoft.com> veya Microsoft davetleri, &lt; TenantName adına &gt;  <invites@microsoft.com> . 

> [!NOTE]
> Çin 'de 21Vianet tarafından işletilen Azure hizmeti için gönderici adresi olur Invites@oe.21vianet.com .

### <a name="reply-to"></a>Yanıtla

Yanıt e-postası, mevcut olduğunda davet edilen e-postasına ayarlanır, böylece e-postayı yanıtlamak davet eden e-posta gönderir.

### <a name="phishing-warning"></a>Kimlik avı uyarısı

E-posta, kullanıcıya kimlik avı hakkında kısa bir uyarıyla başlar ve yalnızca beklediğiniz davetleri kabul etmeleri gerektiğini uyarır. Davet ettiğiniz iş ortaklarının, sizin tarafınızdan bir süre önce kendisine bahseterek davetiniz tarafından şaşırmayacak olduğundan emin olmak iyi bir uygulamadır.

![E-postadaki kimlik avı uyarısı görüntüsü](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information-and-invitation-message"></a>Davet eden kişinin bilgileri ve davet iletisi

E-posta, daveti gönderen kuruluşla ilişkili ad ve birincil etki alanını içerir. Bu bilgiler, davetinin daveti kabul etme konusunda bilinçli bir karar vermesini sağlamaya yardımcı olmalıdır. Davet eden, [bir konuk kullanıcıyı Dizin, Grup veya uygulamaya davet](add-users-administrator.md) ettikleri ya da [davet API 'sini kullandıklarında](customize-invitation-api.md), davetinin bir parçası olarak bir ileti içeriyorsa, ileti e-postanın ana bölümünde vurgulanır. Ayrıca, davet eden kişinin adı ve profil görüntüsü bir tane ayarlandıklarında de bulunur. İletinin kendisi bir metin alanıdır, bu nedenle güvenlik nedenleriyle, HTML etiketlerini işlemez.

![E-postadaki davet iletisi görüntüsü](media/invitation-email-elements/invitation-message-inviters-info.png)

### <a name="accept-button-and-redirect-url"></a>Kabul et düğmesi ve yönlendirme URL 'SI

E-postanın sonraki bölümü, davetinin daveti kabul ettikten sonra nereye alınacağını öğrenmek için bir düğme içerir.  Gelecekte, davetli bu bağlantıyı, kaynaklarınıza doğrudan geri dönmek için kullanabilir.

![E-postadaki kabul düğmesi ve yeniden yönlendirme URL 'SI görüntüsü](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>Altbilgi bölümü

Alt bilgi, gönderilmekte olan davet hakkında daha fazla bilgi içerir. Davetli bir sonraki davetleri engellemek için her zaman bir seçenek vardır. Kuruluş [bir gizlilik bildirimi ayarlandıysa](../fundamentals/active-directory-properties-area.md), deyimin bağlantısı burada görüntülenir.  Aksi halde, kuruluşun bir gizlilik bildirimi ayarlayamadığını gösteren bir nottur.

![E-postadaki altbilgi bölümünün görüntüsü](media/invitation-email-elements/footer-section.png)

### <a name="blocking-an-organization-unsubscribing"></a>Bir kuruluşu engelleme (aboneliği kaldırma)

Bir kuruluştan gelen davetinde, alt bilgi **gelecekteki davetleri engellemeye** yönelik bir seçenek içerir. Konuk Kullanıcı, bundan sonraki davetleri kuruluştan engellemek için bu bağlantıyı seçebilir. Bu eylem, Ayrıca, kuruluştaki kullanıcının aboneliği kaldırma listesine de ekler [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage) .

### <a name="viewing-organizations-youve-blocked"></a>Engellediğiniz kuruluşları görüntüleme

Konuk Kullanıcı, engellediği kuruluşları görüntülemek veya dışarı aktarmak için aşağıdaki adımları izleyebilir:

1. Adresine gidin [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage) .
2. E-posta bir kerelik geçiş kodu kimlik doğrulaması için e-postanızı girin ve oturum açma adımlarını izleyin.
3. Engellediği kuruluşları görüntüleyin veya kopyalama ve yapıştırma kullanarak adları dışarı aktarın.
   > [!NOTE]
   > Sizi yeniden davet etmeyi engellediğiniz bir kuruluşa izin vermek istiyorsanız, organizasyonu seçip **İleri**' yi seçebilirsiniz.

## <a name="how-the-language-is-determined"></a>Dilin nasıl belirlendiği

Davet e-postasında Konuk kullanıcıya sunulan dil aşağıdaki ayarlara göre belirlenir. Bu ayarlar öncelik sırasına göre listelenir. Bir ayar yapılandırılmamışsa, listedeki bir sonraki ayar dili belirler.

- Davet oluşturma API 'SI kullanılıyorsa, [Davettedusermessageınfo](/graph/api/resources/invitedusermessageinfo) nesnesinin **MessageLanguage** özelliği
-   Konuğun [Kullanıcı nesnesinde](/graph/api/resources/user) belirtilen **PreferredLanguage** özelliği
-   Konuk kullanıcının ana kiracının özelliklerinde ayarlanan **bildirim dili** (yalnızca Azure AD kiracılar için)
-   Kaynak kiracının özelliklerinde ayarlanan **bildirim dili**

Bu ayarlardan hiçbiri yapılandırılmazsa, dil varsayılan olarak Ingilizce (US) olur.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD B2B işbirliği ile ilgili aşağıdaki makalelere bakın:

- [Azure AD B2B işbirliği nedir?](what-is-b2b.md)
- [Azure Active Directory yöneticileri B2B işbirliği kullanıcılarını nasıl ekler?](add-users-administrator.md)
- [Bilgi çalışanları B2B işbirliği kullanıcılarını nasıl ekler?](add-users-information-worker.md)
- [B2B işbirliği daveti satın alma](redemption-experience.md)
- [Bir davetiye olmadan B2B işbirliği kullanıcıları ekleyin](add-user-without-invite.md)
