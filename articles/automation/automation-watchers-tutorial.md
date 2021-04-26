---
title: Azure Otomasyonu izleyici göreviyle güncelleştirilmiş dosyaları izleme
description: Bu makalede, bir klasörde oluşturulan yeni dosyaları izlemek için Azure Otomasyonu hesabında bir izleyici görevi oluşturma konusu açıklanır.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: ca4c4013e0044811a5bac8786996761b8a5c45f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97682753"
---
# <a name="track-updated-files-with-a-watcher-task"></a>Güncelleştirilmiş dosyaları izleyici göreviyle izleme

Azure Otomasyonu, PowerShell runbook 'ları ile olayları aramak ve eylemleri tetiklemek için bir izleyici görevi kullanır. İzleyici görevi iki bölümden oluşur, izleyici ve eylem. İzleyici runbook 'u izleyici görevinde tanımlanan bir aralıkla çalışır ve bir eylem runbook 'una veri çıkarır.

> [!NOTE]
> İzleyici görevleri Azure Çin Vianet 21 ' de desteklenmez.

> [!IMPORTANT]
> 2020 Mayıs 'tan başlayarak, Azure Logic Apps kullanımı, olayları izlemek, yinelenen görevleri zamanlamak ve eylemleri tetiklemek için önerilen ve desteklenen bir yoldur. Bkz. [Azure Logic Apps ile yinelenen otomatikleştirilmiş görevleri, işlemleri ve iş akışlarını zamanlama ve çalıştırma](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Bu makalede, bir dizine yeni bir dosya eklendiğinde izlenecek bir izleyici görevi oluşturma işlemi adım adım açıklanmaktadır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

* İzleyici runbook 'unu içeri aktarma
* Otomasyon değişkeni oluşturma
* Eylem runbook 'u oluşturma
* İzleyici görevi oluşturma
* İzleyici tetikleyin
* Çıktıyı inceleyin

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlayabilmeniz için aşağıdakiler gereklidir:

* Azure aboneliği. Henüz bir hesabınız yoksa [MSDN abone avantajlarınızı etkinleştirebilir](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)için kaydolabilirsiniz.
* İzleyici ve eylem runbook 'larını ve Izleyici görevini tutacak [Otomasyon hesabı](./index.yml) .
* İzleyici görevinin çalıştırıldığı [karma bir runbook worker](automation-hybrid-runbook-worker.md) .
* PowerShell runbook 'ları. PowerShell Iş akışı runbook 'ları izleyici görevleri tarafından desteklenmez.

## <a name="import-a-watcher-runbook"></a>İzleyici runbook 'unu içeri aktarma

Bu makale, bir dizindeki yeni dosyaları aramak için **Watch-NewFile** adlı bir izleyici runbook 'unu kullanır. İzleyici runbook 'u, son bilinen yazma süresini bir klasördeki dosyalara alır ve bu filigrandan daha yeni olan dosyalara bakar.

Runbook 'u [Azure Otomasyonu GitHub kuruluştan](https://github.com/azureautomation)indirebilirsiniz.

