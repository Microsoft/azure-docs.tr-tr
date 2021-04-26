---
title: Azure ölçüm uyarıları sorunlarını giderme
description: Azure Izleyici ölçüm uyarıları ve olası çözümlerle ilgili yaygın sorunlar.
author: harelbr
ms.author: harelbr
ms.topic: troubleshooting
ms.date: 04/12/2021
ms.openlocfilehash: fc9af94b07add5728201baaa8fa6992728a60a8c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786018"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Azure İzleyici ölçüm uyarılarındaki sorunları giderme 

Bu makalede, Azure Izleyici [ölçüm uyarılarında](alerts-metric-overview.md) sık karşılaşılan sorunlar ve bunların nasıl giderileceği anlatılmaktadır.

İzleme verilerinizde önemli koşullar bulunduğunda Azure Izleyici uyarıları size önceden bildirimde bulunur. Bunlar, sisteminizin kullanıcıları tarafından bildirilmeksizin sorunları tanımlamanızı ve adreslerinizi belirlemenizi sağlar. Uyarı hakkında daha fazla bilgi için bkz. [Microsoft Azure uyarılara genel bakış](./alerts-overview.md).

## <a name="metric-alert-should-have-fired-but-didnt"></a>Ölçüm uyarısı tetiklendi ancak yoktu 

Bir ölçüm uyarısının tetiklenmesi gerektiğini ancak Azure portal başlatmadıysanız ve bulunamadığını düşünüyorsanız, aşağıdaki adımları deneyin:

