---
title: Öğretici-Azure şablonları ile ölçek kümesini otomatik ölçeklendirme
description: CPU talepleri arttıkça ve azaldıkça, sanal makine ölçek kümesini otomatik olarak ölçeklendirmek için Azure Resource Manager şablonlarının nasıl kullanılacağını öğrenin
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 03/27/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 88cec878ca5d3ccab3a232888ff3a3c0b0faa1db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705260"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-an-azure-template"></a>Öğretici: Azure şablonu ile sanal makine ölçek kümesini otomatik olarak ölçeklendirme
Ölçek kümesi oluşturduğunuzda, çalıştırmak istediğiniz VM örneği sayısını tanımlarsınız. Uygulamanızın talebi değiştikçe, sanal makine örneklerinin sayısını otomatik olarak artırabilir veya azaltabilirsiniz. Otomatik ölçeklendirme özelliği, uygulamanızın yaşam döngüsü boyunca uygulama performansındaki değişikliklere veya müşteri taleplerine ayak uydurmanıza olanak tanır. Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Ölçek kümesiyle otomatik ölçeklendirmeyi kullanma
> * Otomatik ölçeklendirme kuralları oluşturma ve kullanma
> * Sanal makine örneklerinde stres testi yapma ve otomatik ölçeklendirme kurallarını tetikleme
> * Talep düştüğünde geriye doğru otomatik ölçeklendirme

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.29 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür. 


## <a name="define-an-autoscale-profile"></a>Otomatik ölçeklendirme profilini tanımlama
*Microsoft.insights/autoscalesettings* kaynak sağlayıcısı ile bir Azure şablonunda otomatik ölçeklendirme profili tanımlayın. *Profil*, ölçek kümesinin ve tüm ilişkili kuralların kapasitesini açıklar. Aşağıdaki örnek, *CPU kullanımına dayalı yüzdeye göre otomatik ölçeklendir* adlı bir profili tanımlar ve varsayılan değeri, minimum değeri, *2* sanal makine örneği kapasitesini ve maksimum *10* değerini ayarlar:

```json
{
"type": "Microsoft.insights/autoscalesettings",
"name": "Autoscale",
"apiVersion": "2015-04-01",
"location": "[variables('location')]",
"scale": null,
"properties": {
  "profiles": [
    {
      "name": "Autoscale by percentage based on CPU usage",
      "capacity": {
        "minimum": "2",
        "maximum": "10",
        "default": "2"
      }
    }
  ]
}
```


## <a name="define-a-rule-to-autoscale-out"></a>Otomatik ölçeklendirme ölçeğini genişletmek için bir kural tanımlama
Uygulamanızın talebi artarsa, ölçek kümenizdeki sanal makine örneklerinde üzerindeki yük de artar. Bu kısa süreli bir talep olmayıp tutarlı şekilde yük artıyorsa, ölçek kümesindeki sanal makine örneği sayısını artırmak için otomatik ölçeklendirme kuralları yapılandırabilirsiniz. Bu sanal makine örnekleri oluşturulduğunda ve uygulamalarınız dağıtıldığında ölçek kümesi, yük dengeleyici aracılığıyla bunlara trafiği dağıtmaya başlar. CPU veya disk gibi hangi ölçümlerin izleneceğini, uygulama yükünün belirli bir eşiği ne kadar süre karşılaması gerektiği ve ölçek kümesine kaç tane sanal makine örneği ekleneceğini denetlersiniz.

Aşağıdaki örnekte, ortalama CPU yükü 5 dakika boyunca %70’in üzerine çıktığında bir ölçek kümesindeki sanal makine örneği sayısını artıran bir kural tanımlanır. Kural tetiklendiğinde, sanal makine örneği sayısı üç artırılır.

Bu kural için aşağıdaki parametreler kullanılır:

| Parametre         | Açıklama                                                                                                         | Değer           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | İzlenecek ve ölçek kümesi eylemlerinin uygulanmasında temel alınacak performans ölçümü.                                                   | CPU yüzdesi  |
| *zamandilimi*       | Analiz için ne sıklıkla ölçümlerin toplanacağı.                                                                   | 1 dakika        |
| *timeAggregation* | Toplanan ölçümlerin analiz için nasıl bir araya getirileceğini tanımlar.                                                | Ortalama         |
| *timeWindow*      | Ölçüm ve eşik değerleri karşılaştırılmadan önce izlenecek süre.                                   | 5 dakika       |
| *işlecinde*        | Ölçüm verilerini eşikle karşılaştırmak için kullanılan işleç.                                                     | Büyüktür    |
| *eşiği*       | Otomatik ölçeklendirme kuralının bir eylemi tetiklemesine neden olan değer.                                                      | %70             |
| *Görünüm*       | Kural geçerli olduğunda ölçek kümesinin ölçeğinin genişletileceğini veya daraltılacağını tanımlar.                                              | Artır        |
| *türüyle*            | Sanal makine örneği sayısının belirli bir değerle değiştirilmesi gerektiğini belirtir.                                    | Değiştirme Sayısı    |
| *değer*           | Kural geçerli olduğunda kaç tane sanal makine örneğinin ölçeğinin genişletileceği veya daraltılacağı.                                             | 3               |
| *cooldown*        | Otomatik ölçeklendirme eylemlerinin geçerli olması için kural tekrar uygulanmadan önceki bekleme süresi. | 5 dakika       |

