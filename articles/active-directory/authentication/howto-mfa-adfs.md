---
title: Azure AD MFA ve ADFS ile kaynakları güvenli hale getirme Azure Active Directory
description: Bu, Azure AD MFA ve bulutta AD FS kullanmaya başlamanızı açıklayan Azure AD Multi-Factor Authentication sayfasıdır.
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
ms.openlocfilehash: b42f920726d4b3046ab0c292e1090f5217e8b1f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96743250"
---
# <a name="securing-cloud-resources-with-azure-ad-multi-factor-authentication-and-ad-fs"></a>Azure AD Multi-Factor Authentication ve AD FS bulut kaynaklarının güvenliğini sağlama

Kuruluşunuz Azure Active Directory ile federe ise Azure AD tarafından erişilen kaynakları güvenli hale getirmek için Azure AD Multi-Factor Authentication veya Active Directory Federasyon Hizmetleri (AD FS) (AD FS) kullanın. Azure AD Multi-Factor Authentication veya Active Directory Federasyon Hizmetleri (AD FS) ile Azure Active Directory kaynaklarını güvenli hale getirmek için aşağıdaki yordamları kullanın.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>AD FS kullanarak Azure AD kaynaklarını güvenli hale getirme

Bulut kaynağınızın güvenliğini sağlamak için, kullanıcı iki adımlı doğrulamayı başarıyla gerçekleştirdiğinde Active Directory Federation Services tarafından multipleauthn talebinin yayılması için bir talep kuralı oluşturun. Bu talep Azure AD'ye aktarılır. İlerlemek için bu yordamı izleyin:

1. AD FS Yönetimi'ni açın.
2. Solda, **Bağlı Olan Taraf Güvenleri**’ni seçin.
3. **Microsoft Office 365 kimlik platformu** ' na sağ tıklayın ve **talep kurallarını Düzenle**' yi seçin.

   ![ADFS konsolu-bağlı olan taraf güvenleri](./media/howto-mfa-adfs/trustedip1.png)

4. Verme dönüştürme kurallarında **Kural Ekle**' ye tıklayın.

   ![Verme dönüştürme kurallarını Düzenle](./media/howto-mfa-adfs/trustedip2.png)

