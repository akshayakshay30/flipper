---
id: images-plugin
title: Images Setup
sidebar_label: Images
---

Currently, the images plugin only supports [Fresco](https://frescolib.org/) for Android as backend, but just like the network plugin, support for other image loading libraries
could easily be added. Send us a PR!

## Fresco and Android

```java
import com.facebook.flipper.plugins.fresco.FrescoFlipperPlugin;

client.addPlugin(new FrescoFlipperPlugin());
```

The `FrescoFlipperPlugin` constructor offers a whole lot of configuration options which
can be useful if you have an advanced setup of Fresco in your application:


```java
FrescoFlipperPlugin(
      DebugImageTracker imageTracker,
      PlatformBitmapFactory bitmapFactory,
      @Nullable FlipperObjectHelper flipperObjectHelper,
      DebugMemoryManager memoryManager,
      FlipperPerfLogger perfLogger,
      @Nullable FrescoFlipperDebugPrefHelper debugPrefHelper,
      @Nullable CloseableReferenceLeakTracker closeableReferenceLeakTracker) { ... }
```

### Leak Tracking

The Flipper plugin can help you track down `CloseableReferences` who have not had
`close()` called on them. This can have a negative impact on the performance of
your application.

Do enable this functionality, you need to create a `CloseableReferenceLeakTracker`
and set it in both your `ImagePipelineConfig` for Fresco and the `FrescoPluginPlugin`
on creation.

```java
import com.facebook.imagepipeline.debug.FlipperCloseableReferenceLeakTracker;

// ...

FlipperCloseableReferenceLeakTracker leakTracker = new FlipperCloseableReferenceLeakTracker();

new ImagePipelineConfig.Builder()
    // ...
    .setCloseableReferenceLeakTracker(leakTracker)
    .build();


client.addPlugin(new FrescoFlipperPlugin(
    new FlipperImageTracker(),
    Fresco.getImagePipelineFactory().getPlatformBitmapFactory(),
    null,
    new NoOpDebugMemoryManager(),
    new NoOpFlipperPerfLogger(),
    null,
    leakTracker));
```