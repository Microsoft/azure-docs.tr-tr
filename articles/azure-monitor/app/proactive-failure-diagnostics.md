---
title: Akıllı algılama-hata bozuklukları, Application Insights içinde | Microsoft Docs
description: Web uygulamanıza yönelik başarısız isteklerin hızında olağandışı değişiklikler konusunda sizi uyarır ve tanılama analizi sağlar. Yapılandırma gerekmiyor.
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yalavi
ms.openlocfilehash: 0f4de3aaba4acf86df37048134089326196e87ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100587540"
---
# <a name="smart-detection---failure-anomalies"></a>Akıllı algılama-hata bozuklukları
[Application Insights](./app-insights-overview.md) , Web uygulamanız başarısız isteklerin hızında anormal bir artış yaşıyorsa sizi neredeyse gerçek zamanlı olarak uyarır. HTTP isteklerinin veya başarısız olarak bildirilen bağımlılık çağrılarının hızında olağan dışı bir artış algılar. İstekler için, başarısız olan isteklerin genellikle 400 veya üzeri yanıt kodları vardır. Sorunu önceliklendirmenize ve tanılamanıza yardımcı olması için, uyarı ayrıntılarında hataların özelliklerinin ve ilgili uygulama verilerinin bir analizi verilmiştir. Ayrıca, daha fazla tanılama için Application Insights portalına bağlantılar da vardır. Özelliğin, normal hata oranını tahmin etmek için makine öğrenimi algoritmalarını kullandığından, kurulum veya yapılandırma gerekmez.

