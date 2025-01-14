

# Fred Hutch Customizations

Cromwell can help run WDL workflows on a variety of computing resources such as SLURM clusters (like the Fred Hutch cluster), as well as AWS, Google and Azure cloud computing systems.  Using WDL workflows allows users to focus on their workflow contents rather than the intricacies of particular computing platform.  
However, there are optimizations of how those workflows run that may be specific to each computing tool ("task").  

We'll discuss some of the available customizations to help you run WDLs on our cluster that still allow those workflows to be portable to other computing platforms.  


## Standard Runtime Variables

These runtime variables can be used on any computing platform: the values given here are the defaults for our Fred Hutch configuration. 

- `cpu: 1`
  - An integer number of cpus you want for the task
- `memory: 2000`
  - An integer number of MB of memory you want to use for the task
- `docker: "ubuntu:latest"`
  - A specific Docker container to use for the task.  For the custom Hutch configuration, docker containers can be specified and the necessary conversions (to Singularity) will be performed by Cromwell (not the user).  Note: when docker is used, soft links cannot be used in our filesystem, so workflows using very large datasets may run slightly slower due to the need for Cromwell to copy files rather than link to them.  


## Fred Hutch Custom Runtime Variables
For the `gizmo` cluster, the following custom runtime variables are available (below we show each variable with its current default value). You can change these variables in the `runtime` block for individual tasks in a WDL file: task-level specifications will override the defaults.  


- `walltime: "18:00:00"`
  - A string ("HH:MM:SS") that specifies how much time you want to request for the task. Can also specify >1 day, e.g. "1-12:00:00" is 1 day+12 hours.
- `partition:  "campus-new"`
  - Which cluster partition to use. The default is `campus-new`: other options currently include `restart` or `short` but check [SciWiki](https://sciwiki.fredhutch.org/scicomputing/) for updated information. 
- `modules: ""`
  - A space-separated list of the environment modules you'd like to load (in that order) prior to running the task.  See below for more.
- `dockerSL: "ubuntu:latest"`
  - This is a custom configuration for the Hutch that allows users to use docker and softlinks only to specific locations in Scratch.  It is helpful when working with very large files. 
- `account: "paguirigan_a"`
  - This allows users who run jobs for multiple PI accounts to specify which account to use for each task, to manage cluster allocations.  

## Software environments


### Modules
At Fred Hutch we have huge array of pre-curated software modules installed on our SLURM cluster which you can [read about in SciWiki](https://sciwiki.fredhutch.org/scicomputing/compute_scientificSoftware/).  
The custom configuration of our Cromwell server allows users to specify one or more modules to use for individual tasks in a workflow.  The desired module(s) can be requested for a task in the `runtime` block of your calls like this:

```
runtime {
  modules: "GATK/4.2.6.1-GCCcore-11.2.0 SAMtools/1.16.1-GCC-11.2.0"
}
```

In this example, we specify two modules, separated by a space (with quotes surrounding both). The GATK module will be loaded first, followed by the SAMtools module.   In this example you'll note the "toolchain" used to build each modules is the same ("GCC-11.2.0").  When you load >1 module for a single task it is important to ensure that they are compatible with each other: choose versions built with the same toolchain if you can.  

### Docker
However, if you want to move your WDL workflow to the cloud in the future, you'll want to leverage Cromwell's ability to run your tasks in Docker containers.  Users can specify docker containers in runtime blocks. Cromwell will maintain a local cache of previously used containers, facilitating the pull of Docker containers and conversion for use.  This behavior allows us to evade rate-limiting by DockerHub and improves speed of your workflows. 



## Guidance and Support

There are a variety of resources on campus from the [SciWiki](https://sciwiki.fredhutch.org) to SciComp office hours (found in the Research Data Support Teams team) to asking computing related questions in the [Fred Hutch Data Slack #question-and-answer channel](https://fhdata.slack.com/archives/CD3HGJHJT) to coming to the [Effective Computing Drop in hours held by Fred Hutch DaSL](https://fhdata.slack.com/archives/C03T4QPNDA9).

### Effective Computing Drop In Hour
This is a drop-in style biweekly meeting that provides resources for folks to talk about their computational work and improve over time.  Find current events by checking out the Fred Hutch Data Slack workspace [here](https://fhdata.slack.com).

### Slack Workflow Manager Channel
In the [Fred Hutch Data Slack workspace](https://fhdata.slack.com/) the [#workflow-managers](https://fhdata.slack.com/archives/CJFP1NYSZ) channel is a good place to find help from Hutch peers and staff who support Cromwell and WDL

