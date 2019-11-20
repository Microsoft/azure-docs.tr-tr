---
title: Azure dosya paylaşımını Windows'da kullanma | Microsoft Docs
description: Azure dosya paylaşımını Windows ve Windows Server ile kullanmayı öğrenin.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 831c771da385ef6faeba194878ca53ede34ccc0a
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816645"
---
# <a name="use-an-azure-file-share-with-windows"></a>Azure dosya paylaşımını Windows'da kullanma
[Azure Dosyaları](storage-files-introduction.md), Microsoft’un kullanımı kolay bulut dosya sistemidir. Azure dosya paylaşımları, Windows ve Windows Server’da sorunsuz bir şekilde kullanılabilir. Bu makalede Azure dosya paylaşımını Windows ve Windows Server ile kullanma konusunda dikkat edilmesi gerekenler anlatılmaktadır.

Bir Azure dosya paylaşımını, barındırıldığı Azure bölgesinin dışında kullanmak için (örneğin, şirket içinde veya farklı bir Azure bölgesinde) işletim sisteminin SMB 3.0'ı desteklemesi gerekir. 

Azure VM üzerinde veya şirket içinde çalışan bir Windows yüklemesinde Azure dosya paylaşımlarını kullanabilirsiniz. Aşağıdaki tabloda, hangi işletim sistemi sürümlerinin hangi ortamlarda dosya paylaşımlarına erişmeyi desteklediği gösterilmektedir:

| Windows sürümü        | SMB sürümü | Azure VM'de Bağlanabilir | Şirket İçinde Bağlanabilir |
|------------------------|-------------|-----------------------|----------------------|
| Windows Server 2019    | SMB 3.0 | Evet | Evet |
| Windows 10<sup>1</sup> | SMB 3.0 | Evet | Evet |
| Windows Server yarı yıllık kanal<sup>2</sup> | SMB 3.0 | Evet | Evet |
| Windows Server 2016    | SMB 3.0     | Evet                   | Evet                  |
| Windows 8.1            | SMB 3.0     | Evet                   | Evet                  |
| Windows Server 2012 R2 | SMB 3.0     | Evet                   | Evet                  |
| Windows Server 2012    | SMB 3.0     | Evet                   | Evet                  |
| Windows 7              | SMB 2.1     | Evet                   | Hayır                   |
| Windows Server 2008 R2 | SMB 2.1     | Evet                   | Hayır                   |

<sup>1</sup> Windows 10, sürüm 1507, 1607, 1703, 1709, 1803, 1809 ve 1903.  
<sup>2</sup> Windows Server, sürüm 1803, 1809 ve 1903.

> [!Note]  
> Her zaman Windows sürümünüz için en yeni KB’yi almanızı öneririz.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar 
* **Depolama hesabı adı**: Bir Azure dosya paylaşımının bağlanması için depolama hesabının adına ihtiyacınız olacaktır.

* **Depolama hesabı anahtarı**: Bir Azure dosya paylaşımının bağlanması için birincil (veya ikincil) depolama anahtarı gerekir. SAS anahtarları şu an bağlama için desteklenmemektedir.

