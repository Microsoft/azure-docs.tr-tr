---
title: 'Azure AD Connect: Karma Azure AD birleşimi yapılandırma görevleri | Microsoft Docs'
description: Bu belge, hibrit Azure AD JOIN 'i tamamlaması için gereken yapılandırma sonrası görevleri ayrıntılarını
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70e3267792f27a170efa26cc4267d1b25045a099
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231246"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Hibrit Azure AD’ye katılmada yapılandırma sonrası görevler

Kuruluşunuzu karma Azure AD JOIN için yapılandırmak üzere Azure AD Connect çalıştırdıktan sonra, kurulumu sonlandırmak için gerçekleştirmeniz gereken birkaç ek adım vardır.  Yalnızca cihazlarınız için uygulanan adımları uygulayın.

## <a name="1-configure-controlled-rollout-optional"></a>1. Denetlenen dağıtımı yapılandırma (Isteğe bağlı)
Windows 10 ve Windows Server 2016 çalıştıran etki alanına katılmış tüm cihazlar, tüm yapılandırma adımları tamamlandıktan sonra otomatik olarak Azure AD 'ye kaydedilir. Bu otomatik kayıt yerine denetimli bir dağıtımı tercih ediyorsanız, otomatik dağıtımı seçerek etkinleştirmek veya devre dışı bırakmak için Grup İlkesi kullanabilirsiniz.  Bu Grup ilkesi, diğer yapılandırma adımları başlatılmadan önce ayarlanmalıdır:
* Active Directory bir Grup İlkesi nesnesi oluşturun.
* Adlandırın (ex-hibrit Azure AD JOIN).
* Düzenle ve şuraya git:  Bilgisayar Yapılandırma > Ilkeleri Yönetim Şablonları > Windows bileşenleri > cihaz kaydı >.

>[!NOTE]
>2012R2 için ilke ayarları, **bilgisayar yapılandırması > ilkeler > Yönetim Şablonları > Windows bileşenleri > Workplace Join > çalışma alanına katılma istemci bilgisayarları**

* Bu ayarı etkinleştir:  Etki alanına katılmış bilgisayarları cihaz olarak kaydedin.
* Uygula ve Tamam ' a tıklayın.
* GPO 'YU tercih ettiğiniz konuma (kuruluş birimi, güvenlik grubu veya tüm cihazların etki alanı) bağlayın.

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Cihaz kayıt uç noktaları ile ağı yapılandırma
Azure AD 'ye kaydolmak için aşağıdaki URL 'Lerin kurumsal ağınızdaki bilgisayarlardan erişilebilir olduğundan emin olun:

* https://enterpriseregistration.windows.net
* https://login.microsoftonline.com
* https://device.login.microsoftonline.com 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. Windows 10 cihazları için WPAD uygulama
Kuruluşunuz bir giden proxy üzerinden Internet 'e eriştiğinde, Windows 10 bilgisayarlarının Azure AD 'ye kaydolmasının etkinleştirilmesi için Web proxy otomatik bulma (WPAD) uygulayın.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. SCP 'YI, Azure AD Connect tarafından yapılandırılmamış ormanlarda yapılandırma 

Hizmet bağlantı noktası (SCP), cihazlarınız tarafından otomatik kayıt için kullanılacak Azure AD kiracı bilgilerinizi içerir.  Azure AD Connect adresinden indirdiğiniz ConfigureSCP. ps1 PowerShell betiğini çalıştırın.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Azure AD Connect tarafından yapılandırılmayan herhangi bir Federasyon hizmetini yapılandırın

Kuruluşunuz Azure AD 'de oturum açmak için bir Federasyon Hizmeti kullanıyorsa, Azure AD bağlı olan taraf güveninizdeki talep kuralları cihaz kimlik doğrulamasına izin vermelidir. AD FS ile Federasyon kullanıyorsanız, talep kuralları oluşturmak için [AD FS yardım](https://aka.ms/aadrptclaimrules) ' a gidin. Microsoft dışı bir Federasyon çözümü kullanıyorsanız, rehberlik için bu sağlayıcıya başvurun.  

