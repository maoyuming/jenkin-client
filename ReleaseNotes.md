# Release Notes

## Release 0.3.6 (NOT RELEASED YET)

### General Changes

  [Upgraded Maven Plugins][issue-167]

  * maven-release-plugin to 2.5.3
  * maven-site-plugin to 3.5.1
  * maven-shade-plugin to 2.4.3
  * maven-jar-plugin to 3.0.2
  * maven-source-plugin 3.0.1
  * maven-surefire/failsafe-plugin to 2.19.1
  * maven-resources-plugin to 3.0.1
  * Add missing maven-javadoc-plugin 2.10.4

  [Upgraded Maven Plugins][jissue-35108]

   * maven-clean-plugin to 3.0.0
   * maven-resources-plugin to 3.0.0
   * maven-jar-plugin to 3.0.0

  [Fixed issue 172][issue-172]

  The implementation `BuildWithDetails.getCauses()` could cause an 
  NPE which now has been imroved to prevent it. Thanks for hint
  which brought me to reconsider the implementation.

  [Fixed issue 162][issue-162]

  Serveral issues fixed related to using logging framework 
  [issue-161][issue-161], [issue-113][issue-113] and
  [JENKINS-35002][jissue-35002]

  Added slf4j-api as foundation for logging. Using 
  log4j2-slf4j-impl in jenkins-client-it-docker for logging. 
  As a user you can now decide which logging framework
  you would like to use.

  [Changed the structure and integrated Docker IT][issue-160]
  
### API Changes

  [Fixed issue 174][issue-174]

  jenkins.getComputerSet().getComputer() produced an error.
  Changed getComputer() into getComputers() cause it returns
  a list an not only a single computer.
  Based on the above problem the `Executor` needed to be changed to
  represent the correct data which is being returned.

```java
public class ComputerSet {
   public List<ComputerWithDetails> getComputers();
}
``` 

```java
public class Executor {
  public Job getCurrentExecutable();
  public Job getCurrentWorkUnit();
}
``` 

  [Fixed issue 169 Add crumbFlag to renameJob][issue-169]

  Added supplemental `renameJob` method which supports crumbFlag. Furthermore
  added `renameJob` which supports folder with and without `crumbFlag`.
  So we now have the following methods to rename jobs:

```java
public class JenkinsServer {
  public void renameJob(String oldJobName, String newJobName) throws IOException;
  public void renameJob(String oldJobName, String newJobName, Boolean crumbFlag) throws IOException;
  public void renameJob(FolderJob folder, String oldJobName, String newJobName) throws IOException;
  public void renameJob(FolderJob folder, String oldJobName, String newJobName, Boolean crumbFlag) throws IOException;
}
``` 

  [Fixed issue 168 deletejobs in folder][issue-168]

  Added new method to delete a job within a folder.

```java
public class JenkinsServer {
 public void deleteJob(FolderJob folder, String jobName) throws IOException;
}
```

  [Changing getLocalContext(), setLocalContext()][pull-163]

  The protected method `getLocalContext()` now returns
  `HttpContext` instead of `BasicHttpContext`.
  So the API has changed from the following:

```java
public class JenkinsServer {
  protected BasicHttpContext getLocalContext();
  protected void setLocalContext(BasicHttpContext localContext); 
  .
}
``` 

  into this:

```java
public class JenkinsServer {
  protected HttpContext getLocalContext();
  protected void setLocalContext(HttpContext localContext); 
  .
}
``` 

  Apart from that the visibility of the class `PreemptiveAuth` has been changed 
  from package private to public.

  [Get Jenkins Version from http header][issue-90]

```java
public class JenkinsServer {
  public String getVersion();
  .
}
``` 

  [Added description for Job][issue-165]

```java
public class JobWithDetails {
  public String getDescription();
  .
}
``` 
  

## Release 0.3.5

### API Changes

  [Fixed issue 159][issue-159]

  The following methods have been changed to return `Collections.emtpyList()` if 
  no builds exists or have ran before.

```java
getAllBuilds(Range range);
getAllBuilds(), 
getBuilds()
```

  [Fixed NPE][issue-147]

  The JobWithDetails class contains several methods which could
  have returned `null`. This has been changed to return non null values.

```java
     public List<Job> getDownstreamProjects();
     public List<Job> getUpstreamProjects();
```

  They will return `Collections.emptyList()`.

  The following methods will return `Build.BUILD_HAS_NEVER_RAN` in
  cases where no build has executed for the appropriate methods instead
  of the previous `null` value.

