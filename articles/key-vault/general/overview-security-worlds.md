---
title: Azure Key Vault Security dünyaları | Microsoft Docs
description: Azure Key Vault, çok kiracılı bir hizmettir. Her Azure bölgesinde bir HSM havuzu kullanır. Coğrafi bölgedeki tüm bölgeler şifreleme sınırını paylaşır.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: 0d82a3cb4c08d47b6827072378b9827037d32412
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751823"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault güvenlik ve coğrafi sınırlar

Azure ürünleri, bir veya daha fazla bölge içeren her bir Azure coğrafya ile birçok [Azure coğrafi](https://azure.microsoft.com/en-us/global-infrastructure/geographies/)bölümünde bulunabilir. Örneğin, Avrupa coğrafya iki bölge içerir--Kuzey Avrupa ve Batı Avrupa--Brezilya Coğrafya 'daki tek bölge Brezilya Güney.

Azure Key Vault, donanım güvenlik modülleri (HSM 'ler) havuzunu kullanan çok kiracılı bir hizmettir. Bir Coğrafya 'daki tüm HSM 'ler, "güvenlik dünyası" olarak adlandırılan aynı şifreleme sınırını paylaşır. Her Coğrafya tek bir güvenlik dünyasına karşılık gelir ve tam tersi de geçerlidir.

Doğu ABD ve Batı ABD Coğrafya (Birleşik Devletler) ait olduklarından aynı güvenlik dünyasını paylaşır. Benzer şekilde, Japonya 'daki tüm Azure bölgeleri, Avustralya 'daki tüm Azure bölgeleri gibi aynı güvenlik dünyasını paylaşır ve bu şekilde devam eder.

>[!NOTE]
> Bir özel durum, US DOD Doğu ve ABD DOD CENTRAL 'ın kendi güvenlik dünyalarına sahip olduğu durumdur.

## <a name="backup-and-restore-behavior"></a>Yedekleme ve geri yükleme davranışı

Bu koşulların her ikisi de doğru olduğu sürece, bir Azure bölgesindeki anahtar kasasından bir anahtarın bir yedeğini almak başka bir Azure bölgesindeki anahtar kasasına geri yüklenebilir:

- Azure bölgelerinin her ikisi de aynı coğrafi bölgeye aittir.
- Her iki anahtar kasası da aynı Azure aboneliğine aittir.

Örneğin, bir Batı Hindistan anahtar kasasındaki bir anahtarın bir yedeklemesi, Hindistan Coğrafya (Batı Hindistan, Orta Hindistan ve Güney Hindistan bölgeleri) içindeki aynı abonelikte yer aldığı başka bir anahtar kasasına geri yüklenebilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Bölgeye göre Microsoft ürünlerine](https://azure.microsoft.com/regions/services/) bakın
