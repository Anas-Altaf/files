# Comprehensive Documentation and Implementation Guide
### FAST University - BA Software Engineering

---

## Table of Contents
1. UI Testing Frameworks
2. Code Coverage and Testing Metrics
3. API Testing Implementation
4. Data-Driven Testing Framework
5. Advanced Testing Concepts
6. Best Practices and Guidelines

---

# 1. UI Testing Frameworks

## 1.1 Cucumber Framework Implementation

### Step 1: Setting up Cucumber
```xml
<!-- Add to pom.xml dependencies -->
<dependencies>
    <dependency>
        <groupId>io.cucumber</groupId>
        <artifactId>cucumber-java</artifactId>
        <version>7.0.0</version>
    </dependency>
    <dependency>
        <groupId>io.cucumber</groupId>
        <artifactId>cucumber-testng</artifactId>
        <version>7.0.0</version>
    </dependency>
</dependencies>
```

### Step 2: Creating Feature Files
```gherkin
# src/test/resources/features/login.feature
Feature: User Authentication System

Scenario: Successful Login with Valid Credentials
    Given I am on the login page
    When I enter username "admin@test.com"
    And I enter password "Password123"
    And I click the login button
    Then I should be redirected to dashboard
    And I should see welcome message "Welcome, Admin"

Scenario Outline: Login with Multiple Users
    Given I am on the login page
    When I enter username "<username>"
    And I enter password "<password>"
    Then I should see "<message>"

    Examples:
        | username          | password    | message             |
        | admin@test.com    | Password123 | Welcome, Admin      |
        | user@test.com     | User123     | Welcome, User       |
        | invalid@test.com  | wrong       | Invalid credentials |
```

### Step 3: Implementing Step Definitions
```java
// src/test/java/stepdefs/LoginSteps.java
public class LoginSteps {
    private WebDriver driver;
    private LoginPage loginPage;

    @Before
    public void setup() {
        driver = new ChromeDriver();
        loginPage = new LoginPage(driver);
    }

    @Given("I am on the login page")
    public void navigateToLoginPage() {
        driver.get("http://example.com/login");
    }

    @When("I enter username {string}")
    public void enterUsername(String username) {
        loginPage.setUsername(username);
    }

    @When("I enter password {string}")
    public void enterPassword(String password) {
        loginPage.setPassword(password);
    }

    @When("I click the login button")
    public void clickLogin() {
        loginPage.clickLoginButton();
    }

    @Then("I should be redirected to dashboard")
    public void verifyDashboard() {
        Assert.assertTrue(driver.getCurrentUrl().contains("/dashboard"));
    }

    @After
    public void tearDown() {
        driver.quit();
    }
}
```

## 1.2 TestNG Framework Implementation

### Step 1: Basic Test Structure
```java
public class LoginTest {
    private WebDriver driver;
    private LoginPage loginPage;

    @BeforeClass
    public void setupClass() {
        WebDriverManager.chromedriver().setup();
    }

    @BeforeMethod
    public void setupTest() {
        driver = new ChromeDriver();
        loginPage = new LoginPage(driver);
    }

    @Test(groups = {"smoke", "regression"})
    public void testValidLogin() {
        loginPage.navigateTo();
        loginPage.login("admin", "password");
        Assert.assertTrue(loginPage.isDashboardDisplayed());
    }

    @Test(dataProvider = "invalidLoginData")
    public void testInvalidLogin(String username, String password, String errorMessage) {
        loginPage.navigateTo();
        loginPage.login(username, password);
        Assert.assertEquals(loginPage.getErrorMessage(), errorMessage);
    }

    @DataProvider(name = "invalidLoginData")
    public Object[][] getInvalidLoginData() {
        return new Object[][] {
            {"invalid", "wrong", "Invalid credentials"},
            {"", "", "Username is required"},
            {"admin", "", "Password is required"}
        };
    }

    @AfterMethod
    public void tearDown() {
        if (driver != null) {
            driver.quit();
        }
    }
}
```

## 1.3 UI Element Selection Strategies

### CSS Selectors
```java
public class ElementSelectors {
    // Basic Selectors
    WebElement byId = driver.findElement(By.cssSelector("#loginButton"));
    WebElement byClass = driver.findElement(By.cssSelector(".login-form"));
    WebElement byAttribute = driver.findElement(By.cssSelector("[data-testid='submit']"));

    // Combinators
    WebElement childSelector = driver.findElement(By.cssSelector(".form-group > input"));
    WebElement descendantSelector = driver.findElement(By.cssSelector(".form-group input"));
    WebElement siblingSelector = driver.findElement(By.cssSelector("label + input"));

    // Pseudo-classes
    WebElement firstChild = driver.findElement(By.cssSelector("li:first-child"));
    WebElement nthChild = driver.findElement(By.cssSelector("li:nth-child(2)"));
    WebElement hover = driver.findElement(By.cssSelector("button:hover"));
}
```

