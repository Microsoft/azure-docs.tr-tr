---
title: Sanal makine işlem düğümlerinde Linux çalıştırma-Azure Batch | Microsoft Docs
description: Azure Batch içindeki Linux sanal makinelerinin havuzlarında paralel işlem iş yüklerinizi nasıl işleyeceğini öğrenin.
services: batch
documentationcenter: python
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 18df43ebf3a20547917ddd372d922741b4cee849
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350115"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Batch havuzlarında Linux işlem düğümleri sağlama

Hem Linux hem de Windows sanal makinelerinde paralel işlem iş yüklerini çalıştırmak için Azure Batch kullanabilirsiniz. Bu makalede Batch hizmeti 'nde hem [Batch Python][py_batch_package] hem de [Batch .net][api_net] istemci kitaplıkları kullanılarak Linux işlem düğümleri havuzlarının nasıl oluşturulacağı açıklanır.

> [!NOTE]
> Uygulama paketleri 5 Temmuz 2017’den sonra oluşturulmuş tüm Batch havuzlarında desteklenir. Bunların 10 Mart 2016 ve 5 Haziran 2017 arasında oluşturulmuş Batch havuzlarında desteklenebilmesi için, havuzun Bulut Hizmeti yapılandırması kullanılarak oluşturulmuş olması gerekir. 10 Mart 2016’dan önce oluşturulan Batch havuzları uygulama paketlerini desteklemez. Uygulama paketlerini kullanarak uygulamalarınızı Batch düğümlerine dağıtma hakkında daha fazla bilgi için bkz. [Batch uygulama paketleriyle işlem düğümlerine uygulama dağıtımı](batch-application-packages.md).
>
>

## <a name="virtual-machine-configuration"></a>Sanal makine yapılandırması
Batch 'de işlem düğümleri havuzu oluşturduğunuzda, düğüm boyutunu ve işletim sistemini seçebileceğiniz iki seçeneğe sahip olursunuz: Yapılandırma ve sanal makine yapılandırması Cloud Services.

**Cloud Services Yapılandırması** *yalnızca* Windows işlem düğümleri sağlar. Kullanılabilir işlem düğümü boyutları [Cloud Services Için boyutlar](../cloud-services/cloud-services-sizes-specs.md)bölümünde listelenir ve kullanılabilir Işletim sistemleri [Azure Konuk işletim SISTEMI sürümleri ve SDK uyumluluk matrisi](../cloud-services/cloud-services-guestos-update-matrix.md)' nde listelenir. Azure Cloud Services düğümlerini içeren bir havuz oluşturduğunuzda, daha önce bahsedilen makalelerde açıklanan düğüm boyutunu ve işletim sistemi ailesini belirtirsiniz. Windows işlem düğümlerinin havuzları için Cloud Services en yaygın olarak kullanılır.

**Sanal makine yapılandırması** , işlem düğümleri Için hem Linux hem de Windows görüntülerini sağlar. Kullanılabilir işlem düğüm boyutları, Azure 'daki sanal [makinelere yönelik boyutlarda](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) ve [Azure 'Daki sanal makinelerin boyutlarına](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows) göre listelenmiştir. Sanal makine yapılandırma düğümlerini içeren bir havuz oluşturduğunuzda düğümlerin boyutunu, sanal makine görüntü başvurusunu ve düğümlere yüklenecek Batch düğümü aracı SKU 'sunu belirtmeniz gerekir.

### <a name="virtual-machine-image-reference"></a>Sanal makine görüntüsü başvurusu

Batch hizmeti, sanal makine yapılandırmasında işlem düğümleri sağlamak için [sanal makine ölçek kümelerini](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) kullanır. [Azure Marketi][vm_marketplace]'nden bir görüntü belirtebilir veya hazırladığınız özel bir görüntü sağlayabilirsiniz. Özel görüntüler hakkında daha fazla bilgi için bkz. [paylaşılan görüntü Galerisi ile havuz oluşturma](batch-sig-images.md).

