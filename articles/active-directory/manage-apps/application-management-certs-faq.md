---
title: Uygulama yönetimi sertifikaları hakkında sık sorulan sorular Azure Active Directory
description: Kimlik sağlayıcısı (IDP) olarak Azure Active Directory kullanarak uygulamalar için sertifikaları yönetme hakkında sık sorulan soruların (SSS) yanıtlarını öğrenin.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 03/19/2021
ms.author: iangithinji
ms.reviewer: secherka, mifarca, shchaur, shravank, sureshja
ms.openlocfilehash: 0868c942a023662a1a6d3053477d85b0245fef4b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376248"
---
# <a name="azure-active-directory-azure-ad-application-management-certificates-frequently-asked-questions"></a>Azure Active Directory (Azure AD) uygulama yönetimi sertifikaları hakkında sık sorulan sorular

Bu sayfa, kimlik sağlayıcısı (IDP) olarak Azure Active Directory (Azure AD) kullanan uygulamalar için sertifikaları yönetme hakkında sık sorulan soruları yanıtlar.

## <a name="is-there-a-way-to-generate-a-list-of-expiring-saml-signing-certificates"></a>Süresi dolan SAML imzalama sertifikalarının bir listesini oluşturmak için bir yol var mı?

[PowerShell betikleri](app-management-powershell-samples.md)aracılığıyla bir CSV dosyasındaki dizininizden belirtilen uygulamalar için süresi dolan gizli dizileri, sertifikaları ve onların sahiplerini kullanarak tüm uygulama kayıtlarını dışarı aktarabilirsiniz. 

## <a name="where-can-i-find-the-information-about-soon-to-expire-certificates-renewal-steps"></a>Sertifika yenileme adımlarının süresini yakında dolacak olan bilgileri nereden bulabilirim?

