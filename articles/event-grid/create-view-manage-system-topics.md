---
title: Azure Event Grid (portal) içinde sistem konuları oluşturma, görüntüleme ve yönetme
description: Bu makalede, Azure portal kullanarak var olan sistem konusunun nasıl görüntüleneceği, Azure Event Grid sistem konuları oluşturulacağı gösterilmektedir.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 2dbb0893668d72cfebf2d64e6515ff6ab6fed9bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86115120"
---
# <a name="create-view-and-manage-event-grid-system-topics-in-the-azure-portal"></a>Azure portal Event Grid sistem konuları oluşturun, görüntüleyin ve yönetin
Bu makalede, Azure portal kullanarak sistem konuları oluşturma ve yönetme işlemlerinin nasıl yapılacağı gösterilir. Sistem konularına genel bakış için bkz. [sistem konuları](system-topics.md).

## <a name="create-a-system-topic"></a>Bir sistem konusu oluşturun
Azure kaynağı (depolama hesabı, Event Hubs ad alanı vb.) için bir sistem konusu oluşturarak iki şekilde oluşturabilirsiniz:

- Örneğin, depolama hesabı veya Event Hubs ad alanı gibi bir kaynağın **Olaylar** sayfasını kullanma. Azure kaynağı tarafından oluşturulan bir olay için olay aboneliği oluşturmak üzere Azure portal **Olaylar** sayfasını kullandığınızda (örneğin, Azure depolama hesabı), Portal, Azure kaynağı için bir sistem konusu oluşturur ve ardından sistem konusu için bir abonelik oluşturur. Azure kaynağında ilk kez bir olay aboneliği oluşturuyorsanız, sistem konusunun adını belirtirsiniz. İkinci zamandan itibaren, sistem konu adı sizin için salt okuma modunda görüntülenir. Ayrıntılı adımlar için bkz. [hızlı başlangıç: Blob Depolama olaylarını Web uç noktasına yönlendirme Azure Portal](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage) .
- **Event Grid sistem konuları** sayfasını kullanma. Aşağıdaki adımlar **Event Grid sistem konuları** sayfasını kullanarak bir sistem konusu oluşturmaktır. 

