---
title: Azure Pipelines Team Data Science Işlemiyle bir CI/CD Işlem hattı oluşturma
description: Docker ve Kubernetes kullanarak yapay zeka (AI) uygulamaları için sürekli tümleştirme ve sürekli teslim işlem hattı oluşturun.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: af7900faff18d526686b80f23b9de3b3bce5ec5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93309613"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>Azure Pipelines, Docker ve Kubernetes kullanarak AI uygulamaları için CI/CD işlem hatları oluşturma

Yapay zeka (AI) uygulaması, önceden eğitilen makine öğrenimi (ML) modeliyle eklenmiş uygulama kodudur. Bir AI uygulaması için her zaman iki iş akışı vardır: veri bilimcileri, ML modelini oluşturur ve uygulama geliştiricileri uygulamayı oluşturup son kullanıcılara tüketmek üzere kullanıma sunar. Bu makalede, ML modelini uygulama kaynak koduna katıştıran bir AI uygulaması için sürekli tümleştirme ve sürekli teslim (CI/CD) işlem hattının nasıl uygulanacağı açıklanır. Örnek kod ve öğretici bir Python Flask Web uygulaması kullanır ve özel bir Azure Blob depolama hesabından önceden eğitilen bir model getirir. AWS S3 depolama hesabı da kullanabilirsiniz.

> [!NOTE]
> Aşağıdaki işlem, CI/CD yapmak için çeşitli yollarla biridir. Bu araç ve önkoşulların alternatifleri vardır.

## <a name="source-code-tutorial-and-prerequisites"></a>Kaynak kodu, öğretici ve Önkoşullar

GitHub 'dan [kaynak kodu](https://github.com/Azure/DevOps-For-AI-Apps) ve ayrıntılı bir [öğretici](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) indirebilirsiniz. Kendi uygulamanız için bir CI/CD işlem hattı uygulamak için öğretici adımlarını izleyin.

İndirilen kaynak kodu ve öğreticiyi kullanmak için aşağıdaki önkoşullara sahip olmanız gerekir: 

- GitHub hesabınıza yönelik [kaynak kodu deposu](https://github.com/Azure/DevOps-For-AI-Apps)
- [Azure DevOps organizasyonu](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Azure CLI](/cli/azure/install-azure-cli)
- [Kubernetes (AKS) kümesi için bir Azure Container Service](/previous-versions/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) komutları çalıştırmak ve aks kümesinden yapılandırma getirmek için 
- [Azure Container Registry (ACR) hesabı](../../container-registry/container-registry-get-started-portal.md)

## <a name="cicd-pipeline-summary"></a>CI/CD işlem hattı Özeti

Her yeni git işleme, derleme işlem hattının dışına çıkarılan. Derleme, en son ML modelini bir BLOB depolama hesabından güvenli bir şekilde çeker ve uygulama kodu ile tek bir kapsayıcıda paketler. Uygulama geliştirme ve veri bilimi iş akışlarının bu şekilde ayrılması, üretim uygulamasının her zaman en son ML modeliyle en son kodu çalıştırıyor olmasını sağlar. Uygulama testi geçerse, işlem hattı, yapı görüntüsünü ACR 'teki bir Docker kapsayıcısında güvenli bir şekilde depolar. Yayın ardışık düzeni daha sonra AKS kullanarak kapsayıcıyı dağıtır. 

## <a name="cicd-pipeline-steps"></a>CI/CD işlem hattı adımları

Aşağıdaki diyagram ve adımlar, CI/CD ardışık düzen mimarisini anlatmaktadır:

![CI/CD işlem hattı mimarisi](./media/ci-cd-flask/architecture.png)

1. Geliştiriciler, kendi tercih ettikleri IDE 'de uygulama kodu üzerinde çalışır.
2. Geliştiriciler kodu Azure Repos, GitHub veya başka bir git kaynak denetimi sağlayıcısına teslim. 
3. Ayrıca, veri bilimcileri, ML modellerini geliştirmeye çalışır.
4. Veri bilimcileri, tamamlanmış modeli bir model deposuna yayımlayıp bu durumda bir BLOB depolama hesabı. 
5. Git işlemesini temel alan bir derlemeyi Azure Pipelines.
6. Derleme işlem hattı, blob depolamadan en son ML modelini çeker ve bir kapsayıcı oluşturur.
7. İşlem hattı, yapı görüntüsünü ACR 'teki özel görüntü deposuna iter.
8. Yayın ardışık düzeni, başarılı yapıya göre devre dışı bırakır.
9. İşlem hattı, ACR 'den en son görüntüyü çeker ve AKS 'deki Kubernetes kümesi arasında dağıtır.
10. Uygulama için Kullanıcı istekleri, DNS sunucusundan geçer.
11. DNS sunucusu istekleri bir yük dengeleyiciye geçirir ve yanıtları kullanıcılara geri gönderir.

## <a name="see-also"></a>Ayrıca bkz.

- [Ekip veri bilimi Işlemi (TDSP)](./index.yml)
- [Azure Machine Learning (AML)](../index.yml)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Azure Kubernetes Services (AKS)](../../aks/intro-kubernetes.md)