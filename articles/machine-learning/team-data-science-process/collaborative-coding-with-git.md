---
title: Git - Team Data Science Process ile işbirliğine dayalı kodlama
description: Çevik planlama ile Git kullanarak veri bilimi projeleri için işbirliğine dayalı kod geliştirme yapmakla.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 08/23/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3b57621fcec654f11c8e9a68e4568f332dbf9ac6
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195517"
---
# <a name="collaborative-coding-with-git"></a>Git ile işbirliği içinde kodlama

Bu makalede, veri bilimi projeleri için işbirliğine dayalı kod geliştirme çerçevesi olarak git 'in nasıl kullanılacağı açıklanır. Makale, Azure Repos Azure Boards [çevik geliştirme](agile-development.md) iş öğelerine, kod incelemelerinin nasıl yapılacağını ve değişiklikler için çekme istekleri oluşturma ve birleştirme hakkında nasıl bağlantı yapılacağını ele alır.

## <a name='Linkaworkitemwithagitbranch-1'></a>Bir iş öğesini Azure Repos dalına bağlama 

Azure DevOps, bir Azure Boards Kullanıcı hikayesine veya görev iş öğesini Azure Repos git deposu dalına bağlamak için kullanışlı bir yol sağlar. Kullanıcı hikayenizi veya görevinizi, doğrudan onunla ilişkili koda bağlayabilirsiniz. 

Bir iş öğesini yeni bir dala bağlamak için, iş öğesinin yanındaki **Eylemler** üç nokta ( **...** ) simgesini seçin ve bağlam menüsünde, ' ye kaydırın ve **yeni dal**' ı seçin.  

![1\.](./media/collaborative-coding-with-git/1-sprint-board-view.png)

**Dal oluştur** iletişim kutusunda yeni dal adını ve temel Azure Repos git deposu ve dalını sağlayın. Temel Depo, iş öğesiyle aynı Azure DevOps projesinde olmalıdır. Temel dal, ana dal veya var olan başka bir dal olabilir. **Dal oluştur**' u seçin. 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Ayrıca, Windows veya Linux 'ta aşağıdaki git Bash komutunu kullanarak yeni bir dal oluşturabilirsiniz:

```bash
git checkout -b <new branch name> <base branch name>

```
> Bir \<temel dal adı belirtmezseniz, yeni dal temel alır `master`. 

Çalışma dalınıza geçiş yapmak için aşağıdaki komutu çalıştırın: 

```bash
git checkout <working branch name>
```

Çalışma dalına geçiş yaptıktan sonra, iş öğesini tamamlamaya yönelik kod veya belge yapıtları geliştirmeye başlayabilirsiniz. Çalışan `git checkout master` anahtarlar, `master` dala geri döner.

Her Kullanıcı hikayesi iş öğesi için bir git dalı oluşturmak iyi bir uygulamadır. Ardından, her görev iş öğesi için Kullanıcı hikayesi dalını temel alan bir dal oluşturabilirsiniz. Aynı proje için farklı Kullanıcı hikayeleri üzerinde çalışan birden fazla kişiye veya aynı kullanıcı hikayesine yönelik farklı görevlere sahip olduğunuzda, dalları Kullanıcı hikayesi-görev ilişkisine karşılık gelen bir hiyerarşide düzenleyin. Her bir takım üyesinin farklı bir dalda veya bir dalı paylaşırken farklı bir kodda ya da diğer yapıtlarda çalışmasını sağlayarak çakışmaları en aza indirmenize neden olabilirsiniz. 

