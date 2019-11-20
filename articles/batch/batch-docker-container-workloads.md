---
title: Kapsayıcı iş yükleri-Azure Batch | Microsoft Docs
description: Azure Batch üzerindeki kapsayıcı görüntülerden uygulamaları çalıştırmayı öğrenin.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 08/09/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: c9e24924472e0bb8dbd0e529b739263469b631fb
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090747"
---
# <a name="run-container-applications-on-azure-batch"></a>Azure Batch kapsayıcı uygulamaları çalıştırma

Azure Batch, Azure 'da çok sayıda Batch bilgi işlem işini çalıştırmanızı ve ölçeklendirmenizi sağlar. Batch görevleri bir Batch havuzundaki doğrudan sanal makinelerde (düğümler) çalıştırılabilir, ancak aynı zamanda düğümlerdeki Docker ile uyumlu kapsayıcılarda görevler çalıştırmak için bir Batch havuzu da ayarlayabilirsiniz. Bu makalede, kapsayıcı görevlerinin çalıştırılmasını destekleyen bir işlem düğümleri havuzu oluşturma ve ardından havuzda kapsayıcı görevleri çalıştırma işlemleri gösterilir. 

Kapsayıcı kavramları ve bir Batch havuzu ve işi oluşturma hakkında bilgi sahibi olmanız gerekir. Kod örnekleri Batch .NET ve Python SDK 'larını kullanır. Ayrıca, kapsayıcı özellikli toplu Iş havuzları oluşturmak ve kapsayıcı görevlerini çalıştırmak için Azure portal dahil olmak üzere diğer toplu SDK 'Ları ve araçları da kullanabilirsiniz.

## <a name="why-use-containers"></a>Kapsayıcılar neden kullanılmalıdır?

Kapsayıcıları kullanmak, uygulamaları çalıştırmak için bir ortamı ve bağımlılıkları yönetmek zorunda kalmadan Batch görevleri çalıştırmanın kolay bir yolunu sunar. Kapsayıcılar, uygulamaları birçok farklı ortamda çalışabilen hafif, taşınabilir ve kendi kendine yeterli birimler olarak dağıtır. Örneğin, bir kapsayıcıyı yerel olarak derleyin ve test edin, sonra kapsayıcı görüntüsünü Azure 'da veya başka bir yerde bir kayıt defterine yükleyin. Kapsayıcı dağıtım modeli, uygulamanızın çalışma zamanı ortamının her zaman doğru şekilde yüklenmesini ve uygulamayı barındırdığınıza her yerde yapılandırılmasını sağlar. Toplu Işteki kapsayıcı tabanlı görevler Ayrıca, uygulama paketleri ve kaynak dosyalarının ve çıkış dosyalarının yönetimi dahil olmak üzere, kapsayıcı olmayan görevlerin özelliklerinden de yararlanabilir. 

## <a name="prerequisites"></a>Önkoşullar

* **SDK sürümleri**: Batch SDK 'Ları aşağıdaki sürümlerden itibaren kapsayıcı görüntülerini destekler:
    * Batch REST API sürüm 2017 -09-01.6.0
    * Batch .NET SDK sürümü 8.0.0
    * Batch Python SDK sürüm 4,0
    * Batch Java SDK 'Sı sürüm 3,0
    * Batch Node. js SDK 'Sı sürüm 3,0

* **Hesaplar**: Azure aboneliğinizde bir Batch hesabı ve isteğe bağlı olarak bir Azure depolama hesabı oluşturmanız gerekir.

* **Desteklenen BIR VM görüntüsü**: Kapsayıcılar yalnızca, "desteklenen sanal makine görüntüleri" bölümünde açıklanan görüntülerden, sanal makine yapılandırmasıyla oluşturulan havuzlarda desteklenir. Özel bir görüntü sağlarsanız, bir [sanal makine havuzu oluşturmak için aşağıdaki bölümdeki noktalara ve yönetilen özel görüntü kullanma](batch-custom-images.md)' daki gereksinimlere bakın. 

### <a name="limitations"></a>Sınırlamalar

* Batch yalnızca Linux havuzlarında çalışan kapsayıcılar için RDMA desteği sağlar

* Windows kapsayıcı iş yükleri için, havuzunuz için birden çok Ore VM boyutu seçmeniz önerilir

## <a name="supported-virtual-machine-images"></a>Desteklenen sanal makine görüntüleri

