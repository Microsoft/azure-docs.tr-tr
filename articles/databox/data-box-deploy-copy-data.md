---
title: Azure Data Box’ta SMB aracılığıyla veri kopyalama öğreticisi | Microsoft Docs
description: SMB aracılığıyla Azure Data Box'a veri kopyalamayı öğrenin
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: d86da3013a3cb4573556bc14ea1e6a0fbab72623
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70240386"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>Öğretici: SMB aracılığıyla Azure Data Box’a veri kopyalama

::: zone-end

::: zone target="chromeless"

# <a name="copy-data-to-azure-data-box"></a>Azure Data Box'a veri kopyalama

::: zone-end

::: zone target="docs"

Bu öğreticide yerel web arabirimini kullanarak bağlantı kurma, ana bilgisayarınızdan veri kopyalama işlemi anlatılmaktadır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Ön koşullar
> * Data Box'a bağlanma
> * Data Box'a veri kopyalama


## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce aşağıdakilerden emin olun:

1. [Öğretici: Azure Data Box’ı ayarlama](data-box-deploy-set-up.md).
2. Data Box’ı teslim aldınız ve portaldaki sipariş durumu **Teslim Edildi** oldu.
3. Data Box üzerinden kopyalamak istediğiniz verileri içeren bir ana bilgisayarınız var. Ana bilgisayarınız:
    - [Desteklenen bir işletim sistemi](data-box-system-requirements.md) çalıştırılmalıdır.
    - Yüksek hızlı bir ağa bağlı olmalıdır. En az bir adet 10 GbE bağlantınızın olması önemle tavsiye edilir. 10 GbE bağlantı yoksa, 1 GbE veri bağlantısı kullanın ancak kopyalama hızınızın etkileneceğini göz önünde bulundurun.

## <a name="connect-to-data-box"></a>Data Box'a bağlanma

Seçilen depolama hesabına bağlı olarak, Data Box şunların tamamını veya bir bölümünü oluşturabilir:
- İlişkili her depolama hesabına GPv1 ve GPv2 için üç paylaşım.
- Premium depolama için bir paylaşım.
- Blob depolama hesabı için bir paylaşım.

Blok blobu ve sayfa blobu paylaşımlarının altında birinci düzeydeki varlıklar kapsayıcılar, ikinci düzeydeki varlıklar ise bloblardır. Azure Dosyaları paylaşımlarında birinci düzeydeki varlıklar paylaşımlar, ikinci düzeydeki varlıklar ise dosyalardır.

Aşağıdaki tabloda, Data Box üzerindeki paylaşımların UNC yolu ve verilerin karşıya yüklendiği Azure Depolama yolu URL'si gösterilmektedir. Son Azure depolama yolu URL'si, UNC paylaşım yolundan türetilebilir.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure Blok blobları | <li>Paylaşımların UNC yolu: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Azure Depolama URL’si: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure Sayfa blobları  | <li>Paylaşımların UNC yolu: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Azure Depolama URL’si: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Dosyaları       |<li>Paylaşımların UNC yolu: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Azure Depolama URL’si: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Windows Server ana bilgisayarı kullanıyorsanız Data Box'a bağlanmak için aşağıdaki adımları izleyin.

