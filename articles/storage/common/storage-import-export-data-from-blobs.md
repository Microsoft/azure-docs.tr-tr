---
title: Azure Bloblarından verileri dışarı aktarmak için Azure Içeri/dışarı aktarma kullanma | Microsoft Docs
description: Azure Bloblarından veri aktarmak için Azure portal ' de dışarı aktarma işleri oluşturmayı öğrenin.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: d3166c1f97a81c12b75dd400f591fd92a705cadf
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73178026"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Azure Blob depolamadan veri aktarmak için Azure Içeri/dışarı aktarma hizmetini kullanma
Bu makalede, Azure Blob depolama alanındaki büyük miktarlarda verileri güvenli bir şekilde aktarmak için Azure Içeri/dışarı aktarma hizmeti 'nin nasıl kullanılacağına ilişkin adım adım yönergeler sağlanmaktadır. Hizmet, Azure veri merkezine boş sürücüler sevk etmeniz gerekir. Hizmet, depolama hesabınızdan sürücülere veri aktarır ve sonra sürücüleri geri gönderir.

## <a name="prerequisites"></a>Önkoşullar

Azure Blob depolama 'dan verileri aktarmak üzere bir dışarı aktarma işi oluşturmadan önce, bu hizmet için aşağıdaki önkoşul listesini dikkatle gözden geçirin ve doldurun. Şunları yapmanız gerekir:

