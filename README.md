# android-testing-guide [![Join the chat at https://gitter.im/android-testing-guide/Lobby](https://badges.gitter.im/android-testing-guide/Lobby.svg)](https://gitter.im/android-testing-guide/Lobby?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
> Complete reference for Android Testing with examples.

## Contents

- [Introduction](#introduction)
    - [Why testing?](#why-testing)
    - [Why unit test?](#why-unit-test)
    - [Instrumented tests](#instrumented-tests)
- [Local Tests](#local-tests)
    - [JUnit basics](#junit-basics)
    - [Beyond JUnit basics](#beyond-junit-basics)
    - [Local test setup and execution](#)
    - [Adding local tests and failure](#)
    - [Assertions](#)
    - [Hamcrest](#hamcrest)
    - [Rules](#rules)
- [Android](#android)
    - [Android test rules](#android-test-rules)
        - [Rule to test Android Activity](#rule-to-test-android-activity)
        - [Rule to test Android Service](#rule-to-test-android-service)
    - [Android instrumented tests](#android-instrumented-tests)
    - [Test filtering](#test-filtering)
    - [Espresso](#espresso)
    - [Robolectric](#robolectric)
    - [Robotium](#robotium)
    - [UI testing and UI Automator](#ui-testing-and-ui-automator)
    - [MonkeyRunner](#monkeyrunner)
- [References](#references)

## Introduction

### Why testing?

* Testing forces you to think in a different way and implicitly makes your code cleaner in the process.
* You feel more confident about your code if it has tests.
* Shiny green status bars and cool reports detailing how much of your code is covered are both consequences of writing tests.
* Regression testing is made a lot easier, as automated tests would pick up the bugs first.

### Why unit test?

A unit test generally exercises the functionality of the smallest possible unit of code (which could be a method, class, or component) in a repeatable way.

Tools that are used to do this testing:
* [JUnit](http://junit.org/junit4/) – normal test assertions.
* [Mockito](http://mockito.org/) – mocking out other classes that are not under test.
* [PowerMock](https://github.com/jayway/powermock) – mocking out static classes such as Android Environment class etc.

### Instrumented tests

A UI Test or Instrumentation Test mocks typical user interactions with your app. Clicking on buttons, typing in text are some of the things UI Tests can complete.

* [Espresso](https://google.github.io/android-testing-support-library/docs/espresso/) –  Used for testing within your app, selecting items, making sure something is visible.
* [UIAutomator](https://developer.android.com/training/testing/ui-testing/uiautomator-testing.html) – Used for testing interaction between different apps.

There are other tools that are available for this kind of testing such as [Robotium](http://robotium.com/), [Appium](http://appium.io/), [Calabash](http://calaba.sh/), [Robolectric](http://robolectric.org/).

## Local Tests

### JUnit basics

[Calculator.java](https://github.com/ravidsrk/android-testing-guide/blob/master/SampleApp/app/src/main/java/in/ravidsrk/sample/Calculator.java)

```java
public class Calculator {

    public int add(int op1, int op2) {
        return op1 + op2;
    }

    public int diff(int op1, int op2) {
        return op1 - op2;
    }

    public double div(int op1, int op2) {
        // if (op2 == 0) return 0;
        return op1 / op2;
    }
}
```

[CalculatorTest.java](https://github.com/ravidsrk/android-testing-guide/blob/master/SampleApp/app/src/test/java/in/ravidsrk/sample/CalculatorTest.java)
```java
public class CalculatorTest {

    private Calculator calculator;

    @Before
    public void setup() {
        calculator = new Calculator();
        System.out.println("Ready for testing!");
    }

    @After
    public void cleanup() {
        System.out.println("Done with unit test!");
    }

    @BeforeClass
    public static void testClassSetup() {
        System.out.println("Getting test class ready");
    }

    @AfterClass
    public static void testClassCleanup() {
        System.out.println("Done with tests");
    }

    @Test
    public void testAdd() {
        calculator = new Calculator();
        int total = calculator.add(4, 5);
        assertEquals("Calculator is not adding correctly", 9, total);
    }

    @Test
    public void testDiff() {
        calculator = new Calculator();
        int total = calculator.diff(9, 2);
        assertEquals("Calculator is not subtracting correctly", 7, total);
    }

    @Test
    public void testDiv() {
        calculator = new Calculator();
        double total = calculator.div(9, 3);
        assertEquals("Calculator is not dividing correctly", 3.0, total, 0.0);
    }
}

```

### Beyond JUnit basics

[CalculatorTest.java](https://github.com/ravidsrk/android-testing-guide/blob/master/SampleApp/app/src/test/java/in/ravidsrk/sample/CalculatorTest.java#L62)

```java
@Ignore
@Test(expected = java.lang.ArithmeticException.class)
public void testDivWithZeroDivisor() {
    calculator = new Calculator();
    double total = calculator.div(9, 0);
    assertEquals("Calculator is not handling division by zero correctly", 0.0, total, 0.0);
}
```
### Local test setup and execution
### Adding local tests and failure
### Assertions
### Hamcrest

[HamcrestTest.java](https://github.com/ravidsrk/android-testing-guide/blob/master/SampleApp/app/src/test/java/in/ravidsrk/sample/HamcrestTest.java)

```java
public class HamcrestTest {

    @Test
    public void testWithAsserts() {
        List<String> list = generateStingList();
        assertTrue(list.contains("android"));
        assertTrue(list.contains("context"));
        assertTrue(list.size() > 4);
        assertTrue(list.size() < 13);
    }

    @Test
    public void testWithBigAssert() {
        List<String> list = generateStingList();
        assertTrue(list.contains("android") && list.contains("context") && list.size() > 3 && list.size() < 12);
    }

    @Test
    public void testWithHamcrest() {
        List<String> list = generateStingList();
        assertThat(list, (hasItems("android", "context")));
        assertThat(list, allOf(hasSize(greaterThan(3)), hasSize(lessThan(12))));
    }

    @Test
    public void testFailureWithAsserts() {
        List<String> list = generateStingList();
        assertTrue(list.contains("android"));
        assertTrue(list.contains("service"));
        assertTrue(list.size() > 3);
        assertTrue(list.size() < 12);
    }

    @Test
    public void testFailureWithHamcrest() {
        List<String> list = generateStingList();
        assertThat(list, (hasItems("android", "service")));
        assertThat(list, allOf(hasSize(greaterThan(3)), hasSize(lessThan(12))));
    }

    @Test
    public void testTypeSafety() {
        // assertThat("123", equalTo(123));
        // assertThat(123, equalTo("123"));
    }

    private List<String> generateStingList() {
        String[] sentence = {"android", "context", "service", "manifest", "layout", "resource", "broadcast", "receiver", "gradle"};
        return Arrays.asList(sentence);
    }
}
```

### Rules

[CalculatorWithTestName.java](https://github.com/ravidsrk/android-testing-guide/blob/master/SampleApp/app/src/test/java/in/ravidsrk/sample/CalculatorWithTestName.java)

```java
public class CalculatorWithTestName {

    @Rule
    public TestName name = new TestName();

    @Test
    public void testAdd() {
        Calculator calculator = new Calculator();
        int total = calculator.add(4, 5);
        assertEquals(name.getMethodName() + " adding incorrectly", 9, total);
    }

    @Test
    public void testDiff() {
        Calculator calculator = new Calculator();
        int total = calculator.diff(12, 7);
        assertEquals(name.getMethodName() + " subtracting incorrectly", 5, total);
    }
}
```

## Android

### Android test rules

#### Rule to test Android Activity

[MainActivityTestRule.java](https://github.com/ravidsrk/android-testing-guide/blob/master/SampleApp/app/src/androidTest/java/in/ravidsrk/sample/MainActivityTestRule.java)

```java
public class MainActivityTestRule<A extends Activity> extends ActivityTestRule<A> {

    public MainActivityTestRule(Class<A> activityClass) {
        super(activityClass);
    }
    @Override
    protected Intent getActivityIntent() {
        Log.e("MainActivityTestRule", "Prepare the activity's intent");
        return super.getActivityIntent();
    }

    @Override
    protected void beforeActivityLaunched() {
        Log.e("MainActivityTestRule", "Execute before the activity is launched");
        super.beforeActivityLaunched();
    }

    @Override
    protected void afterActivityLaunched() {
        Log.e("MainActivityTestRule", "Execute after the activity has been launched");
        super.afterActivityLaunched();
    }

    @Override
    protected void afterActivityFinished() {
        Log.e("MainActivityTestRule", "Cleanup after it has finished");
        super.afterActivityFinished();
    }

    @Override
    public A launchActivity(Intent startIntent) {
        Log.e("MainActivityTestRule", "Launching the activity");
        return super.launchActivity(startIntent);
    }
}
```

#### Rule to test Android Service

[SampleServiceTestRule.java](https://github.com/ravidsrk/android-testing-guide/blob/master/SampleApp/app/src/androidTest/java/in/ravidsrk/sample/SampleServiceTest.java)

```java
public class SampleServiceTestRule extends ServiceTestRule {

    @Override
    public void startService(Intent intent) throws TimeoutException {
        Log.e("SampleServiceTestRule", "start the service");
        super.startService(intent);
    }

    @Override
    public IBinder bindService(Intent intent) throws TimeoutException {
        Log.e("SampleServiceTestRule", "binding the service");
        return super.bindService(intent);
    }

    @Override
    protected void beforeService() {
        Log.e("SampleServiceTestRule", "work before the service starts");
        super.beforeService();
    }

    @Override
    protected void afterService() {
        Log.e("SampleServiceTestRule", "work after the service has started");
        super.afterService();
    }
}
```

### Android instrumented tests
#### Testing Android Activity

[MainActivityTest.java](https://github.com/ravidsrk/android-testing-guide/blob/master/SampleApp/app/src/androidTest/java/in/ravidsrk/sample/MainActivityTest.java)

```java
@RunWith(AndroidJUnit4.class)
public class MainActivityTest {

    @Rule
    public MainActivityTestRule<MainActivity> mainActivityActivityTestRule = new MainActivityTestRule<MainActivity>(MainActivity.class);

    @Test
    public void testUI() {
        Activity activity = mainActivityActivityTestRule.getActivity();
        assertNotNull(activity.findViewById(R.id.text_hello));
        TextView helloView = (TextView) activity.findViewById(R.id.text_hello);
        assertTrue(helloView.isShown());
        assertEquals("Hello World!", helloView.getText());
        assertEquals(InstrumentationRegistry.getTargetContext().getString(R.string.hello_world), helloView.getText());
        assertNull(activity.findViewById(android.R.id.button1));
    }
}
```

#### Testing Android Service

[SampleServiceTest](https://github.com/ravidsrk/android-testing-guide/blob/master/SampleApp/app/src/androidTest/java/in/ravidsrk/sample/SampleServiceTest.java)

```java
@RunWith(AndroidJUnit4.class)
public class SampleServiceTest {

    @Rule
    public SampleServiceTestRule myServiceRule = new SampleServiceTestRule();

    @Test
    public void testService() throws TimeoutException {
        myServiceRule.startService(new Intent(InstrumentationRegistry.getTargetContext(), SampleService.class));
    }

    @Test
    public void testBoundService() throws TimeoutException {
        IBinder binder = myServiceRule.bindService(
                new Intent(InstrumentationRegistry.getTargetContext(), SampleService.class));
        SampleService service = ((SampleService.LocalBinder) binder).getService();
        // Do work with the service
        assertNotNull("Bound service is null", service);
    }
}
```

### Test filtering

[MainActivityTest.java](https://github.com/ravidsrk/android-testing-guide/blob/master/SampleApp/app/src/androidTest/java/in/ravidsrk/sample/MainActivityTest.java#L61)

```java
@Test
@RequiresDevice
public void testRequiresDevice() {
    Log.d("Test Filters", "This test requires a device");
    Activity activity = activityTestRule.getActivity();
    assertNotNull("MainActivity is not available", activity);
}

@Test
@SdkSuppress(minSdkVersion = 30)
public void testMinSdkVersion() {
    Log.d("Test Filters", "Checking for min sdk >= 30");
    Activity activity = activityTestRule.getActivity();
    assertNotNull("MainActivity is not available", activity);
}

@Test
@SdkSuppress(minSdkVersion = Build.VERSION_CODES.LOLLIPOP)
public void testMinBuild() {
    Log.d("Test Filters", "Checking for min build > Lollipop");
    Activity activity = activityTestRule.getActivity();
    assertNotNull("MainActivity is not available", activity);
}

@Test
@SmallTest
public void testSmallTest() {
    Log.d("Test Filters", "this is a small test");
    Activity activity = activityTestRule.getActivity();
    assertNotNull("MainActivity is not available", activity);
}

@Test
@LargeTest
public void testLargeTest() {
    Log.d("Test Filters", "This is a large test");
    Activity activity = activityTestRule.getActivity();
    assertNotNull("MainActivity is not available", activity);
}
```

### Espresso

[MainActivityTest.java](https://github.com/ravidsrk/android-testing-guide/blob/master/SampleApp/app/src/androidTest/java/in/ravidsrk/sample/MainActivityTest.java#L134)

```java
@Test
public void testEspresso() {
    ViewInteraction interaction =
            onView(allOf(withId(R.id.editText),
                    withText("this is a test"),
                    hasFocus()));
    interaction.perform(replaceText("how about some new text"));
    ViewInteraction interaction2 =
            onView(allOf(withId(R.id.editText),
                    withText("how about some new text")));
    interaction2.check(matches(hasFocus()));
}

@Test
public void testEspressoSimplified() {
    onView(allOf(withId(R.id.editText),
            withText("this is a test"),
            hasFocus())).perform(replaceText("how about some new text"));
    onView(allOf(withId(R.id.editText),
            withText("how about some new text"))).check(matches(hasFocus()));
}

```
### Robolectric

[MainActivityRoboelectricTest.java](https://github.com/ravidsrk/android-testing-guide/blob/master/SampleApp/app/src/test/java/in/ravidsrk/sample/MainActivityRoboelectricTest.java)

```java
@RunWith(RobolectricGradleTestRunner.class)
@Config(constants = BuildConfig.class)
public class MainActivityRoboelectricTest {

    private MainActivity activity;

    @Before
    public void setup() {
        activity = Robolectric.setupActivity(MainActivity.class);
    }

    @Test
    public void clickButton() {
        Button button = (Button) activity.findViewById(R.id.button);
        assertNotNull("test button could not be found", button);
        assertTrue("button does not contain text 'Click Me!'", "Click Me".equals(button.getText()));
    }

}
```

### Robotium

[MainActivityRobotiumTest.java](https://github.com/ravidsrk/android-testing-guide/blob/master/SampleApp/app/src/androidTest/java/in/ravidsrk/sample/MainActivityRobotiumTest.java)

```java
public class MainActivityRobotiumTest {
    private Solo solo;

    @Rule
    public MainActivityRobotiumTestRule<MainActivity> mActivityRule = new MainActivityRobotiumTestRule<>(MainActivity.class);

    public void setUp() {
        solo = new Solo(mActivityRule.getInstrumentation(), mActivityRule.getActivity());
    }

    public void tearDown() {
        solo.finishOpenedActivities();
    }

    @Test
    public void testPushClickMe() {
        solo.waitForActivity(MainActivity.class);
        solo.assertCurrentActivity("MainActivity is not displayed", MainActivity.class);
        assertTrue("This is a test in EditText is not displayed",
                solo.searchText("this is a test"));
        solo.clickOnButton("Click Me");
        assertTrue("You clicked me text is not displayed in the EditText",
                solo.searchText("you clicked me!"));
    }
}
```

[MainActivityRobotiumTestRule.java](https://github.com/ravidsrk/android-testing-guide/blob/master/SampleApp/app/src/androidTest/java/in/ravidsrk/sample/MainActivityRobotiumTestRule.java)

```java
@Beta
public class MainActivityRobotiumTestRule<T extends Activity> extends UiThreadTestRule {

    private static final String TAG = "InstrumentationRule";
    private final Class<T> mActivityClass;

    public Instrumentation getInstrumentation() {
        return mInstrumentation;
    }

    private Instrumentation mInstrumentation;
    private boolean mInitialTouchMode = false;
    private boolean mLaunchActivity = false;
    private T mActivity;

    /**
     * Similar to {@link #MainActivityRobotiumTestRule(Class, boolean, boolean)} but with "touch mode" disabled.
     *
     * @param activityClass    The activity under test. This must be a class in the instrumentation
     *                         targetPackage specified in the AndroidManifest.xml
     * @see MainActivityRobotiumTestRule#MainActivityRobotiumTestRule(Class, boolean, boolean)
     */
    public MainActivityRobotiumTestRule(Class<T> activityClass) {
        this(activityClass, false);
    }

    /**
     * Similar to {@link #MainActivityRobotiumTestRule(Class, boolean, boolean)} but defaults to launch the
     * activity under test once per
     * <a href="http://junit.org/javadoc/latest/org/junit/Test.html"><code>Test</code></a> method.
     * It is launched before the first
     * <a href="http://junit.sourceforge.net/javadoc/org/junit/Before.html"><code>Before</code></a>
     * method, and terminated after the last
     * <a href="http://junit.sourceforge.net/javadoc/org/junit/After.html"><code>After</code></a>
     * method.
     *
     * @param activityClass    The activity under test. This must be a class in the instrumentation
     *                         targetPackage specified in the AndroidManifest.xml
     * @param initialTouchMode true if the Activity should be placed into "touch mode" when started
     * @see MainActivityRobotiumTestRule#MainActivityRobotiumTestRule(Class, boolean, boolean)
     */
    public MainActivityRobotiumTestRule(Class<T> activityClass, boolean initialTouchMode) {
        this(activityClass, initialTouchMode, true);
    }

    /**
     * Creates an {@link MainActivityRobotiumTestRule} for the Activity under test.
     *
     * @param activityClass    The activity under test. This must be a class in the instrumentation
     *                         targetPackage specified in the AndroidManifest.xml
     * @param initialTouchMode true if the Activity should be placed into "touch mode" when started
     * @param launchActivity   true if the Activity should be launched once per
     *                         <a href="http://junit.org/javadoc/latest/org/junit/Test.html">
     *                         <code>Test</code></a> method. It will be launched before the first
     *                         <a href="http://junit.sourceforge.net/javadoc/org/junit/Before.html">
     *                         <code>Before</code></a> method, and terminated after the last
     *                         <a href="http://junit.sourceforge.net/javadoc/org/junit/After.html">
     *                         <code>After</code></a> method.
     */
    public MainActivityRobotiumTestRule(Class<T> activityClass, boolean initialTouchMode,
                              boolean launchActivity) {
        mActivityClass = activityClass;
        mInitialTouchMode = initialTouchMode;
        mLaunchActivity = launchActivity;
        mInstrumentation = InstrumentationRegistry.getInstrumentation();
    }

    /**
     * Override this method to set up Intent as if supplied to
     * {@link android.content.Context#startActivity}.
     * <p>
     * The default Intent (if this method returns null or is not overwritten) is:
     * action = {@link Intent#ACTION_MAIN}
     * flags = {@link Intent#FLAG_ACTIVITY_NEW_TASK}
     * All other intent fields are null or empty.
     *
     * @return The Intent as if supplied to {@link android.content.Context#startActivity}.
     */
    protected Intent getActivityIntent() {
        return new Intent(Intent.ACTION_MAIN);
    }

    /**
     * Override this method to execute any code that should run before your {@link Activity} is
     * created and launched.
     * This method is called before each test method, including any method annotated with
     * <a href="http://junit.sourceforge.net/javadoc/org/junit/Before.html"><code>Before</code></a>.
     */
    protected void beforeActivityLaunched() {
        // empty by default
    }

    /**
     * Override this method to execute any code that should run after your {@link Activity} is
     * launched, but before any test code is run including any method annotated with
     * <a href="http://junit.sourceforge.net/javadoc/org/junit/Before.html"><code>Before</code></a>.
     * <p>
     * Prefer
     * <a href="http://junit.sourceforge.net/javadoc/org/junit/Before.html"><code>Before</code></a>
     * over this method. This method should usually not be overwritten directly in tests and only be
     * used by subclasses of MainActivityRobotiumTestRule to get notified when the activity is created and
     * visible but test runs.
     */
    protected void afterActivityLaunched() {
        // empty by default
    }

    /**
     * Override this method to execute any code that should run after your {@link Activity} is
     * finished.
     * This method is called after each test method, including any method annotated with
     * <a href="http://junit.sourceforge.net/javadoc/org/junit/After.html"><code>After</code></a>.
     */
    protected void afterActivityFinished() {
        // empty by default
    }

    /**
     * @return The activity under test.
     */
    public T getActivity() {
        if (mActivity == null) {
            Log.w(TAG, "Activity wasn't created yet");
        }
        return mActivity;
    }

    @Override
    public Statement apply(final Statement base, Description description) {
        return new ActivityStatement(super.apply(base, description));
    }

    /**
     * Launches the Activity under test.
     * <p>
     * Don't call this method directly, unless you explicitly requested not to launch the Activity
     * manually using the launchActivity flag in
     * {@link MainActivityRobotiumTestRule#MainActivityRobotiumTestRule(Class, boolean, boolean)}.
     * <p>
     * Usage:
     * <pre>
     *    &#064;Test
     *    public void customIntentToStartActivity() {
     *        Intent intent = new Intent(Intent.ACTION_PICK);
     *        mActivity = mActivityRule.launchActivity(intent);
     *    }
     * </pre>
     * @param startIntent The Intent that will be used to start the Activity under test. If
     *                    {@code startIntent} is null, the Intent returned by
     *                    {@link MainActivityRobotiumTestRule#getActivityIntent()} is used.
     * @return the Activity launched by this rule.
     * @see MainActivityRobotiumTestRule#getActivityIntent()
     */
    public T launchActivity(@Nullable Intent startIntent) {
        // set initial touch mode
        mInstrumentation.setInTouchMode(mInitialTouchMode);

        final String targetPackage = mInstrumentation.getTargetContext().getPackageName();
        // inject custom intent, if provided
        if (null == startIntent) {
            startIntent = getActivityIntent();
            if (null == startIntent) {
                Log.w(TAG, "getActivityIntent() returned null using default: " +
                        "Intent(Intent.ACTION_MAIN)");
                startIntent = new Intent(Intent.ACTION_MAIN);
            }
        }
        startIntent.setClassName(targetPackage, mActivityClass.getName());
        startIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        Log.d(TAG, String.format("Launching activity %s",
                mActivityClass.getName()));

        beforeActivityLaunched();
        // The following cast is correct because the activity we're creating is of the same type as
        // the one passed in
        mActivity = mActivityClass.cast(mInstrumentation.startActivitySync(startIntent));

        mInstrumentation.waitForIdleSync();

        afterActivityLaunched();
        return mActivity;
    }

    // Visible for testing
    void setInstrumentation(Instrumentation instrumentation) {
        mInstrumentation = checkNotNull(instrumentation, "instrumentation cannot be null!");
    }

    void finishActivity() {
        if (mActivity != null) {
            mActivity.finish();
            mActivity = null;
        }
    }

    /**
     * <a href="http://junit.org/apidocs/org/junit/runners/model/Statement.html">
     * <code>Statement</code></a> that finishes the activity after the test was executed
     */
    private class ActivityStatement extends Statement {

        private final Statement mBase;

        public ActivityStatement(Statement base) {
            mBase = base;
        }

        @Override
        public void evaluate() throws Throwable {
            try {
                if (mLaunchActivity) {
                    mActivity = launchActivity(getActivityIntent());
                }
                mBase.evaluate();
            } finally {
                finishActivity();
                afterActivityFinished();
            }
        }
    }

}
```
### UI testing and UI Automator

[MainActivityTest](https://github.com/ravidsrk/android-testing-guide/blob/master/SampleApp/app/src/androidTest/java/in/ravidsrk/sample/MainActivityTest.java#L101)

```java
@Test
public void testPressBackButton() {
    UiDevice.getInstance(InstrumentationRegistry.getInstrumentation()).pressBack();
}

@Test
@Ignore
public void testUiDevice() throws RemoteException {
    UiDevice device = UiDevice.getInstance(
            InstrumentationRegistry.getInstrumentation());
    if (device.isScreenOn()) {
        device.setOrientationLeft();
        device.openNotification();
    }
}

@Test
public void testUiAutomatorAPI() throws UiObjectNotFoundException, InterruptedException {
    UiDevice device = UiDevice.getInstance(InstrumentationRegistry.getInstrumentation());

    UiSelector editTextSelector = new UiSelector().className("android.widget.EditText").text("this is a test").focusable(true);
    UiObject editTextWidget = device.findObject(editTextSelector);
    editTextWidget.setText("this is new text");

    Thread.sleep(2000);

    UiSelector buttonSelector = new UiSelector().className("android.widget.Button").text("Click Me").clickable(true);
    UiObject buttonWidget = device.findObject(buttonSelector);
    buttonWidget.click();

    Thread.sleep(2000);

}
```

### MonkeyRunner

[sampletest.py](https://github.com/ravidsrk/android-testing-guide/blob/master/SampleApp/sampletest.py)

```python
# Imports the monkeyrunner modules
from com.android.monkeyrunner import MonkeyRunner, MonkeyDevice, MonkeyImage

# Alert the user a MonkeyRunner script is about to execute
MonkeyRunner.alert("Monkeyrunner about to execute","Monkeyrunner","OK")

# Connects to the current emulator
emulator= MonkeyRunner.waitForConnection()

# Install the Android app package and test package
emulator.installPackage('./app/build/outputs/apk/app-debug-unaligned.apk')
emulator.installPackage('./app/build/outputs/apk/app-debug-androidTest-unaligned.apk')

# sets a variable with the package's internal name
package = 'in.ravidsrk.sample'

# sets a variable with the name of an Activity in the package
activity = 'in.ravidsrk.sample.MainActivity'

# sets the name of the component to start
runComponent = package + '/' + activity

# Runs the component
emulator.startActivity(runComponent)

# wait for the screen to fully come up
MonkeyRunner.sleep(2.0)

# Takes a screenshot
snapshot = emulator.takeSnapshot()

# Writes the screenshot to a file
snapshot.writeToFile('mainactivity.png','png')

# Alert the user a testing is about to be run by MonkeyRunner
MonkeyRunner.alert("Instrumented test about to execute","Monkeyrunner","OK")

#kick off the instrumented test
emulator.shell('am instrument -w in.ravidsrk.sample.test/android.support.test.runner.AndroidJUnitRunner')

# return to the emulator home screen
emulator.press('KEYCODE_HOME','DOWN_AND_UP')

```

## References
* <https://riggaroo.co.za/introduction-automated-android-testing/>
* <http://robolectric.org>
* <https://github.com/robolectric/robolectric>
* <https://www.bignerdranch.com/blog/triumph-android-studio-1-2-sneaks-in-full-testing-support>
* <https://github.com/mutexkid/android-studio-robolectric-example>
* <http://blog.nikhaldimann.com/2013/10/10/robolectric-2-2-some-pages-from-the-missing-manual>
* <https://corner.squareup.com/2013/04/the-resurrection-of-testing-for-android.html>
* <http://simpleprogrammer.com/2010/07/27/the-best-way-to-unit-test-in-android/>
* <https://youtu.be/f7ihSQ44WO0?t=15m11s>
* <https://code.google.com/p/android-test-kit>
* <https://developer.android.com/training/testing/ui-testing/espresso-testing.html>
* <https://github.com/vgrec/EspressoExamples>
* <https://github.com/designatednerd/Wino>
* <http://chiuki.github.io/advanced-android-espresso/#/>
* <http://www.vogella.com/tutorials/AndroidTestingEspresso/article.html>
