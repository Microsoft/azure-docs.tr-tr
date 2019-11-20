---
title: Azure dosyaları nasıl dağıtılır | Microsoft Docs
description: Baştan sona Azure dosyaları dağıtmayı öğrenin.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3ce754a67643f4506fa825f0780969dc4a06f826
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299589"
---
# <a name="how-to-deploy-azure-files"></a>Azure Dosyaları’nı dağıtma
[Azure dosyaları](storage-files-introduction.md) , bulutta ENDÜSTRI standardı SMB protokolü aracılığıyla erişilebilen tam olarak yönetilen dosya paylaşımları sunar. Bu makalede, Azure dosyalarını kuruluşunuzda nasıl kuruluşunuzun içinde dağıtacağınız gösterilmektedir.

Bu makaledeki adımları izleyerek önce [bir Azure dosyaları dağıtımına yönelik planlamayı](storage-files-planning.md) okumayı kesinlikle öneririz.

## <a name="prerequisites"></a>Önkoşullar
Bu makalede, aşağıdaki adımları zaten tamamladığınız varsayılmaktadır:

- İstediğiniz bölgede istediğiniz dayanıklılık ve şifreleme seçeneklerine sahip bir Azure depolama hesabı oluşturdunuz. Depolama hesabı oluşturma hakkında adım adım yönergeler için bkz. [depolama hesabı oluşturma](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) .
- Depolama hesabınızda istediğiniz kotayı içeren bir Azure dosya paylaşımının oluşturulması. Dosya paylaşımının nasıl oluşturulacağı hakkında adım adım yönergeler için bkz. [dosya paylaşma oluşturma](storage-how-to-create-file-share.md) .

