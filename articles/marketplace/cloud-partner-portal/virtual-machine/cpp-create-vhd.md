---
title: Azure Marketi için Azure ile uyumlu bir VHD oluşturma
description: Azure Marketi 'nde bir sanal makine teklifi için VHD oluşturmayı açıklar.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/27/2018
ms.author: pabutler
ms.openlocfilehash: 37fecb8100ec40ace02960a4f3390420a8bfc735
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816811"
---
# <a name="create-an-azure-compatible-vhd"></a>Azure ile uyumlu bir VHD oluşturma

Bu makalede, Azure Marketi 'nde bir sanal makine (VM) teklifiyle ilgili bir sanal sabit disk (VHD) oluşturmak için gereken adımlar ayrıntılı olarak açıklanır.  Ayrıca, Uzak Masaüstü Protokolü (RDP), sanal makine için boyut seçme, en son Windows güncelleştirmelerini yükleme ve VHD görüntüsünü Genelleştirme gibi çeşitli yönlere yönelik en iyi yöntemleri de içerir.  Aşağıdaki bölümler, genellikle Windows tabanlı VHD 'lere odaklanmaktadır; Linux tabanlı VHD 'ler oluşturma hakkında daha fazla bilgi için bkz. [Azure tarafından onaylanan dağıtımlara Linux](../../../virtual-machines/linux/endorsed-distros.md). 

> [!WARNING]
> Önceden yapılandırılmış, onaylı bir işletim sistemi içeren bir VM oluşturmak için Azure 'u kullanmak amacıyla bu konudaki yönergeleri izlemeniz önemle tavsiye edilir.  Çözümünüz ile uyumlu değilse, onaylanmış bir işletim sistemi kullanarak şirket içi bir VM oluşturmak ve yapılandırmak mümkündür.  Daha sonra, [Azure 'a yüklemek üzere bir WINDOWS VHD veya vhdx hazırlama](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)bölümünde açıklandığı gibi karşıya yükleme için bu uygulamayı yapılandırabilir ve hazırlarsınız.


## <a name="select-an-approved-base"></a>Onaylanan bir taban seçin
VM Görüntünüzün işletim sistemi VHD 'SI, Windows Server veya SQL Server içeren bir Azure onaylı temel görüntüye dayalı olmalıdır.
Başlamak için Microsoft Azure portal bulunan aşağıdaki görüntülerden birini bir VM oluşturun:

-   Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
-   [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)
-   [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)

> [!TIP]
> Geçerli Azure portal veya PowerShell 'i kullanıyorsanız, 8 Eylül 2014 ' de yayımlanan Windows Server görüntüleri onaylanır ve daha sonra onaylanır.

Alternatif olarak Azure, onaylı bir Linux dağıtımları yelpazesi sunar.  Geçerli bir liste için bkz. [Azure tarafından onaylanan dağıtımlara Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).


## <a name="create-vm-in-the-azure-portal"></a>Azure portal VM oluşturma 

Microsoft [Azure Portal](https://ms.portal.azure.com/), aşağıdaki adımları kullanarak temel görüntüsünü oluşturun.

1. VM teklifinizi yayımlamak istediğiniz Azure aboneliğinin Microsoft hesabı Portal 'da oturum açın.
2. Yeni bir kaynak grubu oluşturun ve **kaynak grubu adınızı**, **aboneliğinizi**ve **kaynak grubu konumunu**belirtin.  Daha fazla rehberlik için bkz. [kaynak gruplarını yönetme](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Sanal makineler Ayrıntılar sayfasını göstermek için sol menü çubuğundan **sanal makineler** ' e tıklayın. 
4. Bu yeni sayfada, **işlem** dikey penceresini göstermek Için **+ Ekle** ' ye tıklayın.  İlk ekranda sanal makine türünü görmüyorsanız, temel sanal makinenizin adını arayabilirsiniz, örneğin:

    ![Yeni VM 'nin işlem dikey penceresi](./media/publishvm_014.png)

5. Doğru sanal görüntüyü seçtikten sonra aşağıdaki değerleri sağlayın:
   * **Temel bilgiler** dikey penceresinde, 1-15 alfasayısal karakter arasında sanal makine Için bir **ad** girin. (Bu örnek `DemoVm009`kullanır.)
   * VM 'de yerel bir hesap oluşturmak için kullanılan bir **Kullanıcı adı** ve güçlü bir **parola**girin.  (Burada `adminUser` kullanılır.)  Parola 8-123 karakter uzunluğunda olmalı ve şu dört karmaşıklık gereksinimlerinden üçünü karşılamalıdır: bir küçük harf karakter, bir büyük harf karakter, bir sayı ve bir özel karakter. Daha fazla bilgi için bkz. [Kullanıcı adı ve parola gereksinimleri](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm).
   * Oluşturduğunuz kaynak grubunu seçin (burada `DemoResourceGroup`).
   * Bir Azure Veri Merkezi **konumu** seçin (burada `West US`).
   * Bu değerleri kaydetmek için **Tamam** ' ı tıklatın. 

6. Aşağıdaki önerileri kullanarak dağıtılacak VM 'nin boyutunu seçin:
   * VHD 'yi şirket içinde geliştirmeyi planlıyorsanız, boyut önemi yoktur. En küçük VM'lerden birini kullanabilirsiniz.
   * Görüntüyü Azure'da geliştirmeyi planlıyorsanız, seçilen görüntü için önerilen VM boyutlarından birini kullanın.
   * Fiyatlandırma bilgileri için, portalda görüntülenecek **Önerilen Fiyatlandırma katmanları** seçicisine bakın. Yayımcı tarafından sunulan üç önerilen boyutu görüntüler. (Burada yayımcı Microsoft.)

   ![Yeni VM 'nin boyut dikey penceresi](./media/publishvm_015.png)

7. **Ayarlar** dikey penceresinde **yönetilen disk kullan** seçeneğini **Hayır**olarak ayarlayın.  Bu, yeni VHD 'YI el ile yönetmenizi sağlar. ( **Ayarlar** dikey penceresi ayrıca, depolama ve ağ seçeneklerini değiştirmenizi sağlar; örneğin, **disk türünde** **Premium (SSD)** seçeneğini belirleyebilirsiniz.)  Devam etmek için **Tamam** 'ı tıklatın.

    ![Yeni VM 'nin ayarlar dikey penceresi](./media/publishvm_016.png)

8. Seçimlerinizi gözden geçirmek için **Özet**’e tıklayın. **Doğrulama başarılı** iletisini gördüğünüzde **Tamam**’a tıklayın.

    ![Yeni VM 'nin Özet dikey penceresi](./media/publishvm_017.png)

Azure, belirttiğiniz sanal makineyi sağlamaya başlar.  Sol taraftaki **sanal makineler** sekmesine tıklayarak ilerleme durumunu izleyebilirsiniz.  Oluşturulduktan sonra durum **çalışıyor**olarak değişir.  Bu noktada, [sanal makineye bağlanabilirsiniz](./cpp-connect-vm.md).


## <a name="next-steps"></a>Sonraki adımlar

Yeni Azure tabanlı VHD 'nizi oluştururken zorluk yaşıyorsanız, bkz. [VHD oluşturma sırasında yaygın sorunlar](./cpp-common-vhd-creation-issues.md).  Aksi halde, bir sonraki adımda Azure 'da oluşturduğunuz [VM 'lere bağlanmanız](./cpp-connect-vm.md) gerekir. 
