---
title: Kimlik doğrulamasını yönetme
titleSuffix: Azure Maps
description: Azure haritalar kimlik doğrulamasıyla tanıdık olun. Hangi yaklaşımın en iyi hangi senaryoda çalıştığına bakın. Kimlik doğrulama ayarlarını görüntülemek için portalını nasıl kullanacağınızı öğrenin.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 955b541bdb4ae38066f1eb4d2f09363ec51be1d2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864083"
---
# <a name="manage-authentication-in-azure-maps"></a>Azure haritalar 'da kimlik doğrulamasını yönetme

Azure haritalar hesabı oluşturduktan sonra, Azure Active Directory (Azure AD) kimlik doğrulamasını ve paylaşılan anahtar kimlik doğrulamasını desteklemek için bir istemci KIMLIĞI ve anahtarı oluşturulur.

## <a name="view-authentication-details"></a>Kimlik doğrulama ayrıntılarını görüntüle

Azure haritalar hesabı oluşturduktan sonra, birincil ve ikincil anahtarlar oluşturulur. [Azure haritalar 'ı çağırmak Için paylaşılan anahtar kimlik doğrulaması](./azure-maps-authentication.md#shared-key-authentication)kullandığınızda bir birincil anahtarı abonelik anahtarı olarak kullanmanızı öneririz. İkincil bir anahtarı, kayan anahtar değişiklikleri gibi senaryolarda kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure haritalar 'Da kimlik doğrulaması](./azure-maps-authentication.md).

Kimlik doğrulama ayrıntılarınızı Azure portal görüntüleyebilirsiniz. Burada, hesabınızda, **Ayarlar** menüsünde **kimlik doğrulaması**' nı seçin.

> [!div class="mx-imgBorder"]
> ![Kimlik doğrulaması ayrıntıları](./media/how-to-manage-authentication/how-to-view-auth.png)

## <a name="discover-category-and-scenario"></a>Kategori ve senaryoyu bul

