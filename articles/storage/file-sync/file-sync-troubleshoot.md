---
title: Sorun giderme Azure Dosya Eşitleme | Microsoft Docs
description: Windows Server 'ı Azure dosya paylaşımınızın hızlı önbelleğine dönüştürmek için kullanabileceğiniz Azure Dosya Eşitleme üzerindeki dağıtımdaki yaygın sorunları giderin.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 4/20/2021
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 386a95b46bd4787ea9ad2925ea1d2b2a0627a05e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797210"
---
# <a name="troubleshoot-azure-file-sync"></a>Azure Dosya Eşitleme ile ilgili sorunları giderme
Şirket içi bir dosya sunucusunun esnekliğini, performansını ve uyumluluğunu koruyarak kuruluşunuzun dosya paylaşımlarını Azure dosyalarında merkezileştirmek için Azure Dosya Eşitleme kullanın. Azure Dosya Eşitleme, Windows Server’ı Azure dosya paylaşımınızın hızlı bir önbelleğine dönüştürür. Verilere yerel olarak erişmek için Windows Server üzerinde kullanılabilen tüm protokolleri (SMB, NFS ve FTPS gibi) kullanabilirsiniz. Dünyanın dört bir yanında ihtiyacınız olan sayıda önbellekler olabilir.

Bu makale, Azure Dosya Eşitleme dağıtımınızda karşılaşabileceğiniz sorunları gidermenize ve çözmenize yardımcı olmak için tasarlanmıştır. Ayrıca, sorunla daha derin bir şekilde araştırılması durumunda sistemden önemli günlüklerin nasıl toplanacağını da anlatılmaktadır. Sorunuzun yanıtını görmüyorsanız, aşağıdaki kanallarla (yürüyen sırada) bizimle iletişim kurmanız gerekir:

