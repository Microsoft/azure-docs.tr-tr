---
title: Azure geçişi 'ne geçiş araçları ekleme
description: Azure geçişi 'ne geçiş araçları ekleme hakkında bilgi edinin.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 97051e97ec9868f6941b579241e16e62fdd2162b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96751793"
---
# <a name="add-migration-tools"></a>Geçiş araçları ekleme

Bu makalede, [Azure](./migrate-services-overview.md)geçişi 'nde geçiş araçlarının nasıl ekleneceği açıklanmaktadır.

- Bir geçiş aracı eklemek ve henüz bir Azure geçişi projesi ayarlamadıysanız, bu [makaleye](create-manage-projects.md)uyun.
- Geçiş için bir ISV aracı eklediyseniz, araçla çalışmaya hazırlanmak için [adımları izleyin](prepare-isv-movere.md).

## <a name="select-a-migration-scenario"></a>Bir geçiş senaryosu seçin

1. Azure geçişi projesinde **Genel Bakış ' a** tıklayın.
2. Kullanmak istediğiniz geçiş senaryosunu seçin:

    - Makineleri ve iş yüklerini Azure 'a geçirmek için, **sunucuları değerlendir ve geçir**' i seçin.
    - Şirket içi veritabanlarını geçirmek için, **veritabanlarını değerlendir ve geçir**' i seçin.
    - Şirket içi Web uygulamalarını geçirmek için **daha fazla bilgi keşfet**  >  **Web Apps** seçin.
    - Veri kutusunu kullanarak Azure 'a veri geçirmek için **daha fazla**  >  **veri keşfet kutusunu** seçin.

    ![Geçiş senaryosu seçme seçenekleri](./media/how-to-migrate/migrate-scenario.png)


## <a name="select-a-server-migration-tool"></a>Sunucu geçiş aracı seçin

1. Araç ekleme:

    - Portalda **sunucuları değerlendir ve geçir** seçeneğini kullanarak bir Azure geçişi projesi oluşturduysanız, Azure geçişi sunucusu geçiş aracı projeye otomatik olarak eklenir. Ek geçiş araçları eklemek için, **sunucular**' ın yanındaki **geçiş araçları**' nın yanındaki **daha fazla araç ekle**' yi seçin.
    
         ![Ek geçiş araçları ekleme düğmesi](./media/how-to-migrate/add-migration-tools.png)

    - Farklı bir seçenek kullanarak bir proje oluşturduysanız ve henüz herhangi bir geçiş aracı yoksa, **sunucular**  >  **geçiş araçları**' nda **buraya tıklayın ' ı** seçin.

    ![İlk geçiş araçlarını ekleme düğmesi](./media/how-to-migrate/no-migration-tool.png)

2. **Azure geçişi**  >  **ekleme araçları**' nda, eklemek istediğiniz araçları seçin. Ardından **araç ekle**' yi seçin.

    ![Listeden değerlendirme araçlarını seçin](./media/how-to-migrate/select-migration-tool.png)


## <a name="select-a-database-migration-tool"></a>Veritabanı geçiş aracı seçin

Portalda **veritabanını değerlendir ve geçir** seçeneğini kullanarak bir Azure geçişi projesi oluşturduysanız, veritabanı geçiş aracı projeye otomatik olarak eklenir. 

1. Veritabanı geçiş aracı projede değilse, **veritabanları**  >  **değerlendirme araçları**' nda **buraya tıklayın ' ı** seçin.
    
    ![Veritabanı geçiş araçları Ekle](./media/how-to-migrate/no-database-migration-tool.png)


2. **Azure geçişi**  >  **ekleme araçları**' nda veritabanı geçiş aracını seçin. Ardından **araç ekle**' yi seçin.

    ![Listeden veritabanı geçiş aracını seçin](./media/how-to-migrate/select-database-migration-tool.png)

    

## <a name="select-a-web-app-migration-tool"></a>Web uygulaması geçiş aracı seçin

Portalda **daha fazla keşfet** webapps seçeneğini kullanarak bir Azure geçişi projesi oluşturduysanız  >   , Web uygulaması geçiş aracı projeye otomatik olarak eklenir. 

1. Web uygulaması geçiş aracı projede değilse, **Web Apps**  >  **değerlendirme araçları**' nda **buraya tıklayın ' ı** seçin.

    ![Web uygulaması geçiş araçları ekleme](./media/how-to-migrate/no-web-app-migration-tool.png)
 

2. **Azure geçişi**  >  **ekleme araçları**' nda Web uygulaması geçiş aracı ' nı seçin. Ardından **araç ekle**' yi seçin.

    ![Listeden WebApp değerlendirmesi araçlarını seçin](./media/how-to-migrate/select-web-app-migration-tool.png)


## <a name="order-an-azure-data-box"></a>Azure Data Box sıralama

Büyük miktarlardaki verileri Azure 'a geçirmek için, çevrimdışı veri aktarımı için bir Azure Data Box sipariş edebilirsiniz.

1. **Genel bakış**' da **daha fazla keşfet**' i seçin.
2. **Daha fazla keşfet**' de **veri kutusunu** seçin.
3. **Data Box kullanmaya başlayın** bölümünde, bir Data Box sıralanırken kullanmak istediğiniz aboneliği ve kaynak grubunu seçin.
4. **Aktarım türü** , Azure 'a bir içeri aktarmalıdır. Verilerin bulunduğu ülkeyi ve verileri aktarmak istediğiniz Azure bölgesini belirtin. 
5. Ayarları kaydetmek için **Uygula** ' ya tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

[Hyper-V](tutorial-migrate-hyper-v.md) veya [VMware](tutorial-migrate-vmware.md) VM 'Leri Için Azure geçişi sunucu geçiş aracını kullanarak bir geçiş deneyin.
