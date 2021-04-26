---
title: Geliştirici portalı-sık sorulan sorular
titleSuffix: Azure API Management
description: API Management 'de geliştirici portalı hakkında sık sorulan sorular. Geliştirici portalı, API tüketicilerinin API 'lerinizi keşfedebileceği özelleştirilebilir bir Web sitesidir.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.openlocfilehash: b4413bc53cf5c86c311d049046db790582737ca4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741835"
---
# <a name="api-management-developer-portal---frequently-asked-questions"></a>API Management geliştirici portalı-sık sorulan sorular

## <a name="what-if-i-need-functionality-that-isnt-supported-in-the-portal"></a>Portalda desteklenmeyen işlevlere ihtiyacım varsa ne yapmalıyım?

[GitHub deposunda](https://github.com/Azure/api-management-developer-portal) bir özellik isteği açabilir veya [eksik işlevselliği kendiniz uygulayabilirsiniz](developer-portal-implement-widgets.md). Geliştirici portalı [genişletilebilirliği](api-management-howto-developer-portal.md#managed-vs-self-hosted)hakkında daha fazla bilgi edinin.


## <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>Tek bir API Management hizmetinde birden çok geliştirici portalı olabilir mi?

Bir yönetilen portala ve birden çok şirket içinde barındırılan portala sahip olabilirsiniz. Tüm portalların içeriği aynı API Management hizmetinde depolanır, bu nedenle aynı olur. Portalların görünüm ve işlevselliğini birbirinden ayırt etmek istiyorsanız, bu dosyaları, örneğin URL 'sini temel alarak çalışma zamanında sayfaları dinamik olarak özelleştiren kendi özel pencere arkadaşlarınızla birlikte barındırabilirsiniz.

## <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Portal Azure Resource Manager şablonları destekliyor mu ve/veya API Management DevOps kaynak seti ile uyumlu mı?

Hayır.

## <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>Portalın içeriği API Management yedekleme/geri yükleme işleviyle mi kaydedildi?

Hayır.

## <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Yönetilen Portal bağımlılıkları için ek VNet bağlantısını etkinleştirmem gerekir mi?

Çoğu durumda-Hayır.

API Management hizmetiniz bir iç VNet içindeyse, geliştirici portalınızın yalnızca ağ içinden erişilebilir olması gerekir. Yönetim uç noktasının ana bilgisayar adı, portalın yönetim arabirimine erişmek için kullandığınız makineden hizmetin iç VIP 'sine çözümlenmelidir. Yönetim uç noktasının DNS 'de kayıtlı olduğundan emin olun. Yanlış yapılandırma durumunda bir hata görürsünüz: `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

API Management hizmetiniz bir iç sanal ağda ise ve bu ağa İnternet 'ten Application Gateway üzerinden erişiyorsanız, geliştirici portalına ve API Management yönetim uç noktalarına bağlantıyı etkinleştirdiğinizden emin olun. Web uygulaması güvenlik duvarı kurallarını devre dışı bırakmanız gerekebilir. Daha fazla bilgi için [Bu belge makalesine](api-management-howto-integrate-internal-vnet-appgateway.md) bakın.

## <a name="i-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Özel bir API Management etki alanı atadım ve yayımlanan Portal çalışmıyor

Etki alanını güncelleştirdikten sonra değişikliklerin etkili olması için [portalı yeniden yayımlamanız](api-management-howto-developer-portal-customize.md#publish) gerekir.

## <a name="i-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Bir kimlik sağlayıcısı ekledim ve portalda göremiyorum

Bir kimlik sağlayıcısını yapılandırdıktan sonra (örneğin, Azure AD, Azure AD B2C), değişikliklerin etkili olması için [portalı yeniden yayımlamanız](api-management-howto-developer-portal-customize.md#publish) gerekir. Geliştirici portalı sayfalarınızın OAuth düğmeleri pencere öğesini içerdiğinden emin olun.

## <a name="i-set-up-delegation-and-the-portal-doesnt-use-it"></a>Temsilciyi ayarladım ve Portal bunu kullanmıyor

Temsilciyi ayarladıktan sonra değişikliklerin etkili olması için portalı yeniden [yayımlamanız](api-management-howto-developer-portal-customize.md#publish) gerekir.

## <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Diğer API Management yapılandırma Değişikliklerim Geliştirici Portalında yayılmadı

Çoğu yapılandırma değişikliği (örneğin, VNet, oturum açma, ürün koşulları) [portalın yeniden yayımmesini](api-management-howto-developer-portal-customize.md#publish)gerektirir.

## <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> Etkileşimli konsolu kullanırken CORS hatası alıyorum

Etkileşimli konsol tarayıcıdan istemci tarafı API isteği yapar. API 'lerinize [CORS ilkesi](api-management-cross-domain-policies.md#CORS) ekleyerek CORS sorununu çözün.

CORS ilkesinin durumunu, Azure portal API Management hizmetinizin **Portal genel bakış** bölümünde bulabilirsiniz. Bir uyarı kutusu, eksik veya yanlış yapılandırılmış bir ilkeyi gösterir.

> [!NOTE]
> 
> Yalnızca bir CORS ilkesi yürütülür. Birden çok CORS ilkesi belirttiyseniz (örneğin, API düzeyinde ve tümü-API düzeyinde), etkileşimli konsolunuz beklendiği gibi çalışmayabilir.

![CORS ilkenizin durumunu nereden kontrol edebilirsiniz gösteren ekran görüntüsü.](media/developer-portal-faq/cors-azure-portal.png)

CORS ilkesini **Etkinleştir** düğmesine tıklayarak OTOMATIK olarak CORS ilkesini uygulayın.

CORS 'yi el ile de etkinleştirebilirsiniz.

1. Oluşturulan ilke kodunu görmek için **bunu genel düzey bağlantı üzerinde el ile Uygula '** yı seçin.
2. Azure portal API Management hizmetinizin **API 'leri** bölümünde **tüm API** 'lere gidin.
3. **</>** **Gelen işleme** bölümünde simgesini seçin.
4. İlkeyi **<inbound>** XML dosyasının bölümüne ekleyin. **<origin>** Değerin geliştirici portalının etki alanı ile eşleştiğinden emin olun.

> [!NOTE]
> 
> CORS ilkesini, API (ler) kapsamı yerine ürün kapsamında uygularsanız ve API 'niz bir üst bilgi aracılığıyla abonelik anahtarı kimlik doğrulaması kullanıyorsa, konsolunuz çalışmaz.
>
> Tarayıcı, `OPTIONS` abonelik anahtarı içeren bir üst bilgi içermeyen bir http isteğini otomatik olarak yayınlar. Eksik abonelik anahtarı nedeniyle API Management, `OPTIONS` çağrıyı bir ürünle ilişkilendiremez, bu nedenle CORS ilkesini uygulayamaz.
>
> Geçici bir çözüm olarak, abonelik anahtarını bir sorgu parametresine geçirebilirsiniz.

## <a name="what-is-the-cors-proxy-feature-and-when-should-i-use-it"></a>CORS proxy özelliği nedir ve ne zaman kullanmalıyım?

Etkileşimli konsolun API çağrılarını API Management hizmetinize portalın arka ucu üzerinden yönlendirmek için API işlem ayrıntıları pencere öğesinin yapılandırmasındaki **CORS proxy 'Sini kullan** seçeneğini belirleyin. Bu yapılandırmada artık API 'niz için bir CORS İlkesi uygulamanız gerekmez ve yerel makineden ağ geçidi uç noktasına bağlantı gerekli değildir. API 'Ler şirket içinde barındırılan bir ağ geçidiyle veya hizmetiniz bir sanal ağda yer alıyorsa, API Management arka uç hizmetinden ağ geçidine olan bağlantı gereklidir. Şirket içinde barındırılan portalı kullanıyorsanız, yapılandırma dosyalarındaki seçeneğini kullanarak portalın arka uç uç noktasını belirtin `backendUrl` . Aksi takdirde, şirket içinde barındırılan Portal, arka uç hizmetinin konumunu bilmez.

## <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Geliştirici portalını düzenlemek için hangi izinlere ihtiyacım var?

`Oops. Something went wrong. Please try again later.`Portalı yönetim modunda açtığınızda hata görüyorsanız, gerekli izinlere (Azure RBAC) gerek duyulmayabilir.

Eski portallar, `Microsoft.ApiManagement/service/getssotoken/action` `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>` Kullanıcı yöneticisinin portallara erişmesine izin vermek için hizmet kapsamında () izni gerektirdi. Yeni Portal kapsamda izin gerektirir `Microsoft.ApiManagement/service/users/token/action` `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` .

Gerekli izne sahip bir rol oluşturmak için aşağıdaki PowerShell betiğini kullanabilirsiniz. Parametresini değiştirmeyi unutmayın `<subscription-id>` . 

```powershell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
Rol oluşturulduktan sonra, Azure portal **Access Control (IAM)** bölümünde herhangi bir kullanıcıya verilebilir. Bu rolün bir kullanıcıya atanması, bu izni hizmet kapsamında atayacaktır. Kullanıcı, hizmette *herhangi bir* Kullanıcı adına SAS belirteçleri üretebilecektir. En azından, bu rolün hizmetin yöneticisine atanması gerekir. Aşağıdaki PowerShell komutu, `user1` kullanıcıya gereksiz izinler vermekten kaçınmak için rolün en düşük kapsamdaki bir kullanıcıya nasıl atanacağını gösterir: 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

İzinler bir kullanıcıya verildikten sonra, yeni izinlerin etkili olması için kullanıcının oturumu kapatıp Azure portal yeniden oturum açması gerekir.

## <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>`Unable to start the portal. See if settings are specified correctly (...)`Hatayı görüyorum

Bu hata, `GET` çağrısı başarısız olduğunda gösterilir `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` . Bu çağrı tarayıcıdan portalın yönetim arabirimi tarafından verilir.

API Management hizmetiniz bir sanal ağda ise, [VNET bağlantı sorusuna](#do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies)bakın.

Çağrı hatasına Ayrıca özel bir etki alanına atanan ve tarayıcı tarafından güvenilmeyen bir TLS/SSL sertifikası neden olabilir. Risk azaltma olarak, yönetim uç noktası özel etki alanını kaldırabilirsiniz API Management, güvenilen bir sertifika ile varsayılan uç noktaya geri dönecektir.

## <a name="whats-the-browser-support-for-the-portal"></a>Portal için tarayıcı desteği nedir?

| Tarayıcı                     | Desteklenir       |
|-----------------------------|-----------------|
| Apple Safari                | Evet<sup>1</sup> |
| Google Chrome               | Evet<sup>1</sup> |
| Microsoft Edge              | Evet<sup>1</sup> |
| Microsoft Internet Explorer | No              |
| Mozilla Firefox             | Evet<sup>1</sup> |

 <small><sup>1</sup> en son iki üretim sürümünde desteklenir.</small>

## <a name="local-development-of-my-self-hosted-portal-is-no-longer-working"></a>Kendi kendine barındırılan portalımın yerel geliştirmesi artık çalışmıyor

Geliştirici portalının yerel sürümünüz depolama hesabından veya API Management örneğinden bilgi kaydedemeyen veya alamadığında SAS belirteçlerinin geçerliliği bitmiş olabilir. Yeni belirteçler oluşturarak bu hatayı çözebilirsiniz. Yönergeler için, [Geliştirici Portalını Self barındırmak](developer-portal-self-host.md#step-2-configure-json-files-static-website-and-cors-settings)için öğreticiye bakın.

## <a name="how-can-i-remove-the-developer-portal-content-provisioned-to-my-api-management-service"></a>API Management hizmetme sağlanan geliştirici portalı içeriğini nasıl kaldırabilirim?

Komut dosyasında, `scripts.v3/cleanup.bat` geliştirici portalı [GitHub deposundaki](https://github.com/Azure/api-management-developer-portal)gerekli parametreleri sağlayın ve betiği çalıştırın

```sh
cd scripts.v3
.\cleanup.bat
cd ..
```

## <a name="how-do-i-enable-single-sign-on-sso-authentication-to-self-hosted-developer-portal"></a>Şirket içinde barındırılan geliştirici portalına çoklu oturum açma (SSO) kimlik doğrulamasını etkinleştirmek Nasıl yaparım?.

Diğer kimlik doğrulama yöntemlerinin yanı sıra, geliştirici portalı çoklu oturum açmayı (SSO) destekler. Bu yöntemde kimlik doğrulamak için sorgu parametresindeki belirteçle bir çağrısı yapmanız gerekir `/signin-sso` :

```html
https://contoso.com/signin-sso?token=[user-specific token]
```
### <a name="generate-user-tokens"></a>Kullanıcı belirteçleri oluştur
[API Management REST API](/rest/api/apimanagement/apimanagementrest/api-management-rest) [paylaşılan erişim belirteci al](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) işlemini kullanarak *kullanıcıya özgü belirteçler* (Yönetim belirteçleri dahil) oluşturabilirsiniz.

> [!NOTE]
> Belirtecin URL kodlamalı olması gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Yeni geliştirici portalı hakkında daha fazla bilgi edinin:

- [Yönetilen geliştirici portalına erişme ve bunları özelleştirme](api-management-howto-developer-portal-customize.md)
- [Portalın şirket içinde barındırılan sürümünü ayarlama](developer-portal-self-host.md)
- [Kendi pencere öğesini uygulama](developer-portal-implement-widgets.md)

Diğer kaynaklara gözatamazsınız:

- [Kaynak kodlu GitHub deposu](https://github.com/Azure/api-management-developer-portal)

