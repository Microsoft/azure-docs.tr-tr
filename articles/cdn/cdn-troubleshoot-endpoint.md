---
title: Azure CDN uç noktalar sorunlarını giderme-404 durum kodu
description: Azure CDN uç noktalarıyla 404 yanıt kodu sorunlarını giderin.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: c332c6712cdf057491e3039854aa1a29bd54196f
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083126"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>404 durum kodu döndüren uç noktalar Azure CDN sorunlarını giderme
Bu makale, 404 HTTP yanıt durum kodu döndüren Azure Content Delivery Network (CDN) uç noktalarıyla ilgili sorunları gidermenize olanak sağlar.

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayı da oluşturabilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin.

## <a name="symptom"></a>Belirti
Bir CDN profili ve uç noktası oluşturdunuz, ancak içeriğiniz CDN 'de mevcut görünmüyor. CDN URL 'SI aracılığıyla içeriğinize erişmeye çalışan kullanıcılar bir HTTP 404 durum kodu alır. 

## <a name="cause"></a>Nedeni
Aşağıdakiler de dahil olmak üzere birkaç olası nedeni vardır:

* Dosyanın kaynağı CDN 'ye görünür değil.
* Uç nokta yanlış yapılandırılmıştır ve CDN 'nin yanlış yere bakmasına neden olur.
* Konak, CDN 'den konak üstbilgisini reddediyor.
* Uç noktanın CDN genelinde yayılması zaman almamıştı.

## <a name="troubleshooting-steps"></a>Sorun giderme adımları
> [!IMPORTANT]
> Bir CDN uç noktası oluşturduktan sonra, kaydın CDN aracılığıyla yayılması zaman alacağından, bu, kullanım için hemen kullanılabilir olmayacaktır:
> - **Microsoft’tan Azure CDN Standart** profilleri için yayma işlemi genellikle on dakikada tamamlanır. 
> - **Akamai’den Azure CDN Standart** profilleri için yayma işlemi genellikle bir dakika içinde tamamlanır. 
> - **Verizon’dan Azure CDN Standart** ve **Verizon’dan Azure CDN Premium** profilleri için yayma işlemi genellikle 90 dakika içinde tamamlanır. 
> 
> Bu belgedeki adımları tamamlayıp hala 404 yanıtını alıyorsanız, bir destek bileti açmadan önce birkaç saat bekledikten sonra tekrar kontrol etmeniz gerekir.
> 
> 

### <a name="check-the-origin-file"></a>Kaynak dosyasını denetleme
İlk olarak, önbelleğe alma dosyasının kaynak sunucuda kullanılabilir olduğunu ve internet üzerinde herkese açık olduğunu doğrulayın. Bunu yapmanın en hızlı yolu, bir tarayıcıyı özel veya inbilito oturumunda açmak ve doğrudan dosyaya gözatmanız kullanmaktır. URL 'YI yazın veya Adres kutusuna yapıştırın ve istediğiniz dosyayla sonuçlandığından emin olun. Örneğin, https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt adresinden erişilebilen bir Azure depolama hesabında bir dosyanız olduğunu varsayalım. Bu dosyanın içeriğini başarıyla yükleyebiliyorsanız, testi geçirir.

