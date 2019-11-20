---
title: Azure veri kutusu ağır NFS aracılığıyla veri kopyalamak için öğretici | Microsoft Docs
description: İçin Azure veri kutusu ağır NFS aracılığıyla veri kopyalama hakkında bilgi edinin
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 4361cee3d07408c3abb5031d2ab18c15c92c5e0a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595752"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-nfs"></a>Öğretici: Azure veri kutusu ağır NFS aracılığıyla veri kopyalayın

Bu öğreticide, bağlanma ve ana bilgisayarınızda yerel web kullanıcı Arabirimi için Azure veri kutusu ağır kullanarak veri kopyalama açıklar.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Önkoşullar
> * Veri kutusu ağır için Bağlan
> * Veri kutusu ağır için veri kopyalama

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce aşağıdakilerden emin olun:

1. Tamamladığınızda [Öğreticisi: Azure veri kutusu ağır kümesi](data-box-heavy-deploy-set-up.md).
2. Veri kutusu ağır aldık ve sipariş durumu Portalı'nda **teslim edildi**.
3. Üzerinde veri kutusu ağır kopyalamak istediğiniz verileri içeren bir konak bilgisayar var. Ana bilgisayarınız:
    - [Desteklenen bir işletim sistemi](data-box-heavy-system-requirements.md) çalıştırılmalıdır.
    - Yüksek hızlı bir ağa bağlı olmalıdır. Hızlı kopya hızları için iki 40 GbE bağlantılar (düğüm başına bir adet) paralel olarak yararlanılabilir. 40 GbE bağlantı kullanılabilir değilse, en az iki 10 GbE bağlantılar (düğüm başına bir tane) sahip olmasını öneririz. 

## <a name="connect-to-data-box-heavy"></a>Veri kutusu ağır için Bağlan

Seçilen depolama hesabına bağlı olarak, veri kutusu ağır kadar oluşturur:
- İlişkili her depolama hesabına GPv1 ve GPv2 için üç paylaşım.
- Premium depolama için bir paylaşım.
- Blob depolama hesabı için bir paylaşım.

Bu paylaşımlar cihazın her iki düğümde oluşturulur.

Blok blob ve sayfa blob paylaşımlar altında':
- Birinci düzey varlıkları kapsayıcılardır.
- İkinci düzey, BLOB'ları varlıklardır.

Azure dosyaları paylaşımlarını altında:
- Birinci düzey, paylaşımları varlıklardır.
- İkinci düzey varlıkları dosyalarıdır.

Aşağıdaki tabloda UNC yolunu paylaşımları burada veriler karşıya yüklendikten, veri kutusu yoğun ve Azure depolama yolu URL üzerinde gösterir. Son Azure depolama yolu URL'si UNC paylaşımı yolundan elde edilebilir.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure blok BLOB'ları | <li>Paylaşımları UNC yolu: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure depolama URL'si: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure sayfa blobları  | <li>Paylaşımları UNC yolu: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure depolama URL'si: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Dosyaları       |<li>Paylaşımları UNC yolu: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure depolama URL'si: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Bir Linux ana bilgisayar kullanıyorsanız, NFS istemcilerinin erişmesine izin vermek için Cihazınızı yapılandırmak için aşağıdaki adımları gerçekleştirin.

