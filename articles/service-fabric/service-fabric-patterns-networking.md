---
title: Azure Service Fabric için ağ düzenleri | Microsoft Docs
description: Service Fabric için ortak ağ düzenlerini ve Azure ağ özellikleri 'ni kullanarak küme oluşturmayı açıklar.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: atsenthi
ms.openlocfilehash: 638ee162b770f949eaf0a0fc34b745698364d019
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900102"
---
# <a name="service-fabric-networking-patterns"></a>Service Fabric ağ desenleri
Azure Service Fabric kümenizi diğer Azure ağ özellikleriyle tümleştirebilirsiniz. Bu makalede, aşağıdaki özellikleri kullanan kümelerin nasıl oluşturulacağını göstereceğiz:

- [Var olan sanal ağ veya alt ağ](#existingvnet)
- [Statik genel IP adresi](#staticpublicip)
- [Yalnızca iç yük dengeleyici](#internallb)
- [İç ve dış yük dengeleyici](#internalexternallb)

Service Fabric, standart bir sanal makine ölçek kümesinde çalışır. Bir sanal makine ölçek kümesinde kullanabileceğiniz tüm işlevler, bir Service Fabric kümesiyle kullanabilirsiniz. Sanal Makine Ölçek Kümeleri ve Service Fabric için Azure Resource Manager şablonlarının ağ bölümleri aynı. Mevcut bir sanal ağa dağıttıktan sonra Azure ExpressRoute, Azure VPN Gateway, bir ağ güvenlik grubu ve sanal ağ eşlemesi gibi diğer ağ özelliklerini kolayca ekleyebilirsiniz.

### <a name="allowing-the-service-fabric-resource-provider-to-query-your-cluster"></a>Service Fabric kaynak sağlayıcısının kümenizi sorgulayabilmesine izin verme

Service Fabric, tek bir açıdan diğer ağ özelliklerinden farklıdır. [Azure Portal](https://portal.azure.com) , düğümler ve uygulamalar hakkında bilgi almak üzere bir kümeye çağrı yapmak için Service Fabric kaynak sağlayıcısını dahili olarak kullanır. Service Fabric kaynak sağlayıcısı, yönetim uç noktasındaki HTTP Gateway bağlantı noktasına (varsayılan olarak bağlantı noktası 19080) genel olarak erişilebilir gelen erişimi gerektirir. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) , kümenizi yönetmek için yönetim uç noktasını kullanır. Service Fabric kaynak sağlayıcısı, bu bağlantı noktasını, Azure portal görüntülenecek kümeniz hakkındaki bilgileri sorgulamak için de kullanır. 

Bağlantı noktası 19080 Service Fabric kaynak sağlayıcısından erişilebilir durumda değilse, portalda düğüm *bulunamadı* gibi bir ileti görünür ve düğüm ve uygulama listeniz boş görünür. Azure portal kümenizi görmek isterseniz, yük dengeleyicinizin genel bir IP adresi göstermesi gerekir ve ağ güvenlik grubunuz gelen bağlantı noktası 19080 trafiğine izin vermelidir. Kurulumlarınız bu gereksinimleri karşılamıyorsa, Azure portal kümenizin durumunu görüntülemez.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="templates"></a>Şablonlar

Tüm Service Fabric şablonları [GitHub](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking)' da bulunur. Aşağıdaki PowerShell komutlarını kullanarak şablonları olduğu gibi dağıtabilmelisiniz. Mevcut Azure sanal ağ şablonunu veya statik genel IP şablonunu dağıtıyorsanız, önce Bu makalenin [ilk kurulum](#initialsetup) bölümünü okuyun.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>İlk kurulum

### <a name="existing-virtual-network"></a>Var olan sanal ağ

Aşağıdaki örnekte, **existingrg** kaynak grubunda existingrg-VNET adlı mevcut bir sanal ağ ile başlayacağız. Alt ağ varsayılan olarak adlandırılır. Bu varsayılan kaynaklar, bir standart sanal makine (VM) oluşturmak için Azure portal kullandığınızda oluşturulur. Sanal ağı ve alt ağı VM oluşturmadan oluşturabilirsiniz, ancak mevcut bir sanal ağa küme eklemenin ana hedefi, diğer VM 'lere ağ bağlantısı sağlamaktır. VM 'nin oluşturulması, mevcut bir sanal ağın genellikle nasıl kullanıldığına ilişkin iyi bir örnek sağlar. Service Fabric kümeniz, genel IP adresi olmadan yalnızca bir iç yük dengeleyici kullanıyorsa, VM 'yi ve genel IP 'sini güvenli bir geçiş *kutusu*olarak kullanabilirsiniz.

### <a name="static-public-ip-address"></a>Statik genel IP adresi

Statik bir genel IP adresi genellikle atanan VM 'den veya VM 'lerden ayrı olarak yönetilen ayrılmış bir kaynaktır. Bu, adanmış bir ağ kaynak grubunda (Service Fabric kümesi kaynak grubunun aksine) sağlanır. Azure portal veya PowerShell kullanarak aynı ExistingRG kaynak grubunda staticIP1 adlı statik bir genel IP adresi oluşturun:

```powershell
PS C:\Users\user> New-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Service Fabric şablonu

Bu makaledeki örneklerde, Template. JSON Service Fabric kullanırız. Bir kümeyi oluşturmadan önce şablonu portaldan indirmek için standart Portal Sihirbazı 'nı kullanabilirsiniz. Ayrıca, [güvenli beş düğümlü Service Fabric kümesi](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure)gibi [örnek şablonlardan](https://github.com/Azure-Samples/service-fabric-cluster-templates)birini de kullanabilirsiniz.

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Var olan sanal ağ veya alt ağ

1. Alt ağ parametresini var olan alt ağın adıyla değiştirin ve ardından var olan sanal ağa başvurmak için iki yeni parametre ekleyin:

    ```json
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```

2. Mevcut alt ağı kullandığınız ve 1. adımda bu değişkeni devre dışı bıraktığınız için `Microsoft.Compute/virtualMachineScaleSets`özniteliği `nicPrefixOverride`.

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. `vnetID` değişkenini var olan sanal ağı işaret etmek üzere değiştirin:

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. Azure yeni bir sanal ağ oluşturmadığından `Microsoft.Network/virtualNetworks` kaynaklarınızdan kaldırın:

    ```json
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
            {
                "name": "[parameters('subnet0Name')]",
                "properties": {
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

5. Yeni bir sanal ağ oluşturmaya bağlı olmadığınız için, `Microsoft.Compute/virtualMachineScaleSets``dependsOn` özniteliğinden sanal ağı not edin:

    ```json
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

6. Şablonu dağıt:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Dağıtımdan sonra sanal ağınız yeni ölçek kümesi VM 'lerini içermelidir. Sanal makine ölçek kümesi düğüm türü, var olan sanal ağı ve alt ağı göstermelidir. Ayrıca, sanal ağda zaten bulunan VM 'ye erişmek ve yeni ölçek kümesi VM 'lerine ping eklemek için Uzak Masaüstü Protokolü (RDP) kullanabilirsiniz:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Başka bir örnek için, [Service Fabric özgü olmayan birine](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet)bakın.


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Statik genel IP adresi

1. Var olan statik IP kaynak grubu adı, adı ve tam etki alanı adı (FQDN) için parametreler ekleyin:

    ```json
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. `dnsName` parametresini kaldırın. (Statik IP adresinde zaten bir tane var.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Var olan statik IP adresine başvuracak bir değişken ekleyin:

    ```json
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Azure yeni bir IP adresi oluşturmadığından `Microsoft.Network/publicIPAddresses` kaynaklarınızdan kaldırın:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. `Microsoft.Network/loadBalancers``dependsOn` özniteliğinden IP adresini not edin, böylece yeni bir IP adresi oluşturmaya bağlı kalmazsınız:

    ```json
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. `Microsoft.Network/loadBalancers` kaynağında, `frontendIPConfigurations` `publicIPAddress` öğesini yeni oluşturulan bir IP adresine başvuracak şekilde değiştirin:

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. `Microsoft.ServiceFabric/clusters` kaynağında, `managementEndpoint` değerini statik IP adresinin DNS FQDN 'sine değiştirin. Güvenli bir küme kullanıyorsanız, *http://* olarak *https://* olarak değiştirdiğinizden emin olun. (Bu adımın yalnızca Service Fabric kümeler için geçerli olduğunu unutmayın. Bir sanal makine ölçek kümesi kullanıyorsanız, bu adımı atlayın.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. Şablonu dağıt:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Dağıtımdan sonra, yük dengeleyicinizin diğer kaynak grubundaki genel statik IP adresine bağlandığını görebilirsiniz. Service Fabric istemci bağlantı uç noktası ve [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) uç noktası, statik IP ADRESININ DNS FQDN 'sine işaret edin.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Yalnızca iç yük dengeleyici

Bu senaryo varsayılan Service Fabric şablonundaki dış yük dengeleyiciyi yalnızca dahili bir yük dengeleyiciye değiştirir. Azure portal ve Service Fabric kaynak sağlayıcısına yönelik etkileri için [makalenin önceki kısımlarında](#allowing-the-service-fabric-resource-provider-to-query-your-cluster) bulabilirsiniz.

1. `dnsName` parametresini kaldırın. (Gerekli değildir.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. İsteğe bağlı olarak, statik bir ayırma yöntemi kullanırsanız, statik bir IP adresi parametresi ekleyebilirsiniz. Dinamik bir ayırma yöntemi kullanıyorsanız, bu adımı uygulamanız gerekmez.

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Azure yeni bir IP adresi oluşturmadığından `Microsoft.Network/publicIPAddresses` kaynaklarınızdan kaldırın:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. `Microsoft.Network/loadBalancers`IP adresi `dependsOn` özniteliğini kaldırın, böylece yeni bir IP adresi oluşturmaya bağlı kalmazsınız. Yük dengeleyici artık sanal ağ alt ağına bağlı olduğundan sanal ağ `dependsOn` özniteliğini ekleyin:

    ```json
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. Bir alt ağ ve `privateIPAddress`kullanmak için, yük dengeleyicinin `frontendIPConfigurations` ayarını `publicIPAddress`kullanarak değiştirin. `privateIPAddress` önceden tanımlanmış bir statik iç IP adresi kullanır. Dinamik bir IP adresi kullanmak için `privateIPAddress` öğesini kaldırın ve `privateIPAllocationMethod` **dinamik**olarak değiştirin.

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. `Microsoft.ServiceFabric/clusters` kaynağında, `managementEndpoint` iç yük dengeleyici adresini gösterecek şekilde değiştirin. Güvenli bir küme kullanıyorsanız, *http://* olarak *https://* olarak değiştirdiğinizden emin olun. (Bu adımın yalnızca Service Fabric kümeler için geçerli olduğunu unutmayın. Bir sanal makine ölçek kümesi kullanıyorsanız, bu adımı atlayın.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. Şablonu dağıt:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Dağıtımdan sonra, yük dengeleyici özel statik 10.0.0.250 IP adresini kullanır. Aynı sanal ağda başka bir makineniz varsa, iç [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) uç noktasına gidebilirsiniz. Yük dengeleyicinin arkasındaki düğümlerden birine bağlandığını unutmayın.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>İç ve dış yük dengeleyici

Bu senaryoda, var olan tek düğümlü tür dış yük dengeleyiciyi başlatın ve aynı düğüm türü için bir iç yük dengeleyici ekleyin. Arka uç adres havuzuna bağlı bir arka uç bağlantı noktası yalnızca tek bir yük dengeleyiciye atanabilir. Hangi yük dengeleyicinin uygulama bağlantı noktalarınızı olacağını ve hangi yük dengeleyicinin yönetim uç noktalarınıza (bağlantı noktaları 19000 ve 19080) sahip olacağını seçin. Yönetim uç noktalarını iç yük dengeleyiciye yerleştirirseniz, [makalenin önceki bölümlerinde](#allowing-the-service-fabric-resource-provider-to-query-your-cluster)ele alınan Service Fabric kaynak sağlayıcısı kısıtlamalarını aklınızda bulundurun. Kullandığımız örnekte, yönetim uç noktaları dış yük dengeleyicide kalır. Ayrıca, 80 uygulama bağlantı noktasını ekler ve iç yük dengeleyiciye yerleştirebilirsiniz.

İki düğümlü tür bir kümede, tek bir düğüm türü dış yük dengeleyicide bulunur. Diğer düğüm türü, iç yük dengeleyicide yer alır. İki düğümlü tür bir küme kullanmak için, portal tarafından oluşturulan iki düğümlü tür şablonda (iki yük dengeleyiciye gelir) ikinci yük dengeleyiciyi bir iç yük dengeleyiciye geçirin. Daha fazla bilgi için [yalnızca iç yük dengeleyici](#internallb) bölümüne bakın.

1. Statik iç yük dengeleyici IP adresi parametresini ekleyin. (Dinamik bir IP adresi kullanımıyla ilgili notlar için, bu makalenin önceki bölümlerine bakın.)

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Uygulama bağlantı noktası 80 parametresi ekleyin.

3. Mevcut ağ değişkenlerinin iç sürümlerini eklemek için onları kopyalayıp yapıştırın ve "-Int" öğesini ada ekleyin:

    ```json
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. Uygulama bağlantı noktası 80 ' ü kullanan Portal tarafından oluşturulan şablonla başlatırsanız, varsayılan portal şablonu dış yük dengeleyicide AppPort1 (bağlantı noktası 80) ekler. Bu durumda, dış yük dengeleyici `loadBalancingRules` ve yoklamalarını AppPort1 kaldırarak iç yük dengeleyiciye ekleyebilirsiniz:

    ```json
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ],
    "inboundNatPools": [
    ```

5. İkinci bir `Microsoft.Network/loadBalancers` kaynağı ekleyin. [Yalnızca iç yük dengeleyici](#internallb) bölümünde oluşturulan iç yük dengeleyiciye benzer ancak "-Int" yük dengeleyici değişkenlerini kullanır ve yalnızca 80 uygulama bağlantı noktasını uygular. Bu Ayrıca, RDP uç noktalarını ortak yük dengeleyicide tutmak için `inboundNatPools`da kaldırır. İç yük dengeleyicide RDP isterseniz, dış yük dengeleyiciden bu iç yük dengeleyiciye `inboundNatPools` taşıyın:

    ```json
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" load balancer variables. */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add "-Internal" to the name. */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
                    "backendAddressPools": [
                        {
                            "name": "LoadBalancerBEAddressPool",
                            "properties": {}
                        }
                    ],
                    "loadBalancingRules": [
                        /* Add the AppPort rule. Be sure to reference the "-Int" versions of backendAddressPool, frontendIPConfiguration, and the probe variables. */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port. */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. `Microsoft.Compute/virtualMachineScaleSets` kaynağı için `networkProfile` içinde iç arka uç adres havuzunu ekleyin:

    ```json
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. Şablonu dağıt:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Dağıtımdan sonra kaynak grubunda iki yük dengeleyicileri görebilirsiniz. Yük dengeleyiciler ' e gözattığınızda genel IP adresi ve yönetim uç noktaları (bağlantı noktaları 19000 ve 19080) ortak IP adresine atanır. Ayrıca, iç yük dengeleyiciye atanan statik iç IP adresini ve uygulama uç noktasını (bağlantı noktası 80) görebilirsiniz. Her iki yük dengeleyicisi de aynı sanal makine ölçek kümesi arka uç havuzunu kullanır.

## <a name="notes-for-production-workloads"></a>Üretim iş yükleri için notlar

Yukarıdaki GitHub şablonları, temel SKU 'SU olan Azure Standart Load Balancer (SLB) için varsayılan SKU ile çalışacak şekilde tasarlanmıştır. Bu SLB 'ın SLA 'sı yoktur, bu nedenle üretim iş yükleri için standart SKU 'nun kullanılması gerekir. Bunun hakkında daha fazla bilgi için bkz. [Azure Standart Load Balancer genel bakış](/azure/load-balancer/load-balancer-standard-overview). SLB için standart SKU kullanan herhangi bir Service Fabric kümesi, her bir düğüm türünün bağlantı noktası 443 ' de giden trafiğe izin veren bir kurala sahip olduğundan emin olmalıdır. Bu, küme kurulumunu tamamlaması için gereklidir ve bu tür bir kural olmadan herhangi bir dağıtım başarısız olur. Yukarıdaki "iç bir" yük dengeleyicinin örneğinde, bağlantı noktası 443 ' e giden trafiğe izin veren bir kuralla ek bir dış yük dengeleyici eklenmelidir.

## <a name="next-steps"></a>Sonraki adımlar
[Küme oluşturma](service-fabric-cluster-creation-via-arm.md)

Dağıtımdan sonra kaynak grubunda iki yük dengeleyicileri görebilirsiniz. Yük dengeleyiciler ' e gözattığınızda genel IP adresi ve yönetim uç noktaları (bağlantı noktaları 19000 ve 19080) ortak IP adresine atanır. Ayrıca, iç yük dengeleyiciye atanan statik iç IP adresini ve uygulama uç noktasını (bağlantı noktası 80) görebilirsiniz. Her iki yük dengeleyicisi de aynı sanal makine ölçek kümesi arka uç havuzunu kullanır.

