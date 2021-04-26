---
title: Kudu hizmetine genel bakış
description: App Service ve özelliklerinde sürekli dağıtımı destekleyen motor hakkında bilgi edinin.
ms.date: 03/17/2021
ms.topic: reference
ms.openlocfilehash: ad1456f1ca123127f3d84aa8195c99fc34872aee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610029"
---
# <a name="kudu-service-overview"></a>Kudu hizmetine genel bakış

Kudu, kaynak denetimi tabanlı dağıtım ve Dropbox ve OneDrive Sync gibi diğer dağıtım yöntemleriyle ilgili [Azure App Service](overview.md) birçok özelliğin arkasındaki altyapıdır. 

## <a name="access-kudu-for-your-app"></a>Uygulamanıza kudu erişimi
Her ne zaman bir uygulama oluşturduğunuzda, App Service HTTPS tarafından güvenliği sağlanmış bir yardımcı uygulama oluşturur. Bu kudu uygulamasına şu adresten erişilebilir:

- Uygulama yalıtılmış katmanda değil: `https://<app-name>.scm.azurewebsites.net`
- Yalıtılmış katmanda uygulama (App Service Ortamı): `https://<app-name>.scm.<ase-name>.p.azurewebsites.net`

Daha fazla bilgi için bkz. [kudu hizmetine erişme](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).

## <a name="kudu-features"></a>Kudu özellikleri

Kudu, App Service uygulamanız hakkında şu gibi yararlı bilgiler sağlar:

- Uygulama ayarları
- Bağlantı dizeleri
- Ortam değişkenleri
- Sunucu değişkenleri
- HTTP üstbilgileri

Ayrıca, gibi diğer özellikleri de sağlar:

- [Kudu konsolundaki](https://github.com/projectkudu/kudu/wiki/Kudu-console)komutları çalıştırın.
- IIS tanılama dökümlerini veya Docker günlüklerini indirin.
- IIS süreçlerini ve site uzantılarını yönetin.
- Windows AP 'ler için dağıtım Web kancaları ekleyin.
- İle ZIP dağıtımı Kullanıcı arabirimine izin verin `/ZipDeploy` .
- [Özel dağıtım betikleri](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)oluşturur.
- [REST API](https://github.com/projectkudu/kudu/wiki/REST-API)erişimine izin verir.

## <a name="more-resources"></a>Daha Fazla Kaynak

Kudu [Açık kaynaklı bir projem](https://github.com/projectkudu/kudu)ve kendi belgelerine [kudu wiki](https://github.com/projectkudu/kudu/wiki).

