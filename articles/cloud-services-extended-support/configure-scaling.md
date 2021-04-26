---
title: Azure Cloud Services için ölçeklendirmeyi yapılandırma (genişletilmiş destek)
description: Azure Cloud Services için ölçeklendirme seçeneklerini etkinleştirme (genişletilmiş destek)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: autoscale
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: dab3411319f40a111144a3e1c1f02f1b67d011b0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935660"
---
# <a name="configure-scaling-options-with-azure-cloud-services-extended-support"></a>Azure Cloud Services ölçeklendirme seçeneklerini yapılandırma (genişletilmiş destek) 

Koşullar, ölçeklendirmek ve kapatmak için Cloud Services (genişletilmiş destek) dağıtımlarını etkinleştirmek üzere yapılandırılabilir. Bu koşullar CPU kullanımı, disk yükü ve ağ yüküne bağlı olabilir. 

Bulut hizmeti dağıtımlarınızın ölçeğini yapılandırırken aşağıdaki bilgileri göz önünde bulundurun:
- Ölçeklendirme, temel kullanımı etkiler. Daha büyük rol örnekleri daha fazla çekirdek tüketir ve yalnızca aboneliğinizin çekirdek limiti dahilinde ölçeklendirebilirsiniz. Daha fazla bilgi için bkz. [Azure aboneliği ve hizmet limitleri, kotalar ve kısıtlamalar](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Sıra mesajlaşma eşiğini temel alan ölçekleme desteklenir. Daha fazla bilgi için bkz. [Azure kuyruk depolamayı kullanmaya başlama](../storage/queues/storage-dotnet-how-to-use-queues.md).
- Bulut hizmetinizin (genişletilmiş destek) uygulamalarınızın yüksek oranda kullanılabilir olmasını sağlamak için iki veya daha fazla rol örneğiyle dağıtım yapıldığından emin olun.
- Özel otomatik ölçeklendirme yalnızca tüm roller **kullanılabilir durumda olduğunda** gerçekleşebilir.

## <a name="configure-and-manage-scaling"></a>Ölçeklendirmeyi yapılandırma ve yönetme

1. [Azure portalında](https://portal.azure.com) oturum açın. 
2. Ölçeklendirmeyi yapılandırmak istediğiniz bulut hizmeti (genişletilmiş destek) dağıtımını seçin. 
3. **Ölçek** dikey penceresini seçin. 

    :::image type="content" source="media/enable-scaling-1.png" alt-text="Görüntüde Uzak Masaüstü seçeneğinin seçilmesi gösterilmektedir Azure portal":::

4. Sayfa, ölçeklendirmenin yapılandırılabileceği tüm rollerin bir listesini görüntüler. Yapılandırmak istediğiniz rolü seçin. 
5. Yapılandırmak istediğiniz ölçek türünü seçin
    - **El ile ölçeklendirme** , örneklerin mutlak sayısını ayarlar.
        1. **El ile ölçek** seçin.
        2. Ölçeği genişletmek veya daraltmak istediğiniz örneklerin sayısını girin.
        3. **Kaydet**’i seçin.

        :::image type="content" source="media/enable-scaling-2.png" alt-text="Görüntüde Azure portal el ile ölçeklendirmeyi ayarlama gösterilmektedir":::

        4. Ölçeklendirme işlemi hemen başlayacaktır. 
        
    - **Özel otomatik ölçeklendirme** , ölçeklendirmenin ne kadar veya ne kadar küçük olduğunu belirleyen kurallar ayarlamanıza olanak sağlar. 
        1. **Özel otomatik ölçeklendirme** seçin
        2. Ölçüm veya örnek sayısına göre ölçeklendirmeye seçin.

        :::image type="content" source="media/enable-scaling-3.png" alt-text="Görüntüde Azure portal özel otomatik ölçeklendirmeyi ayarlama gösterilmektedir":::

        3. Bir ölçüme göre ölçekleme yaptıysanız, istenen ölçeklendirme sonuçlarına ulaşmak için gereken kuralları ekleyin.

        :::image type="content" source="media/enable-scaling-4.png" alt-text="Görüntü, Azure portal özel otomatik ölçeklendirme kuralları ayarlamayı gösterir":::

        4. **Kaydet**’i seçin.
        5. Ölçek işlemleri bir kural tetiklendikten hemen sonra başlayacaktır.
        
6. **Ölçek** sekmesini seçerek dağıtımlarınıza uygulanan mevcut ölçeklendirme kurallarını görüntüleyebilir veya ayarlayabilirsiniz.

    :::image type="content" source="media/enable-scaling-5.png" alt-text="Görüntü, Azure portal varolan ölçekleme kuralını ayarlamayı gösterir":::

## <a name="next-steps"></a>Sonraki adımlar 
- Cloud Services için [dağıtım önkoşullarını](deploy-prerequisite.md) gözden geçirin (genişletilmiş destek).
- Cloud Services için [sık sorulan soruları](faq.md) gözden geçirin (genişletilmiş destek).
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [şablon](deploy-template.md) veya [Visual Studio](deploy-visual-studio.md)kullanarak bir bulut hizmeti (genişletilmiş destek) dağıtın.