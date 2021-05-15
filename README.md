# Unity Jobs support for LeoEcsLite C# Entity Component System framework
Unity jobs support as ecs-systems.

> Tested on unity 2020.3 (dependent on it) and contains assembly definition for compiling to separate assembly file for performance reason.

> **Important!** Don't forget to use `DEBUG` builds for development and `RELEASE` builds in production: all internal error checks / exception throwing works only in `DEBUG` builds and eleminated for performance reasons in `RELEASE`.

# Table of content
* [Socials](#socials)
* [Installation](#installation)
    * [As unity module](#as-unity-module)
    * [As source](#as-source)
* [Example](#example)
    * [Component](#component)
    * [JobSystem](#jobsystem)
    * [Job](#job)
* [License](#license)

# Socials
[![discord](https://img.shields.io/discord/404358247621853185.svg?label=enter%20to%20discord%20server&style=for-the-badge&logo=discord)](https://discord.gg/5GZVde6)

# Installation

## As unity module
This repository can be installed as unity module directly from git url. In this way new line should be added to `Packages/manifest.json`:
```
"com.leopotam.ecslite.threads.unity": "https://github.com/Leopotam/ecslite-threads-unity.git",
```
By default last released version will be used. If you need trunk / developing version then `develop` name of branch should be added after hash:
```
"com.leopotam.ecslite.threads.unity": "https://github.com/Leopotam/ecslite-threads-unity.git#develop",
```

## As source
If you can't / don't want to use unity modules, code can be downloaded as sources archive of required release from [Releases page](`https://github.com/Leopotam/ecslite-threads-unity/releases`).

# Example

## Component
```csharp
struct C1 {
    public int Id;
}
```
## JobSystem
```csharp
class TestJobSystem : EcsUnityJobSystem<TestJob, C1> {
    protected override int GetChunkSize (EcsSystems systems) {
        // How many entities will be processed in one chunk.
        return 100;
    }

    protected override EcsWorld GetWorld (EcsSystems systems) {
        // World for component pools and filter.
        return systems.GetWorld ();
    }
    
    protected override EcsFilter GetFilter (EcsWorld world) {
        // Filter for processing.
        return world.Filter<C1> ().End ();
    }
}
```
> **Important!** EcsUnityJobSystem supports up to 4 component pools.
 
## Job
```csharp
struct TestJob : IEcsUnityJob<C1> {
    NativeArray<int> _entities;
    NativeArray<JobPoolItem<C1>> _pool;

    public void Init (NativeArray<int> entities, NativeArray<JobPoolItem<C1>> pool) {
        // We should cache input collections to use it later.
        _entities = entities;
        _pool = pool;
    }

    public void Execute (int index) {
        // We should get entity id from entities collection. 
        var entity = _entities[index];
        // To get access for C1 data.
        var c1 = _pool[entity];
        // We can change it.
        c1.Data.Id++;
        // And save back.
        _pool[entity] = c1;
    }
}
```

# License
The software is released under the terms of the [MIT license](./LICENSE.md).

No personal support or any guarantees.