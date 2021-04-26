---
title: 'Öğretici: Azure CLı kullanarak çapraz bölge yük dengeleyici oluşturma'
titleSuffix: Azure Load Balancer
description: Azure CLı kullanarak bir çapraz bölge Azure Load Balancer dağıtmaya yönelik Bu öğreticiye başlayın.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 03/04/2021
ms.openlocfilehash: ca4134ff25dc9915f256b5a7bdd9404021b60a8e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791922"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-cli"></a>Öğretici: Azure CLı kullanarak çapraz bölge Azure Load Balancer oluşturma

Çapraz bölge yük dengeleyici, bir hizmetin birden çok Azure bölgesinde küresel olarak kullanılabilmesini sağlar. Bir bölge başarısız olursa, trafik sonraki en iyi sağlıklı bölgesel yük dengeleyiciye yönlendirilir.  

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bölgeler arası yük dengeleyici oluşturun.
> * Yük dengeleyici kuralı oluşturun.
> * İki bölgesel yük dengeleyicileri içeren bir arka uç havuzu oluşturun.
> * Yük dengeleyiciyi test edin.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği.
- İki farklı Azure bölgesinde dağıtılan arka uç havuzlarıyla iki **Standart** SKU Azure Yük dengeleyicileri.
    - Bir bölgesel standart yük dengeleyici ve arka uç havuzları için sanal makineler oluşturma hakkında bilgi için bkz. [hızlı başlangıç: Azure CLI kullanarak VM 'lerin yükünü dengelemek için genel yük dengeleyici oluşturma](quickstart-load-balancer-standard-public-cli.md).
        - Bir **-R1** ve **-R2** ile her bir bölgedeki yük dengeleyiciler ve sanal makinelerin adını ekleyin. 
- Azure CLı yerel olarak veya Azure Cloud Shell yüklendi.

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu hızlı başlangıç, Azure CLı sürüm 2.0.28 veya üstünü gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Azure CLI'da oturum açma

Azure CLı 'da oturum açın:

```azurecli-interactive
az login
```

## <a name="create-cross-region-load-balancer"></a>Çapraz bölge yük dengeleyici oluştur

Bu bölümde, bir çapraz bölge yük dengeleyici, genel IP adresi ve yük dengeleme kuralı oluşturacaksınız.

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

[Az Group Create](/cli/azure/group#az_group_create)ile bir kaynak grubu oluşturun:

* **Myresourcegrouplb-CR** adında.
* **Westus** konumunda.

```azurecli-interactive
  az group create \
    --name myResourceGroupLB-CR \
    --location westus
```

### <a name="create-the-load-balancer-resource"></a>Yük dengeleyici kaynağı oluşturma

[Az Network Region-lb Create](/cli/azure/network/cross-region-lb#az_network_cross_region_lb_create)ile bir çapraz bölge yük dengeleyici oluşturun:

* **Myloadbalancer-CR** adında.
* **Myön uç-CR** adlı bir ön uç Havuzu.
* **Mybackendpool-CR** adlı bir arka uç Havuzu.

```azurecli-interactive
  az network cross-region-lb create \
    --name myLoadBalancer-CR \
    --resource-group myResourceGroupLB-CR \
    --frontend-ip-name myFrontEnd-CR \
    --backend-pool-name myBackEndPool-CR     
```

### <a name="create-the-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Yük dengeleyici kuralı şunları tanımlar:

* Gelen trafik için ön uç IP yapılandırması.
* Trafiği almak için arka uç IP Havuzu.
* Gerekli kaynak ve hedef bağlantı noktası. 

[Az Network Region-lb Rule Create](/cli/azure/network/cross-region-lb/rule#az_network_cross_region_lb_rule_create)ile bir yük dengeleyici kuralı oluşturun:

* Adlandırılmış **Myhttprule-CR**
* Ön uç havuzundaki **80 numaralı bağlantı noktasını** dınleme **-CR**.
* **80 numaralı bağlantı noktasını** kullanan **mybackendpool-CR adlı** arka uç adres havuzuna yük dengeli ağ trafiği gönderiliyor. 
* Protokol **TCP**.

```azurecli-interactive
  az network cross-region-lb rule create \
    --backend-port 80 \
    --frontend-port 80 \
    --lb-name myLoadBalancer-CR \
    --name myHTTPRule-CR \
    --protocol tcp \
    --resource-group myResourceGroupLB-CR \
    --backend-pool-name myBackEndPool-CR \
    --frontend-ip-name myFrontEnd-CR
```

## <a name="create-backend-pool"></a>Arka uç havuzu oluşturma

Bu bölümde, çapraz bölge yük dengeleyicinin arka uç havuzuna iki bölgesel standart yük dengeleyici ekleyeceksiniz.

> [!IMPORTANT]
> Bu adımları tamamlayabilmeniz için, arka uç havuzlarıyla birlikte iki bölgesel yük dengeleyiciler aboneliğinizde dağıtılmış olduğundan emin olun.  Daha fazla bilgi için bkz. **[hızlı başlangıç: Azure CLI kullanarak VM 'lerin yükünü dengelemek için genel yük dengeleyici oluşturma](quickstart-load-balancer-standard-public-cli.md)**.

### <a name="add-the-regional-frontends-to-load-balancer"></a>Bölgesel ön uçları yük dengeleyiciye ekleme

Bu bölümde, iki bölgesel yük dengeleyiciler için kaynak kimliklerini değişkenlere yerleştirebilirsiniz.  Daha sonra bu değişkenleri, geçici bölge yük dengeleyicinin arka uç adres havuzuna ön uçlar eklemek için kullanabilirsiniz.

[Az Network lb ön uç-IP Show](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_show)komutuyla kaynak kimliklerini alın.

Bölgeler arası yük dengeleyicinin arka uç havuzundaki değişkenlere yerleştirdiğiniz ön uçları eklemek için [az Network Cross-Region-lb Address-Pool Address Add](/cli/azure/network/cross-region-lb/address-pool/address#az_network_cross_region_lb_address_pool_address_add) komutunu kullanın:

```azurecli-interactive
  region1id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R1 \
    --name myFrontEnd-R1 \
    --resource-group CreatePubLBQS-rg-r1 \
    --query id \
    --output tsv)

  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region1id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R1 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR

  region2id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R2 \
    --name myFrontEnd-R2 \
    --resource-group CreatePubLBQS-rg-r2 \
    --query id \
    --output tsv)
  
  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region2id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R2 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR
```

## <a name="test-the-load-balancer"></a>Yük dengeleyiciyi test etme

Bu bölümde, bölgeler arası yük dengeleyiciyi test edersiniz. Genel IP adresine bir Web tarayıcısında bağlanırsınız.  Bölgesel yük dengeleyici arka uç havuzlarından birindeki sanal makineleri durdurarak yük devretmeyi gözlemleyeceksiniz.

1. Yük dengeleyicinin genel IP adresini almak için [az Network public-IP Show](/cli/azure/network/public-ip#az_network_public_ip_show)kullanın:

    ```azurecli-interactive
      az network public-ip show \
        --resource-group myResourceGroupLB-CR \
        --name PublicIPmyLoadBalancer-CR \
        --query ipAddress \
        --output tsv
    ```
2. Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın. IIS Web sunucusunun varsayılan sayfası, tarayıcıda görüntülenir.

3. Bölgesel yük dengeleyicileri birinin arka uç havuzundaki sanal makineleri durdurun.

4. Web tarayıcısını yenileyin ve diğer bölgesel yük dengeleyiciye bağlantının yük devretmesini gözlemleyin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az Group Delete](/cli/azure/group#az_group_delete) komutunu kullanarak kaynak grubunu, yük dengeleyiciyi ve tüm ilgili kaynakları kaldırın.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB-CR
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

* Bir çapraz bölge yük dengeleyici oluşturuldu.
* Yük Dengeleme kuralı oluşturuldu.
* Bölgesel yük dengeleyiciler çapraz bölge yük dengeleyicinin arka uç havuzuna eklendi.
* Yük dengeleyici test edildi.

Nasıl yapılacağını öğrenmek için sonraki makaleye ilerleyin...
> [!div class="nextstepaction"]
> [Farklı kullanılabilirlik bölgelerindeki VM’lerin yükünü dengeleme](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
