---
title: Etkileşimli olmayan oturum açma ile tek sayfalı bir uygulamanın güvenliğini sağlama
titleSuffix: Azure Maps
description: Etkileşimli olmayan Azure rol tabanlı erişim denetimi (Azure RBAC) ve Azure Maps web SDK ile tek sayfalı bir uygulama yapılandırma.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 9d2af0bf731ab069a8512cb10feccf5ba18d3fa0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092730"
---
# <a name="how-to-secure-a-single-page-application-with-non-interactive-sign-in"></a>Etkileşimli olmayan oturum açma ile tek sayfalı bir uygulamanın güvenliğini sağlama

Aşağıdaki kılavuz, Kullanıcı Azure AD 'de oturum açarken Azure Maps uygulamalarına erişim belirteci sağlamak için Azure Active Directory (Azure AD) kullanan bir uygulamayla ilgilidir. Bu akış, yalnızca tek sayfalı Web uygulaması tarafından erişilebilmesi için güvenli hale getirilmesi gereken bir Web hizmetinin barındırılmasını gerektirir. Azure AD kimlik doğrulamasını gerçekleştirebilen birden çok uygulama vardır. Bu kılavuz, erişim belirteçleri almak için ürün, Azure Işlevinden yararlanır.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Azure haritalar, Kullanıcı oturum açma/etkileşimli akışlarından erişim belirteçlerini destekleyebilir. Etkileşimli akışlar, erişim iptali ve gizli dizi yönetiminin daha kısıtlı bir kapsamını etkinleştirir.

## <a name="create-azure-function"></a>Azure İşlevi oluşturma

Azure AD 'den kimlik doğrulamasından sorumlu bir güvenli Web hizmeti uygulaması oluşturun. 

1. Azure portal bir işlev oluşturun. Daha fazla bilgi için bkz. [Azure Işlevi oluşturma](../azure-functions/functions-get-started.md).

2. Azure işlevindeki CORS ilkesini tek sayfalı Web uygulaması tarafından erişilebilir olacak şekilde yapılandırın. Bu, tarayıcı istemcilerinin Web uygulamanızın izin verilen kaynaklarına güvenmemesini sağlar. Bkz. [CORS Işlevselliği ekleme](../app-service/app-service-web-tutorial-rest-api.md#add-cors-functionality).

3. Azure AD 'de kimlik doğrulaması yapmak için bir hizmet sorumlusu oluşturulmasını etkinleştirmek üzere Azure işlevinde [sistem tarafından atanan bir kimlik ekleyin](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) .  

4. Azure haritalar hesabına sistem tarafından atanan kimlik için rol tabanlı erişim verin. Ayrıntılar için bkz. [rol tabanlı erişim verme](#grant-role-based-access) .

5. Desteklenen mekanizmalardan veya REST protokolden biriyle sistem tarafından atanan kimlik kullanarak Azure Maps erişim belirteçlerini almak için Azure işlevi için kod yazın. Bkz. [Azure kaynakları için belirteçleri alma](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)

    Örnek REST protokol örneği:

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    Örnek yanıt:

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "access_token": "eyJ0eXAi…",
        "expires_on": "1586984735",
        "resource": "https://atlas.microsoft.com/",
        "token_type": "Bearer",
        "client_id": "..."
    }
    ```

6. Azure işlevi HttpTrigger için güvenliği yapılandırma

   * [İşlev erişim anahtarı oluşturma](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#authorization-keys)
   * Üretimde Azure işlevi için [GÜVENLI HTTP uç noktası](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production) .
   
7. Web uygulaması Azure Maps web SDK 'sını yapılandırın. 

    ```javascript
    //URL to custom endpoint to fetch Access token
    var url = 'https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>';

    var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                view: "Auto",
            authOptions: {
                authType: "anonymous",
                clientId: "<insert>", // azure map account client id
                getToken: function(resolve, reject, map) {
                    fetch(url).then(function(response) {
                        return response.text();
                    }).then(function(token) {
                        resolve(token);
                    });
                }
            }
        });

        // use the following events to debug, you can remove them at any time.
        map.events.add("tokenacquired", function () {
            console.log("token acquired");
        });
        map.events.add("error", function (err) {
            console.log(JSON.stringify(err.error));
        });
    ```

## <a name="grant-role-based-access"></a>Rol tabanlı erişim verme

Sistem tarafından atanan kimliği bir veya daha fazla Azure rol tanımına atayarak *Azure rol tabanlı erişim denetimi (Azure RBAC)* erişimi verirsiniz. Azure haritalar için kullanılabilen Azure rol tanımlarını görüntülemek için **erişim denetimi 'ne (IAM)** gidin. **Roller**' i seçin ve ardından *Azure Maps* ile başlayan roller için arama yapın.

1. **Azure haritalar hesabınıza** gidin. **Erişim denetimi (IAM)**  >  **rol atamasını** seçin.

    > [!div class="mx-imgBorder"]
    > ![Azure RBAC kullanarak erişim verme](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. **Rol atamaları** sekmesinde, **rol** altında **Azure Maps veri okuyucusu** veya **Azure haritalar veri katılımcısı** gibi yerleşik bir Azure Maps rol tanımı seçin. **Erişim ata** altında **işlev uygulaması**' yi seçin. Asıl ada göre ' yi seçin. Sonra **Kaydet**'i seçin.

   * [Azure rolleri atama](../role-based-access-control/role-assignments-portal.md)hakkında ayrıntılara bakın.

> [!WARNING]
> Azure Maps yerleşik rol tanımları birçok Azure haritalar REST API 'Lerine çok büyük bir yetkilendirme erişimi sağlar. API 'Lerin en düşük düzeyde erişimini kısıtlamak için bkz. [özel rol tanımı oluşturma ve sistem tarafından atanan kimliği](../role-based-access-control/custom-roles.md) özel rol tanımına atama. Bu, uygulamanın Azure Maps 'e erişmesi için gereken en düşük ayrıcalığa olanak sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Tek sayfalı uygulama senaryosuna daha fazla anlama:
> [!div class="nextstepaction"]
> [Tek sayfalı uygulama](../active-directory/develop/scenario-spa-overview.md)

Azure haritalar hesabınız için API kullanım ölçümlerini bulun:
> [!div class="nextstepaction"]
> [Kullanım ölçümlerini görüntüleme](how-to-view-api-usage.md)

Azure AD 'nin Azure Maps ile nasıl tümleştirileceğini gösteren diğer örnekleri göz atın:
> [!div class="nextstepaction"]
> [Azure haritalar örnekleri](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)