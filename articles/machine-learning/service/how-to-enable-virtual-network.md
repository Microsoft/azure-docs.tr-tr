---
title: Sanal ağda güvenli denemeleri ve çıkarım
titleSuffix: Azure Machine Learning
description: bir Azure sanal ağı içindeki Azure Machine Learning deneme/eğitim işlerinin ve çıkarım/Puanlama işlerinin güvenliğini nasıl sağlayacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 11/13/2019
ms.openlocfilehash: c5d0c517e7a3d4c011d66925b8db0c4d09dd34ca
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123580"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>Azure sanal ağı içindeki Azure ML deneme ve çıkarım işlerinin güvenliğini sağlama
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, bir Azure sanal ağı (VNet) içinde Azure Machine Learning deneme/eğitim işlerinin ve çıkarım/Puanlama işlerinin güvenliğini nasıl sağlayacağınızı öğreneceksiniz.

Bir **sanal ağ** , Azure kaynaklarınızı genel İnternet 'ten yalıtmak için bir güvenlik sınırı görevi görür. Ayrıca, bir Azure sanal ağını şirket içi ağınıza da katabilirsiniz. Ağları birleştirerek, modellerinizi güvenli bir şekilde eğitebilir ve çıkarım için dağıtılan modellerinize erişebilirsiniz.

Azure Machine Learning, işlem kaynakları için diğer Azure hizmetlerini kullanır. İşlem kaynakları veya [işlem hedefleri](concept-compute-target.md), modelleri eğitmek ve dağıtmak için kullanılır. Hedefler bir sanal ağ içinde oluşturulabilir. Örneğin, bir modeli eğitme ve modeli Azure Kubernetes Service (AKS) ' e dağıtmak için Microsoft Veri Bilimi Sanal Makinesi kullanabilirsiniz. Sanal ağlar hakkında daha fazla bilgi için bkz. [Azure sanal ağa genel bakış](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Bu makalede, *Gelişmiş güvenlik ayarları*, temel veya Deneysel kullanım durumları için gerekli olmayan bilgiler hakkında ayrıntılı bilgiler de sağlanmaktadır. Bu makalenin bazı bölümleri çeşitli senaryolar için yapılandırma bilgileri sağlar. Yönergeleri sırasıyla veya tamamen doldurmanız gerekmez.

> [!TIP]
> Özellikle, bir sanal ağ içindeki depolama hesapları veya işlem hedefleri gibi kaynakları kullanmak, makine öğrenimi ardışık düzenleri ve betik çalıştırmaları gibi işlem hattı olmayan iş akışlarıyla birlikte çalışacaktır.

