---
title: Azure AD kimlik doğrulaması kullanarak VPN kullanıcıları için MFA 'yı etkinleştirme
description: Azure AD kimlik doğrulaması kullanarak VPN kullanıcılarına yönelik Azure AD Multi-Factor Authentication (MFA) özelliğini nasıl etkinleştirebileceğinizi öğrenin.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: e8d90653372b78aad78fad66e4cde21bd2ab81ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995632"
---
# <a name="enable-azure-ad-multi-factor-authentication-mfa-for-vpn-users-by-using-azure-ad-authentication"></a>Azure AD kimlik doğrulamasını kullanarak VPN kullanıcıları için Azure AD Multi-Factor Authentication (MFA) etkinleştirme

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enable-authentication"></a><a name="enableauth"></a>Kimlik doğrulamayı etkinleştir

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="configure-sign-in-settings"></a><a name="enablesign"></a>Oturum açma ayarlarını yapılandırma

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="option-1---per-user-access"></a><a name="peruser"></a>Seçenek 1-Kullanıcı başına erişim

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="option-2---conditional-access"></a><a name="conditional"></a>Seçenek 2-Koşullu erişim

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>Sonraki adımlar

Sanal ağınıza bağlanmak için bir VPN istemci profili oluşturmanız ve yapılandırmanız gerekir. Bkz. Azure [Ile Noktadan siteye bağlantı Için Azure AD kimlik doğrulamasını yapılandırma](virtual-wan-point-to-site-azure-ad.md).
