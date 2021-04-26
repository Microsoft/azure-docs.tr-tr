---
title: Projenin görevleri ekip veri bilimi Işleminde ortaya çıkmasına neden
description: Takım veri bilimi süreç ekibi üzerinde bir proje için görevlere ilişkin ayrıntılı bir anlatım
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9d9b2546553e03d1555cf4c587d699d9a4ea7e51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96000989"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>Team Data Science Işlemindeki proje sağlama görevleri

Bu makalede, [ekip veri bilimi işlemindeki](overview.md) (TDSP) proje ekibi için bir depo ayarlamak üzere bir *projenin liderine* yönelik olarak tamamladığı görevler açıklanmaktadır. TDSP, bulut tabanlı, tahmine dayalı analiz çözümlerini verimli bir şekilde yürütmek için yapılandırılmış bir etkinlik dizisi sağlayan Microsoft tarafından geliştirilen bir çerçevedir. TDSP, işbirliğinin ve Team Learning 'in artırılmasına yardımcı olmak için tasarlanmıştır. TDSP üzerinde standartlaştırarak bir veri bilimi ekibi için personel rollerinin ve ilişkili görevlerin bir özeti için bkz. [Team Data Science işlem rolleri ve görevleri](roles-tasks.md).

Proje lideri, TDSP 'deki belirli bir veri bilimi projesinde bireysel veri bilimcilerinin günlük etkinliklerini yönetir. Aşağıdaki diyagramda proje sağlama görevleri için iş akışı gösterilmektedir:

