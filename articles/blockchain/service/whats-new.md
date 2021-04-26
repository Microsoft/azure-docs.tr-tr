---
title: Yenilikler Sürüm notları-Azure blok zinciri hizmeti
description: En son sürüm notları, sürümler, bilinen sorunlar ve yaklaşan değişiklikler gibi Azure blok zinciri hizmeti ile nelerin yeni olduğunu öğrenin.
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 6a3113a2d28e704b188d701da13493ecd8263cab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94335035"
---
# <a name="whats-new-in-azure-blockchain-service"></a>Azure blok zinciri hizmetindeki yenilikler nelerdir?

> Bu URL 'YI kopyalayıp yapıştırarak güncelleştirmeler için bu sayfanın ne zaman geri alınacağını öğrenin: RSS Akış `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us` Okuyucusu [ ![ RSS akışı okuyucu simgesine](./media/whats-new/feed-icon-16x16.png)](/api/search/rss?locale=en-us&search=%2522Release%2bnotes%2b-%2bAzure%2bBlockchain%2bService%2522).

Azure blok zinciri hizmeti, iyileştirmeleri sürekli olarak alır. En son gelişmelerden haberdar olmak için, bu makalede hakkında bilgi verilmektedir:

- Yeni özellikler
- Sürüm yükseltmeleri
- Bilinen sorunlar

---

## <a name="june-2020"></a>Haziran 2020

### <a name="version-upgrades"></a>Sürüm yükseltmeleri

