---
title: Ana VHD görüntüsünü hazırlama ve özelleştirme-Azure
description: Windows sanal masaüstü ana görüntüsünü hazırlama, özelleştirme ve Azure 'a yükleme.
author: Heidilohr
ms.topic: how-to
ms.date: 01/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 654b5b293397af9737813d759a7c784d90629942
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445814"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Ana VHD görüntüsünü hazırlama ve özelleştirme

Bu makalede, sanal makineler (VM 'Ler) oluşturma ve bunlara yazılım yükleme dahil olmak üzere Azure 'a yükleme için bir ana sanal sabit disk (VHD) görüntüsünün nasıl hazırlanacağı açıklanmaktadır. Bu yönergeler, kuruluşunuzun mevcut işlemleriyle kullanılabilecek Windows sanal masaüstüne özgü bir yapılandırma içindir.

>[!IMPORTANT]
>Azure görüntü Galerisi 'nden bir resim kullanmanızı öneririz. Ancak, özelleştirilmiş bir görüntü kullanmanız gerekiyorsa, sanal makinenizde Windows sanal masaüstü aracısının zaten yüklü olmadığından emin olun. Windows sanal masaüstü Aracısı ile özelleştirilmiş bir görüntü kullanmak, görüntüde kayıt ve Kullanıcı oturumu bağlantılarını engelleme gibi sorunlara neden olabilir.  

## <a name="create-a-vm"></a>VM oluşturma

Windows 10 Enterprise çoklu oturum, Azure görüntü Galerisi 'nde kullanılabilir. Bu görüntüyü özelleştirmek için iki seçenek vardır.

İlk seçenek, [yönetilen bir GÖRÜNTÜDEN VM oluşturma](../virtual-machines/windows/create-vm-generalized-managed.md)' daki yönergeleri izleyerek Azure 'da bir sanal makıne (VM) sağlamak ve ardından [yazılım hazırlığı ve yüklemesine](set-up-customize-master-image.md#software-preparation-and-installation)geri atlamak için kullanılır.

İkinci seçenek, görüntüyü indirerek, bir Hyper-V VM sağlarken ve bunu gereksinimlerinize uyacak şekilde özelleştirerek aşağıdaki bölümde yer aldığı bir görüntüyü yerel olarak oluşturmaktır.

### <a name="local-image-creation"></a>Yerel görüntü oluşturma

Görüntüyü yerel bir konuma indirdikten sonra, kopyaladığınız VHD ile VM oluşturmak için **Hyper-V Yöneticisi 'ni** açın. Aşağıdaki yönergeler basit bir sürümdür, ancak [Hyper-V ' d a sanal makine oluşturma](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/)bölümünde daha ayrıntılı yönergeler bulabilirsiniz.

Kopyalanmış VHD ile bir VM oluşturmak için:

1. **Yeni sanal makine sihirbazını** açın.

2. Üretimi Belirle sayfasında **1. kuşak**' i seçin.

    > [!div class="mx-imgBorder"]
    > ![Üretimi belirt sayfasının ekran görüntüsü. "2. nesil" seçeneği seçilidir.](media/a41174fd41302a181e46385e1e701975.png)

3. Denetim noktası türü altında onay kutusunun işaretini kaldırarak kontrol noktalarını devre dışı bırakın.

    > [!div class="mx-imgBorder"]
    > ![Kontrol noktaları sayfasının denetim noktası türü bölümünün ekran görüntüsü.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

Ayrıca, kontrol noktalarını devre dışı bırakmak için PowerShell 'de aşağıdaki cmdlet 'i çalıştırabilirsiniz.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Sabit disk

Mevcut bir VHD 'den bir VM oluşturursanız, varsayılan olarak dinamik bir disk oluşturur. Aşağıdaki görüntüde gösterildiği gibi, **diski Düzenle...** seçeneğini belirleyerek sabit bir diske değiştirilebilir. Daha ayrıntılı yönergeler için bkz. [Azure 'a yüklemek için bir WINDOWS VHD veya vhdx hazırlama](../virtual-machines/windows/prepare-for-upload-vhd-image.md).

> [!div class="mx-imgBorder"]
> ![Diski Düzenle seçeneğinin ekran görüntüsü.](media/35772414b5a0f81f06f54065561d1414.png)

Ayrıca, diski sabit bir disk olarak değiştirmek için aşağıdaki PowerShell cmdlet 'ini de çalıştırabilirsiniz.

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Yazılım hazırlama ve yükleme

Bu bölümde FSLogix ve Windows Defender 'ın hazırlanması ve yüklenmesi, uygulamalar için bazı temel yapılandırma seçenekleri ve görüntü kayıt defteriniz açıklanmaktadır.

VM 'nize Enterprise ve OneDrive için Microsoft 365 uygulamalar yüklüyorsanız, [bir ana VHD görüntüsüne Office 'ı yükleme](install-office-on-wvd-master-image.md) ' ye gidin ve uygulamaları yüklemek için bu yönergeleri izleyin. İşiniz bittiğinde bu makaleye geri dönün.

