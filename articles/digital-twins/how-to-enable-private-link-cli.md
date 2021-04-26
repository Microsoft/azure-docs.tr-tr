---
title: Özel bağlantıyla özel erişimi etkinleştir (Önizleme)-CLı
titleSuffix: Azure Digital Twins
description: Azure CLı kullanarak, özel bağlantıyla Azure dijital TWINS çözümleri için özel erişimi etkinleştirme makalesine bakın.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4dab08983fc1348ca49e728a65d48aa65fe19a47
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105723"
---
# <a name="enable-private-access-with-private-link-preview-azure-cli"></a>Özel bağlantıyla özel erişimi etkinleştir (Önizleme): Azure CLı

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

Bu makalede, [bir Azure dijital TWINS örneği (Şu anda önizleme aşamasında) için özel bir uç nokta Ile özel bağlantının etkinleştirilmesi](concepts-security.md#private-network-access-with-azure-private-link-preview) için farklı yollar açıklanmaktadır. Azure dijital TWINS örneğiniz için özel bir uç nokta yapılandırmak, Azure dijital TWINS örneğinizi güvenli hale getirmenizi ve genel pozlamayı ortadan kaldırmanıza ve [Azure sanal ağınızdan (VNet)](../virtual-network/virtual-networks-overview.md)veri alımını önlemenize olanak sağlar.

Bu makalede, [**Azure CLI**](/cli/azure/what-is-azure-cli)kullanarak işlem adım adım açıklanmaktadır.

Bu makalede ele alınan adımlar aşağıda verilmiştir: 
1. Özel bağlantıyı açın ve bir Azure dijital TWINS örneği için özel bir uç nokta yapılandırın.
1. Yalnızca özel bağlantı bağlantılarına yönelik API erişimini kısıtlamak için ortak ağ erişim bayraklarını devre dışı bırakın veya etkinleştirin.

## <a name="prerequisites"></a>Önkoşullar

Özel bir uç nokta ayarlamadan önce, uç noktanın dağıtılabileceği bir [**Azure sanal ağı (VNet)**](../virtual-network/virtual-networks-overview.md) gerekir. Zaten bir sanal ağınız yoksa, bunu ayarlamak için Azure sanal ağ [hızlı](../virtual-network/quick-create-portal.md) başlangıçlarından birini izleyebilirsiniz.

## <a name="manage-private-endpoints-for-an-azure-digital-twins-instance"></a>Azure dijital TWINS örneği için özel uç noktaları yönetme 

[Azure CLI](/cli/azure/what-is-azure-cli)'yı kullanırken, zaten mevcut olan bir Azure dijital TWINS örneğinde özel bir bağlantı ile özel uç noktalar ayarlayabilirsiniz (örnek oluşturmanın bir parçası olarak eklenemez). Daha sonra bunları ek CLı komutları aracılığıyla görüntülemeye ve yönetmeye devam edebilirsiniz. 

>[!TIP]
> Ayrıca, Azure dijital TWINS örneğiniz yerine özel bağlantı hizmeti aracılığıyla özel bağlantı uç noktası da ayarlayabilirsiniz. Bu aynı zamanda aynı yapılandırma seçeneklerini ve aynı nihai sonucu verir.
>
> Özel bağlantı kaynaklarını ayarlama hakkında daha fazla bilgi için bkz. [Azure Portal](../private-link/create-private-endpoint-portal.md), [Azure CLI](../private-link/create-private-endpoint-cli.md), [ARM şablonları](../private-link/create-private-endpoint-template.md)veya [PowerShell](../private-link/create-private-endpoint-powershell.md)için özel bağlantı belgeleri.

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Var olan bir örneğe özel bir uç nokta ekleme

Özel bir uç nokta oluşturmak ve bunu bir Azure dijital TWINS örneğine bağlamak için [**az Network Private-ENDPOINT Create**](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) komutunu kullanın. Parametresindeki tam KIMLIĞINI kullanarak Azure dijital TWINS örneğini belirler `--private-connection-resource-id` .

Yalnızca gerekli parametrelerle özel bir uç nokta oluşturmak için komutunu kullanan bir örnek aşağıda verilmiştir.

```azurecli-interactive
az network private-endpoint create --connection-name {private_link_service_connection} -n {name_for_private_endpoint} -g {resource_group} --subnet {subnet_ID} --private-connection-resource-id "/subscriptions/{subscription_ID}/resourceGroups/{resource_group}/providers/Microsoft.DigitalTwins/digitalTwinsInstances/{Azure_Digital_Twins_instance_name}" 
```

Gerekli ve isteğe bağlı parametrelerin tam listesi ve daha özel uç nokta oluşturma örnekleri için, [ **az Network Private-Endpoint Create** Reference belgelerine](/cli/azure/network/private-endpoint#az_network_private_endpoint_create)bakın.

### <a name="manage-private-endpoint-connections-on-the-instance"></a>Örnekteki özel uç nokta bağlantılarını yönetme

Azure dijital TWINS örneğiniz için özel bir uç nokta oluşturulduktan sonra, Özel uç nokta **bağlantılarını** bu örneğe göre yönetmeye devam etmek için [**az DT Network Private-Endpoint Connection**](/cli/azure/dt/network/private-endpoint/connection) komutlarını kullanabilirsiniz. İşlemler şunları içerir:
* Özel bir uç nokta bağlantısı göster
* Özel uç nokta bağlantısının durumunu ayarla
* Özel uç nokta bağlantısını silme
* Bir örnek için tüm özel uç nokta bağlantılarını listeleme

Daha fazla bilgi ve örnek için bkz. [ **az DT Network Private-Endpoint** Reference belgeleri](/cli/azure/dt/network/private-endpoint).

### <a name="manage-other-private-link-information-on-an-azure-digital-twins-instance"></a>Azure dijital TWINS örneğindeki diğer özel bağlantı bilgilerini yönetme

[**Az DT Network Private-link**](/cli/azure/dt/network/private-link) komutlarıyla örneğinizin özel bağlantı durumu hakkında daha fazla bilgi edinebilirsiniz. İşlemler şunları içerir:
* Bir Azure dijital TWINS örneğiyle ilişkili özel bağlantıları listeleme
* Örnekle ilişkili özel bir bağlantı göster

Daha fazla bilgi ve örnek için bkz. [ **az DT Network Private-link** Reference documentation](/cli/azure/dt/network/private-link).

## <a name="disable--enable-public-network-access-flags"></a>Ortak ağ erişim bayraklarını devre dışı bırak/etkinleştir

Azure dijital TWINS örneğinizi tüm genel bağlantıları reddedecek şekilde yapılandırabilir ve yalnızca ağ güvenliğini geliştirmek için özel uç noktalar aracılığıyla bağlantılara izin verebilirsiniz. Bu eylem, bir **genel ağ erişim bayrağıyla** yapılır. 

Bu ilke yalnızca özel bağlantı bağlantılarına yönelik API erişimini kısıtlayabilmeniz için izin verir. Ortak ağ erişim bayrağı *devre dışı* olarak ayarlandığında, tüm REST API ortak buluttan Azure Digital TWINS örnek veri düzlemine çağrı yapılır `403, Unauthorized` . Alternatif olarak, ilke *devre dışı* olarak ayarlandığında ve bir istek özel bir uç nokta üzerinden YAPıLDıĞıNDA, API çağrısı başarılı olur.

Bu makalede, [Azure CLI](/cli/azure/) veya [armclient komut aracı](https://github.com/projectkudu/ARMClient)kullanılarak ağ bayrağının değerinin nasıl güncelleştirilmesi gösterilmektedir. Azure portal ile nasıl yapılacağı hakkında yönergeler için, bu makalenin [Portal sürümüne](how-to-enable-private-link-portal.md) bakın.

### <a name="use-the-azure-cli"></a>Azure CLI kullanma

Azure CLı 'da, komuta bir parametre ekleyerek ortak ağ erişimini etkinleştirebilir veya devre dışı bırakabilirsiniz `--public-network-access` `az dt create` . Bu komut yeni bir örnek oluşturmak için de kullanılabilir olsa da, mevcut bir örneğin özelliklerini düzenlemek için onu kullanabilirsiniz. (Bu komut hakkında daha fazla bilgi için, [başvuru belgelerine](/cli/azure/dt#az_dt_create) veya [bir Azure dijital TWINS örneği ayarlamaya yönelik genel yönergelere](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)bakın).

Bir Azure dijital TWINS örneği için genel ağ erişimini **devre dışı bırakmak** için aşağıdaki `--public-network-access` gibi parametreyi kullanın:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Disabled
```

Şu anda devre dışı bırakılmış bir örnekte ortak ağ erişimini **etkinleştirmek** için aşağıdaki benzer komutu kullanın:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Enabled
```

### <a name="usethe-armclientcommand-tool"></a>ARMClient komut aracını kullanma 

[Armclient komut aracı](https://github.com/projectkudu/ARMClient)ile, ortak ağ erişimi aşağıdaki komutlar kullanılarak etkinleştirilir veya devre dışı bırakılır. 

Genel ağ erişimini **devre dışı bırakmak** için:
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

Ortak ağ erişimini **etkinleştirmek** için:  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

## <a name="next-steps"></a>Sonraki adımlar

Azure için özel bağlantı hakkında daha fazla bilgi edinin: 
* [*Azure özel bağlantı hizmeti nedir?*](../private-link/private-link-service-overview.md)