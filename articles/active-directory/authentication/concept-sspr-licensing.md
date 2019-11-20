---
title: Lisans self servis parola sıfırlama-Azure Active Directory
description: Azure AD self servis parola sıfırlama lisanslama gereksinimleri
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d7a23ed0a84ebd671c69744dfec145763ef6897
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622726"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Azure AD self servis parola sıfırlama için lisans gereksinimleri

Azure Active Directory (Azure AD) çeşitli sürümlerde gelir: Ücretsiz, Premium P1 ve Premium P2. Farklı Azure AD sürümlerinde bulunan değişiklik, sıfırlama, kilit açma ve geri yazma dahil olmak üzere, self servis parola sıfırlamayı oluşturan farklı özelliklerden bazıları vardır. Bu makale farklılıkları açıklamaya çalışır. Her Azure AD sürümüne dahil olan özellikler hakkında daha fazla ayrıntı [Azure Active Directory fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/active-directory/)bulunabilir.

## <a name="compare-editions-and-features"></a>Sürümleri ve özellikleri karşılaştırma

Azure AD self servis parola sıfırlama Kullanıcı başına lisanslıdır, uyumluluk kuruluşları sağlamak için kullanıcılara uygun lisansı atamanız gerekir.

* Bulut kullanıcıları için Self Servis Parola Değiştirme
   * **Yalnızca bulutta** bulunan bir Kullanıcı ve parolamı öğrendim.
      * Parolamı yeni bir şekilde **değiştirmek** istiyorum.
   * Bu işlevsellik tüm Azure AD sürümlerinde bulunur.

* Bulut kullanıcıları için Self Servis Parola Sıfırlama
   * **Yalnızca bulutta bir Kullanıcı** ve parolamı unutdum.
      * Bilindiğim bir şeye Parolamı **sıfırlamak** istiyorum.
   * Bu işlevsellik Azure AD Premium P1 veya P2, Microsoft 365 İş veya Office 365 ' de bulunur.

* **Şirket içi geri yazma ile** self servis parola sıfırlama/değiştirme/kilit açma
   * Bir **Karma Kullanıcı** olan şirket içi Active Directory Kullanıcı hesabı, Azure AD Connect KULLANıLARAK Azure AD hesabım ile eşitlenir. Parolamı değiştirmek istiyorum, parolamı unutdum veya kilitlendim.
      * Parolamı değiştirmek veya bunu bildiğiniz bir şeye sıfırlamak ya da hesabımın kilidini açmak istiyorum **ve** değişikliğin şirket içi Active Directory geri eşitlendiğinden emin olmak istiyorum.
   * Bu işlevsellik Azure AD Premium P1 veya P2 ya da Microsoft 365 İş dahil edilmiştir.

> [!WARNING]
> Tek başına Office 365 lisans planları, *"Şirket içi geri yazma Ile self servis parola sıfırlama/değiştirme/kilit açma" desteği vermez* ve bu işlevin çalışması Için Azure AD Premium P1, Premium P2 veya Microsoft 365 iş içeren bir plan gerektirir.
>

Aşağıdaki sayfalarda maliyet dahil ek lisans bilgileri bulunabilir:

* [Azure Active Directory fiyatlandırma sitesi](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory özellikleri ve özellikleri](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Kurumsal](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 İş hizmet açıklaması](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Grup veya Kullanıcı tabanlı lisanslamayı etkinleştirme

Azure AD artık grup tabanlı lisanslamayı desteklemektedir. Yöneticiler, lisansları tek seferde atamak yerine bir kullanıcı grubuna toplu olarak atayabilirler. Daha fazla bilgi için bkz. [lisanslarla ilgili sorunları atama, doğrulama ve çözme](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Bazı Microsoft hizmetleri tüm konumlarda kullanılamaz. Bir lisansın bir kullanıcıya atanabilmesi için, yöneticinin kullanıcı üzerinde **Kullanım konumu** özelliğini belirtmesi gerekir. Lisansların atanması Azure Portal **Kullanıcı** > **profili** > **ayarları** bölümünde yapılabilir. *Grup lisansı atamasını kullandığınızda, kullanım konumu belirtilmemiş tüm kullanıcılar dizinin konumunu alır.*

## <a name="next-steps"></a>Sonraki adımlar

* [SSPR’yi başarılı bir şekilde nasıl piyasaya çıkarabilirim?](howto-sspr-deployment.md)
* [Parolanızı sıfırlama veya değiştirme](../user-help/active-directory-passwords-update-your-own-password.md)
* [Self servis parola sıfırlama için kaydolma](../user-help/active-directory-passwords-reset-register.md)
* [SSPR hangi verileri kullanır ve kullanıcılarınız için hangi verileri doldurmanız gerekir?](howto-sspr-authenticationdata.md)
* [Kullanıcılar hangi kimlik doğrulama yöntemlerini kullanabilir?](concept-sspr-howitworks.md#authentication-methods)
* [SSPR ile kullanılabilen ilke seçenekleri nelerdir?](concept-sspr-policy.md)
* [Parola geri yazma nedir ve neden önemlidir?](howto-sspr-writeback.md)
* [SSPR’de etkinliği nasıl bildirebilirim?](howto-sspr-reporting.md)
* [SSPR’deki tüm seçenekler nelerdir ve ne anlama gelir?](concept-sspr-howitworks.md)
* [Bir arıza olduğunu düşünüyorum. SSPR’de nasıl sorun giderebilirim?](active-directory-passwords-troubleshoot.md)
* [Başka bir yerde ele alınmayan bir sorum var](active-directory-passwords-faq.md)
