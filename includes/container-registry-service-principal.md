---
title: 包含檔案
description: 包含檔案
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 04/23/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 6ed114ea6162c9d4888b6f86998cfb422a3944e8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
---
## <a name="create-a-service-principal"></a>建立服務主體

若要建立具容器登錄存取權的服務主體，可以使用以下指令碼。 使用您容器登錄的名稱更新 `ACR_NAME` 變數，並可選擇使用 [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] 命令中的 `--role` 值來授與不同權限。 您必須已安裝 [Azure CLI](/cli/azure/install-azure-cli)，才能使用這個指令碼。

執行指令碼之後，請記下的服務主體的**識別碼**和**密碼**。 一旦擁有其認證，便可以將您的應用程式和服務設定為以服務主體向您的容器登錄進行驗證。

[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>使用現有的服務主體

若要授與現有服務主體登錄存取權，您必須為服務主體指派新的角色。 如同建立新的服務主體，您可以授與提取、推送和提取，以及擁有者存取權。

以下指令碼使用 [az role assignment create][az-role-assignment-create] 命令，以授與您在 `SERVICE_PRINCIPAL_ID` 變數中所指定的服務主體「提取」權限。 如果您要授與不同層級的存取權，請調整 `--role` 值。

[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
