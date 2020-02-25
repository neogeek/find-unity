# find-unity

> Command line tool for locating the version of Unity that a project was built with

![GitHub tag (latest SemVer)](https://img.shields.io/github/v/tag/neogeek/find-unity)

## Install

```bash
$ brew tap neogeek/find-unity
$ brew install find-unity
```

## Usage

### Bash

```bash
$ find-unity
```

```
Usage: find-unity [options]

OPTIONS:
   -v     show the version number and then exit
   -h     show this help message and then exit

   -e     path to search for Unity editors
   -f     file to checking project specific version number
```

### Unity

#### Editor/Build.cs

```csharp
using System.Linq;
using UnityEditor;
using UnityEditor.Build.Reporting;
using UnityEngine;

public static class Build
{

    [MenuItem("Build/Build All")]
    public static void BuildAll()
    {

        BuildAndroid();

        BuildIOS();

    }

    [MenuItem("Build/Build Android")]
    public static void BuildAndroid()
    {

        var report = BuildPipeline.BuildPlayer(new BuildPlayerOptions
        {
            locationPathName = "Builds/Android.apk",
            scenes = EditorBuildSettings.scenes.Select(s => s.path).ToArray(),
            target = BuildTarget.Android,
            options = BuildOptions.None
        });

        Debug.Log(
            $"Android build {(report.summary.result.Equals(BuildResult.Succeeded) ? "completed" : "failed")}.");

    }

    [MenuItem("Build/Build iOS")]
    public static void BuildIOS()
    {

        var report = BuildPipeline.BuildPlayer(new BuildPlayerOptions
        {
            locationPathName = "Builds/iOS",
            scenes = EditorBuildSettings.scenes.Select(s => s.path).ToArray(),
            target = BuildTarget.iOS,
            options = BuildOptions.None
        });

        Debug.Log($"iOS build {(report.summary.result.Equals(BuildResult.Succeeded) ? "completed" : "failed")}.");

    }

}
```

#### Makefile

```bash
build-all: build-android build-ios

build-android:
	"$(shell find-unity)/Contents/MacOS/Unity" -projectPath "$(shell pwd)/" -batchmode -quit -executeMethod "Build.BuildAndroid"

build-ios:
	"$(shell find-unity)/Contents/MacOS/Unity" -projectPath "$(shell pwd)/" -batchmode -quit -executeMethod "Build.BuildIOS"

clean:
	git clean -xdf
```

#### Bash

```bash
$ make build-all
```
