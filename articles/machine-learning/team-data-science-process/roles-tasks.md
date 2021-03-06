---
title: Ekip veri bilimi rol ve görevlerini Işleme
description: Bir veri bilimi grubu için anahtar bileşenlerinin, personel rollerinin ve ilişkili görevlerin bir ana hattı.
author: marktab
manager: marktab
editor: marktab
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4bcbbd9378b6ea861c926eed16d2ceed8131b913
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96000972"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Ekip veri bilimi rol ve görevlerini Işleme

Ekip veri bilimi Işlemi (TDSP), tahmine dayalı analiz çözümlerini ve akıllı uygulamaları verimli bir şekilde oluşturmak için yapılandırılmış bir metodolojisi sağlayan Microsoft tarafından geliştirilen bir çerçevedir. Bu makalede, bu işlem üzerinde standartlaştırarak bir veri bilimi ekibi için anahtar personeli rolleri ve ilişkili görevler özetlenmektedir.

Bu giriş makalesi, TDSP ortamını ayarlama hakkında öğreticilere bağlantı sağlar. Öğreticiler Azure DevOps Projeleri, Azure Repos depoları ve Azure Boards kullanma hakkında ayrıntılı rehberlik sağlar.  Amaç, Modellendirme ve dağıtım aracılığıyla kavramla taşınıyor.

