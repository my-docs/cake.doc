[CakeBuild](http://cakebuild.net/)
====

빌드 환경 준비하기
----
단 한줄의 커맨드 입력만으로 __Cake__ 환경을 구성할 수 있습니다.<br>
__Windows__
```
Invoke-WebRequest http://cakebuild.net/download/bootstrapper/windows -OutFile build.ps1
```
__OSX__
```
curl -Lsfo build.sh http://cakebuild.net/download/bootstrapper/osx
```

이후에 쉘에서 아래의 명령어를 실행하면 빌드가 실행됩니다.
```
./build.ps1
```

첫 번째 빌드 스크립트 작성하기
----
빌드 스크립트의 이름은 `build.cake`로 작성해야 합니다.<br>
프로젝트 폴더에 `build.cake` 파일을 만들고 아래와 같이 코드를 작성합니다.

```cs
var target = Argument("target", "Default");

Task("Default")
  .Does(() =>
{
  Information("Hello World!");
});

RunTarget(target);
```

파일을 저장한 뒤 `./build.ps1`을 입력해 빌드를 수행하면 `Default` 타겟이 실행되고, __Hello World__가 출력되는것을 볼 수 있습니다.


여러개의 Task 만들기
----
단순히 여러개의 `Task()` 를 정의하는것으로 여러개의 Task를 만들 수 있습니다.
```cs
Task("task1")
    .Does(() => {
        // task1 의 실행 코드들...
    });

Task("task2")
    .Does(() => {
        // task2 의 실행 코드들...
    });
```

__디펜던시 설정하기__<br>
태스크간에 디펜던시를 설정할 수 있습니다.
```cs
// package 태스크 실행 전에 build 가 수행된적이 없다면
// build 부터 실행된 이후에 package 가 실행됩니다.
Task("package")
    .IsDependentOn("build")
    .Does(() => {
        // ....
    });
```

__처음에 실행될 태스크 설정하기__
`target` 이름의 Argument를 통해서 실행될 태스크를 설정할 수 있습니다.
```cs
var target = Argument("target", "task1");

Task("task1")
  .Does(() =>
{
  Information("Hello task 1!");
});

Task("task2")
  .Does(() =>
{
  Information("Hello task 2!");
});

RunTarget(target);
```
빌드 스크립트를 실행할 때 `-Target TASK-NAME` 과 같이 추가 파라미터를 전달합니다.
```
./build.ps1 -Target task1
```

MSBuild 사용하기
----
```cs
MSBuild("./SOLUTION_NAME.sln",
    new MSBuildSettings()
        .WithProperty("PostBuildEvent", new string[] { "" })
    );
```

프로세스 시작하기
----
프로세스를 다루기 위해서는 .Net에서 제공하는 `Process` API를 사용합니다.
```cs
using System.Diagnostics;

Process.Start("name", "args").WaitForExit();
```
__에러 코드에 따라 빌드를 실패 처리하기__
```cs
Process.Start("name", "args");
```

디렉토리 작업 수행하기
----

```cs
Task("clean")
    .Does(() => {
        // 이전 빌드 데이터를 삭제한다.
        CleanDirectory("build-stage");
    });
```

파일 작업 수행하기
----

