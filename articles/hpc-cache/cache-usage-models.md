---
title: Azure HPC önbellek kullanım modelleri
description: Farklı önbellek kullanım modellerini ve bunların arasından salt okunurdur veya okuma/yazma önbelleği ayarlamak ve diğer önbelleğe alma ayarlarını denetlemek için nasıl tercih edileceğini açıklar
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/08/2021
ms.author: v-erkel
ms.openlocfilehash: 7e1b11fd15cca9b11fc627222318f08d31743336
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719196"
---
# <a name="understand-cache-usage-models"></a>Önbellek kullanım modellerini anlama

Önbellek kullanım modelleri, Azure HPC önbelleğinizi iş akışınızı hızlandırmak için dosyaları nasıl depolayabileceğinizi özelleştirmenize olanak tanır.

## <a name="basic-file-caching-concepts"></a>Temel dosya önbelleğe alma kavramları

Dosya önbelleğe alma, Azure HPC önbelleğinin istemci isteklerini nasıl hızlandıran. Bu temel uygulamaları kullanır:

* **Okuma önbelleği** -Azure HPC Cache, istemcilerin talep edilen depolama sisteminden aldığı dosyaların bir kopyasını tutar. İstemci bir dahaki sefer aynı dosyayı istediğinde HPC önbelleği, arka uç depolama sisteminden yeniden getirmek yerine sürümü önbellekte sağlayabilir.

* **Yazma önbelleği** -isteğe bağlı olarak, Azure HPC Cache, istemci makinelerden gönderilen değiştirilmiş dosyaların bir kopyasını depolayabilirler. Birden çok istemci, kısa bir süre içinde aynı dosyada değişiklik yapsa, önbellek her değişikliği arka uç depolama sistemine tek tek yazmak yerine önbellekteki tüm değişiklikleri toplayabilir.

  Değişiklik olmadan belirli bir süre geçtikten sonra önbellek, dosyayı uzun süreli depolama sistemine taşımıştır.

  Yazma önbelleği devre dışıysa, önbellek değiştirilen dosyayı depolamaz ve hemen arka uç depolama sistemine yazar.

* **Geri yazma gecikmesi** -yazma önbelleği açık olan bir önbellek için, geri yazma gecikmesi önbelleğin dosyayı arka uç depolama sistemine kopyalamadan önce ek dosya değişiklikleri için bekleyeceği süre miktarıdır.

* **Arka uç doğrulaması** -arka uç doğrulama ayarı, önbelleğin arka uç depolama sistemindeki uzak sürümle bir dosyanın yerel kopyasını ne sıklıkta karşılaştırdığını belirler. Arka uç kopyası önbelleğe alınmış kopyadan daha yeniyse, önbellek uzak kopyayı getirir ve gelecekteki istekler için depolar.

  Arka uç doğrulama ayarı, önbelleğin, dosyalarını uzak depolamadaki kaynak dosyalarla *otomatik olarak* karşılaştırdığı zamanı gösterir. Ancak, bir readdirplus isteği içeren bir dizin işlemi gerçekleştirerek Azure HPC önbelleğini dosyaları karşılaştırmaya zorlayabilirsiniz. Readdirplus, dizin meta verileri döndüren standart bir NFS API 'sidir (genişletilmiş okuma olarak da bilinir). Bu, önbelleğin dosyaları karşılaştırmasına ve güncelleştirmesine neden olur.

Azure HPC önbelleğinde yerleşik kullanım modellerinin bu ayarlar için farklı değerleri vardır ve bu sayede durumunuz için en iyi birleşimi seçebilmenizi sağlayabilirsiniz.

## <a name="choose-the-right-usage-model-for-your-workflow"></a>İş akışınız için doğru kullanım modelini seçin

Kullandığınız her NFS-protokol depolama hedefi için bir kullanım modeli seçmeniz gerekir. Azure Blob depolama hedefleri, özelleştirilebilecek yerleşik bir kullanım modeline sahiptir.

