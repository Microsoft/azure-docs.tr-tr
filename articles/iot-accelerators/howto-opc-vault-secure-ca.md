---
title: OPC Kasası sertifika yönetimi hizmetini güvenli bir şekilde çalıştırma-Azure | Microsoft Docs
description: OPC Kasası sertifika yönetimi hizmetini Azure 'da güvenli bir şekilde çalıştırmayı açıklar ve göz önünde bulundurmanız gereken diğer güvenlik kılavuzlarını gözden geçirir.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 88f8188779c5fb6b3cd07c67e9f35a6b8f9ad97d
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200089"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>OPC Kasası sertifika yönetimi hizmetini güvenli bir şekilde çalıştırın

Bu makalede, OPC Kasası sertifika yönetimi hizmetinin Azure 'da güvenli bir şekilde çalıştırılması ve göz önünde bulundurulması gereken diğer güvenlik yönergeleri inceleneceği açıklanır.

## <a name="roles"></a>Roller

### <a name="trusted-and-authorized-roles"></a>Güvenilen ve yetkili roller

OPC Kasası mikro hizmeti, farklı rollerin hizmetin çeşitli bölümlerine erişmesine izin verir.

> [!IMPORTANT]
> Dağıtım sırasında, komut dosyası yalnızca dağıtım betiğini çalıştıran kullanıcıyı tüm roller için Kullanıcı olarak ekler. Bir üretim dağıtımı için, bu rol atamasını gözden geçirmeniz ve aşağıdaki yönergeleri izleyerek uygun şekilde yeniden yapılandırmanız gerekir. Bu görev, Azure Active Directory (Azure AD) kurumsal uygulamalar portalında rol ve hizmetlerin el ile atanmasını gerektirir.

### <a name="certificate-management-service-roles"></a>Sertifika Yönetimi hizmet rolleri

OPC Kasası mikro hizmeti aşağıdaki rolleri tanımlar:

- **Okuyucu**: Varsayılan olarak, Kiracıdaki tüm kimliği doğrulanmış kullanıcılar okuma erişimine sahiptir. 
  - Uygulamalar ve sertifika isteklerine yönelik okuma erişimi. Uygulamalar ve sertifika isteklerini listeleyebilir ve sorgulayabilir. Ayrıca, cihaz bulma bilgilerine ve genel sertifikalara okuma erişimiyle erişilebilir.
- **Yazıcı**: Yazıcı rolü bir kullanıcıya belirli görevler için yazma izinleri eklemek üzere atanır. 
  - Uygulamalar ve sertifika isteklerine yönelik okuma/yazma erişimi. Uygulamaları kaydedebilir, güncelleştirebilir ve kayıt silebilirsiniz. Sertifika istekleri oluşturabilir ve onaylanan özel anahtarlar ve sertifikalar alabilir. , Özel anahtarları da silebilir.
- **Onaylayan**: Onaylayan rolü, sertifika isteklerini onaylamak veya reddetmek için bir kullanıcıya atanır. Rol başka bir rol içermez.
  - OPC Kasası mikro hizmet API 'sine erişmek için onaylayanın rolüne ek olarak, kullanıcıların sertifikaları imzalayabilmesi için Azure Key Vault anahtar imzalama iznine de sahip olması gerekir.
  - Yazıcı ve onaylayan rolü farklı kullanıcılara atanmalıdır.
  - Onaylayanın ana rolü, sertifika isteklerinin oluşturulması ve redlini onaysıdır.
