---
title: içerme dosyası
description: içerme dosyası
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6ad5bafe66e6e39503d3987aa6ddd9159c532fa0
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72237475"
---
Azure depolama için depolama hesabı kullanımınıza göre faturalandırılırsınız. Bir depolama hesabındaki tüm nesneler, bir grup olarak birlikte faturalandırılır. 

Depolama maliyetleri şu etkenlere göre hesaplanır: bölge/konum, hesap türü, erişim katmanı, depolama kapasitesi, çoğaltma düzeni, depolama işlemleri ve veri çıkışı.

* **Bölge** , hesabınızın dayandığı coğrafi bölgeyi ifade eder.
* **Hesap türü** , kullanmakta olduğunuz depolama hesabı türünü belirtir. 
* **Erişim katmanı** , genel amaçlı v2 veya blob depolama hesabınız için belirttiğiniz veri kullanımı modelini ifade eder.
* Depolama **kapasitesi** , veri depolamak için kullandığınız depolama hesabı servis biriminden ne kadarının olduğunu ifade eder.
* **Çoğaltma** , verilerinizin kaç kopyasının aynı anda ve hangi konumlarda tutulmasını belirler.
* **İşlemler** , Azure depolama 'ya yönelik tüm okuma ve yazma işlemlerine başvurur.
* **Veri** çıkışı, bir Azure bölgesinden aktarılan tüm verileri ifade eder. Depolama hesabınızdaki verilere aynı bölgede çalıştırmayan bir uygulama tarafından erişildiğinde, veri çıkışı için ücretlendirilirsiniz. Kaynak gruplarını, çıkış ücretlerini sınırlandırmak için aynı bölgedeki veri ve hizmetlerinizi gruplandırmak üzere kullanma hakkında bilgi için bkz. [Azure Kaynak grubu nedir?](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group). 

[Azure depolama fiyatlandırma](https://azure.microsoft.com/pricing/details/storage/) sayfası, hesap türü, depolama kapasitesi, çoğaltma ve işlemlere göre ayrıntılı fiyatlandırma bilgileri sağlar. [Veri aktarımları fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/data-transfers/) , veri çıkışı için ayrıntılı fiyatlandırma bilgileri sağlar. Maliyetlerinizi tahmin etmeye yardımcı olması için [Azure depolama Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) ' yı kullanabilirsiniz.

