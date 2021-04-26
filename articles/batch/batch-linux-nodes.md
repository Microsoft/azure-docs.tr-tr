---
title: Sanal makine işlem düğümlerinde Linux çalıştırma
description: Azure Batch 'de Linux sanal makinelerinin havuzlarında paralel işlem iş yüklerini işlemeyi öğrenin.
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: H1Hack27Feb2017, devx-track-python, devx-track-csharp
ms.openlocfilehash: c711ec0d035b9b59ec7628a51fe3cff26de358bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98683709"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Batch havuzlarında Linux işlem düğümleri sağlama

Hem Linux hem de Windows sanal makinelerinde paralel işlem iş yüklerini çalıştırmak için Azure Batch kullanabilirsiniz. Bu makalede Batch hizmeti 'nde hem [Batch Python](https://pypi.python.org/pypi/azure-batch) hem de [Batch .net](/dotnet/api/microsoft.azure.batch) istemci kitaplıkları kullanılarak Linux işlem düğümleri havuzlarının nasıl oluşturulacağı açıklanır. 

## <a name="virtual-machine-configuration"></a>Sanal makine yapılandırması

Batch 'de işlem düğümleri havuzu oluşturduğunuzda, düğüm boyutunu ve işletim sistemini seçebileceğiniz iki seçeneğe sahip olursunuz: Cloud Services yapılandırma ve sanal makine yapılandırması. [Sanal makine yapılandırma](nodes-and-pools.md#virtual-machine-configuration) havuzları, Linux veya Windows görüntülerinden oluşturulan Azure VM 'lerinden oluşur. Sanal makine yapılandırmasıyla bir havuz oluşturduğunuzda, [kullanılabilir bir işlem düğümü boyutu](../virtual-machines/sizes.md), düğümlere yüklenecek sanal makine görüntüsü başvurusu ve Batch düğüm Aracısı SKU 'su (her düğümde çalışan ve düğüm ile Batch hizmeti arasında bir arabirim sağlayan bir program) belirtirsiniz.

### <a name="virtual-machine-image-reference"></a>Sanal makine görüntüsü başvurusu

Batch hizmeti, sanal makine yapılandırmasında işlem düğümleri sağlamak için [sanal makine ölçek kümelerini](../virtual-machine-scale-sets/overview.md) kullanır. [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1)'nden bir görüntü belirtebilir veya [paylaşılan görüntü Galerisi ' ni kullanarak özel bir görüntü hazırlayabilir](batch-sig-images.md).

Bir sanal makine görüntü başvurusu oluşturduğunuzda, aşağıdaki özellikleri belirtmeniz gerekir:

| **Görüntü başvurusu özelliği** | **Örnek** |
| --- | --- |
| Publisher |Canonical |
| Sunduğu |UbuntuServer |
| SKU |18,04-LTS |
| Sürüm |en son |

