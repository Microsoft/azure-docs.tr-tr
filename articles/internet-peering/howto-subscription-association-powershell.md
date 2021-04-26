---
title: PowerShell kullanarak eşdüzey ASN 'yi Azure aboneliğiyle ilişkilendir
titleSuffix: Azure
description: PowerShell kullanarak eşdüzey ASN 'yi Azure aboneliğiyle ilişkilendir
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3b463293899dc2586404d68145943caff3105e89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590197"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>PowerShell kullanarak eşdüzey ASN 'yi Azure aboneliğiyle ilişkilendir

Bir eşleme isteği göndermeden önce, aşağıdaki adımları kullanarak ASN 'nizi Azure aboneliğiyle ilişkilendirmeniz gerekir.

İsterseniz, [portalı](howto-subscription-association-portal.md)kullanarak bu kılavuzu tamamlayabilirsiniz.

### <a name="working-with-azure-powershell"></a>Azure PowerShell çalışma
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>ASN 'nizi Azure aboneliğiyle ilişkilendirmek için PeerASN oluşturun

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure hesabınızda oturum açın ve aboneliğinizi seçin
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>Eşleme kaynak sağlayıcısı için kaydolun
Aşağıdaki komutu kullanarak aboneliğinizde eşleme kaynak sağlayıcısına kaydolun. Bunu yürütütemezsiniz, eşlemeyi ayarlamak için gereken Azure kaynaklarına erişilemez.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

Aşağıdaki komutları kullanarak kayıt durumunu kontrol edebilirsiniz:
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> Devam etmeden önce *Registrationstate* 'In "kayıtlı" olmasını bekleyin. Komutu yürütmeden bu işlem 5 ila 30 dakika sürebilir.

### <a name="update-the-peer-information-associated-with-this-subscription"></a>Bu abonelikle ilişkili eş bilgilerini güncelleştir

Eş bilgilerini güncelleştirme örneği aşağıda verilmiştir.

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -Ad kaynak adına karşılık gelir ve seçtiğiniz herhangi bir şey olabilir. Ancak-peerName, şirketinizin adına karşılık gelir ve PeeringDB profiliniz için mümkün olduğunca yakın olması gerekir. -PeerName değerinin yalnızca a-z, A-Z ve boşluk karakterlerini desteklediğini unutmayın.

Bir abonelikte birden fazla ASNs olabilir. Her ASN için eşleme bilgilerini güncelleştirin. "Ad" nın her ASN için benzersiz olduğundan emin olun.

Eşlerin [Peeringdb](https://www.peeringdb.com)'de tam ve güncel bir profili olması beklenir. Bu bilgileri kayıt sırasında, NOC bilgileri, teknik iletişim bilgileri ve eşleme tesislerinde bulunan bu kişilerin varlığı gibi eşdüzey ayrıntılarını doğrulamak için kullanırız.

Yukarıdaki çıktıda **{SubscriptionID}** yerine gerçek abonelik kimliği görüntülenir.

## <a name="view-status-of-a-peerasn"></a>PeerASN durumunu görüntüleme

Aşağıdaki komutu kullanarak ASN doğrulama durumunu kontrol edin:

```powershell
Get-AzPeerAsn
```

Aşağıda örnek bir yanıt verilmiştir:
```powershell
PeerContactInfo : Microsoft.Azure.PowerShell.Cmdlets.Peering.Models.PSContactInfo
PeerName        : Contoso
ValidationState : Approved
PeerAsnProperty : 1234
Name            : Contoso_1234
Id              : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/Contoso_1234
Type            : Microsoft.Peering/peerAsns
```

> [!IMPORTANT]
> Bir eşleme isteği göndermeden önce ValidationState durumunun "Onaylandı" olmasını bekleyin. Bu onay için 12 saate kadar zaman alabilir.

## <a name="modify-peerasn"></a>PeerAsn 'yi Değiştir
NOC iletişim bilgilerini dilediğiniz zaman değiştirebilirsiniz.

Aşağıda bir örnek verilmiştir:

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>PeerAsn 'yi Sil
PeerASN silme işlemi şu anda desteklenmiyor. PeerASN 'yi silmeniz gerekiyorsa, [Microsoft eşleme](mailto:peering@microsoft.com)ile iletişim kurun.

## <a name="next-steps"></a>Sonraki adımlar

* [Doğrudan eşleme oluşturma veya değiştirme](howto-direct-powershell.md)
* [Eski bir Doğrudan eşlemeyi Azure kaynağına dönüştürme](howto-legacy-direct-powershell.md)
* [Exchange eşlemesi oluşturma veya değiştirme](howto-exchange-powershell.md)
* [Eski bir Exchange eşlemeyi Azure kaynağına dönüştürme](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Ek kaynaklar

Daha fazla bilgi için [Internet eşlemesi SSS](faqs.md) ' yi ziyaret edin
