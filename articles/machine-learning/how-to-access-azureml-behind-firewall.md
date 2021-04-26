---
title: Güvenlik duvarı kullanma
titleSuffix: Azure Machine Learning
description: Azure Güvenlik duvarları ile Azure Machine Learning çalışma alanlarına erişimi denetleyin. Güvenlik Duvarı üzerinden izin vermeniz gereken konaklar hakkında bilgi edinin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/18/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 295228e9eaa3529b05055869bd46f9aefc938a6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102212782"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Azure Machine Learning için bir güvenlik duvarının arkasındaki çalışma alanını kullan

Bu makalede, Azure Güvenlik Duvarı 'nı Azure Machine Learning çalışma alanınıza ve genel İnternet 'e erişimi denetlemek üzere nasıl yapılandıracağınızı öğrenin. Azure Machine Learning güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure Machine Learning Için kuruluş güvenliği](concept-enterprise-security.md).

> [!WARNING]
> Bir güvenlik duvarının arkasındaki veri depolamaya erişim yalnızca kod ilk deneyimlerinde desteklenir. Bir güvenlik duvarının arkasındaki verilere erişmek için [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) kullanılması desteklenmez. Studio ile özel bir ağda veri depolama ile çalışmak için, önce [bir sanal ağ ayarlamanız](../virtual-network/quick-create-portal.md) ve [Studio 'nun bir sanal ağın içinde depolanan verilere erişmesini sağlamanız](how-to-enable-studio-virtual-network.md)gerekir.

## <a name="azure-firewall"></a>Azure Güvenlik Duvarı

Azure Güvenlik Duvarı 'nı kullanırken, gelen trafik için NAT kuralları oluşturmak üzere __hedef ağ adresi çevirisi 'ni (DNAT)__ kullanın. Giden trafik için __ağ__ ve/veya __uygulama__ kuralları oluşturun. Bu kural koleksiyonları [, bazı Azure Güvenlik Duvarı kavramları](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts)hakkında daha ayrıntılı olarak açıklanmıştır.

### <a name="inbound-configuration"></a>Gelen yapılandırma

Azure Machine Learning __işlem örneği__ veya __işlem kümesi__ kullanıyorsanız, Azure Machine Learning kaynaklarını içeren alt ağ için [Kullanıcı tanımlı yollar (udrs)](../virtual-network/virtual-networks-udr-overview.md) ekleyin. Bu yol, ve kaynaklarının IP adreslerinden __gelen__ trafiği, `BatchNodeManagement` `AzureMachineLearning` işlem örneğinizin ve işlem kümenizin genel IP 'si ile zorlar.

Bu UDRs, Batch hizmetinin görev zamanlama için işlem düğümleriyle iletişim kurmasını sağlar. Işlem örneklerine erişim için gerekli olduğundan, Azure Machine Learning hizmetinin IP adresini de ekleyin. Azure Machine Learning hizmeti için IP eklerken, IP 'yi hem __birincil hem de ikincil__ Azure bölgeleri için eklemeniz gerekir. Birincil bölge, çalışma alanınızın bulunduğu bir bölgedir.

İkincil bölgeyi bulmak için [Azure eşleştirilmiş bölgeleri kullanarak olağanüstü durum kurtarma & iş sürekliliği sağlama](../best-practices-availability-paired-regions.md#azure-regional-pairs)konusuna bakın. Örneğin, Azure Machine Learning hizmetiniz Doğu ABD 2 ise, ikincil bölge Orta ABD olur. 

Batch hizmetinin ve Azure Machine Learning hizmetinin IP adreslerinin bir listesini almak için aşağıdaki yöntemlerden birini kullanın:

* [Azure IP aralıklarını ve hizmet etiketlerini](https://www.microsoft.com/download/details.aspx?id=56519) indirin ve dosyada `BatchNodeManagement.<region>` ve `AzureMachineLearning.<region>` Azure bölgeniz olduğu yerde arama yapın `<region>` .

* Bilgileri indirmek için [Azure CLI](/cli/azure/install-azure-cli) 'yi kullanın. Aşağıdaki örnek, IP adresi bilgilerini indirir ve Doğu ABD 2 bölgesi (birincil) ve Orta ABD bölgesi (ikincil) için bilgileri filtreler:

    ```azurecli-interactive
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
    # Get primary region IPs
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
    # Get secondary region IPs
    az network list-service-tags -l "Central US" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='centralus']"
    ```

    > [!TIP]
    > ABD-Virginia, US-Arizona bölgelerini veya Çin-Doğu-2 bölgelerini kullanıyorsanız, bu komutlar IP adresi döndürmez. Bunun yerine, IP adreslerinin bir listesini indirmek için aşağıdaki bağlantılardan birini kullanın:
    >
    > * [Azure Kamu için Azure IP aralıkları ve hizmet etiketleri](https://www.microsoft.com/download/details.aspx?id=57063)
    > * [Azure Çin için Azure IP aralıkları ve hizmet etiketleri](https://www.microsoft.com//download/details.aspx?id=57062)

UDRs 'yi eklediğinizde, ilgili her Batch IP adresi ön eki için yolu tanımlayın ve __sonraki atlama türünü__ __Internet__ olarak ayarlayın. Aşağıdaki görüntüde, Azure portal bu UDR 'nin bir örneği gösterilmektedir:

![Adres ön eki için UDR örneği](./media/how-to-enable-virtual-network/user-defined-route.png)

> [!IMPORTANT]
> IP adresleri zaman içinde değişebilir.

Daha fazla bilgi için bkz. [Sanal ağda Azure Batch havuzu oluşturma](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="outbound-configuration"></a>Giden yapılandırma

1. Aşağıdaki hizmet etiketlerine __gelen__ __ve giden__ trafiğe izin veren __ağ kuralları__ ekleyin:

    * AzureActiveDirectory
    * AzureMachineLearning
    * AzureResourceManager
    * Storage. Region
    * Keykasası. Region
    * ContainerRegistry. Region

    Microsoft tarafından sunulan varsayılan Docker görüntülerini kullanmayı ve Kullanıcı tarafından yönetilen bağımlılıkları etkinleştirmeyi planlıyorsanız, aşağıdaki hizmet etiketlerini de eklemeniz gerekir:

    * MicrosoftContainerRegistry. Region
    * Azurefrontkapısı. Firstpartisi

    İçeren girişler için `region` , kullandığınız Azure bölgesi ile değiştirin. Örneğin, `keyvault.westus`.

    __Protokol__ için öğesini seçin `TCP` . Kaynak ve hedef __bağlantı noktaları__ için öğesini seçin `*` .

1. Aşağıdaki konaklar için __uygulama kuralları__ ekleyin:

    > [!NOTE]
    > Bu, internet üzerindeki tüm Python kaynakları için gerekli olan tüm konaklarının ve yalnızca en yaygın kullanılan bir listesini değildir. Örneğin, bir GitHub deposuna veya başka bir konağa erişmeniz gerekiyorsa, söz konusu senaryo için gerekli Konakları belirleyip eklemeniz gerekir.

    | **Konak adı** | **Amaç** |
    | ---- | ---- |
    | **graph.windows.net** | Azure Machine Learning işlem örneği/kümesi tarafından kullanılır. |
    | **anaconda.com**</br>**\*. anaconda.com** | Varsayılan paketleri yüklemek için kullanılır. |
    | **\*. anaconda.org** | Depo verilerini almak için kullanılır. |
    | **pypi.org** | Varsa, varsayılan dizinden bağımlılıkları listelemek için kullanılır ve dizin, Kullanıcı ayarları tarafından üzerine yazılmaz. Dizinin üzerine yazılırsa, **\* . pythonhosted.org** de izin vermeniz gerekir. |
    | **cloud.r-project.org** | R geliştirmesi için CRAN paketleri yüklenirken kullanılır. |
    | **\*pytorch.org** | PyTorch tabanlı bazı örnekler tarafından kullanılır. |
    | **\*. tensorflow.org** | TensorFlow temel alınarak bazı örnekler tarafından kullanılır. |

    __Protokol: bağlantı noktası__ için __http, https kullan ' ı__ seçin.

    Uygulama kurallarını yapılandırma hakkında daha fazla bilgi için bkz. [Azure Güvenlik duvarını dağıtma ve yapılandırma](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

1. Azure Kubernetes Service 'e (AKS) dağıtılan modellere erişimi kısıtlamak için bkz. [Azure Kubernetes hizmetinde çıkış trafiğini kısıtlama](../aks/limit-egress-traffic.md).

## <a name="other-firewalls"></a>Diğer güvenlik duvarları

Her güvenlik duvarı kendi terminolojisi ve belirli yapılandırmalara sahip olduğundan, bu bölümdeki kılavuz geneldir. Güvenlik duvarınız üzerinden iletişime izin verme hakkında sorularınız varsa, lütfen kullandığınız güvenlik duvarının belgelerine başvurun.

Doğru yapılandırılmamışsa, güvenlik duvarı çalışma alanınızı kullanarak sorunlara yol açabilir. Azure Machine Learning çalışma alanı tarafından her ikisi de kullanılan birçok konak adı vardır. Aşağıdaki bölümlerde Azure Machine Learning için gereken konaklar listelenmektedir.

### <a name="microsoft-hosts"></a>Microsoft Konakları

Bu bölümdeki konaklar Microsoft 'a aittir ve çalışma alanınızın düzgün çalışması için gerekli hizmetleri sağlar. Aşağıdaki tablolarda Azure genel, Azure Kamu ve Azure Çin 21Vianet bölgeleri için ana bilgisayar adları listelenmektedir.

**Genel Azure Konakları**

| **Şunun için gerekli:** | **Azure genel** | **Azure Devlet Kurumları** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| Azure portalı | management.azure.com | management.azure.us | management.azure.cn |
| Azure Resource Manager | management.azure.com | management.usgovcloudapi.net | management.chinacloudapi.cn |

**Azure Machine Learning Konakları**

| **Şunun için gerekli:** | **Azure genel** | **Azure Devlet Kurumları** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*. azureml.ms | \*. ml.azure.us | \*. ml.azure.cn |
| Deneme, geçmiş, hiper sürücü, etiketleme | \*. experiments.azureml.net | \*. ml.azure.us | \*. ml.azure.cn |
| Model yönetimi | \*. modelmanagement.azureml.net | \*. ml.azure.us | \*. ml.azure.cn |
| İşlem Hattı | \*. aether.ms | \*. ml.azure.us | \*. ml.azure.cn |
| Tasarımcı (Studio hizmeti) | \*. studioservice.azureml.com | \*. ml.azure.us | \*. ml.azure.cn |
| Tümleşik Not defteri | \*. notebooks.azure.net | \*. notebooks.usgovcloudapi.net |\*. notebooks.chinacloudapi.cn |
| Tümleşik Not defteri | \*. file.core.windows.net | \*. file.core.usgovcloudapi.net | \*. file.core.chinacloudapi.cn |
| Tümleşik Not defteri | \*. dfs.core.windows.net | \*. dfs.core.usgovcloudapi.net | \*. dfs.core.chinacloudapi.cn |
| Tümleşik Not defteri | \*.blob.core.windows.net | \*. blob.core.usgovcloudapi.net | \*. blob.core.chinacloudapi.cn |
| Tümleşik Not defteri | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| Tümleşik Not defteri | \*. aznbcontent.net |  | |

**İşlem örneği ve işlem kümesi Konakları Azure Machine Learning**

| **Şunun için gerekli:** | **Azure genel** | **Azure Devlet Kurumları** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| İşlem kümesi/örnek | \*. batchai.core.windows.net | \*. batchai.core.usgovcloudapi.net |\*. batchai.ml.azure.cn |
| İşlem kümesi/örnek | graph.windows.net | graph.windows.net | graph.chinacloudapi.cn |
| İşlem örneği | \*. instances.azureml.net | \*. instances.azureml.us | \*. instances.azureml.cn |
| İşlem örneği | \*. instances.azureml.ms |  |  |

**Azure Machine Learning tarafından kullanılan ilişkili kaynaklar**

| **Şunun için gerekli:** | **Azure genel** | **Azure Devlet Kurumları** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Depolama Hesabı | core.windows.net | core.usgovcloudapi.net | core.chinacloudapi.cn |
| Azure Key Vault | vault.azure.net | vault.usgovcloudapi.net | vault.azure.cn |
| Azure Container Registry | azurecr.io | azurecr.us | azurecr.cn |
| Microsoft Container Registry | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |


> [!TIP]
> Federal Kimlik kullanmayı planlıyorsanız [Active Directory Federasyon Hizmetleri (AD FS) makalenin güvenliğini sağlamak Için en iyi uygulamaları](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs) izleyin.

Ayrıca, ve için IP adresleri eklemek üzere [zorlamalı tünelde](how-to-secure-training-vnet.md#forced-tunneling) bulunan bilgileri `BatchNodeManagement` kullanın `AzureMachineLearning` .

Azure Kubernetes Service 'e (AKS) dağıtılan modellere erişimi kısıtlama hakkında bilgi için bkz. [Azure Kubernetes hizmetinde çıkış trafiğini kısıtlama](../aks/limit-egress-traffic.md).

### <a name="python-hosts"></a>Python Konakları

Bu bölümdeki konaklar, Python paketlerini yüklemek için kullanılır. Geliştirme, eğitim ve dağıtım sırasında gereklidir. 

> [!NOTE]
> Bu, internet üzerindeki tüm Python kaynakları için gerekli olan tüm konaklarının ve yalnızca en yaygın kullanılan bir listesini değildir. Örneğin, bir GitHub deposuna veya başka bir konağa erişmeniz gerekiyorsa, söz konusu senaryo için gerekli Konakları belirleyip eklemeniz gerekir.

| **Konak adı** | **Amaç** |
| ---- | ---- |
| **anaconda.com**</br>**\*. anaconda.com** | Varsayılan paketleri yüklemek için kullanılır. |
| **\*. anaconda.org** | Depo verilerini almak için kullanılır. |
| **pypi.org** | Varsa, varsayılan dizinden bağımlılıkları listelemek için kullanılır ve dizin, Kullanıcı ayarları tarafından üzerine yazılmaz. Dizinin üzerine yazılırsa, **\* . pythonhosted.org** de izin vermeniz gerekir. |
| **\*pytorch.org** | PyTorch tabanlı bazı örnekler tarafından kullanılır. |
| **\*. tensorflow.org** | TensorFlow temel alınarak bazı örnekler tarafından kullanılır. |

### <a name="r-hosts"></a>R Konakları

Bu bölümdeki konaklar R paketleri yüklemek için kullanılır. Geliştirme, eğitim ve dağıtım sırasında gereklidir.

> [!NOTE]
> Bu, internet üzerindeki tüm R kaynakları için gerekli olan konaklarının ve yalnızca en yaygın olarak kullanılan tümünün bir listesi değildir. Örneğin, bir GitHub deposuna veya başka bir konağa erişmeniz gerekiyorsa, söz konusu senaryo için gerekli Konakları belirleyip eklemeniz gerekir.

| **Konak adı** | **Amaç** |
| ---- | ---- |
| **cloud.r-project.org** | CRAN paketleri yüklenirken kullanılır. |

> [!IMPORTANT]
> Dahili olarak, Azure Machine Learning için R SDK, Python paketlerini kullanır. Bu nedenle, güvenlik duvarı üzerinden Python konaklarına da izin vermelisiniz.
## <a name="next-steps"></a>Sonraki adımlar

* [Öğretici: Azure portalını kullanarak Azure Güvenlik Duvarı'nı dağıtma ve yapılandırma](../firewall/tutorial-firewall-deploy-portal.md)
* [Azure Sanal Ağı içindeki Azure ML denemesinin ve çıkarım işlerinin güvenliğini sağlama](how-to-network-security-overview.md)
