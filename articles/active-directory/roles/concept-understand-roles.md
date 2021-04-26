---
title: Azure Active Directory rol kavramlarını anlayın
description: Azure Active Directory kaynak kapsamıyla yerleşik ve özel rolleri Azure Active Directory nasıl anlayacağınızı öğrenin.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/20/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71291458eec0aec13542d3e0dfaf04a96f391a23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466125"
---
# <a name="understand-roles-in-azure-active-directory"></a>Azure Active Directory rolleri anlama

Sabit bir rol izinleri kümesine sahip roller olan 60 Azure Active Directory (Azure AD) yerleşik rolleri vardır. Azure AD, yerleşik rolleri tamamlamak için özel rolleri de destekler. İstediğiniz rol izinlerini seçmek için özel rolleri kullanın. Örneğin, uygulamalar veya hizmet sorumluları gibi belirli Azure AD kaynaklarını yönetmek için bir tane oluşturabilirsiniz.

Bu makalede, Azure AD rollerinin ne olduğu ve nasıl kullanılabileceği açıklanmaktadır.

## <a name="how-azure-ad-roles-are-different-from-other-microsoft-365-roles"></a>Azure AD rolleri diğer Microsoft 365 rollerden farklı

Microsoft 365 Azure AD ve Intune gibi birçok farklı hizmet vardır. Bu hizmetlerden bazılarının kendi rol tabanlı erişim denetimi sistemleri vardır; engelle

- Azure AD
- Exchange
- Intune
- Güvenlik Merkezi
- Uyumluluk Merkezi
- Microsoft Cloud App Security
- Ticaret

Takımlar, SharePoint ve yönetilen Masaüstü gibi diğer hizmetlerin ayrı rol tabanlı erişim denetimi sistemleri yoktur. Yönetici erişimleri için Azure AD rollerini kullanırlar. Azure, sanal makineler gibi Azure kaynakları için kendi rol tabanlı erişim denetimi sistemine sahiptir ve bu sistem Azure AD rolleriyle aynı değildir.