1. [Watch-NewFile.ps1](https://github.com/azureautomation/watcher-action-that-processes-events-triggerd-by-a-watcher-runbook)Için Azure Otomasyonu GitHub kuruluş sayfasına gidin.
2. Runbook 'u GitHub 'dan indirmek için sayfanın sağ tarafındaki **kodu** seçin ve ardından kodu bir ZIP dosyasına ındırmek için **zip 'i indir** ' i seçin.
3. İçeriği ayıklayın ve [runbook 'u içeri aktarın](manage-runbooks.md#import-a-runbook-from-the-azure-portal).

Ayrıca, aşağıdaki adımları kullanarak bu runbook 'u portaldan Otomasyon hesabınıza aktarabilirsiniz.

1. Otomasyon hesabınızı açın ve Runbook 'Lar sayfasına tıklayın.
2. **Tarayıcı galerisine** tıklayın ve **kaynak** açılan listesinden **GitHub**' ı seçin.
3. **İzleyici runbook 'u** ara, **bir dizinde yeni dosyalar için görünen izleyici runbook 'Unu** seçin ve **içeri aktar**' a tıklayın.
4. Runbook 'a bir ad ve açıklama verin ve Runbook 'u Otomasyon hesabınıza aktarmak için **Tamam** ' a tıklayın.
5. **Düzenle** ' yi seçin ve ardından **Yayımla**' ya tıklayın. İstendiğinde, runbook 'u yayımlamak için **Evet** ' e tıklayın.

## <a name="create-an-automation-variable"></a>Otomasyon değişkeni oluşturma

Bir [Otomasyon değişkeni](./shared-resources/variables.md) , önceki runbook 'un her bir dosyadan okuduğu ve depoladığı zaman damgalarını depolamak için kullanılır.

1. **Paylaşılan kaynaklar** altında **değişkenler** ' i seçin ve **+ bir değişken Ekle**' ye tıklayın.
1. Ad için **Watch-NewFileTimestamp** yazın.
1. Tür için TarihSaat ' i seçin.
1. Otomasyon değişkenini oluşturmak için **Oluştur** ' a tıklayın.

## <a name="create-an-action-runbook"></a>Eylem runbook 'u oluşturma

Bir işlem runbook 'u, bir izleyici runbook 'tan kendisine geçirilen veriler üzerinde işlem yapmak için bir izleyici görevinde kullanılır. [Azure Otomasyonu GitHub kuruluştan](https://github.com/azureautomation) **Process-NewFile** adlı önceden tanımlanmış bir eylem runbook 'unu içeri aktarmanız gerekir.

Bir eylem runbook 'u oluşturmak için:

1. [Process-NewFile.ps1](https://github.com/azureautomation/watcher-action-that-processes-events-triggerd-by-a-watcher-runbook)Için Azure Otomasyonu GitHub kuruluş sayfasına gidin.
2. Runbook 'u GitHub 'dan indirmek için sayfanın sağ tarafındaki **kodu** seçin ve ardından kodu bir ZIP dosyasına ındırmek için **zip 'i indir** ' i seçin.
3. İçeriği ayıklayın ve [runbook 'u içeri aktarın](manage-runbooks.md#import-a-runbook-from-the-azure-portal).

Ayrıca, bu runbook 'u Otomasyon hesabınıza Azure portal de aktarabilirsiniz:

1. Otomasyon hesabınıza gidin ve **Işlem Otomasyonu** altında **runbook 'lar** ' ı seçin.
1. **Tarayıcı galerisine** tıklayın ve **kaynak** açılan listesinden **GitHub**' ı seçin.
1. **İzleyici eylemi** için arama yapın, **bir izleyici runbook 'u tarafından tetiklenen olayları işleyen izleyici eylemini** seçin ve **içeri aktar**' a tıklayın.
1. Runbook 'a bir ad ve açıklama verin ve Runbook 'u Otomasyon hesabınıza aktarmak için **Tamam** ' a tıklayın.
1. **Düzenle** ' yi seçin ve ardından **Yayımla**' ya tıklayın. İstendiğinde, runbook 'u yayımlamak için **Evet** ' e tıklayın.

## <a name="create-a-watcher-task"></a>İzleyici görevi oluşturma

Bu adımda, önceki bölümlerde tanımlanan izleyici ve eylem runbook 'larına başvuran izleyici görevini yapılandırırsınız.

1. Otomasyon hesabınıza gidin ve **Işlem Otomasyonu** altında **izleyici görevleri** ' ni seçin.
1. Izleyici görevleri sayfasını seçin ve **+ izleyici görevi Ekle**' ye tıklayın.
1. Ad olarak **WatchMyFolder** girin.

1. İzleyiciyi **Yapılandır** ' ı seçin ve **Izle-NewFile** runbook 'unu seçin.

1. Parametreler için aşağıdaki değerleri girin:

   * **FOLDERPATH** -yeni dosyaların oluşturulduğu karma runbook çalışanındaki bir klasör; Örneğin, **d:\examplefiles**.
   * Yapılandırma için **uzantı** uzantısı. Tüm dosya uzantılarını işlemek için boş bırakın.
   * **Recurse** -özyinelemeli işlem. Bu değeri varsayılan olarak bırakın.
   * **Çalışma ayarları** -runbook 'u çalıştırma ayarı. Karma çalışanı seçin.

1. **Tamam**' a tıklayın ve ardından izleyici sayfasına dönmek Için öğesini **seçin** .
1. **Eylemi Yapılandır** ' ı seçin ve **Işlem-NewFile** runbook 'unu seçin.
1. Parametreler için aşağıdaki değerleri girin:

   * **Eventdata** -olay verileri. Boş bırakın. Veriler izleyici runbook 'tan geçirilir.
   * **Çalışma ayarları** -runbook 'u çalıştırma ayarı. Bu runbook Azure Otomasyonu 'nda çalıştığından Azure olarak bırakın.

1. **Tamam**' a tıklayın ve ardından izleyici sayfasına dönmek Için öğesini **seçin** .
1. İzleyici görevini oluşturmak için **Tamam** ' ı tıklatın.

    ![Kullanıcı arabiriminden izleyici eylemini yapılandırma](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>İzleyici tetikleyin

İzleyici görevinin beklendiği gibi çalıştığından emin olmak için, aşağıda açıklandığı gibi bir test çalıştırmanız gerekir. 

1. Karma runbook çalışanına uzak.
2. **PowerShell** 'i açın ve klasörde bir test dosyası oluşturun.

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

Aşağıdaki örnek, beklenen çıktıyı gösterir.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Çıktıyı inceleyin

1. Otomasyon hesabınıza gidin ve **Işlem Otomasyonu** altında **izleyici görevleri** ' ni seçin.
1. İzleyici görevi **WatchMyFolder** seçin.
1. İzleyicinin yeni dosyayı bulup runbook 'u başlatmadığını görmek için **akışlar** ' ın altındaki **izleyici akışlarını görüntüle** ' ye tıklayın.
1. Runbook işlerini görmek için **izleyici işlem Işlerini görüntüle**' ye tıklayın. İşin ayrıntılarını görüntülemek için her bir iş seçilebilir.

   ![Kullanıcı arabiriminden izleyici eylem işleri](media/automation-watchers-tutorial/WatcherActionJobs.png)

Yeni dosya bulunduğunda beklenen çıkış aşağıdaki örnekte görülebilir:

```output
Message is Process new file...

Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Sonraki adımlar

Kendi runbook 'unuzu yazma hakkında daha fazla bilgi için bkz. [PowerShell runbook oluşturma](learn/automation-tutorial-runbook-textual-powershell.md).
