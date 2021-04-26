---
title: VM 'Leri Başlat/Durdur v2 (Önizleme)
description: Bu makalede, Azure aboneliğinizdeki Azure sanal makinelerinize yönelik VM 'Leri Başlat/Durdur v2 (Önizleme) özelliğinin nasıl dağıtılacağı açıklanır.
services: azure-functions
ms.subservice: ''
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: 9ca808fffbd26c8837ad9a43447f60e99f89d922
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112209"
---
# <a name="deploy-startstop-vms-v2-preview"></a>VM 'Leri Başlat/Durdur v2 (Önizleme)

VM 'Leri Başlat/Durdur v2 (Önizleme) özelliğini yüklemek için bu konudaki adımları sırayla gerçekleştirin. Kurulum sürecini tamamladıktan sonra, bunları gereksinimlerinize göre özelleştirmek için zamanlamaları yapılandırın.

## <a name="deploy-feature"></a>Özelliği dağıt

Dağıtım, [burada](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md)VM 'leri Başlat/Durdur v2 GitHub kuruluştan başlatılır. Bu özellik, aboneliğinizdeki tüm VM 'lerinizi abonelik içindeki tek bir dağıtımdan tüm kaynak grupları genelinde yönetmek üzere tasarlanırken, kuruluşunuzun işlem modeline veya gereksinimlerine göre başka bir örneğini yükleyebilirsiniz. Ayrıca, birden çok abonelik üzerinde VM 'Leri merkezi olarak yönetmek için de yapılandırılabilir.

Yönetim ve kaldırma işlemlerini basitleştirmek için, adanmış bir kaynak grubuna VM 'Leri Başlat/Durdur v2 (Önizleme) dağıtmanızı öneririz.

> [!NOTE]
> Şu anda bu önizleme mevcut bir depolama hesabı veya Application Insights kaynağı belirtilmesini desteklemez.

