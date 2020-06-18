---
layout: post
title: "Steal My Idea: DevFunded"
categories: [Business]
tags: [Steal My Idea, Github, DevFunded, Payments, Open Source]
---

The Enterprise world runs on open source software[1](#citation1){:.superscript}. It is possible for large organizations like the RedHat foundation to operate in a self-sufficient manner, and companies like [Facebook](https://www.facebook.com){:target="_blank"} can fund internal support for libraries like [React](https://reactjs.org){:target="_blank"}, but there are hundreds of thousands of free libraries (npm, alone, hosts over 995,000 packages[2](#citation2){:.superscript}) that are supported at no charge by independent developers[3](#citation3){:.superscript}.

What happens when a developer, either through disinterest, lack of time, or [outright](https://twitter.com/izs/status/906775425845571585){:target="_blank"} [abuse](https://jamie.build/dear-javascript.html){:target="_blank"} decides to abandon their project? Even worse, what happens if a library is [hijacked by bad actors](https://blog.npmjs.org/post/180565383195/details-about-the-event-stream-incident){:target="_blank"}? The best case scenario is that a new owner can be found, possibly from within one of the organizations that depend on this software, but there is often internal or legal hurdles to this. The worse case scenario is an outage, data breach, or loss of revenue.

Ensuring a viable roadmap or a consistent maintenance and release schedule is no guaranteed thing for maintainers who have a day job, family, hobbies, and personal responsibilities. Even more challenging is that individual developers are often neither interested with or fluent in **how** to effectively engage with enterprise companies in a contractual setting to provide ongoing support[4](#citation4){:.superscript}.

Providing a web-based solution for connecting corporate funding to independent open source developers would simultaneously ensure support for the software that corporations rely upon and provide a vehicle for compensating independent developers for their efforts to ensure a future of maintenance.

## Overview

The three main components of this solution are a public code repository for hosting the software packages, a secure website for developers and organizations to interact for contracting and support purposes, and a payments tool to streamline/simplify the process of compensating developers for their time.

### Source Code Repository

The core piece of this product is a simple and well-understood method for publicly storing, hosting, and distributing source code. The choice of this solution will be key to driving adoption with developers, who are notoriously picky about the tools and solutions they will adopt. The source code repository would need to support versioning, tracking, packaging, build services as well as basic project management concepts such as tasks, projects, scheduling, and alerting.

### Secure Website

Beyond hosting the technical assets associated with a software product or library, both the corporation and the developer will need a place on the web to collaborate on the general "rhythm of business" processes of ongoing software delivery. On the delivery side, this includes tools such as issue tracking, feature definition and scheduling, and links to different package versions. On the contractual side, this website would need to provide support for authoring and execution of legal contracts between all parties.
Table stakes for a secure site like this would be:

- User authentication and Single Sign-On (SSO) between all components of the system
- Mobile/responsive web views
- Correspondence services (email, SMS) for standard communication items e.g. issue updates, release notifications, contractual updates.

### Payment Tool

Payment services need to work hand-in-hand with the contracts component of the secure website and support one-time and recurring payments to developers.

### How I would do it

If I was implementing this, Github would be the obvious initial choice for source code hosting and distribution, hosting issues and project collaboration, and as the payment processing tool. Github pages are not yet robust enough to support the secure website, but the standard API set would easily provide sufficient integration points to provide a consistent/connected experience from an externally hosted website to enable project collaboration and legal contract tracking.

Additional core services would include static code analysis and package scanning for security and license compliance.

As the solution matures, there may be compelling reasons for adopting other platforms such as

- BitBucket, or a stand-alone git implementation for source control
- Additional payment tools to provide support for common corporate payment types, e.g. ACH
- additional authentication providers, or federation with corporate SSO implementations.

## Revenue Source

This solution would charge the buyer a flat fee percentage, less processing fees, of all transactions.

## Benefits

- reduced corporate fear or anxiety of broader adoption of valuable tools, leading to greater revenue and reduced development costs
- Satisfying internal corporate purchasing and legal teams' need to have structure around engagements
- Corporate-friendly process for paying external software developers

## Challenges

- **Reduced freedom or independent license-** Organizations who fund engineers may feel increased ownership of the software and want to limit developer freedom, leading to resentment and possible abandonment.
- **Scope and schedule creep-** Contracts alone cannot prevent all instances of abuse by either party of the terms of delivery, or honor vacation schedules, sick time, etc.
- **Breach of contract-** Contractual terms provide a promise of delivery, but can not fully guarantee the developer will maintain a product in perpetuity
- **One size fits none--** Contracts or documents may be too broadly scoped and may be completely rewritten or rejected out of hand by corporate legal teams.

### citations

- [1.](/){:name="citation1"} Number of Fortune 500 companies who use [open source software](https://www.zdnet.com/article/its-an-open-source-world-78-percent-of-companies-run-open-source-software/){:target="_blank"}.
- [2.](/){:name="citation2"} NPM count of [total hosted packages](https://www.npmjs.org/){:target="_blank"}.
- [3.](/){:name="citation3"} Github user statistics via [State of the Octoverse](https://octoverse.github.com/people){:target="_blank"}
- [4.](/){:name="citation4"} Challenges Startups have [engaging enterprise organizations](https://news.greylock.com/startups-serving-the-enterprise-6d318fd15c04){:target="_blank"}.

### notes

- **full transparency**: my open source project, [SPGo](https://www.chrishasz.com/spgo){:target="_blank"} was affected by the streams.io malware attack via transient inclusion in a dependent package, so this problem hits close to home.
- Github recently announced [Github Sponsors](https://github.com/sponsors){:target="_blank"} which provides a channel for end-users to compensate (i.e. encourage) developers who build and maintain the open source software and libraries they use day to day. This vastly simplifies implementation of the payments portion of this service.

----
*Steal My Idea is a series of posts describing the various opportunities I see in the marketplace for viable businesses, but don't have the time to build myself. Please go build these products so that I can use them.*
