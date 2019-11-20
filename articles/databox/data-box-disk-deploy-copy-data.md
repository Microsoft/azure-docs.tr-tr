---
title: Verileri Azure Data Box Disk'e kopyalama öğreticisi| Microsoft Docs
description: Azure Data Box Disk'inizi nasıl veri kopyalayacağınızı öğrenmek için bu öğreticiyi kullanın
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: c309a7cb18086526b23c875b41d9d4f4db4bc213
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70231381"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>Öğretici: Verileri Azure Data Box Disk'e kopyalama ve doğrulama

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-azure-data-box-disk-and-validate"></a>Verileri Azure Data Box Disk'e kopyalama ve doğrulama

Diskler bağlanıp kilidi açıldıktan sonra, kaynak veri sunucunuzdaki verileri disklerinize kopyalayabilirsiniz. Veri kopyalama işlemi tamamlandıktan sonra, verilerin Azure'a başarıyla yüklendiğinden emin olmak için verileri doğrulamanız gerekir.

::: zone-end

::: zone target="docs"

Bu öğreticide ana bilgisayarınızdan veri kopyalama ve veri bütünlüğünü doğrulamak için sağlama toplamı oluşturma adımları anlatılmaktadır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Verileri Data Box Disk'e kopyalama
> * Verileri doğrulama

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce aşağıdakilerden emin olun:
- [Öğretici: Azure Data Box Disk’i yükleme ve yapılandırma](data-box-disk-deploy-set-up.md)’yı tamamladınız.
- Disklerinizin kilitleri açılır ve diskler bir istemci bilgisayara bağlanır.
- Disklere veri kopyalamak için kullanılan istemci bilgisayar [Desteklenen işletim sistemi](data-box-disk-system-requirements.md##supported-operating-systems-for-clients) çalıştırmalıdır.
- Verileriniz için hedeflenen depolama türünün [Desteklenen depolama türleri](data-box-disk-system-requirements.md#supported-storage-types-for-upload) ile eşleştiğinden emin olun.
- [Azure nesne boyutu limitlerinde yönetilen disk limitleri](data-box-disk-limits.md#azure-object-size-limits) konusunu gözden geçirin.


## <a name="copy-data-to-disks"></a>Disklere veri kopyalama

Verileri disklere kopyalamadan önce aşağıdaki konuları gözden geçirin:

- Verilerin uygun dosya biçimine karşılık gelen klasörlere kopyalandığından emin olmak sizin sorumluluğunuzdur. Örneğin blok blobu verilerinin blok blobu klasörlerine kopyalanması gerekir. Veri biçimi uygun klasörle (depolama türü) eşleşmiyorsa veriler Azure'a yüklenemez.
- Veri kopyalama sırasında veri boyutunun [Azure depolama ve Data Box Disk sınırları](data-box-disk-limits.md) içinde belirtilen boyut sınırlarına uygun olduğundan emin olun.
- Data Box Disk tarafından yüklenen verilerin Data Box Disk haricinde başka bir uygulama tarafından da yüklenmesi durumunda yükleme işinde hata oluşabilir ve veri bozulması yaşanabilir.

   > [!IMPORTANT]
   >  Sipariş oluşturma sırasında depolama hedeflerinden biri olarak yönetilen diskleri belirttiyseniz aşağıdaki bölüm geçerlidir.

- Önceden oluşturulmuş tüm klasörler ve tüm Data Box Disk’ler arasında, bir kaynak grubunda belirli bir ada sahip yalnızca bir yönetilen diskiniz olabilir. Bu durum, önceden oluşturulan klasörlere yüklenen VHD'lerin benzersiz adlara sahip olması gerektiği anlamına gelir. Verilen adın bir kaynak grubunda zaten var olan bir yönetilen diskle eşleşmediğinden emin olun. VHD'ler aynı ada sahipse, o adı taşıyan yalnızca bir VHD yönetilen diske dönüştürülür. Diğer VHD'ler, hazırlama depolama hesabına sayfa blobları olarak yüklenir.
- VHD'leri her zaman önceden oluşturulmuş klasörlerden birine kopyalayın. VHD'leri bu klasörlerin dışına veya oluşturduğunuz bir klasöre kopyalarsanız, VHD'ler Azure Depolama hesabına yönetilen diskler olarak değil sayfa blobları olarak yüklenir.
- Yönetilen diskler oluşturmak için yalnızca sabit VHD'ler karşıya yüklenebilir. Dinamik VHD'ler, fark kayıt VHD'leri veya VHDX dosyaları desteklenmez.


Bilgisayarınızla Data Box Disk arasında bağlantı kurmak ve veri kopyalamak için aşağıdaki adımları gerçekleştirin.

1. Kilidi açılan sürücünün içeriğini görüntüleyin. Sürücüdeki önceden oluşturulmuş klasör ve alt klasörlerin listesi, Data Box Disk siparişi verilirken belirlenen seçeneklere bağlı olarak değişir.

    |Seçili depolama hedefi  |Depolama hesabı türü|Hazırlama depolama hesabı türü |Klasörler ve alt klasörler  |
    |---------|---------|---------|------------------|
    |Depolama hesabı     |GPv1 veya GPv2                 | NA | BlockBlob <br> PageBlob <br> AzureFile        |
    |Depolama hesabı     |Blob depolama hesabı         | NA | BlockBlob        |
    |Yönetilen diskler     |NA | GPv1 veya GPv2         | ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>        |
    |Depolama hesabı <br> Yönetilen diskler     |GPv1 veya GPv2 | GPv1 veya GPv2         |BlockBlob <br> PageBlob <br> AzureFile <br> ManagedDisk<ul> <li> PremiumSSD </li><li>StandardSSD</li><li>StandardHDD</li></ul>         |
    |Depolama hesabı <br> Yönetilen diskler    |Blob depolama hesabı | GPv1 veya GPv2         |BlockBlob <br> ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>         |

    Bir GPv2 depolama hesabının belirtildiği siparişin örnek ekran görüntüsü aşağıda gösterilmiştir:

    ![Disk sürücüsünün içeriği](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. Blok blobu olarak içeri aktarılması gereken verileri *BlockBlob* klasörüne kopyalayın. Benzer şekilde, VHD/VHDX gibi verileri *PageBlob* klasörüne kopyalayın ve verileri *AzureFile* klasörüne kopyalayın.

    BlockBlob ve PageBlob klasörlerinin altındaki her klasör için Azure depolama hesabında bir kapsayıcı oluşturulur. BlockBlob ve PageBlob klasörlerinin altındaki tüm dosyalar Azure Depolama hesabındaki varsayılan `$root` kapsayıcısına kopyalanır. `$root` kapsayıcısındaki tüm dosyalar her zaman blok blobu olarak yüklenir.

   Dosyaları *AzureFile* klasörü içindeki bir klasöre kopyalayın. *AzureFile* klasöründeki bir alt klasör, bir dosya paylaşımı oluşturur. Doğrudan *AzureFile* klasörüne kopyalanan dosyalar başarısız olur ve blok blobu olarak yüklenir.

    Kök dizinde dosya ve klasörler varsa veri kopyalama işlemine başlamadan önce bunları farklı bir klasöre taşımanız gerekir.

    > [!IMPORTANT]
    > Tüm kapsayıcılar, bloblar ve dosya adlarının [Azure adlandırma kurallarına](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) uygun olması gerekir. Bu kurallara uyulmaması halinde veriler Azure'a yüklenemez.

3. Dosyaları kopyalarken dosya boyutlarının blok blobları için en fazla ~4,7 TiB, sayfa blobları için ~8 TiB ve Azure Dosyalar için ~1 TiB olduğundan emin olun. 
4. Verileri kopyalamak için Veri Gezgini'nde sürükle ve bırak komutlarını kullanabilirsiniz. Verilerinizi kopyalamak için Robocopy gibi SMB uyumlu herhangi bir dosya kopyalama aracını da kullanabilirsiniz. Aşağıdaki Robocopy komutunu kullanarak birden fazla kopyalama işlemini başlatabilirsiniz:

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    Komut parametreleri ve seçenekleri aşağıda belirtilmiştir:
    
    |Parametreler/Seçenekler  |Açıklama |
    |--------------------|------------|
    |Kaynak            | Kaynak dizin yolunu belirtir.        |
    |Hedef       | Hedef dizin yolunu belirtir.        |
    |/E                  | Boş dizinler dahil olmak üzere alt dizinleri kopyalar. |
    |/MT[:N]             | N iş parçacığına sahip çoklu iş parçacıklı kopyalama işlemleri oluşturur ve burada N, 1 ile 128 arasında bir tam sayıdır. <br>N için varsayılan değer 8 olarak belirlenmiştir.        |
    |/R: \<N>             | Başarısız kopyalama işlemleri için yeniden deneme sayısını belirtir. N için varsayılan değer 1.000.000 (bir milyon yeniden deneme) olarak belirlenmiştir.        |
    |/W: \<N>             | Yeniden deneme işlemleri arasındaki bekleme süresini saniye cinsinden belirtir. N için varsayılan değer 30 (30 saniyelik bekleme süresi) olarak belirlenmiştir.        |
    |/NFL                | Dosya adlarının günlüğü alınmayacağını belirtir.        |
    |/NDL                | Dizin adlarının günlüğü alınmayacağını belirtir.        |
    |/FFT                | FAT dosya sürelerini (iki saniyelik duyarlık) kullanır.        |
    |/Günlük:\<Günlük Dosyası>     | Durum çıkışını günlük dosyasına yazar (var olan günlük dosyasının üzerine yazar).         |

    Her birinde birden fazla işin çalıştığı birden fazla disk kullanılabilir.

6. İş devam ederken kopyalama durumunu kontrol edin. Aşağıdaki örnekte dosyaları Data Box Disk'e kopyalamak için kullanılan Robocopy komutunun çıkışı gösterilmektedir.

    ```
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
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:64 /E /R:1 /W:1 /FFT 
    -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:58 PM
       Source : C:\Git\azure-docs-pr\contributor-guide\
         Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
      Options : *.* /DCOPY:DA /COPY:DAT /MT:8 /R:1000000 /W:30
    
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
       Times :   0:00:05   0:00:00                       0:00:00   0:00:00
        
       Speed :                5620 Bytes/sec.
       Speed :               0.321 MegaBytes/min.
       Ended : Thursday, March 8, 2018 2:34:59 PM
        
    C:\Users>
    ```
 
    Performansı iyileştirmek için veri kopyalama sırasında aşağıdaki Robocopy parametrelerini kullanın.

    |    Platform    |    Çoğunlukla küçük dosyalar, 512 KB altı                           |    Çoğunlukla orta büyüklükteki dosyalar, 512 KB-1 MB arası                      |    Çoğunlukla büyük dosyalar, 1 MB üzeri                             |   
    |----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
    |    Data Box Disk        |    4 Robocopy oturumu* <br> Oturum başına 16 iş parçacığı    |    2 Robocopy oturumu* <br> Oturum başına 16 iş parçacığı    |    2 Robocopy oturumu* <br> Oturum başına 16 iş parçacığı    |
    
    **Her Robocopy oturumunda en fazla 7.000 dizin ve 150 milyon dosya olabilir.*
    
    >[!NOTE]
    > Yukarıda önerilen parametreler, şirket içi testlerde kullanılan ortama dayalıdır.
    
    Robocopy komutu hakkında daha fazla bilgi için bkz. [Robocopy ve birkaç örnek](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

6. Kopyalanan dosyaları görüntülemek ve doğrulamak için hedef klasörü açın. Kopyalama işlemi sırasında hatayla karşılaşırsanız sorun giderme için günlük dosyalarını indirin. Günlük dosyaları robocopy komutunda belirtilen dizine kaydedilir.
 
### <a name="split-and-copy-data-to-disks"></a>Verileri bölme ve disklere kopyalama

Birden fazla disk kullanıyorsanız ve bölünerek tüm disklere kopyalanması gereken büyük bir veri kümesine sahipseniz bu isteğe bağlı yordamı kullanabilirsiniz. Data Box Split Copy aracı Windows bilgisayarlarda veri bölme ve kopyalama işlemlerini gerçekleştirmenize yardımcı olur.

>[!IMPORTANT]
> Data Box Split Copy aracı ayrıca verilerinizi doğrular. Verileri kopyalamak için Data Box Split Copy aracını kullanıyorsanız [doğrulama adımını](#validate-data) atlayabilirsiniz.
> Split Copy aracı yönetilen disklerle desteklenmez.

1. Data Box Split Copy aracını Windows bilgisayarınıza indirip yerel bir klasöre ayıkladığınızdan emin olun. Bu araç Windows için Data Box Disk araç takımıyla birlikte indirilmiştir.
2. Dosya Gezgini'ni açın. Veri kaynağı sürücüsünü ve Data Box Disk'e atanmış olan sürücü harflerini not edin. 

     ![Verileri bölme ve kopyalama](media/data-box-disk-deploy-copy-data/split-copy-1.png)
 
3. Kopyalanacak veri kaynağını belirleyin. Örneğin, bu durumda:
    - Aşağıdaki blok blobu tanımlanmıştır.

         ![Verileri bölme ve kopyalama](media/data-box-disk-deploy-copy-data/split-copy-2.png)    

    - Aşağıdaki sayfa blobu tanımlanmıştır.

         ![Verileri bölme ve kopyalama](media/data-box-disk-deploy-copy-data/split-copy-3.png)
 
4. Yazılımı ayıkladığınız klasöre gidin. O klasörde `SampleConfig.json` dosyasını bulun. Bu değiştirip kaydedebileceğiniz bir salt okunur dosyadır.

   ![Verileri bölme ve kopyalama](media/data-box-disk-deploy-copy-data/split-copy-4.png)
 
5. `SampleConfig.json` dosyasını değiştirin.
 
   - İş adı girin. Bunu yaptığınızda Data Box Disk'te bir klasör oluşturulur ve aynı ad Azure depolama hesabında bu disklerle ilişkilendirilmiş olan kapsayıcı için de kullanılır. İş adının Azure kapsayıcı adlandırma kurallarına uygun olması gerekir. 
   - `SampleConfigFile.json` içindeki yol biçimine dikkat ederek kaynak yol belirtin. 
   - Hedef disklere karşılık gelen sürücü harflerini girin. Veriler kaynak yoldan alınarak birden fazla diske kopyalanır.
   - Günlük dosyaları için bir yol belirtin. Varsayılan olarak `.exe` dosyasının bulunduğu dizine gönderilir.

     ![Verileri bölme ve kopyalama](media/data-box-disk-deploy-copy-data/split-copy-5.png)

6. Dosya biçimini doğrulamak için `JSONlint` bölümüne gidin. Dosyayı `ConfigFile.json` olarak kaydedin. 

     ![Verileri bölme ve kopyalama](media/data-box-disk-deploy-copy-data/split-copy-6.png)
 
7. Bir komut istemi penceresi açın. 

8. `DataBoxDiskSplitCopy.exe` dosyasını çalıştırın. Tür

    `DataBoxDiskSplitCopy.exe PrepImport /config:<Your-config-file-name.json>`

     ![Verileri bölme ve kopyalama](media/data-box-disk-deploy-copy-data/split-copy-7.png)
 
9. Betiği sürdürmek için Enter tuşuna basın.

    ![Verileri bölme ve kopyalama](media/data-box-disk-deploy-copy-data/split-copy-8.png)
  
10. Veri kümesi bölünüp kopyalandıktan sonra kopyalama oturumuna ait Split Copy aracı özeti görüntülenir. Örnek çıktı aşağıda gösterilmiştir.

    ![Verileri bölme ve kopyalama](media/data-box-disk-deploy-copy-data/split-copy-9.png)
 
11. Verilerin hedef disklere bölündüğünü doğrulayın. 
 
    ![Kopyalama verilerini bölme](media/data-box-disk-deploy-copy-data/split-copy-10.png)
    ![Kopyalama verilerini bölme](media/data-box-disk-deploy-copy-data/split-copy-11.png)
     
    `n:` sürücüsünün içeriğini ayrıntılı bir şekilde incelediğinizde blok blobu ve sayfa blobu biçimindeki veriler için iki alt klasör oluşturulduğunu görebilirsiniz.
    
     ![Verileri bölme ve kopyalama](media/data-box-disk-deploy-copy-data/split-copy-12.png)

12. Kopyalama oturumu başarısız olursa kurtarıp sürdürmek için şu komutu kullanın:

    `DataBoxDiskSplitCopy.exe PrepImport /config:<configFile.json> /ResumeSession`

Split Copy aracını kullanırken hatalarla karşılaşırsanız [Split Copy aracının hatalarını giderme](data-box-disk-troubleshoot-data-copy.md) bölümüne gidin.

Veri kopyalama işlemi tamamlandıktan sonra verilerinizi doğrulamaya geçebilirsiniz. Split Copy aracını kullandıysanız doğrulamayı atlayın (Split Copy aracı doğrulama da yapar) ve sonraki öğreticiye geçin.


## <a name="validate-data"></a>Verileri doğrulama

Verileri kopyalamak için Split Copy aracını kullanmadıysanız verilerinizi doğrulamanız gerekir. Verileri doğrulamak için aşağıdaki adımları uygulayın.

1. Sağlama toplamı doğrulaması için sürücünüzün *DataBoxDiskImport* klasöründe `DataBoxDiskValidation.cmd` komutunu çalıştırın.
    
    ![Data Box Diski doğrulama aracı çıktısı](media/data-box-disk-deploy-copy-data/data-box-disk-validation-tool-output.png)

2. Uygun olanı seçin. **Her zaman 2. seçeneği işaretleyerek dosyaları doğrulamanızı ve sağlama toplamları oluşturmanızı öneririz**. Bu adım verilerinizin boyutuna bağlı olarak uzun sürebilir. Betik tamamlandığında komut penceresinden çıkın. Doğrulama ve sağlama toplamı alma sırasında herhangi bir hata olursa size bildirilir ve hata günlüklerine bir bağlantı sunulur.

    ![Sağlama toplamı çıktısı](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

    > [!TIP]
    > - İki çalıştırma arasında aracı sıfırlayın.
    > - Küçük dosyalar (~ KB) içeren büyük veri kümeleriyle çalışıyorsanız 1. seçeneği kullanın. Sağlama toplamı almak çok uzun sürebildiği ve performans çok düşük olabildiği için bu seçenek yalnızca dosyaları doğrular.

3. Birden çok disk kullanıyorsanız, komutu her disk için çalıştırın.

Doğrulama sırasında hata görürseniz bkz. [. doğrulama hatalarını giderme](data-box-disk-troubleshoot.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide aşağıdaki Azure Data Box Disk konularını öğrendiniz:

> [!div class="checklist"]
> * Verileri Data Box Disk'e kopyalama
> * Veri bütünlüğünü doğrulama

Data Box Disk'i iade etme ve Azure'a yüklenen verileri doğrulama adımları hakkında bilgi edinmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Data Box verilerinizi Microsoft'a geri gönderme](./data-box-disk-deploy-picked-up.md)

::: zone-end

::: zone target="chromeless"

### <a name="copy-data-to-disks"></a>Disklere veri kopyalama

Bilgisayarınızla Data Box Disk arasında bağlantı kurmak ve veri kopyalamak için aşağıdaki adımları uygulayın.

1. Kilidi açılan sürücünün içeriğini görüntüleyin. Sürücüdeki önceden oluşturulmuş klasör ve alt klasörlerin listesi, Data Box Disk siparişi verilirken belirlenen seçeneklere bağlı olarak değişir.
2. Verileri uygun dosya biçimine karşılık gelen klasörlere kopyalayın. Örneğin, yapılandırılmamış verileri *BlockBlob* klasörüne, VHD veya VHDX verilerini *PageBlob* klasörüne ve dosyaları *AzureFile* klasörüne kopyalayın. Veri biçimi uygun klasörle (depolama türü) eşleşmiyorsa veriler Azure'a yüklenemez.

    - Tüm kapsayıcı, blob ve dosyaların [Azure adlandırma kurallarına](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) ve [Azure nesne boyutu limitlerine](data-box-disk-limits.md#azure-object-size-limits) uyduğundan emin olun. Bu kurallara veya limitlere uyulmaması halinde veriler Azure'a yüklenemez.     
    - Depolama hedeflerinden biri olarak siparişinizde Yönetilen Diskler varsa, [yönetilen disklere](data-box-disk-limits.md#managed-disk-naming-conventions) yönelik adlandırma kurallarına bakın.
    - BlockBlob ve PageBlob klasörlerinin altındaki her klasör için Azure depolama hesabında bir kapsayıcı oluşturulur. *BlockBlob* ve *PageBlob* klasörlerinin altındaki tüm dosyalar Azure Depolama hesabındaki varsayılan $root kapsayıcısına kopyalanır. $root kapsayıcısındaki tüm dosyalar her zaman blok blobu olarak yüklenir.
    - *AzureFile* klasörü içinde bir alt klasör oluşturun. Bu alt klasör, buluttaki bir dosya paylaşımı ile eşlenir. Dosyaları alt klasöre kopyalayın. Doğrudan *AzureFile* klasörüne kopyalanan dosyalar başarısız olur ve blok blobu olarak yüklenir.
    - Kök dizinde dosya ve klasörler varsa veri kopyalama işlemine başlamadan önce bunları farklı bir klasöre taşımanız gerekir.

3. Verilerinizi kopyalamak için Dosya Gezgini ile sürükle bırak özelliğini veya Robocopy gibi SMB uyumlu herhangi bir dosya kopyalama aracını kullanın. Aşağıdaki komutu kullanarak birden fazla kopyalama işlemini başlatabilirsiniz:

    ```
    Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt
    ```
4. Kopyalanan dosyaları görüntülemek ve doğrulamak için hedef klasörü açın. Kopyalama işlemi sırasında hatayla karşılaşırsanız sorun giderme için günlük dosyalarını indirin. Günlük dosyaları robocopy komutunda belirtilen dizine kaydedilir.

Birden fazla disk kullanırken ve tüm disklerde bölünüp kopyalanması gereken büyük bir veri kümeniz olduğunda isteğe bağlı [böl ve kopyala](data-box-disk-deploy-copy-data.md#split-and-copy-data-to-disks) yordamını kullanın.

### <a name="validate-data"></a>Verileri doğrulama

Verilerinizi doğrulamak için aşağıdaki adımları uygulayın.

1. Sağlama toplamı doğrulaması için sürücünüzün *DataBoxDiskImport* klasöründe `DataBoxDiskValidation.cmd` komutunu çalıştırın.
2. Dosyalarınızı doğrulamak ve sağlama toplamları oluşturmak için 2. seçeneği kullanın. Bu adım verilerinizin boyutuna bağlı olarak uzun sürebilir. Doğrulama ve sağlama toplamı alma sırasında herhangi bir hata olursa size bildirilir ve hata günlüklerine bir bağlantı sunulur.

    Veri doğrulama hakkında daha fazla bilgi için bkz. [Verileri doğrulama](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-copy-data#validate-data). Doğrulama sırasında hatalarla karşılaşırsanız bkz. [. doğrulama hatalarını giderme](data-box-disk-troubleshoot.md).

::: zone-end