* **Bağlantı noktası 445 ' ün açık olduğundan emin olun**: SMB protokolü, TCP bağlantı noktası 445 ' ün açık olmasını gerektirir; bağlantı noktası 445 engellenmişse, bağlantılar başarısız olur. `Test-NetConnection` cmdlet'ini kullanarak 445 numaralı bağlantı noktasının güvenlik duvarınız tarafından engellenip engellenmediğini görebilirsiniz. [Geçici çözüm 445 bağlantı noktası ' i engelleyen çeşitli yollar](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked)hakkında bilgi edinebilirsiniz.

    Aşağıdaki PowerShell kodunda Azure PowerShell modülünün yüklü olduğu varsayılır, daha fazla bilgi için bkz. [Azure PowerShell modülünü yükleme](https://docs.microsoft.com/powershell/azure/install-az-ps) . `<your-storage-account-name>` ile `<your-resource-group-name>` yerine depolama hesabınızla ilgili bilgileri yazmayı unutmayın.

    ```powershell
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    ```

    Bağlantı başarılı olursa şu çıktıyı görmeniz gerekir:

    ```
    ComputerName     : <storage-account-host-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
    ```

    > [!Note]  
    > Yukarıdaki komut, depolama hesabının geçerli IP adresini döndürür. Bu IP adresinin aynı kalacağı garanti edilmez ve bu adres herhangi bir zamanda değişebilir. Bu IP adresini betiklere veya güvenlik duvarına sabit şekilde kodlamayın. 

## <a name="using-an-azure-file-share-with-windows"></a>Azure dosya paylaşımını Windows'da kullanma
Bir Azure dosya paylaşımını Windows'da kullanmak için bağlayarak bir sürücü harfi veya bağlama noktası yolu atamanız veya [UNC adı](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx) aracılığıyla erişmeniz gerekir. 

Etkileşim kurduğunuz ve Windows Server, Linux Samba sunucusu veya NAS cihazı üzerinde barındırılan diğer SMB paylaşımlarından farklı olarak Azure dosya paylaşımları şu an için Active Directory (AD) veya Azure Active Directory (AAD) kimliğiniz ile Kerberos kimlik doğrulamasını desteklemez ancak bu, [üzerinde çalıştığımız](https://feedback.azure.com/forums/217298-storage/suggestions/6078420-acl-s-for-azurefiles) bir özelliktir. Bunun yerine Azure dosya paylaşımınıza Azure dosya paylaşımınızı içeren depolama hesabına ait depolama hesabı anahtarıyla erişmeniz gerekir. Depolama hesabı anahtarı, bir depolama hesabının yönetici anahtarıdır ve erişim sağladığınız dosya paylaşımı içindeki tüm dosya ve klasörlerin yanı sıra depolama hesabınızdaki tüm dosya paylaşımları ve diğer depolama kaynakları (bloblar, kuyruklar, tablolar vs.) için yönetici izinleri sunar. Bu özellik iş yükünüz için yeterli değilse [Azure Dosya Eşitleme](storage-files-planning.md#data-access-method), AAD tabanlı Kerberos kimlik doğrulaması ve ACL desteği genel kullanıma sunulana kadar Kerberos kimlik doğrulamasının yokluğunu telafi edebilir.

Azure'da SMB dosya paylaşımına ihtiyaç duyan iş kolu (LOB) uygulamalarını kullanıma sunmak için sıklıkla kullanılan model, Azure dosya paylaşımını Azure VM'de ayrılmış bir Windows dosya sunucusu çalıştırmaya alternatif olarak kullanmaktır. Bir iş kolu uygulamasını, Azure dosya paylaşımını kullanacak şekilde yapılandırma sırasında dikkat edilmesi gereken önemli noktalardan biri, çoğu iş kolu uygulamasının VM'nin yönetici hesabı yerine sınırlı sistem izinlerine sahip adanmış hizmet hesabı bağlamında çalıştığıdır. Bu nedenle Azure dosya paylaşımında yönetici hesabı yerine hizmet hesabı bağlamında bağlama yaptığınızdan/kimlik bilgilerini kaydettiğinizden emin olun.

### <a name="persisting-azure-file-share-credentials-in-windows"></a>Azure dosya paylaşımı kimlik bilgilerinin Windows'da kalıcı olmasını sağlama  
[cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) yardımcı programı, depolama hesabı kimlik bilgilerinizi Windows'a kaydetmenizi sağlar. Bu da bir Azure dosya paylaşımına UNC adını kullanarak erişmeye veya Azure dosya paylaşımını bağlamaya çalıştığınızda kimlik bilgilerini belirtmek zorunda kalmayacağınız anlamına gelir. Depolama hesabınızın kimlik bilgilerini kaydetmek için aşağıdaki PowerShell komutlarını çalıştırın ve `<your-storage-account-name>` ile `<your-resource-group-name>` yerine uygun bilgileri girin.

```powershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# These commands require you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The cmdkey utility is a command-line (rather than PowerShell) tool. We use Invoke-Expression to allow us to 
# consume the appropriate values from the storage account variables. The value given to the add parameter of the
# cmdkey utility is the host address for the storage account, <storage-account>.file.core.windows.net for Azure 
# Public Regions. $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign 
# clouds or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Invoke-Expression -Command ("cmdkey /add:$([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) " + `
    "/user:AZURE\$($storageAccount.StorageAccountName) /pass:$($storageAccountKeys[0].Value)")
```

cmdkey yardımcı programının depolama hesabınızın kimlik bilgilerini kaydedip kaydetmediğini doğrulamak için list parametresini kullanabilirsiniz:

```powershell
cmdkey /list
```

Azure dosya paylaşımınızın kimlik bilgileri başarıyla kaydedildiyse beklenen çıktı aşağıdaki şekilde olacaktır (listeye kaydedilmiş ek anahtarlar olabilir):

```
Currently stored credentials:

Target: Domain:target=<storage-account-host-name>
Type: Domain Password
User: AZURE\<your-storage-account-name>
```

Artık kimlik bilgilerini kullanmadan paylaşımı bağlayabilmeniz veya paylaşıma erişebilmeniz gerekir.

#### <a name="advanced-cmdkey-scenarios"></a>Gelişmiş cmdkey senaryoları
cmdkey ile kullanılabilecek iki ek senaryo daha vardır. Bunlardan biri makineye hizmet hesabı gibi farklı bir kullanıcının kimlik bilgilerini kaydetme, diğeri ise PowerShell uzaktan iletişim özellikleriyle kimlik bilgilerini uzaktaki bir makineye kaydetmedir.

Makinede başka bir kullanıcının kimlik bilgilerini kaydetmek oldukça kolaydır. Hesapta oturum açtığınızda aşağıdaki PowerShell komutunu yürütmeniz yeterlidir:

```powershell
$password = ConvertTo-SecureString -String "<service-account-password>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "<service-account-username>", $password
Start-Process -FilePath PowerShell.exe -Credential $credential -LoadUserProfile
```

Bu komut hizmet hesabınızın (veya kullanıcı hesabınızın) kullanıcı bağlamında yeni bir PowerShell penceresi açar. Pencere açıldıktan sonra cmdkey yardımcı programını [yukarıda](#persisting-azure-file-share-credentials-in-windows) anlatılan şekilde kullanabilirsiniz.

cmdkey yardımcı programı, kullanıcı PowerShell uzaktan iletişim özellikleriyle oturum açtığında ekleme işlemleri için dahi kimlik bilgisi deposuna erişime izin vermediğinden kimlik bilgilerinin PowerShell uzaktan iletişim özellikleri kullanılarak uzak makineye kaydedilmesi mümkün değildir. Makinede [Uzak Masaüstü](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windows) ile oturum açmanızı öneririz.

### <a name="mount-the-azure-file-share-with-powershell"></a>Azure dosya paylaşımını PowerShell ile bağlama
Azure dosya paylaşımını bağlamak için aşağıdaki komutları normal (yükseltilmiş olmayan) bir PowerShell oturumundan çalıştırın. `<your-resource-group-name>`, `<your-storage-account-name>`, `<your-file-share-name>` ve `<desired-drive-letter>` yerine gerekli bilgileri eklemeyi unutmayın.

```powershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"
$fileShareName = "<your-file-share-name>"

# These commands require you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object { 
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("Azure file share not found")
}

