---
title: Dış paylaşım ve B2B işbirliği Microsoft 365-Azure AD
description: Microsoft 365 ve Azure Active Directory B2B işbirliği kullanarak dış iş ortakları ile kaynakları paylaşmayı açıklar.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: d003008ea5b0d2591574f6f488b0145ee6f08a5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100008137"
---
# <a name="microsoft-365-external-sharing-and-azure-active-directory-azure-ad-b2b-collaboration"></a>Microsoft 365 dış paylaşım ve Azure Active Directory (Azure AD) B2B işbirliği

Azure AD B2B işbirliği ve Microsoft 365 dış paylaşım (OneDrive, SharePoint Online, Birleşik Gruplar vb.) içinde, dış kullanıcıların kimliği Azure AD B2B kullanılarak doğrulanır.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Azure AD B2B, SharePoint Online 'daki dış paylaşımlardan farklı midir?

OneDrive/SharePoint Online 'ın ayrı bir davet Yöneticisi vardır. OneDrive/SharePoint Online 'da dış paylaşım desteği, Azure AD tarafından destek geliştirilerek başlatıldı. Zaman içinde, OneDrive/SharePoint Online dış paylaşımı, ürünün yerleşik paylaşım modelini kullanan birçok farklı özelliği ve milyonlarca kullanıcıyı tahakkuk etmiş. Ancak, OneDrive/SharePoint Online dış paylaşımının nasıl çalıştığı ve Azure AD B2B işbirliğinin nasıl çalıştığı arasında bazı hafif farklılıklar vardır. [Dış paylaşıma genel bakışta](/sharepoint/external-sharing-overview)OneDrive/SharePoint Online dış paylaşımı hakkında daha fazla bilgi edinebilirsiniz. İşlem genellikle Azure AD B2B 'den şu yollarla farklılık gösterir:

- OneDrive/SharePoint Online, kullanıcılar davetlerini kullandıktan sonra kullanıcıları dizine ekler. Bu nedenle, kullanmadan önce kullanıcıyı Azure AD portalında görmezsiniz. Bu sırada başka bir site bir kullanıcıyı davet eder, yeni bir davet oluşturulur. Ancak, Azure AD B2B işbirliğini kullandığınızda kullanıcılar her yerde gösterilmesi için davet üzerine hemen eklenir.

- OneDrive/SharePoint Online 'daki kullanım deneyimi, Azure AD B2B işbirliğinin deneyiminden farklı görünüyor. Bir Kullanıcı bir daveti etkinleştirdikten sonra, deneyimler benzer şekilde görünür.

- Azure AD B2B işbirliği davet edilen kullanıcılar OneDrive/SharePoint Online Paylaşım iletişim kutularından alınabilir. OneDrive/SharePoint Online davet edilen kullanıcılar, davetlerini kullandıktan sonra Azure AD 'de de görünür.

- Lisanslama gereksinimleri farklılık gösterir. Lisanslama hakkında daha fazla bilgi edinmek için bkz. [Azure AD dış kimlikleri lisanslama](./external-identities-pricing.md) ve [SharePoint Online dış paylaşımına genel bakış](/sharepoint/external-sharing-overview).
OneDrive/SharePoint Online 'da dış paylaşımı Azure AD B2B işbirliğiyle yönetmek için, OneDrive/SharePoint Online dış paylaşım ayarını **yalnızca kuruluşunuzun dizininde zaten var olan dış kullanıcılarla paylaşıma Izin verecek** şekilde ayarlayın. Kullanıcılar dışarıdan paylaşılan sitelere gidebilir ve yöneticinin eklediği dış ortak çalışanlarla seçim yapabilir. Yönetici, B2B işbirliği daveti API 'Leri aracılığıyla dış ortak çalışanları ekleyebilir.


![OneDrive/SharePoint dış paylaşım ayarı](media/o365-external-user/odsp-sharing-setting.png)

Dış paylaşımı etkinleştirdikten sonra, SharePoint Online (SPO) kişi seçicisindeki mevcut Konuk kullanıcıları arama özelliği, eski davranışa uyacak şekilde varsayılan olarak KAPALıDıR.

Kiracı ve site koleksiyonu düzeyinde ' ShowPeoplePickerSuggestionsForGuestUsers ' ayarını kullanarak bu özelliği etkinleştirebilirsiniz. Özelliği, Set-SPOTenant ve Set-SPOSite cmdlet 'lerini kullanarak ayarlayabilirsiniz. Bu özellik, üyelerin dizindeki tüm mevcut Konuk kullanıcıları aramasına izin verir. Kiracı kapsamındaki değişiklikler zaten sağlanmış olan SPO sitelerini etkilemez.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD B2B işbirliği nedir?](what-is-b2b.md)
* [Bir role B2B işbirliği kullanıcısı ekleme](add-guest-to-role.md)
* [B2B işbirliği davetlerini devretmek](delegate-invitations.md)
* [Dinamik Gruplar ve B2B işbirliği](use-dynamic-groups.md)
* [B2B işbirliği Azure Active Directory sorunlarını giderme](troubleshoot.md)
