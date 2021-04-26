---
title: Risk ilkeleri-Azure Active Directory Kimlik Koruması
description: Azure Active Directory Kimlik Koruması risk ilkelerini etkinleştirme ve yapılandırma
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 366d68be1a7f115980973015e363da6095876754
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997639"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>Nasıl yapılır: risk ilkelerini yapılandırma ve etkinleştirme

Önceki makalede öğrendiğimiz gibi, [kimlik koruma ilkeleri](concept-identity-protection-policies.md) dizinimizde etkinleştirediğimiz iki risk ilkemiz var. 

- Oturum açma riski ilkesi
- Kullanıcı riski ilkesi

![Kullanıcı ve oturum açma risk ilkelerini etkinleştirmek için güvenlik genel bakış sayfası](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Her iki ilke de ortamınızda risk algılamalarını otomatik hale getirmek ve risk algılandığında kullanıcıların kendiliğinden düzelmesini sağlamak için çalışır. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>Önkoşullar 

Kuruluşunuz, risk algılandığında kullanıcıların kendi kendini düzeltmesine izin vermek isterse, kullanıcıların hem self servis parola sıfırlama hem de Azure AD Multi-Factor Authentication için kayıtlı olmaları gerekir. En iyi deneyim için [Birleşik güvenlik bilgileri kayıt deneyiminin etkinleştirilmesini](../authentication/howto-registration-mfa-sspr-combined.md) öneririz. Kullanıcıların kendi kendini düzeltmesine izin vermek, yönetici müdahalesine gerek kalmadan onları daha hızlı bir şekilde daha hızlı bir şekilde geri alır. Yöneticiler bu olayları görmeye devam edebilir ve bunu bulduktan sonra araştırabilir. 

## <a name="choosing-acceptable-risk-levels"></a>Kabul edilebilir risk düzeylerini seçme

Kuruluşlar, Kullanıcı deneyimini ve güvenlik duruşunu kabul etmek isteyen risk düzeyine karar vermelidir. 

Microsoft 'un önerisi, Kullanıcı risk ilkesi eşiğini **yüksek** olarak ve oturum açma riski ilkesini **Orta ve üst düzeyde** ayarlamak.

**Yüksek** bir eşik seçilmesi, bir ilkenin tetiklenme sayısını azaltır ve kullanıcılara etkiyi en aza indirir. Ancak, ilkeden **düşük** ve **Orta ölçekli** risk algılamalarını dışarıda bırakır, bu da bir saldırganın güvenliği aşılmış bir kimlik kötüye yararlanmasıyla engelleyemeyebilir. **Düşük** eşiğin seçilmesi ek Kullanıcı kesintileri tanıtır, ancak güvenlik sonrası artar.

## <a name="exclusions"></a>Dışlamalar

Tüm ilkeler, [acil erişim veya kesme camı yönetici hesaplarınız](../roles/security-emergency-access.md)gibi kullanıcıların dışlanmasını sağlar. Kuruluşlar, hesapların kullanıldığı yönteme göre diğer hesapları belirli ilkelerden hariç tutmaları gerektiğini tespit edebilir. Hala geçerli olup olmadığını görmek için tüm dışlamaları düzenli olarak incelenmelidir.

Yapılandırılan güvenilir [ağ konumları](../conditional-access/location-condition.md) , bazı risk algılamalarındaki kimlik koruması tarafından hatalı pozitif sonuçları azaltmak için kullanılır.

## <a name="enable-policies"></a>İlkeleri etkinleştir

Kullanıcı riskini ve oturum açma risk ilkelerini etkinleştirmek için aşağıdaki adımları izleyin.

1. [Azure Portal](https://portal.azure.com)gidin.
1. **Azure Active Directory**  >  **güvenlik**  >  **kimlik korumasına**  >  **genel bakış konusuna** göz atın.
1. **Kullanıcı risk ilkesini** seçin.
   1. **Atamalar** altında
      1. **Kullanıcılar** - **tüm kullanıcılar** ' ı seçin veya dağıtımı sınırlandırdıysanız **bireyler ve gruplar ' ı seçin** .
         1. İsteğe bağlı olarak, kullanıcıların ilkeden hariç tutulmasını seçebilirsiniz.
      1. **Koşullar**  -  **Kullanıcı riski** Microsoft 'un önerisi, bu seçeneği **yüksek** olarak ayarlamanıza olanak sağlar.
   1. **Denetimler** altında
      1. **Erişim** -Microsoft 'un önerisi **erişime izin vermek** ve **parola değişikliğine gerek duyar**.
   1. **Ilkeyi zorla**  -  **Üzerinde**
   1. **Kaydet** -bu eylem sizi **genel bakış** sayfasına verecektir.
1. **Oturum açma risk İlkesi '** ni seçin.
   1. **Atamalar** altında
      1. **Kullanıcılar** - **tüm kullanıcılar** ' ı seçin veya dağıtımı sınırlandırdıysanız **bireyler ve gruplar ' ı seçin** .
         1. İsteğe bağlı olarak, kullanıcıların ilkeden hariç tutulmasını seçebilirsiniz.
      1. **Koşullar**  -  **Oturum açma riski** Microsoft 'un önerisi, bu seçeneği **Orta ve üst düzeyde** ayarlamaya yönelik bir seçenektir.
   1. **Denetimler** altında
      1. **Erişim** -Microsoft 'un önerisi, **erişime izin vermek** ve **Multi-Factor Authentication gerektirir**.
   1. **Ilkeyi zorla**  -  **Üzerinde**
   1. **Kaydet**

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD Multi-Factor Authentication kayıt ilkesini etkinleştirme](howto-identity-protection-configure-mfa-policy.md)

- [Risk nedir?](concept-identity-protection-risks.md)

- [Risk algılamalarını araştırma](howto-identity-protection-investigate-risk.md)

- [Risk algılamalarının simülasyonunu oluşturma](howto-identity-protection-simulate-risk.md)
