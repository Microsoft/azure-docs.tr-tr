---
title: Aracısız VMware geçişi için Azure geçişi genişleme gereci ayarlama
description: Hyper-V VM 'lerini geçirmek için bir Azure geçiş genişleme gereci ayarlamayı öğrenin.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 5ca821cb4f85deb77595e4a9029cc10298dbb884
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105611982"
---
# <a name="scale-agentless-migration-of-vmware-virtual-machines-to-azure"></a>VMware sanal makinelerinin aracısız geçişini Azure 'a ölçeklendirin

Bu makale, VMware VM 'Leri geçişi için Azure geçişi sunucusu geçiş aracının aracısız yöntemini kullanarak çok sayıda VMware sanal makinesini (VM) Azure 'a geçirmek için bir genişleme gerecinin nasıl kullanılacağını anlamanıza yardımcı olur.

VMware sanal makineleri için aracısız geçiş yöntemini kullanarak şunları yapabilirsiniz:

- Tek bir vCenter sunucusundaki 300 adede kadar VM 'yi bir Azure geçişi gereci ile eşzamanlı olarak çoğaltın.
- Geçiş için ikinci bir genişleme gereci dağıtarak, tek bir vCenter Server 'dan 500 adede kadar VM 'yi eşzamanlı olarak çoğaltın.

Bu makalede şunları yapmayı öğreneceksiniz:

- VMware sanal makinelerinin aracısız geçişi için genişleme gereci ekleme
- Ölçek Genişletme gereci kullanarak eşzamanlı 500 VM 'ye kadar geçiş yapın.

##  <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, aşağıdaki adımları gerçekleştirmeniz gerekir:

- Azure geçişi projesini oluşturun.
- Azure geçişi gereci (birincil gereç) dağıtın ve vCenter Server tarafından yönetilen VMware sanal makinelerini bulmayı tamamen yapın.
- Geçirilecek bir veya daha fazla sanal makine için çoğaltmayı yapılandırın.
> [!IMPORTANT]
> Geçiş için bir genişleme gereci ekleyebilmeniz için önce projede en az bir çoğaltma sanal makinesi olması gerekir.

