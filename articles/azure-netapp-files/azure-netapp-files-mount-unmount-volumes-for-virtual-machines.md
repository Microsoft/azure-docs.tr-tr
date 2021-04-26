---
title: Sanal makineler için Azure NetApp Files birimleri bağlama
description: Azure 'da Windows sanal makineleri veya Linux sanal makineleri için bir birimi bağlama veya çıkarma hakkında bilgi edinin.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 83d6e051f520737e750e6c46c192eb698e7bf0e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94842266"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Windows veya Linux sanal makineleri için birimi bağlama veya ayırma 

Gerektiğinde Windows veya Linux sanal makineleri için bir birimi bağlayabilir veya kaldırabilirsiniz.  Linux sanal makineleri için bağlama yönergeleri Azure NetApp Files kullanılabilir.  

## <a name="requirements"></a>Gereksinimler 

* Bir NFS birimine erişebilmek için en az bir dışarı aktarma ilkeniz olması gerekir.
* NFS birimini başarıyla bağlamak için, istemci ve NFS birimleri arasında aşağıdaki NFS bağlantı noktalarının açık olduğundan emin olun:
    * 111 TCP/UDP = `RPCBIND/Portmapper`
    * 635 TCP/UDP = `mountd`
    * 2049 TCP/UDP = `nfs`
    * 4045 TCP/UDP = `nlockmgr` (yalnızca NFSv3)
    * 4046 TCP/UDP = `status` (yalnızca NFSv3)

## <a name="steps"></a>Adımlar

1. **Birimler** dikey penceresine tıklayın ve sonra bağlamak istediğiniz birimi seçin. 
2. Seçili birimdeki **bağlama yönergeleri** ' ne tıklayın ve ardından birimi bağlamak için yönergeleri izleyin. 

    ![Bağlama yönergeleri NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Bağlama yönergeleri SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)  
    * Bir NFS birimi bağlarsanız, `vers` `mount` bağlamak istediğiniz birime KARŞıLıK gelen NFS protokol sürümünü belirtmek için komutunda seçeneğini kullandığınızdan emin olun. 
    * NFSv 4.1 kullanıyorsanız, dosya sisteminizi bağlamak için aşağıdaki komutu kullanın:  `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  
        > [!NOTE]
        > NFSv 4.1 kullanırsanız, dışarı aktarmayı bağlayan tüm VM 'Lerin benzersiz ana bilgisayar adları kullandığından emin olun.

3. Bir Azure VM başlatıldığında veya yeniden başlatıldığında bir NFS biriminin otomatik olarak bağlanmasını istiyorsanız, konaktaki dosyasına bir giriş ekleyin `/etc/fstab` . 

    Örneğin:  `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` , birim özellikleri dikey penceresinde bulunan Azure NetApp Files biriminin IP adresidir.
    * `$FILEPATH` Azure NetApp Files biriminin dışarı aktarma yoludur.
    * `$MOUNTPOINT` , NFS dışarı aktarmayı bağlamak için kullanılan Linux ana bilgisayarında oluşturulan dizindir.

4. Birimini NFS kullanarak Windows 'a bağlamak istiyorsanız:

    a. Öncelikle birimi bir UNIX veya Linux VM 'ye bağlayın.  
    b. Birimde bir `chmod 777` veya `chmod 775` komutunu çalıştırın.  
    c. Windows üzerinde NFS istemcisi aracılığıyla birimi bağlayın.
    
5. Bir NFS Kerberos birimi bağlamak istiyorsanız, daha fazla ayrıntı için bkz. [NFSv 4.1 Kerberos şifrelemesini yapılandırma](configure-kerberos-encryption.md) . 

## <a name="next-steps"></a>Sonraki adımlar

* [Azure NetApp Files için NFSv 4.1 varsayılan etki alanını yapılandırma](azure-netapp-files-configure-nfsv41-domain.md)
* [NFS hakkında SSS](./azure-netapp-files-faqs.md#nfs-faqs)
* [Ağ dosya sistemine genel bakış](/windows-server/storage/nfs/nfs-overview)
* [NFS Kerberos birimi bağlama](configure-kerberos-encryption.md#kerberos_mount)
