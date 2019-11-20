---
title: Microsoft Azure FXT Edge Filer kümesi oluşturma
description: Azure FXT Edge Filsi ile karma depolama önbelleği kümesi oluşturma
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 54d70f60d4b7290b60c864817c756648fef1f481
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256077"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Öğretici: Azure FXT Edge Filer kümesi oluşturma

Önbelleğiniz için Azure FXT Edge Filer donanım düğümlerini yükleyip başlattıktan sonra, önbellek kümesini oluşturmak için FXT küme yazılımını kullanın. 

Bu öğretici, donanım düğümlerinizi bir küme olarak yapılandırma adımlarında size yol gösterir. 

Bu öğreticide şunları öğreneceksiniz: 

> [!div class="checklist"]
> * Kümeyi oluşturmaya başlamadan önce hangi bilgileri gerekir
> * Kümenin yönetim ağı, küme ağı ve istemciye yönelik ağ arasındaki fark
> * Bir küme düğümüne bağlanma 
> * Bir Azure FXT Edge Filer düğümünü kullanarak bir başlangıç kümesi oluşturma
> * Küme ayarlarını yapılandırmak için küme Denetim Masası 'nda oturum açma

Bu yordam, IP adreslerini ve ağ kaynaklarını belirlemek için yapmanız gereken araştırma miktarına bağlı olarak 15 ila 45 dakika arasında sürer.

## <a name="prerequisites"></a>Prerequisites

Bu öğreticiye başlamadan önce bu önkoşulları doldurun:

* Veri merkezinize Azure FXT Edge Filer donanım sistemlerinizi kurma 

  Kümeyi oluşturmak için yalnızca bir düğümünüz olması gerekir, ancak kümeyi yapılandırmadan ve kullanıma hazırlamak için önce [en az iki düğüm eklemeniz](fxt-add-nodes.md) gerekir. 

