---
title: Azure Site Recovery hizmetiyle AWS VM'lerini Azure'a geçirme | Microsoft Docs
description: Bu makalede, Azure Site Recovery kullanılarak, Amazon Web Services’te (AWS) çalıştırılan Windows sanal makinelerinin Azure’a nasıl geçirileceği açıklanmaktadır.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4a1952f5ece4c021834fb98f8a09f1a2738e6469
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789382"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Amazon Web Services (AWS) sanal makinelerini Azure’a geçirme

Bu öğretici, Azure Site Recovery’yi kullanarak, Amazon Web Services (AWS) sanal makinelerini Azure sanal makinelerine nasıl geçireceğinizi öğretir. AWS EC2 örneklerini Azure’a geçirirken VM’ler, fiziksel şirket içi bilgisayarlarmış gibi değerlendirilir. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Önkoşulları doğrulama
> * Azure kaynaklarını hazırlama
> * AWS EC2 örneklerini geçiş için hazırlama
> * Yapılandırma sunucusunu dağıtma
> * Sanal makineler için çoğaltmayı etkinleştirme
> * Her şeyin çalıştığından emin olmak için yük devretme testi çalıştırma
> * Azure’a bir defalık yük devretme çalıştırma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.


> [!NOTE]
> Artık AWS örneklerini Azure 'a geçirmek için Azure geçişi hizmetini kullanabilirsiniz. [Daha fazla bilgi edinin](../migrate/tutorial-migrate-physical-virtual-machines.md).

## <a name="prerequisites"></a>Önkoşullar
- Geçirmek istediğiniz VM’lerin desteklenen bir işletim sistemi sürümünü çalıştırdığından emin olun. Desteklenen sürümlere şunlar dahildir: 
  - Windows Server 2016 
  - Windows Server 2012 R2
  - Windows Server 2012 
  - 64 bit Windows Server 2008 R2 SP1 veya sonrası
  - Red Hat Enterprise Linux 6,4 6,10, 7,1 7,6 (yalnızca HVM sanallaştırılmış örnekleri) *(RedHat BD sürücülerini çalıştıran örnekler desteklenmez.)*
  - CentOS 6,10 6,4, 7,1, 7,6 (yalnızca HVM sanallaştırılmış örnekleri)
 
- Çoğaltmak istediğiniz her sanal makinede Mobility hizmeti yüklü olmalıdır. 

    > [!IMPORTANT]
    > Sanal makine için çoğaltmayı etkinleştirdiğinizde Site Recovery bu hizmeti otomatik olarak yükler. Otomatik yükleme için, Site Recovery’nin sanal makineye erişmek için kullanacağı EC2 örneklerinde bir hesap hazırlamanız gerekir. Bir etki alanı veya yerel hesap kullanabilirsiniz. 
    > - Linux sanal makineleri için hesap, kaynak Linux sunucusu üzerindeki kök olmalıdır. 
    > - Windows sanal makineleri içinse, bir etki alanı hesabı kullanmıyorsanız yerel makinede Uzak Kullanıcı Erişim denetimini devre dışı bırakın:
    >
    >      Kayıt defterinde **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** altına **LocalAccountTokenFilterPolicy** DWORD girişini ekleyin ve değeri **1** olarak ayarlayın.

- Site Recovery yapılandırma sunucusu olarak kullanabileceğiniz ayrı bir EC2 örneği. Bu örnek, Windows Server 2012 R2’yi çalıştırıyor olmalıdır.

## <a name="prepare-azure-resources"></a>Azure kaynaklarını hazırlama

Geçirilen EC2 örneklerinin kullanılması için Azure’da birkaç kaynağın hazır olması gerekir. Bunlar arasında, depolama hesabı, kasa ve sanal ağ yer alır.

### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Çoğaltılan makinelerin görüntüleri Azure Depolama alanında tutulur. Şirket içinden Azure’a yük devretme gerçekleştirdiğinizde depolama alanından Azure sanal makineleri oluşturulur.

