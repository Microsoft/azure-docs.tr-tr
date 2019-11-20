---
title: Azure geçişi sunucu değerlendirmesi ile Azure 'a geçiş için fiziksel sunucuları değerlendirme
description: Azure geçişi sunucu değerlendirmesi kullanılarak Azure 'a geçiş için şirket içi fiziksel sunucuların nasıl değerlendirileneceğini açıklar.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 7f840a99ab76548c16389955cf49791036cc63a2
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158369"
---
# <a name="assess-physical-servers-with-azure-migrate-server-assessment"></a>Azure geçişi ile fiziksel sunucuları değerlendirme: Sunucu değerlendirmesi

Bu makalede, şirket içi fiziksel sunucuların Azure geçişi: Sunucu değerlendirmesi Aracı kullanılarak nasıl değerlendirireceğiniz gösterilmektedir.

[Azure geçişi](migrate-services-overview.md) , Microsoft Azure için uygulamaları, altyapıyı ve iş yüklerini keşfetmenize, değerlendirmenize ve geçirmenize yardımcı olan araçların merkezini sağlar. Hub, Azure geçiş araçları ve üçüncü taraf bağımsız yazılım satıcısı (ISV) tekliflerini içerir.

Bu öğretici, fiziksel sunucuların Azure 'a nasıl değerlendirileceğini ve geçirileceğini gösteren bir serinin ikinci saniyedir. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Bir Azure geçişi projesi ayarlayın.
> * Fiziksel sunucuları değerlendirmek için şirket içinde çalışan bir Azure geçiş gereci ayarlayın.
> * Şirket içi fiziksel sunucuları sürekli bulmayı başlatın. Gereç, bulunan sunucular için yapılandırma ve performans verilerini Azure 'a gönderir.
> * Bulunan sunucuları gruplandırın ve sunucu grubunu değerlendirin.
> * Değerlendirmeyi gözden geçirin.

> [!NOTE]
> Öğreticiler, bir senaryo için en basit dağıtım yolunu gösterir, böylece bir kavram kanıtı hızlı bir şekilde ayarlayabilmenizi sağlayabilirsiniz. Öğreticiler mümkün olduğunca varsayılan seçenekleri kullanır ve tüm olası ayarları ve yolları göstermez. Ayrıntılı yönergeler için, nasıl yapılır makalelerini gözden geçirin.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.


## <a name="prerequisites"></a>Önkoşullar

