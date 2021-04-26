---
title: Üreticiler için güvenlik uygulamaları-Azure IoT cihaz sağlama hizmeti
description: Azure IoT cihaz sağlama hizmeti 'ne (DPS) cihazları hazırlayan OEM 'Ler ve cihaz Hizmetleri için genel bakış genel güvenlik uygulamalarına genel bakış
author: timlt
ms.author: timlt
ms.date: 3/02/2020
ms.topic: conceptual
ms.service: iot-dps
ms.custom: iot-p0-scenario, iot-devices-deviceOEM
ms.reviewer: nberdy
ms.openlocfilehash: dad46e21b35225855d787c367d112362fc95a463
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107010550"
---
# <a name="security-practices-for-azure-iot-device-manufacturers"></a>Azure IoT cihaz üreticilerine yönelik güvenlik uygulamaları
Daha fazla üretici IoT cihazlarını serbest bıraktığından, yaygın uygulamaların etrafında rehberlik belirlemek yararlı olur. Bu makalede, cihazları Azure IoT cihaz sağlama hizmeti (DPS) ile birlikte kullanmak üzere üretdiğinizde göz önünde bulundurmanız gereken önerilen güvenlik uygulamaları özetlenmektedir.  

> [!div class="checklist"]
> * Cihaz kimlik doğrulama seçeneklerini seçme
> * IoT cihazlarına sertifika yükleme
> * Güvenilir Platform Modülü (TPM) üretim işlemiyle tümleştirme

