---
title: Azure Güvenlik Merkezi 'nde güvenli puan | Microsoft Docs
description: " Azure Güvenlik Merkezi 'ndeki güvenli puanı kullanarak güvenlik önerilerinizi önceliklendirin. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/15/2019
ms.author: memildin
ms.openlocfilehash: 0a39fd1870ae803f2cf694e01c67d125fc96cac9
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200962"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde güvenli puanınızı geliştirme


Bu sayede birçok hizmetin güvenlik avantajları sunarak, iş yükünüzü güvenli hale getirmek ve bu avantajlardan yararlanmak için öncelikle ne yapmanız gereken adımları bilmek zordur. Azure güvenli puanı, güvenlik önerilerinizi gözden geçirir ve bunları sizin için önceliklendirir. bu sayede öncelikle hangi önerilerin gerçekleştirileceğini bilirsiniz. Bu, araştırmanın önceliklerini belirleyebilmeniz için en önemli güvenlik açıklarını bulmanıza yardımcı olur. Güvenlik puanı, iş yükü güvenlik duruşunuzu değerlendirmenize yardımcı olan bir araçtır.

## <a name="secure-score-calculation"></a>Güvenli puan hesaplaması

Güvenlik Merkezi, bir güvenlik analistinin çalışmasına, güvenlik önerilerinizi gözden geçirmeye ve her öneriye ne kadar önemli olduğunu belirlemede gelişmiş algoritmalar uygulamasına olanak sağlar.
Azure Güvenlik Merkezi, etkin önerileri sürekli olarak inceler ve bunlara göre güvenli puanınızı hesaplar. bir önerinin puanı, iş yükünüzü en iyi şekilde etkileyecek önem derecesini ve en iyi güvenlik uygulamalarını elde eder.

Ayrıca Güvenlik Merkezi, size genel olarak **güvenli bir puan**sağlar. 

**Genel güvenli puan** , tüm öneri Puanlarınızın birikmesi olur. Seçtiğiniz seçeneğe bağlı olarak abonelikleriniz veya yönetim gruplarınız genelinde genel güvenli puanınızı görüntüleyebilirsiniz. Puan, seçilen aboneliğe ve bu aboneliklerdeki etkin önerilere göre değişir.

 
En çok güvenli puanınızı hangi önerilerin etkilediğini denetlemek için, güvenlik merkezi panosunda en iyi üç önemli öneriyi görüntüleyebilir veya öneriler listesi dikey penceresinde önerileri, **güvenli puan etkisini** kullanarak sıralayabilirsiniz. sütunuyla.


Genel güvenli puanınızı görüntülemek için:

1. Azure panosunda **Güvenlik Merkezi** ' ne ve ardından **güvenli skor**' e tıklayın.
2. En üstte, güvenli puanı vurgular ' a bakabilirsiniz:
   - **Genel güvenlik puanı**, seçilen aboneliğin ilkelere göre belirlenen puanını gösterir
   - **Kategoriye göre güvenli puan** , en çok dikkat gerektiren kaynakları gösterir
   - **Güvenli puan etkisinden en önemli öneriler** , uygulamanızı uygularsanız en çok güvenli puanı iyileştirecek önerilerin bir listesini sağlar.
 
   ![Güvenli puan](./media/security-center-secure-score/secure-score-dashboard.png)

3. Aşağıdaki tabloda, aboneliklerinizin her birini ve her biri için genel güvenli puanı görebilirsiniz.

   > [!NOTE]
   > Aboneliklerinin güvenlik puanının toplamı, genel güvenlik puanına eşit değildir. Güvenlik puanı, iyi durumdaki kaynaklarınızla öneri başına kaynak sayısı arasındaki orana göre hesaplanır ve aboneliklerinizdeki güvenlik puanlarının toplamı değildir. 
   >
4. Güvenli puanınızı iyileştirmek için düzeltebileceğiniz bu aboneliğe yönelik önerileri görmek için **öneriyi görüntüle** ' ye tıklayın.
4. Öneriler listesinde, her öneri için, **güvenli puan etkisini**temsil eden bir sütun olduğunu görebilirsiniz. Bu sayı, önerileri izlerseniz genel güvenli puanınızın ne kadar iyileştirebileceğini temsil eder. Örneğin, aşağıdaki ekranda, **kapsayıcı güvenlik yapılandırmalarında güvenlik açıklarını düzeltmeniz**durumunda, güvenli puanınız 35 noktayla artacaktır.

   ![Güvenli puan](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Bireysel güvenli puan

Ayrıca, bireysel güvenli puanları görüntülemek için, bunları bireysel öneri dikey penceresinde bulabilirsiniz.  

**Öneri güvenlik puanı**, iyi durumdaki kaynaklarınızla toplam kaynak sayısı arasındaki orana göre hesaplanır. İyi durumdaki kaynakların sayısı toplam kaynak sayısına eşitse en yüksek öneri güvenlik puanı olan 50 puanı alırsınız. Güvenlik puanınızı üst sınıra yaklaştırmaya çalışmak için aşağıdaki önerileri izleyerek iyi durumda olmayan kaynakları düzeltin.

**Öneri etkisi**, önerilen adımları uygulamanız durumunda güvenlik puanınızın ne kadar artacağını gösterir. Örneğin güvenlik puanınız 42 ve **Öneri etkisi** +3 ise öneride belirtilen adımların uygulanması halinde puanınız 45 olacaktır.

Öneri, düzeltme adımları alınmadığı takdirde iş yükünüzün hangi tehditlere maruz olduğunu gösterir.

![tek öneri güvenli puanı](./media/security-center-secure-score/indiv-recommendation-secure-score.png)







## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, Azure Güvenlik Merkezi 'nde **güvenli puanı** kullanarak güvenlik duruşunuzu nasıl iyileştireceğiniz gösterilmektedir. Güvenlik Merkezi hakkında daha fazla bilgi için bkz.

* [Azure Güvenlik Merkezi ile ilgili SSS](security-center-faq.md) - Hizmeti kullanımı ile ilgili sık sorulan soruları bulabilirsiniz.
* [Azure Güvenlik Merkezi'nde güvenlik durumunu izleme](security-center-monitoring.md) - Azure kaynaklarınızın sistem durumunu nasıl izleyeceğiniz hakkında bilgi edinin.


