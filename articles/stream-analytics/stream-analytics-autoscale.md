---
title: Stream Analytics işleri otomatik ölçeklendirme
description: Bu makalede, iş ölçümlerinin önceden tanımlanmış bir zamanlaması veya değerleri temelinde Stream Analytics işin nasıl otomatik olarak ekleneceği açıklanmaktadır
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/03/2020
ms.openlocfilehash: a8e089e302e9d40c69cf7ff2a3480c17894e1463
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016295"
---
# <a name="autoscale-stream-analytics-jobs-using-azure-automation"></a>Azure Otomasyonu 'Nu kullanarak işleri Stream Analytics otomatik ölçeklendirme

Otomatik ölçeklendirmeyi yapılandırarak Stream Analytics işlerinizin maliyetini iyileştirebilirsiniz. Otomatik ölçeklendirme, iş akışı birimleri (SUs), giriş yükinizdeki değişikliğe uyacak şekilde artar veya azalır. İşinizi daha fazla sağlamak yerine, gerekirse ölçeği değiştirebilir veya azaltabilirsiniz. İşlerinizi otomatik ölçeklendirme için yapılandırmanın iki yolu vardır:
1. Tahmin edilebilir bir giriş yükleriniz olduğunda **bir zamanlamayı önceden tanımlayın** . Örneğin, dayı sırasında giriş olaylarının daha yüksek bir oranını beklediğinizi ve işinizin daha fazla SUs ile çalışmasını isteyeceksiniz.
2. Tahmin edilebilir bir giriş yüklemeniz olmadığında **ölçeği artırma ve iş ölçümlerine göre ölçeği azaltma Işlemlerini tetikler** . Giriş olaylarının sayısı veya biriktirme listesi olayları gibi iş ölçümlerinizi temel alan SUs sayısını dinamik olarak değiştirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
İşiniz için otomatik ölçeklendirmeyi yapılandırmaya başlamadan önce aşağıdaki adımları izleyin.
1. İşiniz [paralel topolojiye](./stream-analytics-parallelization.md)sahip olacak şekilde iyileştirildi. Çalışma sırasında işinizin ölçeğini değiştirebiliyorsanız, işiniz paralel topolojiye sahiptir ve otomatik ölçeklendirme için yapılandırılabilir.
2. "RunAsAccount" seçeneği etkin olan [bir Azure Otomasyonu hesabı oluşturun](../automation/automation-create-standalone-account.md) . Bu hesabın Stream Analytics işlerinizi yönetme izinleri olmalıdır.

## <a name="set-up-azure-automation"></a>Azure Otomasyonu 'Nu ayarlama
### <a name="configure-variables"></a>Değişkenleri yapılandırma
Aşağıdaki değişkenleri Azure Otomasyonu hesabının içine ekleyin. Bu değişkenler, sonraki adımlarda açıklanan runbook 'larda kullanılacaktır.

| Ad | Tür | Değer |
| --- | --- | --- |
| **jobName** | Dize | Otomatik ölçeklendirme yapmak istediğiniz Stream Analytics işinizin adı. |
| **resourceGroupName** | Dize | İşinizin bulunduğu kaynak grubunun adı. |
| **Subıd** | Dize | İşinizin bulunduğu abonelik KIMLIĞI. |
| **ın, Sedsu** | Tamsayı | İşinizin bir zamanlamaya göre ölçeklendirilmesini istediğiniz yüksek SU değeri. Bu değer, çalışırken işinizin **Ölçek** ayarlarında gördüğünüz geçerli su seçeneklerinden biri olmalıdır. |
| **decreasedSU** | Tamsayı | İşinizin bir zamanlamaya göre ölçeklendirilmesini istediğiniz alt SU değeri. Bu değer, çalışırken işinizin **Ölçek** ayarlarında gördüğünüz geçerli su seçeneklerinden biri olmalıdır. |
| **maxSU** | Tamsayı | Yüklemeye göre otomatik ölçeklendirme yaparken, işinizin ölçeklendirilmesini istediğiniz maksimum SU değeri. Bu değer, çalışırken işinizin **Ölçek** ayarlarında gördüğünüz geçerli su seçeneklerinden biri olmalıdır. |
| **minSU** | Tamsayı | Yüklemeye göre otomatik ölçeklendirme yaparken, işinizin ölçeklenmesini istediğiniz en küçük SU değeri. Bu değer, çalışırken işinizin **Ölçek** ayarlarında gördüğünüz geçerli su seçeneklerinden biri olmalıdır. |

