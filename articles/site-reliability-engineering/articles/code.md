---
title: 'SSS: SRE ve kodlama | Microsoft Docs'
titleSuffix: Azure
description: 'SSS: SRE ve kodlama arasındaki ilişkiyi anlama'
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: b8865fdd53f4947b17a3621a128fc83f3d93d3e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90089080"
---
# <a name="frequently-asked-questions-do-i-need-to-know-how-to-code-to-get-involved-with-sre"></a>Sık sorulan sorular: SRE ile ilgili nasıl kod alınacağını bilmem gerekir mi?

Bireyler SRE 'de ele alınması düşünüldüğünde ve takımlar, SRE uygulamalarında bir sorun olduğunu düşünürken, en sık kullanılan bir soru "nasıl kod oluşturulacağını bilmeniz gerekir?"

Kısa cevap: Evet. 

Ancak tam yanıt daha fazla bir bit daha Ced. Her biri için gereken kodlama uzmanlığı düzeyi ile birlikte, kodlamanın site güvenilirliği mühendisliğinde oynatılmasına neden olan üç yere göz atalım. Bu liste tamamlanmaz, ancak bu senaryolar daha yaygın kullanım çalışmalarından bazılarıdır.

## <a name="scenario-1-removing-toil-through-automation"></a>Senaryo 1: Otomasyon aracılığıyla toıl kaldırma

Site güvenilirliği mühendisleri ve SRE uygulamalarını kullanan diğerleri, toıl 'yi kaldırmak için mümkün olan her yerde çalışır. "Toıl", SRE 'deki belirli bir şeyi ifade ediyor. Toıl, belirli özelliklere sahip bir insan tarafından gerçekleştirilen işlem çalışmalarını ifade eder. Zahmetin uzun vadede kazanılan bir değeri yoktur. Anlamlı herhangi bir yoldan hizmeti ileri taşımaz. Çoğunlukla tekrarlanan ve (otomatikleştirilebilse de) büyük ölçüde el ile yapılan bir şeydir. Hizmet veya sistemler zamanla büyüdükçe, bununla orantılı olarak söz konusu sisteme yönelik isteklerin sayısı da büyük olasılıkla artar ve daha da fazla el emeği gerektirir.

Örneğin, bir hizmet, SRE ekibinin her hafta bir şeyi sıfırlamasını veya yeni hesaplar ile disk alanı sağlamayı veya el ile yeniden başlatmasını gerektiriyorsa, bu işlem o kadar çalışır. Bu eylemlerin tamamlanması uzun vadede ve kalıcı bir şekilde hizmeti daha iyi hale getirmez. Bu eylemlerin büyük olasılıkla sürekli tekrar tekrar gerçekleştirilmesi gerekecektir.

SRE'ler zahmetli işlerden nefret eder. Mümkün ve uygun olduğu her durumda bunu ortadan kaldırmaya çalışır. SRE'de otomasyonun gündeme geldiği noktalardan biri budur. Bu istekler otomatik olarak işlenebiliyorsanız, ekip, daha fazla ve etkili bir şekilde çalışmak üzere çalışır.

*Kodlama uzmanlığı*: Otomasyon, bazı kodlama uzmanlığını gerektirir, ancak tam yazılım mühendisliği becerileri gerektirmek zorunda değildir. Küçük betikler (Belki de PowerShell veya Bourne kabuğu) yazabilir ya da bir kod içeren bir kod oluştursanız bile [https://docs.microsoft.com/azure/logic-apps/logic-apps-overview](Azure Logic app) , bu uygulama yine de devam edebilir.

## <a name="scenario-2-control-through-apisdomain-specific-languages-dslstemplates"></a>Senaryo 2: API 'Ler/etki alanına özgü diller (DSLs)/Templates aracılığıyla denetim

SRE çalışması için kesinlikle gerekli olmasa da, API 'Ler, DSLs ve şablonlar (özellikle bulut ortamları) üzerinden ortamları denetleyebilmek, SREs 'Lerin çalışmalarını ölçeklendirmesine izin verir. Altyapı sağlama/devre dışı bırakma, izlemeyi yapılandırma ve çeşitli hizmetlerin tümleştirilmesi, kodlama aracılığıyla çok daha verimli hale gelir.

*Kodlama uzmanlığı*: Önceki senaryo gibi bazı kodlama uzmanlığını gerektirir, ancak tam yazılım mühendisliği becerileri gerektirmemelidir. Daha önce bahsedilen betiklerin ve Logic Apps 'e ek olarak, [https://docs.microsoft.com/azure/azure-resource-manager/templates/overview](Azure Resource Manager Templates) en az kodlama deneyimiyle birlikte de kullanılabilir.

## <a name="scenario-3-fixing-the-code"></a>Senaryo 3: kodu Düzeltme

Site güvenilirliği mühendisleri, bir sistemin güvenilirliğini iyileştirmeye bakar. Bu hedef bazen bir sistemin kaynak koduna yönelik olarak öngörüde bulunmasını gerektirir, sorunu belirler ve genellikle kod tabanına geri bir düzelme katkı sağlar. Bu işin gelişmiş algoritmaların mümkündür düzeyi, duruma göre büyük ölçüde farklılık gösterebileceğinden, kodlama uzmanlığı bu durumlarda kesin bir gereksinimdir.

*Kodlama uzmanlığı*: Bu senaryoda genellikle tam yazılım mühendisliği uzmanlığı gereklidir.


## <a name="next-steps"></a>Sonraki adımlar

Site güvenilirliği Mühendisliği ve düşük kodlu iş hakkında daha fazla bilgi edinmek mi istiyorsunuz? Yukarıda bağlantılı ürün belgeleri olan [site güvenilirliği mühendislik hub 'ına](../index.yml)göz atın.