![Başarılı!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Bu, dosyanızın herkese açık bir şekilde doğrulanması için en hızlı ve en kolay yoldur, ancak kuruluşunuzdaki bazı ağ yapılandırmalarının bir dosyanın herkese açık bir şekilde, yalnızca ağınızın kullanıcıları tarafından görülebilmesi gibi görünmesini sağlayabilir ( Azure). Bunun durum olmamasını sağlamak için, dosyayı kuruluşunuzun ağına bağlı olmayan bir mobil cihaz veya Azure 'da bir sanal makine gibi bir dış tarayıcıyla test edin.
> 
> 

### <a name="check-the-origin-settings"></a>Kaynak ayarlarını denetleyin
Dosyanın Internet 'te herkese açık olduğunu doğruladıktan sonra, kaynak ayarlarınızı doğrulayın. [Azure portalında](https://portal.azure.com), CDN profilinize gidin ve sorun gidermeye çalıştığınız uç noktayı seçin. Elde edilen **uç nokta** sayfasında, kaynağı seçin.  

![Kaynak vurgulanacak uç nokta sayfası](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

**Kaynak** sayfası görüntülenir. 

![Kaynak sayfası](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Kaynak türü ve ana bilgisayar adı
**Kaynak türü** ve **kaynak konak adı** değerlerinin doğru olduğundan emin olun. Bu örnekte, https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, URL 'nin ana bilgisayar adı kısmı doğru olan *cdndocdemo.blob.Core.Windows.net*'dir. Azure depolama, Web uygulaması ve bulut hizmeti kaynakları, **kaynak ana bilgisayar adı** alanı için bir açılan liste değeri kullandığından, yanlış yazımlar sorun değildir. Ancak, özel bir kaynak kullanırsanız, ana bilgisayar adının doğru yazıldığından emin olun.

#### <a name="http-and-https-ports"></a>HTTP ve HTTPS bağlantı noktaları
**Http** ve **HTTPS bağlantı noktalarınızı**denetleyin. Çoğu durumda, 80 ve 443 doğru olur ve hiçbir değişiklik yapmanız gerekmez.  Ancak, kaynak sunucu farklı bir bağlantı noktasında dinliyorsa, bu da burada temsil edilir. Emin değilseniz, kaynak dosyanızın URL 'sini görüntüleyin. HTTP ve HTTPS belirtimleri varsayılanlar olarak 80 ve 443 bağlantı noktalarını kullanır. Örnek URL 'de, https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, bir bağlantı noktası belirtilmez, bu nedenle varsayılan 443 varsayılır ve ayarların doğru olması gerekir.  

Ancak, daha önce test ettiğiniz kaynak dosyanın URL 'sinin http:\//www.contoso.com:8080/file.txt olduğunu varsayalım. Ana bilgisayar adı segmentinin sonundaki *: 8080* kısmına göz önüne alın. Bu sayı, tarayıcıya www\.contoso.com adresindeki Web sunucusuna bağlanmak için 8080 numaralı bağlantı noktasını kullanmasını söyler. bu nedenle, **http bağlantı noktası** alanına *8080* girmeniz gerekir. Bu bağlantı noktası ayarlarının yalnızca uç noktanın kaynaktan bilgi almak için kullandığı bağlantı noktasını etkilediğini unutmayın.

> [!NOTE]
> **Akamai bitiş noktalarından Azure CDN Standart** , kaynaklar IÇIN tam TCP bağlantı noktası aralığına izin vermez.  İzin verilmeyen kaynak bağlantı noktalarının listesi için bkz. [Akamai'den Azure CDN İzin Verilen Kaynak Bağlantı Noktaları](/previous-versions/azure/mt757337(v=azure.100)).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Uç nokta ayarlarını denetleyin
**Uç nokta** sayfasında **Yapılandır** düğmesini seçin.

![Yapılandır düğmesinin vurgulandığı uç nokta sayfası](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

CDN uç noktası **Yapılandır** sayfası görünür.

![Sayfayı Yapılandır](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protokoller
**Protokoller**için, istemciler tarafından kullanılan protokolün seçili olduğunu doğrulayın. İstemci tarafından kullanılan aynı protokol, kaynağa erişmek için kullanılan bağlantı noktası olduğundan, önceki bölümde kaynak bağlantı noktalarının doğru bir şekilde yapılandırılması önemlidir. CDN uç noktası, kaynak bağlantı noktalarından bağımsız olarak yalnızca varsayılan HTTP ve HTTPS bağlantı noktalarını (80 ve 443) dinler.

Http:\//www.contoso.com:8080/file.txt ile kuramsal örneğimize dönelim.  Anımsanacak şekilde contoso, HTTP bağlantı noktası olarak *8080* ' i belirtti, ancak aynı zamanda HTTPS bağlantı noktası olarak *44300* ' i belirteceğiz.  *Contoso*adlı bir uç nokta OLUŞTURULDUKLARıNDA, CDN uç noktası ana bilgisayar adı *contoso.azureedge.net*olacaktır.  Http:\//contoso.azureedge.net/file.txt için bir istek bir HTTP isteğidir, bu nedenle uç nokta 8080 numaralı bağlantı noktasında kaynağı istemciden almak için HTTP kullanır.  HTTPS üzerinden güvenli bir istek https:\//contoso.azureedge.net/file.txt, bitiş noktasının, dosyayı kaynaktan alırken bağlantı noktası 44300 üzerinde HTTPS kullanmasına neden olur.

#### <a name="origin-host-header"></a>Kaynak ana bilgisayar üst bilgisi
**Kaynak ana bilgisayar üst bilgisi** , her istekle birlikte kaynağa gönderilen ana bilgisayar üst bilgisi değeridir.  Çoğu durumda bu, daha önce doğrulandığımız **kaynak ana bilgisayar adıyla** aynı olmalıdır.  Bu alandaki yanlış bir değer genellikle 404 durum oluşmasına neden olmaz, ancak kaynağın beklediği değere bağlı olarak diğer 4xx durumlarının oluşmasına neden olabilir.

#### <a name="origin-path"></a>Kaynak yolu
Son olarak, **kaynak yolumuzu**doğrulayacağız.  Varsayılan olarak bu boştur.  Bu alanı yalnızca CDN 'de kullanılabilir hale getirmek istediğiniz kaynak tarafından barındırılan kaynakların kapsamını daraltmak istiyorsanız kullanmanız gerekir.  

Örnek uç noktada, depolama hesabındaki tüm kaynakların kullanılabilir olmasını istiyoruz, bu nedenle **kaynak yolu** boş bırakılmıştı.  Bu, https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt isteğinin bitiş noktasından */publicblob/Lorem.txt*isteyen cdndocdemo.Core.Windows.net 'e bir bağlantıyla sonuçlanır.  Benzer şekilde, https:\//cdndocdemo.azureedge.net/donotcache/status.png için bir istek kaynaktan */donotcache/Status. png* isteğinde bulunan uç noktada sonuçlanır.

Ancak kaynak üzerinde her yol için CDN 'yi kullanmak istemiyorsanız ne olacak?  Yalnızca *publicblob* yolunu göstermek istediğinizi varsayalım.  **Kaynak yolu** alanına */publicblob* ' u girmemiz durumunda bu, bitiş noktasının kaynağa yapılan her istek için */publicblob* eklemesi oluşmasına neden olur.  Bu, https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt isteğinin artık URL 'nin istek kısmını, */publicblob/Lorem.txt*ve Append */publicblob* ' u başlangıca alacağını gösterir. Bu, kaynaktan */publicblob/publicblob/Lorem.txt* isteğine neden olur.  Bu yol gerçek bir dosyaya çözümlenmezse, kaynak 404 durumunu döndürür.  Bu örnekteki Lorem. txt dosyasını almaya yönelik doğru URL aslında https:\//cdndocdemo.azureedge.net/lorem.txt.  URL 'nin istek kısmı */lorem.txt* olduğundan ve uç nokta */publicblob*' u eklediğinden, kaynak kaynağa geçilen istek nedeniyle/publicblob/Lorem.txt ile sonuçlanan */publicblob* yolunu herkese eklememiz gerektiğini unutmayın.

