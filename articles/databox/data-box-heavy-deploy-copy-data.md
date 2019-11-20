---
title: Azure Data Box Heavy’de SMB aracılığıyla veri kopyalama öğreticisi | Microsoft Docs
description: SMB aracılığıyla Azure Data Box Heavy'ye veri kopyalamayı öğrenin
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 475e628901a96334976dbdbbaa8b362197ec3bc3
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914042"
---
::: zone target = "docs"

# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb"></a>Öğretici: SMB aracılığıyla Azure Data Box Heavy’ye veri kopyalama

::: zone-end

::: zone target = "chromeless"

# <a name="copy-data-to-azure-data-box-heavy"></a>Azure Data Box Heavy’ye veri kopyalama

::: zone-end

::: zone target = "docs"

Bu öğreticide yerel web arabirimini kullanarak bağlantı kurma, ana bilgisayarınızdan veri kopyalama işlemi anlatılmaktadır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Data Box Heavy’ye bağlanma
> * Data Box Heavy’ye veri kopyalama

::: zone-end

::: zone target = "chromeless"

SMB, NFS, REST, veri kopyalama hizmeti aracılığıyla Data Box’a veya yönetilen disklere kaynak sunucunuzdan veri kopyalayabilirsiniz.

Her durumda, paylaşım ve klasör adlarıyla veri boyutunun, [Azure Depolama ve Data Box Heavy hizmet sınırları](data-box-heavy-limits.md) altında açıklanan yönergelere uyduğundan emin olun.

::: zone-end

::: zone target = "docs"

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce aşağıdakilerden emin olun:

1. [Öğretici: Azure Data Box Heavy’yi ayarlama](data-box-deploy-set-up.md) bölümünü tamamladınız.
2. Data Box Heavy’yi teslim aldınız ve portaldaki sipariş durumu **Teslim Edildi** oldu.
3. Data Box Heavy üzerinden kopyalamak istediğiniz verileri içeren bir ana bilgisayarınız var. Ana bilgisayarınız:
    - [Desteklenen bir işletim sistemi](data-box-system-requirements.md) çalıştırılmalıdır.
    - Yüksek hızlı bir ağa bağlı olmalıdır. En yüksek kopyalama hızları için iki adet 40 GbE bağlantı (düğüm başına bir tane) birbirine paralel olarak kullanılabilir. 40 GbE bağlantınız yoksa, en az iki tane (düğüm başına bir tane) 10 GbE bağlantınızın olması önerilir.
   

## <a name="connect-to-data-box-heavy-shares"></a>Data Box Heavy paylaşımlarına bağlanma

Seçilen depolama hesabına bağlı olarak, Data Box Heavy şunların tamamını veya bir bölümünü oluşturabilir:
- İlişkili her depolama hesabına GPv1 ve GPv2 için üç paylaşım.
- Premium depolama için bir paylaşım.
- Blob depolama hesabı için bir paylaşım.

Bu paylaşımlar, cihazın her iki düğümünde de oluşturulur.

Blok blobu ve sayfa blobu paylaşımları altında:
- Birinci düzey varlıklar kapsayıcılardır.
- İkinci düzey varlıklar bloblardır.

Azure Dosyalar paylaşımlarının altında:
- Birinci düzey varlıklar paylaşımlardır.
- İkinci düzey varlıklar dosyalardır.

Aşağıdaki tabloda, Data Box Heavy üzerindeki paylaşımların UNC yolu ve verilerin karşıya yüklendiği Azure Depolama yolu URL'si gösterilmektedir. Son Azure depolama yolu URL'si, UNC paylaşım yolundan türetilebilir.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure Blok blobları | <li>Paylaşımların UNC yolu: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Azure Depolama URL’si: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure Sayfa blobları  | <li>Paylaşımların UNC yolu: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Azure Depolama URL’si: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Dosyaları       |<li>Paylaşımların UNC yolu: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Azure Depolama URL’si: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Bir Windows veya Linux istemcisi kullanarak bağlanma adımları farklıdır.

