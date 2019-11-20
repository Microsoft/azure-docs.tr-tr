---
title: Azure sanal makine ölçek kümeleri ile özel ölçeklendirme ilkeleri kullanma | Microsoft Docs
description: Örnek sayısını yönetmek için otomatik ölçeklendirme yapılandırması kullanan Azure sanal makine ölçek kümeleri ile özel ölçeklendirme ilkelerini nasıl kullanacağınızı öğrenin
services: virtual-machine-scale-sets
author: avverma
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: article
ms.date: 10/11/2019
ms.author: avverma
ms.openlocfilehash: c1618c398c0f7c4f0f54647e5232fdacc17de186
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72453166"
---
# <a name="preview-use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Önizleme: Azure sanal makine ölçek kümeleri ile özel ölçeklendirme ilkeleri kullanma

Bir sanal makine ölçek kümesi dağıtımı, platform ve Kullanıcı tanımlı özel ölçümler de dahil olmak üzere bir ölçüm dizisine göre ölçeklendirilebilir veya ölçeklendirilebilir. Ölçeği genişletme, ölçek kümesi modeline göre yeni sanal makineler oluşturduğunda, ölçek kümesi iş yükü geliştikçe farklı yapılandırmalara ve/veya işlevlere sahip olabilecek çalışan sanal makineleri etkiler. 

Ölçek genişletme İlkesi özelliği kullanıcılara sanal makinelerin ölçeklendiği sırayı yapılandırmak için bir yol sağlar. Önizleme üç ölçek genişletme yapılandırması sunar: 

1. Varsayılan
2. NewestVM
3. OldestVM

***Bu önizleme özelliği, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez.***

### <a name="default-scale-in-policy"></a>Varsayılan ölçeklendirme ilkesi

Varsayılan olarak, sanal makine ölçek kümesi bu ilkeyi hangi örneklerin ölçeklendirileceğini belirleyecek şekilde uygular. *Varsayılan* Ilkeyle, sanal makineler aşağıdaki sırada ölçek için seçilir:

1. Kullanılabilirlik alanları genelinde sanal makineleri Dengeleme (ölçek kümesi bölgesel yapılandırmasında dağıtılmışsa)
2. Hata etki alanları genelinde sanal makinelerin dengelenmesi (en iyi çaba)
3. En yüksek örnek KIMLIĞIYLE sanal makineyi Sil

Kullanıcıların yalnızca varsayılan sıralamayı takip etmek istiyorsanız, ölçek bir ilke belirtmeleri gerekmez.

Kullanılabilirlik alanları veya hata etki alanları genelinde dengelemenin örnekleri kullanılabilirlik alanları veya hata etki alanları arasında taşımadığını unutmayın. Dengelemeye, sanal makinelerin dağıtılması dengeli hale gelinceye kadar sanal makinelerin, dengesiz kullanılabilirlik bölgelerinden veya hata etki alanlarından silinmesi aracılığıyla yapılır.

### <a name="newestvm-scale-in-policy"></a>NewestVM ölçek genişletme İlkesi

Bu ilke, kullanılabilirlik alanları genelinde VM 'leri dengeledikten sonra ölçek kümesindeki en yeni oluşturulan sanal makineyi siler (bölgesel dağıtımları için). Bu ilkeyi etkinleştirmek, sanal makine ölçek kümesi modelinde bir yapılandırma değişikliği gerektirir.

### <a name="oldestvm-scale-in-policy"></a>OldestVM ölçeklendirme ilkesi

Bu ilke, kullanılabilirlik alanları genelinde VM 'leri dengeledikten sonra ölçek kümesindeki en eski oluşturulan sanal makineyi siler (bölgesel dağıtımları için). Bu ilkeyi etkinleştirmek, sanal makine ölçek kümesi modelinde bir yapılandırma değişikliği gerektirir.

## <a name="enabling-scale-in-policy"></a>Ölçek Genişletme ilkesini etkinleştirme

Ölçek genişletme İlkesi, sanal makine ölçek kümesi modelinde tanımlanmıştır. Yukarıdaki bölümlerde belirtildiği gibi, ' NewestVM ' ve ' OldestVM ' ilkeleri kullanılırken bir ölçek genişletme ilke tanımı gerekir. Ölçek kümesi modelinde ölçek genişletme ilke tanımı yoksa, sanal makine ölçek kümesi otomatik olarak ' varsayılan ' ölçekleme ilkesini kullanır. 

Bir ölçek genişletme İlkesi, sanal makine ölçek kümesi modelinde aşağıdaki yollarla tanımlanabilir:

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
| Başlatma               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
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
| Başlatma               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Ölçek-ın              | 3, 4, 5, 10            | 2, 6, 9, ***11***      | 1, 7, 8                | Bölge 1 ve 2 arasında seçim yapın. İki bölge genelinde en yeni VM olduğundan Bölge 2 VM11 silin.                                |
| Ölçek-ın              | 3, 4, 5, ***10***      | 2, 6, 9                | 1, 7, 8                | Diğer iki bölgede daha fazla VM 'ye sahip olduğundan Bölge 1 seçin. Bu bölgedeki en yeni VM olduğundan Bölge 1 VM10 silin.          |
| Ölçek-ın              | 3, 4, 5                | 2, 6, ***9***          | 1, 7, 8                | Bölgeler dengelenir. Ölçek kümesindeki en yeni VM olduğundan Bölge 2 VM9 silin.                                                |
| Ölçek-ın              | 3, 4, 5                | 2, 6                   | 1, 7, ***8***          | Bölge 1 ve Bölge 3 arasında seçim yapın. Bu bölgedeki en yeni VM olduğundan Bölge 3 VM8 silin.                                      |
| Ölçek-ın              | 3, 4, ***5***          | 2, 6                   | 1, 7                   | Bölge 3 en yeni VM 'ye sahip olmasına rağmen Bölge 1 seçin. Bu bölgedeki en yeni VM olduğundan Bölge 1 VM5 silin.                    |
| Ölçek-ın              | 3, 4                   | 2, 6                   | 1, ***7***             | Bölgeler dengelenir. Ölçek kümesindeki en yeni VM olduğundan Bölge 3 VM7 silin.                                                |

Ölçeklendirilmemiş sanal makine ölçek kümeleri için, ilke, ölçek kümesi genelinde silinmek üzere en yeni VM 'yi seçer. "Protected" sanal makinesi silinmek üzere atlanacak. 

## <a name="troubleshoot"></a>Sorun giderme

1. ' ' Properties ' türündeki nesne üzerinde "' Scaleınpolicy ' adlı üye bulunamadı" hata iletisiyle bir ' BadRequest ' hatası alırsanız, Scaleınpolicy etkinleştirilemiyor, sonra sanal makine ölçek kümesi için kullanılan API sürümünü denetleyin. Bu önizleme için API sürüm 2019-03-01 veya üzeri gereklidir.

2. Ölçek için yanlış VM seçimi, yukarıdaki örneklere başvurur. Sanal makine ölçek kümesi bir çok dağıtım ise, ölçek genişletme İlkesi önce, önce imdengelenmiş bölgelere, sonra da bölge dengeli olduktan sonra ölçek kümesi içinde uygulanır. Ölçek sırası yukarıdaki örneklerle tutarlı değilse, sorun giderme için sanal makine ölçek kümesi ekibine bir sorgu yükseltin.

## <a name="next-steps"></a>Sonraki adımlar

Uygulamanızı sanal makine ölçek kümelerinde [dağıtmayı](virtual-machine-scale-sets-deploy-app.md) öğrenin.