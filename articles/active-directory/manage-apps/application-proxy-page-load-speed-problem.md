---
title: Uygulama proxy 'Si uygulamasının yüklenmesi çok uzun sürüyor | Microsoft Docs
description: Azure AD Uygulama Ara Sunucusu sayfa yükleme performans sorunlarını giderme
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17cb11cc82ede3b51b5502730079618e136d3ca5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99254483"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Uygulama proxy 'Si uygulamasının yüklenmesi çok uzun sürüyor

Bu makale, bir Azure AD Uygulama Ara Sunucusu uygulamasının yüklenmesi uzun sürebileceğini anlamanıza yardımcı olur. Ayrıca, bu sorunu çözmek için neler yapabileceğinizi açıklar.

## <a name="overview"></a>Genel Bakış
Uygulamalarınız çalışıyor olsa da uzun bir gecikme yaşar. Hızı artırmak için yapabileceğiniz ağ topolojisi tiknoktaları olabilir. Farklı topolojilerden oluşan bir değerlendirme için bkz. [ağ hususları belgesi](application-proxy-network-topology.md).

Ağ topolojisinin yanı sıra, şu anda performans ayarlaması için daha fazla öneri yoktur. Uygulama proxy hizmeti genişledikçe, fiziksel olarak daha yakın bir veri merkezine gelebilir. Daha yakın yakınlık, gecikme süresiyle yardımcı olabilirler. Azure veri merkezlerinin bir listesi için bkz. [gecikme süresi sınama sayfası](http://www.azurespeed.com/Azure/Latency). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Uygulama proxy 'Si veri merkezi konumları hakkında geri bildirim 
Henüz uygulama proxy 'Si içermeyen Azure veri merkezleri olabilir, ancak sizin için harika bir gecikme süresine neden olur. Veri merkezinin konumunu konumuna gönderin aadapfeedback@microsoft.com . Microsoft, genişletme planları için geri bildirimlerinizi kullanır.

Microsoft, gecikme süresini artırmaya yönelik ek yetenekler üzerinde çalışmaktadır. Bu geliştirmeler kullanılabilir duruma geldiğinde belgeler güncelleştirilir.

## <a name="next-steps"></a>Sonraki adımlar
[Mevcut şirket içi proxy sunucularıyla çalışma](application-proxy-configure-connectors-with-proxy-servers.md)
