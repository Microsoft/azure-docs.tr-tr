---
title: Şirket içinde barındırılan Azure API Management ağ geçidiniz için özel bir etki alanı adı yapılandırma | Microsoft Docs
description: Bu konuda, şirket içinde barındırılan Azure API Management Gateway için özel bir etki alanı adı yapılandırmaya yönelik adımlar açıklanmaktadır.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 1df2cce04021c1cd14c356311df921dd1c0298e4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513815"
---
# <a name="configure-a-custom-domain-name"></a>Özel bir etki alanı adı yapılandırma

Şirket içinde barındırılan bir [Azure API Management Gateway](self-hosted-gateway-overview.md) sağladığınızda konak adı ATANMAZ ve IP adresi tarafından başvurulmalıdır. Bu makalede, şirket içinde barındırılan bir ağ geçidinin mevcut bir özel DNS adını (konak adı olarak da bilinir) nasıl eşleyebileceğiniz gösterilmektedir.

> [!NOTE]
> Şirket içinde barındırılan ağ geçidi özelliği önizlemededir. Önizleme sırasında, şirket içinde barındırılan ağ geçidi, yalnızca geliştirici ve Premium katmanlarda ek ücret ödemeden kullanılabilir. Geliştirici katmanı, tek bir şirket içinde barındırılan ağ geçidi dağıtımıyla sınırlıdır.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede açıklanan adımları gerçekleştirmek için şunları yapmanız gerekir:

-   Etkin bir Azure aboneliği.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Bir API Management örneği. Daha fazla bilgi için bkz. [Azure API Management örneği oluşturma](get-started-create-service-instance.md).
- Şirket içinde barındırılan ağ geçidi. Daha fazla bilgi için bkz. [Şirket içinde barındırılan ağ geçidini sağlama](api-management-howto-provision-self-hosted-gateway.md)
-   Siz veya kuruluşunuzun sahip olduğu özel bir etki alanı adı. Bu konu, özel bir etki alanı adını nasıl temin etmek için yönergeler sağlamaz.
-   Özel etki alanı adını kendi kendine barındırılan ağ geçidinin IP adresine eşleyen bir DNS sunucusunda barındırılan bir DNS kaydı. Bu konu, bir DNS kaydını barındırmak için yönergeler sağlamaz.
-   Ortak ve özel anahtarı olan geçerli bir sertifikanız olmalıdır (. PFX). Konu veya konu diğer adı (SAN), etki alanı adıyla eşleşmelidir (Bu, API Management örneğinin SSL üzerinden URL 'Leri güvenli bir şekilde kullanıma sunmasına olanak sağlar).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>API Management hizmetinize özel etki alanı sertifikası ekleme

1. **Güvenlik**altında **Sertifikalar** ' ı seçin.
2. **+ Ekle** öğesini seçin.
3. Sertifika için **kimlik** alanına bir kaynak adı girin.
4. Sertifikayı içeren dosyayı seçin (. PFX) **sertifika** alanını veya yanındaki klasör simgesini seçerek.
5. **Parola** alanına sertifika parolasını girin.
6. Sertifikayı API Management hizmetinize eklemek için **Oluştur** ' u seçin.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Şirket içinde barındırılan ağ geçidiniz için özel bir etki alanı adı ayarlamak üzere Azure portal kullanın

1. **Ayarlar**altında **ağ geçitlerini** seçin.
2. Etki alanı adını yapılandırmak istediğiniz şirket içinde barındırılan ağ geçidini seçin.
3. **Ayarlar**altında **ana bilgisayar adları** ' nı seçin.
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
