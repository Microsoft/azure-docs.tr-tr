---
title: Windows 'da Azure dosyaları sorunlarını giderme | Microsoft Docs
description: Windows 'da Azure dosyaları sorunlarını giderme
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: f36d3bcb16876f080f780658bc59afd794e3431e
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699193"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Windows 'da Azure dosyaları sorunlarını giderme

Bu makalede, Windows istemcilerinden bağlandığınızda Microsoft Azure dosyalarla ilgili yaygın sorunlar listelenmektedir. Ayrıca, bu sorunlar için olası nedenler ve çözümler de sağlar. Bu makaledeki sorun giderme adımlarına ek olarak, Windows istemci ortamının doğru önkoşullara sahip olduğundan emin olmak için [azfilediagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) 'i de kullanabilirsiniz. AzFileDiagnostics, bu makalede bahsedilen belirtilerin çoğunu algılamayı otomatikleştirir ve en iyi performansı elde etmek için ortamınızı ayarlamanıza yardımcı olur. Bu bilgileri Azure dosya [paylaşımları sorun gidericisinde](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) de bulabilirsiniz. Bu bilgiler, Azure dosya paylaşımlarını bağlama/eşleme/bağlama sorunları konusunda size yardımcı olacak adımlar sağlar.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Bir Azure dosya paylaşımından bağlama yaparken hata 5

Bir dosya payını bağlamaya çalıştığınızda, şu hatayı alabilirsiniz:

- Sistem hatası 5 oluştu. Erişim reddedildi.

### <a name="cause-1-unencrypted-communication-channel"></a>Neden 1: Şifrelenmemiş iletişim kanalı

Güvenlik nedeniyle, iletişim kanalı şifrelenmemişse ve bağlantı girişimi Azure dosya paylaşımlarının bulunduğu veri merkezinden yapılmıyorsa Azure dosya paylaşımlarına bağlantılar engellenir. Depolama hesabında [Güvenli aktarım gerekli](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) ayarı etkinleştirildiyse aynı veri merkezi içinde şifrelenmemiş bağlantılar da engellenebilir. Şifrelenmiş bir iletişim kanalının sağlanabilmesi için kullanıcının istemcisi SMB şifrelemesini desteklemelidir.

Her sistemin Windows 8, Windows Server 2012 ve üstü sürümleri şifrelemeyi destekleyen SMB 3.0 içeren isteklerle anlaşır.

### <a name="solution-for-cause-1"></a>Neden 1 için çözüm

1. SMB şifrelemesini (Windows 8, Windows Server 2012 veya üzeri) destekleyen bir istemciden bağlanın veya aynı veri merkezindeki bir sanal makineden Azure dosya paylaşımında kullanılan Azure depolama hesabıyla bağlanın.
2. İstemci SMB şifrelemesini desteklemiyorsa, depolama hesabında [Güvenli aktarım gerekli](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) ayarının devre dışı olduğunu doğrulayın.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Neden 2: Depolama hesabında sanal ağ veya güvenlik duvarı kuralları etkin 

Depolama hesabında sanal ağ (VNET) ve güvenlik duvarı kuralları yapılandırıldıysa, istemci IP adresine veya sanal ağa erişim izni verilmediği sürece ağ trafiğinin erişimi reddedilir.

### <a name="solution-for-cause-2"></a>Neden 2 için çözüm

