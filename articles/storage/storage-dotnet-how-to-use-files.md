---
title: "Windows’da Azure Dosya Depolama’yı kullanmaya başlama | Microsoft Belgeleri"
description: "Azure File Storage ile verileri bulutta depolayın ve Azure Virtual Machine (VM) veya Windows çalıştıran şirket içi bir uygulamadan buluta bir dosya paylaşımı bağlayın."
services: storage
documentationcenter: .net
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6a889ee1-1e60-46ec-a592-ae854f9fb8b6
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/27/2017
ms.author: renash
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: fcdeac53c79551000b48a47a1afc65e082bcc692
ms.lasthandoff: 03/28/2017


---
# <a name="get-started-with-azure-file-storage-on-windows"></a>.NET kullanarak Azure File Storage’ı kullanmaya başlayın
[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../includes/storage-check-out-samples-dotnet.md)]

[!INCLUDE [storage-file-overview-include](../../includes/storage-file-overview-include.md)]

Dosya paylaşımını Linux ile kullanma hakkında bilgi edinmek için bkz. [Azure File Storage’ı Linux ile kullanma](storage-how-to-use-files-linux.md).

File Storage’ın ölçeklenebilirlik ve performans hedefleri hakkında ayrıntılar için bkz. [Azure Storage Ölçeklenebilirlik ve Performans Hedefleri](storage-scalability-targets.md#scalability-targets-for-blobs-queues-tables-and-files).

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[!INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

## <a name="video-using-azure-file-storage-with-windows"></a>Video: Azure File Storage’ı Windows ile kullanma
Aşağıdaki videoda Windows’da Azure Dosya paylaşımlarının nasıl oluşturulacağı ve kullanılacağı gösterilir.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-File-Storage-with-Windows/player]
> 
> 

## <a name="about-this-tutorial"></a>Bu öğretici hakkında
Bu başlangıç öğreticisinde, Microsoft Azure File Storage’ı kullanma hakkında temel bilgiler verilir. Bu öğreticide, şunları yapacağız:

* Azure portalını veya PowerShell’i kullanarak yeni bir Azure Dosya paylaşımı oluşturacak, dizin ekleyecek, paylaşıma yerel bir dosya yükleyecek ve dizindeki dosyaları listeleyeceğiz.
* Herhangi bir SMB paylaşımı bağlar gibi, dosya paylaşımını bağlayacağız.
* .NET’in şirket içi bir uygulamadan dosya paylaşımına erişmesi için Azure Storage İstemci Kitaplığı’nı kullanacağız. Bir konsol uygulaması oluşturacak ve dosya paylaşımı ile şu eylemleri gerçekleştireceksiniz:
  * Paylaşımdaki bir dosyanın konsol penceresine içeriğini yazma.
  * Dosya paylaşımı için kota (en fazla boyut) ayarlama.
  * Paylaşımda tanımlı bir paylaşılan erişim ilkesi kullanan bir dosya için paylaşılan erişim imzası oluşturma.
  * Bir dosyayı aynı depolama hesabındaki başka bir dosyaya kopyalama.
  * Bir dosyayı aynı depolama hesabındaki bir bloba kopyalama.
* Sorun giderme için Azure Storage Ölçümleri’ni kullanacağız.

File Storage artık tüm depolama hesaplarında desteklenir. Bu sayede, ister mevcut bir depolama hesabını kullanabilir, isterseniz de yeni bir tane oluşturabilirsiniz. Yeni depolama hesabı oluşturma hakkında bilgi edinmek için bkz. [Depolama hesabı oluşturma](storage-create-storage-account.md#create-a-storage-account).

## <a name="use-the-azure-portal-to-manage-a-file-share"></a>Bir dosya paylaşımını yönetmek için Azure portalını kullanma
[Azure portalı](https://portal.azure.com), müşterilerin dosya paylaşımlarını yönetebilmesi için bir kullanıcı arabirimi sunar. Portaldan şunları yapabilirsiniz:

* Dosya paylaşımınızı oluşturma
* Dosya paylaşımınızdan veya dosya paylaşımınıza dosya yükleme ve indirme
* Her bir dosya paylaşımının gerçek kullanımını izleme
* Paylaşım boyutu kotasını ayarlama
* Dosya paylaşımını bağlamak üzere bir Windows istemcisinden `net use` komutunu alma

### <a name="create-file-share"></a>Dosya paylaşımı oluşturma
1. Azure Portal’da oturum açın.
2. Gezinti menüsünde, **Depolama hesapları** veya **Depolama hesapları (klasik)** seçeneğine tıklayın.
   
    ![Portalda dosya paylaşımı oluşturmayı gösteren ekran görüntüsü](./media/storage-dotnet-how-to-use-files/files-create-share-0.png)
3. Depolama hesabınızı seçin.
   
    ![Portalda dosya paylaşımı oluşturmayı gösteren ekran görüntüsü](./media/storage-dotnet-how-to-use-files/files-create-share-1.png)
4. "Dosyalar" hizmetini seçin.
   
    ![Portalda dosya paylaşımı oluşturmayı gösteren ekran görüntüsü](./media/storage-dotnet-how-to-use-files/files-create-share-2.png)
5. "Dosya paylaşımları" seçeneğine tıklayın ve ilk dosya paylaşımınızı oluşturmak üzere bağlantıyı takip edin.
   
    ![Portalda dosya paylaşımı oluşturmayı gösteren ekran görüntüsü](./media/storage-dotnet-how-to-use-files/files-create-share-3.png)
6. İlk dosya paylaşımınızı oluşturmak üzere dosya paylaşımının adını ve boyutunu (en fazla 5120 GB) girin. Dosya paylaşımı oluşturulduktan sonra, SMB 2.1 veya SMB 3.0 destekleyen herhangi bir dosya sisteminden bağlayabilirsiniz.
   
    ![Portalda dosya paylaşımı oluşturmayı gösteren ekran görüntüsü](./media/storage-dotnet-how-to-use-files/files-create-share-4.png)

### <a name="upload-and-download-files"></a>Dosyaları yükleme ve indirme
1. Zaten oluşturduğunuz bir dosya paylaşımını seçin.
   
    ![Portaldan dosya yükleme ve indirmeyi gösteren ekran görüntüsü](./media/storage-dotnet-how-to-use-files/files-upload-download-1.png)
2. Yüklenen dosyalar için kullanıcı arabirimini açmak üzere **Yükle**’ye tıklayın.
   
    ![Portaldan dosya yüklemeyi gösteren ekran görüntüsü](./media/storage-dotnet-how-to-use-files/files-upload-download-2.png)
3. Bir dosyaya sağ tıklayın ve yerel makineye indirmek için **İndir**’e tıklayın.
   
    ![Portaldan dosya indirmeyi gösteren ekran görüntüsü](./media/storage-dotnet-how-to-use-files/files-upload-download-3.png)

### <a name="manage-file-share"></a>Dosya paylaşımı yönetme
1. Dosya paylaşımı boyutunu (en fazla 5120 GB) değiştirmek **Kota**’ya tıklayın.
   
    ![Dosya paylaşımının kotasını yapılandırmayı gösteren ekran görüntüsü](./media/storage-dotnet-how-to-use-files/files-manage-1.png)
2. Dosya paylaşımını bağlamak üzere Windows’dan komut satırı almak için **Bağlan**’a tıklayın.
   
    ![Dosya paylaşımını bağlamayı gösteren ekran görüntüsü](./media/storage-dotnet-how-to-use-files/files-manage-2.png)
   
    ![Dosya paylaşımını bağlamayı gösteren ekran görüntüsü](./media/storage-dotnet-how-to-use-files/files-manage-3.png)
   
   > [!TIP]
   > Bağlama için depolama hesabı erişim anahtarını bulmak üzere, depolama hesabınızda **Ayarlar**’a ve ardından **Erişim anahtarları**’na tıklayın.
   > 
   > 
   
    ![Depolama hesabı erişim anahtarı bulmayı gösteren ekran görüntüsü](./media/storage-dotnet-how-to-use-files/files-manage-4.png)
   
    ![Depolama hesabı erişim anahtarı bulmayı gösteren ekran görüntüsü](./media/storage-dotnet-how-to-use-files/files-manage-5.png)

## <a name="use-powershell-to-manage-a-file-share"></a>Dosya paylaşımı yönetmek için PowerShell’i kullanma
Alternatif olarak, dosya paylaşımları oluşturma ve yönetmek için Azure PowerShell’i de kullanabilirsiniz.

### <a name="install-the-powershell-cmdlets-for-azure-storage"></a>Azure Storage için PowerShell cmdlet’leri yükleme
PowerShell’i kullanmaya hazırlamak için Azure PowerShell cmdlet’lerini indirin ve yükleyin. Yükleme noktası ve yükleme yönergeleri için bkz. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> En güncel Azure PowerShell modülünü indirmeniz ve yüklemeniz veya yükseltmeniz önerilir.
> 
> 

**Başlat**’a tıklayıp **Windows PowerShell** yazarak Azure PowerShell penceresini açın. PowerShell penceresi sizin için Azure Powershell modülünü yükler.

### <a name="create-a-context-for-your-storage-account-and-key"></a>Depolama hesabınız ve anahtarı için bir bağlam oluşturma
Şimdi depolama hesabı bağlamını oluşturun. Bağlam, depolama hesabı adını ve hesap anahtarını kapsar. Hesap anahtarını [Azure portalından](https://portal.azure.com) kopyalama yönergeleri için bkz. [Depolama erişim anahtarlarını görüntüleme ve kopyalama](storage-create-storage-account.md#view-and-copy-storage-access-keys).

`storage-account-name` ve `storage-account-key` değerlerini aşağıdaki örnekte olduğu gibi depolama hesabı adı ve anahtarınız ile değiştirin.

```powershell
# create a context for account and key
$ctx=New-AzureStorageContext storage-account-name storage-account-key
```

### <a name="create-a-new-file-share"></a>Yeni dosya paylaşımı oluşturma
Daha sonra, `logs` adında yeni bir paylaşım oluşturun.

```powershell
# create a new share
$s = New-AzureStorageShare logs -Context $ctx
```

Artık, File Storage’da bir dosya paylaşımınız bulunur. Şimdi, bir dizin ve dosya ekleyeceğiz.

> [!IMPORTANT]
> Dosya paylaşımınızın adı küçük harflerden oluşmalıdır. Dosya paylaşımlarının ve dosyaların adlandırılması hakkında tüm ayrıntılara ulaşmak için bkz. [Paylaşımları, Dizinleri, Dosyaları ve Meta Verileri Adlandırma ve Bunlara Başvuruda Bulunma](https://msdn.microsoft.com/library/azure/dn167011.aspx)
> 
> 

### <a name="create-a-directory-in-the-file-share"></a>Dosya paylaşımında bir dizin oluşturma
Şimdi, paylaşımda bir dizin oluşturun. Aşağıdaki örnekte, dizin `CustomLogs` olarak adlandırılmıştır.

```powershell
# create a directory in the share
New-AzureStorageDirectory -Share $s -Path CustomLogs
```

### <a name="upload-a-local-file-to-the-directory"></a>Dizine yerel bir dosya yükleme
Şimdi, dizine yerel bir doya yükleyin. Aşağıdaki örnekte `C:\temp\Log1.txt` konumundan bir dosya yüklenir. Dosya yolunu yerel makinenizdeki geçerli bir dosyaya işaret edecek şekilde düzenleyin.

```powershell
# upload a local file to the new directory
Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs
```

### <a name="list-the-files-in-the-directory"></a>Dizindeki dosyaları listeleme
Dizindeki dosyaları görmek üzere tüm dizin dosyalarını listeleyebilirsiniz. Bu komut, CustomLogs dizinindeki dosyaları ve alt dizinleri (varsa) döndürür.

```powershell
# list files in the new directory
Get-AzureStorageFile -Share $s -Path CustomLogs | Get-AzureStorageFile
```

Get-AzureStorageFile, hangi dizin nesnesi geçiriliyorsa, onun için dosyaların ve dizinlerin bir listesini döndürür. "Get-AzureStorageFile -Share $s" kök dizindeki dosyaların ve dizinlerin bir listesini döndürür. Alt dizindeki dosyaların bir listesini almak için alt dizini Get-AzureStorageFile dizinine geçirmeniz gerekir. Böylece şu işlemleri gerçekleştirmiş olursunuz; komutun kanala kadar olan ilk parçası CustomLogs alt dizininin dizin örneğini döndürür. Daha sonra, CustomLogs alt dizinindeki dosyaları ve dizinleri döndüren Get-AzureStorageFile dizinine geçirilir.

### <a name="copy-files"></a>Dosyaları kopyalama
Azure PowerShell’in 0.9.7 sürümünden başlayarak, bir dosyayı başka bir dosyaya, bir dosyayı başka bir bloba veya bir blobu bir dosyaya kopyalayabilirsiniz. Bu kopyalama işlemlerinin PowerShell cmdlet'leri kullanılarak nasıl yapılacağı aşağıda gösterilmiştir.

```powershell
# copy a file to the new directory
Start-AzureStorageFileCopy -SrcShareName srcshare -SrcFilePath srcdir/hello.txt -DestShareName destshare -DestFilePath destdir/hellocopy.txt -Context $srcCtx -DestContext $destCtx

# copy a blob to a file directory
Start-AzureStorageFileCopy -SrcContainerName srcctn -SrcBlobName hello2.txt -DestShareName hello -DestFilePath hellodir/hello2copy.txt -DestContext $ctx -Context $ctx
```

## <a name="mount-the-file-share"></a>Dosya paylaşımını bağlama
File Storage artık SMB 3.0 desteği sayesinde şifreleme ve SMB 3.0 istemcilerinden kalıcı tanıtıcıları destekler. Şifreleme desteği sayesinde SMB 3.0 istemciler aşağıdakiler dahil olmak üzere her yerde bir dosya paylaşımını bağlayabilir:

* Aynı bölgede bulunan bir Azure Virtual Machine (ayrıca SMB 2.1 tarafından desteklenen)
* Farklı bölgede bulunan bir Azure Virtual Machine (yalnızca SMB 3.0) 
* Şirket içi bir istemci uygulaması (yalnızca SMB 3.0)

Bir istemci File Storage’a eriştiğinde, kullanılan SMB sürümü işletim sistemi tarafından desteklenen SMB sürümüne bağlıdır. Aşağıdaki tabloda Windows istemcileri için sunulan desteğin bir özeti yer almaktadır. [SMB sürümleri](http://blogs.technet.com/b/josebda/archive/2013/10/02/windows-server-2012-r2-which-version-of-the-smb-protocol-smb-1-0-smb-2-0-smb-2-1-smb-3-0-or-smb-3-02-you-are-using.aspx) hakkında daha fazla ayrıntı için bu blogu inceleyin.

| Windows İstemcisi | Desteklenen SMB Sürümü |
|:--- |:--- |
| Windows 7 |SMB 2.1 |
| Windows Server 2008 R2 |SMB 2.1 |
| Windows 8 |SMB 3.0 |
| Windows Server 2012 |SMB 3.0 |
| Windows Server 2012 R2 |SMB 3.0 |
| Windows 10 |SMB 3.0 |

### <a name="mount-the-file-share-from-an-azure-virtual-machine-running-windows"></a>Windows çalıştıran Azure sanal makinesinden dosya paylaşımını bağlama
Azure dosya paylaşımının nasıl bağlandığını göstermek üzere Windows çalıştıran bir Azure Virtual Machine oluşturacak ve paylaşımı bağlamak için buna uzaktan bağlanacaksınız.

1. İlk olarak, [Azure portalında Windows sanal makinesi oluşturma](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) sayfasındaki yönergeleri izleyerek yeni bir Azure sanal makinesi oluşturun.
2. Daha sonra, [Azure portalını kullanarak Windows sanal makinesinde oturum açma](../virtual-machines/virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) sayfasındaki yönergeleri izleyerek sanal makineye uzaktan bağlanın.
3. Sanal makinede bir PowerShell penceresi açın.

### <a name="persist-your-storage-account-credentials-for-the-virtual-machine"></a>Sanal makine için depolama hesabı kimlik bilgilerinizi kalıcı yapma
Dosya paylaşımını bağlamadan önce, ilk olarak depolama hesabı kimlik bilgilerinizi sanal makinede kalıcı yapın. Bu adım uygulanmasıyla birlikte sanal makine yeniden başlatıldığında Windows otomatik olarak dosya paylaşımıyla yeniden bağlantı kurar. Hesap kimlik bilgilerinizi kalıcı yapmak için sanal makinede PowerShell penceresinden `cmdkey` komutunu çalıştırın. `<storage-account-name>` değerini depolama hesabınızın adıyla ve `<storage-account-key>` değerini depolama hesabınızın anahtarıyla değiştirin. Aşağıdaki örnekte olduğu gibi "AZURE" etki alanını açıkça belirtmeniz gerekir. 

```
cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>
```

Artık, sanal makine yeniden başlatıldığında Windows dosya paylaşımınızla yeniden bağlantı kurar. PowerShell penceresinde `net use` komutunu çalıştırarak paylaşımla yeniden bağlantı kurulduğunu doğrulayabilirsiniz.

Kimlik bilgilerinin yalnızca `cmdkey` komutunun çalıştığı bağlamda kalıcı yapıldığını unutmayın. Hizmet olarak çalışan bir uygulama geliştiriyorsanız, bu bağlam içinde de kimlik bilgilerinizi kalıcı yapmanız gerekir.

### <a name="mount-the-file-share-using-the-persisted-credentials"></a>Kalıcı kimlik bilgilerini kullanarak dosya paylaşımını bağlama
Sanal makineyle uzaktan bağlantı kurduktan sonra, aşağıdaki söz dizimini kullanarak dosya paylaşımını bağlamak için `net use` komutunu çalıştırın. `<storage-account-name>` değerini depolama hesabınızın adıyla ve `<share-name>` değerini File Storage paylaşımınızın adıyla değiştirin.

```
net use <drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>

example :
net use z: \\samples.file.core.windows.net\logs
```

Önceki adımda, depolama hesabının kimlik bilgilerini kalıcı yaptığınızdan, `net use` komutu ile bunları sağlamanız gerekmez. Kimlik bilgilerinizi kalıcı yapmadıysanız, aşağıdaki örnekte gösterildiği gibi bunları geçirilen parametre olarak `net use` komutuna ekleyin.

```
net use <drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /u:AZURE\<storage-account-name> <storage-account-key>

example :
net use z: \\samples.file.core.windows.net\logs /u:AZURE\samples <storage-account-key>
```

Artık başka bir sürücüyle yaptığınız gibi sanal makineden File Storage paylaşımı ile çalışabilirsiniz. Komut isteminden standart dosya komutları kullanabilir veya bağlanılan paylaşımı ve içeriklerini Dosya Gezgini’nde görüntüleyebilirsiniz. Ayrıca, .NET Framework’teki [System.IO ad alanları](http://msdn.microsoft.com/library/gg145019.aspx) tarafından sağlananlar gibi standart Windows dosyası G/Ç API’lerini kullanarak dosya paylaşımına erişen sanal makinede kod çalıştırabilirsiniz.

Azure bulut hizmetindeki çalışan bir rolle uzaktan bağlantı kurarak bu rolden de dosya paylaşımını bağlayabilirsiniz.

### <a name="mount-the-file-share-from-an-on-premises-client-running-windows"></a>Windows çalıştıran şirket içi bir istemciden dosya paylaşımını bağlama
Şirket için bir istemciden dosya paylaşımını bağlamak için, öncelikle şu adımları uygulamanız gerekir:

* Windows’un SMB 3.0’ı destekleyen bir sürümünü yükleyin. Windows, şirket içi istemcileriniz ile buluttaki Azure dosya paylaşımı arasında güvenli şekilde veri aktarmak için SMB 3.0 şifreleme özelliğinden yararlanır.
* SMB protokolü için gerektiğinden dolayı yerel ağınızda 445 bağlantı noktası (TCP Giden) için İnternet erişimini açın.

> [!NOTE]
> Bazı İnternet hizmet sağlayıcıları 445 bağlantı noktasını engelleyebilir. Bu nedenle, hizmet sağlayıcınızı kontrol etmeniz gerekebilir.
> 
> 

## <a name="develop-with-file-storage"></a>File Storage ile geliştirme
File Storage’a çağrı yapan kodlar yazmak için .NET ve Java için depolama istemcisi kitaplıklarını veya Azure Storage REST API’sini kullanabilirsiniz. Bu bölümdeki örnekte, masaüstünde çalışan basit bir konsol uygulaması üzerinden [.NET için Azure Storage İstemci Kitaplığı](https://msdn.microsoft.com/library/mt347887.aspx)’nı kullanarak dosya paylaşmayla nasıl çalışacağınız gösterilmektedir.

### <a name="create-the-console-application-and-obtain-the-assembly"></a>Konsol uygulaması oluşturma ve derleme alma
Visual Studio'da yeni bir Windows konsol uygulaması oluşturun. Aşağıdaki adımlar Visual Studio 2017’de konsol uygulaması oluşturmayı gösterir, ancak adımlar, diğer Visual Studio sürümlerindekilerle aynıdır.

1. **Dosya** > **Yeni** > **Proje**’yi seçin
2. **Yüklü** > **Şablonlar** > **Visual C#** > **Windows Klasik Masaüstü** öğesini seçin
3. **Konsol Uygulaması (.NET Framework)** öğesini seçin
4. **Ad:** alanına uygulamanız için bir ad girin
5. **Tamam**’ı seçin

Bu öğreticideki tüm kod örnekleri konsol uygulamanızın `Program.cs` dosyasındaki `Main()` yöntemine eklenebilir.

Azure bulut hizmeti veya web uygulaması ile masaüstü ve mobil uygulamaları dahil olmak üzere herhangi bir .NET uygulaması türünde Azure Depolama İstemcisi Kitaplığını kullanabilirsiniz. Bu kılavuzda, sadeleştirmek için konsol uygulaması kullanmaktayız.

### <a name="use-nuget-to-install-the-required-packages"></a>Gereken paketleri yüklemek için NuGet kullanma
Bu öğreticiyi tamamlamak için projenizde başvurmanız gereken iki paket vardır:

* [.NET için Microsoft Azure Storage İstemcisi Kitaplığı](https://www.nuget.org/packages/WindowsAzure.Storage/): Bu paket depolama hesabınızdaki veri kaynaklarına programlı erişim sağlar.
* [.NET için Microsoft Azure Configuration Manager Kitaplığı](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): Bu paket, uygulamanızın nerede çalıştığına bakmaksızın yapılandırma dosyasından bağlantı dizesini ayrıştırmak için bir sınıf sağlar.

Her iki paketi de almak için NuGet kullanabilirsiniz. Şu adımları uygulayın:

1. **Çözüm Gezgini**'nde projenize sağ tıklayın ve **NuGet Paketlerini Yönet**’i seçin.
2. Çevrimiçi olarak "WindowsAzure.Storage" ifadesini arayın ve Depolama İstemci Kitaplığı’nı ve bağımlılıklarını yüklemek için **Yükle**’ye tıklayın.
3. Çevrimiçi olarak "WindowsAzure.ConfigurationManager" ifadesini arayın ve Azure Yapılandırma Yöneticisi’ni yüklemek için **Yükle**’ye tıklayın.

### <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Depolama hesabı kimlik bilgilerinizi app.config dosyasına kaydetme
Sonraki adımda, kimlik bilgilerinizi projenizin app.config dosyasına kaydedin. app.config dosyasını aşağıdaki örneğe benzeyecek şekilde düzenleyin. `myaccount` değerini depolama hesabınızın adıyla ve `mykey` değerini depolama hesabınızın anahtarıyla değiştirin.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> Azure Storage öykünücüsünün en son sürümü File Storage’ı desteklemez. Bağlantı dizeniz, File Storage ile çalışmak için buluttaki bir Azure Storage hesabını hedeflemelidir.
> 
> 

### <a name="add-using-directives"></a>Using yönergeleri ekleme
Çözüm Gezgini’nde `Program.cs` dosyasını açın ve aşağıdaki using yönergelerini dosyanın üst tarafına ekleyin.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.WindowsAzure.Storage; // Namespace for Storage Client Library
using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage
using Microsoft.WindowsAzure.Storage.File; // Namespace for File storage
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="access-the-file-share-programmatically"></a>Dosya paylaşımına programlamayla erişme
Şimdi, bağlantı dizesini almak için aşağıdaki kodu `Main()` yöntemine (yukarıda gösterilen koddan sonra) ekleyin. Bu kod, daha önce oluşturduğumuz dosyaya başvuru alır ve bu dosyanın içeriğini konsol penceresine çıkarır.

```csharp
// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

Çıkışı görmek konsol uygulamasını çalıştırın.

### <a name="set-the-maximum-size-for-a-file-share"></a>Dosya paylaşımı için boyut üst sınırını ayarlama
Azure Storage İstemci Kitaplığı’nın 5.x sürümünden başlayarak, dosya paylaşımı için gigabayt cinsinden kota (veya boyut üst sınırı) ayarlayabilirsiniz. Paylaşımda halihazırda ne kadar verinin depolandığını da kontrol edebilirsiniz.

Paylaşım için kota ayarlayarak paylaşımda depolanan toplam dosya boyutunu kısıtlayabilirsiniz. Paylaşımdaki toplam dosya boyutu belirlediğiniz kotayı aşarsa, istemciler mevcut dosyaların boyutunu artıramaz veya boş olmamaları halinde yeni dosyalar oluşturamaz.

Aşağıdaki örnekte, paylaşımdaki mevcut kullanımını nasıl kontrol edileceği veya paylaşım için nasıl kota ayarlanacağı gösterilmiştir.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.WindowsAzure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Dosya veya dosya paylaşımı için paylaşılan erişim imzası oluşturma
Azure Storage İstemci Kitaplığı’nın 5.x sürümünden başlayarak, bir dosya paylaşımı veya yalnızca dosya için paylaşılan erişim imzası (SAS) oluşturabilirsiniz. Ayrıca, paylaşılan erişim imzalarını yönetmek için dosya paylaşımında bir paylaşılan erişim ilkesi oluşturabilirsiniz. Gizliliğinin tehlikeye girdiği durumlarda SAS’yi iptal etme aracı olarak kullanılabilmesi nedeniyle bir paylaşılan erişim ilkesi oluşturmanız önerilir.

Aşağıdaki örnekte, paylaşım için bir paylaşılan erişim ilkesi oluşturulur, daha sonra bu ilke paylaşımdaki bir dosyada bulunan SAS için sınırlamalar sağlamak amacıyla kullanılır.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new shared access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the shared access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authenticated via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

Paylaşılan erişim imzaları oluşturma ve kullanma hakkında daha fazla bilgi edinmek için bkz. [Paylaşılan Erişim İmzaları (SAS) kullanma](storage-dotnet-shared-access-signature-part-1.md) ve [Blob depolama ile SAS oluşturma ve kullanma](storage-dotnet-shared-access-signature-part-2.md).

### <a name="copy-files"></a>Dosyaları kopyalama
Azure Storage İstemci Kitaplığı’nın 5.x sürümünden başlayarak, bir dosyayı başka bir dosyaya, bir dosyayı başka bir bloba veya bir blobu bir dosyaya kopyalayabilirsiniz. Sonraki bölümlerde, bu kopyalama işlemlerini programlamayla nasıl gerçekleştirebileceğinizi göstereceğiz.

Bir dosyayı diğer bir dosyaya veya bir blobu bir dosyaya ya da tam tersini yapmak için AzCopy’i de kullanabilirsiniz. Bkz. [AzCopy Komut Satırı Yardımcı Programı ile veri aktarımı](storage-use-azcopy.md).

> [!NOTE]
> Bir blobu dosyaya veya bir dosyayı bloba kopyalamak için aynı depolama hesabında kopyalama yapıyor olsanız da kaynak nesnesinin kimliğini doğrulamak amacıyla bir paylaşılan erişim imzası (SAS) kullanmanız gerekir.
> 
> 

**Dosyayı başka bir dosyaya kopyalama**

Aşağıdaki örnekte, bir dosya aynı paylaşımdaki başka bir dosyaya kopyalanır. Bu kopyalama işlemi aynı depolama hesabındaki dosyaları kopyaladığı için, kopyalama işlemini gerçekleştirmek üzere Paylaşılan Anahtar kimlik doğrulaması kullanabilirsiniz. 

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

**Dosyayı bir bloba kopyalama**

Aşağıdaki örnekte, bir dosya oluşturulur ve aynı depolama hesabındaki bir bloba kopyalanır. Örnekte, kaynak dosya için hizmetin kopyalama sırasında kaynak dosyaya erişimin kimlik doğrulamasını yapmak üzere kullandığı bir SAS oluşturulur.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authenticate access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

Aynı şekilde, bir blobu bir dosyaya kopyalayabilirsiniz. Kaynak dosya bir blob ise, kopyalama sırasında bu bloba erişimin kimlik doğrulamasını yapması için bir SAS oluşturun.

## <a name="troubleshooting-file-storage-using-metrics"></a>Ölçümleri kullanarak File Storage sorunlarını giderme
Azure Storage Analitikleri, File Storage için artık ölçümleri destekliyor. Ölçüm verilerini kullanarak istekleri ve tanılama sorunlarını izleyebilirsiniz.

[Azure portalından](https://portal.azure.com) Dosya depolama ölçümlerini etkinleştirebilirsiniz. Ayrıca, REST API veya Depolama İstemci Kitaplığı’ndaki analoglarından biri aracılığıyla Dosya Hizmeti Özelliklerini Ayarla işlemine çağrı yaparak ölçümleri programlamayla etkinleştirebilirsiniz.

Aşağıdaki kodlarda, File Storage için ölçümleri etkinleştirmek üzere .NET için Depolama İstemcisi Kitaplığı’nı nasıl kullanacağınız gösterilmiştir.

İlk olarak, yukarıda eklediğiniz yönergelere ek olarak aşağıdaki `using` yönergelerini `Program.cs` dosyanıza ekleyin:

```csharp
using Microsoft.WindowsAzure.Storage.File.Protocol;
using Microsoft.WindowsAzure.Storage.Shared.Protocol;
```

Blob, Tablo ve Kuyruk depolamanın `Microsoft.WindowsAzure.Storage.Shared.Protocol` ad alanındaki paylaşılan `ServiceProperties` türünü kullanmasına rağmen, File Storage’ın `Microsoft.WindowsAzure.Storage.File.Protocol` ad alanındaki `FileServiceProperties` türü olan kendi türünü kullandığını unutmayın. Aşağıdaki kodların derlenebilmesi için her iki ad alanına da kodunuzdan başvurulmuş olması gerekir.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.WindowsAzure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

Uçtan uca sorun giderme kılavuzu için [Azure Dosyaları Sorun Giderme Makalesine](storage-troubleshoot-file-connection-problems.md) de bakabilirsiniz. 

## <a name="file-storage-faq"></a>File Storage SSS
1. **Dosya depolama Active Directory tabanlı kimlik doğrulamasını destekliyor mu?**
   
    Şu anda AD tabanlı kimlik doğrulamasını veya ACL’leri desteklemiyoruz. Ancak, bunu özellik istekleri listemize ekledik. Şimdilik, dosya paylaşımı için kimlik doğrulaması sağlamak üzere Azure Storage hesabı anahtarları kullanılıyor. REST API veya istemci kitaplıkları aracılığıyla kullanılan paylaşılan erişim imzaları (SAS) ile geçici bir çözüm sunuyoruz. SAS’yi kullanarak belirli bir süre aralığında geçerli olan özel izinlere sahip belirteçler oluşturabilirsiniz. Örneğin, yalnızca verilen dosyaya salt okunur erişime sahip bir belirteç oluşturabilirsiniz. Geçerli olduğu sürece bu belirtece sahip olan herkes bu dosyaya salt okunur erişim elde eder.
   
    SAS yalnızca REST API veya istemci kitaplıkları aracılığıyla desteklenir. Dosya paylaşımını SMB protokolü aracılığıyla bağladığınızda, bu paylaşımdaki içeriklere erişimi devretmek için SAS kullanamazsınız. 

2. **Bir web tarayıcısı üzerinden belirli bir dosyaya nasıl erişim sağlayabilirim?**
   SAS’yi kullanarak belirli bir süre aralığında geçerli olan özel izinlere sahip belirteçler oluşturabilirsiniz. Örneğin, belirli bir süre için belirli bir dosyaya salt okunur erişim sunan bir belirteç oluşturabilirsiniz. Bu url’ye sahip olan herkes, geçerli olduğu süre boyunca herhangi bir web tarayıcısından indirme işlemini doğrudan gerçekleştirebilir. SAS anahtarları, Depolama Gezgini gibi bir kullanıcı arabiriminden kolayca oluşturulabilir.

3.   **Azure Dosya depolamadaki dosyalara erişmenin farklı yolları nelerdir?**
    SMB 3.0 protokolünü kullanarak dosya paylaşımını yerel makinenize bağlayabilir veya [Depolama Gezgini](http://storageexplorer.com/) ya da Cloudberry gibi araçları kullanarak dosya paylaşımınızdaki dosyalara erişebilirsiniz. Uygulamanızda İstemci Kitaplıkları, REST API veya Powershell kullanarak Azure Dosya paylaşımındaki dosyalarınıza erişebilirsiniz.
    
4.   **Azure dosya paylaşımımı yerel makineme nasıl bağlayabilirim?** 445 bağlantı noktası (TCP Giden) açık olduğu ve istemciniz SMB 3.0 protokolünü (*örn.*, Windows 8 veya Windows Server 2012) desteklediği sürece dosya paylaşımını SMB protokolü üzerinden bağlayabilirsiniz. Bağlantı noktasının engelini kaldırmak için yerel ISS sağlayıcınız ile görüşün. Bu arada, Depolama Gezgini’ni veya Cloudberry gibi başka bir üçüncü taraf çözümünü kullanarak dosyalarınızı görüntüleyebilirsiniz.

5. **Azure sanal makinesi ve dosya paylaşımı arasındaki ağ trafiği ücreti aboneliğe yansıtılan harici bir bant genişliği olarak mı sayılıyor?**
   
    Dosya paylaşımı ve sanal makine farklı bölgelerde bulunuyorsa, aralarındaki trafik harici bant genişliği olarak ücretlendirilir.
6. **Sanal makine ve dosya paylaşımı arasındaki ağ trafiği aynı bölgede bulunursa ücretsiz mi oluyor?**
   
    Evet. Trafiğin aynı bölgede olması koşuluyla ücretsizdir.
7. **Şirket içi sanal makinelerden Azure Dosya Depolama’ya bağlanmak için Azure ExpressRoute mu gerekir?**
   
    Hayır. ExpressRoute’a sahip olmasanız da, 445 bağlantı noktası (TCP Giden) için İnternet erişimi açık olduğu sürece şirket içi sanal makinelerden dosya paylaşımına erişebilirsiniz. Bununla birlikte, isterseniz File Storage ile ExpressRoute’u kullanabilirsiniz.
8. **Yük devretme kümesi için "Dosya Paylaşım Tanığı" Azure Dosya Depolama için kullanım durumlarından biri mi?**
   
    Bu, şu anda desteklenmiyor.
9. **Dosya Depolama şu anda yalnızca LRS veya GRS aracılığıyla mı çoğaltılabiliyor?**  
   
    RA-GRS’yi de desteklemeyi planlıyoruz, ancak bunun için kesin bir tarih veremiyoruz.
10. **Mevcut depolama hesaplarını Azure Dosya Depolama için ne zaman kullanabilirim?**
   
    Azure File Storage şimdi tüm depolama hesapları için etkinleştirildi.
11. **REST API’ye Yeniden adlandırma işlemi de eklenecek mi?**
   
    Yeniden adlandırma, REST API'mizde henüz desteklenmiyor.
12. **İç içe geçmiş paylaşımlarınız, diğer bir deyişle başka bir paylaşım kapsamında bulunan paylaşımlarınız var mı?**
    
    Hayır. Dosya paylaşımı bağlayabileceğiniz sanal bir sunucudur. Bu nedenle, iç içe paylaşımlar desteklenmez.
13. **Paylaşımdaki klasörlere yalnızca salt okunur veya sadece yazılabilir izinleri tanıyabilir miyim?**
    
    Dosya paylaşımını SMB aracılığıyla bağlamanız halinde izinler üzerinde bu düzeyde bir denetiminiz bulunmaz. Ancak, REST API veya istemci kitaplıkları aracılığıyla paylaşılan erişim imzası (SAS) oluşturarak bunu gerçekleştirebilirsiniz.  
14. **Dosyaların sıkıştırmasını Dosya Depolama’da açarken performansta yavaşlama oluyor. Ne yapmalıyım?**
    
    File Storage’a çok sayıda dosya aktarmak istiyorsanız; AzCopy’i, Azure Powershell’i (Windows) veya Azure CLI’yi (Linux/Unix) kullanmanızı öneririz. Bu araçlar, ağ aktarımı için en uygun hale getirilmiştir.
15. **Azure Dosyaları ile yaşanan yavaş performans sorununu çözecek bir düzeltme eki yayımlandı**
    
    Windows ekibi yakın zamanda Windows 8.1 veya Windows Server 2012 R2 üzerinden Azure File Storage’a erişen müşteriler için yavaş performans sorunu çözecek bir düzeltme eki yayımlandı. Daha fazla bilgi için lütfen ilgili KB makalesine göz atın: [Azure File Storage’a Windows 8.1 veya Windows Server 2012 R2 üzerinden erişildiğinde performansın yavaşlaması](https://support.microsoft.com/kb/3114025).
16. **Azure Dosya Depolama’yı IBM MQ ile kullanma**
    
    IBM, IBM MQ müşterileri için hizmetlerini Azure File Storage ile yapılandırmalarına yardımcı olacak bir belge yayımladı. Daha fazla bilgi için bkz. [Microsoft Azure Dosya Hizmeti ile IBM MQ Çok örnekli kuyruk yöneticisini kurma](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).
17. **Azure Dosya Depolama hatalarını nasıl giderebilirim?**
    
    Uçtan uca sorun giderme kılavuzu için [Azure Dosyaları Sorun Giderme Makalesine](storage-troubleshoot-file-connection-problems.md) bakabilirsiniz.               

18. **Azure Dosyaları için sunucu tarafı şifrelemesini nasıl etkinleştirebilirim?**

    Azure Dosyaları için [Sunucu Tarafı Şifrelemesi](storage-service-encryption.md) şu anda önizleme aşamasındadır. Önizleme aşamasında, bu özelliği yalnızca [Azure portalı](https://portal.azure.com) kullanılarak oluşturulmuş yeni Azure Resource Manager depolama hesaplarında etkinleştirebilirsiniz. Bu özelliği etkinleştirmek için ek bir ücret uygulanmamaktadır. Azure Dosya Depolama için Depolama Hizmeti Şifrelemesini etkinleştirdiğinizde verileriniz otomatik olarak şifrelenir. 
    
    Gelecekte [Azure PowerShell](/powershell/resourcemanager/azurerm.storage/v2.7.0/azurerm.storage), [Azure CLI](storage-azure-cli.md) ve [Azure Depolama Kaynak Sağlayıcısı REST API’si](/rest/api/storagerp/storageaccounts) ile dosya depolama şifrelemesini desteklemeyi planlıyoruz. 
    Azure Depolama hizmetinde bekleyen şifreleme hakkında daha fazla bilgi için [Depolama Hizmeti Şifrelemesi](storage-service-encryption.md) bölümüne bakabilir ve önizleme aşamasında sorularınız olursa ssediscussions@microsoft.com ile iletişim kurabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Azure File Storage hakkında daha fazla bilgi edinmek için şu bağlantılara göz atın.

### <a name="conceptual-articles-and-videos"></a>Kavramsal makaleler ve videolar
* [Azure Dosya Depolama: Windows ve Linux için uyumlu bulut SMB dosya sistemi](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Azure Dosya Depolama’yı Linux ile kullanma](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>File Storage için araç desteği
* [Azure Depolama ile Azure PowerShell’i kullanma](storage-powershell-guide-full.md)
* [Microsoft Azure Depolama ile AzCopy kullanma](storage-use-azcopy.md)
* [Azure Depolama ile Azure CLI kullanma](storage-azure-cli.md#create-and-manage-file-shares)
* [Azure Dosya depolama sorunlarını giderme](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Başvuru
* [.NET başvurusu için Depolama İstemci Kitaplığı](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Dosya Hizmeti REST API başvurusu](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### <a name="blog-posts"></a>Blog yazıları
* [Azure Dosya Depolama genel kullanıma sunulmuştur](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Azure Dosya Depolama İncelemesi](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Microsoft Azure Dosya Hizmeti’ne Giriş](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Microsoft Azure Dosyaları ile kalıcı bağlantılar](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

