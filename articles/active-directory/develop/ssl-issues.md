---
title: Amaç-C sorunları için MSAL sorunlarını giderme
titleSuffix: Microsoft identity platform
description: MSAL ile SSL sertifikalarını kullanarak çeşitli sorunlar hakkında ne yapacağınızı öğrenin. Amaç-C Kitaplığı.
services: active-directory
documentationcenter: ''
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc918502cd252b4e53af8bcbd209a8387ef4d8c2
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803659"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-ssl-issues"></a>Nasıl yapılır: iOS ve macOS SSL sorunları için MSAL sorunlarını giderme

Bu makalede, [iOS ve macOS Için Microsoft kimlik doğrulama kitaplığı 'nı (msal)](reference-v2-libraries.md) kullanırken, içinde karşılaşabileceğiniz sorunları gidermenize yardımcı olacak bilgiler sağlanmaktadır

## <a name="network-issues"></a>Ağ sorunları

**Hata-1200**: "bir SSL hatası oluştu ve sunucuyla güvenli bir bağlantı kurulamıyor."

Bu hata bağlantının güvenli olmadığı anlamına gelir. Bir sertifika geçersiz olduğunda meydana gelir. SSL denetiminde başarısız olan sunucu dahil olmak üzere daha fazla bilgi için, hata nesnesinin `userInfo` sözlüğünde `NSURLErrorFailingURLErrorKey` bakın.

Bu hata, Apple 'ın ağ kitaplığından yapılır. NSURL hata kodlarının tam listesi, macOS ve iOS SDK 'lerinde NSURLError. h içinde bulunur. Bu hatayla ilgili daha fazla bilgi için bkz. [URL yükleme sistem hata kodları](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc).

## <a name="certificate-issues"></a>Sertifika sorunları

Geçersiz bir sertifika sağlayan URL, kimlik doğrulama akışının bir parçası olarak kullanmayı düşündüğünüz sunucuya bağlanırsa, sorunu tanılamaya yönelik iyi bir başlangıç, URL 'YI [Qualys SSL Labs Çözümleyicisi](https://www.ssllabs.com/ssltest/analyze.html)gıbı bir SSL doğrulama hizmeti ile test etmek olur. Sunucuyu geniş bir senaryo ve tarayıcı dizisine karşı sınar ve bilinen birçok güvenlik açığını denetler.

Varsayılan olarak, Apple 'ın yeni [uygulama taşıma güvenliği (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) ÖZELLIĞI, SSL sertifikalarını kullanan uygulamalara daha sıkı güvenlik ilkeleri uygular. Bazı işletim sistemleri ve Web tarayıcıları, bu ilkelerin bazılarını varsayılan olarak zorlamayı kullanmaya başlamıştır. Güvenlik nedenleriyle, ATS devre dışı bırakmanızı öneririz.

SHA-1 karmaları kullanan sertifikaların bilinen güvenlik açıkları vardır. Modern Web tarayıcıları çoğu, SHA-1 karmaları olan sertifikalara izin vermez.

## <a name="captive-portals"></a>Kıltıcı portallar

Açıklamalı Portal, bir Wi-Fi ağına ilk kez erişirken ve henüz bu ağa erişim verilmemişse bir kullanıcıya Web sayfası sunar. Kullanıcı portalın gereksinimlerini karşılaana kadar internet trafiğini karşılar. Kullanıcı Portal üzerinden bağlanana kadar ağ kaynaklarına bağlanamadığı için ağ hataları beklenmektedir.

## <a name="next-steps"></a>Sonraki adımlar

[Açıklamalı portallar](https://en.wikipedia.org/wiki/Captive_portal) ve Apple 'ın yeni [uygulama TAŞıMA güvenliği (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) özelliği hakkında bilgi edinin.
