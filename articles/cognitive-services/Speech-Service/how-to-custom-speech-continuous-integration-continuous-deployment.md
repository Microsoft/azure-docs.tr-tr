---
title: Özel Konuşma Tanıma-konuşma hizmeti için CI/CD
titleSuffix: Azure Cognitive Services
description: Özel Konuşma Tanıma ve CI/CD iş akışlarıyla DevOps uygulayın. Kendi projeniz için mevcut bir DevOps çözümü uygulayın.
services: cognitive-services
author: KatieProchilo
manager: cmayomsft
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: kaprochi
ms.openlocfilehash: f7f11ef097d3abee2b4c18c32a1cb215d9fe8ce8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98939890"
---
# <a name="cicd-for-custom-speech"></a>Özel Konuşma Tanıma için CI/CD

Eğitim ve test verilerine güncelleştirmeler uyguladığınızda Özel Konuşma Tanıma modellerinin sürekli gelişmesini sağlamak için otomatik eğitim, test ve sürüm yönetimi uygulayın. CI/CD iş akışlarının etkili bir şekilde uygulanması sayesinde, en iyi performanslı Özel Konuşma Tanıma modeli için uç noktanın her zaman kullanılabilir olduğundan emin olabilirsiniz.

[Sürekli tümleştirme](/azure/devops/learn/what-is-continuous-integration) (CI), paylaşılan bir depodaki güncelleştirmeleri sık sık kaydetme ve üzerinde otomatik derleme gerçekleştirme Mühendisliği uygulamasıdır. Özel Konuşma Tanıma için CI iş akışları, veri kaynaklarından yeni bir modeli eğitme ve yeni modelde otomatik test gerçekleştirerek, önceki modelden daha iyi bir performans gerçekleştirdiğinden emin olur.

[Sürekli teslim](/azure/devops/learn/what-is-continuous-delivery) (CD), CI işleminden modeller alır ve iyileştirilmiş her özel konuşma tanıma modeli için bir uç nokta oluşturur. CD, uç noktaların çözümlerle tümleştirilerek kolayca kullanılabilmesini sağlar.

Özel CI/CD çözümleri mümkündür, ancak sağlam, önceden oluşturulmuş bir çözüm için GitHub eylemlerini kullanarak CI/CD iş akışlarını yürüten [konuşma DevOps şablon deposunu](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)kullanın.

## <a name="cicd-workflows-for-custom-speech"></a>Özel Konuşma Tanıma için CI/CD iş akışları

Bu iş akışlarının amacı, her bir Özel Konuşma Tanıma modelinin önceki derlemeden daha iyi tanıma doğruluğu olmasını sağlamaktır. Test ve/veya eğitim verilerinde yapılan güncelleştirmeler doğruluğu iyileştirçalışıyorsa, bu iş akışları yeni bir Özel Konuşma Tanıma uç noktası oluşturur.

GitHub ve Azure DevOps gibi git sunucuları, birleştirme veya çekme istekleri gibi belirli git olayları gerçekleştiğinde otomatik iş akışlarını çalıştırabilir. Örneğin, verileri test etmek için güncelleştirmeler *ana* dala GÖNDERILDIĞINDE bir CI iş akışı tetiklenebilir. Farklı git sunucularının farklı araçları olur, ancak betik komut satırı arabirimi (CLı) komutlarına bir yapı sunucusunda yürütülebilmeleri için komut satırı arabirimi (CLı) komutlarına izin verir.

Bu şekilde iş akışları, verileri, testleri, test dosyalarını, modelleri ve uç noktaları, tarafından verilen yürütmeye veya sürüme geri getiribilecekleri şekilde ad ve depolar. Bu varlıkları, test verileri ve eğitim verileri güncelleştirildikten sonra oluşturulan bir şekilde görmek kolay olacak şekilde adlandırmak da faydalı olur.

### <a name="ci-workflow-for-testing-data-updates"></a>Veri güncelleştirmelerini test etmek için CI iş akışı

CI/CD iş akışlarının asıl amacı, eğitim verilerini kullanarak yeni bir model oluşturmak ve bu modeli, [Word hata oranının](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) (WER) önceki en iyi şekilde gerçekleştiren modelle ("kıyaslama modeli") karşılaştırıldığında iyileştirilip artmadığını belirlemek için test verilerini kullanarak test etmek içindir. Yeni model daha iyi çalışıyorsa, gelecekteki modellerin karşılaştırılacağı yeni kıyaslama modeli olur.

Veri güncelleştirmelerini test etmek için CI iş akışı, düzeltilen WER 'yi hesaplamak için geçerli kıyaslama modelini güncelleştirilmiş test verileriyle yeniden test etmelidir. Bu, yeni bir modelin WER, kıyaslama WER ile karşılaştırıldığında, her iki modelin de aynı test verileriyle test edilmiş olması ve benzer şekilde karşılaştırılabilmesini sağlar.

