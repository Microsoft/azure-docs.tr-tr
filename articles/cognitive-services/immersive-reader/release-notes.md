---
title: Modern Okuyucu SDK 'Sı sürüm notları
titleSuffix: Azure Cognitive Services
description: Modern okuyucu JavaScript SDK 'sındaki yenilikler hakkında daha fazla bilgi edinin.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 10/12/2020
ms.author: dylankil
ms.openlocfilehash: 6b041916c6fa446732e95d49391d9ead63eb1b17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93133724"
---
# <a name="immersive-reader-javascript-sdk-release-notes"></a>Modern okuyucu JavaScript SDK sürüm notları

## <a name="version-110"></a>Sürüm 1.1.0

Bu sürüm yeni özellikler, güvenlik güvenlik açığı düzeltmeleri, hata düzeltmeleri, kod örneklerine yönelik güncelleştirmeler ve yapılandırma seçenekleri içerir.

#### <a name="new-features"></a>Yeni Özellikler

* Farklı tarayıcılarda ve cihazlarda Kullanıcı tercihlerini kaydetmeyi ve yüklemeyi etkinleştirme
* Varsayılan görüntüleme seçeneklerini yapılandırmayı etkinleştir
* Tam ekran okuyucu başlatılırken çeviri dilini ayarlama, sözcük çevirisini etkinleştirme ve belge çevirisini etkinleştirme seçeneği ekleyin
* Seçenekler aracılığıyla okuma sesli yapılandırma desteği ekleyin
* İlk çalıştırma deneyimini devre dışı bırakma yeteneği ekleyin
* UWP için ImmersiveReaderView ekleme

#### <a name="improvements"></a>Geliştirmeleri

* Android kod örneği HTML 'sini en son SDK ile çalışacak şekilde güncelleştirme
* İşlenen karakter sayısını döndürmek için başlatma yanıtını Güncelleştir
* V 1.1.0 kullanmak için kod örneklerini güncelleştirme
* LaunchAsync zaten yüklenirken çağrılmasına izin verme
* Verilerin bir dize olmadığı iletileri yoksayarak geçersiz içerik olup olmadığını denetleyin
* Promise 'nin tarayıcı desteğini denetlemek için bir if yan tümcesinde çağrıyı pencereye kaydırın

#### <a name="fixes"></a>Düzeltmeler

* Gitignore 'ten yarn. Lock 'ı kaldırarak bağımlışayı düzeltir
* Hızlı başlangıç-NodeJS kod örneği 'nde Pug 'yi v 3.0.0 sürümüne yükselterek güvenlik açığı 'nı Düzeltme
* Jest ve TypeScript paketlerini yükselterek birden çok güvenlik açığını Düzeltme
* Microsoft. IdentityModel. clients. ActiveDirectory 'yi v 5.2.0 sürümüne yükselterek bir güvenlik açığını Düzeltme

<br>

## <a name="version-100"></a>Sürüm 1.0.0

Bu sürüm, önemli değişiklikler, yeni özellikler, kod örnek iyileştirmeleri ve hata düzeltmeleri içerir.

#### <a name="breaking-changes"></a>Hataya Neden Olan Değişiklikler

* Azure AD belirteci ve alt etki alanı gerektirir ve önceki sürümlerde kullanılan belirteçleri kullanımdan kaldırır.
* Tanımlama, ıepolicy 'ı devre dışı olarak ayarlayın. Kullanıcı tercihlerini bekletme varsayılan olarak devre dışıdır. Her seferinde okuyucu varsayılan ayarlarla başlatılır. Bu, birlikte bulunan ıepolicy etkin olarak ayarlanmıştır.

#### <a name="new-features"></a>Yeni Özellikler

* Tanımlama bilgilerini etkinleştirmek veya devre dışı bırakmak için destek ekleme
* Android Kotlin hızlı başlangıç kodu örneği ekleme
* Android Java hızlı başlangıç kodu örneği ekleme
* Node.js hızlı başlangıç kodu örneği ekleme

#### <a name="improvements"></a>Geliştirmeleri

* Güncelleştirme Node.js Advanced README.md
* Python kod örneğini gelişmiş 'ten hızlı başlangıç 'e değiştirme
* İOS Swift kod örneğini js/örneklere taşıma
* V 1.0.0 kullanmak için kod örneklerini güncelleştirme

#### <a name="fixes"></a>Düzeltmeler

* Node.js gelişmiş kod örneği için çözüm
* Gelişmiş-CSharp-birden çok kaynakları için eksik dosyaları ekleyin
* En-US ' i köprülerden kaldır

