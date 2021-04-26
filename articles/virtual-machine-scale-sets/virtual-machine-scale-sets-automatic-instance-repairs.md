---
title: Azure sanal makine ölçek kümeleri ile otomatik örnek onarımları
description: Ölçek kümesindeki sanal makine örnekleri için otomatik onarımlar ilkesini yapılandırma hakkında bilgi edinin
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: instance-protection
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 733f4602e43511924783f6bc8cb1bad29edb5ea0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762920"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Azure sanal makine ölçek kümeleri için otomatik örnek onarımları

Azure sanal makine ölçek kümeleri için otomatik örnek onarımlarını etkinleştirmek, sağlıklı bir örnek kümesini korurken uygulamalar için yüksek kullanılabilirlik elde etmenize yardımcı olur. Ölçek kümesindeki bir örnek, [uygulama sistem durumu uzantısı](./virtual-machine-scale-sets-health-extension.md) ya da [Yük Dengeleyici Sistem durumu araştırmaları](../load-balancer/load-balancer-custom-probe-overview.md)tarafından bildirildiği şekilde sağlıksız olarak bulunursa, bu özellik sağlıksız örneği silerek örnek onarımı gerçekleştirir ve bunu değiştirmek için yeni bir tane oluşturur.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Otomatik örnek onarımları kullanma gereksinimleri

**Ölçek kümesi için uygulama sistem durumu izlemeyi etkinleştir**

Ölçek kümesi, etkin örnekler için uygulama sistem durumu izleme içermelidir. Bu, [uygulama durumu uzantısı](./virtual-machine-scale-sets-health-extension.md) ya da [yük dengeleyici durum araştırmaları](../load-balancer/load-balancer-custom-probe-overview.md)kullanılarak yapılabilir. Tek seferde bunlardan yalnızca biri etkinleştirilebilir. Uygulama sistem durumu uzantısı veya yük dengeleyici, uygulama sistem durumunu tespit etmek için sanal makine örneklerinde yapılandırılan uygulama uç noktasının ping olarak araştırmasını sağlar. Bu sistem durumu, ölçek kümesi Orchestrator tarafından örnek durumunu izlemek ve gerektiğinde onarım gerçekleştirmek için kullanılır.

**Sistem durumunu sağlamak için uç noktayı yapılandırma**

Otomatik örnek onarım ilkesini etkinleştirmeden önce, ölçek kümesi örneklerinin uygulama sistem durumunu göstermek için yapılandırılmış uygulama uç noktasına sahip olduğundan emin olun. Bir örnek bu uygulama uç noktasında 200 (Tamam) durumunu döndürdüğünde, örnek "sağlıklı" olarak işaretlenir. Diğer tüm durumlarda, aşağıdaki senaryolar da dahil olmak üzere örnek "sağlıksız" olarak işaretlenir:

- Uygulama sistem durumu sağlamak için sanal makine örnekleri içinde yapılandırılmış bir uygulama uç noktası yok
- Uygulama uç noktası yanlış yapılandırıldığında
- Uygulama uç noktasına erişilemiyorsa

"Sağlıksız" olarak işaretlenmiş örnekler için otomatik onarımlar ölçek kümesi tarafından tetiklenir. Uç nokta yapılandırılırken, istenmeyen örnek onarımlarını önlemek için otomatik onarımlar ilkesini etkinleştirmeden önce uygulama uç noktasının doğru yapılandırıldığından emin olun.

**Ölçek kümesindeki en fazla örnek sayısı**

Bu özellik şu anda yalnızca en fazla 200 örneğe sahip olan ölçek kümeleri için kullanılabilir. Ölçek kümesi tek bir yerleştirme grubu ya da bir çok yerleştirme grubu olarak dağıtılabilir, ancak ölçek kümesi için otomatik örnek onarımı etkinse, örnek sayısı 200 üzerinde olamaz.

**API sürümü**

İşlem API 'SI sürüm 2018-10-01 veya üzeri için otomatik onarım ilkesi desteklenir.

**Kaynak veya abonelik taşınmasındaki kısıtlamalar**

Otomatik onarımlar özelliği etkinken, kaynak veya abonelik taşımaları ölçek kümelerinde Şu anda desteklenmiyor.

**Service Fabric ölçek kümeleri için kısıtlama**

Bu özellik şu anda Service Fabric ölçek kümeleri için desteklenmiyor.

## <a name="how-do-automatic-instance-repairs-work"></a>Otomatik örnek onarımları nasıl çalışır?

