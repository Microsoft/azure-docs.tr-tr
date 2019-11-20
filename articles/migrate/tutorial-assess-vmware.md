---
title: Azure geçişi ile Azure 'a geçiş için VMware VM 'lerini değerlendirin
description: Azure geçişi kullanılarak Azure 'a geçiş için şirket içi VMware VM 'lerinin nasıl değerlendirileneceğini açıklar.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/18/2019
ms.author: hamusa
ms.openlocfilehash: 7187cb41fa7ea499035c57e83e04038b1269b418
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158676"
---
# <a name="assess-vmware-vms-with-azure-migrate-server-assessment"></a>Azure geçişi ile VMware VM 'lerini değerlendirin: Sunucu değerlendirmesi

Bu makalede, Azure geçişi: Sunucu değerlendirmesi Aracı kullanılarak şirket içi VMware VM 'lerinin nasıl değerlendirireceğiniz gösterilmektedir.

[Azure geçişi](migrate-services-overview.md) , Microsoft Azure için uygulamaları, altyapıyı ve iş yüklerini keşfetmenize, değerlendirmenize ve geçirmenize yardımcı olan araçların merkezini sağlar. Hub, Azure geçiş araçları ve üçüncü taraf bağımsız yazılım satıcısı (ISV) tekliflerini içerir.



Bu öğretici, VMware VM 'lerinin Azure 'a nasıl değerlendirileceğini ve geçirileceğini gösteren bir serinin ikinci saniyedir. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Bir Azure geçişi projesi ayarlayın.
> * VM 'Leri değerlendirmek için şirket içinde çalışan bir Azure geçiş gereci ayarlayın.
> * Şirket içi VM 'lerin sürekli olarak bulunmasını başlatın. Gereç, bulunan VM 'Ler için yapılandırma ve performans verilerini Azure 'a gönderir.
> * Bulunan VM 'Leri gruplandırın ve VM grubunu değerlendirin.
> * Değerlendirmeyi gözden geçirin.



> [!NOTE]
> Öğreticiler, bir senaryo için en basit dağıtım yolunu gösterir, böylece bir kavram kanıtı hızlı bir şekilde ayarlayabilmenizi sağlayabilirsiniz. Öğreticiler mümkün olduğunca varsayılan seçenekleri kullanır ve tüm olası ayarları ve yolları göstermez. Ayrıntılı yönergeler için, nasıl yapılır makalelerini gözden geçirin.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.


## <a name="prerequisites"></a>Önkoşullar