- **Yönetici**: Yönetici rolü bir kullanıcıya sertifika gruplarını yönetmek üzere atanır. Rol onaylayan rolünü desteklemez, ancak yazıcı rolünü içerir.
  - Yönetici, sertifika gruplarını yönetebilir, yapılandırmayı değiştirebilir ve yeni bir sertifika Iptal listesi (CRL) vererek uygulama sertifikalarını iptal edebilir.
  - İdeal olarak, yazıcı, onaylayan ve yönetici rolleri farklı kullanıcılara atanır. Ek güvenlik için, onaylayan veya yönetici rolüne sahip bir kullanıcının ayrıca Key Vault, sertifika vermek veya bir veren CA sertifikasını yenilemek için anahtar imzalama izni olması gerekir.
  - Mikro hizmet Yönetim rolüne ek olarak, rol şunları içerir ancak bunlarla sınırlı değildir:
    - CA 'nın güvenlik uygulamalarının uygulanmasını yönetme sorumluluğu.
    - Sertifikaların oluşturulması, iptali ve askıya alınma yönetimi. 
    - Şifreleme anahtarı yaşam döngüsü yönetimi (örneğin, veren CA anahtarlarının yenilenmesi).
    - CA 'yı çalışan hizmetlerin yüklenmesi, yapılandırılması ve bakımı.
    - Hizmetlerin gündelik işlemi. 
    - CA ve veritabanı yedekleme ve kurtarma.

### <a name="other-role-assignments"></a>Diğer rol atamaları

Ayrıca, hizmeti çalıştırırken aşağıdaki rolleri göz önünde bulundurun:

- Dış kök sertifika yetkilisine sahip sertifika tedarik sözleşmesinin iş sahibi (örneğin, sahibi bir dış CA 'dan sertifika satın alırken veya harici bir CA 'ya bağımlı bir CA çalıştırırken).
- Sertifika yetkilisinin geliştirilmesi ve doğrulanması.
- Denetim kayıtlarını gözden geçirme.
- CA 'yı desteklemeye veya fiziksel ve bulut tesislerinin yönetilmesine yardımcı olan, ancak CA işlemlerini gerçekleştirmek için doğrudan güvenilir olmayan personel, *yetkili* roldür. Yetkilendirilmiş roldeki kişilerin gerçekleştirmesine izin verilen görev kümesi de açıklanmalıdır.

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>Güvenilen ve yetkili rollerin üyeliklerini üç ayda gözden geçirin

Güvenilen ve yetkili rollerin üyeliğini en az üç ayda bir inceleyin. Her roldeki kişilerin (el ile süreçler için) veya hizmet kimliklerinin (Otomatik süreçler için) en düşük düzeyde tutulduğundan emin olun.

### <a name="role-separation-between-certificate-requester-and-approver"></a>Sertifika isteyenin ve onaylayan arasında rol ayrımı

Sertifika verme işlemi, sertifika talep eden ve sertifika onaylayan rolleri (kişiler veya otomatik sistemler) arasında rol ayrımı zorlaması gerekir. Sertifika verme, sertifika istek sahibinin sertifika alma yetkisi olduğunu doğrulayan bir sertifika onaylayan rolü tarafından yetkilendirilmelidir. Sertifika onaylayan rolünü tutan kişiler, resmi olarak yetkilendirilmiş bir kişi olmalıdır.

### <a name="restrict-assignment-of-privileged-roles"></a>Ayrıcalıklı rollerin atamasını kısıtla

Yöneticiler ve onaylayanlar grubunun üyeliğini yetkilendirme gibi ayrıcalıklı rollerin atanmasını, sınırlı bir yetkili personele göre kısıtlamalısınız. Herhangi bir ayrıcalıklı rol değişikliği, 24 saat içinde erişimi iptal etmelidir. Son olarak, ayrıcalıklı rol atamalarını üç ayda bir gözden geçirin ve gereksiz veya süre dolmayan atamaları kaldırın.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>Ayrıcalıklı roller iki öğeli kimlik doğrulaması kullanmalıdır

Onaylayanlar ve yöneticilerin etkileşimli oturum açma işlemleri için çok faktörlü kimlik doğrulaması (iki öğeli kimlik doğrulaması da denir) kullanın.

## <a name="certificate-service-operation-guidelines"></a>Sertifika hizmeti işlem yönergeleri

### <a name="operational-contacts"></a>İşletimsel kişiler

Sertifika Hizmeti, ayrıntılı işletimsel olay yanıtı kişilerini içeren dosya üzerinde güncel bir güvenlik yanıt planına sahip olmalıdır.

### <a name="security-updates"></a>Güvenlik güncelleştirmeleri

