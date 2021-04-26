---
title: Azure Dosya Eşitleme bulut katmanlaması izleme | Microsoft Docs
description: Bulut katmanlama ilkelerinizi izlemek için kullanılacak ölçülerle ilgili ayrıntılar.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cbdde16ff214077d7fe4814c8841d805fe05c37c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797119"
---
# <a name="monitor-cloud-tiering"></a>Bulut katmanlaması izleme
Bulut katmanlama ilkenizi izleyebilmeniz için iki yol vardır: sunucu uç noktası özellikleri dikey penceresi ve Azure Izleyici.

## <a name="monitoring-via-server-endpoint"></a>Sunucu uç noktası üzerinden izleme

[Azure Portal](https://portal.azure.com/)oturum açın, ardından izlemek istediğiniz sunucu uç noktası için **sunucu uç noktası özellikleri** dikey penceresine gidin ve bulut katmanlaması bölümüne gidin. 

![Sunucu uç noktası özelliklerindeki bulut katmanlama bölümünün ekran görüntüsü.](media/storage-sync-monitoring-cloud-tiering/cloud-tiering-monitoring-5.png)

**Alan tasarrufları** , bulut katmanlaması etkinleştirilerek kaydedilen alan miktarıdır. Yerel birim boyutunuz tüm verilerinizi tutacak kadar büyükse, %0 alan tasarrufu elde edersiniz. %0 veya daha az alan tasarrufu varsa, bulut katmanlaması, geçerli yerel önbellek boyutunuz için yararlı olmadığını gösterebilir. 

**Önbellek isabet oranı** , yerel önbelleğiniz için açtığınız dosyaların yüzdesidir. Önbellek isabet oranı, doğrudan önbellekten açılan dosyalar ve açık olan toplam dosya sayısından hesaplanır. %100 önbellek isabet oranı, son saat içinde açtığınız tüm dosyaların zaten yerel önbelleğinizin içinde olduğu anlamına gelir. Amacınız çıkış azaltmaktır, bu, geçerli ilkenizin iyi şekilde çalıştığını gösterir.

> [!NOTE]
> Rastgele erişim desenlerine sahip iş yükleri genellikle daha düşük önbellek isabet oranlarına sahiptir. 

**Toplam Boyut (bulut)** , buluta eşitlenmiş dosyalarınızın boyutudur. 

**Önbelleğe alınan boyut (sunucu)** , sunucunuzdaki hem indirilen hem de katmanlı dosyaların toplam boyutudur. Bazen, önbellekteki boyut, buluttaki dosyalarınızın toplam boyutundan daha büyük olabilir. Sunucudaki birimin küme boyutu gibi değişkenler buna neden olabilir. Önbelleğe alınan boyut istediğiniz daha büyükse, birim boş alan ilkenizi artırmayı göz önünde bulundurun. 

**Etkin birim ilkesi** , sunucu uç noktanızın bulunduğu birimde ne kadar boş alan olduğunu anlamak için Azure dosya eşitleme tarafından kullanılır. Aynı birimde birden çok sunucu uç noktası olduğunda, sunucu uç noktaları arasındaki en yüksek hacimli boş alan ilkesi, söz konusu birimdeki tüm sunucu uç noktaları için etkili birim ilkesi olur. Örneğin, aynı birimde iki sunucu uç noktası varsa, biri %30 birim boş alanı ve diğeri %50 birim boş alanı ile, her iki sunucu uç noktası için geçerli birim boş alan ilkesi %50 olacaktır.

**Geçerli birim boş alanı** , şirket içi sunucunuzda Şu anda kullanılabilir olan birim boş alanıdır. Yüksek çıkış yaptıysanız ancak birim boş alan ilkesi başlamadan önce kullanılabilir daha fazla boş alan varsa, tarih ilkenizi devre dışı bırakmayı göz önünde bulundurun. Şu anda katmanlı dosyalardan başka bir sorun olabilir. en son erişilen dosya, ilke başlatılmadan önce kalan birimdeki boş alandan daha büyük. Bu durumda, yerel birim boyutunuzu artırmayı düşünün. 

## <a name="monitoring-via-azure-monitor"></a>Azure Izleyici aracılığıyla izleme

**Depolama Eşitleme hizmetine** gidin ve yan gezinmede **ölçümler** ' i seçin. 

Özellikle bulut katmanlarından çıkışı izlemek için kullanabileceğiniz üç farklı ölçüm vardır:

- Bulut katmanlama geri çağırma boyutu
    - Bu, bayt cinsinden geri çekilen verilerin toplam boyutudur ve ne kadar veri geri çekmekte olduğunu belirlemek için kullanılabilir.
- Uygulamaya göre bulut katmanlama geri çağırma boyutu
    - Bu, bir uygulama tarafından bayt olarak geri çekilen toplam veri boyutudur ve verilerin geri çekemi olduğunu belirlemek için kullanılabilir.
- Bulut katmanlama geri çekme performansı
    - Bu, verilerin bayt cinsinden ne kadar hızlı geri çekmekte olduğunu ve performansla ilgili endişeleriniz varsa kullanılması gerektiğini ölçer. 

Grafiklerinizin görüntülenmesini istediğiniz şekilde daha belirgin olması için, **Filtre Ekle** ve **bölmeyi Uygula**' yı kullanmayı düşünün.
 
Azure Dosya Eşitleme yönelik farklı ölçüm türleri ve bunların nasıl kullanılacağı hakkında ayrıntılı bilgi için bkz. [Monitor Azure dosya eşitleme](file-sync-monitoring.md).

Ölçümleri kullanma hakkında ayrıntılı bilgi için bkz [. Azure Ölçüm Gezgini kullanmaya başlama.](../../azure-monitor/essentials/metrics-getting-started.md)

Bulut katmanlama ilkenizi değiştirmek isterseniz bkz. [bulut katmanlama Ilkelerini seçme](file-sync-choose-cloud-tiering-policies.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Dosya Eşitleme dağıtımı planlama](file-sync-planning.md)