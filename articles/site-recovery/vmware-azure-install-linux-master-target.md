---
title: Azure Site Recovery ile Linux sanal makinesi için bir ana hedef sunucu yüklemesi
description: Azure Site Recovery kullanarak, VMware VM 'lerinin olağanüstü durum kurtarması sırasında bir Linux ana hedef sunucusunu şirket içi siteye yeniden çalışma için ayarlamayı öğrenin.
author: mayurigupta13
services: site-recovery
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: mayg
ms.openlocfilehash: 5b4d625d28584bb601905e9439c112c845219e54
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954369"
---
# <a name="install-a-linux-master-target-server-for-failback"></a>Yeniden çalışma için bir Linux ana hedef sunucusu yükler
Sanal makinelerinizin yükünü Azure 'a devretmek için sanal makineleri şirket içi siteye geri alabilirsiniz. Yeniden yük devretmek için sanal makineyi Azure 'dan şirket içi siteye yeniden korumanız gerekir. Bu işlem için, trafiği almak için bir şirket içi ana hedef sunucusuna ihtiyacınız vardır. 

Korumalı sanal makineniz bir Windows sanal makinedir, bir Windows Ana hedefine ihtiyacınız vardır. Linux sanal makinesi için bir Linux ana hedefinin olması gerekir. Bir Linux ana hedefini oluşturma ve yüklemeyi öğrenmek için aşağıdaki adımları okuyun.

> [!IMPORTANT]
> 9\.10.0 ana hedef sunucusunun yayımlanmasından başlayarak, en son ana hedef sunucusu yalnızca bir Ubuntu 16,04 sunucusuna yüklenebilir. CentOS 6,6 sunucularında yeni yüklemelere izin verilmez. Ancak, 9.10.0 sürümünü kullanarak eski ana hedef sunucularınızı yükseltmeye devam edebilirsiniz.
> LVM üzerinde ana hedef sunucu desteklenmiyor.

## <a name="overview"></a>Genel Bakış
Bu makalede bir Linux ana hedefinin nasıl yükleneceğine ilişkin yönergeler sağlanmaktadır.

Bu makalenin sonunda veya [Azure kurtarma hizmetleri Forumu](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)' nda yorum veya soru gönderin.

## <a name="prerequisites"></a>Önkoşullar

* Ana hedefin dağıtılacağı Konağı seçmek için, yeniden çalışma 'nin mevcut bir şirket içi sanal makineye mı yoksa yeni bir sanal makinede mı olacağını belirleyin. 
    * Mevcut bir sanal makine için, ana hedefin konağın sanal makinenin veri depolarına erişimi olmalıdır.
    * Şirket içi sanal makine yoksa (alternatif konuma kurtarma olması durumunda), yeniden çalışma sanal makinesi ana hedefle aynı ana bilgisayarda oluşturulur. Ana hedefi yüklemek için herhangi bir ESXi konağı seçebilirsiniz.
* Ana hedef, işlem sunucusu ve yapılandırma sunucusu ile iletişim kurabilen bir ağda olmalıdır.
* Ana hedefin sürümü, işlem sunucusu ve yapılandırma sunucusu sürümlerine eşit veya ondan daha önceki bir sürüme eşit olmalıdır. Örneğin, yapılandırma sunucusunun sürümü 9,4 ise, ana hedefin sürümü 9,4 veya 9,3, ancak 9,5 olamaz.
* Ana hedef, fiziksel sunucu değil yalnızca bir VMware sanal makinesi olabilir.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Ana hedef sunucu oluşturmak için boyutlandırma yönergeleri

Ana hedefi aşağıdaki boyutlandırma yönergelerine uygun olarak oluşturun:
- **RAM**: 6 GB veya daha fazla
- **Işletim sistemi disk boyutu**: 100 GB veya daha fazla (işletim sistemini yüklemek için)
- **Bekletme sürücüsü Için ek disk boyutu**: 1 TB
- **CPU çekirdekleri**: 4 çekirdek veya daha fazla

Aşağıdaki Ubuntu çekirdekler desteklenir.


|Çekirdek serisi  |Destek  |
|---------|---------|
|4.4      |4.4.0-81-genel         |
|4.8      |4.8.0-56-genel         |
|4.10     |4.10.0-24-genel        |


