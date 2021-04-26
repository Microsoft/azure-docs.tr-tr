---
title: Sanal makine ölçek kümeleri tarafından kullanılan mevcut yük dengeleyiciyi güncelleştirme veya silme
titleSuffix: Update or delete an existing load balancer used by virtual machine scale sets
description: Bu nasıl yapılır makalesi sayesinde Azure Standart Load Balancer ve sanal makine ölçek kümelerini kullanmaya başlayın.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18, devx-track-azurecli
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: irenehua
ms.openlocfilehash: 2079eeb97ac935ba24c6ff0616a58fbb28a962f4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480095"
---
# <a name="update-or-delete-a-load-balancer-used-by-virtual-machine-scale-sets"></a>Sanal makine ölçek kümeleri tarafından kullanılan yük dengeleyiciyi güncelleştirme veya silme

Sanal Makine Ölçek Kümeleri ve bir Azure Load Balancer örneğiyle çalışırken şunları yapabilirsiniz:

- Kural ekleyin, güncelleştirin ve silin.
- Yapılandırma ekleyin.
- Yük dengeleyiciyi silin.

## <a name="set-up-a-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Sanal makine ölçek kümelerinin ölçeğini genişletmek için yük dengeleyici ayarlama

Azure Load Balancer örneğinin bir [gelen NAT havuzu](/cli/azure/network/lb/inbound-nat-pool) ayarlamış olduğundan ve sanal makine ölçek kümesinin yük dengeleyicinin arka uç havuzunda bulunduğundan emin olun. Load Balancer, sanal makine ölçek kümesine yeni sanal makine örnekleri eklendiğinde, gelen NAT havuzunda otomatik olarak yeni gelen NAT kuralları oluşturur.

Gelen NAT havuzunun düzgün ayarlanıp ayarlanmadığını denetlemek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol taraftaki menüden **tüm kaynaklar**' ı seçin. Sonra kaynak listesinden **Myloadbalancer** öğesini seçin.
1. **Ayarlar** altında **gelen NAT kuralları**' nı seçin. Sağ bölmede, sanal makine ölçek kümesindeki her bir örnek için oluşturulan kuralların listesini görürseniz, her zaman ölçeklendirme için her şey için herhangi bir zaman hazırsınız.

## <a name="add-inbound-nat-rules"></a>Gelen NAT kuralları ekleme

Tek bir gelen NAT kuralı eklenemez. Ancak, sanal makine ölçek kümesindeki tüm örnekler için tanımlı ön uç bağlantı noktası aralığı ve arka uç bağlantı noktası içeren bir dizi gelen NAT kuralı ekleyebilirsiniz.

Sanal Makine Ölçek Kümeleri için bir bütün gelen NAT kuralı kümesi eklemek için, önce yük dengeleyicide bir gelen NAT havuzu oluşturun. Ardından, sanal makine ölçek kümesinin ağ profilinden gelen NAT havuzuna başvurun. CLı kullanan tam bir örnek gösterilir.

