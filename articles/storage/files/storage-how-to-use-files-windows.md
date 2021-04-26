---
title: Azure dosya paylaşımını Windows'da kullanma | Microsoft Docs
description: Windows ve Windows Server ile Azure dosya paylaşımlarını kullanmayı öğrenin. Şirket içinde veya Azure VM 'lerinde çalışan Windows yüklemelerinde SMB 3,0 ile Azure dosya paylaşımlarını kullanın.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e864dcaa2a611746ae813a4f0adf8409fbc50871
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789798"
---
# <a name="use-an-azure-file-share-with-windows"></a>Azure dosya paylaşımını Windows'da kullanma
[Azure Dosyaları](storage-files-introduction.md), Microsoft’un kullanımı kolay bulut dosya sistemidir. Azure dosya paylaşımları, Windows ve Windows Server’da sorunsuz bir şekilde kullanılabilir. Bu makalede Azure dosya paylaşımını Windows ve Windows Server ile kullanma konusunda dikkat edilmesi gerekenler anlatılmaktadır.

Bir Azure dosya paylaşımını, barındırıldığı Azure bölgesinin dışında kullanmak için (örneğin, şirket içinde veya farklı bir Azure bölgesinde) işletim sisteminin SMB 3.0'ı desteklemesi gerekir. 

Azure VM üzerinde veya şirket içinde çalışan bir Windows yüklemesinde Azure dosya paylaşımlarını kullanabilirsiniz. Aşağıdaki tabloda, hangi işletim sistemi sürümlerinin hangi ortamlarda dosya paylaşımlarına erişmeyi desteklediği gösterilmektedir:

| Windows sürümü        | SMB sürümü | Azure VM'de Bağlanabilir | Şirket içinde takılamaz |
|------------------------|-------------|-----------------------|-----------------------|
| Windows Server 2019 | SMB 3.0 | Yes | Yes |
| Windows 10<sup>1</sup> | SMB 3.0 | Yes | Yes |
| Windows Server yarı yıllık kanal<sup>2</sup> | SMB 3.0 | Yes | Yes |
| Windows Server 2016 | SMB 3.0 | Yes | Yes |
| Windows 8.1 | SMB 3.0 | Yes | Yes |
| Windows Server 2012 R2 | SMB 3.0 | Yes | Yes |
| Windows Server 2012 | SMB 3.0 | Yes | Yes |
| Windows 7<sup>3</sup> | SMB 2.1 | Yes | Hayır |
| Windows Server 2008 R2<sup>3</sup> | SMB 2.1 | Yes | Hayır |

<sup>1</sup> Windows 10, sürüm 1507, 1607, 1803, 1809, 1903, 1909 ve 2004.  
<sup>2</sup> Windows Server, sürüm 1809, 1903, 1909, 2004.  
<sup>3</sup> Windows 7 ve Windows Server 2008 R2 için düzenli Microsoft desteği sona erdi. Güvenlik güncelleştirmeleri için yalnızca [Genişletilmiş Güvenlik Güncelleştirmesi (ESU) programı](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates)aracılığıyla ek destek satın alınabilir. Bu işletim sistemlerinin geçirilmesini kesinlikle öneririz.

> [!Note]  
> Her zaman Windows sürümünüz için en yeni KB’yi almanızı öneririz.

## <a name="prerequisites"></a>Önkoşullar 

445 numaralı bağlantı noktasının açık olduğundan emin olun: SMB protokolü için 445 numaralı TCP bağlantı noktasının açık olması gerekir. 445 numaralı bağlantı noktasının açık olmaması halinde bağlantı gerçekleştirilemez. Güvenlik duvarınızın, cmdlet ile 445 bağlantı noktasını engelleyip engellemediğini kontrol edebilirsiniz `Test-NetConnection` . Engellenen 445 bağlantı noktasına geçici çözüm yolları hakkında bilgi edinmek için, Windows sorun giderme kılavuzumuzdan [1: bağlantı noktası 445 engellendi](storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked) bölümüne bakın.

## <a name="using-an-azure-file-share-with-windows"></a>Azure dosya paylaşımını Windows'da kullanma
Bir Azure dosya paylaşımını Windows'da kullanmak için bağlayarak bir sürücü harfi veya bağlama noktası yolu atamanız veya [UNC adı](/windows/win32/fileio/naming-a-file) aracılığıyla erişmeniz gerekir. 

