---
title: Azure 'da OpenShift kapsayıcı platformu 3,11 dağıtma
description: Azure 'da OpenShift kapsayıcı platformu 3,11 dağıtın.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 56607de57939be769b1951f0eee9078c46d610c0
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035456"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>Azure 'da OpenShift kapsayıcı platformu 3,11 dağıtma

Azure 'da OpenShift kapsayıcı platformu 3,11 dağıtmak için birkaç yöntemden birini kullanabilirsiniz:

- Gerekli Azure altyapı bileşenlerini el ile dağıtabilir ve ardından [OpenShift kapsayıcı platformu belgelerini](https://docs.openshift.com/container-platform)takip edebilirsiniz.
- OpenShift kapsayıcı platformu kümesinin dağıtımını kolaylaştıran mevcut bir [Kaynak Yöneticisi şablonunu](https://github.com/Microsoft/openshift-container-platform/) da kullanabilirsiniz.
- Diğer bir seçenek de [Azure Market Teklifini](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview)kullanmaktır.

Tüm seçenekler için, Red Hat aboneliği gerekir. Dağıtım sırasında Red Hat Enterprise Linux örneği Red Hat aboneliğine kaydedilir ve OpenShift kapsayıcı platformu için yetkilendirmeleri içeren havuz KIMLIĞINE eklenir.
Geçerli bir Red Hat abonelik Yöneticisi (RHSM) Kullanıcı adı, parola ve havuz KIMLIĞINIZ olduğundan emin olun. Etkinleştirme anahtarı, kuruluş KIMLIĞI ve havuz KIMLIĞI kullanabilirsiniz. https://access.redhat.com' de oturum açarak bu bilgileri doğrulayabilirsiniz.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>OpenShift kapsayıcı platformu Kaynak Yöneticisi 3,11 şablonunu kullanarak dağıtma

### <a name="private-clusters"></a>Özel kümeler

Özel OpenShift kümelerini dağıtmak, ana yük dengeleyici (Web Konsolu) veya Infra yük dengeleyici (yönlendirici) ile ilişkilendirilmiş genel bir IP 'yi kullanmaktan daha fazlasını gerektirir.  Özel bir küme genellikle özel bir DNS sunucusu (varsayılan Azure DNS değil), özel bir etki alanı adı (örneğin, contoso.com) ve önceden tanımlanmış sanal ağlar kullanır.  Özel kümeler için, Sanal ağınızı, uygun tüm alt ağlar ve DNS sunucusu ayarları önceden ile yapılandırmanız gerekir.  Ardından, küme tarafından kullanılacak mevcut alt ağı belirtmek için **Existingmastersubnetreference**, **existingInfraSubnetReference**, **Existingcnssubnetreference**ve **existingnodesubnetreference** kullanın.

Özel ana öğe seçilirse (**Masterclustertype**= Private), **masterprivateclusterıp**IÇIN statik bir özel IP belirtilmelidir.  Bu IP, ana yük dengeleyicinin ön ucuna atanacak.  IP, ana alt ağ için CıDR içinde olmalı ve kullanımda olmalıdır.  **Masterclusterdnstype** , "Custom" olarak ayarlanmalıdır ve **masterclusterdns**için ana DNS adı belirtilmelidir.  DNS adı statik özel IP ile eşlenmelidir ve ana düğümlerde konsola erişmek için kullanılacaktır.

Özel yönlendirici seçilmişse (**Routerclustertype**= Private), **routerprivateclusterıp**IÇIN statik bir özel IP belirtilmelidir.  Bu IP, Infra yük dengeleyicinin ön ucuna atanır.  IP, Infra alt ağı için CIDR içinde olmalı ve kullanımda olmalıdır.  **Routingsubdomaintype** , "Custom" olarak ve **yönlendirme IÇIN joker**karakter DNS adı belirtilmelidir.  

Özel ana bilgisayarlar ve özel yönlendirici seçilirse, özel etki alanı adının de **domainName** için girilmesi gerekir

Dağıtım başarılı olduktan sonra savunma düğümü, SSH ile kullanabileceğiniz tek bir genel IP 'nin bulunduğu düğümdür.  Ana düğümler genel erişim için yapılandırılmış olsa bile, SSH erişimi için sunulmazlar.

Kaynak Yöneticisi şablonu kullanarak dağıtmak için, giriş parametrelerini sağlamak üzere bir parametreler dosyası kullanırsınız. Dağıtımı daha fazla özelleştirmek için GitHub deposunun çatalını yapın ve uygun öğeleri değiştirin.

Bazı yaygın özelleştirme seçenekleri şunlardır, ancak bunlarla sınırlı değildir:

- Savunma VM boyutu (azuredeploy. JSON içinde değişken)
- Adlandırma kuralları (azuredeploy. JSON içindeki değişkenler)
- OpenShift küme özellikleri, Hosts dosyası aracılığıyla değiştirildi (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Parametreler dosyasını yapılandırma

[OpenShift kapsayıcı platformu şablonunda](https://github.com/Microsoft/openshift-container-platform) , OpenShift kapsayıcı platformunun farklı sürümleri için kullanılabilen birden çok dal vardır.  Gereksinimlerinize bağlı olarak, depoyu doğrudan depodan dağıtabilir veya dağıtmadan önce şablonlarda veya betiklerinizde özel değişiklikler yapabilirsiniz.

Daha önce `aadClientId` parametresi için oluşturduğunuz hizmet sorumlusunun `appId` değerini kullanın.

Aşağıdaki örnek, tüm gerekli girişlerle azuredeploy. Parameters. JSON adlı bir parametre dosyasını gösterir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
        },
        "storageKind": {
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

Parametreleri kendi özel bilgileriniz ile değiştirin.

Farklı yayınlar farklı parametrelere sahip olabilir, bu nedenle kullandığınız dal için gerekli parametreleri doğrulayın.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy. Parameters. JSON dosyası açıklanıyor

| Özellik | Açıklama | Geçerli seçenekler | Varsayılan Değer |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | Yapıtlar URL 'SI (JSON, betikler, vb.) |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Kaynakların dağıtılacağı Azure bölgesi |  |  |
| `masterVmSize` | Ana VM 'nin boyutu. Azuredeploy. json dosyasında listelenen izin verilen VM boyutlarından birini seçin |  | Standard_E2s_v3 |
| `infraVmSize` | Infra VM 'sinin boyutu. Azuredeploy. json dosyasında listelenen izin verilen VM boyutlarından birini seçin |  | Standard_D4s_v3 |
| `nodeVmSize` | Uygulama düğümü VM 'sinin boyutu. Azuredeploy. json dosyasında listelenen izin verilen VM boyutlarından birini seçin |  | Standard_D4s_v3 |
| `cnsVmSize` | Kapsayıcı yerel depolama (CNS) düğüm VM 'sinin boyutu. Azuredeploy. json dosyasında listelenen izin verilen VM boyutlarından birini seçin |  | Standard_E4s_v3 |
| `osImageType` | Kullanılacak RHEL görüntüsü. defaultgallery: Isteğe bağlı; Market: üçüncü taraf görüntü | defaultgallery <br> Market | defaultgallery |
| `marketplaceOsImage` | Market `osImageType`, Market teklifinin ' Yayımcı ', ' teklif ', ' SKU ' ve ' sürüm ' için uygun değerleri girin. Bu parametre bir nesne türüdür |  |  |
| `storageKind` | Kullanılacak depolamanın türü  | lebilmesi<br> yönetilmeyen | lebilmesi |
| `openshiftClusterPrefix` | Tüm düğümlerde konak adlarını yapılandırmak için kullanılan küme öneki.  1 ila 20 karakter arasında |  | MyCluster |
| `minoVersion` | Dağıtım için OpenShift kapsayıcı platformu 3,11 ' ün ikincil sürümü |  | 69 |
| `masterInstanceCount` | Dağıtılacak ana düğüm sayısı | 1, 3, 5 | 3 |
| `infraInstanceCount` | Dağıtılacak Infra düğümlerinin sayısı | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Dağıtılacak düğüm sayısı | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Dağıtılacak CNS node sayısı | 3, 4 | 3 |
| `osDiskSize` | VM için işletim sistemi diskinin boyutu (GB olarak) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Docker birimi için düğümlere iliştirilecek veri diski boyutu (GB olarak) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | GlusterFS tarafından kullanılmak üzere CNS Nodes 'a iliştirilecek veri diski boyutu (GB olarak | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Hem işletim sistemi (VM) oturum açma hem de ilk OpenShift kullanıcısı için Yönetici Kullanıcı adı |  | ocpadmin |
| `enableMetrics` | Ölçümleri etkinleştirin. Ölçümler daha fazla kaynak gerektirir, bu nedenle Infra sanal makinesi için uygun boyutu seçin | true <br> yanlış | yanlış |
| `enableLogging` | Günlüğe kaydetmeyi etkinleştirin. elagra Search Pod, 8 GB RAM gerektirdiğinden Infra VM için uygun boyutu seçin | true <br> yanlış | yanlış |
| `enableCNS` | Kapsayıcı yerel depolamayı etkinleştir | true <br> yanlış | yanlış |
| `rhsmUsernameOrOrgId` | Red Hat abonelik Yöneticisi Kullanıcı adı veya kuruluş KIMLIĞI |  |  |
| `rhsmPoolId` | İşlem düğümleri için OpenShift yetkilendirmelerinizi içeren Red Hat abonelik Yöneticisi havuz KIMLIĞI |  |  |
| `rhsmBrokerPoolId` | Ana bilgisayarlar ve Infra düğümleri için OpenShift yetkilendirmelerinizi içeren Red Hat abonelik Yöneticisi havuz kimliği. Farklı havuz kimliğiniz yoksa, ' rhsmPoolId ' ile aynı havuz KIMLIĞINI girin |  |
| `sshPublicKey` | SSH ortak anahtarınızı buraya kopyalayın |  |  |
| `keyVaultSubscriptionId` | Key Vault içeren aboneliğin abonelik KIMLIĞI |  |  |
| `keyVaultResourceGroup` | Key Vault içeren kaynak grubunun adı |  |  |
| `keyVaultName` | Oluşturduğunuz Key Vault adı |  |  |
| `enableAzure` | Azure bulut sağlayıcısını etkinleştir | true <br> yanlış | true |
| `aadClientId` | Hizmet sorumlusu için uygulama KIMLIĞI olarak da bilinen Azure Active Directory Istemci KIMLIĞI |  |  |
| `domainName` | Kullanılacak özel etki alanı adının adı (varsa). Tam özel küme dağıtmadığı takdirde "none" olarak ayarlayın |  | yok |
| `masterClusterDnsType` | OpenShift web konsolunun etki alanı türü. ' default ', ana Infra genel IP DNS etiketini kullanır. ' Custom ' kendi adınızı tanımlamanızı sağlar | default <br> Özel | default |
| `masterClusterDns` | `masterClusterDnsType` için ' Custom ' seçtiyseniz OpenShift web konsoluna erişmek için kullanılacak özel DNS adı |  | console.contoso.com |
| `routingSubDomainType` | ' Nipio ' olarak ayarlandıysa `routingSubDomain`, nip.io kullanacaktır.  Yönlendirme için kullanmak istediğiniz kendi etki alanınız varsa ' Custom ' kullanın | nipio <br> Özel | nipio |
| `routingSubDomain` | `routingSubDomainType` için ' Custom ' seçtiyseniz, yönlendirme için kullanmak istediğiniz joker karakter DNS adı |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Mevcut bir sanal ağın kullanılıp kullanılmayacağını seçin veya yeni bir sanal ağ oluşturun | Mevcut <br> yeni | yeni |
| `virtualNetworkResourceGroupName` | `virtualNetworkNewOrExisting` için ' yeni ' seçtiyseniz yeni sanal ağın kaynak grubunun adı |  | resourceGroup().name |
| `virtualNetworkName` | `virtualNetworkNewOrExisting` için ' yeni ' seçtiyseniz oluşturulacak yeni sanal ağın adı |  | openshiftvnet |
| `addressPrefixes` | Yeni sanal ağın adres ön eki |  | 10.0.0.0/14 |
| `masterSubnetName` | Ana alt ağın adı |  | mastersubnet |
| `masterSubnetPrefix` | Ana alt ağ için kullanılan CıDR, Addresspredüzeltmesini alt kümesi olmalıdır |  | 10.1.0.0/16 |
| `infraSubnetName` | Infra alt ağının adı |  | infrasubnet |
| `infraSubnetPrefix` | Infra alt ağı için kullanılan CIDR-addresspredüzeltmesinin bir alt kümesi olması gerekir |  | 10.2.0.0/16 |
| `nodeSubnetName` | Düğüm alt ağının adı |  | nodesubnet |
| `nodeSubnetPrefix` | Düğüm alt ağı için kullanılan CıDR-Addresspredüzeltmesini 'nin bir alt kümesi olması gerekir |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Ana düğümler için mevcut alt ağa tam başvuru. Yeni vNet/alt ağ oluşturulmadığında gerekli değildir |  |  |
| `existingInfraSubnetReference` | Infra düğümleri için mevcut alt ağa tam başvuru. Yeni vNet/alt ağ oluşturulmadığında gerekli değildir |  |  |
| `existingCnsSubnetReference` | CNS Nodes için mevcut alt ağa tam başvuru. Yeni vNet/alt ağ oluşturulmadığında gerekli değildir |  |  |
| `existingNodeSubnetReference` | İşlem düğümleri için mevcut alt ağa tam başvuru. Yeni vNet/alt ağ oluşturulmadığında gerekli değildir |  |  |
| `masterClusterType` | Kümenin özel veya ortak ana düğümler kullanıp kullanmadığını belirtin. Özel seçilirse, ana düğümler genel bir IP aracılığıyla Internet 'e gösterilmez. Bunun yerine, `masterPrivateClusterIp` belirtilen özel IP 'yi kullanır | geneldir <br> özel | geneldir |
| `masterPrivateClusterIp` | Özel ana düğümler seçilirse, ana düğümlerin iç yük dengeleyici tarafından kullanılmak üzere özel bir IP adresinin belirtilmesi gerekir. Bu statik IP, ana alt ağ için CıDR bloğunda olmalıdır ve zaten kullanımda olmalıdır. Ortak ana düğümler seçilirse, bu değer kullanılmaz ancak yine de belirtilmesi gerekir |  | 10.1.0.200 |
| `routerClusterType` | Kümenin özel veya genel Infra düğümleri kullanıp kullanmayacağını belirtin. Özel seçilirse, Infra düğümleri Internet 'e genel bir IP üzerinden gösterilmez. Bunun yerine, `routerPrivateClusterIp` belirtilen özel IP 'yi kullanır | geneldir <br> özel | geneldir |
| `routerPrivateClusterIp` | Özel Infra düğümleri seçilirse, Infra düğümleri için iç yük dengeleyici tarafından kullanılmak üzere özel bir IP adresinin belirtilmesi gerekir. Bu statik IP, ana alt ağ için CıDR bloğunda olmalıdır ve zaten kullanımda olmalıdır. Ortak Infra düğümleri seçilirse, bu değer kullanılmaz ancak yine de belirtilmesi gerekir |  | 10.2.0.200 |
| `routingCertType` | Yönlendirme etki alanı veya varsayılan otomatik olarak imzalanan sertifika için özel sertifika kullan- **özel sertifikalar** bölümündeki yönergeleri izleyin | selfsigned <br> Özel | selfsigned |
| `masterCertType` | Ana etki alanı için özel sertifika veya varsayılan otomatik olarak imzalanan sertifika kullan- **özel sertifikalar** bölümündeki yönergeleri izleyin | selfsigned <br> Özel | selfsigned |

<br>

### <a name="deploy-using-azure-cli"></a>Azure CLI’yi kullanarak dağıtma

> [!NOTE] 
> Aşağıdaki komut, Azure CLı 2.0.8 veya üstünü gerektirir. CLı sürümünü `az --version` komutuyla doğrulayabilirsiniz. CLı sürümünü güncelleştirmek için bkz. [Azure CLI 'Yı yüklemek](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

Aşağıdaki örnek, OpenShift kümesini ve tüm ilgili kaynakları openkaydırıcı adlı bir kaynak grubunda myOpenShiftCluster dağıtım adı ile dağıtır. Şablona doğrudan GitHub deposundan başvurulur ve azuredeploy. Parameters. JSON dosyası adlı bir yerel parametreler dosyası kullanılır.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Dağıtım, dağıtılan toplam düğüm sayısına ve yapılandırılmış seçeneklere bağlı olarak en az 60 dakika sürer. Dağıtım tamamlandığında, OpenShift konsolunun savunma DNS FQDN 'si ve URL 'si terminale yazdırılır.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Dağıtımın tamamlanmasını bekleyen komut satırını bağlamak istemiyorsanız, grup dağıtımı seçeneklerinden biri olarak `--no-wait` ekleyin. Dağıtımdan alınan çıkış, kaynak grubunun dağıtım bölümündeki Azure portal alabilir.

## <a name="connect-to-the-openshift-cluster"></a>OpenShift kümesine bağlanma

Dağıtım tamamlandığında, dağıtımın çıkış bölümünden bağlantıyı alın. **OpenShift konsol URL**'sini kullanarak, tarayıcınızla OpenShift konsoluna bağlanın. Ayrıca, savunma ana bilgisayarına SSH de ekleyebilirsiniz. Aşağıda Yönetici Kullanıcı adının kümeyöneticisi olduğu ve savunma genel IP DNS FQDN 'sinin bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com olduğu bir örnek verilmiştir:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak grubunu, OpenShift kümesini ve artık gerekli olmadığında tüm ilgili kaynakları kaldırmak için [az Group Delete](/cli/azure/group) komutunu kullanın.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Sonraki adımlar

- [Dağıtım sonrası görevler](./openshift-container-platform-3x-post-deployment.md)
- [Azure 'da OpenShift dağıtımında sorun giderme](./openshift-container-platform-3x-troubleshooting.md)
- [OpenShift kapsayıcı platformu ile çalışmaya başlama](https://docs.openshift.com)
