---
title: Azure Stack Edge Pro GPU cihazınız için Windows VHD 'nin Genelleştirilmiş görüntüsünden VM görüntüleri oluşturma
description: Bir Windows VHD veya VHDX 'ten başlayarak Genelleştirilmiş görüntülerden VM görüntülerinin nasıl yapılacağını açıklar. Azure Stack Edge Pro GPU cihazınızda dağıtılan VM 'lerle birlikte kullanılacak VM görüntülerini oluşturmak için bu Genelleştirilmiş görüntüyü kullanın.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: 978099accd57d15c750a27f77b2e220f569a2dd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962711"
---
# <a name="use-generalized-image-from-windows-vhd-to-create-a-vm-image-for-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro cihazınız için bir VM görüntüsü oluşturmak üzere Windows VHD 'den Genelleştirilmiş görüntü kullanma

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro cihazınızda VM 'Leri dağıtmak için, VM 'Leri oluşturmak için kullanabileceğiniz özel VM görüntüleri oluşturmanız gerekir. Bu makalede, genelleştirilmiş bir görüntü oluşturmak üzere bir Windows VHD veya VHDX hazırlamak için gereken adımlar açıklanmaktadır. Daha sonra bu Genelleştirilmiş görüntü, Azure Stack Edge Pro cihazınız için bir VM görüntüsü oluşturmak üzere kullanılır. 

## <a name="about-preparing-windows-vhd"></a>Windows VHD 'YI hazırlama hakkında

Bir Windows VHD veya VHDX, *Genelleştirilmiş* bir görüntü veya *özel* bir görüntü oluşturmak için kullanılabilir. Aşağıdaki tabloda, *Genelleştirilmiş* ve *özelleştirilmiş* görüntüler arasındaki temel farklılıklar özetlenmektedir.


|Görüntü türü  |Genelleştirilmiş  |Özelleştirilmiş  |
|---------|---------|---------|
|Hedef     |Herhangi bir sisteme dağıtıldı         | Belirli bir sistemi hedefleyen        |
|Önyüklemeden sonra kurulum     | Kurulum, VM 'nin ilk önyüklemesi sırasında gereklidir.          | Kurulum gerekli değil. <br> Platform VM 'yi açar.        |
|Yapılandırma     |Ana bilgisayar adı, Yönetici-Kullanıcı ve VM 'ye özgü diğer ayarlar gereklidir.         |Tamamen önceden yapılandırılmış.         |
|Ne zaman kullanılır     |Aynı görüntüden birden çok yeni VM oluşturma.         |Belirli bir makineyi geçirme veya önceki yedeklemeden bir VM 'yi geri yükleme.         |


