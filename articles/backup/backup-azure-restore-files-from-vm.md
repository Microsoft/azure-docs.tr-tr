---
title: Azure VM yedeğinden dosya ve klasörleri kurtarma
description: Bu makalede, Azure sanal makine kurtarma noktasından dosya ve klasörleri kurtarmayı öğrenin.
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 3fff957e542a039fcc5121f13c062f710f9292c9
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172854"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Azure sanal makine yedeklemesinden dosyaları kurtarma

Azure Backup, Azure [sanal makinelerini (VM 'ler) ve diskleri](./backup-azure-arm-restore-vms.md) kurtarma noktaları olarak da BILINEN Azure VM yedeklemelerinden geri yükleme özelliği sağlar. Bu makalede, Azure VM yedeğinden dosya ve klasörlerin nasıl kurtarılacağı açıklanmaktadır. Dosya ve klasörleri geri yükleme yalnızca Kaynak Yöneticisi modeli kullanılarak dağıtılan ve bir kurtarma hizmetleri kasasıyla korunan Azure VM 'Leri için kullanılabilir.

> [!Note]
> Bu özellik, Kaynak Yöneticisi modeli kullanılarak dağıtılan ve bir kurtarma hizmetleri kasasıyla korunan Azure sanal makineleri için kullanılabilir.
> Şifrelenmiş bir VM yedeğinden dosya kurtarma desteklenmez.
>

## <a name="mount-the-volume-and-copy-files"></a>Birimi bağlama ve dosyaları kopyalama

Kurtarma noktasından dosya veya klasörleri geri yüklemek için sanal makineye gidin ve istediğiniz kurtarma noktasını seçin.

1. [Azure Portal](https://portal.Azure.com) oturum açın ve sol bölmede **sanal makineler**' e tıklayın. Sanal makineler listesinden sanal makineyi seçerek bu sanal makinenin panosunu açın.

2. Sanal makinenin menüsünde, yedekleme panosunu açmak için **Yedekle** ' ye tıklayın.

    ![Kurtarma Hizmetleri Kasası yedekleme öğesini aç](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. Yedekleme panosu menüsünde, **dosya kurtarma**' ya tıklayın.

    ![Dosya Kurtarma düğmesi](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    **Dosya kurtarma** menüsü açılır.

    ![Dosya kurtarma menüsü](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. **Kurtarma noktası seç** açılan menüsünde, istediğiniz dosyaları tutan kurtarma noktasını seçin. Varsayılan olarak, en son kurtarma noktası zaten seçilidir.

5. Kurtarma noktasından dosya kopyalamak için kullanılan yazılımı indirmek için, **yürütülebilir dosyayı indir** (WINDOWS Azure VM için) veya **betiği Indir** (Linux Azure VM için bir Python betiği oluşturulur).

    ![Oluşturulan parola](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure, yürütülebilir dosyayı veya betiği yerel bilgisayara indirir.

    ![yürütülebilir dosya veya betik için indirme iletisi](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Yürütülebilir dosyayı veya betiği yönetici olarak çalıştırmak için, indirmeyi bilgisayarınıza kaydetmeniz önerilir.

6. Yürütülebilir dosya veya komut dosyası parola korumalıdır ve parola gerektirir. **Dosya kurtarma** menüsünde, parolayı belleğe yüklemek için Kopyala düğmesine tıklayın.

    ![Oluşturulan parola](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. İndirme konumundan (genellikle Indirmeler klasörü), çalıştırılabilir veya betiğe sağ tıklayın ve yönetici kimlik bilgileriyle çalıştırın. İstendiğinde, parolayı yazın veya parolayı bellekten yapıştırın ve ENTER tuşuna basın. Geçerli parola girildikten sonra, komut dosyası kurtarma noktasına bağlanır.

    ![Dosya kurtarma menüsü](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Betiği kısıtlı erişimi olan bir bilgisayarda çalıştırırsanız, erişimi olduğundan emin olun:

    - download.microsoft.com
    - Kurtarma Hizmeti URL 'Leri (coğrafi ad, kurtarma hizmeti kasasının bulunduğu bölgeyi ifade eder) - <https://pod01-rec2.geo-name.backup.windowsazure.com> (Azure genel geos için) - <https://pod01-rec2.geo-name.backup.windowsazure.cn> (Azure Çin 21Vianet için) - <https://pod01-rec2.geo-name.backup.windowsazure.us> (Azure ABD kamu Için) - <https://pod01-rec2.geo-name.backup.windowsazure.de> (Azure Almanya Için)
    - giden bağlantı noktası 3260

> [!Note]
>
> - İndirilen betik dosyası adı URL 'de doldurulacak **coğrafi ada** sahip olacaktır. Örneğin: indirilen betik adı \'VMname\'\_\'geoname\'_\'GUID\'ile başlar ContosoVM_wcus_12345678
> - URL <https://pod01-rec2.wcus.backup.windowsazure.com>"

   Linux için, betik ' Open-iSCSI ' ve ' lshw ' bileşenlerinin kurtarma noktasına bağlanmasını gerektirir. Bileşenler, betiğin çalıştırıldığı bilgisayarda yoksa, komut dosyası bileşenleri yüklemek için izin ister. Gerekli bileşenleri yüklemeye izin verin.

   Download.microsoft.com erişimi, betiğin çalıştırıldığı makine ve kurtarma noktasındaki veriler arasında güvenli bir kanal oluşturmak için kullanılan bileşenleri indirmek için gereklidir.

   Betiği, yedeklenen VM ile aynı (veya uyumlu) işletim sistemine sahip herhangi bir makinede çalıştırabilirsiniz. Uyumlu işletim sistemleri için [uyumlu IŞLETIM sistemi tablosuna](backup-azure-restore-files-from-vm.md#system-requirements) bakın. Korumalı Azure sanal makinesi Windows depolama alanları (Windows Azure VM 'Ler için) veya LVM/RAID dizileri (Linux VM 'Ler için) kullanıyorsa, çalıştırılabilir veya betiği aynı sanal makinede çalıştıramazsınız. Bunun yerine, çalıştırılabilir veya betiği, uyumlu bir işletim sistemi olan herhangi bir makinede çalıştırın.

### <a name="identifying-volumes"></a>Birimleri tanımlama

#### <a name="for-windows"></a>Windows için

Yürütülebilir dosyayı çalıştırdığınızda, işletim sistemi yeni birimleri takar ve sürücü harfleri atar. Bu sürücülere gözatabilmeniz için Windows Gezgini veya dosya Gezgini ' ni kullanabilirsiniz. Birimlere atanan sürücü harfleri, özgün sanal makineyle aynı harflerde olmayabilir, ancak birim adı korunur. Örneğin, özgün sanal makinedeki birim "veri diski (E:`\`)" ise, bu birim yerel bilgisayara "veri diski (' herhangi bir harf ':`\`) olarak eklenebilir. Dosyalarınız/klasörünüz bulunana kadar betik çıkışında bahsedilen tüm birimlere göz atabilirsiniz.  

   ![Dosya kurtarma menüsü](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Linux için

Linux 'ta, kurtarma noktasının birimleri betiğin çalıştırıldığı klasöre bağlanır. Eklenen diskler, birimler ve ilgili bağlama yolları buna göre gösterilir. Bu bağlama yolları kök düzeyinde erişime sahip kullanıcılar tarafından görülebilir. Betik çıktısında belirtilen birimlere göz atabilirsiniz.

  ![Linux dosya kurtarma menüsü](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>Bağlantı kapatılıyor

Dosyaları tanımladıktan ve yerel bir depolama konumuna kopyaladıktan sonra ek sürücüleri kaldırın (veya çıkarın). Sürücüleri çıkarmak için, Azure portal **dosya kurtarma** menüsünde, **diskleri**çıkar ' a tıklayın.

![Diskleri çıkarın](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Diskler kaldırıldıktan sonra bir ileti alırsınız. Diskleri kaldırabilmeniz için yenilemenin bağlantısının yenilenmesi birkaç dakika sürebilir.

Linux 'ta, kurtarma noktası bağlantısı kapatıldıktan sonra, işletim sistemi ilgili bağlama yollarını otomatik olarak kaldırmaz. Bağlama yolları "yalnız," birimleri olarak bulunur ve görünür durumdadır, ancak dosyalara erişirken/yazarken hata oluşturur. Bunlar el ile kaldırılabilirler. Betiği çalıştırıldığında, önceki kurtarma noktalarından mevcut olan tüm birimleri tanımlar ve izin üzerine temizler.

## <a name="special-configurations"></a>Özel yapılandırma

### <a name="dynamic-disks"></a>Dinamik diskler

Korunan Azure VM 'nin aşağıdaki özelliklerden biri veya her ikisi de varsa, çalıştırılabilir betiği aynı VM 'de çalıştıramazsınız.

- Birden çok diske yayılan birimler (yayılmış ve şeritli birimler)
- Dinamik disklerde hataya dayanıklı birimler (yansıtılmış ve RAID-5 birimleri)

Bunun yerine, çalıştırılabilir betiği, uyumlu bir işletim sistemi olan başka herhangi bir bilgisayarda çalıştırın.

### <a name="windows-storage-spaces"></a>Windows depolama alanları

Windows depolama alanları, depolamayı sanallaştırmanızı sağlayan bir Windows teknolojisidir. Windows depolama alanları ile sektör standardı diskleri depolama havuzlarına gruplayabilirsiniz. Daha sonra, depolama alanları adı verilen sanal diskler oluşturmak için bu depolama havuzlarındaki kullanılabilir alanı kullanırsınız.

Korumalı Azure sanal makinesi Windows depolama alanları kullanıyorsa, çalıştırılabilir betiği aynı VM üzerinde çalıştıramazsınız. Bunun yerine, çalıştırılabilir betiği, uyumlu bir işletim sistemi olan diğer herhangi bir makinede çalıştırın.

### <a name="lvmraid-arrays"></a>LVM/RAID dizileri

Linux 'ta mantıksal birim Yöneticisi (LVM) ve/veya yazılım RAID dizileri, mantıksal birimleri birden çok disk üzerinde yönetmek için kullanılır. Korunan Linux sanal makinesi LVM ve/veya RAID dizileri kullanıyorsa, betiği aynı VM 'de çalıştıramazsınız. Bunun yerine betiği, uyumlu bir işletim sistemi olan ve korunan VM 'nin dosya sistemini destekleyen diğer bir makinede çalıştırın.

Aşağıdaki betik çıktısı, LVM ve/veya RAID dizileri disklerini ve bölüm türüne sahip birimleri görüntüler.

   ![Linux LVM çıkış menüsü](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Bu bölümleri çevrimiçi duruma getirmek için aşağıdaki bölümlerde komutları çalıştırın.

#### <a name="for-lvm-partitions"></a>LVM bölümleri için

Fiziksel birim altındaki birim grubu adlarını listelemek için.

```bash
#!/bin/bash
pvs <volume name as shown above in the script output>
```

Tüm mantıksal birimleri, adları ve bunların yollarını bir birim grubunda listelemek için.

```bash
#!/bin/bash
lvdisplay <volume-group-name from the pvs command’s results>
```

Mantıksal birimleri tercih ettiğiniz yola bağlamak için.

```bash
#!/bin/bash
mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>RAID dizileri için

Aşağıdaki komut tüm RAID disklerinin ayrıntılarını görüntüler.

```bash
#!/bin/bash
mdadm –detail –scan
```

 İlgili RAID diski `/dev/mdm/<RAID array name in the protected VM>` olarak görüntülenir

RAID diskinde fiziksel birimler varsa bağlama komutunu kullanın.

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

RAID diskinde yapılandırılmış başka bir LVM varsa, LVM bölümleri için önceki yordamı kullanın, ancak RAID disk adı yerine birim adını kullanın

## <a name="system-requirements"></a>Sistem gereksinimleri

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

> [!Note]
> SLES 12 SP4 işletim sistemine sahip makinelerde dosya kurtarma betiğini çalıştırırken bazı sorunlar bulduk. SLES ekibi ile araştırma.
> Şu anda, dosya kurtarma betiğini çalıştırmak SLES 12 SP2 ve SP3 işletim sistemi sürümleri olan makinelerde çalışmaktadır.
>

Betik Ayrıca, Python ve Bash bileşenlerinin kurtarma noktasına güvenli bir şekilde yürütülmesi ve güvenli bir şekilde bağlanmasını gerektirir.

|Bileşen | Sürüm  |
| --------------- | ---- |
| Bash | 4 ve üzeri |
| Python | 2.6.6 ve üzeri  |
| IOCTL | 1,2 desteklenmelidir  |

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>Büyük disklere sahip sanal makine yedeklemelerinden dosya kurtarma

Bu bölümde, disk sayısı 16 > ve her disk boyutu > 4 TB olan Azure sanal makine yedeklemelerinden nasıl dosya kurtarmasının gerçekleştirileceği açıklanmaktadır.

Dosya kurtarma işlemi tüm diskleri yedekten iliştirdiğinden, çok sayıda disk (> 16) veya büyük diskler (her biri > 4 TB) kullanıldığında, aşağıdaki eylem noktaları önerilir:

- Dosya kurtarma için ayrı bir geri yükleme sunucusunu (Azure VM D2v3 VM 'Ler) saklayın. Bu yalnızca dosya kurtarma 'yı kullanabilir ve gerekli olmadığında kapatılabilir. Özgün makineye geri yükleme, sanal makinenin kendisi üzerinde önemli bir etkiye sahip olacağı için önerilmez.
- Sonra, dosya kurtarma işleminin başarılı olup olmadığını denetlemek için betiği bir kez çalıştırın.
- Dosya kurtarma işlemi askıda kalırsa (diskler hiçbir şekilde bağlanmamışsa veya bağlanmamışsa ancak birimler görünmezse), aşağıdaki adımları uygulayın.
  - Geri yükleme sunucusu bir Windows sanal makinesi ise
    - İşletim sisteminin WS 2012 + olduğundan emin olun.
    - Kayıt defteri anahtarlarının geri yükleme sunucusunda aşağıda önerildiği şekilde ayarlandığından ve sunucuyu yeniden başlattığınızdan emin olun. GUID 'nin yanındaki sayı 0001-0005 aralığında değişebilir. Aşağıdaki örnekte, 0,0004 ' dir. Parametreler bölümüne kadar kayıt defteri anahtarı yolunda ilerleyin.

    ![iscsi-reg-Key-Changes. png](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- Geri yükleme sunucusu bir Linux sanal makinesi ise
  - /Etc/IDL/SCC \ dosya \ dosya & lt; 1} dosyasında, ayarı
    - Node. Conn [0]. Timeo. noop_out_timeout = 5 ila Node. Conn [0]. Timeo. noop_out_timeout = 30
- Aşağıdaki işlemi gerçekleştirdikten sonra betiği yeniden çalıştırın. Bu değişikliklerle dosya kurtarmanın başarılı olması oldukça yüksektir.
- Kullanıcı bir betiği her indirdiğinde, Azure Backup kurtarma noktasını indirme işlemini hazırlama işlemini başlatır. Büyük disklerle bu, önemli ölçüde zaman alır. İsteklerin birbirini izleyen bir kopyası varsa, hedef hazırlık bir indirme içine gider. Bu nedenle, Portal/PowerShell/CLı 'dan bir betiği indirmeniz, 20-30 dakika boyunca (buluşsal bir değer) bekleyip çalıştırmanız önerilir. Bu süre içinde, hedefin betikten bağlantı için hazırlanma beklenmektedir.
- Dosya kurtarmasından sonra, birimleri takabileceğiniz kurtarma noktaları için "diskleri çıkar" seçeneğine tıklayarak portala geri gitdiğinizden emin olun. Temelde, bu adım mevcut tüm işlem/oturumları temizler ve kurtarma olasılığını artırır.

## <a name="troubleshooting"></a>Sorun giderme

Dosyaları sanal makinelerden kurtarırken sorunlarla karşılaşırsanız, daha fazla bilgi için aşağıdaki tabloya bakın.

| Hata Iletisi/senaryo | Olası neden | Önerilen eylem |
| ------------------------ | -------------- | ------------------ |
| Exe çıkışı: *hedefe bağlanma özel durumu* |Betik, kurtarma noktasına erişemiyor    | Makinenin önceki erişim gereksinimlerini yerine getirip getirmediğini denetleyin. |  
| Exe çıkışı: *hedef, bir iSCSI oturumu aracılığıyla zaten oturum açtı.* | Betik aynı makinede zaten yürütüldü ve sürücüler eklendi | Kurtarma noktasının birimleri zaten eklenmiş. Özgün VM 'nin aynı sürücü harflerine bağlı olmayabilir. Dosya Gezgini 'nde dosyanız için kullanılabilir tüm birimlere göz at |
| Exe çıkışı: *diskler Portal üzerinden çıkartılırsa/12-hr sınırını aştığından bu betik geçersizdir. Portaldan yeni bir komut dosyası indirin.* |    Diskler portaldan çıkarıldı veya 12 saatlik limit aşıldı | Bu belirli bir exe artık geçersiz ve çalıştırılamaz. Bu kurtarma noktasının dosyalarına erişmek isterseniz, yeni bir exe için portalı ziyaret edin|
| Exe 'nin çalıştırıldığı makinede: çıkarma düğmesine tıklandıktan sonra yeni birimler çıkartılırsa | Makinedeki Iscsı Başlatıcısı yanıt vermiyor/hedefe yönelik bağlantısını yenilemedi ve önbelleğin saklanması. |  **Çıkarma**' ya tıkladıktan sonra birkaç dakika bekleyin. Yeni birimler çıkartılırsa, tüm birimlere göz atabilirsiniz. Tüm birimlere göz atmak başlatıcıyı bağlantıyı yenilemeye zorlar ve birim, diskin kullanılamadığı bir hata iletisiyle çıkarılır.|
| Exe çıkışı: betik başarıyla çalıştırıldı, ancak "yeni birimler eklendi" betik çıktısında görüntülenmiyor |    Bu geçici bir hatadır    | Birimler zaten eklenmiş durumda. Gezinmek için Explorer 'ı açın. Her seferinde betikleri çalıştırmak için aynı makineyi kullanıyorsanız, makineyi yeniden başlatmayı düşünün ve listenin sonraki exe çalıştırmaları içinde görüntülenmesi gerekir. |
| Linux 'a özgü: istenen birimleri görüntüleyemeyebilirsiniz | Betiğin çalıştırıldığı makinenin işletim sistemi, korunan sanal makinenin temelindeki dosya sistemini tanımıyor olabilir | Kurtarma noktasının kilitlenmeyle tutarlı veya dosya tutarlılığı olduğunu denetleyin. Dosya tutarlı ise, işletim sistemi, korunan VM 'nin dosya sistemini algılayan başka bir makinede betiği çalıştırın |
| Windows 'a özgü: istenen birimleri görüntüleyemeyebilirsiniz | Diskler eklenmiş olabilir, ancak birimler yapılandırılmadı | Disk Yönetimi ekranından, kurtarma noktasıyla ilgili ek diskleri tanımla. Bu disklerden herhangi biri çevrimdışı durumdaysa, diske sağ tıklayıp ' çevrimiçi ' seçeneğine tıklayarak bunları çevrimiçi yapmayı deneyin.|

## <a name="security"></a>Güvenlik

Bu bölümde, kullanıcıların özelliğin güvenlik yönünden haberdar olması gibi, Azure VM yedeklemelerinden dosya kurtarma uygulamasına yönelik çeşitli güvenlik önlemleri ele alınmıştır.

### <a name="feature-flow"></a>Özellik akışı

Bu özellik, VM verilerine tüm VM veya VM disklerini geri yükleme ve en düşük adımlarda erişmek için oluşturulmuştur. VM verilerine erişim, bir komut dosyası (aşağıda gösterildiği gibi çalıştırıldığında kurtarma birimini bağlar) tarafından sağlanır ve bu nedenle tüm güvenlik uygulamalarının temel pulu oluşturur

  ![Güvenlik özelliği akışı](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Güvenlik uygulamaları

#### <a name="select-recovery-point-who-can-generate-script"></a>Kurtarma noktası seçin (komut dosyası oluşturabilir)

Betik, VM verilerine erişim sağlar. Bu, ilk yerde kimin oluşturabileceği bir şekilde düzenlemek önemlidir. Tek bir Azure portal oturum açması gerekir ve betiği oluşturmak için [RBAC 'nin yetkilendirilmiş](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) olması gerekir.

Dosya kurtarma, VM geri yükleme ve diskleri geri yükleme için gereken yetkilendirme düzeyine ihtiyaç duyuyor. Diğer bir deyişle, yalnızca yetkili kullanıcılar betiği oluşturabileceği VM verilerini görüntüleyebilir.

Oluşturulan betik, Azure Backup hizmeti için resmi Microsoft sertifikası ile imzalanır. Betiğe yapılan herhangi bir değişiklik, imzanın kopuklandığı ve betiği çalıştırmaya yönelik her türlü girişim, işletim sistemi tarafından olası bir risk olarak vurgulandığı anlamına gelir.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Bağlama kurtarma birimi (betiği çalıştırabilir)

Betiği yalnızca yönetici çalıştırabilir ve yükseltilmiş modda çalıştırmalıdır. Betik yalnızca önceden oluşturulmuş bir adım kümesi çalıştırır ve herhangi bir dış kaynaktan gelen girişi kabul etmez.

Betiği çalıştırmak için, tek yapmanız gereken, Azure portal veya PowerShell/CLı 'de betik oluşturma sırasında yalnızca yetkili kullanıcıya gösterilen bir parola gerektirir. Bu, betiği yükleyen yetkili kullanıcının da betiği çalıştırmasından sorumlu olduğundan emin olmak içindir.

#### <a name="browse-files-and-folders"></a>Dosyalara ve klasörlere gözatmaya

Betik, dosyalara ve klasörlere gözatabiliyor olması için makinedeki Iscsı Başlatıcısı 'nı kullanır ve Iscsı hedefi olarak yapılandırılan kurtarma noktasına bağlanır. Burada, biri/tüm bileşenleri taklit edilmeye çalıştığı senaryolar ele alalım.

Her bileşenin kimliğini doğrulayabilmesi için karşılıklı CHAP kimlik doğrulama mekanizmasını kullanıyoruz. Bu, sahte bir başlatıcısının Iscsı hedefine bağlanması ve sahte bir hedefin betiğin çalıştırıldığı makineye bağlanması için son derece zor olduğu anlamına gelir.

Kurtarma hizmeti ile makine arasındaki veri akışı, TCP üzerinden güvenli bir SSL tüneli oluşturarak korunur (TLS 1,2, betiğin çalıştırıldığı makinede[desteklenmelidir](#system-requirements) )

Üst/yedeklenen sanal makinede bulunan herhangi bir dosya Access Control listesi (ACL), bağlı dosya sisteminde de korunur.

Betik, bir kurtarma noktasına salt okunurdur ve yalnızca 12 saat boyunca geçerlidir. Kullanıcı daha önce erişimi kaldırmayı istiyorsa, Azure portalında/PowerShell/CLı ' de oturum açın ve bu kurtarma noktası için **diskleri** çıkar ' ı gerçekleştirin. Betik hemen geçersiz kılınır.
