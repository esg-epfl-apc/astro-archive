many servers do not work?
what interface?
is it part of galaxy core?
how to search?
how will it start jobs?
heasarc?

* can use esap as external archive gui
* can use VO tap apis
* galaxy object store?


see https://github.com/esg-epfl-apc/galaxy-pan as an example

## needs

* search files and see them in galaxy ui
* run pulsar jobs with 

### VO case

can try hess data first
then, also try LS

### rucio plugin in galaxy

auth for rucio? using single user

useful for storing data from hpc

### unorganized comments from Bjorn


As discussed today, here are a few "data_source" links. That might be relevant.

As recap, data can be imported in various ways into Galaxy.

Download via URL / Paste Content / Upload from computer
* You can always create a tool that does in the background somekind of wget/curl to get the data if you have a pure API endpoint this is probably your way to go.
    * many sites block access
    * we might have something like "upload tool" which is a differnt kind of tool. but UI is strickt and does not allow view view of
* If data is exposed with a standard protocoll, SFTP, S3, WebDAV ... if your data store can be queried via something like pyfilesystem, Galaxy has a nice integration for that
    * offers too hierarichal view
* Via a so called "data source"-tool a special Galaxy tool
* rucio-plugin-like way by tricking the galaxy to think a file is there
* [shared data](https://github.com/usegalaxy-eu/shared-data) has "data libraries" but they are either local to this instance or with static index
* we might need specific page with archives 

pulsar needs to talk to a particular

A data source tool can only be used if you have a website that is exposing the data somehow via a GUI.
You need code at website where you want to get data from. So a collaboration is needed.

What happens. You search in Galaxy for your data-source-tool and click on it.
You will be redirected to the data-store website and Galaxy will pass a few parameters to the external website.
The user is now on this external website, selects and filters datasets. The website will show
A button "Send data to Galaxy" this button can be only shown when coming from a Galaxy server, since Galaxy passes along some parameters.

If the user clicks this button, the user is redirected to Galaxy and Galaxy runs a job to get the data from this external website.

A few information here:
https://galaxyproject.org/admin/internals/data-sources/

The best resource to try it out is problably this repo:
https://github.com/hexylena/galaxy-data_source-examples

This repo includes a toy server (the external server that exposes data) and a
very simple Galaxy data-source-tool.

I hope that can get you started.
Galaxy Community Hub - Galaxy Community Hub
All about Galaxy and its community.
GitHub - hexylena/galaxy-data_source-examples: Examples of Galaxy data_source servers - GitHub
Examples of Galaxy data_source servers. Contribute to hexylena/galaxy-data_source-examples development by creating an account on GitHub.
bgruening
patrick-austin: for your question. To update all material tools on EU, you need to update this file: https://github.com/usegalaxy-eu/usegalaxy-eu-tools/blob/master/materials.yaml.lock
usegalaxy-eu-tools/materials.yaml.lock at master · usegalaxy-eu/usegalaxy-eu-tools - GitHub
List of tools included in https://usegalaxy.eu. Contribute to usegalaxy-eu/usegalaxy-eu-tools development by creating an account on GitHub.
there is a Makefile that helps you
Volodymyr

    bgruening

    As discussed today, here are a few "data_source" links. That might be relevant.

    As recap, data can be imported in various ways into Galaxy.

        Download via URL / Paste Content / Upload from computer
        You can always create a tool that does in the background somekind of wget/curl to get the data
        if you have a pure API endpoint this is probably your way to go.
        If data is exposed with a standard protocoll, SFTP, S3, WebDAV ...
        if your data store can be queried via something like pyfilesystem, Galaxy has a nice integration for that
        Via a so called "data source"-tool a special Galaxy tool

    A data source tool can only be used if you have a website that is exposing the data somehow via a GUI.
    You need code at website where you want to get data from. So a collaboration is needed.

    What happens. You search in Galaxy for your data-source-tool and click on it.
    You will be redirected to the data-store website and Galaxy will pass a few parameters to the external website.
    The user is now on this external website, selects and filters datasets. The website will show
    A button "Send data to Galaxy" this button can be only shown when coming from a Galaxy server, since Galaxy passes along some parameters.

    If the user clicks this button, the user is redirected to Galaxy and Galaxy runs a job to get the data from this external website.

    A few information here:
    https://galaxyproject.org/admin/internals/data-sources/

    The best resource to try it out is problably this repo:
    https://github.com/hexylena/galaxy-data_source-examples

    This repo includes a toy server (the external server that exposes data) and a
    very simple Galaxy data-source-tool.

    I hope that can get you started.

Thanks! Right, so we might want the data sources since we have many "archives" which are the basis for all of the analysis. But to start, we can add synthetic data. And/or query in the tool.
Another related question, all what is needed for the tool to run should be either inside the tool definition or in requirements, right? E.g. the code is in a gitlab repository (or maybe just stored on zenodo), how do I make it part of the tool?
patrick-austin
There's required files (https://docs.galaxyproject.org/en/latest/dev/schema.html#tool-required-files). We're using it for ~100 line Python scripts for stuff like plotting and more complex logic for generating input files. Not sure how suitable it is for larger amounts of code though.
Galaxy Tool XML File — Galaxy Project 23.1.dev0 documentation
Development Documentation Galaxy Tool XML File View page source Warning This document is for an in-development version of Galaxy. You can alternatively view this page in the latest release if it exists
bgruening

    Another related question, all what is needed for the tool to run should be either inside the tool definition or in requirements, right? E.g. the code is in a gitlab repository (or maybe just stored on zenodo), how do I make it part of the tool?

The best practice recommendation is to package your code at first. E.g. with the conda package manager. Then you simply define the dependency as a requirement of the tool.

    patrick-austin
    There's required files (https://docs.galaxyproject.org/en/latest/dev/schema.html#tool-required-files). We're using it for ~100 line Python scripts for stuff like plotting and more complex logic for generating input files. Not sure how suitable it is for larger amounts of code though.

Those "required files" is an annotation feature. The idea here is, iff you ship a script next to your tool.xml then you annotate it, so Galaxy can better copy around those files. However, if you can package all the code in a package and "depend" on it.
Hope that makes sense.
Volodymyr

    bgruening

        Another related question, all what is needed for the tool to run should be either inside the tool definition or in requirements, right? E.g. the code is in a gitlab repository (or maybe just stored on zenodo), how do I make it part of the tool?

    The best practice recommendation is to package your code at first. E.g. with the conda package manager. Then you simply define the dependency as a requirement of the tool.

I see. It will end up with moving lot's of code to conda, and potentially frequently. But we can do it, need to try.
bgruening
how frequently?
or how many python files are that?
bgruening
But I guess, you need to release at some point. You also need to update the Galaxy part with every release of your software
1 reply
Volodymyr
yes, so we release on github/gitlab, some repositories to zenodo
bgruening
(reproducibility and such)
We have pipelines and tools to assist in creating conda, containers, nearly completely automatic
Volodymyr

    bgruening
    how frequently?

daily, hourly. What matters is latency of release.
bgruening
let me know if you need information about that
You release hourly your software?
wow
Volodymyr

    bgruening
    You release hourly your software?

It's not actually unusual if you consider what I mean by software here. Someone develops a tool, or perhaps fixes a bug, and wants to make it available immediately
once the tests are passed (<30min) - it's made avaialble. It's not about constant rate, it's about latency
I guess we can publish to  conda in the same CI/CD, so it's not a problem. Need to try how it works practically
bgruening
I'm taking about the rate I think, since there is an entire chain of deployments etc involved. Ending with workflows, that then also needs to be upgraded and users that need to be made aware. 
But cool if that is possible.
2 replies
bgruening
yes
bgruening
image.png
But as I said this is our best practise recommendations, there are other ways possible :)
(all those things are automatise, the eye icon indicates a review, but bots are creating all the PRs etc)
Volodymyr
Cool! so part of this happens for every tool tool which is pushed, right? And the rest, which reacts on the dependency change, can I have a look?
bgruening
Yes.
here one bot that tracks github and creates conda updates: https://github.com/bioconda/bioconda-recipes/pull/39312
Update haptools to 0.1.3 by BiocondaBot · Pull Request #39312 · bioconda/bioconda-recipes - GitHub
Update haptools: 0.1.2 → 0.1.3 Info Link or Description Recipe recipes/haptools (click to view/edit other files) Summary Ancestry and haplotype aware simulation of genotypes and phenotyp...
When the new package is available this bot here create a Galaxy tool bump: https://github.com/galaxyproject/tools-iuc/pull/5073
Updating tools/getorganelle from version 1.7.6.1 to 1.7.7.0 by planemo-autoupdate · Pull Request #5073 · galaxyproject/tools-iuc - GitHub
Hello! This is an automated update of the following tool: tools/getorganelle. I created this PR because I think the tool's main dependency is out of date, i.e. there is a newer version available th...
(this bot is not super smart, it does not detect parameter changes, it just bumps versions atm)
Volodymyr
Thanks!
bgruening
Your welcome!
Tue, Feb 21 2023

