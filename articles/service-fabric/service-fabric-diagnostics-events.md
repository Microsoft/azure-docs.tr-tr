---
title: Azure Service Fabric olayları
description: Azure Service Fabric kümenizi izlemenize yardımcı olmak için kutudan çıkan Service Fabric olayları hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 581bb3f16941fce660e0f464ed8ea9389b034b12
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628823"
---
# <a name="service-fabric-events"></a>Service Fabric olayları 

Service Fabric platformu, kümenizde oluşan önemli işletimsel etkinlikler için birkaç yapılandırılmış olay yazar. Bu Aralık, küme yükseltmelerinden çoğaltma yerleştirme kararlarından yapılır. Service Fabric her olay, kümedeki aşağıdaki varlıklardan birine eşlenir.
* Küme
* Uygulama
* Hizmet
* Bölüm
* Çoğaltma 
* Kapsayıcı

[Service Fabric olaylarının Platform listesi](service-fabric-diagnostics-event-generation-operational.md)tarafından kullanıma sunulan olayların tam listesini görmek için.

İşte, kümenize yönelik olayları görmeniz gereken bazı senaryolar örnekleri aşağıda verilmiştir. 
* Düğüm yaşam döngüsü olayları: düğümler, aşağı doğru, ölçeklendirilir/dışarı ve devre dışı bırakıldığında, bu olaylar ne olduğunu gösterir ve makinenin kendisinde bir sorun olup olmadığını veya bir düğümün durumunu değiştirmek için SF aracılığıyla çağrılan bir API olup olmadığını belirlemenize yardımcı olur.
* Küme yükseltme: kümeniz yükseltildiğinde (SF sürümü veya yapılandırma değişikliği), yükseltme başlatma, yükseltme etki alanlarınızın her birini geri alma ve tamamlanma (veya geri alma) işlemini görürsünüz. 
* Uygulama yükseltmeleri: küme yükseltmeleri gibi, yükseltmenin yaptığı gibi kapsamlı bir olay kümesi vardır. Bu olaylar, bir yükseltmenin ne zaman zamanlandığını, yükseltmenin geçerli durumunu ve genel olay sırasını anlamak için yararlı olabilir. Bu, hangi yükseltmelerin başarıyla oluşturulduğunu veya geri alma işleminin tetiklenip tetiklenmediğini görmek için yararlıdır.
* Uygulama/hizmet dağıtımı/silme: oluşturulan veya silinmekte olan her uygulama, hizmet ve kapsayıcının olayları vardır, örneğin, yineleme sayısını artırma
* Bölüm taşımaları (yeniden yapılandırma): durum bilgisi olan bir bölüm bir yeniden yapılandırma (çoğaltma kümesindeki bir değişiklik) üzerinden geçtiğinde bir olay günlüğe kaydedilir. Bu, Bölüm çoğaltma kümesi 'nin hangi sıklıkta değiştiğini veya yük devretmekte olduğunu anlamaya çalışıyorsanız veya herhangi bir zamanda birincil çoğaltmanız hangi düğümde çalıştırmakta olduğunu anlamak için kullanışlıdır.
* Chaos olayları: Service Fabric [Chaos](service-fabric-controlled-chaos.md) hizmeti kullanılırken, hizmet her başlatıldığında veya durdurulduğunda veya sistemde bir hata söz konusu olduğunda olaylar görüntülenir.
* Sistem durumu olayları: Service Fabric bir uyarı veya bir hata durumu raporu oluşturulduğunda veya bir varlık bir Tamam sistem durumuna geçtiğinde veya bir sistem durumu raporunun süresi dolarsa sistem durumu olaylarını kullanıma sunar. Bu olaylar, bir varlığın geçmiş sistem durumu istatistiklerini izlemek için çok yararlıdır. 

## <a name="how-to-access-events"></a>Olaylara erişme

Service Fabric olaylarına erişmek için birkaç farklı yol vardır:
* Olaylar, ETW/Windows olay günlükleri gibi standart kanallarla günlüğe kaydedilir ve Azure Izleyici günlükleri gibi bunları destekleyen herhangi bir izleme aracı tarafından görselleştirilebilir. Varsayılan olarak, portalda oluşturulan kümeler tanılamayı açıktır ve Windows Azure tanılama Aracısı 'nın olayları Azure Tablo depolamaya göndermesini sağlar, ancak yine de bunu Log Analytics kaynağı ile tümleştirmeniz gerekir. Daha fazla günlük veya performans sayacı ve [Azure izleyici günlükleri tümleştirmesi](service-fabric-diagnostics-event-analysis-oms.md) için, kümenizin tanılama yapılandırmasını değiştirmek üzere [Azure tanılama aracısını](service-fabric-diagnostics-event-aggregation-wad.md) yapılandırma hakkında daha fazla bilgi edinin
* EventStore hizmeti 'nin doğrudan veya Service Fabric Istemci kitaplığı aracılığıyla küme sorgulamanızı sağlayan REST API 'Leri. Bkz. [küme olayları Için sorgu EventStore API 'leri](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Sonraki adımlar
* Kümenizi izleme hakkında daha fazla bilgi [ve kümeyi ve platformu izleme](service-fabric-diagnostics-event-generation-infra.md).
* EventStore hizmeti hakkında daha fazla bilgi edinin- [Eventstore hizmetine genel bakış](service-fabric-diagnostics-eventstore.md)
