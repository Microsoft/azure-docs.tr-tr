---
title: Azure Backup Sunucusu ile Azure VMware Çözüm VM 'lerini yedekleme
description: Azure VMware Çözüm ortamınızı, Azure Backup Sunucusu kullanarak sanal makineleri yedekleyecek şekilde yapılandırın.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 92affbf883215f0d051115fb64e9e7bf7a1430b3
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871786"
---
# <a name="back-up-azure-vmware-solution-vms-with-azure-backup-server"></a>Azure Backup Sunucusu ile Azure VMware Çözüm VM 'lerini yedekleme

Bu makalede, Azure VMware çözümünde çalışan VMware sanal makinelerini (VM 'Ler) Azure Backup Sunucusu ile yedekliyoruz. İlk olarak, [Azure VMware çözümü için Microsoft Azure Backup Server 'ı ayarlama](set-up-backup-server-for-azure-vmware-solution.md)bölümüne bakın.

Ardından, aşağıdakileri yapmak için gereken tüm yordamları inceleyeceğiz:

> [!div class="checklist"] 
> * Azure Backup Sunucusu, HTTPS üzerinden VMware sunucularıyla iletişim kurabilmesi için güvenli bir kanal ayarlayın. 
> * Azure Backup Sunucusu için hesap kimlik bilgilerini ekleyin. 
> * VCenter 'ı Azure Backup Sunucusu ekleyin. 
> * Yedeklemek istediğiniz VMware VM 'lerini içeren bir koruma grubu ayarlayın, yedekleme ayarlarını belirtin ve yedeklemeyi zamanlayın.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>VCenter Server 'a güvenli bağlantı oluşturma

Varsayılan olarak, Azure Backup Sunucusu HTTPS üzerinden VMware sunucularıyla iletişim kurar. HTTPS bağlantısını kurmak için, VMware sertifika yetkilisi (CA) sertifikasını indirin ve Azure Backup Sunucusu alın.

### <a name="set-up-the-certificate"></a>Sertifikayı ayarlama

1. Tarayıcıda, Azure Backup Sunucusu makinesinde, vSphere Web Istemcisi URL 'sini girin.

   > [!NOTE] 
   > VMware **Başlarken** sayfası görünmezse, bağlantı ve tarayıcı ara sunucu ayarlarını doğrulayın ve yeniden deneyin.

1. VMware **Başlarken** sayfasında, **Güvenilen kök CA sertifikalarını indir**' i seçin.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="vSphere Web Istemcisi":::

1. **download.zip** dosyasını Azure Backup sunucusu makinesine kaydedin ve ardından içeriğini içeren **CERT** klasörüne ayıklayın:

   - .0 ve. 1 gibi numaralandırılmış bir sırayla başlayan bir uzantıya sahip kök sertifika dosyası.
   - . R0 veya. R1 gibi bir sırayla başlayan bir uzantıya sahip CRL dosyası.

1. **Sertifikalar** klasöründe, kök sertifika dosyasına sağ tıklayın ve uzantıyı **. CRT** olarak değiştirmek için **Yeniden Adlandır** ' ı seçin.

   Dosya simgesi bir kök sertifikayı temsil eden bir olarak değişir.

1. Kök sertifikaya sağ tıklayın ve **sertifikayı yükler**' i seçin.

