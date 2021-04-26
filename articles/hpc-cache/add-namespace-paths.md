---
title: Azure HPC önbelleği toplanmış ad alanını yapılandırma
description: Azure HPC Cache ile arka uç depolaması için istemciye yönelik yollar oluşturma
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: 2cb8db4e73a8f4fa299031070bffc15a2b754d7e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870382"
---
# <a name="set-up-the-aggregated-namespace"></a>Toplanan ad alanını ayarlama

Depolama hedefleri oluşturduktan sonra, bunlar için de ad alanı yolları oluşturmanız gerekir. İstemci makineleri, arka uç depolamaya doğrudan bağlanmak yerine bu sanal yolları dosyalara önbellek aracılığıyla erişmek için kullanır. Bu sistem, istemci yönergelerini yeniden yazmak zorunda kalmadan önbellek yöneticilerinin arka uç depolama sistemlerini değiştirmesine izin verir.

Bu özellik hakkında daha fazla bilgi edinmek için [toplanan ad alanı planını](hpc-cache-namespace.md) okuyun.

Azure portal **ad alanı** sayfasında, istemcilerin, önbellekteki verilerinize erişmek için kullandığı yollar gösterilir. Ad alanı yollarını oluşturmak, kaldırmak veya değiştirmek için bu sayfayı kullanın. Azure CLı kullanarak ad alanı yollarını da yapılandırabilirsiniz.

Bu önbellek için tanımlanan tüm istemciye yönelik yollar **ad alanı** sayfasında listelenir. Tanımlı bir ad alanı yoluna sahip olmayan depolama hedefleri, tabloda görünmemiştir.

Önbelleğinizi toplanmış ad alanını daha iyi anlamak için tablo sütunlarını sıralayabilirsiniz. Yolları sıralamak için sütun başlıklarındaki oklara tıklayın.