Bir sanal makine görüntü başvurusunu yapılandırdığınızda, sanal makine görüntüsünün özelliklerini belirtirsiniz. Bir sanal makine görüntü başvurusu oluştururken aşağıdaki özellikler gereklidir:

| **Görüntü başvurusu özellikleri** | **Örnek** |
| --- | --- |
| Yayımcı |Canonical |
| Sunduğu |UbuntuServer |
| SKU |14.04.4-LTS |
| Version |latest |

> [!TIP]
> Bu özellikler hakkında daha fazla bilgi edinebilirsiniz ve [Azure 'DA CLI veya PowerShell Ile Linux sanal makine görüntülerini seçebilirsiniz](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Tüm Market görüntülerinin Şu anda Batch ile uyumlu olduğunu unutmayın. Daha fazla bilgi için bkz. [düğüm Aracısı SKU 'su](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>Düğüm Aracısı SKU 'SU
Batch düğüm Aracısı, havuzdaki her düğüm üzerinde çalışan ve düğüm ile Batch hizmeti arasında komut ve denetim arabirimini sağlayan bir programdır. Farklı işletim sistemleri için SKU olarak bilinen düğüm aracısının farklı uygulamaları vardır. Temelde, bir sanal makine yapılandırması oluşturduğunuzda, önce sanal makine görüntüsü başvurusunu belirttikten sonra görüntüye yüklenecek düğüm aracısını belirlersiniz. Genellikle, her düğüm Aracısı SKU 'SU birden çok sanal makine görüntüsü ile uyumludur. Düğüm Aracısı SKU 'Larının birkaç örneği aşağıda verilmiştir:

* Batch. Node. Ubuntu 14,04
* Batch. Node. CentOS 7
* Batch. Node. Windows AMD64

> [!IMPORTANT]
> Market 'te mevcut olan tüm sanal makine görüntüleri, şu anda kullanılabilir olan Batch düğüm aracılarıyla uyumlu değildir. Kullanılabilir düğüm Aracısı SKU 'Larını ve bunların uyumlu olduğu sanal makine görüntülerini listelemek için Batch SDK 'Larını kullanın. Daha fazla bilgi ve çalışma zamanında geçerli görüntülerin listesini alma örnekleri için bu makalenin ilerleyen bölümlerindeki [sanal makine görüntülerinin listesine](#list-of-virtual-machine-images) bakın.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Bir Linux havuzu oluşturun: Batch Python
Aşağıdaki kod parçacığı, bir Ubuntu sunucu işlem düğümleri havuzu oluşturmak için [Python için Microsoft Azure Batch Istemci kitaplığının][py_batch_package] nasıl kullanılacağına ilişkin bir örnek gösterir. Batch Python modülü için başvuru belgeleri Azure 'da bulunabilir. belgeleri okurken [batch paketi][py_batch_docs] .

Bu kod parçacığı açıkça bir [ImageReference][py_imagereference] oluşturur ve her bir özelliğini (yayımcı, TEKLIF, SKU, sürüm) belirtir. Ancak üretim kodunda, çalışma zamanında kullanılabilir görüntü ve düğüm Aracısı SKU kombinasyonlarını belirlemek ve seçmek için [list_node_agent_skus][py_list_skus] yöntemini kullanmanızı öneririz.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="14.04.2-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Daha önce belirtildiği gibi, şu anda desteklenen düğüm Aracısı/Market görüntü kombinasyonlarından [dinamik olarak seçim][py_imagereference] yapmak için [list_node_agent_skus][py_list_skus] yöntemini kullanmanız önerilir. Aşağıdaki Python kod parçacığı, bu yöntemin nasıl kullanılacağını göstermektedir.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(
    agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id=ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Bir Linux havuzu oluşturun: Batch .NET
Aşağıdaki kod parçacığı, bir Ubuntu sunucu işlem düğümleri havuzu oluşturmak için [Batch .net][nuget_batch_net] istemci kitaplığının nasıl kullanılacağına ilişkin bir örnek gösterir. [Batch .net başvuru belgelerini][api_net] docs.Microsoft.com üzerinde bulabilirsiniz.

Aşağıdaki kod parçacığı, [Pooloperations işlemlerini][net_pool_ops]kullanır. Şu anda desteklenen Market görüntüsü ve düğüm Aracısı SKU birleşimleri listesinden seçim yapmak için [Listnodeagentsku 'lar][net_list_skus] yöntemi. Desteklenen Kombinezonların listesi zaman zaman değiştirebileceğinden bu teknik tercih edilir. En yaygın olarak desteklenen birleşimler eklenmiştir.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

Önceki kod parçacığı [Pooloperations][net_pool_ops]'ı kullanıyor olsa da. [Listnodeagentsku 'lar][net_list_skus] Yöntemi desteklenen görüntü ve düğüm Aracısı SKU kombinasyonlarını (önerilir) [dinamik olarak listelemek][net_imagereference] ve seçmek için

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Sanal makine görüntülerinin listesi
Aşağıdaki tabloda, bu makale son kez güncelleştirildiği sırada kullanılabilir Batch düğümü aracılarıyla uyumlu Market sanal makine görüntüleri listelenmektedir. Görüntüler ve düğüm aracıları herhangi bir zamanda eklenebildiğinden veya kaldırılacağından, bu listenin kesin olmadığını unutmayın. Batch uygulamalarınızın ve hizmetlerinizin, mevcut olan SKU 'Ları belirlemek ve seçmek için her zaman [list_node_agent_skus][py_list_skus] (Python) veya [Listnodeagentsku 'ları][net_list_skus] (Batch .net) kullanmasını öneririz.

