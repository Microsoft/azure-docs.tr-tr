---
title: Azure NFS dosya paylaşma-Azure dosyaları bağlama
description: Ağ dosya sistemi paylaşımının nasıl bağlanacağını öğrenin.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 4369619cd83dffe36cf156f523a951e1360438db
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717086"
---
# <a name="how-to-mount-an-nfs-file-share"></a>NFS dosya paylaşımından bağlama

[Azure Dosyaları](storage-files-introduction.md), Windows'un kolay kullanılan bulut dosya sistemidir. Azure dosya paylaşımları, sunucu Ileti bloğu Protokolü (SMB) veya ağ dosya sistemi (NFS) protokolü kullanılarak Linux dağıtımları ile bağlanabilir. Bu makale, NFS ile bağlama ile, SMB ile bağlama hakkında daha fazla bilgi için bkz. [Linux Ile Azure dosyaları kullanma](storage-how-to-use-files-linux.md). Kullanılabilir protokollerin her biri hakkında ayrıntılı bilgi için bkz. [Azure dosya paylaşma protokolleri](storage-files-compare-protocols.md).

## <a name="limitations"></a>Sınırlamalar

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Bölgesel kullanılabilirlik

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Önkoşullar

- [BIR NFS paylaşma oluşturun](storage-files-how-to-create-nfs-shares.md).

    > [!IMPORTANT]
    > NFS paylaşımlarına yalnızca güvenilen ağlardan erişilebilir. NFS paylaşımınızla kurulan bağlantılar aşağıdaki kaynaklardan birinden kaynaklanmalıdır:

- Aşağıdaki ağ çözümlerinden birini kullanın:
    - [Özel uç nokta oluşturun](storage-files-networking-endpoints.md#create-a-private-endpoint) (önerilir) ya da [genel uç noktanıza erişimi kısıtlayın](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Azure dosyaları ile kullanmak üzere Linux üzerinde Noktadan siteye (P2S) VPN yapılandırın](storage-files-configure-p2s-vpn-linux.md).
    - [Azure dosyaları ile kullanmak Için siteden sıteye VPN yapılandırın](storage-files-configure-s2s-vpn.md).
    - [ExpressRoute](../../expressroute/expressroute-introduction.md)'ı yapılandırın.

## <a name="disable-secure-transfer"></a>Güvenli aktarımı devre dışı bırak

1. Azure portal oturum açın ve oluşturduğunuz NFS paylaşımının bulunduğu depolama hesabına erişin.
1. **Yapılandırma**'yı seçin.
1. **Güvenli aktarım** Için **devre dışı** seçeneğini belirleyin.
1. **Kaydet**’i seçin.

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Güvenli aktarım devre dışı olan depolama hesabı yapılandırma ekranının ekran görüntüsü.":::

## <a name="mount-an-nfs-share"></a>NFS paylaşma bağlama

1. Dosya paylaşma oluşturulduktan sonra, paylaşma ' yı seçin ve **Linux 'Tan Bağlan**' ı seçin.
1. Kullanmak istediğiniz bağlama yolunu girip betiği kopyalayın.
1. İstemcinizin bağlantısını yapın ve belirtilen bağlama betiğini kullanın.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="Dosya paylaşma bağlantı dikey penceresinin ekran görüntüsü.":::

Şimdi NFS paylaşımınızı taktıysanız.

### <a name="validate-connectivity"></a>Bağlantıyı doğrula

Bağlamanız başarısız olursa, Özel uç noktanız doğru şekilde ayarlanmamış veya erişilemez durumda olabilir. Bağlantıyı onaylama hakkında ayrıntılar için, ağ uç noktaları makalesinin [bağlantıyı doğrula](storage-files-networking-endpoints.md#verify-connectivity) bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

- Makalemize sahip Azure dosyaları hakkında daha fazla bilgi edinin ve [Azure dosyaları dağıtımı Için planlama](storage-files-planning.md)yapın.
- Herhangi bir sorunla karşılaşırsanız bkz. [Azure NFS dosya paylaşımları sorunlarını giderme](storage-troubleshooting-files-nfs.md).