Bu makalede, genelleştirilmiş bir görüntüden dağıtmak için gereken adımlar ele alınmaktadır. Özelleştirilmiş bir görüntüden dağıtmak için, bkz. cihazınız için [özelleştirilmiş WINDOWS VHD 'Yi kullanma](azure-stack-edge-placeholder.md) .

> [!IMPORTANT]
> Bu yordam, kaynak VHD 'nin özel yapılandırmalarla ve ayarlarla yapılandırıldığı durumları kapsamaz. Örneğin, özel güvenlik duvarı kuralları veya ara sunucu ayarları içeren bir VHD 'yi genelleştirmek için ek eylemler gerekebilir. Bu ek eylemler hakkında daha fazla bilgi için bkz. [WINDOWS VHD 'Yi Azure 'a yükleme-Azure sanal makinelerine hazırlama](../virtual-machines/windows/prepare-for-upload-vhd-image.md).


## <a name="vm-image-workflow"></a>VM görüntüsü iş akışı

Bir Windows VHD 'yi Genelleştirilmiş görüntü olarak kullanılmak üzere hazırlamak için üst düzey iş akışı aşağıdaki adımlara sahiptir:

1. Kaynak VHD veya VHDX 'i sabit boyutlu bir VHD 'ye dönüştürün.
1. Sabit VHD 'yi kullanarak Hyper-V ' d i sanal makine oluşturun.
1. Hyper-V VM 'ye bağlanın.
1. *Sysprep* yardımcı programını kullanarak VHD 'yi genelleştirin. 
1. Genelleştirilmiş görüntüyü blob depolamaya kopyalayın.
1. Cihazınızda VM 'Leri dağıtmak için genelleştirilmiş görüntü kullanın. Daha fazla bilgi için bkz. [sanal makineyi Azure Portal aracılığıyla dağıtma](azure-stack-edge-gpu-deploy-virtual-machine-portal.md) veya [PowerShell aracılığıyla VM dağıtma](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).


## <a name="prerequisites"></a>Önkoşullar

Bir Windows VHD 'yi Azure Stack Edge üzerinde Genelleştirilmiş görüntü olarak kullanılmak üzere hazırlanmadan önce, aşağıdakileri yaptığınızdan emin olun:

- Windows 'un desteklenen bir sürümünü içeren bir VHD veya VHDX var. Azure Stack Edge Pro için [Desteklenen Konuk Işletim sistemleri]() bölümüne bakın. 
- Hyper-V Yöneticisi yüklü bir Windows istemcisine erişiminiz var. 
- VHD 'nizi hazırlandıktan sonra depolamak için bir Azure Blob depolama hesabına erişiminiz var.

## <a name="prepare-a-generalized-windows-image-from-vhd"></a>VHD 'den genelleştirilmiş bir Windows görüntüsü hazırlama

## <a name="convert-to-a-fixed-vhd"></a>Sabit bir VHD 'ye dönüştürme 

Cihazınız için, VM görüntülerini oluşturmak için sabit boyutlu VHD 'ler gerekir. Kaynak Windows VHD 'nizi veya VHDX 'i sabit bir VHD 'ye dönüştürmeniz gerekir. Şu adımları izleyin:

1. İstemci sisteminizde Hyper-V Yöneticisi 'Ni açın. **Diski Düzenle**' ye gidin.

    ![Hyper-V Yöneticisi 'ni açın](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-1.png)

1. **Başlamadan önce** sayfasında, **İleri>**' yi seçin.

1. **Sanal sabit diski bul** sayfasında, dönüştürmek Istediğiniz kaynak Windows VHD 'SININ veya vhdx 'in konumuna gidin. **İleri>' yi** seçin.

    ![Sanal sabit disk sayfasını bul](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-2.png)

1. **Eylem Seç** sayfasında **Dönüştür** ' ü seçin ve **İleri>**' yi seçin.

    ![Eylem sayfası seçin](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-3.png)

1. **Disk Biçimi Seç** sayfasında, **VHD** biçimi ' ni seçin ve ardından **İleri>**' yi seçin.

   ![Disk biçimi sayfası seçin](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-4.png)


1. **Disk türünü seçin** sayfasında **sabit boyut** ' u seçin ve **İleri>**' yi seçin.

   ![Disk türü seçin sayfası](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-5.png)


1. **Diski Yapılandır** sayfasında, konuma gidin ve SABIT boyutlu VHD diski için bir ad belirtin. **İleri>' yi** seçin.

   ![Disk sayfasını yapılandırma](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-6.png)


1. Özeti gözden geçirin ve **son**' u seçin. VHD veya VHDX dönüştürme birkaç dakika sürer. Dönüştürme zamanı, kaynak diskin boyutuna bağlıdır. 

<!--
1. Run PowerShell on your Windows client.
1. Run the following command:

    ```powershell
    Convert-VHD -Path <source VHD path> -DestinationPath <destination-path.vhd> -VHDType Fixed 
    ```
-->
Bu sabit VHD 'YI bu makaledeki tüm sonraki adımlar için kullanacaksınız.


## <a name="create-a-hyper-v-vm-from-fixed-vhd"></a>Sabit VHD 'den Hyper-V VM oluşturma

1. **Hyper-V Yöneticisi**'nde, kapsam bölmesinde, bağlam menüsünü açmak için sistem Düğümünüzün üzerinde sağ tıklayın ve sonra **Yeni**  >  **sanal makine**' yi seçin.

    ![Kapsam bölmesinde yeni sanal makine seçin](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-1.png)

1. Yeni sanal makine Sihirbazı ' nın **başlamadan önce** sayfasında **İleri**' yi seçin.

1. **Ad ve konum belirtin** sayfasında, sanal makineniz Için bir **ad** ve **konum** belirtin. **İleri**’yi seçin.

    ![VM 'niz için ad ve konum belirtin](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-2.png)

1. **Üretimi belirle** sayfasında,. vhd cihaz görüntüsü türü için **1. nesil** ' i seçin ve ardından **İleri**' yi seçin.    

    ![Üretimi belirt](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-3.png)

1. İstediğiniz bellek ve ağ yapılandırmalarınızı atayın.

1. **Sanal sabit diski bağla** sayfasında, **var olan bir sanal sabit diski kullan**' ı seçin, daha önce oluşturduğumuz Windows sabit VHD 'nin konumunu belirtin ve ardından **İleri**' yi seçin.

    ![Sanal sabit disk sayfasına Bağlan](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-4.png)

1. **Özeti** gözden geçirin ve sonra sanal makineyi oluşturmak için **son** ' u seçin.

Sanal makinenin oluşturulması birkaç dakika sürer.
     

## <a name="connect-to-the-hyper-v-vm"></a>Hyper-V VM 'sine bağlanma

VM, istemci sisteminizdeki sanal makineler listesinde gösterilir. 


1. VM 'yi seçin ve ardından sağ tıklayıp **Başlat**' ı seçin. 

    ![VM 'yi seçin ve başlatın](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-2.png)

2. VM, **çalışıyor** olarak göster ' i gösterir. VM 'yi seçin ve sağ tıklayın ve **Bağlan**' ı seçin.

    ![VM’ye bağlanma](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-4.png)

VM 'ye bağlandıktan sonra makine Kurulum Sihirbazı 'nı tamamlayıp VM 'de oturum açın.


## <a name="generalize-the-vhd"></a>VHD 'YI genelleştirin  

VHD 'YI genelleştirmek için *Sysprep* yardımcı programını kullanın. 

1. VM 'nin içinde bir komut istemi açın.
1. VHD 'YI genelleştirmek için aşağıdaki komutu çalıştırın. 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    Ayrıntılar için bkz.  [Sysprep (Sistem hazırlama) genel bakış](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).
1.  Komut tamamlandıktan sonra VM kapanır. **VM'yi yeniden başlatmayın**.

## <a name="upload-the-vhd-to-azure-blob-storage"></a>VHD 'YI Azure Blob depolamaya yükleme

VHD 'niz artık Azure Stack Edge üzerinde genelleştirilmiş bir görüntü oluşturmak için kullanılabilir. 

1. VHD 'YI Azure Blob depolama alanına yükleyin. [Azure Depolama Gezgini kullanarak BIR VHD 'Yi karşıya yükleme](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md)konusundaki ayrıntılı yönergelere bakın.
1. Karşıya yükleme tamamlandıktan sonra, karşıya yüklenen görüntüyü kullanarak VM görüntülerini ve VM 'Leri oluşturabilirsiniz. 

<!-- this should be added to deploy VM articles - If you experience any issues creating VMs from your new image, you can use VM console access to help troubleshoot. For information on console access, see [link].-->



## <a name="next-steps"></a>Sonraki adımlar

Dağıtımın yapısına bağlı olarak, aşağıdaki yordamlardan birini seçebilirsiniz.

- [Azure portal aracılığıyla genelleştirilmiş bir görüntüden VM dağıtma](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Azure PowerShell aracılığıyla genelleştirilmiş bir görüntüden VM dağıtma](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)  
