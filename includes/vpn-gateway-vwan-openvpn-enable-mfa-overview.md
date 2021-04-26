---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8fba8aa577bcb3b5ef44d57c388a1f1de7494782
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995633"
---
Erişim vermeden önce kullanıcılardan ikinci bir kimlik doğrulama faktörü istenmesini istiyorsanız, Azure AD Multi-Factor Authentication (MFA) yapılandırabilirsiniz. MFA 'yı her kullanıcı için ayrı ayrı yapılandırabilir veya [koşullu erişim](../articles/active-directory/conditional-access/overview.md)aracılığıyla MFA özelliğinden yararlanabilirsiniz.

* Kullanıcı başına MFA, ek ücret ödemeden etkinleştirilebilir. Kullanıcı başına MFA etkinleştirilirken, kullanıcıdan Azure AD kiracısına bağlı tüm uygulamalarda ikinci faktör kimlik doğrulaması istenir. Adımlar için bkz. [1. Seçenekler](#peruser) .
* Koşullu erişim, ikinci bir faktörün nasıl yükseltilmesi gerektiği konusunda daha ayrıntılı denetim sağlar. MFA 'nın yalnızca VPN 'e atanmasına ve Azure AD kiracısına bağlı diğer uygulamaları dışlayacak şekilde izin verebilir. Adımlar için bkz. [2](#conditional) .