---
title: Azure Container Registry etiket ve sürüm görüntüleri
description: Resimleri bir Azure Container Registry 'ye gönderirken ve resimleri çekirken Docker kapsayıcı görüntülerini etiketleme ve sürüm oluşturma için en iyi uygulamalar
services: container-registry
author: stevelasker
ms.service: container-registry
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: 41013fb5831d09d7a4334e94d2b8b39e0cafe4d2
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931571"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Kapsayıcı görüntülerini etiketleme ve sürüm oluşturma önerileri

Kapsayıcı görüntüleri bir kapsayıcı kayıt defterine dağıtmaya ve sonra dağıttıktan sonra, görüntü etiketleme ve sürüm oluşturma stratejisine ihtiyacınız vardır. Bu makalede, her birinin kapsayıcı yaşam döngüsü sırasında uygun olduğu iki yaklaşım ele alınmaktadır:

* **Kararlı Etiketler** -Örneğin, *mycontainerımage: 1.0*gibi büyük veya küçük bir sürümü göstermek için yeniden kullandığınız Etiketler.
* **Benzersiz Etiketler** -bir kayıt defterine gönderdiğiniz her görüntü için, *mycontainerımage: abc123*gibi farklı bir etiket.

## <a name="stable-tags"></a>Kararlı Etiketler

**Öneri**: kapsayıcı derlemelerinizin **temel görüntülerini** sürdürmek için kararlı Etiketler kullanın. Bu Etiketler güncelleştirmeleri almaya devam ettiğinden ve üretim ortamlarında tutarsızlıklar getirebildiğinden, kararlı etiketlere sahip dağıtımlardan kaçının.

*Kararlı Etiketler* bir geliştirici veya bir yapı sistemi anlamına gelir. Bu, güncelleştirmeleri almaya devam eden belirli bir etiketi çekmeye devam edebilir. Kararlı değildir çünkü içeriğin dondurulmuş olması anlamına gelir. Bunun yerine, kararlı bir şekilde görüntünün bu sürümün amacı için kararlı olması gerektiğini belirtir. "Kararlı" kalmak için güvenlik düzeltme eklerini veya çerçeve güncelleştirmelerini uygulamak üzere hizmet vermiş olabilir.

### <a name="example"></a>Örnek

Bir çerçeve ekibi 1,0 sürümünü sevk eder. Bunlar, küçük güncelleştirmeler de dahil olmak üzere güncelleştirmeleri sevk edeceğiz. Belirli bir ana ve alt sürüm için kararlı etiketleri desteklemek üzere iki kararlı etiket kümesi vardır.

* `:1`: Ana sürüm için kararlı bir etiket. `1` "en yeni" veya "en son" 1. * sürümünü temsil eder.
* `:1.0`sürüm 1,0 ' e yönelik kararlı bir etiket, bir geliştiricinin güncelleştirme 1,0 güncelleştirmelerine bağlanması ve piyasaya sürülmediği zaman 1,1 'e geri alınana izin verir.

Takım Ayrıca, geçerli ana sürümün ne olduğuna bakılmaksızın, en son kararlı etiketi işaret eden `:latest` etiketini kullanır.

Temel görüntü güncelleştirmeleri kullanılabilir olduğunda veya Framework 'ün herhangi bir hizmet sürümü sürümünde, kararlı etiketlere sahip görüntüler, bu sürümün en güncel kararlı sürümünü temsil eden en yeni Özet olarak güncelleştirilir.

Bu durumda, hem birincil hem de ikincil etiketlere sürekli olarak bakım yapılıyor. Temel görüntü senaryosundan bu, görüntü sahibinin hizmet verilen görüntüleri sağlamasına izin verir.

## <a name="unique-tags"></a>Benzersiz Etiketler

**Öneri**: özellikle birden çok düğümde ölçeklenebilen bir ortamda **dağıtımlar**için benzersiz Etiketler kullanın. Büyük olasılıkla bileşenlerin tutarlı bir sürümünün bilinçli dağıtımlarını istersiniz. Kapsayıcınız yeniden başlatılırsa veya bir Orchestrator daha fazla örnek ölçeklendiyse, ana bilgisayarınız yanlışlıkla daha yeni bir sürüm çekmez ve diğer düğümlerle tutarsız.

Benzersiz etiketleme, bir kayıt defterine gönderilen her görüntünün benzersiz bir etiketi olduğu anlamına gelir. Etiketler yeniden kullanılmaz. Aşağıdakiler de dahil olmak üzere benzersiz Etiketler oluşturmak için izleyebileceğiniz çeşitli desenler vardır:

* **Tarih-saat damgası** -bu yaklaşım oldukça yaygındır çünkü görüntünün ne zaman oluşturulduğunu açıkça anlayabilirsiniz. Ancak, derleme sisteminize nasıl geri bağıntılı? Aynı zamanda tamamlanan derlemeyi bulmanız gerekiyor mu? Hangi saat dilimini kullanıyorsunuz? Tüm derleme sistemleriniz UTC 'ye göre ayarlanmış mı?
* **Git yürütmesi** – bu yaklaşım, temel görüntü güncelleştirmelerini desteklemeye başlamadan işe yarar. Bir temel görüntü güncelleştirmesi gerçekleşdiğinde, derleme sisteminiz önceki derleme ile aynı git yürütmesiyle birlikte çıkar. Ancak, temel görüntüde yeni içerik bulunur. Genel olarak, bir git yürütmesi *yarı*kararlı bir etiket sağlar.
* **Bildirim Özeti** -bir kapsayıcı kayıt defterine gönderilen her kapsayıcı görüntüsü, BENZERSIZ bir SHA-256 karması veya Özet tarafından tanımlanan bir bildirimle ilişkilendirilir. Benzersiz olsa da Özet, derleme ortamınızla bağıntılı, okunması ve bağıntısız bir biçimde yapılır.
* **Derleme kimliği** -Bu seçenek büyük olasılıkla artımlı olduğundan en iyi şekilde olabilir ve tüm yapıtları ve günlükleri bulmak için belirli bir yapıya geri ilişki kurmanıza olanak tanır. Bununla birlikte, bir bildirim Özeti gibi, insanların okuması zor olabilir.

  Kuruluşunuzun çeşitli yapı sistemleri varsa, yapı sistemi adı ile etiketin önüne önek bu seçenekte bir çeşittir: `<build-system>-<build-id>`. Örneğin, derlemeleri API ekibinin Jenkins derleme sisteminden ve Web ekibinin Azure Pipelines derleme sisteminden ayırt edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu makaledeki kavramların daha ayrıntılı bir açıklaması için, Docker [resimlerini etiketleme ve sürüm oluşturma Için en iyi uygulamalar](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/)bölümüne bakın.

Azure Container Registry 'nizin performansını ve düşük maliyetli kullanımını en üst düzeye çıkarmak için bkz. [Azure Container Registry Için en iyi uygulamalar](container-registry-best-practices.md).

<!-- IMAGES -->


<!-- LINKS - Internal -->

