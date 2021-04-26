---
title: Azure AD yerleşik rolleri-Azure Active Directory
description: Azure Active Directory yerleşik roller ve izinler açıklanmaktadır.
services: active-directory
author: rolyon
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 04/20/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf614922503212488c822ac020960b0ddb99fc28
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780074"
---
# <a name="azure-ad-built-in-roles"></a>Azure AD yerleşik rolleri

Azure Active Directory (Azure AD) içinde, başka bir yönetici veya yönetici olmayan Azure AD kaynaklarını yönetmesi gerekiyorsa, bunlara gereken izinleri sağlayan bir Azure AD rolü atarsınız. Örneğin, Kullanıcı ekleme veya değiştirme, Kullanıcı parolalarını sıfırlama, kullanıcı lisanslarını yönetme veya etki alanı adlarını yönetme izni vermek için roller atayabilirsiniz.

Bu makalede, Azure AD kaynaklarının yönetimine izin vermek için atayabileceğiniz Azure AD yerleşik rolleri listelenir. Rol atama hakkında daha fazla bilgi için bkz. [kullanıcılara Azure AD rolleri atama](manage-roles-portal.md).

## <a name="all-roles"></a>Tüm roller

> [!div class="mx-tableFixed"]
> | Rol | Açıklama | Şablon Kimliği |
> | --- | --- | --- |
> | [Uygulama Yöneticisi](#application-administrator) | , Uygulama kayıtlarının ve kurumsal uygulamaların tüm yönlerini oluşturabilir ve yönetebilir. | 9b895d92-2cd3-44C7-9D02-a6ac2d5ea5c3 |
> | [Uygulama Geliştirici](#application-developer) | ' Kullanıcılar uygulama kaydedebilir ' ayarından bağımsız olarak uygulama kayıtları oluşturabilir. | cf1c38e5-3621-4004-a7cb-879624dced7c |
> | [Saldırı yükü yazarı](#attack-payload-author) | , Bir yöneticinin daha sonra başlatabileceği saldırı yükleri oluşturabilir. | 9c6df0f2-1e7c-4dc3-B195-66dfbd24aa8f |
> | [Saldırı simülasyonu Yöneticisi](#attack-simulation-administrator) | , Saldırı Benzetimi kampanyalarının tüm yönlerini oluşturabilir ve yönetebilir. | c430b396-e693-46cc-96f3-db01bf8bb62a |
> | [Kimlik doğrulama Yöneticisi](#authentication-administrator) | Yönetici olmayan kullanıcılar için kimlik doğrulama yöntemi bilgilerini görüntüleme, ayarlama ve sıfırlama erişimi sağlayabilir. | c4e39bd9-1100-46d3-8c65-fb160da0071f |
> | [Kimlik doğrulama Ilkesi Yöneticisi](#authentication-policy-administrator) | , Kimlik doğrulama yöntemlerinin ve parola koruma ilkelerinin tüm yönlerini oluşturabilir ve yönetebilir. | 0526716b-113d-4c15-b2c8-68e3c22b9f80 |
> | [Azure AD 'ye katılmış cihaz yerel Yöneticisi](#azure-ad-joined-device-local-administrator) | Bu role atanan kullanıcılar, Azure AD 'ye katılmış cihazlarda yerel Yöneticiler grubuna eklenir. | 9f06204d-73c1-4d4c-880a-6edb90606fd8 |
> | [Azure DevOps Yöneticisi](#azure-devops-administrator) | , Azure DevOps kuruluş ilkesini ve ayarlarını yönetebilir. | e3973bdf-4987-49ae-837a-ba8e231c7286 |
> | [Azure Information Protection Yöneticisi](#azure-information-protection-administrator) | Azure Information Protection ürünün tüm yönlerini yönetebilir. | 7495fdc4-34c4-4d15-A289-98788ce399fd |
> | [B2C ıEF anahtar kümesi Yöneticisi](#b2c-ief-keyset-administrator) | , Kimlik deneyimi çerçevesinde (ıEF) Federasyon ve şifreleme için gizli dizileri yönetebilir. | aaf43236-0c0d-4d5f-883a-6955382ac081 |
> | [B2C ıEF Ilke Yöneticisi](#b2c-ief-policy-administrator) | , Kimlik deneyimi çerçevesinde (ıEF) güven çerçevesi ilkeleri oluşturabilir ve yönetebilir. | 3edaf663-341e-4475-9f94-5c398ef6c070 |
> | [Faturalama yöneticisi](#billing-administrator) | , Ödeme bilgilerini güncelleştirme gibi genel faturalandırma ile ilgili görevleri gerçekleştirebilir. | b0f54661-2d74-4c50-afa3-1ec803f12efe |
> | [Bulut Uygulaması Yöneticisi](#cloud-application-administrator) | Uygulama kayıtları ve kurumsal uygulamaların uygulama proxy 'Si hariç tüm yönlerini oluşturabilir ve yönetebilir. | 158c047a-C907-4556-b7ef-446551a6b5f7 |
> | [Bulut Cihaz Yöneticisi](#cloud-device-administrator) | Azure AD 'de cihazları yönetmek için sınırlı erişim. | 7698a772-787B-4ac8-901f-60d6b08afd2 |
> | [Uyumluluk Yöneticisi](#compliance-administrator) | , Azure AD ve Microsoft 365 uyumluluk yapılandırma ve raporlarını okuyabilir ve yönetebilir. | 17315797-102D-40b4-93e0-432062caca18 |
> | [Uyumluluk verileri Yöneticisi](#compliance-data-administrator) | Uyumluluk içeriğini oluşturur ve yönetir. | e6d1a23a-da11-4be4-9570-befc86d067a7 |
> | [Koşullu Erişim Yöneticisi](#conditional-access-administrator) | , Koşullu erişim yeteneklerini yönetebilir. | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9 |
> | [Müşteri Kasası erişim onaylayıcısı](#customer-lockbox-access-approver) | , Müşteri kurumsal verilerine erişmek için Microsoft destek isteklerini onaylayabilir. | 5c4f9dcd-47dc-4CF7-8c9a-9e4207cbfc91 |
> | [Masaüstü Analizi Yöneticisi](#desktop-analytics-administrator) | , Masaüstü yönetim araçlarına ve hizmetlerine erişebilir ve bunları yönetebilir. | 38a96431-2bdf-4B4C-8B6E-5d3d8abac1a4 |
> | [Dizin okuyucuları](#directory-readers) | Temel dizin bilgilerini okuyabilir. Genellikle uygulamalara ve konuklara dizin okuma erişimi vermek için kullanılır. | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b |
> | [Dizin eşitleme hesapları](#directory-synchronization-accounts) | Yalnızca Azure AD Connect hizmeti tarafından kullanılır. | d29b2b05-8046-44ba-8758-1e26182fcf32 |
> | [Dizin yazarları](#directory-writers) | , Temel dizin bilgilerini okuyabilir ve yazabilir. Uygulamalara erişim vermek için, kullanıcılara yönelik değildir. | 9360feb5-f418-4baa-8175-e2a00bac4301 |
> | [Etki alanı adı yöneticisi](#domain-name-administrator) | , Bulutta ve şirket içinde etki alanı adlarını yönetebilir. | 8329153b-31d0-4727-b945-745eb3bc5f31 |
> | [Dynamics 365 Yöneticisi](#dynamics-365-administrator) | , Dynamics 365 ürününün tüm yönlerini yönetebilir. | 44367163-eba1-44c3-98af-f5787879f96a |
> | [Exchange Yöneticisi](#exchange-administrator) | , Exchange ürününün tüm yönlerini yönetebilir. | 29232cdf-9323-42fd-ade2-1d097af3e4de |
> | [Exchange alıcı Yöneticisi](#exchange-recipient-administrator) | Exchange Online Kurumsal içinde Exchange Online alıcıları oluşturabilir veya güncelleştirebilir. | 31392ffb-586c-42d1-9346-e59415a2cc4e |
> | [Dış KIMLIK Kullanıcı akış Yöneticisi](#external-id-user-flow-administrator) | , Kullanıcı akışlarının tüm yönlerini oluşturabilir ve yönetebilir. | 6e591065-9bad-43ed-90f3-e9424366d2f0 |
> | [Dış KIMLIK Kullanıcı akışı öznitelik Yöneticisi](#external-id-user-flow-attribute-administrator) | , Tüm Kullanıcı akışları için kullanılabilen öznitelik şemasını oluşturabilir ve yönetebilir. | 0f971eea-41eb-4569-a71e-57bb8a3eff1e |
> | [Dış kimlik sağlayıcısı Yöneticisi](#external-identity-provider-administrator) | , Kimlik sağlayıcılarını doğrudan federasyonda kullanılmak üzere yapılandırabilir. | be2f45a1-457d-42af-a067-6ec1fa63bc45 |
> | [Genel Yönetici](#global-administrator) | , Azure AD kimliklerini kullanan Azure AD ve Microsoft hizmetlerinin tüm yönlerini yönetebilir. | 62e90394-69f5-4237-9190-012177145e10 |
> | [Genel okuyucu](#global-reader) | Genel yöneticinin yapabileceği her şeyi okuyabilir, ancak güncelleştirmez. | f2ef992c-3afb-46b9-b7cf-a126ee74c451 |
> | [Grup Yöneticisi](#groups-administrator) | Bu rolün üyeleri grupları oluşturabilir/yönetebilir, adlandırma ve süre sonu ilkeleri gibi grup ayarları oluşturabilir/yönetebilir, Grup etkinliklerini ve Denetim raporlarını görüntüleyebilir. | fdd7a751-b60b-444a-984c-02652fe8fa1c |
> | [Konuk davetci](#guest-inviter) | ' Üyeler konukları davet edebilir ' ayarından bağımsız olarak Konuk kullanıcıları davet edebilir. | 95e79109-95c0-4d8e-aee3-d01accf2d47b |
> | [Yardım Masası Yöneticisi](#helpdesk-administrator) | , Yönetici olmayanlar ve Yardım Masası yöneticileri için parolaları sıfırlayabilir. | 729827e3-9c14-49f7-bb1b-9608f156bbb8 |
> | [Karma kimlik yöneticisi](#hybrid-identity-administrator) | AD 'yi Azure AD bulut sağlama, Azure AD Connect ve Federasyon ayarlarını yönetebilir. | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2 |
> | [Öngörüler Yöneticisi](#insights-administrator) | Microsoft 365 Insights uygulamasında yönetim erişimine sahiptir. | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c |
> | [Öngörüler Iş lideri](#insights-business-leader) | , M365 Insights uygulaması aracılığıyla panoları ve öngörüleri görüntüleyebilir ve paylaşabilir. | 31e939ad-9672-4796-9c2e-873181342d2d |
> | [Intune Yöneticisi](#intune-administrator) | , Intune ürününün tüm yönlerini yönetebilir. | 3a2c62db-5318-420d-8d74-23afee5d9d5 |
> | [Kaizala Yöneticisi](#kaizala-administrator) | , Microsoft Kaizala ayarlarını yönetebilir. | 74ef975b-6605-40af-a5d2-b9539d836353 |
> | [Bilgi Bankası Yöneticisi](#knowledge-administrator) | Bilgi, öğrenme ve diğer akıllı özellikleri yapılandırabilir. | b5a8dcf3-09d5-43a9-a639-8e29ef291470 |
> | [Lisans Yöneticisi](#license-administrator) | , Kullanıcılar ve gruplar üzerinde Ürün lisanslarını yönetebilir. | 4d6ac14f-3453-41d0-bef9-a3e0c569773a |
> | [İleti Merkezi Gizlilik okuyucusu](#message-center-privacy-reader) | Yalnızca Office 365 Ileti merkezindeki güvenlik iletilerini ve güncelleştirmelerini okuyabilir. | ac16e43d-7b2d-40e0-ac05-243ff356ab5b |
> | [İleti Merkezi okuyucusu](#message-center-reader) | , Yalnızca Office 365 Ileti merkezindeki kuruluşlarındaki iletileri ve güncelleştirmeleri okuyabilir. | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b |
> | [Modern ticari Kullanıcı](#modern-commerce-user) | , Bir şirket, departman veya takım için ticari satınalmaları yönetebilir. | d24aef57-1500-4070-84db-2666f29cf966 |
> | [Ağ Yöneticisi](#network-administrator) | , Hizmet uygulamaları olarak Microsoft 365 yazılım için ağ konumlarını yönetebilir ve kurumsal ağ tasarımı öngörülerini gözden geçirebilir. | d37c8bed-0711-4417-ba38-b4abe66ce4c2 |
> | [Office uygulamaları Yöneticisi](#office-apps-administrator) | , İlke ve ayarlar yönetimi dahil olmak üzere Office uygulamaları bulut hizmetlerini yönetebilir ve son kullanıcının cihazlarındaki ' yenilikler ' özellik içeriğini seçme, seçme ve yayımlayabilme özelliğini yönetme olanağı sağlar. | 2b745bdf-0803-4d80-aa65-822c4493dadac |
> | [Partner Katman1 desteği](#partner-tier1-support) | Kullanmayın-genel kullanıma yönelik değildir. | 4ba39ca4-527c-499a-B93D-d9b492c50246 |
> | [Partner Katman2 desteği](#partner-tier2-support) | Kullanmayın-genel kullanıma yönelik değildir. | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8 |
> | [Parola Yöneticisi](#password-administrator) | , Yönetici olmayan ve parola yöneticileri için parolaları sıfırlayabilir. | 966707d0-3269-4727-9be2-8c3a10f19b9d |
> | [Power BI Yöneticisi](#power-bi-administrator) | Power BI ürünün tüm yönlerini yönetebilir. | a9ea8996-122f-4c74-9520-8edcd192826c |
> | [Power Platform Yöneticisi](#power-platform-administrator) | Microsoft Dynamics 365, PowerApps ve Microsoft Flow 'ın tüm yönlerini oluşturabilir ve yönetebilir. | 11648597-926c-4CF3-9c36-bcebb0ba8dcc |
> | [Yazıcı Yöneticisi](#printer-administrator) | , Yazıcıların ve yazıcı bağlayıcılarının tüm yönlerini yönetebilir. | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f |
> | [Yazıcı teknisyeni](#printer-technician) | Yazıcı kaydedebilir ve kayıt kaydedebilir ve yazıcı durumunu güncelleştirebilir. | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477 |
> | [Ayrıcalıklı kimlik doğrulama Yöneticisi](#privileged-authentication-administrator) | Herhangi bir Kullanıcı (yönetici veya yönetici olmayan) için kimlik doğrulama yöntemi bilgilerini görüntüleme, ayarlama ve sıfırlama erişimi sağlayabilir. | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13 |
> | [Ayrıcalıklı rol yöneticisi](#privileged-role-administrator) | , Azure AD 'de rol atamalarını ve tüm Privileged Identity Management yönlerini yönetebilir. | e8611ab8-c189-46e8-94e1-60213ab1f814 |
> | [Rapor okuyucu](#reports-reader) | , Oturum açma ve Denetim raporlarını okuyabilir. | 4a5d8f65-41DA-4de4-8968-e035b65339cf |
> | [Yönetici ara](#search-administrator) | , Microsoft Search ayarlarının tüm yönlerini oluşturabilir ve yönetebilir. | 0964bb5e-9bdb-4d7b-ac29-58e794862a40 |
> | [Arama Düzenleyicisi](#search-editor) | Yer işaretleri, Q ve as, konumlar, FloorPlan gibi düzenleme içeriği oluşturabilir ve yönetebilir. | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9 |
> | [Güvenlik Yöneticisi](#security-administrator) | Azure AD ve Office 365 ' de güvenlik bilgilerini ve raporlarını okuyabilir ve yapılandırmayı yönetebilir. | 194ae4cb-B126-40b2-bd5b-6091b380977d |
> | [Güvenlik operatörü](#security-operator) | Güvenlik olaylarını oluşturur ve yönetir. | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f |
> | [Güvenlik Okuyucusu](#security-reader) | , Azure AD ve Office 365 ' deki güvenlik bilgilerini ve raporlarını okuyabilir. | 5d6b6bb7-de71-4623-B4AF-96380a352509 |
> | [Hizmet desteği Yöneticisi](#service-support-administrator) | , Hizmet durumu bilgilerini okuyabilir ve destek biletlerini yönetebilir. | f023fd81-a637-4b56-95fd-791ac0226033 |
> | [SharePoint Yöneticisi](#sharepoint-administrator) | , SharePoint hizmetinin tüm yönlerini yönetebilir. | f28a1f50-f6e7-4571-818b-6a12f2af6b6c |
> | [Skype Kurumsal Yöneticisi](#skype-for-business-administrator) | , Skype Kurumsal ürününün tüm yönlerini yönetebilir. | 75941009-915a-4869-ABE7-691bff18279e |
> | [Takımlar Yöneticisi](#teams-administrator) | , Microsoft ekipleri hizmetini yönetebilir. | 69091246-20e8-4a56-aa4d-066075b2a7a8 |
> | [Takımlar Iletişim Yöneticisi](#teams-communications-administrator) | , Microsoft ekipleri hizmeti içindeki çağrı ve toplantılar özelliklerini yönetebilir. | baf37b3a-610e-45da-9e62-d9d1e5e8914b |
> | [Takımlar Iletişimleri Destek Mühendisi](#teams-communications-support-engineer) | , Gelişmiş araçları kullanarak takımlar içindeki iletişim sorunlarını giderebilir. | f70938a0-fc10-4177-9e90-2178f8765737 |
> | [Takımlar Iletişimleri destek uzmanı](#teams-communications-support-specialist) | Temel araçları kullanarak takımlar içindeki iletişim sorunlarını giderebilirsiniz. | fcf91098-03e3-41a9-b5ba-6f0ec8188a12 |
> | [Takımlar cihazları Yöneticisi](#teams-devices-administrator) | Ekip sertifikalı cihazlarda yönetim ile ilgili görevler gerçekleştirebilir. | 3d762c5a-1B6C-493F-843e-55a3b42923d4 |
> | [Kullanım Özeti raporları okuyucusu](#usage-summary-reports-reader) | Microsoft 365 kullanım analizlerinin ve verimlilik puanındaki yalnızca kiracı düzeyindeki toplamaları görebilir. | 75934031-6c7e-415A-99d7-48dbd49e875e |
> | [Kullanıcı Yöneticisi](#user-administrator) | , Sınırlı yöneticiler için parola sıfırlama dahil olmak üzere kullanıcıların ve grupların tüm yönlerini yönetebilir. | fe930be7-5e62-47db-91af-98c3a49a38b1 |

## <a name="application-administrator"></a>Uygulama Yöneticisi

Bu roldeki kullanıcılar kurumsal uygulamaların, uygulama kayıtlarının ve uygulama proxy ayarlarının tüm yönlerini oluşturabilir ve yönetebilir. Bu role atanan kullanıcıların yeni uygulama kayıtları veya kurumsal uygulamalar oluştururken sahip olarak eklenmediğini unutmayın.

Bu rol Ayrıca, hem Microsoft Graph hem de Azure AD Graph için uygulama izinleri dışında, temsilci izinleri ve uygulama izinleri için izin verme olanağı verir.

> [!IMPORTANT]
> Bu özel durum, _diğer_ uygulamalar için uygulama izinlerini (örneğin, kaydettiğiniz Microsoft dışı uygulamalar veya uygulamalar) kabul edebilirsiniz. Bu izinleri uygulama kaydının bir parçası olarak _isteyebilirsiniz_ _, ancak bu izinleri vermek (_ diğer bir deyişle, ' ye Yarışması), genel yönetici gibi daha ayrıcalıklı bir yönetici gerektirir.
>
>Bu rol, uygulama kimlik bilgilerini yönetme olanağı verir. Bu role atanan kullanıcılar, bir uygulamaya kimlik bilgileri ekleyebilir ve uygulamanın kimliğini taklit etmek için bu kimlik bilgilerini kullanabilir. Uygulamanın kimliğine bir kaynağa erişim verildiyse (Kullanıcı veya diğer nesneleri oluşturma veya güncelleştirme yeteneği gibi), bu role atanan bir Kullanıcı, uygulamayı taklit ederken bu işlemleri gerçekleştirebilir. Uygulamanın kimliğini taklit etme özelliği, kullanıcının rol atamaları aracılığıyla ne yapabilecekleri konusunda ayrıcalık yükselmesine neden olabilir. Bir kullanıcıyı uygulama yöneticisi rolüne atamanın, uygulamanın kimliğini taklit etme olanağı sağladığını anlamak önemlidir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/uygulamalar/oluşturma | Tüm uygulama türlerini oluşturma |
> | Microsoft. Directory/uygulamalar/Sil | Tüm uygulama türlerini Sil |
> | Microsoft. Directory/uygulamalar/applicationProxy/okuma | Tüm uygulama proxy özelliklerini okuyun |
> | Microsoft. Directory/uygulamalar/applicationProxy/güncelleştirme | Tüm uygulama proxy özelliklerini güncelleştir |
> | Microsoft. Directory/Applications/applicationProxyAuthentication/Update | Tüm uygulama türlerinde kimlik doğrulamasını Güncelleştir |
> | Microsoft. Directory/uygulamalar/applicationProxySslCertificate/Update | Uygulama proxy 'si için SSL sertifikası ayarlarını güncelleştirme |
> | Microsoft. Directory/Applications/applicationProxyUrlSettings/Update | Uygulama proxy 'SI için URL ayarlarını Güncelleştir |
> | Microsoft. Directory/uygulamalar/appRoles/Update | Tüm uygulama türlerinde appRoles özelliğini güncelleştirme |
> | Microsoft. Directory/uygulamalar/hedef kitle/güncelleştirme | Uygulamalar için hedef kitle özelliğini güncelleştirme |
> | Microsoft. Directory/uygulamalar/kimlik doğrulaması/güncelleştirme | Tüm uygulama türlerinde kimlik doğrulamasını Güncelleştir |
> | Microsoft. Directory/uygulamalar/temel/güncelleştirme | Uygulamalar için temel özellikleri güncelleştirme |
> | Microsoft. Directory/uygulamalar/kimlik bilgileri/güncelleştirme | Uygulama kimlik bilgilerini güncelleştir |
> | Microsoft. Directory/uygulamalar/sahipler/güncelleştirme | Uygulamaların sahiplerini güncelleştirme |
> | Microsoft. Directory/uygulamalar/izinler/güncelleştirme | Tüm uygulama türlerinde sunulan izinleri ve gerekli izinleri güncelleştirin |
> | Microsoft. Directory/uygulamalar/ilkeler/güncelleştirme | Uygulama ilkelerini güncelleştirme |
> | Microsoft. Directory/uygulamalar/doğrulama/güncelleştirme | Applicationsdoğrulaması özelliğini Güncelleştir |
> | Microsoft. Directory/uygulamalar/eşitleme/standart/okuma | Uygulama nesnesiyle ilişkili sağlama ayarlarını okuyun |
> | Microsoft. Directory/applicationTemplates/örnek oluştur | Uygulama şablonlarından Galeri uygulamaları oluştur |
> | Microsoft. Directory/auditLogs/allProperties/Read | Ayrıcalıklı özellikler dahil olmak üzere denetim günlüklerindeki tüm özellikleri okuyun |
> | Microsoft. Directory/bağlayıcılar/oluşturma | Uygulama proxy bağlayıcıları oluşturma |
> | Microsoft. Directory/bağlayıcılar/allProperties/Read | Uygulama proxy 'si bağlayıcılarının tüm özelliklerini okuyun |
> | Microsoft. Directory/connectorGroups/Create | Uygulama proxy Bağlayıcısı grupları oluşturma |
> | Microsoft. Directory/connectorGroups/Delete | Uygulama proxy Bağlayıcısı gruplarını sil |
> | Microsoft. Directory/connectorGroups/allProperties/Read | Uygulama proxy Bağlayıcısı gruplarının tüm özelliklerini okuyun |
> | Microsoft. Directory/connectorGroups/allProperties/Update | Uygulama proxy Bağlayıcısı gruplarının tüm özelliklerini güncelleştir |
> | Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2,0 izin verir ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/applicationPolicies/Create | Uygulama ilkeleri oluşturma |
> | Microsoft. Directory/applicationPolicies/Delete | Uygulama ilkelerini silme |
> | Microsoft. Directory/applicationPolicies/Standard/Read | Uygulama ilkelerinin standart özelliklerini okuyun |
> | Microsoft. Directory/applicationPolicies/Owners/Read | Uygulama ilkelerinde sahipleri okuma |
> | Microsoft. Directory/applicationPolicies/policyAppliedTo/Read | Nesneler listesine uygulanan uygulama ilkelerini oku |
> | Microsoft. Directory/applicationPolicies/Basic/Update | Uygulama ilkelerinin standart özelliklerini güncelleştir |
> | Microsoft. Directory/applicationPolicies/Owners/Update | Uygulama ilkelerinin Owner özelliğini güncelleştirme |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Sağlama günlüklerinin tüm özelliklerini okuyun |
> | Microsoft. Directory/Servicesorumlularını/oluşturma | Hizmet sorumlusu oluşturma |
> | Microsoft. Directory/Servicesorumlularını/silme | Hizmet sorumlularını silme |
> | Microsoft. Directory/Servicesorumlularını/Disable | Hizmet sorumlularını devre dışı bırak |
> | Microsoft. Directory/Servicesorumlularını/etkinleştir | Hizmet sorumlularını etkinleştirme |
> | Microsoft. Directory/Servicesorumlularını/getPasswordSingleSignOnCredentials | Hizmet sorumluları 'nda parola çoklu oturum açma kimlik bilgilerini yönetme |
> | Microsoft. Directory/Servicesorumlularını/synchronizationCredentials/Manage | Uygulama sağlama gizli dizilerini ve kimlik bilgilerini yönetme |
> | Microsoft. Directory/Servicesorumlularını/synchronizationJobs/Manage | Uygulama sağlama eşitleme işlerini başlatın, yeniden başlatın ve duraklatın |
> | Microsoft. Directory/Servicesorumlularını/synchronizationSchema/Yönet | Uygulama sağlama eşitleme işleri ve şeması oluşturma ve yönetme |
> | Microsoft. Directory/Servicesorumlularını/managePasswordSingleSignOnCredentials | Hizmet sorumluları 'nda parola çoklu oturum açma kimlik bilgilerini oku |
> | Microsoft. Directory/Servicesorumlularını/managePermissionGrantsForAll. Microsoft-Application-admin | Microsoft Graph ve Azure AD Graph için uygulama izinleri dışında, uygulama izinleri ve temsilci izinleri için herhangi bir kullanıcı veya tüm kullanıcılar adına izin verin |
> | Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Update | Hizmet sorumlusu rol atamalarını güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/hedef kitle/güncelleştirme | Hizmet sorumluları üzerinde hedef kitle özelliklerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/kimlik doğrulaması/güncelleştirme | Hizmet sorumluları 'nda kimlik doğrulama özelliklerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/temel/güncelleştirme | Hizmet sorumluları 'ndaki temel özellikleri güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/kimlik bilgileri/güncelleştirme | Hizmet sorumluları 'nın kimlik bilgilerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/Owners/Update | Hizmet sorumluları 'nın sahiplerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/izinleri/güncelleştirme | Hizmet sorumluları 'nın izinlerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/ilkeleri/güncelleştirme | Hizmet sorumluları ilkelerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/etiketi/güncelleştirme | Hizmet sorumluları için Tag özelliğini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/eşitleme/standart/okuma | Hizmet sorumluyla ilişkili sağlama ayarlarını okuyun |
> | Microsoft. Directory/Signınreports/allProperties/Read | Ayrıcalıklı özellikler dahil olmak üzere oturum açma raporlarında tüm özellikleri okuyun |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="application-developer"></a>Uygulama Geliştirici

Bu roldeki kullanıcılar, "kullanıcılar uygulamaları kaydedebilirler" ayarı Hayır olarak ayarlanırsa uygulama kayıtları oluşturabilir. Bu rol Ayrıca, "kullanıcılar kendi adına şirket verilerine erişim izni verebilir" ayarında Hayır olarak ayarlanırsa, tek başına kendi adına onay izni verir. Bu role atanan kullanıcılar, yeni uygulama kayıtları veya kurumsal uygulamalar oluştururken sahip olarak eklenir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/Applications/createAsOwner | Tüm uygulama türlerini oluşturma ve Oluşturucu ilk sahip olarak eklendi |
> | Microsoft. Directory/Approtaatamalar/createAsOwner | İlk sahip olarak Oluşturucu ile uygulama rolü atamaları oluşturma |
> | Microsoft. Directory/oAuth2PermissionGrants/createAsOwner | İlk sahip olarak Oluşturucu ile OAuth 2,0 izin onayları oluşturun |
> | Microsoft. Directory/Servicesorumlularını/createAsOwner | İlk sahip olarak Oluşturucu ile hizmet sorumluları oluşturma |

## <a name="attack-payload-author"></a>Saldırı yükü yazarı

Bu roldeki kullanıcılar, saldırı yükleri oluşturabilir ancak bunları gerçekten başlatamaz veya zamanlayamaz. Böylece saldırı yükleri, kiracının benzetim oluşturmak için kullanabilecekleri tüm yöneticiler tarafından kullanılabilir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. office365. protectionCenter/saldırıda Ksimülatör/yük/allProperties/allTasks | Saldırı simülatöründe saldırı yüklerini oluşturma ve yönetme |
> | Microsoft. office365. protectionCenter/saldırıda Ksimülatör/raporlar/allProperties/Read | Saldırı simülasyonu yanıtlarının ve ilişkili eğitimin raporlarını okuyun |

## <a name="attack-simulation-administrator"></a>Saldırı simülasyonu Yöneticisi

Bu roldeki kullanıcılar, saldırı simülasyonu oluşturma, simülasyon başlatma/zamanlama ve simülasyon sonuçlarının incelenmesi gibi tüm özellikleri oluşturabilir ve yönetebilir. Bu rolün üyeleri Kiracıdaki tüm benzetimler için bu erişime sahiptir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. office365. protectionCenter/saldırıda Ksimülatör/yük/allProperties/allTasks | Saldırı simülatöründe saldırı yüklerini oluşturma ve yönetme |
> | Microsoft. office365. protectionCenter/saldırıda Ksimülatör/raporlar/allProperties/Read | Saldırı simülasyonu yanıtlarının ve ilişkili eğitimin raporlarını okuyun |
> | Microsoft. office365. protectionCenter/saldırıda Ksimülatör/simülasyon/allProperties/allTasks | Saldırı simülasyonu 'nda saldırı simülasyonu şablonları oluşturma ve yönetme |

## <a name="authentication-administrator"></a>Kimlik doğrulama Yöneticisi

Bu role sahip kullanıcılar, yönetici olmayanlar ve bazı roller için herhangi bir kimlik doğrulama yöntemi (parolalar dahil) ayarlayabilir veya sıfırlayabilir. Kimlik doğrulama yöneticileri, var olan parola olmayan kimlik bilgilerine (örneğin, MFA veya FIDO) göre yeniden kaydolmak için yönetici olmayan veya bazı rollere atanmayan kullanıcıların, bir sonraki oturum açma üzerinde MFA 'yı isteyen **CIHAZDA MFA 'yı hatırlayabilmesini** gerektirebilir. Bir kimlik doğrulama yöneticisinin kimlik doğrulama yöntemlerini okuyagörebileceği veya güncelleştirebir rolün listesi için bkz. [parola sıfırlama izinleri](#password-reset-permissions).

[Ayrıcalıklı kimlik doğrulama Yöneticisi](#privileged-authentication-administrator) rolünün tüm kullanıcılar için yeniden kaydolmayı ve Multi-Factor Authentication 'ı zorlama izni vardır.

[Kimlik doğrulama İlkesi yönetici](#authentication-policy-administrator) rolü, her kullanıcının kaydedebileceği ve kullanabileceği yöntemleri belirleyen kiracının kimlik doğrulama yöntemi ilkesini ayarlama iznine sahiptir.

| Rol | Kullanıcının kimlik doğrulama yöntemlerini yönetme | Kullanıcı başına MFA yönetme | MFA ayarlarını yönetme | Kimlik doğrulama yöntemi ilkesini Yönet | Parola koruma ilkesini Yönet |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Kimlik doğrulama Yöneticisi | Bazı kullanıcılar için Evet (yukarıya bakın) | Bazı kullanıcılar için Evet (yukarıya bakın) | Hayır | Hayır | Hayır |
| Ayrıcalıklı kimlik doğrulama Yöneticisi| Tüm kullanıcılar için Evet | Tüm kullanıcılar için Evet | Hayır | Hayır | Hayır |
| Kimlik doğrulama İlkesi Yöneticisi | Hayır |Hayır | Yes | Yes | Yes |

> [!IMPORTANT]
> Bu role sahip kullanıcılar, Azure Active Directory ' nin içindeki ve dışındaki gizli veya özel bilgilere veya kritik yapılandırmaya erişimi olabilecek kişiler için kimlik bilgilerini değiştirebilir. Bir kullanıcının kimlik bilgilerini değiştirmek, kullanıcının kimliğini ve izinlerini kabul etme imkanını ifade edebilir. Örnek:
>
>* Sahip oldukları uygulamaların kimlik bilgilerini yönetebilen uygulama kaydı ve kurumsal uygulama sahipleri. Bu uygulamaların Azure AD 'de ayrıcalıklı izinleri olabilir ve kimlik doğrulama yöneticilerine başka bir yerde izin verilmez. Bu yol aracılığıyla bir kimlik doğrulama Yöneticisi bir uygulama sahibinin kimliğini varsayabilir ve sonra uygulamanın kimlik bilgilerini güncelleştirerek ayrıcalıklı bir uygulamanın kimliğini daha fazla varsayabilir.
>* Azure abonelik sahipleri, hassas veya özel bilgilere veya Azure 'da kritik yapılandırmaya erişebilir.
>* Grup üyeliğini yönetebilen güvenlik grubu ve Grup sahipleri Microsoft 365. Bu gruplar, Azure AD 'de ve başka bir yerde hassas veya özel bilgilere veya kritik yapılandırmaya erişim verebilir.
>* Exchange Online, Office Security ve Uyumluluk Merkezi ve insan kaynakları sistemleri gibi Azure AD dışında diğer hizmetlerde bulunan yöneticiler.
>* Gizli veya özel bilgilere erişebilen Yöneticiler, yasal Counsel ve insan kaynakları çalışanları gibi yönetici olmayanlar.

> [!IMPORTANT]
> Bu rol, eski MFA yönetim portalındaki veya donanım OATH belirteçlerinde MFA ayarlarını yönetemez. Aynı işlevler [set-MsolUser](/powershell/module/msonline/set-msoluser) komutunu Azure AD PowerShell modülü kullanılarak gerçekleştirilebilir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/Users/ınvalidateallrefreshtokens | Kullanıcı yenileme belirteçleri geçersiz kılarak oturum açmaya zorla |
> | Microsoft. Directory/Users/strongAuthentication/Update | Kullanıcılar için güçlü kimlik doğrulama özelliğini güncelleştirme |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="authentication-policy-administrator"></a>Kimlik doğrulama Ilkesi Yöneticisi

Bu role sahip kullanıcılar, kimlik doğrulama yöntemleri ilkesini, kiracı genelinde MFA ayarlarını ve parola koruma ilkesini yapılandırabilir. Bu rol, parola koruma ayarlarını yönetme izni verir: akıllı kilitleme yapılandırması ve özel yasaklanmış parolalar listesi güncelleştiriliyor.

[Kimlik doğrulama Yöneticisi](#authentication-administrator) ve [ayrıcalıklı kimlik doğrulama Yöneticisi](#privileged-authentication-administrator) rollerinin kullanıcılara kayıtlı kimlik doğrulama yöntemlerini yönetme izni vardır ve tüm kullanıcılar için yeniden kayıt ve Multi-Factor Authentication 'ı zorunlu hale getirebilirsiniz.

| Rol | Kullanıcının kimlik doğrulama yöntemlerini yönetme | Kullanıcı başına MFA yönetme | MFA ayarlarını yönetme | Kimlik doğrulama yöntemi ilkesini Yönet | Parola koruma ilkesini Yönet |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Kimlik doğrulama Yöneticisi | Bazı kullanıcılar için Evet (yukarıya bakın) | Bazı kullanıcılar için Evet (yukarıya bakın) | Hayır | Hayır | Hayır |
| Ayrıcalıklı kimlik doğrulama Yöneticisi| Tüm kullanıcılar için Evet | Tüm kullanıcılar için Evet | Hayır | Hayır | Hayır |
| Kimlik doğrulama İlkesi Yöneticisi | Hayır | Hayır | Yes | Yes | Yes |

> [!IMPORTANT]
> Bu rol, eski MFA yönetim portalındaki veya donanım OATH belirteçlerinde MFA ayarlarını yönetemez.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/kuruluş/strongAuthentication/Update | Kuruluşun güçlü kimlik doğrulama özelliklerini güncelleştirme |
> | Microsoft. Directory/userCredentialPolicies/oluşturma | Kullanıcılar için kimlik bilgisi ilkeleri oluşturma |
> | Microsoft. Directory/userCredentialPolicies/Delete | Kullanıcılar için kimlik bilgisi ilkelerini silme |
> | Microsoft. Directory/userCredentialPolicies/standart/okuma | Kullanıcılar için kimlik bilgisi ilkelerinin standart özelliklerini okuyun |
> | Microsoft. Directory/userCredentialPolicies/sahipler/okuma | Kullanıcılar için kimlik bilgisi ilkelerinin sahiplerini okuma |
> | Microsoft. Directory/userCredentialPolicies/policyAppliedTo/Read | Policy. appliesTo gezinti bağlantısını oku |
> | Microsoft. Directory/userCredentialPolicies/temel/güncelleştirme | Kullanıcılar için temel ilkeleri güncelleştirme |
> | Microsoft. Directory/userCredentialPolicies/Owners/Update | Kullanıcılar için kimlik bilgisi ilkelerinin sahiplerini güncelleştirme |
> | Microsoft. Directory/userCredentialPolicies/tenantDefault/Update | Update Policy. ıorganizationdefault özelliği |

## <a name="azure-ad-joined-device-local-administrator"></a>Azure AD 'ye katılmış cihaz yerel Yöneticisi

Bu rol, yalnızca [cihaz ayarları](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)'nda ek bir yerel yönetici olarak atanmak üzere kullanılabilir. Bu role sahip kullanıcılar, Azure Active Directory katılmış tüm Windows 10 cihazlarında yerel makine yöneticileri haline gelir. Azure Active Directory cihaz nesnelerini yönetme yeteneğine sahip değildir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/groupSettings/Standard/Read | Grup ayarlarındaki temel özellikleri oku |
> | Microsoft. Directory/groupSettingTemplates/standart/Read | Grup ayarı şablonlarındaki temel özellikleri oku |

## <a name="azure-devops-administrator"></a>Azure DevOps Yöneticisi

Bu role sahip olan kullanıcılar, yeni Azure DevOps kuruluş oluşturmayı yapılandırılabilir bir kullanıcı veya grup kümesine kısıtlamak için Azure DevOps ilkesini yönetebilir. Bu roldeki kullanıcılar, bu ilkeyi şirketin Azure AD kuruluşu tarafından desteklenen herhangi bir Azure DevOps organizasyonu aracılığıyla yönetebilir. Bu rol, şirketin Azure AD organizasyonu tarafından desteklenen herhangi bir Azure DevOps kuruluşunun içindeki başka bir Azure DevOps 'a özgü izin (örneğin, proje koleksiyonu yöneticileri) vermez.

Tüm kurumsal Azure DevOps ilkeleri, bu roldeki kullanıcılar tarafından yönetilebilir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Azure. devOps/allEntities/allTasks | Azure DevOps 'u okuyun ve yapılandırın |

## <a name="azure-information-protection-administrator"></a>Azure Information Protection Yöneticisi

Bu role sahip olan kullanıcılar Azure Information Protection hizmetinde tüm izinlere sahiptir. Bu rol, Azure Information Protection ilkesi için etiketlerin yapılandırılmasını, koruma şablonlarının yönetilmesini ve korumanın etkinleşmesini sağlar. Bu rol, kimlik koruma Merkezi, Privileged Identity Management, Izleme Microsoft 365 hizmet durumu veya Office 365 güvenlik & Uyumluluk Merkezi 'nde herhangi bir izin vermez.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Azure. ınformationprotection/allEntities/allTasks | Azure Information Protection tüm yönlerini yönetin |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="b2c-ief-keyset-administrator"></a>B2C ıEF anahtar kümesi Yöneticisi

Kullanıcı belirteç şifreleme, belirteç imzaları ve talep şifreleme/şifre çözme için ilke anahtarlarını ve gizli dizileri oluşturabilir ve yönetebilir. Mevcut anahtar kapsayıcılarına yeni anahtarlar ekleyerek, bu sınırlı yönetici, var olan uygulamaları etkilemeden gizli dizileri gereken şekilde alabilir. Bu Kullanıcı, oluşturulduktan sonra bile bu parolaların tam içeriğini ve bunların sona erme tarihlerini görebilir.

> [!IMPORTANT]
> Bu hassas bir roldür. Anahtar kümesi yönetici rolü, üretim öncesi ve üretim sırasında dikkatli bir şekilde denetlenmeli ve bunlarla atanmalıdır.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/b2cTrustFrameworkKeySet/allProperties/allTasks | Yetkilendirme ilkelerinin tüm özelliklerini okuyun ve güncelleştirin |

## <a name="b2c-ief-policy-administrator"></a>B2C ıEF Ilke Yöneticisi

Bu roldeki kullanıcılar, Azure AD B2C tüm özel ilkeleri oluşturma, okuma, güncelleştirme ve silme yeteneğine sahiptir ve bu nedenle ilgili Azure AD B2C kuruluştaki kimlik deneyimi çerçevesinde tam denetime sahiptir. İlkeleri düzenleyerek, bu kullanıcı dış kimlik sağlayıcılarıyla doğrudan Federasyon oluşturabilir, Dizin şemasını değiştirebilir, kullanıcıya yönelik tüm içeriği (HTML, CSS, JavaScript) değiştirebilir, bir kimlik doğrulamasını tamamlamaya yönelik gereksinimleri değiştirebilir, yeni kullanıcılar oluşturabilir, tam geçişler dahil olmak üzere dış sistemlere Kullanıcı verileri gönderebilir ve parolalar ve telefon numaraları gibi hassas alanlar da dahil olmak üzere tüm Kullanıcı bilgilerini düzenleyebilir. Buna karşılık, bu rol, şifreleme anahtarlarını değiştiremez veya kuruluşta Federasyon için kullanılan gizli dizileri düzenleyemez.

> [!IMPORTANT]
> B2 ıEF Ilke Yöneticisi, üretimde kuruluşlar için çok sınırlı bir temelde atanması gereken son derece duyarlı bir roldür. Bu kullanıcıların etkinlikleri, özellikle üretimde kuruluşlar için yakından denetlenmelidir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/b2cTrustFrameworkPolicy/allProperties/allTasks | Azure Active Directory B2C 'de anahtar kümelerini okuyun ve yapılandırın |

## <a name="billing-administrator"></a>Faturalama Yöneticisi

Satın alma işlemleri yapar, abonelikleri yönetir, destek biletlerini yönetir ve hizmetin sistem durumunu izler.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/kuruluş/temel/güncelleştirme | Kuruluştaki temel özellikleri Güncelleştir |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. Commerce. faturalandırma/allEntities/allTasks | Office 365 faturalandırma 'nin tüm yönlerini yönetme |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="cloud-application-administrator"></a>Bulut Uygulaması Yöneticisi

Bu roldeki kullanıcılar uygulama proxy 'si rolüyle aynı izinlere sahiptir ve uygulama ara sunucusunu yönetme imkanını dışlar. Bu rol, kurumsal uygulamaların ve uygulama kayıtlarının tüm yönlerini oluşturma ve yönetme olanağı verir. Bu role atanan kullanıcılar, yeni uygulama kayıtları veya kurumsal uygulamalar oluştururken sahip olarak eklenmez.

Bu rol Ayrıca, hem Microsoft Graph hem de Azure AD Graph için uygulama izinleri dışında, temsilci izinleri ve uygulama izinleri için izin verme olanağı verir.

> [!IMPORTANT]
> Bu özel durum, _diğer_ uygulamalar için uygulama izinlerini (örneğin, kaydettiğiniz Microsoft dışı uygulamalar veya uygulamalar) kabul edebilirsiniz. Bu izinleri uygulama kaydının bir parçası olarak _isteyebilirsiniz_ _, ancak bu izinleri vermek (_ diğer bir deyişle, ' ye Yarışması), genel yönetici gibi daha ayrıcalıklı bir yönetici gerektirir.
>
>Bu rol, uygulama kimlik bilgilerini yönetme olanağı verir. Bu role atanan kullanıcılar, bir uygulamaya kimlik bilgileri ekleyebilir ve uygulamanın kimliğini taklit etmek için bu kimlik bilgilerini kullanabilir. Uygulamanın kimliğine bir kaynağa erişim verildiyse (Kullanıcı veya diğer nesneleri oluşturma veya güncelleştirme yeteneği gibi), bu role atanan bir Kullanıcı, uygulamayı taklit ederken bu işlemleri gerçekleştirebilir. Uygulamanın kimliğini taklit etme özelliği, kullanıcının rol atamaları aracılığıyla ne yapabilecekleri konusunda ayrıcalık yükselmesine neden olabilir. Bir kullanıcıyı uygulama yöneticisi rolüne atamanın, uygulamanın kimliğini taklit etme olanağı sağladığını anlamak önemlidir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/uygulamalar/oluşturma | Tüm uygulama türlerini oluşturma |
> | Microsoft. Directory/uygulamalar/Sil | Tüm uygulama türlerini Sil |
> | Microsoft. Directory/uygulamalar/appRoles/Update | Tüm uygulama türlerinde appRoles özelliğini güncelleştirme |
> | Microsoft. Directory/uygulamalar/hedef kitle/güncelleştirme | Uygulamalar için hedef kitle özelliğini güncelleştirme |
> | Microsoft. Directory/uygulamalar/kimlik doğrulaması/güncelleştirme | Tüm uygulama türlerinde kimlik doğrulamasını Güncelleştir |
> | Microsoft. Directory/uygulamalar/temel/güncelleştirme | Uygulamalar için temel özellikleri güncelleştirme |
> | Microsoft. Directory/uygulamalar/kimlik bilgileri/güncelleştirme | Uygulama kimlik bilgilerini güncelleştir |
> | Microsoft. Directory/uygulamalar/sahipler/güncelleştirme | Uygulamaların sahiplerini güncelleştirme |
> | Microsoft. Directory/uygulamalar/izinler/güncelleştirme | Tüm uygulama türlerinde sunulan izinleri ve gerekli izinleri güncelleştirin |
> | Microsoft. Directory/uygulamalar/ilkeler/güncelleştirme | Uygulama ilkelerini güncelleştirme |
> | Microsoft. Directory/uygulamalar/doğrulama/güncelleştirme | Applicationsdoğrulaması özelliğini Güncelleştir |
> | Microsoft. Directory/uygulamalar/eşitleme/standart/okuma | Uygulama nesnesiyle ilişkili sağlama ayarlarını okuyun |
> | Microsoft. Directory/applicationTemplates/örnek oluştur | Uygulama şablonlarından Galeri uygulamaları oluştur |
> | Microsoft. Directory/auditLogs/allProperties/Read | Ayrıcalıklı özellikler dahil olmak üzere denetim günlüklerindeki tüm özellikleri okuyun |
> | Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2,0 izin verir ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/applicationPolicies/Create | Uygulama ilkeleri oluşturma |
> | Microsoft. Directory/applicationPolicies/Delete | Uygulama ilkelerini silme |
> | Microsoft. Directory/applicationPolicies/Standard/Read | Uygulama ilkelerinin standart özelliklerini okuyun |
> | Microsoft. Directory/applicationPolicies/Owners/Read | Uygulama ilkelerinde sahipleri okuma |
> | Microsoft. Directory/applicationPolicies/policyAppliedTo/Read | Nesneler listesine uygulanan uygulama ilkelerini oku |
> | Microsoft. Directory/applicationPolicies/Basic/Update | Uygulama ilkelerinin standart özelliklerini güncelleştir |
> | Microsoft. Directory/applicationPolicies/Owners/Update | Uygulama ilkelerinin Owner özelliğini güncelleştirme |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Sağlama günlüklerinin tüm özelliklerini okuyun |
> | Microsoft. Directory/Servicesorumlularını/oluşturma | Hizmet sorumlusu oluşturma |
> | Microsoft. Directory/Servicesorumlularını/silme | Hizmet sorumlularını silme |
> | Microsoft. Directory/Servicesorumlularını/Disable | Hizmet sorumlularını devre dışı bırak |
> | Microsoft. Directory/Servicesorumlularını/etkinleştir | Hizmet sorumlularını etkinleştirme |
> | Microsoft. Directory/Servicesorumlularını/getPasswordSingleSignOnCredentials | Hizmet sorumluları 'nda parola çoklu oturum açma kimlik bilgilerini yönetme |
> | Microsoft. Directory/Servicesorumlularını/synchronizationCredentials/Manage | Uygulama sağlama gizli dizilerini ve kimlik bilgilerini yönetme |
> | Microsoft. Directory/Servicesorumlularını/synchronizationJobs/Manage | Uygulama sağlama eşitleme işlerini başlatın, yeniden başlatın ve duraklatın |
> | Microsoft. Directory/Servicesorumlularını/synchronizationSchema/Yönet | Uygulama sağlama eşitleme işleri ve şeması oluşturma ve yönetme |
> | Microsoft. Directory/Servicesorumlularını/managePasswordSingleSignOnCredentials | Hizmet sorumluları 'nda parola çoklu oturum açma kimlik bilgilerini oku |
> | Microsoft. Directory/Servicesorumlularını/managePermissionGrantsForAll. Microsoft-Application-admin | Microsoft Graph ve Azure AD Graph için uygulama izinleri dışında, uygulama izinleri ve temsilci izinleri için herhangi bir kullanıcı veya tüm kullanıcılar adına izin verin |
> | Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Update | Hizmet sorumlusu rol atamalarını güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/hedef kitle/güncelleştirme | Hizmet sorumluları üzerinde hedef kitle özelliklerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/kimlik doğrulaması/güncelleştirme | Hizmet sorumluları 'nda kimlik doğrulama özelliklerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/temel/güncelleştirme | Hizmet sorumluları 'ndaki temel özellikleri güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/kimlik bilgileri/güncelleştirme | Hizmet sorumluları 'nın kimlik bilgilerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/Owners/Update | Hizmet sorumluları 'nın sahiplerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/izinleri/güncelleştirme | Hizmet sorumluları 'nın izinlerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/ilkeleri/güncelleştirme | Hizmet sorumluları ilkelerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/etiketi/güncelleştirme | Hizmet sorumluları için Tag özelliğini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/eşitleme/standart/okuma | Hizmet sorumluyla ilişkili sağlama ayarlarını okuyun |
> | Microsoft. Directory/Signınreports/allProperties/Read | Ayrıcalıklı özellikler dahil olmak üzere oturum açma raporlarında tüm özellikleri okuyun |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="cloud-device-administrator"></a>Bulut Cihaz Yöneticisi

Bu roldeki kullanıcılar, Azure AD 'de cihazları etkinleştirebilir, devre dışı bırakabilir ve silebilir ve Azure portal Windows 10 BitLocker anahtarlarını (varsa) okuyabilir. Rol, cihazdaki diğer özellikleri yönetmek için izinler vermez.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/auditLogs/allProperties/Read | Ayrıcalıklı özellikler dahil olmak üzere denetim günlüklerindeki tüm özellikleri okuyun |
> | Microsoft. Directory/bitlockerKeys/Key/Read | Cihazlarda BitLocker meta verilerini ve anahtarını okuyun |
> | Microsoft. Directory/Devices/Delete | Cihazları Azure AD 'den silme |
> | Microsoft. Directory/cihazlar/devre dışı bırak | Azure AD 'de cihazları devre dışı bırakma |
> | Microsoft. Directory/cihazlar/etkinleştir | Azure AD 'de cihazları etkinleştirme |
> | Microsoft. Directory/Devices/extensionAttributes/Update | Cihazların tüm değerlerini güncelleştir. extensionAttributes özelliği |
> | Microsoft. Directory/deviceManagementPolicies/standart/okuma | Cihaz Yönetimi uygulama ilkelerindeki standart özellikleri okuyun |
> | Microsoft. Directory/deviceManagementPolicies/temel/güncelleştirme | Cihaz Yönetimi uygulama ilkelerindeki temel özellikleri güncelleştirme |
> | Microsoft. Directory/deviceRegistrationPolicy/Standard/Read | Cihaz kayıt ilkelerinde standart özellikleri okuyun |
> | Microsoft. Directory/deviceRegistrationPolicy/Basic/Update | Cihaz kayıt ilkelerinde temel özellikleri güncelleştirme |
> | Microsoft. Directory/Signınreports/allProperties/Read | Ayrıcalıklı özellikler dahil olmak üzere oturum açma raporlarında tüm özellikleri okuyun |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |

## <a name="compliance-administrator"></a>Uyumluluk Yöneticisi

Bu role sahip olan kullanıcılar Microsoft 365 Uyumluluk Merkezi 'nde uyumlulukla ilgili özellikleri yönetme izinlerine sahiptir Microsoft 365 Yönetim Merkezi, Azure ve Office 365 güvenlik & Uyumluluk Merkezi. Atanan, Exchange Yönetim Merkezi ve takımlar & Skype Kurumsal Yönetici merkezlerinin içindeki tüm özellikleri yönetebilir ve Azure ve Microsoft 365 için destek biletleri oluşturabilir. [Microsoft 365 Yönetici rolleri hakkında](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)daha fazla bilgi bulabilirsiniz.

İçinde | Yapılabilir
----- | ----------
[Uyumluluk Merkezi Microsoft 365](https://protection.office.com) | Kuruluşunuzun verilerini Microsoft 365 hizmetleri arasında koruyun ve yönetin<br>Uyumluluk uyarılarını yönetme
[Uyumluluk Yöneticisi](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Kuruluşunuzun yasal uyumluluk etkinliklerini izleyin, atayın ve doğrulayın
[Office 365 güvenlik & Uyumluluk Merkezi](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Veri idare yönetimi<br>Yasal ve veri araştırmasını gerçekleştirme<br>Veri konu Isteğini yönetme<br><br>Bu rol, Office 365 güvenlik & Uyumluluk Merkezi rol tabanlı erişim denetimindeki [Uyumluluk Yöneticisi RoleGroup](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) ile aynı izinlere sahiptir.
[Intune](/intune/role-based-access-control) | Tüm Intune denetim verilerini görüntüleyin
[Cloud App Security](/cloud-app-security/manage-admins) | Salt okuma izinlerine sahiptir ve uyarıları yönetebilir<br>Dosya ilkeleri oluşturabilir ve değiştirebilir ve dosya idare eylemlerine izin verebilir<br>Tüm yerleşik raporları Veri Yönetimi altına görüntüleyebilir

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. Directory/entitlementManagement/allProperties/Read | Azure AD Yetkilendirme Yönetimi 'ndeki tüm özellikleri okuyun |
> | Microsoft. office365. Karmaşıssuancemanager/allEntities/allTasks | Office 365 uyumluluk Yöneticisi 'nin tüm yönlerini yönetme |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="compliance-data-administrator"></a>Uyumluluk verileri Yöneticisi

Bu role sahip olan kullanıcılar, Microsoft 365 Uyumluluk Merkezi, Microsoft 365 Yönetim Merkezi ve Azure 'da verileri izleme iznine sahiptir. Kullanıcılar ayrıca Exchange Yönetim Merkezi, uyumluluk Yöneticisi ve takımlar & Skype Kurumsal Yönetim Merkezi 'nde uyumluluk verilerini izleyebilir ve Azure ve Microsoft 365 için destek biletleri oluşturabilir. [Bu belgede](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) , uyumluluk Yöneticisi ve uyumluluk veri Yöneticisi arasındaki farklara ilişkin ayrıntılar bulunur.

İçinde | Yapılabilir
----- | ----------
[Uyumluluk Merkezi Microsoft 365](https://protection.office.com) | Microsoft 365 hizmetleri genelinde uyumlulukla ilgili ilkeleri izleme<br>Uyumluluk uyarılarını yönetme
[Uyumluluk Yöneticisi](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Kuruluşunuzun yasal uyumluluk etkinliklerini izleyin, atayın ve doğrulayın
[Office 365 güvenlik & Uyumluluk Merkezi](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Veri idare yönetimi<br>Yasal ve veri araştırmasını gerçekleştirme<br>Veri konu Isteğini yönetme<br><br>Bu rol, Office 365 güvenlik & Uyumluluk Merkezi rol tabanlı erişim denetimindeki [Uyumluluk verileri Yöneticisi RoleGroup](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) ile aynı izinlere sahiptir.
[Intune](/intune/role-based-access-control) | Tüm Intune denetim verilerini görüntüleyin
[Cloud App Security](/cloud-app-security/manage-admins) | Salt okuma izinlerine sahiptir ve uyarıları yönetebilir<br>Dosya ilkeleri oluşturabilir ve değiştirebilir ve dosya idare eylemlerine izin verebilir<br>Tüm yerleşik raporları Veri Yönetimi altına görüntüleyebilir

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/cloudAppSecurity/allProperties/allTasks | Tüm kaynakları oluşturup silin ve Microsoft Cloud App Security içindeki standart özellikleri okuyun ve güncelleştirin |
> | Microsoft. Azure. ınformationprotection/allEntities/allTasks | Azure Information Protection tüm yönlerini yönetin |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. Karmaşıssuancemanager/allEntities/allTasks | Office 365 uyumluluk Yöneticisi 'nin tüm yönlerini yönetme |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="conditional-access-administrator"></a>Koşullu Erişim Yöneticisi

Bu role sahip olan kullanıcılar Azure Active Directory Koşullu erişim ayarlarını yönetebilir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/conditionalAccessPolicies/oluşturma | Koşullu erişim ilkeleri oluşturma |
> | Microsoft. Directory/conditionalAccessPolicies/Delete | Koşullu erişim ilkelerini silme |
> | Microsoft. Directory/conditionalAccessPolicies/standart/okuma | İlkeler için koşullu erişimi okuyun |
> | Microsoft. Directory/conditionalAccessPolicies/sahipler/okuma | Koşullu erişim ilkelerinin sahiplerini okuyun |
> | Microsoft. Directory/conditionalAccessPolicies/policyAppliedTo/Read | Koşullu erişim ilkeleri için "uygulandı" özelliğini okuyun |
> | Microsoft. Directory/conditionalAccessPolicies/Basic/Update | Koşullu erişim ilkeleri için temel özellikleri güncelleştirme |
> | Microsoft. Directory/conditionalAccessPolicies/Owners/Update | Koşullu erişim ilkeleri için sahipleri güncelleştirme |
> | Microsoft. Directory/conditionalAccessPolicies/tenantDefault/Update | Koşullu erişim ilkeleri için varsayılan kiracıyı güncelleştirme |
> | Microsoft. Directory/crossTenantAccessPolicies/Create | Çapraz kiracı erişim ilkeleri oluşturma |
> | Microsoft. Directory/crossTenantAccessPolicies/Delete | Çapraz kiracı erişim ilkelerini Sil |
> | Microsoft. Directory/crossTenantAccessPolicies/Standard/Read | Çapraz kiracı erişim ilkelerinin temel özelliklerini okuyun |
> | Microsoft. Directory/crossTenantAccessPolicies/Owners/Read | Çapraz kiracı erişim ilkelerinin sahiplerini okuma |
> | Microsoft. Directory/crossTenantAccessPolicies/policyAppliedTo/Read | Çapraz kiracı erişim ilkelerinin Politerpliedto özelliğini okuyun |
> | Microsoft. Directory/crossTenantAccessPolicies/Basic/Update | Çapraz kiracı erişim ilkelerinin temel özelliklerini güncelleştirme |
> | Microsoft. Directory/crossTenantAccessPolicies/Owners/Update | Çapraz kiracı erişim ilkelerinin sahiplerini güncelleştirme |
> | Microsoft. Directory/crossTenantAccessPolicies/tenantDefault/Update | Çapraz kiracı erişim ilkeleri için varsayılan kiracıyı güncelleştirme |

## <a name="customer-lockbox-access-approver"></a>Müşteri Kasası erişim onaylayıcısı

Kuruluşunuzdaki [müşteri kasası isteklerini](/office365/admin/manage/customer-lockbox-requests) yönetir. Müşteri Kasası istekler için e-posta bildirimleri alırlar ve Microsoft 365 Yönetim merkezinden gelen istekleri onaylayabilir ve reddedebilirler. Ayrıca Müşteri Kasası özelliğini açabilir veya kapatabilir. Yalnızca genel Yöneticiler, bu role atanan kişilerin parolalarını sıfırlayabilir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. office365. kasa/allEntities/allTasks | Müşteri Kasası tüm yönlerini yönetin |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="desktop-analytics-administrator"></a>Masaüstü Analizi Yöneticisi

Bu roldeki kullanıcılar masaüstü Analizi hizmetini yönetebilir. Bu, varlık envanterini görüntüleme, dağıtım planları oluşturma ve dağıtım ve sistem durumunu görüntüleme imkanını içerir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. desktopAnalytics/allEntities/allTasks | Masaüstü analizinin tüm yönlerini yönetme |

## <a name="directory-readers"></a>Dizin okuyucuları

Bu roldeki kullanıcılar, temel dizin bilgilerini okuyabilir. Bu rol için kullanılmalıdır:

* Belirli bir konuk kullanıcıları kümesine, tüm konuk kullanıcılara vermek yerine okuma erişimi verme.
* "Azure AD portalına erişimi yalnızca yöneticilerle kısıtla" olarak ayarlarsanız, yönetici olmayan kullanıcıların belirli bir kümesini Azure portal erişimine izin vermek "Evet" olarak ayarlanır.
* Dizin. Read. All 'un bir seçenek olmadığı dizine hizmet sorumlusu erişimi veriliyor.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/Yönetimtiveunits/standart/Read | Yönetim birimlerindeki temel özellikleri oku |
> | Microsoft. Directory/Yönetimtiveunits/Üyeler/Read | Yönetim birimlerinin üyelerini oku |
> | Microsoft. Directory/uygulamalar/standart/okuma | Uygulamaların standart özelliklerini okuyun |
> | Microsoft. Directory/uygulamalar/sahipler/okuma | Uygulamaların sahiplerini okuma |
> | Microsoft. Directory/uygulamalar/ilkeler/okuma | Uygulamaların ilkelerini okuyun |
> | Microsoft. Directory/Contacts/Standard/Read | Azure AD 'de kişilerin temel özelliklerini okuma |
> | Microsoft. Directory/kiþiler/memberOf/Read | Azure AD 'deki tüm kişiler için Grup üyeliğini okuyun |
> | Microsoft. Directory/sözleşmeleri/standart/okuma | İş ortağı sözleşmeleri temel özelliklerini okuyun |
> | Microsoft. Directory/Devices/Standard/Read | Cihazlarda temel özellikleri oku |
> | Microsoft. Directory/Devices/memberOf/Read | Cihaz üyeliklerini oku |
> | Microsoft. Directory/Devices/kayıt bilgileri/okuma | Cihazların kayıtlı sahiplerini okuma |
> | Microsoft. Directory/Devices/registeredUsers/Read | Cihazların kayıtlı kullanıcılarını okuyun |
> | Microsoft. Directory/directoryRoles/Standard/Read | Azure AD rollerindeki temel özellikleri güncelleştirme |
> | Microsoft. Directory/directoryRoles/eli, Lemembers/Read | Azure AD rollerinin uygun üyelerini okuyun |
> | Microsoft. Directory/directoryRoles/Members/Read | Tüm Azure AD rolleri üyelerini okuyun |
> | Microsoft. Directory/Domains/Standard/Read | Etki alanlarında temel özellikleri oku |
> | Microsoft. Directory/Groups/Standard/Read | Gruplardaki temel özellikleri oku |
> | Microsoft. Directory/Groups/Approtaatamalar/Read | Grupların uygulama rolü atamalarını oku |
> | Microsoft. Directory/gruplar/memberOf/Read | Azure AD 'de bir grubun üyesi olduğu grupları okuyun |
> | Microsoft. Directory/gruplar/Üyeler/okuma | Grupların üyelerini oku |
> | Microsoft. Directory/gruplar/Owners/Read | Grupların sahiplerini okuma |
> | Microsoft. Directory/Groups/Settings/Read | Grupların ayarlarını okuyun |
> | Microsoft. Directory/groupSettings/Standard/Read | Grup ayarlarındaki temel özellikleri oku |
> | Microsoft. Directory/groupSettingTemplates/standart/Read | Grup ayarı şablonlarındaki temel özellikleri oku |
> | Microsoft. Directory/oAuth2PermissionGrants/Standard/Read | OAuth 2,0 izin verdiği temel özellikleri okuyun |
> | Microsoft. Directory/kuruluş/standart/okuma | Bir kuruluştaki temel özellikleri okuyun |
> | Microsoft. Directory/Organization/trustedCAsForPasswordlessAuth/okuma | Passwordless kimlik doğrulaması için güvenilen sertifika yetkililerini okuyun |
> | Microsoft. Directory/applicationPolicies/Standard/Read | Uygulama ilkelerinin standart özelliklerini okuyun |
> | Microsoft. Directory/Roleatamalar/standart/okuma | Rol atamalarındaki temel özellikleri oku |
> | Microsoft. Directory/roleDefinitions/standart/okuma | Rol tanımlarında temel özellikleri oku |
> | Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Read | Hizmet sorumlusu rol atamalarını oku |
> | Microsoft. Directory/Servicesorumlularını/Approtaatamalar/okuma | Hizmet sorumlularına atanan rol atamalarını oku |
> | Microsoft. Directory/Servicesorumlularını/standart/okuma | Hizmet sorumluları 'nın temel özelliklerini okuyun |
> | Microsoft. Directory/Servicesorumlularını/memberOf/Read | Hizmet sorumluları 'ndaki grup üyeliklerini okuyun |
> | Microsoft. Directory/Servicesorumlularını/oAuth2PermissionGrants/Read | Hizmet sorumluları üzerinde temsilci izni izin verilen izinleri okuyun |
> | Microsoft. Directory/Servicesorumlularını/Owners/Read | Hizmet sorumluları 'nın sahiplerini okuma |
> | Microsoft. Directory/Servicesorumlularını/ownedObjects/Read | Hizmet sorumluları 'nın sahip olduğu nesneleri oku |
> | Microsoft. Directory/Servicesorumlularını/ilkeleri/okuma | Hizmet sorumluları 'nın ilkelerini okuyun |
> | Microsoft. Directory/subscribedSkus/Standard/Read | Aboneliklerdeki temel özellikleri okuyun |
> | Microsoft. Directory/kullanıcılar/standart/okuma | Kullanıcıların temel özelliklerini okuma |
> | Microsoft. Directory/Users/Approtaatamalar/okuma | Kullanıcıların uygulama rolü atamalarını okuyun |
> | Microsoft. Directory/Users/directReports/Read | Kullanıcılar için doğrudan raporları okuyun |
> | Microsoft. Directory/Users/Manager/okuma | Kullanıcıların yöneticisini okuyun |
> | Microsoft. Directory/kullanıcılar/memberOf/Read | Kullanıcıların grup üyeliklerini okuyun |
> | Microsoft. Directory/Users/oAuth2PermissionGrants/Read | Kullanıcılara atanan izin verdiği izinleri oku |
> | Microsoft. Directory/Users/ownedDevices/Read | Kullanıcıların sahip olduğu cihazları okuyun |
> | Microsoft. Directory/Users/ownedObjects/Read | Kullanıcıların sahip olduğu nesneleri okuma |
> | Microsoft. Directory/Users/registeredDevices/Read | Kullanıcıların kayıtlı cihazlarını okuyun |

## <a name="directory-synchronization-accounts"></a>Dizin eşitleme hesapları

Kullanmayın. Bu rol, Azure AD Connect hizmetine otomatik olarak atanır ve başka bir kullanım için tasarlanmamıştır veya desteklenmez.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/uygulamalar/oluşturma | Tüm uygulama türlerini oluşturma |
> | Microsoft. Directory/uygulamalar/Sil | Tüm uygulama türlerini Sil |
> | Microsoft. Directory/uygulamalar/appRoles/Update | Tüm uygulama türlerinde appRoles özelliğini güncelleştirme |
> | Microsoft. Directory/uygulamalar/hedef kitle/güncelleştirme | Uygulamalar için hedef kitle özelliğini güncelleştirme |
> | Microsoft. Directory/uygulamalar/kimlik doğrulaması/güncelleştirme | Tüm uygulama türlerinde kimlik doğrulamasını Güncelleştir |
> | Microsoft. Directory/uygulamalar/temel/güncelleştirme | Uygulamalar için temel özellikleri güncelleştirme |
> | Microsoft. Directory/uygulamalar/kimlik bilgileri/güncelleştirme | Uygulama kimlik bilgilerini güncelleştir |
> | Microsoft. Directory/uygulamalar/sahipler/güncelleştirme | Uygulamaların sahiplerini güncelleştirme |
> | Microsoft. Directory/uygulamalar/izinler/güncelleştirme | Tüm uygulama türlerinde sunulan izinleri ve gerekli izinleri güncelleştirin |
> | Microsoft. Directory/uygulamalar/ilkeler/güncelleştirme | Uygulama ilkelerini güncelleştirme |
> | Microsoft. Directory/kuruluş/dirSync/Update | Kuruluş dizini eşitleme özelliğini güncelleştirme |
> | Microsoft. Directory/policies/Create | Azure AD 'de ilkeler oluşturma |
> | Microsoft. Directory/policies/Delete | Azure AD 'de ilkeleri silme |
> | Microsoft. Directory/policies/standart/Read | İlkelerdeki temel özellikleri okuyun |
> | Microsoft. Directory/policies/Owners/Read | İlkelerin sahiplerini okuma |
> | Microsoft. Directory/policies/policyAppliedTo/Read | Policies. policyAppliedTo özelliğini okuyun |
> | Microsoft. Directory/policies/Basic/Update | İlkelerdeki temel özellikleri güncelleştirme |
> | Microsoft. Directory/policies/Owners/Update | İlkelerin sahiplerini güncelleştirme |
> | Microsoft. Directory/policies/tenantDefault/Update | Varsayılan kuruluş ilkelerini Güncelleştir |
> | Microsoft. Directory/Servicesorumlularını/oluşturma | Hizmet sorumlusu oluşturma |
> | Microsoft. Directory/Servicesorumlularını/silme | Hizmet sorumlularını silme |
> | Microsoft. Directory/Servicesorumlularını/etkinleştir | Hizmet sorumlularını etkinleştirme |
> | Microsoft. Directory/Servicesorumlularını/Disable | Hizmet sorumlularını devre dışı bırak |
> | Microsoft. Directory/Servicesorumlularını/getPasswordSingleSignOnCredentials | Hizmet sorumluları 'nda parola çoklu oturum açma kimlik bilgilerini yönetme |
> | Microsoft. Directory/Servicesorumlularını/managePasswordSingleSignOnCredentials | Hizmet sorumluları 'nda parola çoklu oturum açma kimlik bilgilerini oku |
> | Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Read | Hizmet sorumlusu rol atamalarını oku |
> | Microsoft. Directory/Servicesorumlularını/Approtaatamalar/okuma | Hizmet sorumlularına atanan rol atamalarını oku |
> | Microsoft. Directory/Servicesorumlularını/standart/okuma | Hizmet sorumluları 'nın temel özelliklerini okuyun |
> | Microsoft. Directory/Servicesorumlularını/memberOf/Read | Hizmet sorumluları 'ndaki grup üyeliklerini okuyun |
> | Microsoft. Directory/Servicesorumlularını/oAuth2PermissionGrants/Read | Hizmet sorumluları üzerinde temsilci izni izin verilen izinleri okuyun |
> | Microsoft. Directory/Servicesorumlularını/Owners/Read | Hizmet sorumluları 'nın sahiplerini okuma |
> | Microsoft. Directory/Servicesorumlularını/ownedObjects/Read | Hizmet sorumluları 'nın sahip olduğu nesneleri oku |
> | Microsoft. Directory/Servicesorumlularını/ilkeleri/okuma | Hizmet sorumluları 'nın ilkelerini okuyun |
> | Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Update | Hizmet sorumlusu rol atamalarını güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/hedef kitle/güncelleştirme | Hizmet sorumluları üzerinde hedef kitle özelliklerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/kimlik doğrulaması/güncelleştirme | Hizmet sorumluları 'nda kimlik doğrulama özelliklerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/temel/güncelleştirme | Hizmet sorumluları 'ndaki temel özellikleri güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/kimlik bilgileri/güncelleştirme | Hizmet sorumluları 'nın kimlik bilgilerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/Owners/Update | Hizmet sorumluları 'nın sahiplerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/izinleri/güncelleştirme | Hizmet sorumluları 'nın izinlerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/ilkeleri/güncelleştirme | Hizmet sorumluları ilkelerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/etiketi/güncelleştirme | Hizmet sorumluları için Tag özelliğini güncelleştirme |

## <a name="directory-writers"></a>Dizin yazarları

Bu roldeki kullanıcılar, kullanıcıların, grupların ve hizmet sorumlularının temel bilgilerini okuyabilir ve güncelleştirebilir. Bu rolü yalnızca [onay çerçevesini](../develop/quickstart-register-app.md)desteklemeyen uygulamalara atayın. Herhangi bir kullanıcıya atanmamalıdır.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/Groups/Atamalisansı | Grup tabanlı lisanslama için gruplara ürün lisansları atama |
> | Microsoft. Directory/gruplar/oluştur | Rol atanabilir gruplar hariç olmak üzere Grup oluşturma |
> | Microsoft. Directory/Groups/Reprocesslicenseatama | Grup tabanlı lisanslama için lisans atamalarını yeniden işleme |
> | Microsoft. Directory/Groups/Basic/Update | Rol atanabilir gruplar hariç olmak üzere gruplardaki temel özellikleri Güncelleştir |
> | Microsoft. Directory/gruplar/sınıflandırma/güncelleştirme | Rol atanabilir gruplar hariç olmak üzere grupların sınıflandırma özelliğini güncelleştirme |
> | Microsoft. Directory/Groups/dynamicMembershipRule/Update | Rol atanabilir gruplar hariç olmak üzere grupların dinamik üyelik kuralını Güncelleştir |
> | Microsoft. Directory/gruplar/groupType/Update | Grup için groupType özelliğini güncelleştirme |
> | Microsoft. Directory/gruplar/Üyeler/güncelleştirme | Rol atanabilir gruplar hariç olmak üzere grupların üyelerini Güncelleştir |
> | Microsoft. Directory/Groups/Onpremgeri yazma/güncelleştirme | Azure AD Connect ile şirket içine geri yazılacak Azure Active Directory grupları güncelleştirme |
> | Microsoft. Directory/gruplar/Owners/Update | Rol atanabilir gruplar hariç olmak üzere grupların sahiplerini Güncelleştir |
> | Microsoft. Directory/Groups/Settings/Update | Grupların ayarlarını Güncelleştir |
> | Microsoft. Directory/gruplar/görünürlük/güncelleştirme | Grupların görünürlük özelliğini güncelleştirme |
> | Microsoft. Directory/groupSettings/Create | Grup ayarları oluşturma |
> | Microsoft. Directory/groupSettings/Delete | Grup ayarlarını silme |
> | Microsoft. Directory/groupSettings/Basic/Update | Grup ayarlarındaki temel özellikleri Güncelleştir |
> | Microsoft. Directory/oAuth2PermissionGrants/Create | OAuth 2,0 izin onayları oluşturma |
> | Microsoft. Directory/oAuth2PermissionGrants/Basic/Update | OAuth 2,0 izin verir güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/synchronizationCredentials/Manage | Uygulama sağlama gizli dizilerini ve kimlik bilgilerini yönetme |
> | Microsoft. Directory/Servicesorumlularını/synchronizationJobs/Manage | Uygulama sağlama eşitleme işlerini başlatın, yeniden başlatın ve duraklatın |
> | Microsoft. Directory/Servicesorumlularını/synchronizationSchema/Yönet | Uygulama sağlama eşitleme işleri ve şeması oluşturma ve yönetme |
> | Microsoft. Directory/Servicesorumlularını/managePermissionGrantsForGroup. Microsoft-All-Application-Permissions | Hizmet sorumlusu bir grubun verilerine doğrudan erişim verme |
> | Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Update | Hizmet sorumlusu rol atamalarını güncelleştirme |
> | Microsoft. Directory/Users/Atamalisansı | Kullanıcı lisanslarını yönetme |
> | Microsoft. Directory/kullanıcılar/oluştur | Kullanıcı ekle |
> | Microsoft. Directory/kullanıcılar/devre dışı bırak | Kullanıcıları devre dışı bırak |
> | Microsoft. Directory/kullanıcılar/etkinleştir | Kullanıcıları etkinleştir |
> | Microsoft. Directory/Users/ınvalidateallrefreshtokens | Kullanıcı yenileme belirteçleri geçersiz kılarak oturum açmaya zorla |
> | Microsoft. Directory/Users/Reprocesslicenseatama | Kullanıcılar için lisans atamalarını yeniden işleme |
> | Microsoft. Directory/Users/Basic/Update | Kullanıcıların temel özelliklerini güncelleştirme |
> | Microsoft. Directory/Users/Manager/Update | Kullanıcılar için Güncelleştirme Yöneticisi |
> | Microsoft. Directory/Users/userPrincipalName/Update | Kullanıcıların Kullanıcı asıl adını güncelleştirme |

## <a name="domain-name-administrator"></a>Etki alanı adı yöneticisi

Bu role sahip kullanıcılar, etki alanı adlarını yönetebilir (okuyabilir, ekleyebilir, doğrulayamaz, güncelleştirebilir ve silebilir). Kullanıcılar, gruplar ve uygulamalarla ilgili dizin bilgilerini de okuyabilir, bu nesneler etki alanı bağımlılıklarına sahip olabilir. Şirket içi ortamlarda, bu role sahip olan kullanıcılar, ilgili kullanıcıların her zaman şirket içinde kimlik doğrulamasından geçirilmeleri için, Federasyon etki alanı adlarını yapılandırabilir. Bu kullanıcılar daha sonra Azure AD tabanlı hizmetlerde çoklu oturum açma yoluyla şirket içi parolalarla oturum açabilirler. Federasyon ayarlarının Azure AD Connect aracılığıyla eşitlenmesi gerekir, böylece kullanıcıların Azure AD Connect yönetme izinleri de vardır.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/Domains/allProperties/allTasks | Etki alanlarını oluşturun ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |

## <a name="dynamics-365-administrator"></a>Dynamics 365 Yöneticisi

Bu role sahip olan kullanıcılar, hizmet mevcut olduğunda Microsoft Dynamics 365 Online içinde genel izinlere sahiptir ve destek biletlerini yönetebilir ve hizmet durumunu izleyebilir. Daha fazla bilgi [için, Azure AD kuruluşunuzu yönetmek üzere hizmet yöneticisi rolünü kullanın](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> Microsoft Graph API ve Azure AD PowerShell 'de, bu rol "Dynamics 365 hizmet yöneticisi" olarak tanımlanır. [Azure Portal](https://portal.azure.com), "Dynamics 365 Yöneticisi" dir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. dynamics365/allEntities/allTasks | Dynamics 365 'in tüm yönlerini yönetme |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="exchange-administrator"></a>Exchange Yöneticisi

Bu role sahip olan kullanıcılar, hizmet mevcut olduğunda Microsoft Exchange Online içinde genel izinlere sahiptir. Ayrıca tüm Microsoft 365 gruplarını oluşturup yönetebilir, destek biletlerini yönetebilir ve hizmet durumunu izleyebilir. [Yönetici rolleri hakkında](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)daha fazla bilgi Microsoft 365.

> [!NOTE]
> Microsoft Graph API ve Azure AD PowerShell 'de, bu rol "Exchange hizmeti Yöneticisi" olarak tanımlanır. [Azure Portal](https://portal.azure.com), "Exchange Yöneticisi" dir. [Exchange Yönetim Merkezi](https://go.microsoft.com/fwlink/p/?LinkID=529144)'Nde "Exchange Online Yöneticisi" dir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/Groups/hiddenMembers/Read | Bir grubun gizli üyelerini oku |
> | Microsoft. Directory/groups. Unified/Create | Rol atanabilir grupların dışlamasıyla birlikte Microsoft 365 grupları oluşturun |
> | Microsoft. Directory/groups. Unified/Delete | Rol atanabilir grupların dışlamasıyla Microsoft 365 gruplarını sil |
> | Microsoft. Directory/groups. Unified/restore | Microsoft 365 gruplarını geri yükleme |
> | Microsoft. Directory/groups. Unified/Basic/Update | Rol atanabilir grupların dışlamasıyla Microsoft 365 gruplardaki temel özellikleri Güncelleştir |
> | Microsoft. Directory/groups. Unified/Members/Update | Microsoft 365 gruplarının üyelerini rol atanabilir grupların dışlamasıyla Güncelleştir |
> | Microsoft. Directory/groups. Unified/Owners/Update | Rol atanabilir grupların dışlamasıyla Microsoft 365 gruplarının sahiplerini güncelleştirme |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. Exchange/allEntities/temel/allTasks | Exchange Online 'ın tüm yönlerini yönetin |
> | Microsoft. office365. Network/Performance/allProperties/Read | Microsoft 365 Yönetim Merkezi 'ndeki tüm ağ performansı özelliklerini okuyun |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. usageReports/allEntities/allProperties/Read | Office 365 kullanım raporlarını okuyun |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="exchange-recipient-administrator"></a>Exchange alıcı Yöneticisi

Bu role sahip olan kullanıcılar, Exchange Online 'da bu alıcıların özniteliklerine alıcılar ve yazma erişimi erişimi sağlar. [Exchange alıcıları](/exchange/recipients/recipients)hakkında daha fazla bilgi.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. office365. Exchange/allRecipients/allProperties/allTasks | Tüm alıcıları oluşturup silin ve Exchange Online 'daki alıcıların tüm özelliklerini okuyun ve güncelleştirin |
> | Microsoft. office365. Exchange/messageTracking/allProperties/allTasks | Exchange Online 'da ileti izlemede tüm görevleri yönetme |
> | Microsoft. office365. Exchange/Migration/allProperties/allTasks | Exchange Online 'da alıcıların geçişle ilgili tüm görevleri yönetin |

## <a name="external-id-user-flow-administrator"></a>Dış KIMLIK Kullanıcı akış Yöneticisi

Bu role sahip kullanıcılar, Azure portal Kullanıcı akışları oluşturabilir ve yönetebilir ("yerleşik" ilkeleri olarak da bilinir). Bu kullanıcılar HTML/CSS/JavaScript içeriğini özelleştirebilir, MFA gereksinimlerini değiştirebilir, belirteçteki talepleri seçebilir, API bağlayıcılarını yönetebilir ve Azure AD kuruluşunda tüm Kullanıcı akışları için oturum ayarlarını yapılandırabilir. Diğer taraftan, bu rol, Kullanıcı verilerini gözden geçirme veya kuruluş şemasına dahil olan özniteliklerde değişiklik yapma özelliğini içermez. Kimlik deneyimi çerçevesi ilkelerine (özel ilkeler olarak da bilinir) yapılan değişiklikler Ayrıca bu rolün kapsamı dışındadır.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/b2cUserFlow/allProperties/allTasks | Azure Active Directory B2C Kullanıcı özniteliklerini okuyun ve yapılandırın |

## <a name="external-id-user-flow-attribute-administrator"></a>Dış KIMLIK Kullanıcı akışı öznitelik Yöneticisi

Bu role sahip kullanıcılar, Azure AD kuruluşundaki tüm Kullanıcı akışları için kullanılabilir özel öznitelikler ekler veya siler. Bu nedenle, bu role sahip olan kullanıcılar Son Kullanıcı şemasına değiştirebilir veya yeni öğe ekleyebilir, tüm Kullanıcı akışlarının davranışını etkileyebilir ve son kullanıcılar ve son olarak uygulamalara talepler olarak gönderilebilir. Bu rol Kullanıcı akışlarını düzenleyemez.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/b2cUserAttribute/allProperties/allTasks | Azure Active Directory B2C özel ilkeleri okuma ve yapılandırma |

## <a name="external-identity-provider-administrator"></a>Dış kimlik sağlayıcısı Yöneticisi

Bu yönetici Azure AD kuruluşları ve dış kimlik sağlayıcıları arasında Federasyonu yönetir. Bu rolle, kullanıcılar yeni kimlik sağlayıcıları ekleyebilir ve tüm kullanılabilir ayarları (örneğin, kimlik doğrulama yolu, hizmet KIMLIĞI, atanan anahtar kapsayıcıları) yapılandırabilir. Bu Kullanıcı Azure AD kuruluşunun, dış kimlik sağlayıcılarından kimlik doğrulamaları güvenmesini sağlayabilir. Son Kullanıcı deneyimlerinde ortaya çıkan etki, kuruluşun türüne bağlıdır:

* Çalışanlar ve iş ortakları için Azure AD kuruluşları: bir Federasyonun eklenmesi (örn. Gmail ile), henüz kullanılmadı tüm konuk davetlerini hemen etkiler. Bkz. [B2B Konuk kullanıcıları için kimlik sağlayıcısı olarak Google ekleme](../external-identities/google-federation.md).
* Azure Active Directory B2C kuruluşlar: bir Federasyonun eklenmesi (örneğin, Facebook ile veya başka bir Azure AD kuruluşu ile), kimlik sağlayıcısı Kullanıcı akışına bir seçenek olarak ekleninceye kadar Son Kullanıcı akışlarını hemen etkilemez (yerleşik ilke olarak da bilinir). Bir örnek için [kimlik sağlayıcısı olarak Microsoft hesabı yapılandırma](../../active-directory-b2c/identity-provider-microsoft-account.md) konusuna bakın. Kullanıcı akışlarını değiştirmek için, "B2C Kullanıcı akış Yöneticisi" nin sınırlı rolü gereklidir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/IdentityProviders/allProperties/allTasks | Azure Active Directory B2C kimlik sağlayıcılarını okuyun ve yapılandırın |

## <a name="global-administrator"></a>Genel Yönetici

Bu role sahip olan kullanıcılar, Azure Active Directory ' deki tüm yönetim özelliklerine ve Microsoft 365 Güvenlik Merkezi, Microsoft 365 Uyumluluk Merkezi, Exchange Online, SharePoint Online ve Skype Kurumsal Çevrimiçi kullanım gibi Azure Active Directory kimliklerini kullanan hizmetlere erişebilir. Ayrıca, genel Yöneticiler tüm Azure aboneliklerini ve Yönetim gruplarını yönetmek üzere [erişimini yükseltebilir](../../role-based-access-control/elevate-access-global-admin.md) . Bu, genel yöneticilerin ilgili Azure AD kiracısı kullanarak tüm Azure kaynaklarına tam erişim sağlamasına izin verir. Azure AD organizasyonu için kaydolan kişi genel yönetici haline gelir. Şirketinizde birden fazla genel yönetici olabilir. Genel Yöneticiler, tüm kullanıcılar ve diğer tüm yöneticiler için parolayı sıfırlayabilir.

> [!NOTE]
> En iyi uygulama olarak, Microsoft, genel yönetici rolünü kuruluşunuzda beşten az kişiden fazlasına atamanızı önerir. Daha fazla bilgi için bkz. [Azure AD rolleri Için en iyi uygulamalar](best-practices.md).

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/Accessincelemeleri/allProperties/allTasks | Erişim İncelemeleri oluşturun ve silin ve Azure AD 'de erişim gözden geçirmeleri özelliklerinin tümünü okuyun ve güncelleştirin |
> | Microsoft. Directory/Yönetimtiveunits/allProperties/allTasks | Yönetim birimleri oluşturma ve yönetme (Üyeler dahil) |
> | Microsoft. Directory/uygulamalar/allProperties/allTasks | Uygulamalar oluşturun ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/uygulamalar/eşitleme/standart/okuma | Uygulama nesnesiyle ilişkili sağlama ayarlarını okuyun |
> | Microsoft. Directory/applicationTemplates/örnek oluştur | Uygulama şablonlarından Galeri uygulamaları oluştur |
> | Microsoft. Directory/Approtaatamalar/allProperties/allTasks | Approlet atamaları oluşturup silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/auditLogs/allProperties/Read | Ayrıcalıklı özellikler dahil olmak üzere denetim günlüklerindeki tüm özellikleri okuyun |
> | Microsoft. Directory/authorizationPolicy/allProperties/allTasks | Yetkilendirme ilkelerinin tüm yönlerini yönetme |
> | Microsoft. Directory/bitlockerKeys/Key/Read | Cihazlarda BitLocker meta verilerini ve anahtarını okuyun |
> | Microsoft. Directory/cloudAppSecurity/allProperties/allTasks | Tüm kaynakları oluşturup silin ve Microsoft Cloud App Security içindeki standart özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/bağlayıcılar/oluşturma | Uygulama proxy bağlayıcıları oluşturma |
> | Microsoft. Directory/bağlayıcılar/allProperties/Read | Uygulama proxy 'si bağlayıcılarının tüm özelliklerini okuyun |
> | Microsoft. Directory/connectorGroups/Create | Uygulama proxy Bağlayıcısı grupları oluşturma |
> | Microsoft. Directory/connectorGroups/Delete | Uygulama proxy Bağlayıcısı gruplarını sil |
> | Microsoft. Directory/connectorGroups/allProperties/Read | Uygulama proxy Bağlayıcısı gruplarının tüm özelliklerini okuyun |
> | Microsoft. Directory/connectorGroups/allProperties/Update | Uygulama proxy Bağlayıcısı gruplarının tüm özelliklerini güncelleştir |
> | Microsoft. Directory/Contacts/allProperties/allTasks | Kişiler oluşturup silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/sözleşmeleri/allProperties/allTasks | İş ortağı sözleşmeleri oluşturup silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/Devices/allProperties/allTasks | Cihazları oluşturun ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/deviceManagementPolicies/standart/okuma | Cihaz Yönetimi uygulama ilkelerindeki standart özellikleri okuyun |
> | Microsoft. Directory/deviceManagementPolicies/temel/güncelleştirme | Cihaz Yönetimi uygulama ilkelerindeki temel özellikleri güncelleştirme |
> | Microsoft. Directory/deviceRegistrationPolicy/Standard/Read | Cihaz kayıt ilkelerinde standart özellikleri okuyun |
> | Microsoft. Directory/deviceRegistrationPolicy/Basic/Update | Cihaz kayıt ilkelerinde temel özellikleri güncelleştirme |
> | Microsoft. Directory/directoryRoles/allProperties/allTasks | Dizin rolleri oluşturup silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/directoryRoleTemplates/allProperties/allTasks | Azure AD rol şablonları oluşturun ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/Domains/allProperties/allTasks | Etki alanlarını oluşturun ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/entitlementManagement/allProperties/allTasks | Kaynakları oluşturup silin ve Azure AD Yetkilendirme Yönetimi 'nde tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/Groups/allProperties/allTasks | Grupları oluşturun ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/Groupsastifbletoroles/Create | Rol atanabilir gruplar oluşturma |
> | Microsoft. Directory/Groupsastifbletoroles/Delete | Rol atanabilir grupları sil |
> | Microsoft. Directory/Groupsastifbletoroles/restore | Rol atanabilir grupları geri yükleme |
> | Microsoft. Directory/Groupsastifbletoroles/allProperties/Update | Rol atanabilir grupları Güncelleştir |
> | Microsoft. Directory/groupSettings/allProperties/allTasks | Grup ayarlarını oluşturun ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/groupSettingTemplates/allProperties/allTasks | Grup ayarı şablonları oluşturun ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/ıdentityprotection/allProperties/allTasks | Tüm kaynakları oluşturup silin ve Azure AD Kimlik Koruması içindeki standart özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/Loginorganizationmarkalaması/allProperties/allTasks | Logınantmarkalaması oluşturup silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2,0 izin verir ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/kuruluş/allProperties/allTasks | Kuruluşlar oluşturun ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/policies/allProperties/allTasks | İlke oluşturma ve silme ve tüm özellikleri okuma ve güncelleştirme |
> | Microsoft. Directory/conditionalAccessPolicies/allProperties/allTasks | Koşullu erişim ilkelerinin tüm özelliklerini yönetme |
> | Microsoft. Directory/Privilegedıdentitymanagement/allProperties/Read | Privileged Identity Management tüm kaynakları okuyun |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Sağlama günlüklerinin tüm özelliklerini okuyun |
> | Microsoft. Directory/Roleatamaları/allProperties/allTasks | Rol atamaları oluşturun ve silin ve tüm rol atama özelliklerini okuyun ve güncelleştirin |
> | Microsoft. Directory/roleDefinitions/allProperties/allTasks | Rol tanımları oluşturun ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/Scopedrolemembersevk/allProperties/allTasks | Scopedrolemembersevk eden oluşturun ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/serviceAction/activateService | , Bir hizmet için "hizmeti etkinleştirme" eylemini gerçekleştirebilir |
> | Microsoft. Directory/serviceAction/disableDirectoryFeature | "Dizin özelliğini devre dışı bırak" hizmet eylemini gerçekleştirebilir |
> | Microsoft. Directory/serviceAction/enableDirectoryFeature | "Dizin özelliğini etkinleştir" hizmet eylemini gerçekleştirebilir |
> | Microsoft. Directory/serviceAction/getAvailableExtentionProperties | GetAvailableExtentionProperties hizmeti eylemini gerçekleştirebilir |
> | Microsoft. Directory/Servicesorumlularını/allProperties/allTasks | Hizmet sorumlularını oluşturup silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/Servicesorumlularını/managePermissionGrantsForAll. Microsoft-Company-Admin | Tüm uygulamalar için izin verme |
> | Microsoft. Directory/Servicesorumlularını/managePermissionGrantsForGroup. Microsoft-All-Application-Permissions | Hizmet sorumlusu bir grubun verilerine doğrudan erişim verme |
> | Microsoft. Directory/Servicesorumlularını/eşitleme/standart/okuma | Hizmet sorumluyla ilişkili sağlama ayarlarını okuyun |
> | Microsoft. Directory/Signınreports/allProperties/Read | Ayrıcalıklı özellikler dahil olmak üzere oturum açma raporlarında tüm özellikleri okuyun |
> | Microsoft. Directory/subscribedSkus/allProperties/allTasks | Abonelikler satın alma ve yönetme ve abonelikleri silme |
> | Microsoft. Directory/Users/allProperties/allTasks | Kullanıcı oluşturun ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/permissionGrantPolicies/oluşturma | İzin verme ilkeleri oluşturma |
> | Microsoft. Directory/permissionGrantPolicies/Delete | İzin verme ilkelerini silme |
> | Microsoft. Directory/permissionGrantPolicies/standart/okuma | İzin verme ilkelerinin standart özelliklerini okuyun |
> | Microsoft. Directory/permissionGrantPolicies/Basic/Update | İzin verme ilkelerinin temel özelliklerini güncelleştirme |
> | Microsoft. Directory/servicePrincipalCreationPolicies/oluşturma | Hizmet sorumlusu oluşturma ilkeleri oluşturma |
> | Microsoft. Directory/servicePrincipalCreationPolicies/Delete | Hizmet sorumlusu oluşturma ilkelerini silme |
> | Microsoft. Directory/servicePrincipalCreationPolicies/standart/Read | Hizmet sorumlusu oluşturma ilkelerinin standart özelliklerini okuyun |
> | Microsoft. Directory/servicePrincipalCreationPolicies/Basic/Update | Hizmet sorumlusu oluşturma ilkelerinin temel özelliklerini güncelleştirme |
> | Microsoft. Azure. advancedThreatProtection/Allentitıes/allTasks | Azure Gelişmiş tehdit koruması 'nın tüm yönlerini yönetin |
> | Microsoft. Azure. ınformationprotection/allEntities/allTasks | Azure Information Protection tüm yönlerini yönetin |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. Commerce. faturalandırma/allEntities/allTasks | Office 365 faturalandırma 'nin tüm yönlerini yönetme |
> | Microsoft. dynamics365/allEntities/allTasks | Dynamics 365 'in tüm yönlerini yönetme |
> | Microsoft. Flow/Allentitıes/allTasks | Microsoft Power otomatikleştirmenin tüm yönlerini yönetin |
> | Microsoft. Intune/allEntities/allTasks | Microsoft Intune tüm yönlerini yönetin |
> | Microsoft. office365. Karmaşıssuancemanager/allEntities/allTasks | Office 365 uyumluluk Yöneticisi 'nin tüm yönlerini yönetme |
> | Microsoft. office365. desktopAnalytics/allEntities/allTasks | Masaüstü analizinin tüm yönlerini yönetme |
> | Microsoft. office365. Exchange/allEntities/temel/allTasks | Exchange Online 'ın tüm yönlerini yönetin |
> | Microsoft. office365. kasa/allEntities/allTasks | Müşteri Kasası tüm yönlerini yönetin |
> | Microsoft. office365. messageCenter/messages/okundu | Microsoft 365 Yönetim Merkezi 'nde Ileti merkezindeki iletileri okuma, güvenlik iletilerini hariç tutma |
> | Microsoft. office365. messageCenter/securityMessages/Read | Microsoft 365 Yönetim merkezinde Ileti merkezinde güvenlik iletilerini okuyun |
> | Microsoft. office365. Network/Performance/allProperties/Read | Microsoft 365 Yönetim Merkezi 'ndeki tüm ağ performansı özelliklerini okuyun |
> | Microsoft. office365. protectionCenter/allEntities/allProperties/allTasks | Güvenlik ve uyumluluk merkezlerinin tüm yönlerini yönetin |
> | Microsoft. office365. Search/Content/Manage | İçerik oluşturma ve silme ve Microsoft Search 'te tüm özellikleri okuma ve güncelleştirme |
> | Microsoft. office365. Securityzorluk Ancecenter/allEntities/allTasks | Tüm kaynakları oluşturup silin ve Microsoft 365 güvenlik ve Uyumluluk Merkezi 'nde standart özellikleri okuyun ve güncelleştirin |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. sharePoint/allEntities/allTasks | Tüm kaynakları oluşturup silin ve SharePoint 'te standart özellikleri okuyun ve güncelleştirin |
> | Microsoft. office365. Sktypeınfo/allEntities/allTasks | Skype Kurumsal Çevrimiçi 'nin tüm yönlerini yönetin |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. usageReports/allEntities/allProperties/Read | Office 365 kullanım raporlarını okuyun |
> | Microsoft. office365. userCommunication/allEntities/allTasks | Yeni iletilerin görünürlüğünü okuyun ve güncelleştirin |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |
> | Microsoft. powerApps/allEntities/allTasks | Power Apps 'in tüm yönlerini yönetin |
> | Microsoft. powerApps. PowerBI/allEntities/allTasks | Power BI tüm yönlerini yönetin |
> | Microsoft. Windows. savunma Deradvancedthreatprotection/allEntities/allTasks | Endpoint için Microsoft Defender 'ın tüm yönlerini yönetin |

## <a name="global-reader"></a>Genel okuyucu

Bu roldeki kullanıcılar Microsoft 365 hizmetleri genelinde ayarları ve yönetim bilgilerini okuyabilir, ancak yönetim eylemleri alamaz. Genel okuyucu genel yöneticiye salt okunurdur. Planlama, denetim veya araştırmalar için genel yönetici yerine genel okuyucu atayın. Küresel bir yönetici rolü atanmadan çalışmayı kolaylaştırmak için, Exchange Yöneticisi gibi diğer sınırlı yönetici rolleriyle birlikte genel okuyucu kullanın. Küresel okuyucu Microsoft 365 Yönetim Merkezi, Exchange Yönetim Merkezi, SharePoint Yönetim Merkezi, takımlar yönetici Merkezi, Güvenlik Merkezi, Uyumluluk Merkezi, Azure AD Yönetim Merkezi ve cihaz yönetimi Yönetim Merkezi ile birlikte kullanılabilir.

> [!NOTE]
> Genel okuyucu rolünde Şu anda birkaç kısıtlama vardır-
>
>- [OneDrive Yönetim Merkezi](https://admin.onedrive.com/) -OneDrive Yönetim Merkezi, genel okuyucu rolünü desteklemez
>- [M365 Yönetim Merkezi](https://admin.microsoft.com/Adminportal/Home#/homepage) -genel okuyucu tümleşik uygulamaları okuyamıyor. M365 Yönetim Merkezi 'nin sol bölmesindeki **Ayarlar** ' ın altında **Tümleşik uygulamalar** sekmesini bulmayacağız.
>- [Office güvenlik & Uyumluluk Merkezi](https://sip.protection.office.com/homepage) -küresel okuyucu SCC denetim günlüklerini okuyamıyor, içerik araması yapamıyor veya güvenli puanı göremez.
>- [Takımlar Yönetim Merkezi](https://admin.teams.microsoft.com) -küresel okuyucu **takımlar yaşam döngüsünü**, **analiz & raporlarını**, **IP telefon cihaz yönetimini** ve **uygulama kataloğunu** okuyamıyor.
>- [Privileged Access Management (Pam)](/office365/securitycompliance/privileged-access-management-overview) , genel okuyucu rolünü desteklemez.
>- [Azure Information Protection](/azure/information-protection/what-is-information-protection) -genel okuyucu yalnızca [merkezi raporlama Için](/azure/information-protection/reports-aip) desteklenir ve Azure AD Kuruluşunuz [Birleşik etiketleme platformunda](/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)ne zaman değildir.
>
> Bu özellikler Şu anda geliştirme aşamasındadır.
>

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/uygulamalar/applicationProxy/okuma | Tüm uygulama proxy özelliklerini okuyun |
> | Microsoft. Directory/uygulamalar/eşitleme/standart/okuma | Uygulama nesnesiyle ilişkili sağlama ayarlarını okuyun |
> | Microsoft. Directory/auditLogs/allProperties/Read | Ayrıcalıklı özellikler dahil olmak üzere denetim günlüklerindeki tüm özellikleri okuyun |
> | Microsoft. Directory/bitlockerKeys/Key/Read | Cihazlarda BitLocker meta verilerini ve anahtarını okuyun |
> | Microsoft. Directory/bağlayıcılar/allProperties/Read | Uygulama proxy 'si bağlayıcılarının tüm özelliklerini okuyun |
> | Microsoft. Directory/connectorGroups/allProperties/Read | Uygulama proxy Bağlayıcısı gruplarının tüm özelliklerini okuyun |
> | Microsoft. Directory/entitlementManagement/allProperties/Read | Azure AD Yetkilendirme Yönetimi 'ndeki tüm özellikleri okuyun |
> | Microsoft. Directory/deviceManagementPolicies/standart/okuma | Cihaz Yönetimi uygulama ilkelerindeki standart özellikleri okuyun |
> | Microsoft. Directory/deviceRegistrationPolicy/Standard/Read | Cihaz kayıt ilkelerinde standart özellikleri okuyun |
> | Microsoft. Directory/Groups/hiddenMembers/Read | Bir grubun gizli üyelerini oku |
> | Microsoft. Directory/policies/standart/Read | İlkelerdeki temel özellikleri okuyun |
> | Microsoft. Directory/policies/Owners/Read | İlkelerin sahiplerini okuma |
> | Microsoft. Directory/policies/policyAppliedTo/Read | Policies. policyAppliedTo özelliğini okuyun |
> | Microsoft. Directory/conditionalAccessPolicies/standart/okuma | İlkeler için koşullu erişimi okuyun |
> | Microsoft. Directory/conditionalAccessPolicies/sahipler/okuma | Koşullu erişim ilkelerinin sahiplerini okuyun |
> | Microsoft. Directory/conditionalAccessPolicies/policyAppliedTo/Read | Koşullu erişim ilkeleri için "uygulandı" özelliğini okuyun |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Sağlama günlüklerinin tüm özelliklerini okuyun |
> | Microsoft. Directory/Servicesorumlularını/kimlik doğrulaması/okuma | Hizmet sorumluları 'nda kimlik doğrulama özelliklerini okuma |
> | Microsoft. Directory/Servicesorumlularını/eşitleme/standart/okuma | Hizmet sorumluyla ilişkili sağlama ayarlarını okuyun |
> | Microsoft. Directory/Signınreports/allProperties/Read | Ayrıcalıklı özellikler dahil olmak üzere oturum açma raporlarında tüm özellikleri okuyun |
> | Microsoft. Directory/Users/strongAuthentication/Read | Kullanıcılar için güçlü kimlik doğrulama özelliğini okuyun |
> | Microsoft. Commerce. faturalandırma/allEntities/okuma | Office 365 faturalandırma 'nin tüm kaynaklarını okuyun |
> | Microsoft. office365. Exchange/allEntities/standart/okuma | Exchange Online 'ın tüm kaynaklarını okuyun |
> | Microsoft. office365. messageCenter/messages/okundu | Microsoft 365 Yönetim Merkezi 'nde Ileti merkezindeki iletileri okuma, güvenlik iletilerini hariç tutma |
> | Microsoft. office365. messageCenter/securityMessages/Read | Microsoft 365 Yönetim merkezinde Ileti merkezinde güvenlik iletilerini okuyun |
> | Microsoft. office365. Network/Performance/allProperties/Read | Microsoft 365 Yönetim Merkezi 'ndeki tüm ağ performansı özelliklerini okuyun |
> | Microsoft. office365. protectionCenter/allEntities/allProperties/Read | Güvenlik ve uyumluluk merkezlerindeki tüm özellikleri okuyun |
> | Microsoft. office365. Securityzorluk Ancecenter/allEntities/Read | Microsoft 365 güvenlik ve Uyumluluk Merkezi 'nde standart özellikleri okuyun |
> | Microsoft. office365. usageReports/allEntities/allProperties/Read | Office 365 kullanım raporlarını okuyun |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="groups-administrator"></a>Grup Yöneticisi

Bu roldeki kullanıcılar, adlandırma ve süre sonu ilkeleri gibi grupları ve ayarlarını oluşturabilir/yönetebilir. Bu role bir Kullanıcı atamanın, bu kullanıcılara kuruluştaki tüm grupları takımlar, SharePoint, Yammer gibi çeşitli iş yükleri arasında Outlook 'a ek olarak yönetme olanağı sağladığını anlamak önemlidir. Ayrıca, Kullanıcı, çeşitli grup ayarlarını Microsoft Yönetim Merkezi, Azure portal gibi çeşitli yönetim portallarında ve takımlar ve SharePoint yönetim merkezleri gibi iş yüklerinin yanı sıra iş yüküne göre yönetebilecektir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/Groups/Atamalisansı | Grup tabanlı lisanslama için gruplara ürün lisansları atama |
> | Microsoft. Directory/gruplar/oluştur | Rol atanabilir gruplar hariç olmak üzere Grup oluşturma |
> | Microsoft. Directory/gruplar/Sil | Rol atanabilir grup hariç olmak üzere grupları sil |
> | Microsoft. Directory/Groups/hiddenMembers/Read | Bir grubun gizli üyelerini oku |
> | Microsoft. Directory/Groups/Reprocesslicenseatama | Grup tabanlı lisanslama için lisans atamalarını yeniden işleme |
> | Microsoft. Directory/Groups/restore | Silinen grupları geri yükleme |
> | Microsoft. Directory/Groups/Basic/Update | Rol atanabilir gruplar hariç olmak üzere gruplardaki temel özellikleri Güncelleştir |
> | Microsoft. Directory/gruplar/sınıflandırma/güncelleştirme | Rol atanabilir gruplar hariç olmak üzere grupların sınıflandırma özelliğini güncelleştirme |
> | Microsoft. Directory/Groups/dynamicMembershipRule/Update | Rol atanabilir gruplar hariç olmak üzere grupların dinamik üyelik kuralını Güncelleştir |
> | Microsoft. Directory/gruplar/groupType/Update | Grup için groupType özelliğini güncelleştirme |
> | Microsoft. Directory/gruplar/Üyeler/güncelleştirme | Rol atanabilir gruplar hariç olmak üzere grupların üyelerini Güncelleştir |
> | Microsoft. Directory/Groups/Onpremgeri yazma/güncelleştirme | Azure AD Connect ile şirket içine geri yazılacak Azure Active Directory grupları güncelleştirme |
> | Microsoft. Directory/gruplar/Owners/Update | Rol atanabilir gruplar hariç olmak üzere grupların sahiplerini Güncelleştir |
> | Microsoft. Directory/Groups/Settings/Update | Grupların ayarlarını Güncelleştir |
> | Microsoft. Directory/gruplar/görünürlük/güncelleştirme | Grupların görünürlük özelliğini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/managePermissionGrantsForGroup. Microsoft-All-Application-Permissions | Hizmet sorumlusu bir grubun verilerine doğrudan erişim verme |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="guest-inviter"></a>Konuk davetci

**Üyeler** Kullanıcı ayarı Hayır olarak ayarlandığında, bu roldeki KULLANıCıLAR Azure Active Directory B2B Konuk kullanıcı davetlerini yönetebilir. [Azure AD B2B Işbirliği hakkında](../external-identities/what-is-b2b.md)B2B işbirliği hakkında daha fazla bilgi. Başka herhangi bir izin içermez.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/Users/Davetteguest | Konuk kullanıcı davet etme |
> | Microsoft. Directory/kullanıcılar/standart/okuma | Kullanıcıların temel özelliklerini okuma |
> | Microsoft. Directory/Users/Approtaatamalar/okuma | Kullanıcıların uygulama rolü atamalarını okuyun |
> | Microsoft. Directory/Users/directReports/Read | Kullanıcılar için doğrudan raporları okuyun |
> | Microsoft. Directory/Users/Manager/okuma | Kullanıcıların yöneticisini okuyun |
> | Microsoft. Directory/kullanıcılar/memberOf/Read | Kullanıcıların grup üyeliklerini okuyun |
> | Microsoft. Directory/Users/oAuth2PermissionGrants/Read | Kullanıcılara atanan izin verdiği izinleri oku |
> | Microsoft. Directory/Users/ownedDevices/Read | Kullanıcıların sahip olduğu cihazları okuyun |
> | Microsoft. Directory/Users/ownedObjects/Read | Kullanıcıların sahip olduğu nesneleri okuma |
> | Microsoft. Directory/Users/registeredDevices/Read | Kullanıcıların kayıtlı cihazlarını okuyun |

## <a name="helpdesk-administrator"></a>Yardım Masası Yöneticisi

Bu role sahip kullanıcılar parolaları değiştirebilir, yenileme belirteçlerini geçersiz kılabilir, hizmet isteklerini yönetebilir ve hizmet durumunu izleyebilir. Yenileme belirtecinin geçersiz kılınması, kullanıcının yeniden oturum açmasını zorlar. Bir yardım masası yöneticisinin bir kullanıcının parolasını sıfırlayıp sıfırlayamayacağı ve yenileme belirteçlerini geçersiz kılabileceği, kullanıcının atandığı role göre değişir. Bir yardım masası yöneticisinin parolalarını sıfırlayıp yenileme belirteçlerini geçersiz kılabileceği rollerin listesi için bkz. [parola sıfırlama izinleri](#password-reset-permissions).

> [!IMPORTANT]
> Bu role sahip kullanıcılar, Azure Active Directory ' nin içindeki ve dışındaki gizli veya özel bilgilere veya kritik yapılandırmaya erişimi olabilecek kişilerin parolalarını değiştirebilir. Bir kullanıcının parolasını değiştirmek, kullanıcının kimliğini ve izinlerini kabul etme imkanını ifade edebilir. Örnek:
>
>- Sahip oldukları uygulamaların kimlik bilgilerini yönetebilen uygulama kaydı ve kurumsal uygulama sahipleri. Bu uygulamaların Azure AD 'de ayrıcalıklı izinleri olabilir ve yardım masası yöneticilerine başka bir yerde izin verilmez. Bu yol aracılığıyla bir yardım masası Yöneticisi, bir uygulama sahibinin kimliğini varsayabilir ve daha sonra uygulamanın kimlik bilgilerini güncelleştirerek ayrıcalıklı bir uygulamanın kimliğini daha fazla varsayabilir.
>- Azure abonelik sahipleri, hassas veya özel bilgilere veya Azure 'da kritik yapılandırmaya erişebilir.
>- Grup üyeliğini yönetebilen güvenlik grubu ve Grup sahipleri Microsoft 365. Bu gruplar, Azure AD 'de ve başka bir yerde hassas veya özel bilgilere veya kritik yapılandırmaya erişim verebilir.
>- Exchange Online, Office Security ve Uyumluluk Merkezi ve insan kaynakları sistemleri gibi Azure AD dışında diğer hizmetlerde bulunan yöneticiler.
>- Gizli veya özel bilgilere erişebilen Yöneticiler, yasal Counsel ve insan kaynakları çalışanları gibi yönetici olmayanlar.

Kullanıcıların alt kümeleri üzerinde yönetim izinlerinin yetkisini verme ve ilkeleri bir kullanıcı alt kümesine uygulama, [yönetim birimleriyle](administrative-units.md)mümkündür.

Bu rol daha önce [Azure Portal](https://portal.azure.com/)"parola Yöneticisi" olarak adlandırılmıştı. Azure AD 'deki "Yardım Masası Yöneticisi" adı artık Azure AD PowerShell 'deki adıyla ve Microsoft Graph API 'siyle eşleşiyor.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/bitlockerKeys/Key/Read | Cihazlarda BitLocker meta verilerini ve anahtarını okuyun |
> | Microsoft. Directory/Users/ınvalidateallrefreshtokens | Kullanıcı yenileme belirteçleri geçersiz kılarak oturum açmaya zorla |
> | Microsoft. Directory/Users/Password/Update | Tüm kullanıcılar için parolaları Sıfırla |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="hybrid-identity-administrator"></a>Karma kimlik yöneticisi

Bu roldeki kullanıcılar, bulut sağlama ve Azure AD Connect ve Federasyon ayarlarını yönetme işlemlerini kullanarak AD 'den Azure AD 'ye sağlama yapılandırma kurulumu oluşturabilir, yönetebilir ve dağıtabilir. Kullanıcılar ayrıca bu rolü kullanarak günlükleri giderebilir ve izleyebilir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/uygulamalar/oluşturma | Tüm uygulama türlerini oluşturma |
> | Microsoft. Directory/uygulamalar/Sil | Tüm uygulama türlerini Sil |
> | Microsoft. Directory/uygulamalar/appRoles/Update | Tüm uygulama türlerinde appRoles özelliğini güncelleştirme |
> | Microsoft. Directory/uygulamalar/hedef kitle/güncelleştirme | Uygulamalar için hedef kitle özelliğini güncelleştirme |
> | Microsoft. Directory/uygulamalar/kimlik doğrulaması/güncelleştirme | Tüm uygulama türlerinde kimlik doğrulamasını Güncelleştir |
> | Microsoft. Directory/uygulamalar/temel/güncelleştirme | Uygulamalar için temel özellikleri güncelleştirme |
> | Microsoft. Directory/uygulamalar/kimlik bilgileri/güncelleştirme | Uygulama kimlik bilgilerini güncelleştir |
> | Microsoft. Directory/uygulamalar/sahipler/güncelleştirme | Uygulamaların sahiplerini güncelleştirme |
> | Microsoft. Directory/uygulamalar/izinler/güncelleştirme | Tüm uygulama türlerinde sunulan izinleri ve gerekli izinleri güncelleştirin |
> | Microsoft. Directory/uygulamalar/ilkeler/güncelleştirme | Uygulama ilkelerini güncelleştirme |
> | Microsoft. Directory/uygulamalar/eşitleme/standart/okuma | Uygulama nesnesiyle ilişkili sağlama ayarlarını okuyun |
> | Microsoft. Directory/applicationTemplates/örnek oluştur | Uygulama şablonlarından Galeri uygulamaları oluştur |
> | Microsoft. Directory/auditLogs/allProperties/Read | Ayrıcalıklı özellikler dahil olmak üzere denetim günlüklerindeki tüm özellikleri okuyun |
> | Microsoft. Directory/Cloudsağlamasını/allProperties/allTasks | Azure AD bulut sağlama hizmeti 'nin tüm özelliklerini okuyun ve yapılandırın. |
> | Microsoft. Directory/Domains/allProperties/Read | Etki alanlarının tüm özelliklerini okuyun |
> | Microsoft. Directory/Domains/Federation/Update | Etki alanlarının Federasyon özelliğini güncelleştirme |
> | Microsoft. Directory/kuruluş/dirSync/Update | Kuruluş dizini eşitleme özelliğini güncelleştirme |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Sağlama günlüklerinin tüm özelliklerini okuyun |
> | Microsoft. Directory/Servicesorumlularını/oluşturma | Hizmet sorumlusu oluşturma |
> | Microsoft. Directory/Servicesorumlularını/silme | Hizmet sorumlularını silme |
> | Microsoft. Directory/Servicesorumlularını/Disable | Hizmet sorumlularını devre dışı bırak |
> | Microsoft. Directory/Servicesorumlularını/etkinleştir | Hizmet sorumlularını etkinleştirme |
> | Microsoft. Directory/Servicesorumlularını/synchronizationCredentials/Manage | Uygulama sağlama gizli dizilerini ve kimlik bilgilerini yönetme |
> | Microsoft. Directory/Servicesorumlularını/synchronizationJobs/Manage | Uygulama sağlama eşitleme işlerini başlatın, yeniden başlatın ve duraklatın |
> | Microsoft. Directory/Servicesorumlularını/synchronizationSchema/Yönet | Uygulama sağlama eşitleme işleri ve şeması oluşturma ve yönetme |
> | Microsoft. Directory/Servicesorumlularını/hedef kitle/güncelleştirme | Hizmet sorumluları üzerinde hedef kitle özelliklerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/kimlik doğrulaması/güncelleştirme | Hizmet sorumluları 'nda kimlik doğrulama özelliklerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/temel/güncelleştirme | Hizmet sorumluları 'ndaki temel özellikleri güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/kimlik bilgileri/güncelleştirme | Hizmet sorumluları 'nın kimlik bilgilerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/Owners/Update | Hizmet sorumluları 'nın sahiplerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/izinleri/güncelleştirme | Hizmet sorumluları 'nın izinlerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/ilkeleri/güncelleştirme | Hizmet sorumluları ilkelerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/etiketi/güncelleştirme | Hizmet sorumluları için Tag özelliğini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/eşitleme/standart/okuma | Hizmet sorumluyla ilişkili sağlama ayarlarını okuyun |
> | Microsoft. Directory/Signınreports/allProperties/Read | Ayrıcalıklı özellikler dahil olmak üzere oturum açma raporlarında tüm özellikleri okuyun |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. messageCenter/messages/okundu | Microsoft 365 Yönetim Merkezi 'nde Ileti merkezindeki iletileri okuma, güvenlik iletilerini hariç tutma |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="insights-administrator"></a>Öngörüler Yöneticisi

Bu roldeki kullanıcılar, [M365 Insights uygulamasındaki](https://go.microsoft.com/fwlink/?linkid=2129521)tüm yönetim özelliklerine erişebilir. Bu rol, dizin bilgilerini okuyabilir, hizmet durumunu izleyebilir, dosya destek biletlerini izleyebilir ve Öngörüler yönetici ayarları yönlerini erişebilir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. Insights/allEntities/allTasks | Öngörüler uygulamasının tüm yönlerini yönetme |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="insights-business-leader"></a>Öngörüler Iş lideri

Bu roldeki kullanıcılar, [M365 Insights uygulaması](https://go.microsoft.com/fwlink/?linkid=2129521)aracılığıyla bir panolar ve Öngörüler kümesine erişebilir. Bu, tüm panolara ve sunulan Öngörüler ve veri araştırma işlevlerine tam erişim içerir. Bu roldeki kullanıcılar, Öngörüler yönetici rolünün sorumluluğu olan ürün yapılandırma ayarlarına erişemez.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Insights/raporlar/okuma | Öngörüler uygulamasındaki raporları ve panoyu görüntüleme |
> | Microsoft. Insights/programlar/güncelleştirme | Öngörüler uygulamasındaki programları dağıtma ve yönetme |

## <a name="intune-administrator"></a>Intune Yöneticisi

Bu role sahip olan kullanıcılar, hizmet mevcut olduğunda Microsoft Intune çevrimiçi olarak genel izinlere sahiptir. Ayrıca, bu rol, ilkeyi ilişkilendirmek ve grupları oluşturmak ve yönetmek için kullanıcıları ve cihazları yönetme özelliğini içerir. [Microsoft Intune Ile rol tabanlı yönetim denetimi (RBAC)](/intune/role-based-access-control)hakkında daha fazla bilgi.

Bu rol, tüm güvenlik gruplarını oluşturabilir ve yönetebilir. Ancak, Intune yöneticisinin Office grupları üzerinde yönetici hakları yoktur. Bu, yöneticinin kuruluştaki tüm Office gruplarının sahiplerini veya üyeliklerini güncelleştiremediği anlamına gelir. Ancak, oluşturduğu Office grubunu, son kullanıcı ayrıcalıklarının bir parçası olarak gelen oluşturduğu Office grubunu yönetebilir. Bu nedenle, oluşturduğu herhangi bir Office grubu (güvenlik grubu değil), 250 kotasına göre sayılır.

> [!NOTE]
> Microsoft Graph API ve Azure AD PowerShell 'de, bu rol "Intune Hizmet Yöneticisi" olarak tanımlanır. Bu, [Azure Portal](https://portal.azure.com)"Intune Yöneticisi" dir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/bitlockerKeys/Key/Read | Cihazlarda BitLocker meta verilerini ve anahtarını okuyun |
> | Microsoft. Directory/kişiler/oluştur | Kişi oluştur |
> | Microsoft. Directory/Contacts/Delete | Kişileri Sil |
> | Microsoft. Directory/Contacts/Basic/Update | Kişilerdeki temel özellikleri Güncelleştir |
> | Microsoft. Directory/cihazlar/oluşturma | Cihaz oluşturma (Azure AD 'ye kaydolma) |
> | Microsoft. Directory/Devices/Delete | Cihazları Azure AD 'den silme |
> | Microsoft. Directory/cihazlar/devre dışı bırak | Azure AD 'de cihazları devre dışı bırakma |
> | Microsoft. Directory/cihazlar/etkinleştir | Azure AD 'de cihazları etkinleştirme |
> | Microsoft. Directory/Devices/Basic/Update | Cihazlarda temel özellikleri güncelleştirme |
> | Microsoft. Directory/Devices/extensionAttributes/Update | Cihazların tüm değerlerini güncelleştir. extensionAttributes özelliği |
> | Microsoft. Directory/Devices/kayıt araçları/güncelleştirme | Cihazların kayıtlı sahiplerini güncelleştirme |
> | Microsoft. Directory/Devices/registeredUsers/Update | Cihazların kayıtlı kullanıcılarını güncelleştirme |
> | Microsoft. Directory/deviceManagementPolicies/standart/okuma | Cihaz Yönetimi uygulama ilkelerindeki standart özellikleri okuyun |
> | Microsoft. Directory/deviceRegistrationPolicy/Standard/Read | Cihaz kayıt ilkelerinde standart özellikleri okuyun |
> | Microsoft. Directory/Groups/hiddenMembers/Read | Bir grubun gizli üyelerini oku |
> | Microsoft. Directory/groups. Security/Create | Rol atanabilir grupların dışlamasıyla birlikte güvenlik grupları oluşturun |
> | Microsoft. Directory/groups. Security/Delete | Rol atanabilir grupların dışlamasıyla güvenlik gruplarını silme |
> | Microsoft. Directory/groups. Security/Basic/Update | Rol atanabilir grupların dışlamasıyla güvenlik gruplarındaki temel özellikleri Güncelleştir |
> | Microsoft. Directory/gruplar. güvenlik/sınıflandırma/güncelleştirme | Rol atanabilir grupların dışlamasıyla güvenlik gruplarının sınıflandırma özelliğini güncelleştirme |
> | Microsoft. Directory/groups. Security/dynamicMembershipRule/Update | Rol atanabilir grupların dışlamasıyla birlikte güvenlik gruplarının dynamicMembershipRule özelliğini güncelleştirin |
> | Microsoft. Directory/gruplar. güvenlik/Üyeler/güncelleştirme | Rol atanabilir grupların dışlamasıyla güvenlik gruplarının üyelerini güncelleştirme |
> | Microsoft. Directory/gruplar. güvenlik/sahipler/güncelleştirme | Rol atanabilir grupların dışlamasıyla güvenlik gruplarının sahiplerini güncelleştirme |
> | Microsoft. Directory/groups. Security/Visibility/Update | Rol atanabilir grupların dışlamasıyla güvenlik gruplarının görünürlük özelliğini güncelleştirme |
> | Microsoft. Directory/Users/Basic/Update | Kullanıcıların temel özelliklerini güncelleştirme |
> | Microsoft. Directory/Users/Manager/Update | Kullanıcılar için Güncelleştirme Yöneticisi |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. Intune/allEntities/allTasks | Microsoft Intune tüm yönlerini yönetin |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="kaizala-administrator"></a>Kaizala Yöneticisi

Bu role sahip olan kullanıcılar, hizmet mevcut olduğunda Microsoft Kaizala içindeki ayarları yönetmek için genel izinlere sahiptir ve destek biletlerini yönetebilir ve hizmet durumunu izleyebilir. Ayrıca, Kullanıcı, kaizala tarafından Kaizala tarafından kullanılan ve Kaizala eylemleri kullanılarak oluşturulan iş raporlarının kullanımına & benimseme ile ilgili raporlara erişebilir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="knowledge-administrator"></a>Bilgi Bankası Yöneticisi

Bu roldeki kullanıcıların, Microsoft 365 Yönetim merkezinde tüm bilgi, öğrenme ve akıllı özellikler ayarlarına tam erişimi vardır. Ürünlerin paketini, lisanslama ayrıntılarını ve erişimi denetleme sorumluluğunu içeren genel bir bilgiye sahip olurlar. Bilgi Yöneticisi konular, kısaltmalar ve öğrenme kaynakları gibi içerikler oluşturabilir ve yönetebilir. Ayrıca, bu kullanıcılar içerik merkezleri oluşturabilir, hizmet durumunu izleyebilir ve hizmet istekleri oluşturabilir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/groups. Security/Create | Rol atanabilir grupların dışlamasıyla birlikte güvenlik grupları oluşturun |
> | Microsoft. Directory/groups. Security/createAsOwner | Rol atanabilir grupların dışlamasıyla birlikte güvenlik grupları oluşturun ve Oluşturucu ilk sahip olarak eklenir |
> | Microsoft. Directory/groups. Security/Delete | Rol atanabilir grupların dışlamasıyla güvenlik gruplarını silme |
> | Microsoft. Directory/groups. Security/Basic/Update | Rol atanabilir grupların dışlamasıyla güvenlik gruplarındaki temel özellikleri Güncelleştir |
> | Microsoft. Directory/gruplar. güvenlik/Üyeler/güncelleştirme | Rol atanabilir grupların dışlamasıyla güvenlik gruplarının üyelerini güncelleştirme |
> | Microsoft. Directory/gruplar. güvenlik/sahipler/güncelleştirme | Rol atanabilir grupların dışlamasıyla güvenlik gruplarının sahiplerini güncelleştirme |
> | Microsoft. office365. Knowledge/contentUnderstanding/allProperties/allTasks | Microsoft 365 Yönetim Merkezi 'nde içeriğin tüm özelliklerini okuyun ve güncelleştirin |
> | Microsoft. office365. Knowledge/knowledgeNetwork/allProperties/allTasks | Microsoft 365 Yönetim Merkezi 'nde bilgi ağının tüm özelliklerini okuyun ve güncelleştirin |
> | Microsoft. office365. protectionCenter/sensitivityLabels/allProperties/Read | Güvenlik ve uyumluluk merkezlerindeki duyarlılık etiketlerinin tüm özelliklerini okuyun |
> | Microsoft. office365. sharePoint/allEntities/allTasks | Tüm kaynakları oluşturup silin ve SharePoint 'te standart özellikleri okuyun ve güncelleştirin |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="license-administrator"></a>Lisans Yöneticisi

Bu roldeki kullanıcılar, kullanıcılar, gruplar (grup tabanlı lisanslama kullanarak) için lisans atamaları ekleyebilir, kaldırabilir ve güncelleştirebilir ve kullanıcılara kullanım konumunu yönetebilir. Rol, abonelik satın alma veya yönetme, Grup oluşturma veya yönetme veya kullanım konumunun ötesinde Kullanıcı oluşturma veya yönetme olanağı vermez. Bu rolün destek biletlerini görüntüleme, oluşturma veya yönetme erişimi yok.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/Groups/Atamalisansı | Grup tabanlı lisanslama için gruplara ürün lisansları atama |
> | Microsoft. Directory/Groups/Reprocesslicenseatama | Grup tabanlı lisanslama için lisans atamalarını yeniden işleme |
> | Microsoft. Directory/Users/Atamalisansı | Kullanıcı lisanslarını yönetme |
> | Microsoft. Directory/Users/Reprocesslicenseatama | Kullanıcılar için lisans atamalarını yeniden işleme |
> | Microsoft. Directory/Users/usageLocation/Update | Kullanıcıların kullanım konumunu güncelleştirme |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="message-center-privacy-reader"></a>İleti Merkezi Gizlilik okuyucusu

Bu roldeki kullanıcılar, veri gizlilik iletileri dahil olmak üzere Ileti merkezindeki tüm bildirimleri izleyebilir. İleti Merkezi Gizlilik okuyucuları, veri gizliliğiyle ilgili olanlar da dahil olmak üzere e-posta bildirimleri alır ve Ileti merkezi tercihlerini kullanarak aboneliği kaldırabilirsiniz Veri gizliliği iletilerini yalnızca genel yönetici ve Ileti Merkezi Gizlilik okuyucusu okuyabilir. Ayrıca, bu rol grupları, etki alanlarını ve abonelikleri görüntüleme özelliğini içerir. Bu rolün hizmet isteklerini görüntüleme, oluşturma veya yönetme izni yok.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. office365. messageCenter/messages/okundu | Microsoft 365 Yönetim Merkezi 'nde Ileti merkezindeki iletileri okuma, güvenlik iletilerini hariç tutma |
> | Microsoft. office365. messageCenter/securityMessages/Read | Microsoft 365 Yönetim merkezinde Ileti merkezinde güvenlik iletilerini okuyun |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="message-center-reader"></a>İleti Merkezi okuyucusu

Bu roldeki kullanıcılar, Exchange, Intune ve Microsoft ekipleri gibi yapılandırılmış hizmetlerde kuruluş için [ileti merkezindeki](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) bildirimleri ve danışmanlık sistem durumu güncelleştirmelerini izleyebilir. İleti Merkezi okuyucuları, e-posta, güncelleştirme ve Microsoft 365 İleti Merkezi gönderilerini paylaşabilir. Azure AD 'de, bu role atanan kullanıcılar yalnızca kullanıcılar ve gruplar gibi Azure AD hizmetlerinde salt okuma erişimine sahip olur. Bu rolün destek biletlerini görüntüleme, oluşturma veya yönetme erişimi yok.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. office365. messageCenter/messages/okundu | Microsoft 365 Yönetim Merkezi 'nde Ileti merkezindeki iletileri okuma, güvenlik iletilerini hariç tutma |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="modern-commerce-user"></a>Modern ticari Kullanıcı

Kullanmayın. Bu rol, otomatik olarak ticari olarak atanır ve başka bir kullanım için tasarlanmamıştır veya desteklenmez. Ayrıntıları aşağıda bulabilirsiniz.

Modern ticaret Kullanıcı rolü, belirli kullanıcılara Microsoft 365 yönetim merkezine erişme izni verir ve **giriş**, **faturalandırma** ve **destek** için sol gezinti girişlerini görebilir. Bu alanlarda bulunan içerik, kullanıcılara kendi veya kuruluşunuz için satın aldıkları ürünleri yönetmek üzere atanan, [ticari özel roller](../../cost-management-billing/manage/understand-mca-roles.md) tarafından denetlenir. Bu, faturalandırma veya faturalama hesaplarına ve faturalandırma profillerine erişim için ödeme gibi görevleri içerebilir.

Modern ticaret Kullanıcı rolüne sahip kullanıcılar genellikle diğer Microsoft satın alma sistemlerinde yönetici izinlerine sahiptir ancak yönetim merkezine erişmek için genel yönetici veya faturalama yöneticisi rollerine sahip değildir.

**Modern ticaret Kullanıcı rolü ne zaman atanır?**

* **Microsoft 365 Yönetim Merkezi 'Nde self servis satın alma** – self servis satın alma, kullanıcılara kendi kendilerine satın alarak veya kendi kendilerine kaydolup yeni ürünleri denemenize olanak tanır. Bu ürünler Yönetim merkezinde yönetilir. Self servis satın alma yapan kullanıcılara, Commerce sisteminde bir rol atanır ve bunları Yönetim Merkezi 'nde satın almaları için modern ticaret Kullanıcı rolü atanır. Yöneticiler, [PowerShell](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell)aracılığıyla self servis satın alımları (Power BI, Power Apps, Power otomatikleştirmek) engelleyebilir. Daha fazla bilgi için bkz. [Self servis satın alma hakkında SSS](/microsoft-365/commerce/subscriptions/self-service-purchase-faq).
* **Microsoft Commercial Market 'Ten satın** alma – self servis satın almaya benzer şekilde, bir Kullanıcı Microsoft AppSource veya Azure Marketi 'nden bir ürün veya hizmet satın aldığında, modern ticaret Kullanıcı rolü, genel yönetici veya faturalandırma Yöneticisi rolüne sahip olmadıkları takdirde atanır. Bazı durumlarda, kullanıcıların bu satınalmaları yapmasını engellemiş olabilir. Daha fazla bilgi için bkz. [Microsoft ticari Market](../../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase).
* Microsoft **'un teklifleri** : teklif, Microsoft 'un Microsoft ürün ve hizmetlerini satın alması için Microsoft 'un sunduğu resmi bir tekliftir. Teklifi kabul eden kişinin Azure AD 'de Genel yönetici veya faturalama yöneticisi rolü yoksa, teklif ve yönetim merkezine erişmek için modern ticaret Kullanıcı rolünü tamamlamaya yönelik olarak hem ticari özel bir rol atanır. Yönetim merkezine erişirken yalnızca, ticari olarak verilen rolleri karşılayan özellikleri kullanabilir.
* **Ticarete özgü roller** – bazı kullanıcılara ticari özel roller atanır. Bir Kullanıcı genel veya faturalama yöneticisi değilse, yönetim merkezine erişebilmeleri için modern ticaret Kullanıcı rolü alırlar.

Modern ticaret Kullanıcı rolü bir kullanıcıdan atanmadıklarında, Microsoft 365 yönetim merkezine erişimi kaybeder. Kendileri veya kuruluşunuz için herhangi bir ürünü yönetiyorsanız, bunları yönetemezler. Bu, lisansları atama, ödeme yöntemlerinin değiştirilmesi, faturalandırların faturalandırılır veya abonelikleri yönetmek için başka görevler içerebilir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Commerce. faturalandırma/iş ortakları/okuma | Microsoft 365 faturalandırma 'nin iş ortağı özelliğini oku |
> | Microsoft. Commerce. volumeLicenseServiceCenter/allEntities/allTasks | Toplu Lisanslama hizmeti Merkezi 'nin tüm yönlerini yönetin |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/temel/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="network-administrator"></a>Ağ Yöneticisi

Bu roldeki kullanıcılar, Kullanıcı konumlarından ağ telemetrisini temel alan Microsoft 'un ağ çevre mimarisi önerilerini gözden geçirebilir. Microsoft 365 için ağ performansı, genellikle kullanıcı konumlarına özgü, dikkatli kurumsal müşteri ağ çevre mimarisine bağımlıdır. Bu rol, geliştirilmiş telemetri ölçümlerini ve tasarım önerilerini kolaylaştırmak için, bulunan Kullanıcı konumlarının düzenlenmesine ve bu konumların ağ parametrelerinin yapılandırılmasını sağlar

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. office365. Network/Locations/allProperties/allTasks | Ağ konumlarının tüm yönlerini yönetme |
> | Microsoft. office365. Network/Performance/allProperties/Read | Microsoft 365 Yönetim Merkezi 'ndeki tüm ağ performansı özelliklerini okuyun |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="office-apps-administrator"></a>Office uygulamaları Yöneticisi

Bu roldeki kullanıcılar, Microsoft 365 uygulamalarının bulut ayarlarını yönetebilir. Bu, bulut ilkelerinin yönetimi, self servis indirme yönetimi ve Office uygulamalarıyla ilgili raporu görüntüleme imkanını içerir. Bu rol Ayrıca, destek biletlerini yönetme ve ana yönetim merkezinde hizmet durumunu izleme özelliğini sağlar. Bu role atanan kullanıcılar, Office uygulamalarındaki yeni özelliklerin iletişimini de yönetebilir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. messageCenter/messages/okundu | Microsoft 365 Yönetim Merkezi 'nde Ileti merkezindeki iletileri okuma, güvenlik iletilerini hariç tutma |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. userCommunication/allEntities/allTasks | Yeni iletilerin görünürlüğünü okuyun ve güncelleştirin |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="partner-tier1-support"></a>Partner Katman1 desteği

Kullanmayın. Bu rol kullanımdan kaldırılmıştır ve gelecekte Azure AD 'den kaldırılacak. Bu rol, az sayıda Microsoft yeniden iş ortağı tarafından kullanılmaya yöneliktir ve genel kullanım için tasarlanmamıştır.

> [!IMPORTANT]
> Bu rol, parolaları sıfırlayabilir ve yalnızca yönetici olmayan yöneticiler için yenileme belirteçlerini geçersiz kılabilir. Bu rol kullanım dışı olduğundan kullanılmamalıdır ve artık API 'de döndürülmeyecektir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/uygulamalar/appRoles/Update | Tüm uygulama türlerinde appRoles özelliğini güncelleştirme |
> | Microsoft. Directory/uygulamalar/hedef kitle/güncelleştirme | Uygulamalar için hedef kitle özelliğini güncelleştirme |
> | Microsoft. Directory/uygulamalar/kimlik doğrulaması/güncelleştirme | Tüm uygulama türlerinde kimlik doğrulamasını Güncelleştir |
> | Microsoft. Directory/uygulamalar/temel/güncelleştirme | Uygulamalar için temel özellikleri güncelleştirme |
> | Microsoft. Directory/uygulamalar/kimlik bilgileri/güncelleştirme | Uygulama kimlik bilgilerini güncelleştir |
> | Microsoft. Directory/uygulamalar/sahipler/güncelleştirme | Uygulamaların sahiplerini güncelleştirme |
> | Microsoft. Directory/uygulamalar/izinler/güncelleştirme | Tüm uygulama türlerinde sunulan izinleri ve gerekli izinleri güncelleştirin |
> | Microsoft. Directory/uygulamalar/ilkeler/güncelleştirme | Uygulama ilkelerini güncelleştirme |
> | Microsoft. Directory/kişiler/oluştur | Kişi oluştur |
> | Microsoft. Directory/Contacts/Delete | Kişileri Sil |
> | Microsoft. Directory/Contacts/Basic/Update | Kişilerdeki temel özellikleri Güncelleştir |
> | Microsoft. Directory/gruplar/oluştur | Rol atanabilir gruplar hariç olmak üzere Grup oluşturma |
> | Microsoft. Directory/gruplar/Sil | Rol atanabilir grup hariç olmak üzere grupları sil |
> | Microsoft. Directory/Groups/restore | Silinen grupları geri yükleme |
> | Microsoft. Directory/gruplar/Üyeler/güncelleştirme | Rol atanabilir gruplar hariç olmak üzere grupların üyelerini Güncelleştir |
> | Microsoft. Directory/gruplar/Owners/Update | Rol atanabilir gruplar hariç olmak üzere grupların sahiplerini Güncelleştir |
> | Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2,0 izin verir ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Update | Hizmet sorumlusu rol atamalarını güncelleştirme |
> | Microsoft. Directory/Users/Atamalisansı | Kullanıcı lisanslarını yönetme |
> | Microsoft. Directory/kullanıcılar/oluştur | Kullanıcı ekle |
> | Microsoft. Directory/kullanıcılar/Sil | Kullanıcıları silme |
> | Microsoft. Directory/kullanıcılar/devre dışı bırak | Kullanıcıları devre dışı bırak |
> | Microsoft. Directory/kullanıcılar/etkinleştir | Kullanıcıları etkinleştir |
> | Microsoft. Directory/Users/ınvalidateallrefreshtokens | Kullanıcı yenileme belirteçleri geçersiz kılarak oturum açmaya zorla |
> | Microsoft. Directory/kullanıcılar/geri yükleme | Silinen kullanıcıları geri yükleme |
> | Microsoft. Directory/Users/Basic/Update | Kullanıcıların temel özelliklerini güncelleştirme |
> | Microsoft. Directory/Users/Manager/Update | Kullanıcılar için Güncelleştirme Yöneticisi |
> | Microsoft. Directory/Users/Password/Update | Tüm kullanıcılar için parolaları Sıfırla |
> | Microsoft. Directory/Users/userPrincipalName/Update | Kullanıcıların Kullanıcı asıl adını güncelleştirme |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="partner-tier2-support"></a>Partner Katman2 desteği

Kullanmayın. Bu rol kullanımdan kaldırılmıştır ve gelecekte Azure AD 'den kaldırılacak. Bu rol, az sayıda Microsoft yeniden iş ortağı tarafından kullanılmaya yöneliktir ve genel kullanım için tasarlanmamıştır.

> [!IMPORTANT]
> Bu rol, tüm yönetici olmayan ve yöneticiler için (genel yöneticiler dahil) parolaları sıfırlayabilir ve yenileme belirteçlerini geçersiz kılabilir. Bu rol kullanım dışı olduğundan kullanılmamalıdır ve artık API 'de döndürülmeyecektir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/uygulamalar/appRoles/Update | Tüm uygulama türlerinde appRoles özelliğini güncelleştirme |
> | Microsoft. Directory/uygulamalar/hedef kitle/güncelleştirme | Uygulamalar için hedef kitle özelliğini güncelleştirme |
> | Microsoft. Directory/uygulamalar/kimlik doğrulaması/güncelleştirme | Tüm uygulama türlerinde kimlik doğrulamasını Güncelleştir |
> | Microsoft. Directory/uygulamalar/temel/güncelleştirme | Uygulamalar için temel özellikleri güncelleştirme |
> | Microsoft. Directory/uygulamalar/kimlik bilgileri/güncelleştirme | Uygulama kimlik bilgilerini güncelleştir |
> | Microsoft. Directory/uygulamalar/sahipler/güncelleştirme | Uygulamaların sahiplerini güncelleştirme |
> | Microsoft. Directory/uygulamalar/izinler/güncelleştirme | Tüm uygulama türlerinde sunulan izinleri ve gerekli izinleri güncelleştirin |
> | Microsoft. Directory/uygulamalar/ilkeler/güncelleştirme | Uygulama ilkelerini güncelleştirme |
> | Microsoft. Directory/kişiler/oluştur | Kişi oluştur |
> | Microsoft. Directory/Contacts/Delete | Kişileri Sil |
> | Microsoft. Directory/Contacts/Basic/Update | Kişilerdeki temel özellikleri Güncelleştir |
> | Microsoft. Directory/Domains/allProperties/allTasks | Etki alanlarını oluşturun ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/gruplar/oluştur | Rol atanabilir gruplar hariç olmak üzere Grup oluşturma |
> | Microsoft. Directory/gruplar/Sil | Rol atanabilir grup hariç olmak üzere grupları sil |
> | Microsoft. Directory/Groups/restore | Silinen grupları geri yükleme |
> | Microsoft. Directory/gruplar/Üyeler/güncelleştirme | Rol atanabilir gruplar hariç olmak üzere grupların üyelerini Güncelleştir |
> | Microsoft. Directory/gruplar/Owners/Update | Rol atanabilir gruplar hariç olmak üzere grupların sahiplerini Güncelleştir |
> | Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2,0 izin verir ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/kuruluş/temel/güncelleştirme | Kuruluştaki temel özellikleri Güncelleştir |
> | Microsoft. Directory/Roleatamaları/allProperties/allTasks | Rol atamaları oluşturun ve silin ve tüm rol atama özelliklerini okuyun ve güncelleştirin |
> | Microsoft. Directory/roleDefinitions/allProperties/allTasks | Rol tanımları oluşturun ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/Scopedrolemembersevk/allProperties/allTasks | Scopedrolemembersevk eden oluşturun ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Update | Hizmet sorumlusu rol atamalarını güncelleştirme |
> | Microsoft. Directory/subscribedSkus/Standard/Read | Aboneliklerdeki temel özellikleri okuyun |
> | Microsoft. Directory/Users/Atamalisansı | Kullanıcı lisanslarını yönetme |
> | Microsoft. Directory/kullanıcılar/oluştur | Kullanıcı ekle |
> | Microsoft. Directory/kullanıcılar/Sil | Kullanıcıları silme |
> | Microsoft. Directory/kullanıcılar/devre dışı bırak | Kullanıcıları devre dışı bırak |
> | Microsoft. Directory/kullanıcılar/etkinleştir | Kullanıcıları etkinleştir |
> | Microsoft. Directory/Users/ınvalidateallrefreshtokens | Kullanıcı yenileme belirteçleri geçersiz kılarak oturum açmaya zorla |
> | Microsoft. Directory/kullanıcılar/geri yükleme | Silinen kullanıcıları geri yükleme |
> | Microsoft. Directory/Users/Basic/Update | Kullanıcıların temel özelliklerini güncelleştirme |
> | Microsoft. Directory/Users/Manager/Update | Kullanıcılar için Güncelleştirme Yöneticisi |
> | Microsoft. Directory/Users/Password/Update | Tüm kullanıcılar için parolaları Sıfırla |
> | Microsoft. Directory/Users/userPrincipalName/Update | Kullanıcıların Kullanıcı asıl adını güncelleştirme |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="password-administrator"></a>Parola Yöneticisi

Bu role sahip olan kullanıcılar, parolaları yönetme yeteneğine sahiptir. Bu rol, hizmet isteklerini yönetme veya hizmet durumunu izleme özelliği vermez. Parola yöneticisinin bir kullanıcının parolasını sıfırlayıp sıfırlayamayacağı, kullanıcının atandığı role bağlıdır. Parola yöneticisinin parolalarını sıfırlayaacağı rollerin listesi için bkz. [parola sıfırlama izinleri](#password-reset-permissions).

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/Users/Password/Update | Tüm kullanıcılar için parolaları Sıfırla |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="power-bi-administrator"></a>Power BI Yöneticisi

Bu role sahip olan kullanıcılar Microsoft Power BI içinde genel izinlere sahiptir, hizmet mevcut olduğunda, destek biletlerini yönetebilir ve hizmet durumunu izleyebilir. [Power BI yönetici rolünü anlama](/power-bi/service-admin-role)hakkında daha fazla bilgi.

> [!NOTE]
> Microsoft Graph API ve Azure AD PowerShell 'de, bu rol "Power BI Hizmet Yöneticisi" olarak tanımlanır. Bu, [Azure Portal](https://portal.azure.com)"Power BI Yöneticisi" dir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |
> | Microsoft. powerApps. PowerBI/allEntities/allTasks | Power BI tüm yönlerini yönetin |

## <a name="power-platform-administrator"></a>Power Platform Yöneticisi

Bu roldeki kullanıcılar, ortamların, PowerApps, akışların ve veri kaybı önleme ilkelerinin tüm yönlerini oluşturabilir ve yönetebilir. Ayrıca, bu role sahip olan kullanıcılar destek biletlerini yönetebilir ve hizmet durumunu izleyebilir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. dynamics365/allEntities/allTasks | Dynamics 365 'in tüm yönlerini yönetme |
> | Microsoft. Flow/Allentitıes/allTasks | Microsoft Power otomatikleştirmenin tüm yönlerini yönetin |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |
> | Microsoft. powerApps/allEntities/allTasks | Power Apps 'in tüm yönlerini yönetin |

## <a name="printer-administrator"></a>Yazıcı Yöneticisi

Bu roldeki kullanıcılar, Evrensel Yazdırma Bağlayıcısı ayarları dahil olmak üzere Microsoft Evrensel Yazdırma çözümünde tüm yazıcı yapılandırmalarının yazıcılarını kaydedebilir ve tüm yönlerini yönetebilir. Tüm temsilci atanmış yazdırma izni isteklerini kabul edebilirler. Yazıcı yöneticilerinin Ayrıca raporları yazdırma erişimi de vardır.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Azure. Print/Allentitıes/allProperties/allTasks | Yazıcı ve bağlayıcılar oluşturup silin ve Microsoft Print 'de tüm özellikleri okuyun ve güncelleştirin |

## <a name="printer-technician"></a>Yazıcı teknisyeni

Bu role sahip kullanıcılar Microsoft Evrensel Yazdırma çözümünde yazıcı kaydedebilir ve yazıcı durumunu yönetebilir. Ayrıca, tüm bağlayıcı bilgilerini okuyabilir. Anahtar görevi bir yazıcı teknisyeni, yazıcılar ve paylaşım yazıcılarında Kullanıcı izinlerini ayarladı.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Azure. Print/bağlayıcılar/allProperties/Read | Microsoft Print 'teki tüm bağlayıcıların özelliklerini okuyun |
> | Microsoft. Azure. Print/Printers/allProperties/Read | Microsoft yazdırma 'da yazıcıların tüm özelliklerini okuyun |
> | Microsoft. Azure. Print/Printers/Register | Microsoft Print 'e yazıcı kaydetme |
> | Microsoft. Azure. Print/Printers/Unregister | Microsoft yazdırma 'da yazıcıların kaydını silme |
> | Microsoft. Azure. Print/Printers/Basic/Update | Microsoft Print 'te yazıcıların temel özelliklerini güncelleştirme |

## <a name="privileged-authentication-administrator"></a>Ayrıcalıklı kimlik doğrulama Yöneticisi

Bu role sahip kullanıcılar, genel yöneticiler dahil olmak üzere herhangi bir kullanıcı için herhangi bir kimlik doğrulama yöntemi (parolalar dahil) ayarlayabilir veya sıfırlayabilir. Ayrıcalıklı kimlik doğrulama yöneticileri, kullanıcıların mevcut parola olmayan kimlik bilgilerine (MFA veya FIDO gibi) yeniden kaydolmasını ve ' cihazda MFA 'yı hatırlamaları için, tüm kullanıcıların bir sonraki oturum açma aşamasında MFA istemini sormasını sağlayabilir.

[Kimlik doğrulama Yöneticisi](#authentication-administrator) rolünün, yönetici rollerine sahip standart kullanıcılar ve kullanıcılar için yeniden kaydolmayı ve Multi-Factor Authentication 'ı zorlama izni vardır.

[Kimlik doğrulama İlkesi yönetici](#authentication-policy-administrator) rolü, her kullanıcının kaydedebileceği ve kullanabileceği yöntemleri belirleyen kiracının kimlik doğrulama yöntemi ilkesini ayarlama iznine sahiptir.

| Rol | Kullanıcının kimlik doğrulama yöntemlerini yönetme | Kullanıcı başına MFA yönetme | MFA ayarlarını yönetme | Kimlik doğrulama yöntemi ilkesini Yönet | Parola koruma ilkesini Yönet |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Kimlik doğrulama Yöneticisi | Bazı kullanıcılar için Evet (yukarıya bakın) | Bazı kullanıcılar için Evet (yukarıya bakın) | Hayır | Hayır | Hayır |
| Ayrıcalıklı kimlik doğrulama Yöneticisi| Tüm kullanıcılar için Evet | Tüm kullanıcılar için Evet | Hayır | Hayır | Hayır |
| Kimlik doğrulama İlkesi Yöneticisi | Hayır | Hayır | Yes | Yes | Yes |

> [!IMPORTANT]
> Bu role sahip kullanıcılar, Azure Active Directory ' nin içindeki ve dışındaki gizli veya özel bilgilere veya kritik yapılandırmaya erişimi olabilecek kişiler için kimlik bilgilerini değiştirebilir. Bir kullanıcının kimlik bilgilerini değiştirmek, kullanıcının kimliğini ve izinlerini kabul etme imkanını ifade edebilir. Örnek:
>
>* Sahip oldukları uygulamaların kimlik bilgilerini yönetebilen uygulama kaydı ve kurumsal uygulama sahipleri. Bu uygulamaların Azure AD 'de ayrıcalıklı izinleri olabilir ve kimlik doğrulama yöneticilerine başka bir yerde izin verilmez. Bu yol aracılığıyla bir kimlik doğrulama Yöneticisi bir uygulama sahibinin kimliğini varsayabilir ve sonra uygulamanın kimlik bilgilerini güncelleştirerek ayrıcalıklı bir uygulamanın kimliğini daha fazla varsayabilir.
>* Azure abonelik sahipleri, hassas veya özel bilgilere veya Azure 'da kritik yapılandırmaya erişebilir.
>* Grup üyeliğini yönetebilen güvenlik grubu ve Grup sahipleri Microsoft 365. Bu gruplar, Azure AD 'de ve başka bir yerde hassas veya özel bilgilere veya kritik yapılandırmaya erişim verebilir.
>* Exchange Online, Office Security ve Uyumluluk Merkezi ve insan kaynakları sistemleri gibi Azure AD dışında diğer hizmetlerde bulunan yöneticiler.
>* Gizli veya özel bilgilere erişebilen Yöneticiler, yasal Counsel ve insan kaynakları çalışanları gibi yönetici olmayanlar.


> [!IMPORTANT]
> Bu rol, şu anda eski MFA yönetim portalındaki Kullanıcı başına MFA 'yı yönetme yeteneğine sahip değil. Aynı işlevler [set-MsolUser](/powershell/module/msonline/set-msoluser) komutunu Azure AD PowerShell modülü kullanılarak gerçekleştirilebilir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/Users/ınvalidateallrefreshtokens | Kullanıcı yenileme belirteçleri geçersiz kılarak oturum açmaya zorla |
> | Microsoft. Directory/Users/strongAuthentication/Update | Kullanıcılar için güçlü kimlik doğrulama özelliğini güncelleştirme |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="privileged-role-administrator"></a>Ayrıcalıklı rol yöneticisi

Bu role sahip kullanıcılar, Azure Active Directory ve Azure AD Privileged Identity Management içindeki rol atamalarını yönetebilir. Azure AD rollerine atanabilecek grupları oluşturabilir ve yönetebilir. Ayrıca, bu rol Privileged Identity Management ve yönetim birimlerinin tüm yönlerinin yönetimine izin verir.

> [!IMPORTANT]
> Bu rol, genel yönetici rolü dahil olmak üzere tüm Azure AD rolleri için atamaları yönetme olanağı verir. Bu rol, Azure AD 'de Kullanıcı oluşturma veya güncelleştirme gibi başka ayrıcalıklı yetenekler içermez. Ancak, bu role atanan kullanıcılar, ek roller atayarak kendilerine veya başkalarına ek ayrıcalık verebilir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/Yönetimtiveunits/allProperties/allTasks | Yönetim birimleri oluşturma ve yönetme (Üyeler dahil) |
> | Microsoft. Directory/Approtaatamalar/allProperties/allTasks | Approlet atamaları oluşturup silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/authorizationPolicy/allProperties/allTasks | Yetkilendirme ilkelerinin tüm yönlerini yönetme |
> | Microsoft. Directory/directoryRoles/allProperties/allTasks | Dizin rolleri oluşturup silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/Groupsastifbletoroles/Create | Rol atanabilir gruplar oluşturma |
> | Microsoft. Directory/Groupsastifbletoroles/Delete | Rol atanabilir grupları sil |
> | Microsoft. Directory/Groupsastifbletoroles/restore | Rol atanabilir grupları geri yükleme |
> | Microsoft. Directory/Groupsastifbletoroles/allProperties/Update | Rol atanabilir grupları Güncelleştir |
> | Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2,0 izin verir ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/Privilegedıdentitymanagement/allProperties/allTasks | Tüm kaynakları oluşturup silin ve Privileged Identity Management içindeki standart özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/Roleatamaları/allProperties/allTasks | Rol atamaları oluşturun ve silin ve tüm rol atama özelliklerini okuyun ve güncelleştirin |
> | Microsoft. Directory/roleDefinitions/allProperties/allTasks | Rol tanımları oluşturun ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/Scopedrolemembersevk/allProperties/allTasks | Scopedrolemembersevk eden oluşturun ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Update | Hizmet sorumlusu rol atamalarını güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/izinleri/güncelleştirme | Hizmet sorumluları 'nın izinlerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/managePermissionGrantsForAll. Microsoft-Company-Admin | Tüm uygulamalar için izin verme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="reports-reader"></a>Rapor okuyucu

Bu role sahip kullanıcılar, Microsoft 365 Yönetim Merkezi 'nde ve Power BI ' de benimseme bağlam paketindeki kullanım raporlama verilerini ve raporlar panosunu görüntüleyebilir. Rol Ayrıca, Azure AD 'de oturum açma raporlarına ve etkinliklere ve Microsoft Graph Raporlama API 'SI tarafından döndürülen verilere erişim sağlar. Rapor okuyucu rolüne atanan bir Kullanıcı yalnızca ilgili kullanım ve benimseme ölçümlerine erişebilir. Ayarları yapılandırmak veya Exchange gibi ürüne özel yönetim merkezlerine erişmek için yönetici izinleri yoktur. Bu rolün destek biletlerini görüntüleme, oluşturma veya yönetme erişimi yok.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/auditLogs/allProperties/Read | Ayrıcalıklı özellikler dahil olmak üzere denetim günlüklerindeki tüm özellikleri okuyun |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Sağlama günlüklerinin tüm özelliklerini okuyun |
> | Microsoft. Directory/Signınreports/allProperties/Read | Ayrıcalıklı özellikler dahil olmak üzere oturum açma raporlarında tüm özellikleri okuyun |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. office365. Network/Performance/allProperties/Read | Microsoft 365 Yönetim Merkezi 'ndeki tüm ağ performansı özelliklerini okuyun |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. usageReports/allEntities/allProperties/Read | Office 365 kullanım raporlarını okuyun |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="search-administrator"></a>Yönetici ara

Bu roldeki kullanıcılar, Microsoft 365 Yönetim Merkezi 'ndeki tüm Microsoft arama yönetimi özelliklerine tam erişime sahiptir. Ayrıca, bu kullanıcılar ileti merkezini görüntüleyebilir, hizmet durumunu izleyebilir ve hizmet istekleri oluşturabilir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. office365. messageCenter/messages/okundu | Microsoft 365 Yönetim Merkezi 'nde Ileti merkezindeki iletileri okuma, güvenlik iletilerini hariç tutma |
> | Microsoft. office365. Search/Content/Manage | İçerik oluşturma ve silme ve Microsoft Search 'te tüm özellikleri okuma ve güncelleştirme |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="search-editor"></a>Arama Düzenleyicisi

Bu roldeki kullanıcılar, Microsoft 365 Yönetim Merkezi 'nde yer işaretleri, Q&as ve konumlar dahil Microsoft Search için içerik oluşturabilir, yönetebilir ve silebilir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. office365. messageCenter/messages/okundu | Microsoft 365 Yönetim Merkezi 'nde Ileti merkezindeki iletileri okuma, güvenlik iletilerini hariç tutma |
> | Microsoft. office365. Search/Content/Manage | İçerik oluşturma ve silme ve Microsoft Search 'te tüm özellikleri okuma ve güncelleştirme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="security-administrator"></a>Güvenlik Yöneticisi

Bu role sahip olan kullanıcılar, Microsoft 365 Güvenlik Merkezi, Azure Active Directory Kimlik Koruması, Azure Active Directory kimlik doğrulaması, Azure Information Protection ve Office 365 güvenlik & Uyumluluk Merkezi 'ndeki güvenlikle ilgili özellikleri yönetme iznine sahiptir. Office 365 izinleri hakkında daha fazla bilgi [Için güvenlik & Uyumluluk Merkezi ' nde izinler](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)bulabilirsiniz.

İçinde | Yapılabilir
--- | ---
[Microsoft 365 Güvenlik Merkezi](https://protection.office.com) | Microsoft 365 hizmetleri arasında güvenlikle ilgili ilkeleri izleme<br>Güvenlik tehditlerini ve uyarıları yönetme<br>Raporları görüntüle
Kimlik koruma Merkezi | Güvenlik okuyucusu rolünün tüm izinleri<br>Ayrıca, parola sıfırlama hariç tüm kimlik koruması merkezi işlemlerini gerçekleştirme özelliği
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Güvenlik okuyucusu rolünün tüm izinleri<br>Azure AD rol atamaları veya **ayarları yönetemez**
[Office 365 güvenlik & Uyumluluk Merkezi](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Güvenlik ilkelerini yönetin<br>Güvenlik tehditlerini görüntüleyin, araştırın ve yanıtlayın<br>Raporları görüntüle
Azure Gelişmiş Tehdit Koruması | Şüpheli güvenlik etkinliğini izleyin ve yanıtlayın
Windows Defender ATP ve EDR | Rolleri atama<br>Makine gruplarını yönetme<br>Uç nokta tehdit algılamasını ve otomatik düzeltmeyi yapılandırma<br>Uyarıları görüntüleyin, araştırın ve yanıtlayın
[Intune](/intune/role-based-access-control) | Kullanıcı, cihaz, kayıt, yapılandırma ve uygulama bilgilerini görüntüler<br>Intune üzerinde değişiklik yapılamıyor
[Cloud App Security](/cloud-app-security/manage-admins) | Yönetici ekleme, ilke ve ayarlar ekleme, günlükleri karşıya yükleme ve idare eylemleri gerçekleştirme
[Azure Güvenlik Merkezi](../../key-vault/managed-hsm/built-in-roles.md) | Güvenlik ilkelerini görüntüleyebilir, güvenlik durumlarını görüntüleyebilir, güvenlik ilkelerini düzenleyebilir, uyarıları ve önerileri görüntüleyebilir, uyarıları ve önerileri kapatabilir
[Microsoft 365 hizmet durumu](/office365/enterprise/view-service-health) | Microsoft 365 hizmetlerinin sistem durumunu görüntüleme
[Akıllı kilitleme](../authentication/howto-password-smart-lockout.md) | Başarısız oturum açma olayları gerçekleştiğinde, loctattatların eşiğini ve süresini tanımlayın.
[Parola koruması](../authentication/concept-password-ban-bad.md) | Özel yasaklanmış parola listesini veya şirket içi parola korumasını yapılandırın.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/uygulamalar/ilkeler/güncelleştirme | Uygulama ilkelerini güncelleştirme |
> | Microsoft. Directory/auditLogs/allProperties/Read | Ayrıcalıklı özellikler dahil olmak üzere denetim günlüklerindeki tüm özellikleri okuyun |
> | Microsoft. Directory/bitlockerKeys/Key/Read | Cihazlarda BitLocker meta verilerini ve anahtarını okuyun |
> | Microsoft. Directory/entitlementManagement/allProperties/Read | Azure AD Yetkilendirme Yönetimi 'ndeki tüm özellikleri okuyun |
> | Microsoft. Directory/ıdentityprotection/allProperties/Read | Azure AD Kimlik Koruması tüm kaynakları okuyun |
> | Microsoft. Directory/ıdentityprotection/allProperties/Update | Azure AD Kimlik Koruması tüm kaynakları güncelleştirme |
> | Microsoft. Directory/policies/Create | Azure AD 'de ilkeler oluşturma |
> | Microsoft. Directory/policies/Delete | Azure AD 'de ilkeleri silme |
> | Microsoft. Directory/policies/Basic/Update | İlkelerdeki temel özellikleri güncelleştirme |
> | Microsoft. Directory/policies/Owners/Update | İlkelerin sahiplerini güncelleştirme |
> | Microsoft. Directory/policies/tenantDefault/Update | Varsayılan kuruluş ilkelerini Güncelleştir |
> | Microsoft. Directory/conditionalAccessPolicies/oluşturma | Koşullu erişim ilkeleri oluşturma |
> | Microsoft. Directory/conditionalAccessPolicies/Delete | Koşullu erişim ilkelerini silme |
> | Microsoft. Directory/conditionalAccessPolicies/standart/okuma | İlkeler için koşullu erişimi okuyun |
> | Microsoft. Directory/conditionalAccessPolicies/sahipler/okuma | Koşullu erişim ilkelerinin sahiplerini okuyun |
> | Microsoft. Directory/conditionalAccessPolicies/policyAppliedTo/Read | Koşullu erişim ilkeleri için "uygulandı" özelliğini okuyun |
> | Microsoft. Directory/conditionalAccessPolicies/Basic/Update | Koşullu erişim ilkeleri için temel özellikleri güncelleştirme |
> | Microsoft. Directory/conditionalAccessPolicies/Owners/Update | Koşullu erişim ilkeleri için sahipleri güncelleştirme |
> | Microsoft. Directory/conditionalAccessPolicies/tenantDefault/Update | Koşullu erişim ilkeleri için varsayılan kiracıyı güncelleştirme |
> | Microsoft. Directory/Privilegedıdentitymanagement/allProperties/Read | Privileged Identity Management tüm kaynakları okuyun |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Sağlama günlüklerinin tüm özelliklerini okuyun |
> | Microsoft. Directory/Servicesorumlularını/ilkeleri/güncelleştirme | Hizmet sorumluları ilkelerini güncelleştirme |
> | Microsoft. Directory/Signınreports/allProperties/Read | Ayrıcalıklı özellikler dahil olmak üzere oturum açma raporlarında tüm özellikleri okuyun |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. protectionCenter/allEntities/standart/okuma | Güvenlik ve uyumluluk merkezlerindeki tüm kaynakların standart özelliklerini okuyun |
> | Microsoft. office365. protectionCenter/allEntities/temel/güncelleştirme | Güvenlik ve uyumluluk merkezlerindeki tüm kaynakların temel özelliklerini güncelleştirme |
> | Microsoft. office365. protectionCenter/saldırıda Ksimülatör/yük/allProperties/allTasks | Saldırı simülatöründe saldırı yüklerini oluşturma ve yönetme |
> | Microsoft. office365. protectionCenter/saldırıda Ksimülatör/raporlar/allProperties/Read | Saldırı simülasyonu yanıtlarının ve ilişkili eğitimin raporlarını okuyun |
> | Microsoft. office365. protectionCenter/saldırıda Ksimülatör/simülasyon/allProperties/allTasks | Saldırı simülasyonu 'nda saldırı simülasyonu şablonları oluşturma ve yönetme |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="security-operator"></a>Güvenlik operatörü

Bu role sahip kullanıcılar, uyarıları yönetebilir ve güvenlikle ilgili özellikler üzerinde Microsoft 365 Güvenlik Merkezi, Azure Active Directory, kimlik koruması Privileged Identity Management ve Office 365 güvenlik & Uyumluluk Merkezi 'ndeki tüm bilgiler dahil olmak üzere genel salt okuma erişimine sahip olabilir. Office 365 izinleri hakkında daha fazla bilgi [Için güvenlik & Uyumluluk Merkezi ' nde izinler](/office365/securitycompliance/permissions-in-the-security-and-compliance-center)bulabilirsiniz.

İçinde | Yapılabilir
--- | ---
[Microsoft 365 Güvenlik Merkezi](https://protection.office.com) | Güvenlik okuyucusu rolünün tüm izinleri<br>Güvenlik tehditleri uyarılarını görüntüleyin, araştırın ve yanıtlayın
Azure AD Kimlik Koruması | Güvenlik okuyucusu rolünün tüm izinleri<br>Ayrıca, parolaları sıfırlama ve uyarı e-postalarını yapılandırma dışında tüm kimlik koruması merkezi işlemlerini gerçekleştirme özelliği de vardır.
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Güvenlik okuyucusu rolünün tüm izinleri
[Office 365 güvenlik & Uyumluluk Merkezi](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Güvenlik okuyucusu rolünün tüm izinleri<br>Güvenlik uyarılarını görüntüleyin, araştırın ve yanıtlayın
Windows Defender ATP ve EDR | Güvenlik okuyucusu rolünün tüm izinleri<br>Güvenlik uyarılarını görüntüleyin, araştırın ve yanıtlayın
[Intune](/intune/role-based-access-control) | Güvenlik okuyucusu rolünün tüm izinleri
[Cloud App Security](/cloud-app-security/manage-admins) | Güvenlik okuyucusu rolünün tüm izinleri
[Microsoft 365 hizmet durumu](/office365/enterprise/view-service-health) | Microsoft 365 hizmetlerinin sistem durumunu görüntüleme

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/auditLogs/allProperties/Read | Ayrıcalıklı özellikler dahil olmak üzere denetim günlüklerindeki tüm özellikleri okuyun |
> | Microsoft. Directory/cloudAppSecurity/allProperties/allTasks | Tüm kaynakları oluşturup silin ve Microsoft Cloud App Security içindeki standart özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/ıdentityprotection/allProperties/allTasks | Tüm kaynakları oluşturup silin ve Azure AD Kimlik Koruması içindeki standart özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/Privilegedıdentitymanagement/allProperties/Read | Privileged Identity Management tüm kaynakları okuyun |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Sağlama günlüklerinin tüm özelliklerini okuyun |
> | Microsoft. Directory/Signınreports/allProperties/Read | Ayrıcalıklı özellikler dahil olmak üzere oturum açma raporlarında tüm özellikleri okuyun |
> | Microsoft. Azure. advancedThreatProtection/Allentitıes/allTasks | Azure Gelişmiş tehdit koruması 'nın tüm yönlerini yönetin |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. Intune/allEntities/okuma | Microsoft Intune tüm kaynakları okuyun |
> | Microsoft. office365. Securityzorluk Ancecenter/allEntities/allTasks | Tüm kaynakları oluşturup silin ve Microsoft 365 güvenlik ve Uyumluluk Merkezi 'nde standart özellikleri okuyun ve güncelleştirin |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. Windows. savunma Deradvancedthreatprotection/allEntities/allTasks | Endpoint için Microsoft Defender 'ın tüm yönlerini yönetin |

## <a name="security-reader"></a>Güvenlik okuyucusu

Bu role sahip olan kullanıcılar, Microsoft 365 Güvenlik Merkezi, Azure Active Directory, kimlik koruması, Privileged Identity Management ve Azure Active Directory oturum açma raporlarını ve denetim günlüklerini okuma ve Office 365 güvenlik & Uyumluluk Merkezi 'ndeki tüm bilgiler dahil olmak üzere güvenlikle ilgili özellik üzerinde genel salt okuma erişimine sahiptir. Office 365 izinleri hakkında daha fazla bilgi [Için güvenlik & Uyumluluk Merkezi ' nde izinler](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)bulabilirsiniz.

İçinde | Yapılabilir
--- | ---
[Microsoft 365 Güvenlik Merkezi](https://protection.office.com) | Microsoft 365 hizmetleri arasında güvenlikle ilgili ilkeleri görüntüleme<br>Güvenlik tehditlerini ve uyarılarını görüntüleme<br>Raporları görüntüle
Kimlik koruma Merkezi | Güvenlik özellikleri için tüm güvenlik raporlarını ve ayar bilgilerini okuyun<br><ul><li>İstenmeyen postadan koruma<li>Şifreleme<li>Veri kaybını önleme<li>Kötü amaçlı yazılımdan koruma<li>Gelişmiş tehdit koruması<li>Kimlik avlayan koruma<li>Posta akışı kuralları
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Azure AD Privileged Identity Management ' de ortaya çıkacak tüm bilgilere salt okuma erişimi vardır: Azure AD rol atamaları ve güvenlik incelemeleri için Ilkeler ve raporlar.<br>Azure AD Privileged Identity Management için **kaydolabilir veya** herhangi bir değişiklik yapabilirsiniz. Privileged Identity Management portalında veya PowerShell aracılığıyla, bu roldeki birisi, Kullanıcı için uygun olması durumunda ek rolleri (örneğin, genel yönetici veya ayrıcalıklı rol yöneticisi) etkinleştirebilir.
[Office 365 güvenlik & Uyumluluk Merkezi](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Güvenlik ilkelerini görüntüleme<br>Güvenlik tehditlerini görüntüle ve araştır<br>Raporları görüntüle
Windows Defender ATP ve EDR | Uyarıları görüntüleyin ve araştırın. Windows Defender ATP 'de rol tabanlı erişim denetimini açtığınızda, Azure AD güvenlik okuyucusu rolü gibi salt okuma izinlerine sahip kullanıcılar bir Windows Defender ATP rolüne atanana kadar erişimi kaybeder.
[Intune](/intune/role-based-access-control) | Kullanıcı, cihaz, kayıt, yapılandırma ve uygulama bilgilerini görüntüler. Intune’da değişiklik yapamaz.
[Cloud App Security](/cloud-app-security/manage-admins) | Salt okuma izinlerine sahiptir ve uyarıları yönetebilir
[Azure Güvenlik Merkezi](../../key-vault/managed-hsm/built-in-roles.md) | Önerileri ve uyarıları görüntüleyebilir, güvenlik ilkelerini görüntüleyebilir, güvenlik durumlarını görüntüleyebilir, ancak değişiklik yapamaz
[Microsoft 365 hizmet durumu](/office365/enterprise/view-service-health) | Microsoft 365 hizmetlerinin sistem durumunu görüntüleme

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/auditLogs/allProperties/Read | Ayrıcalıklı özellikler dahil olmak üzere denetim günlüklerindeki tüm özellikleri okuyun |
> | Microsoft. Directory/bitlockerKeys/Key/Read | Cihazlarda BitLocker meta verilerini ve anahtarını okuyun |
> | Microsoft. Directory/entitlementManagement/allProperties/Read | Azure AD Yetkilendirme Yönetimi 'ndeki tüm özellikleri okuyun |
> | Microsoft. Directory/ıdentityprotection/allProperties/Read | Azure AD Kimlik Koruması tüm kaynakları okuyun |
> | Microsoft. Directory/policies/standart/Read | İlkelerdeki temel özellikleri okuyun |
> | Microsoft. Directory/policies/Owners/Read | İlkelerin sahiplerini okuma |
> | Microsoft. Directory/policies/policyAppliedTo/Read | Policies. policyAppliedTo özelliğini okuyun |
> | Microsoft. Directory/conditionalAccessPolicies/standart/okuma | İlkeler için koşullu erişimi okuyun |
> | Microsoft. Directory/conditionalAccessPolicies/sahipler/okuma | Koşullu erişim ilkelerinin sahiplerini okuyun |
> | Microsoft. Directory/conditionalAccessPolicies/policyAppliedTo/Read | Koşullu erişim ilkeleri için "uygulandı" özelliğini okuyun |
> | Microsoft. Directory/Privilegedıdentitymanagement/allProperties/Read | Privileged Identity Management tüm kaynakları okuyun |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Sağlama günlüklerinin tüm özelliklerini okuyun |
> | Microsoft. Directory/Signınreports/allProperties/Read | Ayrıcalıklı özellikler dahil olmak üzere oturum açma raporlarında tüm özellikleri okuyun |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. office365. protectionCenter/allEntities/standart/okuma | Güvenlik ve uyumluluk merkezlerindeki tüm kaynakların standart özelliklerini okuyun |
> | Microsoft. office365. protectionCenter/saldırıda Ksimülatör/yük/allProperties/Read | Saldırı simülatörü içindeki saldırı yüklerin tüm özelliklerini okuyun |
> | Microsoft. office365. protectionCenter/saldırıda Ksimülatör/raporlar/allProperties/Read | Saldırı simülasyonu yanıtlarının ve ilişkili eğitimin raporlarını okuyun |
> | Microsoft. office365. protectionCenter/saldırıda Ksimülatör/simülasyon/allProperties/Read | Saldırı simülatörü içindeki saldırı simülasyonu şablonlarının tüm özelliklerini okuyun |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="service-support-administrator"></a>Hizmet desteği Yöneticisi

Bu role sahip kullanıcılar, Azure ve Microsoft 365 hizmetleri için Microsoft ile destek istekleri açabilir ve [Azure Portal](https://portal.azure.com) ve [Microsoft 365 Yönetim Merkezi](https://admin.microsoft.com)'nde hizmet panosu ve ileti merkezini görüntüler. [Yönetici rolleri hakkında](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)daha fazla bilgi.

> [!NOTE]
> Daha önce bu rol, [Azure Portal](https://portal.azure.com) ve [Microsoft 365 Yönetim Merkezi](https://admin.microsoft.com)'nde "Hizmet Yöneticisi" olarak adlandırılmıştı. Microsoft Graph API 'SI, Azure AD Graph API ve Azure AD PowerShell 'de bulunan adı ile uyum sağlamak için bunu "hizmet desteği Yöneticisi" olarak yeniden adlandırdık.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. Network/Performance/allProperties/Read | Microsoft 365 Yönetim Merkezi 'ndeki tüm ağ performansı özelliklerini okuyun |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="sharepoint-administrator"></a>SharePoint Yöneticisi

Bu role sahip olan kullanıcılar Microsoft SharePoint Online içinde genel izinlere sahiptir, hizmet mevcut olduğunda, tüm Microsoft 365 grupları oluşturup yönetebilir, destek biletlerini yönetebilir ve hizmet durumunu izleyebilir. [Yönetici rolleri hakkında](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)daha fazla bilgi.

> [!NOTE]
> Microsoft Graph API ve Azure AD PowerShell 'de, bu rol "SharePoint Hizmet Yöneticisi" olarak tanımlanır. Bu, [Azure Portal](https://portal.azure.com)"SharePoint Yöneticisi" dir.

> [!NOTE]
> Bu rol Ayrıca, Microsoft Intune için Microsoft Graph API 'sine kapsamlı izinler vererek SharePoint ve OneDrive kaynaklarıyla ilgili ilkelerin yönetimine ve yapılandırılmasına izin verir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/groups. Unified/Create | Rol atanabilir grupların dışlamasıyla birlikte Microsoft 365 grupları oluşturun |
> | Microsoft. Directory/groups. Unified/Delete | Rol atanabilir grupların dışlamasıyla Microsoft 365 gruplarını sil |
> | Microsoft. Directory/groups. Unified/restore | Microsoft 365 gruplarını geri yükleme |
> | Microsoft. Directory/groups. Unified/Basic/Update | Rol atanabilir grupların dışlamasıyla Microsoft 365 gruplardaki temel özellikleri Güncelleştir |
> | Microsoft. Directory/groups. Unified/Members/Update | Microsoft 365 gruplarının üyelerini rol atanabilir grupların dışlamasıyla Güncelleştir |
> | Microsoft. Directory/groups. Unified/Owners/Update | Rol atanabilir grupların dışlamasıyla Microsoft 365 gruplarının sahiplerini güncelleştirme |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. Network/Performance/allProperties/Read | Microsoft 365 Yönetim Merkezi 'ndeki tüm ağ performansı özelliklerini okuyun |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. sharePoint/allEntities/allTasks | Tüm kaynakları oluşturup silin ve SharePoint 'te standart özellikleri okuyun ve güncelleştirin |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. usageReports/allEntities/allProperties/Read | Office 365 kullanım raporlarını okuyun |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="skype-for-business-administrator"></a>Skype Kurumsal Yöneticisi

Bu role sahip olan kullanıcılar, hizmet mevcut olduğunda, Microsoft Skype Kurumsal içinde genel izinlere sahiptir ve ayrıca Azure Active Directory Skype 'e özgü kullanıcı özniteliklerini yönetir. Ayrıca, bu rol, destek biletlerini yönetme ve hizmet durumunu izleme ve takımlar ve Skype Kurumsal yönetim merkezine erişme olanağı verir. Hesap Ayrıca takımlar için lisanslanmış olmalıdır veya takımlar PowerShell cmdlet 'lerini çalıştıramıyor. Skype Kurumsal [yönetici rolü](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) ve [Skype Kurumsal ve Microsoft ekipleri eklenti lisanslaması](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing) hakkında daha fazla bilgi

> [!NOTE]
> Microsoft Graph API ve Azure AD PowerShell 'de, bu rol "Lync Hizmet Yöneticisi" olarak tanımlanır. Bu, [Azure Portal](https://portal.azure.com/)"Skype Kurumsal Yöneticisi" dir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Sktypeınfo/allEntities/allTasks | Skype Kurumsal Çevrimiçi 'nin tüm yönlerini yönetin |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. usageReports/allEntities/allProperties/Read | Office 365 kullanım raporlarını okuyun |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="teams-administrator"></a>Takımlar Yöneticisi

Bu roldeki kullanıcılar Microsoft ekipleri & Skype Kurumsal Yönetici Merkezi ve ilgili PowerShell modülleri aracılığıyla Microsoft ekipleri iş yükünün tüm yönlerini yönetebilir. Bu, diğer alanların yanı sıra telefon, mesajlaşma, toplantılar ve takımların kendileri ile ilgili tüm yönetim araçlarını içerir. Bu rol Ayrıca, tüm Microsoft 365 grupları oluşturma ve yönetme, destek biletlerini yönetme ve hizmet durumunu izleme özelliğini verir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/Groups/hiddenMembers/Read | Bir grubun gizli üyelerini oku |
> | Microsoft. Directory/groups. Unified/Create | Rol atanabilir grupların dışlamasıyla birlikte Microsoft 365 grupları oluşturun |
> | Microsoft. Directory/groups. Unified/Delete | Rol atanabilir grupların dışlamasıyla Microsoft 365 gruplarını sil |
> | Microsoft. Directory/groups. Unified/restore | Microsoft 365 gruplarını geri yükleme |
> | Microsoft. Directory/groups. Unified/Basic/Update | Rol atanabilir grupların dışlamasıyla Microsoft 365 gruplardaki temel özellikleri Güncelleştir |
> | Microsoft. Directory/groups. Unified/Members/Update | Microsoft 365 gruplarının üyelerini rol atanabilir grupların dışlamasıyla Güncelleştir |
> | Microsoft. Directory/groups. Unified/Owners/Update | Rol atanabilir grupların dışlamasıyla Microsoft 365 gruplarının sahiplerini güncelleştirme |
> | Microsoft. Directory/Servicesorumlularını/managePermissionGrantsForGroup. Microsoft-All-Application-Permissions | Hizmet sorumlusu bir grubun verilerine doğrudan erişim verme |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. Network/Performance/allProperties/Read | Microsoft 365 Yönetim Merkezi 'ndeki tüm ağ performansı özelliklerini okuyun |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Sktypeınfo/allEntities/allTasks | Skype Kurumsal Çevrimiçi 'nin tüm yönlerini yönetin |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. usageReports/allEntities/allProperties/Read | Office 365 kullanım raporlarını okuyun |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |
> | Microsoft. ekipler/allEntities/allProperties/allTasks | Ekipteki tüm kaynakları yönetme |

## <a name="teams-communications-administrator"></a>Takımlar Iletişim Yöneticisi

Bu roldeki kullanıcılar, Voice & telefonlarıyla ilgili Microsoft ekipleri iş yükünün yönlerini yönetebilir. Buna telefon numarası atama, sesli ve toplantı ilkelerinin yönetim araçları ve çağrı Analizi araç takımını tam erişim dahildir.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Sktypeınfo/allEntities/allTasks | Skype Kurumsal Çevrimiçi 'nin tüm yönlerini yönetin |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. usageReports/allEntities/allProperties/Read | Office 365 kullanım raporlarını okuyun |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |
> | Microsoft. ekipler/callQuality/allProperties/Read | Çağrı kalitesi panosundaki tüm verileri oku (CQD) |
> | Microsoft. ekipler/toplantılar/allProperties/allTasks | Toplantı ilkeleri, konfigürasyonlar ve konferans köprüleri dahil olmak üzere toplantıları yönetme |
> | Microsoft. ekipler/ses/allProperties/allTasks | Arama ilkeleri ve telefon numarası envanteri ve atama dahil olmak üzere seslendirmeyi yönetme |

## <a name="teams-communications-support-engineer"></a>Takımlar Iletişimleri Destek Mühendisi

Bu roldeki kullanıcılar, Microsoft ekiplerinde Skype Kurumsal Yönetim Merkezi & Kullanıcı çağrısı sorun giderme araçlarını kullanarak Microsoft & ekiplerinde bulunan iletişim sorunlarını giderebilir. Bu roldeki kullanıcılar, dahil edilen tüm katılımcılar için tam çağrı kaydı bilgilerini görüntüleyebilir. Bu rolün destek biletlerini görüntüleme, oluşturma veya yönetme erişimi yok.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Sktypeınfo/allEntities/allTasks | Skype Kurumsal Çevrimiçi 'nin tüm yönlerini yönetin |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |
> | Microsoft. ekipler/callQuality/allProperties/Read | Çağrı kalitesi panosundaki tüm verileri oku (CQD) |

## <a name="teams-communications-support-specialist"></a>Takımlar Iletişimleri destek uzmanı

Bu roldeki kullanıcılar, Microsoft ekiplerinde Skype Kurumsal Yönetim Merkezi & Kullanıcı çağrısı sorun giderme araçlarını kullanarak Microsoft & ekiplerinde bulunan iletişim sorunlarını giderebilir. Bu roldeki kullanıcılar, yalnızca baktıkları belirli kullanıcı için yapılan çağrıda Kullanıcı ayrıntılarını görüntüleyebilir. Bu rolün destek biletlerini görüntüleme, oluşturma veya yönetme erişimi yok.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Sktypeınfo/allEntities/allTasks | Skype Kurumsal Çevrimiçi 'nin tüm yönlerini yönetin |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |
> | Microsoft. ekipler/callQuality/standart/okuma | Çağrı kalitesi panosundaki temel verileri okuma (CQD) |

## <a name="teams-devices-administrator"></a>Takımlar cihazları Yöneticisi

Bu role sahip kullanıcılar takımlar [tarafından sertifikalı cihazları](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) takımlar yönetim merkezinden yönetebilir. Bu rol, cihazları arama ve filtreleme özelliği sayesinde tüm cihazların tek bakışta görüntülenmesine izin verir. Kullanıcı, cihazın oturum açma hesabı, marka ve modeli dahil olmak üzere her bir cihazın ayrıntılarını denetleyebilir. Kullanıcı cihazdaki ayarları değiştirebilir ve yazılım sürümlerini güncelleştirebilir. Bu rol, ekip etkinliğini denetlemek ve cihazın kalitesini çağırmak için izinler vermez.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |
> | Microsoft. takımlar/cihazlar/standart/okuma | Yapılandırma ilkeleri de dahil olmak üzere ekiplerin sertifikalı cihazların tüm yönlerini yönetin |

## <a name="usage-summary-reports-reader"></a>Kullanım Özeti raporları okuyucusu

Bu role sahip kullanıcılar, kullanım ve üretkenlik puanı için Microsoft 365 Yönetim Merkezi 'nde kiracı düzeyindeki toplu verilere ve ilişkili öngörülere erişebilir, ancak herhangi bir Kullanıcı düzeyi ayrıntılarına veya öngörülere erişemez. İki rapor için Microsoft 365 Yönetim Merkezi 'nde, kiracı düzeyi toplanmış veriler ve Kullanıcı düzeyi ayrıntıları arasında ayrım yaptık. Bu rol, hem müşteriler hem de yasal takımlar tarafından istenen bireysel kullanıcı tarafından tanımlanabilen veriler üzerinde ek bir koruma katmanı sağlar.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. office365. Network/Performance/allProperties/Read | Microsoft 365 Yönetim Merkezi 'ndeki tüm ağ performansı özelliklerini okuyun |
> | Microsoft. office365. usageReports/allEntities/standart/okuma | Kiracı düzeyinde toplanmış Office 365 kullanım raporlarını okuyun |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="user-administrator"></a>Kullanıcı Yöneticisi

Bu role sahip kullanıcılar Kullanıcı oluşturabilir ve bazı kısıtlamalara sahip kullanıcıların tüm yönlerini yönetebilir (tabloya bakın) ve parola süre sonu ilkelerini güncelleştirebilir. Ayrıca, bu role sahip kullanıcılar tüm grupları oluşturabilir ve yönetebilir. Bu rol Ayrıca Kullanıcı görünümleri oluşturma ve yönetme, destek biletlerini yönetme ve hizmet durumunu izleme özelliğini de içerir. Kullanıcı yöneticilerinin çoğu yönetici rolünde kullanıcılar için bazı kullanıcı özelliklerini yönetme izni yoktur. Bu role sahip olan kullanıcının MFA 'yı yönetme izni yok. Bu kısıtlamanın özel durumları olan roller aşağıdaki tabloda listelenmiştir.

| Kullanıcı Yöneticisi izni | Notlar |
| --- | --- |
| Kullanıcı ve grup oluşturma<br/>Kullanıcı görünümleri oluşturma ve yönetme<br/>Office destek biletlerini yönetme<br/>Parola süre sonu ilkelerini Güncelleştir |  |
| Lisansları yönetme<br/>Kullanıcı asıl adı dışındaki tüm kullanıcı özelliklerini yönet | Tüm yöneticiler dahil tüm kullanıcılar için geçerlidir |
| Sil ve geri yükle<br/>Devre dışı bırak ve Etkinleştir<br/>Kullanıcı asıl adı dahil tüm kullanıcı özelliklerini yönet<br/>Güncelleştirme (FIDO) cihaz anahtarları | Yönetici olmayan veya aşağıdaki rollerden hiçbirinde olan kullanıcılar için geçerlidir:<ul><li>Yardım Masası Yöneticisi</li><li>Rolü olmayan Kullanıcı</li><li>Kullanıcı Yöneticisi</li></ul> |
| Yenileme belirteçlerini geçersiz kıl<br/>Parola sıfırlama | Bir Kullanıcı yöneticisinin parola sıfırlayıp yenileme belirteçlerini geçersiz kılabileceği rollerin listesi için bkz. [parola sıfırlama izinleri](#password-reset-permissions). |

> [!IMPORTANT]
> Bu role sahip kullanıcılar, Azure Active Directory ' nin içindeki ve dışındaki gizli veya özel bilgilere veya kritik yapılandırmaya erişimi olabilecek kişilerin parolalarını değiştirebilir. Bir kullanıcının parolasını değiştirmek, kullanıcının kimliğini ve izinlerini kabul etme imkanını ifade edebilir. Örnek:
>
>- Sahip oldukları uygulamaların kimlik bilgilerini yönetebilen uygulama kaydı ve kurumsal uygulama sahipleri. Bu uygulamaların Azure AD 'de ayrıcalıklı izinleri olabilir ve Kullanıcı yöneticilerine başka bir yerde izin verilmez. Bu yoldan, bir Kullanıcı Yöneticisi bir uygulama sahibinin kimliğini varsayabilir ve daha sonra uygulamanın kimlik bilgilerini güncelleştirerek ayrıcalıklı bir uygulamanın kimliğini daha fazla varsayabilir.
>- Azure abonelik sahipleri, hassas veya özel bilgilere veya Azure 'da kritik yapılandırmaya erişebilir.
>- Grup üyeliğini yönetebilen güvenlik grubu ve Grup sahipleri Microsoft 365. Bu gruplar, Azure AD 'de ve başka bir yerde hassas veya özel bilgilere veya kritik yapılandırmaya erişim verebilir.
>- Exchange Online, Office Security ve Uyumluluk Merkezi ve insan kaynakları sistemleri gibi Azure AD dışında diğer hizmetlerde bulunan yöneticiler.
>- Gizli veya özel bilgilere erişebilen Yöneticiler, yasal Counsel ve insan kaynakları çalışanları gibi yönetici olmayanlar.

> [!div class="mx-tableFixed"]
> | Eylemler | Açıklama |
> | --- | --- |
> | Microsoft. Directory/Approtaatamalar/oluşturma | Uygulama rolü atamaları oluşturma |
> | Microsoft. Directory/Approtaatamaları/silme | Uygulama rolü atamalarını silme |
> | Microsoft. Directory/Approtaatamalar/temel/güncelleştirme | Uygulama rolü atamalarının temel özelliklerini güncelleştir |
> | Microsoft. Directory/kişiler/oluştur | Kişi oluştur |
> | Microsoft. Directory/Contacts/Delete | Kişileri Sil |
> | Microsoft. Directory/Contacts/Basic/Update | Kişilerdeki temel özellikleri Güncelleştir |
> | Microsoft. Directory/entitlementManagement/allProperties/allTasks | Kaynakları oluşturup silin ve Azure AD Yetkilendirme Yönetimi 'nde tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/Groups/Atamalisansı | Grup tabanlı lisanslama için gruplara ürün lisansları atama |
> | Microsoft. Directory/gruplar/oluştur | Rol atanabilir gruplar hariç olmak üzere Grup oluşturma |
> | Microsoft. Directory/gruplar/Sil | Rol atanabilir grup hariç olmak üzere grupları sil |
> | Microsoft. Directory/Groups/hiddenMembers/Read | Bir grubun gizli üyelerini oku |
> | Microsoft. Directory/Groups/Reprocesslicenseatama | Grup tabanlı lisanslama için lisans atamalarını yeniden işleme |
> | Microsoft. Directory/Groups/restore | Silinen grupları geri yükleme |
> | Microsoft. Directory/Groups/Basic/Update | Rol atanabilir gruplar hariç olmak üzere gruplardaki temel özellikleri Güncelleştir |
> | Microsoft. Directory/gruplar/sınıflandırma/güncelleştirme | Rol atanabilir gruplar hariç olmak üzere grupların sınıflandırma özelliğini güncelleştirme |
> | Microsoft. Directory/Groups/dynamicMembershipRule/Update | Rol atanabilir gruplar hariç olmak üzere grupların dinamik üyelik kuralını Güncelleştir |
> | Microsoft. Directory/gruplar/groupType/Update | Grup için groupType özelliğini güncelleştirme |
> | Microsoft. Directory/gruplar/Üyeler/güncelleştirme | Rol atanabilir gruplar hariç olmak üzere grupların üyelerini Güncelleştir |
> | Microsoft. Directory/Groups/Onpremgeri yazma/güncelleştirme | Azure AD Connect ile şirket içine geri yazılacak Azure Active Directory grupları güncelleştirme |
> | Microsoft. Directory/gruplar/Owners/Update | Rol atanabilir gruplar hariç olmak üzere grupların sahiplerini Güncelleştir |
> | Microsoft. Directory/Groups/Settings/Update | Grupların ayarlarını Güncelleştir |
> | Microsoft. Directory/gruplar/görünürlük/güncelleştirme | Grupların görünürlük özelliğini güncelleştirme |
> | Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2,0 izin verir ve silin ve tüm özellikleri okuyun ve güncelleştirin |
> | Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Update | Hizmet sorumlusu rol atamalarını güncelleştirme |
> | Microsoft. Directory/Users/Atamalisansı | Kullanıcı lisanslarını yönetme |
> | Microsoft. Directory/kullanıcılar/oluştur | Kullanıcı ekle |
> | Microsoft. Directory/kullanıcılar/Sil | Kullanıcıları silme |
> | Microsoft. Directory/kullanıcılar/devre dışı bırak | Kullanıcıları devre dışı bırak |
> | Microsoft. Directory/kullanıcılar/etkinleştir | Kullanıcıları etkinleştir |
> | Microsoft. Directory/Users/Davetteguest | Konuk kullanıcı davet etme |
> | Microsoft. Directory/Users/ınvalidateallrefreshtokens | Kullanıcı yenileme belirteçleri geçersiz kılarak oturum açmaya zorla |
> | Microsoft. Directory/Users/Reprocesslicenseatama | Kullanıcılar için lisans atamalarını yeniden işleme |
> | Microsoft. Directory/kullanıcılar/geri yükleme | Silinen kullanıcıları geri yükleme |
> | Microsoft. Directory/Users/Basic/Update | Kullanıcıların temel özelliklerini güncelleştirme |
> | Microsoft. Directory/Users/Manager/Update | Kullanıcılar için Güncelleştirme Yöneticisi |
> | Microsoft. Directory/Users/Password/Update | Tüm kullanıcılar için parolaları Sıfırla |
> | Microsoft. Directory/Users/userPrincipalName/Update | Kullanıcıların Kullanıcı asıl adını güncelleştirme |
> | Microsoft. Azure. serviceHealth/Allentitıes/allTasks | Azure hizmet durumunu okuma ve yapılandırma |
> | Microsoft. Azure. Supportbilet/allEntities/allTasks | Azure destek biletleri oluşturma ve yönetme |
> | Microsoft. office365. serviceHealth/allEntities/allTasks | Microsoft 365 Yönetim merkezinde hizmet durumunu okuyun ve yapılandırın |
> | Microsoft. office365. Supportbilet/allEntities/allTasks | Microsoft 365 hizmet istekleri oluşturma ve yönetme |
> | Microsoft. office365. webPortal/allEntities/standart/okuma | Microsoft 365 Yönetim Merkezi 'ndeki tüm kaynaklardaki temel özellikleri okuyun |

## <a name="how-to-understand-role-permissions"></a>Rol izinlerini anlama

İzinlerin şeması, Microsoft Graph REST biçimini esnek şekilde izler:

`<namespace>/<entity>/<propertySet>/<action>`

Örnek:

`microsoft.directory/applications/credentials/update`

| İzin öğesi | Description |
| --- | --- |
| ad alanı | Görevi kullanıma sunan ürün veya hizmet, ve ile sona erer `microsoft` . Örneğin, Azure AD 'deki tüm görevler `microsoft.directory` ad alanını kullanır. |
| varlık | Microsoft Graph ' de hizmet tarafından kullanıma sunulan mantıksal özellik veya bileşen. Örneğin, Azure AD Kullanıcı ve grupları kullanıma sunarsa, OneNote Notları kullanıma sunar ve Exchange posta kutularını ve takvimleri kullanıma sunar. `allEntities`Bir ad alanındaki tüm varlıkların belirtilmesine yönelik özel bir anahtar sözcük vardır. Bu, genellikle ürüne erişim izni veren rollerde kullanılır. |
| propertySet | Erişim izni verilen varlığın belirli özellikleri veya yönleri. Örneğin, `microsoft.directory/applications/authentication/read` Azure AD 'de uygulama nesnesinde yanıt URL 'si, oturum kapatma URL 'si ve örtük akış özelliğini okuma yeteneği verir.<ul><li>`allProperties` ayrıcalıklı özellikler dahil olmak üzere varlığın tüm özelliklerini belirler.</li><li>`standard` ortak özellikleri belirtir, ancak eyleme ilişkin ayrıcalıklı olanları dışlar `read` . Örneğin, `microsoft.directory/user/standard/read` genel telefon numarası ve e-posta adresi gibi standart özellikleri okuma, ancak çok faktörlü kimlik doğrulaması için kullanılan özel ikincil telefon numarası veya e-posta adresi gibi standart özellikleri okuma özelliği de dahildir.</li><li>`basic` ortak özellikleri belirtir, ancak eylemle ilgili ayrıcalıklı olanları dışlar `update` . Okuyabilmeniz gereken özellikler kümesi, güncelleştirebileceğinize göre farklı olabilir. Bu nedenle, bunun nedeni `standard` ve `basic` Bu yüzden bunu yansıtan anahtar sözcükler vardır.</li></ul> |
| action | Verilen işlem, en genellikle oluşturma, okuma, güncelleştirme veya silme (CRUD). `allTasks`Yukarıdaki tüm becerileri (oluşturma, okuma, güncelleştirme ve silme) belirtmek için özel bir anahtar sözcük vardır. |

## <a name="deprecated-roles"></a>Kullanım dışı roller

Aşağıdaki roller kullanılmamalıdır. Bunlar kullanım dışı bırakılmıştır ve gelecekte Azure AD 'den kaldırılacaktır.

* Geçici Lisans Yöneticisi
* Cihaz katılımı
* Cihaz yöneticileri
* Cihaz kullanıcıları
* E-posta doğrulanan kullanıcı Oluşturucu
* Posta kutusu Yöneticisi
* Çalışma alanına cihaz katılımı

## <a name="roles-not-shown-in-the-portal"></a>Portalda gösterilen roller

PowerShell veya MS Graph API tarafından döndürülen her rol Azure portal görünür. Aşağıdaki tabloda bu farklılıklar düzenlerler.

API adı | Azure portal adı | Notlar
-------- | ------------------- | -------------
Cihaz katılımı | Kullanım Dışı | [Kullanım dışı roller belgeleri](#deprecated-roles)
Cihaz yöneticileri | Kullanım Dışı | [Kullanım dışı roller belgeleri](#deprecated-roles)
Cihaz kullanıcıları | Kullanım Dışı | [Kullanım dışı roller belgeleri](#deprecated-roles)
Dizin eşitleme hesapları | Kullanılmaması nedeniyle gösterilmez | [Dizin eşitleme hesapları belgeleri](#directory-synchronization-accounts)
Konuk Kullanıcı | Kullanılmadığından gösterilmez | NA
İş ortağı katman 1 desteği | Kullanılmaması nedeniyle gösterilmez | [İş ortağı Katman1 Destek belgeleri](#partner-tier1-support)
İş ortağı katman 2 desteği | Kullanılmaması nedeniyle gösterilmez | [İş ortağı Katman2 Destek belgeleri](#partner-tier2-support)
Kısıtlı Konuk Kullanıcı | Kullanılmadığından gösterilmez | NA
Kullanıcı | Kullanılmadığından gösterilmez | NA
Çalışma alanına cihaz katılımı | Kullanım Dışı | [Kullanım dışı roller belgeleri](#deprecated-roles)

## <a name="password-reset-permissions"></a>Parola sıfırlama izinleri

Sütun başlıkları, parolaları sıfırlayabilirler rollerini temsil eder. Tablo satırlarında, parolasının sıfırlayabileceği roller bulunur.

Parola sıfırlanabilir | Parola Yöneticisi | Yardım Masası Yöneticisi | Kimlik doğrulama Yöneticisi | Kullanıcı Yöneticisi | Ayrıcalıklı kimlik doğrulama Yöneticisi | Genel Yönetici
------ | ------ | ------ | ------ | ------ | ------ | ------
Kimlik doğrulama Yöneticisi | &nbsp; | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Dizin okuyucuları | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Genel Yönetici | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:\*
Gruplar Yöneticisi | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Konuk davetci | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Yardım Masası Yöneticisi | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
İleti Merkezi okuyucusu | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Parola Yöneticisi | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Ayrıcalıklı kimlik doğrulama Yöneticisi | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Ayrıcalıklı rol yöneticisi | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Rapor okuyucu | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Kullanıcı (yönetici rolü yok) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Kullanıcı Yöneticisi | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Kullanım Özeti raporları okuyucusu | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:

\* Genel yönetici kendi genel yönetici atamasını kaldıramıyor. Bu, bir kuruluşun 0 genel yöneticisi olan bir durumu önlemektir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD rollerini gruplara atama](groups-assign-role.md)
- [Farklı rolleri anlama](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Kullanıcıyı Azure aboneliğinin yöneticisi olarak atama](../../role-based-access-control/role-assignments-portal-subscription-admin.md)
