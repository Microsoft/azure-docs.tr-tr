---
title: Linux 'ta Azure dosyaları sorunlarını giderme | Microsoft Docs
description: Linux 'ta Azure dosyaları sorunlarını giderme
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 005e93837d1d420526f6fb33e79d25a94da6fab7
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838545"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Linux 'ta Azure dosyaları sorunlarını giderme

Bu makalede, Linux istemcilerinden bağlandığınızda Azure dosyalarıyla ilgili yaygın sorunlar listelenmektedir. Ayrıca, bu sorunlar için olası nedenler ve çözümler de sağlar. 

Bu makaledeki sorun giderme adımlarına ek olarak, Linux istemcisinin doğru önkoşullara sahip olduğundan emin olmak için [Azfilediagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) 'i kullanabilirsiniz. AzFileDiagnostics, bu makalede bahsedilen belirtilerin çoğunun algılanmasını otomatikleştirir. En iyi performansı elde etmek için ortamınızı ayarlamanıza yardımcı olur. Ayrıca, bu bilgileri [Azure dosya paylaşımları sorun gidericisinde](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares)bulabilirsiniz. Sorun giderici, Azure dosya paylaşımlarını bağlama, eşleme ve bağlama ile ilgili sorunları gidermenize yardımcı olacak adımları sağlar.

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Azure dosya paylaşımında bağlantı veya bağlama yapılamaz

### <a name="cause"></a>Nedeni

Bu sorunun yaygın nedenleri şunlardır:

- Uyumsuz bir Linux dağıtım istemcisi kullanıyorsunuz. Azure dosya paylaşımında bağlantı kurmak için aşağıdaki Linux dağıtımlarını kullanmanızı öneririz:

|   | SMB 2.1 <br>(Aynı Azure bölgesi içindeki VM 'lerde takar) | SMB 3.0 <br>(Şirket içi ve çapraz bölgeden takar) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04 + | 16.04 + |
| RHEL | 7 + | 7.5+ |
| CentOS | 7 + |  7.5+ |
| Debian | 8+ |   |
| openSUSE | 13.2 + | 42.3 + |
| SUSE Linux Enterprise Server | 12 | 12 SP3 + |

- CIFS yardımcı programları (CIFS-utils) istemcide yüklü değil.
- İstemcide en düşük SMB/CIFS sürümü (2,1) yüklü değil.
- SMB 3,0 şifrelemesi istemcide desteklenmiyor. Yukarıdaki tabloda, şifrelemeyi kullanarak şirket içi ve çapraz bölgeden bağlamayı destekleyen Linux dağıtımların bir listesi verilmiştir. Diğer dağıtımlar için çekirdek 4.11 ve üstü sürümler gerekir.
- TCP bağlantı noktası 445 üzerinden bir depolama hesabına bağlanmaya çalışıyorsunuz, bu desteklenmez.
- Azure VM 'den bir Azure dosya paylaşımıyla bağlantı kurmaya çalışıyorsunuz ve VM depolama hesabı ile aynı bölgede değil.
- Depolama hesabında [Güvenli aktarım gerekli]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) ayarı etkinse, Azure dosyaları yalnızca ŞIFRELEME ile SMB 3,0 kullanan bağlantılara izin verir.

### <a name="solution"></a>Çözüm

