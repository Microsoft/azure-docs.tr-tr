---
title: Azure Backup Sunucusu ile VMware VM 'lerini yedekleme
description: Bu makalede, VMware vCenter/ESXi sunucusunda çalışan VMware VM 'lerini yedeklemek için Azure Backup Sunucusu nasıl kullanacağınızı öğrenin.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: db5e5c4bdac64e2faf5babb107ecec61a02d6468
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002962"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Azure Backup Sunucusu ile VMware VM 'lerini yedekleme

Bu makalede, VMware ESXi Konakları/vCenter Server üzerinde çalışan VMware VM 'lerinin Azure Backup Sunucusu (MABS) kullanarak Azure 'a yedeklenmesi açıklanmaktadır.

Bu makalede nasıl yapılacağı açıklanmaktadır:

- Azure Backup Sunucusu, HTTPS üzerinden VMware sunucularıyla iletişim kurabilmesi için güvenli bir kanal ayarlayın.
- Azure Backup Sunucusu VMware sunucusuna erişmek için tarafından kullanılan bir VMware hesabı ayarlayın.
- Azure Backup için hesap kimlik bilgilerini ekleyin.
- VCenter veya ESXi sunucusunu Azure Backup Sunucusu ekleyin.
- Yedeklemek istediğiniz VMware VM 'lerini içeren bir koruma grubu ayarlayın, yedekleme ayarlarını belirtin ve yedeklemeyi zamanlayın.

## <a name="supported-vmware-features"></a>Desteklenen VMware özellikleri

MABS, VMware sanal makinelerini yedeklerken aşağıdaki özellikleri sağlar:

- Aracısız yedekleme: MABS, sanal makineyi yedeklemek için vCenter veya ESXi sunucusuna bir aracının yüklenmesini gerektirmez. Bunun yerine, IP adresi veya tam etki alanı adı (FQDN) ve MABS ile VMware sunucusunun kimliğini doğrulamak için kullanılan oturum açma kimlik bilgilerini sağlamanız yeterlidir.
- Bulut tümleşik yedekleme: MABS, iş yüklerini disk ve buluta korur. MABS 'nin yedekleme ve kurtarma iş akışı, uzun süreli saklama ve şirket dışı yedekleme yönetmenize yardımcı olur.
- VCenter tarafından yönetilen VM 'Leri Algıla ve koru: MABS, bir VMware sunucusuna (vCenter veya ESXi sunucusu) dağıtılan VM 'Leri algılar ve korur. Dağıtım boyutunuz büyüdükçe, VMware ortamınızı yönetmek için vCenter 'ı kullanın. MABS Ayrıca, vCenter tarafından yönetilen VM 'Leri algılar ve büyük dağıtımları korumanıza olanak sağlar.
- Klasör düzeyinde otomatik koruma: vCenter, sanal makinelerinizi VM klasörlerinde düzenlemenizi sağlar. MABS bu klasörleri algılar ve klasör düzeyinde VM 'Leri korumanıza olanak sağlar ve tüm alt klasörleri içerir. Klasörler korunurken, MABS yalnızca söz konusu klasördeki VM 'Leri korumakla kalmaz, daha sonra eklenen VM 'leri de korur. MABS, günlük olarak yeni VM 'Leri algılar ve bunları otomatik olarak korur. VM 'lerinizi özyinelemeli klasörlerde düzenlediğinizde MABS, özyinelemeli klasörlerde dağıtılan yeni VM 'Leri otomatik olarak algılar ve korur.
- MABS, yerel bir disk, ağ dosya sistemi (NFS) veya küme depolama alanı üzerinde depolanan VM 'Leri korur.
- MABS yük dengeleme için geçirilen VM 'Leri korur: VM 'Ler yük dengeleme için geçirildiğinde, MABS VM korumasını otomatik olarak algılar ve devam ettirir.
- MABS, gerekli dosyaların daha hızlı kurtarılmasına yardımcı olan tüm VM 'leri kurtarmadan dosyaları/klasörleri bir Windows VM 'den kurtarabilir.

## <a name="prerequisites-and-limitations"></a>Önkoşullar ve sınırlamalar

Bir VMware sanal makinesini yedeklemeye başlamadan önce, aşağıdaki sınırlamalar ve Önkoşullar listesini gözden geçirin.

- Bir vCenter sunucusunu (Windows üzerinde çalışan) sunucunun FQDN 'sini kullanarak bir Windows Server olarak korumak için MABS kullandıysanız, bu vCenter Server 'ı sunucunun FQDN 'sini kullanarak bir VMware sunucusu olarak koruyamazsınız.
  - VCenter Server statik IP adresini geçici bir çözüm olarak kullanabilirsiniz.
  - FQDN 'yi kullanmak istiyorsanız, Windows Server olarak korumayı durdurmanız, koruma aracısını kaldırmanız ve ardından FQDN kullanarak VMware sunucusu olarak eklemeniz gerekir.
- Ortamınızdaki ESXi sunucularını yönetmek için vCenter kullanıyorsanız, MABS koruma grubuna vCenter (ve ESXi değil) ekleyin.
- İlk MABS yedeklemesinden önce Kullanıcı anlık görüntülerini yedekleyemiyorum. MABS ilk yedeklemeyi tamamladıktan sonra, Kullanıcı anlık görüntülerini yedekleyebilirsiniz.
- MABS, VMware VM 'lerini doğrudan geçiş disklerine ve fiziksel ham cihaz eşlemeleriyle (pRDM) koruyamaz.
- MABS, VMware vApps 'i algılayamaz veya koruyamıyor.
- MABS, VMware VM 'lerini mevcut anlık görüntülerle koruyamaz.

