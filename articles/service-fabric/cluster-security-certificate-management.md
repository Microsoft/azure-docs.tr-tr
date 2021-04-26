---
title: Service Fabric kümesinde sertifika yönetimi
description: X. 509.440 sertifikalarıyla güvenliği sağlanmış bir Service Fabric kümesindeki sertifikaları yönetme hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 04/10/2020
ms.custom: sfrev
ms.openlocfilehash: 7976d1419aeb0dda3ec2f94a32e9b185a6c14be7
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304877"
---
# <a name="certificate-management-in-service-fabric-clusters"></a>Service Fabric kümelerinde sertifika yönetimi

Bu makale, Azure Service Fabric kümelerinde iletişimin güvenliğini sağlamak için kullanılan sertifikaların yönetim yönlerini ele alır. Ayrıca, Service Fabric açıklama on [X. 509.440 sertifika tabanlı kimlik doğrulaması](cluster-security-certificates.md)için de [Service Fabric küme güvenliğine](service-fabric-cluster-security.md) giriş tamamlar. Okuyucunun temel güvenlik kavramlarını öğrendiğini ve Service Fabric bir kümenin güvenliğini yapılandırmak için sunduğu denetimlerle ilgili olduğunu varsayalım.  

Bu başlık altında yer almayan yönleri:

* ' Sertifika yönetimi ' tam olarak nedir?
* Sertifika yönetimi ile ilgili roller ve varlıklar
* Bir sertifikanın yolculuğu
* Bir örneğe derinlemesine bakış
* Sorun giderme ve sık sorulan sorular

Ancak ilk bir vazgeçme belgesi: Bu makale, teorik yan yana, hizmet, teknoloji ve diğer özellikleri gerektiren uygulamalı örneklerle eşleştirmeye çalışır. Hedef kitle 'in boyutlandırılabilir bir parçası Microsoft tarafından dahili olduğundan, Microsoft Azure özgü hizmetler, teknolojiler ve ürünlere başvuracağız. Microsoft 'a özgü ayrıntıların sizin için uygun olmadığı açıklığa bilirlikten veya kılavuzunuza ilişkin açıklamalar bölümünde sorun.

## <a name="defining-certificate-management"></a>Sertifika yönetimini tanımlama
[Eşlik eden makalede](cluster-security-certificates.md)gördüğümüz bir sertifika, bir şifreleme nesnesidir ve bu, temsil ettiği varlığı açıklayan özniteliklerle asimetrik bir anahtar çifti bağlamasıdır. Ancak, yaşam süresinin sonlu olması ve çok daha fazla açığa çıkmasına açık olması veya başarılı bir yararlanma işleminin bir sertifikayı güvenlik açısından gereksiz şekilde işleyebilmesi için de bir ' Perishable ' nesnesi vardır. Bu, sertifikaları değiştirme ihtiyacını (düzenli olarak veya bir güvenlik olayına yanıt olarak) gösterir. Yönetimin bir başka yönü (ve kendi konusunun tamamı), sertifika özel anahtarlarının korunmasından veya sertifikaların hazırlanması ve sağlanması ile ilgili varlıkların kimliklerini koruyan gizli bir konudur. Sertifikaları almak için kullanılan işlemlere ve yordamlara başvurduk ve bunları güvenli bir şekilde (ve güvenli bir şekilde) ' sertifika yönetimi ' olarak ihtiyaç duydukları yere taşıdık. Kayıt, ilke ayarı ve yetkilendirme denetimleri gibi bazı yönetim işlemleri bu makalenin kapsamı dışındadır. Hala sağlama, yenileme, yeniden anahtarlama veya iptal etme gibi diğerleri, yalnızca Service Fabric ilgili arada; Nonetheless, bu işlemleri anlamak bir ölçüde daha adresliyoruz. bu işlemler, bir kümenin bir kümesini doğru şekilde güvenli hale getirmenize yardımcı olabilir. 

Amaç, kümenin kesintisiz kullanılabilirliğini sağlamak ve bu işlemin Kullanıcı dokunmadan ücretsiz olması nedeniyle güvenlik kesintileri sunmak için, sertifika yönetimini mümkün olduğunca olanaklı hale getirmektir. Bu hedefe Şu anda Azure Service Fabric kümelerinde ulaşılabilir. makalenin geri kalanı önce sertifika yönetimini kaldırır, daha sonra da oto rollover 'ı etkinleştirmeye odaklanacaktır.

Özellikle, kapsamdaki konular şunlardır:
  - sertifikaları yönetme bağlamında, sahip ve platformu arasındaki attribuayrımla ilgili varsayımlar
  - sertifikaların tüketimine verilmesinde uzun işlem hattı
  - Sertifika döndürme-neden, nasıl ve ne zaman
  - yanlış bir sorun olabilir mi?

Etki alanı adlarını güvenli hale getirme/yönetme, sertifikalara kaydetme veya sertifika verilmesini zorlamak için yetkilendirme denetimlerini ayarlama gibi bir görünüm, bu makalenin kapsamı dışındadır. En sevdiğiniz ortak anahtar altyapısı (PKI) hizmetinizin kayıt yetkilisine (RA) bakın. Microsoft-iç tüketiciler: lütfen Azure güvenliğine ulaşın.

## <a name="roles-and-entities-involved-in-certificate-management"></a>Sertifika yönetimi ile ilgili roller ve varlıklar
Bir Service Fabric kümesindeki güvenlik yaklaşımı, "küme sahibi bunu bildiriyor, Service Fabric Runtime onu zorluyor" durumdur. Bu, bir kümenin çalışmasına katılan sertifikaların, anahtarların veya diğer kimlik bilgilerinin neredeyse hiçbirinin hizmetten geldiği anlamına gelir; Bunların hepsi küme sahibi tarafından bildirilmiştir. Ayrıca, küme sahibi Ayrıca, sertifikaların kümeye sağlanması, gerektiğinde yenilenmesi ve sertifikaların her zaman güvenliğini sağlamak için de sorumludur. Daha belirgin olarak, küme sahibi şunları sağlamalıdır:
  - küme bildiriminin NodeType bölümünde belirtilen sertifikalar, [sunu kurallarına](cluster-security-certificates.md#presentation-rules) göre ilgili türdeki her bir düğümde bulunabilir
  - Yukarıda belirtilen sertifikalar, bunlara karşılık gelen özel anahtarlar dahil yüklenir
  - sunu kurallarında belirtilen sertifikaların [doğrulama kuralları](cluster-security-certificates.md#validation-rules) geçmesi gerekir 

Service Fabric, bölümü için şu sorumlulukları varsayar:
  - küme tanımındaki bildirimlerle eşleşen sertifikaları bulma/bulma  
  - ' gereksinim ' temelinde Service Fabric denetimli varlıklara karşılık gelen özel anahtarlara erişim verme
  - en iyi güvenlik uygulamaları ve küme tanımıyla sertifikaları sıkı şekilde doğrulama
  - Sertifikaların kullanım süresi dolduktan veya sertifika doğrulamasının temel adımlarını gerçekleştirme hatalarıyla ilgili uyarılar oluşturma
  - küme tanımının sertifikayla ilgili yönlerinin, ana bilgisayarların temel yapılandırmasıyla karşılandığını doğrulama (bir dereceye kadar) 

Sertifika yönetimi yükü (etkin işlemler olarak) yalnızca küme sahibine denk gelir. Aşağıdaki bölümlerde, kullanılabilir mekanizmaların ve küme üzerindeki etkilerden her bir yönetim işlemine daha yakından bakacağız.

## <a name="the-journey-of-a-certificate"></a>Bir sertifikanın yolculuğu
Bir Service Fabric kümesi bağlamında, bir sertifikanın verilme durumunu hızla yeniden ziyaret etmemizi sağlayın:

  1. Bir etki alanı sahibi, bir PKI 'nın RA ile bir etki alanı veya konuyu, sertifikalarla ilişkilendirmek istedikleri bir etki alanına kaydeder; Sertifikalar, buna karşılık gelen etki alanı veya ilgilinin sahipliğinin provalarını oluşturur
  2. Etki alanı sahibi Ayrıca, belirtilen etki alanı veya konuyla sertifikaların kaydedilmesini istemek üzere yetkili istekçilerin kimliklerini RA olarak belirler. Microsoft Azure, varsayılan kimlik sağlayıcısı Azure Active Directory ve yetkili istekleyiciler karşılık gelen AAD kimliği (veya güvenlik grupları aracılığıyla) tarafından atanır
  3. Yetkili bir istek sahibi daha sonra gizli bir yönetim hizmeti üzerinden bir sertifikaya kaydolur; Microsoft Azure, tercih edilen SMS, yetkili varlıklar tarafından güvenli bir şekilde depolanan ve parolaların/sertifikaların alınmasına izin veren Azure Key Vault (AKV). Ayrıca, aynı zamanda sertifikayı ilişkili sertifika ilkesinde yapılandırılan şekilde yeniler/yeniden anahtarlayın. (AKV, kimlik sağlayıcısı olarak AAD 'yi kullanır.)
  4. ' Sağlama Aracısı ' olarak adlandırdığımız yetkili bir Retriever-sertifikayı kendi özel anahtarı dahil, kasadan alır ve kümeyi barındıran makinelere kurar
  5. Service Fabric hizmeti (her düğüm üzerinde yükseltilmiş olarak çalışır), sertifikaya izin verilen Service Fabric varlıklara erişim verir; Bunlar yerel gruplar tarafından belirlenir ve ServiceFabricAdministrators ile ServiceFabricAllowedUsers arasında bölünür
  6. Service Fabric çalışma zamanı, federasyon kurmak veya yetkili istemcilerden gelen isteklerin kimliğini doğrulamak için sertifikayı erişir ve kullanır
  7. Sağlama Aracısı kasa sertifikasını izler ve yenilemeyi tespit etmek için sağlama akışını tetikler; daha sonra, küme sahibi, gerekirse, sertifikayı alma amacını belirtmek için küme tanımı 'nı güncelleştirir.
  8. Sağlama Aracısı veya küme sahibi, kullanılmayan sertifikaları Temizleme/silme işleminden da sorumludur
  
Amaçlarımız için yukarıdaki dizideki ilk iki adım büyük ölçüde ilgisiz değildir; tek bağlantı, sertifikanın konu ortak adının, küme tanımında belirtilen DNS adıdır.

Bu adımlar aşağıda gösterilmiştir; sırasıyla parmak izi ve ortak ad tarafından belirtilen Sertifikalar arasında sağlama farklarını göz önünde bir şekilde aklınızda olun.

*Şekil 1.* Parmak iziyle belirtilen sertifikaların verme ve sağlama akışı.
![Parmak iziyle belirtilen sertifikaları sağlama][Image1]

*Fig. 2.* Konu ortak adına göre belirtilen sertifikaların verme ve sağlama akışı.
![Konu ortak adına göre belirtilen sertifikaları sağlama][Image2]

### <a name="certificate-enrollment"></a>Sertifika kaydı
Bu konu, Key Vault [belgelerinde](../key-vault/certificates/create-certificate.md)ayrıntılı olarak ele alınmıştır. sürekliliği ve daha kolay başvuru için buraya bir özeti dahil ediyoruz. Bağlam olarak Azure 'a devam ederek ve gizli yönetim hizmeti olarak Azure Key Vault kullanarak, yetkili bir sertifika isteyenin kasa üzerinde en az sertifika yönetim izinlerine sahip olması gerekir; örneğin, kasa sahibi tarafından verilir; istek sahibi daha sonra aşağıdaki gibi bir sertifikaya kaydolur:
    - sertifika etki alanı/konu, istenen veren, anahtar türü ve uzunluğu, hedeflenen anahtar kullanımı ve daha fazlasını belirten Azure Key Vault (AKV) içinde bir sertifika ilkesi oluşturur. Ayrıntılar için [Azure Key Vault Sertifikalar '](../key-vault/certificates/certificate-scenarios.md) a bakın. 
    - Yukarıda belirtilen ilkeyle aynı kasada bir sertifika oluşturur; Buna karşılık, kasa nesneleri olarak bir anahtar çifti, özel anahtarla imzalanmış bir sertifika imzalama isteği ve daha sonra imzalama için belirtilen verene iletilir
    - veren (sertifika yetkilisi) imzalı sertifikayla yanıt verdiğinde, sonuç kasayla birleştirilir ve sertifika aşağıdaki işlemler için kullanılabilir:
      - {vaultUri}/Certificates/{Name} altında: ortak anahtar ve meta verileri içeren sertifika
      - {vaultUri}/Keys/{Name} altında: sertifikanın özel anahtarı şifreleme işlemleri için kullanılabilir (sarmalama/sarmalama, imzala/doğrula)
      - {vaultUri}/Secrets/{Name} altında: özel anahtarı dahil, korumasız bir PFX veya pem dosyası olarak indirmek için kullanılabilir  
    Bir kasa sertifikasının, aslında kronolojik bir sertifika örnekleri, bir ilkeyi paylaşılarak olduğunu unutmayın. Sertifika sürümleri, ilkenin ömür ve yenileme özniteliklerine göre oluşturulacaktır. Kasa sertifikalarının konuları veya etki alanlarını/DNS adlarını paylaşmadığından kesinlikle önerilir; farklı kasalardan, aynı konularla, ancak veren, anahtar kullanımları vb. gibi farklı diğer özniteliklerle sertifika örnekleri sağlamak için bir kümede karışıklığa yol açabilir.

Bu noktada kasada bir sertifika mevcuttur, tüketim için hazırlanın. Onward:

### <a name="certificate-provisioning"></a>Sertifika sağlama
Özel anahtarı dahil olmak üzere kasadan, sertifikayı alan ve kümenin ana bilgisayarlarının her birine yükleyen bir varlık olan ' sağlama Aracısı ' ' nı belirttik. (Service Fabric sertifika sağlamadıkları için çağırır.) Bağlamımızda küme, Azure VM 'Leri ve/veya sanal makine ölçek kümeleri koleksiyonunda barındırılacak. Azure 'da, bir kasadan VM/VMSS 'ye bir sertifika sağlamak, yukarıdaki şekilde, sağlama aracısının kasa sahibine göre kasada daha önce ' Al ' izinleri verildiğini varsayarak, aşağıdaki mekanizmalarla elde edilebilir: 
  - geçici: bir operatör sertifikayı kasadan alır (PFX/PKCS #12 veya ped olarak) ve her bir düğüme yüklenir
  - dağıtım sırasında bir sanal makine ölçek kümesi olarak ' gizli ': Işlem hizmeti, işleç adına ilk taraf kimliğini kullanarak, şablon dağıtımı etkinleştirilmiş bir kasadan alınan sertifikaya ve sanal makine ölçek kümesinin her bir düğümüne ([bunun gibi](/virtual-machine-scale-sets/virtual-machine-scale-sets-faq.yml#certificates)) yüklerse, Bu, yalnızca sürümlenmiş parolaların sağlanmasına izin verir
  - [Key Vault VM uzantısını](../virtual-machines/extensions/key-vault-windows.md)kullanma; Bu, gözlemlenen sertifikaların düzenli olarak yenilenmesi için, sürüm daha az bildirimleri kullanarak sertifikaların sağlanmasına olanak tanır. Bu durumda, VM/VMSS 'nin, gözlemlenen sertifikaları içeren kasaya erişim izni verilen bir kimlik olan [yönetilen bir kimliğe](/virtual-machines/security-policy.md#managed-identities-for-azure-resources)sahip olması beklenir.

Geçici mekanizmanın birden çok nedenden dolayı kullanılması önerilmez, güvenliğin kullanılabilirliğine ve burada açıklanmayacaktır; Ayrıntılar için bkz. [sanal makine ölçek kümelerinde sertifikalara](/virtual-machine-scale-sets/virtual-machine-scale-sets-faq.yml#certificates)bakın.

VMSS-/COMPUTE-based sağlama güvenlik ve kullanılabilirlik avantajları sunar, ancak aynı zamanda kısıtlamaları da sunar. Bu, yalnızca parmak izi tarafından tanımlanan sertifikalarla güvenliği sağlanan kümeler için uygun hale getiren, tasarım kaynaklı gizli dizileri olarak bir sertifika bildirimi gerektirir. Buna karşılık, Key Vault VM Uzantısı tabanlı sağlama, her bir gözlemlenen sertifikanın en son sürümünü her zaman yükler. Bu, yalnızca konu ortak adı tarafından tanımlanan sertifikalarla güvenliği sağlanmış kümeler için uygun hale getirir. Vurgulamak için, örnekle (yani, parmak iziyle) belirtilen sertifikalar için bir oto yenileme sağlama mekanizması (KVVM uzantısı gibi) kullanmayın-kullanılabilirliği kaybetme riski önemli ölçüde olur.

Diğer sağlama mekanizmaları var olabilir; Yukarıdaki şu anda Azure Service Fabric kümeleri için kabul edilmiştir.

### <a name="certificate-consumption-and-monitoring"></a>Sertifika tüketimi ve izleme
Daha önce belirtildiği gibi, Service Fabric çalışma zamanı, küme tanımında bildirilen sertifikaların yerini bulmada ve kullanmaktan sorumludur. [Sertifika tabanlı kimlik doğrulaması](cluster-security-certificates.md) hakkında, Service Fabric, sırasıyla sunumu ve doğrulama kurallarını nasıl uyguladığını ayrıntılı olarak açıklanan ve burada yeniden ziyaret etmeyeceği makalede açıklanmıştır. Erişim ve izin vermeyi ve izlemeyi inceleyeceğiz.

Service Fabric içindeki sertifikaların, Federasyon katmanındaki karşılıklı kimlik doğrulamasından yönetim uç noktaları için TLS kimlik doğrulamasına kadar çok sayıda amaçla kullanıldığını hatırlayın; Bu, sertifikanın özel anahtarına farklı bileşenlerin veya sistem hizmetlerinin erişmesini gerektirir. Service Fabric çalışma zamanı, sertifika deposunu düzenli olarak tarar ve bilinen sunum kurallarından her biri için eşleşmeleri arar; eşleşen sertifikaların her biri için, karşılık gelen özel anahtar bulunur ve isteğe bağlı erişim denetimi listesi, izinleri (genellikle okuma ve yürütme), kendilerine gereken kimliğe izin vermek üzere güncelleştirilir. (Bu işlem ' ACLing ' olarak adlandırılır.) İşlem 1 dakikalık bir temposunda üzerinde çalışır ve ayrıca ayarları şifrelemek için kullanılanlar veya uç nokta sertifikaları gibi ' uygulama ' sertifikalarını da içerir. ACLing, Sunu kurallarını izler, bu nedenle, parmak izi tarafından tanımlanan ve küme yapılandırması güncelleştirmesi olmadan yeniden yenilenen sertifikaların erişilebilir olmaması gerektiğini aklınızda bulundurmanız önemlidir.    

### <a name="certificate-rotation"></a>Sertifika döndürme
Bir yan not olarak: IETF [RFC 3647](https://tools.ietf.org/html/rfc3647) resmi bir sertifikayı değiştirilmekte olan sertifikayla aynı özniteliklere sahip bir sertifika verme [olarak tanımlar;](https://tools.ietf.org/html/rfc3647#section-4.4.6) veren, konunun ortak anahtarı ve bilgileri korunur ve sertifikayı yeni bir anahtar çiftiyle birlikte verme işlemi için [yeniden anahtarlama](https://tools.ietf.org/html/rfc3647#section-4.4.7) ve veren 'in değişebilir olup olmadığı konusunda kısıtlama yoktur. Ayrım için önemli olabilir (veren sabitleme ile konu ortak adı tarafından belirtilen sertifikaların durumunu göz önünde bulundurun), her iki senaryoyu da kapsayan ' döndürme ' nötr terimini kabul edeceğiz. (Satır içi olarak kullanıldığında, ' yenilemesinin ' ne olduğunu, aslında yeniden anahtarlama için) göz önünde bulundurun.) 

Azure Key Vault, otomatik sertifika döndürmeyi desteklediğine daha önce gördük: sertifikayı ilişkilendir ilkesi, sertifikanın kasada döndürüldüğü zaman aşımı süresi veya toplam yaşam süresinin yüzdesi ' ne kadar günler tanımlar. Bu yeni sertifikayı kümenin tüm düğümlerine dağıtmak için, sağlama aracısının bu tarihten sonra ve şimdi önceki sertifikanın süresi dolduktan önce çağrılması gerekir. Service Fabric, bir sertifikanın sona erme tarihi (ve kümede kullanımda olan) önceden belirlenmiş bir aralıktan daha önce gerçekleştiğinde sistem durumu uyarılarını vererek yardımcı olur. Kasa sertifikasını gözlemleyecek şekilde yapılandırılan bir otomatik sağlama Aracısı (yani, Anahtar Kasası VM uzantısı), kasayı düzenli olarak yokladığında, döndürmeyi tespit eder ve yeni sertifikayı alıp yükler. VM/VMSS ' gizli dizileri ' özelliği aracılığıyla yapılan sağlama, VM/VMSS 'yi yeni sertifikaya karşılık gelen sürümlü Anahtar Kasası URI 'SI ile güncelleştirmek için yetkilendirilmiş bir operatör gerektirir.

Her iki durumda da, döndürülen sertifika artık tüm düğümlere sağlanır ve bu mekanizmayı tespit etmek için Service Fabric mekanizmasını açıklıyoruz; daha sonra ne olduğunu inceleyeceğiz. Bu, bir sonraki deyişle, bir konu ortak adına göre belirtilen küme sertifikasına uygulanan döndürme
  - içindeki yeni bağlantılarda ve küme içinde, Service Fabric çalışma zamanı en son verilen eşleşen sertifikayı bulup seçer (' NotBefore ' özelliğinin en büyük değeri). Bu, Service Fabric çalışma zamanının önceki sürümlerinden bir değişiklik olduğunu göz önünde.
  - Mevcut bağlantılar canlı tutulacak/doğal olarak sona erecek veya sonlanmayacak; bir iç işleyici, yeni bir eşleşme olduğu bildirildi

> [!NOTE] 
> Sürüm 7.2.445 (7,2 CU4) öncesinde, en fazla süresi dolan sertifikayı Service Fabric (en uzak ' NotAfter ' özelliğine sahip sertifika) seçildi

Bu, aşağıdaki önemli gözlemleri çevirir:
  - Son kullanma tarihi Şu anda kullanılmakta olan sertifikadan daha önce ise, yenileme sertifikası yoksayılabilir.
  - Kümenin kullanılabilirliği veya barındırılan uygulamalar, sertifikayı döndürme yönergesinden önceliklidir; küme, yeni sertifikaya sonunda, zamanlama garantisi olmadan yakınsama yapılır. Şu şekildedir:
  - Döndürülen sertifikanın öncülünün tamamen değiştirildiğini bir gözlemci için hemen açık olmayabilir; Bunun (küme sertifikaları için) konak makinelerin yeniden başlatılmasını sağlamanın tek yolu. Not, bir kümede kira bağlantıları oluşturan çekirdek modu bileşenleri etkilenmedikleri için Service Fabric düğümlerini yeniden başlatmak yeterli değildir. Ayrıca, VM/VMSS 'nin yeniden başlatılması geçici kullanılabilirlik kaybına neden olabileceğini unutmayın. (Uygulama sertifikaları için, yalnızca ilgili uygulama örneklerinin yeniden başlatılması yeterlidir.)
  - Doğrulama kurallarını karşılamayan bir yeniden anahtarlı sertifikaya giriş, kümeyi etkin bir şekilde kesebilir. Bunun en yaygın örneği, beklenmeyen bir veren örneğidir: küme sertifikaları, veren sabitleme ile konu ortak adı tarafından belirtilir, ancak döndürülen sertifika yeni/bildirilmemiş bir veren tarafından verilir.     

### <a name="certificate-cleanup"></a>Sertifika Temizleme
Şu anda, sertifikaların açık bir şekilde kaldırılması için Azure 'da hiçbir sağlama yoktur. Belirli bir zamanda belirli bir sertifikanın kullanılıp kullanılmadığını belirlemede genellikle önemsiz olmayan bir görevdir. Bu, küme sertifikalarındaki uygulama sertifikaları için daha zordur. Service Fabric kendisini sağlama Aracısı değil, herhangi bir koşulda Kullanıcı tarafından tanımlanan bir sertifikayı silmeyecektir. Standart sağlama mekanizmaları için:
  - VM/VMSS gizli dizisi olarak belirtilen sertifikalar, VM/VMSS tanımında başvurulduğu ve kasadan geri alındığı sürece sağlanır (bir kasa gizli/sertifikası silindiğinde sonraki VM/VMSS dağıtımları başarısız olur; benzer şekilde, kasadaki bir gizli sürümü devre dışı bırakmak VM/VMSS dağıtımlarının de başarısız olmasına neden olur ve bu da gizli sürüme başvurur)
  - Anahtar Kasası VM Uzantısı aracılığıyla sağlanan sertifikaların önceki sürümleri VM/VMSS düğümünde bulunmayabilir veya bulunmayabilir. Aracı yalnızca geçerli sürümü alır ve kurar ve herhangi bir sertifikayı kaldırmaz. Bir düğümü yeniden Imaging (genellikle her ay oluşur), sertifika deposunu işletim sistemi görüntüsünün içeriğine sıfırlayacaktır ve bu nedenle önceki sürümler örtük olarak kaldırılır. Ancak, bir sanal makine ölçek kümesinin ölçeklendirilmesi yalnızca güncel gözlemlenen sertifika sürümlerinin yüklü olmasına yol açacak şekilde göz önünde bulundurun. yüklü sertifikalarla ilgili olarak düğümlerin hogeneliğini varsaymayın.   

## <a name="simplifying-management---an-autorollover-example"></a>Yönetimi basitleştirme-bir oto rollover örneği
Mekanizmaları, kısıtlamaları, ana hatlarıyla açıklanan kuralları ve tanımları açıkladık ve kesintileri d tahminleri yaptı. Belki de bu kaygıları önlemek için otomatik sertifika yönetiminin nasıl ayarlanacağını gösteren bir zaman. Bu işlemi, PaaSv2 sanal makine ölçek kümesi üzerinde çalışan bir Azure Service Fabric kümesi bağlamında, gizli dizi yönetimi için Azure Key Vault ve yönetilen kimlikleri kullanarak şu şekilde yaptık:
  - Sertifikaların doğrulanması, parmak izden, konuya ve veren sabitleme 'e sabitlemeye dönüştürülür: belirli bir veren tarafından verilen bir konuya sahip olan herhangi bir sertifika eşit olarak güvenilirdir
    - Sertifikalar, güvenilir bir mağazadan (Key Vault) kaydedilir ve bir aracı tarafından yenilenir (Bu durumda, Anahtar Kasası VM uzantısı)
    - Sertifikaların sağlanması dağıtım sonrası ve sürüm tabanlı (ComputeRP tarafından yapılır), dağıtım sonrası ve sürüm-daha seyrek Anahtar Kasası URI 'Leri kullanılarak değiştirilir
    - Anahtar kasasına erişim, Kullanıcı tarafından atanan Yönetilen kimlikler aracılığıyla verilir; UA kimliği, dağıtım sırasında oluşturulur ve sanal makine ölçek kümesine atanır
    - Dağıtımdan sonra, aracı (KV VM uzantısı), sanal makine ölçek kümesinin her bir düğümündeki gözlemlenen sertifikaları yoklayacak ve yenileyecek; BT, en uzak geçerli sertifikayı otomatik olarak alacak şekilde, bu sayede sertifika döndürme tamamen otomatikleştirilir.

Dizi tamamen betik/otomatik hale getirilebilir ve sertifika otomatik geçişi için yapılandırılmış bir kümenin Kullanıcı dokunmadan bir başlangıç dağıtımına izin verir. Ayrıntılı adımlar aşağıda verilmiştir. PowerShell cmdlet 'lerinin bir karışımını ve JSON şablonlarının parçalarını kullanacağız. Aynı işlevsellik, Azure ile etkileşime geçen tüm desteklenen yollarla ulaşılabilir.

> [!NOTE]
> Bu örnek, bir sertifikanın zaten kasada bulunduğunu varsayar; Anahtar Kasası tarafından yönetilen bir sertifikayı kaydetme ve yenileme, bu makalenin önceki kısımlarında açıklandığı gibi önkoşul adımlarını gerektirir. Üretim ortamları için, Anahtar Kasası tarafından yönetilen sertifikaları kullanın-Microsoft-iç PKI 'ya özgü bir örnek betik aşağıda verilmiştir.

> [!NOTE]
> Sertifika oto geçişi yalnızca CA tarafından verilen sertifikalar için anlamlı olur; Azure portal bir Service Fabric kümesi dağıtımında oluşturulanlar dahil olmak üzere otomatik olarak imzalanan sertifikalar kullanmak, verenin parmak izini yaprak sertifikayla aynı olacak şekilde bildirerek, yerel/geliştirici tarafından barındırılan dağıtımlar için yine de mümkündür.

### <a name="starting-point"></a>Başlangıç noktası
Kısaltma için, şu başlangıç durumunu varsayacağız:
  - Service Fabric kümesi var ve parmak izi tarafından belirtilen CA tarafından verilen bir sertifika ile korunmaktadır.
  - Sertifika bir kasada depolanıyor ve sanal makine ölçek kümesi parolası olarak sağlandı
  - Aynı sertifika, kümeyi ortak ad tabanlı sertifika bildirimlerine dönüştürmek için kullanılır ve bu nedenle konuya ve veren tarafından doğrulanabilir; Böyle bir durum söz konusu değilse, bu amaçla tasarlanan CA tarafından verilen sertifikayı alın ve [burada](service-fabric-cluster-security-update-certs-azure.md) açıklandığı gibi parmak izine göre küme tanımına ekleyin

Bu, böyle bir duruma karşılık gelen bir şablondan alınan bir JSON alıntısıdır. Bu, gereken birçok ayarı atlar ve yalnızca sertifikayla ilgili yönleri gösterir:
```json
  "resources": [
    {   ## VMSS definition
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeTypeName')]",
      "location": "[variables('computeLocation')]",
      "properties": {
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
            {
                "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
                "properties": {
                  "type": "ServiceFabricNode",
                  "autoUpgradeMinorVersion": true,
                  "publisher": "Microsoft.Azure.ServiceFabric",
                  "settings": {
                    "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                    "nodeTypeRef": "[variables('vmNodeTypeName')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "certificate": {
                        "thumbprint": "[parameters('primaryClusterCertificateTP')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.1"
                }
            },}},
          "osProfile": {
            "adminPassword": "[parameters('adminPassword')]",
            "adminUsername": "[parameters('adminUsername')]",
            "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
                },
                "vaultCertificates": [
                {
                    "certificateStore": "[parameters('certificateStoreValue')]",
                    "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
                },
            ]}]
        },
    },
    {   ## cluster definition
        "apiVersion": "[variables('sfrpApiVersion')]",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "certificate": {
            "thumbprint": "[parameters('primaryClusterCertificateTP')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
    }
  ]
```   

Yukarıdaki temel olarak, parmak izine sahip ```json [parameters('primaryClusterCertificateTP')] ``` ve anahtar KASASı URI 'sinde bulunan sertifikanın ```json [parameters('clusterCertificateUrlValue')] ``` , parmak izine göre kümenin tek sertifikası olarak bildirildiği belirtilir. Daha sonra, sertifikanın tekrar geçişine olanak sağlamak için gereken ek kaynakları ayarlayacağız.

### <a name="setting-up-prerequisite-resources"></a>Önkoşul kaynaklarını ayarlama
Daha önce bahsedildiği gibi, sanal makine ölçek kümesi gizli dizisi olarak sağlanan bir sertifika, ilk taraf kimliği ve dağıtım operatörü adına kullanılarak Microsoft. COMPUTE kaynak sağlayıcısı hizmeti tarafından kasadan alınır. Değiştirilecek olan oto geçişi için, sanal makine ölçek kümesine atanan ve kasaların gizli dizileri için izin verilen yönetilen bir kimlik ile geçiş yapacağız.

Sonraki tüm alıntıları 'ler güvenle dağıtılmalıdır; oynatma, yürütme ve açıklamalar için tek tek listelenir.

Önce Kullanıcı tarafından atanan bir kimlik tanımlayın (varsayılan değerler örnek olarak dahil edilir)-güncel bilgiler için [resmi belgelere](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity) başvurun:
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "userAssignedIdentityName": {
      "type": "string",
      "defaultValue": "sftstuaicus",
      "metadata": {
        "description": "User-assigned managed identity name"
      }
    },
  },
  "variables": {
      "vmssApiVersion": "2018-06-01",
      "sfrpApiVersion": "2018-02-01",
      "miApiVersion": "2018-11-30",
      "kvApiVersion": "2018-02-14",
      "userAssignedIdentityResourceId": "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
  },    
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('userAssignedIdentityName')]",
      "apiVersion": "[variables('miApiVersion')]",
      "location": "[resourceGroup().location]"
    },
  ]}
```

Daha sonra bu kimliğe kasa gizliliklerinde erişim izni verin-geçerli bilgiler için [resmi belgelere](/rest/api/keyvault/vaults/updateaccesspolicy) bakın:
```json
  "resources":
  [{
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "[variables('kvApiVersion')]",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).tenantId]",
            "objectId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).principalId]",
            "dependsOn": [
              "[variables('userAssignedIdentityResourceId')]"
            ],
            "permissions": {
              "secrets": [
                "get",
                "list"
              ]}}]}}]
```

Bir sonraki adımda şunları yapacağız:
  - Kullanıcı tarafından atanan kimliği sanal makine ölçek kümesine ata
  - yönetilen kimliğin oluşturulmasına ve kasaya erişim verme sonucuna sanal makine ölçek kümesi bağımlılığını bildirin
  - Anahtar Kasası VM uzantısını bildirmek için, başlangıçta gözlemlenen sertifikaları alması gerekir ([resmi belgeler](../virtual-machines/extensions/key-vault-windows.md))
  - Service Fabric VM uzantısının tanımını KVVM uzantısına göre güncelleştirin ve küme sertifikasını ortak ada dönüştürmek için (aynı kaynağın kapsamı altında olduğundan, bu değişiklikleri tek bir adımda yapıyoruz.)

```json
  "parameters": {
    "kvvmextPollingInterval": {
      "type": "string",
      "defaultValue": "3600",
      "metadata": {
        "description": "kv vm extension polling interval in seconds"
      }
    },
    "kvvmextLocalStoreName": {
      "type": "string",
      "defaultValue": "MY",
      "metadata": {
        "description": "kv vm extension local store name"
      }
    },
    "kvvmextLocalStoreLocation": {
      "type": "string",
      "defaultValue": "LocalMachine",
      "metadata": {
        "description": "kv vm extension local store location"
      }
    },
    "kvvmextObservedCertificates": {
      "type": "array",
      "defaultValue": [
                "https://sftestcus.vault.azure.net/secrets/sftstcncluster",
                "https://sftestcus.vault.azure.net/secrets/sftstcnserver"
            ],
      "metadata": {
        "description": "kv vm extension observed certificates versionless uri"
      }
    },
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
  },
  "resources": [
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[variables('vmNodeTypeName')]",
    "location": "[variables('computeLocation')]",
    "dependsOn": [
      "[variables('userAssignedIdentityResourceId')]",
      "[concat('Microsoft.KeyVault/vaults/', concat(parameters('keyVaultName'), '/accessPolicies/add'))]"
    ],
    "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "[variables('userAssignedIdentityResourceId')]": {}
      }
    },
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
        {
          "name": "KVVMExtension",
          "properties": {
            "publisher": "Microsoft.Azure.KeyVault",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": "[parameters('kvvmextPollingInterval')]",
                    "linkOnRenewal": false,
                    "observedCertificates": "[parameters('kvvmextObservedCertificates')]",
                    "requireInitialSync": true
                }
            }
          }
        },
        {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
        "properties": {
          "type": "ServiceFabricNode",
          "provisionAfterExtensions" : [ "KVVMExtension" ],
          "publisher": "Microsoft.Azure.ServiceFabric",
          "settings": {
            "certificate": {
                "commonNames": [
                    "[parameters('certificateCommonName')]"
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            }
            },
            "typeHandlerVersion": "1.0"
          }
        },
  ] } ## extension profile
  },  ## VM profile
  "osProfile": {
    "adminPassword": "[parameters('adminPassword')]",
    "adminUsername": "[parameters('adminUsername')]",
  } 
  }
  ]