Bu özellik, bulutta veya kendi sunucularınızda barındırılan, uygulama isteği veya bağımlılık verileri üreten tüm Web uygulamaları için geçerlidir. Örneğin, [Trackrequest ()](./api-custom-events-metrics.md#trackrequest) veya [trackdependency ()](./api-custom-events-metrics.md#trackdependency)öğesini çağıran bir çalışan rolüne sahipseniz.

[Projeniz için Application Insights](./app-insights-overview.md)ayarladıktan sonra ve uygulamanız en az miktarda veri oluşturursa, hata Anaklarının akıllı algılanması, uygulamanızın normal davranışını öğrenirken ve uyarı gönderebilmeniz için 24 saat sürer.

Örnek bir uyarı aşağıda verilmiştir:

:::image type="content" source="./media/proactive-failure-diagnostics/013.png" alt-text="Hata etrafında küme analizini gösteren örnek akıllı algılama uyarısı." lightbox="./media/proactive-failure-diagnostics/013.png":::

Uyarı ayrıntılarına şu bilgileri söylersiniz:

* Normal uygulama davranışına kıyasla hata oranı.
* Kaç kullanıcının etkilendiğine göre, ne kadar endişelenmeniz gerektiğini biliyoruz.
* Hatalarla ilişkili bir özellik kalıbı. Bu örnekte, belirli bir yanıt kodu, istek adı (işlem) ve uygulama sürümü vardır. Bu, hemen kodunuzda aramaya nereden başlayacağınızı söyler. Diğer olasılıklar belirli bir tarayıcı veya istemci işletim sistemi olabilir.
* Özel durum, günlük izlemeleri ve bağımlılık hatası (veritabanları veya diğer dış bileşenler), ayırt edilen hatalarla ilişkilendirilmiş gibi görünür.
* Application Insights verilerde doğrudan ilgili aramalarla bağlantı sağlar.

## <a name="benefits-of-smart-detection"></a>Akıllı algılama 'nın avantajları
Sıradan [ölçüm uyarıları](../alerts/alerts-log.md) bir sorun olabileceğini bildirir. Ancak akıllı algılama, sizin için tanılama işini başlatır, aksi takdirde sizin tarafınızdan yapacağınız Analize sahip olursunuz. Düzenli olarak paketlenmiş sonuçları elde edersiniz ve bu da sorunun köküne hızlı bir şekilde ulaşmanıza yardımcı olur.

## <a name="how-it-works"></a>Nasıl çalışır?
Akıllı algılama, uygulamanızdan alınan verileri ve özellikle de hata oranlarını izler. Bu kural, özelliğin yanlış olduğu istek sayısını `Successful request` ve özelliğin yanlış olduğu bağımlılık çağrılarının sayısını sayar `Successful call` . İstekler için varsayılan olarak `Successful request == (resultCode < 400)` (kendi [trackrequest](./api-custom-events-metrics.md#trackrequest) aramalarınızı [filtrelemek](./api-filtering-sampling.md#filtering) veya oluşturmak için özel kod yazmadığınız müddetçe). 

Uygulamanızın performansı tipik bir davranış düzenine sahiptir. Bazı istekler veya bağımlılık çağrıları başkalarından daha fazla hataya neden olur; ve genel hata oranı yük arttıkça devam edebilir. Akıllı algılama, bu anormallikleri bulmak için makine öğrenimini kullanır.

Veriler Web uygulamanızdan Application Insights geldikçe, akıllı algılama, geçerli davranışı son birkaç gün içinde görülen desenlerle karşılaştırır. Hata oranındaki anormal bir artış önceki performansla karşılaştırılmasıyla gözlemlendiğinde, bir analiz tetiklenir.

Bir analiz tetiklendiğinde, hizmet, hataların nitelediği bir değer modelini belirlemeyi denemek için başarısız istekte bir küme Analizi gerçekleştirir. 

Yukarıdaki örnekte analiz, çoğu hataların belirli bir sonuç kodu, istek adı, sunucu URL 'SI Konağı ve rol örneği hakkında olduğunu tespit etti. 

Hizmetiniz bu çağrılarla birlikte görüntülendiğinde, çözümleyici, tanımladığı kümedeki isteklerle ilişkili bir özel durum ve bağımlılık hatası arar ve bu isteklerle ilişkili izleme günlüklerinin bir örneğidir.

Sonuç analizi, bir olarak yapılandırmadığınız sürece size uyarı olarak gönderilir.

[El ile ayarladığınız uyarılar](../alerts/alerts-log.md)gibi, tetiklenen uyarının durumunu inceleyebilirsiniz ve sorun düzeltildiğinde çözülebilir. Application Insights kaynağınızın Uyarılar sayfasında uyarı kurallarını yapılandırın. Ancak, diğer uyarıların aksine akıllı algılama 'yı ayarlamanıza veya yapılandırmanıza gerek yoktur. İsterseniz, devre dışı bırakabilir veya hedef e-posta adreslerini değiştirebilirsiniz.

### <a name="alert-logic-details"></a>Uyarı mantığı ayrıntıları

Uyarılar, özel makine öğrenimi algoritmız tarafından tetiklenir, bu nedenle tam uygulama ayrıntılarını paylaşamıyoruz. Bu konuda, bazen temeldeki mantığın nasıl çalıştığı hakkında daha fazla bilgi almanız gerektiğini anladık. Bir uyarının tetiklenip tetiklenmeyeceğini belirlemesi için değerlendirilen birincil faktörler şunlardır: 

* 20 dakikalık bir sıralı zaman penceresinde isteklerin/bağımlılıkların hata yüzdesi analizi.
* Son 20 dakikalık hata yüzdesinin son 40 dakikalık ve son yedi günün hızına bir karşılaştırması ve standart sapmanın X-Times ' i aşan önemli sapmaları aramak.
* Uygulamanın istek hacmine/bağımlılıklara göre farklılık gösteren, en düşük hata yüzdesi için uyarlamalı sınır kullanma.
* Sorun artık 8-24 saat için algılanmadıysa, tetiklenen uyarı İzleyicisi koşulunu otomatik olarak çözebilecek mantık vardır.
  Note: geçerli tasarımda. Akıllı algılama uyarısı çözümlendiğinde bir bildirim veya eylem gönderilmez. Azure portal bir akıllı algılama uyarısının çözümlenmiş olup olmadığını kontrol edebilirsiniz.

## <a name="configure-alerts"></a>Uyarı yapılandırma

Portaldan veya Azure Resource Manager kullanarak akıllı algılama uyarı kuralını devre dışı bırakabilirsiniz ([bkz. şablon örneği](./proactive-arm-config.md)).

Bu uyarı kuralı, e-posta ve Web kancası eylemleri içeren "Application Insights akıllı algılama" adlı ilişkili bir [eylem grubuyla](../alerts/action-groups.md) oluşturulur ve uyarı tetiklendiğinde ek eylemleri tetiklemek için genişletilebilir.

> [!NOTE]
> Bu uyarı kuralından gönderilen e-posta bildirimleri artık varsayılan olarak aboneliğin Izleme okuyucu ve katkıda bulunan rollerinin Izlenmesi ile ilişkili kullanıcılara gönderilir. Bu konuda daha fazla bilgiye [buradan](./proactive-email-notification.md)ulaşabilirsiniz.
> Bu uyarı kuralından gönderilen bildirimler [ortak uyarı şemasını](../alerts/alerts-common-schema.md)izler.
>

Uyarılar sayfasını açın. Hata bozuklukları uyarı kuralları, el ile ayarladığınız uyarılarla birlikte eklenir ve şu anda uyarı durumunda olup olmadığını görebilirsiniz.

:::image type="content" source="./media/proactive-failure-diagnostics/021.png" alt-text="Kaynak Application Insights sayfasında, uyarılar Kutucuğu ' na ve ardından uyarı kurallarını yönet ' e tıklayın." lightbox="./media/proactive-failure-diagnostics/021.png":::

Yapılandırmak için uyarıya tıklayın.

:::image type="content" source="./media/proactive-failure-diagnostics/032.png" alt-text="Kural yapılandırma ekranı." lightbox="./media/proactive-failure-diagnostics/032.png":::

Hata anomali uyarı kuralını devre dışı bırakabildiğinize veya silebildiğinize, ancak aynı Application Insights kaynağında başka bir tane oluşturamazsınız.

## <a name="example-of-failure-anomalies-alert-webhook-payload"></a>Hata anomali uyarı Web kancası yükü örneği

```json
{
    "properties": {
        "essentials": {
            "severity": "Sev3",
            "signalType": "Log",
            "alertState": "New",
            "monitorCondition": "Resolved",
            "monitorService": "Smart Detector",
            "targetResource": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule",
            "targetResourceName": "test-rule",
            "targetResourceGroup": "test-group",
            "targetResourceType": "microsoft.insights/components",
            "sourceCreatedId": "1a0a5b6436a9b2a13377f5c89a3477855276f8208982e0f167697a2b45fcbb3e",
            "alertRule": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.alertsmanagement/smartdetectoralertrules/failure anomalies - test-rule",
            "startDateTime": "2019-10-30T17:52:32.5802978Z",
            "lastModifiedDateTime": "2019-10-30T18:25:23.1072443Z",
            "monitorConditionResolvedDateTime": "2019-10-30T18:25:26.4440603Z",
            "lastModifiedUserName": "System",
            "actionStatus": {
                "isSuppressed": false
            },
            "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls."
        },
        "context": {
            "DetectionSummary": "An abnormal rise in failed request rate",
            "FormattedOccurenceTime": "2019-10-30T17:50:00Z",
            "DetectedFailureRate": "50.0% (200/400 requests)",
            "NormalFailureRate": "0.0% (over the last 30 minutes)",
            "FailureRateChart": [
                [
                    "2019-10-30T05:20:00Z",
                    0
                ],
                [
                    "2019-10-30T05:40:00Z",
                    100
                ],
                [
                    "2019-10-30T06:00:00Z",
                    0
                ],
                [
                    "2019-10-30T06:20:00Z",
                    0
                ],
                [
                    "2019-10-30T06:40:00Z",
                    100
                ],
                [
                    "2019-10-30T07:00:00Z",
                    0
                ],
                [
                    "2019-10-30T07:20:00Z",
                    0
                ],
                [
                    "2019-10-30T07:40:00Z",
                    100
                ],
                [
                    "2019-10-30T08:00:00Z",
                    0
                ],
                [
                    "2019-10-30T08:20:00Z",
                    0
                ],
                [
                    "2019-10-30T08:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:00:00Z",
                    0
                ],
                [
                    "2019-10-30T17:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:00:00Z",
                    0
                ],
                [
                    "2019-10-30T09:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:40:00Z",
                    100
                ],
                [
                    "2019-10-30T10:00:00Z",
                    0
                ],
                [
                    "2019-10-30T10:20:00Z",
                    0
                ],
                [
                    "2019-10-30T10:40:00Z",
                    100
                ],
                [
                    "2019-10-30T11:00:00Z",
                    0
                ],
                [
                    "2019-10-30T11:20:00Z",
                    0
                ],
                [
                    "2019-10-30T11:40:00Z",
                    100
                ],
                [
                    "2019-10-30T12:00:00Z",
                    0
                ],
                [
                    "2019-10-30T12:20:00Z",
                    0
                ],
                [
                    "2019-10-30T12:40:00Z",
                    100
                ],
                [
                    "2019-10-30T13:00:00Z",
                    0
                ],
                [
                    "2019-10-30T13:20:00Z",
                    0
                ],
                [
                    "2019-10-30T13:40:00Z",
                    100
                ],
                [
                    "2019-10-30T14:00:00Z",
                    0
                ],
                [
                    "2019-10-30T14:20:00Z",
                    0
                ],
                [
                    "2019-10-30T14:40:00Z",
                    100
                ],
                [
                    "2019-10-30T15:00:00Z",
                    0
                ],
                [
                    "2019-10-30T15:20:00Z",
                    0
                ],
                [
                    "2019-10-30T15:40:00Z",
                    100
                ],
                [
                    "2019-10-30T16:00:00Z",
                    0
                ],
                [
                    "2019-10-30T16:20:00Z",
                    0
                ],
                [
                    "2019-10-30T16:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:30:00Z",
                    50
                ]
            ],
            "ArmSystemEventsRequest": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourceGroups/test-group/providers/microsoft.insights/components/test-rule/query?query=%0d%0a++++++++++++++++systemEvents%0d%0a++++++++++++++++%7c+where+timestamp+%3e%3d+datetime(%272019-10-30T17%3a20%3a00.0000000Z%27)+%0d%0a++++++++++++++++%7c+where+itemType+%3d%3d+%27systemEvent%27+and+name+%3d%3d+%27ProactiveDetectionInsight%27+%0d%0a++++++++++++++++%7c+where+dimensions.InsightType+in+(%275%27%2c+%277%27)+%0d%0a++++++++++++++++%7c+where+dimensions.InsightDocumentId+%3d%3d+%27718fb0c3-425b-4185-be33-4311dfb4deeb%27+%0d%0a++++++++++++++++%7c+project+dimensions.InsightOneClassTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightExceptionCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightDependencyCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightRequestCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightTraceCorrelationTable%0d%0a++++++++++++&api-version=2018-04-20",
            "LinksTable": [
                {
                    "Link": "<a href=\"https://portal.azure.com/#blade/AppInsightsExtension/ProactiveDetectionFeedBlade/ComponentId/{\"SubscriptionId\":\"4f9b81be-fa32-4f96-aeb3-fc5c3f678df9\",\"ResourceGroup\":\"test-group\",\"Name\":\"test-rule\"}/SelectedItemGroup/718fb0c3-425b-4185-be33-4311dfb4deeb/SelectedItemTime/2019-10-30T17:50:00Z/InsightType/5\" target=\"_blank\">View full details in Application Insights</a>"
                }
            ],
            "SmartDetectorId": "FailureAnomaliesDetector",
            "SmartDetectorName": "Failure Anomalies",
            "AnalysisTimestamp": "2019-10-30T17:52:32.5802978Z"
        },
        "egressConfig": {
            "displayConfig": [
                {
                    "rootJsonNode": null,
                    "sectionName": null,
                    "displayControls": [
                        {
                            "property": "DetectionSummary",
                            "displayName": "What was detected?",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "FormattedOccurenceTime",
                            "displayName": "When did this occur?",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "DetectedFailureRate",
                            "displayName": "Detected failure rate",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "NormalFailureRate",
                            "displayName": "Normal failure rate",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "chartType": "Line",
                            "xAxisType": "Date",
                            "yAxisType": "Percentage",
                            "xAxisName": "",
                            "yAxisName": "",
                            "property": "FailureRateChart",
                            "displayName": "Failure rate over last 12 hours",
                            "type": "Chart",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "defaultLoad": true,
                            "displayConfig": [
                                {
                                    "rootJsonNode": null,
                                    "sectionName": null,
                                    "displayControls": [
                                        {
                                            "showHeader": false,
                                            "columns": [
                                                {
                                                    "property": "Name",
                                                    "displayName": "Name"
                                                },
                                                {
                                                    "property": "Value",
                                                    "displayName": "Value"
                                                }
                                            ],
                                            "property": "tables[0].rows[0][0]",
                                            "displayName": "All of the failed requests had these characteristics:",
                                            "type": "Table",
                                            "isOptional": false,
                                            "isPropertySerialized": true
                                        }
                                    ]
                                }
                            ],
                            "property": "ArmSystemEventsRequest",
                            "displayName": "",
                            "type": "ARMRequest",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "showHeader": false,
                            "columns": [
                                {
                                    "property": "Link",
                                    "displayName": "Link"
                                }
                            ],
                            "property": "LinksTable",
                            "displayName": "Links",
                            "type": "Table",
                            "isOptional": false,
                            "isPropertySerialized": false
                        }
                    ]
                }
            ]
        }
    },
    "id": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule/providers/Microsoft.AlertsManagement/alerts/7daf8739-ca8a-4562-b69a-ff28db4ba0a5",
    "type": "Microsoft.AlertsManagement/alerts",
    "name": "Failure Anomalies - test-rule"
}
```

## <a name="triage-and-diagnose-an-alert"></a>Bir uyarıyı önceliklendirme ve tanılayın

Uyarı, başarısız istek oranının anormal bir şekilde algılandığını gösterir. Uygulamanız veya ortamıyla ilgili bir sorun olabilir.

Daha fazla araştırmak için ' Application Insights tüm ayrıntıları görüntüle ' seçeneğine tıklayın. bu sayfadaki bağlantılar, ilgili istekler, özel durum, bağımlılık veya izlemelerle filtrelenmiş bir [arama sayfasına](./diagnostic-search.md) doğrudan götürür. 

Ayrıca, [Azure Portal](https://portal.azure.com)açabilir, uygulamanız için Application Insights kaynağına gidebilir ve sorunlar sayfasını açabilirsiniz.

' Hata Tanıla 'yı tıklatmak, daha fazla ayrıntı almanıza ve sorunu çözmenize yardımcı olur.

:::image type="content" source="./media/proactive-failure-diagnostics/051.png" alt-text="Tanılama araması." lightbox="./media/proactive-failure-diagnostics/051.png#lightbox":::

İsteklerin yüzdesinden ve etkilenen kullanıcı sayısına göre, sorunun ne kadar acil olduğuna karar verebilirsiniz. Yukarıdaki örnekte,% 78,5 hata oranı normal% 2,2 oranında karşılaştırıldığı için kötü bir şeyin olduğunu gösterir. Öte yandan yalnızca 46 Kullanıcı etkilendi. Uygulamanız ise, ne kadar ciddi olduğunu değerlendirebileceksiniz.

Çoğu durumda, sorunu istek adı, özel durum, bağımlılık hatası ve izleme verilerinden hızlı bir şekilde tanılabileceksiniz.

Bu örnekte, istek sınırına ulaşıldığından SQL veritabanından bir özel durum oluştu.

:::image type="content" source="./media/proactive-failure-diagnostics/052.png" alt-text="Başarısız istek ayrıntıları." lightbox="./media/proactive-failure-diagnostics/052.png":::

## <a name="review-recent-alerts"></a>Son uyarıları gözden geçir

En son tetiklenen uyarılara ulaşmak için Application Insights kaynak sayfasındaki **Uyarılar** ' a tıklayın:

:::image type="content" source="./media/proactive-failure-diagnostics/070.png" alt-text="Uyarı Özeti." lightbox="./media/proactive-failure-diagnostics/070.png":::

## <a name="whats-the-difference-"></a>Fark nedir?..
Hata anomali algılama, Application Insights benzer ancak farklı özellikleri tamamlar.

* [ölçüm uyarıları](../alerts/alerts-log.md) sizin tarafınızdan AYARLANıR ve CPU doluluk, istek hızları, sayfa yükleme süreleri vb. gibi çok çeşitli ölçümleri izleyebilirsiniz. Bunları, örneğin daha fazla kaynak eklemeniz gerekiyorsa, sizi uyarmak için kullanabilirsiniz. Bunun aksine, hata bozuklukları akıllı algılama, Web uygulamanızın normal davranışına kıyasla Web uygulamanızın başarısız istek oranı arttıkça neredeyse gerçek zamanlı olarak bildirimde bulunan çok sayıda kritik ölçümü (Şu anda yalnızca başarısız istek hızı) ele alır. Ölçüm uyarılarından farklı olarak, akıllı algılama davranıştaki yanıt değişikliklerinde eşikleri otomatik olarak ayarlar ve güncelleştirir. Akıllı algılama, tanılama işini sizin için de başlatır ve sorunları çözmek için size zaman kazandırır.

* [Performans anlılıkları akıllı algılamasında](proactive-performance-diagnostics.md) Ayrıca, ölçümlerinizin olağandışı desenlerini bulması için makine zekası kullanılır ve sizin tarafınızdan yapılandırma yapmanız gerekmez. Ancak, hata anormallerinin akıllı algılamalarından farklı olarak, performans anormallerinin akıllı algılamasında kullanılması amaç, kullanım açısından kötü olarak sunulmayan, örneğin belirli bir tarayıcı türü üzerinde belirli sayfalara yönelik olan segmentleri bulmaktan kaynaklanabilir. Analiz her gün gerçekleştirilir ve herhangi bir sonuç bulunursa, bir uyarıdan çok daha az acil olma olasılığı yüksektir. Buna karşılık, hata anormallikleri için analiz, gelen uygulama verilerinde sürekli olarak gerçekleştirilir ve sunucu hatası oranları beklenenden büyükse dakikalar içinde bilgilendirilirsiniz.

## <a name="if-you-receive-a-smart-detection-alert"></a>Akıllı bir algılama uyarısı alırsanız
*Bu uyarıyı neden aldım?*

* Önceki dönemin normal taban çizgisiyle karşılaştırıldığında başarısız istek hızında anormal bir artış tespit ettik. Hataların ve ilişkili uygulama verilerinin analizinden sonra, Bakılacak bir sorun olduğunu düşündük.

*Bildirimde kesinlikle bir sorun var mı?*

* Uygulama kesintisi veya düşme üzerinde uyarı denemeyi deniyoruz, ancak yalnızca semantiğini ve uygulama ya da kullanıcılar üzerindeki etkisini tam olarak anlayabiliriz.

*Bu nedenle, uygulama verilerimi arıyorsunuz musunuz?*

* Hayır. Hizmet tamamen otomatiktir. Yalnızca bildirimleri alırsınız. Verileriniz [özeldir](./data-retention-privacy.md).

*Bu uyarıya abone olmam gerekiyor mu?*

* Hayır. İstek verileri gönderen her uygulamanın akıllı algılama uyarısı kuralı vardır.

*Bunun yerine iş arkadaşlarıma gönderilen bildirimleri iptal edebilir veya alabilir miyim?*

* Evet, uyarı kuralları ' nda, yapılandırmak için akıllı algılama kuralına tıklayın. Uyarıyı devre dışı bırakabilir veya uyarının alıcılarını değiştirebilirsiniz.

*E-postayı kaybettim. Portalda bildirimleri nerede bulabilirim?*

* Etkinlik günlüklerinde. Azure 'da, uygulamanız için Application Insights kaynağını açın ve etkinlik günlükleri ' ni seçin.

*Bazı uyarılar bilinen sorunlarla ilgilidir ve bunları almak istemiyorum.*

* [Uyarı eylemi kuralları](../alerts/alerts-action-rules.md) gizleme özelliğini kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Bu tanılama araçları uygulamanızdaki verileri incelemenize yardımcı olur:

* [Ölçüm Gezgini](../essentials/metrics-charts.md)
* [Arama Gezgini](./diagnostic-search.md)
* [Analytics-güçlü sorgu dili](../logs/log-analytics-tutorial.md)

Akıllı algılamalar otomatiktir. Ancak daha fazla uyarı kurmak istiyor olabilirsiniz?

* [El ile yapılandırılmış ölçüm uyarıları](../alerts/alerts-log.md)
* [Kullanılabilirlik web testleri](./monitor-web-app-availability.md)