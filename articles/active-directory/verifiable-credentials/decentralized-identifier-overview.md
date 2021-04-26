---
title: Doğrulanabilir kimlik bilgilerine Azure Active Directory giriş (Önizleme)
description: Genel Bakış Azure doğrulanabilir kimlik bilgileri.
services: active-directory
author: barclayn
manager: daveba
editor: ''
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: overview
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 04b36b9b32e78016f693e61d40246776492be0e3
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222869"
---
# <a name="introduction-to-azure-active-directory-verifiable-credentials-preview"></a>Doğrulanabilir kimlik bilgilerine Azure Active Directory giriş (Önizleme)

> [!IMPORTANT]
> Azure Active Directory doğrulanabilir kimlik bilgileri şu anda genel önizlemede.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dijital ve fiziksel yaşamlarımız, zengin bir deneyim kümesine erişmek için kullandığımız uygulamalar, hizmetler ve cihazlara giderek daha fazla bağlantılıdır. Bu dijital dönüşüm, daha önce unimaginable çeşitli yollarla yüzlerce şirket ve binlerce kullanıcı ile etkileşime girememize olanak sağlar.

Ancak kimlik verileri çok sık güvenlik ihlallerine sunuldu. Bu ihlal, insanların sosyal, profesyonel ve finans yaşamımızı etkileyen yaşamına yönelik olarak tasarlanmıştır. Microsoft daha iyi bir yol olduğunu düşündüğü. Her kişinin kendi sahip oldukları ve denetdukları bir kimlik hakkı vardır. Bunlar, dijital kimlik öğelerini güvenli bir şekilde depolayan ve gizliliği koruyan bir kimlik içerir. Bu makalede, bireyler ve kuruluşlar için açık, güvenilir, birlikte çalışabilen ve standartlara dayalı bir kimlik (DıD) çözümü oluşturmak üzere farklı bir topluluk ile uygulamalı şekilde nasıl katılacağız açıklanmaktadır.

## <a name="why-we-need-decentralized-identity"></a>Neden merkezi olmayan kimliğe ihtiyacımız var?

Bugün iş sırasında, evde ve kullanımda olan her uygulama, hizmet ve cihazdaki dijital kimliklerimizi kullanıyoruz. Bu, yaşamımız her şeyden oluşur: bir olaya yönelik bilet satın alma, otel halinde denetleme veya öğle yemeği siparişi verme. Şu anda kimlik ve tüm dijital etkileşimlerimiz, bazı hatta farkında olmadığımız diğer taraflara aittir ve denetlenmektedir.

Genellikle, kullanıcılar çeşitli uygulamalara ve cihazlara onay verir. Bu yaklaşım, kullanıcının bölümünde hangi bilgilere kimlerin erişebileceğini izlemek için yüksek ölçüde dikkatli gerektirir. Kurumsal ön planda, tüketicilerle ve iş ortaklarıyla birlikte çalışan, verileri güvenli bir şekilde, tüm ilgili gizlilik ve güvenlik özelliklerine sahip olacak şekilde güvenli bir şekilde alışverişi için yüksek dokunma düzenlemesi gerektirir.

Standartlara dayalı, merkezi bir kimlik sisteminin, kullanıcılar ve kuruluşların kendi verileri üzerinde daha fazla denetime sahip olmasını sağlayan yeni bir deneyim kümesinin kilidini açıp uygulamalar, cihazlar ve hizmet sağlayıcıları için daha yüksek düzeyde güven ve güvenlik sunma olanağı sunuyoruz

## <a name="lead-with-open-standards"></a>Açık standartlarla lider

Bir sonraki nesil nesil kimlik tabanlı deneyimlerin kilidini açmak için müşteriler, iş ortakları ve topluluk ile yakın bir şekilde çalışmaya kararlıdır ve bu alanda inanılmaz katkılara sahip olan bireyler ve kuruluşlara iş ortağı heyecanlanıyoruz. Eğer ekosisteminin büyüyeceği, standartlara, teknik bileşenlere ve kod Teslim edilebilirlerin açık kaynak olması ve tümünün erişimine sahip olması gerekir.

Microsoft, merkezi olmayan Identity Foundation (DıF), W3C kimlik bilgileri topluluk grubu ve daha geniş kimlik topluluğu üyeleriyle etkin bir şekilde işbirliği yapılır. Kritik standartları tanımlamak ve geliştirmek için bu gruplarla çalıştık ve hizmetlerimizde aşağıdaki standartlar uygulandı.