Tüm sistemler sürekli olarak izlenmeli ve en son güvenlik güncelleştirmeleriyle güncellenecektir.

> [!IMPORTANT]
> OPC Kasası hizmetinin GitHub deposu, güvenlik düzeltme ekleriyle sürekli olarak güncelleştirilir. Bu güncelleştirmeleri izleyin ve düzenli aralıklarla hizmete uygulayın.

### <a name="security-monitoring"></a>Güvenliği izleme

Uygun güvenlik izlemeye abone olun veya uygulayın. Örneğin, bir merkezi izleme çözümüne abone olun (örneğin, Azure Güvenlik Merkezi veya Office 365 izleme çözümü) ve güvenlik olaylarının izleme çözümüne aktarılmasını sağlamak için uygun şekilde yapılandırın.

> [!IMPORTANT]
> Varsayılan olarak, OPC kasası hizmeti bir izleme çözümü olarak [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/devops) ile dağıtılır. [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/) gibi bir güvenlik çözümü eklemek kesinlikle önerilir.

### <a name="assess-the-security-of-open-source-software-components"></a>Açık kaynaklı yazılım bileşenlerinin güvenliğini değerlendirin

Bir ürün veya hizmet içinde kullanılan tüm açık kaynaklı bileşenler, orta veya daha büyük güvenlik açıklarına karşı ücretsiz olmalıdır.

> [!IMPORTANT]
> Sürekli tümleştirme derlemeleri sırasında OPC kasası hizmeti 'nin GitHub deposu güvenlik açıkları için tüm bileşenleri tarar. Bu güncelleştirmeleri GitHub 'da izleyin ve düzenli aralıklarla hizmete uygulayın.

### <a name="maintain-an-inventory"></a>Envanter Bakımı

Tüm üretim Konakları (kalıcı sanal makineler dahil), cihazlar, tüm iç IP adresi aralıkları, VIP 'ler ve genel DNS etki alanı adları için bir varlık envanterini saklayın. Bir sistem, aygıt IP adresi, VIP veya genel DNS etki alanı eklediğinizde veya kaldırdığınızda, stoku 30 gün içinde güncelleştirmeniz gerekir.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Varsayılan Azure OPC Kasası mikro hizmet üretim dağıtımının envanteri 

Azure 'da:
- **App Service planı**: Hizmet ana bilgisayarları için App Service planı. Varsayılan S1.
- Mikro hizmet için **App Service** : OPC kasa hizmeti ana bilgisayarı.
- Örnek uygulama için **App Service** : OPC Kasası örnek uygulama konağı.
- **Key Vault standart**: Web Hizmetleri için gizli dizileri ve Azure Cosmos DB anahtarlarını depolamak için.
- **Key Vault Premium**: Veren CA anahtarlarını barındırmak için, imzalama hizmeti için ve uygulama özel anahtarlarının kasa yapılandırması ve depolaması için.
- **Azure Cosmos DB**: Uygulama ve sertifika istekleri için veritabanı. 
- **Application Insights**: (isteğe bağlı) Web hizmeti ve uygulaması için izleme çözümü.
- **Azure AD uygulama kaydı**: Örnek uygulama, hizmet ve Edge modülü için bir kayıt.

Bulut hizmetleri için, hizmeti dağıtmak için kullanılan tüm ana bilgisayar adları, kaynak grupları, kaynak adları, abonelik kimlikleri ve kiracı kimlikleri belgelenmelidir. 

Azure IoT Edge veya yerel IoT Edge sunucusunda:
- **OPC kasası IoT Edge modülü**: Fabrika ağı OPC UA küresel bulma sunucusunu desteklemek için. 

IoT Edge cihazlarda, ana bilgisayar adları ve IP adresleri belgelenmelidir. 

### <a name="document-the-certification-authorities-cas"></a>Sertifika yetkililerini (CA 'Lar) belgeleme

