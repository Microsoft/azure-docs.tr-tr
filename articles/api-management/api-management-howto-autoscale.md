---
title: Azure API Management örneğinin otomatik olarak otomatik ölçeğini yapılandırma | Microsoft Docs
description: Bu konuda, bir Azure API Management örneği için otomatik ölçeklendirme davranışının nasıl ayarlanacağı açıklanır.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 8c1c96fdb1f4f42c7592791881b855f74d411171
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018280"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Azure API Management örneğini otomatik olarak ölçeklendirme  

Azure API Management hizmet örneği, bir dizi kurala göre otomatik olarak ölçeklendirebilir. Bu davranış, Azure Izleyici aracılığıyla etkinleştirilebilir ve yapılandırılabilir ve yalnızca Azure API Management hizmetinin **Standart** ve **Premium** katmanlarında desteklenir.

Makale, otomatik ölçeklendirmeyi yapılandırma sürecini adım adım yönlendirir ve otomatik ölçeklendirme kurallarının en iyi yapılandırmasını önerir.

> [!NOTE]
> **Tüketim** katmanındaki API Management hizmeti, herhangi bir ek yapılandırma gerekmeden, trafiğe göre otomatik olarak ölçeklendirilir.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları izleyerek şunları yapmanız gerekir:

+ Etkin bir Azure aboneliğiniz olmalıdır.
+ Azure API Management örneğine sahip olmanız gerekir. Daha fazla bilgi için bkz. [Azure API Management örneği oluşturma](get-started-create-service-instance.md).
+ [Azure API Management örneğinin kapasite](api-management-capacity.md)kavramını anlayın.
+ Maliyet sonuçları dahil olmak üzere [bir Azure API Management örneğinin el ile ölçeklendirilmesini](upgrade-and-scale.md)anlayın.

