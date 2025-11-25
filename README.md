
# STQA Lab — Practical Codes and README

All code blocks below were taken from the uploaded PDF and grouped by practical number. Use these files as a starting point — update driver paths and system-specific settings before running.

---

## How this README is organized
- Each Practical (1..15) is listed with the code snippets provided in the journal.
- Filenames are suggested where applicable.
- For Java/Selenium code, update `webdriver` paths and browser binaries for your environment before executing.

---

## Practical 5 — launch_Browser (Firefox)
**File:** `launch_Browser.java`

```java
package test_firefox;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.openqa.selenium.firefox.FirefoxOptions;
public class launch_Browser {
    public static void main(String[] args) {
        // Set the path to geckodriver executable
        System.setProperty("webdriver.gecko.driver","C:\\Users\\sam\\Downloads\\geckodriver-v0.36.0-win64\\geckodriver.exe");
        // Set the path to Firefox binary
        FirefoxOptions options = new FirefoxOptions();
        options.setBinary("C:\\Program Files\\Mozilla Firefox\\firefox.exe"); // Change this path to your Firefox installation path
        // Create a new instance of the Firefox driver
        WebDriver driver = new FirefoxDriver(options);
        // Launch the website
        driver.get("https://nmitd.edu.in/");
        // Optionally, you can add a wait here
        try {
            Thread.sleep(5000); // Wait for 5 seconds
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        // Close the browser
        driver.quit();
    }
}
```

---

## Practical 5 — launch_chrome
**File:** `launch_chrome.java`

```java
package test_firefox;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
public class launch_chrome {
    public static void main(String[] args) {
        // Set the path to your ChromeDriver executable
        System.setProperty("webdriver.chrome.driver","C:\\Users\\sam\\Downloads\\chrome_driver\\chromedriver.exe");
        // Create a new instance of the ChromeDriver
        WebDriver driver = new ChromeDriver();
        // Launch the opposite
        driver.get("https://nmitd.edu.in/");
        // Close the browser
        driver.quit();
    }
}
```

---

## Practical 6 — findElement (different locators)
**File:** `findElement.java`

```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.firefox.FirefoxDriver;
public class findElement {
    public static void main(String[] args) {
        // Set path for GeckoDriver
        System.setProperty("webdriver.gecko.driver", "C:\\\\Users\\\\sam\\\\Downloads\\\\geckodriver-v0.36.0-win64\\\\geckodriver.exe");
        // Launch Firefox browser
        WebDriver driver = new FirefoxDriver();
        // Maximize browser window
        driver.manage().window().maximize();
        // Open Facebook login page
        driver.get("https://www.facebook.com/");
        // Locate by ID
        WebElement emailField = driver.findElement(By.id("email"));
        emailField.sendKeys("samtestemail@gmail.com");
        // Locate by Name
        WebElement passwordField = driver.findElement(By.name("pass"));
        passwordField.sendKeys("sam123");
        // Locate login button by Name and click
        WebElement loginButton = driver.findElement(By.name("login"));
        loginButton.click();
        // Close browser after execution
        driver.quit();
    }
}
```

---

## Practical 7 — Browser commands and navigation
**File:** `BrowserCommands.java`

```java
package prac7_stqa;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
public class BrowserCommands {
    public static void main(String[] args) throws InterruptedException {
        System.setProperty("Webdriver.chrome.driver",
                "D:\\Selenium\\Selenium\\chromedriver_win32\\chromedriver.exe");
        WebDriver driver = new ChromeDriver();
        // Open a website
        driver.get("https://www.google.com/");
        System.out.println("Title: " + driver.getTitle());
        System.out.println("Current URL: " + driver.getCurrentUrl());
        Thread.sleep(5000); // 5 sec pause
        // Navigate to another site
        driver.navigate().to("https://www.facebook.com/");
        System.out.println("Navigated to Facebook");
        Thread.sleep(5000); // 5 sec pause
        // Go back to previous page
        driver.navigate().back();
        System.out.println("Went back to Google");
        Thread.sleep(5000); // 5 sec pause
        // Go forward to Facebook again
        driver.navigate().forward();
        System.out.println("Went forward to Facebook");
        Thread.sleep(5000); // 5 sec pause
        // Refresh the current page
        driver.navigate().refresh();
        System.out.println("Page refreshed");
        Thread.sleep(5000); // 5 sec pause
        // Close the browser
        driver.close();
    }
}
```