- Bu serideki ilk öğreticiyi [doldurun](tutorial-prepare-vmware.md) . Bunu yapmazsanız, bu öğreticideki yönergeler çalışmaz.
- İlk öğreticide yapmanız gerekenler şunlardır:
    - Azure geçişi için [Azure Izinleri ayarlayın](tutorial-prepare-vmware.md#prepare-azure) .
    - [VMware](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) 'i değerlendirme için hazırlayın. VMware ayarlarının doğrulanması gerekir ve bir OVA şablonuyla bir VMware VM oluşturma izinlerine sahip olmanız gerekir. Ayrıca, VM keşfi için ayarlanmış bir hesabınız olmalıdır. Gerekli bağlantı noktaları kullanılabilir olmalıdır ve Azure 'a erişim için gereken URL 'Lerden haberdar olmanız gerekir.


## <a name="set-up-an-azure-migrate-project"></a>Azure geçişi projesi ayarlama

Aşağıdaki şekilde yeni bir Azure Geçişi projesi oluşturun.

1. Azure portalı > **Tüm hizmetler** bölümünde **Azure Geçişi**’ni arayın.
2. **Hizmetler** altında **Azure Geçişi**’ni seçin.
3. **Genel Bakış** bölümünde **Sunucuları bul, değerlendir ve geçiş** altında **Sunucuları değerlendir ve geçir** üzerine tıklayın.

    ![Sunucuları bulma ve değerlendirme](./media/tutorial-assess-vmware/assess-migrate.png)

4. **Başlarken** bölümünde **Araç ekle**’ye tıklayın.
5. **Projeyi geçir** bölümünde Azure aboneliğinizi seçin ve henüz yapmadıysanız bir kaynak grubu oluşturun.     
6. **Proje ayrıntıları**' nda proje adını ve projeyi oluşturmak istediğiniz coğrafi konumu belirtin. Asya, Avrupa, UK ve Birleşik Devletler desteklenir.

    - Proje coğrafyası yalnızca şirket içi sanal makinelerden toplanan meta verileri depolamak için kullanılır.
    - Bir geçiş çalıştırdığınızda herhangi bir hedef bölgeyi seçebilirsiniz.

    ![Azure geçişi projesi oluşturma](./media/tutorial-assess-vmware/migrate-project.png)


7. **İleri**’ye tıklayın.
8. **Değerlendirme Seç aracında** **Azure geçişi: Sunucu değerlendirmesi** > **İleri**' yi seçin.

    ![Azure geçişi projesi oluşturma](./media/tutorial-assess-vmware/assessment-tool.png)

9. **Geçiş aracını seç** bölümünde **Şimdilik geçiş aracı eklemeyi atla** > **İleri** seçeneğini belirleyin.
10. **İnceleme + araç ekleme** bölümünde ayarları gözden geçirip **Araç ekle**’ye tıklayın.
11. Azure Geçişi projesinin dağıtılması için birkaç dakika bekleyin. Proje sayfasına yönlendirilirsiniz. Projeyi görmüyorsanız Azure Geçişi panosundaki **Sunuculardan** erişebilirsiniz.


## <a name="set-up-the-appliance-vm"></a>Gereç sanal makinesini ayarlama

Azure geçişi: Sunucu değerlendirmesi bir basit VMware VM gereci çalıştırır.

- Bu gereç, VM bulma işlemini gerçekleştirir ve Azure geçişi sunucu değerlendirmesi ' ne VM meta verilerini ve performans verilerini gönderir.
- Gereci kurmak için şunları yapın:
    - Bir OVA şablon dosyasını indirip vCenter Server içe aktarın.
    - Gereci oluşturun ve Azure geçişi sunucu değerlendirmesi 'ne bağlanıp bağlanamadığından emin olun.
    - Gereci ilk kez yapılandırın ve Azure geçişi projesi ile kaydedin.
- Tek bir Azure geçişi projesi için birden çok gereçini ayarlayabilirsiniz. Tüm gereçlerde 35.000 VM 'ye kadar bulma desteklenir. Her gereç için en fazla 10.000 sunucu bulunabilir.

### <a name="download-the-ova-template"></a>OVA şablonunu indirin

1. **Azure geçişi: Sunucu değerlendirmesi** > **sunucuları** > **geçiş hedeflerde** **bul**' a tıklayın.
2. **Makineleri bul** > **Makineleriniz sanallaştırıldı mı?** bölümünde **Evet, VMware vSphere hiper yöneticisi ile** seçeneğini belirleyin.
3. .OVA şablon dosyasını indirmek için **İndir**’e tıklayın.

    ![.ova dosyasını indir](./media/tutorial-assess-vmware/download-ova.png)


### <a name="verify-security"></a>Güvenliği doğrulama

Dağıtmadan önce OVA dosyasının güvenli olduğundan emin olun.

1. Dosyayı indirdiğiniz makinede yönetici komut penceresi açın.
2. OVA’nın karmasını oluşturmak için aşağıdaki komutu çalıştırın:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Örnek kullanım: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Sürüm 2.19.07.30 için, oluşturulan karma bu değerlerle eşleşmelidir.

  **Algoritma** | **Karma değeri**
  --- | ---
  MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
  SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c


### <a name="create-the-appliance-vm"></a>Gereç VM 'sini oluşturma

İndirilen dosyayı içeri aktarın ve bir VM oluşturun.

1. vSphere Client konsolunda **Dosya** > **OVF Şablonu Dağıt**’a tıklayın.

    ![OVF dağıtma](./media/tutorial-assess-vmware/deploy-ovf.png)

2. OVF şablonu Dağıtma Sihirbazı > **kaynak**bölümünde ova dosyasının konumunu belirtin.
3. **Ad** ve **konum**bölümünde VM için bir kolay ad belirtin. VM 'nin barındırıldığı envanter nesnesini seçin.
5. **Konakta/kümede**, sanal makinenin çalıştırılacağı konağı veya kümeyi belirtin.
6. **Depolama**alanında VM için depolama hedefini belirtin.
7. **Disk Biçimi**’nde disk türünü ve boyutunu belirtin.
8. **Ağ eşlemesinde**, sanal makinenin bağlanacağı ağı belirtin. Ağ, Azure geçişi sunucu değerlendirmesi 'ne meta veri göndermek için internet bağlantısı gerektirir.
9. Ayarları gözden geçirip onayladıktan sonra **Son**’a tıklayın.


### <a name="verify-appliance-access-to-azure"></a>Azure 'a gereç erişimini doğrulama

Gereç VM 'sinin [Azure URL 'lerine](migrate-support-matrix-vmware.md#assessment-url-access-requirements)bağlanabildiğinizden emin olun.


### <a name="configure-the-appliance"></a>Gereci yapılandırma

Aşağıdaki adımları kullanarak gereç 'yi ayarlayın.

1. vSphere Client konsolunda VM’ye sağ tıklayın ve **Konsolu Aç** seçeneğini belirleyin.
2. Gereç için dil, saat dilimi ve parola sağlayın.
3. VM 'ye bağlanabilecek herhangi bir makinede bir tarayıcı açın ve gereç Web uygulamasının URL 'sini açın: **https://*Gereç adı veya IP adresi*: 44368**.

   Alternatif olarak, uygulama kısayoluna tıklayarak uygulamayı gereç masaüstünden açabilirsiniz.
4. **Önkoşulları ayarlamak**> Web uygulamasında şunları yapın:
    - **Lisans**: lisans koşullarını kabul edin ve üçüncü taraf bilgilerini okuyun.
    - **Bağlantı**: uygulama, sanal makinenin internet erişimi olup olmadığını denetler. VM bir proxy kullanıyorsa:
        - **Ara sunucu ayarları**' na tıklayın ve proxy adresini ve dinleme bağlantı noktasını, http://ProxyIPAddress veya http://ProxyFQDNbiçiminde belirtin.
        - Proxy için kimlik doğrulaması gerekiyorsa kimlik bilgilerini gerekin.
        - Yalnızca HTTP proxy’si desteklenir.
    - **Zaman eşitleme**: bulmanın düzgün çalışması için gereç üzerindeki zaman internet ile eşitlenmiş olmalıdır.
    - **Güncelleştirmeleri yükleme**: gereç en son güncelleştirmelerin yüklü olmasını sağlar.
    - **VDDK 'Yi yükleme**: gereç, VMware vSphere sanal disk geliştirme seti 'nın (VDDK) yüklü olup olmadığını denetler.
        - Azure geçişi: sunucu geçişi, Azure 'a geçiş sırasında makineleri çoğaltmak için VDDK 'yi kullanır.
        - VMware 'den VDDK 6,7 ' i indirin ve indirilen ZIP içeriğini gereç üzerindeki belirtilen konuma ayıklayın.

### <a name="register-the-appliance-with-azure-migrate"></a>Gereci Azure geçişi ile kaydetme

1. **Oturum aç**' a tıklayın. Görünmüyorsa, tarayıcıda açılır pencere engelleyicisini devre dışı bırakmış olduğunuzdan emin olun.
2. Yeni sekmede, Azure kimlik bilgilerinizi kullanarak oturum açın.
    - Kullanıcı adınızla ve parolanızla oturum açın.
    - PIN ile oturum açma desteklenmez.
3. Başarıyla oturum açtıktan sonra Web uygulamasına geri dönün.
2. Azure geçişi projesinin oluşturulduğu aboneliği seçin, ardından projeyi seçin.
3. Gereç için bir ad belirtin. Ad 14 karakter veya daha az olmalıdır.
4. **Kaydol**' a tıklayın.

## <a name="start-continuous-discovery"></a>Sürekli bulmayı Başlat

Gerecin, VM 'lerin yapılandırma ve performans verilerini bulması için vCenter Server 'e bağlanması gerekir.

### <a name="specify-vcenter-server-details"></a>vCenter Server ayrıntılarını belirtin
1. **VCenter Server ayrıntılarını belirtin**bölümünde vCenter Server adı (FQDN) veya IP adresini belirtin. Varsayılan bağlantı noktasını bırakabilir veya vCenter Server dinlediği özel bir bağlantı noktası belirtebilirsiniz.
2. **Kullanıcı adı** ve **parola**' da, gerecin vCenter sunucusundaki VM 'leri bulması için kullanacağı salt okuma hesabı kimlik bilgilerini belirtin. Hesabın [bulma için gerekli izinlere](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions)sahip olduğundan emin olun. VCenter hesabına erişimi uygun şekilde sınırlayarak bulma kapsamını belirleyebilirsiniz; kapsam [bulma hakkında](tutorial-assess-vmware.md#scoping-discovery)daha fazla bilgi edinin.
3. Gerecin vCenter Server bağlanabildiğini sağlamak için **bağlantıyı doğrula** ' ya tıklayın.

### <a name="specify-vm-credentials"></a>VM kimlik bilgilerini belirtin
Uygulama, rol ve özellik bulma ve VM 'lerin bağımlılıklarını görselleştirme için, VMware VM 'lerine erişimi olan bir VM kimlik bilgisi sağlayabilirsiniz. Windows VM 'ler için bir kimlik bilgisi ve Linux sanal makineleri için bir kimlik bilgisi ekleyebilirsiniz. Gerekli erişim ayrıcalıkları hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) .

> [!NOTE]
> Bu girdi isteğe bağlıdır ve uygulama bulma ve aracısız bağımlılık görselleştirmesini etkinleştirmek için gereklidir.

1. **VM 'lerde uygulamaları ve bağımlılıkları bul**' da, **kimlik bilgileri ekle**' ye tıklayın.
2. **Işletim sistemini**seçin.
3. Kimlik bilgisi için kolay bir ad sağlayın.
4. **Kullanıcı adı** ve **parola**' da, VM 'lerde en az konuk erişimi olan bir hesap belirtin.
5. **Ekle**'ye tıklayın.

VCenter Server ve VM kimlik bilgilerini (isteğe bağlı) belirledikten sonra, şirket içi ortamı bulmaya başlamak için **Kaydet ve bulmayı Başlat** ' a tıklayın.

Keşfedilen VM 'lerin meta verilerinde portalda görünmesi 15 dakika sürer. Yüklenen uygulamaların, rollerin ve özelliklerin bulunması biraz zaman alabilir, süre bulunan VM sayısına bağlıdır. 500 VM 'Ler için, uygulama envanterinin Azure geçişi portalında görünmesi yaklaşık 1 saat sürer.

### <a name="scoping-discovery"></a>Kapsam bulma

Bulma için kullanılan vCenter hesabının erişimi sınırlanarak bulma kapsamı oluşturulabilir. Kapsamı, veri merkezleri, kümeler, kümeler klasörü, konaklar, konaklar klasörü veya ayrı VM 'Ler vCenter Server şekilde ayarlayabilirsiniz.

Kapsamı ayarlamak için aşağıdaki adımları gerçekleştirmeniz gerekir:
1.  VCenter Kullanıcı hesabı oluşturun.
2.  Gerekli ayrıcalıklara sahip yeni bir rol tanımlayın. (<em>aracısız sunucu geçişi için gereklidir</em>)
3.  VCenter nesnelerinde Kullanıcı hesabına izin atayın.

**VCenter Kullanıcı hesabı oluşturma**
1.  VCenter Server Yöneticisi olarak vSphere Web Istemcisinde oturum açın.
2.  **Kullanıcı** sekmesine >  **, SSO kullanıcıları ve grupları** > **Yönetim** ' e tıklayın.
3.  **Yeni Kullanıcı** simgesine tıklayın.
4.  Yeni bir kullanıcı oluşturmak için gerekli bilgileri girin ve **Tamam**' a tıklayın.

**Gerekli ayrıcalıklara sahip yeni bir rol tanımlayın** (<em>aracısız sunucu geçişi için gereklidir</em>)
1.  VCenter Server Yöneticisi olarak vSphere Web Istemcisinde oturum açın.
2.  **Yönetim** > **Rol Yöneticisi**' ne gidin.
3.  Açılır menüden vCenter Server seçin.
4.  **Rol oluştur** eylemi ' ne tıklayın.
5.  Yeni rol için bir ad yazın. (örneğin, <em>Azure_Migrate</em>).
6.  Bu [izinleri](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) yeni tanımlanan role atayın.
7.  **OK (Tamam)** düğmesine tıklayın.

**VCenter nesnelerinde izin atama**

VCenter 'daki envanter nesnelerine atanmış bir role sahip vCenter Kullanıcı hesabına izin atamak için 2 yaklaşım vardır.
- Sunucu değerlendirmesi için, bulunması gereken VM 'Lerin barındırıldığı tüm üst nesneler için vCenter Kullanıcı hesabına **salt okuma** rolü uygulanmalıdır. Tüm üst nesneler-konak, konaklar, küme, hiyerarşide veri merkezine kadar olan kümelerin bir klasörü dahil edilecek. Bu izinler hiyerarşideki alt nesnelere yayılmalıdır.

    Benzer şekilde sunucu geçişi için, Kullanıcı tanımlı bir rol ( <em>Azure _Migrate</em>olarak adlandırılabilir) bu [ayrıcalıklar](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) , geçirilecek VM 'lerin barındırıldığı tüm üst nesneler için vCenter Kullanıcı hesabına uygulanmalıdır.

![İzin atama](./media/tutorial-assess-vmware/assign-perms.png)

- Alternatif yaklaşım, Kullanıcı hesabını ve rolü veri merkezi düzeyinde atamak ve bunları alt nesnelere yaymalıdır. Ardından hesaba, bulmayı/geçirmeyi istemediğiniz her nesne için **erişim** rolü (örneğin, VM 'ler) verin. Bu yapılandırma, kısaberbir yapılandırmadır. Her yeni alt nesneye Ayrıca otomatik olarak üst öğeden devralınan erişim verildiğinden, bu, yanlışlıkla erişim denetimleri sunar. Bu nedenle, ilk yaklaşımı kullanmanızı öneririz.

> [!NOTE]
> Bugün, vCenter hesabının vCenter VM klasör düzeyinde erişimi varsa sunucu değerlendirmesi VM 'Leri bulamaz. Bulma işlemini VM klasörlerine göre yapmak istiyorsanız, vCenter hesabının bir VM düzeyinde salt okuma erişimi olduğundan emin olarak bunu yapabilirsiniz.  Bu, bunu nasıl yapacağınız hakkında yönergeler aşağıda verilmiştir:
>
> 1. Keşfi kapsamında olmasını istediğiniz VM klasörlerindeki tüm VM 'lerde salt okunurdur izinleri atayın.
> 2. VM 'Lerin barındırıldığı tüm üst nesnelere salt okuma erişimi verin. Tüm üst nesneler-konak, konaklar klasörü, küme, küme klasörü, veri merkezine kadar olan hiyerarşide yer alır. İzinleri tüm alt nesnelere yaymaya gerek yoktur.
> 3. Veri merkezini *koleksiyon kapsamı*olarak seçme bulma için kimlik bilgilerini kullanın. RBAC kümesi, karşılık gelen vCenter kullanıcısının yalnızca kiracıya özgü VM 'lere erişmesini sağlar.
>
> Konaklar ve kümeler klasörünün desteklendiğini unutmayın.

### <a name="verify-vms-in-the-portal"></a>VM’lerin portalda olup olmadığını doğrulama

Bulmadan sonra, VM 'Lerin Azure portal göründüğünü doğrulayabilirsiniz.

1. Azure geçişi panosunu açın.
2. **Azure geçişi-sunucular** > **Azure geçişi: Sunucu değerlendirmesi** sayfasında, **bulunan sunucuların**sayısını görüntüleyen simgeye tıklayın.

## <a name="set-up-an-assessment"></a>Değerlendirme ayarlama

Azure geçişi: Sunucu değerlendirmesi kullanarak oluşturabileceğiniz iki tür değerlendirme vardır.

**Değerlendirme** | **Ayrıntılar** | **Veri**
--- | --- | ---
**Performans tabanlı** | Toplanan performans verilerine dayalı değerlendirmeler | **ÖNERILEN VM boyutu**: CPU ve bellek kullanım verilerine göre.<br/><br/> **Önerilen disk türü (Standart veya Premium yönetilen disk)** : Şirket ıçı disklerin IOPS ve aktarım hızına göre.
**Şirket içi olarak** | Şirket içi boyutlandırmayı temel alan değerlendirmeler. | **ÖNERILEN VM boyutu**: ŞIRKET içi VM boyutuna göre<br/><br> **Önerilen disk türü**: değerlendirme için seçtiğiniz depolama türü ayarına göre.


### <a name="run-an-assessment"></a>Değerlendirme çalıştırma

Bir değerlendirmeyi aşağıdaki gibi çalıştırın:

1. Değerlendirme oluşturmak için [en iyi uygulamaları](best-practices-assessment.md) gözden geçirin.
2. **Sunucular** sekmesinde, **Azure geçişi: Sunucu değerlendirmesi** kutucuğunda **değerlendir**' e tıklayın.

    ![Değerlendirme](./media/tutorial-assess-vmware/assess.png)

2. **Sunucuları değerlendir**bölümünde, değerlendirme için bir ad belirtin.
3. Değerlendirme özelliklerini gözden geçirmek için **Tümünü görüntüle**’ye tıklayın.

    ![Değerlendirme özellikleri](./media/tutorial-assess-vmware/view-all.png)

3. **Grup Seç veya oluştur**' da, **Yeni oluştur**' u seçin ve bir grup adı belirtin. Bir grup, değerlendirme için bir veya daha fazla VM 'yi toplar.
4. **Gruba makine ekleme**' de gruba eklenecek VM 'ler ' i seçin.
5. Grubu oluşturmak için **değerlendirme oluştur** ' a tıklayın ve değerlendirmeyi çalıştırın.

    ![Değerlendirme oluşturma](./media/tutorial-assess-vmware/assessment-create.png)

6. Değerlendirme oluşturulduktan sonra, **Azure geçişi: Sunucu değerlendirmesi** > **değerlendirmeleri** > **sunucularda** görüntüleyin.
7. Excel dosyası olarak indirmek için **Değerlendirmeyi dışarı aktar**’a tıklayın.



## <a name="review-an-assessment"></a>Değerlendirmeyi gözden geçirme

Bir değerlendirme şunları açıklar:

- **Azure hazırlığı**: VM 'lerin Azure 'a geçiş için uygun olup olmadığı.
- **Aylık maliyet tahmini**: VM 'leri Azure 'da çalıştırmaya yönelik tahmini aylık işlem ve depolama maliyetleri.
- **Aylık depolama maliyeti tahmini**: geçişten sonra disk depolaması için tahmini maliyetler.

### <a name="view-an-assessment"></a>Değerlendirme görüntüleme

1. **Sunucular** >  **geçiş hedefleri** ' nde **Azure geçişi: Sunucu değerlendirmesi**' nde **değerlendirmeler** ' a tıklayın.
2. **Değerlendirmede**, bir değerlendirmeye tıklayarak açın.

    ![Değerlendirme özeti](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure hazırlığını gözden geçirme

1. **Azure 'a hazırlık**bölümünde, VM 'lerin Azure 'a geçiş için hazır olup olmadığını doğrulayın.
2. VM durumunu gözden geçirin:
    - **Azure Için hazırlanma**: Azure geçişi, değerlendirmede VM 'ler IÇIN bir VM boyutu ve maliyet tahminleri önerir.
    - **Koşullara hazırlanma**: sorunları ve önerilen düzeltmeyi gösterir.
    - **Azure için hazırlanma**: sorunları ve önerilen düzeltmeyi gösterir.
    - **Hazır olma durumu bilinmiyor**: veri kullanılabilirliği sorunları nedeniyle Azure geçişi hazırlığı değerlendiremez kullanılır.

2. **Azure hazırlık** durumuna tıklayın. VM hazırlığı ayrıntılarını görüntüleyebilir ve işlem, depolama ve ağ ayarları dahil olmak üzere VM ayrıntılarını görmek için ayrıntıya gidebilirsiniz.



### <a name="review-cost-details"></a>Maliyet ayrıntılarını gözden geçirin

Bu görünüm Azure 'da çalışan VM 'lerin tahmini işlem ve depolama maliyetini gösterir.

1. Aylık işlem ve depolama maliyetlerini gözden geçirin. Ücretler, değerlendirilen gruptaki tüm VM 'Ler için toplanır.

    - Maliyet tahminleri, bir makine için boyut önerilerini ve bunların disklerini ve özelliklerini temel alır.
    - İşlem ve depolama için tahmini aylık maliyetler gösterilir.
    - Maliyet tahmini, şirket içi VM 'Leri IaaS VM 'Leri olarak çalıştırmaya yöneliktir. Azure geçişi sunucu değerlendirmesi PaaS veya SaaS maliyetlerini göz önünde bulundurmaz.

2. Aylık depolama maliyeti tahminlerini gözden geçirebilirsiniz. Bu görünüm, farklı türlerdeki depolama disklerinin üzerine bölünen, değerlendirilen grup için toplanan depolama maliyetlerini gösterir.
3. Belirli VM 'Lerin ayrıntılarını görmek için ayrıntıya gidebilirsiniz.


### <a name="review-confidence-rating"></a>Güvenilirlik derecelendirmesini gözden geçirme

Performans tabanlı değerlendirmeler çalıştırdığınızda, değerlendirmeye bir güvenilirlik derecelendirmesi atanır.

![Güvenilirlik derecelendirmesi](./media/tutorial-assess-vmware/confidence-rating.png)

- 1-yıldız (en düşük) ile 5 yıldız (en yüksek) arasında bir derecelendirme verilir.
- Güvenilirlik derecelendirmesi, değerlendirme tarafından belirtilen boyut önerilerinin güvenilirliğini tahmin etmenize yardımcı olur.
- Güvenilirlik derecelendirmesi, değerlendirmeyi hesaplamak için gereken veri noktalarının kullanılabilirliğine bağlıdır.

Bir değerlendirme için güven derecelendirmeleri aşağıdaki gibidir.

**Veri noktası kullanılabilirliği** | **Güvenilirlik derecelendirmesi**
--- | ---
%0-%20 | 1 Yıldız
%21-%40 | 2 Yıldız
%41-%60 | 3 Yıldız
%61-%80 | 4 Yıldız
%81-%100 | 5 Yıldız

Güvenilirlik derecelendirmeleri için en iyi uygulamalar hakkında [daha fazla bilgi edinin](best-practices-assessment.md#best-practices-for-confidence-ratings) .


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Azure geçişi gereci ayarlama
> * Bir değerlendirme oluşturulup gözden geçirildi

VMware VM 'lerini Azure 'a geçirme sunucu geçişi ile Azure 'a geçirmeyi öğrenmek için serideki üçüncü öğreticiye geçin.

> [!div class="nextstepaction"]
> [VMware VM 'lerini geçirme](./tutorial-migrate-vmware.md)
