---
title: Team Data Science Işlem projelerinin ilerlemesini izleyin
description: Veri bilimi Grup yöneticileri, ekip liderleri ve proje müşteri adayları, bir veri bilimi projesinin ilerlemesini takip edebilir.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 78a543fabadcc0d4e1766af1bc5c65aac0dadebe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91358934"
---
# <a name="track-the-progress-of-data-science-projects"></a>Veri bilimi projelerinin ilerlemesini izleme

Veri bilimi Grup yöneticileri, ekip liderleri ve proje liderleri, projelerinin ilerlemesini izleyebilir.  Yöneticiler, ne işe yaradığını, kimin işi yaptığını ve ne kadar iş kaldığını bilmesini ister.   Beklentileri yönetmek, başarı açısından önemli bir öğedir.

## <a name="azure-devops-dashboards"></a>Azure DevOps panoları

Azure DevOps kullanıyorsanız, belirli bir çevik projeyle ilişkili etkinlikleri ve iş öğelerini izlemek için panolar oluşturabilirsiniz. Panolar hakkında daha fazla bilgi için bkz. [panolar, raporlar ve pencere öğeleri](/azure/devops/report/dashboards/).

Azure DevOps 'da panolar ve pencere öğeleri oluşturma ve özelleştirme yönergeleri için aşağıdaki hızlı başlangıçlara bakın:

- [Panoları ekleme ve yönetme](/azure/devops/report/dashboards/dashboards)
- [Panoya pencere öğesi ekleme](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>Örnek pano

İşte, ilişkili depolardaki işleme sayısı dahil olmak üzere çevik bir veri bilimi projesinin Sprint etkinliklerini izleyen basit bir örnek Pano. 

- **Geri sayım** kutucuğu, geçerli sprint 'te kalan gün sayısını gösterir. 

- İki **kod kutucuğu** , son yedi günün iki proje depolarındaki işlemeler sayısını gösterir. 

- **TDSP müşteri projesi Için iş öğeleri** , tüm iş öğeleri ve bunların durumları için bir sorgunun sonuçlarını gösterir. 

- **Birikmeli akış diyagramı** (CFD) kapatılan ve etkin iş öğelerinin sayısını gösterir.

- **Burndown grafiği** , Sprint 'teki kalan süre içinde hala tamamlanacak işi gösterir.

- **Tamamlanma grafiği** , Sprint 'teki toplam çalışma miktarı ile karşılaştırıldığında tamamlanan işi gösterir.

![Ekran görüntüsünde bir Azure DevOps Pano örneği gösterilmektedir.](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>Sonraki adımlar

[Takım veri bilimi işlemini yürüten Izlenecek yollar](walkthroughs.md) , tüm işlem adımlarını gösteren izlenecek yolları listeler. Bağlantılı senaryolar, bulut ve şirket içi kaynakların akıllı uygulamalar halinde nasıl yönetileceğini gösterir. 