---

## Practical 8 — Handling multiple frames
**File:** `frame.java`

```java
package Practical;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
public class frame {
    public static void main(String[] args) {
        System.setProperty("Webdriver.chrome.driver", "D:\\\\Selenium\\\\\\\\Selenium\\\\\\\\chromedriver_win32\\\\\\\\chromedriver.exe");
        WebDriver driver = new ChromeDriver();
        // Open the specified URL
        driver.get("http://demo.guru99.com/test/guru99home/");
        // Maximize the browser window
        driver.manage().window().maximize();
        try {
            // Switch to the iframe with name or ID "a077aa5e"
            driver.switchTo().frame("a077aa5e");
            System.out.println("Switched to the iframe with ID 'a077aa5e'");
            // Locate and click the image inside the iframe
            WebElement iframeElement = driver.findElement(By.xpath("/html/body/a/img"));
            iframeElement.click(); // Perform the click action
            System.out.println("*We are done*");
            // Optionally, switch back to main content if further interactions are needed
            driver.switchTo().defaultContent();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close the browser
            driver.quit();
        }
    }
}
```

---

## Practical 9 — Synchronization (Waits)
**File:** `WaitExample.java`

```java
package Practical;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;
import java.time.Duration;
public class WaitExample {
    public static void main(String[] args) throws InterruptedException {
        System.setProperty("Webdriver.chrome.driver",
                "D:\\Selenium\\Selenium\\chromedriver_win32\\chromedriver.exe");
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.google.com/");
        driver.manage().window().maximize();
        // Implicit Wait
        driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
        // Explicit Wait Example
        driver.get("https://practicetestautomation.com/practice-test-login/");
        WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
        WebElement username =
                wait.until(ExpectedConditions.visibilityOfElementLocated(By.id("username")));
        username.sendKeys("student");
        WebElement password = driver.findElement(By.id("password"));
        password.sendKeys("Password123");
        // Pause for 5 seconds after entering password
        Thread.sleep(5000);
        WebElement button = wait.until(ExpectedConditions.elementToBeClickable(By.id("submit")));
        button.click();
        System.out.println("Login submitted successfully!");
        driver.quit();
    }
}
```

---

## Practical 10 — Alerts
**File:** `AlertsExample.java`

```java
package Practical;
import org.openqa.selenium.Alert;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
public class AlertsExample {
    public static void main(String[] args) throws InterruptedException {
        System.setProperty("Webdriver.chrome.driver",
                "D:\\Selenium\\Selenium\\chromedriver_win32\\chromedriver.exe");
        WebDriver driver = new ChromeDriver();
        driver.get("https://the-internet.herokuapp.com/javascript_alerts");
        driver.manage().window().maximize();
        // Simple Alert
        driver.findElement(By.xpath("//button[text()='Click for JS Alert']")).click();
        Alert alert = driver.switchTo().alert();
        System.out.println(alert.getText());
        alert.accept();
        Thread.sleep(5000); // 5-sec pause after simple alert
        // Confirmation Alert
        driver.findElement(By.xpath("//button[text()='Click for JS Confirm']")).click();
        Alert confirm = driver.switchTo().alert();
        System.out.println(confirm.getText());
        confirm.dismiss();
        Thread.sleep(5000); // 5-sec pause after confirmation alert
        // Prompt Alert
        driver.findElement(By.xpath("//button[text()='Click for JS Prompt']")).click();
        Alert prompt = driver.switchTo().alert();
        prompt.sendKeys("Hello Selenium!");
        Thread.sleep(2000);
        System.out.println(prompt.getText());
        prompt.accept();
        Thread.sleep(5000); // 5-sec pause after prompt alert
        // driver.quit();
    }
}
```

---

## Practical 11 — Dropdown, List Boxes, Buttons, Radio/Textboxes, Waits

### dropdown.java
```java
package prac11_stqa;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.support.ui.Select;
public class dropdown {
    public static void main(String[] args) {
        System.setProperty("Webdriver.chrome.driver",
                "D:\\Selenium\\Selenium\\chromedriver_win32\\chromedriver.exe");
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.facebook.com/r.php?r=101");
        Select month = new Select(driver.findElement(By.id("month")));
        // month.deselectByVisibleText("Oct");
        month.selectByIndex(2);
    }
}
```

