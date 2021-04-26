---
title: Azure CLı ile sanal makine ölçek kümelerini yönetme
description: Bir örneği başlatma ve durdurma veya ölçek kümesi kapasitesini değiştirme gibi sanal makine ölçek kümelerini yönetmeye yönelik genel Azure CLı komutları.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 9c2b2217fc6b32e5191bb67ffdaa10b796adf84b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762776"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Azure CLı ile sanal makine ölçek kümesini yönetme
Sanal makine ölçek kümesinin yaşam döngüsü boyunca bir veya daha fazla yönetim görevi çalıştırmanız gerekebilir. Ayrıca, çeşitli yaşam döngüsü görevlerini otomatikleştiren betikler oluşturmak isteyebilirsiniz. Bu makalede, bu görevleri gerçekleştirmenize olanak tanıyan yaygın Azure CLı komutlarının bazıları ayrıntılı olarak açıklanır.

Bu yönetim görevlerini gerçekleştirmek için en son Azure CLı 'ya ihtiyacınız vardır. Bilgi için bkz. [Azure CLI 'Yı yüklerken](/cli/azure/install-azure-cli). Bir sanal makine ölçek kümesi oluşturmanız gerekiyorsa, [Azure CLI ile bir ölçek kümesi oluşturabilirsiniz](quick-create-cli.md).


## <a name="view-information-about-a-scale-set"></a>Ölçek kümesi hakkındaki bilgileri görüntüleme
Ölçek kümesiyle ilgili genel bilgileri görüntülemek için [az VMSS Show](/cli/azure/vmss)komutunu kullanın. Aşağıdaki örnek, *Myresourcegroup* kaynak grubundaki *myScaleSet* adlı ölçek kümesi hakkındaki bilgileri alır. Kendi adlarınızı aşağıdaki gibi girin:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Ölçek kümesindeki VM’leri görüntüleme
Bir ölçek kümesindeki sanal makine örneğinin listesini görüntülemek için [az VMSS List-Instances](/cli/azure/vmss)komutunu kullanın. Aşağıdaki örnekte, *Myresourcegroup* kaynak grubundaki *myScaleSet* adlı ölçek kümesindeki tüm sanal makine örnekleri listelenmektedir. Bu adlar için kendi değerlerinizi sağlayın:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Belirli bir sanal makine örneği hakkında ek bilgileri görüntülemek için `--instance-id` [az VMSS Get-Instance-View](/cli/azure/vmss) öğesine parametreyi ekleyin ve görüntülenecek örneği belirtin. Aşağıdaki örnek *myScaleSet* ve *myresourcegroup* kaynak grubu ADLı *Ölçek kümesinde bulunan VM örneğiyle ilgili* bilgileri görüntüler. Kendi adlarınızı aşağıdaki gibi girin:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```

Ayrıca, bir API çağrısındaki tüm örnekler için ayrıntılı *InstanceView* bilgilerini alabilir ve bu da büyük yüklemeler için API azaltmasını önlemenize yardımcı olabilir. , Ve için kendi değerlerinizi `--resource-group` sağlayın `--subscription` `--name` .

```azurecli
az vmss list-instances \
    --expand instanceView \
    --select instanceView \
    --resource-group <resourceGroupName> \
    --subscription <subID> \
    --name <vmssName>