> [!NOTE]
> Cihazın her iki düğümüne paralel olarak bağlanmak için aynı adımları izleyin.

### <a name="connect-on-a-windows-system"></a>Windows sisteminde bağlanma

Windows Server ana bilgisayarı kullanıyorsanız Data Box Heavy'ye bağlanmak için aşağıdaki adımları izleyin.

1. İlk adım kimlik doğrulamasından geçmek ve oturum başlatmaktır. **Bağlan ve kopyala**'ya gidin. Depolama hesabınızla ilişkilendirilmiş paylaşımların erişim kimlik bilgilerini almak için **Kimlik bilgilerini al**'a tıklayın.

    ![Paylaşım kimlik bilgilerini alma 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. Paylaşıma erişme ve veri kopyalama iletişim kutusunda paylaşıma karşılık gelen **Kullanıcı adı** ve **Parola** değerlerini kopyalayın. **Tamam** düğmesine tıklayın.
    
    ![Paylaşım kimlik bilgilerini alma 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. Ana bilgisayarınızdan depolama hesabınızla (aşağıdaki örnekte *databoxe2etest*) ilişkili paylaşımlara erişmek için bir komut penceresi açın. Komut istemine şunları yazın:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Veri biçimine bağlı olarak paylaşım yolları şu şekilde olacaktır:
    - Azure Blok blobu - `\\10.100.10.100\databoxe2etest_BlockBlob`
    - Azure Sayfa blobu - `\\10.100.10.100\databoxe2etest_PageBlob`
    - Azure Dosyalar - `\\10.100.10.100\databoxe2etest_AzFile`
    
4. İstendiğinde paylaşımın parolasını girin. Aşağıdaki örnekte yukarıdaki komutla paylaşıma bağlanma adımları gösterilmektedir.

    ```
    C:\Users\Databoxuser>net use \\10.100.10.100\databoxe2etest_BlockBlob /u:databoxe2etest
    Enter the password for 'databoxe2etest' to connect to '10.100.10.100':
    The command completed successfully.
    ```

4. Windows + R tuşlarına basın. **Çalıştır** penceresinde `\\<device IP address>` değerini belirtin. Dosya Gezgini'ni açmak için **Tamam**’a tıklayın.
    
    ![Paylaşıma Dosya Gezgini ile bağlanma 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-1.png)

    Artık paylaşımları klasörler olarak görebilirsiniz.
    
    ![Paylaşıma Dosya Gezgini ile bağlanma 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-2.png)

    **Her zaman kopyalamayı düşündüğünüz dosyalar için paylaşımda bir klasör oluşturun ve ardından dosyaları bu klasöre kopyalayın**. Blok blobu ve sayfa blobu paylaşımları altında oluşturulan klasör, verilerin blob olarak karşıya yüklendiği kapsayıcıyı temsil eder. Dosyaları depolama hesabındaki *root* klasörüne doğrudan kopyalayamazsınız.
    
### <a name="connect-on-a-linux-system"></a>Linux sisteminde bağlanma

Bir Linux istemcisi kullanıyorsanız, SMB paylaşımını bağlamak için aşağıdaki komutu kullanın.

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

`vers` parametresi, Linux ana bilgisayarınızın desteklediği SMB sürümüdür. Yukarıdaki komutta verilen uygun sürümü takın.

Data Box Heavy’nin desteklediği SMB sürümleri için bkz. [Linux istemcileri için desteklenen dosya sistemleri](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients).

## <a name="copy-data-to-data-box-heavy"></a>Data Box Heavy’ye veri kopyalama

Data Box Heavy paylaşımlarına bağlandıktan sonra veri kopyalamaya başlayabilirsiniz.

### <a name="copy-considerations"></a>Kopyalama konusunda dikkat edilmesi gerekenler

Verileri kopyalamaya başlamadan önce aşağıdaki konuları gözden geçirin:

- Verilerin uygun dosya biçimine karşılık gelen paylaşımlara kopyalandığından emin olun. Örneğin blok blobu verilerinin blok blobu paylaşımına kopyalanması gerekir. VHD'leri sayfa blobuna kopyalayın.

    Veri biçimi uygun paylaşım türüyle eşleşmiyorsa verilerin Azure'a yüklenmesi başarısız olur.
-  Veri kopyalama sırasında veri boyutunun [Azure depolama ve Data Box Heavy limitleri](data-box-heavy-limits.md) içinde belirtilen boyut sınırlarına uygun olduğundan emin olun.
- Data Box Heavy tarafından yüklenen verilerin Data Box Heavy haricindeki başka bir uygulama tarafından da yüklenmesi durumunda yükleme işinde hata oluşabilir ve veri bozulması yaşanabilir.
- Şunları öneririz:
    - SMB ve NFS'yi aynı anda kullanmayın.
    - Aynı verileri Azure'da aynı son hedefe kopyalayın.
     
  Böyle durumlarda nihai sonucu kestirmek mümkün olmayabilir.
- Her zaman kopyalamayı düşündüğünüz dosyalar için paylaşımda bir klasör oluşturun ve ardından dosyaları bu klasöre kopyalayın. Blok blobu ve sayfa blobu paylaşımları altında oluşturulan klasör, verilerin blob olarak karşıya yüklendiği kapsayıcıyı temsil eder. Dosyaları depolama hesabındaki *root* klasörüne doğrudan kopyalayamazsınız.

SMB paylaşımına bağlandıktan sonra verileri kopyalamaya başlayın.

1. Verilerinizi kopyalamak için Robocopy gibi SMB uyumlu herhangi bir dosya kopyalama aracını kullanabilirsiniz. Robocopy ile birden fazla kopyalama işlemini başlatabilirsiniz. Aşağıdaki komutu kullanın:
    
    ```
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
    ```
    Öznitelikler aşağıdaki tabloda açıklanmıştır.
    
    |Öznitelik  |Açıklama  |
    |---------|---------|
    |/e      |Boş dizinler dahil olmak üzere alt dizinleri kopyalar.         |
    |/r:     |Başarısız kopyalama işlemleri için yeniden deneme sayısını belirtir.         |
    |/w:     |Yeniden deneme işlemleri arasındaki bekleme süresini saniye cinsinden belirtir.         |
    |/is     |Aynı dosyaları dahil eder.         |
    |/nfl    |Dosya adlarının günlüğe kaydedilmeyeceğini belirtir.         |
    |/ndl    |Dizin adlarının günlüğe kaydedilmeyeceğini belirtir.        |
    |/np     |Kopyalama işleminin ilerleme durumunun (kopyalanan dosya veya dizin sayısının) görüntülenmeyeceğini belirtir. İlerleme durumunun gösterilmesi performansı önemli ölçüde düşürür.         |
    |/MT     | Çoklu iş parçacığı kullanılır, 32 veya 64 iş parçacığı önerilir. Bu seçenek şifrelenmiş dosyalarla kullanılmaz. Şifrelenmiş ve şifrelenmemiş dosyaları ayırmanız gerekebilir. Ancak tek iş parçacığına sahip kopyalama işlemi performansı önemli ölçüde düşürür.           |
    |/fft    | Herhangi bir dosya sistemi için zaman damgası ayrıntı düzeyini azaltmak için kullanın.        |
    |/b      | Dosyaları Yedekleme modunda kopyalar.        |
    |/z      | Dosyaları Yeniden başlatma modunda kopyalar, kararsız ortamlarda kullanmanız önerilir. Bu işlem ek günlük kaydı nedeniyle aktarım hızını düşürür.      |
    | /zb    | Yeniden başlatma modunu kullanır. Erişim reddedilirse bu seçenek Yedekleme modunu kullanır. Bu işlem denetim noktası oluşturma nedeniyle aktarım hızını düşürür.         |
    |/efsraw | Şifrelenmiş dosyaların tümünü EFS ham modunda kopyalar. Yalnızca şifrelenmiş dosyalarda kullanın.         |
    |log+:\<LogFile>| Çıkışı var olan günlük dosyasına ekler.|
    
 
    Aşağıdaki örnekte dosyaları Data Box Heavy'ye kopyalamak için kullanılan Robocopy komutunun çıkışı gösterilmektedir.

    ```   
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.100.10.100\devicemanagertest1_AzFile\templates /MT:24
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
        Started : Thursday, April 4, 2019 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
        Dest : \\10.100.10.100\devicemanagertest1_AzFile\templates\
        Files : *.*
        Options : *.* /DCOPY:DA /COPY:DAT /MT:24 /R:5 /W:60
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
    ```       

2. Performansı iyileştirmek için veri kopyalama sırasında aşağıdaki Robocopy parametrelerini kullanın. (Aşağıdaki sayılar en iyi durum senaryolarını temsil eder.)

    | Platform    | Çoğunlukla küçük dosyalar, 512 KB altı    | Çoğunlukla orta büyüklükteki dosyalar, 512 KB-1 MB  | Çoğunlukla büyük dosyalar, 1 MB üzeri                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box Heavy | 6 Robocopy oturumu <br> Oturum başına 24 iş parçacığı | 6 Robocopy oturumu <br> Oturum başına 16 iş parçacığı | 6 Robocopy oturumu <br> Oturum başına 16 iş parçacığı |


    Robocopy komutu hakkında daha fazla bilgi için bkz. [Robocopy ve birkaç örnek](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

3. Kopyalanan dosyaları görüntülemek ve doğrulamak için hedef klasörü açın.

    ![Kopyalanan dosyaları görüntüleme](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. Veriler kopyalandığında:

    - Dosya adları, boyutları ve biçimi, Azure nesne ve depolama limitlerinin yanı sıra Azure dosya ve kapsayıcı adlandırma kurallarına uygun olduğundan emin olmak üzere doğrulanır.
    - Veri bütünlüğünden emin olmak için sağlama toplamı da satır içinde hesaplanır.

    Kopyalama işlemi sırasında hatayla karşılaşırsanız sorun giderme için hata dosyalarını indirin. Hata dosyalarını indirmek için ok simgesini seçin.

    ![Hata dosyalarını indirme](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    Daha fazla bilgi için bkz. [Data Box Heavy’ye veri kopyalama sırasında hata günlüklerini görüntüleme](data-box-logs.md#view-error-log-during-data-copy). Veri kopyalama sırasında karşılaşılan hataların ayrıntılı bir listesi için bkz. [Data Box Heavy sorunlarını giderme](data-box-troubleshoot.md).

5. Hata dosyasını Not Defteri'nde açın. Aşağıdaki hata dosyası, verilerin doğru hizalanmadığını gösterir.

    ![Hata dosyasını açma](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    Bir sayfa blobu için verilerin 512 bayt hizalı olması gerekir. Bu veriler kaldırıldıktan sonra, hata aşağıdaki ekran görüntüsünde gösterildiği gibi çözümlenir.

    ![Hata çözüldü](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. Kopyalama işlemi tamamlandıktan sonra **Panoyu Görüntüle** sayfasına gidin. Cihazınızdaki kullanılan alanı ve boş alanı doğrulayın.
    
    ![Panoda boş ve kullanılan alanı doğrulama](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

Verileri cihazın ikinci düğümüne kopyalamak için yukarıdaki adımları tekrarlayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide Azure Data Box Heavy ile ilgili aşağıdaki bilgileri öğrendiniz:

> [!div class="checklist"]
> * Data Box Heavy’ye bağlanma
> * Data Box Heavy’ye veri kopyalama


Data Box Heavy'yi Microsoft’a geri gönderme hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Data Box Heavy’yi Microsoft'a gönderme](./data-box-heavy-deploy-picked-up.md)

::: zone-end

::: zone target = "chromeless"

### <a name="copy-data-via-smb"></a>SMB ile veri kopyalama

1. Bir Windows ana bilgisayar kullanıyorsanız, SMB paylaşımlarına bağlanmak için aşağıdaki komutu kullanın:

    `\\<IP address of your device>\ShareName`

2. Paylaşım erişimi kimlik bilgilerini almak için Data Box'ın yerel web kullanıcı arabiriminde **Bağlan ve kopyala** sayfasına gidin.

3. Verilerinizi paylaşımlara kopyalamak için Robocopy gibi SMB uyumlu bir dosya kopyalama aracı kullanın.

Adım adım yönergeler için [Öğretici: SMB aracılığıyla Azure Data Box’a veri kopyalama](data-box-heavy-deploy-copy-data.md) bölümüne gidin.

### <a name="copy-data-via-nfs"></a>NFS ile veri kopyalama

1. NFS ana bilgisayarı kullanıyorsanız, NFS paylaşımlarını bağlamak için aşağıdaki komutu kullanın:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. Paylaşım erişimi kimlik bilgilerini almak için Data Box Heavy'nin yerel web kullanıcı arabiriminde **Bağlan ve kopyala** sayfasına gidin.
3. Verilerinizi kopyalamak için `cp` veya `rsync` komutunu kullanın. 
4. Data Box Heavy’nin ikinci düğümüne bağlanmak ve veri kopyalamak için bu adımları yineleyin.

Adım adım yönergeler için [Öğretici: NFS aracılığıyla Azure Data Box’a veri kopyalama](data-box-heavy-deploy-copy-data-via-nfs.md) bölümüne gidin.

### <a name="copy-data-via-rest"></a>REST ile veri kopyalama

1. REST API’leri aracılığıyla Data Box Blob depolama alanı kullanarak verileri kopyalamak için *http* ya da *https* üzerinden bağlanabilirsiniz.
2. Data Box Blob depolama alanına veri kopyalamak için AzCopy aracını kullanabilirsiniz.
3. Data Box Heavy’nin ikinci düğümüne bağlanmak ve veri kopyalamak için bu adımları yineleyin.

Adım adım yönergeler için [Öğretici: REST API’leri aracılığıyla Azure Data Box Blob depolama alanına veri kopyalama](data-box-heavy-deploy-copy-data-via-rest.md) bölümüne gidin.

### <a name="copy-data-via-data-copy-service"></a>Veri kopyalama hizmeti aracılığıyla veri kopyalama

1. Veri kopyalama hizmetini kullanarak veri kopyalamak için bir iş oluşturmanız gerekir. Data Box Heavy yerel web kullanıcı arabiriminde **Yönet > Verileri kopyala > Oluştur**'a gidin.
2. Parametreleri doldurun ve bir iş oluşturun.
3. Data Box Heavy’nin ikinci düğümüne bağlanmak ve veri kopyalamak için bu adımları yineleyin.

Adım adım yönergeler için [Öğretici: Veri kopyalama hizmetini kullanarak Azure Data Box Heavy’ye veri kopyalama](data-box-heavy-deploy-copy-data-via-copy-service.md) bölümüne gidin.

### <a name="copy-data-to-managed-disks"></a>Yönetilen disklere veri kopyalama

1. Data Box Heavy cihazını sipariş ederken depolama hedefiniz olarak yönetilen diskleri seçmiş olmanız gerekir.
2. SMB veya NFS paylaşımları üzerinden Data Box Heavy’ye bağlanabilirsiniz.
3. Daha sonra SMB veya NFS araçları aracılığıyla verileri kopyalayabilirsiniz.
4. Data Box Heavy’nin ikinci düğümüne bağlanmak ve veri kopyalamak için bu adımları yineleyin.

Adım adım yönergeler için [Öğretici: Azure’da verileri yönetilen diskler olarak içeri aktarmak için Data Box Heavy kullanma](data-box-heavy-deploy-copy-data-from-vhds.md) bölümüne gidin.

::: zone-end


