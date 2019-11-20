---
title: Öğretici-Azure portal Linux sanal makinelerini yedekleme
description: Bu öğreticide, Azure Backup ile Linux sanal makinelerinizi korumak için Azure portalını kullanmayı öğreneceksiniz.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 2a53086b959f5b93d17d307a59682a44fe1f33a8
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034595"
---
# <a name="tutorial-back-up-and-restore-files-for-linux-virtual-machines-in-azure"></a>Öğretici: Azure’da Linux sanal makineleri için dosyaları yedekleme ve geri yükleme

Düzenli aralıklarla yedekleme yaparak verilerinizi koruyabilirsiniz. Azure Backup, coğrafi olarak yedekli kurtarma kasalarında depolanan kurtarma noktaları oluşturur. Bir kurtarma noktasından geri yükleme yaptığınızda VM’nin tamamını veya belirli dosyaları geri yükleyebilirsiniz. Bu makalede tek bir dosyanın nginx çalıştıran bir Linux VM’ye nasıl geri yükleneceği açıklanır. Kullanılabilecek bir VM’niz zaten yoksa [Linux hızlı başlangıcını](quick-create-cli.md) kullanarak VM oluşturabilirsiniz. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir VM’nin yedeğini oluşturma
> * Günlük yedekleme zamanlama
> * Bir yedeklemeden bir dosyayı geri yükleme

## <a name="backup-overview"></a>Backup’a genel bakış

Azure Backup hizmeti bir yedekleme başlatır, yedekleme uzantısını zaman içinde önceki bir noktanın anlık görüntüsü alacak şekilde tetikler. Azure Backup hizmeti Linux’ta _VMSnapshotLinux_ uzantısını kullanır. Uzantı, VM’nin çalışıyor olması durumunda ilk VM yedeklemesi sırasında yüklenir. VM çalışmıyorsa Backup hizmeti, temel alınan depolamanın anlık görüntüsünü alır (VM durduğunda herhangi bir uygulama yazma işlemi gerçekleşmediği için).

Varsayılan olarak Azure Backup, Linux VM için dosya sistemiyle uyumlu bir yedekleme alır ancak [ön betik ve son betik çerçevesi kullanılarak uygulama ile tutarlı yedekleme](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent) alacak şekilde yapılandırılabilir. Azure Backup hizmeti anlık görüntüyü aldıktan sonra veriler kasaya aktarılır. Verimliliği maksimuma çıkarmak için hizmet yalnızca bir önceki yedeklemeden itibaren değişmiş olan veri bloklarının aktarımını yapar.

Veri aktarımı tamamlandığında, anlık görüntü kaldırılır ve bir kurtarma noktası oluşturulur.


## <a name="create-a-backup"></a>Yedekleme oluşturma
Bir Kurtarma Hizmetleri Kasasına zamanlanmış günlük yedekleme oluşturun:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Sol taraftaki menüden **Sanal makineler**'i seçin. 
3. Listeden yedekleyeceğiniz VM'yi seçin.
4. VM dikey penceresinde, **Ayarlar** bölümünden **Yedekleme**’ye tıklayın. **Yedeklemeyi etkinleştir** dikey penceresi açılır.
5. **Kurtarma Hizmetleri kasası**’nda **Yeni oluştur**’a tıklayıp yeni kasa için ad belirtin. Sanal makineyle aynı Kaynak Grubunda ve konumda yeni bir kasa oluşturulur.
6. **Yedekleme ilkesi**’ne tıklayın. Bu örnek için varsayılanları tutun ve **Tamam**’a tıklayın.
7. **Yedeklemeyi etkinleştir** dikey penceresinde **Yedeklemeyi Etkinleştir** seçeneğine tıklayın. Bu işlem, varsayılan zamanlamaya göre günlük bir yedekleme oluşturur.
10. Başlangıç kurtarma noktası oluşturmak için **Yedekleme** dikey penceresinde **Şimdi yedekle** seçeneğine tıklayın.
11. **Şimdi Yedekle** dikey penceresinde takvim simgesine tıklayın, bu kurtarma noktasının korunduğu son günü seçmek için takvim denetimini kullanın ve **Yedekleme**’ye tıklayın.
12. VM’nize yönelik **Yedekleme** dikey penceresinde tamamlanmış kurtarma noktalarının sayısını görürsünüz.

    ![Kurtarma noktaları](./media/tutorial-backup-vms/backup-complete.png)

İlk yedekleme yaklaşık olarak 20 dakika sürer. Yedeklemeniz tamamlandıktan sonra bu öğreticinin bir sonraki kısmına geçin.

## <a name="restore-a-file"></a>Bir dosyayı geri yükleme

Bir dosyayı yanlışlıkla siler veya dosyada yanlışlıkla değişiklik yaparsanız dosyayı yedekleme kasanızdan kurtarmak için Dosya Kurtarma’yı kullanabilirsiniz. Dosya Kurtarma, kurtarma noktasını yerel bir sürücü olarak takmak için VM üzerinde çalışan bir betiği kullanır. Bu sürücüler, kurtarma noktasından dosyaları kopyalayıp VM’ye geri yükleyebilmeniz için 12 saat boyunca takılı kalır.  

