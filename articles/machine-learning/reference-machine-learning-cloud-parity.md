---
title: Ortak ve bağımsız bölgeleri arasında eşlik
titleSuffix: Azure Machine Learning
description: Bu makalede, genel bulut ile Azure Kamu, Azure Almanya ve Azure Çin 21Vianet bölgeleri arasındaki özellik eşliği listelenmektedir.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: andzha
author: Anurzeuii
ms.date: 12/21/2020
ms.custom: references_regions
ms.openlocfilehash: 88240f9b46997d11f1e7c2d93fa880b004615a11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97725029"
---
# <a name="azure-machine-learning-sovereign-cloud-parity"></a>Azure Machine Learning sogeign bulut eşliği

Bağımsız bulut bölgelerinde Azure Machine Learning özelliklerinin kullanılabildiğini öğrenin. 

Küresel Azure bölgeleri listesinde, belirli pazarlara yönelik birkaç ' sogeign ' bölgesi vardır. Örneğin, Azure Kamu ve Azure Çin 21Vianet bölgeleri. Şu anda Azure Machine Learning aşağıdaki bağımsız bulut bölgelerine dağıtılır:

* Azure Kamu bölgeleri **US-Arizona** ve **US-Virginia**.
* Azure Çin 21Vianet bölgesi **Çin-Doğu-2**.

> [!TIP]
> Sogeign ve soveremeyen bölgeler arasında ayrım yapmak için bu makale, __genel bulut__ terimini kullanarak sogeign olmayan bölgelere başvurur.

Genel bulutumuz ve bağımsız Bölgelerimiz arasında maksimum eşlik sağlamamız hedefliyoruz. Tüm Azure Machine Learning özellikleri, genel bulutumuzdaki **30 gün** aşağı (genel kullanılabilirlik) dahilinde bu bölgelerde kullanılabilir. Ayrıca, bu bölgelerde daha fazla sayıda önizleme özelliği de etkinleştik. Aşağıda, sogeign ve genel bulutlarımızla arasındaki geçerli eşlik farklılıkları görüntülenir.

## <a name="azure-government"></a>Azure Kamu 