Otomatik örnek onarma özelliği, bir ölçek kümesindeki tek tek örneklerin sistem durumunu izlemeye dayanır. Ölçek kümesindeki sanal makine örnekleri, uygulama durumu [Uzantısı](./virtual-machine-scale-sets-health-extension.md) veya [Yük Dengeleyici Sistem durumu araştırmaları](../load-balancer/load-balancer-custom-probe-overview.md)kullanılarak uygulama sistem durumunu göstermek üzere yapılandırılabilir. Bir örneğin sağlıksız olduğu bulunursa, ölçek kümesi sorunlu örneği silerek ve yeni bir tane oluşturarak onarım eylemi gerçekleştirir. Yeni örneği oluşturmak için en son sanal makine ölçek kümesi modeli kullanılır. Bu özellik, *Automaticrepairspolicy* nesnesi kullanılarak sanal makine ölçek kümesi modelinde etkinleştirilebilir.

### <a name="batching"></a>Toplu İşleme

Otomatik örnek onarma işlemleri toplu olarak gerçekleştirilir. Belirli bir zamanda, ölçek kümesindeki örneklerin %5 ' inden fazlası otomatik onarımlar ilkesi aracılığıyla onarılamamakta. Bu, aynı anda sağlıksız bir şekilde bulunursa çok sayıda örnek için eşzamanlı silme ve yeniden oluşturma yapmaktan kaçınmaya yardımcı olur.

### <a name="grace-period"></a>Yetkisiz kullanım süresi

Bir örnek, ölçek kümesinde gerçekleştirilen bir PUT, PATCH veya POST eylemi nedeniyle bir durum değişikliği işleminden geçtiğinde (örneğin, yeniden görüntü, yeniden dağıtma, güncelleştirme, vb.), söz konusu örnekteki herhangi bir onarım eylemi yalnızca yetkisiz kullanım süresi dolduktan sonra gerçekleştirilir. Yetkisiz kullanım süresi, örneğin sağlıklı duruma dönmesi için izin verilen sürenin miktarıdır. Yetkisiz kullanım süresi, durum değişikliği tamamlandıktan sonra başlar. Bu, zamanından önce veya yanlışlıkla onarım işlemlerinden kaçınmaya yardımcı olur. Kullanım süresi, ölçek kümesindeki yeni oluşturulan her örnek için kabul edilir (onarım işleminin sonucu olarak oluşturulan diğeri dahil). Kullanım süresi, ISO 8601 biçimindeki dakikalar içinde belirtilir ve *Automaticrepairspolicy. gracePeriod* özelliği kullanılarak ayarlanabilir. Yetkisiz kullanım süresi 30 dakika ile 90 dakika arasında değişebilir ve varsayılan değer olan 30 dakika olabilir.

### <a name="suspension-of-repairs"></a>Onarımlar askıya alma 

Sanal Makine Ölçek Kümeleri gerekirse otomatik örnek onarımları geçici olarak askıya alma özelliğini sağlar. Sanal makine ölçek kümesinin örnek görünümündeki otomatik onarımda bulunan *Hizmetler* Için *ServiceState* , otomatik onarımın geçerli durumunu gösterir. Ölçek kümesi otomatik onarımları kabul ettiğinde, *ServiceState* parametresinin değeri *çalışıyor* olarak ayarlanır. Ölçek kümesi için otomatik onarımlar askıya alındığında, *ServiceState* parametresi *askıya alındı* olarak ayarlanır. Bir ölçek kümesi üzerinde *Automaticrepairspolicy* tanımlanmazsa, ancak otomatik onarımlar özelliği etkinleştirilmemişse, *ServiceState* parametresi *çalışmıyor* olarak ayarlanır.

Bir ölçek kümesindeki sağlıksız olanları değiştirmek için yeni oluşturulan örnekler, sürekli olarak onarım işlemleri gerçekleştirildikten sonra bile düzgün şekilde kalmaya devam ederse, bir güvenlik önlemi olarak platform otomatik onarımlar için *ServiceState* 'ı *askıya alındı* olarak güncelleştirir. Otomatik onarımları bir kez daha yeniden *deneyebilirsiniz. bu*, otomatik onarımlar Için *ServiceState* değerini ayarlar. Ölçek kümesi için [Otomatik onarımlar ilkesinin hizmet durumunu görüntüleme ve güncelleştirme](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) bölümünde ayrıntılı yönergeler verilmiştir. 

Otomatik örnek onarımları işlemi aşağıdaki gibi işler:

