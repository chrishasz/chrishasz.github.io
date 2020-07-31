---
layout: post
title: "How to use the VSCode local storage API"
categories: [Projects, Technology]
tags: [Typescript, VSCode, VSCode Extension]
excerpt: "<p>The VSCode key-value storage is a powerful but under-documented API. Learn how to use it to store project or extension-wide data.</p>"
---

## Problem Description

> I want to store Workspace or global data in durable storage, that is accessible only to my extension.

using appdata is often locked down

using temp storage means files can be deleted at any time (sometimes nightly) by IT policy

no guarantee that you have permissions to the filesystem

## Solution

The [Memento](https://code.visualstudio.com/api/references/vscode-api#Memento) object.

added in vsCode version xxxx

## Example: Creating a Generic Class to Read and Write

Here is a simple class for managing storing and retrieving

{% gist d39ee076a863165d1696581a0eccb97a %}

### Initialize localStorageService with Workspace Scope

_in extension.ts_

```typescript
export function activate(context: vscode.ExtensionContext): any {

  //Create your objects - Needs to be a well-formed JSON object.
  let someObject : ISomeObject = null;
  let someOtherObject : ISomeObject = new SomeObject();

  //Initialize the global application manager
  let storageManager = new LocalStorageService(context.workspaceState);

  //Write your objects from the Workspace Store
  storageManager.setValue<ISomeObject>("SomeObject", someObject);

  //Read your objects to the Workspace Store
  someOtherObject = storageManager.getValue<ISomeObject>("SomeObject");

}
```

### Initialize localStorageService with Addin Scope

_in extension.ts_

```typescript
export function activate(context: vscode.ExtensionContext): any {

  //Create your objects - Needs to be a well-formed JSON object.
  let someObject : ISomeObject = null;
  let someOtherObject : ISomeObject = new SomeObject();

  //Initialize the global application manager
  let storageManager = new LocalStorageService(context.workspaceState);

  //Write your objects from the Workspace Store
  storageManager.setValue<ISomeObject>("SomeObject", someObject);

  //Read your objects to the Workspace Store
  someOtherObject = storageManager.getValue<ISomeObject>("SomeObject");
}
```

## Summary and Take-aways

the

## References

* [VSCode Documentation](https://code.visualstudio.com/docs) - Documentation for Visual Studio Code.
* [chrishasz on GitHub](https://www.github.com/chrishasz) - My personal github site where the gists are hosted
