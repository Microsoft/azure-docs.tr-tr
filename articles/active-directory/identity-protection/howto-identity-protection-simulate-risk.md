---
title: Azure AD Kimlik Koruması risk algılamaları benzetimi yapma
description: Kimlik koruması 'nda risk algılamalarını benzetmeyi öğrenin
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
ms.openlocfilehash: 7eafeaf59757fcda978fa89b4bc2f9882b769e48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835909"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>Kimlik korumasında risk algılamaları benzetimi yapma

Yöneticiler, aşağıdaki öğeleri gerçekleştirmek için ortamlarında risk benzetimi yapmak isteyebilir:

- Risk algılamalarını ve güvenlik açıklarını taklit ederek verileri kimlik koruması ortamında doldurun.
- Risk tabanlı koşullu erişim ilkeleri ayarlayın ve bu ilkelerin etkisini test edin.

Bu makalede, aşağıdaki risk algılama türlerinin benzetimini yapma adımları sunulmaktadır:

- Anonim IP adresi (kolay)
- Bilmediğiniz oturum açma özellikleri (orta)
- Atipik seyahat (zor)

Diğer risk algılamaları güvenli bir şekilde benzetilemez.

Her risk algılaması hakkında daha fazla bilgi makalesinde, [risk nedir?](concept-identity-protection-risks.md)bölümünde bulabilirsiniz.

## <a name="anonymous-ip-address"></a>Anonim IP adresi

Aşağıdaki yordamı tamamlamak için şunları kullanmanız gerekir:

