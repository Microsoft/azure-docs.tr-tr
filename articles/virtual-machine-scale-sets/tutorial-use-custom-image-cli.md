---
title: Öğretici-Azure CLı ile bir ölçek kümesinde özel bir VM görüntüsü kullanma
description: Azure CLI kullanarak, sanal makine ölçek kümesini dağıtmak için kullanabileceğiniz bir özel sanal makine görüntüsünün nasıl oluşturulacağını öğrenin
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: a9a4abe550da4f0438f875127b3b689045c06e6f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763010"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Öğretici: Azure CLI ile sanal makine ölçek kümeleri için özel görüntü oluşturma ve kullanma
Ölçek kümesi oluşturduğunuzda, sanal makine örnekleri dağıtılırken kullanılacak bir görüntü belirtirsiniz. Sanal makine örnekleri dağıtıldıktan sonraki görev sayısını azaltmak için özel bir sanal makine görüntüsünü kullanabilirsiniz. Bu özel sanal makine görüntüsü, gerekli uygulama yüklemelerini veya yapılandırmalarını içerir. Ölçek kümesinde oluşturulan tüm sanal makine örnekleri, özel sanal makine görüntüsünü kullanır ve uygulama trafiğinizi sunmaya hazır olur. Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Paylaşılan Görüntü Galerisi Oluşturma
> * Özel bir görüntü tanımı oluşturma
> * Görüntü sürümü oluşturma
> * Özelleştirilmiş görüntüden ölçek kümesi oluşturma
> * Resim galerisini paylaşma


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.4.0 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="overview"></a>Genel Bakış

[Paylaşılan görüntü Galerisi](../virtual-machines/shared-image-galleries.md) , kuruluşunuz genelinde özel görüntü paylaşımını basitleştirir. Özel görüntüler market görüntüleri gibidir, ancak bunları kendiniz oluşturursunuz. Özel görüntüler, uygulamaları, uygulama yapılandırmalarını ve diğer işletim sistemi yapılandırmalarını önceden yükleme gibi yapılandırmaları önyüklemek için kullanılabilir. 

Paylaşılan görüntü Galerisi, özel VM görüntülerinizi başkalarıyla paylaşmanıza olanak sağlar. Hangi görüntüleri paylaşmak istediğinizi, içinde hangi bölgelerin kullanılabilir olmasını istediğinizi ve bunları ile paylaşmak istediğinizi seçin. 

## <a name="create-and-configure-a-source-vm"></a>Kaynak sanal makine oluşturma ve yapılandırma

İlk olarak, [az group create](/cli/azure/group) ile bir kaynak grubu oluşturun ve sonra [az vm create](/cli/azure/vm) ile bir sanal makine oluşturun. Bu VM daha sonra görüntü kaynağı olarak kullanılır. Aşağıdaki örnek, *myResourceGroup* adlı kaynak grubunda *myVM* adlı bir sanal makine oluşturur:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

> [!IMPORTANT]
> VM 'nizin **kimliği** [az VM Create](/cli/azure/vm) komutunun çıktısında gösterilir. Bu öğreticinin ilerleyen kısımlarında kullanabilmek için bu nedenini bildirmeden Safe 'i kopyalayın.

VM 'nizin genel IP adresi, [az VM Create](/cli/azure/vm) komutunun çıktısında de gösterilir. Aşağıdaki adımları uygulayarak sanal makinenizin genel IP adresinde SSH oturumu açın:

```console
ssh azureuser@<publicIpAddress>
```

Şimdi sanal makinenizi özelleştirmek için bir temel web sunucusu yükleyelim. Ölçek kümesindeki sanal makine örneği dağıtılacağı zaman, bir web uygulamasını çalıştırmak için gerekli tüm paketleri içerir. Aşağıdaki adımları uygulayarak `apt-get` komutunu kullanıp *NGINX*’i yükleyin:

```bash
sudo apt-get install -y nginx
```

İşiniz bittiğinde, `exit` SSH bağlantısının bağlantısını kesmek için yazın.

## <a name="create-an-image-gallery"></a>Görüntü galerisi oluşturma 

Görüntü Galerisi, görüntü paylaşımını etkinleştirmek için kullanılan birincil kaynaktır. 

