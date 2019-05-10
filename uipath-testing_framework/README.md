# UiPath_TestingFramework #

Framework used for running of UnitTests in UiPath.

Quick guide:

* Install custom activity package (_Custom.Activities.VariableComparer.x.x.x.nupkg_) from **Gallery** tab of [Package manager](https://studio.uipath.com/docs/managing-activities-packages#section-managing-packages) (Package can also be installed manullay, as it comes with the **Framework** and can be found in the **Custom_Activity_Package** folder).
* Using **Unit Test** template (_Tests_Repository\UnitTestTemplate\MethodName_StateUnderTest_ExpectedBehavior.xaml_) make Unit Tests.
	* **Unit tests** must implement **Assert Unit Test** custom activity to be eligible for running in **Framework**.
* Place **Unit Tests** into the **Tests_Repository** folder.
	* When **RunAllTests.xaml** is started it will run all tests that are found in the **Tests_Repository** folder.
* To integrate **TestingFramework** into another project copy whole folder to the target project root directory. 
	* (**Optional**) When integrating **TestingFramework** into another project, delete **project.json** file from **TestingFramework** folder (if present).
(This is more of a quality of life improvement. For people that open .xaml from the directory itself. If you have integrated framework into another project, 
and you open **RunAllTests.xaml** directly from directory (while project.json is present in framework root) it will count **TestingFramework** as a standalone 
project and you will not be able to see parent project files inside of UiPath "Project" sidebar). 

## Custom.Activities.VariableComparer.x.x.x.nupkg package ##

_Custom.Activities.VariableComparer.x.x.x.nupkg_ package is a core part of the Framework (for source code please check [Custom Activity Repository](https://bitbucket.org/krsma33/variablecomparer/src/master/)):

* It contains custom **Assert Unit Test** activity that must be used when making **Unit Tests**.
* **Assert Unit Test** activity is used as a container for writing **Assert methods** ([imported](https://studio.uipath.com/docs/importing-new-namespaces) from _CustomActivities.VariableComparer_ namespace) which are used to evaluate unit test outputs.
	* Assert.AreEqual(expected,actual)
	* Assert.AreNotEqual(expected,actual)
	* Assert.AreSame(expected,actual)
	* Assert.AreNotSame(expected,actual)
	* Assert.Contains(expected,actual)
	* Assert.NotContains(expected,actual)
	* Assert.IsNull(actual)
	* Assert.IsNotNull(actual)
* **Assert methods** throw custom exceptions that are used by the **Framework** to log test results and give important information in case of a test failiure.
	* AssertException is main exception thrown by Assert methods.
	* AssertNullException is exception thrown when Assert.IsNull or Assert.IsNotNull method returns false.
	* CustomAssertException is generic exception that is thrown when random boolean expression that returns false is passed to Assert Unit Test activity

## Framework

Whole idea behind the **Framework** was to make **UiPath Unit Testing platform** that works as a _plug and play_ component that doesn't need to be configured before use.

Resulting product is a Framework that:

* Can be integrated into any UiPath project or framework (can be placed inside any folder or sub-folder and it will still work out of the box).
* Can run any _.xaml_ file found in **Test_Repository** folder and also make a distinction if _.xaml_ file is a **Unit Test** or not (user is notified via log).
* Logs test runs and gives extensive information about failed tests (both inside **UiPath** console and in textual log files).
	
## Unit tests ##

**Framework** has no use if there are no tests to be run.
As such, biggest effort required from the developers themselves is in writing of **Unit Tests**. 
Luckily, by using unit test template, custom activity and Assert methods, this is much less of a chore.

Basic idea of Unit Testing is to arrange a set of data that will be used to test the code.

After running of code that is being tested, output is compared with the expected value.  

Comparison should give information if test have passed or not.

Unit testing comprises of three stages: 

* **Arrange**
* **Act**
* **Assert**. 

It is recommended to make more than one test for one peace of code.  
This is to make sure that code correctly handles both good and bad data, and also to see if correct exceptions are produced by the code.   

**Tests_Repository\UnitTestExamples** folder contains few examples of how unit tests should look like.   
**Tests_Repository\UnitTestTemplate** folder contains master template for unit tests (_MethodName_StateUnderTest_ExpectedBehavior.xaml_).   
   
### Arrange Stage ###
   
Purpose of the arrange stage is to define input parameters for the test:

* Manually define parameters and data
* (If needed) Define **expected** result
* (If needed) Read configuration file

### Act Stage ###

Purpose of act stage is to run the code that is being tested (by invoking the code and supplying it previously arranged data). 

Output of this stage should be actual value that code produces assigned to the **actual** variable.

### Assert Stage ###

Purpose of the assert stage is to evaluate test outputs (usually this is done by comparing **actual** value gotten by running the code with the **expected value** that was defined in the arrange stage, but is not the only type of test that can be done).

For **Framework** to consider a **Unit Test** as valid, it must implement **Assert Unit Test activity**.
This activity is meant to be used along with, one of the many, **Assert methods** from _CustomActivities.VariableComparer_ namespace.

**Assert methods** make writing of Unit Tests much easier and exceptions thrown by these methods serve a big role in Framework logging.
