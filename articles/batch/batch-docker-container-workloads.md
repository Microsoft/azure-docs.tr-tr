---
title: Kapsayıcı iş yükleri
description: Azure Batch üzerindeki kapsayıcı görüntülerden uygulamaları çalıştırmayı ve ölçeklendirmeyi öğrenin. Kapsayıcı görevlerinin çalıştırılmasını destekleyen bir işlem düğümleri havuzu oluşturun.
ms.topic: how-to
ms.date: 10/06/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 9d8776ba8e683cd14c766fead1e7238a6c24d000
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91843456"
---
# <a name="run-container-applications-on-azure-batch"></a>Azure Batch kapsayıcı uygulamaları çalıştırma

Azure Batch, Azure 'da çok sayıda Batch bilgi işlem işini çalıştırmanızı ve ölçeklendirmenizi sağlar. Batch görevleri bir Batch havuzundaki doğrudan sanal makinelerde (düğümler) çalıştırılabilir, ancak aynı zamanda düğümlerdeki Docker ile uyumlu kapsayıcılarda görevler çalıştırmak için bir Batch havuzu da ayarlayabilirsiniz. Bu makalede, kapsayıcı görevlerinin çalıştırılmasını destekleyen bir işlem düğümleri havuzu oluşturma ve ardından havuzda kapsayıcı görevleri çalıştırma işlemleri gösterilir.

Kod örnekleri burada Batch .NET ve Python SDK 'larını kullanır. Ayrıca, kapsayıcı özellikli toplu Iş havuzları oluşturmak ve kapsayıcı görevlerini çalıştırmak için Azure portal dahil olmak üzere diğer toplu SDK 'Ları ve araçları da kullanabilirsiniz.

## <a name="why-use-containers"></a>Kapsayıcılar neden kullanılmalıdır?

Kapsayıcıları kullanmak, uygulamaları çalıştırmak için bir ortamı ve bağımlılıkları yönetmek zorunda kalmadan Batch görevleri çalıştırmanın kolay bir yolunu sunar. Kapsayıcılar, uygulamaları birçok farklı ortamda çalışabilen hafif, taşınabilir ve kendi kendine yeterli birimler olarak dağıtır. Örneğin, bir kapsayıcıyı yerel olarak derleyin ve test edin, sonra kapsayıcı görüntüsünü Azure 'da veya başka bir yerde bir kayıt defterine yükleyin. Kapsayıcı dağıtım modeli, uygulamanızın çalışma zamanı ortamının her zaman doğru şekilde yüklenmesini ve uygulamayı barındırdığınıza her yerde yapılandırılmasını sağlar. Toplu Işteki kapsayıcı tabanlı görevler Ayrıca, uygulama paketleri ve kaynak dosyalarının ve çıkış dosyalarının yönetimi dahil olmak üzere, kapsayıcı olmayan görevlerin özelliklerinden de yararlanabilir.

## <a name="prerequisites"></a>Önkoşullar

Kapsayıcı kavramları ve bir Batch havuzu ve işi oluşturma hakkında bilgi sahibi olmanız gerekir.

- **SDK sürümleri**: Batch SDK 'ları, aşağıdaki sürümlerden itibaren kapsayıcı görüntülerini destekler:
  - Batch REST API sürüm 2017 -09-01.6.0
  - Batch .NET SDK sürümü 8.0.0
  - Batch Python SDK sürüm 4,0
  - Batch Java SDK 'Sı sürüm 3,0
  - Batch Node.js SDK sürüm 3,0

- **Hesaplar**: Azure aboneliğinizde bir Batch hesabı ve isteğe bağlı olarak bir Azure depolama hesabı oluşturmanız gerekir.

