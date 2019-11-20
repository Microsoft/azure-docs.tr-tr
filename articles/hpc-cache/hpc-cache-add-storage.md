---
title: Azure HPC önbelleğine depolama ekleme
description: Azure HPC önbelleğinizin, uzun süreli dosya depolaması için şirket içi NFS sisteminizi veya Azure Blob kapsayıcılarını kullanabilmesi için depolama hedeflerini tanımlama
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: rohogue
ms.openlocfilehash: 396ed84856604c297551c4593e0d7b82b92ac924
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166647"
---
# <a name="add-storage-targets"></a>Depolama hedefleri ekleme

*Depolama hedefleri* , BIR Azure HPC Cache örneğinden erişilen dosyalar için arka uç depolardır. NFS depolama (Şirket içi donanım sistemi gibi) ekleyebilir veya Azure Blob 'da verileri saklayabilirsiniz.

Tek bir önbellek için en fazla on farklı depolama hedefi tanımlayabilirsiniz. Önbellek, tüm depolama hedeflerini toplanmış bir ad alanında gösterir.

Depolama dışarı aktarımlarının, önbelleğinizin sanal ağından erişilebilir olması gerektiğini unutmayın. Şirket içi donanım depolaması için NFS depolama erişimi için konak adlarını çözebilen bir DNS sunucusu ayarlamanız gerekebilir. [DNS erişiminde](hpc-cache-prereqs.md#dns-access)daha fazla bilgi edinin.

Önbelleğinizi oluşturduktan sonra depolama hedefleri ekleyin. Yordam, Azure Blob depolama veya NFS dışarı aktarma eklediğinize bağlı olarak biraz farklılık gösterebilir. Her biri için Ayrıntılar aşağıda verilmiştir.

## <a name="open-the-storage-targets-page"></a>Depolama hedefleri sayfasını açın

Azure portal, önbellek örneğinizi açın ve sol kenar çubuğundaki **depolama hedefleri** ' ne tıklayın. Depolama hedefleri sayfası, tüm mevcut hedefleri listeler ve yeni bir bağlantı eklemek için bir bağlantı sağlar.

![Kategori başlıkları ayarları ve Izleme arasındaki başlık yapılandırması altında bulunan, alt çubukta depolama hedefleri bağlantısının ekran görüntüsü](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>Yeni bir Azure Blob depolama hedefi ekleme

Yeni bir BLOB depolama hedefi boş bir blob kapsayıcısına veya Azure HPC önbellek bulut dosya sistemi biçimindeki verilerle doldurulmuş bir kapsayıcıya ihtiyaç duyuyor. [Verileri Azure Blob depolamaya taşıma](hpc-cache-ingest.md)bölümünde bir blob kapsayıcısını önceden yükleme hakkında daha fazla bilgi edinin.

Bir Azure Blob kapsayıcısı tanımlamak için bu bilgileri girin.

![Yeni bir Azure Blob depolama hedefi için bilgilerle doldurulmuş depolama hedefi ekleme sayfasının ekran görüntüsü](media/hpc-cache-add-blob.png)

<!-- need to replace screenshot after note text is updated with both required RBAC roles and also with correct search term -->

* **Depolama hedefi adı** -Azure HPC önbelleğinde bu depolama hedefini tanımlayan bir ad ayarlayın.
* **Hedef türü** - **BLOB**seçin.
* **Depolama hesabı** -kullanmak istediğiniz kapsayıcıya sahip hesabı seçin.

  [Erişim rolleri ekleme](#add-the-access-control-roles-to-your-account)bölümünde açıklandığı gibi, depolama hesabına erişmek için önbellek örneğini yetkilendirmeniz gerekecektir.

  Kullanabileceğiniz depolama hesabı türü hakkında daha fazla bilgi için, [BLOB depolama gereksinimlerini](hpc-cache-prereqs.md#blob-storage-requirements)okuyun.

* **Depolama kapsayıcısı** -bu hedefin blob kapsayıcısını seçin.

* **Sanal ad alanı yolu** -bu depolama hedefi için istemciye yönelik dosya yolunu ayarlayın. Sanal ad alanı özelliği hakkında daha fazla bilgi edinmek için [toplanan ad alanını Yapılandır](hpc-cache-namespace.md) makalesini okuyun.

İşiniz bittiğinde, depolama hedefini eklemek için **Tamam** ' ı tıklatın.

> [!NOTE]
> Depolama hesabı güvenlik duvarınız yalnızca "seçili ağlara erişimi kısıtla" olarak ayarlandıysa, [BLOB depolama hesabı güvenlik duvarı ayarlarında çözüm](hpc-cache-blob-firewall-fix.md)olarak belgelenen geçici geçici çözümü kullanın.

### <a name="add-the-access-control-roles-to-your-account"></a>Erişim denetimi rollerini hesabınıza ekleyin

Azure HPC Cache, önbellek hizmetinin Azure Blob depolama hedeflerine yönelik depolama hesabınıza erişmesini yetkilendirmek için [rol tabanlı erişim denetimi 'ni (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) kullanır.

Depolama hesabı sahibi, "HPC Cache kaynak sağlayıcısı" kullanıcısı için rol [depolama hesabı katılımcısı](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) ve [Depolama Blobu veri katılımcısı](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) ' nı açıkça eklemesi gerekir.

Bunu zaman içinde yapabilir veya bir BLOB depolama hedefi eklediğiniz sayfada bir bağlantıya tıklayarak yapabilirsiniz. Rol ayarlarının Azure ortamından yayılması beş dakika sürebileceğini unutmayın. bu nedenle, bir depolama hedefi oluşturmadan önce roller eklendikten sonra birkaç dakika beklemeniz gerekir.

RBAC rolleri ekleme adımları:

1. Depolama hesabı için **erişim denetimi (IAM)** sayfasını açın. ( **Depolama hedefi Ekle** sayfasındaki bağlantı, seçili hesap için otomatik olarak bu sayfayı açar.)

1. Sayfanın üst kısmındaki **+** tıklayın ve **rol ataması Ekle**' yi seçin.

1. Listeden "depolama hesabı katılımcısı" rolünü seçin.

1. **Erişim ata** alanına, varsayılan değeri seçili bırakın ("Azure AD kullanıcısı, Grup veya hizmet sorumlusu").  

1. **Seç** alanında "HPC" ifadesini arayın.  Bu dize, "HPC Cache Resource Provider" adlı bir hizmet sorumlusu ile eşleşmelidir. Bu sorumluyu seçmek için tıklayın.

   > [!NOTE]
   > "HPC" araması işe yaramazsa bunun yerine "storagecache" dizesini kullanmayı deneyin. Önizlemelere katılmış olan (GA 'den önce) kullanıcıların hizmet sorumlusu için eski adı kullanması gerekebilir.

1. Alttaki **Kaydet** düğmesine tıklayın.

1. "Depolama Blobu verileri katılımcısı" rolünü atamak için bu işlemi tekrarlayın.  

![rol ataması Ekle GUI ekran görüntüsü](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>Yeni bir NFS depolama hedefi ekleme

NFS depolama hedefi, BLOB depolama hedefinden daha fazla alan içeriyor. Bu alanlar, depolama dışa aktarmaya nasıl ulaşılmayı ve verilerin nasıl etkili bir şekilde önbelleğe alınacağını belirtir. Ayrıca, NFS ana bilgisayarında birden fazla dışa aktarma işlemi varsa, NFS depolama hedefi birden çok ad alanı yolu oluşturmanıza olanak sağlar.

![NFS hedefi tanımlı depolama hedefi ekleme sayfasının ekran görüntüsü](media/hpc-cache-add-nfs-target.png)

NFS ile desteklenen bir depolama hedefi için şu bilgileri sağlayın:

* **Depolama hedefi adı** -Azure HPC önbelleğinde bu depolama hedefini tanımlayan bir ad ayarlayın.

* **Hedef türü** - **NFS**'yi seçin.

* **Ana bilgisayar** adı-NFS depolama sisteminizin IP adresini veya tam etki alanı adını girin. (Yalnızca önbelleğinizin adı çözebilecek bir DNS sunucusuna erişimi varsa, bir etki alanı adı kullanın.)

* **Kullanım modeli** -aşağıda [bir kullanım modeli seçin](#choose-a-usage-model)bölümünde açıklanan iş akışınızı temel alan veri önbelleğe alma profillerinden birini seçin.

### <a name="nfs-namespace-paths"></a>NFS ad alanı yolları

Her bir yol aynı depolama sisteminde farklı bir dışarı aktarma veya alt dizini temsil ettiğinde, bir NFS depolama hedefi birden çok sanal yola sahip olabilir.

Bir depolama hedefinden tüm yolları oluşturun.

Dilediğiniz zaman, bir depolama hedefinde [ad alanı yolları ekleyebilir ve düzenleyebilirsiniz](hpc-cache-edit-storage.md) .

Her ad alanı yolu için bu değerleri girin:

* **Sanal ad alanı yolu** -bu depolama hedefi için istemciye yönelik dosya yolunu ayarlayın. Sanal ad alanı özelliği hakkında daha fazla bilgi edinmek için [toplanan ad alanını Yapılandır](hpc-cache-namespace.md) makalesini okuyun.

<!--  The virtual path should start with a slash ``/``. -->

* **NFS dışarı aktarma yolu** -NFS dışarı aktarmanın yolunu girin.

* **Alt dizin yolu** -dışarı aktarmanın belirli bir alt dizinini bağlamak istiyorsanız buraya girin. Aksi takdirde, bu alanı boş bırakın.

İşiniz bittiğinde, depolama hedefini eklemek için **Tamam** ' ı tıklatın.

### <a name="choose-a-usage-model"></a>Kullanım modeli seçin
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Bir NFS depolama sistemine işaret eden bir depolama hedefi oluşturduğunuzda, bu hedefin *kullanım modelini* seçmeniz gerekir. Bu model, verilerinizin nasıl önbelleğe alınacağını belirler.

Üç seçenek vardır:

* **Ağır, seyrek erişimli yazmaları okuma** -statik veya nadiren değiştirilen dosyalara okuma erişimini hızlandırmak istiyorsanız bu seçeneği kullanın.

  Bu seçenek, istemcilerin okuyan dosyaları önbelleğe alır, ancak yazma işlemlerini hemen arka uç depolamaya geçirir. Önbellekte depolanan dosyalar hiçbir şekilde NFS depolama birimindeki dosyalarla karşılaştırılmaz.

  Bir dosyanın doğrudan depolama sisteminde önbellekte yazılmadan değiştirilebilmesi için bir risk varsa bu seçeneği kullanmayın. Bu durumda, dosyanın önbelleğe alınmış sürümü arka uçtaki değişikliklerle hiçbir şekilde güncellenmez ve veri kümesi tutarsız hale gelebilir.

* **%15 ' ten fazla yazma** -Bu seçenek hem okuma hem de yazma performansını hızlandırır. Bu seçeneği kullanırken, tüm istemcilerin arka uç depolamayı doğrudan bağlamak yerine Azure HPC Cache aracılığıyla dosyalara erişmesi gerekir. Önbelleğe alınan dosyalar arka uçta depolanmayan son değişikliklere sahip olur.

  Bu kullanım modelinde, önbellekteki dosyalar arka uç depolamadaki dosyalara karşı denetlenmez. Dosyanın önbelleğe alınan sürümünün daha güncel olduğu varsayılır. Önbellekte değiştirilen bir dosya, önbellekte olduktan sonra ek değişiklik yapmadan yalnızca arka uç depolama sistemine yazılır.

* **İstemciler, önbelleği ATLAYARAK NFS hedefine yazar** -iş akışdaki herhangi bir istemci, önce önbelleğe yazmadan verileri doğrudan depolama sistemine yazardıysanız bu seçeneği belirleyin. İstemcilerin istediği dosyalar önbelleğe alınır, ancak istemciden bu dosyalardaki tüm değişiklikler arka uç depolama sistemine hemen geçirilir.

  Bu kullanım modeliyle, önbellekteki dosyalar güncelleştirmeler için arka uç sürümlerine göre sıklıkla denetlenir. Bu doğrulama, verilerin tutarlılığın korunmasında önbelleğin dışında değiştirilmesine izin verir.

Bu tablo, kullanım modeli farklarını özetler:

| Kullanım modeli | Önbelleğe alma modu | Arka uç doğrulaması | En fazla geri yazma gecikmesi |
| ---- | ---- | ---- | ---- |
| Yoğun, seyrek okunan yazma işlemleri | Okuma | hiçbir zaman | None |
| %15 yazma boyutundan büyük | Okuma/yazma | hiçbir zaman | 1 saat |
| İstemcileri önbelleği atlar | Okuma | 30 saniye | None |

## <a name="next-steps"></a>Sonraki adımlar

Depolama hedefleri oluşturduktan sonra şu görevlerden birini göz önünde bulundurun:

* [Azure HPC önbelleğini bağlama](hpc-cache-mount.md)
* [Verileri Azure Blob depolamaya taşıma](hpc-cache-ingest.md)

Herhangi bir ayarı güncelleştirmeniz gerekiyorsa, [bir depolama hedefini düzenleyebilirsiniz](hpc-cache-edit-storage.md).