Sorunu gidermek için, [Linux 'Ta Azure dosyaları bağlama hataları için sorun giderme aracını](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089)kullanın. Bu araç:

* Ortamı çalıştıran istemciyi doğrulamanıza yardımcı olur.
* Azure dosyaları için erişim hatasına neden olacak uyumsuz istemci yapılandırmasını algılar.
* Kendi kendine düzeltme konusunda öngörülü kılavuzluk sağlar.
* Tanılama izlemelerini toplar.

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>"Bağlama hatası (13): bir Azure dosya paylaşımından bağlama sırasında Izin verilmedi"

### <a name="cause-1-unencrypted-communication-channel"></a>Neden 1: şifrelenmemiş iletişim kanalı

Güvenlik nedeniyle, iletişim kanalı şifrelenmemişse ve bağlantı girişimi Azure dosya paylaşımlarının bulunduğu veri merkezinden yapılmıyorsa Azure dosya paylaşımlarına bağlantılar engellenir. Depolama hesabında [Güvenli aktarım gerekli](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) ayarı etkinleştirildiyse aynı veri merkezi içinde şifrelenmemiş bağlantılar da engellenebilir. Şifrelenmiş bir iletişim kanalının sağlanabilmesi için kullanıcının istemcisi SMB şifrelemesini desteklemelidir.

Daha fazla bilgi edinmek için bkz. [Linux ve cifs-utils paketiyle Azure dosya paylaşımını bağlamak için önkoşullar](storage-how-to-use-files-linux.md#prerequisites). 

### <a name="solution-for-cause-1"></a>Neden 1 için çözüm

1. SMB şifrelemesini destekleyen bir istemciden bağlanın veya aynı veri merkezindeki bir sanal makineden Azure dosya paylaşımında kullanılan Azure depolama hesabıyla bağlanın.
2. İstemci SMB şifrelemesini desteklemiyorsa, depolama hesabında [Güvenli aktarım gerekli](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) ayarının devre dışı olduğunu doğrulayın.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Neden 2: depolama hesabında sanal ağ veya güvenlik duvarı kuralları etkin 

Depolama hesabında sanal ağ (VNET) ve güvenlik duvarı kuralları yapılandırıldıysa, istemci IP adresine veya sanal ağa erişim izni verilmediği sürece ağ trafiğinin erişimi reddedilir.

### <a name="solution-for-cause-2"></a>Neden 2 için çözüm

Depolama hesabında sanal ağ ve güvenlik duvarı kurallarının düzgün yapılandırıldığını doğrulayın. Sanal ağ veya güvenlik duvarı kurallarının soruna neden olup olmadığını test etmek için depolama hesabında **Tüm ağlardan erişime izin ver** ayarını geçici olarak değiştirin. Daha fazla bilgi edinmek için bkz. [Azure Depolama güvenlik duvarlarını ve sanal ağları yapılandırma](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"[izin reddedildi] disk kotası aşıldı" bir dosyayı açmaya çalıştığınızda

Linux 'ta aşağıdakine benzer bir hata iletisi alırsınız:

**\<dosya adı > [izin reddedildi] disk kotası aşıldı**

### <a name="cause"></a>Nedeni

Bir dosya için izin verilen eş zamanlı açık tanıtıcıların üst sınırına ulaştınız.

Tek bir dosyada 2.000 açık tanıtıcı kotası vardır. 2\.000 açık tutamacı varsa, kotanın ulaşılmadığını belirten bir hata iletisi görüntülenir.

### <a name="solution"></a>Çözüm

Bazı tutamaçları kapatarak eşzamanlı açık tanıtıcı sayısını azaltın ve işlemi yeniden deneyin.

Bir dosya paylaşımının, dizinin veya dosyanın açık tanıtıcılarını görüntülemek için [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell cmdlet 'ini kullanın.  

Bir dosya paylaşımının, dizinin veya dosyanın açık tanıtıcılarını kapatmak için, [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell cmdlet 'ini kullanın.

> [!Note]  
> Get-AzStorageFileHandle ve Close-AzStorageFileHandle cmdlet 'leri az PowerShell Module sürüm 2,4 veya üzeri sürümlerde bulunur. En son az PowerShell modülünü yüklemek için bkz. [Azure PowerShell modülünü yüklemek](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Linux 'ta Azure dosyalarından yavaş dosya kopyalama

- Belirli bir en düşük g/ç boyutu gereksiniminize sahip değilseniz en iyi performans için g/ç boyutu olarak 1 MIB kullanmanızı öneririz.
- Doğru kopyalama yöntemini kullanın:
    - İki dosya paylaşımı arasındaki herhangi bir aktarım için [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) kullanın.
    - CP veya DD 'yi paralel olarak kullanmak kopyalama hızını iyileştirebilir, iş parçacıklarının sayısı kullanım örneğine ve iş yüküne bağlıdır. Aşağıdaki örneklerde altı kullanılır: 
    - CP örneği (CP dosya sisteminin varsayılan blok boyutunu öbek boyutu olarak kullanır): `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &`.
    - dd örneği (Bu komut, öbek boyutunu açıkça 1 MiB olarak belirler): `find * -type f | parallel --will-cite-j 6 dd if={} of=/mnt/share/{} bs=1M`
    - Açık kaynak üçüncü taraf araçları:
        - [GNU paralel](https://www.gnu.org/software/parallel/).
        - [Fpart](https://github.com/martymac/fpart) -dosyaları sıralar ve onları bölümler halinde paketler.
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) -src_dir verileri dst_url geçirilecek birden çok örnek atamak Için fpart ve bir kopyalama aracı kullanır.
        - GNU coreutils tabanlı [Çoklu](https://github.com/pkolano/mutil) iş parçacıklı CP ve md5sum.
- Dosya boyutunu önceden ayarlamak, her yazma için bir genişletme yazma yapmak yerine dosya boyutunun bilinen senaryolarda kopyalama hızını artırmaya yardımcı olur. Yazmaları genişletmeyi önlenebilir olması gerekiyorsa, `truncate - size <size><file>` komutuyla bir hedef dosya boyutu belirleyebilirsiniz. Bundan sonra, `dd if=<source> of=<target> bs=1M conv=notrunc`komutu, hedef dosyanın boyutunu sürekli olarak güncelleştirmek zorunda kalmadan bir kaynak dosyayı kopyalayacaktır. Örneğin, kopyalamak istediğiniz her dosya için hedef dosya boyutunu ayarlayabilirsiniz (bir paylaşımın/mnt/Share altında bağlanmış olduğunu varsayın):
    - `$ for i in `` find * -type f``; do truncate --size ``stat -c%s $i`` /mnt/share/$i; done`
    - sonra, dosyaları paralel olarak genişletmeksizin Kopyala: `$find * -type f | parallel -j6 dd if={} of =/mnt/share/{} bs=1M conv=notrunc`

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Bağlama hatası (115): Azure dosyalarını SMB 3,0 kullanarak bağladığınızda Işlem şimdi devam ediyor.

### <a name="cause"></a>Nedeni

Bazı Linux dağıtımları henüz SMB 3.0'daki şifreleme özelliklerini desteklemiyor. Azure Dosyalar'ı SMB 3.0 kullanarak bağlamaya çalışan kullanıcılar eksik özellik nedeniyle "115" hata iletisini alabilir. Tam şifreleme ile SMB 3.0 yalnızca Ubuntu 16.04 veya üstünü kullandığınızda desteklenir.

### <a name="solution"></a>Çözüm

Linux için SMB 3.0'ın şifreleme özelliği 4.11 çekirdeğinde kullanıma sunuldu. Bu özellik Azure dosya paylaşımını şirket içinden veya farklı bir Azure bölgesinden bağlamaya olanak tanır. Bazı Linux dağıtımları, 4,11 çekirdekten, korudukları Linux çekirdeğinin eski sürümlerine yönelik geri alınan değişikliklere sahip olabilir. Linux sürümünüzün şifreleme ile SMB 3,0 ' i destekleyip desteklemediğini belirlemeye yardımcı olmak için, [Linux Ile Azure dosyaları kullanma](storage-how-to-use-files-linux.md)bölümüne başvurun. 

Linux SMB istemciniz şifrelemeyi desteklemiyorsa, Azure Dosyalar'ı bağlamak için dosya paylaşımıyla aynı veri merkezinde bulunan bir Azure Linux VM'sinden SMB 2.1'i kullanın. Depolama hesabında [Güvenli aktarım gerekli]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) ayarının devre dışı bırakıldığını doğrulayın. 

<a id="authorizationfailureportal"></a>
## <a name="error-authorization-failure-when-browsing-to-an-azure-file-share-in-the-portal"></a>Portalda bir Azure dosya paylaşımında gezinirken "yetkilendirme hatası" hatası

Portalda bir Azure dosya paylaşımınıza gözattığınızda, şu hatayı alabilirsiniz:

Yetkilendirme hatası  
Erişim izniniz yok

### <a name="cause-1-your-user-account-does-not-have-access-to-the-storage-account"></a>Neden 1: Kullanıcı hesabınızın depolama hesabına erişimi yok

### <a name="solution-for-cause-1"></a>Neden 1 için çözüm

Azure dosya paylaşımının bulunduğu depolama hesabına gidin, **erişim denetimi (IAM)** öğesine tıklayın ve Kullanıcı hesabınızın depolama hesabına erişimi olduğunu doğrulayın. Daha fazla bilgi edinmek için bkz. [rol tabanlı Access Control (RBAC) ile depolama hesabınızın güvenliğini sağlama](https://docs.microsoft.com/azure/storage/common/storage-security-guide#how-to-secure-your-storage-account-with-role-based-access-control-rbac).

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Neden 2: depolama hesabında sanal ağ veya güvenlik duvarı kuralları etkin

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

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Linux VM 'ye bağlanmış bir Azure dosya paylaşımında yavaş performans

### <a name="cause-1-caching"></a>Neden 1: önbelleğe alma

Yavaş performansın olası bir nedeni önbelleğe alma işlemini devre dışı bırakmış. Önbelleğe alma, bir dosyaya sürekli olarak erişiyorsanız yararlı olabilir, aksi takdirde bir ek yük olabilir. Devre dışı bırakmadan önce önbelleği kullanıp kullankullandığınızı denetleyin.

### <a name="solution-for-cause-1"></a>Neden 1 için çözüm

Önbelleğe almanın devre dışı olup olmadığını denetlemek için **Cache =** entry ' i arayın.

**Cache = None** , önbelleğe almanın devre dışı olduğunu gösterir. Varsayılan Mount komutunu kullanarak veya varsayılan önbelleğe alma veya "katı" önbelleğe alma modunun etkinleştirildiğinden emin olmak için Mount komutuna **Cache = Strict** seçeneğini açıkça ekleyerek, paylaşıma yeniden bağlayın.

Bazı senaryolarda, **serverino** bağlama seçeneği **ls** komutunun her dizin girdisinde stat çalıştırmasına neden olabilir. Bu davranış, büyük bir dizin listelenirken performans düşüşüne neden olur. **/Etc/fstab** girişinizdeki bağlama seçeneklerini kontrol edebilirsiniz:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Ayrıca, **sudo Mount | grep CIFS** komutunu çalıştırarak ve çıktısını denetleyerek doğru seçeneklerin kullanılıp kullanılmadığını kontrol edebilirsiniz. Örnek çıkış aşağıda verilmiştir:

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

**Cache = Strict** veya **serverino** seçeneği mevcut değilse, [belgelerden](../storage-how-to-use-files-linux.md)Mount komutunu çalıştırarak Azure dosyalarını yeniden çıkarın ve bağlayın. Sonra, **/etc/fstab** girişinin doğru seçeneklere sahip olduğunu denetleyin.

### <a name="cause-2-throttling"></a>Neden 2: daraltma

Azaltma ve istekleriniz bir kuyruğa gönderiliyor olabilir. Bunu, Azure [izleyici 'de Azure depolama ölçümlerini](../common/storage-metrics-in-azure-monitor.md)kullanarak doğrulayabilirsiniz.

### <a name="solution-for-cause-2"></a>Neden 2 için çözüm

Uygulamanızın [Azure dosya ölçek hedefleri](storage-files-scale-targets.md#azure-files-scale-targets)içinde olduğundan emin olun.

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Windows 'dan Linux 'a dosya kopyalama sırasında zaman damgaları kayboldu

Linux/UNIX platformlarında, farklı kullanıcılar dosya 1 ' i ve dosya 2 ' yi kullanıyorsa, **CP-p** komutu başarısız olur.

### <a name="cause"></a>Nedeni

COPYFILE içindeki **zorla** bayrağı, UNIX üzerinde **CP-p-f** ' y i çalıştırmaya neden olur. Bu komut, sahip olmadığınız dosyanın zaman damgasını da koruyamaz.

### <a name="workaround"></a>Geçici çözüm

Dosyaları kopyalamak için depolama hesabı kullanıcısını kullanın:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: '&lt;Path&gt;' öğesine erişilemiyor: giriş/çıkış hatası

Bir Azure dosya paylaşımındaki dosyaları ls komutunu kullanarak listelemeyi denediğinizde, komut dosyaları listelenirken askıda kalır. Şu hatayı alırsınız:

**ls: '&lt;Path&gt;' öğesine erişilemiyor: giriş/çıkış hatası**


### <a name="solution"></a>Çözüm
Linux çekirdeğini bu sorun için bir düzeltmesine sahip olan aşağıdaki sürümlere yükseltin:

- 4.4.87+
- 4.9.48 +
- 4.12.11+
- 4,13 ' den büyük veya buna eşit olan tüm sürümler

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Sembolik bağlantılar oluşturulamıyor-ln: sembolik bağlantı oluşturulamadı 't ': Işlem desteklenmiyor

### <a name="cause"></a>Nedeni
Varsayılan olarak, CIFS kullanarak Azure dosya paylaşımlarını Linux 'a bağlamak sembolik bağlantılar (symlinks) için desteği etkinleştirmez. Şöyle bir hata görürsünüz:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Çözüm
Linux CIFS istemcisi, SMB 2 veya 3 protokolü üzerinden Windows stili sembolik bağlantıların oluşturulmasını desteklemez. Şu anda Linux istemcisi, oluşturma ve izleme işlemleri için [mini + Fransız çözümlemeyin](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) adlı başka bir sembolik bağlantı stilini destekler. Sembolik bağlantılara ihtiyacı olan müşteriler "mfsymlinks" bağlama seçeneğini kullanabilir. Mac 'in kullandığı biçim olduğundan "mfsymlinks" önerilir.

Symlinks 'i kullanmak için, CIFS Mount komutunuz sonuna aşağıdakini ekleyin:

```
,mfsymlinks
```

Bu nedenle komut aşağıdaki gibi görünür:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

Daha sonra [wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers)'de önerildiği şekilde çözümlemeyin oluşturabilirsiniz.

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Bağlama hatası (112): bir yeniden bağlantı zaman aşımı nedeniyle ana bilgisayar çalışmıyor

Linux istemcisi uzun süre boşta kaldığında istemcide "112" bağlama hatası oluşur. Uzun bir boşta kalma süresinden sonra istemcinin bağlantısı kesilir ve bağlantı zaman aşımına uğrar.  

### <a name="cause"></a>Nedeni

Bağlantı aşağıdaki nedenlerle boşta kalabilir:

-   Varsayılan "yazılım yoluyla" bağlama seçeneği kullanıldığında sunucuyla yeniden TCP bağlantısı kurulmasını engelleyen ağ iletişim hataları
-   Eski çekirdeklerde bulunmayan son yeniden bağlantı düzeltmeleri

### <a name="solution"></a>Çözüm

Linux çekirdeğindeki bu yeniden bağlantı sorunu artık aşağıdaki değişikliklerin bir parçası olarak düzeltildi:

- [Yuva yeniden bağlantısından sonra smb3 oturumunun yeniden bağlantısını geciktirmemek için yeniden bağlantı düzeltildi](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [Yuva yeniden bağlantısından hemen sonra yankı hizmeti çağrısı](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS: yeniden bağlanma sırasında olası bir bellek bozulmasını düzeltir](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS: yeniden bağlantı sırasında karşılıklı mutex 'in olası bir çift kilitlemeyi düzeltir (çekirdek v 4.9 ve üzeri için)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

Öte yandan bu değişiklikler henüz tüm Linux dağıtımlarına taşınmamış olabilir. Popüler bir Linux dağıtımı kullanıyorsanız, hangi dağıtımlarınızın hangi sürümünün gerekli çekirdek değişikliklerine sahip olduğunu görmek için [Azure dosyalarını Linux Ile kullanma](storage-how-to-use-files-linux.md) bölümüne bakabilirsiniz.

### <a name="workaround"></a>Geçici çözüm

Bu soruna geçici bir çözüm olarak donanım yoluyla bağlamayı belirtebilirsiniz. Donanım yoluyla bağlama istemciyi bağlantı kurulana kadar veya açıkça kesilene kadar beklemeye zorlar. Ağ zaman aşımlarından kaynaklanan hataları önlemek için bunu kullanabilirsiniz. Öte yandan bu geçici çözüm belirsiz bekleme sürelerine neden olabilir. Gerektiğinde bağlantıları durdurmaya hazırlıklı olun.

En son çekirdek sürümlerine yükseltemiyorsanız Azure dosya paylaşımında bir dosya tutup her 30 saniyede bir (veya daha sık) bu dosyaya yazarak bu soruna geçici bir çözüm getirebilirsiniz. Bunun, dosyaya oluşturma veya değiştirme tarihini yeniden yazma gibi bir yazma işlemi olması gerekir. Aksi takdirde önbelleğe alınan sonuçlar elde edebilirsiniz ve işleminiz yeniden bağlantıyı tetiklemeyebilir.

## <a name="need-help-contact-support"></a>Yardım mı gerekiyor? Desteğe başvurun.

Hala yardıma ihtiyacınız varsa, sorununuzun hızla çözülmesini sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .
