---
title: Azure Active Directory doğrulanabilir kimlik bilgilerini (Önizleme) devre dışı
description: Doğrulanabilir kimlik bilgileri önizlemeden nasıl vazeceğinizi öğrenin
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: d6e72b6d6f566fcf3f52e1c48ab6824c0e9a968e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222801"
---
# <a name="opt-out-of-the-verifiable-credentials-preview"></a>Doğrulanabilir kimlik bilgilerini (Önizleme) devre dışı

Bu makalede:

- Ne kadar tercih etmeniz gerekebilir?
- Gerekli adımlar.
- Verilerinize ne olur?
- Var olan doğrulanabilir kimlik bilgileriyle ilgili etkisi.

> [!IMPORTANT]
> Azure Active Directory doğrulanabilir kimlik bilgileri şu anda genel önizlemede.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

- Doğrulanabilir kimlik bilgilerini ekleme tamam.

## <a name="potential-reasons-for-opting-out"></a>Kullanıma alma için olası nedenler

Bu sırada, etki alanı bilgilerinde değişiklik yapma imkanına sahip değildir. Sonuç olarak, bir hata yaparsanız veya değişiklik yapmak istediğinize karar verirseniz, yeniden kullanıma alma ve yeniden başlatma gibi başka bir seçenek yoktur.

## <a name="the-steps-required"></a>Gereken adımlar

1. Azure portal doğrulanabilir kimlik bilgileri için arama yapın.
2. Sol taraftaki menüden **Ayarlar** ' ı seçin.
3. Bölümünde, **kuruluşunuzu sıfırlayın**, **tüm kimlik bilgilerini sil ' i seçin ve Önizleme ' yi devre dışı** yapın.

   ![ayarları sıfırlama org](media/how-to-opt-out/settings-reset.png)

4. Uyarı iletisini okuyun ve devam etmek için **Sil ve** geri çevir ' i seçin.

   ![ayarları silme ve geri çevirme](media/how-to-opt-out/delete-and-opt-out.png)

Artık doğrulanabilir kimlik bilgileri önizlemesini devre dışı bırakılırsınız. Ayak kapsamında neler olduğunu anlamak için okumaya devam edin.

## <a name="what-happens-to-your-data"></a>Verilerinize ne olur?

Azure Active Directory doğrulanabilir kimlik bilgileri hizmeti 'ni tamamladıktan sonra, aşağıdaki işlemler gerçekleşmeyecektir:

- Key Vault 'deki DıD anahtarları [geçici olarak silinir](../../key-vault/general/soft-delete-overview.md).
- Veren nesnesi veritabanımızdan silinecek.
- Kiracı tanımlayıcı, veritabanımızdan silinecek. 
- Tüm sözleşmeler nesneleri veritabanımızdan silinecek.

Bir kabul etme gerçekleştikten sonra, yaptığınız değişiklikleri kurtarmanız veya yaptığınız herhangi bir işlem yapmanız mümkün olmayacaktır. Bu adım tek yönlü bir işlemdir ve bir kez daha kabul etmeniz gerekir. Bu, yeni bir oluşturma işlemi ile sonuçlanır.  

## <a name="effect-on-existing-verifiable-credentials"></a>Var olan doğrulanabilir kimlik bilgileriyle ilgili etki

Zaten verilen tüm doğrulanabilir kimlik bilgileri mevcut olmaya devam edecektir. Bu, YAPTıĞıNıZ bir işlem için çözümlenemeye devam edecek şekilde şifreli olarak geçersiz kılınmaz.
Ancak, bağlı olan taraflar durum API 'sini çağırdıklarında, her zaman bir hata iletisi alır.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure kiracınızda](get-started-verifiable-credentials.md) doğrulanabilir kimlik bilgilerini ayarlama