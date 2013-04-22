# Apache Ant

Apache Ant is a tool for build automation, it uses XML to describe the build process. This repository will give examples of an Apache Ant implementation for UI development.

## Requirements

The Java Development Kit (JDK) is required to run Ant tasks. The minimum is JDK 1.4 for YUI Compressor or 1.6 for Closure Compiler, however it is recommended the latest version of JDK is installed from Oracle.

## Installation

Windows users will require [WinAnt](http://code.google.com/p/winant/), this will also come with Ant-Contrib which adds a range of useful tasks. Detailed instructions on installing Ant for Windows can be found from the [HTML5 Boilerplate team](https://github.com/h5bp/ant-build-script/wiki/Detailed-Ant-Installation-Instructions) or [Nicholas Zakas](http://www.nczonline.net/blog/2012/04/12/how-to-install-apache-ant-on-windows/).

Both Linux and Mac OSX users should have Ant preinstalled but will also need Ant-Contrib. This can be installed simply, by using a package manager. 

Linux Red Hat or CentOS users could use yum:

	$ sudo yum info ant-contrib

Mac OSX users could use [MacPorts](http://www.macports.org/install.php):    
    
    $ sudo port install ant-contrib 

## Folder structure

The folder structure used in a project should be similar to the one used in this repository. 

*   A build.xml file containing the build script.
*   Config folder for build properties.
*   Tools folder to store scripts or binaries used by Ant tasks.

This structure should be placed in within your project, however it should be seperate to your project source code as it will need to run tasks on the source code, it is usual to contain the build script within a build folder.

Specifc naming conventions and locations can be changed within the build script or properties files.

## Script structure    

### Initialise

    <!--
        Apache Ant UI build project.
    -->
    <project name="UI build" default="build" basedir="../">

### Configuration    
    
    <!--
        Project configuration.
    -->
    <property file="config/default.properties"/>
        <taskdef resource="net/sf/antcontrib/antcontrib.properties">
        <classpath>
            <pathelement location="./${tools}/${antContrib}"/>
        </classpath>
    </taskdef>
    
### Default target    
    
    <!--
        Development environment build.
        Default:
            ant build
    -->
    <target name="build" description="Build target for the environment">
        <echo message="Building environment..." />
        <antcall target="build.task" />
    </target>
    
### Build tasks

    <!--
        Development environment build task.
    -->
    <target name="build.task" description="Calls all environment targets" 
        depends="remove,
        copy,				
        jshint,
        csslint,
        deploy.server" />

### Remove task        

    <!--
        Clean the publish folder of the previous build.
    -->
    <target name="remove" description="Removes the previous build (Deletes the publish directory)">
        <echo message="Removing the previous publish directory..."/>
        <delete dir="./${publish}/" />
    </target>
    
### Copy task    
    
    <!--
        Copy build. 
    -->	
    <target name="copy" description="Copy all source files to the publish location for manipulation and deployment">
        <echo message="Copying production files to the publish folder..." />
        <!-- Files to be excluded from copy -->
        <var name="excluded" value="${exclude}" />
        <!-- Copy over files except excluded files -->
        <copy todir="./${publish}">
            <fileset dir="${source.web}/" excludes="${exclude}" />
        </copy>
        <echo message="A copy of all web files are now in: ./${publish}." />
    </target>
    
### JSHint task

    <!--
        JSHint.
    -->
    <target name="jshint">
        <apply dir="${source.js}" executable="java" parallel="false" failonerror="true">
            <fileset dir="./">
                <include name="**/${source.js}/*.js" />
                <exclude name="**/*.min.js" />
                <exclude name="**/${publish}/" />
                <exclude name="**/${source.js}/external" />
            </fileset>
            <arg value="-jar" />
            <arg path="./${tools}/${rhino}" />
            <arg path="./${tools}/${jshint}" />
            <srcfile />
            <arg value="${jshint.options}" />
        </apply>
        <echo>JSHint successful</echo>
    </target>

### JavaScript concatenation and minification task
    
    <!--
        Minification and concatenation of JavaScript
    -->
    <target name="js.optimise" description="Minify and Concatenate the JS files">
        <echo message="Concatenating JS files..."/>
        <concat destfile="./${publish}/${source.js}/build.all.js">
            <fileset file="./${publish}/${source.js}/external/jquery.js" />
            <fileset dir="./${publish}/${source.js}/">
                <include name="*.js" />
                <exclude name="config.js" />
            </fileset>
        </concat>
        <echo message="Minifying JS file..."/>		
        <apply executable="java" parallel="false">
            <fileset dir="./${publish}/${source.js}">
                <include name="**/build.all.js" />	
            </fileset>
            <arg line="-jar"/>
            <arg path="./${tools}/${yuicompressor}"/>
            <srcfile />
            <arg line="-o" />
            <mapper type="glob" from="*.js" to="../${publish}/${source.js}/*.min.js" />
            <targetfile />
        </apply>
        <delete>
            <fileset file="./${publish}/${source.js}/external/jquery.js" />
            <fileset dir="./${publish}/${source.js}/">
                <include name="*.js" />
                <exclude name="*.min.js" />
                <exclude name="*.config.js" />
            </fileset>
        </delete>
    </target>

### CSSLint task

    <!--
        CSSLint.
    -->
    <target name="csslint">
        <apply dir="${source.css}" executable="java" parallel="false" failonerror="true">
            <fileset dir="./">
                <include name="**/${source.css}/*.css" />
                <exclude name="**/*.min.css" />
                <exclude name="**/${publish}/" />
            </fileset>
            <arg value="-jar" />
            <arg path="./${tools}/${rhino}" />
            <arg path="./${tools}/${csslint}" />
            <srcfile />
            <arg value="${csslint.options}" />
        </apply>
        <echo>CSSLint completed</echo>
    </target>

### CSS concatenation and minification task

    <!--
        Minification and concatenation of CSS
    --> 
    <target name="css.optimise" description="Minify example.css">
        <echo message="Concatenating CSS files..."/>
        <concat destfile="./${publish}/${source.css}/build.all.css">
            <fileset file="./${publish}/${source.css}/external/example.css" />
            <fileset dir="./${publish}/${source.css}/">
                <include name="*.css" />
                <exclude name="demo.css" />
            </fileset>
        </concat>  
        <echo message="Minifying CSS files..."/>	
        <apply executable="java" parallel="false">
            <fileset dir="./${publish}/${source.css}">
                <include name="**/build.all.css" />	
            </fileset>
            <arg line="-jar"/>
            <arg path="./${tools}/${yuicompressor}"/>
            <srcfile/>
            <arg line="-o"/>
            <mapper type="glob" from="*.css" to="../${publish}/${source.css}/*.min.css"/>
            <targetfile />
        </apply>
        <delete>
            <fileset dir="./${publish}/${source.css}/">
                <include name="*.css" />
                <exclude name="*.min.css" />
            </fileset>
        </delete>
    </target>

### Server remove task

    <!--
        Remove server files.
        SSH in to server to perform a command line arguement
    -->
    <target name="remove.server" description="Remove files from the development server">
        <sshexec host="${hostname}"
            username="${username}"
            password="${password}"
            command="cd /var/www/; rm -rf web;"
            trust="true" />
    </target>
    
### Server deploy task

    <!--
    Deploy server files.
    -->	
    <target name="deploy.server" description="Deploy files to the development server">
        <echo>Copying to ${server.dev.hostname}...</echo>
        <echo>About to send the files...</echo>
        <scp todir="${username}@${hostname}:${directory}/" password="${password}" trust="yes">
            <fileset dir="${publish}"></fileset>
        </scp>
        <echo>Files sent</echo>
    </target>