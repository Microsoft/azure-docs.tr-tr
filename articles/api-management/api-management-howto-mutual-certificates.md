---
title: İstemci sertifikası kimlik doğrulamasını kullanarak API Management arka ucu güvenli hale getirme
titleSuffix: Azure API Management
description: İstemci sertifikalarını ve Azure API Management istemci sertifikası kimlik doğrulamasını kullanarak güvenli arka uç hizmetlerini yönetmeyi öğrenin.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 01/26/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9d198f34abe826af2dce0c1ed9950600d4c7bbd3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812309"
---
# <a name="secure-backend-services-using-client-certificate-authentication-in-azure-api-management"></a>Azure API Management istemci sertifikası kimlik doğrulaması kullanarak arka uç hizmetleri güvenli hale getirme

API Management, istemci sertifikalarını kullanarak bir API 'nin arka uç hizmetine erişimi güvenli hale bırakmanıza olanak tanır. Bu kılavuzda, bir Azure API Management hizmeti örneğindeki sertifikaların Azure portal kullanılarak nasıl yönetileceği gösterilmektedir. Ayrıca, bir arka uç hizmetine erişmek için bir API 'nin bir sertifikayı kullanmak üzere nasıl yapılandırılacağını açıklar.

Ayrıca, [API Management REST API](/rest/api/apimanagement/2020-06-01-preview/certificate)kullanarak API Management sertifikalarını yönetebilirsiniz.

## <a name="certificate-options"></a>Sertifika seçenekleri

API Management, arka uç hizmetlerine erişimin güvenliğini sağlamak için kullanılan sertifikaları yönetmek için iki seçenek sunar:

* [Azure Key Vault](../key-vault/general/overview.md) yönetilen bir sertifikaya başvur 
* Doğrudan API Management bir sertifika dosyası ekleyin

Anahtar Kasası sertifikalarının kullanılması önerilir API Management güvenliği artırmaya yardımcı olur:

