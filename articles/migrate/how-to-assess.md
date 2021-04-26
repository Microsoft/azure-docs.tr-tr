---
title: Azure geçişi 'nde değerlendirme araçları ekleme
description: Azure geçişi 'nde değerlendirme araçları eklemeyi öğrenin.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 37f3748b4f0f3db47bbd6fbe9bc06a307781c2f8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786813"
---
# <a name="add-assessment-tools"></a>Değerlendirme araçları ekleme

Bu makalede, [Azure geçişi](./migrate-services-overview.md)'nde değerlendirme araçlarının nasıl ekleneceği açıklanmaktadır. 

- Bir değerlendirme aracı eklemek isterseniz ve henüz bir Azure geçişi projeniz yoksa, bu [makaleye](create-manage-projects.md)uyun.
- Değerlendirme için bir ISV aracı veya Movere eklediyseniz, araçla çalışmaya hazırlanmak için [adımları izleyin](prepare-isv-movere.md).

## <a name="select-an-assessment-scenario"></a>Değerlendirme senaryosu seçin

1. Azure geçişi projesinde **Genel Bakış ' a** tıklayın.
2. Değerlendirme senaryosunu seçin:

    - Veri merkezinizden veya diğer bulutlardan Azure 'a sunucu (fiziksel veya sanal) bulma, değerlendirme ve geçiş yapmak için **bul, değerlendir ve geçir**' i seçin. Ayrıca, bu geçiş hedefini kullanarak VMware ortamınızdan SQL Server bulabilir ve değerlendirebilirsiniz.
    - Şirket içi SQL Server veritabanlarını değerlendirmek için **veritabanlarını değerlendir ve geçir**' i seçin.
    - Şirket içi Web uygulamalarını değerlendirmek veya geçirmek için **daha fazla Web Apps keşfet**' i seçin  >  .
    - Sanal Masaüstü altyapınızı değerlendirmek için **daha fazla**  >  **sanal masaüstü altyapısını** keşfet ' i seçin.

    ![Değerlendirme senaryosu seçme seçenekleri](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-discovery-and-assessment-tool"></a>Bulma ve değerlendirme aracı seçin 


1. Araç ekleme:

    - Portalda **sunucuları değerlendir ve geçir** seçeneğini kullanarak bir Azure geçişi projesi oluşturduysanız, Azure geçişi bulma ve değerlendirme aracı projeye otomatik olarak eklenir. **Windows, Linux ve SQL Server** ek değerlendirme araçları eklemek için **değerlendirme araçları**' nın yanındaki **daha fazla araç ekle**' yi seçin.

         ![Ek değerlendirme araçları ekleme düğmesi](./media/how-to-assess/add-assessment-tool.png)

    - Farklı bir seçenek kullanarak bir proje oluşturduysanız ve henüz herhangi bir değerlendirme aracı yoksa, **Windows, Linux ve SQL Server**  >  **değerlendirme araçları**'nda **buraya tıklayın ' ı** seçin.

        ![İlk değerlendirme aracı eklenecek düğme](./media/how-to-assess/no-assessment-tool.png)

2. **Azure geçişi**  >  **ekleme araçları**' nda, eklemek istediğiniz araçları seçin. Ardından **araç ekle**' yi seçin.

    ![Listeden değerlendirme araçlarını seçin](./media/how-to-assess/select-assessment-tool.png)



## <a name="select-a-database-assessment-tool"></a>Bir veritabanı değerlendirmesi aracı seçin

1. Araç ekleme:

    - Portalda **veritabanını değerlendir ve geçir** seçeneğini kullanarak bir Azure geçişi projesi oluşturduysanız, veritabanı değerlendirmesi aracı projeye otomatik olarak eklenir. Ek değerlendirme araçları eklemek için, bkz. **değerlendirme araçları**' nın **yanında,** **daha fazla araç ekle**' yi seçin.

    - Farklı bir seçenek kullanarak bir proje oluşturduysanız ve henüz hiç veritabanı değerlendirme araçlarına sahip değilseniz, **veritabanları**  >  **değerlendirme araçları**'nda **buraya tıklayın ' ı** seçin.

2. **Azure geçişi**  >  **ekleme araçları**' nda, eklemek istediğiniz araçları seçin. Ardından **araç ekle**' yi seçin.

    ![Listeden veritabanı değerlendirmesi araçlarını seçin](./media/how-to-assess/select-database-assessment-tool.png)


## <a name="select-a-web-app-assessment-tool"></a>Bir Web uygulaması değerlendirme aracı seçin

Portalda **daha fazla keşfet** webapps seçeneğini kullanarak bir Azure geçişi projesi oluşturduysanız  >   , Web uygulaması değerlendirme aracı projeye otomatik olarak eklenir. 


1. Web uygulaması değerlendirme aracı projede değilse, **Web Apps**  >  **değerlendirme araçları**' nda **buraya tıklayın ' ı** seçin.
    
    ![Web uygulaması değerlendirme araçları ekleme](./media/how-to-assess/no-web-app-assessment-tool.png)


2. **Azure geçişi**  >  **ekleme araçları**' nda Web uygulaması değerlendirme aracı ' nı seçin. Ardından **araç ekle**' yi seçin.

    ![Listeden veritabanı geçiş aracını seçin](./media/how-to-assess/select-web-app-assessment-tool.png)

 


## <a name="next-steps"></a>Sonraki adımlar

[VMware](./tutorial-discover-vmware.md), [Hyper-V](./tutorial-discover-hyper-v.md)veya [fiziksel sunucular](./tutorial-discover-physical.md) için Azure geçişi bulma ve değerlendirme aracı 'nı kullanarak değerlendirme için şirket içi sunucuları keşfetme