HPC önbelleği kullanım modelleri, eski verileri alma riskiyle hızlı yanıt dengelemeye olanak tanır. Dosya okuma hızını iyileştirmek isterseniz, önbellekteki dosyaların arka uç dosyalarına karşı kontrol edilip edilmeyeceğini önemsemeyebilirsiniz. Diğer taraftan, her zaman uzak depolama ile dosyalarınızın güncel olduğundan emin olmak istiyorsanız, sık denetleyen bir model seçin.

Kullanım modeli seçenekleri şunlardır:

* **Ağır, seyrek erişimli yazmaları okuma** -statik veya nadiren değiştirilen dosyalara okuma erişimini hızlandırmak istiyorsanız bu seçeneği kullanın.

  Bu seçenek, istemci okumalarını önbelleğe alır ancak önbelleğe alma işlemleri yapmaz. Yazma işlemlerini hemen arka uç depolamaya geçirir.
  
  Önbellekte depolanan dosyalar, NFS depolama birimindeki dosyalarla otomatik olarak karşılaştırılmaz. (Bunları el ile karşılaştırmayı öğrenmek için yukarıdaki arka uç doğrulamasının açıklamasını okuyun.)

  Bir dosyanın doğrudan depolama sisteminde önbellekte yazılmadan değiştirilebilmesi için bir risk varsa bu seçeneği kullanmayın. Bu durumda, dosyanın önbelleğe alınmış sürümü arka uç dosyasıyla eşitlenmemiş olur.

* **%15 ' ten fazla yazma** -Bu seçenek hem okuma hem de yazma performansını hızlandırır. Bu seçeneği kullanırken, tüm istemcilerin arka uç depolamayı doğrudan bağlamak yerine Azure HPC Cache aracılığıyla dosyalara erişmesi gerekir. Önbelleğe alınan dosyalar henüz arka uca kopyalanmamış son değişikliklere sahip olacaktır.

  Bu kullanım modelinde, önbellekteki dosyalar yalnızca her sekiz saatte bir arka uç depolamada bulunan dosyalara karşı denetlenir. Dosyanın önbelleğe alınan sürümünün daha güncel olduğu varsayılır. Önbellekte değiştirilen bir dosya, önbellekte 20 dakika geçtikten sonra arka uç depolama sistemine yazılır<!-- an hour --> ek değişiklik yok.

* **İstemciler, önbelleği ATLAYARAK NFS hedefine yazar** -iş akışlarınızdan herhangi bir istemci, önce önbelleğe yazmadan veya veri tutarlılığını iyileştirmek istiyorsanız bu seçeneği belirleyin. İstemcilerin istediği dosyalar önbelleğe alınır (okur), ancak istemciden (yazma) bu dosyalardaki değişiklikler önbelleğe alınmaz. Bunlar doğrudan arka uç depolama sistemine geçirilir.

  Bu kullanım modeliyle, önbellekteki dosyalar genellikle güncelleştirmeler için arka uç sürümlerine karşı, her 30 saniyede denetlenir. Bu doğrulama, verilerin tutarlılığın korunmasında önbelleğin dışında değiştirilmesine izin verir.

  > [!TIP]
  > Bu ilk üç temel kullanım modeli, Azure HPC Cache iş akışlarının çoğunu idare etmek için kullanılabilir. Sonraki seçenekler daha az yaygın senaryolar içindir.

* **%15 ' ten fazla yazma, her 30 saniyede** bir ve 15 ' ten fazla yazma için yedekleme sunucusu denetleniyor **, her 60 saniyede bir yedekleme sunucusu denetleniyor** ; bu seçenekler, hem okuma hem de yazma işlemlerini hızlandırmak istediğiniz iş akışları için tasarlanmıştır, ancak başka bir kullanıcının doğrudan arka uç depolama sistemine yazacağı bir şansınız vardır. Örneğin, birden çok istemci kümesi farklı konumlardan aynı dosyalar üzerinde çalışıyorsa, bu kullanım modelleri kaynaktaki eski içerik için düşük toleranslı olan hızlı dosya erişimi gereksinimini dengelemek açısından anlamlı hale gelebilir.

