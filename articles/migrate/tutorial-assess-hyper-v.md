---
title: Azure geçişi ile Azure 'a geçiş için Hyper-V VM 'lerini değerlendirin | Microsoft Docs
description: Azure geçişi kullanılarak Azure 'a geçiş için şirket içi Hyper-V VM 'lerinin nasıl değerlendirileneceğini açıklar.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/18/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: d8a4a6d650684cd5c8c0f22ad683c3952e2f6d08
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158375"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Azure geçişi sunucu değerlendirmesi ile Hyper-V VM 'lerini değerlendirin

Bu makalede, şirket içi Hyper-V VM 'lerini Azure geçişi: Sunucu değerlendirmesi aracını kullanarak nasıl değerlenbileceğiniz gösterilmektedir.

[Azure geçişi](migrate-services-overview.md) , Microsoft Azure için uygulamaları, altyapıyı ve iş yüklerini keşfetmenize, değerlendirmenize ve geçirmenize yardımcı olan araçların merkezini sağlar. Hub, Azure geçiş araçları ve üçüncü taraf bağımsız yazılım satıcısı (ISV) tekliflerini içerir.



Bu öğretici, Hyper-V VM 'lerinin Azure 'a nasıl değerlendirileceğini ve geçirileceğini gösteren bir serinin ikinci saniyedir. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir Azure geçişi projesi ayarlayın.
> * Bir Azure geçiş gereci ayarlayın ve kaydedin.
> * Şirket içi VM 'lerin sürekli olarak bulunmasını başlatın.
> * Bulunan VM 'Leri gruplandırın ve grubu değerlendirin.
> * Değerlendirmeyi gözden geçirin.

> [!NOTE]
> Öğreticiler, bir senaryo için en basit dağıtım yolunu gösterir, böylece bir kavram kanıtı hızlı bir şekilde ayarlayabilmenizi sağlayabilirsiniz. Öğreticiler mümkün olduğunca varsayılan seçenekleri kullanır ve tüm olası ayarları ve yolları göstermez. Ayrıntılı yönergeler için, nasıl yapılır makalelerini gözden geçirin.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.


## <a name="prerequisites"></a>Önkoşullar

