Playwright Automation Tests 

Playwright .NET NUnit

cmd

    dotnet new nunit -n PlaywrightTests
    cd PlaywrightTests
    dotnet add package Microsoft.Playwright.NUnit
    dotnet build

install PowerShell

powershell

    winget search Microsoft.PowerShell

cmd

    dotnet tool install --global PowerShell


install Requires Browser

    pwsh bin/Debug/net8.0/playwright.ps1 install


ExampleTest

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

        //Make action in Locator
        await getStarted.ClickAsync();

        await Page.GetByRole(AriaRole.Link, new() { Name = "Get started" }).ClickAsync();

        Page.Locator("#rhs");

    }
    }

Test Hooks

    [SetUp]
    public async Task SetUp()
    {
        await Page.GotoAsync("https://playwright.dev");
    }

Run Test

    dotnet test 
    dotnet test --filter "ExampleTest"
    dotnet test --filter "ExampleTest1|ExampleTest2"



settings Example
   
   ```
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
    
Run with settings

    dotnet test --settings .runsettings

Codegen

    pwsh bin/Debug/net8.0/playwright.ps1 codegen demo.playwright.dev/todomvc


Trace viewer

    Setup
    await Context.Tracing.StartAsync(new()
        {
            Title = $"{TestContext.CurrentContext.Test.ClassName}.{TestContext.CurrentContext.Test.Name}",
            Screenshots = true,
            Snapshots = true,
            Sources = true
        });

    TearDown
    await Context.Tracing.StopAsync(new()
        {
            Path = Path.Combine(
                TestContext.CurrentContext.WorkDirectory,
                "playwright-traces",
                $"{TestContext.CurrentContext.Test.ClassName}.{TestContext.CurrentContext.Test.Name}.zip"
            )

    Opening the trace
    pwsh bin/Debug/net8.0/playwright.ps1 show-trace bin/Debug/net8.0/playwright-traces/PlaywrightTests.ExampleTest.GetStartedLink.zip


CI GitHub Actions

playwright.yml

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


Create a Repo and Push to GitHub

    git init
    git add .
    git commit -m "first commit"
    git branch -M main
    git remote add origin url
    git push -u origin main 

