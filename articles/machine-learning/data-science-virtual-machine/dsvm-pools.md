---
title: Paylaşılan havuzlar
titleSuffix: Azure Data Science Virtual Machine
description: Bir ekip için paylaşılan kaynak olarak paylaşılan bir veri bilimi sanal makinesi (DSVMs) havuzu oluşturma & oluşturmayı öğrenin.
keywords: derin öğrenme, AI, veri bilimi araçları, veri bilimi sanal makinesi, Jeo-uzamsal analiz, ekip veri bilimi işlemi
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: a118d5a3e716a80bda21ffe82a8cefd1da1202f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519720"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Paylaşılan veri bilimi sanal makineleri havuzu oluşturma

Bu makalede, bir ekip için paylaşılan bir veri bilimi sanal makineleri Havuzu (DSVMs) oluşturmayı öğreneceksiniz. Paylaşılan havuz kullanmanın avantajları, daha iyi kaynak kullanımı, daha kolay paylaşım ve işbirliği ve DSVM kaynaklarının daha etkili bir şekilde yönetilmesini içerir.

Bir DSVMs havuzu oluşturmak için birçok yöntem ve teknolojiden yararlanabilirsiniz. Bu makale etkileşimli sanal makineler (VM 'Ler) için havuzlara odaklanır. Alternatif bir yönetilen işlem altyapısı Azure Machine Learning Işlem olur. Daha fazla bilgi için bkz. [işlem kümesi oluşturma](../how-to-create-attach-compute-cluster.md).

## <a name="interactive-vm-pool"></a>Etkileşimli VM havuzu

Tüm AI/veri bilimi ekibi tarafından paylaşılan etkileşimli VM havuzları, kullanıcıların her bir kullanıcı kümesi için adanmış bir örnek olması yerine DSVM 'nin kullanılabilir bir örneğinde oturum açmasına olanak tanır. Bu kurulum, kaynakların daha iyi kullanılabilirliğini ve daha etkili kullanımını sunar.

Etkileşimli bir VM havuzu oluşturmak için [Azure sanal makine ölçek kümeleri](../../virtual-machine-scale-sets/index.yml) teknolojisini kullanırsınız. Ölçek kümelerini kullanarak özdeş, yük dengeli ve otomatik ölçeklendirme VM 'Leri grubu oluşturabilir ve yönetebilirsiniz.

Kullanıcı ana havuzun IP veya DNS adresinde oturum açar. Ölçek kümesi, oturumu otomatik olarak ölçek kümesindeki kullanılabilir bir DSVM 'ye yönlendirir. Kullanıcılar, oturum açtığı VM 'den bağımsız olarak tutarlı ve tanıdık bir ortam gerektirdiğinden, ölçek kümesindeki sanal makinenin tüm örnekleri, bir Azure dosya paylaşımında veya bir ağ dosya sistemi (NFS) paylaşımında olduğu gibi paylaşılan bir ağ sürücüsü bağlamalarından oluşur. Kullanıcının paylaşılan çalışma alanı, normalde her bir örnek üzerine takılan paylaşılan dosya deposunda tutulur.

[GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json)üzerinde Ubuntu dsvm örnekleri ile bir ölçek kümesi oluşturan örnek bir Azure Resource Manager şablonu bulabilirsiniz. Azure Resource Manager şablonu için aynı konumda bir [parametre dosyası](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) örneği bulacaksınız.

Azure CLı 'de parametre dosyası değerlerini belirterek Azure Resource Manager şablondan ölçek kümesi oluşturabilirsiniz:

```azurecli-interactive
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az deployment group create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```

Yukarıdaki komutlarda şunları varsayalım:

* Ölçek kümesi örneğiniz için belirtilen değerlere sahip parametre dosyasının bir kopyası.
* Sanal makine örneği sayısı.
* Azure dosya paylaşımının işaretçileri.
* Her VM 'ye takılacak depolama hesabı için kimlik bilgileri.

Parametre dosyasına komutlarında yerel olarak başvurulur. Ayrıca, parametreleri satır içi olarak veya betikte komut istemi olarak geçirebilirsiniz.  

Önceki şablon, SSH ve jupi bağlantı noktasının ön uç ölçek kümesinden Ubuntu DSVMs arka uç havuzuna erişmesini sağlar. Bir kullanıcı olarak, bir Secure Shell (SSH) veya jupi üzerinde sanal makinede normal şekilde oturum açın. VM örnekleri dinamik olarak ölçeklendirilebilir veya daha fazla azaltılabilen için, herhangi bir durum bağlı Azure dosyaları paylaşımında kaydedilmelidir. Windows DSVMs havuzu oluşturmak için aynı yaklaşımı kullanabilirsiniz.

[Azure dosya paylaşımının yer aldığı betik](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) , GitHub 'Daki Azure Databilimencevm deposunda de mevcuttur. Betik, Azure dosyaları paylaşımından parametre dosyasındaki belirtilen bağlama noktasında takar. Komut dosyası, ilk kullanıcının giriş dizinindeki bağlı sürücüye yönelik yazılım bağlantıları da oluşturur. Azure dosya paylaşımındaki kullanıcıya özgü bir not defteri dizini, `$HOME/notebooks/remote` kullanıcıların Jupyter not defterlerini erişebilmeleri, çalıştırmaları ve kaydedebilmeleri için dizine geçici olarak bağlanır. Her kullanıcının jupi çalışma alanını Azure dosya paylaşımıyla göstermek için VM 'de ek kullanıcılar oluştururken aynı kuralı kullanabilirsiniz.

Sanal Makine Ölçek Kümeleri otomatik ölçeklendirmeyi destekler. Ne zaman ek örnekler oluşturulacağı ve örneklerin ne zaman Ölçeklendirilecek hakkında kurallar belirleyebilirsiniz. Örneğin, VM 'Ler hiç kullanılmayan bulut donanım kullanım maliyetlerine kaydedilecek şekilde sıfıra kadar ölçeği azaltabilirsiniz. Sanal makine ölçek kümelerinin belge sayfaları, [Otomatik ölçeklendirme](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md)için ayrıntılı adımlar sağlar.

## <a name="next-steps"></a>Sonraki adımlar

* [Ortak bir kimlik ayarlama](dsvm-common-identity.md)
* [Bulut kaynaklarına erişmek için kimlik bilgilerini güvenli bir şekilde depolayın](dsvm-secure-access-keys.md)