* Anahtar kasalarında depolanan sertifikalar, hizmetler arasında yeniden kullanılabilir
* Ayrıntılı [erişim ilkeleri](../key-vault/general/security-features.md#privileged-access) , anahtar kasalarında depolanan sertifikalara uygulanabilir
* Anahtar kasasında güncellenen sertifikalar API Management otomatik olarak döndürülür. Anahtar kasasındaki güncelleştirmeden sonra, API Management bir sertifika 4 saat içinde güncelleştirilir. Ayrıca, Azure portal kullanarak veya yönetim REST API aracılığıyla sertifikayı el ile yenileyebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Henüz bir API Management hizmet örneği oluşturmadıysanız, bkz. [API Management hizmet örneği oluşturma][Create an API Management service instance].
* Arka uç hizmetinizin istemci sertifikası kimlik doğrulaması için yapılandırılmış olması gerekir. Azure App Service sertifika kimlik doğrulamasını yapılandırmak için [Bu makaleye][to configure certificate authentication in Azure WebSites refer to this article]bakın. 
* Azure Anahtar Kasası 'nda yönetim için sertifikaya ve parolaya erişmeniz veya API Management hizmetine yüklemeniz gerekir. Sertifika **PFX** biçiminde olmalıdır. Otomatik olarak imzalanan sertifikalara izin verilir.

### <a name="prerequisites-for-key-vault-integration"></a>Anahtar Kasası tümleştirmesi için Önkoşullar

1. Bir Anahtar Kasası oluşturma adımları için bkz. [hızlı başlangıç: Azure Portal kullanarak bir Anahtar Kasası oluşturma](../key-vault/general/quick-create-portal.md).
1. API Management örneğinde sistem tarafından atanan veya Kullanıcı tarafından atanan bir [yönetilen kimliği](api-management-howto-use-managed-service-identity.md) etkinleştirin.
1. Bir [Anahtar Kasası erişim ilkesini](../key-vault/general/assign-access-policy-portal.md) , kasadan sertifika edinme ve listeleme izinleriyle yönetilen kimliğe atayın. İlkeyi eklemek için:
    1. Portalda anahtar kasanıza gidin.
    1. **Ayarlar > erişim ilkeleri > + erişim Ilkesi Ekle**' yi seçin.
    1. **Sertifika izinleri**' ni seçin ve ardından **Al** ve **Listele**' yi seçin.
    1. **Asıl seçin**' de, yönetilen kimliğinizin kaynak adını seçin. Sistem tarafından atanan bir kimlik kullanıyorsanız sorumlu, API Management örneğinizin adıdır.
1. Anahtar kasasında bir sertifika oluşturun veya içeri aktarın. Bkz. [hızlı başlangıç: Azure Portal kullanarak Azure Key Vault bir sertifikayı ayarlama ve alma](../key-vault/certificates/quick-create-portal.md).

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-a-key-vault-certificate"></a>Anahtar Kasası sertifikası ekleme

Bkz. [Anahtar Kasası tümleştirmesi Için Önkoşullar](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> API Management bir Anahtar Kasası sertifikası kullanırken, anahtar kasasına erişmek için kullanılan sertifikayı, anahtar kasasını veya yönetilen kimliği silmemeye dikkat edin.

API Management bir Anahtar Kasası sertifikası eklemek için:

1. [Azure portal](https://portal.azure.com)API Management örneğinize gidin.
1. **Güvenlik** altında **Sertifikalar**' ı seçin.
1. **Sertifikalar**  >  **+ Ekle**' yi seçin.
1. **Kimlik** alanına tercih ettiğiniz bir ad girin.
1. **Sertifika** bölümünde **Anahtar Kasası**' nı seçin.
1. Bir Anahtar Kasası sertifikasının tanımlayıcısını girin veya bir anahtar kasasından sertifika seçmek için **Seç** ' i seçin.
    > [!IMPORTANT]
    > Kendi kendinize bir Anahtar Kasası sertifika tanımlayıcısı girerseniz, sürüm bilgilerine sahip olmadığından emin olun. Aksi takdirde, sertifika, anahtar kasasındaki bir güncelleştirmeden sonra API Management otomatik olarak döndürülmez.
1. **İstemci kimliği**' nde, sistem tarafından atanan veya Kullanıcı tarafından atanan mevcut bir yönetilen kimlik seçin. [API Management hizmetinize Yönetilen kimlikler ekleme veya değiştirme](api-management-howto-use-managed-service-identity.md)hakkında bilgi edinin.
    > [!NOTE]
    > Kimlik, anahtar kasasından sertifika almak ve listelemek için izinlere ihtiyaç duyuyor. Zaten Anahtar Kasası 'na erişimi yapılandırmadıysanız, API Management kimliği, gerekli izinlerle otomatik olarak yapılandırabilmesi için sizi uyarır.
1. **Add (Ekle)** seçeneğini belirleyin.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-kv.png" alt-text="Anahtar Kasası sertifikası ekleme":::

## <a name="upload-a-certificate"></a>Sertifikayı karşıya yükle

API Management bir istemci sertifikası yüklemek için: 

1. [Azure portal](https://portal.azure.com)API Management örneğinize gidin.
1. **Güvenlik** altında **Sertifikalar**' ı seçin.
1. **Sertifikalar**  >  **+ Ekle**' yi seçin.
1. **Kimlik** alanına tercih ettiğiniz bir ad girin.
1. **Sertifika** bölümünde **özel**' i seçin.
1. Sertifika. pfx dosyasını seçmek için gidin ve parolasını girin.
1. **Add (Ekle)** seçeneğini belirleyin.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-add.png" alt-text="İstemci sertifikasını karşıya yükle":::

Sertifika karşıya yüklendikten sonra, **Sertifikalar** penceresinde gösterilir. Birçok sertifikanız varsa, bir API 'yi [Ağ Geçidi kimlik doğrulaması](#configure-an-api-to-use-client-certificate-for-gateway-authentication)için bir istemci sertifikası kullanacak şekilde yapılandırmak üzere, istenen sertifikanın parmak izini bir yere getirin.

> [!NOTE]
> Örneğin, otomatik olarak imzalanan bir sertifika kullanırken sertifika zinciri doğrulamasını devre dışı bırakmak için, bu makalenin ilerleyen kısımlarında [otomatik olarak imzalanan sertifikalar](#self-signed-certificates)bölümünde açıklanan adımları izleyin.

## <a name="configure-an-api-to-use-client-certificate-for-gateway-authentication"></a>Ağ Geçidi kimlik doğrulaması için istemci sertifikası kullanmak üzere bir API yapılandırma

1. [Azure portal](https://portal.azure.com)API Management örneğinize gidin.
1. **API 'ler** altında **API 'ler**' i seçin.
1. Listeden bir API seçin. 
2. **Tasarım** sekmesinde, **arka uç** bölümündeki Düzenleyici simgesini seçin.
3. **Ağ Geçidi kimlik bilgileri**' nde **istemci sertifikası** ' nı seçin ve açılan listeden sertifikanızı seçin.
1. **Kaydet**’i seçin.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png" alt-text="Ağ Geçidi kimlik doğrulaması için istemci sertifikası kullan":::

> [!CAUTION]
> Bu değişiklik hemen etkili olur ve bu API işlemlerine yapılan çağrılar, arka uç sunucusunda kimlik doğrulaması için sertifikayı kullanır.

> [!TIP]
> Bir API 'nin arka uç hizmeti için Ağ Geçidi kimlik doğrulaması için bir sertifika belirtildiğinde, bu API ilkesinin bir parçası haline gelir ve ilke düzenleyicisinde görüntülenebilir.

## <a name="self-signed-certificates"></a>Otomatik olarak imzalanan sertifikalar

Otomatik olarak imzalanan sertifikalar kullanıyorsanız, arka uç sistemiyle iletişim kurmak için API Management sertifika zinciri doğrulamasını devre dışı bırakmanız gerekir. Aksi takdirde, 500 hata kodu döndürür. Bunu yapılandırmak için [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) (yeni arka uç için) veya [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (mevcut arka uç Için) PowerShell cmdlet 'lerini kullanabilir ve `-SkipCertificateChainValidation` parametresini olarak ayarlayabilirsiniz `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="delete-a-client-certificate"></a>İstemci sertifikasını silme

Bir sertifikayı silmek için, seçin ve bağlam menüsünden **Sil** ' i seçin (**...**).

:::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png" alt-text="Sertifikayı silme":::

> [!IMPORTANT]
> Herhangi bir ilke tarafından sertifikaya başvuruluyorsa, bir uyarı ekranı görüntülenir. Sertifikayı silmek için önce sertifikayı kullanmak üzere yapılandırılmış ilkelerden kaldırmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

* [API Management'ta istemci sertifikası kimlik doğrulamasını kullanarak API'lerin güvenliğini sağlama](api-management-howto-mutual-certificates-for-clients.md)
* [API Management ilkeler](api-management-howto-policies.md) hakkında bilgi edinin


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: ./api-management-caching-policies.md
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