5. Dönüştürme Kuralı Ekleme Sihirbazı’nda, açılır menüde **Gelen Talep için Geçiş ya da Filtre**’yi seçin ve **İleri**’ye tıklayın.

   ![Ekran görüntüsü, bir talep kuralı şablonu seçtiğiniz dönüştürme talep kuralı ekleme Sihirbazı ' nı gösterir.](./media/howto-mfa-adfs/trustedip3.png)

6. Kuralınıza bir ad verin. 
7. Gelen talep türü olarak **Kimlik Doğrulama Yöntemleri Başvuruları**’nı seçin.
8. **Tüm talep değerlerini geçir**’i seçin.
    ![Ekran görüntüsü, tüm talep değerlerini geçir ' i seçtiğiniz dönüştürme talep kuralı ekleme Sihirbazı ' nı gösterir.](./media/howto-mfa-adfs/configurewizard.png)
9. **Finish (Son)** düğmesine tıklayın. AD FS Yönetim Konsolu'nu kapatın.

## <a name="trusted-ips-for-federated-users"></a>Federasyon kullanıcıları için Güvenilen IP'ler

Güvenilen IP'ler yöneticilerin, belirli IP adresleri ya da kendi intranetlerinden kaynaklanan taleplere sahip federasyon kullanıcıları için iki aşamalı doğrulamayı atlamasına izin verir. Aşağıdaki bölümlerde, Azure AD Multi-Factor Authentication güvenilir IP 'Lerin Federasyon kullanıcıları ile nasıl yapılandırılacağı ve bir isteğin bir Federasyon kullanıcıları intraneti içinden kaynaklandığı iki aşamalı doğrulama ile nasıl yapılandırılacağı açıklanır. Bu, AD FS’nin bir geçiş kullanacak ya da Kurumsal Ağ İçinde talep türü ile gelen bir talep şablonunu filtreleyecek şekilde yapılandırılmasıyla gerçekleştirilir.

Bu örnek, bağlı olan taraf Güvenlerimiz için Microsoft 365 kullanır.

### <a name="configure-the-ad-fs-claims-rules"></a>AD FS talep kurallarını yapılandırma

Yapmamız gereken ilk şey, AD FS taleplerini yapılandırmaktır. Biri Kurumsal Ağ İçinde talep türü için, diğeriyse kullanıcıların oturumunu açık şekilde tutmak için olmak üzere iki talep kuralı oluşturun.

1. AD FS Yönetimi'ni açın.
2. Solda, **Bağlı Olan Taraf Güvenleri**’ni seçin.
3. **Microsoft Office 365 kimlik platformu** ' na sağ tıklayın ve **talep kurallarını Düzenle..** 
    ![ . seçeneğini belirleyin. ADFS konsolu-talep kurallarını Düzenle](./media/howto-mfa-adfs/trustedip1.png)
4. Verme dönüştürme kurallarında **Kural Ekle** ' ye tıklayın. 
    ![ Talep kuralı ekleme](./media/howto-mfa-adfs/trustedip2.png)
5. Dönüştürme Kuralı Ekleme Sihirbazı’nda, açılır menüde **Gelen Talep için Geçiş ya da Filtre**’yi seçin ve **İleri**’ye tıklayın.
   ![Ekran görüntüsü, gelen talebi geçir veya filtrele ' yi seçtiğiniz dönüştürme talep kuralı ekleme Sihirbazı ' nı gösterir.](./media/howto-mfa-adfs/trustedip3.png)
6. Talep kuralı adının yanındaki kutuda kuralınıza bir ad verin. Örneğin: InsideCorpNet.
7. Gelen talep türü’nün yanındaki açılır menüde, **Kurumsal Ağ İçinde** seçeneğini belirleyin.
   ![Şirket ağı talebi Içine ekleniyor](./media/howto-mfa-adfs/trustedip4.png)
8. **Finish (Son)** düğmesine tıklayın.
9. Verme dönüştürme kurallarında **Kural Ekle**' ye tıklayın.
10. Dönüştürme Kuralı Ekleme Sihirbazı’nda, açılır menüden **Talepleri Özel Bir Kural Kullanarak Gönder**’i seçin ve **İleri**’ye tıklayın.
11. Talep kuralı adı: altındaki kutuya *Kullanıcıların Oturumlarını Açık Tut* ifadesini girin.
12. Özel kural kutusuna şunu girin:

```ad-fs-claim-rule
        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Create custom claim to keep users signed in](./media/howto-mfa-adfs/trustedip5.png)
```

13. **Finish (Son)** düğmesine tıklayın.
14. **Uygula**’ya tıklayın.
15. **Tamam**’a tıklayın.
16. AD FS Yönetimi'ni kapatın.

### <a name="configure-azure-ad-multi-factor-authentication-trusted-ips-with-federated-users"></a>Federasyon kullanıcılarıyla Azure AD Multi-Factor Authentication güvenilir IP 'Leri yapılandırma

Talepler yapıldığına göre, artık güvenilen IP’leri yapılandırabiliriz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Azure Active Directory**  >  **güvenlik**  >  **koşullu erişimi** olarak  >  **adlandırılan konumları** seçin.
3. **Koşullu erişim-adlandırılmış konumlar** DIKEY penceresinde **MFA güvenilir IP 'lerini Yapılandır** ' ı seçin.

   ![Azure AD koşullu erişim adlandırılmış konumlar MFA güvenilir IP 'Leri yapılandırma](./media/howto-mfa-adfs/trustedip6.png)

4. Hizmet Ayarları sayfasındaki **güvenilen IP'ler** altında bulunan **İntranetimde bulunan şirket dışındaki kullanıcıların istekleri için çok öğeli kimlik doğrulamayı atla** seçeneğini belirleyin.  
5. **Kaydet**' e tıklayın.

İşte bu kadar! Bu noktada, Federal Microsoft 365 kullanıcıların yalnızca bir talebin şirket intraneti dışından geldiği durumlarda MFA kullanması gerekir.
