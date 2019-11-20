---
title: REST ile Azure Media Services API 'sine erişmek için Azure AD kimlik doğrulamasını kullanma | Microsoft Docs
description: REST kullanarak Azure Active Directory kimlik doğrulamasıyla Azure Media Services API 'sine erişme hakkında bilgi edinin.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: willzhan; johndeu
ms.openlocfilehash: 66c69552157df957e572a3af092131a3b7e560d5
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871700"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>REST ile Media Services API 'sine erişmek için Azure AD kimlik doğrulamasını kullanma

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>En son sürüm olan [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)’ü inceleyin. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-from-v2-to-v3.md)

Azure Media Services ile Azure AD kimlik doğrulaması kullanırken, iki şekilde kimlik doğrulaması yapabilirsiniz:

- **Kullanıcı kimlik doğrulaması** , Azure Media Services kaynaklarla etkileşim kurmak için uygulamayı kullanan bir kişinin kimliğini doğrular. Etkileşimli uygulama öncelikle kullanıcıdan kimlik bilgilerini istemelidir. Bu örnek, yetkili kullanıcılar tarafından kodlama işlerini veya canlı akışı izlemek için kullanılan bir yönetim konsolu uygulamasıdır. 
- **Hizmet sorumlusu kimlik doğrulaması** bir hizmetin kimliğini doğrular. Bu kimlik doğrulama yöntemini yaygın olarak kullanan uygulamalar, Web uygulamaları, işlev uygulamaları, Logic Apps, API 'Ler veya mikro hizmetler gibi Daemon Hizmetleri, orta katman hizmetler veya zamanlanan işleri çalıştıran uygulamalardır.

    Bu öğreticide, Azure AD **hizmet sorumlusu** kimlik doğrulaması 'nı Rest ile AMS API 'sine erişmek için nasıl kullanacağınız gösterilmektedir. 

    > [!NOTE]
    > **Hizmet sorumlusu** , Azure Media Services bağlanan çoğu uygulama için önerilen en iyi uygulamadır. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure portal kimlik doğrulama bilgilerini alın
> * Postman kullanarak erişim belirtecini al
> * Erişim belirtecini kullanarak **varlıklar** API 'sini test etme


> [!IMPORTANT]
> Şu anda Media Services Azure Access Control Hizmetleri kimlik doğrulama modelini destekler. Ancak, Access Control kimlik doğrulaması kullanım dışı olacaktır 1 Haziran 2018. Azure AD kimlik doğrulaması modeline mümkün olan en kısa sürede geçiş yapmanız önerilir.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.
- [Azure Portal kullanarak bir Azure Media Services hesabı oluşturun](media-services-portal-create-account.md).
- [Azure AD kimlik doğrulamasına genel bakış makalesini Azure Media Services API 'sine](media-services-use-aad-auth-to-access-ams-api.md) göz atın.
- Bu makalede gösterilen REST API 'Leri yürütmek için [Postman](https://www.getpostman.com/) Rest istemcisini yükleme. 

    Bu öğreticide **Postman** kullanıyoruz, ancak Rest aracı uygun olacak. Diğer alternatifler: REST eklentisi veya **Telerik Fiddler**ile **Visual Studio Code** . 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Azure portal kimlik doğrulama bilgilerini alın

### <a name="overview"></a>Genel Bakış

Media Services API 'sine erişmek için aşağıdaki veri noktalarını toplamanız gerekir.

|Ayar|Örnek|Açıklama|
|---|-------|-----|
|Azure Active Directory kiracı etki alanı|microsoft.onmicrosoft.com|Azure AD, güvenli belirteç hizmeti (STS) uç noktası olarak şu biçim kullanılarak oluşturulmuştur: <https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token>. Azure AD, kaynaklara erişmek için bir JWT yayınlar (bir erişim belirteci).|
|REST API uç noktası|<https://amshelloworld.restv2.westus.media.azure.net/api/>|Bu, uygulamanızdaki tüm Media Services REST API çağrılarının yapıldığı bitiş noktasıdır.|
|İstemci KIMLIĞI (uygulama KIMLIĞI)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Azure AD uygulaması (istemci) KIMLIĞI. Erişim belirtecini almak için istemci KIMLIĞI gereklidir. |
|İstemci Gizli Anahtarı|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Azure AD uygulama anahtarları (istemci gizli dizisi). Erişim belirtecini almak için istemci parolası gerekir.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Azure portal AAD kimlik doğrulama bilgilerini al

Bilgileri almak için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com)’da oturum açın.
2. AMS örneğinize gidin.
3. **API erişimi**seçin.
4. **Hizmet sorumlusu ile Azure MEDIA SERVICES API 'Sine Bağlan**' a tıklayın.

    ![API erişimi](./media/connect-with-rest/connect-with-rest01.png)

