---
title: Dynamics 365 çözüm hazırlığı
description: Paketleme, bileşenleri yükleme ve kaldırma çerçevesi
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricarod.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: bcb3bb63f305aeb98efda3baf0f6661bd7f67a7c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824241"
---
# <a name="dynamics-365-solution-preparation"></a>Dynamics 365 çözüm hazırlığı

Dynamics 365 solul sistemi, belirli iş işlevlerini sağlayan bileşenleri paketleme, yükleme ve kaldırmaya yönelik bir çerçevedir. Çözümler, ISV 'Ler ve diğer Microsoft Dynamics 365 iş ortakları tarafından oluşturdukları uzantıları dağıtmak için kullanılır.

Mevcut bir Dynamics 365 (xRM) ISV iseniz, büyük olasılıkla zaten yönetilen bir çözüm oluşturmuş ve bir çözüm. zip dosyasına sahip olabilirsiniz. Çözümünüzde, lütfen "görünen ad" ve "Açıklama" alanlarının müşterilerin görmesini istediğiniz şeyi yansıttığından emin olun. Bunlar, CRM Online Yönetim merkezinde görüntülenir.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Note:** Aşağıdaki paket örneğinde çözüm adının "SampleSolution. zip" olduğunu varsayacağız_

Yeni bir ISV iseniz, burada çözüm oluşturma hakkında daha fazla bilgi edinebilirsiniz: [https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Çözümünüz destekleme verileri gerektiriyorsa:

* Test ortamınızda örnek verileri oluşturma
* Verilerinize ilişkin karşılaştırma kurallarına sahip bir şema oluşturmak için yapılandırma geçiş aracını kullanın.
* Yapılandırma şemanızı proje dosyalarınıza kaydedin. Yapılandırma verilerinizi güncelleştirirseniz bu, daha sonra gerekecektir.
* Yapılandırma verilerinizi dışarı aktarın. Dışarı aktarma dosyasına dışarı aktarma için anlamlı olan bir ad vermeyi unutmayın.
