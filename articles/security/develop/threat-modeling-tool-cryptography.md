---
title: Şifreleme-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Threat Modeling Tool kullanıma sunulan tehditler için azaltmaları
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: c9116472af5b400ded0fea24f98b07bad9d9039b
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728198"
---
# <a name="security-frame-cryptography--mitigations"></a>Güvenlik çerçevesi: Şifreleme | Karşı 

| Ürün/hizmet | Makale |
| --------------- | ------- |
| **Web uygulaması** | <ul><li>[Yalnızca onaylanmış simetrik blok şifrelemeleri ve anahtar uzunlukları kullanın](#cipher-length)</li><li>[Simetrik şifrelemeler için onaylanan blok şifre modlarını ve başlatma vektörlerini kullanma](#vector-ciphers)</li><li>[Onaylanan asimetrik algoritmaları, anahtar uzunluklarını ve doldurmayı kullanın](#padding)</li><li>[Onaylanan rastgele sayı oluşturucuları kullanın](#numgen)</li><li>[Simetrik akış şifrelemeleri kullanmayın](#stream-ciphers)</li><li>[Onaylanan MAC/HMAC/anahtarlı karma algoritmaları kullan](#mac-hash)</li><li>[Yalnızca onaylanan şifreleme karma işlevlerini kullan](#hash-functions)</li></ul> |
| **Veritabanı** | <ul><li>[Veritabanındaki verileri şifrelemek için güçlü şifreleme algoritmaları kullanma](#strong-db)</li><li>[SSIS paketlerinin şifrelenmesi ve dijital olarak imzalanması gerekir](#ssis-signed)</li><li>[Kritik veritabanına dijital imza ekle securables](#securables-db)</li><li>[SQL Server EKM 'yi kullanarak şifreleme anahtarlarını koruma](#ekm-keys)</li><li>[Şifreleme anahtarlarının veritabanı altyapısına açığa çıkarılmamalıdır, AlwaysEncrypted özelliğini kullanın](#keys-engine)</li></ul> |
| **IoT cihazı** | <ul><li>[Şifreleme anahtarlarını IoT cihazında güvenli bir şekilde depolayın](#keys-iot)</li></ul> | 
| **IoT bulut ağ geçidi** | <ul><li>[IoT Hub kimlik doğrulaması için rastgele bir simetrik anahtar oluşturun](#random-hub)</li></ul> | 
| **Dynamics CRM mobil Istemcisi** | <ul><li>[Bir kullanım PIN 'ı gerektiren bir cihaz yönetim ilkesinin bulunduğundan emin olun ve uzaktan Wonu yeniden ping işlemi sağlar](#pin-remote)</li></ul> | 
| **Dynamics CRM Outlook Istemcisi** | <ul><li>[PIN/parola/otomatik kilit gerektiren bir cihaz yönetimi ilkesinin bulunduğundan emin olun ve tüm verileri şifreler (ör. BitLocker)](#bitlocker)</li></ul> | 
| **Kimlik sunucusu** | <ul><li>[Kimlik sunucusu kullanılırken imzalama anahtarlarının devralındığından emin olun](#rolled-server)</li><li>[Kimlik sunucusunda şifreli olarak tanımlayıcı istemci KIMLIĞI, istemci gizli anahtarı kullanıldığından emin olun](#client-server)</li></ul> | 

## <a id="cipher-length"></a>Yalnızca onaylanmış simetrik blok şifrelemeleri ve anahtar uzunlukları kullanın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | <p>Ürünlerin yalnızca, kuruluşunuzdaki şifre Danışmanı tarafından açıkça onaylanmış olan simetrik blok şifrelemeleri ve ilişkili anahtar uzunluklarını kullanması gerekir. Microsoft 'ta onaylanan simetrik algoritmalar aşağıdaki blok şifrelemelerini içerir:</p><ul><li>Yeni kod AES-128, AES-192 ve AES-256 kabul edilebilir</li><li>Mevcut kodla geriye dönük uyumluluk için üç tuşlu 3DES kabul edilebilir</li><li>Simetrik blok şifrelemeleri kullanan ürünler için:<ul><li>Yeni kod için Gelişmiş Şifreleme Standardı (AES) gerekir</li><li>Geriye dönük uyumluluk için mevcut kodda üç tuşlu üçlü veri şifreleme standardı (3DES) izin verilir</li><li>RC2, DES, 2 anahtar 3DES, DESX ve Skipjak dahil diğer tüm blok şifrelemeleri yalnızca eski verilerin şifresini çözmek için kullanılabilir ve şifreleme için kullanılırsa değiştirilmelidir</li></ul></li><li>Simetrik blok şifreleme algoritmaları için en az 128 bit anahtar uzunluğu gereklidir. Yeni kod için önerilen tek blok şifreleme algoritması AES 'dir (AES-128, AES-192 ve AES-256 hepsi kabul edilebilir)</li><li>Mevcut kodda zaten kullanılıyorsa üç tuşlu 3DES Şu anda kabul edilebilir. AES 'e geçiş önerilir. DES, DESX, RC2 ve Skipjak artık güvenli olarak kabul edilmez. Bu algoritmalar yalnızca geriye dönük uyumluluk için mevcut verilerin şifresini çözmek için kullanılabilir ve verilerin önerilen bir blok şifresi kullanılarak yeniden şifrelenmesi gerekir</li></ul><p>Tüm simetrik blok şifrelemeleri, uygun bir başlatma vektörü (IV) kullanılması gereken onaylanmış bir şifre moduyla kullanılmalıdır. Uygun bir IV, genellikle rastgele bir sayıdır ve hiçbir şekilde sabit değer değildir</p><p>Kuruluşunuzun şifre panosu gözden geçirmesinden sonra, eski veya aksi onaylanmamış şifre algoritmalarının ve mevcut verileri okumak için daha küçük anahtar uzunluklarının kullanılması (yeni veri yazma aksine) izin veriliyor olabilir. Bununla birlikte, bu gereksinimle ilgili bir özel durum dosyası oluşturmanız gerekir. Ayrıca, kurumsal dağıtımlarda, verileri okumak için zayıf şifre kullanıldığında, ürünler uyarı yöneticilerini göz önünde bulundurmalıdır. Bu uyarılar açıklayıcı ve eyleme dönüştürülebilir olmalıdır. Bazı durumlarda, zayıf şifreleme kullanımını grup ilkesi denetlemek uygun olabilir</p><p>Yönetilen şifreleme çevikliği için izin verilen .NET algoritmaları (tercih sırasına göre)</p><ul><li>AesCng (FIPS uyumlu)</li><li>Authenticatedadescng (FIPS uyumlu)</li><li>AESCryptoServiceProvider (FIPS uyumlu)</li><li>AESManaged (FIPS uyumlu olmayan)</li></ul><p>Bu algoritmalardan hiçbirinin, `SymmetricAlgorithm.Create` Machine. config dosyasında değişiklik yapmadan veya `CryptoConfig.CreateFromName` yöntemler aracılığıyla belirtibileceğine lütfen emin olun. Ayrıca .NET 3,5 `AesCng` ' den önceki .net sürümlerindeki AES, ve CodePlex aracılığıyla > ve `RijndaelManaged`temel alınan işletim `AuthenticatedAesCng` sisteminde CNG iste ' nin adlandırıldığını unutmayın.</p>

## <a id="vector-ciphers"></a>Simetrik şifrelemeler için onaylanan blok şifre modlarını ve başlatma vektörlerini kullanma

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | Tüm simetrik blok şifrelemeleri, onaylanmış bir simetrik şifreleme moduyla kullanılmalıdır. Yalnızca onaylanan modlar CBC ve CTS ' dir. Özellikle, elektronik kod kitabı (ECB) işlem modu kaçınılmalıdır; ECB kullanımı, kuruluşunuzun şifre Panosu İncelemesini gerektirir. Tüm OFB, CFB, MRK, CCM ve GCM veya diğer şifreleme modunun kullanımı, kuruluşunuzun şifre panosu tarafından incelenmelidir. "Akış şifrelemeleri modları" gibi blok şifrelemeleri ile aynı başlatma vektörünün (IV) yeniden kullanılması, şifreli verilerin açığa kaybolmasına neden olabilir. Tüm simetrik blok şifrelemeleri uygun bir başlatma vektörü (IV) ile de kullanılmalıdır. Uygun bir IV, şifreli olarak tanımlayıcı, rastgele bir sayı ve hiçbir şekilde sabit değer değildir. |

## <a id="padding"></a>Onaylanan asimetrik algoritmaları, anahtar uzunluklarını ve doldurmayı kullanın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | <p>Yasaklanmış şifreleme algoritmalarının kullanımı, ürün güvenliğine önemli risk getirir ve kaçınılması gerekir. Ürünlerin yalnızca, kuruluşunuzun şifre panosu tarafından açıkça onaylanan şifreleme algoritmalarını ve ilişkili anahtar uzunluklarını ve doldurmayı kullanması gerekir.</p><ul><li>**RSA-** şifreleme, anahtar değişim ve imza için kullanılabilir. RSA şifrelemesi yalnızca OAEP veya RSA-Kee doldurma modlarını kullanmalıdır. Mevcut kod, yalnızca uyumluluk için PKCS #1 v 1.5 doldurma modunu kullanabilir. Null Padding kullanımı açıkça yasaklanmış. Anahtarlar > = 2048 bit yeni kod için gereklidir. Mevcut kod, kuruluşunuzun şifreleme panosu tarafından bir gözden geçirdikten sonra yalnızca geriye dönük uyumluluk için 2048 bit < tuşlarını destekleyebilir. Anahtarlar < 1024 bitleri yalnızca eski verilerin şifresini çözmek/doğrulamak için kullanılabilir ve şifreleme veya imzalama işlemleri için kullanılırsa değiştirilmelidir</li><li>**ECDSA-** yalnızca imza için kullanılabilir. Yeni kod için > = 256 bit anahtarlı ECDSA gereklidir. ECDSA tabanlı imzaların üç NıST onaylı eğrilerden birini kullanması gerekir (P-256, P-384 veya P521). Kapsamlı olarak çözümlenen eğriler, yalnızca kuruluşunuzun şifre panosu ile bir gözden geçirme sonrasında kullanılabilir.</li><li>**ECDH-** yalnızca anahtar değişimi için kullanılabilir. Yeni kod için > = 256 bit anahtarlarla ECDH gereklidir. ECDH tabanlı anahtar değişimi üç NıST onaylı eğrilerden birini kullanmalıdır (P-256, P-384 veya P521). Kapsamlı olarak çözümlenen eğriler, yalnızca kuruluşunuzun şifre panosu ile bir gözden geçirme sonrasında kullanılabilir.</li><li>**Dsa** , kuruluşunuzun şifre panosundan İnceleme ve onay sonrasında kabul edilebilir. Kuruluşunuzun şifre Panosu İncelemesini zamanlamak için güvenlik danışmanınıza başvurun. DSA 'niz onaylanmışsa, 2048 bitten daha düşük olan anahtarların kullanımını yasaklamanıza gerek olacağını unutmayın. CNG, 2048 bit ve Windows 8 itibariyle daha büyük bir anahtar uzunluğu destekler.</li><li>**Diffie-Hellman-** yalnızca oturum anahtarı yönetimi için kullanılabilir. Anahtar uzunluğu > = 2048 bit yeni kod için gereklidir. Mevcut kod, kuruluşunuzun şifreleme panosu tarafından bir gözden geçirdikten sonra, yalnızca geriye dönük uyumluluk için < 2048 bitlik anahtar uzunluklarını destekleyebilir. 1024 bit < anahtarlar kullanılamıyor olabilir.</li><ul>

## <a id="numgen"></a>Onaylanan rastgele sayı oluşturucuları kullanın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | <p>Ürünlerin onaylanmış rastgele sayı oluşturucuları kullanması gerekir. C çalışma zamanı işlev S_SAYI_ÜRET 'leri, .NET Framework Class System. Random veya GetTickCount gibi sistem işlevleri, bu nedenle hiçbir zaman bu kodda kullanılmamalıdır. Çift eliptik eğri rastgele sayı Oluşturucu (DUAL_EC_DRBG) algoritması kullanımı yasaktır</p><ul><li>**CNG-** BCryptGenRandom (çağıran, 0 ' dan büyük herhangi bir ıRQL üzerinde (yani, PASSIVE_LEVEL]) çalıştırmadığı takdirde BCRYPT_USE_SYSTEM_PREFERRED_RNG bayrağını kullanmanız önerilir</li><li>**CAPI-** CryptGenRandom</li><li>**Win32/64-** RtlGenRandom (yeni uygulamalar BCryptGenRandom veya CryptGenRandom) * rand_s * SystemPrng kullanmalıdır (çekirdek modu için)</li><li>**. NET-** RNGCryptoServiceProvider veya RNGCng</li><li>**Windows Mağazası uygulamaları-** Windows. Security. Cryptography. CryptographicBuffer. GenerateRandom veya. GenerateRandomNumber</li><li>**Apple OS X (10.7 +)/iOS (2.0 +)-** int secrandomcopybytes (secrasgeleref Random, size_t Count, uint8_t \*bayt)</li><li>**Apple OS X (< 10.7)-** Rastgele sayıları almak için/dev/random kullanın</li><li>**Java (Google Android Java kodu dahil)-** Java. Security. SecureRandom sınıfı. Android 4,3 (Jelly çekirdeklere) için geliştiricilerin, Android tarafından önerilen geçici çözümü izlemesi ve uygulamalarını/dev/urandom veya/dev/random öğesinden entropi ile açıkça başlatması için uygulamaları güncelleştirmesi gerektiğini unutmayın</li></ul>|

## <a id="stream-ciphers"></a>Simetrik akış şifrelemeleri kullanmayın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | RC4 gibi simetrik akış şifrelemeleri kullanılmamalıdır. Simetrik akış şifrelemeleri yerine, ürünler bir blok şifresi, özellikle de en az 128 bitlik bir anahtar uzunluğu ile AES kullanır. |

## <a id="mac-hash"></a>Onaylanan MAC/HMAC/anahtarlı karma algoritmaları kullan

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | <p>Ürünlerin yalnızca onaylanmış ileti kimlik doğrulama kodu (MAC) veya karma tabanlı ileti kimlik doğrulama kodu (HMAC) algoritmalarını kullanması gerekir.</p><p>İleti kimlik doğrulama kodu (MAC), alıcının hem gönderenin kimliğini hem de gizli anahtar kullanarak iletinin bütünlüğünü doğrulamasına izin veren bir iletiye eklenmiş bir bilgi parçasıdır. Tüm temeldeki karma veya simetrik şifreleme algoritmalarının de kullanım için onaylandığı sürece, karma tabanlı MAC ([HMAC](https://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) veya [blok şifre tabanlı Mac](https://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) kullanımı kullanılabilir; Şu anda bu, HMAC-SHA2 işlevlerini (HMAC-SHA256, HMAC-SHA384 ve HMAC-SHA512 olur) ve CMAC/OMAC1 ve OMAC2 şifre tabanlı Mac 'ler (AES tabanlı) içerir.</p><p>HMAC-SHA1 kullanımı platform uyumluluğu için kullanılabilir olabilir, ancak bu yordama bir özel durum yazmanız ve kuruluşunuzun şifre incelemesini daha az gitmeniz gerekir. HMAC 'lere 128 bitten daha az kesilmesine izin verilmez. Anahtar ve verileri karma hale getirmek için müşteri yöntemlerini kullanmak onaylanmamış ve kullanılmadan önce kuruluşunuzun şifre panosu incelemesine gitmelidir.</p>|

## <a id="hash-functions"></a>Yalnızca onaylanan şifreleme karma işlevlerini kullan

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | <p>Ürünlerin SHA-2 karma algoritmaları ailesini (SHA256, SHA384 ve SHA512 olur) kullanması gerekir. Daha kısa MD5 karması ile tasarlanan bir veri yapısını göz önünde bulundurmak için 128 bitlik çıkış uzunluğu gibi daha kısa bir karma gerekliyse, ürün ekipleri SHA2 karmalarından birini kesebilir (genellikle SHA256). SHA384 'in kesilmiş bir sürümü olduğunu unutmayın. 128 bitten az güvenlik nedeniyle şifreleme karmalarının kesilmesine izin verilmez. Yeni kod MD2, MD4, MD5, SHA-0, SHA-1 veya RIPEMD karma algoritmalarını kullanmamalıdır. Karma çakışmaları bu algoritmalarda etkili bir şekilde uygulanabilir ve bu da onları etkin bir şekilde ayırır.</p><p>Yönetilen şifreleme çevikliği için izin verilen .NET karma algoritmaları (tercih sırasına göre):</p><ul><li>SHA512Cng (FIPS uyumlu)</li><li>SHA384Cng (FIPS uyumlu)</li><li>SHA256Cng (FIPS uyumlu)</li><li>SHA512Managed (FIPS uyumlu değil) (HashAlgorithm. Create veya CryptoConfig. CreateFromName çağrılarında SHA512 olur as algoritma adı kullanın)</li><li>SHA384Managed (FIPS uyumlu değil) (HashAlgorithm. Create veya CryptoConfig. CreateFromName çağrılarında SHA384 as algoritma adı kullanın)</li><li>SHA256Managed (FIPS uyumlu değil) (HashAlgorithm. Create veya CryptoConfig. CreateFromName çağrılarında SHA256 as algoritma adı kullanın)</li><li>SHA512CryptoServiceProvider (FIPS uyumlu)</li><li>SHA256CryptoServiceProvider (FIPS uyumlu)</li><li>SHA384CryptoServiceProvider (FIPS uyumlu)</li></ul>| 

## <a id="strong-db"></a>Veritabanındaki verileri şifrelemek için güçlü şifreleme algoritmaları kullanma

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Database | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [Şifreleme algoritması seçme](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Adımları** | Şifreleme algoritmaları, yetkisiz kullanıcılar tarafından kolayca geri alınamaz veri dönüştürmeleri tanımlar. SQL Server, yöneticilerin ve geliştiricilerin DES, Üçlü DES, TRIPLE_DES_3KEY, RC2, RC4, 128-bit RC4, DESX, 128-bit AES, 192 bit AES ve 256 bit AES gibi çeşitli algoritmalar arasından seçim yapmasına olanak sağlar |

## <a id="ssis-signed"></a>SSIS paketlerinin şifrelenmesi ve dijital olarak imzalanması gerekir

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Database | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Dijital Imzalar, [tehdit ve güvenlik açığı azaltma (Tümleştirme Hizmetleri)](https://msdn.microsoft.com/library/bb522559.aspx) [Ile paketlerin kaynağını belirler](https://msdn.microsoft.com/library/ms141174.aspx) |
| **Adımları** | Paketin kaynağı, paketi oluşturan tek veya kuruluştur. Bilinmeyen veya güvenilmeyen bir kaynaktan bir paket çalıştırmak riskli olabilir. SSIS paketlerinin izinsiz değişikliklere engel olmak için dijital imzaların kullanılması gerekir. Ayrıca, depolama/aktarım sırasında paketlerin gizliliğini sağlamak için SSIS paketlerinin şifrelenmesi gerekir |

## <a id="securables-db"></a>Kritik veritabanına dijital imza ekle securables

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Database | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [Imza Ekle (Transact-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **Adımları** | Kritik bir veritabanının bütünlüğünden doğrulanması gereken durumlarda, dijital imzaların kullanılması gerekir. Saklı yordam, işlev, derleme veya tetikleyici gibi Veritabanı securables dijital olarak imzalanabilir. Bunun yararlı olabilecek bir örneği aşağıda verilmiştir: ISV (bağımsız yazılım satıcısı), müşterilerinin birine sunulan bir yazılıma destek sunmamıza izin verir. Destek sağlamadan önce ISV, yazılımdaki bir veritabanının yanlışlıkla veya kötü niyetli bir girişim üzerinde değişiklik yapılmamasını sağlamak istiyor. Güvenli kılınabilir dijital olarak imzalanmışsa ISV, dijital imzasını doğrulayabilir ve bütünlüğünü doğrulayabilirler.| 

## <a id="ekm-keys"></a>SQL Server EKM 'yi kullanarak şifreleme anahtarlarını koruma

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Database | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [Genişletilebilir anahtar yönetimi (ekm) SQL Server](https://msdn.microsoft.com/library/bb895340) [Azure Key Vault kullanarak Genişletilebilir anahtar yönetimi (SQL Server)](https://msdn.microsoft.com/library/dn198405) |
| **Adımları** | SQL Server Genişletilebilir anahtar yönetimi, veritabanı dosyalarını koruyan şifreleme anahtarlarının akıllı kart, USB cihazı veya EKM/HSM modülü gibi bir yerleşik cihazda depolanmasını sağlar. Bu Ayrıca, veritabanı yöneticilerinin (sysadmin grubu üyeleri hariç) veri korumasına da izin vermez. Veriler, yalnızca veritabanı kullanıcısının dış EKM/HSM modülüne erişimi olan şifreleme anahtarları kullanılarak şifrelenebilir. |

## <a id="keys-engine"></a>Şifreleme anahtarlarının veritabanı altyapısına açığa çıkarılmamalıdır, AlwaysEncrypted özelliğini kullanın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Database | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | SQL Azure, Onprea |
| **Öznitelikler**              | SQL sürümü-V12, MsSQL2016 |
| **Başvur**              | [Always Encrypted (veritabanı altyapısı)](https://msdn.microsoft.com/library/mt163865) |
| **Adımları** | Always Encrypted, Azure SQL veritabanı veya SQL Server veritabanlarında depolanan kredi kartı numaraları veya ulusal tanımlama numaraları (örn. ABD sosyal güvenlik numaraları) gibi hassas verileri korumak için tasarlanmış bir özelliktir. Always Encrypted istemcilerin istemci uygulamalarında hassas verileri şifrelemesine olanak tanır ve şifreleme anahtarlarını veritabanı altyapısına (SQL veritabanı veya SQL Server) hiç açığa çıkarmayın. Sonuç olarak Always Encrypted, verilerin sahibi olan (ve görüntüleyebilen) ve verileri yöneten (ancak erişimi olmayan) aralarında bir ayrım sağlar |

## <a id="keys-iot"></a>Şifreleme anahtarlarını IoT cihazında güvenli bir şekilde depolayın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | IoT Cihazı | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Cihaz işletim sistemi-Windows IoT Core, cihaz bağlantısı-Azure IoT cihaz SDK 'Ları |
| **Başvur**              | [Windows IoT Core üzerinde TPM](https://developer.microsoft.com/windows/iot/docs/tpm), [Windows ıOT Core 'ta TPM ayarlama](https://docs.microsoft.com/windows/iot-core/secure-your-device/setuptpm), [Azure IoT cihaz SDK TPM](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Adımları** | TPM veya akıllı kart yongaları gibi donanım korumalı depolamada güvenli şekilde simetrik veya sertifika özel anahtarları. Windows 10 IoT Core TPM kullanıcısını destekler ve kullanılabilecek birkaç uyumlu TPMs vardır: https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpm. Üretici yazılımı veya ayrı TPM kullanılması önerilir. Yazılım TPM 'si yalnızca geliştirme ve test amaçları için kullanılmalıdır. TPM kullanılabilir olduğunda ve anahtarlar sağlandığında, bu belirteci oluşturan kod, içindeki herhangi bir hassas bilgileri sabit kodlamadan yazılmalıdır. | 

### <a name="example"></a>Örnek
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Görüldüğü gibi, kodda cihaz birincil anahtarı yok. Bunun yerine, 0 yuvada TPM 'de depolanır. TPM cihazı, IoT Hub bağlanmak için kullanılan kısa süreli bir SAS belirteci oluşturur. 

## <a id="random-hub"></a>IoT Hub kimlik doğrulaması için rastgele bir simetrik anahtar oluşturun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | IoT bulut ağ geçidi | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Ağ Geçidi seçimi-Azure IoT Hub |
| **Başvur**              | Yok  |
| **Adımları** | IoT Hub bir cihaz kimliği kayıt defteri ve bir cihaz sağlanırken, otomatik olarak rastgele bir simetrik anahtar oluşturur. Kimlik doğrulama için kullanılan anahtarı oluşturmak için Azure IoT Hub Identity kayıt defteri 'nin bu özelliğinin kullanılması önerilir. IoT Hub, cihaz oluştururken bir anahtarın belirtilmesini de sağlar. Cihaz sağlama sırasında IoT Hub dışında bir anahtar oluşturulursa, rastgele bir simetrik anahtar veya en az 256 bit oluşturmanız önerilir. |

## <a id="pin-remote"></a>Bir kullanım PIN 'ı gerektiren bir cihaz yönetim ilkesinin bulunduğundan emin olun ve uzaktan Wonu yeniden ping işlemi sağlar

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Dynamics CRM mobil Istemcisi | 
| **SDL aşaması**               | Dağıtım |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | Bir kullanım PIN 'ı gerektiren bir cihaz yönetim ilkesinin bulunduğundan emin olun ve uzaktan Wonu yeniden ping işlemi sağlar |

## <a id="bitlocker"></a>PIN/parola/otomatik kilit gerektiren bir cihaz yönetimi ilkesinin bulunduğundan emin olun ve tüm verileri şifreler (ör. BitLocker)

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Dynamics CRM Outlook Istemcisi | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | PIN/parola/otomatik kilit gerektiren bir cihaz yönetimi ilkesinin bulunduğundan emin olun ve tüm verileri şifreler (ör. BitLocker) |

## <a id="rolled-server"></a>Kimlik sunucusu kullanılırken imzalama anahtarlarının devralındığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Kimlik sunucusu | 
| **SDL aşaması**               | Dağıtım |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [Kimlik sunucusu-anahtarlar, Imzalar ve şifreleme](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Adımları** | Kimlik sunucusu kullanılırken imzalama anahtarlarının devralındığından emin olun. Başvurular bölümündeki bağlantı, bunun nasıl planlanacağı, kimlik sunucusuna bağlı olan uygulamalara kesintiye neden olmadan nasıl planlanacağı açıklanmaktadır. |

## <a id="client-server"></a>Kimlik sunucusunda şifreli olarak tanımlayıcı istemci KIMLIĞI, istemci gizli anahtarı kullanıldığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Kimlik sunucusu | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | <p>Şifreli istemci KIMLIĞININ, istemci parolasının kimlik sunucusu 'nda kullanıldığından emin olun. İstemci KIMLIĞI ve gizli dizi üretilirken aşağıdaki kılavuzlar kullanılmalıdır:</p><ul><li>İstemci KIMLIĞI olarak rastgele bir GUID oluşturma</li><li>Gizli dizi olarak şifreli olarak rastgele 256 bitlik bir anahtar oluşturma</li></ul>|
