---
title: Key Vault .NET 2. x API sürüm notları | Microsoft Docs
description: .NET geliştiricileri, Azure Key Vault için bu API 'yi kullanır
services: key-vault
author: msmbaldwin
manager: rkarlin
editor: bryanla
ms.service: key-vault
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: 07502b4c4487a7517e2fe73b62eae0a237a6f22b
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883274"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2,0-sürüm notları ve geçiş kılavuzu
Aşağıdaki bilgiler, ve .NET için C# Azure Key Vault kitaplığının 2,0 sürümüne geçiş yapmanıza yardımcı olur.  Önceki sürümler için yazılan uygulamaların, en son sürümü destekleyecek şekilde güncelleştirilmesi gerekir.  Bu değişiklikler, **Key Vault sertifikaları**gibi yeni ve geliştirilmiş özellikleri tam olarak desteklemek için gereklidir.

## <a name="key-vault-certificates"></a>Key Vault sertifikaları

Key Vault sertifikaları x509 sertifikalarını yönetir ve aşağıdaki davranışları destekler:  

* Key Vault oluşturma işlemi aracılığıyla sertifika oluşturun veya var olan sertifikayı içeri aktarın. Bu, hem otomatik olarak imzalanan hem de sertifika yetkilisi (CA) tarafından oluşturulan sertifikaları içerir.
* Özel anahtar malzemesini kullanarak x509 sertifika depolama alanını etkileşime girmeden güvenli bir şekilde depolayın ve yönetin.  
* Sertifika yaşam döngüsünü yönetmek için Key Vault yönlendirecek ilkeleri tanımlayın.  
* Yaşam süresi uyarıları ve yenileme bildirimleri gibi yaşam döngüsü olayları için iletişim bilgilerini sağlayın.  
* Sertifikaları seçili verenler (Key Vault iş ortağı x509 sertifika sağlayıcıları ve sertifika yetkilileri) ile otomatik olarak yenileyin. * alternatif (iş ortağı olmayan) için destek sertifikası, ve sertifika yetkililerini sağlar (otomatik yenilemeyi desteklemez).  

## <a name="net-support"></a>.NET desteği

* **.Net 4,0** , Azure Key Vault .net kitaplığının 2,0 sürümü tarafından desteklenmiyor
* **.NET Framework 4.5.2** , Azure Key Vault .net kitaplığının 2,0 sürümü tarafından desteklenir
* **.NET Standard 1,4** , Azure Key Vault .net kitaplığının 2,0 sürümü tarafından destekleniyor

## <a name="namespaces"></a>Ad Alanları

* **Modeller** için ad alanı **Microsoft. Azure. Keykasasından** **Microsoft. Azure. keykasa. modellerle**değiştirilmiştir.
* **Microsoft. Azure. Keykasası. Internal** ad alanı bırakılır.
* Aşağıdaki Azure SDK bağımlılıkları ad alanları 

    - **Hyak. Common** artık **Microsoft. Rest**.
    - **Hyak. Common. ınterals** artık **Microsoft. Rest. Serialization**.

## <a name="type-changes"></a>Tür değişiklikleri

* *Gizli anahtar* , *secretdemeti* olarak değiştirildi
* *Sözlük* *IDictionary* olarak değiştirildi
* *Liste\<T >, dize [],* *\<IList T* olarak değiştirildi >
* *Nextlist* , *NextPageLink* olarak değiştirildi

## <a name="return-types"></a>Dönüş türleri

* **Keylist** ve **secrelıst** artık *listkeysresponsemessage* yerine *Ipage\<T >* döndürüyor
* Oluşturulan **Backupkeyasync** artık *değer* (yedek blob) içeren *backupkeyresult*öğesini döndürüyor. Daha önce, yöntemi sarmalanmış ve yalnızca değeri döndürdü.

## <a name="exceptions"></a>Özel durumlar

* *Keyvaultclientexception* değeri *Keyvaulterrorexception* olarak değiştirildi
* Hizmet hatası özel durumdan değiştirildi *.*  *Özel durum hatası. Body. Error. Message*.
* **[Jsonextensiondata]** hata iletisinden daha fazla bilgi kaldırıldı.

## <a name="constructors"></a>Kurucu

* Bir *HttpClient* 'ı bir Oluşturucu bağımsız değişkeni olarak kabul etmek yerine, Oluşturucu yalnızca *HttpClientHandler* veya *DelegatingHandler []* kabul eder.

## <a name="downloaded-packages"></a>İndirilen paketler

Bir istemci bir Key Vault bağımlılığını işlediğinde, aşağıdaki paketler indirilir:

### <a name="previous-package-list"></a>Önceki paket listesi

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Geçerli paket listesi

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Sınıf değişiklikleri

* **Unixepoch** sınıfı kaldırılmıştır.
* **Base64UrlConverter** sınıfı **Base64UrlJsonConverter**olarak yeniden adlandırıldı.

## <a name="other-changes"></a>Diğer değişiklikler

* Bu API sürümüne geçici hatalarda KV işlem yeniden deneme ilkesinin yapılandırmasına yönelik destek eklenmiştir.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft. Azure. Management. Keykasa NuGet

* Bir *kasa*döndüren işlemler için, dönüş türü bir **kasa** özelliği içeren bir sınıftır. Dönüş türü artık *kasa*.
* *Permissionstokeys* ve *permissionstogizlilikler* artık *Izinlerdir. anahtarlar* ve *izinler. gizlilikler*
* Belirli dönüş türleri değişiklikleri denetim düzlemi için de geçerlidir.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft. Azure. Keykasası. Extensions NuGet

* Paket, şifreleme işlemleri için **Microsoft. Azure. Keykasası. Extensions** ve **Microsoft. Azure. Keykasa. Cryptography** 'a bölünmüştür.

