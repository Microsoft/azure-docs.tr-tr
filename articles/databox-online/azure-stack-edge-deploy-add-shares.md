---
title: Azure Stack Edge Pro ile paylaşımlara veri aktarma öğreticisi | Microsoft Docs
description: Bu öğreticide, Azure Stack Edge Pro 'Nun verileri Azure 'a aktarabilmesi için Azure Stack Edge Pro cihazınızda paylaşımların nasıl ekleneceğini ve bu cihazlarda nasıl bağlanacağınızı öğrenin.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: alkohli
ms.openlocfilehash: 8c9ad00a8910562e1a3a53af5120030bc482c927
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060219"
---
# <a name="tutorial-transfer-data-with-azure-stack-edge-pro"></a>Öğretici: Azure Stack Edge Pro ile veri aktarma

Bu öğretici, Azure Stack Edge Pro cihazınızda paylaşımların nasıl ekleneceğini ve bu cihazlarda nasıl bağlanabileceğinizi açıklamaktadır. Paylaşımlar eklendikten sonra Azure Stack Edge Pro, verileri Azure 'a aktarabilir.

Bu yordamın tamamlanması 10 dakika kadar sürebilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Paylaşım ekleme
> * Paylaşıma bağlanma

 
## <a name="prerequisites"></a>Önkoşullar

Azure Stack Edge Pro 'ya paylaşımlar eklemeden önce şunları yaptığınızdan emin olun:

- Fiziksel cihazınızı [yükleme Azure Stack Edge Pro](azure-stack-edge-deploy-install.md)' da anlatıldığı şekilde yüklediniz.