Depolama hesabında sanal ağ ve güvenlik duvarı kurallarının düzgün yapılandırıldığını doğrulayın. Sanal ağ veya güvenlik duvarı kurallarının soruna neden olup olmadığını test etmek için depolama hesabında **Tüm ağlardan erişime izin ver** ayarını geçici olarak değiştirin. Daha fazla bilgi edinmek için bkz. [Azure Depolama güvenlik duvarlarını ve sanal ağları yapılandırma](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Bir Azure dosya paylaşımını bağladığınızda veya kaldırdığınızda hata 53, hata 67 veya hata 87

Şirket içinden veya farklı bir veri merkezinden bir dosya paylaşımının bağlamaya çalıştığınızda aşağıdaki hataları alabilirsiniz:

- Sistem hatası 53 oluştu. Ağ yolu bulunamadı.
- Sistem hatası 67 oluştu. Ağ adı bulunamıyor.
- Sistem hatası 87 oluştu. Parametre yanlış.

### <a name="cause-1-port-445-is-blocked"></a>Neden 1: 445 numaralı bağlantı noktası engellendi

Bir Azure dosyaları veri merkezi ile bağlantı noktası 445 giden iletişim engellendiğinde sistem hatası 53 veya sistem hatası 67 oluşur. 445 numaralı bağlantı noktasından erişime izin veren veya erişimi engelleyen ISP'lerin özetini görmek için [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx)'e gidin.

Güvenlik duvarınızın veya ISS 'nizin bağlantı noktası 445 ' i engelleyip engellemediğini denetlemek için [azfilediagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) aracını veya `Test-NetConnection` cmdlet 'ini kullanın. 

`Test-NetConnection` Cmdlet 'ini kullanmak için Azure PowerShell modülünün yüklenmesi gerekir. daha fazla bilgi için bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps) . `<your-storage-account-name>` ile `<your-resource-group-name>` yerine depolama hesabınızla ilgili bilgileri yazmayı unutmayın.

   
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    
Bağlantı başarılı olursa şu çıktıyı görmeniz gerekir:
    
  
    ComputerName     : <your-storage-account-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
 

> [!Note]  
> Yukarıdaki komut, depolama hesabının geçerli IP adresini döndürür. Bu IP adresinin aynı kalacağı garanti edilmez ve bu adres herhangi bir zamanda değişebilir. Bu IP adresini betiklere veya güvenlik duvarına sabit şekilde kodlamayın.

### <a name="solution-for-cause-1"></a>Neden 1 için çözüm

#### <a name="solution-1---use-azure-file-sync"></a>Çözüm 1-Azure Dosya Eşitleme kullanın
Azure Dosya Eşitleme, şirket içi Windows Server 'larınızı Azure dosya paylaşımınızın hızlı bir önbelleğine dönüştürür. SMB, NFS ve FTPS dahil olmak üzere verilerinize yerel olarak erişmek için Windows Server 'da bulunan herhangi bir protokolü kullanabilirsiniz. Azure Dosya Eşitleme 443 bağlantı noktası üzerinden çalışarak, bağlantı noktası 445 ' nin engellediği istemcilerden Azure dosyalarına erişmek için geçici bir çözüm olarak kullanılabilir. [Azure dosya eşitleme ayarlamayı öğrenin](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>Çözüm 2-VPN kullanma
Belirli depolama hesabınıza bir VPN ayarlayarak trafik, internet üzerinden değil, güvenli bir tünelden geçer. Windows 'dan Azure dosyalarına erişmek için](https://github.com/Azure-Samples/azure-files-samples/tree/master/point-to-site-vpn-azure-files
) VPN Kurulumu için yönergeleriizleyin.[

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Çözüm 3-ISS/BT yöneticinizin yardımıyla bağlantı noktası 445 engelini kaldırın
[Azure IP aralıklarına](https://www.microsoft.com/download/details.aspx?id=41653)giden 445 numaralı bağlantı noktasını açmak için BT departmanınızla veya ISS 'niz ile çalışın.

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Çözüm 4-Depolama Gezgini/PowerShell gibi REST API tabanlı araçlar kullanın
Azure dosyaları da SMB 'ye ek olarak REST 'i destekler. REST Access 443 numaralı bağlantı noktasından (Standart TCP) çalışmaktadır. REST API kullanılarak yazılan ve zengin Kullanıcı arabirimi deneyimini etkinleştiren çeşitli araçlar vardır. [Depolama Gezgini](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) bunlardan biridir. [Depolama Gezgini Indirip yükleyin](https://azure.microsoft.com/features/storage-explorer/) ve Azure dosyaları tarafından desteklenen dosya paylaşımınıza bağlanın. Ayrıca, Kullanıcı REST API de [PowerShell](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) kullanabilirsiniz.

### <a name="cause-2-ntlmv1-is-enabled"></a>Neden 2: NTLMv1 etkin

İstemcide NTLMv1 iletişimi etkinse sistem hatası 53 veya sistem hatası 87 oluşur. Azure Dosyalar yalnızca NTLMv2 kimlik doğrulamasını destekler. NTLMv1'in etkinleştirilmesi daha az güvenli bir istemci oluşturur. Bu nedenle Azure Dosyalar için iletişim engellenir. 

Hatanın nedeninin bu olup olmadığını saptamak için aşağıdaki kayıt defteri alt anahtarının 3 değerine ayarlandığını doğrulayın:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Daha fazla bilgi için TechNet'te [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) konusuna bakın.

### <a name="solution-for-cause-2"></a>Neden 2 için çözüm

Aşağıdaki kayıt defteri alt anahtarında **LmCompatibilityLevel** değerini varsayılan değeri olan 3'e döndürün:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Hata 1816 "Bu komutu işlemek için yeterli kota yok" bir Azure dosya paylaşımında kopyaladığınızda

### <a name="cause"></a>Nedeni

Dosya paylaşımının takılabileceği bilgisayardaki bir dosya için izin verilen eş zamanlı açık tanıtıcıların üst sınırına ulaştığınızda hata 1816 olur.

### <a name="solution"></a>Çözüm

Bazı tutamaçları kapatarak eşzamanlı açık tanıtıcı sayısını azaltın ve yeniden deneyin. Daha fazla bilgi için bkz. [performans ve ölçeklenebilirlik denetim listesi Microsoft Azure depolama](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Bir dosya paylaşımının, dizinin veya dosyanın açık tanıtıcılarını görüntülemek için [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell cmdlet 'ini kullanın.  

Bir dosya paylaşımının, dizinin veya dosyanın açık tanıtıcılarını kapatmak için, [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell cmdlet 'ini kullanın.

> [!Note]  
> Get-AzStorageFileHandle ve Close-AzStorageFileHandle cmdlet 'leri az PowerShell Module sürüm 2,4 veya üzeri sürümlerde bulunur. En son az PowerShell modülünü yüklemek için bkz. [Azure PowerShell modülünü yüklemek](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="authorizationfailureportal"></a>
## <a name="error-authorization-failure-when-browsing-to-an-azure-file-share-in-the-portal"></a>Portalda bir Azure dosya paylaşımında gezinirken "yetkilendirme hatası" hatası

Portalda bir Azure dosya paylaşımınıza gözattığınızda, şu hatayı alabilirsiniz:

Yetkilendirme hatası  
Erişim izniniz yok 

### <a name="cause-1-your-user-account-does-not-have-access-to-the-storage-account"></a>Neden 1: Kullanıcı hesabınızın depolama hesabına erişimi yok

### <a name="solution-for-cause-1"></a>Neden 1 için çözüm

Azure dosya paylaşımının bulunduğu depolama hesabına gidin, **erişim denetimi (IAM)** öğesine tıklayın ve Kullanıcı hesabınızın depolama hesabına erişimi olduğunu doğrulayın. Daha fazla bilgi edinmek için bkz. [rol tabanlı Access Control (RBAC) ile depolama hesabınızın güvenliğini sağlama](https://docs.microsoft.com/azure/storage/common/storage-security-guide#how-to-secure-your-storage-account-with-role-based-access-control-rbac).

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Neden 2: Depolama hesabında sanal ağ veya güvenlik duvarı kuralları etkin

### <a name="solution-for-cause-2"></a>Neden 2 için çözüm

Depolama hesabında sanal ağ ve güvenlik duvarı kurallarının düzgün yapılandırıldığını doğrulayın. Sanal ağ veya güvenlik duvarı kurallarının soruna neden olup olmadığını test etmek için depolama hesabında **Tüm ağlardan erişime izin ver** ayarını geçici olarak değiştirin. Daha fazla bilgi edinmek için bkz. [Azure Depolama güvenlik duvarlarını ve sanal ağları yapılandırma](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Azure dosya paylaşımındaki bir dosya veya dizin silinemiyor

### <a name="cause"></a>Nedeni
Bu sorun genellikle dosya veya dizinde açık bir tanıtıcı varsa oluşur. 

### <a name="solution"></a>Çözüm

SMB istemcileri tüm açık tutamaçları kapatmışsa ve sorun oluşmaya devam ederse, şunları yapın:

- Açık tutamaçları görüntülemek için [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell cmdlet 'ini kullanın.

- Açık tutamaçları kapatmak için [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell cmdlet 'ini kullanın. 

> [!Note]  
> Get-AzStorageFileHandle ve Close-AzStorageFileHandle cmdlet 'leri az PowerShell Module sürüm 2,4 veya üzeri sürümlerde bulunur. En son az PowerShell modülünü yüklemek için bkz. [Azure PowerShell modülünü yüklemek](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Windows 'da Azure dosyalarından yavaş dosya kopyalama

Azure dosya hizmetine dosya aktarmaya çalıştığınızda yavaş performans görebilirsiniz.

- Belirli bir en düşük g/ç boyutu gereksiniminize sahip değilseniz en iyi performans için g/ç boyutu olarak 1 MIB kullanmanızı öneririz.
-   Yazmalar ile genişletilen bir dosyanın son boyutunu biliyorsanız ve bu dosyada, yazılı olmayan kuyruk sıfır içerdiğinde yazılım uyumluluk sorunlarıyla karşılaşırsanız, her yazma için bir genişletme yazma yapmak yerine dosya boyutunu önceden ayarlayın.
-   Doğru kopyalama yöntemini kullanın:
    -   İki dosya paylaşımı arasındaki herhangi bir aktarım için [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) kullanın.
    -   Şirket içi bilgisayardaki dosya paylaşımları arasında [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) kullanın.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Windows 8.1 veya Windows Server 2012 R2 ile ilgili konular

Windows 8.1 veya Windows Server 2012 R2 çalıştıran istemciler için [KB3114025](https://support.microsoft.com/help/3114025) düzeltmesinin yüklü olduğundan emin olun. Bu düzeltme, oluşturma ve kapatma tanıtıcılarının performansını geliştirir.

Düzeltmenin yüklenip yüklenmediğini denetlemek için aşağıdaki betiği çalıştırabilirsiniz:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Düzeltme yüklüyse, aşağıdaki çıktı görüntülenir:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Azure Marketi 'ndeki Windows Server 2012 R2 görüntüleri, varsayılan olarak güncelleştirme 2015 ' den itibaren KB3114025 yüklüdür.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>"Bilgisayarımda" veya "Bu bılgısayar" içinde sürücü harfine sahip bir klasör yok

Bir Azure dosya paylaşımından, net use kullanarak yönetici olarak eşleme yaptıysanız, paylaşımın eksik olduğu görülüyor.

### <a name="cause"></a>Nedeni

Varsayılan olarak, Windows Dosya Gezgini yönetici olarak çalışmaz. Net use komutunu bir yönetim komut isteminden çalıştırırsanız, ağ sürücüsünü yönetici olarak eşlersiniz. Eşlenen sürücüler Kullanıcı merkezli olduğundan, oturum açmış olan kullanıcı hesabı, farklı bir kullanıcı hesabı altına bağlandıklarında sürücüleri görüntülemez.

### <a name="solution"></a>Çözüm
Paylaşımdan yönetici olmayan bir komut satırından bağlayın. Alternatif olarak, **EnableLinkedConnections** kayıt defteri değerini yapılandırmak Için [Bu TechNet konusunu](https://technet.microsoft.com/library/ee844140.aspx) da izleyebilirsiniz.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Depolama hesabı eğik çizgi içeriyorsa net use komutu başarısız olur

### <a name="cause"></a>Nedeni

Net use komutu bir eğik çizgi (/) bir komut satırı seçeneği olarak yorumlar. Kullanıcı hesabınızın adı eğik çizgiyle başlıyorsa sürücü eşleştirmesi başarısız olur.

### <a name="solution"></a>Çözüm

Sorunu geçici olarak çözmek için aşağıdaki adımlardan birini kullanabilirsiniz:

- Aşağıdaki PowerShell komutunu çalıştırın:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  Bir toplu iş dosyasından komutunu şu şekilde çalıştırabilirsiniz:

  `Echo new-smbMapping ... | powershell -command –`

- Bu sorunu geçici olarak çözmek için anahtarın etrafına çift tırnak işareti koyun; eğik çizgi ilk karakter değilse. Bu durumda, etkileşimli modu kullanın ve parolanızı ayrı olarak girin ya da eğik çizgiyle başlamaymeyen bir anahtar almak için anahtarlarınızı yeniden oluşturun.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Uygulama veya hizmet bağlı bir Azure dosyaları sürücüsüne erişemiyor

### <a name="cause"></a>Nedeni

Sürücüler Kullanıcı başına bağlanır. Uygulamanız veya hizmetiniz sürücüyü takenden farklı bir kullanıcı hesabı altında çalışıyorsa, uygulama sürücüyü görmez.

### <a name="solution"></a>Çözüm

Şu çözümlerden birini kullanın:

-   Sürücüyü uygulamayı içeren kullanıcı hesabından bağlayın. PsExec gibi bir araç kullanabilirsiniz.
- Depolama hesabı adını ve anahtarını net use komutunun Kullanıcı adı ve parola parametrelerine geçirin.
- Kimlik bilgilerini kimlik bilgileri Yöneticisi 'ne eklemek için cmdkey komutunu kullanın. Bunu, etkileşimli oturum açma yoluyla veya runas kullanarak hizmet hesabı bağlamı altındaki bir komut satırından gerçekleştirin.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Eşlenen bir sürücü harfi kullanmadan doğrudan paylaşma eşleyin. Bazı uygulamalar sürücü harfine düzgün şekilde yeniden bağlanmayabilir, bu nedenle tam UNC yolunun kullanılması daha güvenilir olabilir. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Bu yönergeleri izledikten sonra, sistem/ağ hizmeti hesabı için net use çalıştırdığınızda aşağıdaki hata iletisini alabilirsiniz: "Sistem hatası 1312 oluştu. Belirtilen bir oturum açma oturumu yok. Zaten sonlandırılmış olabilir. " Bu durumda, ağ kullanımına geçirilen kullanıcı adının etki alanı bilgilerini içerdiğinden emin olun (örneğin: "[depolama hesabı adı]. File. Core. Windows. net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>"Şifrelemeyi desteklemeyen bir hedefe dosya kopyalıyorsunuz" hatası

Ağ üzerinden bir dosya kopyalandığında, dosya, düz metin olarak iletilir ve hedefte yeniden şifrelendiğinde kaynak bilgisayarda şifresi çözülür. Ancak, şifrelenmiş bir dosyayı kopyalamaya çalışırken şu hatayı görebilirsiniz: "Dosyayı şifrelemeyi desteklemeyen bir hedefe kopyalıyorsunuz."

### <a name="cause"></a>Nedeni
Şifreleme dosya sistemi (EFS) kullanıyorsanız, bu sorun oluşabilir. BitLocker ile şifrelenen dosyalar Azure dosyalarına kopyalanabilir. Ancak, Azure dosyaları NTFS EFS 'yi desteklemez.

### <a name="workaround"></a>Geçici Çözüm
Ağ üzerinden bir dosyayı kopyalamak için, önce onu çözmelisiniz. Aşağıdaki yöntemlerden birini kullanın:

- **Copy/d** komutunu kullanın. Şifrelenmiş dosyaların hedefte şifresi çözülmüş dosyalar olarak kaydedilmesine izin verir.
- Aşağıdaki kayıt defteri anahtarını ayarlayın:
  - Yol = HKLM\Software\Policies\Microsoft\Windows\System
  - Değer türü = DWORD
  - Ad = CopyFileAllowDecryptedRemoteDestination
  - Değer = 1

Kayıt defteri anahtarını ayarlamanın ağ paylaşımlarına yapılan tüm kopyalama işlemlerini etkilediğini unutmayın.

## <a name="slow-enumeration-of-files-and-folders"></a>Dosya ve klasörlerin yavaş numaralandırılması

### <a name="cause"></a>Nedeni

Bu sorun, büyük dizinler için istemci makinesinde yeterli önbellek olmadığında ortaya çıkabilir.

### <a name="solution"></a>Çözüm

Bu sorunu çözmek için **DirectoryCacheEntrySizeMax** kayıt defteri değerini, istemci makinedeki daha büyük dizin listelerinin önbelleğe alınmasına izin verecek şekilde ayarlama:

- Konum: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Değer Mane: DirectoryCacheEntrySizeMax 
- Değer türü: DWORD
 
 
Örneğin, bunu 0x100000 olarak ayarlayabilir ve performansın daha iyi olup olmadığını görebilirsiniz.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Hata AadDsTenantNotFound Azure dosyaları için Azure Active Directory kimlik doğrulamasını etkinleştirme "Kiracı kimliği AAD-Tenant-ID" olan etkin kiracılar bulunamıyor

### <a name="cause"></a>Nedeni

[Azure dosyaları için Azure Active Directory (AAD) kimlik doğrulamasını](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable) , [AAD etki alanı HIZMETI 'nın (AAD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) ilişkili aboneliğin AAD kiracısında oluşturulabileceği bir depolama hesabında etkinleştirmeye çalıştığınızda hata AadDsTenantNotFound olur.  

### <a name="solution"></a>Çözüm

Depolama hesabınızın dağıtıldığı aboneliğin AAD kiracısında AAD DS 'yi etkinleştirin. Yönetilen bir etki alanı oluşturmak için AAD kiracısının yönetici ayrıcalıklarına sahip olmanız gerekir. Azure AD kiracısı yöneticisi değilseniz, yöneticiye başvurun ve [Azure Portal kullanarak Azure Active Directory Domain Services etkinleştirmek](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)için adım adım yönergeleri izleyin.

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="need-help-contact-support"></a>Yardım mı gerekiyor? Desteğe başvurun.
Hala yardıma ihtiyacınız varsa, sorununuzun hızla çözülmesini sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .
