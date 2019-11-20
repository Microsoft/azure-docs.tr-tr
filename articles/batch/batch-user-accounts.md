---
title: Görevleri Kullanıcı hesapları altında Çalıştır-Azure Batch | Microsoft Docs
description: Azure Batch görevleri çalıştırmak için Kullanıcı hesaplarını yapılandırma
services: batch
author: laurenhughes
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 820e979c41ddc1c1cf14456ed77a4a55e353ab12
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094272"
---
> [!NOTE] 
> Bu makalede ele alınan kullanıcı hesapları, güvenlik nedenleriyle Uzak Masaüstü Protokolü (RDP) veya Secure Shell (SSH) için kullanılan kullanıcı hesaplarından farklıdır. 
>
> Linux sanal makine yapılandırmasını SSH aracılığıyla çalıştıran bir düğüme bağlanmak için bkz. [Azure 'Da LINUX VM 'ye uzak masaüstü kullanma](../virtual-machines/virtual-machines-linux-use-remote-desktop.md). Windows çalıştıran düğümlere RDP aracılığıyla bağlanmak için bkz. [Windows Server VM 'ye bağlanma](../virtual-machines/windows/connect-logon.md).<br /><br />
> RDP aracılığıyla bulut hizmeti yapılandırmasını çalıştıran bir düğüme bağlanmak için bkz. [Azure Cloud Services bir rol için Uzak Masaüstü bağlantısı etkinleştirme](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).
>
>


# <a name="run-tasks-under-user-accounts-in-batch"></a>Batch 'de Kullanıcı hesapları altında görevleri çalıştırma

Azure Batch bir görev her zaman bir kullanıcı hesabı altında çalışır. Varsayılan olarak, görevler yönetici izinleri olmadan standart Kullanıcı hesapları altında çalışır. Bu varsayılan kullanıcı hesabı ayarları genellikle yeterlidir. Ancak, belirli senaryolarda, bir görevin çalıştırılmasını istediğiniz kullanıcı hesabını yapılandırmak yararlı olur. Bu makalede Kullanıcı hesabı türleri ve bunları senaryonuz için nasıl yapılandırabileceğinizi ele alınmaktadır.

## <a name="types-of-user-accounts"></a>Kullanıcı hesabı türleri

Azure Batch, görevleri çalıştırmak için iki tür Kullanıcı hesabı sağlar:

- **Otomatik Kullanıcı hesapları.** Otomatik Kullanıcı hesapları, Batch hizmeti tarafından otomatik olarak oluşturulan yerleşik kullanıcı hesaplarıdır. Varsayılan olarak, görevler bir otomatik Kullanıcı hesabı altında çalışır. Bir görevin otomatik Kullanıcı belirtimini, bir görevin hangi otomatik Kullanıcı hesabı altında çalışacağını gösterecek şekilde yapılandırabilirsiniz. Otomatik Kullanıcı belirtimi, görevi çalıştıracak otomatik Kullanıcı hesabının yükseltme düzeyini ve kapsamını belirtmenize olanak tanır. 

- **Adlandırılmış bir kullanıcı hesabı.** Havuzu oluştururken bir havuz için bir veya daha fazla adlandırılmış Kullanıcı hesabı belirtebilirsiniz. Her Kullanıcı hesabı, havuzun her bir düğümünde oluşturulur. Hesap adının yanı sıra, Kullanıcı hesabı parolası, yükseltme düzeyi ve Linux havuzları için SSH özel anahtarı ' nı belirtirsiniz. Bir görev eklediğinizde, altında görevin çalıştırılacağı adlandırılmış Kullanıcı hesabını belirtebilirsiniz.

> [!IMPORTANT] 
> Batch hizmeti sürüm 2017 -01-01.4.0, bu sürümü çağırmak için kodunuzun güncelleştirilmesini gerektiren bir son değişiklik sunar. Toplu Işin eski bir sürümünden kod geçiriyorsanız, **Runyükseltilmiş** özelliğinin REST API veya Batch istemci kitaplıklarında artık desteklenmediğini unutmayın. Yükseltme düzeyini belirtmek için bir görevin yeni **UserIdentity** özelliğini kullanın. İstemci kitaplıklarından birini kullanıyorsanız Batch kodunuzu güncelleştirmek için hızlı yönergeler için [kodunuzu en son Batch istemci kitaplığına güncelleştirme](#update-your-code-to-the-latest-batch-client-library) başlıklı bölüme bakın.
>
>

## <a name="user-account-access-to-files-and-directories"></a>Dosya ve dizinlere Kullanıcı hesabı erişimi

Hem bir otomatik Kullanıcı hesabının hem de adlandırılmış bir kullanıcı hesabının, görevin çalışma dizinine, paylaşılan dizinine ve çok örnekli görevler dizinine okuma/yazma erişimi vardır. Her iki hesap türü de başlangıç ve iş hazırlama dizinlerine okuma erişimine sahiptir.

Bir görev, başlangıç görevi çalıştırmak için kullanılan hesap altında çalışıyorsa, görevin başlangıç görevi dizinine okuma yazma erişimi vardır. Benzer şekilde, bir görev iş hazırlama görevi çalıştırmak için kullanılan hesap altında çalışıyorsa, görevin iş hazırlama görev dizinine okuma-yazma erişimi vardır. Bir görev, başlangıç görevi veya iş hazırlama görevinden farklı bir hesap altında çalışıyorsa, görevin ilgili dizine yalnızca okuma erişimi vardır.

Bir görevden dosya ve dizinlere erişme hakkında daha fazla bilgi için bkz. [Batch ile büyük ölçekli paralel işlem çözümleri geliştirme](batch-api-basics.md#files-and-directories).

## <a name="elevated-access-for-tasks"></a>Görevler için yükseltilmiş erişim 

Kullanıcı hesabının yükseltme düzeyi, bir görevin yükseltilmiş erişimle çalıştırılıp çalıştırılmadığını gösterir. Hem bir otomatik Kullanıcı hesabı hem de adlandırılmış bir kullanıcı hesabı, yükseltilmiş erişimle çalıştırılabilir. Yükseltme düzeyi için iki seçenek şunlardır:

- **NonAdmin** Görev, yükseltilmiş erişim olmadan standart bir kullanıcı olarak çalışır. Batch Kullanıcı hesabı için varsayılan yükseltme düzeyi her zaman **yönetici olmayan**bir hesaptır.
- **Yönetici:** Görev, yükseltilmiş erişimi olan bir kullanıcı olarak çalışır ve tam yönetici izinleriyle çalışır. 

## <a name="auto-user-accounts"></a>Otomatik Kullanıcı hesapları

Varsayılan olarak, görevler bir otomatik Kullanıcı hesabı altında, yükseltilmiş erişimi olmayan standart bir kullanıcı olarak ve görev kapsamıyla toplu olarak çalışır. Otomatik Kullanıcı belirtimi görev kapsamı için yapılandırıldığında, Batch hizmeti yalnızca o görev için bir otomatik Kullanıcı hesabı oluşturur.

Görev kapsamının alternatifi havuz kapsamıdır. Bir görevin otomatik Kullanıcı belirtimi havuz kapsamı için yapılandırıldığında, görev, havuzdaki herhangi bir görevde kullanılabilen bir otomatik Kullanıcı hesabı altında çalışır. Havuz kapsamı hakkında daha fazla bilgi için, havuz kapsamına sahip bir görevi otomatik Kullanıcı olarak çalıştır başlıklı bölümüne bakın.   

Varsayılan kapsam Windows ve Linux düğümlerinde farklıdır:

- Windows düğümlerinde görevler, varsayılan olarak görev kapsamında çalışır.
- Linux düğümleri her zaman havuz kapsamı altında çalışır.

Her biri benzersiz bir otomatik Kullanıcı hesabına karşılık gelen otomatik Kullanıcı belirtimi için dört olası yapılandırma vardır:

- Görev kapsamıyla yönetici olmayan erişim (varsayılan Otomatik Kullanıcı belirtimi)
- Görev kapsamıyla yönetici (yükseltilmiş) erişim
- Havuz kapsamıyla yönetici olmayan erişim
- Havuz kapsamıyla yönetici erişimi

> [!IMPORTANT] 
> Görev kapsamında çalışan görevlerin, bir düğümdeki diğer görevlere erişimi de yoktur. Ancak, hesaba erişimi olan kötü niyetli bir Kullanıcı, yönetici ayrıcalıklarıyla çalışan ve diğer görev dizinlerine erişen bir görev göndererek bu kısıtlamaya geçici çözüm sağlayabilir. Kötü niyetli bir Kullanıcı, bir düğüme bağlanmak için RDP veya SSH de kullanabilir. Bu tür bir senaryoya engel olmak için Batch hesap Anahtarlarınıza erişimin korunması önemlidir. Hesabınızın tehlikede olduğunu düşünüyorsanız, anahtarlarınızı yeniden oluşturmayı unutmayın.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Bir görevi yükseltilmiş erişimle otomatik Kullanıcı olarak çalıştırma

Yükseltilmiş erişimle bir görevi çalıştırmanız gerektiğinde, yönetici ayrıcalıkları için otomatik Kullanıcı belirtimini yapılandırabilirsiniz. Örneğin, bir başlangıç görevinin, bu düğüme yazılım yüklemek için yükseltilmiş erişime ihtiyacı olabilir.

> [!NOTE] 
> Genel olarak, yalnızca gerektiğinde yükseltilmiş erişimi kullanmak en iyisidir. En iyi uygulamalar, istenen sonuca ulaşmak için gereken en düşük ayrıcalığa izin verilmesini önerir. Örneğin, bir başlangıç görevi, tüm kullanıcılar yerine geçerli kullanıcı için yazılım yüklerse, görevlere yükseltilmiş erişim vermekten kaçınabilirsiniz. Başlangıç görevi dahil olmak üzere aynı hesap altında çalışması gereken tüm görevler için havuz kapsamı ve yönetici olmayan erişim için otomatik Kullanıcı belirtimini yapılandırabilirsiniz. 
>
>

Aşağıdaki kod parçacıkları, otomatik Kullanıcı belirtiminin nasıl yapılandırılacağını gösterir. Örnekler, yükseltme düzeyini `Admin` ve kapsamını olarak `Task`ayarlar. Görev kapsamı varsayılan ayardır, ancak örnek için buraya eklenmiştir.

#### <a name="batch-net"></a>Batch .NET

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```
#### <a name="batch-java"></a>Batch Java

```java
taskToAdd.withId(taskId)
        .withUserIdentity(new UserIdentity()
            .withAutoUser(new AutoUserSpecification()
                .withElevationLevel(ElevationLevel.ADMIN))
                .withScope(AutoUserScope.TASK));
        .withCommandLine("cmd /c echo hello");                        
```

#### <a name="batch-python"></a>Batch Python

```python
user = batchmodels.UserIdentity(
    auto_user=batchmodels.AutoUserSpecification(
        elevation_level=batchmodels.ElevationLevel.admin,
        scope=batchmodels.AutoUserScope.task))
task = batchmodels.TaskAddParameter(
    id='task_1',
    command_line='cmd /c "echo hello world"',
    user_identity=user)
batch_client.task.add(job_id=jobid, task=task)
```

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Bir görevi havuz kapsamı ile otomatik Kullanıcı olarak çalıştırma

Bir düğüm sağlandığında, havuzdaki her düğümde, yükseltilmiş erişime ve bir yükseltilmiş erişime sahip olmayan iki havuz genelinde otomatik Kullanıcı hesabı oluşturulur. Belirli bir görevin otomatik Kullanıcı kapsamının havuz kapsamına ayarlanması, bu iki havuz genelinde otomatik kullanıcı hesabından biri altında görevi çalıştırır. 

Otomatik Kullanıcı için havuz kapsamı belirttiğinizde, yönetici erişimiyle çalışan tüm görevler, aynı havuz genelinde otomatik Kullanıcı hesabı altında çalışır. Benzer şekilde, yönetici izinleri olmadan çalışan görevler de tek bir havuz genelinde otomatik Kullanıcı hesabı altında çalışır. 

> [!NOTE] 
> İki havuz genelinde otomatik Kullanıcı hesabı ayrı hesaplardır. Havuz genelinde yönetim hesabı altında çalışan görevler, standart hesap altında çalışan görevlerle veri paylaşamaz ve tam tersi de geçerlidir. 
>
>

Aynı otomatik Kullanıcı hesabı altında çalıştırmanın avantajı, görevlerin aynı düğümde çalışan diğer görevlerle veri paylaşabilleridir.

Görevler arasında gizli dizileri paylaşma, iki havuz genelinde otomatik kullanıcı hesabından birini çalıştırmak yararlı olan bir senaryodur. Örneğin, bir başlangıç görevinin diğer görevlerin kullanabileceği düğüm üzerinde bir gizli anahtar sağlaması gerektiğini varsayalım. Windows Data Protection API 'yi (DPAPI) kullanabilirsiniz, ancak yönetici ayrıcalıkları gerektirir. Bunun yerine, parolayı Kullanıcı düzeyinde koruyabilirsiniz. Aynı kullanıcı hesabı altında çalışan görevler, yükseltilmiş erişim olmadan gizli dizi erişimine sahip olabilir.

Havuz kapsamı ile bir otomatik Kullanıcı hesabı altında Görevler çalıştırmak isteyebileceğiniz başka bir senaryo, Ileti geçirme arabirimi (MPı) dosya paylaşımıdır. MPI dosya paylaşma, MPı görevindeki düğümlerin aynı dosya verilerinde çalışması gerektiğinde faydalıdır. Baş düğüm, aynı otomatik Kullanıcı hesabı altında çalışıyorsa alt düğümlerin erişebileceği bir dosya paylaşma oluşturur. 

Aşağıdaki kod parçacığı, Batch .NET içindeki bir görevin havuz kapsamına otomatik kullanıcının kapsamını ayarlar. Yükseltme düzeyi atlanır, bu nedenle görev standart havuz genelinde otomatik Kullanıcı hesabı altında çalışır.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Adlandırılmış Kullanıcı hesapları

Bir havuz oluştururken adlandırılmış Kullanıcı hesapları tanımlayabilirsiniz. Adlandırılmış bir kullanıcı hesabının, sağladığınız adı ve parolası vardır. Adlandırılmış bir kullanıcı hesabı için yükseltme düzeyini belirtebilirsiniz. Linux düğümleri için bir SSH özel anahtarı da sağlayabilirsiniz.

Havuzdaki tüm düğümlerde adlandırılmış bir kullanıcı hesabı bulunur ve bu düğümlerde çalışan tüm görevler için kullanılabilir. Bir havuz için herhangi bir sayıda adlandırılmış Kullanıcı tanımlayabilirsiniz. Bir görev veya görev koleksiyonu eklediğinizde, görevin, havuzda tanımlanan adlandırılmış Kullanıcı hesaplarından biri altında çalıştığını belirtebilirsiniz.

Adlandırılmış bir kullanıcı hesabı, aynı kullanıcı hesabı altında bir işteki tüm görevleri çalıştırmak istediğinizde, ancak bunları aynı anda diğer işlerde çalıştıran görevlerden yalıtmak için yararlıdır. Örneğin, her iş için adlandırılmış bir kullanıcı oluşturabilir ve her bir işin görevlerini bu adlandırılmış Kullanıcı hesabı altında çalıştırabilirsiniz. Her iş daha sonra kendi görevleriyle bir gizli dizi paylaşabilir, ancak diğer işlerde çalışan görevlerle birlikte bu görevleri gerçekleştirebilir.

Ayrıca, dosya paylaşımları gibi dış kaynaklarda izinleri ayarlayan bir görevi çalıştırmak için adlandırılmış bir kullanıcı hesabı kullanabilirsiniz. Adlandırılmış bir kullanıcı hesabı ile Kullanıcı kimliğini kontrol edersiniz ve izinleri ayarlamak için bu kullanıcı kimliğini kullanabilirsiniz.  

Adlandırılmış Kullanıcı hesapları, Linux düğümleri arasında parola daha az SSH 'yi etkinleştirir. Çok örnekli görevleri çalıştırması gereken Linux düğümlerinde adlandırılmış bir kullanıcı hesabı kullanabilirsiniz. Havuzdaki her düğüm, tüm havuzda tanımlanan bir kullanıcı hesabı altında görevleri çalıştırabilir. Çok örnekli görevler hakkında daha fazla bilgi için bkz. [MPI\-uygulamalarını çalıştırmak için çok örnekli görevleri kullanma](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Adlandırılmış Kullanıcı hesapları oluşturma

Batch 'de adlandırılmış Kullanıcı hesapları oluşturmak için, havuza bir kullanıcı hesapları koleksiyonu ekleyin. Aşağıdaki kod parçacıkları, .NET, Java ve Python 'da adlandırılmış Kullanıcı hesaplarının nasıl oluşturulacağını göstermektedir. Bu kod parçacıkları, bir havuzda hem yönetici hem de yönetici olmayan olarak adlandırılmış hesapların nasıl oluşturulacağını gösterir. Örnekler, bulut hizmeti yapılandırmasını kullanarak havuzlar oluşturur, ancak sanal makine yapılandırmasını kullanarak bir Windows veya Linux havuzu oluştururken de aynı yaklaşımı kullanırsınız.

#### <a name="batch-net-example-windows"></a>Batch .NET örneği (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));   

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount("adminUser", "xyz123", ElevationLevel.Admin),
    new UserAccount("nonAdminUser", "123xyz", ElevationLevel.NonAdmin),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-net-example-linux"></a>Batch .NET örneği (Linux)

```csharp
CloudPool pool = null;

// Obtain a collection of all available node agent SKUs.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. 
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the virtual machine configuration to use to create the pool.
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

Console.WriteLine("Creating pool [{0}]...", poolId);

// Create the unbound pool.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,                                             
    virtualMachineSize: "Standard_A1",                                      
    virtualMachineConfiguration: virtualMachineConfiguration);                  

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount(
        name: "adminUser",
        password: "xyz123",
        elevationLevel: ElevationLevel.Admin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 12345,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
    new UserAccount(
        name: "nonAdminUser",
        password: "123xyz",
        elevationLevel: ElevationLevel.NonAdmin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 45678,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
};

// Commit the pool.
await pool.CommitAsync();
```


#### <a name="batch-java-example"></a>Batch Java örneği

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withCloudServiceConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Batch Python örneği

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.nonadmin)
]
pool = batchmodels.PoolAddParameter(
    id=pool_id,
    user_accounts=users,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        node_agent_sku_id=sku_to_use),
    vm_size=vm_size,
    target_dedicated=vm_count)
batch_client.pool.add(pool)
```

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Bir görevi, yükseltilmiş erişimle adlandırılmış bir kullanıcı hesabı altında çalıştırın

Bir görevi yükseltilmiş bir kullanıcı olarak çalıştırmak için görevin **UserIdentity** özelliğini, ' nin **yükseltme düzeyi** özelliği olarak ayarlanmış şekilde `Admin`oluşturulmuş bir adlandırılmış Kullanıcı hesabı olarak ayarlayın.

Bu kod parçacığı, görevin adlandırılmış bir kullanıcı hesabı altında çalışması gerektiğini belirtir. Havuz oluşturulduğu sırada bu adlandırılmış Kullanıcı hesabı havuzda tanımlanmıştır. Bu durumda, adlandırılmış Kullanıcı hesabı yönetici izinleriyle oluşturulmuştur:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Kodunuzu en son Batch istemci kitaplığı 'nda güncelleştirme

Batch hizmeti sürüm 2017 -01-01.4.0, daha önceki sürümlerde bulunan **Runayrýcalproperty** özelliğini **UserIdentity** özelliği ile değiştirerek bir son değişiklik sunar. Aşağıdaki tablolar, kodunuzu istemci kitaplıklarının önceki sürümlerinden güncelleştirmek için kullanabileceğiniz basit bir eşleme sağlar.

### <a name="batch-net"></a>Batch .NET

| Kodunuz kullanıyorsa...                  | Güncelleştirme....                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated`belirtilmemiş | Güncelleştirme gerekmiyor                                                                                               |

### <a name="batch-java"></a>Batch Java

| Kodunuz kullanıyorsa...                      | Güncelleştirme....                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated`belirtilmemiş | Güncelleştirme gerekmiyor                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Kodunuz kullanıyorsa...                      | Güncelleştirme....                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, burada <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`, burada <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.nonadmin))`             |
| `run_elevated`belirtilmemiş | Güncelleştirme gerekmiyor                                                                                                                                  |


## <a name="next-steps"></a>Sonraki adımlar

* Toplu Işe yönelik ayrıntılı genel bakış için bkz. [Batch ile büyük ölçekli paralel işlem çözümleri geliştirme](batch-api-basics.md).