5. Mevcut bir **Azure AD uygulaması** seçin veya yeni bir tane oluşturun (aşağıda gösterildiği gibi).

    > [!NOTE]
    > Azure Medya REST isteğinin başarılı olması için, çağıran kullanıcının erişmeye çalıştığı Media Services hesap için **katkıda bulunan** veya **sahip** rolü olmalıdır. "Uzak sunucu bir hata döndürdü: (401) yetkilendirilmemiş, "bkz. [erişim denetimi](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Yeni bir AD uygulaması oluşturmanız gerekiyorsa, şu adımları izleyin:
    
   1. **Yeni oluştur**' a basın.
   2. Bir ad girin.
   3. **Yeni oluştur** ' a yeniden basın.
   4. **Kaydet**’e basın.

      ![API erişimi](./media/connect-with-rest/new-app.png)

      Yeni uygulama sayfada görüntülenir.

6. **ISTEMCI kimliğini** (uygulama kimliği) alın.
    
   1. Uygulamayı seçin.
   2. Sağdaki pencereden **ISTEMCI kimliğini** alın. 

      ![API erişimi](./media/connect-with-rest/existing-client-id.png)

7. Uygulamanın **anahtarını** (istemci parolası) alın. 

   1. **Uygulamayı Yönet** düğmesine tıklayın (istemci kimliği BILGISININ **uygulama kimliği**altında olduğuna dikkat edin). 
   2. **Tuşlara**basın.
    
       ![API erişimi](./media/connect-with-rest/manage-app.png)
   3. **Açıklama** ' yı doldurarak ve **Kaydet** **' i basarak** uygulama anahtarını (istemci gizli anahtarı) oluşturun.
    
       **Kaydet** düğmesine basıldığında, anahtar değeri görünür. Dikey pencereden çıkmadan önce anahtar değerini kopyalayın.

   ![API erişimi](./media/connect-with-rest/connect-with-rest03.png)

Kodunuzda daha sonra kullanmak için, Web. config veya App. config dosyanıza AD bağlantı parametreleri için değerler ekleyebilirsiniz.

> [!IMPORTANT]
> **İstemci anahtarı** önemli bir gizli dizi ve bir anahtar kasasında veya üretimde şifrelenmiş olarak güvenli bir şekilde korunmalıdır.

## <a name="get-the-access-token-using-postman"></a>Postman kullanarak erişim belirtecini al

Bu bölümde, bir JWT taşıyıcı belirteci (erişim belirteci) döndüren bir REST API yürütmek için **Postman** 'ın nasıl kullanılacağı gösterilmektedir. Herhangi bir Media Services REST API çağırmak için, çağrılara "Authorization" üst bilgisini eklemeniz ve her çağrıya "taşıyıcı *your_access_token*" değerini eklemeniz gerekir (Bu öğreticinin sonraki bölümünde gösterildiği gibi). 

1. **Postman**'yi açın.
2. **POST**'u seçin.
3. Aşağıdaki biçimi kullanarak kiracı adınızı içeren URL 'YI girin: kiracı adı **. onmicrosoft.com** ile BITMELIDIR ve URL 'nin **OAuth2/Token**ile bitmesi gerekir: 

    https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token

4. **Üstbilgiler** sekmesini seçin.
5. "Anahtar/değer" veri kılavuzunu kullanarak **üstbilgiler** bilgilerini girin. 

    ![Veri kılavuzu](./media/connect-with-rest/headers-data-grid.png)

    Alternatif olarak, Postman penceresinin sağ tarafındaki **toplu düzenleme** bağlantısına tıklayın ve aşağıdaki kodu yapıştırın.

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. **Gövde** sekmesine basın.
7. "Anahtar/değer" veri kılavuzunu kullanarak gövde bilgilerini girin (istemci KIMLIĞINI ve gizli değerleri değiştirin). 

    ![Veri kılavuzu](./media/connect-with-rest/data-grid.png)

    Alternatif olarak, Postman penceresinin sağ tarafındaki **Toplu Düzenle** ' ye tıklayın ve aşağıdaki gövdeye yapıştırın (istemci kimliğini ve gizli değerleri değiştirin):

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your Azure AD Application}
        client_secret:{Your client secret that you got from your Azure AD Application's Keys}
        resource:https://rest.media.azure.net

8. **Gönder**’e basın.

    ![belirteç al](./media/connect-with-rest/connect-with-rest04.png)

Döndürülen yanıt, herhangi bir AMS API 'ye erişmek için kullanmanız gereken **erişim belirtecini** içerir.

## <a name="test-the-assets-api-using-the-access-token"></a>Erişim belirtecini kullanarak **varlıklar** API 'sini test etme

Bu bölümde, **Postman**kullanılarak **varlıklar** API 'sine nasıl erişebileceğiniz gösterilmektedir.

1. **Postman**'yi açın.
2. **GET**'i seçin.
3. REST API uç noktasını yapıştırın (örneğin, https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. **Yetkilendirme** sekmesini seçin. 
5. **Taşıyıcı belirtecini**seçin.
6. Önceki bölümde oluşturulan belirteci yapıştırın.

    ![belirteç al](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > Postman UX, Mac ve PC arasında farklı olabilir. Mac sürümünde **kimlik doğrulama** bölümü açılan menüsünde "taşıyıcı belirteci" seçeneği yoksa, **Yetkilendirme** üstbilgisini Mac istemcisine el ile eklemeniz gerekir.

   ![Kimlik doğrulama üst bilgisi](./media/connect-with-rest/auth-header.png)

7. **Üst bilgileri**seçin.
5. Sağ taraftaki Postman penceresinde **toplu düzenleme** bağlantısı ' na tıklayın.
6. Aşağıdaki üst bilgileri yapıştırın:

        x-ms-version:2.15
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. **Gönder**’e basın.

Döndürülen yanıt, hesabınızdaki varlıkları içerir.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Media Services erişim için Azure AD [kimlik doğrulaması 'nda Bu örnek kodu deneyin: Her ikisi de REST API aracılığıyla](https://github.com/willzhan/WAMSRESTSoln)
* [.NET ile karşıya dosya yükleme](media-services-dotnet-upload-files.md)
