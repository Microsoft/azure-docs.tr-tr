---
title: Key Vault sertifikalarını kullanmaya başlama
description: Aşağıdaki senaryolarda, Anahtar Kasanızda ilk sertifikanızı oluşturmak için gereken ek adımlar da dahil olmak üzere Key Vault sertifika yönetimi hizmetinin birincil kullanımlarından bazıları ana hatlarıyla verilmiştir.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 06/13/2020
ms.author: mbaldwin
ms.openlocfilehash: 85f17897c0e3089a2d2bc5b172e98fa24e8085ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94920449"
---
# <a name="get-started-with-key-vault-certificates"></a>Key Vault sertifikalarını kullanmaya başlama
Aşağıdaki senaryolarda, Anahtar Kasanızda ilk sertifikanızı oluşturmak için gereken ek adımlar da dahil olmak üzere Key Vault sertifika yönetimi hizmetinin birincil kullanımlarından bazıları ana hatlarıyla verilmiştir.

Aşağıdakiler aşağıda özetlenmiştir:
- İlk Key Vault sertifikanızı oluşturma
- Key Vault ile iş ortağı olan bir sertifika yetkilisi ile sertifika oluşturma
- Key Vault ile iş ortağı olmayan bir sertifika yetkilisi ile sertifika oluşturma
- Sertifikayı içeri aktar

## <a name="certificates-are-complex-objects"></a>Sertifikalar karmaşık nesnelerdir
Sertifikalar, bir Key Vault sertifikası olarak birbirine bağlı üç ilişkili kaynaktan oluşur; Sertifika meta verileri, anahtar ve gizli dizi.


