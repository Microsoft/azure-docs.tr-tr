---
title: Resim etiketi en iyi uygulamaları
description: Resimleri bir Azure Container Registry 'ye gönderirken ve resimleri çekirken Docker kapsayıcı görüntülerini etiketleme ve sürüm oluşturma için en iyi uygulamalar
author: stevelasker
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: 9dfd29f2265e599f3cc7c412b81c3b7b93af40db
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109722"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Kapsayıcı görüntülerini etiketleme ve sürüm oluşturma önerileri

Kapsayıcı görüntülerini bir kapsayıcı kayıt defterine gönderirken ve sonra dağıttıktan sonra, görüntü etiketleme ve sürüm oluşturma stratejisine ihtiyacınız vardır. Bu makalede, her birinin kapsayıcı yaşam döngüsü sırasında uygun olduğu iki yaklaşım ele alınmaktadır:

* **Kararlı Etiketler** -Örneğin, *mycontainerımage: 1.0* gibi büyük veya küçük bir sürümü göstermek için yeniden kullandığınız Etiketler.
* **Benzersiz Etiketler** -bir kayıt defterine gönderdiğiniz her görüntü için, *mycontainerımage: abc123* gibi farklı bir etiket.

## <a name="stable-tags"></a>Kararlı Etiketler

**Öneri**: kapsayıcı derlemelerinizin **temel görüntülerini** sürdürmek için kararlı Etiketler kullanın. Bu Etiketler güncelleştirmeleri almaya devam ettiğinden ve üretim ortamlarında tutarsızlıklar getirebildiğinden, kararlı etiketlere sahip dağıtımlardan kaçının.

*Kararlı Etiketler* bir geliştirici veya bir yapı sistemi anlamına gelir. Bu, güncelleştirmeleri almaya devam eden belirli bir etiketi çekmeye devam edebilir. Kararlı değildir çünkü içeriğin dondurulmuş olması anlamına gelir. Bunun yerine, kararlı bir şekilde görüntünün bu sürümün amacı için kararlı olması gerektiğini belirtir. "Kararlı" kalmak için güvenlik düzeltme eklerini veya çerçeve güncelleştirmelerini uygulamak üzere hizmet vermiş olabilir.

### <a name="example"></a>Örnek

Bir çerçeve ekibi 1,0 sürümünü sevk eder. Bunlar, küçük güncelleştirmeler de dahil olmak üzere güncelleştirmeleri sevk edeceğiz. Belirli bir ana ve alt sürüm için kararlı etiketleri desteklemek üzere iki kararlı etiket kümesi vardır.

* `:1` – Ana sürüm için kararlı bir etiket. `1` "en yeni" veya "en son" 1. * sürümünü temsil eder.
* `:1.0`-sürüm 1,0 ' e yönelik kararlı bir etiket, bir geliştiricinin güncelleştirme 1,0 güncelleştirmelerine bağlamasını sağlar ve yayımlandığınızda 1,1 'e geri alınamaz.

Takım Ayrıca, `:latest` geçerli ana sürümün ne olduğuna bakılmaksızın, en son kararlı etikete işaret eden etiketini kullanır.

Temel görüntü güncelleştirmeleri kullanılabilir olduğunda veya Framework 'ün herhangi bir hizmet sürümü sürümünde, kararlı etiketlere sahip görüntüler, bu sürümün en güncel kararlı sürümünü temsil eden en yeni Özet olarak güncelleştirilir.

Bu durumda, hem birincil hem de ikincil etiketlere sürekli olarak bakım yapılıyor. Temel görüntü senaryosundan bu, görüntü sahibinin hizmet verilen görüntüleri sağlamasına izin verir.

### <a name="delete-untagged-manifests"></a>Etiketlenmemiş bildirimleri Sil

Kararlı bir etiketi olan bir görüntü güncelleştirilirse, önceden etiketlenmiş görüntü etiketsiz olur ve bu, yalnız bırakılmış bir görüntüye neden olur. Önceki görüntünün bildirimi ve benzersiz katman verileri kayıt defterinde kalır. Kayıt defteri boyutunuzu korumak için, kararlı görüntü güncelleştirmelerinden kaynaklanan etiketlenmemiş bildirimleri düzenli aralıklarla silebilirsiniz. Örneğin, belirli bir süreden daha eski olan etiketlenmemiş bildirimleri [otomatik olarak temizle](container-registry-auto-purge.md) veya etiketlenmemiş bildirimler için bir [bekletme ilkesi](container-registry-retention-policy.md) ayarlayın.

