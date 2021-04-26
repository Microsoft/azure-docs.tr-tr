---
title: Kullanıcı oturumu açma ile tek sayfalı bir uygulamanın güvenliğini sağlama
titleSuffix: Azure Maps
description: Azure Haritalar Web SDK ile Azure AD çoklu oturum açmayı destekleyen tek sayfalı bir uygulama yapılandırma.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 60d8dc45fb26ea210b1827a6938716474faa0304
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895622"
---
# <a name="secure-a-single-page-application-with-user-sign-in"></a>Kullanıcı oturumu açma ile tek sayfalı bir uygulamanın güvenliğini sağlama

Aşağıdaki kılavuz, bir içerik sunucusunda barındırılan veya en az sayıda Web sunucusu bağımlılığı olan bir uygulamayla ilgilidir. Uygulama, korumalı kaynakları yalnızca Azure AD kullanıcıları için güvenli hale getirir. Senaryonun amacı, Web uygulamasının Azure AD 'de kimlik doğrulaması yapmasını ve Kullanıcı adına Azure Maps REST API 'Lerini çağırabilmesini sağlar.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Azure AD 'de uygulama kaydı oluşturma

Kullanıcıların oturum açması için Azure AD 'de Web uygulaması oluşturun. Web uygulaması, Kullanıcı erişimini Azure Maps REST API 'Lerine devreder.

1. Azure Portal Azure hizmetleri listesinde,   >    >  **Yeni kayıt** uygulama kayıtları Azure Active Directory ' ni seçin.  

    > [!div class="mx-imgBorder"]
    > ![Uygulama kaydı](./media/how-to-manage-authentication/app-registration.png)

2. Bir **ad** girin, bir **destek hesabı türü** seçin, Azure AD 'nin belirteci vermesini ve harita denetiminin barındırıldığı URL 'yi temsil edecek bir yeniden yönlendirme URI 'si sağlayın. Ayrıntılı bir örnek için lütfen bkz. [Azure Maps Azure AD örnekleri](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant). Ardından **Kaydet**’i seçin.  

3. Azure haritalar 'a temsil edilen API izinleri atamak için uygulamaya gidin. **Uygulama kayıtları** altında, **API izinleri**  >  **izin Ekle**' yi seçin. **Kuruluşumun kullandığı API 'ler** altında **Azure haritaları**' nı arayıp seçin.

    > [!div class="mx-imgBorder"]
    > ![Uygulama API 'SI izinleri ekleme](./media/how-to-manage-authentication/app-permissions.png)

4. **Azure haritalar**' ın yanındaki onay kutusunu işaretleyin ve ardından **izin Ekle**' yi seçin.

    > [!div class="mx-imgBorder"]
    > ![Uygulama API 'SI izinlerini seçin](./media/how-to-manage-authentication/select-app-permissions.png)

5. Etkinleştirin `oauth2AllowImplicitFlow` . Etkinleştirmek için, uygulama kaydlarınızın **bildirim** bölümünde olarak ayarlanır `oauth2AllowImplicitFlow` `true` .

6. Azure AD uygulama KIMLIĞI ve Azure AD kiracı KIMLIĞINI, uygulama kaydından web SDK 'sında kullanmak üzere kopyalayın. Azure AD uygulama kaydı ayrıntılarını ve `x-ms-client-id` Azure Map hesabından Web SDK 'sına ekleyin.

    ```javascript
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js" />
        <script>
            var map = new atlas.Map("map", {
                center: [-122.33, 47.64],
                zoom: 12,
                language: "en-US",
                authOptions: {
                    authType: "aad",
                    clientId: "<insert>",  // azure map account client id
                    aadAppId: "<insert>",  // azure ad app registration id
                    aadTenant: "<insert>", // azure ad tenant id
                    aadInstance: "https://login.microsoftonline.com/"
                }
            });
        </script>   
    ```

7. Kullanıcılar veya gruplar için Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) yapılandırın. [Azure RBAC 'i etkinleştirmek için aşağıdaki bölümlere](#grant-role-based-access-for-users-to-azure-maps)bakın.
   
[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Sonraki adımlar

Tek sayfalı uygulama senaryosuna daha fazla anlama:
> [!div class="nextstepaction"]
> [Tek sayfalı uygulama](../active-directory/develop/scenario-spa-overview.md)

Azure haritalar hesabınız için API kullanım ölçümlerini bulun:
> [!div class="nextstepaction"]
> [Kullanım ölçümlerini görüntüleme](how-to-view-api-usage.md)

Azure AD 'nin Azure Maps ile nasıl tümleştirileceğini gösteren örnekleri araştırma:
> [!div class="nextstepaction"]
> [Azure haritalar örnekleri](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)