Rol tabanlı erişim denetimi sistemi ayrı olarak söyliyoruz. rol tanımlarının ve rol atamalarının depolandığı farklı bir veri deposu olduğu anlamına gelir. Benzer şekilde, erişim denetimlerinin gerçekleştiği farklı bir ilke karar noktası vardır. Daha fazla bilgi için bkz. [Azure AD 'de Microsoft 365 hizmetleri Için roller](m365-workload-docs.md) ve [Klasik abonelik yöneticisi rolleri, Azure ROLLERI ve Azure AD rolleri](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="why-some-azure-ad-roles-are-for-other-services"></a>Bazı Azure AD rollerinin diğer hizmetler için nedenleri

Microsoft 365, her biri kendi hizmet portalı ile zaman içinde ayrı olarak geliştirilen rol tabanlı bir dizi erişim denetimi sistemine sahiptir. Azure AD portalından Microsoft 365 kimliği arasında kimlik yönetmenizi kolaylaştırmak için, her biri bir Microsoft 365 hizmetine yönetim erişimi veren, hizmete özgü bazı yerleşik roller ekledik. Bu ek bir örnek olarak, Azure AD 'de Exchange yönetici rolü bulunur. Bu rol, Exchange rol tabanlı erişim denetimi sisteminde [Kuruluş Yönetimi rol grubuna](/exchange/organization-management-exchange-2013-help) eşdeğerdir ve Exchange 'in tüm yönlerini yönetebilir. Benzer şekilde, Intune yönetici rolünü, takımlar yöneticisini, SharePoint yöneticisini ve benzerlerini ekledik. Hizmete özgü roller, aşağıdaki bölümde yer alan Azure AD yerleşik rollerinin bir kategorisidir.

## <a name="categories-of-azure-ad-roles"></a>Azure AD rollerinin kategorileri

Azure AD yerleşik rolleri, aşağıdaki üç geniş kategoriye giren yerlerde farklılık gösterir.

- **Azure AD 'ye özgü roller**: Bu roller yalnızca Azure AD içindeki kaynakları yönetme izinleri verir. Örneğin, Kullanıcı Yöneticisi, uygulama Yöneticisi, gruplar Yöneticisi, Azure AD 'de yaşayan kaynakları yönetme izinleri verir.
- **Hizmete özgü roller**: Ana Microsoft 365 Hizmetleri (Azure AD) için, hizmet içindeki tüm özellikleri yönetme izinleri veren hizmete özel roller sunuyoruz.  Örneğin, Exchange Yöneticisi, Intune Yöneticisi, SharePoint Yöneticisi ve takımlar yönetici rolleri, özellikleri ilgili hizmetleriyle yönetebilir. Exchange Yöneticisi posta kutularını yönetebilir, Intune Yöneticisi cihaz ilkelerini yönetebilir, SharePoint Yöneticisi site koleksiyonlarını yönetebilir, ekip yöneticileri, çağrı kalitelerini yönetebilir ve bu şekilde devam edebilir.
- **Çapraz hizmet rolleri**: hizmetleri kapsayan bazı roller vardır. Genel yönetici ve küresel okuyucu olmak üzere iki genel roliz vardır. Tüm Microsoft 365 hizmetleri bu iki rolü dikkate getirir. Ayrıca, güvenlik yöneticisi ve güvenlik okuyucusu gibi güvenlikle ilgili bazı roller de Microsoft 365 içindeki birden çok güvenlik hizmeti arasında erişim izni verir. Örneğin, Azure AD 'de güvenlik yöneticisi rollerini kullanarak, Microsoft 365 Güvenlik Merkezi, Microsoft Defender Gelişmiş tehdit koruması ve Microsoft Cloud App Security yönetebilirsiniz. Benzer şekilde, uyumluluk Yöneticisi rolünde uyumlulukla ilgili ayarları Microsoft 365 Uyumluluk Merkezi, Exchange ve benzeri bir şekilde yönetebilirsiniz.

![Azure AD yerleşik rollerinin üç kategorisi](./media/concept-understand-roles/role-overlap-diagram.png)

Aşağıdaki tablo, bu rol kategorilerini anlamak için bir yardım olarak sunulmuştur. Kategoriler rastgele olarak adlandırılır ve [belgelenen rol izinlerinin](permissions-reference.md)ötesinde başka herhangi bir özelliği belirlemede tasarlanmamıştır.

Kategori | Rol
---- | ----
Azure AD 'ye özgü roller | Uygulama Yöneticisi<br>Uygulama Geliştirici<br>Kimlik doğrulama Yöneticisi<br>B2C ıEF anahtar kümesi Yöneticisi<br>B2C ıEF Ilke Yöneticisi<br>Bulut Uygulaması Yöneticisi<br>Bulut Cihaz Yöneticisi<br>Koşullu Erişim Yöneticisi<br>Cihaz Yöneticileri<br>Dizin okuyucuları<br>Dizin eşitleme hesapları<br>Dizin yazarları<br>Dış KIMLIK Kullanıcı akış Yöneticisi<br>Dış KIMLIK Kullanıcı akışı öznitelik Yöneticisi<br>Dış kimlik sağlayıcısı Yöneticisi<br>Grup Yöneticisi<br>Konuk davetci<br>Yardım Masası Yöneticisi<br>Karma kimlik yöneticisi<br>Lisans Yöneticisi<br>Partner Katman1 desteği<br>Partner Katman2 desteği<br>Parola Yöneticisi<br>Ayrıcalıklı kimlik doğrulama Yöneticisi<br>Ayrıcalıklı rol yöneticisi<br>Rapor okuyucu<br>Kullanıcı hesabı Yöneticisi
Çapraz hizmet rolleri | Genel Yönetici<br>Uyumluluk Yöneticisi<br>Uyumluluk verileri Yöneticisi<br>Genel okuyucu<br>Güvenlik Yöneticisi<br>Güvenlik operatörü<br>Güvenlik Okuyucusu<br>Hizmet desteği Yöneticisi
Hizmete özgü roller | Azure DevOps Yöneticisi<br>Azure Information Protection Yöneticisi<br>Faturalama Yöneticisi<br>CRM hizmet Yöneticisi<br>Müşteri Kasası erişim onaylayıcısı<br>Masaüstü Analizi Yöneticisi<br>Exchange hizmeti Yöneticisi<br>Öngörüler Yöneticisi<br>Öngörüler Iş lideri<br>Intune Hizmet Yöneticisi<br>Kaizala Yöneticisi<br>Lync Hizmet Yöneticisi<br>İleti Merkezi Gizlilik okuyucusu<br>İleti Merkezi okuyucusu<br>Modern ticari Kullanıcı<br>Ağ Yöneticisi<br>Office uygulamaları Yöneticisi<br>Power BI Hizmet Yöneticisi<br>Power Platform Yöneticisi<br>Yazıcı Yöneticisi<br>Yazıcı teknisyeni<br>Yönetici ara<br>Arama Düzenleyicisi<br>SharePoint Hizmet Yöneticisi<br>Takımlar Iletişim Yöneticisi<br>Takımlar Iletişimleri Destek Mühendisi<br>Takımlar Iletişimleri destek uzmanı<br>Takımlar cihazları Yöneticisi<br>Takımlar Hizmet Yöneticisi

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD rol tabanlı erişim denetimine genel bakış](custom-overview.md)
- [Azure Portal, Azure AD PowerShell ve Graph API](custom-create.md) kullanarak rol atamaları oluşturun
- [Rol atamalarını listeleme](view-assignments.md)
