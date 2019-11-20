---
title: PowerShell 'i kullanmaya başlama-Azure Batch | Microsoft Docs
description: Batch kaynaklarını yönetmek için kullanabileceğiniz Azure PowerShell cmdlet'lerine hızlı bir giriş.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 01/15/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 21930d5240225540159fa425d9d9fa518a1b19d5
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323071"
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Batch kaynaklarını PowerShell cmdlet'leriyle yönetme

Azure Batch PowerShell cmdlet’leri ile Batch API'leri, Azure portalı ve Azure Komut Satırı Arabirimi (CLI) ile gerçekleştirdiğiniz Batch aynı görevlerin çoğunu gerçekleştirebilir ve betik oluşturabilirsiniz. Bu bilgiler, Batch hesabınızı yönetmek, havuzlar, işler ve görevler gibi Batch kaynaklarınızla da çalışmak için kullanabildiğiniz cmdlet’lere hızlı bir giriş yapmanızı sağlar.

Tam Batch cmdlet’leri listesi ve ayrıntılı cmdlet sözdizimi için bkz. [Azure Batch cmdlet başvurusu](/powershell/module/az.batch).

Bu makale, az Batch Module 1.0.0 içindeki cmdlet 'lere dayalıdır. Hizmet güncelleştirmeleri ve geliştirmeleri avantajlarından yararlanmak için Azure PowerShell modüllerinizi sık sık güncelleştirin.

## <a name="prerequisites"></a>Önkoşullar

* [Azure PowerShell modülünü yükleyin ve yapılandırın](/powershell/azure/overview). Yayın öncesi modül gibi belirli bir Azure Batch modülünü yüklemek için [PowerShell Galerisi](https://www.powershellgallery.com/packages/Az.Batch/1.0.0)'ne bakın.

* Aboneliğinize bağlanmak için **Connect-AzAccount** cmdlet 'ini çalıştırın (Azure Batch cmdlet 'leri Azure Resource Manager modülünde):

  ```powershell
  Connect-AzAccount
  ```

* **Batch sağlayıcı ad alanı ile kaydolun**. Bu işlemin **her abonelik için yalnızca bir kez** gerçekleştirilmesi gerekir.
  
  ```powershell
  Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
  ```

## <a name="manage-batch-accounts-and-keys"></a>Batch hesaplarını ve anahtarlarını yönetme

### <a name="create-a-batch-account"></a>Batch hesabı oluşturma