[!INCLUDE [premium-standard.md](../../includes/api-management-availability-premium-standard.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Azure API Management otomatik ölçeklendirme sınırlamaları

Ölçek kararlarının belirli sınırlamaları ve sonuçlarının otomatik ölçeklendirme davranışı yapılandırılmadan önce değerlendirilmesi gerekir.

+ Otomatik ölçeklendirme, yalnızca Azure API Management hizmetinin **Standart** ve **Premium** katmanlarında etkinleştirilebilir.
+ Fiyatlandırma katmanları, bir hizmet örneği için en fazla birim sayısını da belirtir.
+ Ölçeklendirme işlemi en az 20 dakika sürer.
+ Hizmet başka bir işlem tarafından kilitliyse, ölçeklendirme isteği başarısız olur ve otomatik olarak yeniden dener.
+ Çoklu bölgesel dağıtımlara sahip bir hizmet olması durumunda yalnızca **birincil konumdaki** birimler ölçeklendirilebilir. Diğer konumlardaki birimler ölçeklendirilemez.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Azure API Management hizmeti için otomatik ölçeklendirmeyi etkinleştirme ve yapılandırma

Bir Azure API Management hizmeti için otomatik ölçeklendirmeyi yapılandırmak için aşağıdaki adımları izleyin:

1. Azure portal **izleme** örneğine gidin.

    ![Azure İzleyici](media/api-management-howto-autoscale/01.png)

2. Soldaki menüden **Otomatik ölçek** ' i seçin.

    ![Azure Izleyici otomatik ölçeklendirme kaynağı](media/api-management-howto-autoscale/02.png)

3. Açılan menülerde filtreleri temel alarak Azure API Management hizmetinizi bulun.
4. İstediğiniz Azure API Management hizmet örneğini seçin.
5. Yeni açılan bölümünde **Otomatik ölçeklendirmeyi etkinleştir** düğmesine tıklayın.

    ![Azure Izleyici otomatik ölçeklendirme etkin](media/api-management-howto-autoscale/03.png)

6. **Kurallar** bölümünde **+ Kural Ekle**' ye tıklayın.

    ![Azure Izleyici otomatik ölçeklendirme kuralı ekle](media/api-management-howto-autoscale/04.png)

7. Yeni bir ölçek genişletme kuralı tanımlayın.

   Örneğin, bir ölçek genişletme kuralı, son 30 dakikalık ortalama kapasite ölçümü% 80 ' ü aştığında bir Azure API Management biriminin eklenmesini tetikleyebilir. Aşağıdaki tablo böyle bir kural için yapılandırma sağlar.

    | Parametre             | Value             | Notlar                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Ölçüm kaynağı         | Geçerli kaynak  | Geçerli Azure API Management kaynak ölçümlerine göre kuralı tanımlayın.                                                                                                                                                                                                     |
    | *Ölçütler*            |                   |                                                                                                                                                                                                                                                                                 |
    | Zaman toplama      | Average           |                                                                                                                                                                                                                                                                                 |
    | Ölçüm adı           | Kapasite          | Kapasite ölçümü, bir Azure API Management örneğinin kaynaklarının kullanımını yansıtan bir Azure API Management ölçümdür.                                                                                                                                                            |
    | Zaman dilimi istatistiği  | Average           |                                                                                                                                                                                                                                                                                 |
    | Operator              | Büyüktür      |                                                                                                                                                                                                                                                                                 |
    | Eşik             | %80               | Ortalama kapasite ölçümü eşiği.                                                                                                                                                                                                                                 |
    | Süre (dakika cinsinden) | 30                | Kapasite ölçüsünün ortalama olarak kullanım desenlerine özgü değeri. Zaman döneminin ne kadar uzun olduğu, yeniden işlemin aralıklı olarak ne kadar iyi olması, ölçek genişletme kararına göre daha az etkiye sahip olacaktır. Ancak, ölçek genişletme tetikleyicisini de erteleyecektir. |
    | *Eylem*              |                   |                                                                                                                                                                                                                                                                                 |
    | Çalışma             | Sayıyı şu kadar artır: |                                                                                                                                                                                                                                                                                 |
    | Örnek sayısı        | 1\.                 | Azure API Management örneğini 1 birim olarak ölçeklendirin.                                                                                                                                                                                                                          |
    | Seyrek Erişimli (dakika)   | 60                | Azure API Management hizmetinin ölçeği genişletmek için en az 20 dakika sürer. Çoğu durumda, 60 dakikalık seyrek erişimli süre çok sayıda ölçeği tetiklemeyi engeller.                                                                                                  |

8. Kuralı kaydetmek için **Ekle** ' ye tıklayın.

    ![Azure Izleyici genişleme kuralı](media/api-management-howto-autoscale/05.png)

9. **+ Kural Ekle**' ye tıklayın.

    Bu kez, kuraldaki bir ölçeğin tanımlanması gerekir. API 'lerin kullanımı azaldıkça kaynakların harcanmadığından emin olur.

10. Kuralda yeni bir ölçek tanımlayın.

    Örneğin, bir kuralda ölçek, son 30 dakikalık ortalama kapasite ölçümü% 35 ' dan düşük olduğunda bir Azure API Management birimini kaldırmayı tetikleyebilir. Aşağıdaki tablo böyle bir kural için yapılandırma sağlar.

    | Parametre             | Value             | Notlar                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Ölçüm kaynağı         | Geçerli kaynak  | Geçerli Azure API Management kaynak ölçümlerine göre kuralı tanımlayın.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Ölçütler*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Zaman toplama      | Average           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Ölçüm adı           | Kapasite          | Ölçek genişletme kuralı için kullanılan ölçüm ile aynı ölçüm.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Zaman dilimi istatistiği  | Average           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operator              | Küçüktür         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Eşik             | % 35               | Aynı şekilde, ölçek genişletme kuralına benzer şekilde, bu değer Azure API Management kullanım desenlerine göre büyük ölçüde değişir. |
    | Süre (dakika cinsinden) | 30                | Ölçek genişletme kuralı için kullanılan değerle aynı değer.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Eylem*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Çalışma             | Sayıyı şu kadar azalt: | Ölçek genişletme kuralı için kullanılmış olan ' ın tersi.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Örnek sayısı        | 1\.                 | Ölçek genişletme kuralı için kullanılan değerle aynı değer.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Seyrek Erişimli (dakika)   | 90                | Ölçek ölçeği bir ölçeğe göre daha pasif olmalıdır, bu nedenle seyrek erişimli dönemin daha uzun olması gerekir.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Kuralı kaydetmek için **Ekle** ' ye tıklayın.

    ![Kuralda Azure Izleyici ölçeği](media/api-management-howto-autoscale/06.png)

12. **Maksimum** Azure API Management birimi sayısını ayarlayın.

    > [!NOTE]
    > Azure API Management, bir örneğin ölçeklenebilen bir birim sınırı içerir. Sınır, bir hizmet katmanına bağlıdır.

    ![Kuralda Azure Izleyici ölçeği](media/api-management-howto-autoscale/07.png)

13. **Kaydet**’e tıklayın. Otomatik ölçeklendirme yapılandırılmış.

## <a name="next-steps"></a>Sonraki adımlar

+ [Bir Azure API Management hizmeti örneğini birden fazla Azure bölgesine dağıtma](api-management-howto-deploy-multi-region.md)
