---
title: Etki alanı ve SSL sertifikalarının sorunlarını giderme-Azure App Service | Microsoft Docs
description: Azure App Service etki alanı ve SSL sertifikası sorunlarını giderme
services: app-service\web
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 778836661ff15c334823f95fef42acadb3e8b649
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470138"
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-app-service"></a>Azure App Service etki alanı ve SSL sertifikası sorunlarını giderme

Bu makalede, Azure App Service ' de Web uygulamalarınız için bir etki alanı veya SSL sertifikası yapılandırdığınızda karşılaşabileceğiniz yaygın sorunlar listelenmektedir. Ayrıca, bu sorunlara yönelik olası nedenler ve çözümler açıklanmaktadır.

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayı da oluşturabilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Sertifika sorunları

### <a name="you-cant-add-an-ssl-certificate-binding-to-an-app"></a>Bir uygulamaya SSL sertifikası bağlaması ekleyemezsiniz 

#### <a name="symptom"></a>Belirti

Bir SSL bağlaması eklediğinizde aşağıdaki hata iletisini alırsınız:

"SSL bağlaması eklenemedi. Başka bir VIP zaten bu sertifikayı kullandığından, var olan VIP için sertifika ayarlanamıyor. "

#### <a name="cause"></a>Nedeni

Birden çok uygulama genelinde aynı IP adresi için birden çok IP tabanlı SSL bağlaması varsa, bu sorun oluşabilir. Örneğin, A uygulamasında eski bir sertifikaya sahip IP tabanlı bir SSL vardır. B uygulamasının aynı IP adresi için yeni bir sertifikaya sahip IP tabanlı bir SSL vardır. Uygulama SSL bağlamasını yeni sertifikayla güncelleştirdiğinizde, aynı IP adresi başka bir uygulama için kullanıldığından bu hata ile başarısız olur. 

#### <a name="solution"></a>Çözüm 

Bu sorunu gidermek için aşağıdaki yöntemlerden birini kullanın:

- Eski sertifikayı kullanan uygulamada IP tabanlı SSL bağlamasını silin. 
- Yeni sertifikayı kullanan yeni bir IP tabanlı SSL bağlaması oluşturun.

### <a name="you-cant-delete-a-certificate"></a>Bir sertifikayı silemezsiniz 

#### <a name="symptom"></a>Belirti

Bir sertifikayı silmeye çalıştığınızda aşağıdaki hata iletisini alırsınız:

"Şu anda bir SSL bağlamasında kullanılmakta olduğu için sertifika silinemiyor. Sertifikayı silebilmeniz için önce SSL bağlamasının kaldırılması gerekir. "

#### <a name="cause"></a>Nedeni

Bu sorun, başka bir uygulamanın sertifikayı kullanması durumunda meydana gelebilir.

#### <a name="solution"></a>Çözüm

Bu sertifikanın SSL bağlamasını uygulamalardan kaldırın. Ardından sertifikayı silmeyi deneyin. Yine de sertifikayı silemseniz, Internet tarayıcı önbelleğini temizleyin ve Azure portal yeni bir tarayıcı penceresinde yeniden açın. Ardından sertifikayı silmeyi deneyin.

### <a name="you-cant-purchase-an-app-service-certificate"></a>App Service sertifikası satın alamazsınız 

