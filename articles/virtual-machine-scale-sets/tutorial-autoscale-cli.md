---
title: Öğretici-Azure CLı ile ölçek kümesini otomatik ölçeklendirme
description: CPU talepleri arttıkça ve azaldıkça, sanal makine ölçek kümesini Azure CLI ile otomatik olarak ölçeklendirmeyi öğrenin
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 05/18/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: b7fdf6d4893a6f6a970223671b28fdae6db3ef3d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763001"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli"></a>Öğretici: Azure CLI ile sanal makine ölçek kümesini otomatik olarak ölçeklendirme

Ölçek kümesi oluşturduğunuzda, çalıştırmak istediğiniz VM örneği sayısını tanımlarsınız. Uygulamanızın talebi değiştikçe, sanal makine örneklerinin sayısını otomatik olarak artırabilir veya azaltabilirsiniz. Otomatik ölçeklendirme özelliği, uygulamanızın yaşam döngüsü boyunca uygulama performansındaki değişikliklere veya müşteri taleplerine ayak uydurmanıza olanak tanır. Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Ölçek kümesiyle otomatik ölçeklendirmeyi kullanma
> * Otomatik ölçeklendirme kuralları oluşturma ve kullanma
> * Sanal makine örneklerinde stres testi yapma ve otomatik ölçeklendirme kurallarını tetikleme
> * Talep düştüğünde geriye doğru otomatik ölçeklendirme

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu öğretici, Azure CLı 'nin sürüm 2.0.32 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-scale-set"></a>Ölçek kümesi oluşturma

Aşağıdaki adımları uygulayarak [az group create](/cli/azure/group) ile bir kaynak grubu oluşturun:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Bu adımda [az vmss create](/cli/azure/vmss) ile bir sanal makine ölçek kümesi oluşturun. Aşağıdaki örnek, *2* örnek sayısı ile bir ölçek kümesini ve yoksa SSH anahtarlarını oluşturur:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --instance-count 2 \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="define-an-autoscale-profile"></a>Otomatik ölçeklendirme profilini tanımlama