Kapsayıcı iş yükleri için bir VM işlem düğümleri havuzu oluşturmak üzere aşağıdaki desteklenen Windows veya Linux görüntülerinden birini kullanın. Batch ile uyumlu Market görüntüleri hakkında daha fazla bilgi için bkz. [sanal makine görüntülerinin listesi](batch-linux-nodes.md#list-of-virtual-machine-images). 

### <a name="windows-images"></a>Windows görüntüleri

Windows kapsayıcı iş yükleri için Batch Şu anda Azure Marketi 'nde **kapsayıcılar görüntüsünü Içeren Windows Server 2016 Datacenter** 'ı desteklemektedir. Windows üzerinde yalnızca Docker kapsayıcı görüntüleri desteklenir.

Ayrıca, Windows üzerinde Docker çalıştıran VM 'lerden özel görüntüler de oluşturabilirsiniz.

### <a name="linux-images"></a>Linux görüntüleri

Linux kapsayıcı iş yükleri için Batch Şu anda Azure Marketi 'nde Microsoft Azure Batch tarafından yayımlanan aşağıdaki Linux görüntülerini desteklemektedir:

* **Azure Batch kapsayıcı havuzları için CentOS**

* **Azure Batch kapsayıcı havuzları için CentOS (RDMA sürücüleriyle birlikte)**

* **Azure Batch kapsayıcı havuzları için Ubuntu sunucusu**

* **Azure Batch kapsayıcı havuzları için Ubuntu Server (RDMA sürücülerle)**

Bu görüntüler yalnızca Azure Batch havuzlarında kullanılmak üzere desteklenir. Bunlar özelliği:

* Önceden yüklenmiş bir [Moby](https://github.com/moby/moby) kapsayıcı çalışma zamanı 

* Azure N serisi VM 'lerde dağıtımı kolaylaştırmak için önceden yüklenmiş NVıDıA GPU sürücüleri

* Önceden yüklenmiş RDMA sürücülerine sahip veya olmayan görüntü seçiminiz. Bu sürücüler, havuz düğümlerinin RDMA özellikli VM boyutlarında dağıtıldığında Azure RDMA ağına erişmesini sağlar. 

Batch ile uyumlu bir Linux dağıtımlarından birinde Docker çalıştıran VM 'lerden özel görüntüler de oluşturabilirsiniz. Kendi özel Linux görüntünüzü sağlamayı seçerseniz, bir [sanal makine havuzu oluşturmak için yönetilen özel görüntü kullanma](batch-custom-images.md)bölümündeki yönergelere bakın.

Özel görüntüde Docker desteği için [Docker Community Edition (CE)](https://www.docker.com/community-edition) veya [Docker ENTERPRISE Edition (ee)](https://www.docker.com/enterprise-edition)yükleyin.

Özel bir Linux görüntüsü kullanımıyla ilgili ek konular:

* Özel bir görüntü kullanırken Azure N serisi boyutlarının GPU performansının avantajlarından yararlanmak için, NVıDıA sürücülerini önceden yüklemelisiniz. Ayrıca, NVıDıA GPU 'Lar, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker)Için Docker altyapısı yardımcı programını yüklemeniz gerekir.

* Azure RDMA ağına erişmek için, RDMA özellikli bir VM boyutu kullanın. Gerekli RDMA sürücüleri, Batch tarafından desteklenen CentOS HPC ve Ubuntu görüntülerine yüklenir. MPı iş yüklerini çalıştırmak için ek yapılandırma gerekebilir. Bkz. [Batch havuzunda RDMA özellikli veya GPU özellikli örnekler kullanma](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Batch havuzu için kapsayıcı yapılandırması

Bir Batch havuzunu kapsayıcı iş yüklerini çalıştıracak şekilde etkinleştirmek için, havuzun [Virtualmachineconfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) nesnesinde [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) ayarlarını belirtmeniz gerekir. (Bu makale Batch .NET API başvurusu için bağlantılar sağlar. Karşılık gelen ayarlar [Batch Python](/python/api/overview/azure/batch) API 'sidir.)

Aşağıdaki örneklerde gösterildiği gibi, önceden getirilen kapsayıcı görüntüleriyle veya olmadan kapsayıcı özellikli bir havuz oluşturabilirsiniz. Çekme (veya önceden getirme) işlemi, Internet 'teki Docker Hub 'ından veya başka bir kapsayıcı kayıt defterinden kapsayıcı görüntülerini önceden yüklemenize olanak sağlar. En iyi performansı elde etmek için, Batch hesabıyla aynı bölgedeki bir [Azure Container kayıt defteri](../container-registry/container-registry-intro.md) kullanın.

Kapsayıcı görüntülerinin önceden getirilirken faydalanması, görevlerin ilk kez çalışmaya başlaması durumunda kapsayıcı görüntüsünün indirilmek zorunda olmadığından emin olur. Kapsayıcı yapılandırması, havuz oluşturulduğunda kapsayıcı görüntülerini VM 'lere çeker. Havuzda çalışan görevler daha sonra kapsayıcı görüntüleri ve kapsayıcı çalıştırma seçenekleri listesine başvurabilir.


### <a name="pool-without-prefetched-container-images"></a>Önceden getirilen kapsayıcı görüntüleri olmadan havuz

Kapsayıcı görüntüleri önceden getirilmeksizin kapsayıcı özellikli bir havuz yapılandırmak için aşağıdaki Python örneğinde `ContainerConfiguration` gösterildiği `VirtualMachineConfiguration` gibi, ve nesneleri tanımlayın. Bu örnek, marketten Azure Batch kapsayıcı havuzları görüntüsü için Ubuntu sunucusunu kullanır.


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


### <a name="prefetch-images-for-container-configuration"></a>Kapsayıcı yapılandırması için görüntüleri önceden getirme

Havuzdaki kapsayıcı görüntülerini önceden almak için kapsayıcı görüntülerinin listesini (`container_image_names`Python 'da) `ContainerConfiguration`öğesine ekleyin. 

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


Aşağıdaki C# örnek, [Docker Hub 'ından](https://hub.docker.com)bir TensorFlow görüntüsünü önceden almak istediğinizi varsayar. Bu örnek, havuz düğümlerinde VM konağında çalışan bir başlangıç görevi içerir. Bir başlangıç görevini, örneğin, kapsayıcılardan erişilebilen bir dosya sunucusunu bağlamak için konakta çalıştırabilirsiniz.

```csharp

ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native host command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);
...
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Özel kapsayıcı kayıt defterinden görüntüleri önceden getirme

Ayrıca, özel bir kapsayıcı kayıt defteri sunucusunda kimlik doğrulaması yaparak kapsayıcı görüntülerini önceden ayarlayabilirsiniz. Aşağıdaki örnekte `ContainerConfiguration` , ve `VirtualMachineConfiguration` nesneleri özel bir Azure Container Registry 'den özel bir TensorFlow görüntüsünü önceden getirme. Görüntü başvurusu, önceki örnekteki ile aynıdır.

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

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

* Kapsayıcıya özgü ayarları yapılandırmak için görev sınıflarının özelliğinikullanın.`ContainerSettings` Bu ayarlar [Taskcontainersettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) sınıfı tarafından tanımlanır. `--rm` Kapsayıcı seçeneğinin toplu iş tarafından sunulduğundan bu yana `--runtime` ek bir seçenek gerektirmediğini unutmayın. 

* Görevleri kapsayıcı görüntülerinde çalıştırırsanız, [bulut görevi](/dotnet/api/microsoft.azure.batch.cloudtask) ve [İş Yöneticisi görevi](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) kapsayıcı ayarları gerektirir. Ancak, [Başlangıç görevi](/dotnet/api/microsoft.azure.batch.starttask), [iş hazırlama görevi](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)ve [iş bırakma görevi](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) , kapsayıcı ayarları gerektirmez (yani bir kapsayıcı bağlamı içinde veya doğrudan düğüm üzerinde çalışabilir).

### <a name="container-task-command-line"></a>Kapsayıcı görevi komut satırı

Bir kapsayıcı görevi çalıştırdığınızda, toplu Işlem, görevde belirtilen görüntüyü kullanarak bir kapsayıcı oluşturmak için [Docker Create](https://docs.docker.com/engine/reference/commandline/create/) komutunu otomatik olarak kullanır. Batch daha sonra kapsayıcıda görev yürütmeyi denetler. 

Kapsayıcı olmayan Batch görevlerinde olduğu gibi, bir kapsayıcı görevi için bir komut satırı ayarlarsınız. Batch kapsayıcıyı otomatik olarak oluşturduğundan, komut satırı yalnızca kapsayıcıda çalıştırılacak komutu veya komutları belirtir.

Bir Batch görevinin kapsayıcı görüntüsü bir [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) betiği ile yapılandırıldıysa, Komut satırlarınızı varsayılan giriş noktasını kullanacak şekilde veya geçersiz kılmak üzere ayarlayabilirsiniz: 

* Kapsayıcı görüntüsünün varsayılan GIRIŞ noktasını kullanmak için, görev komut satırını boş dizeye `""`ayarlayın.

* Varsayılan giriş noktasını geçersiz kılmak için ya da görüntüde bir EntryPoint yoksa, örneğin veya `/app/myapp` `/bin/sh -c python myscript.py`gibi bir komut satırını uygun olarak ayarlayın.

İsteğe bağlı [containerrunoptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) , kapsayıcıyı oluşturmak ve çalıştırmak için `docker create` Batch 'in kullandığı komuta sağladığınız ek bağımsız değişkenlerdir. Örneğin, kapsayıcı için çalışma dizini ayarlamak için `--workdir <directory>` seçeneğini ayarlayın. Ek seçenekler için [Docker oluşturma](https://docs.docker.com/engine/reference/commandline/create/) başvurusuna bakın.

### <a name="container-task-working-directory"></a>Kapsayıcı görevi çalışma dizini

Bir Batch kapsayıcı görevi, kapsayıcıda düzenli (kapsayıcı olmayan) bir görevde Dizin toplu Iş kümelerine çok benzeyen bir çalışma dizininde yürütülür. Bu çalışma dizininin görüntüde yapılandırıldıysa [iş](https://docs.docker.com/engine/reference/builder/#workdir) dizininden farklı olduğunu veya varsayılan kapsayıcı çalışma dizinini (`C:\` bir Windows kapsayıcısında veya `/` bir Linux kapsayıcısında) farklı olduğunu unutmayın. 

Batch kapsayıcı görevi için:

* Konak düğümündeki (Azure Batch dizinlerin `AZ_BATCH_NODE_ROOT_DIR` kökü) her yinelemeli olarak tüm dizinler kapsayıcı ile eşleştirilir
* Tüm görev ortam değişkenleri, kapsayıcınıza eşlenir
* Düğümdeki görev çalışma dizini `AZ_BATCH_TASK_WORKING_DIR` , düzenli bir görevle aynı şekilde ayarlanır ve kapsayıcıya eşlenir. 

Bu eşlemeler kapsayıcı görevlerle, kapsayıcı olmayan görevlerle aynı şekilde çalışmanıza olanak sağlar. Örneğin, uygulama paketleri kullanarak uygulamaları, Azure depolama 'dan kaynak dosyalarına erişin, görev ortamı ayarlarını kullanın ve kapsayıcı durdurulduktan sonra Görev çıkış dosyalarını kalıcı hale getirin.

### <a name="troubleshoot-container-tasks"></a>Kapsayıcı görevlerinin sorunlarını giderme

Kapsayıcı göreviniz beklenen şekilde çalışmazsa, kapsayıcı görüntüsünün WORKDIR veya ENTRYPOINT yapılandırması hakkında bilgi almanız gerekebilir. Yapılandırmayı görmek için [Docker Image İnceleme](https://docs.docker.com/engine/reference/commandline/image_inspect/) komutunu çalıştırın. 

Gerekirse, görüntüye göre kapsayıcı görevinin ayarlarını ayarlayın:

* Görev komut satırında mutlak bir yol belirtin. Görev komut satırı için görüntünün varsayılan GIRIŞ noktası kullanılırsa, mutlak bir yolun ayarlandığından emin olun.

* Görevin kapsayıcı çalıştırma seçenekleri ' nde, çalışma dizinini görüntüdeki WORKDIR ile eşleşecek şekilde değiştirin. Örneğin, ayarlayın `--workdir /app`.

## <a name="container-task-examples"></a>Kapsayıcı görevi örnekleri

Aşağıdaki Python kod parçacığında, Docker Hub 'dan çekilen kurgusal bir görüntüden oluşturulan kapsayıcıda çalışan temel bir komut satırı gösterilmektedir. Burada kapsayıcı seçeneği, görev bittikten sonra kapsayıcıyı kaldırır `--workdir` ve seçeneği bir çalışma dizini ayarlar. `--rm` Komut satırı, küçük bir dosyayı konaktaki görev çalışma dizinine yazan basit bir kabuk komutuyla kapsayıcı GIRIŞ noktasını geçersiz kılar. 

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

Aşağıdaki C# örnekte bir bulut görevi için temel kapsayıcı ayarları gösterilmektedir:

```csharp
// Simple container task command

string cmdLine = "c:\\app\\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "myimage",
    containerRunOptions: "--rm --workdir c:\\app"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>Sonraki adımlar

* Ayrıca bkz. [toplu shipyarda](https://github.com/Azure/batch-shipyard) araç seti, Azure Batch dağıtım iş yüklerinin, [sevk Bahçe tariflerine](https://github.com/Azure/batch-shipyard/tree/master/recipes)göre kolayca dağıtılması için.

* Linux üzerinde Docker CE yükleme ve kullanma hakkında daha fazla bilgi için [Docker](https://docs.docker.com/engine/installation/) belgelerine bakın.

* Özel görüntüleri kullanma hakkında daha fazla bilgi için bkz. [sanal makine havuzu oluşturmak için yönetilen özel görüntü kullanma](batch-custom-images.md).

* Kapsayıcı tabanlı sistemler oluşturmaya yönelik bir çerçeve olan [Moby projesi](https://mobyproject.org/)hakkında daha fazla bilgi edinin.
