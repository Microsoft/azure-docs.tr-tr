---
title: Azure Kaynak taşıyıcısı 'nde PowerShell 'i kullanarak bölgeler arasında kaynakları taşıma
description: Azure Kaynak taşıyıcısı 'nde PowerShell kullanarak kaynakları bölgeler arasında taşımayı öğrenin.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/21/2021
ms.author: raynew
ms.openlocfilehash: b728170521570ff0d83b67671109e82adb7fa7b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584423"
---
# <a name="move-resources-across-regions-in-powershell"></a>PowerShell 'deki bölgeler arasında kaynakları taşıma

Bu makalede, Azure [kaynak taşıyıcısı](overview.md)PowerShell kullanılarak Azure kaynaklarını farklı bir Azure bölgesine nasıl taşıyacağınız açıklanır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Önkoşulları ve gereksinimleri denetleyin.
> * Taşıma koleksiyonunu ayarlayın.
> * Taşıma koleksiyonuna kaynak ekleyin ve bağımlılıkları çözün.
> * Kaynak kaynak grubunu hazırlayın ve taşıyın. 
> * Diğer kaynakları hazırlayın ve taşıyın.
> * Taşımayı atmak mı yoksa kaydetmek mi istediğinize karar verin. 
> * Taşıma işleminden sonra kaynak bölgedeki kaynakları isteğe bağlı olarak kaldırın.

