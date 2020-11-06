---
layout: post
title: "How to use the VSCode local storage API"
categories: [Projects, Technology]
tags: [Typescript, VSCode, VSCode Extension]
excerpt: "<p>The VSCode key-value storage is a powerful but under-documented API. I will show you how to use this feature to store project or extension-wide data.</p>"
---

## Problem Description

> I want to read and write Workspace or global data to a durable storage, that is accessible only to my extension.

This was not always as straight-forward as it is now. In the past you would need to implement local storage using custom files written to temporary storage such as %APP_DATA%. There are three problems with this approach.

1. Using %APP_DATA% is often locked down.
2. Files stored in %APP_DATA% can be deleted at any time (sometimes nightly) by IT policy.
3. There is no guarantee that you have, or will continue to have, permissions to the filesystem.

This can cause inconsistency [issues](https://github.com/chrishasz/spgo/issues/71){:target="_blank"} in how extensions behave.

## Solution

The VS Code API contains a [Memento](https://code.visualstudio.com/api/references/vscode-api#Memento){:target="_blank"} object for this.

According to the documentation, `A memento represents a storage utility. It can store and retrieve values`. We can use this object as a guaranteed, durable storage for persisting state during and between VSCode Sessions.

When you activate an extension, VSCode passes an `ExtensionContext` object to your `activate` function. This object contains two `Memento` objects - a `workspaceState` property, which stores objects at the Workspace scope, and the `globalState` property, which stores objects at the Extension scope.

## Example: Creating a Generic Class to Read and Write Data to the Local Store

You can create a simple class for storing and retrieving data from the `ExtensionContext` by wrapping either of the State objects in a class. By wrapping the State object in a class, you can implement extension-specific custom formatters, or encrypting/hashing data.

{% gist d39ee076a863165d1696581a0eccb97a %}

Once you have created this class, you can use it to write data to and from the local storage object, scoped to either the *Workspace* or the *Extension*.

### Initialize localStorageService with Workspace Scope

*in file: _extension.ts_

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

### Initialize localStorageService with Extension Scope

*in file: _extension.ts_

```typescript
export function activate(context: vscode.ExtensionContext): any {

  //Create your objects - Needs to be a well-formed JSON object.
  let someObject : ISomeObject = null;
  let someOtherObject : ISomeObject = new SomeObject();

  //Initialize the global application manager
  let storageManager = new LocalStorageService(context.globalState);

  //Write your objects from the Workspace Store
  storageManager.setValue<ISomeObject>("SomeObject", someObject);

  //Read your objects to the Workspace Store
  someOtherObject = storageManager.getValue<ISomeObject>("SomeObject");
}
```

## Take-aways

the `Memento` class in the Visual Studio Code Extension API provides a guaranteed, durable storage for persisting state during and between VSCode Sessions.

If you wrap it in a simple helper class, you can implement custom data or security formatters to fine tune how you store data.

## References

* [VSCode Documentation](https://code.visualstudio.com/docs){:target="_blank"} - Documentation for Visual Studio Code.
* [Memento Class](https://code.visualstudio.com/api/references/vscode-api#Memento){:target="_blank"} - The Visual Studio Code Docs for the Memento Class.
* [chrishasz on GitHub](https://www.github.com/chrishasz){:target="_blank"} - My personal github site where the gists are hosted
