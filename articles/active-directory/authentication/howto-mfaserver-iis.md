---
title: IIS kimlik doğrulaması ve Azure MFA sunucusu-Azure Active Directory
description: IIS Kimlik Doğrulaması ve Azure Multi-Factor Authentication Sunucusu dağıtılıyor.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c522fb950e2489afce44553d233ac3eddf138730
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742179"
---
# <a name="configure-azure-multi-factor-authentication-server-for-iis-web-apps"></a>IIS web uygulamaları için Azure Multi-Factor Authentication Sunucusu

Microsoft IIS web uygulamaları ile IIS kimlik doğrulamasını etkinleştirmek ve yapılandırmak için Azure Multi-Factor Authentication (MFA) Sunucusu’nun IIS Kimlik Doğrulaması bölümünü kullanın. Azure MFA Sunucusu, Azure Multi-Factor Authentication eklemek üzere IIS web sunucusuna yapılan istekleri filtreleyebilen bir eklenti yükler. IIS eklentisi Form Tabanlı Kimlik Doğrulaması ve Tümleşik Windows HTTP Kimlik Doğrulaması için destek sağlar. Güvenilen IP’ler iç IP adreslerini iki öğeli kimlik doğrulamasından muaf tutmak için de kullanılabilir.

> [!IMPORTANT]
> 1 Temmuz 2019 itibariyle, Microsoft artık Yeni dağıtımlar için MFA sunucusu sağlamamaktadır. Oturum açma olayları sırasında çok faktörlü kimlik doğrulaması (MFA) gerektirmek isteyen yeni müşteriler, bulut tabanlı Azure AD Multi-Factor Authentication kullanmalıdır.
>
> Bulut tabanlı MFA 'yı kullanmaya başlamak için bkz. [öğretici: Azure AD Multi-Factor Authentication Ile güvenli Kullanıcı oturum açma olayları](tutorial-enable-azure-mfa.md).
>
> MFA sunucusunu 1 Temmuz 2019 tarihinden önce etkinleştiren mevcut müşteriler, en son sürümü, gelecekteki güncelleştirmeleri indirebilir ve her zamanki gibi etkinleştirme kimlik bilgilerini oluşturabilir.
>
> Bulut tabanlı Azure Multi-Factor Authentication kullandığınızda, Azure Multi-Factor Authentication (MFA) sunucusu tarafından sunulan IIS eklentisine bir alternatif yoktur. Bunun yerine, Active Directory Federasyon Hizmetleri (AD FS) (AD FS) veya Azure Active Directory uygulama proxy 'Si ile Web uygulaması ara sunucusu (WAP) kullanın.

![MFA sunucusunda IIS kimlik doğrulaması](./media/howto-mfaserver-iis/iis.png)

## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication Sunucusu ile Form Tabanlı IIS Kimlik Doğrulaması kullanma

Form tabanlı kimlik doğrulaması kullanan bir IIS web uygulamasını güvenli hale getirmek için, IIS web sunucusuna Azure Multi-Factor Authentication Sunucusu’nu yükleyin ve Sunucu’yu aşağıdaki yordama göre yapılandırın:

1. Azure Multi-Factor Authentication Sunucusu’nun soldaki menüsünde IIS Kimlik Doğrulaması simgesine tıklayın.
2. **Form Tabanlı** sekmesine tıklayın.
3. **Ekle**'ye tıklayın.
4. Kullanıcı adı, parola ve etki alanı değişkenlerini otomatik olarak algılamak için, `https://localhost/contoso/auth/login.aspx` otomatik yapılandırma Form-Based Web sitesi iletişim kutusuna oturum açma URL 'sini (gibi) girin ve **Tamam**' a tıklayın.
5. Tüm kullanıcılar sunucuya aktarılmışsa veya sunucuya aktarılacaksa ve Multi-Factor Authentication 'a tabi olursa **Kullanıcı eşleştirme Multi-Factor Authentication iste** kutusunu işaretleyin. Çok sayıda kullanıcı Sunucu’ya henüz aktarılmadı ve/veya multi-factor authentication’da muaf tutulacaksa, kutunun işaretini kaldırın.
6. Sayfa değişkenleri otomatik olarak algılanamıyorsa Otomatik Yapılandırma Form Tabanlı Web Sitesi iletişim kutusunda **Elle Belirt**’e tıklayın.
7. Otomatik Yapılandırma Form Tabanlı Web Sitesi iletişim kutusunda, URL Gönder alanına oturum açma sayfası URL’sini girin ve Uygulama adı girin (isteğe bağlı). Uygulama adı Azure Multi-Factor Authentication raporlarında görünür ve SMS veya Mobil Uygulama kimlik doğrulama iletilerinde görüntülenebilir.
8. Doğru İstek biçimini seçin. Bu, çoğu Web uygulaması için **Post veya Get** olarak ayarlanmıştır.
9. Kullanıcı adı değişkenini, Parola değişkenini ve Etki alanı değişkenini (oturum açma sayfasında görünüyorsa) girin. Girdi kutularının adlarını bulmak için bir web tarayıcısında oturum açma sayfasına gidin, sayfaya sağ tıklayın ve **Kaynağı Görüntüle**’yi seçin.
10. Tüm kullanıcılar sunucuya aktarılmışsa veya sunucuya aktarılacaksa ve Multi-Factor Authentication 'a tabi olursa, **Azure Multi-Factor Authentication Kullanıcı eşleştirme kutusu gerektir** kutusunu işaretleyin. Çok sayıda kullanıcı Sunucu’ya henüz aktarılmadı ve/veya multi-factor authentication’da muaf tutulacaksa, kutunun işaretini kaldırın.
11. Aşağıdaki gibi gelişmiş ayarları gözden geçirmek için **Gelişmiş**’e tıklayın:

    - Özel bir reddetme sayfa dosyası seçme
    - Tanımlama bilgilerini kullanarak web sitesinde başarılı kimlik doğrulamalarını belirli bir dönem boyunca önbelleğe alma
    - Birincil kimlik bilgilerinin bir Windows Etki Alanı, LDAP dizinine göre mi yoksa RADIUS sunucusuna göre mi doğrulanacağını belirtin.

