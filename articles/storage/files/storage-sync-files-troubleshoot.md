---
title: Sorun giderme Azure Dosya Eşitleme | Microsoft Docs
description: Azure Dosya Eşitleme karşılaşılan yaygın sorunları giderin.
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 31a9eda0e17083aac25be071c1d1a3ab84049e39
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274887"
---
# <a name="troubleshoot-azure-file-sync"></a>Azure Dosya Eşitleme ile ilgili sorunları giderme
Şirket içi bir dosya sunucusunun esnekliğini, performansını ve uyumluluğunu koruyarak kuruluşunuzun dosya paylaşımlarını Azure dosyalarında merkezileştirmek için Azure Dosya Eşitleme kullanın. Azure Dosya Eşitleme, Windows Server’ı Azure dosya paylaşımınızın hızlı bir önbelleğine dönüştürür. SMB, NFS ve FTPS dahil olmak üzere verilerinize yerel olarak erişmek için Windows Server 'da bulunan herhangi bir protokolü kullanabilirsiniz. Dünyanın dört bir yanında ihtiyacınız olan sayıda önbellekler olabilir.

Bu makale, Azure Dosya Eşitleme dağıtımınızda karşılaşabileceğiniz sorunları gidermenize ve çözmenize yardımcı olmak için tasarlanmıştır. Ayrıca, sorunla daha derin bir şekilde araştırılması durumunda sistemden önemli günlüklerin nasıl toplanacağını da anlatılmaktadır. Sorunuzun yanıtını görmüyorsanız, aşağıdaki kanallarla (yürüyen sırada) bizimle iletişim kurmanız gerekir:

