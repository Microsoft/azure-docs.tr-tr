---
title: Azure IoT Hub IP bağlantısı filtreleri | Microsoft Docs
description: IP filtrelemeyi kullanarak Azure IoT Hub 'ınıza yönelik belirli IP adreslerinden gelen bağlantıları engelleyin. Tek tek veya IP adresi aralıklarından bağlantıları engelleyebilirsiniz.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/22/2017
ms.author: robinsh
ms.openlocfilehash: a6bd8a766f3205358a65ef2fd0816643e4261cab
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414336"
---
# <a name="use-ip-filters"></a>IP filtrelerini kullanma

Güvenlik, Azure IoT Hub tabanlı tüm IoT çözümlerinin önemli bir yönüdür. Bazen, güvenlik yapılandırmanızın bir parçası olarak cihazların bağlanabileceği IP adreslerini açıkça belirtmeniz gerekir. *IP filtresi* özelliği, belirli IPv4 adreslerinden gelen trafiği reddetme veya kabul etme kurallarını yapılandırmanızı sağlar.

## <a name="when-to-use"></a>Kullanılması gereken durumlar

Belirli IP adresleri için IoT Hub uç noktalarını engellemek faydalı olduğunda iki özel kullanım durumu vardır:

* IoT Hub 'ınız yalnızca belirli bir IP adresi aralığından trafik almalıdır ve diğer her şeyi reddedebilir. Örneğin, IoT Hub 'ı ve şirket içi altyapınız arasında özel bağlantılar oluşturmak için IoT Hub 'ınızı [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) ile birlikte kullanıyorsunuz.

* IoT Hub Yöneticisi tarafından şüpheli olarak tanımlanmış IP adreslerinden gelen trafiği reddetmeniz gerekir.

## <a name="how-filter-rules-are-applied"></a>Filtre kurallarının uygulanma yöntemi

IP filtresi kuralları IoT Hub hizmet düzeyinde uygulanır. Bu nedenle, IP filtresi kuralları, desteklenen herhangi bir protokolü kullanarak cihazlarından ve arka uç uygulamalardan tüm bağlantılara uygulanır.

IoT Hub 'ınızdaki reddetme IP kuralıyla eşleşen bir IP adresinden gelen bağlantı girişimleri, yetkisiz 401 durum kodu ve açıklaması alır. Yanıt iletisi, IP kuralından bahsetmiyor.

## <a name="default-setting"></a>Varsayılan ayar

Varsayılan olarak, bir IoT Hub 'ının portalındaki **IP filtresi** Kılavuzu boştur. Bu varsayılan ayar, hub 'ınızın herhangi bir IP adresinden gelen bağlantıları kabul ettiği anlamına gelir. Bu varsayılan ayarı 0.0.0.0/0 IP adresi aralığı kabul eden bir kural eşdeğerdir.