CA hiyerarşisi belgelerinin tüm çalıştırılan CA 'Ları içermesi gerekir. Bu, hizmet tarafından yönetilmediğinde bile ilgili tüm alt CA 'Ları, üst CA 'Ları ve kök CA 'Ları içerir. Resmi belgeler yerine, tüm süre dolmayan CA sertifikalarının kapsamlı bir kümesini sağlayabilirsiniz.

> [!NOTE]
> OPC Kasası örnek uygulaması, belgeleri için hizmette kullanılan ve üretilen tüm sertifikaların indirilmesini destekler.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>Tüm sertifika yetkililerine (CAs) verilen sertifikaları belgeleme

Son 12 ayda verilen tüm sertifikaların ayrıntılı bir kümesini sağlar.

> [!NOTE]
> OPC Kasası örnek uygulaması, belgeleri için hizmette kullanılan ve üretilen tüm sertifikaların indirilmesini destekler.

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>Şifreleme anahtarlarını güvenli bir şekilde silmek için standart işletim yordamını belgeleyin

CA 'nın kullanım ömrü boyunca, anahtar silme yalnızca nadiren olabilir. Hiçbir kullanıcının Key Vault sertifikası silme hakkı atanmamış ve bir veren CA sertifikasını silmeye yönelik hiçbir API 'nin neden olmadığı. Sertifika yetkilisi şifreleme anahtarlarını güvenli bir şekilde silmek için el ile standart işletim yordamı yalnızca Azure portal Key Vault doğrudan erişim yoluyla kullanılabilir. Key Vault de sertifika grubunu silebilirsiniz. Hemen silme işlemini sağlamak için [Key Vault geçici silme](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) işlevini devre dışı bırakın.

## <a name="certificates"></a>Sertifikalar

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>Sertifikalar en düşük sertifika profiliyle uyumlu olmalıdır

OPC kasa hizmeti, abonelere son varlık sertifikaları veren çevrimiçi bir CA 'dır. OPC Kasası mikro hizmeti bu yönergeleri varsayılan uygulamada izler.

- Tüm sertifikaların aşağıda belirtilen X. 509.440 alanlarını içermesi gerekir:
  - Sürüm alanının içeriği v3 olmalıdır. 
  - SerialNumber alanının içeriği, FIPS (Federal bilgi Işleme standartları) 140 onaylı rastgele sayı üreticisi tarafından alınan en az 8 baytlık entropi içermelidir.<br>
    > [!IMPORTANT]
    > OPC Kasası seri numarası varsayılan olarak 20 bayttır ve işletim sistemi şifreleme rastgele sayı oluşturucusından elde edilir. Rastgele sayı üreticisi, Windows cihazlarında FIPS 140 onaylı, ancak Linux üzerinde onaylanmamış bir sayıdır. Linux VM 'Leri veya Linux Docker Kapsayıcıları kullanan bir hizmet dağıtımı seçerken bunu göz önünde bulundurun. Bu, temel alınan teknoloji OpenSSL FIPS 140 onaylı değildir.
  - Issueruniqueıd ve Subjectuniqueıd alanları mevcut olmamalıdır.
  - Son varlık sertifikalarının, IETF RFC 5280 ' ye uygun olarak temel kısıtlamalar uzantısıyla tanımlanması gerekir.
  - Veren CA sertifikası için pathLenConstraint alanının 0 olarak ayarlanması gerekir. 
  - Genişletilmiş anahtar kullanımı uzantısının mevcut olması gerekir ve en düşük genişletilmiş anahtar kullanımı nesne tanımlayıcıları (OID) kümesini içermelidir. AnyExtendedKeyUsage OID (2.5.29.37.0) belirtilmemelidir. 
  - CRL dağıtım noktası (CDP) uzantısının veren CA sertifikasında mevcut olması gerekir.<br>
    > [!IMPORTANT]
    > CDP uzantısı OPC kasa CA sertifikalarında bulunur. Bununla birlikte, OPC UA cihazları CRL 'Leri dağıtmak için özel yöntemler kullanır.
  - Yetkili bilgi erişimi uzantısının abone sertifikalarında mevcut olması gerekir.<br>
    > [!IMPORTANT]
    > Yetkili bilgi erişimi uzantısı OPC Kasası abone sertifikalarında bulunur. Bununla birlikte, OPC UA cihazları veren CA bilgilerini dağıtmak için özel yöntemler kullanır.