Bu iş akışı, verileri test etmek için güncelleştirmelerde tetiklemelidir:

- Kıyaslama modelini güncelleştirilmiş test verileriyle test edin.
- Güncelleştirilmiş verileri kullanarak kıyaslama modelinin WER 'u içeren test çıktısını saklayın.
- Bu testlerin WER, gelecekteki modellerin olması gereken yeni kıyaslama WER olur.
- CD iş akışı, verileri test güncelleştirmeleriyle ilgili güncelleştirmeler için yürütülmez.

### <a name="ci-workflow-for-training-data-updates"></a>Eğitim verileri güncelleştirmeleri için CI iş akışı

Eğitim verileri güncelleştirmeleri, özel modeldeki güncelleştirmeleri işaret eder.

Bu iş akışı, eğitim verilerine yönelik güncelleştirmelerde tetiklemelidir ve:

- Güncelleştirilmiş eğitim verileriyle yeni bir model eğitme.
- Test verilerinde yeni modeli test edin.
- WER içeren test çıkışını depolayın.
- Yeni modelden WER modelini, kıyaslama modelinden wer ile karşılaştırın.
- WER geliştirmezse iş akışını durdurun.
- WER artdığı takdirde Özel Konuşma Tanıma uç noktası oluşturmak için CD iş akışını yürütün.

### <a name="cd-workflow"></a>CD iş akışı

Eğitim verilerine yönelik bir güncelleştirme bir modelin tanınmasını iyileştirdikten sonra, bu model için yeni bir uç nokta oluşturmak üzere CD iş akışının otomatik olarak yürütülmesi ve bu uç noktanın bir çözümde kullanılabilmesi için kullanılabilir olması gerekir.

#### <a name="release-management"></a>Yayın yönetimi

Çoğu takım, bir üretim ortamına dağıtım için el ile gözden geçirme ve onay süreci gerektirir. Bir üretim dağıtımı için, geliştirme ekibinizdeki önemli kişilerin destek için kullanılabilir olduğu veya düşük trafikli dönemler sırasında olduğundan emin olmak isteyebilirsiniz.

### <a name="tools-for-custom-speech-workflows"></a>Özel Konuşma Tanıma iş akışları için Araçlar

Özel Konuşma Tanıma için CI/CD Otomasyonu iş akışları için aşağıdaki araçları kullanın:

- Azure [CLI](/cli/azure/) , Azure hizmet sorumlusu kimlik doğrulaması oluşturma, Azure aboneliklerini sorgulama ve test sonuçlarını Azure Blob 'da depolama.
- Konuşma hizmeti ile komut satırından veya otomatik bir iş akışından etkileşimde bulunmak için [Azure konuşma CLI](spx-overview.md) .

## <a name="devops-solution-for-custom-speech-using-github-actions"></a>GitHub eylemlerini kullanarak Özel Konuşma Tanıma için DevOps çözümü

Özel Konuşma Tanıma için zaten uygulanmış bir DevOps çözümü için, [konuşma DevOps şablon](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)deposuna gidin. Şablonun bir kopyasını oluşturun ve GitHub eylemlerini kullanarak test, eğitim ve sürüm oluşturma içeren sağlam bir DevOps sistemiyle özel modellerin geliştirilmesini başlatın. Depo, kurulum 'da yardımcı olacak örnek test ve eğitim verileri sağlar ve iş akışını açıklar. İlk kurulumdan sonra örnek verileri proje verileriyle değiştirin.

[Konuşma DevOps şablonu deposu](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) , için altyapı ve ayrıntılı rehberlik sağlar:

- Şablon deposunu GitHub hesabınıza kopyalayın, ardından GitHub eylemleri CI/CD iş akışları için Azure kaynakları ve [hizmet sorumlusu](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) oluşturun.
- "[Geliştirme iç döngüsü](/dotnet/architecture/containerized-lifecycle/design-develop-containerized-apps/docker-apps-inner-loop-workflow)" boyunca ilerleyin. Güncelleştirme eğitimi ve bir özellik dalında verileri test edin, değişiklikleri geçici bir geliştirme modeliyle test edin ve değişiklikleri önermek ve gözden geçirmek için bir çekme isteği yükseltin.
- Eğitim verileri bir çekme isteğinde *Main*'e güncelleniyorsa, modelleri GITHUB eylemleri CI iş akışıyla eğitme.
- Bir modelin [sözcük hata oranını](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) (WER) oluşturmak için otomatik doğruluk testi gerçekleştirin. Test sonuçlarını Azure Blob 'da depolayın.
- WER artdığı zaman bir uç nokta oluşturmak için CD iş akışını yürütün.

## <a name="next-steps"></a>Sonraki adımlar

Konuşma ile DevOps hakkında daha fazla bilgi edinin:

- GitHub eylemleriyle Özel Konuşma Tanıma için DevOps uygulamak üzere [Speech DevOps şablon](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) deposunu kullanın.
