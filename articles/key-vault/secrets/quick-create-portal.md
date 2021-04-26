---
title: "Azure Hızlı Başlangıç: Azure portalı kullanarak Key Vault'tan gizli dizi ayarlama ve alma | Microsoft Docs"
description: Azure portalı kullanarak Azure Key Vault'tan gizli dizi ayarlamayı ve almayı gösteren hızlı başlangıç
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 0c1a3362c4f1dfd3396ccd4704933d06d2611414
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814127"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Hızlı başlangıç: Azure portalı kullanarak Azure Key Vault'tan gizli dizi ayarlama ve alma

Azure Key Vault, gizli diziler için güvenli bir depolama sağlayan bulut hizmetidir. Anahtarları, parolaları, sertifikaları ve diğer gizli dizileri güvenli bir şekilde depolayabilirsiniz. Azure anahtar kasaları Azure portalı aracılığıyla oluşturulup yönetilebilir. Bu hızlı başlangıçta, bir anahtar kasası oluşturacak ve ardından bir gizli diziyi depolamak için kullanacaksınız. 

Hakkında daha fazla bilgi için bkz. 
- [Key Vault genel bakış](../general/overview.md)
- [Gizli dizi genel bakış](about-secrets.md).

## <a name="prerequisites"></a>Önkoşullar

Azure Key Vault erişmek için bir Azure aboneliğine ihtiyacınız olacaktır. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

Gizli dizi erişimi Azure Key Vault aracılığıyla gerçekleşir. Bu hızlı başlangıçta [Azure Portal](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md)veya [Azure PowerShell](../general/quick-create-powershell.md)kullanarak bir Anahtar Kasası oluşturun.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="add-a-secret-to-key-vault"></a>Key Vault’a gizli dizi ekleme

Kasaya bir gizli dizi eklemek için şu adımları izleyin:

1. Azure portal yeni anahtar kasanıza gidin
1. Key Vault ayarları sayfalarında **gizlilikler**' ı seçin.
1. **Oluştur/İçeri Aktar**’a tıklayın.
1. **Bir gizli dizi oluştur** ekranında aşağıdaki değerleri seçin:
    - **Karşıya yükleme seçenekleri**: El ile.
    - **Ad**: gizli dizi için bir ad yazın. Gizli dizi adı bir Key Vault içinde benzersiz olmalıdır. Ad, bir harfle başlayan ve yalnızca 0-9, a-z, A-Z ve-içeren bir 1-127 karakter dizesi olmalıdır. Adlandırma hakkında daha fazla bilgi için bkz. [Key Vault nesneleri, tanımlayıcıları ve sürüm oluşturma](../general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)
    - **Değer**: parola için bir değer yazın. Key Vault API 'Leri, gizli değerleri dizeler olarak kabul eder ve döndürür. 
    - Diğer değerleri varsayılan değerlerinde bırakın. **Oluştur**’a tıklayın.

Gizli dizinin başarıyla oluşturulduğunu belirten iletiyi aldıktan sonra listede gizli diziye tıklayabilirsiniz. 

Gizlilikler öznitelikleri hakkında daha fazla bilgi için bkz. [Azure Key Vault gizli](./about-secrets.md) dizileri

## <a name="retrieve-a-secret-from-key-vault"></a>Key Vault bir gizli dizi alma

Geçerli sürüme tıklarsanız önceki adımda belirtilen değeri görebilirsiniz.

![Gizli dizi özellikleri](../media/quick-create-portal/current-version-hidden.png)

Sağ bölmedeki "gizli değeri göster" düğmesine tıkladığınızda gizli değeri görebilirsiniz. 

![Gizli dizi değeri görüntülendi](../media/quick-create-portal/current-version-shown.png)

[Azure CLI]()veya [Azure PowerShell]() daha önce oluşturulmuş gizli dizi almak için de kullanabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer Key Vault hızlı başlangıçları ve öğreticileri bu hızlı başlangıcı temel alır. Sonraki hızlı başlangıç ve öğreticilerle çalışmaya devam etmeyi planlıyorsanız, bu kaynakları yerinde bırakmanız yararlı olabilir.
Artık gerek kalmadığında kaynak grubunu silin; bunu yaptığınızda Key Vault ve ilgili kaynaklar silinir. Kaynak grubunu portal aracılığıyla silmek için:

1. Portalın üst kısmındaki Arama kutusuna kaynak grubunuzun adını girin. Bu hızlı başlangıçta kullanılan kaynak grubunu arama sonuçlarında gördüğünüzde seçin.
2. **Kaynak grubunu sil**'i seçin.
3. **KAYNAK GRUBU ADINI YAZIN:** kutusuna kaynak grubunun adını yazın ve **Sil**’i seçin.

> [!NOTE]
> Gizli dizi, anahtar, sertifika veya Anahtar Kasası silindikten sonra, yapılandırılabilir bir 90-takvim gününe kadar süreyle kurtarılabilir olarak kalacağını fark etmek önemlidir. Yapılandırma belirtilmemişse, varsayılan kurtarma süresi 90 gün olarak ayarlanır. Bu, kullanıcılara yanlışlıkla gizli dizi silme ve yanıt vermesi için yeterli zaman sağlar. Anahtar kasalarını ve Anahtar Kasası nesnelerini silme ve kurtarma hakkında daha fazla bilgi için bkz. [Azure Key Vault Soft-Delete Overview](../general/soft-delete-overview.md)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Key Vault oluşturdunuz ve içinde gizli dizi depolıdınız. Key Vault ve uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere ilerleyin.

- [Azure Key Vault genel bakışını](../general/overview.md) okuyun
- [Key Vault güvenli erişimi](../general/security-features.md) okuma
- Bkz. [App Service Web uygulamasıyla Key Vault kullanma](../general/tutorial-net-create-vault-azure-web-app.md)
- Bkz. [VM 'ye dağıtılan uygulama ile Key Vault kullanma](../general/tutorial-net-virtual-machine.md)
- [Azure Key Vault geliştirici kılavuzuna](../general/developers-guide.md) bakın
- [Key Vault güvenliğine genel bakış](../general/security-features.md) konusunu gözden geçirin