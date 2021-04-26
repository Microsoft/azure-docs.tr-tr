---
title: Azure Key Vault azaltma yönergeleri
description: Key Vault azaltma, kaynakların aşırı kullanımını önleyen eşzamanlı çağrıların sayısını sınırlar.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 7a215b53f673a7414f1b3662f519de5c26faaa9d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749550"
---
# <a name="azure-key-vault-throttling-guidance"></a>Azure Key Vault azaltma yönergeleri

Azaltma, kaynakların aşırı kullanımını engellemek için Azure hizmetine yapılan eşzamanlı çağrıların sayısını sınırlayan bir işlemdir. Azure Key Vault (AKV), yüksek hacimli istekleri işlemek için tasarlanmıştır. İstek sayısı çok fazla olursa, istemcinizin isteklerini azaltarak, AKV hizmetinin en iyi performans ve güvenilirliğini sürdürmenize yardımcı olur.

Azaltma limitleri senaryoya göre farklılık gösterir. Örneğin, büyük bir yazma hacmi gerçekleştiriyorsanız, yalnızca okuma işlemi gerçekleştirmekten daha yüksektir.

## <a name="how-does-key-vault-handle-its-limits"></a>Key Vault sınırlarını nasıl işler?

Key Vault hizmet limitleri, kaynakların kötüye kullanımını engeller ve tüm Key Vault istemcileri için hizmet kalitesini güvence altına aldığınızdan emin olun. Bir hizmet eşiği aşıldığında, bu istemciden bir süre için diğer istekleri sınırlar Key Vault, HTTP durum kodu 429 (çok fazla istek) döndürür ve istek başarısız olur. 429 döndüren başarısız istekler, Key Vault tarafından izlenen kısıtlama sınırlarına doğru sayılmaz. 

Key Vault ilk olarak, dağıtım zamanında sırlarınızı depolamak ve almak için kullanılmak üzere tasarlanmıştır.  Dünya gelişmiştir ve gizli dizileri depolamak ve almak için çalışma zamanında kullanılıyor Key Vault ve genellikle uygulama ve hizmetler bir veritabanı gibi Key Vault kullanmak ister.  Geçerli sınırlar yüksek verimlilik hızlarını desteklemez.

Key Vault ilk olarak [Azure Key Vault hizmet sınırlarında](service-limits.md)belirtilen limitlerle oluşturulmuştur.  Key Vault, koyma ücretleri üzerinden en üst düzeye çıkarmak için, aktarım hızını en üst düzeye çıkarmak için önerilen bazı yönergeler/en iyi uygulamalar şunlardır
1. Azaltma yapıldığından emin olun.  İstemci, 429 ' un üstel geri dönüş ilkelerini kabul etmelidir ve aşağıdaki kılavuza göre yeniden denemeler yapmakta olduğunuzdan emin olmalıdır.
1. Key Vault trafiğinizi birden çok kasa ve farklı bölgeler arasında bölün.   Her güvenlik/kullanılabilirlik etki alanı için ayrı bir kasa kullanın.   Her biri iki bölgede beş uygulamanız varsa, her biri uygulama ve bölgeye özgü gizli dizileri içeren 10 kasalar öneririz.  Tüm işlem türleri için abonelik genelinde sınır, tek bir Anahtar Kasası sınırının beş katından fazla olur. Örneğin, HSM-abonelik başına diğer işlemler, abonelik başına 10 saniye içinde 5.000 işlem ile sınırlıdır. Ayrıca, RPS 'yi doğrudan Anahtar Kasası 'na düşürmek ve/veya veri bloğu tabanlı trafiği işlemek için hizmet veya uygulamanızdaki gizli anahtarı önbelleğe almayı düşünün.  Ayrıca, gecikme süresini en aza indirmek ve farklı bir abonelik/kasa kullanmak için trafiğinizi farklı bölgeler arasında ayırabilirsiniz.  Tek bir Azure bölgesindeki Key Vault hizmetine daha fazla abonelik sınırı göndermeyin.
1. Bellekte Azure Key Vault aldığınız gizli dizileri önbelleğe alın ve mümkün olan her durumda bellekten yeniden kullanın.  Yalnızca önbelleğe alınmış kopya çalışmayı durdurduğu zaman Azure Key Vault yeniden oku (örneğin, kaynakta döndürülmüştür). 
1. Key Vault kendi hizmet sırlarınız için tasarlanmıştır.   Müşterilerinizin gizli dizilerini depoluyorsanız (özellikle yüksek işlem hacmi olan anahtar depolama senaryoları için), anahtarları bir veritabanına veya depolama hesabına şifrelemeye koymak ve Azure Key Vault yalnızca ana anahtarı depolamak için göz önünde bulundurun.
1. Genel anahtar işlemlerini şifreleme, sarın ve doğrulama, azaltma riskini azalmayan, ancak aynı zamanda güvenilirliği artıran (ortak anahtar malzemesini doğru şekilde önbelleğe aldığınız sürece) Key Vault erişim olmadan gerçekleştirilebilir.
1. Bir hizmet için kimlik bilgilerini depolamak üzere Key Vault kullanıyorsanız, bu hizmetin doğrudan kimlik doğrulamak için Azure AD kimlik doğrulamasını destekleyip desteklemediğini denetleyin. Bu, Key Vault yükünü azaltır, güvenilirliği geliştirir ve Key Vault artık Azure AD belirtecini kullanabilmesi için kodunuzu basitleştirir.  Birçok hizmet Azure AD kimlik doğrulaması kullanılarak taşınmıştır.  [Azure kaynakları için yönetilen kimlikleri destekleyen hizmetler](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)kısmındaki geçerli listeye bakın.
1. Geçerli RPS limitlerinin altında kalmak için yük/dağıtımınızı daha uzun bir süre boyunca kademelendirme değerlendirin.
1. Uygulamanız aynı gizli dizi (ler) i okuması gereken birden çok düğüm içeriyorsa, bir varlığın Key Vault gizli dizi ve tüm düğümlere giden fanları okuduğu bir fan çıkış deseninin kullanılmasını düşünün.   Alınan gizli dizileri yalnızca bellekte önbelleğe al.
Yukarıdakilerden hala gereksinimlerinizi karşılamadığını fark ediyorsanız, lütfen aşağıdaki tabloyu doldurun ve hangi ek kapasitenin eklenebileceklerini öğrenmek için bizimle iletişim kurun (yalnızca tanım amaçları için aşağıda verilmiştir).

