---
title: Microsoft Azure FXT Edge Filer kümesine arka uç depolama ekleme
description: Azure FXT Edge Filer için arka uç depolamayı ve istemciye yönelik sözde olmayan sahte donamespace 'i yapılandırma
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: ecc246368cae74440ada782940931b3588193975
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256072"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Öğretici: arka uç depolama ekleme ve sanal ad alanını yapılandırma 

Bu öğreticide, önbelleğiniz için arka uç depolamanın nasıl ekleneceği ve istemciye yönelik sanal FileSystem 'ın nasıl ayarlanacağı açıklanmaktadır. 

Küme, veri istemcileri isteğine erişmek ve değişiklikleri önbellekten daha kalıcı olarak depolamak için arka uç depolama sistemlerine bağlanır. 

Ad alanı, istemci tarafı iş akışlarını değiştirmeden arka uç depolamayı takas etmenize olanak tanıyan, istemciye dönük sahte FileSystem ' dır. 

Bu öğreticide şunları öğreneceksiniz: 

> [!div class="checklist"]
> * Azure FXT Edge Filer kümesine arka uç depolama ekleme 
> * Depolama için istemciye yönelik yolu tanımlama

## <a name="about-back-end-storage"></a>Arka uç depolama hakkında

Azure FXT Edge dosyalayıcı kümesi, bir arka uç depolama sistemini FXT kümesine bağlamak için bir *çekirdek dosyalayıcı* tanımı kullanır.

Azure FXT Edge Filer, birçok popüler NAS donanım sistemiyle uyumludur ve Azure Blob veya diğer bulut depolamadaki boş kapsayıcıları kullanabilir. 

FXT işletim sisteminin, bulut depolama birimindeki tüm verileri tamamen yönetebilmesi için, eklendiğinde, bulut depolama kapsayıcıları boş olmalıdır. Kapsayıcıyı kümeye bir çekirdek taşıma olarak ekledikten sonra, mevcut verilerinizi bulut kapsayıcısına taşıyabilirsiniz.

Sisteminize bir çekirdek filigran eklemek için Denetim Masası 'nı kullanın.

> [!NOTE]
> 
> Amazon AWS veya Google Cloud Storage 'ı kullanmak istiyorsanız, bir FlashCloud<sup>TM</sup> özellik lisansı yüklemelisiniz. Bir lisans anahtarı için Microsoft temsilcinizle iletişim kurun ve ardından [özellik lisansları eklemek veya kaldırmak](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses)için eski yapılandırma kılavuzundaki yönergeleri izleyin.
> 
> Azure Blob depolama desteği, Azure FXT Edge Filer yazılım lisansına dahildir. 

Çekirdek filerleri ekleme hakkında daha ayrıntılı bilgi için, küme yapılandırma kılavuzunun şu bölümlerini okuyun:

