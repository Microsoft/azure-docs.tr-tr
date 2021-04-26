---
title: Depolama hesabı oluşturma
titleSuffix: Azure Storage
description: Bloblar, dosyalar, kuyruklar ve tablolar depolamak için bir depolama hesabı oluşturmayı öğrenin. Azure depolama hesabı, verilerinizi okumak ve yazmak için Microsoft Azure benzersiz bir ad alanı sağlar.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/19/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8c0158ea4e93b4bbefa7b41b5a280fa804ede35f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791292"
---
# <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Azure depolama hesabı; Bloblar, dosyalar, kuyruklar ve tablolar gibi Azure Storage veri nesnelerinizi içerir. Depolama hesabı, Azure depolama verileriniz için HTTP veya HTTPS üzerinden dünyanın herhangi bir yerinden erişilebilen benzersiz bir ad alanı sağlar. Azure depolama hesapları hakkında daha fazla bilgi için bkz. [depolama hesabına genel bakış](storage-account-overview.md).

Bu nasıl yapılır makalesinde [Azure Portal](https://portal.azure.com/), [Azure POWERSHELL](/powershell/azure/), [Azure CLI](/cli/azure)veya [Azure Resource Manager şablonunu](../../azure-resource-manager/management/overview.md)kullanarak bir depolama hesabı oluşturmayı öğreneceksiniz.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Yok.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell ile bir Azure depolama hesabı oluşturmak için [az PowerShell modülünü](https://www.powershellgallery.com/packages/Az), sürüm 0,7 veya üstünü yüklediğinizden emin olun. Daha fazla bilgi için [Azure PowerShell az Module bölümüne giriş](/powershell/azure/new-azureps-module-az)konusuna bakın.

Geçerli sürümünüzü bulmak için aşağıdaki komutu çalıştırın:

```powershell
Get-InstalledModule -Name "Az"
```

Azure PowerShell yüklemek veya yükseltmek için bkz. [ınstall Azure PowerShell Module](/powershell/azure/install-az-ps).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure 'da oturum açabilir ve Azure CLı komutlarını iki şekilde çalıştırabilirsiniz:

- CLı komutlarını Azure Cloud Shell Azure portal içinden çalıştırabilirsiniz.
- CLı 'yı yükleyebilir ve CLı komutlarını yerel olarak çalıştırabilirsiniz.

### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell kullanma

Azure Cloud Shell doğrudan Azure portalının içinde çalıştırabileceğiniz ücretsiz bir Bash kabuğudur. Azure CLı, önceden yüklenmiş ve hesabınızla kullanılmak üzere yapılandırılmış. Azure portal sağ üst kısmındaki menüdeki **Cloud Shell** düğmesine tıklayın:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Düğme, bu nasıl yapılır makalesinde özetlenen adımları çalıştırmak için kullanabileceğiniz etkileşimli bir kabuk başlatır:

[![Portalda Cloud Shell penceresini gösteren ekran görüntüsü](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>CLI’yi yerel olarak yükleme

Ayrıca, Azure CLI’yi yerel olarak yükleyip kullanabilirsiniz. Bu makaledeki örneklerde Azure CLı sürüm 2.0.4 veya üzeri gerekir. `az --version`Yüklü sürümünüzü bulmak için ' i çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli).

# <a name="template"></a>[Şablon](#tab/template)

Yok.

---

Sonra Azure’da oturum açın.

# <a name="portal"></a>[Portal](#tab/azure-portal)

[Azure portalında](https://portal.azure.com) oturum açın.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Komutuyla Azure aboneliğinizde oturum açın `Connect-AzAccount` ve kimlik doğrulaması yapmak için ekrandaki yönergeleri izleyin.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Cloud Shell başlatmak için [Azure Portal](https://portal.azure.com)oturum açın.

CLı 'nın yerel yüklemesinde oturum açmak için [az Login](/cli/azure/reference-index#az_login) komutunu çalıştırın:

```azurecli-interactive
az login
```

# <a name="template"></a>[Şablon](#tab/template)

Yok

---

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Depolama hesabı bir Azure Resource Manager kaynağıdır. Kaynak Yöneticisi, Azure dağıtım ve yönetim hizmetidir. Daha fazla bilgi için bkz. [Azure Resource Manager'a genel bakış](../../azure-resource-manager/management/overview.md).

Azure depolama hesabı da dahil olmak üzere her Kaynak Yöneticisi kaynağı bir Azure Kaynak grubuna ait olmalıdır. Kaynak grubu, Azure hizmetlerinizi gruplandırmaya yönelik mantıksal bir kapsayıcıdır. Bir depolama hesabı oluşturduğunuzda, yeni bir kaynak grubu oluşturma veya var olan bir kaynak grubu kullanma seçeneğiniz vardır. Bu nasıl yapılır, yeni bir kaynak grubu oluşturmayı gösterir.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal bir Azure depolama hesabı oluşturmak için aşağıdaki adımları izleyin:

1. Sol Portal menüsünde **depolama hesapları ' nı seçerek depolama** hesaplarınızın listesini görüntüleyin.
1. **Depolama hesapları** sayfasında, **Yeni**' yi seçin.

Yeni depolama hesabınız için seçenekler, **depolama hesabı oluşturma** sayfasında sekmeler halinde düzenlenir. Aşağıdaki bölümlerde sekmelerin ve seçeneklerinin her biri açıklanır.

### <a name="basics-tab"></a>Temel bilgiler sekmesi

**Temel bilgiler** sekmesinde, depolama hesabınız için gerekli bilgileri sağlayın. **Temel bilgiler** sekmesini tamamladıktan sonra, diğer sekmelerde seçenekleri ayarlayarak yeni depolama hesabınızı daha fazla özelleştirmeyi tercih edebilir veya varsayılan seçenekleri kabul etmek ve hesabı doğrulamaya ve oluşturmaya devam etmek Için **gözden geçir + oluştur** seçeneğini belirleyebilirsiniz.

Aşağıdaki tabloda **temel bilgiler** sekmesindeki alanlar açıklanmaktadır.

| Section | Alan | Gerekli veya isteğe bağlı | Description |
|--|--|--|--|
| Proje ayrıntıları | Abonelik | Gerekli | Yeni depolama hesabı için aboneliği seçin. |
| Proje ayrıntıları | Kaynak grubu | Gerekli | Bu depolama hesabı için yeni bir kaynak grubu oluşturun veya var olan bir kaynak grubunu seçin. Daha fazla bilgi için bkz. [kaynak grupları](../../azure-resource-manager/management/overview.md#resource-groups). |
| Örnek ayrıntıları | Depolama hesabı adı | Gerekli | Depolama hesabınız için benzersiz bir ad seçin. Depolama hesabı adları 3 ile 24 karakter arasında olmalı ve yalnızca sayıyla küçük harf içermelidir. |
| Örnek ayrıntıları | Region | Gerekli | Depolama hesabınız için uygun bölgeyi seçin. Daha fazla bilgi için bkz. [Azure 'Da bölgeler ve kullanılabilirlik alanları](../../availability-zones/az-overview.md).<br /><br />Tüm bölgeler her türlü depolama hesabı veya artıklık yapılandırması için desteklenmez. Daha fazla bilgi için bkz. [Azure Depolama yedekliliği](storage-redundancy.md).<br /><br />Bölge seçimi bir faturalandırma etkiye sahip olabilir. Daha fazla bilgi için bkz. [depolama hesabı faturalandırması](storage-account-overview.md#storage-account-billing). |
| Örnek ayrıntıları | Performans | Gerekli | Genel amaçlı v2 depolama hesapları için **Standart** performans ' ı seçin (varsayılan). Bu tür bir hesap, çoğu senaryo için Microsoft tarafından önerilir. Daha fazla bilgi için bkz. [depolama hesabı türleri](storage-account-overview.md#types-of-storage-accounts).<br /><br />Düşük gecikme süresi gerektiren senaryolar için **Premium** ' u seçin. **Premium**' u seçtikten sonra oluşturulacak Premium depolama hesabı türünü seçin. Aşağıdaki Premium depolama hesabı türleri kullanılabilir: <ul><li>[Blok blobları](../blobs/storage-blob-performance-tiers.md)</li><li>[Dosya paylaşımları](../files/storage-files-planning.md#management-concepts)</li><li>[Sayfa blobları](../blobs/storage-blob-pageblob-overview.md)</li></ul> |
| Örnek ayrıntıları | Yedeklilik | Gerekli | İstediğiniz artıklık yapılandırmanızı seçin. Her bir bölgedeki tüm depolama hesabı türleri için artıklık seçeneklerinin tümü kullanılamaz. Artıklık yapılandırması hakkında daha fazla bilgi için bkz. [Azure depolama artıklığı](storage-redundancy.md).<br /><br />Coğrafi olarak yedekli bir yapılandırma (GRS veya GZRS) seçerseniz, verileriniz farklı bir bölgedeki veri merkezine çoğaltılır. İkincil bölgedeki verilere yönelik okuma erişimi için **Bölgesel kullanım dışı durumunda bulunan verilere yönelik okuma erişimi oluştur**' u seçin. |

Aşağıdaki görüntüde yeni bir depolama hesabı için standart bir yapılandırma gösterilmektedir.

:::image type="content" source="media/storage-account-create/create-account-basics-tab.png" alt-text="Yeni bir depolama hesabı için Standart yapılandırmayı gösteren ekran görüntüsü-temel bilgiler sekmesi":::

### <a name="advanced-tab"></a>Gelişmiş sekmesi

**Gelişmiş** sekmesinde, yeni depolama hesabınız için ek seçenekler yapılandırabilir ve varsayılan ayarları değiştirebilirsiniz. Bu seçeneklerden bazıları depolama hesabı oluşturulduktan sonra da yapılandırılabilir, diğerleri ise oluşturma sırasında yapılandırılması gerekir.

Aşağıdaki tablo, **Gelişmiş** sekmesindeki alanları açıklar.

| Section | Alan | Gerekli veya isteğe bağlı | Description |
|--|--|--|--|
| Güvenlik | Güvenli aktarımı etkinleştir | İsteğe Bağlı | Bu depolama hesabına yönelik gelen isteklerin yalnızca HTTPS üzerinden yapılmasını gerektirmek için güvenli aktarımı etkinleştirin (varsayılan). En iyi güvenlik için önerilir. Daha fazla bilgi için bkz. [Güvenli aktarım gerektir](storage-require-secure-transfer.md). |
| Güvenlik | Altyapı şifrelemesini etkinleştir | İsteğe Bağlı | Varsayılan olarak, altyapı Şifrelemesi etkin değildir. Hem hizmet düzeyinde hem de altyapı düzeyinde verilerinizi şifrelemek için altyapı şifrelemesini etkinleştirin. Daha fazla bilgi için bkz. [verilerin çift şifrelenmesi için altyapı şifrelemesi etkinleştirilmiş bir depolama hesabı oluşturma](infrastructure-encryption-enable.md). |
| Güvenlik | Blob genel erişimini etkinleştir | İsteğe Bağlı | Bu ayar etkinleştirildiğinde, bir kullanıcıya, depolama hesabındaki bir kapsayıcıya anonim genel erişimi etkinleştirmek için uygun izinleri sağlar (varsayılan). Bu ayarı devre dışı bırakmak, depolama hesabına yönelik tüm anonim genel erişimi engeller. Daha fazla bilgi için bkz. [kapsayıcılar ve bloblara anonim genel okuma erişimini engelleme](../blobs/anonymous-read-access-prevent.md).<br> <br> Blob ortak erişimini etkinleştirmek, Kullanıcı kapsayıcının genel erişim ayarını açıkça yapılandırmak için ek adım kullanmadığı takdirde blob verilerini ortak erişim için kullanılabilir hale getirir. |
| Güvenlik | Depolama hesabı anahtarı erişimini etkinleştirme (Önizleme) | İsteğe Bağlı | Bu ayar etkinleştirildiğinde, istemcilerin, hesap erişim anahtarlarını veya bir Azure Active Directory (Azure AD) hesabını (varsayılan) kullanarak istekleri depolama hesabına yetkilendirmesine olanak tanır. Bu ayarı devre dışı bırakmak, hesap erişim anahtarları ile yetkilendirmeyi engeller. Daha fazla bilgi için bkz. [Azure depolama hesabı Için paylaşılan anahtar yetkilendirmesini engelleme (Önizleme)](shared-key-authorization-prevent.md). |
| Güvenlik | Minimum TLS sürümü | Gerekli | Depolama hesabına gelen istekler için Aktarım Katmanı Güvenliği (TLS) en düşük sürümünü seçin. Varsayılan değer TLS sürüm 1,2 ' dir. Varsayılan değere ayarlandığında, TLS 1,0 veya TLS 1,1 kullanılarak yapılan gelen istekler reddedilir. Daha fazla bilgi için bkz. [depolama hesabına yönelik istekler için en düşük gerekli Aktarım Katmanı Güvenliği (TLS) sürümünü zorlama](transport-layer-security-configure-minimum-version.md). |
| Data Lake Storage 2. Nesil | Hiyerarşik ad alanını etkinleştir | İsteğe Bağlı | Bu depolama hesabını Azure Data Lake Storage 2. iş yükleri için kullanmak üzere hiyerarşik bir ad alanı yapılandırın. Daha fazla bilgi için bkz. [Azure Data Lake Storage 2. giriş](../blobs/data-lake-storage-introduction.md). |
| Blob depolama | Ağ dosya paylaşımının (NFS) v3 'yi (Önizleme) etkinleştir | İsteğe Bağlı | NFS v3, nesne depolama ölçeğinde Linux dosya sistemi uyumluluğu sağlar Linux istemcilerinin bir Azure sanal makinesi (VM) veya şirket içi bir bilgisayardan BLOB depolama alanına bir kapsayıcı takmalarını sağlar. Daha fazla bilgi için bkz. [Azure Blob depolamada (Önizleme) ağ dosya sistemi (NFS) 3,0 protokol desteği](../blobs/network-file-system-protocol-support.md). |
| Blob depolama | Erişim katmanı | Gerekli | Blob erişim katmanları, blob verilerini kullanım açısından en uygun maliyetli biçimde depolamanıza olanak tanır. Sık erişilen veriler için etkin katmanı (varsayılan) seçin. Seyrek erişilen veriler için Cool katmanını seçin. Daha fazla bilgi için bkz. [Azure Blob depolama Için erişim katmanları-sık erişimli, seyrek erişimli ve arşiv](../blobs/storage-blob-storage-tiers.md). |
| Azure Dosyaları | Büyük dosya paylaşımına olanak tanıma | İsteğe Bağlı | Yalnızca dosya paylaşımları için Premium Depolama hesapları için kullanılabilir. Daha fazla bilgi için bkz. [100 TiB 'ye kadar yayılması için standart dosya paylaşımlarını etkinleştirme](../files/storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib). |
| Tablolar ve kuyruklar | Müşteri tarafından yönetilen anahtarlar için desteği etkinleştir | İsteğe Bağlı | Tablolar ve kuyruklar için müşteri tarafından yönetilen anahtarlar desteğini etkinleştirmek üzere, depolama hesabını oluştururken bu ayarı seçmeniz gerekir. Daha fazla bilgi için bkz. [Tablolar ve kuyruklar için müşteri tarafından yönetilen anahtarları destekleyen bir hesap oluşturma](account-encryption-key-create.md). |

### <a name="networking-tab"></a>Ağ sekmesi

**Ağ sekmesinde,** yeni depolama hesabınız için ağ bağlantısı ve yönlendirme tercihi ayarlarını yapılandırabilirsiniz. Bu seçenekler, depolama hesabı oluşturulduktan sonra da yapılandırılabilir.

Aşağıdaki tablo, **ağ** sekmesindeki alanları açıklar.

| Section | Alan | Gerekli veya isteğe bağlı | Description |
|--|--|--|--|
| Ağ bağlantısı | Bağlantı yöntemi | Gerekli | Varsayılan olarak, gelen ağ trafiği, depolama hesabınız için genel uç noktaya yönlendirilir. Trafiğin bir Azure sanal ağı aracılığıyla genel uç noktaya yönlendirilmesi gerektiğini belirtebilirsiniz. Ayrıca, depolama hesabınız için özel uç noktaları yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure depolama için özel uç noktaları kullanma](storage-private-endpoints.md). |
| Ağ yönlendirme | Yönlendirme tercihi | Gerekli | Ağ yönlendirmesi tercihi, ağ trafiğinin Internet üzerinden istemcilerden gelen depolama hesabınızın genel uç noktasına nasıl yönlendirildiğini belirtir. Varsayılan olarak, yeni bir depolama hesabı Microsoft ağ yönlendirmesi kullanır. Ağ trafiğini, depolama hesabına en yakın POP üzerinden yönlendirmeyi de seçebilir ve bu da ağ maliyetlerini düşürebilirler. Daha fazla bilgi için bkz. [Azure depolama Için ağ yönlendirme tercihi](network-routing-preference.md). |

### <a name="data-protection-tab"></a>Veri koruma sekmesi

**Veri koruma** sekmesinde, yeni depolama hesabınızdaki blob verileri için veri koruma seçeneklerini yapılandırabilirsiniz.  Bu seçenekler, depolama hesabı oluşturulduktan sonra da yapılandırılabilir. Azure depolama 'daki veri koruma seçeneklerine genel bakış için bkz. [veri korumasına genel bakış](../blobs/data-protection-overview.md).

Aşağıdaki tablo, **veri koruma** sekmesindeki alanları açıklar.

| Section | Alan | Gerekli veya isteğe bağlı | Description |
|--|--|--|--|
| Kurtarma | Kapsayıcılar için zaman içinde geri yüklemeyi etkinleştir | İsteğe Bağlı | Zaman içinde geri yükleme, Blok Blobu verilerini önceki bir duruma geri yüklemenize olanak tanıyarak yanlışlıkla silinmeye veya bozulmaya karşı koruma sağlar. Daha fazla bilgi için bkz. [blok Blobları Için noktadan noktaya geri yükleme](../blobs/point-in-time-restore-overview.md).<br /><br />Zaman içinde geri yüklemeyi etkinleştirmek, blob sürümü oluşturma, blob geçici silme ve BLOB değişiklik akışını da etkinleştirir. Bu önkoşul özellikleri bir maliyet etkiye sahip olabilir. Daha fazla bilgi için bkz. noktadan noktaya geri yükleme için [fiyatlandırma ve faturalandırma](../blobs/point-in-time-restore-overview.md#pricing-and-billing) . |
| Kurtarma | Bloblar için geçici silmeyi etkinleştirme | İsteğe Bağlı | Blob geçici silme, belirli bir bekletme dönemi için sistemdeki silinen verileri koruarak bir blob, anlık görüntü veya sürümü yanlışlıkla silmeleri veya üzerine yazar. Saklama süresi boyunca, geçici olarak silinen bir nesneyi silindiği sırada durumuna geri yükleyebilirsiniz. Daha fazla bilgi için bkz. [Bloblar Için geçici silme](../blobs/soft-delete-blob-overview.md).<br /><br />Microsoft, depolama hesaplarınız için blob geçici silme özelliğinin etkinleştirilmesini ve en az yedi günlük bekletme süresi ayarlanmasını önerir. |
| Kurtarma | Kapsayıcılar için geçici silmeyi etkinleştir (Önizleme) | İsteğe Bağlı | Kapsayıcı geçici silme, belirli bir bekletme dönemi için sistemdeki silinen verileri koruarak bir kapsayıcıyı ve içeriğini yanlışlıkla silmeleri önler. Saklama süresi boyunca, geçici olarak silinen bir kapsayıcıyı silindiği sırada durumuna geri yükleyebilirsiniz. Daha fazla bilgi için bkz. [kapsayıcılar Için geçici silme (Önizleme)](../blobs/soft-delete-container-overview.md).<br /><br />Microsoft, depolama hesaplarınız için kapsayıcı geçici silme özelliğinin etkinleştirilmesini ve yedi günlük en az bir saklama süresi ayarlanmasını öneriyor. |
| Kurtarma | Dosya paylaşımları için geçici silmeyi etkinleştir | İsteğe Bağlı | Dosya paylaşımları için geçici silme, belirli bir bekletme dönemi için sistemdeki silinen verileri koruarak bir dosya paylaşımını ve içeriğini yanlışlıkla silmeleri önler. Saklama süresi boyunca, geçici olarak silinen bir dosya paylaşımının durumunu silindiği sırada geri yükleyebilirsiniz. Daha fazla bilgi için bkz. [Azure dosya paylaşımlarının yanlışlıkla silinmesini önleme](../files/storage-files-prevent-file-share-deletion.md).<br /><br />Microsoft, Azure dosyaları iş yükleri için dosya paylaşımları için geçici silme özelliğinin etkinleştirilmesini ve en az yedi günlük bekletme süresi ayarlanmasını öneriyor. |
| İzleme | Bloblar için sürüm oluşturmayı etkinleştirme | İsteğe Bağlı | Blob üzerine yazıldığında blob sürümü, önceki bir sürümdeki bir Blobun durumunu otomatik olarak kaydeder. Daha fazla bilgi için bkz. [BLOB sürümü oluşturma](../blobs/versioning-overview.md).<br /><br />Microsoft, depolama hesabı için en iyi veri koruması için blob sürümü oluşturmayı etkinleştirmenizi önerir. |
| İzleme | Blob değişiklik akışını etkinleştir | İsteğe Bağlı | Blob değişiklik akışı, Depolama hesabınızdaki tüm Blobların yanı sıra meta verilerine kadar yapılan tüm değişikliklerin işlem günlüklerini sağlar. Daha fazla bilgi için bkz. [Azure Blob depolamada akış desteğini değiştirme](../blobs/storage-blob-change-feed.md). |

### <a name="tags-tab"></a>Etiketler sekmesi

**Etiketler** sekmesinde, Azure kaynaklarınızın düzenlenmesine yardımcı olacak Kaynak Yöneticisi Etiketler belirtebilirsiniz. Daha fazla bilgi için bkz. [mantıksal kuruluş Için etiket kaynakları, kaynak grupları ve abonelikler](../../azure-resource-manager/management/tag-resources.md).

### <a name="review--create-tab"></a>Gözden geçir + Oluştur sekmesi

**Gözden geçir + oluştur** sekmesine gittiğinizde, Azure seçtiğiniz depolama hesabı ayarları üzerinde doğrulama çalıştırır. Doğrulama başarılı olursa, depolama hesabı oluşturmaya devam edebilirsiniz.

Doğrulama başarısız olursa, Portal hangi ayarların değiştirilmesi gerektiğini gösterir.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell ile genel amaçlı v2 depolama hesabı oluşturmak için, önce [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutunu çağırarak yeni bir kaynak grubu oluşturun:

```azurepowershell-interactive
$resourceGroup = "<resource-group>"
$location = "<location>"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Parametresi için hangi bölgeyi `-Location` belirtlediğinizden emin değilseniz, [Get-azlocation](/powershell/module/az.resources/get-azlocation) komutuyla aboneliğiniz için desteklenen bölgelerin bir listesini alabilirsiniz:

```azurepowershell-interactive
Get-AzLocation | select Location
```

Ardından, [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) komutunu kullanarak Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS) ile standart bir genel amaçlı v2 depolama hesabı oluşturun. Depolama hesabınızın adının Azure genelinde benzersiz olması gerektiğini unutmayın, bu nedenle yer tutucu değerini köşeli ayraç içinde kendi benzersiz bir değere değiştirin:

```azurepowershell-interactive
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

Depolama hesabının [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)kullanacağı hiyerarşik bir ad alanını etkinleştirmek Için, `-EnableHierarchicalNamespace $True` **New-azstorageaccount** komutuna yapılan çağrıya parametresini ekleyin.

Aşağıdaki tabloda, `-SkuName` ve parametreleri için, `-Kind` istenen artıklık yapılandırmasına sahip belirli bir depolama hesabı türü oluşturmak için kullanılacak değerler gösterilmektedir.

| Depolama hesabı türü | Desteklenen artıklık yapılandırması | -Kind parametresinin değeri | -SkuName parametresi için olası değerler | Hiyerarşik ad alanını destekler |
|--|--|--|--|--|
| Standart genel amaçlı v2 | LRS/GRS/RA-GRS/ZRS/GZRS/RA-GZRS | StorageV2 | Standard_LRS/Standard_GRS/Standard_RAGRS/Standard_ZRS/Standard_GZRS/Standard_RAGZRS | Yes |
| Premium blok Blobları | LRS/ZRS | BlockBlobStorage | Premium_LRS/Premium_ZRS | Yes |
| Premium dosya paylaşımları | LRS/ZRS | Dosya depolama | Premium_LRS/Premium_ZRS | No |
| Premium sayfa Blobları | LRS | StorageV2 | Premium_LRS | No |
| Eski standart genel amaçlı v1 | LRS/GRS/RA-GRS | Depolama | Standard_LRS/Standard_GRS/Standard_RAGRS | No |
| Eski blob depolaması | LRS/GRS/RA-GRS | BlobStorage | Standard_LRS/Standard_GRS/Standard_RAGRS | No |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı ile genel amaçlı v2 depolama hesabı oluşturmak için önce [az Group Create](/cli/azure/group#az_group_create) komutunu çağırarak yeni bir kaynak grubu oluşturun.

```azurecli-interactive
az group create \
  --name storage-resource-group \
  --location westus
```

`--location` parametresi için hangi bölgeyi belirteceğinizden emin değilseniz, [az account list-locations](/cli/azure/account#az_account_list) komutuyla aboneliğiniz için desteklenen bölgelerin bir listesini alabilirsiniz.

```azurecli-interactive
az account list-locations \
  --query "[].{Region:name}" \
  --out table
```

Ardından, [az Storage Account Create](/cli/azure/storage/account#az_storage_account_create) komutunu kullanarak Okuma Erişimli Coğrafi olarak yedekli depolama ile standart bir genel amaçlı v2 depolama hesabı oluşturun. Depolama hesabınızın adının Azure genelinde benzersiz olması gerektiğini unutmayın, bu nedenle yer tutucu değerini köşeli ayraç içinde kendi benzersiz bir değere değiştirin:

```azurecli-interactive
az storage account create \
  --name <account-name> \
  --resource-group storage-resource-group \
  --location westus \
  --sku Standard_RAGRS \
  --kind StorageV2
```

Depolama hesabının [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)kullanacağı hiyerarşik bir ad alanını etkinleştirmek için, `--enable-hierarchical-namespace true` **az Storage Account Create** komutuna yapılan çağrıya parametresini ekleyin. Hiyerarşik ad alanı oluşturmak için Azure CLı sürüm 2.0.79 veya üzeri gerekir.

Aşağıdaki tabloda, `-sku` ve parametreleri için, `-kind` istenen artıklık yapılandırmasına sahip belirli bir depolama hesabı türü oluşturmak için kullanılacak değerler gösterilmektedir.

| Depolama hesabı türü | Desteklenen artıklık yapılandırması | -Kind parametresinin değeri | -SKU parametresi için olası değerler | Hiyerarşik ad alanını destekler |
|--|--|--|--|--|
| Standart genel amaçlı v2 | LRS/GRS/RA-GRS/ZRS/GZRS/RA-GZRS | StorageV2 | Standard_LRS/Standard_GRS/Standard_RAGRS/Standard_ZRS/Standard_GZRS/Standard_RAGZRS | Yes |
| Premium blok Blobları | LRS/ZRS | BlockBlobStorage | Premium_LRS/Premium_ZRS | Yes |
| Premium dosya paylaşımları | LRS/ZRS | Dosya depolama | Premium_LRS/Premium_ZRS | No |
| Premium sayfa Blobları | LRS | StorageV2 | Premium_LRS | No |
| Eski standart genel amaçlı v1 | LRS/GRS/RA-GRS | Depolama | Standard_LRS/Standard_GRS/Standard_RAGRS | No |
| Eski blob depolaması | LRS/GRS/RA-GRS | BlobStorage | Standard_LRS/Standard_GRS/Standard_RAGRS | No |

# <a name="template"></a>[Şablon](#tab/template)

Bir depolama hesabı oluşturmak üzere bir Kaynak Yöneticisi şablonu dağıtmak için Azure PowerShell veya Azure CLı kullanabilirsiniz. Bu nasıl yapılır makalesinde kullanılan şablon [Azure Resource Manager hızlı başlangıç şablonlarından](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Betikleri çalıştırmak için **dene** ' yi seçerek Azure Cloud Shell açın. Betiği yapıştırmak için, kabuğa sağ tıklayın ve ardından **Yapıştır**' ı seçin.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

> [!NOTE]
> Bu şablon yalnızca örnek olarak işlev görür. Bu şablonun bir parçası olarak yapılandırılmayan çok sayıda depolama hesabı ayarı var. Örneğin, [Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)kullanmak istiyorsanız, `isHnsEnabledad` nesnesinin özelliğini olarak ayarlayarak bu şablonu değiştirirsiniz `StorageAccountPropertiesCreateParameters` `true` .

Bu şablonu değiştirme veya yenilerini oluşturma hakkında bilgi edinmek için bkz.:

- [Azure Resource Manager belgeleri](../../azure-resource-manager/index.yml).
- [Depolama hesabı şablon başvurusu](/azure/templates/microsoft.storage/allversions).
- [Ek depolama hesabı şablonu örnekleri](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

## <a name="delete-a-storage-account"></a>Bir depolama hesabını silme

Bir depolama hesabını silmek, hesaptaki tüm veriler de dahil olmak üzere tüm hesabı siler ve geri alınamaz.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)depolama hesabına gidin.
1. **Sil**'e tıklayın.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Depolama hesabını silmek için [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) komutunu kullanın:

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Depolama hesabını silmek için [az Storage Account Delete](/cli/azure/storage/account#az_storage_account_delete) komutunu kullanın:

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Şablon](#tab/template)

Depolama hesabını silmek için Azure PowerShell ya da Azure CLı kullanın.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

Alternatif olarak, depolama hesabını ve bu kaynak grubundaki diğer kaynakları silen kaynak grubunu silebilirsiniz. Kaynak grubunu silme hakkında daha fazla bilgi için bkz. [kaynak grubunu ve kaynakları silme](../../azure-resource-manager/management/delete-resource-group.md).

> [!WARNING]
> Silinen depolama hesabını geri yüklemek veya silme işlemi öncesinde içinde yer alan içerikleri almak mümkün değildir. Hesabı silmeden önce kaydetmek istediğiniz şeyleri yedeklediğinizden emin olun. Bu ayrıca hesaptaki tüm kaynaklar için geçerlidir; bir blob, tablo, kuyruk veya dosya sildiğinizde bu işlem kalıcı olarak gerçekleştirilir.
>
> Bir Azure sanal makinesiyle ilişkili bir depolama hesabını silmeye çalışırsanız, depolama hesabının kullanımda olduğu hakkında bir hata alabilirsiniz. Bu hatayla ilgili sorun giderme konusunda yardım için bkz. [depolama hesaplarını silerken hata giderme](/troubleshoot/azure/virtual-machines/welcome-virtual-machines).

## <a name="next-steps"></a>Sonraki adımlar

- [Depolama hesabına genel bakış](storage-account-overview.md)
- [Genel amaçlı v2 depolama hesabına yükseltme](storage-account-upgrade.md)
- [Bir depolama hesabını başka bir bölgeye taşıma](storage-account-move.md)
- [Silinmiş bir depolama hesabını kurtarma](storage-account-recover.md)