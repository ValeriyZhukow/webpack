# TODO

- Increased minimal node.js version from 6 to 8
- removed buildin directory and replaced buildins with runtime modules
- Removed deprecated features
  - BannerPlugin now only support an options object
- removed CachePlugin
- Chunk.entryModule is deprecated
- Chunk.hasEntryModule is deprecated
- Chunk.addModule is deprecated
- Chunk.removeModule is deprecated
- Chunk.getNumberOfModules is deprecated
- Chunk.modulesIterable is deprecated
- Chunk.compareTo is deprecated
- Chunk.containsModule is deprecated
- Chunk.getModules is deprecated
- Chunk.remove is deprecated
- Chunk.moveModule is deprecated
- Chunk.integrate is deprecated
- Chunk.canBeIntegrated is deprecated
- Chunk.isEmpty is deprecated
- Chunk.modulesSize is deprecated
- Chunk.size is deprecated
- Chunk.integratedSize is deprecated
- Chunk.getChunkModuleMaps is deprecated
- Chunk.hasModuleInGraph is deprecated
- Chunk.updateHash signature changed
- Chunk.getChildIdsByOrders signature changed (TODO: consider moving to ChunkGraph)
- Chunk.getChildIdsByOrdersMap signature changed (TODO: consider moving to ChunkGraph)
- Chunk.getChunkModuleMaps removed
- Chunk.setModules removed
- deprecated Chunk methods removed
- ChunkGraph added
- ChunkGroup.setParents removed
- ChunkGroup.containsModule removed
- ChunkGroup.remove no longer disconnected the group from block
- ChunkGroup.compareTo signature changed
- ChunkGroup.getChildrenByOrders signature changed
- ChunkGroup index and index renamed to pre/post order index
  - old getter is deprecated
- ChunkTemplate.hooks.modules sigature changed
- ChunkTemplate.hooks.render sigature changed
- ChunkTemplate.updateHashForChunk sigature changed

# Changes to the Configuration

## Changes to the structure

- `cache: Object` removed: Setting to a memory-cache object is no longer possible
- `cache.type` added: It's no possible to choose between `"memory"` and `"filesystem"`
- New configuration options for `cache.type = "filesystem"` added:
  - `cache.cacheDirectory`
  - `cache.name`
  - `cache.version`
  - `cache.store`
  - `cache.loglevel`
  - `cache.hashAlgorithm`
- `resolve.cache` added: Allows to disable/enable the safe resolve cache
- `resolve.concord` removed
- Automatic polyfills for native node.js modules were removed - `node.Buffer` removed - `node.console` removed - `node.process` removed - `node.*` (node.js native module) removed - MIGRATION: `resolve.alias` and `ProvidePlugin`. Errors will give hints.
- `optimization.chunkIds: "deterministic"` added
- `optimization.moduleIds: "deterministic"` added
- `optimization.moduleIds: "hashed"` deprecated
- `optimization.moduleIds: "total-size"` removed
- Deprecated flags for module and chunk ids were removed - `optimization.hashedModuleIds` removed - `optimization.namedChunks` removed - `optimization.namedModules` removed - `optimization.occurrenceOrder` removed - MIGRATION: Use `chunkIds` and `moduleIds`
- `optimization.splitChunks` sizes can now be objects with a size per source type
  - `minSize` - `maxSize` - `maxAsyncSize` - `maxInitialSize`
- `optimization.splitChunks` `maxAsyncSize` and `maxInitialSize` added next to `maxSize`: allows to specify different max sizes for initial and async chunks
- `optimization.splitChunks` `name: true` removed: Automatic names are no longer supported
  - MIGRATION: Use the default. `chunkIds: "named"` will give your files useful names for debugging
- `optimization.splitChunks.cacheGroups[].idHint` added: Gives a hint how the named chunk id should be chosen
- `optimization.splitChunks` `automaticNamePrefix` removed
  - MIGRATION: Use `idHint` instead
- `output.devtoolLineToLine` removed
  - MIGRATION: No replacement
- `output.hotUpdateChunkFilename: Function` is now forbidden: It never worked anyway.
- `output.hotUpdateMainFilename: Function` is now forbidden: It never worked anyway.
- `stats.chunkRootModules` added: Show root modules for chunks
- `stats.orphanModules` added: Show modules which are not emitted
- `stats.runtime` added: Show runtime modules
- `BannerPlugin.banner` signature changed - `data.basename` removed - `data.query` removed - MIGRATION: extract from `filename`
- `SourceMapDevToolPlugin` `lineToLine` removed
  - MIGRATION: No replacement
- `[hash]` as hash for the full compilation is now deprecated
  - MIGRATION: Use `[fullhash]` instead or better use another hash option
- `[modulehash]` can now replaced with `[hash]`
- `[moduleid]` can now replaced with `[id]`

## Changes to the defaults

- `module.unsafeCache` is now only enabled for `node_modules` by default
- `optimization.moduleIds` defaults to `deterministic` in production mode instead of `size`
- `optimization.chunkIds` defaults to `deterministic` in production mode instead of `total-size`
- `optimization.nodeEnv` defaults to `false` in `none` mode
- `resolve(Loader).cache` defaults to `true` when a `cache` is used
- `resolve(Loader).cacheWithContext` defaults to `false`

# Major Changes

## Runtime Modules

A large part of runtime code was moved into so called "runtime modules". These special modules add runtime code. They can be added in any chunk, but are currently always added to the runtime chunk. "Runtime requirements" control which runtime modules are added to the bundle. This ensures that only runtime code that is used is added to the bundle. In future runtime modules could also added to on-demand-loaded chunk to load runtime code when needed.

The core runtime is now very small and only includes the `__webpack_require__` function, module factories and module instance cache. In future an alternative code runtime can be used to avoid wrapping the bundle in a IIFE and allow ESM style exports. This will allow ESM as target.

This change is not visible from user perspective.

## Serialization

A serialization mechanism was added to allow serialization of complex objects in webpack. It has a opt-in semantic, so classes that should be serialized need to be explicitly flagged (and serialization implemented). This has been done for most Modules, all Dependencies and some Errors.

## Extensible Caching

A `Cache` class with a plugin interface has been added. This class can be used to write and read to the cache. Depending on configuration different plugins add the functionality to the cache. The `MemoryCachePlugin` adds in-memory caching. The `FileCachePlugin` adds persistent caching.

The `FileCachePlugin` uses the serialization mechanism to persist and restore cached items to disk.

## Hook object frozen

Classes with `hooks` have their `hooks` object frozen, so adding custom hooks is no longer possible this way. The recommended way to add custom hooks is via WeakMap and a static `getXXXHooks(XXX)` (i. e. `getCompilationHook(compilation)`) method. Internal classes use the same mechanism for custom hooks.

---

lib/CommentCompilationWarning.js
