---
title: Team Data Science Işlem projelerinin ilerlemesini izleyin
description: Veri bilimi Grup yöneticileri, ekip liderleri ve proje müşteri adayları, bir veri bilimi projesinin ilerlemesini takip edebilir.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/26/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 28bd3e558294c000ba65a1c60fe227bbae7e82dd
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244082"
---
# <a name="track-the-progress-of-data-science-projects"></a>Veri bilimi projelerinin ilerlemesini izleme

Veri bilimi Grup yöneticileri, ekip liderleri ve proje müşteri adayları, ne işin yapıldığı, işin ne olduğunu ve ne kadar iş kaldığını gibi projelerin ilerlemesini izleyebilir. 

## <a name="azure-devops-dashboards"></a>Azure DevOps panoları

Azure DevOps kullanıyorsanız, belirli bir çevik projeyle ilişkili etkinlikleri ve iş öğelerini izlemek için panolar oluşturabilirsiniz. Panolar hakkında daha fazla bilgi için bkz. [panolar, raporlar ve pencere öğeleri](/azure/devops/report/dashboards/).

Azure DevOps 'da panolar ve pencere öğeleri oluşturma ve özelleştirme yönergeleri için aşağıdaki hızlı başlangıçlara bakın:

- [Panoları ekleme ve yönetme](/azure/devops/report/dashboards/dashboards)
- [Panoya pencere öğesi ekleme](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>Örnek Pano

İşte, ilişkili depolardaki işleme sayısı dahil olmak üzere çevik bir veri bilimi projesinin Sprint etkinliklerini izleyen basit bir örnek Pano. 

- **Geri sayım** kutucuğu, geçerli sprint 'te kalan gün sayısını gösterir. 

- İki **kod kutucuğu** , son yedi günün iki proje depolarındaki işlemeler sayısını gösterir. 

- **TDSP müşteri projesi Için iş öğeleri** , tüm iş öğeleri ve bunların durumları için bir sorgunun sonuçlarını gösterir. 

- **Birikmeli akış diyagramı** (CFD) kapatılan ve etkin iş öğelerinin sayısını gösterir.

- **Burndown grafiği** , Sprint 'teki kalan süre içinde hala tamamlanacak işi gösterir.

- **Tamamlanma grafiği** , Sprint 'teki toplam çalışma miktarı ile karşılaştırıldığında tamamlanan işi gösterir.

![panosunda](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>Sonraki adımlar

[Ekip veri bilimi işlemini yürüten Izlenecek yollar](walkthroughs.md) , bağlantılar ve küçük resim açıklamalarıyla belirli senaryolar için işlemdeki tüm adımları gösteren izlenecek yolları listeler. Bağlantılı senaryolar, akıllı uygulamalar oluşturmak için bulut ve şirket içi araçların ve hizmetlerin iş akışları veya işlem hatları halinde nasıl birleştirileceğini gösterir. 
