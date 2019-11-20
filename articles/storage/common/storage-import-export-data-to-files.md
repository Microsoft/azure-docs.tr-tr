---
title: Azure dosyaları 'na veri aktarmak için Azure Içeri/dışarı aktarma kullanma | Microsoft Docs
description: Azure dosyalarına veri aktarmak için Azure portal içeri aktarma işleri oluşturmayı öğrenin.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 1799acdc7a6969d88936705006d67a6ea832fd81
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300279"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Azure dosyaları 'na veri aktarmak için Azure Içeri/dışarı aktarma hizmeti 'ni kullanma

Bu makalede, Azure Içeri/dışarı aktarma hizmeti 'nin büyük miktarlarda verileri Azure dosyalarına güvenli bir şekilde aktarmak için nasıl kullanılacağı hakkında adım adım yönergeler sağlanmaktadır. Verileri içeri aktarmak için hizmet, verilerinizi içeren desteklenen disk sürücülerinin bir Azure veri merkezine sevk etmeniz gerekir.  

Içeri/dışarı aktarma hizmeti, Azure depolama 'ya yalnızca Azure dosyalarını içeri aktarmayı destekler. Azure dosyalarını dışarı aktarma desteklenmiyor.

## <a name="prerequisites"></a>Önkoşullar

Azure dosyalarına veri aktarmaya yönelik bir içeri aktarma işi oluşturmadan önce, aşağıdaki önkoşul listesini dikkatle gözden geçirin ve doldurun. Şunları yapmanız gerekir:

