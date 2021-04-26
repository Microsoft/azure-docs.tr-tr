---
title: Anahtar kasasını farklı bir bölgeye taşıma-Azure Key Vault | Microsoft Docs
description: Bu makalede, anahtar kasasının farklı bir bölgeye taşınması hakkında rehberlik sunulmaktadır.
services: key-vault
author: msmbaldwin
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: ac2f6347776c2f5d230065b80b1c0336e21e181c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751805"
---
# <a name="move-an-azure-key-vault-across-regions"></a>Bir Azure anahtar kasasını bölgeler arasında taşıma

Azure Key Vault, anahtar kasasını bir bölgeden diğerine taşımaya izin veren bir kaynak taşıma işlemini desteklemez. Bu makalede bir iş ihtiyacı olan kuruluşların bir anahtar kasasını başka bir bölgeye taşıması için geçici çözümler ele alınmaktadır. Her geçici çözüm seçeneğinde sınırlamalar vardır. Bir üretim ortamında uygulamayı denemeden önce bu geçici çözümlerin etkilerini anlamak önemlidir.

Bir anahtar kasasını başka bir bölgeye taşımak için, başka bir bölgede bir Anahtar Kasası oluşturun ve ardından her bir parolayı mevcut anahtar kasasından yeni anahtar kasasına el ile kopyalayın. Bunu, aşağıdaki iki seçenekten birini kullanarak yapabilirsiniz.

## <a name="design-considerations"></a>Tasarım konusunda dikkat edilmesi gerekenler

Başlamadan önce aşağıdaki kavramları göz önünde bulundurun:

* Anahtar Kasası adları genel olarak benzersizdir. Kasa adını yeniden kullanamazsınız.
* Yeni anahtar kasasındaki erişim ilkelerinizi ve ağ yapılandırma ayarlarınızı yeniden yapılandırmanız gerekir.
* Yeni anahtar kasasında geçici silme ve Temizleme korumasını yeniden yapılandırmanız gerekir.
* Yedekleme ve geri yükleme işlemi, otomatik döndürme ayarlarınızı korumaz. Ayarları yeniden yapılandırmanız gerekebilir.

## <a name="option-1-use-the-key-vault-backup-and-restore-commands"></a>Seçenek 1: Anahtar Kasası yedekleme ve geri yükleme komutlarını kullanma

Backup komutunu kullanarak kasaızdaki her bir gizli dizi, anahtar ve sertifikayı yedekleyebilirsiniz. Gizli dizileri şifreli bir blob olarak indirilir. Daha sonra blobu yeni anahtar kasanıza geri yükleyebilirsiniz. Komutların listesi için bkz. [Azure Key Vault komutları](/powershell/module/azurerm.keyvault#key_vault).

Backup ve restore komutlarının kullanılması iki sınırlamalara sahiptir:

* Bir coğrafya içindeki anahtar kasasını yedekleyebilir ve başka bir Coğrafya 'ya geri yükleyebilirsiniz. Daha fazla bilgi için bkz. [Azure coğrafi lıkları](https://azure.microsoft.com/global-infrastructure/geographies/).

* Yedekleme komutu her gizli dizinin tüm sürümlerini yedekler. Çok sayıda eski sürümleri olan bir gizli diziniz varsa (10’dan fazla), istek boyutu izin verilen en büyük boyutu aşabilir ve işlem başarısız olabilir.

## <a name="option-2-manually-download-and-upload-the-key-vault-secrets"></a>2. seçenek: Anahtar Kasası gizli dizilerini El Ile indirme ve karşıya yükleme

Belirli gizli türleri el ile indirebilirsiniz. Örneğin, sertifikaları PFX dosyası olarak indirebilirsiniz. Bu seçenek, sertifikalar gibi bazı gizli türlerin coğrafi kısıtlamalarını ortadan kaldırır. PFX dosyalarını herhangi bir bölgedeki herhangi bir anahtar kasasında karşıya yükleyebilirsiniz. Gizli dizileri, parola korumalı olmayan bir biçimde indirilir. Taşıma sırasında gizli dizilerinizi güvenli hale getirmekten siz sorumlusunuz.