## <a name="deploy-the-master-target-server"></a>Ana hedef sunucuyu dağıtma

### <a name="install-ubuntu-16042-minimal"></a>Ubuntu 16.04.2 minimal yüklemeyi

Ubuntu 16.04.2 64-bit işletim sistemini yüklemek için aşağıdaki adımları uygulayın.

1.   [İndirme bağlantısına](http://old-releases.ubuntu.com/releases/16.04.2/ubuntu-16.04.2-server-amd64.iso)gidin, en yakın yansıtmayı seçin ve Ubuntu 16.04.2 minimal 64-bit ISO dosyasını indirin.
DVD sürücüsünde Ubuntu 16.04.2 minimal 64 bit ISO tutun ve sistemi başlatın.

1.  Tercih ettiğiniz dil olarak **İngilizce** ' yi seçin ve ardından **ENTER**' u seçin.
    
    ![Dil Seçin](./media/vmware-azure-install-linux-master-target/image1.png)
1. **Ubuntu sunucusunu yükleyip** **ENTER**' u seçin.

    ![Ubuntu sunucusu yüklemeyi seçin](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Tercih ettiğiniz dil olarak **İngilizce** ' yi seçin ve ardından **ENTER**' u seçin.

    ![Tercih ettiğiniz dil olarak Ingilizce 'yi seçin](./media/vmware-azure-install-linux-master-target/image3.png)

1. **Saat dilimi** seçenekleri listesinden uygun seçeneği belirleyin ve ardından **ENTER**' u seçin.

    ![Doğru saat dilimini seçin](./media/vmware-azure-install-linux-master-target/image4.png)

1. **Hayır** (varsayılan seçenek) seçeneğini belirleyin ve ardından **ENTER**' u seçin.

     ![Klavyeyi Yapılandırma](./media/vmware-azure-install-linux-master-target/image5.png)
1. Klavye için ülke/kaynak alanı olarak **İngilizce (ABD)** seçeneğini belirleyin ve ardından **ENTER**' u seçin.

1. Klavye düzeni olarak **İngilizce (ABD)** seçeneğini belirleyin ve ardından **ENTER**' u seçin.

1. **Ana bilgisayar** adı kutusuna sunucunuzun ana bilgisayar adını girin ve ardından **devam**' ı seçin.

1. Bir kullanıcı hesabı oluşturmak için Kullanıcı adını girip **devam**' ı seçin.

      ![Kullanıcı hesabı oluşturma](./media/vmware-azure-install-linux-master-target/image9.png)

1. Yeni Kullanıcı hesabının parolasını girip **devam**' ı seçin.

1.  Yeni Kullanıcı için parolayı onaylayın ve ardından **devam**' ı seçin.

    ![Parolaları onaylama](./media/vmware-azure-install-linux-master-target/image11.png)

1.  Giriş dizininizi şifrelemek için bir sonraki seçimde **Hayır** (varsayılan seçenek) seçeneğini belirleyin ve ardından **ENTER**' u seçin.

1. Görüntülenen saat dilimi doğru ise, **Evet** ' i (varsayılan seçenek) seçin ve ardından **ENTER**' u seçin. Saat diliminizi yeniden yapılandırmak için **Hayır**' ı seçin.

1. Bölümleme yöntemi seçeneklerinde, **tüm diski kullan**' ı seçin ve ardından **ENTER**' u seçin.

     ![Bölümlendirme yöntemi seçeneğini belirleyin](./media/vmware-azure-install-linux-master-target/image14.png)

1.  **Diski bölümlemek Için seçin** seçeneklerini belirleyin ve ardından **ENTER**' u seçin.

    ![Diski seçin](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Değişiklikleri diske yazmak için **Evet** ' i seçin ve ardından **ENTER**' u seçin.

    ![Varsayılan seçeneği seçin](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  Proxy 'yi Yapılandır seçimini, varsayılan seçeneğini belirleyin, **devam**' ı seçin ve ardından **ENTER**' u seçin.
     
     ![Yükseltmeleri nasıl yöneteceğinizi seçin](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Sisteminizdeki yükseltmeleri yönetmek için seçimde **Otomatik güncelleştirme yok** seçeneğini belirleyin ve ardından **ENTER**' u seçin.

     ![Yükseltmeleri nasıl yöneteceğinizi seçin](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Azure Site Recovery ana hedef sunucusu Ubuntu 'ın çok özel bir sürümünü gerektirdiğinden, sanal makine için çekirdek yükseltmelerin devre dışı bırakıldığından emin olmanız gerekir. Etkin olmaları durumunda, normal yükseltmeler ana hedef sunucunun hatalı çalışmasına neden olur. **Otomatik güncelleştirme yok** seçeneğini seçtiğinizden emin olun.

1.  Varsayılan seçenekleri seçin. SSH Connect için openSSH isterseniz, **OpenSSH sunucusu** seçeneğini belirleyip **devam**' ı seçin.

    ![Yazılım seçin](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. GRUB önyükleme yükleyicisini yükleme seçiminde **Evet**' i seçin ve ardından **ENTER**' u seçin.
     
    ![GRUB önyükleme yükleyicisi](./media/vmware-azure-install-linux-master-target/image20.png)


1. Önyükleme yükleyicisi yüklemesi için uygun cihazı seçin (tercihen **/dev/sda**) ve ardından **ENTER**' u seçin.
     
    ![Uygun cihaz seçin](./media/vmware-azure-install-linux-master-target/image21.png)

1. **Devam**' ı seçin ve ardından **ENTER** ' u seçerek yüklemeyi sona erdirin.

    ![Yüklemeyi Tamamlama](./media/vmware-azure-install-linux-master-target/image22.png)

1. Yükleme tamamlandıktan sonra, VM 'de Yeni Kullanıcı kimlik bilgileriyle oturum açın. (Daha fazla bilgi için **10. adıma** bakın.)

1. KÖK kullanıcı parolasını ayarlamak için aşağıdaki ekran görüntüsünde açıklanan adımları kullanın. Ardından kök kullanıcı olarak oturum açın.

    ![KÖK kullanıcı parolasını ayarlama](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>Makineyi ana hedef sunucu olarak yapılandırma

Bir Linux sanal makinesindeki her SCSI sabit diskinin KIMLIĞINI almak için **disk. Enableuuıd = TRUE** parametresinin etkinleştirilmesi gerekiyor. Bu parametreyi etkinleştirmek için aşağıdaki adımları uygulayın:

1. Sanal makinenizi kapatın.

2. Sol bölmedeki sanal makine girişine sağ tıklayın ve ardından **Ayarları Düzenle**' yi seçin.

3. **Seçenekler** sekmesini seçin.

4. Sol bölmede **gelişmiş** > **genel**' i seçin ve ardından ekranın sağ alt kısmındaki **yapılandırma parametreleri** düğmesini seçin.

    ![Yapılandırma parametresini aç](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    Makine çalışırken **yapılandırma parametreleri** seçeneği kullanılamaz. Bu sekmeyi etkin hale getirmek için sanal makineyi kapatın.

5. Disk içeren bir satır olup olmadığını görün **. Enableuuıd** zaten var.

   - Değer varsa ve **false**olarak ayarlanırsa, değeri **true**olarak değiştirin. (Değerler büyük/küçük harfe duyarlı değildir.)

   - Değer varsa ve **true**olarak ayarlanırsa **iptal**' i seçin.

   - Değer yoksa **satır ekle**' yi seçin.

   - Ad sütununda **disk ekleyin. Enableuuıd**ve ardından değeri **true**olarak ayarlayın.

     ![Disk olup olmadığı denetleniyor. Enableuuıd zaten var](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Çekirdek yükseltmelerini devre dışı bırak

Ana hedef sunucusu Azure Site Recovery, Ubuntu 'ın belirli bir sürümünü gerektirir, sanal makine için çekirdek yükseltmelerin devre dışı bırakıldığından emin olun. Çekirdek yükseltmeleri etkinleştirilirse, ana hedef sunucunun hatalı çalışmasına neden olabilir.

#### <a name="download-and-install-additional-packages"></a>Ek paketleri indir ve yükle

> [!NOTE]
> Ek paketleri indirmek ve yüklemek için Internet bağlantısına sahip olduğunuzdan emin olun. Internet bağlantınız yoksa, bu deb paketlerini el ile bulmanız ve yüklemeniz gerekir.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Kurulum için yükleyiciyi al

Ana hedefinizin Internet bağlantısı varsa, yükleyiciyi indirmek için aşağıdaki adımları kullanabilirsiniz. Aksi takdirde, yükleyiciyi işlem sunucusundan kopyalayabilir ve sonra yükleyebilirsiniz.

#### <a name="download-the-master-target-installation-packages"></a>Ana hedef yükleme paketlerini indirin

[En son Linux ana hedef yükleme bitlerini indirin](https://aka.ms/latestlinuxmobsvc).

Linux kullanarak indirmek için şunu yazın:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Yükleyiciyi ana dizininizdeki indirip sıkıştırmasını açın. **/Usr/local**olarak unzip ederseniz yükleme başarısız olur.


#### <a name="access-the-installer-from-the-process-server"></a>Yükleyiciye işlem sunucusundan erişin

1. İşlem sunucusunda **C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushınstallsvc\repository**adresine gidin.

2. Gerekli yükleyici dosyasını işlem sunucusundan kopyalayın ve giriş dizininizde **latestlinuxmobsvc. tar. gz** olarak kaydedin.


### <a name="apply-custom-configuration-changes"></a>Özel yapılandırma değişikliklerini Uygula

Özel yapılandırma değişikliklerini uygulamak için, kök kullanıcı olarak aşağıdaki adımları kullanın:

1. İkiliyi kaldırmak için aşağıdaki komutu çalıştırın.

    `tar -xvf latestlinuxmobsvc.tar.gz`

    ![Çalıştırılacak komutun ekran görüntüsü](./media/vmware-azure-install-linux-master-target/image16.png)

2. İzin vermek için aşağıdaki komutu çalıştırın.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Betiği çalıştırmak için aşağıdaki komutu çalıştırın.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Betiği sunucuda yalnızca bir kez çalıştırın. Ardından sunucuyu kapatın. Sonraki bölümde açıklandığı gibi bir diski ekledikten sonra sunucuyu yeniden başlatın.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Linux ana hedef sanal makinesine bir bekletme diski ekleme

Bir saklama diski oluşturmak için aşağıdaki adımları kullanın:

1. Linux ana hedef sanal makinesine yeni bir 1 TB disk ekleyin ve ardından makineyi başlatın.

2. Çok yollu- **ll** komutunu kullanarak bekletme diskinin çok yollu kimliğini öğrenin: **çok yollu-ll**

    ![Çok yollu KIMLIĞI](./media/vmware-azure-install-linux-master-target/image27.png)

3. Sürücüyü biçimlendirin ve ardından yeni sürücüde bir dosya sistemi oluşturun: **mkfs. ext4/dev/mapper/\<bekletme diskinin çok yollu kimliği >** .
    
    ![Dosya sistemi](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. Dosya sistemini oluşturduktan sonra, bekletme diskini bağlayın.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Sistem her başlatıldığında bekletme sürücüsünü bağlamak için **fstab** girişi oluşturun.
    
    `vi /etc/fstab`
    
    Dosyayı düzenlemeyle başlamak için **Ekle** ' yi seçin. Yeni bir satır oluşturun ve ardından aşağıdaki metni ekleyin. Önceki komuttan vurgulanan çok yollu KIMLIĞE göre disk çok yollu KIMLIĞINI düzenleyin.

    **/dev/mapper/\<saklama diskleri çok yollu kimliği >/mnt/bekletme ext4 RW 0 0**

    **ESC**' yi seçin ve ardından düzenleyici penceresini kapatmak için **WQ** (yazma ve çıkış) yazın.

### <a name="install-the-master-target"></a>Ana hedefi yükler

> [!IMPORTANT]
> Ana hedef sunucunun sürümü, işlem sunucusu ve yapılandırma sunucusu sürümlerine eşit veya ondan daha önceki bir sürüme eşit olmalıdır. Bu koşul karşılanmazsa, yeniden koruma başarılı olur, ancak çoğaltma başarısız olur.


> [!NOTE]
> Ana hedef sunucuyu yüklemeden önce, sanal makinedeki **/etc/hosts** dosyasında, yerel ana bilgisayar adını tüm ağ bağdaştırıcıları Ile ilişkili IP adreslerine eşleyen girişler bulunduğundan emin olun.

1. Yapılandırma sunucusundaki **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.exe** klasöründen parolayı kopyalayın. Ardından, aşağıdaki komutu çalıştırarak aynı yerel dizine **parolayı. txt** olarak kaydedin:

    `echo <passphrase> >passphrase.txt`

    Örnek: 

       `echo itUx70I47uxDuUVY >passphrase.txt`
    

2. Yapılandırma sunucusunun IP adresini aklınızda edin. Ana hedef sunucuyu yüklemek ve sunucuyu yapılandırma sunucusuna kaydetmek için aşağıdaki komutu çalıştırın.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Örnek: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Komut dosyası bitene kadar bekleyin. Ana hedef başarıyla kaydolduğunda, ana hedef portalın **Site Recovery altyapı** sayfasında listelenir.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Etkileşimli yükleme kullanarak ana hedefi yükleme

1. Ana hedefi yüklemek için aşağıdaki komutu çalıştırın. Aracı rolü için **ana hedef**' i seçin.

    ```
    ./install
    ```

2. Yükleme için varsayılan konumu seçin ve sonra devam etmek için **ENTER** ' u seçin.

    ![Ana hedefin yüklenmesi için varsayılan bir konum seçme](./media/vmware-azure-install-linux-master-target/image17.png)

Yükleme tamamlandıktan sonra, komut satırını kullanarak yapılandırma sunucusunu kaydedin.

1. Yapılandırma sunucusunun IP adresini göz önünde edin. Bunu bir sonraki adımda yapmanız gerekir.

2. Ana hedef sunucuyu yüklemek ve sunucuyu yapılandırma sunucusuna kaydetmek için aşağıdaki komutu çalıştırın.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    Örnek: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

     Komut dosyası bitene kadar bekleyin. Ana hedef başarıyla kaydedilmişse, ana hedef portalın **Site Recovery altyapı** sayfasında listelenir.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>VMware araçları/açık-VM-araçları 'nı ana hedef sunucuya yükler

Veri depolarını keşfedebilmesi için, VMware araçları 'nı veya ana hedefte açık VM araçları 'nı yüklemeniz gerekir. Araçlar yüklü değilse, yeniden koru ekranı veri depolarında listelenmez. VMware araçlarının yüklenmesinden sonra yeniden başlatmanız gerekir.

### <a name="upgrade-the-master-target-server"></a>Ana hedef sunucusunu yükseltme

Yükleyiciyi çalıştırın. Aracının ana hedefte yüklü olduğunu otomatik olarak algılar. Yükseltmek için **Y**'yi seçin.  Kurulum tamamlandıktan sonra, aşağıdaki komutu kullanarak yüklü ana hedefin sürümünü denetleyin:

`cat /usr/local/.vx_version`


**Sürüm** alanının ana hedefin sürüm numarasını sunabilme durumunu görürsünüz.

## <a name="common-issues"></a>Genel sorunlar

* Ana hedef gibi yönetim bileşenlerinde Depolama vMotion 'ı etkinleştirdiğinizden emin olun. Ana hedef başarılı bir şekilde yeniden korunduktan sonra taşınırsa, sanal makine diskleri (VMDK) ayrılamıyor. Bu durumda yeniden çalışma başarısız olur.

* Ana hedefin sanal makinede anlık görüntü olmaması gerekir. Anlık görüntüler varsa, yeniden çalışma başarısız olur.

* Bazı özel NIC yapılandırmalarına bağlı olarak, ağ arabirimi başlangıç sırasında devre dışıdır ve ana hedef Aracısı başlatılamaz. Aşağıdaki özelliklerin doğru ayarlandığından emin olun. Bu özellikleri Ethernet kartı dosyasının/Etc/sysconfig/Network-Scripts/ifcfg-ETH * ' de denetleyin.
    * BOOTPROTO = DHCP
    * ONBOOT = Evet


## <a name="next-steps"></a>Sonraki adımlar
Ana hedefin yüklenmesi ve kaydı tamamlandıktan sonra, ana hedefin yapılandırma sunucusuna genel bakış altında **Site Recovery altyapısı**'nda **ana hedef** bölümünde görüntülendiğini görebilirsiniz.

Artık yeniden çalışma sonrasında [yeniden koruma](vmware-azure-reprotect.md)ile devam edebilirsiniz.