# The value given to the root parameter of the New-PSDrive cmdlet is the host address for the storage account, 
# <storage-account>.file.core.windows.net for Azure Public Regions. $fileShare.StorageUri.PrimaryUri.Host is 
# used because non-Public Azure regions, such as sovereign clouds or Azure Stack deployments, will have different 
# hosts for Azure file shares (and other storage resources).
$password = ConvertTo-SecureString -String $storageAccountKeys[0].Value -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "AZURE\$($storageAccount.StorageAccountName)", $password
New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\$($fileShare.StorageUri.PrimaryUri.Host)\$($fileShare.Name)" -Credential $credential -Persist
```

> [!Note]  
> `New-PSDrive` cmdlet'inde `-Persist` seçeneğini kullanmak yalnızca kimlik bilgilerinin kaydedilmiş olması durumunda açılışta dosya paylaşımının yeniden bağlanmasını sağlar. Kimlik bilgilerini kaydetmek için cmdkey'i [yukarıda anlatılan şekilde](#persisting-azure-file-share-credentials-in-windows) kullanabilirsiniz. 

İsterseniz aşağıdaki PowerShell cmdlet'ini kullanarak Azure dosya paylaşımını çıkarabilirsiniz.

```powershell
Remove-PSDrive -Name <desired-drive-letter>
```

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Azure dosya paylaşımını Dosya Gezgini ile bağlama
> [!Note]  
> Aşağıdaki yönergelerin Windows 10’da gösterildiğini ve eski sürümlerde biraz değişiklik gösterebileceğini aklınızda bulundurun. 

1. Dosya Gezgini'ni açın. Başlat Menüsünden veya Win+E kısayoluna basarak açılabilir.

2. Pencerenin sol tarafındaki **Bu Bilgisayar** öğesine gidin. Bu, şeritteki kullanılabilir menüleri değiştirir. Bilgisayar menüsünün altındaki **Ağ sürücüsüne bağlan**'ı seçin.
    
    ![“Ağ sürücüsüne bağlan” açılan menüsünün ekran görüntüsü](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

3. Azure portaldaki **Bağlan** bölmesinden UNC yolunu kopyalayın. 

    ![Azure Dosyaları Bağlan bölmesinden UNC adı](./media/storage-how-to-use-files-windows/portal_netuse_connect.png)

4. Sürücü harfini seçin ve UNC adını girin. 
    
    ![“Ağ Sürücüsüne Bağlan” iletişim kutusunun ekran görüntüsü](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

5. Kullanıcı adı olarak, başına `AZURE\` ekleyip depolama hesabı adını ve parola olarak depolama hesabı anahtarını kullanın.
    
    ![Ağ kimlik bilgileri iletişim kutusunun ekran görüntüsü](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

6. Azure Dosya paylaşımını istediğiniz gibi kullanın.
    
    ![Azure dosya paylaşımı artık bağlanmıştır](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

7. Azure Dosya paylaşımını çıkarmaya hazır olduğunuzda, Dosya Gezgini’ndeki **Ağ konumları**'nın altında bulunan girdiye sağ tıklayıp **Bağlantıyı kes**'i seçerek bunu yapabilirsiniz.

### <a name="accessing-share-snapshots-from-windows"></a>Windows'dan paylaşım anlık görüntülerine erişme
El ile veya betik ya da Azure Backup gibi bir hizmet aracılığıyla otomatik olarak paylaşım anlık görüntüsü aldıysanız Windows'da dosya paylaşımından bir paylaşımın, dizinin veya belirli bir dosyanın önceki sürümlerini görüntüleyebilirsiniz. Anlık görüntüyü [Azure portal](storage-how-to-use-files-portal.md), [Azure PowerShell](storage-how-to-use-files-powershell.md) veya [Azure CLI](storage-how-to-use-files-cli.md) ile kaydedebilirsiniz.

#### <a name="list-previous-versions"></a>Önceki sürümleri listeleme
Geri yüklemek istediğiniz öğeye veya üst öğeye gidin. Çift tıklayarak istenen dizine gidin. Sağ tıklayın ve açılan menüden **Özellikler**'i seçin.

![Seçilen dizin için sağ tıklama menüsü](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

Bu dizine ait paylaşım anlık görüntülerinin listesini görmek için **Önceki Sürümler**'i seçin. Ağ hızına ve dizindeki paylaşım anlık görüntüsü sayısına bağlı olarak listenin yüklenmesi birkaç saniye sürebilir.

![Önceki Sürümler sekmesi](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

Belirli bir anlık görüntüyü açmak için **Aç**'ı seçebilirsiniz. 

![Açılan anlık görüntü](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Önceki sürümü geri yükleme
Anlık görüntü oluşturma zamanındaki dizin içeriğinin tamamını özgün konuma yinelemeli bir şekilde kopyalamak için **Geri yükle**'yi seçin.
 ![Uyarı iletisindeki geri yükleme düğmesi](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="securing-windowswindows-server"></a>Windows/Windows Server'ı güvenli hale getirme
Windows'da Azure dosya paylaşımını bağlamak için 445 numaralı bağlantı noktasının erişilebilir olması gerekir. Çoğu kuruluş SMB 1 kaynaklı güvenlik riskleri nedeniyle 445 numaralı bağlantı noktasını engeller. CIFS (Ortak Internet Dosya Sistemi) olarak da bilinen SMB 1, Windows ve Windows Server'da bulunan eski bir dosya sistemi protokolüdür. SMB 1 eski, verimsiz ve hepsinden önemlisi güvenli olmayan bir protokoldür. Neyse ki Azure Dosyalar SMB 1 protokolünü desteklemez ve desteklenen tüm Windows ve Windows Server sürümlerinde SMB 1 protokolünü kaldırmak veya devre dışı bırakmak mümkündür. Azure dosya paylaşımlarını üretim ortamında kullanmaya başlamadan önce SMB 1 istemcisini ve sunucusunu mutlaka kaldırmanızı ve devre dışı bırakmanızı [öneririz](https://aka.ms/stopusingsmb1).

Aşağıdaki tabloda tüm Windows sürümlerinde SMB 1 protokolünün durumu hakkında ayrıntılı bilgiler verilmiştir:

| Windows sürümü                           | SMB 1 protokolünün varsayılan durumu | Devre dışı bırakma/kaldırma yöntemi       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019                       | Devre dışı             | Windows özelliği ile kaldırma |
| Windows Server, sürüm 1709+            | Devre dışı             | Windows özelliği ile kaldırma |
| Windows 10, sürüm 1709+                | Devre dışı             | Windows özelliği ile kaldırma |
| Windows Server 2016                       | Enabled              | Windows özelliği ile kaldırma |
| Windows 10, sürüm 1507, 1607 ve 1703 | Enabled              | Windows özelliği ile kaldırma |
| Windows Server 2012 R2                    | Enabled              | Windows özelliği ile kaldırma | 
| Windows 8.1                               | Enabled              | Windows özelliği ile kaldırma | 
| Windows Server 2012                       | Enabled              | Kayıt defteri ile devre dışı bırakma       | 
| Windows Server 2008 R2                    | Enabled              | Kayıt defteri ile devre dışı bırakma       |
| Windows 7                                 | Enabled              | Kayıt defteri ile devre dışı bırakma       | 

### <a name="auditing-smb-1-usage"></a>SMB 1 kullanımını denetleme
> Windows Server 2019, Windows Server yarı yıllık kanal (sürüm 1709 ve 1803), Windows Server 2016, Windows 10 (sürümler 1507, 1607, 1703, 1709 ve 1803), Windows Server 2012 R2 ve Windows 8.1 için geçerlidir.

SMB 1'i ortamınızdan kaldırmadan önce bu değişiklikten etkilenecek istemciler olup olmadığını görmek için SMB 1 kullanımını denetlemek isteyebilirsiniz. SMB 1 ile yapılan SMB paylaşımı isteği varsa `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit` altında bir denetim olayı kaydedilir. 

> [!Note]  
> Windows Server 2012 R2 ve Windows 8.1'de denetim desteğini etkinleştirmek için en az [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720) sürümünü yükleyin.

Denetimi etkinleştirmek için aşağıdaki cmdlet'i yükseltilmiş PowerShell oturumundan yürütün:

```powershell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>Windows Server'dan SMB 1'i kaldırma
> Windows Server 2019, Windows Server yarı yıllık kanal (sürüm 1709 ve 1803), Windows Server 2016, Windows Server 2012 R2 için geçerlidir

