---
title: Kayıtlı sunucuları Azure Dosya Eşitleme yönetme | Microsoft Docs
description: Windows Server 'ı Azure Dosya Eşitleme depolama eşitleme hizmeti ile kaydetmeyi ve kaydını silmeyi öğrenin.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9bbeda33f25aec15124bacb605513a3c52c3f07e
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699265"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Kayıtlı sunucuları Azure Dosya Eşitleme yönetme
Azure Dosya Eşitleme aracısı şirket içi dosya sunucularının sağladığı esneklik, performans ve uyumluluk özelliklerinden vazgeçmeden kuruluşunuzun dosya paylaşımlarını Azure Dosyaları'nda toplamanızı sağlar. Bunu, Windows sunucularınızı Azure dosya paylaşımınızın hızlı bir önbelleğine dönüştürerek yapar. Verilere yerel olarak erişmek için Windows Server üzerinde kullanılabilen tüm protokolleri (SMB, NFS ve FTPS gibi) kullanabilir ve dünya çapında istediğiniz sayıda önbellek oluşturabilirsiniz.

Aşağıdaki makalede, bir sunucunun depolama eşitleme hizmeti ile nasıl kaydedileceği ve yönetileceği gösterilmektedir. Azure Dosya Eşitleme uçtan uca dağıtma hakkında bilgi için bkz. [Azure dosya eşitleme dağıtma](storage-sync-files-deployment-guide.md) .

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Depolama eşitleme hizmeti ile bir sunucuyu kaydetme/kaydını kaldırma
Bir sunucuyu Azure Dosya Eşitleme kaydetme, Windows Server ile Azure arasında bir güven ilişkisi kurar. Bu ilişki daha sonra sunucuda *sunucu uç noktaları* oluşturmak için kullanılabilir. Bu, bir Azure dosya paylaşımıyla ( *bulut uç noktası*olarak da bilinir) eşitlenmesi gereken belirli klasörleri temsil eder. 

### <a name="prerequisites"></a>Önkoşullar
Bir sunucuyu depolama eşitleme hizmeti ile kaydetmek için öncelikle sunucunuzu gerekli önkoşullara hazırlamanız gerekir:

