# Appium Web tests for AWS Device Farm
This test demonstrates how to test a web application using AWS Device Farm and Appium Python. You can use these tests as a reference for your own AWS Device Farm Appium Python tests

# Background
What Is Appium Python?

Appium is an open-source tool for automating native, mobile web, and hybrid applications on platforms such as Android and iOS. For more information, see [About Appium](http://appium.io/slate/en/master/?ruby#about-appium).

# Getting started
1. Follow the [official Appium getting started guide](http://appium.io/slate/en/tutorial/android.html?java#getting-started-with-appium) and install the Appium server and dependencies.

  <b>AWS Device Farm supports Appium version 1.4.10. Using a different version locally may cause unexpected results when running Appium tests on AWS Device Farm.</b>

2. In order to use 1.4.10, download Appium through NPM with this command:
  ```shell
  npm install -g appium@1.4.10
  ```
3. Verify that you have Appium installed with this command: appium -v You should get "1.4.10" as the output

## Steps to package your tests
The Appium Python test packages you upload to Device Farm must be in .zip format and contain all the dependencies of your test. The following instructions show you how to meet these requirements.

<b>Note:</b> The instructions below are based for Linux x86_64 and Mac. In the current scheme of things Device Farm requires that the packaging of your Appium Python Tests be done on <b><u>Linux x86_64 if your tests contain non-universal wheels dependencies</b></u>. The reason for this is that Python wheel gathers the dependencies, your .whl files under the wheelhouse/ folder, for the platform on which you execute the command. Executing the python wheel command on any platform other than Linux x86_64 would gather the flavor of a non-univesral wheel dependency for that particular platform and may cause undesired effects which most likely will lead to errors while executing your tests on Device Farm

1. It is highly recommended that you set up [Python virtualenv](https://pypi.python.org/pypi/virtualenv) for developing and packaging tests so that the unnecessary dependencies are not included in your test package.

  - Do not create a Python virtualenv with ‘--system-site-packages’ option because it will inherit packages from /usr/lib/pythonx.x/site-packages or wherever your global site-packages directory is. This can lead to you including dependencies in your virtual environment that are not needed by your tests.
  - You should also verify that your tests do not use dependencies that are dependent on native libraries as these native libraries may or may not be present on the instance where these tests run.
  - Install py.test in your virtual environment

  An example flow of creating a virtual environment using Python virtualenv would look like:
  ```shell
  $ virtualenv workspace
  $ cd workspace
  $ source bin/activate
  $ pip install pytest
  ```

2. Store all python test scripts under the tests/ folder in your work space.
  ```shell
  - workspace
    |
    - tests/ (your tests go here)
  ```

  Make sure you have py.test installed in your virtual environment and test cases are discoverable by the following command which you should run from your virtual environment ‘workspace' folder. Make sure the output of py.test command below shows you the tests that you want to execute on Device Farm
  ```shell
  $ py.test --collect-only tests/
  ```

3. Go to your work space and run the following command to generate the requirements.txt file.
  ```shell
  $ pip freeze > requirements.txt
  ```

4. Go to your work space and run the following command to generate the wheelhouse/ folder:
  ```shell
  $ pip wheel --wheel-dir wheelhouse -r requirements.txt
  ```

5. You can clean all cached files under your tests/ folder with the following commands:
  ```shell
  $ find . -name '__pycache__' -type d -exec rm -r {} +
  $ find . -name '*.pyc' -exec rm -f {} +
  $ find . -name '*.pyo' -exec rm -f {} +
  $ find . -name '*~' -exec rm -f {} +
  ```

6. Zip the tests/ folder, wheelhouse/ folder, and the requirements.txt file into a single archive:
  ```shell
  $ zip -r test_bundle.zip tests/ wheelhouse/ requirements.txt
  ```

7. Your workspace would eventually look like:
  ```shell
    - workspace
      |
       - tests/
      |
       - test_bundle.zip
      |
       - requirements.txt
      |
       - wheelhouse/
  ```

## Upload and Run your Web Application Appium Python Tests
Use the Device Farm console to upload your tests:

1. Sign in to the Device Farm console at https://console.aws.amazon.com/devicefarm

2. If you see the AWS Device Farm console home page, choose <b>Get started.</b>

3. If you already have a project, you can upload your tests to an existing project or choose <b>Create a new project.</b>

4. If the <b>Create a new run button</b> is displayed, then choose it.

5. On the <b>Choose your application</b> page, choose <b>Web Application (the HTML5 button)</b>

6. Provide a name for your run in the Run name field.

7. Configure your test by choosing <b>Appium Python</b>.

8. Next, choose Upload to upload your .zip file. Device Farm processes your .zip file before continuing.

9. Choose <b>Next step</b>, and then complete the remaining on-screen instructions to select devices and start the run
