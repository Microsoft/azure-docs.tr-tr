---
title: Azure VM dosya kurtarma sorunlarını giderme
description: Azure VM yedeklemesinden dosya ve klasörleri kurtarırken karşılaşılan sorunları giderin.
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 0ee856b10c6a5fbea6f6f76b2082949ab9c1e0db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700312"
---
# <a name="troubleshoot-issues-in-file-recovery-of-an-azure-vm-backup"></a>Azure VM yedeklemesinin dosya kurtarma sorunlarını giderme

Bu makalede, bir Azure sanal makinesi (VM) yedeklemesinden dosya ve klasörleri kurtarmayla ilgili sorunları çözmenize yardımcı olabilecek sorun giderme adımları sunulmaktadır.

## <a name="common-error-messages"></a>Genel hata iletileri

Bu bölüm, görebileceğiniz hata iletilerinin sorunlarını gidermek için gereken adımları sağlar.

### <a name="exception-caught-while-connecting-to-target"></a>"Hedefe bağlanırken özel durum yakalandı"

**Olası neden**: betik kurtarma noktasına erişemiyor.

**Önerilen eylem**: Bu sorunu çözmek için betikte listelenen adımları izleyin, [ancak bağlantı başarısız oldu](#the-script-runs-but-the-connection-to-the-iscsi-target-failed).

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>"Hedef, bir Iscsı oturumu aracılığıyla zaten oturum açtı"

**Olası neden**: betik aynı makinede zaten çalıştırılmış ve sürücüler eklenmiş.

**Önerilen eylem**: kurtarma noktası birimleri zaten eklenmiş. Özgün VM 'nin aynı sürücü harflerine bağlanamaz. Dosya Gezgini 'nde kullanılabilir birimlere göz atabilirsiniz.

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>"Diskler Portal üzerinden çıkartılırsa/12-hr limitini aştığından bu betik geçersizdir. Portaldan yeni bir betik indirin "

**Olası neden**: diskler portaldan çıkarıldı veya 12 saatlik süre sınırı aşıldı.

**Önerilen eylem**: betiği indirdikten 12 saat sonra geçersiz hale gelir ve çalıştırılamaz. Portala gidin ve dosya kurtarmaya devam etmek için yeni bir betik indirin.

### <a name="iscsi_tcp-module-cant-be-loaded-or-iscsi_tcp_module-not-found"></a>iscsi_tcp modülü yüklenemiyor (veya) iscsi_tcp_module bulunamadı

**Önerilen eylem**: Bu sorunu çözmek için, [komut dosyası indirmelerinde](#the-script-downloads-successfully-but-fails-to-run)bulunan adımları başarıyla izleyin, ancak çalıştırılamaz.

## <a name="common-problems"></a>Sık karşılaşılan sorunlar

Bu bölümde, dosya kurtarma için betiği indirirken ve yürütürken karşılaşabileceğiniz yaygın sorunları gidermeye yönelik adımlar sağlanmaktadır.

### <a name="you-cant-download-the-script"></a>Betiği indirileyemiyorum

1. [Betiği indirmek için gerekli izinlere](./backup-azure-restore-files-from-vm.md#select-recovery-point-who-can-generate-script)sahip olduğunuzdan emin olun.
1. Azure hedef IP 'Lerinin bağlantısını doğrulayın. Yükseltilmiş bir komut isteminden aşağıdaki komutlardan birini çalıştırın:

   `nslookup download.microsoft.com`

    veya

    `ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>Betik başarıyla indirilir, ancak çalıştırılamadı

SUSE Linux Enterprise Server 12 SP4 üzerinde öğe düzeyinde kurtarma (ıLR) için Python betiğini çalıştırdığınızda, "iscsi_tcp modülü yüklenemiyor" veya "iscsi_tcp_module bulunamadı" hatasıyla başarısız olur.

**Olası neden**: ILR modülü, yedekleme hizmetine TCP bağlantısı kurmak için **iscsi_tcp** kullanır. SLES 12 SP4 sürümünün bir parçası olarak, SUSE **iscsi_tcp** açık iSCSI paketinden kaldırılmıştır ve bu nedenle ILR işlemi başarısız olur.

**Önerilen eylem**: SUSE 12 SP4 VM 'lerinde dosya kurtarma betiği yürütme desteklenmez. SUSE 12 SP4 'ün eski bir sürümünde geri yükleme işlemini deneyin.

### <a name="the-script-runs-but-the-connection-to-the-iscsi-target-failed"></a>Betik çalışıyor, ancak Iscsı hedefi bağlantısı başarısız oldu

"Hedefe bağlanırken özel durum yakalandı" hata iletisini görebilirsiniz.

1. Betiğin çalıştırıldığı makinenin [erişim gereksinimlerini](./backup-azure-restore-files-from-vm.md#step-4-access-requirements-to-successfully-run-the-script)karşıladığından emin olun.
1. Azure hedef IP 'Lerinin bağlantısını doğrulayın. Yükseltilmiş bir komut isteminden aşağıdaki komutlardan birini çalıştırın:

   `nslookup download.microsoft.com`

   veya

   `ping download.microsoft.com`
1. Iscsı giden bağlantı noktası 3260 ' e erişim sağlayın.
1. Azure hedef IP 'Leri veya kurtarma hizmeti URL 'Lerine giden trafiği engelleyen bir güvenlik duvarı veya NSG denetimi yapın.
1. Virüsten koruma yazılımınızın betiğin yürütülmesini engellemediğinden emin olun.

### <a name="youre-connected-to-the-recovery-point-but-the-disks-werent-attached"></a>Kurtarma noktasına bağlandınız, ancak diskler iliştirilmedi

İşletim sisteminiz için adımları izleyerek bu sorunu çözün.

#### <a name="windows-file-recovery-fails-on-server-with-storage-pools"></a>Windows dosya kurtarma, depolama havuzları ile sunucuda başarısız oluyor

Windows Server 2012 R2 ve Windows Server 2016 (depolama havuzları ile) üzerinde ilk kez betiği çalıştırdığınızda, depolama havuzu sanal makineye salt okunurdur.

>[!Tip]
> [Betiği çalıştırmak için doğru makineye](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)sahip olduğunuzdan emin olun.

Bu sorunu çözmek için, depolama havuzuna el ile okuma/yazma erişimi atayın ve sanal diskleri ekleyin:

1. **Sunucu Yöneticisi**  >  **dosya ve depolama hizmetleri**  >  **birimleri**  >  **depolama havuzları**' na gidin.

   ![Depolama havuzları seçeneklerini gösteren ekran görüntüsü.](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. **Depolama havuzu** penceresinde, kullanılabilir depolama havuzuna sağ tıklayın ve **Read-Write erişimi ayarla**' yı seçin.

   ![Bir depolama kuyruğu için sağ tıklama seçeneklerini gösteren ekran görüntüsü.](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. Depolama havuzuna okuma-yazma erişimi atandıktan sonra, **sanal diskler** bölümüne sağ tıklayıp **sanal disk Ekle**' yi seçin.

   ![Sanal disk için sağ tıklama seçeneklerini gösteren ekran görüntüsü.](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="linux-file-recovery-fails-to-auto-mount-because-the-disk-doesnt-contain-volumes"></a>Disk birim içermediğinden Linux dosya kurtarması otomatik bağlama yapamıyor

Dosya kurtarma gerçekleştirilirken, yedekleme hizmeti birimleri algılar ve otomatik olarak takar. Ancak, yedeklenen disklerin ham bölümleri varsa, bu diskler otomatik olarak bağlanmaz ve kurtarma için veri diski göremezsiniz.

Bu sorunu çözmek için [Azure sanal makine yedeklemesinden dosyaları kurtar](./backup-azure-restore-files-from-vm.md#lvmraid-arrays-for-linux-vms)' a gidin.

#### <a name="linux-file-recovery-fails-because-the-os-couldnt-identify-the-file-system"></a>İşletim sistemi dosya sistemini tanımlamadığından, Linux dosya kurtarması başarısız oluyor

Dosya kurtarma betiğini çalıştırdığınızda veri diski iliştirilemiyor. "İşletim sistemi FileSystem 'ı belirleyemedik bu yana aşağıdaki bölümler takılamadı" hatasını görürsünüz.

Bu sorunu çözmek için birimin bir üçüncü taraf uygulamayla şifrelenip şifrelenmediğini denetleyin. Şifrelendiyse, disk veya VM portalda şifreli olarak gösterilmez.

1. Yedeklenen VM 'de oturum açın ve şu komutu çalıştırın:

   `lsblk -f`

   ![Blok cihazları listelemek için komut sonuçlarının gösterildiği ekran görüntüsü.](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

1. Dosya sistemini ve şifrelemeyi doğrulayın. Birim şifrelendiyse, dosya kurtarma desteklenmez. [Azure VM yedeklemesi Için destek matrisi](./backup-support-matrix-iaas.md#support-for-file-level-restore)hakkında daha fazla bilgi edinin.

### <a name="disks-are-attached-but-the-volumes-arent-mounted"></a>Diskler bağlı, ancak birimler bağlı değil

İşletim sisteminiz için adımları izleyerek bu sorunu çözün.

#### <a name="windows"></a>Windows

Windows için dosya kurtarma komut dosyasını çalıştırdığınızda, "0 kurtarma birimi bağlı" iletisini görürsünüz. Ancak diskler Disk Yönetimi konsolunda bulunur.

**Olası neden**: birimleri iSCSI aracılığıyla iliştirdiğinde, algılanan bazı birimler çevrimdışı oldu. Iscsı kanalı VM ile hizmet arasında iletişim kurduğunda, bu birimleri algılar ve bunları çevrimiçi duruma getirir ancak bağlanmazlar.

   ![Bağlı 0 kurtarma birimini gösteren ekran görüntüsü.](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Bu sorunu tanımlamak ve çözmek için aşağıdaki adımları gerçekleştirin:

>[!Tip]
>[Betiği çalıştırmak için doğru makineye](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)sahip olduğunuzdan emin olun.

1. **Cmd** penceresinde, **disk yönetimi**'ni açmak için **diskmgmt** ' ı çalıştırın.
1. Ek diskler arayın. Aşağıdaki örnekte **Disk 2** ek bir disktir.

   ![Disk Yönetimi penceresinin ek disk ile ekran görüntüsü.](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. **Yeni birim**' e sağ tıklayın ve ardından **sürücü harfini ve yolları Değiştir**' i seçin.

   ![Ek diskte sağ tıklama seçeneklerini gösteren ekran görüntüsü.](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. **Sürücü harfini veya yolu Değiştir** penceresinde, **aşağıdaki sürücü harfini ata**' yı seçin, kullanılabilir bir sürücü atayın ve **Tamam**' ı seçin.

   ![Sürücü harfini veya yolu Değiştir penceresinin ekran görüntüsü.](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. Seçtiğiniz sürücüyü görüntülemek ve dosyaları araştırmak için dosya Gezgini 'ni açın.

#### <a name="linux"></a>Linux

>[!Tip]
>[Betiği çalıştırmak için doğru makineye](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)sahip olduğunuzdan emin olun.

Korunan Linux sanal makinesi LVM veya RAID dizileri kullanıyorsa, [Azure sanal makine yedeklemesinden dosyaları kurtarma](./backup-azure-restore-files-from-vm.md#lvmraid-arrays-for-linux-vms)bölümündeki adımları izleyin.

### <a name="you-cant-copy-the-files-from-mounted-volumes"></a>Dosyaları bağlı birimlerden kopyalayamazsınız

Kopya, "0x80070780: dosyaya sistem tarafından erişilemiyor" hatasıyla başarısız olabilir. 

Kaynak sunucuda disk yinelenenleri kaldırma özelliğinin etkin olup olmadığını denetleyin. Varsa, geri yükleme sunucusunda, sürücülerde da yinelenen verileri kaldırma özelliğinin etkinleştirildiğinden emin olun. Geri yükleme sunucusunda sürücüleri çoğaltmamanız için Yinelenenleri kaldırmayı yapılandırılmamış olarak bırakabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure sanal makine yedeğinden dosya ve klasörleri kurtarma](backup-azure-restore-files-from-vm.md)