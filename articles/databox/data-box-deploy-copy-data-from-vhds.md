---
title: Azure Data Box, VHD 'lerden yönetilen disklere veri kopyalama öğreticisi | Microsoft Docs
description: VHD 'lerden şirket içi VM iş yüklerinden verileri Azure Data Box kopyalama hakkında bilgi edinin
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.openlocfilehash: 4b7182d1fa70a146da1c01273ffe1032f2982546
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240454"
---
# <a name="tutorial-use-data-box-to-import-data-as-managed-disks-in-azure"></a>Öğretici: Azure 'da verileri yönetilen diskler olarak içeri aktarmak için Data Box kullanma

Bu öğreticide, şirket içi VHD 'leri Azure 'da yönetilen disklere geçirmek için Azure Data Box nasıl kullanılacağı açıklanmaktadır. Şirket içi VM 'lerden VHD 'Ler, sayfa Blobları olarak Data Box kopyalanır ve yönetilen diskler olarak Azure 'a yüklenir. Bu yönetilen diskler daha sonra Azure VM 'lerine iliştirilebilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Önkoşulları gözden geçirin
> * Data Box'a bağlanma
> * Data Box'a veri kopyalama


## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce aşağıdakilerden emin olun:

1. [Öğreticiyi tamamladınız: Azure Data Box](data-box-deploy-set-up.md)ayarlayın.
2. Data Box aldınız ve portaldaki sipariş durumu **teslim edildi**.
3. Yüksek hızlı bir ağa bağlanırsınız. En az bir adet 10 GbE bağlantınızın olması önemle tavsiye edilir. 10-GbE bağlantı kullanılamıyorsa, 1-GbE veri bağlantısı kullanın, ancak kopyalama hızları bundan etkilenir.
4. Şunu gözden geçirdiniz:

    - [Azure nesne boyutu sınırları 'nda desteklenen yönetilen disk boyutları](data-box-limits.md#azure-object-size-limits).
    - [Azure yönetilen disklere giriş](/azure/virtual-machines/windows/managed-disks-overview). 

## <a name="connect-to-data-box"></a>Data Box'a bağlanma

Data Box, belirtilen kaynak gruplarına bağlı olarak, her bir ilişkili kaynak grubu için bir paylaşma oluşturur. Örneğin, `mydbmdrg1` ve `mydbmdrg2` sipariş yerleştirilirken oluşturulduysa, aşağıdaki paylaşımlar oluşturulur:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

Her paylaşımda, Depolama hesabınızdaki kapsayıcılara karşılık gelen aşağıdaki üç klasör oluşturulur.

- Premium SSD
- Standart HDD
- Standart SSD

Aşağıdaki tabloda Data Box paylaşımların UNC yolları gösterilmektedir.
 
|        Bağlantı Protokolü           |             Paylaşımın UNC yolu                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<PremiumSSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardHDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardSSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<PremiumSSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardHDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardSSD>/file3.vhd` |

Data Box paylaşımlarına bağlanmak için SMB veya NFS kullanıp kullanmayacağınızı temel alarak, bağlanma adımları farklıdır.

> [!NOTE]
> REST aracılığıyla bağlanma bu özellik için desteklenmiyor.

### <a name="connect-to-data-box-via-smb"></a>SMB üzerinden Data Box bağlanma

Windows Server ana bilgisayar kullanıyorsanız, Data Box bağlanmak için aşağıdaki adımları izleyin.

1. İlk adım kimlik doğrulamasından geçmek ve oturum başlatmaktır. **Bağlan ve kopyala**'ya gidin. Kaynak grubuyla ilişkili paylaşımların erişim kimlik bilgilerini almak için **kimlik bilgilerini al** ' a tıklayın. Azure portal **cihaz ayrıntılarından** erişim kimlik bilgilerini de alabilirsiniz.

    > [!NOTE]
    > Yönetilen disklerin tüm paylaşımlarının kimlik bilgileri aynıdır.

    ![Paylaşım kimlik bilgilerini alma 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. Erişim paylaşma ve verileri kopyalama iletişim kutusunda, paylaşımın **Kullanıcı adını** ve **parolasını** kopyalayın. **Tamam**'ı tıklatın.
    
    ![Paylaşım kimlik bilgilerini alma 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Ana bilgisayarınızdaki kaynakla ilişkili paylaşımlara (aşağıdaki örnekte*mydbmdrg1* ) erişmek için bir komut penceresi açın. Komut istemine şunları yazın:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Bu örnekteki UNC paylaşma yollarınız aşağıdaki gibidir:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. İstendiğinde paylaşımın parolasını girin. Aşağıdaki örnekte yukarıdaki komutla paylaşıma bağlanma adımları gösterilmektedir.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Windows + R tuşlarına basın. **Çalıştır** penceresinde `\\<device IP address>\<ShareName>` değerini belirtin. Dosya Gezgini 'ni açmak için **Tamam** 'a tıklayın.
    
    ![Paylaşıma Dosya Gezgini ile bağlanma 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    Artık her bir paylaşımda aşağıdaki daha önceden düzenlenen klasörleri görmeniz gerekir.
    
    ![Paylaşıma Dosya Gezgini ile bağlanma 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-via-nfs"></a>NFS aracılığıyla Data Box bağlanma

Linux ana bilgisayarı kullanıyorsanız aşağıdaki adımları gerçekleştirerek Data Box'ı NFS istemcilerine izin verecek şekilde yapılandırın.

1. Paylaşıma erişmesine izin verilen istemcilerin IP adreslerini sağlayın. Yerel web arabiriminde **Bağlan ve kopyala** sayfasına gidin. **NFS ayarları** bölümünde **NFS istemci erişimi**'ne tıklayın.

    ![NFS istemci erişimini yapılandırma 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. NFS istemcisinin IP adresini girin ve **Ekle**'ye tıklayın. Bu adımı tekrarlayarak birden fazla NFS istemcisi için erişim sağlayabilirsiniz. **Tamam**'ı tıklatın.

    ![NFS istemci erişimini yapılandırma 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Linux ana bilgisayarında NFS istemcisinin [desteklenen sürümünün](data-box-system-requirements.md) yüklü olduğundan emin olun. Linux dağıtımınıza uygun sürümü kullanın.

3. NFS istemcisi yüklendikten sonra, Data Box cihazınızdaki NFS paylaşımını bağlamak için aşağıdaki komutu kullanın:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    Aşağıdaki örnekte, Data Box paylaşımına NFS yoluyla nasıl bağlanılacağı gösterilir. Data Box cihaz IP'si `169.254.250.200` şeklindedir, `mydbmdrg1_MDisk` ubuntuVM'ye bağlanmıştır ve bağlama noktası `/home/databoxubuntuhost/databox` ile belirtilmiştir.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>Data Box'a veri kopyalama

Veri sunucusuna bağlandıktan sonra, bir sonraki adım verileri kopyalayacağız. VHD dosyası, hazırlama depolama hesabına Sayfa Blobu olarak kopyalanır. Sayfa Blobu daha sonra yönetilen bir diske dönüştürülüp bir kaynak grubuna taşınır.

Veri kopyalamaya başlamadan önce aşağıdaki konuları gözden geçirin:

- VHD 'leri her zaman önceden düzenlenen klasörlerden birine kopyalayın. VHD 'leri bu klasörlerin dışına veya oluşturduğunuz bir klasöre kopyalarsanız, VHD 'ler, yönetilen diskler değil, Azure depolama hesabına sayfa Blobları olarak yüklenir.
- Yönetilen diskler oluşturmak için yalnızca sabit VHD 'ler karşıya yüklenebilir. VHDX dosyaları veya dinamik ve fark kayıt vhd 'leri desteklenmez.
- Yalnızca bir kaynak grubunda belirli bir ada sahip bir yönetilen diske, önceden düzenlenen tüm klasörler arasında sahip olabilirsiniz. Bu, önceden düzenlenen klasörlere yüklenen VHD 'Lerin benzersiz adlara sahip olması gerektiğini gösterir. Verilen adın bir kaynak grubunda zaten var olan bir yönetilen diskle eşleşmediğinden emin olun.
- [Azure nesne boyut sınırları](data-box-limits.md#azure-object-size-limits)'ndaki yönetilen disk sınırlarını gözden geçirin.

SMB veya NFS aracılığıyla bağlanıp bağlandığınıza bağlı olarak şunları kullanabilirsiniz:

- [SMB aracılığıyla veri kopyalama](data-box-deploy-copy-data.md#copy-data-to-data-box)
- [NFS aracılığıyla veri kopyalama](data-box-deploy-copy-data-via-nfs.md#copy-data-to-data-box)

Kopyalama işlerinin bitmesini bekleyin. Sonraki adıma geçmeden önce kopyalama işlerinin hatasız bitdiğinizden emin olun.

![\* * Bağlan ve Kopyala * * sayfasında hata yok](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Kopyalama işlemi sırasında hatalar varsa, **Bağlan ve Kopyala** sayfasından günlükleri indirin.

- 512 bayt hizalı bir dosyayı kopyaladıysanız, dosya, hazırlama depolama hesabınıza Sayfa Blobu olarak yüklenmeyecektir. Günlüklerde bir hata görürsünüz. Dosyayı kaldırın ve 512 bayt hizalı bir dosyayı kopyalayın.

- Bir VHDX kopyaladıysanız (Bu dosyalar desteklenmez), uzun bir adla, günlüklerde bir hata görürsünüz.

    ![Günlüklere * * Bağlan ve Kopyala * * sayfasından hata](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Sonraki adıma geçmeden önce hataları çözün.

Veri bütünlüğünü sağlamak için sağlama toplamı veri kopyalama sırasında satır içinde hesaplanır. Kopyalama tamamlandıktan sonra cihazınızdaki kullanılan alanı ve boş alanı doğrulayın.
    
![Panoda boş ve kullanılan alanı doğrulama](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

Kopyalama işi tamamlandıktan sonra **göndermeye hazırlama**' a gidebilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide aşağıdaki Azure Data Box konularını öğrendiniz:

> [!div class="checklist"]
> * Önkoşulları gözden geçirin
> * Data Box'a bağlanma
> * Data Box'a veri kopyalama


Data Box Microsoft 'a geri gönderme hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Azure Data Box verilerinizi Microsoft'a gönderme](./data-box-deploy-picked-up.md)

