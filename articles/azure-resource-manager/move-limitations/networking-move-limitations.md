---
title: Azure ağ kaynaklarını yeni aboneliğe veya kaynak grubuna taşıma
description: Sanal ağları ve diğer ağ kaynaklarını yeni bir kaynak grubuna veya aboneliğe taşımak için Azure Resource Manager kullanın.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: c4e6acb52f6342c57fb1db9fc3e83d90d6d01285
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150832"
---
# <a name="move-guidance-for-networking-resources"></a>Ağ kaynakları için kılavuzu taşıma

Bu makalede, belirli senaryolar için sanal ağların ve diğer ağ kaynaklarının nasıl taşınacağı açıklanır.

## <a name="dependent-resources"></a>Bağımlı kaynaklar

Bir sanal ağ taşırken, bağımlı kaynaklarını da taşımanız gerekir. VPN ağ geçitleri için IP adresleri, sanal ağ geçitleri ve tüm ilişkili bağlantı kaynakları taşımanız gerekir. Yerel ağ geçitleri farklı kaynak grubunda olabilir.

Bir sanal makineyi bir ağ arabirimi kartıyla yeni bir aboneliğe taşımak için tüm bağımlı kaynakları taşımanız gerekir. Ağ arabirim kartının sanal ağını, sanal ağ için tüm diğer ağ arabirimi kartlarını ve VPN ağ geçitlerini taşıyın.

Daha fazla bilgi için bkz. [abonelikler arasında taşıma senaryosu](../resource-group-move-resources.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Eşlenen sanal ağ

Eşlenmiş sanal ağın taşımak için önce sanal ağ eşlemesi devre dışı bırakmanız gerekir. Devre dışı sonra sanal ağ taşıyabilirsiniz. Taşıma sonrasında, sanal ağ eşlemesi yeniden etkinleştirin.

## <a name="subnet-links"></a>Alt ağ bağlantıları

Bir alt ağ kaynak gezinti bağlantılarının bulunduğu sanal ağı içeriyorsa bir sanal ağ başka bir aboneliğe taşınamıyor. Örneğin, bir Azure önbelleği için Redis kaynak bir alt ağa dağıtılırsa, bu alt ağ kaynak Gezinti bağlantısı vardır.

## <a name="next-steps"></a>Sonraki adımlar

Kaynakları taşıma komutları için bkz. [kaynakları yeni kaynak grubuna veya aboneliğe taşıma](../resource-group-move-resources.md).
