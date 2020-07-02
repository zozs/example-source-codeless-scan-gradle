# Source-codeless Scan for Gradle, with Debricked Github Action

This example shows how you can use the Debricked Github Action to scan the dependencies of your Gradle repository, without uploading the source code to Debricked.
This is called a _Source-codeless Scan_, and is available for Gradle and Maven.
Note that most other languages, such as JavaScript and Python, are already sourcecode-less by default, and you don't need to take any further action.

To do this for Gradle, two steps need to be performed.

1. You need to generate a list of dependencies as a part of your own pipeline.
2. These files must be uploaded to Debricked.

This repository shows how this can be done for a simple Gradle project.

## Steps

Here is a description of the two steps above in more details.

If you want to look at the final version of a working file, look in the [.github/workflow/vulnerabilites.yml](.github/workflow/vulnerabilites.yml) workflow file in this repository.
The different parts of it are described below.

### Generate a list of dependencies

This can be done by running Gradle with the `dependencies` task, and storing the output in a file.

Conceptually, what needs to be done is to run: `gradle dependencies > .debricked-gradle-dependencies.txt` as a part of the pipeline.
The output filename is important, the Debricked integration will look for these files in the next step.
The output file _must_ be placed in the same directory as the `build.gradle` file it belongs to, otherwise Debricked cannot connect them together.

In [.github/workflow/vulnerabilites.yml](.github/workflow/vulnerabilites.yml), this is the first part of the workflow, i.e., the following step:

```
    - run: ./gradlew dependencies > .debricked-gradle-dependencies.txt
```

If you haven't already, you also need to choose which Java version to use beforehand, like below:

```
    - uses: actions/setup-java@v1
      with:
        java-version: '13'
    - run: ./gradlew dependencies > .debricked-gradle-dependencies.txt
```

If you don't use the Gradle wrapper `gradlew` in your repository, you need to install the correct Gradle version yourself beforehand.

### Upload dependency files to Debricked

This is done with the usual Github Action, described in https://github.com/debricked/actions.
As long as your files are named correctly (`.debricked-gradle-dependencies.txt` for Gradle), the action will automatically detect that you have generated the dependency file yourself.
This means you should *not* enable the `UPLOAD_ALL_FILES` option.

Example of this step:

```
    - uses: debricked/actions/scan@v1
      env:
        USERNAME: ${{ secrets.DEBRICKED_USERNAME }}
        PASSWORD: ${{ secrets.DEBRICKED_PASSWORD }}
```

## License

The Gradle example is based on the code from this tutorial https://github.com/spring-guides/gs-gradle, which is licensed under the Apache License, Version 2.0.

Modifications performed by Debricked, as well as other files are also covered under the Apache License, Version 2.0.

See the file LICENSE in this repository.