**New-AzBatchAccount** , belirtilen kaynak grubunda bir Batch hesabı oluşturur. Henüz bir kaynak grubunuz yoksa, [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 'ini çalıştırarak bir tane oluşturun. **Location** parametresinde, "Orta ABD" gibi Azure bölgelerinden birini belirtin. Örneğin:

```powershell
New-AzResourceGroup –Name MyBatchResourceGroup –Location "Central US"
```

Ardından, kaynak grubunda bir Batch hesabı oluşturun. <*Account_name*> hesap için bir ad ve kaynak grubunuzun konumunu ve adını belirtin. Batch hesabının oluşturulması biraz zaman alabilir. Örneğin:

```powershell
New-AzBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>
```

> [!NOTE]
> Batch hesabı adı, kaynak grubunun Azure bölgesinde benzersiz olmalıdır; 3 - 24 arası karakter olmalı, yalnızca küçük harf ve rakam içermelidir.

### <a name="get-account-access-keys"></a>Hesap erişim anahtarı alma

**Get-AzBatchAccountKeys** , bir Azure Batch hesabıyla ilişkili erişim anahtarlarını gösterir. Örneğin, oluşturduğunuz birincil ve ikincil anahtarları almak için aşağıdakini çalıştırın.

 ```powershell
$Account = Get-AzBatchAccountKeys –AccountName <account_name>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### <a name="generate-a-new-access-key"></a>Yeni erişim anahtarı oluşturma

**New-AzBatchAccountKey** bir Azure Batch hesabı için yeni bir birincil veya ikincil hesap anahtarı oluşturur. Örneğin, Batch hesabınıza yeni bir birincil anahtar oluşturmak için şunu yazın:

```powershell
New-AzBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [!NOTE]
> Yeni bir ikincil anahtar oluştururken **KeyType** parametresi için "Secondary" seçeneğini belirtin. Birincil ve ikincil anahtarları ayrı olarak yeniden oluşturmalısınız.

### <a name="delete-a-batch-account"></a>Batch hesabını silme

**Remove-AzBatchAccount** bir Batch hesabını siler. Örneğin:

```powershell
Remove-AzBatchAccount -AccountName <account_name>
```

İstendiğinde, hesabı kaldırmak istediğinizi onaylayın. Hesap kaldırma işleminin tamamlanması biraz zaman alabilir.

## <a name="create-a-batchaccountcontext-object"></a>BatchAccountContext nesnesi oluşturma

Batch kaynaklarını yönetmek için kimlik doğrulamasını paylaşılan anahtar kimlik doğrulamasını veya Azure Active Directory kimlik doğrulamasını kullanarak gerçekleştirebilirsiniz. Batch PowerShell cmdlet'lerini kullanarak kimlik doğrulaması gerçekleştirmek için öncelikle hesabınızın kimlik bilgilerini veya kimliğini depolamak üzere bir BatchAccountContext oluşturun. BatchAccountContext nesnesini **BatchContext** parametresini kullanan cmdlet’lere geçirirsiniz.

### <a name="shared-key-authentication"></a>Paylaşılan anahtar kimlik doğrulaması

```powershell
$context = Get-AzBatchAccountKeys -AccountName <account_name>
```

> [!NOTE]
> Varsayılan olarak, hesabın birincil anahtarı kimlik doğrulaması amacıyla kullanılsa da, BatchAccountContext nesnenizin **KeyInUse** özelliğini değiştirerek kullanılacak anahtarı açıkça seçebilirsiniz: `$context.KeyInUse = "Secondary"`.

### <a name="azure-active-directory-authentication"></a>Azure Active Directory kimlik doğrulaması

```powershell
$context = Get-AzBatchAccount -AccountName <account_name>
```

## <a name="create-and-modify-batch-resources"></a>Batch kaynaklarını oluşturma ve değiştirme

Batch hesabı altında kaynak oluşturmak için **New-AzBatchPool**, **New-azbatchjob**ve **New-azbatchtask** gibi cmdlet 'leri kullanın. Batch hesabı altında var olan kaynakların özelliklerini güncelleştirmek için **Get-** ve **Set-** cmdlet’leri, kaldırmak için de **Remove-** cmdlet’leri vardır.

Bu cmdlet’lerinin birçoğunu kullanırken bir BatchContext nesnesi geçirmeye ek olarak aşağıdaki örnekte gösterildiği gibi ayrıntılı kaynak ayarlarını içeren nesneleri oluşturmanız ya da geçirmeniz gerekir. Diğer örnekler için her bir cmdlet’e ilişkin ayrıntılı yardıma bakın.

### <a name="create-a-batch-pool"></a>Batch havuzu oluşturma

Bir Batch havuzu oluştururken ya da güncelleştirirken, işlem düğümlerindeki işletim sistemine yönelik bir bulut hizmetleri yapılandırmasını veya sanal makine yapılandırmasını seçin (bkz. [Batch özelliğine genel bakış](batch-api-basics.md#pool)). Bulut hizmetleri yapılandırmasını belirtirseniz işlem düğümleriniz [Azure konuk işletim sistemi sürümlerinden](../cloud-services/cloud-services-guestos-update-matrix.md#releases) biriyle görüntülenir. Sanal makine yapılandırmasını belirtirseniz, [Azure sanal makineler marketi][vm_marketplace]'nde listelenen desteklenen Linux veya Windows VM görüntülerinden birini belirtebilir ya da hazırladığınız özel bir görüntü sağlayabilirsiniz.

**New-AzBatchPool**komutunu çalıştırdığınızda, işletim sistemi ayarlarını bir Pschoparlör ServiceConfiguration veya PSVirtualMachineConfiguration nesnesine geçirin. Örneğin, aşağıdaki kod parçacığı, sanal makine yapılandırmasında boyut Standard_A1 işlem düğümlerine sahip bir Batch havuzu oluşturur. Ubuntu Server 18,04-LTS ile yansıma. Burada **VirtualMachineConfiguration** parametresi *$configuration* değişkenini PSVirtualMachineConfiguration nesnesi olarak belirtir. **BatchContext** parametresi önceden tanımlanmış *$context* değişkenini BatchAccountContext nesnesi olarak belirtir.

```powershell
$imageRef = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("UbuntuServer","Canonical","18.04-LTS")

$configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageRef, "batch.node.ubuntu 18.04")

New-AzBatchPool -Id "mypspool" -VirtualMachineSize "Standard_a1" -VirtualMachineConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context
```

Yeni havuzdaki işlem düğümlerinin hedef sayısı bir otomatik ölçeklendirme formülüyle hesaplanır. Bu durumda, havuzdaki işlem düğümü sayısının en çok 4 olduğunu belirten basit bir **$TargetDedicated=4** formülüdür.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Havuzlar, işler, görevler ve diğer ayrıntılar için sorgulama

Batch hesabı altında oluşturulan varlıkları sorgulamak için **Get-AzBatchPool**, **Get-azbatchjob**ve **Get-azbatchtask** gibi cmdlet 'leri kullanın.

### <a name="query-for-data"></a>Verileri sorgulama

Örnek olarak, havuzlarınızı bulmak için **Get-AzBatchPools** ' ı kullanın. Varsayılan olarak, hesabınız altındaki tüm havuzlarla ilgili bu sorgular zaten *$context* değişkenindeki BatchAccountContext nesnesinde depolanır:

```powershell
Get-AzBatchPool -BatchContext $context
```

### <a name="use-an-odata-filter"></a>OData filtresini kullanma

Yalnızca ilgilendiğiniz nesneleri bulmak için **Filtre** parametresini kullanan bir OData filtresi sağlayabilirsiniz. Örneğin, kimlikleri "myPool" ile başlayan tüm havuzları bulabilirsiniz:

```powershell
$filter = "startswith(id,'myPool')"

Get-AzBatchPool -Filter $filter -BatchContext $context
```

Bu yöntem, yerel bir işlem hattında "Where-Object" kullanmak kadar esnek değildir. Ancak, sorgu Batch hizmetine doğrudan gönderilir; böylece, İnternet bant genişliği korunarak filtre işleminin tümü sunucu tarafında gerçekleşir.

### <a name="use-the-id-parameter"></a>Kimlik parametresini kullanma

OData filtresinin bir alternatifi de **Kimlik** parametresi kullanmaktır. "myPool" Kimliğine sahip belirli bir havuzu sorgulamak için:

```powershell
Get-AzBatchPool -Id "myPool" -BatchContext $context
```

**ID** parametresi yalnızca tam kimlik aramasını destekler; joker karakterler veya OData stili filtreler değildir.

### <a name="use-the-maxcount-parameter"></a>MaxCount parametresini kullanma

Varsayılan olarak, her cmdlet en çok 1000 nesne döndürür. Bu sınıra ulaştıysanız, daha az nesne döndürmek için filtreyi daraltın veya **MaxCount** parametresini kullanarak kesin bir üst sınır ayarlayın. Örneğin:

```powershell
Get-AzBatchTask -MaxCount 2500 -BatchContext $context
```

Üst sınırı kaldırmak için **MaxCount**’u 0 veya daha az bir değere ayarlayın.

### <a name="use-the-powershell-pipeline"></a>PowerShell işlem hattını kullanma

Batch cmdlet 'leri, cmdlet 'ler arasında veri göndermek için PowerShell işlem hattını kullanır. İşlem hattı, parametre belirtmekle aynı etkiye sahip olsa da birden çok varlıkla çalışmayı kolaylaştırır.

Örneğin, hesabınızın altındaki tüm görevleri bulup görüntüleyin:

```powershell
Get-AzBatchJob -BatchContext $context | Get-AzBatchTask -BatchContext $context
```

Havuzdaki her işlem düğümünü yeniden başlatın:

```powershell
Get-AzBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

## <a name="application-package-management"></a>Uygulama paketi yönetimi

Uygulama paketleri havuzlarınızdaki işlem düğümlerine uygulama dağıtmanın basit bir yolunu sağlar. Batch PowerShell cmdlet'leriyle, Batch hesabınızdaki uygulama paketlerini yükleyebilir, yönetebilir ve paket sürümlerini işlem düğümlerine dağıtabilirsiniz.

Bir uygulama **oluşturun**:

```powershell
New-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

Bir uygulama paketi **ekleyin**:

```powershell
New-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip
```

Uygulamanın **varsayılan sürümünü** ayarlayın:

```powershell
Set-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"
```

Uygulamanın paketlerini **listeleme**

```powershell
$application = Get-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

$application.ApplicationPackages
```

Uygulama paketini **silme**

```powershell
Remove-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"
```

Uygulamayı **silme**

```powershell
Remove-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

> [!NOTE]
> Uygulamayı silmeden önce, uygulamanın tüm uygulama paketi sürümlerini silmeniz gerekir. Şu anda uygulama paketleri olan bir uygulamayı silmeye çalışırsanız bir 'Çakışma' hatası alırsınız.

### <a name="deploy-an-application-package"></a>Uygulama paketi dağıtma

Bir havuz oluşturduğunuzda dağıtım için bir veya daha fazla uygulama paketi belirtebilirsiniz. Havuz oluşturma saatinde bir paket belirttiğinizde düğüm havuza katıldıkça her bir düğüme dağıtılır. Paketler ayrıca bir düğüm yeniden başlatıldığında veya yeniden görüntüsü oluşturulduğunda dağıtılır.

Bir uygulama paketini havuza katıldıklarında havuzun düğümlerine dağıtmak üzere havuz oluştururken `-ApplicationPackageReference` seçeneğini belirtin. İlk olarak, bir **PSApplicationPackageReference** nesnesi oluşturun ve bunu havuzun işlem düğümlerine dağıtmak ISTEDIĞINIZ uygulama kimliği ve paket sürümü ile yapılandırın:

```powershell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "1.0"
```

Şimdi havuzu oluşturun ve paket başvuru nesnesini, `ApplicationPackageReferences` seçeneğinin bağımsız değişkeni olarak belirtin:

```powershell
New-AzBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference
```

[Batch uygulama paketleriyle işlem düğümlerine uygulama dağıtımı](batch-application-packages.md) konusunda, uygulama paketlerine ilişkin daha fazla bilgi bulabilirsiniz.

> [!IMPORTANT]
> Uygulama paketlerini kullanmak için bir Azure Depolama hesabını Batch hesabınıza bağlamanız gerekir.

### <a name="update-a-pools-application-packages"></a>Bir havuzun uygulama paketlerini güncelleştirme

Mevcut bir havuza atanan uygulamaları güncelleştirmek için önce istenen özelliklerle bir PSApplicationPackageReference nesnesi oluşturun (uygulama KIMLIĞI ve paket sürümü):

```powershell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "2.0"

```

Ardından, Batch’den havuzu alın, mevcut paketleri temizleyin, yeni paket başvurumuzu ekleyin ve Batch hizmetini yeni havuz ayarlarıyla güncelleştirin:

```powershell
$pool = Get-AzBatchPool -BatchContext $context -Id "PoolWithAppPackage"

$pool.ApplicationPackageReferences.Clear()

$pool.ApplicationPackageReferences.Add($appPackageReference)

Set-AzBatchPool -BatchContext $context -Pool $pool
```

Batch hizmetinde havuzun özelliklerini güncelleştirmiş oldunuz. Bununla birlikte, yeni uygulama paketini havuzdaki işlem düğümlerine gerçekten dağıtmak için bu düğümleri yeniden başlatmanız ya da görüntülerini yeniden oluşturmanız gerekir. Bu komutla havuzdaki her düğümü yeniden başlatabilirsiniz:

```powershell
Get-AzBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

> [!TIP]
> Havuzdaki işlem düğümlerine birden fazla uygulama paketini dağıtabilirsiniz. Hâlihazırda dağıtılmış paketleri değiştirmek yerine uygulama paketi *eklemek* isterseniz yukarıdaki `$pool.ApplicationPackageReferences.Clear()` satırını atlayın.

## <a name="next-steps"></a>Sonraki adımlar

* Ayrıntılı cmdlet sözdizimi ve örnekleri için bkz. [Azure Batch cmdlet başvurusu](/powershell/module/az.batch).
* Batch’deki uygulamalar ve uygulama paketleri hakkında daha fazla bilgi için bkz. [Batch uygulama paketleriyle işlem düğümlerine uygulama dağıtımı](batch-application-packages.md).

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
