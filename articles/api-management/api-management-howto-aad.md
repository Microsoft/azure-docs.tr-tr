---
title: Azure Active Directory kullanarak Geliştirici hesaplarını yetkilendirme
titleSuffix: Azure API Management
description: API Management Azure Active Directory kullanarak kullanıcılara yetki verme hakkında bilgi edinin.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2021
ms.author: apimpm
ms.openlocfilehash: 743a7e7d34457405aa4be42b196dc994506c6587
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105035820"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Azure API Management'ta geliştirici hesaplarını yetkilendirmek için Azure Active Directory kullanın

Bu makalede, kullanıcıların Azure Active Directory (Azure AD) aracılığıyla geliştirici portalına erişimi nasıl etkinleştireceğinizi gösterilmektedir. Bu kılavuzda ayrıca, kullanıcıları içeren dış grupları ekleyerek Azure AD kullanıcıları gruplarının nasıl yönetileceği gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

- Şu hızlı başlangıcı tamamlayın: [Azure API Management örneği oluşturma](get-started-create-service-instance.md).
- Azure API Management örneğini içeri aktarın ve yayımlayın. Daha fazla bilgi için bkz. [Içeri aktarma ve yayımlama](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Azure AD 'yi kullanarak Geliştirici hesaplarını yetkilendirme

1. [Azure portalında](https://portal.azure.com) oturum açın. 
2. Şunu seçin: ![Ok simgesi.](./media/api-management-howto-aad/arrow.png).
3. Arama kutusuna **API** yazın.
4. **API Management Hizmetleri**' ni seçin.
5. API Management hizmet örneğinizi seçin.
6. **Geliştirici Portalı** altında **kimlikler**' i seçin.
7. Üstten **+ Ekle** ' yi seçin.

    Sağ tarafta **kimlik sağlayıcı ekle** bölmesi görünür.
8. **Sağlayıcı türü** altında **Azure Active Directory**' yi seçin.

    Diğer gerekli bilgileri girmenize olanak sağlayan denetimler bölmesinde görünür. Denetimler **ISTEMCI kimliğini** ve **istemci gizli** anahtarını içerir. (Makalede daha sonra bu denetimler hakkında bilgi alırsınız.)
9. **Yeniden YÖNLENDIRME URL**'sinin içeriğini bir yere göz önünde alın.
    

    :::image type="content" source="media/api-management-howto-aad/api-management-with-aad001.png" alt-text="Azure portal kimlik sağlayıcısı ekleme":::
    > [!NOTE]
    > İki yeniden yönlendirme URL 'Si vardır:<br/>
    > **Yönlendirme URL 'si** -API Management en son geliştirici portalına işaret eder.<br/>
    > **Yeniden yönlendirme URL 'si (kullanım dışı portal)** -API Management kullanım dışı olan geliştirici portalına işaret eder.
    >
    > En son geliştirici portalı yeniden yönlendirme URL 'sini kullanmanız önerilir.
   
10. Tarayıcınızda, farklı bir sekme açın. 
11. Bir uygulamayı Active Directory kaydetmek için [Azure portal uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) gidin.
12. **Yönet**'in altında **Uygulama kayıtları** nı seçin.
13. **Yeni kayıt** seçeneğini belirleyin. **Uygulama kaydetme** sayfasında, değerleri şu şekilde ayarlayın:
    
    * **Adı** anlamlı bir ad olarak ayarlayın. Örneğin, *Geliştirici-Portal*
    * **Yalnızca bu kuruluş dizinindeki hesaplara** **Desteklenen hesap türlerini** ayarlayın. 
    * **Yeniden yönlendirme URI 'sini** adım 9 ' dan aldığınız değere ayarlayın. 
    * **Kaydet**' i seçin. 

14.  Uygulama kaydedildikten sonra, **genel bakış** sayfasından **uygulama (istemci) kimliğini** kopyalayın. 
15. API Management örneğinize geri dönün. **Kimlik sağlayıcısı ekle** penceresinde, **uygulama (istemci) KIMLIĞI** değerini **istemci kimliği** kutusuna yapıştırın.
16. Azure AD yapılandırmasına dönün, **Yönet** altında **Sertifikalar & gizlilikler** ' ı seçin. **Yeni istemci parolası** düğmesini seçin. **Açıklama** değerinde bir değer girin, **süre sonu** için herhangi bir seçenek belirleyin ve **Ekle**' yi seçin. Sayfadan çıkmadan önce istemci gizli değerini kopyalayın. Bu sonraki adımda gerekecektir. 
17. **Yönet** altında **kimlik doğrulaması** ' nı seçin ve ardından **örtülü izin** altında **Kimlik belirteçleri** ' ni seçin
18. API Management örneğine geri dönün, gizli anahtarı **istemci gizli** kutusuna yapıştırın.

    > [!IMPORTANT]
    > Lütfen anahtarın süresi dolmadan önce **istemci parolasını** güncelleştirdiğinizden emin olun. 
    >  
    >

19. **Kimlik sağlayıcısı ekle** penceresi, **izin verilen kiracılar** metin kutusunu da içerir. Burada, API Management hizmet örneğinin API 'Lerine erişim vermek istediğiniz Azure AD örneklerinin etki alanlarını belirtin. Birden çok etki alanını newlines, boşluk veya virgül ile ayırabilirsiniz.

    > [!NOTE]
    > **Izin verilen kiracılar** bölümünde birden çok etki alanı belirtebilirsiniz. Herhangi bir Kullanıcı, uygulamanın kaydedildiği orijinal etki alanından farklı bir etki alanından oturum açmadan önce, farklı bir etki alanının genel yöneticisinin uygulamanın dizin verilerine erişmesi için izin vermesi gerekir. İzin vermek için genel yönetici: a. Adresine gidin `https://<URL of your developer portal>/aadadminconsent` (örneğin, https://contoso.portal.azure-api.net/aadadminconsent) .
    > b. Erişim vermek istedikleri Azure AD kiracının etki alanı adını yazın.
    > c. **Gönder**’i seçin. 

20.  İstenen yapılandırmayı belirttikten sonra **Ekle**' yi seçin.

Değişiklikler kaydedildikten sonra, belirtilen Azure AD örneğindeki kullanıcılar, [bir Azure AD hesabı kullanarak Geliştirici Portalında oturum açma](#log_in_to_dev_portal)adımlarını izleyerek Geliştirici Portalında oturum açabilir.

## <a name="add-an-external-azure-ad-group"></a>Dış Azure AD grubu ekleme

Bir Azure AD kiracısındaki kullanıcılar için erişimi etkinleştirdikten sonra, Azure AD gruplarını API Management 'e ekleyebilirsiniz. Sonuç olarak, Azure AD gruplarını kullanarak ürün görünürlüğünü kontrol edebilirsiniz.

APıM 'e dış bir Azure AD grubu eklemek için önce önceki bölümü tamamlamalısınız. Ayrıca, kaydettiğiniz uygulamaya `Directory.Read.All` aşağıdaki adımları izleyerek MICROSOFT Graph API 'sine izin verilmelidir: 

1. Önceki bölümde oluşturulan uygulama kaydınız için geri dönün.
2. **API izinleri**' ni seçin ve **+ izin Ekle**' ye tıklayın. 
3. **API Izinleri iste** bölmesinde **Microsoft API 'leri** sekmesini seçin, aşağı kaydırın ve **Azure Active Directory Graph** kutucuğunu seçin. **Uygulama izinleri**' ni seçin, **Dizin** araması yapın ve ardından **Dizin. Read. All** iznini seçin. 
4. Bölmenin alt kısmındaki **Izinleri Ekle** ' ye tıklayın ve ardından bu dizindeki tüm kullanıcılar için erişim izni vermek üzere **{TenantName} Için yönetici onayı ver** ' e tıklayın. 

Artık API Management örneğinizin **gruplar** sekmesinden dış Azure AD grupları ekleyebilirsiniz.

1. **Gruplar** sekmesini seçin.
2. **AAD grubu Ekle** düğmesini seçin.
    !["AAD grubu Ekle" düğmesi](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Eklemek istediğiniz grubu seçin.
4. **Seç** düğmesine basın.

Dış bir Azure AD grubu ekledikten sonra özelliklerini gözden geçirebilir ve yapılandırabilirsiniz. **Gruplar** sekmesinden grubun adını seçin. Buradan, grup için **ad** ve **Açıklama** bilgilerini düzenleyebilirsiniz.
 
Yapılandırılmış Azure AD örneğindeki kullanıcılar artık Geliştirici Portalında oturum açabilir. Görünürlük sahibi oldukları grupları görüntüleyebilir ve bunlara abone olabilirler.

## <a name="developer-portal---add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"></a> Geliştirici portalı-Azure AD hesabı kimlik doğrulaması ekleme

Geliştirici portalında, **oturum açma düğmesi: OAuth** pencere Öğesı ile AAD ile oturum açma mümkündür. Pencere öğesi, varsayılan geliştirici portalı içeriğinin oturum açma sayfasında zaten bulunur.

Yeni bir Kullanıcı AAD ile oturum açtığında yeni bir hesap otomatik olarak oluşturulabilse de, kaydolma sayfasına aynı pencere öğesini eklemeyi göz önünde bulundurmanız gerekebilir.

**Kaydolma formu: OAuth** pencere öğesi, OAuth ile kaydolma için kullanılan bir formu temsil eder.

> [!IMPORTANT]
> AAD değişikliklerinin etkili olması için [portalı yeniden yayımlamanız](api-management-howto-developer-portal-customize.md#publish) gerekir.

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Eski geliştirici portalı-Azure AD ile oturum açma

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Önceki bölümlerde yapılandırdığınız bir Azure AD hesabını kullanarak Geliştirici Portalında oturum açmak için:

1. Active Directory Uygulama yapılandırmasından oturum açma URL 'sini kullanarak yeni bir tarayıcı penceresi açın ve **Azure Active Directory**' ı seçin.

   ![Oturum açma sayfası][api-management-dev-portal-signin]

1. Azure AD 'deki kullanıcılardan birinin kimlik bilgilerini girin ve **oturum aç**' ı seçin.

   ![Kullanıcı adı ve parolayla oturum açma][api-management-aad-signin]

1. Herhangi bir ek bilgi gerekliyse bir kayıt formu istenebilir. Kayıt formunu doldurun ve **Kaydol**' u seçin.

   ![Kayıt formunda "kaydolun" düğmesi][api-management-complete-registration]

Kullanıcılarınız artık API Management hizmet örneğiniz için Geliştirici Portalında oturum açtı.

![Kayıt tamamlandıktan sonra geliştirici portalı][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
