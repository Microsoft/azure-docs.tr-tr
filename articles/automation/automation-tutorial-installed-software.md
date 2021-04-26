---
title: Azure Otomasyonu ile sanal makinelerinize hangi yazılımların yüklendiğini öğrenin | Microsoft Docs
description: Bu makalede, ortamınızdaki sanal makinelerde yüklü yazılımlar açıklanmaktadır.
services: automation
keywords: envanter, otomasyon, değişiklik izleme
ms.date: 04/11/2018
ms.topic: tutorial
ms.subservice: change-inventory-management
ms.custom: mvc
ms.openlocfilehash: 8451067ae86e95269c0c2f22554e1654ac64c1e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593789"
---
# <a name="discover-what-software-is-installed-on-your-vms"></a>Sanal makinelerinize hangi yazılımın yüklendiğini öğrenin

Bu öğreticide, ortamınızda hangi yazılımların yüklü olduğunu öğrenmek için Azure Otomasyonu Değişiklik İzleme ve envanter özelliğini kullanmayı öğreneceksiniz. Bilgisayarlarınızda yazılım, dosya, Linux Daemon 'ları, Windows Hizmetleri ve Windows kayıt defteri anahtarlarının envanterini toplayabilir ve görüntüleyebilirsiniz. Makinelerinizin yapılandırmasını izlemek ortamınızdaki işletimsel sorunları bulmanıza ve makinelerinizin durumunu daha iyi anlamanıza yardımcı olabilir.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Değişiklik İzlemeyi ve Sayımı Etkinleştirme
> * Azure VM 'yi etkinleştirme
> * Azure olmayan bir VM 'yi etkinleştirme
> * Yüklü olan yazılımları görüntüleme
> * Yüklü olan yazılımların stok günlüklerinde arama yapma

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

* Azure aboneliği. Henüz bir hesabınız yoksa [MSDN abone avantajlarınızı etkinleştirebilir](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)için kaydolabilirsiniz.
* İzleyici ve eylem runbook 'larını ve Izleyici görevini tutacak bir [Otomasyon hesabı](./index.yml) .
* Özelliği için etkinleştirilecek bir [sanal makine](../virtual-machines/windows/quick-create-portal.md) .

## <a name="log-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="enable-change-tracking-and-inventory"></a>Değişiklik İzlemeyi ve Sayımı Etkinleştirme

Bu öğreticide ilk yapmanız gereken Değişiklik İzleme ve Stok özelliklerini etkinleştirmektir. Özelliği daha önce etkinleştirdiyseniz, bu adım gerekli değildir.

>[!NOTE]
>Alanlar gri ise, VM için başka bir Otomasyon özelliği etkinleştirilir ve aynı çalışma alanını ve otomasyon hesabını kullanmanız gerekir.

1. Otomasyon hesabınıza gidin ve **yapılandırma yönetimi** altında **Envanter** veya **değişiklik izleme** ' yi seçin.

2. [Log Analytics](../azure-monitor/logs/log-query-overview.md) çalışma alanını seçin. Bu çalışma alanı, Değişiklik İzleme ve envanter gibi özellikler tarafından oluşturulan verileri toplar. Çalışma alanı, birden fazla kaynaktan alınan verilerin incelenip analiz edilebileceği ortak bir konum sağlar.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

3. Kullanılacak Otomasyon hesabını seçin.

4. Dağıtımın konumunu yapılandırın.

5. VM 'niz için özelliği dağıtmak üzere **Etkinleştir** ' e tıklayın. 

    ![Envanter yapılandırma başlığı](./media/automation-tutorial-installed-software/enableinventory.png)

Kurulum sırasında VM, Windows için Log Analytics Aracısı ve [karma Runbook Worker](automation-hybrid-runbook-worker.md)ile sağlanır. Değişiklik İzleme ve stokun etkinleştirilmesi 15 dakika kadar sürebilir. Bu süre boyunca tarayıcı penceresini kapatmamanız gerekir.

Özellik etkinleştirildikten sonra, sanal makinede yüklü yazılımlar ve değişiklikler hakkında bilgiler Azure Izleyici günlüklerine akar. Verilerin çözümlemeye hazır hale gelmesi 30 dakika ile 6 saat arasında sürebilir.

## <a name="add-an-azure-vm-to-change-tracking-and-inventory"></a>Değişiklik İzleme ve envantere bir Azure VM ekleme

1. Otomasyon hesabınızda, **yapılandırma yönetimi** altında **Stok** veya **değişiklik izleme** ' ye gidin.

2. **+ Azure VM Ekle**' yi seçin.

3. VM 'lerin listesinden sanal makinelerinizi seçin. 

4. VM 'de Değişiklik İzleme ve envanteri etkinleştirmek için **Etkinleştir** ' e tıklayın. Windows için Log Analytics Aracısı sanal makineye dağıtılır ve sanal makineyi Log Analytics çalışma alanıyla konuşacak şekilde yapılandırır. Kurulum işlemi birkaç dakika sürebilir. 

5. Bu noktada, isterseniz özelliği etkinleştirmek için listeden yeni bir VM seçebilirsiniz.