## <a name="transfer-data-into-azure-files"></a>Verileri Azure dosyaları 'na aktarma
Şirket içinde depolanan mevcut dosya paylaşımlarını yeni Azure dosya paylaşımınıza geçirmek isteyebilirsiniz. Bu bölümde, [planlama kılavuzunda](storage-files-planning.md#data-transfer-method) ayrıntılı olarak açıklanan çeşitli popüler yöntemler aracılığıyla verileri bir Azure dosya paylaşımında nasıl taşıyacağınız gösterilmektedir.

### <a name="azure-file-sync"></a>Azure Dosya Eşitleme
Azure Dosya Eşitleme aracısı şirket içi dosya sunucularının sağladığı esneklik, performans ve uyumluluk özelliklerinden vazgeçmeden kuruluşunuzun dosya paylaşımlarını Azure Dosyaları'nda toplamanızı sağlar. Bunu, Windows sunucularınızı Azure dosya paylaşımınızın hızlı bir önbelleğine dönüştürerek yapar. Verilere yerel olarak erişmek için Windows Server üzerinde kullanılabilen tüm protokolleri (SMB, NFS ve FTPS gibi) kullanabilir ve dünya çapında istediğiniz sayıda önbellek oluşturabilirsiniz.

Azure Dosya Eşitleme, eşitleme mekanizması uzun vadeli kullanım için istenmese bile, verileri bir Azure dosya paylaşımında geçirmek için kullanılabilir. Azure dosya paylaşımında veri aktarmak için Azure Dosya Eşitleme kullanma hakkında daha fazla bilgi, [Azure dosya eşitleme dağıtımı planlama](storage-sync-files-planning.md) ve [Azure dosya eşitleme dağıtma](storage-sync-files-deployment-guide.md)konusunda daha fazla bilgi bulunabilir.

### <a name="azure-importexport"></a>Azure İçeri/Dışarı Aktarma
Azure Içeri/dışarı aktarma hizmeti, sabit disk sürücülerinin bir Azure veri merkezine göndererek büyük miktarlardaki verileri Azure dosya paylaşımında güvenli bir şekilde aktarmanıza olanak tanır. Hizmete daha ayrıntılı bir genel bakış için bkz. [Azure depolama 'ya veri aktarmak için Microsoft Azure içeri/dışarı aktarma hizmetini kullanma](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) .

> [!Note]  
> Azure Içeri/dışarı aktarma hizmeti, şu anda dosyaların bir Azure dosya paylaşımından dışarı aktarılmasını desteklemez.

Aşağıdaki adımlar, verileri şirket içi bir konumdan Azure dosya paylaşımınıza aktarır.

1. Azure 'da posta için gereken sabit disk sayısını temin edin. Sabit diskler herhangi bir disk boyutunda olabilir, ancak SATA II veya SATA III standardını destekleyen bir 2,5 "ya da 3,5" SSD veya HDD olmalıdır. 

2. Veri aktarımını yaparak sunucu/BILGISAYAR üzerinde her diski bağlayın ve bağlayın. En iyi performans için, şirket içi dışarı aktarma işini verileri içeren sunucuda yerel olarak çalıştırmayı öneririz. Bazı durumlarda, verileri hizmet eden dosya sunucusu bir NAS cihazı olduğunda, bu mümkün olmayabilir. Bu durumda, her diski bir BILGISAYARA bağlamak mükemmel bir kabul edilebilir.

3. Her sürücünün çevrimiçi, başlatılmış ve bir sürücü harfi atandığından emin olun. Bir sürücüyü çevrimiçi duruma getirmek, başlatmak ve bir sürücü harfi atamak için, disk yönetimi MMC ek bileşenini (Diskmgmt. msc) açın.

    - Bir diski çevrimiçi duruma getirmek için (zaten çevrimiçi değilse), disk yönetimi MMC 'nin alt bölmesindeki diske sağ tıklayın ve "çevrimiçi" seçeneğini belirleyin.
    - Bir diski başlatmak için alt bölmedeki (disk çevrimiçi olduktan sonra) diske sağ tıklayın ve "Başlat" ı seçin. Sorulduğunda "GPT" yi seçtiğinizden emin olun.

        ![Disk Yönetimi MMC 'de disk başlatma menüsünün ekran görüntüsü](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Diske bir sürücü harfi atamak için, çevrimiçi ve başlatılmış diskin "ayrılmamış" alanına sağ tıklayın ve "yeni basit birim" ' e tıklayın. Bu, sürücü harfi atamanıza izin verir. Daha sonra yapılacak şekilde bu işlemi yapmak için birimi biçimlendirmeniz gerekmez.

        ![Disk Yönetimi MMC 'deki yeni basit birim Sihirbazı 'nın ekran görüntüsü](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Veri kümesi CSV dosyasını oluşturun. DataSet CSV dosyası, şirket içi verilerin yolu ile verilerin kopyalanması gereken Azure dosya paylaşımının arasında bir eşlemedir. Örneğin, aşağıdaki veri kümesi CSV dosyası bir şirket içi dosya paylaşımını ("F:\shares\") bir Azure dosya paylaşımına ("MyAzureFileShare") eşler:
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Depolama hesabı ile birden çok paylaşım belirtilebilir. Daha fazla bilgi için bkz. [veri kümesini HAZıRLAMA CSV dosyası](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) .

5. Sürücü kümesi CSV dosyasını oluşturun. Sürücü kümesi CSV dosyası, şirket içi dışarı aktarma aracısının kullanabildiği diskleri listeler. Örneğin, aşağıdaki sürücü, `X:`, `Y:` ve `Z:` sürücüleri şirket içi dışarı aktarma işinde kullanılacak CSV dosyası listelerini ayarlar:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Daha fazla bilgi için bkz. [sürücü KÜMESI CSV dosyası hazırlama](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) .

6. Verilerinizi bir veya daha fazla sabit sürücüye kopyalamak için [Waımportexport aracını](https://www.microsoft.com/download/details.aspx?id=55280) kullanın.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Disk hazırlama işlemini tamamladıktan sonra sabit disk sürücülerinde veya günlük dosyasında verileri değiştirmeyin.

7. [İçeri aktarma Işi oluşturun](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>Robocopy
Robocopy, Windows ve Windows Server ile birlikte gelen iyi bilinen bir kopyalama aracıdır. Robocopy, dosya paylaşımının yerel olarak bağlanması ve ardından Robocopy komutunda hedef olarak bağlı konumu kullanarak Azure dosyalarına veri aktarmak için kullanılabilir. Robocopy kullanmak oldukça basittir:

1. [Azure dosya paylaşımınızı bağlayın](storage-how-to-use-files-windows.md). En iyi performans için, Azure dosya paylaşımının verileri içeren sunucuya yerel olarak bağlanmasını öneririz. Bazı durumlarda, verileri hizmet eden dosya sunucusu bir NAS cihazı olduğunda, bu mümkün olmayabilir. Bu durumda, Azure dosya paylaşımının bir BILGISAYARA bağlanması mükemmel bir şekilde kabul edilebilir. Bu örnekte, dosya paylaşımının bağlanması için komut satırında `net use` kullanılır:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Verileri Azure dosya paylaşımında taşımak için komut satırında `robocopy` kullanın:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Robocopy, kopyalama davranışını istenen şekilde değiştirmek için önemli sayıda seçenek içerir. Daha fazla bilgi için [Robocopy](https://technet.microsoft.com/library/cc733145.aspx) el ile sayfasını görüntüleyin.

### <a name="azcopy"></a>AzCopy
AzCopy, en iyi performansla basit komutlar kullanarak Azure dosyalarını ve Azure Blob Storage 'a ve bu verileri kopyalamak için tasarlanan bir komut satırı yardımcı programıdır. AzCopy kullanımı kolaydır:

1. Windows veya [Linux](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux#download-and-install-azcopy) [üzerinde AzCopy 'in en son sürümünü](https://aka.ms/downloadazcopy) indirin.
2. Verileri Azure dosya paylaşımında taşımak için komut satırında `azcopy` kullanın. Windows üzerindeki söz dizimi aşağıdaki gibidir: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    Linux 'ta, komut sözdizimi biraz farklıdır:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    AzCopy, kopyalama davranışını istenen şekilde değiştirmek için önemli sayıda seçeneklere sahiptir. Daha fazla bilgi için bkz. [Windows üzerinde AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) ve [Linux üzerinde AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Gerekli bilgisayarlara/sunuculara otomatik olarak bağla
Şirket içi dosya paylaşımını değiştirmek için, paylaşımların kullanılacağı makinelere önceden bağlanması yararlı olur. Bu, makine listesinde otomatik olarak yapılabilir.

> [!Note]  
> Azure dosya paylaşımının bağlanması için, parola olarak depolama hesabı anahtarının kullanılması gerekir, bu nedenle yalnızca güvenilen ortamlarda bağlama yapmanız önerilir. 

### <a name="windows"></a>Windows
PowerShell, birden çok bilgisayarda Mount komutunu çalıştırmak için kullanılabilir. Aşağıdaki örnekte, `$computers` el ile doldurulur, ancak otomatik olarak bağlanacak bilgisayarların listesini oluşturabilirsiniz. Örneğin, bu değişkeni Active Directory sonuçlarla doldurabilirsiniz.

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
SSH ile Birleşik basit bir bash betiği aşağıdaki örnekte aynı sonucu verebilir. @No__t-0 değişkeni, Kullanıcı tarafından doldurulmuş gibi benzer şekilde kalır:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Sonraki adımlar
- [Azure Dosya Eşitleme dağıtımı için plan yapın](storage-sync-files-planning.md)
- [Windows 'da Azure dosyaları sorunlarını giderme](storage-troubleshoot-windows-file-connection-problems.md)
- [Linux 'ta Azure dosyaları sorunlarını giderme](storage-troubleshoot-linux-file-connection-problems.md)
