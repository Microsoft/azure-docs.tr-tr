---
title: "Öğretici: VHD 'lerden yönetilen disklere kopyalama"
titleSuffix: Azure Data Box Heavy
description: VHD 'lerden şirket içi VM iş yüklerinden verileri Azure Data Box Heavy kopyalama hakkında bilgi edinin
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 335b564225bc2b7a4c2217c1d912f952239ecf24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92127208"
---
# <a name="tutorial-use-data-box-heavy-to-import-data-as-managed-disks-in-azure"></a>Öğretici: Azure 'da verileri yönetilen diskler olarak içeri aktarmak için Data Box Heavy kullanma

Bu öğreticide, şirket içi VHD 'leri Azure 'da yönetilen disklere geçirmek için Azure Data Box Heavy nasıl kullanılacağı açıklanmaktadır. Şirket içi VM 'lerden VHD 'Ler, sayfa Blobları olarak Data Box Heavy kopyalanır ve yönetilen diskler olarak Azure 'a yüklenir. Bu yönetilen diskler daha sonra Azure VM 'lerine iliştirilebilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Önkoşulları inceleyin
> * Data Box Heavy’ye bağlanma
> * Data Box Heavy’ye veri kopyalama


## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce aşağıdakilerden emin olun:

1. [Öğretici: Azure Data Box Heavy’yi ayarlama](data-box-heavy-deploy-set-up.md) bölümünü tamamladınız.
2. Data Box Heavy’yi teslim aldınız ve portaldaki sipariş durumu **Teslim Edildi** oldu.
3. Yüksek hızlı bir ağa bağlanırsınız. En yüksek kopyalama hızları için iki adet 40 GbE bağlantı (düğüm başına bir tane) birbirine paralel olarak kullanılabilir. 40 GbE bağlantınız yoksa, en az iki tane (düğüm başına bir tane) 10 GbE bağlantınızın olması önerilir. 
4. Şunu gözden geçirdiniz:

    - [Azure nesne boyutu sınırları 'nda desteklenen yönetilen disk boyutları](data-box-heavy-limits.md#azure-object-size-limits).
    - [Azure yönetilen disklere giriş](../virtual-machines/managed-disks-overview.md). 

## <a name="connect-to-data-box-heavy"></a>Data Box Heavy’ye bağlanma

Data Box Heavy, belirtilen kaynak gruplarına göre, düğüm başına her bir ilişkili kaynak grubu için bir paylaşma oluşturur. Örneğin, `mydbmdrg1` ve `mydbmdrg2` sipariş yerleştirilirken oluşturulduysa, aşağıdaki paylaşımlar oluşturulur:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

Her paylaşımda, Depolama hesabınızdaki kapsayıcılara karşılık gelen aşağıdaki üç klasör oluşturulur.

- Premium SSD
- Standart HDD
- Standart SSD

Aşağıdaki tabloda Data Box Heavy paylaşımların UNC yolları gösterilmektedir.
 
|        Bağlantı Protokolü           |             Paylaşımın UNC yolu                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

Data Box Heavy paylaşımlarına bağlanmak için SMB veya NFS kullanıp kullanmayacağınızı temel alarak, bağlanma adımları farklıdır.

> [!NOTE]
> - REST aracılığıyla bağlanma bu özellik için desteklenmiyor.
> - Data Box Heavy ikinci düğümüne bağlanmak için bağlanma yönergelerini tekrarlayın.

### <a name="connect-to-data-box-heavy-via-smb"></a>SMB üzerinden Data Box Heavy bağlanma

Windows Server ana bilgisayarı kullanıyorsanız Data Box Heavy'ye bağlanmak için aşağıdaki adımları izleyin.

1. İlk adım kimlik doğrulamasından geçmek ve oturum başlatmaktır. **Bağlan ve kopyala**'ya gidin. Kaynak grubuyla ilişkili paylaşımların erişim kimlik bilgilerini almak için **kimlik bilgilerini al** ' a tıklayın. Azure portal **cihaz ayrıntılarından** erişim kimlik bilgilerini de alabilirsiniz.

    > [!NOTE]
    > Yönetilen disklerin tüm paylaşımlarının kimlik bilgileri aynıdır.

    ![Paylaşım kimlik bilgilerini alma](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. Erişim paylaşma ve verileri kopyalama iletişim kutusunda, paylaşımın **Kullanıcı adını** ve **parolasını** kopyalayın. **Tamam**'a tıklayın.
    
    ![Paylaşım kimlik bilgilerini alma 2](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Ana bilgisayarınızdaki kaynakla ilişkili paylaşımlara (aşağıdaki örnekte *mydbmdrg1* ) erişmek için bir komut penceresi açın. Komut istemine şunları yazın:

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

4. Windows + R tuşlarına basın. **Çalıştır** penceresinde `\\<device IP address>\<ShareName>` değerini belirtin. Dosya Gezgini'ni açmak için **Tamam**’a tıklayın.
    
    ![Dosya Gezgini aracılığıyla paylaşıma bağlanma](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    Artık her bir paylaşımda aşağıdaki daha önceden düzenlenen klasörleri görmeniz gerekir.
    
    ![Paylaşıma Dosya Gezgini ile bağlanma 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-heavy-via-nfs"></a>NFS aracılığıyla Data Box Heavy bağlanma

Bir Linux ana bilgisayar kullanıyorsanız, cihazınızı NFS istemcilerine erişime izin verecek şekilde yapılandırmak için aşağıdaki adımları gerçekleştirin.

1. Paylaşıma erişmesine izin verilen istemcilerin IP adreslerini sağlayın. Yerel web arabiriminde **Bağlan ve kopyala** sayfasına gidin. **NFS ayarları** bölümünde **NFS istemci erişimi**'ne tıklayın.

    ![NFS istemci erişimini yapılandırma](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. NFS istemcisinin IP adresini girin ve **Ekle**'ye tıklayın. Bu adımı tekrarlayarak birden fazla NFS istemcisi için erişim sağlayabilirsiniz. **Tamam**'a tıklayın.

    ![NFS istemci erişimini yapılandırma 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Linux ana bilgisayarında NFS istemcisinin [desteklenen sürümünün](data-box-system-requirements.md) yüklü olduğundan emin olun. Linux dağıtımınıza uygun sürümü kullanın.

3. NFS istemcisi yüklendikten sonra, cihazınıza NFS paylaşımından bağlamak için aşağıdaki komutu kullanın:

    `sudo mount <Data Box or Data Box Heavy IP>:/<NFS share on Data Box or Data Box Heavy device> <Path to the folder on local Linux computer>`

    Aşağıdaki örnek, NFS aracılığıyla bir Data Box veya Data Box Heavy paylaşıma nasıl bağlanılacağını gösterir. Data Box veya Data Box Heavy cihaz IP 'si `169.254.250.200` , paylaşımın `mydbmdrg1_MDisk` ubuntuVM 'ye, bağlama noktasına bağlanır `/home/databoxubuntuhost/databox` .

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box-heavy"></a>Data Box Heavy’ye veri kopyalama

Veri sunucusuna bağlandıktan sonra, bir sonraki adım verileri kopyalayacağız. VHD dosyası, hazırlama depolama hesabına Sayfa Blobu olarak kopyalanır. Sayfa Blobu daha sonra yönetilen bir diske dönüştürülüp bir kaynak grubuna taşınır.

Veri kopyalamaya başlamadan önce aşağıdaki konuları gözden geçirin:

- VHD'leri her zaman önceden oluşturulmuş klasörlerden birine kopyalayın. VHD 'leri bu klasörlerin dışına veya oluşturduğunuz bir klasöre kopyalarsanız, VHD 'ler, yönetilen diskler değil, Azure depolama hesabına sayfa Blobları olarak yüklenir.
- Yönetilen diskler oluşturmak için yalnızca sabit VHD'ler karşıya yüklenebilir. VHDX dosyaları veya dinamik ve fark kayıt vhd 'leri desteklenmez.
- Yalnızca bir kaynak grubunda belirli bir ada sahip bir yönetilen diske, önceden düzenlenen tüm klasörler arasında sahip olabilirsiniz. Bu durum, önceden oluşturulan klasörlere yüklenen VHD'lerin benzersiz adlara sahip olması gerektiği anlamına gelir. Verilen adın bir kaynak grubunda zaten var olan bir yönetilen diskle eşleşmediğinden emin olun.
- [Azure nesne boyut sınırları](data-box-heavy-limits.md#azure-object-size-limits)'ndaki yönetilen disk sınırlarını gözden geçirin.

SMB veya NFS aracılığıyla bağlanıp bağlandığınıza bağlı olarak şunları kullanabilirsiniz:

- [SMB ile veri kopyalama](data-box-heavy-deploy-copy-data.md#copy-data-to-data-box-heavy)
- [NFS ile veri kopyalama](data-box-heavy-deploy-copy-data-via-nfs.md#copy-data-to-data-box-heavy)

Kopyalama işlerinin bitmesini bekleyin. Sonraki adıma geçmeden önce kopyalama işlerinin hatasız bitdiğinizden emin olun.

![* * Bağlan ve Kopyala * * sayfasında hata yok](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Kopyalama işlemi sırasında hatalar varsa, **Bağlan ve Kopyala** sayfasından günlükleri indirin.

- 512 bayt hizalı bir dosyayı kopyaladıysanız, dosya, hazırlama depolama hesabınıza Sayfa Blobu olarak yüklenmeyecektir. Günlüklerde bir hata görürsünüz. Dosyayı kaldırın ve 512 bayt hizalı bir dosyayı kopyalayın.

- Bir VHDX kopyaladıysanız (Bu dosyalar desteklenmez), uzun bir adla, günlüklerde bir hata görürsünüz.

    ![Günlüklere * * Bağlan ve Kopyala * * sayfasından hata](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Sonraki adıma geçmeden önce hataları çözün.

Veri bütünlüğünü sağlamak için sağlama toplamı veri kopyalama sırasında satır içinde hesaplanır. Kopyalama tamamlandıktan sonra cihazınızdaki kullanılan alanı ve boş alanı doğrulayın.
    
![Panoda boş ve kullanılan alanı doğrulama](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

Kopyalama işi tamamlandıktan sonra **göndermeye hazırlama**' a gidebilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide Azure Data Box Heavy ile ilgili aşağıdaki bilgileri öğrendiniz:

> [!div class="checklist"]
> * Önkoşulları inceleyin
> * Data Box Heavy’ye bağlanma
> * Data Box Heavy’ye veri kopyalama


Data Box Heavy'yi Microsoft’a geri gönderme hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Data Box Heavy’yi Microsoft'a gönderme](./data-box-heavy-deploy-picked-up.md)