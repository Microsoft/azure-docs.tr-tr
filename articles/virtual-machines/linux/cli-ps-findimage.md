---
title: Azure CLı ile Linux VM görüntülerini seçme
description: Azure CLı kullanarak Market VM görüntüleri için yayımcı, teklif, SKU ve sürümü belirleme hakkında bilgi edinin.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/25/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e4dd51640c4eeda2ec99c14812a534ee506faeda
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036860"
---
# <a name="find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Azure CLı ile Azure Marketi 'nde Linux VM görüntülerini bulma

Bu konu başlığında, Azure Marketi 'nde VM görüntülerini bulmak için Azure CLı 'nın nasıl kullanılacağı açıklanmaktadır. CLı, Kaynak Yöneticisi şablonları veya diğer araçlarla programlı bir şekilde VM oluştururken Market görüntüsü belirtmek için bu bilgileri kullanın.

Ayrıca, [Azure Marketi](https://azuremarketplace.microsoft.com/) storefront, [Azure Portal](https://portal.azure.com)veya [Azure PowerShell](../windows/cli-ps-findimage.md)kullanarak kullanılabilir görüntülere ve tekliflere de gözatın. 

En son [Azure CLI](/cli/azure/install-azure-cli) 'yi yüklediğinizden ve bir Azure hesabında (`az login`) oturum açtığınızdan emin olun.

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="list-popular-images"></a>Popüler görüntüleri listeleme

Azure Marketi 'nde popüler VM görüntülerinin listesini görmek için, `--all` seçeneği olmadan [az VM image List](/cli/azure/vm/image) komutunu çalıştırın. Örneğin, popüler görüntülerin önbelleğe alınmış bir listesini tablo biçiminde göstermek için aşağıdaki komutu çalıştırın:

```azurecli
az vm image list --output table
```

Çıktı URN ( *urn* sütunundaki değer) resmini içerir. Bu popüler Market görüntülerinden birine sahip bir VM oluştururken alternatif olarak, *Ubuntults*gibi kısaltılmış bir form olan *urnalias*'i de belirtebilirsiniz.

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-RAW               RedHat:RHEL:7-RAW:latest                                        RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>Belirli görüntüleri bulma

Market 'te belirli bir VM görüntüsünü bulmak için, `--all` seçeneğiyle `az vm image list` komutunu kullanın. Komutun bu sürümünün tamamlanabilmesi için bir süre sürer ve uzun çıkış döndürebilir, genellikle listeyi `--publisher` veya başka bir parametreye göre filtrelemeniz gerekir. 

Örneğin, aşağıdaki komut tüm deki tekliflerini görüntüler (`--all` anahtar olmadan, yalnızca ortak görüntülerin yerel önbelleğinde arama yapar):

```azurecli
az vm image list --offer Debian --all --output table 

```

Kısmi çıkış: 

```
Offer              Publisher    Sku                  Urn                                                    Version
-----------------  -----------  -------------------  -----------------------------------------------------  --------------
Debian             credativ     7                    credativ:Debian:7:7.0.201602010                        7.0.201602010
Debian             credativ     7                    credativ:Debian:7:7.0.201603020                        7.0.201603020
Debian             credativ     7                    credativ:Debian:7:7.0.201604050                        7.0.201604050
Debian             credativ     7                    credativ:Debian:7:7.0.201604200                        7.0.201604200
Debian             credativ     7                    credativ:Debian:7:7.0.201606280                        7.0.201606280
Debian             credativ     7                    credativ:Debian:7:7.0.201609120                        7.0.201609120
Debian             credativ     7                    credativ:Debian:7:7.0.201611020                        7.0.201611020
Debian             credativ     7                    credativ:Debian:7:7.0.201701180                        7.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201602010                        8.0.201602010
Debian             credativ     8                    credativ:Debian:8:8.0.201603020                        8.0.201603020
Debian             credativ     8                    credativ:Debian:8:8.0.201604050                        8.0.201604050
Debian             credativ     8                    credativ:Debian:8:8.0.201604200                        8.0.201604200
Debian             credativ     8                    credativ:Debian:8:8.0.201606280                        8.0.201606280
Debian             credativ     8                    credativ:Debian:8:8.0.201609120                        8.0.201609120
Debian             credativ     8                    credativ:Debian:8:8.0.201611020                        8.0.201611020
Debian             credativ     8                    credativ:Debian:8:8.0.201701180                        8.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201703150                        8.0.201703150
Debian             credativ     8                    credativ:Debian:8:8.0.201704110                        8.0.201704110
Debian             credativ     8                    credativ:Debian:8:8.0.201704180                        8.0.201704180
Debian             credativ     8                    credativ:Debian:8:8.0.201706190                        8.0.201706190
Debian             credativ     8                    credativ:Debian:8:8.0.201706210                        8.0.201706210
Debian             credativ     8                    credativ:Debian:8:8.0.201708040                        8.0.201708040
Debian             credativ     8                    credativ:Debian:8:8.0.201710090                        8.0.201710090
Debian             credativ     8                    credativ:Debian:8:8.0.201712040                        8.0.201712040
Debian             credativ     8                    credativ:Debian:8:8.0.201801170                        8.0.201801170
Debian             credativ     8                    credativ:Debian:8:8.0.201803130                        8.0.201803130
Debian             credativ     8                    credativ:Debian:8:8.0.201803260                        8.0.201803260
Debian             credativ     8                    credativ:Debian:8:8.0.201804020                        8.0.201804020
Debian             credativ     8                    credativ:Debian:8:8.0.201804150                        8.0.201804150
Debian             credativ     8                    credativ:Debian:8:8.0.201805160                        8.0.201805160
Debian             credativ     8                    credativ:Debian:8:8.0.201807160                        8.0.201807160
Debian             credativ     8                    credativ:Debian:8:8.0.201901221                        8.0.201901221
...
```

`--location`, `--publisher`ve `--sku` seçenekleriyle benzer filtreler uygulayın. Tüm Delesel görüntüleri bulmak için `--offer Deb` arama gibi bir filtrede kısmi eşleşmeler gerçekleştirebilirsiniz.

`--location` seçeneği ile belirli bir konum belirtmezseniz, varsayılan konum için değerler döndürülür. (`az configure --defaults location=<location>`çalıştırarak farklı bir varsayılan konum ayarlayın.)

Örneğin, aşağıdaki komut Batı Avrupa konumdaki tüm 8 SKU 'Larını listeler:

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

Kısmi çıkış:

```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
Debian   credativ     8                  credativ:Debian:8:8.0.201710090                  8.0.201710090
Debian   credativ     8                  credativ:Debian:8:8.0.201712040                  8.0.201712040
Debian   credativ     8                  credativ:Debian:8:8.0.201801170                  8.0.201801170
Debian   credativ     8                  credativ:Debian:8:8.0.201803130                  8.0.201803130
Debian   credativ     8                  credativ:Debian:8:8.0.201803260                  8.0.201803260
Debian   credativ     8                  credativ:Debian:8:8.0.201804020                  8.0.201804020
Debian   credativ     8                  credativ:Debian:8:8.0.201804150                  8.0.201804150
Debian   credativ     8                  credativ:Debian:8:8.0.201805160                  8.0.201805160
Debian   credativ     8                  credativ:Debian:8:8.0.201807160                  8.0.201807160
Debian   credativ     8                  credativ:Debian:8:8.0.201901221                  8.0.201901221
...
```

## <a name="navigate-the-images"></a>Görüntülerde gezin
 
Bir konumda görüntü bulmanın diğer bir yolu da [az VM image List-Publishers](/cli/azure/vm/image), [az VM Image List-tekliflerini](/cli/azure/vm/image)ve sırasıyla, az VM image [list-SKU](/cli/azure/vm/image) komutlarının çalıştırılacağı bir yoldur. Bu komutlarla, şu değerleri belirlersiniz:

1. Görüntü yayımcılarını listeleyin.
2. Belirli bir yayımcı varsa yayımcının tekliflerini listeleyin.
3. Belirli bir teklif varsa SKU’larını listeleyin.

Daha sonra, seçilen bir SKU için, dağıtılacak bir sürüm seçebilirsiniz.

Örneğin, aşağıdaki komut Batı ABD konumdaki görüntü yayımcılarını listeler:

```azurecli
az vm image list-publishers --location westus --output table
```

Kısmi çıkış:

```
Location    Name
----------  ----------------------------------------------------
westus      128technology
westus      1e
westus      4psa
westus      5nine-software-inc
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      accessdata-group
westus      accops
westus      Acronis
westus      Acronis.Backup
westus      actian-corp
westus      actian_matrix
westus      actifio
westus      activeeon
westus      advantech-webaccess
westus      aerospike
westus      affinio
westus      aiscaler-cache-control-ddos-and-url-rewriting-
westus      akamai-technologies
westus      akumina
...
```

Belirli bir yayımcının tekliflerini bulmak için bu bilgileri kullanın. Örneğin, Batı ABD konumdaki *kurallı* yayımcı için `azure vm image list-offers`çalıştırarak teklifleri bulun. Aşağıdaki örnekte olduğu gibi konumu ve yayımcıyı geçirin:

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

Çıktı:

```
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
```
Batı ABD bölgesinde, kurallı olarak *Ubuntuserver* teklifini Azure üzerinde yayımladığında görürsünüz. Peki bunların SKU’su ne? Bu değerleri almak için `azure vm image list-skus` çalıştırın ve bulduğunuz konum, yayımcı ve teklifi ayarlayın:

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

Çıktı:

```
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-LTS
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      18.04-DAILY-LTS
westus      18.04-LTS
westus      18.10
westus      18.10-DAILY
westus      19.04-DAILY
```

Son olarak, istediğiniz SKU 'nun belirli bir sürümünü bulmak için `az vm image list` komutunu kullanın, örneğin, *18,04-LTS*:

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 18.04-LTS --all --output table
```

Kısmi çıkış:

```
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201804262  18.04.201804262
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805170  18.04.201805170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805220  18.04.201805220
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806130  18.04.201806130
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806170  18.04.201806170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201807240  18.04.201807240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808060  18.04.201808060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808080  18.04.201808080
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808140  18.04.201808140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808310  18.04.201808310
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201809110  18.04.201809110
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810030  18.04.201810030
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810240  18.04.201810240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810290  18.04.201810290
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201811010  18.04.201811010
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812031  18.04.201812031
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812040  18.04.201812040
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812060  18.04.201812060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901140  18.04.201901140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901220  18.04.201901220
...
```

Artık, URN değerini göz önünde bulundurularak kullanmak istediğiniz görüntüyü tam olarak seçebilirsiniz. [Az VM Create](/cli/azure/vm) komutuyla bir VM oluşturduğunuzda, bu değeri `--image` parametresiyle geçirin. İsteğe bağlı olarak URN 'deki sürüm numarasını "en son" ile değiştirebileceğinizi unutmayın. Bu sürüm, her zaman görüntünün en son sürümüdür. 

Bir sanal makineyi Kaynak Yöneticisi şablonuyla dağıtırsanız, görüntü parametrelerini `imageReference` özelliklerinde ayrı ayrı ayarlarsınız. Bkz. [şablon başvurusu](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Plan özelliklerini görüntüle

Bir görüntünün satın alma planı bilgilerini görüntülemek için [az VM Image Show](/cli/azure/image) komutunu çalıştırın. Çıktıda `plan` özelliği `null`değilse, görüntü, programlı dağıtımdan önce kabul etmeniz gereken koşullara sahiptir.

Örneğin, kurallı Ubuntu Server 18,04 LTS görüntüsünün ek terimleri yoktur, çünkü `plan` bilgileri `null`.

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

Çıktı:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/18.04-LTS/Versions/18.04.201901220",
  "location": "westus",
  "name": "18.04.201901220",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

BitNami görüntüsü tarafından Minbbitmq sertifikalı benzer bir komut çalıştırmak, şu `plan` özelliklerini gösterir: `name`, `product`ve `publisher`. (Bazı resimlerde de `promotion code` özelliği vardır.) Bu görüntüyü dağıtmak için, koşulları kabul etmek ve programlı dağıtımı etkinleştirmek için aşağıdaki bölümlere bakın.

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
Çıktı:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1901151016",
  "location": "westus",
  "name": "3.7.1901151016",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

### <a name="accept-the-terms"></a>Koşulları kabul edin

Lisans koşullarını görüntülemek ve kabul etmek için [az VM Image Accept-terms](/cli/azure/vm/image?) komutunu kullanın. Koşulları kabul ettiğinizde, aboneliğinizde programlı dağıtımı etkinleştirirsiniz. Her görüntü için abonelik başına koşulları kabul etmeniz yeterlidir. Örneğin:

```azurecli
az vm image accept-terms --urn bitnami:rabbitmq:rabbitmq:latest
``` 

Çıktı, lisans koşullarına `licenseTextLink` içerir ve `accepted` değerinin `true`olduğunu gösterir:

```
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2019-01-25T20:37:49.937096Z",
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

### <a name="deploy-using-purchase-plan-parameters"></a>Satın alma planı parametrelerini kullanarak dağıtın

Görüntü koşullarını kabul ettikten sonra, abonelikte bir VM dağıtabilirsiniz. `az vm create` komutunu kullanarak görüntüyü dağıtmak için, görüntü için bir URN 'ye ek olarak satın alma planına yönelik parametreler sağlayın. Örneğin, Bıbbitmq sertifikalı bir sanal makineyi BitNami görüntüsü ile dağıtmak için:

```azurecli
az group create --name myResourceGroupVM --location westus

az vm create --resource-group myResourceGroupVM --name myVM --image bitnami:rabbitmq:rabbitmq:latest --plan-name rabbitmq --plan-product rabbitmq --plan-publisher bitnami
```

## <a name="next-steps"></a>Sonraki adımlar
Görüntü bilgilerini kullanarak hızlı bir şekilde sanal makine oluşturmak için bkz. [Azure CLI Ile Linux VM 'Leri oluşturma ve yönetme](tutorial-manage-vm.md).