| Özellik | Genel bulut durumu  | US-Virginia | US-Arizona| 
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|:-------------:|
| **Otomatik makine öğrenimi** | | | |
| Not defterlerinde denemeleri oluşturma ve çalıştırma                                    | GA                   | EVET                | EVET         |
| Studio Web deneyiminde denemeleri oluşturma ve çalıştırma                        | Genel Önizleme       | EVET                | EVET         |
| Sektör lideri tahmin özellikleri                                  | GA                   | EVET                | EVET         |
| Derin öğrenme ve diğer gelişmiş öğrenenler için destek                      | GA                   | EVET                | EVET         |
| Büyük veri desteği (100 GB 'a kadar)                                          | Genel Önizleme       | EVET                | EVET         |
| Azure Databricks tümleştirme                                              | GA                   | NO                 | NO          |
| SQL, CosmosDB ve HDInsight tümleştirmeleri                                   | GA                   | EVET                | EVET         |
| **Machine Learning işlem hatları** |   |  | | 
| Azure ML SDK kullanarak işlem hatları oluşturma, çalıştırma ve yayımlama                   | GA                   | EVET                | EVET         |
| Azure ML SDK 'sını kullanarak işlem hattı uç noktaları oluşturma                           | GA                   | EVET                | EVET         |
| Azure ML SDK 'sını kullanarak zamanlanan işlem hattı çalıştırmalarını oluşturma, düzenleme ve silme | GA                   | Yes               | Yes        |
| Studio 'da işlem hattı çalıştırma ayrıntılarını görüntüleme                                        | GA                   | EVET                | EVET         |
| Azure ML tasarımcısında işlem hatları oluşturma, çalıştırma, görselleştirme ve yayımlama          | GA      | EVET                | EVET         |
| ML işlem hattı ile tümleştirme Azure Databricks                             | GA                   | NO                 | NO          |
| Azure ML tasarımcısında işlem hattı uç noktaları oluşturma                             | GA      | EVET                | EVET         |
| **Tümleşik Not defterleri** |   |  | | 
| Çalışma alanı Not defteri ve dosya paylaşımı                                        | GA                   | EVET                | EVET         |
| R ve Python desteği                                                       | GA                   | EVET                | EVET         |
| Sanal Ağ desteği                                                    | Genel Önizleme       | NO                 | NO          |
| **İşlem örneği** |   |  | | 
| Tümleşik Not defterleri için yönetilen işlem örnekleri                         | GA                   | EVET                | EVET         |
| Jupyıter, Jupyıterlab tümleştirmesi                                            | GA                   | EVET                | EVET         |
| Sanal ağ (VNet) desteği                                             | Genel Önizleme       | EVET                | EVET         |
| **SDK desteği** |  |  | | 
| R SDK desteği                                                              | Genel Önizleme       | EVET                | EVET         |
| Python SDK desteği                                                         | GA                   | EVET                | EVET         |
| **Güvenlik** |   | | | 
| Eğitim için sanal ağ (VNet) desteği                                | GA                   | EVET                | EVET         |
| Çıkarım için sanal ağ (VNet) desteği                               | GA                   | EVET                | EVET         |
| Puanlama uç noktası kimlik doğrulaması                                            | Genel Önizleme       | EVET                | EVET         |
| Çalışma alanı özel bağlantısı                                                     | Genel Önizleme       | NO                 | NO          |
| VNet 'in arkasında ACI                                                            | Genel Önizleme       | NO                 | NO          |
| VNet arkasında ACR                                                            | Genel Önizleme       | NO                 | NO          |
| AKS kümesinin özel IP 'si                                                  | Genel Önizleme       | NO                 | NO          |
| **İşlem** |   | | |
| çalışma alanları genelinde Kota Yönetimi                                         | GA                   | EVET                | EVET         |
| **Machine Learning için veriler** |   | | |
| SDK 'dan veri kümeleri ve veri depoları oluşturma, görüntüleme veya düzenleme                  | GA                   | EVET                | EVET         |
| Kullanıcı arabiriminden veri kümeleri ve veri depoları oluşturma, görüntüleme veya düzenleme                   | GA                   | EVET                | EVET         |
| SDK 'dan veri kümesi DRFT izleyicileri görüntüleme, düzenleme veya silme                   | Genel Önizleme       | EVET                | EVET         |
| Kullanıcı arabiriminden veri kümesi DRFT izleyicileri görüntüleme, düzenleme veya silme                    | Genel Önizleme       | EVET                | EVET         |
| **Makine öğrenimi yaşam döngüsü** |   | | |
| Model profili oluşturma                                                            | GA                   | EVET                | KıSMI     |
| Azure ML CLI & Machine Learning için Azure DevOps uzantısı         | GA                   | EVET                | EVET         |
| FPGA tabanlı Hızlandırılmış Donanım Modelleri                                     | GA                   | NO                 | NO          |
| Visual Studio Code tümleştirme                                             | Genel Önizleme       | NO                 | NO          |
| Event Grid tümleştirmesi                                                     | Genel Önizleme       | NO                 | NO          |
| Azure Stream Analytics Azure Machine Learning ile tümleştirin               | Genel Önizleme       | NO                 | NO          |
| **Kapatma** |   | | |
| Proje Yönetim Portalı etiketleme                                        | GA                   | EVET                | EVET         |
| Etiketleyici portalı                                                            | GA                   | EVET                | EVET         |
| Özel iş gücü kullanarak etiketleme                                          | GA                   | EVET                | EVET         |
| ML yardımlı etiketleme (görüntü sınıflandırması ve nesne algılama)           | Genel Önizleme       | EVET                | EVET         |
| **Sorumlu makine öğrenmesi** |   | | |
| Kullanıcı arabiriminde Explainability                                                       | Genel Önizleme       | NO                 | NO          |
| Değişiklik gizliliği Smartgürültü araç seti                                    | OSS                  | NO                 | NO          |
| veri sayfalarını uygulamak için Azure Machine Learning özel Etiketler              | GA                   | NO                 | NO          |
| Eşitliği AzureML tümleştirmesi                                               | Genel Önizleme       | NO                 | NO          |
| Yorumlenebilirlik SDK 'Sı                                                      | GA                   | EVET                | EVET         |
| **Eğitim** |   | | |
| Deneme günlük akışı                                              | GA                   | EVET                | EVET         |
| Pekiştirmeye dayalı öğrenme                                                     | Genel Önizleme       | NO                 | NO          |
| Deneme Kullanıcı arabirimi                                                         | GA                   | EVET                | EVET         |
| .NET tümleştirmesi ML.NET 1,0                                                | GA                   | EVET                | EVET         |
| **Ma** |   | | |
| Toplu iş ınkrii                                                          | GA                   | EVET                | EVET         |
| FPGA ile Data Box Edge                                                    | Genel Önizleme       | NO                 | NO          |
| **Diğer** |   | | |
| Açık Veri Kümeleri                                                              | Genel Önizleme       | EVET                | EVET         |
| Özel Bilişsel Arama                                                    | Genel Önizleme       | EVET                | EVET         |
| Birçok model                                                                | Genel Önizleme       | NO                 | NO          |


### <a name="azure-government-scenarios"></a>Azure Kamu senaryoları

| Senaryo                                                    | US-Virginia | US-Arizona| Sınırlamalar  |
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|-------------|
| **Genel güvenlik kurulumu** |   | | |
| Hizmetler arasındaki özel ağ iletişimi                                     | NO | NO | Şu anda özel bağlantı yok | 
| İnternet erişimini devre dışı bırak/denetle (gelen ve giden) ve belirli VNet | KıSMI| KıSMI   | Azure Kamu 'da VNet 'in arkasında ACR yok, ACI 'de çift denetleniyor | 
| Tüm ilişkili kaynaklar/hizmetler için yerleştirme  | EVET | EVET |  |
| REST ve aktarım yerinde şifreleme.                                                 | EVET | EVET |  |
| İşlem kaynaklarına kök ve SSH erişimi.                                          | EVET | EVET |  |
| Endpoint Protection, düzeltme eki uygulama ve günlüğe kaydetme dahil dağıtılan sistemlerin (örnekler, uç noktalar vb.) güvenliğini sağlama |  KıSMI|  KıSMI |VNet ve özel uç noktanın arkasındaki ACI Şu anda kullanılamıyor |                                  
| ACı/AKS tümleştirmesinin kullanımını denetleme (devre dışı bırakma/sınırlama/kısıtlama)                    | KıSMI| KıSMI |VNet ve özel uç noktanın arkasındaki ACI Şu anda kullanılamıyor|
| Azure rol tabanlı erişim denetimi (Azure RBAC)-özel rol oluşturma                           | EVET | EVET |  |
| ML hizmeti tarafından kullanılan ACR görüntülerine erişimi denetleme (Azure tarafından sağlanan/korunan/özel)  |KıSMI|  KıSMI | Azure Kamu 'da desteklenmeyen özel uç nokta ve VNet 'in arkasında ACR |
| **Genel Machine Learning hizmeti kullanımı** |  | | |
| Bir model oluşturmak, bu modeli eğmek, bir uç nokta olarak barındırmak ve bir WebApp aracılığıyla kullanmak için bir geliştirme ortamı kullanabilme     | EVET | EVET |  |
| ADLS 'den veri çekme özelliği (Data Lake Storage)                                 |EVET | EVET |  |
| Azure Blob depolamadan veri çekme özelliği                                       |EVET | EVET |  |



### <a name="additional-azure-government-limitations"></a>Ek Azure Kamu sınırlamaları

* Azure Machine Learning işlem örnekleri için, 24 saatten fazla bir belirteç yenileme özelliği Azure Kamu 'da kullanılamaz.
* Model profili oluşturma, US-Arizona bölgesinde 4 CPU desteklemez.   
* Ortak verilere erişmesi gerekiyorsa örnek Not defterleri, Azure Kamu 'da çalışmayabilir.
* IP adresleri: VNET 'te kullanılan CLı komutu [ve Zorlamalı tünel](how-to-secure-training-vnet.md#forced-tunneling) yönergeleri IP aralıklarını döndürmez. Bunun yerine Azure [kamu Için Azure IP aralıklarını ve hizmet etiketlerini](https://www.microsoft.com/download/details.aspx?id=57063) kullanın.
* Zamanlanan işlem hatları için Ayrıca blob tabanlı bir tetikleyici mekanizması sunuyoruz. Bu mekanizma CMK çalışma alanları için desteklenmez. CMK çalışma alanları için blob tabanlı bir tetikleyiciyi etkinleştirmek üzere ek kurulum yapmanız gerekir. Daha fazla bilgi için bkz. [bir mantıksal uygulamadan makine öğrenimi işlem hattı çalıştırmayı tetikleme](how-to-trigger-published-pipeline.md).
* Güvenlik duvarları: bir Azure Kamu bölgesi kullanırken, güvenlik duvarı ayarınızdan aşağıdaki ek Konakları ekleyin:

    * Arizona kullanımı için: `usgovarizona.api.ml.azure.us`
    * Virginia kullanım için: `usgovvirginia.api.ml.azure.us`
    * Her ikisi için: `graph.windows.net` 


## <a name="azure-china-21vianet"></a>Azure China 21Vianet 

| Özellik                                       | Genel bulut durumu | CH-Doğu-2 | CH-Kuzey-3 |
|----------------------------------------------------------------------------|:------------------:|:--------------------:|:-------------:|
| **Otomatik makine öğrenimi** |    | | |
| Not defterlerinde denemeleri oluşturma ve çalıştırma                                    | GA               | EVET       | Yok        |
| Studio Web deneyiminde denemeleri oluşturma ve çalıştırma                        | Genel Önizleme   | EVET       | Yok        |
| Sektör lideri tahmin özellikleri                                  | GA               | EVET       | Yok        |
| Derin öğrenme ve diğer gelişmiş öğrenenler için destek                      | GA               | EVET       | Yok        |
| Büyük veri desteği (100 GB 'a kadar)                                          | Genel Önizleme   | EVET       | Yok        |
| Azure Databricks tümleştirme                                              | GA               | NO        | Yok        |
| SQL, CosmosDB ve HDInsight tümleştirmeleri                                   | GA               | EVET       | Yok        |
| **Machine Learning işlem hatları** |    | | |
| Azure ML SDK kullanarak işlem hatları oluşturma, çalıştırma ve yayımlama                   | GA               | EVET       | Yok        |
| Azure ML SDK 'sını kullanarak işlem hattı uç noktaları oluşturma                           | GA               | EVET       | Yok        |
| Azure ML SDK 'sını kullanarak zamanlanan işlem hattı çalıştırmalarını oluşturma, düzenleme ve silme | GA               | EVET       | Yok        |
| Studio 'da işlem hattı çalıştırma ayrıntılarını görüntüleme                                        | GA               | EVET       | Yok        |
| Azure ML tasarımcısında işlem hatları oluşturma, çalıştırma, görselleştirme ve yayımlama          | GA  | EVET       | Yok        |
| ML işlem hattı ile tümleştirme Azure Databricks                             | GA               | NO        | Yok        |
| Azure ML tasarımcısında işlem hattı uç noktaları oluşturma                             | GA   | EVET       | Yok        |
| **Tümleşik Not defterleri** |   | | |
| Çalışma alanı Not defteri ve dosya paylaşımı                                        | GA               | EVET       | Yok        |
| R ve Python desteği                                                       | GA               | EVET       | Yok        |
| Sanal Ağ desteği                                                    | Genel Önizleme   | NO        | Yok        |
| **İşlem örneği** |    | | |
| Tümleşik Not defterleri için yönetilen işlem örnekleri                         | GA               | NO        | Yok        |
| Jupyıter, Jupyıterlab tümleştirmesi                                            | GA               | EVET       | Yok        |
| Sanal ağ (VNet) desteği                                             | Genel Önizleme   | EVET       | Yok        |
| **SDK desteği** |    | | |
| R SDK desteği                                                              | Genel Önizleme   | EVET       | Yok        |
| Python SDK desteği                                                         | GA               | EVET       | Yok        |
| **Güvenlik** |   | | |
| Eğitim için sanal ağ (VNet) desteği                                | GA               | EVET       | Yok        |
| Çıkarım için sanal ağ (VNet) desteği                               | GA               | EVET       | Yok        |
| Puanlama uç noktası kimlik doğrulaması                                            | Genel Önizleme   | EVET       | Yok        |
| Çalışma alanı özel bağlantısı                                                     | Genel Önizleme   | NO        | Yok        |
| VNet 'in arkasında ACI                                                            | Genel Önizleme   | NO        | Yok        |
| VNet arkasında ACR                                                            | Genel Önizleme   | NO        | Yok        |
| AKS kümesinin özel IP 'si                                                  | Genel Önizleme   | NO        | Yok        |
| **İşlem** |   | | |
| çalışma alanları genelinde Kota Yönetimi                                         | GA               | EVET       | Yok        |
| **Machine Learning için veriler** | | | |
| SDK 'dan veri kümeleri ve veri depoları oluşturma, görüntüleme veya düzenleme                  | GA               | EVET       | Yok        |
| Kullanıcı arabiriminden veri kümeleri ve veri depoları oluşturma, görüntüleme veya düzenleme                   | GA               | EVET       | Yok        |
| SDK 'dan veri kümesi DRFT izleyicileri görüntüleme, düzenleme veya silme                   | Genel Önizleme   | EVET       | Yok        |
| Kullanıcı arabiriminden veri kümesi DRFT izleyicileri görüntüleme, düzenleme veya silme                    | Genel Önizleme   | EVET       | Yok        |
| **Makine öğrenimi yaşam döngüsü** |    | | |
| Model profili oluşturma                                                            | GA               | KıSMI   | Yok        |
| Azure ML CLI & Machine Learning için Azure DevOps uzantısı         | GA               | EVET       | Yok        |
| FPGA tabanlı Hızlandırılmış Donanım Modelleri                                     | GA               | NO        | Yok        |
| Visual Studio Code tümleştirme                                             | Genel Önizleme   | NO        | Yok        |
| Event Grid tümleştirmesi                                                     | Genel Önizleme   | EVET       | Yok        |
| Azure Stream Analytics Azure Machine Learning ile tümleştirin               | Genel Önizleme   | NO        | Yok        |
| **Kapatma** |    | | |
| Proje Yönetim Portalı etiketleme                                        | GA               | EVET       | Yok        |
| Etiketleyici portalı                                                            | GA               | EVET       | Yok        |
| Özel iş gücü kullanarak etiketleme                                          | GA               | EVET       | Yok        |
| ML yardımlı etiketleme (görüntü sınıflandırması ve nesne algılama)           | Genel Önizleme   | EVET       | Yok        |
| **Sorumlu makine öğrenmesi** |    | | |
| Kullanıcı arabiriminde Explainability                                                       | Genel Önizleme   | NO        | Yok        |
| Değişiklik gizliliği Smartgürültü araç seti                                    | OSS              | NO        | Yok        |
| veri sayfalarını uygulamak için Azure Machine Learning özel Etiketler              | GA               | NO        | Yok        |
| Eşitliği AzureML tümleştirmesi                                               | Genel Önizleme   | NO        | Yok        |
| Yorumlenebilirlik SDK 'Sı                                                      | GA               | EVET       | Yok        |
| **Eğitim** |    | | |
| Deneme günlük akışı                                              | GA               | EVET       | Yok        |
| Pekiştirmeye dayalı öğrenme                                                     | Genel Önizleme   | NO        | Yok        |
| Deneme Kullanıcı arabirimi                                                         | GA               | EVET       | Yok        |
| .NET tümleştirmesi ML.NET 1,0                                                | GA               | EVET       | Yok        |
| **Ma** |   | | |
| Toplu iş ınkrii                                                          | GA               | EVET       | Yok        |
| FPGA ile Data Box Edge                                                    | Genel Önizleme   | NO        | Yok        |
| **Diğer** |    | | |
| Açık Veri Kümeleri                                                              | Genel Önizleme   | EVET       | Yok        |
| Özel Bilişsel Arama                                                    | Genel Önizleme   | EVET       | Yok        |
| Birçok model                                                                | Genel Önizleme   | NO        | Yok        |



### <a name="additional-azure-china-limitations"></a>Ek Azure Çin sınırlamaları

* Azure Çin 'de, özellikle GPU SKU 'SU için sınırlı VM SKU 'SU vardır. Yalnızca NCv3 ailesi vardır (v100).
* REST API uç noktaları küresel Azure 'dan farklıdır. Azure Çin bölgeleri için REST API uç noktasını bulmak için aşağıdaki tabloyu kullanın:

    | REST uç noktası                 | Küresel Azure                                 | China-Government                           |
    |------------------|--------------------------------------------|--------------------------------------------|
    | Yönetim düzlemi | `https://management.azure.com/`              | `https://management.chinacloudapi.cn/`       |
    | Veri düzlemi       | `https://{location}.experiments.azureml.net` | `https://{location}.experiments.ml.azure.cn` |
    | Azure Active Directory              | `https://login.microsoftonline.com`          | `https://login.chinacloudapi.cn`             |

* Ortak verilere erişmesi gerekiyorsa örnek Not defteri çalışmayabilir.
* IP adresi aralıkları: [VNET Zorlamalı tünel](how-to-secure-training-vnet.md#forced-tunneling) YÖNERGELERINDE kullanılan CLı komutu IP aralıklarını döndürmez. Bunun yerine [Azure Çin Için Azure IP aralıklarını ve hizmet etiketlerini](https://www.microsoft.com//download/details.aspx?id=57062) kullanın.
* Azure Machine Learning işlem örnekleri önizlemesi, şu anda özel bağlantının etkinleştirildiği bir çalışma alanında desteklenmez, ancak CI 'nin tüm AML bölgelerine hizmet genişletmesi için bir sonraki dağıtımda desteklenecek.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning 'in kullanılabildiği bölgeler hakkında daha fazla bilgi edinmek için bkz. [bölgelere göre ürünler](https://azure.microsoft.com/global-infrastructure/services/).
