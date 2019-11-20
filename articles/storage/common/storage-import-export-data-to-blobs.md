---
title: Azure Blob 'larına veri aktarmak için Azure Içeri/dışarı aktarma kullanma | Microsoft Docs
description: Azure Bloblarına ve Azure Blob 'larına veri aktarmak için Azure portal ' de içeri ve dışarı aktarma işleri oluşturmayı öğrenin.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/06/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: bd15e406cdbee57112ff8ecba158d503e908b73f
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73178021"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Azure Blob depolama alanına veri aktarmak için Azure Içeri/dışarı aktarma hizmetini kullanma

Bu makalede, Azure Içeri/dışarı aktarma hizmeti 'ni kullanarak büyük miktarlarda verileri Azure Blob depolamaya güvenli bir şekilde aktarmak için adım adım yönergeler sağlanmaktadır. Azure Bloblarına veri aktarmak için hizmet, verilerinizi içeren şifrelenmiş disk sürücülerinin bir Azure veri merkezine sevk etmeniz gerekir.  

## <a name="prerequisites"></a>Önkoşullar

Verileri Azure Blob depolamaya aktarmak üzere bir içeri aktarma işi oluşturmadan önce, bu hizmet için aşağıdaki önkoşul listesini dikkatle gözden geçirin ve doldurun. Şunları yapmanız gerekir:

