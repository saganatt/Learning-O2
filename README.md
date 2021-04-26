# Learning O2

## Data files

[MC correspondence on AliMonitor](https://alimonitor.cern.ch/job_details.jsp)  
[O2 conversion trains](https://alimonitor.cern.ch/trains/train.jsp?train_id=132)

I see the following pairs:

|system| train number | run name | MC train number | MC run name |
|-----:|-------------:|---------:|----------------:|------------:|
|Pb-Pb |148           |LHC15o    |151              |LHC20f6      |
| pp   |149           |LHC18_pp_bdefghiklmnop|152  |LHC18g4      | 

To get AOD files:
1. Scroll down on the [O2 conversion trains](https://alimonitor.cern.ch/trains/train.jsp?train_id=132) page
2. Click on the train number --> Test Results
3. Scroll down to 'full train', click on output
4. Download `AO2D.root`

## Quick O2 build (once O2 was installed with aliBuild)

```
cd alice/sw/BUILD/O2-latest/O2
cmake . # Optional step, needed only after you add a new file and/or modify any CMakeLists.txt
ninja stage/bin/o2-analysistutorial-simple-analysis
```

NOTE: Sometimes, if you e.g. add some `std::cout` lines, they might not be correctly linked with such quick build. In this case you need to run full aliBuild :-(

However, if you don't care about having just a partial build of O2, you can copy and run `git apply quick_alibuild.patch` in the `alice/O2/` directory. This will comment out many build targets not essential for an analysis task to speed up the build, and only 1/5 of the O2 gets compiled.

## Prepare O2 Git repository

Fork O2 repository on GitHub. Then in your `alice/O2/` directory:
```
git remote remove origin
git remote add origin <your_repository_link>
git remote add upstream https://github.com/AliceO2Group/AliceO2.git # the main O2 repo
git remote -vvv # verify the remotes are set correctly
```

Now, you will create and push branches to your own repo (`origin`). Remember to stay up-to-date with the official repo:
```
git fetch upstream # Downloads new commits from the official repo
git checkout dev
git rebase upstream/dev # Updates your own dev with the official dev commits
git checkout <your_working_branch>
git rebase dev
```

## Get O2 simple analysis task

Inside `alice/O2` do:
```
git remote add <remote_name> https://github.com/saganatt/AliceO2.git # <remote_name> is any alias you wish e.g. saganatt
git fetch <remote_name>
git checkout learning-o2
git push -u origin # This creates a learning-o2 branch on your own repository
# Your local branch will be then independent from my repo 
```

## Local analysis

```
alienv enter O2/latest
o2-analysistutorial-simple-analysis --aod-file <aod_file_name> -b
```

NOTE: In case you've updated the binaries with `ninja`, you need to copy the new binary to your current dir and call `./o2-analysis*`:
```
cp alice/sw/BUILD/O2-latest/O2/stage/bin/o2-analysistutorial-simple-analysis .
./o2-analysistutorial-simple-analysis --aod-file <aod_file_name> -b
```

The output results are in `AnalysisResults.root`.

## Writing an analysis task

[Documentation](https://pbuehler.github.io/documentation/docs/) under development.

The very simple analysis task is in `Analysis/Tutorials/src/simpleAnalysis.cxx`. You can look at other analyses in Tutorials.

## PID Response - [documentation](https://pbuehler.github.io/documentation/docs/helperTasks/pid.html)

TPC and TOF information is separated. The tables are defined in `Analysis/DataModel/include/PID/PIDResponse.h`.