* [W3C merkezi olmayan tanımlayıcılar](https://www.w3.org/TR/did-core/)
* [W3C doğrulanabilir kimlik bilgileri](https://www.w3.org/TR/vc-data-model/)
* [DıF kenar ağacı](https://identity.foundation/sidetree/spec/)
* [DıF Iyi bilinen bir yapılandırma](https://identity.foundation/specs/did-configuration/)
* [DıF DıD-SıOP](https://identity.foundation/did-siop/)
* [DıF sunum değişimi](https://identity.foundation/presentation-exchange/)


## <a name="what-are-dids"></a>Dalların anlamı nedir?

DIB 'leri anlayabilmemiz için, bunları geçerli kimlik sistemleriyle karşılaştırmanıza yardımcı olur. E-posta adresleri ve sosyal ağ kimlikleri işbirliği için kolay bir diğer adlardır, ancak artık işbirliğinin ötesinde çok sayıda senaryoda veri erişimi için denetim noktaları olarak kullanıma sunulmuştur. Bu, bu kimliklere erişim herhangi bir zamanda dış taraflarca kaldırılabildiğinden olası bir sorun oluşturur.

Merkezi olmayan tanımlayıcılar (DIB 'Ler) farklıdır. Dıds, Kullanıcı tarafından üretilmiş, kendine ait, genel olarak benzersiz tanımlayıcılardır ve bu da, Iyon gibi dağıtılmış sistemler için kök olarak kullanılır. Bunlar, daha fazla güvence güvencesi, censorgemisi ve evasiveness delebilirlik gibi benzersiz özelliklere sahiptir. Bu öznitelikler, kendi kendine sahiplik ve Kullanıcı denetimi sağlamak için tasarlanan tüm KIMLIK sistemleri için önemlidir. 

Microsoft 'un doğrulanabilen kimlik bilgileri çözümü, bağlı olan tarafın (Doğrulayıcı) doğrulanabilir kimlik bilgisinin sahipleri olduğunu kanıtlama konusunda bilgi sahibi olmak üzere, şifreli olmayan kimlik bilgilerini (DIB 'Ler) şifreleme olarak imzalamak için kullanılır. Bu nedenle, Microsoft teklifini temel alan doğrulanabilir kimlik bilgisi çözümü oluşturan herkes için merkezi olmayan tanımlayıcıların temel olarak anlaşılmasına önerilir.
## <a name="what-are-verifiable-credentials"></a>Doğrulanabilir kimlik bilgileri nelerdir?

 Günlük yaşamımızda kimlikleri kullanıyoruz. Bir otomobil çalıştırma imkanımız hakkında kanıt olarak kullandığımız sürücü Lisanslarımız vardır. Üniversiteler, bir eğitim düzeyi sunuyoruz. Diğer ülkelere ulaşdığımızda ne kadar yetkililer olduğunu kanıtlamak için Passport 'lar kullanıyoruz. Veri modeli, internet üzerinden çalışırken ancak kullanıcının gizliliğini karşılayan güvenli bir şekilde bu tür senaryoları nasıl işleyebileceğinizi açıklar. [Doğrulanabilir kimlik bilgileri veri modeli 1,0](https://www.w3.org/TR/vc-data-model/) ' de daha fazla bilgi edinebilirsiniz

Kısacası, doğrulanabilir kimlik bilgileri, bir konuyla ilgili veren attest bilgileri tarafından yapılan taleplerden oluşan veri nesneleridir. Bu talepler, şema tarafından tanımlanır ve veren ve Subject olduğunu içerir. Veren 'in, bu bilgileri test ettikleri kanıt olarak bir dijital imza oluşturuyor.


## <a name="how-does-decentralized-identity-work"></a>Merkezileşmemiş kimlik nasıl çalışır?

Yeni bir kimlik biçimine ihtiyacımız var. Kendi kendine sahiplik ve censorgemisi gibi önemli kimlik öznitelikleri sağlamak için teknolojileri ve standartları birlikte getiren bir kimliğe ihtiyacımız var. Bu yetenekler, mevcut sistemleri kullanarak elde etmek zordur.

Bu taahhüt düzeyini sağlamak için yedi temel yeniliklerden oluşan bir teknik temele ihtiyacımız var. Bir anahtar yeniliği, kullanıcının sahip olduğu tanımlayıcılardır, bu tür tanımlayıcılarla ilişkili anahtarları yönetmek için bir Kullanıcı aracısıdır ve şifreli, kullanıcı denetimli veri depolarıdır.

![Microsoft 'un doğrulanabilen kimlik bilgisi ortamına genel bakış](media/decentralized-identifier-overview/microsoft-did-system.png)

**1. W3C merkezi olmayan tanımlayıcılar (DIB 'ler)** kimlikleri, kullanıcılar herhangi bir kuruluştan veya kamu 'dan bağımsız olarak oluşturur, kendilerine ait ve denetleyebilir. DIB 'Ler, ortak anahtar malzemesi, kimlik doğrulama tanımlayıcıları ve hizmet uç noktaları içeren JSON belgelerinden oluşan, genel olarak kullanılan genel anahtar altyapısı (DPKI) meta verileri ile bağlantılı genel benzersiz tanımlayıcılardır.

**2. merkezi olmayan sistem: iyon (kimlik kaplama ağı)** iyon, özel belirteçler, güvenilir Doğrulayıcıları veya diğer konsensus mekanizmalarını gerektirmeyen, tamamen belirleyici bir sidetree protokolüne dayalı bir katman 2 açık, farklı bir ağ olmalıdır; Bitpara 'in zaman zincirinin doğrusal ilerlemesi, işlemi için gereklidir. Ise ağıyla birlikte çalışarak uygulama ve hizmetlerinize tümleştirilmesi kolay [bir NPM paketi](https://www.npmjs.com/package/@decentralized-identity/ion-tools) sunuyoruz. Kitaplıklar, yeni bir DıD oluşturmayı, anahtar oluşturmayı ve Bitdeni bit para blok zincirinde oluşturmayı içerir. 

**3. Kullanıcı Aracısı/cüzdan: Microsoft Authenticator uygulaması** , gerçek kişilerin merkezi olmayan kimlikleri ve doğrulanabilir kimlik bilgilerini kullanmasına olanak sağlar. Doğrulayıcı, kimlik bilgileri için verme ve sunum isteklerini kolaylaştırır ve şifreli bir cüzdan dosyası aracılığıyla, BAŞARıNıZıN çekirdek yedeğini yönetir.

**4. Microsoft Çözümleyicimize** bağlanarak, yöntemi kullanarak ve çözüm ```did:ion``` belgesi nesnesini (DDO) döndüren Ise DÜĞÜMÜNE bağlanan bir API. DDO, ortak anahtarlar ve hizmet uç noktaları gibi, ile ilişkili DPKI meta verilerini içerir. 

**5. Azure Active Directory doğrulanmış kimlik bilgileri hizmeti** , yöntemi Ile Imzalanmış [W3C doğrulanabilir kimlik bilgileri](https://www.w3.org/TR/vc-data-model/) için bir verme ve doğrulama API 'SI ve açık kaynak SDK 'sı ```did:ion``` . Kimlik sahiplerini talepler oluşturmak, sunmak ve doğrulamak için etkinleştirir. Bu, sistem kullanıcıları arasındaki güvenin temelini oluşturur.

## <a name="a-sample-scenario"></a>Örnek senaryo

VC 'nin nasıl çalıştığını açıklamak için kullandığımız senaryo:

- Woodgrove Inc. a şirketi.
- Woodgrove çalışanlar indirimlerini sağlayan bir şirket olan proseware.
- Woodgrove Inc. proseware 'tan indirim almak isteyen bir çalışan.



Buayla, Woodgrove 'ın ağa bağlı ortamında oturum açmak için bir Kullanıcı adı ve parola sağlar. Woodgrove, Gamze 'nin Woodgrove 'ın bir çalışanı olduğunu kanıtlamaları için daha yönetilebilir bir yöntem sağlamak üzere bir VC çözümü dağıtmakta. Proseware, Woodgrove 'un VC çözümü ile uyumlu bir VC çözümü kullanıyor ve iş kanıtı olarak Woodgrove tarafından verilen kimlik bilgilerini kabul ediyor.

Woodgrove Inc. kimlik bilgisini veren, bir ortak anahtar ve özel anahtar oluşturur. Ortak anahtar, Iyon üzerinde depolanır. Anahtar altyapıya eklendiğinde, giriş blok zinciri tabanlı bir genel muhasebeye kaydedilir. Veren, Çiğdem 'e bir cüzdan uygulamasında depolanan özel anahtar sağlar. Gamze her seferinde özel anahtarı kullandığında, işlem cüzdan uygulamasında günlüğe kaydedilir.

![Microsoft-DID-genel bakış](media/decentralized-identifier-overview/did-overview.png)

## <a name="roles-in-a-verifiable-credential-solution"></a>Doğrulanabilir kimlik bilgileri çözümünde roller 

Doğrulanabilir kimlik bilgileri çözümünde üç birincil aktör vardır. Aşağıdaki diyagramda:

- **1. adım**, **Kullanıcı** bir verenden doğrulanabilir bir kimlik bilgisi ister.
- **2. adım**, kimlik bilgisini **veren** kullanıcının verdiği kanıtları doğru olduğunu ve kendi yaptığıyla imzalanmış bir doğrulanabilir kimlik bilgisi oluşturduğunu ve kullanıcının konusu olduğunu kanıtladığını sınar.
- **3. adımda** Kullanıcı, kendi yaptığıyla bir doğrulanabilen sunu (VP) imzalar ve **doğrulayıcıyı gönderir.** Doğrulayıcı daha sonra kimlik bilgisini DPKI 'a yerleştirilmiş ortak anahtarla eşleştirerek doğrular.

Bu senaryodaki roller şunlardır:

![doğrulanabilir kimlik bilgisi ortamındaki roller](media/decentralized-identifier-overview/issuer-user-verifier.png)

**veren** : veren, bir kullanıcıdan bilgi isteyen bir verme çözümü oluşturan bir kuruluştur. Bilgiler, kullanıcının kimliğini doğrulamak için kullanılır. Örneğin, Woodgrove, Inc., kendilerine doğrulanabilir kimlik bilgileri (VCs) oluşturup tüm çalışanlarına dağıtmalarını sağlayan bir verme çözümüdür. Çalışan, bir KIMLIK belirtecini veren hizmete geçiren Kullanıcı adı ve parolasıyla oturum açmak için kimlik doğrulayıcı uygulamasını kullanır. Woodgrove, Inc. tarafından gönderilen KIMLIK belirteci doğrulandıktan sonra, verme çözümü, çalışan hakkında talepler içeren ve Woodgrove Inc. DıD ile imzalanmış bir VC oluşturur. Şimdi çalışanın, konusu olduğu gibi çalışanlar da dahil olmak üzere, işveren tarafından imzalanan doğrulanabilir bir kimlik bilgisi vardır.  

**Kullanıcı** : Kullanıcı, bir VC isteyen kişi veya varlıktır. Örneğin, Gamze, Woodgrove Inc. ' nin yeni bir çalışanından daha önce iş için doğrulanabilir kimlik bilgileri kanıtı verdi. Çiğdem 'in proseware adresinde indirim almak için iş kanıtı sağlaması gerektiğinde, Çiğdem 'in sahibinin sahibi olduğunu kanıtlayan doğrulanabilir bir sunuyu imzalayarak kimlik doğrulayıcı uygulamasındaki kimlik bilgilerine erişim izni verebilir. Proseware kimlik bilgisinin Woodgrove, Inc. tarafından verildiğini doğrulayabiliyor ve Çiğdem kimlik bilgisinin sahibidir. 

**Doğrulayıcı** – Doğrulayıcı, güvendikleri bir veya daha fazla veren tarafından talepleri doğrulaması gereken bir şirket veya varlıktır. Örneğin, proseware güvenleri Woodgrove, Inc., çalışanlarınızın kimliğini doğrulama ve gerçek ve geçerli VC 'ler verme konusunda yeterli bir iş yapar. Gamze, işi için ihtiyaç duyduğu donatımı sipariş etmeye çalıştığında, proseware, Woodgrove Inc 'nin bir çalışanı olan kullanıcının kimlik bilgilerini istemesi için SıOP ve sunum alışverişi gibi açık standartları kullanır. Örneğin, proseware, Ayla telefon kamerasını taradığı QR koduna sahip bir Web sitesine bağlantı sağlayabilir. Bu, belirli bir VC için isteği başlatır, bu da kimlik doğrulayıcı analiz eder ve Çiğdem 'i proseware 'e kanıtlamak için isteği onaylama yeteneği sağlar. Proseware, doğrulanabilir sununun orijinalliğini doğrulamak için doğrulanabilir kimlik bilgileri hizmeti API 'sini veya SDK 'sını kullanabilir. Gamze tarafından sunulan bilgilere göre, filiz 'e indirim sağlar. Diğer şirketler ve kuruluşlar, Woodgrove, Inc. tarafından çalışanlara bir sorun olduğunu bilseler de bir doğrulayıcı çözümü oluşturabilir ve Woodgrove, Inc. doğrulanabilir kimlik bilgilerini kullanarak Woodgrove, Inc. çalışanları için ayrılmış özel teklifler sağlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Artık DIDs ve doğrulanabilir kimlik bilgileri hakkında bilgi edindiğinizde, kullanmaya başlama makalemizi izleyerek veya makalelerimizden biri doğrulanabilir kimlik bilgileri kavramları hakkında daha fazla ayrıntı sağlayan kendi kendinize deneyin.

- [Doğrulanabilir kimlik bilgileriyle çalışmaya başlama](get-started-verifiable-credentials.md)
- [Kimlik bilgilerinizi özelleştirme](credential-design.md)
- [Doğrulanabilir kimlik bilgileri hakkında SSS](verifiable-credentials-faq.md)