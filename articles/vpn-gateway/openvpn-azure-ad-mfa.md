---
title: "VPN kullanıcıları için MFA 'yı etkinleştirme: Azure AD kimlik doğrulaması | Microsoft Docs"
description: VPN kullanıcıları için Multi-Factor Authentication 'ı etkinleştirme
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: alzam
ms.openlocfilehash: 3f407ecfcbfdff72b79bfe6b5bc3e6b7606b1cf0
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174635"
---
# <a name="enable-multi-factor-authentication-mfa-for-vpn-users"></a>VPN kullanıcıları için Multi-Factor Authentication (MFA) etkinleştirme

Azure AD kiracınızdaki kullanıcılar için MFA 'yı etkinleştirerek, erişim verilmeden önce kullanıcılardan ikinci faktör kimlik doğrulaması istenir.

> [! Önkoşul] "Kiracı yapılandırma" belgesinde açıklandığı gibi bir Azure AD kiracısı yapılandırdınız.
>

### <a name="tenant"></a>1. Azure portal oturum açın ve **Azure Active Directory** , **tüm kullanıcılar** ' a gidin ve **Multi-Factor Authentication** ' e tıklayın.


   ![Yeni Azure AD kiracısı](./media/openvpn-azure-ad-mfa/mfa1.jpg)

### <a name="users"></a>2. MFA 'yı etkinleştirmek istediğiniz kullanıcıları seçin ve **Etkinleştir** 'e tıklayın

   ![Yeni Azure AD kiracısı](./media/openvpn-azure-ad-mfa/mfa2.jpg)

### <a name="enable-authentication"></a>3. **Azure Active Directory** , **Kurumsal uygulamalar**, **tüm uygulamalar** ' a gidin ve **Azure VPN** 'ye tıklayın


   ![Dizin KIMLIĞI](./media/openvpn-azure-ad-mfa/user1.jpg)

### <a name="users"></a>4. **kullanıcıların oturum açması Için etkinleştirilmiş** olduğundan emin olun, Evet olarak ayarlayın. Yalnızca Azure VPN izinleri olan kullanıcıların oturum açabiliyor ve **Kullanıcı atamasını gerekli** olacak şekilde ayarlamak ISTIYORSANıZ, ad kiracısındaki tüm kullanıcılar VPN 'e başarıyla bağlanabilir.

   ![İzinler](./media/openvpn-azure-ad-mfa/user2.jpg)


## <a name="next-steps"></a>Sonraki adımlar

Sanal ağınıza bağlanmak için bir VPN istemci profili oluşturmanız ve yapılandırmanız gerekir. Bkz. [P2S VPN bağlantıları IÇIN VPN Istemcisi yapılandırma](openvpn-azure-ad-client.md).