- Içeri/dışarı aktarma hizmeti için kullanılabilen etkin bir Azure aboneliğine sahip olun.
- Depolama kapsayıcısı olan en az bir Azure depolama hesabınız olmalıdır. [İçeri/dışarı aktarma hizmeti Için desteklenen depolama hesapları ve depolama türleri](storage-import-export-requirements.md)listesine bakın. 
    - Yeni bir depolama hesabı oluşturma hakkında bilgi için bkz. [depolama hesabı oluşturma](storage-quickstart-create-account.md). 
    - Depolama kapsayıcısı hakkında daha fazla bilgi için, [depolama kapsayıcısı oluşturma](../blobs/storage-quickstart-blobs-portal.md#create-a-container)bölümüne gidin.
- [Desteklenen türlerde](storage-import-export-requirements.md#supported-disks)yeterli sayıda disk vardır. 
- [Desteklenen BIR işletim sistemi sürümünü](storage-import-export-requirements.md#supported-operating-systems)çalıştıran bir Windows sistemine sahiptir. 
- Windows sisteminde BitLocker 'ı etkinleştirin. Bkz. [BitLocker 'ı etkinleştirme](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
- Windows sisteminde [Waımportexport sürüm 1 ' i indirin](https://www.microsoft.com/download/details.aspx?id=42659) . `waimportexportv1`varsayılan klasöre ayıklayın. Örneğin, `C:\WaImportExportV1`.
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

1.  Windows sistemine SATA bağlayıcıları aracılığıyla disk sürücülerinizi bağlayın.
1.  Her sürücüde tek bir NTFS birimi oluşturun. Birime bir sürücü harfi atayın. Bağlama noktalarını kullanmayın.
2.  NTFS biriminde BitLocker şifrelemesini etkinleştirin. Windows Server sistemi kullanıyorsanız, [Windows server 2012 R2 'de BitLocker 'ı etkinleştirme](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)bölümündeki yönergeleri kullanın.
3.  Verileri şifrelenmiş birime kopyalama. Sürükle ve bırak veya Robocopy ya da böyle bir kopyalama aracını kullanın.
4.  Yönetici ayrıcalıklarına sahip bir PowerShell veya komut satırı penceresi açın. Dizini sıkıştırılmış klasöre dönüştürmek için aşağıdaki komutu çalıştırın:
    
    `cd C:\WaImportExportV1`
5.  Sürücünün BitLocker anahtarını almak için aşağıdaki komutu çalıştırın:
    
    `manage-bde -protectors -get <DriveLetter>:`
6.  Diski hazırlamak için aşağıdaki komutu çalıştırın. **Veri boyutuna bağlı olarak, bu işlem birkaç saat sürebilir.** 

    ```
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite 
    ```
    Bir günlük dosyası, aracı çalıştırdığınız klasörde oluşturulur. Diğer iki dosya da oluşturulur *. bir. xml* dosyası (aracı çalıştırdığınız klasör) ve bir *Drive-manifest. xml* dosyası (verilerin bulunduğu klasör).
    
    Kullanılan parametreler aşağıdaki tabloda açıklanmıştır:

    |Seçenek  |Açıklama  |
    |---------|---------|
    |/j     |. Jrn uzantılı günlük dosyasının adı. Her sürücü için bir günlük dosyası oluşturulur. Günlük dosyası adı olarak disk seri numarasını kullanmanızı öneririz.         |
    |/id     |Oturum KIMLIĞI. Komutun her örneği için benzersiz bir oturum numarası kullanın.      |
    |/t     |Sevk edilecek diskin sürücü harfi. Örneğin, `D`sürücü.         |
    |/BK:     |Sürücünün BitLocker anahtarı. `manage-bde -protectors -get D:` çıktısından sayısal parola      |
    |/srcdir:     |Sevk edilecek diskin sürücü harfi ve `:\`. Örneğin, `D:\`.         |
    |/dstdir:     |Azure depolama 'daki Hedef kapsayıcının adı.         |
    |/blobtype:     |Bu seçenek, verileri içe aktarmak istediğiniz Blobların türünü belirtir. Blok Blobları için, bu `BlockBlob` ve sayfa Blobları için `PagaBlob`.         |
    |/skipwrite:     |Kopyalamak için gereken yeni verilerin olmadığını ve diskteki mevcut verilerin hazırlandığını belirten seçenek.          |
    |/enablecontentmd5:     |Etkinleştirildiğinde, MD5 'nin hesaplanmasını sağlar ve her Blobun `Content-md5` özellik olarak ayarlanır. Bu seçeneği yalnızca, veriler Azure 'a yüklendikten sonra `Content-md5` alanını kullanmak istiyorsanız kullanın. <br> Bu seçenek, veri bütünlüğü denetimini etkilemez (varsayılan olarak gerçekleşir). Ayar, buluta veri yükleme süresini artırır.          |
7. Yüklenmesi gereken her disk için önceki adımı tekrarlayın. Komut satırının her çalışması için, belirtilen ada sahip bir günlük dosyası oluşturulur.
    
    > [!IMPORTANT]
    > - Günlük dosyası ile birlikte, aracın bulunduğu klasörde bir `<Journal file name>_DriveInfo_<Drive serial ID>.xml` dosyası da oluşturulur. . Xml dosyası, günlük dosyasının çok büyük olması halinde bir iş oluşturulurken günlük dosyası yerine kullanılır. 

## <a name="step-2-create-an-import-job"></a>2\. Adım: içeri aktarma işi oluşturma

Azure portal bir içeri aktarma işi oluşturmak için aşağıdaki adımları gerçekleştirin.

1. https://portal.azure.com/ oturum açın.
2. **Tüm hizmetlere > depolama > içeri/dışarı aktarma işlerine**gidin. 
    
    ![Içeri/dışarı aktarma işlerine git](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. **İçeri/dışarı aktarma Işi oluştur**' a tıklayın.

    ![Içeri/dışarı aktarma işi oluştur ' a tıklayın](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. **Temel bilgiler**:

   - **Azure 'A aktar**' ı seçin.
   - İçeri aktarma işi için açıklayıcı bir ad girin. İşlerinizin ilerlemesini izlemek için adı kullanın.
       - Ad yalnızca küçük harf, sayı ve kısa çizgi içerebilir.
       - Ad bir harfle başlamalı ve boşluk içermemelidir.
   - Bir abonelik seçin.
   - Bir kaynak grubu girin veya seçin.  

     ![İçeri aktarma işi oluşturma-1. adım](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. **İş için Ayrıntılar**:

    - Sürücü Hazırlama adımı sırasında elde ettiğiniz sürücü günlüğü dosyalarını karşıya yükleyin. `waimportexport.exe version1` kullanıldıysa, hazırladığınız her sürücü için bir dosyayı karşıya yükleyin. Günlük dosyası boyutu 2 MB 'yi aşarsa, günlük dosyası ile oluşturulan `<Journal file name>_DriveInfo_<Drive serial ID>.xml` de kullanabilirsiniz. 
    - Verilerin bulunacağı hedef depolama hesabını seçin. 
    - Açılan konum, seçilen depolama hesabı bölgesine göre otomatik olarak doldurulur.
   
   ![İçeri aktarma işi oluşturma-2. adım](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. **İade gönderimi bilgileri**:

   - Açılan listeden taşıyıcısı seçin. FedEx/DHL dışında bir taşıyıcı kullanmak istiyorsanız, açılan listeden varolan bir seçeneği belirleyin. Kullanmayı planladığınız taşıyıcı ile ilgili bilgilerle `adbops@microsoft.com` Azure Data Box Işlemler ekibine başvurun.
   - Bu taşıyıcı ile oluşturduğunuz geçerli bir taşıyıcı hesap numarası girin. Microsoft, içeri aktarma işiniz tamamlandıktan sonra sürücüleri size geri göndermek için bu hesabı kullanır. Hesap numaranız yoksa bir [FedEx](https://www.fedex.com/us/oadr/) veya [DHL](https://www.dhl.com/) taşıyıcı hesabı oluşturun.
   - Tümü ve geçerli bir iletişim adı, telefon, e-posta, sokak adresi, şehir, posta, Eyalet/bölge ve ülke/bölge sağlayın. 
        
       > [!TIP] 
       > Tek bir kullanıcı için bir e-posta adresi belirtmek yerine, bir grup e-postası sağlayın. Bu, bir yönetici ayrılsa bile bildirimleri almanızı sağlar.

     ![İçeri aktarma işi oluşturma-3. adım](./media/storage-import-export-data-to-blobs/import-to-blob5.png)
   
5. **Özet**:

   - Özette belirtilen iş bilgilerini gözden geçirin. Disklerin Azure 'a geri sevk edilmesi için iş adı ve Azure veri merkezi teslimat adresini bir yere göz önünde yapın. Bu bilgiler daha sonra sevkiyat etiketinde kullanılır.
   - İçeri aktarma işini oluşturmak için **Tamam** ' ı tıklatın.

     ![İçeri aktarma işi oluşturma-4. adım](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives"></a>3\. Adım: sürücüleri gönderme 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]


## <a name="step-4-update-the-job-with-tracking-information"></a>4\. Adım: işi izleme bilgileriyle güncelleştirme

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>5\. Adım: verileri Azure 'a yüklemeyi doğrulama

İşi tamamlamaya göre izleyin. İş tamamlandıktan sonra verilerinizin Azure 'a yüklendiğini doğrulayın. Şirket içi verileri yalnızca karşıya yükleme işleminin başarılı olduğunu doğruladıktan sonra silin.

## <a name="next-steps"></a>Sonraki adımlar

* [İşi ve sürücü durumunu görüntüleme](storage-import-export-view-drive-status.md)
* [Içeri/dışarı aktarma gereksinimlerini gözden geçirme](storage-import-export-requirements.md)