- Bu serideki ilk öğreticiyi [doldurun](tutorial-prepare-hyper-v.md) . Bunu yapmazsanız, bu öğreticideki yönergeler çalışmaz.
- İlk öğreticide yapmanız gerekenler şunlardır:
    - Azure geçişi için [Azure Izinleri ayarlayın](tutorial-prepare-hyper-v.md#prepare-azure) .
    - [Hyper-V](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-assessment) kümelerini, konakları ve VM 'leri değerlendirme için hazırlayın.

## <a name="set-up-an-azure-migrate-project"></a>Azure geçişi projesi ayarlama

1. Azure portalı > **Tüm hizmetler** bölümünde **Azure Geçişi**’ni arayın.
2. Arama sonuçlarında **Azure geçişi**' ni seçin.
3. **Genel Bakış** bölümünde **Sunucuları bul, değerlendir ve geçiş** altında **Sunucuları değerlendir ve geçir** üzerine tıklayın.

    ![Sunucuları bulma ve değerlendirme](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. **Başlarken** bölümünde **Araç ekle**’ye tıklayın.
5. **Projeyi geçir** sekmesinde Azure aboneliğinizi seçin ve yoksa bir kaynak grubu oluşturun.
6. **Proje ayrıntıları**' nda proje adını ve projeyi oluşturmak istediğiniz bölgeyi belirtin.


    ![Azure geçişi projesi oluşturma](./media/tutorial-assess-hyper-v/migrate-project.png)

    Bu bölgelerde bir Azure geçişi projesi oluşturabilirsiniz.

    **Coğrafya** | **Bölge**
    --- | ---
    Asya  | Güneydoğu Asya
    Avrupa | Kuzey Avrupa veya Batı Avrupa
    Birleşik Krallık |  UK Güney veya UK Batı
    Amerika Birleşik Devletleri | Doğu ABD, Batı ABD 2 veya Orta Batı ABD

    - Proje bölgesi yalnızca şirket içi VM 'lerden toplanan meta verileri depolamak için kullanılır.
    - VM 'Leri geçirirken farklı bir Azure hedef bölgesi seçebilirsiniz. Tüm Azure bölgeleri geçiş hedefi için desteklenir.

7. **İleri**’ye tıklayın.
8. **Değerlendirme Seç aracında** **Azure geçişi: Sunucu değerlendirmesi** > **İleri**' yi seçin.

    ![Azure geçişi projesi oluşturma](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. **Geçiş aracını seç** bölümünde **Şimdilik geçiş aracı eklemeyi atla** > **İleri** seçeneğini belirleyin.
10. **İnceleme + araç ekleme** bölümünde ayarları gözden geçirip **Araç ekle**’ye tıklayın.
11. Azure Geçişi projesinin dağıtılması için birkaç dakika bekleyin. Proje sayfasına yönlendirilirsiniz. Projeyi görmüyorsanız Azure Geçişi panosundaki **Sunuculardan** erişebilirsiniz.




## <a name="set-up-the-appliance-vm"></a>Gereç sanal makinesini ayarlama

Azure geçişi sunucu değerlendirmesi, hafif bir Hyper-V sanal makine gereci çalıştırır.

- Bu gereç VM bulma işlemini gerçekleştirir ve Azure geçişi: Sunucu değerlendirmesi ' ne VM meta verilerini ve performans verilerini gönderir.
- Gereci kurmak için şunları yapın:
    - Azure portal sıkıştırılmış bir Hyper-V VHD 'sini indirin.
    - Gereci oluşturun ve Azure geçişi sunucu değerlendirmesi 'ne bağlanıp bağlanamadığından emin olun.
    - Gereci ilk kez yapılandırın ve Azure geçişi projesi ile kaydedin.

### <a name="download-the-vhd"></a>VHD 'YI indirin

Gereç için daraltılmış VHD şablonunu indirin.

1. **Azure geçişi: Sunucu değerlendirmesi** > **sunucuları** > **geçiş hedeflerde** **bul**' a tıklayın.
2. **Makineleriniz > ** makinelerde **sanallaştırılmış mı?** , **Hyper-V ile Evet**' e tıklayın.
3. VHD dosyasını indirmek için **İndir** ' e tıklayın.

    ![VM 'yi indir](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Güvenliği doğrulama

Dağıtmadan önce daraltılmış dosyanın güvenli olduğunu denetleyin.

1. Dosyayı indirdiğiniz makinede yönetici komut penceresi açın.

2. ZIP dosyasının karmasını oluşturmak için aşağıdaki PowerShell komutunu çalıştırın
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Örnek kullanım: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  Gereç sürümü 2.19.07.30 için, oluşturulan karma bu ayarlarla eşleşmelidir.

  **Algoritma** | **Karma değeri**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950bc
  SHA256 | 37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31

### <a name="create-the-appliance-vm"></a>Gereç VM 'sini oluşturma

İndirilen dosyayı içeri aktarın ve VM 'yi oluşturun.

1. Sıkıştırılmış VHD dosyasını gereç VM 'sinin yerleştirileceği Hyper-V konağına indirdikten sonra, daraltılmış dosyayı ayıklayın.
    - Ayıklanan konumda, dosya **AzureMigrateAppliance_VersionNumber**adlı bir klasöre UNIP 'leri yok.
    - Bu klasör **AzureMigrateAppliance_VersionNumber**olarak da adlandırılan bir alt klasör içerir.
    - Bu alt klasör, daha fazla üç alt **görüntü**, **sanal sabit disk**ve **sanal makine**içerir.

2. Hyper-V Yöneticisi 'Ni açın. **Eylemler**' de **sanal makineyi içeri aktar**' a tıklayın.

    ![VHD dağıtma](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. Sanal makineyi Içeri aktarma sihirbazında > **başlamadan önce** **İleri**' ye tıklayın.
3. **Klasörü bul**' da, **sanal makineler** klasörünü seçin. Ardından **İleri**'ye tıklayın.
1. **Sanal makine seç**bölümünde **İleri**' ye tıklayın.
2. **Içeri aktarma türünü seçin**bölümünde **sanal makineyi Kopyala (yenı bir benzersiz kimlik oluştur)** seçeneğine tıklayın. Ardından **İleri**'ye tıklayın.
3. **Hedef Seç**bölümünde varsayılan ayarı bırakın. **İleri**’ye tıklayın.
4. **Depolama klasörlerinde**varsayılan ayarı bırakın. **İleri**’ye tıklayın.
5. **Ağ seçin**bölümünde, VM 'nin kullanacağı sanal anahtarı belirtin. Anahtar, verileri Azure 'a göndermek için internet bağlantısı gerektirir.
6. **Özet**bölümünde ayarları gözden geçirin. Ardından **son**' a tıklayın.
7. Hyper-V Yöneticisi 'nde **sanal makineler**>, VM 'yi başlatın.


### <a name="verify-appliance-access-to-azure"></a>Azure 'a gereç erişimini doğrulama

Gereç VM 'sinin [Azure URL 'lerine](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access)bağlanabildiğinizden emin olun.

### <a name="configure-the-appliance"></a>Gereci yapılandırma

Gereci ilk kez ayarlayın.

1. Hyper-V Yöneticisi > **sanal makineler**' de, **bağlan**' a > VM ' ye sağ tıklayın.
2. Gereç için dil, saat dilimi ve parola sağlayın.
3. VM 'ye bağlanabilecek herhangi bir makinede bir tarayıcı açın ve gereç Web uygulamasının URL 'sini açın: **https://*Gereç adı veya IP adresi*: 44368**.

   Alternatif olarak, uygulama kısayoluna tıklayarak uygulamayı gereç masaüstünden açabilirsiniz.
1. **Önkoşulları ayarlamak**> Web uygulamasında şunları yapın:
    - **Lisans**: lisans koşullarını kabul edin ve üçüncü taraf bilgilerini okuyun.
    - **Bağlantı**: uygulama, sanal makinenin internet erişimi olup olmadığını denetler. VM bir proxy kullanıyorsa:
      - **Ara sunucu ayarları**' na tıklayın ve proxy adresini ve dinleme bağlantı noktasını, http://ProxyIPAddress veya http://ProxyFQDNbiçiminde belirtin.
      - Proxy için kimlik doğrulaması gerekiyorsa kimlik bilgilerini gerekin.
      - Yalnızca HTTP proxy’si desteklenir.
    - **Zaman eşitleme**: Saat doğrulandı. VM bulmanın düzgün çalışması için gereç süresi internet saatine eşit olmalıdır.
    - **Güncelleştirmeleri yükleme**: Azure geçişi sunucu değerlendirmesi, gerecin en son güncelleştirmelerin yüklü olduğunu denetler.

### <a name="register-the-appliance-with-azure-migrate"></a>Gereci Azure geçişi ile kaydetme

1. **Oturum aç**' a tıklayın. Görünmüyorsa, tarayıcıda açılır pencere engelleyicisini devre dışı bırakmış olduğunuzdan emin olun.
2. Yeni sekmede, Azure kimlik bilgilerinizi kullanarak oturum açın.
    - Kullanıcı adınızla ve parolanızla oturum açın.
    - PIN ile oturum açma desteklenmez.
3. Başarıyla oturum açtıktan sonra Web uygulamasına geri dönün.
4. Azure geçişi projesinin oluşturulduğu aboneliği seçin. Ardından projeyi seçin.
5. Gereç için bir ad belirtin. Ad 14 karakter veya daha az olmalıdır.
6. **Kaydol**' a tıklayın.


### <a name="delegate-credentials-for-smb-vhds"></a>SMB VHD 'leri için temsilci kimlik bilgileri

SMB 'lerde VHD 'ler çalıştırıyorsanız, Gereç üzerinden Hyper-V konaklarına kimlik bilgileri temsilcisini etkinleştirmeniz gerekir. Bunun için aşağıdakiler gereklidir:

- Her konağın gereç için bir temsilci görevi görmesini sağlayabilirsiniz. Değerlendirme ve geçiş için Hyper-V ' yi hazırladığınızda, bu işlemi önceki öğreticide yapmış olmanız gerekir. Konaklar için CredSSP 'yi [el ile](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts)veya [Hyper-V önkoşulları yapılandırma betiğini çalıştırarak](tutorial-prepare-hyper-v.md#hyper-v-prerequisites-configuration-script)ayarlamanız gerekir.
- Azure geçişi gerecinin istemci olarak davranabilmesi ve bir konağa kimlik bilgileri temsilciliğini sağlamak için CredSSP temsilcisini etkinleştirin.

Gereçte aşağıdaki şekilde etkinleştirin:

#### <a name="option-1"></a>Seçenek 1

Gereç sanal makinesinde bu komutu çalıştırın. HyperVHost1/HyperVHost2, örnek ana bilgisayar adlarıdır.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
```

Örnek: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>Seçenek 2

Alternatif olarak, bunu gereç üzerindeki Yerel Grup İlkesi Düzenleyicisi yapın:

1. **Bilgisayar yapılandırması** > **yerel bilgisayar Ilkesinde** **Yönetim Şablonları** > **sistem** > **kimlik bilgileri temsili**' ne tıklayın.
2. **Yeni kimlik bilgileri aktarımına Izin ver**' e çift tıklayın ve **etkin**' i seçin.
3. **Seçenekler**' de **göster**' e tıklayın ve bulmayı Istediğiniz her Hyper-V konağını, **WSMan/** as öneki ile listeye ekleyin.
4. Ardından, **kimlik bilgileri temsilcisi**' nde, **yalnızca NTLM sunucu kimlik doğrulamasıyla yeni kimlik bilgileri aktarmaya izin ver**' e çift tıklayın. Daha sonra, bulmayı istediğiniz her Hyper-V konağını, **WSMan/** as öneki ile listeye ekleyin.

## <a name="start-continuous-discovery"></a>Sürekli bulmayı Başlat

Gerecden Hyper-V konaklarına veya kümelerine bağlanın ve VM bulmayı başlatın.

1. **Kullanıcı adı** ve **parola**' da, gerecin VM 'leri bulması için kullanacağı hesap kimlik bilgilerini belirtin. Kimlik bilgileri için kolay bir ad belirtin ve **ayrıntıları kaydet**' e tıklayın.
2. **Konak Ekle**' ye tıklayın ve Hyper-V konağı/küme ayrıntılarını belirtin.
3. **Doğrula**' ya tıklayın. Doğrulamadan sonra, her bir konakta/kümede keşfedilebilir VM sayısı gösterilir.
    - Bir konakta doğrulama başarısız olursa, **durum** sütunundaki simgenin üzerine gelerek hatayı gözden geçirin. Sorunları giderin ve yeniden doğrulayın.
    - Konakları veya kümeleri kaldırmak için > **Sil**' i seçin.
    - Belirli bir konağı kümeden kaldıramazsınız. Tüm kümeyi yalnızca çıkarabilirsiniz.
    - Kümedeki belirli konaklarla ilgili sorunlar olsa bile bir küme ekleyebilirsiniz.
4. Doğrulamadan sonra, bulma işlemini başlatmak için **Kaydet ve bulmayı Başlat** ' a tıklayın.

Bu, bulmayı başlatır. Bulunan sunucuların meta verilerinin Azure portal görünmesi için ana bilgisayar başına yaklaşık 1,5 dakika sürer.

### <a name="verify-vms-in-the-portal"></a>VM’lerin portalda olup olmadığını doğrulama

Bulma işlemi tamamlandıktan sonra, VM 'Lerin portalda göründüğünü doğrulayabilirsiniz.

1. Azure geçişi panosunu açın.
2. **Azure geçişi-sunucular** > **Azure geçişi: Sunucu değerlendirmesi** sayfasında, **bulunan sunucuların**sayısını görüntüleyen simgeye tıklayın.

## <a name="set-up-an-assessment"></a>Değerlendirme ayarlama

Azure geçişi sunucu değerlendirmesi kullanarak çalıştırabileceğiniz iki tür değerlendirme vardır.

**Değerlendirme** | **Ayrıntılar** | **Veri**
--- | --- | ---
**Performans tabanlı** | Toplanan performans verilerine dayalı değerlendirmeler | **ÖNERILEN VM boyutu**: CPU ve bellek kullanım verilerine göre.<br/><br/> **Önerilen disk türü (Standart veya Premium yönetilen disk)** : Şirket ıçı disklerin IOPS ve aktarım hızına göre.
**Şirket içi olarak** | Şirket içi boyutlandırmayı temel alan değerlendirmeler. | **ÖNERILEN VM boyutu**: ŞIRKET içi VM boyutuna göre<br/><br> **Önerilen disk türü**: değerlendirme için seçtiğiniz depolama türü ayarına göre.



### <a name="run-an-assessment"></a>Değerlendirme çalıştırma

Bir değerlendirmeyi aşağıdaki gibi çalıştırın:

1. Değerlendirme oluşturmak için [en iyi uygulamaları](best-practices-assessment.md) gözden geçirin.
2. **Azure geçişi: Sunucu değerlendirmesi** > **sunucularında** , **değerlendir**' e tıklayın.

    ![Değerlendirme](./media/tutorial-assess-hyper-v/assess.png)

3. **Sunucuları değerlendir**bölümünde, değerlendirme için bir ad belirtin.
4. Değerlendirme özelliklerini gözden geçirmek için **Tümünü görüntüle**’ye tıklayın.

    ![Değerlendirme özellikleri](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. **Grup Seç veya oluştur**' da, **Yeni oluştur** ' u seçin ve bir grup adı belirtin. Bir grup, değerlendirme için bir veya daha fazla VM 'yi toplar.
4. **Gruba makine ekleme**' de gruba eklenecek VM 'ler ' i seçin.
5. Grubu oluşturmak için **değerlendirme oluştur** ' a tıklayın ve değerlendirmeyi çalıştırın.

    ![Değerlendirme oluşturma](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Değerlendirme oluşturulduktan sonra, **Azure geçişi: Sunucu değerlendirmesi** > **sunucularda** görüntüleyin.
7. Excel dosyası olarak indirmek için **Değerlendirmeyi dışarı aktar**’a tıklayın.


## <a name="review-an-assessment"></a>Değerlendirmeyi gözden geçirme

Bir değerlendirme şunları açıklar:

- **Azure hazırlığı**: VM 'lerin Azure 'a geçiş için uygun olup olmadığı.
- **Aylık maliyet tahmini**: VM 'leri Azure 'da çalıştırmaya yönelik tahmini aylık işlem ve depolama maliyetleri.
- **Aylık depolama maliyeti tahmini**: geçişten sonra disk depolaması için tahmini maliyetler.


### <a name="view-an-assessment"></a>Değerlendirme görüntüleme

1. **Azure geçişi: Sunucu değerlendirmesi** > **sunucuları** >  **geçiş hedefleri** ' nde, **değerlendirmeler**' a tıklayın.
2. **Değerlendirmede**, bir değerlendirmeye tıklayarak açın.

    ![Değerlendirme özeti](./media/tutorial-assess-hyper-v/assessment-summary.png)


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

![Güvenilirlik derecelendirmesi](./media/tutorial-assess-hyper-v/confidence-rating.png)

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

Hyper-V VM 'lerini Azure geçişi sunucu geçişi ile Azure 'a geçirmeyi öğrenmek için serideki üçüncü öğreticiye geçin.

> [!div class="nextstepaction"]
> [Hyper-V VM 'lerini geçirme](./tutorial-migrate-hyper-v.md)
