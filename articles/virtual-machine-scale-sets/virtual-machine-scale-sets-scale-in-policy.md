---
title: Azure sanal makine ölçek kümeleri ile özel ölçeklendirme ilkeleri kullanma
description: Örnek sayısını yönetmek için otomatik ölçeklendirme yapılandırması kullanan Azure sanal makine ölçek kümeleri ile özel ölçeklendirme ilkelerini nasıl kullanacağınızı öğrenin
services: virtual-machine-scale-sets
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: scale-in-policy
ms.date: 02/26/2020
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 10ed223311995e746beca24f7a09d795f7b3f0e6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935712"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Azure sanal makine ölçek kümeleri ile özel ölçeklendirme ilkeleri kullanma

Bir sanal makine ölçek kümesi dağıtımı, platform ve Kullanıcı tanımlı özel ölçümler de dahil olmak üzere bir ölçüm dizisine göre ölçeklendirilebilir veya ölçeklendirilebilir. Ölçeği genişletme, ölçek kümesi modeline göre yeni sanal makineler oluşturduğunda, ölçek kümesi iş yükü geliştikçe farklı yapılandırmalara ve/veya işlevlere sahip olabilecek çalışan sanal makineleri etkiler. 

Ölçek genişletme İlkesi özelliği, kullanıcılara, üç ölçekli yapılandırma yoluyla sanal makinelerin ölçeklendiği sırayı yapılandırmak için bir yol sağlar: 

1. Varsayılan
2. NewestVM
3. OldestVM

### <a name="default-scale-in-policy"></a>Varsayılan ölçeklendirme ilkesi

Varsayılan olarak, sanal makine ölçek kümesi bu ilkeyi hangi örneklerin ölçeklendirileceğini belirleyecek şekilde uygular. *Varsayılan* Ilkeyle, sanal makineler aşağıdaki sırada ölçek için seçilir:

1. Kullanılabilirlik alanları genelinde sanal makineleri Dengeleme (ölçek kümesi bölgesel yapılandırmasında dağıtılmışsa)
2. Hata etki alanları genelinde sanal makinelerin dengelenmesi (en iyi çaba)
3. En yüksek örnek KIMLIĞIYLE sanal makineyi Sil

Kullanıcıların yalnızca varsayılan sıralamayı takip etmek istiyorsanız, ölçek bir ilke belirtmeleri gerekmez.

Kullanılabilirlik alanları veya hata etki alanları genelinde dengelemenin örnekleri kullanılabilirlik alanları veya hata etki alanları arasında taşımadığını unutmayın. Dengeleme, sanal makinelerin dağıtılması dengeli hale gelene kadar, dengesiz kullanılabilirlik bölgelerinden veya hata etki alanlarından sanal makinelerin silinmesinden elde edilir.

### <a name="newestvm-scale-in-policy"></a>NewestVM ölçek genişletme İlkesi

Bu ilke, kullanılabilirlik alanları genelinde VM 'leri dengeledikten sonra ölçek kümesindeki en yeni oluşturulan sanal makineyi siler (bölgesel dağıtımları için). Bu ilkeyi etkinleştirmek, sanal makine ölçek kümesi modelinde bir yapılandırma değişikliği gerektirir.

### <a name="oldestvm-scale-in-policy"></a>OldestVM ölçeklendirme ilkesi

Bu ilke, kullanılabilirlik alanları genelinde VM 'leri dengeledikten sonra ölçek kümesindeki en eski oluşturulan sanal makineyi siler (bölgesel dağıtımları için). Bu ilkeyi etkinleştirmek, sanal makine ölçek kümesi modelinde bir yapılandırma değişikliği gerektirir.

## <a name="enabling-scale-in-policy"></a>Ölçek Genişletme ilkesini etkinleştirme