- Anonim IP adreslerinin benzetimini yapmak için [Tor tarayıcısı](https://www.torproject.org/projects/torbrowser.html.en) . Kuruluşunuz Tor tarayıcısı kullanılarak sınırlandırdıysanız bir sanal makine kullanmanız gerekebilir.
- Azure AD Multi-Factor Authentication için henüz kaydedilmemiş bir test hesabı.

**Anonım IP 'den bir oturum açma benzetimi yapmak için aşağıdaki adımları uygulayın**:

1. [Tor tarayıcısını](https://www.torproject.org/projects/torbrowser.html.en)kullanarak öğesine gidin [https://myapps.microsoft.com](https://myapps.microsoft.com) .   
2. **Anonım IP adreslerinden oturum açma** işlemleri raporunda görünmesini istediğiniz hesabın kimlik bilgilerini girin.

Oturum açma, kimlik koruması panosunda 10-15 dakika içinde görüntülenir. 

## <a name="unfamiliar-sign-in-properties"></a>Bilinmeyen oturum açma özellikleri

Tanıdık konumların benzetimini yapmak için, test hesabınızın öncesinde oturum açmadığı bir konumdan ve cihazdan oturum açmanız gerekir.

Aşağıdaki yordam yeni oluşturulan bir kullanır:

- Yeni konumun benzetimini yapmak için VPN bağlantısı.
- Sanal makine, yeni bir cihazın benzetimini yapar.

Aşağıdaki yordamı tamamlamak için şunları içeren bir kullanıcı hesabı kullanmanız gerekir:

- En az 30 günlük bir oturum açma geçmişi.
- Azure AD Multi-Factor Authentication etkinleştirildi.

**Bilmediğiniz bir konumdan bir oturum açma benzetimi yapmak için aşağıdaki adımları uygulayın**:

1. Test hesabınızla oturum açarken, MFA sınamasını geçirmeyecek şekilde Multi-Factor Authentication (MFA) sınamasını devreder.
2. Yeni VPN 'nizi kullanarak şuraya gidin [https://myapps.microsoft.com](https://myapps.microsoft.com) ve test hesabınızın kimlik bilgilerini girin.

Oturum açma, kimlik koruması panosunda 10-15 dakika içinde görüntülenir.

## <a name="atypical-travel"></a>Olağandışı yolculuk

Algoritma makine öğrenimini taklit ettiğinden, algoritma tanıdık cihazlardan gelen tipik seyahat veya dizindeki diğer kullanıcılar tarafından kullanılan VPN 'lerden oturum açma gibi hatalı pozitif sonuçlar almak için Machine Learning 'i kullandığından, atipik seyahat koşulunun benzetimini yapmak zordur. Ayrıca, algoritma, risk algılamalarını oluşturmaya başlamadan önce 14 gün ve Kullanıcı için 10 oturum açma geçmişi gerektirir. Karmaşık makine öğrenimi modelleri ve yukarıdaki kurallar nedeniyle, aşağıdaki adımların bir risk algılamasına neden olmayacağı bir şansınız vardır. Bu algılamanın benzetimini yapmak için bu adımları birden çok Azure AD hesabı için çoğaltmak isteyebilirsiniz.

**Sıradan bir seyahat riskini algılamada benzetimini yapmak için aşağıdaki adımları uygulayın**:

1. Standart tarayıcınızı kullanarak öğesine gidin [https://myapps.microsoft.com](https://myapps.microsoft.com) .  
2. İçin atipik bir seyahat riski algılaması oluşturmak istediğiniz hesabın kimlik bilgilerini girin.
3. Kullanıcı aracınızı değiştirin. Microsoft Edge 'deki Kullanıcı aracısını Geliştirici Araçları (F12) olarak değiştirebilirsiniz.
4. IP adresinizi değiştirin. Bir VPN, Tor eklentisi veya farklı bir veri merkezinde Azure 'da yeni bir sanal makine oluşturarak IP adresinizi değiştirebilirsiniz.
5. [https://myapps.microsoft.com](https://myapps.microsoft.com)Önceki oturum açma işleminden daha önce ve birkaç dakika içinde aynı kimlik bilgilerini kullanarak oturum açın.

Oturum açma, kimlik koruması panosunda 2-4 saat içinde görüntülenir.

## <a name="testing-risk-policies"></a>Risk ilkelerini test etme

Bu bölüm, makalede oluşturulan kullanıcı ve oturum açma risk ilkelerinin test edilmesine yönelik adımlar, [nasıl yapılır: risk Ilkeleri yapılandırma ve etkinleştirme](howto-identity-protection-configure-risk-policies.md)adımları sağlar.

### <a name="user-risk-policy"></a>Kullanıcı riski ilkesi

Bir Kullanıcı risk güvenlik ilkesini test etmek için aşağıdaki adımları gerçekleştirin:

1. [Azure Portal](https://portal.azure.com)gidin.
1. **Azure Active Directory**  >  **Güvenliğe**  >  **Genel Bakış ' a** göz atın.
1. **Kullanıcı risk Ilkesini Yapılandır**' ı seçin.
   1. **Atamalar** altında
      1. **Kullanıcılar** - **tüm kullanıcılar** ' ı seçin veya dağıtımı sınırlandırdıysanız **bireyler ve gruplar ' ı seçin** .
         1. İsteğe bağlı olarak, kullanıcıların ilkeden hariç tutulmasını seçebilirsiniz.
      1. **Koşullar**  -  **Kullanıcı riski** Microsoft 'un önerisi, bu seçeneği **yüksek** olarak ayarlamanıza olanak sağlar.
   1. **Denetimler** altında
      1. **Erişim** -Microsoft 'un önerisi **erişime izin vermek** ve **parola değişikliğine gerek duyar**.
   1. **Ilkeyi zorla**  -  **Kapalı**
   1. **Kaydet** -bu eylem sizi **genel bakış** sayfasına verecektir.
1. Bir sınama hesabının kullanıcı riskini, örneğin, risk algılarından birinin birkaç kez benzetimini yaparak yükseltin.
1. Birkaç dakika bekleyin ve ardından riskin Kullanıcı için yükseltildiğini doğrulayın. Aksi takdirde, Kullanıcı için daha fazla risk algılamalarını taklit edin.
1. Risk ilkenize dönün **ve ilke değişikliklerinizi** **Açık** olarak ayarlayın ve ilke değişiklerinizi **kaydedin** .
1. Artık yükseltilmiş risk düzeyine sahip bir kullanıcı kullanarak oturum açarak Kullanıcı risk tabanlı koşullu erişimi test edebilirsiniz.

### <a name="sign-in-risk-security-policy"></a>Oturum açma riski güvenlik ilkesi

Bir oturum açma risk ilkesini test etmek için aşağıdaki adımları gerçekleştirin:

1. [Azure Portal](https://portal.azure.com)gidin.
1. **Azure Active Directory**  >  **Güvenliğe**  >  **Genel Bakış ' a** göz atın.
1. **Oturum açma risk Ilkesini Yapılandır**' ı seçin.
   1. **Atamalar** altında
      1. **Kullanıcılar** - **tüm kullanıcılar** ' ı seçin veya dağıtımı sınırlandırdıysanız **bireyler ve gruplar ' ı seçin** .
         1. İsteğe bağlı olarak, kullanıcıların ilkeden hariç tutulmasını seçebilirsiniz.
      1. **Koşullar**  -  **Oturum açma riski** Microsoft 'un önerisi, bu seçeneği **Orta ve üst düzeyde** ayarlamaya yönelik bir seçenektir.
   1. **Denetimler** altında
      1. **Erişim** -Microsoft 'un önerisi, **erişime izin vermek** ve **Multi-Factor Authentication gerektirir**.
   1. **Ilkeyi zorla**  -  **Üzerinde**
   1. **Kaydet** -bu eylem sizi **genel bakış** sayfasına verecektir.
1. Artık riskli bir oturum kullanarak oturum açma riski tabanlı koşullu erişimi test edebilirsiniz (örneğin, Tor tarayıcısını kullanarak). 

## <a name="next-steps"></a>Sonraki adımlar

- [Risk nedir?](concept-identity-protection-risks.md)

- [Nasıl yapılır: risk ilkelerini yapılandırma ve etkinleştirme](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory Kimlik Koruması](overview-identity-protection.md)