![Azure Otomasyonu 'nda değişken ekleme](./media/autoscale/variables.png)

### <a name="create-runbooks"></a>Runbook oluşturma
Sonraki adım iki PowerShell runbook oluşturmaktır. Biri ölçeği artırma ve diğeri de ölçek azaltma işlemleri için.
1. Azure Otomasyonu hesabınızda, **Işlem Otomasyonu** ' nun altındaki **runbook 'Lara** gidin ve **runbook oluştur**' u seçin.
2. Tür PowerShell olarak ayarlanan ilk Runbook *Scaleuprunbook 'unu* adlandırın. GitHub 'da bulunan [Scaleuprunbook PowerShell betiğini](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleUpRunbook.ps1) kullanın. Kaydedin ve yayımlayın.
3. Tür PowerShell ile *Scaledownrunbook* adlı başka bir runbook oluşturun. GitHub 'da bulunan [Scaledownrunbook PowerShell betiğini](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleDownRunbook.ps1) kullanın. Kaydedin ve yayımlayın.

![Azure Otomasyonu 'nda runbook 'ları otomatik ölçeklendirme](./media/autoscale/runbooks.png)

Artık Stream Analytics işteki ölçek artırma ve ölçek azaltma işlemlerini otomatik olarak tetikleyebilen runbook 'larınız var. Bu runbook 'lar önceden tanımlanmış bir zamanlama kullanılarak tetiklenebilir veya iş ölçümlerine göre dinamik olarak ayarlanabilir.

## <a name="autoscale-based-on-a-schedule"></a>Zamanlamaya göre otomatik ölçeklendirme
Azure Otomasyonu, runbook 'larınızı tetiklemek için bir zamanlama yapılandırmanıza olanak tanır.
1. Azure Otomasyonu hesabınızda, **paylaşılan kaynaklar** altında **zamanlamalar** ' ı seçin. Sonra **zamanlama Ekle**' yi seçin.
2. Örneğin, iki zamanlama oluşturabilirsiniz. İşinizin ölçeğini ne zaman ölçeğini istediğinizi ve işinizin ölçeğini ne zaman ölçeklendirmek istediğinizi temsil eden bir tane. Bu zamanlamalar için bir yinelenme tanımlayabilirsiniz.

   ![Azure Otomasyonu 'nda zamanlamalar](./media/autoscale/schedules.png)

3. **Scaleuprunbook** 'unuzu açın ve ardından **kaynaklar** altında **zamanlamalar** ' ı seçin. Daha sonra runbook 'unuzu önceki adımlarda oluşturduğunuz bir zamanlamaya bağlayabilirsiniz. Aynı runbook ile bağlantılı birden çok zamanlamalarınız olabilir. Bu işlem, günün farklı saatlerinde aynı ölçek işlemini çalıştırmak istediğinizde yararlı olabilir.

![Azure Otomasyonu 'nda runbook 'ları zamanlama](./media/autoscale/schedulerunbook.png)

1. **Scaledownrunbook** için önceki adımı tekrarlayın.

## <a name="autoscale-based-on-load"></a>Yüklemeye göre otomatik ölçeklendirme
Giriş yükünü tahmin edemeyebilirsiniz. Bu gibi durumlarda, minimum ve maksimum sınır içindeki adımlarda ölçeği artırma/azaltma daha uygundur. İş ölçümleri eşiğin üzerine veya altına gittiğinizde runbook 'ları tetiklemek için Stream Analytics işlerinizde uyarı kurallarını yapılandırabilirsiniz.
1. Azure Otomasyonu hesabınızda **MinSu** ve **maxsu** adlı iki tamsayı değişkeni oluşturun. Bu, işinizin içinde Ölçeklendirilecek sınırları ayarlar.
2. İki yeni runbook oluşturun. İş üzerindeki SUs 'un, **Maxsu** değerine kadar artışlarla artan olan [Stepscaleup PowerShell betiğini](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleUp.ps1) kullanabilirsiniz. Ayrıca, **MinSu** değerine ulaşılana kadar adımlarda işinizin SUs görüntüsünü azaltan [Stepscaleaşağı PowerShell betiğini](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleDown.ps1) de kullanabilirsiniz. Alternatif olarak, ölçeklendirmek istediğiniz belirli bir SU değeri varsa, önceki bölümdeki runbook 'ları kullanabilirsiniz.
3. Stream Analytics işiniz bölümünde, **izleme** altındaki **Uyarı kuralları** ' nı seçin. 
4. İki eylem grubu oluşturun. Biri ölçek artırma işlemi için ve diğeri de ölçek azaltma işlemi için kullanılır. **Işlemleri Yönet** ' i seçin ve ardından **eylem grubu Ekle**' ye tıklayın. 
5. Gerekli alanları doldurun. **Eylem türünü** seçerken **Otomasyon Runbook** 'u seçin. Uyarı tetiklendiğinde tetiklemek istediğiniz runbook 'u seçin. Sonra, eylem grubunu oluşturun.

   ![Eylem grubu oluşturma](./media/autoscale/create-actiongroup.png)
6. İşte [**Yeni bir uyarı kuralı**](./stream-analytics-set-up-alerts.md#set-up-alerts-in-the-azure-portal) oluşturun. Tercih ettiğiniz bir ölçümü temel alarak bir koşul belirtin. [ *Giriş olayları*, *su yüzdesi kullanımı* veya *biriktirme listesindeki giriş olayları* ,](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics) otomatik ölçeklendirme mantığını tanımlamak için kullanılması önerilen ölçümlerdir. Ayrıca, ölçek artırma işlemlerini tetiklerken 1 dakikalık *toplama ayrıntı düzeyi* ve *değerlendirme sıklığı* kullanılması önerilir. Bunun yapılması, işinizin giriş hacminde büyük ani artışlar sağlamak için büyük kaynaklar olmasını sağlar.
7. Son adımda oluşturulan eylem grubunu seçin ve uyarıyı oluşturun.
8. İş ölçümlerinin koşulu temelinde tetiklemek istediğiniz ek ölçeklendirme işlemleri için 2 ile 4 arasındaki adımları tekrarlayın.

İşinizi üretimde çalıştırmadan önce ölçek testlerini çalıştırmak en iyi uygulamadır. İşinizi değişen giriş yüklerine karşı test ettiğinizde, işinizin farklı giriş aktarım hızı için kaç tane SUs ihtiyacı olduğunu anlamalısınız. Bu, ölçeklendirme ve ölçek azaltma işlemlerini tetikleyen uyarı kurallarında tanımladığınız koşullara bilgi verebilir. 

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Stream Analytics paralelleştirilebilir sorgular oluşturma](stream-analytics-parallelization.md)
* [İş üretimini artırmak için Azure Stream Analytics işleri ölçeklendirin](stream-analytics-scale-jobs.md)