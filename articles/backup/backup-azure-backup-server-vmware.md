---
title: Azure Backup Sunucusu ile VMware VM 'lerini yedekleme
description: Bu makalede, VMware vCenter/ESXi sunucusunda çalışan VMware VM 'lerini yedeklemek için Azure Backup Sunucusu nasıl kullanacağınızı öğrenin.
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: d1c8ec249e010d75bbe96f5c70072f41b9738370
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173370"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Azure Backup Sunucusu ile VMware VM 'lerini yedekleme

Bu makalede Azure Backup Sunucusu kullanarak Azure 'a VMware ESXi Konakları/vCenter Server üzerinde çalışan VMware VM 'lerinin nasıl yedekleneceği açıklanmaktadır.

Bu makalede nasıl yapılacağı açıklanmaktadır:

- Azure Backup Sunucusu, HTTPS üzerinden VMware sunucularıyla iletişim kurabilmesi için güvenli bir kanal ayarlayın.
- Azure Backup Sunucusu VMware sunucusuna erişmek için tarafından kullanılan bir VMware hesabı ayarlayın.
- Azure Backup için hesap kimlik bilgilerini ekleyin.
- VCenter veya ESXi sunucusunu Azure Backup Sunucusu ekleyin.
- Yedeklemek istediğiniz VMware VM 'lerini içeren bir koruma grubu ayarlayın, yedekleme ayarlarını belirtin ve yedeklemeyi zamanlayın.

## <a name="before-you-start"></a>Başlamadan önce

- Yedekleme-sürüm 6,5, 6,0 ve 5,5 için desteklenen vCenter/ESXi 'nin bir sürümünü çalıştırdığınızı doğrulayın.
- Azure Backup Sunucusu ayarladığınızdan emin olun. Yapmadıysanız, başlamadan önce [bunu yapın](backup-azure-microsoft-azure-backup.md) . En son güncelleştirmelerle Azure Backup Sunucusu çalıştırıyor olmanız gerekir.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>vCenter Server güvenli bir bağlantı oluşturun

Varsayılan olarak, Azure Backup Sunucusu HTTPS üzerinden VMware sunucularıyla iletişim kurar. HTTPS bağlantısını kurmak için, VMware sertifika yetkilisi (CA) sertifikasını indirin ve Azure Backup Sunucusu alın.

### <a name="before-you-begin"></a>Başlamadan önce