### XPath Selectors
```java
public class XPathSelectors {
    // Basic XPath
    WebElement byId = driver.findElement(By.xpath("//*[@id='loginButton']"));
    WebElement byClass = driver.findElement(By.xpath("//*[@class='login-form']"));
    WebElement byText = driver.findElement(By.xpath("//button[text()='Login']"));

    // Advanced XPath
    WebElement containsText = driver.findElement(By.xpath("//div[contains(text(),'Welcome')]"));
    WebElement multipleAttributes = driver.findElement(By.xpath("//input[@type='text' and @name='username']"));
    WebElement dynamicElement = driver.findElement(By.xpath("//div[starts-with(@id,'dynamic-')]"));

    // Axes Navigation
    WebElement parent = driver.findElement(By.xpath("//input/parent::div"));
    WebElement following = driver.findElement(By.xpath("//label/following-sibling::input"));
    WebElement ancestor = driver.findElement(By.xpath("//input/ancestor::form"));
}
```

# 2. Code Coverage and Testing Metrics

## 2.1 Cyclomatic Complexity Analysis
```java
public class ComplexityExample {

    public double calculateDiscount(double price, boolean isPremium, int quantity) {
        double discount = 0;

        if (isPremium) { // Premium customer discount
            discount = price * 0.2;
        } else if (quantity > 10) { // Bulk purchase discount
            discount = price * 0.1;
        } else if (price > 1000) { // High-value purchase discount
            discount = price * 0.05;
        }

        return discount;
    }

    @Test
    public void testCalculateDiscount() {
        ComplexityExample example = new ComplexityExample();

        assertEquals(200, example.calculateDiscount(1000, true, 1), 0.01); // Premium customer
        assertEquals(100, example.calculateDiscount(1000, false, 11), 0.01); // Bulk purchase
        assertEquals(50, example.calculateDiscount(1000, false, 1), 0.01); // High-value purchase
        assertEquals(0, example.calculateDiscount(100, false, 1), 0.01); // No discount
    }
}
```

## 2.2 Branch Coverage Implementation
```java
public class UserValidator {
    public boolean validateUser(User user) {
        if (user == null) {
            return false;
        }

        if (user.getUsername() == null || user.getUsername().isEmpty()) {
            return false;
        }

        if (user.getPassword().length() < 8) {
            return false;
        }

        if (!user.getEmail().contains("@")) {
            return false;
        }

        return true;
    }

    @Test
    public void testValidateUser_BranchCoverage() {
        UserValidator validator = new UserValidator();

        // Null user
        assertFalse(validator.validateUser(null));

        // Empty username
        User userEmptyUsername = new User("", "password123", "email@test.com");
        assertFalse(validator.validateUser(userEmptyUsername));

        // Short password
        User userShortPassword = new User("username", "pass", "email@test.com");
        assertFalse(validator.validateUser(userShortPassword));

        // Invalid email
        User userInvalidEmail = new User("username", "password123", "invalidemail");
        assertFalse(validator.validateUser(userInvalidEmail));
    }
}
```

# 3. API Testing Implementation

## 3.1 REST Assured Setup and Configuration
```java
public class APITestBase {
    @BeforeClass
    public void setup() {
        RestAssured.baseURI = "https://api.example.com";
        RestAssured.port = 443;
        RestAssured.basePath = "/v1";

        RestAssured.config = RestAssured.config()
            .objectMapperConfig(new ObjectMapperConfig()
                .jackson2ObjectMapperFactory((type, s) -> new ObjectMapper()
                    .setDateFormat(new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSSZ"))
                    .setSerializationInclusion(JsonInclude.Include.NON_NULL)))
            .logConfig(new LogConfig()
                .enableLoggingOfRequestAndResponseIfValidationFails());
    }

    @BeforeMethod
    public void beforeMethod() {
        RestAssured.authentication = basic("username", "password");
    }
}
```