* **%15 ' ten fazla yazma, her 30 saniyede bir sunucuya geri yazma** -Bu seçenek, birden fazla istemcinin aynı dosyaları etkin bir şekilde değiştiren senaryoya veya bazı istemcilerde önbelleği bağlamak yerine doğrudan uç depolamaya erişebildiğine yönelik olarak tasarlanmıştır.

  Sık kullanılan arka uç yazma işlemleri önbellek performansını etkiler. bu nedenle, dosya çakışmasıyla ilgili düşük bir risk varsa, örneğin farklı istemcilerin aynı dosya kümesinin farklı alanlarında çalıştığını biliyorsanız, **%15 ' ten fazla yazma** kullanım modelini kullanmayı düşünmelisiniz.

* **Yoğun okuma, yedekleme sunucusunu 3 saatte bir denetleme** -Bu seçenek istemci tarafında hızlı okumaları önceliklendirir, ancak Ayrıca, **yoğun okuma, seyrek kullanılan yazma** kullanım modelinden farklı olarak önbelleğe alınan dosyaları arka uç depolama sisteminden de yeniler.

Bu tablo, kullanım modeli farklarını özetler:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

Azure HPC Cache iş akışınız için en iyi kullanım modeliyle ilgili sorularınız varsa, Azure temsilcinizle konuşun veya yardım için bir destek isteği açın.

## <a name="know-when-to-remount-clients-for-nlm"></a>İstemci tarafından NLM için ne zaman yeniden bağlama yapmanız gerektiğini öğrenin

Bazı durumlarda, bir depolama hedefinin kullanım modelini değiştirirseniz istemcileri yeniden bağlama yapmanız gerekebilir. Bu, farklı kullanım modellerinin ağ kilitleme Yöneticisi (NLM) isteklerini işleme biçimi nedeniyle gereklidir.

HPC önbelleği istemciler ve arka uç depolama sistemi arasında bulunur. Genellikle önbellek, NLM isteklerini arka uç depolama sistemine geçirir, ancak bazı durumlarda, önbellek, NLM isteğini onaylar ve istemciye bir değer döndürür. Azure HPC önbelleğinde bu, yalnızca kullanım modelini **okuma ağır, seyrek kullanılan yazma** (veya standart bir BLOB depolama hedefi olan veya yapılandırılabilir kullanım modelleri olmayan) kullandığınızda oluşur.

**Yoğun okuma, seyrek kullanılan yazma** kullanım modeli ve farklı kullanım modeli arasında değişiklik yaparsanız dosya çakışmasıyla oluşan küçük bir risk vardır. Geçerli NLM durumunu önbellekten depolama sistemine aktarmanın bir yolu yoktur ya da tam tersi de geçerlidir. Bu nedenle, istemcinin kilit durumu yanlış.

Yeni kilit yöneticisiyle doğru bir NLM durumuna sahip olduklarından emin olmak için istemcileri yeniden bağlayın.

İstemcileriniz kullanım modeli veya arka uç depolama alanını desteklemediği zaman bir NLM isteği gönderiyorlarsa bir hata alırlar.

### <a name="disable-nlm-at-client-mount-time"></a>İstemci bağlama zamanında NLM 'yi devre dışı bırak

İstemci sistemlerinizin NLM isteklerini göndermesini isteyip istemeyeceğini bilmeniz her zaman kolay değildir.

İstemciler, komutta seçeneğini kullanarak kümeyi bağladığınızda, NLM 'yi devre dışı bırakabilirsiniz ``-o nolock`` ``mount`` .

Seçeneğinin tam davranışı, ``nolock`` istemci işletim sistemine bağlıdır, bu nedenle istemci işletim sistemine yönelik bağlama belgelerini (Man 5 NFS) denetleyin. Çoğu durumda, kilidi yerel olarak istemciye taşımıştı. Uygulamanız dosyaları birden çok istemcide kilitlerseniz dikkatli olun.

> [!NOTE]
> ADLS-NFS, NLM 'yi desteklemez. Bir ADLS-NFS depolama hedefi kullanırken, yukarıdaki bağlama seçeneği ile NLM 'yi devre dışı bırakmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

* Azure HPC önbelleğinize [depolama hedefleri ekleme](hpc-cache-add-storage.md)
