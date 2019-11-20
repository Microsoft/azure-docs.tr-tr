---
title: Azure Güvenlik Merkezi 'nde güvenlik olaylarını yönetme | Microsoft Docs
description: Bu belge, güvenlik olaylarını yönetmek için Azure Güvenlik Merkezi 'ni kullanmanıza yardımcı olur.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: memildin
ms.openlocfilehash: 2a22039b3499f1ba0f2d7acb2e86a1991d35a380
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202129"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde güvenlik olaylarını yönetme

Güvenlik uyarılarını önceliklendirme ve araştırmak, en nitelikli Güvenlik analistleri için zaman alabilir ve birçok büyük olasılıkla nereden başlayabileceğinizi bilmelidir. Farklı [güvenlik uyarıları](security-center-managing-and-responding-alerts.md) arasındaki bilgileri bağlamak için [Analiz](security-center-detection-capabilities.md) özelliğini kullanan Güvenlik Merkezi, bir saldırı kampanyasını ve tüm ilgili uyarıları tek bir yerde görmenizi sağlayabilir; saldırganın hangi işlemleri yaptığını ve hangi kaynakların etkilendiğini hızlıca görebilirsiniz.

Bu konu başlığında Güvenlik Merkezi 'ndeki olaylar ve bunların uyarılarını düzeltme kullanımı açıklanmaktadır.

## <a name="what-is-a-security-incident"></a>Güvenlik olayı nedir?

Güvenlik Merkezi'nde bir güvenlik olayı, bir kaynağın [sonlandırma zinciri](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) desenleri ile hizalanan tüm uyarılarının toplamıdır. Olaylar [güvenlik uyarıları](security-center-managing-and-responding-alerts.md) listesinde görünür. Her oluşum hakkında daha fazla bilgi edinmenizi sağlayan ilgili uyarıları görüntülemek için bir olaya tıklayın.

## <a name="managing-security-incidents"></a>Güvenlik olaylarını yönetme

1. Güvenlik Merkezi panosunda **güvenlik uyarıları** kutucuğuna tıklayın. Olaylar ve uyarılar listelenir. Güvenlik olayı açıklamasının diğer uyarılardan farklı bir simgeye sahip olduğuna dikkat edin.

    ![Güvenlik olaylarını görüntüle](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Ayrıntıları görüntülemek için bir olaya tıklayın. **Güvenlik olayı algılanan** dikey pencerede daha fazla ayrıntı görüntülenir. **Genel bilgiler** bölümü, güvenlik uyarısının tetiklendiği konusunda bir öngörü sunabilir. Hedef kaynak, kaynak IP adresi (varsa), uyarı hala etkinse ve nasıl düzeltileceğine ilişkin öneriler gibi bilgileri görüntüler.  

    ![Azure Güvenlik Merkezi 'nde güvenlik olaylarına yanıt verme](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. Her uyarı hakkında daha fazla bilgi edinmek için bir uyarıya tıklayın. Güvenlik Merkezi tarafından önerilen düzeltme, güvenlik uyarısına göre farklılık gösterir.

   > [!NOTE]
   > Aynı uyarı, bir olayın parçası olarak ve tek başına bir uyarı olarak görülebilmesi için de bulunabilir.

    ![Uyarı ayrıntıları](./media/security-center-incident/security-center-incident-alert.png)

1. Her uyarı için verilen düzeltme adımlarını izleyin.

Uyarılar hakkında daha fazla bilgi için [güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md).

Aşağıdaki konular, kaynak türlerine göre farklı uyarılarda size rehberlik sağlar:

* [IaaS VM 'Leri ve sunucu uyarıları](security-center-alerts-iaas.md)
* [Yerel işlem uyarıları](security-center-alerts-compute.md)
* [Veri Hizmetleri uyarıları](security-center-alerts-data-services.md)

Aşağıdaki konularda, Azure üzerinde dağıtılan kaynaklar için ek koruma katmanları uygulamak amacıyla, güvenlik merkezi 'nin Azure altyapısıyla tümleştirerek topladığı farklı Telemetriyi nasıl kullandığı açıklanmaktadır:

* [Hizmet katmanı uyarıları](security-center-alerts-service-layer.md)
* [Azure Güvenlik ürünleriyle tümleştirme](security-center-alerts-integration.md)

## <a name="see-also"></a>Ayrıca bkz.
Bu belgede, Güvenlik Merkezi'nde güvenlik olayı özelliğini nasıl kullanacağınız hakkında bilgi edindiniz. Güvenlik Merkezi hakkında daha fazla bilgi edinmek için şunlara bakın:

* [Azure Güvenlik Merkezi 'Nde güvenlik uyarıları](security-center-alerts-overview.md).
* [Güvenlik uyarılarını yönetme](security-center-managing-and-responding-alerts.md)
* [Azure Güvenlik Merkezi Planlama ve İşlemler Kılavuzu](security-center-planning-and-operations-guide.md)
* [Azure Güvenlik Merkezi ile ilgili SSS](security-center-faq.md) - Hizmeti kullanımı ile ilgili sık sorulan soruları bulabilirsiniz.
* [Azure Güvenlik blogu](https://blogs.msdn.com/b/azuresecurity/) - Azure güvenliği ve uyumluluğu ile ilgili blog yazılarını bulabilirsiniz
