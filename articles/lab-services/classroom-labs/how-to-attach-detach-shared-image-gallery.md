---
title: Azure Lab Services paylaşılan görüntü galerisini Ekle veya ayır | Microsoft Docs
description: Azure Lab Services ' de bir laboratuvara paylaşılan görüntü Galerisi 'ni nasıl ekleyeceğinizi öğrenin.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 4f3f435110f0cba5ea2370f2900d4e7031904067
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890322"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Azure Lab Services paylaşılan görüntü galerisini bağlama veya ayırma
Öğretmenler/Laboratuvar Yöneticisi, bir şablon VM görüntüsünü başkalarının tarafından yeniden kullanılabilmesi için Azure [paylaşılan görüntü galerisine](../../virtual-machines/windows/shared-image-galleries.md) kaydedebilir. İlk adım olarak, Laboratuvar Yöneticisi laboratuvar hesabına var olan bir paylaşılan görüntü Galerisi ekler. Paylaşılan görüntü Galerisi eklendikten sonra, laboratuvar hesabında oluşturulan laboratuvarlar paylaşılan görüntü galerisine görüntü kaydedebilir. Diğer öğretmenler bu görüntüyü, sınıfları için bir şablon oluşturmak üzere paylaşılan görüntü galerisinden seçebilir. 

Bu makalede, paylaşılan bir görüntü galerisinin bir laboratuvar hesabına nasıl ekleneceği veya ayrılabilmesi gösterilmektedir. 

