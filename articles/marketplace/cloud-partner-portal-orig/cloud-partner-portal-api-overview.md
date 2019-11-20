---
title: API başvurusunu Bulut İş Ortağı Portalı | Azure Marketi
description: Açıklaması, kullanım önkoşulları ve Market API işlemlerinin listesi.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: b6591e1780d03cbfaff70fbd19ec3dfd274fae79
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819613"
---
<a name="cloud-partner-portal-api-reference"></a>Bulut İş Ortağı Portalı API başvurusu
==================================

Bulut İş Ortağı Portalı REST API 'Leri, iş yüklerinin, tekliflerin ve yayımcı profillerinin programlı olarak alınmasına ve işlemesine olanak sağlar. API 'Ler, işlem zamanında doğru izinleri zorlamak için rol tabanlı erişim denetimi (RBAC) kullanır.

Bu başvuru Bulut İş Ortağı Portalı REST API 'Leri için teknik ayrıntılar sağlar. Bu belgedeki yük örnekleri yalnızca başvuru amaçlıdır ve yeni işlevsellik eklendikçe değişikliğe tabidir.


<a name="prerequisites-and-considerations"></a>Önkoşullar ve konular
-------------------------------

API 'Leri kullanmadan önce şunları gözden geçirmeniz gerekir:

- Hesabınıza bir hizmet sorumlusu ekleme ve kimlik doğrulaması için bir Azure Active Directory (Azure AD) erişim belirteci alma hakkında bilgi edinmek için [Önkoşul](./cloud-partner-portal-api-prerequisites.md) makalesi. 
- İki [eşzamanlılık denetimi](./cloud-partner-portal-api-concurrency-control.md).
Bu API 'Leri çağırmak için kullanılabilen stratejiler.
- Sürüm oluşturma ve hata işleme gibi ek API [konuları](./cloud-partner-portal-api-considerations.md).


<a name="common-tasks"></a>Genel görevler
------------
Bu başvuru, aşağıdaki ortak görevleri gerçekleştirmek için API 'Ler hakkında bilgi sağlar.


### <a name="offers"></a>Teklifler

-   [Tüm teklifleri al](./cloud-partner-portal-api-retrieve-offers.md)
-   [Belirli bir teklifi alma](./cloud-partner-portal-api-retrieve-specific-offer.md)
-   [Teklif durumunu alma](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [Teklif oluşturma](./cloud-partner-portal-api-creating-offer.md)
-   [Teklif yayımlama](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>İşlemler

-   [İşlemleri alma](./cloud-partner-portal-api-retrieve-operations.md)
-   [İşlemleri iptal etme](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Uygulama yayımlama

-   [Canlı yayına geçme](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Diğer görevler

-   [Sanal makine teklifleri için fiyatlandırmayı ayarla](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Sorun giderme

-   [Kimlik doğrulaması sorunlarını giderme](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