1. [Uygulama durumu uzantısı](./virtual-machine-scale-sets-health-extension.md) veya [Yük Dengeleyici Sistem durumu araştırmaları](../load-balancer/load-balancer-custom-probe-overview.md) , her bir örnek için uygulama sistem durumunu almak üzere ölçek kümesindeki her bir sanal makinenin içindeki uygulama uç noktasına ping yapar.
2. Uç nokta 200 (Tamam) durumu ile yanıt verirse, örnek "sağlıklı" olarak işaretlenir. Diğer tüm durumlarda (uç nokta ulaşılamaz durumdaysa dahil), örnek "sağlıksız" olarak işaretlenir.
3. Bir örneğin sağlıksız olduğu bulunursa, ölçek kümesi sağlıksız örneği silerek bir onarım eylemi tetikler ve bunu değiştirmek için yeni bir tane oluşturur.
4. Örnek onarımlar toplu olarak gerçekleştirilir. Belirli bir zamanda, ölçek kümesindeki toplam örneklerin %5 ' inden fazlası onarılamamakta. Ölçek kümesinde 20 ' den az örnek varsa, onarımlar tek seferde sağlıksız bir örnek için yapılır.
5. Yukarıdaki işlem, ölçek kümesindeki sağlıksız bir örnek onarılana kadar devam eder.

## <a name="instance-protection-and-automatic-repairs"></a>Örnek koruması ve otomatik onarımlar

Bir ölçek kümesindeki bir örnek [koruma ilkelerinden](./virtual-machine-scale-sets-instance-protection.md)biri uygulanarak korunuyorsa, bu örnekte otomatik onarımlar yapılmaz. Bu, hem koruma ilkeleri için geçerlidir: *ölçeğe karşı* koruma ve *ölçek ayarlama* eylemleriyle koruma. 

## <a name="terminatenotificationandautomaticrepairs"></a>Bildirimi ve otomatik onarımları Sonlandır

Bir ölçek kümesinde [sonlandırma bildirimi](./virtual-machine-scale-sets-terminate-notification.md) özelliği etkinse, otomatik onarma işlemi sırasında, sağlıksız bir örneğin silinmesi, sonlandırma bildirimi yapılandırmasını izler. Azure meta veri hizmeti zamanlanmış olayları aracılığıyla bir sonlandırma bildirimi gönderilir ve yapılandırılan gecikme zaman aşımı süresi boyunca örnek silme geciktirilir. Ancak, sağlıksız bir işlemin yerine geçecek yeni bir örneğin oluşturulması, gecikme zaman aşımının tamamlanmasını beklemez.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Yeni bir ölçek kümesi oluştururken otomatik onarım ilkesini etkinleştirme

