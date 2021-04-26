---
title: Azure Içeri/dışarı aktarma hizmeti gereksinimleri | Microsoft Docs
description: Azure Içeri/dışarı aktarma hizmeti için yazılım ve donanım gereksinimlerini anlayın.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 0bfc09a372584a25c23060cef33d1f698e6d5ff3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101712615"
---
# <a name="azure-importexport-system-requirements"></a>Azure İçeri/Dışarı Aktarma için sistem gereksinimleri

Bu makalede, Azure Içeri/dışarı aktarma hizmetiniz için önemli gereksinimler açıklanmaktadır. Içeri/dışarı aktarma hizmeti 'ni kullanmadan önce bilgileri dikkatlice incelemenizi ve ardından işlem sırasında gerektiği şekilde geri başvurmalarını öneririz.

## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

Waımportexport aracını kullanarak sabit sürücüleri hazırlamak için **BitLocker Sürücü Şifrelemesi destekleyen aşağıdaki 64 bit işletim sistemi** desteklenir.


|Platform |Sürüm |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Windows istemcisi için gerekli diğer yazılımlar

|Platform |Sürüm |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Desteklenen depolama hesapları

Azure Içeri/dışarı aktarma hizmeti aşağıdaki depolama hesabı türlerini destekler:

- Standart Genel Amaçlı v2 depolama hesapları (çoğu senaryo için önerilir)
- Blob Depolama Hesapları
- Genel Amaçlı v1 depolama hesapları (klasik veya Azure Resource Manager dağıtımları)

> [!IMPORTANT]
> Azure Blob depolamada ağ dosya sistemi (NFS) 3,0 protokol desteği Azure Içeri/dışarı aktarma ile desteklenmiyor.

Depolama hesapları hakkında daha fazla bilgi için bkz. [Azure Storage hesaplarına genel bakış](../storage/common/storage-account-overview.md).

Her iş, yalnızca bir depolama hesabından veri aktarmak için kullanılabilir. Diğer bir deyişle, tek bir içeri/dışarı aktarma işi birden çok depolama hesabı arasında yayılamaz. Yeni bir depolama hesabı oluşturma hakkında bilgi için bkz. [depolama hesabı oluşturma](../storage/common/storage-account-create.md).

> [!IMPORTANT]
> [Sanal ağ hizmeti uç noktaları](../virtual-network/virtual-network-service-endpoints-overview.md) özelliğinin etkinleştirildiği depolama hesapları Için, [Içeri/dışarı aktarma](../storage/common/storage-network-security.md) hizmetinin Azure 'dan/dışarı/dışarı aktarma işlemini gerçekleştirmesini sağlamak Için **güvenilir Microsoft hizmetlerine izin ver...** ayarını kullanın.

## <a name="supported-storage-types"></a>Desteklenen depolama türleri

Aşağıdaki Depolama türleri listesi Azure Içeri/dışarı aktarma hizmeti ile desteklenir.


|İş  |Depolama hizmeti |Desteklenir  |Desteklenmez  |
|---------|---------|---------|---------|
|İçeri Aktar     |  Azure Blob depolama <br><br> Azure Dosya Depolama       | Blok Blobları ve sayfa Blobları desteklenir <br><br> Desteklenen dosyalar          |
|Dışarı Aktarma     |   Azure Blob depolama       | Blok Blobları, sayfa Blobları ve ekleme Blobları desteklenir         | Azure dosyaları desteklenmiyor


## <a name="supported-hardware"></a>Desteklenen donanım

Azure Içeri/dışarı aktarma hizmeti için, verileri kopyalamak üzere desteklenen disklere ihtiyacınız vardır.

### <a name="supported-disks"></a>Desteklenen diskler

Aşağıdaki disk listesi Içeri/dışarı aktarma hizmeti ile kullanılmak üzere desteklenir.


|Disk türü  |Boyut  |Desteklenir |
|---------|---------|---------|
|SSD    |   2,5 "      |SATA ııı          |
|HDD     |  2,5 "<br>3,5 "       |SATA ıı, SATA ııı         |

Aşağıdaki disk türleri desteklenmez:

- USBs.
- Yerleşik USB bağdaştırıcısı ile harici HDD.
- Harici bir HDD 'nin kasasını içinde olan diskler.

Tek bir içeri/dışarı aktarma işi şunları içerebilir:

- En fazla 10 HDD/SSD.
- Herhangi bir boyuttaki HDD/SSD karışımı.

Çok sayıda sürücü birden fazla iş arasında yayılabilir ve oluşturulabilen iş sayısı üzerinde hiçbir sınır yoktur. İçeri aktarma işleri için yalnızca sürücüdeki ilk veri hacmi işlenir. Veri hacmi NTFS ile biçimlendirilmelidir.

Waımportexport aracını kullanarak sabit sürücüleri hazırlarken ve verileri kopyalarken, harici USB bağdaştırıcıları kullanabilirsiniz. Raf dışı USB 3,0 veya üzeri bağdaştırıcılar çalışmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

* [AzCopy komut satırı yardımcı programı ile veri aktarımı](../storage/common/storage-use-azcopy-v10.md)