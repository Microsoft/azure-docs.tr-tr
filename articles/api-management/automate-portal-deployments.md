---
title: Geliştirici portalı dağıtımlarını otomatikleştirin
titleSuffix: Azure API Management
description: Şirket içinde barındırılan geliştirici portalı içeriğini iki API Management hizmet arasında otomatik olarak geçirmeyi öğrenin.
author: erikadoyle
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: e189a9339f6ca3bc81148b86206ddd052392074f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741923"
---
# <a name="automate-developer-portal-deployments"></a>Geliştirici portalı dağıtımlarını otomatikleştirin

API Management geliştirici portalı, içeriğe programlı erişimi destekler. [İçerik yönetimi REST API](/rest/api/apimanagement/)aracılığıyla bir API Management hizmetine veri aktarmanıza veya bu hizmetten dışarı aktarmanızı sağlar. REST API erişim, hem yönetilen hem de şirket içinde barındırılan portallar için geçerlidir.

## <a name="automated-migration-script"></a>Otomatik geçiş betiği

İki API Management hizmet arasında içerik geçişini otomatik hale getirmek için API 'yi kullanabilirsiniz; Örneğin, test ortamında bir hizmet ve üretim ortamındaki bir hizmet. `scripts.v3/migrate.js`API Management geliştirici portalı [GitHub deposu](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v3/migrate.js) 'ndaki betik bu Otomasyon işlemini basitleştirir.

> [!WARNING]
> Betik, hedef API Management hizmetinize geliştirici portalının içeriğini kaldırır. İlgili endişeleriniz varsa, bir yedekleme gerçekleştirdiğinizden emin olun.

> [!NOTE]
> Medya dosyalarını barındırmak için açıkça tanımlanmış bir özel depolama hesabıyla şirket içinde barındırılan bir portal kullanıyorsanız (yani, `blobStorageUrl` ayarı `config.design.json` yapılandırma dosyasında tanımlarsanız), özgün `scripts/migrate.js` [betiği](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v2/migrate.js)kullanmanız gerekir. Özgün betik, API Management tarafından yönetilen medya depolama hesabı ile yönetilen veya şirket içinde barındırılan portallarda çalışmıyor. Bu durumda, `/scripts.v3` bunun yerine klasöründen betiği kullanın.

Betik aşağıdaki adımları gerçekleştirir:

1. Kaynak API Management hizmetinden portal içeriğini ve medyayı yakalayın.
1. Portal içeriğini ve medyayı hedef API Management hizmetinden kaldırın.
1. Portal içeriğini ve medyasını hedef API Management hizmetine yükleyin.
1. İsteğe bağlı olarak ve yalnızca yönetilen portallar için portalı otomatik olarak yayımlayın.

Betik başarıyla yürütüldükten sonra, hedef API Management hizmeti, kaynak hizmetle aynı portal içeriğini içermelidir ve bunu yönetici olarak görebileceksiniz.

* Yönetilen bir portal kullanıyorsanız, geçirilen sürümü otomatik olarak ziyaretçilerin kullanımına sunmak için betiği hedef portalı otomatik olarak yayımlayacak şekilde ayarlayabilirsiniz. 
* Şirket içinde barındırılan bir portal kullanıyorsanız, hedef portalı el ile yayımlamanız gerekir. [Kendi kendine barındırılan bir geliştirici portalı kurmak](developer-portal-self-host.md)için öğreticideki yayımlama ve barındırma yönergelerini izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Geliştirici portalı hakkında daha fazla bilgi edinin:

- [Azure API Management geliştirici portalına genel bakış](api-management-howto-developer-portal.md)
- [Geliştirici portalını kendi kendine barındırma](developer-portal-self-host.md)