Ölçek genişletme İlkesi, sanal makine ölçek kümesi modelinde tanımlanmıştır. Yukarıdaki bölümlerde belirtildiği gibi, ' NewestVM ' ve ' OldestVM ' ilkeleri kullanılırken bir ölçek genişletme ilke tanımı gerekir. Ölçek kümesi modelinde ölçek genişletme ilke tanımı yoksa, sanal makine ölçek kümesi otomatik olarak ' varsayılan ' ölçekleme ilkesini kullanır. 

Bir ölçek genişletme İlkesi, sanal makine ölçek kümesi modelinde aşağıdaki yollarla tanımlanabilir:

### <a name="azure-portal"></a>Azure portalı
 
Aşağıdaki adımlar, yeni bir ölçek kümesi oluştururken ölçek genişletme ilkesini tanımlar. 
 
1. **Sanal makine ölçek kümelerine** gidin.
1. Yeni bir ölçek kümesi oluşturmak için **+ Ekle** ' yi seçin.
1. **Ölçeklendirme** sekmesine gidin. 
1. **Ölçek genişletme İlkesi** bölümünü bulun.
1. Açılan listeden bir ölçek genişletme İlkesi seçin.
1. Yeni ölçek kümesini oluşturmayı tamamladığınızda, **gözden geçir + oluştur** düğmesini seçin.

### <a name="using-api"></a>API'yi kullanma

API 2019-03-01 kullanarak sanal makine ölçek kümesinde bir PUT yürütün:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["OldestVM"]  
        } 
    }    
} 
```
### <a name="azure-powershell"></a>Azure PowerShell

Bir kaynak grubu oluşturun ve ardından, *Oldestvm* olarak ayarlanan ölçek genişletme ilkesiyle yeni bir ölçek kümesi oluşturun.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Aşağıdaki örnek, yeni bir ölçek kümesi oluştururken bir ölçek genişletme İlkesi ekler. İlk olarak bir kaynak grubu oluşturun ve ardından *Oldestvm* olarak ölçek genişletme ilkesiyle yeni bir ölçek kümesi oluşturun. 

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Şablon kullanma

Şablonunuzda, "Özellikler" altında aşağıdakileri ekleyin:

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

Yukarıdaki bloklar, sanal makine ölçek kümesinin, bir ölçek tetiklendiğinde (otomatik ölçeklendirme veya el ile silme aracılığıyla) bölge dengelenmiş ölçek kümesindeki en eski VM 'yi silemeyeceğini belirtir.

Bir sanal makine ölçek kümesi bölge dengeli olmadığında, ölçek kümesi önce sanal makineleri imdengeli bölge (lar) da siler. İmdenden dengelenmiş bölgelerde, ölçek kümesi yukarıda belirtilen ölçek genişletme ilkesini kullanarak hangi VM 'nin ölçeklendirileyeceğini tespit eder. Bu durumda, imdenden dengelenmiş bir bölgede, ölçek kümesi silinecek bu bölgedeki en eski VM 'yi seçer.

Ölçeklendirilmemiş sanal makine ölçek kümesi için ilke, ölçek kümesi genelinde silinmek üzere en eski VM 'yi seçer.

Yukarıdaki ölçek-ın ilkesinde ' NewestVM ' kullanılırken aynı işlem geçerlidir.

## <a name="modifying-scale-in-policies"></a>Ölçek Genişletme ilkelerini değiştirme

Ölçek Genişletme ilkesini değiştirmek, ölçek genişletme ilkesini uygulamayla aynı işlemi izler. Örneğin, yukarıdaki örnekte, ilkeyi ' OldestVM ' iken ' NewestVM ' olarak değiştirmek istiyorsanız şunu yaparak şunları yapabilirsiniz:

### <a name="azure-portal"></a>Azure portalı

Mevcut bir ölçek kümesinin ölçek değiştirme ilkesini Azure portal aracılığıyla değiştirebilirsiniz. 
 
1. Var olan bir sanal makine ölçek kümesinde, sol taraftaki menüden **ölçekleme** ' ı seçin.
1. **Ölçek genişletme İlkesi** sekmesini seçin.
1. Açılan listeden bir ölçek genişletme İlkesi seçin.
1. İşiniz bittiğinde **Kaydet**‘i seçin. 

### <a name="using-api"></a>API'yi kullanma

API 2019-03-01 kullanarak sanal makine ölçek kümesinde bir PUT yürütün:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01 

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["NewestVM"]  
        } 
    }    
}
```
### <a name="azure-powershell"></a>Azure PowerShell

