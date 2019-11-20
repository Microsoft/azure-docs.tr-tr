---
title: Azure depolama ile Azure PowerShell kullanma | Microsoft Docs
description: Azure depolama için Azure PowerShell cmdlet 'lerinin nasıl kullanılacağını öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/16/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 7064496b89143f467ea63fe38233724a7b0af96d
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74131026"
---
# <a name="using-azure-powershell-with-azure-storage"></a>Azure PowerShell'i Azure Depolama ile kullanma

Azure PowerShell, PowerShell komut satırından veya betiklerden Azure kaynakları oluşturmak ve yönetmek için kullanılır. Azure depolama için, bu cmdlet 'ler iki kategoriye ayrılır--denetim düzlemi ve veri düzlemi. Denetim düzlemi cmdlet 'leri depolama hesabını yönetmek için kullanılır; depolama hesapları oluşturmak, özellikleri ayarlamak, depolama hesaplarını silmek, erişim anahtarlarını döndürmek vb. Veri düzlemi cmdlet *'leri, depolama hesabında depolanan verileri* yönetmek için kullanılır. Örneğin, Blobları karşıya yükleme, dosya paylaşımları oluşturma ve bir kuyruğa ileti ekleme.

Bu nasıl yapılır makalesi, depolama hesaplarını yönetmek için yönetim düzlemi cmdlet 'lerini kullanan genel işlemleri içerir. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Depolama hesaplarını listeleme
> * Mevcut depolama hesabına yönelik bir başvuru alın
> * Depolama hesabı oluşturma
> * Depolama hesabı özelliklerini ayarla
> * Erişim anahtarlarını alın ve yeniden oluşturun
> * Depolama hesabınıza erişimi koruma
> * Depolama Analizi etkinleştir

Bu makalede, depolama için Depolama Analizi etkinleştirme ve erişme, veri düzlemi cmdlet 'lerini kullanma ve Çin bulutu, Almanya bulutu ve kamu gibi Azure bağımsız bulutlarına erişme gibi çeşitli diğer PowerShell makalelerinin bağlantıları sunulmaktadır Una.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Bu alıştırma, Azure PowerShell modülünü az sürüm 0,7 veya üstünü gerektirir. Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure PowerShell Modülü yükleme](/powershell/azure/install-Az-ps).

