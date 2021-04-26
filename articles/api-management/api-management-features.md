---
title: Azure API Management katmanları için özellik tabanlı karşılaştırma | Microsoft Docs
description: API Management katmanlarını, sundukları özelliklere göre karşılaştırın. Her fiyatlandırma katmanında kullanılabilen temel özellikleri özetleyen bir tablo görüntüleyin.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 04/13/2021
ms.author: apimpm
ms.openlocfilehash: f111729d7d7707ed4f40ce8f89ce76975fb47400
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536458"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Azure API Management katmanlarının Özellik tabanlı karşılaştırması

Her API Management [fiyatlandırma katmanı](https://aka.ms/apimpricing) , ayrı bir özellik kümesi ve birim [kapasitesi](api-management-capacity.md)başına sunulur. Aşağıdaki tabloda, katmanların her birinde kullanılabilen temel özellikler özetlenmektedir. Bazı özellikler farklı çalışabilir veya katmana bağlı olarak farklı yeteneklere sahip olabilir. Bu gibi durumlarda, bu ayrı özellikleri açıklayan belge makalelerinde farklılıklar çağrılır.

> [!IMPORTANT]
> Geliştirici katmanının üretim dışı kullanım örnekleri ve değerlendirmeleri için olduğunu lütfen unutmayın. SLA sunmaz.

| Özellik                                                                                      | Tüketim | Geliştirici | Temel | Standart | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Azure AD tümleştirmesi<sup>1</sup>                                                             | Hayır          | Yes       | Hayır    | Yes      | Yes     |
| Sanal ağ (VNet) desteği                                                               | Hayır          | Yes       | Hayır    | Hayır       | Yes     |
| Çok bölgeli dağıtım                                                                      | Hayır          | Hayır        | Hayır    | Hayır       | Yes     |
| Kullanılabilirlik alanları                                                                           | Hayır          | Hayır        | Hayır    | Hayır       | Yes     |
| Birden çok özel etki alanı adı                                                                 | Hayır          | Yes        | Hayır    | Hayır       | Yes     |
| Geliştirici portalı<sup>2</sup>                                                                 | Hayır          | Yes       | Yes   | Yes      | Yes     |
| Yerleşik önbellek                                                                               | Hayır          | Yes       | Yes   | Yes      | Yes     |
| Yerleşik analiz                                                                           | Hayır          | Yes       | Yes   | Yes      | Yes     |
| [Şirket içinde barındırılan ağ geçidi](self-hosted-gateway-overview.md)<sup>3</sup>                           | Hayır          | Yes       | Hayır    | Hayır       | Yes     |
| [TLS ayarları](api-management-howto-manage-protocols-ciphers.md)                             | Yes         | Yes       | Yes   | Yes      | Yes     |
| [Dış önbellek](./api-management-howto-cache-external.md)                                                    | Yes         | Yes       | Yes   | Yes      | Yes     |
| [İstemci sertifikası kimlik doğrulaması](api-management-howto-mutual-certificates-for-clients.md) | Yes         | Yes       | Yes   | Yes      | Yes     |
| [Yedekleme ve geri yükleme](api-management-howto-disaster-recovery-backup-restore.md)               | Hayır          | Yes       | Yes   | Yes      | Yes     |
| [Git üzerinden yönetim](api-management-configuration-repository-git.md)                        | Hayır          | Yes       | Yes   | Yes      | Yes     |
| Doğrudan yönetim API 'SI                                                                        | Hayır          | Yes       | Yes   | Yes      | Yes     |
| Azure Izleyici günlükleri ve ölçümleri                                                               | Hayır          | Yes       | Yes   | Yes      | Yes     |
| Statik IP                                                                                    | Hayır          | Yes       | Yes   | Yes      | Yes     |

<sup>1</sup> Azure AD 'nin (ve Azure AD B2C) kullanımını, Geliştirici Portalında Kullanıcı oturum açması için bir kimlik sağlayıcısı olarak sunar.<br/>
<sup>2</sup> Kullanıcı, gruplar, sorunlar, uygulamalar ve e-posta şablonları ve bildirimler gibi ilgili işlevler dahil.<br/>
<sup>3</sup> geliştirici katmanında barındırılan ağ geçitlerinde tek ağ geçidi düğümüyle sınırlı.<br/>