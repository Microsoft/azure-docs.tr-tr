---
title: Azure Active Directory 'ten uygulamalara Kullanıcı sağlaması için bir SCıM uç noktası oluşturun
description: Bir SCıM uç noktası geliştirmeyi, SCıM API 'nizi Azure AD ile tümleştirmeyi ve Azure Active Directory ile otomatik olarak bulut uygulamalarınıza Kullanıcı ve grup sağlamayı öğrenin.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 4e933000c8e700d8bfd193b542e3855b2fca26f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101689343"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Öğretici: örnek SCıM uç noktası geliştirme

Sıfırdan yeni bir uç nokta derlemek istemediğinde, [etki alanları arası kimlik yönetimi (SCıM) Için sistemi](https://aka.ms/scimoverview)kullanmaya başlamanıza yönelik bazı [Başvuru kodları](https://aka.ms/scimreferencecode) oluşturduk. Yalnızca beş dakikada bir kod olmadan SCıM uç noktanızı çalışır duruma getirebilirsiniz.

Bu öğreticide, Azure 'da SCıM başvuru kodunun nasıl dağıtılacağı ve Postman kullanılarak veya Azure Active Directory (Azure AD) SCıM istemcisiyle tümleştirilirken nasıl test yapılacağı açıklanmaktadır. Bu öğretici, SCıM ile çalışmaya veya bir SCıM uç noktasını test etmeye ilgilenen herkese yönelik geliştiriciler için tasarlanmıştır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * SCıM uç noktanızı Azure 'da dağıtın.
> * SCıM uç noktanızı test edin.

## <a name="deploy-your-scim-endpoint-in-azure"></a>SCıM uç noktanızı Azure 'da dağıtma

Buradaki adımlar, [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) ve [Azure App Service](../../app-service/index.yml)kullanarak SCIM uç noktasını bir hizmete dağıtır. SCıM başvuru kodu, şirket içi bir sunucu tarafından barındırılan veya başka bir dış hizmete dağıtılan yerel olarak da çalıştırılabilir.

1. GitHub 'dan [başvuru koduna](https://github.com/AzureAD/SCIMReferenceCode) gidin ve **Kopyala ya da indir**' i seçin.

1. **Masaüstünde aç**' ı seçin veya bağlantıyı kopyalayın, Visual Studio 'yu açın, kopyalanmış bağlantıyı girmek ve yerel bir kopya oluşturmak için Kopyala **veya dışarı çek kodu** ' nu seçin.

1. Visual Studio 'da, barındırma kaynaklarınıza erişimi olan hesapta oturum açmayı unutmayın.

1. Çözüm Gezgini, *Microsoft. SCIM. sln* dosyasını açın ve *Microsoft. SCIM. webhostsample* dosyasına sağ tıklayın. **Yayımla**’yı seçin.

    ![Örnek dosyayı gösteren ekran görüntüsü.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > Bu çözümü yerel olarak çalıştırmak için projeye çift tıklayın ve **IIS Express** ' ı seçerek projeyi yerel ana bilgisayar URL 'si olan bir Web sayfası olarak başlatın.

1. **Profil oluştur** ' u seçin ve **App Service** ve **Yeni oluştur** ' un seçili olduğundan emin olun.

    ![Yayımla penceresini gösteren ekran görüntüsü.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. İletişim kutusu seçeneklerinde ilerleyin ve uygulamayı dilediğiniz bir adla yeniden adlandırın. Bu ad hem uygulama hem de SCıM uç nokta URL 'sinde kullanılır.

    ![Yeni bir App Service oluşturmayı gösteren ekran görüntüsü.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Kullanılacak kaynak grubunu seçin ve **Yayımla**' yı seçin.

    ![Yeni bir App Service yayımlamayı gösteren ekran görüntüsü.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-4.png)

1. Azure App Service yapılandırma bölümünde uygulamaya gidin   >   ve **Yeni uygulama ayarı** ' nı seçerek *Token__TokenIssuer* ayarını değere ekleyin `https://sts.windows.net/<tenant_id>/` . `<tenant_id>`Azure AD KIRACı Kimliğinizle değiştirin. [Postman](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)kullanarak SCIM uç noktasını test etmek istiyorsanız, değerine sahip bir *ASPNETCORE_ENVIRONMENT* ayarı ekleyin `Development` .

   ![Uygulama ayarları penceresini gösteren ekran görüntüsü.](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   Uç noktanızı [Azure Portal](use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client)bir kurumsal uygulamayla test ettiğinizde, iki seçeneğiniz vardır. Ortamı ' de tutabilir `Development` ve uç noktadan test belirtecini sağlayabilir `/scim/token` ya da ortamı olarak değiştirebilir `Production` ve belirteç alanını boş bırakacak şekilde ayarlayabilirsiniz.

İşte bu kadar! SCıM uç noktanız artık yayımlandı ve SCıM uç noktasını test etmek için Azure App Service URL 'sini kullanabilirsiniz.

## <a name="test-your-scim-endpoint"></a>SCıM uç noktanızı test etme

Bir SCıM uç noktasına yapılan istekler yetkilendirme gerektirir. SCıM standardında tanımlama bilgileri, temel kimlik doğrulaması, TLS istemci kimlik doğrulaması veya [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2)' de listelenen yöntemlerden herhangi biri dahil olmak üzere kimlik doğrulama ve yetkilendirme için birden çok seçenek vardır.

Kullanıcı adı ve parola gibi güvenli olmayan yöntemlerin, OAuth gibi daha güvenli bir yöntem olarak kullanılması için bu yöntemlerden kaçındığınızdan emin olun. Azure AD, uzun süreli taşıyıcı belirteçleri (Galeri ve Galeri olmayan uygulamalar için) ve OAuth yetkilendirmesi verme (Galeri uygulamaları için) destekler.

> [!NOTE]
> Depoda belirtilen yetkilendirme yöntemleri yalnızca test içindir. Azure AD ile tümleştirdiğinizde, yetkilendirme kılavuzunu gözden geçirebilirsiniz. Bkz. [BIR SCıM uç noktası Için plan sağlama](use-scim-to-provision-users-and-groups.md).

Geliştirme ortamı, güvenlik belirteci doğrulamasının davranışını denetlemek için başvuru kodu gibi üretime yönelik güvenli olmayan özellikleri sağlar. Belirteç doğrulama kodu kendinden imzalı bir güvenlik belirteci kullanır ve imzalama anahtarı yapılandırma dosyasında depolanır. Dosyadakiappsettings.Development.js **belirteç: ıssuersigningkey** parametresine bakın. 

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> Uç noktaya bir **Get** isteği gönderdiğinizde `/scim/token` , yapılandırılmış anahtar kullanılarak bir belirteç verilir. Bu belirteç, sonraki yetkilendirme için bir taşıyıcı belirteci olarak kullanılabilir.

Varsayılan belirteç doğrulama kodu, bir Azure AD belirteci kullanacak şekilde yapılandırılmıştır ve veren kiracının, *appsettings.js* dosyadaki **token: tokenıssuer** parametresi kullanılarak yapılandırılmasını gerektirir.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Uç noktaları test etmek için Postman kullanma

SCıM uç noktasını dağıttıktan sonra, SCıM RFC ile uyumlu olduğundan emin olmak için test edebilirsiniz. Bu örnek, Postman 'da kullanıcılar ve gruplar üzerinde CRUD (oluşturma, okuma, güncelleştirme ve silme) işlemlerini doğrulayan bir test kümesi sağlar, filtreleme, grup üyeliğine yönelik güncelleştirmeler ve kullanıcıları devre dışı bırakır.

Uç noktalar `{host}/scim/` dizinde bulunur ve bunlarla etkileşim kurmak için standart http isteklerini kullanabilirsiniz. Yolu değiştirmek için `/scim/` bkz. **azureadprovisioningscımreference**   >  **scımreferenceapı**  >  **Controllers** içindeki controllerconstant. cs.

> [!NOTE]
> Yalnızca yerel testler için HTTP uç noktalarını kullanabilirsiniz. Azure AD sağlama hizmeti, uç noktanızın HTTPS desteklemesini gerektirir.

1. [Postman](https://www.getpostman.com/downloads/) indirin ve uygulamayı başlatın.
1. Bu bağlantıyı kopyalayıp Postman 'a yapıştırarak test koleksiyonunu içeri aktarın: `https://aka.ms/ProvisioningPostman` .

    ![Test koleksiyonunun Postman 'da içeri aktarılmasını gösteren ekran görüntüsü.](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Bu değişkenlere sahip bir test ortamı oluşturun:

   |Ortam|Değişken|Değer|
   |-|-|-|
   |IIS Express kullanarak projeyi yerel olarak çalıştırın|||
   ||**Sunucu**|`localhost`|
   ||**Bağlantı noktası**|`:44359`*(bunu unutmayın **`:`** )*|
   ||**'Sindeki**|`scim`|
   |Kestrel kullanarak projeyi yerel olarak çalıştırın|||
   ||**Sunucu**|`localhost`|
   ||**Bağlantı noktası**|`:5001`*(bunu unutmayın **`:`** )*|
   ||**'Sindeki**|`scim`|
   |Uç noktayı Azure 'da barındırma|||
   ||**Sunucu**|*(SCıM URL 'nizi girin)*|
   ||**Bağlantı noktası**|*(boş bırakın)*|
   ||**'Sindeki**|`scim`|

1. Belirteç uç noktasına **Get** isteği göndermek ve sonraki istekler için **belirteç** değişkeninde depolanacak bir güvenlik belirteci almak Için Postman koleksiyonundan **Al anahtarını** kullanın.

   ![Postman al anahtar klasörünü gösteren ekran görüntüsü.](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Bir SCıM uç noktası güvenli hale getirmek için, bağlanmadan önce bir güvenlik belirtecine ihtiyacınız vardır. Öğretici, `{host}/scim/token` otomatik olarak imzalanan bir belirteç oluşturmak için uç noktasını kullanır.

İşte bu kadar! Artık SCıM uç noktası işlevini test etmek için **Postman** koleksiyonunu çalıştırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bir istemci için birlikte çalışabilirlik özellikli bir SCıM uyumlu Kullanıcı ve grup uç noktası geliştirmek için bkz. [SCIM istemci uygulama](http://www.simplecloud.info/#Implementations2).

> [!div class="nextstepaction"]
> [Öğretici: SCıM uç noktası Için geliştirme ve plan sağlama](use-scim-to-provision-users-and-groups.md) 
>  [Öğretici: bir galeri uygulaması için sağlamayı yapılandırma](configure-automatic-user-provisioning-portal.md)