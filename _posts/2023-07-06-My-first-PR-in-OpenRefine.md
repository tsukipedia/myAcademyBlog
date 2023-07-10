---
category: Logs
title: Open Source Software - OpenRefine
date: 2023-07-06
---

# OVERVIEW
In my contribution to the OpenRefine project, I addressed an issue related to the reconciliation feature. OpenRefine is an open source tool designed to handle messy data effectively. One of its key functionalities is the ability to perform reconciliation on datasets. The problem I tackled involved the lack of validation when adding a reconciliation service, which resulted in the possibility of duplicate services being added to the list. In this technical log, I will outline the steps taken to resolve this issue and provide insight into the problem-solving process.

# CONTEXT
[https://openrefine.org] [https://github.com/OpenRefine/OpenRefine/issues/5926]

OpenRefine is a powerful tool used to work with data that may be disorganized or contain inconsistencies. One of its standout features is the reconciliation functionality, which allows users to match data against external sources to improve data quality. This feature enables users to add various reconciliation services to their datasets.

The specific issue I addressed was related to the reconciliation dialog in OpenRefine. When adding a new reconciliation service, the system did not check if the service being added already existed in the list. This oversight led to the possibility of duplicate services being included, which could cause confusion and potential errors in data reconciliation.

To reproduce the problem, one would simply add a reconciliation service and then attempt to add the same service again. The result was the service being duplicated in the list, contrary to the expected behavior where duplicates should be prevented.

To address this issue, I implemented a solution that ensured the reconciliation service list would not contain any duplicates. I will outline the steps taken to resolve this problem and describe the modifications made to the codebase of OpenRefine.

# SOLUTION
[https://github.com/OpenRefine/OpenRefine/pull/5964]

Since this project was entirely new to me, I started by watching tutorials on how to use the product in order to understand its functionality. Additionally, I familiarized myself with the developer guide, which provided instructions for installing, running, and testing the project. Once I gained a general understanding of the product's purpose, I began looking for an issue labeled 'good first issue' from the project's Git repository.

![](/docs/assets/openrefine0.png)

During my investigation of the issue, I conducted multiple searches in the project's file explorer, trying different strings. After several attempts, I struck gold when I searched for the `standardServices` array. This search yielded a manageable number of results, including the file I was looking for: `recon-manager.js`.

![](/docs/assets/openrefine1.png)

Upon examining this file, I discovered a function called `registerStandardService`, which seemed to be responsible for adding reconciliation services without validating if the service URL was already registered. To confirm my hypothesis, I added a `console.log("<3")` statement inside the function and reproduced the issue. It was immensely satisfying to finally see that heart symbol printed in the browser's console.

Implementing the necessary validation was straightforward because the `ReconciliationManager` class already had an array that stored all the service URLs. I added the validation as a guard clause at the beginning of the function and printed an error message, following the same standard used in another validation within the same function.

``` js
ReconciliationManager.registerStandardService = function(url, f, silent) {

+  if (ReconciliationManager._urlMap.hasOwnProperty(url)) {
+    if (!silent) {
+      alert($.i18n('core-recon/url-already-registered'));
+    }
+    return;
+  }

  var dismissBusy = function() {};
  if (!silent) {
    dismissBusy =  DialogSystem.showBusy($.i18n('core-recon/contact-service')+"...");
  }

  // ...

};
```

Lastly, since the problem required displaying a message in the graphical user interface (GUI), I made sure to add the message to all the corresponding translation files.
