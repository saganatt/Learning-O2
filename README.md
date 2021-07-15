# O2 build and additional steps

Build O2 with aliBuild as explained [here](https://alice-doc.github.io/alice-analysis-tutorial/building/).<br>
**NOTE:** Most of us use Ubuntu 18.04 / 20.04, some also CentOS 7, so better pick one of these systems.<br>
**NOTE:** The instructions can quickly get outdated - aliDoctor might show you more packages than suggested by the instructions. However, you should never see Python - in that case install additional libraries with `sudo apt-get install python3-dev` (Ubuntu) or `sudo yum install python3-devel` (CentOS).

You will need also the GRID access to data, follow [the instructions](https://alice-doc.github.io/alice-analysis-tutorial/start/cert.html) to get the certificate.<br>
**NOTE:** You can test the certificate inside O2 instead of AliPhysics if you don't have AliPhysics.

## Prepare O2 Git repository

Fork the [O2 repository](https://github.com/AliceO2Group/AliceO2) on GitHub. Then inside your `alice/O2/` directory:
```bash
git remote remove origin
git remote add origin <your_repository_link>
git remote add upstream https://github.com/AliceO2Group/AliceO2.git # the main O2 repo
git remote -vvv # verify the remotes are set correctly
```
What it does: we set the `origin` reference (i.e. the main, default remote repository) to your fork, so `push`/`pull` will by default refer to your own repository. Then, we add an `upstream` reference to the official O2 repository, so you can then `pull` any new changes and regularly update your repository.

**NOTE:** After the June O2 changes you might have already the `upstream` reference set correctly - verify with `git remote -vvv` and add the origin reference to your fork.

Now, you can create and push branches to your own repo (`origin`) with your own O2 codes.

### Staying up-to-date

When you will be developing new codes, remember to stay up-to-date with the official repo:
```bash
git fetch upstream # Downloads new commits from the official repo
git checkout dev
git rebase upstream/dev # Updates your own dev with the official dev commits
git checkout <your_working_branch>
git rebase dev
```

# Running the first simple analysis

## Obtaining the data files

[MC correspondence on AliMonitor](https://alimonitor.cern.ch/job_details.jsp)  
[O2 conversion trains](https://alimonitor.cern.ch/trains/train.jsp?train_id=132)

I see the following pairs:

|system| train number | run name | MC train number | MC run name |
|-----:|-------------:|---------:|----------------:|------------:|
|Pb-Pb |180           |LHC15o    |184              |LHC20f6      |
| pp   |183           |LHC18_pp_bdefghiklmnop|186  |LHC18g4      | 

To get AOD files:
1. Scroll down on the [O2 conversion trains](https://alimonitor.cern.ch/trains/train.jsp?train_id=132) page
2. Click on the train number --> Test Results
3. Scroll down to 'full train', click on output
4. Download `AO2D.root`

## Get the O2 simple analysis task

Inside `alice/O2` do:
```bash
git remote add <remote_name> https://github.com/saganatt/AliceO2.git # <remote_name> is any alias you wish e.g. saganatt
git fetch <remote_name>
git checkout learning-o2 # This automatically creates a local branch with changes from <remote_name>/learning-o2
git push -u origin # This creates a learning-o2 branch in your remote (GitHub) repository
```
What it does: You add a reference to my GitHub repository and you download (`fetch`) my changes. Then you create your own branch, locally and in your remote repository, with the simple analysis example.

After these steps, your `learning-o2` is independent from my repository, i.e. any your following changes won't affect the code in my repo, and vice versa.

## Local analysis

Create a directory for analysis / test. Place your downloaded AOD data files there. Then, call from this directory:

```bash
alienv enter O2/latest
o2-analysistutorial-simple-analysis --aod-file <aod_file_name> -b
```

# Quick O2 build (once O2 was fully installed with aliBuild)

After you change some files, you might want to test them without building the whole O2. You can use ninja to build only the updated codes and their dependants.

```bash
cd alice/sw/BUILD/O2-latest/O2
cmake . # Optional step, needed only after you add a new file and/or modify any CMakeLists.txt
ninja install stage/bin/your-analysis-file
```
You can also call `ninja` without install but then you need to copy built files manually.<br>
Calling `ninja` or `ninja install` without arguments will build any changed files and their dependants.

**NOTE:** Sometimes, if you, e.g., add some `std::cout` lines, they might not be correctly linked with such quick build. In this case you need to run full aliBuild :-(

**NOTE:** In case you've updated the binaries with `ninja` (but not `ninja install`), you need to copy the new binary to your current dir and call `./o2-analysis*`:
```bash
cp alice/sw/BUILD/O2-latest/O2/stage/bin/o2-analysistutorial-simple-analysis .
./o2-analysistutorial-simple-analysis --aod-file <aod_file_name> -b
```

The output results are in `AnalysisResults.root`.

# Writing an analysis task

[Documentation](https://pbuehler.github.io/documentation/docs/) under development.

The very simple analysis task is in `Analysis/Tutorials/src/simpleAnalysis.cxx`. You can look at other analyses in Tutorials.

# PID Response - [documentation](https://pbuehler.github.io/documentation/docs/helperTasks/pid.html)

TPC and TOF information is separated. The tables are defined in `Analysis/DataModel/include/PID/PIDResponse.h`.
