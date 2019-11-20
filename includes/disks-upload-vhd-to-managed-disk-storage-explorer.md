---
title: include dosyası
description: include dosyası
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5104c3b4446a8d3747ce7cc1648ef05dd117eb3d
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013788"
---
Depolama Gezgini 1.10.0, kullanıcıların yönetilen diskleri karşıya yüklemesi, indirmesi ve kopyalaması ve anlık görüntü oluşturma imkanı sağlar. Bu ek yetenekler nedeniyle, verileri Şirket içinden Azure 'a geçirmek ve Azure bölgelerine veri geçirmek için Depolama Gezgini kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlayabilmeniz için şunlar gerekir:
- Bir Azure aboneliği
- Bir veya daha fazla Azure yönetilen diski
- [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) en son sürümü

## <a name="connect-to-an-azure-subscription"></a>Bir Azure aboneliğine Bağlanma

Depolama Gezgini Azure 'a bağlı değilse, kaynakları yönetmek için kullanamazsınız. Bu bölüm, Depolama Gezgini kullanarak kaynakları yönetebilmeniz için Azure hesabınıza bağlanılmasını sağlar.

1. Azure Depolama Gezgini başlatın ve soldaki **eklenti** simgesine tıklayın.

    ![Eklenti simgesine tıklayın](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. **Azure hesabı ekle**' yi seçin ve ardından **İleri**' ye tıklayın.

    ![Azure Hesabı ekleme](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. **Azure oturum açma** iletişim kutusunda, Azure kimlik bilgilerinizi girin.

    ![Azure oturum açma iletişim kutusu](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Listeden aboneliğinizi seçip **Uygula**’ya tıklayın.

    ![Aboneliğinizi seçme](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>Şirket içi bir VHD 'den yönetilen bir disk yükleme

1. Sol bölmede, **diskler** ' i genişletin ve diskinizi yüklemek istediğiniz kaynak grubunu seçin.

    ![Kaynak grubu 1 ' i seçin](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. **Karşıya Yükle**’yi seçin.

    ![Karşıya yükleme seçme](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. **VHD 'Yi karşıya yükle** bölümünde, kaynak VHD 'nizi, diskin adını, işletim sistemi türünü, diski yüklemek istediğiniz bölgeyi ve hesap türünü belirtin. Bazı bölgelerde kullanılabilirlik alanları desteklenir, bu bölgeler için tercih ettiğiniz bir bölgeyi seçebilirsiniz.
1. Disketinizi karşıya yüklemeye başlamak için **Oluştur** ' u seçin.

    ![VHD 'yi karşıya yükle iletişim kutusu](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. Karşıya yüklemenin durumu artık **etkinliklerde**görüntülenecektir.

    ![Karşıya yükleme durumu](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. Karşıya yükleme tamamlandıysa ve sağ bölmede diski görmüyorsanız, **Yenile**' yi seçin.

## <a name="download-a-managed-disk"></a>Yönetilen bir disk indirin

Aşağıdaki adımlarda, yönetilen bir diskin şirket içi bir VHD 'ye nasıl indirileceği açıklanmaktadır. İndirilmek üzere bir diskin durumunun **eklenmemiş** olması gerekir, **ekli** bir disk indirilemez.

1. Sol bölmede, zaten genişletilmemişse **diskler** ' i genişletin ve diskinizi indirmek istediğiniz kaynak grubunu seçin.

    ![Kaynak grubu 1 ' i seçin](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Sağ bölmede, indirmek istediğiniz diski seçin.
1. **İndir** ' i seçin ve ardından diski kaydetmek istediğiniz yeri seçin.

    ![Yönetilen bir disk indirin](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. **Kaydet** ' i seçin ve diskiniz indirilbaşlayacaktır. İndirmenin durumu **etkinliklerde**görüntülenir.

    ![İndirme durumu](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>Yönetilen diski çoğaltma

Depolama Gezgini, bir sürücü içinde veya bölgeler arasında bir sürücü kopyalayabilirsiniz. Bir diski kopyalamak için:

1. Sol taraftaki **diskler** açılan menüsünde, kopyalamak istediğiniz diski içeren kaynak grubunu seçin.

    ![Kaynak grubu 1 ' i seçin](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Sağ bölmede, kopyalamak istediğiniz diski seçin ve **Kopyala**' yı seçin.

    ![Yönetilen diski çoğaltma](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. Sol bölmede, diski yapıştırmak istediğiniz kaynak grubunu seçin.

    ![Kaynak grubu seçin 2](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. Sağ bölmede **Yapıştır** ' ı seçin.

    ![Yönetilen bir disk Yapıştır](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. **Disk Yapıştır** iletişim kutusunda değerleri girin. Ayrıca, desteklenen bölgelerde bir kullanılabilirlik alanı belirtebilirsiniz.

    ![Disk Yapıştır iletişim kutusu](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. **Yapıştır** ' ı seçin ve diskiniz kopyalanmaya başlayacaktır, durum **etkinliklerde**görüntülenir.

    ![Yapıştırma durumunu Kopyala](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>Anlık görüntü oluşturma

1. Sol taraftaki **diskler** açılan menüsünde, anlık görüntü eklemek istediğiniz diski içeren kaynak grubunu seçin.

    ![Kaynak grubu 1 ' i seçin](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Sağ tarafta, anlık görüntü oluşturmak istediğiniz diski seçin ve **anlık görüntü oluştur**' u seçin.

    ![Anlık görüntü oluşturma](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. **Anlık görüntü oluştur**' da, anlık görüntünün adını ve içinde oluşturmak istediğiniz kaynak grubunu belirtin. Ardından **Oluştur**’u seçin.

    ![Anlık görüntü oluştur iletişim kutusu](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. Anlık görüntü oluşturulduktan sonra, Azure portal anlık görüntüyü görüntülemek için **etkinliklerde** **portalda aç** ' ı seçebilirsiniz.

    ![Portalda anlık görüntüyü aç](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>Sonraki adımlar
