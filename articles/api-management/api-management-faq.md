---
title: Azure API Management SSS | Microsoft Docs
description: Azure API Management 'de sık sorulan soruların (SSS), desenlerin ve en iyi yöntemlerin yanıtlarını öğrenin.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1b6a138317d0cc2e10e893d1969f9d5452064d8f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813749"
---
# <a name="azure-api-management-faqs"></a>Azure API Management SSS
Azure API Management için sık sorulan soruların, desenlerin ve en iyi yöntemlerin yanıtlarını alın.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="frequently-asked-questions"></a>Sık sorulan sorular
* [Bir özellik önizlemede olduğunda ne anlama geliyor?](#what-does-it-mean-when-a-feature-is-in-preview)
* [API Management ağ geçidi ile arka uç hizmetleri arasındaki bağlantının güvenliğini nasıl sağlayabilirim?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [API Management hizmet örneğimi yeni bir örneğe nasıl kopyalayabilirim?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [API Management örneğinizi programlı bir şekilde yönetebilir miyim?](#can-i-manage-my-api-management-instance-programmatically)
* [Yöneticiler grubuna nasıl kullanıcı ekleyebilirim?](#how-do-i-add-a-user-to-the-administrators-group)
* [İlke düzenleyicisinde kullanılamaz olarak eklemek istediğim ilke neden?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Tek bir API 'de birden çok ortam ayarlamak Nasıl yaparım??](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [API Management SOAP kullanabilir miyim?](#can-i-use-soap-with-api-management)
* [AD FS güvenliği olan bir OAuth 2,0 yetkilendirme sunucusunu yapılandırabilir miyim?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Birden çok coğrafi konuma dağıtımda hangi yönlendirme yöntemi API Management kullanır?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Bir API Management hizmet örneği oluşturmak için Azure Resource Manager şablonu kullanabilir miyim?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Arka uç için otomatik olarak imzalanan bir TLS/SSL sertifikası kullanabilir miyim?](#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)
* [Bir GIT deposunu kopyalamaya çalıştığımda neden bir kimlik doğrulama hatası alıyorum?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [API Management Azure ExpressRoute ile birlikte çalışıyor mu?](#does-api-management-work-with-azure-expressroute)
* [API Management bunlara dağıtıldığında Kaynak Yöneticisi Style VNET 'lerde neden özel bir alt ağ gerekir?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [VNET 'e API Management dağıtıldığında gereken en düşük alt ağ boyutu nedir?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [API Management hizmetini bir abonelikten diğerine taşıyabilir miyim?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [API 'mi içeri aktarma konusunda kısıtlamalar veya bilinen sorunlar var mı?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Bir özellik önizlemede olduğunda ne anlama geliyor?
Bir özellik önizlemede olduğunda, özelliğin sizin için nasıl çalıştığı konusunda etkin bir şekilde geri bildirimde bulunduğumuz anlamına gelir. Önizlemedeki bir özellik işlevsel olarak tamamlanmıştır, ancak müşteri geri bildirimlerine yanıt olarak bir değişiklik yapacağız olasıdır. Üretim ortamınızda önizlemede olan bir özelliğe bağlı memenizi öneririz.

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>API Management ağ geçidi ile arka uç hizmetleri arasındaki bağlantının güvenliğini nasıl sağlayabilirim?
API Management ağ geçidi ile arka uç hizmetleriniz arasındaki bağlantıyı güvenli hale getirmek için çeşitli seçenekleriniz vardır. Seçenekleriniz şunlardır:

* HTTP temel kimlik doğrulaması kullanın. Daha fazla bilgi için bkz. [Ilk API 'Nizi Içeri aktarma ve yayımlama](import-and-publish.md).
* [Azure API Management 'de istemci sertifikası kimlik doğrulaması kullanarak arka uç hizmetlerini güvenli hale getirme](api-management-howto-mutual-certificates.md)bölümünde AÇıKLANDıĞı gibi TLS karşılıklı kimlik doğrulaması kullanın.
* Arka uç hizmetinizde IP filtrelemeyi kullanın. Tüketim katmanının özel durumu ile API Management tüm katmanlarında, [IP belgesi makalesinde](api-management-howto-ip-addresses.md)açıklanan birkaç uyarılarla ağ geçidinin IP adresi sabit kalır.
* API Management örneğinizi bir Azure sanal ağına bağlayın.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>API Management hizmet örneğimi yeni bir örneğe nasıl kopyalayabilirim?
Yeni bir örneğe API Management bir örnek kopyalamak istiyorsanız, birkaç seçeneğiniz vardır. Seçenekleriniz şunlardır:

* API Management 'de yedekleme ve geri yükleme işlevini kullanın. Daha fazla bilgi için bkz. [Azure API Management 'de hizmet yedeklemesi ve geri yükleme kullanarak olağanüstü durum kurtarma uygulama](api-management-howto-disaster-recovery-backup-restore.md).
* [API Management REST API](/rest/api/apimanagement/)kullanarak kendi yedekleme ve geri yükleme özelliğini oluşturun. Varlıkları istediğiniz hizmet örneğinden kaydetmek ve geri yüklemek için REST API kullanın.
* Git kullanarak hizmet yapılandırmasını indirin ve ardından yeni bir örneğe yükleyin. Daha fazla bilgi için bkz. [Git kullanarak API Management hizmeti yapılandırmanızı kaydetme ve yapılandırma](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>API Management örneğinizi programlı bir şekilde yönetebilir miyim?
Evet, kullanarak API Management programlı bir şekilde yönetebilirsiniz:

* [API Management REST API](/rest/api/apimanagement/).
* [Microsoft Azure ımanagementpackservice yönetim kitaplığı SDK 'sı](https://aka.ms/apimsdk).
* [Hizmet dağıtımı](/powershell/module/wds) ve [hizmet yönetimi](/powershell/azure/servicemanagement/overview) PowerShell cmdlet 'leri.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Yöneticiler grubuna nasıl kullanıcı ekleyebilirim?
Yöneticiler grupları, sabit bir sistem grubudur. Azure abonelik yöneticileri bu grubun üyesidir. Bu gruba bir kullanıcı ekleyemezsiniz. Daha fazla bilgi için bkz. [Azure API Management geliştirici hesaplarını yönetmek için grupları oluşturma ve kullanma](./api-management-howto-create-groups.md) .

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>İlke düzenleyicisinde kullanılamaz olarak eklemek istediğim ilke neden?
Eklemek istediğiniz ilke, ilke düzenleyicisinde soluk veya gölgeli görünüyorsa, ilke için doğru kapsamda olduğunuzdan emin olun. Her ilke açıklaması, belirli kapsamlar ve ilke bölümlerinde kullanabilmeniz için tasarlanmıştır. İlke bölümlerini ve bir ilkeye ait kapsamları gözden geçirmek için, [API Management ilkeleri](./api-management-policies.md)' nde ilkenin kullanımı bölümüne bakın.

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Tek bir API 'de birden çok ortam ayarlamak Nasıl yaparım??
Birden çok ortamı (örneğin, bir test ortamı ve bir üretim ortamı) ayarlamak için, tek bir API 'de iki seçeneğiniz vardır. Seçenekleriniz şunlardır:

* Aynı kiracıda farklı API 'Leri barındırın.
* Farklı kiracılarda aynı API 'Leri barındırın.

### <a name="can-i-use-soap-with-api-management"></a>API Management SOAP kullanabilir miyim?
[SOAP geçişli geçiş](https://azure.microsoft.com/blog/soap-pass-through/) desteği artık kullanılabilir. Yöneticiler SOAP hizmeti WSDL 'sini içeri aktarabilir ve Azure API Management bir SOAP ön ucu oluşturur. Geliştirici portalı belgelerinin, test konsolunun, ilkelerin ve analizlerin hepsi SOAP Hizmetleri için kullanılabilir.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>AD FS güvenliği olan bir OAuth 2,0 yetkilendirme sunucusunu yapılandırabilir miyim?
Bir OAuth 2,0 yetkilendirme sunucusunu Active Directory Federasyon Hizmetleri (AD FS) (AD FS) güvenliği ile yapılandırma hakkında bilgi edinmek için bkz. [API Management ADFS kullanma](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Birden çok coğrafi konuma dağıtımda hangi yönlendirme yöntemi API Management kullanır?
API Management birden çok coğrafi konuma dağıtımda [performans trafiği yönlendirme yöntemini](../traffic-manager/traffic-manager-routing-methods.md#performance) kullanır. Gelen trafik, en yakın API ağ geçidine yönlendirilir. Bir bölge çevrimdışı kalırsa, gelen trafik otomatik olarak bir sonraki en yakın ağ geçidine yönlendirilir. Yönlendirme yöntemleri hakkında [Traffic Manager yönlendirme yöntemleri](../traffic-manager/traffic-manager-routing-methods.md)hakkında daha fazla bilgi edinin.

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Bir API Management hizmet örneği oluşturmak için Azure Resource Manager şablonu kullanabilir miyim?
Evet. Bkz. [Azure API Management hizmeti](https://aka.ms/apimtemplate) hızlı başlangıç şablonları.

### <a name="can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end"></a>Arka uç için otomatik olarak imzalanan bir TLS/SSL sertifikası kullanabilir miyim?
Evet. Bu, PowerShell aracılığıyla veya doğrudan API 'ye göndererek yapılabilir. Bu, sertifika zinciri doğrulamasını devre dışı bırakır ve API Management arka uç hizmetlerine iletişim kurarken otomatik olarak imzalanan veya özel olarak imzalanmış sertifikaları kullanmanıza olanak tanır.

#### <a name="powershell-method"></a>PowerShell yöntemi ####
[`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend)(Yeni arka uç için) veya [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (mevcut arka uç Için) PowerShell cmdlet 'lerini kullanın ve `-SkipCertificateChainValidation` parametresini olarak ayarlayın `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Doğrudan API güncelleştirme yöntemi ####
1. API Management kullanarak bir [arka uç](/rest/api/apimanagement/) varlık oluşturun.     
2. **Skipcertificatechainvalidation** özelliğini **true** olarak ayarlayın.     
3. Artık otomatik olarak imzalanan sertifikalara izin vermek istemiyorsanız, arka uç varlığını silin veya **Skipcertificatechainvalidation** özelliğini **false** olarak ayarlayın.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Bir Git deposunu kopyalamaya çalıştığımda neden kimlik doğrulaması hatası alıyorum?
Git kimlik bilgileri Yöneticisi kullanıyorsanız veya Visual Studio 'Yu kullanarak bir Git deposunu kopyalamaya çalışıyorsanız, Windows kimlik bilgileri iletişim kutusuyla bilinen bir sorunla karşılaşabilirsiniz. İletişim kutusu, parola uzunluğunu 127 karakter ile sınırlandırır ve Microsoft tarafından oluşturulan parolayı keser. Parolayı kısaltada çalışıyoruz. Şimdilik git deponuzu kopyalamak için lütfen git Bash kullanın.

### <a name="does-api-management-work-with-azure-expressroute"></a>API Management Azure ExpressRoute ile birlikte çalışıyor mu?
Evet. API Management Azure ExpressRoute ile birlikte kullanılabilir.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>API Management bunlara dağıtıldığında Kaynak Yöneticisi Style VNET 'lerde neden özel bir alt ağ gerekir?
API Management için adanmış alt ağ gereksinimi, aslında klasik (PAAS v1 katmanı) dağıtım modeli üzerine kurulmuştur. Kaynak Yöneticisi VNET 'e (v2 katmanı) dağıtabileceğiniz sürece, bunun sonuçları vardır. Azure 'daki klasik dağıtım modeli Kaynak Yöneticisi modeliyle sıkı bir şekilde bağlı değildir ve bu nedenle v2 katmanında bir kaynak oluşturursanız v1 katmanı bunun ilgili olduğunu bilmez ve bir NIC 'ye zaten ayrılmış bir IP kullanmaya çalışmak API Management gibi sorunlar oluşabilir (v2 üzerinde oluşturulmuştur).
Azure 'da klasik ve Kaynak Yöneticisi modellerinin farkı hakkında daha fazla bilgi edinmek için [dağıtım modellerindeki farka](../azure-resource-manager/management/deployment-models.md)bakın.

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>VNET 'e API Management dağıtıldığında gereken en düşük alt ağ boyutu nedir?
API Management dağıtmak için gereken en düşük alt ağ boyutu, Azure 'un desteklediği en düşük alt ağ boyutu olan [/29](../virtual-network/virtual-networks-faq.md#configuration)' dır.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>API Management hizmetini bir abonelikten diğerine taşıyabilir miyim?
Evet. Nasıl yapılacağını öğrenmek için bkz. [kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>API 'mi içeri aktarma konusunda kısıtlamalar veya bilinen sorunlar var mı?
Open API (Swagger), WSDL ve WADL biçimleri için [bilinen sorunlar ve kısıtlamalar](api-management-api-import-restrictions.md) .
