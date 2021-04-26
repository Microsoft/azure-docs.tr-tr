---
title: Müşteri tarafından yönetilen anahtarları veya BYOK REST API kullanın
description: Bu öğreticide, müşteri tarafından yönetilen anahtarları kullanın veya bir Azure Media Services depolama hesabıyla kendi anahtarınızı (BYOK) getirin.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c8a5b682e2ac4879d2181bdb069cf554bad512d9
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282395"
---
# <a name="tutorial-use-customer-managed-keys-or-byok-with-media-services-rest-api"></a>Öğretici: Media Services REST API müşteri tarafından yönetilen anahtarları veya BYOK kullanın

2020-05-01 API 'SI ile müşteri tarafından yönetilen bir RSA anahtarını, sistem tarafından yönetilen kimliği olan Azure Media Services bir hesapla kullanabilirsiniz. Bu öğretici, Azure hizmetlerine REST istekleri göndermek için bir Postman koleksiyonu ve ortamı içerir. Kullanılan hizmetler şunlardır:

- Postman için Azure Active Directory (Azure AD) uygulama kaydı
- Microsoft Graph API
- Azure Depolama
- Azure Key Vault
- Azure Media Services

Bu öğreticide, şu şekilde Postman kullanmayı öğreneceksiniz:

> [!div class="checklist"]
> - Azure hizmetleriyle kullanılacak belirteçleri alın.
> - Bir kaynak grubu ve depolama hesabı oluşturun.
> - Sistem tarafından yönetilen bir kimlikle Media Services hesabı oluşturun.
> - Müşteri tarafından yönetilen bir RSA anahtarı depolamak için bir Anahtar Kasası oluşturun.
> - Media Services hesabını, RSA anahtarını depolama hesabıyla kullanacak şekilde güncelleştirin.
> - Postman 'da değişkenleri kullanın.

