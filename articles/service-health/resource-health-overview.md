---
title: Azure Kaynak Durumu genel bakış | Microsoft Docs
description: Azure Kaynak Durumu genel bakış
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 05/10/2019
ms.openlocfilehash: 9c2096f94f38d13288c6ce3742252bc6d576835a
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854222"
---
# <a name="resource-health-overview"></a>Kaynak Durumu genel bakış
 
Azure Kaynak Durumu, Azure kaynaklarınızı etkileyen hizmet sorunlarını tanılamanıza ve destek almanıza yardımcı olur. Kaynaklarınızın geçerli ve geçmiş durumunu raporlar.

[Azure durum](https://status.azure.com) , geniş bir Azure müşterisi kümesini etkileyen hizmet sorunları hakkında raporlar. Kaynak Durumu kaynaklarınızın sistem durumunun kişiselleştirilmiş bir panosunu sağlar. Kaynak Durumu, Azure hizmet sorunları nedeniyle kaynaklarınızın kullanılamadığı tüm süreleri gösterir. Bu veriler, bir SLA 'nın ihlal edildiğini görmenizi kolaylaştırır.

## <a name="resource-definition-and-health-assessment"></a>Kaynak tanımı ve sistem durumu değerlendirmesi

*Kaynak* , sanal makine, Web UYGULAMASı veya SQL veritabanı gibi belirli bir Azure hizmeti örneğidir. Kaynak Durumu, bir kaynağın sağlıklı olup olmadığını değerlendirmek için farklı Azure hizmetlerinden gelen sinyallere bağımlıdır. Bir kaynak sağlıksız ise, Kaynak Durumu sorunun kaynağını belirlemede ek bilgileri analiz eder. Ayrıca, Microsoft 'un sorunu çözmesi ve bunu ele almak için yapabileceklerinizi tanımladığı eylemleri de raporlar.

Sistem durumunun değerlendirilme hakkında daha fazla bilgi için [Azure Kaynak durumu](resource-health-checks-resource-types.md)adresindeki kaynak türleri ve sistem durumu denetimleri listesine bakın.

## <a name="health-status"></a>Sistem durumu

Bir kaynağın sistem durumu aşağıdaki durumlardan biri olarak görüntülenir.

### <a name="available"></a>Kullanılabilir

*Kullanılabilir* , kaynağın sistem durumunu etkileyen bir olay algılanmadığı anlamına gelir. Son 24 saat boyunca kaynağın planlanmamış kapalı kalma süresinden kurtarıldığı durumlarda "son çözümlenmiş" bildirimi görürsünüz.

!["Son çözümlenmiş" bildirimine sahip bir sanal makine için * kullanılabilir * durumu](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Kullanılamaz

*Kullanılamıyor* , hizmetin, kaynağın sistem durumunu etkileyen devam eden bir platform veya platform olmayan bir olay algıladığı anlamına gelir.

#### <a name="platform-events"></a>Platform olayları

Platform olayları, Azure altyapısının birden çok bileşeni tarafından tetiklenir. Hem zamanlanmış eylemler (örneğin, planlı bakım) hem de beklenmeyen olaylar (örneğin, planlanmamış bir konak yeniden başlatması) içerirler.

Kaynak Durumu olay ve kurtarma işlemi hakkında ek ayrıntılar sağlar. Ayrıca etkin bir destek sözleşmeniz olmasa bile Microsoft Desteği ile iletişim kurabilmenizi sağlar.

![Bir platform olayı nedeniyle sanal makine için * kullanılamıyor * durumu](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Platform dışı olaylar

Platform dışı olaylar Kullanıcı eylemleri tarafından tetiklenir. Örnek olarak, bir sanal makineyi durdurmayı veya Reda için Azure önbelleğine en fazla bağlantı sayısına ulaşmanız verilebilir.

![Platform olmayan bir olay nedeniyle sanal makine için "kullanılamıyor" durumu](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Bilinmiyor

*Bilinmiyor* , kaynak durumu 10 dakikadan uzun süredir kaynak hakkında bilgi almamış anlamına gelir. Bu durum kaynağın durumunun kesin bir göstergesi olmamasına karşın, sorun giderme için önemli bir veri noktasıdır.

Kaynak beklendiği gibi çalışıyorsa, kaynağın durumu birkaç dakika sonra *kullanılabilir* olarak değişir.

Kaynakla ilgili sorunlarla karşılaşırsanız, *Bilinmeyen* sistem durumu, platformda bir olayın kaynağı etkilediği anlamına gelebilir.

![Bir sanal makine için * Unknown * durumu](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Düşürüldü

*Düşürülmüş* , kaynağınızın performans kaybı algıladığı, ancak kullanılmaya devam ettiği anlamına gelir.

Farklı kaynakların, düşürüldüğünü bildirdikleri zaman kendi ölçütleri vardır.

![Bir sanal makine için * düşürülmüş * durumu](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Hatalı durum bildirme

Geçerli sistem durumunun yanlış olduğunu düşünüyorsanız bize, **yanlış sistem durumu bildir**' i seçerek bunu söyleyebilirsiniz. Bir Azure sorununun sizi etkilediği durumlarda Kaynak Durumu destek ile iletişim kurmanız önerilir.

![Yanlış bir durum hakkında bilgi göndermek için form](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>Geçmiş bilgileri

Kaynak Durumu **sistem durumu geçmişi** bölümünde 14 güne geçmiş bir geçmişi erişebilirsiniz.

![Son iki hafta içindeki Kaynak Durumu olaylarının listesi](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>başlarken

Bir kaynak için Kaynak Durumu açmak için:

1. Azure Portal’da oturum açın.
2. Kaynağınızı bulun.
3. Sol bölmedeki kaynak menüsünde **kaynak durumu**' nu seçin.

![Kaynak görünümünden Kaynak Durumu açılıyor](./media/resource-health-overview/from-resource-blade.png)

Ayrıca, **tüm hizmetler** ' i seçip filtre metin kutusuna **kaynak durumu** yazarak da kaynak durumu erişebilirsiniz. **Yardım + Destek** bölmesinde [kaynak durumu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth)' nu seçin.

!["Tüm hizmetler" içinden Kaynak Durumu açılıyor](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Sonraki adımlar

Kaynak Durumu hakkında daha fazla bilgi edinmek için bu başvurulara göz atın:
-  [Azure Kaynak Durumu içindeki kaynak türleri ve durum denetimleri](resource-health-checks-resource-types.md)
-  [Azure Kaynak Durumu hakkında sık sorulan sorular](resource-health-faq.md)
