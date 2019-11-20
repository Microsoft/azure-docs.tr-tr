---
title: Azure API Management örneğiniz için özel bir etki alanı adı yapılandırma | Microsoft Docs
description: Bu konu başlığı altında, Azure API Management örneğiniz için özel bir etki alanı adının nasıl yapılandırılacağı açıklanmaktadır.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/12/2019
ms.author: apimpm
ms.openlocfilehash: ba2288fa4add6df6b7861b5f8a99bcd3ee8b4c12
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472206"
---
# <a name="configure-a-custom-domain-name"></a>Özel bir etki alanı adı yapılandırma

Azure API Management hizmet örneği oluşturduğunuzda Azure, azure-api.net 'in bir alt etki alanını (örneğin, `apim-service-name.azure-api.net`) atar. Ancak, **contoso.com**gibi kendi özel etki alanı adınızı kullanarak API Management uç noktalarınızı kullanıma sunabilirsiniz. Bu öğreticide, mevcut bir özel DNS adını API Management örneği tarafından sunulan uç noktalara nasıl eşleyebileceğiniz gösterilmektedir.

> [!WARNING]
> Uygulamalarının güvenliğini geliştirmek için sertifika sabitleme kullanmak isteyen müşterilerin, varsayılan sertifikayı değil, yönettikleri bir özel etki alanı > adı ve sertifika kullanması gerekir. Bunun yerine varsayılan sertifikayı sabitletirecek müşteriler, denetimi olmadıkları sertifikanın özelliklerine > ve bu önerilen bir uygulama değildir.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede açıklanan adımları gerçekleştirmek için şunları yapmanız gerekir:

-   Etkin bir Azure aboneliği.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Bir API Management örneği. Daha fazla bilgi için bkz. [Azure API Management örneği oluşturma](get-started-create-service-instance.md).
-   Siz veya kuruluşunuzun sahip olduğu özel bir etki alanı adı. Bu konu, özel bir etki alanı adını nasıl temin etmek için yönergeler sağlamaz.
-   Özel etki alanı adını API Management örneğinizin varsayılan etki alanı adına eşleyen bir DNS sunucusunda barındırılan bir CNAME kaydı. Bu konu, CNAME kaydını barındırmak için yönergeler sağlamaz.
-   Ortak ve özel anahtarı olan geçerli bir sertifikanız olmalıdır (. PFX). Konu veya konu diğer adı (SAN), etki alanı adıyla eşleşmelidir (Bu, API Management örneğinin SSL üzerinden URL 'Leri güvenli bir şekilde kullanıma sunmasına olanak sağlar).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Özel bir etki alanı adı ayarlamak için Azure portal kullanın

1. [Azure portal](https://portal.azure.com/)API Management örneğine gidin.
1. **Özel etki alanları ' nı**seçin.

    Özel bir etki alanı adı atayabilmeniz için birkaç uç nokta vardır. Şu anda aşağıdaki uç noktalar kullanılabilir:

    - **Ağ geçidi** (varsayılan: `<apim-service-name>.azure-api.net`),
    - **Portal** (varsayılan: `<apim-service-name>.portal.azure-api.net`),
    - **Yönetim** (varsayılan: `<apim-service-name>.management.azure-api.net`),
    - **SCM** (varsayılan: `<apim-service-name>.scm.azure-api.net`).

    > [!NOTE]
    > Yalnızca tüketim katmanında yapılandırma için kullanılabilir olan **ağ geçidi** uç noktası.
    > Tüm uç noktaları veya bunlardan bazılarını güncelleştirebilirsiniz. Genellikle müşteriler **ağ geçidini** güncelleştirir (Bu URL, API Management Ile sunulan API 'yi çağırmak için kullanılır) ve **Portal** (geliştirici portalı URL 'si).
    > **Yönetim** ve **SCM** uç noktaları yalnızca API Management örnek sahipleri tarafından dahili olarak kullanılır ve bu nedenle, özel bir etki alanı adı daha az sıklıkla atanır.
    > **Premium** katmanı, **ağ geçidi** uç noktası için birden çok konak adı ayarlamayı destekler.

1. Güncelleştirmek istediğiniz uç noktayı seçin.
1. Sağdaki pencerede **Özel ' e**tıklayın.

    - **Özel etki alanı adı**' nda, kullanmak istediğiniz adı belirtin. Örneğin, `api.contoso.com`.
    - **Sertifikada**Key Vault bir sertifika seçin. Geçerli bir de yükleyebilirsiniz. Sertifika bir parolayla korunuyorsa PFX dosyası ve **parolasını**girin.

    > [!NOTE]
    > Joker karakter etki alanı adları, örn. `*.contoso.com`, tüketim katmanı hariç tüm katmanlarda desteklenir.

    > [!TIP]
    > Sertifikaları yönetmek için Azure Key Vault kullanmanızı ve onları oto döndürme için ayarlamayı öneririz.
    > Özel etki alanı SSL sertifikasını yönetmek için Azure Key Vault kullanıyorsanız, sertifikanın _gizli_değil, [ _sertifika_olarak](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate)Key Vault yerleştirildiğinden emin olun.
    >
    > Bir SSL sertifikası getirmek için, API Management sertifikayı içeren Azure Key Vault üzerinde parolaları Al izinleri listesine sahip olmalıdır. Azure portal kullanırken, tüm gerekli yapılandırma adımları otomatik olarak tamamlanır. Komut satırı araçları veya yönetim API 'SI kullanılırken, bu izinlerin el ile verilmesi gerekir. Bu iki adımda yapılır. İlk olarak, yönetilen kimliğin etkin olduğundan emin olmak için API Management örnekte Yönetilen kimlikler sayfasını kullanın ve bu sayfada gösterilen asıl kimliği bir yere göz önünde yapın. İkincisi, izin listesini verin ve sertifikayı içeren Azure Key Vault bu asıl kimliğe gizli dizi izinleri alın.
    >
    > Sertifika, otomatik döndürme olarak ayarlandıysa, API Management hizmetin herhangi bir kesinti süresi olmadan en son sürümü otomatik olarak seçer (API Management katmanınızda, geliştirici katmanı hariç tüm katmanlarda SLA-i. e.).

1. Uygula ' ya tıklayın.

    > [!NOTE]
    > Sertifikayı atama işlemi, dağıtımın boyutuna bağlı olarak 15 dakika veya daha fazla sürebilir. Geliştirici SKU 'SU kapalı, temel ve daha yüksek SKU 'Ların kapalı kalma süresi yoktur.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>DNS yapılandırması

Özel etki alanı adınız için DNS yapılandırılırken iki seçeneğiniz vardır:

-   Yapılandırılmış özel etki alanı adınızın uç noktasını işaret eden bir CNAME kaydı yapılandırın.
-   API Management ağ geçidi IP adresinizi işaret eden bir A-kaydı yapılandırın.

> [!NOTE]
> API Yönetimi örneğinin IP adresi statik olsa da, birkaç senaryoda değişebilir. Bu nedenle, özel etki alanı yapılandırılırken CNAME kullanılması önerilir. DNS yapılandırma yöntemi seçerken göz önüne alın. [API Management SSS](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)bölümünde daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

[Hizmetinizi yükseltin ve ölçeklendirin](upgrade-and-scale.md)