## <a name="add-a-non-azure-machine-to-change-tracking-and-inventory"></a>Değişiklik İzleme ve envantere Azure dışı bir makine ekleme

Özelliği için Azure dışı makineleri etkinleştirmek üzere:

1. İşletim sisteminize bağlı olarak, [Windows için Log Analytics aracısını](../azure-monitor/agents/agent-windows.md) veya [Linux için Log Analytics aracısını](automation-linux-hrw-install.md)yükler. 

2. Otomasyon hesabınıza gidin ve **yapılandırma yönetimi** altında **Stok** veya **değişiklik izleme** ' ye gidin. 

3. **Makineleri Yönet**' e tıklayın. Log Analytics çalışma alanınıza rapor veren ve Değişiklik İzleme ve envanterin etkinleştirildiği makinelerin listesini görürsünüz. Ortamınız için uygun seçeneği belirleyin:

    * **Kullanılabilir tüm makinelerde etkinleştir** -Bu seçenek, şu anda Log Analytics çalışma alanınıza rapor veren tüm makinelerdeki özelliği etkinleştirir.
    * **Tüm kullanılabilir makinelerde ve gelecekteki makinelerde etkinleştir** -Bu seçenek, tüm makinelerdeki Log Analytics çalışma alanınıza rapor veren özelliği ve ardından çalışma alanına eklenen tüm gelecekteki makineleri etkinleştirir.
    * **Seçili makinelerde etkinleştir** -Bu seçenek özelliği yalnızca seçtiğiniz makinelerde etkinleştirir.

    ![Makineleri Yönetme](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>Yüklü olan yazılımları görüntüleme

Değişiklik İzleme ve envanter özelliği etkinleştirildikten sonra, sonuçları envanter sayfasında görüntüleyebilirsiniz.

1. Otomasyon hesabınızda, **yapılandırma yönetimi** altında **Stok** ' u seçin.

2. Stok sayfasında **Yazılım** sekmesine tıklayın.

3. Bulunan yazılımı listeleyen bir tablo unutmayın. Yazılımlar ada ve sürüme göre gruplara ayrılmıştır. Tabloda her yazılım kaydı hakkındaki üst düzey ayrıntılar görüntülenebilir. Bu ayrıntılar arasında yazılım adı, sürümü, yayımcı, son yenileme zamanı (grup içindeki bir makine tarafından bildirilen en son yenileme zamanı) ve makine sayısı (bu yazılıma sahip makinelerin sayısı) yer alır.

    ![Yazılım envanteri](./media/automation-tutorial-installed-software/inventory-software.png)

4. Yazılım kaydının özelliklerini ve bu yazılımın yüklü olduğu makinelerin adlarını görüntülemek için satırlardan birine tıklayın.

5. Belirli bir yazılımı veya yazılım grubunu aramak için yazılım listesinin üstünde yer alan metin kutusundan arama yapabilirsiniz.
Filtre yazılım adı, sürümü veya yayımcı ile arama yapmanızı sağlar. Örneğin, **contoso** araması, **contoso** içeren bir ad, yayımcı veya sürüm ile tüm yazılımları döndürür.

## <a name="search-inventory-logs-for-installed-software"></a>Yüklü olan yazılımların stok günlüklerinde arama yapma

Değişiklik İzleme ve envanter, Azure Izleyici günlüklerine gönderilen günlük verileri oluşturur. Sorguları çalıştırarak günlüklerde arama yapmak için, envanter sayfasının en üstünde **Log Analytics** ' yi seçin. Envanter verileri, türü altında depolanır `ConfigurationData` .

Aşağıdaki örnek Log Analytics sorgu, yayımcı Microsoft Corporation için envanter sonuçlarını döndürür.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Azure Izleyici günlüklerinde günlük dosyalarını çalıştırma ve arama hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici günlükleri](../azure-monitor/logs/log-query-overview.md).

## <a name="see-the-software-inventory-for-a-single-machine"></a>Tek bir makine için yazılım envanterini inceleyin

Tek bir makinenin yazılım envanterini görmek için, Azure VM kaynak sayfasından envantere erişebilir veya Azure Izleyici günlüklerini kullanarak ilgili makineye filtre uygulayabilirsiniz. Aşağıdaki örnek Log Analytics sorgu, **Contosovm** adlı bir makine için yazılım listesini döndürür.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide yazılım envanterini görüntüleme hakkında daha fazla öğrendiniz:

> [!div class="checklist"]
> * Değişiklik İzlemeyi ve Sayımı Etkinleştirme
> * Azure VM 'yi etkinleştirme
> * Azure olmayan bir VM 'yi etkinleştirme
> * Yüklü olan yazılımları görüntüleme
> * Yüklü olan yazılımların stok günlüklerinde arama yapma

Daha fazla bilgi edinmek için Değişiklik İzleme ve envanter özelliğine genel bakış ' a geçin.

> [!div class="nextstepaction"]
> [Değişiklik İzleme ve envantere genel bakış](change-tracking/overview.md)
