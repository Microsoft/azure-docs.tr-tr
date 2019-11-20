---
title: Sınırlı bir deneme projesini Azure 'a taşıma
titleSuffix: Azure Cognitive Services
description: Özel Görüntü İşleme için sınırlı bir deneme projeniz mı var? Bu makalede, bir geçiş betiği ile Azure 'a nasıl taşıyacağınız gösterilmektedir.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 0a2b48a6c046150f6e685ecda0c0d765342e0194
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818944"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>Sınırlı deneme projenizi Azure 'a taşıma

Özel Görüntü İşleme Hizmeti Azure 'a taşıma işlemini tamamladıktan sonra, Azure dışında sınırlı deneme projelerine yönelik destek sona eriyor. Bu belgede, sınırlı deneme projenizi bir Azure kaynağına kopyalamak için Özel Görüntü İşleme API 'Lerinin nasıl kullanılacağı gösterilir.

[Özel görüntü işleme web sitesinde](https://customvision.ai) sınırlı deneme projelerini görüntüleme desteği 25 Mart 2019 tarihinde sona erdi. Bu belgede, projenizi bir Azure kaynağına çoğaltmak için Özel Görüntü İşleme API 'Lerinin GitHub üzerinde [geçiş Python betiği](https://github.com/Azure-Samples/custom-vision-move-project) ile nasıl kullanılacağı gösterilmektedir.

Sınırlı deneme sürümü kaldırma işlemindeki anahtar son tarihleri dahil olmak üzere daha fazla ayrıntı için lütfen [sürüm notlarına](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019) veya sınırlı deneme projelerinin sahibine gönderilen e-posta iletişimlerine bakın.

[Geçiş betiği](https://github.com/Azure-Samples/custom-vision-move-project) , geçerli yineleinizdeki tüm etiketleri, bölgeleri ve görüntüleri indirip karşıya yükleyerek bir projeyi yeniden oluşturmanızı sağlar. Daha sonra eğitebileceğiniz yeni aboneliğinizdeki yeni bir proje ile sizi bu şekilde bırakacaktır.

## <a name="prerequisites"></a>Ön koşullar

- [Özel görüntü işleme web sitesinde](https://customvision.ai)oturum açmak için kullanmak istediğiniz Microsoft hesabı veya Azure ACTIVE DIRECTORY (AAD) hesabıyla ilişkili geçerli bir Azure aboneliğine ihtiyacınız olacaktır. 
    - Azure hesabınız yoksa ücretsiz [bir hesap oluşturun](https://azure.microsoft.com/free/) .
    - Aboneliklerin ve kaynakların Azure kavramlarına giriş için [Azure Geliştirici Kılavuzu](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)' na bakın.
-  [Python](https://www.python.org/downloads/)
- [Pip](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Azure portal Özel Görüntü İşleme kaynakları oluşturma

Azure ile Özel Görüntü İşleme Hizmeti kullanmak için, [Azure portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision)özel görüntü işleme eğitim ve tahmin kaynakları oluşturmanız gerekecektir. 

Birden çok proje tek bir kaynakla ilişkilendirilebilir. [Fiyatlandırma ve limitlere](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) ilişkin daha fazla ayrıntı mevcuttur. Özel Görüntü İşleme Hizmeti ücretsiz kullanmaya devam etmek için Azure portal katmanını seçin. 

> [!NOTE]
> Özel Görüntü İşleme projenizi bir Azure kaynağına taşıdığınızda, bu Azure kaynağının temel aldığı [izinleri]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) devralır. Kuruluşunuzdaki diğer kullanıcılar projenizin Azure kaynağının sahipleri ise, [özel görüntü işleme web sitesinde](https://customvision.ai)projenize erişebilecektir. Benzer şekilde, kaynaklarınızın silinmesi projelerinizi de siler.  

## <a name="find-your-limited-trial-project-information"></a>Sınırlı deneme projesi bilgilerinizi bulun

Projenizi taşımak için geçirmeye çalıştığınız proje için _proje kimliği_ ve _eğitim anahtarına_ ihtiyacınız olacaktır. Bu bilgilere sahip değilseniz, projelerinizden her birine ait KIMLIĞI ve anahtarı almak için [https://www.customvision.ai/projects](https://www.customvision.ai/projects) ziyaret edin. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>Projenizi Azure 'a kopyalamak için Python örnek kodunu kullanın

"Kaynak" malzemeler ve "hedef" olarak oluşturduğunuz yeni Azure kaynağından anahtar olarak, sınırlı deneme anahtarınızı ve proje KIMLIĞINIZI kullanarak [örnek kod yönergelerini](https://github.com/Azure-Samples/custom-vision-move-project)izleyin.

Varsayılan olarak, tüm sınırlı deneme projeleri Orta Güney ABD Azure bölgesinde barındırılır.

## <a name="next-steps"></a>Sonraki adımlar

Projeniz artık bir Azure kaynağına taşındı. Yazdığınız tüm uygulamalarda eğitim ve tahmin anahtarlarınızı güncelleştirmeniz gerekir.

[Özel görüntü işleme web sitesinde](https://customvision.ai)projenizi görüntülemek için, Azure Portal oturum açmak için kullandığınız hesapla oturum açın. Projenizi görmüyorsanız, lütfen [özel görüntü işleme web sitesinde](https://customvision.ai) kaynaklarınızın Azure Portal bulunduğu dizin olarak aynı dizinde olduğunu lütfen doğrulayın. Hem Azure portal hem de CustomVision.ai içinde, ekranın sağ üst köşesindeki açılır Kullanıcı menüsünden dizininizi seçebilirsiniz.