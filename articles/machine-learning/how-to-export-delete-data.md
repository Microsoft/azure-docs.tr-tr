---
title: Çalışma alanı verilerini dışarı aktarma veya silme
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Studio, CLı, SDK ve kimliği doğrulanmış REST API 'Leri ile çalışma alanınızı dışarı aktarmayı veya silmeyi öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 04/24/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 030e09f0bb1514e8d3828da52604d751a2a8a11a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520106"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Machine Learning hizmeti çalışma alanı verilerinizi dışarı veya silme

Azure Machine Learning, Portal 'ın grafik arabirimini veya Python SDK 'sını kullanarak çalışma alanı verilerinizi dışarı aktarabilir veya silebilirsiniz. Bu makalede her iki seçenek de açıklanmaktadır.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Çalışma alanı verilerinizi denetleme

Azure Machine Learning tarafından depolanan ürün içi veriler dışarı ve silme için kullanılabilir. Azure Machine Learning Studio, CLı ve SDK kullanarak dışa aktarabilir ve silebilirsiniz. Telemetri verilerine Azure Gizlilik portalı üzerinden erişilebilir. 

Azure Machine Learning, kişisel veriler çalışma geçmişi belgelerindeki Kullanıcı bilgilerinden oluşur. 

## <a name="delete-high-level-resources-using-the-portal"></a>Portalı kullanarak üst düzey kaynakları silme

Bir çalışma alanı oluşturduğunuzda, Azure Kaynak grubu içinde bir dizi kaynak oluşturur:

- Çalışma alanının kendisi
- Bir depolama hesabı
- Bir kapsayıcı kayıt defteri
- Bir Applications Insights örneği
- Bir Anahtar Kasası

Bu kaynaklar listeden seçip **Sil** ' i seçerek silinebilir. 

:::image type="content" source="media/how-to-export-delete-data/delete-resource-group-resources.png" alt-text="Silme simgesi vurgulanmış şekilde portalın ekran görüntüsü":::

Kişisel Kullanıcı bilgilerini içerebilen çalışma geçmişi belgeleri, alt klasörlerinde BLOB depolama alanındaki depolama hesabında depolanır `/azureml` . Portaldan verileri indirebilir ve silebilirsiniz.

:::image type="content" source="media/how-to-export-delete-data/storage-account-folders.png" alt-text="Portal içinde depolama hesabındaki azureml dizininin ekran görüntüsü":::

## <a name="export-and-delete-machine-learning-resources-using-azure-machine-learning-studio"></a>Azure Machine Learning Studio kullanarak makine öğrenimi kaynaklarını dışarı ve silme

Azure Machine Learning Studio, dizüstü bilgisayar öğrenimi kaynaklarınızın Not defterleri, veri kümeleri, modeller ve denemeleri gibi Birleşik bir görünümünü sağlar. Azure Machine Learning Studio, verilerinizin ve denemeleri bir kaydını koruyan vurgular. İşlem hatları ve işlem kaynakları gibi hesaplama kaynakları tarayıcı kullanılarak silinebilir. Bu kaynaklar için, söz konusu kaynağa gidin ve **Sil**' i seçin. 

Veri kümelerinin kaydı silinebilir ve denemeleri arşivlenebilir, ancak bu işlemler verileri silmez. Verileri tamamen kaldırmak için, veri kümeleri ve çalışma verileri depolama düzeyinde silinmelidir. Depolama düzeyinde silme, daha önce açıklandığı gibi portal kullanılarak yapılır.

Studio 'Yu kullanarak deneysel çalışmalardan eğitim yapıtları indirebilirsiniz. İlgilendiğiniz **deneyi** ve **çalıştırmayı** seçin. **Çıkış + Günlükler** ' i seçin ve indirmek istediğiniz belirli yapıtlara gidin. Seç **..** . ve **İndir**.

İstenen **modele** gidip **İndir**' i seçerek kayıtlı bir modeli indirebilirsiniz. 

:::image type="contents" source="media/how-to-export-delete-data/model-download.png" alt-text="İndirme seçeneğinin vurgulandığı Studio model sayfasının ekran görüntüsü":::

## <a name="export-and-delete-resources-using-the-python-sdk"></a>Python SDK 'sını kullanarak kaynakları dışarı ve silme

Kullanarak belirli bir çalıştırmanın çıkışlarını indirebilirsiniz: 

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['my-experiment']
run = next(run for run in ex.get_runs() if run.id == run_id)
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

Aşağıdaki makine öğrenimi kaynakları, Python SDK kullanılarak silinebilir: 

| Tür | İşlev çağrısı | Notlar | 
| --- | --- | --- |
| `Workspace` | [`delete`](/python/api/azureml-core/azureml.core.workspace.workspace#delete-delete-dependent-resources-false--no-wait-false-) | `delete-dependent-resources`Silmeyi basamaklandırmak için kullanın |
| `Model` | [`delete`](/python/api/azureml-core/azureml.core.model%28class%29#delete--) | | 
| `ComputeTarget` | [`delete`](/python/api/azureml-core/azureml.core.computetarget#delete--) | |
| `WebService` | [`delete`](/python/api/azureml-core/azureml.core.webservice%28class%29) | |