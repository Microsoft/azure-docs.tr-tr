---
title: Azure Otomasyonu hesabında izleyici görevi oluşturma
description: Bir klasörde oluşturulan yeni dosyaları izlemek için Azure Otomasyonu hesabında izleyici görevi oluşturmayı öğrenin.
services: automation
ms.service: automation
ms.subservice: process-automation
author: eamonoreilly
ms.author: eamono
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 75341fa2df6972dbf05542577d56ab35315919e6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989245"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Yerel bir makinedeki dosya değişikliklerini izlemek için bir Azure Otomasyonu izleyici görevi oluşturma

Azure Otomasyonu, PowerShell runbook 'ları ile olayları izlemek ve eylemleri tetiklemek için izleyici görevlerini kullanır. Bu öğreticide, bir dizine yeni bir dosya eklendiğinde izlenecek bir izleyici görevi oluşturma işlemi adım adım açıklanmaktadır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * İzleyici runbook 'unu içeri aktarma
> * Otomasyon değişkeni oluşturma
> * Eylem runbook 'u oluşturma
> * İzleyici görevi oluşturma
> * İzleyici tetikleyin
> * Çıktıyı inceleyin

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gereklidir:

* Azure aboneliği. Henüz bir aboneliğiniz yoksa [MSDN abone avantajlarınızı etkinleştirebilir](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ya da [ücretsiz hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) için kaydolabilirsiniz.
* İzleyici ve eylem runbook 'larını ve Izleyici görevini tutacak [Otomasyon hesabı](automation-offering-get-started.md) .
* İzleyici görevinin çalıştırıldığı [karma bir runbook worker](automation-hybrid-runbook-worker.md) .

> [!NOTE]
> İzleyici görevleri Azure Çin 'de desteklenmez.

## <a name="import-a-watcher-runbook"></a>İzleyici runbook 'unu içeri aktarma

Bu öğretici, bir dizindeki yeni dosyaları aramak için **Watch-NewFile** adlı bir izleyici runbook 'unu kullanır. İzleyici runbook 'u, son bilinen yazma süresini bir klasördeki dosyalara alır ve bu filigrandan daha yeni olan dosyalara bakar.

Bu içeri aktarma işlemi [PowerShell Galerisi](https://www.powershellgallery.com)aracılığıyla yapılabilir.

1. [Watch-NewFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd)için Galeri sayfasına gidin.
2. **Azure Otomasyonu** sekmesi altında **Azure Otomasyonu 'na dağıt**' a tıklayın.

Ayrıca, aşağıdaki adımları kullanarak bu runbook 'u portaldan Otomasyon hesabınıza aktarabilirsiniz.

1. Otomasyon hesabınızı açın ve **runbook 'lar** sayfasına tıklayın.
2. **Galeriye gözatamıyorum** düğmesine tıklayın.
3. "Izleyici runbook" ifadesini arayın, **bir dizinde yeni dosyalar için görünen izleyici runbook 'unu** seçin ve **içeri aktar**' ı seçin.
  ![Otomasyon Runbook 'unu kullanıcı arabiriminden içeri aktar](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Runbook 'a bir ad ve açıklama verin ve Runbook 'u Otomasyon hesabınıza aktarmak için **Tamam** ' ı seçin.
1. **Düzenle** ' yi seçin ve ardından **Yayımla**' ya tıklayın. İstendiğinde, runbook 'u yayımlamak için **Evet** ' i seçin.

## <a name="create-an-automation-variable"></a>Otomasyon değişkeni oluşturma

Bir [Otomasyon değişkeni](automation-variables.md) , önceki runbook 'un her bir dosyadan okuduğu ve depoladığı zaman damgalarını depolamak için kullanılır.

1. **PAYLAŞıLAN kaynaklar** altında **değişkenler** ' i seçin ve **+ değişken Ekle**' yi seçin.
1. Ad için "Watch-NewFileTimestamp" yazın
1. Tür için tarih saati seçin.
1. **Oluştur** düğmesine tıklayın. Bu, Otomasyon değişkenini oluşturur.

## <a name="create-an-action-runbook"></a>Eylem runbook 'u oluşturma

Bir işlem runbook 'u, bir izleyici runbook 'tan kendisine geçirilen veriler üzerinde işlem yapmak için bir izleyici görevinde kullanılır. PowerShell Iş akışı runbook 'ları izleyici görevleri tarafından desteklenmiyor, PowerShell runbook 'ları kullanmanız gerekir. **İşlem-NewFile**adlı önceden tanımlanmış bir eylem runbook 'unu içeri aktarmanız gerekir.

Bu içeri aktarma işlemi [PowerShell Galerisi](https://www.powershellgallery.com)aracılığıyla yapılabilir.

1. [Process-NewFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf)için Galeri sayfasına gidin.
2. **Azure Otomasyonu** sekmesi altında **Azure Otomasyonu 'na dağıt**' a tıklayın.

Ayrıca, aşağıdaki adımları kullanarak bu runbook 'u portaldan Otomasyon hesabınıza aktarabilirsiniz.

1. Otomasyon hesabınıza gidin ve **Işlem Otomasyonu** kategorisi altında **runbook 'lar** ' ı seçin.
1. **Galeriye gözatamıyorum** düğmesine tıklayın.
1. "Izleyici eylemi" araması yapın ve **bir izleyici runbook 'u tarafından tetiklenen olayları Işleyen izleyici eylemini** seçin ve **içeri aktar**' ı seçin.
  ![Eylem runbook 'unu kullanıcı arabiriminden içeri aktar](media/automation-watchers-tutorial/importsourceaction.png)
1. Runbook 'a bir ad ve açıklama verin ve Runbook 'u Otomasyon hesabınıza aktarmak için **Tamam** ' ı seçin.
1. **Düzenle** ' yi seçin ve ardından **Yayımla**' ya tıklayın. İstendiğinde, runbook 'u yayımlamak için **Evet** ' i seçin.

## <a name="create-a-watcher-task"></a>İzleyici görevi oluşturma

İzleyici görevi iki bölümden oluşur. İzleyici ve eylem. İzleyici izleyici görevinde tanımlanan bir aralıkta çalışır. İzleyici runbook 'una ait veriler runbook 'a geçirilir. Bu adımda, önceki adımlarda tanımlanan izleyici ve eylem runbook 'larına başvuran izleyici görevini yapılandırırsınız.

1. Otomasyon hesabınıza gidin ve **Işlem Otomasyonu** kategorisi altındaki **izleyici görevleri** ' ni seçin.
1. Izleyici görevleri sayfasını seçin ve **+ izleyici görevi Ekle** düğmesine tıklayın.
1. Ad olarak "WatchMyFolder" yazın.

1. İzleyiciyi **Yapılandır** ' ı seçin ve **Izle-NewFile** runbook 'unu seçin.

1. Parametreler için aşağıdaki değerleri girin:

   * **FOLDERPATH** -yeni dosyaların oluşturulduğu karma çalışan bir klasör. d:\examplefiles
   * **Uzantı** -tüm dosya uzantılarını işlemek için boş bırakın.
   * **Recurse** -bu değeri varsayılan olarak bırakın.
   * **Çalışma ayarları** -karma çalışanı seçin.

1. Tamam ' a tıklayın ve ardından izleyici sayfasına dönmek için öğesini seçin.
1. **Eylemi Yapılandır** ' ı seçin ve "Işlem-NewFile" runbook 'unu seçin.
1. Parametreler için aşağıdaki değerleri girin:

   * **Eventdata** -boş bırakın. Veriler izleyici runbook 'tan geçirilir.
   * **Çalıştır ayarları** -bu runbook 'un Otomasyon hizmetinde çalıştığından Azure olarak bırakın.

1. **Tamam**' a tıklayın ve ardından izleyici sayfasına dönmek Için öğesini seçin.
1. İzleyici görevini oluşturmak için **Tamam** ' ı tıklatın.

![Kullanıcı arabiriminden izleyici eylemini yapılandırma](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>İzleyici tetikleyin

İzleyicinin beklendiği gibi çalıştığını test etmek için bir test dosyası oluşturmanız gerekir.

Karma çalışana uzaktan. **PowerShell** 'i açın ve klasörde bir test dosyası oluşturun.

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

1. Otomasyon hesabınıza gidin ve **Işlem Otomasyonu** kategorisi altındaki **izleyici görevleri** ' ni seçin.
1. "WatchMyFolder" izleyici görevini seçin.
1. İzleyicinin yeni dosyayı bulup runbook 'u başlatmadığını görmek için **akışlar** ' ın altındaki **izleyici akışlarını görüntüle** ' ye tıklayın.
1. Runbook işlerini görmek için izleyiciyi **görüntüle eylem işleri**' ne tıklayın. Her iş seçilebilir ve işin ayrıntıları görüntülenir.

   ![Kullanıcı arabiriminden izleyici eylem işleri](media/automation-watchers-tutorial/WatcherActionJobs.png)

Yeni dosya bulunduğunda beklenen çıkış aşağıdaki örnekte görülebilir:

```output
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * İzleyici runbook 'unu içeri aktarma
> * Otomasyon değişkeni oluşturma
> * Eylem runbook 'u oluşturma
> * İzleyici görevi oluşturma
> * İzleyici tetikleyin
> * Çıktıyı inceleyin

Kendi runbook 'unuzu yazma hakkında daha fazla bilgi edinmek için bu bağlantıyı izleyin.

> [!div class="nextstepaction"]
> [Ilk PowerShell runbook 'Um](automation-first-runbook-textual-powershell.md).