![Sertifikalar karmaşıktır](../media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>İlk Key Vault sertifikanızı oluşturma  
 Bir sertifikanın Key Vault (KV) içinde oluşturulabilmesi için önce 1. ve 2. önkoşul adımlarının başarılı bir şekilde gerçekleştirilmesi gerekir ve bu kullanıcı/kuruluş için bir Anahtar Kasası bulunmalıdır.  

**Adım 1** -sertifika YETKILISI (CA) sağlayıcıları  
-   BT Yöneticisi, PKI Yöneticisi veya CA 'larla hesapları yöneten herkes, belirli bir şirket için (örn. Contoso) Key Vault sertifikaları kullanmanın bir önkoşuludur.  
    Aşağıdaki CA 'Lar Key Vault ile geçerli iş ortağı sağlayıcılarıdır. [Daha fazla bilgi edinin](./create-certificate.md#partnered-ca-providers)   
    -   DigiCert-Key Vault, DigiCert ile OV TLS/SSL sertifikaları sunmaktadır.  
    -   GlobalSign-Key Vault, GlobalSign ile OV TLS/SSL sertifikaları sunmaktadır.  

**2. adım** -CA sağlayıcısı için bir hesap yöneticisi, Key Vault aracılığıyla TLS/SSL sertifikalarını kaydetmek, yenilemek ve kullanmak için Key Vault tarafından kullanılacak kimlik bilgilerini oluşturur.

**3. adım** -CA 'ya bağlı olarak, sertifikalara sahip olan bir contoso çalışanı (Key Vault kullanıcısı) ile birlikte bir contoso Yöneticisi, yöneticiden bir sertifika alabılır veya CA ile doğrudan hesaptan bir sertifika alabilir.  

- Bir [sertifika veren kaynağı ayarlayarak](/rest/api/keyvault/setcertificateissuer/setcertificateissuer) bir anahtar kasasına kimlik bilgisi ekleme işlemi başlatın. Sertifika veren, Azure Key Vault (KV) ile bir Certificateıssuer kaynağı olarak temsil edilen bir varlıktır. Bir KV sertifikasının kaynağı hakkında bilgi sağlamak için kullanılır; verenin adı, sağlayıcı, kimlik bilgileri ve diğer yönetim ayrıntıları.
  - Örn. Mydigicertısuer  
    -   Sağlayıcı  
    -   Kimlik bilgileri – CA hesabı kimlik bilgileri. Her CA 'nın kendine özgü verileri vardır.  

    CA sağlayıcılarıyla hesap oluşturma hakkında daha fazla bilgi için [Key Vault blogdaki](/archive/blogs/kv/manage-certificates-via-azure-key-vault)ilgili gönderisine bakın.  

**Adım 3,1** -bildirimler için [sertifika kişilerini](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) ayarlama. Bu, Key Vault kullanıcısına yönelik kişdir. Key Vault bu adımı zorlamaz.  

Bu süreç, 3,1. adım ile bir kerelik işlemidir.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Key Vault ile CA iş ortağı ile sertifika oluşturma

![Key Vault iş ortağı sertifika yetkilisi ile sertifika oluşturma](../media/certificate-authority-2.png)

**4. adım** -aşağıdaki açıklamalar, önceki diyagramdaki yeşil numaralı adımlara karşılık gelir.  
  (1)-Yukarıdaki diyagramda uygulamanız, Anahtar Kasanızda bir anahtar oluşturarak başlayan bir sertifika oluşturur.  
  (2)-Key Vault CA 'ya bir TLS/SSL sertifika Isteği gönderir.  
  (3)-uygulamanız, sertifika tamamlaması için Key Vault bir döngüde ve bekleme sürecinde yoklar. Anahtar Kasası x509 sertifikasıyla CA yanıtını aldığında sertifika oluşturma işlemi tamamlanır.  
  (4)-CA, bir x509 TLS/SSL sertifikası ile Key Vault TLS/SSL sertifika Isteğine yanıt verir.  
  (5)-yeni sertifika oluşturma, CA için x509 sertifikasının birleşmesi ile tamamlanır.  

  Key Vault User: bir ilke belirterek bir sertifika oluşturur

  -   Gerektiğinde yineleyin  
  -   İlke kısıtlamaları  
      -   X509 özellikleri  
      -   Temel özellikler  
      -   Sağlayıcı başvurusu-> ex. MyDigiCertIssure  
      -   Yenileme bilgileri-> ex. süre sonu 90 gün önce  

  - Sertifika oluşturma işlemi genellikle zaman uyumsuz bir işlemdir ve sertifika oluşturma işleminin durumu için anahtar kasanızın yoklanmasını içerir.  
[Sertifika işlemini al](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Durum: tamamlandı, hata bilgileri ile başarısız oldu veya iptal edildi  
      -   Oluşturma gecikmesi nedeniyle, bir iptal işlemi başlatılabilir. İptal etme etkili olabilir veya olmayabilir.  

### <a name="network-security-and-access-policies-associated-with-integrated-ca"></a>Tümleşik CA ile ilişkili ağ güvenliği ve erişim ilkeleri
Key Vault hizmet istekleri CA 'ya gönderir (giden trafik). Bu nedenle, Güvenlik Duvarı etkin anahtar kasaları ile tamamen uyumludur. Key Vault, CA ile erişim ilkelerini paylaşmaz. CA imzalama isteklerini bağımsız kabul edecek şekilde yapılandırılmalıdır. [Güvenilen CA 'yı tümleştirme Kılavuzu](./how-to-integrate-certificate-authority.md)

## <a name="import-a-certificate"></a>Sertifikayı içeri aktar  
 Alternatif olarak, bir sertifika Key Vault – PFX veya ped içine aktarılabilir.  

 Sertifikayı içeri aktar – bir Pee veya PFX 'nin diskte olması ve bir özel anahtara sahip olması gerekir. 
-   Şunları belirtmeniz gerekir: kasa adı ve sertifika adı (ilke isteğe bağlıdır)

-   PEK/PFX dosyaları, KV 'nin sertifika ilkesini doldurmak için ayrıştırabileceği ve kullanabileceği öznitelikleri içerir. Bir sertifika ilkesi zaten belirtilmişse, KV verileri PFX/ped dosyasından eşleştirmeye çalışır.  

-   İçeri aktarma son olduktan sonra, sonraki işlemler yeni ilkeyi (yeni sürümler) kullanır.  

-   Başka işlemler yoksa Key Vault ilk şey bir süre sonu bildirimi gönderir. 

-   Ayrıca, Kullanıcı ilkeyi düzenleyebilir ve içeri aktarma sırasında işlevsel olan, ancak içeri aktarma sırasında hiçbir bilgi belirtilmediğinde varsayılanları içerir. Örn. veren bilgisi yok  

### <a name="formats-of-import-we-support"></a>Destekduğumuz Içeri aktarma biçimleri
Azure Key Vault, sertifikaları anahtar kasasına aktarmak için. pek ve. pfx sertifika dosyalarını destekler.
PEK dosya biçimi için aşağıdaki Içeri aktarma türünü destekliyoruz. PKCS # 8 kodlamalı, şifrelenmemiş bir anahtarla birlikte, aşağıdaki gibi tek bir pek kodlu sertifika

-----SERTIFIKAYı----------SON SERTIFIKA-----BAŞLAT

ÖZEL ANAHTAR----------SON ÖZEL ANAHTARA-----BAŞLA-----

Sertifikayı içeri aktarırken, anahtarın dosyanın içine eklendiğinden emin olmanız gerekir. Özel anahtarınız farklı bir biçimde ayrı olarak varsa, anahtarı sertifikayla birleştirmeniz gerekir. Bazı sertifika yetkilileri sertifikaları farklı biçimlerde sağlar, bu nedenle sertifikayı içeri aktarmadan önce, bunların. ped veya. pfx biçiminde olduklarından emin olun. 

### <a name="formats-of-merge-csr-we-support"></a>Destekdiğimiz birleştirme CSR biçimleri
AKV, 2 PEM tabanlı biçimleri destekler. Tek bir PKCS # 8 kodlu sertifikayı veya Base64 kodlamalı bir P7B (CA tarafından imzalanan sertifika zinciri) birleştirebilirsiniz 

-----SERTIFIKAYı----------SON SERTIFIKA-----BAŞLAT


## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Key Vault ile iş ortağı olmayan bir CA ile sertifika oluşturma  
 Bu yöntem, Key Vault iş ortağı sağlayıcılardan diğer CA 'larla çalışmaya olanak sağlar. Bu, kuruluşunuzun tercih ettiği bir CA ile çalışabilmesi anlamına gelir.  

![Kendi sertifika yetkilinizle bir sertifika oluşturun](../media/certificate-authority-1.png)  

 Aşağıdaki adım açıklamaları, önceki diyagramdaki yeşil bir şekilde açıklanan adımlara karşılık gelir.  

  (1)-Yukarıdaki diyagramda uygulamanız, Anahtar Kasanızda bir anahtar oluşturarak başlayan bir sertifika oluşturur.  

  (2)-Key Vault uygulamanıza bir sertifika Imzalama Isteği (CSR) döndürür.  

  (3)-uygulamanız CSR 'yi seçtiğiniz CA 'ya geçirir.  

  (4)-seçtiğiniz CA, bir x509 sertifikası ile yanıt verir.  

  (5)-uygulamanız, CA 'nızdan x509 sertifikası birleşmesi ile yeni sertifika oluşturmayı tamamlar.