1. [Azure Portal](https://portal.azure.com)oturum açın.
2. Üstteki arama kutusuna **Event Grid sistem konuları** yazın ve ardından **ENTER** tuşuna basın. 

    ![Sistem konularını ara](./media/create-view-manage-system-topics/search-system-topics.png)
3. **Event Grid sistem konuları** sayfasında, araç çubuğunda **+ Ekle** ' yi seçin.

    ![Sistem konusu Ekle-araç çubuğu düğmesi](./media/create-view-manage-system-topics/add-system-topic-menu.png)
4. **Event Grid sistemi oluşturma konusu** sayfasında, aşağıdaki adımları uygulayın:
    1. **Konu türünü** seçin. Aşağıdaki örnekte, **depolama hesapları** seçeneği seçilidir. 
    2. Depolama hesabı kaynağınız olan **Azure aboneliğini** seçin. 
    3. Depolama hesabına sahip **kaynak grubunu** seçin. 
    4. **Depolama hesabını** seçin. 
    5. Oluşturulacak sistem konusu için bir **ad** girin. 
    
        > [!NOTE]
        > Bu sistem konu adını ölçümler ve tanılama günlükleri aramak için kullanabilirsiniz.
    6. **Gözden geçir ve oluştur**’u seçin.

        ![Sistem konusu oluştur](./media/create-view-manage-system-topics/create-event-grid-system-topic-page.png)
    5. Ayarları gözden geçirin ve **Oluştur**' u seçin. 
        
        ![İnceleme ve sistem oluşturma konusu](./media/create-view-manage-system-topics/system-topic-review-create.png)
    6. Dağıtım başarılı olduktan sonra, oluşturduğunuz sistem konusunun **Event Grid sistem konu** sayfasını görmek Için **Kaynağa Git** ' i seçin. 

        ![Sistem konu sayfası](./media/create-view-manage-system-topics/system-topic-page.png)


## <a name="view-all-system-topics"></a>Tüm sistem konularını görüntüleme
Tüm mevcut Event Grid sistem konularını görüntülemek için bu adımları izleyin. 

1. [Azure Portal](https://portal.azure.com)oturum açın.
2. Üstteki arama kutusuna **Event Grid sistem konuları** yazın ve ardından **ENTER** tuşuna basın. 

    ![Sistem konularını ara](./media/create-view-manage-system-topics/search-system-topics.png)
3. **Event Grid sistem konuları** sayfasında, tüm sistem konularını görürsünüz. 

    ![Sistem konuları listesi](./media/create-view-manage-system-topics/list-system-topics.png)
4. Ayrıntılarını görmek için listeden bir **Sistem konusu** seçin. 

    ![Sistem konu ayrıntıları](./media/create-view-manage-system-topics/system-topic-details.png)

    Bu sayfada, aşağıdaki bilgiler gibi sistem konusuyla ilgili ayrıntılar gösterilir: 
    - Kaynaktaki. Sistem konusunun oluşturulduğu kaynağın adı.
    - Kaynak türü. Kaynağın türü. Örneğin: `Microsoft.Storage.StorageAccounts` , `Microsoft.EventHub.Namespaces` , vb `Microsoft.Resources.ResourceGroups` .
    - Sistem konusu için oluşturulan tüm abonelikler.

    Bu sayfa aşağıdaki gibi işlemlere izin verir:
    - Bir olay aboneliği oluştur araç çubuğunda **+ olay aboneliği** seçin. 
    - Bir olay aboneliğini silin. Araç çubuğunda **Sil** ' i seçin. 
    - Sistem konusu için Etiketler ekleyin. Sol menüden **Etiketler** ' i seçin ve etiket adlarını ve değerlerini belirtin. 


## <a name="delete-a-system-topic"></a>Bir sistem konusunu silme
1. Tüm sistem konularını görüntülemek için [sistem konularını görüntüle](#view-all-system-topics) bölümündeki yönergeleri izleyin ve listeden silmek istediğiniz sistem konusunu seçin. 
2. **Event Grid sistem konusu** sayfasında, araç çubuğunda **Sil** ' i seçin. 

    ![Sistem konusu-Sil düğmesi](./media/create-view-manage-system-topics/system-topic-delete-button.png)
3. Onay sayfasında, silme işlemini onaylamak için **Tamam** ' ı seçin. Sistem konusunu ve ayrıca sistem konusunun tüm olay aboneliklerini siler.  

## <a name="create-an-event-subscription"></a>Olay aboneliği oluşturma
1. Tüm sistem konularını görüntülemek için [sistem konularını görüntüle](#view-all-system-topics) bölümündeki yönergeleri izleyin ve listeden silmek istediğiniz sistem konusunu seçin. 
2. **Event Grid sistem konusu** sayfasında, araç çubuğundan **+ olay aboneliği** ' ni seçin. 

    ![Sistem konusu-olay aboneliği Ekle düğmesi](./media/create-view-manage-system-topics/add-event-subscription-button.png)
3. **Konu türü**, **kaynak kaynağı** ve **konu adının** otomatik olarak doldurulduğunu doğrulayın. Bir ad girin, bir **uç nokta türü** seçin ve **uç noktasını** belirtin. Ardından olay aboneliğini oluşturmak için **Oluştur** ' u seçin. 

    ![Sistem konusu-olay aboneliği oluştur](./media/create-view-manage-system-topics/create-event-subscription.png)

## <a name="next-steps"></a>Sonraki adımlar
Azure Event Grid tarafından desteklenen sistem konuları ve konu türleri hakkında daha fazla bilgi edinmek için [Azure Event Grid bölümündeki sistem konularına](system-topics.md) bakın. 
