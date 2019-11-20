---
title: Büyük dosya paylaşımlarını etkinleştirme ve oluşturma-Azure dosyaları
description: Bu makalede, büyük dosya paylaşımlarını etkinleştirme ve oluşturma hakkında bilgi edineceksiniz.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 00db5905c008644bc21704179363849756fa7dcc
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518427"
---
# <a name="how-to-enable-and-create-large-file-shares"></a>Büyük dosya paylaşımlarını etkinleştirme ve oluşturma

İlk olarak standart dosya paylaşımları yalnızca 5 TiB 'a kadar ölçeklendirilebilir, artık büyük dosya paylaşımları sayesinde 100 TiB 'ye kadar ölçeklenebilirler. Premium dosya paylaşımları, varsayılan olarak 100 TiB 'ye kadar ölçeklenir. 

Standart dosya paylaşımlarını kullanarak 100 TiB 'ye kadar ölçeklendirebilmek için depolama hesabınızı büyük dosya paylaşımlarını kullanacak şekilde etkinleştirmeniz gerekir. Büyük dosya paylaşımlarını kullanmak için mevcut bir hesabı etkinleştirebilir veya yeni bir hesap oluşturabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="restrictions"></a>Kısıtlamalar

Büyük dosya paylaşımları etkin hesaplar LRS veya ZRS 'yi destekler. Şimdilik, büyük dosya paylaşımları etkinleştirilmiş hesaplar GZRS, GRS veya RA-GRS 'yi desteklemez. Hesapta büyük dosya paylaşımlarının etkinleştirilmesi geri alınamaz bir işlemdir, etkin olduktan sonra hesabınız GZRS, GRS veya RA-GRS ' e dönüştürülemez.

## <a name="create-a-new-storage-account"></a>Yeni depolama hesabı oluşturma

[Azure Portal](https://portal.azure.com)’ında oturum açın.

1. Azure portalda **Tüm hizmetler**’i seçin. Kaynak listesinde **Depolama Hesapları** yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Depolama Hesapları**’nı seçin.
1. Açılan **Depolama Hesapları** penceresinde **Ekle**'yi seçin.
1. Depolama hesabının oluşturulacağı aboneliği seçin.
1. **Kaynak grubu** alanı altında **Yeni oluştur**’u seçin. Aşağıdaki görüntüde gösterildiği gibi yeni kaynak grubunuz için bir ad girin.

    ![Portalda kaynak grubu oluşturmayı gösteren ekran görüntüsü](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Ardından, depolama hesabınız için bir ad girin. Seçtiğiniz ad Azure genelinde benzersiz olmalıdır. Ad ayrıca 3 - 24 karakter uzunluğunda olmalıdır ve yalnızca rakam ve küçük harf içerebilir.
1. Depolama hesabınız için bir konum seçin, [LFS için desteklenen bölgelerin biri](storage-files-planning.md#regional-availability)olduğundan emin olun.
1. Çoğaltmayı **yerel olarak yedekli depolama** ya da bölgesel olarak **yedekli depolama**olarak ayarlayın.
1. Bu alanları varsayılan değerlerine ayarlanmış olarak bırakın:

   |Alan  |Değer  |
   |---------|---------|
   |Dağıtım modeli     |Kaynak Yöneticisi         |
   |Performans     |Standart         |
   |Hesap türü     |StorageV2 (genel amaçlı v2)         |
   |Erişim katmanı     |Sık Erişimli         |

1. **Gelişmiş** ' i seçin ve **büyük dosya paylaşımları**için **etkin** ' i seçin.
1. Depolama hesabı ayarlarınızı gözden geçirmek ve hesabı oluşturmak için **Gözden Geçir + Oluştur**’u seçin.

    ![Large-File-Shares-Advanced-Enable. png](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. **Oluştur**'u seçin.

## <a name="enable-on-existing-account"></a>Mevcut hesapta etkinleştir

Ayrıca, var olan hesaplarda büyük dosya paylaşımlarını etkinleştirebilirsiniz. Bunu yaparsanız, hesap artık GZRS, GRS veya RA-GRS ' e dönüştürülemez. Bu seçenek bu hesapta geri alınamaz.

1. [Azure Portal](https://portal.azure.com) açın ve büyük dosya paylaşımlarını etkinleştirmek istediğiniz depolama hesabına gidin.
1. Depolama hesabını açın ve **yapılandırma**' yı seçin.
1. **Büyük dosya paylaşımlarında** **etkin** ' i seçin ve ardından Kaydet ' i seçin.
1. **Genel bakış** ' ı ve **Yenile**' yi seçin.

![Enable-large-File-Shares-on-Existing. png](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Artık depolama hesabınızda büyük dosya paylaşımlarını etkinleştirdiniz.

Şu hatayı alırsanız: "hesabın büyük dosya paylaşımları henüz kullanılamıyor." Bölgeniz muhtemelen kullanıma alma işleminin ortasında büyük bir süre olduğu için veya acil gereksinimleriniz varsa destek 'e ulaşmak için bir süre beklemeniz gerekebilir.

## <a name="create-a-large-file-share"></a>Büyük bir dosya paylaşma oluşturma

Büyük bir dosya paylaşımının oluşturulması, standart bir dosya paylaşımının oluşturulmasıyla neredeyse aynıdır. Temel fark, 100 TiB 'ye kadar bir kota ayarlayabilmeniz gerektiğidir.

1. Depolama hesabınızdan **dosya paylaşımları**' nı seçin.
1. **+ Dosya paylaşma**' yı seçin.
1. Dosya paylaşımınız için bir ad girin ve (isteğe bağlı olarak) istediğiniz kota boyutunu 100 TiB 'ye kadar, ardından **Oluştur**' u seçin. 

![Large-File-Shares-Create-Share. png](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

## <a name="expand-existing-file-shares"></a>Mevcut dosya paylaşımlarını Genişlet

Depolama hesabınızda büyük dosya paylaşımlarını etkinleştirdikten sonra, mevcut paylaşımları daha yüksek kotaya genişletebilirsiniz.

1. Depolama hesabınızdan **dosya paylaşımları**' nı seçin.
1. Dosya paylaşımınıza sağ tıklayıp **Kota**' i seçin.
1. İstediğiniz yeni boyutu girip **Tamam**' ı seçin.

![Update-Large-File-Share-Quota. png](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Dosya Paylaşımını Bağlama - Windows](storage-how-to-use-files-windows.md)
* [Dosya Paylaşımını Bağlama - Linux](../storage-how-to-use-files-linux.md)
* [Dosya Paylaşımını Bağlama - macOS](storage-how-to-use-files-mac.md)