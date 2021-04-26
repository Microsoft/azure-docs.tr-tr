---
title: Windows sanal masaüstü Kullanıcı bağlantısı gecikmesi-Azure
description: Windows sanal masaüstü kullanıcıları için bağlantı gecikme süresi.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: 2fa03fa948c7768f5e74cc68b528e847c5351273
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447871"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Windows sanal masaüstü 'nde Kullanıcı bağlantı gecikmesini belirleme

Windows sanal masaüstü küresel olarak kullanılabilir. Yöneticiler, istedikleri Azure bölgesinde sanal makineler (VM) oluşturabilir. Bağlantı gecikmesi, kullanıcıların ve sanal makinelerin konumuna göre değişir. Windows Sanal Masaüstü Hizmetleri, gecikme süresini artırmak için sürekli olarak yeni coğrafi bir şekilde kullanıma sunulacaktır.

[Windows sanal masaüstü deneyimi Estimator Aracı](https://azure.microsoft.com/services/virtual-desktop/assessment/) , sanal makinelerinizin gecikmesini iyileştirmek için en iyi konumu belirlemenize yardımcı olabilir. Windows sanal masaüstü yeni alanlara kaydolurken en iyi konumun değişmediğinden emin olmak için aracı iki ile üç ayda bir kullanmanızı öneririz.

## <a name="interpreting-results-from-the-windows-virtual-desktop-experience-estimator-tool"></a>Windows sanal masaüstü deneyimi Estimator aracının sonuçlarını yorumlama

Windows sanal masaüstü 'nde, 150 MS 'ye kadar gecikme süresi, işleme veya video içermeyen Kullanıcı deneyimini etkilemez. 150 MS ile 200 MS arasındaki gecikme süreleri metin işleme için ince olmalıdır. 200 MS üzerinde gecikme, Kullanıcı deneyimini etkileyebilir. 

Ayrıca, Windows sanal masaüstü bağlantısı, kullanıcının hizmeti kullandığı makinenin internet bağlantısına bağlıdır. Kullanıcılar, aşağıdaki durumlardan birinde bağlantıyı kaybedebilir veya giriş gecikmesi yaşayabilir:

 - Kullanıcının kararlı bir yerel internet bağlantısı yoktur ve gecikme süresi 200 MS 'den fazla olur.
 - Ağ doygun veya hız sınırlı.

Kullanıcılarınıza mümkün olduğunca yakın VM konumları seçmenizi öneririz. Örneğin, Kullanıcı Hindistan 'da bulunuyorsa ancak VM Birleşik Devletler, genel kullanıcı deneyimini etkileyecek gecikme süresi olacaktır. 

## <a name="azure-front-door"></a>Azure Front Door

Windows sanal masaüstü, Kullanıcı bağlantısını kaynak IP adresine bağlı olarak en yakın Windows sanal masaüstü ağ geçidine yönlendirmek için [Azure ön kapısını](https://azure.microsoft.com/services/frontdoor/) kullanır. Windows sanal masaüstü, istemcinin seçtiği Windows sanal masaüstü ağ geçidini her zaman kullanır.

## <a name="next-steps"></a>Sonraki adımlar

- En iyi gecikme süresinin en iyi konumunu denetlemek için bkz. [Windows sanal masaüstü deneyimi Estimator Aracı](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- Fiyatlandırma planları için bkz. [Windows sanal masaüstü fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Windows sanal masaüstü dağıtımınızı kullanmaya başlamak için [öğreticimize](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace)göz atın.
