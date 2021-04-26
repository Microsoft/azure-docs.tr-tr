---
title: Azure Linux VM aracısına genel bakış
description: Linux aracısının (waagent) nasıl yükleneceğini ve yapılandırılacağını, sanal makinenizin Azure Fabric denetleyicisiyle etkileşimini yönetme hakkında bilgi edinin.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: linux
ms.date: 10/17/2016
ms.openlocfilehash: e8851ddd5211536394614727d990a2b52d32bfcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565386"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Azure Linux aracısını anlama ve kullanma

Microsoft Azure Linux Aracısı (waagent) Linux & FreeBSD sağlamasını ve Azure yapı denetleyicisi ile VM etkileşimini yönetir. Azure, sağlama işlevselliği sağlayan Linux aracısına ek olarak, bazı Linux Işletim sistemleri için Cloud-init kullanma seçeneğini de sağlar. Linux Aracısı, Linux ve FreeBSD IaaS dağıtımları için aşağıdaki işlevleri sağlar:

> [!NOTE]
> Daha fazla bilgi için [Benioku dosyasına](https://github.com/Azure/WALinuxAgent/blob/master/README.md)bakın.
> 
> 

* **Görüntü sağlama**
  
  * Kullanıcı hesabı oluşturma
  * SSH kimlik doğrulama türlerini yapılandırma
  * SSH ortak anahtarlarının ve anahtar çiftlerinin dağıtımı
  * Ana bilgisayar adı ayarlanıyor
  * Ana bilgisayar adı Platform DNS 'ye yayımlanıyor
  * Platforma SSH ana bilgisayar anahtarı parmak izini bildirme
  * Kaynak disk yönetimi
  * Kaynak diski biçimlendirme ve bağlama
  * Değiştirme alanını yapılandırma
* **Ağ**
  
  * Platform DHCP sunucularıyla uyumluluğu geliştirmek için yolları yönetir
  * Ağ arabirimi adının kararlılığını sağlar
* **Çekirdek**
  
  * Sanal NUMA 'yı yapılandırır (çekirdek <için devre dışı bırak `2.6.37` )
  * /Dev/random için Hyper-V entropi kullanır
  * Kök cihaz (uzak olabilir) için SCSI zaman aşımlarını yapılandırır
* **Tanılama**
  
  * Konsol yeniden yönlendirme seri bağlantı noktası
* **SCVMM dağıtımları**
  
  * System Center Virtual Machine Manager 2012 R2 ortamında çalışırken Linux için VMM aracısını algılar ve önyükleme
* **VM Uzantısı**
  
  * Yazılım ve yapılandırma Otomasyonu 'nu etkinleştirmek için Microsoft ve Iş ortakları tarafından Linux VM 'ye (IaaS) yazılan bileşen ekleme
  * VM uzantısı başvuru uygulama açık [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>İletişim
Platformdan aracıya bilgi akışı iki kanal aracılığıyla gerçekleşir:

* IaaS dağıtımları için önyükleme zamanına bağlı bir DVD. Bu DVD, gerçek SSH keypairs dışında tüm sağlama bilgilerini içeren bir OVF uyumlu yapılandırma dosyası içerir.
* Dağıtım ve topoloji yapılandırmasını elde etmek için kullanılan bir REST API ortaya çıkaran bir TCP uç noktası.

## <a name="requirements"></a>Gereksinimler
Aşağıdaki sistemler test edilmiştir ve Azure Linux aracısıyla birlikte çalışmak üzere bilinmektedir:

> [!NOTE]
> Bu liste, [desteklenen detros](../linux/endorsed-distros.md)'nin resmi listesinden farklı olabilir.
> 
> 

* CoreOS
* CentOS 6.3 +
* Red Hat Enterprise Linux 6.7 +
* Debir 7.0 +
* Ubuntu 12.04 +
* openSUSE 12.3 +
* SLES 11 SP3 +
* Oracle Linux 6.4 +

Desteklenen diğer sistemler:

* FreeBSD 10 + (Azure Linux Aracısı v 2.0.10 +)

Linux Aracısı düzgün çalışması için bazı sistem paketlerine bağımlıdır:

* Python 2.6+
* OpenSSL 1.0+
* OpenSSH 5.3+
* Dosya sistemi yardımcı programları: sfdisk, Fdisk, mkfs, ayrıştırmuş
* Parola araçları: chpasswd, sudo
* Metin işleme araçları: SED, grep
* Ağ araçları: IP-yol
* UDF FILESYSTEMS 'ı bağlamak için çekirdek desteği.

VM 'nizin 168.63.129.16 IP adresine erişimi olduğundan emin olun. Daha fazla bilgi için bkz. [IP adresi 168.63.129.16 nedir](../../virtual-network/what-is-ip-address-168-63-129-16.md).


## <a name="installation"></a>Yükleme
Dağıtım paketi deposundaki bir RPM veya bir DEB paketini kullanarak yükleme, Azure Linux aracısını yükleme ve yükseltme için tercih edilen yöntemdir. Tüm [onaylı dağıtım sağlayıcıları](../linux/endorsed-distros.md) , Azure Linux Aracısı paketini görüntülerle ve depolarında tümleştirin.

Kaynaktan veya özel konumlara veya öneklere yükleme gibi gelişmiş yükleme seçenekleri için [GitHub 'Daki Azure Linux Aracısı](https://github.com/Azure/WALinuxAgent) depolarındaki belgelere bakın.

## <a name="command-line-options"></a>Command-Line seçenekleri
### <a name="flags"></a>Bayraklar
* verbose: belirtilen komutun ayrıntı düzeyini artır
* zorla: Bazı komutların etkileşimli onayını atlayın

### <a name="commands"></a>Komutlar
* Yardım: desteklenen komutları ve bayrakları listeler.
* sağlamayı kaldırma: sistemi temizlemeyi deneyin ve yeniden sağlama için uygun hale getirin. Aşağıdaki işlem şunları siler:
  
  * Tüm SSH ana bilgisayar anahtarları (sağlama. RegenerateSshHostKeyPair, yapılandırma dosyasında ' y ' ise)
  * /Etc/resolv.exe içindeki nameserver yapılandırması
  * /Etc/shadow 'tan kök parola (sağlama. DeleteRootPassword, yapılandırma dosyasında ' y ' ise)
  * Önbelleğe alınmış DHCP istemci kiraları
  * Ana bilgisayar adını localhost. localdomain olarak sıfırlar

> [!WARNING]
> Sağlamayı kaldırma, görüntünün tüm hassas bilgilerin temizlenmesini ve yeniden dağıtım için uygun olduğunu garanti etmez.
> 
> 

* sağlamayı kaldırma + Kullanıcı: her şeyi dedeme (yukarıdaki) gerçekleştirir ve ayrıca, sağlanan son kullanıcı hesabını (/var/lib/waagent 'tan alınan) ve ilişkili verileri de siler. Bu parametre, önceden Azure üzerinde daha önce temin edilen bir görüntünün sağlanması ve yeniden kullanılması olabilir.
* Sürüm: waagent sürümünü görüntüler
* serialconsole: ttyS0 (ilk seri bağlantı noktası) önyükleme konsolu olarak işaretlemesi için GRUB 'yi yapılandırır. Bu, çekirdek önyükleme günlüklerinin seri bağlantı noktasına gönderilmesini ve hata ayıklama için kullanılabilir hale gelmesini sağlar.
* Daemon: platformla etkileşimi yönetmek için waagent 'ı bir daemon olarak çalıştırın. Bu bağımsız değişken waagent Init betiğine waagent için belirtilir.
* Başlat: waagent 'ı arka plan işlemi olarak çalıştır

## <a name="configuration"></a>Yapılandırma
Yapılandırma dosyası (/etc/waagent.exe) waagent 'un eylemlerini denetler. Aşağıda örnek bir yapılandırma dosyası gösterilmektedir:

```config
Provisioning.Enabled=y
Provisioning.DeleteRootPassword=n
Provisioning.RegenerateSshHostKeyPair=y
Provisioning.SshHostKeyPairType=rsa
Provisioning.MonitorHostName=y
Provisioning.DecodeCustomData=n
Provisioning.ExecuteCustomData=n
Provisioning.AllowResetSysUser=n
Provisioning.PasswordCryptId=6
Provisioning.PasswordCryptSaltLength=10
ResourceDisk.Format=y
ResourceDisk.Filesystem=ext4
ResourceDisk.MountPoint=/mnt/resource
ResourceDisk.MountOptions=None
ResourceDisk.EnableSwap=n
ResourceDisk.SwapSizeMB=0
LBProbeResponder=y
Logs.Verbose=n
OS.RootDeviceScsiTimeout=300
OS.OpensslPath=None
HttpProxy.Host=None
HttpProxy.Port=None
AutoUpdate.Enabled=y
```

Aşağıdaki çeşitli yapılandırma seçenekleri açıklanmaktadır. Yapılandırma seçenekleri üç türtür; Boolean, dize veya tamsayı. Boole yapılandırma seçenekleri "y" veya "n" olarak belirtilebilir. "None" özel anahtar sözcüğü, bazı dize türü yapılandırma girdileri için aşağıdaki Ayrıntılar olarak kullanılabilir:

**Sağlama. etkin:**  
```txt
Type: Boolean  
Default: y
```
Bu, kullanıcının aracıdaki sağlama işlevini etkinleştirmesine veya devre dışı bırakmasına olanak sağlar. Geçerli değerler şunlardır "y" veya "n". Sağlama devre dışıysa, görüntüdeki SSH ana bilgisayarı ve Kullanıcı anahtarları korunur ve Azure sağlama API 'sinde belirtilen yapılandırma yok sayılır.

> [!NOTE]
> `Provisioning.Enabled`Parametresi, sağlama için Cloud-init kullanan Ubuntu bulut görüntülerinde varsayılan olarak "n" değerine sahiptir.
> 
> 

**Sağlama. DeleteRootPassword:**  
```txt
Type: Boolean  
Default: n
```
Ayarlanırsa,/etc/shadow dosyasındaki kök parola sağlama işlemi sırasında silinir.

**Sağlama. RegenerateSshHostKeyPair:**  
```txt
Type: Boolean  
Default: y
```
Ayarlanırsa, tüm SSH ana bilgisayar anahtar çiftleri (ECDSA, dsa ve RSA),/etc/ssh/kaynağından sağlama işlemi sırasında silinir. Ve tek bir yeni anahtar çifti oluşturulur.

Yeni anahtar çiftinin şifreleme türü, sağlama. SshHostKeyPairType girişi tarafından yapılandırılabilir. Bazı dağıtımlar, SSH arka plan programı yeniden başlatıldığında (örneğin, yeniden başlatma sırasında) eksik şifreleme türleri için SSH anahtar çiftlerini yeniden oluşturur.

**Sağlama. SshHostKeyPairType:**  
```txt
Type: String  
Default: rsa
```
Bu, sanal makinede SSH Daemon tarafından desteklenen bir şifreleme algoritması türüne ayarlanabilir. Genellikle desteklenen değerler "RSA", "dsa" ve "ECDSA" dir. Windows üzerinde "putty.exe", "ECDSA" desteklemez. Bu nedenle, bir Linux dağıtımına bağlanmak için Windows üzerinde putty.exe kullanmayı planlıyorsanız, "RSA" veya "dsa" kullanın.

**Sağlama. MonitorHostName:**  
```txt
Type: Boolean  
Default: y
```
Ayarlanırsa, waagent Linux sanal makinesini ana bilgisayar adı değişiklikleri için izler ("hostname" komutu tarafından döndürülen) ve değişikliği yansıtmak için görüntüdeki ağ yapılandırmasını otomatik olarak güncelleştirir. Ad değişikliğini DNS sunucularına göndermek için sanal makinede ağ yeniden başlatılır. Bu, Internet bağlantısının kısa bir kaybına neden olur.

**. DecodeCustomData sağlama**  
```txt
Type: Boolean  
Default: n
```
Ayarlanırsa, waagent CustomData 'ın Base64 'ten kodunu çözer.

**CuteCustomDataProvisioning.Exe**  
```txt
Type: Boolean  
Default: n
```
Ayarlanırsa, waagent sağlamaktan sonra CustomData öğesini yürütür.

**Sağlama. AllowResetSysUser**
```txt
Type: Boolean
Default: n
```
Bu seçenek, sys kullanıcısının parolasının sıfırlanmasına izin verir; Varsayılan değer devre dışıdır.

**Sağlama. Passwordcryptıd**  
```txt
Type: String  
Default: 6
```
Parola karması oluşturulurken Crypt tarafından kullanılan algoritma.  
 1-MD5  
 2A-Blowbalık  
 5-SHA-256  
 6-SHA-512  

**Sağlama. PasswordCryptSaltLength**  
```txt
Type: String  
Default: 10
```
Parola karması oluşturulurken kullanılan rastgele anahtar uzunluğu.

**ResourceDisk. Format:**  
```txt
Type: Boolean  
Default: y
```
Ayarlanırsa, platform tarafından sunulan kaynak disk, "ResourceDisk. FileSystem" içinde Kullanıcı tarafından istenen dosya sistemi türü "NTFS" dışında herhangi bir şeydir waagent tarafından biçimlendirilir ve bağlanır. Diskte Linux (83) türünde tek bir bölüm kullanılabilir hale getirilir. Bu bölüm, başarıyla bağlanlanabilir ise biçimlendirilmedi.

**ResourceDisk. FileSystem:**  
```txt
Type: String  
Default: ext4
```
Bu, kaynak diskinin dosya sistemi türünü belirtir. Desteklenen değerler Linux dağıtımına göre farklılık gösterir. Dize X ise, mkfs. X, Linux görüntüsünde bulunmalıdır. SLES 11 görüntüleri genellikle ' ext3 ' kullanmalıdır. FreeBSD görüntülerinin burada ' UFS2 ' kullanması gerekir.

**ResourceDisk. Bağlamanoktası:**  
```txt
Type: String  
Default: /mnt/resource 
```
Bu, kaynak diskinin bağlı olduğu yolu belirtir. Kaynak disk *geçici* bir DISKTIR ve VM 'nin sağlaması tamamlandığında boşaltılır.

**ResourceDisk. MountOptions**  
```txt
Type: String  
Default: None
```
Mount-o komutuna geçirilecek disk bağlama seçeneklerini belirtir. Bu, EX değerlerinin virgülle ayrılmış listesidir. ' nodev, nosuıd '. Ayrıntılar için bağlama (8) bölümüne bakın.

**ResourceDisk. EnableSwap:**  
```txt
Type: Boolean  
Default: n
```
Ayarlanırsa, kaynak diskte bir takas dosyası (/Swapfile) oluşturulur ve sistem takas alanına eklenir.

**ResourceDisk. SwapSizeMB:**  
```txt
Type: Integer  
Default: 0
```
Takas dosyasının megabayt cinsinden boyutu.

**Günlükler. verbose:**  
```txt
Type: Boolean  
Default: n
```
Ayarlanırsa, günlük ayrıntı düzeyi artırıldığı. Waagent günlüğü/var/log/waagent.log olarak kaydedilir ve günlükleri döndürmek için sistem logrotate işlevini kullanır.

**Atayamadı. EnableRDMA**  
```txt
Type: Boolean  
Default: n
```
Ayarlanırsa, aracı temel donanımda üretici yazılımının sürümüyle eşleşen bir RDMA çekirdek sürücüsü yüklemeye çalışır ve sonra yükler.

**Atayamadı. Rootdevicescsıtimeout:**  
```txt
Type: Integer  
Default: 300
```
Bu ayar, işletim sistemi diskinde ve veri sürücülerinde SCSI zaman aşımını saniye cinsinden yapılandırır. Ayarlanmamışsa, sistem Varsayılanları kullanılır.

**Atayamadı. OpensslPath:**  
```txt
Type: String  
Default: None
```
Bu ayar, şifreli işlemler için kullanılacak OpenSSL ikilisinin alternatif yolunu belirtmek için kullanılabilir.

**HttpProxy. Host, HttpProxy. Port**  
```txt
Type: String  
Default: None
```
Ayarlanırsa, aracı internet 'e erişmek için bu proxy sunucusunu kullanır. 

**Otomatik güncelleştirme. etkin**
```txt
Type: Boolean
Default: y
```
Hedef durum işleme için otomatik güncelleştirmeyi etkinleştirin veya devre dışı bırakın; Varsayılan değer etkindir.



## <a name="ubuntu-cloud-images"></a>Ubuntu bulut görüntüleri
Ubuntu bulut görüntüleri, aksi takdirde Azure Linux Aracısı tarafından yönetilebilecek birçok yapılandırma görevini gerçekleştirmek için [Cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) ' i kullanır. Aşağıdaki farklar geçerlidir:

* **Sağlama. Enabled** , sağlama görevlerini gerçekleştirmek için Cloud-Init kullanan Ubuntu bulut görüntülerinde varsayılan olarak "n" değerine sahiptir.
* Aşağıdaki yapılandırma parametrelerinin, kaynak diski yönetmek için Cloud-init kullanan Ubuntu bulut görüntüleri üzerinde hiçbir etkisi yoktur ve alanı takas edin:
  
  * **ResourceDisk. Format**
  * **ResourceDisk. FileSystem**
  * **ResourceDisk. Bağlamanoktası**
  * **ResourceDisk. EnableSwap**
  * **ResourceDisk. SwapSizeMB**

* Daha fazla bilgi için, kaynak diski bağlama noktasını yapılandırmak ve sağlama sırasında Ubuntu bulut görüntülerinde alanı değiştirmek için aşağıdaki kaynaklara bakın:
  
  * [Ubuntu wiki: takas bölümlerini yapılandırma](https://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Özel verileri bir Azure sanal makinesine ekleme](../windows/tutorial-automate-vm-deployment.md)