---
title: Azure sanal ağ kapsayıcı ağı dağıtma | Microsoft Docs
description: Kendi dağıttığınız ve ACS-Engine kullanarak dağıttığınız Kubernetes kümelerine ek olarak Docker kapsayıcıları için Azure Sanal Ağ kapsayıcı ağı arabirimi (CNI) eklentisini dağıtmayı öğrenin.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/18/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 5146675b6eefd11fc1e6875ed9009ece92753ffb
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028097"
---
# <a name="deploy-the-azure-virtual-network-container-network-interface-plug-in"></a>Azure Sanal Ağ kapsayıcı ağı arabirimi eklentisini dağıtma

Azure Sanal Ağ kapsayıcı ağı arabirimi (CNI) eklentisi bir Azure sanal makinesi yükler ve Kubernetes podları ile Docker kapsayıcılarına sanal ağ özellikleri kazandırır. Eklenti hakkında daha fazla bilgi edinmek için bkz. [Kapsayıcıların Azure Sanal Ağ özelliklerini kullanmasını sağlama](container-networking-overview.md). Buna ek olarak eklenti, AKS kapsayıcılarını otomatik olarak bir sanal ağa ekleyen [Gelişmiş Ağ](../aks/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) seçeneği sayesinde Azure Kubernetes Service (AKS) ile birlikte kullanılabilir.

## <a name="deploy-plug-in-for-acs-engine-kubernetes-cluster"></a>Eklentiyi ACS-Engine Kubernetes kümesi için dağıtma

ACS-Engine, Kubernetes kümesini bir Azure Resource Manager şablonuyla dağıtır. Küme yapılandırması, şablon oluşturma sırasında araca geçirilen bir JSON dosyasında belirtilir. Desteklenen küme ayarlarının tam listesi ve açıklamaları hakkında daha fazla bilgi edinmek için bkz. [Microsoft Azure Container Service Engine - Küme Tanımı](https://github.com/Azure/acs-engine/blob/master/docs/clusterdefinition.md). Eklenti, ACS-Engine kullanılarak oluşturulan kümeler için varsayılan ağ eklentisidir. Eklenti yapılandırma sırasında aşağıdaki ağ yapılandırma ayarlarına dikkat edilmesi gerekir:

  | Ayar                              | Açıklama                                                                                                           |
  |--------------------------------------|------------------------------------------------------------------------------------------------------                 |
  | firstConsecutiveStaticIP             | Ana düğüme ayrılmış olan IP adresidir. Bu ayar zorunludur.                                     |
  | kubernetesConfig altında clusterSubnet | Kümenin dağıtıldığı ve podlara IP adreslerinin ayrıldığı sanal ağ alt ağının CIDR değeridir   |
  | masterProfile altında vnetSubnetId     | Kümenin dağıtılacağı alt ağın Azure Resource Manager kaynak kimliğini belirtir                    |
  | vnetCidr                             | Kümenin dağıtıldığı sanal ağın CIDR değeridir                                                             |
  | kubeletConfig altında max-Pods         | Aracı sanal makinelerindeki maksimum pod sayısıdır. Eklentide varsayılan değer 30'dur. En fazla 250 değerini kullanabilirsiniz  |

### <a name="example-configuration"></a>Örnek yapılandırma

Aşağıdaki JSON örneği, şu özelliklere sahip bir kümeye aittir:
-   1 Ana düğüm ve 2 Aracı düğümü 
-   *KubeClusterSubnet* (10.0.0.0/20) adlı bir alt ağa dağıtılmış, Ana ve Aracı düğümler de bu alt ağda bulunuyor.

```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
        "clusterSubnet": "10.0.0.0/20" --> Subnet allocated for the cluster
      }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "ACSKubeMaster",
      "vmSize": "Standard_A2",
      "vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<Vnet Name>/subnets/KubeClusterSubnet",
      "firstConsecutiveStaticIP": "10.0.1.50", --> IP address allocated to the Master node
"vnetCidr": "10.0.0.0/16" --> Virtual network address space
    },
    "agentPoolProfiles": [
      {
        "name": "k8sagentpoo1",
        "count": 2,
        "vmSize": "Standard_A2_v2",
"vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<VNet Name>/subnets/KubeClusterSubnet",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
    "linuxProfile": {
      "adminUsername": "KubeServerAdmin",
      "ssh": {
        "publicKeys": [
          {…}
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "dd438987-aa12-4754-b47d-375811889714",
      "secret": "azure123"
    }
  }
}
```

## <a name="deploy-plug-in-for-a-kubernetes-cluster"></a>Eklentiyi Kubernetes kümesi için dağıtma

Eklentiyi bir Kubernetes kümesindeki tüm Azure sanal makinelerine yüklemek için aşağıdaki adımları izleyin:

1. [Eklentiyi indirin ve yükleyin](#download-and-install-the-plug-in).
2. Her sanal makinede pod IP adreslerinin atanacağı sanal ağ IP adresi havuzunu önceden ayırın. Her Azure sanal makinesinin ağ arabirimlerinde birincil sanal ağ özel IP adresi bulunur. Podlar için IP adresi havuzu aşağıdaki seçeneklerden biri kullanılarak sanal makina ağ arabirimine ikincil adresler (*ipconfigs*) olarak eklenir:

   - **CLI**: [Azure CLI kullanarak birden çok IP adresi atama](virtual-network-multiple-ip-addresses-cli.md)
   - **PowerShell**: [PowerShell kullanarak birden çok IP adresi atama](virtual-network-multiple-ip-addresses-powershell.md)
   - **Portal**: [Azure Portal kullanarak birden çok IP adresi atama](virtual-network-multiple-ip-addresses-portal.md)
   - **Azure Resource Manager şablonu**: [şablonları kullanarak birden çok IP adresi atama](virtual-network-multiple-ip-addresses-template.md)

   Sanal makinede kullanmayı beklediğiniz tüm podlar için yeterli IP adresi eklediğinizden emin olun.

3. Küme oluşturma sırasında Kubelet'e `–network-plugin=cni` komut satırı seçeneğini geçirerek kümeniz için ağ oluşturma eklentisini seçin. Kubernetes eklentiyi ve yapılandırma dosyasını varsayılan olarak yüklü olan dizinlerde arar.
4. Podlarınızın internete erişmesini istiyorsanız Linux sanal makinelerinizde kaynak-NAT internet trafiğine aşağıdaki *iptables* kuralını ekleyin. Aşağıdaki örnekte IP aralığı 10.0.0.0/8 olarak belirtilmiştir.

   ```bash
   iptables -t nat -A POSTROUTING -m iprange ! --dst-range 168.63.129.16 -m
   addrtype ! --dst-type local ! -d 10.0.0.0/8 -j MASQUERADE
   ```

   Kurallar belirtilen IP aralıklarına gönderilmeyen trafik için NAT uygular. Önceki aralıkların dışındaki tüm trafiğin internet trafiği olduğu kabul edilir. Sanal makinenin sanal ağının, eşlenen sanal ağların ve şirket içi ağların IP aralıklarını belirtebilirsiniz.

   Windows sanal makineleri otomatik olarak sanal makinenin ait olduğu alt ağın dışında bir hedefe sahip olan NAT trafiği için kaynak oluşturur. Özel IP aralığı belirtmek mümkün değildir.

Yukarıdaki adımları tamamladıktan sonra Kubernetes Aracı sanal makinelerinde oluşturulan podlara otomatik olarak sanal ağdan özel IP adresleri atanır.

## <a name="deploy-plug-in-for-docker-containers"></a>Eklentiyi Docker kapsayıcıları için dağıtma

1. [Eklentiyi indirin ve yükleyin](#download-and-install-the-plug-in).
2. Şu komutu kullanarak Docker kapsayıcılarını oluşturun:

   ```
   ./docker-run.sh \<container-name\> \<container-namespace\> \<image\>
   ```

Kapsayıcılar otomatik olarak atanan havuzdan IP adreslerini almaya başlar. Docker kapsayıcılarına giden trafikte yük dengeleme gerçekleştirmek istiyorsanız bu kapsayıcıları yazılım yük dengeleyici arkasına yerleştirmeniz ve sanal makine için ilke ve yoklama oluşturduğunuz şekilde bir yük dengeleyici yoklaması yapılandırmanız gerekir.

### <a name="cni-network-configuration-file"></a>CLI ağ yapılandırma dosyası

CLI ağ yapılandırma dosyası, JSON biçiminde ifade edilir. Varsayılan konumu Linux'ta `/etc/cni/net.d`, Windows'da ise `c:\cni\netconf` şeklindedir. Bu dosya eklentinin yapılandırmasını belirtir ve Windows ile Linux'ta farklıdır. Aşağıda JSON biçiminde örnek bir Linux yapılandırma dosyası verilmiş ve önemli ayarların bazıları açıklanmıştır. Dosyada değişiklik yapmanıza gerek yoktur:

```json
{
       "cniVersion":"0.3.0",
       "name":"azure",
       "plugins":[
          {
             "type":"azure-vnet",
             "mode":"bridge",
             "bridge":"azure0",
             "ipam":{
                "type":"azure-vnet-ipam"
             }
          },
          {
             "type":"portmap",
             "capabilities":{
                "portMappings":true
             },
             "snat":true
          }
       ]
}
```

#### <a name="settings-explanation"></a>Ayarların açıklaması

- **Cniversion**: Azure sanal ağ CNI eklentileri, [CNI belirtiminin](https://github.com/containernetworking/cni/blob/master/SPEC.md)0.3.0 ve 0.3.1 sürümlerini destekler.
- **name**: Ağın adıdır. Bu özellik herhangi bir benzersiz değer olarak ayarlanabilir.
- **type**: Ağ eklentisinin adıdır. *azure-vnet* olarak ayarlanmıştır.
- **mode**: Çalıştırma modudur. Bu alan isteğe bağlıdır. Yalnızca "bridge" modu desteklenir. Daha fazla bilgi için bkz. [işletimsel modlar](https://github.com/Azure/azure-container-networking/blob/master/docs/network.md).
- **bridge**: Kapsayıcıları sanal ağa bağlamak için kullanılacak köprünün adıdır. Bu alan isteğe bağlıdır. Belirtilmezse eklenti otomatik olarak ana arabirim dizinine göre benzersiz bir ad belirler.
- **ipam type**: IPAM eklentisinin adıdır. Her zaman *azure-vnet-ipam* olarak ayarlanır.

## <a name="download-and-install-the-plug-in"></a>Eklentiyi indirme ve yükleme

Eklentiyi [GitHub](https://github.com/Azure/azure-container-networking/releases)'dan indirin. Kullandığınız platforma uygun en son sürümü indirin:

- **Linux**: [azure-vnet-cni-linux-amd64-\<sürüm no.\>.tgz](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-linux-amd64-v1.0.12-rc3.tgz)
- **Windows**: [azure-vnet-cni-windows-amd64-\<sürüm no.\>.zip](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-windows-amd64-v1.0.12-rc3.zip)

[Linux](https://github.com/Azure/azure-container-networking/blob/master/scripts/install-cni-plugin.sh) veya [Windows](https://github.com/Azure/azure-container-networking/blob/master/scripts/Install-CniPlugin.ps1) yükleme betiğini bilgisayarınıza kopyalayın. Betiği bilgisayarınızdaki bir `scripts` dizinine kopyalayın ve dosyayı Linux için `install-cni-plugin.sh`, Windows için ise `install-cni-plugin.ps1` olarak adlandırın. Eklentiyi yüklemek için platformunuza uygun betiği çalıştırın ve kullandığınız eklentinin sürümünü belirtin. Örneğin *v1.0.12-rc3* değerini belirtebilirsiniz:

   ```bash
   \$scripts/install-cni-plugin.sh [version]
   ```

   ```powershell
   scripts\\ install-cni-plugin.ps1 [version]
   ```

Betik, eklentiyi Linux için `/opt/cni/bin`, Windows için ise `c:\cni\bin` konumuna yükler. Yüklenen eklenti, yükleme sonrasında çalışan basit bir ağ yapılandırma dosyasına sahiptir. Güncelleştirilmesi gerekmez. Dosyadaki ayarlar hakkında daha fazla bilgi edinmek için bkz. [CNI ağ yapılandırma dosyası](#cni-network-configuration-file).