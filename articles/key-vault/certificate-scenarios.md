---
title: Key Vault sertifikalarla çalışmaya başlama
description: Aşağıdaki senaryolarda, Anahtar Kasanızda ilk sertifikanızı oluşturmak için gereken ek adımlar da dahil olmak üzere Key Vault sertifika yönetimi hizmetinin birincil kullanımlarından bazıları ana hatlarıyla verilmiştir.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 338619a13ec3f5fcd0d4fd62cf387f955c556a7c
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70879313"
---
# <a name="get-started-with-key-vault-certificates"></a>Key Vault sertifikalarla çalışmaya başlama
Aşağıdaki senaryolarda, Anahtar Kasanızda ilk sertifikanızı oluşturmak için gereken ek adımlar da dahil olmak üzere Key Vault sertifika yönetimi hizmetinin birincil kullanımlarından bazıları ana hatlarıyla verilmiştir.

Aşağıdakiler aşağıda özetlenmiştir:
- İlk Key Vault sertifikanızı oluşturma
- Key Vault ile iş ortağı olan bir sertifika yetkilisi ile sertifika oluşturma
- Key Vault ile iş ortağı olmayan bir sertifika yetkilisi ile sertifika oluşturma
- Sertifikayı içeri aktar

## <a name="certificates-are-complex-objects"></a>Sertifikalar karmaşık nesnelerdir
Sertifikalar, bir Key Vault sertifikası olarak birbirine bağlı üç ilişkili kaynaktan oluşur; Sertifika meta verileri, anahtar ve gizli dizi.