- **Desteklenen BIR VM görüntüsü**: kapsayıcılar, "desteklenen sanal makine görüntüleri" bölümünde açıklanan görüntülerden yalnızca sanal makine yapılandırmasıyla oluşturulan havuzlarda desteklenir. Özel bir görüntü sağlarsanız, bir [sanal makine havuzu oluşturmak için aşağıdaki bölümdeki noktalara ve yönetilen özel görüntü kullanma](batch-custom-images.md)' daki gereksinimlere bakın.

Aşağıdaki sınırlamaları göz önünde bulundurun:

- Batch yalnızca Linux havuzlarında çalışan kapsayıcılar için RDMA desteği sağlar.

- Windows kapsayıcı iş yükleri için, havuzunuz için çok birimli bir VM boyutu seçmeyi öneririz.

## <a name="supported-virtual-machine-images"></a>Desteklenen sanal makine görüntüleri

Kapsayıcı iş yükleri için bir VM işlem düğümleri havuzu oluşturmak üzere aşağıdaki desteklenen Windows veya Linux görüntülerinden birini kullanın. Batch ile uyumlu Market görüntüleri hakkında daha fazla bilgi için bkz. [sanal makine görüntülerinin listesi](batch-linux-nodes.md#list-of-virtual-machine-images).

### <a name="windows-support"></a>Windows desteği

Batch, kapsayıcı desteği göstergeleri olan Windows Server görüntülerini destekler. Genellikle bu görüntü SKU adları, veya ile sonlardır `-with-containers` `-with-containers-smalldisk` . Ayrıca, [Toplu işteki desteklenen tüm görüntüleri listelerken API,](batch-linux-nodes.md#list-of-virtual-machine-images) `DockerCompatible` görüntü Docker kapsayıcılarını destekliyorsa bir özelliği gösterir.

Ayrıca, Windows üzerinde Docker çalıştıran VM 'lerden özel görüntüler de oluşturabilirsiniz.

### <a name="linux-support"></a>Linux desteği

Linux kapsayıcı iş yükleri için Batch Şu anda, özel bir görüntüye gerek olmadan Azure Marketi 'nde Microsoft Azure Batch tarafından yayımlanan aşağıdaki Linux görüntülerini desteklemektedir.

#### <a name="vm-sizes-without-rdma"></a>RDMA olmadan VM boyutları

- 'In `microsoft-azure-batch`
  - Sunar `centos-container`
  - Sunar `ubuntu-server-container`

#### <a name="vm-sizes-with-rdma"></a>RDMA ile VM boyutları

- 'In `microsoft-azure-batch`
  - Sunar `centos-container-rdma`
  - Sunar `ubuntu-server-container-rdma`

Bu görüntüler yalnızca Azure Batch havuzlarında kullanılmak üzere desteklenir ve Docker kapsayıcı yürütmesi için tasarlanmıştır. Bunlar özelliği:

- Bir önceden yüklenmiş Docker ile uyumlu bir [Moby](https://github.com/moby/moby) kapsayıcı çalışma zamanı

- Azure N serisi VM 'lerde dağıtımı kolaylaştırmak için önceden yüklenmiş NVıDıA GPU sürücüleri ve NVıDıA kapsayıcı çalışma zamanı

- Soneki olan görüntüler için InfiniBand RDMA VM boyutları desteğiyle önceden yüklenmiş/önceden yapılandırılmış görüntü `-rdma` . Şu anda bu görüntüler SR-ıOV ıB/RDMA VM boyutlarını desteklemez.

Batch ile uyumlu bir Linux dağıtımlarından birinde Docker çalıştıran VM 'lerden özel görüntüler de oluşturabilirsiniz. Kendi özel Linux görüntünüzü sağlamayı seçerseniz, bir [sanal makine havuzu oluşturmak için yönetilen özel görüntü kullanma](batch-custom-images.md)bölümündeki yönergelere bakın.

Özel görüntüde Docker desteği için [Docker Community Edition (CE)](https://www.docker.com/community-edition) veya [Docker ENTERPRISE Edition (ee)](https://www.docker.com/enterprise-edition)yükleyin.

Özel bir Linux görüntüsü kullanımıyla ilgili ek konular:

- Özel bir görüntü kullanırken Azure N serisi boyutlarının GPU performansının avantajlarından yararlanmak için, NVıDıA sürücülerini önceden yüklemelisiniz. Ayrıca, NVıDıA GPU 'Lar, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker)Için Docker altyapısı yardımcı programını yüklemeniz gerekir.

- Azure RDMA ağına erişmek için, RDMA özellikli bir VM boyutu kullanın. Gerekli RDMA sürücüleri, Batch tarafından desteklenen CentOS HPC ve Ubuntu görüntülerine yüklenir. MPı iş yüklerini çalıştırmak için ek yapılandırma gerekebilir. Bkz. [Batch havuzunda RDMA özellikli veya GPU özellikli örnekler kullanma](batch-pool-compute-intensive-sizes.md).

## <a name="container-configuration-for-batch-pool"></a>Batch havuzu için kapsayıcı yapılandırması

Bir Batch havuzunu kapsayıcı iş yüklerini çalıştıracak şekilde etkinleştirmek için, havuzun [Virtualmachineconfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) nesnesinde [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) ayarlarını belirtmeniz gerekir. (Bu makale Batch .NET API başvurusu için bağlantılar sağlar. Karşılık gelen ayarlar [Batch Python](/python/api/overview/azure/batch) API 'sidir.)

Aşağıdaki örneklerde gösterildiği gibi, önceden getirilen kapsayıcı görüntüleriyle veya olmadan kapsayıcı özellikli bir havuz oluşturabilirsiniz. Çekme (veya önceden getirme) işlemi, Internet 'teki Docker Hub 'ından veya başka bir kapsayıcı kayıt defterinden kapsayıcı görüntülerini önceden yüklemenize olanak sağlar. En iyi performansı elde etmek için, Batch hesabıyla aynı bölgedeki bir [Azure Container kayıt defteri](../container-registry/container-registry-intro.md) kullanın.

Kapsayıcı görüntülerinin önceden getirilirken faydalanması, görevlerin ilk kez çalışmaya başlaması durumunda kapsayıcı görüntüsünün indirilmek zorunda olmadığından emin olur. Kapsayıcı yapılandırması, havuz oluşturulduğunda kapsayıcı görüntülerini VM 'lere çeker. Havuzda çalışan görevler daha sonra kapsayıcı görüntüleri ve kapsayıcı çalıştırma seçenekleri listesine başvurabilir.

### <a name="pool-without-prefetched-container-images"></a>Önceden getirilen kapsayıcı görüntüleri olmadan havuz

Kapsayıcı görüntüleri önceden getirilmeksizin kapsayıcı özellikli bir havuz yapılandırmak için `ContainerConfiguration` `VirtualMachineConfiguration` Aşağıdaki örneklerde gösterildiği gibi, ve nesneleri tanımlayın. Bu örneklerde, marketten Azure Batch kapsayıcı havuzları görüntüsü için Ubuntu sunucusu kullanılır.

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration. This is required even though there are no prefetched images.
"""

container_conf = batch.models.ContainerConfiguration()

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration. This is required even though there are no prefetched images.
ContainerConfiguration containerConfig = new ContainerConfiguration();

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 1,
    virtualMachineSize: "STANDARD_D1_V2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

### <a name="prefetch-images-for-container-configuration"></a>Kapsayıcı yapılandırması için görüntüleri önceden getirme

Havuzdaki kapsayıcı görüntülerini önceden almak için kapsayıcı görüntülerinin listesini ( `container_image_names` Python 'da) öğesine ekleyin `ContainerConfiguration` .

Aşağıdaki temel Python örneği, [Docker Hub 'ından](https://hub.docker.com)standart bir Ubuntu kapsayıcı görüntüsünün nasıl önceden alınacağını gösterir.

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub.
"""

container_conf = batch.models.ContainerConfiguration(
    container_image_names=['ubuntu'])

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```

Aşağıdaki C# örneği, [Docker Hub 'ından](https://hub.docker.com)bir TensorFlow görüntüsünü önceden almak istediğinizi varsayar. Bu örnek, havuz düğümlerinde VM konağında çalışan bir başlangıç görevi içerir. Bir başlangıç görevini, örneğin, kapsayıcılardan erişilebilen bir dosya sunucusunu bağlamak için konakta çalıştırabilirsiniz.

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

ContainerRegistry containerRegistry = new ContainerRegistry(
    registryServer: "https://hub.docker.com",
    userName: "UserName",
    password: "YourPassword"                
);

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration();
containerConfig.ContainerImageNames = new List<string> { "tensorflow/tensorflow:latest-gpu" };
containerConfig.ContainerRegistries = new List<ContainerRegistry> { containerRegistry };

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Set a native host command line start task
StartTask startTaskContainer = new StartTask( commandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start the task in the pool
pool.StartTask = startTaskContainer;
...
```

### <a name="prefetch-images-from-a-private-container-registry"></a>Özel kapsayıcı kayıt defterinden görüntüleri önceden getirme

Ayrıca, özel bir kapsayıcı kayıt defteri sunucusunda kimlik doğrulaması yaparak kapsayıcı görüntülerini önceden ayarlayabilirsiniz. Aşağıdaki örneklerde `ContainerConfiguration` ve `VirtualMachineConfiguration` nesneleri özel bir Azure Container Registry 'den özel bir TensorFlow görüntüsünü önceden getirme. Görüntü başvurusu, önceki örnekteki ile aynıdır.

```python
image_ref_to_use = batch.models.ImageReference(
        publisher='microsoft-azure-batch',
        offer='ubuntu-server-container',
        sku='16-04-lts',
        version='latest')

# Specify a container registry
container_registry = batch.models.ContainerRegistry(
        registry_server="myRegistry.azurecr.io",
        user_name="myUsername",
        password="myPassword")

# Create container configuration, prefetching Docker images from the container registry
container_conf = batch.models.ContainerConfiguration(
        container_image_names = ["myRegistry.azurecr.io/samples/myImage"],
        container_registries =[container_registry])

new_pool = batch.models.PoolAddParameter(
            id="myPool",
            virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
                image_reference=image_ref_to_use,
                container_configuration=container_conf,
                node_agent_sku_id='batch.node.ubuntu 16.04'),
            vm_size='STANDARD_D1_V2',
            target_dedicated_nodes=1)
```

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry(
    registryServer: "myContainerRegistry.azurecr.io",
    userName: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration();
containerConfig.ContainerImageNames = new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" };
containerConfig.ContainerRegistries = new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```

## <a name="container-settings-for-the-task"></a>Görevin kapsayıcı ayarları

Kapsayıcı etkin bir havuzda bir kapsayıcı görevi çalıştırmak için kapsayıcıya özgü ayarları belirtin. Ayarlar, kullanılacak görüntü, kayıt defteri ve kapsayıcı çalıştırma seçeneklerini içerir.

- `ContainerSettings`Kapsayıcıya özgü ayarları yapılandırmak için görev sınıflarının özelliğini kullanın. Bu ayarlar [Taskcontainersettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) sınıfı tarafından tanımlanır. `--rm`Kapsayıcı seçeneğinin `--runtime` toplu iş tarafından sunulduğundan bu yana ek bir seçenek gerektirmediğini unutmayın.

- Görevleri kapsayıcı görüntülerinde çalıştırırsanız, [bulut görevi](/dotnet/api/microsoft.azure.batch.cloudtask) ve [İş Yöneticisi görevi](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) kapsayıcı ayarları gerektirir. Ancak, [Başlangıç görevi](/dotnet/api/microsoft.azure.batch.starttask), [iş hazırlama görevi](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)ve [iş bırakma görevi](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) , kapsayıcı ayarları gerektirmez (yani bir kapsayıcı bağlamı içinde veya doğrudan düğüm üzerinde çalışabilir).

- Windows için görevlerin, [yükseltme düzeyi](/rest/api/batchservice/task/add#elevationlevel) olarak ayarlanmış şekilde çalıştırılması gerekir `admin` . 

- Linux için Batch Kullanıcı/Grup iznini kapsayıcıya eşleyebilir. Kapsayıcı içindeki herhangi bir klasöre erişim için yönetici izni gerekiyorsa, görevi yönetici yükseltme düzeyiyle havuz kapsamı olarak çalıştırmanız gerekebilir. Bu, toplu Işlemin görevi kapsayıcı bağlamında kök olarak çalıştırmasını sağlayacaktır. Aksi takdirde, yönetici olmayan bir kullanıcının bu klasörlere erişimi olmayabilir.

- GPU özellikli donanımla kapsayıcı havuzları için toplu Işlem, kapsayıcı görevleri için GPU 'YU otomatik olarak etkinleştirecek, bu nedenle `–gpus` bağımsız değişkenini içermemelidir.

### <a name="container-task-command-line"></a>Kapsayıcı görevi komut satırı

Bir kapsayıcı görevi çalıştırdığınızda, toplu Işlem, görevde belirtilen görüntüyü kullanarak bir kapsayıcı oluşturmak için [Docker Create](https://docs.docker.com/engine/reference/commandline/create/) komutunu otomatik olarak kullanır. Batch daha sonra kapsayıcıda görev yürütmeyi denetler.

Kapsayıcı olmayan Batch görevlerinde olduğu gibi, bir kapsayıcı görevi için bir komut satırı ayarlarsınız. Batch kapsayıcıyı otomatik olarak oluşturduğundan, komut satırı yalnızca kapsayıcıda çalıştırılacak komutu veya komutları belirtir.

Bir Batch görevinin kapsayıcı görüntüsü bir [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) betiği ile yapılandırıldıysa, Komut satırlarınızı varsayılan giriş noktasını kullanacak şekilde veya geçersiz kılmak üzere ayarlayabilirsiniz:

- Kapsayıcı görüntüsünün varsayılan GIRIŞ noktasını kullanmak için, görev komut satırını boş dizeye ayarlayın `""` .

- Varsayılan GIRIŞ noktasını geçersiz kılmak için ya da görüntüde bir ENTRYPOINT yoksa, örneğin veya gibi bir komut satırını uygun olarak ayarlayın `/app/myapp` `/bin/sh -c python myscript.py` .

İsteğe bağlı [Containerrunoptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) , `docker create` kapsayıcıyı oluşturmak ve çalıştırmak için Batch 'in kullandığı komuta sağladığınız ek bağımsız değişkenlerdir. Örneğin, kapsayıcı için çalışma dizini ayarlamak için `--workdir <directory>` seçeneğini ayarlayın. Ek seçenekler için [Docker oluşturma](https://docs.docker.com/engine/reference/commandline/create/) başvurusuna bakın.

### <a name="container-task-working-directory"></a>Kapsayıcı görevi çalışma dizini

Bir Batch kapsayıcı görevi, kapsayıcıda düzenli (kapsayıcı olmayan) bir görevde Dizin toplu Iş kümelerine çok benzeyen bir çalışma dizininde yürütülür. Bu çalışma dizininin görüntüde yapılandırıldıysa [iş](https://docs.docker.com/engine/reference/builder/#workdir) dizininden farklı olduğunu veya varsayılan kapsayıcı çalışma dizinini ( `C:\`  bir Windows kapsayıcısında veya `/` bir Linux kapsayıcısında) farklı olduğunu unutmayın.

Batch kapsayıcı görevi için:

- `AZ_BATCH_NODE_ROOT_DIR`Konak düğümündeki (Azure Batch dizinlerin kökü) her yinelemeli olarak tüm dizinler kapsayıcı ile eşleştirilir
- Tüm görev ortam değişkenleri, kapsayıcınıza eşlenir
- Düğümdeki görev çalışma dizini, `AZ_BATCH_TASK_WORKING_DIR` düzenli bir görevle aynı şekilde ayarlanır ve kapsayıcıya eşlenir.

Bu eşlemeler kapsayıcı görevlerle, kapsayıcı olmayan görevlerle aynı şekilde çalışmanıza olanak sağlar. Örneğin, uygulama paketleri kullanarak uygulamaları, Azure depolama 'dan kaynak dosyalarına erişin, görev ortamı ayarlarını kullanın ve kapsayıcı durdurulduktan sonra Görev çıkış dosyalarını kalıcı hale getirin.

### <a name="troubleshoot-container-tasks"></a>Kapsayıcı görevlerinin sorunlarını giderme

Kapsayıcı göreviniz beklenen şekilde çalışmazsa, kapsayıcı görüntüsünün WORKDIR veya ENTRYPOINT yapılandırması hakkında bilgi almanız gerekebilir. Yapılandırmayı görmek için [Docker Image İnceleme](https://docs.docker.com/engine/reference/commandline/image_inspect/) komutunu çalıştırın.

Gerekirse, görüntüye göre kapsayıcı görevinin ayarlarını ayarlayın:

- Görev komut satırında mutlak bir yol belirtin. Görev komut satırı için görüntünün varsayılan GIRIŞ noktası kullanılırsa, mutlak bir yolun ayarlandığından emin olun.
- Görevin kapsayıcı çalıştırma seçenekleri ' nde, çalışma dizinini görüntüdeki WORKDIR ile eşleşecek şekilde değiştirin. Örneğin, ayarlayın `--workdir /app` .

## <a name="container-task-examples"></a>Kapsayıcı görevi örnekleri

Aşağıdaki Python kod parçacığında, Docker Hub 'dan çekilen kurgusal bir görüntüden oluşturulan kapsayıcıda çalışan temel bir komut satırı gösterilmektedir. Burada `--rm` kapsayıcı seçeneği, görev bittikten sonra kapsayıcıyı kaldırır ve `--workdir` seçeneği bir çalışma dizini ayarlar. Komut satırı, küçük bir dosyayı konaktaki görev çalışma dizinine yazan basit bir kabuk komutuyla kapsayıcı GIRIŞ noktasını geçersiz kılar.

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='myimage',
    container_run_options='--rm --workdir /')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/sh -c \"echo \'hello world\' > $AZ_BATCH_TASK_WORKING_DIR/output.txt\"',
    container_settings=task_container_settings
)
```

Aşağıdaki C# örneği bir bulut görevi için temel kapsayıcı ayarlarını göstermektedir:

```csharp
// Simple container task command
string cmdLine = "c:\\app\\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "myimage",
    containerRunOptions: "--rm --workdir c:\\app"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    commandline: cmdLine);
containerTask.ContainerSettings = cmdContainerSettings;
```

## <a name="next-steps"></a>Sonraki adımlar

- [Shipbahçe tariflerine](https://github.com/Azure/batch-shipyard/tree/master/recipes)göre Azure Batch kapsayıcı iş yüklerinin kolay dağıtımı için bkz. [Batch shipbahçe](https://github.com/Azure/batch-shipyard) araç seti.
- Linux üzerinde Docker CE 'yi yükleme ve kullanma hakkında bilgi için bkz. [Docker](https://docs.docker.com/engine/installation/) belgeleri.
- [Yönetilen özel görüntünün bir sanal makine havuzu oluşturmak için nasıl kullanılacağını](batch-custom-images.md)öğrenin.
- Kapsayıcı tabanlı sistemler oluşturmaya yönelik bir çerçeve olan [Moby projesi](https://mobyproject.org/)hakkında daha fazla bilgi edinin.
