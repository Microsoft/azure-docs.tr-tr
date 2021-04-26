---
title: Azure Stack Edge Pro GPU ile paylaşımlara veri aktarma öğreticisi | Microsoft Docs
description: Azure Stack Edge Pro GPU cihazındaki paylaşımlara ekleme ve bunların nasıl bağlanacağınızı öğrenin.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 5f4a40e5b4e0dbced10f9d4a19609d9a0a49a1db
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058774"
---
# <a name="tutorial-transfer-data-via-shares-with-azure-stack-edge-pro-gpu"></a>Öğretici: Azure Stack Edge Pro GPU 'SU ile paylaşımlar aracılığıyla veri aktarımı

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu öğretici, Azure Stack Edge Pro cihazınızda paylaşımların nasıl ekleneceğini ve bu cihazlarda nasıl bağlanabileceğinizi açıklamaktadır. Paylaşımlar eklendikten sonra Azure Stack Edge Pro, verileri Azure 'a aktarabilir.

Bu yordamın tamamlanması 10 dakika kadar sürebilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Paylaşım ekleme
> * Paylaşıma bağlanma

## <a name="prerequisites"></a>Önkoşullar

Azure Stack Edge Pro 'ya paylaşımlar eklemeden önce şunları yaptığınızdan emin olun:

* Fiziksel cihazınızı [yükleme Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md)' da anlatıldığı şekilde yüklediniz.

