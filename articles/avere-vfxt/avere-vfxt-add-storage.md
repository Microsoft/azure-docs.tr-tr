---
title: Avere vFXT Storage 'ı Yapılandırma-Azure
description: Azure için avere vFXT 'nize arka uç depolama sistemi ekleme
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: rohogue
ms.openlocfilehash: 86b63e6d9799387347093e469015fbd3019069d1
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255051"
---
# <a name="configure-storage"></a>Depolama alanını yapılandırma

Bu adım, vFXT kümeniz için bir arka uç depolama sistemi ayarlar.

> [!TIP]
> Avere vFXT kümesiyle birlikte yeni bir Azure Blob kapsayıcısı oluşturduysanız bu kapsayıcı zaten kullanım için ayarlanmıştır ve depolama eklemeniz gerekmez.

Kümeniz ile yeni bir blob kapsayıcısı oluşturmadıysanız veya ek bir donanım ya da bulut tabanlı depolama sistemi eklemek istiyorsanız bu yönergeleri izleyin.

İki ana görev vardır:

1. VFXT kümenizi var olan bir depolama sistemine veya bir Azure Storage hesabına bağlayan [bir çekirdek filme oluşturun](#create-a-core-filer).

1. İstemcilerin bağlanacağı yolu tanımlayan [bir ad alanı kavşak oluşturun](#create-a-junction).

Bu adımlar avere denetim masasını kullanır. Nasıl kullanacağınızı öğrenmek için [vFXT kümesine erişin](avere-vfxt-cluster-gui.md) .

## <a name="create-a-core-filer"></a>Çekirdek filme oluşturma

"Core filme", arka uç depolama sistemi için bir vFXT terimidir. Depolama, NetApp veya ıınon gibi bir donanım NAS gereci olabilir veya bir bulut nesne deposu olabilir. Çekirdek filtrelerin hakkında daha fazla bilgi [için avere küme ayarları kılavuzunda](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers)bulabilirsiniz.

Bir çekirdek fili eklemek için, iki temel tür çekirdek dosyasıcıdan birini seçin:

  * [NAS Core fili](#nas-core-filer) -bir NAS Core filin nasıl ekleneceğini açıklar 
  * [Azure depolama bulut çekirdeği fili](#azure-storage-cloud-core-filer) -bir Azure depolama hesabının bulut çekirdeği filmi olarak ekleneceğini açıklar

### <a name="nas-core-filer"></a>NAS Core fili

Bir NAS Core, bir şirket içi NetApp veya IConnectionPoint ya da buluttaki bir NAS uç noktası olabilir. Depolama sisteminin avere vFXT kümesine güvenilir bir yüksek hızlı bağlantısı olması gerekir (örneğin, bir 1 Gbps ExpressRoute bağlantısı (VPN değil)) ve bu, kullanılmakta olan NAS dışarı aktarmaları için küme kök erişimi vermelidir.

Aşağıdaki adımlar bir NAS Core fili ekler:

1. Avere denetim masasından üstteki **Ayarlar** sekmesine tıklayın.

1. **Çekirdek Filer**@no__t ' a tıklayın. sol taraftaki**çekirdek filers 'yi yönetin** .

1. **Oluştur**’a tıklayın.

   ![Oluştur düğmesinin üzerinde bir imlece yeni çekirdek dosyalayıcı ekleme sayfasının ekran görüntüsü](media/avere-vfxt-add-core-filer-start.png)

1. Sihirbazda gerekli bilgileri girin: 

   * Çekirdek filinizi adlandırın.
   * Varsa tam etki alanı adı (FQDN) sağlayın. Aksi takdirde, çekirdek filize çözümlenen bir IP adresi veya ana bilgisayar adı sağlayın.
   * Listeden dosyalayıcı sınıfınızı seçin. Emin değilseniz, **diğer**' i seçin.

     ![Çekirdek dosyalayıcı adı ve tam etki alanı adına sahip yeni çekirdek film ekleme sayfasının ekran görüntüsü](media/avere-vfxt-add-core-filer.png)
  
   * **İleri** ' ye tıklayın ve bir önbellek ilkesi seçin. 
   * **Filigran Ekle**' ye tıklayın.
   * Daha ayrıntılı bilgi için, avere küme ayarları kılavuzunda [Yeni BIR NAS Core filme ekleme](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) bölümüne bakın.

Sonra, [bir birleşim oluşturmaya](#create-a-junction)devam edin.  

### <a name="azure-storage-cloud-core-filer"></a>Azure depolama bulutu çekirdek fili

VFXT kümenizin arka uç depolama alanı olarak Azure Blob depolamayı kullanmak için, temel bir filme olarak eklemek üzere boş bir kapsayıcıya ihtiyacınız vardır.

> [!TIP] 
> Aynı anda avere vFXT kümesini oluşturduğunuz sırada bir blob kapsayıcısı oluşturmayı seçerseniz, dağıtım şablonu veya betiği bir depolama kapsayıcısı oluşturur, onu bir çekirdek filsi olarak tanımlar ve vFXT kümesi oluşturmanın bir parçası olarak ad alanı birleşimi oluşturur. Şablon, kümenin sanal ağı içinde bir depolama hizmeti uç noktası da oluşturur. 

BLOB depolama alanını kümenize eklemek için şu görevler gereklidir:

* Depolama hesabı oluştur (aşağıdaki 1. adım)
* Boş bir blob kapsayıcısı oluşturun (adım 2-3)
* Depolama erişim anahtarını vFXT kümesine yönelik bir bulut kimlik bilgisi olarak ekleyin (adım 4-6)
* Blob kapsayıcısını vFXT kümesi için bir çekirdek fili olarak ekleyin (adım 7-9)
* İstemcilerin çekirdek filmci bağlamak için kullandığı bir ad alanı birleşimi oluşturma (hem donanım hem de bulut depolaması için aynı olan[bir birleşim oluşturun](#create-a-junction))

Kümeyi oluşturduktan sonra blob depolaması eklemek için aşağıdaki adımları izleyin. 

1. Şu ayarlarla genel amaçlı v2 depolama hesabı oluşturun:

   * **Abonelik** -vFXT kümesiyle aynı
   * **Kaynak grubu** -vFXT küme grubuyla aynı (isteğe bağlı)
   * **Konum** -vFXT kümesiyle aynı
   * **Performans** standardı (Premium Depolama desteklenmez)
   * **Hesap türü** -genel amaçlı v2 (StorageV2)
   * **Çoğaltma** -yerel olarak yedekli depolama (LRS)
   * **Erişim katmanı** -sık erişimli
   * **Güvenli aktarım gerekli** -bu seçeneği devre dışı bırak (varsayılan olmayan değer)
   * **Sanal ağlar** -gerekli değil

   Azure portal kullanabilir veya aşağıdaki "Azure 'a dağıt" düğmesine tıklayabilirsiniz.

   [depolama hesabı oluşturmak için ![düğme](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. Hesap oluşturulduktan sonra depolama hesabı sayfasına gidin.

   ![Azure portal yeni depolama hesabı](media/avere-vfxt-new-storage-acct.png)

1. Genel Bakış sayfasında **Bloblar** ' a tıklayıp **+ Container**' a tıklayarak bir blob kapsayıcısı oluşturun. Herhangi bir kapsayıcı adı kullanın ve erişimin **özel**' e ayarlandığından emin olun.

   ![Mevcut kapsayıcıları olmayan depolama Blobları sayfası](media/avere-vfxt-blob-no-container.png)

1. **Ayarlar**altında **erişim anahtarları** ' na tıklayarak Azure depolama hesabı anahtarını alın:

   ![Anahtarı kopyalamak için Azure portal GUI](media/avere-vfxt-copy-storage-key.png) 

1. Kümeniz için avere denetim masasını açın. **Ayarlar**' a tıklayın ve ardından sol gezinti bölmesinde **küme** > **bulut kimlik bilgileri** ' ni açın. Bulut kimlik bilgileri sayfasında **kimlik bilgisi ekle**' ye tıklayın.

   ![Bulut kimlik bilgileri yapılandırma sayfasında kimlik bilgisi Ekle düğmesine tıklayın](media/avere-vfxt-new-credential-button.png)

1. Bulut çekirdeği fili için bir kimlik bilgisi oluşturmak üzere aşağıdaki bilgileri girin: 

   | Alan | Değer |
   | --- | --- |
   | Kimlik bilgisi adı | herhangi bir açıklayıcı ad |
   | Hizmet türü | (Azure Storage erişim anahtarını seçin) |
   | Kiracı | depolama hesabı adı |
   | Abonelik | abonelik kimliği |
   | Depolama erişim anahtarı | Azure depolama hesabı anahtarı (önceki adımda kopyalanmış) | 

   **Gönder**' e tıklayın.

   ![Avere Denetim Masası 'nda bulut kimlik bilgisi tamamlandı formu](media/avere-vfxt-new-credential-submit.png)

1. Sonra, çekirdek filleyici oluşturun. Avere Denetim Masası ' nın sol tarafında, çekirdek **Filer**@no__t ' a tıklayın,**çekirdek filers 'yi yönetin**. 

1. **Çekirdek filers ayarlarını yönetme** sayfasındaki **Oluştur** düğmesine tıklayın.

1. Sihirbazı doldurun:

   * Dosyalayıcı türü **bulutu**' nı seçin. 
   * Yeni çekirdek filcer ' ı adlandırın ve **İleri**' ye tıklayın.
   * Varsayılan önbellek ilkesini kabul edin ve üçüncü sayfaya devam edin.
   * **Hizmet türü**' nde **Azure Storage**' ı seçin. 
   * Daha önce oluşturulan kimlik bilgisini seçin.
   * **Demet Içeriğini** **boş** olarak ayarla
   * **Sertifika doğrulamasını** **devre dışı** olarak değiştirme
   * **Sıkıştırma modunu** **yok** olarak değiştirme  
   * **İleri**’ye tıklayın.
   * Dördüncü sayfada, **demet adı** ' nda kapsayıcının adını *storage_account_name*/*container_name*olarak girin.
   * İsteğe bağlı olarak, **şifreleme türünü** **hiçbiri**olarak ayarlayın.  Azure depolama, varsayılan olarak şifrelenir.
   * **Filigran Ekle**' ye tıklayın.

   Daha ayrıntılı bilgi için, avere kümesi yapılandırma kılavuzu 'na [Yeni bir bulut çekirdeği ekleme](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) bölümünü okuyun. 

Sayfa yenilenir veya yeni çekirdek filinizi göstermek için sayfayı yenileyebilirsiniz.

Sonra [bir birleşim oluşturmanız](#create-a-junction)gerekir.

## <a name="create-a-junction"></a>Birleşim oluştur

Birleşim, istemciler için oluşturduğunuz bir yoldur. İstemciler yolu bağlayabilir ve seçtiğiniz hedefe ulaşır.

Örneğin, NetApp Core dosyalayıcı `/vol0/data` dışa aktarma ve `/project/resources` alt dizinine eşlemek için `/avere/files` oluşturabilirsiniz.

Junler hakkında daha fazla bilgi [avere kümesi yapılandırma kılavuzunun ad alanı bölümünde](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html)bulunabilir.

Avere Denetim Masası ayarları arabirimindeki şu adımları izleyin:

* Sol üst köşedeki **vServer** > **ad alanı** ' na tıklayın.
* @No__t-0 gibi bir ad alanı yolunu/(eğik çizgi) ile başlayarak belirtin.
* Çekirdek filinizi seçin.
* Çekirdek dosyalayıcı dışarı aktarmayı seçin.
* **İleri**’ye tıklayın.

  ![Birleşim, çekirdek filme ve dışarı aktarma için tamamlanan alanlarla birlikte "yeni birleşim Ekle" sayfasının ekran görüntüsü](media/avere-vfxt-add-junction.png)

Birleşim birkaç saniye sonra görünecektir. Gerektiğinde ek junler oluşturun.

Birleşim oluşturulduktan sonra istemciler, dosya sistemine erişmek için [avere vFXT kümesini bağlayabilir](avere-vfxt-mount-clients.md) .