## 3.2 Comprehensive API Tests
```java
public class UserAPITests extends APITestBase {

    @Test
    public void testGetUser() {
        given()
            .pathParam("id", 1)
            .header("Accept", "application/json")
        .when()
            .get("/users/{id}")
        .then()
            .statusCode(200)
            .contentType(ContentType.JSON)
            .body("id", equalTo(1))
            .body("username", notNullValue())
            .body("email", matchesPattern("^[A-Za-z0-9+_.-]+@(.+)$"));
    }

    @Test
    public void testCreateUser() {
        User user = new User("newuser", "user@test.com");

        given()
            .contentType(ContentType.JSON)
            .body(user)
        .when()
            .post("/users")
        .then()
            .statusCode(201)
            .header("Location", matchesPattern("/users/\\d+"))
            .body("id", notNullValue())
            .body("username", equalTo(user.getUsername()))
            .extract()
            .path("id");
    }

    @Test
    public void testUpdateUser() {
        User updatedUser = new User("updateduser", "updated@test.com");

        given()
            .pathParam("id", 1)
            .contentType(ContentType.JSON)
            .body(updatedUser)
        .when()
            .put("/users/{id}")
        .then()
            .statusCode(200)
            .body("username", equalTo(updatedUser.getUsername()))
            .body("email", equalTo(updatedUser.getEmail()));
    }

    @Test
    public void testDeleteUser() {
        given()
            .pathParam("id", 1)
        .when()
            .delete("/users/{id}")
        .then()
            .statusCode(204);
    }

    @Test
    public void testUserNotFound() {
        given()
            .pathParam("id", 999)
        .when()
            .get("/users/{id}")
        .then()
            .statusCode(404)
            .body("error", equalTo("User not found"))
            .body("timestamp", notNullValue());
    }
}
```

# 4. Data-Driven Testing Framework

## 4.1 CSV Data Provider Implementation
```java
public class CSVDataProvider {

    @DataProvider(name = "userTestData")
    public Object[][] getUserTestData() throws IOException {
        return readCSVFile("src/test/resources/testdata/users.csv");
    }

    private Object[][] readCSVFile(String fileName) throws IOException {
        List<Object[]> records = new ArrayList<>();

        try (CSVReader csvReader = new CSVReader(new FileReader(fileName))) {
            String[] values;
            csvReader.readNext(); // Skip header row

            while ((values = csvReader.readNext()) != null) {
                records.add(values);
            }
        }

        return records.toArray(new Object[0][]);
    }
}

// Test class using CSV data
public class UserRegistrationTest extends TestBase {

    @Test(dataProvider = "userTestData", dataProviderClass = CSVDataProvider.class)
    public void testUserRegistration(String username, String email, String password, String expectedResult) {
        UserRegistrationPage registrationPage = new UserRegistrationPage(driver);

        registrationPage.registerUser(username, email, password);

        if ("SUCCESS".equals(expectedResult)) {
            Assert.assertTrue(registrationPage.isRegistrationSuccessful());
        } else {
            Assert.assertEquals(registrationPage.getErrorMessage(), expectedResult);
        }
    }
}
```

## 4.2 Excel Data Provider Implementation
```java
public class ExcelDataProvider {

    @DataProvider(name = "productData")
    public Object[][] getProductData() throws IOException {
        return readExcelFile("src/test/resources/testdata/products.xlsx", "Sheet1");
    }

    private Object[][] readExcelFile(String fileName, String sheetName) throws IOException {
        FileInputStream fis = new FileInputStream(fileName);
        XSSFWorkbook workbook = new XSSFWorkbook(fis);
        XSSFSheet sheet = workbook.getSheet(sheetName);

        int rowCount = sheet.getPhysicalNumberOfRows();
        int colCount = sheet.getRow(0).getLastCellNum();

        Object[][] data = new Object[rowCount - 1][colCount];

        for (int i = 1; i < rowCount; i++) { // Start from 1 to skip header
            XSSFRow row = sheet.getRow(i);
            for (int j = 0; j < colCount; j++) {
                XSSFCell cell = row.getCell(j);
                data[i - 1][j] = getCellValue(cell);
            }
        }

        workbook.close();
        fis.close();
        return data;
    }

    private Object getCellValue(XSSFCell cell) {
        if (cell == null) return "";
        switch (cell.getCellType()) {
            case STRING:
                return cell.getStringCellValue();
            case NUMERIC:
                return cell.getNumericCellValue();
            case BOOLEAN:
                return cell.getBooleanCellValue();
            default:
                return "";
        }
    }
}
```

# 5. Advanced Testing Concepts

