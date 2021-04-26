---
title: Azure HPC önbellek önkoşulları
description: Azure HPC önbelleğini kullanma önkoşulları
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/14/2021
ms.author: v-erkel
ms.openlocfilehash: 3cddbba3dca64561d7e2b7b27715152a26a8c9e9
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717594"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Azure HPC önbelleği önkoşulları

Yeni bir Azure HPC önbelleği oluşturmak için Azure portal kullanmadan önce ortamınızın bu gereksinimleri karşıladığından emin olun.

## <a name="video-overviews"></a>Video genel bakış

Sistemin bileşenlerine hızlı bir genel bakış ve birlikte çalışmak için gerekenler için bu videoları izleyin.

(Video görüntüsünü veya izlemek için bağlantıyı tıklatın.)

* [Nasıl çalışır?](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/) Azure HPC önbelleğinin depolama ve istemcilerle nasıl etkileşime gireceğini açıklar

  [![video küçük resmi resmi: Azure HPC Cache: nasıl çalıştığı (video sayfasını ziyaret etmek için tıklayın)](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [Önkoşullar](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/) -NAS depolama, Azure Blob depolama, ağ erişimi ve istemci erişimi için gereksinimleri açıklar

  [![video küçük resmi resmi: Azure HPC Cache: Önkoşullar (video sayfasını ziyaret etmek için tıklayın)](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

Belirli öneriler için bu makalenin geri kalanını okuyun.

## <a name="azure-subscription"></a>Azure aboneliği

Ücretli bir abonelik önerilir.

## <a name="network-infrastructure"></a>Ağ altyapısı

Önbelleğinizi kullanabilmeniz için ağ ile ilgili iki önkoşul ayarlanmalıdır:

* Azure HPC önbellek örneği için ayrılmış alt ağ
* Önbelleğin depolamaya ve diğer kaynaklara erişebilmesi için DNS desteği

### <a name="cache-subnet"></a>Önbellek alt ağı

Azure HPC önbelleği, bu kaliteleri olan ayrılmış bir alt ağa ihtiyaç duyuyor:

* Alt ağda en az 64 IP adresi kullanılabilir olmalıdır.
* Alt ağ, istemci makineler gibi ilgili hizmetler için bile diğer VM 'Leri barındıramaz.
* Birden çok Azure HPC önbellek örneği kullanıyorsanız, her birinin kendi alt ağına ihtiyacı vardır.

En iyi yöntem, her önbellek için yeni bir alt ağ oluşturmaktır. Önbellek oluşturmanın bir parçası olarak yeni bir sanal ağ ve alt ağ oluşturabilirsiniz.

### <a name="dns-access"></a>DNS erişimi

Önbelleğin, sanal ağının dışındaki kaynaklara erişmesi için DNS ihtiyacı vardır. Kullandığınız kaynaklara bağlı olarak, özelleştirilmiş bir DNS sunucusu ayarlamanız ve bu sunucu ile Azure DNS sunucuları arasında iletmeyi yapılandırmanız gerekebilir:

* Azure Blob depolama uç noktalarına ve diğer iç kaynaklara erişmek için, Azure tabanlı DNS sunucusuna ihtiyacınız vardır.
* Şirket içi depolamaya erişmek için, depolama ana bilgisayar adlarını çözümleyebilecek özel bir DNS sunucusu yapılandırmanız gerekir. Önbelleği oluşturmadan önce bunu yapmanız gerekir.

Yalnızca blob Storage kullanıyorsanız, önbelleğiniz için Azure tarafından sağlanmış varsayılan DNS sunucusunu kullanabilirsiniz. Ancak, Azure dışındaki depolama veya diğer kaynaklara erişmeniz gerekiyorsa, özel bir DNS sunucusu oluşturmanız ve Azure 'a özgü tüm çözümleme isteklerini Azure DNS sunucusuna iletecek şekilde yapılandırmanız gerekir.

Özel bir DNS sunucusu kullanmak için önbelleğinizi oluşturmadan önce bu kurulum adımlarını gerçekleştirmeniz gerekir:

* Azure HPC önbelleğini barındıracak sanal ağı oluşturun.
* DNS sunucusunu oluşturun.
* DNS sunucusunu önbelleğin sanal ağına ekleyin.

  DNS sunucusunu Azure portal sanal ağa eklemek için aşağıdaki adımları izleyin:

  1. Azure portal sanal ağı açın.
  1. Kenar çubuğu 'ndaki ayarlar menüsünden DNS sunucuları ' nı seçin.
  1. Özel ' i seçin
  1. Alana DNS sunucusunun IP adresini girin.

Basit bir DNS sunucusu, istemci bağlantılarının tüm kullanılabilir önbellek bağlama noktaları arasında yükünü dengelemek için de kullanılabilir.

Azure sanal ağları ve DNS sunucusu konfigürasyonları hakkında daha fazla bilgi [için bkz. Azure sanal ağlardaki kaynaklar Için ad çözümlemesi](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="permissions"></a>İzinler

Önbelleğinizi oluşturmaya başlamadan önce Bu izinle ilgili önkoşulları denetleyin.

* Önbellek örneğinin sanal ağ arabirimlerini (NIC 'ler) oluşturabiliyor olması gerekir. Önbelleği oluşturan kullanıcının, NIC 'ler oluşturmak için abonelikte yeterli ayrıcalıklara sahip olması gerekir.

* BLOB depolama kullanılıyorsa, Azure HPC önbelleğinin depolama hesabınıza erişmesi için yetkilendirmeye ihtiyacı vardır. BLOB depolama verilerinize önbellek erişimi sağlamak için Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) kullanın. İki rol gereklidir: depolama hesabı katılımcısı ve Depolama Blobu veri katılımcısı.

  Rolleri eklemek için [depolama hedefleri ekleme](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) bölümündeki yönergeleri izleyin.

## <a name="storage-infrastructure"></a>Depolama altyapısı
<!-- heading is linked in create storage target GUI as aka.ms/hpc-cache-prereq#storage-infrastructure - make sure to fix that if you change the wording of this heading -->

Önbellek, Azure Blob kapsayıcılarını, NFS donanım depolama dışarı aktarmaları ve NFS bağlı ADLS blob kapsayıcılarını (Şu anda önizlemede) destekler. Önbelleği oluşturduktan sonra depolama hedefleri ekleyin.

Her depolama türünün belirli önkoşulları vardır.

### <a name="blob-storage-requirements"></a>BLOB depolama gereksinimleri

Önbelleğiniz ile Azure Blob depolama 'yı kullanmak istiyorsanız, uyumlu bir depolama hesabı ve boş bir blob kapsayıcısı ya da [verileri Azure Blob depolamaya taşıma](hpc-cache-ingest.md)bölümünde açıklandığı gıbı Azure HPC önbellek biçimli verilerle doldurulmuş bir kapsayıcı gerekir.

> [!NOTE]
> NFS 'e bağlı BLOB depolama için farklı gereksinimler geçerlidir. Ayrıntılar için [ADLS-NFS depolama gereksinimlerini](#nfs-mounted-blob-adls-nfs-storage-requirements-preview) okuyun.

Depolama hedefi eklemeyi denemeden önce hesabı oluşturun. Hedefi eklediğinizde yeni bir kapsayıcı oluşturabilirsiniz.

Uyumlu bir depolama hesabı oluşturmak için şu birleşimlerden birini kullanın:

| Performans | Tür | Çoğaltma | Erişim katmanı |
|--|--|--|--|
| Standart | StorageV2 (genel amaçlı v2)| Yerel olarak yedekli depolama (LRS) veya bölge yedekli depolama (ZRS) | Sık Erişimli |
| Premium | Blok blobları | Yerel olarak yedekli depolama (LRS) | Sık Erişimli |

Depolama hesabına önbelleğinizin özel alt ağından erişilebilir olması gerekir. Hesabınız özel bir uç nokta veya belirli sanal ağlarla kısıtlanmış ortak bir uç nokta kullanıyorsa, önbelleğin alt ağından erişimi etkinleştirdiğinizden emin olun. (Açık bir genel uç nokta önerilmez.)

Önbelleğiniz ile aynı Azure bölgesindeki bir depolama hesabı kullanmak iyi bir uygulamadır.

Ayrıca, yukarıdaki [izinlerle](#permissions)belirtilen şekilde, önbellek uygulamasına Azure depolama hesabınıza erişim izni vermeniz gerekir. Önbelleğe gerekli erişim rollerini sağlamak için [depolama hedefleri ekleme](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) bölümündeki yordamı izleyin. Depolama hesabı sahibi değilseniz, sahibi bu adımı izleyin.

### <a name="nfs-storage-requirements"></a>NFS depolama gereksinimleri
<!-- linked from configuration.md -->

Bir NFS depolama sistemi (örneğin, şirket içi donanım NAS sistemi) kullanıyorsanız, bu gereksinimleri karşıladığından emin olun. Bu ayarları doğrulamak için depolama sisteminizin (veya veri merkezinizdeki) ağ yöneticileri veya güvenlik duvarı yöneticileri ile çalışmanız gerekebilir.

> [!NOTE]
> Önbellekte NFS depolama sistemine yeterli erişim yoksa depolama hedefi oluşturma işlemi başarısız olur.

Daha fazla bilgi için [bkz. NAS yapılandırması ve NFS depolama hedefi sorunları sorunlarını giderme](troubleshoot-nas.md).

* Ağ bağlantısı: Azure HPC önbelleğinin önbellek alt ağı ile NFS sisteminin veri merkezi arasında yüksek bant genişliğine sahip ağ erişimi olması gerekir. [ExpressRoute](../expressroute/index.yml) veya benzer erişim önerilir. VPN kullanıyorsanız, büyük paketlerin engellenmediğinden emin olmak için bunu 1350 adresindeki Clamp TCP yönetim paketleri için yapılandırmanız gerekebilir. VPN ayarları sorunlarını gidermeye yönelik ek yardım için [VPN paket boyutu kısıtlamalarını](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) okuyun.

* Bağlantı noktası erişimi: önbelleğin depolama sisteminizdeki belirli TCP/UDP bağlantı noktalarına erişmesi gerekir. Farklı depolama türlerinin farklı bağlantı noktası gereksinimleri vardır.

  Depolama sisteminizin ayarlarını denetlemek için bu yordamı izleyin.

  * `rpcinfo`Gerekli bağlantı noktalarını denetlemek için depolama sisteminize bir komut verin. Aşağıdaki komut, bağlantı noktalarını listeler ve ilgili sonuçları bir tabloyla biçimlendirir. ( *<storage_IP>* terimi yerıne sisteminizin IP adresini kullanın.)

    Bu komutu, NFS altyapısının yüklü olduğu herhangi bir Linux istemcisinden verebilirsiniz. Küme alt ağı içinde bir istemci kullanırsanız, alt ağ ve depolama sistemi arasındaki bağlantıyı doğrulamaya da yardımcı olabilir.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Sorgu tarafından döndürülen tüm bağlantı noktalarının ``rpcinfo`` Azure HPC önbelleğinin alt ağından sınırsız trafiğe izin verdiğinden emin olun.

  * Komutunu kullanamıyoruz `rpcinfo` , yaygın olarak kullanılan bu bağlantı noktalarının gelen ve giden trafiğe izin verdiğinizden emin olun:

    | Protokol | Bağlantı noktası  | Hizmet  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | dağtd   |
    | TCP/UDP  | 4047  | durum   |

    Bazı sistemler bu hizmetler için farklı bağlantı noktası numaraları kullanır-emin olmak için depolama sisteminizin belgelerine başvurun.

  * Bu gerekli bağlantı noktalarının tümünde trafiğe izin verdiklerinden emin olmak için güvenlik duvarı ayarlarını kontrol edin. Azure 'da kullanılan güvenlik duvarlarını, veri merkezinizdeki şirket içi güvenlik duvarlarını da denetlediğinizden emin olun.

* Kök erişimi (okuma/yazma): önbellek arka uç sistemine Kullanıcı KIMLIĞI 0 olarak bağlanır. Depolama sisteminizde bu ayarları kontrol edin:
  
  * Etkinleştirin `no_root_squash` . Bu seçenek, uzak kök kullanıcının köke ait dosyalara erişebilmesini sağlar.

  * Önbelleğin alt ağından kök erişim kısıtlamalarını dahil olmadıklarından emin olmak için dışarı aktarma ilkelerini denetleyin.

  * Depolama ortamınızda başka bir dışarı aktarmanın alt dizinleri olan dışarı aktarımlar varsa, önbelleğin yolun en düşük kesimine kök erişimi olduğundan emin olun. Ayrıntılar için NFS depolama hedefi sorun giderme makalesindeki [Dizin yollarında kök erişimi](troubleshoot-nas.md#allow-root-access-on-directory-paths) okuyun.

* NFS arka uç depolaması, uyumlu bir donanım/yazılım platformu olmalıdır. Ayrıntılar için Azure HPC önbellek ekibine başvurun.

### <a name="nfs-mounted-blob-adls-nfs-storage-requirements-preview"></a>NFS bağlı blob (ADLS-NFS) depolama gereksinimleri (ÖNIZLEME)

Azure HPC Cache Ayrıca, NFS protokolüyle bağlanmış bir blob kapsayıcısını depolama hedefi olarak kullanabilir.

> [!NOTE]
> Azure Blob depolama için NFS 3,0 protokol desteği genel önizlemeye sunuldu. Kullanılabilirlik kısıtlıdır ve özellik genel kullanıma sunulduğunda özellikler sınırlı olur. Üretim sistemlerinde önizleme teknolojisini kullanmayın.
>
> Bu önizleme özelliği hakkında daha fazla bilgi için bkz. [NFS 3,0 protokol desteği Azure Blob depolamada](../storage/blobs/network-file-system-protocol-support.md).

Depolama hesabı gereksinimleri, bir ADLS-NFS BLOB depolama hedefi ve standart BLOB depolama hedefi için farklıdır. NFS özellikli depolama hesabı oluşturmak ve yapılandırmak için [ağ dosya sistemi (NFS) 3,0 protokolünü dikkatle kullanarak blob Storage 'ı bağlama](../storage/blobs/network-file-system-protocol-support-how-to.md) bölümündeki yönergeleri izleyin.

Bu, adımlara genel bir bakış. Bu adımlar değişebilir, bu nedenle geçerli Ayrıntılar için her zaman [ADLS-NFS yönergelerine](../storage/blobs/network-file-system-protocol-support-how-to.md) başvurun.

1. İhtiyacınız olan özelliklerin çalışmayı planladığınız bölgelerde kullanılabildiğinden emin olun.

1. Aboneliğiniz için NFS Protokolü özelliğini etkinleştirin. Depolama hesabını oluşturmadan *önce* bunu yapın.

1. Depolama hesabı için güvenli bir sanal ağ (VNet) oluşturun. NFS etkinleştirilmiş depolama hesabınız ve Azure HPC önbelleğiniz için aynı sanal ağı kullanmanız gerekir. (Önbelleğiyle aynı alt ağı kullanmayın.)

1. Depolama hesabı oluşturun.

   * Standart BLOB depolama hesabı için depolama hesabı ayarlarını kullanmak yerine, [nasıl yapılır belgesindeki](../storage/blobs/network-file-system-protocol-support-how-to.md)yönergeleri izleyin. Desteklenen depolama hesabı türü, Azure bölgesine göre farklılık gösterebilir.

   * Ağ bölümünde, oluşturduğunuz güvenli sanal ağda özel bir uç nokta seçin (önerilir) veya güvenli VNet 'ten kısıtlı erişimi olan bir genel uç nokta seçin.

   * NFS erişimini etkinleştirdiğiniz Gelişmiş bölümünü tamamlamayı unutmayın.

   * Yukarıdaki [izinlerle](#permissions)belirtilen şekilde, önbellek uygulamasına Azure depolama hesabınıza erişim izni verin. Bunu ilk kez bir depolama hedefi oluşturduğunuzda yapabilirsiniz. Önbelleğe gerekli erişim rollerini sağlamak için [depolama hedefleri ekleme](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) bölümündeki yordamı izleyin.

     Depolama hesabı sahibi değilseniz, sahibi bu adımı izleyin.

Azure HPC cache [ile](nfs-blob-considerations.md)Azure HPC Cache Ile ADLS-NFS depolama hedeflerini kullanma hakkında daha fazla bilgi edinin.

## <a name="set-up-azure-cli-access-optional"></a>Azure CLı erişimi ayarlama (isteğe bağlı)

Azure komut satırı arabirimi 'nden (Azure CLı) Azure HPC önbelleği oluşturmak veya yönetmek istiyorsanız, CLı yazılımını ve HPC-Cache uzantısını yüklemeniz gerekir. [Azure HPC önbelleği Için Azure CLI ayarlama](az-cli-prerequisites.md)bölümündeki yönergeleri izleyin.

## <a name="next-steps"></a>Sonraki adımlar

* Azure portal [Azure HPC önbellek örneği oluşturma](hpc-cache-create.md)
