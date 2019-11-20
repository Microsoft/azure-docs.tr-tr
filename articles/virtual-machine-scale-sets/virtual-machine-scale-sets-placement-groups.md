---
title: Büyük Azure Sanal Makine Ölçek Kümeleri ile çalışma | Microsoft Docs
description: Büyük Azure sanal makine ölçek kümelerini kullanmak için bilmeniz gerekenler
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/9/2017
ms.author: jeconnoc
ms.openlocfilehash: 46ca46c99187b14974b78ccc4acc134a5f716b05
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326698"
---
# <a name="working-with-large-virtual-machine-scale-sets"></a>Büyük sanal makine ölçek kümeleri ile çalışma
Artık, 1000 adede kadar VM kapasiteli Azure [sanal makine ölçek kümeleri](/azure/virtual-machine-scale-sets/) oluşturabilirsiniz. Bu belgede _büyük bir sanal makine ölçek kümesi_, 100’den fazla VM'yi ölçeklendirme kapasitesine sahip bir ölçek kümesi olarak tanımlanır. Bu özellik bir ölçek kümesi özelliği ile ayarlanır (_singlePlacementGroup=False_). 

Büyük ölçek kümelerinin yük dengeleme ve hata etki alanları gibi bazı özellikleri, standart bir ölçek kümesinden farklı davranır. Bu belgede büyük ölçek kümelerinin özellikleri ve bunları uygulamalarınızda başarıyla kullanabilmeniz için bilmeniz gerekenler açıklanmaktadır. 

Büyük ölçekte bulut altyapısı dağıtmaya yönelik genel bir yaklaşım, _ölçek birimleri_ kümesi oluşturmayı içerir (örneğin, birden fazla sanal ağ ve depolama hesabında birden fazla VM oluşturarak). Bu yaklaşım, tek VM'lerle karşılaştırıldığında daha kolay yönetim sağlar. Ayrıca, birden fazla ölçek birimi, yığınlanabilen bileşenler (birden çok sanal ağ ve uç nokta gibi) gerektiren uygulamalar başta olmak üzere çoğu uygulama için yararlıdır. Ancak, uygulamanız tek bir büyük küme gerektiriyorsa, 1.000 adede kadar VM içerebilen tek bir ölçek kümesinin dağıtılması daha kolay olabilir. Örnek senaryolar arasında merkezi büyük veri dağıtımları veya büyük bir çalışan düğümü havuzunun basit yönetimini gerektiren işlem kılavuzları sayılabilir. Sanal makine ölçek kümesi [bağlı veri diskleri](virtual-machine-scale-sets-attached-disks.md) ile bir araya geldiğinde, büyük ölçek kümeleri, tek bir işlem olarak binlerce vCPU ve petabaytlarca depolama alanından oluşan ölçeklenebilir bir altyapı dağıtmanızı sağlar.

## <a name="placement-groups"></a>Yerleştirme grupları 
_Büyük_ bir ölçek kümesini özel kılan özellik VM sayısı değil, içerdiği _yerleştirme grubu_ sayısıdır. Yerleştirme grubu, kendi hata etki alanları ve yükseltme etki alanları ile Azure kullanılabilirlik kümesine benzer bir yapıdır. Varsayılan olarak, bir ölçek kümesi en fazla 100 VM boyutuna sahip tek bir yerleştirme grubundan oluşur. _singlePlacementGroup_ adlı ölçek kümesi özelliği _false_ olarak ayarlanırsa, ölçek kümesi birden fazla yerleştirme grubundan oluşabilir ve 0-1.000 aralığında VM içerebilir. Varsayılan _true_ değerine ayarlandığında ise ölçek kümesi tek bir yerleştirme grubundan oluşur ve 0-100 aralığında VM içerir.

## <a name="checklist-for-using-large-scale-sets"></a>Büyük ölçek kümelerini kullanmaya ilişkin denetim listesi
Uygulamanızın büyük ölçek kümelerini etkili bir şekilde kullanıp kullanmadığına karar vermek için aşağıdaki gereksinimleri göz önünde bulundurun:

