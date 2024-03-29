---
status: WIP
author: Hyeseong Kim <tim@daangn.com>
---

# Brane Project Explainer

Brane is an experimental project that aims to build secure, convenient and powerful third-party app hosting environment based on modern web technologies. It's a set of building blocks for **OS-grade experiences**, such as vendorizing, publishing, loading apps and embedding widgets and smooth transition between apps and widgets.

Brane's idea loosely follows the [Progressive Web App](https://web.dev/progressive-web-apps/) and [MiniApp Standardization White Paper](https://www.w3.org/TR/mini-app-white-paper/)

## Motivation

Web is very popular approch for building mobile apps today.

Still, there has been a lot of discussion about whether something can be implemented on the web, because of lack of platform capabilities.

The [Progressive Web Apps](https://web.dev/progressive-web-apps/) is a well-known approach, can [brings many of platform-native features](https://whatwebcando.today/) such as installation, offline support, payments, and push notifications that are available in modern browsers today. However, there are many limitations to providing an well-integrated experience with the platform, which tends to be avoided.

Using WebView and JavaScript bridge allows app to build experiences that are well-integrated with the platform. However, APIs (Android's WebViewClient and iOS's WKWebView) are somewhat outdated and have their own limitations. WebView APIs are inconvenient to use and often conflict with platform-specific lifecycle model or security model.

As the business evolves and apps begin to scale and to verticalized, new patterns such as ["Micro Frontends"](https://micro-frontends.org/) emerge. These are all for single, standalone app development and non of these can describe a usecase for running third-party codes.

Using trusted third-party codes may partially be able by static build tools like [Module Federation](https://webpack.js.org/concepts/module-federation/) by Webpack, or platform-specific APIs such as [Trusted Web Activities](https://developer.chrome.com/docs/android/trusted-web-activity/) by Android.

But collaborating with *untrusted* third-party apps is absurd yet. Making it work with a good experience on the web (at least it's not iframe) seems very difficult. If anyone want to build a creative space for third-parties, interactive ads, plugin systems, or [Mini Apps], they need to consider a unique approach from the scratch to secure and to integrate with existing platform.

The Brane Project starts from the desire to share some working and sensible building blocks for apps that want to build their own platforms today. Brane lifts up the responsibilities and feature limitations that are exclusive a few OS/browser vendors today, so offers to apps the opportunity to become itself a host environment for other apps.

## Initiative

- **Safe**: Running third-party apps can be a big security threat. Brane should provide the safe way to isolate it.
- **User Experience**: Brane should not degrade the user experience while running isolated third-party apps. It should not only minimizing the performance overhead due to isolation, but also even if third-party apps are getting slow, it shouldn't block the host-level experience.
- **Modern Web**: Instead of designing a new system interface, Brane leverages the interfaces and security models Web already has today and constantly evolving. And gives power that users can provide themselves without being blocked to browsers implementation status. Meanwhile, Brane should not be an attempt to disrupt the legacy of the Web. We need to help and cooperate with all Brane users to fulfill their responsibilities as Web providers.
- **Inclusive**: Brane goes beyond being transparent, keeping it open to anyone who wants to build and improve their own platform.

## Architecture

### Web Sandbox

Brane apps operate on a different security model than typical web apps. It's the cross-origin iframe and [Web Workers].

A Brane host spawns multiple frames with worker to manage apps. A web worker isolates a app instance and has a port to communicate with the host environment. Since the many of browser APIs including DOM does not exist in the JavaScript context spawned in the worker, it has to be implemented separately. This is similar to the AMP's [WorkerDOM](https://github.com/ampproject/worker-dom) project. Brane's implementation goes further here on a shared memory basis rather than messaging basis. This unlocks cooperative scheduling so allows all web interfaces including blocking ones like `getClientBoundingRect()` to be properly implemented.

See more detail on [RFC](https://github.com/braneproject/rfcs/pull/1)

### Single-page, Multi-apps

One thing Brane requires of a guest app is that it be a single JavaScript app to accommodate isolation. This makes sense when targeting packaged applications and also already popular today, so called SPA(Single Page Application) model.

Brane runtime is prebuilt and shared between multiple apps and can preload shared resources, apps or state. This makes for a noticeably better user experience on a multi-app environment.

## Comparison

### vs. Progressive Web Apps

[PWA] is often introduced as an alternative to native apps. However, since it relies on the browser, it is different from the actual platform-native experience, and there are restrictions on customization for the vendor app want to integrate.

Brane provides opportunities for fine-grained control across the native platform, browser, vendor app (host), and user app (guest) through its sandboxed runtime, and the interface adopts the same Web standards as the PWA, maximizing interoperability.

### vs. Mini Apps

[Mini Apps], which are in the process of being standardized, also partially use the Web technologies like HTML, JavaScript, and CSS. However, it doesn't strictly flollow Web standards such as DOM and HTML.

This can lead to fragmented interfaces vendors and create new barriers of entry.

### vs. Iframe

Iframe is a standard way to embed third-party content into the DOM. There are several issues in iframes that make them unsuitable for displaying app content.

1. Accessibility; content loaded into an iframe is not displayed on screen readers.
2. Capabilities; the `sandbox` attribute makes content's origin to be `null` so disables browser features that requires origin policy.

[PWA]: https://web.dev/progressive-web-apps/
[Mini Apps]: https://web.dev/mini-apps/
[Web Workers]: https://html.spec.whatwg.org/multipage/#workers
