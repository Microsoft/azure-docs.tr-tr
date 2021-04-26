---
title: CLı kullanarak Market satın alma planı bilgilerini bulma ve kullanma
description: Market VM görüntüleri için yayımcı, teklif, SKU ve sürüm gibi görüntü URNs ve satın alma planı parametrelerini bulmak için Azure CLı 'yi nasıl kullanacağınızı öğrenin.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.collection: linux
ms.custom: contperf-fy21q3-portal, devx-track-azurecli
ms.openlocfilehash: be0535a49b47c45cad49abd1bf720b6347a660b8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484209"
---
# <a name="find-azure-marketplace-image-information-using-the-azure-cli"></a>Azure CLı kullanarak Azure Marketi görüntü bilgilerini bulma

Bu konu başlığında, Azure Marketi 'nde VM görüntülerini bulmak için Azure CLı 'nın nasıl kullanılacağı açıklanmaktadır. CLı, Kaynak Yöneticisi şablonları veya diğer araçlarla programlı bir şekilde VM oluştururken Market görüntüsü belirtmek için bu bilgileri kullanın.

Ayrıca, [Azure Marketi](https://azuremarketplace.microsoft.com/) veya  [Azure PowerShell](../windows/cli-ps-findimage.md)kullanarak kullanılabilir görüntülere ve tekliflere de gidebilirsiniz. 

## <a name="terminology"></a>Terminoloji

Azure 'da Market görüntüsü aşağıdaki özniteliklere sahiptir:

* **Yayımcı**: görüntüyü oluşturan kuruluş. Örnekler: Canonical, MicrosoftWindowsServer
* **Teklif**: Yayımcı tarafından oluşturulan ilgili görüntü grubunun adı. Örnekler: UbuntuServer, WindowsServer
* **SKU**: bir dağıtımın ana sürümü gibi bir teklifin örneği. Örnekler: 18,04-LTS, 2019-Datacenter
* **Sürüm**: BIR görüntü SKU 'sunun sürüm numarası. 

Bu değerler ayrı ayrı veya bir görüntü *urn*'si olarak geçirilebilir ve iki nokta üst üste (:) ile ayrılmış değerler birleştiren. Örneğin: *Yayımcı*:*teklif*:*SKU*:*Sürüm*. URN 'deki sürüm numarasını `latest` görüntünün en son sürümünü kullanacak şekilde değiştirebilirsiniz. 

Görüntü yayımcısı ek lisans ve satın alma koşulları sağlıyorsa, görüntüyü kullanabilmeniz için önce bunları kabul etmelisiniz.  Daha fazla bilgi için bkz. [Satınalma planı bilgilerini denetleme](#check-the-purchase-plan-information).



## <a name="list-popular-images"></a>Popüler görüntüleri listeleme

Azure Marketi 'nde popüler VM görüntülerinin listesini görmek için, seçeneği olmadan [az VM image List](/cli/azure/vm/image) komutunu çalıştırın `--all` . Örneğin, popüler görüntülerin önbelleğe alınmış bir listesini tablo biçiminde göstermek için aşağıdaki komutu çalıştırın:

```azurecli
az vm image list --output table
```

Çıktı, URN resmini içerir. *Ubuntults* gibi popüler görüntüler için oluşturulan kısaltılmış bir sürüm olan *urnalias* 'i de kullanabilirsiniz.

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
debian-10      Debian                  10                  Debian:debian-10:10:latest                                      Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-LVM               RedHat:RHEL:7-LVM:latest                                        RHEL                 latest
SLES           SUSE                    15                  SUSE:SLES:15:latest                                             SLES                 latest
UbuntuServer   Canonical               18.04-LTS           Canonical:UbuntuServer:18.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2019-Datacenter     MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest     Win2019Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

## <a name="find-specific-images"></a>Belirli görüntüleri bulma

Market 'te belirli bir VM görüntüsünü bulmak için `az vm image list` komutunu `--all` seçeneğiyle kullanın. Komutun bu sürümünün tamamlanabilmesi için bir süre sürer ve uzun çıkış döndürebilir, genellikle listeyi `--publisher` veya başka bir parametreyi filtreleyebilirsiniz. 

Örneğin, aşağıdaki komut tüm deki tekliflerini görüntüler ( `--all` anahtar olmadan, yalnızca ortak görüntülerin yerel önbelleğini arar):

```azurecli
az vm image list --offer Debian --all --output table 
```

Kısmi çıkış: 

```output
Offer                                    Publisher                         Sku                                      Urn                                                                                                   Version
---------------------------------------  --------------------------------  ---------------------------------------  ----------------------------------------------------------------------------------------------------  --------------
apache-solr-on-debian                    apps-4-rent                       apache-solr-on-debian                    apps-4-rent:apache-solr-on-debian:apache-solr-on-debian:1.0.0                                         1.0.0
atomized-h-debian10-v1                   atomizedinc1587939464368          hdebian10plan                            atomizedinc1587939464368:atomized-h-debian10-v1:hdebian10plan:1.0.0                                   1.0.0
atomized-h-debian9-v1                    atomizedinc1587939464368          hdebian9plan                             atomizedinc1587939464368:atomized-h-debian9-v1:hdebian9plan:1.0.0                                     1.0.0
atomized-r-debian10-v1                   atomizedinc1587939464368          rdebian10plan                            atomizedinc1587939464368:atomized-r-debian10-v1:rdebian10plan:1.0.0                                   1.0.0
atomized-r-debian9-v1                    atomizedinc1587939464368          rdebian9plan                             atomizedinc1587939464368:atomized-r-debian9-v1:rdebian9plan:1.0.0                                     1.0.0
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.7                         1.0.7
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.8                         1.0.8
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.9                         1.0.9
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.18                          1.0.18
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.19                          1.0.19
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.20                          1.0.20
apache-web-server-with-debian-10         cognosys                          apache-web-server-with-debian-10         cognosys:apache-web-server-with-debian-10:apache-web-server-with-debian-10:1.2019.1008                1.2019.1008
docker-ce-with-debian-10                 cognosys                          docker-ce-with-debian-10                 cognosys:docker-ce-with-debian-10:docker-ce-with-debian-10:1.2019.0710                                1.2019.0710
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201602010                                                                       8.0.201602010
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201603020                                                                       8.0.201603020
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201604050                                                                       8.0.201604050
...
```


## <a name="look-at-all-available-images"></a>Tüm kullanılabilir görüntülere bakın
 
Bir konumda görüntü bulmanın diğer bir yolu da [az VM image List-Publishers](/cli/azure/vm/image), [az VM Image List-tekliflerini](/cli/azure/vm/image)ve sırasıyla, az VM image [list-SKU](/cli/azure/vm/image) komutlarının çalıştırılacağı bir yoldur. Bu komutlarla, şu değerleri belirlersiniz:

1. Bir konumun görüntü yayımcılarını listeleyin. Bu örnekte, *Batı ABD* bölgesine bakıyoruz.
    
    ```azurecli
    az vm image list-publishers --location westus --output table
    ```

1. Belirli bir yayımcı varsa yayımcının tekliflerini listeleyin. Bu örnekte, yayımcı olarak *kurallı* bir ekliyoruz.
    
    ```azurecli
    az vm image list-offers --location westus --publisher Canonical --output table
    ```

1. Belirli bir teklif varsa SKU’larını listeleyin. Bu örnekte, teklif olarak *Ubuntuserver* ekleyeceğiz.
    ```azurecli
    az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
    ```

1. Belirli bir yayımcı, teklif ve SKU için görüntünün tüm sürümlerini gösterir. Bu örnekte, SKU olarak *18,04-LTS* ekleyeceğiz.

    ```azurecli
    az vm image list \
        --location westus \
        --publisher Canonical \  
        --offer UbuntuServer \    
        --sku 18.04-LTS \
        --all --output table
    ```

`--image` [Az VM Create](/cli/azure/vm) komutuyla bir VM oluşturduğunuzda, urn sütununun bu değerini parametresiyle geçirin. Ayrıca, yalnızca görüntünün en son sürümünü kullanmak için URN 'deki sürüm numarasını "en son" ile değiştirebilirsiniz. 

Bir sanal makineyi Kaynak Yöneticisi şablonuyla dağıtırsanız, görüntü parametrelerini özelliklerde tek tek ayarlarsınız `imageReference` . Bkz. [şablon başvurusu](/azure/templates/microsoft.compute/virtualmachines).


## <a name="check-the-purchase-plan-information"></a>Satın alma planı bilgilerini denetleyin

Azure Marketi 'ndeki bazı sanal makine görüntülerinin, programlama yoluyla dağıtmadan önce kabul etmeniz gereken ek lisans ve satın alma koşulları vardır.  

Bu tür bir görüntüden bir VM dağıtmak için, her abonelik için bir kez ilk kez kullandığınızda görüntünün koşullarını kabul etmeniz gerekir. Ayrıca, bu görüntüden bir VM dağıtmak için *satın alma planı* parametreleri belirtmeniz gerekir

Bir görüntünün satın alma planı bilgilerini görüntülemek için [az VM Image Show](/cli/azure/image) komutunu görüntünün urn 'si ile çalıştırın. `plan`Çıktıda özelliği yoksa `null` , görüntü, programlı dağıtımdan önce kabul etmeniz gereken koşulları içerir.

Örneğin, kurallı Ubuntu Server 18,04 LTS görüntüsünün ek terimleri yoktur, çünkü `plan` bilgiler şu şekilde olur `null` :

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

Çıkış:

```output
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

BitNami görüntüsü tarafından Minbbitmq sertifikalı benzer bir komutun çalıştırılması aşağıdaki `plan` özellikleri gösterir: `name` , `product` ve `publisher` . (Bazı görüntülerin bir özelliği de vardır `promotion code` .) 

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
Çıkış:

```output
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

Bu görüntüyü dağıtmak için, bu görüntüyü kullanarak bir VM dağıtırken koşulları kabul etmeniz ve satın alma planı parametrelerini sağlamanız gerekir.

## <a name="accept-the-terms"></a>Koşulları kabul edin

Lisans koşullarını görüntülemek ve kabul etmek için [az VM Image Accept-terms](/cli/azure/vm/image/terms) komutunu kullanın. Koşulları kabul ettiğinizde, aboneliğinizde programlı dağıtımı etkinleştirirsiniz. Her görüntü için abonelik başına koşulları kabul etmeniz yeterlidir. Örnek:

```azurecli
az vm image terms show --urn bitnami:rabbitmq:rabbitmq:latest
``` 

Çıktı, `licenseTextLink` lisans koşullarına bir içerir ve değerinin şu olduğunu gösterir `accepted` `true` :

```output
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

Koşulları kabul etmek için şunu yazın:

```azurecli
az vm image terms accept --urn bitnami:rabbitmq:rabbitmq:latest
``` 

## <a name="deploy-a-new-vm-using-the-image-parameters"></a>Görüntü parametrelerini kullanarak yeni bir VM dağıtma

Görüntüyle ilgili bilgilerle, komutunu kullanarak dağıtımı yapabilirsiniz `az vm create` . 

En son Ubuntu Server 18,04 görüntüsü gibi plan bilgilerine sahip olmayan bir görüntü dağıtmak için URN 'yi geçirin `--image` :

```azurecli-interactive
az group create --name myURNVM --location westus
az vm create \
   --resource-group myURNVM \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image Canonical:UbuntuServer:18.04-LTS:latest 
```


BitNami görüntüsü tarafından verilen Kbbitmq sertifikalı gibi satın alma planı parametrelerine sahip bir görüntü için, URN 'yi geçirirsiniz `--image` ve satın alma planı parametrelerini de sağlarsınız:

```azurecli
az group create --name myPurchasePlanRG --location westus

az vm create \
   --resource-group myPurchasePlanRG \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image bitnami:rabbitmq:rabbitmq:latest \
   --plan-name rabbitmq \
   --plan-product rabbitmq \
   --plan-publisher bitnami
```

Görüntü koşullarını kabul etme hakkında bir ileti alırsanız, bölümü gözden geçirin [Koşulları kabul edin](#accept-the-terms). Çıkışının, `az vm image accept-terms` görüntünün şartlarını kabul ettiğinizi gösteren değeri döndürdüğünden emin olun `"accepted": true,` .


## <a name="using-an-existing-vhd-with-purchase-plan-information"></a>Satın alma planı bilgileriyle mevcut bir VHD 'YI kullanma

Ücretli bir Azure Marketi görüntüsü kullanılarak oluşturulmuş bir VM 'den mevcut bir VHD varsa, bu VHD 'den yeni bir VM oluşturduğunuzda satın alma planı bilgilerini sağlamanız gerekebilir. 

Hala orijinal VM veya aynı Market görüntüsü kullanılarak oluşturulmuş başka bir VM varsa, [az VM Get-instance-View](/cli/azure/vm#az_vm_get_instance_view)kullanarak bu bilgisayardan plan adı, yayımcı ve ürün bilgilerini alabilirsiniz. Bu örnek *Myresourcegroup* kaynak grubundaki *myvm* adlı bir VM 'yi alır ve ardından satın alma planı bilgilerini görüntüler.

```azurepowershell-interactive
az vm get-instance-view -g myResourceGroup -n myVM --query plan
```

Özgün VM silinmeden önce plan bilgilerini almadıysanız, bir [destek isteği](https://ms.portal.azure.com/#create/Microsoft.Support)dosyası gönderebilirsiniz. Bu, VM adı, abonelik KIMLIĞI ve silme işleminin zaman damgasına ihtiyaç duyar.

Plan bilgilerine sahip olduktan sonra, `--attach-os-disk` VHD 'yi belirtmek için parametresini kullanarak yenı VM 'yi oluşturabilirsiniz.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myNewVM \
  --nics myNic \
  --size Standard_DS1_v2 --os-type Linux \
  --attach-os-disk myVHD \
  --plan-name planName \
  --plan-publisher planPublisher \
  --plan-product planProduct 
```


## <a name="next-steps"></a>Sonraki adımlar
Görüntü bilgilerini kullanarak hızlı bir şekilde sanal makine oluşturmak için bkz. [Azure CLI Ile Linux VM 'Leri oluşturma ve yönetme](tutorial-manage-vm.md).