* Uygun güç ve ağ kablolarını sisteme bağlama  
* En az bir Azure FXT Edge Filer düğümünü açma ve [kök parolasını ayarlama](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>Küme için bilgi toplama 

Azure FXT Edge Filer kümesini oluşturmak için aşağıdaki bilgilere ihtiyacınız vardır:

* Küme adı

* Küme için ayarlanacak yönetici parolası

* IP adresleri:

  * Küme yönetimi için tek bir IP adresi ve yönetim ağı için kullanılacak ağ maskesi ve yönlendirici
  * Küme (düğümden düğüme) iletişim için bitişik bir IP adresleri aralığındaki ilk ve son IP adresleri. Ayrıntılar için aşağıdaki [IP adresi dağıtımına](#ip-address-distribution)bakın. 
  * (İstemciye yönelik IP adresleri küme oluşturulduktan sonra ayarlanır.)

* Ağ altyapısı bilgileri:

  * Küme için bir DNS sunucusunun IP adresi
  * Kümenin DNS etki alanının adı
  * Küme NTP sunucuları için ad veya IP adresi (bir sunucu ya da üç veya daha fazla) 
  * Kümenin arabirimlerinde IEEE 802.1 AX-2008 bağlantı toplamasını etkinleştirmek isteyip istemediğiniz
  * Bağlantı toplamayı etkinleştirirseniz IEEE 802.3 ad (LACP) dinamik toplama kullanılıp kullanılmayacağını belirtir

Kümeyi oluşturduktan sonra bu ağ altyapısı öğelerini yapılandırabilirsiniz, ancak bunu oluşturma sırasında yapmak daha iyidir. 

### <a name="ip-address-distribution"></a>IP adresi dağıtımı

Azure FXT Edge Filer karma depolama önbelleği kümesi, IP adreslerini üç kategoride kullanır:

* Yönetim IP: küme yönetimi için tek bir IP adresi

  Bu adres, küme yapılandırma yardımcı programlarına (Web tabanlı Denetim Masası veya XML-RPC API) erişmek için giriş noktası görevi görür. Bu adres otomatik olarak kümedeki birincil düğüme atanır ve birincil düğüm değişirse otomatik olarak geçer.

  Diğer IP adresleri, denetim masasına erişmek için kullanılabilir ancak yönetim IP adresi, tek tek düğümlerin yük devri durumunda bile erişim sağlamak için tasarlanmıştır.

* Küme ağı: küme iletişimi için bir IP adresi aralığı

  Küme ağı, küme düğümleri arasındaki iletişim için ve arka uç depolamadan (çekirdek filers) dosyaları almak için kullanılır.

  **En iyi uygulama:** Her Azure FXT Edge Filer düğümünde küme iletişimi için kullanılan fiziksel bağlantı noktası başına bir IP adresi ayırın. Küme otomatik olarak belirtilen aralıktaki adresleri ayrı düğümlere atar.

* İstemciye yönelik ağ: istemcilerin dosya isteme ve yazma için kullandığı IP adresleri aralığı

  İstemci ağ adresleri, istemciler tarafından, çekirdek filme verilerine küme aracılığıyla erişmek için kullanılır. Örneğin, bir NFS istemcisi bu adreslerden birini bağlayabilir.

  **En iyi uygulama:** Her bir FXT serisi düğümünde istemci iletişimi için kullanılan fiziksel bağlantı noktası başına bir IP adresi ayırın.

  Küme, istemci ile ilgili IP adreslerini bileşen düğümleri arasında mümkün olduğunca eşit şekilde dağıtır.

  Kolaylık olması için birçok yönetici, istemci isteklerinin adres aralığı genelinde dağıtılmasını kolaylaştırmak için hepsini bir kez deneme DNS (RRDNS) yapılandırmasıyla tek bir DNS adı yapılandırır. Bu kurulum, tüm istemcilerin kümeye erişmek için aynı Mount komutunu kullanmasına de olanak sağlar. Daha fazla bilgi için [DNS yapılandırma](fxt-configure-network.md#configure-dns-for-load-balancing) makalesini okuyun.

Yeni bir küme oluşturmak için yönetim IP adresi ve bir küme ağ adresi aralığı belirtilmelidir. İstemciye yönelik adresler küme oluşturulduktan sonra belirtilir.

## <a name="connect-to-the-first-node"></a>İlk düğüme Bağlan

Yüklü FXT düğümlerine bağlanabilir ve kümeyi ayarlamak için işletim sistemi yazılımını kullanabilirsiniz.

Daha önce yapmadıysanız, kümenizin en az bir kısmını ve bir ağ bağlantısına ve bir IP adresine sahip olduğundan emin olun. Düğümü etkinleştirmek için yeni bir kök parolası ayarlamanız gerekir, bu nedenle daha önce yapmadıysanız, [donanım parolalarını ayarlama](fxt-node-password.md) bölümündeki adımları uygulayın.

Ağ bağlantısını denetlemek için, düğümün ağ bağlantısı LED 'Leri 'nin aydınlatma olduğundan emin olun (ve gerekirse ağ anahtarındaki göstergeler). Gösterge LED 'Leri, [Azure FXT Edge Filer donanım durumunu izle](fxt-monitor.md)bölümünde açıklanmıştır.

Düğüm önyüklendiğinde bir IP adresi ister. Bir DHCP sunucusuna bağlıysa, DHCP tarafından sağlanmış IP adresini kabul eder. (Bu IP adresi geçicidir. Kümeyi oluşturduğunuz zaman değişecektir.)

Bir DHCP sunucusuna bağlı değilse veya yanıt almazsa, düğüm, 169,254 biçiminde bir otomatik olarak atanan IP adresi ayarlamak için Bonjour yazılımını kullanır. \*. \*. Ancak, bir küme oluşturmak için kullanmadan önce düğümün ağ kartlarının birinde geçici bir statik IP adresi ayarlamanız gerekir. Yönergeler bu eski belgeye dahildir; güncel bilgiler için Microsoft hizmetine ve desteğe başvurun: [Ek A: BIR FXT düğümünde STATIK IP adresi ayarlama](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>IP adresini bulma

Azure FXT Edge Filer düğümüne bağlanarak IP adresini bulun. Seri kablo kullanabilir, USB ve VGA bağlantı noktalarına doğrudan bağlantı veya bir KVM anahtarıyla bağlanabilirsiniz. (Bağlantı noktası bağlantı ayrıntıları için bkz. [Başlangıç parolalarını ayarlama](fxt-node-password.md).)

Bağlandıktan sonra, Kullanıcı adı `root` ile oturum açın ve düğümü ilk kez önyüklerken ayarladığınız parolayı belirleyin.  

Oturum açtıktan sonra, düğümün IP adresini belirlemeniz gerekir.

Bu sisteme atanan adresleri görmek için `ifconfig` komutunu kullanın.

Örneğin, `ifconfig | grep -B5 inet` komutu internet adresleriyle bağlantı noktalarını arar ve bağlantı noktası tanımlayıcısını göstermek için beş bağlam satırı sağlar.

İfconfig raporunda gösterilen tüm IP adreslerini yazın. E0A veya e0b gibi bağlantı noktası adlarıyla listelenen adresler iyi seçeneklerdir. Bu adlar yalnızca Idrac/ıPMı hizmeti bağlantı noktalarında kullanıldığından, E7 * adlarıyla listelenen herhangi bir IP adresini kullanmayın.  

## <a name="load-the-cluster-configuration-wizard"></a>Küme yapılandırma Sihirbazı 'nı yükleme

Kümeyi oluşturmak için tarayıcı tabanlı küme yapılandırma aracını kullanın. 

Düğümün IP adresini bir Web tarayıcısına girin. Tarayıcı güvenilmeyen siteyle ilgili bir ileti veriyorsa yine de siteye devam edin. (Tekil Azure FXT Edge Filer düğümlerinde CA tarafından sağlanmış güvenlik sertifikaları yoktur.)

![Tarayıcı penceresinde Denetim Masası oturum açma sayfası](media/fxt-cluster-create/unconfigured-node-gui.png)

**Kullanıcı adı** ve **parola** alanlarını boş bırakın. Küme oluşturma sayfasını yüklemek için **oturum aç** ' a tıklayın.

![Tarayıcı tabanlı GUI denetim masasında yapılandırılmamış bir düğüm için ilk kurulum ekranı. Yazılım güncelleştirme, bir kümeyi el ile yapılandırma veya bir kümeyi Kurulum dosyasından yapılandırma seçeneklerini gösterir.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>Kümeyi oluşturma

Küme yapılandırma aracı, Azure FXT Edge Filer kümesini oluşturmaya yönelik bir ekran kümesi boyunca size rehberlik eder. Başlamadan önce [gerekli bilgilerin](#gather-information-for-the-cluster) bulunduğundan emin olun. 

### <a name="creation-options"></a>Oluşturma seçenekleri

İlk ekran üç seçenek sunar. Destek personeline ait özel yönergelerden bazıları yoksa el ile yapılandırma seçeneğini kullanın.

Yeni küme yapılandırma seçenekleri ekranını yüklemek için **kümeyi el ile yapılandıracağım** ' ı tıklatın. 

Diğer seçenekler nadiren kullanılır:

* "Sistem görüntüsünü güncelleştirme", kümeyi oluşturmadan önce yeni işletim sistemi yazılımı yüklemenizi ister. (Yüklü olan yazılım sürümü ekranın en üstünde listelenir.) Yazılım paketi dosyasını bir URL ve Kullanıcı adı/parola sağlamanız gerekir ya da bilgisayarınızdan bir dosya karşıya yükleyin. 

* Küme kurulum dosyası seçeneği bazen Microsoft Müşteri Hizmetleri ve desteği tarafından kullanılır. 

## <a name="cluster-options"></a>Küme seçenekleri

Sonraki ekranda, yeni küme için seçenekleri yapılandırmanız istenir.

Sayfa, **temel yapılandırma** ve **ağ yapılandırması**olmak üzere iki ana bölüme ayrılmıştır. Ağ yapılandırma bölümünün alt bölümleri de vardır: bir tane **Yönetim** ağı ve diğeri **küme** ağı için.

### <a name="basic-configuration"></a>Temel yapılandırma

Üstteki bölümde, yeni küme için temel bilgileri girin.

![Tarayıcı GUI sayfasındaki "temel yapılandırma" bölümünün ayrıntısı. Üç alanı gösterir (küme adı, yönetici parolası, parolayı onaylayın)](media/fxt-cluster-create/basic-configuration.png) 

* **Küme adı** -küme için benzersiz bir ad girin.

  Küme adı şu ölçütlere uymalıdır:
  
  * 1 ile 16 karakter arasında uzunluk
  * Harfler, rakamlar ve çizgi (-) ve alt çizgi (_) karakterlerini içerebilir 
  * Diğer noktalama işaretleri veya özel karakterler içermemelidir
  
  Bu adı daha sonra **küme** > **genel kurulum** yapılandırma sayfasında değiştirebilirsiniz. (Küme ayarları hakkında daha fazla bilgi için, bu belge kümesinin bir parçası olmayan [küme yapılandırma kılavuzunu](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html)okuyun.)

  > [!NOTE] 
  > Küme adınız, izleme veya sorun giderme için desteğe yüklenen sistem bilgilerini belirlemek için kullanılır, bu nedenle şirketinizin adını eklemek yararlı olur.

* **Yönetici parolası** -varsayılan yönetici kullanıcı için parolayı ayarlayın `admin`.
  
  Kümeyi yöneten her kişi için bireysel kullanıcı hesapları ayarlamanız gerekir, ancak `admin` kullanıcısını kaldıramazsınız. Ek kullanıcılar oluşturmanız gerekiyorsa `admin` olarak oturum açın.
 
  Küme Denetim Masası 'ndaki **yönetim** > **kullanıcıları** ayarları sayfasında `admin` parolasını değiştirebilirsiniz. Ayrıntılar için, [küme yapılandırma kılavuzundaki](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html) **Kullanıcılar** belgelerini okuyun.

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Ağ yapılandırması

**Ağ** bölümü, kümenin kullanacağı ağ altyapısını belirtmenizi ister. 

Yapılandırılacak iki ayrı ağ vardır:

* *Yönetim ağı* , yapılandırma ve izleme için kümeye yönetici erişimi sağlar. Burada belirtilen IP adresi, Denetim Masası 'na veya SSH erişimine bağlanırken kullanılır. 

  Çoğu küme yalnızca tek bir yönetim IP adresi kullanır, ancak arabirim eklemek istiyorsanız Kümeyi oluşturduktan sonra bunu yapabilirsiniz.

* Küme *ağı* , küme düğümleri ve küme düğümleri ile arka uç depolama (çekirdek filers) arasında iletişim için kullanılır.

İstemciye yönelik ağ daha sonra, küme oluşturulduktan sonra yapılandırılır.

Bu bölüm ayrıca her iki ağ tarafından kullanılan DNS ve NTP sunucuları için yapılandırmayı içerir.

### <a name="configure-the-management-network"></a>Yönetim ağını yapılandırma

**Yönetim** bölümündeki ayarlar, kümeye yönetici erişimi sağlayan ağ içindir.

![5 seçenek alanları ve 1 GB yönetim ağı için bir onay kutusu ile "Yönetim" bölümünün ayrıntısı](media/fxt-cluster-create/management-network-filled.png)

* **YÖNETIM IP** -küme Denetim Masası 'na erişmek IÇIN kullanacağınız IP adresini belirtin. Bu adres kümenin birincil düğümü tarafından talep edilir, ancak özgün birincil düğüm kullanılamaz hale gelirse otomatik olarak sağlıklı bir düğüme geçer.

  Çoğu küme yalnızca bir yönetim IP adresi kullanır. Birden fazla istiyorsanız, küme  > **Yönetim ağı** ayarları sayfasını **kullanarak kümeyi oluşturduktan**sonra bunları ekleyebilirsiniz. [Küme yapılandırma kılavuzunda](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html)daha fazla bilgi edinin.

* **Ağ maskesi** -yönetim ağı için ağ maskesini belirtin.

* **Yönlendirici** -yönetim ağı tarafından kullanılan varsayılan ağ geçidi adresini girin.

* **VLAN etiketi (isteğe bağlı)** -kümeniz VLAN etiketleri kullanıyorsa, yönetim ağının etiketini belirtin.

  Ek VLAN ayarları, **küme** > **VLAN** ayarları sayfasında yapılandırılır. Daha fazla bilgi edinmek için, küme yapılandırma kılavuzundaki VLAN ve [cluster > VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) [ile çalışma](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) bölümünü okuyun.

* **MTU** -gerekirse, kümenizin yönetim ağı için en yüksek iletim BIRIMINI (MTU) ayarlayın.

* **1 GB MGMT ağ kullanın** -FXT düğümlerinizin Iki 1GbE ağ bağlantı noktasını yalnızca yönetim ağına atamak istiyorsanız bu kutuyu işaretleyin. (Diğer tüm trafik için kullanılabilir 25 GbE/10GbE bağlantı noktası olması gerekir.) Bu kutuyu görmüyorsanız, yönetim ağı kullanılabilir en yüksek hız bağlantı noktasını kullanır. 

### <a name="configure-the-cluster-network"></a>Küme ağını yapılandırma 

Küme ağ ayarları, küme düğümleri arasındaki trafik için ve küme düğümleri ile çekirdek filers arasında geçerlidir.

![altı değer girmeye yönelik alanlarla birlikte "Cluster" bölümünün ayrıntısı](media/fxt-cluster-create/cluster-network-filled.png)

* **Ilk IP** ve **son IP** -iç küme iletişimi IÇIN kullanılacak aralığı tanımlayan IP adreslerini girin. Burada kullanılan IP adresleri, DHCP tarafından ayrılmamış ve atanmamış olmalıdır.

  Kümeyi oluşturduktan sonra daha fazla IP adresi ekleyebilirsiniz. **@No__t-** 1**küme ağları** ayarları sayfasını kullanın ([küme yapılandırma kılavuzu belgeleri](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)).

  **Aralıktaki IP sayısı** değeri hesaplanır ve otomatik olarak gösterilir.

* **MGMT dışı ağ maskesi (isteğe bağlı)** -küme ağı için ağ maskesini belirtin. 

  Sistem otomatik olarak yönetim ağı için girdiğiniz ağ maskesi değerini önerir; gerekirse değiştirin.

* **Küme yönlendiricisi (isteğe bağlı)** -küme ağı tarafından kullanılan varsayılan ağ geçidi adresini belirtin. 

  Sistem otomatik olarak yönetim ağı için sağladığınız ağ geçidi adresini önerir.

* **Küme VLAN etiketi (isteğe bağlı)** -kümeniz VLAN etiketleri kullanıyorsa, küme ağının etiketini belirtin.

* **MGMT olmayan MTU (isteğe bağlı)** -gerekirse, küme ağınız için en yüksek iletim BIRIMINI (MTU) ayarlayın.

### <a name="configure-cluster-dns-and-ntp"></a>Küme DNS ve NTP yapılandırma 

**Küme** bölümünün altında, DNS ve NTP sunucularını belirtmek ve bağlantı toplamayı etkinleştirmek için alanlar vardır. Bu ayarlar, kümenin kullandığı tüm ağlar için geçerlidir.

![DNS/NTP Yapılandırması bölümünün ayrıntıları, DNS sunucuları için üç alan, DNS etki alanı ve DNS arama alanları, NTP sunucuları için üç alan ve bağlantı toplama seçenekleri için açılan bir menü](media/fxt-cluster-create/dns-ntp-filled.png)

* **DNS sunucuları** -bir veya daha fazla etki alanı adı SISTEMI (DNS) sunucusunun IP adresini girin.

  DNS, tüm kümeler için önerilir ve SMB, AD veya Kerberos kullanmak istiyorsanız gereklidir. 
  
  En iyi performansı elde etmek için, kümenin DNS sunucusunu [Azure FXT Edge Filer kümesi IÇIN DNS yapılandırma](fxt-configure-network.md#configure-dns-for-load-balancing)başlığı altında açıklandığı şekilde, hepsini bir kez deneme yük dengelemesi için yapılandırın.

* **DNS etki alanı** -kümenin kullanacağı ağ etki alanı adını girin.

* **DNS araması** -isteğe bağlı olarak, sistemin DNS sorgularını çözümlemek için arama gereken ek etki alanı adlarını girin. Boşluklarla ayırarak altı adede kadar etki alanı adı ekleyebilirsiniz.

* **NTP** sunucuları-belirtilen alanlarda bir veya üç ağ zaman Protokolü (NTP) sunucusu belirtin. Ana bilgisayar adları veya IP adresleri kullanabilirsiniz.

* **Bağlantı toplama** bağlantı toplaması, küme FXT düğümlerinde Ethernet bağlantı noktalarının çeşitli trafik türlerini nasıl işleyeceğini özelleştirmenize olanak tanır. Daha fazla bilgi edinmek için, küme yapılandırma kılavuzunda [bağlantı toplamayı](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) okuyun.

### <a name="click-the-create-button"></a>Oluştur düğmesine tıklayın

Formdaki tüm gerekli ayarları doğruladıktan sonra, **küme oluştur** düğmesine tıklayın.

![sağ alt köşedeki oluştur düğmesinin üzerinde imleç ile tamamlanan formun altı](media/fxt-cluster-create/create-cluster.png)

Sistem, kümeyi oluştururken bir ileti görüntüler.

![tarayıcıda küme yapılandırması durum iletisi: "FXT düğümü artık kümeyi oluşturuyor. Bu işlem birkaç dakika sürer. Küme oluşturulduğunda, yapılandırmayı gerçekleştirmek için bu bağlantıyı ziyaret edin. " "Bu bağlantıyı ziyaret et" adresindeki köprü](media/fxt-cluster-create/creating-message.png)

Birkaç dakika sonra, küme Denetim Masası 'na gitmek için iletideki bağlantıya tıklayabilirsiniz. (Bu bağlantı sizi **Yönetim IP 'si**IÇINDE belirttiğiniz IP adresine yönlendirir.) Oluştur düğmesine tıkladıktan sonra bağlantı etkin hale gelmesi için 15 saniye boyunca bir dakika sürer. Web arabirimi yüklenmezse birkaç saniye bekleyip bağlantıyı yeniden tıklatın. 

Küme oluşturma bir dakika veya daha fazla zaman alır, ancak işlem devam ederken Denetim Masası 'nda oturum açabilirsiniz. Denetim Masası Pano sayfasının, küme oluşturma işlemi bitene kadar uyarıları göstermesi normaldir. 

## <a name="open-the-settings-pages"></a>Ayarlar sayfalarını açın 

Kümeyi oluşturduktan sonra, ağınızın ve iş akışınızın yapılandırmasını özelleştirmeniz gerekir. 

Yeni kümenizi ayarlamak için Denetim Masası web arabirimini kullanın. Küme oluşturma durumu ekranınızdan bağlantıyı izleyin veya kümede ayarladığınız yönetim IP adresine gidin.

Web arabiriminde `admin` Kullanıcı adı ve kümeyi oluştururken ayarladığınız parolayla oturum açın.

![Denetim Masası oturum açma alanlarını gösteren Web tarayıcısı](media/fxt-cluster-create/admin-login.png)

Denetim Masası açılır ve **Pano** sayfasını gösterir. Küme oluşturma işlemi tamamlandığında, tüm uyarı iletileri ekranda temizlenmelidir.

Kümeyi yapılandırmak için **Ayarlar** sekmesine tıklayın.

**Ayarlar** sekmesinde, sol kenar çubuğu yapılandırma sayfalarının bir menüsünü gösterir. Sayfalar kategoriye göre düzenlenir. Tek tek sayfaları genişletmek veya gizlemek için kategori adının en üstündeki + veya-Control öğesine tıklayın.

![Küme > genel kurulum sayfası yüklü olan denetim masası 'nın (tarayıcıda) ayarlar sekmesi](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>Küme kurulum adımları

İşlemin bu noktasında kümeniz var, ancak yalnızca bir düğüme sahip, istemciye yönelik IP adresi yok ve arka uç depolama alanı yok. Yeni oluşturulan bir kümeden, iş akışınızı işlemeye hazırlanmaya yönelik bir önbellek sistemine gitmek için ek kurulum adımları gerekir.

### <a name="required-configuration"></a>Gerekli yapılandırma

Bu adımlar çoğu veya tüm kümeler için gereklidir. 

* Kümeye düğüm ekleme 

  Üç düğüm standarttır, ancak birçok üretim kümesi en fazla 24 düğüme sahiptir.

  Diğer Azure FXT Edge filigran birimleri kümenize nasıl ekleneceğini ve yüksek kullanılabilirliği nasıl sağlayacağınızı öğrenmek için [küme düğümleri ekleme](fxt-add-nodes.md) ' yi okuyun.

* Arka uç depolamayı belirtme

  Kümenin kullanacağı her bir arka uç depolama sistemi için *çekirdek dosyalayıcı* tanımları ekleyin. Daha fazla bilgi edinmek için [arka uç depolama ekleme ve sanal ad alanını yapılandırma](fxt-add-storage.md#about-back-end-storage) makalesini okuyun.

* İstemci erişimini ve sanal ad alanını ayarlama 

  En az bir sanal sunucu (vServer) oluşturun ve istemci makinelerin kullanması için bir IP adresi aralığı atayın. Ayrıca, arka uç depolama dışarı aktarmaları sanal yollara eşlemenizi sağlayan bir sanal dosya sistemi özelliği olan küme ad alanını (bazen genel ad alanı veya GNS olarak adlandırılır) yapılandırmanız gerekir. Küme ad alanı, arka uç depolama medyasını değiştirseniz bile istemcilere tutarlı ve erişilebilir bir dosya sistemi yapısı sağlar. Ad alanı Ayrıca, Azure Blob kapsayıcıları veya desteklenen diğer bulut nesne depolama alanı için Kullanıcı dostu bir sanal depolama hiyerarşisi sağlayabilir.

  Ayrıntılar için [ad alanını yapılandırma](fxt-add-storage.md#configure-the-namespace) makalesini okuyun. Bu adım şunları içerir:
  * Vservers oluşturma
  * İstemci ağ görünümü ve arka uç depolama arasında junler ayarlama 
  * Her vServer tarafından hangi istemci IP adreslerinin sunulduğunu tanımlama

  > [!Note] 
  > Kümenin GNS 'i ayarlamaya başlamadan önce önemli planlama yapmanız önerilir. Yardım için, [genel ad alanı kullanma](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) ve küme yapılandırma kılavuzundaki [Vservers ile çalışma](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) başlıklı bölümleri okuyun.

* [Ağ ayarlarını ayarla](fxt-configure-network.md)

  Yeni bir küme için doğrulanması veya özelleştirilmesi gereken ağla ilgili birkaç ayar vardır. Şu öğeler hakkındaki ayrıntılar için [ağ ayarlarını ayarla](fxt-configure-network.md) bölümünü okuyun:

  * DNS ve NTP yapılandırması doğrulanıyor 
  * Gerekirse dizin hizmetlerini yapılandırma 
  * VLAN 'Ları ayarlama
  * Proxy sunucularını yapılandırma
  * Küme ağına IP adresleri ekleme
  * Şifreleme sertifikalarını depolama

* [Destek izlemeyi ayarlama](#enable-support)

  Yapılandırma aracı için gizlilik ilkesini kabul etmeniz ve desteklenen yükleme ayarlarını aynı anda yapılandırmanız gerekir.

  Küme, istatistik ve hata ayıklama dosyaları da dahil olmak üzere kümeniz hakkında sorun giderme verilerini otomatik olarak karşıya yükleyebilir. Bu karşıya yüklemeler, Microsoft müşteri hizmeti ve destek 'in olası en iyi hizmeti sağlamasına imkan tanır. Nelerin izleneceğini özelleştirebilir ve isteğe bağlı olarak proaktif destek ve Uzaktan sorun giderme hizmetini etkinleştirebilirsiniz.  

### <a name="optional-configuration"></a>İsteğe bağlı yapılandırma

Bu adımlar tüm kümeler için gerekli değildir. Bazı iş akışı türleri veya belirli küme yönetimi stilleri için bunlar gereklidir. 

* Düğüm ayarlarını özelleştirme

  Küme genelinde düğüm adlarını ayarlayabilir ve düğüm ıPMı bağlantı noktalarını yapılandırabilir veya tek tek oluşturabilirsiniz. Kümeye düğüm eklemeden önce bu ayarları yapılandırırsanız, yeni düğümler, katılırsanız ayarları otomatik olarak alabilir. Seçenekler, eski küme oluşturma belgesinde [düğüm ayarlarını özelleştirme](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html)bölümünde açıklanmıştır.

  > [!TIP]
  > Bu ürün için bazı belgeler Microsoft Azure belge sitesinde henüz kullanılamamaktadır. Küme [yapılandırma kılavuzunun](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) ve [küme oluşturma kılavuzunun](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) eski sürümünün bağlantıları sizi, GitHub 'da barındırılan ayrı bir Web sitesine götürür. 

* SMB 'yi yapılandırma

  Ayrıca, kümenize SMB erişimine izin vermek istiyorsanız, SMB 'yi yapılandırmanız ve açmanız gerekir. SMB (bazen CIFS olarak adlandırılır), genellikle Microsoft Windows istemcilerini desteklemek için kullanılır.

  SMB planlaması ve yapılandırılması, Denetim Masası 'nda birkaç düğmeye tıklanmasından daha fazlasını içerir. SMB, sisteminizin gereksinimlerine bağlı olarak, çekirdek filers 'yi nasıl tanımlayacağınızı, kaç tane sanal sunucu oluşturacağınız, birleşme ve ad alanınızı, erişim izinlerini ve diğer ayarları nasıl yapılandıracağınızı etkileyebilir.

  Daha fazla bilgi için, [SMB erişimi yapılandırma](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html) bölümünde küme yapılandırma Kılavuzu ' nu okuyun.

* Ek lisanslar yüklemesi

  Azure Blob dışında bir bulut depolama alanı kullanmak istiyorsanız, ek bir özellik lisansı yüklemelisiniz. Bir FlashCloud<sup>TM</sup> lisansı satın alma hakkındaki ayrıntılar için Microsoft temsilcinize başvurun. Ayrıntılar [arka uç depolama ekleme ve sanal ad alanı yapılandırma](fxt-add-storage.md#about-back-end-storage)bölümünde açıklanmaktadır.


### <a name="enable-support"></a>Desteği etkinleştir

Azure FXT Edge Filer kümesi, kümeniz hakkında Destek verilerini otomatik olarak karşıya yükleyebilir. Bu karşıya yüklemeler, personelin olası en iyi müşteri hizmetini sağlamasına imkan tanır.

Destek karşıya yüklemelerini ayarlamak için bu adımları izleyin.

1. **Küme** > **destek** ayarları sayfasına gidin. Gizlilik ilkesini kabul edin. 

   ![Gizlilik ilkesini kabul etmek için denetim masasını ve onay düğmesini içeren açılır pencereyi gösteren ekran görüntüsü](media/fxt-cluster-create/fxt-privacy-policy.png)

1. Bölümü genişletmek için **Müşteri bilgisinin** solundaki üçgeni tıklatın.
1. **Karşıya yükleme bilgilerini yeniden doğrula** düğmesine tıklayın.
1. **Benzersiz küme adında** kümenin destek adını ayarlayın-personeli desteklemek için kümenizi benzersiz şekilde tanımladığından emin olun.
1. **Istatistik izleme**, **genel bilgiler karşıya yükleme**ve **kilitlenme bilgileri karşıya yükleme**kutularını işaretleyin.
1. **Gönder**' e tıklayın.  

   ![Destek ayarları sayfasının tamamlanan müşteri bilgisi bölümünün bulunduğu ekran görüntüsü](media/fxt-cluster-create/fxt-support-info.png)

1. Bölümü genişletmek için **güvenli proaktif destek (SPS)** sol tarafındaki üçgeni tıklatın.
1. **SPS 'Yi etkinleştir bağlantısının**kutusunu işaretleyin.
1. **Gönder**' e tıklayın.

   ![Destek ayarları sayfasında tamamlanmış güvenli proaktif destek bölümünün bulunduğu ekran görüntüsü](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>Sonraki adımlar

Temel Kümeyi oluşturduktan ve gizlilik ilkesini kabul ettikten sonra, küme düğümlerinin geri kalanını ekleyin. 

> [!div class="nextstepaction"]
> [Küme düğümleri Ekle](fxt-add-nodes.md)
