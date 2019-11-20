---
title: Azure Sentinel ile uyarıları araştırın | Microsoft Docs
description: Azure Sentinel ile uyarıları araştırmaya yönelik bilgi edinmek için bu öğreticiyi kullanın.
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
ms.openlocfilehash: 6d61b94a50716966ac0b3c75551ddd9d8e291965
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72024464"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Öğretici: kullanıma hazır tehditleri algılama


> [!IMPORTANT]
> Kullanıma hazır tehdit algılama Şu anda genel önizlemededir.
> Bu özellik, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Veri kaynaklarınızı  Azure Sentinel 'e [bağladıktan](quickstart-onboard.md)sonra, şüpheli bir sorun olduğunda bildirim almak istersiniz. Bunu yapmanız için Azure Sentinel, kullanıma hazır yerleşik şablonlar sağlar. Bu şablonlar, Microsoft 'un, bilinen tehditler, yaygın saldırı vektörleri ve şüpheli etkinlik yükseltme zincirlerine göre güvenlik uzmanı ve analistlerinin ekibi tarafından tasarlanmıştır. Bu şablonları etkinleştirdikten sonra, ortamınız genelinde şüpheli görünen tüm etkinlikleri otomatik olarak arar. Birçok şablon, gereksinimlerinize göre, etkinlikleri aramak veya filtrelemek için özelleştirilebilir. Bu şablonlar tarafından oluşturulan uyarılar, ortamınızda atayabileceğiniz ve araştırabilmeniz için olaylar oluşturur.

Bu öğretici, Azure Sentinel ile tehditleri algılamanıza yardımcı olur:

> [!div class="checklist"]
> * Kullanıma hazır algılamaları kullanma
> * Tehdit yanıtlarını otomatikleştirin

## <a name="about-out-of-the-box-detections"></a>Kullanıma hazır algılamalar hakkında

Kullanıma hazır tüm algılamaları görüntülemek için analizler **' e ve ardından** **kural şablonları**' na gidin. Bu sekme, tüm Azure Sentinel yerleşik kurallarını içerir.

   ![Azure Sentinel ile tehditleri bulmak için yerleşik algılamaları kullanın](media/tutorial-detect-built-in/view-oob-detections.png)

Aşağıdaki şablon türleri kullanılabilir:

- **Microsoft** güvenlik-Microsoft güvenlik şablonları, diğer Microsoft Güvenlik çözümlerinde oluşturulan uyarılardan gerçek zamanlı olarak Azure Sentinel olaylarını otomatik olarak oluşturur. Benzer mantığa sahip yeni kurallar oluşturmak için Microsoft güvenlik kurallarını şablon olarak kullanabilirsiniz. Güvenlik kuralları hakkında daha fazla bilgi için bkz. [Microsoft Güvenlik uyarılarından olayları otomatik olarak oluşturma](create-incidents-from-alerts.md).
- **Fusion teknolojisine** göre, Azure Sentinel 'de gelişmiş çok aşamalı saldırı algılama, birden fazla ürün genelinde çok sayıda düşük kaliteli uyarı ve olayla Yüksek uygunluğa ve eyleme geçebilen ölçeklenebilir makine öğrenimi algoritmaları kullanır olaylara. Fusion varsayılan olarak etkindir. Logic Hidden olduğundan, bunu birden fazla kural oluşturmak için şablon olarak kullanamazsınız.
- **Machine Learning davranış analizi** -bu şablonlar, özel Microsoft makine öğrenimi algoritmalarını temel alır. bu nedenle, nasıl çalıştıkları ve ne zaman çalıştıkları hakkında iç mantığı göremezsiniz. Logic Hidden olduğundan, bunu birden fazla kural oluşturmak için şablon olarak kullanamazsınız.
-   **Zamanlanmış** – zamanlanmış analitik kurallar, Microsoft güvenlik uzmanları tarafından yazılan zamanlanmış sorgulardır. Sorgu mantığını görebilir ve üzerinde değişiklik yapabilirsiniz. Benzer mantığa sahip yeni kurallar oluşturmak için, zamanlanmış kuralları şablon olarak kullanabilirsiniz.

## <a name="use-out-of-the-box-detections"></a>Kullanıma hazır algılamaları kullanma

1. Yerleşik bir şablon kullanmak için, bu şablonu temel alan yeni bir etkin kural oluşturmak üzere **kural oluştur** ' a tıklayın. Her giriş, otomatik olarak denetlenen gerekli veri kaynaklarının bir listesini içerir ve bu, **oluşturma kuralının** devre dışı bırakılmakta kaynaklanabilir.
  
   ![Azure Sentinel ile tehditleri bulmak için yerleşik algılamaları kullanın](media/tutorial-detect-built-in/use-built-in-template.png)
 
1. Bu, seçilen şablona göre kural oluşturma Sihirbazı ' nı açar. Tüm ayrıntılar, yeniden doldurulur ve **zamanlanan kurallar** veya **Microsoft güvenlik kuralları**için, mantığı kuruluşunuza daha iyi uyacak şekilde özelleştirebilir veya yerleşik şablonu temel alan ek kurallar oluşturabilirsiniz. Kural oluşturma Sihirbazı 'ndaki adımları uyguladıktan ve şablonu temel alan bir kural oluşturmayı tamamladıktan sonra, yeni kural **etkin kurallar** sekmesinde görünür.

Sihirbazdaki alanlarla ilgili daha fazla bilgi için bkz. [öğretici: şüpheli tehditleri algılamak için özel analitik kurallar oluşturma](tutorial-detect-threats-custom.md).



## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, Azure Sentinel kullanarak tehditleri algılamaya nasıl başlacağınız hakkında daha fazla öğrenirsiniz. 

Tehditlere yönelik yanıtlarınızı otomatik hale getirmeyi öğrenmek için [Azure Sentinel 'de otomatik tehdit yanıtları ayarlayın](tutorial-respond-threats-playbook.md).

