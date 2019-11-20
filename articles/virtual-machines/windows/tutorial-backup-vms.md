---
title: Öğretici-Azure portal Windows sanal makinelerini yedekleme
description: Bu öğreticide, Azure Backup ile Windows sanal makinelerinizi korumak için Azure portalını kullanmayı öğreneceksiniz.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 603bffe3d28214dbdcd51888925c3c653d0759e7
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74068176"
---
# <a name="tutorial-back-up-and-restore-files-for-windows-virtual-machines-in-azure"></a>Öğretici: Azure'da Windows sanal makineleri için dosyaları yedekleme ve geri yükleme

Düzenli aralıklarla yedekleme yaparak verilerinizi koruyabilirsiniz. Azure Backup, coğrafi olarak yedekli kurtarma kasalarında depolanan kurtarma noktaları oluşturur. Bir kurtarma noktasından geri yükleme yaptığınızda VM’nin tamamını veya belirli dosyaları geri yükleyebilirsiniz. Bu makalede tek bir dosyanın Windows Server ve IIS çalıştıran bir VM'ye nasıl geri yükleneceği açıklanır. Henüz kullanılabilecek bir VM’niz yoksa [Windows hızlı başlangıcını](quick-create-portal.md) kullanarak VM oluşturabilirsiniz. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir VM’nin yedeğini oluşturma
> * Günlük yedekleme zamanlama
> * Bir yedeklemeden bir dosyayı geri yükleme

## <a name="backup-overview"></a>Backup’a genel bakış

Azure Backup hizmeti bir yedekleme işi başlattığında, yedekleme uzantısını zaman içinde bir noktanın anlık görüntüsü alacak şekilde tetikler. Azure Backup hizmeti _VMSnapshot_ uzantısını kullanır. Uzantı, VM’nin çalışıyor olması durumunda ilk VM yedeklemesi sırasında yüklenir. VM çalışmıyorsa Backup hizmeti, temel alınan depolamanın anlık görüntüsünü alır (VM durduğunda herhangi bir uygulama yazma işlemi gerçekleşmediği için).

Windows VM'lerinin anlık görüntüsü alınırken, Backup hizmeti Birim Gölge Kopyası Hizmeti (VSS) ile eşgüdümlü çalışarak sanal makine disklerinin tutarlı bir anlık görüntüsünü elde eder. Azure Backup hizmeti anlık görüntüyü aldıktan sonra veriler kasaya aktarılır. Verimliliği maksimuma çıkarmak için hizmet yalnızca bir önceki yedeklemeden itibaren değişmiş olan veri bloklarının aktarımını yapar.

Veri aktarımı tamamlandığında, anlık görüntü kaldırılır ve bir kurtarma noktası oluşturulur.

## <a name="create-a-backup"></a>Yedekleme oluşturma
Kurtarma Hizmetleri Kasasına basit bir zamanlanmış günlük yedekleme oluşturma. 

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. Sol taraftaki menüden **Sanal makineler**'i seçin. 
1. Listeden yedekleyeceğiniz VM'yi seçin.
1. VM dikey penceresinde, **işlemler** bölümünde, **Yedekle**' ye tıklayın. **Yedeklemeyi etkinleştir** dikey penceresi açılır.
1. **Kurtarma Hizmetleri kasası**’nda **Yeni oluştur**’a tıklayıp yeni kasa için ad belirtin. Sanal makineyle aynı kaynak grubunda ve konumda yeni bir kasa oluşturulur.
1. **Yedekleme Ilkesi Seç**altında, varsayılan **(yeni) Dailypolicy**' ı seçin ve ardından **yedeklemeyi etkinleştir**' e tıklayın.
1. Başlangıç kurtarma noktası oluşturmak için **Yedekleme** dikey penceresinde **Şimdi yedekle** seçeneğine tıklayın.
1. **Şimdi Yedekle** dikey penceresinde Takvim simgesine tıklayın, geri yükleme noktasının ne kadar süreyle tutulacağını seçmek için Takvim denetimini kullanın ve **Tamam**' a tıklayın.
1. VM 'niz için **yedekleme** dikey penceresinde, tamamlanmış geri yükleme noktası sayısını görürsünüz.


    ![Kurtarma noktaları](./media/tutorial-backup-vms/backup-complete.png)
    
İlk yedekleme yaklaşık olarak 20 dakika sürer. Yedeklemeniz tamamlandıktan sonra bu öğreticinin bir sonraki kısmına geçin.

## <a name="recover-a-file"></a>Dosyayı kurtarma

