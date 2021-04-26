---
title: Hizmet uç noktası ilkelerini Yapılandırma-Azure HDInsight
description: Azure HDInsight ile sanal ağınız için hizmet uç noktası ilkelerini yapılandırmayı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/15/2020
ms.openlocfilehash: 992cd994f96b5637d5afd91bccfecde8704d2886
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98940631"
---
# <a name="configure-virtual-network-service-endpoint-policies-for-azure-hdinsight"></a>Azure HDInsight için sanal ağ hizmet uç noktası ilkelerini yapılandırma

Bu makalede, Azure HDInsight ile sanal ağlarda hizmet uç noktası ilkelerinin nasıl uygulanacağı hakkında bilgi sağlanır.

## <a name="background"></a>Arka Plan

Azure HDInsight, kendi sanal ağınızda kümeler oluşturmanızı sağlar. Sanal ağınızdan gelen ve depolama hesapları gibi diğer Azure hizmetlerine giden trafiğe izin vermeniz gerekiyorsa, [hizmet uç noktası ilkeleri](../virtual-network/virtual-network-service-endpoint-policies-overview.md)oluşturabilirsiniz. Ancak Azure portal aracılığıyla oluşturulan hizmet uç noktası ilkeleri yalnızca tek bir hesap, bir abonelikteki tüm hesaplar veya bir kaynak grubundaki tüm hesaplar için bir ilke oluşturmanıza izin verir.

Ancak, yönetilen bir hizmet olarak, Azure HDInsight her bir bölgedeki belirli depolama hesaplarındaki her bir kümeden veri ve günlük dosyaları toplar. Bu verilerin, sanal ağınızdan HDInsight 'a ulaşması için, Azure HDInsight tarafından yönetilen belirli veri toplama noktalarına giden trafiğe izin veren hizmet uç noktası ilkeleri oluşturmanız gerekir.

## <a name="service-endpoint-policies-for-hdinsight"></a>HDInsight için hizmet uç noktası ilkeleri

Bu hizmet uç noktası ilkeleri aşağıdaki işlevleri destekler:

- Küme oluşturma, iş yürütme ve ölçekleme gibi platform işlemlerinde Günlükler ve telemetri koleksiyonu.
- Kümenizdeki yazılım ve kitaplıkları sağlamak için sanal sabit diskleri (VHD 'ler) yeni oluşturulan küme düğümlerine ekleme.

Bu veri akışını etkinleştirmek için hizmet uç noktası ilkeleri oluşturulmadıysa, küme oluşturma işlemi başarısız olabilir ve Azure HDInsight, kümeleriniz için destek sağlayamayacak.

## <a name="create-service-endpoint-policies-for-hdinsight"></a>HDInsight için hizmet uç noktası ilkeleri oluşturma

Yeni kümeler oluşturmadan önce, sanal ağınıza doğru hizmet uç noktası ilkelerinin eklendiğinden emin olun. Aksi takdirde, küme oluşturma başarısız olabilir veya hata ile sonuçlanabilir.

Gerekli hizmet uç noktası ilkelerini oluşturmak için aşağıdaki işlemi kullanın:

1. HDInsight kümenizi oluşturacağınız bölgeye karar verin.
1. HDInsight Management Storage hesapları için tüm kaynak gruplarını sağlayan [hizmet uç noktası ilke kaynakları listesinde](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json)bu bölgeye bakın.
1. Bölgeniz için kaynak grupları listesini seçin. İçin kaynak bir örnek aşağıda verilmiştir `Canada Central` :

    ```json
    "Canada Central":[
        "/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
        "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG"
    ],
    ```

1. Azure CLı veya Azure PowerShell yazılmış kurulum betiğine kaynak grupları listesini ekleyin.

    ```azurecli
    $subscriptionId = "<subscription id>"
    $rgName="<resource group name> "
    $location="<location name>"
    $vnetName="<vnet name>"
    $subnetName="<subnet name>"
    $sepName="<service endpoint policy name>"
    $sepDefName="<service endpoint policy definition name>"
    
    # Set to the right subscription ID
    az account set --subscription $subscriptionId
    
    # setup service endpoint on the virtual network subnet
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoints Microsoft.Storage
    
    # Create Service Endpoint Policy
    az network service-endpoint policy create -g $rgName  -n $sepName -l $location
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",`
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Assign service resources to the SEP policy.
    az network service-endpoint policy-definition create -g $rgName --policy-name $sepName -n $sepDefName --service "Microsoft.Storage" --service-resources $resources
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoint-policy $sepName
    ```

    Aşağıdaki kod parçacığını kullanarak, PowerShell kullanarak hizmet uç noktası ilkenizi ayarlamayı tercih ediyorsanız.
    
    ```powershell
    #Script to assign SEP 
    $subscriptionId = "<subscription id>"
    $rgName = "<resource group name>"
    $vnetName = "<vnet name>"
    $subnetName = "<subnet Name"
    $location = "Canada Central"
    
    # Connect to your Azure Account
    Connect-AzAccount
    
    # Select the Subscription that you want to use
    Select-AzSubscription -SubscriptionId $subscriptionId
    
    # Retrieve VNet Config
    $vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
    
    # Retrieve Subnet Config
    $subnet = Get-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Declare service endpoint policy definition
    $sepDef = New-AzServiceEndpointPolicyDefinition -Name "SEPHDICanadaCentral" -Description "Service Endpoint Policy Definition" -Service "Microsoft.Storage" -ServiceResource $resources
    
    # Service Endpoint Policy
    $sep= New-AzServiceEndpointPolicy -ResourceGroupName $rgName -Name "SEPHDICanadaCentral" -Location $location -ServiceEndpointPolicyDefinition $sepDef
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    Set-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet -AddressPrefix $subnet.AddressPrefix -ServiceEndpointPolicy $sep
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure HDInsight sanal ağ mimarisi](hdinsight-virtual-network-architecture.md)
* [Ağ sanal gereci yapılandırma](./network-virtual-appliance.md)
