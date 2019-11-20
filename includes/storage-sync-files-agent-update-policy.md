---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: 02e9553b9704c96794e0c1113ab3e06458f0f7c8
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391821"
---
Azure Dosya Eşitleme Aracısı, yeni işlevsellik eklemek ve sorunları gidermek için düzenli olarak güncelleştirilir. Microsoft Update, Azure Dosya Eşitleme aracısına ait güncelleştirmeleri kullanıma almak için yapılandırmanızı öneririz.

#### <a name="major-vs-minor-agent-versions"></a>Birincil ve ikincil Aracı sürümleri
* Ana Aracı sürümleri genellikle yeni özellikler içerir ve sürüm numarasının ilk bölümü olarak artan bir sayı içerir. Örneğin: \*2. \*. \* @ no__t-3
* Alt Aracı sürümleri "yamalar" olarak da adlandırılır ve Ana sürümlerden daha sık yayımlanır. Bunlar genellikle hata düzeltmeleri ve daha küçük geliştirmeler içerir ancak yeni özellikler içermez. Örneğin: \* @ no__t-1.3. \* @ no__t-3

#### <a name="upgrade-paths"></a>Yükseltme yolları
Azure Dosya Eşitleme Aracısı güncelleştirmelerini yüklemek için dört onaylı ve test edilmiş yol vardır. 
1. **Sındadır Aracı güncelleştirmelerini otomatik olarak indirmek ve yüklemek için Microsoft Update yapılandırın.**  
    Sunucu aracısına yönelik en son düzeltmelerde erişiminizin olduğundan emin olmak için her zaman Azure Dosya Eşitleme güncelleştirme yapmanız önerilir. Microsoft Update, güncelleştirmeleri otomatik olarak indirip yükleyerek bu işlemi sorunsuz hale getirir.
2. **Aracı güncelleştirmelerini indirmek ve yüklemek için Afsgüncelleştirici. exe ' yi kullanın.**  
    Afsgüncelleştirici. exe aracı yükleme dizininde bulunur. Aracı güncelleştirmelerini indirmek ve yüklemek için çalıştırılabilire çift tıklayın. 
3. **Microsoft Update bir düzeltme eki dosyası veya. msp yürütülebilir dosyası kullanarak var olan bir Azure Dosya Eşitleme aracısına yama yapın. En son Azure Dosya Eşitleme güncelleştirme paketi [Microsoft Update kataloğundan](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync)indirilebilir.**  
    Bir. msp yürütülebilir dosyası çalıştırmak, Azure Dosya Eşitleme yüklemenizi önceki yükseltme yolundaki Microsoft Update otomatik olarak kullanılan yöntemle yükseltir. Microsoft Update bir düzeltme eki uygulandığında, Azure Dosya Eşitleme yüklemesinin yerinde yükseltilmesi gerçekleştirilir.
