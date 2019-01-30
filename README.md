# dspot-hands-on

The slides and some scripts are avalaible on this repository.

## Try DSpot on dhell project

1. Clone and go in the root directory of the project
```sh
git clone https://github.com/STAMP-project/dhell.git
cd dhell
```

2. Amplify the test class `eu.stamp_project.examples.dhell.HelloAppTest`
```sh
mvn eu.stamp-project:dspot-maven:amplify-unit-tests \
    -DtargetOneTestClass \
    -Damplifiers=MethodAdd,MethodRemove \
    -Diteration=1 \
    -Dtest=eu.stamp_project.examples.dhell.HelloAppTest
```

* `-Diteration=1`: DSpot will make only one iteration of the three steps: Input Modification, Assertion Generation and Test Selection Criterion.
* `-Damplifiers=MethodAdd,MethodRemove`: DSpot will use two input amplifiers (for the input modification).
  1. MethodAdd: duplicate an existing method call.
  2. MethodRemove: Remove an existing method call.

3. You should obtain something like:

```txt
======= REPORT =======
PitMutantScoreSelector:
The original test suite kills 12 mutants
The amplification results with 1 new tests
it kills 2 more mutants
```

4. Checkout the output in `target/dspot/output/`

## Try DSpot on your own project

1. Run the assertion amplification with by replace `Your.TestClass` by the name of an actual test class:

```sh
mvn eu.stamp-project:dspot-maven:amplify-unit-tests \
    -DtargetOneTestClass \
    -Dtest=Your.TestClass
```

2. Run the amplification with input modification:

```sh
mvn eu.stamp-project:dspot-maven:amplify-unit-tests \
    -DtargetOneTestClass \
    -Diteration=1 \
    -Damplifiers=MethodAdd,MethodRemove \
    -Dtest=Your.TestClass
```


_Recall_:
* `-Diteration=1`: DSpot will make only one iteration of the three steps: Input Modification, Assertion Generation and Test Selection Criterion.
* `-Damplifiers=MethodAdd,MethodRemove`: DSpot will use two input amplifiers (for the input modification).
  1. MethodAdd: duplicate an existing method call.
  2. MethodRemove: Remove an existing method call.
  
## Tune amplification's options:
  
Before making multiple runs of DSpot, we advice you to pre-compute the mutation score of the test class that you want to amplify.
In fact, the first thing that DSpot does, is to compute the mutation score of the original test suite, in order to have a base line.
  
1. To compute the original mutation score of your test class, type and `Your.TestClass` by the name of an actual test class:
```sh
mvn clean test -DskipTests \
  eu.stamp-project:pitmp-maven-plugin:descartes \
  -DoutputFormats=XML \
  -DtargetTests=Your.TestClass
```

2. Find and copy the mutations report in `target/pit-reports/AAAAMMDDHHMM/mutations.xml:
```sh
cp target/pit-reports/*/mutations.xml ./mutations.xml
```

3. Use the option `-Dpath-pit-result=mutations.xml` of DSpot to provide the pre-computed mutation score:
```sh
mvn eu.stamp-project:dspot-maven:amplify-unit-tests \
  -Dpath-pit-result=mutations.xml \
  -Dtest=Your.TestClass
```

You should obtain the same output than you had before.

* Avalaible values for `-Damplifiers`:
        		 - MethodAdd
        		 - MethodRemove
        		 - TestDataMutator
        		 - MethodGeneratorAmplifier
        		 - ReturnValueAmplifier
        		 - StringLiteralAmplifier
        		 - NumberLiteralAmplifier
        		 - BooleanLiteralAmplifier
        		 - CharLiteralAmplifier
        		 - AllLiteralAmplifiers
        		 - NullifierAmplifier
        		 - None
        (default: None)
    
* Increase the number of iterations: `-Diteration=1`, `-Diteration=2`...

4. Examples:

```sh
mvn eu.stamp-project:dspot-maven:amplify-unit-tests \
    -DtargetOneTestClass \
    -Diteration=3 \
    -Damplifiers=TestDataMutator \
    -Dtest=Your.TestClass
```

```sh
mvn eu.stamp-project:dspot-maven:amplify-unit-tests \
    -DtargetOneTestClass \
    -Diteration=2 \
    -Damplifiers=TestDataMutator,MethodGeneratorAmplifier,NullifierAmplifier \
    -Dtest=Your.TestClass
```

