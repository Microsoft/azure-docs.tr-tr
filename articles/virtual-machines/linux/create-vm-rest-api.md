---
title: Azure REST API Linux sanal makinesi oluşturma
description: Azure REST API ile yönetilen diskler ve SSH kimlik doğrulaması kullanan Azure 'da Linux sanal makinesi oluşturmayı öğrenin.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: c1010bf4bde01920449e9252de563d79bfc61997
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036439"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>REST API ile SSH kimlik doğrulaması kullanan bir Linux sanal makinesi oluşturma

Azure 'daki bir Linux sanal makinesi (VM), diskler ve ağ arabirimleri gibi çeşitli kaynaklardan oluşur ve konum, boyut ve işletim sistemi görüntüsü ve kimlik doğrulama ayarları gibi parametreleri tanımlar.

Azure portal, Azure CLı 2,0, birçok Azure SDK, Azure Resource Manager şablon ve anormal ya da Terırform gibi birçok üçüncü taraf araç aracılığıyla bir Linux sanal makinesi oluşturabilirsiniz. Bu araçların hepsi, Linux VM oluşturmak için REST API kullanır.

Bu makalede, yönetilen diskler ve SSH kimlik doğrulamasıyla Ubuntu 18,04-LTS çalıştıran bir Linux sanal makinesi oluşturmak için REST API nasıl kullanılacağı gösterilmektedir.

## <a name="before-you-start"></a>Başlamadan önce

İsteği oluşturmadan ve göndermeden önce şunları yapmanız gerekir:

* Aboneliğiniz için `{subscription-id}`
  * Birden çok aboneliğiniz varsa bkz. [birden çok abonelikle çalışma](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)
* Zaman önce oluşturduğunuz bir `{resourceGroupName}`
* Aynı kaynak grubundaki bir [sanal ağ arabirimi](../../virtual-network/virtual-network-network-interface.md)
* Bir SSH anahtar çifti (bir tane yoksa [Yeni bir tane](mac-create-ssh-keys.md) oluşturabilirsiniz)

## <a name="request-basics"></a>İstek temelleri

Bir sanal makine oluşturmak veya güncelleştirmek için aşağıdaki *PUT* işlemini kullanın:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

`{subscription-id}` ve `{resourceGroupName}` parametrelerine ek olarak `{vmName}` belirtmeniz gerekir (`api-version` isteğe bağlıdır, ancak bu makale `api-version=2017-12-01`ile test edilmiştir)

Aşağıdaki üstbilgiler gereklidir:

| İstek üst bilgisi   | Açıklama |
|------------------|-----------------|
| *Content-Type:*  | Gereklidir. `application/json` olarak ayarlayın. |
| *Yetkilendirme:* | Gereklidir. Geçerli bir `Bearer` [erişim belirtecine](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) ayarlayın. |

REST API isteklerle çalışma hakkında genel bilgi için, bkz. [bir REST API isteği/yanıtı bileşenleri](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>İstek gövdesini oluşturma

Aşağıdaki ortak tanımlar bir istek gövdesi oluşturmak için kullanılır:

| Ad                       | Gerekli | Tür                                                                                | Açıklama  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | string                                                                              | Kaynak konumu. |
| ad                       |          | string                                                                              | Sanal makinenin adı. |
| Properties. hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Sanal makine için donanım ayarlarını belirtir. |
| Properties. storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Sanal makine disklerinin depolama ayarlarını belirtir. |
| Properties. osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Sanal makine için işletim sistemi ayarlarını belirtir. |
| Properties. networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Sanal makinenin ağ arabirimlerini belirtir. |

Örnek bir istek gövdesi aşağıda verilmiştir. VM adını `{computerName}` ve `{name}` parametrelerinde, `networkInterfaces`altında oluşturduğunuz ağ arabiriminin adını, `adminUsername` ve `path`Kullanıcı adınızı ve SSH KeyPair 'nin (örneğin, `~/.ssh/id_rsa.pub`) *ortak* bölümünü `keyData`belirttiğinizden emin olun. Değiştirmek isteyebileceğiniz diğer parametreler `location` ve `vmSize`içerir.  

```json
{
  "location": "eastus",
  "name": "{vmName}",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "{vmName}",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

İstek gövdesindeki kullanılabilir tanımlarının tüm listesi için bkz. [sanal makineler istek gövdesi tanımları oluşturma veya güncelleştirme](/rest/api/compute/virtualmachines/createorupdate#definitions).

## <a name="sending-the-request"></a>İstek gönderiliyor

Bu HTTP isteğini göndermek için tercih ettiğiniz istemciyi kullanabilirsiniz. Ayrıca, **deneyin** düğmesine tıklayarak [tarayıcı içi bir araç](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate) da kullanabilirsiniz.

### <a name="responses"></a>Responses

Bir sanal makineyi oluşturma veya güncelleştirme işlemi için iki başarılı yanıt vardır:

| Ad        | Tür                                                                              | Açıklama |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 TAMAM      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Tamam          |
| 201 oluşturuldu | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Oluşturulan     |

Bir VM oluşturan önceki örnek istek gövdesinden sıkıştırılmış *201 tarafından oluşturulan* bir yanıt, bir *VMID* atandığını ve *provisioningstate* 'in *oluşturuyor*olduğunu gösterir:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

REST API yanıtları hakkında daha fazla bilgi için bkz. [Yanıt Iletisini işleme](/rest/api/azure/#process-the-response-message).

## <a name="next-steps"></a>Sonraki adımlar

Azure REST API 'Leri veya Azure CLı veya Azure PowerShell gibi diğer yönetim araçları hakkında daha fazla bilgi için aşağıdakilere bakın:

- [Azure Işlem sağlayıcısı REST API](/rest/api/compute/)
- [Azure REST API’yi kullanmaya başlayın](/rest/api/azure/)
- [Azure CLI](/cli/azure/)
- [Azure PowerShell modülü](/powershell/azure/overview)
