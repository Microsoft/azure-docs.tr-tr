---
title: Azure için avere vFXT hakkında ek bağlantılar
description: Azure için avere vFXT hakkında ek bilgilere bağlantılar
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: rohogue
ms.openlocfilehash: c8ac08e3d03e8eb525cad7d73bece40c515e31a1
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256286"
---
# <a name="additional-documentation"></a>Ek belgeler

Bu makalede, avere Denetim Masası yönetim arabirimi ve ilgili konular hakkında ek belgelere bağlantılar bulunur. 

## <a name="avere-cluster-documentation"></a>Avere kümesi belgeleri

Ek avere kümesi belgeleri <https://azure.github.io/Avere/> ' da Web sitesinde bulunabilir. Bu belgeler, kümenin yeteneklerini ve ayarlarını nasıl yapılandıracağınızı anlamanıza yardımcı olabilir. 

* [FXT kümesi oluşturma kılavuzu](<https://azure.github.io/Avere/#fxt_cluster>) , fiziksel donanım düğümlerinden oluşan kümeler için tasarlanmıştır, ancak belgedeki bazı bilgiler vFXT kümelerine de uygundur. Özellikle, yeni vFXT küme yöneticileri bu bölümleri okumayı yararlı olabilir:
  * [Destek ve Izleme ayarlarını](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) özelleştirmek, destek yükleme ayarlarını özelleştirmeyi ve uzaktan izlemeyi etkinleştirmeyi açıklar. 
  * [VServers ve Global ad alanı yapılandırması](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) , istemciye yönelik bir ad alanı oluşturma hakkında bilgi içerir.
  * [Avere kümesi IÇIN DNS yapılandırması,](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) hepsini bir kez deneme DNS 'nin nasıl yapılandırılacağını açıklar.
  * [Arka uç depolama belgeleri ekleme](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) temel filers ekleme.

* [Küme yapılandırma kılavuzu](<https://azure.github.io/Avere/#operations>) , bir avere kümesi için ayarların ve seçeneklerin tamamı başvurusudur. VFXT kümesi bu seçeneklerin bir alt kümesini kullanır, ancak aynı yapılandırma sayfalarının çoğu geçerlidir.

* [Pano kılavuzunda](<https://azure.github.io/Avere/#operations>) , avere Denetim Masası 'nın küme izleme özelliklerinin nasıl kullanılacağı açıklanmaktadır.

## <a name="vfxt-creation-and-management-documentation"></a>vFXT oluşturma ve yönetimi belgeleri

Bulut kümesi oluşturma ve yönetim yardımcı programı vfxt.py kullanmaya yönelik bir tam kılavuz GitHub: [vfxt.py Ile bulut kümesi Yönetimi](https://github.com/Azure/AvereSDK/blob/master/docs/README.md)için verilmiştir.  
