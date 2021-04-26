---
title: Azure statik Web Apps için yerel geliştirmeyi ayarlama
description: Azure statik Web Apps için yerel geliştirme ortamınızı ayarlamayı öğrenin
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 04/02/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: e19d39a32d48ec55473bb957595d47ec5148e74b
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588794"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Azure statik Web Apps önizlemesi için yerel geliştirmeyi ayarlama

Buluta yayımlandığında, bir Azure statik Web Apps sitesinin aynı uygulama gibi birlikte çalışan çok sayıda hizmeti vardır. Bu hizmetler arasında şunlar bulunur:

- Statik Web uygulaması
- Azure Işlevleri API 'SI
- Kimlik doğrulama ve yetkilendirme hizmetleri
- Yönlendirme ve Yapılandırma Hizmetleri

Bu hizmetlerin birbirleriyle iletişim kurması gerekir ve Azure statik Web Apps Bu tümleştirmeyi bulutta sizin için işler.

Ancak yerel olarak çalıştırıldığında, bu hizmetler otomatik olarak birlikte birbirlerine bağlı değildir.

Azure 'da aldığınız gibi benzer bir deneyim sağlamak için, [Azure statik Web Apps CLI](https://github.com/Azure/static-web-apps-cli) aşağıdaki hizmetleri sağlar:

- Yerel bir statik site sunucusu
- Ön uç Framework geliştirme sunucusuna bir proxy
- API uç noktalarınıza bir ara sunucu-Azure Functions Core Tools aracılığıyla kullanılabilir
- Bir sahte kimlik doğrulama ve yetkilendirme sunucusu
- Yerel yollar ve yapılandırma ayarları zorlaması

## <a name="how-it-works"></a>Nasıl çalışır?

Aşağıdaki grafikte isteklerin yerel olarak nasıl işlendiği gösterilmektedir.

:::image type="content" source="media/local-development/cli-conceptual.png" alt-text="Azure statik Web uygulaması CLı isteği ve yanıt akışı":::

> [!IMPORTANT]
> `http://localhost:4280`CLI tarafından sunulan uygulamaya erişmek için öğesine gidin.

-  Bağlantı noktasına yapılan istekler `4280` , istek türüne bağlı olarak uygun sunucuya iletilir.

- HTML veya CSS gibi **statik içerik** istekleri, iç CLI statik içerik sunucusu veya hata ayıklama için ön uç çerçeve sunucusu tarafından işlenir.

- **Kimlik doğrulama ve yetkilendirme** istekleri, uygulamanıza sahte bir kimlik profili sağlayan bir öykünücü tarafından işlenir.

- **Işlevler çekirdek Araçları çalışma zamanı** , ISTEKLERI sitenin API 'sine işler.

- Tüm hizmetlerden gelen **yanıtlar** , tek bir uygulama gibi, tarayıcıya döndürülür.

## <a name="prerequisites"></a>Önkoşullar