```java
    public Build getFirstBuild();
    public Build getLastBuild();
    public Build getLastCompletedBuild();
    public Build getLastFailedBuild();
    public Build getLastStableBuild();
    public Build getLastSuccessfulBuild();
    public Build getLastUnstableBuild();
    public Build getLastUnsuccessfulBuild();
```


  [Added getAllBuilds(), getAllBuilds(Range range) ][issue-148]

  The `JobWithDetails` has been enhanced with two methods
  to get all builds which exists or a range can be used
  to select.
  
  Note: There seemed to be no option to extract simply the number
  of existing builds of a particular job via the REST API.

```java
public class JobWithDetails {
    public List<Build> getAllBuilds() throws IOException;
    public List<Build> getAllBuilds(Range range) throws IOException;
}
```

 [Added renameJob(..)][pull-158]
 
```java
public class JenkinsServer {
  renameJob(String jobName, String newJobName) throws IOException;
  .
}
``` 


## Release 0.3.4


### API Changes

  [Added toggleOffline Node][issue-157]
  Added toggleOffline to `ComputerWithDetails` class.

```java
public class ComputerWithDetails {
  public void toggleOffline(boolean crumbFlag) throws IOException;
  public void toggleOffline() throws IOException;
}
```

  * [deleteJob throws exception but works anyway][issue-154]
  * [Some HTTP calls to jenkins result in a 302, which currently throws an HttpResponseException][issue-7]
  * [Create Job is failing - any idea on this error][issue-121]
  
  * Fixed. by changing call to client.post(, crumbFlag = true) into
    client.post(, crumbFlag = false).

  [Added getPluginManager() to JenkinsServer][issue-120]

```java
PluginManager getPluginManager() throws IOException;
```

  Added method to get `Plugin`.

```java
public class PluginManager extends BaseModel {
    public List<Plugin> getPlugins()
}
```

  `Plugin` class contains methods to get informations about
  the plugins plus a `PluginDependency` class.

  [Added getFileFromWorkspace() to Job][issue-119]
  Added method `getFileFromWorkspace()` to `Job` class to get a file 
  from workspace in Jenkins.

```java
String getFileFromWorkspace(String fileName)
```


  [Make jobNames case sensitive.][pull-149]

  [TestCase class enhanced][issue-155]
  Now the `TestCase` contains the information
  about the errorDetails and the errorStackTrace of 
  a failed test case.

```java
String getErrorDetails();
String getErrorStackTrace()
```

  [Added disableJob, enabledJob][pull-123]
  The following methods have been added to support enabling
  and disabling jobs.