Aşağıdaki diyagramda, TDSP için önerilen dallanma stratejisi gösterilmektedir. Özellikle bir proje üzerinde yalnızca bir veya iki kişi çalışırken ya da bir kullanıcı hikayesinin tüm görevlerinde yalnızca bir kişi çalıştığı zaman, burada gösterildiği gibi birçok dala gerek duymayabilir. Ancak, geliştirme dalını ana daldan ayırmak her zaman iyi bir uygulamadır ve yayın dalının geliştirme etkinlikleri tarafından kesintiye uğratılmasını önlemeye yardımcı olabilir. Git dal modelinin tamamının açıklaması için, bkz. [başarılı git dallanma modeli](https://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Ayrıca, bir iş öğesi var olan bir dala bağlayabilirsiniz. Bir iş öğesinin **ayrıntı** sayfasında **bağlantı ekle**' yi seçin. Ardından, iş öğesini bağlamak için mevcut bir dalı seçin ve **Tamam**' ı seçin. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name='WorkonaBranchandCommittheChanges-2'></a>Dalda çalışma ve değişiklikleri yapma 

Yerel makinenizin `script` dalına bir R betik dosyası eklemek gibi iş öğesi için bir değişiklik yaptıktan sonra, aşağıdaki git Bash komutlarını kullanarak yerel dalınızdan değişikliği yukarı akış çalışma dalına kaydedebilirsiniz:

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name='CreateapullrequestonVSTS-3'></a>Çekme isteği oluşturma

Bir veya daha fazla işleme ve gönderim sonrasında, geçerli çalışma dalınızı temel dalına birleştirmeye hazırsanız, Azure Repos bir *çekme isteği* oluşturabilir ve gönderebilirsiniz. 

Azure DevOps projenizin ana sayfasından, sol gezinti bölmesinde bulunan **Repos** > **çekme istekleri** ' ni işaret edin. Ardından **Yeni çekme isteği** düğmelerinden birini veya **çekme isteği oluştur** bağlantısını seçin.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

**Yeni çekme isteği** ekranında, gerekirse Değişikliklerinizi birleştirmek istediğiniz git deposuna ve dalına gidin. İstediğiniz diğer bilgileri ekleyin veya değiştirin. **Gözden geçirenler**altında, değişikliklerinizi gözden geçirmek için ihtiyacınız olan adların adlarını ekleyin ve ardından **Oluştur**' u seçin. 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name='ReviewandMerge-4'></a>Gözden geçirme ve birleştirme

Çekme isteğini oluşturduktan sonra, gözden geçirenler çekme isteğini gözden geçirmek için bir e-posta bildirimi alır. Gözden geçirenler değişikliklerin çalışıp çalışmadığını test edin ve mümkünse değişiklikleri istek sahibine göre kontrol edin. Gözden geçirenler yorum yapabilir, değişiklikler yapabilir ve değerlendirmesi temelinde çekme isteğini onaylayabilir veya reddedebilir. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

Gözden geçirenler değişiklikleri onayladıktan sonra, sizin veya birleştirme izinlerine sahip başka bir kişi çalışma dalını kendi temel dalına birleştirebilirler. **Tamam**' ı seçin ve ardından **çekme isteği Tamam** iletişim kutusunda **birleştirmeyi Tamam** ' ı seçin. Çalışma dalını birleştirdikten sonra silmeyi tercih edebilirsiniz. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

İsteğin **tamamlandı**olarak işaretlendiğinden emin olun. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Sol gezinmede yer alan **depoya** geri döndüğünüzde, `script` dal silindiğinden Ana dala geçtiniz görürsünüz.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Ayrıca, `script` çalışma dalını temel dalında birleştirmek ve Birleştirmeden sonra çalışma dalını silmek için aşağıdaki git Bash komutlarını da kullanabilirsiniz:

```bash
git checkout master
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>Sonraki adımlar

[Veri bilimi görevleri yürütme](execute-data-science-tasks.md) , etkileşimli veri araştırması, veri analizi, raporlama ve model oluşturma gibi çeşitli yaygın veri bilimi görevlerini tamamlamaya yönelik yardımcı programları nasıl kullanacağınızı gösterir.

[Örnek izlenecek yollar](walkthroughs.md) , bağlantılar ve küçük resim açıklamalarıyla belirli senaryolara yönelik izlenecek yolları listeler. Bağlantılı senaryolar, akıllı uygulamalar oluşturmak için bulut ve şirket içi araçların ve hizmetlerin iş akışları veya işlem hatları halinde nasıl birleştirileceğini gösterir. 

