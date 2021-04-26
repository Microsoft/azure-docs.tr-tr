---
title: Azure Stream Analytics kümesi hızlı başlangıcı oluşturma
description: Azure Stream Analytics kümesi oluşturmayı öğrenin.
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/09/2021
ms.openlocfilehash: 7298862d29c02709afe737558421b4a8743ca5b0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305503"
---
# <a name="quickstart-create-a-dedicated-azure-stream-analytics-cluster-using-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak adanmış Azure Stream Analytics kümesi oluşturma

Bir Azure Stream Analytics kümesi oluşturmak için Azure portal kullanın. [Stream Analytics kümesi](cluster-overview.md) , karmaşık ve yoğun akış kullanım durumları için kullanılabilen tek kiracılı bir dağıtımdır. Birden çok Stream Analytics işini bir Stream Analytics kümesinde çalıştırabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Hızlı başlangıç tamamlama: Azure Portal kullanarak Stream Analytics bir Iş oluşturun](stream-analytics-quick-create-portal.md).

## <a name="create-a-stream-analytics-cluster"></a>Stream Analytics kümesi oluşturma

Bu bölümde bir Stream Analytics küme kaynağı oluşturacaksınız.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. **Kaynak oluştur**’u seçin. Market aramasını *Ara* kutusuna **Stream Analytics kümesi** yazın ve seçin. Ardından **Ekle**'yi seçin.

   :::image type="content" source="./media/create-cluster/search-result.png" alt-text="Küme arama sonucunu Stream Analytics.":::

1. **Stream Analytics kümesi oluştur** sayfasında, yeni kümeniz için temel ayarları girin.

   |Ayar|Değer|Açıklama |
   |---|---|---|
   |Abonelik|Abonelik adı|Bu Stream Analytics kümesi için kullanmak istediğiniz Azure aboneliğini seçin. |
   |Kaynak Grubu|Kaynak grubu adı|Bir kaynak grubu seçin veya **Yeni oluştur** seçeneğini belirleyin ve yeni kaynak grubu için benzersiz bir ad girin. |
   |Küme Adı|Benzersiz bir ad|Stream Analytics kümenizi tanımlamak için bir ad girin.|
   |Konum|Veri kaynaklarınıza ve havuzlar için en yakın bölge|Stream Analytics kümenizi barındırmak için bir coğrafi konum seçin. Düşük gecikme süreli analizler için veri kaynaklarınızda ve havuzlar için en yakın konumu kullanın.|
   |Akış birimi kapasitesi|36 ile 216 arasında |Kaç Stream Analytics işi çalıştırmayı planladığınızı ve işin gerektirdiği toplam SUs sayısını tahmin ederek küme boyutunu saptayın. 36 SUs ile başlayabilir ve daha sonra ölçeği gerektiği şekilde yukarı veya aşağı taşıyabilirsiniz.|

   ![Küme oluşturma](./media/create-cluster/create-cluster.png)

1. **Gözden geçir ve oluştur**’u seçin. **Etiketler** bölümünü atlayabilirsiniz.

1. Küme ayarlarını gözden geçirin ve ardından **Oluştur**' u seçin. Küme oluşturma uzun süredir çalışan bir işlemdir ve tamamlanması yaklaşık 60 dakika sürebilir. Portal sayfasında **Dağıtımınız tamamlandı** iletisinin görüntülenmesini bekleyin. Bu sırada, zaten yapmadıysanız, bu kümede çalıştırmak istediğiniz [Stream Analytics işleri](stream-analytics-quick-create-portal.md#create-a-stream-analytics-job) oluşturabilir ve geliştirebilirsiniz.

1. Stream Analytics kümesi sayfasına gitmek için **Kaynağa Git** ' i seçin.

## <a name="delete-your-cluster"></a>Kümenizi silme

Stream Analytics kümenizi, üzerinde herhangi bir Stream Analytics işi çalıştırmayı planlamıyorsanız silebilirsiniz. Azure portal adımları izleyerek kümenizi silin:

1. **Ayarlar** altında **Stream Analytics işlere** gidin ve çalıştıran tüm işleri durdurun.

1. Kümenize **genel bakış** bölümüne gidin. **Sil** ' i seçin ve kümenizi silmek için yönergeleri izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure Stream Analytics kümesi oluşturmayı öğrendiniz. Stream Analytics bir işi kümenizde nasıl çalıştıracağınızı öğrenmek için sonraki makaleye ilerleyin:

> [!div class="nextstepaction"]
> [Stream Analytics kümesinde Stream Analytics işleri yönetme](manage-jobs-cluster.md)
