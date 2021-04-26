---
title: Azure Sentinel ile olayları araştırın | Microsoft Docs
description: Bu öğreticide, atayabileceğiniz ve araştırmanız gereken olayları oluşturan gelişmiş uyarı kuralları oluşturmak için Azure Sentinel 'i nasıl kullanacağınızı öğrenin.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2021
ms.author: yelevin
ms.openlocfilehash: 8980a8920b4f41f5a8e6afe106415032eef2055b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375853"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Öğretici: Azure Sentinel ile olayları araştırın

> [!IMPORTANT]
> Araştırma grafiği artık **genel kullanıma** sunulmuştur. 

Bu öğretici, Azure Sentinel ile olayları araştırmanıza yardımcı olur. Veri kaynaklarınızı Azure Sentinel 'e bağladıktan sonra, şüpheli bir sorun olduğunda bildirim almak istersiniz. Bunu yapmanızı sağlamak için Azure Sentinel, atayabileceğiniz ve araştırmanız gereken olayları üreten gelişmiş uyarı kuralları oluşturmanıza olanak sağlar.

Bu makalede şunları ele alınmaktadır:
> [!div class="checklist"]
> * Olayları araştırma
> * Araştırma grafiğini kullanma
> * Tehditlere yanıt verme

Bir olay birden çok uyarı içerebilir. Bu, belirli bir araştırma için ilgili tüm kanıtın bir toplamadır. **Analiz** sayfasında oluşturduğunuz analiz kuralları temel alınarak bir olay oluşturulur. Önem ve durum gibi uyarılarla ilgili özellikler olay düzeyinde ayarlanır. Azure Sentinel 'e hangi tür tehditleri aradığınızı ve bunların nasıl bulunacağını öğrendikten sonra, olayları inceleyerek algılanan tehditleri izleyebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
- Yalnızca analiz kuralınızı ayarlarken varlık eşleme alanlarını kullandıysanız olayı araştırabileceksiniz. Araştırma grafiği için özgün olayınızın varlıkları içermesi gerekir.

