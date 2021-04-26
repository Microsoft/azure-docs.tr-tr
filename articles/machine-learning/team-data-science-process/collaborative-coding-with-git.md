---
title: Git-Team veri bilimi Işlemiyle işbirliğine dayalı kodlama
description: Çevik planlamayla git kullanan veri bilimi projeleri için işbirliğine dayalı kod geliştirme.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ca24a781f4f3ad5c210813dabbb896de35056ed6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97588718"
---
# <a name="collaborative-coding-with-git"></a>Git ile işbirliği içinde kodlama

Bu makalede, veri bilimi projeleri için işbirliğine dayalı kod geliştirme çerçevesi olarak git 'in nasıl kullanılacağı açıklanır. Makale, Azure Repos Azure Boards [çevik geliştirme](agile-development.md) iş öğelerine, kod incelemelerinin nasıl yapılacağını ve değişiklikler için çekme istekleri oluşturma ve birleştirme hakkında nasıl bağlantı yapılacağını ele alır.

## <a name="link-a-work-item-to-an-azure-repos-branch"></a><a name='Linkaworkitemwithagitbranch-1'></a>Bir iş öğesini Azure Repos dalına bağlama 

Azure DevOps, bir Azure Boards Kullanıcı hikayesine veya görev iş öğesini Azure Repos git deposu dalına bağlamak için kullanışlı bir yol sağlar. Kullanıcı hikayenizi veya görevinizi, doğrudan onunla ilişkili koda bağlayabilirsiniz. 

Bir iş öğesini yeni bir dala bağlamak için, iş öğesinin yanındaki **Eylemler** üç nokta (**...**) simgesini seçin ve bağlam menüsünde, ' ye kaydırın ve **yeni dal**' ı seçin.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

**Dal oluştur** iletişim kutusunda yeni dal adını ve temel Azure Repos git deposu ve dalını sağlayın. Temel Depo, iş öğesiyle aynı Azure DevOps projesinde olmalıdır. Temel dal, var olan herhangi bir dal olabilir. **Dal oluştur**' u seçin. 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Ayrıca, Windows veya Linux 'ta aşağıdaki git Bash komutunu kullanarak yeni bir dal oluşturabilirsiniz:

```bash
git checkout -b <new branch name> <base branch name>

```
\<base branch name>' I belirtmezseniz, yeni dal temel alır `main` . 

Çalışma dalınıza geçiş yapmak için aşağıdaki komutu çalıştırın: 

```bash
git checkout <working branch name>
```

Çalışma dalına geçiş yaptıktan sonra, iş öğesini tamamlamaya yönelik kod veya belge yapıtları geliştirmeye başlayabilirsiniz. Çalışan `git checkout main` anahtarlar, dala geri döner `main` .

Her Kullanıcı hikayesi iş öğesi için bir git dalı oluşturmak iyi bir uygulamadır. Ardından, her görev iş öğesi için Kullanıcı hikayesi dalını temel alan bir dal oluşturabilirsiniz. Aynı proje için farklı Kullanıcı hikayeleri üzerinde çalışan birden fazla kişiye veya aynı kullanıcı hikayesine yönelik farklı görevlere sahip olduğunuzda, dalları Kullanıcı Story-Task ilişkisine karşılık gelen bir hiyerarşide düzenleyin. Her bir takım üyesinin farklı bir dalda veya bir dalı paylaşırken farklı bir kodda ya da diğer yapıtlarda çalışmasını sağlayarak çakışmaları en aza indirmenize neden olabilirsiniz. 

