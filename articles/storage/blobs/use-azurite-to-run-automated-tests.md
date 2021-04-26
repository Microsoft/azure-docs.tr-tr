---
title: Azurite kullanarak otomatikleştirilmiş testleri çalıştırma
titleSuffix: Azure Storage
description: Azurite kullanarak Azure Blob depolama için özel uç noktalara yönelik otomatikleştirilmiş testlerin nasıl yazılacağını öğrenin.
services: storage
author: ikivanc
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 03/31/2021
ms.author: ikivanc
ms.openlocfilehash: c4e8a11e0c46cb9a138a1a66060d9fdcc72c192e
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112047"
---
# <a name="run-automated-tests-by-using-azurite"></a>Azurite kullanarak otomatikleştirilmiş testleri çalıştırma

Azurite depolama öykünücüsünü kullanarak Azure Blob depolama için özel uç noktalara yönelik otomatikleştirilmiş testlerin nasıl yazılacağını öğrenin.

## <a name="run-tests-on-your-local-machine"></a>Yerel makinenizde testleri çalıştırma

1. [Python](https://www.python.org/) 'un en son sürümünü yükler

1. [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) yüklensin

1. [Azurite](../common/storage-use-azurite.md)'yi yükleyip çalıştırın:

   **Seçenek 1:** NPM kullanarak Azurite 'yi yükleyip yerel olarak çalıştırın

   ```bash
   # Install Azurite
   npm install -g azurite
   
   # Create an Azurite directory
   mkdir c:/azurite
   
   # Launch Azurite locally
   azurite --silent --location c:\azurite --debug c:\azurite\debug.log
   ```

   **Seçenek 2:** Azurite çalıştırmak için Docker 'ı kullanma

   ```bash
   docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite azurite-blob --blobHost 0.0.0.0
   ```

1. Azure Depolama Gezgini, **Yerel öykünücüye Ekle** ' yi seçin

    :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection.png" alt-text="Azure depolama kaynağına bağlanan Azure Depolama Gezgini ekran görüntüsü.":::

1. Azurıite bağlamak için bir **görünen ad** ve **Bloblar bağlantı noktası** numarası girin ve yerel blob depolamayı yönetmek için Azure Depolama Gezgini kullanın.

   :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection-attach.png" alt-text="Yerel öykünücüye ekleme Azure Depolama Gezgini ekran görüntüsü.":::

1. Sanal Python ortamı oluşturma

   ```bash
   python -m venv .venv
   ```

1. Bir kapsayıcı oluşturun ve ortam değişkenlerini başlatın. Testler oluşturmak için bir [Pytest](https://docs.pytest.org/) [conftest.py](https://docs.pytest.org/en/2.1.0/plugins.html) dosyası kullanın. Aşağıda bir conftest.py dosyası örneği verilmiştir:

   ```python
   from azure.storage.blob import BlobServiceClient
   import os

   def pytest_generate_tests(metafunc):
      os.environ['AZURE_STORAGE_CONNECTION_STRING'] = 'DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;'
      os.environ['STORAGE_CONTAINER'] = 'test-container'

      # Create a container for Azurite for the first run
      blob_service_client = BlobServiceClient.from_connection_string(os.environ.get("AZURE_STORAGE_CONNECTION_STRING"))
      try:
         blob_service_client.create_container(os.environ.get("STORAGE_CONTAINER"))
      except Exception as e:
         print(e)
   ```

   > [!NOTE]
   > İçin gösterilen değer `AZURE_STORAGE_CONNECTION_STRING` Azurite için varsayılan değerdir, özel bir anahtar değildir.

1. [requirements.txt](https://github.com/Azure-Samples/automated-testing-with-azurite/blob/main/requirements.txt) dosyasında listelenen bağımlılıkları yükler

   ```bash
   pip install -r requirements.txt
   ```

1. Testleri Çalıştır:

   ```bash
   python -m pytest ./tests
   ```

Testleri çalıştırdıktan sonra, Azure Depolama Gezgini kullanarak Azurite blob depolamadaki dosyaları görebilirsiniz.

:::image type="content" source="media/use-azurite-to-run-automated-tests/http-local-blob-storage.png" alt-text="Testler tarafından oluşturulan dosyaları gösteren Azure Depolama Gezgini ekran görüntüsü.":::

## <a name="run-tests-on-azure-pipelines"></a>Azure Pipelines testleri çalıştırma

Testleri yerel olarak çalıştırdıktan sonra, testlerin [Azure Pipelines](/azure/devops/pipelines)geçirdiğinizden emin olun. Azure 'da barındırılan bir aracı olarak Docker Azurite görüntüsünü kullanın veya Azurite yüklemek için NPM kullanın. Aşağıdaki Azure Pipelines örnek, Azurite yüklemek için NPM kullanır.
  
```yaml
trigger:
- master

steps:
- task: UsePythonVersion@0
  displayName: 'Use Python 3.7'
  inputs:
    versionSpec: 3.7

- bash: |
    pip install -r requirements_tests.txt
  displayName: 'Setup requirements for tests'
  
- bash: |
    sudo npm install -g azurite
    sudo mkdir azurite
    sudo azurite --silent --location azurite --debug azurite\debug.log &
  displayName: 'Install and Run Azurite'

- bash: |
    python -m pytest --junit-xml=unit_tests_report.xml --cov=tests --cov-report=html --cov-report=xml ./tests
  displayName: 'Run Tests'

- task: PublishCodeCoverageResults@1
  inputs:
    codeCoverageTool: Cobertura
    summaryFileLocation: '$(System.DefaultWorkingDirectory)/**/coverage.xml'
    reportDirectory: '$(System.DefaultWorkingDirectory)/**/htmlcov'

- task: PublishTestResults@2
  inputs:
    testResultsFormat: 'JUnit'
    testResultsFiles: '**/*_tests_report.xml'
    failTaskOnFailedTests: true
```

Azure Pipelines testlerini çalıştırdıktan sonra aşağıdakine benzer bir çıktı görmeniz gerekir:

:::image type="content" source="media/use-azurite-to-run-automated-tests/azure-pipeline.png" alt-text="Test sonuçlarının Azure Pipelines ekran görüntüsü.":::

## <a name="next-steps"></a>Sonraki adımlar

- [Yerel Azure depolama geliştirmesi için Azurite öykünücüsünü kullanma](../common/storage-use-azurite.md)
- [Örnek: Azure DevOps ardışık düzeninde BLOB depolama testlerini çalıştırmak için Azurite kullanma](https://github.com/Azure-Samples/automated-testing-with-azurite)