Öğreticiler, Microsoft 'ta TDSP 'nin uygulanması nedeniyle Azure DevOps kullanır. Azure DevOps, rol tabanlı güvenliği, iş öğesi yönetimini, izlemeyi ve kod barındırma, paylaşım ve kaynak denetimini tümleştirerek işbirliğini kolaylaştırır. Öğreticiler Ayrıca, Microsoft yazılımı ve Azure hizmetleriyle önceden yapılandırılmış ve tümleşik birçok popüler veri bilimi aracı bulunan analiz masaüstü olarak bir Azure [veri bilimi sanal makinesi](https://aka.ms/dsvm) (dsvm) kullanır. 

Diğer kod barındırma, çevik planlama ve geliştirme araçlarını ve ortamlarını kullanarak TDSP uygulamak için öğreticileri kullanabilirsiniz, ancak bazı özellikler kullanılamayabilir.

## <a name="structure-of-data-science-groups-and-teams"></a>Veri bilimi gruplarının ve takımların yapısı

Kuruluşların veri bilimi işlevleri genellikle aşağıdaki hiyerarşide düzenlenmiştir:

- Veri bilimi grubu
  - Grup içindeki veri bilimi ekibi/öğeleri

Böyle bir yapıda, Grup müşteri adayları ve ekip liderleri vardır. Genellikle, bir veri bilimi projesi bir veri bilimi ekibi tarafından yapılır. Veri bilimi takımları, proje yönetimi ve idare görevlerine yönelik proje liderleri ve bireysel veri bilimcileri ve mühendislerin, projenin veri bilimi ve veri Mühendisliği parçalarını gerçekleştirmesini sağlar. İlk proje kurulumu ve yönetimi, Grup, takım veya proje tarafından gerçekleştirilir.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>Dört TDSP rolü için tanım ve görevler
Veri bilimi biriminin bir grup içindeki ekiplerden oluştuğu varsayımıyla, TDSP personeli için dört ayrı rol vardır:

1. **Grup Yöneticisi**: bir kuruluştaki tüm veri bilimi birimini yönetir. Bir veri bilimi birimi, her biri ayrı iş verticinde birden çok veri bilimi projesi üzerinde çalışan birden fazla takıma sahip olabilir. Bir Grup Yöneticisi kendi görevlerini bir vekil olarak devredebilir, ancak rolle ilişkili görevler değişmez.
   
2. **Ekip lideri**: bir kuruluşun veri bilimi biriminde bir ekibi yönetir. Bir ekip birden çok veri biliminden oluşur. Küçük bir veri bilimi birimi için Grup Yöneticisi ve ekip lideri aynı kişi olabilir.
   
3. **Proje lideri**: belirli bir veri bilimi projesinde bireysel veri bilimcilerinin günlük etkinliklerini yönetir.
   
4. **Proje bireysel katılımcıları**: veri bilimcileri, Iş analistleri, veri mühendisleri, mimarlar ve bir veri bilimi projesi çalıştıran diğerleri.

> [!NOTE]
> Kuruluşun yapısına ve boyutuna bağlı olarak, tek bir kişi birden fazla rol oynatabilir veya birden fazla kişi bir rolü doldurabilir.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>Dört rol tarafından tamamlanacak görevler

Aşağıdaki diyagramda her bir Team Data Science Işlem rolü için en üst düzey görevler gösterilmektedir. Bu şema ve aşağıdaki, her bir TDSP rolü için görevlerin daha ayrıntılı anahatları, sorumluluklarınıza göre ihtiyacınız olan öğreticiyi seçmenize yardımcı olabilir.

![Rollere ve görevlere genel bakış](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>Grup Yöneticisi görevleri

Grup Yöneticisi veya belirlenen bir TDSP Sistem Yöneticisi, TDSP 'yi benimsemek için aşağıdaki görevleri tamamlar:

- Kuruluş içinde bir Azure DevOps **organizasyonu** ve bir grup projesi oluşturur. 
- Azure DevOps grup projesinde bir **proje şablonu deposu** oluşturur ve Microsoft TDSP ekibi tarafından geliştirilen proje şablonu deposundan bu depoyu ekler. Microsoft TDSP proje şablonu deposu şunları sağlar:
  - Veri, kod ve belge dizinleri dahil **standartlaştırılmış bir dizin yapısı**.
  - Verimli bir veri bilimi işlemine kılavuzluk eden **standartlaştırılmış bir belge şablonları** kümesi.
- Bir **yardımcı program deposu** oluşturur ve bunu Microsoft TDSP ekibi tarafından geliştirilen yardımcı depodan üretir. Microsoft tarafından sağlanan TDSP yardımcı programı deposu, veri bilimclarının çalışmasını daha verimli hale getirmek için bir dizi kullanışlı yardımcı program sağlar. Microsoft yardımcı programı deposu etkileşimli veri araştırması, analiz, raporlama ve temel modelleme ve raporlama için yardımcı programları içerir.
- Kuruluş hesabı için **güvenlik denetimi ilkesini** ayarlar.

Ayrıntılı yönergeler için bkz. [veri bilimi ekibi Için Grup Yöneticisi görevleri](group-manager-tasks.md).

## <a name="team-lead-tasks"></a>Ekip lideri görevleri

Takım lideri veya belirlenen bir proje yöneticisi, TDSP 'yi benimsemek için aşağıdaki görevleri tamamlar:

- Grubun Azure DevOps kuruluşunda bir takım **projesi** oluşturur.
- Projede **proje şablonu deposunu** oluşturur ve Grup Yöneticisi veya temsilci tarafından ayarlanan grup projesi şablon deposundan bu depoyu oluşturur.
- **Ekip yardımcı programını** oluşturur, Grup yardımcı programı deposundan bu depoyu oluşturur ve depoya ekibe özel yardımcı programlar ekler.
- İsteğe bağlı olarak, takıma yönelik faydalı veri varlıklarını depolamak için [Azure dosya depolama](https://azure.microsoft.com/services/storage/files/) oluşturur. Diğer takım üyeleri, bu paylaşılan bulut dosya deposunu analiz masaüstlerinde bağlayabilir.
- İsteğe bağlı olarak, Azure dosya depolama alanını ekibin **Dsvm** 'sine bağlar ve buna ekip veri varlıkları ekler.
- Takım üyeleri ekleyerek ve izinlerini yapılandırarak **güvenlik denetimini** ayarlar.

Ayrıntılı yönergeler için bkz. [bir veri bilimi ekibi Için ekip sağlama görevleri](team-lead-tasks.md).


## <a name="project-lead-tasks"></a>Proje sağlama görevleri

Proje lideri, TDSP 'yi benimsemek için aşağıdaki görevleri tamamlar:

- Takım projesinde bir **proje deposu** oluşturur ve bunu proje şablonu deposundan bir şekilde oluşturur.
- İsteğe bağlı olarak, projenin veri varlıklarını depolamak için **Azure dosya depolama** oluşturur.
- İsteğe bağlı olarak, Azure dosya depolama alanını **Dsvm** 'ye bağlar ve buna proje veri varlıkları ekler.
- Proje üyeleri ekleyerek ve izinlerini yapılandırarak **güvenlik denetimini** ayarlar.

Ayrıntılı yönergeler için bkz. [bir veri bilimi ekibi Için proje sağlama görevleri](project-lead-tasks.md).

## <a name="project-individual-contributor-tasks"></a>Proje bireysel katkıda bulunan görevleri

Genellikle bir veri Bilimcu kullanarak, projenin tek tek katılımcısı, aşağıdaki görevleri de TDSP 'yi kullanarak yapmaktadır:

- Projenin lideri tarafından ayarlanan **Proje deposunu** klonlar.
- İsteğe bağlı olarak, paylaşılan ekibi ve proje **Azure dosya depolama alanını** **veri bilimi sanal makinesi** (dsvm) üzerinde takar.
- Projeyi yürütür.

Bir projeye ekleme hakkında ayrıntılı yönergeler için, bkz. [bir veri bilimi ekibi Için projeye bireysel katkıda bulunan görevleri](project-ic-tasks.md).

## <a name="data-science-project-execution-workflow"></a>Veri bilimi proje yürütmesi iş akışı

İlgili öğreticileri, veri bilimcileri, proje liderleri ve takım liderleri, projenin başlangıcından sonuna kadar tüm görevleri ve aşamaları izlemek için iş öğeleri oluşturabilir. Azure Repos kullanımı, veri bilimcileri arasında işbirliğini yükseltir ve proje yürütmesi sırasında oluşturulan yapıların tüm proje üyeleri tarafından denetlenmesini ve paylaşılmasını sağlar. Azure DevOps, Azure Boards iş öğelerinizi Azure Repos depo dallarınıza bağlamanızı ve bir iş öğesi için ne yapıldığını kolayca izlemenizi sağlar.

Aşağıdaki şekil, proje yürütmesi için TDSP iş akışını özetler:

![Tipik veri bilimi proje iş akışı](./media/roles-tasks/overview-project-execute.png)

İş akışı adımları üç etkinlik halinde gruplandırılabilir:

- Proje liderleri Sprint planlaması gerçekleştir
- Veri `git` bilimcileri, çalışma öğelerine yönelik dallarda yapıtlar geliştirebilir
- Proje liderleri veya diğer takım üyeleri kod İncelemeleri ve çalışma dallarını birincil dalda birleştirir

Proje yürütme iş akışı hakkında ayrıntılı yönergeler için bkz. [veri bilimi projelerinin çevik geliştirmesi](agile-development.md).

## <a name="tdsp-project-template-repository"></a>TDSP proje şablonu deposu

Verimli proje yürütme ve işbirliğini desteklemek için Microsoft TDSP ekibinin [proje şablonu deposunu](https://github.com/Azure/Azure-TDSP-ProjectTemplate) kullanın. Depo size kendi TDSP projeleriniz için kullanabileceğiniz standartlaştırılmış bir dizin yapısı ve belge şablonları sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Team Data Science Işlemi tarafından tanımlanan rollerin ve görevlerin daha ayrıntılı açıklamalarını bulun:

- [Veri bilimi ekibi için Grup Yöneticisi görevleri](group-manager-tasks.md)
- [Bir veri bilimi ekibi için ekip sağlama görevleri](team-lead-tasks.md)
- [Veri bilimi ekibi için proje lideri görevleri](project-lead-tasks.md)
- [Bir veri bilimi ekibi için projeye bireysel katkıda bulunan görevleri](project-ic-tasks.md)