## 5.1 Page Object Model (POM) Implementation
```java
public class BasePage {
    protected WebDriver driver;
    protected WebDriverWait wait;

    public BasePage(WebDriver driver) {
        this.driver = driver;
        this.wait = new WebDriverWait(driver, Duration.ofSeconds(20));
        PageFactory.initElements(driver, this);
    }

    protected WebElement waitForElement(By locator) {
        return wait.until(ExpectedConditions.presenceOfElementLocated(locator));
    }

    protected void clickElement(By locator) {
        waitForElement(locator).click();
    }

    protected void setText(By locator, String text) {
        WebElement element = waitForElement(locator);
        element.clear();
        element.sendKeys(text);
    }
}
```

## 5.2 Implementing Test Listeners
```java
public class TestListener implements ITestListener {
    private ExtentReports extent;
    private ExtentTest test;

    @Override
    public void onStart(ITestContext context) {
        extent = new ExtentReports();
        ExtentSparkReporter spark = new ExtentSparkReporter("test-output/report.html");
        extent.attachReporter(spark);
    }

    @Override
    public void onTestStart(ITestResult result) {
        test = extent.createTest(result.getMethod().getMethodName());
    }

    @Override
    public void onTestSuccess(ITestResult result) {
        test.log(Status.PASS, "Test passed");
    }

    @Override
    public void onTestFailure(ITestResult result) {
        test.log(Status.FAIL, "Test failed");
        test.log(Status.FAIL, result.getThrowable());

        String screenshotPath = takeScreenshot(result.getMethod().getMethodName());
        if (screenshotPath != null) {
            test.addScreenCaptureFromPath(screenshotPath);
        }
    }

    private String takeScreenshot(String methodName) {
        try {
            File source = ((TakesScreenshot) BaseTest.getDriver()).getScreenshotAs(OutputType.FILE);
            String screenshotPath = "test-output/screenshots/" + methodName + ".png";
            File destination = new File(screenshotPath);
            FileUtils.copyFile(source, destination);
            return screenshotPath;
        } catch (IOException e) {
            return null;
        }
    }
}
```

## 5.3 Parallel Test Execution
```xml
<!-- testng.xml configuration for parallel execution -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Test Suite" parallel="methods" thread-count="3">
    <test name="Parallel Tests">
        <classes>
            <class name="tests.LoginTest" />
            <class name="tests.ProductTest" />
            <class name="tests.UserTest" />
        </classes>
    </test>
</suite>
```

```java
// ThreadLocal WebDriver implementation
public class DriverFactory {
    private static ThreadLocal<WebDriver> driver = new ThreadLocal<>();

    public static WebDriver getDriver() {
        if (driver.get() == null) {
            driver.set(new ChromeDriver()); // Initialize WebDriver if not set
        }
        return driver.get();
    }

    public static void removeDriver() {
        if (driver.get() != null) {
            driver.get().quit(); // Quit WebDriver
            driver.remove();     // Remove from ThreadLocal
        }
    }
}

// Test Base using ThreadLocal driver
public class TestBase {
    @BeforeMethod
    public void setup() {
        DriverFactory.getDriver().get("https://example.com");
    }

    @AfterMethod
    public void tearDown() {
        DriverFactory.removeDriver();
    }

    protected WebDriver getDriver() {
        return DriverFactory.getDriver();
    }
}
```

## 5.4 Test Configuration Management
```java
public class ConfigurationManager {
    private static Properties properties;

    static {
        try {
            properties = new Properties();
            FileInputStream fis = new FileInputStream("src/test/resources/config.properties");
            properties.load(fis);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static String getProperty(String key) {
        return properties.getProperty(key);
    }

    public static String getProperty(String key, String defaultValue) {
        return properties.getProperty(key, defaultValue);
    }
}
```

Example `config.properties` file:
```
browser=chrome
baseUrl=https://example.com
implicit.wait=10
explicit.wait=20
screenshot.path=test-output/screenshots
```

# 6. Best Practices and Guidelines

1. **Test Organization**
   - Group related tests in test classes.
   - Use meaningful test names.
   - Follow naming conventions.
   - Maintain test independence.

2. **Code Quality**
   - Adhere to the DRY (Don't Repeat Yourself) principle.
   - Implement proper exception handling.
   - Use logging effectively.
   - Include meaningful comments.

3. **Test Data Management**
   - Externalize test data.
   - Utilize data providers.
   - Version control test data.
   - Clean up test data after execution.

4. **Reporting**
   - Generate detailed test reports.
   - Include screenshots for failures.
   - Track test metrics.
   - Maintain test execution history.

This documentation provides a comprehensive guide for implementing various testing concepts and frameworks. Each section includes practical examples and best practices to ensure effective test automation implementation.