## <a name="before-you-start"></a>Başlamadan önce

- Yedekleme için desteklenen vCenter/ESXi 'nin bir sürümünü çalıştırdığınızı doğrulayın. [Buradaki](./backup-mabs-protection-matrix.md)destek matrisine bakın.
- Azure Backup Sunucusu ayarladığınızdan emin olun. Yapmadıysanız, başlamadan önce [bunu yapın](backup-azure-microsoft-azure-backup.md) . En son güncelleştirmelerle Azure Backup Sunucusu çalıştırıyor olmanız gerekir.
- Aşağıdaki ağ bağlantı noktalarının açık olduğundan emin olun:
  - MABS ve vCenter arasında TCP 443
  - TCP 443 ve MABS ile ESXi Konağı arasında TCP 902

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>vCenter Server güvenli bir bağlantı oluşturun

Varsayılan olarak, Azure Backup Sunucusu HTTPS üzerinden VMware sunucularıyla iletişim kurar. HTTPS bağlantısını kurmak için, VMware sertifika yetkilisi (CA) sertifikasını indirin ve Azure Backup Sunucusu alın.

### <a name="before-you-begin"></a>Başlamadan önce

- HTTPS kullanmak istemiyorsanız, [Tüm VMware sunucuları IÇIN HTTPS sertifika doğrulamasını devre dışı](backup-azure-backup-server-vmware.md#disable-https-certificate-validation)bırakabilirsiniz.
- Genellikle Azure Backup Sunucusu makinesindeki bir tarayıcıdan vSphere Web Istemcisini kullanarak vCenter/ESXi sunucusuna bağlanırsınız. Bunu ilk yaptığınızda bağlantı güvenli olmaz ve aşağıdaki gösterir.
- Azure Backup Sunucusu yedeklemeleri nasıl işlediğini anlamanız önemlidir.
  - İlk adım olarak, verileri yerel disk depolama alanına yedekler Azure Backup Sunucusu. Azure Backup Sunucusu, korunan veriler için Azure Backup Sunucusu disk kurtarma noktalarını depolayan bir depolama havuzu, bir dizi disk ve birim kullanır. Depolama havuzu doğrudan bağlı depolama (DAS), bir Fiber Kanal SAN veya Iscsı depolama cihazı veya SAN olabilir. VMware VM verilerinizin yerel yedeklemesi için yeterli depolama alanı olduğundan emin olmanız önemlidir.
  - Azure Backup Sunucusu sonra yerel disk depolamadan Azure 'a yedekler.
  - Ne kadar depolama alanına ihtiyacınız olduğunu anlamak için [yardım alın](/system-center/dpm/create-dpm-protection-groups#figure-out-how-much-storage-space-you-need) . Bilgiler DPM içindir, ancak Azure Backup Sunucusu için de kullanılabilir.

### <a name="set-up-the-certificate"></a>Sertifikayı ayarlama

Güvenli bir kanalı aşağıdaki şekilde ayarlayın:

1. Azure Backup Sunucusu tarayıcıda, vSphere Web Istemcisi URL 'sini girin. Oturum açma sayfası görünmezse, bağlantı ve tarayıcı proxy ayarlarını doğrulayın.

    ![vSphere Web Istemcisi](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. VSphere Web Istemcisi oturum açma sayfasında, **Güvenilen kök CA sertifikalarını indir**' i seçin.

    ![Güvenilen kök CA sertifikasını indir](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. **İndirme** adlı bir dosya indirilir. Tarayıcınıza bağlı olarak, dosyayı açmak veya kaydetmek isteyip istemediğinizi soran bir ileti alırsınız.

    ![CA sertifikasını indir](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Dosyayı bir. zip uzantısıyla Azure Backup Sunucusu makineye kaydedin.

5. **download.zip**  >  **Tümünü Ayıkla**' ya sağ tıklayın. . Zip dosyası, içeriğini içeren **CERT** klasörüne ayıklar:
   - .0 ve. 1 gibi numaralandırılmış bir diziyle başlayan bir uzantıya sahip kök sertifika dosyası.
   - CRL dosyası. R0 veya. R1 gibi bir sırayla başlayan bir uzantıya sahiptir. CRL dosyası bir sertifikayla ilişkilendirilir.

    ![İndirilen sertifikalar](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. **Sertifikalar** klasöründe kök sertifika dosyasına sağ tıklayıp **Yeniden Adlandır**>.

    ![Kök sertifikayı yeniden adlandır](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. Kök sertifikanın uzantısını. CRT olarak değiştirin ve onaylayın. Dosya simgesi bir kök sertifikayı temsil eden bir olarak değişir.

8. Kök sertifikaya sağ tıklayın ve açılır menüden **sertifikayı yükler**' i seçin.

9. **Sertifika Içeri aktarma sihirbazında**, sertifika için hedef olarak **yerel makine** ' yi seçin ve ardından **İleri**' yi seçin. Bilgisayarda değişikliklere izin vermek isteyip istemediğiniz sorulursa onaylayın.

    ![Sihirbaza hoş geldiniz](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. **Sertifika deposu** sayfasında, **tüm sertifikaları aşağıdaki depolama alanına yerleştir**' i seçin ve ardından sertifika deposunu seçmek için **Araştır** ' ı seçin.

    ![Sertifika depolama alanı](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. **Sertifika deposu Seç**' te, sertifikalar için hedef klasör olarak **Güvenilen kök sertifika yetkilileri** ' ni seçin ve ardından **Tamam**' ı seçin.

    ![Sertifika hedef klasörü](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. **Sertifika Içeri aktarma Sihirbazı 'Nı tamamladıktan** sonra klasörü doğrulayıp **son**' u seçin.

    ![Sertifikanın doğru klasörde olduğunu doğrulama](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. Sertifika içeri aktarma onaylandıktan sonra, bağlantınızın güvenli olduğunu onaylamak için vCenter Server oturum açın.

### <a name="disable-https-certificate-validation"></a>HTTPS sertifika doğrulamasını devre dışı bırak

Kuruluşunuz dahilinde güvenli sınırlarınız varsa ve VMware sunucuları ile Azure Backup Sunucusu makine arasında HTTPS protokolünü kullanmak istemiyorsanız, HTTPS 'yi şu şekilde devre dışı bırakın:

1. Aşağıdaki metni kopyalayıp bir. txt dosyasına yapıştırın.

    ```text
    Windows Registry Editor Version 5.00
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
    "IgnoreCertificateValidation"=dword:00000001
    ```

2. Dosyayı Azure Backup Sunucusu makinesinde **Disablesecuyeniden doğrulama. reg** adıyla kaydedin.

3. Kayıt defteri girişini etkinleştirmek için dosyaya çift tıklayın.

## <a name="create-a-vmware-role"></a>VMware rolü oluşturma

Azure Backup Sunucusu, v-Center Server/ESXi konağına erişim izinleri olan bir kullanıcı hesabına ihtiyaç duyuyor. Belirli ayrıcalıklara sahip bir VMware rolü oluşturun ve ardından bir kullanıcı hesabını rolüyle ilişkilendirin.

1. VCenter Server (veya vCenter Server kullanmıyorsanız ESXi ana bilgisayarında) oturum açın.
2. **Gezgin** panelinde, **Yönetim**' i seçin.

    ![Yönetim](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. **Yönetim**  >  **rolleri**' nde Rol Ekle simgesini (+ simgesi) seçin.

    ![Rol Ekle](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. **Rol**  >  **rolü adı** Oluştur bölümünde *backupadminrole* yazın. Rol adı dilediğiniz gibi olabilir, ancak rolün amacı için tanınabilir olmalıdır.

5. Aşağıdaki tabloda özetlenen ayrıcalıkları seçin ve ardından **Tamam**' ı seçin.  Yeni rol, **Roller** panelinde listede görüntülenir.
   - Üst etiketin yanındaki simgeyi seçerek üst etiketi genişletin ve alt ayrıcalıkları görüntüleyin.
   - VirtualMachine ayrıcalıklarını seçmek için, üst alt hiyerarşiye birkaç düzey gitmeniz gerekir.
   - Üst ayrıcalık içindeki tüm alt ayrıcalıkları seçmeniz gerekmez.

    ![Üst alt öğe ayrıcalık hiyerarşisi](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Rol izinleri

Aşağıdaki tablo, oluşturduğunuz Kullanıcı hesabına atamanız gereken ayrıcalıkları yakalar:

| VCenter 6,5 Kullanıcı hesabı ayrıcalıkları                          | VCenter 6,7 Kullanıcı hesabı ayrıcalıkları                            |
|----------------------------------------------------------------------------|----------------------------------------------------------------------------|
| Veri deposu cluster.Configbir veri deposu kümesi                           | Veri deposu cluster.Configbir veri deposu kümesi                           |
| DataStore. AllocateSpace                                                    | DataStore. AllocateSpace                                                    |
| Veri deposu. veri deposuna gözatamazsınız                                                 | Veri deposu. veri deposuna gözatamazsınız                                                 |
| Veri deposu. alt düzey dosya işlemleri                                        | Veri deposu. alt düzey dosya işlemleri                                        |
| Global. Disable yöntemleri                                                     | Global. Disable yöntemleri                                                     |
| Global. Enable yöntemleri                                                      | Global. Enable yöntemleri                                                      |
| Küresel. lisanslar                                                            | Küresel. lisanslar                                                            |
| Global. log olayı                                                           | Global. log olayı                                                           |
| Global. Manage özel öznitelikler                                            | Global. Manage özel öznitelikler                                            |
| Global. set özel özniteliği                                                | Global. set özel özniteliği                                                |
| Ana bilgisayar. yerel işlemler. Sanal makine oluştur                               | Ana bilgisayar. yerel işlemler. Sanal makine oluştur                               |
| Network. assign ağı                                                     | Network. assign ağı                                                     |
| Kaynak. Sanal makineyi kaynak havuzuna ata                          | Kaynak. Sanal makineyi kaynak havuzuna ata                          |
| vApp. sanal makine Ekle                                                   | vApp. sanal makine Ekle                                                   |
| vApp. assign kaynak havuzu                                                  | vApp. assign kaynak havuzu                                                  |
| vApp. Unregister                                                            | vApp. Unregister                                                            |
| VirtualMachine.Configurlama. Cihaz Ekle veya Kaldır                         | VirtualMachine.Configurlama. Cihaz Ekle veya Kaldır                         |
| Sanal machine.Configurlama. Disk kirası                                   | Sanal machine.Configurlama. Disk kirası al                           |
| Sanal machine.Configurlama. Yeni Disk Ekle                                 | Sanal machine.Configurlama. Yeni Disk Ekle                                 |
| Sanal machine.Configurlama. İleri                                     | Sanal machine.Configurlama. Gelişmiş yapılandırma                       |
| Sanal machine.Configurlama. Disk değişiklik izleme                         | Sanal machine.Configurlama. Disk değişiklik izlemeyi Değiştir                  |
| Sanal machine.Configurlama. Konak USB cihazı                              | Sanal machine.Configuration.Configure USB cihazı                    |
| Sanal machine.Configurlama. Sanal diski Genişlet                          | Sanal machine.Configurlama. Sanal diski Genişlet                          |
| Sanal machine.Configurlama. Sahip olunan dosyaları sorgula                          | Sanal machine.Configurlama. Sahip olunan dosyaları sorgula                          |
| Sanal machine.Configurlama. Swapfile yerleşimi                           | Sanal machine.Configurlama. Swapfile yerleşimini değiştirme                    |
| Sanal makine. Konuk Işlemleri. Konuk Işlemi program yürütme         | Sanal makine. Konuk Işlemleri. Konuk Işlemi program yürütme         |
| Sanal makine. Konuk Işlemleri. Konuk Işlemi değişiklikleri             | Sanal makine. Konuk Işlemleri. Konuk Işlemi değişiklikleri             |
| Sanal makine. Konuk Işlemleri. Konuk Işlemi sorguları                   | Sanal makine. Konuk Işlemleri. Konuk Işlemi sorguları                   |
| Sanal makine. Uyor. Cihaz bağlantısı                            | Sanal makine. Uyor. Cihaz bağlantısı                            |
| Sanal makine. Uyor. VIX API tarafından Konuk işletim sistemi yönetimi | Sanal makine. Uyor. VIX API tarafından Konuk işletim sistemi yönetimi |
| Sanal makine. Uyor. Gücü kapat                                    | Sanal makine. Uyor. Gücü kapat                                    |
| Sanal makine. Envanter. Yeni oluştur                                      | Sanal makine. Envanter. Yeni oluştur                                      |
| Sanal makine. Inventory. Remove                                          | Sanal makine. Inventory. Remove                                          |
| Sanal makine. Inventory. Register                                        | Sanal makine. Inventory. Register                                        |
| Sanal makine. Sağlama. disk erişimine Izin ver                            | Sanal makine. Sağlama. disk erişimine Izin ver                            |
| Sanal makine. Sağlama. dosya erişimine Izin ver                            | Sanal makine. Sağlama. dosya erişimine Izin ver                            |
| Sanal makine. Sağlama. salt okuma disk erişimine Izin ver                  | Sanal makine. Sağlama. salt okuma disk erişimine Izin ver                  |
| Sanal makine. Sağlama. sanal makine indirmeye Izin ver               | Sanal makine. Sağlama. sanal makine indirmeye Izin ver               |
| Sanal makine. Anlık görüntü yönetimi. Anlık görüntü oluşturma                      | Sanal makine. Anlık görüntü yönetimi. Anlık görüntü oluşturma                      |
| Sanal makine. Anlık görüntü yönetimi. Anlık görüntüyü kaldır                       | Sanal makine. Anlık görüntü yönetimi. Anlık görüntüyü kaldır                       |
| Sanal makine. Anlık görüntü yönetimi. Anlık görüntüye dön                    | Sanal makine. Anlık görüntü yönetimi. Anlık görüntüye dön                    |

> [!NOTE]
> Aşağıdaki tabloda vCenter 6,0 ve vCenter 5,5 Kullanıcı hesapları için ayrıcalıklar listelenmektedir.

| VCenter 6,0 Kullanıcı hesabı ayrıcalıkları | VCenter 5,5 Kullanıcı hesabı ayrıcalıkları |
| --- | --- |
| DataStore. AllocateSpace | Network. assign |
| Global. Manage özel öznitelikler | DataStore. AllocateSpace |
| Global. set özel özniteliği | VirtualMachine.Config. Değişiklik izleme dosyanız |
| Ana bilgisayar. yerel işlemler. Sanal makine oluştur | VirtualMachine. State. RemoveSnapshot |
| Ağ. Ağ ata | VirtualMachine. State. CreateSnapshot |
| Kaynak. Sanal makineyi kaynak havuzuna ata | VirtualMachine. sağlama. DiskRandomRead |
| Sanal machine.Configurlama. Yeni Disk Ekle | VirtualMachine. etkileşim. PowerOff |
| Sanal machine.Configurlama. İleri | VirtualMachine. Inventory. Create |
| Sanal machine.Configurlama. Disk değişiklik izleme | VirtualMachine.Config. AddNewDisk |
| Sanal machine.Configurlama. Konak USB cihazı | VirtualMachine.Config. HostUSBDevice |
| Sanal machine.Configurlama. Sahip olunan dosyaları sorgula | VirtualMachine.Config. AdvancedConfig |
| Sanal machine.Configurlama. Swapfile yerleşimi | VirtualMachine.Config. Swapyerleştirmesini |
| Sanal makine. Etkileşim. güç kapalı | Global. ManageCustomFields |
| Sanal makine. Envanteri. Yeni oluştur |   |
| Sanal makine. Sağlama. disk erişimine Izin ver |   |
| Sanal makine. Alınıyor. Salt okuma disk erişimine izin ver |   |
| Sanal makine. Anlık görüntü yönetimi. Anlık görüntü oluştur |   |
| Sanal makine. Anlık görüntü yönetimi. Anlık görüntüyü kaldır |   |

## <a name="create-a-vmware-account"></a>VMware hesabı oluşturma

1. VCenter Server **Gezgin** panelinde **Kullanıcılar ve gruplar**' ı seçin. VCenter Server kullanmıyorsanız, hesabı uygun ESXi ana bilgisayarında oluşturun.

    ![Kullanıcılar ve gruplar seçeneği](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    **VCenter kullanıcıları ve grupları** paneli görüntülenir.

2. **VCenter kullanıcıları ve grupları** panelinde, **Kullanıcılar** sekmesini seçin ve ardından Kullanıcı Ekle simgesini (+ Symbol) seçin.

    ![vCenter kullanıcıları ve grupları paneli](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. **Yeni Kullanıcı** iletişim kutusunda, **Tamam**> Kullanıcı bilgilerini ekleyin. Bu yordamda, Kullanıcı adı BackupAdmin ' dir.

    ![Yeni Kullanıcı iletişim kutusu](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. Kullanıcı hesabını rolüyle ilişkilendirmek için **Gezgin** panelinde **genel izinler**' i seçin. **Genel izinler** panelinde **Yönet** sekmesini seçin ve ardından Ekle simgesini (+ simgesini) seçin.

    ![Genel Izinler paneli](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. **Küresel Izin kökü**' nde, Kullanıcı veya grup ' u **seçmek için** izin Ekle ' yi seçin.

    ![Kullanıcı veya grup seçin](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. **Kullanıcı/Grup Seç** bölümünde **backupadmin**  >  **Ekle**' yi seçin. **Kullanıcılar**' da Kullanıcı hesabı için *etkialanı \ KullanıcıAdı* biçimi kullanılır. Farklı bir etki alanı kullanmak istiyorsanız, **etki alanı** listesinden seçin. Seçilen kullanıcıları **Izin Ekle** iletişim kutusuna eklemek için **Tamam ' ı** seçin.

    ![BackupAdmin kullanıcısı ekle](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. **Atanan rolde**, açılan listeden **backupadminrole**  >  **Tamam**' ı seçin.

    ![Rolü kullanıcıya ata](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

**Genel izinler** panelindeki **Yönet** sekmesinde, Yeni Kullanıcı hesabı ve ilişkili rol listede görüntülenir.

## <a name="add-the-account-on-azure-backup-server"></a>Hesabı Azure Backup Sunucusu ekleyin

1. Azure Backup Sunucusu açın. Masaüstünde simgesini bulamazsanız, uygulamalar listesinden Microsoft Azure Backup açın.

    ![Azure Backup Sunucusu simgesi](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. Azure Backup sunucusu konsolunda, **Yönetim**  >   **üretim sunucuları**  >  **VMware 'yi Yönet**' i seçin.

    ![Azure Backup Sunucusu konsolu](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. **Kimlik bilgilerini Yönet** Iletişim kutusunda **Ekle**' yi seçin.

    ![Kimlik bilgilerini Yönet iletişim kutusu](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. **Kimlik bilgisi ekle**' de, yeni kimlik bilgisi için bir ad ve açıklama girin ve VMware sunucusunda tanımladığınız Kullanıcı adını ve parolayı belirtin. Ad, *contoso vCenter kimlik bilgisi* , bu yordamdaki kimlik bilgisini belirlemek için kullanılır. VMware sunucusu ve Azure Backup Sunucusu aynı etki alanında değilse, Kullanıcı adında etki alanını belirtin.

    ![Kimlik bilgisi Ekle iletişim kutusu Azure Backup Sunucusu](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Yeni kimlik bilgisini eklemek için **Ekle** ' yi seçin.

    ![Yeni kimlik bilgileri ekle](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>vCenter Server ekleyin

VCenter Server Azure Backup Sunucusu ekleyin.

1. Azure Backup sunucusu konsolunda **Yönetim**  >  **üretim sunucuları**  >  **Ekle**' yi seçin.

    ![Üretim sunucusu ekleme Sihirbazı 'Nı aç](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. **Üretim sunucusu ekleme Sihirbazı 'nda**  >  **üretim sunucusu türünü seçin** sayfasında, **VMware sunucuları**' nı seçin ve ardından **İleri**' yi seçin.

    ![Üretim sunucusu ekleme Sihirbazı](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. **Bilgisayarları seçin****sunucu adı/IP adresi**' nde, VMware sunucusunun FQDN 'sini veya IP adresini belirtin.   Tüm ESXi sunucuları aynı vCenter tarafından yönetiliyorsa vCenter adını belirtin. Aksi takdirde ESXi konağını ekleyin.

    ![VMware sunucusu belirtin](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. **SSL bağlantı noktası**' nda, VMware sunucusuyla iletişim kurmak için kullanılan bağlantı noktasını girin. 443 varsayılan bağlantı noktasıdır, ancak VMware sunucunuz farklı bir bağlantı noktasında dinliyorsa bunu değiştirebilirsiniz.

5. **Kimlik bilgilerini belirtin** bölümünde, daha önce oluşturduğunuz kimlik bilgisini seçin.

    ![Kimlik bilgisini belirtin](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. VMware sunucusunu sunucular listesine eklemek için **Ekle** ' yi seçin. Sonra **İleri**’yi seçin.

    ![VMWare sunucusu ve kimlik bilgisi ekleme](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. **Özet** sayfasında, VMware sunucusunu Azure Backup sunucusu eklemek için **Ekle** ' yi seçin. Yeni sunucu anında eklenir, VMware sunucusunda bir aracı gerekmez.

    ![VMware sunucusunu Azure Backup Sunucusu Ekle](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. **Son** sayfadaki ayarları doğrulayın.

   ![Son sayfa](./media/backup-azure-backup-server-vmware/summary-screen.png)

VCenter Server tarafından yönetilmeyen birden çok ESXi ana bilgisayarı varsa veya birden çok vCenter Server örneğine sahipseniz, sunucuları eklemek için Sihirbazı yeniden çalıştırmanız gerekir.

## <a name="configure-a-protection-group"></a>Koruma grubu yapılandırma

Yedekleme için VMware VM 'Leri ekleyin. Koruma grupları birden çok VM toplar ve aynı veri saklama ve yedekleme ayarlarını gruptaki tüm VM 'lere uygular.

1. Azure Backup Sunucusu konsolunda, **koruma**, **Yeni**> ' yi seçin.

    ![Yeni koruma grubu oluşturma Sihirbazı 'nı açın](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. **Yeni koruma grubu oluşturma** Sihirbazı 'na hoş geldiniz sayfasında **İleri**' yi seçin.

    ![Yeni koruma grubu oluşturma Sihirbazı iletişim kutusu](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. **Koruma grubu türünü seçin** sayfasında **sunucular** ' ı seçin ve ardından **İleri**' yi seçin. **Grup üyelerini seçin** sayfası görüntülenir.

1. **Grup üyelerini seçin** bölümünde yedeklemek Istediğiniz VM 'leri (veya VM klasörlerini) seçin. Sonra **İleri**’yi seçin.

    - Bir klasör seçtiğinizde veya bu klasörün içindeki VM 'Ler veya klasörler yedekleme için de seçilir. Yedeklemek istemediğiniz klasörlerin veya VM 'Lerin işaretini kaldırabilirsiniz.
1. Bir VM veya klasör zaten yedekleniyorsa, onu seçemezsiniz. Bu, bir VM için yinelenen kurtarma noktalarının oluşturulmamasını sağlar.

    ![Grup üyelerini seçin](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. **Veri koruma yöntemini seçin** sayfasında, koruma grubu için bir ad ve koruma ayarları girin. Azure 'a yedeklemek için, kısa vadeli korumayı **diske** ayarlayın ve çevrimiçi korumayı etkinleştirin. Sonra **İleri**’yi seçin.

    ![Veri koruma yöntemini seçme](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. **Short-Term hedeflerini belirtin** bölümünde, verilerin diske ne kadar süreyle yedeklenmek istediğinizi belirtin.
   - **Bekletme aralığı**' nda, disk kurtarma noktalarının kaç gün tutulması gerektiğini belirtin.
   - **Eşitleme sıklığı**' nda, disk kurtarma noktalarının ne sıklıkta alınacağını belirtin.
       - Bir yedekleme aralığı ayarlamak istemiyorsanız, her bir kurtarma noktası zamanlanmadan önce bir yedeklemenin çalışması için bir **kurtarma noktasının hemen öncesine** bakabilirsiniz.
       - Kısa vadeli yedeklemeler tam yedeklemelerdir ve artımlı değildir.
       - Kısa vadeli yedeklemelerin oluşma zamanlarını/tarihleri değiştirmek için **Değiştir** ' i seçin.

         ![Kısa vadeli hedefleri belirtin](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. **Disk ayırmayı İncele**' de, VM yedeklemeleri için sunulan disk alanını gözden geçirin. VM 'Ler için.

   - Önerilen disk ayırmaları belirttiğiniz bekletme aralığına, iş yükü türüne ve korunan verilerin boyutuna göre belirlenir. Gerekli değişiklikleri yapın ve ardından **İleri**' yi seçin.
   - **Veri boyutu:** Koruma grubundaki verilerin boyutu.
   - **Disk alanı:** Koruma grubu için önerilen disk alanı miktarı. Bu ayarı değiştirmek istiyorsanız, her veri kaynağının büyüyerek tahmin ettiğiniz miktardan biraz daha büyük olan toplam alan ayırmanız gerekir.
   - **Verileri** birlikte bulundurma: Birlikte bulundurmayı açarsanız, korumadaki birden fazla veri kaynağı tek bir çoğaltma ve kurtarma noktası hacmine eşlenir. Birlikte bulundurma, tüm iş yükleri için desteklenmez.
   - **Otomatik olarak Büyüt:** Bu ayarı etkinleştirirseniz, korumalı gruptaki veriler ilk ayırmayı aşarsa, Azure Backup Sunucusu disk boyutunu yüzde 25 arttırmaya çalışır.
   - **Depolama havuzu ayrıntıları:** Toplam ve kalan disk boyutu da dahil olmak üzere depolama havuzunun durumunu gösterir.

    ![Disk ayırmayı İncele](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. **Çoğaltma oluşturma yöntemini seçin** sayfasında, ilk yedeklemeyi nasıl almak istediğinizi belirtin ve ardından **İleri**' yi seçin.
   - Varsayılan değer **otomatik olarak ağ üzerinden** ve **Şimdi**.
   - Varsayılanı kullanıyorsanız, yoğun olmayan bir zaman belirtmenizi öneririz. **Daha sonra** öğesini seçin ve bir gün ve saat belirtin.
   - Büyük miktarlarda veri veya en iyi olmayan ağ koşulları için, çıkarılabilir medya kullanarak verileri çevrimdışı olarak çoğaltmayı göz önünde bulundurun.

    ![Çoğaltma oluşturma yöntemini seçin](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. **Tutarlılık denetimi seçenekleri**' nde, tutarlılık denetimlerinin nasıl ve ne zaman otomatikleştirildiğini seçin. Sonra **İleri**’yi seçin.
      - Çoğaltma verileri tutarsız hale geldiğinde veya bir küme zamanlaması üzerinde tutarlılık denetimleri gerçekleştirebilirsiniz.
      - Otomatik tutarlılık denetimlerini yapılandırmak istemiyorsanız el ile denetim gerçekleştirebilirsiniz. Bunu yapmak için, **tutarlılık denetimi yapmak**> koruma grubuna sağ tıklayın.

1. **Çevrimiçi koruma verilerini belirtin** sayfasında, yedeklemek Istediğiniz VM 'LERI veya VM klasörlerini seçin. Üyeleri tek tek seçebilir veya tümünü **Seç** ' i seçerek tüm üyeler arasından seçim yapabilirsiniz. Sonra **İleri**’yi seçin.

    ![Çevrimiçi koruma verilerini belirtin](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. **Çevrimiçi yedekleme zamanlamasını belirtin** sayfasında, verileri yerel depolamadan Azure 'a ne sıklıkta yedeklemek istediğinizi belirtin.

    - Verilerin bulut kurtarma noktaları zamanlamaya göre oluşturulacaktır. Sonra **İleri**’yi seçin.
    - Kurtarma noktası oluşturulduktan sonra, Azure 'daki kurtarma hizmetleri kasasına aktarılır.

    ![Çevrimiçi Yedekleme zamanlamasını belirtin](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. **Çevrimiçi saklama Ilkesini belirtin** sayfasında, günlük/haftalık/aylık/yıllık yedeklerden Azure 'a oluşturulan kurtarma noktalarını ne kadar süreyle saklamak istediğinizi belirtin. ardından **İleri**' yi seçin.

    - Azure 'da verileri ne kadar süreyle saklayabilmeniz için zaman sınırı yoktur.
    - Tek sınır, korunan örnek başına 9999 taneden fazla kurtarma noktasına sahip olamaz. Bu örnekte, korumalı örnek VMware sunucusudur.

    ![Çevrimiçi bekletme ilkesini belirtin](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. **Özet** sayfasında, ayarları gözden geçirin ve ardından **Grup Oluştur**' u seçin.

    ![Koruma grubu üyesi ve ayar Özeti](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-parallel-backups"></a>VMware paralel yedeklemeleri

>[!NOTE]
> Bu özellik MABS v3 UR1 için geçerlidir.

MABS 'nin önceki sürümlerinde, paralel yedeklemeler yalnızca koruma grupları arasında gerçekleştirildi. MABS v3 UR1 ile, tek bir koruma grubundaki tüm VMWare VM yedeklemeleriniz paralel ve daha hızlı VM yedeklerine göre önde olur. Tüm VMWare Delta çoğaltma işleri paralel olarak çalışır. Varsayılan olarak, paralel olarak çalıştırılacak işlerin sayısı 8 ' e ayarlanır.

İş sayısını aşağıda gösterildiği gibi kayıt defteri anahtarını kullanarak değiştirebilirsiniz (varsayılan olarak mevcut değildir, eklemeniz gerekir):

**Anahtar yolu**: `Software\Microsoft\Microsoft Data Protection Manager\Configuration\ MaxParallelIncrementalJobs\VMWare`<BR>
**Anahtar türü**: DWORD (32-bit) değeri.

> [!NOTE]
> İşlerin sayısını daha yüksek bir değere değiştirebilirsiniz. İş numarasını 1 olarak ayarlarsanız, çoğaltma işleri işlem temelli olarak çalışır. Sayıyı daha yüksek bir değere yükseltmek için VMWare performansını göz önünde bulundurmanız gerekir. Kullanımdaki kaynak sayısını ve VMWare vSphere sunucusunda gereken ek kullanımı göz önünde bulundurun ve paralel olarak çalıştırılacak Delta çoğaltma işlerinin sayısını saptayın. Ayrıca, bu değişiklik yalnızca yeni oluşturulan koruma gruplarını etkiler. Mevcut koruma grupları için, koruma grubuna geçici olarak başka bir VM eklemeniz gerekir. Bu, koruma grubu yapılandırmasını uygun şekilde güncelleştirmelidir. Bu VM 'yi, yordam tamamlandıktan sonra koruma grubundan kaldırabilirsiniz.

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6,7

VSphere 6,7 'yi yedeklemek için aşağıdakileri yapın:

- MABS sunucusunda TLS 1,2 'yi etkinleştirme

>[!NOTE]
>VMWare 6,7 üzerinde, iletişim kuralı olarak etkinleştirilmiş TLS var.

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

## <a name="exclude-disk-from-vmware-vm-backup"></a>Diski VMware VM yedeğinden hariç tut

> [!NOTE]
> Bu özellik MABS v3 UR1 için geçerlidir.

MABS v3 UR1 ile, belirli bir diski VMware VM yedeğinden dışlayabilirsiniz. Yapılandırma betiği **ExcludeDisk.ps1** içinde bulunur `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin folder` .

Disk dışlamayı yapılandırmak için aşağıdaki adımları izleyin:

### <a name="identify-the-vmware-vm-and-disk-details-to-be-excluded"></a>Dışlanacak VMWare VM ve disk ayrıntılarını tanımla

  1. VMware konsolunda, diski dışlamak istediğiniz VM ayarları ' na gidin.
  2. Dışlamak istediğiniz diski seçin ve bu diskin yolunu unutmayın.

        Örneğin, TestVM4 sabit disk 2 ' den dışlamak için, sabit disk 2 yolu **[datastore1] TestVM4/TestVM4 \_ 1. vmdk** olur.

        ![Dışlanacak sabit disk](./media/backup-azure-backup-server-vmware/test-vm.png)

### <a name="configure-mabs-server"></a>MABS sunucusunu yapılandırma

Disk dışlamayı yapılandırmak için VMware VM 'sinin koruma için yapılandırıldığı MABS sunucusuna gidin.

  1. MABS sunucusunda korunan VMware konağının ayrıntılarını alın.

        ```powershell
        $psInfo = get-DPMProductionServer
        $psInfo
        ```

        ```output
        ServerName   ClusterName     Domain            ServerProtectionState
        ----------   -----------     ------            ---------------------
        Vcentervm1                   Contoso.COM       NoDatasourcesProtected
        ```

  2. VMware konağını seçin ve VMware konağının VM korumasını listeleyin.

        ```powershell
        $vmDsInfo = get-DPMDatasource -ProductionServer $psInfo[0] -Inquire
        $vmDsInfo
        ```

        ```output
        Computer     Name     ObjectType
        --------     ----     ----------
        Vcentervm1  TestVM2      VMware
        Vcentervm1  TestVM1      VMware
        Vcentervm1  TestVM4      VMware
        ```

  3. Diski dışlamak istediğiniz VM 'yi seçin.

        ```powershell
        $vmDsInfo[2]
        ```

        ```output
        Computer     Name      ObjectType
        --------     ----      ----------
        Vcentervm1   TestVM4   VMware
        ```

  4. Diski dışlamak için `Bin` klasöre gidin ve aşağıdaki parametrelerle *ExcludeDisk.ps1* betiği çalıştırın:

        > [!NOTE]
        > Bu komutu çalıştırmadan önce, MABS sunucusunda DPMRA hizmetini durdurun. Aksi takdirde, betik başarı döndürür, ancak dışlama listesini güncelleştirmez. Hizmeti durdurmadan önce devam eden bir iş bulunmadığından emin olun.

     **Diski dışlamaya eklemek/kaldırmak için aşağıdaki komutu çalıştırın:**

      ```powershell
      ./ExcludeDisk.ps1 -Datasource $vmDsInfo[0] [-Add|Remove] "[Datastore] vmdk/vmdk.vmdk"
      ```

     **Örnek**:

     TestVM4 için disk dışlamasını eklemek için aşağıdaki komutu çalıştırın:

       ```powershell
      C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -Add "[datastore1] TestVM4/TestVM4\_1.vmdk"
       ```

      ```output
       Creating C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\excludedisk.xml
       Disk : [datastore1] TestVM4/TestVM4\_1.vmdk, has been added to disk exclusion list.
      ```

  5. Diskin dışlama için eklendiğini doğrulayın.

     **Belirli sanal makinelerin mevcut dışlamasını görüntülemek için aşağıdaki komutu çalıştırın:**

        ```powershell
        ./ExcludeDisk.ps1 -Datasource $vmDsInfo[0] [-view]
        ```

     **Örnek**

        ```powershell
        C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -view
        ```

        ```output
        <VirtualMachine>
        <UUID>52b2b1b6-5a74-1359-a0a5-1c3627c7b96a</UUID>
        <ExcludeDisk>[datastore1] TestVM4/TestVM4\_1.vmdk</ExcludeDisk>
        </VirtualMachine>
        ```

     Bu VM için korumayı yapılandırdıktan sonra, dışlanan disk koruma sırasında listelenmez.

        > [!NOTE]
        > Zaten korunan bir VM için bu adımları uyguluyorsanız, diski dışlama için ekledikten sonra tutarlılık denetimini el ile çalıştırmanız gerekir.

### <a name="remove-the-disk-from-exclusion"></a>Diski dışlamaya kaldır

Diskin dışlamasını kaldırmak için aşağıdaki komutu çalıştırın:

```powershell
C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -Remove "[datastore1] TestVM4/TestVM4\_1.vmdk"
```

## <a name="next-steps"></a>Sonraki adımlar

Yedeklemeleri ayarlarken oluşan sorunları gidermek için, [Azure Backup sunucusu için sorun giderme kılavuzunu](./backup-azure-mabs-troubleshoot.md)gözden geçirin.