* Sunucunuz Windows Server 'ın desteklenen bir sürümünü çalıştırıyor olmalıdır. Daha fazla bilgi için bkz. [sistem gereksinimleri ve birlikte çalışabilirlik Azure dosya eşitleme](storage-sync-files-planning.md#azure-file-sync-system-requirements-and-interoperability).
* Depolama eşitleme hizmeti 'nin dağıtıldığından emin olun. Depolama eşitleme hizmeti dağıtma hakkında daha fazla bilgi için bkz. [nasıl dağıtılır Azure dosya eşitleme](storage-sync-files-deployment-guide.md).
* Sunucusunun İnternet 'e bağlı olduğundan ve Azure 'un erişilebilir olduğundan emin olun.
* Yöneticiler için IE artırılmış güvenlik yapılandırmasını Sunucu Yöneticisi Kullanıcı arabirimine devre dışı bırakın.
    
    ![IE artırılmış güvenlik yapılandırması vurgulanmış şekilde Kullanıcı arabirimi Sunucu Yöneticisi](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Azure PowerShell modülünün sunucunuzda yüklü olduğundan emin olun. Sunucunuz bir yük devretme kümesinin üyesiyse, kümedeki her düğüm az modül gerektirir. Az modülün nasıl yükleneceğine ilişkin daha fazla ayrıntı [Azure PowerShell yüklemek ve yapılandırmak](https://docs.microsoft.com/powershell/azure/install-Az-ps)için bulunabilir.

    > [!Note]  
    > Bir sunucuyu kaydetmek/kaydını silmek için az PowerShell modülünün en yeni sürümünü kullanmanızı öneririz. Az Package bu sunucuya daha önce yüklenmişse (ve bu sunucudaki PowerShell sürümü 5. * veya daha büyükse), bu paketi güncelleştirmek için `Update-Module` cmdlet 'ini kullanabilirsiniz. 
* Ortamınızda bir ağ proxy sunucusu kullanıyorsanız, eşitleme aracısının kullanmasını sağlamak için sunucunuzdaki proxy ayarlarını yapılandırın.
    1. Proxy IP adresinizi ve bağlantı noktası numaranızı belirleme
    2. Şu iki dosyayı düzenleyin:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Yukarıdaki iki dosyada, Şekil 1 ' deki/System.ServiceModel altındaki satırları (Bu bölümün altında), 127.0.0.1:8888 değerini doğru IP adresine (127.0.0.1 değiştirin) ve doğru bağlantı noktası numarasını (8888 değiştirin) ekleyin:
    4. WinHTTP proxy ayarlarını komut satırı ile ayarlayın:
        * Proxy 'yi göster: netsh WinHTTP proxy 'yi göster
        * Proxy 'yi ayarlama: netsh WinHTTP set proxy 127.0.0.1:8888
        * Proxy 'yi sıfırlayın: netsh WinHTTP Reset proxy
        * Aracı yüklendikten sonra bu kurulum ise, eşitleme aracımızı yeniden başlatın: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Depolama eşitleme hizmeti ile bir sunucu kaydetme
Bir sunucunun bir Azure Dosya Eşitleme *eşitleme grubunda* *sunucu uç noktası* olarak kullanılabilmesi Için, bir *depolama eşitleme hizmeti*ile kaydedilmesi gerekir. Bir sunucu, tek seferde yalnızca bir depolama eşitleme hizmeti ile kaydedilebilir.

#### <a name="install-the-azure-file-sync-agent"></a>Azure Dosya Eşitleme aracısını yükleme
1. [Azure dosya eşitleme aracısını indirin](https://go.microsoft.com/fwlink/?linkid=858257).
2. Azure Dosya Eşitleme Aracısı yükleyicisini başlatın.
    
    ![Azure Dosya Eşitleme Aracısı yükleyicisinin ilk bölmesi](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Azure Dosya Eşitleme aracısında Microsoft Update kullanarak güncelleştirmeleri etkinleştirdiğinizden emin olun. Sunucu paketine yönelik kritik güvenlik düzeltmeleri ve özellik geliştirmeleri Microsoft Update aracılığıyla sevk edildiğinden bu önemlidir.

    ![Azure Dosya Eşitleme Aracısı yükleyicisinin Microsoft Update bölmesinde Microsoft Update etkinleştirildiğinden emin olun](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Sunucu daha önce kaydedilmemişse, sunucu kayıt Kullanıcı Arabirimi yükleme tamamlandıktan hemen sonra açılır.

> [!Important]  
> Sunucu bir yük devretme kümesinin üyesiyse, Azure Dosya Eşitleme aracısının kümedeki her düğüme yüklenmesi gerekir.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Sunucu kaydı kullanıcı arabirimini kullanarak sunucuyu kaydetme
> [!Important]  
> Bulut çözümü sağlayıcısı (CSP) abonelikleri, sunucu kaydı kullanıcı arabirimini kullanamaz. Bunun yerine PowerShell kullanın (Bu bölümün altında).

1. Sunucu kaydı kullanıcı arabirimi, Azure Dosya Eşitleme aracısının yüklenmesi tamamlandıktan hemen sonra başlamazsa, yürüterek `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`el ile başlatılabilir.
2. Azure aboneliğinize erişmek için *oturum aç* ' a tıklayın. 

    ![Sunucu kaydı kullanıcı arabiriminin açma iletişim kutusu](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. İletişim kutusundan doğru abonelik, kaynak grubu ve depolama eşitleme hizmeti ' ni seçin.

    ![Depolama eşitleme hizmeti bilgileri](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. Önizlemede, işlemin tamamlanabilmesi için bir oturum açma işlemi gerekir. 

    ![Oturum Aç iletişim kutusu](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Sunucu bir yük devretme kümesinin üyesiyse, her sunucunun sunucu kaydını çalıştırması gerekir. Azure portalında kayıtlı sunucuları görüntülediğinizde, Azure Dosya Eşitleme her bir düğümü aynı yük devretme kümesinin bir üyesi olarak otomatik olarak tanır ve bunları uygun şekilde gruplandırır.

#### <a name="register-the-server-with-powershell"></a>Sunucuyu PowerShell 'e kaydetme
Ayrıca, PowerShell aracılığıyla sunucu kaydı gerçekleştirebilirsiniz. Bu, bulut çözümü sağlayıcısı (CSP) abonelikleri için sunucu kaydı 'nın yalnızca desteklenen yoludur:

```powershell
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Depolama eşitleme hizmeti ile sunucu kaydını silme
Bir depolama eşitleme hizmeti ile bir sunucunun kaydını silmek için gereken birkaç adım vardır. Bir sunucunun düzgün şekilde kaydını silme bölümüne göz atalım.

> [!Warning]  
> Eşitleme, bulut katmanlama veya bir Microsoft mühendis tarafından açıkça belirtilmedikçe sunucu uç noktalarını kaldırma ve yeniden oluşturma ile Azure Dosya Eşitleme ile ilgili sorunları gidermeye çalışmayın. Sunucunun kaydını silme ve sunucu uç noktalarını kaldırma, bir bozucu işlemdir ve sunucu uç noktalarına sahip birimlerdeki katmanlı dosyalar, kayıtlı sunucu ve sunucu uç noktaları için Azure dosya paylaşımındaki konumlara "yeniden bağlanmaz" yeniden oluşturulur, bu da eşitleme hatalarına neden olur. Ayrıca, sunucu uç noktası ad alanı dışında bulunan katmanlı dosyalar kalıcı olarak kaybolabilir. Bulut katmanlaması hiç etkinleştirilmediği halde, katmanlı dosyalar sunucu uç noktalarında bulunabilir.

#### <a name="optional-recall-all-tiered-data"></a>Seçim Tüm katmanlı verileri geri çek
Azure Dosya Eşitleme kaldırıldıktan sonra katmanlı olan dosyaların (yani, test, ortam değil) kullanılabilir olmasını istiyorsanız, sunucu uç noktaları içeren her bir birimdeki tüm dosyaları geri çekin. Tüm sunucu uç noktaları için bulut katmanlamayı devre dışı bırakın ve ardından aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Sunucu uç noktasını barındıran yerel birimde, tüm katmanlı verileri yeniden `Invoke-StorageSyncFileRecall` çağırmak için yeterli boş alan yoksa cmdlet başarısız olur.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Sunucuyu tüm eşitleme gruplarından kaldır
Depolama eşitleme hizmetindeki sunucunun kaydını kaldırmadan önce, o sunucudaki tüm sunucu uç noktaları kaldırılmalıdır. Bu, Azure portal aracılığıyla yapılabilir:

1. Sunucunuzun kaydedildiği depolama Eşitleme hizmetine gidin.
2. Bu sunucu için tüm sunucu uç noktalarını depolama eşitleme hizmetindeki her bir eşitleme grubunda kaldırın. Bu, eşitleme grubu bölmesindeki ilgili sunucu uç noktasına sağ tıklanarak gerçekleştirilebilir.

    ![Bir eşitleme grubundan sunucu uç noktası kaldırılıyor](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Bu, basit bir PowerShell betiği ile de gerçekleştirilebilir:

```powershell
Connect-AzAccount

$storageSyncServiceName = "<your-storage-sync-service>"
$resourceGroup = "<your-resource-group>"

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $storageSyncServiceName | ForEach-Object { 
    $syncGroup = $_; 
    Get-AzStorageSyncServerEndpoint -ParentObject $syncGroup | Where-Object { $_.ServerEndpointName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzStorageSyncServerEndpoint -InputObject $_ 
    } 
}
```

#### <a name="unregister-the-server"></a>Sunucunun kaydını kaldır
Artık tüm veriler geri çekilir ve sunucu tüm eşitleme gruplarından kaldırıldığına göre, sunucunun kaydı silinmiş olabilir. 

1. Azure portal, depolama eşitleme hizmeti 'nin *kayıtlı sunucular* bölümüne gidin.
2. Kaydını kaldırmak istediğiniz sunucuya sağ tıklayın ve "sunucu kaydını kaldır" a tıklayın.

    ![Sunucunun kaydını kaldır](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Azure Dosya Eşitleme, veri merkezinizde iyi bir komşu bir komşuyu sağlamaktır 
Azure Dosya Eşitleme, veri merkezinizde çalışan tek hizmet olduğundan, Azure Dosya Eşitleme ağ ve depolama kullanımını sınırlamak isteyebilirsiniz.

> [!Important]  
> Sınırları çok düşük olarak ayarlamak, Azure Dosya Eşitleme eşitlemenin ve geri çekmenin performansını etkiler.

### <a name="set-azure-file-sync-network-limits"></a>Azure Dosya Eşitleme ağ sınırlarını ayarla
`StorageSyncNetworkLimit` Cmdlet 'lerini kullanarak Azure dosya eşitleme ağ kullanımını azallendirebilirsiniz.

> [!Note]  
> Katmanlı bir dosyaya erişildiğinde veya Invoke-StorageSyncFileRecall cmdlet 'i kullanıldığında ağ sınırları uygulanmaz.

Örneğin, Azure Dosya Eşitleme çalışma haftası sırasında 9 ila 5 pm (17:00h) arasında 10 Mbps 'den fazlasını kullanmadığından emin olmak için yeni bir kısıtlama sınırı oluşturabilirsiniz: 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Aşağıdaki cmdlet 'i kullanarak sınırınızı görebilirsiniz:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Ağ sınırlarını kaldırmak için kullanın `Remove-StorageSyncNetworkLimit`. Örneğin, aşağıdaki komut tüm ağ sınırlarını kaldırır:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Windows Server Depolama QoS kullanma 
Azure Dosya Eşitleme, Windows Server Sanallaştırma Ana bilgisayarı üzerinde çalışan bir sanal makinede barındırıldığı zaman, depolama ıO kullanımını düzenlemek için Depolama QoS (hizmet kalitesi) kullanabilirsiniz. Depolama QoS ilkesi, en yüksek (ya da StorageSyncNetwork sınırının üzerinde nasıl zorlandığından) veya minimum (veya rezervasyon) olarak ayarlanabilir. En yüksek yerine en düşük değeri ayarlamak Azure Dosya Eşitleme, diğer iş yükleri bunu kullanmadığı takdirde kullanılabilir depolama bant genişliğini kullanmak için veri bloğu kullanmasına izin verir. Daha fazla bilgi için bkz. [depolama hizmet kalitesi](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Ayrıca bkz.
- [Bir Azure dosya eşitleme dağıtımı planlama](storage-sync-files-planning.md)
- [Azure Dosya Eşitleme’yi dağıtma](storage-sync-files-deployment-guide.md)
- [Azure Dosya Eşitleme’yi izleme](storage-sync-files-monitoring.md)
- [Azure Dosya Eşitleme ile ilgili sorunları giderme](storage-sync-files-troubleshoot.md)
