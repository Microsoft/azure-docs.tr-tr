---
title: Azure CLı ile DevTest Labs 'de sanal makineler oluşturma ve yönetme
description: Azure CLı ile sanal makineler oluşturmak ve yönetmek için Azure DevTest Labs kullanmayı öğrenin
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 95e0add8ce14e47c609b1ae951673c261316293f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763550"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Azure CLı kullanarak DevTest Labs ile sanal makineler oluşturma ve yönetme
Bu hızlı başlangıç, laboratuvarınızda bir geliştirme makinesi oluşturma, başlatma, bağlama, güncelleştirme ve Temizleme sürecinde size kılavuzluk eder. 

Başlamadan önce:

* Bir laboratuvar oluşturulmadıysa, yönergeler [burada](devtest-lab-create-lab.md)bulunabilir.

* [Azure CLI 'Yı yükler](/cli/azure/install-azure-cli). Başlamak için az oturum çalıştırarak Azure ile bir bağlantı oluşturun. 

## <a name="create-and-verify-the-virtual-machine"></a>Sanal makineyi oluşturma ve doğrulama 
DevTest Labs ile ilgili komutları yürütmeden önce, komutunu kullanarak uygun Azure bağlamını ayarlayın `az account set` :

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

Sanal makine oluşturma komutu: `az lab vm create` . Laboratuvar, laboratuvar adı ve sanal makine adı için kaynak grubunun tümü gereklidir. Bağımsız değişkenlerin geri kalanı, sanal makine türüne bağlı olarak değişir.

Aşağıdaki komut, Azure Pazar yerinde Windows tabanlı bir görüntü oluşturur. Görüntünün adı, Azure portal kullanarak bir sanal makine oluştururken göreceğiniz şekilde aynıdır. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Aşağıdaki komut, laboratuvarda kullanılabilen özel bir görüntüye dayalı bir sanal makine oluşturur:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

**Image-Type** bağımsız değişkeni **Galeriden** **özel** olarak değiştirildi. Azure portal sanal makineyi oluştururken, görüntünün adı, gördüklerinize göre eşleşir.

Aşağıdaki komut, bir market görüntüsünden SSH kimlik doğrulaması ile bir VM oluşturur:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Ayrıca, **resim türü** parametresini **Formül** olarak ayarlayarak, formülleri temel alan sanal makineler de oluşturabilirsiniz. Sanal makineniz için belirli bir sanal ağ seçmeniz gerekiyorsa, **VNET-adı** ve **alt ağ** parametrelerini kullanın. Daha fazla bilgi için bkz. [az Lab VM Create](/cli/azure/lab/vm#az_lab_vm_create).

## <a name="verify-that-the-vm-is-available"></a>VM 'nin kullanılabilir olduğunu doğrulayın.
Başlamadan `az lab vm show` önce VM 'nin kullanılabilir olduğunu doğrulamak için komutunu kullanın. 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Başlat ve sanal makineyi bağla
Aşağıdaki örnek komut bir VM başlatır:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Bir VM 'ye bağlanın: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) veya [Uzak Masaüstü](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Sanal makineyi Güncelleştir
Aşağıdaki örnek komut, yapıları bir VM 'ye uygular:

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

### <a name="list-artifacts-available-in-the-lab"></a>Laboratuvarda bulunan yapıları listeleyin

Laboratuvardaki bir sanal makinede bulunan yapıtları listelemek için aşağıdaki komutları çalıştırın.

**Cloud Shell-PowerShell**: $ in $Expand önce backtick () kullanımına dikkat edin \` (örn. ' $Expand):

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud Shell-Bash**: \\ komutta $ 'ın önünde eğik çizgi () karakterinin kullanılmasına dikkat edin. 

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(\$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

Örnek çıktı: 

```json
[
  {
    "artifactId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DevTestLab/labs/<lab name>/artifactSources/public repo/artifacts/windows-7zip",
    "status": "Succeeded"
  }
]
```

## <a name="stop-and-delete-the-virtual-machine"></a>Sanal Makineyi durdur ve Sil    
Aşağıdaki örnek komut bir VM 'yi sonlandırır.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Bir sanal makineyi silme.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki içeriğe bakın: [Azure DevTest Labs Için Azure CLI belgeleri](/cli/azure/lab). 
