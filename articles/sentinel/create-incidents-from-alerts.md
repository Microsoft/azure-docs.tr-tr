---
title: Azure Sentinel 'de uyarılardan olay oluşturma | Microsoft Docs
description: Azure Sentinel 'de uyarılardan olay oluşturmayı öğrenin.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 722feb29a08cd5e1113eae197fbcae9769ed5550
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029770"
---
# <a name="automatically-create-incidents-from-microsoft-security-alerts"></a>Microsoft Güvenlik uyarılarından otomatik olarak olay oluştur

Microsoft Cloud App Security ve Azure Gelişmiş tehdit koruması gibi Azure Sentinel 'e bağlı Microsoft Güvenlik çözümlerinde tetiklenen uyarılar, Azure Sentinel 'de otomatik olarak olay oluşturmaz. Varsayılan olarak, bir Microsoft çözümünü Azure Sentinel 'e bağladığınızda, söz konusu hizmette oluşturulan herhangi bir uyarı Azure Sentinel çalışma alanınızdaki güvenlik uyarısı tablosunda Azure Sentinel 'de ham veri olarak depolanır. Daha sonra bu verileri, Sentinel 'e bağlandığınız diğer ham veriler gibi kullanabilirsiniz.

Azure Sentinel 'i, bağlı bir Microsoft Güvenlik çözümünde her uyarı tetiklendiğinde bu makaledeki yönergeleri uygulayarak otomatik olarak olaylar oluşturacak şekilde yapılandırabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
Güvenlik hizmeti uyarılarından olay oluşturmayı etkinleştirmek için [Microsoft güvenlik çözümlerini bağlamanız](connect-data-sources.md#data-connection-methods) gerekir.

## <a name="using-microsoft-security-incident-creation-analytic-rules"></a>Microsoft güvenlik olayı oluşturma analitik kurallarını kullanma

Hangi bağlı Microsoft güvenlik çözümlerinin Azure Sentinel olaylarını otomatik olarak gerçek zamanlı olarak oluşturması gerektiğini seçmek için Azure Sentinel 'de bulunan yerleşik kuralları kullanın. Ayrıca, Microsoft güvenlik çözümü tarafından oluşturulan uyarıların Azure Sentinel 'de olay oluşturması gereken filtre için daha belirgin seçenekler tanımlamak üzere kuralları düzenleyebilirsiniz. Örneğin, yalnızca yüksek öneme sahip Azure Güvenlik Merkezi uyarılarından Azure Sentinel olaylarını otomatik olarak oluşturmayı tercih edebilirsiniz.

1. Azure Sentinel altında Azure portal **analiz**' yı seçin.

1. Tüm yerleşik analitik kuralları görmek için **kural şablonları** sekmesini seçin.

    ![Kural şablonları](media/incidents-from-alerts/rule-templates.png)

1. Kullanmak istediğiniz **Microsoft Güvenlik** Analytics kural şablonunu seçin ve **kural oluştur**' a tıklayın.

    ![Güvenlik analizi kuralı](media/incidents-from-alerts/security-analytics-rule.png)

1. Kural ayrıntılarını değiştirebilir ve uyarı önem derecesine göre ya da uyarının adında bulunan metin ile olayları oluşturacak uyarıları filtrelemeye seçim yapabilirsiniz.  
      
    Örneğin, **Microsoft güvenlik hizmeti** alanında **Azure Güvenlik Merkezi** ' ni seçip **önem derecesine göre filtrele** alanında **yüksek** ' i seçerseniz, yalnızca yüksek önem derecesine sahip Azure Güvenlik Merkezi uyarıları otomatik olarak olay oluşturur Azure Sentinel 'de.  

    ![Kural oluşturma Sihirbazı](media/incidents-from-alerts/create-rule-wizard.png)

1. Ayrıca, **+ Oluştur** ' a tıklayıp **Microsoft olay oluşturma kuralı**' nı seçerek farklı Microsoft güvenlik hizmetlerinden gelen uyarıları filtreleyen yeni bir **Microsoft Güvenlik** kuralı oluşturabilirsiniz.

    ![Olay oluşturma kuralı](media/incidents-from-alerts/incident-creation-rule.png)

  **Microsoft güvenlik hizmeti** türü başına birden fazla **Microsoft Güvenlik** çözümleme kuralı oluşturabilirsiniz. Her kural bir filtre olarak kullanıldığından, bu yinelenen olaylar oluşturmaz. Bir uyarı birden fazla **Microsoft Güvenlik** analitik kuralıyla eşleşiyorsa bile, yalnızca bir Azure Sentinel olayı oluşturur.

## <a name="enable-incident-generation-automatically-during-connection"></a>Bağlantı sırasında olay oluşturmayı otomatik olarak etkinleştir
 Bir Microsoft güvenlik çözümünü bağladığınızda, güvenlik çözümünden gelen uyarıların otomatik olarak Azure Sentinel 'de olay oluşturmasını isteyip istemediğinizi seçebilirsiniz.

1. Bir Microsoft güvenlik çözümü veri kaynağını bağlayın. 

   ![Güvenlik Olayları oluştur](media/incidents-from-alerts/generate-security-incidents.png)

1. Olayları **Oluştur** altında, bağlı güvenlik hizmetinde oluşturulan uyarılardan olayları otomatik olarak oluşturan varsayılan analitik kuralı etkinleştirmek için **Etkinleştir** ' i seçin. Daha sonra bu kuralı **analiz** ve ardından **etkin kurallar**altında düzenleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Azure Sentinel ile çalışmaya başlamak için Microsoft Azure aboneliğine sahip olmanız gerekir. Aboneliğiniz yoksa [ücretsiz deneme sürümü](https://azure.microsoft.com/free/) için kaydolabilirsiniz.
- [Verilerinizi Azure Sentinel 'e](quickstart-onboard.md) [ekleme ve verilerinize ilişkin görünürlük alma ve olası tehditler](quickstart-get-visibility.md)hakkında bilgi edinin.
