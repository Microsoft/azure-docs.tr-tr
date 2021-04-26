---
title: Azure Otomasyonu VM'leri çalışma saatleri dışında başlat/durdur yapılandırma
description: Bu makalede, farklı kullanım durumlarını veya senaryolarını desteklemek üzere VM'leri çalışma saatleri dışında başlat/durdur özelliğinin nasıl yapılandırılacağı açıklanır.
services: automation
ms.subservice: process-automation
ms.date: 03/12/2021
ms.topic: conceptual
ms.openlocfilehash: ef8b4ddcbf09fa3ec0e3b07ae797693aed095c88
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104975"
---
# <a name="configure-startstop-vms-during-off-hours"></a>VM'leri çalışma saatleri dışında başlat/durdur Yapılandır

Bu makalede, [VM'leri çalışma saatleri dışında Başlat/Durdur](automation-solution-vm-management.md) özelliğinin açıklanan senaryoları destekleyecek şekilde nasıl yapılandırılacağı açıklanır. Şunları da öğrenebilirsiniz:

* [E-posta bildirimlerini yapılandırma](#configure-email-notifications)
* [VM ekleme](#add-a-vm)
* [VM hariç tutuma](#exclude-a-vm)
* [Başlatma ve kapatılma zamanlamalarını değiştirme](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>Senaryo 1: VM 'Leri zamanlamaya göre Başlat/Durdur

Bu senaryo, VM'leri çalışma saatleri dışında başlat/durdur ilk kez dağıttığınızda varsayılan yapılandırmadır. Örneğin, bu özelliği bir abonelik genelindeki tüm VM 'Leri durdurmak için yapılandırabilir ve siz de ofisiniz geri döndüğünüzde sabah sabah 'da başlatabilirsiniz. Dağıtım sırasında **Zamanlanmış-startvm** ve **Zamanlanmış-stopvm** zamanlamalarını yapılandırdığınızda, hedeflenen VM 'leri başlatıp durdurur. 

Özelliği yalnızca VM 'Leri durduracak şekilde yapılandırmak desteklenir. Özel bir zamanlamayı nasıl yapılandıracağınızı öğrenmek için bkz. [Başlangıç ve kapalı zamanlamalarını değiştirme](#modify-the-startup-and-shutdown-schedules) .

> [!NOTE]
> Zamanlama saati parametresini yapılandırdığınızda, özelliği tarafından kullanılan saat dilimi geçerli saat diliminiz. Ancak Azure Otomasyonu, Azure Otomasyonu 'nda UTC biçiminde depolar. Makine dağıtımı sırasında işlendiği için herhangi bir zaman dilimi dönüştürmesi yapmanız gerekmez.

Kapsamdaki VM 'Leri denetlemek için, değişkenlerini yapılandırın: `External_Start_ResourceGroupNames` , `External_Stop_ResourceGroupNames` ve `External_ExcludeVMNames` .

Eylemi bir abonelik ve kaynak grubuna karşı hedefleyerek veya VM 'lerin belirli bir listesini hedefleyerek, her ikisini birden değil de etkinleştirebilirsiniz.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Başlat ve durdur eylemlerini bir aboneliğe ve kaynak grubuna göre hedefleyin

1. `External_Stop_ResourceGroupNames`Ve değişkenlerini, `External_ExcludeVMNames` hedef VM 'leri belirtecek şekilde yapılandırın.

1. **Zamanlanmış-startvm** ve **Zamanlanmış-stopvm** zamanlamalarını etkinleştirin ve güncelleştirin.

1. Değişikliklerinizi önizlemek için **eylem** parametresi alanı **Start** ve **whatIf** parametre alanı true olarak ayarlanmış **ScheduledStartStop_Parent** runbook 'u çalıştırın.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>VM listesine göre Başlat ve Durdur eylemini hedefleme

1. **ScheduledStartStop_Parent** runbook 'u **başlatmak** için ayarla **eylemini** çalıştırın.

1. **Vmlist** parametre alanına VM 'lerin virgülle ayrılmış bir listesini (boşluk olmadan) ekleyin. Örnek bir liste `vm1,vm2,vm3` .

1. Değişikliklerinizin önizlemesine bakmak için **whatIf** parametre alanını true olarak ayarlayın.

1. Değişkeni, virgülle `External_ExcludeVMNames` ayrılmış değerler arasında boşluk olmadan, virgülle ayrılmış VM 'lerin (VM1, VM2, VM3) bir listesiyle yapılandırın.

1. Bu senaryo ve değişkenlerini dikkate almaz `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupnames` . Bu senaryo için kendi Otomasyon zamanlamanızı oluşturmanız gerekir. Ayrıntılar için bkz. [Azure Otomasyonu 'nda runbook 'U zamanlama](shared-resources/schedules.md).

    > [!NOTE]
    > **Hedef ResourceGroup adları** için değeri hem hem de için değerler olarak depolanır `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` . Daha fazla ayrıntı düzeyi için, bu değişkenlerin her birini farklı kaynak gruplarını hedefleyecek şekilde değiştirebilirsiniz. Başlat eylemi için, kullanın `External_Start_ResourceGroupNames` ve `External_Stop_ResourceGroupNames` Durdur eylemini kullanın. VM 'Ler başlatma ve durdurma zamanlamalarına otomatik olarak eklenir.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Senaryo 2: etiketleri kullanarak sırayla VM 'Leri başlatma/durdurma

Dağıtılmış bir iş yükünü destekleyen birden çok VM üzerinde iki veya daha fazla bileşen içeren bir ortamda, bileşenlerin başlatıldığı ve sırasıyla durdurulduğu sırayı destekleme. 

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Başlat ve durdur eylemlerini bir aboneliğe ve kaynak grubuna göre hedefleyin

1. `sequencestart` `sequencestop` Ve değişkenlerini hedefleyen VM 'lere pozitif tamsayı değerleri olan bir ve etiketi ekleyin `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` . Başlat ve Durdur eylemleri artan sırada gerçekleştirilir. Bir VM 'nin nasıl etiketleyeceğinizi öğrenmek için bkz. Azure ['Da Windows sanal makinesini etiketleme](../virtual-machines/tag-portal.md) ve [Azure 'da Linux sanal makinesini etiketleme](../virtual-machines/tag-cli.md).

1. **Sıralı-startvm** ve **sıralı-stopvm** zamanlamalarını gereksinimlerinize uygun tarih ve saate göre değiştirin ve zamanlamayı etkinleştirin.

1. Değişikliklerinizi önizlemek için **Start** ve **whatIf** olarak ayarlanan **ACTION** **SequencedStartStop_Parent** runbook 'unu doğru olarak ayarlayın.

1. Üretim VM 'lerine göre uygulamadan önce eylemin önizlemesini görüntüleyin ve gerekli değişiklikleri yapın. Hazırlandığınızda, parametresini **false** olarak ayarlanmış şekilde el ile yürütün veya önceden belirlenmiş zamanlamalarınızın ardından otomatik olarak **sıralı-startvm** ve **sıralı-stopvm** olarak çalışır.

### <a name="target-the-start-and-stop-actions-by-vm-list"></a>Başlat ve durdur eylemlerini VM listesine göre hedefle

1. `sequencestart` `sequencestop` Parametreye eklemeyi planladığınız VM 'lere pozitif tamsayı değerleri olan bir ve bir etiketi ekleyin `VMList` .

1. **SequencedStartStop_Parent** runbook 'u **başlatmak** için ayarla **eylemini** çalıştırın.

1. **Vmlist** parametre alanına VM 'lerin virgülle ayrılmış bir listesini (boşluk olmadan) ekleyin. Örnek bir liste `vm1,vm2,vm3` .

1. Değişikliklerinizi önizlemek için **whatIf** değerini true olarak ayarlayın. 

1. `External_ExcludeVMNames`Değişkeni, virgülle ayrılmış değerler arasında boşluk olmadan, virgülle ayrılmış VM 'ler listesiyle yapılandırın.

1. Bu senaryo ve değişkenlerini dikkate almaz `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupnames` . Bu senaryo için kendi Otomasyon zamanlamanızı oluşturmanız gerekir. Ayrıntılar için bkz. [Azure Otomasyonu 'nda runbook 'U zamanlama](shared-resources/schedules.md).

1. Üretim VM 'lerine göre uygulamadan önce eylemin önizlemesini görüntüleyin ve gerekli değişiklikleri yapın. Hazırlandığınızda, **izleme-ve-Tanılama/izleme-eylem-groupsrunbook** ' u, parametresini **false** olarak ayarlanmış şekilde el ile yürütün. Alternatif olarak, Otomasyon zamanlamalarının **sıralı-startvm** ve **sıralı-stopvm** ' y i belirtilen zamanlamanızı izleyerek otomatik olarak çalıştırmasını sağlar.

## <a name="scenario-3-start-or-stop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Senaryo 3: CPU kullanımına göre otomatik olarak Başlat veya Durdur

VM'leri çalışma saatleri dışında başlat/durdur, saat sonu gibi yoğun olmayan dönemler sırasında kullanılmayan makineleri değerlendirerek ve işlemci kullanımı belirtilen yüzdeden küçük olduğunda otomatik olarak kapatılabilen Azure Resource Manager ve klasik VM 'Lerin çalışma maliyetini yönetmenize yardımcı olabilir.

Varsayılan olarak, özellik, ortalama kullanımın yüzde 5 veya daha az olup olmadığını görmek için CPU Ölçümü yüzdesini değerlendirmek üzere önceden yapılandırılmıştır. Bu senaryo aşağıdaki değişkenlerle denetlenir ve varsayılan değerler gereksinimlerinizi karşılamıyorsa değiştirilebilir:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

Eylemi bir abonelik ve kaynak grubuna karşı etkinleştirebilir ve hedefleyebilir ya da belirli bir VM listesini hedefleyebilirsiniz.

**AutoStop_CreateAlert_Parent** runbook 'unu çalıştırdığınızda, hedeflenen aboneliğin, kaynak grubunun ve VM 'lerin mevcut olduğunu doğrular. VM 'Ler mevcutsa, runbook üst runbook tarafından doğrulanan her VM için **AutoStop_CreateAlert_Child** runbook 'u çağırır. Bu alt runbook:

* Doğrulanan her VM için bir ölçüm uyarı kuralı oluşturur.
* CPU, belirtilen zaman aralığı boyunca yapılandırılan eşiğin altına düşerse belirli bir sanal makine için **AutoStop_VM_Child** runbook 'unu tetikler. 
* VM 'yi durdurmaya çalışır.

### <a name="target-the-autostop-action-against-all-vms-in-a-subscription"></a>Bir abonelikteki tüm VM 'Lere karşı, oto durdurma eylemini hedefleme

1. `External_Stop_ResourceGroupNames`Değişkenin boş veya * (joker karakter) olarak ayarlandığından emin olun.

1. Seçim Bazı sanal makineleri yeniden durdur eyleminden dışlamak istiyorsanız, değişkene ayrılmış bir VM adları listesi ekleyebilirsiniz `External_ExcludeVMNames` .

1. Aboneliğinizdeki tüm VM 'Ler için gereken sanal makine ölçümü uyarı kurallarını oluşturmak üzere **Schedule_AutoStop_CreateAlert_Parent** zamanlamasını çalıştırmaya izin vermeyi etkinleştirin. Bu tür bir zamanlamayı çalıştırmak, aboneliğe yeni VM 'Ler eklendikçe yeni ölçüm uyarı kuralları oluşturmanıza olanak sağlar.

### <a name="target-the-autostop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>Bir kaynak grubundaki veya birden çok kaynak grubundaki tüm VM 'Lerde, oto durdurma eylemini hedefleyin

1. Değişkene, kaynak grubu adlarının virgülle ayrılmış bir listesini ekleyin `External_Stop_ResourceGroupNames` .

1. Bazı sanal makineleri yeniden Durdur ' dan dışlamak istiyorsanız, değişkenine bir VM adları virgülle ayrılmış listesi ekleyebilirsiniz `External_ExcludeVMNames` .

1. Kaynak gruplarınızın tüm VM 'Leri için gereken sanal makine ölçümü uyarı kurallarını oluşturmak üzere **Schedule_AutoStop_CreateAlert_Parent** zamanlamasını çalıştırmaya izin vermeyi etkinleştirin. Bu işlemi bir zamanlamaya göre çalıştırmak, yeni VM 'Ler kaynak gruplarına eklendikçe yeni ölçüm uyarı kuralları oluşturmanıza olanak sağlar.

### <a name="target-the-autostop-action-to-a-list-of-vms"></a>Oto durdur eylemini VM 'lerin bir listesine hedefleyin

1. Yeni bir [zamanlama](shared-resources/schedules.md#create-a-schedule) oluşturun ve bu parametreyi **AutoStop_CreateAlert_Parent** runbook 'una bağlayın, bu da PARAMETREYE virgülle ayrılmış bir VM adları listesi ekler `VMList` .

1. İsteğe bağlı olarak, bazı VM 'Leri tekrar durdurma eyleminden dışlamak istiyorsanız, değişkene ayrılmış bir VM adları listesi (boşluk olmadan) ekleyebilirsiniz `External_ExcludeVMNames` .

## <a name="configure-email-notifications"></a>E-posta bildirimlerini yapılandırma

VM'leri çalışma saatleri dışında başlat/durdur dağıtıldıktan sonra e-posta bildirimlerini değiştirmek için, dağıtım sırasında oluşturulan eylem grubunu değiştirebilirsiniz.  

> [!NOTE]
> Azure Kamu bulutundaki abonelikler, bu özelliğin e-posta işlevlerini desteklemez.

1. Azure portal, **izleme** altındaki **Uyarılar** ' a ve ardından **eylemleri Yönet**' e tıklayın. **Eylemleri Yönet** sayfasında, **eylem grupları** sekmesinde olduğunuzdan emin olun. **StartStop_VM_Notification** adlı eylem grubunu seçin.

    :::image type="content" source="media/automation-solution-vm-management/azure-monitor-sm.png" alt-text="Izleyici-eylem grupları sayfasının ekran görüntüsü." lightbox="media/automation-solution-vm-management/azure-monitor-lg.png":::

1. **StartStop_VM_Notification** sayfasında **temel bilgiler** bölümü sizin için doldurulacak ve **görünen ad** alanı dışında düzenlenemez. Adı düzenleyin veya önerilen adı kabul edin. **Bildirimler** bölümünde, eylem ayrıntılarını düzenlemek için kalem simgesine tıklayın. Bu, **e-posta/SMS ileti/gönderim/ses** bölmesini açar. E-posta adresini güncelleştirip **Tamam** ' a tıklayarak değişikliklerinizi kaydedin.

    :::image type="content" source="media/automation-solution-vm-management/change-email.png" alt-text="Güncelleştirilmiş örnek bir e-posta adresi gösteren e-posta/SMS ileti/gönderim/ses sayfasının ekran görüntüsü.":::

    Eylem grubuna ek eylemler ekleyebilirsiniz. Eylem grupları hakkında daha fazla bilgi edinmek için bkz. [eylem grupları](../azure-monitor/platform/action-groups.md)

Aşağıda, özelliği sanal makineleri kapattığında gönderilen örnek bir e-posta verilmiştir.

:::image type="content" source="media/automation-solution-vm-management/email.png" alt-text="Özellik sanal makineleri kapattığında gönderilen örnek bir e-postanın ekran görüntüsü." lightbox="media/automation-solution-vm-management/email.png":::

## <a name="add-or-exclude-vms"></a><a name="add-exclude-vms"></a>VM 'Leri ekleme veya dışlama

Özelliği, hedeflenecek veya hariç tutulacak VM eklemenize olanak tanır. 

### <a name="add-a-vm"></a>VM ekleme

Özelliği çalışırken bir VM 'nin dahil edilmesini sağlamanın iki yolu vardır:

* Özelliğin üst [runbook](automation-solution-vm-management.md#runbooks) 'larının her biri bir `VMList` parametreye sahiptir. Koşullarınız için uygun üst runbook 'u zamanlarken bu parametreye, virgülle ayrılmış bir VM adı listesi (boşluk olmadan) geçirebilirsiniz ve bu VM 'Ler Özellik çalıştırıldığında dahil edilir.

* Birden çok VM seçmek için, `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` başlatmak veya durdurmak Istediğiniz VM 'leri içeren kaynak grubu adlarını ayarlayın. Ayrıca, `*` özelliğin abonelikteki tüm kaynak grupları üzerinde çalışmasını sağlamak için değişkenlerini değerine ayarlayabilirsiniz.

### <a name="exclude-a-vm"></a>VM hariç tutuma

VM 'Leri, çalışma saatleri dışında durdurma/başlatma VM 'lerinden dışlamak için, adını `External_ExcludeVMNames` değişkenine ekleyebilirsiniz. Bu değişken, özelliğin dışında tutulacak belirli VM 'lerin (boşluk olmadan) virgülle ayrılmış listesidir. Bu liste 140 VM 'Lerle sınırlıdır. Bu listeye 140 'den fazla VM eklerseniz, dışarıda bırakılacak şekilde ayarlanan VM 'Ler istenmeden başlatılabilir veya durmuş olabilir.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Başlatma ve kapatılma zamanlamalarını değiştirme

Bu özellikte başlangıç ve kapalı zamanlamaların yönetilmesi, [Azure Otomasyonu 'nda Runbook zamanlama](shared-resources/schedules.md)bölümünde özetlenen adımların aynısını izler. VM 'Leri başlatmak ve durdurmak için ayrı zamanlamalar gereklidir.

Özelliği belirli bir zamanda yalnızca VM 'Leri durduracak şekilde yapılandırmak desteklenir. Bu senaryoda, yalnızca bir Dur zamanlaması oluşturmanız ve buna karşılık gelen bir başlangıç zamanlaması yapmanız gerekir. 

1. Değişkende kapatılacak VM 'Ler için kaynak gruplarını eklemiş olduğunuzdan emin olun `External_Stop_ResourceGroupNames` .

1. VM 'Leri kapatmak istediğiniz zaman için kendi zamanlamanızı oluşturun.

1. **ScheduledStartStop_Parent** runbook 'a gidin ve **zamanla**' ya tıklayın. Bu, önceki adımda oluşturduğunuz zamanlamayı seçmenizi sağlar.

1. **Parametreler ' i seçin ve ayarları çalıştırın** ve **eylem** alanını **Durdur** olarak ayarlayın.

1. Değişikliklerinizi kaydetmek için **Tamam**’ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

* İşlem sırasında özelliği izlemek için bkz. [VM'leri çalışma saatleri dışında Başlat/Durdur sorgu günlükleri](automation-solution-vm-management-logs.md).
* VM yönetimi sırasında sorunları işlemek için bkz. [VM'leri çalışma saatleri dışında Başlat/Durdur sorunları giderme](troubleshoot/start-stop-vm.md).