Varolan ölçek kümesinin ölçek genişletme ilkesini güncelleştirin:

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Aşağıda, var olan bir ölçek kümesinin ölçek genişletme ilkesini güncelleştirmeye yönelik bir örnek verilmiştir: 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Şablon kullanma

Şablonunuzda, "Özellikler" altında, şablonu aşağıda gösterildiği gibi değiştirin ve yeniden dağıtın: 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

' NewestVM ' öğesini ' default ' veya ' OldestVM ' olarak değiştirmeye karar verirseniz aynı işlem geçerlidir

## <a name="instance-protection-and-scale-in-policy"></a>Örnek koruma ve ölçek genişletme İlkesi

Sanal makine ölçek kümeleri iki tür [örnek koruma](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection)sağlar:

1. Ölçeklendirmeye karşı koruma
2. Ölçek kümesi eylemlerine karşı koruma

Korunan bir sanal makine, uygulanan ölçek ilke eyleminden bağımsız olarak, ölçek temelli bir eylem aracılığıyla silinmez. Örneğin, VM_0 (ölçek kümesindeki en eski VM), ölçeklendirmeden korunuyorsa ve ölçek kümesinde ' OldestVM ' ölçeği etkinse, ölçek kümesinde en eski VM olmasına rağmen, VM_0 ölçeklendirilmez. 

Korunan bir sanal makine, ölçek kümesinde etkin olan ölçek temelli ilkeden bağımsız olarak, Kullanıcı tarafından herhangi bir zamanda el ile silinebilir. 

## <a name="usage-examples"></a>Kullanım örnekleri 

Aşağıdaki örneklerde, bir sanal makine ölçek kümesinin, bir ölçek olayı tetiklendiğinde silinecek VM 'Leri nasıl seçdikleri gösterilmektedir. En yüksek örnek kimliğine sahip sanal makinelerin ölçek kümesindeki en yeni VM 'Ler olduğu varsayılır ve en küçük örnek kimlikleri olan VM 'Lerin ölçek kümesindeki en eski VM olarak kabul edilir. 

### <a name="oldestvm-scale-in-policy"></a>OldestVM ölçeklendirme ilkesi

| Olay                 | /Ay içinde örnek kimlikleri  | Bölge 2 içinde örnek kimlikleri  | Bölge 3 içinde örnek kimlikleri  | Ölçek seçimi                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Başlangıç               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Ölçek-ın              | 3, 4, 5, 10            | ***2***, 6, 9, 11      | 1, 7, 8                | Bölge 3 en eski VM 'ye sahip olsa bile Bölge 1 ve 2 arasında seçim yapın. Bu bölgedeki en eski VM olduğundan, VM2 Bölge 2 silin.   |
| Ölçek-ın              | ***3***, 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | En eski VM Bölge 3 olmasına rağmen Bölge 1 seçin. Bu bölgedeki en eski VM olduğundan, VM3 Bölge 1 silin.                  |
| Ölçek-ın              | 4, 5, 10               | 6, 9, 11               | ***1***, 7, 8          | Bölgeler dengelenir. Ölçek kümesindeki en eski VM olduğundan Bölge 3 VM1 silin.                                               |
| Ölçek-ın              | ***4***, 5, 10         | 6, 9, 11               | 7, 8                   | Bölge 1 ve Bölge 2 arasında seçim yapın. İki bölge genelinde en eski VM olduğundan Bölge 1 VM4 silin.                              |
| Ölçek-ın              | 5, 10                  | ***6***, 9, 11         | 7, 8                   | En eski VM Bölge 1 olmasına rağmen Bölge 2 seçin. Bu bölgedeki en eski VM olduğundan Bölge 1 VM6 silin.                    |
| Ölçek-ın              | ***5***, 10            | 9, 11                  | 7, 8                   | Bölgeler dengelenir. Ölçek kümesindeki en eski VM olduğundan Bölge 1 VM5 silin.                                                |