```

```rest
GET "https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSSName>/virtualMachines?api-version=2019-03-01&%24expand=instanceView"
```

## <a name="list-connection-information-for-vms"></a>VM 'Ler için bağlantı bilgilerini listeleyin
Ölçek kümesindeki VM 'lere bağlanmak için SSH veya RDP 'yi atanan bir genel IP adresi ve bağlantı noktası numarası ile kullanabilirsiniz. Varsayılan olarak, ağ adresi çevirisi (NAT) kuralları, uzak bağlantı trafiğini her bir VM 'ye ileten Azure yük dengeleyicisine eklenir. Ölçek kümesindeki sanal makine örneklerine bağlanacak adresi ve bağlantı noktalarını listelemek için [az VMSS List-instance-Connection-Info](/cli/azure/vmss)komutunu kullanın. Aşağıdaki örnek, *myScaleSet* adlı ölçek kümesindeki sanal makine örneklerine yönelik bağlantı bilgilerini ve *myresourcegroup* kaynak grubunu listeler. Bu adlar için kendi değerlerinizi sağlayın:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Ölçek kümesinin kapasitesini değiştirme
Yukarıdaki komutlarda, ölçek kümesi ve VM örnekleri hakkında bilgiler gösterildi. Ölçek kümesindeki örneklerin sayısını artırmak veya azaltmak için kapasiteyi değiştirebilirsiniz. Ölçek kümesi gereken sanal makine sayısını oluşturur veya kaldırır, ardından VM 'Leri uygulama trafiğini alacak şekilde yapılandırır.

Ölçek kümesinde şu anda yer alan örneklerin sayısını görmek için [az vmss show](/cli/azure/vmss) komutunu kullanarak *sku.capacity* üzerinde bir sorgu çalıştırın:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Ardından [az vmss scale](/cli/azure/vmss) ile ölçek kümesindeki sanal makinelerin sayısını elle artırabilir veya azaltabilirsiniz. Aşağıdaki örnek, ölçek kümesindeki sanal makinelerin sayısını *5* olarak ayarlar:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

Ölçek kümenizin kapasitesinin güncelleştirilmesi birkaç dakika sürer. Ölçek kümesinin kapasitesini azaltırsanız, en yüksek örnek kimliğine sahip VM 'Ler ilk önce kaldırılır.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Ölçek kümesindeki VM’leri durdurma ve başlatma
Ölçek kümesindeki bir veya daha fazla sanal makineyi durdurmak için [az VMSS stop](/cli/azure/vmss#az_vmss_stop)komutunu kullanın. `--instance-ids` parametresi, durdurulacak bir veya daha fazla sanal makine belirtmenize olanak sağlar. Örnek kimliği belirtmezseniz, ölçek kümesindeki tüm sanal makineler durdurulur. Birden çok VM 'yi durdurmak için, her örnek KIMLIĞINI bir boşluk ile ayırın.

Aşağıdaki örnek, *myScaleSet* ve *myresourcegroup* kaynak grubu adlı ölçek kümesinde *0* örneğini durduruyor. Kendi değerlerinizi aşağıdaki gibi sağlayın:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

Durdurulan VM 'Ler ayrılmış olarak kalır ve işlem ücretleri uygulanmaya devam eder. VM 'Lerin serbest bırakılmış olmasını ve yalnızca depolama ücretleri uygulanmasını istiyorsanız [az VMSS ayırmayı](/cli/azure/vmss)kullanın. Birden çok VM 'yi serbest bırakmak için her örnek KIMLIĞINI bir boşluk ile ayırın. Aşağıdaki örnek, *myScaleSet* ve *myresourcegroup* kaynak grubu adlı ölçek kümesinde *0* örneğini durdurup kaldırır. Kendi değerlerinizi aşağıdaki gibi sağlayın:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Ölçek kümesindeki VM 'Leri başlatma
Ölçek kümesinde bir veya daha fazla VM başlatmak için [az VMSS start](/cli/azure/vmss)komutunu kullanın. `--instance-ids` parametresi, başlatılacak bir veya daha fazla sanal makine belirtmenize olanak sağlar. Örnek kimliği belirtmezseniz, ölçek kümesindeki tüm sanal makineler başlatılır. Birden çok VM başlatmak için, her örnek KIMLIĞINI bir boşluk ile ayırın.

Aşağıdaki örnek, *myScaleSet* ve *myresourcegroup* kaynak grubu adlı ölçek kümesinde *0* örneğini başlatır. Kendi değerlerinizi aşağıdaki gibi sağlayın:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Ölçek kümesindeki VM’leri yeniden başlatma
Bir ölçek kümesindeki bir veya daha fazla sanal makineyi yeniden başlatmak için [az VMSS restart](/cli/azure/vmss)komutunu kullanın. `--instance-ids` parametresi, yeniden başlatılacak bir veya daha fazla sanal makine belirtmenize olanak sağlar. Örnek kimliği belirtmezseniz, ölçek kümesindeki tüm sanal makineler yeniden başlatılır. Birden çok VM 'yi yeniden başlatmak için, her örnek KIMLIĞINI bir boşluk ile ayırın.

Aşağıdaki örnek, *myScaleSet* ve *myresourcegroup* kaynak grubu adlı ölçek kümesinde *0* örneğini yeniden başlatır. Kendi değerlerinizi aşağıdaki gibi sağlayın:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Ölçek kümesinden VM 'Leri kaldırma
Ölçek kümesindeki bir veya daha fazla VM 'yi kaldırmak için [az VMSS Delete-Instances](/cli/azure/vmss)komutunu kullanın. `--instance-ids`Parametresi, kaldırılacak bir veya daha fazla sanal makine belirtmenize olanak sağlar. Örnek KIMLIĞI için * belirtirseniz, ölçek kümesindeki tüm sanal makineler kaldırılır. Birden çok VM 'yi kaldırmak için, her örnek KIMLIĞINI bir boşluk ile ayırın.

Aşağıdaki örnek, *myScaleSet* ve *myresourcegroup* kaynak grubu adlı ölçek kümesinde *0* örneğini kaldırır. Kendi değerlerinizi aşağıdaki gibi sağlayın:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Sonraki adımlar
Ölçek Kümeleri için diğer yaygın görevler, [bir uygulamanın nasıl dağıtılacağını](virtual-machine-scale-sets-deploy-app.md)ve [sanal makine örneklerinin nasıl yükseltileceğini](virtual-machine-scale-sets-upgrade-scale-set.md)içerir. [Otomatik ölçek kurallarını yapılandırmak](virtual-machine-scale-sets-autoscale-overview.md)IÇIN Azure CLI de kullanabilirsiniz.
