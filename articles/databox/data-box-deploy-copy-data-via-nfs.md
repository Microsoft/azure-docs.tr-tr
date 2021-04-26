---
title: NFS aracılığıyla Azure Data Box veri kopyalama öğreticisi | Microsoft Docs
description: Bu öğreticide, yerel Web Kullanıcı arabirimi ile NFS kullanarak ana bilgisayar bilgisayarınızdan Azure Data Box 'e bağlanmayı ve verileri kopyalamayı öğrenin.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: alkohli
ms.openlocfilehash: d53a619dc6ca5fb0f43f6097664f50bf22943928
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97678893"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-nfs"></a>Öğretici: NFS aracılığıyla Azure Data Box verileri kopyalama

Bu öğreticide yerel web arabirimini kullanarak bağlantı kurma, ana bilgisayarınızdan veri kopyalama işlemi anlatılmaktadır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Ön koşullar
> * Data Box'a bağlanma
> * Data Box'a veri kopyalama

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce aşağıdakilerden emin olun:

1. [Öğretici: Azure Data Box'ı kurma](data-box-deploy-set-up.md) konusunu tamamladınız.
2. Data Box’ınızı teslim aldınız ve portaldaki sipariş durumu **Teslim Edildi** oldu.
3. Data Box üzerinden kopyalamak istediğiniz verileri içeren bir ana bilgisayarınız var. Ana bilgisayarınız:
    - [Desteklenen bir işletim sistemi](data-box-system-requirements.md) çalıştırılmalıdır.
    - Yüksek hızlı bir ağa bağlı olmalıdır. En az bir adet 10 GbE bağlantınızın olması önemle tavsiye edilir. 10-GbE bağlantı kullanılamıyorsa, 1-GbE veri bağlantısı kullanılabilir ancak kopyalama hızları etkilenecektir. 

## <a name="connect-to-data-box"></a>Data Box'a bağlanma

Seçilen depolama hesabına bağlı olarak, Data Box şunların tamamını veya bir bölümünü oluşturabilir:
- İlişkili her depolama hesabına GPv1 ve GPv2 için üç paylaşım.
- Premium depolama için bir paylaşım. 
- Blob depolama hesabı için bir paylaşım. 

Blok blobu ve sayfa blobu paylaşımlarının altında birinci düzeydeki varlıklar kapsayıcılar, ikinci düzeydeki varlıklar ise bloblardır. Azure Dosyaları paylaşımlarında birinci düzeydeki varlıklar paylaşımlar, ikinci düzeydeki varlıklar ise dosyalardır.

Aşağıdaki tabloda, Data Box üzerindeki paylaşımların UNC yolu ve verilerin karşıya yüklendiği Azure Depolama yolu URL'si gösterilmektedir. Son Azure depolama yolu URL'si, UNC paylaşım yolundan türetilebilir.
 
| Azure depolama türü| Data Box paylaşımları                                       |
|-------------------|--------------------------------------------------------------------------------|
| Azure Blok blobları | <li>Paylaşımların UNC yolu: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure Depolama URL’si: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure Sayfa blobları  | <li>Paylaşımların UNC yolu: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure Depolama URL’si: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Dosyaları       |<li>Paylaşımların UNC yolu: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure Depolama URL’si: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Linux ana bilgisayarı kullanıyorsanız aşağıdaki adımları gerçekleştirerek Data Box'ı NFS istemcilerine izin verecek şekilde yapılandırın.

1. Paylaşıma erişmesine izin verilen istemcilerin IP adreslerini sağlayın. Yerel web arabiriminde **Bağlan ve kopyala** sayfasına gidin. **NFS ayarları** bölümünde **NFS istemci erişimi**'ne tıklayın. 

    ![NFS istemci erişimini yapılandırma](media/data-box-deploy-copy-data/nfs-client-access-1.png)

2. NFS istemcisinin IP adresini girin ve **Ekle**'ye tıklayın. Bu adımı tekrarlayarak birden fazla NFS istemcisi için erişim sağlayabilirsiniz. **Tamam**'a tıklayın.

    ![NFS istemcisinin IP adresini yapılandırma](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Linux ana bilgisayarında NFS istemcisinin [desteklenen sürümünün](data-box-system-requirements.md) yüklü olduğundan emin olun. Linux dağıtımınıza uygun sürümü kullanın. 

3. NFS istemcisi yüklendikten sonra, Data Box cihazınızdaki NFS paylaşımını bağlamak için aşağıdaki komutu kullanın:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    Aşağıdaki örnekte, Data Box paylaşımına NFS yoluyla nasıl bağlanılacağı gösterilir. Data Box cihaz IP'si `10.161.23.130` şeklindedir, `Mystoracct_Blob` ubuntuVM'ye bağlanmıştır ve bağlama noktası `/home/databoxubuntuhost/databox` ile belirtilmiştir.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    Mac istemcileri için aşağıdaki gibi ek bir seçenek eklemeniz gerekir: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **Her zaman kopyalamayı düşündüğünüz dosyalar için paylaşımda bir klasör oluşturun ve ardından dosyaları bu klasöre kopyalayın**. Blok blobu ve sayfa blobu paylaşımları altında oluşturulan klasör, verilerin blob olarak karşıya yüklendiği kapsayıcıyı temsil eder. Dosyaları depolama hesabındaki *root* klasörüne doğrudan kopyalayamazsınız.

## <a name="copy-data-to-data-box"></a>Data Box'a veri kopyalama

Data Box paylaşımlarına bağlandıktan sonra veri kopyalamaya başlayabilirsiniz. Verileri kopyalamaya başlamadan önce aşağıdaki konuları gözden geçirin:

* Verilerin uygun dosya biçimine karşılık gelen paylaşımlara kopyalandığından emin olun. Örneğin blok blobu verilerinin blok blobu paylaşımına kopyalanması gerekir. VHD 'leri sayfa bloblarına kopyalayın. Veri biçimi uygun paylaşım türüyle eşleşmiyorsa verilerin Azure'a yüklenmesi başarısız olur.
*  Verileri kopyalarken, veri boyutunun [Azure depolama hesabı boyut sınırları](data-box-limits.md#azure-storage-account-size-limits)bölümünde açıklanan boyut sınırlarına uyduğundan emin olun.
* Data Box tarafından yüklenen verilerin Data Box haricinde başka bir uygulama tarafından da yüklenmesi durumunda yükleme işinde hata oluşabilir ve veri bozulması yaşanabilir.
* Aynı anda hem SMB hem de NFS kullanmamanızı veya aynı verileri Azure'daki aynı uç hedefe kopyalamamanızı öneririz. Bu gibi durumlarda nihai sonucu kestirmek mümkün olmayabilir.
* **Her zaman kopyalamayı düşündüğünüz dosyalar için paylaşımda bir klasör oluşturun ve ardından dosyaları bu klasöre kopyalayın**. Blok blobu ve sayfa blobu paylaşımları altında oluşturulan klasör, verilerin blob olarak karşıya yüklendiği kapsayıcıyı temsil eder. Dosyaları depolama hesabındaki *root* klasörüne doğrudan kopyalayamazsınız.
* Büyük/küçük harfe duyarlı dizin ve dosya adlarını NFS paylaşımından Data Box:
  * Durum, ad içinde korunur.
  * Dosyalar büyük/küçük harfe duyarlıdır.

    Örneğin, ve kopyalama yaptıysanız `SampleFile.txt` `Samplefile.Txt` , bu durum Data Box kopyalanırken saklanır, ancak ikinci dosya aynı dosya olarak kabul edildiği sürece ikinci dosya birincisinin üzerine yazılır.

> [!IMPORTANT]
> Data Box'ın verilerinizi Azure Depolama'ya aktardığını onaylayana kadar kaynak verilerinizin bir kopyasına sahip olduğunuzdan emin olun.

Linux ana bilgisayar kullanıyorsanız Robocopy ile benzer bir kopyalama yardımcı programı kullanabilirsiniz. Linux 'ta bulunan alternatiflere bazıları [`rsync`](https://rsync.samba.org/) , [FreeFileSync](https://www.freefilesync.org/), [uyum](https://www.cis.upenn.edu/~bcpierce/unison/)veya [Ultracopier](https://ultracopier.first-world.info/).  

`cp` komutu, dizin kopyalamak için en iyi seçeneklerden biridir. Kullanımı hakkında daha fazla bilgi için [cp man sayfalarına](http://man7.org/linux/man-pages/man1/cp.1.html) gidin.

`rsync`Çoklu iş parçacıklı bir kopya için kullanıyorsanız, aşağıdaki yönergeleri izleyin:

* Linux istemcinizde kullanılan dosya sistemine bağlı olarak **CIFS Utils** veya **NFS Utils** paketini yükleyebilirsiniz.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

* Install `rsync` ve **Parallel** (Linux dağıtılmış sürümüne bağlı olarak değişir).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

* Bağlama noktası oluşturun.

    `sudo mkdir /mnt/databox`

* Birimi bağlayın.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

* Klasör dizin yapısını kopyalayın.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

* Dosyaları kopyalayın.

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     Burada j paralelleştirme sayısını, X ise paralel kopya sayısını belirtir

     16 paralel kopyayla başlamanızı ve kullanılabilir kaynak durumuna göre iş parçacığı sayısını artırmanızı öneririz.

> [!IMPORTANT]
> Şu Linux dosya türleri desteklenmez: sembolik bağlantılar, karakter dosyaları, blok dosyaları, yuvalar ve kanallar. Bu dosya türleri **göndermeye hazırlama** adımı sırasında hatalara neden olur.

Kopyalama işlemi sırasında hatalarla karşılaşırsanız bir bildirim görürsünüz.

![Bağlan ve kopyala adımındaki hataları indirip görüntüleme](media/data-box-deploy-copy-data/view-errors-1.png)

**Sorun listesini indir**’i seçin.

![Kopyalama hatası için sorunlar listesini indirin](media/data-box-deploy-copy-data/view-errors-2.png)

Hatanın ayrıntılarını görüntülemek için listeyi açın ve önerilen çözümü görüntülemek için çözüm URL’sini seçin.

![Bir kopyalama hata sorunları listesindeki sorunlar](media/data-box-deploy-copy-data/view-errors-3.png)

Daha fazla bilgi için bkz. [Data Box’a veri kopyalama sırasında hata günlüklerini görüntüleme](data-box-logs.md#view-error-log-during-data-copy). Veri kopyalama sırasında karşılaşılan hataların ayrıntılı bir listesi için bkz. [Data Box sorunlarını giderme](data-box-troubleshoot.md).

Veri bütünlüğünü sağlamak için sağlama toplamı veri kopyalama sırasında satır içinde hesaplanır. Kopyalama tamamlandıktan sonra cihazınızdaki kullanılan alanı ve boş alanı doğrulayın.

   ![Panoda boş ve kullanılan alanı doğrulama](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide aşağıdaki Azure Data Box konularını öğrendiniz:

> [!div class="checklist"]
>
> * Ön koşullar
> * Data Box'a bağlanma
> * Data Box'a veri kopyalama

Data Box'ı Microsoft’a geri gönderme hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Data Box verilerinizi Microsoft'a gönderme](./data-box-deploy-picked-up.md)
