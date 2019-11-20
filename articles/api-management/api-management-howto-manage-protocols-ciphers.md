---
title: Azure API Management protokolleri ve şifrelemeleri yönetme | Microsoft Docs
description: Azure API Management protokolleri (TLS, SSL) ve şifrelemeleri (DES) yönetmeyi öğrenin.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: f7c7fdd06480ce3da70c86d38ab0685b9b3aaaf2
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072398"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Azure API Management protokolleri ve şifrelemeleri yönetme

Azure API Management, hem istemci hem de arka uç tarafı ve 3DES şifresi için TLS protokolünün birden çok sürümünü destekler.

Bu kılavuzda, Azure API Management örneği için protokollerin ve şifre yapılandırmasının nasıl yönetileceği gösterilmektedir.

![APıM 'de protokolleri ve şifrelemeleri yönetme](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları izlemek için, şunları yapmanız gerekir:

* Bir API Management örneği

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>TLS protokollerini ve 3DES şifre yönetimini yönetme

1. Azure portal **API Management örneğine** gidin.
2. Menüden **protokol ayarları** ' nı seçin.  
3. İstenen protokolleri veya şifrelemeleri etkinleştirin veya devre dışı bırakın.
4. **Kaydet**’e tıklayın. Değişiklikler bir saat içinde uygulanır.  

## <a name="next-steps"></a>Sonraki adımlar

* [TLS (Aktarım Katmanı Güvenliği)](https://docs.microsoft.com/dotnet/framework/network-programming/tls)hakkında daha fazla bilgi edinin.
* API Management hakkında daha fazla [videoya](https://azure.microsoft.com/documentation/videos/index/?services=api-management) göz atın.