Sebastian Schaaf set a profile picture
Thu, Feb 23 2023
bgruening
Btw. in the WP5 summary page, linked in this channel but also in GDrive, is the Matrix link: https://docs.google.com/document/d/1pIJnBBk_atygw8YPnQiGzmxs0P9EY0ne6dH_0hILq4Q/edit#
WP5 README - Google Docs
README WP5 This document is intended to be very brief and contains a list of essential links with further information for members of ESG WP5. A long version with all information about the EuroScienceGateway is available here. Its also editable. Management Information Hub Google Drive folder News...
So you can sent this summary document around to anyone that wants to contribute to this workpacke

Sveinung Gundersen joined the room
Mon, Feb 27 2023
bgruening
Planemo was finally published :-) "The Planemo toolkit for developing, deploying, and executing scientific data analyses in Galaxy and beyond" (https://genome.cshlp.org/content/early/2023/02/23/gr.276963.122.long).
It includes a detailed description about the Galaxy SDK and the relation to Bioconda, Biocontainers and automatic training generation.
Screenshot 2023-02-27 at 09-59-56 The Planemo toolkit for developing deploying and executing scientific data analyses in Galaxy and beyond - Genome Res.-2023-Bray-gr.276963.122.pdf.png
Everyone that deals with Galaxy tools, please read it. It contains a nice summary of what is possible and what we have automatised over the years.
Nikolay Vazov
Great, Thanks!


https://galaxyproject.org/news/2021-07-04-nextcloud-support/
https://github.com/usegalaxy-eu/infrastructure-playbook/blob/master/templates/galaxy/config/file_sources_conf.yml.j2#L7

## Deferred datasets

can be used to refer to big data

https://galaxyproject.org/news/2022-08-galaxy-release-22-05/
