---
title: Azure HPC önbellek depolama hedeflerini güncelleştirme
description: Azure HPC önbellek depolama hedeflerini düzenleme
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rohogue
ms.openlocfilehash: 115e75c0149a35104d9c3696710bf8231a98743d
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168525"
---
# <a name="edit-storage-targets"></a>Depolama hedeflerini düzenleme

Bazı özelliklerini değiştirmek için depolama hedeflerini düzenleyebilirsiniz. Farklı depolama türleri için farklı özellikler düzenlenebilir:

* BLOB depolama hedefleri için ad alanı yolunu değiştirebilirsiniz.

* NFS depolama hedefleri için şu özellikleri değiştirebilirsiniz:

  * Ad alanı yolu
  * Kullanım modeli
  * Dışarı Aktarma
  * Alt dizini dışarı aktar

Depolama hedefinin adını, türünü veya arka uç depolama sistemini (blob kapsayıcısı veya NFS ana bilgisayar adı/IP adresi) düzenleyemezsiniz. Bu özellikleri değiştirmeniz gerekiyorsa, depolama hedefini silin ve yeni değerle bir değiştirme oluşturun.

Bir depolama hedefini değiştirmek için depolama hedefi adına tıklayarak Ayrıntılar sayfasını açın. Sayfadaki bazı alanlar düzenlenebilir.

![NFS depolama hedefi için düzenleme sayfasının ekran görüntüsü](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>NFS depolama hedefini güncelleştirme

NFS depolama hedefi için, birkaç özelliği güncelleştirebilirsiniz. (Örnek düzenleme sayfası için yukarıdaki ekran görüntüsüne bakın.)

* **Kullanım modeli** -kullanım modeli, önbelleğin verileri nasıl koruduğunu etkiler. Daha fazla bilgi edinmek için [bir kullanım modeli seçin](hpc-cache-add-storage.md#choose-a-usage-model) .
* **Sanal ad alanı yolu** -istemcilerin bu depolama hedefini bağlamak için kullandığı yol. Ayrıntılar için [toplanan ad alanı planını](hpc-cache-namespace.md) okuyun.
* **NFS dışa aktarma yolu** -bu ad alanı yolu için kullanılacak depolama sistemi dışa aktarma.
* **Alt dizin yolu** -bu ad alanı yoluyla ilişkilendirilecek alt dizin (dışarı aktarma altında). Bir alt dizin belirtmeniz gerekmiyorsa bu alanı boş bırakın.

Her ad alanı yolu, bir dışarı aktarma ve alt dizinin benzersiz bir birleşimini gerektirir. Diğer bir deyişle, arka uç depolama sisteminde tam olarak aynı dizine sahip iki farklı istemciye yönelik yol yapamazsınız.

Değişiklik yaptıktan sonra, depolama hedefini güncelleştirmek için **Tamam** ' ı tıklatın veya değişiklikleri atmak için **iptal** ' i tıklatın.

## <a name="update-an-azure-blob-storage-target"></a>Azure Blob depolama hedefini güncelleştirme

BLOB depolama hedefinin Ayrıntılar sayfası, sanal ad alanı yolunu değiştirmenize olanak sağlar.

![BLOB depolama hedefi için düzenleme sayfasının ekran görüntüsü](media/hpc-cache-edit-storage-blob.png)

İşiniz bittiğinde, depolama hedefini güncelleştirmek için **Tamam** ' ı tıklatın veya değişiklikleri atmak için **iptal** ' i tıklatın.

## <a name="next-steps"></a>Sonraki adımlar

* Bu seçenekler hakkında daha fazla bilgi edinmek için [depolama hedefleri ekleme](hpc-cache-add-storage.md) bölümünü okuyun.
* Sanal yolları kullanma hakkında daha fazla ipucu için [toplanan ad alanı planını](hpc-cache-namespace.md) okuyun.
