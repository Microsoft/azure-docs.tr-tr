---
title: Etkileşimli Çoklu oturum açma ile bir Web uygulamasının güvenliğini sağlama
titleSuffix: Azure Maps
description: OpenID Connect protokolünü kullanarak Azure Maps web SDK ile Azure AD çoklu oturum açmayı destekleyen bir Web uygulaması yapılandırma.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: ebdc4b219e0840c18e6bef8ebfe9b8eefa8faf3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895578"
---
# <a name="secure-a-web-application-with-user-sign-in"></a>Kullanıcı oturumu açma ile bir Web uygulamasını güvenli hale getirme

Aşağıdaki kılavuz, Web sunucularında barındırılan bir uygulamayla ilgilidir, birden çok iş senaryosunu korur ve Web sunucularına dağıtır. Uygulama, korumalı kaynakları yalnızca Azure AD kullanıcılarına karşı güvenli hale sağlama gereksinimine sahiptir. Senaryonun amacı, Web uygulamasının Azure AD 'de kimlik doğrulaması yapmasını ve Kullanıcı adına Azure Maps REST API 'Lerini çağırabilmesini sağlar.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Azure AD 'de uygulama kaydı oluşturma

Kullanıcıların oturum açması için Azure AD 'de Web uygulaması oluşturmanız gerekir. Bu Web uygulaması daha sonra Azure Maps REST API 'Lerine Kullanıcı erişimi devredebilir.

1. Azure Portal Azure hizmetleri listesinde,   >    >  **Yeni kayıt** uygulama kayıtları Azure Active Directory ' ni seçin.  

    > [!div class="mx-imgBorder"]
    > ![Uygulama kaydı](./media/how-to-manage-authentication/app-registration.png)

2. Bir **ad** girin, bir **destek hesabı türü** seçin, Azure AD 'nin belirteci vermesini ve harita denetiminin barındırıldığı URL 'yi temsil edecek bir yeniden yönlendirme URI 'si sağlayın. Daha fazla ayrıntı için lütfen bkz. Azure AD [senaryosu: kullanıcılar oturum açan Web uygulaması](../active-directory/develop/scenario-web-app-sign-user-overview.md). Azure AD senaryosundan belirtilen adımları izleyin.  

3. Uygulama kaydı tamamlandıktan sonra, uygulama oturum açma 'nın kullanıcılar için çalıştığından emin olun. Oturum açma işe başladıktan sonra uygulamaya Azure Maps REST API 'Lerine atanmış erişim verilebilir.
    
4.  Azure haritalar 'a temsil edilen API izinleri atamak için uygulamaya gidin. Ardından **API izinleri**  >  **bir izin Ekle**' yi seçin. **Kuruluşumun kullandığı API 'ler** altında **Azure haritaları**' nı arayıp seçin.

    > [!div class="mx-imgBorder"]
    > ![Uygulama API 'SI izinleri ekleme](./media/how-to-manage-authentication/app-permissions.png)

5. **Azure haritalar**' ın yanındaki onay kutusunu işaretleyin ve ardından **izin Ekle**' yi seçin.

    > [!div class="mx-imgBorder"]
    > ![Uygulama API 'SI izinlerini seçin](./media/how-to-manage-authentication/select-app-permissions.png)

6. Web uygulamasının, uygulama kaydını bir uygulama gizli anahtarı ile yapılandırarak Azure haritalar REST API 'Lerini aramasını etkinleştirin. ayrıntılı adımlar Için, [Web API 'lerini çağıran bir Web uygulaması: uygulama kaydı](../active-directory/develop/scenario-web-app-call-api-app-registration.md)' na bakın. Kullanıcı adına Azure AD kimlik doğrulaması için bir parola gerekir. Uygulama kayıt sertifikası veya gizli dizi, Web uygulamasının Azure AD kimlik doğrulaması için alabilmesi için güvenli bir depoda depolanmalıdır. 
   
   * Uygulama zaten bir Azure AD uygulama kaydı yapılandırmışsa ve gizli dizi bu adım atlanabilir.

> [!Tip]
> Uygulama bir Azure ortamında barındırılıyorsa, [Azure kaynakları Için yönetilen kimliklerin](../active-directory/managed-identities-azure-resources/overview.md) ve Azure Key Vault gizli dizileri veya sertifikalara erişim için [bir erişim belirteci edinerek](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) bir Azure Key Vault örneği kullanmanızı öneririz. Gizli dizileri almak üzere Azure Key Vault bağlanmak için bkz. [yönetilen kimlik üzerinden bağlanmak için öğretici](../key-vault/general/tutorial-net-create-vault-azure-web-app.md).
   
7. Bir belirtece erişmek için Azure Maps web SDK 'Sı için güvenli bir belirteç uç noktası uygulayın. 
   
   * Örnek belirteç denetleyicisi için bkz. [Azure Maps Azure AD örnekleri](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/blob/master/src/OpenIdConnect/AzureMapsOpenIdConnectv1/AzureMapsOpenIdConnect/Controllers/TokenController.cs). 
   * Bir AspNetCore uygulaması veya diğeri için bkz. Azure AD belgelerinden [uygulama belirteci alma](../active-directory/develop/scenario-web-app-call-api-acquire-token.md) .
   * Güvenli belirteç uç noktası, kimliği doğrulanmış ve yetkili kullanıcının Azure haritalar REST API 'Lerini çağırması için bir erişim belirteci döndürmekten sorumludur.

8. Kullanıcılar veya gruplar için Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) yapılandırın. Bkz. [kullanıcılar için rol tabanlı erişim verme](#grant-role-based-access-for-users-to-azure-maps).

9. Güvenli belirteç uç noktasına erişmek için Web uygulaması sayfasını Azure Maps web SDK 'Sı ile yapılandırın. 

```javascript
var map = new atlas.Map("map", {
        center: [-122.33, 47.64],
        zoom: 12,
        language: "en-US",
        authOptions: {
            authType: "anonymous",
            clientId: "<insert>",  // azure map account client id
            getToken: function (resolve, reject, map) {
                var xhttp = new XMLHttpRequest();
                xhttp.open("GET", "/api/token", true); // the url path maps to the token endpoint.
                xhttp.onreadystatechange = function () {
                    if (this.readyState === 4 && this.status === 200) {
                        resolve(this.responseText);
                    } else if (this.status !== 200) {
                        reject(this.responseText);
                    }
                };

                xhttp.send();
            }
        }
    });
    map.events.add("tokenacquired", function () {
        console.log("token acquired");
    });
    map.events.add("error", function (err) {
        console.log(JSON.stringify(err.error));
    });
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Sonraki adımlar

Web uygulaması senaryosuna daha fazla anlama:
> [!div class="nextstepaction"]
> [Senaryo: kullanıcılarda oturum açan Web uygulaması](../active-directory/develop/scenario-web-app-sign-user-overview.md)

Azure haritalar hesabınız için API kullanım ölçümlerini bulun:
> [!div class="nextstepaction"]
> [Kullanım ölçümlerini görüntüleme](how-to-view-api-usage.md)

Azure AD 'nin Azure Maps ile nasıl tümleştirileceğini gösteren örnekleri araştırma:
> [!div class="nextstepaction"]
> [Azure haritalar Azure AD Web uygulaması örnekleri](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/OpenIdConnect)