![Proje müşteri adayı görev iş akışı](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Bu öğreticide 1. Adım: proje deposu oluşturma ve adım 2: çekirdek proje deposu ekip ProjectTemplate deponuzdan ele alınmaktadır. 

3. Adım: proje için özellik iş öğesi oluşturma ve 4. Adım: proje aşamaları için hikaye ekleme, bkz. [veri bilimi projelerinin çevik geliştirmesi](agile-development.md).

5. Adım: depolama/analiz varlıkları ve paylaşma oluşturma ve özelleştirme hakkında bilgi için bkz. [Takım verileri ve Analytics kaynakları oluşturma](team-lead-tasks.md#create-team-data-and-analytics-resources).

6. Adım: proje deposunun güvenlik denetimini ayarlama için bkz. [Takım üyeleri ekleme ve izinleri yapılandırma](team-lead-tasks.md#add-team-members-and-configure-permissions).

> [!NOTE] 
> Bu makalede, Microsoft 'ta TDSP 'nin uygulanması nedeniyle bir TDSP projesi kurmak için Azure Repos kullanılmıştır. Ekibiniz başka bir kod barındırma platformu kullanıyorsa, proje sağlama görevleri aynıdır, ancak bunları tamamlamaya yönelik yol farklı olabilir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide, [Grup Yöneticisi](group-manager-tasks.md) ve [ekip lideri](team-lead-tasks.md) aşağıdaki kaynakları ve izinleri ayarladığını varsayar:

- Veri biriminiz için Azure DevOps **organizasyonu**
- Veri bilimi takımınız için bir takım **projesi**
- Ekip şablonu ve yardımcı program **depoları**
- Projeniz için depolar oluşturmanız ve düzenlemeniz için kuruluş hesabınızdaki **izinler**

Depoları kopyalamak ve yerel makinenizde veya Veri Bilimi Sanal Makinesi (DSVM) içeriğini değiştirmek ya da Azure dosya depolama alanını ayarlayıp DSVM 'nize bağlamak için şu denetim listesini göz önünde bulundurmanız gerekir:

- Azure aboneliği.
- Git makinenizde yüklü. DSVM kullanıyorsanız git önceden yüklüdür. Aksi takdirde, [platformlar ve araçlar ek](platforms-and-tools.md#appendix)bölümüne bakın.
- DSVM 'yi, Azure 'da oluşturulan ve yapılandırılan Windows veya Linux DSVM 'yi kullanmak istiyorsanız. Daha fazla bilgi ve yönergeler için [veri bilimi sanal makinesi belgelerine](../data-science-virtual-machine/index.yml)bakın.
- Bir Windows DSVM için, makinenizde yüklü [Git kimlik bilgileri Yöneticisi (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) . *README.MD* dosyasında, **indir ve yükle** bölümüne gidin ve **en son yükleyiciyi** seçin. Yükleyici sayfasından *. exe* yükleyicisini indirin ve çalıştırın. 
- Bir Linux DSVM için, DSVM 'niz üzerinde ayarlanan ve Azure DevOps 'a eklenen bir SSH ortak anahtarı. Daha fazla bilgi ve yönergeler için [platformlar ve araçlar EKINDE](platforms-and-tools.md#appendix) **SSH ortak anahtarı oluşturma** bölümüne bakın. 

## <a name="create-a-project-repository-in-your-team-project"></a>Takım projenizde bir proje deposu oluşturma

Takımınızın **myTeam** projesinde bir proje deposu oluşturmak için:

1. Https 'de takımınızın proje **Özeti** sayfasına gidin *: \/ / \<server name> / \<organization name> / \<team name>* Örneğin, **https: \/ /dev.Azure.com/DataScienceUnit/myTeam** ve sol gezinmede bulunan **Depo** ' ı seçin. 
   
1. Sayfanın üst kısmındaki depo adını seçin ve ardından açılan menüden **yeni depo** ' ı seçin.
   
   ![Yeni depo Seç](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. **Yeni depo oluştur** Iletişim kutusunda **tür** altında **Git** ' in seçildiğinden emin olun. **Depo adı** bölümüne *DSProject1* girin ve ardından **Oluştur**' u seçin.
   
   ![Depo oluştur](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. Yeni **DSProject1** deposunu proje ayarları sayfasında göreistediğinizi onaylayın. 
   
   ![Proje ayarlarındaki proje deposu](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>Takım şablonunu proje deponuza aktarın

Proje deponuzu takım şablonu deponuzun içeriğiyle doldurmak için:

1. Takımınızın proje **Özeti** sayfasında sol **Gezinti bölmesinde yer** seçin. 
   
1. Sayfanın üst kısmındaki depo adını seçin ve açılan listeden **DSProject1** öğesini seçin.
   
1. **DSProject1 boş** sayfasında **içeri aktar**' ı seçin. 
   
   ![Içeri aktar 'ı seçin](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. **Git deposunu Içeri aktar** iletişim kutusunda, **kaynak türü** olarak **Git** ' i seçin ve **kopya URL**'si altında **teamtemplate** deponuzun URL 'sini girin. URL *: https: \/ / \<server name> / \<organization name> / \<team name> /_Git/ \<team template repository name>*. Örneğin: **https: \/ /dev.Azure.com/DataScienceUnit/myTeam/_Git/TeamTemplate**. 
   
1. **İçeri aktar**'ı seçin. Ekip şablonu deponuzun içeriği proje deponuza aktarılır. 
   
   ![Takım şablonu deposunu içeri aktar](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

Projenizin özel ihtiyaçlarını karşılamak için proje deponuzun içeriğini özelleştirmeniz gerekiyorsa, depo dosyalarını ve klasörlerini ekleyebilir, silebilir veya değiştirebilirsiniz. Doğrudan Azure Repos çalışabilir veya depoyu yerel makinenize veya DSVM 'nize kopyalayabilir, değişiklikler yapabilir ve güncelleştirmelerinizi paylaşılan proje deposuna gönderebilirsiniz. [Ekip depolarının Içeriğini özelleştirme](team-lead-tasks.md#customize-the-contents-of-the-team-repositories)bölümündeki yönergeleri izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Team Data Science Işlemi tarafından tanımlanan diğer roller ve görevler hakkında ayrıntılı açıklamaların bağlantıları aşağıda verilmiştir:

- [Veri bilimi ekibi için Grup Yöneticisi görevleri](group-manager-tasks.md)
- [Bir veri bilimi ekibi için ekip sağlama görevleri](team-lead-tasks.md)
- [Bir veri bilimi ekibi için bireysel katkıda bulunan görevler](project-ic-tasks.md)