Önceki bölümde yer alan *Microsoft.insights/autoscalesettings* kaynak sağlayıcısının profil bölümüne aşağıdaki kural eklenir:

```json
"rules": [
  {
    "metricTrigger": {
      "metricName": "Percentage CPU",
      "metricNamespace": "",
      "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('vmssName'))]",
      "metricResourceLocation": "[variables('location')]",
      "timeGrain": "PT1M",
      "statistic": "Average",
      "timeWindow": "PT5M",
      "timeAggregation": "Average",
      "operator": "GreaterThan",
      "threshold": 70
    },
    "scaleAction": {
      "direction": "Increase",
      "type": "ChangeCount",
      "value": "3",
      "cooldown": "PT5M"
    }
  }
]
```


## <a name="define-a-rule-to-autoscale-in"></a>Otomatik ölçeklendirme ölçeğini daraltmak için bir kural tanımlama
Bir akşam veya hafta sonu uygulama talebiniz azalabilir. Yük belirli bir süreye yayılarak tutarlı şekilde azalıyorsa, ölçek kümesindeki sanal makine örneği sayısını azaltmak için otomatik ölçeklendirme kuralları yapılandırabilirsiniz. Mevcut talebi karşılamak için gerekli örnek sayısını yalnızca siz çalıştırdığınızdan, bu ölçeği daraltma eylemi ölçek kümenizi çalıştırma maliyetini azaltır.

Aşağıdaki örnek, ortalama CPU yükü 5 dakikadan uzun bir süre boyunca %30’un altına düştüğünde sanal makine sayısını bir azaltmak için bir kural tanımlar. Önceki ölçeği genişletme kuralından sonra otomatik ölçeklendirme profiline bu kural eklenir:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('vmssName'))]",
    "metricResourceLocation": "[variables('location')]",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "ChangeCount",
    "value": "1",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-an-autoscaling-scale-set"></a>Otomatik ölçeklendirme ölçek kümesi oluşturma
