---
title: .NET 4.7.2 Upgrade nedeniyle Azure Data Lake Analytics U-SQL iş hatalarıyla ilgili sorunları giderme
description: .NET 4.7.2 yükseltmesi nedeniyle U-SQL iş hatalarıyla ilgili sorunları giderin.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/11/2019
ms.openlocfilehash: 851a405e5143ea5bb3a26de76f713914aa4bb569
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648525"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure Data Lake Analytics .NET Framework v 4.7.2 sürümüne yükseltiyor

Azure Data Lake Analytics varsayılan çalışma zamanı .NET Framework v 4.5.2 'den .NET Framework v 4.7.2 'e yükseltiyor. Bu değişiklik, U-SQL kodunuz özel derlemeler kullanıyorsa ve bu özel derlemeler .NET kitaplıklarını kullanıyorsa, küçük değişikliklere karşı küçük bir risk sunar.

Bu .NET Framework 4.5.2 sürümüne yükseltme, bir U-SQL çalışma zamanına (varsayılan çalışma zamanı) dağıtılan .NET Framework artık her zaman 4.7.2 olacağı anlamına gelir. .NET Framework sürümleri için yan yana bir seçenek yoktur.

.NET 4.7.2 'e yükseltme işlemi tamamlandıktan sonra, sistemin yönetilen kodu sürüm 4.7.2 olarak çalıştırılır. U-SQL özel derlemeler gibi kullanıcı tarafından sunulan kitaplıklar, derlemenin oluşturulduğu sürüm için uygun olan geriye dönük uyumlu modda çalışacaktır bekleniyor.

- Derleme dll 'larınız 4.5.2 sürümü için oluşturulduysa, dağıtılan Framework bunları 4.5.2 kitaplıkları olarak değerlendirir, bu da (birkaç özel durum ile) 4.5.2 semantiğini sağlar.
- Artık .NET Framework 4.7.2 ' i hedefliyorsanız, sürüm 4.7.2 özelliklerini kullanan U-SQL özel derlemelerini kullanabilirsiniz.

Bu .NET 4.7.2 yükseltmesi nedeniyle, .NET özel derlemeleri kullanan U-SQL işleriniz üzerinde önemli değişiklikler yapmanızı sağlar. Aşağıdaki yordamı kullanarak geriye doğru uyumluluk sorunlarını kontrol etmeniz önerilir.

## <a name="how-to-check-for-backwards-compatibility-issues"></a>Geriye dönük uyumluluk sorunlarını denetleme

U-SQL özel derlemelerinizdeki .NET kodunuzda .NET uyumluluk denetimlerini çalıştırarak geriye doğru uyumluluk sorunları olup olmadığını denetleyin.

> [!Note]
> Araç gerçek son değişiklikleri algılamaz. yalnızca (bazı girişler için) sorunlara neden olabilecek bilinen .NET API 'Lerini tanımlar. Bir sorunla karşılaşırsanız, kodunuz yine de iyi olabilir, ancak daha fazla ayrıntı denetlemeniz gerekir.

1. Geriye doğru uyumluluk denetleyicisi 'ni .NET DLL 'lerinizin üzerinde çalıştırın
   1. [.Net taşınabilirlik Çözümleyicisi Visual Studio uzantısı](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) 'Nda Visual Studio uzantısı 'nı kullanma
   1. [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport)'tan tek başına aracı indiriliyor ve kullanılıyor. Tek başına aracı çalıştırmaya ilişkin yönergeler [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md) ile ilgili değişiklikler
   1. 4\.7.2 için. Uyumluluk Read ısrehedefleme = = true, son değişikliklerdir.
2. Araç, kodunuzun olası geri uyumsuzlukların herhangi biri tarafından etkilenip etkilenmeyeceğini gösteriyorsa (bazı yaygın uyumsuzluklar örnekleri aşağıda listelenmiştir), aşağıdakileri yaparak daha fazla kontrol edebilirsiniz
   1. Kodunuzun çözümlenmesi ve kodunuzun etkilenen API 'lere değer geçirirse tanımlanması
   1. Çalışma zamanı denetimi gerçekleştirin. Çalışma zamanı dağıtımı ADLA içinde yan yana yapılmaz. Bir temsilci veri kümesine karşı bir yerel .NET Framework 4.7.2 ile, VisualStudio 'in yerel çalıştırmasını kullanarak yükseltmeden önce bir çalışma zamanı denetimi yapabilirsiniz.
3. Gerçekten geriye dönük uyumsuzlukta etkilenirse, düzeltmek için gerekli adımları gerçekleştirin (örneğin, verilerinizi veya kod mantığınızı düzeltme).