![Sertifikalar karmaşıktır](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>İlk Key Vault sertifikanızı oluşturma  
 Bir sertifikanın Key Vault (KV) içinde oluşturulabilmesi için önce 1. ve 2. önkoşul adımlarının başarılı bir şekilde gerçekleştirilmesi gerekir ve bu kullanıcı/kuruluş için bir Anahtar Kasası bulunmalıdır.  

**Adım 1** -sertifika YETKILISI (CA) sağlayıcıları  
-   BT Yöneticisi, PKI Yöneticisi veya CA 'larla hesapları yöneten herkes, belirli bir şirket için (örn. Contoso) Key Vault sertifikaları kullanmanın bir önkoşuludur.  
    Aşağıdaki CA 'Lar Key Vault ile geçerli iş ortağı sağlayıcılarıdır:  
    -   DigiCert-Key Vault, DigiCert ile OV SSL sertifikaları sunmaktadır.  
    -   GlobalSign-Key Vault GlobalSign ile OV SSL sertifikaları sunmaktadır.  

**2. adım** -CA sağlayıcısı için bir hesap yöneticisi, Key Vault aracılığıyla SSL sertifikalarını kaydetmek, yenilemek ve kullanmak için Key Vault tarafından kullanılacak kimlik bilgilerini oluşturur.

**3. adım** -CA 'ya bağlı olarak, sertifikalara sahip olan bir contoso çalışanı (Key Vault kullanıcısı) ile birlikte bir contoso Yöneticisi, yöneticiden bir sertifika alabılır veya CA ile doğrudan hesaptan bir sertifika alabilir.  

- Bir [sertifika veren kaynağı ayarlayarak](/rest/api/keyvault/setcertificateissuer/setcertificateissuer) bir anahtar kasasına kimlik bilgisi ekleme işlemi başlatın. Sertifika veren, Azure Key Vault (KV) ile bir Certificateıssuer kaynağı olarak temsil edilen bir varlıktır. Bir KV sertifikasının kaynağı hakkında bilgi sağlamak için kullanılır; verenin adı, sağlayıcı, kimlik bilgileri ve diğer yönetim ayrıntıları.
  - Örn. Mydigicertısuer  
    -   Sağlayıcı  
    -   Kimlik bilgileri – CA hesabı kimlik bilgileri. Her CA 'nın kendine özgü verileri vardır.  

    CA sağlayıcılarıyla hesap oluşturma hakkında daha fazla bilgi için [Key Vault blogdaki](https://aka.ms/kvcertsblog)ilgili gönderisine bakın.  

**Adım 3,1** -bildirimler için [sertifika kişilerini](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) ayarlama. Bu, Key Vault kullanıcısına yönelik kişdir. Key Vault bu adımı zorlamaz.  

Bu süreç, 3,1. adım ile bir kerelik işlemidir.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Key Vault ile CA iş ortağı ile sertifika oluşturma

![Key Vault iş ortağı sertifika yetkilisi ile sertifika oluşturma](media/certificate-authority-2.png)

**4. adım** -aşağıdaki açıklamalar, önceki diyagramdaki yeşil numaralı adımlara karşılık gelir.  
  (1)-Yukarıdaki diyagramda uygulamanız, Anahtar Kasanızda bir anahtar oluşturarak başlayan bir sertifika oluşturur.  
  (2)-Key Vault CA 'ya bir SSL sertifikası Isteği gönderir.  
  (3)-uygulamanız, sertifika tamamlaması için Key Vault bir döngüde ve bekleme sürecinde yoklar. Key Vault, CA 'nın x509 sertifikasıyla yanıtını aldığında sertifika oluşturma işlemi tamamlanır.  
  (4)-CA, bir x509 SSL sertifikasıyla Key Vault SSL sertifikası Isteğine yanıt verir.  
  (5)-yeni sertifika oluşturma, CA için x509 sertifikasının birleşmesi ile tamamlanır.  

  Key Vault User: bir ilke belirterek bir sertifika oluşturur

  -   Gerektiğinde yineleyin  
  -   İlke kısıtlamaları  
      -   X509 özellikleri  
      -   Anahtar özellikleri  
      -   Sağlayıcı başvurusu-> Ex. MyDigiCertIssure  
      -   Yenileme bilgileri-> Ex. süre sonu 90 gün önce  

  - Sertifika oluşturma işlemi genellikle zaman uyumsuz bir işlemdir ve sertifika oluşturma işleminin durumu için anahtar kasanızın yoklanmasını içerir.  
[Sertifika işlemini al](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Durum: tamamlandı, hata bilgileri ile başarısız oldu veya iptal edildi  
      -   Oluşturma gecikmesi nedeniyle, bir iptal işlemi başlatılabilir. İptal etme etkili olabilir veya olmayabilir.  

## <a name="import-a-certificate"></a>Sertifikayı içeri aktar  
 Alternatif olarak, bir sertifika Key Vault – PFX veya ped içine aktarılabilir.  

 PEK biçimi hakkında daha fazla bilgi için [anahtarlar, gizli diziler ve sertifikalar hakkında](about-keys-secrets-and-certificates.md)konusunun sertifikalar bölümüne bakın.  

 Sertifikayı içeri aktar – bir Pee veya PFX 'nin diskte olması ve bir özel anahtara sahip olması gerekir. 
-   Şunları belirtmeniz gerekir: kasa adı ve sertifika adı (ilke isteğe bağlıdır)

-   PEK/PFX dosyaları, KV 'nin sertifika ilkesini doldurmak için ayrıştırabileceği ve kullanabileceği öznitelikleri içerir. Bir sertifika ilkesi zaten belirtilmişse, KV verileri PFX/ped dosyasından eşleştirmeye çalışır.  

-   İçeri aktarma son olduktan sonra, sonraki işlemler yeni ilkeyi (yeni sürümler) kullanır.  

-   Başka işlemler yoksa Key Vault ilk şey bir süre sonu bildirimi gönderir. 

-   Ayrıca, Kullanıcı ilkeyi düzenleyebilir ve içeri aktarma sırasında işlevsel olan, ancak içeri aktarma sırasında hiçbir bilgi belirtilmediğinde varsayılanları içerir. Örn. veren bilgisi yok  

### <a name="formats-of-import-we-support"></a>Destekduğumuz Içeri aktarma biçimleri
PEK dosya biçimi için aşağıdaki Içeri aktarma türünü destekliyoruz. PKCS # 8 kodlamalı, şifrelenmemiş bir anahtarla birlikte, aşağıdaki gibi tek bir pek kodlu sertifika

-----SERTIFIKAYI----------SON SERTIFIKA-----BAŞLAT

ÖZEL ANAHTAR----------SON ÖZEL ANAHTARA-----BAŞLA-----

Sertifika birleştirmede 2 pek tabanlı biçimleri destekliyoruz. Tek bir PKCS # 8 kodlu sertifikayı veya Base64 kodlamalı bir P7B dosyasını birleştirebilirsiniz. -----SERTIFIKAYI----------SON SERTIFIKA-----BAŞLAT

Şu anda pek biçimindeki EC anahtarlarını desteklemiyoruz.

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Key Vault ile iş ortağı olmayan bir CA ile sertifika oluşturma  
 Bu yöntem, Key Vault iş ortağı sağlayıcılardan diğer CA 'larla çalışmaya olanak sağlar. Bu, kuruluşunuzun tercih ettiği bir CA ile çalışabilmesi anlamına gelir.  

![Kendi sertifika yetkilinizle bir sertifika oluşturun](media/certificate-authority-1.png)  

 Aşağıdaki adım açıklamaları, önceki diyagramdaki yeşil bir şekilde açıklanan adımlara karşılık gelir.  

  (1)-Yukarıdaki diyagramda uygulamanız, Anahtar Kasanızda bir anahtar oluşturarak başlayan bir sertifika oluşturur.  

  (2)-Key Vault uygulamanıza bir sertifika Imzalama Isteği (CSR) döndürür.  

  (3)-uygulamanız CSR 'yi seçtiğiniz CA 'ya geçirir.  

  (4)-seçtiğiniz CA, bir x509 sertifikası ile yanıt verir.  

  (5)-uygulamanız, CA 'nızdan x509 sertifikası birleşmesi ile yeni sertifika oluşturmayı tamamlar.

## <a name="see-also"></a>Ayrıca Bkz.

- [Anahtarlar, gizli diziler ve sertifikalar hakkında](about-keys-secrets-and-certificates.md)
