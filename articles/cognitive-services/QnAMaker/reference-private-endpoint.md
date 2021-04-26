---
title: Özel uç nokta ayarlama-Soru-Cevap Oluşturma
description: Soru-Cevap Oluşturma yönetilen özel uç nokta oluşturmayı anlayın.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/12/2021
ms.openlocfilehash: 7907c81e45680de49f6653891fb4204a59db1002
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101710558"
---
# <a name="private-endpoints"></a>Özel Uç Noktalar

Azure Özel Uç Noktası sizi Azure Özel Bağlantı ile desteklenen bir hizmete özel olarak ve güvenle bağlayan bir ağ arabirimidir. Artık Soru-Cevap Oluşturma, Azure Search hizmetinde özel uç noktalar oluşturmak için destek sağlar. Bu işlev yönetilen Soru-Cevap Oluşturma kullanılabilir. 

Özel uç noktalar [Azure özel bağlantısı](../../private-link/private-link-overview.md)tarafından ayrı bir hizmet olarak sağlanır. Maliyetler hakkında daha fazla bilgi için bkz [. fiyatlandırma sayfası.](https://azure.microsoft.com/pricing/details/private-link/) 

## <a name="prerequisites"></a>Ön koşullar
> [!div class="checklist"]
> * Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/cognitive-services/) oluşturun.
> * Azure portal oluşturulan Soru-Cevap Oluşturma [yönetilen kaynak](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) . Kaynağı oluştururken seçtiğiniz Azure Active Directory KIMLIĞI, aboneliğiniz, QnA kaynak adınızı unutmayın.

## <a name="steps-to-enable-private-endpoint"></a>Özel uç noktayı etkinleştirme adımları
1. Azure Search hizmeti örneğindeki Soru-Cevap Oluşturma yönetilen hizmete bir *katılımcısı* rolü atayın. Bu işlem, aboneliğe *sahip* erişimi gerektirir. Kimliği almak için hizmet kaynağında kimlik sekmesine gidin.
![Yönetilen hizmet kimliği](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-identity.png)

2. Azure Search hizmeti ıAM sekmesine giderek yukarıdaki kimliği *katılımcısı* olarak ekleyin. ![ Yönetilen hizmet ıAM](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. *Rol atamaları Ekle*' ye tıklayın, kimliği ekleyin ve *Kaydet*' e tıklayın.
![Yönetilen rol ataması](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. Şimdi, Azure Search hizmet örneğindeki *ağ* sekmesine gidin ve uç nokta bağlantısı verilerini *genel* ' ten *özel*' e geçirin. Bu işlem uzun süre çalışan bir işlemdir ve tamamlanması 30 dakika kadar sürebilir. 
![Yönetilen Azure Search ağı](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. Soru-Cevap Oluşturma yönetilen hizmetin *ağ* sekmesine gidin ve *erişime izin ver*' ın altında, *Seçili ağları ve özel uç noktaları* seçeneğini belirleyin ve *Kaydet*' e tıklayın. 
![Yönetilen soru-cevap Oluşturucu](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-2.png)

Bu, QnA Oluşturucu hizmeti ile Azure bilişsel arama hizmeti örneği arasında özel bir uç nokta bağlantısı kurar. Azure Search hizmet örneğinin *ağ* sekmesinde Özel uç nokta bağlantısını doğrulayabilirsiniz. Tüm işlem tamamlandıktan sonra Soru-Cevap Oluşturma hizmetinizi kullanmanız iyi olur. 
![Yönetilen ağ hizmeti](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)


## <a name="support-details"></a>Destek ayrıntıları
 * QnAMaker hizmetinize özel erişimi etkinleştirdikten sonra değişiklik Azure Search hizmetini desteklemiyoruz. Özel erişimi etkinleştirdikten sonra Azure Search hizmetini ' yapılandırma ' sekmesi aracılığıyla değiştirirseniz, QnAMaker hizmeti kullanılamaz hale gelir.
 * Özel uç nokta bağlantısı kurulduktan sonra, Azure Search hizmeti ağını ' Public ' öğesine geçerseniz QnAMaker hizmetini kullanamazsınız. Özel uç nokta bağlantısının çalışması için Azure Search hizmet ağının ' Private ' olması gerekir