Bu örnekte, varsayılan /var/www/html/index.nginx-debian.html nginx web sayfasının nasıl kurtarılacağı gösterilmektedir. Bu örnekte VM’mizin genel IP adresi *13.69.75.209* şeklindedir. Aşağıdaki adımları uygulayarak VM’nizin IP adresini bulabilirsiniz:

 ```bash 
 az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
 ```

 
1. Yerel bilgisayarınızda bir tarayıcı açın ve varsayılan nginx web sayfasını görmek için VM’nizin genel IP adresini yazın.

    ![Varsayılan nginx web sayfası](./media/tutorial-backup-vms/nginx-working.png)

1. VM’nizde SSH gerçekleştirin.

    ```bash
    ssh 13.69.75.209
    ```
2. /var/www/html/index.nginx-debian.html kısmını silin.

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```
    
4. Yerel bilgisayarınızda, varsayılan nginx sayfasının kaybolduğundan emin olmak için CTRL + F5 tuşlarına basarak tarayıcıyı yenileyin.

    ![Varsayılan nginx web sayfası](./media/tutorial-backup-vms/nginx-broken.png)
    
1. Yerel bilgisayarınızdan [Azure portalında](https://portal.azure.com/) oturum açın.
6. Sol taraftaki menüden **Sanal makineler**'i seçin. 
7. Listeden VM’yi seçin.
8. VM dikey penceresinde, **Ayarlar** bölümünden **Yedekleme**’ye tıklayın. **Yedekleme** dikey penceresi açılır. 
9. Dikey pencerenin üst tarafındaki menüde **Dosya Kurtarma** seçeneğini belirleyin. **Dosya Kurtarma** dikey penceresi açılır.
10. **1. Adım: Kurtarma noktasını seçme** bölümünde, açılır menüden bir kurtarma noktası seçin.
11. **2. Adım: Dosyalara göz atmak ve kurtarmak için betiği indirme indirme** bölümünde **Yürütülebilir Dosyayı İndir** düğmesine tıklayın. İndirilen dosyayı yerel bilgisayarınıza kaydedin.
7. Betik dosyasını yerel olarak indirmek için **Betiği indir**’e tıklayın.
8. Bir Bash istemi açıp aşağıdaki ifadeyi yazın. Bunu yazarken *Linux_myVM_05-05-2017.sh* kısmını indirdiğiniz betiğin asıl yolu ve dosyasıyla, *azureuser* kısmını VM’nin kullanıcı adıyla ve *13.69.75.209* kısmını ise VM’nizin genel IP adresi ile değiştirin.
    
    ```bash
    scp Linux_myVM_05-05-2017.sh azureuser@13.69.75.209:
    ```
    
9. Yerel bilgisayarınızda, VM’ye giden bir SSH bağlantısı açın.

    ```bash
    ssh 13.69.75.209
    ```
    
10. VM’nizde yürütme izinlerini betik dosyasına ekleyin.

    ```bash
    chmod +x Linux_myVM_05-05-2017.sh
    ```
    
11. VM’nizde kurtarma noktasını dosya sistemi olarak takmak için betiği çalıştırın.

    ```bash
    ./Linux_myVM_05-05-2017.sh
    ```
    
12. Betiğin çıkışı, takma noktasına ilişkin yolu size sağlar. Çıkış şuna benzer:

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
                          
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
                         
    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath 

    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170505191055/Volume1

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

12. VM’nizde, takma noktasından nginx varsayılan web sayfasını dosyayı sildiğiniz yere tekrar kopyalayın.

    ```bash
    sudo cp ~/myVM-20170505191055/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```
    
17. Yerel bilgisayarınızda, nginx varsayılan sayfasını gösteren VM IP adresine bağlandığınız tarayıcı sekmesini açın. Tarayıcı sayfasını yenilemek için CTRL + F5 tuşlarına basın. Varsayılan sayfanın artık tekrar çalıştığını görürsünüz.

    ![Varsayılan nginx web sayfası](./media/tutorial-backup-vms/nginx-working.png)

18. Yerel bilgisayarınızda Azure portalı için tarayıcı sekmesine geri dönün ve **3. Adım: Kurtarma işleminden sonra diskleri çıkarma** bölümünde **Diskleri Çıkar** düğmesine tıklayın. Bu adımı gerçekleştirmeyi unutursanız takma noktası ile bağlantı 12 saatin sonunda otomatik olarak kesilir. Bu 12 saatin ardından yeni takma noktası oluşturmak için yeni bir betik indirmeniz gerekir.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Bir VM’nin yedeğini oluşturma
> * Günlük yedekleme zamanlama
> * Bir yedeklemeden bir dosyayı geri yükleme

Sanal makineleri izleme hakkında bilgi edinmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Sanal makineleri yönetme](tutorial-govern-resources.md)