4. **En yeni Azure Dosya Eşitleme Aracı yükleyicisini [Microsoft Indirme merkezi](https://go.microsoft.com/fwlink/?linkid=858257)' nden indirin.**  
    Mevcut bir Azure Dosya Eşitleme Aracısı yüklemesini yükseltmek için eski sürümü kaldırın ve ardından indirilen yükleyiciden en son sürümü yüklemeniz gerekir. Sunucu kaydı, eşitleme grupları ve diğer tüm ayarlar Azure Dosya Eşitleme yükleyicisi tarafından korunur.

#### <a name="automatic-agent-lifecycle-management"></a>Otomatik aracı yaşam döngüsü yönetimi
Aracı sürümü 6 ile, dosya eşitleme ekibi bir aracı otomatik yükseltme özelliği sunmuştur. İki moddan birini seçebilir ve sunucuda yükseltmenin denendiği bir bakım penceresi belirtebilirsiniz. Bu özellik aracı yaşam döngüsü yönetimine yardımcı olmak için tasarlanmıştır. Bu özellik, aracılarınızın süresinin dolması veya sorunsuz bir şekilde izin vermesi için bir guardraıl sağlayarak, geçerli ayarı güncel tutun.
1. **Varsayılan ayar** , aracının süre sonunu engellemeye çalışır. Aracının, bir aracının deftere nakledildiği son kullanma tarihinden itibaren 21 gün içinde, aracı kendi kendine yükseltmeyi dener. Süre sonu ve seçilen bakım penceresinde 21 gün içinde bir hafta sonra bir kez yükseltme girişiminde yer alacak. **Bu seçenek, normal Microsoft Update düzeltme ekleri alma gereksinimini ortadan kaldırmaz.**
1. İsteğe bağlı olarak, yeni bir aracı sürümü kullanılabilir hale geldiğinde (Şu anda kümelenmiş sunucular için geçerli değildir), aracının otomatik olarak kendisini yükseltediğini seçebilirsiniz. Bu güncelleştirme, seçilen bakım penceresi sırasında gerçekleşir ve sunucunuzun, genel kullanıma sunulduğunda yeni özelliklerden ve geliştirmelerden yararlanmasına olanak tanır. Bu, ana aracı sürümlerinin yanı sıra sunucunuza düzenli güncelleştirme yamaları sağlayan önerilen, sorunsuz bir ayardır. Yayınlanan her aracı GA kalitesine göre yapılır. Bu seçeneği belirlerseniz, Microsoft en yeni aracı sürümünü size vermeyecektir. Kümelenmiş sunucular hariç tutulur. Fışıklandırma tamamlandıktan sonra, aracı [Microsoft Indirme merkezi](https://go.microsoft.com/fwlink/?linkid=858257) aka.MS/AFS/Agent ' de de kullanılabilir hale gelir.

 ##### <a name="changing-the-auto-upgrade-setting"></a>Otomatik yükseltme ayarını değiştirme

Aşağıdaki yönergelerde, değişiklikler yapmanız gerekiyorsa, yükleyiciyi tamamladıktan sonra ayarların nasıl değiştirileceği açıklanır.

Bir PowerShell konsolu açın ve eşitleme aracısını yüklediğiniz dizine gidin ve sunucu cmdlet 'lerini içeri aktarın. Bu, varsayılan olarak şuna benzer:
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name \StorageSync.Management.ServerCmdlets.dll
```

Geçerli ilke ayarını denetlemek ve değiştirmek istediğinizi öğrenmek için `Get-StorageSyncAgentAutoUpdatePolicy` ' yı çalıştırabilirsiniz.

Geçerli ilke ayarını gecikmeli güncelleştirme izlemesine göre değiştirmek için şunları kullanabilirsiniz:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

Geçerli ilke ayarını anında güncelleştirme izi olarak değiştirmek için şunları kullanabilirsiniz:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Aracı yaşam döngüsü ve değişiklik yönetimi garantisi
Azure Dosya Eşitleme, sürekli olarak yeni özellikler ve geliştirmeler sunan bir bulut hizmetidir. Bu, belirli bir Azure Dosya Eşitleme Aracısı sürümünün yalnızca sınırlı bir süre için desteklenebilir olduğu anlamına gelir. Dağıtımınızı kolaylaştırmak için aşağıdaki kurallar, değişiklik yönetimi işleminizde aracı güncelleştirmelerini/yükseltmelerini karşılamaya yetecek zaman ve bildirimin olduğunu garanti eder:

- Ana Aracı sürümleri ilk yayın tarihinden en az altı ay boyunca desteklenir.
- Ana Aracı sürümleri desteği arasında en az üç aydan oluşan bir çakışma olduğunu garanti ediyoruz. 
- Son kullanma süresi dolmadan önce en az üç ay sonra, kayıtlı sunucular için uyarılar verilir. Kayıtlı bir sunucunun, bir depolama eşitleme hizmeti 'nin kayıtlı sunucular bölümünde aracının daha eski bir sürümünü kullanıp kullan, kontrol edebilirsiniz.
- Bir alt Aracı sürümünün ömrü, ilişkili ana sürüme bağlanır. Örneğin, aracı sürümü 3,0 bırakıldığında, 2. \* Aracı sürümleri birlikte sona ermeyecek şekilde ayarlanır.

> [!Note]
> Bir aracı sürümünü bir süre sonu uyarısıyla yüklemek bir uyarı görüntüler, ancak başarılı olur. Zaman aşımına uğradı bir Aracı sürümüyle yüklenmeye veya bağlanmaya çalışılması desteklenmez ve engellenir.
