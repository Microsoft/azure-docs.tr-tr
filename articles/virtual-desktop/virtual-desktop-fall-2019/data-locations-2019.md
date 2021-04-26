---
title: Windows sanal masaüstü (klasik) için veri konumları-Azure
description: Windows sanal masaüstü (klasik) verilerinin ve meta verilerinin hangi konumlarına depolanabileceğini kısaca inceleyin.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 7726795582ffa3d85601ec123b58ab705442ef67
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445015"
---
# <a name="data-locations-for-windows-virtual-desktop-classic"></a>Windows sanal masaüstü için veri konumları (klasik)

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir. Azure Resource Manager Windows sanal masaüstü nesnelerini yönetmeye çalışıyorsanız, [Bu makaleye](../data-locations.md)bakın.

Windows sanal masaüstü Şu anda tüm coğrafi konumlar için kullanılabilir. Başlangıçta, hizmet meta verileri yalnızca Birleşik Devletler (US) Coğrafya 'da depolanabilir. Yöneticiler, konak havuzu sanal makinelerini ve dosya sunucuları gibi ilişkili hizmetleri oluştururken Kullanıcı verilerinin depolayabileceği konumu seçebilir. Azure [veri merkezi haritasında](https://azuredatacentermap.azurewebsites.net/)Azure coğrafi lıkları hakkında daha fazla bilgi edinin.

>[!NOTE]
>Microsoft, sizin veya kullanıcılarınızın Kullanıcı ve uygulamaya özel verilerinize erişebileceği bölgeleri denetlemez veya sınırlamaz.

>[!IMPORTANT]
>Windows sanal masaüstü, Birleşik Devletler bulunan bir veri merkezinde kiracı adları, ana bilgisayar havuzu adları, uygulama grubu adları ve Kullanıcı asıl adları gibi genel meta veri bilgilerini depolar. Depolanan meta veriler Rest 'de şifrelenir ve coğrafi olarak yedekli yansıtmalar Birleşik Devletler içinde tutulur. Uygulama ayarları ve Kullanıcı verileri gibi tüm müşteri verileri, müşterinin seçtiği ve hizmet tarafından yönetilmediği konumda bulunur.

Hizmet meta verileri olağanüstü durum kurtarma amaçları için Birleşik Devletler çoğaltılır.