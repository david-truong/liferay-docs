# Building Themes in a Maven Project [](id=building-themes-in-a-maven-project)

Liferay's Theme Builder is a tool used to build @product@ theme files in your
project. You can incorporate the Theme Builder into your Maven project to
generate WAR-style themes deployable to @product@. To learn more about theming
in @product@, see the
[Themes and Layout Templates](/develop/tutorials/-/knowledge_base/7-0/themes-and-layout-templates)
tutorial section. 

The easiest way to create a Liferay theme with Maven is to create a new Maven
project using Liferay's provided Theme archetype. You can learn how to generate
a Maven Theme project by visiting the
[Generating New Projects Using Archetypes](/develop/tutorials/-/knowledge_base/7-0/generating-new-projects-using-archetypes)
tutorial. In some cases, however, this may not be convenient. For instance, if
you have a legacy theme project and don't want to start over, generating a new
project is not ideal. 

For cases like this, you should manually configure your Maven project to
build a theme. You'll learn how to do this next.

1.  Configure Liferay's Theme Builder plugin in your project's `pom.xml` file:

        <build>
            <plugins>
                <plugin>
                    <groupId>com.liferay</groupId>
                    <artifactId>com.liferay.portal.tools.theme.builder</artifactId>
                    <version>2.0.1</version>
                    <executions>
                        <execution>
                            <phase>generate-resources</phase>
                            <goals>
                                <goal>build</goal>
                            </goals>
                            <configuration>
                                <diffsDir>${maven.war.src}</diffsDir>
                                <outputDir>${project.build.directory}/${project.build.finalName}</outputDir>
                            </configuration>
                        </execution>
                    </executions>
                </plugin>
            </plugins>
        </build>

    The above configuration applies the Theme Builder by specifying its
    `groupId`, `artifactId`, and `version`. It then defines the Theme Builder's
    execution and configuration.

    - The
      [`executions` tag](https://maven.apache.org/guides/mini/guide-configuring-plugins.html#Using_the_executions_Tag)
      configures the Theme Builder to run during the `generate-resources` phase
      of your Maven project's build lifecycle. The `build-theme`
      [goal](http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#A_Build_Phase_is_Made_Up_of_Plugin_Goals)
      is defined for that lifecycle phase.
    - The
      [`configuration` tag](https://maven.apache.org/pom.html#Plugins) defines
      several important properties:
        - `diffsDir`: The folder holding the files to copy over the parent
          theme.
        - `name`: The new theme's name.
        - `outputDir`: The folder to build the theme.
        - `parentDir`: The parent theme's folder.
        - `parentName`: The parent theme's name.
        - `templateExtension`: The extension of the template files (e.g., `ftl`
          or `vm`).
        - `unstyledDir`: The unstyled theme's folder.

2.  Apply the CSS Builder plugin, which is required to use the Theme Builder:

        <plugin>
            <groupId>com.liferay</groupId>
            <artifactId>com.liferay.css.builder</artifactId>
            <version>1.0.24</version>
            <executions>
                <execution>
                    <id>default-build-css</id>
                    <phase>compile</phase>
                    <goals>
                        <goal>build</goal>
                    </goals>
                </execution>
            </executions>
            <configuration>
                <docrootDirName>target/${project.build.finalName}</docrootDirName>
                <outputDirName>/</outputDirName>
            </configuration>
        </plugin>

    You can learn more about the CSS Builder's Maven configuration by visiting
    the
    [Compiling Sass Files in a Maven Project](/develop/tutorials/-/knowledge_base/7-0/compiling-sass-files-in-a-maven-project)
    tutorial.

3.  You can configure your project to exclude Sass files from being packaged in
    your theme. This is optional, but is a nice convenience to keep any
    unnecessary source code out of your WAR. Since the Theme Builder creates
    a WAR-style theme, you should apply the
    [maven-war-plugin](https://maven.apache.org/plugins/maven-war-plugin/) so it
    instructs the WAR file packaging process to exclude Sass files:

        <plugin>
            <artifactId>maven-war-plugin</artifactId>
            <version>3.0.0</version>
            <configuration>
                <packagingExcludes>**/*.scss</packagingExcludes>
            </configuration>
        </plugin>

4.  Insert the `<packaging>` tag in your project's POM so your project is
    correctly packaged as a WAR file. This tag can be placed with your project's
    `groupId`, `artifactId`, and `version` specifications like this:

        <groupId>com.liferay</groupId>
        <artifactId>com.liferay.project.templates.theme</artifactId>
        <version>1.0.0</version>
        <packaging>war</packaging>

You've successfully configured your Maven project to build a theme! You can
generate your theme by calling the following Maven command:

    mvn verify

The Theme Builder generates your WAR-style theme in your Maven project's
configured output folder (e.g., `/target`).

Excellent! You configured your project to use Theme Builder and can build
Liferay themes using Maven!