- HTTPS kullanmak istemiyorsanız, [Tüm VMware sunucuları IÇIN HTTPS sertifika doğrulamasını devre dışı](backup-azure-backup-server-vmware.md#disable-https-certificate-validation)bırakabilirsiniz.
- Genellikle Azure Backup Sunucusu makinesindeki bir tarayıcıdan vSphere Web Istemcisini kullanarak vCenter/ESXi sunucusuna bağlanırsınız. Bunu ilk yaptığınızda bağlantı güvenli olmaz ve aşağıdaki gösterir.
- Azure Backup Sunucusu yedeklemeleri nasıl işlediğini anlamanız önemlidir.
  - İlk adım olarak, verileri yerel disk depolama alanına yedekler Azure Backup Sunucusu. Azure Backup Sunucusu, korunan veriler için Azure Backup Sunucusu disk kurtarma noktalarını depolayan bir depolama havuzu, bir dizi disk ve birim kullanır. Depolama havuzu doğrudan bağlı depolama (DAS), bir Fiber Kanal SAN veya Iscsı depolama cihazı veya SAN olabilir. VMware VM verilerinizin yerel yedeklemesi için yeterli depolama alanı olduğundan emin olmanız önemlidir.
  - Azure Backup Sunucusu sonra yerel disk depolamadan Azure 'a yedekler.
  - Ne kadar depolama alanına ihtiyacınız olduğunu anlamak için [yardım alın](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) . Bilgiler DPM içindir, ancak Azure Backup Sunucusu için de kullanılabilir.

### <a name="set-up-the-certificate"></a>Sertifikayı ayarlama

Güvenli bir kanalı aşağıdaki şekilde ayarlayın:

1. Azure Backup Sunucusu tarayıcıda, vSphere Web Istemcisi URL 'sini girin. Oturum açma sayfası görünmezse, bağlantı ve tarayıcı proxy ayarlarını doğrulayın.

    ![vSphere Web Istemcisi](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. VSphere Web Istemcisi oturum açma sayfasında, **Güvenilen kök CA sertifikalarını indir**' e tıklayın.

    ![Güvenilen kök CA sertifikasını indir](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. **İndirme** adlı bir dosya indirilir. Tarayıcınıza bağlı olarak, dosyayı açmak veya kaydetmek isteyip istemediğinizi soran bir ileti alırsınız.

    ![CA sertifikasını indir](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Dosyayı bir. zip uzantısıyla Azure Backup Sunucusu makineye kaydedin.

5. **Tümünü ayıkla** >  **. zip dosyasını indir** ' e sağ tıklayın. . Zip dosyası, içeriğini içeren **CERT** klasörüne ayıklar:
   - .0 ve. 1 gibi numaralandırılmış bir diziyle başlayan bir uzantıya sahip kök sertifika dosyası.
   - CRL dosyası. R0 veya. R1 gibi bir sırayla başlayan bir uzantıya sahiptir. CRL dosyası bir sertifikayla ilişkilendirilir.

    ![İndirilen sertifikalar](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. **Sertifikalar** klasöründe kök sertifika dosyasına sağ tıklayıp **Yeniden Adlandır**>.

    ![Kök sertifikayı yeniden adlandır](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. Kök sertifikanın uzantısını. CRT olarak değiştirin ve onaylayın. Dosya simgesi bir kök sertifikayı temsil eden bir olarak değişir.

8. Kök sertifikaya sağ tıklayın ve açılır menüden **sertifikayı yükler**' i seçin.

9. **Sertifika Içeri aktarma sihirbazında**, sertifika için hedef olarak **yerel makine** ' yi seçin ve ardından **İleri**' ye tıklayın. Bilgisayarda değişikliklere izin vermek isteyip istemediğiniz sorulursa onaylayın.

    ![Sihirbaza hoş geldiniz](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. **Sertifika deposu** sayfasında, **tüm sertifikaları aşağıdaki depolama alanına yerleştir**' i seçin ve ardından, sertifika deposunu seçmek için, **Gözden** geçirme ' ye tıklayın.

    ![Sertifika depolama alanı](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. **Sertifika deposu Seç**' te, sertifikalar için hedef klasör olarak **Güvenilen kök sertifika yetkilileri** ' ni seçin ve ardından **Tamam**' a tıklayın.

    ![Sertifika hedef klasörü](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. **Sertifika Içeri aktarma Sihirbazı 'Nı tamamladıktan**sonra klasörü doğrulayıp **son**' a tıklayın.

    ![Sertifikanın doğru klasörde olduğunu doğrulama](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. Sertifika içeri aktarma onaylandıktan sonra, bağlantınızın güvenli olduğunu onaylamak için vCenter Server oturum açın.

### <a name="disable-https-certificate-validation"></a>HTTPS sertifika doğrulamasını devre dışı bırak

Kuruluşunuz dahilinde güvenli sınırlarınız varsa ve VMware sunucuları ile Azure Backup Sunucusu makine arasında HTTPS protokolünü kullanmak istemiyorsanız, HTTPS 'yi şu şekilde devre dışı bırakın:

1. Aşağıdaki metni kopyalayıp bir. txt dosyasına yapıştırın.

       ```text
      Windows kayıt defteri Düzenleyicisi sürüm 5,00 [HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare] "IgnoreCertificateValidation" = DWORD: 00000001
       ```

2. Dosyayı Azure Backup Sunucusu makinesinde **Disablesecuyeniden doğrulama. reg**adıyla kaydedin.

3. Kayıt defteri girişini etkinleştirmek için dosyaya çift tıklayın.

## <a name="create-a-vmware-role"></a>VMware rolü oluşturma

Azure Backup Sunucusu, v-Center Server/ESXi konağına erişim izinleri olan bir kullanıcı hesabına ihtiyaç duyuyor. Belirli ayrıcalıklara sahip bir VMware rolü oluşturun ve ardından bir kullanıcı hesabını rolüyle ilişkilendirin.

1. VCenter Server (veya vCenter Server kullanmıyorsanız ESXi ana bilgisayarında) oturum açın.
2. **Gezgin** panelinde, **Yönetim**' e tıklayın.

    ![Yönetim](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. **Yönetim** > **Roller**' de rol ekle simgesine (+ Symbol) tıklayın.

    ![Rol Ekle](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. **Rol oluşturma** > rol **adı**' nda *backupadminrole*yazın. Rol adı dilediğiniz gibi olabilir, ancak rolün amacı için tanınabilir olmalıdır.

5. Aşağıdaki tabloda özetlenen ayrıcalıkları seçin ve ardından **Tamam**' a tıklayın.  Yeni rol, **Roller** panelinde listede görüntülenir.
   - Üst etiketin yanındaki simgeye tıklayarak üst etiketi genişletin ve alt ayrıcalıkları görüntüleyin.
   - VirtualMachine ayrıcalıklarını seçmek için, üst alt hiyerarşiye birkaç düzey gitmeniz gerekir.
   - Üst ayrıcalık içindeki tüm alt ayrıcalıkları seçmeniz gerekmez.

    ![Üst alt öğe ayrıcalık hiyerarşisi](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Rol izinleri

**6.5/6.0** | **5,5**
--- | ---
DataStore. AllocateSpace | DataStore. AllocateSpace
Global. ManageCustomFields | Global. ManageCustomFields
Global. SetCustomField |
Host. Local. CreateVM | Network. assign
Network. assign |
Resource. Atamavmtopool |
VirtualMachine. config. AddNewDisk  | VirtualMachine. config. AddNewDisk
VirtualMachine. config. AdvancedConfig| VirtualMachine. config. AdvancedConfig
VirtualMachine. config. ChangeTracking| VirtualMachine. config. ChangeTracking
VirtualMachine. config. HostUSBDevice |
VirtualMachine. config. QueryUnownedFiles |
VirtualMachine. config. Swapyerleştirmesini| VirtualMachine. config. Swapyerleştirmesini
VirtualMachine. etkileşim. PowerOff| VirtualMachine. etkileşim. PowerOff
VirtualMachine. Inventory. Create| VirtualMachine. Inventory. Create
VirtualMachine. sağlama. DiskRandomAccess |
VirtualMachine. sağlama. DiskRandomRead | VirtualMachine. sağlama. DiskRandomRead
VirtualMachine. State. CreateSnapshot | VirtualMachine. State. CreateSnapshot
VirtualMachine. State. RemoveSnapshot | VirtualMachine. State. RemoveSnapshot

## <a name="create-a-vmware-account"></a>VMware hesabı oluşturma

1. VCenter Server **Gezgin** panelinde **Kullanıcılar ve gruplar**' a tıklayın. VCenter Server kullanmıyorsanız, hesabı uygun ESXi ana bilgisayarında oluşturun.

    ![Kullanıcılar ve gruplar seçeneği](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    **VCenter kullanıcıları ve grupları** paneli görüntülenir.

2. **VCenter kullanıcıları ve grupları** panelinde, **Kullanıcılar** sekmesini seçin ve ardından Kullanıcı ekle simgesine (+ Symbol) tıklayın.

    ![vCenter kullanıcıları ve grupları paneli](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. **Yeni Kullanıcı** iletişim kutusunda, **Tamam**> Kullanıcı bilgilerini ekleyin. Bu yordamda, Kullanıcı adı BackupAdmin ' dir.

    ![Yeni Kullanıcı iletişim kutusu](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. Kullanıcı hesabını rolüyle ilişkilendirmek için **Gezgin** panelinde **genel izinler**' e tıklayın. **Genel izinler** panelinde **Yönet** sekmesini seçin ve ardından Ekle simgesine (+ Symbol) tıklayın.

    ![Genel Izinler paneli](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. **Küresel Izin kökü**' nde, Kullanıcı veya grup ' u seçmek Için izin Ekle ' **ye tıklayın.**

    ![Kullanıcı veya grup seçin](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. **Kullanıcı/Grup Seç**bölümünde **Backupadmin** > **Ekle**' yi seçin. **Kullanıcılar**' da Kullanıcı hesabı için *etkialanı \ KullanıcıAdı* biçimi kullanılır. Farklı bir etki alanı kullanmak istiyorsanız, **etki alanı** listesinden seçin. Seçilen kullanıcıları **Izin Ekle** iletişim kutusuna eklemek için **Tamam** ' ı tıklatın.

    ![BackupAdmin kullanıcısı ekle](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. **Atanan rolde**, açılan listeden **backupadminrole** > **Tamam**' ı seçin.

    ![Rolü kullanıcıya ata](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

**Genel izinler** panelindeki **Yönet** sekmesinde, Yeni Kullanıcı hesabı ve ilişkili rol listede görüntülenir.

## <a name="add-the-account-on-azure-backup-server"></a>Hesabı Azure Backup Sunucusu ekleyin

1. Azure Backup Sunucusu açın. Masaüstünde simgesini bulamazsanız, uygulamalar listesinden Microsoft Azure Backup açın.

    ![Azure Backup Sunucusu simgesi](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. Azure Backup Sunucusu konsolunda, **VMware 'Yi yönetmek** > **Yönetim** >  **üretim sunucuları** ' na tıklayın.

    ![Azure Backup Sunucusu konsolu](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. **Kimlik bilgilerini Yönet** Iletişim kutusunda **Ekle**' ye tıklayın.

    ![Kimlik bilgilerini Azure Backup Sunucusu Yönet iletişim kutusu](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. **Kimlik bilgisi ekle**' de, yeni kimlik bilgisi için bir ad ve açıklama girin ve VMware sunucusunda tanımladığınız Kullanıcı adını ve parolayı belirtin. Ad, *contoso vCenter kimlik bilgisi* , bu yordamdaki kimlik bilgisini belirlemek için kullanılır. VMware sunucusu ve Azure Backup Sunucusu aynı etki alanında değilse, Kullanıcı adında etki alanını belirtin.

    ![Kimlik bilgisi Ekle iletişim kutusu Azure Backup Sunucusu](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Yeni kimlik bilgisini eklemek için **Ekle** ' ye tıklayın.

    ![Kimlik bilgilerini Azure Backup Sunucusu Yönet iletişim kutusu](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>vCenter Server ekleyin

VCenter Server Azure Backup Sunucusu ekleyin.

1. Azure Backup Sunucusu konsolunda **yönetim** > **üretim sunucuları** > **Ekle**' ye tıklayın.

    ![Üretim sunucusu ekleme Sihirbazı 'Nı aç](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. **Üretim sunucusu ekleme sihirbazı** > **üretim sunucusu türünü seçin** sayfasında, **VMware sunucuları**' nı seçin ve ardından **İleri**' ye tıklayın.

    ![Üretim sunucusu ekleme Sihirbazı](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. **Bilgisayarları seçin** **sunucu adı/IP adresi**' nde, VMware sunucusunun FQDN 'sini veya IP adresini belirtin. Tüm ESXi sunucuları aynı vCenter tarafından yönetiliyorsa vCenter adını belirtin. Aksi takdirde ESXi konağını ekleyin.

    ![VMware sunucusu belirtin](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. **SSL bağlantı noktası**' nda, VMware sunucusuyla iletişim kurmak için kullanılan bağlantı noktasını girin. 443 varsayılan bağlantı noktasıdır, ancak VMware sunucunuz farklı bir bağlantı noktasında dinliyorsa bunu değiştirebilirsiniz.

5. **Kimlik bilgilerini belirtin**bölümünde, daha önce oluşturduğunuz kimlik bilgisini seçin.

    ![Kimlik bilgisini belirtin](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. VMware sunucusunu sunucular listesine eklemek için **Ekle** ' ye tıklayın. Ardından **İleri**'ye tıklayın.

    ![VMWare sunucusu ve kimlik bilgisi ekleme](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. **Özet** sayfasında, VMware sunucusunu Azure Backup sunucusu eklemek için **Ekle** ' ye tıklayın. Yeni sunucu anında eklenir, VMware sunucusunda bir aracı gerekmez.

    ![VMware sunucusunu Azure Backup Sunucusu Ekle](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. **Son** sayfadaki ayarları doğrulayın.

   ![Son sayfa](./media/backup-azure-backup-server-vmware/summary-screen.png)

VCenter Server tarafından yönetilmeyen birden çok ESXi ana bilgisayarı varsa veya birden çok vCenter Server örneğine sahipseniz, sunucuları eklemek için Sihirbazı yeniden çalıştırmanız gerekir.

## <a name="configure-a-protection-group"></a>Koruma grubu yapılandırma

Yedekleme için VMware VM 'Leri ekleyin. Koruma grupları birden çok VM toplar ve aynı veri saklama ve yedekleme ayarlarını gruptaki tüm VM 'lere uygular.

1. Azure Backup Sunucusu konsolunda, **koruma**, **Yeni**> ' ye tıklayın.

    ![Yeni koruma grubu oluşturma Sihirbazı 'nı açın](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. **Yeni koruma grubu oluşturma** Sihirbazı 'na hoş geldiniz sayfasında **İleri**' ye tıklayın.

    ![Yeni koruma grubu oluşturma Sihirbazı iletişim kutusu](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. **Koruma grubu türünü seçin** sayfasında **sunucular** ' ı seçin ve ardından **İleri**' ye tıklayın. **Grup üyelerini seçin** sayfası görüntülenir.

1. **Grup üyelerini seçin**bölümünde yedeklemek Istediğiniz VM 'leri (veya VM klasörlerini) seçin. Ardından **İleri**'ye tıklayın.

    - Bir klasör seçtiğinizde veya bu klasörün içindeki VM 'Ler veya klasörler yedekleme için de seçilir. Yedeklemek istemediğiniz klasörlerin veya VM 'Lerin işaretini kaldırabilirsiniz.
1. Bir VM veya klasör zaten yedekleniyorsa, onu seçemezsiniz. Bu, bir VM için yinelenen kurtarma noktalarının oluşturulmamasını sağlar.

    ![Grup üyelerini seçin](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. **Veri koruma yöntemini seçin** sayfasında, koruma grubu için bir ad ve koruma ayarları girin. Azure 'a yedeklemek için, kısa vadeli korumayı **diske** ayarlayın ve çevrimiçi korumayı etkinleştirin. Ardından **İleri**'ye tıklayın.

    ![Veri koruma yöntemini seçin](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. **Kısa vadeli hedefleri belirtin**bölümünde, verileri diske ne kadar süreyle yedeklenediğinizi belirtin.
   - **Bekletme aralığı**' nda, disk kurtarma noktalarının kaç gün tutulması gerektiğini belirtin.
   - **Eşitleme sıklığı**' nda, disk kurtarma noktalarının ne sıklıkta alınacağını belirtin.
       - Bir yedekleme aralığı ayarlamak istemiyorsanız, her bir kurtarma noktası zamanlanmadan önce bir yedeklemenin çalışması için bir **kurtarma noktasının hemen öncesine** bakabilirsiniz.
       - Kısa vadeli yedeklemeler tam yedeklemelerdir ve artımlı değildir.
       - Kısa vadeli yedeklemelerin oluşma zamanlarını/tarihleri değiştirmek için **Değiştir** ' e tıklayın.

         ![Kısa vadeli hedefleri belirtin](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. **Disk ayırmayı İncele**' de, VM yedeklemeleri için sunulan disk alanını gözden geçirin. VM 'Ler için.

   - Önerilen disk ayırmaları belirttiğiniz bekletme aralığına, iş yükü türüne ve korunan verilerin boyutuna göre belirlenir. Gerekli değişiklikleri yapın ve ardından **İleri**' ye tıklayın.
   - **Veri boyutu:** Koruma grubundaki verilerin boyutu.
   - **Disk alanı:** Koruma grubu için önerilen disk alanı miktarı. Bu ayarı değiştirmek istiyorsanız, her veri kaynağının büyüyeceğini tahmin ettiğiniz miktardan biraz daha büyük olan toplam alanı ayırmanız gerekir.
   - **Verileri** birlikte bulundurma: Birlikte bulundurmayı açarsanız, korumadaki birden fazla veri kaynağı tek bir çoğaltma ve kurtarma noktası hacmine eşlenir. Birlikte bulundurma, tüm iş yükleri için desteklenmez.
   - **Otomatik olarak Büyüt:** Bu ayarı etkinleştirirseniz, korumalı gruptaki veriler ilk ayırmayı aşarsa, Azure Backup Sunucusu disk boyutunu yüzde 25 arttırmaya çalışır.
   - **Depolama havuzu ayrıntıları:** Toplam ve kalan disk boyutu da dahil olmak üzere depolama havuzunun durumunu gösterir.

    ![Disk ayırmayı İncele](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. **Çoğaltma oluşturma yöntemini seçin** sayfasında, ilk yedeklemeyi nasıl almak istediğinizi belirtin ve ardından **İleri**' ye tıklayın.
   - Varsayılan değer **otomatik olarak ağ üzerinden** ve **Şimdi**.
   - Varsayılanı kullanıyorsanız, yoğun olmayan bir zaman belirtmenizi öneririz. **Daha sonra** öğesini seçin ve bir gün ve saat belirtin.
   - Büyük miktarlarda veri veya en iyi olmayan ağ koşulları için, çıkarılabilir medya kullanarak verileri çevrimdışı olarak çoğaltmayı göz önünde bulundurun.

    ![Çoğaltma oluşturma yöntemini seçin](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. **Tutarlılık denetimi seçenekleri**' nde, tutarlılık denetimlerinin nasıl ve ne zaman otomatikleştirildiğini seçin. Ardından **İleri**'ye tıklayın.
      - Çoğaltma verileri tutarsız hale geldiğinde veya bir küme zamanlaması üzerinde tutarlılık denetimleri gerçekleştirebilirsiniz.
      - Otomatik tutarlılık denetimlerini yapılandırmak istemiyorsanız el ile denetim gerçekleştirebilirsiniz. Bunu yapmak için, **tutarlılık denetimi yapmak**> koruma grubuna sağ tıklayın.

1. **Çevrimiçi koruma verilerini belirtin** sayfasında, yedeklemek Istediğiniz VM 'LERI veya VM klasörlerini seçin. Üyeleri tek tek seçebilir veya tümünü **Seç** ' e tıklayarak tüm üyeleri seçebilirsiniz. Ardından **İleri**'ye tıklayın.

    ![Çevrimiçi koruma verilerini belirtin](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. **Çevrimiçi yedekleme zamanlamasını belirtin** sayfasında, verileri yerel depolamadan Azure 'a ne sıklıkta yedeklemek istediğinizi belirtin.

    - Verilerin bulut kurtarma noktaları zamanlamaya göre oluşturulacaktır. Ardından **İleri**'ye tıklayın.
    - Kurtarma noktası oluşturulduktan sonra, Azure 'daki kurtarma hizmetleri kasasına aktarılır.

    ![Çevrimiçi Yedekleme zamanlamasını belirtin](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. **Çevrimiçi saklama Ilkesini belirtin** sayfasında, günlük/haftalık/aylık/yıllık yedeklerden Azure 'a oluşturulan kurtarma noktalarını ne kadar süreyle saklamak istediğinizi belirtin. ardından **İleri**' ye tıklayın.

    - Azure 'da verileri ne kadar süreyle saklayabilmeniz için zaman sınırı yoktur.
    - Tek sınır, korunan örnek başına 9999 taneden fazla kurtarma noktasına sahip olamaz. Bu örnekte, korumalı örnek VMware sunucusudur.

    ![Çevrimiçi bekletme ilkesini belirtin](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. **Özet** sayfasında, ayarları gözden geçirin ve ardından **Grup Oluştur**' a tıklayın.

    ![Koruma grubu üyesi ve ayar Özeti](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6,7

VSphere 6,7 'yi yedeklemek için aşağıdakileri yapın:

- DPM sunucusunda TLS 1,2 'yi etkinleştirme
  >[!Note]
  >VMWare 6,7 ve üzeri, iletişim kuralı olarak TLS 'yi etkinleştirdi.

- Kayıt defteri anahtarlarını aşağıdaki şekilde ayarlayın:

       ```text

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

## <a name="next-steps"></a>Sonraki adımlar

Yedeklemeleri ayarlarken oluşan sorunları gidermek için, [Azure Backup sunucusu için sorun giderme kılavuzunu](./backup-azure-mabs-troubleshoot.md)gözden geçirin.
