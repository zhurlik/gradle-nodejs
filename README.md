# gradle-nodejs
Here you can see a very simple way how to use nodejs and gradle together without any plugins.
# Install NodeJs
* Add a repository
```groovy
repositories {
    // For example: https://nodejs.org/dist/v10.16.3/node-v10.16.3-linux-x64.tar.xz
    ivy {
        url 'https://nodejs.org/dist/'
        patternLayout {
            //This maps to the pattern: [organisation]:[module]:[revision]:[classifier]@[ext]
            artifact '/v[revision]/[organisation]-v[revision]-[module].[ext]'
        }
    }
}
```
* Add a new configuration and a dependency
```groovy
configurations {
    nodejs
}

dependencies {
    // to be able to download nodejs as a dependency
    nodejs group: 'node', name: project.'nodejs.platform', version: project.'nodejs.version', ext: 'tar.xz'
}
```
**Note:** the version of NodeJs you can specify in the [gradle.properties](./gradle.properties)
* A task for installing NodeJs
 ```groovy
def nodeJsHome = file("$projectDir/node-v${project.'nodejs.version'}-${project.'nodejs.platform'}")

task installNodeJs() {
    group = 'NodeJs'
    description = 'Install NodeJs'

    onlyIf {
        !nodeJsHome.exists()
    }

    doLast {
        println "NodeJs: ${project.'nodejs.version'} will be installed..."
        def dist = project.configurations.nodejs.files[0]
        exec {
            commandLine 'tar', '-xf', dist.path, '-C', projectDir
        }
    }
}
```
# How to use
The following examples will show how you will be able to use that:
## node <options>
* **node --help** `./gradlew nodejs-projectA:node <<<--help`
## npm <options>
* **npm -v** `./gradlew nodejs-projectB:npm <<<-v`
* **npm install --save-dev webpack** `./gradlew nodejs-projectA:npm <<<'install --save-dev webpack'`
## npx <options>
* **npx webpack --colors** `./gradlew nodejs-projectB:npx <<<'webpack --colors'`