Kullanıcılarınızın belirli LOB uygulamalarına erişmesi gerekiyorsa, bu bölümün yönergelerini tamamladıktan sonra yüklemenizi öneririz.

### <a name="set-up-user-profile-container-fslogix"></a>Kullanıcı profili kapsayıcısını ayarlama (FSLogix)

FSLogix kapsayıcısını görüntünün bir parçası olarak eklemek için, bir [dosya paylaşma kullanarak bir konak havuzu için profil kapsayıcısı oluşturma](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)' daki yönergeleri izleyin. FSLogix kapsayıcısının işlevselliğini [Bu hızlı başlangıç](/fslogix/configure-cloud-cache-tutorial/)ile test edebilirsiniz.

### <a name="configure-windows-defender"></a>Windows Defender 'ı yapılandırma

VM 'de Windows Defender yapılandırıldıysa, ek sırasında VHD ve VHDX dosyalarının tüm içeriğini taramayan şekilde yapılandırıldığından emin olun.

Bu yapılandırma yalnızca ek sırasında VHD ve VHDX dosyalarının taranmasını kaldırır, ancak gerçek zamanlı taramayı etkilemez.

Windows Server 'da Windows Defender 'ı yapılandırma hakkında daha ayrıntılı yönergeler için bkz. Windows [Server 'da Windows Defender virüsten koruma dışlamaları yapılandırma](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/).

Windows Defender 'ın belirli dosyaları taramayla hariç tutmak üzere nasıl yapılandırılacağı hakkında daha fazla bilgi edinmek için bkz. [dosya uzantısına ve klasör konumuna göre dışlamaları yapılandırma ve doğrulama](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/).

### <a name="disable-automatic-updates"></a>Otomatik güncelleştirmeleri devre dışı bırak

Yerel grup ilkesi aracılığıyla otomatik güncelleştirmeleri devre dışı bırakmak için:

1. **\\ \\ Windows bileşenleri \\ Windows Update Yönetim Şablonları yerel Grup İlkesi Düzenleyicisi** açın.
2. **Otomatik güncelleştirmeyi yapılandır** ' a sağ tıklayın ve **devre dışı** olarak ayarlayın.

Otomatik güncelleştirmeleri devre dışı bırakmak için komut isteminde aşağıdaki komutu da çalıştırabilirsiniz.

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Windows 10 bilgisayarlar için başlangıç düzeni belirtme (isteğe bağlı)

Windows 10 bilgisayarları için bir başlangıç düzeni belirtmek üzere bu komutu çalıştırın.

```cmd
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>Saat dilimi yeniden yönlendirmeyi ayarlama

Bir konak havuzundaki tüm VM 'Ler aynı güvenlik grubunun parçası olduğundan, grup ilkesi düzeyinde saat dilimi yeniden yönlendirmesi zorlanabilir.

Saat dilimlerini yeniden yönlendirmek için:

1. Active Directory sunucusunda, **Grup İlkesi Yönetim Konsolu** açın.
2. Etki alanınızı ve grup ilkesi nesnelerini genişletin.
3. Grup İlkesi ayarları için oluşturduğunuz **Grup İlkesi nesnesine** sağ tıklayın ve **Düzenle**' yi seçin.
4. **Grup İlkesi Yönetimi Düzenleyicisi**,   >    >    >  **Windows bileşenleri** Yönetim Şablonları  >    >    >  **Cihaz ve kaynak yeniden yönlendirme** Uzak Masaüstü Hizmetleri Uzak Masaüstü oturumu ana bilgisayarı bilgisayar yapılandırma ilkeleri ' ne gidin.
5. **Saat dilimi yeniden yönlendirmesine Izin ver** ayarını etkinleştirin.

Ayrıca, saat dilimlerini yeniden yönlendirmek için ana görüntüde bu komutu çalıştırabilirsiniz:

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Depolama alanını devre dışı bırak

Windows 10 Enterprise veya Windows 10 Enterprise çoklu oturum kullanan Windows sanal masaüstü oturumu ana bilgisayarı için depolama alanını devre dışı bırakmayı öneririz. Aşağıdaki ekran görüntüsünde gösterildiği gibi, **depolama** alanının altındaki ayarlar menüsünde depolama alanını devre dışı bırakabilirsiniz:

> [!div class="mx-imgBorder"]
> ![Ayarlar altında depolama menüsünün ekran görüntüsü. "Depolama algılama" seçeneği devre dışıdır.](media/storagesense.png)

Ayrıca, aşağıdaki komutu çalıştırarak ayarı kayıt defteriyle değiştirebilirsiniz:

```cmd
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Ek dil desteği ekle

Bu makale, dil ve bölge desteğinin nasıl yapılandırılacağını kapsamaz. Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

- [Windows yansımalarına dil ekleme](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [İsteğe bağlı özellikler](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [İsteğe bağlı dil ve bölge özellikleri (FOD)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>Diğer uygulamalar ve kayıt defteri yapılandırması

Bu bölüm, uygulama ve işletim sistemi yapılandırmasını ele alır. Bu bölümdeki tüm yapılandırmalar komut satırı ve regedit araçları tarafından yürütülebilecek kayıt defteri girdileri aracılığıyla yapılır.

>[!NOTE]
>Yapılandırma grup ilkesi nesneleri (GPO 'Lar) veya kayıt defteri içeri aktarmaları ile en iyi yöntemleri uygulayabilirsiniz. Yönetici, kuruluşunuzun gereksinimlerine göre her iki seçeneği de seçebilir.

Windows 10 Enterprise çoklu oturumunda Telemetri verilerinin geri bildirim hub 'ı koleksiyonu için şu komutu çalıştırın:

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

Watson kilitlenmelerini düzeltmek için aşağıdaki komutu çalıştırın:

```cmd
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

5 k çözünürlük desteğini onarmak için kayıt defteri düzenleyicisine aşağıdaki komutları girin. Yan yana yığını etkinleştirebilmeniz için önce komutları çalıştırmanız gerekir.

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Görüntüyü Azure 'a yüklemek için hazırlama

Yapılandırmayı tamamladıktan ve tüm uygulamaları yükledikten sonra, görüntüyü hazırlamak için [WINDOWS VHD veya vhdx hazırlama bölümündeki yönergeleri Izleyerek Azure 'a yükleyin](../virtual-machines/windows/prepare-for-upload-vhd-image.md) .

Görüntü karşıya yüklenmek üzere hazırlandıktan sonra, sanal makinenin kapalı veya serbest bırakılmış durumunda kaldığından emin olun.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Azure 'da bir depolama hesabına ana görüntü yükleme

Bu bölüm yalnızca ana görüntü yerel olarak oluşturulduğunda geçerlidir.

Aşağıdaki yönergeler, ana görüntünüzü bir Azure depolama hesabına nasıl yükleyeceksiniz. Henüz bir Azure depolama hesabınız yoksa, bir tane oluşturmak için [Bu makaledeki](/azure/developer/javascript/tutorial-vscode-static-website-node-03) yönergeleri izleyin.

1. Henüz yapmadıysanız VM görüntüsünü (VHD) sabit olarak dönüştürün. Görüntüyü Sabitsiz dönüştürmezseniz, görüntüyü başarıyla oluşturamazsınız.

2. VHD 'YI Depolama hesabınızdaki bir blob kapsayıcısına yükleyin. [Depolama Gezgini aracı](https://azure.microsoft.com/features/storage-explorer/)ile hızlıca karşıya yükleyebilirsiniz. Depolama Gezgini aracı hakkında daha fazla bilgi edinmek için [Bu makaleye](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)bakın.

    > [!div class="mx-imgBorder"]
    > ![Microsoft Azure Depolama Gezgini aracının arama penceresinin ekran görüntüsü. ". Vhd veya VHDX dosyalarını sayfa Blobları olarak yükle (önerilir)" onay kutusu seçilidir.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Sonra, tarayıcınızda Azure portal gidin ve "görüntüler" ifadesini arayın. Aramanız, aşağıdaki ekran görüntüsünde gösterildiği gibi sizi **görüntü oluşturma** sayfasına götürür:

    > [!div class="mx-imgBorder"]
    > ![Azure portal görüntü oluştur sayfasının ekran görüntüsü, görüntünün örnek değerleriyle doldurulmuştur.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Görüntüyü oluşturduktan sonra, aşağıdaki ekran görüntüsünde aşağıdakine benzer bir bildirim görmeniz gerekir:

    > [!div class="mx-imgBorder"]
    > !["Başarıyla oluşturulan görüntü" bildiriminin ekran görüntüsü.](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Sonraki adımlar

Artık bir görüntünüz olduğuna göre, konak havuzları oluşturabilir veya güncelleyebilirsiniz. Konak havuzlarını oluşturma ve güncelleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure Resource Manager şablonuyla ana bilgisayar havuzu oluşturma](./virtual-desktop-fall-2019/create-host-pools-arm-template.md)
- [Öğretici: Azure Marketi ile bir konak havuzu oluşturma](create-host-pools-azure-marketplace.md)
- [PowerShell ile ana bilgisayar havuzu oluşturma](create-host-pools-powershell.md)
- [Dosya paylaşımı kullanarak ana bilgisayar havuzu için profil kapsayıcısı oluşturma](create-host-pools-user-profile.md)
- [Windows Sanal Masaüstü yük dengeleme yöntemini yapılandırma](configure-host-pool-load-balancing.md)

VHD görüntünüzü hazırlarken veya özelleştirirken bir bağlantı sorunuyla karşılaşırsanız yardım için [sorun giderme kılavuzuna](troubleshoot-agent.md#your-issue-isnt-listed-here-or-wasnt-resolved) göz atın.