- **Mevcut Azure statik Web Apps sitesi**: bir hesabınız yoksa [Vanilla-API](https://github.com/staticwebdev/vanilla-api/generate?return_to=/staticwebdev/vanilla-api/generate) başlangıç uygulamasıyla başlayın.
- **NPM ile [Node.js](https://nodejs.org)**: [npm](https://www.npmjs.com/)'e erişim içeren [Node.js LTS](https://nodejs.org) sürümünü çalıştırın.
- **[Visual Studio Code](https://code.visualstudio.com/)**: API uygulamasında hata ayıklamak için kullanılır, ancak CLI için gerekli değildir.

## <a name="get-started"></a>başlarken

Mevcut Azure statik Web Apps sitenizin kök klasörüne bir Terminal açın.

1. CLı 'yı yükler.

    `npm install -g @azure/static-web-apps-cli`

1. Uygulamanız için gerekliyse uygulamanızı oluşturun.

    `npm run build`Veya projeniz için eşdeğer komutunu çalıştırın.

1. Uygulamanızın çıkış dizinine geçin. Çıkış klasörleri genellikle _derleme_ veya benzer bir şekilde adlandırılır.

1. CLı 'yı başlatın.

    `swa start`

1. http://localhost:4280Uygulamayı tarayıcıda görüntülemek için bölümüne gidin.

### <a name="other-ways-to-start-the-cli"></a>CLı 'yi başlatmak için diğer yollar

| Description | Komut |
|--- | --- |
| Belirli bir klasörü sunar | `swa start ./output-folder` |
| Çalışan bir Framework geliştirme sunucusu kullanma | `swa start http://localhost:3000` |
| Bir klasörde Işlevler uygulaması başlatma | `swa start ./output-folder --api ./api` |
| Çalışan Işlevler uygulaması kullanma | `swa start ./output-folder --api http://localhost:7071` |

## <a name="authorization-and-authentication-emulation"></a>Yetkilendirme ve kimlik doğrulama öykünmesi

Statik Web Apps CLı, Azure 'da uygulanan [güvenlik akışına](./authentication-authorization.md) öykünür. Bir Kullanıcı oturum açtığında, uygulamaya döndürülen sahte kimlik profilini tanımlayabilirsiniz.

Örneğin, ' a gitmeye çalıştığınızda `/.auth/login/github` bir kimlik profili tanımlamanızı sağlayan bir sayfa döndürülür.

> [!NOTE]
> Öykünücü yalnızca GitHub değil, herhangi bir güvenlik sağlayıcısıyla birlikte çalışmaktadır.

:::image type="content" source="media/local-development/auth-emulator.png" alt-text="Yerel kimlik doğrulama ve yetkilendirme öykünücüsü":::

Öykünücü, aşağıdaki [istemci sorumlusu](./user-information.md#client-principal-data) değerlerini sağlamanıza olanak tanıyan bir sayfa sağlar:

| Değer | Açıklama |
| --- | --- |
| **Kullanıcı adı** | Güvenlik sağlayıcısıyla ilişkili hesap adı. Bu değer, `userDetails` istemci sorumlusu içinde özellik olarak görünür ve bir değer sağlamazsanız otomatik olarak oluşturulur. |
| **Kullanıcı Kimliği** | CLı tarafından otomatik olarak oluşturulan değer.  |
| **Roller** | Her adın yeni bir satırda bulunduğu rol adlarının listesi.  |

Oturum açıldıktan sonra:

- `/.auth/me`Kullanıcının [istemci sorumlusunu](./user-information.md)almak için uç noktasını veya bir işlev uç noktasını kullanabilirsiniz.

- Gezinme `./auth/logout` , istemci sorumlusunu temizler ve sahte kullanıcıyı günlüğe kaydeder.

## <a name="debugging"></a>Hata Ayıklama

Statik bir Web uygulamasında iki hata ayıklama bağlamı vardır. Birincisi, statik içerik sitesi içindir ve ikincisi API işlevleri içindir. Statik Web Apps CLı 'nın bu bağlamların bir veya her ikisi için geliştirme sunucularını kullanmasına izin vererek yerel hata ayıklama mümkündür.

Aşağıdaki adımlarda, her iki hata ayıklama bağlamı için geliştirme sunucularını kullanan ortak bir senaryo gösterilmektedir.

1. Statik site geliştirme sunucusunu başlatın. Bu komut, kullanmakta olduğunuz ön uç çerçevesine özgüdür, ancak genellikle, veya gibi komutlar biçiminde gelir `npm run build` `npm start` `npm run dev` .

1. Visual Studio Code ' de API uygulama klasörünü açın ve bir hata ayıklama oturumu başlatın.

1. Statik sunucu ve API sunucusu adreslerini `swa start` sırayla listeleyerek komuta geçirin.

    `swa start http://localhost:<DEV-SERVER-PORT-NUMBER> --api=http://localhost:7071`

Aşağıdaki ekran görüntülerinde tipik bir hata ayıklama senaryosu için terminaller gösterilmektedir:

Statik içerik sitesi aracılığıyla çalışmaktadır `npm run dev` .

:::image type="content" source="media/local-development/run-dev-static-server.png" alt-text="Statik site geliştirme sunucusu":::

Azure Işlevleri API uygulaması, Visual Studio Code bir hata ayıklama oturumu çalıştırıyor.

:::image type="content" source="media/local-development/visual-studio-code-debugging.png" alt-text="Visual Studio Code API hata ayıklaması":::

Statik Web Apps CLı her iki geliştirme sunucusu kullanılarak başlatılır.

:::image type="content" source="media/local-development/static-web-apps-cli-terminal.png" alt-text="Azure statik Web Apps CLı terminali":::

Şimdi bağlantı noktası üzerinden gelen istekler, `4280` statik içerik geliştirme sunucusuna ya da API hata ayıklama oturumuna yönlendirilir.

Farklı hata ayıklama senaryoları hakkında daha fazla bilgi için, bağlantı noktalarını ve sunucu adreslerini özelleştirmeye ilişkin yönergeler içeren [Azure Static Web Apps CLI deposuna](https://github.com/Azure/static-web-apps-cli)bakın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Uygulamanızı yapılandırma](configuration.md)
