---
title: macOS’da SMB üzerinden Azure dosya paylaşımını bağlama | Microsoft Docs
description: macOS’da SMB üzerinden Azure dosya paylaşımını bağlamayı öğrenin.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6bd696ce5a314b0c849256311d0629b917036ea2
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699544"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>macOS’da SMB üzerinden Azure dosya paylaşımını bağlama
[Azure Dosyaları](storage-files-introduction.md), Microsoft’un kullanımı kolay bulut dosya sistemidir. Azure dosya paylaşımları macOS El Capitan 10.11+ üzerine endüstri standardı SMB 3 protokolü kullanılarak takılabilir. Bu makalede, macOS’ta Bulucu kullanıcı arabirimiyle ve Terminal kullanarak Azure dosya paylaşımını bağlamanın iki farklı yolu gösterilir.

> [!Note]  
> SMB üzerinden Azure dosya paylaşımını bağlamadan önce, SMB paket imzalamasını devre dışı bırakmanızı öneririz. Bunu yapmazsanız, macOS’tan Azure dosya paylaşımına erişirken performansta düşüş olabilir. SMB bağlantınız şifrelenir, bu sayede bağlantınızın güvenliği etkilenmez. Terminalde, aşağıdaki komutlar [SMB paket imzalamasını devre dışı bırakma üzerine Apple destek makalesinde](https://support.apple.com/HT205926) anlatıldığı gibi SMB paket imzalamasını devre dışı bırakır:  
>    ```
>    sudo -s
>    echo "[default]" >> /etc/nsmb.conf
>    echo "signing_required=no" >> /etc/nsmb.conf
>    exit
>    ```

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>macOS’ta Azure dosya paylaşımını bağlama önkoşulları
* **Depolama hesabı adı**: Bir Azure dosya paylaşımının bağlanması için depolama hesabının adına ihtiyacınız olacaktır.

* **Depolama hesabı anahtarı**: Bir Azure dosya paylaşımının bağlanması için birincil (veya ikincil) depolama anahtarı gerekir. SAS anahtarları şu an bağlama için desteklenmemektedir.

* **Bağlantı noktası 445 ' ün açık olduğundan emin olun**: SMB, 445 numaralı TCP bağlantı noktası üzerinden iletişim kurar. İstemci makinenizde (Mac) güvenlik duvarının TCP bağlantı noktası 445’i engellemediğinden emin olun.

## <a name="mount-an-azure-file-share-via-finder"></a>Bulucu yoluyla Azure dosya paylaşımını bağlama
1. **Bulucu aç**: Finder, macOS 'ta varsayılan olarak açıktır, ancak Dock 'ta "macOS yüz simgesine" tıklayarak şu anda seçili olan uygulamanın olduğundan emin olabilirsiniz:  
    ![macOS yüz simgesi](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **"Git" menüsünden "sunucuya Bağlan" ı seçin**: Önkoşullardan UNC yolunu kullanarak, Başlangıç`\\`çift eğik çizgi ( `smb://` ) öğesini`\`ve tüm diğer ters eğik çizgileri () eğik çizgi (`/`) ile dönüştürür. Bağlantınız aşağıdaki gibi görünmelidir: !["Sunucuya Bağlan" iletişim kutusu](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Kullanıcı adı ve parola istendiğinde depolama hesabı adını ve depolama hesabı anahtarını kullanın**: "Sunucuya Bağlan" iletişim kutusunda "Bağlan" a tıkladığınızda, sizden Kullanıcı adı ve parola istenir (Bu işlem macOS Kullanıcı adınızla birlikte doldurulur). macOS Anahtarlığınıza depolama hesabı adı/depolama hesabı anahtarı yerleştirebilirsiniz.

4. **Azure dosya paylaşımından istediğiniz gibi kullanın**: Kullanıcı adı ve parola için içindeki paylaşma adı ve depolama hesabı anahtarını değiştirdikten sonra, bu paylaşımın bağlanması gerekir. Bunu, yerel klasör/dosya paylaşımını normalde kullandığınız gibi kullanabilirsiniz. Örneğin, dosyaları dosya paylaşımına sürükleyip bırakabilirsiniz:

    ![Bağlı bir Azure dosya paylaşımının anlık görüntüsü](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Terminal yoluyla Azure dosya paylaşımını bağlama
1. Değerini `<storage-account-name>`depolama hesabınızın adıyla değiştirin . İstendiğinde parola olarak Depolama Hesabı Anahtarı’nı girin. 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **Azure dosya paylaşımından istediğiniz gibi kullanın**: Azure dosya paylaşma, önceki komutla belirtilen bağlama noktasında takılacağı.  

    ![Bağlı Azure dosya paylaşımının anlık görüntüsü](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Sonraki adımlar
Azure Dosyaları hakkında daha fazla bilgi edinmek için şu bağlantılara göz atın.

* [Apple Destek Makalesi - Mac’inizde Dosya Paylaşımı ile bağlantı kurma](https://support.apple.com/HT204445)
* [SSS](../storage-files-faq.md)
* [Windows’da sorun giderme](storage-troubleshoot-windows-file-connection-problems.md)      
* [Linux’ta sorun giderme](storage-troubleshoot-linux-file-connection-problems.md)    
