---
title: Azure Lab Services bir laboratuvarın Market görüntülerini belirtin
description: Bu makalede, laboratuvar Oluşturucu 'nun Azure Lab Services laboratuvar hesabında laboratuvarları oluşturmak için kullanabileceği Market görüntülerinin nasıl ayarlanacağı gösterilmektedir.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5c81c8d7a15d67055729a29e98f5b7e30f3d0764
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434745"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Laboratuvar oluşturucuları için kullanılabilen Market görüntülerini belirtme
Laboratuvar sahibi olarak laboratuvar oluşturucuların laboratuvar hesabında laboratuvar oluşturmak için kullanacağı Market görüntülerini belirtebilirsiniz. 

## <a name="select-images-available-for-labs"></a>Labs için kullanılabilen görüntüleri seçme
Sol taraftaki menüden **Market görüntüleri**'ni seçin. Listede varsayılan olarak tüm görüntüler (hem etkin hem devre dışı) yer alır.  / En üstteki aşağı açılan listeden yalnızca etkin yalnızca **devre dışı** seçeneğini belirleyerek yalnızca etkin/devre dışı görüntüleri görmek için listeyi filtreleyebilirsiniz. 
    
![Market görüntüleri sayfası](./media/tutorial-setup-lab-account/marketplace-images-page.png)

Listede görüntülenen Market görüntüleri yalnızca aşağıdaki koşulları karşılayan görüntülerdir:
    
- Tek bir VM oluşturur.
- VM'leri sağlamak için Azure Resource Manager'ı kullanır
- Ek lisans planı satın alınmasını gerektirmez

## <a name="disable-images-for-a-lab"></a>Laboratuvar için görüntüleri devre dışı bırakma 
Bir laboratuvarın tek bir görüntüsünü devre dışı bırakmak için... seçeneğini belirleyin **. (üç nokta)** Son sütunda ve **görüntüyü devre dışı bırak**' ı seçin. 

![Tek bir görüntüyü devre dışı bırakma](./media/tutorial-setup-lab-account/disable-one-image.png) 

Alternatif olarak, görüntü adından önce onay kutusunu işaretleyip araç çubuğunda **Seçili görüntüleri devre dışı bırak** ' ı seçebilirsiniz. 

Aynı anda birden çok görüntüyü devre dışı bırakmak için, görüntü adlarından önce onay kutularını seçin ve araç çubuğunda **Seçili görüntüleri devre dışı bırak** ' ı seçin. 

![Birden fazla görüntüyü devre dışı bırakma](./media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Laboratuvar için görüntüleri etkinleştirme
Devre dışı bırakılmış bir görüntüyü etkinleştirmek için... seçeneğini belirleyin **. (üç nokta)** Son sütunda ve **görüntüyü etkinleştir**' i seçin. Alternatif olarak, görüntü adından önce onay kutusunu işaretleyip araç çubuğunda **Seçili görüntüleri etkinleştir** ' i seçin. 

Aynı anda birden çok görüntüyü devre dışı bırakmak için, görüntü adlarından önce onay kutularını seçin ve araç çubuğunda **Seçili görüntüleri etkinleştir** ' i seçin. 

## <a name="enable-images-at-the-time-of-lab-creation"></a>Laboratuvar oluşturma sırasında görüntüleri etkinleştir
Zaman Laboratuvarı oluştururken daha fazla görüntü etkinleştirebilirsiniz: 

1. **Laboratuvar hesabı sahibi** kimlik bilgilerini kullanarak [Azure Lab Services Web sitesinde](https://labs.azure.com) oturum açın
2. Varsayılan sanal makine görüntüsünü veya aşağı oku seçin. 
3. **Daha fazla görüntü seçeneklerini etkinleştir**' i seçin. 

    ![Diğer görüntü seçeneklerini etkinleştir](./media/specify-marketplace-images/enable-more-images-menu.png)
4. Seçtiğiniz görüntüleri etkinleştirmek için önceki bölümdeki yönergeleri izleyin. 
5. Önceki adımda seçtiğiniz görüntüleri görmek için **Yeni laboratuvar** penceresini kapatmanız ve yeniden açmanız gerekebilir. 



## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:

- [Laboratuvar sahibi olarak, Labs oluşturma ve yönetme](how-to-manage-classroom-labs.md)
- [Laboratuvar sahibi olarak, şablonları ayarlama ve yayımlama](how-to-create-manage-template.md)
- [Laboratuvar sahibi olarak, bir laboratuvarın kullanımını yapılandırma ve denetleme](how-to-configure-student-usage.md)
- [Laboratuvar kullanıcısı olarak, erişim laboratuvarları](how-to-use-classroom-lab.md)