1. [Azure depolama Forumu](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
2. [Azure dosyaları UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. Microsoft Desteği. Yeni bir destek isteği oluşturmak için, Azure portal **Yardım** sekmesinde **Yardım + Destek** düğmesini seçin ve ardından **Yeni destek isteği**' ni seçin.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Sunucum üzerinde Azure Dosya Eşitleme bir sorun yaşıyorum (eşitleme, bulut katmanlama vb.). Sunucu uç noktasını kaldırıp yeniden oluşturmanız gerekir mi?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Aracı yüklemesi ve sunucu kaydı
<a id="agent-installation-failures"></a>**Aracı yükleme hatalarında sorun giderme**  
Azure Dosya Eşitleme Aracısı yüklemesi başarısız olursa, yükseltilmiş bir komut isteminde, aracı yüklemesi sırasında günlüğü açmak için aşağıdaki komutu çalıştırın:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Yükleme hatasının nedenini öğrenmek için Installer. log dosyasına bakın.

<a id="agent-installation-on-DC"></a>**Active Directory Etki Alanı denetleyicisinde aracı yüklemesi başarısız oluyor**  
Bir Active Directory etki alanı denetleyicisine, PDC rolü sahibinin bir Windows Server 2008 R2 veya altında işletim sistemi sürümü olan bir etki alanı denetleyicisine yüklemeye çalışırsanız, eşitleme aracısının yüklenememe sorununa ulaşırsınız.

Bu sorunu gidermek için, PDC rolünü Windows Server 2012 R2 veya daha yeni bir sürümünü çalıştıran başka bir etki alanı denetleyicisine aktarın ve ardından eşitleme 'yi çalıştırın.

<a id="server-registration-prerequisites"></a>**Sunucu kaydı şu iletiyi görüntüler: "önkoşulların önkoşulları eksik"**

Bu ileti, PowerShell 5,1 ' de az veya Azurerd PowerShell modülü yüklü değilse görüntülenir. 

> [!Note]  
> ServerRegistration. exe PowerShell 6. x desteklemez. Sunucuyu kaydetmek için PowerShell 6. x üzerinde Register-AzStorageSyncServer cmdlet 'ini kullanabilirsiniz.

PowerShell 5,1 ' ye az veya Azurerd modülünü yüklemek için aşağıdaki adımları uygulayın:

1. Yükseltilmiş bir komut isteminden **PowerShell** yazın ve ENTER tuşuna basın.
2. Belgeleri izleyerek en son az veya Azurerd modülünü yükler:
    - [Az Module (.NET 4.7.2 gerektirir)](https://go.microsoft.com/fwlink/?linkid=2062890)
    - [Azurerd modülü]( https://go.microsoft.com/fwlink/?linkid=856959)
3. ServerRegistration. exe ' yi çalıştırın ve sunucuyu bir depolama Eşitleme hizmetine kaydetmek için Sihirbazı doldurun.

<a id="server-already-registered"></a>**Sunucu kaydı şu iletiyi görüntüler: "Bu sunucu zaten kayıtlı"** 

!["Sunucu zaten kayıtlı" hata iletisiyle sunucu kayıt iletişim kutusunun ekran görüntüsü](media/storage-sync-files-troubleshoot/server-registration-1.png)

Sunucu daha önce bir depolama eşitleme hizmeti ile kaydedilmişse bu ileti görüntülenir. Sunucunun geçerli depolama eşitleme hizmetinden kaydını silip yeni bir depolama eşitleme hizmeti ile kaydolacak şekilde, [Azure dosya eşitleme ile sunucu kaydını silme](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)bölümünde açıklanan adımları uygulayın.

Sunucu, depolama eşitleme hizmeti 'nde **kayıtlı sunucular** altında listelenmiyorsa, kaydını kaldırmak istediğiniz sunucuda aşağıdaki PowerShell komutlarını çalıştırın:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Sunucu bir kümenin parçasıysa, küme kaydını da kaldırmak için isteğe bağlı *sıfırlama-StorageSyncServer-CleanClusterRegistration* parametresini kullanabilirsiniz.

<a id="web-site-not-trusted"></a>**Bir sunucuyu kaydettiğimde, çok sayıda "Web sitesi güvenilir değil" yanıtı görüyorum. Kaydol?**  
Bu sorun, sunucu kaydı sırasında **geliştirilmiş Internet Explorer güvenlik** ilkesi etkinleştirildiğinde oluşur. **Gelişmiş Internet Explorer güvenlik** ilkesini doğru şekilde devre dışı bırakma hakkında daha fazla bilgi için bkz. [Windows Server 'ı Azure dosya eşitleme Ile kullanmak üzere hazırlama](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) ve [Azure dosya eşitleme dağıtma](storage-sync-files-deployment-guide.md).

<a id="server-registration-missing"></a>**Sunucu, Azure portal kayıtlı sunucular altında listelenmiyor**  
Bir sunucu, bir depolama eşitleme hizmeti için **kayıtlı sunucular** altında listelenmiyorsa:
1. Kaydetmek istediğiniz sunucuda oturum açın.
2. Dosya Gezgini 'ni açın ve ardından depolama eşitleme Aracısı yükleme dizinine gidin (varsayılan konum C:\Program Files\Azure\StorageSyncAgent ' dir.) 
3. ServerRegistration. exe ' yi çalıştırın ve sunucuyu bir depolama Eşitleme hizmetine kaydetmek için Sihirbazı doldurun.

## <a name="sync-group-management"></a>Eşitleme grubu yönetimi
<a id="cloud-endpoint-using-share"></a>**Bulut uç noktası oluşturma işlemi şu hatayla başarısız oluyor: "belirtilen Azure FileShare farklı bir CloudEndpoint tarafından zaten kullanılıyor"**  
Azure dosya paylaşımının zaten başka bir bulut uç noktası tarafından kullanımda olması durumunda bu hata oluşur. 

Bu iletiyi görürseniz ve Azure dosya paylaşma Şu anda bir bulut uç noktası tarafından kullanımda değilse, Azure dosya paylaşımında Azure Dosya Eşitleme meta verileri temizlemek için aşağıdaki adımları izleyin:

> [!Warning]  
> Şu anda bir bulut uç noktası tarafından kullanılan bir Azure dosya paylaşımındaki meta verilerin silinmesi Azure Dosya Eşitleme işlemlerin başarısız olmasına neden olur. 

1. Azure portal Azure dosya paylaşımınıza gidin.  
2. Azure dosya paylaşımında sağ tıklayın ve ardından **meta verileri Düzenle**' yi seçin.
3. **SyncService**öğesine sağ tıklayın ve ardından **Sil**' i seçin.

<a id="cloud-endpoint-authfailed"></a>**Bulut uç noktası oluşturma işlemi şu hata ile başarısız oluyor: "AuthorizationFailed"**  
Kullanıcı hesabınız bir bulut uç noktası oluşturmak için yeterli haklara sahip değilse bu hata oluşur. 

Bir bulut uç noktası oluşturmak için, Kullanıcı hesabınızın aşağıdaki Microsoft yetkilendirme izinlerine sahip olması gerekir:  
* Okuma: rol tanımını al
* Yazma: özel rol tanımı oluştur veya güncelleştir
* Oku: rol atamasını al
* Yazma: rol ataması oluştur

Aşağıdaki yerleşik roller gerekli Microsoft yetkilendirme izinlerine sahiptir:  
* Sahip
* Kullanıcı Erişimi Yöneticisi

Kullanıcı hesabı rolünüzün gerekli izinlere sahip olup olmadığını belirleme:  
1. Azure portal **kaynak grupları**' nı seçin.
2. Depolama hesabının bulunduğu kaynak grubunu seçin ve ardından **erişim denetimi (IAM)** öğesini seçin.
3. **Rol atamaları** sekmesini seçin.
4. Kullanıcı hesabınız için **rolü** (örneğin, sahibi veya katkıda bulunan) seçin.
5. **Kaynak sağlayıcısı** listesinde, **Microsoft yetkilendirmesi**' ni seçin. 
    * **Rol ataması** **okuma** ve **yazma** izinlerine sahip olmalıdır.
    * **Rol tanımı** **okuma** ve **yazma** izinlerine sahip olmalıdır.

<a id="-2134375898"></a>**Sunucu uç noktası oluşturma işlemi şu hatayla başarısız oldu: "MgmtServerJobFailed" (hata kodu:-2134375898 veya 0x80c80226)**  
Bu hata, sunucu uç noktası yolu sistem birimi üzerinde ise ve bulut katmanlama etkinse oluşur. Bulut katmanlaması, sistem biriminde desteklenmez. Sistem biriminde bir sunucu uç noktası oluşturmak için sunucu uç noktası oluştururken bulut katmanlamayı devre dışı bırakın.

<a id="-2147024894"></a>**Sunucu uç noktası oluşturma işlemi şu hata ile başarısız oluyor: "MgmtServerJobFailed" (hata kodu:-2147024894 veya 0x80070002)**  
Belirtilen sunucu uç noktası yolu geçerli değilse bu hata oluşur. Belirtilen sunucu uç noktası yolunun yerel olarak bağlı bir NTFS birimi olduğunu doğrulayın. Not, Azure Dosya Eşitleme eşlenen sürücüleri sunucu uç noktası yolu olarak desteklemez.

<a id="-2134347507"></a>**Sunucu uç noktası oluşturma işlemi şu hatayla başarısız oldu: "MgmtServerJobFailed" (hata kodu:-2134347507 veya 0x80c8710d)**  
Bu hata, Azure Dosya Eşitleme sıkıştırılmış bir sistem birimi bilgi klasörü olan birimlerde sunucu uç noktalarını desteklemediğinden oluşur. Bu sorunu çözmek için, sistem birimi bilgileri klasörünü açın. Sistem birimi bilgileri klasörü birimde sıkıştırılmış tek klasördeyse, aşağıdaki adımları uygulayın:

1. [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) aracını indirin.
2. Sistem hesabı altında çalışan bir komut istemi başlatmak için yükseltilmiş bir komut isteminden aşağıdaki komutu çalıştırın: **PsExec. exe-i-s-d cmd**
3. Sistem hesabı altında çalışan komut isteminden aşağıdaki komutları yazın ve ENTER tuşuna basın:   
    **CD/d "sürücü harfi: \ sistem birimi bilgileri"**  
    **Compact/u/s**

<a id="-2134376345"></a>**Sunucu uç noktası oluşturma işlemi şu hatayla başarısız oldu: "MgmtServerJobFailed" (hata kodu:-2134376345 veya 0x80C80067)**  
Sunucu sınırına göre sunucu uç noktalarına ulaşıldığında bu hata oluşur. Azure Dosya Eşitleme Şu anda sunucu başına en fazla 30 sunucu uç noktasını desteklemektedir. Daha fazla bilgi için bkz. [Azure dosya eşitleme ölçek hedefleri](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-file-sync-scale-targets).

<a id="-2134376427"></a>**Sunucu uç noktası oluşturma işlemi şu hatayla başarısız oldu: "MgmtServerJobFailed" (hata kodu:-2134376427 veya 0x80c80015)**  
Bu hata, başka bir sunucu uç noktası zaten belirtilen sunucu uç noktası yolunu eşitbir durumda ise oluşur. Azure Dosya Eşitleme, aynı dizini veya birimi eşitlerken birden çok sunucu uç noktasını desteklemez.

<a id="-2134347757"></a>**Sunucu uç noktası silme işlemi şu hatayla başarısız oluyor: "Mgmtserverjobendpoint" (hata kodu:-2134347757 veya 0x80c87013)**  
Sunucu çevrimdışıysa veya ağ bağlantısı yoksa bu hata oluşur. Sunucu artık kullanılabilir durumda değilse, portaldaki sunucu kaydını silerek sunucu uç noktalarını silecektir. Sunucu uç noktalarını silmek için [Azure dosya eşitleme ile sunucu kaydını](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)silme bölümünde açıklanan adımları izleyin.

<a id="server-endpoint-provisioningfailed"></a>**Sunucu uç noktası özellikleri sayfası açılamıyor veya bulut katmanlama ilkesini güncelleştirme**  
Bu sorun, sunucu uç noktasındaki bir yönetim işlemi başarısız olursa ortaya çıkabilir. Sunucu uç noktası özellikleri sayfası Azure portal açılmadığından, sunucu uç noktasının sunucudan PowerShell komutları kullanılarak güncelleştirilmesi bu sorunu çözebilir. 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**Sunucu uç noktası "etkinlik yok" veya "beklemede" sistem durumuna sahip ve kayıtlı sunucular dikey penceresindeki sunucu durumu "çevrimdışı görünüyor"**  

Bu sorun, depolama eşitleme Izleyicisi işlemi çalışmıyorsa veya sunucu, proxy veya güvenlik duvarı nedeniyle Azure Dosya Eşitleme hizmetiyle iletişim kuramadıysanız oluşabilir.

Bu sorunu çözmek için aşağıdaki adımları gerçekleştirin:

1. Sunucuda Görev Yöneticisi'ni açın ve Depolama Eşitleme İzleyicisi (AzureStorageSyncMonitor.exe) işleminin çalıştığından emin olun. İşlem çalışmıyorsa önce sunucuyu yeniden başlatmayı deneyin. Sunucunun yeniden başlatılması sorunu çözmezse en son Azure Dosya Eşitleme [aracısı sürümüne](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes) yükseltin.
2. Güvenlik duvarının ve proxy ayarlarının doğru yapılandırıldığını doğrulayın:
    - Sunucu bir güvenlik duvarının arkasındaysa 443 numaralı bağlantı noktası üzerinden giden bağlantılara izin verildiğinden emin olun. Güvenlik Duvarı trafiği belirli etki alanlarıyla kısıtlarsa, güvenlik duvarı [belgelerinde](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall) listelenen etki alanlarının erişilebilir olduğunu doğrulayın.
    - Sunucu bir proxy 'nin arkasındaysa, proxy [belgelerindeki](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy)adımları izleyerek makine genelinde veya uygulamaya özel proxy ayarlarını yapılandırın.

<a id="endpoint-noactivity-sync"></a>**Sunucu uç noktası "etkinlik yok" durumuna sahip ve kayıtlı sunucular dikey penceresindeki sunucu durumu "çevrimiçi"**  

"Etkinlik yok" sunucu uç noktası sistem durumu, sunucu uç noktasının, son iki saat içinde eşitleme etkinliğini günlüğe almadığı anlamına gelir.

Sunucu uç noktası eşitleme etkinliğini aşağıdaki nedenlerle günlüğe içermeyebilir:

- Aracı sürümü 4.3.0.0 veya üzeri yüklü ve sunucuda etkin bir VSS eşitleme oturumu (SnapshotSync) vardır. Bir VSS eşitleme oturumu bir sunucu uç noktası için etkin olduğunda, aynı birimdeki diğer sunucu uç noktaları VSS eşitleme oturumu tamamlanana kadar bir başlangıç eşitleme oturumu başlatamaz. Bu sorunu çözmek için, bir VSS eşitleme oturumu etkinken bir birimde birden çok sunucu uç noktası eşitlemesini destekleyen aracı sürümü 5.0.2.0 veya daha yeni bir sürümünü yüklemelisiniz.

    Bir sunucudaki geçerli eşitleme etkinliğini denetlemek için, [geçerli bir eşitleme oturumunun ilerlemesini nasıl yaparım? izleme](#how-do-i-monitor-the-progress-of-a-current-sync-session)bölümüne bakın.

- Sunucu, eşzamanlı eşitleme oturumu sayısı üst sınırına ulaştı. 
    - Aracı sürümü 4. x ve üzeri: sınır kullanılabilir sistem kaynaklarına göre farklılık gösterir.
    - Aracı sürüm 3. x: işlemci başına 2 etkin eşitleme oturumu veya sunucu başına en fazla 8 etkin eşitleme oturumu.

> [!Note]  
> Kayıtlı sunucular dikey penceresindeki sunucu durumu "çevrimdışı" ise "sunucu uç noktasında belgelenen adımları gerçekleştirmek için [" etkinlik yok "veya" beklemede "sistem durumu ve kayıtlı sunucular dikey penceresinde sunucu durumu" çevrimdışı görünüyor "](#server-endpoint-noactivity) bölümünde verilmiştir .

## <a name="sync"></a>Eşitleme
<a id="afs-change-detection"></a>**SMB veya Portal üzerinden doğrudan Azure dosya paylaşımım içinde bir dosya oluşturdum, dosyanın eşitleme grubundaki sunucularla eşitlenmesi ne kadar sürer?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**Sunucu uç noktası sistem durumu, birkaç saat boyunca bekleme durumunda**  
Bu sorun, bir bulut uç noktası oluşturup veri içeren bir Azure dosya paylaşımının kullanılması halinde beklenmektedir. Azure dosya paylaşımındaki değişiklikleri tarayan değişiklik numaralandırma işi, dosyaların bulut ve sunucu uç noktaları arasında eşitlenebilmesi için tamamlanmalıdır. İşi tamamlanma süresi, Azure dosya paylaşımındaki ad alanının boyutuna bağlıdır. Değişiklik numaralandırması işi tamamlandıktan sonra sunucu uç noktası durumunun güncelleştirilmesi gerekir.

### <a id="broken-sync"></a>Nasıl yaparım? eşitleme durumu İzmi?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
Her bir eşitleme grubunda, son tamamlanan eşitleme oturumlarının durumunu görmek için bireysel sunucu uç noktalarında ayrıntıya gidebilirsiniz. Yeşil bir sistem durumu sütunu ve 0 değerini eşitlemeden bir dosya eşitlemenin beklendiği gibi çalıştığını gösterir. Böyle bir durum söz konusu değilse, yaygın eşitleme hatalarının listesi ve eşitlenmemiş dosyaların nasıl işleneceği hakkında bilgi için aşağıya bakın. 

![Azure portal ekran görüntüsü](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="servertabserver"></a>[Sunucu](#tab/server)
@No__t-0 ' da Olay Görüntüleyicisi bulunan sunucunun telemetri günlüklerine gidin. Olay 9102, tamamlanan bir eşitleme oturumuna karşılık geliyor; eşitlemenin en son durumu için 9102 KIMLIKLI en son olayı arayın. SyncDirection, bu oturumun karşıya yükleme veya indirme olup olmadığını söyler. HResult 0 ise, eşitleme oturumu başarılı olmuştur. Sıfır olmayan bir HResult, eşitleme sırasında bir hata olduğu anlamına gelir; yaygın hataların listesi için aşağıya bakın. PerItemErrorCount değeri 0 ' dan büyükse bu, bazı dosya veya klasörlerin düzgün şekilde eşitlenmediği anlamına gelir. 0 ' dan büyük olan bir PerItemErrorCount değeri 0 ' dan büyük bir HResult olması mümkündür.

Aşağıda başarılı bir karşıya yükleme örneği verilmiştir. Kısaltma için, her 9102 olayında yer alan değerlerden yalnızca bazıları aşağıda listelenmiştir. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Buna karşılık, başarısız bir karşıya yükleme şöyle görünebilir:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Bazen eşitleme oturumları genel başarısız olur ya da sıfır olmayan bir PerItemErrorCount değeri, ancak yine de devam ediyor, ancak bazı dosyalar başarıyla eşitleniyor. Bu, oturumun ne kadarının başarılı olduğunu söyleyen uygulanan * Fields (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount ve AppliedSizeBytes) içinde görülebilir. Hatalı bir satırda birden çok eşitleme oturumu görürseniz, ancak artan uygulanmış * sayımla karşılaşırsanız, bir destek bileti açmadan önce yeniden denemek için eşitleme süresi vermeniz gerekir.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Geçerli bir eşitleme oturumunun ilerleme durumunu izlemek Nasıl yaparım? mı?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
Eşitleme grubunuz içinde, söz konusu sunucu uç noktasına gidin ve geçerli eşitleme oturumunda karşıya yüklenen veya indirilen dosya sayısını görmek için eşitleme etkinliği bölümüne bakın. Bu durumun yaklaşık 5 dakika geciktiğine ve eşitleme oturumunuzun bu süre içinde tamamlanmak üzere yeterince küçük olduğunu unutmayın. Bu işlem portalda raporlanmayabilir. 

# <a name="servertabserver"></a>[Sunucu](#tab/server)
Sunucudaki telemetri günlüğünde en son 9302 olayına bakın (Olay Görüntüleyicisi, uygulamalar ve hizmetler Logs\microsoft\filesync\fik\telemetri) bölümüne gidin. Bu olay, geçerli eşitleme oturumunun durumunu gösterir. Totalıtemcount, kaç dosyanın eşitleneceğini gösterir, şimdiye kadar eşitlenen dosya sayısını AppliedItemCount ve PerItemErrorCount, eşitlenmediği dosyaların sayısını belirtir (Bu konuyla başa çıkmak için aşağıya bakın).

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Nasıl yaparım? sunucularım birbirleriyle eşitlenmiş mi olduğunu bilir mi?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
Belirli bir eşitleme grubundaki her bir sunucu için şunları yaptığınızdan emin olun:
- Hem karşıya yükleme hem de indirme için denenen son eşitlemeye ilişkin zaman damgaları son tarih.
- Durum hem karşıya yükleme hem de indirme için yeşil olur.
- Eşitleme etkinliği alanı, eşitlenmek üzere kalan çok az veya hiç dosya gösterir.
- Dosya eşitlenmiyor alanı, hem karşıya yükleme hem de indirme için 0 ' dır.

# <a name="servertabserver"></a>[Sunucu](#tab/server)
Her sunucu için telemetri olay günlüğünde 9102 olay tarafından işaretlenen tamamlanan eşitleme oturumlarına bakın (Olay Görüntüleyicisi `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` ' a gidin). 

1. Belirli bir sunucuda, en son karşıya yükleme ve indirme oturumlarının başarıyla tamamlandığından emin olmak istersiniz. Bunu yapmak için, HResult ve PerItemErrorCount değerinin her ikisi de karşıya yükleme ve indirme için 0 olup olmadığını denetleyin (SyncDirection alanı, belirli bir oturumun karşıya yükleme veya indirme oturumu olup olmadığını gösterir). Yakın zamanda tamamlanan bir eşitleme oturumu görmüyorsanız, büyük miktarda veri eklediyseniz veya değişiklik yaptıysanız beklenmekte olan bir eşitleme oturumunun devam ettiğini unutmayın.
2. Bir sunucu buluta tamamen güncellecektir ve her iki yönde de eşitleme değişikliği olmadığında, boş eşitleme oturumları görürsünüz. Bunlar, tüm Sync * alanları (SyncFileCount, SyncDirCount, SyncTombstoneCount ve SyncSizeBytes) sıfır olduğu anlamına gelen karşıya yükleme ve indirme olayları tarafından belirtilir, ancak eşitleme için hiçbir şey yoktur. Eşitlenmesi gereken her zaman yeni bir şey olduğundan, bu boş eşitleme oturumlarının yüksek dalgalanma sunucularında gerçekleşmediğini unutmayın. Eşitleme etkinliği yoksa, her 30 dakikada bir gerçekleşmelidir. 
3. Tüm sunucular bulutla güncel ise, en son karşıya yükleme ve indirme oturumları boş eşitleme oturumlarsa da, sistemin bir bütün olarak eşitlenmiş olduğu kesin bir belirsizlik ile söyleyebilirsiniz. 
    
Azure dosya paylaşımınızda değişiklik yaptıysanız Azure Dosya Eşitleme, 24 saatte bir kez gerçekleşen değişiklik numaralandırması tamamlanana kadar bu değişikliği algılamayacağını unutmayın. Bir sunucu, Azure dosya paylaşımında doğrudan yapılan son değişiklikler eksik olduğunda bulut ile güncel olduğunu söyler. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Nasıl yaparım?, eşitlenmekte olan belirli dosya veya klasörler olup olmadığını görmek mi istiyorsunuz?
Sunucu üzerinde PerItemErrorCount veya portalda eşitlenmemiş dosyalar, belirli bir eşitleme oturumunda 0 ' dan büyükse, bazı öğelerin eşitlenmesi başarısız oluyor demektir. Dosya ve klasörlerin eşitlenmesine engel olan özellikler olabilir. Bu özellikler kalıcı olabilir ve eşitlemeyi sürdürmeye yönelik açık eylem gerektirebilir, örneğin, dosya veya klasör adından desteklenmeyen karakterleri kaldırır. Bunlar da geçici olabilir. Bu, dosya veya klasörün otomatik olarak eşitlemeyi sürdürmesini belirtir; Örneğin, dosya kapatıldığında açık tanıtıcıların bulunduğu dosyalar otomatik olarak eşitlemeye sürdürülür. Azure Dosya Eşitleme altyapısı böyle bir sorunu algıladığında, şu anda doğru şekilde eşitlenmeyen öğeleri listelemek için ayrıştırılabilen bir hata günlüğü oluşturulur.

Bu hataları görmek için, açık tanıtıcılar, desteklenmeyen karakterler veya diğer sorunlar nedeniyle eşitleme başarısız olan dosyaları belirlemek üzere **Filesyncerrorsreport. ps1** PowerShell betiğini (Azure dosya eşitleme aracısının aracı yükleme dizininde bulunur) çalıştırın. ItemPath alanı, kök eşitleme diziniyle ilişkili olarak dosyanın konumunu söyler. Düzeltme adımları için aşağıdaki yaygın eşitleme hatalarının listesine bakın.

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Dosya/Dizin Eşitleme hataları başına sorun giderme
**Itemresults-öğe başına Eşitleme hataları**  

| HRESULT | HRESULT (ondalık) | Hata dizesi | Sorun | Düzeltmesi |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | Sunucudaki katmanlı dosya erişilebilir değil. Bu sorun, katmanlı dosya bir sunucu uç noktası silinmeden önce geri çağrılmıyorsa oluşur. | Bu sorunu çözmek için, [sunucu uç noktasını sildikten sonra, sunucuda katmanlı dosyalara erişilebilir değil ' a](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)bakın. |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | Bağımlı bir klasör henüz eşitlenmediği için dosya veya dizin değişikliği henüz eşitlenemiyor. Bu öğe, bağımlı değişiklikler eşitlendikten sonra eşitlenecek. | Eylem gerekmiyor. |
| 0x8007007B | -2147024773 | ERROR_INVALID_NAME | Dosya veya dizin adı geçersiz. | Söz konusu dosyayı veya dizini yeniden adlandırın. Daha fazla bilgi için [Desteklenmeyen karakterleri işleme](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) bölümüne bakın. |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | Dosya veya dizin adı geçersiz. | Söz konusu dosyayı veya dizini yeniden adlandırın. Daha fazla bilgi için [Desteklenmeyen karakterleri işleme](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) bölümüne bakın. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Dosya kullanımda olduğundan eşitlenemiyor. Dosya artık kullanımda olmadığında eşitlenecek. | Eylem gerekmiyor. Azure Dosya Eşitleme, açık tanıtıcıların bulunduğu dosyaları eşitlemek için sunucuda günde bir kez geçici bir VSS anlık görüntüsü oluşturur. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Dosya değişti, ancak değişiklik eşitleme tarafından henüz algılanmadı. Eşitleme, bu değişiklik algılandıktan sonra kurtarılacak. | Eylem gerekmiyor. |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | Dosya silindi ve eşitleme değişikliğin farkında değildir. | Eylem gerekmiyor. Değişiklik algılama, dosyanın silindiğini algıladığında, eşitleme bu hatayı günlüğe kaydetmeyi durduracak. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | Dosya atlandı, ancak sonraki eşitleme oturumu sırasında eşitlenecek. | Eylem gerekmiyor. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Azure dosya paylaşımının sınırına ulaşıldığından dosya eşitlenemiyor. | Bu sorunu çözmek için sorun giderme kılavuzundaki [Azure dosya paylaşma depolama sınırı bölümüne ulaştınız](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810) . |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | Dosya desteklenmeyen bir çözüm (NTFS EFS gibi) tarafından şifrelenir. | Dosyanın şifresini çözün ve desteklenen bir şifreleme çözümünü kullanın. Destek çözümlerinin bir listesi için, planlama kılavuzundaki [şifreleme çözümleri](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption-solutions) bölümüne bakın. |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | Dosya, DFS-R salt okunurdur çoğaltma klasöründe bulunur. | Dosya, DFS-R salt okunurdur çoğaltma klasöründe bulunur. Azure dosya eşitleme, DFS-R salt okuma çoğaltma klasörlerindeki sunucu uç noktalarını desteklemez. Daha fazla bilgi için bkz. [Planlama Kılavuzu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) . |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Dosyada silme beklemede durumu vardır. | Eylem gerekmiyor. Dosya, tüm açık dosya tutamaçları kapatıldıktan sonra silinir. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | Depolama hesabındaki güvenlik duvarı ve sanal ağ ayarları etkinleştirildiğinden ve sunucunun depolama hesabına erişimi olmadığından dosya eşitlenemiyor. | Dağıtım kılavuzundaki [güvenlik duvarı ve sanal ağ ayarlarını yapılandırma](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) bölümünde belgelenen adımları IZLEYEREK sunucu IP adresini veya sanal ağını ekleyin. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | Güvenlik tanımlayıcı boyutu 64 KiB sınırını aştığından dosya eşitlenemiyor. | Bu sorunu çözmek için, güvenlik tanımlayıcısı boyutunu azaltmak için dosyadaki erişim denetim girdilerini (ACE) kaldırın. |
| 0x8000FFFF | -2147418113 | E_UNEXPECTED | Beklenmeyen bir hata nedeniyle dosya eşitlenemiyor. | Birkaç gün boyunca hata devam ederse lütfen bir destek talebi açın. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | Dosya kullanımda olduğundan eşitlenemiyor. Dosya artık kullanımda olmadığında eşitlenecek. | Eylem gerekmiyor. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Dosya eşitleme sırasında değiştirildi, bu nedenle yeniden eşitlenmesi gerekiyor. | Eylem gerekmiyor. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | Çakışma dosyası sayısı üst sınırına ulaşıldığından dosya eşitlenemiyor. Azure Dosya Eşitleme dosya başına 100 çakışma dosyasını destekler. Dosya çakışmaları hakkında daha fazla bilgi edinmek için Azure Dosya Eşitleme [SSS](https://docs.microsoft.com/azure/storage/files/storage-files-faq#afs-conflict-resolution)bölümüne bakın. | Bu sorunu çözmek için, çakışma dosyalarının sayısını azaltın. Çakışma dosyası sayısı 100 ' den az olduğunda dosya eşitlenir. |

#### <a name="handling-unsupported-characters"></a>Desteklenmeyen karakterleri işleme
**Filesyncerrorsreport. ps1** PowerShell betiği desteklenmeyen karakterler (hata kodu 0x8007007B veya 0x80c80255) nedeniyle hataları gösteriyorsa, ilgili dosya adlarından hata durumunda karakterleri kaldırmanız veya yeniden adlandırmanız gerekir. Bu karakterlerin çoğu standart görsel kodlamaya sahip olmadığından, PowerShell bu karakterleri muhtemelen soru işaretleri veya boş dikdörtgenler olarak yazdıracaktır. [Değerlendirme aracı](storage-sync-files-planning.md#evaluation-cmdlet) , desteklenmeyen karakterleri belirlemek için kullanılabilir.

Aşağıdaki tabloda, Azure Dosya Eşitleme henüz desteklemediği tüm Unicode karakterler bulunur.

| Karakter kümesi | Karakter sayısı |
|---------------|-----------------|
| <ul><li>0x0000009D (OSC işletim sistemi komutu)</li><li>0x00000090 (DCS cihaz denetim dizesi)</li><li>0x0000008F (SS3 tek SHIFT üç)</li><li>0x00000081 (yüksek sekizli ön ayarı)</li><li>0x0000007F (del Delete)</li><li>0x0000008D (RI ters satır besleme)</li></ul> | 6 |
| 0x0000FDD0-0x0000FDEF (Arapça sunum formları-a) | 32 |
| 0x0000FFF0-0x0000FFFF (özel) | 16 |
| <ul><li>0x0001FFFE-0x0001FFFF = 2 (karakter olmayan)</li><li>0x0002FFFE-0x0002FFFF = 2 (karakter olmayan)</li><li>0x0003FFFE-0x0003FFFF = 2 (karakter olmayan)</li><li>0x0004FFFE-0x0004FFFF = 2 (karakter olmayan)</li><li>0x0005FFFE-0x0005FFFF = 2 (karakter olmayan)</li><li>0x0006FFFE-0x0006FFFF = 2 (karakter olmayan)</li><li>0x0007FFFE-0x0007FFFF = 2 (karakter olmayan)</li><li>0x0008FFFE-0x0008FFFF = 2 (karakter olmayan)</li><li>0x0009FFFE-0x0009FFFF = 2 (karakter olmayan)</li><li>0X000affe-0X000afff = 2 (karakter olmayan)</li><li>0x000BFFFE-0x000BFFFF = 2 (karakter olmayan)</li><li>0x000CFFFE-0x000CFFFF = 2 (karakter olmayan)</li><li>0x000DFFFE-0x000DFFFF = 2 (karakter olmayan)</li><li>0x000EFFFE-0x000EFFFF = 2 (tanımsız)</li><li>0x000FFFFE-0x000FFFFF = 2 (tamamlayıcı özel kullanım alanı)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Ortak Eşitleme hataları
<a id="-2147023673"></a>**Eşitleme oturumu iptal edildi.**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (ondalık)** | -2147023673 | 
| **Hata dizesi** | ERROR_CANCELLED |
| **Düzeltme gerekli** | Hayır |

Eşitleme oturumları, sunucunun yeniden başlatılması veya güncelleştirilmesini, VSS anlık görüntülerini vb. dahil çeşitli nedenlerle başarısız olabilir. Bu hata, izlenmesi gereken gibi görünse de, birkaç saat boyunca devam etmediği takdirde bu hatayı yoksaymak güvenlidir.

<a id="-2147012889"></a>**Hizmetle bir bağlantı kurulamadı.**    

| | |
|-|-|
| **HRESULT** | 0x80072EE7 |
| **HRESULT (ondalık)** | -2147012889 | 
| **Hata dizesi** | WININET_E_NAME_NOT_RESOLVED |
| **Düzeltme gerekli** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**Kullanıcı isteği hizmet tarafından kısıtlandı.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (ondalık)** | -2134376372 |
| **Hata dizesi** | ECS_E_USER_REQUEST_THROTTLED |
| **Düzeltme gerekli** | Hayır |

Herhangi bir eylem gerekmez; sunucu yeniden denenecek. Bu hata birkaç saat devam ederse, bir destek isteği oluşturun.

<a id="-2134364043"></a>**Değişiklik algılama geri yükleme sonrası tamamlanana kadar eşitleme engellendi**  

| | |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (ondalık)** | -2134364043 |
| **Hata dizesi** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Düzeltme gerekli** | Hayır |

İşlem yapmanız gerekmez. Bir dosya veya dosya paylaşma (bulut uç noktası) Azure Backup kullanılarak geri yüklendiğinde, Azure dosya paylaşımında değişiklik algılama tamamlanana kadar eşitleme engellenir. Değişiklik algılama işlemi, geri yükleme tamamlandıktan ve süre dosya paylaşımındaki dosya sayısına dayandıktan hemen sonra çalışır.

<a id="-2147216747"></a>**Eşitleme veritabanı kaldırıldığından eşitleme başarısız oldu.**  

| | |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT (ondalık)** | -2147216747 |
| **Hata dizesi** | SYNC_E_METADATA_INVALID_OPERATION |
| **Düzeltme gerekli** | Hayır |

Bu hata genellikle bir yedekleme uygulaması bir VSS anlık görüntüsü oluşturduğunda ve eşitleme veritabanı kaldırıldığında oluşur. Bu hata birkaç saat devam ederse, bir destek isteği oluşturun.

<a id="-2134364065"></a>**Eşitleme, bulut uç noktasında belirtilen Azure dosya paylaşımıyla erişemiyor.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (ondalık)** | -2134364065 |
| **Hata dizesi** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Düzeltme gerekli** | Yes |

Bu hata, Azure Dosya Eşitleme Aracısı Azure dosya paylaşımında erişemediği için oluşur. Bu, Azure dosya paylaşımının veya onu barındıran depolama hesabının artık var olmaması olabilir. Aşağıdaki adımlarda çalışarak bu hatayı giderebilirsiniz:

1. [Depolama hesabının mevcut olduğundan emin olun.](#troubleshoot-storage-account)
2. [Azure dosya paylaşımının mevcut olduğundan emin olun.](#troubleshoot-azure-file-share)
3. [Azure Dosya Eşitleme depolama hesabına erişiminin olduğundan emin olun.](#troubleshoot-rbac)
4. [Depolama hesabındaki güvenlik duvarının ve sanal ağ ayarlarının düzgün yapılandırıldığını doğrulama (etkinse)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**Kullanılan depolama hesabı adı çözümlenemedi.**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (ondalık)** | -2134364064 |
| **Hata dizesi** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Düzeltme gerekli** | Yes |

1. Depolama DNS adını sunucudan çözümleyebilmeniz için denetleyin.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Depolama hesabının mevcut olduğundan emin olun.](#troubleshoot-storage-account)
3. [Depolama hesabındaki güvenlik duvarının ve sanal ağ ayarlarının düzgün yapılandırıldığını doğrulama (etkinse)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Depolama hesabına erişirken bilinmeyen bir hata oluştu.**  

| | |
|-|-|
| **HRESULT** | 0x80c8308a |
| **HRESULT (ondalık)** | -2134364022 |
| **Hata dizesi** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Düzeltme gerekli** | Yes |

1. [Depolama hesabının mevcut olduğundan emin olun.](#troubleshoot-storage-account)
2. [Depolama hesabındaki güvenlik duvarının ve sanal ağ ayarlarının düzgün yapılandırıldığını doğrulama (etkinse)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-1906441138"></a>**Eşitleme veritabanıyla ilgili bir sorun nedeniyle eşitleme başarısız oldu.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (ondalık)** | -1906441138 |
| **Hata dizesi** | JET_errWriteConflict |
| **Düzeltme gerekli** | Yes |

Azure Dosya Eşitleme tarafından kullanılan iç veritabanıyla ilgili bir sorun olduğunda bu hata oluşur. Bu sorun oluştuğunda, bir destek isteği oluşturun ve bu sorunu çözmenize yardımcı olması için sizinle iletişim kuracağız.

<a id="-2134364053"></a>**Sunucuda yüklü Azure Dosya Eşitleme Aracısı sürümü desteklenmiyor.**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (ondalık)** | -2134364053 |
| **Hata dizesi** | ECS_E_AGENT_VERSION_BLOCKED |
| **Düzeltme gerekli** | Yes |

Bu hata, sunucuda yüklü Azure Dosya Eşitleme Aracısı sürümü desteklenmiyorsa oluşur. Bu sorunu çözmek için [desteklenen bir aracı sürümüne]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions) [yükseltin]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) .

<a id="-2134351810"></a>**Azure dosya paylaşımının depolama sınırına ulaştınız.**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (ondalık)** | -2134351810 |
| **Hata dizesi** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Düzeltme gerekli** | Yes |

Azure dosya paylaşma depolama sınırına ulaşıldığında bu hata oluşur. bu durum, bir Azure dosya paylaşımında kota uygulandığında veya kullanım bir Azure dosya paylaşımının sınırlarını aşarsa oluşabilir. Daha fazla bilgi için bkz. [Azure dosya paylaşımının geçerli sınırları](storage-files-scale-targets.md).

1. Depolama eşitleme hizmeti içindeki eşitleme grubuna gidin.
2. Eşitleme grubu içindeki bulut uç noktasını seçin.
3. Açılan bölmedeki Azure dosya paylaşımının adını aklınızda edin.
4. Bağlı depolama hesabını seçin. Bu bağlantı başarısız olursa, başvurulan depolama hesabı kaldırılmıştır.

    ![Bulut uç noktası ayrıntı bölmesini, depolama hesabına yönelik bir bağlantı ile gösteren ekran görüntüsü.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Dosya paylaşımlarının listesini görüntülemek için **dosyalar** ' ı seçin.
6. Bulut uç noktası tarafından başvurulan Azure dosya paylaşımının satırının sonundaki üç noktaya tıklayın.
7. **Kullanımın** **kotanın**altında olduğunu doğrulayın. Alternatif bir kota belirtilmediği takdirde, kota [Azure dosya paylaşımının en büyük boyutuyla](storage-files-scale-targets.md)eşleşmeyecektir.

    ![Azure dosya paylaşma özelliklerinin ekran görüntüsü.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Paylaşma doluysa ve bir kota ayarlanmamışsa, bu sorunu düzeltmenin olası bir yolu, geçerli sunucu uç noktasının her bir alt klasörünü kendi ayrı eşitleme gruplarında kendi sunucu uç noktasına vermektir. Bu şekilde, her alt klasör bireysel Azure dosya paylaşımlarına eşitlenir.

<a id="-2134351824"></a>**Azure dosya paylaşımında bulunamıyor.**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (ondalık)** | -2134351824 |
| **Hata dizesi** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Düzeltme gerekli** | Yes |

Bu hata, Azure dosya paylaşımında erişilebilir olmadığında oluşur. Sorun gidermek için:

1. [Depolama hesabının mevcut olduğundan emin olun.](#troubleshoot-storage-account)
2. [Azure dosya paylaşımının mevcut olduğundan emin olun.](#troubleshoot-azure-file-share)

Azure dosya paylaşma silinmişse, yeni bir dosya paylaşma oluşturmanız ve ardından eşitleme grubunu yeniden oluşturmanız gerekir. 

<a id="-2134364042"></a>**Bu Azure aboneliği askıya alındığında eşitleme duraklatıldı.**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (ondalık)** | -2134364042 |
| **Hata dizesi** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Düzeltme gerekli** | Yes |

Bu hata, Azure aboneliği askıya alındığında oluşur. Azure aboneliği geri yüklendiğinde eşitleme yeniden etkinleştirilecek. [Azure Aboneliğimin neden devre dışı bırakıldığını ve daha fazla bilgi için nasıl yeniden etkinleştirebilirim?](../../billing/billing-subscription-become-disable.md) bölümüne bakın.

<a id="-2134364052"></a>**Depolama hesabında yapılandırılmış bir güvenlik duvarı veya sanal ağ vardır.**  

| | |
|-|-|
| **HRESULT** | 0x80c8306c |
| **HRESULT (ondalık)** | -2134364052 |
| **Hata dizesi** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **Düzeltme gerekli** | Yes |

Bu hata, depolama hesabı güvenlik duvarı nedeniyle veya depolama hesabı bir sanal ağa ait olduğu için Azure dosya paylaşımında erişilebilir olmadığında oluşur. Depolama hesabındaki güvenlik duvarının ve sanal ağ ayarlarının doğru şekilde yapılandırıldığını doğrulayın. Daha fazla bilgi için bkz. [güvenlik duvarı ve sanal ağ ayarlarını yapılandırma](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings). 

<a id="-2134375911"></a>**Eşitleme veritabanıyla ilgili bir sorun nedeniyle eşitleme başarısız oldu.**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (ondalık)** | -2134375911 |
| **Hata dizesi** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Düzeltme gerekli** | Hayır |

Bu hata genellikle kendiliğinden çözülür ve bu durum oluşabilir:

* Eşitleme grubundaki sunucular arasında yüksek sayıda dosya değişikliği.
* Tek tek dosyalarda ve dizinlerde çok sayıda hata var.

Bu hata birkaç saatten uzun sürerse, bir destek isteği oluşturun ve bu sorunu çözmenize yardımcı olması için sizinle iletişim kuracağız.

<a id="-2146762487"></a>**Sunucu güvenli bir bağlantı kuramadı. Bulut hizmeti beklenmeyen bir sertifika aldı.**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (ondalık)** | -2146762487 |
| **Hata dizesi** | CERT_E_UNTRUSTEDROOT |
| **Düzeltme gerekli** | Yes |

Kuruluşunuz bir SSL Sonlandırıcı proxy kullanıyorsa veya kötü amaçlı bir varlık, sunucunuz ve Azure Dosya Eşitleme hizmeti arasındaki trafiği kesintiye uğrayadıysanız bu hata oluşabilir. Bunun beklendiğinden eminseniz (kuruluşunuz bir SSL sonlandırma proxy kullandığından), bir kayıt defteri geçersiz kılma ile sertifika doğrulamayı atlayabilirsiniz.

1. SkipVerifyingPinnedRootCertificate kayıt defteri değerini oluşturun.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Kayıtlı sunucuda eşitleme hizmetini yeniden başlatın.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Bu kayıt defteri değerini ayarlayarak Azure Dosya Eşitleme Aracısı, sunucu ile bulut hizmeti arasında veri aktarırken yerel olarak güvenilen tüm SSL sertifikalarını kabul eder.

<a id="-2147012894"></a>**Hizmetle bir bağlantı kurulamadı.**  

| | |
|-|-|
| **HRESULT** | 0x80072EE2 |
| **HRESULT (ondalık)** | -2147012894 |
| **Hata dizesi** | WININET_E_TIMEOUT |
| **Düzeltme gerekli** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**Kimlik doğrulamasıyla ilgili bir sorun nedeniyle eşitleme başarısız oldu.**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (ondalık)** | -2134375680 |
| **Hata dizesi** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Düzeltme gerekli** | Yes |

Bu hata genellikle sunucu saati yanlış olduğu için oluşur. Sunucu bir sanal makinede çalışıyorsa, konaktaki saatin doğru olduğundan emin olun.

<a id="-2134364040"></a>**Sertifika süre sonu nedeniyle eşitleme başarısız oldu.**  

| | |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT (ondalık)** | -2134364040 |
| **Hata dizesi** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Düzeltme gerekli** | Yes |

Kimlik doğrulama için kullanılan sertifikanın kullanım dışı bırakıldığı için bu hata oluşur.

Sertifikanın dolduğunu doğrulamak için aşağıdaki adımları gerçekleştirin:  
1. Sertifikalar MMC ek bileşenini açın, bilgisayar hesabı ' nı seçin ve Sertifikalar (yerel bilgisayar) \ kişisel \ sertifika ates' a gidin.
2. İstemci kimlik doğrulama sertifikasının dolup dolmadığına bakın.

İstemci kimlik doğrulama sertifikasının geçerliliği dolmuşsa, sorunu çözmek için aşağıdaki adımları gerçekleştirin:

1. Azure Dosya Eşitleme Agent sürümü 4.0.1.0 veya üstünün yüklü olduğunu doğrulayın.
2. Sunucuda aşağıdaki PowerShell komutunu çalıştırın:

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**Kimlik doğrulama sertifikası bulunamadığı için eşitleme başarısız oldu.**  

| | |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT (ondalık)** | -2134375896 |
| **Hata dizesi** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Düzeltme gerekli** | Yes |

Kimlik doğrulaması için kullanılan sertifika bulunamadığı için bu hata oluşur.

Bu sorunu çözmek için aşağıdaki adımları gerçekleştirin:

1. Azure Dosya Eşitleme Agent sürümü 4.0.1.0 veya üstünün yüklü olduğunu doğrulayın.
2. Sunucuda aşağıdaki PowerShell komutunu çalıştırın:

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**Kimlik doğrulama kimliği bulunamadığı için eşitleme başarısız oldu.**  

| | |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT (ondalık)** | -2134364039 |
| **Hata dizesi** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Düzeltme gerekli** | Yes |

Sunucu uç noktası silmenin başarısız olması ve uç noktanın artık kısmen silinmiş olması nedeniyle bu hata oluşur. Bu sorunu çözmek için sunucu uç noktasını silmeyi yeniden deneyin.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Sunucu uç noktasının bulunduğu birimde disk alanı azalmış olur.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (ondalık)** | -1906441711 |
| **Hata dizesi** | JET_errLogDiskFull |
| **Düzeltme gerekli** | Yes |
| | |
| **HRESULT** | 0x80c8031a |
| **HRESULT (ondalık)** | -2134375654 |
| **Hata dizesi** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Düzeltme gerekli** | Yes |

Bu hata, birim doldurulduğundan oluşur. Bu hata genellikle sunucu uç noktası dışındaki dosyalar birimde boş alan kullandığından oluşur. Başka sunucu uç noktaları ekleyerek, dosyaları farklı bir birime taşıyarak veya sunucu uç noktasının açık olduğu birimin boyutunu artırarak birimde yer açın.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**Hizmet henüz bu sunucu uç noktası ile eşitlenmeye hazırlanıyor.**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (ondalık)** | -2134364145 |
| **Hata dizesi** | ECS_E_REPLICA_NOT_READY |
| **Düzeltme gerekli** | Hayır |

Bu hata, Azure dosya paylaşımında doğrudan değişiklikler olduğu ve değişiklik algılama işleminin devam ettiği için oluşur. Değişiklik algılama tamamlandığında eşitleme başlar.

[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Birçok ayrı dosya ile ilgili sorunlar nedeniyle eşitleme başarısız oldu.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (ondalık)** | -2134375877 |
| **Hata dizesi** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Düzeltme gerekli** | Yes |
| | |
| **HRESULT** | 0x80c8021c |
| **HRESULT (ondalık)** | -2134375908 |
| **Hata dizesi** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Düzeltme gerekli** | Yes |
| | |
| **HRESULT** | 0x80c80253 |
| **HRESULT (ondalık)** | -2134375853 |
| **Hata dizesi** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Düzeltme gerekli** | Yes |

Dosya başına eşitleme hatalarının çok fazla olması durumunda, eşitleme oturumları başarısız olabilir. <!-- To troubleshoot this state, see [Troubleshooting per file/directory sync errors]().-->

> [!NOTE]
> Azure Dosya Eşitleme, açık tanıtıcıların bulunduğu dosyaları eşitlemek için sunucuda günde bir kez geçici bir VSS anlık görüntüsü oluşturur.

<a id="-2134376423"></a>**Sunucu uç noktası yolundaki bir sorun nedeniyle eşitleme başarısız oldu.**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (ondalık)** | -2134376423 |
| **Hata dizesi** | ECS_E_SYNC_INVALID_PATH |
| **Düzeltme gerekli** | Yes |

Yolun var olduğundan, yerel bir NTFS biriminde olduğundan ve bir yeniden ayrıştırma noktası ya da var olan sunucu uç noktası olmadığından emin olun.

<a id="-2134375817"></a>**Filtre sürücüsü sürümü Aracı sürümüyle uyumlu olmadığından eşitleme başarısız oldu**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (ondalık)** | -2134375817 |
| **Hata dizesi** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Düzeltme gerekli** | Yes |

Bu hata, yüklenen bulut katmanlama filtresi sürücüsü (Storagessync. sys) sürümü depolama eşitleme Aracısı (FileSyncSvc) hizmetiyle uyumlu olmadığı için oluşur. Azure Dosya Eşitleme Aracısı yükseltilmişse, yüklemeyi gerçekleştirmek için sunucuyu yeniden başlatın. Hata oluşmaya devam ederse aracıyı kaldırın, sunucuyu yeniden başlatın ve Azure Dosya Eşitleme aracısını yeniden yükleyin.

<a id="-2134376373"></a>**Hizmet şu anda kullanılamıyor.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (ondalık)** | -2134376373 |
| **Hata dizesi** | ECS_E_SERVICE_UNAVAILABLE |
| **Düzeltme gerekli** | Hayır |

Azure Dosya Eşitleme hizmeti kullanılamadığından bu hata oluşur. Azure Dosya Eşitleme hizmeti yeniden kullanılabilir olduğunda bu hata otomatik olarak çözümlenir.

<a id="-2146233088"></a>**Bir özel durum nedeniyle eşitleme başarısız oldu.**  

| | |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT (ondalık)** | -2146233088 |
| **Hata dizesi** | COR_E_EXCEPTION |
| **Düzeltme gerekli** | Hayır |

Eşitleme bir özel durum nedeniyle başarısız olduğu için bu hata oluşur. Hata birkaç saat devam ederse lütfen bir destek isteği oluşturun.

<a id="-2134364045"></a>**Depolama hesabı başka bir bölgeye yük devrettiğinden eşitleme başarısız oldu.**  

| | |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT (ondalık)** | -2134364045 |
| **Hata dizesi** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Düzeltme gerekli** | Yes |

Bu hata, depolama hesabının başka bir bölgeye yük devrettiğinden oluşur. Azure Dosya Eşitleme depolama hesabı yük devretme özelliğini desteklemiyor. Azure Dosya Eşitleme bulut uç noktaları olarak kullanılan Azure dosya paylaşımlarını içeren depolama hesapları yük devretmemelidir. Bunun yapılması eşitlemenin durmasına neden olur ve yeni katmanlı dosyalar söz konusu olduğunda beklenmedik veri kaybına neden olabilir. Bu sorunu çözmek için depolama hesabını birincil bölgeye taşıyın.

<a id="-2134375922"></a>**Eşitleme veritabanı ile ilgili geçici bir sorun nedeniyle eşitleme başarısız oldu.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (ondalık)** | -2134375922 |
| **Hata dizesi** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Düzeltme gerekli** | Hayır |

Bu hata, eşitleme veritabanıyla ilgili bir iç sorun nedeniyle oluşur. Bu hata, eşitleme yeniden denemeleri sırasında otomatik olarak çözümlenir. Bu hata uzabir süre devam ederse, bir destek isteği oluşturun ve bu sorunu çözmenize yardımcı olması için sizinle iletişim kuracağız.

<a id="-2134364024"></a>**Azure Active Directory kiracısındaki değişiklik nedeniyle eşitleme başarısız oldu**  

| | |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT (ondalık)** | -2134364024 | 
| **Hata dizesi** | ECS_E_INVALID_AAD_TENANT |
| **Düzeltme gerekli** | Yes |

Bu hata, aboneliğin farklı bir Azure Active Directory kiracısına taşınmasını desteklemediği Azure Dosya Eşitleme oluşur.
 
Bu sorunu çözmek için aşağıdaki seçeneklerden birini gerçekleştirin:

- **1. seçenek (önerilir)** : aboneliği özgün Azure Active Directory kiracıya geri taşıyın
- **2. seçenek**: geçerli eşitleme grubunu silin ve yeniden oluşturun. Bulut katmanlaması sunucu uç noktasında etkinleştirildiyse, eşitleme grubunu silin ve ardından, eşitleme gruplarını yeniden oluşturmadan önce, yalnız bırakılmış katmanlı dosyaları kaldırmak için [bulut katmanlaması bölümünde]( https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) belgelenen adımları gerçekleştirin. 

<a id="-2134364010"></a>**Güvenlik Duvarı ve sanal ağ özel durumu yapılandırılmadığı için eşitleme başarısız oldu**  

| | |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT (ondalık)** | -2134364010 | 
| **Hata dizesi** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Düzeltme gerekli** | Yes |

Bu hata, güvenlik duvarı ve sanal ağ ayarları depolama hesabında etkinleştirilmişse ve "Güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine Izin ver" özel durumu işaretli değilse oluşur. Bu sorunu çözmek için dağıtım kılavuzu 'ndaki [güvenlik duvarı ve sanal ağ ayarlarını yapılandırma](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) bölümünde belgelenen adımları izleyin.

<a id="-2147024891"></a>**Sistem birimi bilgi klasörü izinleri yanlış olduğundan eşitleme başarısız oldu.**  

| | |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT (ondalık)** | -2147024891 |
| **Hata dizesi** | ERROR_ACCESS_DENIED |
| **Düzeltme gerekli** | Yes |

NT AUTHORITY\SYSTEM hesabının, sunucu uç noktasının bulunduğu birimde sistem birimi bilgileri klasörü için izinleri yoksa, bu hata oluşabilir. Bağımsız dosyalar ERROR_ACCESS_DENIED ile eşitlenememesi durumunda [Dosya/Dizin eşitleme hatalarını giderme](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshooting-per-filedirectory-sync-errors) bölümünde belgelenen adımları uygulayın.

Bu sorunu çözmek için aşağıdaki adımları gerçekleştirin:

1. [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) aracını indirin.
2. Sistem hesabını kullanarak bir komut istemi başlatmak için yükseltilmiş bir komut isteminden aşağıdaki komutu çalıştırın: **PsExec. exe-i-s-d cmd** 
3. Sistem hesabı altında çalışan komut isteminden, NT AUTHORITY\SYSTEM hesabının sistem birimi bilgileri klasörüne erişimi olmadığından emin olmak için aşağıdaki komutu çalıştırın: **cacls "sürücü harfi: \ sistem birimi bilgileri"/T/c**
4. NT AUTHORITY\SYSTEM hesabının sistem birimi bilgileri klasörüne erişimi yoksa, şu komutu çalıştırın: **cacls "sürücü harfi: \ sistem birimi bilgileri"/T/E/g "NT AUTHORITY\SYSTEM: F"**
    - #4 adım erişim engellendi komutuyla başarısız olursa, sistem birimi bilgileri klasörünün sahipliğini almak için aşağıdaki komutu çalıştırın ve sonra #4: **takeown/A/R/f "sürücü harfi: \ sistem birimi bilgileri"** öğesini tekrarlayın.

<a id="-2134375810"></a>**Azure dosya paylaşımının silindiği ve yeniden oluşturulduğu için eşitleme başarısız oldu.**  

| | |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT (ondalık)** | -2134375810 |
| **Hata dizesi** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Düzeltme gerekli** | Yes |

Bu hata, aynı eşitleme grubundaki bir Azure dosya paylaşımının silinmesini ve yeniden oluşturulmasını desteklemediğinden Azure Dosya Eşitleme oluşur. 

Bu sorunu çözmek için, aşağıdaki adımları gerçekleştirerek eşitleme grubunu silip yeniden oluşturun:

1. Eşitleme grubundaki tüm sunucu uç noktalarını silin.
2. Bulut uç noktasını silin. 
3. Eşitleme grubunu silin.
4. Bulut katmanlaması bir sunucu uç noktasında etkinleştirilmişse, sunucu [uç noktası bölümünü sildikten sonra katmanlı dosyalar](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) bölümünde belgelenen adımları gerçekleştirerek sunucuda yalnız bırakılmış olan dosyaları silin.
5. Eşitleme grubunu yeniden oluşturun.

<a id="-2145844941"></a>**HTTP isteği yeniden yönlendirildiği için eşitleme başarısız oldu**  

| | |
|-|-|
| **HRESULT** | 0x80190133 |
| **HRESULT (ondalık)** | -2145844941 |
| **Hata dizesi** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **Düzeltme gerekli** | Yes |

Bu hata, Azure Dosya Eşitleme HTTP yeniden yönlendirmeyi (3xx durum kodu) desteklemediğinden oluşur. Bu sorunu çözmek için, proxy sunucunuzda veya ağ cihazınızda HTTP yeniden yönlendirmeyi devre dışı bırakın.

### <a name="common-troubleshooting-steps"></a>Ortak sorun giderme adımları
<a id="troubleshoot-storage-account"></a>**Depolama hesabının mevcut olduğundan emin olun.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Depolama eşitleme hizmeti içindeki eşitleme grubuna gidin.
2. Eşitleme grubu içindeki bulut uç noktasını seçin.
3. Açılan bölmedeki Azure dosya paylaşımının adını aklınızda edin.
4. Bağlı depolama hesabını seçin. Bu bağlantı başarısız olursa, başvurulan depolama hesabı kaldırılmıştır.
    ![ depolama hesabı bağlantısı ile bulut uç noktası ayrıntı bölmesini gösteren ekran görüntüsü. ](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Azure dosya paylaşımının mevcut olduğundan emin olun.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Ana depolama hesabı sayfasına dönmek için sol taraftaki içindekiler tablosuna **Genel Bakış ' a** tıklayın.
2. Dosya paylaşımlarının listesini görüntülemek için **dosyalar** ' ı seçin.
3. Bulut uç noktası tarafından başvurulan dosya paylaşımının dosya paylaşımları listesinde göründüğünü doğrulayın (yukarıdaki 1. adımda bu dosyayı Not etmeniz gerekir).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**Azure Dosya Eşitleme depolama hesabına erişiminin olduğundan emin olun.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Sol taraftaki içindekiler tablosunda **erişim denetimi (IAM)** seçeneğine tıklayın.
1. Depolama hesabınıza erişimi olan Kullanıcı ve uygulamaları (*hizmet sorumluları*) listelemek için **rol atamaları** sekmesine tıklayın.
1. **Karma dosya eşitleme hizmetinin** , **okuyucu ve veri erişimi** rolüyle listede göründüğünü doğrulayın. 

    ![Depolama hesabının erişim denetimi sekmesinde karma Dosya Eşitleme hizmet hizmet sorumlusu ekran görüntüsü](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    **Karma dosya eşitleme hizmeti** listede görünmezse, aşağıdaki adımları uygulayın:

    - **Ekle**'ye tıklayın.
    - **Rol** alanında, **okuyucu ve veri erişimi**' ni seçin.
    - **Seç** alanına **karma dosya eşitleme hizmeti**yazın, rolü seçin ve **Kaydet**' e tıklayın.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Hybrid File Sync Service" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Nasıl yaparım?, kullanıcıların sunucuda desteklenmeyen karakterler içeren dosyalar oluşturmasını önler mi?
[Dosya sunucusu Kaynak Yöneticisi (FSRM) dosya ekranlarını](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management) , adlarında desteklenmeyen karakterlerden oluşan dosyaları sunucuda oluşturulmasını engellemek için kullanabilirsiniz. Desteklenmeyen karakterlerin çoğu yazdırılamaz olduğundan PowerShell 'i kullanarak bunu yapmanız gerekebilir. bu nedenle, onaltılı gösterimlerini öncelikle karakter olarak atamalısınız.

Önce [New-Fsrmfılegroup cmdlet 'ini](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup)kullanarak bir FSRM dosya grubu oluşturun. Bu örnek, grubu desteklenmeyen karakterlerden yalnızca ikisini içerecek şekilde tanımlar, ancak dosya grubunuza gereken sayıda karakteri dahil edebilirsiniz.

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

Bir FSRM dosya grubu tanımladıktan sonra New-FsrmFileScreen cmdlet 'ini kullanarak bir FSRM dosyası ekranı oluşturabilirsiniz.

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Dosya ekranlarının yalnızca Azure Dosya Eşitleme tarafından desteklenmeyen karakterlerin oluşturulmasını engellemek için kullanılması gerektiğini unutmayın. Dosya ekranları diğer senaryolarda kullanılıyorsa, eşitleme sürekli olarak dosyaları Azure dosya paylaşımından sunucuya indirmeye çalışır ve dosya ekranı nedeniyle, yüksek veri çıkışı elde edilecek şekilde engellenir. 

## <a name="cloud-tiering"></a>Bulut katmanlaması 
Bulut katmanlarındaki hatalara yönelik iki yol vardır:

- Dosyalar katmanında başarısız olabilir. Bu, Azure Dosya Eşitleme başarısız olduğu anlamına gelir.
- Dosyalar, bir Kullanıcı katmanlı bir dosyaya erişmeyi denediğinde Azure Dosya Eşitleme dosya sistemi filtresinin (Storagessync. sys) verileri indiremediği anlamına gelir.

Hata yolu aracılığıyla gerçekleşebileceği iki ana hata sınıfı vardır:

- Bulut depolama sorunları
    - *Geçici depolama hizmeti kullanılabilirlik sorunları*. Daha fazla bilgi için bkz. [Azure depolama için hizmet düzeyi sözleşmesi (SLA)](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Erişilemeyen Azure dosya paylaşma*. Bu hata genellikle, bir eşitleme grubunda hala bir bulut uç noktası olduğunda Azure dosya paylaşımından sildiğiniz zaman gerçekleşir.
    - *Erişilemeyen depolama hesabı*. Bu hata genellikle, bir eşitleme grubundaki bir bulut uç noktası olan bir Azure dosya paylaşımıyla hala depolama hesabını sildiğinizde meydana gelir. 
- Sunucu arızaları 
  - *Azure dosya eşitleme dosya sistemi filtresi (Storagessync. sys) yüklü değil*. Katmanlama/geri çekme isteklerini yanıtlamak için Azure Dosya Eşitleme dosya sistemi filtresi yüklenmelidir. Bu filtre, birkaç nedenden dolayı oluşabilir, ancak en sık görülen nedenler yöneticinin el ile bellekten kaldırılmamasından kaynaklanıyor olabilir. Azure Dosya Eşitleme düzgün çalışması için Azure Dosya Eşitleme dosya sistemi filtresinin her zaman yüklenmesi gerekir.
  - *Eksik, bozuk veya bozuk yeniden ayrıştırma noktası*. Yeniden ayrıştırma noktası, iki bölümden oluşan bir dosyadaki özel bir veri yapısıdır:
    1. Azure Dosya Eşitleme dosya sistemi filtresinin (Storagessync. sys), dosyaya GÇ üzerinde bazı eylemler yapması gerekebilecek işletim sistemini gösteren bir yeniden ayrıştırma etiketi. 
    2. Dosya sisteminin, ilişkili bulut uç noktasındaki (Azure dosya paylaşımında) dosyanın URI 'sini filtrelemesine işaret eden yeniden ayrıştırma verileri. 
        
       Bir yeniden ayrıştırma noktasının bozulmuş hale gelebileceği en yaygın yolu, bir yöneticinin etiketi veya verilerini değiştirme girişiminde bulunduğu bir şeydir. 
  - *Ağ bağlantısı sorunları*. Bir dosyayı katmana eklemek veya geri çağırmak için, sunucusunun İnternet bağlantısı olması gerekir.

Aşağıdaki bölümlerde, bulut katmanlama sorunlarının nasıl giderileceği ve bir sorunun bir bulut depolama sorunu ya da sunucu sorunu olup olmadığı saptandığını gösterir.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>Bir sunucuda katmanlama etkinliğini izleme  
Bir sunucudaki katmanlama etkinliğini izlemek için, telemetri olay günlüğünde olay KIMLIĞI 9003, 9016 ve 9029 (Olay Görüntüleyicisi içinde uygulamalar ve Services\Microsoft\FileSync\Agent altında bulunur) kullanın.

- Olay KIMLIĞI 9003, bir sunucu uç noktası için hata dağıtımı sağlar. Örneğin, toplam hata sayısı, hata kodu, vb. Her hata kodu için bir olay günlüğe kaydedilir.
- Olay kimliği 9016, bir birim için hayalet kopya oluşturuluyor sonuçları sağlar. Örneğin, boş alan yüzdesi, oturumdaki hayalet dosya sayısı, hayalet olmayan dosya sayısı, vb.
- Olay kimliği 9029, bir sunucu uç noktası için hayalet kopya oluşturuluyor oturum bilgileri sağlar. Örneğin, oturumda denendiği dosya sayısı, oturumda katmanlı dosya sayısı, zaten katmanlı dosya sayısı, vb.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>Bir sunucuda geri çekme etkinliğini izleme
Bir sunucudaki geri çağırma etkinliğini izlemek için, telemetri olay günlüğünde olay KIMLIĞI 9005, 9006, 9009 ve 9059 (Olay Görüntüleyicisi içinde uygulamalar ve Services\Microsoft\FileSync\Agent altında bulunur) kullanın.

- Olay KIMLIĞI 9005, bir sunucu uç noktası için geri çağırma güvenilirliği sağlar. Örneğin, erişilen toplam benzersiz dosya sayısı, başarısız erişimi olan toplam benzersiz dosya vb.
- Olay KIMLIĞI 9006, bir sunucu uç noktası için geri çağırma hatası dağıtımı sağlar. Örneğin, toplam başarısız Istek, hata kodu, vb. Her hata kodu için bir olay günlüğe kaydedilir.
- Olay KIMLIĞI 9009, bir sunucu uç noktası için geri çağırma oturumu bilgilerini sağlar. Örneğin, DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, vb.
- Olay KIMLIĞI 9059, bir sunucu uç noktası için uygulama geri çağırma dağıtımı sağlar. Örneğin, Shareıd, uygulama adı ve TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>Katman başarısız olan dosyalarla ilgili sorunları giderme
Dosyalar Azure dosyalarını katmanlamaz:

1. Olay Görüntüleyicisi, uygulamalar ve Services\microsoft\filesync\agentaltında bulunan telemetri, işletimsel ve Tanılama olay günlüklerini gözden geçirin. 
   1. Dosyaların Azure dosya paylaşımında mevcut olduğunu doğrulayın.

      > [!NOTE]
      > Bir dosya, katmanlanmadan önce bir Azure dosya paylaşımıyla eşitlenmelidir.

   2. Sunucunun internet bağlantısı olduğunu doğrulayın. 
   3. Azure Dosya Eşitleme filtresi sürücülerinin (Storagessync. sys ve StorageSyncGuard. sys) çalıştığını doğrulayın:
       - Yükseltilmiş bir komut isteminde `fltmc` ' ı çalıştırın. Storagessync. sys ve StorageSyncGuard. sys dosya sistemi filtre sürücülerinin listelendiğini doğrulayın.

> [!NOTE]
> Bir dosya katmanı (hata kodu başına bir olay günlüğe kaydedilir) başarısız olursa, telemetri olay günlüğünde bir saat sonra bir olay KIMLIĞI 9003 kaydedilir. Bir sorunu tanılamak için ek bilgiler gerekliyse, Işletimsel ve Tanılama olay günlükleri kullanılmalıdır.

### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Geri çağırılabilmesi gereken dosyalar nasıl giderilir?  
Dosyalar geri çekilemez:
1. Olay Görüntüleyicisi, uygulamalar ve Services\microsoft\filesync\agentaltında bulunan telemetri, işletimsel ve Tanılama olay günlüklerini gözden geçirin.
    1. Dosyaların Azure dosya paylaşımında mevcut olduğunu doğrulayın.
    2. Sunucunun internet bağlantısı olduğunu doğrulayın. 
    3. Hizmetler MMC ek bileşenini açın ve depolama eşitleme Aracısı hizmeti 'nin (FileSyncSvc) çalıştığını doğrulayın.
    4. Azure Dosya Eşitleme filtresi sürücülerinin (Storagessync. sys ve StorageSyncGuard. sys) çalıştığını doğrulayın:
        - Yükseltilmiş bir komut isteminde `fltmc` ' ı çalıştırın. Storagessync. sys ve StorageSyncGuard. sys dosya sistemi filtre sürücülerinin listelendiğini doğrulayın.

> [!NOTE]
> Bir dosya geri çağıramazsa (hata kodu başına bir olay günlüğe kaydedilir), telemetri olay günlüğünde bir olay KIMLIĞI 9006 saatte bir kez günlüğe kaydedilir. Hata kodu için düzeltme adımlarının listelenip listelenmediğini görmek için [geri çağırma hataları ve düzeltme](#recall-errors-and-remediation) bölümünü denetleyin.

### <a name="recall-errors-and-remediation"></a>Hataları ve düzeltmeyi geri çağır

| HRESULT | HRESULT (ondalık) | Hata dizesi | Sorun | Düzeltmesi |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -121 | ERROR_SEM_TIMEOUT | Bir g/ç zaman aşımı nedeniyle dosya geri çekemedi. Bu sorun çeşitli nedenlerden kaynaklanabilir: sunucu kaynak kısıtlamaları, zayıf ağ bağlantısı veya bir Azure depolama sorunu (örneğin, azaltma). | Eylem gerekmiyor. Hata birkaç saat devam ederse lütfen bir destek talebi açın. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | Ağ sorunu nedeniyle dosya geri çekemedi.  | Hata devam ederse, Azure dosya paylaşımının ağ bağlantısını kontrol edin. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | Sunucu uç noktası silindiğinden dosya geri çekemedi. | Bu sorunu çözmek için, [sunucu uç noktasını sildikten sonra, sunucuda katmanlı dosyalara erişilebilir değil ' a](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)bakın. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Erişim reddedildi hatası nedeniyle dosya geri çekemedi. Bu sorun, depolama hesabındaki güvenlik duvarı ve sanal ağ ayarları etkinleştirilmişse ve sunucunun depolama hesabına erişimi yoksa oluşabilir. | Bu sorunu çözmek için, dağıtım kılavuzu 'ndaki [güvenlik duvarı ve sanal ağ ayarlarını yapılandırma](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) bölümünde belgelenen adımları IZLEYEREK sunucu IP adresini veya sanal ağını ekleyin. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | Azure dosya paylaşımında erişilebilir olmadığından dosya geri çekemedi. | Bu sorunu çözmek için dosyanın Azure dosya paylaşımında bulunduğunu doğrulayın. Dosya Azure dosya paylaşımında varsa, en son Azure Dosya Eşitleme [aracı sürümüne](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions)yükseltin. |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | Depolama hesabına yönelik yetkilendirme hatası nedeniyle dosya geri çekemedi. | Bu sorunu çözmek için [Azure dosya eşitleme depolama hesabına erişiminin](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshoot-rbac)olduğunu doğrulayın. |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | Azure dosya paylaşımının erişimi olmadığından dosya geri çekemedi. | Dosya paylaşımının var olduğunu ve erişilebilir olduğunu doğrulayın. Dosya paylaşma silinmişse ve yeniden oluşturulduysa, eşitleme grubunu silip yeniden oluşturmak için [Azure dosya paylaşımının silindiği ve](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134375810) yeniden oluşturulduğu bir bölüm olduğundan eşitleme sırasında belgelenen adımları gerçekleştirin. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Yalıtılmış sistem kaynakları nedeniyle dosya geri çekemedi. | Hata devam ederse, sistem kaynaklarını hangi uygulamanın veya çekirdek modu sürücüsünün tüketdiünü araştırın. |
| 0x8007000E | -2147024882 | ERROR_OUTOFMEMORY | Yalıtılmış bellek nedeniyle dosya geri çekemedi. | Hata devam ederse, düşük bellek koşuluna hangi uygulamanın veya çekirdek modu sürücüsünün neden olduğunu araştırın. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Yetersiz disk alanı nedeniyle dosya geri çekemedi. | Bu sorunu çözmek için, dosyaları farklı bir birime taşıyarak birimde yer açın, birimin boyutunu artırın veya Invoke-Storagesynccloudtımpl cmdlet 'ini kullanarak dosyaları katmana zorlayın. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>Sunucu uç noktası silindikten sonra katmanlı dosyalara sunucuda erişilemiyor
Sunucu uç noktası silinmeden önce dosyalar geri çağrılmıyorsa, bir sunucudaki katmanlı dosyalar erişilemez duruma gelir.

Katmanlı dosyalar erişilebilir değilse günlüğe kaydedilen hatalar
- Bir dosya eşitlenirken, ıtemresults olay günlüğünde hata kodu-2147942467 (0x80070043-ERROR_BAD_NET_NAME) günlüğe kaydedilir
- Bir dosyayı geri çekerken, RecallResults olay günlüğünde hata kodu-2134376393 (0x80c80037-ECS_E_SYNC_SHARE_NOT_FOUND) günlüğe kaydedilir

Aşağıdaki koşullar karşılanıyorsa katmanlı dosyalarınıza erişimi geri yükleme işlemi mümkündür:
- Sunucu uç noktası son 30 gün içinde silindi
- Bulut uç noktası silinmedi 
- Dosya paylaşma silinmedi
- Eşitleme grubu silinmedi

Yukarıdaki koşullar karşılanıyorsa, sunucu uç noktasını 30 gün içinde aynı eşitleme grubu içindeki sunucuda aynı yolda yeniden oluşturarak sunucudaki dosyalara erişimi geri yükleyebilirsiniz. 

Yukarıdaki koşullar karşılanmazsa, sunucuda bu katmanlı dosyalar artık yalnız bırakılmış olduğundan erişimin geri yüklenmesi mümkün değildir. Yalnız bırakılmış katmanlı dosyaları kaldırmak için lütfen aşağıdaki yönergeleri izleyin.

**Notlar**
- Katmanlı dosyalara sunucuda erişilemezse, Azure dosya paylaşımında doğrudan erişmeniz durumunda tam dosya yine de erişilebilir olmalıdır.
- Artık katmanlı dosyaları daha sonra engellemek için sunucu uç noktası silinirken [sunucu uç noktasını kaldırma](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint) bölümünde belgelenen adımları izleyin.

<a id="get-orphaned"></a>**Yalnız bırakılmış katmanlı dosyaların listesini alma** 

1. Azure Dosya Eşitleme Agent sürümü v 5.1 veya sonraki bir sürümün yüklü olduğundan emin olun.
2. Yalnız bırakılmış katmanlı dosyaları listelemek için aşağıdaki PowerShell komutlarını çalıştırın:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Dosyaları silindikten sonra yedekten geri yüklenmesi gereken durumlarda, OrphanTieredFiles. txt çıkış dosyasını kaydedin.

<a id="remove-orphaned"></a>**Yalnız bırakılmış katmanlı dosyaları kaldırma** 

*Seçenek 1: yalnız bırakılmış katmanlı dosyaları sil*

Bu seçenek, Windows Server 'daki yalnız bırakılmış katmanlı dosyaları siler, ancak 30 gün sonra yeniden oluşturma veya farklı bir eşitleme grubuna bağlanma nedeniyle sunucu uç noktasının kaldırılmasını gerektirir. Sunucu uç noktası yeniden oluşturulmadan önce Windows Server veya Azure dosya paylaşımında dosyalar güncelleştirilirse dosya çakışmaları oluşur.

1. Azure Dosya Eşitleme Agent sürümü v 5.1 veya sonraki bir sürümün yüklü olduğundan emin olun.
2. Azure dosya paylaşımından ve sunucu uç noktası konumunu yedekleyin.
3. [Sunucu uç noktasını kaldırma](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint)bölümünde belgelenen adımları izleyerek eşitleme grubundaki sunucu uç noktasını (varsa) kaldırın.

> [!Warning]  
> Sunucu uç noktası Remove-Storagesyncorhayalet Edtieredfiles cmdlet 'i kullanılmadan önce kaldırılmıyorsa, sunucuda yalnız bırakılmış katmanlı dosyanın silinmesi, Azure dosya paylaşımındaki tam dosyayı siler. 

4. Yalnız bırakılmış katmanlı dosyaları listelemek için aşağıdaki PowerShell komutlarını çalıştırın:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Dosyaları silindikten sonra yedekten geri yüklenmesi gereken durumlarda, OrphanTieredFiles. txt çıkış dosyasını kaydedin.
6. Yalnız bırakılmış katmanlı dosyaları silmek için aşağıdaki PowerShell komutlarını çalıştırın:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Notlar** 
- Azure dosya paylaşımıyla eşitlenmemiş sunucuda değiştirilen katmanlı dosyalar silinir.
- Erişilebilir olan katmanlı dosyalar (sahipsiz değil) silinmez.
- Katmanlı olmayan dosyalar sunucuda kalır.

7. İsteğe bağlı: adım 3 ' te silinmişse sunucu uç noktasını yeniden oluşturun.

*2. seçenek: Azure dosya paylaşımından bağlama ve dosyaları sunucuda yalnız bırakılmış olan yerel olarak kopyalama*

Bu seçenek sunucu uç noktasının kaldırılmasını gerektirmez, ancak tam dosyaları yerel olarak kopyalamak için yeterli disk alanı gerektirir.

1. Azure dosya paylaşımından, yalnız bırakılmış katmanlı dosyalar içeren Windows Server 'a [bağlayın](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) .
2. Yalnız bırakılmış katmanlı dosyaları listelemek için aşağıdaki PowerShell komutlarını çalıştırın:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Sunucuda yalnız bırakılmış katmanlı dosyaları tanımlamak için OrphanTieredFiles. txt çıkış dosyasını kullanın.
4. Tam dosyayı Azure dosya paylaşımından Windows Server 'a kopyalayarak yalnız bırakılmış katmanlı dosyaların üzerine yazın.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Sunucuda beklenmedik şekilde geri çekilen dosyalar nasıl giderilir?  
Virüsten koruma, yedekleme ve çok sayıda dosyayı okuyan diğer uygulamalar, çevrimdışı özniteliği atla ve bu dosyaların içeriğini okumayı atlayarak istenmeyen geri çekenlere neden olur. Bu seçeneği destekleyen ürünler için çevrimdışı dosyaları atlamak, virüsten koruma taramaları veya yedekleme işleri gibi işlemler sırasında istenmeyen yeniden çağırma olaylarından kaçınılmasına yardımcı olabilir.

Çözümlerinin çevrimdışı dosyaları okumayı atlayacak şekilde yapılandırılması konusunda bilgi almak için yazılım satıcınızla iletişime geçin.

İstenmeyen geri çekmeler, dosya Gezgini 'nde dosyalara göz atarken olduğu gibi diğer senaryolarda da gerçekleşebilir. Dosya Gezgini'nde bulut katmanlı dosyalara sahip bir klasörün açılması, istenmeyen yeniden çağırma işlemlerine neden olabilir. Bu durumun gerçekleşme olasılığı, sunucuda virüsten koruma çözümünün etkinleştirilmiş olması halinde daha yüksektir.

> [!NOTE]
>Hangi uygulamaların geri çekme işlemine neden olduğunu öğrenmek için telemetri olay günlüğünde olay KIMLIĞI 9059 ' yı kullanın. Bu olay, bir sunucu uç noktası için uygulama geri çağırma dağılımı sağlar ve saatte bir kez günlüğe kaydedilir.

## <a name="general-troubleshooting"></a>Genel sorun giderme
Sunucuda Azure Dosya Eşitleme sorunlarla karşılaşırsanız, aşağıdaki adımları tamamlayarak başlatın:
1. Olay Görüntüleyicisi, telemetri, işletimsel ve Tanılama olay günlüklerini gözden geçirin.
    - Eşitleme, katmanlama ve geri çağırma sorunları, uygulamalar ve Services\microsoft\filesync\agentaltýnda telemetri, tanılama ve işletimsel olay günlüklerinde günlüğe kaydedilir.
    - Sunucu yönetimiyle ilgili sorunlar (örneğin, yapılandırma ayarları), uygulamalar ve Services\microsoft\filesync\managementaltındaki işletimsel ve Tanılama olay günlüklerine kaydedilir.
2. Sunucuda Azure Dosya Eşitleme hizmetinin çalıştığını doğrulayın:
    - Hizmetler MMC ek bileşenini açın ve depolama eşitleme Aracısı hizmeti 'nin (FileSyncSvc) çalıştığını doğrulayın.
3. Azure Dosya Eşitleme filtresi sürücülerinin (Storagessync. sys ve StorageSyncGuard. sys) çalıştığını doğrulayın:
    - Yükseltilmiş bir komut isteminde `fltmc` ' ı çalıştırın. Storagessync. sys ve StorageSyncGuard. sys dosya sistemi filtre sürücülerinin listelendiğini doğrulayın.

Sorun çözümlenmezse AFSDiag aracını çalıştırın:
1. AFSDiag çıkışının kaydedileceği bir dizin oluşturun (örneğin, C:\Output).
    > [!NOTE]
    >AFSDiag, günlükleri toplamadan önce çıkış dizinindeki tüm içeriği silecektir. Veri içermeyen bir çıkış konumu belirtin.
2. Yükseltilmiş bir PowerShell penceresi açın ve ardından aşağıdaki komutları çalıştırın (her komuttan sonra ENTER tuşuna basın):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Azure Dosya Eşitleme çekirdek modu izleme düzeyi için **1** girin (aksi belirtilmedikçe, daha ayrıntılı izlemeler oluşturmak için) ve ardından ENTER tuşuna basın.
4. Kullanıcı modu izleme düzeyi Azure Dosya Eşitleme için **1** girin (aksi belirtilmedikçe, daha ayrıntılı izlemeler oluşturmak için) ve ardından ENTER tuşuna basın.
5. Sorunu yeniden üretin. İşiniz bittiğinde **D**girin.
6. Günlükleri ve izleme dosyalarını içeren bir. zip dosyası belirttiğiniz çıkış dizinine kaydedilir.

## <a name="see-also"></a>Ayrıca bkz.
- [Azure Dosya Eşitleme’yi izleme](storage-sync-files-monitoring.md)
- [Azure dosyaları hakkında sık sorulan sorular](storage-files-faq.md)
- [Windows’ta Azure Dosyalar sorunlarını giderme](storage-troubleshoot-windows-file-connection-problems.md)
- [Linux’ta Azure Dosyalar sorunlarını giderme](storage-troubleshoot-linux-file-connection-problems.md)
