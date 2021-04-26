---
title: Azure DevTest Labs ' de bekletme ilkesi ayarlama | Microsoft Docs
description: Bir bekletme ilkesi yapılandırmayı, fabrikası temizlemeyi ve eski görüntüleri DevTest Labs 'den devre dışı bırakmayı öğrenin.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4e642d7dc0733e55caa4ed62e3382ef4422030f9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727544"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Azure DevTest Labs 'de bekletme ilkesi ayarlama
Bu makalede bir bekletme ilkesi ayarlama, fabrikasını Temizleme ve eski görüntüleri kuruluştaki tüm diğer DevTest Labs 'den devre dışı bırakma konuları ele alınmaktadır. 

## <a name="prerequisites"></a>Önkoşullar
Devam etmeden önce bu makaleleri izlediğinizden emin olun:

- [Görüntü fabrikası oluşturma](image-factory-create.md)
- [Azure DevOps’tan bir görüntü fabrikası çalıştırma](image-factory-set-up-devops-lab.md)
- [Özel görüntüleri birden çok laboratuvara kaydetme ve dağıtma](image-factory-save-distribute-custom-images.md)

Şu öğeler zaten yerinde olmalıdır:

- Azure DevTest Labs görüntü fabrikası için laboratuvar
- Bir veya daha fazla hedef Azure DevTest Labs fabrikasının altın görüntüleri dağıtacaktır
- Görüntü fabrikasını otomatikleştirmek için kullanılan bir Azure DevOps projesi.
- Betikleri ve yapılandırmayı içeren kaynak kodu konumu (örneğimizde, yukarıda kullanılan DevOps projesinde)
- Azure PowerShell görevlerini düzenlemek için bir derleme tanımı
 
## <a name="setting-the-retention-policy"></a>Bekletme ilkesini ayarlama
Temizleme adımlarını yapılandırmadan önce, DevTest Labs 'de kaç geçmiş görüntünün kalmasını istediğinizi tanımlayın. [Azure DevOps 'dan bir görüntü fabrikası Çalıştır](image-factory-set-up-devops-lab.md) makalesini izlediyseniz, çeşitli yapı değişkenlerini yapılandırdınız. Bunlardan biri **ımageretden geliyor**. Bu değişkeni `1` , DevTest Labs özel görüntülerin geçmişini koruyamayacağı anlamına gelen olarak ayarlarsınız. Yalnızca en son dağıtılmış görüntüler kullanılabilir olacaktır. Bu değişkeni olarak değiştirirseniz `2` , en son dağıtılmış görüntü ve önceki geri olanlar korunur. Bu değeri, DevTest Labs içinde sürdürmek istediğiniz geçmiş görüntü sayısını tanımlamak için ayarlayabilirsiniz.

## <a name="cleaning-up-the-factory"></a>Fabrikası Temizleme
Fabrikasını temizlemede ilk adım, altın görüntü sanal makinelerini görüntü fabrikasından kaldırdır. Bu görevi, önceki betiklerimize benzer şekilde yapmak için bir betik vardır. İlk adım, aşağıdaki görüntüde gösterildiği gibi, derleme tanımına başka bir **Azure PowerShell** görevi eklemektir:

![PowerShell adımı](./media/set-retention-policy-cleanup/powershell-step.png)

Listede yeni görevi aldıktan sonra, öğeyi seçin ve aşağıdaki görüntüde gösterildiği gibi tüm ayrıntıları girin:

![Eski görüntüleri Temizle PowerShell görevi](./media/set-retention-policy-cleanup/configure-powershell-task.png)

Betik parametreleri: `-DevTestLabName $(devTestLabName)` .

## <a name="retire-old-images"></a>Eski görüntüleri devre dışı bırak 
Bu görev, eski görüntüleri kaldırır ve yalnızca **Imageretention** derleme değişkeniyle eşleşen bir geçmişi saklayın. Derleme tanımımız için ek bir **Azure PowerShell** derleme görevi ekleyin. Eklendikten sonra, görevi seçin ve aşağıdaki görüntüde gösterildiği gibi ayrıntıları girin: 

