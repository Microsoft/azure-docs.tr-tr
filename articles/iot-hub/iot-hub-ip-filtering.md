---
title: Azure IoT Hub IP filtreleri | Microsoft Docs
description: Azure IoT Hub 'ınıza yönelik belirli IP adreslerinden gelen bağlantılara izin vermek için IP filtrelemeyi kullanma.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: jlian
ms.openlocfilehash: bf9d58926c5a0fdc7c305e1d9daebfa1c8c9cf63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023614"
---
# <a name="use-ip-filters"></a>IP filtrelerini kullanma

Güvenlik, Azure IoT Hub tabanlı tüm IoT çözümlerinin önemli bir yönüdür. Bazen güvenlik yapılandırmanız kapsamında cihazların hangi IP adreslerinden bağlanabileceğini açıkça belirtmeniz gerekir. *IP filtresi* özelliği, belirli IPv4 adreslerinden gelen trafiği reddetme veya kabul etme kurallarını yapılandırmanızı sağlar.

## <a name="when-to-use"></a>Kullanılması gereken durumlar

Yalnızca belirli bir IP adresi aralığından trafik almak ve diğer her şeyi reddetmek için IP filtresini kullanın. Örneğin, IoT Hub 'ı ve şirket içi altyapınız arasında özel bağlantılar oluşturmak için IoT Hub 'ınızı [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) ile birlikte kullanıyorsunuz.

## <a name="default-setting"></a>Varsayılan ayar

IP filtresi ayarları sayfasına ulaşmak için **ağ**, **ortak erişim**' i SEÇIN ve ardından **Seçili IP aralıkları**' nı seçin:

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="IoT Hub varsayılan IP filtresi ayarları":::

Varsayılan olarak, bir IoT Hub 'ının portalındaki **IP filtresi** Kılavuzu boştur. Bu varsayılan ayar, hub 'ınızın tüm IP adreslerinden gelen bağlantıları engellediği anlamına gelir. Bu varsayılan ayar, IP adresi aralığını engelleyen bir kuralla eşdeğerdir `0.0.0.0/0` .

## <a name="add-or-edit-an-ip-filter-rule"></a>IP filtresi kuralını ekleme veya düzenleme

IP filtresi kuralı eklemek için **+ IP Filtresi Kuralı Ekle**'yi seçin. Bilgisayarınızın IP adresini hızlıca eklemek için, **ISTEMCI IP adresini ekle**' ye tıklayın. 

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="IoT Hub 'ına bir IP filtre kuralı ekleme":::

**IP Filtresi Kuralı Ekle**'yi seçtikten sonra alanları doldurun. İstemci IP adresinizi eklemeyi seçtiyseniz, bu alanlar sizin için önceden doldurulur.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="IP Filtresi Kuralı Ekle'yi seçtikten sonra":::

* IP Filtresi kuralı için bir **ad** girin. Bu ad, 128 karakter uzunluğunda benzersiz, büyük/küçük harfe duyarsız, alfasayısal bir dize olmalıdır. Yalnızca ASCII 7-bit alfasayısal karakterler ile `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` karakterleri kabul edilir.

* Tek IPv4 adresi veya CIDR gösteriminde bir IP adresleri bloğu belirtin. Örneğin CIDR gösteriminde 192.168.100.0/22, 192.168.100.0 ile 192.168.103.255 arasındaki 1024 IPv4 adresini temsil eder.

Alanları doldurduktan sonra **Kaydet**'i seçerek kuralı kaydedin. Güncelleştirmenin devam ettiğini bildiren bir uyarı görürsünüz.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="IP filtresi kuralını kaydetme bildirimi":::

IP filtresi kurallarının üst sınırı olan 10 kurala ulaştığınızda **Ekle** seçeneği devre dışı bırakılır.

Mevcut kuralı düzenlemek için, değiştirmek istediğiniz verileri seçin, değişikliği yapın ve sonra da **Kaydet**'i seçerek düzenlemenizi kaydedin.

## <a name="delete-an-ip-filter-rule"></a>IP filtresi kuralını silme

IP filtresi kuralını silmek için, söz konusu satırdaki çöp kutusu simgesini seçin ve sonra da **Kaydet**'i seçin. Kural kaldırılır ve değişiklik kaydedilir.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="IoT Hub IP filtresi kuralını silme":::

## <a name="apply-ip-filter-rules-to-the-built-in-event-hub-compatible-endpoint"></a>Yerleşik Olay Hub 'ı ile uyumlu uç noktaya IP filtresi kuralları uygulama

IP filtresi kurallarını yerleşik Olay Hub 'ı ile uyumlu uç noktaya uygulamak için, **yerleşik uç noktaya IP filtreleri Uygula**' nın yanındaki kutuyu işaretleyin ve ardından **Kaydet**' i seçin.

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-built-in-endpoint.png" alt-text="Yerleşik uç nokta için geçişi gösteren resim ve Kaydet":::

> [!NOTE]
> Bu seçenek ücretsiz (F1) IoT Hub 'ları için kullanılamaz. Yerleşik uç noktaya IP filtresi kuralları uygulamak için ücretli bir IoT Hub 'ı kullanın.

Bu seçeneği etkinleştirerek, IP filtresi kurallarınız yerleşik uç noktaya çoğaltılır, bu nedenle yalnızca güvenilir IP aralıkları buna erişebilir.

Bu seçeneği devre dışı bırakırsanız, yerleşik uç noktaya tüm IP adreslerinden erişilebilir. Bu davranış, Azure Stream Analytics gibi değişen IP adresleri olan hizmetlerle uç noktadan okumak istiyorsanız yararlı olabilir. 

## <a name="how-filter-rules-are-applied"></a>Filtre kuralları nasıl uygulanır?