- Olayları atamak için gereken bir Konuk kullanıcı varsa, kullanıcının Azure AD kiracınızda [Dizin okuyucu](../active-directory/roles/permissions-reference.md#directory-readers) rolüne atanması gerekir. Normal (konuk olmayan) kullanıcılara varsayılan olarak bu rol atanır.

## <a name="how-to-investigate-incidents"></a>Olayları araştırma

1. **Olayları** seçin. **Olaylar** sayfası, kaç olay olduğunu, kaç tane açık olduğunu, **devam** ediyor olarak ayarlamış olduğunuzu ve kaç tane kapatıldığını görmenizi sağlar. Her olay için, gerçekleştiği zamanı ve olayın durumunu görebilirsiniz. İlk olarak hangi olayların işleneceğini belirlemek için önem derecesine bakın.

    ![Olay önem derecesini görüntüle](media/tutorial-investigate-cases/incident-severity.png)

1. Olayları gerektiği gibi filtreleyebilirsiniz, örneğin durum veya önem derecesine göre.

1. Bir araştırmaya başlamak için belirli bir olay seçin. Sağ tarafta, olay için önem derecesi, dahil edilen varlıkların sayısının Özeti, bu olayı tetikleyen ham olaylar ve olayın benzersiz KIMLIĞI gibi ayrıntılı bilgileri görebilirsiniz.

1. Olaydaki uyarılar ve varlıklar hakkında daha fazla ayrıntı görüntülemek için olay sayfasında **tam ayrıntıları görüntüle** ' yi seçin ve olay bilgilerini özetleyen ilgili sekmeleri gözden geçirin. 

    ![Uyarı ayrıntılarını görüntüle](media/tutorial-investigate-cases/incident-timeline.png)

    Örnek:

    - **Zaman çizelgesi** sekmesinde, olaydaki uyarı ve yer işaretlerinin zaman çizelgesini gözden geçirin ve bu, saldırgan etkinliğinin zaman çizelgesini yeniden oluşturmanıza yardımcı olabilir.
    - **Uyarılar** sekmesinde, uyarının kendisini gözden geçirin. Uyarı ile ilgili tüm bilgileri, uyarıyı tetikleyen sorguyu, sorgu başına döndürülen sonuç sayısını ve uyarılar üzerinde PlayBook 'ları çalıştırma özelliğini görebilirsiniz. Olay içinde daha fazla ayrıntıya geçmek için **olay** sayısını seçin. Bu, sonuçları oluşturan sorguyu ve Log Analytics uyarıyı tetikleyen olayları açar. 
    - **Varlıklar** sekmesinde, uyarı kuralı tanımının bir parçası olarak eşleştirmekte olduğunuz tüm varlıkları görebilirsiniz.

1. Bir olayı etkin bir şekilde araştırıyorsanız, siz kapatıncaya kadar olayın durumunu **devam ediyor** olarak ayarlamanız iyi bir fikirdir.

1. Olaylar, belirli bir kullanıcıya atanabilir. Her olay için, **olay sahibi** alanını ayarlayarak bir sahip atayabilirsiniz. Tüm olaylar atanmamış olarak başlar. Ayrıca, diğer analistlerin ne araştırdığınızı anlayabilmesi ve kaygılarınızın olay etrafında ne olduğunu anlayabilmesi için yorum ekleyebilirsiniz.

    ![Kullanıcıya olay atama](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. Araştırma haritasını görüntülemek için **Araştır** ' ı seçin.

## <a name="use-the-investigation-graph-to-deep-dive"></a>Ayrıntılı bilgi edinmek için araştırma grafiğini kullanın

Araştırma grafiği analistlerin her İnceleme için doğru soruları sormasını sağlar. Araştırma grafiği, ilgili verileri herhangi bir ilgili varlıkla ilişkilendirerek olası bir güvenlik tehdidi kapsamını anlamanıza ve kök nedenini belirlemenize yardımcı olur. Grafik ' i seçerek ve farklı genişletme seçenekleri arasında seçim yaparak grafikte sunulan herhangi bir varlığı daha ayrıntılı bir şekilde araştırıp inceleyebilirsiniz.  
  
Araştırma grafiği size şunları sağlar:

- **Ham verilerden görsel bağlam**: canlı, görsel grafik, ham verilerden otomatik olarak ayıklanan varlık ilişkilerini görüntüler. Bu, farklı veri kaynakları genelinde bağlantıları kolayca görmenizi sağlar.

- **Tam araştırma kapsamı bulma**: bir ihlalin tam kapsamını yüzeye almak için yerleşik araştırma sorgularını kullanarak araştırma kapsamınızı genişletin.

- **Yerleşik araştırma adımları**: bir tehdit tarafında doğru soruları sorduğunuzdan emin olmak için önceden tanımlanmış araştırma seçeneklerini kullanın.

Araştırma grafiğini kullanmak için:

1. Bir olay seçin ve ardından **Araştır**' ı seçin. Bu sizi araştırma grafiğine götürür. Grafik, uyarıya doğrudan bağlı varlıkların bir tanım haritasını ve her bir kaynağı daha fazla bağlantılı olarak sağlar.

   > [!IMPORTANT] 
   > - Yalnızca analiz kuralınızı ayarlarken varlık eşleme alanlarını kullandıysanız olayı araştırabileceksiniz. Araştırma grafiği için özgün olayınızın varlıkları içermesi gerekir.
   >
   > - Azure Sentinel Şu anda **30 güne kadar eski olay** araştırmasını desteklemektedir.

   ![Haritayı görüntüleme](media/tutorial-investigate-cases/map1.png)

1. **Varlıklar** bölmesini açmak için bir varlık seçin, böylece ilgili varlık hakkındaki bilgileri gözden geçirebilirsiniz.

    ![Haritalardaki varlıkları görüntüle](media/tutorial-investigate-cases/map-entities.png)
  
1. Araştırmanızı sağlamak için her bir varlığın, varlık türü başına güvenlik uzmanlarımız ve analistlerimiz tarafından tasarlanan soruların listesini açığa çıkarmak için araştırmanızı genişletin. Bu seçenekler **araştırma sorgularını** çağırıyoruz.

    ![Diğer ayrıntıları keşfet](media/tutorial-investigate-cases/exploration-cases.png)

   Örneğin, bir bilgisayarda ilgili uyarılar isteyebilirsiniz. Bir araştırma sorgusu seçerseniz, elde edilen sahibine grafiğe geri eklenir. Bu örnekte, **ilgili uyarıları** seçmek grafiğe aşağıdaki uyarıları döndürdü:

    ![İlgili Uyarıları görüntüle](media/tutorial-investigate-cases/related-alerts.png)

1. Her araştırma sorgusu için, **Olaylar \>**' ı seçerek Ham olay sonuçlarını ve Log Analytics ' de kullanılan sorguyu açma seçeneğini belirleyebilirsiniz.

1. Olayı anlamak için, grafik size paralel bir zaman çizelgesi sunar.

    ![Haritada zaman çizelgesini görüntüle](media/tutorial-investigate-cases/map-timeline.png)

1. Hangi noktada grafikteki hangi öğelerin oluştuğunu görmek için zaman çizelgesinin üzerine gelin.

    ![Uyarıları araştırmak için haritada zaman çizelgesini kullanın](media/tutorial-investigate-cases/use-timeline.png)

## <a name="closing-an-incident"></a>Bir olayı kapatma

Belirli bir olayı çöztikten sonra (örneğin, araştırmanızın sonucu geldiğinde), olayın durumunu **kapalı** olarak ayarlamanız gerekir. Bunu yaptığınızda, bunu kapatmanızın nedenini belirterek olayı sınıflamak isteyip istemediğiniz sorulur. Bu adım zorunludur. **Sınıflandırmayı Seç** ' e tıklayın ve açılan listeden aşağıdakilerden birini seçin:

- Doğru Pozitif: Şüpheli etkinlik
- Zararsız Pozitif: Şüpheli ancak beklenen
- Hatalı Pozitif: Hatalı uyarı mantığı
- Hatalı Pozitif: Hatalı veri
- Belirsiz

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-dropdown.png" alt-text="Sınıflandırma seçme listesinde bulunan sınıflandırmaların vurgualdığı ekran görüntüsü.":::

Uygun sınıflandırmayı seçtikten sonra **Açıklama** alanına bir açıklayıcı metin ekleyin. Bu olaya geri başvurmanız gereken olayda yararlı olacaktır. İşiniz bittiğinde **Uygula** ' ya tıklayın ve olay kapatılır.

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-comment-apply.png" alt-text="{alternatif-metin}":::

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, Azure Sentinel kullanarak olayları araştırmaya nasıl başladığınızı öğrendiniz. [Otomatikleştirilmiş PlayBook 'lar kullanılarak tehditlere yanıt verme](tutorial-respond-threats-playbook.md)öğreticisine devam edin.
> [!div class="nextstepaction"]
> Tehditlere yönelik yanıtlarınızı otomatik hale getirmek için [tehditleri yanıtlayın](tutorial-respond-threats-playbook.md) .

