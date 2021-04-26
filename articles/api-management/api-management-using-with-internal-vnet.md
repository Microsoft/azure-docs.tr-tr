---
title: Azure API Management iç sanal ağlarla kullanma
titleSuffix: Azure API Management
description: Azure API Management iç sanal ağ üzerinde ayarlama ve yapılandırma hakkında bilgi edinin
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: how-to
ms.date: 04/12/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 35d813b6dfedbd7f76a88713757ce83c2644ff95
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813155"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Azure API Management hizmetini iç sanal ağ ile kullanma
Azure API Management, Azure sanal ağları ile internet 'te erişilemeyen API 'Leri yönetebilir. Bağlantıyı yapmak için bir dizi VPN teknolojisi mevcuttur. API Management, bir sanal ağ içindeki iki ana modda dağıtılabilir:
* Dış
* İç

API Management iç sanal ağ modunda dağıtırken, tüm hizmet uç noktaları (proxy ağ geçidi, geliştirici portalı, doğrudan yönetim ve git) yalnızca erişimi denetlediğiniz bir sanal ağ içinde görünür. Hizmet uç noktalarının hiçbiri genel DNS sunucusunda kayıtlı değil.

> [!NOTE]
> Hizmet uç noktaları için DNS girişi olmadığından, sanal ağ için [DNS yapılandırılana](#apim-dns-configuration) kadar bu uç noktalara erişilemeyecektir.

API Management kullanarak, iç modda aşağıdaki senaryolar elde edebilirsiniz:

* Özel veri merkezinizde barındırılan API 'Leri siteden siteye veya Azure ExpressRoute VPN bağlantıları kullanarak BT dışındaki üçüncü taraflar tarafından güvenli bir şekilde erişilebilir hale getirin.
* Bulut tabanlı API 'lerinizi ve şirket içi API 'lerinizi ortak bir ağ geçidi üzerinden açarak karma bulut senaryolarını etkinleştirin.
* Tek bir ağ geçidi uç noktası kullanarak birden çok coğrafi konumda barındırılan API 'lerinizi yönetin.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu makalede açıklanan adımları gerçekleştirmek için şunları yapmanız gerekir:

+ **Etkin bir Azure aboneliği**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Azure API Management örneği**. Daha fazla bilgi için bkz. [Azure API Management örneği oluşturma](get-started-create-service-instance.md).

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

Bir sanal ağda API Management hizmeti dağıtıldığında, [bağlantı noktalarının listesi](./api-management-using-with-vnet.md#required-ports) kullanılır ve açılması gerekir. 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>İç sanal ağda API Management oluşturma
Hizmet istemci API 'SI sürüm 2020-12-01 ile oluşturulduysa, iç sanal ağdaki API Management hizmeti bir iç yük dengeleyici temel SKU 'SU arkasında barındırılır. API sürüm 2021-01-01-önizleme ve müşterinin aboneliğinden ortak bir IP adresi olan istemcilerle oluşturulan hizmet için, bir iç yük dengeleyici standart SKU 'SU arkasında barındırılır. Daha fazla bilgi için bkz. [Azure Load Balancer SKU 'lar](../load-balancer/skus.md).

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Azure portal kullanarak bir sanal ağ bağlantısını etkinleştirme

1. [Azure Portal](https://portal.azure.com/)Azure API Management örneğinizi inceleyin.
1. **Sanal ağ**' ı seçin.
1. **İç** erişim türünü yapılandırın. Ayrıntılı adımlar için bkz. [Azure Portal kullanarak VNET bağlantısını etkinleştirme](api-management-using-with-vnet.md#enable-vnet-connectivity-using-the-azure-portal).

    ![İç sanal ağda Azure API Management ayarlama menüsü][api-management-using-internal-vnet-menu]

4. **Kaydet**’i seçin.

Dağıtım başarılı olduktan sonra, genel bakış dikey penceresinde API Management hizmetinizin **özel** sanal IP adresini ve **genel** sanal IP adresini görmeniz gerekir. **Özel** sanal IP adresi,, `gateway` `portal` `management` ve `scm` bitiş noktalarına erişilebilen API Management alt ağ içinden gelen yük dengeli bir IP adresidir. **Ortak** sanal IP adresi **yalnızca** , `management` 3443 numaralı bağlantı noktası üzerinden uç noktaya giden denetim düzlemi trafiği Için kullanılır ve [apimana,][ServiceTags] hizmet etiketine kilitlenebilir.

![Dahili bir sanal ağ yapılandırılmış API Management panosu][api-management-internal-vnet-dashboard]

> [!NOTE]
> Azure portalında kullanılabilen test konsolu, ağ geçidi URL 'Si genel DNS 'de kayıtlı olmadığından, **iç** VNET tarafından dağıtılan hizmet için çalışmaz. Bunun yerine, **Geliştirici Portalında** sunulan test konsolunu kullanmanız gerekir.

### <a name="deploy-api-management-into-virtual-network"></a><a name="deploy-apim-internal-vnet"> </a>API Management sanal ağa dağıtma

Ayrıca, aşağıdaki yöntemleri kullanarak sanal ağ bağlantısını etkinleştirebilirsiniz.


### <a name="api-version-2020-12-01"></a>API sürümü 2020-12-01

* Azure Resource Manager [şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-api-management-create-with-internal-vnet)

     [![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-internal-vnet%2Fazuredeploy.json)

* Azure PowerShell cmdlet 'leri-sanal ağda bir API Management örneği [oluşturma](/powershell/module/az.apimanagement/new-azapimanagement) veya [güncelleştirme](/powershell/module/az.apimanagement/update-azapimanagementregion)

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>DNS yapılandırması
API Management dış sanal ağ modundayken DNS, Azure tarafından yönetilir. İç sanal ağ modunda, kendi DNS 'nizi yönetmeniz gerekir. Azure DNS özel bir bölgeyi yapılandırma ve sanal ağa bağlama API Management hizmeti, önerilen seçenektir. [Azure DNS bir özel bölge ayarlamayı](../dns/private-dns-getstarted-portal.md)öğrenin.

> [!NOTE]
> API Management hizmet, IP adreslerinden gelen istekleri dinlemez. Yalnızca hizmet uç noktalarında yapılandırılan ana bilgisayar adına yapılan isteklere yanıt verir. Bu uç noktalar; ağ geçidi, Azure portal ve geliştirici portalı, doğrudan yönetim uç noktası ve git içerir.

### <a name="access-on-default-host-names"></a>Varsayılan ana bilgisayar adlarına erişim
Örneğin, "contosoınternalvnet" adlı bir API Management hizmeti oluşturduğunuzda, aşağıdaki hizmet uç noktaları varsayılan olarak yapılandırılır:

   * Ağ geçidi veya proxy: contosointernalvnet.azure-api.net

   * Geliştirici portalı: contosointernalvnet.portal.azure-api.net

   * Yeni geliştirici portalı: contosointernalvnet.developer.azure-api.net

   * Doğrudan yönetim uç noktası: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

Bu API Management hizmet uç noktalarına erişmek için, API Management dağıtıldığı sanal ağa bağlı bir alt ağda sanal makine oluşturabilirsiniz. Hizmetinizin iç sanal IP adresinin 10.1.0.5 olduğu varsayılırsa,%SystemDrive%\drivers\etc\hosts adlı Hosts dosyasını aşağıdaki gibi eşleyebilirsiniz:

   * 10.1.0.5 contosointernalvnet.azure-api.net

   * 10.1.0.5 contosointernalvnet.portal.azure-api.net

   * 10.1.0.5 contosointernalvnet.developer.azure-api.net

   * 10.1.0.5 contosointernalvnet.management.azure-api.net

   * 10.1.0.5 contosointernalvnet.scm.azure-api.net

Daha sonra, oluşturduğunuz sanal makineden tüm hizmet uç noktalarına erişebilirsiniz.
Bir sanal ağda özel bir DNS sunucusu kullanıyorsanız, DNS kayıtları oluşturabilir ve bu uç noktalara sanal ağınızdaki herhangi bir yerden erişebilirsiniz.

### <a name="access-on-custom-domain-names"></a>Özel etki alanı adlarına erişim

1. API Management hizmetine varsayılan ana bilgisayar adlarıyla erişmek istemiyorsanız, aşağıdaki görüntüde gösterildiği gibi tüm hizmet uç noktalarınız için özel etki alanı adları ayarlayabilirsiniz:

   ![API Management için özel etki alanı ayarlama][api-management-custom-domain-name]

2. Daha sonra, yalnızca sanal ağınızdan erişilebilen uç noktalara erişmek için DNS sunucunuzda kayıtlar oluşturabilirsiniz.

## <a name="routing"></a><a name="routing"></a> Yönlendirme

* Alt ağ aralığından yük dengeli bir *özel* sanal IP adresi ayrılır ve sanal ağ içinden API Management hizmet uç noktalarına erişmek için kullanılır. Bu *özel* IP adresi, Azure Portal hizmetin genel bakış dikey penceresinde bulunabilir. Bu adresin, sanal ağ tarafından kullanılan DNS sunucularına kayıtlı olması gerekir.
* 3443 numaralı bağlantı noktası üzerinden yönetim hizmeti uç noktasına erişim sağlamak için yük dengeli bir *genel* IP adresı (VIP) de ayrılmış olur. Bu *genel* IP adresi, Azure Portal hizmetin genel bakış dikey penceresinde bulunabilir. *Genel* IP adresi yalnızca `management` bağlantı noktası 3443 üzerinden uç noktaya yönelik denetim düzlemi trafiği Için kullanılır ve [apimana,][ServiceTags] hizmet etiketine kilitlenebilir.
* Alt ağ IP aralığından (DIP) IP adresleri, hizmette her bir VM 'ye atanır ve sanal ağ içindeki kaynaklara erişmek için kullanılır. Sanal ağ dışındaki kaynaklara erişmek için genel bir IP adresi (VIP) kullanılacaktır. Sanal ağ içindeki kaynakları güvenli hale getirmek için IP kısıtlama listeleri kullanılıyorsa, hizmete erişim vermek veya erişimi kısıtlamak için API Management hizmetinin dağıtıldığı alt ağ için tüm aralığın belirtilmesi gerekir.
* Yük dengeli ortak ve özel IP adresleri Azure portal genel bakış dikey penceresinde bulunabilir.
* Ortak ve özel erişim için atanan IP adresleri, hizmet konumundan kaldırılıp sonra sanal ağa geri eklendiyse değişebilir. Bu durumda, sanal ağ içindeki DNS kayıtları, yönlendirme kuralları ve IP kısıtlama listelerinin güncelleştirilmesi gerekebilir.

## <a name="related-content"></a><a name="related-content"> </a>İlgili içerik
Daha fazla bilgi için aşağıdaki makalelere bakın:
* [Bir sanal ağda Azure API Management ayarlanırken ortak ağ yapılandırması sorunları][Common network configuration problems]
* [Sanal ağ SSS](../virtual-network/virtual-networks-faq.md)
* [DNS 'de kayıt oluşturma](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