### listbox.java
```java
package prac11_stqa;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.support.ui.Select;
import java.util.List;
public class listbox {
    public static void main(String[] args) throws InterruptedException {
        System.setProperty("Webdriver.chrome.driver",
                "D:\\Selenium\\Selenium\\chromedriver_win32\\chromedriver.exe");
        WebDriver driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.get("https://demoqa.com/select-menu");
        // Locate the list box
        Select cars = new Select(driver.findElement(By.id("cars")));
        if (cars.isMultiple()) {
            cars.selectByIndex(0);
            cars.selectByIndex(1);
            Thread.sleep(3000);
            cars.deselectAll();
        }
        // Print all options
        List<WebElement> options = cars.getOptions();
        System.out.println("Available options in the list box:");
        for (WebElement opt : options) {
            System.out.println(opt.getText());
        }
        Thread.sleep(5000);
        // driver.quit();
    }
}
```

### commandButton.java
```java
package prac11_stqa;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
public class commandButton {
    public static void main(String[] args) throws InterruptedException {
        System.setProperty("Webdriver.chrome.driver",
                "D:\\Selenium\\Selenium\\chromedriver_win32\\chromedriver.exe");
        WebDriver driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.get("https://practicetestautomation.com/practice-test-login/");
        // Enter username and password
        driver.findElement(By.id("username")).sendKeys("student");
        driver.findElement(By.id("password")).sendKeys("Password123");
        // Click the login button (command button)
        WebElement loginBtn = driver.findElement(By.id("submit"));
        loginBtn.click();
        System.out.println("Login button clicked successfully!");
        Thread.sleep(5000);
        // driver.quit();
    }
}
```

### RadioBtnAndTextBox.java
```java
package prac11_stqa;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
public class RadioBtnAndTextBox {
    public static void main(String[] args) throws InterruptedException {
        System.setProperty("Webdriver.chrome.driver",
                "D:\\Selenium\\Selenium\\chromedriver_win32\\chromedriver.exe");
        WebDriver driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.get("https://demoqa.com/automation-practice-form");
        // Text boxes
        driver.findElement(By.id("firstName")).sendKeys("sam");
        driver.findElement(By.id("lastName")).sendKeys("Lad");
        driver.findElement(By.id("userEmail")).sendKeys("sam@example.com");
        // Radio button
        WebElement maleRadio = driver.findElement(By.xpath("//label[text()='Male']"));
        maleRadio.click();
        System.out.println("Radio button selected");
        // Text box - mobile number
        driver.findElement(By.id("userNumber")).sendKeys("91234563210");
        System.out.println("Textbox details filled");
        Thread.sleep(5000);
        // driver.quit();
    }
}
```

---

## Practical 12 — Action Class
**File:** `ActionClass.java`

```java
package prac11_stqa;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.interactions.Actions;
public class ActionClass {
    public static void main(String[] args) throws InterruptedException {
        System.setProperty("Webdriver.chrome.driver",
                "D:\\Selenium\\Selenium\\chromedriver_win32\\chromedriver.exe");
        WebDriver driver = new ChromeDriver();
        driver.get("https://demoqa.com/buttons");
        driver.manage().window().maximize();
        Actions actions = new Actions(driver);
        // Double Click
        WebElement doubleClickBtn = driver.findElement(By.id("doubleClickBtn"));
        actions.doubleClick(doubleClickBtn).perform();
        System.out.println("Double Click performed");
        Thread.sleep(5000); // 5-sec pause after double click
        // Right Click
        WebElement rightClickBtn = driver.findElement(By.id("rightClickBtn"));
        actions.contextClick(rightClickBtn).perform();
        System.out.println("Right Click performed");
        Thread.sleep(5000); // 5-sec pause after right click
        // Move to Element and Click
        WebElement clickBtn = driver.findElement(By.xpath("//button[text()='Click Me']"));
        actions.moveToElement(clickBtn).click().perform();
        System.out.println("Single Click performed");
        Thread.sleep(5000); // 5-sec pause after single click
        // driver.quit();
    }
}
```

---

## Practical 13 — TestNG demos