* Bir çekirdek filmi eklemek için seçme ve hazırlama hakkında daha fazla bilgi için, bkz. [temel filers Ile çalışma](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview).
* Ayrıntılı Önkoşullar ve adım adım yönergeler için şu makaleleri okuyun:

  * [Yeni bir NAS Core fili ekleme](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Yeni bir bulut çekirdeği ekleme](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

Bir çekirdek fili ekledikten sonra, ayarlarını çekirdek Filer ayrıntıları ayarları sayfasında güncelleştirebilirsiniz.

## <a name="add-a-core-filer"></a>Çekirdek fili ekleme

Çekirdek **filme** > **çekirdek filers ayarlarını yönet** sayfasında **Oluştur** düğmesine tıklayarak bir çekirdek filmi tanımlayın.

![Çekirdek Dosyasıları yönetme sayfasındaki çekirdek dosyasıları listesinin üzerindeki Oluştur düğmesine tıklanın](media/fxt-cluster-config/create-core-filer-button.png)

**Yeni çekirdek fili ekleme** Sihirbazı, arka uç depolama alanınızı bağlayan bir çekirdek film oluşturma sürecinde size yol gösterir. Küme yapılandırma kılavuzunda, işlemin adım adım açıklamaları vardır. Bu, NFS/NAS depolama ve bulut depolama için farklıdır (bağlantılar yukarıda bulunur). 

Alt görevler şunları içerir:

* Çekirdek filleyici (NAS veya bulut) türünü belirtin

  ![Donanım NAS yeni çekirdek dosyalayıcı sihirbazının ilk sayfası. "Cloud Core Filer" seçeneği devre dışı bırakılmıştır ve eksik lisans hakkında bir hata iletisi gösterir.](media/fxt-cluster-config/new-nas-1.png)

* Çekirdek Filer 'ın adını ayarlayın. Küme yöneticilerinin hangi depolama sisteminin temsil ettiğini anlamalarına yardımcı olan bir ad seçin.

* NAS Core filers için tam etki alanı adını (FQDN) veya IP adresini belirtin. FQDN tüm çekirdek dosyasıları için önerilir ve SMB erişimi için gereklidir.

* Önbellek ilkesi seçin-sihirbazın ikinci sayfasında, yeni çekirdek filme için kullanılabilir önbellek ilkeleri listelenir. Ayrıntılar için, [küme yapılandırma kılavuzunun önbellek ilkeleri bölümünü](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html)okuyun. 

  ![Donanım NAS yeni çekirdek filleyici sihirbazının ikinci sayfası; Önbellek Ilkesi açılır menüsü, birkaç devre dışı seçeneği ve üç geçerli önbellek ilkesi seçeneğini (atlama, okuma önbelleği ve okuma/yazma önbelleği) gösteren açıktır.](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* Bulut depolama için, diğer parametrelerin yanı sıra bulut hizmetini ve erişim kimlik bilgilerini belirtmeniz gerekir. Ayrıntılar için, küme yapılandırma kılavuzunda [bulut hizmeti ve protokol](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) makalesini okuyun.

  ![Yeni çekirdek filme sihirbazında bulut çekirdeği dosyalayıcı bilgileri](media/fxt-cluster-config/new-core-filer-cloud3.png) 
  
  Bu küme için bulut erişim kimlik bilgileri zaten eklediyseniz listede görünürler. **@No__t-** 1**bulut kimlik bilgileri** ayarları sayfasında kimlik bilgilerini güncelleştirin ve ekleyin. 

Sihirbazda gerekli tüm ayarları doldurduktan sonra değişikliği göndermek için, **Film Ekle** düğmesine tıklayın.

Birkaç dakika sonra, depolama sistemi **Pano** çekirdeği filtrelerin listesinde görünür ve çekirdek dosyalayıcı ayarları sayfaları aracılığıyla erişilebilir.

![çekirdek filers ayarlarını yönet sayfasında, dosyalayıcı Ayrıntıları görünümü genişletilmiş olan çekirdek dosyalayıcı "Flurry-NAS"](media/fxt-cluster-config/core-filer-in-manage-page.png)

Bu ekran görüntüsündeki çekirdek filme 'de vServer eksik. Çekirdek filmci bir vServer 'a bağlamanız ve istemcilerin depolamaya erişebilmesi için bir birleşim oluşturmanız gerekir. Bu adımlar [, ad alanını yapılandırma](#configure-the-namespace)bölümünde aşağıda açıklanmıştır.

## <a name="configure-the-namespace"></a>Ad alanını yapılandırma

Azure FXT Edge Filer kümesi, farklı arka uç sistemlerinde depolanan verilere istemci erişimini kolaylaştıran *küme ad alanı* adlı bir sanal dosya sistemi oluşturur. İstemciler sanal bir yol kullanarak dosya istebildiğinden, depolama sistemleri istemci iş akışını değiştirmek zorunda kalmadan eklenebilir veya değiştirilebilir. 

Küme ad alanı Ayrıca benzer bir dosya yapısında bulut ve NAS depolama sistemleri sunmanıza olanak tanır. 

Kümenin vservers, ad alanını korur ve istemcilere içerik sağlar. Küme ad alanını oluşturmak için iki adım vardır: 

1. VServer oluştur 
1. Arka uç depolama sistemleri ve istemciye yönelik dosya sistemi yolları arasında ardımları ayarlama 

### <a name="create-a-vserver"></a>VServer oluştur

VServers, istemci ile kümenin çekirdek dosyasıları arasında verilerin nasıl akacağını denetleyen sanal dosya sunucularıdır:

* Sanal sunucular istemciye yönelik IP adreslerini barındırır
* VServers ad alanını oluşturur ve istemciye yönelik sanal dizin yapısını arka uç depolamada dışarı aktarmalar için eşleyen junler tanımlar
* Vservers, çekirdek dosyalayıcı dışa aktarma ilkeleri ve Kullanıcı kimlik doğrulama sistemleri dahil olmak üzere dosya erişim denetimlerini uygular
* Sanal sunucular SMB altyapısı sağlar

Bir küme vServer 'ı yapılandırmaya başlamadan önce, bağlantılı belgeleri okuyun ve ad alanı ve vservers 'ı anlamak için Microsoft temsilcinize başvurun. VLAN 'Lar kullanıyorsanız, vServer 'ı oluşturmadan önce [bunları oluşturun](fxt-configure-network.md#adjust-network-settings) . 

Küme yapılandırma kılavuzunun bu bölümleri, FXT vServer ve genel ad alanı özellikleri hakkında bilgi almanıza yardımcı olur:

* [VServers oluşturma ve bunlarla çalışma](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Genel ad alanı kullanma](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [VServer oluşturma](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

Kümeniz için en az bir vServer gerekir. 

Yeni bir vServer oluşturmak için aşağıdaki bilgilere ihtiyacınız vardır:

* VServer için ayarlanacak ad

* VServer 'ın işleyeceği istemciye yönelik IP adresleri aralığı

  VServer 'ı oluştururken tek bir bitişik IP adresi aralığı sağlamalısınız. Daha sonra, **Istemciye yönelik ağ** ayarları sayfasını kullanarak daha fazla adres ekleyebilirsiniz.

* Ağınızda VLAN 'Lar varsa, bu vServer için kullanılacak VLAN

Yeni bir vServer oluşturmak için **vserver** >  Ile**Vservers ayarlarını yönet** sayfasını kullanın. Ayrıntılar için küme yapılandırma kılavuzunda [bir VServer oluşturma](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) konusunu okuyun. 

![Yeni bir sanal sunucu oluşturmaya yönelik açılır pencere](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Birleşim oluştur

*Birleşim* , bir arka uç depolama yolunu istemci görünür ad alanına eşler.

Bu sistemi, istemci bağlama noktalarında kullanılan yolu basitleştirmek ve tek bir sanal yol birden çok çekirdek dosyasıcından depolamayı barındırabildiğinden kapasiteyi sorunsuz bir şekilde ölçeklendirmek için kullanabilirsiniz.

![Doldurulan ayarlarla yeni birleşim Ekleme Sihirbazı sayfası](media/fxt-cluster-config/add-junction-full.png)

Ad alanı birleşimi oluşturma hakkında tam Ayrıntılar için, küme yapılandırma kılavuzunda [ **vserver** > **ad alanı** ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) ' na bakın.

![Bir birleşimin ayrıntılarını gösteren VServer > ad alanı ayarları sayfası](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Dışarı aktarma kurallarını yapılandırma

Hem vServer hem de bir çekirdek filinize sahip olduktan sonra, istemcilerin çekirdek dosyalayıcı dışarı aktarmaları üzerindeki dosyalara nasıl erişebileceğini denetleyen dışarı aktarma kurallarını ve dışarı aktarma ilkelerini özelleştirmeniz gerekir.

İlk olarak, yeni kurallar eklemek, varsayılan ilkeyi değiştirmek veya kendi özel dışa aktarma ilkenizi oluşturmak için **vServer** > **dışarı aktarma kuralları** sayfasını kullanın.

İkincisi, bu vServer aracılığıyla erişildiğinde, özelleştirilmiş ilkeyi çekirdek filme dışarı aktarmalara uygulamak için **vserver** > **dışarı aktarma ilkeleri** sayfasını kullanın.

Ayrıntılar için [çekirdek Filer dışarı aktarımlarının erişimini denetleyen](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) küme yapılandırma kılavuzu makalesini okuyun.


## <a name="next-steps"></a>Sonraki adımlar

Depolama ekledikten ve istemciye yönelik ad alanını yapılandırdıktan sonra, kümenizin ilk kurulumunu doldurun: 

> [!div class="nextstepaction"]
> [Kümenin Ağ ayarlarını yapılandırma](fxt-configure-network.md)