Bu makale, dosya paylaşımıyla erişmek için depolama hesabı anahtarını kullanır. Depolama hesabı anahtarı, eriştiğiniz dosya paylaşımındaki tüm dosya ve klasörlere yönelik yönetici izinleri ve depolama hesabınızda bulunan tüm dosya paylaşımları ve diğer depolama kaynakları (blob 'lar, kuyruklar, tablolar vb.) dahil olmak üzere bir depolama hesabı için yönetici anahtarıdır. İş yükünüz için yeterli değilse, [Azure dosya eşitleme](../file-sync/file-sync-planning.md) KULLANILABILIR veya [SMB üzerinden kimlik tabanlı kimlik doğrulaması](storage-files-active-directory-overview.md)kullanabilirsiniz.

Azure'da SMB dosya paylaşımına ihtiyaç duyan iş kolu (LOB) uygulamalarını kullanıma sunmak için sıklıkla kullanılan model, Azure dosya paylaşımını Azure VM'de ayrılmış bir Windows dosya sunucusu çalıştırmaya alternatif olarak kullanmaktır. Bir iş kolu uygulamasını, Azure dosya paylaşımını kullanacak şekilde yapılandırma sırasında dikkat edilmesi gereken önemli noktalardan biri, çoğu iş kolu uygulamasının VM'nin yönetici hesabı yerine sınırlı sistem izinlerine sahip adanmış hizmet hesabı bağlamında çalıştığıdır. Bu nedenle Azure dosya paylaşımında yönetici hesabı yerine hizmet hesabı bağlamında bağlama yaptığınızdan/kimlik bilgilerini kaydettiğinizden emin olun.

### <a name="mount-the-azure-file-share"></a>Azure dosya paylaşımından bağlama

Azure portal, dosya paylaşımınızı doğrudan bir konağa bağlamak için kullanabileceğiniz bir betik sağlar. Bu belirtilen betiği kullanmanızı öneririz.

Bu betiği almak için:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. Bağlamak istediğiniz dosya paylaşımının bulunduğu depolama hesabına gidin.
1. **Dosya paylaşımları**’nı seçin.
1. Bağlamak istediğiniz dosya payını seçin.

    :::image type="content" source="media/storage-how-to-use-files-windows/select-file-shares.png" alt-text="Dosya paylaşımları dikey penceresinin ekran görüntüsü, dosya paylaşımı vurgulanır.":::

1. **Bağlan**’ı seçin.

    :::image type="content" source="media/storage-how-to-use-files-windows/file-share-connect-icon.png" alt-text="Dosya paylaşımınız için Bağlan simgesinin ekran görüntüsü.":::

1. Paylaşımın bağlanması için sürücü harfini seçin.
1. Belirtilen betiği kopyalayın.

    :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="Bağlan dikey penceresinin ekran görüntüsü, betikteki kopyalama düğmesi vurgulanır.":::

1. Betiği, dosya paylaşımının üzerine bağlamak istediğiniz konaktaki bir kabuğa yapıştırın ve çalıştırın.

Azure dosya paylaşımınızı artık taktıysanız.

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Azure dosya paylaşımını Dosya Gezgini ile bağlama
> [!Note]  
> Aşağıdaki yönergelerin Windows 10’da gösterildiğini ve eski sürümlerde biraz değişiklik gösterebileceğini aklınızda bulundurun. 

1. Dosya Gezgini'ni açın. Başlat Menüsünden veya Win+E kısayoluna basarak açılabilir.

1. Pencerenin sol tarafındaki **Bu bilgisayara** gidin. Bu, şeritteki kullanılabilir menüleri değiştirir. Bilgisayar menüsünde, **Ağ Sürücüsüne Bağlan**' ı seçin.
    
    ![“Ağ sürücüsüne bağlan” açılan menüsünün ekran görüntüsü](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. Sürücü harfini seçin ve UNC yolu biçiminde bir UNC yolu girin `\\<storageAccountName>.file.core.windows.net\<fileShareName>` . Örneğin: `\\anexampleaccountname.file.core.windows.net\example-share-name`.
    
    ![“Ağ Sürücüsüne Bağlan” iletişim kutusunun ekran görüntüsü](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. Kullanıcı adı olarak, başına `AZURE\` ekleyip depolama hesabı adını ve parola olarak depolama hesabı anahtarını kullanın.
    
    ![Ağ kimlik bilgileri iletişim kutusunun ekran görüntüsü](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. Azure Dosya paylaşımını istediğiniz gibi kullanın.
    
    ![Azure dosya paylaşımı artık bağlanmıştır](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. Azure Dosya paylaşımını çıkarmaya hazır olduğunuzda, Dosya Gezgini’ndeki **Ağ konumları**'nın altında bulunan girdiye sağ tıklayıp **Bağlantıyı kes**'i seçerek bunu yapabilirsiniz.

### <a name="accessing-share-snapshots-from-windows"></a>Windows'dan paylaşım anlık görüntülerine erişme
El ile veya betik ya da Azure Backup gibi bir hizmet aracılığıyla otomatik olarak paylaşım anlık görüntüsü aldıysanız Windows'da dosya paylaşımından bir paylaşımın, dizinin veya belirli bir dosyanın önceki sürümlerini görüntüleyebilirsiniz. [Azure PowerShell](storage-how-to-use-files-powershell.md), [Azure CLI](storage-how-to-use-files-cli.md)veya [Azure Portal](storage-how-to-use-files-portal.md)kullanarak bir paylaşma anlık görüntüsü alabilirsiniz.

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
| Windows Server 2016                       | Etkin              | Windows özelliği ile kaldırma |
| Windows 10, sürüm 1507, 1607 ve 1703 | Etkin              | Windows özelliği ile kaldırma |
| Windows Server 2012 R2                    | Etkin              | Windows özelliği ile kaldırma | 
| Windows 8.1                               | Etkin              | Windows özelliği ile kaldırma | 
| Windows Server 2012                       | Etkin              | Kayıt defteri ile devre dışı bırakma       | 
| Windows Server 2008 R2                    | Etkin              | Kayıt defteri ile devre dışı bırakma       |
| Windows 7                                 | Etkin              | Kayıt defteri ile devre dışı bırakma       | 

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
- [DSCEA ile ortamınızda SMB 1'i keşfetme](/archive/blogs/ralphkyttle/discover-smb1-in-your-environment-with-dscea)
- [SMB 1'i Grup İlkesi ile devre dışı bırakma](/archive/blogs/secguide/disabling-smbv1-through-group-policy)

## <a name="next-steps"></a>Sonraki adımlar
Azure Dosyaları hakkında daha fazla bilgi edinmek için şu bağlantılara göz atın:
- [Azure Dosyalar dağıtımını planlama](storage-files-planning.md)
- [SSS](./storage-files-faq.md)
- [Windows’da sorun giderme](storage-troubleshoot-windows-file-connection-problems.md)