- 2.6.0 sürümüne çekirdek sürümü yükseltmesi. Sürüm 2.6.0 ile imzalı özel işlemler gönderebilirsiniz. Özel işlemler gönderme hakkında daha fazla bilgi için bkz. [çekirdek API 'si belgeleri](https://docs.goquorum.consensys.net/en/latest/Reference/APIs/ContractExtensionAPIs/#apis).
- Tessera sürümü 0.10.5 sürümüne yükseltilir.

### <a name="contract-size-and-transaction-size-increased-to-128-kb"></a>Sözleşme boyutu ve işlem boyutu 128 KB 'ye yükseldi

Tür: yapılandırma değişikliği

Daha büyük boyutlu akıllı sözleşmeleri dağıtabilmeniz için sözleşme boyutu (MaxCodeSize) 128 KB 'ye yükselmiştir. Ayrıca, işlem boyutu (txnSizeLimit) 128 KB 'ye yükselmiştir. Yapılandırma değişiklikleri, Haziran 19 2020 ' den sonra Azure blok zinciri hizmeti 'nde oluşturulan yeni yarışmatılıklar için geçerlidir.

### <a name="trietimeout-value-reduced"></a>TrieTimeout değeri azaltıldı

Tür: yapılandırma değişikliği

TrieTimeout değeri, bellek içi durumunun diske daha sık yazıldığı şekilde azaltılmıştır. Alt değer, düğüm kilitlenmesinin nadir durumunda bir düğümün daha hızlı kurtarılmasını sağlar.

## <a name="may-2020"></a>Mayıs 2020

### <a name="version-upgrades"></a>Sürüm yükseltmeleri

- Ubuntu sürümüne yükseltme 18,04
- 2.5.0 'e çekirdek sürümü yükseltme
- Tessera sürüm yükseltme 0.10.4

### <a name="azure-blockchain-service-supports-sending-rawprivate-transactions"></a>Azure blok zinciri hizmeti, Rawprıvate işlem gönderilmesini destekler

Tür: Özellik

Müşteriler, düğümdeki hesap dışında özel işlemleri imzalayabilir.

### <a name="two-phase-member-provisioning"></a>İki aşamalı üye sağlama

Tür: geliştirme

İki aşama, bir üyenin var olan bir konsorsiyumun üzerinde oluşturulduğu senaryoların iyileştirmenize yardımcı olur. Üye altyapısı ilk aşamada sağlanıyor. İkinci aşamada, üye blok zinciriyle eşitlenir. İki aşamalı sağlama zaman aşımları nedeniyle üye oluşturma hatasından kaçınmanıza yardımcı olur.

## <a name="known-issues"></a>Bilinen sorunlar

### <a name="ethestimategas-function-throws-exception-in-quorum-v260"></a>ETH. Estimategaz işlevi, çekirdek v 2.6.0 'de özel durum oluşturur

Çekirdek v 2.6.0 'de, ek *değer* parametresi sağlanması gerekmeden *ETH. estimategaz* işlevine yapılan çağrılar, bir *Yöntem işleyicisinin çökme* özel durumuna neden olur. Çekirdek ekibine bildirildi ve 2020 Temmuz 'dan sonuna bir onarım beklenmektedir. Bir çözüm kullanılabilir olana kadar aşağıdaki geçici çözümleri kullanabilirsiniz:

- Performansı etkileyebileceğinden, *ETH. Estimategaz* kullanmaktan kaçının. ETH. Estimategaz performans sorunları hakkında daha fazla bilgi için, bkz [.. estimategaz Işlevi çağırma performansı azaltır](#calling-ethestimategas-function-reduces-performance). Her işlem için bir gaz değeri ekleyin. Çoğu kitaplık, çekirdek v 2.6.0 'in kilitlenmesine neden olan bir gaz değeri sağlanmazsa, ETH. Estimategaz çağırır.
- *ETH. Estimategaz* çağrısı yapmanız gerekiyorsa, çekirdek ekip ek parametre *değerini* geçici bir çözüm olarak *0* olarak iletmeniz önerilir.

### <a name="mining-stops-if-fewer-than-four-validator-nodes"></a>Dört Doğrulayıcı düğümü daha az olursa araştırma durduruluyor

Üretim ağlarında en az dört Doğrulayıcı düğümü olmalıdır. Çekirdek, IBFT kilitlenme hata toleransını (3F + 1) karşılamak için en az dört Doğrulayıcı düğümü gerektirir. Dört Doğrulayıcı düğümü almak için en az iki Azure blok zinciri hizmeti *Standart* katman düğümünüz olmalıdır. Standart bir düğüm, iki Doğrulayıcı düğümüyle birlikte sağlanır.  

Azure blok zinciri hizmeti üzerindeki blok zinciri ağında dört Doğrulayıcı düğümü yoksa, araştırma ağ üzerinde durmayabilir. İşlenen bloklar üzerinde bir uyarı ayarlayarak, araştırma durdurulur. Sağlıklı bir ağda, işlenen blok beş dakika başına düğüm başına 60 blok olacaktır.

Risk azaltma olarak, Azure blok zinciri hizmeti ekibinin düğümü yeniden başlatması gerekir. Müşterilerin, düğümü yeniden başlatması için bir destek isteği açması gerekir. Azure blok zinciri hizmeti ekibi, araştırma sorunlarını otomatik olarak algılayıp düzeltme konusunda çalışmaktadır.

Üretim sınıfı dağıtımları için *Standart* katmanı kullanın. Geliştirme, test ve kavram kanıtı için *temel* katmanı kullanın. Üye oluşturulduktan sonra temel ve standart arasındaki fiyatlandırma katmanını değiştirmek desteklenmez.

### <a name="blockchain-data-manager-requires-standard-tier-node"></a>Blok zinciri Veri Yöneticisi standart katman düğümü gerektiriyor

Blok zinciri Veri Yöneticisi kullanıyorsanız *Standart* katmanı kullanın. *Temel* katmanda yalnızca 4 GB bellek bulunur. Bu nedenle, blok zinciri Veri Yöneticisi ve üzerinde çalışan diğer hizmetler için gereken kullanıma ölçeklenmez.

Geliştirme, test ve kavram kanıtı için *temel* katmanı kullanın. Üye oluşturulduktan sonra temel ve standart arasındaki fiyatlandırma katmanını değiştirmek desteklenmez.

### <a name="large-volume-of-unlock-account-calls-causes-geth-to-crash"></a>Kilit açma hesabı çağrılarının büyük hacmi geth 'nin kilitlenmesine neden oluyor

İşlem gönderilirken bir dizi kilit açma hesabı çağrısı, bir işlem düğümünde çökme oluşmasına neden olabilir. Sonuç olarak, işlemleri durdurmanız gerekir. Aksi takdirde, bekleyen işlem kuyruğu artar.

Geth bir dakikadan kısa bir sürede otomatik olarak yeniden başlatılır. Düğüme bağlı olarak, eşitleme uzun zaman alabilir. Azure blok zinciri hizmeti ekibi, eşitleme süresini azaltacak bir arşivleme özelliğini etkinleştirir.

Geth kilitlenmelerini belirlemek için, uygulama günlüklerindeki blok zinciri iletilerinde herhangi bir hata iletisi için günlüklere bakabilirsiniz. Ayrıca, beklemede olan işlemlerin artması sırasında işlenen blokların azalmasını da denetleyebilirsiniz.

Sorunu azaltmak için, hesabın kilidini açmak üzere imzasız işlemleri bir komutla göndermek yerine imzalı işlemler gönderin. Dışarıdan imzalanmış işlemler için hesabın kilidini açmanız gerekmez.

İmzasız işlemleri göndermek istiyorsanız, Unlock komutunda Time parametresi olarak 0 göndererek hesabın sınırsız süre boyunca kilidini açın. Tüm işlemler gönderildikten sonra hesabı geri alabilirsiniz.  

Azure blok zinciri hizmeti 'nin kullandığı geth parametreleri aşağıda verilmiştir. Bu parametreleri ayarlayamezsiniz.

- Istanbul blok dönemi: 5 saniye
- Kat gaz sınırı: 700000000
- CEIL gaz sınırı: 800000000

### <a name="large-volume-of-private-transactions-reduces-performance"></a>Büyük özel işlem hacmi performansı azaltır

Azure blok zinciri hizmeti temel katmanı ve özel işlemler kullanıyorsanız Tessera kilitlenebilir.

Blok zinciri uygulama günlüklerini inceleyerek ve iletiyi arayarak Tessera kilitlenmesinin algılanmasını sağlayabilirsiniz `Tessera crashed. Restarting Tessera...` .

Azure blok zinciri hizmeti, kilitlenme olduğunda Tessera 'yi yeniden başlatır. Yeniden başlatma yaklaşık bir dakika sürer.

Yüksek hacimli özel işlemler gönderiyorsanız *Standart* katmanı kullanın. Geliştirme, test ve kavram kanıtı için *temel* katmanı kullanın. Üye oluşturulduktan sonra temel ve standart arasındaki fiyatlandırma katmanını değiştirmek desteklenmez.

### <a name="calling-ethestimategas-function-reduces-performance"></a>ETH. Estimategaz işlevinin çağrılması performansı azaltır

*ETH. Estimategaz* işlevini birden çok kez çağırmak, saniye başına işlem azaltır. Her işlem gönderimi için *ETH. Estimategaz* işlevini kullanmayın. *ETH. Estimategaz* işlevi bellek açısından yoğun.

Mümkünse, işlem göndermek için bir klasik gaz değeri kullanın ve *ETH. Estimategaz* kullanımını en aza indirin.

### <a name="unbounded-loops-in-smart-contracts-reduces-performance"></a>Akıllı sözleşmelerdeki sınırsız döngüler performansı azaltır

Performansı azaltabilmeleri için akıllı sözleşmelerdeki sınırsız döngüden kaçının. Daha fazla bilgi için aşağıdaki kaynaklara bakın:

- [Sınırsız döngüden kaçının](https://blog.b9lab.com/getting-loopy-with-solidity-1d51794622ad )
- [Akıllı sözleşme güvenlik en iyi uygulamaları](https://github.com/ConsenSys/smart-contract-best-practices)
- [Çekirdek tarafından sunulan akıllı sözleşme yönergeleri](https://docs.goquorum.consensys.net/en/stable/Concepts/Security/Framework/DecentralizedApplication/SmartContractsSecurity/)
- [Solidity tarafından sunulan gaz sınırları ve döngüleriyle ilgili yönergeler](https://solidity.readthedocs.io/en/develop/security-considerations.html#gas-limit-and-loops)