1. **Sertifika Içeri aktarma sihirbazında**, sertifika için hedef olarak **yerel makine** ' yi seçin ve **İleri**' yi seçin.

   ![Sihirbaza hoş geldiniz sayfası](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > İstenirse, bilgisayarda değişikliklere izin vermek istediğinizi onaylayın.

1. **Tüm sertifikaları aşağıdaki depolama alanına yerleştir**' i seçin ve sertifika deposunu seçmek için **Araştır** ' ı seçin.

   ![Sertifika depolama alanı](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. Hedef klasör olarak **Güvenilen kök sertifika yetkilileri** ' ni seçin ve **Tamam**' ı seçin.

1. Ayarları gözden geçirin ve sertifikayı içeri aktarmaya başlamak için **son** ' u seçin.

   ![Sertifikanın doğru klasörde olduğunu doğrulama](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. Sertifika içeri aktarma onaylandıktan sonra, bağlantınızın güvenli olduğunu onaylamak için vCenter sunucusunda oturum açın.

### <a name="enable-tls-12-on-azure-backup-server"></a>Azure Backup Sunucusu TLS 1,2 'yi etkinleştirme

VMware 6,7 üzerinde iletişim kuralı olarak TLS etkinleştirilmiş. 

1. Aşağıdaki kayıt defteri ayarlarını kopyalayıp Not defteri 'ne yapıştırın. Sonra dosyayı TLS olarak kaydedin. . Txt uzantısı olmayan REG.

   ```
   
   Windows Registry Editor Version 5.00
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   ```

1. TLS öğesine sağ tıklayın. REG dosyasını seçin ve ayarları kayıt defterine eklemek için **Birleştir** veya **Aç** ' ı seçin.


## <a name="add-the-account-on-azure-backup-server"></a>Hesabı Azure Backup Sunucusu ekleyin

1. Azure Backup sunucusu açın ve Azure Backup sunucusu konsolunda **Yönetim**  >  **üretim sunucuları**  >  **VMware 'yi Yönet**' i seçin.

   ![Azure Backup Sunucusu konsolu](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. **Kimlik bilgilerini Yönet** Iletişim kutusunda **Ekle**' yi seçin.

   ![Kimlik bilgilerini Yönet iletişim kutusunda Ekle ' yi seçin.](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. **Kimlik bilgisi ekle** iletişim kutusunda yeni kimlik bilgisi için bir ad ve açıklama girin. VMware sunucusunda tanımladığınız Kullanıcı adını ve parolayı belirtin.

   > [!NOTE] 
   > VMware sunucusu ve Azure Backup Sunucusu aynı etki alanında değilse, **Kullanıcı adı** kutusunda etki alanını belirtin.

   ![Kimlik bilgisi Ekle iletişim kutusu Azure Backup Sunucusu](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. Yeni kimlik bilgisini eklemek için **Ekle** ' yi seçin.

   ![Ekran görüntüsü yeni kimlik bilgileriyle Azure Backup Sunucusu kimlik bilgilerini Yönet iletişim kutusunu gösterir.](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>VCenter Server 'ı Azure Backup Sunucusu ekleyin

1. Azure Backup sunucusu konsolunda **Yönetim**  >  **üretim sunucuları**  >  **Ekle**' yi seçin.

   ![Üretim sunucusu ekleme Sihirbazı 'Nı aç](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. **VMware sunucuları**' nı seçin ve **İleri**' yi seçin.

   ![Üretim sunucusu ekleme Sihirbazı](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. VCenter 'ın IP adresini belirtin.

   ![VMware sunucusu belirtin](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. **SSL bağlantı noktası** kutusuna vCenter ile iletişim kurmak için kullanılan bağlantı noktasını girin.

   > [!TIP] 
   > Bağlantı noktası 443 varsayılan bağlantı noktasıdır, ancak vCenter 'niz farklı bir bağlantı noktasında dinliyorsa bunu değiştirebilirsiniz.

1. **Kimlik bilgilerini belirtin** kutusunda, önceki bölümde oluşturduğunuz kimlik bilgisini seçin.

1. **Ekle** ' yi seçerek sunucu listesine vCenter ekleyin ve **İleri**' yi seçin.

   ![VMware sunucusu ve kimlik bilgisi ekleme](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. **Özet** sayfasında, vCenter ' ı Azure Backup sunucusu eklemek için **Ekle** ' yi seçin.

   Yeni sunucu hemen eklenir. vCenter 'a bir aracı gerekmez.

   ![VMware sunucusunu Azure Backup Sunucusu Ekle](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. **Son** sayfasında, ayarları gözden geçirin ve ardından **Kapat**' ı seçin.

   ![Son sayfa](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   **Üretim sunucusu** altında listelenen vCenter Server 'ı şu şekilde görürsünüz:
   - **VMware sunucusu** olarak yazın 
   - Aracı durumu **Tamam** olarak 
   
      **Aracı durumu** **bilinmiyor** olarak görürseniz, **Yenile**' yi seçin.

## <a name="configure-a-protection-group"></a>Koruma grubu yapılandırma

Koruma grupları birden çok VM toplar ve aynı veri saklama ve yedekleme ayarlarını gruptaki tüm VM 'lere uygular.

1. Azure Backup sunucusu konsolunda, **koruma**  >  **Yeni**' yi seçin.

   ![Yeni koruma grubu oluşturma Sihirbazı 'nı açın](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. **Yeni koruma grubu oluşturma** Sihirbazı 'na hoş geldiniz sayfasında **İleri**' yi seçin.

   ![Yeni koruma grubu oluşturma Sihirbazı iletişim kutusu](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. **Koruma grubu türünü seçin** sayfasında **sunucular**' ı seçin ve ardından **İleri**' yi seçin. **Grup üyelerini seçin** sayfası görüntülenir.

1. **Grup üyelerini seçin** sayfasında, yedeklemek Istediğiniz VM 'leri (veya VM klasörleri) seçin ve ardından **İleri**' yi seçin.

   > [!NOTE]
   > Bir klasör veya VM seçtiğinizde, bu klasörün içindeki klasörler de yedekleme için seçilir. Yedeklemek istemediğiniz klasörlerin veya VM 'Lerin işaretini kaldırabilirsiniz. Bir VM veya klasör zaten yedekleniyorsa, sanal makine için yinelenen kurtarma noktalarının oluşturulmamasını sağlayan bu seçeneği seçemezsiniz.

   ![Grup üyelerini seçin](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. **Veri koruma yöntemini seçin** sayfasında, koruma grubu ve koruma ayarları için bir ad girin. 

1. Kısa vadeli korumayı **diske** ayarlayın, çevrimiçi korumayı etkinleştirin ve sonra **İleri**' yi seçin.

   ![Veri koruma yöntemini seçme](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Verilerin diske ne kadar süreyle yedeklenmek istediğinizi belirtin.

   - **Bekletme aralığı**: disk kurtarma noktalarının saklanacağı gün sayısı.
   - **Hızlı tam yedekleme**: disk kurtarma noktalarının ne sıklıkta alındığı. Kısa vadeli yedeklemelerin oluşma zamanlarını veya tarihleri değiştirmek için **Değiştir**' i seçin.

   :::image type="content" source="media/azure-vmware-solution-backup/new-protection-group-specify-short-term-goals.png" alt-text="Disk tabanlı koruma için kısa vadeli hedeflerinizi belirtin":::

1. **Disk depolama ayırmayı gözden geçir** SAYFASıNDA, VM yedeklemeleri için sunulan disk alanını gözden geçirin.

   - Önerilen disk ayırmaları belirttiğiniz bekletme aralığına, iş yükü türüne ve korunan verilerin boyutuna göre belirlenir. Gerekli değişiklikleri yapın ve ardından **İleri**' yi seçin.
   - **Veri boyutu:** Koruma grubundaki verilerin boyutu.
   - **Disk alanı:** Koruma grubu için önerilen miktarda disk alanı. Bu ayarı değiştirmek istiyorsanız, her veri kaynağı arttıkça tahmin ettiğiniz miktardan daha fazla alan seçin.
   - **Depolama havuzu ayrıntıları:** Depolama havuzunun toplam ve kalan disk boyutunu içeren durumunu gösterir.

   :::image type="content" source="media/azure-vmware-solution-backup/review-disk-allocation.png" alt-text="Depolama havuzunda verilen disk alanını gözden geçirme":::

   > [!NOTE]
   > Bazı senaryolarda raporlanan veri boyutu gerçek VM boyutundan daha yüksek. Sorunun farkındayız ve şu anda araştırıyoruz.

1. **Çoğaltma oluşturma yöntemini seçin** sayfasında, ilk yedeklemeyi nasıl almak istediğinizi belirtin ve **İleri**' yi seçin.

   - Varsayılan değer **otomatik olarak ağ üzerinden** ve **Şimdi**. Varsayılan değeri kullanırsanız, yoğun olmayan bir zaman belirtin. **Daha sonra** seçeneğini belirlerseniz, gün ve saat belirtin.
   - Büyük miktarlarda veri veya en iyi olmayan ağ koşulları için, çıkarılabilir medya kullanarak verileri çevrimdışı olarak çoğaltmayı göz önünde bulundurun.

   ![Çoğaltma oluşturma yöntemini seçin](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. **Tutarlılık denetimi seçenekleri** için tutarlılık denetimlerinin nasıl ve ne zaman otomatikleştirildiğini seçin ve **İleri**' yi seçin.

   - Çoğaltma verileri tutarsız hale geldiğinde veya bir küme zamanlaması üzerinde tutarlılık denetimleri gerçekleştirebilirsiniz.
   - Otomatik tutarlılık denetimlerini yapılandırmak istemiyorsanız, koruma grubuna sağ tıklayarak **tutarlılık denetimi** yapabilirsiniz.

1. **Çevrimiçi koruma verilerini belirtin** sayfasında, yedeklemek Istediğiniz VM 'LERI veya VM klasörlerini seçin ve ardından **İleri**' yi seçin. 

   > [!TIP]
   > Üyeleri tek tek seçebilir veya **Tümünü Seç ' i seçerek tüm** üyeleri seçebilirsiniz.

   ![Çevrimiçi koruma verilerini belirtin](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. **Çevrimiçi yedekleme zamanlamasını belirtin** sayfasında, verileri yerel depolamadan Azure 'a ne sıklıkta yedeklemek istediğinizi belirtin. 

   - Zamanlamaya göre oluşturulacak verilerin bulut kurtarma noktaları. 
   - Kurtarma noktası oluşturulduktan sonra, Azure 'daki kurtarma hizmetleri kasasına aktarılır.

   ![Çevrimiçi Yedekleme zamanlamasını belirtin](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. **Çevrimiçi saklama Ilkesini belirtin** sayfasında, yedeklerden Azure 'a olan kurtarma noktalarını ne kadar süreyle saklamak istediğinizi belirtin.

   - Azure 'da verileri ne kadar süreyle saklayabilmeniz için zaman sınırı yoktur.
   - Tek sınır, korunan örnek başına 9.999 taneden fazla kurtarma noktasına sahip olamaz. Bu örnekte, korumalı örnek VMware sunucusudur.

   ![Çevrimiçi bekletme ilkesini belirtin](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. **Özet** sayfasında, ayarları gözden geçirin ve ardından **Grup Oluştur**' u seçin.

   ![Koruma grubu üyesi ve ayar Özeti](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-the-azure-backup-server-console"></a>Azure Backup Sunucusu konsolu ile izleme

Koruma grubunu Azure VMware Çözüm VM 'lerini yedekleyecek şekilde yapılandırdıktan sonra, Azure Backup Sunucusu konsolunu kullanarak yedekleme işinin ve uyarının durumunu izleyebilirsiniz. Şunları izleyebilirsiniz.

- **İzleme** görev alanında:
   - **Uyarılar**' ın altında hataları, uyarıları ve genel bilgileri izleyebilirsiniz.  Etkin ve etkin olmayan uyarıları görüntüleyebilir ve e-posta bildirimleri ayarlayabilirsiniz.
   - **İşler** altında, belirli bir korumalı veri kaynağı veya koruma grubu için Azure Backup sunucusu tarafından başlatılan işleri görüntüleyebilirsiniz. İş ilerleme durumunu izleyebilir veya işler tarafından tüketilen kaynakları kontrol edebilirsiniz.
- **Koruma** görev bölmesinde, koruma grubundaki birim ve paylaşımların durumunu kontrol edebilirsiniz. Kurtarma ayarları, disk ayırma ve yedekleme zamanlaması gibi yapılandırma ayarlarını da denetleyebilirsiniz.
- **Yönetim** görev alanında, **diskler, çevrimiçi** ve **aracılar** sekmelerini görüntüleyerek depolama havuzundaki disklerin durumunu DENETLEYEBILIR, Azure 'a kaydedebilir ve DPM aracısının durumunu dağıttınız.

:::image type="content" source="media/azure-vmware-solution-backup/monitor-backup-jobs.png" alt-text="Azure Backup Sunucusu 'de yedekleme işlerinin durumunu izleme":::

## <a name="restore-vmware-virtual-machines"></a>VMware sanal makinelerini geri yükleme

Azure Backup Sunucusu Yönetici Konsolu, kurtarılabilir verileri bulmanın iki yolu vardır. Arama yapabilir veya gezinebilirsiniz. Verileri kurtardığınızda, verileri veya bir VM 'yi aynı konuma geri yüklemek istemeyebilirsiniz. Bu nedenle Azure Backup Sunucusu, VMware VM yedeklemeleri için üç kurtarma seçeneğini destekler:

- **Özgün konum kurtarma (olr)**: KORUMALı bir VM 'yi özgün konumuna geri yüklemek için olr 'yi kullanın. Bir VM 'yi yalnızca yedekleme gerçekleşmesinden sonra hiçbir disk eklenmediyse veya silinmediyse, özgün konumuna geri yükleyebilirsiniz. Diskler eklendiyse veya silinirse, alternatif bir konum kurtarma kullanmanız gerekir.
- **Alternatif Konum Kurtarma (ALR)**: özgün VM eksik olduğunda veya özgün VM 'yi rahatsız etmek istemediğiniz durumlarda kullanın. Bir ESXi konağının, kaynak havuzunun, klasörün ve depolama veri deposunun ve yolun konumunu belirtin. Geri yüklenen VM 'yi özgün VM 'den ayırt etmeye yardımcı olmak için, Azure Backup Sunucusu *"-Kurtarılan"* öğesini VM adına ekler.
- **Tek dosya konumu kurtarma (ıLR)**: korunan VM bir Windows Server sanal makinesi ise, Azure Backup sunucusu ILR ÖZELLIĞI kullanılarak sanal makine içindeki tek tek dosyalar veya klasörler kurtarılabilir. Tek tek dosyaları kurtarmak için bu makalenin ilerleyen kısımlarında yer alarak bulunan yordama bakın. Bir VM 'den tek bir dosyanın geri yüklenmesi yalnızca Windows VM ve disk kurtarma noktaları için kullanılabilir.

### <a name="restore-a-recovery-point"></a>Kurtarma noktasını geri yükleme

1. Azure Backup Sunucusu Yönetici Konsolu **Kurtarma** görünümünü seçin. 

1. Kurtarmak istediğiniz VM 'yi bulmak için, **tarama** bölmesini kullanın veya filtre uygulayın. Bir VM veya klasör seçtikten sonra, bölüm için * * kurtarma noktaları kullanılabilir kurtarma noktalarını görüntüler.

   ![Kullanılabilir kurtarma noktaları](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. **Için kurtarma noktaları** bölmesinde, bir kurtarma noktası çekilirken bir tarih seçin. Kalın yazı tipinde takvim tarihleri kullanılabilir kurtarma noktalarına sahip. Alternatif olarak, VM 'ye sağ tıklayıp **tüm kurtarma noktalarını göster** ' i seçip listeden kurtarma noktasını seçebilirsiniz.

   > [!NOTE] 
   > Kısa vadeli koruma için daha hızlı kurtarma için disk tabanlı bir kurtarma noktası seçin. Kısa vadeli kurtarma noktalarının süresi dolduktan sonra, kurtarmak için yalnızca **çevrimiçi** kurtarma noktaları görürsünüz.

1. Çevrimiçi kurtarma noktasından kurtarmadan önce, hazırlama konumunun kurtarmak istediğiniz sanal makinenin tam sıkıştırılmamış boyutunu barındırmak için yeterli boş alan içerdiğinden emin olun. Hazırlama konumu, **abonelik ayarlarını yapılandırma Sihirbazı** çalıştırılarak görüntülenebilir veya değiştirilebilir.

   :::image type="content" source="media/azure-vmware-solution-backup/mabs-recovery-folder-settings.png" alt-text="Kurtarma klasörü ayarlarını Azure Backup Sunucusu":::

1. **Kurtarma Sihirbazı 'nı** açmak için **kurtar** ' ı seçin.

   ![Kurtarma Sihirbazı, kurtarma seçim sayfasını gözden geçirin](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. **Kurtarma seçeneklerini belirtin** ekranına gitmek için **İleri ' yi** seçin. **Kurtarma türü seç** ekranına gitmek için **İleri ' yi** tekrar seçin. 

   > [!NOTE]
   > VMware iş yükleri ağ bant genişliği azaltmayı etkinleştirmeyi desteklemez.

1. **Kurtarma türünü seçin** sayfasında, özgün örneğe veya yeni bir konuma kurtarın.

   - **Özgün örneğe kurtar**' ı seçerseniz, sihirbazda başka seçimler yapmanıza gerek kalmaz. Özgün örnek için veriler kullanılır.
   - **Herhangi bir konakta sanal makine olarak kurtar**' ı seçerseniz, **hedef belirtin** ekranında **ESXi Konağı**, **kaynak havuzu**, **klasör** ve **yol** bilgilerini girin.

   ![Kurtarma türü seçin sayfası](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. **Özet** sayfasında, ayarlarınızı gözden geçirin ve kurtarma işlemini başlatmak için **kurtar** ' ı seçin. 

   **Kurtarma durumu** ekranı, kurtarma işleminin ilerlemesini gösterir.

### <a name="restore-an-individual-file-from-a-vm"></a>Bir VM 'den tek bir dosyayı geri yükleme

Korunan bir VM kurtarma noktasından tek tek dosyaları geri yükleyebilirsiniz. Bu özellik yalnızca Windows Server VM 'Leri için kullanılabilir. Tek tek dosyaları geri yükleme, VMDK 'ye gözatıp kurtarma işlemine başlamadan önce istediğiniz dosyaları bulabilmeniz dışında tüm VM 'leri geri yüklemeye benzer. 

> [!NOTE]
> Bir VM 'den tek bir dosyanın geri yüklenmesi yalnızca Windows VM ve disk kurtarma noktaları için kullanılabilir.

1. Azure Backup Sunucusu Yönetici Konsolu **Kurtarma** görünümünü seçin.

1. Kurtarmak istediğiniz VM 'yi bulmak için, **tarama** bölmesini kullanın veya filtre uygulayın. Bir VM veya klasör seçtikten sonra, bölüm için * * kurtarma noktaları kullanılabilir kurtarma noktalarını görüntüler.

   ![Kullanılabilir kurtarma noktaları](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. **Için kurtarma noktaları** bölmesinde, istenen kurtarma noktalarını içeren tarihi seçmek için takvimi kullanın. Yedekleme ilkesinin nasıl yapılandırıldığına bağlı olarak, tarihler birden fazla kurtarma noktasına sahip olabilir. 

1. Kurtarma noktasının alındığı günü seçtikten sonra, doğru **kurtarma süresini** seçtiğinizden emin olun. 

   > [!NOTE]
   > Seçilen tarihin birden çok kurtarma noktası varsa, kurtarma **zamanı** açılır menüsünde bunu seçerek kurtarma noktanızı seçin. 

   Kurtarma noktasını seçtikten sonra, kurtarılabilir öğelerin listesi **yol** bölmesinde görünür.

1. Kurtarmak istediğiniz dosyaları bulmak için, **yol** bölmesinde **kurtarılabilir öğe** sütunundaki öğeye çift tıklayarak açın. Sonra kurtarmak istediğiniz dosya veya klasörleri seçin. Birden çok öğe seçmek için, her öğeyi seçerken **CTRL** tuşunu seçin. **Kurtarılabilir öğe** sütununda görünen dosya veya klasör listesinde arama yapmak için **yol** bölmesini kullanın.
    
   > [!NOTE]
   > **Aşağıdaki arama listesi** alt klasörlerde arama yapmaz. Alt klasörlerde arama yapmak için klasörü çift tıklayın. Alt bir klasörden üst klasöre geçmek için **yukarı** düğmesini kullanın. Birden çok öğe (dosya ve klasör) seçebilirsiniz, ancak aynı ana klasörde olmaları gerekir. Aynı kurtarma işinde birden çok klasörden öğeleri kurtaramazsınız.

   ![Kurtarma seçimini İncele](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. Kurtarma için öğeleri seçtiğinizde, Yönetici Konsolu araç şeridinde **Kurtarma Sihirbazı**'nı açmak için **kurtar** ' ı seçin. **Kurtarma Sihirbazı**'Nda, **Kurtarma seçimini İncele** ekranı, kurtarılacak seçili öğeleri gösterir.

1. **Kurtarma seçeneklerini belirtin** ekranında, aşağıdaki adımlardan birini yapın:

   - Ağ bant genişliği azaltmayı etkinleştirmek için **Değiştir** ' i seçin. **Kısıtlama** iletişim kutusunda, açmak için **ağ bant genişliği kullanımını azaltmayı etkinleştir** ' i seçin. Etkinleştirildikten sonra **ayarları** ve **iş zamanlamasını** yapılandırın.
   - Ağ azaltmayı devre dışı bırakmak için **İleri ' yi** seçin.

1. **Kurtarma türü seçin** ekranında, **İleri**' yi seçin. Dosya veya klasörlerinizi yalnızca bir ağ klasörüne kurtarabilirsiniz.

1. **Hedef belirtin** ekranında, dosyalarınız veya klasörleriniz için bir ağ konumu bulmak üzere **Araştır** ' ı seçin. Azure Backup Sunucusu, kurtarılan tüm öğelerin kopyalandığı bir klasör oluşturur. Klasör adı MABS_day-ay-yıl ön ekine sahiptir. Kurtarılan dosyalar veya klasör için bir konum seçtiğinizde, bu konumun ayrıntıları sağlanır.

   ![Dosyaların kurtarılacağı konumu belirtin](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. **Kurtarma seçeneklerini belirtin** ekranında, hangi güvenlik ayarının uygulanacağını seçin. Ağ bant genişliği kullanımını azaltmayı tercih edebilirsiniz, ancak daraltma varsayılan olarak devre dışıdır. Ayrıca, **San kurtarma** ve **bildirim** etkin değildir.

1. **Özet** ekranında, ayarlarınızı gözden geçirin ve kurtarma işlemini başlatmak için **kurtar** ' ı seçin. **Kurtarma durumu** ekranı, kurtarma işleminin ilerlemesini gösterir.

## <a name="next-steps"></a>Sonraki adımlar

Azure VMware Çözüm VM 'lerinizi Azure Backup Sunucusu yedeklemeyi artık kapsadığınıza göre şunları öğrenmek isteyebilirsiniz: 

- [Azure Backup Sunucusu yedeklemeler ayarlanırken sorun giderme](../backup/backup-azure-mabs-troubleshoot.md)
- [Azure VMware Çözüm VM 'lerinin yaşam döngüsü yönetimi](lifecycle-management-of-azure-vmware-solution-vms.md)
