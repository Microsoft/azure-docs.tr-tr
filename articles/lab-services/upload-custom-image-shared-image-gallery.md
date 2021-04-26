---
title: Azure Lab Services-paylaşılan görüntü galerisine özel bir görüntü yükleme
description: Paylaşılan görüntü galerisine özel bir görüntü yüklemeyi açıklar. University BT departmanları, özellikle yararlı olan görüntüleri içeri aktarmayı bulur.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: 3e2f38b0cab87eab27181ddef79d0c02bd8c9bdb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98787172"
---
# <a name="upload-a-custom-image-to-shared-image-gallery"></a>Paylaşılan Görüntü Galerisi’ne özel görüntü yükleme

Paylaşılan görüntü Galerisi, Azure Lab Services Labs oluşturmak için kendi özel görüntülerinizi içeri aktarmak için kullanılabilir. University IT departmanları, özellikle aşağıdaki nedenlerden dolayı yararlı olan görüntüleri içeri aktarma işlemi bulur: 

* Bir laboratuvarın şablon VM 'sini kullanarak el ile görüntü oluşturmanız gerekmez.
* SCCM, Endpoint Manager vb. gibi diğer araçları kullanarak oluşturulan görüntüleri karşıya yükleyebilirsiniz.

Bu makalede, özel bir görüntü getirmek ve Azure Lab Services ' de kullanmak için gerçekleştirilebilecek adımlar açıklanmaktadır. 

> [!IMPORTANT]
> Görüntülerinizi fiziksel bir laboratuvar ortamından az Labs 'e taşıdığınızda, bunları uygun şekilde yeniden oluşturmanız gerekir. Yalnızca var olan görüntülerinizi fiziksel laboratuvarlardan yeniden kullanmayın. <br/>Ayrıntılar için bkz. [fiziksel laboratuvardan Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931) blog gönderisine geçme.

## <a name="bring-custom-image-from-a-physical-lab-environment"></a>Fiziksel laboratuvar ortamından özel görüntü getirme

Aşağıdaki adımlarda, bir fiziksel laboratuvar ortamından başlayan özel bir görüntünün nasıl içeri aktarılacağı gösterilmektedir. Bir VHD daha sonra bu ortamdan oluşturulur ve Azure Lab Services içinde kullanılabilmesi için Azure 'daki paylaşılan görüntü galerisine aktarılır.

Fiziksel laboratuvar ortamından bir VHD oluşturmak için birçok seçenek mevcuttur. Aşağıdaki adımlarda, bir Windows Hyper-V VM 'sinden VHD oluşturma gösterilmektedir:

1. Görüntünüzü oluşturduğunuz fiziksel laboratuar ortamınızda bir Hyper-V VM 'si ile başlayın.
    1. VM, 1. nesil bir VM olarak oluşturulmalıdır.
    1. VM sabit disk boyutu kullanmalıdır. Ayrıca, bu pencerede diskin boyutunu belirtebilirsiniz. Disk boyutu 128 GB 'tan büyük olmamalıdır.<br/>    
    Disk boyutu > 128 GB olan görüntüler Azure Lab Services desteklenmez. 
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="Sanal sabit diski bağlama":::   
    1. VM 'yi normalde olduğu gibi yansıın.
1. [VM 'ye bağlanın ve Azure için hazırlayın](../virtual-machines/windows/prepare-for-upload-vhd-image.md).
    1. [Azure için Windows yapılandırmalarını ayarlama](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure)
    1. [VM bağlantısı sağlamak için gereken en düşük Windows hizmetlerini denetleyin](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)
    1. [Uzak Masaüstü kayıt defteri ayarlarını Güncelleştir](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)
    1. [Windows güvenlik duvarı kurallarını yapılandırma](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)
    1. Windows güncelleştirmelerini yükler
    1. [Azure VM aracısını ve ek yapılandırmayı burada gösterildiği gibi yükleyip](../virtual-machines/windows/prepare-for-upload-vhd-image.md#complete-the-recommended-configurations) 
    
    Yukarıdaki adımlar, özelleştirilmiş bir görüntü oluşturur. Genelleştirilmiş bir görüntü oluşturuyorsanız, [Sysprep 'i](../virtual-machines/windows/prepare-for-upload-vhd-image.md#determine-when-to-use-sysprep)de çalıştırmanız gerekecektir. <br/>
        Görüntüde içerilen yazılımın gerektirdiği Kullanıcı dizinini (dosya, Kullanıcı hesabı bilgisi vb. içerebilir) korumak istiyorsanız, özelleştirilmiş bir görüntü oluşturmalısınız.
1. **Hyper-V** varsayılan olarak bir **VHDX** dosyası OLUŞTURDUĞUNDAN, bunu bir VHD dosyasına dönüştürmeniz gerekir.
    1. **Hyper-V Yöneticisi**  ->  **eylem**  ->  **düzenleme diski**' ne gidin.
    1. Burada, diski bir VHDX 'ten bir VHD 'ye **dönüştürme** seçeneğiniz vardır
    1. Disk boyutunu genişletmeye çalışırken 128 GB 'ı aşmadığından emin olun.        
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="Eylem seçin":::   
1. Yönetilen disk oluşturmak için VHD 'yi Azure 'a yükleyin.
    1. [BIR VHD 'Yi Azure 'A yükleme veya yönetilen bir diski başka bir bölgeye kopyalama](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md)başlığı altında açıklandığı gibi, komut satırından Depolama Gezgini veya AzCopy kullanabilirsiniz.        
    Makineniz uyku veya kilitler 'e gittiğinde, karşıya yükleme işlemi kesintiye uğrar ve başarısız olabilir.
    1. Bu adımın sonucunda, artık Azure portal görebileceğiniz bir yönetilen disk vardır. 
        Disk boyutunuzu seçmek için Azure portal "Size\Performance" sekmesini kullanabilirsiniz. Daha önce belirtildiği gibi, boyutun > 128 GB olmaması gerekir.
1. Yönetilen diskin anlık görüntüsünü alın.
    Bu, PowerShell 'den, Azure portal kullanılarak veya Depolama Gezgini içinden, [Portal veya PowerShell kullanarak anlık görüntü oluşturma](../virtual-machines/windows/snapshot-copy-managed-disk.md)bölümünde açıklandığı gibi yapılabilir.
1. Paylaşılan görüntü galerisinde, bir görüntü tanımı ve sürümü oluşturun:
    1. [Bir görüntü tanımı oluşturun](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition).
    1. Ayrıca, özelleştirilmiş/Genelleştirilmiş görüntü oluşturup oluşturmadığınızda burada belirtmeniz gerekir.
1. Azure Lab Services ' de laboratuvar oluşturun ve paylaşılan görüntü galerisindeki özel görüntüyü seçin.

    İşletim sistemi özgün Hyper-V VM 'sine yüklendikten sonra diski genişlettiyseniz, ayrılmamış disk alanını kullanmak için Windows 'daki C sürücüsünü de genişletmeniz gerekir. Bunu yapmak için, laboratuvar oluşturulduktan sonra şablon VM 'de oturum açın, [temel bir birimi genişletme](/windows-server/storage/disk-management/extend-a-basic-volume)bölümünde gösterilene benzer adımları uygulayın. Bunu, Kullanıcı arabirimi ve PowerShell kullanarak yapmak için seçenekler vardır.

## <a name="next-steps"></a>Sonraki adımlar

* [Paylaşılan görüntü galerisine genel bakış](../virtual-machines/shared-image-galleries.md)
* [Paylaşılan görüntü Galerisi 'ni kullanma](how-to-use-shared-image-gallery.md)