- [Microsoft Q&Azure depolama Için bir soru sayfası](/answers/products/azure?product=storage).
- [Azure dosyaları UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
- Microsoft Desteği. Yeni bir destek isteği oluşturmak için, Azure portal **Yardım** sekmesinde **Yardım + Destek** düğmesini seçin ve ardından **Yeni destek isteği**' ni seçin.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Sunucum üzerinde Azure Dosya Eşitleme bir sorun yaşıyorum (eşitleme, bulut katmanlama vb.). Sunucu uç noktasını kaldırıp yeniden oluşturmanız gerekir mi?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Aracı yüklemesi ve sunucu kaydı
<a id="agent-installation-failures"></a>**Aracı yükleme hatalarında sorun giderme**  
Azure Dosya Eşitleme Aracısı yüklemesi başarısız olursa, yükseltilmiş bir komut isteminde, aracı yüklemesi sırasında günlüğü açmak için aşağıdaki komutu çalıştırın:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Yükleme hatasının nedenini öğrenmek için Installer. log dosyasına bakın.

<a id="agent-installation-gpo"></a>**Aracı yüklemesi hata vererek başarısız oldu: depolama eşitleme Aracısı Kurulum Sihirbazı bir hata nedeniyle erken sona erdi**

Aracı yükleme günlüğünde, aşağıdaki hata günlüğe kaydedilir:

```
CAQuietExec64:  + CategoryInfo          : SecurityError: (:) , PSSecurityException
CAQuietExec64:  + FullyQualifiedErrorId : UnauthorizedAccess
CAQuietExec64:  Error 0x80070001: Command line returned an error.
```

Bu sorun, [PowerShell yürütme ilkesi](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies#use-group-policy-to-manage-execution-policy) Grup İlkesi kullanılarak yapılandırılmışsa ve ilke ayarı "yalnızca imzalı betiklerine izin ver" ise oluşur. Azure Dosya Eşitleme aracısına dahil edilen tüm betikler imzalanır. Yükleyici, yürütme ilkesini atla ilke ayarını kullanarak betik yürütmeyi gerçekleştirdiğinden Azure Dosya Eşitleme Aracısı yüklemesi başarısız olur.

Bu sorunu çözmek için, sunucuda [betik yürütmeyi aç](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies#use-group-policy-to-manage-execution-policy) Grup İlkesi ayarını geçici olarak devre dışı bırakın. Aracı yüklemesi tamamlandıktan sonra, Grup İlkesi ayarı yeniden etkinleştirilebilir.

<a id="agent-installation-on-DC"></a>**Active Directory Etki Alanı denetleyicisinde aracı yüklemesi başarısız oluyor**  
Bir Active Directory etki alanı denetleyicisine, PDC rolü sahibinin bir Windows Server 2008 R2 veya altında işletim sistemi sürümü olan bir etki alanı denetleyicisine yüklemeye çalışırsanız, eşitleme aracısının yüklenememe sorununa ulaşırsınız.

Bu sorunu gidermek için, PDC rolünü Windows Server 2012 R2 veya daha yeni bir sürümünü çalıştıran başka bir etki alanı denetleyicisine aktarın ve ardından eşitleme 'yi çalıştırın.

<a id="parameter-is-incorrect"></a>**Windows Server 2012 R2 'deki bir birime erişilmesi hata vererek başarısız olur: parametre yanlış**  
Windows Server 2012 R2 'de sunucu uç noktası oluşturduktan sonra, birime erişirken aşağıdaki hata oluşur:

SürücüHarfi: \ erişilebilir değil.  
Parametre yanlış.

Bu sorunu çözmek için [KB2919355](https://support.microsoft.com/help/2919355/windows-rt-8-1-windows-8-1-windows-server-2012-r2-update-april-2014) yükleyip sunucuyu yeniden başlatın. Daha sonraki bir güncelleştirme zaten yüklü olduğu için bu güncelleştirme yüklenmeyecektir Windows Update gidin, Windows Server 2012 R2 için en son güncelleştirmeleri yükleyip sunucuyu yeniden başlatın.

<a id="server-registration-missing-subscriptions"></a>**Sunucu kaydı tüm Azure aboneliklerini listelemez**  
ServerRegistration.exe kullanarak bir sunucuyu kaydederken, Azure aboneliği açılır listesini tıklattığınızda abonelikler eksiktir.

Bu sorun, ServerRegistration.exe yalnızca ilk 5 Azure AD kiracısından abonelik alacağından oluşur. 

Sunucuda sunucu kaydı kiracı sınırını artırmak için, 5 ' ten büyük bir değer ile HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync altında ServerRegistrationTenantLimit adlı bir DWORD değeri oluşturun.

Ayrıca, sunucuyu kaydetmek için aşağıdaki PowerShell komutlarını kullanarak bu sorunu geçici olarak bulabilirsiniz:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**Sunucu kaydı şu iletiyi görüntüler: "önkoşulların önkoşulları eksik"**  
Bu ileti, PowerShell 5,1 ' de az veya Azurerd PowerShell modülü yüklü değilse görüntülenir. 

> [!Note]  
> ServerRegistration.exe PowerShell 6. x desteklemez. Sunucuyu kaydetmek için PowerShell 6. x üzerinde Register-AzStorageSyncServer cmdlet 'ini kullanabilirsiniz.

PowerShell 5,1 ' ye az veya Azurerd modülünü yüklemek için aşağıdaki adımları uygulayın:

1. Yükseltilmiş bir komut isteminden **PowerShell** yazın ve ENTER tuşuna basın.
2. Belgeleri izleyerek en son az veya Azurerd modülünü yükler:
    - [Az Module (.NET 4.7.2 gerektirir)](/powershell/azure/install-az-ps)
    - [AzureRM modülü](https://go.microsoft.com/fwlink/?linkid=856959)
3. ServerRegistration.exe çalıştırın ve sunucuyu bir depolama Eşitleme hizmetine kaydetmek için Sihirbazı doldurun.

<a id="server-already-registered"></a>**Sunucu kaydı şu iletiyi görüntüler: "Bu sunucu zaten kayıtlı"** 

!["Sunucu zaten kayıtlı" hata iletisiyle sunucu kayıt iletişim kutusunun ekran görüntüsü](media/storage-sync-files-troubleshoot/server-registration-1.png)

Sunucu daha önce bir depolama eşitleme hizmeti ile kaydedilmişse bu ileti görüntülenir. Sunucunun geçerli depolama eşitleme hizmetinden kaydını silip yeni bir depolama eşitleme hizmeti ile kaydolacak şekilde, [Azure dosya eşitleme ile sunucu kaydını silme](file-sync-server-registration.md#unregister-the-server-with-storage-sync-service)bölümünde açıklanan adımları uygulayın.

Sunucu, depolama eşitleme hizmeti 'nde **kayıtlı sunucular** altında listelenmiyorsa, kaydını kaldırmak istediğiniz sunucuda aşağıdaki PowerShell komutlarını çalıştırın:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Sunucu bir kümenin parçasıysa, küme kaydını da kaldırmak için isteğe bağlı *sıfırlama-StorageSyncServer-CleanClusterRegistration* parametresini kullanabilirsiniz.

<a id="web-site-not-trusted"></a>**Bir sunucuyu kaydettiğimde, çok sayıda "Web sitesi güvenilir değil" yanıtı görüyorum. Kaydol?**  
Bu sorun, sunucu kaydı sırasında **geliştirilmiş Internet Explorer güvenlik** ilkesi etkinleştirildiğinde oluşur. **Gelişmiş Internet Explorer güvenlik** ilkesini doğru şekilde devre dışı bırakma hakkında daha fazla bilgi için bkz. [Windows Server 'ı Azure dosya eşitleme Ile kullanmak üzere hazırlama](file-sync-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) ve [Azure dosya eşitleme dağıtma](file-sync-deployment-guide.md).

<a id="server-registration-missing"></a>**Sunucu, Azure portal kayıtlı sunucular altında listelenmiyor**  
Bir sunucu, bir depolama eşitleme hizmeti için **kayıtlı sunucular** altında listelenmiyorsa:
1. Kaydetmek istediğiniz sunucuda oturum açın.
2. Dosya Gezgini 'ni açın ve ardından depolama eşitleme Aracısı yükleme dizinine gidin (varsayılan konum C:\Program Files\Azure\StorageSyncAgent ' dir.) 
3. ServerRegistration.exe çalıştırın ve sunucuyu bir depolama Eşitleme hizmetine kaydetmek için Sihirbazı doldurun.

## <a name="sync-group-management"></a>Eşitleme grubu yönetimi

### <a name="cloud-endpoint-creation-errors"></a>Bulut uç noktası oluşturma hataları

<a id="cloud-endpoint-using-share"></a>**Bulut uç noktası oluşturma işlemi şu hatayla başarısız oluyor: "belirtilen Azure FileShare farklı bir CloudEndpoint tarafından zaten kullanılıyor"**  
Azure dosya paylaşımı zaten başka bir bulut uç noktasının kullanımında olduğunda bu hata oluşur. 

Bu iletiyi görürseniz ve Azure dosya paylaşma Şu anda bir bulut uç noktası tarafından kullanımda değilse, Azure dosya paylaşımında Azure Dosya Eşitleme meta verileri temizlemek için aşağıdaki adımları izleyin:

> [!Warning]  
> Şu anda bir bulut uç noktası tarafından kullanılan bir Azure dosya paylaşımındaki meta verilerin silinmesi Azure Dosya Eşitleme işlemlerin başarısız olmasına neden olur. Daha sonra bu dosya paylaşımından farklı bir eşitleme grubunda eşitleme için kullanıyorsanız, eski eşitleme grubundaki dosyalar için veri kaybı neredeyse belli olur.

1. Azure portal Azure dosya paylaşımınıza gidin.  
2. Azure dosya paylaşımında sağ tıklayın ve ardından **meta verileri Düzenle**' yi seçin.
3. **SyncService** öğesine sağ tıklayın ve ardından **Sil**' i seçin.

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

### <a name="server-endpoint-creation-and-deletion-errors"></a>Sunucu uç noktası oluşturma ve silme hataları

<a id="-2134375898"></a>**Sunucu uç noktası oluşturma işlemi şu hatayla başarısız oldu: "MgmtServerJobFailed" (hata kodu:-2134375898 veya 0x80c80226)**  
Sunucu uç noktası yolu sistem birimindeyse ve bulut katmanlama etkinleştirildiyse bu hata oluşur. Sistem biriminde bulut katmanlama desteklenmez. Sistem biriminde sunucu uç noktası oluşturmak için, sunucu uç noktasını oluştururken bulutta katmanlamayı devre dışı bırakın.

<a id="-2147024894"></a>**Sunucu uç noktası oluşturma işlemi şu hata ile başarısız oluyor: "MgmtServerJobFailed" (hata kodu:-2147024894 veya 0x80070002)**  
Belirtilen sunucu uç noktası yolu geçerli değilse bu hata oluşur. Belirtilen sunucu uç noktası yolunun yerel olarak eklenmiş bir NTFS birimi olduğunu doğrulayın. Unutmayın, Azure Dosya Eşitleme sunucu uç noktası yolu olarak eşlenmiş sürücüleri desteklemez.

<a id="-2134375640"></a>**Sunucu uç noktası oluşturma işlemi şu hatayla başarısız oldu: "MgmtServerJobFailed" (hata kodu:-2134375640 veya 0x80c80328)**  
Belirtilen sunucu uç noktası yolu bir NTFS birimi değilse bu hata oluşur. Belirtilen sunucu uç noktası yolunun yerel olarak eklenmiş bir NTFS birimi olduğunu doğrulayın. Unutmayın, Azure Dosya Eşitleme sunucu uç noktası yolu olarak eşlenmiş sürücüleri desteklemez.

<a id="-2134347507"></a>**Sunucu uç noktası oluşturma işlemi şu hatayla başarısız oldu: "MgmtServerJobFailed" (hata kodu:-2134347507 veya 0x80c8710d)**  
Azure Dosya Eşitleme, sıkıştırılmış Sistem Birim Bilgisi klasörü bulunan birimlerde sunucu uç noktalarını desteklemediğinden bu hata oluşur. Bu sorunu çözmek için Sistem Birim Bilgisi klasörünün sıkıştırmasını açın. Sistem Birim Bilgisi klasörü birimde sıkıştırılmış durumdaki tek klasörse aşağıdaki adımları izleyin:

1. [PsExec](/sysinternals/downloads/psexec) aracını indirin.
2. Sistem hesabı altında çalışan bir komut istemi başlatmak için yükseltilmiş bir komut isteminden aşağıdaki komutu çalıştırın: **PsExec.exe-i-s-d cmd**
3. Sistem hesabı altında çalışan komut isteminde şu komutu yazın ve Enter tuşuna basın:   
    **CD/d "sürücü harfi: \ sistem birimi bilgileri"**  
    **Compact/u/s**

<a id="-2134376345"></a>**Sunucu uç noktası oluşturma işlemi şu hatayla başarısız oldu: "MgmtServerJobFailed" (hata kodu:-2134376345 veya 0x80C80067)**  
Sunucu başına sunucu uç noktası sınırına ulaşılırsa bu hata oluşur. Azure Dosya Eşitleme şu anda sunucu başına en çok 30 sunucu uç noktasını destekler. Daha fazla bilgi için bkz. [Azure dosya eşitleme ölçek hedefleri](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#azure-file-sync-scale-targets).

<a id="-2134376427"></a>**Sunucu uç noktası oluşturma işlemi şu hatayla başarısız oldu: "MgmtServerJobFailed" (hata kodu:-2134376427 veya 0x80c80015)**  
Belirtilen sunucu uç noktası yolunu başka bir sunucu uç noktası zaten eşitliyorsa bu hata oluşur. Azure Dosya Eşitleme birden çok sunucu uç noktasının aynı dizini veya birimi eşitlemesini desteklemez.

<a id="-2160590967"></a>**Sunucu uç noktası oluşturma işlemi şu hatayla başarısız oldu: "MgmtServerJobFailed" (hata kodu:-2160590967 veya 0x80c80077)**  
Sunucu uç noktası yolu yalnız bırakılmış katmanlı dosyalar içeriyorsa bu hata oluşur. Kısa süre önce bir sunucu uç noktası kaldırıldıysa, yalnız bırakılmış katmanlı dosyaları temizleme işleminin tamamlanmasını bekleyin. Yalnız bırakılmış katmanlı dosyalar temizliği başladıktan sonra telemetri olay günlüğüne olay KIMLIĞI 6662 kaydedilir. Bir olay KIMLIĞI 6661, yalnız bırakılmış katmanlı dosyalar temizliği tamamlandığında günlüğe kaydedilir ve bir sunucu uç noktası yol kullanılarak yeniden oluşturulabilir. Katmanlı dosyalar Temizleme işlemi tamamlandıktan sonra sunucu uç noktası oluşturma işlemi başarısız olursa veya olay günlüğü geçişi nedeniyle telemetri olay günlüğünde olay KIMLIĞI 6661 bulunamazsa, [sunucu uç noktası bölümü silindikten sonra katmanlı dosyalarda](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) belgelenen adımları gerçekleştirerek, yalnız bırakılmış katmanlı dosyaları kaldırın.

<a id="-2134347757"></a>**Sunucu uç noktası silme işlemi şu hatayla başarısız oluyor: "Mgmtserverjobendpoint" (hata kodu:-2134347757 veya 0x80c87013)**  
Sunucu çevrimdışıysa veya ağ bağlantısı yoksa bu hata oluşur. Sunucu artık kullanılabilir değilse portalda sunucunun kaydını kaldırın; bu işlem sunucu uç noktalarını siler. Sunucu uç noktalarını silmek için [Azure dosya eşitleme ile sunucu kaydını](file-sync-server-registration.md#unregister-the-server-with-storage-sync-service)silme bölümünde açıklanan adımları izleyin.

### <a name="server-endpoint-health"></a>Sunucu uç noktası sistem durumu

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

Bu sorun, depolama eşitleme Izleyicisi işlemi (AzureStorageSyncMonitor.exe) çalışmıyorsa veya sunucu Azure Dosya Eşitleme hizmetine erişemediğinden ortaya çıkabilir.

Portalda "çevrimdışı görünüyor" olarak gösterilen sunucuda, sunucunun neden Azure Dosya Eşitleme hizmetine erişemediğini öğrenmek için telemetri olay günlüğündeki (Olay Görüntüleyicisi uygulamalar ve Services\Microsoft\FileSync\Agent altında bulunur) olay KIMLIĞI 9301 ' a bakın. 

- **Getnextjob şu durumla tamamlanırsa: 0** günlüğe kaydedilir, sunucu Azure dosya eşitleme hizmetiyle iletişim kurabilir. 
    - Sunucuda Görev Yöneticisi'ni açın ve Depolama Eşitleme İzleyicisi (AzureStorageSyncMonitor.exe) işleminin çalıştığından emin olun. İşlem çalışmıyorsa önce sunucuyu yeniden başlatmayı deneyin. Sunucunun yeniden başlatılması sorunu çözmezse en son Azure Dosya Eşitleme [aracısı sürümüne](file-sync-release-notes.md) yükseltin. 

- **Getnextjob şu durum ile tamamlanırsa:-2134347756** günlüğe kaydedilir, bir güvenlik duvarı, proxy veya TLS şifre paketi sırası yapılandırması nedeniyle sunucu Azure dosya eşitleme hizmetiyle iletişim kuramaz. 
    - Sunucu bir güvenlik duvarının arkasındaysa 443 numaralı bağlantı noktası üzerinden giden bağlantılara izin verildiğinden emin olun. Güvenlik Duvarı trafiği belirli etki alanlarıyla kısıtlarsa, güvenlik duvarı [belgelerinde](file-sync-firewall-and-proxy.md#firewall) listelenen etki alanlarının erişilebilir olduğunu doğrulayın.
    - Sunucu bir proxy 'nin arkasındaysa, proxy [belgelerindeki](file-sync-firewall-and-proxy.md#proxy)adımları izleyerek makine genelinde veya uygulamaya özel proxy ayarlarını yapılandırın.
    - Hizmet uç noktalarına ağ bağlantısını denetlemek için Test-StorageSyncNetworkConnectivity cmdlet 'ini kullanın. Daha fazla bilgi için bkz. [hizmet uç noktalarına ağ bağlantısını test](file-sync-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints)etme.
    - Sunucuda TLS şifre paketi sırası yapılandırılmışsa, şifre paketleri eklemek için Grup İlkesi veya TLS cmdlet 'leri kullanabilirsiniz:
        - Grup İlkesi 'ni kullanmak için, bkz. [Grup İlkesi kullanarak TLS şifre paketi sırasını yapılandırma](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-group-policy).
        - TLS cmdlet 'lerini kullanmak için TLS [PowerShell cmdlet 'lerini kullanarak TLS şifre paketi sırasını yapılandırma](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-tls-powershell-cmdlets)konusuna bakın.
    
        Azure Dosya Eşitleme Şu anda TLS 1,2 Protokolü için aşağıdaki şifre paketlerini desteklemektedir:  
        - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
        - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256  

- **Getnextjob şu durumla tamamlanırsa:-2134347764** günlüğe kaydedilir, sunucu, süresi dolan veya silinen bir sertifika nedeniyle Azure dosya eşitleme hizmetiyle iletişim kuramaz.  
    - Kimlik doğrulama için kullanılan sertifikayı sıfırlamak için sunucuda aşağıdaki PowerShell komutunu çalıştırın:
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**Sunucu uç noktası "etkinlik yok" durumuna sahip ve kayıtlı sunucular dikey penceresindeki sunucu durumu "çevrimiçi"**  

Sunucu uç noktasının sistem durumunun "Etkinlik Yok" olması, sunucu uç noktasının son iki saat içinde eşitleme etkinliği kaydetmediğini gösterir.

Bir sunucudaki geçerli eşitleme etkinliğini denetlemek için, [geçerli bir eşitleme oturumunun ilerlemesini nasıl yaparım? izleme](#how-do-i-monitor-the-progress-of-a-current-sync-session)bölümüne bakın.

Sunucu uç noktası, bir hata veya yetersiz sistem kaynakları nedeniyle eşitleme etkinliğini birkaç saat boyunca günlüğe içermemelidir. En son Azure Dosya Eşitleme [Aracısı sürümünün](file-sync-release-notes.md) yüklü olduğunu doğrulayın. Sorun devam ederse bir destek isteği açın.

> [!Note]  
> Kayıtlı sunucular dikey penceresindeki sunucu durumu "çevrimdışı" ise "sunucu uç noktasında belgelenen adımları gerçekleştirmek için [" hiçbir etkinlik yok "veya" beklemede "sistem durumu ve kayıtlı sunucular dikey penceresinde sunucu durumu" çevrimdışı görünüyor "](#server-endpoint-noactivity) bölümünde verilmiştir.

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**SMB veya Portal üzerinden doğrudan Azure dosya paylaşımım içinde bir dosya oluşturdum, dosyanın eşitleme grubundaki sunucularla eşitlenmesi ne kadar sürer?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**Sunucu uç noktası sistem durumu, birkaç saat boyunca bekleme durumunda**  
Bu sorun, bir bulut uç noktası oluşturup veri içeren bir Azure dosya paylaşımının kullanılması halinde beklenmektedir. Azure dosya paylaşımındaki değişiklikleri tarayan değişiklik numaralandırma işi, dosyaların bulut ve sunucu uç noktaları arasında eşitlenebilmesi için tamamlanmalıdır. İşi tamamlanma süresi, Azure dosya paylaşımındaki ad alanının boyutuna bağlıdır. Değişiklik numaralandırması işi tamamlandıktan sonra sunucu uç noktası durumunun güncelleştirilmesi gerekir.

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>Nasıl yaparım? eşitleme durumu İzmi?
# <a name="portal"></a>[Portal](#tab/portal1)
Her bir eşitleme grubunda, son tamamlanan eşitleme oturumlarının durumunu görmek için bireysel sunucu uç noktalarında ayrıntıya gidebilirsiniz. Yeşil bir sistem durumu sütunu ve 0 değerini eşitlemeden bir dosya eşitlemenin beklendiği gibi çalıştığını gösterir. Böyle bir durum söz konusu değilse, yaygın eşitleme hatalarının listesi ve eşitlenmemiş dosyaların nasıl işleneceği hakkında bilgi için aşağıya bakın. 

![Azure portal ekran görüntüsü](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[Sunucu](#tab/server)
Üzerinde Olay Görüntüleyicisi bulunan sunucunun telemetri günlüklerine gidin `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` . Olay 9102, tamamlanan bir eşitleme oturumuna karşılık geliyor; eşitlemenin en son durumu için 9102 KIMLIKLI en son olayı arayın. SyncDirection, bu oturumun karşıya yükleme veya indirme olup olmadığını söyler. HResult 0 ise, eşitleme oturumu başarılı olmuştur. Sıfır olmayan bir HResult, eşitleme sırasında bir hata olduğu anlamına gelir; yaygın hataların listesi için aşağıya bakın. PerItemErrorCount değeri 0 ' dan büyükse bu, bazı dosya veya klasörlerin düzgün şekilde eşitlenmediği anlamına gelir. 0 ' dan büyük olan bir PerItemErrorCount değeri 0 ' dan büyük bir HResult olması mümkündür.

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

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Geçerli eşitleme oturumunun ilerleme durumunu nasıl izleyebilirim?
# <a name="portal"></a>[Portal](#tab/portal1)
Eşitleme grubunuz içinde, söz konusu sunucu uç noktasına gidin ve geçerli eşitleme oturumunda karşıya yüklenen veya indirilen dosya sayısını görmek için eşitleme etkinliği bölümüne bakın. Bu oturumun yaklaşık 5 dakika gecikmeli olduğunu unutmayın. Eşitleme oturumunuz bu süre içinde tamamlanabilecek kadar küçükse portalda gösterilmeyebilir. 

# <a name="server"></a>[Sunucu](#tab/server)
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

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Sunucularımın eşitlenmiş olup olmadığını nasıl anlayabilirim?
# <a name="portal"></a>[Portal](#tab/portal1)
Belirli bir eşitleme grubundaki her bir sunucu için şunları yaptığınızdan emin olun:
- Hem karşıya yükleme hem de indirme için denenen son eşitlemeye ilişkin zaman damgaları son tarih.
- Durum hem karşıya yükleme hem de indirme için yeşil olur.
- Eşitleme etkinliği alanı, eşitlenmek üzere kalan çok az veya hiç dosya gösterir.
- Dosya eşitlenmiyor alanı, hem karşıya yükleme hem de indirme için 0 ' dır.

# <a name="server"></a>[Sunucu](#tab/server)
Her sunucu için telemetri olay günlüğünde 9102 olay tarafından işaretlenen tamamlanan eşitleme oturumlarına bakın (Olay Görüntüleyicisi, ' a gidin `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` ). 

1. Belirli bir sunucuda, en son karşıya yükleme ve indirme oturumlarının başarıyla tamamlandığından emin olmak istersiniz. Bunu yapmak için, HResult ve PerItemErrorCount değerinin her ikisi de karşıya yükleme ve indirme için 0 olup olmadığını denetleyin (SyncDirection alanı, belirli bir oturumun karşıya yükleme veya indirme oturumu olup olmadığını gösterir). Yakın zamanda tamamlanan bir eşitleme oturumu görmüyorsanız, büyük miktarda veri eklediyseniz veya değişiklik yaptıysanız beklenmekte olan bir eşitleme oturumunun devam ettiğini unutmayın.
2. Bir sunucu buluta tamamen güncellecektir ve her iki yönde de eşitleme değişikliği olmadığında, boş eşitleme oturumları görürsünüz. Bunlar, tüm Sync * alanları (SyncFileCount, SyncDirCount, SyncTombstoneCount ve SyncSizeBytes) sıfır olduğu anlamına gelen karşıya yükleme ve indirme olayları tarafından belirtilir, ancak eşitleme için hiçbir şey yoktur. Eşitlenmesi gereken her zaman yeni bir şey olduğundan, bu boş eşitleme oturumlarının yüksek dalgalanma sunucularında gerçekleşmediğini unutmayın. Eşitleme etkinliği yoksa, her 30 dakikada bir gerçekleşmelidir. 
3. Tüm sunucular bulutla güncel ise, en son karşıya yükleme ve indirme oturumları boş eşitleme oturumlarsa da, sistemin bir bütün olarak eşitlenmiş olduğu kesin bir belirsizlik ile söyleyebilirsiniz. 
    
Azure dosya paylaşımınızda değişiklik yaptıysanız Azure Dosya Eşitleme, 24 saatte bir kez gerçekleşen değişiklik numaralandırması tamamlanana kadar bu değişikliği algılamayacağını unutmayın. Bir sunucu, Azure dosya paylaşımında doğrudan yapılan son değişiklikler eksik olduğunda bulut ile güncel olduğunu söyler. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Belirli dosya veya klasörlerin eşitlenip eşitlenmediğini nasıl görebilirim?
Sunucu üzerinde PerItemErrorCount veya portalda eşitlenmemiş dosyalar, belirli bir eşitleme oturumunda 0 ' dan büyükse, bazı öğelerin eşitlenmesi başarısız oluyor demektir. Dosya ve klasörlerin eşitlenmesine engel olan özellikler olabilir. Bu özellikler kalıcı olabilir ve eşitlemeyi sürdürmeye yönelik açık eylem gerektirebilir, örneğin, dosya veya klasör adından desteklenmeyen karakterleri kaldırır. Bunlar da geçici olabilir. Bu, dosya veya klasörün otomatik olarak eşitlemeyi sürdürmesini belirtir; Örneğin, dosya kapatıldığında açık tanıtıcıların bulunduğu dosyalar otomatik olarak eşitlemeye sürdürülür. Azure Dosya Eşitleme altyapısı böyle bir sorunu algıladığında, şu anda doğru şekilde eşitlenmeyen öğeleri listelemek için ayrıştırılabilen bir hata günlüğü oluşturulur.

Bu hataları görmek için, açık tanıtıcılar, desteklenmeyen karakterler veya diğer sorunlar nedeniyle eşitleme başarısız olan dosyaları belirlemek üzere **FileSyncErrorsReport.ps1** PowerShell betiğini (Azure dosya eşitleme aracısının aracı yükleme dizininde bulunur) çalıştırın. ItemPath alanı, kök eşitleme diziniyle ilişkili olarak dosyanın konumunu söyler. Düzeltme adımları için aşağıdaki yaygın eşitleme hatalarının listesine bakın.

> [!Note]  
> FileSyncErrorsReport.ps1 betiği "dosya bulunamadı" hatası döndürürse ya da eşitleme grubu için öğe başına hataları listelemez, nedeni şu olur:
>
>- Neden 1: son tamamlanan eşitleme oturumunda öğe başına hatalar yoktu. Portalın, eşitlenmeyen 0 dosya göstermesi için yakında güncelleştirilmiş olması gerekir. 
>    - PerItemErrorCount değerinin 0 olduğunu doğrulamak için telemetri olay günlüğündeki [olay kimliği 9102](?tabs=server%252cazure-portal#broken-sync) ' yı denetleyin. 
>
>- Neden 2: öğe başına çok fazla hata ve olay günlüğü artık bu eşitleme grubu için hata içerdiğinden, sunucuda bulunan ıtemresults olay günlüğü.
>    - Bu sorunu engellemek için, ıtemresults olay günlüğü boyutunu artırın. Itemresults olay günlüğü, Olay Görüntüleyicisi içinde "uygulamalar ve hizmetler Logs\Microsoft\FileSync\Agent" altında bulunabilir. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Dosya/Dizin Eşitleme hataları başına sorun giderme
**Itemresults-öğe başına Eşitleme hataları**  

| HRESULT | HRESULT (ondalık) | Hata dizesi | Sorun | Düzeltme |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | Sunucudaki katmanlı dosya erişilebilir değil. Sunucu uç noktası silinmeden önce katmanlanmış dosya geri çağrılmıyorsa bu hata oluşur. | Bu sorunu çözmek için, [sunucu uç noktasını sildikten sonra, sunucuda katmanlı dosyalara erişilebilir değil ' a](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)bakın. |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | Bağımlı bir klasör henüz eşitlenmediği için dosya veya dizin değişikliği henüz eşitlenemiyor. Bu öğe, bağımlı değişiklikler eşitlendikten sonra eşitlenecek. | Eylem gerekmiyor. Hata birkaç gün boyunca devam ederse, bağımlı klasörün neden henüz eşitlenmemiş olduğunu anlamak için FileSyncErrorsReport.ps1 PowerShell betiğini kullanın. |
| 0x80C8028A | -2134375798 | ECS_E_SYNC_CONSTRAINT_CONFLICT_ON_FAILED_DEPENDEE | Bağımlı bir klasör henüz eşitlenmediği için dosya veya dizin değişikliği henüz eşitlenemiyor. Bu öğe, bağımlı değişiklikler eşitlendikten sonra eşitlenecek. | Eylem gerekmiyor. Hata birkaç gün boyunca devam ederse, bağımlı klasörün neden henüz eşitlenmemiş olduğunu anlamak için FileSyncErrorsReport.ps1 PowerShell betiğini kullanın. |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | Bağımlı bir klasör henüz eşitlenmemiş ve eşitleme oturumu başarısız olduğu için dosya veya dizin değişikliği henüz eşitlenemiyor. Bu öğe, bağımlı değişiklikler eşitlendikten sonra eşitlenecek. | Eylem gerekmiyor. Hata devam ederse, eşitleme oturumu başarısızlığını araştırın. |
| 0x8007007B | -2147024773 | ERROR_INVALID_NAME | Dosya veya dizin adı geçersiz. | Söz konusu dosyayı veya dizini yeniden adlandırın. Daha fazla bilgi için [Desteklenmeyen karakterleri işleme](?tabs=portal1%252cazure-portal#handling-unsupported-characters) bölümüne bakın. |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | Dosya veya dizin adı geçersiz. | Söz konusu dosyayı veya dizini yeniden adlandırın. Daha fazla bilgi için [Desteklenmeyen karakterleri işleme](?tabs=portal1%252cazure-portal#handling-unsupported-characters) bölümüne bakın. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Dosya kullanımda olduğundan eşitlenemiyor. Dosya artık kullanımda olmadığında eşitlenir. | Eylem gerekmiyor. Azure Dosya Eşitleme, açık tanıtıcıların bulunduğu dosyaları eşitlemek için sunucuda günde bir kez geçici bir VSS anlık görüntüsü oluşturur. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Dosya değişti, ancak değişiklik eşitleme tarafından henüz algılanmadı. Eşitleme, bu değişiklik algılandıktan sonra kurtarılacak. | Eylem gerekmiyor. |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | Dosya silindi ve eşitleme değişikliğin farkında değildir. | Eylem gerekmiyor. Değişiklik algılama, dosyanın silindiğini algıladığında, eşitleme bu hatayı günlüğe kaydetmeyi durduracak. |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | Öğe hedefte zaten silindiğinden ve eşitleme değişikliğin farkında olmadığından, bir dosyanın veya dizinin silinmesi eşitlenemiyor. | Eylem gerekmiyor. Eşitleme, hedefte değişiklik algılama çalıştırmaları yapıldıktan sonra bu hatayı günlüğe kaydetmeyi durduracak ve eşitleme öğenin silindiğini algılıyor. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | Dosya veya dizin atlandı, ancak sonraki eşitleme oturumu sırasında eşitlenecek. Öğe indirilirken Bu hata bildirilirse, dosya veya dizin adı büyük olasılıkla geçersiz olur. | Dosya karşıya yüklenirken bu hata bildirilmezse hiçbir eylem gerekmez. Dosya indirilirken hata bildirilirse, söz konusu dosyayı veya dizini yeniden adlandırın. Daha fazla bilgi için [Desteklenmeyen karakterleri işleme](?tabs=portal1%252cazure-portal#handling-unsupported-characters) bölümüne bakın. |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | Öğe hedefte zaten varolduğundan ve eşitleme değişikliğin farkında olmadığından, bir dosya veya dizin oluşturma eşitlenemiyor. | Eylem gerekmiyor. Hedefte değişiklik algılaması çalıştırıldıktan ve eşitleme bu yeni öğenin bilgisine sahip olduktan sonra, eşitleme bu hatanın günlüğe kaydedilmesini durduracaktır. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Azure dosya paylaşımı sınırına ulaşıldığından dosya eşitlenemiyor. | Bu sorunu çözmek için sorun giderme kılavuzundaki [Azure dosya paylaşma depolama sınırı bölümüne ulaştınız](?tabs=portal1%252cazure-portal#-2134351810) . |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | Dosya, DFS-R salt okunurdur çoğaltma klasöründe bulunur. | Dosya, DFS-R salt okunurdur çoğaltma klasöründe bulunur. Azure Dosya Eşitleme DFS-R salt okunur çoğaltma klasörlerindeki sunucu uç noktalarını desteklemez. Daha fazla bilgi için [planlama kılavuzuna](file-sync-planning.md#distributed-file-system-dfs) bakın. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Dosyada silme beklemede durumu vardır. | Eylem gerekmiyor. Dosya, tüm açık dosya tutamaçları kapatıldıktan sonra silinir. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | Depolama hesabındaki güvenlik duvarı ve sanal ağ ayarları etkinleştirildiğinden ve sunucunun depolama hesabına erişimi olmadığından dosya eşitlenemiyor. | Dağıtım kılavuzundaki [güvenlik duvarı ve sanal ağ ayarlarını yapılandırma](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) bölümünde belgelenen adımları IZLEYEREK sunucu IP adresini veya sanal ağını ekleyin. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | Güvenlik tanımlayıcı boyutu 64 KiB sınırını aştığından dosya eşitlenemiyor. | Bu sorunu çözmek için dosyadaki erişim denetimi girdilerini (ACE) kaldırarak güvenlik açıklayıcısının boyutunu küçültün. |
| 0x8000FFFF | -2147418113 | E_UNEXPECTED | Beklenmeyen bir hata nedeniyle dosya eşitlenemiyor. | Birkaç gün boyunca hata devam ederse lütfen bir destek talebi açın. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | Dosya kullanımda olduğundan eşitlenemiyor. Dosya artık kullanımda olmadığında eşitlenir. | Eylem gerekmiyor. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Dosya eşitleme sırasında değiştirildi, bu nedenle yeniden eşitlenmesi gerekiyor. | Eylem gerekmiyor. |
| 0x80070017 | -2147024873 | ERROR_CRC | CRC hatası nedeniyle dosya eşitlenemiyor. Bu hata, bir sunucu uç noktası silinmeden veya dosya bozuksa önce katmanlı bir dosya geri çağrılmıyorsa meydana gelebilir. | Bu sorunu çözmek için, sunucu uç noktası silindikten sonra, yalnız bırakılmış katmanlı dosyaları kaldırmak için [katmanlı dosyalara sunucu üzerinde erişilebilir değil](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) ' e bakın. Yalnız bırakılmış katmanlı dosyalar kaldırıldıktan sonra hata oluşmaya devam ederse, birimde [Chkdsk komutunu](/windows-server/administration/windows-commands/chkdsk) çalıştırın. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | Çakışma dosyası sayısı üst sınırına ulaşıldığından dosya eşitlenemiyor. Azure Dosya Eşitleme dosya başına 100 çakışma dosyasını destekler. Dosya çakışmaları hakkında daha fazla bilgi edinmek için Azure Dosya Eşitleme [SSS](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#afs-conflict-resolution)bölümüne bakın. | Bu sorunu çözmek için çakışma dosyalarının sayısını azaltın. Çakışma dosyalarının sayısı 100’ün altına düştükten sonra dosya eşitlenecektir. |

#### <a name="handling-unsupported-characters"></a>Desteklenmeyen karakterleri işleme
**FileSyncErrorsReport.ps1** PowerShell betiği desteklenmeyen karakterler (hata kodu 0x8007007B veya 0x80c80255) nedeniyle öğe başına eşitleme hatalarını gösteriyorsa, ilgili dosya adlarından hata durumunda karakterleri kaldırmanız veya yeniden adlandırmanız gerekir. Bu karakterlerin çoğu standart görsel kodlamaya sahip olmadığından, PowerShell bu karakterleri muhtemelen soru işaretleri veya boş dikdörtgenler olarak yazdıracaktır. 
> [!Note]  
> [Değerlendirme aracı](file-sync-planning.md#evaluation-cmdlet) , desteklenmeyen karakterleri belirlemek için kullanılabilir. Veri kümeniz geçersiz karakterler içeren birkaç dosya içeriyorsa, desteklenmeyen karakterler içeren dosyaları yeniden adlandırmak için [Scanunsupportedchars](https://github.com/Azure-Samples/azure-files-samples/tree/master/ScanUnsupportedChars) betiğini kullanın.

Aşağıdaki tabloda, Azure Dosya Eşitleme henüz desteklemediği tüm Unicode karakterler bulunur.

| Karakter kümesi | Karakter sayısı |
|---------------|-----------------|
| 0x00000000-0x0000001F (denetim karakterleri) | 32 |
| 0x0000FDD0-0X0000fbbb (Arapça sunum formları-a) | 14 |
| <ul><li>0x00000022 (tırnak işareti)</li><li>0x0000002A (yıldız işareti)</li><li>0x0000002F (eğik çizgi)</li><li>0x0000003A (iki nokta üst üste)</li><li>0x0000003C (küçüktür)</li><li>0x0000003E (büyüktür)</li><li>0x0000003F (soru işareti)</li><li>0x0000005C (ters eğik çizgi)</li><li>0x0000007C (kanal veya çubuk)</li></ul> | 9 |
| <ul><li>0x0004FFFE-0x0004FFFF = 2 (karakter olmayan)</li><li>0x0008FFFE-0x0008FFFF = 2 (karakter olmayan)</li><li>0x000CFFFE-0x000CFFFF = 2 (karakter olmayan)</li><li>0x0010FFFE-0x0010FFFF = 2 (karakter olmayan)</li></ul> | 8 |
| <ul><li>0x0000009D (OSC işletim sistemi komutu)</li><li>0x00000090 (DCS cihaz denetim dizesi)</li><li>0x0000008F (SS3 tek SHIFT üç)</li><li>0x00000081 (yüksek sekizli ön ayarı)</li><li>0x0000007F (del Delete)</li><li>0x0000008D (RI ters satır besleme)</li></ul> | 6 |
| 0x0000FFF0, 0x0000FFFD, 0x0000FFFE, 0x0000FFFF (özel) | 4 |
| Bir noktayla biten dosyalar veya dizinler | 1 |

### <a name="common-sync-errors"></a>Ortak Eşitleme hataları
<a id="-2147023673"></a>**Eşitleme oturumu iptal edildi.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (ondalık)** | -2147023673 | 
| **Hata dizesi** | ERROR_CANCELLED |
| **Düzeltme gerekli** | No |

Eşitleme oturumları, sunucunun yeniden başlatılması veya güncelleştirilmesini, VSS anlık görüntülerini vb. dahil çeşitli nedenlerle başarısız olabilir. Bu hata, izlenmesi gereken gibi görünse de, birkaç saat boyunca devam etmediği takdirde bu hatayı yoksaymak güvenlidir.

<a id="-2147012889"></a>**Hizmetle bir bağlantı kurulamadı.**    

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80072EE7 |
| **HRESULT (ondalık)** | -2147012889 | 
| **Hata dizesi** | WININET_E_NAME_NOT_RESOLVED |
| **Düzeltme gerekli** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**Kullanıcı isteği hizmet tarafından kısıtlandı.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (ondalık)** | -2134376372 |
| **Hata dizesi** | ECS_E_USER_REQUEST_THROTTLED |
| **Düzeltme gerekli** | No |

Herhangi bir eylem gerekmez; sunucu yeniden denenecek. Bu hata birkaç saat devam ederse destek isteği oluşturun.

<a id="-2134364043"></a>**Değişiklik algılama geri yükleme sonrası tamamlanana kadar eşitleme engellendi**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (ondalık)** | -2134364043 |
| **Hata dizesi** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Düzeltme gerekli** | No |

İşlem yapmanız gerekmez. Bir dosya veya dosya paylaşma (bulut uç noktası) Azure Backup kullanılarak geri yüklendiğinde, Azure dosya paylaşımında değişiklik algılama tamamlanana kadar eşitleme engellenir. Geri yükleme tamamlandıktan hemen sonra değişiklik algılama çalıştırılır ve çalışma süresi dosya paylaşımındaki dosyaların sayısına bağlıdır.

<a id="-2147216747"></a>**Eşitleme veritabanı kaldırıldığından eşitleme başarısız oldu.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT (ondalık)** | -2147216747 |
| **Hata dizesi** | SYNC_E_METADATA_INVALID_OPERATION |
| **Düzeltme gerekli** | No |

Normalde bir yedekleme uygulaması VSS anlık görüntüsü oluşturduğunda ve eşitleme veritabanının yüklemesi kaldırıldığında bu hata oluşur. Bu hata birkaç saat devam ederse destek isteği oluşturun.

<a id="-2134364065"></a>**Eşitleme, bulut uç noktasında belirtilen Azure dosya paylaşımıyla erişemiyor.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (ondalık)** | -2134364065 |
| **Hata dizesi** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Düzeltme gerekli** | Yes |

Azure dosya paylaşımı veya bunu barındıran depolama hesabı artık var olmadığından Azure Dosya Eşitleme aracısı Azure dosya paylaşımına erişemediği için bu hata oluştu. Aşağıdaki adımları uygulayarak bu hataya giderebilirsiniz:

1. [Depolama hesabının mevcut olduğundan emin olun.](#troubleshoot-storage-account)
2. [Azure dosya paylaşımının mevcut olduğundan emin olun.](#troubleshoot-azure-file-share)
3. [Azure Dosya Eşitleme depolama hesabına erişiminin olduğundan emin olun.](#troubleshoot-rbac)
4. [Depolama hesabında güvenlik duvarı ve sanal ağ ayarlarının (etkinleştirildiyse) düzgün yapılandırıldığını doğrulayın](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**İstek bu işlemi gerçekleştirmek için yetkili olmadığından eşitleme başarısız oldu.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c86044 |
| **HRESULT (ondalık)** | -2134351804 |
| **Hata dizesi** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **Düzeltme gerekli** | Yes |

Bu hata, Azure Dosya Eşitleme aracısının Azure dosya paylaşımında erişim yetkisi olmadığı için oluşur. Aşağıdaki adımları uygulayarak bu hataya giderebilirsiniz:

1. [Depolama hesabının mevcut olduğundan emin olun.](#troubleshoot-storage-account)
2. [Azure dosya paylaşımının mevcut olduğundan emin olun.](#troubleshoot-azure-file-share)
3. [Depolama hesabında güvenlik duvarı ve sanal ağ ayarlarının (etkinleştirildiyse) düzgün yapılandırıldığını doğrulayın](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [Azure Dosya Eşitleme depolama hesabına erişiminin olduğundan emin olun.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**Kullanılan depolama hesabı adı çözümlenemedi.**  

| Hata | Kod |
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
3. [Depolama hesabında güvenlik duvarı ve sanal ağ ayarlarının (etkinleştirildiyse) düzgün yapılandırıldığını doğrulayın](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Depolama hesabına erişirken bilinmeyen bir hata oluştu.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c8308a |
| **HRESULT (ondalık)** | -2134364022 |
| **Hata dizesi** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Düzeltme gerekli** | Yes |

1. [Depolama hesabının mevcut olduğundan emin olun.](#troubleshoot-storage-account)
2. [Depolama hesabında güvenlik duvarı ve sanal ağ ayarlarının (etkinleştirildiyse) düzgün yapılandırıldığını doğrulayın](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**Depolama hesabı kilitli olduğundan eşitleme başarısız oldu.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c83092 |
| **HRESULT (ondalık)** | -2134364014 |
| **Hata dizesi** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **Düzeltme gerekli** | Yes |

Bu hata, depolama hesabının salt okunurdur [kaynak kilidi](../../azure-resource-manager/management/lock-resources.md)olduğu için oluşur. Bu sorunu çözmek için depolama hesabındaki salt okunur kaynak kilidini kaldırın. 

<a id="-1906441138"></a>**Eşitleme veritabanıyla ilgili bir sorun nedeniyle eşitleme başarısız oldu.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (ondalık)** | -1906441138 |
| **Hata dizesi** | JET_errWriteConflict |
| **Düzeltme gerekli** | Yes |

Azure Dosya Eşitleme tarafından kullanılan iç veritabanıyla ilgili bir sorun olduğunda bu hata oluşur. Bu sorun oluştuğunda, bir destek isteği oluşturun ve bu sorunu çözmenize yardımcı olması için sizinle iletişim kuracağız.

<a id="-2134364053"></a>**Sunucuda yüklü Azure Dosya Eşitleme Aracısı sürümü desteklenmiyor.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (ondalık)** | -2134364053 |
| **Hata dizesi** | ECS_E_AGENT_VERSION_BLOCKED |
| **Düzeltme gerekli** | Yes |

Sunucuda yüklü olan Azure Dosya Eşitleme aracısının sürümü desteklenmiyorsa bu hata oluşur. Bu sorunu çözmek için [desteklenen bir aracı sürümüne]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions) [yükseltin]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) .

<a id="-2134351810"></a>**Azure dosya paylaşımının depolama sınırına ulaştınız.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (ondalık)** | -2134351810 |
| **Hata dizesi** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Düzeltme gerekli** | Yes |

Bu hata, Azure dosya paylaşımına kota uygulandığında veya kullanım Azure dosya paylaşımının sınırlarını aştığında ortaya çıkabilen Azure dosya paylaşımı depolama sınırına ulaşılması durumunda oluşur. Daha fazla bilgi için bkz. [Azure dosya paylaşımının geçerli sınırları](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json).

1. Depolama eşitleme hizmeti içindeki eşitleme grubuna gidin.
2. Eşitleme grubu içindeki bulut uç noktasını seçin.
3. Açılan bölmedeki Azure dosya paylaşımının adını aklınızda edin.
4. Bağlantılı depolama hesabını seçin. Bu bağlantı başarısız olursa, başvurulan depolama hesabı kaldırılmıştır.

    ![Bulut uç noktası ayrıntı bölmesini, depolama hesabına yönelik bir bağlantı ile gösteren ekran görüntüsü.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Dosya paylaşımlarının listesini görüntülemek için **dosyalar** ' ı seçin.
6. Bulut uç noktası tarafından başvurulan Azure dosya paylaşımının satırının sonundaki üç noktaya tıklayın.
7. **Kullanım** değerinin **Kota**'nın altında kaldığını doğrulayın. Alternatif bir kota belirtilmediği takdirde, kota [Azure dosya paylaşımının en büyük boyutuyla](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)eşleşmeyecektir.

    ![Azure dosya paylaşma özelliklerinin ekran görüntüsü.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Paylaşım doluysa ve kota ayarlanmadıysa, bu sorunu çözmenin olası bir yolu geçerli sunucu uç noktasının her alt klasörünü kendi ayrı eşitleme gruplarında kendi sunucu uç noktası yapmaktır. Bu şekilde her alt klasör ayrı bir Azure dosya paylaşımına eşitlenir.

<a id="-2134351824"></a>**Azure dosya paylaşımında bulunamıyor.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (ondalık)** | -2134351824 |
| **Hata dizesi** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Düzeltme gerekli** | Yes |

Azure dosya paylaşımı erişilemez olduğunda bu hata oluşur. Sorunu gidermek için:

1. [Depolama hesabının mevcut olduğundan emin olun.](#troubleshoot-storage-account)
2. [Azure dosya paylaşımının mevcut olduğundan emin olun.](#troubleshoot-azure-file-share)

Azure dosya paylaşma silinmişse, yeni bir dosya paylaşma oluşturmanız ve ardından eşitleme grubunu yeniden oluşturmanız gerekir. 

<a id="-2134364042"></a>**Bu Azure aboneliği askıya alındığında eşitleme duraklatıldı.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (ondalık)** | -2134364042 |
| **Hata dizesi** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Düzeltme gerekli** | Yes |

Azure aboneliği askıya alındığında bu hata oluşur. Azure aboneliği geri yüklendiğinde eşitleme yeniden etkinleştirilir. [Azure Aboneliğimin neden devre dışı bırakıldığını ve daha fazla bilgi için nasıl yeniden etkinleştirebilirim?](../../cost-management-billing/manage/subscription-disabled.md) bölümüne bakın.

<a id="-2134375618"></a>**Depolama hesabında yapılandırılmış bir güvenlik duvarı veya sanal ağ vardır.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c8033e |
| **HRESULT (ondalık)** | -2134375618 |
| **Hata dizesi** | ECS_E_SERVER_BLOCKED_BY_NETWORK_ACL |
| **Düzeltme gerekli** | Yes |

Depolama hesabı güvenlik duvarından veya depolama hesabının bir sanal ağa ait olmasından dolayı Azure dosya paylaşımına erişilemediğinde bu hata oluşur. Depolama hesabındaki güvenlik duvarının ve sanal ağ ayarlarının doğru şekilde yapılandırıldığını doğrulayın. Daha fazla bilgi için bkz. [güvenlik duvarı ve sanal ağ ayarlarını yapılandırma](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings). 

<a id="-2134375911"></a>**Eşitleme veritabanıyla ilgili bir sorun nedeniyle eşitleme başarısız oldu.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (ondalık)** | -2134375911 |
| **Hata dizesi** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Düzeltme gerekli** | No |

Bu hata genellikle kendiliğinden çözülür ve şunlar varsa gerçekleşebilir:

* Eşitleme grubundaki sunucular arasında yüksek sayıda dosya değişikliği.
* Tek tek dosyalarda ve dizinlerde çok sayıda hata var.

Bu hata birkaç saatten uzun sürerse, bir destek isteği oluşturun ve bu sorunu çözmenize yardımcı olması için sizinle iletişim kuracağız.

<a id="-2146762487"></a>**Sunucu güvenli bir bağlantı kuramadı. Bulut hizmeti beklenmeyen bir sertifika aldı.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (ondalık)** | -2146762487 |
| **Hata dizesi** | CERT_E_UNTRUSTEDROOT |
| **Düzeltme gerekli** | Yes |

Kuruluşunuz bir TLS Sonlandırıcı proxy kullanıyorsa veya kötü amaçlı bir varlık, sunucunuz ve Azure Dosya Eşitleme hizmeti arasındaki trafiği kesintiye uğratır bu hata oluşabilir. Bunun beklendiğinden eminseniz (kuruluşunuz bir TLS Sonlandırıcı proxy kullandığından), bir kayıt defteri geçersiz kılma ile sertifika doğrulamayı atlayabilirsiniz.

1. SkipVerifyingPinnedRootCertificate kayıt defteri değerini oluşturun.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Kayıtlı sunucuda eşitleme hizmetini yeniden başlatın.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Bu kayıt defteri değerini ayarlayarak Azure Dosya Eşitleme Aracısı, sunucu ile bulut hizmeti arasında veri aktarımı yaparken yerel olarak güvenilen TLS/SSL sertifikasını kabul eder.

<a id="-2147012894"></a>**Hizmetle bir bağlantı kurulamadı.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80072EE2 |
| **HRESULT (ondalık)** | -2147012894 |
| **Hata dizesi** | WININET_E_TIMEOUT |
| **Düzeltme gerekli** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**Kimlik doğrulamasıyla ilgili bir sorun nedeniyle eşitleme başarısız oldu.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (ondalık)** | -2134375680 |
| **Hata dizesi** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Düzeltme gerekli** | Yes |

Bu hata normalde sunucu saati yanlış olduğunda oluşur. Sunucu bir sanal makinede çalışıyorsa, konaktaki saatin doğru olduğundan emin olun.

<a id="-2134364040"></a>**Sertifika süre sonu nedeniyle eşitleme başarısız oldu.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT (ondalık)** | -2134364040 |
| **Hata dizesi** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Düzeltme gerekli** | Yes |

Kimlik doğrulaması için kullanılan sertifikanın süresi dolduğundan bu hata oluştu.

Sertifikanın süresinin dolduğunu onaylamak için aşağıdaki adımları uygulayın:  
1. Sertifikalar MMC ek bileşenini açın, bilgisayar hesabı ' nı seçin ve Sertifikalar (yerel bilgisayar) \ kişisel \ sertifika ates' a gidin.
2. İstemci kimlik doğrulama sertifikasının dolup dolmadığına bakın.

İstemci kimlik doğrulama sertifikasının süresi dolduysa, sorunu çözmek için aşağıdaki adımları uygulayın:

1. Azure Dosya Eşitleme Agent sürümü 4.0.1.0 veya üstünün yüklü olduğunu doğrulayın.
2. Sunucuda şu PowerShell komutunu çalıştırın: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**Kimlik doğrulama sertifikası bulunamadığı için eşitleme başarısız oldu.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT (ondalık)** | -2134375896 |
| **Hata dizesi** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Düzeltme gerekli** | Yes |

Kimlik doğrulaması için kullanılan sertifika bulunamadığından bu hata oluştu.

Bu sorunu çözmek için aşağıdaki adımları gerçekleştirin:

1. Azure Dosya Eşitleme Agent sürümü 4.0.1.0 veya üstünün yüklü olduğunu doğrulayın.
2. Sunucuda şu PowerShell komutunu çalıştırın: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**Kimlik doğrulama kimliği bulunamadığı için eşitleme başarısız oldu.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT (ondalık)** | -2134364039 |
| **Hata dizesi** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Düzeltme gerekli** | Yes |

Sunucu uç noktasını silme işlemi başarısız olduğu ve uç nokta şimdi kısmen silinmiş durumda olduğu için bu hata oluşur. Bu sorunu çözmek için sunucu uç noktasını silmeyi yeniden deneyin.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Sunucu uç noktasının bulunduğu birimde disk alanı azalmış olur.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (ondalık)** | -1906441711 |
| **Hata dizesi** | JET_errLogDiskFull |
| **Düzeltme gerekli** | Yes |

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c8031a |
| **HRESULT (ondalık)** | -2134375654 |
| **Hata dizesi** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Düzeltme gerekli** | Yes |

Bu hata birimin doldurulmuş olması nedeniyle oluşur. Bu hata genellikle sunucu uç noktasının dışındaki dosyaların birimde yer kaplamasından dolayı oluşur. Başka sunucu uç noktaları ekleyerek, dosyaları farklı bir birime taşıyarak veya sunucu uç noktasının açık olduğu birimin boyutunu artırarak birimde yer açın.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**Hizmet henüz bu sunucu uç noktası ile eşitlenmeye hazırlanıyor.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (ondalık)** | -2134364145 |
| **Hata dizesi** | ECS_E_REPLICA_NOT_READY |
| **Düzeltme gerekli** | No |

Bu hata, bulut uç noktasının Azure dosya paylaşımında zaten mevcut olan içerikle oluşturulduğu için oluşur. Azure Dosya Eşitleme, sunucu uç noktasının ilk eşitlemesine devam etmesini sağlamak için Azure dosya paylaşımında tüm içerikleri taramalıdır.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Birçok ayrı dosya ile ilgili sorunlar nedeniyle eşitleme başarısız oldu.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (ondalık)** | -2134375877 |
| **Hata dizesi** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Düzeltme gerekli** | Yes |

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c8021c |
| **HRESULT (ondalık)** | -2134375908 |
| **Hata dizesi** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Düzeltme gerekli** | Yes |

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c80253 |
| **HRESULT (ondalık)** | -2134375853 |
| **Hata dizesi** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Düzeltme gerekli** | Yes |

Öğe başına hatalarla eşitlenemeyecek çok sayıda dosya olduğunda eşitleme oturumları bu hatalardan biriyle başarısız olur. Öğe başına hataları çözmek için [nasıl yaparım?, eşitlenmekte olan belirli dosya veya klasörler](?tabs=portal1%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) olup olmadığını görmek için ' de belgelenen adımları uygulayın. Eşitleme hatası ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED için lütfen bir destek talebi açın.

> [!NOTE]
> Azure Dosya Eşitleme, açık tanıtıcıların bulunduğu dosyaları eşitlemek için sunucuda günde bir kez geçici bir VSS anlık görüntüsü oluşturur.

<a id="-2134376423"></a>**Sunucu uç noktası yolundaki bir sorun nedeniyle eşitleme başarısız oldu.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (ondalık)** | -2134376423 |
| **Hata dizesi** | ECS_E_SYNC_INVALID_PATH |
| **Düzeltme gerekli** | Yes |

Yolun var olduğundan, yerel bir NTFS biriminde olduğundan ve bir yeniden ayrıştırma noktası ya da var olan sunucu uç noktası olmadığından emin olun.

<a id="-2134375817"></a>**Filtre sürücüsü sürümü Aracı sürümüyle uyumlu olmadığından eşitleme başarısız oldu**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (ondalık)** | -2134375817 |
| **Hata dizesi** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Düzeltme gerekli** | Yes |

Yüklenmiş olan bulut katmanlama filtresi sürücüsü (StorageSync.sys) sürümü, Depolama Eşitleme Aracısı (FileSyncSvc) hizmeti ile uyumlu olmadığı için bu hata oluştu. Azure Dosya Eşitleme aracısı yükseltildiyse, yüklemeyi tamamlamak için sunucuyu yeniden başlatın. Hata oluşmaya devam ederse aracıyı kaldırın, sunucuyu yeniden başlatın ve Azure Dosya Eşitleme aracısını yeniden yükleyin.

<a id="-2134376373"></a>**Hizmet şu anda kullanılamıyor.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (ondalık)** | -2134376373 |
| **Hata dizesi** | ECS_E_SERVICE_UNAVAILABLE |
| **Düzeltme gerekli** | No |

Azure Dosya Eşitleme hizmeti kullanılamadığından bu hata oluşur. Azure Dosya Eşitleme hizmeti kullanılabilir olduğunda bu hata otomatik olarak çözülecektir.

<a id="-2146233088"></a>**Bir özel durum nedeniyle eşitleme başarısız oldu.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT (ondalık)** | -2146233088 |
| **Hata dizesi** | COR_E_EXCEPTION |
| **Düzeltme gerekli** | No |

Eşitleme özel bir durum nedeniyle başarısız olduğu için bu hata oluşur. Hata birkaç saat devam ederse lütfen bir destek isteği oluşturun.

<a id="-2134364045"></a>**Depolama hesabı başka bir bölgeye yük devrettiğinden eşitleme başarısız oldu.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT (ondalık)** | -2134364045 |
| **Hata dizesi** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Düzeltme gerekli** | Yes |

Depolama hesabı başka bir bölgeye yük devrettiği için bu hata oluştu. Azure Dosya Eşitleme, depolama hesabı yük devretme özelliğini desteklemez. Azure Dosya Eşitleme'de bulut uç noktaları olarak kullanılan Azure dosya paylaşımlarının bulunduğu depolama hesapları yük devretmemelidir. Bunun yapılması eşitlemenin çalışmayı durdurmasına neden olur ve yeni katmanlanmış dosyalar söz konusu olduğunda beklenmedik veri kaybına da yol açabilir. Bu sorunu çözmek için depolama hesabını birincil bölgeye taşıyın.

<a id="-2134375922"></a>**Eşitleme veritabanı ile ilgili geçici bir sorun nedeniyle eşitleme başarısız oldu.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (ondalık)** | -2134375922 |
| **Hata dizesi** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Düzeltme gerekli** | No |

Eşitleme veritabanındaki bir iç sorundan dolayı bu hata oluşur. Eşitleme yeniden denendiğinde bu hata otomatik olarak düzelecektir. Bu hata uzabir süre devam ederse, bir destek isteği oluşturun ve bu sorunu çözmenize yardımcı olması için sizinle iletişim kuracağız.

<a id="-2134364024"></a>**Azure Active Directory kiracısındaki değişiklik nedeniyle eşitleme başarısız oldu**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT (ondalık)** | -2134364024 | 
| **Hata dizesi** | ECS_E_INVALID_AAD_TENANT |
| **Düzeltme gerekli** | Yes |

En son Azure Dosya Eşitleme aracısına sahip olduğunuzdan emin olun. Aracı Ile v10 arasındaki itibariyle, Azure Dosya Eşitleme aboneliğin farklı bir Azure Active Directory kiracısına taşınmasını destekler.
 
En son aracı sürümüne sahip olduğunuzda, Microsoft. Storagessync uygulamasına depolama hesabına erişim sağlamanız gerekir (bkz. [Azure dosya eşitleme depolama hesabına erişimi olduğundan emin olun](#troubleshoot-rbac)).

<a id="-2134364010"></a>**Güvenlik Duvarı ve sanal ağ özel durumu yapılandırılmadığı için eşitleme başarısız oldu**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT (ondalık)** | -2134364010 | 
| **Hata dizesi** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Düzeltme gerekli** | Yes |

Bu hata, güvenlik duvarı ve sanal ağ ayarları depolama hesabında etkinleştirilmişse ve "Güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine Izin ver" özel durumu işaretli değilse oluşur. Bu sorunu çözmek için dağıtım kılavuzunun [Güvenlik duvarı ve sanal ağ ayarlarını yapılandırma](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) bölümünde açıklanan adımları izleyin.

<a id="-2147024891"></a>**Sistem birimi bilgi klasörü izinleri yanlış olduğundan eşitleme başarısız oldu.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT (ondalık)** | -2147024891 |
| **Hata dizesi** | ERROR_ACCESS_DENIED |
| **Düzeltme gerekli** | Yes |

Bu hata, NT AUTHORITY\SYSTEM hesabının sunucu uç noktasının bulunduğu birimdeki Sistem Birim Bilgisi klasörü üzerinde izinleri olmadığında oluşabilir. Ayrı dosyalar ERROR_ACCESS_DENIED eşitleme başarısız olursa, [Dosya/Dizin eşitleme hatalarını giderme](?tabs=portal1%252cazure-portal#troubleshooting-per-filedirectory-sync-errors) bölümünde belgelenen adımları uygulayın.

Bu sorunu çözmek için aşağıdaki adımları gerçekleştirin:

1. [Psexec](/sysinternals/downloads/psexec) aracını indirin.
2. Sistem hesabını kullanarak yükseltilmiş komut isteminde şu komutu çalıştırıp komut istemini başlatın: **PsExec.exe -i -s -d cmd** 
3. Sistem hesabı altında çalıştırılan komut isteminde şu komutu çalıştırarak NT AUTHORITY\SYSTEM hesabının Sistem Birim Bilgisi klasörüne erişimi olmadığını onaylayın: **cacls "sürücü harfi:\sistem birim bilgisi" /T /C**
4. NT AUTHORITY\SYSTEM hesabının Sistem Birim Bilgisi klasörüne erişimi yoksa şu komutu çalıştırın: **cacls  "sürücü harfi:\sistem birim bilgisi" /T /E /G "NT AUTHORITY\SYSTEM:F"**
    - 4. adım erişim reddedildi hatasıyla başarısız olursa Sistem Birim Bilgisi klasörünün sahipliğini almak için şu komutu çalıştırın ve sonra 4. adımı yineleyin: **takeown /A /R /F "sürücü harfi:\Sistem Birim Bilgisi"**

<a id="-2134375810"></a>**Azure dosya paylaşımının silindiği ve yeniden oluşturulduğu için eşitleme başarısız oldu.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT (ondalık)** | -2134375810 |
| **Hata dizesi** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Düzeltme gerekli** | Yes |

Azure Dosya Eşitleme aynı eşitleme grubunda Azure dosya paylaşımını silmeyi ve oluşturmayı desteklemediğinden bu hata oluşur. 

Bu hatayı düzeltmek için aşağıdaki adımları uygulayarak eşitleme grubunu silin ve yeniden oluşturun:

1. Eşitleme grubundaki tüm sunucu uç noktalarını silin.
2. Bulut uç noktasını silin. 
3. Eşitleme grubunu silin.
4. Bulut katmanlaması bir sunucu uç noktasında etkinleştirilmişse, sunucu [uç noktası bölümünü sildikten sonra katmanlı dosyalar](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) bölümünde belgelenen adımları gerçekleştirerek sunucuda yalnız bırakılmış olan dosyaları silin.
5. Eşitleme grubunu yeniden oluşturun.

<a id="-2145844941"></a>**HTTP isteği yeniden yönlendirildiği için eşitleme başarısız oldu**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80190133 |
| **HRESULT (ondalık)** | -2145844941 |
| **Hata dizesi** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **Düzeltme gerekli** | Yes |

Bu hatanın nedeni, Azure Dosya Eşitleme'nin HTTP yeniden yönlendirme (3xx durum kodu) yönlendirme desteği sunmamasıdır. Bu sorunu çözmek için, ara sunucunuzda veya ağ cihazınızda HTTP yeniden yönlendirmeyi devre dışı bırakın.

<a id="-2134364027"></a>**Çevrimdışı veri aktarımı sırasında zaman aşımı oluştu, ancak devam ediyor.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c83085 |
| **HRESULT (ondalık)** | -2134364027 |
| **Hata dizesi** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **Düzeltme gerekli** | No |

Bu hata, bir veri alma işlemi zaman aşımını aştığında oluşur. Eşitleme ilerleme durumu (AppliedItemCount 0 ' dan büyükse) Bu hata yoksayılabilir. [Geçerli bir eşitleme oturumunun ilerlemesini izlemek nasıl yaparım? bakın mi?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

<a id="-2134375814"></a>**Sunucu uç noktası yolu sunucuda bulunamadığı için eşitleme başarısız oldu.**  

| Hata | Kod |
|-|-|
| **HRESULT** | 0x80c8027a |
| **HRESULT (ondalık)** | -2134375814 |
| **Hata dizesi** | ECS_E_SYNC_ROOT_DIRECTORY_NOT_FOUND |
| **Düzeltme gerekli** | Yes |

Sunucu uç noktası yolu olarak kullanılan dizin yeniden adlandırılırsa veya silinirse bu hata oluşur. Dizin yeniden adlandırılırsa, dizini özgün adla yeniden adlandırın ve depolama eşitleme Aracısı hizmetini (FileSyncSvc) yeniden başlatın.

Dizin silinmişse, mevcut sunucu uç noktasını kaldırmak ve yeni bir yol kullanarak yeni bir sunucu uç noktası oluşturmak için aşağıdaki adımları gerçekleştirin:

1. [Sunucu uç noktasını kaldırma](file-sync-server-endpoint.md#remove-a-server-endpoint)bölümünde belgelenen adımları izleyerek eşitleme grubundaki sunucu uç noktasını kaldırın.
2. [Sunucu uç noktası ekleme](file-sync-server-endpoint.md#add-a-server-endpoint)bölümünde belgelenen adımları izleyerek eşitleme grubunda yeni bir sunucu uç noktası oluşturun.

### <a name="common-troubleshooting-steps"></a>Ortak sorun giderme adımları
<a id="troubleshoot-storage-account"></a>**Depolama hesabının mevcut olduğundan emin olun.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Depolama eşitleme hizmeti içindeki eşitleme grubuna gidin.
2. Eşitleme grubu içindeki bulut uç noktasını seçin.
3. Açılan bölmedeki Azure dosya paylaşımının adını aklınızda edin.
4. Bağlantılı depolama hesabını seçin. Bu bağlantı başarısız olursa, başvurulan depolama hesabı kaldırılmıştır.
    ![Bulut uç noktası ayrıntı bölmesini, depolama hesabına yönelik bir bağlantı ile gösteren ekran görüntüsü.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
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
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Ana depolama hesabı sayfasına dönmek için sol taraftaki içindekiler tablosuna **Genel Bakış ' a** tıklayın.
2. Dosya paylaşımlarının listesini görüntülemek için **dosyalar** ' ı seçin.
3. Bulut uç noktası tarafından başvurulan dosya paylaşımının dosya paylaşımları listesinde göründüğünü doğrulayın (yukarıdaki 1. adımda bu dosyayı Not etmeniz gerekir).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
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
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Sol taraftaki içindekiler tablosunda **erişim denetimi (IAM)** seçeneğine tıklayın.
1. Depolama hesabınıza erişimi olan Kullanıcı ve uygulamaları (*hizmet sorumluları*) listelemek için **rol atamaları** sekmesine tıklayın.
1. **Microsoft. Storagessync** veya **hibrit dosya eşitleme hizmeti** 'nin (eski uygulama adı), **okuyucu ve veri erişimi** rolüyle listede göründüğünü doğrulayın. 

    ![Depolama hesabının erişim denetimi sekmesinde karma Dosya Eşitleme hizmet hizmet sorumlusu ekran görüntüsü](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    **Microsoft. Storagessync** veya **hibrit dosya eşitleme hizmeti** listede görünmezse, aşağıdaki adımları uygulayın:

    - **Ekle**'ye tıklayın.
    - **Rol** alanında, **okuyucu ve veri erişimi**' ni seçin.
    - **Seç** alanında **Microsoft. storagessync** yazın, rolü seçin ve **Kaydet**' e tıklayın.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Microsoft.StorageSync" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

## <a name="cloud-tiering"></a>Bulut katmanlaması 
Bulut katmanlarındaki hatalara yönelik iki yol vardır:

- Dosyalar katmanında başarısız olabilir. Bu, Azure Dosya Eşitleme başarısız olduğu anlamına gelir.
- Dosyalar, bir Kullanıcı katmanlı bir dosyaya erişmeyi denediğinde Azure Dosya Eşitleme dosya sistemi filtresinin (StorageSync.sys) verileri indiremediği anlamına gelir.

Hata yolu aracılığıyla gerçekleşebileceği iki ana hata sınıfı vardır:

- Bulut depolama sorunları
    - *Geçici depolama hizmeti kullanılabilirlik sorunları*. Daha fazla bilgi için bkz. [Azure depolama için hizmet düzeyi sözleşmesi (SLA)](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Erişilemeyen Azure dosya paylaşma*. Bu hata genellikle, bir eşitleme grubunda hala bir bulut uç noktası olduğunda Azure dosya paylaşımından sildiğiniz zaman gerçekleşir.
    - *Erişilemeyen depolama hesabı*. Bu hata genellikle, bir eşitleme grubundaki bir bulut uç noktası olan bir Azure dosya paylaşımıyla hala depolama hesabını sildiğinizde meydana gelir. 
- Sunucu arızaları 
  - *Azure dosya eşitleme dosya sistemi filtresi (StorageSync.sys) yüklü değil*. Katmanlama/geri çekme isteklerini yanıtlamak için Azure Dosya Eşitleme dosya sistemi filtresi yüklenmelidir. Bu filtre, birkaç nedenden dolayı oluşabilir, ancak en sık görülen nedenler yöneticinin el ile bellekten kaldırılmamasından kaynaklanıyor olabilir. Azure Dosya Eşitleme düzgün çalışması için Azure Dosya Eşitleme dosya sistemi filtresinin her zaman yüklenmesi gerekir.
  - *Eksik, bozuk veya bozuk yeniden ayrıştırma noktası*. Yeniden ayrıştırma noktası, iki bölümden oluşan bir dosyadaki özel bir veri yapısıdır:
    1. Azure Dosya Eşitleme dosya sistemi filtresinin (StorageSync.sys), dosya için GÇ üzerinde bazı eylemler yapması gerekebilecek işletim sistemini gösteren bir yeniden ayrıştırma etiketi. 
    2. Dosya sisteminin, ilişkili bulut uç noktasındaki (Azure dosya paylaşımında) dosyanın URI 'sini filtrelemesine işaret eden yeniden ayrıştırma verileri. 
        
       Bir yeniden ayrıştırma noktasının bozulmuş hale gelebileceği en yaygın yolu, bir yöneticinin etiketi veya verilerini değiştirme girişiminde bulunduğu bir şeydir. 
  - *Ağ bağlantısı sorunları*. Bir dosyayı katmana eklemek veya geri çağırmak için, sunucusunun İnternet bağlantısı olması gerekir.

Aşağıdaki bölümlerde, bulut katmanlama sorunlarının nasıl giderileceği ve bir sorunun bir bulut depolama sorunu ya da sunucu sorunu olup olmadığı saptandığını gösterir.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>Bir sunucuda katmanlama etkinliğini izleme  
Bir sunucudaki katmanlama etkinliğini izlemek için, telemetri olay günlüğünde olay KIMLIĞI 9003, 9016 ve 9029 (Olay Görüntüleyicisi içinde uygulamalar ve Services\Microsoft\FileSync\Agent altında bulunur) kullanın.

- Olay KIMLIĞI 9003, bir sunucu uç noktası için hata dağıtımı sağlar. Örneğin, toplam hata sayısı, hata kodu, vb. Her hata kodu için bir olay günlüğe kaydedilir.
- Olay kimliği 9016, bir birim için hayalet kopya oluşturuluyor sonuçları sağlar. Örneğin, boş alan yüzdesi, oturumdaki hayalet dosya sayısı, hayalet olmayan dosya sayısı, vb.
- Olay kimliği 9029, bir sunucu uç noktası için hayalet kopya oluşturuluyor oturum bilgileri sağlar. Örneğin, oturumda denendiği dosya sayısı, oturumda katmanlı dosya sayısı, zaten katmanlı dosya sayısı, vb.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>Bir sunucuda geri çağırma etkinliğini izleme
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
   3. Azure Dosya Eşitleme filtresi sürücülerinin (StorageSync.sys ve StorageSyncGuard.sys) çalıştığını doğrulayın:
       - Yükseltilmiş bir komut isteminde komutunu çalıştırın `fltmc` . StorageSync.sys ve StorageSyncGuard.sys dosya sistemi filtre sürücülerinin listelendiğini doğrulayın.

> [!NOTE]
> Bir dosya katmanı (hata kodu başına bir olay günlüğe kaydedilir) başarısız olursa, telemetri olay günlüğünde bir saat sonra bir olay KIMLIĞI 9003 kaydedilir. Hata kodu için düzeltme adımlarının listelenip listelenmediğini görmek için [katmanlama hatalarını ve düzeltme](#tiering-errors-and-remediation) bölümünü denetleyin.

### <a name="tiering-errors-and-remediation"></a>Hataları ve düzeltmeyi katmanlama

| HRESULT | HRESULT (ondalık) | Hata dizesi | Sorun | Düzeltme |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86045 | -2134351803 | ECS_E_INITIAL_UPLOAD_PENDING | İlk karşıya yükleme devam ettiğinden dosya katmanı başarısız oldu. | Eylem gerekmiyor. İlk karşıya yükleme tamamlandıktan sonra dosya katmanlanacaktır. |
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | Dosya kullanımda olduğundan katmanı açamadı. | Eylem gerekmiyor. Dosya artık kullanımda olmadığında katmanlanır. |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | Dosya, eşitleme tarafından dışlandığından katmana gönderilemedi. | Eylem gerekmiyor. Eşitleme dışlama listesindeki dosyalar katmanlanamaz. |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | Sunucuda bulunamadığı için dosya katmanı başarısız oldu. | Eylem gerekmiyor. Hata devam ederse dosyanın sunucuda mevcut olup olmadığını denetleyin. |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | Azure dosya paylaşımında silindiği için dosya katmanı başarısız oldu. | Eylem gerekmiyor. Bir sonraki indirme eşitleme oturumu çalıştığında dosya sunucudan silinmelidir. |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | Ağ sorunu nedeniyle dosya katmanı başarısız oldu. | Eylem gerekmiyor. Hata devam ederse Azure dosya paylaşımının ağ bağlantısını kontrol edin. |
| 0x80072EE7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | Ağ sorunu nedeniyle dosya katmanı başarısız oldu. | Eylem gerekmiyor. Hata devam ederse Azure dosya paylaşımının ağ bağlantısını kontrol edin. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Erişim reddedildi hatası nedeniyle dosya katmanı başarısız oldu. Bu hata dosyanın bir DFS-R salt okunur çoğaltma klasöründe yer alması nedeniyle oluşabilir. | Azure Dosya Eşitleme DFS-R salt okunur çoğaltma klasörlerindeki sunucu uç noktalarını desteklemez. Daha fazla bilgi için [planlama kılavuzuna](file-sync-planning.md#distributed-file-system-dfs) bakın. |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | Ağ sorunu nedeniyle dosya katmanı başarısız oldu. | Eylem gerekmiyor. Hata devam ederse Azure dosya paylaşımının ağ bağlantısını kontrol edin. |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | Dosya boyutu desteklenen boyuttan daha az olduğu için dosya katmana kopyalanamadı. | Aracı sürümü 9.0 öncesiyse desteklenen en küçük dosya boyutu 64 kb olacaktır. Aracı sürümü 9.0 ve üzeriyse desteklenen en küçük dosya boyutu, dosya sistemi kümesinin boyutuna göre değişir (dosya sistemi kümesinin boyutunun iki katıdır). Örneğin, dosya sistemi kümesi boyutu 4kb ise, en küçük dosya boyutu 8kb 'tır. |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | Azure depolama sorunu nedeniyle dosya katmanı başarısız oldu. | Hata devam ederse bir destek isteği açın. |
| 0x800703E3 | -2147023901 | ERROR_OPERATION_ABORTED | Dosya, aynı anda geri çekilmiş olduğundan katmana gönderilemedi. | Eylem gerekmiyor. Dosya, geri çağırma tamamlandığında ve artık kullanımda olmadığında katmanlanacaktır. |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | Dosya, Azure dosya paylaşımıyla eşitlenmediği için katmanı gerçekleştiremedi. | Eylem gerekmiyor. Dosya, Azure dosya paylaşımıyla eşitlendikten sonra katmanlanır. |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | Bulut katmanlama filtresi sürücüsü (storagesync.sys) çalışmadığından dosya katmanı başarısız oldu. | Bu sorunu çözmek için, yükseltilmiş bir komut istemi açın ve aşağıdaki komutu çalıştırın: `fltmc load storagesync`<br>FltMC komutu çalıştırılırken storagessync filtre sürücüsü yüklenemezse, Azure Dosya Eşitleme aracısını kaldırın, sunucuyu yeniden başlatın ve Azure Dosya Eşitleme aracısını yeniden yükleyin. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Sunucu uç noktasının bulunduğu birimde yetersiz disk alanı nedeniyle dosya katmana gönderilemedi. | Bu sorunu çözmek için sunucu uç noktasının bulunduğu birimde en az 100 MB disk alanını boşaltın. |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | Dosya, Azure dosya paylaşımıyla eşitlenmediği için katmanı gerçekleştiremedi. | Eylem gerekmiyor. Dosya, Azure dosya paylaşımıyla eşitlendikten sonra katmanlanır. |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | Dosya, desteklenmeyen bir yeniden ayrıştırma noktası olduğundan katmana gönderilemedi. | Dosyanın bir Yinelenen Verileri Kaldırma yeniden ayrıştırma noktası olması halinde Yinelenen Verileri Kaldırma desteğini etkinleştirmek için [plan kılavuzundaki](file-sync-planning.md#data-deduplication) adımları izleyin. Yinelenen Verileri Kaldırma haricinde yeniden ayrıştırma noktalarına sahip dosyalar desteklenmez ve katmanlanmaz.  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | Dosya değiştirildiğinden katman başarısız oldu. | Eylem gerekmiyor. Dosya, değiştirilen dosya Azure dosya paylaşımıyla eşitlendikten sonra katmanlanır. |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | Dosya, Azure dosya paylaşımıyla eşitlenmediği için katmanı gerçekleştiremedi. | Eylem gerekmiyor. Dosya, Azure dosya paylaşımıyla eşitlendikten sonra katmanlanır. |
| 0x80072EE2 | -2147012894 | WININET_E_TIMEOUT | Ağ sorunu nedeniyle dosya katmanı başarısız oldu. | Eylem gerekmiyor. Hata devam ederse Azure dosya paylaşımının ağ bağlantısını kontrol edin. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Dosya değiştirildiğinden katman başarısız oldu. | Eylem gerekmiyor. Dosya, değiştirilen dosya Azure dosya paylaşımıyla eşitlendikten sonra katmanlanır. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Yetersiz sistem kaynakları nedeniyle dosya katmanı başarısız oldu. | Hata devam ederse sistem kaynaklarının tüketilmesine hangi uygulamanın veya çekirdek modu sürücüsünün neden olduğunu araştırın. |
| 0x8e5e03fe | -1906441218 | JET_errDiskIO | Bulut katmanlama veritabanına yazılırken bir g/ç hatası nedeniyle dosya katmanı başarısız oldu. | Hata devam ederse, birimde Chkdsk komutunu çalıştırın ve depolama donanımını denetleyin. |
| 0x8e5e0442 | -1906441150 | JET_errInstanceUnavailable | Bulut katmanlama veritabanı çalışmadığından dosya katmanı başarısız oldu. | Bu sorunu çözmek için FileSyncSvc hizmetini veya sunucusunu yeniden başlatın. Hata devam ederse, birimde Chkdsk komutunu çalıştırın ve depolama donanımını denetleyin. |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Geri çağırılabilmesi gereken dosyalar nasıl giderilir?  
Dosyalar geri çekilemez:
1. Olay Görüntüleyicisi, uygulamalar ve Services\microsoft\filesync\agentaltında bulunan telemetri, işletimsel ve Tanılama olay günlüklerini gözden geçirin.
    1. Dosyaların Azure dosya paylaşımında mevcut olduğunu doğrulayın.
    2. Sunucunun internet bağlantısı olduğunu doğrulayın. 
    3. Hizmetler MMC ek bileşenini açın ve depolama eşitleme Aracısı hizmeti 'nin (FileSyncSvc) çalıştığını doğrulayın.
    4. Azure Dosya Eşitleme filtresi sürücülerinin (StorageSync.sys ve StorageSyncGuard.sys) çalıştığını doğrulayın:
        - Yükseltilmiş bir komut isteminde komutunu çalıştırın `fltmc` . StorageSync.sys ve StorageSyncGuard.sys dosya sistemi filtre sürücülerinin listelendiğini doğrulayın.

> [!NOTE]
> Bir dosya geri çağıramazsa (hata kodu başına bir olay günlüğe kaydedilir), telemetri olay günlüğünde bir olay KIMLIĞI 9006 saatte bir kez günlüğe kaydedilir. Hata kodu için düzeltme adımlarının listelenip listelenmediğini görmek için [geri çağırma hataları ve düzeltme](#recall-errors-and-remediation) bölümünü denetleyin.

### <a name="recall-errors-and-remediation"></a>Hataları ve düzeltmeyi geri çağır

| HRESULT | HRESULT (ondalık) | Hata dizesi | Sorun | Düzeltme |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | Bir g/ç zaman aşımı nedeniyle dosya geri çekemedi. Bu sorun çeşitli nedenlerden kaynaklanabilir: sunucu kaynak kısıtlamaları, zayıf ağ bağlantısı veya bir Azure depolama sorunu (örneğin, azaltma). | Eylem gerekmiyor. Hata birkaç saat devam ederse bir destek talebi açın. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | Ağ sorunu nedeniyle dosya geri çekemedi.  | Hata devam ederse Azure dosya paylaşımının ağ bağlantısını kontrol edin. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | Sunucu uç noktası silindiğinden dosya geri çekemedi. | Bu sorunu çözmek için, [sunucu uç noktasını sildikten sonra, sunucuda katmanlı dosyalara erişilebilir değil ' a](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)bakın. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Erişim reddedildi hatası nedeniyle dosya geri çekemedi. Bu sorunun oluşmasının nedeni, depolama hesabında güvenlik duvarı ve sanal ağ ayarlarının etkinleştirilmesi ve sunucunun depolama hesabına erişim sahibi olmamasıdır. | Bu sorunu çözmek için, dağıtım kılavuzu 'ndaki [güvenlik duvarı ve sanal ağ ayarlarını yapılandırma](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) bölümünde belgelenen adımları IZLEYEREK sunucu IP adresini veya sanal ağını ekleyin. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | Azure dosya paylaşımında erişilebilir olmadığından dosya geri çekemedi. | Bu sorunu çözmek için dosyanın Azure dosya paylaşımında mevcut olduğunu doğrulayın. Dosya Azure dosya paylaşımında varsa, en son Azure Dosya Eşitleme [aracı sürümüne](file-sync-release-notes.md#supported-versions)yükseltin. |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | Depolama hesabına yönelik yetkilendirme hatası nedeniyle dosya geri çekemedi. | Bu sorunu çözmek için [Azure dosya eşitleme depolama hesabına erişiminin](?tabs=portal1%252cazure-portal#troubleshoot-rbac)olduğunu doğrulayın. |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | Azure dosya paylaşımının erişimi olmadığından dosya geri çekemedi. | Dosya paylaşımının var olduğunu ve erişilebilir olduğunu doğrulayın. Dosya paylaşma silinmişse ve yeniden oluşturulduysa, eşitleme grubunu silip yeniden oluşturmak için [Azure dosya paylaşımının silindiği ve](?tabs=portal1%252cazure-portal#-2134375810) yeniden oluşturulduğu bir bölüm olduğundan eşitleme sırasında belgelenen adımları gerçekleştirin. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Yetersiz sistem kaynakları nedeniyle dosya geri çekemedi. | Hata devam ederse sistem kaynaklarının tüketilmesine hangi uygulamanın veya çekirdek modu sürücüsünün neden olduğunu araştırın. |
| 0x8007000E | -2147024882 | ERROR_OUTOFMEMORY | Yetersiz bellek nedeniyle dosya geri çekemedi. | Hata devam ederse düşük bellek koşuluna hangi uygulamanın veya çekirdek modu sürücüsünün neden olduğunu araştırın. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Yetersiz disk alanı nedeniyle dosya geri çekemedi. | Bu sorunu çözmek için dosyaları farklı bir birime taşıyarak birimde yer açın, birimin boyutunu artırın veya Invoke-StorageSyncCloudTiering cmdlet'ini kullanarak dosyaları katmanlamaya zorlayın. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>Sunucu uç noktası silindikten sonra katmanlı dosyalara sunucuda erişilemiyor
Sunucu uç noktası silinmeden önce dosyalar geri çağrılmıyorsa, bir sunucudaki katmanlı dosyalar erişilemez duruma gelir.

Katmanlı dosyalar erişilebilir değilse günlüğe kaydedilen hatalar
- Bir dosya eşitlenirken, ıtemresults olay günlüğünde hata kodu-2147942467 (0x80070043-ERROR_BAD_NET_NAME) kaydedilir
- Bir dosyayı geri çekerken hata kodu-2134376393 (0x80c80037-ECS_E_SYNC_SHARE_NOT_FOUND), RecallResults olay günlüğünde günlüğe kaydedilir

Aşağıdaki koşullara uyulursa katmanlanmış dosyalarınıza erişimi geri yüklemek mümkün olabilir:
- Sunucu uç noktası son 30 gün içinde silinmiştir
- Bulut uç noktası silinmemiştir 
- Dosya paylaşımı silinmemiştir
- Eşitleme grubu silinmemiştir

Yukarıdaki koşullara uyulmazsa, 30 gün içinde sunucuda aynı yolda aynı eşitleme grubu içinde sunucu uç noktasını yeniden oluşturarak dosyalara erişimi geri yükleyebilirsiniz. 

Yukarıdaki koşullara uyulmazsa, bu katmanlanmış dosyalar sunucuda yalnız bırakılmış olduğundan erişimi geri yüklemek mümkün olmaz. Yalnız bırakılmış katmanlı dosyaları kaldırmak için aşağıdaki yönergeleri izleyin.

**Notlar**
- Katmanlı dosyalara sunucuda erişilemezse, Azure dosya paylaşımında doğrudan erişmeniz durumunda tam dosya yine de erişilebilir olmalıdır.
- Artık katmanlı dosyaları daha sonra engellemek için sunucu uç noktası silinirken [sunucu uç noktasını kaldırma](file-sync-server-endpoint.md#remove-a-server-endpoint) bölümünde belgelenen adımları izleyin.

<a id="get-orphaned"></a>**Yalnız bırakılmış katmanlı dosyaların listesini alma** 

1. Azure Dosya Eşitleme Agent sürümü v 5.1 veya sonraki bir sürümün yüklü olduğundan emin olun.
2. Yalnız bırakılmış katmanlı dosyaları listelemek için aşağıdaki PowerShell komutlarını çalıştırın:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Dosyaların silindikten sonra yedekten geri yüklenmesi gereken durumlarda OrphanTieredFiles.txt çıktı dosyasını kaydedin.

<a id="remove-orphaned"></a>**Yalnız bırakılmış katmanlı dosyaları kaldırma** 

*Seçenek 1: yalnız bırakılmış katmanlı dosyaları sil*

Bu seçenek, Windows Server 'daki yalnız bırakılmış katmanlı dosyaları siler, ancak 30 gün sonra yeniden oluşturma veya farklı bir eşitleme grubuna bağlanma nedeniyle sunucu uç noktasının kaldırılmasını gerektirir. Sunucu uç noktası yeniden oluşturulmadan önce Windows Server veya Azure dosya paylaşımında dosyalar güncelleştirilirse dosya çakışmaları oluşur.

1. Azure Dosya Eşitleme Agent sürümü v 5.1 veya sonraki bir sürümün yüklü olduğundan emin olun.
2. Azure dosya paylaşımından ve sunucu uç noktası konumunu yedekleyin.
3. [Sunucu uç noktasını kaldırma](file-sync-server-endpoint.md#remove-a-server-endpoint)bölümünde belgelenen adımları izleyerek eşitleme grubundaki sunucu uç noktasını (varsa) kaldırın.

> [!Warning]  
> Sunucu uç noktası Remove-StorageSyncOrphanedTieredFiles cmdlet 'i kullanılmadan önce kaldırılmıyorsa, sunucuda yalnız bırakılmış katmanlı dosyanın silinmesi, Azure dosya paylaşımındaki tam dosyayı siler. 

4. Yalnız bırakılmış katmanlı dosyaları listelemek için aşağıdaki PowerShell komutlarını çalıştırın:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Dosyaların silindikten sonra yedekten geri yüklenmesi gereken durumlarda OrphanTieredFiles.txt çıktı dosyasını kaydedin.
6. Yalnız bırakılmış katmanlı dosyaları silmek için aşağıdaki PowerShell komutlarını çalıştırın:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Notlar** 
- Azure dosya paylaşımıyla eşitlenmemiş sunucuda değiştirilen katmanlı dosyalar silinir.
- Erişilebilen katmanlı dosyalar (sahipsiz değil) silinmez.
- Katmanlı olmayan dosyalar sunucuda kalır.

7. İsteğe bağlı: adım 3 ' te silinmişse sunucu uç noktasını yeniden oluşturun.

*2. seçenek: Azure dosya paylaşımından bağlama ve dosyaları sunucuda yalnız bırakılmış olan yerel olarak kopyalama*

Bu seçenek sunucu uç noktasının kaldırılmasını gerektirmez, ancak tam dosyaları yerel olarak kopyalamak için yeterli disk alanı gerektirir.

1. Azure dosya paylaşımından, yalnız bırakılmış katmanlı dosyalar içeren Windows Server 'a [bağlayın](../files/storage-how-to-use-files-windows.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) .
2. Yalnız bırakılmış katmanlı dosyaları listelemek için aşağıdaki PowerShell komutlarını çalıştırın:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Sunucuda yalnız bırakılmış katmanlı dosyaları belirlemek için OrphanTieredFiles.txt çıktı dosyasını kullanın.
4. Tam dosyayı Azure dosya paylaşımından Windows Server 'a kopyalayarak yalnız bırakılmış katmanlı dosyaların üzerine yazın.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Sunucuda beklenmedik şekilde geri çekilen dosyalar nasıl giderilir?  
Virüsten koruma, yedekleme ve çok sayıda dosyayı okuyan diğer uygulamalar, çevrimdışı özniteliği atla ve bu dosyaların içeriğini okumayı atlayarak istenmeyen geri çekenlere neden olur. Bu seçeneği destekleyen ürünler için çevrimdışı dosyaları atlamak, virüsten koruma taramaları veya yedekleme işleri gibi işlemler sırasında istenmeyen yeniden çağırma olaylarından kaçınılmasına yardımcı olabilir.

Çözümlerinin çevrimdışı dosyaları okumayı atlayacak şekilde yapılandırılması konusunda bilgi almak için yazılım satıcınızla iletişime geçin.

İstenmeyen geri çekmeler, dosya Gezgini 'nde dosyalara göz atarken olduğu gibi diğer senaryolarda da gerçekleşebilir. Dosya Gezgini'nde bulut katmanlı dosyalara sahip bir klasörün açılması, istenmeyen yeniden çağırma işlemlerine neden olabilir. Bu durumun gerçekleşme olasılığı, sunucuda virüsten koruma çözümünün etkinleştirilmiş olması halinde daha yüksektir.

> [!NOTE]
>Hangi uygulamaların geri çekme işlemine neden olduğunu öğrenmek için telemetri olay günlüğünde olay KIMLIĞI 9059 ' yı kullanın. Bu olay, bir sunucu uç noktası için uygulama geri çağırma dağılımı sağlar ve saatte bir kez günlüğe kaydedilir.

### <a name="tls-12-required-for-azure-file-sync"></a>Azure Dosya Eşitleme için TLS 1,2 gerekir

[Kayıt defteri ayarlarına](/windows-server/security/tls/tls-registry-settings)bakarak sunucunuzdaki TLS ayarlarını görüntüleyebilirsiniz. 

Proxy kullanıyorsanız, proxy 'nizin belgelerine başvurun ve TLS 1.2 kullanacak şekilde yapılandırıldığından emin olun.

## <a name="general-troubleshooting"></a>Genel sorun giderme
Sunucuda Azure Dosya Eşitleme sorunlarla karşılaşırsanız, aşağıdaki adımları tamamlayarak başlatın:
1. Olay Görüntüleyicisi, telemetri, işletimsel ve Tanılama olay günlüklerini gözden geçirin.
    - Eşitleme, katmanlama ve geri çağırma sorunları, uygulamalar ve Services\microsoft\filesync\agentaltýnda telemetri, tanılama ve işletimsel olay günlüklerinde günlüğe kaydedilir.
    - Sunucu yönetimiyle ilgili sorunlar (örneğin, yapılandırma ayarları), uygulamalar ve Services\microsoft\filesync\managementaltındaki işletimsel ve Tanılama olay günlüklerine kaydedilir.
2. Sunucuda Azure Dosya Eşitleme hizmetinin çalıştığını doğrulayın:
    - Hizmetler MMC ek bileşenini açın ve depolama eşitleme Aracısı hizmeti 'nin (FileSyncSvc) çalıştığını doğrulayın.
3. Azure Dosya Eşitleme filtresi sürücülerinin (StorageSync.sys ve StorageSyncGuard.sys) çalıştığını doğrulayın:
    - Yükseltilmiş bir komut isteminde komutunu çalıştırın `fltmc` . StorageSync.sys ve StorageSyncGuard.sys dosya sistemi filtre sürücülerinin listelendiğini doğrulayın.

Sorun çözümlenmezse AFSDiag aracını çalıştırın ve. zip dosyası çıkışını daha fazla tanılama için servis talebine atanan destek mühendisine gönderin.

AFSDiag 'ı çalıştırmak için aşağıdaki adımları gerçekleştirin.

Aracı sürümü v11 ve üzeri için:
1. Yükseltilmiş bir PowerShell penceresi açın ve ardından aşağıdaki komutları çalıştırın (her komuttan sonra ENTER tuşuna basın):

    > [!NOTE]
    >AFSDiag, günlükleri toplamadan önce içinde çıkış dizinini ve bir Temp klasörünü oluşturur ve yürütmeden sonra Temp klasörünü siler. Veri içermeyen bir çıkış konumu belirtin.
    
    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-AFS -OutputDirectory C:\output -KernelModeTraceLevel Verbose -UserModeTraceLevel Verbose
    ```

2. Sorunu yeniden üretin. İşiniz bittiğinde **D** girin.
3. Günlükleri ve izleme dosyalarını içeren bir. zip dosyası belirttiğiniz çıkış dizinine kaydedilir. 

Aracı sürümü ile v10 arasındaki ve önceki sürümler için:
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
5. Sorunu yeniden üretin. İşiniz bittiğinde **D** girin.
6. Günlükleri ve izleme dosyalarını içeren bir. zip dosyası belirttiğiniz çıkış dizinine kaydedilir.


## <a name="see-also"></a>Ayrıca bkz.
- [Azure Dosya Eşitleme’yi izleme](file-sync-monitoring.md)
- [Windows’ta Azure Dosyalar sorunlarını giderme](../files/storage-troubleshoot-windows-file-connection-problems.md)
- [Linux’ta Azure Dosyalar sorunlarını giderme](../files/storage-troubleshoot-linux-file-connection-problems.md)