```java
void disableJob(String jobName);
void disableJob(String jobName, boolean crumbFlag);
void enableJob(String jobName);
void enableJob(String jobName, boolean crumbFlag);
```

  [Fixed #128 Added OfflineCause as a real object in ComputerWithDetails][issue-128]
  Added `OfflineCause` class which can now being used 
  as a return type for the attribute `offlineCause` instead
  of `Object` in `ComputerWithDetails`. 

```java
public class ComputerWithDetails {
  Object offlineCause;
..
}
```

  into the following which also influenced the appropriate return types 
  of the getters:

into 
```java
public class ComputerWithDetails {
    OfflineCause offlineCause;

    public OfflineCause getOfflineCause() throws IOException;

}
```

  [Fixed #135 Created documentation area][issue-135]
  Started a documentation area under src/site/ for either Markdown
  or asciidoctor.

  [Fixed #130 org.jvnet.hudson:xstream][issue-130]
  I needed to remove the toString method from View class
  which uses the XStream classes which does not make sense
  from my point of view.

  [Fixed #144 Problems with spaces in paths and job names][issue-144]

  [Fixed #133 How do we find out which system the built was build on?][issue-133]

  Now `BuildWithDetails` API has been enhanced with the following method get
  the information on which node this build has ran.

```java
String getBuiltOn();
```

   [Fixed #146 duration / estimatedDuration are long][issue-146]

   The API in `BuildWithDetails`has been changed to represent the change
   in datatype.

```java
int getDuration();
int getEstimatedDuration();
```
   into:
```java
long getDuration();
long getEstimatedDuration();
```

## Release 0.3.3

  [Fixed #111 A missing dependency to jaxen caused problems][issue-111]
  The problem was that the dependency has missed from the compile scope
  which caused problems for users to use. The dependency was there via
  transitive dependency of a test scoped artifact.

  [Do not propagate IOException when GET for /CrumbIssuer fails][issue-116]

### API Changes

  [Added Cloudbees Folder support][issue-108]
  The following methods have been added to support `FolderJob` type.

```java
void createFolder(String jobName, Boolean crumbFlag);
Optional<FolderJob> getFolderJob(Job job);
Map<String, Job> getJobs(FolderJob folder);
Map<String, Job> getJobs(FolderJob folder, String view);
Map<String, View> getViews(FolderJob folder);
View getView(FolderJob folder, String name);
JobWithDetails getJob(FolderJob folder, String jobName);
MavenJobWithDetails getMavenJob(FolderJob folder, String jobName);
void createJob(FolderJob folder, String jobName, String jobXml);
void createJob(FolderJob folder, String jobName, String jobXml, Boolean crumbFlag);
```

## Release 0.3.2

### API Changes:

  [JobWithDetails has been enhanced with information about the first build][issue-91].

```java
Build getFirstBuild()
```

  [The `JenkinsServer` API has been enhanced to get information about the Queue][issue-104]

```java
QueueItem getQueueItem(QueueReference ref) throws IOException 
```

```java
Build getBuild(QueueItem q)  throws IOException 
```

 The `JenkinsHttpClient` API has been changed from the following:

```java
JenkinsHttpClient(URI uri, DefaultHttpClient defaultHttpClient);
```
 into

```java
JenkinsHttpClient(URI uri, CloseableHttpClient client);
```

  Furthermore the `JenkinsHttpClient` API has been enhanced with the following
  method which allows to create an unauthenticated Jenkins HTTP client.

```java
JenkinsHttpClient(URI uri, HttpClientBuilder builder);
```

  The `Build` class `Stop` method needed to be changed internally based on an
  inconsistencies in Jenkins versions. (This might be change in future). There
  are versions 1.565 which supports the stop method as a post call, version
  1.609.1 support it as a get call and 1.609.2 as a post call.


  The `Job` class has been enhanced to trigger parameterized builds.

```java
QueueReference build(Map<String, String> params, boolean crumbFlag) throws IOException;
```


## Release 0.3.1

### API Changes:


 Until to Release 0.3.0 the `getLoadStatistics()` method returned a simple `Map` which
 needed to be changed to represent the information which is returned.

 So the old one looked like this:

```java
Map getLoadStatistics();
```

 The new one looks like this:

```java
LoadStatistics getLoadStatistics() throws IOException
```

 You can see how it works by using the [JenkinsLoadStatisticsExample.java][2].

 The API for `getExecutors()` has been changed from a simple [`Map` into something
 more meaningful][issue-67].

 The old one looked like this:

```java
List<Map> getExecutors(); 
```

  where as the new API looks like this:


```java
List<Executor> getExecutors();
```

  This will result in a list of [Executor][4] which contain supplemental informations
  about the appropriate executor.


## Issues

### [Issue #53][issue-53]

  This release contains new functionality like support to get the list of existing views.
  This can be accomplished by using the following:

```java
Map<String, View> views = jenkins.getViews();
```

### [Issue #67][issue-67]

  The change for `getExecutors()` which is documented in API changes.

### [Issue #82][issue-82]

  The information about the `ChangeSet` and the `culprit` has been improved.

```java
JenkinsServer jenkins = new JenkinsServer(new URI("http://localhost:8080/jenkins"), "admin", "password");
MavenJobWithDetails mavenJob = js.getMavenJob("javaee");
BuildWithDetails details = mavenJob.getLastSuccessfulBuild().details();
```

  So now you can extract the causes which is related to the trigger which triggered the build.

```java
List<BuildCause> causes = details.getCauses();
```

  With the help of `getChangeSet()` you can extract the changeset information of your build:

```java
BuildChangeSet changeSet = details.getChangeSet();
```

  By using the `changeSet.getKind()` you would expect to get the kind of version control
  which has been used. If you use Git or Subversion this is true but unfortunately it 
  is not true for all version control systems (for example TFS). 

  The information about files which have been changed an other information can be extracted
  by using the following:

```java
List<BuildChangeSetItem> items = changeSet.getItems();
```

  If you like to get a better overview [check the example file in the project][1].


### [Issue #89][issue-89]

  Extract TestReport from Jenkins

```java
JenkinsServer js = new JenkinsServer(URI.create("http://jenkins-server/"));
MavenJobWithDetails mavenJob = js.getMavenJob("MavenJob");
BuildWithDetails details = mavenJob.getLastSuccessfulBuild().details();
TestReport testReport = mavenJob.getLastSuccessfulBuild().getTestReport(); 
```

  You can extract the information about the tests which have run which contains
  information about the number of tests, failed tests and skipped tests etc.
  [For a more detailed example take a look into the project][3].





[1]: https://github.com/jenkinsci/java-client-api/blob/master/src/test/java/com/offbytwo/jenkins/integration/JenkinsChangeSetExample.java
[2]: https://github.com/jenkinsci/java-client-api/blob/master/src/test/java/com/offbytwo/jenkins/integration/JenkinsLoadStatisticsExample.java
[3]: https://github.com/jenkinsci/java-client-api/blob/master/src/test/java/com/offbytwo/jenkins/integration/BuildJobTestReports.java
[4]: https://github.com/jenkinsci/java-client-api/blob/master/src/main/java/com/offbytwo/jenkins/model/Executor.java
[issue-7]: https://github.com/jenkinsci/java-client-api/issues/7
[issue-53]: https://github.com/jenkinsci/java-client-api/issues/53
[issue-67]: https://github.com/jenkinsci/java-client-api/issues/67
[issue-82]: https://github.com/jenkinsci/java-client-api/issues/82
[issue-89]: https://github.com/jenkinsci/java-client-api/issues/89
[issue-90]: https://github.com/jenkinsci/java-client-api/issues/90
[issue-91]: https://github.com/jenkinsci/java-client-api/issues/91
[issue-104]: https://github.com/jenkinsci/java-client-api/issues/104
[issue-111]: https://github.com/jenkinsci/java-client-api/issues/111
[issue-116]: https://github.com/jenkinsci/java-client-api/issues/116
[issue-108]: https://github.com/jenkinsci/java-client-api/issues/108
[issue-113]: https://github.com/jenkinsci/java-client-api/issues/113
[issue-119]: https://github.com/jenkinsci/java-client-api/issues/119
[issue-120]: https://github.com/jenkinsci/java-client-api/issues/120
[issue-121]: https://github.com/jenkinsci/java-client-api/issues/121
[issue-128]: https://github.com/jenkinsci/java-client-api/issues/128
[issue-130]: https://github.com/jenkinsci/java-client-api/issues/130
[issue-133]: https://github.com/jenkinsci/java-client-api/issues/133
[issue-135]: https://github.com/jenkinsci/java-client-api/issues/135
[issue-144]: https://github.com/jenkinsci/java-client-api/issues/144
[issue-146]: https://github.com/jenkinsci/java-client-api/issues/146
[issue-147]: https://github.com/jenkinsci/java-client-api/issues/147
[issue-148]: https://github.com/jenkinsci/java-client-api/issues/148
[issue-154]: https://github.com/jenkinsci/java-client-api/issues/154
[issue-155]: https://github.com/jenkinsci/java-client-api/issues/155
[issue-157]: https://github.com/jenkinsci/java-client-api/issues/157
[issue-159]: https://github.com/jenkinsci/java-client-api/issues/159
[issue-160]: https://github.com/jenkinsci/java-client-api/issues/160
[issue-161]: https://github.com/jenkinsci/java-client-api/issues/161
[issue-162]: https://github.com/jenkinsci/java-client-api/issues/162
[issue-165]: https://github.com/jenkinsci/java-client-api/issues/165
[issue-167]: https://github.com/jenkinsci/java-client-api/issues/167
[issue-168]: https://github.com/jenkinsci/java-client-api/issues/168
[issue-169]: https://github.com/jenkinsci/java-client-api/issues/169
[issue-172]: https://github.com/jenkinsci/java-client-api/issues/172
[issue-174]: https://github.com/jenkinsci/java-client-api/issues/174
[pull-123]: https://github.com/jenkinsci/java-client-api/pull/123
[pull-149]: https://github.com/jenkinsci/java-client-api/pull/149
[pull-158]: https://github.com/jenkinsci/java-client-api/pull/158
[pull-163]: https://github.com/jenkinsci/java-client-api/pull/163
[jissue-35002]: https://issues.jenkins-ci.org/browse/JENKINS-35002
[jissue-35108]: https://issues.jenkins-ci.org/browse/JENKINS-35108
