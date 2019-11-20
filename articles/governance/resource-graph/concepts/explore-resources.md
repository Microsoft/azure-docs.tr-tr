---
title: Azure kaynaklarınızı inceleme
description: Kaynak grafik sorgu dilini kullanarak kaynaklarınızı keşfetme ve bunların nasıl bağlandığını bulma hakkında bilgi edinin.
ms.date: 10/18/2019
ms.topic: conceptual
ms.openlocfilehash: e489a4eafdbbd838c4850d67fcd8ec40f76f290c
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73959232"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Kaynak Grafiği ile Azure kaynaklarınızı keşfedin

Azure Kaynak Grafiği, Azure kaynaklarınızı hızlı ve ölçeklenebilir bir şekilde keşfetme ve bulma özelliği sağlar. Hızlı yanıtlar için tasarlanan bu, ortamınız ve ayrıca Azure kaynaklarınızı oluşturan özellikler hakkında bilgi edinmek için harika bir yoldur.

## <a name="explore-virtual-machines"></a>Sanal makineleri keşfet

Azure 'daki ortak bir kaynak bir sanal makinedir. Kaynak türü olarak, sanal makinelerin sorgulanabilecek birçok özelliği vardır. Her özellik, aradığınız kaynağı filtrelemek veya bulmak için bir seçenek sağlar.

### <a name="virtual-machine-discovery"></a>Sanal makine bulma

Ortamınızdaki tek bir VM 'yi almak için basit bir sorgu ile başlayalım ve döndürülen özelliklere bakın.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> Azure PowerShell `Search-AzGraph` cmdlet 'i varsayılan olarak bir **PSCustomObject** döndürür. Çıktının Azure CLı tarafından döndürülen ile aynı görünmesi için `ConvertTo-Json` cmdlet 'i kullanılır. **Derinlik** için varsayılan değer _2_' dir. _100_ olarak ayarlanması, döndürülen tüm düzeyleri dönüştürmelidir.

JSON sonuçları, aşağıdaki örneğe benzer şekilde yapılandırılır:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1",
    "plan": {},
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_B2s"
      },
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535",
            "resourceGroup": "MyResourceGroup"
          }
        ]
      },
      "osProfile": {
        "adminUsername": "localAdmin",
        "computerName": "ContosoVM1",
        "secrets": [],
        "windowsConfiguration": {
          "enableAutomaticUpdates": true,
          "provisionVMAgent": true
        }
      },
      "provisioningState": "Succeeded",
      "storageProfile": {
        "dataDisks": [],
        "imageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": 127,
          "managedDisk": {
            "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
            "resourceGroup": "MyResourceGroup",
            "storageAccountType": "Premium_LRS"
          },
          "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
          "osType": "Windows"
        }
      },
      "vmId": "bbb9b451-6dc7-4117-bec5-c971eb1118c6"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {},
    "subscriptionId": "<subscriptionId>",
    "tags": {},
    "type": "microsoft.compute/virtualmachines"
  }
]
```

Özellikler, sanal makine kaynağının kendisi, SKU, işletim sistemi, diskler, Etiketler ve kaynak grubu ve aboneliğin üyesi olduğu her şey hakkındaki ek bilgileri bize söyler.

### <a name="virtual-machines-by-location"></a>Konuma göre sanal makineler

Sanal makineler kaynağı hakkında öğrendiğimiz şeyler, konuma göre tüm sanal makineleri saymak için **Location** özelliğini kullanalım. Sorguyu güncelleştirmek için sınırı kaldıracağız ve konum değerlerinin sayısını özetleyeceğiz.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

JSON sonuçları, aşağıdaki örneğe benzer şekilde yapılandırılır:

```json
[
  {
    "count_": 386,
    "location": "eastus"
  },
  {
    "count_": 215,
    "location": "southcentralus"
  },
  {
    "count_": 59,
    "location": "westus"
  }
]
```

Artık her bir Azure bölgesinde kaç tane sanal makine olduğunu görebiliriz.

### <a name="virtual-machines-by-sku"></a>SKU 'ya göre sanal makineler

Özgün sanal makine özelliklerine geri dönerek, **STANDARD_B2S**SKU boyutu olan tüm sanal makineleri bulmayı deneyelim. Döndürülen JSON 'a bakarak **Properties. HardwareProfile. VMSize**yolunda depolandığını görüyoruz. Bu boyutla eşleşen tüm VM 'Leri bulmak ve yalnızca VM ve bölgenin adını döndürmek için sorguyu güncelleştireceğiz.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup"
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Premium yönetilen disklere bağlı sanal makineler

Bu **Standard_B2s** sanal makinelere eklenmiş Premium yönetilen disklerin ayrıntılarını almak istiyorsam, bu yönetilen DISKLERIN kaynak kimliğini sağlamak için sorguyu genişletebilirsiniz.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

> [!NOTE]
> SKU 'YU almanın başka bir yolu da **Microsoft. COMPUTE/virtualMachines/SKU. Name** **diğer** ad özelliği kullanılarak yapılır. [Diğer adları göster](../samples/starter.md#show-aliases) ve [farklı diğer ad değerlerini göster](../samples/starter.md#distinct-alias-values) örneklerine bakın.

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

Sonuç, disk kimliklerinin bir listesidir.

### <a name="managed-disk-discovery"></a>Yönetilen disk bulma

Önceki sorgudaki ilk kayıt ile, ilk sanal makineye eklenmiş yönetilen diskte bulunan özellikleri araştıracağız. Güncelleştirilmiş sorgu disk KIMLIĞINI kullanır ve türü değiştirir.

Örneğin, önceki sorgudaki örnek çıktı:

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
Resources
| where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

Sorguyu çalıştırmadan önce, **türü** Şu anda **Microsoft. COMPUTE/Disks**olarak biliyoruz?
Tam KIMLIĞE bakarsanız dizenin bir parçası olarak **/providers/Microsoft.COMPUTE/Disks/** görürsünüz. Bu dize parçası, arama yapılacak tür için bir ipucu sağlar. Farklı bir yöntem, sınırı türe göre kaldırmak ve bunun yerine yalnızca KIMLIK alanına göre aramak olacaktır. KIMLIK benzersiz olduğundan, yalnızca bir kayıt döndürülür ve üzerinde **tür** özelliği bu ayrıntıyı sağlar.

> [!NOTE]
> Bu örneğin çalışması için, KIMLIK alanını kendi ortamınızdaki bir sonuçla değiştirmelisiniz.

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

JSON sonuçları, aşağıdaki örneğe benzer şekilde yapılandırılır:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "plan": {},
    "properties": {
      "creationData": {
        "createOption": "Empty"
      },
      "diskSizeGB": 127,
      "diskState": "ActiveSAS",
      "provisioningState": "Succeeded",
      "timeCreated": "2018-09-14T12:17:32.2570000Z"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {
      "name": "Premium_LRS",
      "tier": "Premium"
    },
    "subscriptionId": "<subscriptionId>",
    "tags": {
      "environment": "prod"
    },
    "type": "microsoft.compute/disks"
  }
]
```

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>Ortak IP adreslerini bulmak için sanal makineleri keşfedin