> [!WARNING]
> Aşağıdaki liste herhangi bir zamanda değişebilir. Batch işlerinizi çalıştırdığınızda uyumlu sanal makineyi ve düğüm Aracısı SKU 'Larını listelemek için her zaman Batch API 'Lerinde bulunan **node Agent SKU** yöntemlerini kullanın.
>
>

| **Yayımcı** | **Teklif** | **Görüntü SKU 'SU** | **Sürüm** | **Düğüm Aracısı SKU KIMLIĞI** |
| ------------- | --------- | ------------- | ----------- | --------------------- |
| toplu iş | işleme-centos73 | çizmeye | latest | Batch. Node. CentOS 7 |
| toplu iş | işleme-windows2016 | çizmeye | latest | Batch. Node. Windows AMD64 |
| Canonical | UbuntuServer | 16.04-LTS | latest | Batch. Node. Ubuntu 16,04 |
| Canonical | UbuntuServer | 14.04.5-LTS | latest | Batch. Node. Ubuntu 14,04 |
| credativ | Debian | 9 | latest | Batch. Node. de, 9 |
| credativ | Debian | 8 | latest | Batch. Node. de, 8 |
| Microsoft-ads | linux-data-science-vm | linuxdsvm | latest | Batch. Node. CentOS 7 |
| Microsoft-ads | Standart-veri bilimi-VM | Standart-veri bilimi-VM | latest | Batch. Node. Windows AMD64 |
| Microsoft-Azure-Batch | CentOS-kapsayıcı | 7-4 | latest | Batch. Node. CentOS 7 |
| Microsoft-Azure-Batch | CentOS-kapsayıcı-RDMA | 7-4 | latest | Batch. Node. CentOS 7 |
| Microsoft-Azure-Batch | Ubuntu-Server-Container | 16-04-LTS | latest | Batch. Node. Ubuntu 16,04 |
| Microsoft-Azure-Batch | Ubuntu-Server-Container-RDMA | 16-04-LTS | latest | Batch. Node. Ubuntu 16,04 |
| MicrosoftWindowsServer | WindowsServer | 2016-veri merkezi | latest | Batch. Node. Windows AMD64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-smalldisk | latest | Batch. Node. Windows AMD64 |
| MicrosoftWindowsServer | WindowsServer | 2016-veri merkezi-kapsayıcılar | latest | Batch. Node. Windows AMD64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | latest | Batch. Node. Windows AMD64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter-smalldisk | latest | Batch. Node. Windows AMD64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | latest | Batch. Node. Windows AMD64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter-smalldisk | latest | Batch. Node. Windows AMD64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | latest | Batch. Node. Windows AMD64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1-smalldisk | latest | Batch. Node. Windows AMD64 |
| OpenLogic | CentOS | 7.4 | latest | Batch. Node. CentOS 7 |
| OpenLogic | CentOS-HPC | 7.4 | latest | Batch. Node. CentOS 7 |
| OpenLogic | CentOS-HPC | 7.3 | latest | Batch. Node. CentOS 7 |
| OpenLogic | CentOS-HPC | 7.1 | latest | Batch. Node. CentOS 7 |
| Oracle | Oracle-Linux | 7.4 | latest | Batch. Node. CentOS 7 |
| SUSE | SLES-HPC | 12 SP2 | latest | Batch. Node. openSUSE 42,1 |

