---
template: "post"
category: "blog"
tags: 
  - "cpp"
  - "tip"
  - "vscode"
  - "blog"
banner: "/assets/bg/1.jpg"
---

## visual studio code에서 mingw gdb로 c++ 디버깅 문제해결중.

이전 글대로 설정을 했는데도 [이 github 이슈](https://github.com/microsoft/vscode-cpptools/issues/2889)에 나온것처럼 "Unable to establish a connection to GDB"라고 나오면서 디버깅이 되지 않아서, 혹시나 해서 파워셸 프로필에서 쓸데없는 작업들을 없앴더니 해결되었다.

깃헙 이슈에 따르면 파워셸 대신 cmd로 터미널을 바꿔서 해결하거나, `launch.json`에서 `externalConsole: true`로 설정하여 해결한 경우도 있는 것으로 보인다.

파워셸 프로필의 동작을 줄이면 실행이 잘 되고, 늘리면 실패하는 경우가 많아 어떤 고정된 timeout이 있어서 그런 것 아닌가 싶어 vscode를 뜯어보기로 했다.

vscode를 빌드하려는데 node-gyp 문제가 발생하여 `npm -g install --production windows-build-tools --msvs_version="2015"`로 msbuild를 대체하여 빌드했다.

vscode에서 찾아보려는데 DebugAdapter 등 직접적이지 않은 코드만 있고, timeout도 거의 없어서 그나마 있던 1초짜리 timeout을 늘려봤지만 결과는 같았다. 그래서 c/c++ extension의 문제인가 하고 extension을 받았다. 여러가지 timeout이 있었고 전부 breakpoint를 건 다음 디버깅을 시도해봐도 브레이크가 걸리지 않았다. 그래서 그냥 global timeOut를 지정하는 변수를 열 배로 늘려보았지만 결과는 여전했다. 찾아본 결과 다음과 같은 코드를 확인하여 vscode는 debugAdapter들을 실행시켜주고, c++ extension은 debugAdapter를 등록하는 식으로 동작하는 거 같았다. 

```ts
 disposables.push(vscode.debug.registerDebugAdapterDescriptorFactory(CppvsdbgDebugAdapterDescriptorFactory.DEBUG_TYPE, new CppvsdbgDebugAdapterDescriptorFactory(context)));
```

그러면 어느 부분에서는 새 콘솔을 켜고 그 콘솔에서 WindowsDebugLauncher를 실행시키는 command를 등록하는 코드가 있어야 한다. 이상한 건, 실행시켜야 하는 것은 gdb.exe여야 할 거 같은데, miDebuggerPath 옵션으로 경로만 지정하게 되어있고 그 경로조차 직접 실행시킬 것 같은 코드는 보이지 않았다. 다시 DebugAdapter에 대해 찾아보니 "OpenDebugAD7"이라는 것을 실행시키는 것으로 보이는 (`function makeBinariesExecutable(...)`) 코드가 있어서 찾아보니 [MIEngine](https://github.com/microsoft/MIEngine)이라는 것이 있었다. 이것이 정확히 어떻게 동작하는지 몰라 우선 버그 문구가 여기에 있는지 찾아보았다. 실패할 때 "Unable to establish a connection to GDB. Debug output may contain more information"이라고 떴는데, 그래서 MIEngine에서 찾아보니 `public static string Error_DebuggerInitializeFailed_NoStdErr`의 주석에 그 문구가 달려있었다. 다국어화를 위한 리소스화로 보였다. 어쨌든 이 변수를 어디에서 쓰는지 찾아보니, `MIDebuggerInitializeFailedException`클래스의 `Message` 프로퍼티와, `RunInTerminalTransport`클래스의 `Init`함수에서 참조하고 있었다. 그런데, 후자의 함수에서 다음과 같은 식으로 Timeout이 있었다.

```cs
Task waitOrTimeout = Task.WhenAny(_waitForConnection, Task.Delay(5000));
```

그래서 이걸 늘려보기로 했다.

MIEngine 저장소의 Prerequisites에 다음과 같이 설명되어 있어서 빌드가 가능할지는 불명확하다.

```
Prerequisites

MIEngine requires Visual Studio 2015 with the following features installed:

    Programming Languages -> Visual C++ -> Common Tools for Visual C++
    Cross Platform Mobile Development -> Visual C++ Mobile Development
    Cross Platform Mobile Development -> Microsoft Visual Studio Emulator for Android
    Common Tools -> Visual Studio Extensibility Tools
```

솔루션을 열어보니 .NET 타기팅 팩을 다운받으라 해서 설치하고, 확장 개발을 위한 Visual Studio 컴포넌트도 받았다.

timeout시간을 늘려서 저장한 후, cpp-tools쪽에 설명된 대로 Desktop.Debug모드로 빌드해서 `debugAdapters/bin`에 복붙해 넣었는데도 실행에 변화가 없는 것 같다. 로그 출력 부분을 바꿔봐도 출력이 그대로 나온다.


아래와 같은 코드가 처음에 timeout 시간을 늘렸던 부분인데, 여기서 에러가 걸려도 디버깅이 될 때도 있었으므로 이것은 원인이 아니다.

```ts
		return Promise.race([
			Promise.all(promises).then(result => {
				const trackers = <vscode.DebugAdapterTracker[]>result.filter(t => !!t);	// filter null
				if (trackers.length > 0) {
					return new MultiTracker(trackers);
				}
				return undefined;
			}),
			new Promise<never>((resolve, reject) => {
				const timeout = setTimeout(() => {
					clearTimeout(timeout);
					reject(new Error('timeout'));
				}, 1000);
			})
		]).catch(err => {
			// ignore errors
			return undefined;
		});

```