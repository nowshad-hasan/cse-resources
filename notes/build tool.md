# Introduction

There are two most popular build tools out there - 
- Gradle 
- Maven

## Gradle

### Resources

Gradle Tutorial - Crash Course [Marco Behler](https://youtu.be/gKPMKRnnbXU)
Gradle Course for Beginners | Get Going with Gradle [Tom Gregory](https://youtu.be/So0j4RnoKkU)
Gradle best practice tips [Tom Gregory](https://youtube.com/playlist?list=PL0UJI1nZ56yAHv9H9kZA6vat4N1kSRGis)


### General Info

`gradlew` stands for gradle wrapper.

There is one `settings.gradle` file per gradle project. If the project is a multi-module project, then we can use `include {module-name}`  in that file.

### Folder structure

`build` folder contains below things - 

- classes - compiled classes
- generated
- libs - executable jar file after build
- 

### Commands

- `java -jar {jar-file}` - to run a jar file
- `gradle build` - run the tests and build a jar
- `gradle clear` - remove the build folder
- `gradle test` - run the tests and show the report in `build/reports/tests/test/index.html`. Open it with `open -a 'google chrome' index.html` command. To run tests in a specific module (or, aka project) we can type `gradle test -p {module-name}`, p for project.
- `gradle {module-name}:dependencies` - see the dependency tree if any module present, otherwise `gradle dependencies` for a single project. `gradlew htmlDependencyReport` generates report at `build/reports/project/dependencies/index.html`, apply this plugin if that does not work - `apply plugin: 'project-report'`.