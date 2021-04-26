---
title: 'Azure AD Connect: sorunsuz tek Sign-On-nasıl çalıştığı | Microsoft Docs'
description: Bu makalede Azure Active Directory kesintisiz tek Sign-On özelliğinin nasıl çalıştığı açıklanır.
services: active-directory
keywords: Azure AD Connect nedir, yükler Active Directory, Azure AD, SSO, çoklu oturum açma için gerekli bileşenler
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bde937adba8d2469390a6cf404f6cce8c5008e87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100369654"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Sorunsuz çoklu oturum açma Azure Active Directory: teknik kapsamlı bakış

Bu makalede, Azure Active Directory kesintisiz tek Sign-On (sorunsuz SSO) özelliğinin nasıl çalıştığı hakkında teknik ayrıntılar verilmektedir.

## <a name="how-does-seamless-sso-work"></a>Sorunsuz SSO nasıl çalışır?

Bu bölümde üç bölümü vardır:

1. Sorunsuz SSO özelliğinin kurulumu.
2. Web tarayıcısında tek bir Kullanıcı oturum açma işlemi sorunsuz SSO ile nasıl kullanılır?
3. Yerel istemcideki tek bir Kullanıcı oturum açma işlemi sorunsuz SSO ile nasıl kullanılır?

### <a name="how-does-set-up-work"></a>Çalışma nasıl ayarlanır?

[Burada](how-to-connect-sso-quick-start.md)gösterildiği gibi, sorunsuz SSO Azure AD Connect kullanılarak etkinleştirilir. Özelliği etkinleştirirken, aşağıdaki adımlar oluşur:

- `AZUREADSSOACC`Azure AD 'ye eşitlediğiniz her ad ormanında şirket içi Active Directory (ad) bir bilgisayar hesabı () oluşturulur (Azure AD Connect kullanarak).
- Ayrıca, Azure AD oturum açma işlemi sırasında kullanılmak üzere bir dizi Kerberos hizmet asıl adı (SPN) oluşturulur.
- Bilgisayar hesabının Kerberos şifre çözme anahtarı Azure AD ile güvenli bir şekilde paylaşılır. Birden çok AD Ormanı varsa, her bilgisayar hesabının kendi benzersiz Kerberos şifre çözme anahtarı olur.

>[!IMPORTANT]
> `AZUREADSSOACC`Güvenlik nedenleriyle bilgisayar hesabının güçlü korunması gerekir. Bilgisayar hesabını yalnızca etki alanı yöneticileri yönetebilmelidir. Bilgisayar hesabında Kerberos temsilcisinin devre dışı bırakıldığından ve Active Directory ' deki başka hiçbir hesabın bilgisayar hesabında temsilciliizin izinlerine sahip olduğundan emin olun `AZUREADSSOACC` . Bilgisayar hesabını, yanlışlıkla silinmelerden güvenli oldukları ve yalnızca etki alanı yöneticilerinin erişimi olan bir kuruluş biriminde (OU) depolayın. Bilgisayar hesabındaki Kerberos şifre çözme anahtarı da hassas olarak değerlendirilmelidir. Bilgisayar hesabının [Kerberos şifre çözme anahtarını](how-to-connect-sso-faq.md) `AZUREADSSOACC` en az 30 günde bir ele almanızı önemle tavsiye ederiz.

>[!IMPORTANT]
> Sorunsuz SSO, Kerberos için AES256_HMAC_SHA1, AES128_HMAC_SHA1 ve RC4_HMAC_MD5 şifreleme türlerini destekler. AzureADSSOAcc $ hesabı için şifreleme türü AES256_HMAC_SHA1 olarak, ek güvenlik için de AES türlerinden biri de. RC4 olarak ayarlanmalıdır. Şifreleme türü, Active Directory hesabın msDS-SupportedEncryptionTypes özniteliğinde depolanır.  AzureADSSOAcc $ Account ENCRYPTION türü RC4_HMAC_MD5 olarak ayarlandıysa ve bunu AES şifreleme türlerinden biri olarak değiştirmek istiyorsanız, lütfen ilk olarak AzureADSSOAcc $ hesabının Kerberos şifre çözme anahtarını, ilgili sorunun altındaki [SSS belgesinde](how-to-connect-sso-faq.md) açıklandığı gibi aldığınızdan emin olun, aksi takdırde sorunsuz SSO gerçekleşmeyecektir.

Kurulum tamamlandıktan sonra sorunsuz SSO, tümleşik Windows kimlik doğrulaması (ıWA) kullanan başka bir oturum açma yöntemiyle aynı şekilde çalışır.

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Sorunsuz SSO ile bir Web tarayıcısında oturum açma nasıl çalışır?

Bir Web tarayıcısında oturum açma akışı aşağıdaki gibidir:

