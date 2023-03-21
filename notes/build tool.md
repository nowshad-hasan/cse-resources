# Introduction

There are two most popular build tools out there - 
- Gradle 
- Maven

## Gradle

### What is gradle?

Gradle is  a build automation tool. It takes our code and packages it into deployable unit. It is written on Groovy language.

Why Use gradle?

- Gradle makes building and running application very easy
- No need to install gradle in our machine. We can use `gradlew` in our gradlew wrapper and execute commands.
- very fast as it uses cache for re-building, better than maven as it uses XML 

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

### Commands

- `java -jar {jar-file}` - to run a jar file
- `gradle build` - run the tests and build a jar
- `gradle clear` - remove the build folder
- `gradle test` - run the tests and show the report in `build/reports/tests/test/index.html`. Open it with `open -a 'google chrome' index.html` command. To run tests in a specific module (or, aka project) we can type `gradle test -p {module-name}`, p for project.
- `gradle {module-name}:dependencies` - see the dependency tree if any module present, otherwise `gradle dependencies` for a single project. `gradlew htmlDependencyReport` generates report at `build/reports/project/dependencies/index.html`, apply this plugin if that does not work - `apply plugin: 'project-report'`.


## Gradle key concepts

### build.gradle

build.gradle is the Gradle build script file

- written in a Groovy DSL (Groovy Domain Specific Language)
- equivalent of pom.xml of maven
- resides in the top of the project


**Dependencies**

All the libraries will be there.
To add another module in there, write `implementation project(':my-backend')`
Scopes - 
    - compile -> implementation
    - testCompile -> testImplementation
    - api - let's say in our main project, A module uses a dependency called X, now module B tries to access it. For this, it needs `api X` to be defined in moduel A's build.gradle. But then we will get another error. We need to use `id 'java-library'` plugin instead of `id 'java'` in A's `build.gradle`. After that, B can use A's dependencies aka libraries.

**Repositories**

We let the gradle know from where to find and download repositories, like maven central, or google or jcenter or local or our private repositories.


**Plugins**

Actually a normal `build.gradle` can not do very much. It needs plugins to do different types of tasks, like to understand `/src` folder in our project we need to use `id 'java'`. It's a java plugin which does all kinds of java related staffs.

### Tasks

- defines a unit of work
- invoked from command line by `./gradlew build`
- see available tasks by running `./gradlew tasks`
- we can create our own tasks
- task have dependencies on other tasks, Build task depends on  `Assemble task` and `check task`. Check task run tests.

#### wrapper

### Start a new project
 
`gradle init` 
    - select type of project
    - select language
    - single or multi module projects

**Group** - namespace, used in artifact. But project name is defined in `settings.gradle` file.