Adımları [burada](manage-certificates-for-federated-single-sign-on.md#renew-a-certificate-that-will-soon-expire) bulabilirsiniz.

## <a name="how-can-i-customize-the-expiration-date-for-the-certificates-issued-by-azure-ad"></a>Azure AD tarafından verilen sertifikaların sona erme tarihini nasıl özelleştirebilirim?

Varsayılan olarak, Azure AD bir sertifikayı SAML çoklu oturum açma yapılandırması sırasında otomatik olarak oluşturulduğunda üç yıl sonra sona ermek üzere yapılandırır. Kaydettikten sonra bir sertifikanın tarihini değiştiremiyoruz, yeni bir sertifika oluşturmanız gerekir. Bunun nasıl yapılacağı hakkında adımlar için lütfen [Federasyon sertifikanızın sona erme tarihini özelleştirin ve yeni bir sertifikaya geri alın](manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate).

## <a name="how-can-i-automate-the-certificates-expiration-notifications"></a>Sertifikaların süre sonu bildirimlerini nasıl otomatikleştirebilirim?

Azure AD, SAML sertifikasının süresi dolmadan 60, 30 ve 7 gün önce bir e-posta bildirimi gönderir. Bildirimleri almak için birden fazla e-posta adresi ekleyebilirsiniz. 

> [!NOTE]
> Bildirim listesine en çok 5 e-posta adresi ekleyebilirsiniz (uygulamayı ekleyen yöneticinin e-posta adresi dahil). Daha fazla kişiye bildirimde bulunulmasına gerek duyuyorsanız dağıtım listesi e-postalarını kullanın. 

Bildirimlerin gönderilmesini istediğiniz e-postaları belirtmek için, bkz. [sertifika süre sonu için e-posta bildirim adresleri ekleme](manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration).

' Den alınan bu e-posta bildirimlerini düzenleme veya özelleştirme seçeneği yoktur `aadnotification@microsoft.com` . Ancak, [PowerShell betikleri](app-management-powershell-samples.md)aracılığıyla süresi dolan gizli dizileri ve sertifikaları kullanarak uygulama kayıtlarını dışarı aktarabilirsiniz.

## <a name="who-can-update-the-certificates"></a>Sertifikaları kimler güncelleştirebilir?

Uygulamanın sahibi veya genel yönetici ya da uygulama Yöneticisi sertifikaları Azure portal Kullanıcı arabirimi, PowerShell veya Microsoft Graph aracılığıyla güncelleştirebilir.

## <a name="i-need-more-details-about-certificate-signing-options"></a>Sertifika imzalama seçenekleriyle ilgili daha fazla ayrıntıya ihtiyacım var.

Azure AD 'de sertifika imzalama seçeneklerini ve sertifika imzalama algoritmasını ayarlayabilirsiniz. Daha fazla bilgi edinmek için bkz. [Azure AD uygulamaları Için GELIŞMIŞ SAML belirteci sertifika imzalama seçenekleri](certificate-signing-options.md).

## <a name="i-need-to-replace-the-certificate-for-azure-ad-application-proxy-applications-and-need-more-instructions"></a>Azure AD Uygulama Ara Sunucusu uygulamaları için sertifikayı değiştirmem ve daha fazla yönerge yapmanız gerekir.

Azure AD Uygulama Ara Sunucusu uygulamalarına yönelik sertifikaları değiştirmek için bkz. [PowerShell örneği-uygulama proxy 'si uygulamalarında sertifikayı değiştirme](scripts/powershell-get-custom-domain-replace-cert.md).

## <a name="how-do-i-manage-certificates-for-custom-domains-in-azure-ad-application-proxy"></a>Azure AD Uygulama Ara Sunucusu özel etki alanları için sertifikaları yönetmek Nasıl yaparım??

Şirket içi bir uygulamayı özel etki alanı kullanacak şekilde yapılandırmak için, bir özel etki alanı, özel etki alanı için bir PFX sertifikası ve yapılandırılacak şirket içi bir uygulama için onaylanmış bir Azure Active Directory gerekir. Daha fazla bilgi için bkz. [Azure AD uygulama ara sunucusu özel etki alanları](application-proxy-configure-custom-domain.md). 

## <a name="i-need-to-update-the-token-signing-certificate-on-the-application-side-where-can-i-get-it-on-azure-ad-side"></a>Uygulama tarafında belirteç imzalama sertifikasını güncelleştirmem gerekiyor. Azure AD tarafında nereden alabilirim?

SAML X. 509.952 sertifikasını yenileyebilmeniz için bkz. [SAML imzalama sertifikası](configure-saml-single-sign-on.md#saml-signing-certificate).

## <a name="what-is-azure-ad-signing-key-rollover"></a>Azure AD imzalama anahtar geçişi nedir?

Daha fazla ayrıntı için [buradan](../develop/active-directory-signing-key-rollover.md)daha fazla bilgi bulabilirsiniz. 

## <a name="how-do-i-renew-application-token-encryption-certificate"></a>Uygulama belirteci şifreleme sertifikası Nasıl yaparım? yenilensin mi?

Bir uygulama belirteci şifreleme sertifikasını yenilemek için bkz. bir [Kurumsal uygulama için belirteç şifreleme sertifikasını yenileme](howto-saml-token-encryption.md). 

## <a name="how-do-i-renew-application-token-signing-certificate"></a>Uygulama belirteci imza sertifikası Nasıl yaparım? yenilensin mi?

Bir uygulama belirteci imzalama sertifikasını yenilemek için bkz. bir [Kurumsal uygulama için belirteç imzalama sertifikasını yenileme](manage-certificates-for-federated-single-sign-on.md).

## <a name="how-do-i-update-azure-ad-after-changing-my-federation-certificates"></a>Federasyon Sertifikalarımı değiştirdikten sonra Azure AD Nasıl yaparım? güncelleştirilsin mi?

Federasyon sertifikalarınızı değiştirdikten sonra Azure AD 'yi güncelleştirmek için bkz. [Microsoft 365 ve Azure Active Directory için Federasyon sertifikalarını yenileme](../hybrid/how-to-connect-fed-o365-certs.md).
