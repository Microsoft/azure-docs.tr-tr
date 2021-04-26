---
title: Azure Data Box Gateway ile verileri aktarma | Microsoft Docs
description: Azure Data Box Gateway paylaşımlara nasıl ekleneceğini ve bunların nasıl bağlanacağınızı öğrenin ve Data Box Gateway cihazınız Azure 'a veri aktarabilir.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 0c0ef6157ebf70c896fbac5ff692246e4fad2c14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98937213"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>Öğretici: Azure Data Box Gateway veri aktarma


## <a name="introduction"></a>Giriş

Bu makalede, Data Box Gateway paylaşımlara nasıl ekleneceğini ve bunların nasıl bağlanabileceği açıklanır. Paylaşımlar eklendikten sonra cihaz, verileri Azure 'a aktarabilir.

Bu yordamın tamamlanması 10 dakika kadar sürebilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Paylaşım ekleme
> * Paylaşıma bağlanma

## <a name="prerequisites"></a>Önkoşullar

Data Box Gateway cihazınıza paylaşım eklemeden önce aşağıdakilerden emin olun:

- Bir sanal cihaz sağladınız ve [Hyper-V ' d e bir Data Box Gateway sağlama](data-box-gateway-deploy-provision-hyperv.md) veya [VMware 'de bir Data Box Gateway sağlama](data-box-gateway-deploy-provision-vmware.md)bölümünde ayrıntılı şekilde bağlantı kurdu olabilirsiniz.

- [Bağlama ve Azure Data Box Gateway etkinleştirme](data-box-gateway-deploy-connect-setup-activate.md)bölümünde açıklanan sanal cihazı etkinleştirdiniz.

- Cihazınız paylaşım oluşturmak ve veri aktarmak için hazır durumda.

## <a name="add-a-share"></a>Paylaşım ekleme

Bir paylaşma oluşturmak için aşağıdaki yordamı uygulayın:

1. [Azure Portal](https://portal.azure.com/), Data Box Gateway kaynağını seçin ve **genel bakış**' a gidin. Cihazınızın çevrimiçi olması gerekir. Cihaz komut çubuğunda **+ paylaşma Ekle** ' yi seçin.
   
   ![Paylaşım ekleme](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. **Paylaşma Ekle** bölümünde aşağıdaki yordamı uygulayın:

    1. Paylaşımınız için benzersiz bir ad sağlayın. Paylaşım adları yalnızca küçük harflerden, sayılardan ve kısa çizgilerden oluşabilir. Paylaşım adı 3 ile 63 karakter arası uzunlukta olmalı ve bir harf veya rakamla başlamalıdır. Her kısa çizginin önünde ve arkasında kısa çizgi dışında bir karakter bulunmalıdır.
    
    2. Paylaşma için bir **tür** seçin. Tür SMB veya NFS olabilir; varsayılan tür SMB'dir. SMB Windows istemcilerinin standardıdır ve NFS de Linux istemcilerinde kullanılır. SMB paylaşımları mı yoksa NFS paylaşımları mı seçtiğinize bağlı olarak, gösterilen seçenekler biraz farklı olur.

    3. Paylaşımın duracağı depolama hesabını sağlayın. Henüz kapsayıcı yoksa, yeni oluşturulan paylaşım adıyla depolama hesabında oluşturulur. Kapsayıcı zaten varsa, kapsayıcı kullanılır.
       > [!IMPORTANT]
       > Kullandığınız Azure depolama hesabının, Data Box Gateway bir cihazla kullanıyorsanız, bu sunucuda ayarlanmış bir kullanılabilirlik ilkesi olmadığından emin olun. Daha fazla bilgi için bkz. [BLOB depolama için dengesde kullanılabilirlik Ilkelerini ayarlama ve yönetme](../storage/blobs/storage-blob-immutability-policies-manage.md).
    
    4. Blok blobundan, sayfa blobundan veya dosyadan **Depolama hizmeti**'ni seçin. Seçilen hizmetin türü, verilerin Azure'da hangi biçimde tutulmasını istediğinize bağlıdır. Örneğin, buradaki örnekte biz verilerin Azure'da blob blokları olarak tutulmasını istediğimiz için Blok Blobunu seçtik. Sayfa Blobunu seçerseniz, verilerinizi 512 bayt hizalı olduğundan emin olmalısınız. Örneğin VHDX her zaman 512 bayt hizalıdır.
   
    5. Bu adım SMB paylaşımı mı yoksa NFS paylaşımı mı oluşturduğunuza bağlıdır.
     
    - **SMB paylaşma** - **Tüm ayrıcalıklı yerel kullanıcı** altında **Yeni oluştur** ' u seçin veya **var olanı kullanın**. Yeni bir yerel kullanıcı oluşturursanız, bir **Kullanıcı adı** ve **parola** girip **parolayı onaylayın**. Bu eylem, izinleri yerel kullanıcıya atar. Paylaşma düzeyi izinlerinin değiştirilmesi Şu anda desteklenmiyor.
    
        ![SMB paylaşımı ekleme](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Bu paylaşma verileri için **yalnızca okuma Işlemlerine Izin ver** onay kutusunu seçerseniz, salt okunurdur kullanıcılar belirtebilirsiniz.
        
    - **NFS paylaşma** -paylaşıma erişebilen izin VERILEN istemcilerin IP adreslerini girin.

        ![NFS paylaşımı ekleme](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Paylaşma oluşturmak için **Oluştur** ' u seçin.
    
    Paylaşma oluşturma işleminin devam ettiğini size bildirilir. Paylaşım belirtilen ayarlarla oluşturulduktan sonra, **Paylaşımlar** kutucuğunu yeni paylaşımı yansıtacak şekilde güncelleştirir.
    
    ![Güncelleştirilmiş paylaşımlar kutucuğu](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Paylaşıma bağlanma

Artık son adımda oluşturduğunuz bir veya daha fazla paylaşıma bağlanabilirsiniz. SMB veya NFS paylaşımınız olmasına bağlı olarak, adımlar farklılık gösterebilir.

### <a name="connect-to-an-smb-share"></a>SMB paylaşımına bağlanma

Data Box Gateway'inize bağlı Windows Server istemcinizde aşağıdaki komutları girerek SMB paylaşımına bağlanın:


1. Komut penceresinde şunu yazın: 

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    İstendiğinde paylaşımın parolasını girin. Burada, bu komutun örnek çıkışı gösterilir.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60'  
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. Klavyenizde Windows + R ' yi seçin. 
3. **Çalıştır** penceresinde, öğesini belirtip Tamam ' `\\<device IP address>` ı seçin.  Dosya Gezgini açılır. Artık oluşturduğunuz paylaşımları klasörler olarak görüntüleyebiliyor olmalısınız. İçeriğini görüntülemek için Dosya Gezgini'nde paylaşıma (klasöre) çift tıklayın.
 
    ![SMB paylaşımına bağlanma](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    Bu paylaşımlara oluşturulduğu anda veriler yazılır ve cihaz verileri buluta gönderir.

### <a name="connect-to-an-nfs-share"></a>NFS paylaşımına bağlanma

Cihazınıza bağlı Linux istemciniz için aşağıdaki yordamı uygulayın:

1. İstemcide NFSv4 istemcisinin yüklü olduğundan emin olun. NFS istemcisini yüklemek için aşağıdaki komutu kullanın:

   `sudo apt-get install nfs-common`

    Daha fazla bilgi için [Install NFSv4 Client](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client)bölümüne gidin.

2. NFS istemcisi yüklendikten sonra, Data Box Gateway cihazınızda oluşturduğunuz NFS paylaşımını bağlamak için aşağıdaki komutu kullanın:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    Bağlamaları ayarlamadan önce, yerel bilgisayarınızda bağlama noktası işlevi görecek dizinlerin zaten oluşturulmuş olduğundan ve hiçbir dosya veya alt klasör içermediğinden emin olun.

    Aşağıdaki örnekte, Gateway cihazındaki bir paylaşıma NFS yoluyla nasıl bağlanılacağı gösterilir. Sanal cihaz IP'si `10.10.10.60`dır, `mylinuxshare2` ubuntuVM'ye bağlanmıştır ve bağlama noktası `/home/databoxubuntuhost/gateway`dir.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> Bu sürüm için aşağıdaki uyarılar geçerlidir:
> - Paylaşımlardaki bir dosya oluşturulduktan sonra dosyanın yeniden adlandırılması desteklenmez.
> - Paylaşımdan dosya silindiğinde, depolama hesabındaki girdi silinmez.
> - `rsync`Verileri kopyalamak için kullanıyorsanız, `rsync -a` seçeneği desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, aşağıdaki Data Box Gateway konularını öğrendiniz:

> [!div class="checklist"]
> * Paylaşım ekleme
> * Paylaşıma bağlanma


Data Box Gateway'inizi yönetmeyi öğrenmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Data Box Gateway'i yönetmek için yerel web kullanıcı arabirimini kullanma](https://aka.ms/dbg-docs)