- Fiziksel cihazı [Bağlan, ayarla ve Azure Stack Edge Pro 'yu etkinleştir](azure-stack-edge-deploy-connect-setup-activate.md)bölümünde açıklandığı gibi etkinleştirdiniz.


## <a name="add-a-share"></a>Paylaşım ekleme

Bir paylaşma oluşturmak için aşağıdaki yordamı uygulayın:

1. [Azure Portal](https://portal.azure.com/), Azure Stack Edge kaynağını seçin ve **genel bakış**' a gidin. Cihazınızın çevrimiçi olması gerekir. **Bulut depolama ağ geçidi**' ni seçin.

   ![Cihaz çevrimiçi](./media/azure-stack-edge-deploy-add-shares/device-online-1.png)

2. Cihaz komut çubuğunda **+ paylaşma Ekle** ' yi seçin.

   ![Paylaşım ekleme](./media/azure-stack-edge-deploy-add-shares/select-add-share-1.png)

3. **Paylaşma Ekle** bölmesinde aşağıdaki yordamı uygulayın:

    a. **Ad** kutusunda, paylaşımınız için benzersiz bir ad sağlayın.  
    Paylaşma adı yalnızca küçük harf, sayı ve kısa çizgi içerebilir. 3 ila 63 karakter arasında olmalıdır ve bir harf veya rakam ile başlamalıdır. Kısa çizgilerden önce ve ardından bir harf ya da rakam gelmelidir.
    
    b. Paylaşma için bir **tür** seçin.  
    Bu tür SMB veya **NFS**, SMB 'nin varsayılan olarak **olması olabilir.** SMB Windows istemcilerinin standardıdır ve NFS de Linux istemcilerinde kullanılır.  
    SMB veya NFS paylaşımlarını seçmenize bağlı olarak, seçeneklerin geri kalanı biraz farklılık gösterir. 

    c. Paylaşımın duracağı depolama hesabını sağlayın. 

    > [!IMPORTANT]
    > Kullandığınız Azure depolama hesabının, bir Azure Stack Edge Pro veya Data Box Gateway cihazından yararlanarak bu sunucuda ayarlanmış bir değişiklik olmadığından emin olun. Daha fazla bilgi için bkz. [BLOB depolama için dengesde kullanılabilirlik Ilkelerini ayarlama ve yönetme](../storage/blobs/storage-blob-immutability-policies-manage.md).
    
    d. **Depolama hizmeti** açılan listesinde, **Blok Blobu**, **Sayfa Blobu** veya **dosyalar**' ı seçin.  
    Seçtiğiniz hizmetin türü, verilerin Azure 'da kullanmasını istediğiniz biçime bağlıdır. Bu örnekte, verileri Azure 'da blok Blobları olarak depolamak istiyoruz, **Blok Blobu** seçtik. **Sayfa Blobu**' nı seçerseniz, verilerinizin 512 bayt hizalı olduğundan emin olun. Örneğin VHDX her zaman 512 bayt hizalıdır.

    e. Yeni bir blob kapsayıcısı oluşturun veya açılan listeden mevcut bir tane kullanın. Blob kapsayıcısı oluşturuyorsanız bir kapsayıcı adı sağlayın. Henüz kapsayıcı yoksa, yeni oluşturulan paylaşım adıyla depolama hesabında oluşturulur.
   
    f. Bir SMB veya NFS paylaşımının oluşturulup oluşturulmayacağını bağlı olarak, aşağıdaki adımlardan birini yapın: 
     
    - **SMB paylaşma**: **tüm ayrıcalık yerel kullanıcısı**' nın altında, **Yeni oluştur** ' u seçin veya **var olanı kullanın**. Yeni yerel kullanıcı oluşturuyorsanız kullanıcı adı ve parola girin, sonra da parolayı onaylayın. Bu eylem yerel kullanıcıya izinleri atar. Burada izinleri atadıktan sonra, Dosya Gezgini'ni kullanarak bunlarda değişiklik yapabilirsiniz.

        Bu paylaşma verileri için **yalnızca okuma Işlemlerine Izin ver** onay kutusunu seçerseniz, salt okunurdur kullanıcıları belirtebilirsiniz.

        ![SMB paylaşımı ekleme](./media/azure-stack-edge-deploy-add-shares/add-share-smb-1.png)
   
    - **NFS paylaşma**: paylaşıma erişebilen izin VERILEN istemcilerin IP adreslerini girin.

        ![NFS paylaşımı ekleme](./media/azure-stack-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. Paylaşma oluşturmak için **Oluştur** ' u seçin.
    
    Paylaşma oluşturma işleminin devam ettiğini size bildirilir. Paylaşım belirtilen ayarlarla oluşturulduktan sonra, **Paylaşımlar** kutucuğunu yeni paylaşımı yansıtacak şekilde güncelleştirir.
    

## <a name="connect-to-the-share"></a>Paylaşıma bağlanma

Artık son adımda oluşturduğunuz bir veya daha fazla paylaşıma bağlanabilirsiniz. SMB veya NFS paylaşımınız olmasına bağlı olarak, adımlar farklılık gösterebilir.

### <a name="connect-to-an-smb-share"></a>SMB paylaşımına bağlanma

Azure Stack Edge Pro cihazınıza bağlı Windows Server istemcisinde şu komutları girerek bir SMB paylaşımıyla bağlantı edin:


1. Komut penceresinde şunu yazın: 

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

2. İstendiğinde paylaşımın parolasını girin.  
   Burada, bu komutun örnek çıkışı gösterilir.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60':
    The command completed successfully.
    
    C: \Users\DataBoxEdgeUser>
    ```   


3. Klavyenizde Windows + R ' yi seçin.

4. **Çalıştır** penceresinde, `\\<device IP address>` öğesini belirtin ve ardından **Tamam**' ı seçin.  
   Dosya Gezgini açılır. Artık oluşturduğunuz paylaşımları klasörler olarak görüntüleyebiliyor olmalısınız. İçeriğini görüntülemek için Dosya Gezgini'nde paylaşıma (klasöre) çift tıklayın.
 
    ![SMB paylaşımına bağlanma](./media/azure-stack-edge-deploy-add-shares/connect-to-share2.png)

    Bu paylaşımlara oluşturulduğu anda veriler yazılır ve cihaz verileri buluta gönderir.

### <a name="connect-to-an-nfs-share"></a>NFS paylaşımına bağlanma

Azure Stack Edge Pro cihazınıza bağlı Linux istemciniz için aşağıdaki yordamı uygulayın:

1. İstemcide NFSv4 istemcisinin yüklü olduğundan emin olun. NFS istemcisini yüklemek için aşağıdaki komutu kullanın:

   `sudo apt-get install nfs-common`

    Daha fazla bilgi için [Install NFSv4 Client](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client)bölümüne gidin.

2. NFS istemcisi yüklendikten sonra, aşağıdaki komutu kullanarak Azure Stack Edge Pro cihazınızda oluşturduğunuz NFS payını bağlayın:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > `sync`Paylaşım paylaşımları, büyük dosyaların aktarım hızlarını geliştirir.
    > Paylaşma 'yı bağlamadan önce, yerel bilgisayarınızda bağlama noktası olarak görev yapacak dizinlerin zaten oluşturulduğundan emin olun. Bu dizinler herhangi bir dosya veya alt klasör içermemelidir.

    Aşağıdaki örnek, Azure Stack Edge Pro cihazınızda bir paylaşıma NFS aracılığıyla nasıl bağlanılacağını gösterir. Cihaz IP adresi: `10.10.10.60`. `mylinuxshare2` paylaşımı ubuntuVM öğesine bağlanmış. Paylaşımı bağlama noktası: `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> Bu sürüm için aşağıdaki uyarılar geçerlidir:
> - Paylaşımda bir dosya oluşturulduktan sonra dosyanın yeniden adlandırılması desteklenmez. 
> - Bir paylaşımdan bir dosyanın silinmesi, depolama hesabındaki girişi silmez.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, aşağıdaki Azure Stack Edge Pro konuları hakkında bilgi edindiniz:

> [!div class="checklist"]
> * Paylaşım ekleme
> * Paylaşıma bağlanma

Azure Stack Edge Pro 'Yu kullanarak verilerinizi nasıl dönüştürebileceğinizi öğrenmek için sonraki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro ile veri dönüştürme](./azure-stack-edge-deploy-configure-compute.md)