Galeri adı için izin verilen karakterler büyük veya küçük harflerden, rakamlardan, noktalardan ve noktalardan oluşur. Galeri adı tire içeremez.   Galeri adları, aboneliğiniz dahilinde benzersiz olmalıdır. 

[Az Sig Create](/cli/azure/sig#az_sig_create)kullanarak bir görüntü galerisi oluşturun. Aşağıdaki örnek, *Doğu ABD* Içinde *mygallerrg* adlı Galeri adlı bir kaynak grubu ve *MyGallery* adlı bir galeri oluşturur.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Görüntü tanımı oluşturma

Görüntü tanımları görüntüler için bir mantıksal gruplama oluşturur. Bunlar içinde oluşturulan görüntü sürümleri hakkındaki bilgileri yönetmek için kullanılır. 

Görüntü tanımı adları büyük veya küçük harflerden, rakamlardan, noktalardan, çizgilerden ve noktalardan oluşabilir. 

Görüntü tanımınızın doğru türde olduğundan emin olun. VM 'yi genelleştirdiğinizde (Windows için Sysprep veya Linux için waagent-deprovision), kullanarak genelleştirilmiş bir görüntü tanımı oluşturmanız gerekir `--os-state generalized` . VM 'yi mevcut kullanıcı hesaplarını kaldırmadan kullanmak istiyorsanız, kullanarak özel bir görüntü tanımı oluşturun `--os-state specialized` .

Bir görüntü tanımı için belirtebileceğiniz değerler hakkında daha fazla bilgi için bkz. [görüntü tanımları](../virtual-machines/shared-image-galleries.md#image-definitions).

Galeride [az Sig Image-Definition Create](/cli/azure/sig/image-definition#az_sig_image_definition_create)kullanarak bir görüntü tanımı oluşturun.

Bu örnekte, görüntü tanımı *Myımagedefinition* olarak adlandırılır ve [özelleştirilmiş](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) bir Linux işletim sistemi görüntüsü içindir. Windows işletim sistemi kullanan görüntülerin tanımını oluşturmak için kullanın `--os-type Windows` . 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

> [!IMPORTANT]
> Görüntü tanımınızın **kimliği** , komutun çıktısında gösterilir. Bu öğreticinin ilerleyen kısımlarında kullanabilmek için bu nedenini bildirmeden Safe 'i kopyalayın.


## <a name="create-the-image-version"></a>Görüntü sürümü oluşturma

[Az Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az_sig_image_version_create)kullanarak VM 'den bir görüntü sürümü oluşturun.  

Görüntü sürümü için izin verilen karakterler rakamlardan ve dönemlerdir. Sayılar 32 bitlik bir tamsayı aralığında olmalıdır. Biçim: *MajorVersion*. *MinorVersion*. *Düzeltme Eki*.

Bu örnekte, görüntüimizin sürümü *1.0.0* ' dir ve *Orta Güney ABD* bölgesinde 1 çoğaltma ve *Doğu ABD 2* bölgesinde 1 çoğaltma oluşturacağız. Çoğaltma bölgeleri, kaynak VM 'nin bulunduğu bölgeyi içermelidir.

Bu örnekteki değerini, `--managed-image` önceki ADıMDA sanal MAKINENIZIN kimliğiyle değiştirin.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1" \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Farklı bir görüntü sürümü oluşturmak için aynı yönetilen görüntüyü kullanabilmeniz için görüntü sürümünün oluşturulması ve çoğaltılması tamamen bitmesini beklemeniz gerekir.
>
> Görüntü `--storage-account-type  premium_lrs` sürümünü oluştururken ekleyerek görüntünüzü bir ekleme veya [bölge yedekli depolama](../storage/common/storage-redundancy.md) ile Premium depolamada da saklayabilirsiniz `--storage-account-type  standard_zrs` .
>




## <a name="create-a-scale-set-from-the-image"></a>Görüntüden ölçek kümesi oluşturma
Kullanarak özelleştirilmiş görüntüden bir ölçek kümesi oluşturun [`az vmss create`](/cli/azure/vmss#az_vmss_create) . 

[`az vmss create`](/cli/azure/vmss#az_vmss_create)Görüntünün özelleşmiş bir görüntü olduğunu göstermek için--özelleşmiş parametresini kullanarak ölçek kümesi oluşturun. 

`--image`Kullanılabilir görüntünün en son sürümünden ölçek kümesi örnekleri oluşturmak için görüntü tanımı kimliği ' ni kullanın. Ayrıca, için görüntü sürümü KIMLIĞINI sağlayarak belirli bir sürümden ölçek kümesi örnekleri de oluşturabilirsiniz `--image` . 

Daha önce oluşturduğumuz *Myımagedefinition* resminin en son sürümünü *myScaleSet* adlı bir ölçek kümesi oluşturun.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```

Tüm ölçek kümesi kaynaklarının ve VM'lerin oluşturulup yapılandırılması birkaç dakika sürer.


## <a name="test-your-scale-set"></a>Ölçek kümenizi test etme
Trafiğin ölçek kümenize ulaşmasına izin vermek ve web sunucusunun düzgün çalıştığını doğrulamak için [az network lb rule create](/cli/azure/network/lb/rule) komutunu kullanarak bir yük dengeleyici kuralı oluşturun. Aşağıdaki örnek, *80* numaralı *TCP* bağlantı noktasında trafiğe izin veren, *myLoadBalancerRuleWeb* adlı bir kural oluşturur:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Ölçek kümenizi çalışır halde görmek için [az network public-ip show](/cli/azure/network/public-ip) komutunu kullanarak yük dengeleyicinizin genel IP adresini alın. Aşağıdaki örnek, ölçek kümesinin bir parçası olarak oluşturulan *myScaleSetLBPublicIP* için IP adresini alır:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Genel IP adresini bir web tarayıcınıza yazın. Aşağıdaki örnekte gösterildiği gibi varsayılan NGINX web sayfası görüntülenir:

![Özel sanal makine görüntüsünden çalıştırılan Nginx](media/tutorial-use-custom-image-cli/default-nginx-website.png)



## <a name="share-the-gallery"></a>Galeriyi paylaşma

Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak, abonelikler arasında görüntü paylaşabilirsiniz. Görüntüleri galeride, görüntü tanımında veya görüntü sürümünde paylaşabilirsiniz. Abonelikler arasında bile, görüntü sürümü üzerinde okuma izinlerine sahip olan tüm kullanıcılar, görüntü sürümünü kullanarak bir sanal makine dağıtacaktır.

Galeri düzeyindeki diğer kullanıcılarla paylaşmanızı öneririz. Galerinizin nesne KIMLIĞINI almak için [az SIG Show](/cli/azure/sig#az_sig_show)' ı kullanın.

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Nesne KIMLIĞINI bir e-posta adresi ile birlikte kullanın ve bir kullanıcıya paylaşılan görüntü galerisine erişim sağlamak için [az role atama oluştur](/cli/azure/role/assignment#az_role_assignment_create) ' u kullanın. `<email-address>`Ve `<gallery iD>` bilgilerini kendi bilgileriniz ile değiştirin.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Azure RBAC kullanarak kaynakları paylaşma hakkında daha fazla bilgi için bkz. Azure [CLI kullanarak Azure rol atamaları ekleme veya kaldırma](../role-based-access-control/role-assignments-cli.md).


## <a name="clean-up-resources"></a>Kaynakları temizleme
Ölçek kümenizi ve ek kaynaklarınızı kaldırmak için [az group delete](/cli/azure/group) komutunu kullanarak kaynak grubunu ve bu kaynak grubunun tüm kaynaklarını silin. `--no-wait` parametresi işlemin tamamlanmasını beklemeden denetimi komut istemine döndürür. `--yes` parametresi kaynakları ek bir komut istemi olmadan silmek istediğinizi onaylar.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, Azure CLI ile ölçek kümeleriniz için özel sanal makine görüntüsü oluşturma ve kullanma işleminin nasıl yapılacağını öğrendiniz:

> [!div class="checklist"]
> * Paylaşılan Görüntü Galerisi Oluşturma
> * Özel bir görüntü tanımı oluşturma
> * Görüntü sürümü oluşturma
> * Özelleştirilmiş görüntüden ölçek kümesi oluşturma
> * Resim galerisini paylaşma

Uygulamaların ölçek kümenize nasıl dağıtılacağını öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Ölçek kümelerinize uygulama dağıtma](tutorial-install-apps-cli.md)