[![bir tabloda iki yol içeren Portal ad alanı sayfasının ekran görüntüsü. Sütun başlıkları: ad alanı yolu, depolama hedefi, dışa aktarma yolu ve dışarı aktarma alt dizini ve Istemci erişim ilkesi. İlk sütundaki yol adları tıklatılabilir bağlantılardır. Üst düğmeler: ad alanı yolu Ekle, Yenile, Sil ](media/namespace-page.png)](media/namespace-page.png#lightbox)

## <a name="add-or-edit-namespace-paths"></a>Ad alanı yolları ekleme veya düzenleme

İstemcilerin depolama hedefine erişebilmesi için en az bir ad alanı yolu oluşturmanız gerekir. (İstemci erişimi hakkında daha fazla bilgi için [Azure HPC önbelleğini bağlama](hpc-cache-mount.md) konusunu okuyun.)

Yakın zamanda bir depolama hedefi eklediyseniz veya bir erişim ilkesi özelleştirdiyseniz, bir ad alanı yolu oluşturabilmeniz için bir veya iki dakika sürebilir.

### <a name="blob-namespace-paths"></a>Blob ad alanı yolları

Azure Blob depolama hedefi yalnızca bir ad alanı yoluna sahip olabilir.

Azure portal veya Azure CLı ile yolu ayarlamak veya değiştirmek için aşağıdaki yönergeleri izleyin.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal, **ad alanı** ayarları sayfasını yükleyin. Bu sayfadan ad alanı yollarını ekleyebilir, değiştirebilir veya silebilirsiniz.

* **Yeni bir yol ekleyin:** Üstteki **+ Ekle** düğmesine tıklayın ve düzenleme panelinde bilgileri girin.

  ![Bir BLOB depolama hedefi seçili olan ad alanı Ekle Düzenle alanlarının ekran görüntüsü. Dışarı aktarma ve alt dizin yolları düzenlenemez olarak ayarlanır.](media/namespace-add-blob.png)

  * İstemcilerin bu depolama hedefine erişmek için kullanacağı yolu girin.

  * Bu yol için kullanılacak erişim ilkesini seçin. İstemci erişim [Ilkelerini kullan](access-policies.md)bölümünde istemci erişimini özelleştirme hakkında daha fazla bilgi edinin.

  * Açılan listeden depolama hedefini seçin. BLOB depolama hedefinin zaten bir ad alanı yolu varsa, bu seçilemez.

  * Azure Blob depolama hedefi için, dışa aktarma ve alt dizin yolları otomatik olarak olarak ayarlanır ``/`` .

* **Mevcut bir yolu değiştirin:** Ad alanı yoluna tıklayın. Düzenleme paneli açılır. Yolu ve erişim ilkesini değiştirebilirsiniz, ancak farklı bir depolama hedefine geçiş yapamazsınız.

* **Ad alanı yolunu silme:** Yolun solundaki onay kutusunu seçin ve **Sil** düğmesine tıklayın.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC önbelleği Için Azure CLI 'Yi ayarlayın](./az-cli-prerequisites.md).

Azure CLı kullanırken, depolama hedefini oluştururken bir ad alanı yolu eklemeniz gerekir. Ayrıntılar için [Yeni bir Azure Blob depolama hedefi ekleyin](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-azure-blob-storage-target) makalesini okuyun.

Hedefin ad alanı yolunu güncelleştirmek için [az HPC-Cache blob-Storage-Target Update](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_update) komutunu kullanın. Update komutu için bağımsız değişkenler, kapsayıcı adını veya depolama hesabını geçirmezseniz, Create komutunda bağımsız değişkenlere benzerdir.

Azure CLı ile bir BLOB depolama hedefinden bir ad alanı yolunu silemezsiniz, ancak yolun üzerine farklı bir değer yazabilirsiniz.

---

### <a name="nfs-namespace-paths"></a>NFS ad alanı yolları

Her bir yol aynı depolama sisteminde farklı bir dışarı aktarma veya alt dizini temsil ettiğinde, bir NFS depolama hedefi birden çok sanal yola sahip olabilir.

Bir NFS depolama hedefi için ad alanınızı planlarken, her bir yolun benzersiz olması gerektiğini ve başka bir ad alanı yolunun bir alt dizini olabileceğini aklınızda bulundurun. Örneğin, adlı bir ad alanı yolunuz varsa ``/parent-a`` , ve gibi ad alanı yolları da oluşturamazsınız ``/parent-a/user1`` ``/parent-a/user2`` . Bu dizin yollarına, öğesinin alt dizinleri olarak ad alanında zaten erişilebilir ``/parent-a`` .

Bir NFS depolama sistemi için tüm ad alanı yolları tek bir depolama hedefinde oluşturulur. Çoğu önbellek yapılandırması, depolama hedefi başına en fazla on ad alanı yolunu destekleyebilir, ancak daha büyük konfigürasyonlar 20 ' ye kadar destek sağlayabilir.

Bu liste, yapılandırma başına en fazla ad alanı yolu sayısını gösterir.

* 2 GB/sn aktarım hızı:

  * 3 TB önbellek-10 ad alanı yolu
  * 6 TB önbellek-10 ad alanı yolu
  * 12 TB önbellek-20 ad alanı yolu

* 4 GB/sn aktarım hızı:

  * 6 TB önbellek-10 ad alanı yolu
  * 12 TB önbellek-10 ad alanı yolu
  * 24 TB önbellek-20 ad alanı yolu

* En fazla 8 GB/sn aktarım hızı:

  * 12 TB önbellek-10 ad alanı yolu
  * 24 TB önbellek-10 ad alanı yolu
  * 48 TB önbellek-20 ad alanı yolu

Her NFS ad alanı yolu için, istemciye yönelik yolu, depolama sistemi dışarı aktarmayı ve isteğe bağlı olarak bir dışarı aktarma alt dizinini sağlayın.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal, **ad alanı** ayarları sayfasını yükleyin. Bu sayfadan ad alanı yollarını ekleyebilir, düzenleyebilir veya silebilirsiniz.

* **Yeni bir yol eklemek için:** Üstteki **+ Ekle** düğmesine tıklayın ve düzenleme panelinde bilgileri girin.
* **Varolan bir yolu değiştirmek için:** Ad alanı yoluna tıklayın. Düzenleme paneli açılır ve yolu değiştirebilirsiniz.
* **Bir ad alanı yolunu silmek için:** Yolun solundaki onay kutusunu seçin ve **Sil** düğmesine tıklayın.

Her ad alanı yolu için bu değerleri girin:

* **Ad alanı yolu** -istemciye yönelik dosya yolu.

* **İstemci erişim ilkesi** -bu yol için kullanılacak erişim ilkesini seçin. İstemci erişim [Ilkelerini kullan](access-policies.md)bölümünde istemci erişimini özelleştirme hakkında daha fazla bilgi edinin.

* **Depolama hedefi** -yeni bir ad alanı yolu oluşturuyorsanız, açılan menüden bir depolama hedefi seçin.

* **Dışarı aktarma yolu** -NFS dışarı aktarmanın yolunu girin. Dışarı aktarma adını doğru yazdığınızdan emin olun; Portal bu alanın sözdizimini doğrular ancak değişikliği yapana kadar dışarı aktarmayı denetlemez.

* **Alt dizini dışarı aktar** -bu yolun, dışarı aktarmanın belirli bir alt dizinini takmasına istiyorsanız buraya girin. Aksi takdirde, bu alanı boş bırakın.

![sağ tarafta düzenleme sayfası açık olan Portal ad alanı sayfasının ekran görüntüsü. Düzenleme formu, NFS depolama hedefi ad alanı yolunun ayarlarını gösterir](media/namespace-edit-nfs.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC önbelleği Için Azure CLI 'Yi ayarlayın](./az-cli-prerequisites.md).

Azure CLı kullanırken, depolama hedefini oluştururken en az bir ad alanı yolu eklemeniz gerekir. Ayrıntılar için [yenı NFS depolama hedefi ekleme](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) bölümünü okuyun.

Hedefin ad alanı yolunu güncelleştirmek veya ek yollar eklemek için [az HPC-Cache NFS-Storage-Target Update](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_update) komutunu kullanın. İstediğiniz ``--junction`` ad alanı yollarını belirtmek için seçeneğini kullanın.

Güncelleştirme komutu için kullanılan seçenekler, depolama sistemi bilgilerini (IP adresi veya ana bilgisayar adı) geçirmezseniz ve kullanım modelinin isteğe bağlı olması dışında "Oluştur" komutuna benzer. Seçeneğin sözdizimi hakkında daha fazla bilgi için [yenı NFS depolama hedefi ekleme](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) makalesini okuyun ``--junction`` .

---

### <a name="adls-nfs-namespace-paths-preview"></a>ADLS-NFS ad alanı yolları (ÖNIZLEME)

Normal bir BLOB depolama hedefi gibi, ADLS-NFS depolama hedefinin yalnızca bir dışarı aktarma işlemi bulunur, bu nedenle yalnızca bir ad alanı yolu olabilir.

Yolu Azure portal ayarlamak veya değiştirmek için aşağıdaki yönergeleri izleyin.

**Ad alanı** ayarları sayfasını yükleyin.

* **Yeni bir yol ekleyin:** Üstteki **+ Ekle** düğmesine tıklayın ve düzenleme panelinde bilgileri girin.

  ![Ad alanı Ekle ' nin ekran görüntüsü bir ADLS-NFS depolama hedefi seçiliyken düzenleme alanları. Dışarı aktarma ve alt dizin yolları düzenlenemez olarak ayarlanır.](media/namespace-add-adls.png)

  * İstemcilerin bu depolama hedefine erişmek için kullanacağı yolu girin.

  * Bu yol için kullanılacak erişim ilkesini seçin. İstemci erişim [Ilkelerini kullan](access-policies.md)bölümünde istemci erişimini özelleştirme hakkında daha fazla bilgi edinin.

  * Açılan listeden depolama hedefini seçin. Bir ADLS-NFS depolama hedefinin zaten bir ad alanı yolu varsa, bu seçilemez.

  * Bir ADLS-NFS depolama hedefi için, dışa aktarma ve alt dizin yolları otomatik olarak olarak ayarlanır ``/`` .

* **Mevcut bir yolu değiştirin:** Ad alanı yoluna tıklayın. Düzenleme paneli açılır. Yolu ve erişim ilkesini değiştirebilirsiniz, ancak farklı bir depolama hedefine geçiş yapamazsınız.

* **Ad alanı yolunu silme:** Yolun solundaki onay kutusunu seçin ve **Sil** düğmesine tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Depolama hedeflerinizin toplanmış ad alanını oluşturduktan sonra, istemcileri önbellekte bağlayabilirsiniz. Daha fazla bilgi edinmek için bu makaleleri okuyun.

* [Azure HPC Önbelleği’ni bağlama](hpc-cache-mount.md)
* [Verileri Azure Blob depolamaya taşıma](hpc-cache-ingest.md)
