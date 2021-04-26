---
title: CLı kullanarak Azure spot sanal makineleri dağıtma
description: Azure spot sanal makinelerini kullanarak maliyetleri tasarruf etmek için CLı 'yı nasıl kullanacağınızı öğrenin.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 8e8bdaa7a812d8c7accfea59b58b75a58d50e21e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789618"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Azure CLı kullanarak Azure spot sanal makineleri dağıtma

[Azure spot sanal makinelerinin](../spot-vms.md) kullanılması, önemli bir maliyet tasarruflarından kullanılmamış kapasitemizin avantajlarından yararlanmanızı sağlar. Azure 'un kapasiteyi yeniden sağlaması gerektiğinde Azure altyapısı, Azure spot sanal makinelerini çıkarır. Bu nedenle, Azure spot sanal makineleri toplu işleme işleri, geliştirme/test ortamları, büyük işlem iş yükleri ve daha fazlası gibi kesintileri işleyebilen iş yükleri için mükemmeldir.

Azure spot sanal makineleri için fiyatlandırma, bölge ve SKU temel alınarak değişkendir. Daha fazla bilgi için bkz. [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ve [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)için VM fiyatlandırması. 

VM için saat başına ödeme yapmak istediğiniz maksimum fiyatı ayarlama seçeneğiniz vardır. En fazla 5 ondalık basamak kullanarak bir Azure spot sanal makinesi için maksimum fiyat ABD Doları (USD) olarak ayarlanabilir. Örneğin, değer, `0.98765` saat başına $0,98765 ABD Doları olan en yüksek fiyat olacaktır. En yüksek fiyatı olacak şekilde ayarlarsanız `-1` , VM fiyata göre çıkarılmaz. Kapasite ve kota kullanılabilir olduğu sürece, sanal makine için Fiyat, Azure spot sanal makinesi için geçerli fiyat veya standart bir VM 'nin fiyatı olacaktır. En yüksek fiyatı ayarlama hakkında daha fazla bilgi için bkz. [Azure spot sanal makineler-fiyatlandırma](../spot-vms.md#pricing).

Azure CLı kullanarak Azure spot sanal makinesi oluşturma işlemi [hızlı başlangıç makalesinde](./quick-create-cli.md)ayrıntılıdır. Yalnızca '--Priority Spot ' parametresini ekleyin, öğesini serbest bırak `--eviction-policy` (varsayılan) veya `Delete` olarak ayarlayın ve en yüksek fiyat ya da belirtin `-1` . 


## <a name="install-azure-cli"></a>Azure CLI'yı yükleme

Azure spot sanal makineleri oluşturmak için Azure CLı sürüm 2.0.74 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için **az--Version** ' i çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli). 

[Az Login](/cli/azure/reference-index#az_login)kullanarak Azure 'da oturum açın.

```azurecli-interactive
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Azure spot sanal makinesi oluşturma

Bu örnek, fiyata göre çıkarımayacak bir Linux Azure spot sanal makinesinin nasıl dağıtılacağını göstermektedir. Çıkarma İlkesi VM 'yi serbest bırakmak üzere ayarlanır, böylece daha sonra yeniden başlatılabilir. VM çıkartılan sanal makineyi ve temel diski silmek istiyorsanız, `--eviction-policy` olarak ayarlayın `Delete` .

```azurecli-interactive
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



VM oluşturulduktan sonra, kaynak grubundaki tüm VM 'Ler için maksimum fatura fiyatını görmek üzere sorgulama yapabilirsiniz.

```azurecli-interactive
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Çıkargı benzetimi yap

Uygulamanızın ani bir çıkarmasına ne kadar iyi yanıt vereceğini test etmek için REST, PowerShell veya CLı kullanarak bir Azure spot sanal makinesi çıkarması benzetimi yapabilirsiniz.

Çoğu durumda, REST API sanal makineleri kullanmak isteyeceksiniz-uygulamaların otomatikleştirilmiş test edilmesine yardımcı olmak için [çıkarmayı benzetim](/rest/api/compute/virtualmachines/simulateeviction) . REST için, `Response Code: 204` benzetimi yapılan çıkargı başarılı oldu demektir. Sanal makine çıkarıldığında uygulamanızın nasıl yanıt vereceğini otomatik hale getirmek için [planlı olay hizmeti](scheduled-events.md)ile benzetilen çıkarmaları birleştirebilirsiniz.

Zamanlanan olayları işlem içinde görmek için [Azure Cuma 'yı izleyin-VM bakımına hazırlanmak Için azure zamanlanan olaylar kullanma](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance).


### <a name="quick-test"></a>Hızlı test

Bir sanal makinenin nasıl çalıştığını gösteren hızlı bir test için, Azure CLı kullanarak bir çıkarma benzetimi yaparken nasıl göründüğünü görmek üzere zamanlanmış olay hizmetini sorgulama konusunda adım adım inceleyelim.

Olay için ilk kez istek yaptığınızda zamanlanan olay hizmeti hizmetiniz için etkinleştirilir. 

Sanal makinenize uzaktan ekleyin ve ardından bir komut istemi açın. 

SANAL makinenizin komut isteminden şunu yazın:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Bu ilk yanıt 2 dakikaya kadar sürebilir. Bundan sonra çıktıyı neredeyse hemen görüntülemesi gerekir.

Azure CLı 'nın yüklü olduğu bir bilgisayardan (yerel makineniz gibi) [az VM simülasyonu](https://docs.microsoft.com/cli/azure/vm#az_vm_simulate_eviction)çıkarma kullanarak bir çıkarma benzetimi yapın. Kaynak grubu adını ve VM adını kendi adınızla değiştirin. 

```azurecli-interactive
az vm simulate-eviction --resource-group mySpotRG --name mySpot
```

Yanıt çıkışı, `Status: Succeeded` isteğin başarılı bir şekilde yapılmış olması durumunda olacaktır.

Spot sanal makinenize hızlı bir şekilde uzak bağlantınıza dönün ve Zamanlanan Olaylar uç noktasını tekrar sorgulayın. Daha fazla bilgi içeren bir çıkış yapana kadar aşağıdaki komutu yineleyin:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Zamanlanan olay hizmeti çıkarma bildirimini aldığında şuna benzer bir yanıt alacaksınız:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

Bunu `"EventType":"Preempt"` ve KAYNAĞıN VM kaynağı olduğunu görebilirsiniz `"Resources":["myspotvm"]` . 

Ayrıca, sanal makinenin ne zaman çıkarılmasını denetleyerek, `"NotBefore"` sanal makinenin bir süre önce çıkarılmayacak, böylece uygulamanızın sizin tarafınızdan sorunsuz bir şekilde kapatılmasını sağlayabilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

[Azure PowerShell](../windows/spot-powershell.md), [Portal](../spot-portal.md)veya [şablon](spot-template.md)kullanarak Azure spot sanal makinesi de oluşturabilirsiniz.

Azure spot sanal makinesi hakkında bilgi edinmek için [Azure perakende FIYATLARı API](/rest/api/cost-management/retail-prices/azure-retail-prices) 'sini kullanarak geçerli fiyatlandırma bilgilerini sorgulayın. `meterName`Ve `skuName` her ikisi de içerecektir `Spot` .

Bir hatayla karşılaşırsanız bkz. [hata kodları](../error-codes-spot.md).
