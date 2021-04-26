---
title: Azure Stack Edge Pro 'da VM 'Leri Azure portal aracılığıyla dağıtma
description: Azure portal aracılığıyla Azure Stack Edge Pro 'da sanal makineler oluşturmayı ve yönetmeyi öğrenin.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: 139b543160b679ba063a0633f9091e7bc0ef1fc1
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074948"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-the-azure-portal"></a>Azure Stack Edge Pro GPU cihazınızda VM 'Leri Azure portal aracılığıyla dağıtın

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure portal, şablonlar, Azure PowerShell cmdlet 'leri ve Azure CLı/Python betikleri aracılığıyla Azure Stack Edge cihazında sanal makineler (VM) oluşturabilir ve yönetebilirsiniz. Bu makalede, Azure portal kullanarak Azure Stack Edge cihazınızda bir sanal makinenin nasıl oluşturulacağı ve yönetileceği açıklanmaktadır. 

Bu makale Azure Stack Edge Pro GPU, Azure Stack Edge Pro R ve Azure Stack Edge Mini R cihazları için geçerlidir. 

> [!IMPORTANT] 
> Cihazınızda, buluta dağıtılan VM 'Leri yöneten kullanıcı için çok faktörlü kimlik doğrulamasını etkinleştirmenizi öneririz.
        
## <a name="vm-deployment-workflow"></a>VM dağıtımı iş akışı

Dağıtım iş akışının üst düzey özeti aşağıdaki gibidir:

1. Azure Stack Edge cihazınızda işlem için bir ağ arabirimi etkinleştirin. Bu, belirtilen ağ arabiriminde bir sanal anahtar oluşturur.
1. Azure portal 'ten sanal makinelerin bulut yönetimini etkinleştirin.
1. Depolama Gezgini kullanarak bir Azure depolama hesabına VHD yükleyin. 
1. VHD 'yi cihaza indirmek ve VHD 'den bir VM görüntüsü oluşturmak için karşıya yüklenen VHD 'yi kullanın. 
1. Önceki adımlarda oluşturulan kaynakları kullanın:
    1. Oluşturduğunuz VM görüntüsü.
    1. İşlem için etkinleştirilmiş ağ arabirimiyle ilişkili VSwitch.
    1. VSwitch ile ilişkili alt ağ.

    Ve aşağıdaki kaynakları satır içinde oluşturun veya belirtin:
    1. VM adı, sanal makine için desteklenen bir VM boyutu, oturum açma kimlik bilgileri seçin. 
    1. Yeni veri diskleri oluşturun veya var olan veri disklerini ekleyin.
    1. VM için statik veya dinamik IP 'yi yapılandırın. Statik bir IP sağlıyorsanız, işlem için etkinleştirilen Ağ arabiriminin alt ağ aralığındaki bir boş IP 'den birini seçin.

    Bir sanal makine oluşturmak için yukarıdaki kaynakları kullanın.


## <a name="prerequisites"></a>Önkoşullar

Azure portal aracılığıyla cihazınızda VM 'Leri oluşturmaya ve yönetmeye başlamadan önce şunları yaptığınızdan emin olun:

1. Azure Stack Edge Pro cihazınızdaki ağ ayarlarını [Adım 1: yapılandırma Azure Stack Edge Pro cihazı](./azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device)bölümünde açıklandığı gibi tamamladınız.

    1. İşlem için bir ağ arabirimini etkinleştirdiniz. Bu ağ arabirimi IP 'si, VM dağıtımı için bir sanal anahtar oluşturmak üzere kullanılır. Cihazınızın yerel kullanıcı arabiriminde, **işlem**' a gidin. Sanal anahtar oluşturmak için kullanacağınız ağ arabirimini seçin.

        > [!IMPORTANT] 
        > İşlem için yalnızca bir bağlantı noktası yapılandırabilirsiniz.

    1. Ağ arabiriminde işlem etkinleştirin. Azure Stack Edge Pro, bu ağ arabirimine karşılık gelen bir sanal anahtar oluşturur ve yönetir.

1. Oluşturmayı düşündüğünüz sanal makine için VM görüntüsünü oluşturmak üzere kullanacağınız bir Windows veya Linux VHD 'sine erişiminiz vardır.

## <a name="deploy-a-vm"></a>VM'yi dağıtma

Azure Stack Edge cihazınızda bir sanal makine oluşturmak için aşağıdaki adımları izleyin.

