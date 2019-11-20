---
title: Azure Uygulama yapılandırması SSS | Microsoft Docs
description: Azure Uygulama yapılandırması hakkında sık sorulan sorular
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 8d286cbab33a1fb6a2d2a2cb70caed11b21af735
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904104"
---
# <a name="azure-app-configuration-faq"></a>Azure uygulama yapılandırma hakkında SSS

Bu makalede, Azure Uygulama yapılandırması hakkında sık sorulan sorular ele alınmaktadır.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Uygulama Yapılandırması’nın Azure Key Vault’tan farkı nedir?

Uygulama yapılandırması farklı kullanım örnekleri kümesi için tasarlanmıştır: geliştiricilerin uygulama ayarlarını yönetmesine ve özellik kullanılabilirliğini denetlemesine yardımcı olur. Karmaşık yapılandırma verileriyle çalışan pek çok görevi basitleştirecek.

Uygulama yapılandırması şunları destekler:

- Hiyerarşik ad alanları
- kapatma
- Kapsamlı sorgular
- Toplu iş alımı
- Özel yönetim işlemleri
- Özellik yönetimi Kullanıcı arabirimi

Uygulama yapılandırması Key Vault için tamamlayıcı ve iki uygulama dağıtımında yan yana kullanılmalıdır.

## <a name="should-i-store-secrets-in-app-configuration"></a>Gizli dizileri uygulama yapılandırmasında depolamam gerekir mi?

Uygulama yapılandırması sıkı güvenlik sağlar, ancak Key Vault uygulama gizli dizileri depolamak için de en iyi yerdir. Key Vault, donanım düzeyinde şifreleme, ayrıntılı erişim ilkeleri ve sertifika döndürme gibi yönetim işlemleri sağlar.

Key Vault depolanan gizli dizileri referans eden uygulama yapılandırma değerleri oluşturabilirsiniz. Daha fazla bilgi için bkz. [ASP.NET Core uygulamasında Key Vault başvurularını kullanma](./use-key-vault-references-dotnet-core.md).

## <a name="does-app-configuration-encrypt-my-data"></a>Uygulama yapılandırması verilerimi şifreliyor mu?

Evet. Uygulama yapılandırması, tuttuğu tüm anahtar değerlerini şifreler ve ağ iletişimini şifreler. Anahtar adları, yapılandırma verilerini almak için dizinler olarak kullanılır ve şifrelenmez.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Uygulama yapılandırması Azure App Service ayarlarından farklı midir?

Azure App Service, her bir App Service örneği için uygulama ayarlarını tanımlamanızı sağlar. Bu ayarlar, uygulama koduna ortam değişkenleri olarak geçirilir. İsterseniz bir ayarı belirli bir dağıtım yuvası ile ilişkilendirebilirsiniz. Daha fazla bilgi için bkz. [uygulama ayarlarını yapılandırma](/azure/app-service/configure-common#configure-app-settings).

Buna karşılık, Azure Uygulama yapılandırması, App Service çalıştıran uygulamalar dahil olmak üzere birden çok uygulama arasında paylaşılabilen ayarları tanımlamanızı sağlar. Bu ayarlara, .NET ve Java için yapılandırma sağlayıcıları aracılığıyla, Azure SDK aracılığıyla veya doğrudan REST API 'Leri aracılığıyla uygulama kodundan erişilebilir.

Ayrıca, App Service ile uygulama yapılandırması arasında ayarları içeri ve dışarı aktarabilirsiniz. Bu, mevcut App Service ayarlarını temel alarak yeni bir uygulama yapılandırma deposu ayarlamanıza veya yapılandırmayı App Service ayarları temel alan mevcut bir uygulamayla kolayca paylaşmanıza olanak sağlar.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Uygulama yapılandırmasında saklanan anahtarlar ve değerler üzerinde herhangi bir boyut sınırlaması var mı?

Tek bir anahtar-değer öğesi için 10 KB sınırı vardır.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Birden çok ortam için yapılandırma (test, hazırlama, üretim vb.) nasıl depolamalıyım?

Şu anda uygulama yapılandırmasına kimin bir mağaza düzeyinde erişebileceğini kontrol edebilirsiniz. Farklı izinler gerektiren her ortam için ayrı bir depo kullanın. Bu yaklaşım size en iyi güvenlik yalıtımına sahip olmanızı sağlar.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Uygulama yapılandırmasını kullanmanın önerilen yolları nelerdir?

Bkz. [en iyi uygulamalar](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Uygulama yapılandırma maliyeti ne kadar?

Hizmet, genel önizleme sırasında ücretsiz olarak kullanılabilir.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Yeni yayınlar ve uygulama yapılandırmasıyla ilgili diğer bilgiler hakkında duyurular nasıl alabilirim?

[GitHub duyuruları](https://github.com/Azure/AppConfiguration-Announcements)depolarımıza abone olun.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Bir sorunu nasıl bildirebilirim veya öneride bulunmak için ne yapabilirim?

Doğrudan [GitHub](https://github.com/Azure/AppConfiguration/issues)üzerinde bizimle iletişime ulaşabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Uygulama yapılandırması hakkında](./overview.md)
