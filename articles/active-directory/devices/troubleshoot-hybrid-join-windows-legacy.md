---
title: Eski karma Azure Active Directory katılmış cihazlarda sorun giderme
description: Karma Azure Active Directory katılmış alt düzey cihazlarda sorun giderme.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbf936370b401a76479c7679499103d083e0752a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551756"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Karma Azure Active Directory katılmış alt düzey cihazlarda sorun giderme 

Bu makale yalnızca aşağıdaki cihazlarda geçerlidir: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 

Windows 10 veya Windows Server 2016 için bkz. [karma Azure Active Directory katılmış Windows 10 ve Windows server 2016 cihazlarda sorun giderme](troubleshoot-hybrid-join-windows-current.md).

Bu makalede, [karma Azure Active Directory katılmış cihazları](hybrid-azuread-join-plan.md) aşağıdaki senaryoları destekleyecek şekilde yapılandırdığınız varsayılır:

- Cihaz tabanlı Koşullu Erişim

Bu makale, olası sorunların nasıl çözüleceği hakkında sorun giderme kılavuzu sağlar.  

**Bilmeniz gerekenler:** 

- Alt düzey Windows cihazları için karma Azure AD katılımı, Windows 10 ' da olduğundan biraz farklı çalışır. Birçok müşteri, AD FS (federe etki alanları için) veya sorunsuz SSO (yönetilen etki alanları için) ihtiyacı olduğunu fark etmez.
- Sorunsuz SSO, Firefox ve Microsoft Edge tarayıcılarında özel göz atma modunda çalışmaz. Tarayıcı Gelişmiş korumalı modda çalışıyorsa veya artırılmış güvenlik yapılandırması etkinse, Internet Explorer üzerinde de çalışmaz.
- Federasyon etki alanları olan müşteriler için, hizmet bağlantı noktası (SCP), yönetilen etki alanı adını (örneğin, contoso.onmicrosoft.com) işaret edecek şekilde yapılandırıldıysa, alt düzey Windows cihazlarına yönelik karma Azure AD katılımı çalışmaz.
- Birden çok etki alanı kullanıcısı, alt düzey karma Azure AD 'ye katılmış cihazlarda oturum açtığında aynı fiziksel cihaz Azure AD 'de birden çok kez görünür.  Örneğin, *jtikan* ve *jharnett* bir cihazda oturum açtığında, her biri için **Kullanıcı** bilgileri sekmesinde ayrı bir kayıt (DeviceID) oluşturulur. 
- Ayrıca, işletim sisteminin yeniden yüklenmesi veya el ile yeniden kayıt olması nedeniyle Kullanıcı bilgileri sekmesinde bir cihaz için birden çok giriş alabilirsiniz.
- Cihazların ilk kaydı/katılması, oturum açma ya da kilit/kilit açma girişimi için bir girişim gerçekleştirecek şekilde yapılandırılmıştır. Bir görev zamanlayıcı görevi tarafından tetiklenen 5 dakikalık bir gecikme olabilir. 
- Windows 7 SP1 veya Windows Server 2008 R2 SP1 söz konusu olduğunda [KB4284842](https://support.microsoft.com/help/4284842) yüklendiğinden emin olun. Bu güncelleştirme, parola değiştirildikten sonra müşterinin korunan anahtarlara erişim kaybı nedeniyle gelecekteki kimlik doğrulama başarısızlıklarını önler.
- Karma Azure AD katılımı, bir kullanıcının UPN 'si değiştirildikten sonra sorunsuz SSO kimlik doğrulama işlemini bozduktan sonra başarısız olabilir. JOIN işlemi sırasında, tarayıcı oturumu tanımlama bilgileri temizlenmediği veya Kullanıcı tarafından açık bir şekilde açılana veya eski UPN 'yi kaldırmadığı sürece, hala eski UPN 'yi Azure AD 'ye gönderiyor olabileceğini görebilirsiniz.

## <a name="step-1-retrieve-the-registration-status"></a>1. Adım: kayıt durumunu alma 

**Kayıt durumunu doğrulamak için:**  

1. Karma Azure AD katılımı gerçekleştiren kullanıcı hesabıyla oturum açın.
1. Komut istemi 'ni açın 
1. `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i` yazın

Bu komut, size JOIN durumu hakkında ayrıntılar sağlayan bir iletişim kutusu görüntüler.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/01.png" alt-text="Windows için Workplace Join iletişim kutusunun ekran görüntüsü. E-posta adresi içeren metin, belirli bir cihazın çalışma alanına katıldığını belirtir." border="false":::

## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>2. Adım: karma Azure AD JOIN durumunu değerlendirme 

Cihaz karma Azure AD 'ye katılmış değilse, "katıl" düğmesine tıklayarak karma Azure AD katılımı yapmayı deneyebilirsiniz. Karma Azure AD JOIN işlemi başarısız olursa, hata hakkındaki ayrıntılar gösterilir.

**En yaygın sorunlar şunlardır:**

- Yanlış yapılandırılmış AD FS veya Azure AD veya ağ sorunları

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/02.png" alt-text="Windows için Workplace Join iletişim kutusunun ekran görüntüsü. Metin, hesap kimlik doğrulaması sırasında bir hata oluştuğunu bildiriyor." border="false":::
    
   - Autoworkplace.exe, Azure AD veya AD FS ile sessizce kimlik doğrulaması yapamıyor. Bunun nedeni, eksik veya yanlış yapılandırılmış AD FS (federe etki alanları için) veya eksik ya da yanlış yapılandırılmış Azure AD sorunsuz tek Sign-On (yönetilen etki alanları için) veya ağ sorunları olabilir. 
   - Bu, Multi-Factor Authentication (MFA) özelliğinin etkinleştirilmesi/yapılandırılması ve WIAORMULTIAUTHN 'nin AD FS sunucuda yapılandırılmamış olması olabilir. 
   - Diğer bir deyişle, giriş bölgesi bulma (HRD) sayfasının kullanıcı etkileşimini beklediği, bu da **autoworkplace.exe** sessizce bir belirteç istemekte engel olur.
   - AD FS ve Azure AD URL 'Lerinin istemci üzerindeki intranet bölgesinde eksik olması olabilir.
   - Ağ bağlantısı sorunları, **autoworkplace.exe** AD FS veya Azure AD URL 'lerine ulaşmasını engelleyebilir. 
   - **Autoworkplace.exe** istemcinin, istemciden kuruluşun ŞIRKET içi ad etki alanı denetleyicisine doğrudan bir görüş satırı olmasını gerektirir. Bu, karma Azure AD 'ye yalnızca istemci kuruluşun intranetine bağlıyken başarılı olduğu anlamına gelir.
   - Kuruluşunuz Azure AD sorunsuz çoklu oturum açma 'yı kullanır `https://autologon.microsoftazuread-sso.com` veya `https://aadg.windows.net.nsatc.net` cihazın IE intranet ayarlarında mevcut değildir ve intranet bölgesi için **komut dosyası aracılığıyla durum çubuğundaki güncelleştirmelerin** etkin olmadığından izin vermez.
- Bir etki alanı kullanıcısı olarak oturum açmadınız

   :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/03.png" alt-text="Windows için Workplace Join iletişim kutusunun ekran görüntüsü. Metin, hesap doğrulaması sırasında bir hata oluştuğunu bildiriyor." border="false":::

   Bunun gerçekleşebileceği birkaç farklı neden vardır:

   - Oturum açan kullanıcı bir etki alanı kullanıcısı değil (örneğin, yerel bir Kullanıcı). Alt düzey cihazlarda karma Azure AD katılımı yalnızca etki alanı kullanıcıları için desteklenir.
   - İstemci bir etki alanı denetleyicisine bağlanamıyor.    
- Kotaya ulaşıldı

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/04.png" alt-text="Windows için Workplace Join iletişim kutusunun ekran görüntüsü. Kullanıcı en fazla birleştirilmiş cihaz sayısına ulaştığından, metin bir hata bildiriyor." border="false":::

- Hizmet yanıt vermiyor 

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/05.png" alt-text="Windows için Workplace Join iletişim kutusunun ekran görüntüsü. Metin, sunucu yanıt vermediği için bir hata oluştuğunu bildiriyor." border="false":::

Durum bilgilerini olay günlüğü 'nde de bulabilirsiniz: **uygulamalar ve hizmetler Log\Microsoft-Workplace JOIN**
  
**Başarısız bir karma Azure AD birleştirmesi için en yaygın nedenler şunlardır:** 

- Bilgisayarınız kuruluşunuzun iç ağına veya şirket içi AD etki alanı denetleyicinize bağlantısı olan bir VPN 'ye bağlı değil.
- Bilgisayarınızda yerel bir bilgisayar hesabıyla oturum açtınız. 
- Hizmet yapılandırma sorunları: 
   - AD FS sunucusu **Wiaormultiauthn**'yi destekleyecek şekilde yapılandırılmamış. 
   - Bilgisayarınızın ormanında, Azure AD 'de doğrulanmış etki alanı adınızı işaret eden bir hizmet bağlantı noktası nesnesi yok 
   - Ya da etki alanınız yönetiliyorsa, sorunsuz SSO Yapılandırılmadı veya çalışmıyor.
   - Bir Kullanıcı cihaz sınırına ulaştı. 

## <a name="next-steps"></a>Sonraki adımlar

Sorular için bkz. [cihaz YÖNETIMI SSS](faq.yml)  
