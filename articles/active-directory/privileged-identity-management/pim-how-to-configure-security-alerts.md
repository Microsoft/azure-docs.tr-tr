---
title: PıM-Azure AD 'de Azure AD rolleri için güvenlik uyarıları | Microsoft Docs
description: Azure Active Directory Privileged Identity Management Azure AD rolleri için güvenlik uyarılarını yapılandırın.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e05502b40f7f917526b2514d83dcc58636ac06f3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023051"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management Azure AD rolleri için güvenlik uyarılarını yapılandırma

Privileged Identity Management (PıM), Azure Active Directory (Azure AD) kuruluşunuzda şüpheli veya güvenli olmayan bir etkinlik olduğunda uyarılar oluşturur. Bir uyarı tetiklendiğinde Privileged Identity Management panosunda görüntülenir. Uyarıyı tetikleyen kullanıcıları veya rolleri listeleyen bir rapor görmek için uyarıyı seçin.

![Azure AD rolleri-uyarıları ve önem derecesini listeleme uyarı bölmesi](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Güvenlik uyarıları

Bu bölümde, Azure AD rolleri için tüm güvenlik uyarıları ve nasıl düzeltileceğini ve nasıl önleneceği anlatılmaktadır. Önem derecesi aşağıdaki anlama sahiptir:

- **Yüksek**: bir ilke ihlali nedeniyle hemen eyleme gerek duyar.
- **Orta**: anında eyleme gerek yoktur, ancak olası bir ilke ihlaline işaret eder.
- **Düşük**: hemen eylem gerektirmez, ancak tercih edilen bir ilke değişikliği önerilir.

### <a name="administrators-arent-using-their-privileged-roles"></a>Yöneticiler ayrıcalıklı rollerini kullanmıyor

| | |
| --- | --- |
| **İnin** | Düşük |
| **Bu uyarıyı neden alıyorum?** | Ayrıcalıklı roller atanmış kullanıcılar, saldırının olasılığını artırır. Saldırganlar, etkin olarak kullanılmayan hesaplara ait olmaması durumunda daha da kolaydır. |
| **Nasıl düzeltilir?** | Listedeki kullanıcıları gözden geçirin ve gerekmeyen ayrıcalıklı rollerden kaldırın. |
| **Önlem** | Ayrıcalıklı rolleri yalnızca iş gerekçesinin bulunduğu kullanıcılara atayın. </br>Kullanıcıların erişimine hala ihtiyacı olduğunu doğrulamak için düzenli [erişim İncelemeleri](pim-how-to-start-security-review.md) zamanlayın. |
| **Portalın risk azaltma eylemi** | Hesabı ayrıcalıklı rolünden kaldırır. |
| **Tetikleyici** | Bir Kullanıcı bir rolü etkinleştirmeden belirtilen gün sayısından sonra harekete geçtiğinde tetiklenir. |
| **Gün sayısı** | Bu ayar, bir kullanıcının bir rolü etkinleştirmeden gidebileceği, 0 ile 100 arasında olan en fazla gün sayısını belirtir.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Roller etkinleştirme için çok faktörlü kimlik doğrulaması gerektirmez

| | |
| --- | --- |
| **İnin** | Düşük |
| **Bu uyarıyı neden alıyorum?** | Çok faktörlü kimlik doğrulaması olmadan, güvenliği aşılmış kullanıcılar ayrıcalıklı rolleri etkinleştirebilir. |
| **Nasıl düzeltilir?** | Rol listesini gözden geçirin ve her rol için [çok faktörlü kimlik doğrulaması gerektir](pim-how-to-change-default-settings.md) . |
| **Önlem** | Her rol için [MFA gerektir](pim-how-to-change-default-settings.md) .  |
| **Portalın risk azaltma eylemi** | Ayrıcalıklı rolün etkinleştirilmesi için çok faktörlü kimlik doğrulamasını zorunlu kılar. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>Kuruluşun Azure AD Premium P2 yok

| | |
| --- | --- |
| **İnin** | Düşük |
| **Bu uyarıyı neden alıyorum?** | Geçerli Azure AD kuruluşunda Azure AD Premium P2 yok. |
| **Nasıl düzeltilir?** | [Azure AD sürümleri](../fundamentals/active-directory-whatis.md)hakkındaki bilgileri gözden geçirin. Azure AD Premium P2 ' ye yükseltin. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Ayrıcalıklı bir roldeki olası eski hesaplar

| | |
| --- | --- |
| **İnin** | Orta |
| **Bu uyarıyı neden alıyorum?** | Ayrıcalıklı bir roldeki hesaplar, son 90 gün içinde parolasını değiştirmedi. Bu hesaplar, tutulmayan ve saldırganların savunmasız olduğu hizmet veya paylaşılan hesaplar olabilir. |
| **Nasıl düzeltilir?** | Listedeki hesapları gözden geçirin. Artık erişim gerekmiyorsa, bunları ayrıcalıklı rollerden kaldırın. |
| **Önlem** | Parolayı bilen kullanıcılarda değişiklik olduğunda, paylaşılan hesapların güçlü parolalar döndürürken emin olun. </br>[Erişim gözden geçirmeleri](pim-how-to-start-security-review.md) kullanarak ayrıcalıklı rollere sahip hesapları düzenli olarak gözden geçirin ve artık gerekmeyen rol atamalarını kaldırın. |
| **Portalın risk azaltma eylemi** | Hesabı ayrıcalıklı rolünden kaldırır. |
| **En iyi uygulamalar** | Bir parola kullanarak kimlik doğrulayan ve genel yönetici veya güvenlik yöneticisi gibi son derece ayrıcalıklı yönetim rollerine atanan paylaşılan, hizmet ve acil durum erişim hesapları, parolalarının aşağıdaki durumlar için döndürülmelidir:<ul><li>Bir güvenlik olayından sonra yönetim erişimi haklarının kötüye kullanımını veya güvenliğinin aşılmasına neden olacak şekilde</li><li>Herhangi bir kullanıcının ayrıcalıkları, artık yönetici kalmayacak şekilde değiştirildikten sonra (örneğin, yönetici olan bir çalışan, veya kuruluştan ayrıldıktan sonra)</li><li>Düzenli aralıklarla (örneğin, üç aylık veya yıllık), bilinen bir ihlal olmaması veya BT ekip üzerinde değişiklik yapılmasa bile</li></ul>Birden çok kişinin bu hesapların kimlik bilgilerine erişimi olduğundan, rollerinin bırakılmış kişilerin hesaplara artık erişememesini sağlamak için kimlik bilgileri döndürülmelidir. [Daha fazla bilgi](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>Roller Privileged Identity Management dışında atanıyor

| | |
| --- | --- |
| **İnin** | Yüksek |
| **Bu uyarıyı neden alıyorum?** | Privileged Identity Management dışında yapılan ayrıcalıklı rol atamaları düzgün izlenmiyor ve etkin bir saldırı gösterebilir. |
| **Nasıl düzeltilir?** | Listedeki kullanıcıları gözden geçirin ve Privileged Identity Management dışında atanan ayrıcalıklı rollerden kaldırın. |
| **Önlem** | Kullanıcılara Privileged Identity Management dışında ayrıcalıklı roller atandığını araştırın ve bundan sonra gelen atamaları yasakla. |
| **Portalın risk azaltma eylemi** | Kullanıcıyı ayrıcalıklı rolünden kaldırır. |

### <a name="there-are-too-many-global-administrators"></a>Çok fazla genel yönetici var

| | |
| --- | --- |
| **İnin** | Düşük |
| **Bu uyarıyı neden alıyorum?** | Genel yönetici, en yüksek ayrıcalıklı roldür. Küresel bir yöneticinin güvenliği tehlikeye girerse, saldırgan tüm ve tüm sisteminizi riske sokan tüm izinlerine erişim kazanır. |
| **Nasıl düzeltilir?** | Listedeki kullanıcıları gözden geçirin ve kesinlikle genel yönetici rolüne gerek olmayan her türlü kaldırın. </br>Bunun yerine bu kullanıcılara daha düşük ayrıcalıklı roller atayın. |
| **Önlem** | Kullanıcılara gereken en az ayrıcalıklı rolü atayın. |
| **Portalın risk azaltma eylemi** | Hesabı ayrıcalıklı rolünden kaldırır. |
| **Tetikleyici** | İki farklı ölçüt karşılanırsa tetiklenir ve her ikisini de yapılandırabilirsiniz. İlk olarak, belirli bir genel yönetici eşiğine erişmeniz gerekir. İkinci olarak, toplam rol atamalarınızın belirli bir yüzdesi genel yönetici olmalıdır. Yalnızca bu ölçülerden birini karşılıyorsa, uyarı görünmez. |
| **En az genel yönetici sayısı** | Bu ayar, Azure AD kuruluşunuz için çok az bir değer olduğunu düşündüğünüz genel yönetici sayısını 2 ' den 100 ' e kadar belirtir. |
| **Genel yöneticilerin yüzdesi** | Bu ayar, Azure AD kuruluşunuzun DIP 'e istemediğiniz genel 100 yönetici olan en düşük yönetici yüzdesini belirtir. |

### <a name="roles-are-being-activated-too-frequently"></a>Roller çok sık etkinleştiriliyor

| | |
| --- | --- |
| **İnin** | Düşük |
| **Bu uyarıyı neden alıyorum?** | Aynı kullanıcı tarafından aynı ayrıcalıklı rol için birden çok etkinleştirme saldırının bir imzadır. |
| **Nasıl düzeltilir?** | Listedeki kullanıcıları gözden geçirin ve ayrıcalıklı rollerinin [Etkinleştirme süresinin](pim-how-to-change-default-settings.md) görevlerini gerçekleştirmesi için yeterince uzun ayarlandığından emin olun. |
| **Önlem** | Ayrıcalıklı roller için [Etkinleştirme süresinin](pim-how-to-change-default-settings.md) , kullanıcıların görevlerini gerçekleştirmesi için yeterince uzun ayarlandığından emin olun.</br>Birden çok yönetici tarafından paylaşılan hesaplara sahip ayrıcalıklı roller için [çok faktörlü kimlik doğrulaması gerektir](pim-how-to-change-default-settings.md) . |
| **Portalın risk azaltma eylemi** | Yok |
| **Tetikleyici** | Bir Kullanıcı belirli bir süre içinde aynı ayrıcalıklı rolü birden çok kez etkinleştirdiğinde tetiklenir. Hem zaman dönemini hem de etkinleştirme sayısını yapılandırabilirsiniz. |
| **Etkinleştirme yenileme zaman çerçevesi** | Bu ayar, şüpheli yenilemeler izlemek için kullanmak istediğiniz zaman dilimini gün, saat, dakika ve saniye cinsinden belirtir. |
| **Etkinleştirme yenilemesinin sayısı** | Bu ayar, seçtiğiniz zaman dilimi içinde, bilgilendirilmek istediğiniz 2 ile 100 arasında etkinleştirme sayısını belirtir. Kaydırıcıyı taşıyarak veya metin kutusuna bir sayı yazarak bu ayarı değiştirebilirsiniz. |

## <a name="configure-security-alert-settings"></a>Güvenlik Uyarısı ayarlarını yapılandırma

Privileged Identity Management ' deki bazı güvenlik uyarılarını, kuruluşunuzun ihtiyaçlarına ve güvenlik hedefleriyle çalışacak şekilde özelleştirebilirsiniz. Güvenlik Uyarısı ayarlarını açmak için aşağıdaki adımları izleyin:

1. Azure AD 'de **Privileged Identity Management** açın.

1. **Azure AD rolleri**' ni seçin.

1. **Ayarlar** ' ı ve ardından **Uyarılar**' ı seçin.

    ![Azure AD rolleri-uyarılar seçiliyken ayarlar](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Bu uyarının ayarını yapılandırmak için bir uyarı adı seçin.

    ![Seçili uyarı için güvenlik uyarısı ayarları bölmesi](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure AD rol ayarlarını yapılandırma](pim-how-to-change-default-settings.md)