1. İlk adım kimlik doğrulamasından geçmek ve oturum başlatmaktır. **Bağlan ve kopyala**'ya gidin. Depolama hesabınızla ilişkilendirilmiş paylaşımların erişim kimlik bilgilerini almak için **Kimlik bilgilerini al**'a tıklayın. 

    ![Paylaşım kimlik bilgilerini alma 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. Paylaşıma erişme ve veri kopyalama iletişim kutusunda paylaşıma karşılık gelen **Kullanıcı adı** ve **Parola** değerlerini kopyalayın. **Tamam** düğmesine tıklayın.
    
    ![Paylaşım kimlik bilgilerini alma 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Ana bilgisayarınızdan depolama hesabınızla (aşağıdaki örnekte *devicemanagertest1*) ilişkili paylaşımlara erişmek için bir komut penceresi açın. Komut istemine şunları yazın:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Veri biçimine bağlı olarak paylaşım yolları şu şekilde olacaktır:
    - Azure Blok blobu - `\\10.126.76.172\devicemanagertest1_BlockBlob`
    - Azure Sayfa blobu - `\\10.126.76.172\devicemanagertest1_PageBlob`
    - Azure Dosyalar - `\\10.126.76.172\devicemanagertest1_AzFile`
    
4. İstendiğinde paylaşımın parolasını girin. Aşağıdaki örnekte yukarıdaki komutla paylaşıma bağlanma adımları gösterilmektedir.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Windows + R tuşlarına basın. **Çalıştır** penceresinde `\\<device IP address>` değerini belirtin. Dosya Gezgini'ni açmak için **Tamam**’a tıklayın.
    
    ![Paylaşıma Dosya Gezgini ile bağlanma 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    Artık paylaşımları klasörler olarak görebilirsiniz.
    
    ![Paylaşıma Dosya Gezgini ile bağlanma 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png)    

    **Her zaman kopyalamayı düşündüğünüz dosyalar için paylaşımda bir klasör oluşturun ve ardından dosyaları bu klasöre kopyalayın**. Blok blobu ve sayfa blobu paylaşımları altında oluşturulan klasör, verilerin blob olarak karşıya yüklendiği kapsayıcıyı temsil eder. Dosyaları depolama hesabındaki *root* klasörüne doğrudan kopyalayamazsınız.
    
Bir Linux istemcisi kullanıyorsanız, SMB paylaşımını bağlamak için aşağıdaki komutu kullanın. Aşağıdaki "vers" parametresi, Linux ana bilgisayarınızın desteklediği SMB sürümüdür. Aşağıdaki komutta verilen uygun sürümü takın. Data Box’ın desteklediği SMB sürümleri için bkz. [Linux istemcileri için desteklenen dosya sistemleri](https://docs.microsoft.com/azure/databox/data-box-system-requirements#supported-file-systems-for-linux-clients) 

    `sudo mount -t nfs -o vers=2.1 10.126.76.172:/devicemanagertest1_BlockBlob /home/databoxubuntuhost/databox`
    


## <a name="copy-data-to-data-box"></a>Data Box'a veri kopyalama

Data Box paylaşımlarına bağlandıktan sonra veri kopyalamaya başlayabilirsiniz. Verileri kopyalamaya başlamadan önce aşağıdaki konuları gözden geçirin:

- Verilerin uygun dosya biçimine karşılık gelen paylaşımlara kopyalandığından emin olun. Örneğin blok blobu verilerinin blok blobu paylaşımına kopyalanması gerekir. VHD'leri sayfa blobuna kopyalayın. Veri biçimi uygun paylaşım türüyle eşleşmiyorsa verilerin Azure'a yüklenmesi başarısız olur.
-  Veri kopyalama sırasında veri boyutunun [Azure depolama ve Data Box sınırları](data-box-limits.md) içinde belirtilen boyut sınırlarına uygun olduğundan emin olun.
- Data Box tarafından yüklenen verilerin Data Box haricinde başka bir uygulama tarafından da yüklenmesi durumunda yükleme işinde hata oluşabilir ve veri bozulması yaşanabilir.
- Şunları öneririz:
    - SMB ve NFS'yi aynı anda kullanmayın.
    - Aynı verileri Azure'da aynı son hedefe kopyalayın. 
     
  Böyle durumlarda nihai sonucu kestirmek mümkün olmayabilir.
- Her zaman kopyalamayı düşündüğünüz dosyalar için paylaşımda bir klasör oluşturun ve ardından dosyaları bu klasöre kopyalayın. Blok blobu ve sayfa blobu paylaşımları altında oluşturulan klasör, verilerin blob olarak karşıya yüklendiği kapsayıcıyı temsil eder. Dosyaları depolama hesabındaki *root* klasörüne doğrudan kopyalayamazsınız.

SMB paylaşımına bağlandıktan sonra verileri kopyalamaya başlayın. Verilerinizi kopyalamak için Robocopy gibi SMB uyumlu herhangi bir dosya kopyalama aracını kullanabilirsiniz. Robocopy ile birden fazla kopyalama işlemini başlatabilirsiniz. Aşağıdaki komutu kullanın:
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 Öznitelikler aşağıdaki tabloda açıklanmıştır.
    
|Öznitelik  |Açıklama  |
|---------|---------|
|/e     |Boş dizinler dahil olmak üzere alt dizinleri kopyalar.         |
|/r:     |Başarısız kopyalama işlemleri için yeniden deneme sayısını belirtir.         |
|/w:     |Yeniden deneme işlemleri arasındaki bekleme süresini saniye cinsinden belirtir.         |
|/is     |Aynı dosyaları dahil eder.         |
|/nfl     |Dosya adlarının günlüğe kaydedilmeyeceğini belirtir.         |
|/ndl    |Dizin adlarının günlüğe kaydedilmeyeceğini belirtir.        |
|/np     |Kopyalama işleminin ilerleme durumunun (kopyalanan dosya veya dizin sayısının) görüntülenmeyeceğini belirtir. İlerleme durumunun gösterilmesi performansı önemli ölçüde düşürür.         |
|/MT     | Çoklu iş parçacığı kullanılır, 32 veya 64 iş parçacığı önerilir. Bu seçenek şifrelenmiş dosyalarla kullanılmaz. Şifrelenmiş ve şifrelenmemiş dosyaları ayırmanız gerekebilir. Ancak tek iş parçacığına sahip kopyalama işlemi performansı önemli ölçüde düşürür.           |
|/fft     | Herhangi bir dosya sistemi için zaman damgası ayrıntı düzeyini azaltmak için kullanın.        |
|/b     | Dosyaları Yedekleme modunda kopyalar.        |
|/z    | Dosyaları Yeniden başlatma modunda kopyalar, kararsız ortamlarda kullanmanız önerilir. Bu işlem ek günlük kaydı nedeniyle aktarım hızını düşürür.      |
| /zb     | Yeniden başlatma modunu kullanır. Erişim reddedilirse bu seçenek Yedekleme modunu kullanır. Bu işlem denetim noktası oluşturma nedeniyle aktarım hızını düşürür.         |
|/efsraw     | Şifrelenmiş dosyaların tümünü EFS ham modunda kopyalar. Yalnızca şifrelenmiş dosyalarda kullanın.         |
|log+:\<LogFile>| Çıkışı var olan günlük dosyasına ekler.|    
 
Aşağıdaki örnekte dosyaları Data Box'a kopyalamak için kullanılan Robocopy komutunun çıkışı gösterilmektedir.
    
    C:\Users>robocopy
        -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:53 PM
            Simple Usage :: ROBOCOPY source destination /MIR
    
                    source :: Source Directory (drive:\path or \\server\share\path).
            destination :: Destination Dir  (drive:\path or \\server\share\path).
                    /MIR :: Mirror a complete directory tree.
    
        For more usage information run ROBOCOPY /?    
    
    ****  /MIR can DELETE files as well as copy them !
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60
    
    ------------------------------------------------------------------------------
    
    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------
    
                    Total    Copied   Skipped  Mismatch    FAILED    Extras
        Dirs :         1         1         1         0         0         0
        Files :        17        17         0         0         0         0
        Bytes :     3.9 k     3.9 k         0         0         0         0          
    C:\Users>
       

Performansı iyileştirmek için veri kopyalama sırasında aşağıdaki Robocopy parametrelerini kullanın.

|    Platform    |    Çoğunlukla küçük dosyalar, 512 KB altı                           |    Çoğunlukla orta büyüklükteki dosyalar, 512 KB-1 MB arası                      |    Çoğunlukla büyük dosyalar, 1 MB üzeri                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
|    Data Box         |    2 Robocopy oturumu <br> Oturum başına 16 iş parçacığı    |    3 Robocopy oturumu <br> Oturum başına 16 iş parçacığı    |    2 Robocopy oturumu <br> Oturum başına 24 iş parçacığı    |


Robocopy komutu hakkında daha fazla bilgi için bkz. [Robocopy ve birkaç örnek](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

Kopyalanan dosyaları görüntülemek ve doğrulamak için hedef klasörü açın. Kopyalama işlemi sırasında hatayla karşılaşırsanız sorun giderme için hata dosyalarını indirin. Daha fazla bilgi için bkz. [Data Box’a veri kopyalama sırasında hata günlüklerini görüntüleme](data-box-logs.md#view-error-log-during-data-copy). Veri kopyalama sırasında karşılaşılan hataların ayrıntılı bir listesi için bkz. [Data Box sorunlarını giderme](data-box-troubleshoot.md).

Veri bütünlüğünü sağlamak için sağlama toplamı veri kopyalama sırasında satır içinde hesaplanır. Kopyalama tamamlandıktan sonra cihazınızdaki kullanılan alanı ve boş alanı doğrulayın.
    
   ![Panoda boş ve kullanılan alanı doğrulama](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

::: zone-end

::: zone target="chromeless"

SMB, NFS, REST, veri kopyalama hizmeti veya yönetilen disklere kaynak sunucunuzdan veri kopyalayabilirsiniz.

Her durumda, paylaşım ve klasör adlarıyla veri boyutunun, [Azure Depolama ve Data Box hizmet sınırları](data-box-limits.md) altında açıklanan yönergelere uyduğundan emin olun.

## <a name="copy-data-via-smb"></a>SMB ile veri kopyalama

1. Bir Windows ana bilgisayar kullanıyorsanız, SMB paylaşımlarına bağlanmak için aşağıdaki komutu kullanın:

    `\\<IP address of your device>\ShareName`

2. Paylaşım erişimi kimlik bilgilerini almak için Data Box'ın yerel web kullanıcı arabiriminde **Bağlan ve kopyala** sayfasına gidin.
3. Verilerinizi paylaşımlara kopyalamak için Robocopy gibi SMB uyumlu bir dosya kopyalama aracı kullanın. 

Adım adım yönergeler için [Öğretici: SMB aracılığıyla Azure Data Box’a veri kopyalama](data-box-deploy-copy-data.md) bölümüne gidin.

## <a name="copy-data-via-nfs"></a>NFS ile veri kopyalama

1. NFS ana bilgisayarı kullanıyorsanız, NFS paylaşımlarını Data Box’a bağlamak için aşağıdaki komutu kullanın:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. Paylaşım erişimi kimlik bilgilerini almak için Data Box'ın yerel web kullanıcı arabiriminde **Bağlan ve kopyala** sayfasına gidin.
3. Verilerinizi kopyalamak için `cp` veya `rsync` komutunu kullanın.

Adım adım yönergeler için [Öğretici: NFS aracılığıyla Azure Data Box’a veri kopyalama](data-box-deploy-copy-data-via-nfs.md) bölümüne gidin.

## <a name="copy-data-via-rest"></a>REST ile veri kopyalama

1. REST API’leri aracılığıyla Data Box Blob depolama alanı kullanarak verileri kopyalamak için *http* ya da *https* üzerinden bağlanabilirsiniz.
2. Data Box Blob depolama alanına veri kopyalamak için AzCopy aracını kullanabilirsiniz.

Adım adım yönergeler için [Öğretici: REST API’leri aracılığıyla Azure Data Box Blob depolama alanına veri kopyalama](data-box-deploy-copy-data-via-nfs.md) bölümüne gidin.

## <a name="copy-data-via-data-copy-service"></a>Veri kopyalama hizmeti aracılığıyla veri kopyalama

1. Veri kopyalama hizmetini kullanarak veri kopyalamak için bir iş oluşturmanız gerekir. Data Box yerel web kullanıcı arabiriminde **Yönet > Verileri kopyala > Oluştur**'a gidin. 
2. Parametreleri doldurun ve bir iş oluşturun.

Adım adım yönergeler için [Öğretici: Veri kopyalama hizmetini kullanarak Azure Data Box’a veri kopyalama](data-box-deploy-copy-data-via-copy-service.md) bölümüne gidin.

## <a name="copy-data-to-managed-disks"></a>Yönetilen disklere veri kopyalama

1. Data Box cihazını sipariş ederken depolama hedefiniz olarak yönetilen diskleri seçmiş olmanız gerekir.
2. SMB veya NFS paylaşımları üzerinden Data Box’a bağlanabilirsiniz.
3. Daha sonra SMB veya NFS araçları aracılığıyla verileri kopyalayabilirsiniz.

Adım adım yönergeler için [Öğretici: Azure’da yönetilen diskler olarak verileri içeri aktarmak için Data Box kullanma](data-box-deploy-copy-data-from-vhds.md) bölümüne gidin.

::: zone-end


::: zone target="docs"

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide aşağıdaki Azure Data Box konularını öğrendiniz:

> [!div class="checklist"]
> * Ön koşullar
> * Data Box'a bağlanma
> * Data Box'a veri kopyalama


Data Box'ı Microsoft’a geri gönderme hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Data Box verilerinizi Microsoft'a gönderme](./data-box-deploy-picked-up.md)

::: zone-end

