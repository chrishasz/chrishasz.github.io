---
layout: post
title: "How I Made It: The SPGo Promise Architecture"
categories: [Architecture, Projects]
tags: [Promise, Typescript, SPGo, VSCode Extension]
---

From the beginning, one of the non-negotiable goals I set for developing [SPGo](https://www.chrishasz.com/spgo/) is that any automated activity would be completely unobtrusive to the developer experience. For example, Publishing a file should not make the application unresponsive. Implementation and architecture are often overlooked components of user experience, which greatly affect end-user satisfaction and, ultimately, adoption.

For the majority of cases, this meant asynchronous task execution. How do you ensure asynchronous operation in your functional language? Promises.

## Overview: Promise Architecture

> (From Wikipedia) A **promise**[1](#citation1){:.superscript} is an object that acts as a proxy for a result that is initially unknown, usually because the computation of its value is not yet complete.

I want to break that definition down into two parts. The first part: _"a proxy for a result that is initially unknown"_ gives the application freedom to continue executing without having to wait for potentially long-running tasks, such as making a service call to a remote API, or performing an expensive background calculation. In practical terms, this means that VSCode will not be unresponsive while SPGo uploads your file is to SharePoint. This is where the User Experience component comes in to play. Nobody likes having an application halt while some background task runs (even if you display a spinner or progress bar).

Second, _"the computation of its value is not yet complete"_ tells us means that anything inside a promise has not happened yet. You must be OK that the code inside a promise will most likely not have been run by the time the next statement in your function is executed. If you are hoisting variables, or sharing scope between your parent execution block and your promise, expect them to behave in ways you do not expect (i.e. poorly). It is critical that the code executed within the promise does not share state with any other components of your application.

In short, a promise is way to execute a block of code in the background without it affecting execution of any foreground tasks.

## Anatomy of a Promise

### Promises in Typescript

Typescript added the `async` property as syntactical sugar to automatically have a function return a promise. An example of the `async` keyword

```typescript
async function foo() {
    return 'Hello World';
}

foo().then(value => {
  console.log(value);
});
```

This is functionally identical to the following code:

```typescript
function foo() {
    return new Promise( ()=>{
        return 'Hello World';
    });
}

foo().then(value => {
  console.log(value);
});
```

## VS Code Command Behavior

To better understand how an asynchronous architecture benefits SPGo, it is important to understand a little bit about the architecture of a VSCode Extension.

## The SPGo Implementation

The basic signature for an asynchronous method built on promises looks like this:

```typescript
function someFunction(message: string, action: Promise<any>): Promise<any>
```

### A concrete example from SPGo

In this common code block from SPGo, we call an asynchronous helper to display a message in the shell (window) ProgressBar while a promise is executed. You can see from this example that the promise contains two steps. First, verify the user's credentials, and second download files from the server. Finally, in the `catch` function, we handle any errors which occurred during the execution of any promise in the chain.

```typescript
return UiHelper.showStatusBarProgress('Populating workspace', AuthenticationService.verifyCredentials(vscode.window.spgo)
);
```

Let's break this down line by line:

```typescript
    .then(downloadFiles)
```

```typescript
    .catch(err => ErrorHelper.handleError(err))
```

## Architecture: The Promise Chain and Promise Queue

When implementing your promise-based architecture, there are two common approaches

When do you use one versus the other?

are there performance tradeoffs?

### Promise Chain

This is the most common approach to promise-based development.
*Example: publish Workspace* from SPGo [Here](https://github.com/chrishasz/spgo/blob/master/src/command/publishWorkspace.ts)

```typescript
'use strict';
import * as vscode from 'vscode';

import { Logger } from '../util/logger';
import { Constants } from './../constants';
import { UrlHelper } from '../util/urlHelper';
import { IPublishingAction } from './../spgo';
import { UiHelper } from './../util/uiHelper';
import { ErrorHelper } from '../util/errorHelper';
import { SPFileService } from './../service/spFileService';
import { AuthenticationService } from './../service/authenticationService';

export default function publishWorkspace() : Thenable<any> {
    let publishingInfo : IPublishingAction = {
        contentUri : vscode.window.spgo.config.publishWorkspaceGlobPattern
            ? UrlHelper.ensureLeadingSlash(vscode.window.spgo.config.publishWorkspaceGlobPattern)
            : vscode.window.spgo.config.workspaceRoot + UrlHelper.osAwareGlobStar(),
        scope : Constants.PUBLISHING_MAJOR,
        message : vscode.window.spgo.config.checkInMessage || Constants.PUBLISHING_DEFAULT_MESSAGE
    }
    Logger.outputMessage('Starting Workspace Publish...', vscode.window.spgo.outputChannel);
    let fileService : SPFileService = new SPFileService();

    return UiHelper.showStatusBarProgress('Publishing workspace',
        AuthenticationService.verifyCredentials(vscode.window.spgo, publishingInfo)
            .then((publishingInfo) => UiHelper.getPublishingMessage(publishingInfo))
            .then((publishingInfo) => fileService.uploadFilesToServer(publishingInfo))
            .then(()=>{Logger.outputMessage('Workspace Publish complete.', vscode.window.spgo.outputChannel)})
            .catch(err => ErrorHelper.handleError(err))
    );
}
```

### Promise Queue

*Example: Synchronize Workspace* from SPGo [Here](https://github.com/chrishasz/spgo/blob/master/src/command/populateWorkspace.ts)

```typescript
'use strict';
import * as vscode from 'vscode';

import {IError} from './../spgo';
import {Logger} from '../util/logger';
import {UiHelper} from './../util/uiHelper';
import { ErrorHelper } from '../util/errorHelper';
import {SPFileService} from './../service/spFileService';
import {AuthenticationService} from './../service/authenticationService';

export default function populateWorkspace() : Thenable<any> {
    let fileService : SPFileService = new SPFileService();

    return UiHelper.showStatusBarProgress('Populating workspace',
        AuthenticationService.verifyCredentials(vscode.window.spgo)
            .then(downloadFiles)
            .catch(err => ErrorHelper.handleError(err))
    );

    function downloadFiles() : Thenable<any> {

        return new Promise((resolve, reject) => {
            let downloads : Promise<any>[] = [];

            Logger.outputMessage('Starting File Synchronization...', vscode.window.spgo.outputChannel);

            if(vscode.window.spgo.config.remoteFolders){
                for (let folder of vscode.window.spgo.config.remoteFolders) {
                    downloads.push(fileService.downloadFiles(decodeURI(folder)));
                }
                Promise.all(downloads)
                    .then(() => {
                        Logger.outputMessage(`file synchronization complete.`, vscode.window.spgo.outputChannel);
                        resolve();
                    })
            }
            else{
                let error : IError ={
                    message : '"remoteFolders":[string] property not configured in workspace configuration file.'
                };
                Logger.showError(error.message, error);
                reject();
            }
        });
    }
}
```

## Extraction and Encapsulation

[1.](/){:name="citation1"} You may also have heard of promises as **futures**.
