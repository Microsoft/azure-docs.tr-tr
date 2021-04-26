---
title: Azure HPC önbelleğine depolama ekleme
description: Azure HPC önbelleğinizin, uzun süreli dosya depolaması için şirket içi NFS sisteminizi veya Azure Blob kapsayıcılarını kullanabilmesi için depolama hedeflerini tanımlama
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 708ad8bbfec9e3fd0176c53c111b5b5b25a5318f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862246"
---
# <a name="add-storage-targets"></a>Depolama hedefleri ekleme

*Depolama hedefleri* , BIR Azure HPC önbelleğinden erişilen dosyalar için arka uç depolardır. NFS depolama (Şirket içi donanım sistemi gibi) ekleyebilir veya Azure Blob 'da verileri saklayabilirsiniz.

Tek bir önbellek için 20 ' ye kadar farklı depolama hedefi tanımlayabilirsiniz. Önbellek, tüm depolama hedeflerini toplanmış bir ad alanında gösterir.

Ad alanı yolları, depolama hedeflerini ekledikten sonra ayrı olarak yapılandırılır. Genel olarak, bir NFS depolama hedefi en fazla on ad alanı yoluna sahip olabilir veya bazı büyük yapılandırmalarda daha fazla bulunabilir. Ayrıntılar için [NFS ad alanı yollarını](add-namespace-paths.md#nfs-namespace-paths) okuyun.

Depolama dışarı aktarımlarının, önbelleğinizin sanal ağından erişilebilir olması gerektiğini unutmayın. Şirket içi donanım depolaması için NFS depolama erişimi için konak adlarını çözebilen bir DNS sunucusu ayarlamanız gerekebilir. [DNS erişiminde](hpc-cache-prerequisites.md#dns-access)daha fazla bilgi edinin.

Önbelleğinizi oluşturduktan sonra depolama hedefleri ekleyin. Bu işlemi izleyin:

1. [Önbelleği oluşturma](hpc-cache-create.md)
1. Depolama hedefi tanımlama (Bu makaledeki bilgiler)
1. [İstemciye yönelik yolları oluşturma](add-namespace-paths.md) ( [toplanan ad alanı](hpc-cache-namespace.md)için)

Depolama hedefi ekleme yordamı, kullandığı depolama türüne bağlı olarak biraz farklıdır. Her biri için Ayrıntılar aşağıda verilmiştir.

Bir önbellek oluşturma ve Azure portal bir depolama hedefi ekleme hakkında [video tanıtımı](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) izlemek için aşağıdaki görüntüye tıklayın.

[![video küçük resmi: Azure HPC Cache: Setup (video sayfasını ziyaret etmek için tıklayın)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="add-a-new-azure-blob-storage-target"></a>Yeni bir Azure Blob depolama hedefi ekleme

Yeni bir BLOB depolama hedefi boş bir blob kapsayıcısına veya Azure HPC önbellek bulut dosya sistemi biçimindeki verilerle doldurulmuş bir kapsayıcıya ihtiyaç duyuyor. [Verileri Azure Blob depolamaya taşıma](hpc-cache-ingest.md)bölümünde bir blob kapsayıcısını önceden yükleme hakkında daha fazla bilgi edinin.

Azure portal **depolama hedefi ekleme** sayfası, eklemeden hemen önce yeni bir blob kapsayıcısı oluşturma seçeneğini içerir.

> [!NOTE]
> NFS bağlı blob depolaması için [ADLS-NFS depolama hedefi](#) türünü kullanın.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal, önbellek örneğinizi açın ve sol kenar çubuğundaki **depolama hedefleri** ' ne tıklayın.

![bir tabloda iki mevcut depolama hedefi ve tablonun üzerindeki + depolama hedefi Ekle düğmesinin etrafında vurgu içeren ayarlar > depolama hedefi sayfasının ekran görüntüsü](media/add-storage-target-button.png)

**Depolama hedefleri** sayfası, tüm mevcut hedefleri listeler ve yeni bir bağlantı eklemek için bir bağlantı sağlar.

**Depolama hedefi Ekle** düğmesine tıklayın.

![Yeni bir Azure Blob depolama hedefi için bilgilerle doldurulmuş depolama hedefi ekleme sayfasının ekran görüntüsü](media/hpc-cache-add-blob.png)

Bir Azure Blob kapsayıcısı tanımlamak için bu bilgileri girin.

* **Depolama hedefi adı** -Azure HPC önbelleğinde bu depolama hedefini tanımlayan bir ad ayarlayın.
* **Hedef türü** - **BLOB** seçin.
* **Depolama hesabı** -kullanmak istediğiniz hesabı seçin.

  [Erişim rolleri ekleme](#add-the-access-control-roles-to-your-account)bölümünde açıklandığı gibi, depolama hesabına erişmek için önbellek örneğini yetkilendirmeniz gerekecektir.

  Kullanabileceğiniz depolama hesabı türü hakkında daha fazla bilgi için, [BLOB depolama gereksinimlerini](hpc-cache-prerequisites.md#blob-storage-requirements)okuyun.

* **Depolama kapsayıcısı** -bu hedefin blob kapsayıcısını seçin veya **Yeni oluştur**' a tıklayın.

  ![Yeni kapsayıcı için ad ve erişim düzeyi (özel) belirtme iletişim kutusunun ekran görüntüsü](media/add-blob-new-container.png)

İşiniz bittiğinde, depolama hedefini eklemek için **Tamam** ' ı tıklatın.

> [!NOTE]
> Depolama hesabı güvenlik duvarınız yalnızca "seçili ağlara erişimi kısıtla" olarak ayarlandıysa, [BLOB depolama hesabı güvenlik duvarı ayarlarında çözüm](hpc-cache-blob-firewall-fix.md)olarak belgelenen geçici geçici çözümü kullanın.

### <a name="add-the-access-control-roles-to-your-account"></a>Erişim denetimi rollerini hesabınıza ekleyin

Azure HPC Cache, Azure Blob depolama hedeflerine yönelik depolama hesabınıza erişmek üzere Önbellek hizmetini yetkilendirmek için [Azure rol tabanlı erişim denetimi 'ni (Azure RBAC)](../role-based-access-control/index.yml) kullanır.

Depolama hesabı sahibi, "HPC Cache kaynak sağlayıcısı" kullanıcısı için rol [depolama hesabı katılımcısı](../role-based-access-control/built-in-roles.md#storage-account-contributor) ve [Depolama Blobu veri katılımcısı](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) ' nı açıkça eklemesi gerekir.

Bunu zaman içinde yapabilir veya bir BLOB depolama hedefi eklediğiniz sayfada bir bağlantıya tıklayarak yapabilirsiniz. Rol ayarlarının Azure ortamından yayılması beş dakika sürebileceğini unutmayın. bu nedenle, bir depolama hedefi oluşturmadan önce roller eklendikten sonra birkaç dakika beklemeniz gerekir.

Azure rollerini ekleme adımları:

1. Depolama hesabı için **erişim denetimi (IAM)** sayfasını açın. ( **Depolama hedefi Ekle** sayfasındaki bağlantı, seçili hesap için otomatik olarak bu sayfayı açar.)

1. Sayfanın üst kısmındaki öğesine tıklayın **+** ve **rol ataması Ekle**' yi seçin.

1. Listeden "depolama hesabı katılımcısı" rolünü seçin.

1. **Erişim ata** alanına, varsayılan değeri seçili bırakın ("Azure AD kullanıcısı, Grup veya hizmet sorumlusu").  

1. **Seç** alanında "HPC" ifadesini arayın.  Bu dize, "HPC Cache Resource Provider" adlı bir hizmet sorumlusu ile eşleşmelidir. Bu sorumluyu seçmek için tıklayın.

   > [!NOTE]
   > "HPC" araması işe yaramazsa bunun yerine "storagecache" dizesini kullanmayı deneyin. Önizlemelerde (GA 'den önce) katılan kullanıcıların hizmet sorumlusu için eski adı kullanması gerekebilir.

1. Alttaki **Kaydet** düğmesine tıklayın.

1. "Depolama Blobu verileri katılımcısı" rolünü atamak için bu işlemi tekrarlayın.  

![rol ataması Ekle GUI ekran görüntüsü](media/hpc-cache-add-role.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisite-storage-account-access"></a>Önkoşul: depolama hesabı erişimi

[Azure HPC önbelleği Için Azure CLI 'Yi ayarlayın](./az-cli-prerequisites.md).

BLOB depolama hedefi eklemeden önce, önbelleğin depolama hesabına erişmek için doğru rollere sahip olduğunu ve güvenlik duvarı ayarlarının depolama hedefi oluşturulmasına izin ver, olduğunu kontrol edin.

Azure HPC Cache, Azure Blob depolama hedeflerine yönelik depolama hesabınıza erişmek üzere Önbellek hizmetini yetkilendirmek için [Azure rol tabanlı erişim denetimi 'ni (Azure RBAC)](../role-based-access-control/index.yml) kullanır.

Depolama hesabı sahibi, "HPC Cache kaynak sağlayıcısı" kullanıcısı için rol [depolama hesabı katılımcısı](../role-based-access-control/built-in-roles.md#storage-account-contributor) ve [Depolama Blobu veri katılımcısı](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) ' nı açıkça eklemesi gerekir.

Önbellekte bu roller yoksa depolama hedefi oluşturma başarısız olur.

Rol ayarlarının Azure ortamından yayılması beş dakika kadar sürebilir. bu nedenle, bir depolama hedefi oluşturmadan önce roller eklendikten sonra birkaç dakika beklemeniz gerekir.

Ayrıntılı yönergeler için Azure [CLI kullanarak Azure rol atamaları ekleme veya kaldırma](../role-based-access-control/role-assignments-cli.md) konusunu okuyun.

Ayrıca depolama hesabınızın güvenlik duvarı ayarlarını kontrol edin. Güvenlik Duvarı yalnızca "seçili ağlara erişimi kısıtla" olarak ayarlandıysa, depolama hedefi oluşturma işlemi başarısız olabilir. [BLOB depolama hesabı güvenlik duvarı ayarları etrafında çalışırken](hpc-cache-blob-firewall-fix.md)belgelenen geçici çözümü kullanın.

### <a name="add-a-blob-storage-target-with-azure-cli"></a>Azure CLı ile BLOB depolama hedefi ekleme

Bir Azure Blob depolama hedefi tanımlamak için [az HPC-Cache blob-Storage-Target Add](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_add) arabirimini kullanın.

> [!NOTE]
> Azure CLı komutları Şu anda bir depolama hedefi eklerken bir ad alanı yolu oluşturmanızı gerektirir. Bu, Azure portal arabirimiyle kullanılan işlemden farklıdır.

Standart kaynak grubu ve önbellek adı parametrelerine ek olarak, depolama hedefi için bu seçenekleri sağlamanız gerekir:

* ``--name`` -Azure HPC önbelleğinde bu depolama hedefini tanımlayan bir ad ayarlayın.

* ``--storage-account`` -Hesap tanımlayıcısı şu biçimdedir:/Subscriptions/*<subscription_id>*/ResourceGroups/*<storage_resource_group>*/Providers/Microsoft.Storage/storageAccounts/*<account_name>*

  Kullanabileceğiniz depolama hesabı türü hakkında daha fazla bilgi için, [BLOB depolama gereksinimlerini](hpc-cache-prerequisites.md#blob-storage-requirements)okuyun.

* ``--container-name`` -Bu depolama hedefi için kullanılacak kapsayıcının adını belirtin.

* ``--virtual-namespace-path`` -Bu depolama hedefi için istemciye yönelik dosya yolunu ayarlayın. Yolları tırnak işaretleri içine alın. Sanal ad alanı özelliği hakkında daha fazla bilgi edinmek için [toplanan ad alanı planını](hpc-cache-namespace.md) okuyun.

Örnek komut:

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>Yeni bir NFS depolama hedefi ekleme

Bir NFS depolama hedefi, bir BLOB depolama hedefinden farklı ayarlara sahiptir. Kullanım modeli ayarı önbelleğin bu depolama sisteminden verileri verimli bir şekilde önbelleğe almak için yardımcı olur.

![NFS hedefi tanımlı depolama hedefi ekleme sayfasının ekran görüntüsü](media/add-nfs-target.png)

> [!NOTE]
> Bir NFS depolama hedefi oluşturmadan önce, depolama sisteminizin Azure HPC önbelleğinden erişilebilir olduğundan ve izin gereksinimlerini karşıladığından emin olun. Önbellek depolama sistemine erişemeyecektir depolama hedefi oluşturma işlemi başarısız olur. Ayrıntılar için [NFS depolama gereksinimlerini](hpc-cache-prerequisites.md#nfs-storage-requirements) okuyun ve [NAS yapılandırma ve NFS depolama hedefi sorunlarını giderin](troubleshoot-nas.md) .

### <a name="choose-a-usage-model"></a>Kullanım modeli seçin
<!-- referenced from GUI by aka.ms link -->

Depolama sistemine ulaşmak için NFS kullanan bir depolama hedefi oluşturduğunuzda, bu hedef için bir kullanım modeli seçmeniz gerekir. Bu model, verilerinizin nasıl önbelleğe alınacağını belirler.

Bu ayarların tümü hakkında daha fazla ayrıntı için bkz. [kullanım modellerini anlayın](cache-usage-models.md) .

Yerleşik kullanım modelleri, eski verileri alma riskiyle hızlı yanıt dengelemeye olanak tanır. Dosya okuma hızını iyileştirmek isterseniz, önbellekteki dosyaların arka uç dosyalarına karşı kontrol edilip edilmeyeceğini önemsemeyebilirsiniz. Diğer taraftan, her zaman uzak depolama ile dosyalarınızın güncel olduğundan emin olmak istiyorsanız, sık denetleyen bir model seçin.

Bu üç seçenek çoğu durum kapsar:

* **Yoğun, seyrek erişimli yazmaları okuma** -statik veya nadiren değiştirilen dosyalara okuma erişimini hızlandırır.

  Bu seçenek, dosyaları istemci okumalarından önbelleğe alır, ancak istemci yazma işlemlerini doğrudan arka uç depolamaya geçirir. Önbellekte depolanan dosyalar, NFS depolama birimindeki dosyalarla otomatik olarak karşılaştırılmaz.

  Bir dosyanın doğrudan depolama sisteminde önbellekte yazılmadan değiştirilebilmesi için bir risk varsa bu seçeneği kullanmayın. Bu durumda, dosyanın önbelleğe alınmış sürümü arka uç dosyasıyla eşitlenmemiş olur.

* **%15 ' ten fazla yazma** -Bu seçenek hem okuma hem de yazma performansını hızlandırır.

  İstemci okuma ve istemci yazmaları her ikisi de önbelleğe alınır. Önbellekteki dosyaların arka uç depolama sistemindeki dosyalardan daha yeni olduğu varsayılır. Önbelleğe alınan dosyalar yalnızca her sekiz saatte bir arka uç depolamada bulunan dosyalar için otomatik olarak denetlenir. Önbellekte değiştirilen dosyalar ek değişiklik olmadan 20 dakika boyunca önbellekte olduktan sonra arka uç depolama sistemine yazılır.

  Herhangi bir istemci arka uç depolama birimini doğrudan bağlamadığı takdirde bu seçeneği kullanmayın, çünkü bir risk eski dosyalar olur.

* **İstemciler, önbelleği ATLAYARAK NFS hedefine yazar** -iş akışlarınızdan herhangi bir istemci, önce önbelleğe yazmadan veya veri tutarlılığını iyileştirmek istiyorsanız bu seçeneği belirleyin.

  İstemcilerin istediği dosyalar önbelleğe alınır, ancak istemciden bu dosyalardaki tüm değişiklikler arka uç depolama sistemine hemen geçirilir. Önbellekteki dosyalar güncelleştirmeler için arka uç sürümlerine göre sıklıkla denetlenir. Bu doğrulama, dosyalar önbellek yerine doğrudan depolama sisteminde değiştirildiğinde veri tutarlılığını korur.

Diğer seçeneklerle ilgili ayrıntılar için bkz. [kullanım modellerini anlayın](cache-usage-models.md).

Bu tablo tüm kullanım modelleri arasındaki farkları özetler:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

> [!NOTE]
> **Arka uç doğrulama** değeri, önbelleğin, dosyalarını uzak depolamadaki kaynak dosyalarla otomatik olarak karşılaştırdığı zamanı gösterir. Ancak, arka uç depolama sisteminde readdirplus işlemini içeren bir istemci isteği göndererek karşılaştırmayı tetikleyebilirsiniz. Readdirplus, dizin meta verileri döndüren standart bir NFS API 'sidir (genişletilmiş okuma olarak da bilinir). Bu, önbelleğin dosyaları karşılaştırmasına ve güncelleştirmesine neden olur.

### <a name="create-an-nfs-storage-target"></a>NFS depolama hedefi oluşturma

### <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal, önbellek örneğinizi açın ve sol kenar çubuğundaki **depolama hedefleri** ' ne tıklayın.

![bir tabloda iki mevcut depolama hedefi ve tablonun üzerindeki + depolama hedefi Ekle düğmesinin etrafında vurgu içeren ayarlar > depolama hedefi sayfasının ekran görüntüsü](media/add-storage-target-button.png)

**Depolama hedefleri** sayfası, tüm mevcut hedefleri listeler ve yeni bir bağlantı eklemek için bir bağlantı sağlar.

**Depolama hedefi Ekle** düğmesine tıklayın.

![NFS hedefi tanımlı depolama hedefi ekleme sayfasının ekran görüntüsü](media/add-nfs-target.png)

NFS ile desteklenen bir depolama hedefi için şu bilgileri sağlayın:

* **Depolama hedefi adı** -Azure HPC önbelleğinde bu depolama hedefini tanımlayan bir ad ayarlayın.

* **Hedef türü** - **NFS**'yi seçin.

* **Ana bilgisayar** adı-NFS depolama sisteminizin IP adresini veya tam etki alanı adını girin. (Yalnızca önbelleğinizin adı çözebilecek bir DNS sunucusuna erişimi varsa, bir etki alanı adı kullanın.)

* **Kullanım modeli** -yukarıda [bir kullanım modeli seçin](#choose-a-usage-model) bölümünde açıklanan iş akışınızı temel alan veri önbelleğe alma profillerinden birini seçin.

İşiniz bittiğinde, depolama hedefini eklemek için **Tamam** ' ı tıklatın.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC önbelleği Için Azure CLI 'Yi ayarlayın](./az-cli-prerequisites.md).

Depolama hedefini oluşturmak için [az HPC-Cache NFS-Storage-Target Add](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_add) Azure CLI komutunu kullanın.

> [!NOTE]
> Azure CLı komutları Şu anda bir depolama hedefi eklerken bir ad alanı yolu oluşturmanızı gerektirir. Bu, Azure portal arabirimiyle kullanılan işlemden farklıdır.

Bu değerleri, önbellek adı ve önbellek kaynak grubuna ek olarak sağlayın:

* ``--name`` -Azure HPC önbelleğinde bu depolama hedefini tanımlayan bir ad ayarlayın.
* ``--nfs3-target`` -NFS depolama sisteminizin IP adresi. (Önbelleğiniz adı çözebilecek bir DNS sunucusuna erişimi varsa, tam etki alanı adını burada kullanabilirsiniz.)
* ``--nfs3-usage-model`` -Yukarıda [bir kullanım modeli seçin](#choose-a-usage-model)bölümünde açıklanan veri önbelleğe alma profillerinden biridir.

  [Az HPC-Cache Usage-model List](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage_model_list)komutuyla kullanım modellerinin adlarını doğrulayın.

* ``--junction`` -Kavşak parametresi, istemciye yönelik sanal dosya yolunu depolama sistemindeki bir dışarı aktarma yoluyla bağlar.

  Her bir yol aynı depolama sisteminde farklı bir dışarı aktarma veya alt dizini temsil ettiğinde, bir NFS depolama hedefi birden çok sanal yola sahip olabilir. Tek bir depolama hedefinde bir depolama sistemi için tüm yolları oluşturun.

  Dilediğiniz zaman, bir depolama hedefinde [ad alanı yolları ekleyebilir ve düzenleyebilirsiniz](add-namespace-paths.md) .

  ``--junction``Parametresi şu değerleri kullanır:

  * ``namespace-path`` -İstemciye yönelik sanal dosya yolu
  * ``nfs-export`` -İstemciye yönelik yol ile ilişkilendirilecek depolama sistemi dışa aktarma
  * ``target-path`` (isteğe bağlı)-gerekirse, dışarı aktarmanın alt dizini

  Örnek: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  Sanal ad alanı özelliği hakkında daha fazla bilgi edinmek için [toplanan ad alanını Yapılandır](hpc-cache-namespace.md) makalesini okuyun.

Örnek komut:

```azurecli

az hpc-cache nfs-storage-target add --resource-group "hpc-cache-group" --cache-name "doc-cache0629" \
    --name nfsd1 --nfs3-target 10.0.0.4 --nfs3-usage-model WRITE_WORKLOAD_15 \
    --junction namespace-path="/nfs1/data1" nfs-export="/datadisk1" target-path=""
```

Çıkış:
```azurecli

{- Finished ..
  "clfs": null,
  "id": "/subscriptions/<subscriptionID>/resourceGroups/hpc-cache-group/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "hpc-cache-group",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}

```

---

## <a name="add-a-new-adls-nfs-storage-target-preview"></a>Yeni bir ADLS-NFS depolama hedefi ekleme (ÖNIZLEME)

ADLS-NFS depolama hedefleri, ağ dosya sistemi (NFS) 3,0 protokolünü destekleyen Azure Blob kapsayıcıları kullanır.

> [!NOTE]
> Azure Blob depolama için NFS 3,0 protokol desteği genel önizlemeye sunuldu. Kullanılabilirlik kısıtlıdır ve özellik genel kullanıma sunulduğunda özellikler sınırlı olur. Üretim sistemlerinde önizleme teknolojisini kullanmayın.
>
> En son bilgiler için [NFS 3,0 protokol desteğini](../storage/blobs/network-file-system-protocol-support.md) okuyun.

ADLS-NFS depolama hedefleri, BLOB depolama hedefleri ve bazı NFS depolama hedefleri ile benzerlikler vardır. Örnek:

* BLOB depolama hedefi gibi, [depolama hesabınıza erişmek](#add-the-access-control-roles-to-your-account)IÇIN Azure HPC önbelleği izni vermeniz gerekir.
* NFS depolama hedefi gibi bir önbellek [kullanım modeli](#choose-a-usage-model)ayarlamanız gerekir.
* NFS özellikli blob kapsayıcıları, NFS uyumlu bir hiyerarşik yapıya sahip olduğundan, verileri almak için önbelleği kullanmanız gerekmez ve kapsayıcılar diğer NFS sistemleri tarafından okunabilir. Bir ADLS-NFS kapsayıcısında verileri önceden yükleyebilir, ardından bir depolama hedefi olarak HPC önbelleğine ekleyebilir ve sonra verileri HPC önbelleğinin dışından daha sonra erişebilirsiniz. HPC önbellek depolama hedefi olarak standart bir blob kapsayıcısı kullandığınızda, veriler özel bir biçimde yazılır ve yalnızca diğer Azure HPC Cache ile uyumlu ürünlerden erişilebilir.

Bir ADLS-NFS depolama hedefi oluşturabilmeniz için önce NFS özellikli bir depolama hesabı oluşturmanız gerekir. [Azure HPC Cache önkoşulları](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements-preview) ve [NFS kullanarak blob Storage bağlama](../storage/blobs/network-file-system-protocol-support-how-to.md)bölümündeki yönergeleri izleyin. Depolama hesabınızı ayarladıktan sonra, depolama hedefini oluştururken yeni bir kapsayıcı oluşturabilirsiniz.

Bu yapılandırma hakkında daha fazla bilgi edinmek için [Azure HPC Cache Ile NFS 'e bağlı BLOB depolama kullanın](nfs-blob-considerations.md) makalesini okuyun.

Bir ADLS-NFS depolama hedefi oluşturmak için Azure portal **depolama hedefi Ekle** sayfasını açın. (Ek yöntemler geliştirmektedir.)

![ADLS-NFS hedefi tanımlı depolama hedefi ekleme sayfasının ekran görüntüsü](media/add-adls-target.png)

Bu bilgileri girin.

* **Depolama hedefi adı** -Azure HPC önbelleğinde bu depolama hedefini tanımlayan bir ad ayarlayın.
* **Hedef türü** - **ADLS-NFS**' i seçin.
* **Depolama hesabı** -kullanmak istediğiniz hesabı seçin. NFS etkinleştirilmiş depolama hesabınız listede görünmezse, önkoşullara uygun olduğunu ve önbelleğin buna erişip erişemediğinden emin olun.

  [Erişim rolleri ekleme](#add-the-access-control-roles-to-your-account)bölümünde açıklandığı gibi, depolama hesabına erişmek için önbellek örneğini yetkilendirmeniz gerekecektir.

* **Depolama kapsayıcısı** -bu hedef için NFS etkin blob kapsayıcısını seçin veya **Yeni oluştur**' a tıklayın.

* **Kullanım modeli** -yukarıda [bir kullanım modeli seçin](#choose-a-usage-model) bölümünde açıklanan iş akışınızı temel alan veri önbelleğe alma profillerinden birini seçin.

İşiniz bittiğinde, depolama hedefini eklemek için **Tamam** ' ı tıklatın.

## <a name="view-storage-targets"></a>Depolama hedeflerini görüntüleme

Önbelleğiniz için önceden tanımlanmış depolama hedeflerini göstermek için Azure portal veya Azure CLı kullanabilirsiniz.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal, önbellek örneğinizi açın ve sol kenar çubuğundaki ayarlar başlığı altında bulunan **depolama hedefleri**' ne tıklayın. Depolama hedefleri sayfası, var olan tüm hedefleri ve bunları ekleme veya silmeye yönelik denetimleri listeler.

Ayrıntılar sayfasını açmak için bir depolama hedefinin adına tıklayın.

Daha fazla bilgi edinmek için [depolama hedeflerini düzenleme](hpc-cache-edit-storage.md) bölümünü okuyun.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC önbelleği Için Azure CLI 'Yi ayarlayın](./az-cli-prerequisites.md).

Bir önbelleğin mevcut depolama hedeflerini göstermek için [az HPC-Cache Storage-Target List](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list) seçeneğini kullanın. Önbellek adını ve kaynak grubunu sağlayın (Bu ayarı küresel olarak ayarlamadığınız müddetçe).

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

Belirli bir depolama hedefi hakkındaki ayrıntıları görmek için [az HPC-Cache Storage-Target Show](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list) ' i kullanın. (Depolama hedefini ada göre belirtin.)

Örnek:

```azurecli
$ az hpc-cache storage-target show --cache-name doc-cache0629 --name nfsd1

{
  "clfs": null,
  "id": "/subscriptions/<subscription_ID>/resourceGroups/scgroup/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "scgroup",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}
$
```

---

## <a name="next-steps"></a>Sonraki adımlar

Depolama hedefleri oluşturduktan sonra önbelleğinizi kullanıma hazırlamak için bu görevlerle devam edin:

* [Toplanan ad alanını ayarlama](add-namespace-paths.md)
* [Azure HPC Önbelleği’ni bağlama](hpc-cache-mount.md)
* [Verileri Azure Blob depolamaya taşıma](hpc-cache-ingest.md)

Herhangi bir ayarı güncelleştirmeniz gerekiyorsa, [bir depolama hedefini düzenleyebilirsiniz](hpc-cache-edit-storage.md).