## <a name="unique-tags"></a>Benzersiz Etiketler

**Öneri**: özellikle birden çok düğümde ölçeklenebilen bir ortamda **dağıtımlar** için benzersiz Etiketler kullanın. Büyük olasılıkla bileşenlerin tutarlı bir sürümünün bilinçli dağıtımlarını istersiniz. Kapsayıcınız yeniden başlatılırsa veya bir Orchestrator daha fazla örnek ölçeklendiyse, ana bilgisayarınız yanlışlıkla daha yeni bir sürüm çekmez ve diğer düğümlerle tutarsız.

Benzersiz etiketleme, bir kayıt defterine gönderilen her görüntünün benzersiz bir etiketi olduğu anlamına gelir. Etiketler yeniden kullanılmaz. Aşağıdakiler de dahil olmak üzere benzersiz Etiketler oluşturmak için izleyebileceğiniz çeşitli desenler vardır:

* **Tarih-saat damgası** -bu yaklaşım oldukça yaygındır çünkü görüntünün ne zaman oluşturulduğunu açıkça anlayabilirsiniz. Ancak, derleme sisteminize nasıl geri bağıntılı? Aynı zamanda tamamlanan derlemeyi bulmanız gerekiyor mu? Hangi saat dilimini kullanıyorsunuz? Tüm derleme sistemleriniz UTC 'ye göre ayarlanmış mı?
* **Git yürütmesi**  – bu yaklaşım, temel görüntü güncelleştirmelerini desteklemeye başlamadan işe yarar. Bir temel görüntü güncelleştirmesi gerçekleşdiğinde, derleme sisteminiz önceki derleme ile aynı git yürütmesiyle birlikte çıkar. Ancak, temel görüntüde yeni içerik bulunur. Genel olarak, bir git yürütmesi *yarı* kararlı bir etiket sağlar.
* **Bildirim Özeti** -bir kapsayıcı kayıt defterine gönderilen her kapsayıcı görüntüsü, BENZERSIZ bir SHA-256 karması veya Özet tarafından tanımlanan bir bildirimle ilişkilendirilir. Benzersiz olsa da Özet, derleme ortamınızla bağıntılı, okunması ve bağıntısız bir biçimde yapılır.
* **Derleme kimliği** -Bu seçenek büyük olasılıkla artımlı olduğundan en iyi şekilde olabilir ve tüm yapıtları ve günlükleri bulmak için belirli bir yapıya geri ilişki kurmanıza olanak tanır. Bununla birlikte, bir bildirim Özeti gibi, insanların okuması zor olabilir.

  Kuruluşunuzun çeşitli yapı sistemleri varsa, bu seçenek üzerinde yapı sistemi adı ile etiketin önüne önek olarak eklenir: `<build-system>-<build-id>` . Örneğin, derlemeleri API ekibinin Jenkins derleme sisteminden ve Web ekibinin Azure Pipelines derleme sisteminden ayırt edebilirsiniz.

### <a name="lock-deployed-image-tags"></a>Dağıtılan görüntü etiketlerini kilitle

En iyi uygulama olarak, tüm dağıtılan görüntü etiketlerini, özniteliğini olarak ayarlayarak [kilitlemenizi](container-registry-image-lock.md) öneririz `write-enabled` `false` . Bu uygulama, yanlışlıkla bir görüntüyü kayıt defterinden kaldırmanızı ve dağıtımlarınızı kesintiye engel olmasını önler. Yayın işlem hattınızda kilitleme adımını dahil edebilirsiniz.

Dağıtılan bir görüntünün kilitlenmesi, kayıt defterinizi sürdürmek için Azure Container Registry özellikleri kullanarak Kayıt defterinizden diğer, dağıtılmamış görüntüleri kaldırmanıza olanak tanır. Örneğin, etiketlenmemiş bildirimleri veya belirtilen süreden daha eski görüntüleri [otomatik olarak temizle](container-registry-auto-purge.md) veya etiketlenmemiş bildirimler için bir [bekletme ilkesi](container-registry-retention-policy.md) ayarlayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu makaledeki kavramların daha ayrıntılı bir açıklaması için, Docker [resimlerini etiketleme ve sürüm oluşturma Için en iyi uygulamalar](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/)bölümüne bakın.

Azure Container Registry 'nizin performansını ve düşük maliyetli kullanımını en üst düzeye çıkarmak için bkz. [Azure Container Registry Için en iyi uygulamalar](container-registry-best-practices.md).

<!-- IMAGES -->


<!-- LINKS - Internal -->