- Onaylanan Asimetrik algoritmalar, anahtar uzunlukları, karma işlevler ve doldurma modları kullanılmalıdır.
  - Yalnızca RSA ve SHA-2 desteklenen algoritmalardır.
  - RSA, şifreleme, anahtar değişim ve imza için kullanılabilir.
  - RSA şifrelemesi yalnızca OAEP, RSA-KED veya RSA-PSS doldurma modlarını kullanmalıdır.
  - 2048 bitten büyük veya buna eşit olan anahtar uzunlukları gereklidir.
  - SHA-2 karma algoritmaları ailesini (SHA256, SHA384 ve SHA512 olur) kullanın.
  - 20 yaşından büyük veya buna eşit bir yaşam süresine sahip RSA kök CA anahtarları 4096 bit veya daha büyük olmalıdır.
  - RSA veren CA anahtarları en az 2048 bit olmalıdır. CA sertifikasının sona erme tarihi 2030 sonrasında, CA anahtarı 4096 bit veya daha büyük olmalıdır.
- Sertifika ömrü
  - Kök CA sertifikaları: Kök CA 'Lar için en fazla sertifika geçerlilik süresi 25 yıldan fazla olmamalıdır.
  - Alt CA veya çevrimiçi sertifika veren CA sertifikaları: Çevrimiçi olan ve yalnızca abone sertifikaları veren CA 'Lar için en fazla sertifika geçerlilik süresi 6 yıldan fazla olmamalıdır. Bu CA 'Lar için, ilgili özel imza anahtarı 3 yıldan daha uzun bir süre önce yeni sertifika vermek için kullanılmamalıdır.<br>
    > [!IMPORTANT]
    > Sertifikayı veren sertifika, dış kök CA 'sı olmayan varsayılan OPC Kasası mikro hizmetinde oluşturulduğu gibi, ilgili gereksinimler ve yaşam süreleri ile bir çevrimiçi alt CA gibi değerlendirilir. Varsayılan ömür, 2048 veya daha büyük bir anahtar uzunluğu ile 5 yıl olarak ayarlanır.
  - Tüm asimetrik anahtarların en fazla 5 yıllık ömrü ve önerilen 1 yıllık ömrü olması gerekir.<br>
    > [!IMPORTANT]
    > Varsayılan olarak, OPC kasası ile verilen uygulama sertifikalarının yaşam sürelerinin ömrü 2 yıldır ve her yıl değiştirilmelidir. 
  - Bir sertifika her yenilendiğinde, yeni bir anahtarla yenilenir.
- Uygulama örneği sertifikalarındaki OPC UA 'a özgü uzantılar
  - SubjectAltName uzantısı, uygulama URI 'sini ve ana bilgisayar adlarını içerir. Bunlara FQDN, IPv4 ve IPv6 adresleri de dahil olabilir.
  - KeyUsage, digitalSignature, reddetme, keyEncipherment ve Dataencment öğelerini içerir.
  - ExtendedKeyUsage, serverAuth ve clientAuth bilgilerini içerir.
  - Authoritykeyıdentifier, imzalanmış sertifikalarda belirtilir.

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>CA anahtarları ve sertifikaların minimum gereksinimleri karşılaması gerekir

- **Özel anahtarlar**: RSA anahtarları en az 2048 bit olmalıdır. CA sertifikasının sona erme tarihi 2030 sonrasında, CA anahtarı 4096 bit veya daha büyük olmalıdır.
- **Ömür**: Çevrimiçi olan ve yalnızca abone sertifikaları veren CA 'Lar için en fazla sertifika geçerlilik süresi 6 yıldan fazla olmamalıdır. Bu CA 'Lar için, ilgili özel imza anahtarı 3 yıldan daha uzun bir süre önce yeni sertifika vermek için kullanılmamalıdır.

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>CA anahtarları, donanım güvenlik modülleri kullanılarak korunur

