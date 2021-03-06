---
title: Deploy a VM with securely stored password on Azure Stack | Microsoft Docs
description: Learn how to deploy a VM using a password stored in Azure Stack Key Vault
services: azure-stack
documentationcenter: 
author: rlfmendes
manager: natmack
editor: 
ms.assetid: 23322a49-fb7e-4dc2-8d0e-43de8cd41f80
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/23/2017
ms.author: ricardom
translationtype: Human Translation
ms.sourcegitcommit: a3a8b9ff522369ed0f03817585e1666507c95c6e
ms.openlocfilehash: fe44b7c36b1527b6008e20e6f29c41d662222a1e


---
# <a name="deploy-a-vm-by-retrieving-the-password-stored-in-key-vault"></a>Deploy a VM by retrieving the password stored in Key Vault
When you need to pass a secure value (like a password) as a parameter during deployment, you can store that value as a secret in an Azure Stack key vault and reference the value in other Azure Resource Manager templates. You include only a reference to the secret in your template so the secret is never exposed. You do not need to manually enter the value for the secret each time you deploy the resources. You specify which users or service principals can access the secret.

## <a name="reference-a-secret-with-static-id"></a>Reference a secret with static ID
You reference the secret from within a parameters file, which passes values to your template. You reference the secret by passing the resource identifier of the key vault and the name of the secret. In this example, the key vault secret must already exist. You use a static value for its resource ID.

    "parameters": {
    "adminPassword": {
    "reference": {
    "keyVault": {
    "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
    },
    "secretName": "sqlAdminPassword"


> [!NOTE]
> The parameter that accepts the secret should be a *securestring*.
> 
> 

## <a name="next-steps"></a>Next Steps
[Deploy a sample app with Key Vault](azure-stack-kv-sample-app.md)

[Deploy a VM with a Key Vault certificate](azure-stack-kv-push-secret-into-vm.md)




<!--HONumber=Jan17_HO4-->