- Çok sayıda VM dağıtmayı planlıyorsanız, İşlem vCPU kotası sınırlarınızın artırılması gerekebilir. 
- Azure Market’te oluşturulan ölçek kümeleri 1.000 VM’ye kadar ölçeklendirilebilir.
- Özel görüntülerden oluşturulan ölçek kümeleri (kendi oluşturduğunuz ve yüklediğiniz VM görüntüleri) şu anda en fazla 600 VM’ye kadar ölçeklendirilebilir.
- Büyük ölçek kümeleri Azure Yönetilen Diskleri gerektirir. Yönetilen Diskler ile oluşturulmayan ölçek kümeleri birden fazla depolama hesabı (her 20 VM için bir tane) gerektirir. Büyük ölçek kümeleri, depolama yönetimi yükünüzü azaltmak ve depolama hesaplarının abonelik sınırlarını aşma riskini önlemek üzere yalnızca Yönetilen Disklerle çalışacak şekilde tasarlanmıştır. 
- Birden fazla yerleştirme grubundan oluşan ölçek kümeleriyle Katman-4 yük dengelemesi için [Azure Load Balancer Standart SKU'su](../load-balancer/load-balancer-standard-overview.md) gerekir. Load Balancer Standart SKU'su, birden çok ölçek kümesi arasında yük dengeleme özelliği gibi ek avantajlar sağlar. Standart SKU ayrıca, ölçek kümesinin kendisiyle ilişkilendirilmiş bir Ağ Güvenlik Grubu olmasını da gerektirir; aksi takdirde NAT havuzları düzgün çalışmaz. Azure Load Balancer Temel SKU'sunu kullanmanız gerekirse, ölçek kümesinin tek bir yerleştirme grubu kullanacak şekilde (varsayılan ayar) yapılandırıldığından emin olun.
- Azure Application Gateway ile 7. katman yük dengeleme tüm ölçek kümeleri için desteklenir.
- Ölçek kümesi tek bir alt ağ ile tanımlanır; alt ağınızın gereken tüm VM’ler için yeterince geniş bir adres alanına sahip olduğundan emin olun. Varsayılan olarak, dağıtım güvenilirliğini ve performansını artırmak için ölçek kümesi fazla sağlama yapar (dağıtım sırasında veya ölçeklendirme sırasında fazladan VM oluşturur, bunlar için ücret alınmaz). Ölçeklendirmeyi planladığınız VM sayısından %20 daha fazla adres alanı ayırın.
- Hata etki alanları ve yükseltme etki alanları yalnızca bir yerleştirme grubu içinde tutarlıdır. VM’ler ayrı bir fiziksel donanım üzerinde dağıtıldığı için bu mimari bir ölçek kümesinin genel kullanılabilirliğini değiştirmez, ancak farklı bir donanım üzerinde iki VM’yi garanti etmeniz gerekiyorsa, bunların aynı yerleştirme grubundaki farklı hata etki alanlarında olduğundan emin olmanız gerektiği anlamına gelir. Lütfen bu bağlantı [kullanılabilirliği seçeneklerine](/azure/virtual-machines/windows/availability)bakın. 
- Hata etki alanı ve yerleştirme grubu kimliği, bir ölçek kümesi sanal makinesinin _örnek görünümünde_ gösterilir. Bir ölçek kümesi sanal makinesinin örnek görünümünü [Azure Kaynak Gezgini](https://resources.azure.com/)’nde görüntüleyebilirsiniz.

## <a name="creating-a-large-scale-set"></a>Büyük ölçek kümesi oluşturma
Azure portalda bir ölçek kümesi oluştururken en fazla 1000 olarak şekilde bir *Örnek sayısı* değeri belirtin. Bu sayı 100'den fazlaysa *100'den fazla örnekle ölçeklendirmeyi etkinleştirme* ayarı *Evet* olarak belirlenir ve bu sayede birden fazla yerleştirme grubuna ölçeklendirme yapılabilir. 

![](./media/virtual-machine-scale-sets-placement-groups/portal-large-scale.png)

[Azure CLI](https://github.com/Azure/azure-cli) _az vmss create_ komutunu kullanarak büyük bir sanal makine ölçek kümesi oluşturabilirsiniz. Bu komut, alt ağ boyutu gibi akıllı varsayılan değerleri _instance-count_ bağımsız değişkenine göre ayarlar:

```bash
az group create -l southcentralus -n biginfra
az vmss create -g biginfra -n bigvmss --image ubuntults --instance-count 1000
```
Belirtmemeniz durumunda, _vmss create_ komutu bazı yapılandırma değerlerini varsayılan olarak kullanır. Geçersiz kılabileceğiniz seçenekleri görmek için şunları deneyin:
```bash
az vmss create --help
```

Bir Azure Resource Manager şablonu oluşturarak büyük bir ölçek kümesi oluşturuyorsanız, şablonun Azure Yönetilen Diskleri temel alan bir ölçek kümesi oluşturduğundan emin olun. _Microsoft. COMPUTE/virtualMachineScaleSets_ kaynağının _Özellikler_ bölümünde _singleplacementgroup_ özelliğini _false_ olarak ayarlayabilirsiniz. Aşağıdaki JSON parçası, 1.000 VM kapasite ve _"singlePlacementGroup" : false_ ayarına sahip bir ölçek kümesi şablonunun başlangıcını gösterir:
```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "location": "australiaeast",
  "name": "bigvmss",
  "sku": {
    "name": "Standard_DS1_v2",
    "tier": "Standard",
    "capacity": 1000
  },
  "properties": {
    "singlePlacementGroup": false,
    "upgradePolicy": {
      "mode": "Automatic"
    }
```
Büyük bir ölçek kümesi şablonunun tam örneği için [https://github.com/gbowerman/azure-myriad/blob/master/bigtest/bigbottle.json](https://github.com/gbowerman/azure-myriad/blob/master/bigtest/bigbottle.json) adresine başvurun.

## <a name="converting-an-existing-scale-set-to-span-multiple-placement-groups"></a>Mevcut bir ölçek kümesini birden fazla yerleştirme grubuna yayılacak şekilde dönüştürme
Mevcut bir sanal makine ölçek kümesini 100’den fazla VM ölçeklendirebilecek kapasiteye getirmek için, ölçek kümesi modelinde _singplePlacementGroup_ özelliğini _false_ olarak ayarlamanız gerekir. Bu özelliğin değiştirilmesini [Azure Kaynak Gezgini](https://resources.azure.com/) ile test edebilirsiniz. Mevcut bir ölçek kümesini bulun, _Düzenle_’yi seçin ve _singlePlacementGroup_ özelliğini değiştirin. Bu özelliği görmüyorsanız, ölçek kümesini Microsoft.Compute API’sinin daha eski bir sürümüyle görüntülüyor olabilirsiniz.

> [!NOTE]
> Tek bir yerleştirme grubunu destekleyen bir ölçek kümesini (varsayılan davranış), birden fazla yerleştirme grubunu destekleyecek şekilde değiştirebilirsiniz, ancak zıt yönde değiştiremezsiniz. Bu nedenle, dönüştürmeden önce büyük ölçek kümelerinin özelliklerini anladığınızdan emin olun.