1. [Azure portalda](https://portal.azure.com), soldaki menüde **Kaynak oluştur** > **Depolama** > **Depolama hesabı**’nı seçin.
2. Depolama hesabınız için bir ad girin. Bu öğreticilerde **awsmigrated2017** adını kullanılır. Ad gereksinimleri şöyledir:
    - Azure içinde benzersiz olmalıdır
    - 3 ile 24 karakter arasında olmalıdır
    - Yalnızca rakamlar ve küçük harfler içerebilir
3. **Dağıtım modeli**, **Hesap türü**, **Performans** ve **Güvenli aktarım gereklidir** için varsayılanları değiştirmeyin.
5. **Çoğaltma** için varsayılan **RA-GRS** seçeneğini belirleyin.
6. Bu öğretici için kullanmak istediğiniz aboneliği seçin.
7. **Kaynak grubu** için **Yeni oluştur**’u seçin. Bu örnekte, kaynak grubu adı için **migrationRG** kullanacağız.
8. **Konum** için **Batı Avrupa**’yı seçin.
9. Depolama hesabını oluşturmak için **Oluştur**’u seçin.

### <a name="create-a-vault"></a>Kasa oluşturma

1. [Azure portalda](https://portal.azure.com) **Tüm hizmetler**’i seçin. **Kurtarma Hizmetleri kasaları**’nı arayın ve seçin.
2. Azure Kurtarma Hizmetleri kasaları sayfasında **Ekle**’yi seçin.
3. **Ad** olarak **myVault** değerini girin.
4. **Abonelik** için kullanmak istediğiniz aboneliği seçin.
4. **Kaynak Grubu** için **Mevcut olanı kullan**’ı ve **migrationRG** adını seçin.
5. **Konum** için **Batı Avrupa**’yı seçin.
5. Panodan yeni kasaya hızlı şekilde erişmek için **Panoya sabitle**’yi seçin.
7. İşiniz bittiğinde **Oluştur**’u seçin.

Yeni kasayı görmek için **Pano** > **Tüm Kaynaklar**’a gidin. Yeni kasa, **Kurtarma Hizmetleri kasaları** sayfasında da görüntülenir.

### <a name="set-up-an-azure-network"></a>Azure ağı ayarlama

Geçişten (yük devretme) sonra Azure sanal makineleri oluşturulduğunda bu Azure ağına katılırlar.

1. [Azure portalda](https://portal.azure.com) **Kaynak oluştur** > **Ağ** >
   **Sanal ağ**’ı seçin.
3. **Ad** için **myMigrationNetwork** yazın.
4. **Adres alanı** için varsayılan değeri bırakın (değer girmeniz gerekir).
5. **Abonelik** için kullanmak istediğiniz aboneliği seçin.
6. **Kaynak grubu** için **Mevcut olanı kullan**’ı ve **migrationRG** adını seçin.
7. **Konum** için **Batı Avrupa**’yı seçin.
8. **Alt ağ**altında, **ad** ve IP aralığı için varsayılan değerleri bırakın **(değer girilmesi gerekir)** .
9. DDoS koruması ayarları için yönergeler ekleyin.
10. **Hizmet Uç Noktaları** seçeneğini devre dışı bırakın.
11. Güvenlik Duvarı ayarları için yönergeler ekleyin.
12. İşiniz bittiğinde **Oluştur**’u seçin.

## <a name="prepare-the-infrastructure"></a>Altyapıyı hazırlama

Azure portalda kasanızın sayfasında, **Başlarken** bölümünden **Site Recovery** seçeneğini belirleyin ve **Altyapıyı Hazırla**’yı seçin. Aşağıdaki adımları tamamlayın.

### <a name="1-protection-goal"></a>1: Koruma hedefi

**Koruma Hedefi** sayfasında aşağıdaki değerleri seçin:

|    |  |
|---------|-----------|
| Makineleriniz nerede bulunuyor? |**Şirket içi**’ni seçin.|
| Makinelerinizi nereye çoğaltmak istiyorsunuz? |**Azure’a**’yı seçin.|
| Geçiş gerçekleştiriyor musunuz? | **Evet**' i seçin, sonra da beğenim ' ın yanındaki kutuyu işaretleyin **, ancak Azure Site Recovery devam etmek istiyorum.**
| Makineleriniz sanallaştırılmış mı? |**Sanallaştırılmamış / Diğer**’i seçin.|

İşiniz bittiğinde, sonraki bölüme geçmek için **Tamam**’ı seçin.

### <a name="2-select-deployment-planning"></a>2: dağıtım planlamasını seçin

**Dağıtım planlamasını tamamladınız mı?** bölümünde, **Daha sonra yapacağım**’ı seçin ve **Tamam**’ı seçin.

### <a name="3-prepare-source"></a>3: kaynağı hazırla

**Kaynağı hazırla** sayfasında **+ Yapılandırma Sunucusu** seçeneğini belirleyin.

1. Yapılandırma sunucusu oluşturmak ve kurtarma kasası ile kaydetmek için Windows Server 2012 R2 çalıştıran bir EC2 örneği kullanın.
2. [Hizmet URL’lerine](site-recovery-support-matrix-to-azure.md) erişebilmesi için yapılandırma sunucusu olarak kullandığınız EC2 örneği sanal makinesindeki ara sunucuyu yapılandırın.
3. [Microsoft Azure Site Recovery Birleşik Kurulumu](https://aka.ms/unifiedinstaller_wus)’nu indirin. Yerel makinenize indirebilir ve yapılandırma sunucusu olarak kullandığınız sanal makineye kopyalayabilirsiniz.
4. Kasa kayıt anahtarını indirmek için **İndir** düğmesini seçin. İndirilen dosyayı, yapılandırma sunucusu olarak kullandığınız sanal makineye kopyalayın.
5. Sanal makinede, Microsoft Azure Site Recovery Birleşik Kurulumu için indirdiğiniz yükleyiciye sağ tıklayın ve **Yönetici olarak çalıştır**’ı seçin.

    1. **Başlamadan Önce** bölümünde **Yapılandırma sunucusunu ve işlem sunucusunu yükleme**’yi seçin ve ardından **İleri**’yi seçin.
    2. **Üçüncü Taraf Yazılım Lisansı** bölümünde **Üçüncü taraf lisans sözleşmesini kabul ediyorum**’u seçin, ardından **İleri**’yi seçin.
    3. **Kayıt** bölümünde, **Gözat**’ı seçip kasa kayıt anahtarı dosyasını koyduğunuz yere gidin. **İleri**’yi seçin.
    4. **İnternet Ayarları** bölümünde **Ara sunucu olmadan Azure Site Recovery’ye bağlan** seçeneğini belirleyin, ardından **İleri**’yi seçin.
    5. **Önkoşul Denetimi** sayfasında birkaç öğe için denetimler çalıştırılır. Tamamlandığında, **İleri**’yi seçin.
    6. **MySQL Yapılandırması** bölümünde gerekli parolaları sağlayın ve **İleri**’yi seçin.
    7. **Ortam Ayrıntıları**’nda **Hayır**’ı seçin. VMware makinelerini korumaya gerek yoktur. Sonra **İleri**’yi seçin.
    8. **Yükleme Konumu** bölümünde **İleri**’yi seçin ve varsayılanı kabul edin.
    9. **Ağ Seçimi** bölümünde **İleri**’yi seçin ve varsayılanı kabul edin.
    10. **Özet** bölümünde **Yükle**’yi seçin.
    11. **Yükleme İlerleme Durumu**, size yükleme süreci hakkında bilgiler gösterir. Tamamlandığında, **Bitir**’i seçin. Bir pencere sistemin yeniden başlatılması hakkında bir ileti görüntüler. **Tamam**’ı seçin. Ardından, bir pencere yapılandırma sunucusunun bağlantı parolası hakkında bir ileti görüntüler. Parolayı panonuza kopyalayın ve güvenli bir yere kaydedin.
6. Sanal makinede, yapılandırma sunucusunda bir veya daha fazla yönetim hesabı oluşturmak için cspsconfigtool.exe dosyasını çalıştırın. Yönetim hesaplarının, geçirmek istediğiniz EC2 örneklerinde yönetici iznine sahip olduğundan emin olun.

Yapılandırma sunucusunu ayarlama işiniz bittiğinde portala geri dönün, **Yapılandırma Sunucusu** için oluşturmuş olduğunuz sunucuyu seçin. 3\. adım olan Hedef Hazırlama adımına ilerlemek için **Tamam**’u seçin.

### <a name="4-prepare-target"></a>4: hedefi hazırla

Bu bölümde, bu öğreticinin önceki kısımlarındaki [Azure kaynaklarını hazırlama](#prepare-azure-resources) bölümündeyken oluşturduğunuz kaynaklar hakkında bilgi girersiniz.

1. **Abonelik** bölümünde, [Azure’u Hazırlama](tutorial-prepare-azure.md) öğreticisi için kullandığınız Azure aboneliğini seçin.
2. Dağıtım modeli olarak **Kaynak Yöneticisi**’ni seçin.
3. Site Recovery, bir veya birden çok uyumlu Azure depolama hesabınızın ve ağınızın olup olmadığını doğrular. Bunlar, bu öğreticinin önceki kısımlarında [Azure kaynaklarını hazırlama](#prepare-azure-resources) bölümündeyken oluşturduğunuz kaynaklar olmalıdır.
4. İşiniz bittiğinde **Tamam**’ı seçin.

### <a name="5-prepare-replication-settings"></a>5: çoğaltma ayarlarını hazırlama

Çoğaltmayı etkinleştirmek için önce bir çoğaltma ilkesi oluşturmanız gerekir.

1. **Oluştur ve ilişkilendir '** i seçin.
2. **Ad** bölümüne **myReplicationPolicy** yazın.
3. Geri kalan varsayılan ayarları değiştirmeyin ve **Tamam**’ı seçerek ilkeyi oluşturun. Yeni ilke otomatik olarak yapılandırma sunucusu ile ilişkilendirilir.

**Altyapıyı hazırlama** altındaki beş bölümü de bitirdiğinizde **Tamam**’ı seçin.

## <a name="enable-replication"></a>Çoğaltmayı etkinleştirme

Geçirmek istediğiniz her sanal makine için çoğaltmayı etkinleştirin. Çoğaltma etkinleştirildiğinde Site Recovery otomatik olarak Mobility hizmetini yükler.

1. [Azure Portal](https://portal.azure.com) gidin.
1. Kasanızın sayfasındaki **Başlarken** bölümünde **Site Recovery**’i seçin.
2. **Şirket içi makineler ve Azure VM’ler için** bölümünde **1. Adım: Uygulamayı Çoğaltma**’yı seçin. Aşağıdaki bilgilerle sihirbazın sonraki sayfalarını tamamlayın. İşiniz bittiğinde her sayfada **Tamam**’ı seçin:
   - 1: Kaynağı yapılandırma

     |  |  |
     |-----|-----|
     | Kaynak: | **Şirket İçi**’ni seçin.|
     | Kaynak konumu:| Yapılandırma sunucusu EC2 örneğinizin adını girin.|
     |Makine türü: | **Fiziksel Makineler**’i seçin.|
     | İşlem sunucusu: | Açılır listeden yapılandırma sunucusunu seçin.|

   - 2: Hedefi yapılandırma

     |  |  |
     |-----|-----|
     | Hedef: | Varsayılanı değiştirmeyin.|
     | Abonelik: | Kullanmakta olduğunuz aboneliği seçin.|
     | Yük devretme sonrası kaynak grubu:| [Azure kaynaklarını hazırlama](#prepare-azure-resources) bölümünde oluşturduğunuz kaynak grubunu kullanın.|
     | Yük devretme sonrası dağıtım modeli: | **Resource Manager**’ı seçin.|
     | Depolama hesabı: | [Azure kaynaklarını hazırlama](#prepare-azure-resources) bölümünde oluşturduğunuz depolama hesabını seçin.|
     | Azure ağı: | **Seçili makineler için şimdi yapılandırın**’ı seçin.|
     | Yük devretme sonrası Azure ağı: | [Azure kaynaklarını hazırlama](#prepare-azure-resources) bölümünde oluşturduğunuz ağı seçin.|
     | Alt ağ: | Açılır listeden **varsayılan** seçeneğini belirleyin.|

   - 3: Fiziksel makineleri seçme

     **Fiziksel makine** seçeneğini belirleyin ve ardından geçirmek istediğiniz EC2 örneğinin **Ad**, **IP Adresi** ve **İşletim Sistemi Türü** bilgilerini girin. **Tamam**’ı seçin.

   - 4 Özellikleri yapılandırma

     Yapılandırma sunucusunda oluşturduğunuz hesabı ve ardından **Tamam**’ı seçin.

   - 5: Çoğaltma ayarlarını yapılandırma

     Açılır listede seçilen çoğaltma ilkesinin **myReplicationPolicy** olduğundan emin olun ve **Tamam**’ı seçin.

3. Sihirbaz tamamlandığında **Çoğaltmayı etkinleştir**’i seçin.

**Korumayı Etkinleştir** işinin ilerleme durumunu izlemek için **İzleme ve raporlar** > **İşler** > **Site Recovery işleri** bölümüne gidin. **Korumayı Sonlandır** işi çalıştırıldıktan sonra makine yük devretme için hazırdır.        

Bir sanal makine için çoğaltmayı etkinleştirdiğinizde, değişikliklerin geçerli olması ve portalda görüntülenmesi 15 dakika veya daha uzun sürebilir.

## <a name="run-a-test-failover"></a>Yük devretme testi çalıştırma

Yük devretme testi çalıştırdığınızda şunlar olur:

- Yük devretme için gerekli tüm koşulların karşılandığından emin olmak için bir önkoşul denetimi çalıştırılır.
- Yük devretme, bir Azure sanal makinesi oluşturulabilmesi için verileri işler. En son kurtarma noktasını seçerseniz verilerden bir kurtarma noktası oluşturulur.
- Önceki adımda işlenen veriler kullanılarak bir Azure sanal makinesi oluşturulur.

Portalda yük devretme testini çalıştırın:

1. Kasanızın sayfasında **Korumalı öğeler** > **Çoğaltılan Öğeler** bölümüne gidin. VM’yi ve ardından **Yük Devretme Testi**’ni seçin.
2. Yük devretme için kullanılacak bir kurtarma noktası seçin:
    - **En son işlenen**: VM yükü, Site Recovery tarafından işlenen en son kurtarma noktasına devredilir. Zaman damgası gösterilir. Bu seçenekle veri işlemeye zaman harcanmadığından düşük kurtarma süresi hedefi (RTO) elde edilir.
    - **Uygulamayla tutarlı olan son**: Bu seçenek, tüm VM’lerin yükünü uygulamayla tutarlı olan en son kurtarma noktasına devreder. Zaman damgası gösterilir.
    - **Özel**: Herhangi bir kurtarma noktası seçin.

3. **Yük Devretme Testi** bölümünde, yük devretme gerçekleştikten sonra Azure VM’lerinin bağlanacağı hedef Azure ağını seçin. Bu, [Azure kaynaklarını hazırlama](#prepare-azure-resources) aşamasında oluşturduğunuz ağ olmalıdır.
4. Yük devretmeyi başlatmak için **Tamam**'ı seçin. İlerleme durumunu izlemek için VM’yi seçip özelliklerini açın. Kasanızın sayfasında **Yük Devretme Testi**’ni de seçebilirsiniz. Bunu yapmak için **İzleme ve raporlar** > **İşler** >  **Site Recovery işleri**’ni seçin.
5. Yük devretme bittikten sonra, Azure VM çoğaltması Azure portalda görünür. VM’yi görüntülemek için **Sanal Makineler**’i seçin. Sanal makinenin uygun boyutta olduğundan, doğru ağa bağlandığından ve çalıştığından emin olun.
6. Şimdi Azure’da çoğaltılan sanal makineye bağlanabiliyor olmanız gerekir.
7. Yük devretme testi sırasında oluşturulan Azure sanal makinelerini silmek için, kurtarma planında **Yük devretme testini temizle**’yi seçin. Yük devretme testiyle ilişkili gözlemlerinizi **Notlar**’da kaydedin veya saklayın.

Bazı senaryolarda, yük devretme için ek işlemler gerekir. İşlemin tamamlanması 8-10 dakika sürer.

## <a name="migrate-to-azure"></a>Azure'a geçiş yapma

EC2 örneklerinin Azure sanal makinelerine geçişi için gerçek bir yük devretme çalıştırın:

1. **Korumalı öğeler** > **Çoğaltılan öğeler** bölümünde AWS örneklerini ve **Yük devretme**’yi seçin.
2. **Yük devretme** bölümünde yük devretmenin yapılacağı bir **Kurtarma Noktası** seçin. En son kurtarma noktasını seçin ve yük devretmeyi başlatın. Yük devretme işlemini **İşler** sayfasında takip edebilirsiniz.
1. Sanal makinenin, **Çoğaltılan öğeler** bölümünde görüntülendiğinden emin olun.
2. Her bir sanal makineye sağ tıklayın ve **Geçişi Tamamla**’yı seçin. Bu, şunları yapar:

   - Böylece geçiş işlemi tamamlanır, AWS VM için çoğaltma durdurulur ve sanal makine için Site Recovery faturalaması durdurulur.
   - Bu adım, çoğaltma verilerini temizler. Geçirilen VM 'Leri silmez. 

     ![Geçişi tamamlama](./media/migrate-tutorial-aws-azure/complete-migration.png)

> [!WARNING]
> *Devam eden yük devretme işlemini iptal etmeyin*. Yük devretme başlatılmadan önce VM çoğaltması durdurulur. Devam eden bir yük devretme işlemini iptal ederseniz yük devretme durdurulur, ancak VM yeniden çoğaltılmaz.  


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, AWS EC2 örneklerinin Azure sanal makinelerine nasıl geçirileceğini öğrendiniz. Azure sanal makineleri hakkında daha fazla bilgi için Windows sanal makinelerine yönelik öğreticilere geçin.

> [!div class="nextstepaction"]
> [Azure Windows sanal makine öğreticileri](../virtual-machines/windows/tutorial-manage-vm.md)
