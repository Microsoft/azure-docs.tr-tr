---
title: Azure hızlı başlangıç-Azure portal kullanarak Key Vault sertifikayı ayarlama ve alma | Microsoft Docs
description: Azure portal kullanarak Azure Key Vault bir sertifikanın nasıl ayarlanacağını ve alınacağını gösteren hızlı başlangıç
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2020
ms.author: mbaldwin
ms.openlocfilehash: b7a3126aef94ccdef7f6c9120ddeb6f20d872ab5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815783"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-the-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak Azure Key Vault bir sertifikayı ayarlama ve alma

Azure Key Vault, gizli diziler için güvenli bir depolama sağlayan bulut hizmetidir. Anahtarları, parolaları, sertifikaları ve diğer gizli dizileri güvenli bir şekilde depolayabilirsiniz. Azure anahtar kasaları Azure portalı aracılığıyla oluşturulup yönetilebilir. Bu hızlı başlangıçta bir Anahtar Kasası oluşturup bir sertifikayı depolamak için kullanacaksınız. Key Vault hakkında daha fazla bilgi için [Genel Bakış](../general/overview.md) bölümünü inceleyin.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-a-vault"></a>Kasa oluşturma

1. Azure portal menüsünde veya **giriş** sayfasından **kaynak oluştur**' u seçin.
2. Arama kutusuna **Key Vault** girin.
3. Sonuç listesinden **Key Vault**’u seçin.
4. Key Vault bölümünde **Oluştur**’u seçin.
5. **Anahtar kasası oluşturma** bölümünde aşağıdaki bilgileri sağlayın:
    - **Ad**: Benzersiz bir ad gereklidir. Bu hızlı başlangıçta **örnek kasasını** kullanırız. 
    - **Abonelik**: Bir abonelik seçin.
    - **Kaynak grubu** altında **Yeni oluştur** ' u seçin ve bir kaynak grubu adı girin.
    - **Konum** aşağı açılan menüsünde bir konum seçin.
    - Diğer seçenekleri varsayılan değerlerinde bırakın.
6. Yukarıdaki bilgileri girdikten sonra **Oluştur**’u seçin.

Aşağıda listelenen iki özelliği not edin:

* **Kasa adı**: örnekte bu **örnek kasadır**. Bu adı diğer adımlar için kullanacaksınız.
* **Kasa URI’si**: Örnekte bu: `https://example-vault.vault.azure.net/`. REST API'si aracılığıyla kasanızı kullanan uygulamaların bu URI'yi kullanması gerekir.

Bu noktada Azure hesabınız, bu yeni anahtar kasasında işlemler gerçekleştirmeye yetkili olan tek hesaptır.

![Key Vault oluşturma işlemi tamamlandıktan sonra alınan çıktı](../media/certificates/quick-create-portal/vault-properties.png)

## <a name="add-a-certificate-to-key-vault"></a>Key Vault bir sertifika ekleyin

Kasaya bir sertifika eklemek için, birkaç ek adım yapmanız yeterlidir. Bu durumda, bir uygulama tarafından kullanılabilecek otomatik olarak imzalanan bir sertifika ekleyeceğiz. Sertifikaya **örnek sertifikası** denir.

1. Key Vault Özellikler sayfalarında, **Sertifikalar**' ı seçin.
2. **Oluştur/İçeri Aktar**’a tıklayın.
3. **Sertifika oluştur** ekranında aşağıdaki değerleri seçin:
    - **Sertifika oluşturma yöntemi**: oluştur.
    - **Sertifika adı**: örnek sertifikası.
    - **Konu**: CN = exampledomain
    - Diğer değerleri varsayılan değerlerinde bırakın. (Varsayılan olarak, gelişmiş ilkede özel bir şey belirtmezseniz, istemci kimlik doğrulama sertifikası olarak kullanılabilir.)
 4. **Oluştur**’a tıklayın.

Sertifikanın başarıyla oluşturulduğunu belirten iletiyi aldıktan sonra listede üzerine tıklayabilirsiniz. Ondan sonra bazı özellikleri görebilirsiniz. Geçerli sürüme tıklarsanız önceki adımda belirtilen değeri görebilirsiniz.

![Sertifika Özellikleri](../media/certificates/quick-create-portal/current-version-hidden.png)

## <a name="export-certificate-from-key-vault"></a>Sertifikayı Key Vault dışarı aktar
"CER biçiminde Indir" veya "PFX/ped biçiminde Indir" düğmesine tıklayarak sertifikayı indirebilirsiniz. 

![Sertifika indirme](../media/certificates/quick-create-portal/current-version-shown.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer Key Vault hızlı başlangıçları ve öğreticileri bu hızlı başlangıcı temel alır. Sonraki hızlı başlangıç ve öğreticilerle çalışmaya devam etmeyi planlıyorsanız, bu kaynakları yerinde bırakmanız yararlı olabilir.
Artık gerek kalmadığında kaynak grubunu silin; bunu yaptığınızda Key Vault ve ilgili kaynaklar silinir. Kaynak grubunu portal aracılığıyla silmek için:

1. Portalın üst kısmındaki Arama kutusuna kaynak grubunuzun adını girin. Bu hızlı başlangıçta kullanılan kaynak grubunu arama sonuçlarında gördüğünüzde seçin.
2. **Kaynak grubunu sil**'i seçin.
3. **KAYNAK GRUBU ADINI YAZIN:** kutusuna kaynak grubunun adını yazın ve **Sil**’i seçin.


## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Key Vault oluşturdunuz ve içinde bir sertifika depotamamladınız. Key Vault ve uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere ilerleyin.

- [Azure Key Vault genel bakışını](../general/overview.md) okuyun
- [Azure Key Vault geliştirici kılavuzuna](../general/developers-guide.md) bakın
- [Key Vault güvenliğine genel bakış](../general/security-features.md) konusunu gözden geçirin
