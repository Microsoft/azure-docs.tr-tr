---
title: Windows sanal masaüstü (klasik) oturum ana bilgisayarı-Azure sorunlarını giderme
description: Windows sanal masaüstü (klasik) oturum ana bilgisayarı sanal makinelerini yapılandırırken sorunları çözme.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: af0da66766a4b814dde8dfa45a0abe00f9e93efa
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444182"
---
# <a name="windows-virtual-desktop-classic-session-host-virtual-machine-configuration"></a>Windows sanal masaüstü (klasik) oturum ana bilgisayarı sanal makine yapılandırması

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir. Azure Resource Manager Windows sanal masaüstü nesnelerini yönetmeye çalışıyorsanız, [Bu makaleye](../troubleshoot-vm-configuration.md)bakın.

Windows sanal masaüstü oturumu ana bilgisayarı sanal makinelerini (VM 'Ler) yapılandırırken karşılaştığınız sorunları gidermek için bu makaleyi kullanın.

## <a name="provide-feedback"></a>Geribildirim gönderme

Windows Sanal Masaüstü hizmetini ürün ekibi ve etkin topluluk üyeleriyle tartışmak için [Windows sanal masaüstü teknoloji Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) 'yi ziyaret edin.

## <a name="vms-are-not-joined-to-the-domain"></a>VM 'Ler etki alanına katılmamış

VM 'Leri etki alanına eklerken sorun yaşıyorsanız bu yönergeleri izleyin.

- [Windows Server sanal makinesini yönetilen bir etki alanına ekleme](../../active-directory-domain-services/join-windows-vm.md) veya [etki alanına ekleme şablonunu](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)kullanma içindeki işlemi kullanarak VM 'yi el ile birleştirin.
- VM 'de komut satırından etki alanı adının ping komutunu deneyin.
- Etki alanına ekleme [hata Iletileriyle Ilgili sorunları gidermek](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx)için etki alanına katılması hata iletileri listesini gözden geçirin.

### <a name="error-incorrect-credentials"></a>Hata: yanlış kimlik bilgileri

**Neden:** Azure Resource Manager şablon arabirimi düzeltmeleriyle kimlik bilgileri girildiğinde oluşturulan bir yazım hatası vardı.

**Çözüm:** Çözümlemek için aşağıdaki eylemlerden birini gerçekleştirin.

- VM 'Leri bir etki alanına el ile ekleyin.
- Kimlik bilgileri onaylandıktan sonra şablonu yeniden dağıtın. Bkz. [PowerShell ile konak havuzu oluşturma](create-host-pools-powershell-2019.md).
- [Mevcut bir WINDOWS sanal MAKINESINI ad etki alanına birleştiren](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)bir şablon kullanarak VM 'leri etki alanına katın.

### <a name="error-timeout-waiting-for-user-input"></a>Hata: Kullanıcı girişi beklenirken zaman aşımı oluştu

**Neden:** Etki alanı katılımı tamamlamaya yönelik hesap Multi-Factor Authentication 'a (MFA) sahip olabilir.

**Çözüm:** Çözümlemek için aşağıdaki eylemlerden birini gerçekleştirin.

- Hesap için MFA 'yı geçici olarak kaldırın.
- Bir hizmet hesabı kullanın.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Hata: sağlama sırasında kullanılan hesabın işlemi tamamlamaya yönelik izinleri yok

**Neden:** Kullanılmakta olan hesabın, uyumluluk ve yönetmelikler nedeniyle VM 'Leri etki alanına katma izni yok.

**Çözüm:** Çözümlemek için aşağıdaki eylemlerden birini gerçekleştirin.

- Yönetici grubunun üyesi olan bir hesap kullanın.
- Kullanılan hesaba gerekli izinleri verin.

### <a name="error-domain-name-doesnt-resolve"></a>Hata: etki alanı adı çözümlenmiyor

**Neden 1:** VM 'Ler, etki alanının bulunduğu sanal ağ (VNET) ile ilişkilendirilmemiş bir sanal ağ üzerinde bulunuyor.

**1. Çözüm:** VM 'Lerin sağlandığı VNET ile etki alanı denetleyicisinin (DC) çalıştığı VNET arasında VNET eşlemesi oluşturun. Bkz. [sanal ağ eşlemesi oluşturma-kaynak yöneticisi, farklı abonelikler](../../virtual-network/create-peering-different-subscriptions.md).

**Neden 2:** Azure Active Directory Domain Services (Azure AD DS) kullanırken, sanal ağın DNS sunucusu ayarları, yönetilen etki alanı denetleyicilerini işaret etmek üzere güncellenir.

**2. Çözüm:** Azure AD DS içeren sanal ağın DNS ayarlarını güncelleştirmek için bkz. [Azure sanal ağı IÇIN DNS ayarlarını güncelleştirme](../../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network).

**Neden 3:** Ağ arabiriminin DNS sunucusu ayarları, sanal ağda uygun DNS sunucusunu göstermiyor.

**3. Çözüm:** Çözümlemek için, [DNS sunucularını değiştirme] bölümündeki adımları izleyerek aşağıdaki eylemlerden birini gerçekleştirin.
- Ağ arabiriminin DNS sunucusu ayarlarını, [DNS sunucularını değiştirme](../../virtual-network/virtual-network-network-interface.md#change-dns-servers) adımları ile **özel** olarak DEĞIŞTIRIN ve sanal ağda DNS sunucularının özel IP adreslerini belirtin.
- Ağ arabiriminin DNS sunucusu ayarlarını, [DNS sunucularını değiştirme](../../virtual-network/virtual-network-network-interface.md#change-dns-servers)adımları ile **sanal ağdan devralacak** şekilde değiştirin, ardından sanal ağın DNS sunucusu ayarlarını [değiştirme DNS sunucuları](../../virtual-network/manage-virtual-network.md#change-dns-servers)adımlarından adımları değiştirin.

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Windows sanal masaüstü Aracısı ve Windows sanal masaüstü önyükleme yükleyicisi yüklü değil

VM 'Leri sağlamak için önerilen yol, **Windows sanal masaüstü konak havuzu şablonu oluşturma ve sağlama** Azure Resource Manager kullanmaktır. Şablon, Windows sanal masaüstü Aracısı ve Windows sanal masaüstü Aracısı önyükleme yükleyicisini otomatik olarak yükler.

Bileşenlerin yüklendiğini doğrulamak ve hata iletilerini denetlemek için bu yönergeleri izleyin.

1. **Denetim Masası**  >  **Programlar**  >  **Programlar ve Özellikler ' i** denetleyerek iki bileşenin yüklendiğini onaylayın. **Windows sanal masaüstü Aracısı** ve **Windows sanal masaüstü Aracısı önyükleme yükleyicisi** görünür değilse, VM 'de yüklü değildir.
2. **Dosya Gezgini** 'ni açın ve **C:\windows\temp\scriptlog.log** konumuna gidin. Dosya eksikse, iki bileşeni yükleyen PowerShell DSC 'nin belirtilen güvenlik bağlamında çalıştırılmadığını gösterir.
3. **C:\windows\temp\scriptlog.log** dosyası varsa, açın ve hata iletilerini denetleyin.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>Hata: Windows sanal masaüstü Aracısı ve Windows sanal masaüstü Aracısı önyükleme yükleyicisi eksik. C:\Windows\Temp\ScriptLog.log de eksik

**Neden 1:** Azure Resource Manager şablonu için giriş sırasında belirtilen kimlik bilgileri yanlış veya izinler yetersiz.

**1. Çözüm:** [PowerShell ile konak havuzu oluşturma](create-host-pools-powershell-2019.md)' yı kullanarak eksik bileşenleri VM 'lere el ile ekleyin.

**Neden 2:** PowerShell DSC, Windows sanal masaüstü 'nde oturum açıp gerekli bilgileri alabilmesi için başlatma ve yürütme işlemi başarısız oldu, ancak tamamlanamayacak.

**2. Çözüm:** Aşağıdaki listedeki öğeleri onaylayın.

- Hesabın MFA içermediğinden emin olun.
- Kiracı adının doğru olduğunu ve kiracının Windows sanal masaüstü 'nde mevcut olduğunu doğrulayın.
- Hesabın en az RDS katkıda bulunan izinleri olduğunu onaylayın.

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>Hata: kimlik doğrulaması başarısız oldu, hata: C:\Windows\Temp\ScriptLog.log

**Neden:** PowerShell DSC 'nin yürütülmesi, ancak Windows sanal masaüstüne bağlanamadık.

**Çözüm:** Aşağıdaki listedeki öğeleri onaylayın.

- VM 'Leri Windows sanal masaüstü hizmeti ile el ile kaydedin.
- Windows sanal masaüstüne bağlanmak için kullanılan hesabın, kiracı üzerinde konak havuzları oluşturma izinlerine sahip olduğunu doğrulayın.
- Onaylama hesabında MFA yok.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Windows sanal masaüstü Aracısı Windows sanal masaüstü hizmeti 'ne kaydolmadı

Windows sanal masaüstü Aracısı, oturum ana bilgisayar VM 'lerine ilk kez yüklendiğinde (el ile veya Azure Resource Manager şablonu ve PowerShell DSC aracılığıyla), bir kayıt belirteci sağlar. Aşağıdaki bölümde, Windows sanal masaüstü Aracısı ve belirteci için geçerli olan sorun giderme sorunları ele alınmaktadır.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Hata: Get-RdsSessionHost cmdlet 'inde dosyalanmış durum durumu kullanılamıyor olarak gösterir

> [!div class="mx-imgBorder"]
> ![Get-RdsSessionHost cmdlet 'i, durumu kullanım dışı olarak gösterir.](../media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Neden:** Aracı kendisini yeni bir sürüme güncelleştiremeyebilir.

**Çözüm:** Aracıyı el ile güncelleştirmek için bu yönergeleri izleyin.

1. Oturum Ana bilgisayar VM 'sinde aracının yeni bir sürümünü indirin.
2. Görev Yöneticisi 'Ni başlatın ve hizmet sekmesinde Rdadgentönyükleme Yükleyicisi hizmetini durdurun.
3. Windows sanal masaüstü aracısının yeni sürümü için yükleyiciyi çalıştırın.
4. Kayıt belirteci sorulduğunda INVALID_TOKEN girişi kaldırın ve ileri ' ye basın (yeni bir belirteç gerekli değildir).
5. Yükleme sihirbazını doldurun.
6. Görev Yöneticisi 'Ni açın ve Rdadgentönyükleme Yükleyicisi hizmetini başlatın.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Hata: Windows sanal masaüstü Aracısı kayıt defteri girdisi IsRegistered değeri 0 değerini gösteriyor

**Neden:** Kayıt belirtecinin süresi doldu veya süre sonu 999999 ile üretildi.

**Çözüm:** Aracı kayıt defteri hatasını onarmak için bu yönergeleri izleyin.

1. Zaten bir kayıt belirteci varsa Remove-RDSRegistrationInfo ile kaldırın.
2. RDS-Newregistrationınfo ile yeni belirteç oluştur.
3. -ExpriationHours parametresinin 72 olarak ayarlandığını onaylayın (en büyük değer 99999 ' dir).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Hata: Windows sanal masaüstü Aracısı Get-RdsSessionHost çalıştırılırken bir sinyal bildirmiyor

**Neden 1:** Rdavgentönyükleme Yükleyicisi hizmeti durduruldu.

**1. Çözüm:** Görev Yöneticisi 'Ni başlatın ve hizmet sekmesi Rdadgentönyükleme Yükleyicisi hizmeti için durdurulmuş bir durum bildirirse hizmeti başlatın.

**Neden 2:** 443 numaralı bağlantı noktası kapatılabilir.

**2. Çözüm:** 443 numaralı bağlantı noktasını açmak için bu yönergeleri izleyin.

1. PSPing aracını [SysInternal araçlarından](/sysinternals/downloads/psping/)indirerek bağlantı noktası 443 ' nin açık olduğunu doğrulayın.
2. Aracının çalıştığı oturum ana bilgisayar VM 'sine PSPing 'yi yükler.
3. Komut istemi 'ni yönetici olarak açın ve aşağıdaki komutu verin:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Rdsping 'nin bilgileri RDBroker 'dan geri aldığını onaylayın:

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Windows sanal masaüstü yan yana yığınıyla ilgili sorunları giderme

Windows sanal masaüstü yan yana yığın Windows Server 2019 ile otomatik olarak yüklenir. Microsoft Windows Server 2016 veya Windows Server 2012 R2 'ye yan yana yığın yüklemek için Microsoft yükleyicisi 'ni (MSI) kullanın. Microsoft Windows 10 için, Windows sanal masaüstü yan yana yığın **enablesxstackrs.ps1** ile etkinleştirilir.

Yan yana yığının, oturum ana bilgisayar havuzu VM 'lerinde yüklü veya etkin olduğu başlıca üç yol vardır:

- Azure Resource Manager **Yeni Windows sanal masaüstü konak havuzu şablonu oluşturma ve sağlama**
- Ana görüntüde dahil edilip etkin olarak
- Her VM 'de el ile yüklenmiş veya etkinleştirilmiş (veya uzantılar/PowerShell ile)

Windows sanal masaüstü 'Nün yan yana Stack ile ilgili sorun yaşıyorsanız, yan yana yığının yüklendiğini veya etkinleştirildiğini doğrulamak için komut isteminden **qwinsta** komutunu yazın.

Yan yana yığın yüklenip etkinleştirilirse, **qwinsta** çıktısı çıktıda **RDP-sxs** ' i listeler.

> [!div class="mx-imgBorder"]
> ![Yan yana yığın, çıktıda RDP-sxs olarak listelenen qwinsta yüklü veya etkin.](../media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Aşağıda listelenen kayıt defteri girişlerini inceleyin ve değerlerinin eşleştiğini doğrulayın. Kayıt defteri anahtarları eksikse veya değerler uyuştubulunursa, yan yana yığının nasıl yeniden yükleneceğini öğrenmek için [PowerShell ile bir konak havuzu oluşturma](create-host-pools-powershell-2019.md) ' daki yönergeleri izleyin.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Hata: O_REVERSE_CONNECT_STACK_FAILURE

> [!div class="mx-imgBorder"]
> ![O_REVERSE_CONNECT_STACK_FAILURE hata kodu.](../media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Neden:** Yan yana yığın, oturum ana bilgisayar VM 'sinde yüklü değil.

**Çözüm:** Oturum Ana bilgisayar VM 'sine yan yana yığın yüklemek için bu yönergeleri izleyin.

1. Doğrudan oturum ana bilgisayar VM 'sine yerel yönetici olarak almak için Uzak Masaüstü Protokolü (RDP) kullanın.
2. PowerShell oturumunuzda kullanmak için [Windows sanal masaüstü PowerShell modülünü](/powershell/windows-virtual-desktop/overview/) indirip içeri aktarın ve ardından hesabınızda oturum açmak için bu cmdlet 'i çalıştırın:

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
    ```

3. [PowerShell ile bir konak havuzu oluşturma](create-host-pools-powershell-2019.md)' yı kullanarak yan yana yığını yükler.

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Düzgün bir şekilde bir Windows sanal masaüstü yan yana yığınını çözme

Yan yana yığının hatalı çalışmasına neden olabilecek bilinen koşullar vardır:

- Yan yana yığını etkinleştirmek için adımların doğru sırasını takip etmez
- Windows 10 geliştirilmiş çok yönlü disk (EVD) için otomatik güncelleştirme
- Uzak Masaüstü Oturumu Ana Bilgisayarı (RDSH) rolü eksik
- enablesxsstackrc.ps1 birden çok kez çalıştırma
- Yerel yönetici ayrıcalıklarına sahip olmayan bir hesapta enablesxsstackrc.ps1 çalıştırma

Bu bölümdeki yönergeler, Windows sanal masaüstü 'Nün yan yana yığınını kaldırmanızı sağlamanıza yardımcı olabilir. Yan yana yığını kaldırdıktan sonra yan yana yığını yeniden yüklemek için [PowerShell ile bir konak havuzu oluşturma](create-host-pools-powershell-2019.md) bölümünde "VM 'Yi Windows sanal masaüstü ana bilgisayar havuzuna Kaydet" bölümüne gidin.

Düzeltmeyi çalıştırmak için kullanılan VM 'nin, hatalı çalışan yan yana Stack ile aynı alt ağda ve etki alanında olması gerekir.

Aynı alt ağ ve etki alanından düzeltmeyi çalıştırmak için aşağıdaki yönergeleri izleyin:

1. Standart Uzak Masaüstü Protokolü (RDP) ile, bu, düzeltmesinin uygulanacağı VM 'ye bağlanın.
2. PsExec 'yi şuradan indirin https://docs.microsoft.com/sysinternals/downloads/psexec .
3. İndirilen dosyayı sıkıştırmayı açın.
4. Komut istemi 'ni yerel yönetici olarak başlatın.
5. PsExec 'nin sıkıştırısaklandığı klasöre gidin.
6. Komut isteminden aşağıdaki komutu kullanın:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!NOTE]
    >VMname, hatalı çalışan yan yana yığın ile VM 'nin makine adıdır.

7. Kabul et 'e tıklayarak PsExec lisans sözleşmesini kabul edin.

    > [!div class="mx-imgBorder"]
    > ![Yazılım Lisans Sözleşmesi ekran görüntüsü.](../media/SoftwareLicenseTerms.png)

    >[!NOTE]
    >Bu iletişim kutusu yalnızca PsExec ilk kez çalıştırıldığında görünür.

8. Komut istemi oturumu, hatalı çalışan yan yana Stack ile sanal makinede açıldıktan sonra qwinsta komutunu çalıştırın ve RDP-sxs adlı bir girdinin kullanılabilir olduğunu onaylayın. Aksi halde, sorun yan yana yığına bağlı olmadığından, sanal makinede yan yana bir yığın yok.

    > [!div class="mx-imgBorder"]
    > ![Yönetici komut istemi](../media/AdministratorCommandPrompt.png)

9. Aşağıdaki komutu çalıştırarak, sanal makinede yüklü olan Microsoft bileşenlerini hatalı yan yana yığın ile listeleyin.

    ```cmd
        wmic product get name
    ```

10. Yukarıdaki adımdaki ürün adlarıyla aşağıdaki komutu çalıştırın.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. "Uzak Masaüstü" ile başlayan tüm ürünleri kaldırın.

12. Tüm Windows Sanal Masaüstü bileşenleri kaldırıldıktan sonra, işletim sisteminiz için yönergeleri izleyin:

13. İşletim sisteminiz Windows Server ise, hatalı çalışan yan yana yığına sahip olan VM 'yi (Azure portal veya PsExec aracından) yeniden başlatın.

İşletim sisteminiz Microsoft Windows 10 ise aşağıdaki yönergelerle devam edin:

14. PsExec çalıştıran VM 'den, dosya Gezgini 'ni açın ve disablesxsstackrc.ps1, sanal makinenin sistem sürücüsüne sanal bir yan yana yığın ile kopyalayın.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname, hatalı çalışan yan yana yığın ile VM 'nin makine adıdır.

15. Önerilen işlem: PsExec aracından PowerShell 'i başlatın ve önceki adımda bulunan klasöre gidin ve disablesxsstackrc.ps1 çalıştırın. Alternatif olarak, aşağıdaki cmdlet 'leri çalıştırabilirsiniz:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Cmdlet 'ler çalışmayı tamamladıktan sonra, arızalı yan yana yığın ile sanal makineyi yeniden başlatın.

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>Uzak Masaüstü lisans modu yapılandırılmadı

Bir yönetici hesabı kullanarak Windows 10 Enterprise çoklu oturumunda oturum açarsanız, "Uzak Masaüstü lisans modu yapılandırılmadı, Uzak Masaüstü Hizmetleri X gün içinde çalışmayı durduracaktır. Bağlantı Aracısı sunucusunda, Uzak Masaüstü lisans modunu belirtmek için Sunucu Yöneticisi kullanın. "

Zaman sınırının süresi dolarsa, "Bu bilgisayar için kullanılabilir uzak masaüstü istemci erişim lisansı olmadığından uzak oturumun bağlantısı kesildi." ifadesini içeren bir hata iletisi görüntülenir.

Bu iletilerden birini görürseniz bu, görüntüde en son Windows güncelleştirmelerinin yüklü olmadığı veya Uzak Masaüstü lisans modunu Grup İlkesi aracılığıyla ayarladığınız anlamına gelir. Grup İlkesi ayarını denetlemek, Windows 10 Enterprise çoklu oturum sürümünü belirlemek ve ilgili güncelleştirmeyi yüklemek için sonraki bölümlerdeki adımları izleyin.

>[!NOTE]
>Windows sanal masaüstü, ana bilgisayar havuzunuz Windows Server oturum Konakları içerdiğinde yalnızca bir RDS istemci erişim lisansı (CAL) gerektirir. Bir RDS CAL yapılandırma hakkında bilgi edinmek için bkz. [istemci erişim lisanslarıyla RDS dağıtımınıza lisans](/windows-server/remote/remote-desktop-services/rds-client-access-license/).

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>Uzak Masaüstü lisans modu Grup İlkesi ayarını devre dışı bırak

VM 'de Grup İlkesi düzenleyicisini açıp **Yönetim Şablonları**  >  **Windows bileşenlerine** giderek  >  **Uzak Masaüstü Hizmetleri**  >  **Uzak Masaüstü oturumu ana bilgisayarı**  >  **lisanslama**  >  **Uzak Masaüstü lisans modunu ayarlayarak** Grup İlkesi ayarını kontrol edin. Grup İlkesi ayarı **etkinse**, **devre dışı** olarak değiştirin. Zaten devre dışıysa, olduğu gibi bırakın.

>[!NOTE]
>Etki alanınız aracılığıyla Grup İlkesi ayarlarsanız, bu Windows 10 Kurumsal Çoklu oturum VM 'lerini hedefleyen ilkelerde bu ayarı devre dışı bırakın.

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>Hangi Windows 10 Kurumsal Çoklu oturum sürümünü kullandığınızı tanımla

Hangi Windows 10 Kurumsal Çoklu oturum sürümünü olduğunu denetlemek için:

1. Yönetici hesabınızla oturum açın.
2. Başlangıç menüsünün yanındaki arama çubuğuna "About" yazın.
3. **PC 'Niz hakkında**' yı seçin.
4. "Sürüm" nın yanındaki numarayı denetleyin. Aşağıdaki görüntüde gösterildiği gibi, sayı "1809" ya da "1903" olmalıdır.

    > [!div class="mx-imgBorder"]
    > ![Windows belirtimleri penceresinin ekran görüntüsü. Sürüm numarası mavi renkle vurgulanır.](../media/windows-specifications.png)

Artık sürüm numaranızı bildiğinize göre ilgili bölüme atlayın.

### <a name="version-1809"></a>Sürüm 1809

Sürüm numaranız "1809" ifadesini görürseniz, [KB4516077 güncelleştirmesini](https://support.microsoft.com/help/4516077)yükler.

### <a name="version-1903"></a>Sürüm 1903

Azure galerisinden Windows 10, sürüm 1903 görüntüsünün en son sürümüyle konak işletim sistemini yeniden dağıtın.

## <a name="we-couldnt-connect-to-the-remote-pc-because-of-a-security-error"></a>Bir güvenlik hatası nedeniyle uzak BILGISAYARA bağlanamıyoruz

Kullanıcılarınız "bir hata görürseniz," bir güvenlik hatası nedeniyle uzak BILGISAYARA bağlanamıyoruz. Bu durum devam ederse, "varsayılan RDP 'yi değiştiren mevcut ilkeleri doğrulama" konusunda yardım için yöneticinize veya teknik desteğe sorun. Bu hatanın görünmesine neden olabilecek bir ilke, "Uzak Masaüstü Hizmetleri güvenlik ilkesinde oturum açmaya Izin ver" dir.

Bu ilke hakkında daha fazla bilgi edinmek için bkz. [Uzak Masaüstü Hizmetleri aracılığıyla oturum açmaya Izin verme](/windows/security/threat-protection/security-policy-settings/allow-log-on-through-remote-desktop-services).

## <a name="next-steps"></a>Sonraki adımlar

- Windows sanal masaüstü ve yükseltme izlemelerinin sorunlarını giderme hakkında genel bilgi için bkz. [sorun giderme genel bakış, geri bildirim ve destek](troubleshoot-set-up-overview-2019.md).
- Bir Windows sanal masaüstü ortamında kiracı ve konak havuzu oluştururken oluşan sorunları gidermek için bkz. [kiracı ve konak havuzu oluşturma](troubleshoot-set-up-issues-2019.md).
- Windows sanal masaüstündeki bir sanal makineyi (VM) yapılandırırken oluşan sorunları gidermek için bkz. [oturum ana bilgisayarı sanal makine yapılandırması](troubleshoot-vm-configuration-2019.md).
- Windows sanal masaüstü istemci bağlantılarıyla ilgili sorunları gidermek için bkz. [Windows sanal masaüstü hizmeti bağlantıları](troubleshoot-service-connection-2019.md).
- Uzak Masaüstü istemcileriyle ilgili sorunları gidermek için bkz [. uzak masaüstü Istemcisinde sorun giderme](../troubleshoot-client.md)
- Windows sanal masaüstü ile PowerShell kullanırken karşılaşılan sorunları gidermek için bkz. [Windows sanal masaüstü PowerShell](troubleshoot-powershell-2019.md).
- Hizmet hakkında daha fazla bilgi edinmek için bkz. [Windows sanal masaüstü ortamı](environment-setup-2019.md).
- Sorun giderme öğreticisini öğrenmek için bkz. [öğretici: Kaynak Yöneticisi şablonu dağıtımlarının sorunlarını giderme](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Denetim eylemleri hakkında bilgi edinmek için bkz. [Kaynak Yöneticisi Ile denetim işlemleri](../../azure-resource-manager/management/view-activity-logs.md).
- Dağıtım sırasında hataları belirleme eylemleri hakkında bilgi edinmek için bkz. [dağıtım Işlemlerini görüntüleme](../../azure-resource-manager/templates/deployment-history.md).