1. Paylaşıma erişmesine izin verilen istemcilerin IP adreslerini sağlayın. Yerel web arabiriminde **Bağlan ve kopyala** sayfasına gidin. **NFS ayarları** bölümünde **NFS istemci erişimi**'ne tıklayın. 

    ![NFS istemci erişimini yapılandırma 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. NFS istemcisinin IP adresini girin ve **Ekle**'ye tıklayın. Bu adımı tekrarlayarak birden fazla NFS istemcisi için erişim sağlayabilirsiniz. **Tamam**'ı tıklatın.

    ![NFS istemci erişimini yapılandırma 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Linux ana bilgisayarında NFS istemcisinin [desteklenen sürümünün](data-box-system-requirements.md) yüklü olduğundan emin olun. Linux dağıtımınıza uygun sürümü kullanın. 

3. NFS istemcisi yüklendikten sonra, Data Box cihazınızdaki NFS paylaşımını bağlamak için aşağıdaki komutu kullanın:

    `sudo mount <Data Box Heavy device IP>:/<NFS share on Data Box Heavy device> <Path to the folder on local Linux computer>`

    Aşağıdaki örnek, bir veri kutusu ağır paylaşımına NFS bağlanmak gösterilmektedir. Veri kutusu ağır IP `10.161.23.130`, paylaşım `Mystoracct_Blob` olduğundan üzerinde ubuntuVM bağlanmış, bağlama noktası olan `/home/databoxheavyubuntuhost/databoxheavy`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`
    
    Mac istemcileri için ek bir seçenek aşağıdaki gibi eklemeniz gerekecektir: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`

    **Her zaman kopyalamayı düşündüğünüz dosyalar için paylaşımda bir klasör oluşturun ve ardından dosyaları bu klasöre kopyalayın**. Blok blobu altında klasör oluşturulur ve sayfa blob paylaşımları veriler BLOB olarak karşıya bir kapsayıcıyı temsil eder. Dosyaları doğrudan kopyalanamıyor *kök* depolama hesabında klasör.

## <a name="copy-data-to-data-box-heavy"></a>Veri kutusu ağır için veri kopyalama

Veri kutusu ağır paylaşımlarına bağlandıktan sonra sonraki adıma veri kopyalamaktır. Veri kopyalama başlamadan önce aşağıdaki konuları gözden geçirin:

- Verilerin uygun dosya biçimine karşılık gelen paylaşımlara kopyalandığından emin olun. Örneğin blok blobu verilerinin blok blobu paylaşımına kopyalanması gerekir. VHD'ler sayfa BLOB'ları için kopyalayın. Veri biçimi uygun paylaşım türüyle eşleşmiyorsa verilerin Azure'a yüklenmesi başarısız olur.
-  Veri kopyalama sırasında veri boyutu, açıklanan boyutu sınırları uymasını sağlamak [Azure depolama ve veri kutusu ağır sınırları](data-box-heavy-limits.md). 
- Ardından veri kutusu ağır tarafından karşıya yükleniyor, veri, veri kutusu ağır dışında başka uygulamalar tarafından eşzamanlı olarak yüklenirse, bu karşıya yükleme işi hataları ve veri bozulmasına neden olabilir.
- Aynı anda hem SMB hem de NFS kullanmamanızı veya aynı verileri Azure'daki aynı uç hedefe kopyalamamanızı öneririz. Bu gibi durumlarda nihai sonucu kestirmek mümkün olmayabilir.
- **Her zaman kopyalamayı düşündüğünüz dosyalar için paylaşımda bir klasör oluşturun ve ardından dosyaları bu klasöre kopyalayın**. Blok blobu altında klasör oluşturulur ve sayfa blob paylaşımları veriler BLOB olarak karşıya bir kapsayıcıyı temsil eder. Dosyaları doğrudan kopyalanamıyor *kök* depolama hesabında klasör.
- Büyük/küçük harfe dizin ve dosya adları bir NFS paylaşımına veri kutusu ağır NFS için başlayan kümeniz varsa: 
    - Durum adı korunur.
    - Dosyaları büyük/küçük harfe duyarsızdır.
    
    Örneğin, kopyalama `SampleFile.txt` ve `Samplefile.Txt`, durum, cihaza kopyalandığında adında korunur ancak olarak bunlar aynı dosyaya kabul ikinci dosyası birinci üzerine yazar.


Linux ana bilgisayar kullanıyorsanız Robocopy ile benzer bir kopyalama yardımcı programı kullanabilirsiniz. Linux için kullanabileceğiniz bazı alternatifler: [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) veya [Ultracopier](https://ultracopier.first-world.info/).  

`cp` komutu, dizin kopyalamak için en iyi seçeneklerden biridir. Kullanımı hakkında daha fazla bilgi için [cp man sayfalarına](http://man7.org/linux/man-pages/man1/cp.1.html) gidin.

Çok iş parçacığına sahip olan bir kopyalama işlemi için rsync seçeneğini kullanıyorsanız şu yönergeleri izleyin:

 - Linux istemcinizde kullanılan dosya sistemine bağlı olarak **CIFS Utils** veya **NFS Utils** paketini yükleyebilirsiniz.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

 -  **Rsync** ve **Parallel** uygulamalarını yükleyin (Linux dağıtımınıza göre değişir).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

 - Bağlama noktası oluşturun.

    `sudo mkdir /mnt/databoxheavy`

 - Birimi bağlayın.

    `sudo mount -t NFS4  //Databox-heavy-IP-Address/share_name /mnt/databoxheavy` 

 - Klasör dizin yapısını kopyalayın.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databoxheavy`

 - Dosyaları kopyalayın. 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databoxheavy/{}`

     Burada j paralelleştirme sayısını, X ise paralel kopya sayısını belirtir

     16 paralel kopyayla başlamanızı ve kullanılabilir kaynak durumuna göre iş parçacığı sayısını artırmanızı öneririz.

> [!IMPORTANT]
> Aşağıdaki Linux dosya türlerinde desteklenmez: sembolik bağlantılar, karakter dosyası, blok dosyaları, yuva ve kanallar. Bu dosya türlerini hatalarda sonuçlanır **göndermeye hazırlama** adım.

Kopyalanan dosyaları görüntülemek ve doğrulamak için hedef klasörü açın. Kopyalama işlemi sırasında hatayla karşılaşırsanız sorun giderme için hata dosyalarını indirin. Daha fazla bilgi için [veri kutusu yoğun veri kopyalama sırasında hata günlüklerini görüntülemek](data-box-logs.md#view-error-log-during-data-copy). Veri kopyalama sırasında hatalar ayrıntılı bir listesi için bkz. [veri kutusu ağır sorun giderme sorunları](data-box-troubleshoot.md).

Veri bütünlüğünü sağlamak için sağlama toplamı veri kopyalama sırasında satır içinde hesaplanır. Kopyalama tamamlandıktan sonra cihazınızdaki kullanılan alanı ve boş alanı doğrulayın.
    
   ![Panoda boş ve kullanılan alanı doğrulama](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure veri kutusu ağır konuları hakkında gibi öğrendiniz:

> [!div class="checklist"]
> * Önkoşullar
> * Veri kutusu ağır için Bağlan
> * Veri kutusu ağır için veri kopyalama


Data Box'ınızı Microsoft'a göndermeye hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Microsoft, Azure veri kutusu ağır gönderin](./data-box-heavy-deploy-picked-up.md)

