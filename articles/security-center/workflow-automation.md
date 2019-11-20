---
title: Azure Güvenlik Merkezi 'nde iş akışı Otomasyonu (Önizleme) | Microsoft Docs
description: Azure Güvenlik Merkezi 'nde iş akışları oluşturmayı ve otomatikleştirmeyi öğrenin
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 1abfd8af7e0ef18d4e7bcf05b4726c5d0bcb0a84
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151703"
---
# <a name="workflow-automation-preview"></a>Workflow Otomasyonu (Önizleme)

Her güvenlik programı, olay yanıtı için birden çok iş akışı içerir. Bu işlemler ilgili hissedarları bildirmeyi, bir değişiklik yönetimi işlemini başlatmayı ve belirli düzeltme adımlarını uygulamayı içerebilir. Güvenlik uzmanları, yaptığınız gibi bu yordamların pek çok adımını otomatikleştirmenizi önerir. Otomasyon ek yükü azaltır. Ayrıca işlem adımlarının hızlı, tutarlı bir şekilde ve önceden tanımlanmış gereksinimlerinize göre hızla yapılmasını sağlayarak güvenliği geliştirebilir.

Bu makalede, Azure Güvenlik Merkezi 'nin Iş akışı Otomasyonu özelliği (Önizleme) açıklanmaktadır. Bu önizleme özelliği, güvenlik uyarıları ve önerileri üzerinde Logic Apps tetikleyebilir. Örneğin, bir uyarı oluştuğunda Güvenlik Merkezi 'nin belirli bir kullanıcıyı e-posta ile e-posta olarak istemeniz gerekebilir. Ayrıca, [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)kullanarak Logic Apps oluşturmayı öğreneceksiniz.

> [!NOTE]
> Daha önce kenar çubuğunda playbooks (Önizleme) görünümünü kullandıysanız, yeni Iş akışı Otomasyonu (Önizleme) sayfasında genişletilmiş işlevlerle aynı özellikleri bulacaksınız.


## <a name="requirements"></a>Gereksinimler

* Azure Logic Apps iş akışlarıyla çalışmak için aşağıdaki Logic Apps rollere/izinlere sahip olmanız gerekir:

    * [Mantıksal uygulama işleci](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) izinleri gerekiyor veya mantıksal uygulama okuma/tetikleme erişimi (Bu rol, mantıksal uygulamalar oluşturamaz veya düzenleyemez; yalnızca var olanları *Çalıştır* )

    * Mantıksal uygulama oluşturma ve değiştirme için [mantıksal uygulama katılımcısı](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor) izinleri gereklidir

* Logic App bağlayıcıları kullanmak istiyorsanız, ilgili hizmetlerinde oturum açmak için ek kimlik bilgilerine ihtiyacınız olabilir (örneğin, Outlook/takımlar/bolluk örnekleri)


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Bir mantıksal uygulama oluşturun ve ne zaman otomatik olarak çalışacağını tanımlayın 

1. Güvenlik Merkezi 'nin kenar çubuğundan **Iş akışı Otomasyonu (Önizleme)** seçeneğini belirleyin.

    [iş akışı otomasyonları ![listesi](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    Bu sayfadan yeni otomasyon kuralları oluşturabilir, Ayrıca var olanları etkinleştirebilir, devre dışı bırakabilir veya silebilirsiniz.  
1. Yeni bir iş akışı tanımlamak için **iş akışı Otomasyonu Ekle**' ye tıklayın. 

    Yeni otomasyonuna yönelik seçenekleri içeren bir bölme açılır. Buraya şunu girebilirsiniz:
    1. Otomasyon için bir ad ve açıklama.
    1. Bu otomatik iş akışını başlatacak Tetikleyiciler. Örneğin, "SQL" içeren bir güvenlik uyarısı oluşturulduğunda mantıksal uygulamanızın çalışmasını isteyebilirsiniz.
    1. Tetikleyici koşullarınız karşılandığında çalıştırılacak mantıksal uygulama. 

        [iş akışı otomasyonları ![listesi](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. Mantıksal uygulama oluşturma işlemini başlatmak için Eylemler bölümünden **Yeni bir tane oluştur ' a** tıklayın.

    Azure Logic Apps için yönlendirilirsiniz.

    [Yeni bir mantıksal uygulama oluşturma ![](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Bir ad, kaynak grubu ve konum girin ve **Oluştur**' a tıklayın.

1. Yeni mantıksal uygulamanızda, güvenlik kategorisinden yerleşik, önceden tanımlanmış şablonlar arasından seçim yapabilirsiniz. Ya da bu işlem tetiklendiğinde gerçekleşecek özel olay akışını tanımlayabilirsiniz.

    Mantıksal uygulama tasarımcısında, güvenlik merkezi bağlayıcılarından aşağıdaki Tetikleyiciler desteklenir:

    * **Bir Azure Güvenlik Merkezi önerisi oluşturulduğunda veya tetiklendiğinde (Önizleme)**
    * **Bir Azure Güvenlik Merkezi uyarısı oluşturulduğunda veya tetiklendiğinde (Önizleme)**
    
    > [!NOTE]
    > Eski tetikleyiciyi kullanıyorsanız "bir Azure Güvenlik Merkezi uyarısıyla bir yanıt tetiklendiğinde", Logic Apps Iş akışı Otomasyonu özelliği tarafından başlatılmaz. Bunun yerine, yukarıda bahsedilen tetikleyicilerden birini kullanın. 

    [Örnek mantıksal uygulama ![](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Mantıksal uygulamanızı tanımladıktan sonra, iş akışı Otomasyon tanımı bölmesine ("iş akışı Otomasyonu Ekle") dönün. Yeni mantıksal uygulamanızın seçim için kullanılabilir olduğundan emin olmak için **Yenile** ' ye tıklayın.

    ![Yenile](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Mantıksal uygulamanızı seçin ve Otomasyonu kaydedin. Mantıksal uygulama açılan listesinin yalnızca yukarıda bahsedilen destekleyici Güvenlik Merkezi bağlayıcıları olan Logic Apps gösterdiğini unutmayın.


## <a name="manually-trigger-a-logic-app"></a>Mantıksal uygulamayı el ile tetikleme

Ayrıca, güvenlik önerisi görüntülenirken Logic Apps el ile de çalıştırabilirsiniz.

Mantıksal uygulamayı el ile çalıştırmak için bir öneri açın ve mantıksal uygulama tetikleme (Önizleme) seçeneğine tıklayın:

[Mantıksal uygulama ![El Ile tetikleyin](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede Logic Apps oluşturma, güvenlik merkezi 'nde el ile çalıştırma ve yürütmeyi otomatikleştirme hakkında bilgi edindiniz. 

Diğer ilgili malzemeler için aşağıdaki makalelere bakın: 

- [Azure Güvenlik Merkezi 'nde güvenlik önerileri](security-center-recommendations.md)
- [Azure Güvenlik Merkezi'ndeki güvenlik uyarıları](security-center-alerts-overview.md)
- [Azure Logic Apps hakkında](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Logic Apps bağlayıcıları](https://docs.microsoft.com/connectors/)
