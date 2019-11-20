---
title: Öğretici için vhd'lerden veri kopyalamak için yönetilen diskler ile Azure veri kutusu ağır | Microsoft Docs
description: Veri Vhd'lerden şirket içi VM iş yüklerini, Azure veri kutusu ağır için kopyalama hakkında bilgi edinin
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: a29cd142b3322c958f70aad8d5cad2bc30b87d76
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670833"
---
# <a name="tutorial-use-data-box-heavy-to-import-data-as-managed-disks-in-azure"></a>Öğretici: Verileri içeri aktarmak için kullanım veri kutusu ağır yönetilen diskler azure'da

Bu öğreticide, Azure veri kutusu ağır şirket içi VHD'ler azure'da yönetilen disklere geçirmek için nasıl kullanılacağı açıklanır. Şirket içi Vm'leri Vhd'lerden sayfa blobları veri kutusu ağır kopyalanır ve Azure yönetilen diskler olarak yüklenir. Bu yönetilen diskler can sonra Azure Vm'lerine bağlı olmalıdır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Önkoşulları gözden geçirin
> * Veri kutusu ağır için Bağlan
> * Veri kutusu ağır için veri kopyalama


## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce aşağıdakilerden emin olun:

1. Tamamladınız [Öğreticisi: Azure veri kutusu ağır kümesi](data-box-heavy-deploy-set-up.md).
2. Aldığınız, veri kutusu yoğun ve sipariş durumu Portalı'nda **teslim edildi**.
3. Yüksek hızlı bir ağa bağlı olursunuz. Hızlı kopya hızları için iki 40 GbE bağlantılar (düğüm başına bir adet) paralel olarak yararlanılabilir. 40 GbE bağlantı kullanılabilir değilse, en az iki 10 GbE bağlantılar (düğüm başına bir tane) sahip olmasını öneririz. 
4. Gözden geçirdiğimize göre:

    - Desteklenen [yönetilen disk boyutları Azure nesne boyutu sınırları](data-box-heavy-limits.md#azure-object-size-limits).
    - [Azure tanıtımını yönetilen diskler](/azure/virtual-machines/windows/managed-disks-overview). 

## <a name="connect-to-data-box-heavy"></a>Veri kutusu ağır için Bağlan

Belirtilen kaynak gruplarında bağlı olarak, veri kutusu ağır bir paylaşımı için düğüm başına her bir ilişkili kaynak grubu oluşturur. Örneğin, varsa `mydbmdrg1` ve `mydbmdrg2` olduğunda oluşturulan sipariş verme, aşağıdaki paylaşımları oluşturulur:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

İçinde her paylaşımı, depolama hesabınızdaki kapsayıcılar, karşılık gelen aşağıdaki üç klasör oluşturulur.

- Premium SSD
- Standart HDD
- Standart SSD

Aşağıdaki tabloda, UNC yollarını paylaşımları, veri kutusu yoğun gösterilmektedir.
 
|        Bağlantı protokolü           |             Paylaşımının UNC yolu                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

Olup, SMB veya NFS veri kutusu ağır paylaşımlarına bağlanmak için kullandığınıza bağlı olarak, bağlanma adımları farklıdır.

> [!NOTE]
> - REST bağlanmak için bu özellik desteklenmiyor.
> - Veri kutusu ağır'ın ikinci düğüme bağlanmak connect yönergelerini yineleyin.

### <a name="connect-to-data-box-heavy-via-smb"></a>Veri kutusu ağır SMB üzerinden bağlanma

Bir Windows Server ana bilgisayar kullanıyorsanız, veri kutusu ağır için bağlanmak için aşağıdaki adımları izleyin.

1. İlk adım kimlik doğrulamasından geçmek ve oturum başlatmaktır. **Bağlan ve kopyala**'ya gidin. Tıklayın **kimlik bilgilerini alma** kaynak grubunuz ile ilişkili paylaşımlar için erişim kimlik bilgileri alınamıyor. Erişim kimlik bilgilerini de alabilirsiniz **cihaz ayrıntıları** Azure portalında.

    > [!NOTE]
    > Yönetilen disklere yönelik tüm paylaşımlar için kimlik bilgilerini aynıdır.

    ![Paylaşım kimlik bilgilerini alma 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. Erişim paylaşımı ve kopyalama verileri iletişim kutusu, kopyalama **kullanıcıadı** ve **parola** paylaşım için. **Tamam**'ı tıklatın.
    
    ![Paylaşım kimlik bilgilerini alma 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Kaynağınız ile ilişkili paylaşımlara erişmek için (*mydbmdrg1* aşağıdaki örnekte), ana bilgisayardan bir komut penceresi açın. Komut istemine şunları yazın:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Bu örnekte, UNC paylaşımı yolları aşağıdaki gibidir:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. İstendiğinde paylaşımın parolasını girin. Aşağıdaki örnekte yukarıdaki komutla paylaşıma bağlanma adımları gösterilmektedir.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Windows + R tuşlarına basın. **Çalıştır** penceresinde `\\<device IP address>\<ShareName>` değerini belirtin. Tıklayın **Tamam** dosya Gezgini'ni açın.
    
    ![Paylaşıma Dosya Gezgini ile bağlanma 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    Artık aşağıdaki klasörleri her paylaşımı içinde precreated görmeniz gerekir.
    
    ![Paylaşıma Dosya Gezgini ile bağlanma 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-heavy-via-nfs"></a>Veri kutusu ağır NFS aracılığıyla bağlanma

Bir Linux ana bilgisayar kullanıyorsanız, NFS istemcilerinin erişmesine izin vermek için Cihazınızı yapılandırmak için aşağıdaki adımları gerçekleştirin.

1. Paylaşıma erişmesine izin verilen istemcilerin IP adreslerini sağlayın. Yerel web arabiriminde **Bağlan ve kopyala** sayfasına gidin. **NFS ayarları** bölümünde **NFS istemci erişimi**'ne tıklayın.

    ![NFS istemci erişimini yapılandırma 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. NFS istemcisinin IP adresini girin ve **Ekle**'ye tıklayın. Bu adımı tekrarlayarak birden fazla NFS istemcisi için erişim sağlayabilirsiniz. **Tamam**'ı tıklatın.

    ![NFS istemci erişimini yapılandırma 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Linux ana bilgisayarında NFS istemcisinin [desteklenen sürümünün](data-box-system-requirements.md) yüklü olduğundan emin olun. Linux dağıtımınıza uygun sürümü kullanın.

3. NFS İstemcisi yüklendikten sonra bir NFS paylaşımına Cihazınızda bağlamak için aşağıdaki komutu kullanın:

    `sudo mount <Data Box or Data Box Heavy IP>:/<NFS share on Data Box or Data Box Heavy device> <Path to the folder on local Linux computer>`

    Aşağıdaki örnek, bir Data Box veya veri kutusu ağır paylaşımına NFS bağlanmak gösterilmektedir. IP Data Box veya veri kutusu ağır cihaz `169.254.250.200`, paylaşım `mydbmdrg1_MDisk` olduğundan üzerinde ubuntuVM bağlanmış, bağlama noktası olan `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box-heavy"></a>Veri kutusu ağır için veri kopyalama

Veri sunucusuna bağlandıktan sonra sonraki adıma veri kopyalamaktır. VHD dosyası, sayfa blobu olarak hazırlama depolama hesabına kopyalanır. Sayfa blob'u ardından bir yönetilen diskin dönüştürülür ve bir kaynak grubuna taşındı.

Veri kopyalamaya başlamadan önce aşağıdaki konuları gözden geçirin:

- Her zaman VHD'lerin precreated klasörlerden birine kopyalayın. VHD'lerin dışında bu klasörleri veya oluşturduğunuz bir klasöre kopyalamak, VHD'ler sayfa blobları Azure depolama hesabına yüklenir ve yönetilen diskler değil.
- Yönetilen disk oluşturmak için yalnızca sabit VHD'lerin karşıya yüklenebilir. VHDX dosyası veya dinamik ve fark kayıt VHD'leri desteklenmez.
- Yalnızca bir kaynak grubunda belirli bir ada sahip bir yönetilen disk precreated klasörler arasında olabilir. Başka bir gelir precreated klasörlere karşıya VHD'ler benzersiz adlara sahip olmalıdır. Belirtilen ada, bir kaynak grubunda zaten mevcut bir yönetilen disk eşleşmiyor emin olun.
- Yönetilen disk sınırları gözden [Azure nesne boyutu sınırları](data-box-heavy-limits.md#azure-object-size-limits).

Olup, SMB veya NFS bağlanan bağlı olarak, şunları kullanabilirsiniz:

- [SMB üzerinden veri kopyalama](data-box-heavy-deploy-copy-data.md#copy-data-to-data-box-heavy)
- [NFS aracılığıyla veri kopyalama](data-box-heavy-deploy-copy-data-via-nfs.md#copy-data-to-data-box-heavy)

Son kopyalama işlerinin tamamlanmasını bekleyin. Sonraki adıma geçmeden önce hatasız kopyası işleri tamamlandığından emin emin olun.

![Herhangi bir hata ** Bağlan ve Kopyala ** sayfası](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Kopyalama işlemi sırasında bir hata varsa, günlükleri indirmek **Bağlan ve Kopyala** sayfası.

- Hizalı değil 512 bayt olan dosya kopyaladıysanız, dosya hazırlama depolama hesabınıza sayfa blobu olarak karşıya değil. Günlüklerindeki bir hata görürsünüz. Dosyayı kaldırın ve 512 bayt hizalı bir dosyaya kopyalayın.

- Uzun bir ada sahip bir VHDX (Bu dosyaları desteklenmez) kopyaladıysanız, günlüklerindeki bir hata görürsünüz.

    ![Hata günlüklerinden ** Bağlan ve Kopyala ** sayfası](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Sonraki adıma devam etmeden önce hataları giderin.

Veri bütünlüğünü sağlamak için sağlama toplamı veri kopyalama sırasında satır içinde hesaplanır. Kopyalama tamamlandıktan sonra cihazınızdaki kullanılan alanı ve boş alanı doğrulayın.
    
![Panoda boş ve kullanılan alanı doğrulama](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

Kopyalama işi tamamlandığında gidebilirsiniz **göndermeye hazırlama**.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure veri kutusu ağır konuları hakkında gibi öğrendiniz:

> [!div class="checklist"]
> * Önkoşulları gözden geçirin
> * Veri kutusu ağır için Bağlan
> * Veri kutusu ağır için veri kopyalama


Veri kutusu ağır Microsoft'a göndermeye hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Microsoft, Azure veri kutusu ağır gönderin](./data-box-heavy-deploy-picked-up.md)

