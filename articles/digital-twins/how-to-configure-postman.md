---
title: Postman Yapılandırma-Azure dijital TWINS | Microsoft Docs
description: Azure dijital TWINS API 'Lerini test etmek için Postman yapılandırma ve kullanma hakkında bilgi edinin.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 6a001d6b501a22b4b07599792a64af735c5d4d9b
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74090497"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Azure dijital TWINS için Postman 'ı yapılandırma

Bu makalede, Azure Digital TWINS yönetim API 'Lerini ile etkileşime geçmek ve test etmek üzere Postman REST istemcisinin nasıl yapılandırılacağı açıklanır. Özellikle şunları açıklar:

* Azure Active Directory uygulamasını OAuth 2,0 örtük izin akışını kullanacak şekilde yapılandırma.
* Yönetim API 'lerinize belirteç oluşturma HTTP istekleri yapmak için Postman REST istemcisini kullanma.
* Yönetim API 'lerinize çok parçalı GÖNDERI istekleri yapmak için Postman 'ı kullanma.

## <a name="postman-summary"></a>Postman Özeti

Yerel test ortamınızı hazırlamak için [Postman](https://www.getpostman.com/) gıbı bir rest istemci aracı kullanarak Azure dijital TWINS 'i kullanmaya başlayın. Postman istemcisi, karmaşık HTTP isteklerinin hızla oluşturulmasına yardımcı olur. [Www.getpostman.com/apps](https://www.getpostman.com/apps)'e giderek Postman istemcisinin Masaüstü sürümünü indirin.

[Postman](https://www.getpostman.com/) , önemli http istek işlevlerini yararlı bir masaüstü ve EKLENTI tabanlı GUI 'ye bulan bir rest test aracıdır.

Çözüm geliştiricileri Postman istemcisi aracılığıyla HTTP isteği türünü (*gönderi*, *Al*, *Güncelleştir*, *Düzeltme Eki*uygulama ve *silme*), API 'yi çağırmak için API uç noktası ve SSL kullanımını belirtebilir. Postman ayrıca HTTP istek üstbilgileri, parametreleri, form verileri ve gövdeleri eklemeyi destekler.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>OAuth 2,0 örtük izin akışını kullanmak için Azure Active Directory yapılandırma

Azure Active Directory uygulamanızı OAuth 2,0 örtük izin akışını kullanacak şekilde yapılandırın.

1. Uygulama kaydınız için **API izinleri** bölmesini açın. **Izin Ekle** düğmesini seçin. **API Izinleri iste** bölmesinde **Kuruluşumun kullandığı API 'leri** seçin ve ardından şunu arayın:
    
    1. `Azure Digital Twins`. **Azure dijital TWINS** API 'sini seçin.

        [![arama API 'SI veya Azure dijital TWINS](../../includes/media/digital-twins-permissions/aad-aap-search-api-dt.png)](../../includes/media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Alternatif olarak, `Azure Smart Spaces Service`için arama yapın. **Azure akıllı boşluklar hizmeti** API 'sini seçin.

        [Azure akıllı alanları için Search API ![](../../includes/media/digital-twins-permissions/aad-app-search-api.png)](../../includes/media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > Görüntülenecek Azure AD API adı ve KIMLIĞI, kiracınıza bağlı olarak değişir:
    > * Test kiracının ve müşteri hesaplarının `Azure Digital Twins`araması gerekir.
    > * Diğer Microsoft hesaplarının `Azure Smart Spaces Service`araması gerekir.

1. Seçilen API, aynı **istek API 'si izinleri** bölmesinde **Azure dijital TWINS** olarak gösterilir. **Oku (1)** açılır öğesini seçin ve ardından **oku. yazma** onay kutusunu seçin. **Izin Ekle** düğmesini seçin.

    [![API izinleri ekleme](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Kuruluşunuzun ayarlarına bağlı olarak, bu API 'ye yönetici erişimi sağlamak için ek adımlar gerçekleştirmeniz gerekebilir. Daha fazla bilgi için yöneticinize başvurun. Yönetici erişimi onaylandıktan sonra **API izinleri** BÖLMESINDEKI **yönetici onayı gerekli** sütunu, API 'leriniz için aşağıdakine benzer şekilde görünür:

    [Yönetici onay onayını ![](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png)](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

1. İkinci bir **yeniden yönlendirme URI 'sini** `https://www.getpostman.com/oauth2/callback`için yapılandırın.

    [![Postman yeniden yönlendirme URI 'SI Ekle](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. [Uygulamanın bir **ortak istemci**olarak kaydedildiğinden](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration)emin olmak için, uygulama kaydlarınızın **kimlik doğrulama** bölmesini açın ve bu bölmeyi aşağı kaydırın. **Varsayılan istemci türü** bölümünde, **uygulamayı ortak istemci olarak değerlendir**' **i seçin ve** **Kaydet**' i tıklayın.

    Manifest. JSON 'inizdeki **oauth2AllowImplicitFlow** ayarını etkinleştirmek için **erişim belirteçlerini** denetleyin.

    [![ortak istemci yapılandırma ayarı](../../includes/media/digital-twins-permissions/aad-public-client.png)](../../includes/media/digital-twins-permissions/aad-public-client.png#lightbox)

1. Azure Active Directory uygulamanızın **uygulama kimliğini** kopyalayın ve saklayın. Bu, izleyen adımlarda kullanılır.

   [![Azure Active Directory uygulama KIMLIĞI](../../includes/media/digital-twins-permissions/aad-app-reg-app-id.png)](../../includes/media//digital-twins-permissions/aad-app-reg-app-id.png#lightbox)


## <a name="obtain-an-oauth-20-token"></a>OAuth 2,0 belirteci edinme

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Azure Active Directory belirtecini almak için Postman ayarlayın ve yapılandırın. Daha sonra, alınan belirteci kullanarak Azure dijital TWINS 'e kimliği doğrulanmış bir HTTP isteği oluşturun:

1. Uygulamayı indirmek için [www.getpostman.com](https://www.getpostman.com/) adresine gidin.
1. **YETKILENDIRME URL** 'nizin doğru olduğundan emin olun. Şu biçimde olmalıdır:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Ad  | Şununla değiştir | Örnek |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Kiracınızın veya kuruluşunuzun adı | `microsoft` |

1. **Yetkilendirme** sekmesini seçin, **OAuth 2,0**' i seçin ve ardından **Yeni erişim belirteci al**' ı seçin.

    | Alan  | Değer |
    |---------|---------|
    | Verme türü | `Implicit` |
    | Geri çağırma URL 'SI | `https://www.getpostman.com/oauth2/callback` |
    | Kimlik doğrulama URL 'SI | **2. adımdaki** **yetkilendirme URL 'sini** kullanın |
    | İstemci Kimliği | Önceki bölümden oluşturulmuş veya yeniden kullanılan Azure Active Directory uygulamasının **uygulama kimliğini** kullanın |
    | Kapsam | Boş bırakın |
    | Durum | Boş bırakın |
    | İstemci kimlik doğrulaması | `Send as Basic Auth header` |

1. İstemci şu şekilde görünmelidir:

    [![Postman istemci örneği](media/how-to-configure-postman/postman-oauth-token.png)](media/how-to-configure-postman/postman-oauth-token.png#lightbox)

1. **Istek belirtecini**seçin.
  
1. Aşağı kaydırın ve **belirteci kullan**' ı seçin.

## <a name="make-a-multipart-post-request"></a>Çok parçalı bir POST isteği oluşturun

Önceki adımları tamamladıktan sonra, kimliği doğrulanmış bir HTTP çok parçalı GÖNDERI isteği oluşturmak için Postman 'ı yapılandırın:

1. **Üst bilgiler** sekmesi ALTıNA bir http istek üst bilgisi anahtarı **Content-Type** değeri `multipart/mixed`ekleyin.

   [![Içerik türü parçalı/karışık](media/how-to-configure-postman/content-type.png)](media/how-to-configure-postman/content-type.png#lightbox)

1. Metin olmayan verileri dosyalara serileştirme. JSON verileri bir JSON dosyası olarak kaydedilir.
1. **Gövde** sekmesinde `form-data`' yi seçin. 
1. Her dosyayı bir **anahtar** adı atayarak ekleyin, `File`öğesini seçin.
1. Sonra, **Dosya Seç** düğmesini kullanarak her bir dosyayı seçin.

   [![Postman istemci örneği](media/how-to-configure-postman/form-body.png)](media/how-to-configure-postman/form-body.png#lightbox)

   >[!NOTE]
   > * Postman istemcisi, çok parçalı öbeklerin el ile atanmış bir **Içerik türü** veya **Content-Disposition**olmasını gerektirmez.
   > * Her bölüm için bu üst bilgileri belirtmeniz gerekmez.
   > * Tüm istek için `multipart/mixed` veya başka bir uygun **Içerik türü** seçmelisiniz.

1. Son olarak, çok parçalı HTTP POST isteğinizi göndermek için **Gönder** ' i seçin. `200` veya `201` durum kodu başarılı bir isteği gösterir. Ayrıca ilgili yanıt iletisini görürsünüz.

## <a name="next-steps"></a>Sonraki adımlar

- Dijital TWINS yönetim API 'Leri ve bunların nasıl kullanılacağı hakkında bilgi edinmek için [Azure Digital TWINS yönetim API 'Lerini nasıl kullanacağınızı](how-to-navigate-apis.md)okuyun.

- [Azure dijital TWINS 'in varlıklarına blob eklemek](./how-to-add-blobs.md)için çok parçalı istekleri kullanın.

- Yönetim API 'Lerinde kimlik doğrulama hakkında bilgi edinmek için, [API 'Lerle kimlik doğrulaması](./security-authenticating-apis.md)' nı okuyun.