>[!NOTE]
>Windows alt düzey cihazlarınız varsa, Azure AD güvenine istek alırken hizmetin AuthenticationMethod ve wiaormultiauthn taleplerini vermeyi desteklemesi gerekir. AD FS, kimlik doğrulama yöntemi aracılığıyla geçiş yapan bir verme dönüştürme kuralı eklemeniz gerekir.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Windows alt düzey cihazlar için Azure AD sorunsuz SSO 'yu etkinleştirme

Kuruluşunuz Azure AD 'de oturum açmak için parola karma eşitlemesi veya geçişli kimlik doğrulaması kullanıyorsa, Windows alt düzey cihazların kimliğini doğrulamak için Azure AD sorunsuz SSO 'yu bu oturum açma yöntemiyle etkinleştirin: https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso. 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Windows alt düzey cihazlar için Azure AD ilkesi ayarlama

Windows alt düzey cihazlarını kaydetmek için, Azure AD ilkesinin kullanıcıların cihazları kaydetmesi için izin verdiğinden emin olmanız gerekir. 

* Azure portal hesabınızda oturum açın.
* Şuraya gidin:  Cihaz ayarlarını > Azure Active Directory > cihazları
* "Kullanıcılar cihazlarını Azure AD 'ye kaydedebileceği" olarak ayarlayın.
* Kaydet’e tıklayın.

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Windows alt düzey cihazlara Azure AD uç noktası ekleme

Cihazların kimliğini doğrularken sertifika istemlerinin önüne geçmek için, Windows alt düzey cihazlarınızdaki yerel Intranet bölgelerine Azure AD cihaz kimlik doğrulaması uç noktasını ekleyin: https://device.login.microsoftonline.com 

[Sorunsuz SSO](how-to-connect-sso.md)kullanıyorsanız, bu bölgedeki "komut dosyası aracılığıyla durum çubuğu güncelleştirmelerine izin ver" i de etkinleştirin ve aşağıdaki uç noktayı ekleyin: https://autologon.microsoftazuread-sso.com 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Windows alt düzey cihazlara Microsoft Workplace Join 'yi yükler

Bu yükleyici, kullanıcı bağlamında çalışan cihaz sisteminde zamanlanmış bir görev oluşturur. Görev, Kullanıcı Windows 'da oturum açtığında tetiklenir. Görev, tümleşik Windows kimlik doğrulaması kullanarak kimlik doğrulamasından geçtikten sonra, Kullanıcı kimlik bilgileriyle cihazı Azure AD ile sessizce birleştirir. Yükleme Merkezi https://www.microsoft.com/download/details.aspx?id=53554. 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Grup ilkesini cihaz kaydına izin verecek şekilde yapılandırma

* Active Directory bir Grup İlkesi nesnesi oluşturun--zaten oluşturulmadıysa.
* Adlandırın (ex-hibrit Azure AD JOIN).
* Düzenle & şuraya git:  Bilgisayar Yapılandırma > Ilkeleri Yönetim Şablonları > Windows bileşenleri > cihaz kaydı >
* Etkinleştirebilir  Etki alanına katılmış bilgisayarları cihaz olarak kaydetme
* Uygula ve Tamam ' a tıklayın.
* GPO 'YU tercih ettiğiniz konuma (kuruluş birimi, güvenlik grubu veya tüm cihazların etki alanı) bağlayın.

>[!NOTE]
>2012R2 için ilke ayarları, **bilgisayar yapılandırması > ilkeler > Yönetim Şablonları > Windows bileşenleri > Workplace Join > çalışma alanına katılma istemci bilgisayarları**

## <a name="next-steps"></a>Sonraki adımlar
[Cihaz geri yazmayı yapılandırma](how-to-connect-device-writeback.md)
