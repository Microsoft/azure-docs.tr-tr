---
title: 'Örnek: Azure Machine Learning ile özel bir yetenek oluşturma ve dağıtma'
titleSuffix: Azure Cognitive Search
description: Bu örnekte, Azure Bilişsel Arama 'ın AI zenginleştirme işlem hattı için özel bir yetenek derlemek ve dağıtmak üzere Azure Machine Learning nasıl kullanılacağı gösterilmektedir.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 98d8395236bf955eed88f36c03c96981fa0e4b6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98745642"
---
# <a name="example-build-and-deploy-a-custom-skill-with-azure-machine-learning"></a>Örnek: Azure Machine Learning ile özel bir yetenek oluşturun ve dağıtın 

Bu örnekte, incelemelerden en boy tabanlı yaklaşımı çıkarmak için Azure Machine Learning kullanarak [özel bir yetenek](./cognitive-search-aml-skill.md) oluşturmak için [otel İncelemeleri veri kümesini](https://www.kaggle.com/datafiniti/hotel-reviews) (CREATIVE Commons LISANS [CC BY-NC-SA 4,0](https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode.txt)altında dağıtılır) kullanacaksınız. Bu, aynı gözden geçirme içindeki olumlu ve olumsuz yaklaşım atamasının, personel, oda, giriş veya havuz gibi tanımlanan varlıklara doğru şekilde atanmasını sağlar.

Azure Machine Learning ' de en boy tabanlı yaklaşım modelini eğitmek için, [NLP tariflerinin deposunu](https://github.com/microsoft/nlp-recipes/tree/master/examples/sentiment_analysis/absa)kullanacaksınız. Model daha sonra bir Azure Kubernetes kümesinde bir uç nokta olarak dağıtılır. Dağıtım yapıldıktan sonra, uç nokta, Bilişsel Arama hizmeti tarafından kullanılmak üzere bir AML yeteneği olarak enzenginleştirme ardışık düzenine eklenir.

Belirtilen iki veri kümesi vardır. Modeli kendiniz eğmek istiyorsanız hotel_reviews_1000.csv dosyası gereklidir. Eğitim adımını atlamayı tercih ediyor musunuz? hotel_reviews_100.csv indirin.

> [!div class="checklist"]
> * Azure Bilişsel Arama örneği oluşturma
> * Azure Machine Learning çalışma alanı oluşturma (arama hizmeti ve çalışma alanı aynı abonelikte olmalıdır)
> * Azure Kubernetes kümesine model eğitme ve dağıtma
> * Dağıtılan modele bir AI zenginleştirme işlem hattı bağlama
> * Dağıtılan modelden özel bir beceri olarak çıkış alma

> [!IMPORTANT] 
> Bu yetenek Şu anda genel önizlemededir. Önizleme işlevselliği, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Şu anda .NET SDK desteği yok.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği-ücretsiz bir [abonelik](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)alın.
* [Bilişsel Arama hizmeti](./search-get-started-arm.md)
* [Bilişsel hizmetler kaynağı](../cognitive-services/cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows)
* [Azure Storage hesabı](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure Machine Learning çalışma alanı](../machine-learning/how-to-manage-workspace.md)

## <a name="setup"></a>Kurulum

* [Örnek deponun](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)içeriğini kopyalayın veya indirin.
* İndirme bir ZIP dosyası ise içeriği ayıkla. Dosyaların okuma-yazma olduğundan emin olun.
* Azure hesaplarını ve hizmetlerini ayarlarken, adları ve anahtarları kolayca erişilen bir metin dosyasına kopyalayın. Adlar ve anahtarlar, Not defteri 'ndeki Azure hizmetlerine erişim değişkenlerinin tanımlandığı ilk hücreye eklenir.
* Azure Machine Learning ve gereksinimlerini tanımıyorsanız, başlamadan önce bu belgeleri gözden geçirmek isteyeceksiniz:
 * [Azure Machine Learning için bir geliştirme ortamı yapılandırma](../machine-learning/how-to-configure-environment.md)
 * [Azure portal Azure Machine Learning çalışma alanları oluşturun ve yönetin](../machine-learning/how-to-manage-workspace.md)
 * Azure Machine Learning için geliştirme ortamını yapılandırırken, Başlarken ' de hız ve kolaylık sağlamak için [bulut tabanlı işlem örneğini](../machine-learning/how-to-configure-environment.md#compute-instance) kullanmayı düşünün.
* Veri kümesi dosyasını depolama hesabındaki bir kapsayıcıya yükleyin. Not defterinde eğitim adımını gerçekleştirmek istiyorsanız, daha büyük dosya gereklidir. Eğitim adımını atlamayı tercih ediyorsanız, daha küçük dosya önerilir.

## <a name="open-notebook-and-connect-to-azure-services"></a>Not defteri 'ni açın ve Azure hizmetlerine bağlanın

1. İlk hücrenin içindeki Azure hizmetlerine erişime izin verecek değişkenler için gerekli tüm bilgileri koyun ve hücreyi çalıştırın.
1. İkinci hücreyi çalıştırmak, aboneliğiniz için arama hizmetine bağlandığınızı doğrulayacaktır.
1. Bölümler 1,1-1,5 arama hizmeti veri deposu, Beceri, dizin ve Dizin Oluşturucu oluşturacak.

Bu noktada, eğitim veri kümesi oluşturma ve Azure Machine Learning deneme adımlarını atlayıp GitHub deposunun modeller klasöründe belirtilen iki modeli kaydetmeye doğrudan atlayabilirsiniz. Bu adımları atlarsanız, not defterinde Bölüm 3,5 ' e atlayın, Puanlama betiği yazın. Bu zaman tasarruf eder; veri indirme ve karşıya yükleme adımlarının tamamlanması 30 dakika kadar sürebilir.

## <a name="creating-and-training-the-models"></a>Modeller oluşturma ve eğitme

Bölüm 2 ' de, NLP yemek dosyası deposundan eldiven embeddosyalarını yükleyen altı hücre vardır. İndirildikten sonra dosya Azure Machine Learning veri deposuna yüklenir. . Zip dosyası 2G ile ilgilidir ve bu görevlerin gerçekleşmesi biraz zaman alabilir. Karşıya yüklendikten sonra eğitim verileri ayıklanır ve şimdi bölüm 3 ' e taşımaya hazırsınız.

## <a name="train-the-aspect-based-sentiment-model-and-deploy-your-endpoint"></a>En boy tabanlı yaklaşım modelini eğitme ve uç noktanızı dağıtma

Not defterinin 3. bölümü, Bölüm 2 ' de oluşturulan modelleri eğitecektir, bu modelleri kaydeder ve bunları bir Azure Kubernetes kümesinde uç nokta olarak dağıtır. Azure Kubernetes hakkında bilginiz yoksa, bir çıkarım kümesi oluşturmayı denemeden önce aşağıdaki makaleleri gözden geçirmeniz önemle önerilir:

* [Azure Kubernetes hizmetine genel bakış](../aks/intro-kubernetes.md)
* [Azure Kubernetes hizmeti (AKS) için Kubernetes temel kavramları](../aks/concepts-clusters-workloads.md)
* [Azure Kubernetes Service (AKS) içinde kotalar, sanal makine boyutu kısıtlamaları ve bölge kullanılabilirliği](../aks/quotas-skus-regions.md)

Çıkarım kümesini oluşturmak ve dağıtmak 30 dakikaya kadar sürebilir. Son adımlara geçmeden önce Web hizmetini test etmek, Beceri ' yi güncelleştirmek ve Dizin oluşturucuyu çalıştırmak önerilir.

## <a name="update-the-skillset"></a>Beceri güncelleştirme

Not defterindeki Bölüm 4 ' te beceri ve Indexer güncelleştiren dört hücre vardır. Alternatif olarak, Beceri ' a yeni bir yetenek seçip uygulayıp, sonra da arama hizmetini güncelleştirmek için Dizin oluşturucuyu çalıştırabilirsiniz.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Active-Learning-with-Azure-Cognitive-Search/player#time=19m35s:paused/03/player]

Portalda beceri adresine gidin ve beceri Definition (JSON) bağlantısını seçin. Portal, Not defterinin ilk hücrelerinde oluşturulan beceri JSON 'sini görüntüler. Görüntü sağında, yetenek tanımı şablonunu seçebileceğiniz bir açılan menü vardır. Azure Machine Learning (AML) şablonunu seçin. Azure ML çalışma alanının adını ve çıkarım kümesine dağıtılan modelin uç noktasını belirtin. Şablon, uç nokta URI 'si ve anahtarıyla güncelleştirilir.

> :::image type="content" source="media/cognitive-search-aml-skill/portal-aml-skillset-definition.png" alt-text="Beceri tanımı şablonu":::

Beceri şablonunu pencereden kopyalayın ve sol taraftaki beceri tanımına yapıştırın. Şablonu, için eksik değerleri sağlayacak şekilde düzenleyin:

* Ad
* Açıklama
* Bağlam
* ' girişler ' adı ve kaynağı
* ' çıkışlar ' adı ve targetName

Beceri kaydedin.

Beceri kaydettikten sonra, dizin oluşturucuya gidin ve Dizin Oluşturucu tanımı (JSON) bağlantısını seçin. Portal, Not defterinin ilk hücrelerinde oluşturulan dizin oluşturucunun JSON 'sini görüntüler. Dizin oluşturucunun doğru şekilde işleyebilmesi ve geçirebilmesi için, çıkış alanı eşlemelerinin ek alan eşlemeleriyle güncellenmesi gerekir. Değişiklikleri kaydedin ve sonra Çalıştır ' ı seçin. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kendi aboneliğinizde çalışırken, projenin sonunda oluşturduğunuz kaynaklara hala ihtiyacınız olup olmadığını belirlemek iyi bir fikirdir. Çalışır durumda bırakılan kaynaklar maliyetlerin artmasına neden olabilir. Kaynakları teker teker silebilir veya tüm kaynak grubunu silerek kaynak kümesinin tamamını kaldırabilirsiniz.

Sol gezinti bölmesindeki **tüm kaynaklar** veya **kaynak grupları** bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.

Ücretsiz bir hizmet kullanıyorsanız, üç Dizin, Dizin Oluşturucu ve veri kaynağı ile sınırlı olduğunu unutmayın. Sınırın altında kalmak için portalda ayrı ayrı öğeleri silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Özel beceri Web API](./cognitive-search-custom-skill-web-api.md) 
>  'sini gözden geçirme [Zenginleştirme ardışık düzenine özel beceriler ekleme hakkında daha fazla bilgi edinin](./cognitive-search-custom-skill-interface.md)