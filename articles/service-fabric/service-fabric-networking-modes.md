---
title: Azure Service Fabric kapsayıcı Hizmetleri için ağ modlarını yapılandırma | Microsoft Docs
description: Azure Service Fabric tarafından desteklenen farklı ağ modlarını ayarlamayı öğrenin.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: aa7b63453a5147742e27b9bb32ad05221e745f8c
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168799"
---
# <a name="service-fabric-container-networking-modes"></a>Service Fabric kapsayıcı ağ modları

Kapsayıcı Hizmetleri için Azure Service Fabric kümesi varsayılan olarak **NAT** ağ modunu kullanır. Aynı bağlantı noktasında birden fazla kapsayıcı hizmeti dinlerken ve NAT modu kullanılırken, dağıtım hataları oluşabilir. Aynı bağlantı noktasında dinleme yapan birden çok kapsayıcı hizmetini desteklemek için, Service Fabric **Açık** ağ modu (sürüm 5,7 ve üzeri) sağlar. Açık modda, her kapsayıcı hizmetinde, aynı bağlantı noktasında dinleme yapan birden çok hizmeti destekleyen dahili ve dinamik olarak atanan bir IP adresi vardır.  

Hizmet bildiriminizde bir statik uç noktaya sahip bir kapsayıcı hizmetiniz varsa, dağıtım hataları olmadan açma modunu kullanarak yeni hizmetler oluşturabilir ve silebilirsiniz. Aynı Docker-Compose. yıml dosyası aynı zamanda birden fazla hizmet oluşturmak için statik bağlantı noktası eşlemeleriyle birlikte kullanılabilir.

Bir kapsayıcı hizmeti yeniden başlatıldığında veya kümedeki başka bir düğüme taşınırsa, IP adresi değişir. Bu nedenle, kapsayıcı Hizmetleri 'ni bulmaya yönelik dinamik olarak atanan IP adresini kullanmanızı önermiyoruz. Hizmet bulma için yalnızca Service Fabric Adlandırma Hizmeti veya DNS hizmeti kullanılmalıdır. 

>[!WARNING]
>Azure, sanal ağ başına toplam 65.356 IP 'ye izin verir. Düğüm sayısının toplamı ve kapsayıcı hizmeti örneklerinin sayısı (Açık modu kullanılarak) bir sanal ağ içindeki 65.356 IP 'yi aşamaz. Yüksek yoğunluklu senaryolar için NAT ağ modunu öneririz. Ek olarak, yük dengeleyici gibi diğer bağımlılıkların göz önünde bulundurulması gereken diğer [sınırlamalar](https://docs.microsoft.com/azure/azure-subscription-service-limits) olacaktır. Şu anda düğüm başına 50 IP 'ye kadar sınanmış ve kanıtlanmış kararlı durumda. 
>

## <a name="set-up-open-networking-mode"></a>Açık ağ modunu ayarlama

1. Azure Resource Manager şablonunu ayarlayın. Küme kaynağının **Fabricsettings** bölümünde DNS HIZMETINI ve IP sağlayıcısını etkinleştirin: 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```
    
2. Sanal makine ölçek kümesi kaynağının ağ profili bölümünü ayarlayın. Bu, kümenin her bir düğümünde birden çok IP adresinin yapılandırılmasını sağlar. Aşağıdaki örnek bir Windows/Linux Service Fabric kümesi için düğüm başına beş IP adresi ayarlar. Her düğümdeki bağlantı noktasını dinleyen beş hizmet örneği olabilir. Azure Load Balancer beş IP 'nin erişilebilir olması için, aşağıda gösterildiği gibi, beş IP 'yi Azure Load Balancer arka uç adres havuzuna kaydedin.  Değişkenleri, değişkenler bölümünde şablonunuzun en üstüne de eklemeniz gerekir.

    Bu bölümü değişkenlere ekleyin:

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    ```
    
    Bu bölümü sanal makine ölçek kümesi kaynağına ekleyin:

    ```json   
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        }
                      ],
                      "primary": true
                    }
                  }
                ]
              }
   ```
 
3. Yalnızca Windows kümeleri için, sanal ağ için UDP/53 bağlantı noktasını aşağıdaki değerlerle açan bir Azure ağ güvenlik grubu (NSG) kuralı ayarlayın:

   |Ayar |Değer | |
   | --- | --- | --- |
   |Öncelik |2000 | |
   |Adı |Custom_Dns  | |
   |Kaynak |VirtualNetwork | |
   |Hedef | VirtualNetwork | |
   |Hizmet | DNS (UDP/53) | |
   |Eylem | Allow  | |
   | | |

4. Her hizmet için uygulama bildiriminde ağ modunu belirtin: `<NetworkConfig NetworkType="Open">`. **Açık** ağ modu, hizmette ayrılmış bir IP adresi elde eden sonuçlara neden olur. Bir mod belirtilmemişse, hizmet varsayılan olarak **NAT** moduna alır. Aşağıdaki bildirim örneğinde, `NodeContainerServicePackage1` ve `NodeContainerServicePackage2` Hizmetleri her biri aynı bağlantı noktasını dinleyebilir (her iki hizmet de `Endpoint1` ' de dinleme yapılır). Açık ağ modu belirtildiğinde, `PortBinding` yapılandırması belirtilemez.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```

    Windows kümesi için bir uygulama içindeki hizmetler genelinde farklı ağ modlarını karıştırabilir ve eşleştirebilirsiniz. Bazı hizmetler, diğer kullanıcılar NAT modunu kullanırken açma modunu kullanabilir. Bir hizmet NAT modunu kullanacak şekilde yapılandırıldığında, hizmetin dinlediği bağlantı noktasının benzersiz olması gerekir.

    >[!NOTE]
    >Linux kümelerinde, farklı hizmetler için ağ modlarını karıştırma desteklenmez. 
    >

5. **Açık** mod seçildiğinde, hizmet bildiriminde yalnızca bir kod paketi olsa bile, hizmet bildirimindeki **uç nokta** tanımı, uç noktaya karşılık gelen kod paketine açıkça işaret etmelidir. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. Windows için, bir VM yeniden başlatması açık ağın yeniden oluşturulmasına neden olur. Bu, ağ yığınında temel bir sorunu azaltmaya yönelik olur. Varsayılan davranış, ağı yeniden oluşturmalıdır. Bu davranışın devre dışı olması gerekiyorsa, aşağıdaki yapılandırma ardından bir yapılandırma yükseltmesi tarafından kullanılabilir.

```json
"fabricSettings": [
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "SkipContainerNetworkResetOnReboot",
                            "value": "true"
                    }
                    ]
                }
            ],          
 ``` 
 
## <a name="next-steps"></a>Sonraki adımlar
* [Service Fabric uygulama modelini anlama](service-fabric-application-model.md)
* [Service Fabric hizmeti bildirim kaynakları hakkında daha fazla bilgi edinin](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
* [Windows Server 2016 ' de Windows kapsayıcısını Service Fabric dağıtma](service-fabric-get-started-containers.md)
* [Linux üzerinde Service Fabric bir Docker kapsayıcısı dağıtma](service-fabric-get-started-containers-linux.md)