1. Tarayıcınızı açın ve Başlat/Durdur VM v2 [GitHub kuruluşuna](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md)gidin.
1. Azure sanal makinelerinizin oluşturulduğu Azure bulut ortamı temelinde dağıtım seçeneğini belirleyin. Bu işlem, Azure portal özel Azure Resource Manager dağıtım sayfasını açar.
1. İstenirse [Azure Portal](https://portal.azure.com)oturum açın.
1. Aşağıdaki değerleri girin:

    |Name |Değer |
    |-----|------|
    |Region |Yeni kaynaklar için size yakın bir bölge seçin.|
    |Kaynak Grubu Adı |VM 'Leri başlatma/durdurma için tek tek kaynakları içerecek kaynak grubu adını belirtin. |
    |Kaynak grubu bölgesi |Kaynak grubunun bölgesini belirtin. Örneğin **Orta ABD**.|
    |Azure İşlev Uygulaması adı |URL yolunda geçerli olan bir ad yazın. Yazdığınız ad, Azure Işlevlerinde benzersiz olduğundan emin olmak için onaylanır. |
    |Application Insights adı |VM 'Leri Başlat/Durdur analizini barındıracak Application Insights örneğinizin adını belirtin. |
    |Application Insights bölgesi |Application Insights örneği için bölgeyi belirtin.|
    |Depolama Hesap Adı |VM yürütme telemetrisini başlatmak/durdurmak için Azure depolama hesabının adını belirtin. |
    |E-posta Adresi |Durum bildirimleri almak için bir veya daha fazla e-posta adresi belirtin (,).|

    :::image type="content" source="media/deploy/deployment-template-details.png" alt-text="VM şablonu dağıtım yapılandırmasını Başlat/Durdur":::

1. Sayfanın alt kısmında **gözden geçir + oluştur** ' u seçin.
1. Dağıtımı başlatmak için **Oluştur** ' u seçin.
1. Dağıtım durumunu görmek için ekranın en üstündeki zil simgesini (bildirimler) seçin. **Dağıtım devam ediyor** görürsünüz. Dağıtım tamamlanana kadar bekleyin.
1. Bildirim bölmesinden **Kaynak grubuna git**'i seçin. Şuna benzer bir ekran görmeniz gerekir:

    :::image type="content" source="media/deploy/deployment-results-resource-list.png" alt-text="VM şablonu dağıtım kaynak listesini Başlat/Durdur":::

## <a name="enable-multiple-subscriptions"></a>Birden çok aboneliği etkinleştir

Dağıtımı Başlat/Durdur işlemi tamamlandıktan sonra, birden fazla aboneliğe işlem yapmak için VM 'Leri Başlat/Durdur v2 'yi (Önizleme) etkinleştirmek üzere aşağıdaki adımları gerçekleştirin.

1. Dağıtım sırasında belirttiğiniz Azure İşlev Uygulaması adı için değeri kopyalayın.

1. Portalda ikincil aboneliğinize gidin. Aboneliği seçin ve ardından **Access Control (IAM)** öğesini seçin.

1. **Ekle** ' yi ve ardından **rol ataması Ekle**' yi seçin.

1. **Rol** açılan listesinden **katkıda bulunan** rolünü seçin.

1. **Seç** alanına Azure Işlev uygulaması adını girin. Sonuçlarda işlev adını seçin.

1. Değişikliklerinizi uygulamak için **Kaydet** ' i seçin.

## <a name="configure-schedules-overview"></a>Zamanlamaları yapılandırma genel bakış

Sanal makinelerinizin başlamasını ve durdurulmasını denetlemek üzere otomasyon metodunu yönetmek için, dahil edilen bir veya daha fazla mantıksal uygulamayı gereksinimlerinize göre yapılandırırsınız.

- Zamanlanan-başlatma ve durdurma eylemleri, Azure Resource Manager ve klasik VM 'Lerde sizin belirttiğiniz bir zamanlamaya göre yapılır.**ststv2_vms_Scheduled_start** ve **ststv2_vms_Scheduled_stop** zamanlanan başlatma ve durdurmayı yapılandırma.

- Sıralı-başlatma ve durdurma eylemleri, önceden tanımlanmış sıralama etiketlerine sahip VM 'Leri hedefleyen bir zamanlamaya göre yapılır. Yalnızca iki adlandırılmış etiket desteklenir- **sequencestart** ve **sequencestop**. sıralı başlatma ve durdurma **ststv2_vms_Sequenced_start** ve **ststv2_vms_Sequenced_stop** yapılandırın.

    > [!NOTE]
    > Bu senaryo yalnızca Azure Resource Manager VM 'Leri destekler.

- Oto durdur-bu işlevsellik yalnızca, CPU kullanımına bağlı olarak hem Azure Resource Manager hem de klasik VM 'Lerde bir durdurma eylemi gerçekleştirmek için kullanılır. Ayrıca *, VM*'lerde uyarı oluşturan ve koşula bağlı olarak, uyarı tetiklenecek şekilde tetiklenir.**ststv2_vms_AutoStop** otomatik durdurma işlevini yapılandırır.

Ek zamanlamalara ihtiyacınız varsa, Azure portal **kopyalama** seçeneği kullanılarak sunulan Logic Apps birini çoğaltabilirsiniz.

:::image type="content" source="media/deploy/logic-apps-clone-option.png" alt-text="Bir mantıksal uygulamayı yinelemek için Kopyala seçeneğini belirleyin":::

## <a name="scheduled-start-and-stop-scenario"></a>Zamanlanan başlatma ve durdurma senaryosu

Azure Resource Manager ve klasik VM 'Ler için zamanlanmış başlatma ve durdurma eylemini yapılandırmak için aşağıdaki adımları gerçekleştirin. Örneğin, **ststv2_vms_Scheduled_start** zamanlamasını ofisiniz sırasında her sabah başlatmak üzere yapılandırabilir ve bir abonelik genelindeki tüm VM 'leri, **ststv2_vms_Scheduled_stop** zamanlamaya bağlı olarak akşam da bırakabilirsiniz.

Mantıksal uygulamayı yalnızca VM 'Leri başlatacak şekilde yapılandırma desteklenir.

Her senaryo için eylemi bir veya daha fazla aboneliğe, tek veya birden çok kaynak grubuna karşı hedefleyebilir ve bir içerme veya dışlama listesinde bir veya daha fazla VM belirtebilirsiniz. Bunları aynı mantıksal uygulamada birlikte belirtemezsiniz.

1. [Azure Portal](https://portal.azure.com) oturum açın ve **Logic Apps**' e gidin.

1. Mantıksal uygulamalar listesinden, zamanlanmış başlangıç ' ı yapılandırmak için **ststv2_vms_Scheduled_start**' yi seçin. Zamanlanan durdurmayı yapılandırmak için **ststv2_vms_Scheduled_stop**' yi seçin.

1. Sol bölmeden **Logic App Designer** ' ı seçin.

1. Logic App Designer görüntülendikten sonra, tasarımcı bölmesinde, mantıksal uygulama zamanlamasını yapılandırmak için **yinelenme** ' yi seçin. Belirli yinelenme seçenekleri hakkında bilgi edinmek için bkz. [Yinelenen görevi zamanlama](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Mantıksal uygulama için yinelenme sıklığını yapılandırma":::

1. Tasarımcı bölmesinde, **işlev-** hedef ayarlarını yapılandırmak için dene ' yi seçin. İstek gövdesinde, sanal makineleri abonelikteki tüm kaynak grupları genelinde yönetmek istiyorsanız, aşağıdaki örnekte gösterildiği gibi istek gövdesini değiştirin.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     }
    }
    ```

    `subscriptions`Aşağıdaki örnekte olduğu gibi her bir değeri virgülle ayırarak dizide birden çok abonelik belirtin.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    İstek gövdesinde, belirli kaynak grupları için VM 'Leri yönetmek istiyorsanız, aşağıdaki örnekte gösterildiği gibi istek gövdesini değiştirin. Belirtilen her kaynak yolunun virgülle ayrılması gerekir. Gerekirse bir kaynak grubu veya daha fazlasını belirtebilirsiniz.

    Bu örnek, bir sanal makinenin dışlanmasını da gösterir. VM 'Leri kaynak yolunu veya joker karakterle belirterek VM 'yi dışlayabilirsiniz.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

    Burada eylem, VM adı hariç tüm VM 'lerde gerçekleştirilecek ve her iki abonelik için az ve BZ ile başlar.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [“Az*”,“Bz*”],
       "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
    
        ]
      }
    }
    ```

    İstek gövdesinde, abonelik içindeki belirli bir VM kümesini yönetmek istiyorsanız, aşağıdaki örnekte gösterildiği gibi istek gövdesini değiştirin. Belirtilen her kaynak yolunun virgülle ayrılması gerekir. Gerekirse bir VM belirtebilirsiniz.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg3/providers/Microsoft.Compute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
          
        ]
    }
    ```

## <a name="sequenced-start-and-stop-scenario"></a>Sıralı başlatma ve durdurma senaryosu

Dağıtılmış bir uygulama mimarisinde birden çok Azure Resource Manager VM üzerinde iki veya daha fazla bileşen içeren bir ortamda, bileşenlerin başlatıldığı ve sırasıyla durdurulduğu sırayı destekleme.

1. Mantıksal uygulamalar listesinden sıralı başlangıcı yapılandırmak için **ststv2_vms_Sequenced_start**' yi seçin. Sıralı durdurmayı yapılandırmak için **ststv2_vms_Sequenced_stop**' yi seçin.

1. Sol bölmeden **Logic App Designer** ' ı seçin.

1. Logic App Designer görüntülendikten sonra, tasarımcı bölmesinde, mantıksal uygulama zamanlamasını yapılandırmak için **yinelenme** ' yi seçin. Belirli yinelenme seçenekleri hakkında bilgi edinmek için bkz. [Yinelenen görevi zamanlama](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Mantıksal uygulama için yinelenme sıklığını yapılandırma":::

1. Tasarımcı bölmesinde, **işlev-** hedef ayarlarını yapılandırmak için dene ' yi seçin. İstek gövdesinde, sanal makineleri abonelikteki tüm kaynak grupları genelinde yönetmek istiyorsanız, aşağıdaki örnekte gösterildiği gibi istek gövdesini değiştirin.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     },
       "Sequenced": true
    }
    ```

    `subscriptions`Aşağıdaki örnekte olduğu gibi her bir değeri virgülle ayırarak dizide birden çok abonelik belirtin.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    İstek gövdesinde, belirli kaynak grupları için VM 'Leri yönetmek istiyorsanız, aşağıdaki örnekte gösterildiği gibi istek gövdesini değiştirin. Belirtilen her kaynak yolunun virgülle ayrılması gerekir. Gerekirse bir kaynak grubu belirtebilirsiniz.

    Bu örnek ayrıca, bir sanal makinenin kaynak yoluna göre dışlanmasını, bu joker karakter kullanan zamanlanmış başlatma/durdurma örneğine kıyasla gösterir.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      },
       "Sequenced": true
    }
    ```

    İstek gövdesinde, bir abonelik içindeki belirli bir VM kümesini yönetmek istiyorsanız, aşağıdaki örnekte gösterildiği gibi istek gövdesini değiştirin. Belirtilen her kaynak yolunun virgülle ayrılması gerekir. Gerekirse bir VM belirtebilirsiniz.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
        ]
      },
       "Sequenced": true
    }
    ```