Aşağıdaki diyagramda, TDSP için önerilen dallanma stratejisi gösterilmektedir. Özellikle bir proje üzerinde yalnızca bir veya iki kişi çalışırken ya da bir kullanıcı hikayesinin tüm görevlerinde yalnızca bir kişi çalıştığı zaman, burada gösterildiği gibi birçok dala gerek duymayabilir. Ancak, geliştirme dalını birincil daldan ayırmak her zaman iyi bir uygulamadır ve yayın dalının geliştirme etkinlikleri tarafından kesintiye uğramasını önlemeye yardımcı olabilir. Git dal modelinin tamamının açıklaması için, bkz. [başarılı git dallanma modeli](https://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Ayrıca, bir iş öğesini varolan bir dala bağlayabilirsiniz. Bir iş öğesinin **ayrıntı** sayfasında **bağlantı ekle**' yi seçin. Ardından, iş öğesini bağlamak için mevcut bir dalı seçin ve **Tamam**' ı seçin. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name="work-on-the-branch-and-commit-changes"></a><a name='WorkonaBranchandCommittheChanges-2'></a>Dalda çalışma ve değişiklikleri yapma 

Yerel makinenizin dalına bir R betik dosyası eklemek gibi iş öğesi için bir değişiklik yaptıktan sonra, `script` aşağıdaki git Bash komutlarını kullanarak yerel dalınızdan değişikliği yukarı akış çalışma dalına kaydedebilirsiniz:

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name="create-a-pull-request"></a><a name='CreateapullrequestonVSTS-3'></a>Çekme isteği oluşturma

Bir veya daha fazla işleme ve gönderim sonrasında, geçerli çalışma dalınızı temel dalına birleştirmeye hazırsanız, Azure Repos bir *çekme isteği* oluşturabilir ve gönderebilirsiniz. 

Azure DevOps projenizin ana sayfasından, sol gezinti bölmesinde bulunan **Repos**  >  **çekme istekleri** ' ni işaret edin. Ardından **Yeni çekme isteği** düğmelerinden birini veya **çekme isteği oluştur** bağlantısını seçin.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

**Yeni çekme isteği** ekranında, gerekirse Değişikliklerinizi birleştirmek istediğiniz git deposuna ve dalına gidin. İstediğiniz diğer bilgileri ekleyin veya değiştirin. **Gözden geçirenler** altında, gözden geçirenlerin adlarını ekleyin ve ardından **Oluştur**' u seçin. 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name="review-and-merge"></a><a name='ReviewandMerge-4'></a>İnceleme ve birleştirme

Çekme isteğini oluşturduktan sonra, gözden geçirenler çekme isteğini gözden geçirmek için bir e-posta bildirimi alır. Gözden geçirenler değişikliklerin çalışıp çalışmadığını test edin ve mümkünse değişiklikleri istek sahibine göre kontrol edin. Gözden geçirenler yorum yapabilir, değişiklikler yapabilir ve değerlendirmesi temelinde çekme isteğini onaylayabilir veya reddedebilir. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

Gözden geçirenler değişiklikleri onayladıktan sonra, sizin veya birleştirme izinlerine sahip başka bir kişi çalışma dalını kendi temel dalına birleştirebilirler. **Tamam**' ı seçin ve ardından **çekme isteği Tamam** iletişim kutusunda **birleştirmeyi Tamam** ' ı seçin. Çalışma dalını birleştirdikten sonra silmeyi tercih edebilirsiniz. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

İsteğin **tamamlandı** olarak işaretlendiğinden emin olun. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Sol gezinmede yer alan **depoya** geri döndüğünüzde, dal silindiğinden Ana dala geçtiniz olduğunu görebilirsiniz `script` .

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Ayrıca, `script` çalışma dalını temel dalında birleştirmek ve Birleştirmeden sonra çalışma dalını silmek için aşağıdaki git Bash komutlarını da kullanabilirsiniz:

```bash
git checkout main
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>Sonraki adımlar

[Veri bilimi görevleri yürütme](execute-data-science-tasks.md) , etkileşimli veri araştırması, veri analizi, raporlama ve model oluşturma gibi çeşitli yaygın veri bilimi görevlerini tamamlamaya yönelik yardımcı programları nasıl kullanacağınızı gösterir.

[Örnek izlenecek yollar](walkthroughs.md) , bağlantılar ve küçük resim açıklamalarıyla belirli senaryolara yönelik izlenecek yolları listeler. Bağlantılı senaryolar, akıllı uygulamalar oluşturmak için bulut ve şirket içi araçların ve hizmetlerin iş akışları veya işlem hatları halinde nasıl birleştirileceğini gösterir. 