![Eski görüntüleri devre dışı bırakma PowerShell görevi](./media/set-retention-policy-cleanup/retire-old-image-task.png)

Betik parametreleri şunlardır: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Derlemeyi kuyruğa al
Artık derleme tanımını tamamladığınıza göre, her şeyin çalıştığından emin olmak için yeni bir derleme kuyruğa alın. Derleme başarıyla tamamlandıktan sonra yeni özel görüntüler hedef laboratuvarda görünür ve görüntü fabrikası laboratuvarını denetledikten, sağlanan VM yok görürsünüz. Ayrıca, daha fazla derleme sıranız varsa, derleme değişkenlerinde ayarlanan saklama değerine uygun olarak, DevTest Labs 'den eski özel görüntüleri devre dışı bırakarak temizleme görevlerini görürsünüz.

> [!NOTE]
> Serideki son makalenin sonunda derleme işlem hattını yürütülürse, yeni bir derlemeyi kuyruğa almadan önce görüntü fabrikası laboratuvarında oluşturulan sanal makineleri el ile silin.  El ile temizleme adımı yalnızca her şeyi ayarladığımızda ve çalıştığını doğruladıktan sonra gereklidir.



## <a name="summary"></a>Özet
Artık laboratuvarlarınızın talep üzerine özel görüntüler oluşturup dağıtabilecek çalışan bir görüntü fabrikası vardır. Bu noktada, görüntülerinizi doğru bir şekilde ayarlamayı ve hedef laboratuvarları tanımlamayı de amaçlanıyor olmanız yeterlidir. Önceki makalede belirtildiği gibi, **yapılandırma** klasörünüzde bulunan dosya **Labs.js** , hedef laboratuvarların her birinde hangi görüntülerin kullanılabilir hale getirilgerektiğini belirtir. Kuruluşunuza başka DevTest Labs eklerken, yeni laboratuvar için Labs.jsbir giriş eklemeniz yeterlidir.

Fabrikanıza yeni bir görüntü eklemek de basittir. Fabrikanıza yeni bir görüntü eklemek istediğinizde [Azure Portal](https://portal.azure.com)açın, Factory DevTest Labs ' e gidin, bir VM eklemek için düğmeyi seçin ve istediğiniz Market görüntüsünü ve yapıtları seçin. Yeni VM 'yi **oluşturmak Için oluştur** düğmesini seçmek yerine, **Azure Resource Manager şablonu görüntüle**' yi seçin ve şablonu, deponuzdaki **goldenımages** klasöründe yer alan bir. JSON dosyası olarak kaydedin. Görüntü fabrikanızı bir sonraki sefer çalıştırdığınızda özel görüntünüzü oluşturacaktır.


## <a name="next-steps"></a>Sonraki adımlar
1. Görüntü fabrikasını düzenli aralıklarla çalıştırmak için [derlemenizi/sürümünüzü zamanlayın](/azure/devops/pipelines/build/triggers?tabs=designer) . Fabrika tarafından üretilen görüntülerinizi düzenli aralıklarla yeniler.
2. Fabrikanızın daha fazla altın görüntüsünü oluşturun. Ayrıca sanal makine kurulum görevlerinizin ek parçalarını betiğe ve yapıtları fabrika yansımalarına dahil etmek için [yapıtlar oluşturmayı](devtest-lab-artifact-author.md) düşünebilirsiniz.
4. **DistributeImages** betiğini ayrı olarak çalıştırmak için [ayrı bir derleme/yayın](/azure/devops/pipelines/overview) oluşturun. Üzerinde Labs.jsdeğişiklikler yaptığınızda ve tüm görüntüleri yeniden oluşturmak zorunda kalmadan hedef laboratuvarlara kopyalanmış resimleri almak için bu betiği çalıştırabilirsiniz.

