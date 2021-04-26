---
title: Azure Backup 'de Aktarım Katmanı Güvenliği
description: Ağ üzerinden aktarılırken verileri güvenli tutmak için Şifreleme Protokolü Aktarım Katmanı Güvenliği 'ni (TLS) kullanmak üzere Azure Backup nasıl etkinleştireceğinizi öğrenin.
ms.topic: conceptual
ms.date: 11/01/2020
ms.openlocfilehash: ba9c9d91f562f54695a0739908c8a409d14d5852
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96327126"
---
# <a name="transport-layer-security-in-azure-backup"></a>Azure Backup 'de Aktarım Katmanı Güvenliği

Aktarım Katmanı Güvenliği (TLS), bir ağ üzerinden aktarılırken verileri güvende tutan bir şifreleme protokolüdür. Azure Backup, aktarılan yedekleme verilerinin gizliliğini korumak için Aktarım Katmanı Güvenliği kullanır. Bu makalede, önceki sürümlere göre daha iyi güvenlik sağlayan TLS 1,2 protokolünü etkinleştirme adımları açıklanmaktadır.

## <a name="earlier-versions-of-windows"></a>Önceki Windows sürümleri

Makine Windows 'un önceki sürümlerini çalıştırıyorsa, aşağıda belirtilen ilgili güncelleştirmeler yüklü olmalıdır ve KB makalelerinde belgelenen kayıt defteri değişikliklerinin uygulanması gerekir.

|İşletim sistemi  |KB makalesi |
|---------|---------|
|Windows Server 2008 SP2   |   <https://support.microsoft.com/help/4019276>      |
|Windows Server 2008 R2, Windows 7, Windows Server 2012   | <https://support.microsoft.com/help/3140245>         |

>[!NOTE]
>Güncelleştirme, gerekli protokol bileşenlerini yükler. Yükleme sonrasında, gerekli protokolleri düzgün şekilde etkinleştirmek için yukarıdaki KB makalelerinde bahsedilen kayıt defteri anahtarı değişikliklerini yapmanız gerekir.

## <a name="verify-windows-registry"></a>Windows kayıt defterini doğrulama

### <a name="configuring-schannel-protocols"></a>SChannel protokollerini yapılandırma

Aşağıdaki kayıt defteri anahtarları, TLS 1,2 protokolünün SChannel bileşen düzeyinde etkinleştirildiğinden emin olur:

```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "Enabled"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "DisabledByDefault"=dword:00000000
```

>[!NOTE]
>Gösterilen değerler Windows Server 2012 R2 ve daha yeni sürümlerde varsayılan olarak ayarlanır. Bu Windows sürümlerinde, kayıt defteri anahtarları yoksa, oluşturulması gerekmez.

### <a name="configuring-net-framework"></a>.NET Framework yapılandırma

Aşağıdaki kayıt defteri anahtarları, güçlü şifrelemeyi destekleyecek .NET Framework yapılandırır. [.NET Framework yapılandırma](/dotnet/framework/network-programming/tls#configuring-schannel-protocols-in-the-windows-registry)hakkında daha fazla bilgi edinebilirsiniz.

```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001
```

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="why-enable-tls-12"></a>TLS 1,2 neden etkileşmelidir?

TLS 1,2, SSL 2,0, SSL 3,0, TLS 1,0 ve TLS 1,1 gibi önceki şifreleme protokollerinden daha güvenlidir. Azure Backup Hizmetleri zaten TLS 1,2 ' i desteklemektedir.

### <a name="what-determines-the-encryption-protocol-used"></a>Şifreleme Protokolü ne belirler?

Hem istemci hem de sunucu tarafından desteklenen en yüksek protokol sürümü, şifrelenmiş konuşmayı kurmak için anlaşılır. TLS El Sıkışma Protokolü hakkında daha fazla bilgi için bkz. [TLS kullanarak güvenli oturum oluşturma](/windows/win32/secauthn/tls-handshake-protocol#establishing-a-secure-session-by-using-tls).

### <a name="what-is-the-impact-of-not-enabling-tls-12"></a>TLS 1,2 ' i etkinleştirmenin etkisi nedir?

Protokol düşürme saldırılarına karşı geliştirilmiş güvenlik için Azure Backup, 1,2 'den eski olan TLS sürümlerinin aşamalı olarak devre dışı bırakılması başlıyor. Bu, eski protokol ve şifre paketi bağlantılarına izin vermemek için hizmetler genelinde uzun süreli bir kaydırma parçasıdır. Azure Backup Hizmetleri ve bileşenleri, TLS 1,2 ' i tam olarak destekler. Ancak gerekli güncelleştirmeler veya bazı özelleştirilmiş yapılandırmalara sahip olmayan Windows sürümleri, TLS 1,2 protokollerinin önerilmeye devam edebilir. Bu, aşağıdakilerden bir veya daha fazlası dahil ancak bunlarla sınırlı olmamak üzere hatalara neden olabilir:

- Yedekleme ve geri yükleme işlemleri başarısız olabilir.
- Yedekleme bileşenlerinde bağlantı hataları 10054 hatası (mevcut bir bağlantı uzak ana bilgisayar tarafından zorla kapatıldı).
- Azure Backup ilgili hizmetler her zamanki gibi durdurulur veya başlamaz.

## <a name="additional-resources"></a>Ek kaynaklar

- [Aktarım Katmanı Güvenliği Protokolü](/windows/win32/secauthn/transport-layer-security-protocol)
- [Dağıtılan işletim sistemleri genelinde TLS 1,2 desteğini sağlama](/security/engineering/solving-tls1-problem#ensuring-support-for-tls-12-across-deployed-operating-systems)
- [.NET Framework ile Aktarım Katmanı Güvenliği (TLS) en iyi uygulamaları](/dotnet/framework/network-programming/tls)