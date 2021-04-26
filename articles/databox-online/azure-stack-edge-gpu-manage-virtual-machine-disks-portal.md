---
title: Azure Stack Edge Pro GPU, Pro R, Mini R üzerinde VM disklerini Azure portal aracılığıyla yönetme
description: Azure Stack Edge Pro GPU, Azure Stack Edge Pro R ve Azure portal aracılığıyla Azure Stack Edge Mini R 'ye dağıtılan VM 'lerde veri diski ekleme veya ayırma dahil olmak üzere diskleri yönetmeyi öğrenin.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage disks on a VM running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: dff3f4bafdb35d89e8f1792c1aca68824a9bc685
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080649"
---
# <a name="use-the-azure-portal-to-manage-disks-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU 'unuzdaki sanal makinelerde bulunan diskleri yönetmek için Azure portal kullanın

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure portal kullanarak Azure Stack Edge Pro cihazınıza dağıtılan sanal makinelerde (VM) disk sağlayabilirsiniz. Diskler yerel Azure Resource Manager aracılığıyla cihazda sağlanır ve cihaz kapasitesini kullanır. Disk ekleme, diski ayırma gibi işlemler Azure portal aracılığıyla yapılabilir ve bu da depolama alanı sağlamak için yerel Azure Resource Manager çağrıları yapar. 

Bu makalede, mevcut bir VM 'ye veri diski ekleme, bir veri diski ayırma ve son olarak Azure portal aracılığıyla VM 'nin nasıl yeniden boyutlandırılması açıklanmaktadır. 

        
## <a name="about-disks-on-vms"></a>VM 'lerde diskler hakkında

VM 'niz bir işletim sistemi diskine ve veri diskine sahip olabilir. Cihazınızda dağıtılan her sanal makinenin bir bağlı işletim sistemi diski vardır. Bu işletim sistemi diskinde, sanal makine oluşturulduğunda seçilmiş olan önceden yüklenmiş bir işletim sistemi vardır. Bu disk, önyükleme birimini içerir.

> [!NOTE]
> Cihazınızdaki VM 'nin işletim sistemi disk boyutunu değiştiremezsiniz. İşletim sistemi disk boyutu, seçtiğiniz VM boyutuna göre belirlenir. 


Diğer yandan bir veri diski, cihazınızda çalışan VM 'ye bağlı yönetilen bir disktir. Uygulama verilerini depolamak için bir veri diski kullanılır. Veri diskleri genellikle SCSI sürücülerdir. VM 'nin boyutu, bir VM 'ye ekleyebileceğiniz veri disklerinin sayısını belirler. Varsayılan olarak, diskleri barındırmak için Premium Depolama kullanılır.

Cihazınızda dağıtılan bir VM, bazen geçici bir disk içerebilir. Geçici disk, uygulamalar ve süreçler için kısa vadeli depolama sağlar ve yalnızca sayfa veya takas dosyaları gibi verileri depolamak için tasarlanmıştır. Geçici diskteki veriler, bakım olayı sırasında veya bir VM 'yi yeniden dağıttığınızda kaybolabilir. Sanal makinenin başarılı bir standart yeniden başlatması sırasında, geçici diskteki veriler devam edecektir. 


## <a name="prerequisites"></a>Önkoşullar

Cihazınızda çalışan VM 'lerde Azure portal aracılığıyla disk yönetmeye başlamadan önce şunları yaptığınızdan emin olun:


1. Etkinleştirilmiş bir Azure Stack Edge Pro GPU cihazına erişirsiniz. Ayrıca, cihazınızda işlem için bir ağ arabirimi etkinleştirdiniz. Bu eylem, VM 'niz üzerinde bu ağ arabiriminde bir sanal anahtar oluşturur. 
    1. Cihazınızın yerel kullanıcı arabiriminde, **işlem**' a gidin. Sanal anahtar oluşturmak için kullanacağınız ağ arabirimini seçin.

        > [!IMPORTANT] 
        > İşlem için yalnızca bir bağlantı noktası yapılandırabilirsiniz.

    1. Ağ arabiriminde işlem etkinleştirin. Azure Stack Edge Pro GPU, bu ağ arabirimine karşılık gelen bir sanal anahtar oluşturur ve yönetir.