Ölçeklendirilmemiş sanal makine ölçek kümeleri için, ilke, ölçek kümesi genelinde silinmek üzere en eski VM 'yi seçer. "Protected" sanal makinesi silinmek üzere atlanacak.

### <a name="newestvm-scale-in-policy"></a>NewestVM ölçek genişletme İlkesi

| Olay                 | /Ay içinde örnek kimlikleri  | Bölge 2 içinde örnek kimlikleri  | Bölge 3 içinde örnek kimlikleri  | Ölçek seçimi                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Başlangıç               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Ölçek-ın              | 3, 4, 5, 10            | 2, 6, 9, ***11***      | 1, 7, 8                | Bölge 1 ve 2 arasında seçim yapın. İki bölge genelinde en yeni VM olduğundan Bölge 2 VM11 silin.                                |
| Ölçek-ın              | 3, 4, 5, ***10***      | 2, 6, 9                | 1, 7, 8                | Diğer iki bölgede daha fazla VM 'ye sahip olduğundan Bölge 1 seçin. Bu bölgedeki en yeni VM olduğundan Bölge 1 VM10 silin.          |
| Ölçek-ın              | 3, 4, 5                | 2, 6, ***9***          | 1, 7, 8                | Bölgeler dengelenir. Ölçek kümesindeki en yeni VM olduğundan Bölge 2 VM9 silin.                                                |
| Ölçek-ın              | 3, 4, 5                | 2, 6                   | 1, 7, ***8***          | Bölge 1 ve Bölge 3 arasında seçim yapın. Bu bölgedeki en yeni VM olduğundan Bölge 3 VM8 silin.                                      |
| Ölçek-ın              | 3, 4, ***5***          | 2, 6                   | 1, 7                   | Bölge 3 en yeni VM 'ye sahip olmasına rağmen Bölge 1 seçin. Bu bölgedeki en yeni VM olduğundan Bölge 1 VM5 silin.                    |
| Ölçek-ın              | 3, 4                   | 2, 6                   | 1, ***7***             | Bölgeler dengelenir. Ölçek kümesindeki en yeni VM olduğundan Bölge 3 VM7 silin.                                                |

Ölçeklendirilmemiş sanal makine ölçek kümeleri için, ilke, ölçek kümesi genelinde silinmek üzere en yeni VM 'yi seçer. "Protected" sanal makinesi silinmek üzere atlanacak. 

## <a name="troubleshoot"></a>Sorun giderme

1. ' ' Properties ' türündeki nesne üzerinde "' Scaleınpolicy ' adlı üye bulunamadı" hata iletisiyle bir ' BadRequest ' hatası alırsanız, Scaleınpolicy etkinleştirilemiyor, sonra sanal makine ölçek kümesi için kullanılan API sürümünü denetleyin. Bu özellik için API sürüm 2019-03-01 veya üzeri gereklidir.

2. Ölçek için yanlış VM seçimi, yukarıdaki örneklere başvurur. Sanal makine ölçek kümesi bir çok dağıtım ise, ölçek genişletme İlkesi önce, önce imdengelenmiş bölgelere, sonra da bölge dengeli olduktan sonra ölçek kümesi içinde uygulanır. Ölçek sırası yukarıdaki örneklerle tutarlı değilse, sorun giderme için sanal makine ölçek kümesi ekibine bir sorgu yükseltin.

## <a name="next-steps"></a>Sonraki adımlar

Uygulamanızı sanal makine ölçek kümelerinde [dağıtmayı](virtual-machine-scale-sets-deploy-app.md) öğrenin.