Opckasa Azure Key Vault Premium kullanır ve anahtarlar FIPS 140-2 düzey 2 donanım güvenlik modülleri (HSM) tarafından korunur. 

Key Vault kullandığı şifreleme modülleri, HSM veya yazılımın FIPS tarafından doğrulanıp onaylanmadığını belirtir. HSM korumalı olarak oluşturulan veya içeri aktarılan anahtarlar, FIPS 140-2 düzey 2 olarak doğrulanan bir HSM içinde işlenir. Yazılım korumalı olarak oluşturulan veya içeri aktarılan anahtarlar FIPS 140-2 düzey 1 ' e doğrulanan şifreleme modülleri içinde işlenir.

## <a name="operational-practices"></a>İşletimsel uygulamalar

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Sertifika kaydı için standart işletimsel PKI uygulamalarını belgeleyin ve bakımını yapın

CA 'Ların sertifika verme yöntemi (SOP 'ler belirli) için aşağıdakiler dahil olmak üzere standart işletimsel yordamları belgeleyin ve saklayın: 
- Abonenin nasıl tanımlandığı ve kimliği doğrulanır. 
- Sertifika isteği nasıl işlenir ve onaylanır (varsa, sertifika yenileme ve yeniden anahtarlama isteklerinin nasıl işleneceğini de içerir). 
- Verilen sertifikalar abonelere nasıl dağıtılır. 

OPC Kasası mikro hizmeti SOP, OPC Kasası [mimarisinde](overview-opc-vault-architecture.md) açıklanmıştır ve [OPC Kasası sertifika hizmetini yönetebilir](howto-opc-vault-manage.md). Uygulamalar "OPC Birleşik mimari belirtimi Bölüm 12 ' i izler: Bulma ve küresel hizmetler. "


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Sertifika iptali için standart işletimsel PKI uygulamalarını belgeleyin ve bakımını yapın

Sertifika iptal işlemi [OPC kasa mimarisinde](overview-opc-vault-architecture.md) açıklanmıştır ve [OPC Kasası sertifika hizmetini yönetebilir](howto-opc-vault-manage.md).
    
### <a name="document-ca-key-generation-ceremony"></a>Belge CA anahtarı oluşturma seremonisi 

OPC Kasası mikro hizmetindeki veren CA anahtarı oluşturma işlemi, Azure Key Vault güvenli depolama nedeniyle basitleştirilmiştir. Daha fazla bilgi için bkz. [OPC Kasası sertifika hizmetini yönetme](howto-opc-vault-manage.md).

Ancak, bir dış kök sertifika yetkilisi kullanırken, CA anahtarı oluşturma sertifikası aşağıdaki gereksinimlere uymalıdır.

CA anahtarı oluşturma sertifikası, en azından aşağıdaki öğeleri içeren belgelenmiş bir betikte gerçekleştirilmelidir: 
- Rollerin ve katılımcı sorumlulukların tanımı.
- CA anahtar oluşturma seremonisi 'nin kullanım için onay.
- Ceremony için gereken şifreleme donanımı ve etkinleştirme malzemeleri.
- Donanım hazırlama (varlık/yapılandırma bilgileri güncelleştirmesi ve oturumu kapatma dahil).
- İşletim sistemi yüklemesi.
- CA anahtarı oluşturma sertifikası sırasında gerçekleştirilen belirli adımlar: 
  - CA uygulaması yükleme ve yapılandırma.
  - CA anahtarı oluşturma.
  - CA anahtarı yedeklemesi.
  - CA sertifika imzalama.
  - İmzalı anahtarların hizmetin korumalı HSM 'de içeri aktarılması.
  - CA sistemi kapanıyor.
  - Depolama için malzemelerin hazırlanması.


## <a name="next-steps"></a>Sonraki adımlar

Artık OPC kasasını güvenli bir şekilde yönetmeyi öğrendiğinize göre şunları yapabilirsiniz:

> [!div class="nextstepaction"]
> [OPC kasası ile OPC UA cihazlarını güvenli hale getirme](howto-opc-vault-secure.md)