Çoğu durumda, geriye dönük uyumsuzluğun etkilenmemesi gerekir.

## <a name="timeline"></a>Zaman çizelgesi

Yeni çalışma zamanının dağıtımını, [sorun giderme sorunlarını gidermeye](runtime-troubleshoot.md)ve önceki başarılı bir işe bakarak denetleyebilirsiniz.

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>Kodumu zaman içinde gözden geçirmem için ne yapmalıyım?

İşinizi, .NET Framework yan yana yeteneklerin olmaması nedeniyle, ancak yalnızca 4.5.2 uyumluluk modunda çalışacak olan eski çalışma zamanı sürümüne (hedefleme 4.5.2) karşı gönderebilirsiniz. Bu davranış nedeniyle geriye doğru uyumluluk sorunlarından bazılarını yaşamaya devam edebilirsiniz.

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>Karşılaşabileceğiniz en yaygın geriye doğru uyumluluk sorunları nelerdir?

Denetleyicinin tanımlanmasının olası en yaygın geri uyumsuzluklarını şunlardır: (Bu listeyi kendi iç ADLA işlerinde denetleyiciyi çalıştırarak oluşturduk), hangi kitaplıkların etkilendiğini (yani, kitaplıkları yalnızca dolaylı olarak çağırabileceğinizi unutmayın, bu nedenle işlerin etkilenip etkilenmediğinizi denetlemek için gerekli eylemi #1 ve çözüm için olası işlemleri yapmak için önemli. Note: kendi işlerimizin neredeyse tüm durumlarında, en önemli değişikliklerden oluşan dar değişiklikler nedeniyle uyarılar hatalı pozitif olarak açılır.

- Sonuç görevinin tamamlanabilmesi için IAsyncResult. CompletedSynchronously özelliğinin doğru olması gerekir
  - TaskFactory. FromAsync çağrılırken, sonuçta elde edilen görevin tamamlanabilmesi için IAsyncResult. CompletedSynchronously özelliğinin uygulanması doğru olmalıdır. Diğer bir deyişle, özelliğinin true döndürmesi gerekir ve yalnızca uygulama eşzamanlı olarak tamamlanırsa. Daha önce özellik denetlenmedi.
  - Etkilenen kitaplıklar: mscorlib, System. Threading. Tasks
  - Önerilen eylem: TaskFactory. FromAsync ' nin doğru doğru döndürdüğünden emin olun

- DataObject. GetData artık verileri UTF-8 olarak alıyor
  - .NET Framework 4 ' ü hedefleyen veya .NET Framework 4.5.1 veya önceki sürümlerde çalışan uygulamalar için DataObject. GetData HTML biçimli verileri bir ASCII dizesi olarak alır. Sonuç olarak, ASCII olmayan karakterler (ASCII kodları 0x7F 'den büyük olan karakterler) iki rastgele karakterle temsil edilir. .NET Framework 4,5 veya üstünü hedefleyen ve .NET Framework 4.5.2 üzerinde çalışan uygulamalar Için # #N # #N, `DataObject.GetData` HTML biçimli verileri alır , 0x7F 'den büyük karakterleri temsil eden UTF-8 olarak.
  - Etkilenen kitaplıklar: GLO
  - Önerilen eylem: alınan verilerin istediğiniz biçimde olduğundan emin olun

- XmlWriter, geçersiz vekil çiftlerine atar
  - .NET Framework 4.5.2 veya önceki sürümleri hedefleyen uygulamalar için, özel durum geri dönüş işleme kullanarak geçersiz bir yedek çifti yazmak her zaman bir özel durum oluşturmaz. .NET Framework 4,6 ' i hedefleyen uygulamalar için geçersiz bir yedek çifti yazma girişimi bir `ArgumentException`oluşturur.
  - Etkilenen kitaplıklar: System. xml, System. xml. ReaderWriter
  - Önerilen eylem: bağımsız değişken özel durumuna neden olacak geçersiz bir yedek çifti yazmatığınızdan emin olun

- HtmlTextWriter `<br/>` öğeyi doğru bir şekilde işlemiyor
  - .NET Framework 4,6 ' den başlayarak, `HtmlTextWriter.RenderBeginTag()` ve `HtmlTextWriter.RenderEndTag()` bir `<BR />` öğesiyle çağırmak doğru şekilde yalnızca bir `<BR />` ekler (iki yerine)
  - Etkilenen kitaplıklar: System. Web
  - Önerilen eylem: istediğiniz `<BR />` miktarını yerleştirdiğinizden emin olun. bu nedenle, üretim işinde rastgele bir davranış görülmemelidir

