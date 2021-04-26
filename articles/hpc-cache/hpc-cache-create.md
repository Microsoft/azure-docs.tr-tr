---
title: Azure HPC önbelleği oluşturma
description: Azure HPC önbellek örneği oluşturma
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 02934a1943ef37d282dd2a2e7862c5695bbd6ecb
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862714"
---
# <a name="create-an-azure-hpc-cache"></a>Azure HPC önbelleği oluşturma

Önbelleğinizi oluşturmak için Azure portal veya Azure CLı 'yi kullanın.

![En altta oluştur düğmesi ile Azure portal önbellekte genel bakış ekran görüntüsü](media/hpc-cache-home-page.png)

Önbellek oluşturma ve depolama hedefi ekleme hakkında bir [video tanıtımı](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) izlemek için aşağıdaki görüntüye tıklayın.

[![video küçük resmi: Azure HPC Cache: Setup (video sayfasını ziyaret etmek için tıklayın)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="define-basic-details"></a>Temel ayrıntıları tanımla

![Azure portal içindeki proje ayrıntıları sayfasının ekran görüntüsü](media/hpc-cache-create-basics.png)

**Proje ayrıntıları**' nda, önbelleği barındıracak aboneliği ve kaynak grubunu seçin.

**Hizmet Ayrıntıları**' nda, önbellek adını ve bu diğer öznitelikleri ayarlayın:

* Konum- [desteklenen bölgelerden](hpc-cache-overview.md#region-availability)birini seçin.
* Sanal ağ-mevcut bir tane seçebilirsiniz veya yeni bir sanal ağ oluşturabilirsiniz.
* Alt ağ-en az 64 IP adresi olan bir alt ağ seçin veya oluşturun (/24). Bu alt ağ yalnızca bu Azure HPC önbellek örneği için kullanılmalıdır.

## <a name="set-cache-capacity"></a>Önbellek kapasitesini ayarla
<!-- referenced from GUI - update aka.ms link if you change this header text -->

**Önbellek** sayfasında önbelleğinizin kapasitesini ayarlamanız gerekir. Burada ayarlanan değerler, önbelleğinizin ne kadar veri tutabildiğini ve ne kadar hızlı istemci isteklerine hizmet kullanabileceğini belirlemektir.

Kapasite ayrıca önbelleğin maliyetini de etkiler.

Şu iki değeri belirleyerek kapasiteyi seçin:

* Önbellek (aktarım hızı) için GB/saniye cinsinden maksimum veri aktarımı oranı
* Önbelleğe alınmış veriler için ayrılan depolama miktarı (TB)

Kullanılabilir aktarım hızı değerlerinden birini ve önbellek depolama boyutunu seçin.

Gerçek veri aktarımı hızının iş yüküne, ağ hızına ve depolama hedeflerinin türüne bağlı olduğunu unutmayın. Seçtiğiniz değerler tüm önbellek sistemi için en fazla aktarım hızını ayarlar, ancak bazıları ek yük görevleri için kullanılır. Örneğin, bir istemci önbellekte zaten depolanmayan bir dosya isterse veya dosya eski olarak işaretlenmişse önbelleğiniz, arka uç depolamadan getirmek için aktarım hızını kullanır.

Azure HPC Cache, önbellek isabet oranlarını en üst düzeye çıkarmak için hangi dosyaların önbelleğe alınacağını ve ön yüklenmiş olduğunu yönetir Önbellek içerikleri sürekli değerlendirilir ve dosyalar daha az sıklıkta erişildiğinde uzun süreli depolamaya taşınır. Etkin çalışma dosyaları kümesini rahatça tutabilecek bir önbellek depolama boyutu, ayrıca meta veriler ve diğer ek yük için ek alan seçin.

![önbellek boyutlandırma sayfasının ekran görüntüsü](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Azure Key Vault şifrelemeyi etkinleştir (isteğe bağlı)

**Önbellek** ve **Etiketler** sekmeleri arasında **disk şifreleme anahtarları** sayfası görüntülenir.<!-- Read [Regional availability](hpc-cache-overview.md#region-availability) to learn more about region support. -->

Önbellek depolama alanı için kullanılan şifreleme anahtarlarını yönetmek istiyorsanız **disk şifreleme anahtarları** sayfasında Azure Key Vault bilgilerinizi sağlayın. Anahtar Kasası, önbellek ile aynı bölgede ve aynı abonelikte olmalıdır.

Müşteri tarafından yönetilen anahtarlar gerekmiyorsa, bu bölümü atlayabilirsiniz. Azure, verileri varsayılan olarak Microsoft tarafından yönetilen anahtarlarla şifreler. Daha fazla bilgi edinmek için [Azure depolama şifrelemesini](../storage/common/storage-service-encryption.md) okuyun.

> [!NOTE]
>
> * Önbelleği oluşturduktan sonra Microsoft tarafından yönetilen anahtarlar ve müşteri tarafından yönetilen anahtarlar arasında geçiş yapılamaz.
> * Önbellek oluşturulduktan sonra anahtar kasasına erişmek için yetkilendirmelisiniz. Şifrelemeyi açmak için önbelleğin **genel bakış** sayfasındaki **şifrelemeyi etkinleştir** düğmesine tıklayın. Bu adımı, önbelleğin oluşturulması 90 dakika içinde gerçekleştirin.
> * Önbellek diskleri bu yetkilendirmeden sonra oluşturulur. Bu, ilk önbellek oluşturma zamanının kısa olduğu, ancak erişim yetkilendirdikten sonra önbelleğin on dakika veya daha fazlası için kullanılmak üzere hazırlanmayacağı anlamına gelir.

Müşteri tarafından yönetilen anahtar şifreleme süreci hakkında tam bir açıklama için, [Azure HPC Cache için müşteri tarafından yönetilen şifreleme anahtarlarını kullanın](customer-keys.md)makalesini okuyun.

!["müşteri tarafından yönetilen" seçiliyken ve Anahtar Kasası alanlarının gösterildiği şifreleme anahtarları sayfasının ekran görüntüsü](media/create-encryption.png)

Müşteri tarafından yönetilen anahtar şifrelemesini seçmek için **Müşteri** tarafından yönetilen ' ı seçin. Anahtar Kasası belirtim alanları görüntülenir. Kullanılacak Azure Key Vault seçin, ardından bu önbellekte kullanılacak anahtarı ve sürümü seçin. Anahtar, 2048 bitlik bir RSA anahtarı olmalıdır. Bu sayfadan yeni bir Anahtar Kasası, anahtar veya anahtar sürümü oluşturabilirsiniz.

Önbelleği oluşturduktan sonra Anahtar Kasası hizmetini kullanmak için onu yetkilendirmelisiniz. Ayrıntılar için [önbellekten Azure Key Vault şifreleme kimliğini](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) okuyun.

## <a name="add-resource-tags-optional"></a>Kaynak etiketleri ekleme (isteğe bağlı)

**Etiketler** sayfası, Azure HPC Cache örneğinizi [kaynak etiketleri](../azure-resource-manager/management/tag-resources.md) eklemenize olanak tanır.

## <a name="finish-creating-the-cache"></a>Önbellek oluşturmayı tamamlama

Yeni önbelleği yapılandırdıktan sonra, **gözden geçir + oluştur** sekmesine tıklayın. Portal seçimlerinizi doğrular ve seçimlerinizi incelemenizi sağlar. Her şey doğruysa, **Oluştur**' a tıklayın.

Önbellek oluşturma yaklaşık 10 dakika sürer. İlerlemeyi Azure portal Bildirimler panelinde izleyebilirsiniz.

!["dağıtım çalışma" ve "Bildirimler" sayfalarındaki önbellek oluşturma ekranının ekran görüntüsü](media/hpc-cache-deploy-status.png)

Oluşturma tamamlandığında, yeni Azure HPC önbellek örneğinin bağlantısını içeren bir bildirim görünür ve önbellek, aboneliğinizin **kaynaklar** listesinde görünür.

![Azure portal 'de Azure HPC önbellek örneğinin ekran görüntüsü](media/hpc-cache-new-overview.png)

> [!NOTE]
> Önbelleğiniz müşteri tarafından yönetilen şifreleme anahtarlarını kullanıyorsa, dağıtım durumu tamamlanmadan önce önbellek kaynaklar listesinde görünebilir. Önbelleğin durumu **anahtar beklerken** , anahtar kasasını kullanmak için [bunu yetkilendirebilirsiniz](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) .

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="create-the-cache-with-azure-cli"></a>Azure CLı ile önbellek oluşturma

[Azure HPC önbelleği Için Azure CLI 'Yi ayarlayın](./az-cli-prerequisites.md).

> [!NOTE]
> Azure CLı Şu anda müşteri tarafından yönetilen şifreleme anahtarlarıyla bir önbellek oluşturmayı desteklemiyor. Azure portalını kullanın.

Yeni bir Azure HPC önbelleği oluşturmak için [az HPC-Cache Create](/cli/azure/hpc-cache#az_hpc_cache_create) komutunu kullanın.

Bu değerleri sağlayın:

* Önbellek kaynak grubu adı
* Önbellek adı
* Azure bölgesi
* Önbellek alt ağı şu biçimde:

  ``--subnet "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"``

  Önbellek alt ağının en az 64 IP adresi (/24) gerekir ve diğer kaynakları da barındırabilir.

* Önbellek kapasitesi. İki değer, Azure HPC önbelleğinizin en yüksek aktarım hızını ayarlar:

  * Önbellek boyutu (GB olarak)
  * Önbellek altyapısında kullanılan sanal makinelerin SKU 'SU

  [az HPC-Cache SKU listesi](/cli/azure/hpc-cache/skus) , kullanılabilir SKU 'ları ve her biri için geçerli önbellek boyutu seçeneklerini gösterir. Önbellek boyutu seçenekleri 3 TB ile 48 TB arasındadır, ancak yalnızca bazı değerler desteklenir.

  Bu grafik, bu belgenin hazırlanması sırasında hangi önbellek boyutunun ve SKU birleşimlerinin geçerli olduğunu gösterir (2020 Temmuz).

  | Önbellek boyutu | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | evet         | hayır          | hayır          |
  | 6144 GB    | evet         | evet         | hayır          |
  | 12288 GB   | evet         | evet         | evet         |
  | 24576 GB   | hayır          | evet         | evet         |
  | 49152 GB   | hayır          | hayır          | evet         |

  Fiyatlandırma, aktarım hızı ve önbelleğinizi iş akışınız için uygun şekilde boyutlandırma hakkında önemli bilgiler için Portal yönergeleri sekmesindeki **önbellek kapasitesini ayarla** bölümünü okuyun.

Önbellek oluşturma örneği:

```azurecli
az hpc-cache create --resource-group doc-demo-rg --name my-cache-0619 \
    --location "eastus" --cache-size-gb "3072" \
    --subnet "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default" \
    --sku-name "Standard_2G"
```

Önbellek oluşturma birkaç dakika sürer. Başarılı olduğunda Create komutu aşağıdakine benzer bir çıktı döndürür:

```azurecli
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },
  "id": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619",
  "location": "eastus",
  "mountAddresses": [
    "10.3.0.17",
    "10.3.0.18",
    "10.3.0.19"
  ],
  "name": "my-cache-0619",
  "provisioningState": "Succeeded",
  "resourceGroup": "doc-demo-rg",
  "sku": {
    "name": "Standard_2G"
  },
  "subnet": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default",
  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.42",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-04-01T15:19:54.068299+00:00",
    "pendingFirmwareVersion": null
  }
}
```

İleti, şu öğeler dahil bazı yararlı bilgiler içerir:

* İstemci bağlama adresleri-istemcileri önbelleğe bağlamaya hazırsanız bu IP adreslerini kullanın. Daha fazla bilgi edinmek için [Azure HPC önbelleğini bağlama](hpc-cache-mount.md) makalesini okuyun.
* Yükseltme durumu-bir yazılım güncelleştirmesi yayınlandığında, bu ileti değişecektir. [Önbellek yazılımını](hpc-cache-manage.md#upgrade-cache-software) uygun bir zamanda el ile yükseltebilirsiniz veya birkaç gün sonra otomatik olarak uygulanır.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

> [!CAUTION]
> Az. HPCCache PowerShell modülü şu anda genel önizlemededir. Bu önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır. Üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmeyebilir veya kısıtlı özelliklere sahip olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Gereksinimler

PowerShell 'i yerel olarak kullanmayı seçerseniz, bu makale az PowerShell modülünü yüklemenizi ve [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'Ini kullanarak Azure hesabınıza bağlanmanızı gerektirir. Az PowerShell modülünü yükleme hakkında daha fazla bilgi için bkz. [yükleme Azure PowerShell](/powershell/azure/install-az-ps). Cloud Shell kullanmayı seçerseniz, daha fazla bilgi için bkz. [Azure Cloud Shell Genel Bakış](../cloud-shell/overview.md) .

> [!IMPORTANT]
> **Az. HPCCache** PowerShell modülü önizlemedeyken, cmdlet 'ini kullanarak ayrı olarak yüklemelisiniz `Install-Module` . Bu PowerShell modülü genel kullanıma sunulduğunda, gelecekteki az PowerShell modülü sürümlerinin bir parçası olur ve Azure Cloud Shell içinden yerel olarak kullanılabilir.

```azurepowershell-interactive
Install-Module -Name Az.HPCCache
```

## <a name="create-the-cache-with-azure-powershell"></a>Azure PowerShell ile önbellek oluşturma

> [!NOTE]
> Azure PowerShell Şu anda, müşteri tarafından yönetilen şifreleme anahtarlarıyla bir önbellek oluşturulmasını desteklemez. Azure portalını kullanın.

Yeni bir Azure HPC önbelleği oluşturmak için [New-AzHpcCache](/powershell/module/az.hpccache/new-azhpccache) cmdlet 'ini kullanın.

Şu değerleri girin:

* Önbellek kaynak grubu adı
* Önbellek adı
* Azure bölgesi
* Önbellek alt ağı şu biçimde:

  `-SubnetUri "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"`

  Önbellek alt ağının en az 64 IP adresi (/24) gerekir ve diğer kaynakları da barındırabilir.

* Önbellek kapasitesi. İki değer, Azure HPC önbelleğinizin en yüksek aktarım hızını ayarlar:

  * Önbellek boyutu (GB olarak)
  * Önbellek altyapısında kullanılan sanal makinelerin SKU 'SU

  [Get-AzHpcCacheSku](/powershell/module/az.hpccache/get-azhpccachesku) , kullanılabilir SKU 'ları ve her biri için geçerli önbellek boyutu seçeneklerini gösterir. Önbellek boyutu seçenekleri 3 TB ile 48 TB arasındadır, ancak yalnızca bazı değerler desteklenir.

  Bu grafik, bu belgenin hazırlanması sırasında hangi önbellek boyutunun ve SKU birleşimlerinin geçerli olduğunu gösterir (2020 Temmuz).

  | Önbellek boyutu | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | evet         | hayır          | hayır          |
  | 6144 GB    | evet         | evet         | hayır          |
  | 12.288 GB   | evet         | evet         | evet         |
  | 24.576 GB   | hayır          | evet         | evet         |
  | 49.152 GB   | hayır          | hayır          | evet         |

  Fiyatlandırma, aktarım hızı ve önbelleğinizi iş akışınız için uygun şekilde boyutlandırma hakkında önemli bilgiler için Portal yönergeleri sekmesindeki **önbellek kapasitesini ayarla** bölümünü okuyun.

Önbellek oluşturma örneği:

```azurepowershell-interactive
$cacheParams = @{
  ResourceGroupName = 'doc-demo-rg'
  CacheName = 'my-cache-0619'
  Location = 'eastus'
  cacheSize = '3072'
  SubnetUri = "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default"
  Sku = 'Standard_2G'
}
New-AzHpcCache @cacheParams
```

Önbellek oluşturma birkaç dakika sürer. Başarılı olduğunda Create komutu aşağıdaki çıktıyı döndürür:

```Output
cacheSizeGb       : 3072
health            : @{state=Healthy; statusDescription=The cache is in Running state}
id                : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619
location          : eastus
mountAddresses    : {10.3.0.17, 10.3.0.18, 10.3.0.19}
name              : my-cache-0619
provisioningState : Succeeded
resourceGroup     : doc-demo-rg
sku               : @{name=Standard_2G}
subnet            : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default
tags              :
type              : Microsoft.StorageCache/caches
upgradeStatus     : @{currentFirmwareVersion=5.3.42; firmwareUpdateDeadline=1/1/0001 12:00:00 AM; firmwareUpdateStatus=unavailable; lastFirmwareUpdate=4/1/2020 10:19:54 AM; pendingFirmwareVersion=}
```

İleti, şu öğeler dahil bazı yararlı bilgiler içerir:

* İstemci bağlama adresleri-istemcileri önbelleğe bağlamaya hazırsanız bu IP adreslerini kullanın. Daha fazla bilgi edinmek için [Azure HPC önbelleğini bağlama](hpc-cache-mount.md) makalesini okuyun.
* Yükseltme durumu-bir yazılım güncelleştirmesi yayınlandığında, bu ileti değişir. [Önbellek yazılımını](hpc-cache-manage.md#upgrade-cache-software) uygun bir zamanda el ile yükseltebilir veya birkaç gün sonra otomatik olarak uygulanır.

---

## <a name="next-steps"></a>Sonraki adımlar

Önbelleğiniz **kaynaklar** listesinde görüntülendikten sonra bir sonraki adıma geçebilirsiniz.

* Veri kaynaklarınıza önbelleğinizi erişiminizi sağlamak için [depolama hedeflerini tanımlayın](hpc-cache-add-storage.md) .
* Müşteri tarafından yönetilen şifreleme anahtarları kullanıyorsanız, önbellek kurulumunuzu gerçekleştirmek için önbelleğin genel bakış sayfasından [Azure Key Vault şifrelemeyi yetkilendirmeniz](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) gerekir. Depolama ekleyebilmek için önce bu adımı yapmanız gerekir. Ayrıntılar için [müşteri tarafından yönetilen şifreleme anahtarlarını kullanın](customer-keys.md) ' i okuyun.