> [!NOTE]
> Bir görüntü paylaşılan bir görüntü galerisine kaydedildiğinde, Azure Lab Services kaydedilen görüntüyü aynı [Coğrafya](https://azure.microsoft.com/global-infrastructure/geographies/)'da bulunan diğer bölgelere çoğaltır. Görüntünün aynı coğrafya içindeki diğer bölgelerde oluşturulan laboratuvarlarda kullanılabilir olmasını sağlar. Resimleri paylaşılan bir görüntü galerisine kaydetmek, çoğaltılan tüm görüntülerin maliyetini içeren ek bir maliyet doğurur. Bu maliyet Azure Lab Services kullanım maliyetinden ayrıdır. Paylaşılan görüntü Galerisi fiyatlandırması hakkında daha fazla bilgi için bkz. [paylaşılan görüntü Galerisi – faturalandırma]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).


## <a name="configure-at-the-time-of-lab-account-creation"></a>Laboratuvar hesabı oluşturma sırasında yapılandırma
Laboratuvar hesabı oluştururken, laboratuvar hesabına paylaşılan bir görüntü Galerisi ekleyebilirsiniz. Açılan listeden var olan bir paylaşılan görüntü Galerisi seçebilir veya yeni bir tane oluşturabilirsiniz. Laboratuvar hesabına paylaşılan bir görüntü galerisi oluşturup eklemek için **Yeni oluştur**' u seçin, Galeri için bir ad girin ve **Tamam**' ı girin. 

![Laboratuvar hesabı oluşturma sırasında paylaşılan görüntü galerisini yapılandırma](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Laboratuvar hesabı oluşturulduktan sonra yapılandırma
Laboratuvar hesabı oluşturulduktan sonra, aşağıdaki görevleri gerçekleştirebilirsiniz:

- Paylaşılan görüntü galerisi oluşturma ve iliştirme
- Laboratuvar hesabına paylaşılan bir görüntü Galerisi ekleyin
- Laboratuvar hesabından paylaşılan görüntü galerisini ayır

## <a name="create-and-attach-a-shared-image-gallery"></a>Paylaşılan görüntü galerisi oluşturma ve iliştirme
1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Sol taraftaki menüden **tüm hizmetler** ' i seçin. **DevOps** bölümünde **Laboratuvar Hizmetleri** ' ni seçin. **Laboratuvar Hizmetleri**' nin yanında yıldız (`*`) seçeneğini belirlerseniz Sol menüdeki **Sık Kullanılanlar** bölümüne eklenir. Sonraki zamanda, **Sık Kullanılanlar**altında **Laboratuvar Hizmetleri** ' ni seçersiniz.

    ![Tüm Hizmetler-> Laboratuvar Hizmetleri](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. **Laboratuvar hesabı** sayfasını görmek için laboratuvar hesabınızı seçin. 
4. Sol menüdeki **paylaşılan görüntü Galerisi** ' ni seçin ve araç çubuğunda **+ Oluştur** ' u seçin.  

    ![Paylaşılan görüntü Galerisi düğmesi oluştur](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. **Paylaşılan görüntü Galerisi oluştur** penceresinde, Galeri için bir **ad** girin ve **Tamam**girin. 

    ![Paylaşılan görüntü Galerisi penceresi oluştur](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services paylaşılan görüntü galerisini oluşturur ve laboratuvar hesabına iliştirilir. Bu laboratuvar hesabında oluşturulan tüm laboratuvarların ekli paylaşılan görüntü galerisine erişimi vardır. 

    ![Ekli görüntü Galerisi](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    Alt bölmede, paylaşılan görüntü galerisinde görüntüler görürsünüz. Bu yeni galeride görüntü yok. Galeriye resim yüklediğinizde, bu sayfada görürsünüz.     

    Ekli paylaşılan görüntü galerisindeki tüm görüntüler varsayılan olarak etkindir. Seçili görüntüleri listede seçerek etkinleştirebilir veya devre dışı bırakabilir ve seçili görüntüleri **Etkinleştir** veya **Seçili görüntüleri devre dışı bırak** düğmesini kullanabilirsiniz.

## <a name="attach-an-existing-shared-image-gallery"></a>Var olan bir paylaşılan görüntü galerisini iliştirme
Aşağıdaki yordamda, var olan bir paylaşılan görüntü galerisinin bir laboratuar hesabına nasıl ekleneceği gösterilmektedir. 

1. **Laboratuvar hesabı** sayfasında sol menüdeki **paylaşılan görüntü Galerisi** ' ni seçin ve araç çubuğunda **Ekle** ' yi seçin. 

    ![Paylaşılan görüntü Galerisi-ekleme düğmesi](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. **Var olan bir paylaşılan görüntü Galerisi Ekle** sayfasında, paylaşılan görüntü galerinizi seçin ve **Tamam**' ı seçin.

    ![Mevcut bir galeri seçin](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Aşağıdaki ekranı görürsünüz: 

    ![Listedeki galerim](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    Bu örnekte, paylaşılan görüntü galerisinde henüz görüntü yok.

    Azure Lab Services kimlik, laboratuvara eklenmiş paylaşılan görüntü galerisine katkıda bulunan olarak eklenir. Öğretmenler/BT Yöneticisi 'nin sanal makine görüntülerini paylaşılan görüntü galerisine kaydetmesine izin verir. Bu laboratuvar hesabında oluşturulan tüm laboratuvarların ekli paylaşılan görüntü galerisine erişimi vardır. 

    Ekli paylaşılan görüntü galerisindeki tüm görüntüler varsayılan olarak etkindir. Seçili görüntüleri listede seçerek etkinleştirebilir veya devre dışı bırakabilir ve seçili görüntüleri **Etkinleştir** veya **Seçili görüntüleri devre dışı bırak** düğmesini kullanabilirsiniz. 

## <a name="detach-a-shared-image-gallery"></a>Paylaşılan görüntü galerisini ayır
Laboratuvara yalnızca bir paylaşılan görüntü Galerisi eklenebilir. Başka bir paylaşılan görüntü galerisi eklemek isterseniz yenisini eklemeden önce geçerli olanı ayırın. Bir paylaşılan görüntü galerisini laboratuvarınızdan ayırmak için araç çubuğunda **Ayır** ' ı seçin ve ayırma işlemini onaylayın. 

![Paylaşılan görüntü galerisini laboratuvar hesabından ayırın](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Sonraki adımlar
Bir laboratuvar görüntüsünü paylaşılan görüntü galerisine kaydetme veya bir VM oluşturmak için paylaşılan görüntü galerisinden bir görüntü kullanma hakkında bilgi edinmek için bkz. [paylaşılan görüntü Galerisi 'ni kullanma](how-to-use-shared-image-gallery.md).

Paylaşılan görüntü galerileri hakkında genel bilgi için bkz. [paylaşılan görüntü Galerisi](../../virtual-machines/windows/shared-image-galleries.md).
