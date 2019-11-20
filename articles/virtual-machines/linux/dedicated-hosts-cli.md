---
title: CLı kullanarak Azure adanmış Konakları dağıtma
description: Azure CLı kullanarak VM 'Leri adanmış konaklara dağıtın.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/29/2019
ms.author: cynthn
ms.openlocfilehash: ece9967321cfca44b102d78722f0df3d8f980bdb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036412"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>Önizleme: Azure CLı kullanarak VM 'Leri adanmış konaklara dağıtma
 

Bu makalede, sanal makinelerinizi (VM 'Ler) barındırmak için Azure [adanmış ana bilgisayar](dedicated-hosts.md) oluşturma konusunda size kılavuzluk eder. 

Azure CLı sürüm 2.0.70 veya üstünü yüklediğinizden ve `az login`kullanarak bir Azure hesabında oturum açtığınızdan emin olun. 

> [!IMPORTANT]
> Azure ayrılmış Konakları Şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Bilinen önizleme sınırlamaları**
> - Sanal Makine Ölçek Kümeleri Şu anda adanmış konaklarda desteklenmiyor.
> - Önizleme ilk sürümü şu VM serisini destekler: DSv3 ve ESv3. 
 

## <a name="create-resource-group"></a>Kaynak grubu oluşturma 
Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Az Group Create ile kaynak grubu oluşturun. Aşağıdaki örnek, *Doğu ABD* konumunda *mydhresourcegroup* adlı bir kaynak grubu oluşturur.

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>Konak grubu oluştur 

**Konak grubu** , adanmış Konakları koleksiyonunu temsil eden bir kaynaktır. Bir bölge ve kullanılabilirlik bölgesinde bir konak grubu oluşturur ve bu gruba ana bilgisayar ekleyebilirsiniz. Yüksek kullanılabilirlik için planlama yaparken ek seçenekler vardır. Aşağıdaki seçeneklerden birini veya her ikisini de adanmış konaklarınız ile kullanabilirsiniz: 
- Birden çok kullanılabilirlik alanı arasında yayılır. Bu durumda, kullanmak istediğiniz her bölgede bir konak grubunuz olması gerekir.
- Fiziksel raflarla eşlenen birden çok hata etki alanı arasında yayılma. 
 
Her iki durumda da, konak grubunuz için hata etki alanı sayısını sağlamanız gerekir. Grubunuzdaki hata etki alanlarına yayılması istemiyorsanız, hata etki alanı sayısı 1 ' i kullanın. 

Hem kullanılabilirlik bölgelerini hem de hata etki alanlarını kullanmaya karar verebilirsiniz. 

Bu örnekte, kullanılabilirlik bölgelerini ve hata etki alanlarını kullanarak bir konak grubu oluşturmak için [az VM konak grubu oluştur](/cli/azure/vm/host/group#az-vm-host-group-create) ' u kullanacağız. 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>Diğer örnekleri

Kullanılabilirlik bölge 1 ' de (hata etki alanları olmadan) bir konak grubu oluşturmak için [az VM konak grubu oluştur](/cli/azure/vm/host/group#az-vm-host-group-create) ' u de kullanabilirsiniz.

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
Aşağıdaki, yalnızca hata etki alanlarını kullanarak bir konak grubu oluşturmak için [az VM konak grubu oluştur](/cli/azure/vm/host/group#az-vm-host-group-create) ' u kullanır (kullanılabilirlik bölgelerinin desteklenmediği bölgelerde kullanılmak üzere). 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Konak Oluşturma 

Şimdi konak grubunda adanmış bir konak oluşturalım. Konak için bir ada ek olarak, ana bilgisayar için SKU sağlamanız gerekir. Ana bilgisayar SKU 'SU, desteklenen VM serisini ve adanmış ana bilgisayarınız için donanım oluşturmayı yakalar.  Önizleme sırasında, aşağıdaki konak SKU değerlerini destekliyoruz: DSv3_Type1 ve ESv3_Type1.


Konak SKU 'Ları ve fiyatlandırma hakkında daha fazla bilgi için bkz. [Azure ayrılmış ana bilgisayar fiyatlandırması](https://aka.ms/ADHPricing).

Konak oluşturmak için [az VM Host Create](/cli/azure/vm/host#az-vm-host-create) kullanın. Konak grubunuz için bir hata etki alanı sayısı ayarlarsanız, ana bilgisayarınız için hata etki alanını belirtmeniz istenir.  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma 
[Az VM Create](/cli/azure/vm#az-vm-create)kullanarak adanmış bir ana bilgisayar içinde bir sanal makine oluşturun. Konak grubunuzu oluştururken bir kullanılabilirlik alanı belirttiyseniz, sanal makineyi oluştururken aynı bölgeyi kullanmanız gerekir.

```bash
az vm create \
   -n myVM \
   --image debian \
   --generate-ssh-keys \
   --host-group myHostGroup \
   --host myHost \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```
 
> [!WARNING]
> Yeterli kaynağı olmayan bir konakta sanal makine oluşturursanız, sanal makine başarısız durumda oluşturulur. 


## <a name="check-the-status-of-the-host"></a>Konağın durumunu denetleme

Ana bilgisayar sistem durumunu ve [az VM Host Get-Instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view)kullanarak konağa ne kadar sanal makine dağıtacağınızı kontrol edebilirsiniz.

```bash
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 Çıktı şuna benzer olacaktır:
 
```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```
 
## <a name="export-as-a-template"></a>Şablon olarak dışarı aktar 
Artık aynı parametrelerle veya bununla eşleşen bir üretim ortamıyla ek bir geliştirme ortamı oluşturmak istiyorsanız bir şablonu dışarı aktarabilirsiniz. Kaynak Yöneticisi, ortamınız için tüm parametreleri tanımlayan JSON şablonları kullanır. Bu JSON şablonuna başvurarak tüm ortamları oluşturursunuz. JSON şablonlarını el ile oluşturabilir veya var olan bir ortamı, sizin için JSON şablonu oluşturmak üzere dışarı aktarabilirsiniz. Kaynak grubunuzu dışarı aktarmak için [az Group Export](/cli/azure/group#az-group-export) kullanın.

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Bu komut, geçerli çalışma dizininizde `myDHResourceGroup.json` dosyasını oluşturur. Bu şablondan bir ortam oluşturduğunuzda, sizden tüm kaynak adları istenir. `az group export` komutuna `--include-parameter-default-value` parametresini ekleyerek, bu adları şablon dosyanızda doldurabilirsiniz. Kaynak adlarını belirtmek için JSON şablonunuzu düzenleyin veya kaynak adlarını belirten Parameters. JSON dosyası oluşturun.
 
Şablonunuzda bir ortam oluşturmak için [az Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create)kullanın.

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Temizleme 

Hiçbir sanal makine dağıtılmamışsa bile adanmış ana bilgisayarlar için ücret ödersiniz. Maliyetleri kaydetmek için şu anda kullanmadığınız tüm Konakları silmelisiniz.  

Bir konağı yalnızca, onu kullanan daha fazla sanal makine olmadığında silebilirsiniz. [Az VM Delete](/cli/azure/vm#az-vm-delete)kullanarak VM 'leri silin.

```bash
az vm delete -n myVM -g myDHResourceGroup
```

VM 'Leri sildikten sonra [az VM Host Delete](/cli/azure/vm/host#az-vm-host-delete)kullanarak Konağı silebilirsiniz.

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Tüm konaklarınızı sildikten sonra [az VM konak grubu Sil](/cli/azure/vm/host/group#az-vm-host-group-delete)' i kullanarak konak grubunu silebilirsiniz.  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
Tüm kaynak grubunu tek bir komutta de silebilirsiniz. Bu işlem, tüm VM 'Ler, konaklar ve konak grupları dahil olmak üzere grupta oluşturulan tüm kaynakları siler.
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Sonraki adımlar

- Daha fazla bilgi için bkz. [adanmış ana bilgisayarlara](dedicated-hosts.md) genel bakış.

- Ayrıca, [Azure Portal](dedicated-hosts-portal.md)kullanarak adanmış konaklar da oluşturabilirsiniz.

- [Burada](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), bir bölgedeki maksimum dayanıklılık için hem bölge hem de hata etki alanı kullanan örnek şablon vardır.