SMB 1'i bir Windows Server örneğinden kaldırmak için aşağıdaki cmdlet'i yükseltilmiş PowerShell oturumundan yürütün:

```powershell
Remove-WindowsFeature -Name FS-SMB1
```

Kaldırma işlemini tamamlamak için sunucunuzu yeniden başlatın. 

> [!Note]  
> Windows 10 ve Windows Server sürüm 1709'dan itibaren SMB 1 varsayılan olarak yüklenmez ve SMB 1 istemcisi ile SMB 1 sunucusu için ayrı Windows özelliklerine sahiptir. SMB 1 sunucusu (`FS-SMB1-SERVER`) ve SMB 1 istemcisi (`FS-SMB1-CLIENT`) özelliklerini yüklememenizi öneririz.

### <a name="removing-smb-1-from-windows-client"></a>Windows istemcisinden SMB 1'i kaldırma
> Windows 10 (sürüm 1507, 1607, 1703, 1709 ve 1803) ve Windows 8.1 için geçerlidir

SMB 1'i bir Windows istemcinizden kaldırmak için aşağıdaki cmdlet'i yükseltilmiş PowerShell oturumundan yürütün:

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

Kaldırma işlemini tamamlamak için bilgisayarınızı yeniden başlatın.

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>SMB 1'i eski Windows/Windows Server sürümlerinde kaldırma
> Windows Server 2012, Windows Server 2008 R2 ve Windows 7 için geçerlidir