> [!NOTE]
> Öğreticiler, bir senaryoyu denemek için en hızlı yolu gösterir ve varsayılan seçenekleri kullanır. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun. Ardından [Azure Portal](https://portal.azure.com)oturum açın.

## <a name="prerequisites"></a>Önkoşullar
**Gereksinim** | **Açıklama**
--- | ---
**Abonelik izinleri** | Taşımak istediğiniz kaynakları içeren abonelikte *sahip* erişiminizin olduğunu denetleyin<br/><br/> **Neden sahip erişimine ihtiyacım var?** Azure aboneliğindeki belirli bir kaynak ve hedef çifti için ilk kez kaynak eklediğinizde, kaynak taşıyıcısı abonelik tarafından güvenilen [sistem tarafından atanan bir yönetilen kimlik](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (eski adıyla yönetilen hizmet tanımlaması (MSI)) oluşturur. Kimliği oluşturmak ve gerekli rolü (katkıda bulunan ya da kaynak abonelikte Kullanıcı erişimi Yöneticisi) atamak için, kaynak eklemek için kullandığınız hesabın abonelikte *sahip* izinleri olması gerekir. Azure rolleri hakkında [daha fazla bilgi edinin](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) .
**Kaynak taşıyıcısı desteği** | Desteklenen bölgeleri ve diğer yaygın soruları [gözden geçirin](common-questions.md) .
**VM desteği** |  Taşımak istediğiniz tüm VM 'Lerin desteklendiğinden emin olun.<br/><br/> - Desteklenen Windows VM 'lerini [doğrulayın](support-matrix-move-region-azure-vm.md#windows-vm-support) .<br/><br/> - Desteklenen Linux VM 'lerini ve çekirdek sürümlerini [doğrulayın](support-matrix-move-region-azure-vm.md#linux-vm-support) .<br/><br/> -Desteklenen [işlem](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [depolama](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)ve [ağ](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) ayarlarını kontrol edin.
**SQL desteği** | SQL kaynaklarını taşımak istiyorsanız, [SQL gereksinimleri listesini](tutorial-move-region-sql.md#check-sql-requirements)gözden geçirin.
**Hedef abonelik** | Hedef bölgedeki aboneliğin, hedef bölgede taşıdığınız kaynakları oluşturmak için yeterli kotası olması gerekir. Kota içermiyorsa [ek sınırlamalar isteyin](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Hedef bölge ücretleri** | VM 'Leri taşıdığınız hedef Bölgeyle ilişkili fiyatlandırmayı ve ücretleri doğrulayın. Size yardımcı olması için [fiyatlandırma hesaplayıcısını](https://azure.microsoft.com/pricing/calculator/) kullanın.

### <a name="review-powershell-requirements"></a>PowerShell gereksinimlerini gözden geçirme

Çoğu kaynak taşıma işlemi, Azure portal veya PowerShell 'i kullanarak, birkaç özel durum ile aynıdır.

**İşlem** | **PowerShell** | **Portal**
--- | --- | ---
**Taşıma koleksiyonu oluşturma** | Bir taşıma koleksiyonu (taşıdığınız tüm kaynakların listesi) otomatik olarak oluşturulur. Gerekli kimlik izinleri, portal tarafından arka uca atanır. | PowerShell cmdlet 'lerini şu şekilde kullanabilirsiniz:<br/><br/> -Taşıma koleksiyonu için bir kaynak grubu oluşturun ve bunun konumunu belirtin.<br/><br/> -Koleksiyona yönetilen bir kimlik atayın.<br/><br/> -Koleksiyona kaynak ekleyin.
**Taşıma koleksiyonunu kaldırma** | Portalda bir taşıma koleksiyonunu doğrudan kaldıramazsınız. | Bir taşıma koleksiyonunu kaldırmak için PowerShell cmdlet 'ini kullanırsınız.
**Kaynak taşıma işlemleri**<br/><br/> (Hazırlama, başlatma taşıma, tamamlama vb.).| Kaynak taşıyıcısı tarafından otomatik doğrulama ile tek adımlar. | PowerShell cmdlet 'leri:<br/><br/> 1) bağımlılıkları doğrulayın.<br/><br/> 2) taşıma işlemini gerçekleştirin.
**Kaynak kaynaklarını silme** | Doğrudan kaynak taşıyıcısı portalında. | Kaynak türü düzeyinde PowerShell cmdlet 'leri.


### <a name="sample-values"></a>Örnek değerler

Bu değerleri betiğimizde kullanıyoruz:

**Ayar** | **Değer** 
--- | ---
Abonelik Kimliği | abonelik kimliği
Kaynak bölge |  Central US
Hedef bölge | Orta Batı ABD
Kaynak grubu (taşıma koleksiyonu meta verilerini tutan) | RG-MoveCollection-demoRMS
Koleksiyon adını taşı | PS-merkezde US-westcentralus-demoRMS
Kaynak grubu (kaynak bölgesi) | Psdemorz 
Kaynak grubu (hedef bölge) | Psdemord-hedef
Kaynak taşıma hizmeti konumu | Doğu ABD 2
IdentityType | SystemAssigned
Taşınacak VM | PSDemoVM


## <a name="sign-into-azure"></a>Azure oturumu açma

Connect-AzAccount cmdlet 'i ile Azure aboneliğinizde oturum açın:

```azurepowershell-interactive
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="set-up-the-move-collection"></a>Taşıma koleksiyonunu ayarlama

MoveCollection nesnesi, taşımak istediğiniz kaynaklarla ilgili meta verileri ve yapılandırma bilgilerini depolar. Bir taşıma koleksiyonu ayarlamak için şunları yapın:

- Taşıma koleksiyonu için bir kaynak grubu oluşturun.
- Hizmet sağlayıcısını aboneliğe kaydedin, böylece MoveCollection kaynağı oluşturulabilir.
- Yönetilen kimliğe sahip MoveCollection nesnesini oluşturun. MoveCollection nesnesinin, kaynak taşıyıcısı hizmetinin bulunduğu aboneliğe erişmesi için, aboneliğin güvendiği [sistem tarafından atanan yönetilen kimliğe](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (eski adıyla YÖNETILEN HIZMET KIMLIĞI (MSI)) ihtiyacı vardır.
- Yönetilen kimlik için kaynak taşıyıcısı aboneliğine erişim izni verin.

### <a name="create-the-resource-group"></a>Kaynak grubunu oluşturma

Taşıma koleksiyonu meta verileri ve yapılandırma bilgileri için aşağıdaki gibi bir kaynak grubu oluşturun:

```azurepowershell-interactive
New-AzResourceGroup -Name "RG-MoveCollection-demoRMS" -Location "East US 2"
```
**Çıkış**:

![Kaynak Grubu oluşturulduktan sonra çıkış metni](./media/tutorial-move-region-powershell/create-metadata-resource-group.png)

### <a name="register-the-resource-provider"></a>Kaynak sağlayıcısını kaydetme

1. MoveCollection kaynağının oluşturulabilmesi için aşağıdaki gibi Microsoft. Migrate kaynak sağlayıcısını kaydedin:

    ```azurepowershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate
    
2. Wait for registration:

    ```azurepowershell-interactive 
    While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
    {
        Start-Sleep -Seconds 5
        Write-Output "Waiting for registration to complete."
    }
    ```
### <a name="create-a-movecollection-object"></a>MoveCollection nesnesi oluşturma

Bir MoveCollection nesnesi oluşturun ve buna aşağıdaki şekilde bir yönetilen kimlik atayın: 

```azurepowershell-interactive
New-AzResourceMoverMoveCollection -Name "PS-centralus-westcentralus-demoRMS"  -ResourceGroupName "RG-MoveCollection-demoRMS" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location "centraluseuap" -IdentityType "SystemAssigned"
```

**Çıkış**:

![Taşıma koleksiyonu oluşturduktan sonra çıkış metni](./media/tutorial-move-region-powershell/output-move-collection.png)


### <a name="grant-access-to-the-managed-identity"></a>Yönetilen kimliğe erişim izni verme

Aşağıdaki şekilde, kaynak taşıyıcısı aboneliğine yönetilen kimlik erişimi verin. Abonelik sahibi olmanız gerekir.

1. MoveCollection nesnesinden kimlik ayrıntılarını alın.

    ```azurepowershell-interactive
    $moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RG-MoveCollection-demoRMS" -Name "PS-centralus-westcentralus-demoRMS"

    $identityPrincipalId = $moveCollection.IdentityPrincipalId   
    ``` 

2. Azure Kaynak taşıyıcısı, kaynakları taşımaya yardımcı olması için aboneliğinize erişebilmek üzere gerekli rolleri kimliğe atayın.

    ```azurepowershell-interactive
    New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

    New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"
    ``` 

## <a name="add-resources-to-the-move-collection"></a>Taşıma koleksiyonuna kaynak ekleme

Taşımak istediğiniz mevcut kaynak kaynakların kimliklerini alın. Hedef kaynak ayarları nesnesini oluşturun ve ardından taşıma koleksiyonuna kaynakları ekleyin.

> [!NOTE]
> Bir taşıma koleksiyonuna eklenen kaynakların aynı abonelikte olması gerekir, ancak farklı kaynak gruplarında yer alabilir.

Kaynakları şu şekilde ekleyin:

1. Kaynak kaynak KIMLIĞINI al:

    ```azurepowershell-interactive
    Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
    ```

    **Çıktı**

    ![Kaynak KIMLIĞI alındıktan sonra çıkış metni](./media/tutorial-move-region-powershell/output-retrieve-resource.png)

2. Hedef kaynak ayarları nesnesini, taşıdığınız kaynağa uygun olarak oluşturun. Bu durumda bir VM olur.

    ```azurepowershell-interactive
    $targetResourceSettingsObj = New-Object Microsoft.Azure.PowerShell.Cmdlets.ResourceMover.Models.Api202101.VirtualMachineResourceSettings
    ```

3. Nesnenin kaynak türünü ve hedef kaynak adını ayarlayın.

    ```azurepowershell-interactive
    $targetResourceSettingsObj.ResourceType = "Microsoft.Compute/virtualMachines"
    $targetResourceSettingsObj.TargetResourceName = "PSDemoVM"
    ```
    > [!NOTE]
    > Hedef VM 'imiz, kaynak bölgedeki VM ile aynı ada sahip. Farklı bir ad seçebilirsiniz.

4. Kaynak kaynaklarını taşıma koleksiyonuna, aldığınız/oluşturduğunuz kaynak KIMLIĞI ve hedef ayarları nesnesini kullanarak ekleyin.

    ```azurepowershell-interactive
    Add-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx xxxxxxxxxxxx/resourceGroups/
    PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM" -Name "PSDemoVM" -ResourceSetting $targetResourceSettingsObj
    ```

    **Çıkış** ![ Kaynağı ekledikten sonra çıkış metni](./media/tutorial-move-region-powershell/output-add-resource.png)

## <a name="validate-and-add-dependencies"></a>Bağımlılıkları doğrulama ve ekleme

Eklediğiniz kaynakların diğer kaynaklarla ilgili bağımlılıklara sahip olup olmadığını denetleyin ve gerektiğinde ekleyin. 

1. Bağımlılıkları aşağıdaki gibi doğrulayın:

    ```azurepowershell-interactive
    Resolve-AzResourceMoverMoveCollectionDependency -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```
    **Çıkış (bağımlılıklar varsa)**

    ![Bağımlılıklar doğrulandıktan sonra çıkış metni](./media/tutorial-move-region-powershell/dependency-output.png)

2. Kimlik eksik bağımlılıkları:

    - Eksik tüm bağımlılıkların bir listesini almak için:

        ```azurepowershell-interactive
        Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Descendant"
        ```
        **Çıkış** ![ Tüm bağımlılıkların listesini aldıktan sonra çıkış metni](./media/tutorial-move-region-powershell/dependencies-list.png)  

    - Yalnızca ilk düzey bağımlılıkları almak için (kaynak için doğrudan bağımlılıklar):

        ```azurepowershell-interactive
        Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Direct
        ```
        **Çıkış** ![ Birinci düzey bağımlılıkların bir listesini aldıktan sonra çıkış metni](./media/tutorial-move-region-powershell/dependencies-list-direct.png)  

3. Bekleyen eksik bağımlılıkları eklemek için, [taşıma koleksiyonuna kaynak eklemek](#add-resources-to-the-move-collection)için yukarıdaki yönergeleri tekrarlayın ve bekleyen kaynak bulunmadığından yeniden doğrulayın.

> [!NOTE]
> Kaynak koleksiyonundan kaynakları kaldırmak istediğiniz nedenlerden dolayı, [Bu makaledeki](remove-move-resources.md)yönergeleri izleyin.

## <a name="add-the-source-resource-group"></a>Kaynak kaynak grubunu ekleme

Taşıma koleksiyonuna taşımak istediğiniz kaynakları içeren kaynak kaynak grubunu ekleyin.

1. Kaynak grubunun KIMLIĞINI alın.

    ```azurepowershell-interactive
    Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Direct
    ```
    **Çıkış** ![ Kaynak kaynak grubunun KIMLIĞI alındıktan sonra çıkış metni](./media/tutorial-move-region-powershell/source-resource-group-id.png)  

    > [!NOTE]
    > Hedef bölgede zaten olan bir kaynak grubu kullanıyoruz.

 
2. Kaynak grubunu koleksiyona eklemek için alınan KIMLIĞI kullanın.

    ```azurepowershell-interactive
    Add-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS"  -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/psdemorm"  -Name "psdemorm"  -ExistingTargetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/PSDemoRM-target"
    ```
    **Çıkış** ![ Kaynak kaynak grubu taşıma koleksiyonuna eklendikten sonra çıkış metni](./media/tutorial-move-region-powershell/add-source-resource-group.png)

3. Kaynak grubunu ekledikten sonra hiçbir şey kaçırmadığınızdan emin olmak için bağımlılıkları doğrulayın.

    ```azurepowershell-interactive
    Resolve-AzResourceMoverMoveCollectionDependency -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```
4. Bekleyen bağımlılıklar olmadığını görüyoruz.

    **Çıkış** ![ Bağımlılıkları denetledikten sonra çıkış metni](./media/tutorial-move-region-powershell/all-dependencies-added.png)


## <a name="prepare-resources"></a>Kaynakları hazırlama

Genellikle kaynak bölgedeki taşımadan önce kaynakları hazırlamanız gerekir. Örnek:

- Azure sanal ağları, ağ bağdaştırıcıları, yük dengeleyiciler ve ağ güvenlik grupları gibi durum bilgisi olmayan kaynakları taşımak için bir Azure Resource Manager şablonu dışarı aktarmanız gerekebilir.
- Azure VM 'Leri ve SQL veritabanları gibi durum bilgisi olan kaynakları taşımak için kaynaktan hedef bölgeye kaynak çoğaltmayı başlatmanız gerekebilir.

Bu öğreticide, VM 'Leri taşırken, VM 'Leri hazırlamaya başlayabilmemiz için kaynak kaynak grubunu hazırlamanız ve ardından taşıma işlemini başlatıp yürütmemiz gerekir.

> [!NOTE]
> Mevcut bir hedef kaynak grubunuz varsa, kaynak kaynak grubu için taşımayı doğrudan kaydedebilir ve taşıma ve başlatma aşamalarını atlayabilirsiniz.

  
### <a name="prepare-the-source-resource-group"></a>Kaynak kaynak grubunu hazırlama:

1. Kaynak grubunu hazırlama:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    **Çıktı**

    ![Kaynak kaynak grubu hazırlandıktan sonra çıkış metni](./media/tutorial-move-region-powershell/prepare-source-resource-group.png)

2. Kaynak kaynak grubunun taşınmasını başlatın.

    ```azurepowershell-interactive
    "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    ![Kaynak kaynak grubu taşıma başlatıldıktan sonra çıkış metni](./media/tutorial-move-region-powershell/initiate-move-source-resource-group.png)

3. Kaynak kaynak grubu için taşımayı yürütün.

    ```azurepowershell-interactive
    Invoke-AzResourceMoverCommit -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    **Çıktı**

    ![Kaynak kaynak grubu kaydedildikten sonra çıkış metni](./media/tutorial-move-region-powershell/commit-move-source-resource-group.png)


### <a name="prepare-vm-resources"></a>VM kaynaklarını hazırlama

Kaynak kaynak grubunu hazırladıktan ve taşıdıktan sonra taşıma için VM kaynakları hazırlayabiliriz.

1. VM kaynaklarını hazırlanmadan önce bağımlılıkları doğrulayın.

    ```azurepowershell-interactive
    $resp = Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm') -ValidateOnly
    ```
    **Çıktı**

    ![MAKINEYI hazırlamadan önce doğruladıktan sonra çıkış metni](./media/tutorial-move-region-powershell/validate-vm-before-move.png)

2. VM ile birlikte hazırlanması gereken bağımlı kaynakları alın.

    ```azurepowershell-interactive
    $resp.AdditionalInfo[0].InfoMoveResource
    ```
    **Çıktı**

    ![Bağımlı VM kaynakları alındıktan sonra çıkış metni](./media/tutorial-move-region-powershell/get-resources-before-prepare.png)

3. Tüm bağımlı kaynaklar için hazırlama işlemini başlatın.

    ```azurepowershell-interactive
    Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('PSDemoVM','psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg')
    ```
    **Çıktı**

    ![Tüm kaynakların hazırlanması yapıldıktan sonra çıkış metni](./media/tutorial-move-region-powershell/initiate-prepare-all.png)


    > [!NOTE]
    > Kaynak adı yerine hazırlama cmdlet 'inin giriş parametreleri olarak kaynak adı yerine, taşıma ve yürütmeyi Başlat cmdlet 'lerinde de kaynak kaynağı KIMLIĞI sağlayabilirsiniz. Bunu yapmak için şunu çalıştırın:


    ```azurepowershell-interactive
        Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/PSDemoRMS/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
    ```

## <a name="initiate-move-of-vm-resources"></a>VM kaynaklarını taşımayı başlatma

1. VM kaynaklarının bir *başlatma taşıma beklemede* durumunda olduğunu doğrulayın:

    ```azurepowershell-interactive
    Get-AzResourceMoverMoveResource  -SubscriptionId “ xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx “ -ResourceGroupName “RG-MoveCollection-demoRMS” -MoveCollectionName “PS-centralus-westcentralus-demoRMS ”   | Where-Object {  $_.MoveStatusMoveState -eq “InitiateMovePending” } | Select Name
    ```    

    **Çıktı**

    ![Başlatma durumunu denetledikten sonra çıkış metni](./media/tutorial-move-region-powershell/verify-initiate-move-pending.png)

2. Taşımayı başlatın:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverInitiateMove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
    ```    

    **Çıktı**

    ![Kaynak taşımayı başlattıktan sonra çıkış metni](./media/tutorial-move-region-powershell/initiate-resources-move.png)


## <a name="discard-or-commit"></a>Atın veya teslim edilsin mi?

İlk taşıma işleminden sonra, taşımayı yürütmek mi yoksa atmak mi istediğinize karar verebilirsiniz. 

- **At**: test ediyorsanız bir taşıma atabilir ve kaynak kaynağı gerçekten taşımak istemezsiniz. Taşımayı atmak, kaynağı *başlatma bekleyen* bir durumuna döndürür. Ardından, gerekirse taşımayı yeniden başlatabilirsiniz.
- **COMMIT**: COMMIT, hedef bölgeye taşıma işlemini tamamlar. İşlem tamamlandıktan sonra kaynak kaynak, *bekleyen silme kaynağı* durumunda olur ve silmek istediğinize karar verebilirsiniz.

### <a name="discard-the-move"></a>Taşımayı at

Taşımayı atmak için:

```azurepowershell-interactive
Invoke-AzResourceMoverDiscard -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
```
**Çıktı**

![Taşıma iptal edildikten sonra çıkış metni](./media/tutorial-move-region-powershell/discard-move.png)


### <a name="commit-the-move"></a>Taşımayı Yürüt

1. Taşımayı aşağıdaki gibi yürütün:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverCommit -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
    ```
    **Çıktı**

    ![Taşıma işleminden sonra çıkış metni](./media/tutorial-move-region-powershell/commit-move.png)

2. Tüm kaynakların hedef bölgeye taşındığını doğrulayın:

    ```azurepowershell-interactive
    Get-AzResourceMoverMoveResource  -ResourceGroupName “RG-MoveCollection-demoRMS ” -MoveCollectionName “PS-centralus-westcentralus-demoRMS”   
    ```
    Tüm kaynaklar artık hedef bölgedeki bir *silme kaynağı bekliyor* durumundadır.

## <a name="delete-source-resources"></a>Kaynak kaynaklarını silme

Taşıma işleminden sonra ve kaynakların hedef bölgede beklendiği gibi çalıştığını doğruladıktan sonra, PowerShell veya [Azure CLI](../azure-resource-manager/management/manage-resources-cli.md#delete-resources) [kullanarak](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources) [Azure Portal](../azure-resource-manager/management/manage-resources-portal.md#delete-resources)her kaynak kaynağını silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Azure VM 'lerini PowerShell kullanarak başka bir Azure bölgesine taşıdı.
> * VM 'lerle ilişkili kaynakları başka bir bölgeye taşıdı.

Şimdi, portalı kullanarak Azure VM 'lerini taşımaya çalışırken

> [!div class="nextstepaction"]
> [Portalda Azure VM 'lerini taşıma](./tutorial-move-region-virtual-machines.md)


