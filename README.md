# Playwright with .NET and NUnit

## Installation

```sh
dotnet new nunit -n PlaywrightTests
cd PlaywrightTests
dotnet add package Microsoft.Playwright.NUnit
dotnet build
```

## Install PowerShell

```sh
winget search Microsoft.PowerShell
dotnet tool install --global PowerShell
```

## Install Requires Browser

```sh
pwsh bin/Debug/net8.0/playwright.ps1 install
```

## Playwright ExampleTest

```cs
public class ExampleTest : PageTest
    {
        [Test]
        public async Task Test1()
        {
            //Navigation
            await Page.GotoAsync("url");

            //Assertion
            await Expect(Page).ToHaveTitleAsync(new Regex("Playwright"));

            //Create Locator
            var getStarted = Page.GetByRole(AriaRole.Link, new() { Name = "Get started" });

            //Interactions in Locator
            await getStarted.ClickAsync();

            await Page.GetByRole(AriaRole.Link, new() { Name = "Get started" }).ClickAsync();

            Page.Locator("#rhs");

        }
    }
```

## Run Test

```sh
dotnet test 
dotnet test --filter "ExampleTest"
dotnet test --filter "ExampleTest1|ExampleTest2"
dotnet test --settings .runsettings
```

## runsettings Example
   
```xml
    <?xml version="1.0" encoding="utf-8"?>
   <RunSettings>
    <Playwright>
     <BrowserName>chromium</BrowserName>
      <LaunchOptions>
       <Headless>false</Headless>
       <Channel>msedge</Channel>
       <SlowMo>500</SlowMo>
      </LaunchOptions>
    </Playwright>
    </RunSettings>
```

## Generating locators by Codegen

```sh
pwsh bin/Debug/net8.0/playwright.ps1 codegen demo.playwright.dev/todomvc
```

## Recording a Trace

```cs
[SetUp]
public async Task Setup()
    {
        await Context.Tracing.StartAsync(new()
        {
            Title = $"{TestContext.CurrentContext.Test.ClassName}.{TestContext.CurrentContext.Test.Name}",
            Screenshots = true,
            Snapshots = true,
            Sources = true
        });
    }

[TearDown]
public async Task TearDown()
    {
        await Context.Tracing.StopAsync(new()
        {
            Path = Path.Combine(
                TestContext.CurrentContext.WorkDirectory,
                "playwright-traces",
                $"{TestContext.CurrentContext.Test.ClassName}.{TestContext.CurrentContext.Test.Name}.zip"
            )
        });
    }
```

## Opening the Trace

```sh
pwsh bin/Debug/net8.0/playwright.ps1 show-trace bin/Debug/net8.0/playwright-traces/PlaywrightTests.ExampleTest.GetStartedLink.zip
```

## CI GitHub Actions

```yml
name: Playwright Tests
on:
  push:
    branches: [ main, master ]
  pull_request:
    branches: [ main, master ]
jobs:
  test:
    timeout-minutes: 60
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - name: Setup dotnet
      uses: actions/setup-dotnet@v4
      with:
        dotnet-version: 8.0.x
    - run: dotnet build
    - name: Ensure browsers are installed
      run: pwsh bin/Debug/net8.0/playwright.ps1 install --with-deps
    - name: Run your tests
      run: dotnet test
    - name: upload Test artifacts
      uses: actions/upload-artifact@v4
      with:
        name: playwright-traces
        path: bin/Debug/net8.0/playwright-traces/
```

## Create a Repo and Push to GitHub

```sh
git init
git add .
git commit -m "write comment"
git branch -M main
git remote add origin url
git push -u origin main 
```