Bu sorgu kümesi önce, sanal makinelere bağlı tüm ağ arabirimleri (NIC) kaynaklarını bulur ve depolar. Ardından sorgular, bir genel IP adresi olan her IP adresi kaynağını bulmak ve bu değerleri depolamak için NIC 'ler listesini kullanır. Son olarak, sorgular genel IP adreslerinin bir listesini sağlar.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nics.txt' file
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

```azurepowershell-interactive
# Use Resource Graph to get all NICs and store in the $nics variable
$nics = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20"

# Review the output of the query stored in the variable
$nics.nic
```

NIC 'e bağlı bir genel IP adresinin bulunduğu ilgili ağ arabirimi kaynakları ayrıntılarını almak için sonraki sorgudaki dosyayı (Azure CLı) veya değişkenini (Azure PowerShell) kullanın.

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

```azurepowershell-interactive
# Use Resource Graph  with the $nics variable to get all related public IP addresses and store in $ips variable
$ips = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$($nics.nic -join "','")') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp"

# Review the output of the query stored in the variable
$ips.publicIp
```

Son olarak, ilgili nesne ve görüntü ' den gerçek genel IP adresini almak için dosyada (Azure CLı) veya değişkende (Azure PowerShell) depolanan genel IP adresi kaynaklarının listesini kullanın.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

```azurepowershell-interactive
# Use Resource Graph with the $ips variable to get the IP address of the public IP address resources
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$($ips.publicIp -join "','")') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip"
```

Bu adımları `join` işleçle tek bir sorguda nasıl gerçekleştireceğinizi görmek için, [sanal makineleri ağ arabirimiyle listeleme ve genel IP](../samples/advanced.md#join-vmpip) örneği ' ne bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [Sorgu dili](query-language.md)hakkında daha fazla bilgi edinin.
- Bkz. [Başlangıç sorgularında](../samples/starter.md)kullanılan dil.
- Gelişmiş [sorgularda](../samples/advanced.md)gelişmiş kullanımlar bölümüne bakın.