---
title: Azure Güvenlik Merkezi'ndeki Güvenlik Playbook'u | Microsoft Docs
description: Bu belge, Azure Güvenlik Merkezi'ndeki güvenlik playbook'larını kullanarak güvenlik olaylarına verilen yanıtları otomatik hale getirmenize yardımcı olur.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: a8c45ddf-5c4c-4393-b6e9-46ed1f91bf5f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: memildin
ms.openlocfilehash: faf297ef4b07b76f98bb7773c636cd8f6228bbc0
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201732"
---
# <a name="security-playbook-in-azure-security-center-preview"></a>Azure Güvenlik Merkezi'ndeki Güvenlik Playbook'u (Önizleme)
Bu belge, Azure Güvenlik Merkezi'ndeki güvenlik playbook'larını kullanarak güvenlikle ilgili sorunlara yanıt vermenize yardımcı olur.

## <a name="what-is-security-playbook-in-security-center"></a>Güvenlik Merkezi'ndeki güvenlik playbook'u ne işe yarar?
Güvenlik playbook'u belirli bir playbook seçilen uyarı tarafından tetiklendiğinde Güvenlik Merkezi'nden çalıştırılabilecek yordam koleksiyonudur. Güvenlik playbook'u Güvenlik Merkezi tarafından tespit edilen belirli bir güvenlik uyarısına verilecek yanıtı otomatikleştirmenize ve yönetmenize yardımcı olabilir. Güvenlik Merkezi'ndeki Güvenlik Playbook'ları [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps) tabanlıdır. Bu nedenle güvenlik kategorisindeki Logic Apps şablonlarını kullanabilir, ihtiyaçlarınıza göre değiştirebilir veya [Azure Logic Apps iş akışını](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app) kullanarak yeni playbook'lar oluşturabilir ve Güvenlik Merkezi'ni tetikleyici olarak kullanabilirsiniz.

> [!NOTE]
> Playbook, Azure Logic Apps'ı kullandığı için ücret alınır. Ayrıntılı bilgi için [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) fiyatlandırma sayfasını ziyaret edin.

## <a name="how-to-create-a-security-playbook-from-security-center"></a>Güvenlik Merkezi'nden güvenlik playbook'u nasıl oluşturulur?
Güvenlik Merkezi'nden güvenlik playbook'u oluşturmak için bu adımları izleyin:

1.  **Güvenlik Merkezi** panosunu açın.
2.  Sol bölmedeki **Otomasyon ve Düzenleme** bölümünde **Playbook'lar (Önizleme)** öğesine tıklayın.

    ![Mantıksal Uygulama](./media/security-center-playbooks/security-center-playbooks-fig17.png)

3. **Güvenlik Merkezi - Playbook'lar (Önizleme)** sayfasında **Ekle** düğmesine tıklayın.

    ![Mantıksal uygulama oluşturma](./media/security-center-playbooks/security-center-playbooks-fig2.png)

4. **Mantıksal uygulama oluştur** sayfasında yeni mantıksal uygulamanızı oluşturmak için gerekli bilgileri yazın ve **Oluştur** düğmesine tıklayın. Oluşturma işlemi tamamlandıktan sonra yeni playbook listede görünür. Görünmüyorsa **Yenile** düğmesine tıklayın. Playbook'u düzenlemeye başlamak için üzerine tıklayın.

    ![Mantıksal uygulama oluşturma](./media/security-center-playbooks/security-center-playbooks-fig3.png)

5. **Mantıksal Uygulama Tasarımcısı** açılır. Yeni bir playbook oluşturmak için **Boş Mantıksal Uygulama** girişine tıklayın. Dilerseniz kategoriler bölümünden **Güvenlik** öğesini seçip şablonlardan birini kullanabilirsiniz.

    ![Mantıksal uygulama tasarımcısı](./media/security-center-playbooks/security-center-playbooks-fig4.png)

6. **Tüm bağlayıcılar ve tetikleyiciler içinde arayın** alanına *Azure Güvenlik Merkezi* yazın ve **Bir Azure Güvenlik Merkezi uyarısı yanıtı tetiklendiğinde** girişini seçin.

    ![Tetikleyici](./media/security-center-playbooks/security-center-playbooks-fig12.png)

7. Şimdi playbook'unuz tetiklendiğinde gerçekleştirilecek işlemleri tanımlayabilirsiniz. Eylem, mantıksal koşul, anahtar durumu koşulu veya döngü ekleyebilirsiniz.

    ![Mantıksal uygulama tasarımcısı](./media/security-center-playbooks/security-center-playbooks-fig5.png)