## <a name="connect-to-linux-nodes-using-ssh"></a>SSH kullanarak Linux düğümlerine bağlanma
Geliştirme sırasında veya sorun giderirken, havuzunuzdaki düğümlerde oturum açmayı gerekli bulabilirsiniz. Windows işlem düğümlerinden farklı olarak, Linux düğümlerine bağlanmak için Uzak Masaüstü Protokolü (RDP) kullanamazsınız. Bunun yerine, Batch hizmeti uzak bağlantı için her düğümde SSH erişimi sunar.

Aşağıdaki Python kod parçacığı, bir havuzdaki her düğümde uzak bağlantı için gerekli olan bir kullanıcı oluşturur. Daha sonra her düğüm için Secure Shell (SSH) bağlantı bilgilerini yazdırır.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
    credentials,
    base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Aşağıda dört Linux düğümü içeren bir havuzun önceki kodu için örnek çıktı verilmiştir:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Bir parola yerine, bir düğümde Kullanıcı oluştururken bir SSH ortak anahtarı belirtebilirsiniz. Python SDK 'sında [Computenodeuser][py_computenodeuser]üzerinde **ssh_public_key** parametresini kullanın. .NET ' te [Computenodeuser][net_computenodeuser]öğesini kullanın. [Sshpublickey][net_ssh_key] özelliği.

## <a name="pricing"></a>Fiyatlandırma
Azure Batch Azure Cloud Services ve Azure sanal makineler teknolojisinden oluşturulmuştur. Batch hizmetinin kendisi ücretsiz olarak sunulur, bu da yalnızca Batch çözümlerinizin kullandığı işlem kaynakları için ücretlendirildiğiniz anlamına gelir. **Cloud Services yapılandırma**' yı seçtiğinizde, [Cloud Services fiyatlandırma][cloud_services_pricing] yapısına göre ücretlendirilirsiniz. **Sanal makine yapılandırması**' nı seçtiğinizde, [sanal makinelerin fiyatlandırma][vm_pricing] yapısına göre ücretlendirilirsiniz. 

[Uygulama paketlerini](batch-application-packages.md)kullanarak toplu iş düğümlerinize uygulamalar dağıtırsanız, uygulama paketlerinizin kullanacağı Azure depolama kaynakları için de ücretlendirilirsiniz. Genel olarak, Azure depolama maliyetleri en az düzeydedir. 

## <a name="next-steps"></a>Sonraki adımlar

GitHub 'daki [Azure-Batch-Samples][github_samples] deposundaki [Python kodu örnekleri][github_samples_py] , havuz, iş ve görev oluşturma gibi yaygın toplu işlemlerin nasıl gerçekleştirileceğini gösteren betikler içerir. Python örneklerine eşlik eden [Benioku dosyası][github_py_readme] , gerekli paketlerin nasıl yükleneceğine ilişkin ayrıntılara sahiptir.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: /dotnet/api/microsoft.azure.batch.computenodeuser?view=azure-dotnet
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: /dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey?view=azure-dotnet#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure-sdk-for-python.readthedocs.io/batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: /python/api/azure-batch/azure.batch.models.computenodeuser
[py_imagereference]: /python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference
[py_list_skus]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