```
Not, yukarıda açıkça listelenmese de, sanal makine ölçek kümesinin ' OsProfile ' bölümünden kasa sertifikası URL 'sini kaldırdık.
Son adım, sertifika bildirimini parmak izden ortak ada değiştirmek üzere küme tanımını güncelleştirmenin yanı sıra verenin parmak izlerini de sabitliyoruz:

```json
  "parameters": {
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "defaultValue": "1b45ec255e0668375043ed5fe78a09ff1655844d,d7fe717b5ff3593764f4d90654d86e8362ec26c8,3ac7c3cac8de0dd392c02789c8be97474f456960,96ea05926e2e42cc207e358668be2c316857fb5e",
      "metadata": {
        "description": "Certificate issuer thumbprints separated by comma"
      }
    },
  },
  "resources": [
    {
      "apiVersion": "[variables('sfrpApiVersion')]",
      "type": "Microsoft.ServiceFabric/clusters",
      "name": "[parameters('clusterName')]",
      "location": "[parameters('clusterLocation')]",
      "properties": {
        "certificateCommonNames": {
          "commonNames": [{
              "certificateCommonName": "[parameters('certificateCommonName')]",
              "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
          }],
          "x509StoreName": "[parameters('certificateStoreValue')]"
        },
  ]
```

Bu noktada, yukarıda belirtilen güncelleştirmeleri tek bir dağıtımda çalıştırabilirsiniz; Service Fabric kaynak sağlayıcısı hizmeti, küme [sertifikalarını parmak izinden ortak ada dönüştürme](cluster-security-certificates.md#converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations)bölümünde açıklandığı gibi çeşitli adımlarda küme yükseltmesini böler.

### <a name="analysis-and-observations"></a>Analiz ve gözlemler
Bu bölüm, yukarıda açıklanan adımları açıklayan ve önemli yönlere dikkat çekmek için bir catch-all ' dır.

#### <a name="certificate-provisioning-explained"></a>Sertifika sağlama, açıklanma
Bir sağlama Aracısı olarak KVVM uzantısı, önceden belirlenmiş bir sıklıkta sürekli olarak çalışır. Gözlemlenen bir sertifikayı almadığında, sıradaki bir sonraki satıra devam eder ve sonraki döngüye kadar bekler. Küme önyükleme Aracısı olarak SFVM uzantısı, kümenin Form oluşturmadan önce, belirtilen sertifikaları gerektirecektir. Bu, buna karşılık, SFVM uzantısının yalnızca, ```json "provisionAfterExtensions" : [ "KVVMExtension" ]"``` yan tümce ve KeyVaultVM uzantısının ayarı tarafından belirtilen küme sertifikalarının başarıyla alındıktan sonra çalıştırılabileceği anlamına gelir ```json "requireInitialSync": true``` . Bu, ilk çalıştırmada (dağıtımdan sonra veya yeniden başlatmadan sonra) tüm başarılı bir şekilde indirilene kadar gözlemlenen sertifikalarında geçiş yapılması gereken KVVM uzantısını belirtir. Bu parametrenin, küme sertifikalarını alma hatasıyla birlikte false olarak ayarlanması, küme dağıtımında hata oluşmasına neden olur. Buna karşılık, gözlemlenen sertifikaların hatalı/geçersiz listesiyle bir ilk eşitleme gerektirmek, KVVM uzantısının bir hatasına neden olur ve bu nedenle, kümeyi dağıtmaya yönelik bir hata oluşur.  

#### <a name="certificate-linking-explained"></a>Sertifika bağlama, açıklanacak
KVVM uzantısının ' Linkonyenilemeye ' bayrağını ve false olarak ayarlandığını fark etmiş olabilirsiniz. Burada, bu bayrak tarafından denetlenen davranışın ve kümenin çalışmasına ilişkin etkilerine ilişkin ayrıntılı olarak adresliyoruz. Bu davranışın Windows 'a özgü olduğunu aklınızda yapın.

[Tanımına](../virtual-machines/extensions/key-vault-windows.md#extension-schema)göre:

```json
"linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
```

Bir TLS bağlantısı kurmak için kullanılan sertifikalar genellikle S kanallı güvenlik desteği sağlayıcısı aracılığıyla [bir tanıtıcı olarak](/windows/win32/api/sspi/nf-sspi-acquirecredentialshandlea) alınır; diğer bir deyişle, istemci sertifikanın özel anahtarına doğrudan erişemez. S-Channel, kimlik bilgilerinin bir sertifika uzantısı ([CERT_RENEWAL_PROP_ID](/windows/win32/api/wincrypt/nf-wincrypt-certsetcertificatecontextproperty#cert_renewal_prop_id)) biçiminde yeniden yönlendirilmesini (bağlamayı) destekler: Bu özellik ayarlandıysa, değeri ' yenileme ' sertifikasının parmak izini temsil eder ve bu nedenle S-Channel, bağlı sertifikayı yüklemeyi dener. Aslında, bir yenileme işareti olmadan ' son ' sertifikayla bitene kadar bu bağlantılı (ve neyse ki Çevrimsiz) listesini gezecektir. Bu özellik, bozacağından kullanıldığında, zaman aşımına uğramamış sertifikaların (örneğin) neden olduğu kullanılabilirlik kaybına karşı çok büyük bir risk. Diğer durumlarda, tanılamaya ve hafifleşmeye zorlanması zor olan kesintilerin nedeni olabilir. S-Channel, sertifika geçişini, sertifika, verenler veya istemci tarafından elde edilen sertifikanın doğrulanmasına katılan diğer belirli özniteliklere bakılmaksızın, kendi yenileme özelliklerine koşulsuz olarak yürütür. Aslında, sonuçta elde edilen sertifikanın ilişkili bir özel anahtarı yoktur ya da anahtar onun olası tüketicisine bir şekilde erişim altına alınmaz. 
 
Bağlama etkinse, kasadan gözlemlenen bir sertifika alındıktan sonra Anahtar Kasası VM uzantısı, yenileme uzantısı özelliği aracılığıyla bunları bağlamak için eşleşen, mevcut sertifikaları bulmaya çalışır. Eşleştirme (özel olarak) konu alternatif adı (SAN) temelinde ve aşağıda belirtilen örnek olarak çalışmaktadır.
Var olan iki sertifikayı şu şekilde varsayın: A: CN = "Gamze 'nin aksesuarları", SAN = {"alice.universalexports.com"}, yenileme = ' ' B: CN = "Bob 's bit", SAN = {"bob.universalexports.com", "bob.universalexports.net"}, yenileme = ' '
 
KVVM ext: CN = "Mallory 'un kötü amaçlı yazılımı", SAN = {"alice.universalexports.com", "bob.universalexports.com", "mallory.universalexports.com"} tarafından alınan bir sertifika olduğunu varsayalım
 
Bir SAN listesi C ' ye tam olarak dahildir ve bu nedenle bir. yenilemesi = C. parmak izi; B 'nin SAN listesinin C ile ortak bir kesişimi vardır ancak tam olarak dahil edilmez, bu nedenle B. yenileme boş kalır.
 
Sertifika A 'da bu durumda AcquireCredentialsHandle (S-Channel) çağırmak için bir girişim, aslında uzak tarafa C 'yi gönderiyor. Service Fabric durumda, bir kümenin [Aktarım alt sistemi](service-fabric-architecture.md#transport-subsystem) karşılıklı kimlik doğrulaması için S kanalı kullanır ve bu nedenle yukarıda açıklanan davranış kümenin temel iletişimini doğrudan etkiler. Yukarıdaki örneğe devam eder ve bir küme sertifikası olduğu varsayıldığında, bir sonraki durum değişir:
  - C 'nin özel anahtarı, dokunun çalıştığı hesaba ACLd değilse, özel anahtarı (SEC_E_UNKNOWN_CREDENTIALS veya benzeri) alma başarısızlıklarını görüyoruz
  - C 'nin özel anahtarı erişilebilir ise, diğer düğümlerin (Certificatenoteşleşti, yetkisiz vb.) döndürdüğü yetkilendirme başarısızlıklarını görüyoruz. 
 
Her iki durumda da aktarım başarısız olur ve küme devam edebilir; Belirtiler farklılık gösterir. Nesnelerin daha kötü olması için, bağlantı yenileme sırasına bağlıdır. Bu, KVVM uzantısının gözlemlenen sertifikaların listesinin sırası, kasadaki yenileme zamanlaması veya hatta alma sırasını değiştirecek geçici hatalar ile belirlenir.

Bu tür olaylara karşı azaltmak için şunları yapmanızı öneririz:
  - farklı kasa sertifikalarının San 'larını karıştırmayın; Her kasa sertifikası ayrı bir amaç sunmalı ve bunların konusu ve SAN 'ı benzersiz bir şekilde yansıtmalıdır
  - ilgili ortak adı SAN listesine ekleyin (as, harfine, "CN = <subject common name> ")  
  - emin değilseniz, KVVM uzantısıyla sağlanan sertifikalar için yenileme bağlantısını devre dışı bırakın 

#### <a name="why-use-a-user-assigned-managed-identity-what-are-the-implications-of-using-it"></a>Kullanıcı tarafından atanan yönetilen kimlik neden kullanılmalıdır? Kullanmanın etkileri nelerdir?
Yukarıdaki JSON parçacıklarında olduğu gibi, dönüştürmenin başarısını garantilemek ve kümenin kullanılabilirliğini sürdürmek için işlemler/güncelleştirmeler için belirli bir sıralama gerekir. Özellikle, sanal makine ölçek kümesi kaynağı, tek bir (kullanıcının perspektifinden) güncelleştirmesinden parolaları almak için kimliğini bildirir ve kullanır. Service Fabric sanal makine uzantısı (önyükleme), gözlemlenen sertifikaların başarıyla alınmasına bağlı olan Anahtar Kasası VM uzantısının tamamlanmasına bağlıdır. KVVM uzantısı kasaya erişmek için sanal makine ölçek kümesinin kimliğini kullanır, bu da kasadaki erişim ilkesinin sanal makine ölçek kümesinin dağıtımıyla önce zaten güncelleştirilmiş olması gerektiği anlamına gelir. 

Yönetilen bir kimliğin oluşturulmasını atmak ya da başka bir kaynağa atamak için, şablonda başvurulan diğer kaynakları yönetmek için gereken rollere ek olarak, dağıtım işlecinin abonelikte veya kaynak grubunda gerekli role (Managedıdentityoperator) sahip olması gerekir. 

Bir güvenlik açısından, sanal makinenin (ölçek kümesi) Azure kimliğiyle ilgili bir güvenlik sınırı olarak kabul edileceğini hatırlayın. Yani, VM 'de barındırılan herhangi bir uygulamanın, ilke içinde VM tarafından yönetilen kimlik erişim belirteçlerini temsil eden bir erişim belirtecini, kimliği doğrulanmamış ıMDS uç noktasından elde ettiğiniz anlamına gelir. VM 'yi paylaşılan veya çok kiracılı bir ortam olarak kabul ediyorsanız, bu küme sertifikalarını alma yöntemi belirtilmemiş olabilir. Bununla birlikte, yalnızca sertifika oto geçişi için uygun olan tek sağlama mekanizması vardır.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Sorun giderme ve sık sorulan sorular

*S*: program aracılığıyla Anahtar Kasası tarafından yönetilen bir sertifikaya nasıl kaydolur?
Y *: anahtar* Kasası belgelerinden verenin adını bulun ve aşağıdaki komut dosyasında değiştirin.  
```PowerShell
  $issuerName=<depends on your PKI of choice>
    $clusterVault="sftestcus"
    $clusterCertVaultName="sftstcncluster"
    $clusterCertCN="cus.cluster.sftstcn.system.servicefabric.azure-int"
    Set-AzKeyVaultCertificateIssuer -VaultName $clusterVault -Name $issuerName -IssuerProvider $issuerName
    $distinguishedName="CN=" + $clusterCertCN
    $policy = New-AzKeyVaultCertificatePolicy `
        -IssuerName $issuerName `
        -SubjectName $distinguishedName `
        -SecretContentType 'application/x-pkcs12' `
        -Ekus "1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2" `
        -ValidityInMonths 4 `
        -KeyType 'RSA' `
        -RenewAtPercentageLifetime 75        
    Add-AzKeyVaultCertificate -VaultName $clusterVault -Name $clusterCertVaultName -CertificatePolicy $policy
    
    # poll the result of the enrollment
    Get-AzKeyVaultCertificateOperation -VaultName $clusterVault -Name $clusterCertVaultName 
```

*S*: bir sertifika bildirilmemiş/belirtilmemiş veren tarafından verildiğinde ne olur? Belirli bir PKI 'nın etkin verenler listesini nereden alabilirim?
Y *: sertifika* bildirimi veren parmak izlerini belirtir ve sertifikayı doğrudan veren sabitlenmiş verenler listesine dahil edilmiyorsa, sertifika, köküne istemci tarafından güvenilip güvenilmediği bağımsız olarak kabul edilir. Bu nedenle, verenler listesinin güncel/güncel olmasını sağlamak önemlidir. Yeni bir veren 'in tanıtımı nadir bir olaydır ve sertifika verilmesinden önce yaygın olarak genel kullanıma sunulmalıdır. 

Genellikle bir PKI, IETF [RFC 7382](https://tools.ietf.org/html/rfc7382)' ye uygun olarak bir sertifika uygulama ekstresi yayımlayıp bakımını sağlayacaktır. Diğer bilgiler arasında, tüm etkin verenler dahil edilir. Bu listenin program aracılığıyla alınması bir PKI 'dan diğerine farklılık gösterebilir.   

Microsoft-iç PKI 'lar için lütfen yetkili verenler almak için kullanılan uç noktalar/SDK 'lar hakkında iç belgelere başvurun; Bu liste, bu listenin düzenli olarak araştırılıp küme tanımının beklenen *Tüm* verenler içerdiğinden emin olmanızı sağlar.

*S*: birden çok PKI destekleniyor mu? 
Y *: Evet*; küme bildiriminde aynı değere sahip birden fazla CN girişi bildiremeyebilirsiniz, ancak aynı CN 'ye karşılık gelen birden fazla PKI 'dan verenler listeleyebilir. Önerilen bir uygulama değildir ve sertifika saydamlığı uygulamaları, bu tür sertifikaların verilmasını önleyebilir. Ancak, bir PKI 'dan diğerine geçiş yapmak için bu kabul edilebilir bir mekanizmadır.

*S*: geçerli küme sertifikası CA tarafından yayınlanmıyorsa veya amaçlanan konusu yoksa ne olur? 
Y *: hedeflenen konuya sahip bir sertifika* alın ve bunu, parmak izine göre ikincil olarak kümenin tanımına ekleyin. Yükseltme başarıyla tamamlandıktan sonra, sertifika bildirimini ortak ada dönüştürmek için başka bir küme yapılandırma yükseltmesi başlatın. 

[Image1]:./media/security-cluster-certificate-mgmt/certificate-journey-thumbprint.png
[Image2]:./media/security-cluster-certificate-mgmt/certificate-journey-common-name.png
