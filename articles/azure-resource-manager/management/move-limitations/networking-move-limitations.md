---
title: Azure ağ kaynaklarını yeni aboneliğe veya kaynak grubuna taşıma
description: Sanal ağları ve diğer ağ kaynaklarını yeni bir kaynak grubuna veya aboneliğe taşımak için Azure Resource Manager kullanın.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: b7aaf01b696b13136a0f4077f315b137c8917906
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120138"
---
# <a name="move-guidance-for-networking-resources"></a>Ağ kaynakları için kılavuzu taşıma

Bu makalede, belirli senaryolar için sanal ağların ve diğer ağ kaynaklarının nasıl taşınacağı açıklanır.

## <a name="dependent-resources"></a>Bağımlı kaynaklar

> [!NOTE]
> Lütfen genel IP adresleriyle ilişkili VPN ağ geçitlerinin kaynak grupları veya abonelikler arasında şu anda taşınamayacak olduğunu unutmayın.

Bir kaynağı taşırken, bağımlı kaynaklarını da (örn. genel IP adresleri, sanal ağ geçitleri, tüm ilişkili bağlantı kaynakları) taşımanız gerekir. Yerel ağ geçitleri farklı bir kaynak grubunda olabilir.

Bir sanal makineyi bir ağ arabirimi kartıyla yeni bir aboneliğe taşımak için tüm bağımlı kaynakları taşımanız gerekir. Ağ arabirim kartının sanal ağını, sanal ağ için tüm diğer ağ arabirimi kartlarını ve VPN ağ geçitlerini taşıyın.

Daha fazla bilgi için bkz. [abonelikler arasında taşıma senaryosu](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Eşlenen sanal ağ

Eşlenen bir sanal ağı taşımak için öncelikle sanal ağ eşlemesini devre dışı bırakmanız gerekir. Devre dışı bırakıldıktan sonra sanal ağı taşıyabilirsiniz. Taşıdıktan sonra sanal ağ eşlemesini yeniden etkinleştirin.

## <a name="subnet-links"></a>Alt ağ bağlantıları

Sanal ağ, kaynak gezintisi bağlantıları içeren bir alt ağ içeriyorsa, sanal ağı farklı bir aboneliğe taşıyamazsınız. Örneğin, Redsıs kaynağı için bir Azure önbelleği bir alt ağa dağıtılmışsa, bu alt ağda bir kaynak gezinti bağlantısı bulunur.

## <a name="next-steps"></a>Sonraki adımlar

Kaynakları taşıma komutları için bkz. [kaynakları yeni kaynak grubuna veya aboneliğe taşıma](../move-resource-group-and-subscription.md).