> [!WARNING]
> Microsoft, bir sanal ağ içindeki kaynaklarla Azure Machine Learning tasarımcısını veya otomatik makine öğrenimini (Studio 'dan) kullanmayı desteklemez.

## <a name="prerequisites"></a>Önkoşullar

+ Azure Machine Learning [çalışma alanı](how-to-manage-workspace.md).

+ [Azure sanal ağ hizmeti](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) ve [IP ağının](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)genel çalışma bilgileri.

+ İşlem kaynaklarınızla kullanılacak önceden var olan bir sanal ağ ve alt ağ.

## <a name="use-a-storage-account-for-your-workspace"></a>Çalışma alanınız için bir depolama hesabı kullanın

Bir sanal ağdaki çalışma alanı için bir Azure depolama hesabı kullanmak üzere şunları yapın:

1. Bir sanal ağın arkasında bir işlem kaynağı (örneğin, bir Machine Learning kümesi) oluşturun veya çalışma alanına (örneğin, bir HDInsight kümesi, sanal makine veya Azure Kubernetes hizmet kümesi) bir işlem kaynağı ekleyin. İşlem kaynağı deneme veya model dağıtımı için olabilir.

   Daha fazla bilgi için, bu makaledeki [Machine Learning Işlem kullanma](#amlcompute), [sanal makine veya HDInsight kümesi](#vmorhdi)kullanma ve [Azure Kubernetes hizmet](#aksvnet) bölümlerini kullanma bölümüne bakın.

1. Azure portal, çalışma alanınıza bağlı depolama alanına gidin.

   [Azure Machine Learning çalışma alanına bağlı depolamayı ![](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. **Azure depolama** sayfasında __güvenlik duvarları ve sanal ağlar__' ı seçin.

   ![Azure portal Azure Storage sayfasındaki "güvenlik duvarları ve sanal ağlar" alanı](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. __Güvenlik duvarları ve sanal ağlar__ sayfasında, aşağıdakileri yapın:
    - __Seçili ağlar__'ı seçin.
    - __Sanal ağlar__altında __var olan sanal ağ ekle__ bağlantısını seçin. Bu eylem, işlemin bulunduğu sanal ağı ekler (bkz. 1. adım).

        > [!IMPORTANT]
        > Depolama hesabı, eğitim veya çıkarım için kullanılan kümeler ile aynı sanal ağda olmalıdır.

    - __Güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine Izin ver__ onay kutusunu seçin.

    > [!IMPORTANT]
    > Azure Machine Learning SDK ile çalışırken, geliştirme ortamınızın Azure depolama hesabına bağlanabiliyor olması gerekir. Depolama hesabı bir sanal ağın içindeyken güvenlik duvarının, geliştirme ortamının IP adresinden erişime izin vermelidir.
    >
    > Depolama hesabına erişimi etkinleştirmek için, *geliştirme istemcisinde bir Web tarayıcısından*depolama hesabı Için __güvenlik duvarlarını ve sanal ağları__ ziyaret edin. Ardından, istemcinin IP adresini __adres aralığına__eklemek IÇIN __Istemci IP adresini ekle__ onay kutusunu kullanın. Ayrıca, geliştirme ortamının IP adresini el ile girmek için __adres aralığı__ alanını da kullanabilirsiniz. İstemcinin IP adresi eklendikten sonra SDK 'Yı kullanarak depolama hesabına erişebilir.

   [Azure portal "güvenlik duvarları ve sanal ağlar" bölmesine ![](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> Bir sanal ağda Azure Machine Learning veya _varsayılan olmayan depolama hesapları_ için hem _varsayılan depolama hesabını_ yerleştirebilirsiniz.
>
> Varsayılan depolama hesabı, bir çalışma alanı oluşturduğunuzda otomatik olarak sağlanır.
>
> Varsayılan olmayan depolama hesapları için, [`Workspace.create()` işlevindeki](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) `storage_account` parametresi Azure kaynak kimliği 'ne göre özel bir depolama hesabı belirtmenizi sağlar.

## <a name="use-a-key-vault-instance-with-your-workspace"></a>Çalışma alanınız ile bir Anahtar Kasası örneği kullanın

Çalışma alanıyla ilişkili anahtar kasası örneği, Azure Machine Learning tarafından aşağıdaki kimlik bilgilerini depolamak için kullanılır:
* İlişkili depolama hesabı bağlantı dizesi
* Azure Container Repository deposu örneklerine parolalar
* Veri depolarına yönelik bağlantı dizeleri

Bir sanal ağın arkasındaki Azure Key Vault Azure Machine Learning deneme yeteneklerini kullanmak için şunları yapın:
1. Çalışma alanıyla ilişkili olan anahtar kasasına gidin.

   [Azure Machine Learning çalışma alanıyla ilişkili anahtar kasasını ![](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. **Key Vault** sayfasında, sol bölmede __güvenlik duvarları ve sanal ağlar__' ı seçin.

   ![Key Vault bölmesindeki "güvenlik duvarları ve sanal ağlar" bölümü](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. __Güvenlik duvarları ve sanal ağlar__ sayfasında, aşağıdakileri yapın:
    - __Erişime Izin ver__' ın altında __Seçili ağlar__' ı seçin.
    - __Sanal ağlar__altında, deneme hesapla'nizin bulunduğu sanal ağı eklemek için __var olan sanal ağları Ekle__ ' yi seçin.
    - __Güvenilen Microsoft hizmetlerinin bu güvenlik duvarını atlamasına Izin ver__altında __Evet__' i seçin.

   [Key Vault bölmesindeki "güvenlik duvarları ve sanal ağlar" bölümünü ![](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

<a id="amlcompute"></a>

## <a name="use-a-machine-learning-compute"></a>Machine Learning İşlem kullanma

Bir sanal ağda Azure Machine Learning işlem kümesi kullanmak için aşağıdaki ağ gereksinimlerinin karşılanması gerekir:

> [!div class="checklist"]
> * Sanal ağın, Azure Machine Learning çalışma alanıyla aynı abonelikte ve bölgede olması gerekir.
> * Hesaplama kümesi için belirtilen alt ağın, hedeflenen VM sayısına uyum sağlaması için yeterli sayıda atanmamış IP adresi olmalıdır. Alt ağda yeterli sayıda atanmamış IP adresi yoksa, bir işlem kümesi kısmen tahsis edilir.
> * Sanal ağın aboneliğine veya kaynak grubuna yönelik güvenlik ilkelerinizin veya kilitlerinizin sanal ağı yönetmek için izinleri kısıtlayıp kısıtlamamadığını denetleyin. Trafiği kısıtlayarak sanal ağın güvenliğini sağlamayı planlıyorsanız, bazı bağlantı noktalarını işlem hizmeti için açık bırakın. Daha fazla bilgi için, [gerekli bağlantı noktaları](#mlcports) bölümüne bakın.
> * Tek bir sanal ağa birden çok işlem kümesi koyacaksanız bir veya daha fazla kaynağınız için bir kota artışı istemeniz gerekebilir.
> * Çalışma alanı için Azure depolama hesabı bir sanal ağda da güvenlik altına alınırsa, Azure Machine Learning işlem kümesiyle aynı sanal ağda olmaları gerekir.

Machine Learning işlem kümesi, sanal ağı içeren kaynak grubundaki ek ağ kaynaklarını otomatik olarak ayırır. Her işlem kümesi için hizmet aşağıdaki kaynakları ayırır:

* Bir ağ güvenlik grubu
* Bir genel IP adresi
* Bir yük dengeleyici

Bu kaynaklar, aboneliğin [kaynak kotalarıyla](https://docs.microsoft.com/azure/azure-subscription-service-limits) sınırlıdır.

### <a id="mlcports"></a>Gerekli bağlantı noktaları

Machine Learning İşlem şu anda, belirtilen sanal ağda VM 'Leri sağlamak için Azure Batch hizmetini kullanıyor. Alt ağ, Batch hizmetinden gelen iletişime izin vermelidir. Bu iletişimi, Machine Learning İşlem düğümlerinde çalıştırma zamanlamak ve Azure depolama ile diğer kaynaklarla iletişim kurmak için kullanırsınız. Batch hizmeti, sanal makinelere bağlı ağ arabirimi (NIC) düzeyinde ağ güvenlik grupları (NSG 'Ler) ekler. Bu NSG'ler şu trafiğe izin vermek için gelen ve giden bağlantı kurallarını otomatik olarak yapılandırır:

- __Batchnodemanagement__ __hizmet etiketinden__ 29876 ve 29877 bağlantı noktalarında gelen TCP trafiği.

    ![BatchNodeManagement hizmet etiketini kullanan bir gelen kuralı](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- Seçim Uzaktan erişime izin vermek için 22 numaralı bağlantı noktasında gelen TCP trafiği. Bu bağlantı noktasını yalnızca genel IP üzerinde SSH kullanarak bağlanmak istiyorsanız kullanın.

- Sanal ağa giden herhangi bir bağlantı noktasında giden trafik.

- İnternete giden herhangi bir bağlantı noktasında giden trafik.

Batch tarafından yapılandırılmış olan NSG'lerdeki gelen veya giden kurallarını değiştirirken veya yenilerini eklerken dikkatli olun. Bir NSG, işlem düğümleriyle iletişimi engelliyorsa, işlem hizmeti işlem düğümlerinin durumunu kullanılamıyor olarak ayarlar.

Azure Batch hizmeti kendi NSG 'leri yapılandırdığından alt ağ düzeyinde NSG 'leri belirtmeniz gerekmez. Bununla birlikte, belirtilen alt ağda ilişkili NSG 'ler veya güvenlik duvarı varsa, gelen ve giden güvenlik kurallarını daha önce belirtilen şekilde yapılandırın.

Azure portal NSG kural yapılandırması aşağıdaki görüntülerde gösterilmektedir:

[Machine Learning İşlem için gelen NSG kurallarını ![](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png#lightbox)

![Machine Learning İşlem giden NSG kuralları](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a>Sanal ağ ile giden bağlantıyı sınırlayın

Varsayılan giden kurallarını kullanmak istemiyorsanız ve sanal ağınızın giden erişimini sınırlandırmak istiyorsanız, aşağıdakileri yapın:

- NSG kurallarını kullanarak giden internet bağlantısını reddedin.

- Giden trafiği şu şekilde sınırlayın:
   - Depolama alanı __hizmet etiketi__ kullanılarak Azure storage __. Region_Name__ (örneğin, Storage. EastUS)
   - Azure Container Registry, __AzureContainerRegistry. Region_Name__ __hizmet etiketi__ kullanılarak (örneğin, AzureContainerRegistry. EastUS)
   - __AzureMachineLearning__ __hizmet etiketi__ kullanılarak Azure Machine Learning

Azure portal NSG kural yapılandırması aşağıdaki görüntüde gösterilmektedir:

[giden NSG kurallarını Machine Learning İşlem ![](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

### <a name="user-defined-routes-for-forced-tunneling"></a>Zorlamalı tünel için Kullanıcı tanımlı yollar

Machine Learning İşlem ile Zorlamalı tünel kullanıyorsanız, [Kullanıcı tanımlı yollar (UDRs)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) işlem kaynağını içeren alt ağa ekleyin.

* Kaynaklarınızın bulunduğu bölgedeki Azure Batch hizmeti tarafından kullanılan her IP adresi için bir UDR oluşturun. Bu UDRs, Batch hizmetinin görev zamanlama için işlem düğümleriyle iletişim kurmasını sağlar. Batch hizmetinin IP adreslerinin bir listesini almak için aşağıdaki yöntemlerden birini kullanın:

    * [Azure IP aralıklarını ve hizmet etiketlerini](https://www.microsoft.com/download/details.aspx?id=56519) indirin ve dosyada `BatchNodeManagement.<region>`arayın; burada `<region>` Azure bölgesidir.

    * Bilgileri indirmek için [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 'yi kullanın. Aşağıdaki örnek, IP adresi bilgilerini indirir ve Doğu ABD 2 bölgesinin bilgilerini filtreler:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        ```

* Azure depolama 'ya giden trafik, şirket içi ağ gereciniz tarafından engellenmemelidir. Özellikle, URL 'Ler `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`ve `<account>.blob.core.windows.net`biçiminde bulunur.

UDRs 'yi eklediğinizde, ilgili her Batch IP adresi ön eki için yolu tanımlayın ve __sonraki atlama türünü__ __Internet__olarak ayarlayın. Aşağıdaki görüntüde, Azure portal bu UDR 'nin bir örneği gösterilmektedir:

![Adres ön eki için UDR örneği](./media/how-to-enable-virtual-network/user-defined-route.png)

Daha fazla bilgi için bkz. [Sanal ağda Azure Batch havuzu oluşturma](../../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Sanal ağda bir işlem kümesi oluşturma

Machine Learning İşlem kümesi oluşturmak için aşağıdakileri yapın:

1. [Azure Portal](https://portal.azure.com), Azure Machine Learning çalışma alanınızı seçin.

1. __Uygulama__ bölümünde __işlem__' i seçin ve ardından __İşlem Ekle__' yi seçin.

1. Bu işlem kaynağını bir sanal ağ kullanacak şekilde yapılandırmak için aşağıdakileri yapın:

    a. __Ağ yapılandırması__için __Gelişmiş__' i seçin.

    b. __Kaynak grubu__ açılır listesinde, sanal ağı içeren kaynak grubunu seçin.

    c. __Sanal ağ__ açılan listesinde, alt ağı içeren sanal ağı seçin.

    d. __Alt ağ__ açılan listesinde, kullanılacak alt ağı seçin.

   ![Machine Learning İşlem için sanal ağ ayarları](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Ayrıca, Azure Machine Learning SDK kullanarak bir Machine Learning İşlem kümesi de oluşturabilirsiniz. Aşağıdaki kod, `mynetwork`adlı bir sanal ağın `default` alt ağında yeni bir Machine Learning İşlem kümesi oluşturur:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Oluşturma işlemi tamamlandığında, bir deneyde kümeyi kullanarak modelinizi eğitebilirsiniz. Daha fazla bilgi için bkz. [eğitim için bir işlem hedefi seçme ve kullanma](how-to-set-up-training-targets.md).

## <a name="use-azure-databricks"></a>Azure Databricks kullan

Azure Azure Databricks çalışma alanınıza sahip bir sanal ağda kullanmak için aşağıdaki gereksinimlerin karşılanması gerekir:

> [!div class="checklist"]
> * Sanal ağın, Azure Machine Learning çalışma alanıyla aynı abonelikte ve bölgede olması gerekir.
> * Çalışma alanı için Azure depolama hesabı bir sanal ağda da güvenlik altına alınırsa, Azure Databricks kümesiyle aynı sanal ağda olmaları gerekir.
> * Azure Databricks tarafından kullanılan __databricks-Private__ ve __databricks-genel__ alt ağlarına ek olarak, sanal ağ için oluşturulan __varsayılan__ alt ağ de gereklidir.

Bir sanal ağla Azure Databricks kullanma hakkında ayrıntılı bilgi için bkz. [Azure sanal ağınızda Azure Databricks dağıtma](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Bir sanal makine veya HDInsight kümesi kullanma

> [!IMPORTANT]
> Azure Machine Learning yalnızca Ubuntu çalıştıran sanal makineleri destekler.

Bir sanal makineyi veya Azure HDInsight kümesini çalışma alanınıza sahip bir sanal ağda kullanmak için şunları yapın:

1. Azure portal veya Azure CLı kullanarak bir VM veya HDInsight kümesi oluşturun ve kümeyi bir Azure sanal ağına yerleştirin. Daha fazla bilgi için aşağıdaki makalelere bakın:
    * [Linux VM 'Ler için Azure sanal ağları oluşturma ve yönetme](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Azure sanal ağını kullanarak HDInsight 'ı genişletme](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Azure Machine Learning, VM veya kümedeki SSH bağlantı noktasıyla iletişim kurmasına izin vermek için, ağ güvenlik grubu için bir kaynak girişi yapılandırın. SSH bağlantı noktası genellikle bağlantı noktası 22 ' dir. Bu kaynaktan gelen trafiğe izin vermek için aşağıdakileri yapın:

    * __Kaynak__ açılan listesinde __hizmet etiketi__' ni seçin.

    * __Kaynak hizmet etiketi__ açılan listesinde __AzureMachineLearning__' yi seçin.

    * __Kaynak bağlantı noktası aralıkları__ aşağı açılan listesinde __*__ ' yi seçin.

    * __Hedef__ açılan listesinde __herhangi birini__seçin.

    * __Hedef bağlantı noktası aralıkları__ aşağı açılan listesinde __22__' yi seçin.

    * __Protokol__altında __herhangi bir__seçin.

    * __Eylem__altında __izin ver__' i seçin.

   ![Bir sanal ağ içindeki bir VM veya HDInsight kümesi üzerinde deneme yapmak için gelen kurallar](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Ağ güvenlik grubu için varsayılan giden kuralları saklayın. Daha fazla bilgi için bkz. [güvenlik gruplarında](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)varsayılan güvenlik kuralları.

    Varsayılan giden kurallarını kullanmak istemiyorsanız ve sanal ağınızın giden erişimini sınırlandırmak istiyorsanız, [sanal ağ ile giden bağlantıyı sınırla](#limiting-outbound-from-vnet) bölümüne bakın.

1. Azure Machine Learning çalışma alanınıza VM veya HDInsight kümesini ekleyin. Daha fazla bilgi için bkz. [model eğitimi için işlem hedeflerini ayarlama](how-to-set-up-training-targets.md).

<a id="aksvnet"></a>

## <a name="use-azure-kubernetes-service-aks"></a>Azure Kubernetes hizmetini (AKS) kullanma

Çalışma alanınıza bir sanal ağdaki AKS eklemek için aşağıdakileri yapın:

> [!IMPORTANT]
> Aşağıdaki yordama başlamadan önce, [Azure Kubernetes Service (AKS) içindeki gelişmiş ağı yapılandırma (aks)](https://docs.microsoft.com/azure/aks/configure-advanced-networking#prerequisites) ve kümenizin IP adreslemesini planlayın bölümündeki önkoşulları izleyin.
>
> AKS örneği ve Azure sanal ağı aynı bölgede olmalıdır. Bir sanal ağ içinde çalışma alanı tarafından kullanılan Azure depolama hesaplarını güvenli hale getirmek istiyorsanız, AKS örneğiyle aynı sanal ağda olmaları gerekir.

1. [Azure Portal](https://portal.azure.com), sanal ağı denetleyen NSG 'nin **kaynak**olarak __AzureMachineLearning__ kullanarak Azure Machine Learning için etkinleştirilen bir gelen kuralı olduğundan emin olun.

    [![Azure Machine Learning Işlem bölmesi Ekle](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png#lightbox)

1. Azure Machine Learning çalışma alanınızı seçin.

1. __Uygulama__ bölümünde __işlem__' i seçin ve ardından __İşlem Ekle__' yi seçin.

1. Bu işlem kaynağını bir sanal ağ kullanacak şekilde yapılandırmak için aşağıdakileri yapın:

    - __Ağ yapılandırması__için __Gelişmiş__' i seçin.

    - __Kaynak grubu__ açılır listesinde, sanal ağı içeren kaynak grubunu seçin.

    - __Sanal ağ__ açılan listesinde, alt ağı içeren sanal ağı seçin.

    - __Alt ağ__ açılan listesinde, alt ağı seçin.

    - __Kubernetes hizmeti adres aralığı__ kutusuna Kubernetes hizmeti adres aralığını girin. Bu adres aralığı, küme için kullanılabilir IP adreslerini tanımlamak üzere sınıfsız etki alanları arası yönlendirme (CıDR) gösterimi IP aralığını kullanır. Herhangi bir alt ağ IP aralığı ile çakışmamalıdır (örneğin, 10.0.0.0/16).

    - __KUBERNETES DNS HIZMETI IP adresi__ kutusuna Kubernetes DNS hizmeti IP adresini girin. Bu IP adresi, Kubernetes DNS hizmetine atanır. Bu, Kubernetes hizmeti adres aralığı içinde olmalıdır (örneğin, 10.0.0.10).

    - __Docker köprü adresi__ kutusuna Docker köprü adresini girin. Bu IP adresi Docker köprüsüne atandı. Herhangi bir alt ağ IP aralığında olmamalı veya Kubernetes hizmeti adres aralığı (örneğin, 172.17.0.1/16) olmalıdır.

   ![Azure Machine Learning: Machine Learning İşlem sanal ağ ayarları](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Sanal ağı denetleyen NSG grubunun, sanal ağ dışından çağrılabilmesi için Puanlama uç noktası için etkin bir gelen güvenlik kuralına sahip olduğundan emin olun.
   > [!IMPORTANT]
   > NSG için varsayılan giden kuralları saklayın. Daha fazla bilgi için bkz. [güvenlik gruplarında](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)varsayılan güvenlik kuralları.

   [gelen güvenlik kuralı ![](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Azure Kubernetes hizmetini bir sanal ağa eklemek için Azure Machine Learning SDK 'sını de kullanabilirsiniz. Bir sanal ağda zaten bir AKS kümeniz varsa, bunları [aks 'e dağıtma](how-to-deploy-to-aks.md)bölümünde açıklandığı gibi çalışma alanına ekleyin. Aşağıdaki kod, `mynetwork`adlı bir sanal ağın `default` alt ağında yeni bir AKS örneği oluşturur:

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

Oluşturma işlemi tamamlandığında, bir sanal ağın arkasındaki AKS kümesinde çıkarım veya model Puanlama çalıştırabilirsiniz. Daha fazla bilgi için bkz. [AKS 'e dağıtma](how-to-deploy-to-aks.md).

## <a name="use-azure-firewall"></a>Azure Güvenlik duvarını kullanma

Azure Güvenlik Duvarı 'nı kullanırken aşağıdaki adreslere gelen ve giden trafiğe izin vermek için bir ağ kuralı yapılandırmanız gerekir:

- `*.batchai.core.windows.net`
- `ml.azure.com`
- `*.azureml.ms`
- `*.experiments.azureml.net`
- `*.modelmanagement.azureml.net`
- `mlworkspace.azure.ai`
- `*.aether.ms`

Kural eklenirken, __Protokolü__ herhangi bir ve `*`bağlantı noktaları olarak ayarlayın.

Ağ kuralını yapılandırma hakkında daha fazla bilgi için bkz. [Azure Güvenlik duvarını dağıtma ve yapılandırma](/azure/firewall/tutorial-firewall-deploy-portal#configure-a-network-rule).

## <a name="next-steps"></a>Sonraki adımlar

* [Eğitim ortamlarını ayarlama](how-to-set-up-training-targets.md)
* [Modelleri dağıtılacağı yeri](how-to-deploy-and-where.md)
* [SSL ile güvenli bir şekilde model dağıtma](how-to-secure-web-service.md)