## <a name="selecting-device-authentication-options"></a>Cihaz kimlik doğrulama seçeneklerini seçme
IoT cihaz güvenlik ölçüsünün en son amacı, güvenli bir IoT çözümü oluşturmaktır. Ancak donanım sınırlamaları, maliyet ve güvenlik uzmanlığı düzeyi gibi sorunlar, hangi seçenekleri istediğinizi etkiler. Ayrıca, güvenlik yaklaşımınız IoT cihazlarınızın buluta nasıl bağlanacağını etkiler. IoT güvenliğinin dikkate alınması gereken [birkaç öğe](https://www.microsoft.com/research/publication/seven-properties-highly-secure-devices/) olsa da, her müşterinin karşılaştığı bir anahtar öğe, hangi kimlik doğrulama türünün kullanılacağını kullanır. 

Yaygın olarak kullanılan üç kimlik doğrulama türü X. 509.440 sertifikalarıdır, güvenilir platform modülleri (TPM) ve simetrik anahtarlardır. Diğer kimlik doğrulama türleri mevcut olsa da, Azure IoT üzerinde çözüm oluşturan müşterilerin çoğu bu üç türden birini kullanır. Bu makalenin geri kalanı, her bir kimlik doğrulama türünü kullanmanın profesyonelleri ve dezavantajlarını inceleyin.

### <a name="x509-certificate"></a>X. 509.440 sertifikası
X. 509.440 sertifikaları, kimlik doğrulaması için kullanabileceğiniz bir dijital kimlik türüdür. X. 509.440 sertifika standardı, [IETF RFC 5280](https://tools.ietf.org/html/rfc5280)' de belgelenmiştir. Azure IoT 'de sertifikaların kimliğini doğrulamak için iki yol vardır:
- #C0. Bir parmak izi algoritması, bir sertifika üzerinde bir onaltılı dize oluşturmak için çalıştırılır. Oluşturulan dize, sertifika için benzersiz bir kimlikleyici. 
- Tam zinciri temel alan CA kimlik doğrulaması. Sertifika zinciri, son varlık (EE) sertifikasını doğrulamak için gereken tüm sertifikaların hiyerarşik bir listesidir. Bir EE sertifikasının kimliğini doğrulamak için, bir güvenilen kök CA da dahil olmak üzere zincirdeki her bir sertifikanın kimliğinin doğrulanması gerekir. 

X. 509.440 için profesyonelleri:
- X. 509.440, Azure IoT 'de desteklenen en güvenli kimlik doğrulama türüdür.
- X. 509.440, sertifika yönetiminin amaçları için yüksek düzeyde denetim sağlar.
- X. 509.440 tabanlı kimlik doğrulama çözümlerini sağlamak için birçok satıcı mevcuttur.

X. 509.440 için dezavantajlarını:
- Birçok müşterinin sertifikaları için dış satıcılara ihtiyacı olabilir.
- Sertifika yönetimi maliyetli olabilir ve toplam çözüm maliyetine eklenebilir.
- Sertifika yaşam döngüsü yönetimi, lojistik iyi şekilde düşünülmedikleri takdirde zor olabilir. 

### <a name="trusted-platform-module-tpm"></a>Güvenilir Platform Modülü (TPM)
[ISO/ıec 11889](https://www.iso.org/standard/66510.html)olarak da bilinen TPM, şifreleme anahtarlarını güvenli bir şekilde oluşturmak ve depolamak için bir standarttır. TPM ayrıca standart uygulayan modüllerle etkileşim kuran bir sanal veya fiziksel g/ç cihazını da ifade eder. Bir TPM cihazı ayrı donanım, tümleşik donanım, bellenim tabanlı bir modül veya yazılım tabanlı bir modül olarak bulunabilir. 

TPMs ve simetrik anahtarlar arasında iki temel fark vardır: 
- Ayrıca, TPM yongaları X. 509.440 sertifikalarını da saklayabilir.
- DPS 'de TPM kanıtlama, asimetrik kimlik doğrulama biçimindeki TPM onay anahtarını (EK) kullanır. Asimetrik kimlik doğrulamasıyla, şifreleme için ortak anahtar kullanılır ve şifre çözme için ayrı bir özel anahtar kullanılır. Buna karşılık, simetrik anahtarlar özel anahtarın hem şifreleme hem de şifre çözme için kullanıldığı simetrik kimlik doğrulaması kullanır. 

TPM için profesyonelleri:
- TPM 'Ler, işletim sistemi için yerleşik desteğe sahip birçok Windows cihazına standart donanım olarak dahil edilmiştir. 
- TPM kanıtlama, paylaşılan erişim imzası (SAS) belirteci tabanlı simetrik anahtar kanıtlamasını daha kolay hale getirir.
- Cihaz kimlik bilgilerini kolayca sona erdirebilirsiniz ve yenileyebilir veya aktarabilirsiniz. Bir TPM cihazının yeniden sağlanması gerektiğinde, DPS IoT Hub kimlik bilgilerini otomatik olarak kaydeder.

TPM için dezavantajlarını: 
- TPMs karmaşıktır ve kullanılması zor olabilir. 
- TPM 'Ler ile uygulama geliştirme, fiziksel bir TPM veya kalite öykünücüsü olmadığı için zordur.
- Donanımınıza TPM 'YI dahil etmek için cihazınızın panosunu yeniden tasarlamanız gerekebilir. 
- Bir TPM 'ye EK eklerseniz, TPM 'nin kimliğini yok eder ve yeni bir tane oluşturur. Fiziksel yonga aynı kalır, ancak IoT çözümünüzde yeni bir kimliğe sahip olur.

### <a name="symmetric-key"></a>Simetrik anahtar
Simetrik Anahtarlarla, iletileri şifrelemek ve şifrelerini çözmek için aynı anahtar kullanılır. Sonuç olarak, aynı anahtar hem cihaz hem de bunun kimliğini doğrulayan hizmet tarafından bilinir. Azure IoT, SAS belirteç tabanlı simetrik anahtar bağlantılarını destekler. Simetrik anahtar kimlik doğrulaması, anahtarları güvenli hale getirmek ve X. 509.440 kimlik doğrulamasıyla eşit düzeyde güvenlik elde etmek için önemli bir sahip sorumluluğu gerektirir. Simetrik anahtarlar kullanıyorsanız, bir donanım güvenlik modülü (HSM) kullanarak anahtarları korumak önerilen uygulamadır.

Simetrik anahtar için profesyonelleri:
- Simetrik anahtarların kullanılması, kimlik doğrulamaya başlamak için en basit ve en düşük maliyetli yoldur.
- Oluşturmak için ek bir şey olmadığından, simetrik anahtarların kullanılması işleminizi basitleştirir. 

Simetrik anahtarın dezavantajlarını: 
- Simetrik anahtarların anahtarları güvenli hale getirmek için önemli ölçüde çaba vardır. Aynı anahtar cihaz ve bulut arasında paylaşılır, bu da anahtarın iki yerde korunması gerektiği anlamına gelir. Buna karşılık, TPM ve X. 509.440 sertifikalarıyla ilgili zorluk, özel anahtarı göstermeden ortak anahtarla birlikte temin edilir.
- Simetrik anahtarlar, zayıf güvenlik uygulamalarının izlemesini kolaylaştırır. Simetrik Anahtarlarla yaygın bir şekilde, cihazlarda şifrelenmemiş anahtarlar sabit olarak kodlanır. Bu uygulama kullanışlı olsa da anahtarları savunmasız bırakır. Simetrik anahtarı cihazda güvenli bir şekilde depolayarak, bazı riskleri azaltabilirsiniz. Bununla birlikte, önceliğiniz rahatlığı yerine en sonunda güvenlik ise, kimlik doğrulaması için X. 509.440 sertifikaları veya TPM kullanın. 

### <a name="shared-symmetric-key"></a>Paylaşılan simetrik anahtar
Paylaşılan simetrik anahtar olarak bilinen bir simetrik anahtar kimlik doğrulaması çeşitlemesi vardır. Bu yaklaşım, tüm cihazlarda aynı simetrik anahtarı kullanmayı içerir. Cihazlarda paylaşılan simetrik anahtarların kullanmaktan kaçının. 

Paylaşılan simetrik anahtar için Pro:
- Kolay bir şekilde uygulamak ve uygun ölçekte üretmek için ucuzdur. 

Paylaşılan simetrik anahtarın dezavantajlarını: 
- Saldırılara karşı çok savunmasız. Kolay uygulama avantajı, riske göre çok daha ağır bir şekilde yapılır. 
- Paylaşılan anahtarı elde ettikleri takdirde herkes cihazlarınızın kimliğine bürünebilir.
- Güvenliği aşılmış bir paylaşılan simetrik anahtara sahipseniz, muhtemelen cihazların denetimini kaybedersiniz. 

### <a name="making-the-right-choice-for-your-devices"></a>Cihazlarınız için doğru seçimi yapma
Bir kimlik doğrulama yöntemi seçmek için, benzersiz üretim sürecinizdeki her yaklaşımın avantajlarını ve maliyetlerini göz önünde bulundurduğunuzdan emin olun.  Cihaz kimlik doğrulaması için, genellikle belirli bir yaklaşımın ne kadar güvenli olduğunu ve ne kadar kullanışlı olduğunu gösteren ters bir ilişki vardır.  

## <a name="installing-certificates-on-iot-devices"></a>IoT cihazlarına sertifika yükleme
IoT cihazlarınızın kimliğini doğrulamak için X. 509.952 sertifikalarını kullanırsanız, bu bölümde sertifikaların üretim sürecinizdeki tümleştirilmesine ilişkin yönergeler sunulmaktadır. Birkaç kararın olması gerekir.  Bunlar, ortak sertifika değişkenleriyle ilgili kararlar, ne zaman sertifika oluşturulacağı ve ne zaman yükleneceğini içerir. 

Parolaları kullanırken, tüm cihazlarınızda aynı parolayı kullanabilmeniz için, tüm cihazlarınızda aynı sertifikayı neden kullanamadığınızı sorun. bu şekilde, tüm cihazlarınızda aynı parolayı kullanabileceksiniz. İlk olarak, her yerde aynı parolanın kullanılması tehlikelidir. Bu uygulama, şirketi önemli DDoS saldırılarına maruz bıraktı. Bu, ABD Doğu Yakası birkaç yıl önce DNS 'i alan bir işlem. Kişisel hesaplar da dahil olmak üzere her yerde aynı parolayı kullanmayın. İkincisi, sertifika bir parola değil, benzersiz bir kimliktir. Parola, herkesin güvenli bir binada bir kapı açmak için kullanabileceği gizli bir kod gibidir.  Bu, bildiğiniz bir şeydir ve parolayı herhangi bir girişe, giriş kazanmasına izin verebilirsiniz.  Bir sertifika, fotoğrafınız için bir sürücünün lisansına ve güvenli bir oluşturmaya ulaşmak üzere bir koruyucuyu gösterebilmeniz gereken diğer ayrıntılara benzer. Sahip olduğunuz kim ile bağlantılıdır.  Koruma 'nın sürücü lisanslarına sahip kişilerle doğru bir şekilde eşleştiğinden, yalnızca giriş kazanmak için lisansınızı (kimlik) kullanabilirsiniz. 

### <a name="variables-involved-in-certificate-decisions"></a>Sertifika kararlarında yer alan değişkenler
Aşağıdaki değişkenleri ve her birinin genel üretim işlemini nasıl etkilediğini göz önünde bulundurun. 

#### <a name="where-the-certificate-root-of-trust-comes-from"></a>Güvenin sertifika kökünün nereden geldiği
Ortak anahtar altyapısını (PKI) yönetmek için maliyetli ve karmaşık olabilir.  Özellikle şirketinizde PKI yönetme deneyimi yoksa. Seçenekleriniz şunlardır:
- Üçüncü taraf PKI kullanın. Bir üçüncü taraf sertifika satıcısından ara imza sertifikaları satın alabilirsiniz. Ya da özel bir sertifika yetkilisi (CA) kullanabilirsiniz. 
- Kendi kendine yönetilen bir PKI kullanın. Kendi PKI sisteminizi koruyabilir ve kendi sertifikalarınızı oluşturabilirsiniz.
- [Azure Sphere](https://azure.microsoft.com/services/azure-sphere/) güvenlik hizmetini kullanın. Bu seçenek yalnızca Azure Sphere cihazları için geçerlidir. 

#### <a name="where-certificates-are-stored"></a>Sertifikaların depolandığı yer
Sertifikaların depolandığı kararı etkileyen birkaç etken vardır. Bu faktörler cihaz türünü, beklenen kâr marjlarını (güvenli depolama alanı için), cihaz yeteneklerini ve kullanabileceğiniz cihazda mevcut güvenlik teknolojisini içerir. Aşağıdaki seçenekleri göz önünde bulundurun:
- Bir donanım güvenlik modülünde (HSM). HSM kullanmak kesinlikle önerilir. Cihazınızın denetim panosunun zaten bir HSM 'nin yüklü olup olmadığını denetleyin. HSM 'niz yoksa, gereksinimlerinizi karşılayan bir HSM 'yi belirlemek için donanım üreticinize danışın.
- , Güvenilir bir yürütme ortamı (t) gibi bir diskte güvenli bir yerde.
- Yerel dosya sisteminde veya bir sertifika deposunda. Örneğin, Windows sertifika deposu. 

#### <a name="connectivity-at-the-factory"></a>Fabrikada bağlantı
Fabrikada bağlantı, cihazlara yüklenecek sertifikaların nasıl ve ne zaman alınacağını belirler. Bağlantı seçenekleri şunlardır:
- Bilirlik. Bağlantıların en iyi durumda olması, sertifikaları yerel olarak oluşturma sürecini kolaylaştırır. 
- Bağlantı yok. Bu durumda, cihaz sertifikalarını yerel olarak ve çevrimdışı olarak oluşturmak için bir CA 'dan imzalı bir sertifika kullanırsınız. 
- Bağlantı yok. Bu durumda, önceden oluşturulmuş sertifikaları elde edebilirsiniz. Alternatif olarak, sertifikaları yerel olarak oluşturmak için çevrimdışı bir PKI kullanabilirsiniz.

#### <a name="audit-requirement"></a>Denetim gereksinimi
Oluşturduğunuz cihazların türüne bağlı olarak, cihaz kimliklerinin cihazlarınıza nasıl yüklendiğine ilişkin bir denetim izi oluşturmaya yönelik bir düzenleme gereksinimine sahip olabilirsiniz. Denetim önemli üretim maliyeti ekler. Çoğu durumda, yalnızca gerekirse bunu yapın. Bir denetimin gerekli olup olmadığı konusunda emin değilseniz, şirketinizin yasal bölümüne bakın. Denetim seçenekleri şunlardır: 
- Hassas bir sektör değil. Denetim gerekli değildir.
- Hassas sektör. Sertifikaların, uyumluluk sertifikası gereksinimlerine göre güvenli bir odada yüklü olması gerekir. Sertifikaları yüklemek için güvenli bir oda olması gerekiyorsa, sertifikaların cihazlarınızda nasıl yüklendiğine ilişkin büyük olasılıkla zaten farkındaymış olursunuz. Ve muhtemelen zaten bir denetim sistemine sahipsiniz. 

#### <a name="length-of-certificate-validity"></a>Sertifika geçerliliği uzunluğu
Bir sürücünün lisansı gibi, sertifikaların oluşturulduğunda ayarlanmış bir sona erme tarihi vardır. Sertifika geçerliliği uzunluğu için seçenekler şunlardır:
- Yenileme gerekli değil.  Bu yaklaşım uzun bir yenileme süresi kullanır, bu nedenle cihazın ömrü boyunca sertifikayı yenilemenize gerek kalmaz. Böyle bir yaklaşım kullanışlı olsa da riskli olur.  Cihazlarınızda HSM gibi güvenli depolama alanını kullanarak riski azaltabilirsiniz. Ancak, uzun süreli sertifikaların kullanmaktan kaçınmak, önerilen uygulamadır.
- Yenileme gerekiyor.  Cihazın ömrü boyunca sertifikayı yenilemeniz gerekir. Sertifika geçerlilik süresinin uzunluğu bağlama göre değişir ve yenileme stratejisi gerekir.  Strateji, sertifikaları aldığınız yeri ve cihazlarınızın yenileme sürecinde kullanması gereken ne tür bir kablosuz işlevselliği içermelidir. 

### <a name="when-to-generate-certificates"></a>Sertifika oluşturma
Fabrikanızdaki Internet bağlantısı özellikleri, sertifika oluşturma sürecinizi etkiler. Sertifikaların ne zaman oluşturulacağı konusunda birkaç seçeneğiniz vardır: 

- Önceden yüklenmiş sertifikalar.  Bazı HSM satıcıları, HSM satıcısının müşteri için sertifika yüklediği bir Premium hizmet sunar. Birincisi, müşteriler HSM satıcısına bir imzalama sertifikası erişimi verir. Ardından, HSM satıcısı, bu imzalama sertifikası tarafından imzalanan sertifikaları, müşterinin satın aldığı her HSM 'ye yükler. Tüm müşteriler cihaza HSM 'yi yükler. Bu hizmet maliyet ekleirken, üretim sürecinizi kolaylaştırmaya yardımcı olur.  Ayrıca, sertifikaların ne zaman yükleneceğine ilişkin soruyu çözer.
- Cihaz tarafından oluşturulan sertifikalar.  Cihazlarınız dahili olarak sertifika üretemiyor, bunu DPS 'e kaydetmek için cihazdan ortak X. 509.952 sertifikasını ayıklamanız gerekir. 
- Bağlı fabrika.  Fabrikanızın bağlantısı varsa, her ihtiyaç duyduğunuzda cihaz sertifikaları oluşturabilirsiniz.
- Kendi PKI 'ınızla çevrimdışı fabrika. Fabrikanızın bağlantısı yoksa ve çevrimdışı destek ile kendi PKI 'nizi kullanıyorsanız, sertifikaları ihtiyacınız olduğunda oluşturabilirsiniz.
- Üçüncü taraf PKI ile çevrimdışı fabrika. Fabrikanızın bağlantısı yoksa ve üçüncü taraf bir PKI kullanıyorsanız, sertifikaları daha önce oluşturmanız gerekir. Ayrıca, bağlantısı olan bir konumdan sertifikaların oluşturulması gerekir. 

### <a name="when-to-install-certificates"></a>Sertifikalar ne zaman yüklenir
IoT cihazlarınız için Sertifika oluşturduktan sonra bunları cihazlara yükleyebilirsiniz. 

Önceden yüklenmiş sertifikaları bir HSM ile birlikte kullanıyorsanız, işlem basitleştirilir. HSM 'yi cihaza yükledikten sonra cihaz kodu buna erişebilir. Daha sonra HSM 'de depolanan sertifikaya erişmek için HSM API 'Lerini çağıracaksınız. Bu yaklaşım, üretim sürecinizi en kolay yöntemdir. 

Önceden yüklenmiş bir sertifika kullanmıyorsanız, sertifikayı üretim işleminizin bir parçası olarak yüklemelisiniz. En basit yaklaşım, sertifikayı HSM 'ye ilk üretici yazılımı görüntüsünü Flash ile aynı anda yüklemektir. İşlemin her cihaza görüntüyü yüklemek için bir adım eklemesi gerekir. Bu adımdan sonra, cihazı paketleyip teslim etmeden önce son kalite denetimlerini ve diğer adımları çalıştırabilirsiniz. 

Yükleme işlemini ve son kalite denetimini tek bir adımda çalıştırmanıza olanak tanıyan yazılım araçları mevcuttur. Bu araçları, bir sertifika oluşturmak veya önceden oluşturulmuş bir sertifika deposundan bir sertifika çekmek için değiştirebilirsiniz. Daha sonra yazılım, yüklemeniz gereken sertifikayı yükleyebilir. Bu türün yazılım araçları, üretim kalitesinde üretimi ölçekli olarak çalıştırmanızı sağlar. 

Cihazlarınızda sertifikalar yüklendikten sonra, bir sonraki adım cihazları [DPS](about-iot-dps.md)'e nasıl kaydedeceğinizi öğrenirsiniz. 

## <a name="integrating-a-tpm-into-the-manufacturing-process"></a>TPM 'YI üretim işlemiyle tümleştirme
IoT cihazlarınızın kimliğini doğrulamak için TPM kullanıyorsanız, bu bölümde rehberlik sunulmaktadır. Bu kılavuz, karma tabanlı ileti kimlik doğrulama kodu (HMAC) anahtar desteği olan yaygın olarak kullanılan TPM 2,0 cihazlarını içerir. TPM yongaları için TPM belirtimi, Trusted Computing Group tarafından tutulan bir ISO standardıdır. TPM hakkında daha fazla bilgi için bkz. [tpm 2,0](https://trustedcomputinggroup.org/tpm-library-specification/) ve [ıso/IEC 11889](https://www.iso.org/standard/66510.html)özellikleri. 

### <a name="taking-ownership-of-the-tpm"></a>TPM sahipliğini alma
TPM yongasıyla bir cihaz ürettiği için kritik bir adım TPM 'nin sahipliğini alma. Cihaz sahibine bir anahtar sağlayabilmeniz için bu adım gereklidir. İlk adım, cihazdaki onay anahtarını (EK) ayıklamaya yönelik olur. Sonraki adım gerçekten sahiplik talep etmek için kullanılır. Bu işlem, kullandığınız TPM ve işletim sistemine bağlıdır. Gerekirse, sahipliği nasıl talep etmek istediğinizi öğrenmek için TPM üreticisine veya cihaz işletim sistemi geliştiricisine başvurun. 

Üretim sürecinizdeki EK ve talep sahipliğini farklı zamanlarda ayıklayabilir ve bu da esneklik kazandırır. Birçok üretici, cihazlarının güvenliğini geliştirmek için bir donanım güvenlik modülü (HSM) ekleyerek bu esnekliğin avantajlarından yararlanır. Bu bölümde, EK ne zaman ayıklanacağı, TPM 'nin sahipliğinin ne zaman talep edilebileceğine ve bu adımların bir üretim zaman çizelgesine tümleştirilmesine yönelik hususlar sunulmaktadır. 

> [!IMPORTANT]
> Aşağıdaki kılavuzda ayrı, bellenim veya tümleşik TPM kullandığınız varsayılır. Bunun uygulanabilir olduğu yerlerde, kılavuz, ayrık olmayan veya yazılım TPM 'si kullanarak not ekler. Bir yazılım TPM 'si kullanırsanız, bu kılavuzun içermediği ek adımlar olabilir. Yazılım TPMs, bu makalenin kapsamı ötesinde çeşitli uygulamalar vardır.  Genel olarak, bir yazılım TPM 'sini aşağıdaki genel üretim zaman çizelgesine entegre etmek mümkündür. Ancak, bir yazılım öykünülmüş TPM prototip oluşturma ve test etme için uygun olsa da, ayrı, bellenim veya tümleşik TPM ile aynı güvenlik düzeyini sağlayamaz. Genel bir uygulama olarak, üretimde bir yazılım TPM 'si kullanmaktan kaçının.

### <a name="general-manufacturing-timeline"></a>Genel üretim zaman çizelgesi
Aşağıdaki zaman çizelgesinde bir TPM 'nin bir üretim işleminden nasıl gittiği ve bir cihazda nasıl bittiği gösterilmektedir. Her üretim işlemi benzersizdir ve bu zaman çizelgesi en sık kullanılan desenleri gösterir. Zaman çizelgesi, anahtarlarla ilgili bazı eylemlerin ne zaman ele sunulduğunu hakkında rehberlik sunar. 

#### <a name="step-1-tpm-is-manufactured"></a>1. Adım: TPM üretilir
- Cihazlarınızda kullanım için bir üreticiden TPMs satın alırsanız, sizin için genel onay anahtarlarını (EK_pubs) ayıklayadıklarından, bkz.. Üretici, sevk edilen cihazlarla EK_pubs listesini sağlıyorsa yararlı olur. 
    > [!NOTE]
    > Sağlama hizmetinizde paylaşılan erişim ilkelerini kullanarak kayıt listenize TPM üreticisine yazma erişimi verebilirsiniz.  Bu yaklaşım, sizin için kayıt listenize TPMs 'Leri ekleyebilmenizi sağlar.  Ancak bu, üretim sürecinde erken bir işlemdir ve TPM üreticisi için güven gerektirir. Bunu sizin sorumluluğunuzdadır. 

- Cihaz üreticilerine satış yapmak için TPM 'Ler oluşturursanız, müşterilerinize fiziksel Tpmlarıyla birlikte EK_pubs bir listesi vermeyi düşünün.  EK_pubs ile müşterilerin sağlanması, sürecinde bir adım kazandırır. 
- Kendi cihazlarınızla kullanmak üzere TPM 'Ler oluşturduysanız, EK_pub ayıklamak için işleminizin en uygun noktasını belirlemeniz gerekir. EK_pub, zaman çizelgesinde kalan noktaların herhangi birinde ayıklayabilirsiniz. 

#### <a name="step-2-tpm-is-installed-into-a-device"></a>2. Adım: TPM bir cihaza yüklenmiş
Üretim sürecinde bu noktada, cihazın hangi DPS örneğine kullanılacağını bilmeniz gerekir. Sonuç olarak, otomatik sağlama için kayıt listesine cihaz ekleyebilirsiniz. Otomatik cihaz sağlama hakkında daha fazla bilgi için bkz. [DPS belgeleri](about-iot-dps.md).
- EK_pub ayıkladıysanız, bunu yapmak için iyi bir zaman vardır. 
- TPM 'nin yükleme işlemine bağlı olarak, bu adım TPM 'nin sahipliğini almak için de iyi bir zamandır. 

#### <a name="step-3-device-has-firmware-and-software-installed"></a>3. Adım: cihazda üretici yazılımı ve yazılım yüklü
İşlemin bu noktasında, sağlama için DPS istemcisini KIMLIK kapsamı ve genel URL ile birlikte yüklemelisiniz.
- Şimdi EK_pub ayıklamaya yönelik son şansınız vardır. Üçüncü bir taraf yazılımı cihazınıza yükleyerde öncelikle EK_pub ayıklanmanız iyi bir fikirdir.
- Üretim işlemindeki bu nokta TPM sahipliğini almak için idealdir.  
    > [!NOTE]
    > Bir yazılım TPM 'si kullanıyorsanız, şimdi yükleyebilirsiniz.  EK_pub aynı zamanda ayıklayın.

#### <a name="step-4-device-is-packaged-and-sent-to-the-warehouse"></a>4. Adım: cihaz paketlenir ve ambara gönderilir
Bir cihaz, bir ambarın dağıtılmadan ve DPS ile sağlanmadan önce bir yıla kadar bir ambara ait olabilir.  Bir cihaz, dağıtımdan önce uzun bir süre boyunca mevcutsa, cihazı dağıtan müşterilerin üretici yazılımı, yazılım veya süresi dolduğunda kimlik bilgilerini güncelleştirmesi gerekebilir.

#### <a name="step-5-device-is-installed-into-the-location"></a>5. Adım: cihaz konuma yüklendi
Cihaz son konumuna ulaştıktan sonra, DPS ile otomatik sağlama aracılığıyla geçer.

Daha fazla bilgi için bkz. [sağlama](about-iot-dps.md#provisioning-process) ve [TPM kanıtlama](concepts-tpm-attestation.md). 

## <a name="resources"></a>Kaynaklar

Bu makaledeki önerilen güvenlik uygulamalarına ek olarak Azure IoT, güvenli donanım seçme ve güvenli IoT dağıtımları oluşturma konusunda yardımcı olacak kaynaklar sağlar: 
- Dağıtım sürecine kılavuzluk eden Azure IoT [güvenlik önerileri](../iot-fundamentals/security-recommendations.md) . 
- [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/) , güvenli IoT dağıtımları oluşturmaya yardımcı olmak için bir hizmet sunar. 
- Donanım ortamınızı değerlendirmede yardım için [IoT güveninizi değerlendirme](https://download.microsoft.com/download/D/3/9/D3948E3C-D5DC-474E-B22F-81BA8ED7A446/Evaluating_Your_IOT_Security_whitepaper_EN_US.pdf)teknik incelemesi bölümüne bakın. 
- Güvenli donanım seçme konusunda yardım için [IoT dağıtımınız Için doğru güvenli donanıma](https://download.microsoft.com/download/C/0/5/C05276D6-E602-4BB1-98A4-C29C88E57566/The_right_secure_hardware_for_your_IoT_deployment_EN_US.pdf)bakın. 