## <a name="auto-stop-scenario"></a>Otomatik durdurma senaryosu

VM 'Leri Başlat/Durdur v2 (Önizleme), süre sonu gibi yoğun olmayan dönemlerde kullanılmayan makineleri değerlendirerek ve işlemci kullanımı belirtilen yüzdeden küçük olduğunda otomatik olarak kapatmak için aboneliğinizdeki Azure Resource Manager ve klasik VM 'Lerin çalışma maliyetini yönetmenize yardımcı olabilir.

İstek gövdesi destek özelleştirmesindeki aşağıdaki ölçüm uyarısı özellikleri:

- AutoStop_MetricName
- AutoStop_Condition
- AutoStop_Threshold
- AutoStop_Description
- AutoStop_Frequency
- AutoStop_Severity
- AutoStop_Threshold
- AutoStop_TimeAggregationOperator
- AutoStop_TimeWindow

Azure Izleyici ölçümünün nasıl çalıştığı ve bunların nasıl yapılandırılacağı hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici 'de ölçüm uyarıları](../../azure-monitor/alerts/alerts-metric-overview.md).

1. Mantıksal uygulamalar listesinden otomatik durdurmayı yapılandırmak için **ststv2_vms_AutoStop**' yi seçin.

1. Sol bölmeden **Logic App Designer** ' ı seçin.