Bu alıştırmada, komutları normal bir PowerShell penceresine yazabilir veya [Windows PowerShell Tümleşik komut dosyası ortamı 'nı (ıSE)](/powershell/scripting/components/ise/exploring-the-windows-powershell-ise) kullanabilir ve komutları bir düzenleyiciye yazarak bir veya daha fazla komutu bir düzenleyicide test edebilirsiniz. Yürütmek istediğiniz satırları vurgulayabilir ve yalnızca bu komutları çalıştırmak için Seçileni Çalıştır ' a tıklayabilirsiniz.

Depolama hesapları hakkında daha fazla bilgi için bkz. [depolamaya giriş](storage-introduction.md) ve [Azure depolama hesapları hakkında](storage-create-storage-account.md).

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

`Connect-AzAccount` komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin.

```powershell
Connect-AzAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Abonelikteki depolama hesaplarını listeleme

Geçerli abonelikteki depolama hesaplarının listesini almak için [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) cmdlet 'ini çalıştırın.

```powershell
Get-AzStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Depolama hesabına yönelik bir başvuru alın

Ardından, bir depolama hesabına yönelik bir başvuruya ihtiyacınız vardır. Yeni bir depolama hesabı oluşturabilir ya da var olan bir depolama hesabına başvuru alabilirsiniz. Aşağıdaki bölümde her iki yöntem de gösterilmektedir.

### <a name="use-an-existing-storage-account"></a>Var olan bir depolama hesabını kullan

Mevcut bir depolama hesabını almak için, kaynak grubunun adı ve depolama hesabının adı gerekir. Bu iki alanın değişkenlerini ayarlayın ve ardından [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) cmdlet 'ini kullanın.

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName
```

Artık, var olan bir depolama hesabına işaret eden $storageAccount sahipsiniz.

### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Aşağıdaki betik, [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount)kullanılarak genel amaçlı depolama hesabı oluşturmayı gösterir. Hesabı oluşturduktan sonra, her çağrıyla kimlik doğrulama belirtmek yerine sonraki komutlarda kullanılabilecek bağlamını alın.

```powershell
# Get list of locations and select one.
Get-AzLocation | select Location
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzResourceGroup -Name $resourceGroup -Location $location

# Set the name of the storage account and the SKU name.
$storageAccountName = "testpshstorage"
$skuName = "Standard_LRS"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context.
$ctx = $storageAccount.Context
```

Betik aşağıdaki PowerShell cmdlet 'lerini kullanır:

*   [Get-AzLocation](/powershell/module/az.resources/get-azlocation) --geçerli konumların bir listesini alır. Örnek, konum için `eastus` kullanır.

*   [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) --yeni bir kaynak grubu oluşturur. Kaynak grubu, Azure kaynaklarınızın dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Bizde `teststoragerg`olarak adlandırılır.

*   [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) --depolama hesabı oluşturur. Örnek `testpshstorage`kullanır.

SKU adı, depolama hesabı için LRS (yerel olarak yedekli depolama) gibi çoğaltma türünü gösterir. Çoğaltma hakkında daha fazla bilgi için bkz. [Azure Storage çoğaltma](storage-redundancy.md).

> [!IMPORTANT]
> Depolama hesabınızın adı Azure içinde benzersiz olmalıdır ve küçük harfle yazılmalıdır. Adlandırma kuralları ve kısıtlamaları için bkz. [kapsayıcıları, Blobları ve meta verileri adlandırma ve başvuru](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
>

Artık yeni bir depolama hesabınız ve buna başvurunuz var.

## <a name="manage-the-storage-account"></a>Depolama hesabını yönetme

Artık yeni bir depolama hesabına veya var olan bir depolama hesabına başvurunuz olduğuna göre, aşağıdaki bölümde depolama hesabınızı yönetmek için kullanabileceğiniz komutların bazıları gösterilmektedir.

### <a name="storage-account-properties"></a>Depolama hesabı özellikleri

Bir depolama hesabının ayarlarını değiştirmek için [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)komutunu kullanın. Bir depolama hesabının veya içinde bulunduğu kaynak grubunun konumunu değiştiremiyoruz, diğer özelliklerin birçoğunu değiştirebilirsiniz. Aşağıda, PowerShell kullanarak değiştirebileceğiniz özelliklerden bazıları listelenmiştir.

* Depolama hesabına atanan **özel etki alanı** .

* Depolama hesabına atanan **Etiketler** . Etiketler, genellikle kaynakları Faturalama amacıyla kategorize etmek için kullanılır.

* **SKU** , depolama hesabı Için yerel olarak yedekli depolama için LRS gibi çoğaltma ayarıdır. Örneğin, standart\_LRS 'den standart\_GRS veya standart\_RAMPALARıNDAN geçiş yapabilirsiniz. Standart\_ZRS, standart\_GZRS, standart\_RAGZRS veya Premium\_LRS 'yi diğer SKU 'Lara değiştiremediğini veya diğer SKU 'Ları bu şekilde değiştirebileceğinizi unutmayın.

* BLOB depolama hesapları için **erişim katmanı** . Erişim katmanının değeri **sık** **erişimli veya seyrek erişimli olarak ayarlanır**ve depolama hesabını nasıl kullandığınız ile hizalanan erişim katmanını seçerek maliyetinizi en aza indirmenize olanak tanır. Daha fazla bilgi için bkz. sık erişimli, seyrek erişimli [ve arşiv depolama katmanları](../blobs/storage-blob-storage-tiers.md).

* Yalnızca HTTPS trafiğine izin verir.

### <a name="manage-the-access-keys"></a>Erişim anahtarlarını yönetme

Bir Azure depolama hesabı iki hesap anahtarı ile gelir. Anahtarları almak için [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey)komutunu kullanın. Bu örnek ilk anahtarı alır. Diğerini almak için `Value[0]`yerine `Value[1]` kullanın.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Anahtarı yeniden oluşturmak için [New-AzStorageAccountKey](/powershell/module/az.Storage/New-azStorageAccountKey)komutunu kullanın.

```powershell
New-AzStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1
```

Diğer anahtarı yeniden oluşturmak için `key1`yerine anahtar adı olarak `key2` kullanın.

Anahtarlarınızın birini yeniden oluşturun ve ardından yeni değeri görmek için geri alın.

> [!NOTE]
> Bir üretim depolama hesabı için anahtarı yeniden oluşturmadan önce dikkatli bir planlama gerçekleştirmeniz gerekir. Bir veya her iki anahtarın yeniden oluşturulması, yeniden oluşturulan anahtarı kullanarak herhangi bir uygulamanın erişimini geçersiz kılacaktır. Daha fazla bilgi için bkz. [Erişim anahtarları](storage-account-manage.md#access-keys)


### <a name="delete-a-storage-account"></a>Bir depolama hesabını silme

Bir depolama hesabını silmek için [Remove-AzStorageAccount](/powershell/module/az.storage/Remove-azStorageAccount)komutunu kullanın.

```powershell
Remove-AzStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Bir depolama hesabını sildiğinizde, hesapta depolanan varlıkların hepsi de silinir. Yanlışlıkla bir hesabı silerseniz, desteği hemen çağırın ve depolama hesabını geri yüklemek için bir bilet açın. Verilerinizin kurtarılması garanti edilmez, ancak bazen çalışır. Destek bileti çözümlenene kadar eskisiyle aynı ada sahip yeni bir depolama hesabı oluşturmayın.
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>VNET ve güvenlik duvarları kullanarak depolama hesabınızı koruma

Varsayılan olarak, tüm depolama hesaplarına internet erişimi olan herhangi bir ağ tarafından erişilebilir. Ancak, ağ kurallarını yalnızca belirli sanal ağlardan gelen uygulamaların bir depolama hesabına erişmesine izin verecek şekilde yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure Storage güvenlik duvarlarını ve sanal ağları yapılandırma](storage-network-security.md).

