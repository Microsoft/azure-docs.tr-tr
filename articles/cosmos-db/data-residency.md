---
title: Azure Cosmos DB veri yerleşimi gereksinimlerini karşılayın
description: verilerinize ve yedeklemelerinize yönelik Azure Cosmos DB, tek bir bölgede kalacak şekilde veri fazlalığını nasıl karşılacağınızı öğrenin.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: c5f8a4361774368e3934d1e2b16c8311876f5caa
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491874"
---
# <a name="how-to-meet-data-residency-requirements-in-azure-cosmos-db"></a>Azure Cosmos DB veri yerleşimi gereksinimlerini karşılayın

Azure Cosmos DB, veri ve yedeklemelerinizi tek bir bölgede kalacak şekilde yapılandırarak, bu gereksinimleri karşılayabilirsiniz[ .](https://azure.microsoft.com/en-us/global-infrastructure/data-residency/)

## <a name="residency-requirements-for-data"></a>Veri gereksinimleri

Azure Cosmos DB, bölgeler arası veri çoğaltmasını açıkça yapılandırmanız gerekir. [Azure clı](scripts/cli/common/regions.md) [Azure Portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account)kullanarak Coğrafi çoğaltmayı yapılandırmayı öğrenin. Veri yerleşimi gereksinimlerini karşılamak için belirli bölgelerin istenmeyen bölgelere veri çoğaltılmasını engellemesine izin veren bir Azure ilkesi oluşturabilirsiniz.

## <a name="residency-requirements-for-backups"></a>Yedeklemeler için yeniden gereksinimleri

**Sürekli mod yedeklemeleri**: Bu yedeklemeler, yerel olarak yedekli ya da bölgesel olarak yedekli depolamada depolandıkları için varsayılan olarak yerleşik olarak bulunur. Daha fazla bilgi edinmek için [sürekli yedekleme](continuous-backup-restore-portal.md) makalesine bakın.

**Düzenli mod yedeklemeleri**: varsayılan olarak, düzenli mod hesap yedeklemeleri, coğrafi olarak yedekli depolama alanında depolanır. Düzenli yedekleme modlarında, veri yedekliliği hesap düzeyinde yapılandırabilirsiniz. Yedekleme depolaması için üç artıklık seçeneği vardır. Bunlar yerel yedeklilik, bölge artıklığı veya coğrafi yedeklilik olanlardır. Daha fazla bilgi için bkz. Portal kullanarak [yedek artıklığı yapılandırma](configure-periodic-backup-restore.md#configure-backup-interval-retention) .

## <a name="use-azure-policy-to-enforce-the-residency-requirements"></a>Azure Ilkesini kullanarak yeniden kullanım gereksinimlerini zorunlu tutun

Tüm verilerinizi tek bir Azure bölgesinde tutmanızı gerektiren veri yerleşimi gereksinimleriniz varsa, Azure Ilkesi kullanarak hesabınız için bölgesel olarak yedekli veya yerel olarak yedekli yedeklemeler uygulayabilirsiniz.  Ayrıca, Azure Cosmos DB hesapların diğer bölgelere coğrafi olarak çoğaltılmadığı bir ilkeyi zorunlu kılabilirsiniz.

Azure Ilkesi, Azure kaynaklarına kurallar uygulayan ilkeler oluşturmak, atamak ve yönetmek için kullanabileceğiniz bir hizmettir. Azure Ilkesi, bu kaynakları kurumsal standartlarınızla ve hizmet düzeyi Sözleşmelerinizle uyumlu tutmanıza yardımcı olur. Daha fazla bilgi için bkz. [Azure ilkesini](policy.md) kullanarak Azure Cosmos DB kaynaklarına yönelik idare ve denetimleri uygulama

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Portal](configure-periodic-backup-restore.md) kullanarak düzenli yedeklemeyi yapılandırma ve yönetme
* [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)veya [Azure Resource Manager](continuous-backup-restore-template.md)kullanarak sürekli yedeklemeyi yapılandırın ve yönetin.
