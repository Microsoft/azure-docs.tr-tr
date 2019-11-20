---
title: Ekip veri bilimi Işleminde bireysel katkıda bulunan görevler
description: Bir veri bilimi takım projesindeki bireysel katkıda bulunan görevlere ilişkin ayrıntılı bir anlatım.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/24/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b64d9669c11f15de5e6bd616ff7a79f59b748363
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244304"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Ekip veri bilimi Işleminde bireysel katkıda bulunan görevler

Bu konu, bir *bireyin* [ekip veri bilimi işleminde](overview.md) (TDSP) bir proje ayarlamak için tamamladığı görevleri özetler. Amaç, TDSP üzerinde standartlayan bir ortak ekip ortamında çalışır. TDSP, işbirliğinin ve Team Learning 'in artırılmasına yardımcı olmak için tasarlanmıştır. Personel rollerinin ve TDSP üzerinde standartlaştırarak bir veri bilimi ekibi tarafından işlenen ilişkili görevlerinin bir özeti için bkz. [Team Data Science işlem rolleri ve görevleri](roles-tasks.md).

Aşağıdaki diyagramda, takım ortamlarını ayarlamak için projenin bireysel katkı sağlayanlar (veri bilimcileri) tamamlanmış görevler gösterilmektedir. TDSP altında bir veri bilimi projesi yürütmeye ilişkin yönergeler için bkz. [veri bilimi projelerini yürütme](project-execution.md). 

![Bireysel katkıda bulunan görevleri](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository** , proje ekibinizin proje şablonlarını ve varlıklarını paylaşmak için tuttuğu depodur.
- **Ekipte** ekip, takımınızın özel olarak sağladığı yardımcı program deposudur. 
- **Grouputilities** , grubunuzun tüm Grup genelinde yararlı yardımcı programları paylaşmak için sakladığı depodur. 

> [!NOTE] 
> Bu makalede, Microsoft 'ta TDSP 'nin nasıl uygulanacağı için bir TDSP ortamı ayarlamak üzere Azure Repos ve bir Veri Bilimi Sanal Makinesi (DSVM) kullanılmaktadır. Takımınız diğer kod barındırma veya geliştirme platformlarını kullanıyorsa, bireysel katkıda bulunan görevleri aynıdır, ancak bunları tamamlamaya yönelik yol farklı olabilir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide, aşağıdaki kaynakların ve izinlerin [Grup yöneticiniz](group-manager-tasks.md), [ekip lideri](team-lead-tasks.md)ve [Proje lideri](project-lead-tasks.md)tarafından ayarlandığı varsayılır:

- Veri bilimi biriminiz için Azure DevOps **organizasyonu**
- Proje şablonu ve varlıklarını paylaşmak için proje lideri tarafından ayarlanan proje **deposu**
- Grup Yöneticisi ve ekip lideri tarafından ayarlanan **Grouputilities** ve **Ekipçistilities** depoları
- Varsa, takımınız veya projeniz için paylaşılan varlıklar için Azure **dosya depolama** ayarı
- Proje deponuzdan kopyalamanız ve geri göndermeniz için **izinler** 

Depoları kopyalamak ve yerel makinenizde veya DSVM 'nizin içeriğini değiştirmek ya da Azure dosya depolama alanını DSVM 'nize bağlamak için şunlar gerekir:

- Azure aboneliği.
- Git makinenizde yüklü. DSVM kullanıyorsanız git önceden yüklüdür. Aksi takdirde, [platformlar ve araçlar ek](platforms-and-tools.md#appendix)bölümüne bakın.
- DSVM 'yi, Azure 'da oluşturulan ve yapılandırılan Windows veya Linux DSVM 'yi kullanmak istiyorsanız. Daha fazla bilgi ve yönergeler için [veri bilimi sanal makinesi belgelerine](/azure/machine-learning/data-science-virtual-machine/)bakın.
- Bir Windows DSVM için, makinenizde yüklü [Git kimlik bilgileri Yöneticisi (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) . *README.MD* dosyasında, **indir ve yükle** bölümüne gidin ve **en son yükleyiciyi**seçin. Yükleyici sayfasından *. exe* yükleyicisini indirin ve çalıştırın. 
- Bir Linux DSVM için, DSVM 'niz üzerinde ayarlanan ve Azure DevOps 'a eklenen bir SSH ortak anahtarı. Daha fazla bilgi ve yönergeler için [platformlar ve araçlar EKINDE](platforms-and-tools.md#appendix) **SSH ortak anahtarı oluşturma** bölümüne bakın. 
- Azure dosya depolama için Azure dosya depolama bilgileri, DSVM 'nize bağlamanız gerekir. 

## <a name="clone-repositories"></a>Depoları Kopyala

Depolarla yerel olarak çalışmak ve değişikliklerinizi paylaşılan ekibe ve proje depolarına iletmek *için, önce* depoları yerel makinenize kopyalamanız veya kopyalamanız gerekir. 

1. Azure DevOps 'da, *https: \/ @ no__t-2 @ no__t-3sunucu adı >/\<kuruluş adı >/\<team name >* (örneğin, **https: \//dev. Azure. com/Databilimenceunit/myTeam**) konumundaki takımınızın Proje Özeti sayfasına gidin.
   
1. Sol **Gezinti bölmesinde depo** ' ı seçin ve sayfanın üst kısmında, kopyalamak istediğiniz depoyu seçin.
   
1. Depo sayfasında sağ üstteki **kopya** ' ı seçin.
   
1. **Depoyu Kopyala** iletişim kutusunda, http bağlantısı için **https** ' yi veya bir SSH bağlantısı için **SSH** ' yi SEÇIN ve **komut satırı** altına kopya URL 'sini panonuza kopyalayın.
   
   ![Depoyu Kopyala](./media/project-ic-tasks/clone.png)
   
1. Yerel makinenizde veya DSVM 'de aşağıdaki dizinleri oluşturun:
   
   - Windows için: **C:\GitRepos**
   - Linux için: **$Home/gitrepos**
   
1. Oluşturduğunuz dizine geçin.
   
1. Git Bash 'de, kopyalamak istediğiniz her depo için `git clone <clone URL>` komutunu çalıştırın. 
   
   Örneğin, aşağıdaki komut, **Teamutilities** deposunu yerel makinenizde *myTeam* dizinine kopyalar. 
   
   **HTTPS bağlantısı:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH bağlantısı:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. Yerel proje dizininizde kopyalanmış depoların klasörlerini görebilecekleri onaylayın.
   
   ![Üç yerel depo klasörü](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>Azure dosya depolama alanını DSVM 'nize bağlama

Takımınız veya projeniz Azure dosya depolama alanında paylaşılan varlıklar içeriyorsa, dosya depolama alanını yerel makinenize veya DSVM 'nize bağlayın. [Yerel makinenizde veya DSVM 'de Azure dosya depolama takma](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm)bölümündeki yönergeleri izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Team Data Science Işlemi tarafından tanımlanan diğer roller ve görevler hakkında ayrıntılı açıklamaların bağlantıları aşağıda verilmiştir:

- [Veri bilimi ekibi için Grup Yöneticisi görevleri](group-manager-tasks.md)
- [Bir veri bilimi ekibi için ekip sağlama görevleri](team-lead-tasks.md)
- [Veri bilimi ekibi için proje lideri görevleri](project-lead-tasks.md)