> [!TIP]
> Bu özellikler hakkında daha fazla bilgi ve Azure [CLI Ile Azure Marketi 'Nde LINUX VM görüntülerini bulma](../virtual-machines/linux/cli-ps-findimage.md)bölümünde Market görüntülerinin nasıl belirtilme hakkında daha fazla bilgi edinebilirsiniz. Bazı Market görüntülerinin Şu anda Batch ile uyumlu olmadığına unutmayın.

### <a name="list-of-virtual-machine-images"></a>Sanal makine görüntülerinin listesi

Tüm Market görüntüleri, mevcut olan toplu Iş düğüm aracılarıyla uyumlu değildir. Batch hizmeti ve bunlara karşılık gelen düğüm Aracısı SKU 'Ları için desteklenen tüm Market sanal makine görüntülerini listelemek için [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) (Python), [Listsupportedimages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) (Batch .net) veya başka bir dil SDK 'sında karşılık gelen API 'yi kullanın.

### <a name="node-agent-sku"></a>Düğüm Aracısı SKU 'SU

[Batch düğüm Aracısı](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md) , havuzdaki her düğüm üzerinde çalışan ve düğüm ile Batch hizmeti arasında komut ve denetim arabirimini sağlayan bir programdır. Farklı işletim sistemleri için SKU olarak bilinen düğüm aracısının farklı uygulamaları vardır. Temelde, bir sanal makine yapılandırması oluşturduğunuzda, önce sanal makine görüntüsü başvurusunu belirttikten sonra görüntüye yüklenecek düğüm aracısını belirlersiniz. Genellikle, her düğüm Aracısı SKU 'SU birden çok sanal makine görüntüsü ile uyumludur. Düğüm Aracısı SKU 'Larının birkaç örneği aşağıda verilmiştir:

- Batch. Node. Ubuntu 18,04
- Batch. Node. CentOS 7
- Batch. Node. Windows AMD64

## <a name="create-a-linux-pool-batch-python"></a>Linux havuzu oluşturma: Batch Python

Aşağıdaki kod parçacığı, bir Ubuntu sunucu işlem düğümleri havuzu oluşturmak için [Python için Microsoft Azure Batch Istemci kitaplığının](https://pypi.python.org/pypi/azure-batch) nasıl kullanılacağına ilişkin bir örnek gösterir. Batch Python modülü hakkında daha fazla ayrıntı için [başvuru belgelerini](/python/api/overview/azure/batch)görüntüleyin.

Bu kod parçacığı açıkça bir [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) oluşturur ve her bir özelliğini (yayımcı, TEKLIF, SKU, sürüm) belirtir. Ancak üretim kodunda, çalışma zamanında kullanılabilir görüntü ve düğüm Aracısı SKU birleşimleri arasından seçim yapmak için [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) yöntemini kullanmanızı öneririz.

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
vm_size = "STANDARD_D2_V3"
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
# virtual machine image to install on the nodes
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent
# to install on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Daha önce belirtildiği gibi, şu anda desteklenen düğüm Aracısı/Market görüntü kombinasyonlarından dinamik olarak seçim yapmak için [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) yöntemini kullanmanızı öneririz (bir [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) oluşturmak yerine). Aşağıdaki Python kod parçacığı, bu yöntemin nasıl kullanılacağını göstermektedir.

```python
# Get the list of supported images from the Batch service
images = client.account.list_supported_images()

# Obtain the desired image reference
image = None
for img in images:
  if (img.image_reference.publisher.lower() == "canonical" and
        img.image_reference.offer.lower() == "ubuntuserver" and
        img.image_reference.sku.lower() == "18.04-lts"):
    image = img
    break

if image is None:
  raise RuntimeError('invalid image reference for desired configuration')

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Linux havuzu oluşturma: Batch .NET

Aşağıdaki kod parçacığı, bir Ubuntu sunucu işlem düğümleri havuzu oluşturmak için [Batch .net](https://www.nuget.org/packages/Microsoft.Azure.Batch/) istemci kitaplığının nasıl kullanılacağına ilişkin bir örnek gösterir. Batch .NET hakkında daha fazla ayrıntı için [başvuru belgelerini](/dotnet/api/microsoft.azure.batch)görüntüleyin.

Aşağıdaki kod parçacığı, şu anda desteklenen Market görüntüsü ve düğüm Aracısı SKU birleşimleri listesinden seçmek için [Pooloperations. ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) yöntemini kullanır. Desteklenen Kombinezonların listesi zaman zaman değiştirebileceğinden bu teknik önerilir. En yaygın olarak desteklenen birleşimler eklenmiştir.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_D2_V3";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<ImageInformation> images =
    batchClient.PoolOperations.ListSupportedImages().ToList();

// Find the appropriate image information
ImageInformation image = null;
foreach (var img in images)
{
    if (img.ImageReference.Publisher == "Canonical" &&
        img.ImageReference.Offer == "UbuntuServer" &&
        img.ImageReference.Sku == "18.04-LTS")
    {
        image = img;
        break;
    }
}

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(image.ImageReference, image.NodeAgentSkuId);

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

Önceki kod parçacığında, desteklenen görüntü ve düğüm Aracısı SKU kombinasyonlarını (önerilir) dinamik olarak listelemek ve seçmek için [Pooloperations. istSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) yöntemini kullanıyor olsa da, bir [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) 'ı açıkça de yapılandırabilirsiniz:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

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

Bu kod, aşağıdaki örneğe benzer bir çıktı olacaktır. Bu durumda, havuz dört Linux düğümü içerir.

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Bir parola yerine, bir düğümde Kullanıcı oluştururken bir SSH ortak anahtarı belirtebilirsiniz. Python SDK 'sında [Computenodeuser](/python/api/azure-batch/azure.batch.models.computenodeuser)üzerinde **ssh_public_key** parametresini kullanın. .NET ' te [Computenodeuser. SshPublicKey](/dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey) özelliğini kullanın.

## <a name="pricing"></a>Fiyatlandırma

Azure Batch Azure Cloud Services ve Azure sanal makineler teknolojisinden oluşturulmuştur. Batch hizmeti 'nin kendisi ücretsiz olarak sunulur, bu da yalnızca toplu Iş çözümlerinizin tükettiği işlem kaynakları (ve buna yönelik ilişkili maliyetler) için ücretlendirilirsiniz. **Sanal makine yapılandırması**' nı seçtiğinizde, [sanal makinelerin fiyatlandırma](https://azure.microsoft.com/pricing/details/virtual-machines/) yapısına göre ücretlendirilirsiniz.

[Uygulama paketlerini](batch-application-packages.md)kullanarak toplu iş düğümlerinize uygulamalar dağıtırsanız, uygulama paketlerinizin kullanacağı Azure depolama kaynakları için de ücretlendirilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Havuz, iş ve görev oluşturma gibi yaygın toplu işlemleri nasıl gerçekleştirebileceğiniz hakkında bilgi için [Azure-Batch-Samples GitHub deposundaki](https://github.com/Azure/azure-batch-samples) [Python kod örneklerini](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch) inceleyin. Python örneklerine eşlik eden [Benioku dosyası](https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md) , gerekli paketlerin nasıl yükleneceğine ilişkin ayrıntılara sahiptir.
- Batch ile [düşük öncelikli VM 'ler](batch-low-pri-vms.md) kullanma hakkında bilgi edinin.