Uygulama ihtiyaçlarına bağlı olarak, uygulamayı güvenli hale getirmek için belirli yol yolları vardır. Azure AD, çok çeşitli kimlik doğrulama akışlarını desteklemek için kategorileri tanımlar. Uygulamanın hangi kategoriye uyduğunu anlamak için bkz. [uygulama kategorileri](../active-directory/develop/authentication-flows-app-scenarios.md#application-categories) .

> [!NOTE]
> Paylaşılan anahtar kimlik doğrulaması kullanıyor olsanız bile, kategorileri ve senaryoları anlamak, uygulamayı güvenli hale getirmeye yardımcı olur.

## <a name="determine-authentication-and-authorization"></a>Kimlik doğrulama ve yetkilendirmeyi belirleme

Aşağıdaki tabloda Azure Maps 'ta yaygın kimlik doğrulama ve yetkilendirme senaryoları özetlenmektedir. Tablo her senaryonun sunduğu koruma türlerinin bir karşılaştırmasını sağlar.

> [!IMPORTANT]
> Microsoft, üretim uygulamaları için Azure rol tabanlı erişim denetimi (Azure RBAC) ile Azure Active Directory (Azure AD) uygulamayı önerir.

| Senaryo                                                                                    | Kimlik Doğrulaması | Yetkilendirme | Geliştirme çabası | İşletimsel çaba |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [Güvenilen Daemon/etkileşimli olmayan istemci uygulaması](./how-to-secure-daemon-app.md)        | Paylaşılan Anahtar     | Yok           | Orta             | Yüksek               |
| [Güvenilen Daemon/etkileşimli olmayan istemci uygulaması](./how-to-secure-daemon-app.md)        | Azure AD       | Yüksek          | Düşük                | Orta             |
| [Etkileşimli Çoklu oturum açma ile Web tek sayfalı uygulama](./how-to-secure-spa-users.md) | Azure AD       | Yüksek          | Orta             | Orta             |
| [Etkileşimli olmayan oturum açma ile Web tek sayfalı uygulama](./how-to-secure-spa-app.md)      | Azure AD       | Yüksek          | Orta             | Orta             |
| [Etkileşimli Çoklu oturum açma ile Web uygulaması](./how-to-secure-webapp-users.md)          | Azure AD       | Yüksek          | Yüksek               | Orta             |
| [IoT cihazı/giriş kısıtlanmış cihaz](./how-to-secure-device-code.md)                     | Azure AD       | Yüksek          | Orta             | Orta             |

Tablodaki bağlantılar sizi her senaryo için ayrıntılı yapılandırma bilgilerine götürür.

## <a name="view-role-definitions"></a>Rol tanımlarını görüntüle

Azure haritalar için kullanılabilen Azure rollerini görüntülemek için **erişim denetimi (IAM)** sayfasına gidin. **Roller**' i seçin ve ardından *Azure Maps* ile başlayan roller için arama yapın. Bu Azure Maps rolleri, erişim sağlayabilmeniz için kullanabileceğiniz rollerdir.

> [!div class="mx-imgBorder"]
> ![Kullanılabilir rolleri görüntüle](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

## <a name="view-role-assignments"></a>Rol atamalarını görüntüle

Azure haritalar için erişim izni verilen kullanıcıları ve uygulamaları görüntülemek için **Access Control (IAM)** sayfasına gidin. Burada **rol atamaları**' nı seçin ve ardından **Azure Maps**'a göre filtreleyin.

> [!div class="mx-imgBorder"]
> ![Erişim izni verilen kullanıcıları ve uygulamaları görüntüleme](./media/how-to-manage-authentication/how-to-view-amrbac.png)

## <a name="request-tokens-for-azure-maps"></a>Azure haritalar için istek belirteçleri

Azure AD belirteç uç noktasından bir belirteç isteyin. Azure AD isteğiniz içinde aşağıdaki ayrıntıları kullanın:

| Azure ortamı      | Azure AD belirteç uç noktası             | Azure Kaynak KIMLIĞI              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Azure genel bulutu     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Kamu Bulutu | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

Kullanıcılar ve hizmet sorumluları için Azure AD 'den erişim belirteçleri isteme hakkında daha fazla bilgi için bkz. [Azure AD Için kimlik doğrulama senaryoları](../active-directory/develop/authentication-vs-authorization.md) ve [senaryolar](./how-to-manage-authentication.md#determine-authentication-and-authorization)tablosunda belirli senaryoları görüntüleme.

## <a name="manage-and-rotate-shared-keys"></a>Paylaşılan anahtarları yönetme ve döndürme

Azure haritalar abonelik anahtarlarınız, Azure haritalar hesabınızın kök parolasıyla benzerdir. Abonelik anahtarlarınızı korumak için her zaman dikkatli olun. Anahtarlarınızı güvenli bir şekilde yönetmek ve döndürmek için Azure Key Vault kullanın. Erişim anahtarlarını diğer kullanıcılara dağıtmaktan, sabit kodlamadan veya başkalarının erişebileceği düz metin olarak herhangi bir yere kaydetmekten kaçının. Anahtarların tehlikede olduğunu düşünüyorsanız, anahtarlarınızı döndürün.

> [!NOTE]
> Microsoft, mümkünse paylaşılan anahtar yerine istekleri yetkilendirmek için Azure Active Directory (Azure AD) kullanılmasını önerir. Azure AD, paylaşılan anahtar üzerinde üstün güvenlik ve kullanım kolaylığı sağlar.

### <a name="manually-rotate-subscription-keys"></a>Abonelik anahtarlarını el ile döndürme

Microsoft, Azure haritalar hesabınızı güvende tutmaya yardımcı olmak için abonelik anahtarlarınızı düzenli aralıklarla döndürmenizi önerir. Mümkünse, erişim anahtarlarınızı yönetmek için Azure Key Vault kullanın. Key Vault kullanmıyorsanız, anahtarlarınızı el ile döndürmeniz gerekir.

Anahtarlarınızı döndürebilmeniz için iki abonelik anahtarı atanır. İki anahtara sahip olmak, uygulamanızın işlem boyunca Azure Maps 'e erişimini sürdürmesini sağlar.

Azure haritalar abonelik anahtarlarınızı Azure portal döndürmek için:

1. Azure haritalar hesabı ve dağıtımı için ikincil anahtara başvurmak üzere uygulama kodunuzu güncelleştirin.
2. [Azure Portal](https://portal.azure.com/)Azure Maps hesabınıza gidin.
3. **Ayarlar** altında **kimlik doğrulaması**' nı seçin.
4. Azure haritalar hesabınız için birincil anahtarı yeniden oluşturmak üzere birincil anahtarın yanındaki **Oluştur** düğmesini seçin.
5. Yeni birincil anahtara başvuracak ve dağıtımı yapmak için uygulama kodunuzu güncelleştirin.
6. İkincil anahtarı aynı şekilde yeniden oluşturun.

> [!WARNING]
> Microsoft, tüm uygulamalarınızda aynı anda yalnızca bir tane anahtar kullanılmasını önerir. Anahtar 1 ' i bazı yerlerde ve anahtar 2 ' de kullanırsanız, bazı uygulamaları erişimi kaybetmeksizin döndüremezsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [Azure AD ve Azure Maps web SDK](./how-to-use-map-control.md).

Azure haritalar hesabınız için API kullanım ölçümlerini bulun:
> [!div class="nextstepaction"]
> [Kullanım ölçümlerini görüntüleme](how-to-view-api-usage.md)

Azure AD 'nin Azure Maps ile nasıl tümleştirileceğini gösteren örnekleri araştırma:

> [!div class="nextstepaction"]
> [Azure AD kimlik doğrulaması örnekleri](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)