Bir dosyayı yanlışlıkla siler veya dosyada yanlışlıkla değişiklik yaparsanız dosyayı yedekleme kasanızdan kurtarmak için Dosya Kurtarma’yı kullanabilirsiniz. Dosya Kurtarma, kurtarma noktasını yerel bir sürücü olarak takmak için VM üzerinde çalışan bir betiği kullanır. Bu sürücüler, kurtarma noktasından dosyaları kopyalayıp VM’ye geri yükleyebilmeniz için 12 saat boyunca takılı kalır.  

Bu örnekte, IIS'nin varsayılan web sayfasında kullanılan görüntü dosyasının nasıl kurtarıldığını gösteriyoruz. 

1. Tarayıcıyı açın ve varsayılan IIS sayfasını görüntülemek için VM'nin IP adresine bağlanın.

    ![Varsayılan IIS web sayfası](./media/tutorial-backup-vms/iis-working.png)

1. VM’ye bağlanın.
1. VM'de **Dosya Gezgini**'ni açın ve \inetpub\wwwroot konumuna gidip **iisstart.png** dosyasını silin.
1. Yerel bilgisayarınızda, varsayılan IIS sayfasındaki görüntünün gittiğini görmek için tarayıcıyı yenileyin.

    ![Varsayılan IIS web sayfası](./media/tutorial-backup-vms/iis-broken.png)

1. Yerel bilgisayarınızda, yeni bir sekme açın ve [Azure portal](https://portal.azure.com)'a gidin.
1. Sol taraftaki menüde **Sanal makineler**'i ve listedeki VM'lerden birini seçin.
1. VM dikey penceresinde, **işlemler** bölümünde, **Yedekle**' ye tıklayın. **Yedekleme** dikey penceresi açılır. 
1. Dikey pencerenin üst tarafındaki menüde **Dosya Kurtarma** seçeneğini belirleyin. **Dosya Kurtarma** dikey penceresi açılır.
1. **1. Adım: Kurtarma noktasını seçme** bölümünde, açılır menüden bir kurtarma noktası seçin.
1. **2. Adım: Dosyalara göz atmak ve kurtarmak için betiği indirme indirme** bölümünde **Yürütülebilir Dosyayı İndir** düğmesine tıklayın. Dosyanın parolasını kopyalayın ve güvenli bir yere kaydedin.
1. Yerel bilgisayarınızda **Dosya Gezgini**'ni açın, **İndirilenler** klasörünüze gidin ve indirilen .exe dosyasını kopyalayın. Dosya adında ön ek olarak VM adınız bulunur. 
1. VM 'niz (RDP bağlantısını kullanarak),. exe dosyasını sanal makinenizin masaüstüne yapıştırın. 
1. VM'nizin masaüstüne gidin ve .exe dosyasına çift tıklayın. Bir komut istemi başlatılır. Program, kurtarma noktasını erişebileceğiniz bir dosya paylaşma olarak takar. Paylaşımı oluşturmayı tamamladığında, **q** yazarak komut istemini kapatın.
1. VM'nizde **Dosya Gezgini**'ni açın ve dosya paylaşımı için kullanılmış olan sürücü harfine gidin.
1. \inetpub\wwwroot konumuna gidin ve dosya paylaşımından **iisstart.png** dosyasını kopyalayıp \inetpub\wwwroot konumuna yapıştırın. Örneğin, F:\inetpub\wwwroot\iisstart.png dosyasını kopyalayın ve dosyayı kurtarmak için bunu c:\inetpub\wwwroot konumuna yapıştırın.
1. Yerel bilgisayarınızda, IIS varsayılan sayfasını gösteren VM'nin IP adresine bağlandığınız tarayıcı sekmesini açın. Tarayıcı sayfasını yenilemek için CTRL + F5 tuşlarına basın. Artık görüntünün geri yüklendiğini görüyor olmalısınız.
1. Yerel bilgisayarınızda Azure portalı için tarayıcı sekmesine geri dönün ve **3. Adım: Kurtarma işleminden sonra diskleri çıkarma** bölümünde **Diskleri Çıkar** düğmesine tıklayın. Bu adımı gerçekleştirmeyi unutursanız takma noktası ile bağlantı 12 saatin sonunda otomatik olarak kesilir. Bu 12 saat sonra yeni bir bağlama noktası oluşturmak için yeni bir betik indirmeniz gerekir.





## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Bir VM’nin yedeğini oluşturma
> * Günlük yedekleme zamanlama
> * Bir yedeklemeden bir dosyayı geri yükleme

Sanal makineleri izleme hakkında bilgi edinmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Sanal makineleri yönetme](tutorial-govern-resources.md)









