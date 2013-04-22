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

This structure should be placed in within your project, however it should be seperate to your project source code as it will need to run tasks on the source code.

## Script structure    


  

