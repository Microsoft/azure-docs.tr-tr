---
title: Azure Güvenlik Merkezi 'nde Güvenirlik puanı | Microsoft Docs
description: " Azure Güvenlik Merkezi 'nin güven puanı ile nasıl çalışacağınızı öğrenin. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: memildin
ms.openlocfilehash: 0f0380ca4a285d9cb4a8472a40c77130a8f63035
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202387"
---
# <a name="alert-confidence-score"></a>Güvenilirlik puanı uyarısı 

Azure Güvenlik Merkezi, Azure 'da çalıştırdığınız kaynaklar üzerinde görünürlük sağlar ve olası sorunları algıladığında sizi uyarır. Uyarı hacmi bir güvenlik işlemleri ekibinin tek tek adreslenebilmesi için zor olabilir ve hangi uyarıların araştırılacağını önceliklendirmek için gerekli hale gelir. Uyarıların araştırması karmaşık ve zaman alıcı olabilir ve sonuç olarak bazı uyarılar yok sayılır.

Güvenlik Merkezi 'ndeki güven puanı, takımınızın uyarıları önceliklendirme ve önceliklendirmesine yardımcı olabilir. Güvenlik Merkezi, bir tehdidin meşru olup olmadığını anlamak için analist tarafından kullanılan sektör en iyi uygulamalarını, akıllı algoritmaları ve işlemleri otomatik olarak uygular ve Güvenirlik puanı biçiminde anlamlı öngörüler sağlar.

## <a name="how-the-confidence-score-is-triggered"></a>Güvenirlik puanı nasıl tetiklenir

Uyarılar, sanal makinelerinizde çalışan şüpheli süreçler algılandığında oluşturulur. Güvenlik Merkezi, Azure 'da çalışan Windows sanal makinelerinde bu uyarıları inceler ve analiz eder. Kuruluş genelinde birden fazla varlık ve veri kaynağı genelinde gelişmiş algoritmaları kullanarak otomatik denetimler ve bağıntılar gerçekleştirir ve tüm Azure kaynaklarınız ve Güvenlik Merkezi 'nin ne kadar güvenli olduğunu gösteren bir güven puanı sunar. uyarının orijinal olduğunu ve araştırılması gerektiğini unutmayın.

## <a name="understanding-the-confidence-score"></a>Güvenirlik Puanını anlama

Güvenirlik puanı 1 ile 100 arasında değişir ve güven güvenlik merkezi 'nin incelenmesi, uyarının araştırılması gerektiğini gösterir. Puan arttıkça, daha güvenli bir güvenlik merkezi, uyarının orijinal bir kötü amaçlı etkinliği gösterdiği anlamına gelir. Güven puanı, uyarının güven puanı alma nedeninin bir listesini içerir. Güvenirlik puanı, Güvenlik analistlerinin uyarılara yönelik önceliklerini önceliklendirmesini ve öncelikle saldırılara ve ihlallere yanıt vermek için geçen süreyi azaltarak, en çok bir kez saldırmayı daha kolay hale getirir.

Güven Puanını görüntülemek için:
- Güvenlik Merkezi portalında güvenlik uyarıları dikey penceresini açın.
-  Uyarılar ve olaylar en yüksekten en düşüğe doğru düzenlenmiştir, yani daha güvenli güvenlik merkezi bir uyarının tehdidi gösterdiği olması durumunda sayfanın en üstüne daha yakın hale gelir. 


 ![Güvenilirlik puanı][1]

Bir uyarıda Güvenlik Merkezi 'nin güvenliğine katkıda bulunan verileri görüntülemek için:
- Güvenlik Uyarısı dikey penceresinde, **güven**altında, güvenilirlik puanına katkıda bulunan gözlemleri görüntüleyin ve uyarıyla ilgili Öngörüler elde edin. Bu, uyarıya neden olan etkinliklerin doğası hakkında daha fazla öngörü sağlar.

  ![Şüpheli Güvenirlik puanı][2]

Ortamınızdaki uyarı önceliklendirme önceliğini belirlemek için Güvenlik Merkezi 'nin güven Puanını kullanın. Güvenilirlik puanı, uyarıları otomatik olarak inceleyerek, sektördeki en iyi uygulamaları ve akıllı algoritmaları uygulayarak ve bir sanal analist olarak davranarak, hangi tehditlerin gerçek olduğunu ve dikkat etmeniz gereken yerleri tespit ederek zaman ve çaba tasarrufu sağlar.


## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, uyarı araştırmasının önceliklerini belirlemek için güven Puanının nasıl kullanılacağı açıklanmaktadır. Güvenlik Merkezi hakkında daha fazla bilgi edinmek için şunlara bakın:

* [Azure Güvenlik Merkezi ile ilgili SSS](security-center-faq.md) - Hizmeti kullanımı ile ilgili sık sorulan soruları bulabilirsiniz.
* [Azure Güvenlik Merkezi'nde güvenlik durumunu izleme](security-center-monitoring.md) - Azure kaynaklarınızın sistem durumunu nasıl izleyeceğiniz hakkında bilgi edinin.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