1. Kullanıcı, bir Web uygulamasına (örneğin, Outlook Web App- https://outlook.office365.com/owa/) Şirket ağınızdaki etki alanına katılmış bir şirket aygıtından) erişmeyi dener.
2. Kullanıcı önceden oturum açmamışsa, Kullanıcı Azure AD oturum açma sayfasına yönlendirilir.
3. Kullanıcı, Kullanıcı adlarını Azure AD oturum açma sayfasına göre yapılır.

   >[!NOTE]
   >[Bazı uygulamalar](./how-to-connect-sso-faq.md)için adım 2 & 3 atlanır.

4. Azure AD, arka planda JavaScript kullanarak, bir Kerberos bileti sağlamak için tarayıcıyı 401 Yetkisiz bir yanıt aracılığıyla zorluk halinde sunmaktadır.
5. Tarayıcı, sırasıyla bilgisayar hesabı için Active Directory bir bilet ister `AZUREADSSOACC` (Azure AD 'yi temsil eder).
6. Active Directory bilgisayar hesabını bulur ve bilgisayar hesabının gizli anahtarı ile şifrelenen tarayıcıya Kerberos bileti döndürür.
7. Tarayıcı Active Directory 'tan elde edilen Kerberos biletini Azure AD 'ye iletir.
8. Azure AD, önceden paylaşılan anahtar kullanılarak şirket cihazında oturum açan kullanıcının kimliğini içeren Kerberos biletinin şifresini çözer.
9. Değerlendirmeden sonra Azure AD uygulamaya bir belirteç döndürür ya da kullanıcıdan Multi-Factor Authentication gibi ek provalar gerçekleştirmesini ister.
10. Kullanıcı oturum açma işlemi başarılı olursa, Kullanıcı uygulamaya erişebilir.

Aşağıdaki diyagramda tüm bileşenler ve ilgili adımlar gösterilmektedir.

![Sorunsuz çoklu oturum açma-Web uygulaması akışı](./media/how-to-connect-sso-how-it-works/sso2.png)

Sorunsuz SSO fırsatçı, bu da başarısız olursa, oturum açma deneyiminin normal davranışına geri dönmesi, yani kullanıcının oturum açması için parolasını girmesi gerekir.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Sorunsuz SSO ile yerel bir istemcide oturum açma nasıl çalışır?

Yerel istemcideki oturum açma akışı aşağıdaki gibidir:

1. Kullanıcı, yerel bir uygulamaya (örneğin, Outlook istemcisi) şirket ağınız içindeki etki alanına katılmış bir kurumsal cihazdan erişmeyi dener.
2. Kullanıcı önceden oturum açmamışsa, yerel uygulama kullanıcının Kullanıcı adını cihazın Windows oturumundan alır.
3. Uygulama, Kullanıcı adını Azure AD 'ye gönderir ve kiracınızın WS-Trust MEX Uç noktasını alır. Bu WS-Trust uç noktası, yalnızca sorunsuz SSO özelliği tarafından kullanılır ve Azure AD 'de WS-Trust protokolünün genel bir uygulamasıdır.
4. Ardından uygulama, tümleşik kimlik doğrulama uç noktasının kullanılabilir olup olmadığını görmek için WS-Trust MEX Uç noktasını sorgular. Tümleşik kimlik doğrulama uç noktası, yalnızca sorunsuz SSO özelliği tarafından kullanılır.
5. 4. adım başarılı olursa, Kerberos sınaması verilir.
6. Uygulama Kerberos anahtarını alabiliyor ise, Azure AD 'nin tümleşik kimlik doğrulama uç noktasına iletir.
7. Azure AD, Kerberos biletinin şifresini çözer ve doğrular.
8. Azure AD, kullanıcıyı ' de imzalar ve uygulamaya bir SAML belirteci yayınlar.
9. Uygulama daha sonra SAML belirtecini Azure AD 'nin OAuth2 belirteç uç noktasına gönderir.
10. Azure AD, SAML belirtecini doğrular ve belirtilen kaynak için bir erişim belirteci ile uygulama ve bir yenileme belirteci ve bir kimlik belirteci doğrular.
11. Kullanıcı uygulamanın kaynağına erişimi alır.

Aşağıdaki diyagramda tüm bileşenler ve ilgili adımlar gösterilmektedir.

![Kesintisiz çoklu oturum açma-yerel uygulama akışı](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>Sonraki adımlar

- [**Hızlı başlangıç**](how-to-connect-sso-quick-start.md) -Azure AD sorunsuz SSO 'yu alın ve çalıştırın.
- [**Sık sorulan sorular**](how-to-connect-sso-faq.md) -sık sorulan soruların yanıtları.
- [**Sorun giderme**](tshoot-connect-sso.md) -özellikle ilgili yaygın sorunları çözmeyi öğrenin.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) -yeni özellik isteklerini dosyalama.
