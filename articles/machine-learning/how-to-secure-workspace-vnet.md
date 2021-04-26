---
title: Sanal ağlarla Azure Machine Learning çalışma alanının güvenliğini sağlama
titleSuffix: Azure Machine Learning
description: Azure Machine Learning çalışma alanınızı ve ilişkili kaynaklarınızı güvenli hale getirmek için yalıtılmış bir Azure sanal ağı kullanın.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 03/17/2021
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: 20f75580c425cee9128f9c94123dcf902642eac4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531035"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>Sanal ağlarla Azure Machine Learning çalışma alanının güvenliğini sağlama

Bu makalede, bir sanal ağda Azure Machine Learning çalışma alanının ve ilişkili kaynakların güvenliğini nasıl sağlayacağınızı öğreneceksiniz.

Bu makale, bir Azure Machine Learning iş akışını güvenli hale getirmek için size kılavuzluk eden beş bölümlü bir serinin ikinci bölümüdür. İlk olarak birinci mimariyi anlamak için [tek bir bölüm (VNet genel bakış](how-to-network-security-overview.md) ) okumanız önemle tavsiye ederiz. 

Bu serideki diğer makalelere göz atın:

[1. VNET 'e genel bakış](how-to-network-security-overview.md)  >  **2. Çalışma alanı** 3 ' ü güvenli hale getirin  >  [. Eğitim ortamının](how-to-secure-training-vnet.md)4 ' ü koruyun  >  [. Invenli ortam](how-to-secure-inferencing-vnet.md)5 ' i güvenli hale getirin  >  [. Studio işlevselliğini etkinleştir](how-to-enable-studio-virtual-network.md)

Bu makalede, bir sanal ağda aşağıdaki çalışma alanı kaynaklarını nasıl etkinleştireceğinizi öğreneceksiniz:
> [!div class="checklist"]
> - Azure Machine Learning çalışma alanı
> - Azure Depolama hesapları
> - Veri depoları ve veri kümeleri Azure Machine Learning
> - Azure Key Vault
> - Azure Container Registry

## <a name="prerequisites"></a>Önkoşullar

+ Genel sanal ağ senaryolarını ve genel sanal ağ mimarisini anlamak için [ağ güvenliğine genel bakış](how-to-network-security-overview.md) makalesini okuyun.

+ İşlem kaynaklarınızla kullanılacak mevcut bir sanal ağ ve alt ağ.

+ Bir sanal ağa veya alt ağa kaynak dağıtmak için, Kullanıcı hesabınızın Azure rol tabanlı erişim denetimi 'nde (Azure RBAC) aşağıdaki eylemler için izinleri olmalıdır:

    - Sanal ağ kaynağında "Microsoft. Network/virtualNetworks/JOIN/Action".
    - Alt ağ kaynağında "Microsoft. Network/virtualNetworks/subnet/JOIN/Action".

    Ağ ile Azure RBAC hakkında daha fazla bilgi için bkz. [ağ yerleşik rolleri](../role-based-access-control/built-in-roles.md#networking)


## <a name="secure-the-workspace-with-private-endpoint"></a>Çalışma alanını özel uç noktayla güvenli hale getirin

Azure özel bağlantısı, özel bir uç nokta kullanarak çalışma alanınıza bağlanmanızı sağlar. Özel uç nokta, sanal ağınız içindeki özel IP adresleri kümesidir. Daha sonra çalışma alanınıza erişimi yalnızca özel IP adresleri üzerinden oluşacak şekilde sınırlayabilirsiniz. Özel bağlantı, veri taşması riskini azaltmaya yardımcı olur.

Özel bağlantı çalışma alanı ayarlama hakkında daha fazla bilgi için bkz. [özel bağlantı yapılandırma](how-to-configure-private-link.md).

> [!Warning]
> Çalışma alanını özel uç noktalarla güvenli hale getirmek, kendi kendine uçtan uca güvenlik sağlanması gerekmez. Çözümünüzün tek tek bileşenlerini güvenli hale getirmek için bu makalenin geri kalanında ve VNet serisinde bulunan adımları izlemeniz gerekir.

## <a name="secure-azure-storage-accounts-with-service-endpoints"></a>Hizmet uç noktaları ile Azure depolama hesaplarını güvenli hale getirme

Azure Machine Learning, hizmet uç noktalarını veya özel uç noktaları kullanmak üzere yapılandırılmış depolama hesaplarını destekler. Bu bölümde, hizmet uç noktalarını kullanarak bir Azure depolama hesabının güvenliğini nasıl sağlayacağınızı öğreneceksiniz. Özel uç noktalar için bir sonraki bölüme bakın.

Bir sanal ağdaki çalışma alanı için bir Azure depolama hesabı kullanmak için aşağıdaki adımları kullanın:

1. Azure portal, çalışma alanınızda kullanmak istediğiniz depolama hizmetine gidin.

   [![Azure Machine Learning çalışma alanına bağlı depolama alanı](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Depolama hizmeti hesabı sayfasında __ağ iletişimi__' ni seçin.

   ![Azure portal Azure Storage sayfasındaki ağ alanı](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. __Güvenlik duvarları ve sanal ağlar__ sekmesinde, aşağıdaki işlemleri yapın:
    1. __Seçili ağlar__'ı seçin.
    1. __Sanal ağlar__ altında __var olan sanal ağ ekle__ bağlantısını seçin. Bu eylem, işlemin bulunduğu sanal ağı ekler (bkz. 1. adım).

        > [!IMPORTANT]
        > Depolama hesabı, eğitim veya çıkarım için kullanılan işlem örnekleri veya kümeler ile aynı sanal ağ ve alt ağ içinde olmalıdır.

    1. __Güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine Izin ver__ onay kutusunu seçin. Bu değişiklik, tüm Azure hizmetlerinin depolama hesabınıza erişmesini sağlamaz.
    
        * **Aboneliğinizde kayıtlı** bazı hizmetlerin kaynakları, select işlemleri için **aynı abonelikteki** depolama hesabına erişebilir. Örneğin, Günlükler yazma veya yedeklemeler oluşturma.
        * Bazı hizmetlerin kaynaklarına, sistem tarafından atanan yönetilen kimliğine __bir Azure rolü atayarak__ depolama hesabınıza açık erişim verilebilir.

        Daha fazla bilgi için bkz. [Azure Depolama güvenlik duvarlarını ve sanal ağları yapılandırma](../storage/common/storage-network-security.md#trusted-microsoft-services).

    > [!IMPORTANT]
    > Azure Machine Learning SDK ile çalışırken, geliştirme ortamınızın Azure depolama hesabına bağlanabiliyor olması gerekir. Depolama hesabı bir sanal ağın içindeyken güvenlik duvarının, geliştirme ortamının IP adresinden erişime izin vermelidir.
    >
    > Depolama hesabına erişimi etkinleştirmek için, *geliştirme istemcisinde bir Web tarayıcısından* depolama hesabı Için __güvenlik duvarlarını ve sanal ağları__ ziyaret edin. Ardından, istemcinin IP adresini __adres aralığına__ eklemek IÇIN __Istemci IP adresini ekle__ onay kutusunu kullanın. Ayrıca, geliştirme ortamının IP adresini el ile girmek için __adres aralığı__ alanını da kullanabilirsiniz. İstemcinin IP adresi eklendikten sonra SDK 'Yı kullanarak depolama hesabına erişebilir.

   [![Azure portal "güvenlik duvarları ve sanal ağlar" bölmesi](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="secure-azure-storage-accounts-with-private-endpoints"></a>Özel uç noktalar ile Azure depolama hesaplarını güvenli hale getirme

Azure Machine Learning, hizmet uç noktalarını veya özel uç noktaları kullanmak üzere yapılandırılmış depolama hesaplarını destekler. Depolama hesabı özel uç noktalar kullanıyorsa, varsayılan depolama hesabınız için iki özel uç nokta yapılandırmanız gerekir:
1. **BLOB** hedef alt kaynağı olan özel bir uç nokta.
1. **Dosya** hedefi alt kaynağına (FileShare) sahip özel bir uç nokta.

![Blob ve dosya seçenekleriyle özel uç nokta yapılandırma sayfasını gösteren ekran görüntüsü](./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png)

Varsayılan depolama alanı **olmayan** bir depolama hesabı için özel bir uç nokta yapılandırmak üzere, eklemek istediğiniz depolama hesabına karşılık gelen **hedef alt kaynak** türünü seçin.

Daha fazla bilgi için bkz. [Azure depolama için özel uç noktaları kullanma](../storage/common/storage-private-endpoints.md)

## <a name="secure-datastores-and-datasets"></a>Güvenli veri depoları ve veri kümeleri

Bu bölümde, bir sanal ağ ile SDK deneyiminde veri deposunu ve veri kümelerini nasıl kullanacağınızı öğreneceksiniz. Studio deneyimi hakkında daha fazla bilgi için bkz. [sanal bir ağda Azure Machine Learning Studio 'Yu kullanma](how-to-enable-studio-virtual-network.md).

SDK 'yı kullanarak verilere erişmek için, verilerin depolandığı tek hizmet için gereken kimlik doğrulama yöntemini kullanmanız gerekir. Örneğin, Azure Data Lake Store Gen2 erişmek için bir veri deposunu kaydettiğinizde, [Azure Storage Services 'A bağlanma](how-to-access-data.md#azure-data-lake-storage-generation-2)bölümünde anlatıldığı gibi bir hizmet sorumlusu kullanmaya devam etmeniz gerekir.

### <a name="disable-data-validation"></a>Veri doğrulamayı devre dışı bırak

Varsayılan olarak, Azure Machine Learning SDK kullanarak verilere erişmeye çalıştığınızda veri geçerliliği ve kimlik bilgisi denetimleri gerçekleştirir. Veriler bir sanal ağın arkasındaysa Azure Machine Learning bu denetimleri tamamlayamıyor. Bu denetimi atlamak için, doğrulamayı atlayan veri depoları ve veri kümeleri oluşturmanız gerekir.

### <a name="use-datastores"></a>Veri depolarını kullanma

 Azure Data Lake Store gen1 ve Azure Data Lake Store Gen2, varsayılan olarak doğrulamayı atlayın, bu nedenle başka bir eylem gerekmez. Ancak aşağıdaki hizmetlerde, veri deposu doğrulamasını atlamak için benzer sözdizimini kullanabilirsiniz:

- Azure Blob depolama
- Azure FileShare
- PostgreSQL
- Azure SQL Veritabanı

Aşağıdaki kod örneği, yeni bir Azure blob veri deposu ve kümesi oluşturur `skip_validation=True` .

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-datasets"></a>Veri kümelerini kullanma

Veri kümesi doğrulamasını atlamak için sözdizimi aşağıdaki veri kümesi türleri için benzerdir:
- Ayrılmış dosya
- JSON 
- Parquet
- SQL
- Dosya

Aşağıdaki kod, yeni bir JSON veri kümesi ve kümesi oluşturur `validate=False` .

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 

```

## <a name="secure-azure-key-vault"></a>Güvenli Azure Key Vault

Azure Machine Learning, aşağıdaki kimlik bilgilerini depolamak için ilişkili bir Key Vault örneğini kullanır:
* İlişkili depolama hesabı bağlantı dizesi
* Azure Container Repository deposu örneklerine parolalar
* Veri depolarına yönelik bağlantı dizeleri

Bir sanal ağın arkasındaki Azure Key Vault Azure Machine Learning deneme yeteneklerini kullanmak için aşağıdaki adımları kullanın:

1. Çalışma alanıyla ilişkili olan Key Vault gidin.

1. __Key Vault__ sayfasında, sol bölmede __ağ iletişimi__' ni seçin.

1. __Güvenlik duvarları ve sanal ağlar__ sekmesinde, aşağıdaki işlemleri yapın:
    1. __Erişime Izin ver__' ın altında __Özel uç nokta ve seçili ağlar__' ı seçin.
    1. __Sanal ağlar__ altında, deneme hesapla'nizin bulunduğu sanal ağı eklemek için __var olan sanal ağları Ekle__ ' yi seçin.
    1. __Güvenilen Microsoft hizmetlerinin bu güvenlik duvarını atlamasına Izin ver__ altında __Evet__' i seçin.

   [![Key Vault bölmesindeki "güvenlik duvarları ve sanal ağlar" bölümü](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>Azure Container Registry etkinleştir (ACR)

Bir sanal ağ içinde Azure Container Registry kullanmak için aşağıdaki gereksinimleri karşılamanız gerekir:

* Azure Container Registry Premium sürüm olmalıdır. Yükseltme hakkında daha fazla bilgi için bkz. [SKU 'Ları değiştirme](../container-registry/container-registry-skus.md#changing-tiers).

* Azure Container Registry, eğitim veya çıkarım için kullanılan depolama hesabı ve işlem hedefleri ile aynı sanal ağ ve alt ağ içinde olmalıdır.

* Azure Machine Learning çalışma alanınızın bir [Azure Machine Learning işlem kümesi](how-to-create-attach-compute-cluster.md)içermesi gerekir.

    ACR bir sanal ağın arkasındaysa, Azure Machine Learning Docker görüntülerini doğrudan oluşturmak için kullanamaz. Bunun yerine, görüntüleri oluşturmak için işlem kümesi kullanılır.

    > [!IMPORTANT]
    > Docker görüntülerini oluşturmak için kullanılan işlem kümesi, modellerinizi eğitmek ve dağıtmak için kullanılan paket depolarına erişebilmelidir. Ortak depoya erişime izin veren, [özel Python paketleri kullanan](how-to-use-private-python-packages.md)veya paketleri zaten Içeren [özel Docker görüntülerini](how-to-train-with-custom-image.md) kullanan ağ güvenlik kuralları eklemeniz gerekebilir.

Bu gereksinimler karşılandıktan sonra, Azure Container Registry etkinleştirmek için aşağıdaki adımları kullanın.

> [!TIP]
> Çalışma alanını oluştururken Mevcut bir Azure Container Registry kullanmıyorsanız, bir tane var olmayabilir. Varsayılan olarak, çalışma alanı bir ACR örneği oluşturana kadar bir örnek oluşturmaz. Bir tane oluşturulmasını zorlamak için, bu bölümdeki adımları kullanmadan önce çalışma alanınızı kullanarak bir modeli eğitme veya dağıtma.

1. Aşağıdaki yöntemlerden birini kullanarak, çalışma alanınızın Azure Container Registry adını bulun:

    __Azure portalı__

    Çalışma alanınızın genel bakış bölümünden __kayıt defteri__ değeri Azure Container Registry bağlanır.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Çalışma alanı için Azure Container Registry" border="true":::

    __Azure CLI__

    [Azure CLI için Machine Learning uzantısını yüklediyseniz](reference-azure-machine-learning-cli.md), `az ml workspace show` çalışma alanı bilgilerini göstermek için komutunu kullanabilirsiniz.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Bu komut şuna benzer bir değer döndürür `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` . Dizenin son bölümü, çalışma alanının Azure Container Registry adıdır.

1. [Kayıt defteri için ağ erişimini yapılandırma](../container-registry/container-registry-vnet.md#configure-network-access-for-registry)bölümündeki adımları kullanarak sanal ağınıza erişimi sınırlayın. Sanal ağ eklenirken Azure Machine Learning kaynaklarınızın sanal ağını ve alt ağını seçin.

1. [Güvenilen hizmetlere erişim izni](../container-registry/allow-access-trusted-services.md)vermek için çalışma alanı için ACR 'yi yapılandırın.

1. Docker görüntülerini derlemek üzere bir işlem kümesi yapılandırmak için Python SDK Azure Machine Learning kullanın. Aşağıdaki kod parçacığı bunun nasıl yapılacağını göstermektedir:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    # To switch back to using ACR to build (if ACR is not in the VNet):
    # ws.update(image_build_compute = '')
    ```

    > [!IMPORTANT]
    > Depolama hesabınız, işlem kümeniz ve Azure Container Registry hepsi sanal ağın aynı alt ağında olmalıdır.
    
    Daha fazla bilgi için [Update ()](/python/api/azureml-core/azureml.core.workspace.workspace#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) Yöntem başvurusuna bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bu makale, beş bölümlü bir sanal ağ serisinin ikinci bölümüdür. Bir sanal ağın güvenliğini nasıl sağlayacağınızı öğrenmek için makalelerin geri kalanına bakın:

* [1. kısım: sanal ağa genel bakış](how-to-network-security-overview.md)
* [3. kısım: eğitim ortamının güvenliğini sağlama](how-to-secure-training-vnet.md)
* [4. Bölüm: ikinci dereceden sınırlama ortamının güvenliğini sağlama](how-to-secure-inferencing-vnet.md)
* [5. Bölüm: Studio işlevselliğini etkinleştirme](how-to-enable-studio-virtual-network.md)

Ayrıca, ad çözümlemesi için [özel DNS](how-to-custom-dns.md) kullanma makalesine bakın.