### NewTest.java
```java
package TestNg;
import org.openqa.selenium.By;
import org.openqa.selenium.Keys;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.annotations.Test;
public class NewTest {
    public String baseUrl = "https://www.google.com/";
    String driverPath = "D:\\Selenium\\Selenium\\chromedriver_win32\\chromedriver.exe";
    public WebDriver driver;
    @Test
    public void f() throws InterruptedException {
        System.out.println("launching chrome browser");
        System.setProperty("Webdriver.chrome.driver", driverPath);
        driver = new ChromeDriver();
        driver.get(baseUrl);
        driver.findElement(By.name("q")).sendKeys("DES's NMITD",Keys.ENTER);
        Thread.sleep(2000);
    }
}
```

### NewTest2.java
```java
package TestNg;
import org.openqa.selenium.By;
import org.openqa.selenium.Keys;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.annotations.Test;
public class NewTest2 {
    @Test
    public void TestGoogle() throws InterruptedException {
        System.setProperty("Webdriver.chrome.driver",
                "D:\\Selenium\\Selenium\\chromedriver_win32\\chromedriver.exe");
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.google.com/");
        driver.findElement(By.name("q")).sendKeys("DES's NMITD", Keys.ENTER);
        Thread.sleep(2000);
    }
    @Test
    public void TestFacebook() throws InterruptedException {
        System.setProperty("Webdriver.chrome.driver",
                "D:\\Selenium\\Selenium\\chromedriver_win32\\chromedriver.exe");
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.facebook.com/");
        driver.findElement(By.name("email")).sendKeys("abc@gmail.com", Keys.ENTER);
        Thread.sleep(2000);
    }
}
```

### NewTest3.java (TestNG annotations example)
```java
package TestNg;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.AfterTest;
import org.testng.annotations.BeforeTest;
import org.testng.annotations.Test;
public class NewTest3 {
    public String baseUrl = "https://demo.guru99.com/test/newtours/";
    String driverPath = "D:\\Selenium\\Selenium\\chromedriver_win32\\chromedriver.exe";
    public WebDriver driver;
    @BeforeTest
    public void launchBrowser() {
        System.out.println("Launching Google Chrome browser");
        System.setProperty("Webdriver.chrome.driver","D:\\Selenium\\Selenium\\chromedriver_win32\\chromedriver.exe");
        driver = new ChromeDriver();
        driver.get(baseUrl);
    }
    @Test
    public void verifyHomepageTitle() {
        String expectedTitle = "Welcome: Mercury Tours";
        String actualTitle = driver.getTitle();
        Assert.assertEquals(actualTitle, expectedTitle);
    }
    @AfterTest
    public void terminateBrowser() {
        // driver.close();
    }
}
```

---

## Practical 14 — Data Driven Framework examples

### DataParametrization.java
```java
package TestNg;
import org.testng.annotations.Parameters;
import org.testng.annotations.Test;
public class DataParametrization {
    @Test
    @Parameters({ "Reviewer_1", "Reviewer_2" })
    public void check_if_shortlisted(String marks_1, String marks_2) {
        int m1 = Integer.parseInt(marks_1);
        int m2 = Integer.parseInt(marks_2);
        float average = (m1 + m2) / 2.0f;
        System.out.println("The average achieved by the writer is " + average);
        if (average >= 4) {
            System.out.println("The Writer is shortlisted");
        } else {
            System.out.println("The Writer is not shortlisted");
        }
    }
}
```

**testing XML:** `DataParametrization.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<suite name="DataParameterization">
  <test name="Data Parameterization Test Level">
    <parameter name="Reviewer_1" value="2" />
    <parameter name="Reviewer_2" value="3" />
    <classes>
      <class name="TestNg.DataParametrization" />
    </classes>
  </test>
</suite>
```

### DataDrivenTest.java (with @DataProvider)
```java
package TestNg;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;
public class DataDrivenTest {
    @Test(dataProvider = "loginData")
    public void loginTest(String username, String password) {
        // Set path of browser driver
        System.setProperty("webdriver.gecko.driver","D:\\geckodriver-v0.36.0-win64\\geckodriver.exe");
        // create instance of browser driver
        WebDriver driver = new FirefoxDriver();
        // load webpage
        driver.get("https://demo.guru99.com/test/newtours/");
        // locate username textbox
        WebElement user = driver.findElement(By.name("userName"));
        user.sendKeys(username);
        // locate password textbox
        WebElement passwrd = driver.findElement(By.name("password"));
        passwrd.sendKeys(password);
        // locate Submit button
        WebElement submit_btn = driver.findElement(By.name("submit"));
        submit_btn.click();
        // Validate login success
        String expectedTitle = "Login: Mercury Tours";
        String actualTitle = driver.getTitle();
        System.out.println("Page Title is: " + actualTitle);
        if (actualTitle.equals(expectedTitle)) {
            System.out.println("Login Test Passed");
        } else {
            System.out.println("Login Test Failed");
        }
        // Close the browser
        driver.quit();
    }
    @DataProvider(name = "loginData")
    public Object[][] getData() {
        // Here you can fetch data from an external source like Excel or CSV
        return new Object[][] { { "mercury", "mercury" }, { "tutorial", "tutorial" } };
    }
}
```