Yukarıdaki işlemleri yapmayı öğrenmek için, [VMware sanal makinelerini aracısız geçiş yöntemiyle Azure 'a geçirme](./tutorial-migrate-vmware.md)öğreticisini gözden geçirin.

## <a name="deploy-a-scale-out-appliance"></a>Genişleme gereci dağıtma

Bir genişleme gereci eklemek için aşağıda bahsedilen adımları izleyin:

1.   >  **Makineleriniz sanallaştırılmış mı?** 
1. **VMware vSphere hiper yöneticiyle Evet ' i seçin.**
1. Sonraki adımda aracısız çoğaltma ' yı seçin.
1. Gereç türünü seçin menüsünde **var olan bir birincil** gerecin ölçeğini Seç ' i seçin.
1. Genişletmek istediğiniz birincil gereci (bulmayı kullanan gereç) seçin.

:::image type="content" source="./media/how-to-scale-out-for-migration/add-scale-out.png" alt-text="Genişleme ekleme için makineleri bul sayfası":::

### <a name="1-generate-the-azure-migrate-project-key"></a>1. Azure geçişi proje anahtarını oluşturma

1. **Azure geçişi proje anahtarı oluştur**' da, genişleme gereci için bir sonek adı sağlayın. Sonek yalnızca alfasayısal karakterler içerebilir ve uzunluğu 14 karakter olabilir.
2. Gerekli Azure kaynaklarının oluşturulmasını başlatmak için **anahtar oluştur** ' a tıklayın. Kaynak oluşturma sırasında bul sayfasını kapatmayın.
3. Oluşturulan anahtarı kopyalayın. Ölçek Genişletme gerecinin kaydını tamamlaması için daha sonra anahtara ihtiyacınız olacak.

### <a name="2-download-the-installer-for-the-scale-out-appliance"></a>2. genişleme gereci için yükleyiciyi indirin

**Azure geçişi yükleme** gereci ' nda **İndir**' e tıklayın. Genişleme gerecini Windows Server 2016 çalıştıran var olan bir sunucuya ve gerekli donanım yapılandırması (32-GB RAM, 8 V80 CPU ve doğrudan ya da bir ara sunucu üzerinden) için gereken donanım yapılandırmasıyla dağıtmak üzere PowerShell yükleyici betiğini indirmeniz gerekir.
:::image type="content" source="./media/how-to-scale-out-for-migration/download-scale-out.png" alt-text="Genişleme gereci için betiği indirme":::

> [!TIP]
> İndirilen ZIP dosyasının sağlama toplamını aşağıdaki adımları kullanarak doğrulayabilirsiniz:
>
> 1. Komut istemi 'ni yönetici olarak aç
> 2. Daraltılmış dosyanın karmasını oluşturmak için aşağıdaki komutu çalıştırın:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Genel bulut için örnek kullanım: ```C:\>Get-FileHash -Path .\AzureMigrateInstaller-VMware-Public-Scaleout.zip -Algorithm SHA256 ```
> 3. Hesaplanan karma değeri şu dizeyle eşleşmiyorsa, portaldan genişleme gereç yükleyicisinin en son sürümünü indirin: 1E6B6E3EE8B2A800818B925F5DA67EF7874DAD87E32847120B32F3E21F5960F9

### <a name="3-run-the-azure-migrate-installer-script"></a>3. Azure geçişi yükleyici betiğini çalıştırın
Yükleyici betiği şunları yapar:

- Daha eşzamanlı sunucu çoğaltmaları gerçekleştirmek için ağ geçidi Aracısı ve gereç yapılandırma yöneticisini kurar.
- Windows etkinleştirme hizmeti, IIS ve PowerShell ıSE dahil Windows rollerini yükler.
- Bir IIS yeniden yazılabilir modülünü indirip yükler. [Daha fazla bilgi edinin](https://www.microsoft.com/download/details.aspx?id=7435).
- Azure geçişi için kalıcı ayar ayrıntılarıyla bir kayıt defteri anahtarını (HKLM) güncelleştirir.
- Yolun altında aşağıdaki dosyaları oluşturur:
    - **Yapılandırma dosyaları**:%ProgramData%\Microsoft Azure\Config
    - **Günlük dosyaları**:%ProgramData%\Microsoft Azure\Logs

Betiği aşağıdaki gibi çalıştırın:

1. ZIP dosyasını, genişleme gerecini barındıracak sunucuda bir klasöre ayıklayın.  Betiği, mevcut bir Azure geçiş gereci ile bir sunucuda çalıştırmayın.
2. Yönetim (yükseltilmiş) ayrıcalığıyla yukarıdaki sunucuda PowerShell 'i başlatın.
3. PowerShell dizinini, indirilen ZIP dosyasından içeriğin ayıklandığı klasör olarak değiştirin.
4. Aşağıdaki komutu kullanarak **AzureMigrateInstaller.ps1**  adlı betiği çalıştırın:

    - Genel bulut için: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```

    Betik, yürütmeyi tamamladığında gereç yapılandırma yöneticisini başlatır.

Herhangi bir sorunla karşılaşırsanız, komut dosyası günlüklerine şu adreste erişebilirsiniz: <br/> Sorun giderme için C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.


### <a name="4-configure-the-appliance"></a>4. gereci yapılandırma

Başlamadan önce, [Bu Azure uç noktalarına](migrate-appliance.md#public-cloud-urls) genişleme gerecinden erişilebildiğinden emin olun.

- Genişleme gereç sunucusuna bağlanabilecek herhangi bir makinede bir tarayıcı açın ve gereç Yapılandırma Yöneticisi 'nin URL 'sini açın: **https://*genişleme Gereç adı veya IP adresi*: 44368**.

   Alternatif olarak, Configuration Manager kısayolunu kullanarak, genişleme gereç sunucusunun masaüstündeki Yapılandırma Yöneticisi 'ni açabilirsiniz.
- **Lisans koşullarını** kabul edin ve üçüncü taraf bilgilerini okuyun.
- Configuration Manager > **önkoşulları ayarlama** bölümünde şunları yapın:
   - **Bağlantı**: gereç, sunucunun internet erişimi olup olmadığını denetler. Sunucu bir proxy kullanıyorsa:
     1. Ara sunucu adresini belirtmek için **Ara sunucu** ' ya tıklayın (formdaki http://ProxyIPAddress veya http://ProxyFQDN) dinleme bağlantı noktasında).
     2. Proxy için kimlik doğrulaması gerekiyorsa kimlik bilgilerini gerekin.
     3. Yalnızca HTTP proxy’si desteklenir.
     4. Proxy ayrıntıları eklediyseniz veya proxy ve/veya kimlik doğrulamasını devre dışı bırakırsanız, bağlantıyı tetiklemek için **Kaydet** 'e tıklayarak bağlantı denetimini yeniden başlatın.
   - **Zaman eşitleme**: bulmanın düzgün çalışması için gereç üzerindeki zaman internet ile eşitlenmiş olmalıdır.
   - **Güncelleştirmeleri yükleme**: gereç en son güncelleştirmelerin yüklü olmasını sağlar. Denetim tamamlandıktan sonra, Gereç sunucusunda çalışan hizmetlerin durumunu ve sürümlerini görmek için **gereç hizmetlerini görüntüle** ' ye tıklayabilirsiniz.
   - **VDDK 'Yi yükleme**: gereç, VMware vSphere sanal disk geliştirme seti 'nın (VDDK) yüklü olduğunu denetler. Yüklü değilse, sanal DDK 6,7 ' i VMware 'den indirin ve yüklenen ZIP içeriğini gereç Configuration Manager ekranındaki **yükleme yönergelerindeki** belirtilen konuma ayıklayın.


### <a name="register-the-appliance-with-azure-migrate"></a>Gereci Azure geçişi ile kaydetme

1. Portaldan kopyalanmış **Azure geçişi proje anahtarını** yapıştırın. Anahtarınız yoksa, sunucu değerlendirmesi ' ne gidin **> var olan gereçlerini keşfedin> yönetin**, birincil gereç adını seçin, onunla ilişkili genişleme gerecini bulun ve ilgili anahtarı kopyalayın.
1. Azure ile kimlik doğrulaması yapmak için bir cihaz koduna ihtiyacınız olacaktır. **Oturum** açmak için tıkladığınızda, aşağıda gösterildiği gibi cihaz koduyla kalıcı olarak açılır.
:::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Cihaz kodunu kalıcı olarak gösterme":::

1. Cihaz kodunu kopyalamak ve yeni bir tarayıcı sekmesinde bir Azure oturum açma istemi açmak için **kodu kopyala & oturum** aç ' a tıklayın. Görünmüyorsa, tarayıcıda açılır pencere engelleyicisini devre dışı bırakmış olduğunuzdan emin olun.
1. Yeni sekmede, Azure Kullanıcı adınızı ve parolanızı kullanarak cihaz kodunu yapıştırın ve oturum açın.
   
   PIN ile oturum açma desteklenmez.
3. Oturum açmadan oturum açma sekmesini yanlışlıkla kapatırsanız, oturum açma düğmesini yeniden etkinleştirmek için gereç Yapılandırma Yöneticisi 'nin tarayıcı sekmesini yenilemeniz gerekir.
1. Başarıyla oturum açtıktan sonra, Gereç Yapılandırma Yöneticisi ile önceki sekmeye geri dönün.
1. Günlüğe kaydetme için kullanılan Azure Kullanıcı hesabının, anahtar üretimi sırasında oluşturulan Azure kaynakları üzerinde doğru izinleri varsa, Gereç kaydı başlatılır.
:::image type="content" source="./media/how-to-scale-out-for-migration/registration-scale-out.png" alt-text="Gereç Yapılandırma Yöneticisi 'nde panel 2":::

#### <a name="import-appliance-configuration-from-primary-appliance"></a>Gereç yapılandırmasını birincil gereç 'dan içeri aktar

Ölçek Genişletme gerecinin kaydını tamamlayabilmeniz için, **içeri aktar** ' a tıklayarak gerekli yapılandırma dosyalarını birincil gereç içinden alın.

1. **İçeri aktar** ' a tıkladığınızda, gerekli yapılandırma dosyalarının birincil gereç üzerinden nasıl içeri aktarılacağı hakkında yönergeler içeren bir açılır pencere açılır.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-modal-scale-out.png" alt-text="Yapılandırmayı kalıcı olarak içeri aktar":::
1. Birincil gereç için oturum açın (Uzak Masaüstü) ve aşağıdaki PowerShell komutlarını yürütün:

    ``` PS cd 'C:\Program Files\Microsoft Azure Appliance Configuration Manager\Scripts\PowerShell' ```
    
    ``` PS .\ExportConfigFiles.ps1 ```

1. Yukarıdaki komutları çalıştırarak oluşturulan ZIP dosyasını genişleme gerecine kopyalayın. ZIP dosyası, ölçek genişletme gerecinin kaydedilmesi için gereken yapılandırma dosyalarını içerir.
1. Önceki adımda açılan açılan pencerede, kopyalanmış yapılandırma ZIP dosyasının konumunu seçin ve **Kaydet**' e tıklayın.

Dosyalar başarıyla alındıktan sonra, ölçek genişletme gerecinin kaydı tamamlanır ve son başarılı almanın zaman damgasını gösterir. Ayrıca, **Ayrıntıları görüntüle**' ye tıklayarak kayıt ayrıntılarına bakabilirsiniz.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-success.png" alt-text="Ekran görüntüsünde, Azure geçişi projesi ile genişleme gereç kaydı gösterilmektedir.":::

Bu noktada, ölçek genişletme gerecinin vCenter Server 'a bağlanabildiğini yeniden doğrulamanız gerekir. Genişleme gerecinden vCenter Server bağlantısını doğrulamak için **yeniden doğrula** ' ya tıklayın.
:::image type="content" source="./media/how-to-scale-out-for-migration/view-sources.png" alt-text="Ekran görüntüsü, doğrulanacak kimlik bilgilerini ve bulma kaynaklarını gösterir.":::

> [!IMPORTANT]
> Birincil gereç üzerinde vCenter Server kimlik bilgilerini düzenlerseniz, en son yapılandırmayı almak ve devam eden çoğaltmaları sürdürmek için yapılandırma dosyalarını ölçek genişletme gerecine yeniden içeri aktartığınızdan emin olun.<br/> Genişleme gerecine daha fazla ihtiyacınız yoksa, genişleme gerecini devre dışı bıraktığınızdan emin olun. Gerekli olmadığında ölçek genişletme gerecinin nasıl devre dışı bırakılacağı hakkında [**daha fazla bilgi edinin**](./common-questions-appliance.md) .

## <a name="replicate"></a>Çoğaltma

1. Genişleme gereci kaydedildikten sonra, Azure geçişi: sunucu geçişi kutucuğunda **Çoğalt**' a tıklayın.

2.  Daha fazla sanal makine Çoğaltmaya başlamak için ekrandaki adımları izleyin. 

Genişleme gereci yerine, artık 500 VM 'Leri eşzamanlı olarak çoğaltabilirsiniz. Ayrıca, Azure portal aracılığıyla VM 'Leri 200 toplu işlemlerine geçirebilirsiniz.

Azure geçişi sunucusu geçiş aracı, sanal makinelerin çoğaltma için birincil ve genişleme gereci arasında dağıtılması için gereken bir işlem gerçekleştirir. Çoğaltma tamamlandıktan sonra, sanal makineleri geçirebilirsiniz.

> [!TIP]
> Çok sayıda sanal makineyi geçirmek istiyorsanız, en iyi performans için, sanal makinelerin 200 toplu işleri halinde geçirilmesini öneririz.
  
## <a name="next-steps"></a>Sonraki adımlar

Bu makalede şunları öğrendiniz:
- Genişleme gereci yapılandırma
- Genişleme gereci kullanarak VM 'Leri çoğaltma


Azure geçişi 'ni kullanarak sunucuları Azure 'a geçirme hakkında [daha fazla bilgi edinin](./tutorial-migrate-vmware.md) : sunucu geçiş aracı.