- Içeri/dışarı aktarma hizmeti ile kullanmak için etkin bir Azure aboneliğine sahip olmanız gerekir.
- En az bir Azure depolama hesabınız olmalıdır. [İçeri/dışarı aktarma hizmeti Için desteklenen depolama hesapları ve depolama türleri](storage-import-export-requirements.md)listesine bakın. Yeni bir depolama hesabı oluşturma hakkında bilgi için bkz. [depolama hesabı oluşturma](storage-quickstart-create-account.md).
- [Desteklenen türlerde](storage-import-export-requirements.md#supported-disks)yeterli sayıda disk vardır. 
- [Desteklenen BIR işletim sistemi sürümünü](storage-import-export-requirements.md#supported-operating-systems)çalıştıran bir Windows sistemine sahiptir.
- Windows sisteminde [Waımportexport sürüm 2](https://aka.ms/waiev2) ' nı indirin. Varsayılan klasöre unzip `waimportexport`. Örneğin, `C:\WaImportExport`.
- FedEx/DHL hesabınız olmalıdır. FedEx/DHL dışında bir taşıyıcı kullanmak istiyorsanız, `adbops@microsoft.com` ' da Azure Data Box Işlemler ekibine başvurun.  
    - Hesap geçerli olmalıdır, bakiyesi olmalıdır ve dönüş teslim özelliklerine sahip olmalıdır.
    - Dışarı aktarma işi için bir izleme numarası oluştur.
    - Her işin ayrı bir izleme numarası olmalıdır. Aynı izleme numarasına sahip birden çok iş desteklenmez.
    - Bir taşıyıcı hesabınız yoksa şuraya gidin:
        - [FedEX hesabı oluşturun](https://www.fedex.com/en-us/create-account.html)veya 
        - [BIR DHL hesabı oluşturun](http://www.dhl-usa.com/en/express/shipping/open_account.html).
 


## <a name="step-1-prepare-the-drives"></a>1\. Adım: sürücüleri hazırlama

Bu adım bir günlük dosyası oluşturur. Günlük dosyası, sürücü seri numarası, şifreleme anahtarı ve depolama hesabı ayrıntıları gibi temel bilgileri depolar.

Sürücüleri hazırlamak için aşağıdaki adımları gerçekleştirin.

1. Windows sistemine SATA bağlayıcıları aracılığıyla disk sürücülerimizi bağlayın.
2. Her sürücüde tek bir NTFS birimi oluşturun. Birime bir sürücü harfi atayın. Bağlama noktalarını kullanmayın.
3. Aracın bulunduğu kök klasördeki *DataSet. csv* dosyasını değiştirin. Bir dosyayı veya klasörü veya her ikisini de içeri aktarmak istediğinize bağlı olarak, aşağıdaki örneklere benzer şekilde *DataSet. csv* dosyasına giriş ekleyin.  

   - **Bir dosyayı içeri aktarmak için**: aşağıdaki örnekte kopyalanacak veriler C: sürücüsünde bulunur. *MyFile1. txt* dosyanız *MyAzureFileshare1*köküne kopyalanır. *MyAzureFileshare1* yoksa, Azure depolama hesabında oluşturulur. Klasör yapısı korunur.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
    
       ```
   - **Bir klasörü içeri aktarmak için**: *MyFolder2* altındaki tüm dosyalar ve klasörler yinelemeli olarak FileShare 'e kopyalanır. Klasör yapısı korunur.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            
       ```
     İçeri aktarılan klasörlere veya dosyalara karşılık gelen aynı dosyada birden çok giriş yapılabilir. 

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
                        
       ```
     [Veri KÜMESI CSV dosyasını hazırlama](storage-import-export-tool-preparing-hard-drives-import.md)hakkında daha fazla bilgi edinin.
    

4. Aracın bulunduğu kök klasördeki *driveset. csv* dosyasını değiştirin. Aşağıdaki örneklere benzer şekilde *driveset. csv* dosyasına girdi ekleyin. Sürücünün hazırlanabilmesi için disk listesini doğru bir şekilde seçmesini sağlamak üzere sürücü kümesi dosyasında diskler ve karşılık gelen sürücü harfleri bulunur.

    Bu örnek, iki diskin bağlı olduğunu ve temel NTFS birimlerinin G:\ olduğunu varsayar. ve H:\ oluşturulur. G: zaten şifrelenirken h:\şifrelenmez. Araç, H:\ barındıran diski biçimlendirir ve şifreler yalnızca (ve G: \) değil.

   - **Şifrelenmeyen bir disk için**: diskte BitLocker şifrelemesini etkinleştirmek üzere *şifrelemeyi* belirtin.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```
    
   - **Zaten şifrelenmiş bir disk için**: *Alreadyencrypted* belirtip BitLocker anahtarını sağlayın.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Birden fazla girişe karşılık gelen aynı dosyada birden çok giriş yapılabilir. [Sürücü KÜMESI CSV dosyasını hazırlama](storage-import-export-tool-preparing-hard-drives-import.md)hakkında daha fazla bilgi edinin. 

5. Verileri disk sürücüsüne kopyalamak ve hazırlamak için `PrepImport` seçeneğini kullanın. İlk kopyalama oturumunda, dizinleri ve/veya dosyaları yeni bir kopyalama oturumuyla kopyalamak için aşağıdaki komutu çalıştırın:

       ```
       .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
       ```

   Aşağıda bir içeri aktarma örneği gösterilmektedir.
  
       ```
       .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
       ```
 
6. Komut satırının her çalışması için `/j:` parametresiyle birlikte verdiğiniz bir günlük dosyası oluşturulur. Hazırladığınız her sürücü, içeri aktarma işini oluştururken karşıya yüklenmesi gereken bir günlük dosyasına sahiptir. Günlük dosyaları olmayan sürücüler işlenmedi.

    > [!IMPORTANT]
    > - Disk sürücüsü veya günlük dosyası üzerinde disk hazırlama işlemini tamamladıktan sonra verileri değiştirmeyin.

Ek örnekler için, [günlük dosyaları Için örneklere](#samples-for-journal-files)gidin.

## <a name="step-2-create-an-import-job"></a>2\. Adım: içeri aktarma işi oluşturma 

Azure portal bir içeri aktarma işi oluşturmak için aşağıdaki adımları gerçekleştirin.
1. @No__t-0 ' da oturum açın.
2. **Tüm hizmetlere > depolama > içeri/dışarı aktarma işlerine**gidin. 

    ![Içeri/dışarı aktarmaya git](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. **İçeri/dışarı aktarma Işi oluştur**' a tıklayın.

    ![Içeri/dışarı aktarma işi ' ne tıklayın](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. **Temel bilgiler**:

    - **Azure 'A aktar**' ı seçin.
    - İçeri aktarma işi için açıklayıcı bir ad girin. Bu adı, işleri devam ederken ve tamamlandıktan sonra izlemek için kullanın.
        -  Bu ad yalnızca küçük harf, sayı, kısa çizgi ve alt çizgi içerebilir.
        -  Ad bir harfle başlamalı ve boşluk içermemelidir. 
    - Bir abonelik seçin.
    - Kaynak grubunu seçin. 

        ![İçeri aktarma işi oluşturma-1. adım](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. **İş için Ayrıntılar**:
    
    - Yukarıdaki [Adım 1: sürücüleri hazırlama](#step-1-prepare-the-drives)sırasında oluşturduğunuz günlük dosyalarını karşıya yükleyin. 
    - Verilerin aktarılacağı depolama hesabını seçin. 
    - Açılan konum, seçilen depolama hesabı bölgesine göre otomatik olarak doldurulur.
   
       ![İçeri aktarma işi oluşturma-2. adım](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. **İade gönderimi bilgileri**:

    - Açılır listeden taşıyıcısı seçin. FedEx/DHL dışında bir taşıyıcı kullanmak istiyorsanız, açılan listeden varolan bir seçeneği belirleyin. @No__t-0 ' da Azure Data Box Işlemler ekibine başvurarak kullanmayı planladığınız taşıyıclarla ilgili bilgileri kullanın.
    - Bu taşıyıcı ile oluşturduğunuz geçerli bir taşıyıcı hesap numarası girin. Microsoft, içeri aktarma işiniz tamamlandıktan sonra sürücüleri size geri göndermek için bu hesabı kullanır. 
    - Tümü ve geçerli bir iletişim adı, telefon, e-posta, sokak adresi, şehir, posta, Eyalet/bölge ve ülke/bölge sağlayın.

        > [!TIP] 
        > Tek bir kullanıcı için bir e-posta adresi belirtmek yerine, bir grup e-postası sağlayın. Bu, bir yönetici ayrılsa bile bildirimleri almanızı sağlar.

       ![İçeri aktarma işi oluşturma-3. adım](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

   
5. **Özet**:

    - Disklerin Azure 'a geri sevk edilmesi için Azure veri merkezi teslimat adresini sağlayın. İş adının ve tam adresin Sevkiyat Etiketi üzerinde belirtildiğinden emin olun.
    - İçeri aktarma işi oluşturmayı gerçekleştirmek için **Tamam** ' ı tıklatın.

        ![İçeri aktarma işi oluşturma-4. adım](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>3\. Adım: sürücüleri Azure veri merkezine gönderme 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>4\. Adım: işi izleme bilgileriyle güncelleştirme

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>5\. Adım: verileri Azure 'a yüklemeyi doğrulama

İşi tamamlamaya göre izleyin. İş tamamlandıktan sonra verilerinizin Azure 'a yüklendiğini doğrulayın. Şirket içi verileri yalnızca karşıya yükleme işleminin başarılı olduğunu doğruladıktan sonra silin.

## <a name="samples-for-journal-files"></a>Günlük dosyaları için örnekler

**Daha fazla sürücü eklemek**için yeni bir sürücü kümesi dosyası oluşturun ve komutu aşağıda gösterildiği gibi çalıştırın. 

*Initialdriveset. csv* dosyasında belirtilenden farklı disk sürücülerine sonraki kopyalama oturumları için yeni bir sürücü kümesi *. csv* dosyası belirtin ve bunu `AdditionalDriveSet` parametresine bir değer olarak sağlayın. **Aynı günlük dosyası** adını kullanın ve **yenı bir oturum kimliği**sağlayın. AdditionalDriveset CSV dosyası biçimi ınitialdriveset biçimiyle aynı.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

Aşağıda bir içeri aktarma örneği gösterilmektedir.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


Aynı sürücü kümesine ek veri eklemek için, ek dosya/dizin kopyalamak üzere sonraki kopyalama oturumları için Prepımport komutunu kullanın.

*Initialdriveset. csv* dosyasında belirtilen sabit disk sürücülerine sonraki kopyalama oturumları için, **aynı günlük dosyası** adını belirtin ve **Yeni bir oturum kimliği**sağlayın; depolama hesabı anahtarı sağlanması gerekmez.

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

Aşağıda bir içeri aktarma örneği gösterilmektedir.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [İşi ve sürücü durumunu görüntüleme](storage-import-export-view-drive-status.md)
* [Içeri/dışarı aktarma gereksinimlerini gözden geçirme](storage-import-export-requirements.md)