1. **Yapılandırma** -uygun şekilde yapılandırıldığından emin olmak için ölçüm uyarı kuralı yapılandırmasını gözden geçirin:
    - **Toplama türü** ve **toplama ayrıntı düzeyi (Period)** beklenen şekilde yapılandırılıp yapılandırılmadığını denetleyin. **Toplama türü** , ölçüm değerlerinin nasıl toplandığını belirler [(daha fazla](../essentials/metrics-aggregation-explained.md#aggregation-types)bilgi edinin) ve **toplama ayrıntı düzeyi (süre)** , uyarı kuralının her çalıştırılışında değerlendirmenin ölçüm değerlerini toplamasının ne kadar geri alınacağını denetler.
    -  **Eşik değerinin** veya **Duyarlığın** beklenen şekilde yapılandırılıp yapılandırılmadığını denetleyin.
    - Dinamik eşikleri kullanan bir uyarı kuralı için, gelişmiş ayarların yapılandırılıp yapılandırılmadığını denetleyin, **Ihlallerin sayısı** uyarıları filtreleyip daha sonra **verileri yok sayarak** eşiklerin nasıl hesaplanacağını etkilemez.

       > [!NOTE] 
       > Dinamik eşikler, etkin hale gelmeden önce en az 3 gün ve 30 ölçüm örneği gerektirir.

2. **Başlatıldı ancak bildirim yok** -tetiklenen uyarıyı bulup [bulacağınızı görmek için tetiklenen uyarılar listesini](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) gözden geçirin. Uyarıyı listede görebilir, ancak bazı eylemleri veya bildirimleriyle ilgili bir sorun varsa daha fazla bilgi için [buraya](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)bakın.

3. **Zaten etkin** -bir uyarı almayı beklediğiniz ölçüm zaman serisinde zaten başlatılmış bir uyarı olup olmadığını kontrol edin. Ölçüm uyarıları durum bilgisine sahiptir, uyarı belirli bir ölçüm zaman serisine tetiklendiğinde o zaman serisindeki ek uyarılar, sorun gözlemlenmeye devam ettiği sürece tetiklenmeyecektir. Bu tasarım seçeneği paraziti azaltır. Uyarı koşulu üç ardışık değerlendirme için karşılanmazsa uyarı otomatik olarak çözümlenir.

4. **Kullanılan boyutlar** - [bir ölçüm için boyut değerlerini](./alerts-metric-overview.md#using-dimensions)seçtiyseniz, uyarı kuralı her bir ölçüm zaman serisini (boyut değerlerinin birleşimiyle tanımlanan şekilde) bir eşik ihlali için izler. Ayrıca, toplam ölçüm zaman serisini (Seçili boyutlar olmadan) izlemek için, boyutları seçmeden ölçüm üzerinde ek bir uyarı kuralı yapılandırın.

5. **Toplama ve zaman ayrıntı düzeyi** -ölçümü, ölçüm [grafiklerini](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)kullanarak görselleştirmeniz halinde şunları doğrulayın:
    * Ölçüm grafiğindeki seçili **toplama** , uyarı kuralınızın **toplama türüyle** aynı
    * Seçilen **zaman ayrıntı düzeyi** , uyarı kuralınızın (ve ' otomatik ' olarak ayarlanmamış) **toplama ayrıntı düzeyi (dönemi)** ile aynıdır.

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>Ölçüm uyarısı, sahip olmaması durumunda tetiklendi

Ölçüm uyarılarınızın tetiklenmemesi, ancak bunu yaptığı düşünüyorsanız, aşağıdaki adımlar sorunu çözmeye yardımcı olabilir.

1. Tetiklenen uyarıyı bulmak için [tetiklenen uyarılar listesini](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) gözden geçirin ve ayrıntılarını görüntülemek için tıklayın. Uyarının tetiklendiği sırada ölçüm grafiğini, **ölçüm değerini** ve **eşik değerini** görmek için **Bu uyarının neden başlatıldığı konusunda** belirtilen bilgileri gözden geçirin.

    > [!NOTE] 
    > Dinamik eşikler koşul türü kullanıyorsanız ve kullanılan eşiklerin doğru olmadığını düşünüyorsanız, lütfen kaş n simgesini kullanarak geri bildirim sağlayın. Bu geri bildirim Machine Learning algoritmik araştırmasını etkiler ve gelecekteki algılamaları artırmaya yardımcı olur.

2. Bir ölçüm için birden çok boyut değeri seçtiyseniz **, bir ölçüm** zaman serisinden (boyut değerlerinin birleşimi tarafından tanımlanan) buna ulaştığında uyarı tetiklenir. Ölçüm uyarılarındaki boyutları kullanma hakkında daha fazla bilgi için [buraya](./alerts-metric-overview.md#using-dimensions) bakın.

3. Düzgün yapılandırıldığından emin olmak için uyarı kuralı yapılandırmasını gözden geçirin:
    - **Toplama türü**, **toplama ayrıntı düzeyi (süre)** ve **eşik değerinin** veya **Duyarlığın** beklenen şekilde yapılandırılıp yapılandırılmadığını denetleyin
    - Dinamik eşikleri kullanan bir uyarı kuralı için, gelişmiş ayarların yapılandırılıp yapılandırılmadığını denetleyin. bu durum, **eşiklerin** nasıl hesaplanacağını etkilemeden önce uyarıları filtreleyebilir ve **verileri yoksayabilir** .

   > [!NOTE]
   > Dinamik eşikler, etkin hale gelmeden önce en az 3 gün ve 30 ölçüm örneği gerektirir.

4. Ölçüm [grafiğini](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)kullanarak ölçümü görselleştirmeniz halinde şunları doğrulayın:
    - Ölçüm grafiğindeki seçili **toplama** , uyarı kuralınızın **toplama türüyle** aynı
    - Seçilen **zaman ayrıntı düzeyi** , uyarı kuralınızın (ve ' otomatik ' olarak ayarlanmamış) **toplama ayrıntı düzeyi (dönemi)** ile aynıdır.

5. Uyarı, aynı ölçütleri izleyen (çözümlenmeyen) uyarılar varken harekete geçirildiğinde, uyarı kuralının *otomatik azaltma* özelliği **false** olarak ayarlanmış şekilde yapılandırılıp yapılandırılmadığını denetleyin (Bu özellik yalnızca Rest/PowerShell/CLI aracılığıyla yapılandırılabilir, bu nedenle uyarı kuralını dağıtmak için kullanılan betiği denetleyin). Böyle bir durumda, uyarı kuralı tetiklenen uyarıları oto çözmez ve yeniden başlatılmadan önce tetiklenen bir uyarının çözülmesini gerektirmez.


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>Sanal makinelerin Konuk ölçümlerini uyarmak için ölçüm bulunamıyor

Sanal makinelerin Konuk işletim sistemi ölçümleri hakkında uyarı almak için (örneğin, bellek, disk alanı), bu verileri Azure Izleyici ölçümlerine toplamak için gerekli aracıyı yüklediğinizden emin olun:
- [Windows VM'leri için](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
- [Linux VM'leri için](../essentials/collect-custom-metrics-linux-telegraf.md)

Bir sanal makinenin Konuk işletim sisteminden veri toplama hakkında daha fazla bilgi için [buraya](../vm/monitor-vm-azure.md#guest-operating-system)bakın.

> [!NOTE] 
> Konuk ölçümlerini bir Log Analytics çalışma alanına gönderilmek üzere yapılandırdıysanız, ölçümler Log Analytics çalışma alanı kaynağı altında görünür ve **yalnızca** bunları izleyen bir uyarı kuralı oluşturduktan sonra verileri göstermeye başlayacaktır. Bunu yapmak için, [günlükler için ölçüm uyarısı yapılandırma](./alerts-metric-logs.md#configuring-metric-alert-for-logs) adımlarını izleyin.

> [!NOTE] 
> Tek bir uyarı kuralına sahip birden çok sanal makine için konuk ölçüsünü izlemek şu anda ölçüm uyarıları tarafından desteklenmez. Bunu bir [günlük uyarısı kuralıyla](./alerts-unified-log.md)elde edebilirsiniz. Bunu yapmak için, Konuk ölçümlerinin bir Log Analytics çalışma alanına toplanmış olduğundan emin olun ve çalışma alanında bir günlük uyarısı kuralı oluşturun.

## <a name="cant-find-the-metric-to-alert-on"></a>Uyarı almak için ölçüm bulunamıyor

Uyarı kuralı oluştururken belirli bir ölçümle ilgili uyarıyı arıyorsanız ama göremiyorsanız, aşağıdakileri denetleyin:
- Kaynağın ölçümlerini göremiyorsanız [kaynak türünün ölçüm uyarıları için desteklenip desteklenmediğini denetleyin](./alerts-metric-near-real-time.md).
- Kaynağın bazı ölçümlerini görebiliyorsanız ama belirli bir ölçümü bulamıyorsanız [ölçümün kullanılabilir olup olmadığını denetleyin](../essentials/metrics-supported.md) ve kullanılabiliyorsa, ölçüm açıklamasına bakarak yalnızca kaynağın belirli sürüm veya yayınlarda kullanılabilir olup olmadığını gözden geçirin.
- Kaynak için ölçüm kullanılabilir değilse, kaynak günlüklerinde bulunabilir ve günlük uyarıları kullanılarak izlenebilir. [Azure kaynağından kaynak günlüklerini toplama ve analiz etme](../essentials/tutorial-resource-logs.md) hakkında daha fazla bilgi için buraya bakın.

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Uyarı almak için ölçüm boyutu bulunamıyor

[Bir ölçümün belirli boyut değerlerini](./alerts-metric-overview.md#using-dimensions)uyarmak istiyorsanız ancak bu değerleri bulamazsanız, aşağıdakilere göz önünde bulunur:

1. Boyut değerlerinin **Boyut değerleri** listesinde görünmesi birkaç dakika sürebilir
2. Görüntülenen boyut değerleri son gün toplanan ölçüm verilerini temel alır
3. Boyut değeri henüz yayınlanmadıysa veya gösterilmiyorsa özel bir boyut değeri eklemek için ‘Özel değer ekle’ seçeneğini kullanabilirsiniz
4. Bir boyutun tüm olası değerlerini (gelecekteki değerler dahil) uyarmak isterseniz, ' tüm geçerli ve gelecekteki değerleri Seç ' seçeneğini belirleyin.
5. Application Insights kaynakların özel ölçüm boyutları varsayılan olarak kapalıdır. Bu özel ölçümler için boyut koleksiyonunu açmak üzere [buraya](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)bakın.

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>Ölçüm uyarı kuralları silinen bir kaynakta hala tanımlı 

Bir Azure kaynağını sildiğinizde o kaynakla ilişkilendirilmiş olan ölçüm uyarı kuralları otomatik olarak silinmez. Silinmiş bir kaynakla ilişkili uyarı kurallarını silmek için:

1. Silinen kaynağın tanımlandığı kaynak grubunu açın
1. Kaynakların görüntülendiği listede **Gizli türleri göster** onay kutusunu işaretleyin
1. Listeye şu filtreyi uygulayın: Tür == **microsoft.insights/metricalerts**
1. İlgili uyarı kurallarını seçin ve **Sil** ' i seçin.

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>Koşulumun her karşılanışında ölçüm uyarılarını meydana getirme

Ölçüm uyarıları varsayılan olarak durum bilgisine sahiptir ve bu nedenle, belirli bir zaman serisinde zaten tetiklenen bir uyarı varsa ek uyarılar tetiklenmez. Belirli bir ölçüm uyarısı kuralını durum bilgisiz yapmak ve uyarı koşulunun karşılandığı her değerlendirmede uyarı almak istiyorsanız, uyarı kuralını programlı bir şekilde oluşturun (örneğin, [Kaynak Yöneticisi](./alerts-metric-create-templates.md), [PowerShell](/powershell/module/az.monitor/), [rest](/rest/api/monitor/metricalerts/createorupdate), [CLI](/cli/azure/monitor/metrics/alert)) ve *oto hafifletmek* özelliğini ' false ' olarak ayarlayın.

> [!NOTE] 
> Ölçüm uyarısı kuralının durum bilgisiz olması, tetiklenen uyarıların çözümlenmesini önler, bu nedenle, koşulun artık karşılanmaması durumunda, başlatılan uyarılar 30 gün Bekletme süresine kadar tetiklenen bir durumda kalır.

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>Henüz yayınlanmayan özel bir ölçüm üzerinde bir uyarı kuralı tanımlayın

Ölçüm uyarısı kuralı oluştururken, ölçüm adı [Ölçüm tanımları API 'sine](/rest/api/monitor/metricdefinitions/list) göre doğrulandıktan ve var olduğundan emin olur. Bazı durumlarda, özel bir ölçümde, yayılmadan önce bile bir uyarı kuralı oluşturmak istersiniz. Örneğin, (bir Kaynak Yöneticisi şablonu kullanarak), özel bir ölçümü yayan bir Application Insights kaynağı ve bu ölçüyü izleyen bir uyarı kuralı oluşturur.

Özel Ölçüm tanımlarını doğrulamaya çalışırken dağıtımın başarısız olmasına engel olmak için, uyarı kuralının ölçüt bölümünde, ölçüm doğrulamasının atlanmasına neden olacak *Skipmetricvalidation* parametresini kullanabilirsiniz. Kaynak Yöneticisi şablonunda bu parametrenin nasıl kullanılacağı için aşağıdaki örneğe bakın. Daha fazla bilgi için, [ölçüm uyarı kuralları oluşturmak için Kaynak Yöneticisi tüm şablon örneklerine](./alerts-metric-create-templates.md)bakın.

```json
"criteria": {
    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
        "allOf": [
            {
                "name" : "condition1",
                "metricName": "myCustomMetric",
                "metricNamespace": "myCustomMetricNamespace",
                "dimensions":[],
                "operator": "GreaterThan",
                "threshold" : 10,
                "timeAggregation": "Average",
                "skipMetricValidation": true
            }
        ]
    }
```

## <a name="export-the-azure-resource-manager-template-of-a-metric-alert-rule-via-the-azure-portal"></a>Ölçüm uyarısı kuralının Azure Resource Manager şablonunu Azure portal aracılığıyla dışarı aktarın

Ölçüm uyarısı kuralının Kaynak Yöneticisi şablonunu dışarı aktarmak, JSON sözdizimi ve özelliklerini anlamanıza yardımcı olur ve gelecekteki dağıtımları otomatikleştirmek için kullanılabilir.
1. Azure portal, ayrıntılarını görüntülemek için uyarı kuralını açın.
2. **Özellikler**'e tıklayın.
3. **Otomasyon** altında **şablonu dışarı aktar**' ı seçin.

## <a name="metric-alert-rules-quota-too-small"></a>Ölçüm uyarı kuralları kotası çok küçük

Abonelik başına izin verilen ölçüm uyarısı kuralı sayısı, [kota sınırlarına](../service-limits.md)tabidir.

Kota sınırına ulaştıysanız, aşağıdaki adımlar sorunu çözmeye yardımcı olabilir:
1. Artık kullanılmayan ölçüm uyarısı kurallarını silmeyi veya devre dışı bırakmayı deneyin.

2. Birden fazla kaynağı izleyen ölçüm uyarı kurallarına geçiş yapın. Bu özellik sayesinde, tek bir uyarı kuralı, kotayla sayılan tek bir uyarı kuralını kullanarak birden çok kaynağı izleyebilir. Bu özellik ve desteklenen kaynak türleri hakkında daha fazla bilgi için [buraya](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) bakın.

3. Kota sınırının artırılması gerekiyorsa, bir destek isteği açın ve aşağıdaki bilgileri sağlayın:

    - Kota sınırının artırılması gereken abonelik kimlikleri
    - Kota artışı için kaynak türü: **ölçüm uyarıları** veya **ölçüm uyarıları (klasik)**
    - İstenen kota sınırı

## <a name="check-total-number-of-metric-alert-rules"></a>Toplam ölçüm uyarısı kuralı sayısını denetle

Ölçüm uyarı kurallarının geçerli kullanımını denetlemek için aşağıdaki adımları izleyin.

### <a name="from-the-azure-portal"></a>Azure portalından

1. **Uyarılar** ekranını açıp **Uyarı kurallarını yönetin**'e tıklayın
2. **Abonelik** açılan denetimini kullanarak ilgili aboneliğe filtre uygulayın
3. Belirli bir kaynak grubuna, kaynak türüne veya kaynağa filtre uyguladığınızdan emin olun
4. **Sinyal türü** açılan kutusunda **ölçümler** ' i seçin.
5. **Durum** açılan denetiminin **etkin** olarak ayarlandığını doğrulama
6. Ölçüm uyarı kurallarının toplam sayısı, uyarı kuralları listesinin üzerinde görüntülenir

### <a name="from-api"></a>API'den

- PowerShell - [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2)
- REST API - [Aboneliğe göre listeleme](/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI - [az monitor metrics alert list](/cli/azure/monitor/metrics/alert#az_monitor_metrics_alert_list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Kaynak Yöneticisi şablonları, REST API, PowerShell veya Azure CLı kullanarak uyarı kurallarını yönetme

Kaynak Yöneticisi şablonları, REST API, PowerShell veya Azure komut satırı arabirimi (CLı) kullanarak ölçüm uyarıları oluşturma, güncelleştirme, alma veya silme sorunları yaşıyorsanız, aşağıdaki adımlar sorunu çözmeye yardımcı olabilir.

### <a name="resource-manager-templates"></a>Resource Manager şablonları

- [Yaygın Azure dağıtım hataları](../../azure-resource-manager/templates/common-deployment-errors.md) listesini gözden geçirin ve ilgili sorun giderme adımlarını izleyin
- Tüm parametreleri doğru şekilde geçirdiğinizden emin olmak için [ölçüm uyarıları Azure Resource Manager şablon örneklerine](./alerts-metric-create-templates.md) bakın

### <a name="rest-api"></a>REST API

Tüm parametreleri doğru şekilde geçirdiğinizi doğrulamak için [REST API kılavuzunu](/rest/api/monitor/metricalerts/) gözden geçirin

### <a name="powershell"></a>PowerShell

Ölçüm uyarıları için doğru PowerShell cmdlet 'lerini kullandığınızdan emin olun:

- Ölçüm uyarılarına yönelik PowerShell cmdlet’leri [Az.Monitor modülünde](/powershell/module/az.monitor/) de sunulur
- Yeni (klasik olmayan) ölçüm uyarıları için ' v2 ' ile biten cmdlet 'leri kullandığınızdan emin olun (örneğin, [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2))

### <a name="azure-cli"></a>Azure CLI

Ölçüm uyarıları için doğru CLı komutlarını kullandığınızdan emin olun:

- Ölçümlere yönelik CLI komutları `az monitor metrics alert` ile başlar. Söz dizimi hakkında bilgi edinmek için [Azure CLI başvurusunu](/cli/azure/monitor/metrics/alert) gözden geçirin.
- [Ölçüm uyarısı CLI komutlarını kullanmayı gösteren örneğe](./alerts-metric.md#with-azure-cli) bakabilirsiniz
- Özel bir ölçümde uyarı tetiklemek için ölçüm adını ilgili ölçümün alan adına ön ek olarak getirdiğinizden emin olun: NAMESPACE.METRIC

### <a name="general"></a>Genel

- Bir `Metric not found` hata alıyorsanız:

   - Platform ölçümü için: ölçüm **görünen adı** değil, [Azure izleyici tarafından desteklenen ölçümler sayfasından](../essentials/metrics-supported.md) **ölçüm** adını kullandığınızdan emin olun

   - Özel bir ölçüm için: ölçümün zaten yayıldığından (henüz mevcut olmayan bir özel ölçüm üzerinde bir uyarı kuralı oluşturamazsınız) ve özel ölçümün ad alanını sağladığınızdan emin olun ( [burada](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric)bir kaynak yöneticisi şablon örneğine bakın)

- [Günlüklerde ölçüm uyarıları](./alerts-metric-logs.md)oluşturuyorsanız, uygun bağımlılıkların eklendiğinden emin olun. Bkz. [örnek şablon](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs).

- Birden çok ölçüt içeren bir uyarı kuralı oluşturuyorsanız, aşağıdaki kısıtlamalara göz önünde bulabilirsiniz:

   - Her bir ölçüt içinde boyut başına yalnızca bir değer seçebilirsiniz
   - Boyut değeri olarak "\*" kullanamazsınız
   - Farklı criterions ' de yapılandırılan ölçümler aynı boyutu destekledikleri zaman, yapılandırılan bir boyut değeri bu ölçümler için açıkça aynı şekilde ayarlanmalıdır ( [burada](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria)bir kaynak yöneticisi şablon örneğine bakın)


## <a name="no-permissions-to-create-metric-alert-rules"></a>Ölçüm uyarı kuralları oluşturmak için izin yok

Ölçüm uyarısı kuralı oluşturmak için aşağıdaki izinlere sahip olmanız gerekir:

- Uyarı kuralının hedef kaynağında Oku izni
- Uyarı kuralının oluşturulduğu kaynak grubu üzerinde yazma izni (Azure portal uyarı kuralı oluşturuyorsanız, uyarı kuralı hedef kaynağın bulunduğu aynı kaynak grubunda varsayılan olarak oluşturulur)
- Uyarı kuralıyla ilişkili tüm eylem grupları üzerinde okuma izni (varsa)


## <a name="naming-restrictions-for-metric-alert-rules"></a>Ölçüm uyarı kuralları için adlandırma kısıtlamaları

Ölçüm uyarı kuralı adları için aşağıdaki kısıtlamaları göz önünde bulundurun:

- Ölçüm uyarısı kural adları, oluşturulduktan sonra değiştirilemez (yeniden adlandırılamaz)
- Ölçüm uyarısı kural adları, bir kaynak grubu içinde benzersiz olmalıdır
- Ölçüm uyarısı kural adları şu karakterleri içeremez: * # & +:  < > ? @ % { } \ / 
- Ölçüm uyarısı kural adları boşluk veya nokta ile bitemez

> [!NOTE] 
> Uyarı kuralı adı alfabetik veya sayısal olmayan karakterler içeriyorsa (örneğin: boşluklar, noktalama işaretleri veya semboller), bu karakterler belirli istemciler tarafından alındığında URL kodlamalı olabilir.

## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>Birden çok koşula sahip bir ölçüm uyarı kuralında boyutlar kullanırken kısıtlamalar

Ölçüm uyarıları çok boyutlu ölçümler üzerinde uyarı vermeyi ve birden çok koşul tanımlamayı destekler (uyarı kuralı başına 5 koşula kadar).

Birden çok koşul içeren bir uyarı kuralında boyutları kullanırken aşağıdaki kısıtlamaları göz önünde bulundurun:
- Her bir koşul içinde yalnızca boyut başına bir değer seçebilirsiniz.
- "Tüm geçerli ve gelecekteki değerleri Seç" seçeneğini (Select \* ) kullanamazsınız.
- Farklı koşullarda yapılandırılan ölçümler aynı boyutu destekledikleri zaman, yapılandırılmış bir boyut değerinin tüm bu ölçümler için (ilgili koşullarda) aynı şekilde ayarlanması gerekir.
Örnek:
    - Bir depolama hesabında tanımlanan ölçüm uyarısı kuralını düşünün ve iki koşulu izler:
        * Toplam **işlem** sayısı > 5
        * Ortalama **SuccessE2ELatency** > 250 MS
    - İlk koşulu güncelleştirmek istiyorum ve yalnızca **Apiname** boyutunun *"GetBlob"* değerine eşit olduğu işlemleri izle
    - Hem **işlemler** hem de **SuccessE2ELatency** ölçümleri bir **apiname** boyutunu destekledikleri için her iki koşulu da güncelleştirmem gerekir ve her ikisine de bir *"GetBlob"* değeri ile **apiname** boyutunu belirtmektir.

## <a name="setting-the-alert-rules-period-and-frequency"></a>Uyarı kuralının dönemini ve sıklığını ayarlama

Aşağıdaki durumlarda, eklenen zaman serisinin ilk değerlendirmesinin oluşma olasılığını azaltmak için *değerlendirme sıklığından* daha büyük bir *toplama ayrıntı düzeyi (süre)* seçmeyi öneririz:
-   Birden çok boyutu izleyen ölçüm uyarısı kuralı – yeni bir boyut değer birleşimi eklendiğinde
-   Birden çok kaynağı izleyen ölçüm uyarısı kuralı: kapsama yeni bir kaynak eklendiğinde
-   Sürekli olarak (seyrek ölçüm) yayınlanmayan bir ölçüyü izleyen ölçüm uyarısı kuralı: ölçüm, kendisine yayılmadığı 24 saatten daha uzun bir süre sonra yayınlandığında

## <a name="the-dynamic-thresholds-borders-dont-seem-to-fit-the-data"></a>Dinamik eşiklerin kenarlıkları verilere uygun görünmüyor

Bir ölçümün davranışı yakın zamanda değiştirilmişse, değişiklikler, son 10 günden alınan ölçüm verilerine göre hesaplanarak dinamik eşik kenarlıklarında (büyük ve küçük sınırlara) yansıtılmalıdır. Belirli bir ölçüm için dinamik eşik kenarlıklarını görüntülerken, son hafta içinde ölçüm eğilimi ' ne baktığınızdan ve yalnızca son saatler veya günler için değil.

## <a name="why-is-weekly-seasonality-not-detected-by-dynamic-thresholds"></a>Dinamik eşiklere neden haftalık mevsimsellik algılanmadı?

Haftalık mevsimsellik belirlemek için dinamik eşik modeli en az üç haftalık geçmiş veri gerektirir. Yeterli geçmiş veri kullanılabilir olduğunda, ölçüm verilerinde bulunan haftalık mevsimsellik tanımlanacaktır ve model uygun şekilde ayarlanır. 

## <a name="dynamic-thresholds-shows-a-negative-lower-bound-for-a-metric-even-though-the-metric-always-has-positive-values"></a>Ölçüm her zaman pozitif değerlere sahip olsa da dinamik eşikler bir ölçüm için negatif bir alt sınır gösterir

Bir metrik büyük bir dalgalanma yol gösteriyorsa dinamik eşikler ölçüm değerleri etrafında daha geniş bir model oluşturur ve bu da alt kenarlığın sıfırdan daha düşük olmasını sağlayabilir. Özellikle, bu durum aşağıdaki durumlarda oluşabilir:
1. Duyarlılık düşük olarak ayarlanmıştır 
2. Ortanca değerleri sıfıra yakın
3. Ölçüm yüksek varyans ile düzensiz bir davranış sergiler (veride ani artışlar veya DIB 'ler vardır)

Alt sınır negatif bir değere sahip olduğunda, bu, ölçümün ölçüsünün düzensiz davranış için sıfır değere ulaşması anlamına gelir. Modeli daha az hassas hale getirmek için daha yüksek bir duyarlılık veya daha büyük bir *toplama ayrıntı düzeyi (nokta)* veya modeli oluşturmak için kullanılan geçmiş verilerden en son bir ırreği hariç tutmak Için *verileri yoksay* seçeneğini kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Uyarılar ve bildirimler hakkında genel sorun giderme bilgileri için bkz. [Azure izleyici uyarılarında sorun giderme sorunları](alerts-troubleshoot.md).