Bir ölçek kümesinde otomatik ölçeklendirmeyi etkinleştirmek için ilk olarak bir otomatik ölçeklendirme profili tanımlamanız gerekir. Bu profil varsayılan, en düşük ve en yüksek ölçek kümesi kapasitesini tanımlar. Bu sınırlar, sanal makine örneklerini sürekli olarak oluşturmadığından maliyeti denetlemenizi sağlar ve ölçek genişletme olayında kalan minimum örnek sayısı ile kabul edilebilir performansı dengelemenize olanak tanır. [az monitor autoscale create](/cli/azure/monitor/autoscale#az_monitor_autoscale_create) komutu ile bir otomatik ölçeklendirme profili oluşturun. Aşağıdaki örnek, varsayılan ve en düşük, *2* VM örneğinin kapasitesini ve en fazla *10*' u ayarlar:

```azurecli-interactive
az monitor autoscale create \
  --resource-group myResourceGroup \
  --resource myScaleSet \
  --resource-type Microsoft.Compute/virtualMachineScaleSets \
  --name autoscale \
  --min-count 2 \
  --max-count 10 \
  --count 2
```

## <a name="create-a-rule-to-autoscale-out"></a>Otomatik ölçeklendirme ölçeğini genişletmek için kural oluşturma

Uygulamanızın talebi artarsa, ölçek kümenizdeki sanal makine örneklerinde üzerindeki yük de artar. Bu kısa süreli bir talep olmayıp tutarlı şekilde yük artıyorsa, ölçek kümesindeki sanal makine örneği sayısını artırmak için otomatik ölçeklendirme kuralları yapılandırabilirsiniz. Bu sanal makine örnekleri oluşturulduğunda ve uygulamalarınız dağıtıldığında ölçek kümesi, yük dengeleyici aracılığıyla bunlara trafiği dağıtmaya başlar. CPU veya disk gibi hangi ölçümlerin izleneceğini, uygulama yükünün belirli bir eşiği ne kadar süre karşılaması gerektiği ve ölçek kümesine kaç tane sanal makine örneği ekleneceğini denetlersiniz.

Şimdi [az monitor autoscale rule create](/cli/azure/monitor/autoscale/rule#az_monitor_autoscale_rule_create) komutu ile, ortalama CPU yükü 5 dakika boyunca %70’in üzerine çıktığında bir ölçek kümesindeki sanal makine örneği sayısını artıran bir kural oluşturalım. Kural tetiklendiğinde, sanal makine örneği sayısı üç artırılır.

```azurecli-interactive
az monitor autoscale rule create \
  --resource-group myResourceGroup \
  --autoscale-name autoscale \
  --condition "Percentage CPU > 70 avg 5m" \
  --scale out 3
```

## <a name="create-a-rule-to-autoscale-in"></a>Otomatik ölçeklendirme ölçeğini daraltmak için kural oluşturma

Bir akşam veya hafta sonu uygulama talebiniz azalabilir. Yük belirli bir süreye yayılarak tutarlı şekilde azalıyorsa, ölçek kümesindeki sanal makine örneği sayısını azaltmak için otomatik ölçeklendirme kuralları yapılandırabilirsiniz. Mevcut talebi karşılamak için gerekli örnek sayısını yalnızca siz çalıştırdığınızdan, bu ölçeği daraltma eylemi ölçek kümenizi çalıştırma maliyetini azaltır.

[az monitor autoscale rule create](/cli/azure/monitor/autoscale/rule#az_monitor_autoscale_rule_create) komutu ile, ortalama CPU yükü 5 dakika boyunca %30’un altına indiğinde bir ölçek kümesindeki sanal makine örneği sayısını azaltan başka bir kural oluşturun. Aşağıdaki örnek, sanal makine örneği sayısını bir artırmak için kuralı tanımlar:

```azurecli-interactive
az monitor autoscale rule create \
  --resource-group myResourceGroup \
  --autoscale-name autoscale \
  --condition "Percentage CPU < 30 avg 5m" \
  --scale in 1
```

## <a name="generate-cpu-load-on-scale-set"></a>Ölçek kümesinde CPU yükü oluşturma

Otomatik ölçeklendirme kurallarını test etmek için, ölçek kümesindeki sanal makine örneklerinde biraz CPU yükü oluşturun. Bu benzetimi yapılan CPU yükü, otomatik ölçeklendirmenin ölçeği genişletmesine ve sanal makine örneği sayısını artırmasına neden olur. Benzetimi yapılan CPU yükü daha sonra azaldığında otomatik ölçeklendirme kuralları ölçeği daraltır ve sanal makine örneği sayısını azaltır.

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

Ölçek kümenizdeki sanal makine örneği sayısını izlemek için **watch** yardımcı programını kullanın. Otomatik ölçeklendirme kurallarının, sanal makine örneklerinin her birinde **stress** tarafından oluşturulan CPU yüküne yanıt olarak ölçeği genişletme işlemini başlatması 5 dakika sürer:

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
           1  True                  eastus      myScaleSet_1  Succeeded            myResourceGroup  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            myResourceGroup  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             myResourceGroup  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             myResourceGroup  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             myResourceGroup  9e4133dd-2c57-490e-ae45-90513ce3b336
```

**stress** yardımcı programı ilk sanal makine örneklerinde durduğunda ortalama CPU yükü normale döner. Bir 5 dakika daha geçtikten sonra otomatik ölçeklendirme kuralları, ölçeği daraltarak sanal makine örneklerinin sayısını azaltır. Ölçeği daraltma eylemleri önce en yüksek kimliğe sahip sanal makine örneklerini kaldırır. Bir ölçek kümesi Kullanılabilirlik Kümeleri veya Kullanılabilirlik Alanları kullandığında, eylemlerdeki ölçek VM örnekleri arasında eşit olarak dağıtılır. Aşağıdaki örnek çıktıda, ölçek kümesi otomatik olarak ölçeği daralttığında tek bir sanal makine örneğinin silindiği gösterilmektedir:

```output
           6  True                  eastus      myScaleSet_6  Deleting             myResourceGroup  9e4133dd-2c57-490e-ae45-90513ce3b336
```

`Ctrl-c` ile *watch* yardımcı programından çıkın. Ölçek kümesi, minimum 2 örnek sayısına ulaşılıncaya kadar her 5 dakikada bir ölçeği daraltmaya ve bir sanal makine örneğini kaldırmaya devam eder.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Ölçek kümenizi ve ek kaynaklarınızı kaldırmak için [az group delete](/cli/azure/group) komutunu kullanarak kaynak grubunu ve bu kaynak grubunun tüm kaynaklarını silin. `--no-wait` parametresi işlemin tamamlanmasını beklemeden denetimi komut istemine döndürür. `--yes` parametresi kaynakları ek bir komut istemi olmadan silmek istediğinizi onaylar.

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