#### <a name="symptom"></a>Belirti
Azure portal bir [Azure App Service sertifikası](./configure-ssl-certificate.md#import-an-app-service-certificate) satın alamazsınız.

#### <a name="cause-and-solution"></a>Neden ve çözüm
Bu sorun, aşağıdakilerden herhangi biri nedeniyle oluşabilir:

- App Service planı ücretsizdir veya paylaşılır. Bu fiyatlandırma katmanları SSL 'yi desteklemez. 

    **Çözüm**: uygulama için App Service planını standart olarak yükseltin.

- Aboneliğin geçerli bir kredi kartı yok.

    **Çözüm**: aboneliğinize geçerli bir kredi kartı ekleyin. 

- Abonelik teklifi, Microsoft öğrenci gibi bir App Service sertifikası satın almayı desteklemez.  

    **Çözüm**: aboneliğinizi yükseltin. 

- Abonelik, bir abonelikte izin verilen satın alma sınırına ulaştı.

    **Çözüm**: App Service sertifikalarda, Kullandıkça öde ve EA Abonelik türleri için 10 sertifika satın alma limiti vardır. Diğer abonelik türleri için sınır 3 ' tür. Limiti artırmak için [Azure desteğine](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)başvurun.
- App Service sertifikası sahtekarlık olarak işaretlendi. Şu hata iletisini aldınız: "sertifikanız olası sahtekarlık için işaretlendi. İstek şu anda gözden geçirme aşamasındadır. Sertifika 24 saat içinde kullanılabilir duruma gelmezse Azure desteği 'ne başvurun. "

    **Çözüm**: sertifika sahtekarlık olarak işaretlenmişse ve 24 saat sonra çözümlenmiyorsa, şu adımları izleyin:

    1. [Azure portalında](https://portal.azure.com) oturum açın.
    2. **App Service sertifikalar**' a gidin ve sertifikayı seçin.
    3. **Sertifika yapılandırması** ' nı seçin > **2. Adım:**  > **etki alanı doğrulamasını**doğrulayın. Bu adım, sorunu çözmek için Azure Sertifika sağlayıcısına bir e-posta bildirimi gönderir.

## <a name="custom-domain-problems"></a>Özel etki alanı sorunları

### <a name="a-custom-domain-returns-a-404-error"></a>Özel bir etki alanı 404 hatası döndürüyor 

#### <a name="symptom"></a>Belirti

Özel etki alanı adını kullanarak siteye gözattığınızda aşağıdaki hata iletisini alırsınız:

"Hata 404-Web uygulaması bulunamadı."

#### <a name="cause-and-solution"></a>Neden ve çözüm

**Neden 1** 

Yapılandırdığınız özel etki alanında bir CNAME veya bir kayıt eksik. 

**Neden 1 için çözüm**

- Bir kayıt eklediyseniz, bir TXT kaydının de eklendiğinden emin olun. Daha fazla bilgi için bkz. [A kaydı oluşturma](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Uygulamanız için kök etki alanını kullanmanız gerekmiyorsa, bir kayıt yerine bir CNAME kaydı kullanmanızı öneririz.
- Aynı etki alanı için hem CNAME kaydı hem de bir kayıt kullanmayın. Bu sorun bir çakışmaya neden olabilir ve etki alanının çözülmesini engelleyebilir. 

**Neden 2** 

Internet tarayıcısı, etki alanınız için eski IP adresini önbelleğe almaya devam edebilir. 

**Neden 2 için çözüm**

Tarayıcıyı temizleyin. Windows cihazları için `ipconfig /flushdns`komutu çalıştırabilirsiniz. Etki alanınızı uygulamanın IP adresine işaret ettiğini doğrulamak için [WhatsmyDNS.net](https://www.whatsmydns.net/) kullanın. 

### <a name="you-cant-add-a-subdomain"></a>Alt etki alanı ekleyemezsiniz 

#### <a name="symptom"></a>Belirti

Bir alt etki alanı atamak için uygulamaya yeni bir ana bilgisayar adı ekleyemezsiniz.

#### <a name="solution"></a>Çözüm

- Uygulamaya bir ana bilgisayar adı ekleme izinlerinizin olduğundan emin olmak için abonelik Yöneticisi ile görüşün.
- Daha fazla alt etki alanı gerekiyorsa, etki alanı barındırmayı Azure etki alanı adı hizmeti (DNS) olarak değiştirmenizi öneririz. Azure DNS kullanarak, uygulamanıza 500 ana bilgisayar adı ekleyebilirsiniz. Daha fazla bilgi için bkz. alt [etki alanı ekleme](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).

### <a name="dns-cant-be-resolved"></a>DNS çözümlenemiyor

#### <a name="symptom"></a>Belirti

Aşağıdaki hata iletisini aldınız:

"DNS kaydı bulunamadı."

#### <a name="cause"></a>Nedeni
Bu sorun aşağıdaki nedenlerden biriyle oluşur:

- Yaşam süresi (TTL) döneminin süresi dolmadı. TTL değerini öğrenmek için etki alanınız için DNS yapılandırmasını denetleyip sürenin sona ermesini bekleyin.
- DNS yapılandırması yanlış.

#### <a name="solution"></a>Çözüm
- Bu sorunun kendisini çözmesi için 48 saat bekleyin.
- DNS yapılandırmanızda TTL ayarını değiştirebiliyorsanız, sorunu giderip çözmediğine bakmak için değeri 5 dakika olarak değiştirin.
- Etki alanınızı uygulamanın IP adresine işaret ettiğini doğrulamak için [WhatsmyDNS.net](https://www.whatsmydns.net/) kullanın. Aksi takdirde, bir kaydı uygulamanın doğru IP adresine yapılandırın.

### <a name="you-need-to-restore-a-deleted-domain"></a>Silinen bir etki alanını geri yüklemeniz gerekiyor 

#### <a name="symptom"></a>Belirti
Etki alanınız Azure portal artık görünmez.

#### <a name="cause"></a>Nedeni 
Aboneliğin sahibi, yanlışlıkla etki alanını silmiş olabilir.

#### <a name="solution"></a>Çözüm
Etki alanınız yedi günden daha önce silinmişse, etki alanı silme işlemini henüz başlatmamıştır. Bu durumda, aynı etki alanını aynı abonelikte Azure portal yeniden satın alabilirsiniz. (Arama kutusuna tam etki alanı adını yazdığınızdan emin olun.) Bu etki alanı için bir daha ücretlendirilmezsiniz. Etki alanı yedi günden daha önce silinmişse, etki alanını geri yüklemeyle ilgili yardım için [Azure desteğine](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) başvurun.

## <a name="domain-problems"></a>Etki alanı sorunları

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>Yanlış etki alanı için bir SSL sertifikası satın aldınız

#### <a name="symptom"></a>Belirti

Yanlış etki alanı için bir App Service sertifikası satın aldınız. Sertifikayı doğru etki alanını kullanacak şekilde güncelleştiremezsiniz.

#### <a name="solution"></a>Çözüm

Bu sertifikayı silin ve yeni bir sertifika satın alın.

Yanlış etki alanını kullanan geçerli sertifika "verilen" durumundaysa, bu sertifika için de faturalandırılırsınız. App Service sertifikalar kullanılamaz, ancak başka seçenekler olup olmadığını görmek için [Azure desteğine](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) başvurabilirsiniz. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Bir App Service sertifikası yenilendi, ancak uygulama eski sertifikayı gösterir 

#### <a name="symptom"></a>Belirti

App Service sertifikası yenilendi, ancak App Service sertifikasını kullanan uygulama hala eski sertifikayı kullanıyor. Ayrıca, HTTPS protokolünün gerekli olduğunu belirten bir uyarı aldınız.

#### <a name="cause"></a>Nedeni 
App Service, sertifikanızı 48 saat içinde otomatik olarak eşitler. Bir sertifikayı döndürdüğünüzde veya güncelleştirdiğinizde, bazı durumlarda uygulama yeni güncelleştirilmiş sertifikayı değil eski sertifikayı almaya devam eder. Bunun nedeni, sertifika kaynağını eşitleme işinin henüz çalıştırılmadığını unutmayın. Eşitle ' ye tıklayın. Eşitleme işlemi, uygulamalarınıza herhangi bir kapalı kalma süresine neden olmadan App Service içindeki sertifika için konak bağlamalarını otomatik olarak güncelleştirir.
 
#### <a name="solution"></a>Çözüm

Sertifikanın eşitlenmesini zorunlu hale getirebilirsiniz:

1. [Azure portalında](https://portal.azure.com) oturum açın. **App Service sertifikaları**' nı seçin ve ardından sertifikayı seçin.
2. Yeniden **anahtarlama ve eşitleme**' yi seçin ve ardından **Eşitle**' yi seçin. Eşitlemenin tamamlaması biraz zaman alır. 
3. Eşitleme tamamlandığında, şu bildirimi görürsünüz: "tüm kaynaklar en son sertifika ile başarıyla güncelleştirildi."

### <a name="domain-verification-is-not-working"></a>Etki alanı doğrulaması çalışmıyor 

#### <a name="symptom"></a>Belirti 
App Service sertifikası, sertifika kullanıma hazırlanmadan önce etki alanı doğrulaması gerektirir. **Doğrula**' yı seçtiğinizde işlem başarısız olur.

#### <a name="solution"></a>Çözüm
Bir TXT kaydı ekleyerek etki alanınızı el ile doğrulayın:
 
1.  Etki alanı adınızı barındıran etki alanı adı hizmeti (DNS) sağlayıcısına gidin.
2.  Etki alanınız için Azure portal gösterilen etki alanı belirtecinin değerini kullanan bir TXT kaydı ekleyin. 

DNS yayılması 'nın çalışması için birkaç dakika bekleyin ve ardından doğrulamayı tetiklemek için **Yenile** düğmesini seçin. 

Alternatif olarak, etki alanınızı el ile doğrulamak için HTML Web sayfası yöntemini de kullanabilirsiniz. Bu yöntem, sertifika yetkilisinin sertifikanın verildiği etki alanının etki alanı sahipliğini onaylamasını sağlar.

1.  {Etki alanı doğrulama belirteci}. html adlı bir HTML dosyası oluşturun. Bu dosyanın içeriği, etki alanı doğrulama belirtecinin değeri olmalıdır.
3.  Bu dosyayı, etki alanınızı barındıran Web sunucusunun köküne yükleyin.
4.  Sertifika durumunu denetlemek için **Yenile** ' yi seçin. Doğrulamanın tamamlanması birkaç dakika sürebilir.

Örneğin, etki alanı doğrulama belirteci 1234abcd ile azure.com için standart bir sertifika satın alırsanız, https://azure.com/1234abcd.html yapılan bir Web isteği 1234abcd döndürmelidir. 

> [!IMPORTANT]
> Bir sertifika sırasının, etki alanı doğrulama işlemini tamamlaması için yalnızca 15 gün vardır. 15 gün sonra, sertifika yetkilisi sertifikayı reddeder ve sertifika için ücretlendirilirsiniz. Bu durumda, bu sertifikayı silip yeniden deneyin.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Bir etki alanı satın alamazsınız

#### <a name="symptom"></a>Belirti
Azure portal bir App Service etki alanı satın alamazsınız.

#### <a name="cause-and-solution"></a>Neden ve çözüm

Bu sorun aşağıdaki nedenlerden biriyle oluşur:

- Azure aboneliğinde kredi kartı yok veya kredi kartı geçersiz.

    **Çözüm**: aboneliğinize geçerli bir kredi kartı ekleyin.

- Abonelik sahibi olmadığınızdan, bir etki alanı satın alma izniniz yok.

    **Çözüm**: [sahibi rolünü hesabınıza atayın](../role-based-access-control/role-assignments-portal.md) . Ya da bir etki alanı satın alma izni almak için abonelik yöneticisine başvurun.
- Aboneliğinizdeki etki alanı satın alma sınırına ulaştınız. Geçerli sınır 20 ' dir.

    **Çözüm**: sınıra bir artış Istemek için [Azure desteğine](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)başvurun.
- Azure abonelik türü App Service bir etki alanının satın alınmasını desteklemez.

    **Çözüm**: Azure aboneliğinizi, Kullandıkça Öde aboneliği gibi başka bir abonelik türüne yükseltin.

### <a name="you-cant-add-a-host-name-to-an-app"></a>Bir uygulamaya ana bilgisayar adı ekleyemezsiniz 

#### <a name="symptom"></a>Belirti

Bir ana bilgisayar adı eklediğinizde, işlem etki alanını doğrulayamıyor ve doğrulayamaz.

#### <a name="cause"></a>Nedeni 

Bu sorun aşağıdaki nedenlerden biriyle oluşur:

- Ana bilgisayar adı ekleme izniniz yok.

    **Çözüm**: abonelik yöneticisinden bir ana bilgisayar adı ekleme izni vermesini isteyin.
- Etki alanı sahipliğiniz doğrulanamadı.

    **Çözüm**: CNAME veya bir kaydın doğru yapılandırıldığını doğrulayın. Özel bir etki alanını bir uygulamayla eşlemek için bir CNAME kaydı veya bir kayıt oluşturun. Kök etki alanı kullanmak istiyorsanız, ve TXT kayıtlarını kullanmanız gerekir:

    |Kayıt türü|Host|Üzerine gelin|
    |------|------|-----|
    |A|@|Bir uygulama için IP adresi|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>SSS

**Web siteme özel etki alanım 'ı satın aldıktan sonra yapılandırmam gerekir mi?**

Azure portal bir etki alanı satın aldığınızda, App Service uygulama otomatik olarak bu özel etki alanını kullanacak şekilde yapılandırılır. Ek adımlar uygulamanız gerekmez. Daha fazla bilgi için [Azure App Service kendi kendine yardım: Channel9 'de özel etki alanı adı ekleme](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) ' yi izleyin.

**Azure portal satın alınan bir etki alanını bir Azure VM 'ye işaret etmek için kullanabilir miyim?**

Evet, etki alanını bir VM 'ye işaret edebilirsiniz. Daha fazla bilgi için bkz. [Bir Azure hizmeti için özel etki alanı ayarları sağlamak üzere Azure DNS'yi kullanma](../dns/dns-custom-domain.md).

**Etki alanım GoDaddy veya Azure DNS mi barındırılıyor?**

App Service etki alanları, etki alanı kaydı için GoDaddy kullanır ve etki alanlarını barındırmak için Azure DNS. 

**Otomatik yenilemem, ancak e-posta yoluyla etki alanım için bir yenileme bildirimi aldı. Ne yapmam gerekir?**

Otomatik yenilemeyi etkinleştirdiyseniz, herhangi bir işlem yapmanız gerekmez. Bildirim e-postası, etki alanının süresi dolmak üzere olduğunu ve otomatik yenileme etkinleştirilmemişse el ile yenilemeyi bilgilendirmek için sağlanır.

**Etki alanım 'ı barındıracak Azure DNS için ücret ödersiniz?**

Etki alanı satın alma başlangıç ücreti yalnızca etki alanı kaydı için geçerlidir. Kayıt maliyetine ek olarak, kullanımınıza göre Azure DNS ücretleri de vardır. Daha fazla bilgi için bkz. daha fazla ayrıntı için [Azure DNS fiyatlandırması](https://azure.microsoft.com/pricing/details/dns/) .

**Etki alanım 'ı Azure portal daha önce satın aldım ve GoDaddy barındırmanın Azure DNS barındırılmasına taşımak istiyorsunuz. Bunu nasıl yapabilirim?**

Azure DNS barındırmak için geçirilmesi zorunlu değildir. Azure DNS geçiş yapmak istiyorsanız, Azure portal etki alanı yönetim deneyimi Azure DNS 'e geçmek için gereken adımlarla ilgili bilgiler sağlar. Etki alanı App Service aracılığıyla satın alındıysa, GoDaddy Hosting 'ten Azure DNS geçişi nispeten sorunsuz bir yordamdır.

**Etki alanım App Service etki alanından satın almak istiyorum, ancak etki alanım 'ı Azure DNS yerine GoDaddy üzerinde barındırabilirim?**

24 Temmuz 2017 ' den itibaren portalda satın alınan App Service etki alanları Azure DNS barındırılır. Farklı bir barındırma sağlayıcısı kullanmayı tercih ediyorsanız, etki alanı barındırma çözümü edinmek için Web sitelerine gitmeniz gerekir.

**Etki alanım için gizlilik koruması için ödeme yapmam gerekir mi?**

Azure portal aracılığıyla bir etki alanı satın aldığınızda, ek ücret ödemeden gizlilik eklemeyi tercih edebilirsiniz. Bu, etki alanınızı Azure App Service aracılığıyla satın almanın avantajlarından biridir.

**Etki alanım 'ı artık istemiyorum, paranızı geri alabilir miyim?**

Bir etki alanı satın aldığınızda, beş güne kadar ücretlendirilmez ve bu süre, etki alanına istemeyeceğine karar verebilirsiniz. Bu beş günlük süre içinde etki alanının olmasını istemediğinize karar verirseniz ücretlendirilmezsiniz. (. UK etki alanları bunun özel bir durumdur. Bir. UK etki alanı satın aldıysanız, hemen ücretlendirilirsiniz ve iade edilemez.)

**Etki alanını Aboneliğimde başka bir Azure App Service uygulamada kullanabilir miyim?**

Evet. Azure portal özel etki alanları ve SSL dikey penceresine eriştiğinizde, satın aldığınız etki alanlarını görürsünüz. Uygulamanızı bu etki alanlarından herhangi birini kullanacak şekilde yapılandırabilirsiniz.

**Bir etki alanını bir abonelikten başka bir aboneliğe aktarabilir miyim?**

[Move-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Move-azResource) PowerShell cmdlet 'ini kullanarak bir etki alanını başka bir aboneliğe/kaynak grubuna taşıyabilirsiniz.

**Şu anda Azure App Service bir uygulaması yoksa özel etki alanım 'ı nasıl yönetebilirim?**

Bir App Service Web uygulamanız olmasa bile, etki alanınızı yönetebilirsiniz. Etki alanı, sanal makine, depolama vb. gibi Azure hizmetleri için kullanılabilir. Etki alanını App Service Web Apps için kullanmayı düşünüyorsanız, etki alanını Web uygulamanıza bağlamak için Ücretsiz App Service planı üzerinde olmayan bir Web uygulaması eklemeniz gerekir.

**Özel bir etki alanı olan bir Web uygulamasını başka bir aboneliğe veya App Service Ortamı v1 'den v2 'ye taşıyabilir miyim?**

Evet, Web uygulamanızı abonelikler arasında taşıyabilirsiniz. [Azure 'da kaynakları taşıma](../azure-resource-manager/resource-group-move-resources.md)bölümündeki yönergeleri izleyin. Web uygulamasını taşırken bazı sınırlamalar vardır. Daha fazla bilgi için bkz. [App Service kaynaklarını taşıma sınırlamaları](../azure-resource-manager/move-limitations/app-service-move-limitations.md).

Web uygulamasını taşıdıktan sonra, özel etki alanları ayarı içindeki etki alanlarının konak adı bağlamaları aynı kalmalıdır. Konak adı bağlamalarını yapılandırmak için ek adım gerekmez.
