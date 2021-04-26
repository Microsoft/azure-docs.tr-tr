---
title: Azure AD ve Azure haritalar REST API 'Leri ile giriş kısıtlı cihazı güvenli hale getirme
titleSuffix: Azure Maps
description: Azure AD 'de oturum açmayı destekleyen ve Azure haritalar REST API 'Lerini çağıran, tarayıcı için daha az bir uygulama yapılandırma.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 3833cbfd0802f334e482203d269984eb0e299797
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895639"
---
# <a name="secure-an-input-constrained-device-with-azure-ad-and-azure-maps-rest-apis"></a>Azure AD ve Azure haritalar REST API 'Leri ile giriş kısıtlı bir cihazın güvenliğini sağlama

Bu kılavuzda, gizli dizileri güvenli bir şekilde depolayabilen veya tarayıcı girişini kabul etmede ortak uygulamaların veya cihazların güvenliğini sağlama hakkında bilgiler Bu tür uygulamalar, IoT veya nesnelerin interneti kategorisine girer. Bu uygulamalara bazı örnekler şunlar olabilir: akıllı TV cihazları veya algılayıcı verileri yayma uygulamaları. 

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Azure AD 'de uygulama kaydı oluşturma

> [!NOTE]
> * **Önkoşul okuma:** [Senaryo: Web API 'lerini çağıran masaüstü uygulaması](../active-directory/develop/scenario-desktop-overview.md)
> * Aşağıdaki senaryo, belirteç almak için bir Web tarayıcısı içermeyen cihaz kod akışını kullanır.

Azure AD oturum açma özelliğini etkinleştirmek için Azure AD 'de cihaz tabanlı uygulamayı oluşturun. Bu uygulamaya Azure Maps REST API 'Leri erişimi verilecektir.

1. Azure Portal Azure hizmetleri listesinde,   >    >  **Yeni kayıt** uygulama kayıtları Azure Active Directory ' ni seçin.  

    > [!div class="mx-imgBorder"]
    > ![Uygulama kaydı](./media/how-to-manage-authentication/app-registration.png)

2. Bir **ad** girin, **bu kuruluş dizininde yalnızca** **Desteklenen hesap türü** olarak hesaplar ' ı seçin. **Yeniden yönlendirme URI 'Lerinde** **ortak istemci/yerel (mobil & Masaüstü)** belirtin ve ardından `https://login.microsoftonline.com/common/oauth2/nativeclient` değere ekleyin. Daha fazla ayrıntı için lütfen bkz. [Web API 'lerini çağıran Azure AD masaüstü uygulaması: uygulama kaydı](../active-directory/develop/scenario-desktop-app-registration.md). Ardından uygulamayı **kaydedin** .

    > [!div class="mx-imgBorder"]
    > ![Ad ve yeniden yönlendirme URI 'si için uygulama kaydı ayrıntılarını ekleyin](./media/azure-maps-authentication/devicecode-app-registration.png)

3. **Kimlik doğrulaması** ' na gidin ve **uygulamayı ortak istemci olarak değerlendir**' i etkinleştirin. Bu, Azure AD ile cihaz kodu kimlik doğrulamasını etkinleştirir.
    
    > [!div class="mx-imgBorder"]
    > ![Uygulama kaydını ortak istemci olarak etkinleştir](./media/azure-maps-authentication/devicecode-public-client.png)

4.  Azure haritalar 'a temsil edilen API izinleri atamak için uygulamaya gidin. Ardından **API izinleri**  >  **bir izin Ekle**' yi seçin. **Kuruluşumun kullandığı API 'ler** altında **Azure haritaları**' nı arayıp seçin.

    > [!div class="mx-imgBorder"]
    > ![Uygulama API 'SI izinleri ekleme](./media/how-to-manage-authentication/app-permissions.png)

5. **Azure haritalar**' ın yanındaki onay kutusunu işaretleyin ve ardından **izin Ekle**' yi seçin.

    > [!div class="mx-imgBorder"]
    > ![Uygulama API 'SI izinlerini seçin](./media/how-to-manage-authentication/select-app-permissions.png)

6. Kullanıcılar veya gruplar için Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) yapılandırın. Bkz. [Kullanıcılar Için Azure Maps 'e rol tabanlı erişim verme](#grant-role-based-access-for-users-to-azure-maps).

7. Uygulamada belirteç akışı almak için kod ekleyin, uygulama ayrıntıları için bkz. [cihaz kodu akışı](../active-directory/develop/scenario-desktop-acquire-token.md#device-code-flow). Belirteçleri alırken, `user_impersonation` önceki adımlarda seçili olan kapsama başvurun:

> [!Tip]
> Erişim belirteçleri almak için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanın. Bkz [. Web API 'lerini çağıran masaüstü uygulamasında öneriler: kod yapılandırma](../active-directory/develop/scenario-desktop-app-configuration.md)

8. Azure AD 'den alınan belirteç ile HTTP isteğini ve geçerli bir HTTP istemcisiyle istek gönderilmesini oluşturun.

### <a name="sample-request"></a>Örnek istek
Bir orta nokta ve yarıçap kullanarak bir daire geometrisi olarak temsil edilen basit bölge listesini yüklemek için örnek bir istek gövdesi aşağıda verilmiştir.

```http
POST /mapData/upload?api-version=1.0&dataFormat=geojson
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

 Aşağıdaki örnek istek gövdesi GeoJSON ' dır:
```json
{
    "type": "FeatureCollection",
    "features": [{
        "type": "Feature",
        "geometry": {
            "type": "Point",
            "coordinates": [-122.126986, 47.639754]
        },
        "properties": {
            "geometryId": "001",
            "radius": 500
        }
    }]
}
```

### <a name="sample-response"></a>Örnek yanıt:

Bilgisinde
```http
Location: https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0
Access-Control-Expose-Headers: Location
```

Bölümü
```json
{
  "operationId": "{operationId}",
  "status": "Succeeded",
  "created": "2020-01-02 1:02:03 AM +00:00",
  "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{resourceId}?api-version=1.0"
}
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Sonraki adımlar

Azure haritalar hesabınız için API kullanım ölçümlerini bulun:
> [!div class="nextstepaction"]
> [Kullanım ölçümlerini görüntüleme](how-to-view-api-usage.md)