Yeni bir ölçek kümesi oluştururken otomatik onarım ilkesini etkinleştirmek için, bu özelliğe yönelik tüm [gereksinimlerin](#requirements-for-using-automatic-instance-repairs) karşılandığından emin olun. Uç nokta yapılandırılırken, istenmeyen onarımlar tetiklenmemek için uygulama uç noktasının ölçek kümesi örnekleri için doğru şekilde yapılandırılması gerekir. Yeni oluşturulan ölçek kümeleri için tüm örnek onarımları yalnızca yetkisiz kullanım süresi dolduktan sonra gerçekleştirilir. Ölçek kümesinde otomatik örnek onarımını etkinleştirmek için, sanal makine ölçek kümesi modelinde *Automaticrepairspolicy* nesnesini kullanın.

Bu [hızlı başlangıç şablonunu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) , yük dengeleyici durum araştırmasına sahip bir sanal makine ölçek kümesi dağıtmak ve 30 dakikalık yetkisiz kullanım süresiyle etkin otomatik örnek onarımları sağlamak için de kullanabilirsiniz.

### <a name="azure-portal"></a>Azure portalı
 
Aşağıdaki adımlar, yeni bir ölçek kümesi oluştururken otomatik onarım ilkesini etkinleştirir.
 
1. **Sanal makine ölçek kümelerine** gidin.
1. Yeni bir ölçek kümesi oluşturmak için **+ Ekle** ' yi seçin.
1. **Sistem durumu** sekmesine gidin. 
1. **Sistem durumu** bölümünü bulun.
1. **Uygulama durumunu izle** seçeneğini etkinleştirin.
1. **Otomatik onarma ilkesi** bölümünü bulun.
1. **Otomatik onarımlar** **seçeneğini açın.**
1. **Yetkisiz kullanım süresi (dk)** alanında yetkisiz kullanım süresini dakika cinsinden belirtin, izin verilen değerler 30 ila 90 dakika arasındadır. 
1. Yeni ölçek kümesini oluşturmayı tamamladığınızda, **gözden geçir + oluştur** düğmesini seçin.

### <a name="rest-api"></a>REST API

Aşağıdaki örnekte, bir ölçek kümesi modelinde otomatik örnek onarımın nasıl etkinleştirileceği gösterilmektedir. API sürüm 2018-10-01 veya üstünü kullanın.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

[Yeni-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) cmdlet 'i kullanılarak yeni bir ölçek kümesi oluşturulurken otomatik örnek onarma özelliği etkinleştirilebilir. Bu örnek betik, yapılandırma dosyasını kullanarak bir ölçek kümesinin ve ilişkili kaynakların oluşturulmasını adım adım göstermektedir: [tamamen sanal makine ölçek kümesi oluşturma](./scripts/powershell-sample-create-complete-scale-set.md). Ölçek kümesini oluşturmak için Configuration nesnesine *Enableautomaticrepair* ve *AutomaticRepairGracePeriod* parametrelerini ekleyerek otomatik örnek onarımları ilkesini yapılandırabilirsiniz. Aşağıdaki örnek, özelliği 30 dakikalık yetkisiz kullanım süresine sahip olacak şekilde sunar.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Aşağıdaki örnek, *[az VMSS Create](/cli/azure/vmss#az_vmss_create)* komutunu kullanarak yeni bir ölçek kümesi oluştururken otomatik onarımlar ilkesini mümkün bir şekilde sunar. İlk olarak bir kaynak grubu oluşturun ve ardından otomatik onarımlar ilke kullanım süresi 30 dakika olarak ayarlanan yeni bir ölçek kümesi oluşturun.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-grace-period 30
```

Yukarıdaki örnek, örneklerin uygulama sistem durumunu izlemek için mevcut yük dengeleyiciyi ve sistem durumu araştırmasını kullanır. Bunun yerine izleme için bir uygulama sistem durumu uzantısı kullanmayı tercih ederseniz, bir ölçek kümesi oluşturabilir, uygulama durumu uzantısını yapılandırabilir ve ardından, sonraki bölümde açıklandığı gibi *az VMSS Update* kullanarak otomatik örnek onarım ilkesini etkinleştirebilirsiniz.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Mevcut bir ölçek kümesini güncelleştirirken otomatik onarım ilkesini etkinleştirme

Mevcut bir ölçek kümesinde otomatik onarım ilkesini etkinleştirmeden önce, bu özelliğe yönelik tüm [gereksinimlerin](#requirements-for-using-automatic-instance-repairs) karşılandığından emin olun. Uç nokta yapılandırılırken, istenmeyen onarımlar tetiklenmemek için uygulama uç noktasının ölçek kümesi örnekleri için doğru şekilde yapılandırılması gerekir. Ölçek kümesinde otomatik örnek onarımını etkinleştirmek için, sanal makine ölçek kümesi modelinde *Automaticrepairspolicy* nesnesini kullanın.

Varolan bir ölçek kümesinin modelini güncelleştirdikten sonra, en son modelin ölçeğin tüm örneklerine uygulandığından emin olun. [En son ölçek kümesi modeliyle VM 'leri güncel hale getirme](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)yönergelerine bakın.

### <a name="azure-portal"></a>Azure portalı

Mevcut bir ölçek kümesinin otomatik onarımlar ilkesini Azure portal aracılığıyla değiştirebilirsiniz. 
 
1. Var olan bir sanal makine ölçek kümesine gidin.
1. Soldaki menüdeki **Ayarlar** ' ın altında **sistem durumu ve Onar**' ı seçin.
1. **Uygulama durumunu izle** seçeneğini etkinleştirin.
1. **Otomatik onarma ilkesi** bölümünü bulun.
1. **Otomatik onarımlar** **seçeneğini açın.**
1. **Yetkisiz kullanım süresi (dk)** alanında yetkisiz kullanım süresini dakika cinsinden belirtin, izin verilen değerler 30 ila 90 dakika arasındadır. 
1. İşiniz bittiğinde **Kaydet**‘i seçin. 

### <a name="rest-api"></a>REST API

Aşağıdaki örnek, ilke için 40 dakikalık yetkisiz kullanım süresi sunar. API sürüm 2018-10-01 veya üstünü kullanın.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Var olan bir ölçek kümesindeki otomatik örnek onarma özelliğinin yapılandırmasını değiştirmek için [Update-AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet 'ini kullanın. Aşağıdaki örnek, yetkisiz kullanım süresini 40 dakikaya güncelleştirir.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Aşağıda, var olan bir ölçek kümesinin, *[az VMSS Update](/cli/azure/vmss#az_vmss_update)* kullanılarak otomatik örnek onarımları ilkesini güncelleştirmeye yönelik bir örnek verilmiştir.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>Otomatik örnek onarımı ilkesinin hizmet durumunu görüntüleme ve güncelleştirme

### <a name="rest-api"></a>REST API 

Sanal makine ölçek kümesi, özellik düzenleme *Hizmetleri* altındaki otomatik onarımların *hizmetlerini görüntülemek için, API* sürüm 2019-12-01 veya üzeri ile [örnek görünümü al](/rest/api/compute/virtualmachinescalesets/getinstanceview) ' i kullanın. 

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

Otomatik onarımın durumunu ayarlamak için, bir sanal makine ölçek kümesinde *setOrchestrationServiceState* API 'sini apı sürüm 2019-12-01 veya üzeri ile kullanın. Ölçek kümesi otomatik onarımlar özelliğini kabul ettiğinde, ölçek kümesi için otomatik onarımları askıya almak veya yeniden başlatmak üzere bu API 'yi kullanabilirsiniz. 

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>Azure CLI 

Otomatik örnek onarımlarını için *ServiceState* 'i görüntülemek için [Get-Instance-View](/cli/azure/vmss#az_vmss_get_instance_view) cmdlet 'ini kullanın. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

Otomatik örnek onarımları için *ServiceState* 'i güncelleştirmek için [set-Orchestration-Service-State](/cli/azure/vmss#az_vmss_set_orchestration_service_state) cmdlet 'ini kullanın. Ölçek kümesi otomatik onarma özelliğini kabul ettiğinde, ölçek kümesi için otomatik onarımları askıya almak veya yeniden başlatmak üzere bu cmdlet 'i kullanabilirsiniz. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Otomatik örnek onarımlarını için *ServiceState* 'i görüntülemek Için, *InstanceView* parametresiyle [Get-azvmss](/powershell/module/az.compute/get-azvmss) cmdlet 'ini kullanın.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Otomatik örnek onarımları için *ServiceState* 'i güncelleştirmek üzere Set-AzVmssOrchestrationServiceState cmdlet 'ini kullanın. Ölçek kümesi otomatik onarma özelliğini kabul ettiğinde, ölçek kümesi için otomatik onarımları askıya almak veya yeniden başlatmak üzere bu cmdlet 'i kullanabilirsiniz.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Sorun giderme

**Otomatik onarım ilkesini etkinleştirme hatası**

"' Properties ' türündeki nesne üzerinde" automaticRepairsPolicy ' üyesi bulunamadı "iletisiyle bir ' Rozrequest ' hatası alırsanız, sonra sanal makine ölçek kümesi için kullanılan API sürümünü denetleyin. Bu özellik için API sürüm 2018-10-01 veya üzeri gereklidir.

**İlke etkin olsa bile örnek onarılmadı**

Örnek, yetkisiz kullanım süresi içinde olabilir. Bu, onarım gerçekleştirilmeden önce örnekteki durum değişikliğinden sonra beklenecek sürenin miktarıdır. Bu, zamanından önce veya yanlışlıkla onarımların önüne geçmek. Örnek için yetkisiz kullanım süresi tamamlandıktan sonra onarım eylemi gerçekleşmelidir.

**Ölçek kümesi örnekleri için uygulama sistem durumu görüntüleme**

Uygulamanın sistem durumunu görüntülemek için bir sanal makine ölçek kümesindeki örnekler için [örnek görünümü al API](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) 'sini kullanabilirsiniz. Azure PowerShell ile [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) cmdlet 'ini *-InstanceView* bayrağıyla birlikte kullanabilirsiniz. Uygulama sistem durumu, *Vmhealth* özelliği altında verilmiştir.

Azure portal sistem durumunu da görebilirsiniz. Mevcut bir ölçek kümesine gidin, sol taraftaki menüden **örnekler** ' i seçin ve her bir ölçek kümesi örneğinin sistem durumu için **sistem** durumu sütununa bakın. 

## <a name="next-steps"></a>Sonraki adımlar

Ölçek kümeleriniz için [uygulama durumu uzantısının](./virtual-machine-scale-sets-health-extension.md) veya [Yük Dengeleyici Sistem durumu araştırmalarının](../load-balancer/load-balancer-custom-probe-overview.md) nasıl yapılandırılacağını öğrenin.