- Bu serideki ilk öğreticiyi [doldurun](tutorial-prepare-physical.md) . Bunu yapmazsanız, bu öğreticideki yönergeler çalışmaz.
- İlk öğreticide yapmanız gerekenler şunlardır:
    - Azure geçişi için [Azure Izinleri ayarlayın](tutorial-prepare-physical.md#prepare-azure) .
    - [Fiziksel sunucuları](tutorial-prepare-physical.md#prepare-for-physical-server-assessment) değerlendirme için hazırlayın. Gereç gereksinimleri doğrulanmalıdır. Ayrıca fiziksel sunucu keşfi için ayarlanmış bir hesabınız olmalıdır. Gerekli bağlantı noktaları kullanılabilir olmalıdır ve Azure 'a erişim için gereken URL 'Lerden haberdar olmanız gerekir.


## <a name="set-up-an-azure-migrate-project"></a>Azure geçişi projesi ayarlama

Aşağıdaki şekilde yeni bir Azure Geçişi projesi oluşturun.

1. Azure portalı > **Tüm hizmetler** bölümünde **Azure Geçişi**’ni arayın.
2. **Hizmetler** altında **Azure Geçişi**’ni seçin.
3. **Genel Bakış** bölümünde **Sunucuları bul, değerlendir ve geçiş** altında **Sunucuları değerlendir ve geçir** üzerine tıklayın.

    ![Sunucuları bulma ve değerlendirme](./media/tutorial-assess-physical/assess-migrate.png)

4. **Başlarken** bölümünde **Araç ekle**’ye tıklayın.
5. **Projeyi geçir** bölümünde Azure aboneliğinizi seçin ve henüz yapmadıysanız bir kaynak grubu oluşturun.     
6. **Proje ayrıntıları**' nda proje adını ve projeyi oluşturmak istediğiniz coğrafi konumu belirtin. Asya, Avrupa, UK ve Birleşik Devletler desteklenir.

    - Proje Coğrafya yalnızca şirket içi sunuculardan toplanan meta verileri depolamak için kullanılır.
    - Bir geçiş çalıştırdığınızda herhangi bir hedef bölgeyi seçebilirsiniz.

    ![Azure geçişi projesi oluşturma](./media/tutorial-assess-physical/migrate-project.png)


7. **İleri**’ye tıklayın.
8. **Değerlendirme Seç aracında** **Azure geçişi: Sunucu değerlendirmesi** > **İleri**' yi seçin.

    ![Azure geçişi projesi oluşturma](./media/tutorial-assess-physical/assessment-tool.png)

9. **Geçiş aracını seç** bölümünde **Şimdilik geçiş aracı eklemeyi atla** > **İleri** seçeneğini belirleyin.
10. **İnceleme + araç ekleme** bölümünde ayarları gözden geçirip **Araç ekle**’ye tıklayın.
11. Azure Geçişi projesinin dağıtılması için birkaç dakika bekleyin. Proje sayfasına yönlendirilirsiniz. Projeyi görmüyorsanız Azure Geçişi panosundaki **Sunuculardan** erişebilirsiniz.


## <a name="set-up-the-appliance"></a>Gereci ayarlama

Azure geçişi: Sunucu değerlendirmesi bir hafif gereç çalıştırır.

- Bu gereç fiziksel sunucu bulmayı gerçekleştirir ve Azure geçişi sunucu değerlendirmesi için sunucu meta verilerini ve performans verilerini gönderir.
- Gereci kurmak için şunları yapın:
    - Azure geçişi yükleyicisi komut dosyasıyla Azure portal sıkıştırılmış bir dosyayı indirin.
    - Sıkıştırılmış dosyadan içerikleri ayıklayın. Yönetim ayrıcalıklarıyla PowerShell konsolunu başlatın.
    - Gereç Web uygulamasını başlatmak için PowerShell betiğini yürütün.
    - Gereci ilk kez yapılandırın ve Azure geçişi projesi ile kaydedin.
- Tek bir Azure geçişi projesi için birden çok gereçini ayarlayabilirsiniz. Tüm gereçlerde, istediğiniz sayıda fiziksel sunucu bulabilirsiniz. Her gereç için en fazla 250 sunucu bulunabilir.

### <a name="download-the-installer-script"></a>Yükleyici betiğini indir

Gereç için daraltılmış dosyayı indirin.

1. **Azure geçişi: Sunucu değerlendirmesi** > **sunucuları** > **geçiş hedeflerde** **bul**' a tıklayın.
2. **Makineleriniz > ** makinelerde **sanallaştırılmış mı?** , **sanallaştırılmamış/diğer**' e tıklayın.
3. Sıkıştırılmış dosyayı indirmek için **İndir** ' e tıklayın.

    ![Yükleyiciyi indir](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Güvenliği doğrulama

Dağıtmadan önce daraltılmış dosyanın güvenli olduğunu denetleyin.

1. Dosyayı indirdiğiniz makinede yönetici komut penceresi açın.
2. Daraltılmış dosyanın karmasını oluşturmak için aşağıdaki komutu çalıştırın
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Örnek kullanım: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller\AzureMigrateInstaller.ps1 SHA256```

3.  Gereç sürümü 1.19.05.10 için, oluşturulan karma bu ayarlarla eşleşmelidir.

  **Algoritma** | **Karma değeri**
  --- | ---
  MD5 | 5b98cf68dad495696c789bdad8f0d91b
  SHA256 | c2364887738875a31634eb0cf5defd0398f813d41069875976ca076297071e6f

### <a name="run-the-azure-migrate-installer-script"></a>Azure geçişi yükleyici betiğini çalıştırma

Yükleyici betiği şunları yapar:

- Fiziksel sunucu keşfi ve değerlendirmesi için aracıları ve bir Web uygulamasını kurar.
- Windows etkinleştirme hizmeti, IIS ve PowerShell ıSE dahil Windows rollerini yükler.
- Bir IIS yeniden yazılabilir modülünü indirip yükler. [Daha fazla bilgi edinin](https://www.microsoft.com/download/details.aspx?id=7435).
- Azure geçişi için kalıcı ayar ayrıntılarıyla bir kayıt defteri anahtarını (HKLM) güncelleştirir.
- Yolun altında aşağıdaki dosyaları oluşturur:
    - **Yapılandırma dosyaları**:%ProgramData%\Microsoft Azure\Config
    - **Günlük dosyaları**:%ProgramData%\Microsoft Azure\Logs

Betiği aşağıdaki gibi çalıştırın:

1. Sıkıştırılmış dosyayı, Gereç barındıracak sunucuda bir klasöre ayıklayın.
2. Yönetim (yükseltilmiş) ayrıcalığıyla yukarıdaki sunucuda PowerShell 'i başlatın.
3. PowerShell dizinini, indirilen sıkıştırılmış dosyadan içeriğin ayıklandığı klasör olarak değiştirin.
4. Aşağıdaki komutu çalıştırarak **AzureMigrateInstaller. ps1** adlı betiği çalıştırın:
    ```
    PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
    ```
Betik, başarıyla tamamlandığında gereç Web uygulamasını başlatacaktır.

Herhangi bir sorun olması durumunda, sorun giderme için C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log konumundaki komut dosyası günlüklerine erişebilirsiniz.

> [!NOTE]
> Lütfen mevcut bir Azure geçişi gereci üzerinde Azure geçişi yükleyicisi betiğini çalıştırmayın.

### <a name="verify-appliance-access-to-azure"></a>Azure 'a gereç erişimini doğrulama

Gerecin [Azure URL 'lerine](migrate-support-matrix-physical.md#assessment-appliance-url-access)bağlanabildiğinizden emin olun.


### <a name="configure-the-appliance"></a>Gereci yapılandırma

Gereci ilk kez ayarlayın.

1. Gereç ile bağlanabilecek herhangi bir makinede bir tarayıcı açın ve gereç Web uygulamasının URL 'sini açın: **https://*Gereç adı veya IP adresi*: 44368**.

   Alternatif olarak, uygulama kısayoluna tıklayarak uygulamayı masaüstünden açabilirsiniz.
2. **Önkoşulları ayarlamak**> Web uygulamasında şunları yapın:
    - **Lisans**: lisans koşullarını kabul edin ve üçüncü taraf bilgilerini okuyun.
    - **Bağlantı**: uygulama, sunucunun internet erişimi olup olmadığını denetler. Sunucu bir proxy kullanıyorsa:
        - **Ara sunucu ayarları**' na tıklayın ve proxy adresini ve dinleme bağlantı noktasını, http://ProxyIPAddress veya http://ProxyFQDNbiçiminde belirtin.
        - Proxy için kimlik doğrulaması gerekiyorsa kimlik bilgilerini gerekin.
        - Yalnızca HTTP proxy’si desteklenir.
    - **Zaman eşitleme**: Saat doğrulandı. Sunucu bulmanın düzgün çalışması için gereç saatinin internet ile eşitlenmiş olması gerekir.
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


## <a name="start-continuous-discovery"></a>Sürekli bulmayı Başlat

Şimdi, gerecden keşfedilecek fiziksel sunuculara bağlanın ve bulmayı başlatın.

1. Gerecin sunucuları keşfetme için kullanacağı hesap kimlik bilgilerini belirtmek için **kimlik bilgileri ekle** ' ye tıklayın.  
2. Kimlik bilgileri, **Kullanıcı adı** ve **parola** için **işletim sistemini**, kolay adı belirtin ve **Ekle**' ye tıklayın.
Her biri Windows ve Linux sunucuları için bir kimlik bilgileri kümesi ekleyebilirsiniz.
4. Sunucuya bağlanmak için sunucu **Ekle**' ye tıklayın ve sunucu ayrıntılarını BELIRTIN-FQDN/IP adresi ve kimlik bilgilerinin kolay adı (satır başına bir giriş).
3. **Doğrula**' ya tıklayın. Doğrulamadan sonra, keşfedilebilir sunucu listesi gösterilir.
    - Bir sunucu için doğrulama başarısız olursa, **durum** sütunundaki simgenin üzerine gelerek hatayı gözden geçirin. Sorunları giderin ve yeniden doğrulayın.
    - Bir sunucuyu kaldırmak için > **Sil**' i seçin.
4. Doğrulamadan sonra, bulma işlemini başlatmak için **Kaydet ve bulmayı Başlat** ' a tıklayın.

Bu, bulmayı başlatır. Sunucu başına, bulunan sunucunun meta verilerinde Azure portal görünmesi için 1,5 dakika sürer.

### <a name="verify-servers-in-the-portal"></a>Portalda sunucuları doğrulama

Bulmadan sonra, sunucuların Azure portal göründüğünü doğrulayabilirsiniz.

1. Azure geçişi panosunu açın.
2. **Azure geçişi-sunucular** > **Azure geçişi: Sunucu değerlendirmesi** sayfasında, **bulunan sunucuların**sayısını görüntüleyen simgeye tıklayın.

## <a name="set-up-an-assessment"></a>Değerlendirme ayarlama

Azure geçişi: Sunucu değerlendirmesi kullanarak oluşturabileceğiniz iki tür değerlendirme vardır.

**Değerlendirme** | **Ayrıntılar** | **Veri**
--- | --- | ---
**Performans tabanlı** | Toplanan performans verilerine dayalı değerlendirmeler | **ÖNERILEN VM boyutu**: CPU ve bellek kullanım verilerine göre.<br/><br/> **Önerilen disk türü (Standart veya Premium yönetilen disk)** : Şirket ıçı disklerin IOPS ve aktarım hızına göre.
**Şirket içi olarak** | Şirket içi boyutlandırmayı temel alan değerlendirmeler. | **ÖNERILEN VM boyutu**: şirket içi sunucu boyutuna göre<br/><br> **Önerilen disk türü**: değerlendirme için seçtiğiniz depolama türü ayarına göre.


### <a name="run-an-assessment"></a>Değerlendirme çalıştırma

Bir değerlendirmeyi aşağıdaki gibi çalıştırın:

1. Değerlendirme oluşturmak için [en iyi uygulamaları](best-practices-assessment.md) gözden geçirin.
2. **Sunucular** sekmesinde, **Azure geçişi: Sunucu değerlendirmesi** kutucuğunda **değerlendir**' e tıklayın.

    ![Değerlendirme](./media/tutorial-assess-physical/assess.png)

2. **Sunucuları değerlendir**bölümünde, değerlendirme için bir ad belirtin.
3. Değerlendirme özelliklerini gözden geçirmek için **Tümünü görüntüle**’ye tıklayın.

    ![Değerlendirme özellikleri](./media/tutorial-assess-physical/view-all.png)

3. **Grup Seç veya oluştur**' da, **Yeni oluştur**' u seçin ve bir grup adı belirtin. Bir grup, değerlendirme için bir veya daha fazla sunucuyu bir araya toplar.
4. **Gruba makine ekleme**' de gruba eklenecek sunucular ' ı seçin.
5. Grubu oluşturmak için **değerlendirme oluştur** ' a tıklayın ve değerlendirmeyi çalıştırın.

    ![Değerlendirme oluşturma](./media/tutorial-assess-physical/assessment-create.png)

6. Değerlendirme oluşturulduktan sonra, **Azure geçişi: Sunucu değerlendirmesi** > **değerlendirmeleri** > **sunucularda** görüntüleyin.
7. Excel dosyası olarak indirmek için **Değerlendirmeyi dışarı aktar**’a tıklayın.



## <a name="review-an-assessment"></a>Değerlendirmeyi gözden geçirme

Bir değerlendirme şunları açıklar:

- **Azure hazırlığı**: sunucuların Azure 'a geçiş için uygun olup olmadığı.
- **Aylık maliyet tahmini**: Azure 'da sunucuları çalıştırmaya yönelik tahmini aylık işlem ve depolama maliyetleri.
- **Aylık depolama maliyeti tahmini**: geçişten sonra disk depolaması için tahmini maliyetler.

### <a name="view-an-assessment"></a>Değerlendirme görüntüleme

1. **Sunucular** >  **geçiş hedefleri** ' nde **Azure geçişi: Sunucu değerlendirmesi**' nde **değerlendirmeler** ' a tıklayın.
2. **Değerlendirmede**, bir değerlendirmeye tıklayarak açın.

    ![Değerlendirme özeti](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure hazırlığını gözden geçirme

1. **Azure 'a hazırlık**bölümünde, sunucuların Azure 'a geçiş için hazır olup olmadığını doğrulayın.
2. Durumu gözden geçirin:
    - **Azure Için hazırlanma**: Azure geçişi, değerlendirmede VM 'ler IÇIN bir VM boyutu ve maliyet tahminleri önerir.
    - **Koşullara hazırlanma**: sorunları ve önerilen düzeltmeyi gösterir.
    - **Azure için hazırlanma**: sorunları ve önerilen düzeltmeyi gösterir.
    - **Hazır olma durumu bilinmiyor**: veri kullanılabilirliği sorunları nedeniyle Azure geçişi hazırlığı değerlendiremez kullanılır.

2. **Azure hazırlık** durumuna tıklayın. Sunucu hazırlığı ayrıntılarını görüntüleyebilir ve işlem, depolama ve ağ ayarları dahil sunucu ayrıntılarını görmek için ayrıntıya gidebilirsiniz.



### <a name="review-cost-details"></a>Maliyet ayrıntılarını gözden geçirin

Bu görünüm Azure 'da çalışan VM 'lerin tahmini işlem ve depolama maliyetini gösterir.

1. Aylık işlem ve depolama maliyetlerini gözden geçirin. Ücretler, değerlendirilen gruptaki tüm sunucular için toplanır.

    - Maliyet tahminleri, bir makine için boyut önerilerini ve bunların disklerini ve özelliklerini temel alır.
    - İşlem ve depolama için tahmini aylık maliyetler gösterilir.
    - Maliyet tahmini, şirket içi sunucuları IaaS VM 'Leri olarak çalıştırmak içindir. Azure geçişi sunucu değerlendirmesi PaaS veya SaaS maliyetlerini göz önünde bulundurmaz.

2. Aylık depolama maliyeti tahminlerini gözden geçirebilirsiniz. Bu görünüm, farklı türlerdeki depolama disklerinin üzerine bölünen, değerlendirilen grup için toplanan depolama maliyetlerini gösterir.
3. Belirli sunucuların ayrıntılarını görmek için ayrıntıya gidebilirsiniz.


### <a name="review-confidence-rating"></a>Güvenilirlik derecelendirmesini gözden geçirme

Performans tabanlı değerlendirmeler çalıştırdığınızda, değerlendirmeye bir güvenilirlik derecelendirmesi atanır.

![Güvenilirlik derecelendirmesi](./media/tutorial-assess-physical/confidence-rating.png)

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

Azure geçişi: sunucu geçişi ile fiziksel sunucuları Azure 'a geçirmeyi öğrenmek için serideki üçüncü öğreticiye geçin.

> [!div class="nextstepaction"]
> [Fiziksel sunucuları geçirme](./tutorial-migrate-physical-virtual-machines.md)
