---
title: Confluent bulutuna genel bakış-Azure iş ortağı çözümleri Apache Kafka
description: Azure Marketi 'nde confluent bulutu üzerinde Apache Kafka kullanma hakkında bilgi edinin.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 01/15/2021
ms.author: tomfitz
ms.openlocfilehash: fefbc21c385e3beacbf570c31ffbf97238c780fc
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109093"
---
# <a name="what-is-apache-kafka-for-confluent-cloud"></a>Confluent bulutu için Apache Kafka nedir?

Konluent bulutu için Apache Kafka, hizmet olarak Apache Kafka sağlayan bir Azure Marketi teklifleridir. Bunları, kümelerini yönetmek yerine uygulamalarınızın oluşturulmasına odaklanabilmeniz için tam olarak yönetilir.

Platformlar arası yönetimin yükünü azaltmak için, Microsoft, Azure 'dan confluent bulutu 'na tümleşik bir sağlama katmanı oluşturmak üzere confluent bulutuyla işbirliği yaptı. Azure 'da confluent bulutu kullanmaya yönelik bir birleştirilmiş deneyim sağlar. Azure uygulamalarınızla, uyumlu bulutu kolayca tümleştirebilir ve yönetebilirsiniz.

Daha önce, Market 'te confluent bulut teklifini satın almanız ve bu hesabı, confluent bulutu 'nda ayrı olarak ayarlamanız gerekiyordu. Yapılandırma ve kaynakları yönetmek için, Azure ve diğer bulut portalları arasında gezinmeniz gerekiyordu.

Şimdi, **Microsoft. confluent** adlı bir kaynak sağlayıcı aracılığıyla, uyumlu bulut kaynaklarını temin edersiniz. [Azure Portal](https://portal.azure.com/), [Azure CLI](/cli/azure/)veya [Azure SDK 'ları](/azure/#languages-and-tools)aracılığıyla, bulut kuruluş kaynaklarını oluşturun ve yönetin. Uyumlu bulut, ortamlar, kümeler, konular, API anahtarları ve yönetilen bağlayıcılar dahil olmak üzere hizmet olarak yazılım (SaaS) uygulaması için sahiptir ve çalışır.

## <a name="capabilities"></a>Özellikler

Confluent bulutu ve Azure arasında derin tümleştirme aşağıdaki özellikleri sunar:

- Tam olarak yönetilen altyapıyla Azure portal yeni bir confluent bulut kuruluş kaynağı sağlayın.
- Azure 'dan Azure Active Directory (Azure AD) ile sorunsuz buluta yönelik çoklu oturum açma (SSO) işlemlerini kolaylaştırın. Confluent bulut portalında ayrı kimlik doğrulaması gerekmez.
- Azure abonelik faturalaması aracılığıyla, uyumlu bulut tüketiminin Birleşik olarak faturalandırılmasına ulaşın.
- Azure portal ve diğer Azure kaynaklarınızla **tüm kaynaklar** sayfasında, confluent bulut kaynaklarını yönetin ve bunları izleyin.

## <a name="confluent-organization"></a>Uyumlu kuruluş

Bir uyumlu kuruluş, Azure ile uyumlu bulut kaynakları arasında eşlemeyi sağlayan bir kaynaktır. Diğer, diğer bulut kaynakları için üst kaynaktır.

Her bir Azure aboneliği birden çok Confplansı planı içerebilir. Her bir confluent planı, bir kullanıcı hesabı ve kuruluş portalındaki bir kuruluşa eşlenir. Her bir uyumlu kuruluş içinde birden çok ortam, küme, konu ve bağlayıcı oluşturabilirsiniz.

Azure 'da bir confluent bulut kaynağı sağladığınızda, bir veya daha fazla kuruluş KIMLIĞI, varsayılan ortam ve Kullanıcı hesabı alırsınız. Daha fazla bilgi için bkz. [hızlı başlangıç: Azure 'Da confluent bulutu kullanmaya başlama](create.md).

Faturalandırma için, Market 'te satın alınan her bir bulut teklifi, benzersiz bir uyumlu kuruluşa eşlenir.

## <a name="single-sign-on"></a>Çoklu oturum açma

Azure portal oturum açtığınızda, kimlik bilgileriniz, confluent bulut SaaS portalında oturum açmak için de kullanılır. Bu deneyim, oturum açmanıza yönelik güvenli ve uygun bir yol sağlamak için [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) ve [Azure AD SSO](../../active-directory/manage-apps/what-is-single-sign-on.md) kullanır.

Daha fazla bilgi için bkz. [Çoklu oturum açma](manage.md#single-sign-on).

## <a name="billing"></a>Faturalandırma

Kullanılabilecek iki faturalandırma seçeneği vardır: Kullandıkça Öde aylık planı ve taahhüt planı.

- **Kullandıkça Öde aylık planı** sayesinde, Azure aylık faturanızda kesilen bulut tüketim ücretlerini alırsınız.
- **Taahhüt planı** ile, en düşük harcama miktarındaki kaydolun ve bu bulut bulutunuzun kullanım kullanımınız için bir indirim elde edersiniz.

Hizmeti oluştururken hangi faturalandırma seçeneğinin kullanılması gerektiğine karar verirsiniz.

## <a name="connector-to-azure-cosmos-db"></a>Azure Cosmos DB bağlayıcıya

Confluent hub istemcisi içinden, Cosmos DB bağlayıcısını, bağlantı [Merkezi listesinde](https://www.confluent.io/hub/microsoftcorporation/kafka-connect-cosmos)önerilen şekilde yükler. 

Bağlayıcıyı el ile yüklemek için, önce [Cosmos DB yayımları sayfasından](https://github.com/microsoft/kafka-connect-cosmosdb/releases)bir UıBER jar dosyasını indirin. Alternatif olarak, [kendi Uber jar 'nizi doğrudan kaynak kodundan](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/README_Sink.md#install-sink-connector)oluşturabilirsiniz. [Bağlayıcıları el ile yüklemek](https://docs.confluent.io/home/connect/install.html#install-connector-manually)için, bu belgelerde açıklanan kılavuzu izleyerek yüklemeyi doldurun.  

## <a name="confluent-links"></a>Bağlantı bağlantıları

Confluent bulutu için Apache Kafka kullanmayla ilgili ek yardım için, [confluent sitesine](https://docs.confluent.io/home/overview.html)yönelik aşağıdaki bağlantılara bakın.

Faturalandırma seçenekleri hakkında bilgi edinmek için bkz.:

* [Kullandıkça Öde ile Azure Marketi](https://docs.confluent.io/cloud/current/billing/ccloud-azure-payg.html)
* [Taahhütlerle Azure Marketi](https://docs.confluent.io/cloud/current/billing/ccloud-azure-ubb.html)

Çözümleri yönetme hakkında bilgi edinmek için bkz.:

* [Confluent bulutu 'nda küme oluşturma](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)
* [Uyumlu bulut ortamları](https://docs.confluent.io/current/cloud/using/environments.html)
* [Confluent bulutu temelleri](https://docs.confluent.io/current/cloud/using/cloud-basics.html)

Destek ve koşullar için bkz.:

* [Confluent desteği](https://support.confluent.io)
* [Hizmet koşulları](https://www.confluent.io/confluent-cloud-tos).

## <a name="next-steps"></a>Sonraki adımlar

Confluent bulutu için bir Apache Kafka örneği oluşturmak için bkz. [hızlı başlangıç: Azure 'Da confluent bulutu kullanmaya başlama](create.md).