| Kasa adı | Kasa bölgesi | Nesne türü (gizli, anahtar veya sertifika) | İşlemler * | Anahtar türü | Anahtar uzunluğu veya eğrisi | HSM anahtarı?| Sabit durum RPS gerekli | Gerekli en yüksek RPS |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Anahtar | İşaret | EC | P-256 | No | 200 | 1000 |

\* Olası değerlerin tam listesi için bkz. [Azure Key Vault işlemler](/rest/api/keyvault/key-operations).

Ek kapasite onaylanırsa, kapasitenin sonucu arttıkça lütfen aşağıdakileri unutmayın:
1. Veri tutarlılığı modeli değişiklikleri. Kasa, ek aktarım hızı kapasitesine izin vertikten sonra, Key Vault Service veri tutarlılığı garantisi (temeldeki Azure depolama hizmeti devam edemediğinden daha yüksek hacimli RPS 'yi karşılamak için gereklidir).  Bir Nutshell 'de:
  1. **Listeye izin verme olmadan**: Key Vault hizmeti bir yazma işleminin sonuçlarını yansıtır (örn. SecretSet, CreateKey) sonraki çağrılarda hemen (ör. SecretGet, KeySign).
  1. **İzin verilenler listesi ile**: Key Vault hizmeti bir yazma işleminin sonuçlarını yansıtır (örn. SecretSet, CreateKey) sonraki çağrılarda 60 saniye içinde (örn. SecretGet, KeySign).
1. İstemci kodu, 429 yeniden deneme için geri dönüş ilkesini kabul etmelidir. Key Vault hizmetini çağıran istemci kodu, 429 yanıt kodu aldığında istekleri Key Vault anında yeniden denememelidir.  Burada yayımlanan Azure Key Vault daraltma Kılavuzu, 429 http yanıt kodu alınırken üstel geri alma uygulanmasını önerir.

Daha yüksek kısıtlama limitleri için geçerli bir iş örneğine sahipseniz lütfen bizimle iletişime geçin.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Hizmet sınırlarına yanıt olarak uygulamanızı kısıtlama

Aşağıda, hizmetiniz kısıtlandığınızda uygulamanız gereken **en iyi yöntemler** verilmiştir:
- İstek başına işlem sayısını azaltın.
- İsteklerin sıklığını azaltın.
- Anında yeniden denemeler yapmaktan kaçının. 
    - Tüm istekler kullanım sınırlarınıza göre tahakkuk eder.

Uygulamanızın hata işlemesini uyguladığınızda, istemci tarafı azaltma gereksinimini algılamak için 429 HTTP hata kodunu kullanın. İstek bir HTTP 429 hata koduyla yeniden başarısız olursa, yine de bir Azure hizmet sınırı ile karşılaşmaya devam edersiniz. Önerilen istemci tarafı azaltma yöntemini kullanmaya devam edin, isteği başarılı olana kadar yeniden denemeye devam edin.

Üstel geri alma uygulayan kod aşağıda gösterilmiştir. 
```
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
    var client = new SecretClient(new Uri("https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


Bu kodun bir istemci C# uygulamasında kullanılması basittir. 

### <a name="recommended-client-side-throttling-method"></a>Önerilen istemci tarafı azaltma yöntemi

HTTP hata kodu 429 ' de, bir üstel geri alma yaklaşımı kullanarak istemcinizi azaltmayı başlatın:

1. 1 saniye bekleyin, yeniden deneme isteği
2. Hala kısıtlanıyor 2 saniye bekle, isteği yeniden dene
3. Hala kısıtlanıyor 4 saniye bekle, yeniden deneme isteği
4. Hala kısıtlanıyor 8 saniye bekle, yeniden deneme isteği
5. Hala kısıtlanıyor, 16 saniye bekle, isteği yeniden dene

Bu noktada, HTTP 429 yanıt kodları alma kullanmamalısınız.

## <a name="see-also"></a>Ayrıca bkz.

Microsoft Bulut azaltma daha derin bir yönü için bkz. [daraltma kriteri](/azure/architecture/patterns/throttling).