## <a name="how-to-run-a-security-playbook-in-security-center"></a>Güvenlik Merkezi'ndeki güvenlik playbook'u nasıl çalıştırılır?

Düzenleme yapmak, diğer hizmetlerden daha fazla bilgi edinmek veya düzeltme yapmak istediğinizde Güvenlik Merkezi'ndeki bir güvenlik playbook'unu çalıştırabilirsiniz. Playbook'lara erişmek için aşağıdaki adımları izleyin:

1.  **Güvenlik Merkezi** panosunu açın.
2.  Sol bölmede **Tehdit Algılama**'nın altında **Güvenlik olayları ve uyarılar**'a tıklayın.

    ![Uyarılar](./media/security-center-playbooks/security-center-playbooks-fig6.png)

3.  Araştırmak istediğiniz uyarıya tıklayın.
4.  Uyarı sayfasının en üst kısmında yer alan **Playbook'ları çalıştır** düğmesine tıklayın.

    ![Playbook'u çalıştırma](./media/security-center-playbooks/security-center-playbooks-fig7.png)

5. Playbook'lar sayfasından çalıştırmak istediğiniz playbook'u seçip **Çalıştır** düğmesine tıklayın. Playbook'u tetiklemeden önce görmek isterseniz üzerine tıklayarak tasarımcıda açılmasını sağlayabilirsiniz.

    ![Playbook'lar](./media/security-center-playbooks/security-center-playbooks-fig13.png)

### <a name="history"></a>Geçmiş

Playbook'u çalıştırdıktan sonra daha önce yürütülen playbook'ların durumu hakkında daha fazla bilgi içeren önceki yürütme bilgilerine ve adımlarına da erişebilirsiniz. Geçmiş, uyarı bağlamında gösterilir. Başka bir deyişle bu sayfada gördüğünüz playbook geçmişi bu playbook'ta tetiklenen uyarıyla bağlantılıdır.

![Geçmiş](./media/security-center-playbooks/security-center-playbooks-fig16.png)

Belirli bir playbook'un yürütülmesi hakkında ayrıntılı bilgilere ulaşmak için playbook'a tıklayarak Mantıksal Uygulama çalıştırma sayfasının iş akışının tamamıyla birlikte açılmasını sağlayabilirsiniz.

![Ayrıntılar](./media/security-center-playbooks/security-center-playbooks-fig14.png)

Bu iş akışında her bir görevin yürütülmesi için geçen süreyi görebilir ve görevleri genişleterek sonuçları inceleyebilirsiniz.

### <a name="changing-an-existing-playbook"></a>Var olan bir playbook'u değiştirme

Var olan playbook'ları Güvenlik Merkezi'nde değiştirerek eylem veya koşul ekleyebilirsiniz. Bunu yapmak için tek yapmanız gereken Playbook'lar sekmesinde değiştirmek istediğiniz playbook'un adına tıklayarak Mantıksal Uygulama Tasarımcısı'nda açılmasını sağlamaktır.

> [!NOTE]
> Azure Logic App kullanarak kendi playbook'unuzu oluşturma hakkında daha fazla bilgi için bkz. [Bulut uygulamaları ile bulut hizmetleri arasında süreçleri otomatik hale getirmek için ilk mantıksal uygulama iş akışınızı oluşturma](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app).


## <a name="see-also"></a>Ayrıca bkz.
Bu belgede, Azure Güvenlik Merkezi'ndeki playbook'ları nasıl kullanacağınız hakkında bilgi edindiniz. Azure Güvenlik Merkezi hakkında daha fazla bilgi edinmek için şunlara bakın:

* [Azure Güvenlik Merkezi'nde güvenlik uyarılarını yönetme ve ele alma](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Güvenlik Merkezi’nde uyarıları yönetme ve güvenlik olaylarına yanıt vermeyi öğrenin.
* [Azure Güvenlik Merkezi'nde güvenlik durumunu izleme](security-center-monitoring.md). Azure kaynaklarınızı durumunu izleme hakkında bilgi edinin.
* [Azure Güvenlik Merkezi'ndeki güvenlik uyarılarını anlama](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Farklı güvenlik uyarısı türleri hakkında bilgi edinin.
* [Azure Güvenlik Merkezi Sorun Giderme Kılavuzu](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Güvenlik Merkezi’nde sık karşılaşılan sorunları gidermeyi öğrenin.
* [Azure Güvenlik Merkezi SSS](security-center-faq.md). Hizmet kullanımı ile ilgili sık sorulan soruları bulun.
* [Azure Güvenlik Blogu](https://blogs.msdn.com/b/azuresecurity/). Azure güvenliği ve uyumluluğu ile ilgili blog yazılarını bulun.
