---
title: Azure Relay API 'ye Genel Bakış | Microsoft Docs
description: Bu makalede, kullanılabilir Azure Relay API 'Lerine (.NET Standard, .NET Framework, Node.js vb.) ilişkin bir genel bakış sunulmaktadır.
ms.topic: article
ms.custom: devx-track-dotnet
ms.date: 06/23/2020
ms.openlocfilehash: 391bd74f2bce8721b6d6359f3990af494277aa45
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313003"
---
# <a name="available-relay-apis"></a>Kullanılabilir geçiş API 'Leri

## <a name="runtime-apis"></a>Çalışma zamanı API 'Leri

Aşağıdaki tabloda, şu anda kullanılabilir olan tüm geçiş çalışma zamanı istemcileri listelenmiştir.

[Ek bilgi](#additional-information) bölümü, her çalışma zamanı kitaplığının durumu hakkında daha fazla bilgi içerir.

| Dil/platform | Kullanılabilir özellik | İstemci paketi | Depo |
| --- | --- | --- | --- |
| .NET Standard | Karma Bağlantılar | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | WCF Geçişi | [WindowsAzure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | Yok |
| Düğüm | Karma Bağlantılar | [WebSockets `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[WebSockets `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[HTTP Istekleri: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Ek bilgiler

#### <a name="net"></a>.NET

.NET ekosisteminde birden fazla çalışma zamanı var, bu nedenle geçiş için birden çok .NET kitaplığı var. .NET Standard kitaplığı, .NET Core veya .NET Framework kullanılarak çalıştırılabilir, ancak .NET Framework kitaplığı yalnızca bir .NET Framework ortamında çalıştırılabilir. .NET çerçeveleri hakkında daha fazla bilgi için bkz. [Framework sürümleri](/dotnet/articles/standard/frameworks).

.NET Framework kitaplığı yalnızca WCF programlama modelini destekler ve WCF taşımasını temel alan özel bir ikili protokolü kullanır `net.tcp` . Bu protokol ve kitaplık, mevcut uygulamalarla geriye dönük uyumluluk için korunur.

.NET Standard kitaplığı, HTTP ve WebSockets üzerinde derlemelerin Karma Bağlantılar geçişinin açık protokol tanımına dayalıdır. Kitaplık, HTTP isteklerini yanıtlamak için WebSockets üzerinde bir akış soyutlama ve basit bir istek-yanıt API hareketi destekler. [Web API](https://github.com/Azure/azure-relay-dotnet) örneği, karma bağlantılar Web hizmetleri için ASP.NET Core nasıl tümleştirileceğini gösterir.

#### <a name="nodejs"></a>Node.js

Yukarıdaki tabloda listelenen Karma Bağlantılar modülleri, yerel ağ yığını yerine Azure Relay hizmetini dinleyen alternatif uygulamalarla mevcut Node.js modüllerini değiştirin veya değiştirin.

`hyco-https`Modül, çekirdek Node.js modüllerini düzeltme ve kısmen geçersiz kılar ve `http` `https` Bu temel modülleri kullanan birçok Node.js modülle ve uygulama Ile uyumlu bir HTTPS dinleyicisi uygulaması sağlar.

`hyco-ws`Ve `hyco-websocket` modülleri, `ws` `websocket` her Iki modülün da karma bağlantılar geçişinin arkasında çalışmasına izin veren modülleri ve uygulamaları etkinleştiren alternatif dinleyici uygulamaları sağlayarak Node.js yönelik popüler ve modülleri düzeltilir.

Bu modüllerle ilgili ayrıntılar [Azure-Relay-node](https://github.com/Azure/azure-relay-node) GitHub deposunda bulunabilir.

## <a name="next-steps"></a>Sonraki adımlar

Azure Relay hakkında daha fazla bilgi edinmek için şu bağlantıları ziyaret edin:
* [Azure Relay nedir?](relay-what-is-it.md)
* [Geçiş hakkında SSS](relay-faq.yml)