- Içeri/dışarı aktarma hizmeti için kullanılabilen etkin bir Azure aboneliğine sahip olun.
- En az bir Azure depolama hesabınız olmalıdır. [İçeri/dışarı aktarma hizmeti Için desteklenen depolama hesapları ve depolama türleri](storage-import-export-requirements.md)listesine bakın. Yeni bir depolama hesabı oluşturma hakkında bilgi için bkz. [depolama hesabı oluşturma](storage-quickstart-create-account.md).
- [Desteklenen türlerde](storage-import-export-requirements.md#supported-disks)yeterli sayıda disk vardır.
- FedEx/DHL hesabınız olmalıdır. FedEx/DHL dışında bir taşıyıcı kullanmak istiyorsanız, `adbops@microsoft.com` ' da Azure Data Box Işlemler ekibine başvurun. 
    - Hesap geçerli olmalıdır, bakiyesi olmalıdır ve dönüş teslim özelliklerine sahip olmalıdır.
    - Dışarı aktarma işi için bir izleme numarası oluştur.
    - Her işin ayrı bir izleme numarası olmalıdır. Aynı izleme numarasına sahip birden çok iş desteklenmez. 
    - Bir taşıyıcı hesabınız yoksa şuraya gidin:
        - [FedEX hesabı oluşturun](https://www.fedex.com/en-us/create-account.html)veya 
        - [BIR DHL hesabı oluşturun](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>1\. Adım: dışarı aktarma işi oluşturma

Azure portal bir dışarı aktarma işi oluşturmak için aşağıdaki adımları gerçekleştirin.

1. https://portal.azure.com/ oturum açın.
2. **Tüm hizmetlere > depolama > içeri/dışarı aktarma işlerine**gidin. 

    ![Içeri/dışarı aktarma işlerine git](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. **İçeri/dışarı aktarma Işi oluştur**' a tıklayın.

    ![Içeri/dışarı aktarma işi ' ne tıklayın](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. **Temel bilgiler**:
    
    - **Azure 'Dan dışarı aktar**' ı seçin. 
    - Dışarı aktarma işi için açıklayıcı bir ad girin. İşlerinizin ilerlemesini izlemek için seçtiğiniz adı kullanın. 
        - Ad yalnızca küçük harf, sayı, kısa çizgi ve alt çizgi içerebilir.
        - Ad bir harfle başlamalı ve boşluk içermemelidir. 
    - Bir abonelik seçin.
    - Bir kaynak grubu girin veya seçin.

        ![Temel Bilgiler](./media/storage-import-export-data-from-blobs/export-from-blob3.png) 
    
3. **İş için Ayrıntılar**:

    - Aktarılacağı verilerin bulunduğu depolama hesabını seçin. Bir depolama hesabını, bulunduğu yere yakın bir şekilde kullanın.
    - Açılan konum, seçilen depolama hesabı bölgesine göre otomatik olarak doldurulur. 
    - Depolama hesabınızdan, boş sürücünüze veya sürücülere dışarı aktarmak istediğiniz blob verilerini belirtin. 
    - Depolama hesabındaki **tüm blob verilerini dışa aktarmayı** seçin.
    
         ![Tümünü dışarı aktar](./media/storage-import-export-data-from-blobs/export-from-blob4.png) 

    - Hangi kapsayıcıları ve Blobların dışarı aktarılacağını belirtebilirsiniz.
        - **Dışarı aktarılacak bir blob belirtmek için**: **eşittir** seçiciyi kullanın. Kapsayıcının adından başlayarak, Blobun göreli yolunu belirtin. Kök kapsayıcıyı belirtmek için *$root* kullanın.
        - **Önekle başlayan tüm Blobları belirtmek için**: **ile başlar** seçiciyi kullanın. '/' Eğik çizgiyle başlayan öneki belirtin. Ön ek, kapsayıcı adının ön eki, tüm kapsayıcı adı ya da tüm kapsayıcı adı ve ardından blob adının öneki olabilir. İşlem sırasında hataları önlemek için, bu ekran görüntüsünde gösterildiği gibi BLOB yollarını geçerli biçimde sağlamanız gerekir. Daha fazla bilgi için bkz. [geçerli blob yolları örnekleri](#examples-of-valid-blob-paths). 
   
           ![Seçili kapsayıcıları ve Blobları dışarı aktar](./media/storage-import-export-data-from-blobs/export-from-blob5.png) 

    - Blob listesi dosyasından dışa aktarabilirsiniz.

        ![Blob listesi dosyasından dışarı aktar](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  
   
   > [!NOTE]
   > Dışarı aktarılacak blob veri kopyalama sırasında kullanılıyorsa, Azure Içeri/dışarı aktarma hizmeti Blobun anlık görüntüsünü alır ve anlık görüntüyü kopyalar.
 

4. **İade gönderimi bilgileri**:

    - Açılan listeden taşıyıcısı seçin. FedEx/DHL dışında bir taşıyıcı kullanmak istiyorsanız, açılan listeden varolan bir seçeneği belirleyin. Kullanmayı planladığınız taşıyıcı ile ilgili bilgilerle `adbops@microsoft.com` Azure Data Box Işlemler ekibine başvurun.
    - Bu taşıyıcı ile oluşturduğunuz geçerli bir taşıyıcı hesap numarası girin. Microsoft bu hesabı, dışa aktarma işiniz tamamlandıktan sonra sürücüleri size geri göndermek için kullanır. 
    - Tümü ve geçerli bir iletişim adı, telefon, e-posta, sokak adresi, şehir, posta, Eyalet/bölge ve ülke/bölge sağlayın.

        > [!TIP] 
        > Tek bir kullanıcı için bir e-posta adresi belirtmek yerine, bir grup e-postası sağlayın. Bu, bir yönetici ayrılsa bile bildirimleri almanızı sağlar.
   
5. **Özet**:

    - İşin ayrıntılarını gözden geçirin.
    - Azure 'a disklerin gönderimi için iş adını ve Azure veri merkezi teslimat adresini bir yere göz önünde yapın. 

        > [!NOTE] 
        > Diskleri Azure portal belirtilen veri merkezine her zaman gönderin. Diskler yanlış veri merkezine sevk edildiğinde iş işlenmez.

    - Dışarı aktarma işi oluşturmayı gerçekleştirmek için **Tamam** ' ı tıklatın.

## <a name="step-2-ship-the-drives"></a>2\. Adım: sürücüleri gönderme

İhtiyaç duyduğunuz sürücü sayısını görmüyorsanız, [sürücü sayısını kontrol](#check-the-number-of-drives)edin. Sürücü sayısını biliyorsanız, sürücüleri teslim edin.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>3\. Adım: işi izleme bilgileriyle güncelleştirme

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]


## <a name="step-4-receive-the-disks"></a>4\. Adım: diskleri alma
Pano işi tamamladıktan sonra, diskler size gönderilir ve sevkıyatın izleme numarası portalda kullanılabilir.

1. İçe aktarılmış verileri olan sürücüleri aldıktan sonra, sürücülerin kilidini açmak için BitLocker anahtarlarını almanız gerekir. Azure portal dışarı aktarma işine gidin. **İçeri/dışarı aktarma** sekmesine tıklayın. 
2. Listeden dışarı aktarma işinizi seçin ve tıklayın. **BitLocker anahtarlarına** gidin ve anahtarları kopyalayın.
   
   ![Dışarı aktarma işi için BitLocker anahtarlarını görüntüle](./media/storage-import-export-service/export-job-bitlocker-keys.png)

3. Disklerin kilidini açmak için BitLocker anahtarlarını kullanın.

Dışarı aktarma işlemi tamamlanmıştır. Şu anda işi silebilir veya 90 gün sonra otomatik olarak silinir.


## <a name="check-the-number-of-drives"></a>Sürücü sayısını denetleyin

Bu *isteğe bağlı* adım, dışa aktarma işi için gereken sürücü sayısını belirlemenize yardımcı olur. [Desteklenen BIR işletim sistemi sürümünü](storage-import-export-requirements.md#supported-operating-systems)çalıştıran bir Windows sisteminde bu adımı gerçekleştirin.

1. Windows sisteminde [Waımportexport sürüm 1 ' i indirin](https://www.microsoft.com/download/details.aspx?id=42659) . 
2. `waimportexportv1`varsayılan klasöre ayıklayın. Örneğin, `C:\WaImportExportV1`.
3. Yönetici ayrıcalıklarına sahip bir PowerShell veya komut satırı penceresi açın. Dizini sıkıştırılmış klasöre dönüştürmek için aşağıdaki komutu çalıştırın:
    
    `cd C:\WaImportExportV1`

4. Seçili Bloblar için gereken disk sayısını denetlemek için şu komutu çalıştırın:

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Parametreler aşağıdaki tabloda açıklanmıştır:
    
    |Komut satırı parametresi|Açıklama|  
    |--------------------------|-----------------|  
    |**/logdir:**|İsteğe bağlı. Günlük dizini. Ayrıntılı günlük dosyaları bu dizine yazılır. Belirtilmemişse, geçerli dizin günlük dizini olarak kullanılır.|  
    |**sn**|Gereklidir. Dışarı aktarma işi için depolama hesabının adı.|  
    |**sor**|Yalnızca bir kapsayıcı SAS belirtilmemişse gereklidir. Dışarı aktarma işi için depolama hesabının hesap anahtarı.|  
    |**/csas:**|Yalnızca bir depolama hesabı anahtarı belirtilmemişse gereklidir. Dışarı aktarma işine verilecek Blobları listelemek için kapsayıcı SAS.|  
    |**/ExportBlobListFile:**|Gereklidir. Dışarı aktarılacak Bloblar için blob yollarının listesini veya blob yolu öneklerini içeren XML dosyasının yolu. Içeri/dışarı aktarma hizmeti REST API [Iş koyma](/rest/api/storageimportexport/jobs) işlemindeki `BlobListBlobPath` öğesinde kullanılan dosya biçimi.|  
    |**/DriveSize:**|Gereklidir. Bir dışa aktarma işi için kullanılacak sürücülerin boyutu, *örn.* 500 GB, 1,5 TB.|  

    [Önizleme dışa aktarma komutuna bir örnek](#example-of-previewexport-command)görüntüleyin.
 
5. Dışa aktarma işi için sevk edilecek sürücüleri okuyup yazverecağınızı kontrol edin.

### <a name="example-of-previewexport-command"></a>Önizleme dışa aktarma komutu örneği

Aşağıdaki örnekte `PreviewExport` komutu gösterilmektedir:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
Dışarı aktarma blobu liste dosyası, burada gösterildiği gibi BLOB adlarını ve BLOB öneklerini içerebilir:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Azure Içeri/dışarı aktarma aracı, dışarı aktarılacak tüm Blobları listeler ve gerekli ek yükü hesaba katarak bu dosyaları belirtilen boyuttaki sürücülere paketlerinizi hesaplar ve ardından blob 'ları ve sürücü kullanım bilgilerini tahmin etmek için gereken sürücü sayısını tahmin eder.  
  
Bilgi günlüklerinin atlanmasıyla birlikte çıktının bir örneği aşağıda verilmiştir:  
  
```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  
  
Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```

## <a name="examples-of-valid-blob-paths"></a>Geçerli blob yolları örnekleri

Aşağıdaki tabloda geçerli blob yollarının örnekleri gösterilmektedir:
   
   | İsindeki | Blob yolu | Açıklama |
   | --- | --- | --- |
   | Ile başlar |/ |Depolama hesabındaki tüm Blobları dışa aktarır |
   | Ile başlar |/$root/ |Kök kapsayıcıdaki tüm Blobları dışa aktarır |
   | Ile başlar |/Book |Önek **defteriyle** başlayan herhangi bir kapsayıcıdaki tüm Blobları dışa aktarır |
   | Ile başlar |öğelerini |Kapsayıcı **müziindeki** tüm Blobları dışa aktarır |
   | Ile başlar |/Music/aşk |Ön ek **sevimle** başlayan **kapsayıcıdaki** tüm Blobları dışa aktarır |
   | Eşittir |$root/logo.exe |Kök kapsayıcıda blob **logo. bmp** dışa aktarır |
   | Eşittir |Videolar/öykü. mp4 |Kapsayıcı **videolarında** blob **öykü. mp4** dışa aktarır |

## <a name="next-steps"></a>Sonraki adımlar

* [İşi ve sürücü durumunu görüntüleme](storage-import-export-view-drive-status.md)
* [Içeri/dışarı aktarma gereksinimlerini gözden geçirme](storage-import-export-requirements.md)