1. Cihazınızda dağıtılmış en az bir sanal makine var. Bu sanal makineyi oluşturmak için [Azure Portal aracılığıyla Azure Stack Edge Pro 'unuzda VM dağıtma](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)' daki yönergelere bakın.



## <a name="add-a-data-disk"></a>Veri diski ekleme

Cihazınızda dağıtılan bir sanal makineye disk eklemek için bu adımları izleyin. 

1. Veri diski eklemek istediğiniz sanal makineye gidin ve **genel bakış** sayfasına gidin. **Diskler**'i seçin.
    
    ![Genel Bakış sayfasında diskleri seçin](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-1.png)

1. **Diskler** dikey penceresinde, **veri diskleri** altında **Oluştur ve yeni bir disk Ekle**' yi seçin.

    ![Yeni bir disk oluşturun ve ekleyin](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-2.png)

1. **Yeni disk oluştur** dikey penceresinde aşağıdaki parametreleri girin:

    
    |Alan  |Açıklama  |
    |---------|---------|
    |Ad     | Kaynak grubu içinde benzersiz bir ad. Veri diski oluşturulduktan sonra ad değiştirilemez.     |
    |Boyut| GiB 'deki veri diskinizin boyutu. Bir veri diskinin en büyük boyutu, seçtiğiniz VM boyutuna göre belirlenir. Bir disk sağlarken, cihazınızdaki gerçek alanı ve kapasiteyi kullanan diğer VM iş yüklerinizi de göz önünde bulundurmanız gerekir.  |         

    ![Yeni bir disk dikey penceresi oluştur](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-3.png)

    **Tamam** ' ı seçin ve devam edin.

1. **Genel bakış** sayfasında, **diskler** altında, yeni diske karşılık gelen bir giriş görürsünüz. Varsayılanı kabul edin veya diske geçerli bir mantıksal birim numarası (LUN) atayın ve **Kaydet**' i seçin. LUN, SCSI disk için benzersiz bir tanımlayıcıdır. Daha fazla bilgi için bkz. [LUN nedir?](../virtual-machines/linux/azure-to-guest-disk-mapping.md#what-is-a-lun).

    ![Genel Bakış sayfasında yeni disk](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-4.png)

1. Disk oluşturma işleminin devam ettiğini belirten bir bildirim görürsünüz. Disk başarıyla oluşturulduktan sonra, sanal makine güncelleştirilir. 

    ![Disk oluşturma bildirimi](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-5.png)

1. **Genel bakış** sayfasına geri gidin. Disk listesi, yeni oluşturulan veri diskini görüntüleyecek şekilde güncelleştirilir.

    ![Veri disklerinin güncelleştirilmiş listesi](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-6.png)


## <a name="change-a-data-disk"></a>Veri diski değiştirme

Cihazınızda dağıtılan bir sanal makineyle ilişkili bir diski değiştirmek için bu adımları izleyin.

1. Değiştirilecek veri diskine sahip sanal makineye gidin ve **genel bakış** sayfasına gidin. **Diskler**'i seçin.

1. Veri diskleri listesinde, değiştirmek istediğiniz diski seçin. Seçili diskin en sağında düzenleme simgesini (kurşun kalem) seçin.  

    ![Değiştirilecek diski seçin](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-1.png)

1. **Diski Değiştir** dikey penceresinde yalnızca diskin boyutunu değiştirebilirsiniz. Diskle ilişkilendirilen ad, oluşturulduktan sonra değiştirilemez. **Boyutu** değiştirin ve değişiklikleri kaydedin.

    ![Veri diskinin boyutunu değiştirme](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-2.png)

    > [!NOTE]
    > Yalnızca bir veri diskini genişletebilirsiniz, diski daraltamazsınız.

1. **Genel bakış** sayfasında, disk listesi güncelleştirilmiş diski görüntüleyecek şekilde yenilenir.


## <a name="attach-an-existing-disk"></a>Var olan bir diski ekleme

Cihazınızda dağıtılan sanal makineye mevcut bir diski eklemek için bu adımları izleyin.

1. Var olan diski eklemek istediğiniz sanal makineye gidin ve **genel bakış** sayfasına gidin. **Diskler**'i seçin.
    
    ![Diskleri seçin ](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. **Diskler** dikey penceresinde, **veri diskleri** altında **var olan bir diski Ekle**' yi seçin.

    ![Var olan bir diski Ekle seçeneğini belirleyin](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-1.png)

1. Varsayılan LUN 'yi kabul edin veya geçerli bir LUN atayın. Açılan listeden mevcut bir veri diski seçin. Kaydet’i seçin.

    ![Var olan bir diski seçin](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-2.png)

    **Kaydet** ve devam et ' i seçin.

1. Sanal makinenin güncelleştirildiğini belirten bir bildirim görürsünüz. VM güncelleştirildikten sonra **genel bakış** sayfasına geri gidin. Yeni eklenen diski veri diskleri listesinde görüntülemek için sayfayı yenileyin.

    ![Genel Bakış sayfasında veri disklerinin güncelleştirilmiş listesini görüntüle](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="detach-a-data-disk"></a>Veri diski çıkarma

Cihazınızda dağıtılan bir sanal makineyle ilişkili bir veri diskini ayırmak veya kaldırmak için bu adımları izleyin.

> [!NOTE]
> - VM çalışırken bir veri diski kaldırabilirsiniz. VM 'den kullanımdan çıkarmadan önce hiçbir şeyin diski etkin bir şekilde kullandığından emin olun.
> - Bir diski ayırdıysanız, otomatik olarak silinmez.

1. Veri diskini ayırmak istediğiniz sanal makineye gidin ve **genel bakış** sayfasına gidin. **Diskler**'i seçin.

    ![Diskleri seçin](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. Disk listesinde, ayırmak istediğiniz diski seçin. Seçili diskin en sağında, ayırma simgesini (çapraz) seçin. Seçilen girdi ayrılecektir. **Kaydet**’i seçin. 

    ![Ayrımak üzere bir disk seçin](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/detach-data-disk-1.png)

1. Disk ayrıldıktan sonra, sanal makine güncellenir. Veri disklerinin güncelleştirilmiş listesini görüntülemek için **genel bakış** sayfasını yenileyin.

    ![Kaydet 'i seçin](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="next-steps"></a>Sonraki adımlar

Azure Stack Edge Pro cihazınızda sanal makinelerin nasıl dağıtılacağını öğrenmek için, bkz. [Azure Portal aracılığıyla sanal makineleri dağıtma](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