Şimdi örnek bir şablon kullanarak bir ölçek kümesi oluşturalım ve otomatik ölçeklendirme kuralları uygulayalım. [Tüm şablonu gözden geçirebilir](https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/autoscale.json) veya [şablonun *Microsoft.insights/autoscalesettings* kaynak sağlayıcı bölümüne](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/autoscale.json#L220) bakabilirsiniz.

Öncelikle [az group create](/cli/azure/group) komutuyla bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Şimdi [az Deployment Group Create](/cli/azure/deployment/group)komutuyla bir sanal makine ölçek kümesi oluşturun. İstendiğinde, her bir sanal makine örneği için kimlik bilgileri olarak kullanılan kendi kullanıcı adınızı (örn. *azureuser*) ve parolanızı sağlayın.

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/autoscale.json
```

Tüm ölçek kümesi kaynaklarının ve VM'lerin oluşturulup yapılandırılması birkaç dakika sürer.


## <a name="generate-cpu-load-on-scale-set"></a>Ölçek kümesinde CPU yükü oluşturma
Otomatik ölçeklendirme kurallarını test etmek için, ölçek kümesindeki sanal makine örneklerinde biraz CPU yükü oluşturun. Bu benzetimi yapılan CPU yükü, otomatik ölçeklendirme kurallarının ölçeği genişletmesine ve sanal makine örneği sayısını artırmasına neden olur. Benzetimi yapılan CPU yükü daha sonra azaldığında otomatik ölçeklendirme kuralları ölçeği daraltır ve sanal makine örneği sayısını azaltır.

İlk olarak, [az vmss list-instance-connection-info](/cli/azure/vmss) komutunu kullanarak bir ölçek kümesindeki sanal makine örneklerine bağlanacak bağlantı noktalarını ve adresi listeleyin:

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

Aşağıdaki örnek çıktıda, yük dengeleyicinin genel IP adresi, örnek adı ve Ağ Adresi Çevirisi (NAT) kurallarının trafiği ilettiği bağlantı noktası numarası gösterilmektedir:

```json
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

Birinci sanal makine örneğinizde SSH oturumu açın. Önceki komutta gösterildiği gibi, `-p` parametresiyle kendi genel IP adresinizi ve bağlantı noktası numaranızı belirtin:

```console
ssh azureuser@13.92.224.66 -p 50001
```

Oturum açtıktan sonra **stres** yardımcı programını yükleyin. CPU yükü oluşturan *10* **stress** çalışanıyla başlayın. Bu çalışanlar *420* saniye çalışır; bu, otomatik ölçeklendirme kurallarının istenen eylemi uygulamasını sağlamak için yeterlidir.

```console
sudo apt-get update
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

**stress**, *stress: info: [2688] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd* benzeri bir çıktı gösterdiğinde, isteme geri dönmek için *Enter* tuşuna basın.

**stress** yardımcı programının CPU yükü oluşturduğunu onaylamak için, **top** yardımcı programını kullanarak etkin sistem yükünü inceleyin:

```console
top
```

**top** yardımcı programından çıkın ve sanal makine örneğiyle bağlantınızı kapatın. **stress** yardımcı programı, sanal makine örneğinde çalışmaya devam eder.

```console
Ctrl-c
exit
```

Önceki [az vmss list-instance-connection-info](/cli/azure/vmss) komutunda yer alan bağlantı noktası numarası ile ikinci sanal makine örneğine bağlanın:

```console
ssh azureuser@13.92.224.66 -p 50003
```

**stress** yardımcı programını yükleyip çalıştırın ve sonra bu ikinci sanal makine örneğinde on çalışan başlatın.

```console
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Tekrar **stress**, *stress: info: [2713] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd* benzeri bir çıktı gösterdiğinde, isteme geri dönmek için *Enter* tuşuna basın.

İkinci sanal makine örneğiyle bağlantınızı kapatın. **stress** yardımcı programı, sanal makine örneğinde çalışmaya devam eder.

```console
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>Etkin otomatik ölçeklendirme kurallarını izleme
Ölçek kümenizdeki sanal makine örneği sayısını izlemek için **watch** yardımcı programını kullanın. Otomatik ölçeklendirme kurallarının, sanal makine örneklerinin her birinde **stress** tarafından oluşturulan CPU yüküne yanıt olarak ölçeği genişletme işlemini başlatması 5 dakika sürer.

```azurecli-interactive
watch az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

CPU eşiği karşılandıktan sonra otomatik ölçeklendirme kuralları, ölçek kümesindeki sanal makine örneği sayısını artırır. Aşağıdaki çıkışta, ölçek kümesi ölçeği genişlettiğinde oluşturulan üç sanal makine gösterilir:

```output
Every 2.0s: az vmss list-instances --resource-group myResourceGroup --name myScaleSet --output table

  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            MYRESOURCEGROUP  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             MYRESOURCEGROUP  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             MYRESOURCEGROUP  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

**stress** yardımcı programı ilk sanal makine örneklerinde durduğunda ortalama CPU yükü normale döner. Bir 5 dakika daha geçtikten sonra otomatik ölçeklendirme kuralları, ölçeği daraltarak sanal makine örneklerinin sayısını azaltır. Ölçeği daraltma eylemleri önce en yüksek kimliğe sahip sanal makine örneklerini kaldırır. Bir ölçek kümesi Kullanılabilirlik Kümeleri veya Kullanılabilirlik Alanları kullandığında, eylemlerdeki ölçek VM örnekleri arasında eşit olarak dağıtılır. Aşağıdaki örnek çıktıda, ölçek kümesi otomatik olarak ölçeği daralttığında tek bir sanal makine örneğinin silindiği gösterilmektedir:

```output
           6  True                  eastus      myScaleSet_6  Deleting             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

`Ctrl-c` ile *watch* yardımcı programından çıkın. Ölçek kümesi, minimum 2 örnek sayısına ulaşılıncaya kadar her 5 dakikada bir ölçeği daraltmaya ve bir sanal makine örneğini kaldırmaya devam eder.


## <a name="clean-up-resources"></a>Kaynakları temizleme
Ölçek kümesini ve ek kaynaklarınızı kaldırmak için [az Group Delete](/cli/azure/group)komutuyla kaynak grubunu ve tüm kaynaklarını silin:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, Azure CLI ile otomatik olarak bir ölçek kümesinin ölçeğini daraltma veya genişletme işleminin nasıl yapılacağını öğrendiniz:

> [!div class="checklist"]
> * Ölçek kümesiyle otomatik ölçeklendirmeyi kullanma
> * Otomatik ölçeklendirme kuralları oluşturma ve kullanma
> * Sanal makine örneklerinde stres testi yapma ve otomatik ölçeklendirme kurallarını tetikleme
> * Talep düştüğünde geriye doğru otomatik ölçeklendirme
