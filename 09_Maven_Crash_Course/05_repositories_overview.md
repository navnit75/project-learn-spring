## There are two repos
- Local Repo
    - Located  on developer's computer 
    - Windows: c:\Users\<username>\.m2\repositories
    - Mac or Linux: ~/.m2/repository
    - Maven will search the local repo first 
        - Before going out to Maven Central Repository
        - Your local cache

- Central Repo
    - By default maven will search Maven Central Repository (remote)
    - https://repo.maven.apache.org/maven2/
    - Requires an internet connection 
    - Once the files are downloaded they are stored in local repository. 

## Private Repositories - USE CASE
- Your company has created super top secret code modules 
- Would like to share with other developement teams at your company 
- But lets keep it private
- NOT AVAILABLE TO PUBLIC

## Solution
- We can set up our own Maven repository
- Secure it with credentials : id/password
- Create your super - top - secret projects and publish on private repository 
- Your developement team can access your private repository 

## Few Manager repository Manager products
|Product|Company|Website|
|-------|-------|-------|
|Archiva|Apache|archiva.apache.org|
|Artifactory|JFrog|www.jfrog.com|
|Nexus|Sonatype|www.sonatype.com|

## Cloud hosted solutions
- If you don't need to self-host internally in your company 
- Cloud hosted solutions are available
- Check for 
    - www.packagecloud.io, www.mymavenrepo.com
    - Google Search : "maven cloud hosts"