![IoT Hub varsayılan IP filtresi ayarları](./media/iot-hub-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>IP filtresi kuralı ekleme veya düzenleme

Bir IP filtre kuralı eklemek için **+ IP filtre kuralı ekle**' yi seçin.

![IoT Hub 'ına bir IP filtre kuralı ekleme](./media/iot-hub-ip-filtering/ip-filter-add-rule.png)

**IP filtre kuralı ekle**' yi seçtikten sonra alanları girin.

![IP filtre kuralı ekle seçeneğini belirledikten sonra](./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png)

* IP filtresi kuralı için bir **ad** girin. Bu, 128 karakter uzunluğunda benzersiz, büyük/küçük harfe duyarsız, alfasayısal bir dize olmalıdır. Yalnızca ASCII 7 bit alfasayısal karakterler ve `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` kabul edilir.

* CıDR gösteriminde tek bir IPv4 adresi veya IP adresi bloğu sağlayın. Örneğin, CıDR gösteriminde 192.168.100.0/22, 192.168.100.0 'den 192.168.103.255 'e 1024 IPv4 adresini temsil eder.

* IP filtresi kuralı için **eylem** olarak **Izin ver** veya **Engelle** ' yi seçin.

Alanları doldurduktan sonra, kuralı kaydetmek için **Kaydet** ' i seçin. Güncelleştirmenin devam ettiğini bildiren bir uyarı görürsünüz.

![IP filtresi kuralını kaydetme bildirimi](./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png)

En fazla 10 IP filtresi kuralına ulaştığınızda **Ekle** seçeneği devre dışıdır.

Mevcut bir kuralı düzenlemek için, değiştirmek istediğiniz verileri seçin, değişikliği yapın ve ardından **Kaydet** ' i seçerek düzenleme bilgilerinizi kaydedin.

> [!NOTE]
> IP adreslerini reddetme, diğer Azure hizmetlerinin (Azure Stream Analytics, Azure sanal makineleri veya portaldaki Device Explorer) IoT Hub ile etkileşimde bulunmasını önleyebilir.

> [!WARNING]
> IP filtresi etkin olan bir IoT Hub 'ından iletileri okumak için Azure Stream Analytics (ASA) kullanırsanız, ASA bağlantı dizesinde IoT Hub Olay Hub 'ı ile uyumlu adı ve uç noktasını kullanın.

## <a name="delete-an-ip-filter-rule"></a>IP filtre kuralını Sil

Bir IP filtre kuralını silmek için söz konusu satırdaki çöp kutusu simgesini seçin ve ardından **Kaydet**' i seçin. Kural kaldırılır ve değişiklik kaydedilir.

![IoT Hub IP filtresi kuralını silme](./media/iot-hub-ip-filtering/ip-filter-delete-rule.png)

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Azure CLı kullanarak IP filtrelerini alma ve güncelleştirme

IoT Hub IP filtreleri, [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)aracılığıyla alınabilir ve güncelleştirilir.

IoT Hub geçerli IP filtrelerini almak için şunu çalıştırın:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Bu, mevcut IP filtrelerinizin `properties.ipFilterRules` anahtar altında listelendiği bir JSON nesnesi döndürür:

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

IoT Hub için yeni bir IP filtresi eklemek için şunu çalıştırın:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

IoT Hub var olan bir IP filtresini kaldırmak için şunu çalıştırın:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

IoT Hub için IP filtreleri sıralamasına karşılık `<ipFilterIndexToRemove>`gelmelidir. `properties.ipFilterRules`

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Azure PowerShell kullanarak IP filtrelerini alma ve güncelleştirme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

IoT Hub IP filtreleri alınabilir ve [Azure PowerShell](/powershell/azure/overview)aracılığıyla ayarlanabilir.

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>REST kullanarak IP filtresi kurallarını güncelleştirme

Ayrıca, Azure Kaynak sağlayıcısı 'nın REST uç noktasını kullanarak IoT Hub IP filtresini alabilir ve değiştirebilirsiniz. Bkz `properties.ipFilterRules` . [CreateOrUpdate yöntemi](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-rule-evaluation"></a>IP filtresi kuralı değerlendirme

IP filtresi kuralları sırasıyla uygulanır ve IP adresiyle eşleşen ilk kural kabul etme veya reddetme eylemini belirler.

Örneğin, 192.168.100.0/22 aralığındaki adresleri kabul etmek ve diğer her şeyi reddetmek istiyorsanız kılavuzdaki ilk kural 192.168.100.0/22 adres aralığını kabul etmelidir. Sonraki kural aralığı 0.0.0.0/0 kullanarak tüm adresleri reddedecek.

Kılavuzdaki IP filtre kurallarınızın sırasını, bir satırın başlangıcında bulunan üç dikey noktaya tıklayıp sürükle ve bırak seçeneğini kullanarak değiştirebilirsiniz.

Yeni IP filtresi kuralı siparişinizi kaydetmek için **Kaydet**' e tıklayın.

![IoT Hub IP filtresi kurallarınızın sırasını değiştirme](./media/iot-hub-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Sonraki adımlar

IoT Hub yeteneklerini daha fazla incelemek için bkz.:

* [İşlemleri izleme](iot-hub-operations-monitoring.md)
* [IoT Hub ölçümleri](iot-hub-metrics.md)
