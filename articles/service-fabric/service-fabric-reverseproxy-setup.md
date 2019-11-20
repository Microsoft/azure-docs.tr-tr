---
title: Azure Service Fabric ters proxy ayarlama | Microsoft Docs
description: Service Fabric ters proxy 'sini ayarlamayı ve yapılandırmayı anlayın.
services: service-fabric
documentationcenter: na
author: jimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/13/2018
ms.author: pepogors
ms.openlocfilehash: a80eb1ba341a1b15da7ad59203a1d25284d03fd0
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933907"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Azure Service Fabric ters proxy ayarlama ve yapılandırma
Ters proxy, bir Service Fabric kümesinde çalışan mikro hizmetlerin HTTP uç noktalarına sahip diğer hizmetleri bulmasına ve iletişim kurmasına yardımcı olan isteğe bağlı bir Azure Service Fabric hizmetidir. Daha fazla bilgi edinmek için bkz. [Azure Service Fabric 'de ters proxy](service-fabric-reverseproxy.md). Bu makalede, kümenizde ters proxy 'nin nasıl ayarlanacağı ve yapılandırılacağı gösterilir. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Azure portal kullanarak ters proxy 'yi etkinleştirme

Azure portal, yeni bir Service Fabric kümesi oluşturduğunuzda ters proxy 'yi etkinleştirme seçeneği sunar. Mevcut bir kümeyi Portal üzerinden ters proxy kullanacak şekilde yükseltemezsiniz. 

[Azure Portal kullanarak bir küme oluşturduğunuzda](./service-fabric-cluster-creation-via-portal.md)ters proxy 'yi yapılandırmak için aşağıdakileri yaptığınızdan emin olun:

1. **2. Adım: küme yapılandırması**altında, **düğüm türü yapılandırması**altında, **ters proxy 'yi etkinleştir**' i seçin.

   ![Portalda ters proxy 'yi etkinleştir](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. Seçim Güvenli ters proxy 'yi yapılandırmak için bir SSL sertifikası yapılandırmanız gerekir. **Adım 3: güvenlik**, **küme güvenlik ayarlarını yapılandırma**bölümünde, **yapılandırma türü**altında **özel**' i seçin. Ardından, **ters proxy SSL sertifikası**' nın altında, **ters proxy Için SSL sertifikası Ekle** ' yi seçin ve sertifika ayrıntılarınızı girin.

   ![Portalda güvenli ters proxy 'yi yapılandırma](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Kümeyi oluştururken ters proxy 'yi bir sertifika ile yapılandırmamayı seçerseniz, daha sonra kümenin kaynak grubu için Kaynak Yöneticisi şablonu aracılığıyla bunu yapabilirsiniz. Daha fazla bilgi için bkz. [Azure Resource Manager şablonları aracılığıyla ters proxy 'Yi etkinleştirme](#enable-reverse-proxy-via-azure-resource-manager-templates).

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Azure Resource Manager şablonları aracılığıyla ters proxy 'yi etkinleştirme

Azure üzerindeki kümeler için, Service Fabric ' de ters proxy 'yi etkinleştirmek üzere Azure Resource Manager şablonunu kullanabilirsiniz. Kümeyi oluşturduğunuzda ters proxy 'yi etkinleştirebilir veya kümeyi daha sonra güncelleştirerek etkinleştirin. 

Yeni bir küme için [özel bir kaynak yöneticisi şablonu oluşturabilir](service-fabric-cluster-creation-via-arm.md) veya örnek bir şablon kullanabilirsiniz. 

GitHub 'daki [güvenli ters proxy örnek şablonlarında](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample) bir Azure kümesi için güvenli ters proxy 'yi yapılandırmanıza yardımcı olabilecek örnek kaynak yöneticisi şablonlar bulabilirsiniz. Güvenli ters proxy 'yi bir sertifikayla yapılandırmak ve sertifika rollover 'ı işlemek amacıyla kullanılacak yönergeler ve şablonlar için, BENIOKU dosyasındaki [güvenli bir kümede https ters proxy 'Yi yapılandırma](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) konusuna bakın.

Mevcut bir küme için, [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template), [POWERSHELL](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell)veya [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli)kullanarak kümenin kaynak grubu için Kaynak Yöneticisi şablonunu dışarı aktarabilirsiniz.

Kaynak Yöneticisi şablonunuz olduktan sonra, ters proxy 'yi aşağıdaki adımlarla etkinleştirebilirsiniz:

1. Şablonun [Parametreler bölümündeki](../azure-resource-manager/resource-group-authoring-templates.md) ters proxy için bir bağlantı noktası tanımlayın.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. [**Microsoft. ServiceFabric/kümeler**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [kaynak türü bölümündeki](../azure-resource-manager/resource-group-authoring-templates.md)NodeType nesnelerinin her biri için bağlantı noktasını belirtin.

    Bağlantı noktası, Smarproxyendpointport parametre adı tarafından tanımlanır.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Ters proxy 'nin bağlantı noktasında SSL sertifikalarını yapılandırmak için, sertifikayı **Microsoft. ServiceFabric/kümeler** [kaynak türü bölümündeki](../resource-group-authoring-templates.md) ***smarproxycertificate*** özelliğine ekleyin.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Küme sertifikasından farklı bir ters proxy sertifikası destekleme
 Ters proxy sertifikası, kümenin güvenliğini sağlayan sertifikadan farklıysa, daha önce belirtilen sertifikanın sanal makinede yüklü olması ve Service Fabric erişebilmesi için erişim denetim listesine (ACL) eklenmiş olması gerekir. Bu işlem, [**Microsoft. COMPUTE/virtualMachineScaleSets**](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachinescalesets) [kaynak türü bölümünde](../resource-group-authoring-templates.md)yapılabilir. Yükleme için sertifikayı osProfile ekleyin. Şablonun uzantı bölümü ACL 'deki sertifikayı güncelleştirebilir.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> Mevcut bir kümede ters proxy 'yi etkinleştirmek için küme sertifikasından farklı sertifikalar kullandığınızda, ters proxy 'yi etkinleştirmeden önce ters proxy sertifikasını yükledikten sonra kümedeki ACL 'yi güncelleştirin. 1-3 adımlarında ters proxy 'yi etkinleştirmek üzere bir dağıtımı başlatmaya başlamadan önce, daha önce bahsedilen ayarları kullanarak [Azure Resource Manager şablonu](service-fabric-cluster-creation-via-arm.md) dağıtımını doldurun.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Tek başına kümelerde ters proxy 'yi etkinleştir

Tek başına kümeler için, ClusterConfig. json dosyasında ters proxy 'yi etkinleştirirsiniz. Küme oluşturulurken ters proxy 'yi etkinleştirebilir veya var olan bir kümenin yapılandırmasını yükselterek. ClusterConfig. JSON dosyalarında kullanılabilen ayarlar hakkında daha fazla bilgi edinmek için bkz. [tek başına küme ayarları](./service-fabric-cluster-manifest.md).

Aşağıdaki adımlarda, ters proxy 'yi etkinleştirmek için kullanılacak ayarlar ve isteğe bağlı olarak, bir X. 509.440 sertifikasıyla ters proxy 'yi güvenli hale getirmek için kullanabileceğiniz ayarlar gösterilmektedir. 

1. Ters proxy 'yi etkinleştirmek için, küme yapılandırması 'ndaki **Özellikler** altında bulunan düğüm türü Için **Smarproxyendpointport** değerini ayarlayın. Aşağıdaki JSON, "NodeType0" türüne sahip düğümler için ters proxy uç noktası bağlantı noktasını 19081 olarak ayarlamayı gösterir:

   ```json
       "properties": {
          ... 
           "nodeTypes": [
               {
                   "name": "NodeType0",
                   ...
                   "reverseProxyEndpointPort": "19081",
                   ...
               }
           ],
          ...
       }
   ```
2. Seçim Güvenli bir ters proxy için, **Özellikler**altında **güvenlik** bölümünde bir sertifika yapılandırın. 
   - Geliştirme veya test ortamı için, **Smarproxycertificate** ayarını kullanabilirsiniz:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificate": {
                          "Thumbprint": "[Thumbprint]",
                          "ThumbprintSecondary": "[Thumbprint]",
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```
   - Bir üretim ortamında, **Smarproxycertificatecommonnames** ayarı önerilir:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificateCommonNames": {
                        "CommonNames": [
                            {
                              "CertificateCommonName": "[CertificateCommonName]"
                            }
                          ],
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```

   Tek başına bir küme için sertifika yapılandırma ve yönetme hakkında daha fazla bilgi edinmek ve ters proxy 'yi güvenli hale getirmek için kullanılan sertifikaları yapılandırma hakkında daha fazla ayrıntı için bkz. [x509 sertifika tabanlı güvenlik](./service-fabric-windows-cluster-x509-security.md).

Ters proxy 'yi etkinleştirmek için ClusterConfig. JSON dosyanızı değiştirdikten sonra, değişiklikleri kümenize göndermek için [küme yapılandırmasını yükseltme](service-fabric-cluster-config-upgrade-windows-server.md) bölümündeki yönergeleri izleyin.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Azure Load Balancer aracılığıyla, genel bir bağlantı noktasında ters proxy kullanıma sunma

Ters proxy 'yi bir Azure kümesi dışından ele almak için, ters proxy bağlantı noktası için Azure Load Balancer kuralları ve bir Azure sistem durumu araştırması ayarlayın. Bu adımlar, kümeyi oluşturduktan sonra herhangi bir zamanda Azure portal veya Kaynak Yöneticisi şablonu kullanılarak gerçekleştirilebilir. 

> [!WARNING]
> Load Balancer ' de ters proxy 'nin bağlantı noktasını yapılandırdığınızda, kümedeki bir HTTP uç noktasını kullanıma sunan tüm mikro hizmetler, küme dışından adreslenemez. Diğer bir deyişle, mikro hizmetlerin iç olduğu, belirlenen kötü amaçlı kullanıcı tarafından bulunabilir olabileceği anlamına gelir. Bu potansiyel olarak yararlanılabilen ciddi güvenlik açıkları sunmaktadır. Örneğin:
>
> * Kötü niyetli bir Kullanıcı, yeterince sıkı bir saldırı yüzeyi olmayan bir iç hizmeti tekrar çağırarak bir hizmet reddi saldırısı başlatabilir.
> * Kötü niyetli bir Kullanıcı, dahili bir hizmete hatalı oluşturulmuş paketler sunabilir ve bu durum istenmeden davranışa neden olabilir.
> * İç olması amaçlanan bir hizmet, küme dışındaki hizmetlere sunulmayan özel veya hassas bilgileri döndürebilir ve bu nedenle bu hassas bilgileri kötü amaçlı bir kullanıcıya ortaya çıkarabiliriz. 
>
> Ters proxy bağlantı noktasını genel yapmadan önce kümeniz ve üzerinde çalışan uygulamalar için olası güvenlik kollerini tam olarak anladığınızdan ve azaltmanıza emin olun. 
>

Tek başına bir küme için ters proxy 'yi genel kullanıma sunmak istiyorsanız, bunu yaptığınız yol, kümeyi barındıran sisteme bağlı olur ve bu makalenin kapsamı dışındadır. Ancak, ters proxy genel kullanıma sunulmasına ilişkin önceki uyarı, hala geçerlidir.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>Azure portal kullanarak ters proxy 'yi kullanıma sunma 

1. Azure portal, kümenizin kaynak grubuna ve ardından kümeniz için yük dengeleyiciye tıklayın.
2. Ters proxy bağlantı noktası için bir sistem durumu araştırması eklemek üzere, yük dengeleyici penceresinin sol bölmesinde, **Ayarlar**' ın altında, **sistem durumu araştırmaları**' na tıklayın. Sonra, sistem durumu araştırmaları penceresinin en üstündeki **Ekle** ' ye tıklayın ve ters proxy bağlantı noktasının ayrıntılarını girin ve ardından **Tamam**' a tıklayın. Varsayılan olarak, kümeyi oluştururken değiştirmediğiniz müddetçe, ters proxy bağlantı noktası 19081 ' dir.

   ![Ters proxy sistem durumu araştırmasını yapılandırma](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. Ters proxy bağlantı noktasını kullanıma sunmak için bir Load Balancer kuralı eklemek üzere, yük dengeleyici penceresinin sol bölmesinde, **Ayarlar**altında **Yük Dengeleme kuralları**' na tıklayın. Ardından Yük Dengeleme kuralları penceresinin en üstündeki **Ekle** ' ye tıklayın ve ters proxy bağlantı noktasının ayrıntılarını girin. **Bağlantı noktası** değerini, ters proxy 'nin açık olmasını istediğiniz bağlantı noktasına, **arka uç bağlantı noktası** değerini, ters proxy 'yi etkinleştirdiğinizde ayarladığınız bağlantı noktasına ve **sistem durumu araştırma** değerini, önceki adımda yapılandırdığınız durum araştırmasına ayarladığınızdan emin olun. Diğer alanları uygun şekilde ayarlayın ve **Tamam**' a tıklayın.

   ![Ters proxy için yük dengeleyici kuralını yapılandırma](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>Kaynak Yöneticisi şablonları aracılığıyla ters proxy 'yi kullanıma sunma

Aşağıdaki JSON, [Azure Resource Manager şablonları aracılığıyla ters proxy 'Yi etkinleştirmek](#enable-reverse-proxy-via-azure-resource-manager-templates)için kullanılan şablona başvurur. Kaynak Yöneticisi şablonu oluşturma veya var olan bir küme için bir şablonu dışarı aktarma hakkında bilgi için belgenin bu bölümüne bakın.  Değişiklikler [**Microsoft. Network/loadBalancers**](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers) [Resource Type bölümünde](../resource-group-authoring-templates.md)yapılır.

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>Doku ayarlarını kullanarak ters proxy davranışını özelleştirme

Ters proxy 'nin davranışını Azure 'da barındırılan kümeler için Kaynak Yöneticisi şablonundaki doku ayarları aracılığıyla veya tek başına kümeler için ClusterConfig. json dosyasında özelleştirebilirsiniz. Ters proxy davranışını denetleyen ayarlar, küme **özellikleri** bölümünde **Fabricsettings** bölümündeki [**applicationgateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) bölümünde bulunur. 

Örneğin, **DefaultHttpRequestTimeout** değerini, ters proxy 'ye yönelik isteklerin zaman AŞıMıNı aşağıdaki JSON 'daki gibi 180 saniyeye ayarlanacak şekilde ayarlayabilirsiniz:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "DefaultHttpRequestTimeout",
                   "value": "180"
                 }
               ]
             }
           ],
           ...
   }
   ``` 

Azure kümeleri için doku ayarlarını güncelleştirme hakkında daha fazla bilgi için bkz. [Kaynak Yöneticisi şablonları kullanarak küme ayarlarını özelleştirme](service-fabric-cluster-config-upgrade-azure.md). Tek başına kümeler için bkz. [tek başına kümeler için küme ayarlarını özelleştirme](service-fabric-cluster-config-upgrade-windows-server.md). 

Ters proxy ve hizmetler arasında güvenli iletişim kurmaya yardımcı olması için çeşitli yapı ayarları kullanılır. Bu ayarlar hakkında ayrıntılı bilgi için bkz. [ters ara sunucu ile güvenli bir hizmete bağlanma](service-fabric-reverseproxy-configure-secure-communication.md).

## <a name="next-steps"></a>Sonraki adımlar
* [Ters ara sunucu ile güvenli HTTP hizmeti için yönlendirmeyi ayarlama](service-fabric-reverseproxy-configure-secure-communication.md)
* Ters proxy yapılandırma seçenekleri için [Service Fabric kümesi ayarlarını özelleştirme konusunun Applicationgateway/http bölümüne](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)bakın.