### <a name="add-a-vm-image"></a>VM görüntüsü ekleme

1. Bir VHD 'YI bir Azure depolama hesabına yükleyin. [Azure Depolama Gezgini kullanarak BIR VHD 'Yi karşıya yükleme](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md)bölümündeki adımları izleyin.

1. Azure portal, Azure Stack Edge cihazınız için Azure Stack Edge kaynağına gidin. **Uç işlem > sanal makinelere** gidin.

    ![VM görüntüsü Ekle 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

1. **Genel bakış** sayfasına gitmek Için **sanal makineleri** seçin. Sanal makine bulutu yönetimini **etkinleştirin** .
    ![VM görüntüsü Ekle 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)

1. İlk adım bir VM görüntüsü eklemektir. Önceki adımda depolama hesabına zaten bir VHD yüklediniz. Bu VHD 'yi bir VM görüntüsü oluşturmak için kullanacaksınız.

    VHD 'yi depolama hesabından indirmek ve cihaza eklemek için **Görüntü ekle** ' yi seçin. İndirme işlemi, VHD 'nin boyutuna ve indirme için kullanılabilir internet bant genişliğine bağlı olarak birkaç dakika sürer. 

    ![VM görüntüsü Ekle 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-3.png)

1. **Görüntü ekle** dikey penceresinde aşağıdaki parametreleri girin. **Add (Ekle)** seçeneğini belirleyin.


    |Parametre  |Açıklama  |
    |---------|---------|
    |Depolama blobundan indir    |VHD 'YI karşıya yüklediğiniz depolama hesabındaki Depolama Blobu konumuna gidin.         |
    |İndirin    | , Sanal makineyi dağıttığınız geçerli cihaza otomatik olarak ayarlanır.        |
    |Görüntüyü farklı kaydet      | Depolama hesabına yüklediğiniz VHD 'den oluşturmakta olduğunuz VM görüntüsünün adı.        |
    |İşletim sistemi türü     |VM görüntüsünü oluşturmak için kullanacağınız VHD 'nin işletim sistemi olarak Windows veya Linux 'tan birini seçin.         |
   

    ![VM görüntüsü Ekle 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-6.png)

1. VHD indirilir ve VM görüntüsü oluşturulur. Görüntü oluşturma işleminin tamamlanabilmesi birkaç dakika sürer. VM görüntüsünün başarıyla tamamlanmasıyla ilgili bir bildirim görürsünüz.

    ![VM resmi ekleme 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-8.png)


1. VM görüntüsü başarıyla oluşturulduktan sonra, **görüntüler** dikey penceresindeki görüntü listesine eklenir.
    ![VM görüntüsü Ekle 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-9.png)

    **Dağıtımlar** dikey penceresi, dağıtımın durumunu belirtecek şekilde güncelleştirilir.

    ![VM görüntüsü Ekle 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-10.png)

    Yeni eklenen görüntü Ayrıca **genel bakış** sayfasında de görüntülenir.
    ![VM görüntüsü ekleme 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-11.png)


### <a name="add-a-vm"></a>VM ekleme

VM görüntüsü oluşturduktan sonra bir VM oluşturmak için bu adımları izleyin.

1. **Genel bakış** sayfasında, **sanal makine Ekle**' yi seçin.

    ![VM 1 ekle](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-1.png)

1. **Temel bilgiler** sekmesinde, aşağıdaki parametreleri girin.


    |Parametre |Açıklama  |
    |---------|---------|
    |Sanal makine adı     |         |
    |Edge kaynak grubu     | VM ile ilişkili tüm kaynaklar için yeni bir kaynak grubu oluşturun.        |
    |Görüntü     | Cihazda bulunan VM görüntülerinin arasından seçim yapın.        |
    |Boyut     | [Desteklenen VM boyutları](azure-stack-edge-gpu-virtual-machine-sizes.md)arasından seçim yapın.        |
    |Kullanıcı adı     | Yöneticinin VM 'de oturum açması için varsayılan Kullanıcı adı *azureuser* kullanın.        |
    |Kimlik doğrulaması türü    | SSH ortak anahtarından veya Kullanıcı tanımlı paroladan birini seçin.       |
    |Parola     | Sanal makinede oturum açmak için bir parola girin. Parola en az 12 karakter uzunluğunda olmalı ve tanımlanmış [karmaşıklık gereksinimlerini](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)karşılamalıdır.        |
    |Parolayı onayla    | Parolayı yeniden girin.        |


    ![VM Ekle 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-basics-1.png)

    **İleri ' yi seçin: diskler**.

1. **Diskler** SEKMESINDE, sanal makinenize disk iliştirecaksınız. 
    
    1. **Yeni bir disk oluşturup eklemeyi** veya **var olan bir diski eklemeyi** seçebilirsiniz.

        ![VM Ekle 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-1.png)

    1. **Oluştur ve yeni bir disk Ekle** öğesini seçin. **Yeni disk oluştur** dikey penceresinde, disk için bir ad ve gib 'de boyut girin.

        ![VM Ekle 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-2.png)

    1.  Daha fazla disk eklemek için yukarıdaki işlemi tekrarlayın. Diskler oluşturulduktan sonra, **diskler** sekmesinde görünür. **İleri ' yi seçin: ağ**.

1. **Ağ sekmesinde,** sanal makinenizin ağ bağlantısını yapılandıracaksınız.

    
    |Parametre  |Açıklama |
    |---------|---------|
    |Sanal ağ    | Ağ arabiriminde işlem etkinleştirildiğinde, açılan listeden Azure Stack Edge cihazınızda oluşturulan sanal anahtarı seçin.    |
    |Alt ağ     | Bu alan, işlem ' i etkinleştirdiğiniz ağ arabirimiyle ilişkili alt ağ ile otomatik olarak doldurulur.         |
    |IP Adresi     | VM 'niz için statik veya dinamik bir IP sağlayın. Statik IP, belirtilen alt ağ aralığından kullanılabilir ve ücretsiz bir IP olmalıdır.        |

    ![VM Ekle 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-networking-1.png)

    **İleri ' yi seçin: gözden geçir + oluştur**.

1. **Gelişmiş** SEKMESINDE, VM 'nizi özelleştirmek için özel verileri veya Cloud-init ' i belirtebilirsiniz. 

    Bir sanal makineyi ilk önyüklemede özelleştirmek için Cloud-init ' i kullanabilirsiniz. Paketleri yüklemek ve dosyaları yazmak ya da kullanıcıları ve güvenliği yapılandırmak için Cloud-init ' i kullanın. İlk önyükleme işlemi sırasında Cloud-init çalıştırılıyorsa, yapılandırmanızı uygulamak için başka bir adım gerekmez. Cloud-init hakkında ayrıntılı bilgi için bkz. [Cloud-init Overview](../virtual-machines/linux/tutorial-automate-vm-deployment.md#cloud-init-overview).

    ![VM ekle 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-advanced-1.png)    

1. **Gözden geçir + oluştur** sekmesinde VM 'nin belirtimlerini gözden geçirin ve **Oluştur**' u seçin.

    ![VM 8 Ekle](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-review-create-1.png)

1. VM oluşturma başlar ve 20 dakikaya kadar sürebilir. VM oluşturmayı izlemek için **dağıtımlar** ' a gidebilirsiniz.

    ![VM Ekle 9](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-deployments-page-1.png)

    
1. VM başarıyla oluşturulduktan sonra, **genel bakış** sayfası yeni VM 'yi görüntüleyecek şekilde güncelleştirilir.

    ![VM ekleme 10](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-overview-page-1.png)

1. **Sanal makinelere** gitmek için yenı oluşturulan VM 'yi seçin.

    ![VM Ekle 11](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-page-1.png)

    Ayrıntıları görmek için VM 'yi seçin. 

    ![VM Ekle 12](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-details-1.png)

## <a name="connect-to-a-vm"></a>Sanal makineye bağlanma

Windows veya Linux VM oluşturup oluşturdığınıza bağlı olarak, bağlanma adımları farklı olabilir. Azure portal aracılığıyla cihazınızda dağıtılan VM 'lere bağlanamazsınız. Linux veya Windows VM 'nize bağlanmak için aşağıdaki adımları uygulamanız gerekir.

### <a name="connect-to-linux-vm"></a>Linux VM 'ye bağlanma

Bir Linux sanal makinesine bağlanmak için bu adımları izleyin.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>Windows VM 'ye bağlanma

Windows VM 'ye bağlanmak için aşağıdaki adımları izleyin.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

## <a name="next-steps"></a>Sonraki adımlar

Azure Stack Edge Pro cihazınızı yönetme hakkında bilgi edinmek için bkz.[Azure Stack Edge Pro 'yu yönetmek için yerel Web Kullanıcı arabirimini kullanma](azure-stack-edge-manage-access-power-connectivity-mode.md).