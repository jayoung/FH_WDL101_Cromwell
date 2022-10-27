

# Cromwell at the Fred Hutch

Cromwell can help run WDL workflows on a variety of computing resources such as SLURM clusters, as well as AWS, Google and Azure cloud computing systems. 
Using WDL workflows allows users to focus on their workflow contents rather than the intricacies of the particular computing platforms they are using.  
However, there are always optimizations of how those workflows run taht may be somewhat specific to the various computing tools you may be using.  
We'll discuss some of the available customizations to help you run WDLs on our cluster in a simple way that still allows those workflows to be portable to other computing platforms.  


## Standard Runtime Variables
These runtime variables are both the defaults for our Fred Hutch configuration and also standard runtime variables you can use on other computing platforms too. 

- `cpu: 1`
  - An integer number of cpus you want for the task
- `memory: 2000`
  - An integer number of MB of memory you want to use for the task
- `docker: "ubuntu:latest"`
  - A specific Docker container to use for the task.  For the custom Hutch configuration, docker containers can be specified and the necessary conversions (to Singularity) will be performed by Cromwell (not the user).  Note: when docker is used, soft links cannot be used in our filesystem, so workflows using very large datasets may run slightly slower due to the need for Cromwell to copy files rather than link to them.  


## Fred Hutch Custom Runtime Variables
For the `gizmo` cluster, the following runtime variables are available that are customized to our configuration.  
What is specified below is the current default as written, you can edit these in the config file if you'd like OR you can specify these variables in your `runtime` block in each task to change only the variables you want to change from the default for that particular task.  


- `walltime: "18:00:00"`
  - A string of date/time that specifies how many hours/days you want to request for the task
- `partition:  "campus-new"`
  - Which partition you want to use, the default is `campus-new` but whatever is in the runtime block of your WDL will overrride this. Other options currently include `restart` or `short` but check [SciWiki](https://sciwiki.fredhutch.org/scicomputing/) for more updated information. 
- `modules: ""`
  - A space-separated list of the environment modules you'd like to have loaded (in that order) prior to running the task.  
- `dockerSL: "ubuntu:latest"`
  - This is a custom configuration for the Hutch that allows users to use docker and softlinks only to specific locations in Scratch.  It is helpful when working with very large files. 
- `account: "paguirigan_a"`
  - This allows users who run jobs for multiple PI accounts to specify at the level of a task which account to use for a given job to manage cluster allocations.  

### Software environments


#### Modules
At Fred Hutch we have huge array of pre-curated software modules installed on our SLURM cluster which you can [read about in SciWiki](https://sciwiki.fredhutch.org/scicomputing/compute_scientificSoftware/).  
The custom configuration of our Cromwell server software here at the Hutch allows users to specify modules (or combinations of modules by simply adding them on in a space separated string) available on the cluster to use for individual tasks in a workflow.  The module(s) desired can be used for a task by specifying in the `runtime` block of your calls the following:

```
runtime {
  modules: "GATK/4.2.6.1-GCCcore-11.2.0 SAMtools/1.16.1-GCC-11.2.0"
}
```

In this example, the GATK module will be loaded first, followed by the SAMtools modiule.   In this example you'll note the "toolchain" used to build both modules are the same ("GCC-11.2.0").  It is important to ensure when you load modules together for a single task that they are compatible with each other.  

#### Docker
However, if you want to move your WDL workflow to the cloud in the future, you'll want to leverage Cromwell's pre-configured ability to run your tasks on `gizmo` in Docker containers.  This configuration allows users to specify docker containers in their runtime blocks, allows Cromwell to maintain a local cache of previously used containers, and facilitates the pull of Docker containers and conversion for use.  This behavior allows us to evade rate-limiting by DockerHub and improves speed of your workflows. 


#### Find Support
If you're unfamiliar with resources for using Docker or to learn more about using modules and software environments see the "Guidance and Support" section of this course. Software management in workflows can be don in many ways so finding what works best for your work is often an iterative process.  




## Guidance and Support

There are a variety of resources on campus from the [SciWiki](https://sciwiki.fredutch.org) to SciComp office hours (found in the Research Data Support Teams team) to asking questions in the [FH Data Slack #question-and-answer channel](https://fhdata.slack.com/archives/CD3HGJHJT) to coming to the [Effective Computing Drop in hours held by Fred Hutch DaSL](https://fhdata.slack.com/archives/C03T4QPNDA9).

### Effective Computing User Group
This is a drop-in style biweekly meeting that provides resources for folks to talk about their computational work and improve over time.  Find current events by checking out the FH Data Slack workspace [here](https://fhdata.slack.com).

### Slack Workflow Managers
In the [FH Data Slack workspace](https://fhdata.slack.com/) there is a specific channel where you can find help from peers and staff who support Cromwell and WDL here at the Hutch called [#workflow-managers](https://fhdata.slack.com/archives/CJFP1NYSZ)