12. Form Tabanlı Web Sitesi Ekle iletişim kutusuna dönmek için **Tamam**’a tıklayın.
13. **Tamam**'a tıklayın.
14. URL ve sayfa değişkenleri algılandığında veya girildiğinde, web sitesi verileri Form Tabanlı panelde görüntülenir.

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication Sunucusu ile Tümleşik Windows Kimlik Doğrulaması kullanma

Tümleşik Windows HTTP kimlik doğrulaması kullanan bir IIS web uygulamasını güvenli hale getirmek için, IIS web sunucusuna Azure MFA Sunucusu’nu yükleyin ve aşağıdaki adımları izleyerek Sunucu’yu yapılandırın:

1. Azure Multi-Factor Authentication Sunucusu’nun soldaki menüsünde IIS Kimlik Doğrulaması simgesine tıklayın.
2. **HTTP** sekmesine tıklayın.
3. **Ekle**'ye tıklayın.
4. Taban URL 'SI Ekle iletişim kutusunda, HTTP kimlik doğrulamasının gerçekleştirildiği Web sitesinin URL 'sini girin (gibi `http://localhost/owa` ) ve bir uygulama adı sağlayın (isteğe bağlı). Uygulama adı Azure Multi-Factor Authentication raporlarında görünür ve SMS veya Mobil Uygulama kimlik doğrulama iletilerinde görüntülenebilir.
5. Varsayılan yeterli değilse, Boşta kalma zaman aşımı ve Maksimum oturum sürelerini ayarlayın.
6. Tüm kullanıcılar sunucuya aktarılmışsa veya sunucuya aktarılacaksa ve Multi-Factor Authentication 'a tabi olursa **Kullanıcı eşleştirme Multi-Factor Authentication iste** kutusunu işaretleyin. Çok sayıda kullanıcı Sunucu’ya henüz aktarılmadı ve/veya multi-factor authentication’da muaf tutulacaksa, kutunun işaretini kaldırın.
7. İsterseniz **tanımlama bilgisi önbelleği** kutusunu işaretleyin.
8. **Tamam**'a tıklayın.

## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication Sunucusu için IIS Eklentilerini Etkinleştirme

Form Tabanlı ya da HTTP kimlik doğrulaması URL’lerini ve ayarlarını yapılandırdıktan sonra, Azure Multi-Factor Authentication IIS eklentilerinin IIS’de yüklenmesi ve etkinleştirilmesi gereken konumları seçin. Aşağıdaki yordamı kullanın:

1. IIS 6 ' da çalışıyorsa, **ISAPI** sekmesine tıklayın. Bu site için Azure Multi-Factor Authentication ISAPI filtresi eklentisini etkinleştirmek üzere Web uygulamasının altında çalıştığı Web sitesini (örneğin, varsayılan Web sitesi) seçin.
2. IIS 7 veya üzeri sürümlerde çalışıyorsa **yerel modül** sekmesine tıklayın. İstenen düzeylerde IIS eklentisini etkinleştirmek için sunucu, Web siteleri veya uygulamalar ' ı seçin.
3. Ekranın üst kısmındaki **IIS kimlik doğrulamasını etkinleştir** kutusuna tıklayın. Azure Multi-Factor Authentication artık seçilen IIS uygulamasını güvenli hale getirir. Kullanıcıların sunucuya aktarıldığından emin olun.

## <a name="trusted-ips"></a>Güvenilen IP'ler

Güvenilen IP'ler kullanıcıların belirli IP adresleri veya alt ağlardan kaynaklanan web sitesi istekleri için Azure Multi-Factor Authentication’ı atlamasına olanak tanır. Örneğin, ofisten oturum açarken kullanıcıların Azure Multi-Factor Authentication’dan muaf tutulmasını isteyebilirsiniz. Bunun için ofis alt ağını Güvenilen IP’ler girişi olarak belirtebilirsiniz. Güvenilen IP’leri yapılandırmak için aşağıdaki yordamı kullanın:

1. IIS Kimlik Doğrulaması bölümünde **Güvenilen IP'ler** sekmesine tıklayın.
2. **Ekle**'ye tıklayın.
3. Güvenilen IP Ekle iletişim kutusu göründüğünde, **tek IP**, **IP aralığı** veya **alt ağ** radyo düğmesini seçin.
4. İzin verilmesi gereken IP adresini, IP adresleri aralığını ya da alt ağı girin. Bir alt ağ giriyorsanız uygun Ağ maskesini seçip **Tamam**’a tıklayın.
