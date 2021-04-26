---
title: Azure Machine Learning için git tümleştirmesi
titleSuffix: Azure Machine Learning
description: Azure Machine Learning, bir eğitim çalıştırmasının parçası olarak depoyu, dalı ve geçerli kayıt bilgilerini izlemek için yerel bir git deposu ile nasıl tümleştireceğinizi öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 04/08/2021
ms.openlocfilehash: 60dca43f95b190791c8fb593042ed612340a3af5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874558"
---
# <a name="git-integration-for-azure-machine-learning"></a>Azure Machine Learning için git tümleştirmesi

[Git](https://git-scm.com/) , projelerinize paylaşmanıza ve işbirliği yapmanıza olanak sağlayan popüler bir sürüm denetim sistemidir. 

İşleri izlemek için Git depolarını tamamen destekler. depoları doğrudan paylaşılan çalışma alanı dosya sisteminize kopyalayabilir, yerel iş istasyonunuzda git kullanabilir veya bir CI/CD işlem hattından git kullanabilirsiniz. Azure Machine Learning

Azure Machine Learning bir iş gönderilirken, kaynak dosyalar yerel bir git deposunda depolanıyorsa, eğitim sürecinin bir parçası olarak depo hakkında bilgi izlenir.

Azure Machine Learning, yerel bir git deposundan bilgileri izlediğinden, belirli bir merkezi depoya bağlı değildir. Deponuz GitHub, GitLab, Bitbucket, Azure DevOps veya başka bir git uyumlu hizmetten kopyalanabilir.

> [!TIP]
> Grafik kullanıcı arabiriminden git ile etkileşim kurmak için Visual Studio Code kullanın. Visual Studio Code kullanarak Azure Machine Learning uzak bir işlem örneğine bağlanmak için bkz. [Visual Studio Code 'de Azure Machine Learning işlem örneğine bağlanma (Önizleme)](how-to-set-up-vs-code-remote.md)
>
> Visual Studio Code sürüm denetimi özellikleri hakkında daha fazla bilgi için bkz. [vs Code sürüm denetimini kullanma](https://code.visualstudio.com/docs/editor/versioncontrol) ve [vs Code GitHub ile çalışma](https://code.visualstudio.com/docs/editor/github).

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Git depolarını çalışma alanı dosya sisteminize kopyalama
Azure Machine Learning, çalışma alanındaki tüm kullanıcılar için paylaşılan bir dosya sistemi sağlar.
Bir Git deposunu bu dosya paylaşımında kopyalamak için bir [Terminal açmak](how-to-access-terminal.md)& bir işlem örneği oluşturmanız önerilir.
Terminal açıldıktan sonra tam bir git istemcisine erişiminiz olur ve git CLı deneyimi aracılığıyla Git 'i kopyalayabilir ve bunlarla çalışabilirsiniz.

Başkalarının doğrudan çalışma dalınızda çakışmalar yapamaması için depoyu kullanıcılar dizininize kopyalamanız önerilir.

Kimlik doğrulayabilecek herhangi bir Git deposunu (GitHub, Azure Repos, BitBucket vb.) kopyalayabilirsiniz.

Kopyalama hakkında daha fazla bilgi için bkz. [GIT CLI kullanma](https://guides.github.com/introduction/git-handbook/)Kılavuzu.

## <a name="authenticate-your-git-account-with-ssh"></a>SSH ile git hesabınızın kimliğini doğrulama
### <a name="generate-a-new-ssh-key"></a>Yeni bir SSH anahtarı oluştur
1) Azure Machine Learning Not Defteri sekmesinde [Terminal penceresini açın](./how-to-access-terminal.md) .

2) Aşağıdaki metni e-posta adresinizdeki yerine yapıştırın.

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Bu, belirtilen e-postayı etiket olarak kullanarak yeni bir SSH anahtarı oluşturur.

```
> Generating public/private rsa key pair.
```

3) "Anahtarı kaydedeceğiniz dosyayı girmeniz" istendiğinde ENTER tuşuna basın. Bu, varsayılan dosya konumunu kabul eder.

4) Varsayılan konumun '/Home/azureuser/.SSH ' olduğunu doğrulayın ve ENTER 'a basın. Aksi takdirde '/Home/azureuser/.SSH ' konumunu belirtin.

> [!TIP]
> SSH anahtarının '/Home/azureuser/.SSH ' içine kaydedildiğinden emin olun. Bu dosya işlem örneğine kaydedilir ve yalnızca Işlem örneği sahibi tarafından erişilebilir

```
> Enter a file in which to save the key (/home/azureuser/.ssh/id_rsa): [Press enter]
```

5) İstemde, güvenli bir parola yazın. Ek güvenlik için SSH anahtarınıza bir parola eklemenizi öneririz