* Fiziksel cihazı, [Azure Stack Edge Pro 'Yu etkinleştirme](azure-stack-edge-gpu-deploy-activate.md)bölümünde açıklandığı gibi etkinleştirdiniz.

## <a name="add-a-share"></a>Paylaşım ekleme

Bir paylaşma oluşturmak için aşağıdaki yordamı uygulayın:

1. [Azure Portal](https://portal.azure.com/), Azure Stack Edge kaynağını seçin ve **genel bakış**' a gidin. Cihazınızın çevrimiçi olması gerekir. **Bulut depolama ağ geçidi**' ni seçin.

   ![Cihaz çevrimiçi](./media/azure-stack-edge-gpu-deploy-add-shares/device-online-1.png)

2. Cihaz komut çubuğunda **+ paylaşma Ekle** ' yi seçin.

   ![Paylaşım ekleme](./media/azure-stack-edge-gpu-deploy-add-shares/select-add-share-1.png)

3. **Paylaşma Ekle** bölmesinde şu adımları izleyin:

    a. **Ad** kutusunda, paylaşımınız için benzersiz bir ad sağlayın.  
    Paylaşma adı yalnızca harf, rakam ve kısa çizgi içerebilir. 3 ila 63 karakter arasında olmalıdır ve bir harf veya rakam ile başlamalıdır. Kısa çizgilerden önce ve ardından bir harf ya da rakam gelmelidir.
    
    b. Paylaşma için bir **tür** seçin.  
    Bu tür SMB veya **NFS**, SMB 'nin varsayılan olarak **olması olabilir.** SMB Windows istemcilerinin standardıdır ve NFS de Linux istemcilerinde kullanılır.  
    SMB veya NFS paylaşımlarını seçmenize bağlı olarak, seçeneklerin geri kalanı biraz farklılık gösterir. 

    c. Paylaşımın duracağı depolama hesabını sağlayın.

    d. **Depolama hizmeti** açılan listesinde, **Blok Blobu**, **Sayfa Blobu** veya **dosyalar**' ı seçin.  
    Seçtiğiniz hizmetin türü, verilerin Azure 'da kullanmasını istediğiniz biçime bağlıdır. Bu örnekte, verileri Azure 'da blok Blobları olarak depolamak istiyoruz, **Blok Blobu** seçtik. **Sayfa Blobu**' nı seçerseniz, verilerinizin 512 bayt hizalı olduğundan emin olun. Örneğin VHDX her zaman 512 bayt hizalıdır.

   > [!IMPORTANT]
   > Kullandığınız Azure depolama hesabının, bir Azure Stack Edge Pro veya Data Box Gateway cihazından yararlanarak bu sunucuda ayarlanmış bir değişiklik olmadığından emin olun. Daha fazla bilgi için bkz. [BLOB depolama için dengesde kullanılabilirlik Ilkelerini ayarlama ve yönetme](../storage/blobs/storage-blob-immutability-policies-manage.md).

    e. Yeni bir blob kapsayıcısı oluşturun veya açılan listeden mevcut bir tane kullanın. Blob kapsayıcısı oluşturuyorsanız bir kapsayıcı adı sağlayın. Henüz kapsayıcı yoksa, yeni oluşturulan paylaşım adıyla depolama hesabında oluşturulur.
   
    f. Bir SMB veya NFS paylaşımının oluşturulup oluşturulmayacağını bağlı olarak, aşağıdaki adımlardan birini yapın: 
     
    - **SMB paylaşma**: **tüm ayrıcalık yerel kullanıcısı**' nın altında, **Yeni oluştur** ' u seçin veya **var olanı kullanın**. Yeni yerel kullanıcı oluşturuyorsanız kullanıcı adı ve parola girin, sonra da parolayı onaylayın. Bu eylem yerel kullanıcıya izinleri atar. Paylaşma düzeyi izinlerinin değiştirilmesi Şu anda desteklenmiyor. Bu paylaşma verileri için **yalnızca okuma Işlemlerine Izin ver** onay kutusunu seçerseniz, salt okunurdur kullanıcıları belirtebilirsiniz.
    
        ![SMB paylaşımı ekleme](./media/azure-stack-edge-gpu-deploy-add-shares/add-share-smb-1.png)
   
    - **NFS paylaşma**: paylaşıma erişebilen izin VERILEN istemcilerin IP adreslerini girin.

        ![NFS paylaşımı ekleme](./media/azure-stack-edge-gpu-deploy-add-shares/add-share-nfs-1.png)
   
4. Paylaşma oluşturmak için **Oluştur** ' u seçin.
    
    Paylaşma oluşturma işleminin devam ettiğini size bildirilir. Paylaşım belirtilen ayarlarla oluşturulduktan sonra, **Paylaşımlar** kutucuğunu yeni paylaşımı yansıtacak şekilde güncelleştirir.
    

## <a name="connect-to-the-share"></a>Paylaşıma bağlanma

Artık son adımda oluşturduğunuz bir veya daha fazla paylaşıma bağlanabilirsiniz. SMB veya NFS paylaşımınız olmasına bağlı olarak, adımlar farklılık gösterebilir.

İlk adım, SMB veya NFS paylaşımının kullanıldığı sırada cihaz adının çözümlenebildiğinden emin olunması.

### <a name="modify-host-file-for-name-resolution"></a>Ad çözümlemesi için konak dosyasını değiştirme

Artık cihazın IP 'sini ve cihazın yerel Web Kullanıcı arabiriminde tanımladığınız cihaz kolay adını şu şekilde ekleyeceksiniz:

- İstemci üzerindeki konak dosyası veya,
- DNS sunucusundaki konak dosyası

> [!IMPORTANT]
> Cihaz adı çözümlemesi için DNS sunucusunda konak dosyasını değiştirmenizi öneririz.

Cihaza bağlanmak için kullandığınız Windows istemcisinde aşağıdaki adımları uygulayın:

1. **Not defteri 'ni** yönetici olarak başlatın ve ardından konumunda bulunan **Hosts** dosyasını açın `C:\Windows\System32\Drivers\etc` .

    ![Windows Gezgini ana bilgisayarları dosyası](media/azure-stack-edge-gpu-deploy-add-shares/client-hosts-file-1.png)


2. Aşağıdaki girişi, cihazınız için uygun değerlerle değiştirerek **ana bilgisayar** dosyanıza ekleyin: 

    ```
    <Device IP>   <device friendly name>
    ``` 
    Yerel Web Kullanıcı arabirimindeki **cihaz** sayfasından **ağdan** ve CIHAZ kolay adından cihaz IP 'sini alabilirsiniz. Hosts dosyasının aşağıdaki ekran görüntüsünde giriş gösterilmektedir:

    ![Windows Gezgini ana bilgisayar dosyası 2](media/azure-stack-edge-gpu-deploy-add-shares/client-hosts-file-2.png)

### <a name="connect-to-an-smb-share"></a>SMB paylaşımına bağlanma

Azure Stack Edge Pro cihazınıza bağlı Windows Server istemcisinde şu komutları girerek bir SMB paylaşımıyla bağlantı edin:


1. Komut penceresinde şunu yazın: 

    `net use \\<Device name>\<share name>  /u:<user name for the share>`

    > [!NOTE]
    > Cihaz IP adresi aracılığıyla değil, yalnızca cihaz adıyla bir SMB paylaşımıyla bağlantı yapabilirsiniz.

2. İstendiğinde paylaşımın parolasını girin.  
   Burada, bu komutun örnek çıkışı gösterilir.

    ```powershell
    Microsoft Windows [Version 10.0.18363.476)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\AzureStackEdgeUser>net use \\myasetest1\myasesmbshare1 /u:aseuser
    Enter the password for 'aseuser' to connect to 'myasetest1':
    The command completed successfully.
    
    C: \Users\AzureStackEdgeUser>
    ```   

3. Klavyenizde Windows + R ' yi seçin.

4. **Çalıştır** penceresinde, `\\<device name>` öğesini belirtin ve ardından **Tamam**' ı seçin.  

    ![Windows çalıştırma iletişim kutusu](media/azure-stack-edge-gpu-deploy-add-shares/run-window-1.png)

   Dosya Gezgini açılır. Artık oluşturduğunuz paylaşımları klasörler olarak görüntüleyebiliyor olmalısınız. İçeriğini görüntülemek için Dosya Gezgini'nde paylaşıma (klasöre) çift tıklayın.
 
    ![SMB paylaşımına bağlanma](./media/azure-stack-edge-gpu-deploy-add-shares/file-explorer-smbshare-1.png)

    Bu paylaşımlara oluşturulduğu anda veriler yazılır ve cihaz verileri buluta gönderir.

### <a name="connect-to-an-nfs-share"></a>NFS paylaşımına bağlanma

Azure Stack Edge Pro cihazınıza bağlı Linux istemciniz için aşağıdaki yordamı uygulayın:

1. İstemcide NFSv4 istemcisinin yüklü olduğundan emin olun. NFS istemcisini yüklemek için aşağıdaki komutu kullanın:

   `sudo apt-get install nfs-common`

    Daha fazla bilgi için [Install NFSv4 Client](https://help.ubuntu.com/community/NFSv4Howto)bölümüne gidin.

2. NFS istemcisi yüklendikten sonra, aşağıdaki komutu kullanarak Azure Stack Edge Pro cihazınızda oluşturduğunuz NFS payını bağlayın:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    Yerel Web UI 'nizin **ağ** SAYFASıNDAN cihaz IP 'sini alabilirsiniz.

    > [!IMPORTANT]
    > `sync`Paylaşım paylaşımları, büyük dosyaların aktarım hızlarını geliştirir.
    > Paylaşma 'yı bağlamadan önce, yerel bilgisayarınızda bağlama noktası olarak görev yapacak dizinlerin zaten oluşturulduğundan emin olun. Bu dizinler herhangi bir dosya veya alt klasör içermemelidir.

    Aşağıdaki örnek, Azure Stack Edge Pro cihazınızda bir paylaşıma NFS aracılığıyla nasıl bağlanılacağını gösterir. Cihaz IP adresi: `10.10.10.60`. `mylinuxshare2` paylaşımı ubuntuVM öğesine bağlanmış. Paylaşımı bağlama noktası: `/home/azurestackedgeubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/azurestackedgeubuntuhost/Edge`

> [!NOTE] 
> Bu sürüm için aşağıdaki uyarılar geçerlidir:
> - Paylaşımda bir dosya oluşturulduktan sonra dosyanın yeniden adlandırılması desteklenmez. 
> - Bir paylaşımdan bir dosyanın silinmesi, Azure depolama hesabındaki girişi silmez.
> - `rsync`NFS üzerinden kopyalamak için kullanırken `--inplace` bayrağını kullanın. 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, aşağıdaki Azure Stack Edge Pro konuları hakkında bilgi edindiniz:

> [!div class="checklist"]
> * Paylaşım ekleme
> * Paylaşıma bağlanma

Azure Stack Edge Pro 'Yu kullanarak verilerinizi nasıl dönüştürebileceğinizi öğrenmek için sonraki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro ile veri dönüştürme](./azure-stack-edge-j-series-deploy-configure-compute.md)