Azure aboneliğiniz yoksa [ücretsiz bir deneme hesabı oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

1. Uygun izinlerle bir hizmet sorumlusu kaydedin.
1. [Postman](https://www.postman.com)'yi yükleme.
1. Bu öğretici için Postman koleksiyonunu [Azure örneklerine indirin: Media-Services-Customer-Managed-Keys-bYok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok).

### <a name="register-a-service-principal-with-the-needed-permissions"></a>Gerekli izinlere sahip bir hizmet sorumlusu kaydetme

1. [Hizmet sorumlusu oluşturun](../../active-directory/develop/howto-create-service-principal-portal.md).
1. 2. seçenek: hizmet sorumlusu gizliliğini almak için [Yeni bir uygulama parolası oluşturma](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) sayfasına gidin.

   > [!IMPORTANT]
   >Daha sonra kullanmak üzere gizli anahtarı kopyalayıp kaydedin. Portalın gizli sayfasını kapattıktan sonra gizli dizi erişimine erişemezsiniz.

1. Aşağıdaki ekran görüntüsünde gösterildiği gibi, hizmet sorumlusuna izinler atayın:

   :::image type="complex" source="./media/tutorial-byok/service-principal-permissions-1.png" alt-text="Hizmet sorumlusu için gereken izinleri gösteren ekran görüntüsü.":::
   İzinler hizmete, izin adına, türe ve sonra açıklamaya göre listelenir. Azure Key Vault: kullanıcı kimliğine bürünme, temsilci olarak Azure Key Vault tam erişim. Azure hizmet yönetimi: kullanıcı kimliğine bürünme, temsilcili, kuruluş kullanıcısı olarak Azure hizmet yönetimine erişme. Azure depolama: Kullanıcı kimliğe bürünme, temsilci, Azure depolama erişimi. Medya Hizmetleri: kullanıcı kimliğine bürünme, temsilci, erişim medya Hizmetleri. Microsoft Graph: User. Read, temsilcili, oturum açın ve kullanıcı profilini okuyun.
   :::image-end:::

### <a name="install-postman"></a>Postman yükleme

Azure ile kullanmak için Postman 'ı henüz yüklemediyseniz, [Postman.com](https://www.postman.com/)adresinden edinebilirsiniz.

### <a name="download-and-import-the-collection"></a>Koleksiyonu indirme ve içeri aktarma

Bu öğretici için Postman koleksiyonunu [Azure örneklerine indirin: Media-Services-Customer-Managed-Keys-bYok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok).

## <a name="install-the-postman-collection-and-environment"></a>Postman koleksiyonunu ve ortamını yükleme

1. Postman 'ı çalıştırın.
1. **İçeri aktar**'ı seçin.
1. **Dosyaları karşıya yükle**' yi seçin.
1. Koleksiyonu ve ortam dosyalarını kaydettiğiniz yere gidin.
1. Koleksiyon ve ortam dosyalarını seçin.
1. **Aç**’ı seçin. Dosyaların bir API olarak içeri aktarılmadığını ancak koleksiyonlar olarak göründüğünü belirten bir uyarı görüntülenir. Bu uyarı iyidir. Bu, istediğiniz şeydir.

Koleksiyonda artık koleksiyonlarınız BYOK olarak gösteriliyor. Ayrıca, ortam değişkenleri ortamlarınızda görüntülenir.

### <a name="understand-the-rest-api-requests-in-the-collection"></a>Koleksiyondaki REST API isteklerini anlayın

Koleksiyon aşağıdaki REST API isteklerini sağlar.

> [!NOTE]
>
>- İsteklerin belirtilen sırada gönderilmesi gerekir.
>- Çoğu isteğin, sıradaki bir sonraki istek için dinamik olarak genel değişkenler oluşturan test betikleri vardır.
>- Genel değişkenleri el ile oluşturmanız gerekmez.

Postman 'da, parantez içinde bulunan bu değişkenleri görürsünüz. Örneğin, `{{bearerToken}}`.

1. Azure AD belirteci al: test, **Yataertoken** genel değişkenini ayarlar.
2. Microsoft Graph belirteci al: test, **Graphtoken** genel değişkenini ayarlar.
3. Hizmet sorumlusu ayrıntılarını al: test, **Serviceprincipalobjectıd** genel değişkenini ayarlar.
4. Depolama hesabı oluşturma: test **Storageaccountıd** genel değişkenini ayarlar.
5. Sistem tarafından yönetilen kimliğe sahip bir Media Services hesabı oluşturun: test, **PrincipalId** genel değişkenini ayarlar.
6. Hizmet sorumlusuna erişim vermek için bir Anahtar Kasası oluşturun: test, **Keyvaultıd** genel değişkenini ayarlar.
7. Key Vault belirteci al: test, **Keyvaulttoken** genel değişkenini ayarlar.
8. Anahtar kasasında RSA anahtarını oluşturun: test, **keyId** genel değişkenini ayarlar.
9. Depolama hesabıyla anahtarı kullanmak için Media Services hesabını güncelleştirin: Bu istek için test betiği yok.

## <a name="define-environment-variables"></a>Ortam değişkenlerini tanımlama

1. İndirdiğiniz ortama geçmek için ortamın açılan listesini seçin.
1. Postman 'da ortam değişkenlerinizi oluşturun. Parantez içinde bulunan değişkenler olarak da kullanılır. Örneğin, `{{tenantId}}`.

    - **Tenantıd**: kiracı Kimliğiniz.
    - **Serviceprincipalıd**: portal veya CLI gibi sık kullanılan yöntemden oluşturduğunuz HIZMET sorumlusunun kimliği.
    - **Servicesprincipalsecret**: hizmet sorumlusu için oluşturulan gizli dizi.
    - **abonelik**: abonelik Kimliğiniz.
    - **Storagename**: depolamaya vermek istediğiniz ad.
    - **AccountName**: kullanmak istediğiniz Media Services hesap adı.
    - **Keyvaultname**: kullanmak istediğiniz Anahtar Kasası adı.
    - **ResourceLocation**: bir **merkezde** veya kaynaklarınızı yerleştirmek istediğiniz konum. Bu koleksiyon yalnızca **merkezileştirme** ile test edilmiştir.
    - **resourceGroup**: kaynak grubu adı.

    Aşağıdaki değişkenler Azure kaynaklarıyla çalışmaya yönelik standarttır. Bu nedenle, bunları değiştirmenize gerek yoktur.

    - **Armresource**: `https://management.core.windows.net`
    - **Graphresource**: `https://graph.windows.net/`
    - **Keyvaultresource**: `https://vault.azure.net`
    - **Ermenistan**: `management.azure.com`
    - **Graphendpoint**: `graph.windows.net`
    - **Aadendpoint**: `login.microsoftonline.com`
    - **Keyvaultdomainsuffix**: `vault.azure.net`

## <a name="send-the-requests"></a>İstekleri Gönder

Ortam değişkenlerinizi tanımladıktan sonra, önceki dizide tek seferde istekleri çalıştırabilirsiniz. Ya da, koleksiyonu çalıştırmak için Postman 'nın Çalıştırıcısı ' nı kullanabilirsiniz.

## <a name="change-the-key"></a>Anahtarı değiştirme

Media Services, anahtarın ne zaman değiştirildiğini otomatik olarak algılar. Bu işlemi test etmek için aynı anahtar için başka bir anahtar sürümü oluşturun. Media Services, anahtarı 15 dakikadan kısa bir süre içinde algılayabilmelidir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Oluşturduğunuz kaynakları kullanmaya devam edemezseniz ve *faturalandırılmaya devam* etmek istemiyorsanız, bunları silin.

## <a name="next-steps"></a>Sonraki adımlar

Nasıl yapılacağını öğrenmek için sonraki makaleye gidin:
> [!div class="nextstepaction"]
> [URL 'ye göre uzak bir dosya kodlayın ve REST ile videoyu akışla](stream-files-tutorial-with-rest.md)