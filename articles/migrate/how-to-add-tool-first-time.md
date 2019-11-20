---
title: Azure geçişi 'nde ilk kez bir değerlendirme/geçiş aracı ekleyin | Microsoft Docs
description: Azure geçişi projesi oluşturmayı ve değerlendirme/geçiş aracının nasıl ekleneceğini açıklar.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: a4e66175b20552e632702cb2ba46d0ae6c0956d4
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720251"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Bir değerlendirmeyi/geçiş aracını ilk kez ekleme

Bu makalede, bir [Azure geçiş](migrate-overview.md) projesine ilk kez nasıl değerlendirme veya geçiş aracı ekleneceği açıklanmaktadır.  
Azure geçişi, şirket içi uygulamalarınızı, iş yüklerinizi ve özel/genel bulut VM 'lerini Azure 'a bulmayı, değerlendirmeyi ve geçirmeyi izlemek için bir merkezi Merkez sağlar. Hub, değerlendirme ve geçiş için Azure geçiş araçları ve diğer araçlar ve bağımsız yazılım satıcısı (ISV) [tekliflerini](migrate-services-overview.md#isv-integration) sağlar. 

## <a name="create-a-project-and-add-a-tool"></a>Proje oluşturma ve araç ekleme

Azure aboneliğinde yeni bir Azure geçişi projesi ayarlayın ve bir araç ekleyin.

- Değerlendirme veya geçiş yaptığınız ortamdan toplanan bulma, değerlendirme ve geçiş meta verilerini depolamak için bir Azure geçişi projesi kullanılır. 
- Bir projede keşfedilen varlıkları izleyebilir ve değerlendirme ve geçişi organize edebilirsiniz.

1. Azure portalı > **Tüm hizmetler** bölümünde **Azure Geçişi**’ni arayın.
2. **Hizmetler** altında **Azure Geçişi**’ni seçin.

    ![Azure geçişi 'ni ayarlama](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. **Genel Bakış** bölümünde **Sunucuları değerlendir ve geçir**’e tıklayın.
4. **Sunucuları bul, değerlendir ve geçir**altında, **sunucuları değerlendir ve geçir**' e tıklayın.

    ![Sunucuları bulma ve değerlendirme](./media/how-to-add-tool-first-time/assess-migrate.png)

1. **Sunucuları bul, değerlendir ve geçir** bölümünde **Araç ekle**’ye tıklayın.
2. **Projeyi geçir** bölümünde Azure aboneliğinizi seçin ve henüz yapmadıysanız bir kaynak grubu oluşturun.
3. **Proje ayrıntıları**' nda projeyi oluşturmak istediğiniz proje adını ve coğrafi konumu belirtin. 

    ![Azure geçişi projesi oluşturma](./media/how-to-add-tool-first-time/migrate-project.png)

    Bu coğrafi bölgelerde bir Azure geçişi projesi oluşturabilirsiniz.

   **Coğrafya** | **Depolama konumu bölgesi**
    --- | ---
    Asya   | Güneydoğu Asya veya Doğu Asya
    Avrupa | Kuzey Avrupa veya Batı Avrupa
    Japonya  | Japonya Doğu veya Japonya Batı
    Birleşik Krallık | UK Güney veya UK Batı
    Amerika Birleşik Devletleri | Orta ABD veya Batı ABD 2
    Kanada | Kanada Orta
    Hindistan  | Hindistan Orta veya Hindistan Güney
    Avustralya | Avustralya Güneydoğu

    Proje için belirtilen coğrafya yalnızca şirket içi VM’lerden toplanan meta verileri depolamak için kullanılır. Gerçek geçiş için herhangi bir hedef bölge seçebilirsiniz.

    Geçiş projesini ve ilişkili kaynaklarını dağıtmaya yönelik bir Coğrafya içinde belirli bir bölge belirtmek isterseniz (aboneliğinizdeki Ilke kısıtlamaları Azure kaynaklarının yalnızca belirli bir Azure bölgesine dağıtımına izin verebilir), aşağıdaki API 'yi kullanarak bir geçiş projesi oluşturun. Abonelik KIMLIĞINI, kaynak grubu adını ve proje adını (Azure geçişi 'nin dağıtıldığı tabloda belirtilen Azure bölgelerinden) birlikte geçirin.

    `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. **İleri**' ye tıklayın ve bir değerlendirme veya geçiş aracı ekleyin.

    > [!NOTE]
    > Bir proje oluşturduğunuzda, en az bir değerlendirme veya geçiş aracı eklemeniz gerekir.

5. **Değerlendirme aracı seçin**' de bir değerlendirme aracı ekleyin. Bir değerlendirme aracına ihtiyacınız yoksa şimdi ** > ** **için değerlendirme aracı eklemeyi atla** ' yı seçin. 
2. **Geçiş aracı Seç**bölümünde, gereken şekilde bir geçiş aracı ekleyin. Şu anda bir geçiş aracına gerek duymuyorsanız şimdi > **İleri**' ye **geçiş aracı eklemeyi atla** ' yı seçin.
3. **İnceleme ve araç ekle**' de ayarları gözden geçirin ve **araç ekle**' ye tıklayın.

Projeyi oluşturduktan sonra sunucuların ve iş yüklerinin, veritabanlarının ve Web uygulamalarının değerlendirilmesi ve geçirilmesi için ek araçlar seçebilirsiniz.

## <a name="create-additional-projects"></a>Ek projeler oluşturma

Bazı durumlarda, ek Azure geçişi projeleri oluşturmanız gerekebilir. Örneğin, farklı coğrafi bölgelerde Veri merkezleriniz varsa veya meta verileri farklı bir Coğrafya içinde depolamanız gerekirse. Ek bir projeyi aşağıdaki gibi oluşturun:

1. Geçerli Azure geçişi projesinde **sunucular** veya **veritabanları**' na tıklayın.
2. Sağ üst köşede, geçerli proje adının yanındaki **Değiştir** ' e tıklayın.
3. **Ayarlar**' da, **Yeni bir proje oluşturmak Için buraya tıklayın ' ı**seçin.
4. Yeni bir proje oluşturun ve önceki yordamda açıklandığı gibi yeni bir araç ekleyin.

## <a name="next-steps"></a>Sonraki adımlar

Ek [değerlendirme](how-to-assess.md) ve [geçiş](how-to-migrate.md) araçları eklemeyi öğrenin. 
