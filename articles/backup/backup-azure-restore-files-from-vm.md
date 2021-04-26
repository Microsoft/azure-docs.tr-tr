---
title: Azure VM yedeğinden dosya ve klasörleri kurtarma
description: Bu makalede, Azure sanal makine kurtarma noktasından dosya ve klasörleri kurtarmayı öğrenin.
ms.topic: conceptual
ms.date: 03/12/2020
ms.custom: references_regions
ms.openlocfilehash: c2af279ec7e846316a94e58977e7079305ab9b03
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579364"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Azure sanal makine yedeklemesinden dosyaları kurtarma

Azure Backup, Azure [sanal makinelerini (VM 'ler) ve diskleri](./backup-azure-arm-restore-vms.md) kurtarma noktaları olarak da BILINEN Azure VM yedeklemelerinden geri yükleme özelliği sağlar. Bu makalede, Azure VM yedeğinden dosya ve klasörlerin nasıl kurtarılacağı açıklanmaktadır. Dosya ve klasörleri geri yükleme yalnızca Kaynak Yöneticisi modeli kullanılarak dağıtılan ve bir kurtarma hizmetleri kasasıyla korunan Azure VM 'Leri için kullanılabilir.


> [!NOTE]
> Bu özellik, Kaynak Yöneticisi modeli kullanılarak dağıtılan ve bir kurtarma hizmetleri kasasıyla korunan Azure sanal makineleri için kullanılabilir.
> Şifrelenmiş bir VM yedeğinden dosya kurtarma desteklenmez.
>

![Dosya klasörü kurtarma iş akışı](./media/backup-azure-restore-files-from-vm/file-recovery-1.png)

## <a name="step-1-generate-and-download-script-to-browse-and-recover-files"></a>1. Adım: dosyalara gözatıp kurtarmak için betik oluşturma ve indirme

Kurtarma noktasından dosya veya klasörleri geri yüklemek için sanal makineye gidin ve aşağıdaki adımları gerçekleştirin:

1. [Azure Portal](https://portal.Azure.com) oturum açın ve sol bölmede **sanal makineler**' i seçin. Sanal makineler listesinden sanal makineyi seçerek bu sanal makinenin panosunu açın.

2. Sanal makinenin menüsünde, yedekleme panosunu açmak için **Yedekle** ' yi seçin.

    ![Kurtarma Hizmetleri Kasası yedekleme öğesini aç](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. Yedekleme panosu menüsünde **dosya kurtarma**' yı seçin.

    ![Dosya kurtarma seçin](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)32

    **Dosya kurtarma** menüsü açılır.

    ![Dosya kurtarma menüsü](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. **Kurtarma noktası seç** açılan menüsünde, istediğiniz dosyaları tutan kurtarma noktasını seçin. Varsayılan olarak, en son kurtarma noktası zaten seçilidir.

5. Kurtarma noktasından dosya kopyalamak için kullanılan yazılımı indirmek için **çalıştırılabilir dosyayı indir** (Windows Azure VM 'ler için) veya **betiği Indir** (Linux Azure VM 'leri için bir Python betiği oluşturulur) seçeneğini belirleyin.

    ![Yürütülebilir dosyayı indir](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure, yürütülebilir dosyayı veya betiği yerel bilgisayara indirir.

    ![yürütülebilir dosya veya betik için indirme iletisi](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Yürütülebilir dosyayı veya betiği yönetici olarak çalıştırmak için, indirilen dosyayı bilgisayarınıza kaydetmeniz önerilir.

6. Yürütülebilir dosya veya komut dosyası parola korumalıdır ve parola gerektirir. **Dosya kurtarma** menüsünde, parolayı belleğe yüklemek için Kopyala düğmesini seçin.

    ![Oluşturulan parola](./media/backup-azure-restore-files-from-vm/generated-pswd.png)


## <a name="step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script"></a>2. Adım: betiği yürütmeden önce makinenin gereksinimleri karşıladığından emin olun

Betik başarıyla indirildikten sonra, bu betiği yürütmek için doğru makineye sahip olduğunuzdan emin olun. Betiği yürütmeyi planladığınız sanal makine, aşağıdaki desteklenmeyen yapılandırmalardan birini içermemelidir. **Varsa, gereksinimleri karşılayan aynı bölgeden tercihen alternatif bir makine seçin**.  

### <a name="dynamic-disks"></a>Dinamik diskler

Şu özelliklerden herhangi biriyle çalıştırılabilir betiği sanal makinede çalıştıramazsınız: alternatif bir makine seçin

- Birden çok diske yayılan birimler (yayılmış ve şeritli birimler).
- Dinamik disklerde hataya dayanıklı birimler (yansıtılmış ve RAID-5 birimleri).

### <a name="windows-storage-spaces"></a>Windows Depolama Alanları

Yedeklenen sanal makinede Windows depolama alanları varsa, indirilen yürütülebilir dosyayı aynı yedeklenen VM üzerinde çalıştıramazsınız. Alternatif bir makine seçin.

### <a name="virtual-machine-backups-having-large-disks"></a>Büyük disklere sahip sanal makine yedeklemeleri

Yedeklenen makinede çok sayıda disk (>16) veya büyük diskler (> 4 TB) varsa, VM üzerinde önemli bir etkisi olacağı için betiği geri yükleme için aynı makinede yürütülmesi önerilmez. Bunun yerine, yalnızca dosya kurtarma (Azure VM D2v3 VM 'Ler) için ayrı bir VM 'nin kullanılması önerilir ve gerekli olmadığında bu sanal makineyi kapatın. 

Büyük disk ile yedeklenen VM 'lerden dosyaları geri yükleme gereksinimlerine bakın:<br>
[Windows İşletim Sistemi](#for-backed-up-vms-with-large-disks-windows)<br>
[Linux işletim sistemi](#for-backed-up-vms-with-large-disks-linux)

ILR betiğini çalıştırmak için doğru makineyi seçtikten sonra, [Işletim sistemi gereksinimlerini](#step-3-os-requirements-to-successfully-run-the-script) ve [erişim gereksinimlerini](#step-4-access-requirements-to-successfully-run-the-script)karşıladığından emin olun. 

## <a name="step-3-os-requirements-to-successfully-run-the-script"></a>3. Adım: betiği başarıyla çalıştırmak için işletim sistemi gereksinimleri

İndirilen betiği çalıştırmak istediğiniz VM 'nin aşağıdaki gereksinimleri karşılaması gerekir.

### <a name="for-windows-os"></a>Windows işletim sistemi için

Aşağıdaki tabloda sunucu ve bilgisayar işletim sistemleri arasındaki uyumluluk gösterilmektedir. Dosyaları kurtarırken, önceki veya sonraki bir işletim sistemi sürümüne dosya geri yükleme yapamazsınız. Örneğin, bir dosyayı Windows Server 2016 VM 'den Windows Server 2012 veya Windows 8 bilgisayarına geri alamazsınız. Bir VM 'den aynı sunucu işletim sistemine veya uyumlu istemci işletim sistemine dosyaları geri yükleyebilirsiniz.

|Sunucu işletim sistemi | Uyumlu istemci işletim sistemi  |
| --------------- | ---- |
| Windows Server 2019    | Windows 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Linux işletim sistemi için

Linux 'ta, dosyaları geri yüklemek için kullanılan bilgisayarın işletim sisteminin korumalı sanal makinenin dosya sistemini desteklemesi gerekir. Betiği çalıştırmak için bir bilgisayar seçerken, bilgisayarın uyumlu bir işletim sistemi olduğundan emin olun ve aşağıdaki tabloda belirtilen sürümlerden birini kullandığından emin olun:

|Linux işletim sistemi | Sürümler  |
| --------------- | ---- |
| Ubuntu | 12,04 ve üzeri |
| CentOS | 6,5 ve üzeri  |
| RHEL | 6,7 ve üzeri |
| Debian | 7 ve üzeri |
| Oracle Linux | 6,4 ve üzeri |
| SLES | 12 ve üzeri |
| openSUSE | 42,2 ve üzeri |

> [!NOTE]
> SLES 12 SP4 işletim sistemine sahip makinelerde dosya kurtarma betiğini çalıştırırken bazı sorunlar bulduk ve SLES ekibine araştırma yapıyoruz.
> Şu anda, dosya kurtarma betiğini çalıştırmak SLES 12 SP2 ve SP3 işletim sistemi sürümleri olan makinelerde çalışmaktadır.
>

Betik Ayrıca, Python ve Bash bileşenlerinin kurtarma noktasına güvenli bir şekilde yürütülmesi ve güvenli bir şekilde bağlanmasını gerektirir.

|Bileşen | Sürüm  |
| --------------- | ---- |
| bash | 4 ve üzeri |
| python | 2.6.6 ve üzeri  |
| .NET | 4.6.2 ve üzeri |
| TLS | 1,2 desteklenmelidir  |

Ayrıca, [ILR betiğini yürütmek için doğru makineye](#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script) sahip olduğunuzdan emin olun ve [erişim gereksinimlerini](#step-4-access-requirements-to-successfully-run-the-script)karşılar.

## <a name="step-4-access-requirements-to-successfully-run-the-script"></a>4. Adım: betiği başarıyla çalıştırmak için erişim gereksinimleri

Betiği kısıtlı erişimi olan bir bilgisayarda çalıştırırsanız, erişimi olduğundan emin olun:

- `download.microsoft.com` veya `AzureFrontDoor.FirstParty` NSG 'de hizmet etiketi
- Kurtarma Hizmeti URL 'Leri (COĞRAFI ad, kurtarma hizmetleri kasasının bulunduğu bölgeyi ifade eder)
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.com` NSG 'de (Azure ortak bölgeleri için) veya `AzureBackup` hizmet etiketi
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.cn` (Azure Çin 21Vianet için) veya `AzureBackup` NSG 'de hizmet etiketi
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.us` (Azure ABD kamu için) veya `AzureBackup` NSG 'de hizmet etiketi
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.de` (Azure Almanya için) veya `AzureBackup` NSG 'de hizmet etiketi
- Giden bağlantı noktaları 53 (DNS), 443, 3260

> [!NOTE]
>
> [Yukarıdaki](#step-1-generate-and-download-script-to-browse-and-recover-files) 1. adımda indirdiğiniz betik dosyası, dosyanın adında **coğrafi adı** olacaktır. URL 'yi doldurmanız için bu **coğrafi adı** kullanın. İndirilen betik adı şu şekilde başlayacak: \' VMName \' \_ \' geoname \' _ \' GUID \' .<br><br>
> Örneğin, betik dosya adı *ContosoVM_wcus_12345678* ise, **coğrafi adı** *wcus* olur ve URL şöyle olacaktır:<br> <https://pod01-rec2.wcus.backup.windowsazure.com>
>

Linux için, betik ' Open-iSCSI ' ve ' lshw ' bileşenlerinin kurtarma noktasına bağlanmasını gerektirir. Bileşenler, betiğin çalıştırıldığı bilgisayarda yoksa, komut dosyası bileşenleri yüklemek için izin ister. Gerekli bileşenleri yüklemeye izin verin.

`download.microsoft.com`Komut dosyasının çalıştırıldığı makine ve kurtarma noktasındaki veriler arasında güvenli bir kanal oluşturmak için kullanılan bileşenleri indirmek için erişim gerekir.

Ayrıca, [ILR betiğini yürütmek için doğru makineye](#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script) sahip olduğunuzdan emin olun ve [işletim sistemi gereksinimlerini](#step-3-os-requirements-to-successfully-run-the-script)karşılar.

## <a name="step-5-running-the-script-and-identifying-volumes"></a>5. Adım: betiği çalıştırma ve birimleri tanımlama

### <a name="for-windows"></a>Windows için

[Adım 2](#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)' de listelenen tüm gereksinimleri karşıladıktan sonra [Adım 3](#step-3-os-requirements-to-successfully-run-the-script) ve [Adım 4](#step-4-access-requirements-to-successfully-run-the-script)' te, betiği indirilen konumdan (genellikle indirmeler klasörü) kopyalayın, bkz. 1. [adım betiği oluşturma ve indirme hakkında bilgi edinin](#step-1-generate-and-download-script-to-browse-and-recover-files). Yürütülebilir dosyaya sağ tıklayın ve yönetici kimlik bilgileriyle çalıştırın. İstendiğinde, parolayı yazın veya parolayı bellekten yapıştırın ve ENTER tuşuna basın. Geçerli parola girildikten sonra, komut dosyası kurtarma noktasına bağlanır.

  ![Yürütülebilir çıkış](./media/backup-azure-restore-files-from-vm/executable-output.png)


Yürütülebilir dosyayı çalıştırdığınızda, işletim sistemi yeni birimleri takar ve sürücü harfleri atar. Bu sürücülere gözatabilmeniz için Windows Gezgini veya dosya Gezgini ' ni kullanabilirsiniz. Birimlere atanan sürücü harfleri, özgün sanal makineyle aynı harfle bulunmayabilir. Ancak, birim adı korunur. Örneğin, özgün sanal makinedeki birim "veri diski (E: `\` )" ise, bu birim yerel bilgisayara "veri diski (' herhangi bir harf ':) olarak iliştirilebilir `\` . Dosyalarınız veya klasörünüzü bulana kadar betik çıktısında bahsedilen tüm birimlere göz atabilirsiniz.  

   ![Bağlı kurtarma birimleri](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-backed-up-vms-with-large-disks-windows"></a>Büyük disklere sahip yedeklenen VM 'Ler için (Windows)

Dosya kurtarma işlemi, dosya geri yükleme betiğini çalıştırdıktan sonra askıda kalırsa (örneğin, diskler hiçbir şekilde bağlanmadıysa veya bağlanmamışsa ancak birimler görünmezse), aşağıdaki adımları uygulayın:
  
1. İşletim sisteminin WS 2012 veya daha yüksek olduğundan emin olun.
2. Kayıt defteri anahtarlarının geri yükleme sunucusunda aşağıda önerildiği şekilde ayarlandığından ve sunucuyu yeniden başlattığınızdan emin olun. GUID 'nin yanındaki sayı 0001-0005 aralığında değişebilir. Aşağıdaki örnekte, 0,0004. Parametreler bölümüne kadar kayıt defteri anahtarı yolunda ilerleyin.

    ![Kayıt defteri anahtarı değişiklikleri](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

### <a name="for-linux"></a>Linux için

[2](#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script). adım, adım [3](#step-3-os-requirements-to-successfully-run-the-script) ve [4](#step-4-access-requirements-to-successfully-run-the-script). adımda listelenen tüm gereksinimleri karşıladıktan sonra, Linux makineleri için bir Python betiği oluşturun. [Betik oluşturma ve indirme hakkında bilgi edinmek için](#step-1-generate-and-download-script-to-browse-and-recover-files)bkz. 1. adım. Betiği indirin ve ilgili/uyumlu Linux sunucusuna kopyalayın. İle yürütmek için izinleri değiştirmeniz gerekebilir ```chmod +x <python file name>``` . Sonra Python dosyasını ile çalıştırın ```./<python file name>``` .


Linux 'ta, kurtarma noktasının birimleri betiğin çalıştırıldığı klasöre bağlanır. Eklenen diskler, birimler ve ilgili bağlama yolları buna göre gösterilir. Bu bağlama yolları kök düzeyinde erişime sahip kullanıcılar tarafından görülebilir. Betik çıktısında belirtilen birimlere göz atabilirsiniz.

  ![Linux dosya kurtarma menüsü](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)


#### <a name="for-backed-up-vms-with-large-disks-linux"></a>Büyük disklere sahip yedeklenen VM 'Ler için (Linux) * *

Dosya kurtarma işlemi, dosya geri yükleme betiğini çalıştırdıktan sonra askıda kalırsa (örneğin, diskler hiçbir şekilde bağlanmadıysa veya bağlanmamışsa ancak birimler görünmezse), aşağıdaki adımları uygulayın:

1. /Etc/IDL/SCC \ dosya \ dosya & lt; 1} dosyasında, ayarı şu şekilde değiştirin:
    - `node.conn[0].timeo.noop_out_timeout = 5`  Hedef `node.conn[0].timeo.noop_out_timeout = 120`
2. Yukarıdaki değişiklikleri yaptıktan sonra betiği yeniden çalıştırın. Geçici başarısızlıklar varsa, hedef hazırlanmasını etkileyen isteklerin birbirini izleyen ani arızalarını önlemek için yeniden oluşturma arasında 20 ila 30 dakikalık bir boşluk olduğundan emin olun. Yeniden çalıştırma arasındaki bu Aralık, hedefin betikten bağlantı için hazırlanmaya yönelik olduğundan emin olur.
3. Dosya kurtarmasından sonra, portala geri dönüp birimleri takabileceğiniz kurtarma noktaları için **diskleri** çıkar ' ı seçtiğinizden emin olun. Temelde, bu adım mevcut tüm işlem/oturumları temizler ve kurtarma olasılığını artırır.


#### <a name="lvmraid-arrays-for-linux-vms"></a>LVM/RAID dizileri (Linux VM 'Leri Için)

Linux 'ta mantıksal birim Yöneticisi (LVM) ve/veya yazılım RAID dizileri, mantıksal birimleri birden çok disk üzerinde yönetmek için kullanılır. Korunan Linux sanal makinesi LVM ve/veya RAID dizileri kullanıyorsa, betiği aynı VM 'de çalıştıramazsınız.<br>
Bunun yerine betiği, uyumlu bir işletim sistemi olan ve korunan VM 'nin dosya sistemini destekleyen diğer bir makinede çalıştırın.<br>
Aşağıdaki betik çıktısı, LVM ve/veya RAID dizileri disklerini ve bölüm türüne sahip birimleri görüntüler.

   ![Linux LVM çıkış menüsü](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Bu bölümleri çevrimiçi duruma getirmek için aşağıdaki bölümlerde komutları çalıştırın.

#### <a name="for-lvm-partitions"></a>LVM bölümleri için

Betik çalıştırıldığında, LVM bölümleri komut dosyası çıkışında belirtilen fiziksel birim/disk (ler) i içine bağlanır. İşlem şu şekilde yapılır

1. Fiziksel birimler veya disklerden birim grubu adlarının benzersiz listesini al
2. Ardından bu birim gruplarındaki mantıksal birimleri listeleyin
3. Ardından mantıksal birimleri istenen yola bağlayın.

##### <a name="listing-volume-group-names-from-physical-volumes"></a>Fiziksel birimlerden birim grubu adlarını listeleme

Birim grubu adlarını listelemek için:

```bash
pvs -o +vguuid
```

Bu komut, tüm fiziksel birimleri (betiği çalıştırmadan önce mevcut olanlar dahil), bunlara karşılık gelen birim grubu adlarını ve birim grubunun benzersiz kullanıcı kimliklerini (UUID 'ler) listeler. Komutun örnek çıktısı aşağıda gösterilmiştir.

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdf   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN

  /dev/sdd   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN
```

İlk sütunda (BD) fiziksel birim, sonraki sütunlarda ise ilgili birim grubu adı, biçim, öznitelikler, boyut, boş alan ve birim grubunun benzersiz KIMLIĞI gösterilir. Komut çıktısı tüm fiziksel birimleri gösterir. Betik çıktısına başvurun ve yedeklemeyle ilgili birimleri tanımla. Yukarıdaki örnekte, betik çıktısı/dev/sdf ve/dev/sddtarafından gösterilirdi. Bu nedenle, *datavg_db* birim grubu betiğe ve *Appvg_new* birim grubu makineye aittir. Son fikir benzersiz bir birim grubu adının benzersiz bir KIMLIĞE sahip olmasını sağlamalıdır.

###### <a name="duplicate-volume-groups"></a>Yinelenen birim grupları

Toplu grup adlarının betiği çalıştırdıktan sonra 2 UUID 'ler sahip olduğu senaryolar vardır. Bu, makinenin, betiğin yürütüldüğü ve yedeklenen VM 'deki birim grubu adlarının aynı olduğu anlamına gelir. Ardından, yedeklenen VM 'Ler birim gruplarını yeniden adlandırdık. Aşağıdaki örneğe göz atın.

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdg   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdh   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdm2  rootvg    lvm2 a--  194.57g  127.57g efohjX-KUGB-ETaH-4JKB-MieG-EGOc-XcfLCt
```

Betik çıktısı,/dev/sdg,/dev/SDH,/dev/sdm2 eklenmiş olarak gösterilirdi. Bu nedenle, karşılık gelen VG adları Appvg_new ve kök VG ' dir. Ancak aynı adlar makinenin VG listesinde de bulunur. Bir VG adının iki UUID 'ler olduğunu doğrulayabiliriz.

Artık betik tabanlı birimler için VG adlarını yeniden adlandırdık, örneğin:/dev/sdg,/dev/SDH,/dev/sdm2. Birim grubunu yeniden adlandırmak için aşağıdaki komutu kullanın

```bash
vgimportclone -n rootvg_new /dev/sdm2
vgimportclone -n APPVg_2 /dev/sdg /dev/sdh
```

Artık benzersiz kimlikler içeren tüm VG adlara sahip olduğumuz.

###### <a name="active-volume-groups"></a>Etkin birim grupları

Betiğin birimlerine karşılık gelen birim gruplarının etkin olduğundan emin olun. Aşağıdaki komut, etkin birim gruplarını göstermek için kullanılır. Betiğin ilgili birim gruplarının bu listede olup olmadığını denetleyin.

```bash
vgdisplay -a
```  

Aksi takdirde, aşağıdaki komutu kullanarak birim grubunu etkinleştirin.

```bash
#!/bin/bash
vgchange –a y  <volume-group-name>
```

##### <a name="listing-logical-volumes-within-volume-groups"></a>Birim grupları içindeki mantıksal birimleri listeleme

Betiğiyle ilgili benzersiz, etkin olan sanal grupların listesini aldıktan sonra, bu birim gruplarında bulunan mantıksal birimler aşağıdaki komut kullanılarak listelenebilir.

```bash
#!/bin/bash
lvdisplay <volume-group-name>
```

Bu komut her mantıksal birimin yolunu ' LV yol ' olarak görüntüler.

##### <a name="mounting-logical-volumes"></a>Mantıksal birimleri bağlama

Mantıksal birimleri tercih ettiğiniz yola bağlamak için:

```bash
#!/bin/bash
mount <LV path from the lvdisplay cmd results> </mountpath>
```

> [!WARNING]
> ' Mount-a ' kullanmayın. Bu komut '/etc/fstab ' içinde açıklanan tüm cihazları takar. Bu, yinelenen cihazların bağlı olabileceği anlamına gelebilir. Veriler, verileri kalıcı hale getiremeyen bir komut dosyası tarafından oluşturulan cihazlara yönlendirilebilir ve bu nedenle veri kaybına neden olabilir.

#### <a name="for-raid-arrays"></a>RAID dizileri için

Aşağıdaki komut tüm RAID disklerinin ayrıntılarını görüntüler:

```bash
#!/bin/bash
mdadm –detail –scan
```

 İlgili RAID diski şöyle görüntülenir `/dev/mdm/<RAID array name in the protected VM>`

RAID diskinde fiziksel birimler varsa bağlama komutunu kullanın:

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

RAID diskinde yapılandırılmış başka bir LVM varsa, LVM bölümleri için önceki yordamı kullanın, ancak RAID disk adı yerine birim adını kullanın.

## <a name="step-6-closing-the-connection"></a>6. Adım: Bağlantıyı kapatma

Dosyaları tanımladıktan ve yerel bir depolama konumuna kopyaladıktan sonra ek sürücüleri kaldırın (veya çıkarın). Sürücüleri çıkarmak için Azure portal **dosya kurtarma** menüsünde, **diskleri** çıkar ' ı seçin.

![Diskleri çıkarın](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Diskler kaldırıldıktan sonra bir ileti alırsınız. Diskleri kaldırabilmeniz için yenilemenin bağlantısının yenilenmesi birkaç dakika sürebilir.

Linux 'ta, kurtarma noktası bağlantısı kapatıldıktan sonra, işletim sistemi ilgili bağlama yollarını otomatik olarak kaldırmaz. Bağlama yolları "yalnız," birimleri olarak bulunur ve görünür, ancak dosyalara erişirken/yazarken bir hata oluşturur. Bunlar el ile kaldırılabilirler. Betiği çalıştırıldığında, önceki kurtarma noktalarından mevcut olan tüm birimleri tanımlar ve izin üzerine temizler.

> [!NOTE]
> Gerekli dosyalar geri yüklendikten sonra bağlantı kapalı olduğundan emin olun. Özellikle, komut dosyasının yürütüldüğü makinenin da yedekleme için yapılandırıldığı senaryoda bu önemlidir. Bağlantı hala açıksa, sonraki yedekleme "UserErrorUnableToOpenMount" hatasıyla başarısız olabilir. Bu durum, bağlı sürücülerin/birimlerin kullanılabilir olduğu varsayıcağından ve erişilen depolama, diğer bir deyişle, Iscsı hedef sunucusu kullanılabilir durumda olmadığı için başarısız olabilir. Bağlantı temizliği bu sürücüleri/birimleri kaldırır, bu nedenle yedekleme sırasında kullanılamaz.

## <a name="security"></a>Güvenlik

Bu bölümde, Azure VM yedeklemelerinden dosya kurtarma uygulamasına yönelik çeşitli güvenlik önlemleri ele alınmıştır.

### <a name="feature-flow"></a>Özellik akışı

Bu özellik, VM verilerine tüm VM veya VM disklerini geri yükleme veya en az adım sayısı olmadan erişmek üzere oluşturulmuştur. VM verilerine erişim, bir komut dosyası (aşağıda gösterildiği gibi çalıştırıldığında kurtarma birimini bağlar ve tüm güvenlik uygulamalarının temel Stone ' i oluşturur) tarafından sağlanır:

  ![Güvenlik özelliği akışı](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Güvenlik uygulamaları

#### <a name="select-recovery-point-who-can-generate-script"></a>Kurtarma noktası seçin (komut dosyası oluşturabilir)

Betik, VM verilerine erişim sağlar. bu nedenle, ilk yerde kimin oluşturabileceği hakkında daha fazla veri düzenlemek önemlidir. Azure portal oturum açmanız ve betiği oluşturmak için [Azure RBAC yetkiniz](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) olması gerekir.

Dosya kurtarma, VM geri yükleme ve diskleri geri yükleme için gereken yetkilendirme düzeyine ihtiyaç duyuyor. Diğer bir deyişle, yalnızca yetkili kullanıcılar betiği oluşturabileceği VM verilerini görüntüleyebilir.

Oluşturulan betik, Azure Backup hizmeti için resmi Microsoft sertifikası ile imzalanır. Betikle yapılan herhangi bir değişiklik, imzanın bozuk olduğu anlamına gelir ve betiği çalıştırmaya yönelik her türlü girişim işletim sistemi tarafından olası bir risk olarak vurgulanır.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Bağlama kurtarma birimi (betiği çalıştırabilir)

Yalnızca bir yönetici betiği çalıştırabilir ve yükseltilmiş modda çalışmalıdır. Betik yalnızca önceden oluşturulmuş bir adım kümesi çalıştırır ve herhangi bir dış kaynaktan girişi kabul etmez.

Betiği çalıştırmak için, yalnızca yetkili kullanıcıya Azure portal veya PowerShell/CLı 'de komut dosyası oluşturma sırasında gösterilen bir parola gerekir. Bu, betiği yükleyen yetkili kullanıcının da betiği çalıştırmasından sorumlu olduğundan emin olmak içindir.

#### <a name="browse-files-and-folders"></a>Dosyalara ve klasörlere gözatmaya

Betik, dosyalara ve klasörlere gözatabiliyor olması için makinedeki Iscsı Başlatıcısı 'nı kullanır ve Iscsı hedefi olarak yapılandırılan kurtarma noktasına bağlanır. Burada, biri/tüm bileşenleri taklit etmek için bir veya daha fazla aldatma denedikleri senaryolar hakkında Imagine olabilirsiniz.

Her bileşenin kimliğini doğrulayabilmesi için karşılıklı CHAP kimlik doğrulama mekanizması kullanıyoruz. Bu, sahte bir başlatıcısının Iscsı hedefine bağlanması ve sahte bir hedefin betiğin çalıştırıldığı makineye bağlanması son derece zor olduğu anlamına gelir.

Kurtarma hizmeti ile makine arasındaki veri akışı, TCP üzerinden güvenli bir TLS tüneli oluşturarak korunur (TLS 1,2, betiğin çalıştırıldığı makinede[desteklenmelidir](#step-3-os-requirements-to-successfully-run-the-script) ).

Üst/yedeklenen VM 'de bulunan herhangi bir dosya Access Control listesi (ACL), bağlı dosya sisteminde de korunur.

Betik, bir kurtarma noktasına salt okunurdur ve yalnızca 12 saat boyunca geçerlidir. Daha önce erişimi kaldırmak istiyorsanız, Azure portal/PowerShell/CLı ' da oturum açın ve söz konusu kurtarma noktası için **çıkarma diskleri** gerçekleştirin. Betik hemen geçersiz kılınır.


## <a name="next-steps"></a>Sonraki adımlar

- [PowerShell aracılığıyla dosyaları geri yüklemeyi](./backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup) öğrenin
- [Azure CLI aracılığıyla dosyaları geri yüklemeyi](./tutorial-restore-files.md) öğrenin
- VM geri yüklendikten sonra [yedeklemeleri yönetme](./backup-azure-manage-vms.md) hakkında bilgi edinin