```
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

### <a name="add-the-public-key-to-git-account"></a>Git hesabına ortak anahtar ekleme
1) Terminal pencerenizde, ortak anahtar dosyanızın içeriğini kopyalayın. Anahtarı yeniden adlandırdıysanız, id_rsa. pub öğesini ortak anahtar dosyası adıyla değiştirin.

```bash
cat ~/.ssh/id_rsa.pub
```
> [!TIP]
> **Terminalde Kopyala ve Yapıştır**
> * Windows: `Ctrl-Insert` kopyalayıp `Ctrl-Shift-v` yapıştırmak için veya kullanın `Shift-Insert` .
> * Mac OS: `Cmd-c` kopyalamak ve `Cmd-v` yapıştırmak için.
> * FireFox/IE Pano izinlerini düzgün şekilde desteklemiyor olabilir.

2) Panodaki anahtar çıkışını seçin ve kopyalayın.

+ [GitHub](https://docs.github.com/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

+ [GitLab](https://docs.gitlab.com/ee/ssh/#adding-an-ssh-key-to-your-gitlab-account)

+ [Azure DevOps](/azure/devops/repos/git/use-ssh-keys-to-authenticate#step-2--add-the-public-key-to-azure-devops-servicestfs)  2. **adımda** başlayın.

+ [Bitbucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/#SetupanSSHkey-ssh2). **Adım 4**' te başlayın.

### <a name="clone-the-git-repository-with-ssh"></a>Git deposunu SSH ile kopyalama

1) SSH git kopyası URL 'sini git deposundan kopyalayın.

2) `git clone`SSH git deposu URL 'nizi kullanmak için aşağıdaki komuta URL 'yi yapıştırın. Bu, şöyle bir şey arayacaktır:

```bash
git clone git@example.com:GitUser/azureml-example.git
Cloning into 'azureml-example'...
```

Şöyle bir yanıt göreceksiniz:

```bash
The authenticity of host 'example.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
```

SSH, sunucunun SSH parmak izini görüntüleyebilir ve doğrulamanızı ister. Görüntülenen parmak izinin, SSH ortak anahtarları sayfasındaki parmak izlerinden biriyle eşleştiğini doğrulamanız gerekir.

SSH, bu parmak izini, bilinmeyen bir konağa bağlanırken [ortadaki adam saldırılarına](/previous-versions/windows/it-pro/windows-2000-server/cc959354(v=technet.10))karşı korumak için görüntüler. Konağın parmak izini kabul ettikten sonra, parmak izi değişmediği takdirde SSH sizi yeniden istemez.

3) Bağlanmaya devam etmek isteyip istemediğiniz sorulduğunda, yazın `yes` . Git, depoyu kopyalar ve sonraki git komutları için SSH ile bağlantı kurmak üzere uzak kaynağı ayarlar.

## <a name="track-code-that-comes-from-git-repositories"></a>Git depolarından gelen kodu izleme

Python SDK 'dan veya CLı Machine Learning bir eğitim çalıştırması gönderdiğinizde, modeli eğitmek için gereken dosyalar çalışma alanınıza yüklenir. `git`Komut geliştirme ortamınızda kullanılabiliyorsa, karşıya yükleme işlemi, dosyaların bir git deposunda saklanıp saklanmadığını denetlemek için onu kullanır. Bu durumda, git deponuzdaki bilgiler, eğitim çalıştırmasının bir parçası olarak da yüklenir. Bu bilgiler, eğitim çalışması için aşağıdaki özelliklerde depolanır:

| Özellik | Değeri almak için kullanılan git komutu | Description |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | Deponuzdan kopyalanan URI. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | Deponuzdan kopyalanan URI. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Çalışma gönderildiğinde etkin dal. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Çalışma gönderildiğinde etkin dal. |
| `azureml.git.commit` | `git rev-parse HEAD` | Çalıştırma için gönderilen kodun COMMIT karması. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | Çalıştırma için gönderilen kodun COMMIT karması. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`dal/COMMIT kirli ise; Aksi takdirde, `false` . |

Bu bilgiler, bir Estimator, Machine Learning işlem hattı veya betik çalıştırması kullanan çalıştırmalar için gönderilir.

Eğitim dosyalarınız geliştirme ortamınızda bir git deposunda bulunmuyorsa veya `git` komut kullanılabilir değilse, git ile ilgili bilgiler izlenmez.

> [!TIP]
> Git komutunun geliştirme ortamınızda kullanılabilir olup olmadığını denetlemek için bir kabuk oturumu, komut istemi, PowerShell veya başka bir komut satırı arabirimi açın ve aşağıdaki komutu yazın:
>
> ```
> git --version
> ```
>
> Yüklüyse ve yolunda şuna benzer bir yanıt alırsınız `git version 2.4.1` . Geliştirme ortamınıza git 'i yükleme hakkında daha fazla bilgi için [Git Web sitesine](https://git-scm.com/)bakın.

## <a name="view-the-logged-information"></a>Günlüğe kaydedilen bilgileri görüntüleyin

Git bilgileri bir eğitim çalışmasının özelliklerinde depolanır. Azure portal, Python SDK ve CLı kullanarak bu bilgileri görüntüleyebilirsiniz. 

### <a name="azure-portal"></a>Azure portalı

1. [Studio portalından](https://ml.azure.com), çalışma alanınızı seçin.
1. __Denemeleri__ öğesini seçin ve denemeleri birini seçin.
1. __Çalıştırma numarası__ sütunundan çalıştırmaların birini seçin.
1. __Çıktılar + Günlükler__' i seçin ve ardından __Günlükler__ ve __azureml__ girdilerini genişletin. __### \_ Azure__ ile başlayan bağlantıyı seçin.

Günlüğe kaydedilen bilgiler aşağıdaki JSON 'a benzer bir metin içerir:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK'sı

Bir eğitim çalıştırması gönderdikten sonra bir [çalıştırma](/python/api/azureml-core/azureml.core.run%28class%29) nesnesi döndürülür. `properties`Bu nesnenin özniteliği, günlüğe kaydedilen git bilgilerini içerir. Örneğin, aşağıdaki kod, COMMIT karmasını alır:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

`az ml run`CLI komutu, bir çalıştırmanın özelliklerini almak için kullanılabilir. Örneğin, aşağıdaki komut adlı deneyde son çalıştırmanın özelliklerini döndürür `train-on-amlcompute` :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Daha fazla bilgi için, [az ml Run](/cli/azure/ml/run) Reference belgelerine bakın.

## <a name="next-steps"></a>Sonraki adımlar

* [Model eğitimi için işlem hedeflerini kullanma](how-to-set-up-training-targets.md)