SMB 1, eski Windows/Windows Server sürümlerinden tamamen kaldırılamaz ancak Kayıt defteri aracılığıyla devre dışı bırakılabilir. SMB 1'i devre dışı bırakmak için `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters` altında `0` değerine ve `DWORD` türüne sahip yeni bir `SMB1` kayıt defteri anahtarı oluşturun.

Bunu işlemi aşağıdaki PowerShell cmdlet'ini kullanarak da kolayca gerçekleştirebilirsiniz:

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

Bu kayıt defteri anahtarını oluşturduktan sonra SMB 1'i devre dışı bırakmak için sunucunuzu yeniden başlatmanız gerekir.

### <a name="smb-resources"></a>SMB kaynakları
- [SMB 1'i kullanmayı durdurma](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/)
- [SMB 1 Product Clearinghouse](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/)
- [DSCEA ile ortamınızda SMB 1'i keşfetme](https://blogs.technet.microsoft.com/ralphkyttle/2017/04/07/discover-smb1-in-your-environment-with-dscea/)
- [SMB 1'i Grup İlkesi ile devre dışı bırakma](https://blogs.technet.microsoft.com/secguide/2017/06/15/disabling-smbv1-through-group-policy/)

## <a name="next-steps"></a>Sonraki adımlar
Azure Dosyaları hakkında daha fazla bilgi edinmek için şu bağlantılara göz atın:
- [Azure Dosyaları dağıtımını planlama](storage-files-planning.md)
- [SSS](../storage-files-faq.md)
- [Windows’da sorun giderme](storage-troubleshoot-windows-file-connection-problems.md)      
