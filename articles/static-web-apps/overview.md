---
title: Azure Statik Web Uygulamaları nedir?
description: Azure statik Web Apps 'nin temel özellikleri ve işlevleri.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 04/01/2021
ms.author: cshoe
ms.openlocfilehash: 945979daa3c766b737e5b312a6c14e60204a6a55
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874072"
---
# <a name="what-is-azure-static-web-apps-preview"></a>Azure statik Web Apps önizlemesi nedir?

Azure statik Web Apps, tam yığın Web uygulamalarını otomatik olarak oluşturup bir kod deposundan Azure 'a dağıtan bir hizmettir.

:::image type="content" source="media/overview/azure-static-web-apps-overview.png" alt-text="Azure statik Web Apps genel bakış Diyagramı":::

Azure statik Web Apps iş akışı, geliştiricinin günlük iş akışına göre tasarlanmıştır. Uygulamalar kod değişikliklerinden oluşturulur ve dağıtılır.

Azure statik Web Apps kaynağı oluşturduğunuzda, Azure seçtiğiniz bir dalı izlemek için GitHub veya Azure DevOps ile doğrudan etkileşime girer. Değişiklikleri izlenen dala her gönderişinizde veya çekme isteklerini kabul ettiğinizde, bir yapı otomatik olarak çalıştırılır ve uygulamanız ve API 'SI Azure 'a dağıtılır.

Statik Web Apps, genellikle angular, tepki, svelte, Vue veya Blazor gibi sunucu tarafı işlemenin gerekmediği gibi kitaplıklar ve çerçeveler kullanılarak oluşturulmuştur. Bu uygulamalar, uygulamayı oluşturan HTML, CSS, JavaScript ve görüntü varlıklarını içerir. Geleneksel bir Web sunucusu ile, bu varlıklar, gerekli tüm API uç noktaları ile birlikte tek bir sunucudan sunulur.

Statik Web Apps, statik varlıklar geleneksel bir Web sunucusundan ayrılır ve bunun yerine dünyanın dört bir yanındaki coğrafi olarak dağıtılan noktalarından sunulur. Dosyalar son kullanıcılara fiziksel olarak daha yakında bulunduğu için bu dağıtım yöntemi dosyaların daha hızlı sunulmasına olanak tanır. Ayrıca, API uç noktaları [sunucusuz bir mimari](../azure-functions/functions-overview.md)kullanılarak barındırılır ve tamamen bir arka uç sunucusu gereksinimini ortadan kaldırır.

## <a name="key-features"></a>Önemli özellikler

- HTML, CSS, JavaScript ve görüntüler gibi statik içerik için **Web barındırma** .
- Azure Işlevleri tarafından sunulan **TÜMLEŞIK API** desteği.
- **İlk sınıf GitHub ve Azure DevOps tümleştirmesi** depo değişikliklerinin derlemeleri ve dağıtımları tetiklemesi.
- **Genel olarak dağıtılmış** statik içerik, içeriği kullanıcılarınıza yaklaştırır.
- Otomatik olarak yenilenen **ÜCRETSIZ SSL sertifikaları**.
- Uygulamanıza markalı özelleştirmeler sağlamak için **özel etki alanları** .
- CORS yapılandırması gerektirmeyen API 'Ler çağrılırken ters proxy ile **sorunsuz güvenlik modeli** .
- Azure Active Directory, Facebook, Google, GitHub ve Twitter ile **kimlik doğrulama sağlayıcısı tümleştirmeleri** .
- **Özelleştirilebilir yetkilendirme rolü tanımı** ve atamaları.
- **Arka uç yönlendirme kuralları** , size kullandığınız içerik ve yollar üzerinde tam denetimi etkinleştirir.
- Çekme istekleri tarafından desteklenen **üretilen hazırlama sürümleri** , yayımlamadan önce sitenizin önizleme sürümlerini etkinleştirir.

## <a name="what-you-can-do-with-static-web-apps"></a>Statik Web Apps ile yapabilecekleriniz

- JavaScript çerçeveleri ve bir [Azure işlevleri](apis.md) arka ucu Ile WebAssembly uygulamaları [](getting-started.md?tabs=react)oluşturmak Için [](/learn/modules/publish-app-service-static-web-app-api/) [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) [kullanma gibi](getting-started.md?tabs=angular)JavaScript [](getting-started.md?tabs=vue)çerçeveleri ve kitaplıkları ile **Modern Web uygulamaları** oluşturun.
- [Gatsby](publish-gatsby.md), [kugo](publish-hugo.md), [vuepress](publish-vuepress.md)gibi çerçeveler ile **statik siteler yayımlayın** .
- [Next.js](deploy-nextjs.md) ve [Nuxt.js](deploy-nuxtjs.md)gibi çerçeveler ile **Web uygulamaları dağıtın** .

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [İlk statik uygulamanızı oluşturma](getting-started.md)
