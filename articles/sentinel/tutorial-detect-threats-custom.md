---
title: Azure Sentinel ile şüpheli tehditleri algılamak için özel analitik kurallar oluşturma | Microsoft Docs
description: Azure Sentinel ile şüpheli tehditleri algılamak için özel analitik kurallar oluşturmayı öğrenmek için bu öğreticiyi kullanın.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 9c4ba09c7e3eca4482ed56b0b337124aeec5b838
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928245"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Öğretici: şüpheli tehditleri algılamak için özel analitik kurallar oluşturma

Veri kaynaklarınızı Azure Sentinel 'e [bağladıktan](quickstart-onboard.md) sonra, ortamınızda belirli kriterleri arayabilmeniz ve ölçütler eşleştirildiği zaman bir olay oluşturarak bu kuralları araştırabilmeniz için özel kurallar oluşturabilirsiniz. Bu öğretici, Azure Sentinel ile tehditleri algılamak için özel kurallar oluşturmanıza yardımcı olur.

Bu öğretici, Azure Sentinel ile tehditleri algılamanıza yardımcı olur.
> [!div class="checklist"]
> * Analitik kuralları oluşturma
> * Tehdit yanıtlarını otomatikleştirin

## <a name="create-custom-analytic-rules"></a>Özel analitik kurallar oluşturma

Ortamınızda şüpheli olan tehditler ve bozukluklar için arama yapmanıza yardımcı olacak özel analitik kurallar oluşturabilirsiniz. Bu kural, tehditleri önceliklendirmenize, araştırmanıza ve düzeltebilmeniz için hemen bilgilendirildiğinizden emin olur.

1. Azure Sentinel altında Azure portal **analiz**' yı seçin.

1. Üstteki menü çubuğunda **+ Oluştur** ' u seçin ve **zamanlanan sorgu kuralı**' nı seçin. Bu, **özel kural oluşturma Sihirbazı 'nı**açar.

    ![Zamanlanmış sorgu oluştur](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. **Genel** sekmesinde açıklayıcı bir ad ve açıklama sağlayın. **Uyarı önem derecesini** gerektiği şekilde ayarlayın. Kuralı oluşturduğunuzda, onu oluşturmayı bitirdikten sonra hemen çalışmasına neden olacak şekilde etkinleştirebilirsiniz. Devre dışı olarak oluşturursanız, kural **etkin kurallar** sekmesine eklenir ve ihtiyacınız olduğunda bu seçeneği etkinleştirebilirsiniz.

    ![Özel analitik kural oluşturmaya başlayın](media/tutorial-detect-threats-custom/general-tab.png)

1. **Ayarlar** sekmesinde doğrudan bir sorgu yazabilir veya Log Analytics sorgu oluşturabilir ve sonra **arama sorgusu** alanına yapıştırabilirsiniz. Sorgunuzu değiştirirken ve yapılandırırken, Azure Sentinel sorgu sonuçlarının sağ taraftaki **sonuçlar önizleme** penceresinde benzetimini yapar. Bu, oluşturmakta olduğunuz uyarının belirli bir zaman aralığında ne kadar veri üretilecektir hakkında fikir edinmenizi sağlar. Bu miktar **, son verilerden her veri**için **çalışma sorgusu** ve arama için ayarlandığınıza bağlıdır. Bu durumda, uyarılarınızın uyarıları çok sık tetikleyebilmesini görürseniz, sonuçların sayısını, ortalama taban çizgisinin üstünde olacak şekilde ayarlayabilirsiniz.

   ![Azure Sentinel 'de sorgu oluştur](media/tutorial-detect-threats-custom/settings-tab.png)

   İşte Azure etkinliğinde anormal sayıda kaynak oluşturulduğunda sizi uyaran örnek bir sorgu.

    `AzureActivity
    \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    \| where ActivityStatus == "Succeeded"
    \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

   > [!NOTE]
   > Sorgu uzunluğu 1 ila 10.000 karakter arasında olmalıdır ve "Search \*" veya "Union \*" içeremez.

    1. **Sorgu zamanlaması**' nın altında, aşağıdaki parametreleri ayarlayın:

        1.  Sorgunun her 5 dakikada bir veya bir günde yaklaşık olarak ne sıklıkta çalıştığı hakkında **sıklığı** ayarlamak için **çalışma sorgusunu her** zaman ayarlayın.

        1.  Sorgunun ne kadar veri üzerinde çalıştığı hakkında zaman penceresini denetlemek için **en son Içinden arama verilerini** ayarlayın. örneğin, 60 dakikalık veriler arasında her saat çalışabilir.

        1. Uyarı oluşturulduktan sonra yalnızca uyarıyı bir kez almak istiyorsanız, **Uyarı oluşturulduktan sonra sorguyu çalıştırmayı durduracak** şekilde, Azure Sentinel 'i ayarlayabilirsiniz. Sorgunun çalışmayı durdurması gereken, 24 saate kadar olan pencereyi ayarlamanız gerekir.

    1. **Uyarı tetikleyicisi**altında uyarı tetikleyicisi koşullarını tanımlayın. **Varlık eşleme**altında, Sorgunuzdaki sütunları Azure Sentinel tarafından tanınan varlık alanlarıyla eşleyebilirsiniz. Her alan için Log Analytics oluşturduğunuz sorgudaki ilgili sütunu uygun varlık alanına eşleyin. Her varlık, SID ve GUID gibi birden çok alan içerir. Varlığı yalnızca üst düzey varlık değil, herhangi bir alana göre eşleyebilirsiniz.

1.  **Yanıtları otomatikleştir** sekmesinde, özel kural tarafından bir uyarı oluşturulduğunda otomatik olarak çalıştırmak istediğiniz tüm PlayBook 'ları seçin. PlayBook 'ları oluşturma ve otomatikleştirme hakkında daha fazla bilgi için bkz. [tehditlere yanıt verme](tutorial-respond-threats-playbook.md).

    ![Azure Sentinel 'de tehditlere yanıt otomatik hale getirme](media/tutorial-detect-threats-custom/response-automation-custom.png)

1. Yeni uyarı kuralınızın tüm ayarlarını gözden geçirmek için **gözden geçir** ' i seçin ve ardından **Uyarı kuralınızı başlatmak için Oluştur**' u seçin.

   ![Özel sorgunuzu gözden geçirin](media/tutorial-detect-threats-custom/review-tab.png)

1.  Uyarı oluşturulduktan sonra, **etkin kurallar**altındaki tabloya özel bir kural eklenir. Bu listeden her kuralı etkinleştirebilir, devre dışı bırakabilir veya silebilirsiniz.

1.  Oluşturduğunuz uyarı kurallarının sonuçlarını görüntülemek için **Olaylar** sayfasına gidin; burada, olayları önceliklendirebilirsiniz, [araştırın](tutorial-investigate-cases.md)ve tehditleri düzeltin.


> [!NOTE]
> Azure Sentinel 'de oluşturulan uyarılar [Microsoft Graph güvenliği](https://aka.ms/securitygraphdocs)aracılığıyla kullanılabilir. Daha fazla bilgi için [Microsoft Graph güvenlik uyarıları belgelerine](https://aka.ms/graphsecurityreferencebetadocs)bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Sentinel kullanarak tehditleri algılamaya nasıl başlacağınız hakkında daha fazla öğrenirsiniz.

Tehditlere yönelik yanıtlarınızı otomatik hale getirmeyi öğrenmek için [Azure Sentinel 'de otomatik tehdit yanıtları ayarlayın](tutorial-respond-threats-playbook.md).