1. Logic App Designer görüntülendikten sonra, tasarımcı bölmesinde, mantıksal uygulama zamanlamasını yapılandırmak için **yinelenme** ' yi seçin. Belirli yinelenme seçenekleri hakkında bilgi edinmek için bkz. [Yinelenen görevi zamanlama](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Mantıksal uygulama için yinelenme sıklığını yapılandırma":::

1. Tasarımcı bölmesinde, **işlev-** hedef ayarlarını yapılandırmak için dene ' yi seçin. İstek gövdesinde, sanal makineleri abonelikteki tüm kaynak grupları genelinde yönetmek istiyorsanız, aşağıdaki örnekte gösterildiği gibi istek gövdesini değiştirin.

    ```json
    {
      "Action": "stop",
      "EnableClassic": false,    
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "RequestScopes":{        
        "Subscriptions":[
            "/subscriptions/12345678-1111-2222-3333-1234567891234/",
            "/subscriptions/12345678-2222-4444-5555-1234567891234/"
        ],
        "ExcludedVMLists":[]
      }        
    }
    ```

    İstek gövdesinde, belirli kaynak grupları için VM 'Leri yönetmek istiyorsanız, aşağıdaki örnekte gösterildiği gibi istek gövdesini değiştirin. Belirtilen her kaynak yolunun virgülle ayrılması gerekir. Gerekirse bir kaynak grubu belirtebilirsiniz.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "ResourceGroups": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroupsvmrg2/",
          "/subscriptions/12345678-2222-4444-5555-1234567891234/resourceGroups/VMHostingRG/"
          ]
      }
    }
    ```

    İstek gövdesinde, abonelik içindeki belirli bir VM kümesini yönetmek istiyorsanız, aşağıdaki örnekte gösterildiği gibi istek gövdesini değiştirin. Belirtilen her kaynak yolunun virgülle ayrılması gerekir. Gerekirse bir VM belirtebilirsiniz.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/rg3/providers/Microsoft.ClassicCompute/virtualMachines/Clasyvm11",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

## <a name="next-steps"></a>Sonraki adımlar

VM 'Leri Başlat/Durdur v2 (Önizleme) özelliği tarafından yönetilen Azure sanal makinelerinizin durumunu izlemeyi öğrenmek ve diğer yönetim görevlerini gerçekleştirmek için bkz. [VM 'Leri Başlat/Durdur](manage.md) makalesine bakın.