# Updating Measure Unit with new CLDR data

This document explains how to update the C and JAVA version of the MeasureUnit
class with new CLDR data.

Code is generated by running MeasureUnitTest.java unit tests, which writes
generated code to System.out. Two ways to access this:

1. Within **eclipse**:
   - Open MeasureUnitTest.java, run it by clicking on the green play button on
     menu bar.
   - Copy the generated code from the eclipse console to the clipboard.

2. With **ant**:
   - Run: `ant checkTest
     -Dtestclass='com.ibm.icu.dev.test.format.MeasureUnitTest'`
   - Open the checkTest output: `out/junit-results/checkTest/html/index.html`
   - Navigate to "System.out" at the bottom of the MeasureUnitTest page to find
     the generated code, and copy to the clipboard.

After syncing CLDR data with ICU do the following. This documentation assumes
that you are updating the MeasureUnit clases for ICU 68.

* Check out
  $GIT_ROOT/icu4j/main/tests/core/src/com/ibm/icu/dev/test/format/MeasureUnitTest.java
* Open MeasureUnitTest.java.
* Find the `testZZZ` test, its code should all be commented out. This test will
  execute last and will run the desired code.

    Make sure DRAFT_VERSIONS at top of MeasureUnitTest.java is set correctly.
    These are the ICU versions that have draft methods.

## Update MeasureUnit.java

* Change `testZZZ` to run `generateConstants(“68”); // ICU 68.`
* Run MeasureUnitTest.java, copy the generated code (see instructions above).
* Open MeasureUnit.java:
  $GIT_ROOT/icu4j/main/classes/core/src/com/ibm/icu/util/MeasureUnit.java
* Look for line containing:

  `// Start generated MeasureUnit constants`
* Look for line containing:

  `// End generated MeasureUnit constants`
* Replace all the generated code in between with the contents of the clipboard
* Run the MeasureUnitTest.java to ensure that the new code is backward
  compatible. These compatibility tests are called something like
  `TestCompatible65`, which tests backward compatibility with ICU 65.
* Create a compatibility test for ICU 68. Change `testZZZ` to run
  `generateBackwardCompatibilityTest(“68”)`
* Run tests.
* Copy generated test (see instructions above) into MeasureUnitTest.java
* Run tests again to ensure that new code is backward compatible with itself

## Update ICU4C

* checkout ICU4C

### Update measunit.h

* Change testZZZ to run `generateCXXHConstants(“68”); // ICU 68`.
* Run MeasureUnitTest.java, copy the generated code (see instructions above).
* Open $GIT_ROOT/icu4c/source/i18n/unicode/measunit.h. Look for line containing:

  `// Start generated createXXX methods`
* Look for line:

  `// End generated createXXX methods`
* Replace all the generated code in between with the contents of the clipboard

### Update measunit.cpp

* Change testZZZ to run generateCXXConstants();
* Run MeasureUnitTest.java, copy the generated code (see instructions above).
* Open $GIT_ROOT/icu4c/source/i18n/measunit.cpp. Look for line containing:

  `// Start generated code for measunit.cpp`
* Look for lines

  `// End generated code for measunit.cpp`
* Replace all the generated code in between with the contents of the clipboard

\# Update measfmt.cpp -- Update the value for `MEAS_UNIT_COUNT` - no longer
necessary

### Run C++ tests

* Run `./intltest format/MeasureFormatTest` from `test/intltest` to ensure new
  code is backward compatible.
* Create a compatibility test for ICU 68. Change `testZZZ` in eclipse to run
  `generateCXXBackwardCompatibilityTest(“68”)`
* Run tests.
* Copy generated test (see instructions above) into
  $GIT_ROOT/icu4c/source/test/intltest/measfmttest.cpp. Make other necessary
  changes to make test compile. You can find these changes by searching for
  `TestCompatible65()`
* Run tests again to ensure that new code is backward compatible with itself

## Finishing changes

These last changes are necessary to permanently record the ICU version number of
any new measure units. Without these changes any new functions for this release
will be considered new for the next release too.

* Change `testZZZ` to run `updateJAVAVersions(“68”);`
* Run MeasureUnitTest.java, copy the generated code (see instructions above).
* Append the clipboard contents to the values of the JAVA_VERSIONS variable
  near the top of MeasureUnitTest.java.

  **Important:** what you are copying are just the new functions for the current
  ICU version, in this case 68. Therefore append, do not replace.