Yeni gelen NAT havuzunda, mevcut gelen NAT havuzlarıyla çakışan bir ön uç bağlantı noktası aralığı olmamalıdır. Ayarlanan mevcut gelen NAT havuzlarını görüntülemek için bu [CLI komutunu](/cli/azure/network/lb/inbound-nat-pool#az_network_lb_inbound_nat_pool_list)kullanın:
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIp
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          -â€“instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
```
## <a name="update-inbound-nat-rules"></a>Gelen NAT kurallarını Güncelleştir

Bağımsız gelen NAT kuralları güncelleştirilemiyor. Ancak, bir dizi gelen NAT kuralını, tanımlı bir ön uç bağlantı noktası aralığı ve sanal makine ölçek kümesindeki tüm örnekler için bir arka uç bağlantı noktası ile güncelleştirebilirsiniz.

Sanal Makine Ölçek Kümeleri için tüm gelen NAT kurallarını güncelleştirmek için yük dengeleyicideki gelen NAT havuzunu güncelleştirin.
    
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="delete-inbound-nat-rules"></a>Gelen NAT kurallarını Sil

Bağımsız gelen NAT kuralları silinemez, ancak gelen NAT havuzunu silerek gelen NAT kuralları kümesinin tamamını silebilirsiniz.

NAT havuzunu silmek için önce ölçek kümesinden kaldırın. CLı kullanarak tam bir örnek burada gösterilmiştir:

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
     az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
    az network lb inbound-nat-pool delete
       --resource-group MyResourceGroup
       -â€“lb-name MyLoadBalancer
       --name MyNatPool
```

## <a name="add-multiple-ip-configurations"></a>Birden çok IP yapılandırması ekleme

Birden çok IP yapılandırması eklemek için:

1. Sol taraftaki menüden **tüm kaynaklar**' ı seçin. Sonra kaynak listesinden **Myloadbalancer** öğesini seçin.
1. **Ayarlar** altında **ön uç IP yapılandırması**' nı seçin. Ardından **Ekle**'yi seçin.
1. **Ön uç IP adresi ekle** sayfasında, değerleri girin ve **Tamam**' ı seçin.
1. Yeni Yük Dengeleme kuralları gerekiyorsa, bu öğreticide [5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) . ve [6](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) . adımı izleyin.
1. Gerekirse yeni oluşturulan ön uç IP yapılandırmasını kullanarak yeni bir gelen NAT kuralları kümesi oluşturun. Önceki bölümde bir örnek bulunur.

## <a name="multiple-virtual-machine-scale-sets-behind-a-single-load-balancer"></a>Tek bir Load Balancer arkasında birden çok sanal makine ölçek kümesi

Load Balancer içinde gelen NAT havuzu oluşturun, bir sanal makine ölçek kümesinin ağ profilinde gelen NAT havuzuna başvurun ve son olarak değişikliklerin etkili olması için örnekleri güncelleştirin. Tüm sanal makine ölçek kümeleri için adımları yineleyin.

Çakışmayan ön uç bağlantı noktası aralıklarıyla ayrı gelen NAT havuzları oluşturmayı unutmayın.
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          -â€“instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
          
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool2
          --protocol Tcp 
          --frontend-port-range-start 100 
          --frontend-port-range-end 109 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig2
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS2 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool2'}"
            
  az vmss update-instances
          -â€“instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS2
```

## <a name="delete-the-front-end-ip-configuration-used-by-the-virtual-machine-scale-set"></a>Sanal makine ölçek kümesi tarafından kullanılan ön uç IP yapılandırmasını sil

Ölçek kümesi tarafından kullanılan ön uç IP yapılandırmasını silmek için:

 1. Önce ön uç IP yapılandırmasına başvuran gelen NAT havuzunu (gelen NAT kuralları kümesi) silin. Gelen kuralların nasıl silineceği hakkındaki yönergeler önceki bölümde bulunur.
 1. Ön uç IP yapılandırmasına başvuran Yük Dengeleme kuralını silin.
 1. Ön uç IP yapılandırmasını silin.

## <a name="delete-a-load-balancer-used-by-a-virtual-machine-scale-set"></a>Bir sanal makine ölçek kümesi tarafından kullanılan yük dengeleyiciyi silme

Ölçek kümesi tarafından kullanılan ön uç IP yapılandırmasını silmek için:

 1. Önce ön uç IP yapılandırmasına başvuran gelen NAT havuzunu (gelen NAT kuralları kümesi) silin. Gelen kuralların nasıl silineceği hakkındaki yönergeler önceki bölümde bulunur.
 1. Sanal makine ölçek kümesini içeren arka uç havuzuna başvuran Yük Dengeleme kuralını silin.
 1. `loadBalancerBackendAddressPool`Sanal makine ölçek kümesinin ağ profilinden başvuruyu kaldırın.
 
 CLı kullanarak tam bir örnek burada gösterilmiştir:

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools
    az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
```
Son olarak, yük dengeleyici kaynağını silin.
 
## <a name="next-steps"></a>Sonraki adımlar

Azure Load Balancer ve sanal makine ölçek kümeleri hakkında daha fazla bilgi edinmek için kavramlar hakkında daha fazla bilgi edinin.

> [Sanal makine ölçek kümeleriyle Azure Load Balancer](load-balancer-standard-virtual-machine-scale-sets.md)
