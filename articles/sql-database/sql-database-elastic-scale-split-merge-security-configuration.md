---
title: Bölünmüş birleştirme güvenlik yapılandırması
description: Elastik ölçek için bölünmüş/birleştirme hizmetiyle şifreleme için x409 sertifikaları ayarlayın.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: a916645f153f73a98e7fc5d4046bdf557e8acf2b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823523"
---
# <a name="split-merge-security-configuration"></a>Bölünmüş birleştirme güvenlik yapılandırması

Bölünmüş/birleştirme hizmetini kullanmak için güvenliği doğru şekilde yapılandırmanız gerekir. Hizmet, Microsoft Azure SQL Veritabanı elastik ölçek özelliğinin bir parçasıdır. Daha fazla bilgi için bkz. [elastik ölçek bölünmüş ve birleştirme hizmeti öğreticisi](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Sertifikaları yapılandırma

Sertifikalar iki şekilde yapılandırılır. 

1. [SSL sertifikasını yapılandırmak için](#to-configure-the-ssl-certificate)
2. [Istemci sertifikalarını yapılandırmak için](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Sertifikaları almak için

Sertifikalar, genel sertifika yetkililerinden (CA 'Lar) veya [Windows sertifika hizmeti](https://msdn.microsoft.com/library/windows/desktop/aa376539.aspx)'nden alınabilir. Bunlar, sertifikaları almak için tercih edilen yöntemlerdir.

Bu seçenekler yoksa, **otomatik olarak imzalanan sertifikalar**oluşturabilirsiniz.

## <a name="tools-to-generate-certificates"></a>Sertifika oluşturmaya yönelik araçlar

* [MakeCert. exe](https://msdn.microsoft.com/library/bfsktky3.aspx)
* [Pvk2pfx. exe](https://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Araçları çalıştırmak için

* Visual Studios için bir Geliştirici Komut İstemi, bkz. [Visual Studio komut istemi](https://msdn.microsoft.com/library/ms229859.aspx) 
  
    Yüklüyse, şuraya gidin:
  
        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
* Windows 8.1 WDK Al [: takımları ve araçları indirme](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>SSL sertifikasını yapılandırmak için

İletişimi şifrelemek ve sunucunun kimliğini doğrulamak için bir SSL sertifikası gerekir. Aşağıdaki üç senaryonun en uygun olanını seçin ve tüm adımlarını yürütün:

### <a name="create-a-new-self-signed-certificate"></a>Otomatik olarak imzalanan yeni bir sertifika oluştur

1. [Otomatik olarak Imzalanan sertifika oluşturma](#create-a-self-signed-certificate)
2. [Otomatik olarak Imzalanan SSL sertifikası için PFX dosyası oluştur](#create-pfx-file-for-self-signed-ssl-certificate)
3. [SSL sertifikasını bulut hizmetine yükle](#upload-ssl-certificate-to-cloud-service)
4. [Hizmet yapılandırma dosyasında SSL sertifikası Güncelleştir](#update-ssl-certificate-in-service-configuration-file)
5. [SSL sertifika yetkilisini içeri aktar](#import-ssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Sertifika deposundan var olan bir sertifikayı kullanmak için
1. [Sertifika deposundan SSL sertifikasını dışarı aktar](#export-ssl-certificate-from-certificate-store)
2. [SSL sertifikasını bulut hizmetine yükle](#upload-ssl-certificate-to-cloud-service)
3. [Hizmet yapılandırma dosyasında SSL sertifikası Güncelleştir](#update-ssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Bir PFX dosyasında var olan bir sertifikayı kullanmak için
1. [SSL sertifikasını bulut hizmetine yükle](#upload-ssl-certificate-to-cloud-service)
2. [Hizmet yapılandırma dosyasında SSL sertifikası Güncelleştir](#update-ssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>İstemci sertifikalarını yapılandırmak için
Hizmette isteklerin kimliğini doğrulamak için istemci sertifikaları gereklidir. Aşağıdaki üç senaryonun en uygun olanını seçin ve tüm adımlarını yürütün:

### <a name="turn-off-client-certificates"></a>İstemci sertifikalarını kapat
1. [Istemci sertifikası tabanlı kimlik doğrulamasını kapat](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Otomatik olarak imzalanan yeni istemci sertifikaları verme
1. [Otomatik olarak Imzalanan sertifika yetkilisi oluşturma](#create-a-self-signed-certification-authority)
2. [CA sertifikasını bulut hizmetine yükle](#upload-ca-certificate-to-cloud-service)
3. [Hizmet yapılandırma dosyasında CA sertifikasını Güncelleştir](#update-ca-certificate-in-service-configuration-file)
4. [Istemci sertifikası verme](#issue-client-certificates)
5. [Istemci sertifikaları için PFX dosyaları oluşturma](#create-pfx-files-for-client-certificates)
6. [Istemci sertifikasını içeri aktar](#import-client-certificate)
7. [Istemci sertifikası parmak Izlerini Kopyala](#copy-client-certificate-thumbprints)
8. [Hizmet yapılandırma dosyasında Izin verilen Istemcileri yapılandırma](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Mevcut istemci sertifikalarını kullan
1. [CA ortak anahtarını bul](#find-ca-public-key)
2. [CA sertifikasını bulut hizmetine yükle](#upload-ca-certificate-to-cloud-service)
3. [Hizmet yapılandırma dosyasında CA sertifikasını Güncelleştir](#update-ca-certificate-in-service-configuration-file)
4. [Istemci sertifikası parmak Izlerini Kopyala](#copy-client-certificate-thumbprints)
5. [Hizmet yapılandırma dosyasında Izin verilen Istemcileri yapılandırma](#configure-allowed-clients-in-the-service-configuration-file)
6. [Istemci sertifikası Iptal denetimini yapılandırma](#configure-client-certificate-revocation-check)

## <a name="allowed-ip-addresses"></a>İzin verilen IP adresleri
Hizmet uç noktalarına erişim, belirli IP adresi aralıklarıyla kısıtlanabilir.

## <a name="to-configure-encryption-for-the-store"></a>Mağaza şifrelemesini yapılandırmak için
Meta veri deposunda depolanan kimlik bilgilerini şifrelemek için bir sertifika gerekir. Aşağıdaki üç senaryonun en uygun olanını seçin ve tüm adımlarını yürütün:

### <a name="use-a-new-self-signed-certificate"></a>Otomatik olarak imzalanan yeni bir sertifika kullan
1. [Otomatik olarak Imzalanan sertifika oluşturma](#create-a-self-signed-certificate)
2. [Otomatik olarak Imzalanan şifreleme sertifikası için PFX dosyası oluştur](#create-pfx-file-for-self-signed-ssl-certificate)
3. [Şifreleme sertifikasını bulut hizmetine yükle](#upload-encryption-certificate-to-cloud-service)
4. [Hizmet yapılandırma dosyasında şifreleme sertifikasını Güncelleştir](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Sertifika deposundan var olan bir sertifikayı kullan
1. [Sertifika deposundan şifreleme sertifikasını dışarı aktar](#export-encryption-certificate-from-certificate-store)
2. [Şifreleme sertifikasını bulut hizmetine yükle](#upload-encryption-certificate-to-cloud-service)
3. [Hizmet yapılandırma dosyasında şifreleme sertifikasını Güncelleştir](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>PFX dosyasında var olan bir sertifikayı kullan
1. [Şifreleme sertifikasını bulut hizmetine yükle](#upload-encryption-certificate-to-cloud-service)
2. [Hizmet yapılandırma dosyasında şifreleme sertifikasını Güncelleştir](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>Varsayılan yapılandırma
Varsayılan yapılandırma HTTP uç noktasına tüm erişimi reddeder. Bu uç noktalara yönelik istekler veritabanı kimlik bilgileri gibi hassas bilgileri taşıyabileceğinizden bu, önerilen ayardır.
Varsayılan yapılandırma, HTTPS uç noktasına tüm erişime izin verir. Bu ayar daha fazla kısıtlanmış olabilir.

### <a name="changing-the-configuration"></a>Yapılandırmayı değiştirme
Ve uç nokta için uygulanan erişim denetimi kuralları grubu, **hizmet yapılandırma dosyasının** **\<EndpointAcls >** bölümünde yapılandırılır.

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

Bir erişim denetim grubundaki kurallar, hizmet yapılandırma dosyasının \<AccessControl Name = "" > bölümünde yapılandırılır. 

Biçim, ağ Access Control listeleri belgelerinde açıklanmıştır.
Örneğin, 100.100.0.0 ile 100.100.255.255 arasında yalnızca IP 'Lerin HTTPS uç noktasına erişmesine izin vermek için kurallar şuna benzer:

```xml
<AccessControl name="Retricted">
    <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
    <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
</AccessControl>
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />
</EndpointAcls>
```

## <a name="denial-of-service-prevention"></a>Hizmet reddi engellemesi
Hizmet reddi saldırılarını algılamak ve engellemek için desteklenen iki farklı mekanizma vardır:

* Uzak ana bilgisayar başına eşzamanlı istek sayısını kısıtla (varsayılan olarak kapalı)
* Uzak ana bilgisayar başına erişim oranını kısıtla (varsayılan olarak açık)

Bunlar, IIS 'de dinamik IP güvenliği ile belgelendiği özelliklere dayalıdır. Bu yapılandırmayı değiştirirken aşağıdaki faktörlerden de dikkat edin:

* Uzak ana bilgisayar bilgileri üzerinde proxy ve ağ adresi çevirisi cihazlarının davranışı
* Web rolündeki herhangi bir kaynağa yönelik her istek kabul edilir (örneğin, betikleri, resimleri yükleme vb.)

## <a name="restricting-number-of-concurrent-accesses"></a>Eşzamanlı erişimlerin sayısını kısıtlama
Bu davranışı yapılandıran ayarlar şunlardır:

```xml
<Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
<Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />
```

Bu korumayı etkinleştirmek için DynamicIpRestrictionDenyByConcurrentRequests değerini true olarak değiştirin.

## <a name="restricting-rate-of-access"></a>Erişim oranını kısıtlama
Bu davranışı yapılandıran ayarlar şunlardır:

```xml
<Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
<Setting name="DynamicIpRestrictionMaxRequests" value="100" />
<Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />
```

## <a name="configuring-the-response-to-a-denied-request"></a>Reddedilen bir isteğe yanıt yapılandırma
Aşağıdaki ayar, reddedilen bir istek için yanıtı yapılandırır:

```xml
<Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
```

Desteklenen diğer değerler için IIS 'deki dinamik IP güvenliği belgelerine bakın.

## <a name="operations-for-configuring-service-certificates"></a>Hizmet sertifikalarını yapılandırma işlemleri
Bu konu yalnızca başvuru amaçlıdır. İçinde özetlenen yapılandırma adımlarını izleyin:

* SSL sertifikasını yapılandırma
* İstemci sertifikalarını yapılandırma

## <a name="create-a-self-signed-certificate"></a>Otomatik olarak imzalanan sertifika oluşturma
Yürütme

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha256 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Özelleştirmek için:

* -n hizmet URL 'SI. Joker karakterler ("CN = *. cloudapp. net") ve alternatif adlar ("CN = myservice1. cloudapp. net, CN = myservice2. cloudapp. net") desteklenir.
* -e, sertifika sona erme tarihi ile güçlü bir parola oluşturun ve istendiğinde belirtin.

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>Otomatik olarak imzalanan SSL sertifikası için PFX dosyası oluştur
Yürütme

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Parolayı girin ve sertifikayı şu seçeneklerle dışarı aktarın:

* Evet, özel anahtarı dışarı aktar
* Tüm genişletilmiş özellikleri dışarı aktar

## <a name="export-ssl-certificate-from-certificate-store"></a>Sertifika deposundan SSL sertifikasını dışarı aktar
* Sertifika bul
* Eylemler-> Tüm Görevler-> dışarı aktar... öğesine tıklayın.
* Sertifikayı bir öğesine dışarı aktarın. Bu seçeneklere sahip PFX dosyası:
  * Evet, özel anahtarı dışarı aktar
  * Mümkünse sertifika yolundaki tüm sertifikaları Ekle * tüm genişletilmiş özellikleri dışarı aktar

## <a name="upload-ssl-certificate-to-cloud-service"></a>SSL sertifikasını bulut hizmetine yükle
Sertifikayı var olan veya oluşturulan ile karşıya yükleyin. SSL anahtar çiftiyle PFX dosyası:

* Özel anahtar bilgilerini koruyan parolayı girin

## <a name="update-ssl-certificate-in-service-configuration-file"></a>Hizmet yapılandırma dosyasında SSL sertifikası Güncelleştir
Hizmet yapılandırma dosyasında, bulut hizmetine yüklenen sertifikanın parmak izine sahip aşağıdaki ayarın parmak izi değerini güncelleştirin:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>SSL sertifika yetkilisini içeri aktar
Hizmetle iletişim kuracak tüm hesap/makinedeki bu adımları izleyin:

* Öğesine çift tıklayın. Windows Gezgini 'nde CER dosyası
* Sertifika iletişim kutusunda Sertifikayı Kur... öğesine tıklayın.
* Sertifikayı güvenilen kök sertifika yetkilileri deposuna aktarma

## <a name="turn-off-client-certificate-based-authentication"></a>İstemci sertifikası tabanlı kimlik doğrulamasını kapat
Yalnızca istemci sertifikası tabanlı kimlik doğrulaması desteklenir ve devre dışı bırakıldığında, başka mekanizmalar yerinde değilse (örneğin, Microsoft Azure Sanal Ağ) hizmet uç noktalarına genel erişim izni verilir.

Özelliği devre dışı bırakmak için, hizmet yapılandırma dosyasında bu ayarları false olarak değiştirin:

```xml
<Setting name="SetupWebAppForClientCertificates" value="false" />
<Setting name="SetupWebserverForClientCertificates" value="false" />
```

Ardından, CA sertifikası ayarında aynı parmak izini SSL sertifikası ile kopyalayın:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>Otomatik olarak imzalanan sertifika yetkilisi oluşturma
Sertifika yetkilisi görevi görecek otomatik olarak imzalanan bir sertifika oluşturmak için aşağıdaki adımları yürütün:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha256 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Özelleştirmek için

* -e sertifikanın sona erme tarihi

## <a name="find-ca-public-key"></a>CA ortak anahtarını bul
Tüm istemci sertifikalarının, hizmet tarafından güvenilen bir sertifika yetkilisi tarafından verilmiş olması gerekir. Bulut hizmetine yüklemek için kimlik doğrulaması için kullanılacak istemci sertifikalarını veren sertifika yetkilisinin ortak anahtarını bulun.

Ortak anahtara sahip dosya kullanılamıyorsa, sertifika deposundan dışarı aktarın:

* Sertifika bul
  * Aynı sertifika yetkilisi tarafından verilen bir istemci sertifikası ara
* Sertifikaya çift tıklayın.
* Sertifika iletişim kutusunda sertifika yolu sekmesini seçin.
* Yoldaki CA girişini çift tıklatın.
* Sertifika özelliklerine not alın.
* **Sertifika** iletişim kutusunu kapatın.
* Sertifika bul
  * Yukarıda belirtilen CA 'yı arayın.
* Eylemler-> Tüm Görevler-> dışarı aktar... öğesine tıklayın.
* Sertifikayı bir öğesine dışarı aktarın. Bu seçeneklerle CER:
  * **Hayır, özel anahtarı dışarı aktarma**
  * Mümkünse sertifika yolundaki tüm sertifikaları ekleyin.
  * Tüm genişletilmiş özellikleri dışarı aktarın.

## <a name="upload-ca-certificate-to-cloud-service"></a>CA sertifikasını bulut hizmetine yükle
Sertifikayı var olan veya oluşturulan ile karşıya yükleyin. CA ortak anahtarına sahip CER dosyası.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Hizmet yapılandırma dosyasında CA sertifikasını Güncelleştir
Hizmet yapılandırma dosyasında, bulut hizmetine yüklenen sertifikanın parmak izine sahip aşağıdaki ayarın parmak izi değerini güncelleştirin:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

Aşağıdaki ayarın değerini aynı parmak izine göre güncelleştirin:

```xml
<Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
```

## <a name="issue-client-certificates"></a>İstemci sertifikası verme
Hizmete erişim yetkisi olan her bireyin kendi özel kullanımları için verilmiş bir istemci sertifikasına sahip olmalıdır ve özel anahtarını korumak için kendi güçlü parolasını seçmesi gerekir. 

Aşağıdaki adımlar, otomatik olarak imzalanan CA sertifikasının oluşturulduğu ve depolandığı makinede çalıştırılmalıdır:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha256 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Bkz

* -n istemciye, bu sertifikayla kimlik doğrulaması yapılacak bir KIMLIĞE sahip
* -e, sertifika sona erme tarihi
* Bu istemci sertifikası için benzersiz dosya adlarıyla MyID. PVK ve MyID. cer

Bu komut, bir parolanın oluşturulmasını ve sonra bir kez kullanılmasını ister. Güçlü bir parola kullanın.

## <a name="create-pfx-files-for-client-certificates"></a>İstemci sertifikaları için PFX dosyaları oluşturma
Oluşturulan her istemci sertifikası için şunu yürütün:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Bkz

    MyID.pvk and MyID.cer with the filename for the client certificate

Parolayı girin ve sertifikayı şu seçeneklerle dışarı aktarın:

* Evet, özel anahtarı dışarı aktar
* Tüm genişletilmiş özellikleri dışarı aktar
* Bu sertifikanın verildiği kişi, parolayı dışarı aktar seçeneğini seçmenizi sağlar

## <a name="import-client-certificate"></a>İstemci sertifikasını içeri aktar
İstemci sertifikası verilen her bir bireyin, hizmet ile iletişim kurmak için kullandıkları makinelere anahtar çiftini içeri aktarmanız gerekir:

* Öğesine çift tıklayın. Windows Gezgini 'nde PFX dosyası
* En azından bu seçenekle sertifikayı Kişisel depoya aktarın:
  * Tüm genişletilmiş özellikleri ekle işaretli

## <a name="copy-client-certificate-thumbprints"></a>İstemci sertifikası parmak izlerini Kopyala
İstemci sertifikası verilen her bir bireyin, sertifikasının parmak izini almak için, hizmet yapılandırma dosyasına eklenecek bu adımları izlemelidir:

* Certmgr. exe dosyasını çalıştır
* Kişisel sekmesini seçin
* Kimlik doğrulaması için kullanılacak istemci sertifikasına çift tıklayın
* Açılan Sertifika iletişim kutusunda Ayrıntılar sekmesini seçin.
* Gösterme 'nin tümünü görüntülediğinden emin olun
* Listede Parmak Izi adlı alanı seçin
* Parmak izi değerini kopyalayın
  * İlk basamağın önünde görünür olmayan Unicode karakterleri Sil
  * Tüm boşlukları Sil

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Hizmet yapılandırma dosyasında Izin verilen istemcileri yapılandırma
Hizmet yapılandırma dosyasında, hizmet erişimine izin verilen istemci sertifikalarının parmak izlerinin virgülle ayrılmış listesiyle birlikte, aşağıdaki ayarın değerini güncelleştirin:

```xml
<Setting name="AllowedClientCertificateThumbprints" value="" />
```

## <a name="configure-client-certificate-revocation-check"></a>İstemci sertifikası iptal denetimini yapılandırma
Varsayılan ayar, istemci sertifikası iptal durumu için sertifika yetkilisini denetlemez. Denetimleri açmak için, istemci sertifikalarını veren sertifika yetkilisi bu denetimleri destekliyorsa, X509RevocationMode sabit listesinden tanımlanan değerlerden biriyle aşağıdaki ayarı değiştirin:

```xml
<Setting name="ClientCertificateRevocationCheck" value="NoCheck" />
```

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Otomatik olarak imzalanan şifreleme sertifikaları için PFX dosyası oluştur
Bir şifreleme sertifikası için şunu yürütün:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Bkz

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Parolayı girin ve sertifikayı şu seçeneklerle dışarı aktarın:

* Evet, özel anahtarı dışarı aktar
* Tüm genişletilmiş özellikleri dışarı aktar
* Sertifikayı bulut hizmetine yüklerken parolaya ihtiyaç duyarsınız.

## <a name="export-encryption-certificate-from-certificate-store"></a>Sertifika deposundan şifreleme sertifikasını dışarı aktar
* Sertifika bul
* Eylemler-> Tüm Görevler-> dışarı aktar... öğesine tıklayın.
* Sertifikayı bir öğesine dışarı aktarın. Bu seçeneklere sahip PFX dosyası: 
  * Evet, özel anahtarı dışarı aktar
  * Mümkünse sertifika yolundaki tüm sertifikaları Ekle 
* Tüm genişletilmiş özellikleri dışarı aktar

## <a name="upload-encryption-certificate-to-cloud-service"></a>Şifreleme sertifikasını bulut hizmetine yükle
Sertifikayı var olan veya oluşturulan ile karşıya yükleyin. Şifreleme anahtarı çiftinin bulunduğu PFX dosyası:

* Özel anahtar bilgilerini koruyan parolayı girin

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Hizmet yapılandırma dosyasında şifreleme sertifikasını Güncelleştir
Hizmet yapılandırma dosyasında, bulut hizmetine yüklenen sertifikanın parmak izine sahip aşağıdaki ayarların parmak izi değerini güncelleştirin:

```xml
<Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="common-certificate-operations"></a>Ortak sertifika işlemleri
* SSL sertifikasını yapılandırma
* İstemci sertifikalarını yapılandırma

## <a name="find-certificate"></a>Sertifika bul
Şu adımları uygulayın:

1. MMC. exe ' yi çalıştırın.
2. Dosya-> ek bileşen Ekle/Kaldır...
3. **Sertifikalar**' ı seçin.
4. **Ekle**'ye tıklayın.
5. Sertifika deposu konumunu seçin.
6. **Finish (Son)** düğmesine tıklayın.
7. **Tamam** düğmesine tıklayın.
8. **Sertifikalar**' ı genişletin.
9. Sertifika depolama düğümünü genişletin.
10. Sertifika alt düğümünü genişletin.
11. Listeden bir sertifika seçin.

## <a name="export-certificate"></a>Sertifikayı dışarı aktarma
**Sertifika dışarı aktarma Sihirbazı**'nda:

1. **İleri**’ye tıklayın.
2. **Evet**' i seçin ve ardından **özel anahtarı dışarı aktarın**.
3. **İleri**’ye tıklayın.
4. İstenen çıkış dosyası biçimini seçin.
5. İstenen seçenekleri denetleyin.
6. **Parolayı**denetleyin.
7. Güçlü bir parola girin ve onaylayın.
8. **İleri**’ye tıklayın.
9. Sertifikanın depolanacağı bir dosya adı yazın veya dosyaya gidin (bir kullanın. PFX uzantısı).
10. **İleri**’ye tıklayın.
11. **Finish (Son)** düğmesine tıklayın.
12. **Tamam** düğmesine tıklayın.

## <a name="import-certificate"></a>Sertifikayı içeri aktar
Sertifika Içeri aktarma Sihirbazı 'nda:

1. Mağaza konumunu seçin.
   
   * Yalnızca geçerli kullanıcı altında çalışan işlemlerin hizmete erişmesi durumunda **Geçerli Kullanıcı** ' yı seçin
   * Bu bilgisayardaki başka işlemlerin hizmete erişmesi durumunda **yerel makine** ' yi seçin
2. **İleri**’ye tıklayın.
3. Bir dosyadan içeri aktarıldıysanız dosya yolunu onaylayın.
4. İçeri aktarıldıysanız. PFX dosyası:
   1. Özel anahtarı koruyan parolayı girin
   2. İçeri aktarma seçeneklerini belirleyin
5. Aşağıdaki depoya "yerleştir" sertifikalarını seçin
6. **Gözat**’a tıklayın.
7. İstenen depoyu seçin.
8. **Finish (Son)** düğmesine tıklayın.
   
   * Güvenilen kök sertifika yetkilisi deposu seçilmişse **Evet**' e tıklayın.
9. Tüm iletişim kutusu pencereleri üzerinde **Tamam** ' ı tıklatın.

## <a name="upload-certificate"></a>Sertifikayı karşıya yükleme
[Azure portalında](https://portal.azure.com/)

1. **Cloud Services**seçin.
2. Bulut hizmetini seçin.
3. Üstteki menüde, **Sertifikalar**' a tıklayın.
4. Alt çubukta **karşıya yükle**' ye tıklayın.
5. Sertifika dosyasını seçin.
6. Eğer bir. PFX dosyası, özel anahtar için parolayı girin.
7. Tamamlandıktan sonra, listedeki yeni girdiden sertifika parmak izini kopyalayın.

## <a name="other-security-considerations"></a>Diğer güvenlik konuları
Bu belgede açıklanan SSL ayarları, HTTPS uç noktası kullanıldığında hizmet ve istemcileri arasındaki iletişimi şifreler. Bu, veritabanı erişimi için kimlik bilgileri ve potansiyel olarak diğer gizli bilgiler iletişim içinde bulunduğundan önemlidir. Ancak, hizmetin, kimlik bilgileri de dahil olmak üzere iç durumu, Microsoft Azure aboneliğinizde meta veri depolaması için sağladınız Microsoft Azure SQL veritabanındaki iç tablolarında devam etmediğini unutmayın. Bu veritabanı, hizmet yapılandırma dosyanızda aşağıdaki ayarın bir parçası olarak tanımlanmıştır (. CSCFG dosyası): 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

Bu veritabanında depolanan kimlik bilgileri şifrelenir. Bununla birlikte, en iyi uygulama olarak, hizmet dağıtımlarınızın hem Web hem de çalışan rollerinin hem meta veri veritabanına erişimi hem de depolanan kimlik bilgilerinin şifrelenmesi ve şifresinin çözülmesi için kullanılan sertifika olduğundan emin olun. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

