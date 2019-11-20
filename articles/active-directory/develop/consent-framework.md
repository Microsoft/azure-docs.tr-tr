---
title: Azure Active Directory izin çerçevesi
titleSuffix: Microsoft identity platform
description: Azure Active Directory 'de onay çerçevesi ve çok kiracılı web ve yerel istemci uygulamaları geliştirmeyi nasıl kolaylaştırdığını öğrenin.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2018
ms.author: ryanwi
ms.reviewer: zachowd, lenalepa, jesakowi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: af5b60901e57392aaea504f96572801a878d707c
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803852"
---
# <a name="azure-active-directory-consent-framework"></a>Azure Active Directory izin çerçevesi

Azure Active Directory (Azure AD) onay çerçevesi, çok kiracılı web ve yerel istemci uygulamaları geliştirmeyi kolaylaştırır. Bu uygulamalar, bir Azure AD kiracısından, uygulamanın kaydedildiği bilgisayardan farklı olan kullanıcı hesaplarına oturum açma izni verir. Ayrıca, kendi Web API 'lerinize ek olarak, Microsoft Graph API (Office 365 ' deki Azure AD, Intune ve hizmetlere erişmek için) ve diğer Microsoft Hizmetleri ' API 'Leri gibi Web API 'Lerine erişim de gerekebilir.

Çerçeve, dizin verilerine erişmeyi gerektirebilecek, kendi dizinine kaydedilmesini isteyen bir uygulamaya onay veren bir kullanıcı veya yöneticiye dayalıdır. Örneğin, bir Web istemcisi uygulamasının Office 365 kullanıcısı hakkındaki takvim bilgilerini okuması gerekiyorsa, bu kullanıcının önce istemci uygulamaya onay verilmesi gerekir. Onay verildikten sonra, istemci uygulaması kullanıcı adına Microsoft Graph API 'sini çağırabilir ve gerektiğinde takvim bilgilerini kullanabilir. [Microsoft Graph API 'Si](https://developer.microsoft.com/graph) Office 365 ' deki verilere erişim sağlar (takvimler ve Exchange 'ten gelen Iletiler, SharePoint 'ten gelen siteler ve listeler, OneDrive 'daki belgeler, iş defterlerindeki görevler ve Excel 'deki çalışma kitapları) ve kullanıcılar ve Azure AD ve diğer veri nesnelerinden daha fazla Microsoft bulut hizmetlerinden gruplar.

Onay çerçevesi, OAuth 2,0 ' de ve yetkilendirme kodu verme ve istemci kimlik bilgileri gibi çeşitli akışlarda, genel veya gizli istemciler kullanılarak oluşturulmuştur. Azure AD, OAuth 2,0 kullanarak bir telefon, tablet, sunucu veya Web uygulaması gibi birçok farklı türde istemci uygulaması oluşturmayı mümkün kılar ve gerekli kaynaklara erişim elde edebilir.

Onay çerçevesini OAuth 2.0 yetkilendirmesi ile kullanma hakkında daha fazla bilgi için bkz. [Azure AD Için](authentication-scenarios.md) [OAuth 2,0 ve Azure AD ve kimlik doğrulama senaryolarını kullanarak Web uygulamalarına erişim yetkisi verme](v1-protocols-oauth-code.md) . Microsoft Graph aracılığıyla Office 365 yetkilendirme erişimi alma hakkında bilgi için bkz. [Microsoft Graph Ile uygulama kimlik doğrulaması](https://developer.microsoft.com/graph/docs/authorization/auth_overview).

## <a name="consent-experience---an-example"></a>Onay deneyimi-bir örnek

Aşağıdaki adımlarda, onay deneyiminin hem uygulama geliştiricisi hem de Kullanıcı için nasıl çalıştığı gösterilmektedir.

1. Bir kaynağa/API 'ye erişmek için belirli izinler istemesi gereken bir Web istemci uygulamanız olduğunu varsayalım. Bir sonraki bölümde bu yapılandırmayı nasıl yapacağınızı öğreneceksiniz, ancak temel olarak Azure portal yapılandırma zamanında izin isteklerini bildirmek için kullanılır. Diğer yapılandırma ayarları gibi, uygulamanın Azure AD kaydının bir parçası haline gelir:

    ![Diğer uygulamalar için izinler](./media/consent-framework/permissions.png)

1. Uygulamanızın izinlerinin güncelleştirildiğini, uygulamanın çalıştığını ve bir kullanıcının ilk kez kullanmak üzere olduğunu göz önünde bulundurun. İlk olarak, uygulamanın Azure AD 'nin `/authorize` uç noktasından bir yetkilendirme kodu alması gerekir. Yetkilendirme kodu daha sonra yeni bir erişim ve yenileme belirteci almak için kullanılabilir.

1. Kullanıcının kimliği doğrulanmıyorsa, Azure AD 'nin `/authorize` uç noktası kullanıcıdan oturum açmasını ister.

    ![Kullanıcı veya yönetici Azure AD 'de oturum açın](./media/quickstart-v1-integrate-apps-with-azure-ad/usersignin.png)

1. Kullanıcı oturum açtıktan sonra, Azure AD kullanıcının bir onay sayfası gösterilmesi gerekip gerekmediğini tespit eder. Bu belirleme, kullanıcının (veya kuruluşun yöneticisinin) uygulama iznini zaten vermiş olup olmadığına bağlıdır. İzin verilmemişse, Azure AD kullanıcıya onay ister ve çalışması gereken gerekli izinleri görüntüler. Onay iletişim kutusunda görüntülenen izin kümesi, Azure portal **temsilci izinleri** içinde seçili olanlarla eşleşir.

    ![Onay iletişim kutusunda gösterilen izinlere bir örnek gösterir](./media/quickstart-v1-integrate-apps-with-azure-ad/consent.png)

1. Kullanıcı onay verdikten sonra uygulamanıza bir erişim belirteci ve yenileme belirteci almak için kullanılan bir yetkilendirme kodu döndürülür. Bu akış hakkında daha fazla bilgi için bkz. [Web API uygulaması türü](web-api.md).

1. Yönetici olarak, kiracınızdaki tüm kullanıcılar adına bir uygulamanın temsilci izinlerini de kabul edebilirsiniz. Yönetici onayı, izin iletişim kutusunun Kiracıdaki her kullanıcı için görünmesini engeller ve yönetici rolüne sahip kullanıcılar tarafından [Azure Portal](https://portal.azure.com) yapılabilir. Hangi Yönetici rollerinin temsilci izinleri onaylamasına izin verebileceğini öğrenmek için bkz. [Azure AD 'de yönetici rolü izinleri](../users-groups-roles/directory-assign-admin-roles.md).

    **Uygulamanın temsilci izinlerini kabul etmek için**

   1. Uygulamanızın **API izinleri** sayfasına gidin
   1. **Yönetici onayı ver** düğmesine tıklayın.

      ![Açık yönetici onayı için izin verme](./media/consent-framework/grant-consent.png)

   > [!IMPORTANT]
   > **Izin verme** düğmesi kullanılarak açık onay verilmesi şu anda adal. js kullanan tek sayfalı uygulamalar (Spa) için gereklidir. Aksi takdirde, erişim belirteci istendiğinde uygulama başarısız olur.

## <a name="next-steps"></a>Sonraki adımlar

* Bkz. [bir uygulamayı çok kiracılı olarak dönüştürme](howto-convert-app-to-be-multi-tenant.md)
* Daha ayrıntılı bilgi edinmek için [yetkilendirme kodu verme akışı sırasında OAuth 2,0 protokol katmanında izin nasıl desteklendiğini öğrenin.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)
