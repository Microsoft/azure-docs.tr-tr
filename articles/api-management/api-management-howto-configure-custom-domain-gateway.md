---
title: Şirket içinde barındırılan Azure API Management ağ geçidiniz için özel bir etki alanı adı yapılandırma | Microsoft Docs
description: Bu konuda, şirket içinde barındırılan Azure API Management Gateway için özel bir etki alanı adı yapılandırmaya yönelik adımlar açıklanmaktadır.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: d52bf87b74ae9b1770ed5092738fd05eb9f54fde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99491041"
---
# <a name="configure-a-custom-domain-name-for-a-self-hosted-gateway"></a>Şirket içinde barındırılan ağ geçidi için özel etki alanı adı yapılandırma

Şirket içinde barındırılan bir [Azure API Management Gateway](self-hosted-gateway-overview.md)sağladığınızda, bir ana bilgisayar adı ATANMAZ ve IP adresi tarafından başvurulmalıdır. Bu makalede, mevcut bir özel DNS adının (ana bilgisayar adı olarak da bilinir) şirket içinde barındırılan bir ağ geçidine nasıl eşleneceğini gösterir.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede açıklanan adımları gerçekleştirmek için şunları yapmanız gerekir:

-   Etkin bir Azure aboneliği.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Bir API Management örneği. Daha fazla bilgi için bkz. [Azure API Management örneği oluşturma](get-started-create-service-instance.md).
- Şirket içinde barındırılan ağ geçidi. Daha fazla bilgi için bkz. [Şirket içinde barındırılan ağ geçidini sağlama](api-management-howto-provision-self-hosted-gateway.md)
-   Siz veya kuruluşunuzun sahip olduğu özel bir etki alanı adı. Bu konu, özel bir etki alanı adını nasıl temin etmek için yönergeler sağlamaz.
-   Özel etki alanı adını kendi kendine barındırılan ağ geçidinin IP adresine eşleyen bir DNS sunucusunda barındırılan bir DNS kaydı. Bu konu, bir DNS kaydını barındırmak için yönergeler sağlamaz.
-   Ortak ve özel anahtarı olan geçerli bir sertifikanız olmalıdır (. PFX). Konu veya konu diğer adı (SAN), etki alanı adıyla eşleşmelidir (Bu, API Management örneğinin, TLS üzerinden URL 'Leri güvenli bir şekilde kullanıma sunmasına olanak sağlar).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>API Management hizmetinize özel etki alanı sertifikası ekleme

Özel bir etki alanı sertifikası (. PFX) dosyası API Management örneğine veya Azure Key Vault depolanan bir sertifikaya başvuru. [Azure API Management 'de istemci sertifikası kimlik doğrulamasını kullanarak güvenli arka uç hizmetlerindeki](api-management-howto-mutual-certificates.md)adımları izleyin.

> [!NOTE]
> Şirket içinde barındırılan ağ geçidi etki alanı için bir Anahtar Kasası sertifikası kullanmanızı öneririz.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Şirket içinde barındırılan ağ geçidiniz için özel bir etki alanı adı ayarlamak üzere Azure portal kullanın

1. **Dağıtım ve altyapı** altında **ağ geçitlerini** seçin.
2. Etki alanı adını yapılandırmak istediğiniz şirket içinde barındırılan ağ geçidini seçin.
3. **Ayarlar** altında **ana bilgisayar adları** ' nı seçin.
4. **+ Ekle** ' yi seçin
5. **Ad** alanına ana bilgisayar adının kaynak adını girin.
6. **Ana bilgisayar** adı alanına etki alanı adını girin.
7. **Sertifika** açılan listesinden bir sertifika seçin.
8. Bu ağ geçidi aracılığıyla sunulan API 'lerden herhangi biri istemci sertifikası kimlik doğrulamasını kullanıyorsa **istemci sertifikası** onay kutusunu seçin.
    > [!WARNING]
    > Bu ayar, ağ geçidi için yapılandırılmış tüm etki alanı adları tarafından paylaşılır.
9. Özel etki alanı adını Seçili şirket içinde barındırılan ağ geçidine atamak için **Ekle** ' yi seçin.

## <a name="next-steps"></a>Sonraki adımlar

[Hizmetinizi yükseltin ve ölçeklendirin](upgrade-and-scale.md)