IP filtresi kuralları IoT Hub hizmet düzeyinde uygulanır. Bu nedenle, IP filtresi kuralları, desteklenen herhangi bir protokolü kullanarak cihazlarından ve arka uç uygulamalardan tüm bağlantılara uygulanır. Ayrıca, [yerleşik Olay Hub 'ı ile uyumlu uç noktanın](iot-hub-devguide-messages-read-builtin.md) (IoT Hub bağlantı dizesi aracılığıyla değil) bu kurallara bağlanıp bağlanmadığından da seçim yapabilirsiniz. 

Açıkça izin verilmeyen bir IP adresinden gelen bağlantı girişimleri, yetkisiz 401 durum kodu ve açıklaması alır. Yanıt iletisinde IP kuralı belirtilmez. IP adreslerini reddetme, Azure Stream Analytics, Azure sanal makineleri veya Azure portal Device Explorer, IoT Hub ile etkileşime geçmek gibi diğer Azure hizmetlerini engelleyebilir.

> [!NOTE]
> IP filtresi etkinleştirilmiş bir IoT Hub 'ından iletileri okumak için Azure Stream Analytics (ASA) kullanmanız gerekiyorsa, **IP filtrelerini yerleşik uç noktaya Uygula** seçeneğine **devre dışı bırakın** ve ardından, IoT Hub 'ınızın Olay Hub 'ı ile uyumlu adını ve uç noktasını kullanarak ASA el ile bir [Event Hubs Stream girişi](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) ekleyin.

### <a name="ordering"></a>Sıralama

IP filtresi kuralları, kurallara *izin verir* ve sıralama yapılmadan uygulanabilir. Yalnızca eklediğiniz IP adreslerinin IoT Hub bağlanmasına izin verilir. 

Örneğin, aralıktaki adresleri kabul etmek `192.168.100.0/22` ve diğer her şeyi reddetmek isterseniz, kılavuza adres aralığı ile yalnızca bir kural eklemeniz gerekir `192.168.100.0/22` .

### <a name="azure-portal"></a>Azure portalı 

Azure portal üzerinden IoT Hub kullanılırken IP filtresi kuralları da uygulanır. Bunun nedeni, IoT Hub hizmetine yönelik API çağrılarının, diğer Azure hizmetleriyle tutarlı olan kimlik bilgilerinizle doğrudan tarayıcınızı kullanarak yapılmadığı anlamına gelir. IP filtresi etkinleştirildiğinde Azure portal kullanarak IoT Hub erişmek için bilgisayarınızın IP adresini allowlist olarak ekleyin. 

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Azure CLı kullanarak IP filtrelerini alma ve güncelleştirme

IoT Hub IP filtreleri, [Azure CLI](/cli/azure/)aracılığıyla alınabilir ve güncelleştirilir.

IoT Hub geçerli IP filtrelerini almak için şunu çalıştırın:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Bu, mevcut IP filtrelerinizin anahtar altında listelendiği bir JSON nesnesi döndürür `properties.networkRuleSets` :

```json
{
...
    "properties": {
        "networkRuleSets": {
            "defaultAction": "Deny",
            "applyToBuiltInEventHubEndpoint": true,
            "ipRules": [{
                    "filterName": "TrustedFactories",
                    "action": "Allow",
                    "ipMask": "1.2.3.4/5"
                },
                {
                    "filterName": "TrustedDevices",
                    "action": "Allow",
                    "ipMask": "1.1.1.1/1"
                }
            ]
        }
    }
}
```

IoT Hub için yeni bir IP filtresi eklemek için şunu çalıştırın:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules "{\"action\":\"Allow\",\"filterName\":\"TrustedIP\",\"ipMask\":\"192.168.0.1\"}"
```

IoT Hub var olan bir IP filtresini kaldırmak için şunu çalıştırın:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules <ipFilterIndexToRemove>
```

Burada, `<ipFilterIndexToRemove>` IoT Hub ' DEKI IP filtrelerinin sıralamasına karşılık gelmelidir `properties.networkRuleSets.ipRules` .

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Azure PowerShell kullanarak IP filtrelerini alma ve güncelleştirme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

IoT Hub IP filtreleri alınabilir ve [Azure PowerShell](/powershell/azure/)aracılığıyla ayarlanabilir.

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.networkRuleSets.ipRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='TrustedIP'; 'action'='Allow'; 'ipMask'='192.168.0.1'}

# Add your new IP filter rule
$iothubResource.Properties.networkRuleSets.ipRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.networkRuleSets.ipRules = @($iothubResource.Properties.networkRuleSets.ipRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>REST kullanarak IP filtresi kurallarını güncelleştirme


Ayrıca, Azure Kaynak sağlayıcısı 'nın REST uç noktasını kullanarak IoT Hub IP filtresini alabilir ve değiştirebilirsiniz. [createorupdate yöntemi](/rest/api/iothub/iothubresource/createorupdate) altında `properties.networkRuleSets` bölümüne bakın.

## <a name="ip-filter-classic-retirement"></a>IP Filtresi (klasik) emeklilik

Klasik IP filtresi kullanımdan kaldırıldı. Daha fazla bilgi için bkz. [klasık IP filtresi IoT Hub ve yükseltme](iot-hub-ip-filter-classic.md).

## <a name="next-steps"></a>Sonraki adımlar

IoT Hub yeteneklerini daha fazla incelemek için bkz.:

* [IoT Hub ölçümleri](./monitor-iot-hub.md)
* [Özel bağlantı ve yönetilen kimlik ile sanal ağlar için IoT Hub desteği](virtual-network-support.md)
* [IoT Hub 'ınız için ortak ağ erişimini yönetme](iot-hub-public-network-access.md)
* [IoT Hub’ı izleme](monitor-iot-hub.md)