Bu makalede, aşağıdaki PowerShell cmdlet 'lerini kullanarak bu ayarların nasıl yönetileceği gösterilmektedir:
* [Add-AzStorageAccountNetworkRule](/powershell/module/az.Storage/Add-azStorageAccountNetworkRule)
* [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)
* [Remove-AzStorageAccountNetworkRule](https://docs.microsoft.com/powershell/module/az.storage/remove-azstorageaccountnetworkrule)

## <a name="use-storage-analytics"></a>Depolama analizlerini kullanma

[Azure depolama Analizi](storage-analytics.md) , [Depolama Analizi ölçümleri](/rest/api/storageservices/about-storage-analytics-metrics) ve [depolama Analizi günlüğe kaydetme](/rest/api/storageservices/about-storage-analytics-logging)içerir.

**Depolama Analizi ölçümleri** , Azure depolama hesaplarınız için bir depolama hesabının sistem durumunu izlemek için kullanabileceğiniz ölçümleri toplamak üzere kullanılır. Ölçümler; Bloblar, dosyalar, tablolar ve kuyruklar için etkinleştirilebilir.

**Depolama Analizi günlük** sunucu tarafında gerçekleşir ve depolama hesabınıza hem başarılı hem de başarısız isteklerin ayrıntılarını kaydetmenize olanak sağlar. Bu Günlükler, tablolarınıza, kuyruklara ve bloblarınıza karşı okuma, yazma ve silme işlemlerinin ayrıntılarını ve başarısız isteklerin nedenlerini görmenizi sağlar. Azure dosyaları için günlüğe kaydetme kullanılamıyor.

İzlemeyi [Azure Portal](https://portal.azure.com), PowerShell veya depolama istemci kitaplığını kullanarak programlı bir şekilde yapılandırabilirsiniz.

> [!NOTE]
> PowerShell kullanarak dakika analizlerini etkinleştirebilirsiniz. Bu özellik portalda kullanılamıyor.
>

* PowerShell kullanarak depolama ölçümleri verilerini etkinleştirme ve görüntüleme hakkında bilgi edinmek için bkz. [Storage Analytics ölçümleri](storage-analytics-metrics.md).

* PowerShell kullanarak depolama günlüğü verilerinin nasıl etkinleştirileceğini ve alınacağını öğrenmek için bkz. [PowerShell kullanarak depolama günlüğünü etkinleştirme](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data) ve [depolama günlüğü günlük verilerinizi bulma](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data).

* Depolama sorunlarını gidermek için depolama ölçümlerini ve depolama günlüğünü kullanma hakkında ayrıntılı bilgi için, bkz. [izleme, tanılama ve sorun giderme Microsoft Azure depolama](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>Depolama hesabındaki verileri yönetme

Artık depolama hesabınızı PowerShell ile yönetmeyi anladığınıza göre, depolama hesabındaki veri nesnelerine nasıl erişebileceğinizi öğrenmek için aşağıdaki makaleleri kullanabilirsiniz.

* [Blob 'ları PowerShell ile yönetme](../blobs/storage-how-to-use-blobs-powershell.md)
* [PowerShell ile dosyaları yönetme](../files/storage-how-to-use-files-powershell.md)
* [Kuyrukları PowerShell ile yönetme](../queues/storage-powershell-how-to-use-queues.md)
* [PowerShell ile Azure Tablo depolama işlemlerini gerçekleştirme](../../storage/tables/table-storage-how-to-use-powershell.md)

Azure Cosmos DB Tablo API'si, tablo depolaması için, anahtar alma küresel dağıtım, düşük gecikme süreli okuma ve yazma işlemleri, otomatik ikincil dizin oluşturma ve adanmış aktarım hızı gibi Premium özellikler sağlar.

* Daha fazla bilgi için bkz. [Azure Cosmos DB tablo API'si](../../cosmos-db/table-introduction.md).

## <a name="independent-cloud-deployments-of-azure"></a>Azure 'un bağımsız bulut dağıtımları

Çoğu kişi küresel Azure dağıtımı için Azure genel bulutu kullanır. Ayrıca, bağımsız Microsoft Azure bağımsız dağıtımları da vardır. Bu bağımsız dağıtımlar "ortamlar" olarak adlandırılır. Bunlar kullanılabilir ortamlardır:

* [Azure Kamu Bulutu](https://azure.microsoft.com/features/gov/)
* [Çin 'de 21Vianet tarafından işletilen Azure Çin 21Vianet bulutu](http://www.windowsazure.cn/)
* [Azure Almanya bulutu](../../germany/germany-welcome.md)

Bu bulutlara ve bunların depolamasına PowerShell 'e erişme hakkında daha fazla bilgi için lütfen bkz. [PowerShell kullanarak Azure bağımsız bulutlarında depolamayı yönetme](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu alıştırma için yeni bir kaynak grubu ve bir depolama hesabı oluşturduysanız, kaynak grubunu kaldırarak oluşturduğunuz tüm varlıkları kaldırabilirler. Bu, ayrıca grubun içerdiği tüm kaynakları da siler. Bu durumda, oluşturulan depolama hesabı ve kaynak grubunun kendisi kaldırılır.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Sonraki adımlar

Bu nasıl yapılır makalesi, depolama hesaplarını yönetmek için yönetim düzlemi cmdlet 'lerini kullanan genel işlemleri içerir. Şunları öğrendiniz:

> [!div class="checklist"]
> * Depolama hesaplarını listeleme
> * Mevcut depolama hesabına yönelik bir başvuru alın
> * Depolama hesabı oluşturma
> * Depolama hesabı özelliklerini ayarla
> * Erişim anahtarlarını alın ve yeniden oluşturun
> * Depolama hesabınıza erişimi koruma
> * Depolama Analizi etkinleştir

Bu makalede ayrıca veri nesnelerinin nasıl yönetileceği, Depolama Analizi nasıl etkinleştirileceği ve Çin bulutu, Almanya bulutu ve kamu bulutu gibi Azure bağımsız bulutlarına nasıl erişebileceğiniz gibi diğer birçok makaleye başvurular de sağlanmaktadır. Başvuruya yönelik daha fazla ilgili makale ve kaynak aşağıda verilmiştir:

* [Azure depolama denetim düzlemi PowerShell cmdlet 'leri](/powershell/module/az.storage/)
* [Azure depolama veri düzlemi PowerShell cmdlet 'leri](/powershell/module/azure.storage/)
* [Windows PowerShell Başvurusu](/powershell/scripting/overview)
