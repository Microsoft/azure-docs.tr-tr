---
title: include dosyası
description: include dosyası
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 05/22/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 3f358dedea37eb33c2d2bb26a823d3633560d3a0
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73850522"
---
Azure Active Directory (Azure AD) hizmetine yönelik kullanım kısıtlamalarını ve diğer hizmet sınırlarını burada bulabilirsiniz.

| Kategori | Sınırlar |
| --- | --- |
| Dizinler | Tek bir kullanıcı, üye veya konuk olarak en fazla 500 Azure AD dizinine ait olabilir.<br/>Tek bir kullanıcı, en fazla 20 dizin oluşturabilir. |
| Etki Alanları | En fazla 900 yönetilen etki alanı adı ekleyebilirsiniz. Şirket içi Active Directory ile tüm etki alanlarınızı federasyon için ayarlarsanız her dizinde en fazla 450 etki alanı adı ekleyebilirsiniz. |
|Kaynaklar |<ul><li>Varsayılan olarak Azure Active Directory ücretsiz sürümü kullanıcıları tarafından tek bir dizinde en fazla 50.000 Azure AD kaynağı oluşturulabilir. En az bir doğrulanmış etki alanınız varsa, Azure AD 'deki varsayılan dizin hizmeti kotası 300.000 Azure AD kaynağına genişletilir. </li><li>Yönetici olmayan bir Kullanıcı 250 taneden fazla Azure AD kaynağı oluşturabilir. Hem etkin kaynaklar hem de geri yüklenebilecek silinen kaynaklar bu kotaya doğru sayılır. Yalnızca 30 günden daha kısa bir süre önce silinen Azure AD kaynakları geri yüklemek için kullanılabilir. 30 gün boyunca bir çeyrekte bu kotaya doğru sayımı geri yüklemek için artık kullanılamayan Azure AD kaynakları silindi. Normal görevleri çerçevesinde bu kotayı sürekli olarak aşmaları muhtemel olan geliştiricileriniz varsa, sınırsız sayıda uygulama kaydı oluşturma izniyle [özel bir rol oluşturabilir ve atayabilirsiniz](../articles/active-directory/users-groups-roles/roles-quickstart-app-registration-limits.md) .</li></ul> |
| Şema uzantıları |<ul><li>Dize türü uzantılar en fazla 256 karakter olabilir. </li><li>İkili tür uzantılar 256 bayt ile sınırlıdır.</li><li>Tek bir Azure AD kaynağına yalnızca 100 uzantı değeri, *Tüm* türler ve *Tüm* uygulamalar arasında yazılabilir.</li><li>Dize türü veya ikili tür tek değerli özniteliklerle yalnızca User, Group, TenantDetail, Device, Application ve ServicePrincipal varlıkları uzatılabilir.</li><li>Şema uzantıları yalnızca Graph API'si 1.21 önizleme sürümünde kullanılabilir. Bir uzantıyı kaydetmek için uygulamaya yazma erişimi verilmelidir.</li></ul> |
| Uygulamalar |Tek bir uygulamanın sahibi en fazla 100 kullanıcı olabilir. |
|Uygulama bildirimi |Uygulama bildiriminde en fazla 1200 giriş eklenebilir. |
| Gruplar |<ul><li>Tek bir grubun sahibi olarak en fazla 100 kullanıcı atanabilir.</li><li>Herhangi bir sayıda Azure AD kaynağı tek bir grubun üyesi olabilir.</li><li>Bir kullanıcı, herhangi bir sayıda grubun üyesi olabilir.</li><li>Bir grupta, Azure AD Connect kullanarak şirket içi Active Directory'den Azure Active Directory'ye eşitleyebileceğiniz üye sayısı 50.000 ile sınırlıdır.</li></ul> |
| Uygulama Proxy'si | <ul><li>Her uygulama proxy uygulaması için saniyede en fazla 500 işlem</li><li>Kiracı için saniye başına en fazla 750 işlem</li></ul><br/>Bir işlem, benzersiz bir kaynak için tek bir http isteği ve yanıtı olarak tanımlanır. Kısıtlandığınızda, istemciler bir 429 yanıtı (çok fazla istek) alır. |
| Erişim Paneli |<ul><li>Erişim Paneli'nde kullanıcı başına görülebilecek uygulama sayısı sınırı yoktur. Bu, Azure AD Premium veya Enterprise Mobility Suite lisansları atanmış kullanıcılar için geçerlidir.</li><li>Erişim Paneli'nde her kullanıcı için en fazla 10 uygulama kutucuğu görülebilir. Bu sınır Azure AD Ücretsiz Lisans planına lisans atanmış kullanıcılar için geçerlidir. Uygulama kutucuklarına Box, Salesforce veya Dropbox örnek verilebilir. Bu sınır yönetici hesapları için geçerli değildir.</li></ul> |
| Reports | Herhangi bir raporda en fazla 1.000 satır görüntülenebilir veya indirilebilir. Bunun üzerindeki veriler kesilir. |
| Yönetim birimleri | Bir Azure AD kaynağı 30 ' dan fazla yönetim birimi olmayan bir üye olabilir. |
| Yönetici rolleri ve izinleri | <ul><li>Bir grup, [sahip](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership)olarak eklenemez.</li><li>Bir grup [role](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)atanamaz.</li><li>Yönetici olmayan kullanıcıların tüm dizin bilgilerine erişimini devre dışı bırakmak için kullanıcıların diğer kullanıcıların dizin bilgilerini okuma yeteneği, kiracı genelinde anahtar dışında kısıtlanamaz. [Burada](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users)varsayılan izinler hakkında daha fazla bilgi.</li><li>Yönetici rolü üyelik eklemeleri ve geri alma işlemleri yürürlüğe girmeden önce 15 dakika kadar sürebilir veya oturum kapatıp oturum açıp oturum açabilirsiniz.</li></ul> |