<br>

## <a name="version-003"></a>Sürüm 0.0.3

Bu sürüm yeni özellikler, kod örneklerine yönelik geliştirmeler, güvenlik güvenlik açığı düzeltmeleri ve hata düzeltmeleri içerir.

#### <a name="new-features"></a>Yeni Özellikler

* İOS Swift kodu örneği ekleme
* Birden çok kaynağın kullanımını gösteren C# Gelişmiş kod örneği ekleme 
* Tam ekran geçiş özelliğini devre dışı bırakmak için destek ekleme
* Modern okuyucu uygulaması Çıkış düğmesini gizlemek için destek ekleme
* Modern okuyucudan çıkmadan ana bilgisayar uygulaması tarafından kullanılabilecek bir geri çağırma işlevi ekleyin
* Azure Active Directory kimlik doğrulaması kullanmak için kod örneklerini güncelleştirme

#### <a name="improvements"></a>Geliştirmeleri

* C# Gelişmiş kod örneğini Word belgesi içerecek şekilde güncelleştirme
* V 0.0.3 kullanmak için kod örneklerini güncelleştirme

#### <a name="fixes"></a>Düzeltmeler

* Güvenlik açığını gidermek için lodash 'i 4.17.14 sürümüne yükseltin
* Güvenlik açığını gidermek için C# MSAL kitaplığını güncelleştirme
* Güvenlik açığını gidermek için Mixin-derin sürümünü 1.3.2 sürümüne yükseltin
* Set-Value ve Mixin-derin sürümlerini kullanan ve güvenlik açığını gidermek için

<br>

## <a name="version-002"></a>Sürüm 0.0.2

Bu sürüm yeni özellikler, kod örneklerine yönelik geliştirmeler, güvenlik güvenlik açığı düzeltmeleri ve hata düzeltmeleri içerir.

#### <a name="new-features"></a>Yeni Özellikler

* Python gelişmiş kod örneği ekleme
* Java hızlı başlangıç kodu örneği ekleme
* Basit kod örneği Ekle

#### <a name="improvements"></a>Geliştirmeleri

* ResourceName öğesini Cogsvcsalt etki alanı olarak yeniden adlandır
* Gizli dizileri kod dışına taşıyın ve ortam değişkenlerini kullanın
* V 0.0.2 kullanmak için kod örneklerini güncelleştirme

#### <a name="fixes"></a>Düzeltmeler

* Modern okuyucu düğmesine yönelik erişilebilirlik hatalarını çözme
* Bozuk kaydırmayı çözme
* Güvenlik açığını gidermek için handleçubuklarıyla paketini 4.1.2 sürümüne yükseltin
* SDK birim testlerinde hataları düzeltir
* JavaScript Internet Explorer 11 uyumluluk hatalarını düzeltir
* SDK URL 'lerini güncelleştirir

<br>

## <a name="version-001"></a>Sürüm 0.0.1

Tam ekran okuyucusu JavaScript SDK 'sının ilk sürümü.

* Derinlikli okuyucu JavaScript SDK 'Sı ekleme
* Kullanıcı arabirimi dilini belirtmek için destek ekleme
* LaunchAsync işlevinin bir zaman aşımı hatasıyla başarısız olması gerektiğini belirleyecek bir zaman aşımı ekleyin
* Tam ekran okuyucu iframe 'in z dizinini belirtmek için destek ekleme
* Chrome uygulamalarıyla uyumluluk için iframe yerine WebView etiketi kullanma desteği ekleyin
* SDK birim testleri ekleme
* Node.js gelişmiş kod örneği Ekle
* C# Gelişmiş kod örneği Ekle
* C# hızlı başlangıç kodu örneği ekleme
* Paket yapılandırması, Yarn ve diğer derleme dosyaları Ekle
* Git yapılandırma dosyalarını ekle
* Kod örneklerine ve SDK 'ya README.md dosyaları ekleyin
* MıT lisansı Ekle
* Katkıda bulunan yönergeleri Ekle
* Statik simge düğmesi SVG varlıkları Ekle

## <a name="next-steps"></a>Sonraki adımlar

Tam Ekran Okuyucu’yu kullanmaya başlama:

* [Tam ekran okuyucusu istemci kitaplığı başvurusunu](./reference.md) okuyun
* [GitHub 'Da modern okuyucu istemci kitaplığını](https://github.com/microsoft/immersive-reader-sdk) keşfet
