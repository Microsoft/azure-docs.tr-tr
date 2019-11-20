---
title: Azure 'da Windows çalıştıran bir Service Fabric kümesi oluşturma | Microsoft Docs
description: Bu öğreticide, PowerShell kullanarak bir Azure sanal ağı ve ağ güvenlik grubuna Windows Service Fabric kümesi dağıtmayı öğreneceksiniz.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 28571584fbd82b245e85e2ebe5b1d282ab5ae979
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177977"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>Öğretici: Windows çalıştıran bir Service Fabric kümesini Azure sanal ağına dağıtma

Bu öğretici, bir dizinin birinci bölümüdür. Windows çalıştıran bir Azure Service Fabric kümesini PowerShell ve şablon kullanarak bir [Azure sanal ağına](../virtual-network/virtual-networks-overview.md) ve [ağ güvenlik grubuna](../virtual-network/virtual-networks-nsg.md) dağıtmayı öğrenirsiniz. İşiniz bittiğinde, bulutta çalışan ve uygulama dağıtabileceğiniz bir kümeniz vardır. Azure CLı kullanan bir Linux kümesi oluşturmak için bkz. [Azure 'da güvenli bir Linux kümesi oluşturma](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Bu öğreticide bir üretim senaryosu açıklanır. Sınama amacıyla daha küçük bir küme oluşturmak istiyorsanız, bkz. [test kümesi oluşturma](./scripts/service-fabric-powershell-create-secure-cluster-cert.md).

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * PowerShell kullanarak Azure’da VNet oluşturma
> * Anahtar kasası oluşturma ve karşıya sertifika yükleme
> * Azure Active Directory kimlik doğrulamasını ayarla
> * Tanılama toplamayı yapılandır
> * EventStore hizmetini ayarlama
> * Azure Izleyici günlüklerini ayarlama
> * Azure PowerShell’de güvenli bir Service Fabric kümesi oluşturma
> * X.509 sertifikasıyla kümenin güvenliğini sağlama
> * PowerShell kullanarak kümeye bağlanma
> * Bir kümeyi kaldırma

Bu öğretici dizisinde şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * Azure’da güvenli bir küme oluşturma
> * [Bir kümeyi izleme](service-fabric-tutorial-monitor-cluster.md)
> * [Bir kümenin ölçeğini daraltma veya genişletme](service-fabric-tutorial-scale-cluster.md)
> * [Bir kümenin çalışma zamanını yükseltme](service-fabric-tutorial-upgrade-cluster.md)
> * [Küme silme](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce:

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
* [SERVICE fabrıc SDK ve PowerShell modülünü](service-fabric-get-started.md)yükler.
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)'i yükler.
* [Azure kümelerinin](service-fabric-azure-clusters-overview.md)temel kavramlarını gözden geçirin.
* Üretim kümesi dağıtımını [planlayın ve hazırlayın](service-fabric-cluster-azure-deployment-preparation.md) .

Aşağıdaki yordamlar yedi düğümlü Service Fabric kümesi oluşturur. Azure 'da bir Service Fabric kümesi çalıştırarak oluşan maliyeti hesaplamak için [Azure Fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/) ' nı kullanın.

## <a name="download-and-explore-the-template"></a>Şablonu indirin ve keşfedin

Aşağıdaki Azure Resource Manager şablon dosyalarını indirin:

* [azuredeploy. JSON][template]
* [azuredeploy. Parameters. JSON][parameters]

Bu şablon, bir sanal ağa ve bir ağ güvenlik grubuna yedi sanal makine ve üç düğümlü tür güvenli bir küme dağıtır.  Diğer örnek şablonlar [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates)'da bulunabilir. [Azuredeploy. JSON][template] , aşağıdakiler dahil olmak üzere bir dizi kaynak dağıtır.

### <a name="service-fabric-cluster"></a>Service Fabric kümesi

**Microsoft.ServiceFabric/clusters** kaynağında şu özelliklere sahip bir Windows kümesi yapılandırılır:

* Üç düğüm türü.
* Birincil düğüm türünde (şablon parametrelerinde yapılandırılabilir) beş düğüm ve diğer iki düğüm türünün her birindeki bir düğüm.
* İşletim sistemi: kapsayıcılar içeren Windows Server 2016 Datacenter (şablon parametrelerinde yapılandırılabilir).
* Sertifika güvenli (şablon parametrelerinde yapılandırılabilir).
* [Ters ara sunucu](service-fabric-reverseproxy.md) etkin.
* [DNS hizmeti](service-fabric-dnsservice.md) etkinleştirildi.
* Bronz [dayanıklılık düzeyi](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) (şablon parametrelerinde yapılandırılabilir).
* Gümüş [güvenilirlik düzeyi](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) (şablon parametrelerinde yapılandırılabilir).
* İstemci bağlantı uç noktası: 19000 (şablon parametrelerinde yapılandırılabilir).
* HTTP ağ geçidi uç noktası: 19080 (şablon parametrelerinde yapılandırılabilir).

### <a name="azure-load-balancer"></a>Azure Load Balancer

**Microsoft. Network/loadBalancers** kaynağında, bir yük dengeleyici yapılandırılır. Yoklamalar ve kurallar aşağıdaki bağlantı noktaları için ayarlanır:

* İstemci bağlantı uç noktası: 19000
* HTTP ağ geçidi uç noktası: 19080
* Uygulama bağlantı noktası: 80
* Uygulama bağlantı noktası: 443
* Service Fabric ters proxy’si: 19081

Diğer uygulama bağlantı noktaları gerekliyse, içindeki trafiğe izin vermek için **Microsoft. Network/loadBalancers** kaynağını ve **Microsoft. Network/networksecuritygroups** kaynağını ayarlamanız gerekir.

### <a name="virtual-network-subnet-and-network-security-group"></a>Sanal ağ, alt ağ ve ağ güvenlik grubu

Sanal ağ, alt ağ ve ağ güvenlik grubunun adları şablon parametrelerinde bildirilir. Sanal ağın ve alt ağın adres alanları da şablon parametrelerinde bildirilir ve **Microsoft.Network/virtualNetworks** kaynağında yapılandırılır:

* Sanal ağ adres alanı: 172.16.0.0/20
* Service Fabric alt ağ adres alanı: 172.16.2.0/23

**Microsoft.Network/networkSecurityGroups** kaynağında aşağıdaki gelen trafik kuralları etkinleştirilir. Şablon değişkenlerini değiştirerek bağlantı noktası değerlerini değiştirebilirsiniz.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* Internodecommunication: 1025, 1026, 1027
* Kısa ömürlü bağlantı noktası aralığı: 49152-65534 (en az 256 bağlantı noktası gerekir).
* Uygulamanın kullanımına yönelik bağlantı noktaları: 80 ve 443
* Uygulama bağlantı noktası aralığı: 49152-65534 (hizmetten hizmete iletişim için kullanılır. Diğer bağlantı noktaları yük dengeleyicide açılmaz).
* Diğer tüm bağlantı noktalarını engelleyin

Diğer uygulama bağlantı noktaları gerekliyse, içindeki trafiğe izin vermek için **Microsoft. Network/loadBalancers** kaynağını ve **Microsoft. Network/networksecuritygroups** kaynağını ayarlamanız gerekir.

### <a name="windows-defender"></a>Windows Defender
Varsayılan olarak, [Windows Defender virüsten koruma programı](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) windows Server 2016 ' de yüklü ve çalışır. Kullanıcı arabirimi bazı SKU 'Larda varsayılan olarak yüklenir, ancak gerekli değildir. Şablonda belirtilen her düğüm türü/VM Ölçek kümesi için [Azure VM kötü amaçlı yazılımdan koruma uzantısı](/azure/virtual-machines/extensions/iaas-antimalware-windows) , Service Fabric dizinlerini ve süreçlerini dışlamak için kullanılır:

```json
{
"name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
"properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
        "AntimalwareEnabled": "true",
        "Exclusions": {
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
        },
        "RealtimeProtectionEnabled": "true",
        "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
        }
        },
        "protectedSettings": null
}
}
```

## <a name="set-template-parameters"></a>Şablon parametrelerini ayarlama

[Azuredeploy. Parameters. JSON][parameters] parametre dosyası, kümeyi ve ilişkili kaynakları dağıtmak için kullanılan birçok değeri bildirir. Dağıtımınız için değiştirme parametreleri aşağıda verilmiştir:

**Parametresinin** | **Örnek değer** | **Notlar** 
|---|---|---|
|adminUserName|vmadmin| Küme VM’leri için yönetici kullanıcı adı. [VM Için Kullanıcı adı gereksinimleri](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm). |
|adminPassword|Password#1234| Küme VM’leri için yönetici parolası. [VM Için parola gereksinimleri](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm).|
|clusterName|mysfcluster123| Kümenin adı. Yalnızca harf ve sayı içerebilir. Uzunluğu 3 ile 23 karakter arasında olmalıdır.|
|location|southcentralus| Kümenin konumu. |
|certificateThumbprint|| <p>Otomatik olarak imzalanan bir sertifika oluşturuluyor veya sertifika dosyası sağlanıyorsa değer boş olmalıdır.</p><p>Daha önce bir anahtar kasasına yüklenmiş mevcut bir sertifikayı kullanmak için sertifika SHA1 parmak izi değerini girin. Örneğin: "6190390162C988701DB5676EB81083EA608DCCF3".</p> |
|certificateUrlValue|| <p>Otomatik olarak imzalanan bir sertifika oluşturuluyor veya sertifika dosyası sağlanıyorsa değer boş olmalıdır. </p><p>Daha önce bir anahtar kasasına yüklenmiş mevcut bir sertifikayı kullanmak için sertifika URL’sini girin. Örneğin, "https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>Otomatik olarak imzalanan bir sertifika oluşturuluyor veya sertifika dosyası sağlanıyorsa değer boş olmalıdır.</p><p>Daha önce bir anahtar kasasına yüklenmiş mevcut bir sertifikayı kullanmak için kaynak kasa değerini girin. Örneğin: "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Azure Active Directory istemci kimlik doğrulamasını ayarlama
Azure 'da barındırılan ortak bir ağda dağıtılan Service Fabric kümeleri için istemciden düğüme karşılıklı kimlik doğrulaması önerisi:
* İstemci kimliği için Azure Active Directory kullanın.
* HTTP iletişiminin sunucu kimliği ve SSL şifrelemesi için bir sertifika kullanın.

[Küme oluşturulmadan](#createvaultandcert)önce bir Service Fabric kümesi için istemcilerin kimliğini doğrulamak üzere Azure Active Directory (Azure AD) ayarlama işlemi yapılmalıdır. Azure AD, kuruluşların (kiracılar olarak bilinir) uygulamalara Kullanıcı erişimini yönetmesine olanak sağlar. 

Service Fabric kümesi, Web tabanlı [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ve [Visual Studio](service-fabric-manage-application-in-visual-studio.md)da dahil olmak üzere yönetim işlevlerine birkaç giriş noktası sunar. Sonuç olarak, kümeye erişimi denetlemek için iki Azure AD uygulaması oluşturursunuz: bir Web uygulaması ve bir yerel uygulama.  Uygulamalar oluşturulduktan sonra kullanıcıları salt okuma ve yönetici rollerine atarsınız.

> [!NOTE]
> Kümeyi oluşturmadan önce aşağıdaki adımları gerçekleştirmeniz gerekir. Betikler küme adlarını ve uç noktalarını beklediği için, değerler önceden oluşturmuş olduğunuz değerler için planlanmış olmalıdır.

Bu makalede, zaten bir kiracı oluşturmuş olduğunuz varsayılmaktadır. Yapmadıysanız, [Azure Active Directory kiracının nasıl alınacağını](../active-directory/develop/quickstart-create-new-tenant.md)okuyarak başlayın.

Azure AD 'yi Service Fabric bir kümeyle yapılandırma ile ilgili adımları basitleştirmek için bir Windows PowerShell komut dosyası kümesi oluşturduk. [Betikleri bilgisayarınıza indirin](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool) .

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Azure AD uygulamaları oluşturma ve rollere kullanıcı atama
Kümeye erişimi denetlemek için iki Azure AD uygulaması oluşturun: bir Web uygulaması ve bir yerel uygulama. Kümenizi temsil etmek üzere uygulamalar oluşturduktan sonra, kullanıcılarınızı [Service Fabric tarafından desteklenen rollere](service-fabric-cluster-security-roles.md)atayın: salt okunurdur ve yönetici.

`SetupApplications.ps1`çalıştırın ve kiracı KIMLIĞI, küme adı ve Web uygulaması yanıt URL 'sini parametre olarak sağlayın. Kullanıcılar için Kullanıcı adlarını ve parolaları belirtin. Örnek:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Ulusal bulutlar için (örneğin, Azure Kamu, Azure Çin, Azure Almanya) `-Location` parametresini belirtin.

[Azure Portal](https://portal.azure.com) *tenantıd*veya dizin kimliğinizi bulabilirsiniz. **Azure Active Directory** > **özellikleri** ' nı seçin ve **dizin kimliği** değerini kopyalayın.

*Clustername* , komut dosyası tarafından oluşturulan Azure AD uygulamalarının ön ekini almak için kullanılır. Gerçek küme adıyla tam olarak eşleşmesi gerekmez. Yalnızca Azure AD yapıtları kullanımda olan Service Fabric kümesine eşlemeyi kolaylaştırır.

*Webapplicationreplyurl 'si* , oturum açmayı tamamladıktan sonra Azure AD 'nin kullanıcılarınıza döndürdüğü varsayılan uç noktadır. Bu uç noktayı kümeniz için Service Fabric Explorer uç noktası olarak ayarlayın, varsayılan olarak şu şekilde olur:

https://&lt;cluster_domain&gt;: 19080/Explorer

Azure AD kiracısı için yönetici ayrıcalıklarına sahip bir hesapta oturum açmanız istenir. Oturum açtıktan sonra betik, Service Fabric kümenizi temsil etmek üzere Web ve yerel uygulamalar oluşturur. [Azure Portal](https://portal.azure.com)Kiracıdaki uygulamalarda, iki yeni giriş görmeniz gerekir:

   * *ClusterName*\_kümesi
   * *ClusterName*\_istemcisi

Betik, kümeyi oluştururken Kaynak Yöneticisi şablonu için gereken JSON 'ı yazdırır, bu nedenle PowerShell penceresini açık tutmak iyi bir fikirdir.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>İstemci erişimi için Azure AD 'yi kullanmak üzere Azure AD yapılandırması ekleme
[Azuredeploy. JSON][template]dosyasında, **Microsoft. servicefabric/kümeler** bölümünde Azure AD 'yi yapılandırın. Kiracı KIMLIĞI, küme uygulama KIMLIĞI ve istemci uygulama KIMLIĞI için parametreler ekleyin.  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...

    "aadTenantId": {
      "type": "string",
      "defaultValue": "0e3d2646-78b3-4711-b8be-74a381d9890c"
    },
    "aadClusterApplicationId": {
      "type": "string",
      "defaultValue": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
    },
    "aadClientApplicationId": {
      "type": "string",
      "defaultValue": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
    }
  },

...

{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

Parametre değerlerini [azuredeploy. Parameters. JSON][parameters] parametreleri dosyasına ekleyin. Örnek:

```json
"aadTenantId": {
"value": "0e3d2646-78b3-4711-b8be-74a381d9890c"
},
"aadClusterApplicationId": {
"value": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
},
"aadClientApplicationId": {
"value": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
}
```
<a id="configurediagnostics" name="configurediagnostics_anchor"></a>

## <a name="configure-diagnostics-collection-on-the-cluster"></a>Küme üzerinde tanılama toplamayı yapılandırma
Bir Service Fabric kümesi çalıştırırken, günlükleri merkezi konumdaki tüm düğümlerden toplamak iyi bir fikirdir. Günlüklerin merkezi bir konumda olması, kümenizdeki sorunları veya bu kümede çalışan uygulama ve hizmetlerde sorunları analiz etmenize ve gidermenize yardımcı olur.

Günlükleri karşıya yükleme ve toplamanın bir yolu, Azure depolama 'ya günlük yükleyen Azure Tanılama (WAD) uzantısını kullanmaktır ve ayrıca Azure Application Insights veya Event Hubs günlük gönderme seçeneğine sahiptir. Ayrıca, olayları depolama alanından okumak ve Azure Izleyici günlükleri ya da başka bir günlük ayrıştırma çözümü gibi bir çözümleme platformu ürününe yerleştirmek için bir dış işlem de kullanabilirsiniz.

Bu öğreticiyi takip ediyorsanız, [şablonda][template]tanılama koleksiyonu zaten yapılandırılmış.

Tanılamayı dağıtmayan mevcut bir kümeniz varsa, küme şablonu aracılığıyla ekleyebilir veya güncelleştirebilirsiniz. Mevcut kümeyi oluşturmak için kullanılan Kaynak Yöneticisi şablonunu değiştirin veya şablonu portaldan indirin. Aşağıdaki görevleri gerçekleştirerek Template. json dosyasını değiştirin:

Şablondaki kaynaklar bölümüne yeni bir depolama kaynağı ekleyin:
```json
"resources": [
...
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
...
]
```

Ardından, depolama hesabı adı için parametreler ekleyin ve şablonun parametreler bölümüne yazın. Yer tutucu metin depolama hesabı adının yerine buraya, istediğiniz depolama hesabının adını girin.

```json
"parameters": {
...
"applicationDiagnosticsStorageAccountType": {
    "type": "string",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
    "description": "Replication option for the application diagnostics storage account"
    }
},
"applicationDiagnosticsStorageAccountName": {
    "type": "string",
    "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
    "metadata": {
    "description": "Name for the storage account that contains application diagnostics data from the cluster"
    }
},
...
}
```

Sonra, kümedeki her bir **Microsoft. COMPUTE/virtualMachineScaleSets** kaynağının **virtualmachineprofile** özelliğinin uzantılar dizisine **ıaasdiagnostics** uzantısını ekleyin.  [Örnek şablonu][template]kullanıyorsanız, üç sanal makine ölçek kümesi vardır (kümedeki her düğüm türü için bir tane).

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
                    "properties": {
                        "type": "IaaSDiagnostics",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                        "storageAccountEndPoint": "https://core.windows.net/"
                        },
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "settings": {
                        "WadCfg": {
                            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                            "EtwProviders": {
                                "EtwEventSourceProviderConfiguration": [
                                {
                                    "provider": "Microsoft-ServiceFabric-Actors",
                                    "scheduledTransferKeywordFilter": "1",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableActorEventTable"
                                    }
                                },
                                {
                                    "provider": "Microsoft-ServiceFabric-Services",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                                    }
                                }
                                ],
                                "EtwManifestProviderConfiguration": [
                                {
                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                    "scheduledTransferLogLevelFilter": "Information",
                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricSystemEventTable"
                                    }
                                }
                                ]
                            }
                            }
                        },
                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                        },
                        "typeHandlerVersion": "1.5"
                    }
                }
            ...
            ]
        }
    }
}
```
<a id="configureeventstore" name="configureeventstore_anchor"></a>

## <a name="configure-the-eventstore-service"></a>EventStore hizmetini yapılandırma
EventStore hizmeti Service Fabric ' de bir izleme seçeneğidir. EventStore, belirli bir noktadaki kümenizin veya iş yüklerinizin durumunu anlamak için bir yol sağlar. EventStore, kümeden olayları tutan, durum bilgisi olan bir Service Fabric hizmetidir. Olay Service Fabric Explorer, REST ve API 'Ler aracılığıyla sunulur. EventStore, kümenizdeki herhangi bir varlıkta Tanılama verileri almak için kümeyi doğrudan sorgular ve yardımcı olması için kullanılmalıdır:

* Geliştirme veya test ederken sorunları tanılayın veya bir izleme işlem hattı kullanıyor olabilirsiniz
* Kümenizde yaptığınız yönetim eylemlerinin doğru şekilde işleneceğini onaylayın
* Service Fabric belirli bir varlıkla nasıl etkileşim kurduğuna ilişkin bir "Snapshot" alın



Kümenizde EventStore hizmetini etkinleştirmek için **Microsoft. ServiceFabric/kümeler** kaynağının **fabricsettings** özelliğine aşağıdakileri ekleyin:

```json
"apiVersion": "2018-02-01",
"type": "Microsoft.ServiceFabric/clusters",
"name": "[parameters('clusterName')]",
"properties": {
    ...
    "fabricSettings": [
        ...
        {
            "name": "EventStoreService",
            "parameters": [
                {
                "name": "TargetReplicaSetSize",
                "value": "3"
                },
                {
                "name": "MinReplicaSetSize",
                "value": "1"
                }
            ]
        }
    ]
}
```
<a id="configureloganalytics" name="configureloganalytics_anchor"></a>

## <a name="set-up-azure-monitor-logs-for-the-cluster"></a>Küme için Azure Izleyici günlüklerini ayarlama

Azure Izleyici günlükleri, küme düzeyindeki olayları izlemek için önerimiz. Kümenizi izlemek üzere Azure Izleyici günlüklerini ayarlamak için, [Küme düzeyindeki olayları görüntülemek için tanılamayı etkinleştirmeniz](#configure-diagnostics-collection-on-the-cluster)gerekir.  

Çalışma alanının, kümeinizden gelen tanılama verilerine bağlanması gerekir.  Bu günlük verileri, WADServiceFabric * EventTable, WADWindowsEventLogsTable ve Wadeviltable tablolarında *Applicationdiagnosticsstorageaccountname* depolama hesabında depolanır.

Azure Log Analytics çalışma alanını ekleyin ve çalışma alanına çözümü ekleyin:

```json
"resources": [
    ...
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Memory",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Available MBytes"
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter2",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Service Fabric Service",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Average milliseconds per request"
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
]
```

Sonra, parametreleri Ekle
```json
"parameters": {
    ...
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "mysfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "West Europe",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
}
```

Sonra, değişken Ekle:
```json
"variables": {
    ...
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
}
```

Kümedeki her bir sanal makine ölçek kümesine Log Analytics Agent uzantısını ekleyin ve aracıyı Log Analytics çalışma alanına bağlayın. Bu, kapsayıcılar, uygulamalar ve performans izleme hakkında tanılama verileri toplamaya izin vermez. Bunu sanal makine ölçek kümesi kaynağına bir uzantı olarak ekleyerek Azure Resource Manager, kümeyi ölçeklendirirken bile her düğümde yüklü olmasını sağlar.

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "MicrosoftMonitoringAgent",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                        }
                    }
                }
            ...
            ]
        }
    }
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Sanal ağı ve kümeyi dağıtma

Ardından, ağ topolojisini ayarlayın ve Service Fabric kümesini dağıtın. [Azuredeploy. json][template] Kaynak Yöneticisi şablonu Service Fabric için bir sanal ağ, alt ağ ve ağ güvenlik grubu oluşturur. Şablon tarafından sertifika güvenliği etkin bir küme de dağıtılır. Üretim kümeleri için, bir sertifika yetkilisinden küme sertifikası olarak bir sertifika kullanın. Test kümelerinin güvenliğinin sağlanması için otomatik olarak imzalanan bir sertifika kullanılabilir.

Bu makaledeki şablon, küme sertifikasını tanımlamak için sertifika parmak izini kullanan bir küme dağıtır. İki sertifika aynı parmak izine sahip olamaz, bu da sertifika yönetimini daha zor hale getirir. Dağıtılan bir kümeyi sertifika parmak izlerinden sertifika ortak adlarına geçirme, sertifika yönetimini basitleştirir. Sertifikayı sertifika yönetimi için sertifika ortak adlarını kullanacak şekilde güncelleştirme hakkında bilgi edinmek için, [değişiklik kümesini sertifika ortak ad yönetimine](service-fabric-cluster-change-cert-thumbprint-to-cn.md)okuyun.

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>Mevcut bir sertifikayı kullanarak bir küme oluşturma

Aşağıdaki betik [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) cmdlet 'ini ve yeni bir kümeyi Azure 'da dağıtmak için bir şablon kullanır. Cmdlet 'i Azure 'da yeni bir Anahtar Kasası oluşturur ve sertifikanızı karşıya yükler.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # Must match the clustername parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-by-using-a-new-self-signed-certificate"></a>Yeni, otomatik olarak imzalanan bir sertifika kullanarak bir küme oluşturma

Aşağıdaki betik [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) cmdlet 'ini ve yeni bir kümeyi Azure 'da dağıtmak için bir şablon kullanır. Cmdlet Azure 'da yeni bir Anahtar Kasası oluşturur, anahtar kasasına yeni bir kendinden imzalı sertifika ekler ve sertifika dosyasını yerel olarak indirir.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Güvenli kümeye bağlanma

Service Fabric SDK ile yüklenen Service Fabric PowerShell modülünü kullanarak kümeye bağlanın.  İlk olarak sertifikayı bilgisayarınızda geçerli kullanıcının Kişisel (My) deposuna yükleyin. Aşağıdaki PowerShell komutunu çalıştırın:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Artık güvenli kümenize bağlanmaya hazırsınız.

**Service Fabric** PowerShell modülü, Service Fabric kümelerini, uygulamalarını ve hizmetlerini yönetmek için birçok cmdlet sağlar. Güvenli kümeye bağlanmak için [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) cmdlet'ini kullanın. Sertifika SHA1 parmak izi ve bağlantı uç noktası ayrıntıları önceki adımda elde edilen çıktıdan bulunabilir.

Daha önce Azure AD istemci kimlik doğrulamasını ayarlarsanız, aşağıdaki komutu çalıştırın: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Azure AD istemci kimlik doğrulamasını ayarlamadıysanız, aşağıdaki komutu çalıştırın:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

[Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) cmdlet 'ini kullanarak bağlı olup olmadığınızı ve kümenin sağlıklı olup olmadığını kontrol edin.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğretici serisindeki diğer makaleler, oluşturduğunuz kümeyi kullanır. Hemen bir sonraki makaleye geçmeyecekseniz ücretlendirmeden kaçınmak için [kümeyi silmeniz](service-fabric-cluster-delete.md) iyi olur.

## <a name="next-steps"></a>Sonraki adımlar

Kümenizi ölçeklendirmeyi öğrenmek için aşağıdaki öğreticiye ilerleyin.

> [!div class="checklist"]
> * PowerShell kullanarak Azure’da VNet oluşturma
> * Anahtar kasası oluşturma ve karşıya sertifika yükleme
> * Azure Active Directory kimlik doğrulamasını ayarla
> * Tanılama toplamayı yapılandır
> * EventStore hizmetini ayarlama
> * Azure Izleyici günlüklerini ayarlama
> * Azure PowerShell’de güvenli bir Service Fabric kümesi oluşturma
> * X.509 sertifikasıyla kümenin güvenliğini sağlama
> * PowerShell kullanarak kümeye bağlanma
> * Bir kümeyi kaldırma

Daha sonra, kümenizi nasıl izleyeceğinizi öğrenmek için aşağıdaki öğreticiye ilerleyin.
> [!div class="nextstepaction"]
> [Bir kümeyi izleme](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