**DataDrivenTesting.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<suite name="Data Driven Suite" verbose="1">
  <test name="Data Driven Test">
    <classes>
      <class name="TestNg.DataDrivenTest"/>
    </classes>
  </test>
</suite>
```

### Cross-browser example: CrossBowserTest.java
```java
package TestNg;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.testng.annotations.BeforeTest;
import org.testng.annotations.Parameters;
import org.testng.annotations.Test;
public class CrossBowserTest {
    WebDriver driver;
    @BeforeTest
    @Parameters("browser")
    public void setup(String browser) throws Exception {
        if (browser.equalsIgnoreCase("firefox")) {
            // Set path of browser driver
            System.setProperty("webdriver.gecko.driver", "D:\\geckodriver-v0.36.0-win64\\geckodriver.exe");
            driver = new FirefoxDriver();
        } else if (browser.equalsIgnoreCase("chrome")) {
            System.setProperty("webdriver.chrome.driver","D:\\Selenium\\Selenium\\chromedriver_win32\\chromedriver.exe");
            // driver = new ChromeDriver(options);
            driver = new ChromeDriver();
        } else {
            throw new Exception("incorrect Browser");
        }
    }
    @Test
    public void PrintTitle() {
        driver.get("https://www.google.com/");
        String actualTitle = driver.getTitle();
        System.out.println("The Title is " + actualTitle);
    }
}
```

**CrossBrowserTest.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<suite name="Cross Browser Suite" thread-count="2" parallel="tests">
  <test name="ChromeTest">
    <parameter name="browser" value="chrome"/>
    <classes>
      <class name="TestNg.CrossBowserTest"/>
    </classes>
  </test>
  <test name="FirefoxTest">
    <parameter name="browser" value="firefox"/>
    <classes>
      <class name="TestNg.CrossBowserTest"/>
    </classes>
  </test>
</suite>
```

---

## Practical 15 — Validation Testing
**File:** `ValidationTesting.java`

```java
package prac11_15_stqa;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
public class ValidationTesting {
    public static void main(String[] args) throws InterruptedException {
        System.setProperty("Webdriver.chrome.driver",
                "D:\\Selenium\\Selenium\\chromedriver_win32\\chromedriver.exe");
        WebDriver driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.get("https://practicetestautomation.com/practice-test-login/");
        // Leave fields blank and click login
        WebElement loginBtn = driver.findElement(By.id("submit"));
        loginBtn.click();
        Thread.sleep(5000);
        System.out.println("Test 1: Blank fields validation tested.");
        // Enter invalid username and password
        driver.findElement(By.id("username")).sendKeys("Usersam");
        driver.findElement(By.id("password")).sendKeys("C24050");
        driver.findElement(By.id("submit")).click();
        Thread.sleep(5000);
        // Capture validation message
        WebElement errorMsg = driver.findElement(By.id("error"));
        String message = errorMsg.getText();
        System.out.println("Test 2: Error Message Displayed: " + message);
        // Enter valid credentials
        driver.findElement(By.id("username")).clear();
        driver.findElement(By.id("password")).clear();
        driver.findElement(By.id("username")).sendKeys("student");
        driver.findElement(By.id("password")).sendKeys("Password123");
        driver.findElement(By.id("submit")).click();
        Thread.sleep(5000);
        System.out.println("Test 3: Valid login successful. Validation passed.");
        // driver.quit();
    }
}
```

---

## Notes & Next Steps
- All driver paths are environment-specific; replace the `System.setProperty` values with the correct paths on your machine.
- Some snippets in the journal intentionally left `driver.quit()` commented — update them to close sessions as needed.
- If you'd like, I can:
  - produce individual `.java` and `.xml` files for download,
  - package everything in a zip ready to push to GitHub,
  - create a GitHub-friendly project structure (pom.xml / Gradle) and a polished `README.md` with run instructions.

---

_End of README file._