- CreateDefaultAuthorizationContext 'in null bir bağımsız değişkenle çağrılması değiştirildi
  - Null authorizationPolicies bağımsız değişkeniyle `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` çağrısı tarafından döndürülen AuthorizationContext 'in uygulanması, .NET Framework 4,6 ' deki uygulamasını değiştirdi.
  - Etkilenen kitaplıklar: System. IdentityModel
  - Önerilen eylem: null yetkilendirme ilkesi olduğunda, beklenen yeni davranışı işlediğinizden emin olun
  
- RSACng artık standart olmayan anahtar boyutunun RSA anahtarlarını doğru şekilde yükler
  - 4\.6.2 ' den önceki .NET Framework sürümlerde, RSA sertifikaları için standart olmayan anahtar boyutlarına sahip müşteriler bu anahtarlara `GetRSAPublicKey()` ve `GetRSAPrivateKey()` uzantı yöntemleri aracılığıyla erişemez. "İstenen anahtar boyutu desteklenmiyor" iletisiyle bir `CryptographicException` oluşturulur. .NET Framework 4.6.2 Bu sorun düzeltildi. Benzer şekilde, `RSA.ImportParameters()` ve `RSACng.ImportParameters()` artık `CryptographicException`' i oluşturmadan standart olmayan anahtar boyutlarıyla çalışır.
  - Etkilenen kitaplıklar: mscorlib, System. Core
  - Önerilen eylem: RSA anahtarlarının beklendiği gibi çalıştığından emin olun

- Yol noktalı virgül denetimleri daha sıkı
  - .NET Framework 4.6.2 ' de, daha önce desteklenmeyen yolları (hem length hem de biçiminde) desteklemeye yönelik bir dizi değişiklik yapılmıştır. Uygun sürücü ayırıcı (iki nokta üst üste) olup olmadığını denetler ve daha doğru bir şekilde, birkaç seçim yolu API 'SI ile kullanım dışı olarak kullanıldıkları yerde bazı URI yollarını engellemenin yan etkisi vardı.
  - Etkilenen kitaplıklar: mscorlib, System. Runtime. Extensions
  - Önerilen eylem:

- ClaimsIdentity oluşturucuları çağrıları
  - .NET Framework 4.6.2 ile başlayarak, bir `T:System.Security.Principal.IIdentity` parametresine sahip `T:System.Security.Claims.ClaimsIdentity` oluşturucularının `P:System.Security.Claims.ClaimsIdentify.Actor` özelliğini nasıl ayarlayabir değişikliği vardır. `T:System.Security.Principal.IIdentity` bağımsız değişkeni bir `T:System.Security.Claims.ClaimsIdentity` nesnedir ve bu `T:System.Security.Claims.ClaimsIdentity` nesnesinin `P:System.Security.Claims.ClaimsIdentify.Actor` özelliği `null`değilse, `P:System.Security.Claims.ClaimsIdentify.Actor` özelliği `M:System.Security.Claims.ClaimsIdentity.Clone` yöntemi kullanılarak iliştirilir. Framework 4.6.1 ve önceki sürümlerde `P:System.Security.Claims.ClaimsIdentify.Actor` özelliği var olan bir başvuru olarak eklenir. Bu değişiklik nedeniyle .NET Framework 4.6.2 ile başlayarak, yeni `T:System.Security.Claims.ClaimsIdentity` nesnesinin `P:System.Security.Claims.ClaimsIdentify.Actor` özelliği oluşturucunun `T:System.Security.Principal.IIdentity` bağımsız değişkeninin `P:System.Security.Claims.ClaimsIdentify.Actor` özelliğine eşit değildir. .NET Framework 4.6.1 ve önceki sürümlerde, eşittir.
  - Etkilenen kitaplıklar: mscorlib
  - Önerilen eylem: yeni çalışma zamanında beklenen ClaimsIdentity 'nin çalıştığından emin olun

- DataContractJsonSerializer ile denetim karakterlerinin serileştirilmesi artık ECMAScript V6 ve V8 ile uyumludur
  - .NET Framework 4.6.2 ve önceki sürümlerinde, DataContractJsonSerializer, \b, \f ve \t gibi bazı özel denetim karakterlerini ECMAScript V6 ve V8 standartlarıyla uyumlu olacak şekilde serileştirmedi. 4,7 .NET Framework başlayarak, bu denetim karakterlerinin serileştirilmesi ECMAScript V6 ve V8 ile uyumludur.
  - Etkilenen kitaplıklar: System. Runtime. Serialization. JSON
  - Önerilen eylem: DataContractJsonSerializer ile aynı davranışa sahip olun
