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
ms.openlocfilehash: 677e38f69729bba8caf1ec3f88b2e0a1a4f8c7e8
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073678"
---
# <a name="azure-api-management-faqs"></a>Azure API Management SSS
Azure API Management için sık sorulan soruların, desenlerin ve en iyi yöntemlerin yanıtlarını alın.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="contact-us"></a>Bizimle iletişim kurun
* [Microsoft Azure API Management ekipte soru sorabilirsiniz.](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

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
* [Arka uç için otomatik olarak imzalanan bir SSL sertifikası kullanabilir miyim?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
* [Bir GIT deposunu kopyalamaya çalıştığımda neden bir kimlik doğrulama hatası alıyorum?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [API Management Azure ExpressRoute ile birlikte çalışıyor mu?](#does-api-management-work-with-azure-expressroute)
* [API Management bunlara dağıtıldığında Kaynak Yöneticisi Style VNET 'lerde neden özel bir alt ağ gerekir?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [VNET 'e API Management dağıtıldığında gereken en düşük alt ağ boyutu nedir?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [API Management hizmetini bir abonelikten diğerine taşıyabilir miyim?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [API 'mi içeri aktarma konusunda kısıtlamalar veya bilinen sorunlar var mı?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Microsoft Azure API Management ekipte soru sorabilirsiniz.
Aşağıdaki seçeneklerden birini kullanarak bizimle iletişim sağlayabilirsiniz:

* Sorularınızı [API Management MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt)forumumuza gönderin.
* <mailto:apimgmt@microsoft.com> adresine e-posta gönderin.
* [Azure geri bildirim forumuna](https://feedback.azure.com/forums/248703-api-management)bir özellik isteği gönderin.

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Bir özellik önizlemede olduğunda ne anlama geliyor?
Bir özellik önizlemede olduğunda, özelliğin sizin için nasıl çalıştığı konusunda etkin bir şekilde geri bildirimde bulunduğumuz anlamına gelir. Önizlemedeki bir özellik işlevsel olarak tamamlanmıştır, ancak müşteri geri bildirimlerine yanıt olarak bir değişiklik yapacağız olasıdır. Üretim ortamınızda önizlemede olan bir özelliğe bağlı memenizi öneririz. Önizleme özellikleriyle ilgili geri bildiriminiz varsa, lütfen [Microsoft Azure API Management takımına nasıl soru sorabilirim? konusundaki](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)iletişim seçeneklerinden birini öğrenmek için lütfen bize bildirin.

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>API Management ağ geçidi ve arka uç Hizmetlerim arasındaki bağlantının güvenliğini nasıl giderebilirim?
API Management ağ geçidi ile arka uç hizmetleriniz arasındaki bağlantıyı güvenli hale getirmek için çeşitli seçenekleriniz vardır. Şunları yapabilirsiniz:

* HTTP temel kimlik doğrulaması kullanın. Daha fazla bilgi için bkz. [Ilk API 'Nizi Içeri aktarma ve yayımlama](import-and-publish.md).
* [Azure API Management 'de istemci sertifikası kimlik doğrulaması kullanarak arka uç hizmetlerini güvenli hale getirme](api-management-howto-mutual-certificates.md)bölümünde AÇıKLANDıĞı gibi SSL karşılıklı kimlik doğrulaması kullanın.
* Arka uç hizmetinizde IP beyaz listesini kullanın. Tüketim katmanının özel durumu ile API Management tüm katmanlarında, [IP belgesi makalesinde](api-management-howto-ip-addresses.md)açıklanan birkaç uyarılarla ağ geçidinin IP adresi sabit kalır.
* API Management örneğinizi bir Azure sanal ağına bağlayın.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>API Management hizmeti örneğinizi yeni bir örneğe kopyalamak Nasıl yaparım? mı?
Yeni bir örneğe API Management bir örnek kopyalamak istiyorsanız, birkaç seçeneğiniz vardır. Şunları yapabilirsiniz:

* API Management 'de yedekleme ve geri yükleme işlevini kullanın. Daha fazla bilgi için bkz. [Azure API Management 'de hizmet yedeklemesi ve geri yükleme kullanarak olağanüstü durum kurtarma uygulama](api-management-howto-disaster-recovery-backup-restore.md).
* [API Management REST API](/rest/api/apimanagement/)kullanarak kendi yedekleme ve geri yükleme özelliğini oluşturun. Varlıkları istediğiniz hizmet örneğinden kaydetmek ve geri yüklemek için REST API kullanın.
* Git kullanarak hizmet yapılandırmasını indirin ve ardından yeni bir örneğe yükleyin. Daha fazla bilgi için bkz. [Git kullanarak API Management hizmeti yapılandırmanızı kaydetme ve yapılandırma](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>API Management örneğinizi programlı bir şekilde yönetebilir miyim?
Evet, kullanarak API Management programlı bir şekilde yönetebilirsiniz:

* [API Management REST API](/rest/api/apimanagement/).
* [Microsoft Azure ımanagementpackservice yönetim kitaplığı SDK 'sı](https://aka.ms/apimsdk).
* [Hizmet dağıtımı](https://docs.microsoft.com/powershell/module/wds) ve [hizmet yönetimi](https://docs.microsoft.com/powershell/azure/servicemanagement/overview) PowerShell cmdlet 'leri.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Yöneticiler grubuna kullanıcı eklemek Nasıl yaparım??
Yöneticiler grubuna bir kullanıcıyı nasıl ekleyebileceğiniz aşağıda bulabilirsiniz:

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. Güncelleştirmek istediğiniz API Management örneğine sahip kaynak grubuna gidin.
3. API Management, **API Management hizmeti katkıda bulunan** rolünü kullanıcıya atayın.

Artık yeni eklenen katkıda bulunan Azure PowerShell [cmdlet 'lerini](https://docs.microsoft.com/powershell/azure/overview)kullanabilir. Yönetici olarak oturum açmak için şu adımları uygulayın:

1. Oturum açmak için cmdlet 'ini kullanın. `Connect-AzAccount`
2. Bağlamını kullanarak `Set-AzContext -SubscriptionID <subscriptionGUID>`hizmeti olan aboneliğin bağlamını ayarlayın.
3. Kullanarak `Get-AzApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`çoklu oturum açma URL 'si alın.
4. Yönetici portalına erişmek için URL 'YI kullanın.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>İlke düzenleyicisinde kullanılamaz olarak eklemek istediğim ilke neden?
Eklemek istediğiniz ilke, ilke düzenleyicisinde soluk veya gölgeli görünüyorsa, ilke için doğru kapsamda olduğunuzdan emin olun. Her ilke açıklaması, belirli kapsamlar ve ilke bölümlerinde kullanabilmeniz için tasarlanmıştır. İlke bölümlerini ve bir ilkeye ait kapsamları gözden geçirmek için, [API Management ilkeleri](/azure/api-management/api-management-policies)' nde ilkenin kullanımı bölümüne bakın.

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Tek bir API 'de birden çok ortam ayarlamak Nasıl yaparım??
Birden çok ortamı (örneğin, bir test ortamı ve bir üretim ortamı) ayarlamak için, tek bir API 'de iki seçeneğiniz vardır. Şunları yapabilirsiniz:

* Aynı kiracıda farklı API 'Leri barındırın.
* Farklı kiracılarda aynı API 'Leri barındırın.

### <a name="can-i-use-soap-with-api-management"></a>API Management SOAP kullanabilir miyim?
[SOAP geçişli geçiş](https://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) desteği artık kullanılabilir. Yöneticiler SOAP hizmeti WSDL 'sini içeri aktarabilir ve Azure API Management bir SOAP ön ucu oluşturur. Geliştirici portalı belgelerinin, test konsolunun, ilkelerin ve analizlerin hepsi SOAP Hizmetleri için kullanılabilir.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>AD FS güvenliği olan bir OAuth 2,0 yetkilendirme sunucusunu yapılandırabilir miyim?
Bir OAuth 2,0 yetkilendirme sunucusunu Active Directory Federasyon Hizmetleri (AD FS) (AD FS) güvenliği ile yapılandırma hakkında bilgi edinmek için bkz. [API Management ADFS kullanma](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Birden çok coğrafi konuma dağıtımda hangi yönlendirme yöntemi API Management kullanır?
API Management birden çok coğrafi konuma dağıtımda [performans trafiği yönlendirme yöntemini](../traffic-manager/traffic-manager-routing-methods.md#performance) kullanır. Gelen trafik, en yakın API ağ geçidine yönlendirilir. Bir bölge çevrimdışı kalırsa, gelen trafik otomatik olarak bir sonraki en yakın ağ geçidine yönlendirilir. Yönlendirme yöntemleri hakkında [Traffic Manager yönlendirme yöntemleri](../traffic-manager/traffic-manager-routing-methods.md)hakkında daha fazla bilgi edinin.

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Bir API Management hizmet örneği oluşturmak için Azure Resource Manager şablonu kullanabilir miyim?
Evet. Bkz. [Azure API Management hizmeti](https://aka.ms/apimtemplate) hızlı başlangıç şablonları.

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Arka uç için otomatik olarak imzalanan bir SSL sertifikası kullanabilir miyim?
Evet. Bu, PowerShell aracılığıyla veya doğrudan API 'ye göndererek yapılabilir. Bu, sertifika zinciri doğrulamasını devre dışı bırakır ve API Management arka uç hizmetlerine iletişim kurarken otomatik olarak imzalanan veya özel olarak imzalanmış sertifikaları kullanmanıza olanak tanır.

#### <a name="powershell-method"></a>PowerShell yöntemi ####
(Yeni arka uç için) veya [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) (mevcut arka uç için) `-SkipCertificateChainValidation` PowerShell cmdlet 'lerini kullanın ve parametresini olarak `True`ayarlayın. [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) 

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Doğrudan API güncelleştirme yöntemi ####
1. API Management kullanarak bir [arka uç](/rest/api/apimanagement/) varlık oluşturun.     
2. **Skipcertificatechainvalidation** özelliğini **true**olarak ayarlayın.     
3. Artık otomatik olarak imzalanan sertifikalara izin vermek istemiyorsanız, arka uç varlığını silin veya **Skipcertificatechainvalidation** özelliğini **false**olarak ayarlayın.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Bir Git deposunu kopyalamaya çalıştığımda neden bir kimlik doğrulama hatası alıyorum?
Git kimlik bilgileri Yöneticisi kullanıyorsanız veya Visual Studio 'Yu kullanarak bir Git deposunu kopyalamaya çalışıyorsanız, Windows kimlik bilgileri iletişim kutusuyla bilinen bir sorunla karşılaşabilirsiniz. İletişim kutusu, parola uzunluğunu 127 karakter ile sınırlandırır ve Microsoft tarafından oluşturulan parolayı keser. Parolayı kısaltada çalışıyoruz. Şimdilik git deponuzu kopyalamak için lütfen git Bash kullanın.

### <a name="does-api-management-work-with-azure-expressroute"></a>API Management Azure ExpressRoute ile birlikte çalışıyor mu?
Evet. API Management Azure ExpressRoute ile birlikte kullanılabilir.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>API Management bunlara dağıtıldığında Kaynak Yöneticisi Style VNET 'lerde neden özel bir alt ağ gerekir?
API Management için adanmış alt ağ gereksinimi, aslında klasik (PAAS v1 katmanı) dağıtım modeli üzerine kurulmuştur. Kaynak Yöneticisi VNET 'e (v2 katmanı) dağıtabileceğiniz sürece, bunun sonuçları vardır. Azure 'daki klasik dağıtım modeli Kaynak Yöneticisi modeliyle sıkı bir şekilde bağlı değildir ve bu nedenle v2 katmanında bir kaynak oluşturursanız v1 katmanı bunun hakkında bilgi vermez ve bir NIC 'ye önceden ayrılmış bir IP kullanmaya çalışmak API Management gibi sorunlar oluşabilir  (v2 üzerinde oluşturulmuştur).
Azure 'da klasik ve Kaynak Yöneticisi modellerinin farkı hakkında daha fazla bilgi edinmek için [dağıtım modellerindeki farka](../azure-resource-manager/resource-manager-deployment-model.md)bakın.

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>VNET 'e API Management dağıtıldığında gereken en düşük alt ağ boyutu nedir?
API Management dağıtmak için gereken en düşük alt ağ boyutu, Azure 'un desteklediği en düşük alt ağ boyutu olan [/29](../virtual-network/virtual-networks-faq.md#configuration)' dır.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Bir API Management hizmetini bir abonelikten diğerine taşıyabilir miyim?
Evet. Nasıl yapılacağını öğrenmek için bkz. [kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/resource-group-move-resources.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>API 'mi içeri aktarma konusunda kısıtlamalar veya bilinen sorunlar var mı?
Open API (Swagger), WSDL ve WADL biçimleri için [bilinen sorunlar ve kısıtlamalar](api-management-api-import-restrictions.md) .
