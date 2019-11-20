---
title: Azure geçişi geçiş araçları ekleme
description: Geçiş Araçları Azure geçişi Merkezi'nde eklemeyi açıklar.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b3c77f052ed92db235b363e63859b9beb9e4f5a2
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811875"
---
# <a name="add-migration-tools"></a>Geçiş Araçları'nı ekleyin

Bu makalede, Geçiş Araçları'nda eklemeyi açıklar [Azure geçişi](migrate-overview.md).

Azure geçişi, Azure'a Araçları'nın bir hub'ı değerlendirme ve geçiş için sağlar. Bu araçlar, diğer Azure Hizmetleri ve üçüncü taraf bağımsız yazılım satıcısı (ISV) teklifleri tarafından sağlanan yerel araçlar içerir.

Geçiş Aracı eklemek istediğiniz ve bir Azure geçişi projesini ayarlarsınız henüz ayarlamadıysanız, bu izleyin [makale](how-to-add-tool-first-time.md).



## <a name="selecting-an-isv-tool"></a>Bir ISV aracı seçme

Seçerseniz bir [ISV aracı](migrate-services-overview.md#isv-integration) geçiş için bir lisans almak veya ISV ilkesine uygun olarak ücretsiz bir deneme için kaydolma başlatabilirsiniz. Her Aracı'nda Azure geçişi için bağlanmak için bir seçenek yoktur. Aracı dağıtma ve Azure geçişi ile aracı çalışma alanına bağlamak için aracı yönergeleri ve belgeleri izleyin. 

## <a name="select-a-migration-scenario"></a>Bir geçiş senaryosu seçin

1. Azure geçişi projesinde tıklayın **genel bakış**.
2. Kullanmak istediğiniz geçiş senaryosu seçin:

    - Makineleri ve iş yüklerini Azure'a geçirmek için seçin **değerlendirin ve sunucularını geçirme**.
    - Şirket içi SQL makineleri geçirmeyi seçin **değerlendirin ve veritabanlarını geçirme**.
    - Şirket içi web uygulamaları geçirmek için seçin **değerlendirin ve web uygulamaları geçirme**.
    - Şirket içi veri büyük miktarlarda çevrimdışı modda Azure'a geçirmek için seçin **Data Box sipariş**.

    ![Değerlendirme senaryosu](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Bir sunucu geçiş aracını seçin

1. Tıklayın **değerlendirmek ve sunucularını geçirme**.
2. İçinde **Azure geçişi - sunucuları**, Geçiş Araçları'nı henüz eklemediyseniz altında **Geçiş Araçları**seçin **geçiş aracı eklemek için burayı tıklatın**. Varsa, Geçiş Araçları, önceden eklediğiniz **daha fazla geçiş araçları ekleme**seçin **değişiklik**.

    > [!NOTE]
    > Farklı bir projeye gidin gerekip gerekmediğini **Azure geçişi - sunucuları**yanındaki **farklı geçiş projesi için ayrıntılara bakın**, tıklayın **Buraya**.

3. İçinde **Azure geçişi**, kullanmak istediğiniz Taşıma Aracı'nı seçin.
    - Azure geçişi sunucusu geçişi kullanırsanız, ayarlayın ve doğrudan Azure geçişi projesinde geçişleri çalıştırın.
    - Bir üçüncü taraf değerlendirme aracı kullanırsanız, ISV için sağlanan bağlantıya gidin ve sağladıkları yönergelere uygun olarak bir geçiş çalıştırın.

## <a name="select-a-database-migration-tool"></a>Veritabanı geçiş aracını seçin

1. Tıklayın **değerlendirin ve veritabanlarını geçirme**
2. İçinde **veritabanları**, tıklayın **ekleme Araçları**.
3. Bir araç eklentisi > **Select geçiş aracı**, veritabanınızı geçirme için kullanmak istediğiniz aracı seçin.

## <a name="select-a-web-app-migration-tool"></a>Bir web uygulaması geçiş aracını seçin

1. Tıklayın **değerlendirin ve web uygulamaları geçirme**.
2. Bağlantı için Azure App Service için geçiş aracı izleyin. Geçiş Aracı için kullanın:

    - **Çevrimiçi uygulamalar değerlendirmek**: Değerlendirmek ve uygulamaları çevrimiçi Genel bir URL ile Azure App Service Migration Yardımcısı'nı kullanarak geçirme.
    - **.NET/PHP**: Dahili .NET ve PHP uygulamaları için indirin ve geçiş Yardımcısı'nı çalıştırın.

## <a name="order-an-azure-data-box"></a>Bir Azure Data Box sipariş

Büyük miktarda veriyi Azure'a geçirmek için çevrimdışı veri aktarımı için bir Azure DAta Box sipariş edebilirsiniz.

1. Tıklayın **bir veri kutusu siparişi**.
2. İçinde **Azure Data Box'ınızı seçin**, aboneliğinizi belirtin. 
3. Azure'a bir içeri aktarım olacaktır. Veri kaynağı ve verileri bir Azure bölgesine hedefini belirtin.

## <a name="next-steps"></a>Sonraki adımlar

Azure geçişi sunucusu geçişi kullanan bir geçiş denemek [Hyper-V](tutorial-migrate-hyper-v.md) veya [VMware](tutorial-migrate-vmware.md) VM'ler.
